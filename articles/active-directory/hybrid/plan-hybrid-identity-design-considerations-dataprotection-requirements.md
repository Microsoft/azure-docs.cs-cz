---
title: Návrh hybridní identity – požadavky na ochranu dat Azure | Microsoft Docs
description: Při plánování řešení hybridní identity Identifikujte požadavky na ochranu dat pro vaši firmu a možnosti, které jsou k dispozici pro nejlepší splnění těchto požadavků.
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
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "64918779"
---
# <a name="plan-for-enhancing-data-security-through-a-strong-identity-solution"></a>Plánování vylepšení zabezpečení dat prostřednictvím řešení silné identity
Prvním krokem při ochraně dat je určení toho, kdo má přístup k těmto datům. K zajištění možností ověřování a autorizace je taky potřeba mít řešení identity, které se dá integrovat s vaším systémem. Ověřování a autorizace jsou často zaměňovány mezi sebou a jejich role jsou nesrozumitelnější. Ve skutečnosti se liší, jak je znázorněno na následujícím obrázku:

![životní cyklus mobilního zařízení](./media/plan-hybrid-identity-design-considerations/mobile-devicemgt-lifecycle.png)

**Fáze životního cyklu správy mobilních zařízení**

Při plánování řešení hybridní identity musíte pochopit požadavky na ochranu dat pro vaši firmu a dostupné možnosti, které nejlépe splňují tyto požadavky.

> [!NOTE]
> Jakmile dokončíte plánování zabezpečení dat, přečtěte si téma [Určení požadavků na vícefaktorové ověřování](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md) , abyste měli jistotu, že vaše výběry týkající se požadavků služby Multi-Factor Authentication nebyly ovlivněny rozhodnutími uvedenými v této části.
> 
> 

## <a name="determine-data-protection-requirements"></a>Určení požadavků na ochranu dat
Ve věku mobility mají většina společností společný cíl: umožní jejich využívání uživatelům na svých mobilních zařízeních, místně i místně, nebo vzdáleně z libovolného místa, aby mohli zvýšit produktivitu. Společnosti, které mají tyto požadavky, budou také mít obavy z počtu hrozeb, které je potřeba zmírnit, aby se data společnosti zabezpečila a udržovala soukromí uživatelů. Každá společnost může mít v tomto ohledu různé požadavky; různá pravidla dodržování předpisů, která se budou lišit podle toho, v jakém odvětví bude společnost působit, povede k různým rozhodnutím o návrhu. 

Existují však některé aspekty zabezpečení, které by se měly prozkoumat a ověřit bez ohledu na obor.

## <a name="data-protection-paths"></a>Cesty ochrany dat
![cesty ochrany dat](./media/plan-hybrid-identity-design-considerations/data-protection-paths.png)

**Cesty ochrany dat**

Ve výše uvedeném diagramu bude komponenta identity první, která bude ověřena před tím, než budou k datům přicházet. Tato data ale můžou být v různých stavech během doby, kdy k nim došlo. Každé číslo v tomto diagramu představuje cestu, ve které může být v určitém okamžiku k dispozici data. Tato čísla jsou vysvětlena níže:

1. Ochrana dat na úrovni zařízení.
2. Ochrana dat během přenosu.
3. Ochrana dat v místním umístění.
4. Ochrana dat v klidovém režimu v cloudu.

Pro identifikaci uživatele před tím, než udělí přístup k datům, je nutné, aby řešení hybridní identity bylo schopné využít místní i cloudové prostředky správy identit. Při plánování řešení hybridní identity se ujistěte, že jsou na základě požadavků vaší organizace zodpovězené následující otázky:

## <a name="data-protection-at-rest"></a>Ochrana dat v klidovém umístění
Bez ohledu na to, kde jsou data v klidovém formátu (zařízení, Cloud nebo místní), je důležité provést posouzení, abyste v tomto ohledu pochopili potřeby organizace. V této oblasti se ujistěte, že jsou kladeny následující otázky:

* Potřebuje vaše společnost chránit neaktivní neaktivní data?
  * Pokud ano, je řešení hybridní identity schopné integrovat s vaší aktuální místní infrastrukturou?
  * Pokud ano, je řešení hybridní identity schopné integrovat s vašimi úlohami, které se nacházejí v cloudu?
* Je cloudová správa identit schopná chránit přihlašovací údaje uživatele a jiná data uložená v cloudu?

## <a name="data-protection-in-transit"></a>Ochrana dat při přenosu
Data přenášená mezi zařízením a datacenterm nebo mezi zařízením a cloudem musí být chráněná. Nejedná se ale nutně o komunikační proces s komponentou mimo vaši cloudovou službu; přesouvá se interně, navíc mezi dvěma virtuálními sítěmi. V této oblasti se ujistěte, že jsou kladeny následující otázky:

* Potřebuje vaše společnost chránit data při přenosu?
  * Pokud ano, je řešení hybridní identity schopné integrovat s zabezpečenými ovládacími prvky, jako je protokol SSL/TLS?
* Udržuje Správa identit v cloudu i v úložišti adresářů (v rámci a mezi datacentry) podepsané přenosy?

## <a name="compliance"></a>Dodržování předpisů
Předpisy, zákony a požadavky na dodržování legislativních předpisů se budou lišit v závislosti na odvětví, ve kterém vaše společnost patří. Společnosti ve vysoce regulovaných odvětvích musí řešit aspekty týkající se správy identit související s problémy s dodržováním předpisů. Právní předpisy, jako je například Sarbanes-Oxley (SOX), přenositelnost zdravotního pojištění a AKT (HIPAA), Bliley Act (Gramm) a standard zabezpečení dat v odvětví platebních karet (PCI DSS) jsou striktní v souvislosti s identitou a přístupem. Řešení hybridní identity, které vaše společnost přijme, musí mít základní funkce, které budou plnit požadavky jednoho nebo více těchto předpisů. V této oblasti se ujistěte, že jsou kladeny následující otázky:

* Dodržuje řešení hybridní identity dodržování zákonných požadavků pro vaši firmu?
* Má vytvořené řešení hybridní identity 
* ve funkcích, které umožní vaší společnosti splňovat zákonné požadavky? 

> [!NOTE]
> Každou odpověď si poznamenejte a ujistěte se, že dobře chápete důvody, které vás k ní vedly. [Definování strategie ochrany dat](plan-hybrid-identity-design-considerations-data-protection-strategy.md) překročí dostupné možnosti a výhody a nevýhody jednotlivých možností.  Po zodpovězení těchto otázek si vyberete, která možnost nejlépe vyhovuje vašim obchodním potřebám.
> 
> 

## <a name="next-steps"></a>Další kroky
 [Určení požadavků na správu obsahu](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>Viz také
[Přehled otázek návrhu](plan-hybrid-identity-design-considerations-overview.md)

