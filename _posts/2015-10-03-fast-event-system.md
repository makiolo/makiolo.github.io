---
layout: post
title: Fast Event System
excerpt: "Channels implementation in C++11."
tags: [sample post, code, highlighting]
comments: true
---

bla bla bla bla code C++.
Enfatizar con: `/_sass/_pygments.scss`.
Enfatizar con: [/_sass/_pygments.scss](link).
Enfatizar con: */_sass/_pygments.scss*.
Enfatizar con: **/_sass/_pygments.scss**.
Ejemplo de Referencia[^1].

[^1]: <http://en.wikipedia.org/wiki/Syntax_highlighting>

{% highlight cpp %}
int main(int, const char**)
{
	fes::async_fast<int, std::string, double> sync;

	// test connect in context
	{
		fes::connection<int, std::string, double> conn(
			sync.connect([](int n, const std::string& str, double r)
				{
					std::cout << "received message: " << std::endl;
					std::cout << "n = " << n << std::endl;
					std::cout << "str = " << str << std::endl;
					std::cout << "r = " << r << std::endl;
					if (str == "kill")
					{
						exit(1);
					}
				}));
		sync(5, "hello world", 11.0);
		sync.update();
	}
	sync(6, "kill", 12.0);
	sync.update();

	return 0;
}
{% endhighlight %}

> comentario

# seccion 2

* **elem1**: that is a asdjkhdl
* **elem2**: that is a asdjkhdl
* **elem3**: that is a asdjkhdl
* **elem4**: that is a asdjkhdl

