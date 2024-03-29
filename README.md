<!-- PROJECT SHIELDS -->
<!--
*** I'm using markdown "reference style" links for readability.
*** Reference links are enclosed in brackets [ ] instead of parentheses ( ).
*** See the bottom of this document for the declaration of the reference variables
*** for contributors-url, forks-url, etc. This is an optional, concise syntax you may use.
*** https://www.markdownguide.org/basic-syntax/#reference-style-links
-->
[![Contributors][contributors-shield]][contributors-url]
[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
[![GPLv3 License][license-shield]][license-url]

[![build and test](https://github.com/sjrdc/px/actions/workflows/cmake.yml/badge.svg)](https://github.com/sjrdc/px/actions/workflows/cmake.yml)

<!-- ABOUT THE PROJECT -->
## About The Project

px is a command line argument parser written in modern C++.
### why?
Don't we have enough command line argument parsers already? Probably, yes...
This is meant to be an excercise in the use of some C++17 and C++20 features, as well as a way to provide a parser that
- adheres to the DRY principle; in many of these libraries, we need to first provide names to arguments, to later retrieve values with the same name, or assign some default, then validate the retrieved value. All of this could be a lot leaner.
- facilitates the use of the filesystem library (to obtain path variable directly from an argument)
- by no means aims to provide many different ways to specify arguments; one simple syntax is sufficient

### px is built with

* [love](https://www.youtube.com/watch?v=f_HmF84G7ZY)
* [standard C++20](https://isocpp.org)
* [cmake](https://cmake.org) >= 3.14
* [google test](https://github.com/google/googletest) >= 1.10

<!-- GETTING STARTED -->
## Getting Started

To get a local copy up and running follow these simple steps.

### prerequisites
px is a header only library without any dependencies; as such there are no prerequisites, except a compiler that supports modern C++.
Tests are written with google test and hence require the presence of this library to build and run.

<!-- USAGE EXAMPLES -->
### example usage
```c++
#include "px.h"
#include <filesystem>

namespace fs = std::filesystem;

void show_kittens(int i)
{
}

void store_kittens(const fs::path& p)
{
}

int main(int argc, char** argv)
{
    int i = 1;
    fs::path pth;
    
    px::command_line cli("the program name");
    cli.add_value_argument<int>("integer", "-i")
        .set_description("the number of kittens to show; must be large than 0 and 5 at most")
        .set_validator([](auto i) { return i > 0 && i <= 5; })
        .bind(&i);
    cli.add_value_argument<fs::path>("path", "-p")
        .set_required(true)
        .set_description("the path to use for storage of the shown kittens (must be an existing file)")
        .bind(&pth)
        .set_validator([](auto p) { return fs::exists(p) && fs::is_regular_file(p); });
    auto& help_arg = cli.add_flag_argument("help", "-h")
        .set_alternate_tag("--help");
    
    try
    {
        cli.parse(argc, argv);
    }
    catch (std::runtime_error& e)
    {
        if (help_arg.get_value())
        {
            cli.print_help(std::cout);
            return 0;
        }
        else
        {
            std::cerr << e.what() << "\n\n";
            cli.print_help(std::cerr);
            return 1          ;        
        }
    }
    
    show_kittens(i);
    store_kittens(pth);
    
    return 0;
}
```
### option syntax
While the aim is to provide just minimal support for option syntaxes, the following is assumed
- an argument can be tag-based (i.e. there is a tag preceding the value) or position-based (i.e., the arguments are distinguished by their order)
- an argument tag starts with a hyphen
- a long argument tag starts with two hyphens
- arguments of the two categories may both be specified when invoking a program from the command line
- two hyphens are used to separate tag-based arguments from position-based arguments 
<!-- ROADMAP -->
## Roadmap

See the [open issues](https://github.com/sjrdc/px/issues) for a list of proposed features (and known issues).

<!-- CONTRIBUTING -->
## Contributing

Contributions are what make the open source community such an amazing place to be learn, inspire, and create. Any contributions you make are greatly appreciated.

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

<!-- LICENSE -->
## License

Distributed under the GPLv3 License. See `LICENSE` for more information.

<!-- ACKNOWLEDGEMENTS -->
## Acknowledgements

* this readme file is base on [the BestREADME-Template](https://github.com/othneildrew/Best-README-Template)


<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[contributors-shield]: https://img.shields.io/github/contributors/sjrdc/px.svg?style=flat-square
[contributors-url]: https://github.com/sjrdc/px/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/sjrdc/px.svg?style=flat-square
[forks-url]: https://github.com/sjrdc/px/network/members
[stars-shield]: https://img.shields.io/github/stars/sjrdc/px.svg?style=flat-square
[stars-url]: https://github.com/sjrdc/px/stargazers
[issues-shield]: https://img.shields.io/github/issues/sjrdc/px.svg?style=flat-square
[issues-url]: https://github.com/sjrdc/px/issues
[license-shield]: https://img.shields.io/github/license/sjrdc/px.svg?style=flat-square
[license-url]: https://github.com/sjrdc/px/blob/main/LICENSE
