---
title: Equivalences of cubes
---

```agda
{-# OPTIONS --without-K --exact-split #-}

module univalent-combinatorics.equivalences-cubes where

open import elementary-number-theory.natural-numbers using (ℕ; zero-ℕ; succ-ℕ)

open import foundation.contractible-types using (is-contr)
open import foundation.dependent-pair-types using (Σ; pair; pr1; pr2)
open import foundation.equality-dependent-function-types using
  ( is-contr-total-Eq-Π)
open import foundation.equivalences using
  ( _≃_; map-equiv; id-equiv; is-equiv; map-inv-is-equiv; _∘e_;
    is-contr-total-htpy-equiv; htpy-equiv)
open import foundation.functions using (_∘_)
open import foundation.fundamental-theorem-of-identity-types using
  ( fundamental-theorem-id)
open import foundation.homotopies using (_~_; refl-htpy)
open import foundation.identity-types using (Id; tr; refl)
open import foundation.structure-identity-principle using
  ( is-contr-total-Eq-structure)
open import foundation.universe-levels using (UU; lzero)

open import univalent-combinatorics.cubes using
  ( cube; dim-cube; axis-cube; dim-cube-UU-Fin; axis-cube-UU-2; standard-cube)
open import univalent-combinatorics.finite-types using
  ( type-UU-Fin; UU-Fin; equiv-UU-Fin; is-contr-total-equiv-UU-Fin;
    id-equiv-UU-Fin)
```

## Definitions

### Equivalences of cubes

```agda
equiv-cube : {k : ℕ} → cube k → cube k → UU lzero
equiv-cube {k} X Y =
  Σ ( (dim-cube X) ≃ (dim-cube Y))
    ( λ e → (x : dim-cube X) → (axis-cube X x) ≃ (axis-cube Y (map-equiv e x)))

dim-equiv-cube :
  {k : ℕ} (X Y : cube k) → equiv-cube X Y → dim-cube X ≃ dim-cube Y
dim-equiv-cube X Y e = pr1 e

map-dim-equiv-cube :
  {k : ℕ} (X Y : cube k) → equiv-cube X Y → dim-cube X → dim-cube Y
map-dim-equiv-cube X Y e = map-equiv (dim-equiv-cube X Y e)

axis-equiv-cube :
  {k : ℕ} (X Y : cube k) (e : equiv-cube X Y) (d : dim-cube X) →
  axis-cube X d ≃ axis-cube Y (map-dim-equiv-cube X Y e d)
axis-equiv-cube X Y e = pr2 e

map-axis-equiv-cube :
  {k : ℕ} (X Y : cube k) (e : equiv-cube X Y) (d : dim-cube X) →
  axis-cube X d → axis-cube Y (map-dim-equiv-cube X Y e d)
map-axis-equiv-cube X Y e d =
  map-equiv (axis-equiv-cube X Y e d)

id-equiv-cube :
  {k : ℕ} (X : cube k) → equiv-cube X X
id-equiv-cube X = pair id-equiv (λ x → id-equiv)

equiv-eq-cube :
  {k : ℕ} {X Y : cube k} → Id X Y → equiv-cube X Y
equiv-eq-cube {k} {X} refl = id-equiv-cube X

is-contr-total-equiv-cube :
  {k : ℕ} (X : cube k) → is-contr (Σ (cube k) (equiv-cube X))
is-contr-total-equiv-cube X =
  is-contr-total-Eq-structure
    ( λ D (A : type-UU-Fin D → UU-Fin 2)
          (e : equiv-UU-Fin (dim-cube-UU-Fin X) D) →
          (i : dim-cube X) → axis-cube X i ≃ pr1 (A (map-equiv e i)))
    ( is-contr-total-equiv-UU-Fin (dim-cube-UU-Fin X))
    ( pair (dim-cube-UU-Fin X) (id-equiv-UU-Fin (dim-cube-UU-Fin X)))
    ( is-contr-total-Eq-Π
      ( λ i (A : UU-Fin 2) → equiv-UU-Fin (axis-cube-UU-2 X i) A)
      ( λ i → is-contr-total-equiv-UU-Fin (axis-cube-UU-2 X i)))

is-equiv-equiv-eq-cube :
  {k : ℕ} (X Y : cube k) → is-equiv (equiv-eq-cube {k} {X} {Y})
is-equiv-equiv-eq-cube X =
  fundamental-theorem-id X
    ( id-equiv-cube X)
    ( is-contr-total-equiv-cube X)
    ( λ Y → equiv-eq-cube {X = X} {Y})

eq-equiv-cube :
  {k : ℕ} (X Y : cube k) → equiv-cube X Y → Id X Y
eq-equiv-cube X Y =
  map-inv-is-equiv (is-equiv-equiv-eq-cube X Y)

comp-equiv-cube :
  {k : ℕ} (X Y Z : cube k) → equiv-cube Y Z → equiv-cube X Y → equiv-cube X Z
comp-equiv-cube X Y Z (pair dim-f axis-f) (pair dim-e axis-e) =
  pair (dim-f ∘e dim-e) (λ d → axis-f (map-equiv (dim-e) d) ∘e axis-e d)

htpy-equiv-cube :
  {k : ℕ} (X Y : cube k) (e f : equiv-cube X Y) → UU lzero
htpy-equiv-cube X Y e f =
  Σ ( map-dim-equiv-cube X Y e ~ map-dim-equiv-cube X Y f)
    ( λ H → (d : dim-cube X) →
            ( tr (axis-cube Y) (H d) ∘ map-axis-equiv-cube X Y e d) ~
            ( map-axis-equiv-cube X Y f d))

refl-htpy-equiv-cube :
  {k : ℕ} (X Y : cube k) (e : equiv-cube X Y) → htpy-equiv-cube X Y e e
refl-htpy-equiv-cube X Y e = pair refl-htpy (λ d → refl-htpy)

htpy-eq-equiv-cube :
  {k : ℕ} (X Y : cube k) (e f : equiv-cube X Y) →
  Id e f → htpy-equiv-cube X Y e f
htpy-eq-equiv-cube X Y e .e refl = refl-htpy-equiv-cube X Y e

is-contr-total-htpy-equiv-cube :
  {k : ℕ} (X Y : cube k) (e : equiv-cube X Y) →
  is-contr (Σ (equiv-cube X Y) (htpy-equiv-cube X Y e))
is-contr-total-htpy-equiv-cube X Y e =
  is-contr-total-Eq-structure
    ( λ α β H →
      ( d : dim-cube X) →
      ( tr (axis-cube Y) (H d) ∘ map-axis-equiv-cube X Y e d) ~
      ( map-equiv (β d)))
    ( is-contr-total-htpy-equiv (dim-equiv-cube X Y e))
    ( pair (dim-equiv-cube X Y e) refl-htpy)
    ( is-contr-total-Eq-Π
      ( λ d β → htpy-equiv (axis-equiv-cube X Y e d) β)
      ( λ d → is-contr-total-htpy-equiv (axis-equiv-cube X Y e d)))

is-equiv-htpy-eq-equiv-cube :
  {k : ℕ} (X Y : cube k) (e f : equiv-cube X Y) →
  is-equiv (htpy-eq-equiv-cube X Y e f)
is-equiv-htpy-eq-equiv-cube X Y e =
  fundamental-theorem-id e
    ( refl-htpy-equiv-cube X Y e)
    ( is-contr-total-htpy-equiv-cube X Y e)
    ( htpy-eq-equiv-cube X Y e)

eq-htpy-equiv-cube :
  {k : ℕ} (X Y : cube k) (e f : equiv-cube X Y) →
  htpy-equiv-cube X Y e f → Id e f
eq-htpy-equiv-cube X Y e f =
  map-inv-is-equiv (is-equiv-htpy-eq-equiv-cube X Y e f)
```

### Labelings of cubes

```agda
labelling-cube : {k : ℕ} (X : cube k) → UU lzero
labelling-cube {k} X = equiv-cube (standard-cube k) X
```
