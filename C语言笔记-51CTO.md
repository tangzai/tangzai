

<h2><a id="0_C_10"></a>0. 什么是C语言&#xff1f;</h2> 
<p>C语言是一门面向过程的计算机编程语言&#xff0c;与C&#43;&#43;&#xff0c;Java等面向对象的编程语言有所不同。<br /> 其编译器主要有Clang、<strong>GCC</strong>、WIN-TC、SUBLIME、<strong>MSVC</strong>、Turbo C等。</p> 
<h2><a id="1_C_13"></a>1. 第一个C语言程序</h2> 
<p>如果报错scanf:this function may be unsafe.请加上一句&#xff1a;&#xff08;具体什么原因&#xff0c;当时鹏哥说了&#xff0c;但是没记住&#xff01;&#xff09;<br /> #define _CRT_SECURE_NO_WARNINGS</p> 
<pre><code class="prism language-c"><span class="token comment">//#define _CRT_SECURE_NO_WARNINGS</span>
<span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">include</span> <span class="token string">&lt;stdio.h&gt;</span>  <span class="token comment">//头文件</span></span

<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;hello bit\n&#34;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;he he\n&#34;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>
<span class="token comment">//解释&#xff1a;</span>
<span class="token comment">//main函数是程序的入口</span>
<span class="token comment">//一个 工程中main函数有且仅有一个</span>
</code></pre> 

<h2><a id="2__29"></a>2. 数据类型</h2> 
<p>char //字符数据类型<br /> short //短整型<br /> int //整形<br /> long //长整型<br /> long long //更长的整形<br /> float //单精度浮点数<br /> double //双精度浮点数<br /> //C语言有没有字符串类型&#xff1f; 答案是没有。c语言中没有string类型&#xff0c;string是用char型数组来构造的。</p> 
<p>每种数据类型占据的字节大小&#xff1a;</p> 
<pre><code class="prism language-c"><span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">include</span> <span class="token string">&lt;stdio.h&gt;</span>  </span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
		<span class="token comment">//以下是在win10的vs2017显示的结果</span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> <span class="token keyword">sizeof</span><span class="token punctuation">(</span><span class="token keyword">char</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">// 1</span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> <span class="token keyword">sizeof</span><span class="token punctuation">(</span><span class="token keyword">short</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">// 2</span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> <span class="token keyword">sizeof</span><span class="token punctuation">(</span><span class="token keyword">int</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">// 4</span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> <span class="token keyword">sizeof</span><span class="token punctuation">(</span><span class="token keyword">long</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">// 4</span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> <span class="token keyword">sizeof</span><span class="token punctuation">(</span><span class="token keyword">long</span> <span class="token keyword">long</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">//8</span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> <span class="token keyword">sizeof</span><span class="token punctuation">(</span><span class="token keyword">float</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">//4</span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> <span class="token keyword">sizeof</span><span class="token punctuation">(</span><span class="token keyword">double</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">// 8</span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> <span class="token keyword">sizeof</span><span class="token punctuation">(</span><span class="token keyword">long</span> <span class="token keyword">double</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">//8</span>
    <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>
</code></pre> 
<p>存在这么多的类型&#xff0c;其实是为了更加丰富的表达生活中的各种值。<br /> 类型的使用&#xff1a;</p> 
<pre><code class="prism language-c"><span class="token keyword">char</span> ch <span class="token operator">&#61;</span> <span class="token char">&#39;w&#39;</span><span class="token punctuation">;</span>  <span class="token comment">//字符型</span>
<span class="token keyword">int</span> weight <span class="token operator">&#61;</span> <span class="token number">120</span><span class="token punctuation">;</span>  <span class="token comment">//整型</span>
<span class="token keyword">float</span> salary <span class="token operator">&#61;</span> <span class="token number">20000.0f</span><span class="token punctuation">;</span>  <span class="token comment">//单精度浮点型</span>
</code></pre> 
<h2><a id="3__64"></a>3. 变量、常量</h2> 
<p>生活中的有些值是不变的&#xff08;比如&#xff1a;圆周率&#xff0c;性别&#xff0c;身份证号码&#xff0c;血型等等&#xff09;<br /> 有些值是可变的&#xff08;比如&#xff1a;年龄&#xff0c;体重&#xff0c;薪资&#xff09;。<br /> 不变的值&#xff0c;C语言中用常量的概念来表示&#xff0c;变得值C语言中用变量来表示。</p> 
<h3><a id="31__69"></a>3.1 定义变量的方法</h3> 
<p>语法&#xff1a;变量类型 变量名称 &#61; 初始值&#xff1b;</p> 
<pre><code class="prism language-c"><span class="token keyword">int</span> age <span class="token operator">&#61;</span> <span class="token number">150</span><span class="token punctuation">;</span>
<span class="token keyword">float</span> weight <span class="token operator">&#61;</span> <span class="token number">45.5f</span><span class="token punctuation">;</span>
<span class="token keyword">char</span> ch <span class="token operator">&#61;</span> <span class="token char">&#39;w&#39;</span><span class="token punctuation">;</span>
</code></pre> 
<h3><a id="32__76"></a>3.2 变量的分类</h3> 
<p>1&#xff09;局部变量<br /> 2&#xff09;全局变量</p> 
<pre><code class="prism language-c"><span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">include</span> <span class="token string">&lt;stdio.h&gt;</span></span>
<span class="token keyword">int</span> global <span class="token operator">&#61;</span> <span class="token number">2019</span><span class="token punctuation">;</span><span class="token comment">//全局变量</span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
    <span class="token keyword">int</span> local <span class="token operator">&#61;</span> <span class="token number">2018</span><span class="token punctuation">;</span><span class="token comment">//局部变量</span>
    <span class="token comment">//下面定义的global会不会有问题&#xff1f;没有问题</span>
    <span class="token keyword">int</span> global <span class="token operator">&#61;</span> <span class="token number">2020</span><span class="token punctuation">;</span><span class="token comment">//局部变量&#xff0c;当局部变量和全局变量同名时&#xff0c;优先使用局部变量&#xff0c;这个人感觉和搜索路径相关&#xff01;</span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;global &#61; %d\n&#34;</span><span class="token punctuation">,</span> global<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>
</code></pre> 
<h3><a id="33__91"></a>3.3 变量的使用</h3> 
<pre><code class="prism language-c"><span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">include</span> <span class="token string">&lt;stdio.h&gt;</span></span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
    <span class="token keyword">int</span> num1 <span class="token operator">&#61;</span> <span class="token number">0</span><span class="token punctuation">;</span>
    <span class="token keyword">int</span> num2 <span class="token operator">&#61;</span> <span class="token number">0</span><span class="token punctuation">;</span>
    <span class="token keyword">int</span> sum <span class="token operator">&#61;</span> <span class="token number">0</span><span class="token punctuation">;</span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;输入两个操作数:&gt;&#34;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">scanf</span><span class="token punctuation">(</span><span class="token string">&#34;%d %d&#34;</span><span class="token punctuation">,</span> <span class="token operator">&amp;</span>num1<span class="token punctuation">,</span> <span class="token operator">&amp;</span>num2<span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">//int -- %d  float -- %f  double -- %lf  char -- %c</span>
    sum <span class="token operator">&#61;</span> num1 <span class="token operator">&#43;</span> num2<span class="token punctuation">;</span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;sum &#61; %d\n&#34;</span><span class="token punctuation">,</span> sum<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>
</code></pre> 
<p>这里介绍一下输入&#xff0c;输出语句<br /> scanf()&#xff1a;输入<br /> printf()&#xff1a;输出<br /> int – %d float – %f double – %lf char – %c</p> 
<pre><code class="prism language-c"><span class="token comment">//scanf()定义&#xff1a;等以后我看的懂&#xff0c;再回来解释&#xff0c;菜鸟哭泣&#xff01;</span>
    _Check_return_ <span class="token function">_CRT_INSECURE_DEPRECATE</span><span class="token punctuation">(</span>scanf_s<span class="token punctuation">)</span>
    _CRT_STDIO_INLINE <span class="token keyword">int</span> __CRTDECL <span class="token function">scanf</span><span class="token punctuation">(</span>
        _In_z_ _Scanf_format_string_ <span class="token keyword">char</span> <span class="token keyword">const</span><span class="token operator">*</span> <span class="token keyword">const</span> _Format<span class="token punctuation">,</span>
        <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span>
<span class="token comment">//printf()定义&#xff1a;</span>
    _Check_return_opt_
    _CRT_STDIO_INLINE <span class="token keyword">int</span> __CRTDECL <span class="token function">printf</span><span class="token punctuation">(</span>
        _In_z_ _Printf_format_string_ <span class="token keyword">char</span> <span class="token keyword">const</span><span class="token operator">*</span> <span class="token keyword">const</span> _Format<span class="token punctuation">,</span>
        <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span>
</code></pre> 
<h3><a id="34__123"></a>3.4 变量的作用域和生命周期</h3> 
<p>1&#xff09;作用域&#xff1a;作用域&#xff08;scope&#xff09;是程序设计概念&#xff0c;通常来说&#xff0c;一段程序代码中所用到的名字并不总是有效/可用<br /> 的。而限定这个名字的可用性的代码范围就是这个名字的作用域。<br /> &#xff08;1&#xff09;局部变量的作用域是变量所在的局部范围。比如&#xff1a;{int a &#61; 10;}&#xff0c;{}内就是局部变量a的作用域。<br /> &#xff08;2&#xff09;全局变量的作用域是整个工程。比如&#xff1a;int b &#61;100;int main(){};,在main()函数{}之外也不在其他范围内的变量。</p> 
<p>2&#xff09;生命周期:变量的生命周期指的是变量的创建到变量的销毁之间的一个时间段<br /> &#xff08;1&#xff09;局部变量的生命周期是&#xff1a;进入作用域生命周期开始&#xff0c;出作用域生命周期结束。比如函数的形参。<br /> &#xff08;2&#xff09;全局变量的生命周期是&#xff1a;整个程序的生命周期。程序结束&#xff0c;全局变量生命周期才结束。</p> 
<h3><a id="35__132"></a>3.5 常量</h3> 
<p>C语言中的常量和变量的定义的形式有所差异。<br /> C语言中的常量分为以下以下几种&#xff1a;<br /> 1&#xff09;字面常量<br /> 2&#xff09;const 修饰的常变量 语法&#xff1a; cont 变量类型<br /> 3&#xff09;#define 定义的标识符常量 语法&#xff1a;#define 常量标识符 常量值<br /> 4&#xff09;枚举常量 语法&#xff1a;enum 枚举常量名 {常量1&#xff0c; 常量2&#xff0c;…}; //这里记得加上分号&#xff01;常量面前没有常量类型</p> 
<pre><code class="prism language-c"><span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">define</span> <span class="token macro-name">_CRT_SECURE_NO_WARNINGS</span></span>
<span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">include</span> <span class="token string">&lt;stdio.h&gt;</span></span>
<span class="token comment">//举例</span>
<span class="token keyword">enum</span> <span class="token class-name">Sex</span>
<span class="token punctuation">{<!-- --></span>
	MALE<span class="token punctuation">,</span>
	<span class="token comment">//MALE&#61;2,  //可指定默认值&#xff0c;后面的值在2的基础上&#xff0c;递增&#43;1</span>
	FEMALE<span class="token punctuation">,</span>
	SECRET
<span class="token punctuation">}</span><span class="token punctuation">;</span>  <span class="token comment">//这里要加上分号;</span>
<span class="token comment">//括号中的MALE,FEMALE,SECRET是枚举常量</span>


<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
	<span class="token comment">//字面常量演示</span>
	<span class="token number">3.14</span><span class="token punctuation">;</span><span class="token comment">//字面常量</span>
	<span class="token number">1000</span><span class="token punctuation">;</span><span class="token comment">//字面常量</span>

	<span class="token comment">//const 修饰的常变量</span>
	<span class="token keyword">const</span> <span class="token keyword">float</span> pai <span class="token operator">&#61;</span> <span class="token number">3.14f</span><span class="token punctuation">;</span> <span class="token comment">//这里的pai是const修饰的 常变量</span>
	<span class="token comment">//pai &#61; 5.14;//是不能直接修改的&#xff01;vs2017会提示表达式必须是可修改的左值</span>
	
	<span class="token comment">//#define的标识符常量 演示</span>
	<span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">define</span> <span class="token macro-name">MAX</span> <span class="token expression"><span class="token number">100</span>  </span><span class="token comment">//这里没有分号”;“</span></span>
	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;max &#61; %d\n&#34;</span><span class="token punctuation">,</span> MAX<span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">// 100</span>
	
	<span class="token comment">//枚举常量演示</span>
	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> MALE<span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">// 0</span>
	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> FEMALE<span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">// 1</span>
	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> SECRET<span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">// 2</span>
	<span class="token comment">//注&#xff1a;枚举常量的默认是从0开始&#xff0c;依次向下递增1的</span>
	<span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre> 
<p>注&#xff08;常变量&#xff09;&#xff1a;<br /> 上面例子上的 pai 被称为 const 修饰的常变量&#xff0c; const 修饰的常变量在C语言中只是在语法层面限制了变量 pai 不能直接被改变&#xff0c;但是 pai 本质上还是一个变量的&#xff0c;所以叫常变量。</p> 
<h2><a id="4__178"></a>4. 字符串&#43;转义字符&#43;注释</h2> 
<h3><a id="41__179"></a>4.1 字符串</h3> 
<pre><code class="prism language-c"><span class="token string">&#34;hello bit.\n&#34;</span>
</code></pre> 
<p>这种由双引号&#xff08;Double Quote&#xff09;引起来的一串字符称为字符串字面值&#xff08;String Literal&#xff09;&#xff0c;或者简称字符串。<br /> 注&#xff1a;<strong>字符串的结束标志是一个 \0 的转义字符。在计算字符串长度的时候 \0 是结束标志&#xff0c;不算作字符串内容。</strong></p> 
<pre><code class="prism language-c"><span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">include</span> <span class="token string">&lt;stdio.h&gt;</span></span>
<span class="token comment">//下面代码&#xff0c;打印结果是什么&#xff1f;为什么&#xff1f;&#xff08;突出&#39;\0&#39;的重要性&#xff09;</span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
	<span class="token keyword">char</span> arr1<span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token operator">&#61;</span> <span class="token string">&#34;bit&#34;</span><span class="token punctuation">;</span>  
	<span class="token keyword">char</span> arr2<span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token operator">&#61;</span> <span class="token punctuation">{<!-- --></span> <span class="token char">&#39;b&#39;</span><span class="token punctuation">,</span> <span class="token char">&#39;i&#39;</span><span class="token punctuation">,</span> <span class="token char">&#39;t&#39;</span> <span class="token punctuation">}</span><span class="token punctuation">;</span>
	<span class="token keyword">char</span> arr3<span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token operator">&#61;</span> <span class="token punctuation">{<!-- --></span> <span class="token char">&#39;b&#39;</span><span class="token punctuation">,</span> <span class="token char">&#39;i&#39;</span><span class="token punctuation">,</span> <span class="token char">&#39;t&#39;</span><span class="token punctuation">,</span> <span class="token char">&#39;\0&#39;</span> <span class="token punctuation">}</span><span class="token punctuation">;</span>  
	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%s\n&#34;</span><span class="token punctuation">,</span> arr1<span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">//bit  默认包含了&#39;\0&#39;</span>
	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> <span class="token keyword">sizeof</span><span class="token punctuation">(</span>arr1<span class="token punctuation">)</span><span class="token operator">/</span><span class="token keyword">sizeof</span><span class="token punctuation">(</span><span class="token keyword">char</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//4, 但是在计算长度的时候是包括的&#xff01;&#xff01;&#xff01;</span>
	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%s\n&#34;</span><span class="token punctuation">,</span> arr2<span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">//bit烫烫烫烫蘠it 随机值</span>
	<span class="token comment">//printf(&#34;%d\n&#34;, sizeof(arr2) / sizeof(char));</span>
	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%s\n&#34;</span><span class="token punctuation">,</span> arr3<span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">//bit 显示写出&#39;\0&#39;</span>
	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> <span class="token keyword">sizeof</span><span class="token punctuation">(</span>arr3<span class="token punctuation">)</span> <span class="token operator">/</span> <span class="token keyword">sizeof</span><span class="token punctuation">(</span><span class="token keyword">char</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">// 4  </span>

	<span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre> 
<h3><a id="42__204"></a>4.2 转义字符</h3> 
<p>加入我们要在屏幕上打印一个目录&#xff1a; c:\code\test.c<br /> 我们该如何写代码&#xff1f;</p> 
<pre><code class="prism language-c"><span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">include</span> <span class="token string">&lt;stdio.h&gt;</span></span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
 <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;c:\code\test.c\n&#34;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>
</code></pre> 
<p>实际上程序运行的结果是这样的&#xff1a;<br /> <img src="https://img-blog.csdnimg.cn/524504a5f7194eaba25bbf2dadd5746c.png#pic_center" alt="转移字符\t" /><br /> 这里就不得不提一下转义字符了。转义字符顾名思义就是转变意思。<br /> 下面看一些转义字符。<br /> <img src="https://img-blog.csdnimg.cn/420243e826114f628e49e1e776fa2121.png?x-oss-process&#61;image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5p2o5b6354ix5ZGA,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center" alt="常用转义字符&#xff0c;我一般都是用到就查&#xff0c;实在是记不住那么多&#xff01;" /></p> 
<pre><code class="prism language-c"><span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">define</span> <span class="token macro-name">_CRT_SECURE_NO_WARNINGS</span></span>

<span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">include</span> <span class="token string">&lt;stdio.h&gt;</span></span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
	<span class="token comment">//问题1&#xff1a;在屏幕上打印一个单引号&#39;&#xff0c;怎么做&#xff1f;</span>
	<span class="token comment">//问题2&#xff1a;在屏幕上打印一个字符串&#xff0c;字符串的内容是一个双引号“&#xff0c;怎么做&#xff1f;</span>
	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%c\n&#34;</span><span class="token punctuation">,</span> <span class="token char">&#39;\&#39;&#39;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">//注意&#xff1a;单引号&#39;&#39;括起来的对应%c,双引号括起来的对应%s</span>
	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%s\n&#34;</span><span class="token punctuation">,</span> <span class="token string">&#34;\&#39;&#34;</span><span class="token punctuation">)</span><span class="token punctuation">;</span> 
	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%s\n&#34;</span><span class="token punctuation">,</span> <span class="token string">&#34;\&#34;&#34;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%c\n&#34;</span><span class="token punctuation">,</span> <span class="token char">&#39;\&#34;&#39;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre> 
<pre><code class="prism language-c"><span class="token comment">//程序输出什么&#xff1f;</span>
<span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">include</span> <span class="token string">&lt;stdio.h&gt;</span></span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
	<span class="token comment">//strlen()求字符串长度</span>
	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> <span class="token function">strlen</span><span class="token punctuation">(</span><span class="token string">&#34;abcdef&#34;</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">// 6</span>
	<span class="token comment">// \62被解析成一个转义字符</span>
	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> <span class="token function">strlen</span><span class="token punctuation">(</span><span class="token string">&#34;c:\test\628\test.c&#34;</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">//14 &#61; 11 &#43; 3个转义字符</span>

	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%c\n&#34;</span><span class="token punctuation">,</span> <span class="token char">&#39;\62&#39;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">//2  ???</span>
	
	<span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre> 
<h2><a id="5__251"></a>5. 注释</h2> 
<p>1&#xff09; 代码中有不需要的代码可以直接删除&#xff0c;也可以注释掉<br /> 2&#xff09;代码中有些代码比较难懂&#xff0c;可以加一下注释文字<br /> //注释单行<br /> /* 注释代码块 */</p> 
<pre><code class="prism language-c"><span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">include</span> <span class="token string">&lt;stdio.h&gt;</span></span>
<span class="token keyword">int</span> <span class="token function">Add</span><span class="token punctuation">(</span><span class="token keyword">int</span> x<span class="token punctuation">,</span> <span class="token keyword">int</span> y<span class="token punctuation">)</span> <span class="token punctuation">{<!-- --></span>
	<span class="token keyword">return</span> x <span class="token operator">&#43;</span> y<span class="token punctuation">;</span>
<span class="token punctuation">}</span>
<span class="token comment">/*C语言风格注释
int Sub(int x, int y)
{
    return x-y;
}
*/</span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
	<span class="token comment">//C&#43;&#43;注释风格</span>
	<span class="token comment">//int a &#61; 10;</span>
	<span class="token comment">//调用Add函数&#xff0c;完成加法</span>
	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> <span class="token function">Add</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre> 
<h2><a id="6__276"></a>6. 选择语句</h2> 
<p>如果你好好学习&#xff0c;校招时拿一个好offer&#xff0c;走上人生巅峰。<br /> 如果你不学习&#xff0c;毕业等于失业&#xff0c;回家卖红薯。<br /> 这就是选择&#xff01;<br /> <img src="https://img-blog.csdnimg.cn/6decd64d13f94ec7959be266018b9cdb.png?x-oss-process&#61;image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5p2o5b6354ix5ZGA,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center" alt="选择语句" /></p> 
<pre><code class="prism language-c"><span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">include</span> <span class="token string">&lt;stdio.h&gt;</span></span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
    <span class="token keyword">int</span> coding <span class="token operator">&#61;</span> <span class="token number">0</span><span class="token punctuation">;</span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;你会去敲代码吗&#xff1f;&#xff08;选择1 or 0&#xff09;:&gt;&#34;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">scanf</span><span class="token punctuation">(</span><span class="token string">&#34;%d&#34;</span><span class="token punctuation">,</span> <span class="token operator">&amp;</span>coding<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">if</span><span class="token punctuation">(</span>coding <span class="token operator">&#61;&#61;</span> <span class="token number">1</span><span class="token punctuation">)</span>
   <span class="token punctuation">{<!-- --></span>
       <span class="token function">prinf</span><span class="token punctuation">(</span><span class="token string">&#34;坚持&#xff0c;你会有好offer\n&#34;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
   <span class="token punctuation">}</span>
    <span class="token keyword">else</span>
   <span class="token punctuation">{<!-- --></span>
       <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;放弃&#xff0c;回家卖红薯\n&#34;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
   <span class="token punctuation">}</span>
    <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>
</code></pre> 
<h2><a id="7__298"></a>7. 循环语句</h2> 
<p>有些事必须一直做&#xff0c;比如我日复一日的讲课&#xff0c;比如大家&#xff0c;日复一日的学习。<br /> 还比如&#xff1a;<br /> <img src="https://img-blog.csdnimg.cn/11811a1d85e647289060d147e31d8d36.png?x-oss-process&#61;image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5p2o5b6354ix5ZGA,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center" alt="循环结构" />C语言的循环结构有&#xff1a;<br /> 1&#xff09;while语句-讲解<br /> while(结束条件) //满足结束条件&#xff0c;就跳出循环体<br /> {<!-- --><br /> 循环体&#xff1b;<br /> }</p> 
<p>2&#xff09;for语句&#xff08;后期讲解&#xff09;<br /> 3&#xff09;do…while (后期讲解)<br /> 要想成为大牛&#xff01;要敲20000行有效代码&#xff0c;干完这个就去干数据结构&#xff01;&#xff01;&#xff01;奥力给&#xff01;&#xff01;&#xff01;</p> 
<pre><code class="prism language-c"><span class="token comment">//while循环的实例</span>
<span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">include</span> <span class="token string">&lt;stdio.h&gt;</span></span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;加入比特\n&#34;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">int</span> line <span class="token operator">&#61;</span> <span class="token number">0</span><span class="token punctuation">;</span>
	<span class="token keyword">while</span> <span class="token punctuation">(</span>line <span class="token operator">&lt;&#61;</span> <span class="token number">20000</span><span class="token punctuation">)</span>
	<span class="token punctuation">{<!-- --></span>
		line<span class="token operator">&#43;&#43;</span><span class="token punctuation">;</span>
		<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;我要继续努力敲代码\n&#34;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
	<span class="token keyword">if</span> <span class="token punctuation">(</span>line <span class="token operator">&gt;</span> <span class="token number">20000</span><span class="token punctuation">)</span>
		<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;好offer\n&#34;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre> 
<h2><a id="8__328"></a>8. 函数</h2> 
<pre><code class="prism language-c"><span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">include</span> <span class="token string">&lt;stdio.h&gt;</span></span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
    <span class="token keyword">int</span> num1 <span class="token operator">&#61;</span> <span class="token number">0</span><span class="token punctuation">;</span>
   <span class="token keyword">int</span> num2 <span class="token operator">&#61;</span> <span class="token number">0</span><span class="token punctuation">;</span>
    <span class="token keyword">int</span> sum <span class="token operator">&#61;</span> <span class="token number">0</span><span class="token punctuation">;</span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;输入两个操作数:&gt;&#34;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">scanf</span><span class="token punctuation">(</span><span class="token string">&#34;%d %d&#34;</span><span class="token punctuation">,</span> <span class="token operator">&amp;</span>num1<span class="token punctuation">,</span> <span class="token operator">&amp;</span>num2<span class="token punctuation">)</span><span class="token punctuation">;</span>
    sum <span class="token operator">&#61;</span> num1 <span class="token operator">&#43;</span> num2<span class="token punctuation">;</span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;sum &#61; %d\n&#34;</span><span class="token punctuation">,</span> sum<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>
</code></pre> 
<p>上述代码&#xff0c;写成函数如下&#xff1a;<br /> 函数的特点就是简化代码&#xff0c;代码复用。<br /> 函数语法&#xff1a;<br /> 函数返回值类型 函数名&#xff08;函数形参&#xff09;<br /> {<!-- --><br /> 函数体<br /> }</p> 
<pre><code class="prism language-c"><span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">include</span> <span class="token string">&lt;stdio.h&gt;</span></span>
<span class="token keyword">int</span> <span class="token function">Add</span><span class="token punctuation">(</span><span class="token keyword">int</span> x<span class="token punctuation">,</span> <span class="token keyword">int</span> y<span class="token punctuation">)</span> <span class="token punctuation">{<!-- --></span>
   <span class="token keyword">int</span> z <span class="token operator">&#61;</span> x<span class="token operator">&#43;</span>y<span class="token punctuation">;</span>
   <span class="token keyword">return</span> z<span class="token punctuation">;</span> <span class="token punctuation">}</span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
    <span class="token keyword">int</span> num1 <span class="token operator">&#61;</span> <span class="token number">0</span><span class="token punctuation">;</span>
   <span class="token keyword">int</span> num2 <span class="token operator">&#61;</span> <span class="token number">0</span><span class="token punctuation">;</span>
    <span class="token keyword">int</span> sum <span class="token operator">&#61;</span> <span class="token number">0</span><span class="token punctuation">;</span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;输入两个操作数:&gt;&#34;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">scanf</span><span class="token punctuation">(</span><span class="token string">&#34;%d %d&#34;</span><span class="token punctuation">,</span> <span class="token operator">&amp;</span>num1<span class="token punctuation">,</span> <span class="token operator">&amp;</span>num2<span class="token punctuation">)</span><span class="token punctuation">;</span>
    sum <span class="token operator">&#61;</span> <span class="token function">Add</span><span class="token punctuation">(</span>num1<span class="token punctuation">,</span> num2<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;sum &#61; %d\n&#34;</span><span class="token punctuation">,</span> sum<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>
</code></pre> 
<h2><a id="9__366"></a>9. 数组</h2> 
<p>要存储1-10的数字&#xff0c;怎么存储&#xff1f;<br /> C语言中给了数组的定义&#xff1a;一组<strong>相同类型</strong>元素的集合</p> 
<h3><a id="91__369"></a>9.1 数组定义</h3> 
<p>语法&#xff1a;数组元素类型 数组名[数组长度] &#61; {元素1&#xff0c; 元素2&#xff0c; …};</p> 
<pre><code class="prism language-c"><span class="token keyword">int</span> arr<span class="token punctuation">[</span><span class="token number">10</span><span class="token punctuation">]</span> <span class="token operator">&#61;</span> <span class="token punctuation">{<!-- --></span><span class="token number">1</span><span class="token punctuation">,</span><span class="token number">2</span><span class="token punctuation">,</span><span class="token number">3</span><span class="token punctuation">,</span><span class="token number">4</span><span class="token punctuation">,</span><span class="token number">5</span><span class="token punctuation">,</span><span class="token number">6</span><span class="token punctuation">,</span><span class="token number">7</span><span class="token punctuation">,</span><span class="token number">8</span><span class="token punctuation">,</span><span class="token number">9</span><span class="token punctuation">,</span><span class="token number">10</span><span class="token punctuation">}</span><span class="token punctuation">;</span><span class="token comment">//定义一个整形数组&#xff0c;最多放10个元素</span>
</code></pre> 
<h3><a id="92__375"></a>9.2 数组的下标</h3> 
<p>C语言规定&#xff1a;数组的每个元素都有一个下标&#xff0c;下标是从0开始的。<br /> 数组可以通过下标来访问的。比如&#xff1a;</p> 
<pre><code class="prism language-c"><span class="token keyword">int</span> arr<span class="token punctuation">[</span><span class="token number">10</span><span class="token punctuation">]</span> <span class="token operator">&#61;</span> <span class="token punctuation">{<!-- --></span><span class="token number">0</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
<span class="token comment">//如果数组10个元素&#xff0c;下标的范围是0-9</span>
</code></pre> 
<h3><a id="93__382"></a>9.3 数组的使用</h3> 
<p>利用循环for遍历数组</p> 
<pre><code class="prism language-c"><span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">include</span> <span class="token string">&lt;stdio.h&gt;</span></span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
 <span class="token keyword">int</span> i <span class="token operator">&#61;</span> <span class="token number">0</span><span class="token punctuation">;</span>
 <span class="token keyword">int</span> arr<span class="token punctuation">[</span><span class="token number">10</span><span class="token punctuation">]</span> <span class="token operator">&#61;</span> <span class="token punctuation">{<!-- --></span><span class="token number">1</span><span class="token punctuation">,</span><span class="token number">2</span><span class="token punctuation">,</span><span class="token number">3</span><span class="token punctuation">,</span><span class="token number">4</span><span class="token punctuation">,</span><span class="token number">5</span><span class="token punctuation">,</span><span class="token number">6</span><span class="token punctuation">,</span><span class="token number">7</span><span class="token punctuation">,</span><span class="token number">8</span><span class="token punctuation">,</span><span class="token number">9</span><span class="token punctuation">,</span><span class="token number">10</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
 <span class="token keyword">for</span><span class="token punctuation">(</span>i<span class="token operator">&#61;</span><span class="token number">0</span><span class="token punctuation">;</span> i<span class="token operator">&lt;</span><span class="token number">10</span><span class="token punctuation">;</span> i<span class="token operator">&#43;&#43;</span><span class="token punctuation">)</span>
 <span class="token punctuation">{<!-- --></span>
       <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d &#34;</span><span class="token punctuation">,</span> arr<span class="token punctuation">[</span>i<span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
 <span class="token punctuation">}</span>
 <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;\n&#34;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span> 
<span class="token punctuation">}</span>
</code></pre> 
<h3><a id="10__398"></a>10. 操作符</h3> 
<p>简单介绍<br /> 1&#xff09;算术操作符</p> 
<pre><code class="prism language-c"><span class="token operator">&#43;</span> <span class="token operator">-</span> <span class="token operator">*</span> <span class="token operator">/</span> <span class="token operator">%</span>  <span class="token comment">//加 减 乘 除 取余</span>
</code></pre> 
<p>2&#xff09;移位操作符</p> 
<pre><code class="prism language-c"><span class="token operator">&gt;&gt;</span> <span class="token operator">&lt;&lt;</span>   <span class="token comment">//右移操作符号  左移操作符号</span>
</code></pre> 
<p>3&#xff09;位操作符</p> 
<pre><code class="prism language-c"><span class="token operator">&amp;</span> <span class="token operator">^</span> <span class="token operator">|</span>  <span class="token comment">//按位与  按位取反 按位或</span>
</code></pre> 
<p>4&#xff09;赋值操作符</p> 
<pre><code class="prism language-c"><span class="token operator">&#61;</span>  <span class="token operator">&#43;&#61;</span>  <span class="token operator">-&#61;</span>  <span class="token operator">*&#61;</span>  <span class="token operator">/&#61;</span>  <span class="token operator">&amp;&#61;</span> <span class="token operator">^&#61;</span>  <span class="token operator">|&#61;</span>    <span class="token operator">&gt;&gt;&#61;</span>   <span class="token operator">&lt;&lt;&#61;</span>
</code></pre> 
<p>5&#xff09;单目操作符</p> 
<pre><code class="prism language-c"><span class="token operator">!</span>          <span class="token comment">// 逻辑反操作</span>
<span class="token operator">-</span>          <span class="token comment">// 负值</span>
<span class="token operator">&#43;</span>          <span class="token comment">// 正值</span>
<span class="token operator">&amp;</span>          <span class="token comment">// 取地址</span>
<span class="token keyword">sizeof</span>     <span class="token comment">// 操作数的类型长度&#xff08;以字节为单位&#xff09;</span>
<span class="token operator">~</span>          <span class="token comment">// 对一个数的二进制按位取反</span>
<span class="token operator">--</span>         <span class="token comment">// 前置、后置--</span>
<span class="token operator">&#43;&#43;</span>         <span class="token comment">// 前置、后置&#43;&#43;</span>
<span class="token operator">*</span>          <span class="token comment">// 间接访问操作符(解引用操作符) (类型)       强制类型转换</span>
</code></pre> 
<p>6&#xff09;关系操作符</p> 
<pre><code class="prism language-c"><span class="token operator">&gt;</span>
<span class="token operator">&gt;&#61;</span>
<span class="token operator">&lt;</span>
<span class="token operator">&lt;&#61;</span>
<span class="token operator">!&#61;</span>    <span class="token comment">// 用于测试“不相等”</span>
<span class="token operator">&#61;&#61;</span>    <span class="token comment">// 用于测试“相等</span>
</code></pre> 
<p>7&#xff09;逻辑操作符</p> 
<pre><code class="prism language-c"><span class="token operator">&amp;&amp;</span>     <span class="token comment">// 逻辑与</span>
<span class="token operator">||</span>     <span class="token comment">// 逻辑或</span>
</code></pre> 
<p>8&#xff09;条件操作符</p> 
<pre><code class="prism language-c">exp1 <span class="token operator">?</span> exp2 <span class="token operator">:</span> exp3   <span class="token comment">//三目运算符 exp1成立&#xff0c;取exp2&#xff0c;否则取exp3</span>
</code></pre> 
<p>9&#xff09;逗号运算符</p> 
<pre><code class="prism language-c">exp1<span class="token punctuation">,</span> exp2<span class="token punctuation">,</span> exp3<span class="token punctuation">,</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
</code></pre> 
<p>10&#xff09;下标引用、函数调用和结构成员</p> 
<pre><code class="prism language-c"><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">.</span> <span class="token operator">-&gt;</span>  <span class="token comment">//-&gt;针对结构体指针</span>
</code></pre> 
<h2><a id="11__466"></a>11. 常见关键字</h2> 
<p>定义变量名时不能占用这些关键字。</p> 
<pre><code class="prism language-c"><span class="token keyword">auto</span>  <span class="token keyword">break</span>   <span class="token keyword">case</span>  <span class="token keyword">char</span>  <span class="token keyword">const</span>   <span class="token keyword">continue</span>  <span class="token keyword">default</span>  <span class="token keyword">do</span>   <span class="token keyword">double</span> <span class="token keyword">else</span>  <span class="token keyword">enum</span>   
<span class="token class-name">extern</span> <span class="token keyword">float</span>  <span class="token keyword">for</span>   <span class="token keyword">goto</span>  <span class="token keyword">if</span>   <span class="token keyword">int</span>   <span class="token keyword">long</span>  <span class="token keyword">register</span>    <span class="token keyword">return</span>   <span class="token keyword">short</span>  <span class="token keyword">signed</span>
<span class="token keyword">sizeof</span>   <span class="token keyword">static</span> <span class="token keyword">struct</span>  <span class="token class-name">switch</span>  <span class="token keyword">typedef</span> <span class="token keyword">union</span>  <span class="token keyword">unsigned</span>   <span class="token keyword">void</span>  <span class="token keyword">volatile</span>  <span class="token keyword">while</span>
</code></pre> 
<p>注&#xff1a;关键字&#xff0c;先介绍下面几个&#xff0c;后期遇到讲解。</p> 
<h3><a id="111__typedef_474"></a>11.1 关键字 typedef</h3> 
<p>typedef 顾名思义是类型定义&#xff0c;这里应该理解为类型重命名。比如&#xff1a;</p> 
<pre><code class="prism language-c"><span class="token comment">//将unsigned int 重命名为uint_32, 所以uint_32也是一个类型名</span>
<span class="token keyword">typedef</span> <span class="token keyword">unsigned</span> <span class="token keyword">int</span> uint_32<span class="token punctuation">;</span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
    <span class="token comment">//观察num1和num2,这两个变量的类型是一样的</span>
    <span class="token keyword">unsigned</span> <span class="token keyword">int</span> num1 <span class="token operator">&#61;</span> <span class="token number">0</span><span class="token punctuation">;</span>
    uint_32 num2 <span class="token operator">&#61;</span> <span class="token number">0</span><span class="token punctuation">;</span>
    <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>
</code></pre> 
<h3><a id="112_static_487"></a>11.2 关键字static</h3> 
<p>在C语言中&#xff1a;<br /> static是用来修饰变量和函数的</p> 
<ol><li>修饰局部变量-称为静态局部变量</li><li>修饰全局变量-称为静态全局变量</li><li>修饰函数-称为静态函数</li></ol> 
<h4><a id="1121__493"></a>11.2.1 修饰局部变量</h4> 
<p>对比代码1和代码2的效果理解static修饰局部变量的意义。</p> 
<pre><code class="prism language-c"><span class="token comment">//代码1</span>
<span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">include</span> <span class="token string">&lt;stdio.h&gt;</span></span>
<span class="token keyword">void</span> <span class="token function">test</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
    <span class="token keyword">int</span> i <span class="token operator">&#61;</span> <span class="token number">0</span><span class="token punctuation">;</span>
    i<span class="token operator">&#43;&#43;</span><span class="token punctuation">;</span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d &#34;</span><span class="token punctuation">,</span> i<span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">//每次函数执行结束&#xff0c;i值都会被释放掉</span>
<span class="token punctuation">}</span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
 <span class="token keyword">int</span> i <span class="token operator">&#61;</span> <span class="token number">0</span><span class="token punctuation">;</span>
    <span class="token keyword">for</span><span class="token punctuation">(</span>i<span class="token operator">&#61;</span><span class="token number">0</span><span class="token punctuation">;</span> i<span class="token operator">&lt;</span><span class="token number">10</span><span class="token punctuation">;</span> i<span class="token operator">&#43;&#43;</span><span class="token punctuation">)</span>
   <span class="token punctuation">{<!-- --></span>
        <span class="token function">test</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//每次有打印1</span>
   <span class="token punctuation">}</span>
    <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>
</code></pre> 
<pre><code class="prism language-c"><span class="token comment">//代码2</span>
<span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">include</span> <span class="token string">&lt;stdio.h&gt;</span></span>
<span class="token keyword">void</span> <span class="token function">test</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
    <span class="token comment">//static修饰局部变量</span>
    <span class="token keyword">static</span> <span class="token keyword">int</span> i <span class="token operator">&#61;</span> <span class="token number">0</span><span class="token punctuation">;</span>  <span class="token comment">//test()执行结束后,保留i值</span>
    i<span class="token operator">&#43;&#43;</span><span class="token punctuation">;</span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d &#34;</span><span class="token punctuation">,</span> i<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
 <span class="token keyword">int</span> i <span class="token operator">&#61;</span> <span class="token number">0</span><span class="token punctuation">;</span>
    <span class="token keyword">for</span><span class="token punctuation">(</span>i<span class="token operator">&#61;</span><span class="token number">0</span><span class="token punctuation">;</span> i<span class="token operator">&lt;</span><span class="token number">10</span><span class="token punctuation">;</span> i<span class="token operator">&#43;&#43;</span><span class="token punctuation">)</span>
   <span class="token punctuation">{<!-- --></span>
        <span class="token function">test</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//每次有打印1到10</span>
   <span class="token punctuation">}</span>
    <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>
</code></pre> 
<p>结论&#xff1a;static修饰局部变量改变了变量的生命周期&#xff0c;让静态局部变量出了作用域依然存在&#xff0c;<strong>到程序结束&#xff0c;生命周期才结束。</strong></p> 
<h4><a id="1122__535"></a>11.2.2 修饰全局变量</h4> 
<p>代码1正常&#xff0c;代码2在编译的时候会出现连接性错误。</p> 
<pre><code class="prism language-c"><span class="token comment">//代码1</span>
<span class="token comment">//add.c</span>
<span class="token keyword">int</span> g_val <span class="token operator">&#61;</span> <span class="token number">2018</span><span class="token punctuation">;</span>
<span class="token comment">//test.c</span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> g_val<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>

<span class="token comment">//代码2</span>
<span class="token comment">//add.c   static 全局变量</span>
<span class="token keyword">static</span> <span class="token keyword">int</span> g_val <span class="token operator">&#61;</span> <span class="token number">2018</span><span class="token punctuation">;</span>
<span class="token comment">//test.c</span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> g_val<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>
</code></pre> 
<p>结论&#xff1a;<strong>一个全局变量被static修饰&#xff0c;使得这个全局变量只能在本源文件内使用</strong>&#xff0c;不能在其他源文件内使用。</p> 
<h4><a id="1123__557"></a>11.2.3 修饰函数</h4> 
<p>代码1正常&#xff0c;代码2在编译的时候会出现连接性错误&#xff0c;和修饰全局变量一样。</p> 
<pre><code class="prism language-c"><span class="token comment">//代码1</span>
<span class="token comment">//add.c</span>
<span class="token keyword">int</span> <span class="token function">Add</span><span class="token punctuation">(</span><span class="token keyword">int</span> x<span class="token punctuation">,</span> <span class="token keyword">int</span> y<span class="token punctuation">)</span> <span class="token punctuation">{<!-- --></span>
    <span class="token keyword">return</span> x<span class="token operator">&#43;</span>y<span class="token punctuation">;</span> <span class="token punctuation">}</span>
<span class="token comment">//test.c</span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> <span class="token function">Add</span><span class="token punctuation">(</span><span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>

<span class="token comment">//代码2</span>
<span class="token comment">//add.c   static 修饰函数</span>
<span class="token keyword">static</span> <span class="token keyword">int</span> <span class="token function">Add</span><span class="token punctuation">(</span><span class="token keyword">int</span> x<span class="token punctuation">,</span> <span class="token keyword">int</span> y<span class="token punctuation">)</span> <span class="token punctuation">{<!-- --></span>
    <span class="token keyword">return</span> c<span class="token operator">&#43;</span>y<span class="token punctuation">;</span> <span class="token punctuation">}</span>
<span class="token comment">//test.c</span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> <span class="token function">Add</span><span class="token punctuation">(</span><span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>
</code></pre> 
<p>结论&#xff1a;<strong>一个函数被static修饰&#xff0c;使得这个函数只能在本源文件内使用</strong>&#xff0c;不能在其他源文件内使用。<br /> &#xff08;剩余关键字后续课程中陆续会讲解。&#xff09;</p> 
<h2><a id="12_define__582"></a>12. #define 定义常量和宏</h2> 
<p>注意 #define 后面没有分号;<br /> #define 常量名 常量值<br /> #define 宏名 宏表达式</p> 
<pre><code class="prism language-c"><span class="token comment">//define定义标识符常量</span>
<span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">define</span> <span class="token macro-name">MAX</span> <span class="token expression"><span class="token number">1000</span></span></span>
<span class="token comment">//define定义宏</span>
<span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">define</span> <span class="token macro-name function">ADD</span><span class="token expression"><span class="token punctuation">(</span>x<span class="token punctuation">,</span> y<span class="token punctuation">)</span> <span class="token punctuation">(</span><span class="token punctuation">(</span>x<span class="token punctuation">)</span><span class="token operator">&#43;</span><span class="token punctuation">(</span>y<span class="token punctuation">)</span><span class="token punctuation">)</span>   </span><span class="token comment">//注意x,y要用括号括起来</span></span>
<span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">include</span> <span class="token string">&lt;stdio.h&gt;</span></span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
	<span class="token keyword">int</span> sum <span class="token operator">&#61;</span> <span class="token function">ADD</span><span class="token punctuation">(</span><span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;sum &#61; %d\n&#34;</span><span class="token punctuation">,</span> sum<span class="token punctuation">)</span><span class="token punctuation">;</span>

	sum <span class="token operator">&#61;</span> <span class="token number">10</span> <span class="token operator">*</span> <span class="token function">ADD</span><span class="token punctuation">(</span><span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;sum &#61; %d\n&#34;</span><span class="token punctuation">,</span> sum<span class="token punctuation">)</span><span class="token punctuation">;</span>
	
	<span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre> 
<h2><a id="13__603"></a>13. 指针</h2> 
<h3><a id="131__604"></a>13.1 内存</h3> 
<p>内存是电脑上特别重要的存储器&#xff0c;计算机中程序的运行都是在内存中进行的 。<br /> 所以为了有效的使用内存&#xff0c;就把内存划分成一个个小的内存单元&#xff0c;每个内存单元的大小是1个字节。<br /> 为了能够有效的访问到内存的每个单元&#xff0c;就给内存单元进行了编号&#xff0c;这些编号被称为该内存单元的地址。<br /> <img src="https://img-blog.csdnimg.cn/8fb97c9db41f48419415d7c9ab35252d.png?x-oss-process&#61;image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5p2o5b6354ix5ZGA,size_16,color_FFFFFF,t_70,g_se,x_16#pic_center" alt="内存示意图" />变量是创建在内存中的&#xff08;在内存中分配空间的)&#xff0c;每个内存单元都有地址&#xff0c;所以变量也是有地址的。取出变量地址如下&#xff1a;</p> 
<pre><code class="prism language-c"><span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">include</span> <span class="token string">&lt;stdio.h&gt;</span></span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
 <span class="token keyword">int</span> num <span class="token operator">&#61;</span> <span class="token number">10</span><span class="token punctuation">;</span>
 <span class="token operator">&amp;</span>num<span class="token punctuation">;</span><span class="token comment">//取出num的地址</span>
    <span class="token comment">//注&#xff1a;这里num的4个字节&#xff0c;每个字节都有地址&#xff0c;取出的是第一个字节的地址&#xff08;较小的地址&#xff09;</span>
 <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%p\n&#34;</span><span class="token punctuation">,</span> <span class="token operator">&amp;</span>num<span class="token punctuation">)</span><span class="token punctuation">;</span><span class="token comment">//打印地址&#xff0c;%p是以地址的形式打印</span>
 <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>
</code></pre> 
<p><img src="https://img-blog.csdnimg.cn/0a677fdaf62a40809e990a954e2e71c7.png?x-oss-process&#61;image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5p2o5b6354ix5ZGA,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center" alt="取地址示意图" />那地址如何存储&#xff0c;需要定义指针变量。</p> 
<pre><code class="prism language-c"><span class="token keyword">int</span> num <span class="token operator">&#61;</span> <span class="token number">10</span><span class="token punctuation">;</span>
<span class="token keyword">int</span> <span class="token operator">*</span>p<span class="token punctuation">;</span>  <span class="token comment">//p为一个整形指针变量</span>
p <span class="token operator">&#61;</span> <span class="token operator">&amp;</span>num<span class="token punctuation">;</span>
</code></pre> 
<p>指针的使用实例&#xff1a;</p> 
<pre><code class="prism language-c"><span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">include</span> <span class="token string">&lt;stdio.h&gt;</span></span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
	<span class="token keyword">int</span> num <span class="token operator">&#61;</span> <span class="token number">10</span><span class="token punctuation">;</span>
	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> num<span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">// 10</span>

	<span class="token keyword">int</span> <span class="token operator">*</span>p <span class="token operator">&#61;</span> <span class="token operator">&amp;</span>num<span class="token punctuation">;</span>
	<span class="token operator">*</span>p <span class="token operator">&#61;</span> <span class="token number">20</span><span class="token punctuation">;</span>
	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> <span class="token operator">*</span>p<span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">// 20</span>
	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> num<span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">// 20</span>
	
	<span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre> 
<p><img src="https://img-blog.csdnimg.cn/37e7789767754e75b5507cb9acc756c3.png?x-oss-process&#61;image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5p2o5b6354ix5ZGA,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center" alt="指针应用" />以整形指针举例&#xff0c;可以推广到其他类型&#xff0c;如&#xff1a;</p> 
<pre><code class="prism language-c"><span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">include</span> <span class="token string">&lt;stdio.h&gt;</span></span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
 <span class="token keyword">char</span> ch <span class="token operator">&#61;</span> <span class="token char">&#39;w&#39;</span><span class="token punctuation">;</span>  
 <span class="token keyword">char</span><span class="token operator">*</span> pc <span class="token operator">&#61;</span> <span class="token operator">&amp;</span>ch<span class="token punctuation">;</span>  <span class="token comment">// 用ch的地址给指针变量pc初始化,即pc存的是变量ch的地址</span>
 <span class="token operator">*</span>pc <span class="token operator">&#61;</span> <span class="token char">&#39;q&#39;</span><span class="token punctuation">;</span>  <span class="token comment">//pc指向的&#xff08;ch&#xff09;地址上的值改为‘q’</span>
 <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%c\n&#34;</span><span class="token punctuation">,</span> ch<span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">// q</span>
    <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span> 
<span class="token punctuation">}</span>
</code></pre> 
<h3><a id="132__656"></a>13.2 指针变量的大小</h3> 
<pre><code class="prism language-c"><span class="token macro property"><span class="token directive-hash">#</span><span class="token directive keyword">include</span> <span class="token string">&lt;stdio.h&gt;</span></span>
<span class="token comment">//指针变量的大小取决于地址的大小,即无论何种类型的指针都取决于地址的大小</span>
<span class="token comment">//32位平台下地址是32个bit位&#xff08;即4个字节&#xff09;</span>
<span class="token comment">//64位平台下地址是64个bit位&#xff08;即8个字节&#xff09;</span>
<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{<!-- --></span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> <span class="token keyword">sizeof</span><span class="token punctuation">(</span><span class="token keyword">char</span> <span class="token operator">*</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> <span class="token keyword">sizeof</span><span class="token punctuation">(</span><span class="token keyword">short</span> <span class="token operator">*</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> <span class="token keyword">sizeof</span><span class="token punctuation">(</span><span class="token keyword">int</span> <span class="token operator">*</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;%d\n&#34;</span><span class="token punctuation">,</span> <span class="token keyword">sizeof</span><span class="token punctuation">(</span><span class="token keyword">double</span> <span class="token operator">*</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>
</code></pre> 
<p>结论&#xff1a;<strong>指针大小在32位平台是4个字节&#xff0c;64位平台是8个字节。</strong></p> 
<h2><a id="14__671"></a>14. 结构体</h2> 
<p>结构体是C语言中特别重要的知识点&#xff0c;结构体使得C语言有能力描述复杂类型。<br /> 比如描述学生&#xff0c;学生包含&#xff1a; 名字&#43;年龄&#43;性别&#43;学号 这几项信息。<br /> 这里只能使用结构体来描述了。例如&#xff1a;<br /> 语法&#xff1a;struct 结构体类型名<br /> {<!-- --><br /> 成员变量类型1 成员变量1&#xff1b;<br /> 成员变量类型2 成员变量2&#xff1b;<br /> }<br /> 个人感觉像类&#xff0c;但是只包含类的属性&#xff0c;没有方法。</p> 
<pre><code class="prism language-c"><span class="token keyword">struct</span> <span class="token class-name">Stu</span>
<span class="token punctuation">{<!-- --></span>
    <span class="token keyword">char</span> name<span class="token punctuation">[</span><span class="token number">20</span><span class="token punctuation">]</span><span class="token punctuation">;</span><span class="token comment">//名字</span>
    <span class="token keyword">int</span> age<span class="token punctuation">;</span>      <span class="token comment">//年龄</span>
    <span class="token keyword">char</span> sex<span class="token punctuation">[</span><span class="token number">5</span><span class="token punctuation">]</span><span class="token punctuation">;</span>  <span class="token comment">//性别</span>
    <span class="token keyword">char</span> id<span class="token punctuation">[</span><span class="token number">15</span><span class="token punctuation">]</span>&#xff1b; <span class="token comment">//学号</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre> 
<p>结构体的初始化和点操作符 . -&gt; &#xff1a;</p> 
<pre><code class="prism language-c"><span class="token comment">//打印结构体信息</span>
<span class="token keyword">struct</span> <span class="token class-name">Stu</span> s <span class="token operator">&#61;</span> <span class="token punctuation">{<!-- --></span><span class="token string">&#34;张三&#34;</span>&#xff0c; <span class="token number">20</span>&#xff0c; <span class="token string">&#34;男&#34;</span>&#xff0c; <span class="token string">&#34;20180101&#34;</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
<span class="token comment">//.为结构成员访问操作符  用于结构体变量访问成员</span>
<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;name &#61; %s age &#61; %d sex &#61; %s id &#61; %s\n&#34;</span><span class="token punctuation">,</span> s<span class="token punctuation">.</span>name<span class="token punctuation">,</span> s<span class="token punctuation">.</span>age<span class="token punctuation">,</span> s<span class="token punctuation">.</span>sex<span class="token punctuation">,</span> s<span class="token punctuation">.</span>id<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token comment">//-&gt;操作符  用于结构体指针变量访问成员变量</span>
<span class="token keyword">struct</span> <span class="token class-name">Stu</span> <span class="token operator">*</span>ps <span class="token operator">&#61;</span> <span class="token operator">&amp;</span>s<span class="token punctuation">;</span>
<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">&#34;name &#61; %s age &#61; %d sex &#61; %s id &#61; %s\n&#34;</span><span class="token punctuation">,</span> ps<span class="token operator">-&gt;</span>name<span class="token punctuation">,</span> ps<span class="token operator">-&gt;</span>age<span class="token punctuation">,</span> ps<span class="token operator">-&gt;</span>sex<span class="token punctuation">,</span> ps<span class="token operator">-</span> <span class="token operator">&gt;</span>id<span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre> 
<p>&#xff08;缺少了联合体union的内容&#xff01;&#xff09;<br /> 初始C语言到此结束&#xff01;</p>