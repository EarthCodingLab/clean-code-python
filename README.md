# clean-code-python

[![Build Status](https://travis-ci.com/zedr/clean-code-python.svg?branch=master)](https://travis-ci.com/zedr/clean-code-python)
[![](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/download/releases/3.8.3/)

## Table of Contents

- [clean-code-python](#clean-code-python)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [**Variables**](#variables)
    - [의미 있고 발음하기 쉬운 변수명을 사용하기](#의미-있고-발음하기-쉬운-변수명을-사용하기)
    - [같은 타입의 변수에는 같은 단어 사용하기](#같은-타입의-변수에는-같은-단어-사용하기)
    - [검색하기 쉬운 이름 사용하기](#검색하기-쉬운-이름-사용하기)
    - [설명 가능한 변수 사용하기](#설명-가능한-변수-사용하기)
    - [암시적인 의미 피하기](#암시적인-의미-피하기)
    - [불필요한 맥락 제거하기](#불필요한-맥락-제거하기)
    - [Use default arguments instead of short circuiting or conditionals](#use-default-arguments-instead-of-short-circuiting-or-conditionals)
  - [**Functions**](#functions)
    - [함수는 한 가지 일만 하기](#함수는-한-가지-일만-하기)
    - [함수 인자 (이상적으로는 2개 이하)](#함수-인자-이상적으로는-2개-이하)
    - [함수 이름은 함수가 무슨 일을 하는지 알기 쉽게](#함수-이름은-함수가-무슨-일을-하는지-알기-쉽게)
    - [함수는 하나의 추상화 수준만 가지기](#함수는-하나의-추상화-수준만-가지기)
    - [함수 파라미터로 플래그 사용하지 않기](#함수-파라미터로-플래그-사용하지-않기)
    - [사이드 이펙트 피하기](#사이드-이펙트-피하기)
  - [**Classes**](#classes)
    - [**Single Responsibility Principle (SRP)**](#single-responsibility-principle-srp)
    - [**Open/Closed Principle (OCP)**](#openclosed-principle-ocp)
    - [**Liskov Substitution Principle (LSP)**](#liskov-substitution-principle-lsp)
    - [**Interface Segregation Principle (ISP)**](#interface-segregation-principle-isp)
    - [**Dependency Inversion Principle (DIP)**](#dependency-inversion-principle-dip)
  - [**Don't repeat yourself (DRY)**](#dont-repeat-yourself-dry)
  - [**Translations**](#translations)

## Introduction

Robert C. Martin의 저서
[*Clean Code*](https://product.kyobobook.co.kr/detail/S000001032980)의 소프트웨어 엔지니어링 원칙들을 Python으로 소개합니다. 본 문서는 스타일 가이드가 아닙니다. Python에서 읽기 쉽고 재사용 가능하며 리팩토링 가능한 소프트웨어를 제작하기 위한 가이드입니다.

Not every principle herein has to be strictly followed, and even fewer will be
universally agreed upon. These are guidelines and nothing more, but they are
ones codified over many years of collective experience by the authors of *Clean
Code*.

[clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript)를 바탕으로 작성되었습니다.

Python 3.7 이상 버전을 사용합니다.

## **Variables**

### 의미 있고 발음하기 쉬운 변수명을 사용하기

**나쁜 예:**

```python
import datetime

ymdstr = datetime.date.today().strftime("%y-%m-%d")
```

추가로, 변수명에 str이라는 타입을 명시해 줄 필요가 없습니다. 

**좋은 예:**

```python
import datetime

current_date: str = datetime.date.today().strftime("%y-%m-%d")
```

**[⬆ back to top](#table-of-contents)**

### 같은 타입의 변수에는 같은 단어 사용하기

**나쁜 예:**
동일한 엔티티에 대해 세 가지 다른 이름을 사용하고 있습니다:

```python
def get_user_info(): pass


def get_client_data(): pass


def get_customer_record(): pass
```

**좋은 예**: 동일한 엔티티라면, 함수에서 엔티티를 일관되게 참조해야 합니다:

```python
def get_user_info(): pass


def get_user_data(): pass


def get_user_record(): pass
```

**더 좋은 방법으로**
Python은 객체 지향 언어입니다. 따라서 가능하다면 의미가 있는 인스턴스 attributes, 프로퍼티 메서드 또는 메서드와 같이 코드에서 엔티티의 구체적인 구현과 함께 함수를 패키징합니다.

```python
from typing import Union, Dict


class Record:
    pass


class User:
    info: str

    @property
    def data(self) -> Dict[str, str]:
        return {}

    def get_record(self) -> Union[Record, None]:
        return Record()
```

**[⬆ back to top](#table-of-contents)**

### 검색하기 쉬운 이름 사용하기

우리는 통상 쓰는 것보다 더 많은 양의 코드를 읽습니다. 따라서 코드가 읽기 쉽고 검색하기 쉬운 것이 중요합니다. 프로그램을 이해하는데 의미가 있도록 변수를 짓지 않는다면 읽는 사람이 힘들 것 입니다. 이름을 검색하기 쉽도록 작성합니다.

**나쁜 예:**

```python
import time

# What is the number 86400 for again?
time.sleep(86400)
```

**좋은 예**:

```python
import time

# Declare them in the global namespace for the module.
SECONDS_IN_A_DAY = 60 * 60 * 24
time.sleep(SECONDS_IN_A_DAY)
```

**[⬆ back to top](#table-of-contents)**

### 설명 가능한 변수 사용하기

**나쁜 예**

```python
import re

address = "One Infinite Loop, Cupertino 95014"
city_zip_code_regex = r"^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$"

matches = re.match(city_zip_code_regex, address)
if matches:
    print(f"{matches[1]}: {matches[2]}")
```

**개선된 예**:

더 낫긴 하지만 여전히 정규식에 크게 의존하고 있습니다.

```python
import re

address = "One Infinite Loop, Cupertino 95014"
city_zip_code_regex = r"^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$"
matches = re.match(city_zip_code_regex, address)

if matches:
    city, zip_code = matches.groups()
    print(f"{city}: {zip_code}")
```

**좋은 예**:

서브패턴을 활용한 네이밍으로 정규식의 의존성을 줄일 수 있습니다.



```python
import re

address = "One Infinite Loop, Cupertino 95014"
city_zip_code_regex = r"^[^,\\]+[,\\\s]+(?P<city>.+?)\s*(?P<zip_code>\d{5})?$"

matches = re.match(city_zip_code_regex, address)
if matches:
    print(f"{matches['city']}, {matches['zip_code']}")
```

**[⬆ back to top](#table-of-contents)**

### 암시적인 의미 피하기

코드를 읽는 사람이 변수의 의미를 해석해야 하도록 두지 마십시오.  
명시적인 것이 암시적인 것보다 낫습니다.

**나쁜 예:**

```python
seq = ("Austin", "New York", "San Francisco")

for item in seq:
    # do_stuff()
    # do_some_other_stuff()

    # Wait, what's `item` again?
    print(item)
```

**좋은 예**:

```python
locations = ("Austin", "New York", "San Francisco")

for location in locations:
    # do_stuff()
    # do_some_other_stuff()
    # ...
    print(location)
```

**[⬆ back to top](#table-of-contents)**

### 불필요한 맥락 제거하기

클래스/객체 이름에서 알 수 있는 내용이 있으면, 해당 변수명에서 반복하지 마십시오. 

**나쁜 예:**

```python
class Car:
    car_make: str
    car_model: str
    car_color: str
```

**좋은 예**:

```python
class Car:
    make: str
    model: str
    color: str
```

**[⬆ back to top](#table-of-contents)**

### Use default arguments instead of short circuiting or conditionals

**Tricky**

Why write:

```python
import hashlib


def create_micro_brewery(name):
    name = "Hipster Brew Co." if name is None else name
    slug = hashlib.sha1(name.encode()).hexdigest()
    # etc.
```

... when you can specify a default argument instead? This also makes it clear
that you are expecting a string as the argument.

**좋은 예**:

```python
import hashlib


def create_micro_brewery(name: str = "Hipster Brew Co."):
    slug = hashlib.sha1(name.encode()).hexdigest()
    # etc.
```

**[⬆ back to top](#table-of-contents)**

## **Functions**

### 함수는 한 가지 일만 하기

소프트웨어 엔지니어링에서 가장 중요한 규칙입니다. 함수가 한가지 이상의 일을 수행한다면 함수를 구성, 테스트, 추론하기 어려워집니다. 함수를 한 가지 일만 수행하도록 분리한다면 쉽게 리팩토링 할 수 있으며, 코드는 더욱 깔끔해집니다. 이 가이드에서 이 한 가지만 기억하더라도 다른 많은 개발자보다 앞서게 될 것입니다. 

**나쁜 예:**

```python
from typing import List


class Client:
    active: bool


def email(client: Client) -> None:
    pass


def email_clients(clients: List[Client]) -> None:
    """Filter active clients and send them an email.
    """
    for client in clients:
        if client.active:
            email(client)
```

**좋은 예**:

```python
from typing import List


class Client:
    active: bool


def email(client: Client) -> None:
    pass


def get_active_clients(clients: List[Client]) -> List[Client]:
    """Filter active clients.
    """
    return [client for client in clients if client.active]


def email_clients(clients: List[Client]) -> None:
    """Send an email to a given list of clients.
    """
    for client in get_active_clients(clients):
        email(client)
```

generator를 사용하여 더욱 개선할 수 있습니다.

**더 좋은 예**

```python
from typing import Generator, Iterator


class Client:
    active: bool


def email(client: Client):
    pass


def active_clients(clients: Iterator[Client]) -> Generator[Client, None, None]:
    """Only active clients"""
    return (client for client in clients if client.active)


def email_client(clients: Iterator[Client]) -> None:
    """Send an email to a given list of clients.
    """
    for client in active_clients(clients):
        email(client)
```

**[⬆ back to top](#table-of-contents)**

### 함수 인자 (이상적으로는 2개 이하)

함수에 많은 파라미터가 있는 경우 함수가 너무 많은 작업 (하나 이상의 책임) 을 수행하고 있다는 신호일 수 있습니다. 이상적으로 2개 이하의 파라미터를 가지도록 함수를 분해해보세요.

만약 함수가 하나의 책임만을 가진다면, 일부 또는 모든 파라미터를 함수에 전달할 특수화된 객체로 묶어보는 것을 고려해보세요. 이러한 파라미터는 전용 데이터 구조로 표현될 수 있는 단일 엔티티의 애트리뷰트일 수 있습니다. 또한 이러한 엔티티는 다른 곳에서도 재사용할 수 있습니다. 여러 파라미터를 사용하는 것보다 이 방법이 나은 이유는 함수 내부에서 해당 파라미터로 수행되는 일부 계산을 새 엔티티에 속하는 메서드로 옮겨 함수의 복잡도를 줄일 수 있기 떄문입니다.

**나쁜 예:**

```python
def create_menu(title, body, button_text, cancellable):
    pass
```

**Java-esque**:

```python
class Menu:
    def __init__(self, config: dict):
        self.title = config["title"]
        self.body = config["body"]
        # ...


menu = Menu(
    {
        "title": "My Menu",
        "body": "Something about my menu",
        "button_text": "OK",
        "cancellable": False
    }
)
```

**Also good**

```python
class MenuConfig:
    """A configuration for the Menu.

    Attributes:
        title: The title of the Menu.
        body: The body of the Menu.
        button_text: The text for the button label.
        cancellable: Can it be cancelled?
    """
    title: str
    body: str
    button_text: str
    cancellable: bool = False


def create_menu(config: MenuConfig) -> None:
    title = config.title
    body = config.body
    # ...


config = MenuConfig()
config.title = "My delicious menu"
config.body = "A description of the various items on the menu"
config.button_text = "Order now!"
# The instance attribute overrides the default class attribute.
config.cancellable = True

create_menu(config)
```

**Fancy**

```python
from typing import NamedTuple


class MenuConfig(NamedTuple):
    """A configuration for the Menu.

    Attributes:
        title: The title of the Menu.
        body: The body of the Menu.
        button_text: The text for the button label.
        cancellable: Can it be cancelled?
    """
    title: str
    body: str
    button_text: str
    cancellable: bool = False


def create_menu(config: MenuConfig):
    title, body, button_text, cancellable = config
    # ...


create_menu(
    MenuConfig(
        title="My delicious menu",
        body="A description of the various items on the menu",
        button_text="Order now!"
    )
)
```

**Even fancier**

```python
from dataclasses import astuple, dataclass


@dataclass
class MenuConfig:
    """A configuration for the Menu.

    Attributes:
        title: The title of the Menu.
        body: The body of the Menu.
        button_text: The text for the button label.
        cancellable: Can it be cancelled?
    """
    title: str
    body: str
    button_text: str
    cancellable: bool = False


def create_menu(config: MenuConfig):
    title, body, button_text, cancellable = astuple(config)
    # ...


create_menu(
    MenuConfig(
        title="My delicious menu",
        body="A description of the various items on the menu",
        button_text="Order now!"
    )
)
```

**Even fancier, Python3.8+ only**

```python
from typing import TypedDict


class MenuConfig(TypedDict):
    """A configuration for the Menu.

    Attributes:
        title: The title of the Menu.
        body: The body of the Menu.
        button_text: The text for the button label.
        cancellable: Can it be cancelled?
    """
    title: str
    body: str
    button_text: str
    cancellable: bool


def create_menu(config: MenuConfig):
    title = config["title"]
    # ...


create_menu(
    # You need to supply all the parameters
    MenuConfig(
        title="My delicious menu",
        body="A description of the various items on the menu",
        button_text="Order now!",
        cancellable=True
    )
)
```

**[⬆ back to top](#table-of-contents)**

### 함수 이름은 함수가 무슨 일을 하는지 알기 쉽게

**나쁜 예:**

```python
class Email:
    def handle(self) -> None:
        pass


message = Email()
# What is this supposed to do again?
message.handle()
```

**Good:**

```python
class Email:
    def send(self) -> None:
        """Send this message"""


message = Email()
message.send()
```

**[⬆ back to top](#table-of-contents)**

### 함수는 하나의 추상화 수준만 가지기

만약 하나 이상의 추상화 수준을 가진다면, 함수는 너무 많은 일을 할 수도 있습니다. 함수를 재사용 가능하고 테스트하기 쉽게 분리하세요. 

**나쁜 예:**

```python
# type: ignore

def parse_better_js_alternative(code: str) -> None:
    regexes = [
        # ...
    ]

    statements = code.split('\n')
    tokens = []
    for regex in regexes:
        for statement in statements:
            pass

    ast = []
    for token in tokens:
        pass

    for node in ast:
        pass
```

**좋은 예:**

```python
from typing import Tuple, List, Dict

REGEXES: Tuple = (
    # ...
)


def parse_better_js_alternative(code: str) -> None:
    tokens: List = tokenize(code)
    syntax_tree: List = parse(tokens)

    for node in syntax_tree:
        pass


def tokenize(code: str) -> List:
    statements = code.split()
    tokens: List[Dict] = []
    for regex in REGEXES:
        for statement in statements:
            pass

    return tokens


def parse(tokens: List) -> List:
    syntax_tree: List[Dict] = []
    for token in tokens:
        pass

    return syntax_tree
```

**[⬆ back to top](#table-of-contents)**

### 함수 파라미터로 플래그 사용하지 않기

플래그는 사용자에게 함수가 한 가지 이상의 일을 한다는 것을 알려줍니다. 함수는 한가지 일만을 수행해야 합니다. 따라서 불리언 값에 따라 다른 코드 경로를 따르도록 함수를 분리하세요. 

**Bad:**

```python
from tempfile import gettempdir
from pathlib import Path


def create_file(name: str, temp: bool) -> None:
    if temp:
        (Path(gettempdir()) / name).touch()
    else:
        Path(name).touch()
```

**Good:**

```python
from tempfile import gettempdir
from pathlib import Path


def create_file(name: str) -> None:
    Path(name).touch()


def create_temp_file(name: str) -> None:
    (Path(gettempdir()) / name).touch()
```

**[⬆ back to top](#table-of-contents)**

### 사이드 이펙트 피하기

함수가 입력값을 받아서 다른 값들을 반환하는 것 이외에 다른 작업을 수행하면 사이드 이펙트를 일으킵니다. 예를 들어, 파일 쓰기, 전역 변수 수정, 실수로 모르는 사람에게 돈을 송금하는 것 등이 있습니다.

프로그램에서 때때로 사이드 이펙트가 필요할 수 있습니다. 예를 들어, 이전 예와 같이 파일에 쓰기 작업이 필요할 수 있습니다. 이러한 경우에는 사이트 이펙트를 포함하는 위치를 중앙 집중화하고 표시해야 합니다. 특정 파일에 쓰기 작업을 하는 여러 함수와 클래스를 가지지 말고 하나의 서비스만을 가지세요. 

요점은 구조 없이 객체 간에 상태를 공유하거나, 누구나 쓸 수 있는 가변 데이터 유형을 사용하거나, 클래스의 인스턴스를 사용하거나, 부작용이 발생하는 위치를 중앙 집중화하지 않는 것과 같은 일반적인 함정을 피하는 것입니다. 이를 통해 대다수의 다른 프로그래머보다 행복하게 작업할 수 있을 것입니다.

**나쁜 예:**

```python
# type: ignore

# This is a module-level name.
# It's good practice to define these as immutable values, such as a string.
# However...
fullname = "Ryan McDermott"


def split_into_first_and_last_name() -> None:
    # The use of the global keyword here is changing the meaning of the
    # the following line. This function is now mutating the module-level
    # state and introducing a side-effect!
    global fullname
    fullname = fullname.split()


split_into_first_and_last_name()

# MyPy will spot the problem, complaining about 'Incompatible types in
# assignment: (expression has type "List[str]", variable has type "str")'
print(fullname)  # ["Ryan", "McDermott"]

# OK. It worked the first time, but what will happen if we call the
# function again?
```

**좋은 예:**

```python
from typing import List, AnyStr


def split_into_first_and_last_name(name: AnyStr) -> List[AnyStr]:
    return name.split()


fullname = "Ryan McDermott"
name, surname = split_into_first_and_last_name(fullname)

print(name, surname)  # => Ryan McDermott
```

**Also good**

```python
from dataclasses import dataclass


@dataclass
class Person:
    name: str

    @property
    def name_as_first_and_last(self) -> list:
        return self.name.split()


# The reason why we create instances of classes is to manage state!
person = Person("Ryan McDermott")
print(person.name)  # => "Ryan McDermott"
print(person.name_as_first_and_last)  # => ["Ryan", "McDermott"]
```

**[⬆ back to top](#table-of-contents)**

## **Classes**

### **Single Responsibility Principle (SRP)**

Robert C. Martin writes:

> A class should have only one reason to change.

"Reasons to change" are, in essence, the responsibilities managed by a class or
function.

In the following example, we create an HTML element that represents a comment
with the version of the document:

**Bad**

```python
from importlib import metadata


class VersionCommentElement:
     """An element that renders an HTML comment with the program's version number
     """

     def get_version(self) -> str:
          """Get the package version"""
          return metadata.version("pip")

     def render(self) -> None:
          print(f'<!-- Version: {self.get_version()} -->')


VersionCommentElement().render()
```

This class has two responsibilities:

- Retrieve the version number of the Python package
- Render itself as an HTML element

Any change to one or the other carries the risk of impacting the other.

We can rewrite the class and decouple these responsibilities:

**Good**

```python
from importlib import metadata


def get_version(pkg_name: str) -> str:
     """Retrieve the version of a given package"""
     return metadata.version(pkg_name)


class VersionCommentElement:
     """An element that renders an HTML comment with the program's version number
     """

     def __init__(self, version: str):
          self.version = version

     def render(self) -> None:
          print(f'<!-- Version: {self.version} -->')


VersionCommentElement(get_version("pip")).render()
```

The result is that the class only needs to take care of rendering itself. It
receives the version text during instantiation and this text is generated by
calling a separate function, `get_version()`. Changing the class has no impact
on the other, and vice-versa, as long as the contract between them does not
change, i.e. the function provides a string and the class `__init__` method
accepts a string.

As an added bonus, the `get_version()` is now reusable elsewhere.

### **개방/폐쇄 원칙(Open/Closed Principle (OCP))**

> “시스템을 확장함으로써 새로운 기능을 통합하세요. (시스템을) 수정하지 말고요.”, Uncle Bob.

객체는 확장에는 개방적이어야 하지만 수정에는 폐쇄적이어야 합니다. 내부 계약의 변경 없이 객체(클래스 등)에서 제공하는 기능을 활용해 확장이 가능해야 합니다. 객체가 깔끔하게 확장될 수 있도록 디자인되어야 이와 같은 방법이 가능해집니다.

다음의 예시는 HTTP 요청을 처리하고 응답을 반환하는 간단한 웹 프레임워크를 구현합니다. `View` 클래스에는 HTTP 서버가 GET 요청을 클라이언트로부터 받았을 때 호출될 단일 메서드 `.get()`가 있습니다.

`View`는 의도적으로 단순하고 `text/plain` 응답을 반환합니다. 또한 템플릿 기반의 HTML 응답을 반환하고자 하기에, `TemplateView`라는 하위 클래스를 사용합니다.

**나쁜 예**

```python
from dataclasses import dataclass


@dataclass
class Response:
     """An HTTP response"""

     status: int
     content_type: str
     body: str


class View:
     """A simple view that returns plain text responses"""

     def get(self, request) -> Response:
          """Handle a GET request and return a message in the response"""
          return Response(
               status=200,
               content_type='text/plain',
               body="Welcome to my web site"
          )


class TemplateView(View):
     """A view that returns HTML responses based on a template file."""

     def get(self, request) -> Response:
          """Handle a GET request and return an HTML document in the response"""
          with open("index.html") as fd:
               return Response(
                    status=200,
                    content_type='text/html',
                    body=fd.read()
               )

```

`TemplateView` 클래스는 더 많은 기능을 수행하기 위해 부모 클래스의 내부 동작을 수정했습니다. 이렇게 함으로써, 이제 일정 시간 정지해야하는 `.get()` 메서드의 구현을 변경하지 않기 위해 `View`에 의존하게 됩니다. 예를 들어, `View`의 파생 클래스들에 대한 몇 가지 추가 검사를 도입하려할 때, 적어도 하나의 하위 유형에 오버라이딩 되어있고 이를 업데이트해야 하기 때문에 불가능합니다.

이 클래스들을 다시 디자인해서 문제를 해결하고 `View` 클래스가 (수정하지 않고) 깔끔하게 확장될 수 있도록 해봅시다.

**좋은 예**

```python
from dataclasses import dataclass


@dataclass
class Response:
     """An HTTP response"""

     status: int
     content_type: str
     body: str


class View:
     """A simple view that returns plain text responses"""

     content_type = "text/plain"

     def render_body(self) -> str:
          """Render the message body of the response"""
          return "Welcome to my web site"

     def get(self, request) -> Response:
          """Handle a GET request and return a message in the response"""
          return Response(
               status=200,
               content_type=self.content_type,
               body=self.render_body()
          )


class TemplateView(View):
     """A view that returns HTML responses based on a template file."""

     content_type = "text/html"
     template_file = "index.html"

     def render_body(self) -> str:
          """Render the message body as HTML"""
          with open(self.template_file) as fd:
               return fd.read()


```
body의 소스를 바꾸려면 `render_body()`에 오버라이딩해야 합니다만, 이 방식은 **'오버라이딩을 위해 서브타입을 불러온다'** 라는 단일이고 잘 정의된 책임을 가집니다. 이는 서브타입에 의해 확장될 수 있게끔 디자인되었습니다.

객체 상속과 객체 구성의 장점을 둘 다 활용하는 또 다른 좋은 방법은 [Mixins](https://docs.djangoproject.com/en/4.1/topics/class-based-views/mixins/)을 사용하는 것입니다.

Mixins는 다른 관련 클래스들과 독점적으로 사용되게끔 하게 해주는 베어본 클래스입니다. 대상의 동작을 변경하기 위해 다중상속을 활용하여 '혼합' 되어있습니다.

몇 가지 규칙들이 있습니다.

- Mixins는 항상 `객체`로부터 상속받아야 합니다.
- Mixins는 다음과 같이 항상 대상 클래스 앞에 와야합니다. `class Foo(MixinA, MixinB, TargetClass): ...`

**다른 좋은 예**

```python
from dataclasses import dataclass, field
from typing import Protocol


@dataclass
class Response:
     """An HTTP response"""

     status: int
     content_type: str
     body: str
     headers: dict = field(default_factory=dict)


class View:
     """A simple view that returns plain text responses"""

     content_type = "text/plain"

     def render_body(self) -> str:
          """Render the message body of the response"""
          return "Welcome to my web site"

     def get(self, request) -> Response:
          """Handle a GET request and return a message in the response"""
          return Response(
               status=200,
               content_type=self.content_type,
               body=self.render_body()
          )


class TemplateRenderMixin:
     """A mixin class for views that render HTML documents using a template file
 
     Not to be used by itself!
     """
     template_file: str = ""

     def render_body(self) -> str:
          """Render the message body as HTML"""
          if not self.template_file:
               raise ValueError("The path to a template file must be given.")

          with open(self.template_file) as fd:
               return fd.read()


class ContentLengthMixin:
     """A mixin class for views that injects a Content-Length header in the
     response
 
     Not to be used by itself!
     """

     def get(self, request) -> Response:
          """Introspect and amend the response to inject the new header"""
          response = super().get(request)  # type: ignore
          response.headers['Content-Length'] = len(response.body)
          return response


class TemplateView(TemplateRenderMixin, ContentLengthMixin, View):
     """A view that returns HTML responses based on a template file."""

     content_type = "text/html"
     template_file = "index.html"

```
보시다시피, Mixins는 객체를 관련 기능들을 함께 패키징하여 단일 책임을 갖고 있고 깔끔하게 분리될 수 있는 재사용 가능한 클래스로 만듭니다. 클래스 확장은 추가 클래스의 '혼합'으로 달성됩니다.

인기있는 Django 프로젝트는 Mixins을 많이 활용하여 클래스 기반의 뷰를 구성합니다.

FIXME: Mixins에서 `typing.Protocol`를 사용해 타입 체킹하는 방법이 명확해지면 코드에서도 다시 활성화하도록 합니다.

### **Liskov Substitution Principle (LSP)**

> “Functions that use pointers or references to base classes
> must be able to use objects of derived classes without knowing it”,
> Uncle Bob.

This principle is named after Barbara Liskov, who collaborated with fellow
computer scientist Jeannette Wing on the seminal paper
*"A behavioral notion of subtyping" (1994). A core tenet of the paper is that
"a subtype (must) preserve the behaviour of the supertype methods and also all
invariant and history properties of its supertype".

In essence, a function accepting a supertype should also accept all its
subtypes with no modification.

Can you spot the problem with the following code?

**Bad**

```python
from dataclasses import dataclass


@dataclass
class Response:
     """An HTTP response"""

     status: int
     content_type: str
     body: str


class View:
     """A simple view that returns plain text responses"""

     content_type = "text/plain"

     def render_body(self) -> str:
          """Render the message body of the response"""
          return "Welcome to my web site"

     def get(self, request) -> Response:
          """Handle a GET request and return a message in the response"""
          return Response(
               status=200,
               content_type=self.content_type,
               body=self.render_body()
          )


class TemplateView(View):
     """A view that returns HTML responses based on a template file."""

     content_type = "text/html"

     def get(self, request, template_file: str) -> Response:  # type: ignore
          """Render the message body as HTML"""
          with open(template_file) as fd:
               return Response(
                    status=200,
                    content_type=self.content_type,
                    body=fd.read()
               )


def render(view: View, request) -> Response:
     """Render a View"""
     return view.get(request)

```

The expectation is that `render()` function will be able to work with `View`
and its subtype `TemplateView`, but the latter has broken compatibility by
modifying the signature of the `.get()` method. The function will raise
a `TypeError`
exception when used with `TemplateView`.

If we want the `render()` function to work with any subtype of `View`, we must
pay attention not to break its public-facing protocol. But how do we know what
constitutes it for a given class? Type hinters like *mypy* will raise an error
when it detects mistakes like this:

```
error: Signature of "get" incompatible with supertype "View"
<string>:36: note:      Superclass:
<string>:36: note:          def get(self, request: Any) -> Response
<string>:36: note:      Subclass:
<string>:36: note:          def get(self, request: Any, template_file: str) -> Response
```

### **Interface Segregation Principle (ISP)**

> “Keep interfaces small
> so that users don’t end up depending on things they don’t need.”,
> Uncle Bob.

Several well known object oriented programming languages, like Java and Go,
have a concept called interfaces. An interface defines the public methods and
properties of an object without implementing them. They are useful when we
don't want to couple the signature of a function to a concrete object; we'd
rather say "I don't care what object you give me, as long as it has certain
methods and attributes I expect to make use of".

Python does not have interfaces. We have Abstract Base Classes instead, which
are a little different, but can serve the same purpose.

**Good**

```python

from abc import ABCMeta, abstractmethod


# Define the Abstract Class for a generic Greeter object
class Greeter(metaclass=ABCMeta):
     """An object that can perform a greeting action."""

     @staticmethod
     @abstractmethod
     def greet(name: str) -> None:
          """Display a greeting for the user with the given name"""


class FriendlyActor(Greeter):
     """An actor that greets the user with a friendly salutation"""

     @staticmethod
     def greet(name: str) -> None:
          """Greet a person by name"""
          print(f"Hello {name}!")


def welcome_user(user_name: str, actor: Greeter):
     """Welcome a user with a given name using the provided actor"""
     actor.greet(user_name)


welcome_user("Barbara", FriendlyActor())
```

Now imagine the following scenario: we have a certain number of PDF documents
that we author and want to serve to our web site visitors. We are using a
Python web framework and we might be tempted to design a class to manage these
documents, so we go ahead and design a comprehensive abstract base class for
our document.

**Error**

```python
import abc


class Persistable(metaclass=abc.ABCMeta):
     """Serialize a file to data and back"""

     @property
     @abc.abstractmethod
     def data(self) -> bytes:
          """The raw data of the file"""

     @classmethod
     @abc.abstractmethod
     def load(cls, name: str):
          """Load the file from disk"""

     @abc.abstractmethod
     def save(self) -> None:
          """Save the file to disk"""


# We just want to serve the documents, so our concrete PDF document
# implementation just needs to implement the `.load()` method and have
# a public attribute named `data`.

class PDFDocument(Persistable):
     """A PDF document"""

     @property
     def data(self) -> bytes:
          """The raw bytes of the PDF document"""
          ...  # Code goes here - omitted for brevity

     @classmethod
     def load(cls, name: str):
          """Load the file from the local filesystem"""
          ...  # Code goes here - omitted for brevity


def view(request):
     """A web view that handles a GET request for a document"""
     requested_name = request.qs['name']  # We want to validate this!
     return PDFDocument.load(requested_name).data

```

But we can't! If we don't implement the `.save()` method, an exception will be
raised:

```
Can't instantiate abstract class PDFDocument with abstract method save.
```

That's annoying. We don't really need to implement `.save()` here. We could
implement a dummy method that does nothing or raises `NotImplementedError`, but
that's useless code that we will need to maintain.

At the same time, if we remove `.save()` from the abstract class now we will
need to add it back when we will later implement a way for users to submit
their documents, bringing us back to the same situation as before.

The problem is that we have written an *interface* that has features we don't
need right now as we are not using them.

The solution is to decompose the interface into smaller and composable
interfaces that segregate each feature.

**Good**

```python
import abc


class DataCarrier(metaclass=abc.ABCMeta):
     """Carries a data payload"""

     @property
     def data(self):
          ...


class Loadable(DataCarrier):
     """Can load data from storage by name"""

     @classmethod
     @abc.abstractmethod
     def load(cls, name: str):
          ...


class Saveable(DataCarrier):
     """Can save data to storage"""

     @abc.abstractmethod
     def save(self) -> None:
          ...


class PDFDocument(Loadable):
     """A PDF document"""

     @property
     def data(self) -> bytes:
          """The raw bytes of the PDF document"""
          ...  # Code goes here - omitted for brevity

     @classmethod
     def load(cls, name: str) -> None:
          """Load the file from the local filesystem"""
          ...  # Code goes here - omitted for brevity


def view(request):
     """A web view that handles a GET request for a document"""
     requested_name = request.qs['name']  # We want to validate this!
     return PDFDocument.load(requested_name).data

```

### **Dependency Inversion Principle (DIP)**

> “Depend upon abstractions, not concrete details”,
> Uncle Bob.

Imagine we wanted to write a web view that returns an HTTP response that
streams rows of a CSV file we create on the fly. We want to use the CSV writer
that is provided by the standard library.

**Bad**

```python
import csv
from io import StringIO


class StreamingHttpResponse:
     """A streaming HTTP response"""
     ...  # implementation code goes here


def some_view(request):
     rows = (
          ['First row', 'Foo', 'Bar', 'Baz'],
          ['Second row', 'A', 'B', 'C', '"Testing"', "Here's a quote"]
     )

     # Define a generator to stream data directly to the client
     def stream():
          buffer_ = StringIO()
          writer = csv.writer(buffer_, delimiter=';', quotechar='"')
          for row in rows:
               writer.writerow(row)
               buffer_.seek(0)
               data = buffer_.read()
               buffer_.seek(0)
               buffer_.truncate()
               yield data

     # Create the streaming response  object with the appropriate CSV header.
     response = StreamingHttpResponse(stream(), content_type='text/csv')
     response[
          'Content-Disposition'] = 'attachment; filename="somefilename.csv"'

     return response

```

Our first implementation works around the CSV's writer interface by
manipulating a `StringIO` object (which is file-like) and performing several
low level operations in order to farm out the rows from the writer. It's a lot
of work and not very elegant.

A better way is to leverage the fact that the writer just needs an object with
a `.write()` method to do our bidding. Why not pass it a dummy object that
immediately returns the newly assembled row, so that
the `StreamingHttpResponse`
class can immediate stream it back to the client?

**Good**

```python
import csv


class Echo:
     """An object that implements just the write method of the file-like
     interface.
     """

     def write(self, value):
          """Write the value by returning it, instead of storing in a buffer."""
          return value


def some_streaming_csv_view(request):
     """A view that streams a large CSV file."""
     rows = (
          ['First row', 'Foo', 'Bar', 'Baz'],
          ['Second row', 'A', 'B', 'C', '"Testing"', "Here's a quote"]
     )
     writer = csv.writer(Echo(), delimiter=';', quotechar='"')
     return StreamingHttpResponse(
          (writer.writerow(row) for row in rows),
          content_type="text/csv",
          headers={
               'Content-Disposition': 'attachment; filename="somefilename.csv"'},
     )

```

Much better, and it works like a charm! The reason it's superior to the
previous implementation should be obvious: less code (and more performant) to
achieve the same result. We decided to leverage the fact that the writer class
depends on the `.write()` abstraction of the object it receives, without caring
about the low level, concrete details of what the method actually does.

This example was taken from
[a submission made to the Django documentation](https://code.djangoproject.com/ticket/21179)
by this author.

**[⬆ back to top](#table-of-contents)**

## **Don't repeat yourself (DRY)**

Try to observe the [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)
principle.

Do your absolute best to avoid duplicate code. Duplicate code is bad because it
means that there's more than one place to alter something if you need to change
some logic.

Imagine if you run a restaurant and you keep track of your inventory: all your
tomatoes, onions, garlic, spices, etc. If you have multiple lists that you keep
this on, then all have to be updated when you serve a dish with tomatoes in
them. If you only have one list, there's only one place to update!

Often you have duplicate code because you have two or more slightly different
things, that share a lot in common, but their differences force you to have two
or more separate functions that do much of the same things. Removing duplicate
code means creating an abstraction that can handle this set of different things
with just one function/module/class.

Getting the abstraction right is critical. Bad abstractions can be worse than
duplicate code, so be careful! Having said this, if you can make a good
abstraction, do it! Don't repeat yourself, otherwise you'll find yourself
updating multiple places any time you want to change one thing.

**Bad:**

```python
from typing import List, Dict
from dataclasses import dataclass


@dataclass
class Developer:
    def __init__(self, experience: float, github_link: str) -> None:
        self._experience = experience
        self._github_link = github_link

    @property
    def experience(self) -> float:
        return self._experience

    @property
    def github_link(self) -> str:
        return self._github_link


@dataclass
class Manager:
    def __init__(self, experience: float, github_link: str) -> None:
        self._experience = experience
        self._github_link = github_link

    @property
    def experience(self) -> float:
        return self._experience

    @property
    def github_link(self) -> str:
        return self._github_link


def get_developer_list(developers: List[Developer]) -> List[Dict]:
    developers_list = []
    for developer in developers:
        developers_list.append({
            'experience': developer.experience,
            'github_link': developer.github_link
        })
    return developers_list


def get_manager_list(managers: List[Manager]) -> List[Dict]:
    managers_list = []
    for manager in managers:
        managers_list.append({
            'experience': manager.experience,
            'github_link': manager.github_link
        })
    return managers_list


## create list objects of developers
company_developers = [
    Developer(experience=2.5, github_link='https://github.com/1'),
    Developer(experience=1.5, github_link='https://github.com/2')
]
company_developers_list = get_developer_list(developers=company_developers)

## create list objects of managers
company_managers = [
    Manager(experience=4.5, github_link='https://github.com/3'),
    Manager(experience=5.7, github_link='https://github.com/4')
]
company_managers_list = get_manager_list(managers=company_managers)
```

**Good:**

```python
from typing import List, Dict
from dataclasses import dataclass


@dataclass
class Employee:
    def __init__(self, experience: float, github_link: str) -> None:
        self._experience = experience
        self._github_link = github_link

    @property
    def experience(self) -> float:
        return self._experience

    @property
    def github_link(self) -> str:
        return self._github_link


def get_employee_list(employees: List[Employee]) -> List[Dict]:
    employees_list = []
    for employee in employees:
        employees_list.append({
            'experience': employee.experience,
            'github_link': employee.github_link
        })
    return employees_list


## create list objects of developers
company_developers = [
    Employee(experience=2.5, github_link='https://github.com/1'),
    Employee(experience=1.5, github_link='https://github.com/2')
]
company_developers_list = get_employee_list(employees=company_developers)

## create list objects of managers
company_managers = [
    Employee(experience=4.5, github_link='https://github.com/3'),
    Employee(experience=5.7, github_link='https://github.com/4')
]
company_managers_list = get_employee_list(employees=company_managers)
```

**[⬆ back to top](#table-of-contents)**

## **Translations**

This document is also available in other languages:

- 🇨🇳 **
  Chinese** [yinruiqing/clean-code-python](https://github.com/yinruiqing/clean-code-python)
- 🇵🇹 🇧🇷 **
  Portugese** [fredsonchaves07/clean-code-python](https://github.com/fredsonchaves07/clean-code-python)
- 🇮🇷 **
  Persian:** [SepehrRasouli/clean-code-python](https://github.com/SepehrRasouli/clean-code-python)

**[⬆ back to top](#table-of-contents)**
