## 2.20

$$ AUC=\cfrac{1}{2}\sum\_{i=1}^{m-1}(x\_{i+1} - x\_i)\cdot(y\_i + y\_{i+1}) $$

[解析]：由于图2.4(b)中给出的ROC曲线为横平竖直的标准折线，所以乍一看这个式子的时候很不理解其中的$ \cfrac{1}{2} $和$ (y\_i + y\_{i+1}) $代表着什么，因为对于横平竖直的标准折线用$ AUC=\sum\_{i=1}^{m-1}(x\_{i+1} - x\_i) \cdot y\_i $就可以求出AUC了，但是图2.4(b)中的ROC曲线只是个特例罢了，因为此图是所有样例的预测值均不相同时的情形，也就是说每次分类阈值变化的时候只会划分新增**1个**样例为正例，所以下一个点的坐标为$ (x+\cfrac{1}{m^-},y) $或$ (x,y+\cfrac{1}{m^+}) $，然而当模型对某个正样例和某个反样例给出的预测值相同时，便会划分新增**两个**样例为正例，于是其中一个分类正确一个分类错误，那么下一个点的坐标为$ (x+\cfrac{1}{m^-},y+\cfrac{1}{m^+}) $（当没有预测值相同的样例时，若采取按固定梯度改变分类阈值，也会出现一下划分新增两个甚至多个正例的情形，但是此种阈值选取方案画出的ROC曲线AUC值更小，不建议使用），此时ROC曲线中便会出现斜线，而不再是只有横平竖直的折线，所以用**梯形面积公式**就能完美兼容这两种分类阈值选取方案，也即 **(上底+下底)\*高\*$ \cfrac{1}{2} $**

## 2.21

$$ l\_{rank}=\cfrac{1}{m^+m^-}\sum\_{x^+ \in D^+}\sum\_{x^- \in D^-}(||(f(x^+)<f(x^-))+\cfrac{1}{2}||(f(x^+)=f(x^-))) $$

[解析]：此公式正如书上所说，$ l\_{rank} $为ROC曲线**之上**的面积，假设某ROC曲线如下图所示：

![avatar](resources/images/lrank.png)

观察ROC曲线易知：
- 每增加一条绿色线段对应着有**1个**正样例（$ x^+\_i $）被模型正确判别为正例，且该线段在Y轴的投影长度恒为$ \cfrac{1}{m^+} $；
- 每增加一条红色线段对应着有**1个**反样例（$ x^-\_i $）被模型错误判别为正例，且该线段在X轴的投影长度恒为$ \cfrac{1}{m^-} $；
- 每增加一条蓝色线段对应着有a个正样例和b个反样例**同时**被判别为正例，且该线段在X轴上的投影长度=$ b * \cfrac{1}{m^-} $，在Y轴上的投影长度=$ a * \cfrac{1}{m^+} $；
- 任何一条线段所对应的样例的预测值一定**小于**其左边和下边的线段所对应的样例的预测值，其中蓝色线段所对应的a+b个样例的预测值相等。

公式里的$ \sum\_{x^+ \in D^+} $可以看成一个遍历$ x^+\_i $的循环：

for $ x^+\_i $ in $ D^+ $:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$ \cfrac{1}{m^+}\cdot\cfrac{1}{m^-}\cdot\sum\_{x^- \in D^-}(||(f(x^+\_i)<f(x^-))+\cfrac{1}{2}||(f(x^+\_i)=f(x^-))) $ #记为式S

由于每个$ x^+\_i $都对应着一条绿色或蓝色线段，所以遍历$ x^+\_i $可以看成是在遍历每条绿色和蓝色线段，并用式S来求出每条绿色线段与Y轴构成的面积（例如上图中的m1)或者蓝色线段与Y轴构成的面积（例如上图中的m2+m3）。

**对于每条绿色线段：** 将其式S展开可得：
$$ \cfrac{1}{m^+}\cdot\cfrac{1}{m^-}\cdot\sum\_{x^- \in D^-}||(f(x^+\_i)<f(x^-))+\cfrac{1}{m^+}\cdot\cfrac{1}{m^-}\cdot\sum\_{x^- \in D^-}\cfrac{1}{2}||(f(x^+\_i)=f(x^-)) $$其中$ x^+\_i $此时恒为该线段所对应的正样例，是一个定值。$ \sum\_{x^- \in D^-}\cfrac{1}{2}||(f(x^+\_i)=f(x^-) $是在通过遍历所有反样例来统计和$ x^+\_i $的预测值相等的反样例个数，由于没有反样例的预测值和$ x^+\_i $的预测值相等，所以$ \sum\_{x^- \in D^-}\cfrac{1}{2}||(f(x^+\_i)=f(x^-)) $此时恒为0，于是其式S可以化简为：$$ \cfrac{1}{m^+}\cdot\cfrac{1}{m^-}\cdot\sum\_{x^- \in D^-}||(f(x^+\_i)<f(x^-)) $$其中$ \cfrac{1}{m^+} $为该线段在Y轴上的投影长度，$ \sum\_{x^- \in D^-}||(f(x^+\_i)<f(x^-)) $同理是在通过遍历所有反样例来统计预测值大于$ x^+\_i $的预测值的反样例个数，也即该线段左边和下边的红色线段个数+蓝色线段对应的反样例个数，所以$ \cfrac{1}{m^-}\cdot\sum\_{x^- \in D^-}(||(f(x^+)<f(x^-))) $便是该线段左边和下边的红色线段在X轴的投影长度+蓝色线段在X轴的投影长度，也就是该绿色线段在X轴的投影长度，观察ROC图像易知绿色线段与Y轴围成的面积=该线段在Y轴的投影长度 * 该线段在X轴的投影长度。

**对于每条蓝色线段：** 将其式S展开可得：
$$ \cfrac{1}{m^+}\cdot\cfrac{1}{m^-}\cdot\sum\_{x^- \in D^-}||(f(x^+\_i)<f(x^-))+\cfrac{1}{m^+}\cdot\cfrac{1}{m^-}\cdot\sum\_{x^- \in D^-}\cfrac{1}{2}||(f(x^+\_i)=f(x^-)) $$
其中前半部分表示的是蓝色线段和Y轴围成的图形里面矩形部分的面积，后半部分表示的便是剩下的三角形的面积，矩形部分的面积公式同绿色线段的面积公式一样很好理解，而三角形部分的面积公式里面的$ \cfrac{1}{m^+} $为底边长，$ \cfrac{1}{m^-}\cdot\sum\_{x^- \in D^-}||(f(x^+\_i)=f(x^-)) $为高。

综上分析可知，式S既可以用来求绿色线段与Y轴构成的面积也能求蓝色线段与Y轴构成的面积，所以遍历完所有绿色和蓝色线段并将其与Y轴构成的面积累加起来即得$ l\_{rank} $。