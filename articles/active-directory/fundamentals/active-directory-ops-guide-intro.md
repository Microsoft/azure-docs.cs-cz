---
title: Referenční příručka k operacím s Azure Active Directory
description: Referenční příručka operací popisuje kontroly a akce, které byste měli provést při zabezpečení a údržbě správy identit a přístupu, ověřování, zásad správného řízení a operací.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: c815256e60e5ca6582220d247d587a4488c4a55a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74535311"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Referenční příručka k operacím s Azure Active Directory

Referenční příručka operací popisuje kontroly a akce, které byste měli provést při zabezpečení a údržbě těchto oblastí:

- **[Správa identit a přístupu](active-directory-ops-guide-iam.md)** – schopnost spravovat životní cyklus identit a jejich nároků.
- **[Správa ověřování](active-directory-ops-guide-auth.md)** – možnost spravovat přihlašovací údaje, definovat možnosti ověřování, přiřazení delegáta, využití měr a definovat zásady přístupu na základě podnikového zabezpečení stav.
- Možnost **[správného řízení](active-directory-ops-guide-govern.md)** – možnost posoudit a ověřit přístup k neprivilegovaným a privilegovaným identitám, auditům a řízením změn v prostředí.
- **[Operace](active-directory-ops-guide-ops.md)** – optimalizuje provozní Azure Active Directory (Azure AD).

Některá doporučení se nemusí vztahovat na prostředí všech zákazníků, například AD FS osvědčené postupy nemusí platit, pokud vaše organizace používá synchronizaci hodnot hash hesel.

> [!NOTE]
> Tato doporučení jsou aktuální k datu publikování, ale mohou se v průběhu času měnit. Organizace by měly průběžně vyhodnocovat své postupy své identity, protože produkty a služby Microsoftu se v průběhu času vyvíjí. Doporučení se můžou změnit, když se organizace přihlásí k odběru jiné licence Azure AD Premium. Například Azure AD Premium P2 bude zahrnovat více doporučení zásad správného řízení.

## <a name="stakeholders"></a>Držitel

V každé části tohoto referenčního průvodce se doporučuje přiřadit zúčastněné strany k úspěšnému naplánování a implementaci klíčových úloh. Následující tabulka obsahuje seznam všech zúčastněných účastníků v této příručce:

| Účastník | Popis |
| :- | :- |
| Provozní tým IAM | Tento tým zpracovává každodenní provoz systému správy identit a přístupu denně. |
| Tým pro produktivitu | Tento tým je vlastníkem a spravuje aplikace produktivity, jako je e-mail, sdílení souborů a spolupráce, rychlé zasílání zpráv a konference. |
| Vlastník aplikace | Tento tým vlastní konkrétní aplikaci z firmy a obvykle je technickou perspektivou v organizaci. |
| Tým architektury InfoSec | Tento tým plánuje a navrhuje postupy zabezpečení informací v organizaci. |
| Provozní tým InfoSec | Tento tým používá a sleduje implementované postupy zabezpečení informací týmu architektury InfoSec. |

## <a name="next-steps"></a>Další kroky

Začněte s [kontrolami a akcemi správy identit a přístupu](active-directory-ops-guide-iam.md).
