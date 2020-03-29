---
title: Návrh hybridní identity – požadavky na ochranu dat Azure | Dokumenty společnosti Microsoft
description: Při plánování hybridního řešení identity určete požadavky na ochranu dat pro vaši firmu a jaké možnosti jsou k dispozici, aby tyto požadavky co nejlépe splňovaly.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 40dc4baa-fe82-4ab6-a3e4-f36fa9dcd0df
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 056d0caf2bfc6e99d5d659a2561bc41844feb79a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "64918779"
---
# <a name="plan-for-enhancing-data-security-through-a-strong-identity-solution"></a>Plán pro zvýšení zabezpečení dat prostřednictvím řešení silné identity
Prvním krokem při ochraně dat je určení, kdo má k těmto datům přístup. Také musíte mít řešení identity, které lze integrovat s vaším systémem a poskytovat možnosti ověřování a autorizace. Ověřování a autorizace jsou často zaměňovány mezi sebou a jejich role nepochopeny. Ve skutečnosti se liší, jak je znázorněno na obrázku níže:

![životní cyklus mobilního zařízení](./media/plan-hybrid-identity-design-considerations/mobile-devicemgt-lifecycle.png)

**Fáze životního cyklu správy mobilních zařízení**

Při plánování hybridního řešení identity musíte pochopit požadavky na ochranu dat pro vaši firmu a jaké možnosti jsou k dispozici, aby tyto požadavky co nejlépe splňovaly.

> [!NOTE]
> Po dokončení plánování zabezpečení dat zkontrolujte, zda [určit požadavky na vícefaktorové ověřování,](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md) abyste zajistili, že vaše výběry týkající se požadavků na vícefaktorové ověřování nebyly ovlivněny rozhodnutími, která jste provedli v této části.
> 
> 

## <a name="determine-data-protection-requirements"></a>Určení požadavků na ochranu údajů
Ve věku mobility má většina společností společný cíl: umožnit svým uživatelům produktivitu na svých mobilních zařízeních, zatímco místně nebo vzdáleně odkudkoli, aby zvýšili produktivitu. Společnosti, které mají takové požadavky, budou také znepokojeny počtem hrozeb, které je třeba zmírnit, aby byla data společnosti v bezpečí a soukromí uživatelů. Každá společnost může mít v tomto ohledu jiné požadavky; různá pravidla dodržování předpisů, která se budou lišit podle odvětví, které společnost jedná, povedou k různým rozhodnutím o návrhu. 

Existují však některé bezpečnostní aspekty, které by měly být prozkoumány a ověřeny, bez ohledu na odvětví.

## <a name="data-protection-paths"></a>Cesty ochrany dat
![cesty ochrany dat](./media/plan-hybrid-identity-design-considerations/data-protection-paths.png)

**Cesty ochrany dat**

Ve výše uvedeném diagramu bude součást identity první, která bude ověřena před přístupem k datům. Tato data však mohou být v různých stavech během doby, kdy byla přístupná. Každé číslo v tomto diagramu představuje cestu, ve které mohou být data umístěna v určitém okamžiku v čase. Tato čísla jsou vysvětlena níže:

1. Ochrana dat na úrovni zařízení.
2. Ochrana údajů během přepravy.
3. Ochrana osobních údajů v místním klidu.
4. Ochrana dat v klidu v cloudu.

Je nezbytné, aby řešení hybridní identity je schopno využívat prostředky pro správu místníi i cloudové identity k identifikaci uživatele dříve, než udělí přístup k datům. Při plánování řešení hybridní identity se ujistěte, že jsou zodpovězeny následující otázky podle požadavků vaší organizace:

## <a name="data-protection-at-rest"></a>Ochrana údajů v klidovém stavu
Bez ohledu na to, kde jsou data v klidovém stavu (zařízení, cloud nebo místní), je důležité provést posouzení, abychom pochopili potřeby organizace v tomto ohledu. V této oblasti se ujistěte, že jsou položeny následující otázky:

* Potřebuje vaše společnost chránit data v klidovém stavu?
  * Pokud ano, je řešení hybridní identity schopno integrovat s vaší aktuální místní infrastrukturou?
  * Pokud ano, je řešení hybridní identity schopné integrovat s vašimi úlohami umístěnými v cloudu?
* Je správa cloudových identit schopná chránit přihlašovací údaje uživatele a další data uložená v cloudu?

## <a name="data-protection-in-transit"></a>Ochrana údajů při přenosu
Data při přenosu mezi zařízením a datovým centrem nebo mezi zařízením a cloudem musí být chráněna. Být v tranzitu však nemusí nutně znamenat komunikační proces s komponentou mimo vaši cloudovou službu; přesune interně, také, například mezi dvěma virtuálními sítěmi. V této oblasti se ujistěte, že jsou položeny následující otázky:

* Potřebuje vaše společnost chránit data při přepravě?
  * Pokud ano, je řešení hybridní identity schopno integrovat se zabezpečenými ovládacími prvky, jako je například SSL/TLS?
* Udržuje správa cloudových identit provoz do úložiště adresářů a v jeho rámci (v rámci datových center a mezi nimi) podepsaný?

## <a name="compliance"></a>Dodržování předpisů
Předpisy, zákony a požadavky na dodržování předpisů se budou lišit v závislosti na odvětví, do kterého vaše společnost patří. Společnosti ve vysoce regulovaných odvětvích se musí zabývat otázkami správy identit souvisejících s otázkami dodržování předpisů. Předpisy jako Sarbanes-Oxley (SOX), zákon o přenositelnosti a odpovědnosti zdravotního pojištění (HIPAA), gramm-leach-blileyský zákon (GLBA) a standard bezpečnosti dat v odvětví platebních karet (PCI DSS) jsou přísné, pokud jde o identitu a přístup. Řešení hybridní identity, které vaše společnost přijme, musí mít základní funkce, které budou splňovat požadavky jednoho nebo více těchto předpisů. V této oblasti se ujistěte, že jsou položeny následující otázky:

* Je řešení hybridní identity v souladu s regulačními požadavky pro vaši firmu?
* Má hybridní identity řešení vybudovala 
* ve schopnostech, které umožní vaší společnosti, aby byly v souladu s regulačními požadavky? 

> [!NOTE]
> Každou odpověď si poznamenejte a ujistěte se, že dobře chápete důvody, které vás k ní vedly. [Definovat strategii ochrany osobních údajů](plan-hybrid-identity-design-considerations-data-protection-strategy.md) půjde přes možnosti k dispozici a výhody / nevýhody každé možnosti.  Tím, že odpověděl na tyto otázky, budete vybírat, která možnost nejlépe vyhovuje vašim obchodním potřebám.
> 
> 

## <a name="next-steps"></a>Další kroky
 [Určení požadavků na správu obsahu](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>Viz také
[Přehled aspekty návrhu](plan-hybrid-identity-design-considerations-overview.md)

