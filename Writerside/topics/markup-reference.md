# Reference

<tldr>
    <p>tldr block</p>
</tldr>

<table>
    <tr><td>MarkUp</td><td>Result</td></tr>
    <tr><td>
        <code-block lang="markup">
            <![CDATA[<format style="bold" color="Red">Hello, World!</format>]]>
        </code-block>
    </td><td>
        <format style="bold" color="Red">Hello, world!</format>
    </td></tr>
</table>


<tip>
    <p>tip</p>
</tip>

<note><p>note</p></note>

<warning><p>warning</p></warning>

[Link Text](https://ckgod.github.io/TDD)

링크 타겟에 id, 파일 등으로 이동도 가능

<shortcut>Cmd</shortcut>+<shortcut>N</shortcut> 입력 시 표, 이미지, 링크 생성 가능


|  |   |   |
|--|---|---|
|  |   |   |
{style = "both"}

style 종류: header-row, header-column, both, none

<tabs>
    <tab title="tab1">
        <p>tab1 내용</p>
    </tab>
    <tab title="tab2">
        <p>tab2 내용</p>
    </tab>
</tabs>

```Kotlin
data class Chako(
    val name: String? = null
)
```

<compare type="left-right" first-title="이전" second-title="이후">
<code-block lang="kotlin">
data class Chako(
    val name: String? = null
)
</code-block>
<code-block lang="kotlin">
data class Chako(
    val name: String = "고창국"
)
</code-block>
</compare>


