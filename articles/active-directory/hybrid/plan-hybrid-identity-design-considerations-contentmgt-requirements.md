---
title: Návrh hybridní identity – požadavky na správu obsahu Azure | Microsoft Docs
description: Poskytuje přehled o tom, jak určit požadavky vaší firmy na správu obsahu. Když má uživatel vlastní zařízení, může mít také několik přihlašovacích údajů, které se budou střídat podle používané aplikace. Je důležité odlišit, který obsah byl vytvořen pomocí osobních přihlašovacích údajů, oproti těm vytvořeným pomocí podnikových přihlašovacích údajů. Vaše řešení identity by mělo být schopné komunikovat s Cloud Services a zajistit tak bezproblémové prostředí koncovým uživatelům a zároveň zajistit jejich soukromí a zvýšit ochranu před únikem dat.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57990fda7475b95bd6582fa5a495ac8d24aa55d5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94408528"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Určení požadavků na správu obsahu pro vaše řešení hybridní identity
Porozumění požadavkům na správu obsahu pro vaši firmu může přímo ovlivnit vaše rozhodnutí, které řešení hybridní identity použít. Při šíření více zařízení a schopnost uživatelů přinášet si vlastní zařízení ([BYOD](/mem/intune/fundamentals/byod-technology-decisions)) musí společnost chránit svoje vlastní data, ale také musí zachovat ochranu osobních údajů uživatele. Když má uživatel vlastní zařízení, může mít také několik přihlašovacích údajů, které se budou střídat podle používané aplikace. Je důležité odlišit, který obsah byl vytvořen pomocí osobních přihlašovacích údajů, oproti těm vytvořeným pomocí podnikových přihlašovacích údajů. Vaše řešení identity by mělo být schopné komunikovat s Cloud Services a zajistit tak bezproblémové prostředí koncovým uživatelům a zároveň zajistit jejich soukromí a zvýšit ochranu před únikem dat. 

Vaše řešení identity budou využívat různé technické ovládací prvky, aby bylo možné poskytovat správu obsahu, jak je znázorněno na následujícím obrázku:

![ovládací prvky zabezpečení](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**Ovládací prvky zabezpečení, které budou využívat váš systém správy identit**

Obecně platí, že požadavky na správu obsahu budou využívat váš systém správy identit v následujících oblastech:

* Ochrana osobních údajů: Identifikujte uživatele, který vlastní prostředek, a použijte příslušné ovládací prvky pro zachování integrity.
* Klasifikace dat: Identifikujte uživatele nebo skupinu a úroveň přístupu k objektu podle jeho klasifikace. 
* Ochrana před únikem dat: kontroly zabezpečení, které jsou zodpovědné za ochranu dat, aby se zabránilo úniku dat, bude potřeba, abyste se systémem identity ověřili identitu uživatele. To je důležité taky pro účely auditu pro audit.

> [!NOTE]
> Další informace o osvědčených postupech a pokynech pro klasifikaci dat najdete v článku [klasifikace dat pro připravenost na Cloud](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) .
> 
> 

Při plánování řešení hybridní identity zajistěte, aby byly na základě požadavků vaší organizace zodpovězeny následující otázky:

* Má vaše společnost bezpečnostní mechanismy pro vymáhání ochrany soukromí dat?
  * Pokud ano, budou mít ovládací prvky zabezpečení integraci s řešením hybridní identity, které chcete přijmout?
* Používá vaše společnost klasifikaci dat?
  * Pokud ano, je aktuální řešení schopné integrovat řešení hybridní identity, které hodláte přijmout?
* Má vaše společnost teď nějaké řešení pro úniky dat? 
  * Pokud ano, je aktuální řešení schopné integrovat řešení hybridní identity, které hodláte přijmout?
* Potřebuje vaše společnost Auditovat přístup k prostředkům?
  * Pokud ano, jaký typ prostředků?
  * Pokud ano, jaké množství informací je potřeba?
  * Pokud ano, kde se musí nacházet protokol auditu? Místně nebo v cloudu?
* Potřebuje vaše společnost šifrovat všechny e-maily, které obsahují citlivá data (čísla sociálního zabezpečení, čísla kreditních karet atd.)?
* Potřebuje vaše společnost šifrovat všechny dokumenty a obsah sdílený s externími obchodními partnery?
* Potřebuje vaše společnost vyhovět firemním zásadám u určitých druhů e-mailů (Neodpovídat všem, Nepředávat)?

> [!NOTE]
> Každou odpověď si poznamenejte a ujistěte se, že dobře chápete důvody, které vás k ní vedly. [Definování strategie ochrany dat](plan-hybrid-identity-design-considerations-data-protection-strategy.md) překročí dostupné možnosti a výhody a nevýhody jednotlivých možností.  Po zodpovězení těchto otázek si vyberete, která možnost nejlépe vyhovuje vašim obchodním potřebám.
> 
> 

## <a name="next-steps"></a>Další kroky
[Určení požadavků na řízení přístupu](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Viz také
[Přehled otázek návrhu](plan-hybrid-identity-design-considerations-overview.md)