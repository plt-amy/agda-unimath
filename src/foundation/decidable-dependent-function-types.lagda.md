---
title: Decidability of dependent function types
---

```agda
{-# OPTIONS --without-K --exact-split #-}

module foundation.decidable-dependent-function-types where

open import foundation.coproduct-types using (coprod; inl; inr)
open import foundation.decidable-types using
  ( is-decidable; is-decidable-equiv; is-decidable-prod; is-decidable-equiv')
open import foundation.equivalences using (_≃_; map-equiv)
open import foundation.functoriality-dependent-function-types using
  ( equiv-Π)
open import foundation.maybe using (Maybe; unit-Maybe; exception-Maybe)
open import foundation.universal-property-coproduct-types using
  ( equiv-dependent-universal-property-coprod)
open import foundation.universal-property-maybe using
  ( equiv-dependent-universal-property-Maybe)
open import foundation.universe-levels using (Level; UU)
```

## Idea

We describe conditions under which dependent products are decidable.

### Decidablitilty of dependent products over coproducts

```agda
is-decidable-Π-coprod :
  {l1 l2 l3 : Level} {A : UU l1} {B : UU l2} {C : coprod A B → UU l3} →
  is-decidable ((a : A) → C (inl a)) → is-decidable ((b : B) → C (inr b)) →
  is-decidable ((x : coprod A B) → C x)
is-decidable-Π-coprod {C = C} dA dB =
  is-decidable-equiv
    ( equiv-dependent-universal-property-coprod C)
    ( is-decidable-prod dA dB)
```

### Decidability of dependent products over `Maybe`.

```agda
is-decidable-Π-Maybe :
  {l1 l2 : Level} {A : UU l1} {B : Maybe A → UU l2} →
  is-decidable ((x : A) → B (unit-Maybe x)) → is-decidable (B exception-Maybe) →
  is-decidable ((x : Maybe A) → B x)
is-decidable-Π-Maybe {B = B} du de =
  is-decidable-equiv
    ( equiv-dependent-universal-property-Maybe B)
    ( is-decidable-prod du de)
```

### Decidability of dependent products over an equivalence

```agda
is-decidable-Π-equiv :
  {l1 l2 l3 l4 : Level} {A : UU l1} {B : UU l2} {C : A → UU l3} {D : B → UU l4}
  (e : A ≃ B) (f : (x : A) → C x ≃ D (map-equiv e x)) →
  is-decidable ((x : A) → C x) → is-decidable ((y : B) → D y)
is-decidable-Π-equiv {D = D} e f = is-decidable-equiv' (equiv-Π D e f)

is-decidable-Π-equiv' :
  {l1 l2 l3 l4 : Level} {A : UU l1} {B : UU l2} {C : A → UU l3} {D : B → UU l4}
  (e : A ≃ B) (f : (x : A) → C x ≃ D (map-equiv e x)) →
  is-decidable ((y : B) → D y) → is-decidable ((x : A) → C x)
is-decidable-Π-equiv' {D = D} e f = is-decidable-equiv (equiv-Π D e f)
```
