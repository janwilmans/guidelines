# Start Learning C++ in 5 minutes

I recently came across a comment of user `Aftab Rehmat` on the `C++ and Qt` Telegram group, I'm paraphrasing: "Can someone tell me the very basics of C++ for an absolute beginner". And the response was 'These is no beginning, its a sea of complexity and you need to dive deep to learn it'. I though this was both true and harmful. While these is definitely a lot you _can_ learn, I don't think you need all of it to start using it.

To use C++ for production you need to install it and just that comes with a lot of choices like, what OS are you targeting? are you using Windows, Linux or MacOS. What compiler will you use? etc. I would like leave these questions for later and go to a website called `https://compiler-explorer.com/`. Here you can experiment with C++ which having to install anything. 

Example 1:

[compiler explorer](https://compiler-explorer.com/z/5vYhnKh6h)

```cpp
#include <iostream>
#include <string>

int main()
{
    std::string first_name = "peter";
    std::string last_name = "griffin";
    std::cout << first_name << " " << last_name << "\n";
    return 0;
}
```

The first thing you need to know about C++ is that it comes in two parts, the language and libraries. And most prominent library is the STL (Standard Template Library). To begin using a library, it needs to be included and that is what we see at `#include <iostream>`. We want to output some text using std::cout (standard character output) and we want to use `std::string` (standard string) to make our example, so we include `#include <string>`.

Then the `main` part starts by creating a function called `int main()` which is the entry point of the program that is called by the system when the program starts.
The name must be `main` (or sometimes WinMain() on microsoft compilers, but that is not standard C++).

The function `main` returns a value `int` which is an integer (a signed numeric value) as the result of the program. On many OS's like windows, MacOS and Linux, there is a convention that the program returns zero if everything went OK and some non-zero error-code if the program failed is some way.

If you click on the [compiler explorer](https://compiler-explorer.com/z/5vYhnKh6h) link you will see first two lines, the output of building the program:

```
ASM generation compiler returned: 0
Execution build compiler returned: 0
```

the 'returned: 0' means the step was successful.
Then two more lines, which is the output of actually running the program:

```
Program returned: 42
peter griffin
```

Now you have the first step out of the way, you can experiment by modifying the program, try whatever you like, see what other things you can make happen or what errors occur. I think experimentation is the best way to learn. Also see the resources below for a wealth of information:

# resources to learn more:

- https://cppreference.com/     ('the manual', for looking up all standard library features, reading it takes some getting used to)
- https://roadmap.sh/cpp        (suggestions for a growth path, check it out, it has many nice explanations and guides)
- https://nullptr.nl/blog       (my own blog, mainly random posts not specifically targeted at beginners)
- https://nullptr.nl/2019/01/better-cpp-software-engineer/ (A specific blog post about 'how to learn more once you started')

## learning on youtube

- https://www.youtube.com/@TheCherno   (wide variety of subjects)
- https://www.youtube.com/@CppCon      (talks from many speakers from the last decade)
- https://www.youtube.com/@CopperSpice (more advanced topics explained in detail)
- https://www.youtube.com/@BoostCon

