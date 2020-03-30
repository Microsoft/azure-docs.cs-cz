---
title: Referenční příručka pro operace služby Azure Active Directory
description: Tato referenční příručka pro operace popisuje kontroly a akce, které byste měli provést k zabezpečení a zabezpečení správy identity a přístupu, ověřování, zásad správného řízení a operací.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535311"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Referenční příručka pro operace služby Azure Active Directory

Tato referenční příručka pro operace popisuje kontroly a akce, které byste měli provést k zabezpečení a údržbě následujících oblastí:

- **[Správa identit a přístupu](active-directory-ops-guide-iam.md)** – schopnost řídit životní cyklus identit a jejich nároky.
- **[Správa ověřování](active-directory-ops-guide-auth.md)** – schopnost spravovat přihlašovací údaje, definovat prostředí ověřování, delegovat přiřazení, měřit využití a definovat zásady přístupu založené na stavu zabezpečení rozlehlé sítě.
- **[Zásady správného řízení](active-directory-ops-guide-govern.md)** – schopnost posoudit a osvědčit přístup udělené neprivilegované a privilegované identity, audit a řízení změny prostředí.
- **[Operace](active-directory-ops-guide-ops.md)** – optimalizace operací Azure Active Directory (Azure AD).

Některá doporučení zde nemusí být použitelná pro prostředí všech zákazníků, například doporučené postupy služby AD FS nemusí platit, pokud vaše organizace používá synchronizaci hash hesel.

> [!NOTE]
> Tato doporučení jsou aktuální k datu publikování, ale mohou se v průběhu času měnit. Organizace by měly průběžně vyhodnocovat své postupy identity, jak se produkty a služby společnosti Microsoft v průběhu času vyvíjejí. Doporučení se můžou změnit, když se organizace přihlásí k odběru jiné licence Azure AD Premium. Například Azure AD Premium P2 bude obsahovat další doporučení zásad správného řízení.

## <a name="stakeholders"></a>Zúčastněné strany

Každá část v této referenční příručce doporučuje přiřadit zúčastněným stranám, aby úspěšně plánovaly a implementovaly klíčové úkoly. V následující tabulce je uveden seznam všech zúčastněných stran v této příručce:

| Účastník | Popis |
| :- | :- |
| Operační tým IAM | Tento tým zpracovává každodenní řízení systému správy identit a přístupu. |
| Tým produktivity | Tento tým vlastní a spravuje aplikace pro zvýšení produktivity, jako je e-mail, sdílení souborů a spolupráce, zasílání rychlých zpráv a konference. |
| Vlastník aplikace | Tento tým vlastní konkrétní aplikaci z firmy a obvykle z technického hlediska v organizaci. |
| Tým architektury Infosec | Tento tým plánuje a navrhuje postupy zabezpečení informací organizace. |
| Operační tým InfoSec | Tento tým spouští a monitoruje implementované postupy zabezpečení informací týmu architektury InfoSec. |

## <a name="next-steps"></a>Další kroky

Začínáme s [kontrolami a akcemi správy identit a přístupu](active-directory-ops-guide-iam.md).
