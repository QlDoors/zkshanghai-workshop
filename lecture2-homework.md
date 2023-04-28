# 第2课 课后作业

## 第1题 转换为bit位 Num2Bits

### 1.1 实现1

```c
pragma circom 2.1.4;

include "circomlib/poseidon.circom";


template Num2Bits2(n) {
    signal input in;
    signal output b[n];
    var acc;
    for (var i = 0; i < n; i++) {
        b[i] <-- (in \ (2 ** i)) % 2;
        0 === b[i] * (b[i] - 1);
        acc += (2 ** i) * b[i];
    }
    in === acc;
}

template Main() {
    signal input in;
    signal output out[4];

    component n2b = Num2Bits2(4);
    n2b.in <== in;
    out <== n2b.b;
}

component main = Main();

/* INPUT = {
    "in": "13"
} */
```

输出：

```
OUTPUT: 
  out = [1, 0, 1, 1]
```

### 1.2 实现2

```c
pragma circom 2.1.4;

include "circomlib/poseidon.circom";

template Num2Bits(n) {
    signal input in;
    signal output out[n];
    var lc1 = 0;
    var e2 = 1;
    for (var i = 0; i < n; i++) {
        out[i] <-- (in >> i) & 1;
        out[i] * (out[i] - 1) === 0;
        lc1 += out[i] * e2;
        e2 = e2 + e2;
    }
    lc1 === in;
}

component main = Num2Bits(4);

/* INPUT = {
     "in": "5"
} */
```

输出：

```
OUTPUT:
  out = [1, 0, 1, 0]
```

## 第2题 判零 IsZero

```c
pragma circom 2.1.4;

include "circomlib/poseidon.circom";

template IsZero () {
    signal input in;
    signal output out;
    signal inv;
    inv <-- in != 0 ? 1 / in : 0;
    log("inv", inv);
    out <== -in * inv + 1;

    in * out === 0;
}

component main = IsZero();
```

### 2.1 输入为 0 时

```
/* INPUT = {
    "in": "0"
} */
```

结果

```
LOG: 
  inv 0
OUTPUT: 
  out = 1
```

### 2.2 输入为23时

```
/* INPUT = {
    "in": "0"
} */
```

结果

```
LOG: 
  inv 8564964602024064217400767465535455469431968678423665612751471203442707672198
OUTPUT: 
  out = 0
```

2.3 把代码第13行改为`    in * out === 0;`

输入：

```
/* INPUT = {
    "in": "0"
} */
```

结果：

```
LOG: 
  inv 0
FAIL: 
  Error: Assert Failed.
  Error in template IsZero_0 line: 13
```

## 第3题 相等 IsEqual

### 3.1 实现1，利用IsZero

```c
template IsEqual() {
    signal input in[2];
    signal output out;

    component isz = IsZero();

    in[1] - in[0] ==> isz.in;

    isz.out ==> out;
}

component main = IsEqual();
```

#### 3.1.1 输入不等时

输入：

```
/* INPUT = {
    "in": ["33", "32"]
} */
```

输出：

```
OUTPUT: 
  out = 0
```

#### 3.1.2 输入相等时

输入：

```
/* INPUT = {
    "in": ["32", "32"]
} */
```

输出：

```
OUTPUT: 
  out = 1
```

### 3.2 实现2，课堂上的方法

```c
pragma circom 2.1.4;

include "circomlib/poseidon.circom";
// include "https://github.com/0xPARC/circom-secp256k1/blob/master/circuits/bigint.circom";

template Num2Bits(n) {
    signal input in;
    signal output out[n];
    var lc1 = 0;
    var e2 = 1;
    for (var i = 0; i < n; i++) {
        out[i] <-- (in >> i) & 1;
        out[i] * (out[i] - 1) === 0;
        lc1 += out[i] * e2;
        e2 = e2 + e2;
    }
    log("lc1", lc1);
    log("in", in);
    lc1 === in;
}

template IsEqual (n) {
    signal input in[2];


    signal inv;
    in[0] + (2 ** n) - in[1] ==> inv;  // 这个数最大应该是 (2^(n) - 1) + 2^n - (2^(n-1)) = 1.5 * (2^n) - 1 < 2^(n + 1) - 1, 所以最大位数为 n + 1 

    var nBits = n + 1;
    component n2b = Num2Bits(nBits);
    n2b.in <== inv;
    signal output out0[nBits];
    n2b.out ==> out0;
    signal output out;
    n2b.out[n] ==> out;
}

component main = IsEqual(3);
```

#### 3.2.1 输入不等

输入：

```
/* INPUT = {
    "in": ["7", "4"]
} */
```

输出：

```
OUTPUT: 
  out0[0] = 1
  out0[1] = 1
  out0[2] = 0
  out0[3] = 1
  out = 1
```

#### 3.2.2 输入不等2

输入：

```
/* INPUT = {
    "in": ["4", "7"]
} */
```

输出：

```
OUTPUT: 
  out0[0] = 1
  out0[1] = 0
  out0[2] = 1
  out0[3] = 0
out = 0
```

这个方法似乎行不通，或者这里的n和需要对比的数之间有个约束我没搞对？

## 4. 选择器 Selector

```
pragma circom 2.1.4;

include "circomlib/poseidon.circom";
// include "https://github.com/0xPARC/circom-secp256k1/blob/master/circuits/bigint.circom";

template Num2Bits(n) {
    signal input in;
    signal output out[n];
    var lc1 = 0;
    var e2 = 1;
    for (var i = 0; i < n; i++) {
        out[i] <-- (in >> i) & 1;
        out[i] * (out[i] - 1) === 0;
        lc1 += out[i] * e2;
        e2 = e2 + e2;
    }
    log("lc1", lc1);
    log("in", in);
    lc1 === in;
}

// N is the number of bits the input  have.
template LessThan(n) {
    assert(n <= 252);
    signal input in[2];
    signal output out;
    
    component n2b = Num2Bits(n + 1);
    n2b.in <== in[0] + (1 << n) - in[1];
    out <== 1 - n2b.out[n];

    signal output out1[n+1];
    out1 <== n2b.out;
}

// 输出out应该等于in[index]。 如果 index 越界（不在 [0, nChoices) 中），out 应该是 0。
template Selector(choices) {
    signal input in[choices];
    signal input index;
    signal output out;
    
    assert(choices <= 15);
    assert(index < 15);
    component lessThan = LessThan(4);
    lessThan.in[0] <== index;
    lessThan.in[1] <== choices;

    signal inv;
    inv <-- lessThan.out == 1 ? in[index] : 0;
    out <== inv;
}

component main=Selector(8);

/* INPUT = {
    "in": ["1", "2", "3", "4", "5", "6", "7", "8"],
    "index": 9
} */
```

### 4.1 输入不越界

```
/* INPUT = {
    "in": ["1", "2", "3", "4", "5", "6", "7", "8"],
    "index": 0
} */
```

```
OUTPUT: 
	out = 1
```

### 4.2 输入越界

```
/* INPUT = {
    "in": ["1", "2", "3", "4", "5", "6", "7", "8"],
    "index": 9
} */
```

```
OUTPUT: 
	out = 0
```

## 5. 判负 IsNegative



## 6. 少于 LessThan

```c
pragma circom 2.1.4;

include "circomlib/poseidon.circom";
// include "https://github.com/0xPARC/circom-secp256k1/blob/master/circuits/bigint.circom";

template Num2Bits(n) {
    signal input in;
    signal output out[n];
    var lc1 = 0;
    var e2 = 1;
    for (var i = 0; i < n; i++) {
        out[i] <-- (in >> i) & 1;
        out[i] * (out[i] - 1) === 0;
        lc1 += out[i] * e2;
        e2 = e2 + e2;
    }
    log("lc1", lc1);
    log("in", in);
    lc1 === in;
}

// N is the number of bits the input  have.
template LessThan(n) {
    assert(n <= 252);
    signal input in[2];
    signal output out;
    component n2b = Num2Bits(n + 1);
    n2b.in <== in[0] + (1 << n) - in[1];
    out <== 1 - n2b.out[n];

    signal output out1[n+1];
    out1 <== n2b.out;
}

component main = LessThan(3);
```

### 6.1 输入小于

```
/* INPUT = {
    "in": ["4", "7"]
} */
```

```
OUTPUT: 
  out = 1
  out1[0] = 1
  out1[1] = 0
  out1[2] = 1
  out1[3] = 0
```

### 6.2 输入大于

```
/* INPUT = {
    "in": ["7", "4"]
} */
```

```
OUTPUT: 
  out = 0
  out1[0] = 1
  out1[1] = 1
  out1[2] = 0
  out1[3] = 1
```

## 7. 整数除法 IntegerDivide