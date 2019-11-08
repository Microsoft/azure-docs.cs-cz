---
title: Aktualizace nabídek Marketplace | Azure Marketplace
description: Aktualizace nabídek na tržištích Azure a AppSource pomocí portál partnerů cloudu
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pabutler
ms.openlocfilehash: b3f579dbdc943b2380c9de3dde6b2ebf4754d4d1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826717"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>Aktualizace Azure Marketplace a nabídek AppSource

Existují různé druhy aktualizací, které můžete po publikování použít pro vaši nabídku.  [Portál partnerů cloudu](https://cloudpartner.azure.com/) vám pomůže se správnými úpravami atributů nabídky, včetně:

-  Přidání nové image virtuálního počítače (VM) nebo verze balíčku do existující SKU
-  Změna oblastí, ve kterých je SKU k dispozici
-  Přidávání nových SKU
-  Aktualizace metadat Marketplace pro nabídky nebo SKU 
-  Aktualizace cen pro nabídky s průběžnými platbami

Portál také obsahuje funkce, jako je například možnost porovnat funkce a zobrazit historii funkcí pro nabídku, která vám pomůže při správě změn.  Po úpravě nabídky nebo skladové položky je nutné ji znovu publikovat, aby se změny projevily jako "živé".  Tento článek vás provede různými aspekty aktualizace nabídky na webu Marketplace.

## <a name="unpermitted-changes-to-an-offersku"></a>Nepovolené změny nabídky/SKU

Existují některé atributy nabídky nebo SKU, které po publikování na webu Marketplace nelze upravovat.  Odpovídající pole jsou na kartě **Editor** na portálu zakázána, například:  

- ID nabídky a ID vydavatele
- ID SKU 
- Počet stávajících SKU dat pro datový disk
- Fakturační a licenční modelové změny stávajících SKU
- Značky verze, například: `1.0.1`


## <a name="common-update-operations"></a>Běžné operace aktualizace

Následující části vysvětlují, jak provést většinu operací aktualizace.  Tyto operace nejsou k dispozici pro všechny typy nabídek.  Aby bylo možné spustit některou z těchto operací, je nutné se přihlásit k portál partnerů cloudu.


### <a name="update-offer-contacts"></a>Aktualizovat kontakty nabídky

Pomocí následujících kroků aktualizujte kontakty podpory pro vaši nabídku.
1. Na stránce **všechny nabídky** vyberte nabídku.
2. Vyberte kartu **Kontakty** . aktualizujte své kontakty.
3. Vyberte tlačítko **Uložit**.
4. Vyberte **publikovat** a spusťte proces publikování.


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>Změna oblastí nabídka nebo SKU jsou k dispozici v

V průběhu času možná budete chtít, aby vaše nabídka/SKU byla dostupná ve více oblastech.
Alternativně můžete chtít zastavit podporu nabídky/SKU v dané oblasti.
Chcete-li tyto změny implementovat, postupujte podle následujících kroků.

1. Na stránce **všechny nabídky** Vyhledejte nabídku, kterou chcete aktualizovat.

Pro Azure Marketplace nabídky:

1. Vyberte kartu **SKU** .  Vyberte skladovou jednotku, kterou chcete upravit.
1. Klikněte na tlačítko **Vybrat země** v poli **dostupnost země/oblasti** .
1. V dialogovém okně dostupnost oblasti přidejte nebo odeberte oblasti této SKU.

Pro AppSource nabídky:

1. Vyberte kartu **Podrobnosti prezentace** .
1. Vedle popisku **Podporované země/oblasti** klikněte na možnost **Podporované země/oblasti**. 
1. V dialogovém okně podporované země nebo oblasti přidejte nebo odeberte oblasti pro tuto nabídku.

Pro obě Marketplace:

1. Kliknutím na **publikovat** spusťte proces publikování. 

Pokud je SKU dostupná v nové oblasti, máte možnost zadat pro tuto konkrétní oblast ceny pomocí funkce **exportovat cenové údaje** . Pokud přidáváte oblast zpátky, která byla dříve k dispozici, nemůžete její ceny aktualizovat, protože cenové změny nejsou povoleny.


### <a name="add-a-new-sku"></a>Přidat novou SKLADOVOU položku 

Chcete-li zpřístupnit novou SKLADOVOU položku pro existující nabídku, použijte následující postup:

1. Na stránce **všechny nabídky** Najděte nabídku.
3. Ve formuláři **SKU** klikněte na **Přidat novou skladovou** položku a v automaticky otevíraném okně zadejte **ID SKU** .
4. Postupujte podle zbývajících kroků uvedených v části [publikování nabídky virtuálního počítače](../virtual-machine/cpp-publish-offer.md).
5. Kliknutím na **publikovat** spusťte proces publikování.


### <a name="update-offer-marketplace-assets"></a>Aktualizace assetů nabídky Marketplace

Můžete mít scénáře, kdy potřebujete aktualizovat textový a obrázkové materiály na webu Marketplace, jako jsou například loga společnosti, popis nabídky atd. K aktualizaci těchto prostředků použijte následující postup.

1. Na stránce **všechny nabídky** Najděte svoji nabídku. 
2. Vyberte kartu **Marketplace** a postupujte podle pokynů v tématu na *kartě Marketplace* vaší nabídky.
3. Kliknutím na **publikovat** spusťte proces publikování.


### <a name="update-pricing-on-published-offers"></a>Aktualizace cen u publikovaných nabídek

Po publikování vaší nabídky s průběžnými platbami nelze zvýšit cenu existující SKU.  Místo toho vytvořte SKU v rámci stejné nabídky, odstraňte původní SKU a pak znovu publikujte vaši nabídku. Můžete snížit cenu z dříve publikovaných nabídek. Snížení ceny vaší nabídky:

1. Vyberte skladovou jednotku, pro kterou chcete snížit ceny.
2. Nižší cena se musí nastavit stejným mechanismem, jaký jste původně použili: buď přímo v uživatelském rozhraní portálu, nebo v tabulce import/export.
3. Klikněte na **Uložit**.
4. Kliknutím na **publikovat** spusťte proces publikování.

Ceny se pro nové zákazníky zobrazují hned po jeho živém pobytu a všichni noví zákazníci pak budou za novou sníženou cenu platit.  U stávajících zákazníků se snížení ceny odrazí zpětně na začátek fakturačního cyklu, během kterého se snížení ceny projeví.  Pokud již byly za cyklus, během kterých došlo k poklesu cen, účtovány, obdrží během příštího fakturačního cyklu refundaci, aby se pokryla snížená cena.


## <a name="compare-feature"></a>Compare – funkce

Když provedete změny v publikované nabídce, můžete pomocí funkce *Compare* auditovat změny. K využití této funkce:

1. V jakémkoli okamžiku v procesu úprav můžete kliknout na tlačítko **Porovnat** na kartě **Editor** pro vaši nabídku.
2. Okno porovnání zobrazuje souběžné verze uložených změn této nabídky ve srovnání s nabídkou na webu Marketplace. 

![Tlačítko nabídky porovnat na kartě editoru](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>Historie akcí publikování

Chcete-li zobrazit historické aktivity publikování, vyberte kartu **Historie** v levém svislém řádku portál partnerů cloudu.  Stránka Historie poskytuje flexibilní filtrování podle několika vlastností a podporuje řazení sloupců.  Každé události publikování je časové razítko.  Další informace najdete na [stránce Historie auditu](../portal-tour/cpp-history-page.md).


## <a name="next-steps"></a>Další kroky

Můžete také použít portál partnerů cloudu k [odstranění publikované SKU nebo nabídky](./cpp-delete-offer.md).
