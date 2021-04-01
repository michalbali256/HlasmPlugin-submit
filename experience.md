# Experience with developing High level assembler plugin
- The project was created with cooperation with a corporate company Broadcom while we were employed there. That was great, because we had a place to work on the project together and extra motivation. At the same time we had a chance to experience the real workplace and even got paid.
- Working under Broadcom, we needed to check all third party libraries if they are compliant with their policies (we were lucky, but sometimes it can restrict you from using your favorite library). However, even when not in a corporate company, try to choose external libraries that are actively maintained and have a large community. For example, we made a mistake of using a library that has only few commits per year and ended up refactoring it out of our code.
- [C++] If you choose C++ for your project and want your project to be cross-platform leave extra time for ensuring that. We used CMake and it was really hard and time consuming to make everything work on every platform. And every time a new platform you should support emerges, it needs extra research and work to ensure that. For example, in the middle of development, we supported Windows and Ubuntu linux, but when we had to support alpine linux, it was extra work.
- Docker images for each supported linux platform helped.
- Adopt automation early in your project. We ended up using GitHub Actions, which is free for public projects and I can definitely recommend it. Right now, each pull request in our project is checked by 6 different builds in different environments.
- Define your code format early in the project and enforce it in your pipeline. We thought that we do not need it at first, but it is inevitable that there will be some differences between code style of team members. Even code style of each programmer evolves in time. We ended up reformatting all the code close to the end of the project, changing all C++ files. We used clang-format, which was fine.
- Testing is important (I know, it is cliché, but it is true). We use Sonarcloud (free for public projects) to visualize which parts of code are not covered by tests. That is much more useful than just getting the information “your test coverage is 70%”. 
- [C++] Use sanitizers! We used clang address sanitizer and undefined behavior sanitizer (there is also thread sanitizer). Add it to the pipeline and run your tests with sanitizers. It was helpful especially when we needed to find a bug that caused crashes only on one specific platform.
- If applicable to your project, you can use fuzzer to enhance your application stability and find some bugs. We used clang fuzzer.