% Notes du module de grammaires lexicalisées
% Hugo Mougard
 
Grammaires lexicalisées
=======================

Une grammaire lexicalisée est une grammaire dont toutes les règles
contiennent au moins un terminal.


Forme normale de Greibach
=========================

Concerne les langages propres (sans ε) dont les grammaires (propres
elles aussi, ou réduites) sont sous la forme

    V → XV*

où `X` est terminal et `V` non-terminal.

Par exemple, voici une grammaire lexicale :

    S → aST | bUU | a
    T → bT | cSCU | cTU
    U → aU | bC
    C → c

Et l'arbre de parsing de la phrase `abbcabcbcacabc` en utilisant cette
grammaire :
 
$$
\Tree
[.S a
    [.S b
        [.U b [.C c ] ]
        [.U a [.U b [.C c ] ] ]
    ]
    [.T b
        [.T c
            [.S a ]
            [.C c ]
            [.U a [.U b [.C c ] ] ]
        ]
    ]
]
$$

On peut aussi « inverser » la grammaire pour la centrer sur les
terminaux :

    a    → S
    aST  → S
    aU   → U
    bUU  → S
    bT   → T
    bC   → U
    cSCU → T
    cTU  → T
    c    → C

Traduction en automate à piles :
--------------------------------

En utilisant la grammaire inversée ci-dessus, on peut produire le
tableau ci-dessous :

$$
\begin{tabular}{rp{1cm}p{1cm}p{1cm}p{1cm}}
  & S   & T & U & C \\ \hline
a & ε   &   & U &   \\
  & ST  &   &   &   \\ \hline
b & UU  & T & C &   \\ \hline
c & SCU &   &   & ε \\
  & TU  &   &   &   \\
\end{tabular}
$$

Le tableau se lit, par exemple pour la case (a, S) : on peut dépiler S
si on lit a et qu'on empile ε ou T puis S.

Exemple d'exécution :

    bande : abb, pile S
    → lit a
    → bande : bb, dépile S, empile ST, pile ST
    → lit b
    → bande : b,  dépile S, empile UU, pile UUT
    → lit b
    → bande : #,  dépile U, empile C,  pile CUT

Transformation en forme normale de Greibach :
---------------------------------------------

Tout d'abord, il faut noter quels sont les problèmes :

- les règles produisant ε ne sont pas tolérées (on vise une grammaire
propre)
- les règles récursives à gauche ne sont pas tolérées

Voici maintenant l'algorithme pour transformer les règles récursives à
gauche en règles récursives à droite :

Pour tout V~i~ non terminal, on introduit V~i~' puis, on transforme
tout règle de la forme :

V~j~ → V~i~m~1~ | V~i~m~2~ | ... | V~i~m~p~ | w~1~ | w~2~ | ... | w~q~

en

V~j~ → w~1~V~i~' | w~2~V~i~' | ... | w~q~V~i~'

et

V~i~' → m~1~V~i~' | m~2~V~i~' | ... | m~p~V~i~' | m~1~ | m~2~ | ... | m~p~

Exemple :

    A → BC
    B → AB | a
    C → AC | b
