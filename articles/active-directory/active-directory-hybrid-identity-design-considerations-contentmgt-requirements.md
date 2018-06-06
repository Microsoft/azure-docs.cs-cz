---
title: Návrhu hybridní identity – požadavky na správu obsahu Azure | Microsoft Docs
description: Poskytuje přehled o tom, jak určit požadavky na správu obsahu vaší firmy. Obvykle v případě, že uživatel má svá vlastní zařízení, budou také může mít několik přihlašovacích údajů, které bude střídavých závislosti na aplikaci, která používají. Je důležité k rozlišení postupu, který obsah byl vytvořen pomocí osobní přihlašovacích údajů oproti těm, které jsou vytvořené pomocí podnikové přihlašovací údaje. Řešení identity měli být schopní pracovat s cloud services a poskytuje bezproblémové prostředí pro koncového uživatele při zajištění jejich ochrany osobních údajů a zvýšit ochranu před úniky dat.
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: b414a1e93d5a0f4be5be82011482b492fd9ce378
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34800789"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Stanovení požadavků na správu obsahu pro vaše řešení hybridní identity
Seznámení s požadavky na správu obsahu pro vaši organizaci může přímo ovlivnit vaše rozhodnutí, na které hybridní řešení identit používat. S tím, jak narůstá více zařízení a schopnost uživatelům přinést si vlastní zařízení ([BYOD](https://aka.ms/byodcg)), musí společnost chránit svá vlastní data, ale ho také musí zachovat ochranu osobních údajů uživatelů. Obvykle v případě, že uživatel má svá vlastní zařízení, budou také může mít několik přihlašovacích údajů, které bude střídavých závislosti na aplikaci, která používají. Je důležité k rozlišení postupu, který obsah byl vytvořen pomocí osobní přihlašovacích údajů oproti těm, které jsou vytvořené pomocí podnikové přihlašovací údaje. Řešení identity měli být schopní pracovat s cloud services a poskytuje bezproblémové prostředí pro koncového uživatele při zajištění jejich ochrany osobních údajů a zvýšit ochranu před úniky dat. 

Chcete-li poskytovat správy obsahu, jak je znázorněno na obrázku níže bude řešení identity využít různé technické ovládací prvky:

![](./media/hybrid-id-design-considerations/securitycontrols.png)

**Ovládací prvky zabezpečení, které bude možné využívat váš systém správy identit**

Obecně platí požadavky na správu obsahu bude využívat váš systém správy identit v těchto oblastech:

* Ochrana osobních údajů: identifikace uživatele, který vlastní prostředek a použití příslušných ovládacích prvků k udržení integrity.
* Klasifikace dat: Identifikujte uživatele nebo skupinu a úroveň přístupu k objektu podle jejich klasifikace. 
* Ochrana úniku dat: ovládací prvky zabezpečení odpovědnost za ochranu dat, aby se zabránilo úniku potřebovat k interakci se systém identit k ověření identity uživatele. To je důležité pro auditování záznam účel.

> [!NOTE]
> Čtení [klasifikace dat pro cloud připravenosti](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) Další informace o osvědčených postupech a pokyny pro klasifikaci dat.
> 
> 

Při plánování řešení hybridní identity se ujistěte, že následující otázky jsou zodpovězeny v souladu s požadavky vaší organizace:

* Má vaše společnost zavedené ovládací prvky zabezpečení k vynucení ochrany osobních údajů?
  * Pokud ano, ovládací prvky zabezpečení budou moci integrovat řešení hybridní identity, který chcete použít?
* Používá vaše společnost klasifikace dat?
  * Pokud ano, je aktuální řešení schopné integrovat řešení hybridní identity, který chcete použít?
* Vaše společnost aktuálně nemá řešení pro úniku dat? 
  * Pokud ano, je aktuální řešení schopné integrovat řešení hybridní identity, který chcete použít?
* Potřebuje vaše společnost auditovat přístup k prostředkům?
  * Pokud ano, jaké typy prostředků?
  * Pokud ano, jakou úroveň informací je potřeba?
  * Pokud ano, kde protokol auditu se musí nacházet? Místní nebo v cloudu?
* Potřebuje vaše společnost k šifrování e-mailů, které obsahují citlivá data (čísla sociálního zabezpečení, čísla platebních karet, atd.)?
* Potřebuje vaše společnost k šifrování všech dokumentů nebo obsah sdílet s externími obchodními partnery?
* Vaše společnost potřebuje vynucovat podnikové zásady na určité typy e-mailů (dělat žádné Odpovědět všem, nepředávat)?

> [!NOTE]
> Zajistěte, aby poznamenejte každou odpověď a pochopit důvody odpověď. [Definování strategie ochrany dat](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) budou přenášeny po dostupných možností a výhod i nevýhod jednotlivých možností.  Po zodpovězení těchto otázek, které vyberete která možnost nejlépe vyhovuje vaší firmě potřebuje.
> 
> 

## <a name="next-steps"></a>Další postup
[Určete požadavky řízení přístupu](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Viz také
[Přehled aspektů návrhu](active-directory-hybrid-identity-design-considerations-overview.md)

