---
title: Azure Active Directory hybridní identity aspekty návrhu – přehled | Microsoft Docs
description: Přehled a obsahu mapy Průvodce aspekty návrhu hybridní Identity
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
ms.openlocfilehash: e81908e3fd77b8fde706b27c3bed305ad0436677
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801632"
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Důležité informace k návrhu hybridní identity Azure Active Directory
Zařízení se systémem příjemce jsou proliferating podnikové world a cloudové aplikace software jako služba (SaaS) se dají snadno přijmout. V důsledku toho je náročné zachování kontroly nad přístup k aplikaci uživatelů napříč platformami interní datová centra a cloudu.  

Řešení společnosti Microsoft identity span místní a cloudové schopnosti, vytváření identitu jednoho uživatele pro ověřování a autorizaci pro všechny prostředky, bez ohledu na umístění. Tento koncept se říká hybridní identita. Existují různé návrhu a možnosti konfigurace pro hybridní identita pomocí řešení Microsoft a v některých případech může být obtížné určit, která kombinace bude nejlépe vyhovovat potřebám vaší organizace. 

Tento průvodce aspekty návrhu hybridní Identity vám pomůže pochopit, jak k navržení řešení hybridní identity, nejlépe vyhovuje firmy a technologickým požadavkům vaší organizace.  Tento průvodce Detailně popisuje sérii kroků a úloh, které vám pomůžou při návrhu řešení hybridní identity, který splňuje požadavky vaší organizace jedinečný. V krocích a úlohách, bude k dispozici relevantní technologie a funkce možností organizace k dispozici funkční úroveň a kvalitu služeb (například dostupnost, škálovatelnost, výkon, možnosti správy a zabezpečení) v Průvodci úroveň požadavky. 

Konkrétně je cílů Průvodce aspekty návrhu hybridní identity odpovězte si na následující otázky: 

* Jaké otázky potřebuji zodpovědět k dovedení navrhnout hybridní identity specifické technologii nebo doméně problému, nejlépe splňuje mé požadavky a požádejte?
* Jakou posloupnost aktivit, by se měla dokončit k navržení řešení hybridní identity pro technologii nebo doménu problému? 
* Jaké hybridní identity technologické a konfigurační možnosti jsou dostupné ke splnění mých požadavků? Jaké jsou kompromisy mezi těmito možnostmi, aby bylo možné vybrat nejlepší možnost pro firmy?

## <a name="who-is-this-guide-intended-for"></a>Komu je tato příručka určená?
 Ředitel IT, společností CITO, hlavní Identity architektům, Enterprise architekty a architekti informačních technologií zodpovědní za návrhy řešení hybridní identity pro střední a velké organizace.

## <a name="how-can-this-guide-help-you"></a>Jak může tento průvodce pomoct?
Tento průvodce vám pomůže porozumět procesu návrhu hybridní řešení identit, aby bylo možné integrovat řešení aktuální identita místní systém pro správu cloudových identit. 

Následující obrázek ukazuje příklad hybridní řešení identit, která umožňuje správcům IT spravovat k integraci se službou Microsoft Azure Active Directory umožňuje uživatelům používat jednotné přihlašování (aktuální Windows Server Active Directory řešením místních Jednotné přihlašování) ve všech aplikacích, které jsou umístěné v cloudu a místně.

![](./media/hybrid-id-design-considerations/hybridID-example.png)

Výše uvedený obrázek je příkladem řešení hybridní identity, které se využívají cloudové služby pro integraci místních funkcí za účelem jednotnému v procesu ověřování koncového uživatele a usnadnění Správa těch, které IT prostředky. I když v tomto příkladu může být běžný scénář, každá organizace hybridní identity návrhu, bude pravděpodobně lišit od příkladu znázorněno na obrázku 1 kvůli různým požadavkům. 

Tento průvodce popisuje řady kroků a úloh, které vám pomůžou při návrhu řešení hybridní identity, který splňuje požadavky vaší organizace jedinečný. V následujících krocích a úlohách uvádí Průvodce relevantní technologie a možnosti funkcí, které jsou k dispozici na vás funkční úroveň a úrovně požadavky na kvalitu služeb pro vaši organizaci.

**Předpoklady**: máte nějaké zkušenosti s Windows serverem, Active Directory Domain Services a Azure Active Directory. V tomto dokumentu se předpokládá, že hledáte jak řešení můžete splňují vaše obchodní potřeby samostatně, nebo integrované řešení.

## <a name="design-considerations-overview"></a>Přehled aspektů návrhu
Tento dokument obsahuje sadu kroků a úloh, které vám pomůžou při návrhu řešení hybridní identity, které nejlépe vyhovuje vašim požadavkům. Kroky uvádíme v seřazené posloupnosti. Aspekty návrhu, s nimiž se seznámíte v dalších krocích může vyžadovat budete muset změnit rozhodnutí, která jste udělali v dřívějších krocích, však kvůli konfliktním volbám návrhu. Každý pokus se uskuteční vás upozorní na potenciální konflikty návrhu v celém dokumentu. 

Dorazí návrhu, která nejlépe splňuje vaše požadavky dopracujete projdete kroky tolikrát, kolikrát podle potřeby, abyste zapracovali všechny aspekty uvedené v dokumentu. 

| Fáze hybridní Identity | Seznam témat |
| --- | --- |
| Stanovení požadavků na identity |[Určení obchodních potřeb](active-directory-hybrid-identity-design-considerations-business-needs.md)<br> [Určení požadavků na synchronizaci adresáře](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Stanovení požadavků na službu Multi-Factor authentication](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Definování strategie přijetí hybridní identity](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| Plán pro zlepšení zabezpečení dat prostřednictvím řešení silné identity |[Určení požadavků na ochranu dat](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Stanovení požadavků na správu obsahu](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Určete požadavky řízení přístupu](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Stanovení požadavků na reakce na incidenty](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Definování strategie ochrany dat](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) |
| Plánování životního cyklu hybridní identity |[Určení úlohy správy hybridní identity](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Synchronizace správy](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Určení strategie přijetí správy hybridní identity](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="download-this-guide"></a>Stáhněte si tento průvodce
Pdf verzi Průvodce aspekty návrhu hybridní Identity z si můžete stáhnout [galerii Technet](https://gallery.technet.microsoft.com/Azure-Hybrid-Identity-b06c8288). 

