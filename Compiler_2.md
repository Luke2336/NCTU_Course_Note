# Context-Free Grammar

## Definition

- $G = (T, N, S, P)$
    - $T$: terminals
    - $N$: nonterminals
    - $S$: start symbol
    - $P$: productions 

## Derivation

- Leftmost derivation
- Rightmost derivation

## Parse Tree

- The leaves of a parse tree (from left to right) constitute a sentential form, called yield or frontier of the tree.

## Example

```
idlist: | nonEmptyIdlist;
nonEmptyIdlist: nonEmptyIdlist ',' id | id;
```

```
if-stmt: unmatched-stmt | matched-stmt;
matched-stmt: IF expr THEN matched-stmt ELSE matched-stmt | Others
unmatched-stmt: IF expr THEN matched-stmt ELSE unmatched-stmt
unmatched-stmt: IF expr THEN if-stmt
```

## Rules of Associativity

- left associativity: left recursion
    - $A \Rightarrow^+ A\alpha$
- right associativity: right recursion
    - $A \Rightarrow^+ \alpha A$

## Elimination of Left Recursion

From

$A \to A \alpha_1 | A \alpha_2 | \dots | A \alpha_m | \beta_1 | \beta_2 | \dots | \beta_n$

to

$A \to \beta_1 A' | \beta_2 A' | dots | \beta_n A'$

$A' \to \alpha_1 A' | \alpha_2 A' | \dots | \alpha_m A' | \epsilon$

### Algorithm

- No $A \Rightarrow^+ A$ or $A \to \epsilon$

```
Arrange nonterminals in some order A1, A2, ..., An
for (each i from 1 to n) {
    for (each j from 1 to i - 1) {
        replace each production Ai -> Aj r by production
        Ai -> E1r | E2r | ... | Ekr where Aj -> E1 | E2 | ... | Ek are current Aj-productions
    }
    eliminate immediate left recursion among Ai-production
}
```

## Left Factoring

From

$A \to \alpha \beta_1 | \alpha \beta_2 | \dots | \alpha \beta_n | \gamma$

to

$A \to \alpha A' | \gamma, A' \to \beta_1 | dots | \beta_n$

# Parser

## Syntax Analysis

- Determine if the input token stream satisfies the syntax of the program
- Given a token stream, a parser outputs a syntax tree (parse tree) upon the grammatical definitions

## Type of Parsers

- Universal Parser (any CFG)
- Top-Down Parser
- Bottom-up Parser

## Top-Down Parsing

- Left-recursive grammars cannot be parsed by top-down parsers

### Recursive-Descent Parsing


#### Preditive Parsing

##### LL(k) class

- The class of grammars for which we can construct predictive parsers looking $k$ symbols ahead in the input

#### Definition - $FIRST(\alpha)$

- $\alpha$ is any string of grammar symbols
- The set of terminals $c$ that can be the first symbol of strings derived from $\alpha$
    - If $\alpha \Rightarrow c \gamma$, then $c \in FIRST(\alpha)$
    - If $\alpha \Rightarrow \epsilon$, then $\epsilon \in FIRST(\alpha)$ 

#### Definition - $FOLLOW(A)$

- $A$ is a nonterminal
- The set of terminals $a$ (no $\epsilon$) that can appear immediately to the right of $A$ in some sentential form
    - If $S \Rightarrow \alpha A a \beta$, then $a \in FOLLOW(A)$
    - If $A$ can be rightmost symbol insome sentential form, then $\$ \in FOLLOW(A)$

#### Compute $FIRST(X)$

- $FIRST(\epsilon) = \epsilon$
- If $X$ is a terminal
    - $FIRST(X) = {X}$
- If $X$ is a nonterminal
    - $FIRST(X) = \cup_{X \to \alpha} FIRST(\alpha)$
    - If $X \to \epsilon$, then add $\epsilon$ into $FRIST(X)$
    - $If X \to Y_1 Y_2 \dots Y_k$ for some $k \ge 1$
        - $Y_i$ is either a terminal or a nonterminal
        - If for all $j = 1 \dots i - 1$, $\epsilon \in FIRST(Y_j)$, then add $FRIST(Y_j) - \{\epsilon\}$ for all $j$ and $FIRST(Y_i)$ into $FIRST(X)$
        - If for all $j = 1 \dots k, \epsilon \in FIRST(Y_j)$, then add $\epsilon$ into $FIRST(X)$

#### Compute $FOLLOW(A)$

- $S$ is the starting symbol
    - Add $\$$ into $FOLLOW(S)$
- $A$ is a nonterminal
    - Find the productions with $A$ on the right-hand-side
        - If $X \to \alpha A \beta$, then add $FIRST(\beta) - \{\epsilon\}$ into $FOLLOW(A)$
        - If $X \to \alpha A$ or $X \to \alpha A \beta$ and $\epsilon \in FIRST(\beta)$, then add $FOLLOW(X)$ into $FOLLOW(A)$


#### LL(1)

A grammar $G$ is $LL(1)$ iff whenever $A \to \alpha | \beta$ are tow distince productions of $G$ and $FIRST(\alpha) \cap FIRST(\beta) = \emptyset$; if $\epsilon \in FIRST(\beta)$, then $FIRST(\alpha) \cap FOLLOW(A) = \emptyset$

#### Predictive Parsing Table

```
For each production A -> α, to the following:
1. For each α ∈ FIRST(α), add A -> α to M[A, α]
2. If ϵ ∈ FIRST(α), then
    - for each b ∈ FOLLOW(A), add A -> α to M[A, b]
    - if $ ∈ FOLLOW(A), add A -> α to M[A, $]
Set M[A, α] to error if M[A, α] is an empty entry
```

### Nonrecursive Predictive Parsing

- Using the parsing table and a stack.


## Bottom-Up Parsing

- Shift-Reduce Parsing
    - LR(0)
    - SLR
    - LR(1)
    - LALR(1)
