---
title: Návrh hybridní identity – požadavky na správu obsahu Azure | Dokumenty společnosti Microsoft
description: Poskytuje přehled o tom, jak určit požadavky na správu obsahu vaší firmy. Obvykle, když má uživatel vlastní zařízení, může mít také více pověření, která se budou střídat podle aplikace, kterou používají. Je důležité rozlišovat, jaký obsah byl vytvořen pomocí osobních pověření oproti pověřením vytvořeným pomocí podnikových pověření. Vaše řešení identit y by mělo být schopno pracovat s cloudovými službami, aby koncovým uživatelům poskytovalo bezproblémové prostředí a současně zajistilo jejich ochranu osobních údajů a zvýšilo ochranu před únikem dat.
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
ms.openlocfilehash: 0d970fd133f8c43319e7f1fdb6b3a50c3c05f687
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "64918442"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Určení požadavků na správu obsahu pro řešení hybridní identity
Pochopení požadavků na správu obsahu pro vaši firmu může mít přímý vliv na vaše rozhodnutí, které řešení hybridní identity použít. S rozšířením více zařízení a schopností uživatelů přinést si vlastní zařízení[(BYOD)](https://aka.ms/byodcg)musí společnost chránit vlastní data, ale musí také udržovat soukromí uživatele neporušené. Obvykle, když má uživatel vlastní zařízení, může mít také více pověření, která se budou střídat podle aplikace, kterou používají. Je důležité rozlišovat, jaký obsah byl vytvořen pomocí osobních pověření oproti pověřením vytvořeným pomocí podnikových pověření. Vaše řešení identit y by mělo být schopno pracovat s cloudovými službami, aby koncovým uživatelům poskytovalo bezproblémové prostředí a současně zajistilo jejich ochranu osobních údajů a zvýšilo ochranu před únikem dat. 

Vaše řešení identity bude využito různými technickými kontrolami, aby bylo možné poskytovat správu obsahu, jak je znázorněno na obrázku níže:

![ovládací prvky zabezpečení](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**Ovládací prvky zabezpečení, které budou využívat váš systém správy identit**

Obecně platí, že požadavky na správu obsahu budou využívat váš systém správy identit v následujících oblastech:

* Ochrana osobních údajů: identifikace uživatele, který vlastní prostředek, a použití příslušných ovládacích prvků pro zachování integrity.
* Klasifikace dat: identifikujte uživatele nebo skupinu a úroveň přístupu k objektu podle jeho klasifikace. 
* Ochrana před únikem dat: bezpečnostní kontroly odpovědné za ochranu dat, aby se zabránilo úniku, budou muset pracovat se systémem identit, aby se ověřila identita uživatele. To je také důležité pro audit trail účel.

> [!NOTE]
> Další informace o doporučených postupech a pokyny pro klasifikaci dat načtete [pro klasifikaci cloudu.](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf)
> 
> 

Při plánování hybridního řešení identity zajistěte, aby byly zodpovězeny následující otázky podle požadavků vaší organizace:

* Má vaše společnost zavedeny bezpečnostní kontroly k vynucení ochrany osobních údajů?
  * Pokud ano, budou bezpečnostní ovládací prvky schopny integrovat se s řešením hybridní identity, které se chystáte přijmout?
* Používá vaše společnost klasifikaci dat?
  * Pokud ano, je současné řešení schopno integrovat s řešením hybridní identity, které se chystáte přijmout?
* Má vaše společnost v současné době nějaké řešení pro únik dat? 
  * Pokud ano, je současné řešení schopno integrovat s řešením hybridní identity, které se chystáte přijmout?
* Potřebuje vaše společnost auditovat přístup k prostředkům?
  * Pokud ano, jaký typ zdrojů?
  * Pokud ano, jaká úroveň informací je nezbytná?
  * Pokud ano, kde musí být umístěn protokol auditu? Místní nebo v cloudu?
* Potřebuje vaše společnost šifrovat všechny e-maily, které obsahují citlivá data (SSN, čísla kreditních karet atd.)?
* Potřebuje vaše společnost šifrovat všechny dokumenty/obsahy sdílené s externími obchodními partnery?
* Potřebuje vaše společnost vynucovat firemní zásady u určitých druhů e-mailů (neodpovíte na všechny, nepředávají)?

> [!NOTE]
> Každou odpověď si poznamenejte a ujistěte se, že dobře chápete důvody, které vás k ní vedly. [Definovat strategii ochrany osobních údajů](plan-hybrid-identity-design-considerations-data-protection-strategy.md) půjde přes možnosti k dispozici a výhody / nevýhody každé možnosti.  Tím, že odpověděl na tyto otázky, budete vybírat, která možnost nejlépe vyhovuje vašim obchodním potřebám.
> 
> 

## <a name="next-steps"></a>Další kroky
[Určení požadavků na řízení přístupu](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Viz také
[Přehled aspekty návrhu](plan-hybrid-identity-design-considerations-overview.md)

