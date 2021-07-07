# 클린코드 : 자바스크립트 (한국어 번역)
> [원문 링크](https://github.com/ryanmcdermott/clean-code-javascript)<br>
> 한국어 번역 [@sbyeol3](https://github.com/sbyeol3)
## 목차

1. [도입부](#도입부)
2. [변수](#변수)
3. [함수](#함수)
4. [객체와 자료구조](#객체와-자료구조)
5. [Classes](#classes)
6. [SOLID](#solid)
7. [Testing](#testing)
8. [Concurrency](#concurrency)
9. [Error Handling](#error-handling)
10. [Formatting](#formatting)
11. [Comments](#comments)
12. [Translation](#translation)

## 도입부

![Humorous image of software quality estimation as a count of how many expletives
you shout when reading code](https://www.osnews.com/images/comics/wtfm.jpg)

소프트웨어 엔지니어링 원리에 대해 로버트 C. 마틴이 저술한 [클린코드](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)를 JavaScript에 적용시킨 것입니다.
이 글은 스타일 가이드가 아니라 JavaScript로 가독성 있고, 재사용 가능하며, 리팩토링 가능한 소프트웨어를 개발하는 것에 대한 가이드입니다.

이 글에 나와 있는 원칙을 모두 엄격하게 준수해야 하는 것은 아니며, 더 적은 수의 원칙들이 보편적으로 합의될 것입니다.
이 글은 지침서 그 이상은 아니지만 _클린 코드_ 저자들의 다년간의 집단적인 경험에 걸쳐 성문화된 것입니다.

우리의 소프트웨어 공학 기술은 이제 막 50년이 조금 넘었을 뿐이고, 우리는 여전히 많은 것들을 배우고 있습니다.
소프트웨어 아키텍처가 아키텍처 자제만큼 오래된 경우 아마도 더 까다로운 규칙을 따라야 할 수도 있습니다.
지금은 이런 지침들이 여러분과 여러분의 팀이 만드는 JavaScript 코드의 퀄리티를 평가하는 시금석이 될 것입니다.

**한 가지 더!** : 이 글을 읽는다고 바로 더 나은 소프트웨어 개발자가 되는 것은 아니며, 오랜 기간 팀으로 일을 했다고 해서 여러분이 실수하지 않는다는 것을 의미하지 않습니다.
젖은 점토가 점차 최종적인 형태로 변해가는 것과 같이 모든 코드 조각들은 초안으로 시작합니다.
마지막으로, 우리는 동료들과 함께 코드를 검토할 때 결함들을 제거할 수 있죠.
개선이 필요한 초안 코드에 대해 자책하지 마세요.
대신 코드를 두들겨 완성합시다!

## **변수**

### 의미 있고 발음 가능한 변수 이름을 사용하라

**나쁜 예:**

```javascript
const yyyymmdstr = moment().format("YYYY/MM/DD");
```

**좋은 예:**

```javascript
const currentDate = moment().format("YYYY/MM/DD");
```

**[⬆ 목차로 이동](#목차)**

### 같은 유형의 변수에 동일한 어휘를 사용하라

**나쁜 예:**

```javascript
getUserInfo();
getClientData();
getCustomerRecord();
```

**좋은 예:**

```javascript
getUser();
```

**[⬆ 목차로 이동](#목차)**

### 검색 가능한 이름을 사용하라

우리는 코드를 쓸 때보다 코드를 읽는 경우가 더 많습니다. 우리가 작성하는 코드를 읽고 검색하는 것이 중요한 이유입니다. 
여러분의 프로그램을 이해하는 데 의미가 있는 변수 이름으로 짓지 않는다면, 여러분의 코드를 읽을 사람들을 힘들게 하는 것이죠. 변수 이름을 지을 때는 검색 가능하게 지으세요.
[buddy.js](https://github.com/danielstjules/buddy.js)와 [ESLint](https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md) 같은 도구들이 이름 없는 상수를 식별하는 데 도움이 될 수 있습니다.

**나쁜 예:**

```javascript
// 86400000이 도대체 어떤 의미일까요? 😡
setTimeout(blastOff, 86400000);
```

**좋은 예:**

```javascript
// 상수는 대문자로 명명하세요.
const MILLISECONDS_PER_DAY = 60 * 60 * 24 * 1000; //86400000;

setTimeout(blastOff, MILLISECONDS_PER_DAY);
```

**[⬆ 목차로 이동](#목차)**

### 이유를 알려주는 변수를 사용하라

**나쁜 예:**

```javascript
const address = "One Infinite Loop, Cupertino 95014";
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
saveCityZipCode(
  address.match(cityZipCodeRegex)[1],
  address.match(cityZipCodeRegex)[2]
);
```

**좋은 예:**

```javascript
const address = "One Infinite Loop, Cupertino 95014";
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
const [_, city, zipCode] = address.match(cityZipCodeRegex) || [];
saveCityZipCode(city, zipCode);
```

**[⬆ 목차로 이동](#목차)**

### 머리로 매핑하지 마라

명시적인 것이 암묵적인 것보다 낫습니다.

**나쁜 예:**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach(l => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // 잠깐, 'l'이 뭐였더라?
  dispatch(l);
});
```

**좋은 예:**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach(location => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  dispatch(location);
});
```

**[⬆ 목차로 이동](#목차)**

### 불필요한 맥락을 추가하지 마라

여러분의 클래스나 객체가 무언가를 말해주고 있다면, 변수 이름에서 불필요하게 또 말해주지 않아도 됩니다.

**나쁜 예:**

```javascript
const Car = {
  carMake: "Honda",
  carModel: "Accord",
  carColor: "Blue"
};

function paintCar(car, color) {
  car.carColor = color;
}
```

**좋은 예:**

```javascript
const Car = {
  make: "Honda",
  model: "Accord",
  color: "Blue"
};

function paintCar(car, color) {
  car.color = color;
}
```

**[⬆ 목차로 이동](#목차)**

### 짧은 코드나 조건문 대신에 인자의 기본값을 사용하라

인자의 기본값을 주는 것이 보통은 코드 한 줄을 추가하는 것보다 더 명료합니다.
기본값을 설정하게 되면 함수는 `undefined` 값을 가지는 인자의 경우에만 기본값을 제공합니다.
다른 `''`, `""`, `false`, `null`, `0`, `NaN`와 같이 "falsy"한 값들에는 기본값이 주어지지 않습니다.

**니쁜 예:**

```javascript
function createMicrobrewery(name) {
  const breweryName = name || "Hipster Brew Co.";
  // ...
}
```

**좋은 예:**

```javascript
function createMicrobrewery(name = "Hipster Brew Co.") {
  // ...
}
```

**[⬆ 목차로 이동](#목차)**

## **함수**

### 함수의 인자는 2개 이하인 것이 이상적이다

함수 파라미터의 개수를 제한하는 것은 매우 중요합니다. 여러분의 함수를 테스트하는 것이 쉬워지기 때문이죠.
3개 이상의 파라미터를 가지는 것은 파라미터 조합 간의 폭발로 이어집니다. 조합이 많아질 수록 각각의 경우에 대해 테스트해야 할 것이 많아집니다.

1개 또는 2개의 인자를 갖는 것이 이상적이며, 3개는 가능한 피하는 것이 좋습니다.
3개보다 많은 경우는 인자를 합치는 것이 좋습니다.
일반적으로 인수를 3개 이상 갖는 함수라면 너무 많은 일을 하는 함수일 것입니다.
그렇지 않은 경우에는 대부분 상위 수준의 객체로 충분합니다.

자바스크립트를 사용하면 많은 클래스 없이도 바로 객체를 만들 수 있으므로, 많은 인수가 필요할 때는 객체를 사용할 수 있습니다.

함수가 기대하는 프로퍼티가 무엇인지 명시하기 위해 여러분은 ES2015/ES6의 비구조화 할당 문법을 사용할 수 있습니다. 이것은 몇 가지 장점이 있습니다:

1. 누군가가 함수를 봤을 때 그 함수에 어떤 속성이 사용되는지 바로 알 수 있습니다.
2. 명명된 파라미터를 시뮬레이션 하는 데 사용할 수 있습니다.
3. 비구조화는 또한 함수에 전달되는 인수 객체의 원시값들도 복제합니다. 이것은 사이드 이펙트를 방지하는 데 도움이 됩니다. (참고 📝 인수 객체에서 비구조화된 객체나 배열들은 **복제되지 않습니다**)
4. Linter는 여러분이 사용하지 않는 프로퍼티에 대해 경고해줄 수 있습니다. 비구조화한 것이 아니라면 불가능하죠.

**나쁜 예:**

```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}

createMenu("Foo", "Bar", "Baz", true);

```

**좋은 예:**

```javascript
function createMenu({ title, body, buttonText, cancellable }) {
  // ...
}

createMenu({
  title: "Foo",
  body: "Bar",
  buttonText: "Baz",
  cancellable: true
});
```

**[⬆ 목차로 이동](#목차)**

### 함수는 한 가지 일을 해야 한다

이 룰은 소프트웨어 공학에서 가장 중요합니다.
함수가 한 가지 일보다 더 많은 일을 할 때, 힘수를 구성하고 테스트하고 추론하는 것이 더 어려워집니다.
여러분이 함수가 한 가지 일을 하도록 분리시킬 때 그 함수는 리팩토링 하기가 쉬워지고 여러분의 코드도 더 명료해질 것입니다.
다른 것들보다 이 지침만을 잘 따른다면 여러분은 많은 개발자를 앞설 수 있습니다.

**나쁜 예:**

```javascript
function emailClients(clients) {
  clients.forEach(client => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**좋은 예:**

```javascript
function emailActiveClients(clients) {
  clients.filter(isActiveClient).forEach(email);
}

function isActiveClient(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```

**[⬆ 목차로 이동](#목차)**

### 함수의 이름이 하는 일을 말하도록 하라

**나쁜 예:**

```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date();

// 함수 이름만 보면 무엇이 더해지는 지 알기 어렵군요 🥲
addToDate(date, 1);
```

**좋은 예:**

```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date();
addMonthToDate(1, date);
```

**[⬆ 목차로 이동](#목차)**

### 함수는 추상화의 하나의 레벨이어야 한다

여러분의 함수가 둘 이상의 추상화를 가질 때, 그 함수는 너무 많은 일을 하고 있는 것입니다.
함수를 쪼개는 것은 함수의 재사용성을 높여주고 테스트를 용이하게 합니다.

**나쁜 예:**

```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach(token => {
    // lex...
  });

  ast.forEach(node => {
    // parse...
  });
}
```

**좋은 예:**

```javascript
function parseBetterJSAlternative(code) {
  const tokens = tokenize(code);
  const syntaxTree = parse(tokens);
  syntaxTree.forEach(node => {
    // parse...
  });
}

function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      tokens.push(/* ... */);
    });
  });

  return tokens;
}

function parse(tokens) {
  const syntaxTree = [];
  tokens.forEach(token => {
    syntaxTree.push(/* ... */);
  });

  return syntaxTree;
}
```

**[⬆ 목차로 이동](#목차)**

### 중복되는 코드를 제거하라

중복되는 코드를 작성하지 않도록 최선을 다해야 합니다. 로직을 변경해야 할 때 변경해야 하는 위치가 두 개 이상이 되므로 중복되는 코드는 나쁩니다.

여러분이 식당을 운영하고 있는데 토마토, 양파, 마늘, 향신료 등이 있는 재고를 추적해야 한다고 상상해보세요.
관리해야 하는 목록이 여러 개 있는 경우라면, 토마토가 들어간 요리를 손님에게 주었다면 여러 목록이 모두 업데이트 되어야 할 것입니다. 재고 목록이 한 개뿐이라면 그 목록 하나만 업데이트하면 되고요!

종종 여러분은 공통되는 부분이 많지만 두 개 이상의 약간은 다른 부분이 있기에 중복 코드를 갖고 있을 것입니다. 그 차이로 인해 거의 동일한 작업을 수행하는 두 개 이상의 개별적인 함수들이 필요하게 됩니다.
중복되는 코드를 제거하는 것은 하나의 함수/모듈/클래스 만으로도 이러한 서로 다른 것들을 처리할 수 있는 추상화를 만듦을 의미합니다.

추상화를 올바르게 하는 것은 매우 중요하기 때문에 여러분은 _클래스_ 장에서 제시되는 SOLID 원칙을 따라야 합니다.
나쁜 추상화는 중복 코드보다 더 좋지 않으니 주의하세요! 이렇게 말하기는 했지만, 여러분이 추상화를 잘 하신다면 그렇게 하세요! 반복할 수록 여러분은 변경하고 싶은 것이 생길 때마다 여러 곳을 업데이트해야 할 것입니다.

**나쁜 예:**

```javascript
function showDeveloperList(developers) {
  developers.forEach(developer => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();
    const data = {
      expectedSalary,
      experience,
      githubLink
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach(manager => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();
    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```

**좋은 예:**

```javascript
function showEmployeeList(employees) {
  employees.forEach(employee => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    const data = {
      expectedSalary,
      experience
    };

    switch (employee.type) {
      case "manager":
        data.portfolio = employee.getMBAProjects();
        break;
      case "developer":
        data.githubLink = employee.getGithubLink();
        break;
    }

    render(data);
  });
}
```

**[⬆ 목차로 이동](#목차)**

### `Object.assign`으로 객체의 기본값을 설정하라

**나쁜 예:**

```javascript
const menuConfig = {
  title: null,
  body: "Bar",
  buttonText: null,
  cancellable: true
};

function createMenu(config) {
  config.title = config.title || "Foo";
  config.body = config.body || "Bar";
  config.buttonText = config.buttonText || "Baz";
  config.cancellable =
    config.cancellable !== undefined ? config.cancellable : true;
}

createMenu(menuConfig);
```

**좋은 예:**

```javascript
const menuConfig = {
  title: "Order",
  // User did not include 'body' key
  buttonText: "Send",
  cancellable: true
};

function createMenu(config) {
  let finalConfig = Object.assign(
    {
      title: "Foo",
      body: "Bar",
      buttonText: "Baz",
      cancellable: true
    },
    config
  );
  return finalConfig
  // config는 이제 다음과 같습니다 : {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig);
```

**[⬆ 목차로 이동](#목차)**

### 함수의 파라미터로 플래그 변수를 사용하지 마라

플래그 변수는 이 함수가 둘 이상의 일을 한다는 것을 여러분의 사용자에게 알려줍니다.
함수들은 한 가지 일을 해야 합니다.
boolean 값을 기반으로 다른 코드를 따라야 하는 경우 함수를 분할하세요.

**나쁜 예:**

```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**좋은 예:**

```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```

**[⬆ 목차로 이동](#목차)**

### 사이트 이펙트를 피하라 1

함수가 값을 받아 다른 값(들)을 리턴하는 경우 함수는 사이드 이펙트를 만들어 냅니다. 
사이드 이펙트는 파일에 쓰거나, 어떤 전역 변수를 수정하거나, 어쩌면 실수로 당신의 모든 돈을 낯선 이에게 송금해버리는 것이 될 수도 있죠.

이제 여러분은 가끔 프로그램에서 사이드 이펙트를 가질 필요가 있습니다.  이전 예시와 같이 여러분은 파일에 write 작업이 필요할 수도 있죠.
여러분이 하고 있는 것들을 중앙화 하는 것이 좋습니다.
특정 파일에 쓰는 함수나 클래스를 여러 개 가지지 마세요.
그것을 하는 단 하나를 가지세요.

핵심은 어떠한 구조 없이 객체들 간에 상태를 공유하거나, 무엇으로든 써질 수 있는 mutable한 데이터 타입을 사용하거나, 사이드 이펙트가 발생하는 곳을 중앙화 하지 않는 일반적인 함정을 피하는 것입니다.
이런 함정들을 피할 수 있다면 여러분은 대부분의 다른 프로그래머들보다 더 행복해질 겁니다.

**나쁜 예:**

```javascript
// 다음 함수에 의해 참조되는 전역 변수
// name을 사용하는 다른 함수가 있다면, 배열이 되어 깨져버릴지도 몰라요.
let name = "Ryan McDermott";

function splitIntoFirstAndLastName() {
  name = name.split(" ");
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

**좋은 예:**

```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(" ");
}

const name = "Ryan McDermott";
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```

**[⬆ 목차로 이동](#목차)**

### 사이드 이펙트를 피하라 2

자바스크립트에서 어떤 값들은 변경이 불가능(불변, immutable)하고 어떤 값들은 변경할 수 있습니다(가변, mutable).
객체나 배열은 변경 가능한 값들의 두 종류이기 때문에 이 값들은 함수에 파라미터로 전달할 때 조심히 다룰 필요가 있습니다.
자바스크립트 함수는 객체의 프로퍼티를 변경하거나 배열의 요소들을 바꿀 수 있기 때문에 다른 곳에서 쉽게 버그를 일으킬 수 있습니다

장바구니를 나타내는 배열 파라미터를 받아들이는 함수가 있다고 가정해보세요. 만약 함수가 구매할 아이템을 추가하는 것과 같이 장바구니 배열에 변경사항을 만든다면 동일한 `장바구니` 배열을 사용하는 다른 함수들도 아이템 추가에 의해 영향을 받게 될 겁니다.
좋아요, 하지만 안 좋을 수도 있어요. 좋지 않은 상황을 상상해봅시다.

사용자가 "구매" 버튼을 클릭하면 네트워크 요청을 생성하여 서버에 `cart` 배열을 보내는 `purchase` 함수를 호출됩니다.
네트워크 연결 불량으로 `purchase` 함수는 네트워크 요청을 계속 재시도 합니다.
그 사이 네트워크 요청이 시작되기 전에 사용자가 실제로 원하지 않는 항목의 "장바구니에 추가" 버튼을 실수로 클릭한 경우에는 어떻게 해야 할까요? 네트워크 요청이 시작되었다면, `purchase` 함수는 `cart` 배열이 변경되었기 때문에 실수로 추가된 아이템을 전송할 겁니다.

가장 좋은 해결책은 `addItemToCart` 함수가 언제나 `cart`를 복제하여 수정한 후 복제본을 반환하는 것입니다.
이 방법은 여전히 이전 장바구니 값을 사용하기 때문에 변경 사항의 영향을 받지 않음을 보장합니다.

이 접근 방식에 대한 두 가지 주의사항이 있습니다.

1. 인풋 객체를 실제로 수정하고 싶을 때도 있지만 실무에서 프로그래밍을 하다보면 실제로는 그런 경우가 드물다는 것을 아실 겁니다. 대부분의 함수들은 사이드 이펙트가 없어 리팩토링이 가능합니다!

2. 거대한 객체를 복제하는 것은 퍼포먼스 측면에서 비용이 많이 들 수 있습니다. 다행히도 직접 복제할 때보다 빠르고 메모리를 덜 쓰는 [좋은 라이브러리들](https://facebook.github.io/immutable-js/)이 있기 때문에 실제로는 큰 이슈가 아닙니다.

**나쁜 예:**

```javascript
const addItemToCart = (cart, item) => {
  cart.push({ item, date: Date.now() });
};
```

**좋은 예:**

```javascript
const addItemToCart = (cart, item) => {
  return [...cart, { item, date: Date.now() }];
};
```

**[⬆ 목차로 이동](#목차)**

### 전역 함수에 Write 하지 마라

전역 값들을 오염시키는 것은 자바스크립트에서 매우 나쁜 방법입니다. 다른 라이브러리와 충돌할 수 있으며 당신의 API를 사용하는 사용자는 프로덕션에서 예외가 발생할 때까지는 아무것도 알 수 없습니다. 
한 가지 예시를 생각해볼까요. 
만약 여러분이 JavaScript의 네이티브 배열 메소드를 확장하여 두 배열의 차이를 보여주는 `diff` 메소드를 사용하고 싶다면 어떻게 해야 할까요? 여러분은 `Array.prototype`에 새로운 함수를 쓸 수 있을 겁니다. 
이렇게 하면 같은 작업을 하려는 다른 라이브러리와 충돌할 수도 있죠. 다른 라이브러리가 배열의 첫 요소와 마지막 요소의 차이를 찾고자 `diff`를 사용했다면 어떻게 될까요?
이 예시는 ES2015/ES6 클래스를 사용하여 `Array`를 상속받는 것이 훨씬 더 나은 이유를 보여줍니다.

**나쁜 예:**

```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter(elem => !hash.has(elem));
};
```

**좋은 예:**

```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter(elem => !hash.has(elem));
  }
}
```

**[⬆ 목차로 이동](#목차)**

### 명령형 프로그래밍보다 함수형 프로그래밍을 선호하라

자바스크립트는 Haskell처럼 함수형 언어는 아니지만, 함수형 언어와 같은 면이 있습니다.
함수형 언어들은 더 명료하고 테스트하기 쉽습니다.
가능할 때 여러분은 함수형 프로그래밍 스타일을 선호하세요.

**나쁜 예:**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**좋은 예:**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

const totalOutput = programmerOutput.reduce(
  (totalLines, output) => totalLines + output.linesOfCode,
  0
);
```

**[⬆ 목차로 이동](#목차)**

### 조건문을 캡슐화하라

**나쁜 예:**

```javascript
if (fsm.state === "fetching" && isEmpty(listNode)) {
  // ...
}
```

**좋은 예:**

```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === "fetching" && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```

**[⬆ 목차로 이동](#목차)**

### 부정 조건문을 지양하라

**나쁜 예:**

```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**좋은 예:**

```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```

**[⬆ 목차로 이동](#목차)**

### 조건문을 피하라

이 원칙은 불가능한 일처럼 보일 것입니다. 이 말을 처음 듣자마자 대부분의 사람들은 "`if`문 없이 어떻게 뭘 하겠어요?"라고 말합니다.
그 답은 많은 경우에서 같은 일을 하기 위해 다형성을 사용할 수 있다는 것입니다.
두 번째 질문은 보통 "음 좋네요, 근데 왜 제가 그렇게 하고 싶겠어요?" 입니다.
이전에 우리가 배웠던 클린 코드 개념에 답이 있습니다. 함수는 오직 한 가지 일만 해야 한다는 것이죠.
여러분이 `if`문을 사용하는 클래스와 함수를 가질 때, 여러분은 그 함수가 두 가지 이상의 일을 한다고 말하는 것입니다.
오직 한 가지 일을 해야 한다는 것을 기억하세요.

**나쁜 예:**

```javascript
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case "777":
        return this.getMaxAltitude() - this.getPassengerCount();
      case "Air Force One":
        return this.getMaxAltitude();
      case "Cessna":
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}
```

**좋은 예:**

```javascript
class Airplane {
  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```

**[⬆ 목차로 이동](#목차)**

### 타입 체크를 피하라 1

자바스크립트의 함수는 어떤 타입의 인자든 받을 수 있습니다. 때때로 여러분은 이러한 타입으로부터의 자유에 사로잡혀 함수에서 타입 체크를 하고자 할 것입니다.
타입 체크를 피할 수 있는 많은 방법이 있습니다. 첫 번째는 일관된 API입니다.

**나쁜 예:**

```javascript
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(this.currentLocation, new Location("texas"));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location("texas"));
  }
}
```

**좋은 예:**

```javascript
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location("texas"));
}
```

**[⬆ 목차로 이동](#목차)**

### 타입 체크를 피하라 2

문자열이나 정수와 같은 원시값으로 작업을 하고 있다면 여러분은 다형성을 사용할 수는 없겠지만 여전히 타입 체크가 필요할 수 있습니다.
그렇다면 타입스크립트를 고려해보세요. 타입스크립트는 표준 자바스크립트 구문 위에 정적 타이핑 기능을 제공하기 때문에 자바스크립트의 좋은 대안이 됩니다.
일반 자바스크립트에서 "타입 안정성"을 보충하기 위해서는 장황한 말이 필요해지므로 가독성이 저하됩니다.
여러분의 자바스크립트 코드를 깨끗하게 유지하고, 좋은 테스트를 작성하고, 좋은 코드 리뷰를 하세요.
그렇지 않으면 타입스크립트를 사용하세요 (말했듯이 타입스크립트는 좋은 대안입니다!).

**나쁜 예:**

```javascript
function combine(val1, val2) {
  if (
    (typeof val1 === "number" && typeof val2 === "number") ||
    (typeof val1 === "string" && typeof val2 === "string")
  ) {
    return val1 + val2;
  }

  throw new Error("Must be of type String or Number");
}
```

**좋은 예:**

```javascript
function combine(val1, val2) {
  return val1 + val2;
}
```

**[⬆ 목차로 이동](#목차)**

### 지나치게 최적화하지 마라

최신 브라우저는 런타임에 많은 최적화를 수행합니다. 여러분이 작업 시간의 대부분을 최적화 하는 데 사용한다면 시간을 낭비하고 있는 겁니다.
최적화가 부족한 곳을 볼 수 있는 [좋은 리소스들](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)이 있습니다.
문제점들을 수정할 때까지 타겟으로 삼으세요.

**나쁜 예:**

```javascript
// 오래된 브라우저에서는 `list.length`이 캐싱되지 않아 다시 계산을 해야 하므로 비용이 많이 들 수 있습니다.
// 최신 브라우저에서는 최적화 됩니다.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**좋은 예:**

```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```

**[⬆ 목차로 이동](#목차)**

### 죽은 코드는 제거하라

죽은 코드는 중복된 코드처럼 좋지 않습니다. 여러분의 코드에 죽은 코드를 냅둘 이유가 없습니다.
더 이상 호출되지 않는다면 지워 버리세요! 만약 그 코드가 다시 필요하더라도 버전 기록에서는 안전하니까요.

**나쁜 예:**

```javascript
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**좋은 예:**

```javascript
function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**[⬆ 목차로 이동](#목차)**

## 객체와 자료구조

### getter와 setter를 사용하라

getter와 setter를 사용하여 객체의 데이터에 접근하는 것은 단순하게 객체에서 프로퍼티를 찾는 것보다 낫습니다.
"왜?"냐고 물으실 수도 있습니다. 글쎄요, 아래 이유들이 있습니다.

- 여러분이 객체 프로퍼티를 가져오는 것을 넘어 무언가를 하고 싶을 때 여러분은 코드베이스의 모든 액세스 권한을 찾아 변경할 필요는 없습니다.
- `set`을 수행할 때 검증 과정을 간단하게 추가할 수 있습니다.
- 내부 표현을 캡슐화합니다.
- 로깅과 에러 핸들링이 쉽습니다.
- 객체의 프로퍼티를 로드하는 것을 느리게 할 수 있습니다.

**나쁜 예:**

```javascript
function makeBankAccount() {
  // ...

  return {
    balance: 0
    // ...
  };
}

const account = makeBankAccount();
account.balance = 100;
```

**좋은 예:**

```javascript
function makeBankAccount() {
  // this one is private
  let balance = 0;

  // a "getter", made public via the returned object below
  function getBalance() {
    return balance;
  }

  // a "setter", made public via the returned object below
  function setBalance(amount) {
    // ... validate before updating the balance
    balance = amount;
  }

  return {
    // ...
    getBalance,
    setBalance
  };
}

const account = makeBankAccount();
account.setBalance(100);
```

**[⬆ 목차로 이동](#목차)**

### 객체가 프라이빗 멤버를 갖게 해라

이 원칙은 클로저를 통해 수행될 수 있습니다. (ES5 이하)

**나쁜 예:**

```javascript
const Employee = function(name) {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

const employee = new Employee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: undefined
```

**좋은 예:**

```javascript
function makeEmployee(name) {
  return {
    getName() {
      return name;
    }
  };
}

const employee = makeEmployee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
```

**[⬆ 목차로 이동](#목차)**

## **Classes**

### Prefer ES2015/ES6 classes over ES5 plain functions

It's very difficult to get readable class inheritance, construction, and method
definitions for classical ES5 classes. If you need inheritance (and be aware
that you might not), then prefer ES2015/ES6 classes. However, prefer small functions over
classes until you find yourself needing larger and more complex objects.

**Bad:**

```javascript
const Animal = function(age) {
  if (!(this instanceof Animal)) {
    throw new Error("Instantiate Animal with `new`");
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function(age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error("Instantiate Mammal with `new`");
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function(age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error("Instantiate Human with `new`");
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};
```

**Good:**

```javascript
class Animal {
  constructor(age) {
    this.age = age;
  }

  move() {
    /* ... */
  }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age);
    this.furColor = furColor;
  }

  liveBirth() {
    /* ... */
  }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor);
    this.languageSpoken = languageSpoken;
  }

  speak() {
    /* ... */
  }
}
```

**[⬆ back to top](#table-of-contents)**

### Use method chaining

This pattern is very useful in JavaScript and you see it in many libraries such
as jQuery and Lodash. It allows your code to be expressive, and less verbose.
For that reason, I say, use method chaining and take a look at how clean your code
will be. In your class functions, simply return `this` at the end of every function,
and you can chain further class methods onto it.

**Bad:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
  }

  setModel(model) {
    this.model = model;
  }

  setColor(color) {
    this.color = color;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car("Ford", "F-150", "red");
car.setColor("pink");
car.save();
```

**Good:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
    // NOTE: Returning this for chaining
    return this;
  }

  setModel(model) {
    this.model = model;
    // NOTE: Returning this for chaining
    return this;
  }

  setColor(color) {
    this.color = color;
    // NOTE: Returning this for chaining
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // NOTE: Returning this for chaining
    return this;
  }
}

const car = new Car("Ford", "F-150", "red").setColor("pink").save();
```

**[⬆ back to top](#table-of-contents)**

### Prefer composition over inheritance

As stated famously in [_Design Patterns_](https://en.wikipedia.org/wiki/Design_Patterns) by the Gang of Four,
you should prefer composition over inheritance where you can. There are lots of
good reasons to use inheritance and lots of good reasons to use composition.
The main point for this maxim is that if your mind instinctively goes for
inheritance, try to think if composition could model your problem better. In some
cases it can.

You might be wondering then, "when should I use inheritance?" It
depends on your problem at hand, but this is a decent list of when inheritance
makes more sense than composition:

1. Your inheritance represents an "is-a" relationship and not a "has-a"
   relationship (Human->Animal vs. User->UserDetails).
2. You can reuse code from the base classes (Humans can move like all animals).
3. You want to make global changes to derived classes by changing a base class.
   (Change the caloric expenditure of all animals when they move).

**Bad:**

```javascript
class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// Bad because Employees "have" tax data. EmployeeTaxData is not a type of Employee
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**Good:**

```javascript
class EmployeeTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  setTaxData(ssn, salary) {
    this.taxData = new EmployeeTaxData(ssn, salary);
  }
  // ...
}
```

**[⬆ back to top](#table-of-contents)**

## **SOLID**

### Single Responsibility Principle (SRP)

As stated in Clean Code, "There should never be more than one reason for a class
to change". It's tempting to jam-pack a class with a lot of functionality, like
when you can only take one suitcase on your flight. The issue with this is
that your class won't be conceptually cohesive and it will give it many reasons
to change. Minimizing the amount of times you need to change a class is important.
It's important because if too much functionality is in one class and you modify
a piece of it, it can be difficult to understand how that will affect other
dependent modules in your codebase.

**Bad:**

```javascript
class UserSettings {
  constructor(user) {
    this.user = user;
  }

  changeSettings(settings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**Good:**

```javascript
class UserAuth {
  constructor(user) {
    this.user = user;
  }

  verifyCredentials() {
    // ...
  }
}

class UserSettings {
  constructor(user) {
    this.user = user;
    this.auth = new UserAuth(user);
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```

**[⬆ back to top](#table-of-contents)**

### Open/Closed Principle (OCP)

As stated by Bertrand Meyer, "software entities (classes, modules, functions,
etc.) should be open for extension, but closed for modification." What does that
mean though? This principle basically states that you should allow users to
add new functionalities without changing existing code.

**Bad:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    if (this.adapter.name === "ajaxAdapter") {
      return makeAjaxCall(url).then(response => {
        // transform response and return
      });
    } else if (this.adapter.name === "nodeAdapter") {
      return makeHttpCall(url).then(response => {
        // transform response and return
      });
    }
  }
}

function makeAjaxCall(url) {
  // request and return promise
}

function makeHttpCall(url) {
  // request and return promise
}
```

**Good:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    return this.adapter.request(url).then(response => {
      // transform response and return
    });
  }
}
```

**[⬆ back to top](#table-of-contents)**

### Liskov Substitution Principle (LSP)

This is a scary term for a very simple concept. It's formally defined as "If S
is a subtype of T, then objects of type T may be replaced with objects of type S
(i.e., objects of type S may substitute objects of type T) without altering any
of the desirable properties of that program (correctness, task performed,
etc.)." That's an even scarier definition.

The best explanation for this is if you have a parent class and a child class,
then the base class and child class can be used interchangeably without getting
incorrect results. This might still be confusing, so let's take a look at the
classic Square-Rectangle example. Mathematically, a square is a rectangle, but
if you model it using the "is-a" relationship via inheritance, you quickly
get into trouble.

**Bad:**

```javascript
class Rectangle {
  constructor() {
    this.width = 0;
    this.height = 0;
  }

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width) {
    this.width = width;
    this.height = width;
  }

  setHeight(height) {
    this.width = height;
    this.height = height;
  }
}

function renderLargeRectangles(rectangles) {
  rectangles.forEach(rectangle => {
    rectangle.setWidth(4);
    rectangle.setHeight(5);
    const area = rectangle.getArea(); // BAD: Returns 25 for Square. Should be 20.
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**Good:**

```javascript
class Shape {
  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(length) {
    super();
    this.length = length;
  }

  getArea() {
    return this.length * this.length;
  }
}

function renderLargeShapes(shapes) {
  shapes.forEach(shape => {
    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)];
renderLargeShapes(shapes);
```

**[⬆ back to top](#table-of-contents)**

### Interface Segregation Principle (ISP)

JavaScript doesn't have interfaces so this principle doesn't apply as strictly
as others. However, it's important and relevant even with JavaScript's lack of
type system.

ISP states that "Clients should not be forced to depend upon interfaces that
they do not use." Interfaces are implicit contracts in JavaScript because of
duck typing.

A good example to look at that demonstrates this principle in JavaScript is for
classes that require large settings objects. Not requiring clients to setup
huge amounts of options is beneficial, because most of the time they won't need
all of the settings. Making them optional helps prevent having a
"fat interface".

**Bad:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.settings.animationModule.setup();
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  animationModule() {} // Most of the time, we won't need to animate when traversing.
  // ...
});
```

**Good:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.options = settings.options;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.setupOptions();
  }

  setupOptions() {
    if (this.options.animationModule) {
      // ...
    }
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  options: {
    animationModule() {}
  }
});
```

**[⬆ back to top](#table-of-contents)**

### Dependency Inversion Principle (DIP)

This principle states two essential things:

1. High-level modules should not depend on low-level modules. Both should
   depend on abstractions.
2. Abstractions should not depend upon details. Details should depend on
   abstractions.

This can be hard to understand at first, but if you've worked with AngularJS,
you've seen an implementation of this principle in the form of Dependency
Injection (DI). While they are not identical concepts, DIP keeps high-level
modules from knowing the details of its low-level modules and setting them up.
It can accomplish this through DI. A huge benefit of this is that it reduces
the coupling between modules. Coupling is a very bad development pattern because
it makes your code hard to refactor.

As stated previously, JavaScript doesn't have interfaces so the abstractions
that are depended upon are implicit contracts. That is to say, the methods
and properties that an object/class exposes to another object/class. In the
example below, the implicit contract is that any Request module for an
`InventoryTracker` will have a `requestItems` method.

**Bad:**

```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items;

    // BAD: We have created a dependency on a specific request implementation.
    // We should just have requestItems depend on a request method: `request`
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(["apples", "bananas"]);
inventoryTracker.requestItems();
```

**Good:**

```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ["WS"];
  }

  requestItem(item) {
    // ...
  }
}

// By constructing our dependencies externally and injecting them, we can easily
// substitute our request module for a fancy new one that uses WebSockets.
const inventoryTracker = new InventoryTracker(
  ["apples", "bananas"],
  new InventoryRequesterV2()
);
inventoryTracker.requestItems();
```

**[⬆ back to top](#table-of-contents)**

## **Testing**

Testing is more important than shipping. If you have no tests or an
inadequate amount, then every time you ship code you won't be sure that you
didn't break anything. Deciding on what constitutes an adequate amount is up
to your team, but having 100% coverage (all statements and branches) is how
you achieve very high confidence and developer peace of mind. This means that
in addition to having a great testing framework, you also need to use a
[good coverage tool](https://gotwarlost.github.io/istanbul/).

There's no excuse to not write tests. There are [plenty of good JS test frameworks](https://jstherightway.org/#testing-tools), so find one that your team prefers.
When you find one that works for your team, then aim to always write tests
for every new feature/module you introduce. If your preferred method is
Test Driven Development (TDD), that is great, but the main point is to just
make sure you are reaching your coverage goals before launching any feature,
or refactoring an existing one.

### Single concept per test

**Bad:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles date boundaries", () => {
    let date;

    date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);

    date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);

    date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**Good:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles 30-day months", () => {
    const date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);
  });

  it("handles leap year", () => {
    const date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);
  });

  it("handles non-leap year", () => {
    const date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**[⬆ back to top](#table-of-contents)**

## **Concurrency**

### Use Promises, not callbacks

Callbacks aren't clean, and they cause excessive amounts of nesting. With ES2015/ES6,
Promises are a built-in global type. Use them!

**Bad:**

```javascript
import { get } from "request";
import { writeFile } from "fs";

get(
  "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
  (requestErr, response, body) => {
    if (requestErr) {
      console.error(requestErr);
    } else {
      writeFile("article.html", body, writeErr => {
        if (writeErr) {
          console.error(writeErr);
        } else {
          console.log("File written");
        }
      });
    }
  }
);
```

**Good:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**[⬆ back to top](#table-of-contents)**

### Async/Await are even cleaner than Promises

Promises are a very clean alternative to callbacks, but ES2017/ES8 brings async and await
which offer an even cleaner solution. All you need is a function that is prefixed
in an `async` keyword, and then you can write your logic imperatively without
a `then` chain of functions. Use this if you can take advantage of ES2017/ES8 features
today!

**Bad:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**Good:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

async function getCleanCodeArticle() {
  try {
    const body = await get(
      "https://en.wikipedia.org/wiki/Robert_Cecil_Martin"
    );
    await writeFile("article.html", body);
    console.log("File written");
  } catch (err) {
    console.error(err);
  }
}

getCleanCodeArticle()
```

**[⬆ back to top](#table-of-contents)**

## **Error Handling**

Thrown errors are a good thing! They mean the runtime has successfully
identified when something in your program has gone wrong and it's letting
you know by stopping function execution on the current stack, killing the
process (in Node), and notifying you in the console with a stack trace.

### Don't ignore caught errors

Doing nothing with a caught error doesn't give you the ability to ever fix
or react to said error. Logging the error to the console (`console.log`)
isn't much better as often times it can get lost in a sea of things printed
to the console. If you wrap any bit of code in a `try/catch` it means you
think an error may occur there and therefore you should have a plan,
or create a code path, for when it occurs.

**Bad:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**Good:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
}
```

### Don't ignore rejected promises

For the same reason you shouldn't ignore caught errors
from `try/catch`.

**Bad:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    console.log(error);
  });
```

**Good:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    // One option (more noisy than console.log):
    console.error(error);
    // Another option:
    notifyUserOfError(error);
    // Another option:
    reportErrorToService(error);
    // OR do all three!
  });
```

**[⬆ back to top](#table-of-contents)**

## **Formatting**

Formatting is subjective. Like many rules herein, there is no hard and fast
rule that you must follow. The main point is DO NOT ARGUE over formatting.
There are [tons of tools](https://standardjs.com/rules.html) to automate this.
Use one! It's a waste of time and money for engineers to argue over formatting.

For things that don't fall under the purview of automatic formatting
(indentation, tabs vs. spaces, double vs. single quotes, etc.) look here
for some guidance.

### Use consistent capitalization

JavaScript is untyped, so capitalization tells you a lot about your variables,
functions, etc. These rules are subjective, so your team can choose whatever
they want. The point is, no matter what you all choose, just be consistent.

**Bad:**

```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const Artists = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**Good:**

```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const SONGS = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const ARTISTS = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```

**[⬆ back to top](#table-of-contents)**

### Function callers and callees should be close

If a function calls another, keep those functions vertically close in the source
file. Ideally, keep the caller right above the callee. We tend to read code from
top-to-bottom, like a newspaper. Because of this, make your code read that way.

**Bad:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**Good:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**[⬆ back to top](#table-of-contents)**

## **Comments**

### Only comment things that have business logic complexity.

Comments are an apology, not a requirement. Good code _mostly_ documents itself.

**Bad:**

```javascript
function hashIt(data) {
  // The hash
  let hash = 0;

  // Length of string
  const length = data.length;

  // Loop through every character in data
  for (let i = 0; i < length; i++) {
    // Get character code.
    const char = data.charCodeAt(i);
    // Make the hash
    hash = (hash << 5) - hash + char;
    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**Good:**

```javascript
function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = (hash << 5) - hash + char;

    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**[⬆ back to top](#table-of-contents)**

### Don't leave commented out code in your codebase

Version control exists for a reason. Leave old code in your history.

**Bad:**

```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Good:**

```javascript
doStuff();
```

**[⬆ back to top](#table-of-contents)**

### Don't have journal comments

Remember, use version control! There's no need for dead code, commented code,
and especially journal comments. Use `git log` to get history!

**Bad:**

```javascript
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
function combine(a, b) {
  return a + b;
}
```

**Good:**

```javascript
function combine(a, b) {
  return a + b;
}
```

**[⬆ back to top](#table-of-contents)**

### Avoid positional markers

They usually just add noise. Let the functions and variable names along with the
proper indentation and formatting give the visual structure to your code.

**Bad:**

```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: "foo",
  nav: "bar"
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function() {
  // ...
};
```

**Good:**

```javascript
$scope.model = {
  menu: "foo",
  nav: "bar"
};

const actions = function() {
  // ...
};
```

**[⬆ back to top](#table-of-contents)**

## Translation

This is also available in other languages:

- ![am](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Armenia.png) **Armenian**: [hanumanum/clean-code-javascript/](https://github.com/hanumanum/clean-code-javascript)
- ![bd](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Bangladesh.png) **Bangla(বাংলা)**: [InsomniacSabbir/clean-code-javascript/](https://github.com/InsomniacSabbir/clean-code-javascript/)
- ![br](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Brazil.png) **Brazilian Portuguese**: [fesnt/clean-code-javascript](https://github.com/fesnt/clean-code-javascript)
- ![cn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Simplified Chinese**:
  - [alivebao/clean-code-js](https://github.com/alivebao/clean-code-js)
  - [beginor/clean-code-javascript](https://github.com/beginor/clean-code-javascript)
- ![tw](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Taiwan.png) **Traditional Chinese**: [AllJointTW/clean-code-javascript](https://github.com/AllJointTW/clean-code-javascript)
- ![fr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/France.png) **French**: [GavBaros/clean-code-javascript-fr](https://github.com/GavBaros/clean-code-javascript-fr)
- ![de](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Germany.png) **German**: [marcbruederlin/clean-code-javascript](https://github.com/marcbruederlin/clean-code-javascript)
- ![id](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Indonesia.png) **Indonesia**: [andirkh/clean-code-javascript/](https://github.com/andirkh/clean-code-javascript/)
- ![it](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Italy.png) **Italian**: [frappacchio/clean-code-javascript/](https://github.com/frappacchio/clean-code-javascript/)
- ![ja](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Japan.png) **Japanese**: [mitsuruog/clean-code-javascript/](https://github.com/mitsuruog/clean-code-javascript/)
- ![kr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/South-Korea.png) **Korean**: [qkraudghgh/clean-code-javascript-ko](https://github.com/qkraudghgh/clean-code-javascript-ko)
- ![pl](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Poland.png) **Polish**: [greg-dev/clean-code-javascript-pl](https://github.com/greg-dev/clean-code-javascript-pl)
- ![ru](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Russia.png) **Russian**:
  - [BoryaMogila/clean-code-javascript-ru/](https://github.com/BoryaMogila/clean-code-javascript-ru/)
  - [maksugr/clean-code-javascript](https://github.com/maksugr/clean-code-javascript)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Spain.png) **Spanish**: [tureey/clean-code-javascript](https://github.com/tureey/clean-code-javascript)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Uruguay.png) **Spanish**: [andersontr15/clean-code-javascript](https://github.com/andersontr15/clean-code-javascript-es)
- ![rs](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Serbia.png) **Serbian**: [doskovicmilos/clean-code-javascript/](https://github.com/doskovicmilos/clean-code-javascript)
- ![tr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Turkey.png) **Turkish**: [bsonmez/clean-code-javascript](https://github.com/bsonmez/clean-code-javascript/tree/turkish-translation)
- ![ua](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Ukraine.png) **Ukrainian**: [mindfr1k/clean-code-javascript-ua](https://github.com/mindfr1k/clean-code-javascript-ua)
- ![vi](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Vietnam.png) **Vietnamese**: [hienvd/clean-code-javascript/](https://github.com/hienvd/clean-code-javascript/)

**[⬆ back to top](#table-of-contents)**
