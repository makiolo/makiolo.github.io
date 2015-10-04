---
layout: post
title: Factory (with or without cache)
excerpt: "Factory and Memoize implementations in C++11"
tags: [factory,cpp]
comments: true
---

This is a header-only library with some of the most common design patterns implemmented in C++11/14.

## Contributing

The source for design-patterns-cpp14 is held at [design-patterns-cpp14](https://github.com/makiolo/design-patterns-cpp14) github.com site. To report an issue, use the [design-patterns-cpp14 issue tracker](https://github.com/makiolo/design-patterns-cpp14/issues) at github.com.

### Compile design-patterns-cpp14
It's a header-only library. Only need an include.

### Compile tests
You will need cmake (and a compiler).

{% highlight bash %}
$ git clone https://github.com/makiolo/design-patterns-cpp14.git
$ mkdir build
$ cd build
$ cmake ..
$ make (in unix) or compile generated solution (in windows)
{% endhighlight %}

### Example factory

{% highlight cpp %}
#include <iostream>
#include <assert.h>
#include <design-patterns/factory.h>

class Base
{
public:
	using Factory = dp14::Factory<Base, std::string, int>;

	explicit Base(const std::string& name, int q)
		: _name(name)
		, _q(q)
	{
		std::cout << "constructor " << _name << " - " << _q << std::endl;
	}
	virtual ~Base() { std::cout << "destruction" << std::endl; }

protected:
	std::string _name;
	int _q;
};

class A : public Base
{
public:
	explicit A(const std::string& name, int q) : Base(name, q) { ; }
	virtual ~A() = default;
};
DEFINE_HASH(A)

class B : public Base
{
public:
	explicit B(const std::string& name, int q) : Base(name, q) { ; }
	virtual ~B() = default;
};
DEFINE_HASH(B)

int main()
{
	Base::Factory factory;
	Base::Factory::Registrator<A> reg1(factory);
	Base::Factory::Registrator<B> reg2(factory);

	{
		// equivalent ways of create A
		std::shared_ptr<Base> a1 = factory.create<A>("first parameter", 2);
		std::shared_ptr<A> a2 = factory.create<A>("first parameter", 2);
		std::shared_ptr<Base> a3 = factory.create("A", "first parameter", 2);

		// equivalent ways of create B
		std::shared_ptr<Base> b1 = factory.create<B>("first parameter", 2);
		std::shared_ptr<B> b2 = factory.create<B>("first parameter", 2);
		std::shared_ptr<Base> b3 = factory.create("B", "first parameter", 2);

		assert(a1 != a2);
		assert(a3 != b1);
		assert(b1 != b2);
	}

	return(0);
}
{% endhighlight %}

### Example memoize (factory + cache)
{% highlight cpp %}
#include <iostream>
#include <sstream>
#include <assert.h>
#include <design-patterns/memoize.h>

class Base
{
public:
	using memoize = dp14::memoize<Base, std::string, int>;

	explicit Base(const std::string& name, int q)
		: _name(name)
		, _q(q)
	{
		std::cout << "constructor " << _name << " - " << _q << std::endl;
	}
	virtual ~Base() { std::cout << "destruction" << std::endl; }

protected:
	std::string _name;
	int _q;
};

class A : public Base
{
public:
	explicit A(const std::string& name, int q) : Base(name, q) { ; }
	virtual ~A() = default;
};
DEFINE_HASH(A)

class B : public Base
{
public:
	explicit B(const std::string& name, int q) : Base(name, q) { ; }
	virtual ~B() = default;
};
DEFINE_HASH(B)

int main()
{
	Base::memoize m;
	Base::memoize::registrator<A> reg1(m);
	Base::memoize::registrator<B> reg2(m);

	{
		std::shared_ptr<Base> a1 = m.get<A>("first parameter", 2);
		assert( m.exists<A>("first parameter", 2) == true );
	}
	assert( m.exists<A>("first parameter", 2) == false );

	{
		std::shared_ptr<Base> a1 = m.get<A>("first parameter", 2);
		std::shared_ptr<A> a2 = m.get<A>("first parameter", 2);
		assert(a1 == a2);

		std::shared_ptr<Base> a3 = m.get("A", "first parameter", 4);
		assert(a2 != a3);

		std::shared_ptr<Base> b1 = m.get<B>("first parameter", 2);
		std::shared_ptr<B> b2 = m.get<B>("first parameter", 2);
		assert(b1 == b2);

		std::shared_ptr<Base> b3 = m.get("B", "first parameter", 4);
		assert(b2 != b3);

		assert( m.exists<A>("first parameter", 2) == true );
	}
	assert( m.exists<A>("first parameter", 2) == false );

	return(0);
}
{% endhighlight %}


