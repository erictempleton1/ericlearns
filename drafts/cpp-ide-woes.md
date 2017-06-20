Since most of my C++ programs are pretty small at the moment, I've been using [Visual Studio Code](https://code.visualstudio.com/) as my primary editor. The general consensus (or Microsoft lead consensus) seems to be that Visual Studio is better since it features `intellisense`. So, I've downloaded the [community version of Visual Studio](https://www.visualstudio.com/downloads/) to give it a try.

Editor wise, it's fantastic. I personally think that working with an IDE when learning a new language can be tremendously helpful. It will help you learn the syntax faster, spot mistakes faster, and learn the overall style of the language faster. One thing I usually avoid when learning a new language with an IDE is `code completion`,especially with standard or 3rd party libraries. 

Visual Studio keeps trying to move my brackets though, which is sort of annoying but I suppose it's trying to break me of my bad C++ habits.

```C++
int main() {
    for (int i = 0; i < 10; i++) {
		if (i > 5) {
			std::cout << i << std::endl;
		} else {
			std::cout << "too small" < std::endl;
		}
	}
}
```
Becomes:
```C++
int main() {
    for (int i = 0; i < 10; i++) {
		if (i > 5) {
			std::cout << i << std::endl;
		}
		else {
			std::cout << "too small" < std::endl;
		}
	}
}
```
Ok, fine. I'll comply with that. 

The other sort of problematic thing with Visual Studio for me at the moment is that I cannot for the life of me get the IDE to compile my code correctly or debug my code using breakpoints. It keep barking at me about missing dll files or unknown headers. I noticed that creating a new project in VS adds a bunch of stuff (read header files) in the project directory that _might_ not be needed.

I also gave [eclipse](http://www.eclipse.org/cdt/) a try, but it honestly felt like a Windows 98 application. I might re-visit it later.

For now I am sticking with Visual Studio, but I guess I'll keep compiling and running programs from the command line. Debugging with `cout` is sort of a pain and not good practice, but maybe it's ok for smaller programs for the time being.