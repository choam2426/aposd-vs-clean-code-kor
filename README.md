## Introductions

**JOHN:**

Hi (Uncle) Bob! You and I have each written books on software design.
We agree on some things, but there are some pretty big differences of
opinion between my recent book *A Philosophy of Software Design*
(herafter "APOSD") and your classic book *Clean Code*. Thanks for
agreeing to discuss those differences here.

**UB:**

My pleasure John.  Before we begin let me say that I've read through your book and I found it very enjoyable, and full of valuable insights.  There are some things I disagree with you on, such as TDD, and Abstraction-First incrementalism, but overall I enjoyed it a lot.

**JOHN:**

I'd like to discuss three topics with you: method length, comments,
and test-driven development. But before getting into these,
let's start by comparing overall philosophies. When you hear about a
new idea related to software design, how do you decide whether or not
to endorse that idea?

I'll go first. For me, the fundamental goal of software design is
to make it easy to understand and modify the system. I use the term
"complexity" to refer to things that make it hard to understand and
modify a system. The most important contributors
to complexity relate to information:

* How much information must a developer have in their head in order to carry out a task?
* How accessible and obvious is the information that the developer needs?

The more information a developer needs to have, the harder it will be
for them to work on the system. Things get even worse if the required
information isn't obvious. The worst case is when there is a crucial
piece of information hidden in some far-away piece of code
that the developer has never heard of.

When I'm evaluating an idea related to software design, I ask whether
it will reduce complexity. This usually means either reducing the amount
of information a developer has to know, or making the required information
more obvious.

Now over to you: are there general principles that you use when deciding
which ideas to endorse?

**UB:**

I agree with your approach. A discipline or technique should make the job of programmers easier. I would add that the programmer we want to help most is not the author.  The programmer whose job we want to make easier is the programmer who must read and understand the code written by others (or by themself a week later).  Programmers spend far more hours reading code than writing code, so the activity we want to ease is that of reading.

**JOHN:**
I agree: ease of reading is much more important for code than ease of writing.

## Method Length

**JOHN:**

Our first area of disagreement is method length.
On page 34 of *Clean Code* you say "The first rule of functions is that
they should be small. The second rule of functions is that
*they should be smaller than that*." Later on, you say "Functions
should hardly ever be 20 lines long" and suggest that functions
should be "just two, three, or four lines long". On page 35, you
say "Blocks within `if` statements, `else` statements, `while` statements,
and so on should be one line long. Probably that line should be a function
call." I couldn't find anything in *Clean Code* to suggest that a function
could ever be too short.

I agree that dividing up code into relatively small units ("modular design")
is one of the most important ways to reduce the amount of information a
programmer has to keep in their mind at once. The idea, of course, is to take a
complex chunk of functionality and encapsulate it in a separate function
with a simple interface. Developers can then harness the functionality
of the function (or read code that invokes the function) without learning
the details of how the function is implemented; they only need to learn its
interface. The best functions are those that provide a lot of functionality
but have a very simple interface: they replace a large cognitive load
(reading the detailed implementation) with a much smaller
cognitive load (learning the interface). I call these functions "deep".

However, like most ideas in software design, decomposition can be taken too far.
As functions get smaller and smaller there is less and less
benefit to further subdivision.
The amount of functionality hidden behind each interface
drops, while the interfaces often become more complex.
I call these interfaces "shallow": they don't help much in terms of
reducing what the programmer needs to know. Eventually, the point is
reached where someone using the function needs
to understand every aspect of its implementation. Such functions
are usually pointless.

Another problem with decomposing too far is that it tends to
result in *entanglement*. Two functions
are entangled (or "conjoined" in APOSD terminology) if, in order to
understand how one of them works internally, you also need to read the
code of the other. If you've ever found yourself flipping back and forth
between the implementations of two functions as you read code, that's a
red flag that the functions might be entangled. Entangled functions
are hard to read because the information you need to have in your head
at once isn't all in the same place. Entangled functions can usually
be improved by combining them so that all the code is in one place.

The advice in *Clean Code* on method length is so extreme that it encourages
programmers to create teeny-tiny functions that suffer from both shallow
interfaces and entanglement.  Setting arbitrary numerical limits such
as 2-4 lines in a function and a single line in the body of an
`if` or `while` statement exacerbates this problem.

I think these problems will be easiest to understand if we look at
specific code examples. But before we do that, let me ask you, Bob:
do you believe that it's possible for code to be over-decomposed, or
is smaller always better? And, if you believe that over-decomposition
is possible, how do you recognize when it has occurred?

(Note: I decided to start over on this discussion; sorry about that.
The previous discussion
felt like it led us off into the weeds, arguing about stuff that isn't
really very important. Hopefully this new text will allow us to focus
on the most important issues, such as "is smaller always better?", "what are
the consequences of over-decomposition?", and "how can I tell if I've
decomposed too much?" I have removed all my old text below, and I'm
guessing you'll need to revise yours. BTW, please don't interrupt my
thoughts above; place your comments below here.)

(Also, I'd prefer to call the entities "methods" rather than "functions",
particularly since the example we're going to discuss is in Java, which
uses methods. Do you feel strongly that we should use the term "function"?)

**UB:**

Let me stop you there with a challenge.  Is it true that extracting small function adds complexity?  Or, rather, do the extracted small functions make the already existing complexity explict and give it names and a defined interfaces?  Doesn't such extraction give the reader the opportunity to understand the the code in small pieces, and to ignore those pieces that the interfaces make obvious.

Generally, when I extract small functions from larger functions, those smaller functions are private.  They do not add to the overall interface of the containing class.  That class remains narrow and deep.  No outside user sees those small private functions.

By the same token, those small private functions partition and organize the internal structure with names to guide the reader.  That internal structure is particularly helpful if it describes the top-down functional decomposition of the problem.

Extracting small functions allows me to separate higher level code from lower level code so that readers of the code can read the high level without being forced to see the details they don't want or need.  The names given to the small extracted functions tell the reader what they need to know.

I call this: *being polite*.

**UB:**

Before a function is extracted it exists as a snippet of code in a larger function.  That snippet has an interface both at it's entry point and it exit point -- but that interface is not explicit. It exists within the current scope, has access to all the variables of that scope, and is "called" by the flow of control.  By extracting the function that snippet turns into a black box with a restricted access, and an explicit call to a named and defined interface.

I don't think we disagree in principle.  We both agree that splitting up functions is a good way to manage complexity.  It seems to me that all we are quibbling about is the degree.  I believe in the principle of many small named functions.  Perhaps you believe in the principle of a few moderately sized functions.  To each his own.

The strategy that I use for deciding how far to take extraction is the old rule that a function should do "*One Thing*".  If I can *meaninfully* extract one function from another, then the original function did more than one thing.  "Meaningfully" means that the extracted functionality can be named; and that it does less than the original function.

An example of a meaningless extraction is one in which you extract the entire body of a function, reducing that function to a single call to the extracted function.  That new function does not do less than the original, and it cannot be given a name that differs in meaning from the original.

There are also times when a stretch of code is just so obvious that an extraction, albeit meaningful, would obscure more than it reveals.  That's a judgement call.  And it is in that judgement that the two of us likely differ.

**JOHN:**
Unfortunately the One Thing approach will lead to over-decompositon:
* The term "one thing" is vague and easy to abuse. For example, if a method
  has two lines of code, isn't it doing two things?
* The guardrails you described aren't likely to prevent over-decomposition.
  The "can it be named" qualification doesn't help: anything can be named.
  The "extracting the entire body" guardrail only deals with one extreme case.
  The "obscure more than it reveals" test doesn't provide meaningful
  guidance (I don't know what you mean by "obscure" and "reveal").
* The One Thing approach is simply wrong in many cases. If two things
  are closely related, it might well make sense to implement them in a
  single method. For example, any thread-safe method will first have
  to acquire a lock, then carry out its function. These are two "things",
  but they belong in the same method.

I couldn't find any mention of the One Thing principle in
*Clean Code*, or any other guardrails to prevent over-decomposition.
Is there something I missed?

**JOHN:**

I think it will be easier to clarify our differences if we consider
a specific code example. Let's look at the PrimeGenerator class from
*Clean Code*, which is Listing 10-8 on pages 145-146. This Java class
generates the first N prime numbers:

	package literatePrimes;

	import java.util.ArrayList;

	public class PrimeGenerator {
	  private static int[] primes;
	  private static ArrayList<Integer> multiplesOfPrimeFactors;

	  protected static int[] generate(int n) {
	    primes = new int[n];
	    multiplesOfPrimeFactors = new ArrayList<Integer>();
	    set2AsFirstPrime();
	    checkOddNumbersForSubsequentPrimes();
	    return primes;
	  }

	  private static void set2AsFirstPrime() {
	    primes[0] = 2;
	    multiplesOfPrimeFactors.add(2);
	  }

	  private static void checkOddNumbersForSubsequentPrimes() {
	    int primeIndex = 1;
	    for (int candidate = 3;
	         primeIndex < primes.length;
	         candidate += 2) {
	      if (isPrime(candidate))
	        primes[primeIndex++] = candidate;
	    }
	  }

	  private static boolean isPrime(int candidate) {
	    if (isLeastRelevantMultipleOfLargerPrimeFactor(candidate)) {
	      multiplesOfPrimeFactors.add(candidate);
	      return false;
	    }
	    return isNotMultipleOfAnyPreviousPrimeFactor(candidate);
	  }

	  private static boolean
	  isLeastRelevantMultipleOfLargerPrimeFactor(int candidate) {
	    int nextLargerPrimeFactor = primes[multiplesOfPrimeFactors.size()];
	    int leastRelevantMultiple = nextLargerPrimeFactor * nextLargerPrimeFactor;
	    return candidate == leastRelevantMultiple;
	  }

	  private static boolean
	  isNotMultipleOfAnyPreviousPrimeFactor(int candidate) {
	    for (int n = 1; n < multiplesOfPrimeFactors.size(); n++) {
	      if (isMultipleOfNthPrimeFactor(candidate, n))
	        return false;
	    }
	    return true;
	  }

	  private static boolean
	  isMultipleOfNthPrimeFactor(int candidate, int n) {
	    return candidate ==
	      smallestOddNthMultipleNotLessThanCandidate(candidate, n);
	  }

	  private static int
	  smallestOddNthMultipleNotLessThanCandidate(int candidate, int n) {
	    int multiple = multiplesOfPrimeFactors.get(n);
	    while (multiple < candidate)
	      multiple += 2 * primes[n];
	    multiplesOfPrimeFactors.set(n, multiple);
	    return multiple;
	  }
	}

Before we dive into this code, I'd encourage everyone reading
this article to take time to read over the code and draw your own conclusions
about it. Did you find the code easy to understand? If so, why? If not, what
makes it complex?

Also, Bob, can you confirm that you stand by this code (i.e. the code
properly exemplifies the design philosophy of *Clean Code* and this
is the way you believe the code should appear if it were used in
production)?

**UB:**

Ah, yes.  The `PrimeGenerator`.  This code comes from the 1982 paper on [*Literate Programming*](https://www.cs.tufts.edu/~nr/cs257/archive/literate-programming/01-knuth-lp.pdf) written by Donald Knuth.  The program was originally written in Pascal, and was automatically generated by Knuth's WEB system.  I translated it to Java.

The example appears in a chapter named *Classes*.  I used it as a demonstration for how to partition a large and messy legacy function into a set of smaller classes and functions.

The original code was a large-ish program that not only generated prime numbers but also printed them in columns and pages with page numbers.  It was one big monolithic function.  The three new classes I extracted from it were `PrimePrinter`, `RowColumnPagePrinter` and `PrimeGenerator`.

Once the classes were extracted, the `PrimeGenerator` looked like this: (which I did not publish in the book.)  The variable names were Knuth's.

	public class PrimeGenerator {
	  protected static int[] generate(int n) {
	    int[] p = new int[n];
	    ArrayList<Integer> mult = new ArrayList<Integer>();
	    p[0] = 2;
	    mult.add(2);
	    int k = 1;
	    for (int j = 3; k < p.length; j += 2) {
	      boolean jprime = false;
	      int ord = mult.size();
	      int square = p[ord] * p[ord];
	      if (j == square) {
	        mult.add(j);
	      } else {
	        jprime=true;
	        for (int mi = 1; mi < ord; mi++) {
	          int m = mult.get(mi);
	          while (m < j)
	            m += 2 * p[mi];
	          mult.set(mi, m);
	          if (j == m) {
	            jprime = false;
	            break;
	          }
	        }
	      }
	      if (jprime)
	        p[k++] = j;
	    }
	    return p;
	  }
	}

I refactored that wad of code in order to break it up into a few reasonbaly sized and reasonably named chunks so that my readers could see how large functions, that violate the Single Responsibility Principle, can be broken down into a few smaller well-named classes containing a few smaller well-named functions.

I think it's somewhat better than where it started.

It was NOT my intent, in this chapter, to teach my readers how to write the most optimal prime number generator.  That was the last thing on my mind, and the last thing I wanted on theirs.

**JOHN:**

I use this code for an in-class exercise in a software design course
that I teach at Stanford. I give students about 30 minutes to read
over the code and discuss it in pairs. Invariably, the students
can't get comfortable with
the code in that time; they are unable to understand how the
algorithm works or why. I also struggled to understand this code.
In the class, we discuss why the code is so impenetrable, then students
rewrite it to simplify it.

**UB:**

Thirty minutes?  Wow.  Maybe I'm just dense but it took me a lot longer than that to understand it.  I even had Knuth's original description and I still had to puzzle it out for a really long time. I even had the code that I expect you will show next, and since it has been eighteen years, it *still* took me a lot longer than 30 minutes to work it all out.

**JOHN:**

I'm not sure what you're getting at here, but to me, if 70 lines of
of self-contained code with no external dependencies
can't be understand in 30 minutes by two people working together, then
that code has serious problems. I'm unwilling to accept that such high
of complexity is inevitable without first trying to fix it.
And in this particular case
I'm confident that the complexity can be reduced significantly.

(Note: I think both your comment and my comment are distractions; I'd be
happy to remove mine if you're willing to remove yours. You can either
delete both those comments and this parenthetical, or leave both comments
and just delete this parenthetical)

**JOHN:**

I think there are many design problems with this code, but for now I'll
focus on method length (I'll come back to this code later when we
discuss comments). This code is chopped up so much (8 teeny-tiny methods)
that it's difficult to read. For starters, consider the
`isNotMultipleOfAnyPreviousPrimeFactor` method. This method invokes
`isMultipleOfNthPrimeFactor`, which invokes
`smallestOddNthMultipleNotLessThanCandidate`. These methods are entangled:
in order to understand
`isNot...` you have to read the other two
methods and load all of that code into your mind at once. For example,
`isNot...` has side effects (it modifies `multiplesOfPrimeFactors`), but
you can't see that unless you read all three methods.

**UB:**

I agree.  Though I would not have agreed eighteen years ago when I was in the throes of refactoring this.  At the time that name made perfect sense to me.  It makes perfect sense to me now, too -- but that's because I understand the algorithm.  When I returned to the algorithm a few days ago, I also struggled with those names.  Once you understand the algorithm the names make perfect sense, and that understanding breaks the entaglement.

So, a good critique of those names is that they depend, to some extent, upon gaining some understanding of the algorithm.

But again, teaching this algorithm was not the intent of that chapter.

**JOHN:**

Going back to my introductory remarks about complexity, splitting up
`isNot...` into three methods doesn't reduce the amount of information
you have to have. It just splits it up and spreads it out, so it isn't as
obvious that you need to read all three methods together. And, it's harder
to see the overall structure of the code because it's split up: readers have
to flip back and forth between the methods, effectively reconstructing a
monolithic version in their minds. Thus, this code will be easiest to
understand if it's all together in one place.

**UB:**

I disagree.  What you say may be true in this particular case; but it is not true in the general case.  For example:

	for (Employee e : employees)
	  if (e.shouldPayToday())
		  e.pay();

This would not be made more understandable if we replaced those two function calls with the their implementations.  Such a replacement would simply obscure the intent.

**JOHN:**

Not only is `isNot...` entangled with the methods it calls, it's also
entangled with its callers. `isNot...` only works if it is invoked in
a loop where `candidate` increases monotonically. To convince yourself
that it works, you have to find the code that invokes `isNot...` and
make sure that `candidate` never decreases from one call to the next.
Separating `isNot...` from the loop that invokes it makes it harder
for readers to convince themselves that it works.

To me, all of the methods in the class are entangled: in order to
understand the class I had to load all of them into my mind
at once. I was constantly flipping back and forth between the methods
as I read the code. This is a red flag indicating
that the code has been over-decomposed.

Bob, can you help me understand why you divided the code into such
tiny methods?
Is there some benefit to having so many methods that I have missed?

**UB:**

In general I believe in the principle of small well-named functions. Generally speaking if you can break a large function into several well-named smaller function, and by doing so expose the high level functional decomposition, then that's a good thing.

In this case I think I improved the structure and naming of the original quite a bit.  I think you can make the argumennt that I did not improve it enough.  However, the goal of this chapter, and the goal of this example, were not to show the best possible prime number generator.  Rather it was to present the strategy for breaking up large functions into smaller classes and functions.  In that regard I think the example was a success.

**JOHN:**

Let's consider another example: `isMultipleOfNthPrimeFactor`.
This method contains a single statement that calls `smallestOdd...`; the only
functionality this method contributes is a `==` comparison.
Why does it make sense for this to be a separate method rather than
just invoking `smallestOdd...` directly from `isNot...`?

**UB:**

Functions like this are trying to describe a logical equivalence that might help the reader understand things.  In this case it is saying that the smallest odd nth multiple not less than the candidate _is_ a multiple of the nth prime factor.  That's an interesting equivalence.  Or, at least, I found it to be interesting at the time I wrote it.

## Comments

**JOHN:**

Let's move on to the second area of disagreement: comments. In my opinion,
the *Clean Code* approach to commenting results in code with
inadequate documentation, which increases the cost of software development.
I'm sure you disagree, so let's discuss.

Here is what *Clean Code* says about comments (page 54):

> The proper use of comments is to compensate for our failure to express
  ourselves in code. Note that I use the word failure. I meant it.
  Comments are always failures. We must have them because we cannot always
  figure out how to express ourselves without them, but their use is not
  a cause for celebration... Every time you write a comment, you should
  grimace and feel the failure of your ability of expression.

I have to be honest: I was horrified when I first read this text, and it
still makes me cringe. This stigmatizes writing comments. Junior developers
will think "if I write comments, people may think I've failed, so the
safest thing is to write no comments."

**UB:**

That chapter begins with these words:
>*Nothing can be quite so helpful as a well placed comment.*

It goes on to say that comments are a *necessary* evil.

**JOHN:**

I moved your comment above down so it didn't break up my paragraph. You
may want to reword it and/or the comment below. You can delete this comment.

**UB:**

Well, perhaps, but only if they didn't read the chapter.  The chapter walks through a series of comments, some bad, some good.

**JOHN:**

*Clean Code* focuses a lot more on the "evil" aspects of comments than the
"necessary" aspects. The sentence you quoted above is followed by two
sentences criticizing comments. Chapter 4 spends 4 pages talking about good
comments, followed by 15 pages talking about bad comments. There are snubs
like "the only truly good comment is the comment you found a way
not to write". And "Comments are always failures" is so catchy
that it's the one thing readers are most likely to remember from the
chapter.

I don't agree that comments are evil. Comments are not the problem,
they are the solution. The problem is that there is a lot of important
information that simply cannot be expressed in code. When a programmer
writes comments, they have not failed to express themselves; they have
succeeded in providing important information that makes code easier to
understand.

**UB:**

It's very true that there is important information that is not, or cannot, be expresssed in code.  That's a failure.  A failure of our languages, or of our ability to use them to express ourselves.  In every case a comment is a failure of our ability to use our languages to express our intent.

And we fail at that very frequently, and so comments are a necessary evil.  If we had the perfect programming language TM we would never write another comment.

**JOHN:**

I don't agree that a perfect programming language would
eliminate the need for comments. Comments and code serve very different
purposes, so it's not obvious to me that we should use the same
language for both. In my experience, English works quite well
as a language for comments.
Why are you so adamant that all information about a program must
be expressed in code, rather than using a combination of code
and English?

**UB:**

*Oh, baloney!*  Again, if you read the chapter you are not going to be led to write uncommented code.  Hopefully you will be led to strive to write fewer comments by using the code to express your intent.

**JOHN:**

I dropped my comment that precedes your comment above, so I'm guessing your
comment is no longer necessary? You can delete this one as well.

**JOHN:**

Let's consider the `PrimeGenerator` class. There is not a single comment
in this code other than the one I added at the top; presumably you
think this is appropriate? There are two major reasons why comments
are needed.

**UB:**

If I was putting that code into a library then some comments would be appropriate. But his code is not getting put into a library.  It was created to make the point that large functions can be broken down into smaller classes containing smaller functions. Adding lots of explanatory comments would have detracted from that point.

**JOHN:**

I disagree that adding comments would have distracted from your point,
but let's not argue that. Instead, let's discuss what comments this code
*should* have if it were used in production. For starters, would you like to
modify the code to add what you think are appropriate comments?
Or, I can talk about what I think is needed and you can agree or
disagree.

**JOHN:**

First, there is important infomation that cannot be represented in the code.
For example:

* One of the key ideas behind this code is to improve performance by
  avoiding divisions, which are relatively expensive. Readers will wonder
  "why didn't you just use `mod` instead of all this complexity with
  prime multiples?"

**UB:**

Why is that important to understanding the lesson of that chapter?

Consider my audience.  I was writing for software developers in 2008, and I was explaining how large functions can be split into several classes.  Why would I clutter up that explanatory code with a comment that they would have utterly no interest in?

**JOHN:**

* The first multiple for each new prime number is computed by squaring
  the prime, rather than multiplying it by 3. This is mysterious:
  why is it safe to skip the intervening odd multiples?

**UB:**

Why indeed?  This is not at all easy to understand.  I needed to go on an hour long bike ride to finally work it out.  Again, maybe I'm dense, but this is not an easy thing to understand.

And again I was not teaching software developers the most optimal way to calculate prime numbers.  I was teaching them how and when to break large functions up into smaller classes and smaller functions.

**JOHN:**

There is no way to explain either of these things in the code; without
comments, readers are left to figure them out on their own. The students
in my class are generally unable to figure out either of these in the
30 minutes I give them, but comments would have
allowed them to understand in a minute or two. Going back to my
introductory remarks, this is an example where information is important,
but it isn't made available.

**UB:**

That bike ride I took was *after* I had read the comment you put in your version of this function.  So -- again, maybe I'm dense, but that comment didn't help me at all -- it was just a jumble of numbers that I could not map to the problem.  Now that I understand the algorithm (again), I understand your comment.  But the reverse was not true.

**JOHN:**

The second reason for comments is abstraction. Simply put, without
comments there is no way to have abstraction or modularity.

Abstraction is one of the most important components of good software design.
I define an abstraction as "a simplified way of thinking about something
that omits unimportant details." The most obvious example of an abstraction
is a method. It should be possible to use a method without reading its code.
The way we achieve this is by writing a header comment that describes
the method's *interface* (all the information someone needs in order
to invoke the method). If the method is well designed, the interface will be
much simpler than the code of the method (it omits implementation details),
so the comments reduce the amount of information people must have in
their heads.

**UB:**

Um...

**UB:**

Long ago, in a 1995 book, I defined it as:
>*The amplification of the essential and the elimination of the irrelevant.*

**UB:**

I agree.

**JOHN:**

I moved the preceeding 3 comments down so that they don't interrupt my thoughts
on abstraction; you may want to revise them and/or combine with the comment
below. You can delete this comment.

**UB:**

	addSongToLibrary(String title, String[] authors, int durationInSeconds);

This seems like a very nice abstraction to me, and I cannot imagine how a comment might improve it.

**JOHN:**

Sometimes the signature of a method (names and types of the method, its
arguments, and its return value) contains all the information
needed to use it, but this is pretty rare. Just skim through the documentation
for your favorite library package: in how many cases could you understand how
to use a method with only its signature? Even in the `addSongToLibrary` example
above more information is needed:
* Is there any expected format for an author string, such as
  "LastName, FirstName"?
* Are the authors expected to be in alphabetical order? If not, is
  the order significant in some other way?
* What happens if there is already a song in the library with the
  given title? Is it replaced with the new one, or will the library keep
  multiple songs with the same title?
* How is the library stored (e.g. is it entirely in memory? saved on disk?)?
  This information may have been documented somewhere else, such as
  the overall class documentation, in which case it wouldn't be needed here.

**JOHN:**

Consider the `isNot...` method in `PrimeGenerator`. This method has no header
comment, so readers are forced to read the method's code to figure out how
to use it. This means that readers have to load more information into their
heads, which makes it harder to work in the code.
Here are a couple of things that would need to be described in
the header comment for `isNot...`:
* Its side effects (modifying `multiplesOfPrimeFactors`); without comments,
  the entire tree of methods underneath `isNot...` must be read to
  discover the side effects.
* The fact that it works only if invoked with ascending argument values (this
  constraint is unlikely to be obvious even after reading the code).

Bob, can you explain why you chose not to include any comments in the `PrimeGenerator`
class? How is code better when it has no comments?

**UB:**

As I explained earlier, those kinds of comments in this code would have detracted from the lesson of the chapter, which was how and why to break large functions into smaller classes containing smaller functions, and not to understand Knuth's ancient prime generator algorithm.

## John's Rewrite of PrimeGenerator

**JOHN:**

I mentioned that I ask the students in my software design class to rewrite
PrimeGenerator to fix all of its design problems. Here is my rewrite:

	package literatePrimes;

	import java.util.ArrayList;

	public class PrimeGenerator2 {

	    /**
	     * Computes the first prime numbers; the return value contains the
	     * computed primes, in increasing order of size.
	     * @param n
	     *      How many prime numbers to compute.
	     */
	    public static int[] generate(int n) {
	        int[] primes = new int[n];

	        // Used to test efficiently (without division) whether a candidate
	        // is a multiple of a previously-encountered prime number. Each entry
	        // here contains an odd multiple of the corresponding entry in
	        // primes. Entries increase monotonically.
	        int[] multiples = new int[n];

	        // Index of the last value in multiples that we need to consider
	        // when testing candidates (all elements after this are greater
	        // than our current candidate, so they don't need to be considered).
	        int lastMultiple = 0;

	        // Number of valid entries in primes.
	        int primesFound = 1;

	        primes[0] = 2;
	        multiples[0] = 4;

	        // Each iteration through this loop considers one candidate; skip
	        // the even numbers, since they can't be prime.
	        candidates: for (int candidate = 3; primesFound < n; candidate += 2) {
	            if (candidate >= multiples[lastMultiple]) {
	                lastMultiple++;
	            }

	            // Each iteration of this loop tests the candidate against one
	            // potential prime factor. Skip the first factor (2) since we
	            // only consider odd candidates.
	            for (int i = 1; i <= lastMultiple; i++) {
	                while (multiples[i] < candidate) {
	                    multiples[i] += 2*primes[i];
	                }
	                if (multiples[i] == candidate) {
	                    continue candidates;
	                }
	            }
	            primes[primesFound] = candidate;

	            // Start with the prime's square here, rather than 3x the prime.
	            // This saves time and is safe because all of the intervening
	            // multiples will be detected by smaller prime numbers. As an
	            // example, consider the prime 7: the value in multiples will
	            // start at 49; 21 will be ruled out as a multiple of 3, and
	            // 35 will be ruled out as a multiple of 5, so 49 is the first
	            // multiple that won't be ruled out by a smaller prime.
	            multiples[primesFound] = candidate*candidate;
	            primesFound++;
	        }
	        return primes;
	    }
	}


Everyone can read this and decide for themselves whether they think
it is easier to understand than the original. I'd like to mention a
couple of overall things:
* There is only one method. I didn't subdivide it because I felt the
  method already divides naturally into pieces that are distinct
  and understandable. It didn't seem to me that pulling out
  methods would improve readability significantly. When students
  rewrite the code, they typically have 2 or 3 methods, and those are
  usually OK too.
* There are a *lot* of comments. It's extremely rare for me to
  write code with this density of comments. Most methods
  I write have no comments in the body, just a header comment
  describing the interface. But this code is subtle and tricky,
  so it needs a lot of comments to make the subtleties clear to
  readers. Even with all this additional explanatory material
  my version is a bit shorter than the original (65 lines vs. 70).

**UB:**

I presume this is a complete rewrite.  My guess is that you worked to understand the algorithm from Clean Code and then wrote this from scratch.  If that's so, then fair enough.

That's not what I did.  I *refactored* Knuth's algorithm in order to give it a little structure.

Having said that, your version is much better than either Knuth's or mine.  I could not have used it in the chapter I was writing *because* it is still in a single function, and I needed to show the partioning.

I wrote that chapter 18 years ago, so it's been a long time since I saw and understood this algorithm.  When I first saw your challenge I thought: "Oh, I can figure out my own code!"  But, no.  I could see all the moving parts, but I could not figure out why those moving parts generated a list of prime numbers.

So then I looked at your code.  I had the same problem.  I could see all the moving parts, all with comments, but I still could not figure out why those moving parts generated a list of prime numbers.

Figuring that out required a lot of staring at the ceiling, closing my eyes, visualizing, and riding my bike.

Among the problems I had were the comments you wrote.  Let's take them one at a time.

	    /**
	     * Computes the first prime numbers; the return value contains the
	     * computed primes, in increasing order of size.
	     * @param n
	     *      How many prime numbers to compute.
	     */
	    public static int[] generate(int n) {

It seems to me that this would be better as:

	public static int[] generateNPrimeNumbers(int n) {

or if you must:

	//Return the first n prime numbers
	public static int[] generate(int n) {

I'm not opposed to Javadocs as a rule; but I write them only when absolutely necessary. I also have an aversion for descriptions and `@param` statements that are perfectly obvious from the function signature.

The next comment cost me a good 20 minutes of puzzling things out.

	        // Used to test efficiently (without division) whether a candidate
	        // is a multiple of a previously-encountered prime number. Each entry
	        // here contains an odd multiple of the corresponding entry in
	        // primes. Entries increase monotonically.

First of all I'm not sure why the "division" statement is necessary.  I'm old school so I expect that everyone knows to avoid division in inner loops if it can be avoided.  But maybe I'm wrong about that...

Also, the *Sieve of Eratosthenes* does not do division, and is a lot easier to understand *and explain* than this algorithm.  So why this particular algorithm?  I think Knuth was trying to save memory -- and in 1982 saving memory was important.  This algorithm uses a lot less memory than the sieve.

Then came the phrase: `Each entry here contains an odd multiple...`.  I looked at that, and then at the code, and I saw: `multiples[0] = 4;`.

"That's not odd" I said to myself.  "So maybe he meant even."

So then I looked down and saw: `multiples[i] += 2*primes[i];`

"That's adding an even number!" I said to myself.  "I'm pretty sure he meant to say 'even' instead of 'odd'."

I hadn't yet worked out what the `multiples` array was.  So I thought it was perfectly reasonable that it would have even numbers in it, and that your comment was simply an understandable word transposition.  After all, there's no compiler for comments so they suffer from the kinds of mistakes that humans often make with words.

It was only when I got to `multiples[primesFound] = candidate*candidate;` that I started to question things.  If the `candidate` is prime, shouldn't `prime*prime` be odd in every case beyond 2?  I had to do the math in my head to prove that.  (2n+1)(2n+1) = 4n^2+4n+1 ... Yeah, that's odd.

OK, so the `multiples` array is full of odd multiples, except for the first element, since it will be muliples of 2.

So perhaps that comment should be:

	 // multiples of corresponding prime.

Or perhaps we should change the name of the array to something like `primeMultiples` and drop the comment altogether.

Moving on to the next comment:

	            // Each iteration of this loop tests the candidate against one
	            // potential prime factor. Skip the first factor (2) since we
	            // only consider odd candidates.

That doesn't  make a lot of sense.  The code it's talking about is:

	            for (int i = 1; i <= lastMultiple; i++) {
	                while (multiples[i] < candidate) {

The `multiples` array, as we have now learned, is an array of *multiples* of prime numbers.  This loop is not testing the candidate against prime *factors*, it's testing it against the current prime multiples.

Fortunately for me the third of fourth time I read this comment I realized that you really meant to use the word "multiples".  But the only way for me to know that was to understand the algorithm.  And when I understand the algorithm, why do I need the comment?

The last comment is:

	            // Start with the prime's square here, rather than 3x the prime.
	            // This saves time and is safe because all of the intervening
	            // multiples will be detected by smaller prime numbers. As an
	            // example, consider the prime 7: the value in multiples will
	            // start at 49; 21 will be ruled out as a multiple of 3, and
	            // 35 will be ruled out as a multiple of 5, so 49 is the first
	            // multiple that won't be ruled out by a smaller prime.

The first few times I read this it made no sense to me at all.  It was just a jumble of numbers.  So I ignored it.

As I started at the ceiling, and closed my eyes to visualize, I finally realized that the `multiples` array was the equivalent of the array of booleans we use in the *Sieve of Eratosthenes* -- but with a really interesting twist.  If you were to do the sieve on a whiteboard, you _could_ erase every number less than the candidate, and only cross out the numbers that were the next multiples of all the previous primes.

That explanation makes perfect sense to me -- now, but I'd be willing to be that those who are reading it are puzzling over it.  The idea is just hard to explain.

OK, so that left me with one final question.  What the deuce was the reason behind:

	multiples[primesFound] = candidate*candidate;

Why the square?  That makes no sense.  So I changed it to:

	multiples[primesFound] = candidate;

And it worked just fine.  So this must just be an optimization.  That whole long comment is about an optimization.  OK, so why?  Why can you start the multiple for a prime at prime^2?

That's not an easy question to answer.  Eventually, after a long bike ride, I realized that the prime multiples of 2 will at one point contain 2*3 and then 2*5.  So the `multiples` array will at some point contain multiples of primes *larger* than the prime they represent.  !!!  And then it all made sense.

Then I could read your comment and see what you were saying.

###A Tale of Two Programmers
The bottom line here is that you and I both fell into the same trap.  I refactored that old algorithm 18 years ago, and I thought all those function and variable names would make my intent clear -- *because I understood that algorithm*.

You wrote that code awhile back and decorated it with comments that you thought would explain your intent -- *because you understood that algorithm*.

But my names didn't help me 18 years later.  They didn't help you, or your students either.  And your comments didn't help me.

We were inside the box trying to communicate to those who stood outside and could not see what we saw.

## Bob's Rewrite of PrimeGenerator2

**UB:**

When I saw your solution, and after I gained a good understanding of it.  I refactored it just a bit.  I loaded it into my IDE, wrote some simple tests, and extracted a few simple methods.

I also got rid of that *awful* labeled `continue` statement.  And I added 3 to the primes list so that I could mark the first element as *irrelevant* and give it a value of -1.  (I think I was still reeling from the even/odd confusion.)

I like this because the implementation of the `generateFirstNPrimes` function describes the moving parts in a way that hints at what is going on.  It's easy to read that implementation and get a glimpse of the mechanism.  I'm not at all sure that the comment helps.

I think it is just the reality of this algorithm that the effort required to properly explain it, and the effort required for anyone else to read and understand that explanation is roughly equivalent to the effort needed to read the code and go on a bike ride.

```
package literatePrimes;

public class PrimeGenerator3 {
    private static int[] primes;
    private static int[] primeMultiples;
    private static int lastRelevantMultiple;
    private static int primesFound;
    private static int candidate;

    // Lovely little algorithm that finds primes by predicting
    // the next composite number and skipping over it. That prediction
    // consists of a set of prime multiples that are continuously
    // increased to keep pace with the candidate.

    public static int[] generateFirstNPrimes(int n) {
        initializeTheGenerator(n);

        for (candidate = 5; primesFound < n; candidate += 2) {
            increaseEachPrimeMultipleToOrBeyondCandidate();
            if (candidateIsNotOneOfThePrimeMultiples()) {
                registerTheCandiateAsPrime();
            }
        }
        return primes;
    }

    private static void initializeTheGenerator(int n) {
        primes = new int[n];
        primeMultiples = new int[n];
        lastRelevantMultiple = 1;

        // prime the pump. (Sorry, couldn't resist.)
        primesFound = 2;
        primes[0] = 2;
        primes[1] = 3;

        primeMultiples[0] = -1;// irrelevant
        primeMultiples[1] = 9;
    }

    private static void increaseEachPrimeMultipleToOrBeyondCandidate() {
        if (candidate >= primeMultiples[lastRelevantMultiple])
            lastRelevantMultiple++;

        for (int i = 1; i <= lastRelevantMultiple; i++)
            while (primeMultiples[i] < candidate)
                primeMultiples[i] += 2 * primes[i];
    }

    private static boolean candidateIsNotOneOfThePrimeMultiples() {
        for (int i = 1; i <= lastRelevantMultiple; i++)
            if (primeMultiples[i] == candidate)
                return false;
        return true;
    }

    private static void registerTheCandiateAsPrime() {
        primes[primesFound] = candidate;
        primeMultiples[primesFound] = candidate * candidate;
        primesFound++;
    }
}
```

## Test-Driven Development

**JOHN:**

Let's move on to our third area of disagreement, which is Test-Driven
Development. I am a huge fan of unit testing. I believe that unit tests are
an indispensable part of the software development process and pay for
themselves over and over. I think we agree on this.

However, I am not fan of Test-Driven Development (TDD), which dictates
that tests must be written before code and that code must be written
and tested in tiny increments. This approach has serious problems
without any compensating advantages that I have been able to identify.

*Clean Code* advocates for TDD, but I was not able to find any
explanation in *Clean Code* of *why* TDD is a good idea. Bob,
perhaps you can start the discussion off by explaining what benefits
accrue from writing tests before code?

**BOB:**

*Your answer here*