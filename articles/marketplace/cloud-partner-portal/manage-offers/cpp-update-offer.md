---
title: Aktualizace nabídky tržiště | Azure Marketplace
description: Aktualizace nabídek na tržištích Azure a AppSource pomocí portálu partnerů cloudu
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: dsindona
ms.openlocfilehash: 101369a050770be3acd9534cef6229037fe1c366
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288508"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>Aktualizace nabídek Azure Marketplace a AppSource

Existují různé druhy aktualizací, které můžete použít na nabídku po publikování.  [Portál partnerů cloudu](https://cloudpartner.azure.com/) vám pomůže správně upravit atributy nabídky, včetně:

-  Přidání nové image virtuálního počítače nebo verze balíčku do existující skladové položky
-  Změna oblastí, ve kterých je skladová položka k dispozici
-  Přidání nových sous
-  Aktualizace metadat marketplace pro nabídky nebo sku 
-  Aktualizace cen v nabídkách průběžných plateb

Portál má také funkce, jako je možnost porovnat funkce a zobrazit historii funkcí pro nabídku, které vám pomohou při správě změn.  Po úpravě nabídky nebo skladové položky musí být znovu publikována před změnou přejít "live".  Tento článek vás provede různými aspekty aktualizace nabídky marketplace.

## <a name="unpermitted-changes-to-an-offersku"></a>Nepovolené změny nabídky/skladové položky

Existují některé atributy nabídky nebo skladové položky, které nelze změnit, jakmile byla publikována na trhu.  Odpovídající pole jsou zakázána na kartě **Editor** na portálu, například:  

- ID nabídky a ID vydavatele
- ID skladové položky 
- Počet datových disků existujících sku
- Změny fakturačního/licenčního modelu stávajících sku
- Značky verzí, například:`1.0.1`


## <a name="common-update-operations"></a>Běžné operace aktualizace

V následujících částech je vysvětleno, jak provádět některé z nejvíce operací aktualizace.  Tyto operace nejsou k dispozici pro všechny typy nabídek.  Chcete-li spustit některou z těchto operací, musíte se přihlásit k portálu partnerů cloudu.


### <a name="update-offer-contacts"></a>Aktualizovat kontakty nabídky

Pomocí následujících kroků aktualizujte kontakty podpory pro vaši nabídku.
1. Na stránce **Všechny nabídky** vyberte nabídku.
2. Vyberte kartu **Kontakty.** Aktualizujte kontakty.
3. Vyberte tlačítko **Uložit**.
4. Chcete-li zahájit proces publikování, vyberte **publikovat.**


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>Změnit oblasti, v kterých je nabídka nebo skladová položka k dispozici

V průběhu času můžete chtít, aby vaše nabídka nebo skladová položka k dispozici ve více oblastech.
Případně můžete chtít přestat podporovat nabídku nebo skladovou položku v dané oblasti.
Chcete-li tyto změny implementovat, postupujte podle následujících kroků.

1. Na stránce **Všechny nabídky** najděte nabídku, kterou chcete aktualizovat.

Pro nabídky Azure Marketplace:

1. Vyberte kartu **SKU.**  Vyberte skladovou položku, kterou chcete upravit.
1. Klikněte na tlačítko **Vybrat země** v poli Dostupnost země **nebo oblasti.**
1. V dialogovém okně dostupnost oblasti přidejte nebo odeberte oblasti pro tuto skladovou položku.

Pro nabídky AppSource:

1. Vyberte kartu **Podrobnosti na výloze.**
1. Vedle popisku **Podporované země nebo oblasti** klikněte na Podporované země nebo **oblasti**. 
1. V dialogovém okně Podporované země nebo oblasti přidejte nebo odeberte oblasti pro tuto nabídku.

Pro oba tržiště:

1. Chcete-li zahájit proces publikování, klepněte na tlačítko **Publikovat.** 

Pokud je skladová položka k dispozici v nové oblasti, máte možnost zadat ceny pro tuto konkrétní oblast prostřednictvím funkce **Exportovat data ocenění.** Pokud přidáváte oblast zpět, která byla dříve k dispozici, nelze aktualizovat její ceny, protože změny cen nejsou povoleny.


### <a name="add-a-new-sku"></a>Přidání nové skladové položky 

Chcete-li zpřístupnit novou skladovou položku pro existující nabídku, postupujte takto:

1. Na stránce **Všechny nabídky** najděte nabídku.
3. Ve formuláři **Skladové položky** klikněte na **Přidat novou skladovou položku** a ve automaticky otevíraném tlačítku zadejte **ID skladové** položky.
4. Postupujte podle zbývajících kroků popsaných v [části Publikovat nabídku virtuálního počítače](../virtual-machine/cpp-publish-offer.md).
5. Chcete-li zahájit proces publikování, klepněte na tlačítko **Publikovat.**


### <a name="update-offer-marketplace-assets"></a>Aktualizovat prostředky tržiště nabídky

Můžete mít scénáře, kde je třeba aktualizovat marketplace text-založené a image aktiv, jako jsou vaše společnost loga, popis nabídky, atd. Pomocí následujících kroků aktualizujte tyto datové zdroje.

1. Na stránce **Všechny nabídky** najděte svou nabídku. 
2. Vyberte kartu **Marketplace** a postupujte podle pokynů v tématu karty Marketplace v *nabídce.*
3. Chcete-li zahájit proces publikování, klepněte na tlačítko **Publikovat.**


### <a name="update-pricing-on-published-offers"></a>Aktualizovat ceny publikovaných nabídek

Jakmile je vaše průběžná nabídka zveřejněna, nemůžete zvýšit cenu stávající skladové položky.  Místo toho vytvořte skladovou položku v rámci stejné nabídky, odstraňte starou skladovou položku a pak nabídku znovu publikujte. U dříve publikovaných nabídek můžete snížit cenu. Chcete-li snížit nabídkovou cenu:

1. Vyberte skladovou položku, pro kterou chcete snížit ceny.
2. Nižší cenu je nutné nastavit stejným mechanismem, který jste původně použili: buď přímo v unovém portálu, nebo v tabulce importu a exportu.
3. Klikněte na **Uložit**.
4. Chcete-li zahájit proces publikování, klepněte na tlačítko **Publikovat.**

Ceny jsou viditelné pro nové zákazníky, jakmile je žít na trhu, a všichni noví zákazníci pak zaplatí novou sníženou cenu.  U stávajících zákazníků se pokles ceny odráží zpětně na začátku fakturačního cyklu, během něhož pokles ceny nabyl účinnosti.  Pokud již byly fakturovány za cyklus, během kterého došlo ke snížení ceny, obdrží refundaci během dalšího fakturačního cyklu, aby pokryli sníženou cenu.


## <a name="compare-feature"></a>Funkce Porovnat

Když provedete změny v publikované nabídce, můžete ke auditování změn použít funkci *Porovnat.* Využití této funkce:

1. Kdykoli v procesu úprav můžete kliknout na tlačítko **Porovnat** na kartě **Editor** pro vaši nabídku.
2. Srovnávací okno zobrazuje souběžné verze uložených změn této nabídky ve srovnání s nabídkou marketplace. 

![Tlačítko Porovnat nabídku na kartě editoru](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>Historie publikování akcí

Chcete-li zobrazit historickou publikační aktivitu, vyberte kartu **Historie** v levém svislém panelu nabídek portálu partnerů cloudu.  Historie stránka poskytuje flexibilní filtrování podle několika charakteristik a podporuje řazení sloupců.  Každá událost publikování je označena časovým razítkem.  Další informace naleznete na [stránce Historie auditu](../portal-tour/cpp-history-page.md).


## <a name="next-steps"></a>Další kroky

Pomocí portálu cloudových partnerů můžete také [odstranit publikovanou skladovou položku nebo nabídku](./cpp-delete-offer.md).
