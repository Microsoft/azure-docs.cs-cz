---
title: zahrnout soubor
description: zahrnout soubor
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 03/04/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2459cf6c5055dcde83dccf37addc160aeeaa64ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198645"
---
Domény událostí se nejsnadněji vysvětlí pomocí příkladu. Řekněme, že spouštíte výrobní stroj contoso, kde vytváříte traktory, prozkoumá vybavení a další těžká zařízení. V rámci provozu firmy zadáváte zákazníkům informace o údržbě zařízení, stavu systémů a aktualizacích smluv v reálném čase. Všechny tyto informace se přenášejí do různých koncových bodů, včetně vaší aplikace, koncových bodů zákazníka a jiné infrastruktury, kterou si zákazníci nastavili.

Domény událostí umožňují modelovat strojová konstrukce společnosti Contoso jako jednu entitu událostí. Každý z vašich zákazníků je reprezentován jako téma v rámci domény. Ověřování a autorizace jsou zpracovávány pomocí Azure Active Directory. Každý z vašich zákazníků se může přihlásit k odběru svého tématu a získat jejich události do nich. Spravovaný přístup prostřednictvím domény události zajišťuje, že budou moct přistupovat jenom k jejich tématu.

Poskytuje taky jeden koncový bod, na který můžete publikovat všechny události zákazníků. Event Grid se postará o to, aby každé téma bylo důležité pouze o událostech, které jsou vymezeny pro svého tenanta.

:::image type="content" source="./media/event-grid-domain-example-use-case/contoso-construction-example.png" alt-text="Příklad konstrukce contoso" lightbox="./media/event-grid-domain-example-use-case/contoso-construction-example.png":::