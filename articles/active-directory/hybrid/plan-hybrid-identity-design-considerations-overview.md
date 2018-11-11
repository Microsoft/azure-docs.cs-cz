---
title: Azure Active Directory faktory návrhu hybridních identit – přehled | Dokumentace Microsoftu
description: Přehled a mapa obsahu z Průvodce aspekty návrhu hybridní Identity
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: 100509c4-0b83-4207-90c8-549ba8372cf7
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: d85157c3f1aafd33a2b996168f2f9eac613a209c
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344878"
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Důležité informace k návrhu hybridní identity Azure Active Directory
Zařízení se systémem příjemce se proliferating světě a se snadno přijmout cloudové aplikace software jako služba (SaaS). V důsledku toho je náročné mít pod kontrolou přístupu uživatelů aplikace mezi interní datacentry a cloudovými platformami.  

Řešení pro správu identit od Microsoftu pokrývá místní i cloudové funkce a vytvářejí jedinou identitu uživatele pro ověřování a autorizaci u všech prostředků bez ohledu na umístění. Tento koncept se označuje jako hybridní identita. Existují různé návrhu a možnosti konfigurace pro hybridní identitu pomocí řešení Microsoftu a v některých případech může být obtížné určit, která kombinace bude nejlépe vyhovovat potřebám vaší organizace. 

Tento průvodce aspekty návrhu hybridní Identity vám pomůže pochopit, jak k navržení řešení hybridní identity, která nejlíp odpovídá podnikání a technologickým požadavkům vaší organizace.  Tento průvodce Detailně popisuje sérii kroků a úloh, které můžete provést při návrhu řešení hybridní identity, který splňuje jedinečné požadavky vaší organizace. Ve všech krocích a úlohách, bude prezentovat relevantní technologie a funkce Možnosti dostupné pro organizace ke splnění funkčních a kvalitu služeb (například dostupnost, škálovatelnost, výkon, možnosti správy a zabezpečení) v příručce úroveň požadavky. 

Konkrétně cílů Průvodce důležité informace o návrhu hybridní identity se odpovědět na následující otázky: 

* Jaké otázky potřebuji klást otázky a odpovědi k podpoře návrhu hybridní identity specifické technologii nebo doméně problému, která nejlépe splňuje mé požadavky?
* Jakou posloupnost aktivit by se měla dokončit navrhnout řešení s hybridní identitou technologii nebo doménu problému? 
* Jaké hybridní identity technologické a konfigurační možnosti jsou dostupné ke splnění mých požadavků? Jaké jsou kompromisy mezi těmito možnostmi tak, aby se dalo vybrat nejlepší možnost pro firmy?

## <a name="who-is-this-guide-intended-for"></a>Komu je tato příručka určená?
 Doporučení, CITO, Chief Identity architekty, podnikové architekty a architekti informačních technologií zodpovědní za návrhy řešení s hybridní identitou pro střední a velké organizace.

## <a name="how-can-this-guide-help-you"></a>Jak vám může tento průvodce pomoct?
Tento průvodce můžete porozumět procesu návrhu řešení hybridní identity, aby bylo možné integrovat vaše aktuální řešení identit v místním systému pro správu identit v cloudu. 

Následující obrázek ukazuje příklad řešení s hybridní identitou, která umožňuje správcům IT spravovat jejich aktuální Windows Server Active Directory řešení, které jsou umístěny v místním integrovat Microsoft Azure Active Directory a umožníte uživatelům používat jednotné přihlašování ( Jednotné přihlašování) napříč aplikacemi, které jsou umístěné v cloudu i lokálně.

![Příklad:](media/plan-hybrid-identity-design-considerations/hybridID-example.png)

Na obrázku výše je příkladem řešení s hybridní identitou, která se využívají cloudové služby pro integraci místních funkcí, aby bylo možné poskytovat jednotné prostředí pro proces ověřování koncového uživatele a usnadnit správu těch IT prostředky. I v tomto příkladu může být běžný scénář, bude pravděpodobně lišit od příkladu znázorněno na obrázku 1 kvůli různým požadavkům návrhu hybridní identity každý organizace. 

Tato příručka obsahuje řadu kroků a úloh, které můžete provést k navržení řešení hybridní identity, který splňuje jedinečné požadavky vaší organizace. V následujících krocích a úlohách uvádí Průvodce relevantní technologie a možnosti funkcí, které jsou k dispozici pro splnění funkčních a úrovni požadavky na kvalitu služeb pro vaši organizaci.

**Předpoklady**: máte nějaké zkušenosti s Windows serverem, Active Directory Domain Services a Azure Active Directory. V tomto dokumentu se předpokládá, že hledáte způsob tato řešení můžete svých obchodních potřeb sami, nebo integrované řešení.

## <a name="design-considerations-overview"></a>Přehled aspektů návrhu
Tento dokument obsahuje sadu kroků a úloh, které můžete provést k navržení řešení hybridní identity, která nejlépe vyhovuje vašim požadavkům. Kroky uvádíme v seřazené posloupnosti. Aspekty návrhu, které se seznámíte v dalších krocích se může vyžadovat, budete muset změnit rozhodnutí, které jste udělali v dřívějších krocích, ale kvůli konfliktním volbám návrhu. Každý pokusu o vytvoření vás upozorní na potenciální konflikty návrhu v celém dokumentu. 

Dostanete se na design, která nejlépe splňuje vaše požadavky dopracujete že kroky projdete tolikrát, kolikrát podle potřeby, abyste zapracovali všechny aspekty uvedené v dokumentu. 

| Hybridní identita fáze | Seznam témat |
| --- | --- |
| Určení požadavků na identitu |[Určit, potřeb firmy](plan-hybrid-identity-design-considerations-business-needs.md)<br> [Určete požadavky na synchronizaci adresáře](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Určení požadavků na ověření službou Multi-Factor Authentication](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Definování strategie přijetí hybridní identity](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| Plán pro zlepšení zabezpečení dat prostřednictvím řešení využívá silné identity |[Určení požadavků na ochranu dat](plan-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Určení požadavků na správu obsahu](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Určete požadavky na řízení přístupu](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Určení požadavků na reakce na incidenty](plan-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Definování strategie ochrany dat](plan-hybrid-identity-design-considerations-data-protection-strategy.md) |
| Plánování životního cyklu hybridní identity |[Určení úloh správy hybridní identity](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Správa synchronizace](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Určení strategie přijetí správy hybridní identity](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="next-steps"></a>Další kroky
[Určení požadavků na identity](plan-hybrid-identity-design-considerations-business-needs.md)

