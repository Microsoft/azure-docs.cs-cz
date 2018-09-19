---
title: Hybridní identita návrh – požadavky na správu obsahu Azure | Dokumentace Microsoftu
description: Poskytuje podrobné informace o tom, jak určit požadavky na správu obsahu vašeho podnikání. Většinou když uživatel má své vlastní zařízení, jsou také může mít více přihlašovacích údajů, které se Alternující závislosti na aplikaci, která používají. Je důležité rozlišení, jaký obsah byl vytvořen pomocí osobních údajů a modely vytvořené pomocí firemních přihlašovacích údajů. Řešení identity měli být schopni komunikovat s cloudovou službou službami a zajistit bezproblémové prostředí pro koncového uživatele při zajištění jejich ochrany osobních údajů a zvýšit ochranu před úniky dat chránit.
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
ms.openlocfilehash: a3e5ae61fcefcb65d08288a2ddc2dd239e68b211
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46312253"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Určení požadavků na správu obsahu pro vaše řešení hybridní identity
Pochopení požadavků správy obsahu pro vaši společnost může přímo ovlivnit vaše rozhodnutí, na které řešení hybridní identity používat. S čím více zařízení a umožňuje uživatelům přinést si vlastní zařízení ([BYOD](https://aka.ms/byodcg)), společnost musí chránit svoje vlastní data, ale také musí uchovávat ochrany osobních údajů uživatele ponechá beze změny. Většinou když uživatel má své vlastní zařízení, jsou také může mít více přihlašovacích údajů, které se Alternující závislosti na aplikaci, která používají. Je důležité rozlišení, jaký obsah byl vytvořen pomocí osobních údajů a modely vytvořené pomocí firemních přihlašovacích údajů. Řešení identity měli být schopni komunikovat s cloudovou službou službami a zajistit bezproblémové prostředí pro koncového uživatele při zajištění jejich ochrany osobních údajů a zvýšit ochranu před úniky dat chránit. 

Řešení identit budou využívat jiné technické ovládací prvky negace správy obsahu, jak je znázorněno na následujícím obrázku:

![](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**Kontrolní mechanismy zabezpečení, které se využití vašeho systému správy identit**

Obecně platí požadavky na správu obsahu bude využívat systém správy identit v následujících oblastech:

* Ochrana osobních údajů: identifikace uživatele, který vlastní prostředek a používání patřičných opatření na udržení integrity.
* Klasifikace dat: Identifikujte uživatele nebo skupinu a úroveň přístupu k objektu podle jejich klasifikace. 
* Ochrana úniku dat: kontrolní mechanismy zabezpečení za ochranu dat, aby se zabránilo úniku potřebovat pro interakci s systém identit pro ověření identity uživatele. To je důležité pro záznam pro účely auditování.

> [!NOTE]
> Čtení [klasifikace dat podle připravenosti na cloud](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) Další informace o osvědčených postupech a pokyny pro klasifikaci dat.
> 
> 

Při plánování řešení hybridní identity Ujistěte se, že podle požadavků vaší organizace jsou odpovědi na následující otázky:

* Má vaše společnost nějaké kontrolní mechanismy zabezpečení si vynutit ochrany osobních údajů?
  * Pokud ano, kontrolní mechanismy zabezpečení bude moci integrovat řešení hybridní identity, která se má přijmout?
* Používá vaše společnost klasifikace dat?
  * Pokud ano, je aktuální řešení schopná integrovat s hybridní řešení identit, která se má přijmout?
* Má vaše společnost aktuálně žádným řešením pro úniku dat? 
  * Pokud ano, je aktuální řešení schopná integrovat s hybridní řešení identit, která se má přijmout?
* Potřebuje vaše společnost pro auditování přístupu k prostředkům?
  * Pokud ano, jaký typ prostředků?
  * Pokud ano, jaké úroveň informací je potřeba?
  * Pokud ano, ve kterém musí nacházet v protokolu auditu? Místní nebo v cloudu?
* Potřebuje vaše společnost k šifrování e-mailů, které obsahují citlivá data (čísla sociálního zabezpečení, čísla platebních karet, atd.)?
* Potřebuje vaše společnost k šifrování všech dokumentů/obsah sdílet s externími obchodními partnery?
* Vaše společnost potřebuje vynucovat podnikové zásady na určité druhy e-mailů (dělat žádné Odpovědět všem, není přeposlat)?

> [!NOTE]
> Ujistěte se, že každá odpověď a pochopení odůvodnění odpověď. [Definování strategie ochrany dat](plan-hybrid-identity-design-considerations-data-protection-strategy.md) možností a výhod i nevýhod každé z.  Po zodpovězení těchto otázek, které vyberete která možnost nejlépe vyhovuje stylu vaší firmě potřebuje.
> 
> 

## <a name="next-steps"></a>Další postup
[Určete požadavky na řízení přístupu](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Viz také
[Přehled aspektů návrhu](plan-hybrid-identity-design-considerations-overview.md)

