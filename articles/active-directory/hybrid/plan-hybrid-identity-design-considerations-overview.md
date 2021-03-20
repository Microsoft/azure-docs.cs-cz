---
title: Azure Active Directory informace o návrhu hybridní identity – přehled | Microsoft Docs
description: Přehled a mapa obsahu Průvodce hledisky návrhu hybridní identity
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 100509c4-0b83-4207-90c8-549ba8372cf7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7f8dd49f3668b8f68753681123a04d21edac46c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997728"
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Důležité informace k návrhu hybridní identity Azure Active Directory
Zařízení založená na zákaznících přecházejí z podnikového světa a aplikace založené na cloudovém softwaru jako služby (SaaS) se dají snadno přijmout. Výsledkem je, že udržování řízení přístupu uživatelů k aplikacím napříč interními datacentry a cloudové platformy je náročné.  

Řešení pro správu identit od Microsoftu pokrývá místní i cloudové funkce a vytvářejí jedinou identitu uživatele pro ověřování a autorizaci u všech prostředků bez ohledu na umístění. Tento koncept se označuje jako hybridní identita. Existují různé možnosti návrhu a konfigurace hybridní identity pomocí řešení Microsoftu a v některých případech může být obtížné určit, která kombinace bude nejlépe vyhovovat potřebám vaší organizace. 

Tato příručka k návrhu hybridní identity vám pomůže pochopit, jak navrhnout řešení hybridní identity, které nejlépe vyhovuje obchodním a technologickým potřebám vaší organizace.  Tato příručka podrobně popisuje řadu kroků a úloh, které vám pomůžou navrhnout řešení hybridní identity, které splňuje jedinečné požadavky vaší organizace. V průběhu kroků a úloh bude průvodce prezentovat relevantní technologie a možnosti funkcí, které jsou k dispozici organizacím, aby splnily požadavky na funkčnost a kvalitu služeb (například dostupnost, škálovatelnost, výkon, možnosti správy a zabezpečení). 

Cílem příručky pro aspekty návrhu hybridní identity je konkrétně odpovědět na následující otázky: 

* Jaké otázky potřebuji k tomu, abyste si vyžádali a odpověděli na vlastní návrh konkrétní identity pro technologii nebo doménu problému, která nejlépe splňuje moje požadavky?
* Jakou sekvenci aktivit mám dokončit, abyste mohli navrhnout řešení hybridní identity pro technologii nebo doménu problému? 
* Jaké technologie hybridní identity a možnosti konfigurace jsou k dispozici pro pomoc se splněním mých požadavků? Jaké jsou kompromisy mezi těmito možnostmi, aby bylo možné vybrat nejlepší možnost pro moji firmu?

## <a name="who-is-this-guide-intended-for"></a>Komu je tato příručka určená?
 CIO, CITO, hlavní architekti identity, podnikové architekti a architekti IT, kteří zodpovídají za návrh řešení hybridní identity pro střední nebo velké organizace.

## <a name="how-can-this-guide-help-you"></a>V čem vám může tento průvodce pomoct?
Tento průvodce vám pomůže pochopit, jak navrhnout řešení hybridní identity, které umožňuje integrovat cloudový systém správy identit do vašeho aktuálního místního řešení identity. 

Následující obrázek znázorňuje příklad řešení hybridní identity, které umožňuje správcům IT spravovat jejich aktuální řešení služby Active Directory Windows serveru v místním prostředí s Microsoft Azure Active Directory, aby uživatelé mohli používat jednotné Sign-On (SSO) v aplikacích, které se nacházejí v cloudu a místně.

![Příklad](media/plan-hybrid-identity-design-considerations/hybridID-example.png)

Výše uvedená ilustrace je příkladem řešení hybridní identity, které využívá cloudové služby pro integraci s místními funkcemi, aby poskytovala jednotné prostředí pro účely procesu ověřování koncovým uživatelem a usnadnila správu těchto prostředků. I když tento příklad může být běžným scénářem, je pravděpodobně jiný návrh hybridní identity každé organizace jiný než příklad znázorněný na obrázku 1 z důvodu různých požadavků. 

Tato příručka nabízí řadu kroků a úloh, pomocí kterých můžete navrhnout řešení hybridní identity, které splňuje jedinečné požadavky vaší organizace. V následujících krocích a úlohách příručka prezentují relevantní technologie a možnosti funkcí, které vám umožní splnit požadavky vaší organizace na funkční a provozní úroveň kvality.

**Předpoklady**: máte nějaké zkušenosti s Windows serverem, Active Directory Domain Services a Azure Active Directory. V tomto dokumentu se předpokládá, že hledáte, jak tato řešení můžou vyhovovat vašim obchodním potřebám, a to v integrovaném řešení.

## <a name="design-considerations-overview"></a>Přehled otázek návrhu
Tento dokument poskytuje sadu kroků a úloh, pomocí kterých můžete navrhnout řešení hybridní identity, které nejlépe vyhovuje vašim požadavkům. Kroky uvádíme v seřazené posloupnosti. Aspekty návrhu, které se naučíte v pozdějších krocích, můžou vyžadovat, abyste změnili rozhodnutí, která jste udělali v předchozích krocích, ale kvůli konfliktním volbám návrhu. Dojde k pokusu o spuštění upozornění na potenciální konflikty návrhu v celém dokumentu. 

K návrhu, který nejlépe vyhovuje vašim požadavkům, se dostanete až po provedení iterace v rámci kroků tolikrát, kolik je potřeba, aby se zahrnuly všechny důležité podmínky v dokumentu. 

| Fáze hybridní identity | Seznam témat |
| --- | --- |
| Určení požadavků na identitu |[Určení obchodních potřeb](plan-hybrid-identity-design-considerations-business-needs.md)<br> [Určení požadavků na synchronizaci adresářů](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Určení požadavků na službu Multi-Factor Authentication](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Definice strategie přijetí hybridní identity](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| Plánování vylepšení zabezpečení dat prostřednictvím řešení silné identity |[Určení požadavků na ochranu dat](plan-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Určení požadavků na správu obsahu](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Určení požadavků na řízení přístupu](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Určení požadavků na reakce na incidenty](plan-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Definovat strategii ochrany dat](plan-hybrid-identity-design-considerations-data-protection-strategy.md) |
| Plánování životního cyklu hybridní identity |[Určení úloh správy hybridních identit](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Správa synchronizace](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Určení strategie přijetí správy hybridních identit](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="next-steps"></a>Další kroky
[Určení požadavků na identitu](plan-hybrid-identity-design-considerations-business-needs.md)

