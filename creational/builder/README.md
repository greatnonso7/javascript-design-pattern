# Builder Pattern(建造者模式)

> 使你能够分步骤创建复杂对象。该模式允许你使用相同的创建代码生成不同类型和形式的对象

- https://refactoringguru.cn/design-patterns/creational-patterns

## Definition

> Separate the construction of a complex object from its representation so that the same construction process can create different representations.

## Example

### Sample-1

```js
export class ProductBuilder {
  constructor() {
    this.name = 'DEFAULT';
    this.price = 0;
    this.category = 'OTHER';
  }

  withName(name) {
    this.name = name;
    return this;
  }

  withPrice(price) {
    this.price = price;
    return this;
  }

  withCategory(category) {
    this.category = category;
    return this;
  }

  build() {
    return {
      name: this.name,
      price: this.price,
      category: this.category,
    };
  }
}

console.log(
  new ProductBuilder() //    {
    .withName('Harry Potter') //      name: 'Harry Potter',
    .withCategory('book') //      price: 9.99,
    .build() //      category: 'book'
);
```

### Sample-2

```js
export class ProductBuilder {
  constructor() {
    this.name = 'DEFAULT';
    this.price = 0;
    this.category = 'OTHER';

    // Generate "wither" methods for each property
    Object.keys(this).forEach((key) => {
      const witherName = `with${key
        .substring(0, 1)
        .toUpperCase()}${key.substring(1)}`;
      this[witherName] = (value) => {
        this[key] = value;
        return this;
      };
    });
  }

  build() {
    // Get an array of all non-function properties of this builder
    const keysNoWithers = Object.keys(this).filter(
      (key) => typeof this[key] !== 'function'
    );

    // Transform the array of keys into an object
    return keysNoWithers.reduce((returnValue, key) => {
      return {
        ...returnValue,
        [key]: this[key],
      };
    }, {});
  }
}

console.log(
  // =>
  new ProductBuilder() //    {
    .withName('Harry Potter') //      name: 'Harry Potter',
    .withCategory('book') //      price: 9.99,
    .build() //      category: 'book'
);
```

### Sample-3

```js
class BaseBuilder {
  init() {
    Object.keys(this).forEach((key) => {
      const witherName = `with${key
        .substring(0, 1)
        .toUpperCase()}${key.substring(1)}`;
      this[witherName] = (value) => {
        this[key] = value;
        return this;
      };
    });
  }

  build() {
    const keysNoWithers = Object.keys(this).filter(
      (key) => typeof this[key] !== 'function'
    );

    return keysNoWithers.reduce((returnValue, key) => {
      return {
        ...returnValue,
        [key]: this[key],
      };
    }, {});
  }
}

export class ProductBuilder extends BaseBuilder {
  constructor() {
    super();
    this.name = 'DEFAULT';
    this.price = 0;
    this.category = 'OTHER';
    super.init();
  }
}

export class SandwichBuilder extends BaseBuilder {
  constructor() {
    super();
    this.meat = 'ham';
    this.cheese = 'swiss';
    super.init();
  }
}

console.log(
  new ProductBuilder().withName('Harry Potter').withCategory('book').build()
);

console.log(
  new SandwichBuilder().withMeat('Roast Beef').withCheese('Havarti').build()
);
```

### Sample-4

```js
export const buildProduct = (overrides = {}) => {
  const defaults = {
    name: 'DEFAULT',
    price: 0,
    category: 'OTHER',
  };

  return { ...defaults, ...overrides };
};

console.log(
  buildProduct({
    name: 'Harry Potter',
    category: 'book',
  })
);
```
