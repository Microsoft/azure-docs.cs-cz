---
title: Aspekty návrhu hybridní identity služby Azure Active Directory – přehled | Dokumenty společnosti Microsoft
description: Přehled a mapa obsahu průvodce aspekty návrhu hybridní identity
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381452"
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Důležité informace k návrhu hybridní identity Azure Active Directory
Zařízení založená na spotřebitelích se rozrůstají v podnikovém světě a aplikace s cloudovým softwarem jako službou (SaaS) se snadno přijímají. V důsledku toho je udržování kontroly nad přístupem uživatelů k aplikacím napříč interními datovými centry a cloudovými platformami náročné.  

Řešení pro správu identit od Microsoftu pokrývá místní i cloudové funkce a vytvářejí jedinou identitu uživatele pro ověřování a autorizaci u všech prostředků bez ohledu na umístění. Tento koncept se označuje jako hybridní identita. Existují různé možnosti návrhu a konfigurace pro hybridní identitu pomocí řešení společnosti Microsoft a v některých případech může být obtížné určit, která kombinace nejlépe vyhovuje potřebám vaší organizace. 

Tento průvodce aspekty návrhu hybridní identity vám pomůže pochopit, jak navrhnout řešení hybridní identity, které nejlépe vyhovuje obchodním a technologickým potřebám vaší organizace.  Tato příručka podrobně popisuje řadu kroků a úkolů, které můžete sledovat, aby vám pomohla navrhnout hybridní identity řešení, které splňuje jedinečné požadavky vaší organizace. V průběhu kroků a úkolů průvodce představí příslušné technologie a možnosti funkcí, které mají organizace k dispozici, aby splnily úroveň funkčnosti a kvality služeb (jako je dostupnost, škálovatelnost, výkon, možnosti správy a zabezpečení). Požadavky. 

Konkrétně aspekty návrhu hybridní identity, které jsou vodítkem pro dosažení cílů, jsou zodpovězení následujících otázek: 

* Jaké otázky je třeba se zeptat a odpovědět na řídit hybridní identity-specifický design pro technologii nebo problém domény, která nejlépe splňuje mé požadavky?
* Jakou posloupnost aktivit mám dokončit, abych navrhl řešení hybridní identity pro technologii nebo problémovou doménu? 
* Jaká technologie hybridní identity a možnosti konfigurace jsou k dispozici, aby mi pomohly splnit mé požadavky? Jaké jsou kompromisy mezi těmito možnostmi, abych mohl vybrat nejlepší možnost pro své podnikání?

## <a name="who-is-this-guide-intended-for"></a>Komu je tato příručka určená?
 CIO, CITO, Chief Identity Architects, Enterprise Architects a IT Architects zodpovědní za návrh řešení hybridní identity pro střední nebo velké organizace.

## <a name="how-can-this-guide-help-you"></a>V čem vám může tento průvodce pomoct?
Pomocí této příručky můžete pochopit, jak navrhnout řešení hybridní identity, které je schopno integrovat cloudový systém správy identit s aktuálním místním řešením identit. 

Následující obrázek znázorňuje příklad hybridního řešení identit, které správcům IT umožňuje spravovat integraci aktuálního řešení služby Windows Server Active Directory umístěného místně se službou Microsoft Azure Active Directory, aby uživatelé mohli používat jednotné přihlašování ( SSO) napříč aplikacemi umístěnými v cloudu a místně.

![Příklad](media/plan-hybrid-identity-design-considerations/hybridID-example.png)

Výše uvedený obrázek je příkladem řešení hybridní identity, které využívá cloudové služby k integraci s místními funkcemi, aby poskytlo jediné prostředí procesu ověřování koncovým uživatelům a usnadnilo správu IT těchto Zdroje. I když tento příklad může být běžný scénář, každý návrh hybridní identity každé organizace se pravděpodobně bude lišit od příkladu znázorněného na obrázku 1 z důvodu různých požadavků. 

Tato příručka obsahuje řadu kroků a úkolů, které můžete sledovat při návrhu řešení hybridní identity, které splňuje jedinečné požadavky vaší organizace. V následujících krocích a úkolech průvodce představuje příslušné technologie a možnosti funkcí, které máte k dispozici, abyste splnili požadavky na funkční úroveň a úroveň kvality služeb pro vaši organizaci.

**Předpoklady**: Máte určité zkušenosti se systémy Windows Server, Službou Active Directory Domain Services a Azure Active Directory. V tomto dokumentu se předpokládá, že hledáte, jak tato řešení mohou uspokojit vaše obchodní potřeby samostatně nebo v integrovaném řešení.

## <a name="design-considerations-overview"></a>Přehled aspekty návrhu
Tento dokument obsahuje sadu kroků a úkolů, které můžete sledovat při návrhu řešení hybridní identity, které nejlépe vyhovuje vašim požadavkům. Kroky uvádíme v seřazené posloupnosti. Důležité informace o návrhu, které se dozvíte v pozdějších krocích, mohou vyžadovat změnu rozhodnutí, která jste provedli v předchozích krocích, avšak kvůli protichůdným volbám návrhu. Každý pokus je upozornit na potenciální konflikty návrhu v celém dokumentu. 

K návrhu, který nejlépe vyhovuje vašim požadavkům, se dostanete až po opakování kroků tolikrát, kolikrát je to nutné k začlenění všech aspektů do dokumentu. 

| Fáze hybridní identity | Seznam témat |
| --- | --- |
| Určení požadavků na identitu |[Určení obchodních potřeb](plan-hybrid-identity-design-considerations-business-needs.md)<br> [Určení požadavků na synchronizaci adresářů](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Určení vícefaktorových požadavků na ověřování](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Definování strategie přechodu hybridní identity](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| Plán pro zvýšení zabezpečení dat prostřednictvím řešení silné identity |[Určení požadavků na ochranu údajů](plan-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Určení požadavků na správu obsahu](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Určení požadavků na řízení přístupu](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Určení požadavků na reakce na incidenty](plan-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Definování strategie ochrany údajů](plan-hybrid-identity-design-considerations-data-protection-strategy.md) |
| Plánování životního cyklu hybridní identity |[Určení úloh správy hybridní identity](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Správa synchronizace](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Určit strategii přijetí hybridní správy identit](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="next-steps"></a>Další kroky
[Určení požadavků na identitu](plan-hybrid-identity-design-considerations-business-needs.md)

