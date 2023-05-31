# Lorem Ipsum
> Neque porro quisquam est qui dolorem ipsum quia dolor sit amet, consectetur, adipisci velit..

## What is Lorem Ipsum?
**Lorem Ipsum** is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronic typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem Ipsum.

- Lorem ipsum dolor sit amet, consectetur adipiscing elit.
- Sed bibendum sem sollicitudin lobortis dapibus.
  - Sed venenatis tellus vulputate est congue aliquet.
  - Donec eu lacus tincidunt massa hendrerit imperdiet quis quis nisl.
  - Pellentesque at augue eget nulla hendrerit scelerisque.

## Stories

### Russian Spy
There are three people **A B C**, 2 Germans **G**, 1 Russian spy **R**: <br>$(RA \land GB \land GC) \lor (GA \land RB \land GC) \lor (GA \land GB \land RC)$
Russian is **Spy**:
$(RA \rightarrow SpyA) \land (RB \rightarrow SpyB) \land (RC \rightarrow SpyC)$
**A** is as German as **B** is Russian
$GA \leftrightarrow RB$
hidden: Russian is not German
$(RA \leftrightarrow ¬GA) \land (RB \leftrightarrow ¬GB) \land (RC \leftrightarrow ¬GC)$
we need to show **C** is not a Russian spy
$RC \land SpyC$
$\therefore$ If set of formulas is not satisfiable, then Person C cannot be a Russian Spy

### Sudoku

Introduce $9 \times 9 \times 9 = 729$ variables , use $V_{rcd}$ to represent board $V$ at row $r$ and column $c$ contains digit $d$.
**Each cell contains at least 1 digit**
$\left( V_{rc1} \vee  \dots \vee V_{rc9}\right)$
**Each cell contains at most 1 digit**
$\left( \neg V_{rc1} \vee \neg V_{rc2}\right) \wedge \left( \neg V_{rc1} \vee \neg V_{rc3}\right) \wedge \dots \wedge \left( \neg V_{rc8} \vee \neg V_{rc9}\right)$
**Every row must contain different digits**
$\{ \neg V_{r',c,d} \vee \neg V_{r',c,d}\  |\  r, r', c, d \in \{ 1, \dots, 9 \}, c < c' \}$
**Every column must contain different digits**
$\{ \neg V_{r,c,d} \vee \neg V_{r,c',d}\  |\  r, c, c', d \in \{ 1, \dots, 9 \}, c < c' \}$
**Every 3x3 square must contain one of each digits**
similar.
**Finally we add initial configuration**
e.g. $V_{129}$.

$\therefore$ If the set of formulas is satisfiable then the given sudoku with initial config has a solution.

### Loop the Loop
We first number the rows and columns.
Let $V_{ij}$ be there is a vertical line between $node_{ij}$ and $node_{ij+1}$
Let $H_{ij}$ be there is a horizontal line between $node_{ij}$ and $node_{ij+1}$
Then we can encode exactly $n$ lines around a number $p$ like 
$T_{=n}(v_{15}, v_{25}, h_{15}, h_{25})$
**However, there is no simple way of expressing single loop in propositional logic**

## Lemma
Monotonicity Replacement <br />
- Polarity act as sign here, it will invert the interpretation if it is negative<br />
- If $pol(A, π) = 1$, then $I(B) ≤ I(B')$ implies $I(A[B]π) ≤ I(A[B']π)$<br />
- If $pol(A, π) = −1$, then $I(B') ≤ I(B)$ implies $I(A[B]π) ≤ I(A[B']π)$<br />

## Expected Workflow
#### Expected Work flow

```mermaid
flowchart LR

lead["<font size=6>Artist"]
keyframes["<font size=6>Keyframes"]
roughframes["<font size=6>Rough Frames"]
genframes["<font size=6>Generate Frames\n(RNN, AE, MV)"]
cleanup_matting["<font size=6>Clean Up & Matting\n(GAN, CNN, VQVAE)\nUpsampling"]
finaloutput["<font size=6>Output Frames"]
timingnotes["<font size=6>Timing Notes"] 
linestyle["<font size=6>Overall Line Style"]
tag["<font size=6>Optional Tags"]

lead -->|<font size=6>interactive edit| roughframes
lead -->|<font size=6>draw| keyframes --> genframes
lead  -->|<font size=6>select| tag & linestyle & timingnotes
roughframes --> cleanup_matting
timingnotes --> genframes
genframes <--> roughframes
tag & linestyle --> cleanup_matting --> finaloutput


subgraph  <font size=6>Layout
 keyframes
end

subgraph <font size=6>ML Techniques
	

     subgraph <font size=6>In-betweening
     genframes
     end

     subgraph <font size=6>CleanUp & Matting
     cleanup_matting
     end
     
end
```


