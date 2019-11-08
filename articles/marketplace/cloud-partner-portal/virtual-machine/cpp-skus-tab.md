---
title: Karta SKU virtuálního počítače v portál partnerů cloudu pro Azure Marketplace
description: Popisuje kartu SKU, která se používá při vytváření nabídky virtuálního počítače v Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 101f928e296d0b48b4e077676393772829aa76ab
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808974"
---
# <a name="virtual-machine-skus-tab"></a>Karta SKU virtuálního počítače

Karta **SKU** na nové stránce **nabídky** umožňuje vytvořit jednu nebo více SKU a přidružit je k nové nabídce.  Různé SKU můžou řešení odlišit pomocí sad funkcí, typů imagí virtuálních počítačů, propustnosti nebo škálovatelnosti, modelů fakturace nebo některé jiné charakteristiky.


## <a name="create-a-sku"></a>Vytvoření SKU

Zpočátku nová nabídka nebude mít žádné přidružené SKU, takže ji vytvoříte kliknutím na **Nová SKU**.

![Tlačítko Nová SKU na nové kartě Nabídka pro virtuální počítače](./media/publishvm_005.png)

<br/>

Zobrazí se dialogové okno **Nová SKU** .  Zadejte identifikátor pro novou SKLADOVOU položku a pak klikněte na **OK**. (Viz níže pro konvence pojmenování identifikátorů.)  Na kartě **SKU** se nyní zobrazí pole, která jsou k dispozici pro úpravy.    Připojená hvězdička (*) v názvu pole označuje, že je požadovaná.

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![Karta SKU na novém formuláři nabídky pro virtuální počítače](./media/publishvm_006.png)

Následující tabulka popisuje účel, obsah a formátování těchto polí.  Požadovaná pole jsou indicted hvězdičkou (*).

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **Pole**       |     **Popis**                                                          |
|  ---------       |     ---------------                                                          |
|  *Nastavení SKU*   |    |
| **ID SKU\***       | Identifikátor této SKU  Tento název má maximálně 50 znaků, který se skládá z malých alfanumerických znaků nebo pomlček (-), ale nemůže končit pomlčkou.  Po publikování nabídky nelze změnit.  |
|  *Podrobnosti SKU*   |  |
| **\* názvu**        | Popisný název nabídky pro zobrazení na webu Marketplace Maximální délka 50 znaků. |
| **Souhrn\***      | Stručný popis nabídky pro zobrazení na webu Marketplace Maximální délka 100 znaků. |
| **Popis\***  | Popisný text, který poskytuje podrobnější vysvětlení nabídky.  <!-- TD: max len/guidance? 3k characters -->  |
| **Skrýt tento\* SKU** | Označuje, zda má být SKU na webu Marketplace viditelná pro zákazníky.  SKU můžete chtít skrýt, pokud chcete, aby byla k dispozici pouze prostřednictvím šablon řešení a nikoli pro jednotlivé nákupy.  Může to být užitečné i pro počáteční testování nebo pro dočasné nebo sezónní nabídky. |
| **\* dostupnosti cloudu** | Určuje, které cloudy by měly být k dispozici ve SKLADových položkách.  Výchozím nastavením je veřejná verze Azure.  Microsoft Azure Government je Cloud pro státní správu, který má řízený přístup pro USA federální, státní, místní nebo samosprávnéové vlády a jejich certifikované partnery.  Další informace o cloudu pro státní správu najdete v tématu [Vítá vás Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome). |
| **Je to privátní SKU?\*** | Označuje, zda je SKU soukromá nebo veřejná. Výchozí **hodnota není (Public** ).  Další informace najdete v tématu [veřejné a soukromé SKU](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md). |
| **\* dostupnosti země nebo oblasti** | Určuje, které země nebo světové oblasti budou vaše SKU dostupné k nákupu. Vyberte alespoň jednu oblast/zemi. <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *Ceny*   |  |
| **\* licenčního modelu**| Model normalizované fakturace, který se má použít.  Pokud vyberete **měsíční fakturovaná SKU na základě využití**, otevře se část pro poskytování služeb, která vám umožní zadat podrobnosti o cenách za jádro a jestli chcete nabídnout bezplatnou zkušební dobu.  Tato část také umožňuje exportovat a importovat tento cenový plán do aplikace Excel. Další informace najdete v tématu [Možnosti fakturace v Azure Marketplace](../../billing-options-azure-marketplace.md). | 
|  *Image virtuálních počítačů*   |  |
| **Řada operačních systémů\*** | Uvádí, zda je virtuální počítač řešení založený na systému Windows nebo Linux. |
| **Vyberte typ operačního systému** | Konkrétní dodavatel nebo vydání zadaného operačního systému. |
| **Popisný název OS\*** | Název operačního systému, který se má zobrazit pro zákazníky.  |
| **Doporučené velikosti virtuálních počítačů\*** | Umožňuje výběr až šesti doporučených velikostí virtuálních počítačů ze standardizovaného seznamu.  Tento seznam se předává spolu s Azure Portal a Microsoft Marketplace.  První velikost virtuálního počítače v tomto seznamu, která je platná (pro toto předplatné zákazníka, oblast, zónu atd.), je nastavená jako výchozí pro tohoto potenciálního zákazníka.  Uživatel může tuto velikost změnit na všechny kompatibilní s obrázkem řešení. | 
| **Otevřené porty**| Porty pro otevření a protokol pro podporu pro SKU.  Tyto konfigurace se musí shodovat s virtuální sítí, kterou jste nakonfigurovali pro síť virtuálního počítače řešení. Tato nastavení vstoupí v platnost během nasazování virtuálního počítače. Nastavení portů však lze změnit po publikování SKU. Další informace najdete v tématu [Postup otevření portů na virtuálním počítači s Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal). <br/>Následující výchozí mapování sítě se přidá do všech virtuálních počítačů. &emsp; Windows: 3389-> 3389 TCP, 5986-> 5986 TCP; &emsp; Linux: 22-> 22, TCP (SSH). |
| **Verze disku**  | Virtuální počítač přidruženého řešení určený číslem verze disku a adresou URL disku Verze disku musí být ve formátu [sémantické verze](https://semver.org/) : `<major>.<minor>.<patch>`.  Adresa URL je identifikátor URI sdíleného přístupového podpisu, který jste vytvořili pro virtuální pevný disk s operačním systémem.  I když můžete přidat až osm verzí disků na SKU, v Azure Marketplace se zobrazí jenom nejvyšší číslo verze disku SKU. Ostatní verze budou viditelné pouze přes rozhraní API.  <!--TD: Add more specific link to API --> <br/> Nová část přidělení **datových disků** vám umožní připojit k virtuálnímu počítači až 15 datových disků.  Po publikování SKU pomocí dané verze virtuálního počítače a přidružených datových disků nejde tuto konfiguraci upravit.  Pokud se do SKU přidají další verze virtuálních počítačů, musí také podporovat stejný počet datových disků. <br/> Pokud jste nevytvořili image virtuálních počítačů založených na Azure, můžete později přidat aktualizaci tohoto pole.  Informace o vytvoření přidruženého prostředku virtuálního počítače najdete v části [Vytvoření technických prostředků virtuálního počítače](./cpp-create-technical-assets.md).  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> Kliknutím na **Uložit** uložte svůj průběh. Na další kartě určíte, jestli vaše nabídka podporuje [testovací jednotku](./cpp-test-drive-tab.md).


## <a name="additional-pricing-considerations"></a>Další cenové doporučení

Výše popsaným cenovým modelem je základní popis.  Změny se projeví a můžou být ovlivněné místními nebo regionálními daňovými předpisy a cenovými politikami Microsoftu. 

### <a name="new-core-sizes-added-on-722019"></a>Nové základní velikosti přidané v 7/2/2019

Vydavatelé virtuálních počítačů byli informováni 2. července 2019 o přidání nových cen pro nové velikosti virtuálních počítačů Azure (na základě počtu jader).  Nové ceny jsou pro základní velikosti 10, 44, 48, 60, 120, 208 a 416.  Pro existující virtuální počítač se automaticky vypočítaly nové ceny pro tyto velikosti jader na základě aktuálních cen.  Vydavatelé mají od 1. srpna 2019 kontrolu nad dalšími cenami a provádět požadované změny.  Po tomto datu se projeví automaticky vypočtené ceny pro tyto nové základní velikosti, pokud už je Vydavatel znovu Nepublikovaná.


### <a name="simplified-currency-pricing"></a>Ceny pro zjednodušenou měnu

Od září 1 2018 se na portál přidá nový oddíl s názvem **zjednodušená měna** . Microsoft zjednodušuje Azure Marketplace podnikání tím, že umožňuje více předvídatelných cen a kolekcí od zákazníků po celém světě. Toto zjednodušení bude zahrnovat omezení počtu měn, ve kterých vaše zákazníky fakturují.  Další informace najdete v tématu [aktualizace existující nabídky virtuálních počítačů na Azure Marketplace](./cpp-update-existing-offer.md).


### <a name="additional-information-on-taxes-and-prices"></a>Další informace o daních a cenách

* Společnost Microsoft klasifikuje některé země nebo oblasti jako *daňové poukázané země*.  V takových zemích a oblastech společnost Microsoft shromažďuje daně od zákazníků a následně platí pro státní správu daně.  V jiných zemích nebo oblastech jsou partneři obvykle odpovědni za shromažďování daní ze svých zákazníků a vyplácení daní vládě. Pokud se rozhodnete prodávat v zemích nebo oblastech, musíte mít možnost počítat a platit místní daně.  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* Ceny se po živé nabídce nemění. Přesto však můžete přidat nebo odebrat podporované oblasti. 
* Společnost Microsoft se doúčtuje za standardní poplatky za využití virtuálních počítačů Azure pro zákazníky kromě vašich naplánovaných poplatků za SKU.
* Ceny jsou nastavené pro všechny oblasti v místní měně v dostupných měnách v době nastavení cen.  <!-- TD: Meaning? - Offer created, published, other? -->
* Pokud chcete nastavit cenu každé oblasti jednotlivě, exportujte prosím tabulku s cenami, použijte vlastní ceny a pak proveďte import. 


## <a name="next-steps"></a>Další kroky

Pokud tuto funkci podporujete, můžete zadat i informace o [testovací jednotce](./cpp-test-drive-tab.md) . jinak zadáte data z [Marketplace](./cpp-marketplace-tab.md) pro vaši nabídku.
