# 策略模式

> 策略（Strategy）模式的定義：該模式定義了一系列算法，並將每個算法封裝起來，使它們可以相互替換，且算法的變化不會影響使用算法的客戶。策略模式屬於對象行為模式，它通過對算法進行封裝，把使用算法的責任和算法的實現分割開來，並委派給不同的對象對這些算法進行管理。此處引用了 gof 設計模式的描述。

策略模式的主要優點如下：

* 多重條件語句 **不易維護**，而使用策略模式可以避免使用多重條件語句。

## 項目實戰 

> 解決多重條件業務

多業務場景下的訂單跳轉一直是個很頭疼的問題，因為每條業務的訂單可能需要定製，跳轉的詳情也可能不太一樣，當業務線過多的時候，很容易陷入多重條件地獄，不斷去累加判斷條件

```js
const orderType = 1 // 1: 美妝，2：電器，3：家具
if (orderType === 1) {
  console.log('美妝訂單')  // 簡單是用打印方法來舉例，實際可以當做每個訂單條件跳轉、展示的操作都不一樣
} else if (orderType === 2) {
  console.log('電器訂單')
} else if (orderType === 3) {
  console.log('家具訂單')
} 
```

如上這種，雖然是看起來條件很多但是屬於單條件，我們可以使用策略模式來簡單改造，如下所示：

```js
const orderType = 1 // 1: 美妝，2：電器，3：家具

const orderFunction = {
  1() { console.log('美妝訂單') },
  2() { console.log('電器訂單') },
  3() { console.log('家具訂單') },
}

orderFunction(orderType)
```

```js
const orderType = 1 // 1: 美妝，2：電器，3：家具
const orderWay = 1 // 1：h5，2：app，3：web

if (orderType === 1) {
  if (orderWay === 1) {
    console.log('美妝訂單h5')
  } else if (orderWay === 2) {
    console.log('美妝訂單app')
  } else if (orderWay === 3) {
    console.log('美妝訂單web')
  }
} else if (orderType === 2) {
  if (orderWay === 1) {
    console.log('電器訂單h5')
  } else if (orderWay === 2) {
    console.log('電器訂單app')
  } else if (orderWay === 3) {
    console.log('電器訂單web')
  }
} else if (orderType === 3) {
  if (orderWay === 1) {
    console.log('家具訂單h5')
  } else if (orderWay === 2) {
    console.log('家具訂單app')
  } else if (orderWay === 3) {
    console.log('家具訂單web')
  }
}
```
雖然上述是多重嵌套條件，但拆分開來還是可以理解為訂單類型跟環境類型的組合，我們借助 es6 map 對象來進行改造

```js
const orderType = 1 // 1: 美妝，2：電器，3：家具
const orderWay = 2 // 1：h5，2：app，3：web

const strategy = () => { // 訂單類型+環境類型策略
  const map = new Map([
    [{
      orderType: 1,
      orderWay: 1
    }, () => {
      console.log('美妝訂單h5')
    }],
    [{
      orderType: 1,
      orderWay: 2
    }, () => {
      console.log('美妝訂單app')
    }],
    [{
      orderType: 1,
      orderWay: 3
    }, () => {
      console.log('美妝訂單web')
    }],
    [{
      orderType: 2,
      orderWay: 1
    }, () => {
      console.log('電器訂單h5')
    }],
    [{
      orderType: 2,
      orderWay: 2
    }, () => {
      console.log('電器訂單app')
    }],
    [{
      orderType: 2,
      orderWay: 3
    }, () => {
      console.log('電器訂單web')
    }],
    [{
      orderType: 3,
      orderWay: 1
    }, () => {
      console.log('家具訂單h5')
    }],
    [{
      orderType: 3,
      orderWay: 2
    }, () => {
      console.log('家具訂單app')
    }],
    [{
      orderType: 3,
      orderWay: 3
    }, () => {
      console.log('家具訂單web')
    }],
  ])
  return map
}

const run = (orderType, orderWay) => {
  let action = [...strategy()].filter(([key, value]) => (key.orderType === orderType && key.orderWay === orderWay))
  action.forEach(([key, value]) => value.call(this))
}

run(orderType, orderWay)
```

解決多重嵌套條件地獄

```js
const orderType = 1 // 1: 美妝，2：電器，3：家具
const orderWay = 1 // 1：h5，2：app，3：web
const orderMoney = 100 // 金額範圍劃分，0-100，100-1000，1000以上，跳轉的訂單詳情也不相同

if (orderType === 1) {
    if (orderWay === 1) {
        if (0 <= orderMoney && orderMoney < 100) {
            console.log('美妝訂單h5-0')
        } else if (orderMoney < 1000) {
            console.log('美妝訂單h5-100')
        } else {
            console.log('美妝訂單h5-1000')
        }
    } else if (orderWay === 2) {
        if (0 <= orderMoney && orderMoney < 100) {
            console.log('美妝訂單app-0')
        } else if (orderMoney < 1000) {
            console.log('美妝訂單app-100')
        } else {
            console.log('美妝訂單app-1000')
        }
    } else if (orderWay === 3) {
        if (0 <= orderMoney && orderMoney < 100) {
            console.log('美妝訂單web-0')
        } else if (orderMoney < 1000) {
            console.log('美妝訂單web-100')
        } else {
            console.log('美妝訂單web-1000')
        }
    }
} // 條件判斷次數太多，所以此處只用了一層條件
```

可以將相同的策略提取出來。首先環境跟訂單類型的組合可以默認已知，所以將**金額條件**提取出來，當作一組策略返回


```js
const orderType = 1 // 1: 美妝，2：電器，3：家具
const orderWay = 1 // 1：h5，2：app，3：web
const orderMoney = 10000 // 金額範圍劃分，0-100，100-1000，1000以上，跳轉的訂單詳情也不相同

const orderMoneyStrategy = (orderMoney) => { // 提取金額策略
    if (0 <= orderMoney && orderMoney < 100) {
        return 1
    } else if (orderMoney < 1000) {
        return 2
    }
    return 3
}

const strategy = () => { // 訂單類型+環境類型策略
    const map = new Map([
        [{
            orderType: 1,
            orderWay: 1,
            orderMoney: 1
        }, () => {
            console.log('美妝訂單h5-0')
        }],
        [{
            orderType: 1,
            orderWay: 1,
            orderMoney: 2
        }, () => {
            console.log('美妝訂單h5-100')
        }],
        [{
            orderType: 1,
            orderWay: 1,
            orderMoney: 3
        }, () => {
            console.log('美妝訂單h5-1000')
        }],
    ])
    return map
}

const run = (orderType, orderWay, orderMoney) => {
    let action = [...strategy()].filter(([key, value]) => (key.orderType === orderType && key.orderWay === orderWay && key.orderMoney === orderMoney))
    action.forEach(([key, value]) => value.call(this))
}

run(orderType, orderWay, orderMoneyStrategy(orderMoney))
```


[前端23種設計模式之策略模式](https://juejin.im/post/5ee6f53fe51d450d800a4459)