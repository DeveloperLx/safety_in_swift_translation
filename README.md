<div class="blogpost">
    <div class="post-meta">
        <time datetime="2017-04-05T00:00:00-04:00" itemprop="datePublished" class="timestamp">
            2017年4月5日
        </time>
        <h2 class="post-title" itemprop="name headline">
            Swift中的安全特性
        </h2>
    </div>
    <p>
        Swift is commonly described as a “safe” language. Indeed, the
        <a href="https://swift.org/about/">
            About
        </a>
        page of
        <a href="https://swift.org/">
            swift.org
        </a>
        says:
    </p>
    <blockquote>
        <p>
            Swift is a general-purpose programming language built using a modern approach
            to safety, performance, and software design patterns.
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
                        Safe.
                    </strong>
                    The most obvious way to write code should also behave in a safe manner.
                    Undefined behavior is the enemy of safety, and developer mistakes should
                    be caught before software is in production. Opting for safety sometimes
                    means Swift will feel strict, but we believe that clarity saves time in
                    the long run.
                </p>
            </li>
            <li>
                <p>
                    <strong>
                        Fast.
                    </strong>
                    Swift is intended as a replacement for C-based languages (C, C++, and
                    Objective-C). As such, Swift must be comparable to those languages in performance
                    for most tasks. Performance must also be predictable and consistent, not
                    just fast in short bursts that require clean-up later. There are lots of
                    languages with novel features — being fast is rare.
                </p>
            </li>
            <li>
                <p>
                    <strong>
                        Expressive.
                    </strong>
                    Swift benefits from decades of advancement in computer science to offer
                    syntax that is a joy to use, with modern features developers expect. But
                    Swift is never done. We will monitor language advancements and embrace
                    what works, continually evolving to make Swift even better.
                </p>
            </li>
        </ul>
    </blockquote>
    <p>
        For example, when working with things like the
        <code class="highlighter-rouge">
            Optional
        </code>
        type, its clear how Swift increases safety. Before, you would never know
        which variables could be null and which couldn’t. With this new nullability
        information, you’re forced to handle the null case explicitly. When working
        with these “nullable” types, you can opt to crash, usually using an operator
        that involves an exclamation point (
        <code class="highlighter-rouge">
            !
        </code>
        ). What is meant by safety here is apparent. It’s a seatbelt that you
        can choose to unbuckle, at your own risk.
    </p>
    <p>
        However, in other cases, the safety seems to be lacking. Let’s take a
        look at an example. If we have a dictionary, grabbing the value for some
        given key returns an optional:
    </p>
    <div class="highlighter-rouge">
        <pre class="highlight">
            <code>
                let person: [String: String] = //... type(of: person["name"]) // =&gt;
                Optional&lt;String&gt;
            </code>
        </pre>
    </div>
    <p>
        But if we do the same with an array, we don’t get an optional:
    </p>
    <div class="highlighter-rouge">
        <pre class="highlight">
            <code>
                let users: [User] = //... type(of: users[0]) // =&gt; User
            </code>
        </pre>
    </div>
    <p>
        Why not? The array could be empty. If the
        <code class="highlighter-rouge">
            users
        </code>
        array were empty, the program would have no real option but to crash.
        That hardly seems safe. I want my money back!
    </p>
    <p>
        Well, okay. Swift has an open development process. Perhaps we can suggest
        a change to the
        <a href="">
            <code class="highlighter-rouge">
                swift evolution
            </code>
        </a>
        mailing list, and—
    </p>
    <p>
        Nope, that won’t work either. The
        <a href="https://github.com/apple/swift-evolution/blob/master/commonly_proposed.md">
            “commonly rejected” proposals
        </a>
        page in the
        <code class="highlighter-rouge">
            swift-evolution
        </code>
        GitHub repo says that they won’t accept such a change:
    </p>
    <blockquote>
        <ul>
            <li>
                Make
                <code class="highlighter-rouge">
                    Array&lt;T&gt;
                </code>
                subscript access return
                <code class="highlighter-rouge">
                    T?
                </code>
                or
                <code class="highlighter-rouge">
                    T!
                </code>
                instead of
                <code class="highlighter-rouge">
                    T
                </code>
                : The current array behavior is
                <a href="https://lists.swift.org/pipermail/swift-evolution/Week-of-Mon-20151214/002446.html">
                    intentional
                </a>
                , as it accurately reflects the fact that out-of-bounds array access is
                a logic error. Changing the current behavior would slow
                <code class="highlighter-rouge">
                    Array
                </code>
                accesses to an unacceptable degree. This topic has come up
                <a href="https://lists.swift.org/pipermail/swift-evolution/Week-of-Mon-20151214/002425.html">
                    multiple
                </a>
                times before but is very unlikely to be accepted.
            </li>
        </ul>
    </blockquote>
    <p>
        What gives? The stated reason is that speed is too important in this particular
        case. But referring back to the About page linked above, “safe” is listed
        as a description of the language before “fast”. Shouldn’t safety be more
        important than speed?
    </p>
    <p>
        There is a fundamental contention here, and the solution lies in the definitions
        of the word “safe”. While the common understanding of “safe” is more or
        less “doesn’t crash”, the Swift core members usually use the same word
        to mean “will never access incorrect memory unintentionally”.
    </p>
    <p>
        In this way, Swift’s
        <code class="highlighter-rouge">
            Array
        </code>
        subscript is “safe”. It’ll never return data in memory beyond the bounds
        allocated for the array itself. It will crash before giving you a handle
        on memory that doesn’t contain what it should. In the same way that the
        Optional type prevents whole classes of bugs (null dereferencing) from
        existing, this behavior prevents a different class of bugs (buffer overflows)
        from existing.
    </p>
    <p>
        You can hear Chris Lattner make this distinction
        <a href="https://overcast.fm/+CdTE-_oY/24:37">
            at 24:39 in his interview with ATP
        </a>
        :
    </p>
    <blockquote>
        <p>
            We said the only way that this can make sense in terms of the cost of
            the disruption to the community is if we make it a safe programming language:
            not “safe” as in “you can have no bugs,” but “safe” in terms of memory
            safety while also providing high performance and moving the programming
            model forward.
        </p>
    </blockquote>
    <p>
        Perhaps “memory-safe” is a better term than just “safe”. The idea is that,
        while some application programmers might prefer getting back an optional
        instead of trapping on out-of-bounds-array access, everyone can agree that
        they’d prefer to crash their program rather than let it continue with a
        variable that contains invalid data, a variable that could potentially
        be exploited in a buffer overflow attack.
    </p>
    <p>
        While this second tradeoff (crashing instead of allowing buffer overflows)
        may seem obvious, some languages
        <em>
            don’t
        </em>
        give you this guarantee. In C, accessing an array out-of-bounds gives
        you undefined behavior, meaning that anything could happen, depending on
        the implementation of the compiler that you were using. Especially in cases
        when the programmer can quickly tell that they made a mistake, such as
        with out-of-bounds array access, the Swift team has shown that they feel
        like this is an acceptable place to (consistently!) crash, instead of returning
        an optional, and definitely instead of returning junk memory.
    </p>
    <p>
        Using this definition of “safe” also clarifies what the “unsafe” APIs
        are designed for. Because they muck about in memory directly, the programmer
        herself has to take special care to
        <em>
            ensure
        </em>
        that she’ll never allow access to invalid memory. This is extremely hard,
        and even experts get it wrong. For an interesting read on this topic, check
        out
        <a href="https://www.cocoawithlove.com/blog/2016/02/16/use_it_or_lose_it_why_safe_c_is_sometimes_unsafe_swift.html">
            Matt Gallagher’s post
        </a>
        on bridging C to Swift in a safe fashion.
    </p>
    <p>
        Swift and the core team’s definition of “safe” may not line up 100% with
        yours, but they do prevent classes of bugs so that programmers like you
        don’t have to think about them day-to-day. It can often help to replace
        their usage of “safe” with “memory safe” to help understand what they mean.
    </p>
</div>