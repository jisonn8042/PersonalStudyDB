<h1>HTML_Guide</h1>

<h2>目次</h2>

<h3>Html Entities</h3>
<ul>
    <li>HTML Entitiesとは</li>
    <li>使い方</li>
    <li>仕組み</li>
</ul>

</br>

<h2>Html Entities</h2>

<h3>Html Entitiesとは</h3>

<p>HTMLを作成する時、HTML上特別の意味を持っていてキーボードで打った時画面上には表示されない文字があります。</p>

<p>例え上では空白は一個以上だとブラウザはそれを一つの空白として扱います。</p>

<p>その時Html Entitiesを使うとそれを表現できます。</p>

</br>

<h3>使い方</h3>

<p>全てのEntityは＆で始まり；で終わります。</p>

<h4>主要Entities</h4>

<table>
    <tr>
        <th>文字</th>
        <th>Entity</th>
        <th>意味</th>
    </tr>
    <tr>
        <td>&amp;</td>
        <td>
            <code>
                &amp;amp;
            </code>
        </td>
        <td>AND</td>
    </tr>
    <tr>
        <td>&lt;</td>
        <td>
            <code>
                &amp;lt;
            </code>
        </td>
        <td>Less than</td>
    </tr>
    <tr>
        <td>&gt;</td>
        <td>
            <code>
                &amp;gt;
            </code>
        </td>
        <td>Greater than</td>
    </tr>
    <tr>
        <td>&quot;</td>
        <td>
            <code>
                &amp;quot;
            </code>
        </td>
        <td>Double quote</td>
    </tr>
    <tr>
        <td>&apos;</td>
        <td>
            <code>
                &amp;apos;
            </code>
        </td>
        <td>Single quote</td>
    </tr>
</table>

</br>

<h3>仕組み</h3>

<h4>HTML解析（Parsing）</h4>

<p>HTML解析時、特定機能を持つ文字を文字列として扱えるようにHTMLEntitiesを規定しています。</p>

<p>空白を例として説明すると</p>

<p>HTMLを作成するとき、コードの構造を把握しやすく表すために行を変えたり、複数の空白を入力する場合があります。</p>

<p>画面に表す時、把握しやすくする為入れた行や複数の空白をそのまま表示すると内容が意図と違く見える場合があります。</p>

<p>その状況を避ける為、ブラウザがそれを1つの空白として扱う仕組みになっています。</p>

<p>でも、実際にその文字を文字列として扱って欲しい場合もあります。</p>

<p>その時、HTMLEntitiesを使うと特定機能を持っている文字でも文字列として扱うようにすることができますs。</p>

<h4>Entity処理</h4>

<p>ブラウザはHTMLを解析する時、&で始まり；で終わる文字列をHTMLEntitiesとして認識し、その文字列に定義されているUnicode文字に置き換えされます。</p>