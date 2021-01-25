## Biomodern-Serializer
`Biomodern.Serializer` is a C++20 generalized serializer which can serialize the [`continuous_range`][continuous_range] which `value_type` is [`trivially_copyable`][trivially_copyable], such as scalar type, simple `struct`, `array` etc. The binary archive file size is actually the memory occupation in run time plus one `size_t` which indicates the size of the continuous container.

Further more, `Biomodern.Serializer` can also serialize the `vector<bool>` and still make the binary archive file size as small as the origin memory occupation. This is huge different from [`Boost.Serialization`][Boost.Serialization] which store the `vector<bool>` as a `vector<uint8_t>` even in `binary_archive` mode. 

The `Biomodern.Serializer` can also serialize my other project [`Biomodern.DitbitVector`][Biomodern.DitbitVector] which is a space-efficient specialization of the `vector` of two-bits numbers.

## Compilers
- GCC 10.2

## Usage
This project is header-only and has no dependency with other libraries, if you want use it, just copy and paste.
```cpp
#include <string>
#include "serializer.hpp"
#include "xbit_vector.hpp"

template <typename R>
void test(const R& r, const char* path) {
  using biomodern::utility::Serializer;
  {
    auto fout = std::ofstream{path, std::ios::binary};
    Serializer::save(fout, r);
  }
  auto r2 = R{};
  {
    auto fin = std::ifstream{path, std::ios::binary};
    Serializer::load(fin, r2);
  }
  assert(r == r2);
}

struct Point {
  double x;
  double y;
  auto operator<=>(const Point&) const = default;
};

int main() {
  test(std::vector{true, false, false}, "vector_bool.bin");
  test(std::vector{7, 5, 16, 8}, "vector_int.bin");
  test(std::string{"Exemplar"}, "string.bin");
  test(std::vector{Point{0.0, 0.0}, {1.0, 2.0}, {3.0, 4.0}}, "vector_point.bin");
  using biomodern::DibitVector;
  test(DibitVector<>{1, 0, 2, 3, 3, 0, 2}, "DibitVector.bin");
}
```

[continuous_range]: https://en.cppreference.com/w/cpp/ranges/contiguous_range
[trivially_copyable]: https://en.cppreference.com/w/cpp/types/is_trivially_copyable
[Boost.Serialization]: https://www.boost.org/doc/libs/1_66_0/boost/serialization/vector.hpp
[Biomodern.DitbitVector]: https://github.com/hewillk/biomodern-dibit_vector
