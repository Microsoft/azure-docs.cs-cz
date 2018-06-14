---
title: Pravidla ve službě Azure CDN modul podmíněné výrazy | Microsoft Docs
description: Referenční dokumentace pro Azure CDN pravidla shody stav motoru a funkce.
services: cdn
documentationcenter: ''
author: Lichard
manager: akucer
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 57e56c38e003cb83dcf44f455c4451d159db8a59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23842927"
---
# <a name="azure-cdn-rules-engine-conditional-expressions"></a>Pravidla ve službě Azure CDN modul podmíněné výrazy
Toto téma obsahuje podrobné popisy podmíněné výrazy pro Azure Content Delivery Network (CDN) [stroj pravidel](cdn-rules-engine.md).

První část pravidla je podmíněným výrazem.

Podmíněným výrazem | Popis
-----------------------|-------------
POKUD | Výraz IF je vždy součástí první příkaz v pravidle. Stejně jako všechny ostatní podmíněné výrazy musí být tento příkaz IF přidružený shody. Pokud jsou definovány žádné další podmíněné výrazy, určuje toto porovnání kritérium, které je nutné splnit před sadu funkcí, je možné používat na žádost.
A POKUD | Výraz a v případě lze přidat pouze po následující typy podmíněné výrazy: IF, a v případě. Ho znamená, že existuje jiný stav, který musí být splněné počáteční Pokud příkaz.
POKUD JINÝ| Výraz ELSE IF Určuje alternativní podmínku, která je nutné splnit před sadu funkcí, které jsou specifické pro tento výraz ELSE když probíhá. Přítomnost příkazu ELSE IF označuje konec předchozí příkaz. Pouze podmíněným výrazem, který se může použít jiný příkaz ELSE IF po příkazu ELSE IF. To znamená, že příkaz ELSE IF lze použít pouze k určení jeden další podmínku, která musí být splněny.

**Příklad**: ![CDN vyhovují podmínce](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Následující pravidlo může přepsat akce zadané předchozí pravidlem. Příklad: Catch všechna pravidla zabezpečuje všechny požadavky na základě tokenu ověřování. Přímo pod ním vytvářet výjimky pro určité typy požadavků může vytvořit jiné pravidlo.

### <a name="next-steps"></a>Další kroky
* [Přehled Azure CDN](cdn-overview.md)
* [Referenční dokumentace pravidel modulu](cdn-rules-engine-reference.md)
* [Stav shody motoru pravidla](cdn-rules-engine-reference-match-conditions.md)
* [Funkce modulu pravidla](cdn-rules-engine-reference-features.md)
* [Přepsání výchozího nastavení HTTP používá stroj pravidel](cdn-rules-engine.md)
