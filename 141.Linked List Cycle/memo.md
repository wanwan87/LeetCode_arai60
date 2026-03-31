# step1
- 問題読む。
  - posがどこのノードに接続されているかをあらわしている
  - headはリスト
    - なんでheadなんだろう-
  - リストの中でサイクルしていたらtrueを返して、それ以外はfalse

連結リスト、linked listは一般的にどこで使われるものなのだろうか？
posはパラメータとして与えられない？なんか問題が正しく読めてなさそう。
いったん翻訳。

headが与えられて、その中でサイクルしているかどうかはposがないと分からないけどposはパラメータとして与えられない。自分で呼び出せということ？そうするとposの値だけ見て0以上のときにtrueを返すとかできちゃうよね。
何見落としているんだろう

nextポインターを見ろってことか。コードチラ見。

例えば
3,2,0,-4のケースだとvalが-4のときにnextポインターは2を指すということかな。valって今の値という解釈でいいのか？

val next

3   2

2   0

0   -4

-4  2

2   0

headの中には同じ数字は持てないということ？？

同じ数字を盛った場合 nextでどこにコネクトするとかわからなくなっちゃうよね？

ここもなにか思い込みしている可能性高そう。

一度しか出ないのであれば、nextかvalが一度でも同じ値を出したらそこで止めるという方法...？

思いつかないし一回やってみるか。

自分はfor文すら見ないと書けないらしい。
関数のselfも実は理解があいまいなのでこの際調べておこう
https://qiita.com/free_jinji2020/items/93a45102995648ad06f1

```python
class Solution:

    def hasCycle(self, head: Optional[ListNode]) -> bool:

        for node in head:

            print(node)
```

エラー
TypeError: 'ListNode' object is not iterable
https://www.reddit.com/r/learnprogramming/comments/o1ea6h/listnode_object_not_iterable/?tl=ja

そもそも連結リストは配列とは違うんだ
そうなるとvalとnextってなんだろう
https://zenn.dev/daichi09167/articles/6df8fbe13c671f
クラスのフィールド

```python
class Solution:

    def hasCycle(self, head: Optional[ListNode]) -> bool:

        print(head.val)

        while head:

            print(head.val)

            if head.val == 3:

                break

        print("End")
```

あれ動作確認でwhileに入れてprintしたら headの値が１になった。なんかListnodeの触り方が良くわかっていないところで詰まっている。printしても期待している[3,2,0,-4]が出てこない。


21:24(1時間ぐらい経過)
問題と関係所を色々調べたが、ちょっと分からないので、一旦解答とほかの人のPRを眺めてみる。


https://github.com/olsen-blue/Arai60/pull/1/changes
自分も初見じゃわからなかったから勝手に親近感。
なるほど、headが空または要素が１つしかない場合はそもそもサイクル内から即falseにしちゃうえばいいんだ
スローポインタとファストポインタという考え方らしい。
この方のstep1はC++かこれ。　「->」　この矢印なんだとおもってみてた。step2から見る。
slowとfaseにheadを入れている。
whileがfast != Noneかつ　fase.next != None:
これってfastがNoneの条件式いるのかな？
3 2 0 -4
上のケースだと要らないけど、そもそもリストが空の可能性があるから要るか。

C++と同じ解法でslowは１個ずつ、fastは２個進めている
slow = =fast　になったらture
感覚的には分かる。具体的に考えてみると
1ループ：slow 3 fast 2
２ループ：slow 2 fast -4
３ループ：slow 0 fast None？範囲外？
自分の考え方間違えてそう。やってることというかやりたいことはおそらく
3ループ：slow 0 fast 2
4ループ：slow -4 fast -4　でtrue
ということ？
ただnextの考え方が良く分からない。ちゃんとループするのか？

その前にこの考え方の前提としてslowがリストの終端にすすむまでのあいだに、fitstは必ずどこかでslowに出会うらしい。これも感覚的には分かるが。。
firstが追いつかなければいけないslowとの距離は最大でn-1とのこと。てかfirstじゃなくてfastか。
10のノードの場合、最大9個離れている...？fastはnステップでslowに追いつける...？
https://mhiro216.hatenablog.com/entry/2019/08/17/103553
記事読んで2つ思ったが
①そもそも上のループの考え方違うのか？
slow.next
fast.next.next⇒これが2と-4を行ったり来たりしちゃうよね。1要素ずつ距離を詰めると言っているので、なんか誤って考えていそう。
②じゃあ、上の考え方があってるとして、例えば3ループ目のタイミングで
slow 0 fast 2
fastとslowの距離は1か2と考えた時に...と思ったけど
やっぱり今回の場合n=4で最大距離は3　fastは4ステップで必ずslownに追いつける、、だから結果には合うけど、違和感あり。
https://www.youtube.com/watch?v=kOhQ5bfpq2I
youtubeみたら元の考えで合っていた。
ちゃんとループするのかと、なぜこれでうまくいくのかがあまりしっくり来ていない。
と思ったけどよく考えたら2と-4を行ったり来たりじゃないわ。
fast 2 -> -4 ->0 ->2... になるわ。3ループ目でぶつかるのか。そうすると絶対どこでぶつかるか？
ちょっと紙に書きたいな。
3 2 0 -4 5
1ループ：slow 3 fast 2
２ループ：slow 2 fast -4
3ループ：slow 0 fast 2
4ループ：slow -4 fast-4

3 2 0 -4 5 6
1ループ：slow 3 fast 2
２ループ：slow 2 fast -4
3ループ：slow 0 fast 6
4ループ：slow -4 fast 0
5ループ：slow 5 fast 5

一旦PR見に行こう

https://github.com/Nbotter/leetcode-easy-swe-practice/pull/11/files
考え方が似てる
フロイドの循環検出法というらしい
https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.2k4z0wt6ytf9
コメント集やPRを眺めてみると、あまりこの解法はソフトウェアエンジニアの中では一般的ではないそう。


https://qiita.com/vulptex/items/090e66477e2333564807

https://github.com/hiratasec/leetcode/pull/1/files

あまり考えずに今まで最終行は空けたほうがいいんだ(lintで修整されるなあ)と思ってたけどちゃんと理由があった
https://github.com/aiueoriku/LeetCode/pull/7
>Python でアドレスといった場合、 Python のインタープリターの内部でオブジェクトを
>格納しているメモリアドレスを指すように思います。
pythonインタープリタってなんだろう
https://xtech.nikkei.com/atcl/nxt/column/18/02646/112200001/

>　Pythonインタプリタの仕組みを詳しく知るには、Pythonインタプリタのソースコードを読み解くことが有効です。標準のPythonインタプリタ、つまり、皆さんが普段利用しているPythonインタプリタは「CPython」とも呼ばれ、そのソースコードはC言語で書かれています。
ほえ～C言語で書かれてるんだ...後で見てようかな

電車の中と昼休憩の中でいろんなPRみた。メモ残しておけば良かった。
setでの考え方があるらしい。
https://utokyo-ipp.github.io/appendix/2-set.html
https://www.rstone-jp.com/column/145109/
>Pythonのsetは、重複のない要素を格納できるデータ構造で、順序がなく、重複を自動排除します。

最初のほうに考えていたもので大体発想はあっていたっぽい
> 一度しか出ないのであれば、nextかvalが一度でも同じ値を出したらそこで止めるという方法...？

javaだった、pythonでの回答が固まったら見てみよう。
https://github.com/goto-untrapped/Arai60/pull/21/changes
結構かきっぷりはpythonと同じだった


https://zenn.dev/daichi09167/articles/6df8fbe13c671f
てかそもそも
head.valは数値だけど、head.nextは次のノードへの参照が入っているのか。
勝手に配列で渡されて、head.nextも数値が入ると勘違いしていたかも。

というかstep1,2を混在していた。いったんsetの書き方をまねながら処理を追ってみる

```python
# Definition for singly-linked list.

# class ListNode:

#     def __init__(self, x):

#         self.val = x

#         self.next = None

  

class Solution:

    def hasCycle(self, head: Optional[ListNode]) -> bool:

        visited = set() #空のsetを作成

        current_node = head #current nodeにheadを入れる。これ入れる必要はあるのかな？あとで試してみる。

        while current_node is not None: #再度まで行ったらNoneかな？

            if current_node in visited: # current_nodeがvisitedの中に含まれていたら

                return true

            visited.add(current_node)

            current_node = current_node.next

            print(current_node)

            print(current_node.next)

        return false


Runtime Error

NameError: name 'true' is not defined ^^^^ return true Line 13 in hasCycle (Solution.py) ret = Solution().hasCycle(param_1) Line 44 in _driver (Solution.py) ~~~~~~~^^ _driver() Line 57 in <module> (Solution.py)


```

True,Flaseに直した。

```python
AttributeError: 'NoneType' object has no attribute 'val'


# Definition for singly-linked list.

# class ListNode:

#     def __init__(self, x):

#         self.val = x

#         self.next = None

  

class Solution:

    def hasCycle(self, head: Optional[ListNode]) -> bool:

        visited = set() #空のsetを作成

        current_node = head #current nodeにheadを入れる。これ入れる必要はあるのかな？あとで試してみる。

        while current_node is not None: #再度まで行ったらNoneかな？

  

            if current_node in visited: # current_nodeがvisitedの中に含まれていたら

                return True

            visited.add(current_node)

            current_node = current_node.next

            print(current_node.val)

            print(current_node.next)

        return False


```

current_nodeがループを終えてNoneになっているかと思ったけど、Noneだったらwhileの条件で止まるから違うかと思ったけどcurrent_nodeにcurrent_node.next入れた後だった。。
のでprintの位置を変えよう


```python
# Definition for singly-linked list.

# class ListNode:

#     def __init__(self, x):

#         self.val = x

#         self.next = None

  

class Solution:

    def hasCycle(self, head: Optional[ListNode]) -> bool:

        visited = set() #空のsetを作成

        current_node = head #current nodeにheadを入れる。これ入れる必要はあるのかな？あとで試してみる。

        while current_node is not None: #再度まで行ったらNoneかな？

            print(current_node.val)

            print(current_node.next)

            if current_node in visited: # current_nodeがvisitedの中に含まれていたら

                return True

            visited.add(current_node)

            current_node = current_node.next

        return False
```

```
3 Error - Found cycle in the ListNode 2 Error - Found cycle in the ListNode 0 Error - Found cycle in the ListNode -4 Error - Found cycle in the ListNode 2 Error - Found cycle in the ListNode
```

パスするようになったので、何も見ないで書いてみる。というかslowとfastで書くの忘れてたけど、時間かけすぎたので一旦setの方法で練習。
# step2 step3

まだチラ見しながらのトライ。
```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        visited = set()
        node = head
        while node is not None: 
            if node in visited:
                return True
            visited.add(node)
            node = node.next
        return False
```



```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        visited = set()
        while head is not None:
            if head in visited:
                return True
            visited.add(head)
            head = head.next
        return False
```
上のコードのようにheadのままでもいいのか？けどnextした時点でheadじゃなくなるからさすがにcurrent_nodeやnodeに入れた方がよさそう。
current_nodeのほうがやっぱり現在感が出ていいかな？


```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        visited = set()
        current_node = head

        while current_node is not None:
            if current_node in visited:
                return True
            visited.add(current_node)
            current_node = current_node.next
        return False
```
```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        visited = set()
        current_node = head
        while current_node is not None:
            if current_node in visited:
                return True
            visited.add(current_node)
            current_node = current_node.next
        return False
```
```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        visited = set()
        current_node = head
        while current_node is not None:
            if current_node in visited:
                return True
            visited.add(current_node)
            current_node = current_node.next
        return False

```
大体2，3分で書けるようになったので終了。
