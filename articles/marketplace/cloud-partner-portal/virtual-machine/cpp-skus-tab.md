---
title: Karta skladové položky virtuálních počítačů v portál partnerů cloudu pro Azure Marketplace | Dokumentace Microsoftu
description: Popisuje skladové položky karty použité při vytváření nabídky virtuálních počítačů na webu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 4ecc259d40cdcba93a484f27e27191e967f10ff1
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639623"
---
# <a name="virtual-machine-skus-tab"></a>Karta skladové položky virtuálních počítačů

**SKU** karty **nová nabídka** stránka umožňuje vytvořit jeden nebo více skladové položky a přidružovat je k nové nabídky.  Různé skladové položky lze rozlišit řešení sady funkcí, typy imagí virtuálních počítačů, propustnost nebo škálovatelnost, modely fakturace nebo některé typické.

## <a name="create-a-sku"></a>Vytvoří skladová jednotka

Na začátku novou nabídku nebude mít žádné přidružené skladové položky, takže vytvoříte jeden kliknutím **novou skladovou Položku**.

![Tlačítko Nová skladovou Položku na kartě nová nabídka pro virtuální počítače](./media/publishvm_005.png)

<br/>

**Novou skladovou Položku** se zobrazí dialogové okno.  Zadejte identifikátor pro novou skladovou Položku a pak klikněte na tlačítko **OK**. (Viz níže pro identifikátor zásady vytváření názvů.)  **SKU** kartu se teď budou zobrazovat dostupná pole pro úpravy.    Připojený hvězdička (*) na název pole označuje, že je povinný.

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![Karta SKU pro ve formuláři nová nabídka pro virtuální počítače](./media/publishvm_006.png)

Následující tabulka popisuje účel, obsah a formátování z těchto polí.

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **Pole**       |     **Popis**                                                          |
|  ---------       |     ---------------                                                          |
|  *Nastavení SKU*   |  |
| **SKU ID**       | Identifikátor pro toto SKU.  Tento název může mít nejvýše 50 znaků, který se skládá z malé alfanumerické znaky nebo spojovníky (-), ale nemůže končit spojovníkem.  Nelze změnit po publikování nabídky.  |
|  *Podrobné údaje SKU*   |  |
| **Název**        | Popisný název nabídky. pro zobrazení na webu Marketplace. Maximální délka 50 znaků. |
| **Souhrn**      | Stručný popis nabídky pro zobrazení na webu Marketplace. Delší než 100 znaků. |
| **Popis**  | Text popisu, který poskytuje podrobnější vysvětlení nabídky.  <!-- TD: max len/guidance? 3k characters -->  |
| **Skrýt toto SKU** | Určuje, zda SKU by měl být viditelný na webu Marketplace pro zákazníky.  Můžete chtít skrýt skladovou Položku, pokud chcete pouze je k dispozici pouze prostřednictvím šablon řešení a není pro nákup jednotlivě.  Může být také užitečné pro počáteční testování nebo pro dočasné nebo sezónní nabídky. |
| **Dostupnost cloudu** | Určuje, na cloudy, které by měly být dostupné SKU.  Výchozí hodnota je ve veřejné verzi Azure.  Microsoft Azure Government je cloud určený pro státní úřady s řízený přístup federální, stavu, místní nebo samosprávné instituce a jejich certifikovaných partnerů.  Další informace o cloudu pro státní správu, najdete v části [Vítá vás Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome). |
| **Je to privátní SKU?** | Určuje, zda SKU je privátní nebo veřejné. Výchozí hodnota je **ne** (veřejné).  Další informace najdete v tématu [veřejné a privátní SKU](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md). |
| **Dostupnost země/oblast** | Určuje, které země nebo oblasti world budou dostupné ke koupi skladové jednotky. Vyberte alespoň jednu oblast nebo zemi. <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *Ceny*   |  |
| **Licenční Model**| Standardizované fakturační model se má použít.  Pokud vyberete **podle využití každý měsíc fakturovat SKU**, ovládacího prvku accordion části otevřete umožňující vám zadat podrobnosti o cenách za jádro a, jestli chcete nabídky bezplatné zkušební období.  Tato část také umožňuje exportovat a importovat do aplikace Excel tento cenový plán. Další informace najdete v tématu [možnosti fakturace na webu Azure Marketplace](../../billing-options-azure-marketplace.md). | 
|  *Image virtuálních počítačů*   |  |
| **Řada operačních systémů** | Označuje, jestli řešení virtuálních počítačů založené na Windows nebo Linuxu. |
| **Vyberte typ operačního systému** | Konkrétní dodavatele nebo verzi zadaný operační systém. |
| **Popisný název OS** | Název operačního systému, který se má zobrazit pro zákazníky.  |
| **Doporučené velikosti virtuálních počítačů** | Umožňuje výběr až šest doporučené velikosti virtuálních počítačů ze seznamu standardizované.  Přestože tato doporučení jsou viditelném umístění pro potenciální zákazníky, jsou zadat jakoukoli velikost virtuálního počítače, který je kompatibilní s touto imagí řešení. | 
| **Otevření portů**| Porty otevřete a protokol pro podporu pro danou skladovou jednotku.  Tyto konfigurace se musí shodovat virtuální sítě, které jste nakonfigurovali pro síť virtuálních počítačů řešení. Tato nastavení vstoupí během nasazení virtuálního počítače. Však nastavení portu můžete změnit po publikování SKU. Další informace najdete v tématu [postup otevření portů k virtuálnímu počítači pomocí webu Azure portal](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal). <br/>Následující výchozí mapování sítě se přidají ke všem virtuálním počítačům. &emsp; Windows: 3389 TCP -> 3389, 5986 -> 5986 TCP; &emsp; Linux: 22 -> 22 TCP (SSH). |
| **Disková verze**  | Související řešení virtuálních počítačů, určené číslo verze disku a adresa URL disku. Verze disku musí být v [sémantickou verzi](http://semver.org/) formátu: `<major>.<minor>.<patch>`.  Adresa URL je sdílený přístupový podpis, který vytvořit identifikátor URI pro operační systém virtuálního pevného disku.  I když můžete přidat až 8 diskových verzí na SKU, pouze nejvyšší verze číslo disku SKU se zobrazí na webu Azure Marketplace. Jiné verze budou zobrazeny pouze prostřednictvím rozhraní API.  <!--TD: Add more specific link to API --> <br/> **Nový datový disk** ovládacího prvku accordion oddíl umožňuje vám připojit až 15 datových disků k virtuálnímu počítači.  Jakmile publikujete SKU s danou verzí virtuálních počítačů a přidružených datových disků, tuto konfiguraci nelze upravit.  Pokud pro danou skladovou jednotku nechejte se přidat další verze virtuálního počítače, musí taky podporovat stejný počet datových disků. <br/> Pokud jste ještě nevytvořili vaší Image virtuálního počítače založené na Azure, můžete přidat později aktualizovat toto pole.  Informace o vytváření přidružený prostředek virtuálního počítače najdete v části [technických prostředků vytvořit virtuální počítač](./cpp-create-technical-assets.md).  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> Klikněte na tlačítko **Uložit** uložte svůj postup. Na další kartě můžete zadat při podporuje vaši nabídku [Test Drive](./cpp-test-drive-tab.md).


## <a name="additional-pricing-considerations"></a>Další důležité informace o cenách

Cenového modelu popsaného výše je základní popis.  Probíhá změny a mohou být ovlivněny daňové místní nebo místní předpisy a Microsoft ceny zásad. 

### <a name="simplified-currency-pricing"></a>Ceny zjednodušené měny

Od 1. září 2018 se nazývá obsahují nový oddíl **zjednodušená ceny měny** přidá se k portálu. Microsoft je zjednodušení obchodních Azure Marketplace povolením více předvídatelné ceny a kolekce od svých zákazníků po celém světě. Toto zjednodušení bude obsahovat snížení počtu měny, ve kterých jsme vašim zákazníkům faktury.  Další informace najdete v tématu [aktualizace stávajícího virtuálního počítače nabízet na webu Azure Marketplace](./cpp-update-existing-offer.md).


### <a name="additional-information-on-taxes-and-prices"></a>Další informace o daní a ceny

* V některých zemích jako klasifikuje Microsoft *daně uhrazené zemí*.  V těchto zemích Microsoft shromažďuje daně od zákazníků, pak platí (vykazovány) daň vládě.  V jiných zemích se většinou pojí shromažďování daně od svých zákazníků a platit daň vládě partnery. Pokud chcete prodávat v druhém zemí, musíte mít možnost Vypočítat a hradit daně, které místní.  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* Ceny nejsou změnit, jakmile uvedete nabídky. Mohou však stále přidat nebo odebrat oblasti jsou podporované. 
* Microsoft účtuje využití poplatky standardní virtuální počítač Azure zákazníka kromě vaše naplánované poplatků za skladové položky.
* Ceny se nastavují pro všechny oblasti v místní měně na dostupné směnné kurzy v době nastavení cen.  <!-- TD: Meaning? - Offer created, published, other? -->
* Cena každé oblasti nastavit samostatně, prosím cenovou tabulku exportovat, použití vlastních cen a potom importovat. 

<!-- TD: The detailed information in the table and supplemental notes should be centralized in another topic, maybe "Billing Options" in AMP tree. Need to include a common section on export/import pricing-->

