#### [原文地址](hhttp://khanlou.com/2017/04/safety-in-swift/) 翻译：[DeveloperLx](http://weibo.com/DeveloperLx)


<div class="blogpost">
    <div class="post-meta">
        <h2 class="post-title" itemprop="name headline">
            Swift中的安全特性
        </h2>
    </div>
    <p>
        Swift通常被描述为一个安全的语言。的确，在
        <a href="https://swift.org/">
            swift.org
        </a>
        的
        <a href="https://swift.org/about/">
            关于
        </a>
        页面中，提到了：
    </p>
    <blockquote>
        <p>
            Swift是一种通用的编程语言，采用现代化的方式来构建安全、高性能和软件设计模式来构建。
        </p>
    </blockquote>
    <p>
        and
    </p>
    <blockquote>
        <ul>
            <li>
                <p>
                    <strong>
                        安全。
                    </strong>
                    写代码最显而易见的，是应当表现在安全的方式下。未定义的行为是安全的敌人，开发者的错误应当要在产品上线前被捕捉到。选择了安全，意味着swift会显得很严格，但我们认为这样的清晰长期来看是可以节省时间的。
                </p>
            </li>
            <li>
                <p>
                    <strong>
                        快速。
                    </strong>
                    Swift旨在代替基于C的语言（C，C++，和Objective-C）。因此，对于大多数的任务，Swift必须在性能上可以与之相比。且性能必须是可预测且稳定的，不仅仅只那种在短期内还需要清理的快速。很多的语言都具有着新奇的特性 – 但很少会有快速这一条。
                </p>
            </li>
            <li>
                <p>
                    <strong>
                        表现力。
                    </strong>
                    表现力。Swift获益于计算机科学几十年的进步，提供了现代的特性开发者所期望的，乐于去使用的语法。但Swift从不止步。我们将监控语言的进步，并拥抱有效的部分，持续地改进使Swift变得更好。
                </p>
            </li>
        </ul>
    </blockquote>
    <p>
        例如，当用到
        <code class="highlighter-rouge">
            Optional
        </code>
        类型的对象时，很明显它是如何增强Swift的安全性的。在之前，你是无法知道哪个变量是可以为空，哪个是不可以的。有了这个可为空的信息后，你就必须显式地处理为空时的情形了。当使用这些“nullable”类型时，你可以选择崩溃掉，通常使用一个“
        <code class="highlighter-rouge">
            !
        </code>        
        ”的操作符。“安全”在这里的含义是很明显的。它是你可以选择去解开的“安全带”，但风险要由你自己承担。
    </p>
    <p>
        然而，在其它的情形下，似乎是缺乏安全性的。让我们来看一个例子。如果我们有一个字典，通过给定的键来获取相应的值，将返回可选的类型：
    </p>
    <pre class="highlight"><code>let person: [String: String] = //... type(of: person["name"]) // =&gt;Optional&lt;String&gt;</code></pre>
    <p>
        但我们在数组上做相同的事的时候，我们得到的却并非是可选的类型：
    </p>
    <pre class="highlight"><code>let users: [User] = //... type(of: users[0]) // =&gt; User</code></pre>
    <p>
        为何如此呢？这个数组可能是空的。如果
        <code class="highlighter-rouge">
            users
        </code>
        这个数组是空的，那程序就没有选择，只能崩溃。这很难是安全的。我可不想被老板罚钱！
    </p>
    <p>
        嗯，好吧。Swift有一个开放的开发进程。或许我们可以建议一项改动到
        <a href="">
            <code class="highlighter-rouge">
                swift evolution
            </code>
        </a>
        的mailing列表中，然后-
    </p>
    <p>
        不，这也不行！在
        <code class="highlighter-rouge">
            swift-evolution
        </code>
        这个GitHub repo，
        <a href="https://github.com/apple/swift-evolution/blob/master/commonly_proposed.md">
            “commonly rejected” proposals
        </a>
        提案的页面中，声明了他们是不会接受这样的变化的：
    </p>
    <blockquote>
        <ul>
            <li>
                使 
                <code class="highlighter-rouge">
                    Array&lt;T&gt;
                </code>
                的下标访问方式返回
                <code class="highlighter-rouge">
                    T?
                </code>
                或
                <code class="highlighter-rouge">
                    T!
                </code>
                而不是
                <code class="highlighter-rouge">
                    T
                </code>
                ：当前数组的这个行为是
                <a href="https://lists.swift.org/pipermail/swift-evolution/Week-of-Mon-20151214/002446.html">
                    有意图的
                </a>
                ，它精确地反映了数组越界是逻辑错误的这一事实。改变当前的这一行为会使
                <code class="highlighter-rouge">
                    Array
                </code>
                的访问速度降低到一个不可接受的程度。这个话题之前已被提到过了                
                <a href="https://lists.swift.org/pipermail/swift-evolution/Week-of-Mon-20151214/002425.html">
                    多
                </a>
                次，但确实是不大可能被接受的。
            </li>
        </ul>
    </blockquote>
    <p>
        给出了什么？它阐明的原因是，在这种情况下，速度太重要了。但回到上面的关于页面，对于这个语言的描述中，“安全”是被列为“快速”之前的。安全难道不应该比速度更为重要么？
    </p>
    <p>
        这里有一个基本的争论，解决的要点则在于对“安全”一词的定义。尽管通常对于“安全”的理解是更多或更少的“不要崩溃”，但Swift的核心成员常常会使用相同的词语来表示“永远不会无意识地访问到错误的内存”。
    </p>
    <p>
        在这种含义之下，Swift的
        <code class="highlighter-rouge">
            Array
        </code>
        下标就是“安全”的了。它将永远都不会在内存中返回超越数组本身所分配的范围外的数据。它会在给出你本不应该包含的内存中的句柄前崩溃掉。相同的方式，可选类型避免了现有的整个类型的错误（null解除引用），而这个行为则防止了不同类型错误的存在（缓冲溢出）。
    </p>
    <p>
        你可以听到Chris Lattner的区分，
        <a href="https://overcast.fm/+CdTE-_oY/24:37">
            在他被ATP采访中的24分39秒
        </a>
        ：
    </p>
    <blockquote>
        <p>
            我们说，在社区破坏的成本方面，唯一可以讲得通的方式，是如果我们把它变成为一种安全的编程语言：不是“你不会有bug”，而是在内存方面的“安全”， 同时提供高性能，以及推动编程模型向前发展。
        </p>
    </blockquote>
    <p>
        或许“内存安全”是一个比“安全”更好的术语。这个想法是，尽管一些应用的编程者会更喜欢返回可选的类型，而不是捕获超出范围的数组访问，但每个人都可以同意，他们宁愿让程序崩溃，也不要让程序带有包含无效数据的变量，或可能会在缓冲溢出的攻击中被利用的变量继续下去。
    </p>
    <p>
        尽管第二个权衡（崩溃而不是允许缓冲溢出）可靠看起来很明显，一些语言
        <em>
            并不能
        </em>
        给你这样的包装。在C语言中，访问数据越界会得到未知的行为，意味着任何事都可能发生，具体则依赖于你所使用的编译器的实现。特别是当编程者可以快速被告知它们犯了一个错误的情况下，Swift的团队则表示了，他们认为这是一个可以接受崩溃的地方（一直都是！），而不是返回一个可选类型，并明确地代替返回垃圾内存。
    </p>
    <p>
        使用这个“安全”的定义也阐明了“不安全”API的设计意图。因为他们直接在内存中“鬼混”（muck about），编程者自己就必须特别小心，来
        <em>
            确保
        </em>
        它永远都不会访问无效的内存。这是非常困难的，甚至专家也会出错。关于这个话题的有趣的读物，请查看
        <a href="https://www.cocoawithlove.com/blog/2016/02/16/use_it_or_lose_it_why_safe_c_is_sometimes_unsafe_swift.html">
            Matt Gallagher的帖子
        </a>
        ：以安全的方式将C连接到Swift。
    </p>
    <p>
        Swift和核心团队对于“安全”的定义和你可能并不是100%相同的，但他们确实可以防止很多类型的错误，以便像你这样的编程者无需每天都考虑它们。通常，将它们使用的“安全”替换为“内存安全”，可以帮助理解他们的意图。
    </p>
</div>
