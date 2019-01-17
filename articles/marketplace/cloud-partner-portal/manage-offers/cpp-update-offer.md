---
title: Aktualizace nabídky na webu Marketplace – Azure Marketplace | Dokumentace Microsoftu
description: Aktualizace nabídky na Azure a AppSource tržišť s řešeními pomocí portál Cloud Partner
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pbutlerm
ms.openlocfilehash: 690ce08aa15a9677b04931ed1965ef819614ee84
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355478"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>Aktualizace nabídky Azure Marketplace a AppSource

Existují různé typy aktualizací, které můžete použít pro vaši nabídku po publikování.  [Portál partnerů cloudu](https://cloudpartner.azure.com/) vám pomůže se správně změnou atributy nabídky, včetně:

-  Přidává se nová verze virtuálního počítače (VM) bitovou kopii nebo balíček do existující skladové položky
-  Změna oblasti, které skladová jednotka je k dispozici v
-  Přidání nové SKU
-  Aktualizace metadat marketplace pro SKU a nabídky 
-  Aktualizace ceny na průběžné platby

Portál také obsahuje funkce, jako je možnost porovnání funkcí a zobrazit historii funkcí pro nabídky, které vám pomohou při správě změn.  Po úpravě nabídku nebo skladovou Položku, musíte publikovat předtím, než se změny přejít "živé".  Tento článek vás provede různé aspekty aktualizace nabídky marketplace.

## <a name="unpermitted-changes-to-an-offersku"></a>Nedovolené změny nabídka/SKU

Existují některé atributy nabídky nebo skladovou jednotku, kterou nelze změnit po publikování na webu Marketplace.  Odpovídající pole jsou zakázána v **Editor** kartu na portálu, například:  

- ID a ID vydavatele
- SKU ID 
- Datový disk počet existujících skladových položek
- Fakturace nebo licenční model změny existujících skladových položek
- Verze značky, například: `1.0.1`


## <a name="common-update-operations"></a>Běžné operace update

Následující části popisují, jak provádět některé většinu operací aktualizace.  Tyto operace nejsou k dispozici pro všechny typy nabídek.  Musíte se přihlásit na portál Cloud Partner spusťte některý z těchto operací.


### <a name="update-offer-contacts"></a>Aktualizovat kontakty nabídky

Následujícím postupem aktualizovat kontakty podpory pro vaši nabídku.
1. V **všechny nabízí** stránky, vyberte nabídku služby.
2. Vyberte **kontakty** kartu. Aktualizace kontaktů.
3. Vyberte tlačítko **Uložit**.
4. Vyberte **publikovat** zahájíte proces publikování.


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>Změna oblasti, které nabídku nebo SKU je k dispozici v

V průběhu času můžete zpřístupnit vaše nabídka/SKU v dalších oblastech.
Alternativně můžete chtít zastavit podporu nabídka/SKU v dané oblasti.
Tyto změny implementovat, postupujte podle následujících kroků.

1. V **všechny nabídky** stránky, vyhledejte nabídky, které chcete aktualizovat.

Nabídky Azure Marketplace:

2. Vyberte **SKU** kartu.  Vyberte skladovou Položku změnit.
3. Klikněte na tlačítko **vybrat země** tlačítko **země/oblast dostupnosti** pole.
4. V oblasti dostupnosti dialogovém okně Přidat nebo odebrat oblasti pro tato skladová položka.

AppSource nabídky:

2. Vyberte **podrobnosti z prodejních míst** kartu.
3. Vedle položky **podporované země/oblasti** popisek, klikněte na tlačítko **podporované země/oblasti**. 
4. V dialogovém okně podporované země/oblasti přidání nebo odebrání oblastí pro v rámci této nabídky.

Pro buď marketplace:

5. Klikněte na tlačítko **publikovat** zahájíte proces publikování. 

Pokud SKU je k dispozici v nové oblasti, máte možnost určit ceny pro tuto konkrétní oblast prostřednictvím **exportovat Data ceny** funkce. Pokud chcete přidat zpět oblast, která byla dříve k dispozici, nelze aktualizovat, jeho ceny, protože o změně v cenách nejsou povolené.


### <a name="add-a-new-sku"></a>Přidat novou skladovou Položku 

Chcete-li novou skladovou Položku k dispozici pro stávající nabídky, postupujte následovně:

1. V **všechny nabídky** stránky, vyhledejte nabídky.
3. V části **skladové položky** formuláře, klikněte na tlačítko **přidat novou skladovou Položku** a zadejte **SKU ID** v místní nabídce.
4. Postupujte podle zbývajících kroky popsané v [publikování nabídky virtuálních počítačů](../virtual-machine/cpp-publish-offer.md).
5. Klikněte na tlačítko **publikovat** zahájíte proces publikování.


### <a name="update-offer-marketplace-assets"></a>Aktualizovat prostředky nabídky marketplace

Můžete mít situacích, kdy je potřeba aktualizovat na webu marketplace založený na textu a obrázku prostředky, tyto vašeho loga společnosti nabízejí popis atd. Použijte následující postup k aktualizaci těchto prostředků.

1. V **všechny nabídky** stránky, vyhledejte vaši nabídku. 
2. Vyberte **Marketplace** kartu a postupujte podle pokynů ve vaší nabídce *Marketplace kartu* tématu.
3. Klikněte na tlačítko **publikovat** zahájíte proces publikování.


### <a name="update-pricing-on-published-offers"></a>Aktualizace cen pro publikování nabídek

Po publikování vaší nabídky s průběžnými platbami nelze zvýšit cena existující skladové položky.  Místo toho vytvoří skladová jednotka v rámci stejné nabídky, odstranit staré skladové položky a pak znovu publikovat vaši nabídku. Cena na dříve publikované nabídky může snížit. Ke snížení ceny nabídky:

1. Vyberte SKU, pro kterou chcete snížit ceny.
2. Je nutné nastavit nižší cenu shodný mechanismus, který jste původně použili: buď přímo v Uživatelském rozhraní portálu nebo pomocí tabulky importu/exportu.
3. Klikněte na **Uložit**.
4. Klikněte na tlačítko **publikovat** zahájíte proces publikování.

Ceny je viditelná pro nové zákazníky, jakmile je v provozu na webu marketplace a všem novým zákazníkům platit pak nové nižší ceny.  Pro stávající zákazníky se zpětně projeví snížení cen na začátku fakturačního cyklu, během které snížení cen začal platit.  Pokud mají byl již účtuje cyklu, během které došlo k cenu, dostanou při jejich příštím účtovacím období pro snížení ceny refundaci.


## <a name="compare-feature"></a>Porovnání funkcí

Pokud provedete změny v publikované nabídky, můžete použít *porovnání* funkce Auditovat změny. Aby se začala používat tuto funkci:

1. V každém okamžiku procesu úprav, můžete kliknout na **porovnání** tlačítko **Editor** kartu pro vaši nabídku.
2. Porovnání okno zobrazí vedle sebe verze uložené změny do této nabídky porovnání s nabídky marketplace. 

![Porovnání tlačítko nabídky v editoru kartu](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>Historie publikování akce

Chcete-li zobrazit historické publikační aktivity, vyberte **historie** kartu v levém svislé řádku nabídek portálu partnerů cloudu.  Na stránce Historie nabízí pružné filtrování podle několika vlastností a podporuje řazení sloupce.  Každá událost publikování je označen časovým razítkem.  Další informace najdete v tématu [stránky s historií auditu](../portal-tour/cpp-history-page.md).


## <a name="next-steps"></a>Další postup

Můžete také použít portál partnerů cloudu k [odstranit publikované skladové položky nebo tuto nabídku](./cpp-delete-offer.md).
