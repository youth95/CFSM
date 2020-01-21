# CFSM

基于条件的,可回溯的有限状态机.

## 快速开始

```typescript
import {create} from 'fsm';
const mac = create({
  states:["A","B","C","D"],
  condVars:["a","b"],
  inital:"A",
  termination:"D",
  transforms:[
    {
      from:"A",
      to:"B",
      cond:"a && b",
    },
    {
      from:"A",
      to:"C",
      cond:"!b",
    },
    {
      from:"C",
      to:"D",
      cond:"true",
    },
  ],
});
console.log(mac.state); // A
console.log(mac.isTermination); // false
console.log(mac.isInital); // true
console.log(mac.nexts); // ["B","C"]
console.log(mac.canNext({
  a:true,
  b:true,
}));    // {can:true,next:"B"}
console.log(mac.canNext({
  a:false,
  b:true,
}));    // {can:false}
console.log(mac.canNext({
  a:false,
  b:false,
}));    // {can:true,next:"C"}
console.log(mac.canNext({
  a:true,
  b:false,
}));    // {can:true,next:"C"}
console.log(mac.next({
  a:false,
  b:true,
})) // throw error
const mac2 = mac.next({
  a:true,
  b:true,
});// return new mac object
console.log(mac2.state); // B
const mac3 = mac2.next();
console.log(mac3.state); // C
console.log(mac3.isTermination);  // throw Error
mac3.next();  // throw Error
const mac4 = mac3.reset();
console.log(mac4.state);  // A

// 订阅状态到达B
mac4.subscribe("B",
(from:string,to:string,ctx:{[s:string]:bool}) => {
  console.log(from,to,ctx);
  }
);
// 订阅状态到达A
mac4.subscribe("A",
(from:string,to:string,ctx:{[s:string]:bool}) => {
  console.log(from,to,ctx);
  }
);

// 订阅状态从A到B
mac4.subscribe({
  from:"A",
  to:"B"
},
(from:string,to:string,ctx:{[s:string]:bool}) => {
  console.log(from,to,ctx);
  }
);

```

## 概念

* **初态**: 状态机的初始状态.
* **现态**: 状态机的当前状态.
* **次态**: 状态机的下一个状态.
* **转换**: 状态机从**现态**变化到**次态**的过程.
* **终态**: 状态机的终止状态.
* **转换条件**: 状态机由**现态**变化到**次态**的条件.
* **条件上下文**: 一组标识,用于定义条件表达式中的可用变量标识.

状态的转换是基于条件的,并且在运行时中,状态机的**次态**是确定的一个,特殊的**终态**下没有**次态**。

TODO 图实例

**条件上下文**的定义是一个字符串数组.

```typescript
const vars:string[] = ["a","b","c","d"];
```

同一个from下的cond 不可以在特定上下文中match多个branch, 可以在特定上下文中不match任何branch.


## TODO

* 项目搭建
  * [x] 文件: README.md
  * [ ] 文件: .gitignore
  * [ ] 文件: package.json
  * [ ] 文件: jest.config.js
  * [ ] 文件: tsconfig.js
  * [ ] 机制: commitlint
  * [ ] 机制: conventional-changelog
  * [ ] 机制: 版本号迭代机制
  * [ ] 机制: CI
  * [ ] 机制: 单元测试
* 特性
  * [ ] 支持条件上下文表述
  * [ ] 支持条件表达式计算
  * [ ] 支持转换规则配置
  * [ ] 支持规则冲突检测
