---
title: Hybridní identita návrh – požadavky na ochranu dat Azure | Dokumentace Microsoftu
description: Při plánování řešení hybridní identity, identifikujte požadavky na ochranu dat pro vaši firmu a jaké možnosti jsou k dispozici pro nejlepší splnění těchto požadavků.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 40dc4baa-fe82-4ab6-a3e4-f36fa9dcd0df
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 34c284f3fdd78d8b2f56ec7c36e139f9ac9d78bf
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54473499"
---
# <a name="plan-for-enhancing-data-security-through-a-strong-identity-solution"></a>Plán pro zlepšení zabezpečení dat prostřednictvím řešení využívá silné identity
Prvním krokem při ochraně dat je identifikovat, kdo má přístup k těmto datům. Navíc musíte mít řešení identit, které můžete integrovat do vašeho systému pro poskytování možností ověřování a autorizace. Ověřování a autorizace se často zaměňuje za číslo mezi sebou a nesprávně pochopeny, jejich role. Ve skutečnosti jsou různé, jak je znázorněno na následujícím obrázku:

![](./media/plan-hybrid-identity-design-considerations/mobile-devicemgt-lifecycle.png)

**Fáze životního cyklu správy mobilních zařízení**

Při plánování řešení hybridní identity, je třeba pochopit, že tyto požadavky splňují požadavky na ochranu dat pro vaši firmu a jaké možnosti jsou k dispozici pro co nejlepší.

> [!NOTE]
> Jakmile dokončíte plánování zabezpečení dat, zkontrolujte [stanovit požadavky na ověřování službou Multi-Factor Authentication](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md) zajistit, že váš výběr ohledně požadavky na vícefaktorové ověřování nebyly ovlivněny rozhodnutí, která jste provedené v této části.
> 
> 

## <a name="determine-data-protection-requirements"></a>Určení požadavků na ochranu dat
Ve věku mobility, většina společností má stejný cíl – umožnit: povolit jejich produktivitu uživatelů na jejich mobilních zařízeních, místně nebo vzdáleně z libovolného místa za účelem zvýšení produktivity. Společností, které mají tyto požadavky budou také mít obavy o počet hrozeb, které musí možné zmírnit, aby bylo možné zabezpečit firemní data a zachovat ochranu osobních údajů uživatelů. Každá společnost může mít různé požadavky v tomto ohledu; pravidla různých dodržování předpisů, které se budou lišit podle odvětví, které působí společnost povede k jiné rozhodnutí. 

Existují však některé aspekty zabezpečení, které by měl prozkoumat a ověřit, bez ohledu na odvětví.

## <a name="data-protection-paths"></a>Cesty k datům ochrany
![](./media/plan-hybrid-identity-design-considerations/data-protection-paths.png)

**Cesty k datům ochrany**

V diagramu výše bude komponentu identita první data se před přístupem k ověření. Však tato data mohou být v různých stavech dobu, kdy se použila. Každého čísla v tomto diagramu představuje cestu, ve kterém data můžou být umístěná v určitém okamžiku v čase. Tato čísla jsou vysvětleny níže:

1. Ochrana dat na úrovni zařízení.
2. Ochrana dat při přenosu.
3. Ochrana dat v rest místní.
4. Ochrana dat v klidovém stavu uložených v cloudu.

Je nezbytné, aby hybridní řešení identit umožňující využití v místním a cloudovým prostředkům správy identit k identifikaci uživatele předtím, než se udělí přístup k datům. Při plánování řešení hybridní identity, ujistěte se, že podle požadavků vaší organizace jsou odpovědi na následující otázky:

## <a name="data-protection-at-rest"></a>Ochrana dat v klidovém stavu
Bez ohledu na to, kde jsou data v klidovém stavu (zařízení, cloudové nebo místní) je potřeba provést k posouzení v tomto ohledu o potřebách organizace. Pro tuto oblast Ujistěte se, že se zobrazí výzva na následující otázky:

* Potřebuje vaše společnost k ochraně dat v klidovém stavu?
  * Pokud ano, je řešení hybridní identity spojit s aktuální místní infrastrukturu?
  * Pokud ano, je řešení hybridní identity schopná integrovat s vašimi úlohami umístěný v cloudu?
* Správu cloudových identit je schopné chránit přihlašovacích údajů uživatele a další data uložená v cloudu?

## <a name="data-protection-in-transit"></a>Ochrana dat při přenosu
Data přenášená mezi zařízením a datového centra nebo mezi zařízením a cloudem musí být chráněny. Ale probíhá během přenosu nemusí nutně znamenat proces komunikace s komponentou mimo vaši cloudovou službu; Přesune interně, také, například mezi dvěma virtuálními sítěmi. Pro tuto oblast Ujistěte se, že se zobrazí výzva na následující otázky:

* Potřebuje vaše společnost k ochraně dat během přenosu?
  * Pokud ano, je řešení hybridní identity spojit s zabezpečené ovládací prvky, jako je například SSL/TLS?
* Správu cloudových identit zachovat provoz do a v rámci adresáře úložiště (v rámci a mezi datacentry) podepsané?

## <a name="compliance"></a>Dodržování předpisů
Právní, zákony a požadavky na dodržování předpisů se liší podle odvětví, do kterého patří vaší společnosti. Správa identit obavy týkající se problémů s dodržováním předpisů musí řešit společností ve vysoce regulované odvětví. Jako Sarbanes-Oxley (SOX), údajů o zdravotním pojištění a zdravotním pojištění (HIPAA), Gramm-Leach-Bliley Act (GRAMM) a Standard odvětví platebních karet Data zabezpečení (PCI DSS) jsou striktní týkající se identit a přístupů. Řešení hybridní identity, které vaše společnost zavede musí mít základní funkce, které se bude splňovat požadavky jednoho nebo více těchto pravidel. Pro tuto oblast Ujistěte se, že se zobrazí výzva na následující otázky:

* Je řešení hybridní identity splňovat zákonné požadavky pro vaši společnost?
* Neexistuje, má integrované řešení hybridní identity 
* Funkce, které vám umožní vaší společnosti bude kompatibilní s požadavky na dodržování legislativních? 

> [!NOTE]
> Ujistěte se, že každá odpověď a pochopení odůvodnění odpověď. [Definování strategie ochrany dat](plan-hybrid-identity-design-considerations-data-protection-strategy.md) možností a výhod i nevýhod každé z.  Po zodpovězení těchto otázek, které vyberete která možnost nejlépe vyhovuje stylu vaší firmě potřebuje.
> 
> 

## <a name="next-steps"></a>Další postup
 [Určení požadavků na správu obsahu](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>Viz také
[Přehled aspektů návrhu](plan-hybrid-identity-design-considerations-overview.md)

