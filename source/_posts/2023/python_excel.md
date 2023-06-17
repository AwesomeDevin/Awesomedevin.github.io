---
title: 使用openpyxl进行excel操作
date: 2021-04-20
categories:
- python
tags:
- 数据处理
- excel 处理
---
<section class="post">
    <p>openpyxl模块用来读写Excel文件。openpyxl工作时，在内存中创建Excel工作簿和工作表，然后在工作表中的单元格中进行各种数据编辑和样式编辑操作，或在工作表中绘制图形，最后再保存文件写入到Excel中。</p>

<p>官方文档： <a href="http://openpyxl.readthedocs.io/en/default/" title="http://openpyxl.readthedocs.io/en/default/">http://openpyxl.readthedocs.io/en/default/</a></p>

<h1 id="1-基本操作">1. 基本操作</h1>
<h2 id="11-引入openpyxl库">1.1. 引入openpyxl库</h2>

<div class="language-python highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs javascript"><span class="kn"><span class="hljs-keyword">import</span></span> <span class="nn">openpyxl</span>
</code></pre></div></div>

<h2 id="12-打开工作簿xlsx文件">1.2. 打开工作簿（xlsx）文件</h2>

<h3 id="121-全新创建工作簿">1.2.1. 全新创建工作簿</h3>

<div class="language-python highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs"><span class="n">wb</span> <span class="o">=</span> <span class="n">openpyxl</span><span class="o">.</span><span class="n">Workbook</span><span class="p">()</span>
</code></pre></div></div>

<p>得到一个wb对象，它就是我们要操作的工作簿文件的对象。</p>

<div class="language-python highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs ruby"><span class="o"><span class="hljs-prompt">&gt;&gt;</span>&gt;</span> <span class="n">wb</span>
<span class="o">&lt;</span><span class="n">openpyxl</span><span class="o">.</span><span class="n">workbook</span><span class="o">.</span><span class="n">workbook</span><span class="o">.</span><span class="n"><span class="hljs-constant">Workbook</span></span> <span class="nb">object</span> <span class="n">at</span> <span class="mh"><span class="hljs-number">0x0000000003543358</span></span><span class="o">&gt;</span>
</code></pre></div></div>

<h3 id="122-打开现有xlsx文件">1.2.2. 打开现有xlsx文件：</h3>

<p>wb = openpyxl.load_workbook(“test.xlsx”)</p>

<h2 id="13-保存wb对象到xlsx文件">1.3. 保存wb对象到xlsx文件</h2>
<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs cpp">wb.save(<span class="hljs-string">"test.xlsx"</span>)
</code></pre></div></div>
<blockquote>
  <p>文件名支持路径。</p>
</blockquote>



<p>以上语句获得的一个空的工作簿，默认含有一个名为“Sheet”的工作表页面。</p>

<h2 id="14--工作表sheet操作">1.4.  工作表(sheet)操作</h2>
<p>wb对象既是整个xlsx文件在内存中的存在形式，它一般由一个或多个sheet页面组成，我们对xlsx文件的操作，主要就是在sheet页面上操作；我们在Excel软件中，能对sheet页面进行什么操作，基本使用openpyxl也都能完成。</p>

<h3 id="141-工作表的创建和选定">1.4.1. 工作表的创建和选定</h3>
<p>wb对象创建后，默认含有一个默认的名为 Sheet 的 页面，可以使用active来得到它</p>

<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs ruby"><span class="hljs-prompt">&gt;&gt;</span>&gt; ws1 = wb.active
<span class="hljs-prompt">&gt;&gt;</span>&gt; ws1
&lt;<span class="hljs-constant">Worksheet</span> <span class="hljs-string">"Sheet"</span>&gt;
</code></pre></div></div>

<p>或使用名称来得到它：</p>
<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs javascript">ws1 = wb.get_sheet_by_name(<span class="hljs-string">'Sheet'</span>)  
</code></pre></div></div>
<p>或</p>
<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs cpp">ws1 = wb[<span class="hljs-string">"Sheet"</span>]
</code></pre></div></div>
<p>名称有中文要使用unicode</p>

<p>也可以使用工作表序号获得该页面： <code class="highlighter-rouge">ws1 = wb.worksheets[0]</code></p>
<blockquote>
  <p>注：序号从0开始</p>
</blockquote>

<p>可以使用 wb.create_sheet() 来创建新的sheet页面。
创建时可以同时关联一个变量。</p>
<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs">ws2 = wb.create_sheet()
</code></pre></div></div>
<p>如：</p>
<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs javascript"><span class="hljs-keyword">import</span> openpyxl
wb = openpyxl.Workbook()
# ws1 = wb.active
ws1 = wb.get_sheet_by_name(<span class="hljs-string">'Sheet'</span>)
ws2 = wb.create_sheet()
wb.create_sheet()
wb.create_sheet()
wb.create_sheet()
ws1[<span class="hljs-string">'A1'</span>] = <span class="hljs-string">"This is a test!"</span>
wb.save(<span class="hljs-string">"test.xlsx"</span>)
</code></pre></div></div>


<p>对于已有的工作表，我们也可以通过遍历名称、序号等方式拿到相应的对象地址。</p>

<p><code class="highlighter-rouge">wb.sheetnames</code> 是所有工作表sheet页面的名称列表。</p>

<h3 id="142-工作表命名">1.4.2. 工作表命名</h3>
<p>工作表sheet可以在创建时候直接指定名字，中文要使用unicode，也可以通过修改对象的title值，来修改名称。</p>

<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs javascript"><span class="hljs-keyword">import</span> openpyxl
wb = openpyxl.Workbook()
# ws1 = wb.active
ws1 = wb.get_sheet_by_name(<span class="hljs-string">'Sheet'</span>)
ws1.title = <span class="hljs-string">"Test"</span>
ws2 = wb.create_sheet(<span class="hljs-string">"ABC"</span>)
wb.create_sheet(u<span class="hljs-string">"中文表名"</span>)
ws1[<span class="hljs-string">'A1'</span>] = <span class="hljs-string">"哇嘎嘎"</span>
wb.save(<span class="hljs-string">"test.xlsx"</span>)
</code></pre></div></div>


<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs ruby"><span class="hljs-prompt">&gt;&gt;</span>&gt; print wb.sheetnames
[u<span class="hljs-string">'Test'</span>, u<span class="hljs-string">'ABC'</span>, u<span class="hljs-string">'\u4e2d\u6587\u8868\u540d'</span>]
</code></pre></div></div>

<h3 id="143-工作表的标签颜色修改">1.4.3. 工作表的标签颜色修改</h3>

<p>修改工作表的标签的颜色，等价于工作表标签右键菜单中“工作表标签颜色”功能：</p>



<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs cpp">ws1.sheet_properties.tabColor=<span class="hljs-string">"0000FF"</span>
</code></pre></div></div>

<p>颜色的值为颜色的RRGGBB格式。</p>

<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs javascript"><span class="hljs-keyword">import</span> openpyxl
wb = openpyxl.Workbook()
#ws1 = wb.active
ws1 = wb.get_sheet_by_name(<span class="hljs-string">'Sheet'</span>)
ws1.title = <span class="hljs-string">"Test"</span>
ws2 = wb.create_sheet(<span class="hljs-string">"ABC"</span>)
wb.create_sheet(u<span class="hljs-string">"中文表名"</span>)
ws1.sheet_properties.tabColor=<span class="hljs-string">"0000FF"</span>
ws2.sheet_properties.tabColor=<span class="hljs-string">"FF00FF"</span>
ws1[<span class="hljs-string">'A1'</span>] = <span class="hljs-string">"哇嘎嘎"</span>
ws1[<span class="hljs-string">'B2'</span>] = <span class="hljs-string">"嗯~ o(*￣▽￣*)o"</span>
wb.save(<span class="hljs-string">"test.xlsx"</span>)
</code></pre></div></div>

<p><img src="/images/posts/2017/08/openpyxl005.png" alt=""></p>

<h3 id="144-删除工作表">1.4.4. 删除工作表</h3>

<p>使用wb.remove_sheet()方法删除工作表</p>

<p>wb.remove_sheet(wb.get_sheet_by_name(“中文表名”))
wb.remove_sheet(ws2)</p>

<h2 id="15-单元格操作">1.5. 单元格操作</h2>

<p>获得一个工作表对象后，就可以工作表中的单元格进行操作。</p>

<h3 id="151-向单个单元格中写入值">1.5.1. 向单个单元格中写入值</h3>
<ul>
  <li>按照Excel单元格定位的传统方法，在指定单元格写入数据，之前例子中已有体现：
    <div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs javascript"> ws1[<span class="hljs-string">'A1'</span>] = <span class="hljs-string">"哇嘎嘎"</span>
 ws1[<span class="hljs-string">'B2'</span>] = <span class="hljs-string">"嗯~ o(*￣▽￣*)o"</span>
</code></pre></div>    </div>
  </li>
  <li>在指定行、列的单元格中写入数据：
 	<code class="highlighter-rouge">
 ws1.cell(row=3, column=2).value = "AAAAA"
 	</code></li>
</ul>

<p><img src="/images/posts/2017/08/openpyxl006.png" alt=""></p>

<h3 id="152-遍历方式向多个单元格中写入值">1.5.2. 遍历方式向多个单元格中写入值：</h3>

<p>有多种的方式可对多个单元格进行选定：</p>
<ul>
  <li>采用iter_cols方法，指定行列范围，对范围内的列进行遍历操作，然后再对列中的单元格操作；</li>
  <li>对应有iter_rows方法，与inter_cols区别，是遍历出所有行先；
    <div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs javascript"> <span class="hljs-keyword">for</span> col <span class="hljs-keyword">in</span> ws1.iter_cols(min_row=<span class="hljs-number">2</span>, min_col=<span class="hljs-number">2</span>, max_row=<span class="hljs-number">7</span>, max_col=<span class="hljs-number">6</span>):
     <span class="hljs-keyword">for</span> cell <span class="hljs-keyword">in</span> col:
</code></pre></div>    </div>
  </li>
  <li>采用传统的Excel多单元格选择的标记方法，区域选择单元格，遍历与iter_rows相同，返回每行，然后再遍历行中单元格；
    <div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs javascript"><span class="hljs-keyword">for</span> row <span class="hljs-keyword">in</span> ws2[<span class="hljs-string">"B2:F7"</span>]:
    <span class="hljs-keyword">for</span> cell <span class="hljs-keyword">in</span> row:
</code></pre></div>    </div>
  </li>
</ul>

<p>完整示例：</p>

<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs ruby">import openpyxl
wb = openpyxl.<span class="hljs-constant">Workbook</span>()
ws1 = wb.get_sheet_by_name(<span class="hljs-string">'Sheet'</span>)
ws1.title = <span class="hljs-string">"Test"</span>
i = <span class="hljs-number">0</span>
<span class="hljs-comment"># 遍历方式1：</span>
ws1[<span class="hljs-string">'A1'</span>] = <span class="hljs-string">"遍历方式1"</span>
<span class="hljs-keyword">for</span> col <span class="hljs-keyword">in</span> ws1.iter_cols(min_row=<span class="hljs-number">2</span>, min_col=<span class="hljs-number">2</span>, max_row=<span class="hljs-number">7</span>, max_col=<span class="hljs-number">6</span>)<span class="hljs-symbol">:</span>
    <span class="hljs-keyword">for</span> cell <span class="hljs-keyword">in</span> <span class="hljs-symbol">col:</span>
        cell.value = i
        i = i + <span class="hljs-number">1</span>
ws2 = wb.create_sheet(<span class="hljs-string">"Test2"</span>)
i = <span class="hljs-number">0</span>
<span class="hljs-comment"># 遍历方式2：</span>
ws2[<span class="hljs-string">'A1'</span>] = <span class="hljs-string">"遍历方式2"</span>
<span class="hljs-keyword">for</span> row <span class="hljs-keyword">in</span> ws2[<span class="hljs-string">"B2:F7"</span>]<span class="hljs-symbol">:</span>
    <span class="hljs-keyword">for</span> cell <span class="hljs-keyword">in</span> <span class="hljs-symbol">row:</span>
        cell.value = i
        i = i + <span class="hljs-number">1</span>
wb.save(<span class="hljs-string">"test.xlsx"</span>)
</code></pre></div></div>


<p>可以看出这两种遍历方式遍历的行列顺序是完全不一样的。</p>

<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs swift"><span class="hljs-keyword">import</span> openpyxl
wb = openpyxl.<span class="hljs-type">Workbook</span>()
ws1 = wb.get_sheet_by_name('<span class="hljs-type">Sheet'</span>)
ws1.title = <span class="hljs-string">"Test"</span>
i = <span class="hljs-number">0</span>
# 遍历方式<span class="hljs-number">1</span>：
ws1['<span class="hljs-type">A1'</span>] = <span class="hljs-string">"遍历方式1"</span>
<span class="hljs-built_in">print</span>(<span class="hljs-string">"遍历方式1"</span>)
j = <span class="hljs-number">1</span>
<span class="hljs-keyword">for</span> col <span class="hljs-keyword">in</span> ws1.iter_cols(min_row=<span class="hljs-number">2</span>, min_col=<span class="hljs-number">2</span>, max_row=<span class="hljs-number">7</span>, max_col=<span class="hljs-number">6</span>):
    <span class="hljs-built_in">print</span>(<span class="hljs-string">"Cols No."</span> + str(j) + <span class="hljs-string">":  "</span>, end=<span class="hljs-string">""</span>)
    <span class="hljs-built_in">print</span>(col)
    j = j + <span class="hljs-number">1</span>
    <span class="hljs-keyword">for</span> cell <span class="hljs-keyword">in</span> col:
        cell.value = i
        i = i + <span class="hljs-number">1</span>
ws2 = wb.create_sheet(<span class="hljs-string">"Test2"</span>)
i = <span class="hljs-number">0</span>
# 遍历方式<span class="hljs-number">2</span>：
ws2['<span class="hljs-type">A1'</span>] = <span class="hljs-string">"遍历方式2"</span>
<span class="hljs-built_in">print</span>(<span class="hljs-string">"遍历方式2"</span>)
j = <span class="hljs-number">1</span>
<span class="hljs-keyword">for</span> row <span class="hljs-keyword">in</span> ws2[<span class="hljs-string">"B2:F7"</span>]:
    <span class="hljs-built_in">print</span>(<span class="hljs-string">"Row No."</span> + str(j) + <span class="hljs-string">":  "</span>, end=<span class="hljs-string">""</span>)
    <span class="hljs-built_in">print</span>(row)
    j = j + <span class="hljs-number">1</span>
    <span class="hljs-keyword">for</span> cell <span class="hljs-keyword">in</span> row:
        cell.value = i
        i = i + <span class="hljs-number">1</span>
wb.save(<span class="hljs-string">"test.xlsx"</span>)
</code></pre></div></div>
<p>执行后控制台输出：</p>

<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs xml">遍历方式1
Cols No.1:  (<span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.B2</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.B3</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.B4</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.B5</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.B6</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.B7</span>&gt;</span>)
Cols No.2:  (<span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.C2</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.C3</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.C4</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.C5</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.C6</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.C7</span>&gt;</span>)
Cols No.3:  (<span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.D2</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.D3</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.D4</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.D5</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.D6</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.D7</span>&gt;</span>)
Cols No.4:  (<span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.E2</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.E3</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.E4</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.E5</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.E6</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.E7</span>&gt;</span>)
Cols No.5:  (<span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.F2</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.F3</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.F4</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.F5</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.F6</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test</span>'<span class="hljs-attribute">.F7</span>&gt;</span>)
遍历方式2
Row No.1:  (<span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.B2</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.C2</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.D2</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.E2</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.F2</span>&gt;</span>)
Row No.2:  (<span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.B3</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.C3</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.D3</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.E3</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.F3</span>&gt;</span>)
Row No.3:  (<span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.B4</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.C4</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.D4</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.E4</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.F4</span>&gt;</span>)
Row No.4:  (<span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.B5</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.C5</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.D5</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.E5</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.F5</span>&gt;</span>)
Row No.5:  (<span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.B6</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.C6</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.D6</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.E6</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.F6</span>&gt;</span>)
Row No.6:  (<span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.B7</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.C7</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.D7</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.E7</span>&gt;</span>, <span class="hljs-tag">&lt;<span class="hljs-title">Cell</span> '<span class="hljs-attribute">Test2</span>'<span class="hljs-attribute">.F7</span>&gt;</span>)
</code></pre></div></div>

<h3 id="153-按行追加数据">1.5.3. 按行追加数据</h3>

<p>采用工作表的append()方法，可以向工作表中按行追加数据：</p>

<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs ruby">import openpyxl
wb = openpyxl.<span class="hljs-constant">Workbook</span>()
ws1 = wb.get_sheet_by_name(<span class="hljs-string">'Sheet'</span>)
ws1.title = <span class="hljs-string">"Test"</span>
<span class="hljs-constant">DATA</span> = [
    [<span class="hljs-string">'第一天'</span>, <span class="hljs-number">123</span>, <span class="hljs-number">12</span>, <span class="hljs-number">123</span>, <span class="hljs-number">900</span>, <span class="hljs-number">231</span>, <span class="hljs-number">7</span>],
    [<span class="hljs-string">'第二天'</span>, <span class="hljs-number">13</span>, <span class="hljs-number">56</span>, <span class="hljs-number">3</span>, <span class="hljs-number">900</span>, <span class="hljs-number">231</span>, <span class="hljs-number">90</span>],
    [<span class="hljs-string">'第三天'</span>, <span class="hljs-number">216</span>, <span class="hljs-number">38</span>, <span class="hljs-number">37</span>, <span class="hljs-number">543</span>, <span class="hljs-number">55</span>, <span class="hljs-number">376</span>],
    [<span class="hljs-string">'第四天'</span>, <span class="hljs-number">89</span>, <span class="hljs-number">99</span>, <span class="hljs-number">88</span>, <span class="hljs-number">453</span>, <span class="hljs-number">87</span>, <span class="hljs-number">527</span>]
]
<span class="hljs-keyword">for</span> row <span class="hljs-keyword">in</span> <span class="hljs-constant">DATA</span><span class="hljs-symbol">:</span>
    ws1.append(row)
wb.save(<span class="hljs-string">"test.xlsx"</span>)
</code></pre></div></div>

<p><img src="/images/posts/2017/08/openpyxl009.png" alt=""></p>

<h3 id="154-加载已有文件读取数据">1.5.4. 加载已有文件，读取数据</h3>
<p>已有文件：test2.xlsx
<img src="/images/posts/2017/08/openpyxl010.png" alt=""></p>

<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs swift"><span class="hljs-keyword">import</span> openpyxl
wb = openpyxl.load_workbook(<span class="hljs-string">"test2.xlsx"</span>)
ws = wb.get_sheet_by_name('<span class="hljs-type">Sheet'</span>)
<span class="hljs-built_in">print</span>(<span class="hljs-string">"数据表最大行数："</span> + str(ws.max_row))
<span class="hljs-built_in">print</span>(<span class="hljs-string">"数据表最大列数："</span> + str(ws.max_column))
<span class="hljs-type">DATA</span> = []
# 遍历读取
<span class="hljs-keyword">for</span> row <span class="hljs-keyword">in</span> ws.iter_rows(min_col=<span class="hljs-number">1</span>, min_row=<span class="hljs-number">1</span>, max_row=ws.max_row, max_col=ws.max_column):
    <span class="hljs-type">ROW</span> = []
    <span class="hljs-keyword">for</span> cell <span class="hljs-keyword">in</span> row:
        <span class="hljs-type">ROW</span>.append(cell.value)
    <span class="hljs-type">DATA</span>.append(<span class="hljs-type">ROW</span>)
<span class="hljs-built_in">print</span>(<span class="hljs-string">"表中读取到的数据："</span>, end=<span class="hljs-string">""</span>)
<span class="hljs-built_in">print</span>(<span class="hljs-type">DATA</span>)
 
# 读取指定位置
<span class="hljs-type">B2</span> = ws['<span class="hljs-type">B2'</span>].value
<span class="hljs-built_in">print</span>(<span class="hljs-string">"B2 = "</span> + <span class="hljs-type">B2</span>)
</code></pre></div></div>
<p>执行结果：</p>
<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs javascript">数据表最大行数：<span class="hljs-number">2</span>
数据表最大列数：<span class="hljs-number">2</span>
表中读取到的数据：[[<span class="hljs-string">'测试2'</span>, None], [None, <span class="hljs-string">'B列2行'</span>]]
B2 = B列<span class="hljs-number">2</span>行
</code></pre></div></div>
<h3 id="155-使用公式">1.5.5. 使用公式</h3>

<p>可以直接在单元格中写入公式：</p>

<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs ruby">import openpyxl
wb = openpyxl.<span class="hljs-constant">Workbook</span>()
ws1 = wb.get_sheet_by_name(<span class="hljs-string">'Sheet'</span>)
ws1.title = <span class="hljs-string">"Test"</span>
<span class="hljs-constant">DATA</span> = [
    [<span class="hljs-string">'第一天'</span>, <span class="hljs-number">123</span>, <span class="hljs-number">12</span>, <span class="hljs-number">123</span>, <span class="hljs-number">900</span>, <span class="hljs-number">231</span>, <span class="hljs-number">7</span>],
    [<span class="hljs-string">'第二天'</span>, <span class="hljs-number">13</span>, <span class="hljs-number">56</span>, <span class="hljs-number">3</span>, <span class="hljs-number">900</span>, <span class="hljs-number">231</span>, <span class="hljs-number">90</span>],
    [<span class="hljs-string">'第三天'</span>, <span class="hljs-number">216</span>, <span class="hljs-number">38</span>, <span class="hljs-number">37</span>, <span class="hljs-number">543</span>, <span class="hljs-number">55</span>, <span class="hljs-number">376</span>],
    [<span class="hljs-string">'第四天'</span>, <span class="hljs-number">89</span>, <span class="hljs-number">99</span>, <span class="hljs-number">88</span>, <span class="hljs-number">453</span>, <span class="hljs-number">87</span>, <span class="hljs-number">527</span>]
]
ws1[<span class="hljs-string">'A1'</span>] = <span class="hljs-string">'这是一个测试用表格'</span>
<span class="hljs-keyword">for</span> row <span class="hljs-keyword">in</span> <span class="hljs-constant">DATA</span><span class="hljs-symbol">:</span>
    ws1.append(row)
ws1.append([<span class="hljs-string">'合计'</span>, <span class="hljs-string">'=sum(B2:B5)'</span>, <span class="hljs-string">'=sum(C2:C5)'</span>, <span class="hljs-string">'=sum(D2:D5)'</span>, <span class="hljs-string">'=sum(E2:E5)'</span>, <span class="hljs-string">'=sum(F2:F5)'</span>, <span class="hljs-string">'=sum(G2:G5)'</span>])
wb.save(<span class="hljs-string">"test.xlsx"</span>)
</code></pre></div></div>

<h1 id="2单元格样式设置">2.单元格样式设置</h1>

<p>单元格样式的控制，依赖openpyxl.style包，其中定义有样式需要的对象，引入样式相关：</p>
<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs javascript">from openpyxl.styles <span class="hljs-keyword">import</span> PatternFill, Font, Alignment, Border, Side
</code></pre></div></div>
<ul>
  <li>PatternFill  填充</li>
  <li>Font  字体</li>
  <li>Aignment 对齐</li>
  <li>Border 边框</li>
  <li>Side 边线</li>
</ul>

<p>以上五个基本可满足需要</p>

<p>基本用法是，将单元格对象的设置的属性赋为新的与默认不同的相应对象。</p>

<p>比如设置一个字体对象：</p>

<h2 id="21-字体设置">2.1. 字体设置</h2>
<p>在 <strong>1.5.5.</strong> 的示例中，保存文件前先设置一下标题字体：</p>

<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs ruby"><span class="hljs-comment"># 样式</span>
font = <span class="hljs-constant">Font</span>(size=<span class="hljs-number">14</span>, bold=<span class="hljs-constant">True</span>, name=<span class="hljs-string">'微软雅黑'</span>,  color=<span class="hljs-string">"FF0000"</span>)
ws1[<span class="hljs-string">'A1'</span>].font = font
wb.save(<span class="hljs-string">"test.xlsx"</span>)
</code></pre></div></div>



<h2 id="22-设置边框">2.2. 设置边框</h2>
<p>边框Border对象，创建时可指定边框各边的Side对象，根据之前的执行结果，默认都是无边框</p>

<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs ruby"><span class="hljs-comment"># 样式</span>
font = <span class="hljs-constant">Font</span>(size=<span class="hljs-number">14</span>, bold=<span class="hljs-constant">True</span>, name=<span class="hljs-string">'微软雅黑'</span>, color=<span class="hljs-string">"FF0000"</span>)
thin = <span class="hljs-constant">Side</span>(border_style=<span class="hljs-string">"thin"</span>, color=<span class="hljs-string">"0000FF"</span>)
border = <span class="hljs-constant">Border</span>(left=thin, right=thin, top=thin, bottom=thin)
ws1[<span class="hljs-string">'A1'</span>].font = font
ws1[<span class="hljs-string">'A1'</span>].border = border
<span class="hljs-keyword">for</span> row <span class="hljs-keyword">in</span> ws1[<span class="hljs-string">'A2:G6'</span>]<span class="hljs-symbol">:</span>
    <span class="hljs-keyword">for</span> cell <span class="hljs-keyword">in</span> <span class="hljs-symbol">row:</span>
        cell.border = border
wb.save(<span class="hljs-string">"test.xlsx"</span>)
</code></pre></div></div>



<h2 id="23-对齐设置">2.3. 对齐设置</h2>

<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs ruby"><span class="hljs-comment"># 对齐</span>
alignment = <span class="hljs-constant">Alignment</span>(horizontal=<span class="hljs-string">"center"</span>, vertical=<span class="hljs-string">"center"</span>, wrap_text=<span class="hljs-constant">True</span>)
ws1[<span class="hljs-string">'A1'</span>].alignment = alignment
</code></pre></div></div>
<ul>
  <li>wrap_text=True 打开自动换行</li>
</ul>



<h2 id="24-合并单元格">2.4. 合并单元格</h2>

<p>采用 <code class="highlighter-rouge">merge_cells</code> 方法合并单元格</p>

<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs ruby"><span class="hljs-comment"># 对齐</span>
alignment = <span class="hljs-constant">Alignment</span>(horizontal=<span class="hljs-string">"center"</span>, vertical=<span class="hljs-string">"center"</span>, wrap_text=<span class="hljs-constant">True</span>)
ws1[<span class="hljs-string">'A1'</span>].alignment = alignment
<span class="hljs-comment"># 合并单元格</span>
ws1.merge_cells(<span class="hljs-string">'A1:G1'</span>)
wb.save(<span class="hljs-string">"test.xlsx"</span>)
</code></pre></div></div>


<h2 id="25填充单元格">2.5.填充单元格</h2>

<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code class="hljs ruby"><span class="hljs-comment"># 填充</span>
fill = <span class="hljs-constant">PatternFill</span>(patternType=<span class="hljs-string">"solid"</span>, start_color=<span class="hljs-string">"33CCFF"</span>)
ws1[<span class="hljs-string">'A1'</span>].fill = fill
<span class="hljs-keyword">for</span> row <span class="hljs-keyword">in</span> ws1.iter_rows(min_row=ws1.max_row, max_col=ws1.max_column)<span class="hljs-symbol">:</span>
    <span class="hljs-keyword">for</span> cell <span class="hljs-keyword">in</span> <span class="hljs-symbol">row:</span>
        cell.fill = <span class="hljs-constant">PatternFill</span>(patternType=<span class="hljs-string">"solid"</span>, start_color=<span class="hljs-string">"0066FF"</span>)
        cell.font = <span class="hljs-constant">Font</span>(bold=<span class="hljs-constant">True</span>, color=<span class="hljs-string">"FFFFFF"</span>)
        cell.alignment = <span class="hljs-constant">Alignment</span>(horizontal=<span class="hljs-string">"center"</span>)
wb.save(<span class="hljs-string">"test.xlsx"</span>)
</code></pre></div></div>


文章转载自:http://yaoyan.me/2017/08/python-openpyxl1/