## 编程

- 数字千分位处理，正则和非正则都要实现(千位加逗号)

  - `numObj.toLocaleString([locales [, options]])`

    ```js
    // 方法一
    var num = 234982347.73;
    console.log(num.toLocaleString());

    // 方法二
    var num = 234982347.73;
    num.toString().replace(/^\d+/, m => m.replace(/(\d{1,3})(?=(?:\d{3})+$)/g, '$1,'));
    ```

- 实现一个 flatten 方法。

  ```js
  function flatten(input) {
    if (Array.isArray(input)) {
      let output = [];
      function _flatten(input) {
        for (let i = 0, len = input.length; i < len; i++) {
          let value = input[i];
          if (Array.isArray(value)) {
            _flatten(value);
          } else {
            output.push(value);
          }
        }
      }
      _flatten(input);
      return output;
    } else {
      return input;
    }
  }
  ```

  ```js
  function flatten(input) {
    return Array.isArray(input) ? input.toString().split(',') : input;
  }
  ```

- 将一个 json 数据的所有 key 从下划线改为驼峰

  ```js
  const testData = {
    a_bbb: 123,
    a_g: [1, 2, 3, 4],
    a_d: {
      s: 2,
      s_d: 3
    },
    a_f: [
      1,
      2,
      3,
      {
        a_g: 5
      }
    ],
    a_d_s: 1
  };

  /**
   * 将一个json数据的所有key从下划线改为驼峰
   *
   * @param {object | array} value 待处理对象或数组
   * @returns {object | array} 处理后的对象或数组
   */
  function mapKeysToCamelCase(data) {
    /**
     * 如果是基本常量return
     */
    if (isBaseType(data)) {
      return data;
    }

    if (Array.isArray(data)) {
      return data.map(key => {
        return isBaseType(key) ? key : mapKeysToCamelCase(key);
      });
    }

    let obj = {};

    Object.keys(data).forEach(key => {
      const _key = strToCamelCase(key);
      obj[_key] = mapKeysToCamelCase(data[key]);
    });
    return obj;
  }

  const isBaseType = (function() {
    const baseTypes = ['Number', 'String', 'Boolean', 'Null', 'Undefined'].map(item => `[object ${item}]`);
    return val => {
      const tp = Object.prototype.toString.call(val);
      return baseTypes.includes(tp);
    };
  })();

  function strToCamelCase(key) {
    return ('' + key).replace(/(_.{1})/g, val => val.slice(1).toUpperCase());
  }

  console.log(mapKeysToCamelCase(testData));
  ```

- JS 中判断字符串中出现次数最多的字符及出现的次数

  ```js
  function maxN(str) {
    // const obj = (''+str).split('').reduce((accu, cur, index) => {
    // accu[cur] = (accu[cur] || 0) + 1;
    // return accu
    // },
    // {}
    // );

    let obj = {};
    ('' + str).replace(/\w{1}/g, letter => {
      obj[letter] = (obj[letter] || 0) + 1;
      return letter;
    });

    let letter = '',
      maxNum = 0;

    for (let _letter in obj) {
      if (obj[_letter] > maxNum) {
        maxNum = obj[_letter];
        letter = _letter;
      }
    }

    return { letter, maxNum };
  }

  const str = 'qweqrtyuiqqqwrtyudfgerqtywer';
  console.log(maxN(str));
  ```

- 请编写一个 JavaScript 函数 parseQueryString ，他的用途是把 URL 参数解析为一个对象

  ```js
  function parseQueryString(url) {
    let result = {};
    let arr = url.split('?');
    if (arr.length <= 1) {
      return result;
    } else {
      arr = arr[1].split('#');
      arr = arr[0].split('&');
      arr.forEach(item => {
        const [key, value] = item.split('=');
        result[key] = value;
      });
    }

    return result;
  }

  var url = 'http://witmax.cn/index.php?key0=0&key1=1&key2=2#location';

  console.log(parseQueryString(url));
  ```

- 在 IE6.0 下面是不支持 `position：fixed` 的，请写一个 JS 使用固定在页面的右下角。

  ```html
  <html lang="en">
    <head>
      <meta charset="UTF-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1.0" />
      <meta http-equiv="X-UA-Compatible" content="ie=edge" />
      <title>Document</title>
      <style>
        .tit {
          position: absolute;
          width: 100px;
          height: 100px;
          background: red;
          right: 0;
          bottom: 0;
        }
      </style>
    </head>
    <body>
      <div id="box" class="tit"></div>
      <!-- <script>
          window.onscroll = window.onresize = window.onload = function() {
            const box = document.getElementById('box')
            const scrollTop = document.documentElement.scrollTop || document.body.scrollTop
            box.style.left = document.documentElement.clientWidth - box.offsetWidth + 'px'
            box.style.top = document.documentElement.clientHeight + scrollTop - box.offsetHeight + 'px'
          }
        </script> -->
    </body>
  </html>
  ```

- 请实现，鼠标移到页面中的任意标签，显示出这个标签的基本矩形轮廓。

  ```js
  function mouseOverShowBorder(container) {
    const children = container.childNodes;
    for (let i = 0; i < children.length; i++) {
      const child = children[i];

      if (child.nodeType === 1) {
        child.onmouseover = function(evt) {
          if (evt && evt.stopPropagation) {
            evt.stopPropagation();
          } else {
            evt.cancelBubble = true; // IE 阻止事件冒泡
          }
          this.style.border = '1px solid #ccc';
        };

        child.onmouseout = function() {
          this.style.border = '';
        };

        mouseOverShowBorder(child);
      }
    }
  }

  mouseOverShowBorder(document.body);
  ```

  nodeType:

  | 常量                             | 值  | 描述                                                                              |
  | :------------------------------- | :-- | :-------------------------------------------------------------------------------- |
  | Node.ELEMENT_NODE                | 1   | 一个 元素 节点，例如 `<p>` 和 `<div>`。                                           |
  | Node.TEXT_NODE                   | 3   | Element 或者 Attr 中实际的文字                                                    |
  | Node.PROCESSING_INSTRUCTION_NODE | 7   | 一个用于 XML 文档的 ProcessingInstruction ，例如 `<?xml-stylesheet ... ?>` 声明。 |
  | Node.COMMENT_NODE                | 8   | 一个 Comment 节点。                                                               |
  |                                  |     |                                                                                   |
  | Node.DOCUMENT_NODE               | 9   | 一个 Document 节点。                                                              |
  | Node.DOCUMENT_TYPE_NODE          | 10  | 描述文档类型的 DocumentType 节点。例如 `<!DOCTYPE html>` 就是用于 HTML5 的。      |
  | Node.DOCUMENT_FRAGMENT_NODE      | 11  | 一个 DocumentFragment 节点                                                        |

- 排序算法

  - 冒泡排序

    ```js
    /**
     * 每次比较相邻两个值
     */
    function bubbleSort(arr) {
      if (arr === null || arr.length === 0) return;

      const length = arr.length;

      /**
       * 从前往后冒泡
       */
      // for(let i=0; i < length -1; i++) {
      //   for(let j=0; j < length - 1 - i; j++){
      //     if(arr[j]>arr[j+1]){
      //       swap(arr, j, j+1);
      //     }
      //   }
      // }

      /**
       * 从后往前冒泡
       */
      for (let i = 0, len = length - 1; i < len; i++) {
        for (let j = length - 1; j > i; j--) {
          if (arr[j] < arr[j - 1]) {
            swap(arr, j, j - 1);
          }
        }
      }

      function swap(arr, i, j) {
        // let temp = arr[i];
        // arr[i] = arr[j];
        // arr[j] = temp;
        [arr[i], arr[j]] = [arr[j], arr[i]];
      }

      return arr;
    }
    ```

  - 选择排序

    ```js
    function selectSort(arr) {
      if (arr === null || arr.length === 0) return;

      for (let i = 0, len = arr.length - 1; i < len; i++) {
        let minIndex = i;
        for (let j = i + 1, len = arr.length; j < len; j++) {
          if (arr[minIndex] > arr[j]) {
            minIndex = j;
          }
        }

        if (minIndex !== i) {
          swap(arr, i, minIndex);
        }
      }

      function swap(arr, i, j) {
        [arr[i], arr[j]] = [arr[j], arr[i]];
      }
      return arr;
    }
    ```

  - 插入排序

    ```js
    function insertSort(arr) {
      if (arr === null || arr.length === 0) return;

      for (let i = 1, len = arr.length; i < len; i++) {
        // 假设第一个数是正确的
        let j = i,
          target = arr[i]; //待插入的

        //后移
        while (j > 0 && target < arr[j - 1]) {
          arr[j] = arr[j - 1];
          j--;
        }

        //插入
        arr[j] = target;
      }

      return arr;
    }
    ```

- 数组去重

  ```js
  function unique(arr) {
    return [...new Set(arr)];
  }
  ```

  ```js
  function unique(arr) {
    return arr.filter((item, index) => arr.indexOf(item) === index);
  }
  ```

  ```js
  function unique(arr) {
    let obj = {};
    return arr.filter(item => {
      if (obj[item]) {
        return false;
      }

      return (obj[item] = true);
    });
  }
  ```

- 字符串去重

  ```js
  function unique(str) {
    return [...new Set(str)].join('');
  }
  ```

- 输入 `携程C2t0r1i8p2020校招`, 输出 `2018Ctrip`

  ```js
  function handleStr(str) {
    let nums = str.match(/\d/g).join('');
    let words = str.match(/[a-zA-Z]/g).join('');

    return uniqueStr(nums) + words;
  }

  function uniqueStr(str) {
    const arr = str.split('');
    return arr.filter((item, index) => arr.indexOf(item) === index).join('');
  }
  ```

- 对一维数组，根据 type 类型分组成二维数组

  ```js
  // var input = [null, 2, "test", undefined, {
  //        "type": "product",
  //        "content": "product1"
  //      }, {
  //        "type": "product",
  //        "content": "product2"
  //      }, {
  //        "type": "tag",
  //        "content": "tag1"
  //      }, {
  //        "type": "product",
  //        "content": "product3"
  //      }, {
  //        "type": "tag",
  //        "content": "tag2"
  //      }];

  // output = [{"type":"product","contents":["product1","product2","product3"]},{"type":"tag","contents":["tag1","tag2"]}]

  function groupList(arr) {
    if (!Array.isArray(arr) || arr.length === 0) {
      return [];
    }

    const validItems = getValidItems(arr);
    const result = {};

    validItems.forEach(item => {
      result[item.type] ? result[item.type].push(item) : (result[item.type] = [item]);
      // if(result.hasOwnProperty(item.type)) {
      //  result[item.type].push(item)
      // } else {
      //  result[item.type] = [];
      //  result[item.type].push(item)
      // }
    });

    return resultFormat(result);
  }

  function getValidItems(arr) {
    return arr.filter(item => isPureObject(item) && item.type && item.content);
  }

  function isPureObject(obj) {
    return Object.prototype.toString.call(obj).slice(8, -1) === 'Object';
  }

  function resultFormat(obj) {
    return Object.keys(obj).map(type => {
      return { type, contents: obj[type] };
    });
  }
  ```

- 计算目录 `/a/b/c/d/e.js` 和 `/a/b/f/g.js` 的相对目录

  ```js
  function calculateRoute(path1, path2) {
    let pathArr1 = path1.split('/'),
      pathArr2 = path2.split('/'),
      routeArr = [],
      fileArr = [],
      diff = false;

    for (let i = 1, len = pathArr1.length; i < len; i++) {
      if (pathArr1[i] !== pathArr2[i] || diff) {
        if (pathArr1[i]) {
          routeArr.push('..');
        }
        if (pathArr2[i]) {
          fileArr.push(pathArr2[i]);
        }
        diff = true;
      } else {
        diff = false;
      }
    }

    return `${routeArr.join('/')}/${fileArr.join('/')}`;
  }

  let path = caculateRoute('/a/b/c/d/e.js', '/a/b/f/g.js');
  ```

- 实现一个数组中删除一个子数组的函数，要求函数中不 return 返回新的数组。

  ```js
  function removeSubArray(main, sub) {
    for (let i = 0; i < main.length; i++) {
      if (sub.includes(main[i])) {
        main.splice(i, 1);
        i--;
      }
    }
  }
  ```

- 使用 promise 4 秒后打印’A’，然后经过 3 秒打印’B’，再经过 2 秒打印’C’，再经过一秒打印’D’。

  ```js
  let promise = new Promise(resolve => {
    setTimeout(() => {
      console.log('A');
      resolve();
    }, 4000);
  });

  promise
    .then(() => {
      return new Promise(resolve => {
        setTimeout(() => {
          console.log('B');
          resolve();
        }, 3000);
      });
    })
    .then(() => {
      return new Promise(resolve => {
        setTimeout(() => {
          console.log('C');
          resolve();
        }, 2000);
      });
    })
    .then(() => {
      return new Promise(resolve => {
        setTimeout(() => {
          console.log('D');
          resolve();
        }, 1000);
      });
    });
  ```

- 数组中的 Promise 顺序执行

```js
function sequenceTasks(arr) {
  return arr.reduce(function(prevPromise, promiseFn, index) {
    return prevPromise.then(function() {
      return promiseFn();
    });
  }, Promise.resolve());
}
```

```js
function sequenceTasks(arr) {
  let p = Promise.resolve();
  arr.forEach(promiseFn => {
    p = p.then(function() {
      return promiseFn();
    });
  });
  return p;
}
```

```js
function sequenceTasks(arr) {
  async function run() {
    for (let promiseFn of arr) {
      await promiseFn();
    }
  }

  return run();
}
```