# L2S-PATCHES-GI
This repo record the patches generated by L2S-E. The folders named as "Math_xx" and "Time_xx" are patches for the corresponding bugs. In the folder, the Java source file is named as its package name and file name, which can help up locate it in Defects4j. A patch was either commented with `/*-- PATCH P1 --*/` or `/*-- PATCH P2 --*/`, indicating it was generated by the first pattern (`P1`) or the second pattern (`P2`) of ACS. 


## L2S-E Correct Patches
L2S-E successfully fixes 11 bugs out of 133 buges.
Those patches below are semantically equivalent to the developer-provided patches. While the others are plausible but error patches.
1. [Math-3](https://github.com/wangbo15/L2S-PATCHES-GI/blob/master/math_3)
1. [Math-4](https://github.com/wangbo15/L2S-PATCHES-GI/tree/master/math_4)
1. [Math-5](https://github.com/wangbo15/L2S-PATCHES-GI/tree/master/math_5)
1. [Math-25](https://github.com/wangbo15/L2S-PATCHES-GI/tree/master/math_25)
1. [Math-32](https://github.com/wangbo15/L2S-PATCHES-GI/tree/master/math_32)
1. [Math-33](https://github.com/wangbo15/L2S-PATCHES-GI/tree/master/math_33)
1. [Math-35](https://github.com/wangbo15/L2S-PATCHES-GI/tree/master/math_35)
1. [Math-61](https://github.com/wangbo15/L2S-PATCHES-GI/tree/master/math_61)
1. [Math-63](https://github.com/wangbo15/L2S-PATCHES-GI/tree/master/math_63)
1. [Time-15](https://github.com/wangbo15/L2S-PATCHES-GI/tree/master/time_15)
1. [Time-19](https://github.com/wangbo15/L2S-PATCHES-GI/tree/master/time_19)

## Detailed Analysis about the Patches
Compared with [Nopol](https://github.com/SpoonLabs/nopol) and [ACS](https://github.com/Adobee/ACS), there are four uniquely patches genearted by L2S-E, they are `Math-32`, `Math-33`, `Math-63` and `Time-19`.

### Math_32
#### The Generated Patch
    + if ( tree.getCut() == null && ((Boolean)tree.getAttribute()) ) {
    - if (((Boolean)tree.getAttribute()) ) {
    
#### The Developer's Patch**
    + if (tree.getCut() == null && (Boolean) tree.getAttribute()) {
    - if (((Boolean)tree.getAttribute()) ) {
#### Analysis
The patch `tree.getCut() == null` is beyond **ACS**'s search space, and **Nopol** cannot get the angelic value of the special method `getCut()`. **L2S-E** can link the type `Tree` to its `getCut()` method, and rank the patch as a proceeding one.

### Math_33
#### The Generated Patch
    + if ( Precision.compareTo(entry,0d,epsilon) > 0 ) {
    - if (Precision.compareTo(entry, 0d, maxUlps) > 0) {
    
#### The Developer's Patch
    + if (Precision.compareTo(entry, 0d, epsilon) > 0) {
    - if (Precision.compareTo(entry, 0d, maxUlps) > 0) {

#### Analysis
**ACS**' can only make use of the original expression `Precision.compareTo(entry, 0d, maxUlps)`, but it is unable to make the patch. **Nopol** is also unable to handle this. Following the rulls of **L2S-E**, the models first predict `entry ` should be at the first position, then predict the expression `Precision.compareTo($,0d,$) > 0`, at last fill the second positon wish `epsilon`. Because the condtion `Precision.compareTo(entry, 0d, epsilon) < 0` has been occured, `epsilon` has higher probability than the others except `maxUlps`.

### Math_63
#### The Generated Patch
    + if(Double.isNaN(x)){return false;}        return (Double.isNaN(x) && Double.isNaN(y)) || x == y;
    - return (Double.isNaN(x) && Double.isNaN(y)) || x == y;
    
#### The Developer's Patch
    + return equals(x, y, 1);
    - return (Double.isNaN(x) && Double.isNaN(y)) || x == y;

#### Analysis
In the method `public static boolean equals(double x, double y, int maxUlps)`, which is called by developer, it will return false if either `x` or `y` is `NaN`. So the patch of **L2S-E** equals to the developer's patch.

**ACS** can not generate this type of method invocation. The method space of **Nopol** does not contain `isNaN`. 

### Time_19
#### The Generated Patch
    + } else if ( offsetLocal >= 0 ) {
    - } else if (offsetLocal > 0) {
    
#### The Developer's Patch
    + } else if (offsetLocal >= 0) {
    - } else if (offsetLocal > 0) {

#### Analysis
Both **ACS** and **Nopol** can handle this type of bug. The differences in timeout setting may consequently lead them not to fix this bug.
