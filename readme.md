
# S-Coherence

The modern world contains a plethora of intelligent life and
semi-intelligent semi-life which usually serves the intelligent life.

This hybrid world of intelligent and semi-intelligent actors brings
new risks associated with communication and trust. A rising challenge
all modern actors encounter is lack of information context and authority;
modern intelligent life chooses not seek to speak with authority and
modern semi-intelligent life fundamentally cannot provide meaningful context
to the information it reports, leading to incomplete realities which are
rarely shared between the intelligent life because of their inability to
communicate with authority.

A long-term semi-intelligent-powered solution to this problem is implemented here, and described below.

To solve the incomplete reality problem the system must have a way to:

1. Accept new information
2. Rank information correctness by relative authority to all existing information in the system
3. Quickly search ranked information to fill gaps in incomplete realities when new information is presented


`s-coherence` is a standalone CLI program which implements this system via
a distributed authority identity pool paired with an information token-based economy
where the value of any given token is measured by its coherence to other tokens.
Coherence is measured by requiring [semi-]intelligent life to sign a token relating two or more 
others in the form `TOKEN1=TOKEN2=TOKEN3...` where `TOKEN` may be any other token.
`s-coherence` defines a number of primitive tokens alongside measurement
definitions for the coherence of those tokens.


`s-coherence` also provides a population with dynamic defense against untrustworthy communication
by passively publishing randomly-associated tokens from the pool of known tokens. A population lacking
context and authority will also lack communication, as the noise will make it impossible to rank statements
near `1` for coherence. Tools must be built to identify the smallest subset of new tokens which can transform
an incoherent measurement of reality into a coherent one.


## Primitive Tokens

 - `TRUE`
    - Represents the information that some associated information is valid, correct, and representative of reality.
    - Coherence is defined as `1` associated with `TRUE` and `0` when associated with `FALSE`.

 - `FALSE`
    - Represents the information that some associated information is invalid, incorrect, and not representative of reality.
    - Coherence is defined as `1` associated with `FALSE` and `0` when associated with `TRUE`.

 - `ACTO(public_key, signature_timestamp, signed_tokens)`
    - Represents the information that some associated information is `TRUE` not because it is valid, correct, and representative of reality
      but rather because the [semi-]intelligent life identified by `public_key` has used their authority to declare the associated information `TRUE`.
      This must be validated by decrypting `signed_tokens` using `public_key` and verifying the result matches the `signature_timestamp` + the preceding tokens.
    - Coherence is defined as: ( `1` if the signature is valid for the associated tokens else `0` ) * `2 - (2 / (1 + e^-|(1/180)*( signature_timestamp age in days )|))` * user trust modifier (defaults to 1)
    - This means new signed information is coherent but forged signatures and information an infinite number of days old will not add to the coherence of the associated tokens.
      If you know the intelligent actor owning `public_key` you may set a trust modifier to trust this actor more or less when calculating the coherence
      of information they associate.

 - `TIME(begin_timestamp, end_timestamp)`
    - Represents the information that some associated information is `TRUE` ONLY between `begin_timestamp` and `end_timestamp`.
    - Coherence is defined using `a=(T1_end_timestamp - T1_begin_timestamp) / 2) in days` and `b=(T2_end_timestamp - T2_begin_timestamp) / 2) in days`
      put into the sigmoid function `2 - (2 / (1 + e^-|(1/180)*(a-b)|))`
    - Tokens with identical center `TIME` values will have a coherence score of `1`, while
      tokens that are an infinite number of days apart will have a coherence score of `0`. (LaTeX formula for copy/pasting into calculators: `2-\frac{2}{1+e^{-\left|\left(\frac{1}{180}\right)\cdot\left(a-b\right)\right|}}`)

 - `RAW(utf8_information_expressed_in_any_human_language)`
    - Represents the information as expressed in `information_expressed_in_any_human_language`. This is a whildcard
      used to associate arbitrary outside information with the remaining tokens, and plays a large role in step 1 of
      solving the incomplete reality problem.
    - Coherence is defined as `0` for all information expressed in a `RAW` token.
    - This means new information attached to the `s-coherence` graph is meaningless without an association to other tokens which can
      be mechanically ranked for `coherence` against all other known information.


TODO identify other fundamental measurements which may be compared numerically such that groups of statements which agree score close to `1` and
statements which present other realities cause the entire system to score close to `0`.


## Token coherence examples

```
RAW("Today is John's birthday")=TRUE=TIME(Fri Jul 16 00:00:01 PM EDT 2021, Fri Jul 16 23:59:59 PM EDT 2021)=ACTO(<public key>, <signature of RAW("Today is John's birthday")=TRUE=TIME(Fri Jul 16 00:00:01 PM EDT 2021, Fri Jul 16 23:59:59 PM EDT 2021) using <public key>>)
```

Coherence score: `0+0+0+1 = 1` because one `ACTO` with a valid signature exists in this collection of tokens,
and none of the others are being compared to anything.


```
RAW("Today is John's birthday")=TRUE=TIME(Fri Jul 16 00:00:01 PM EDT 2021, Fri Jul 16 23:59:59 PM EDT 2021)=ACTO(<public key1>, <signature .... using <public key1>>)
RAW("Today is John's birthday")=TRUE=TIME(Mon Aug 16 00:00:01 PM EDT 2021, Mon Aug 16 23:59:59 PM EDT 2021)=ACTO(<public key2>, <signature .... using <public key2>>)
```

Coherence score: `0+1+0.914+1  +  0+1+0.914+1 = 5.828` hmmmm that's not great...


TODO design comparison logic so a useful number may be derived for measuring agreement against arbitrary groups of statements.



# Overall Goals

 - [ ] Given group of statements and trust assumptions sort statements by coherency and display most coherent statements.
 - [ ] Given incoherent group of statements inform user of tokens outside the group which would act the most to increase coherency (aka a mathematical tool for identifying the most useful questions to ask to make the statements valid, correct, and representative of reality)
 - [ ] FB/Twitter integration via a browser plug-in, just to get some verification that the system works as intended when a large population is actively working to make partially-coherent lies appear true.








