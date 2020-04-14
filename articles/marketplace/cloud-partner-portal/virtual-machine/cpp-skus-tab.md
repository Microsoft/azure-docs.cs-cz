---
title: Karta SKU virtuálních strojů na portálu cloudových partnerů pro Azure Marketplace
description: Popisuje kartu SKU používanou při vytváření nabídky virtuálních strojů na Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: a8dbadf959107e733a94216c8a01495e6ebf5039
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273116"
---
# <a name="virtual-machine-skus-tab"></a>Karta SKU virtuálního počítače

> [!IMPORTANT]
> dubna 2020 začneme přesouvat správu nabídek virtuálního počítače Azure do Centra partnerů. Po migraci vytvoříte a spravujete nabídky v Centru partnerů. Postupujte podle pokynů v [části Vytvoření nabídky virtuálního počítače Azure](https://aka.ms/CreateAzureVMoffer) ke správě migrovaných nabídek.

Karta **SKU** na stránce **Nová nabídka** umožňuje vytvořit jednu nebo více slok a přidružit je k nové nabídce.  Různé sku mohou rozlišit řešení podle sad funkcí, typů bitových bitových obrazů virtuálních počítačů, propustnosti nebo škálovatelnosti, fakturačních modelů nebo jiné charakteristiky.


## <a name="create-a-sku"></a>Vytvoření skladové položky

Zpočátku nová nabídka nebude mít žádné přidružené skladové položky, takže ji vytvoříte klepnutím na **položku Nová skladová položka**.

![Tlačítko Nová skladová položka na kartě Nová nabídka pro virtuální počítače](./media/publishvm_005.png)

<br/>

Zobrazí se dialogové okno **Nová** skladová položka.  Zadejte identifikátor nové skladové položky a klepněte na tlačítko **OK**. (Níže naleznete konvence pojmenování identifikátorů.)  Na kartě **SKU** se nyní zobrazí pole, která jsou k dispozici pro úpravy.    Připojená hvězdička (*) k názvu pole označuje, že je požadována.

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![Karta SKU ve formuláři Nová nabídka pro virtuální počítače](./media/publishvm_006.png)

Následující tabulka popisuje účel, obsah a formátování těchto polí.  Povinná pole jsou obžalována hvězdičkou (*).

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **Pole**       |     **Popis**                                                          |
|  ---------       |     ---------------                                                          |
|  *Nastavení skladové položky*   |    |
| **ID skladové položky\***       | Identifikátor této skladové položky.  Tento název má maximálně 50 znaků, skládající se z malých alfanumerických znaků nebo pomlčky (-), ale nemůže končit pomlčkou.  Po zveřejnění nabídky jej nelze změnit.  |
|  *Podrobnosti o skladové jednotce*   |  |
| **Název\***        | Popisný název pro nabídku pro zobrazení na trhu. Maximální délka 50 znaků. |
| **Souhrn\***      | Stručný popis nabídky k vystavení na trhu. Maximální délka 100 znaků. |
| **Popis\***  | Popisný text, který poskytuje podrobnější vysvětlení nabídky.  <!-- TD: max len/guidance? 3k characters -->  |
| **Skrýt tuto skladovou položku\*** | Označuje, zda skladová položka by měla být viditelná na trhu pro zákazníky.  Skladovou položku můžete skrýt, pokud chcete, aby byla k dispozici pouze prostřednictvím šablon řešení a nikoli k nákupu jednotlivě.  To by mohlo být také užitečné pro počáteční testování nebo pro dočasné nebo sezónní nabídky. |
| **Dostupnost cloudu\*** | Určuje, na kterých cloudech by měla být skladová položka k dispozici.  Výchozí je veřejná verze Azure.  Microsoft Azure Government je cloud vládní komunity s kontrolovaným přístupem pro federální, státní, místní nebo kmenové vlády USA a jejich certifikované partnery.  Další informace o vládním cloudu najdete [v tématu Vítá vás Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome). |
| **Je to vojína SKU?\*** | Označuje, zda je skladová položka soukromá nebo veřejná. Výchozí hodnota je **Ne** (veřejné).  Další informace naleznete [v tématu Veřejné a soukromé sku.](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) |
| **Dostupnost země/oblasti\*** | Určuje, které země nebo oblasti světa bude vaše skladová položka k dispozici ke koupi. Vyberte alespoň jednu oblast nebo zemi. <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *Ceny*   |  |
| **Licenční model\***| Standardizovaný model fakturace.  Pokud vyberete **měsíční fakturovnou skladovou položku založenou na využití**, otevře se oddíl akordeon, který vám umožní zadat podrobnosti o cenách podle jádra a o tom, zda chcete nabídnout bezplatné zkušební období.  Tato část také umožňuje exportovat a importovat tento cenový plán do aplikace Excel. Další informace najdete [v tématu Možnosti fakturace na Webu Azure Marketplace](../../billing-options-azure-marketplace.md). | 
|  *Obrazy virtuálních obrazovek*   |  |
| **Rodina operačního systému\*** | Označuje, jestli je virtuální počítač na bázi windows nebo linuxové. |
| **Vybrat typ operačního systému** | Konkrétní dodavatel nebo uvolnění zadaného operačního systému. |
| **Popisný název operačního serveru\*** | Název operačního systému, který má být zobrazen zákazníkům.  |
| **Doporučené velikosti virtuálních počítače\*** | Umožňuje výběr až šesti doporučených velikostí virtuálních počítače ze standardizovaného seznamu.  Tento seznam se předává na portál Azure a tržiště Microsoftu.  První velikost virtuálního počítače v tomto seznamu, která je platná (pro toto předplatné zákazníka, oblast, zónu atd.), je nastavena jako výchozí pro tohoto potenciálního zákazníka.  Uživatel může tuto velikost změnit na libovolnou kompatibilní s bitovou kopii řešení. | 
| **Otevřené porty**| Porty otevřít a protokol pro podporu skladové položky.  Tyto konfigurace musí odpovídat virtuální síti, kterou jste nakonfigurovali pro síť virtuálního počítače řešení. Tato nastavení vstoupí v platnost během nasazení virtuálního počítače. Nastavení portu však lze změnit po publikování skladové položky. Další informace najdete v tématu věnovaném [otevření portů pro virtuální počítač s využitím webu Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal). <br/>Následující výchozí mapování sítě jsou přidány do všech virtuálních aplikací. &emsp;Windows: 3389 -> 3389 TCP, 5986 -> 5986 TCP; &emsp; Linux: 22 -> 22, TCP (SSH). |
| **Verze disku**  | Přidružený virtuální virtuální modul řešení určený číslem verze disku a adresou URL disku. Verze disku musí být ve formátu `<major>.<minor>.<patch>` [sémantické verze:](https://semver.org/) .  Adresa URL je identifikátor URI sdíleného přístupového podpisu vytvořený pro virtuální pevný disk operačního systému.  I když můžete přidat až osm verzí disku na skladovou položku, na Azure Marketplace se zobrazí pouze nejvyšší číslo verze disku pro skladovou položku. Ostatní verze budou viditelné pouze prostřednictvím api.  <!--TD: Add more specific link to API --> <br/> **Nový datový disk** akordeon části umožňuje připojit až 15 datových disků k virtuálnímu počítači.  Po publikování skladové položky s danou verzí virtuálního počítače a přidruženými datovými disky nelze tuto konfiguraci změnit.  Pokud se do skladové položky přidají další verze virtuálních počítačů, musí také podporovat stejný počet datových disků. <br/> Pokud jste nevytvořili image virtuálních počítače založené na Azure, můžete přidat aktualizovat toto pole později.  Informace o vytvoření přidruženého prostředku virtuálního zařízení najdete v části [Vytvoření technických prostředků virtuálního virtuálního zařízení](./cpp-create-technical-assets.md).  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> Kliknutím na **Uložit** uložte svůj postup. Na další kartě určíte, zda vaše nabídka podporuje [testdrive](./cpp-test-drive-tab.md).


## <a name="additional-pricing-considerations"></a>Další aspekty stanovení cen

Výše popsaný cenový model je základním popisem.  Prochází změnami a může být ovlivněnmístními nebo regionálními daňovými předpisy a cenovými zásadami společnosti Microsoft. 

### <a name="new-core-sizes-added-on-722019"></a>Nové velikosti jader přidané dne 7/2/2019

2. července 2019 byli vydavatelé virtuálních počítače upozorněni na přidání nových cen pro nové velikosti virtuálních automatů Azure (na základě počtu jader).  Nové ceny jsou pro velikosti jádra 10, 44, 48, 60, 120, 208 a 416.  Pro stávající virtuální počítač nabízí nové ceny pro tyto velikosti jader byly automaticky vypočteny na základě aktuálních cen.  Srpen 2019 mají vydavatelé čas do 1.  Po tomto datu, pokud již není vydavatelem znovu publikován, se automaticky vypočtené ceny pro tyto nové velikosti jádra projeví.


### <a name="simplified-currency-pricing"></a>Zjednodušené ceny měn

září 2018 bude na portál přidánnový oddíl s názvem **Zjednodušené oceňování měn.** Microsoft zefektivňuje podnikání na Azure Marketplace tím, že umožňuje předvídatelnější ceny a kolekce od vašich zákazníků po celém světě. Toto zefektivnění bude zahrnovat snížení počtu měn, ve kterých fakturujeme vašim zákazníkům.  Další informace najdete [v tématu Aktualizace existující nabídky virtuálních počítačů na Azure Marketplace](./cpp-update-existing-offer.md).


### <a name="additional-information-on-taxes-and-prices"></a>Další informace o daních a cenách

* Společnost Microsoft klasifikuje některé země nebo oblasti jako *země propouštěné daně*.  V těchto zemích nebo oblastech společnost Microsoft vybírá daně od zákazníků a poté platí (platí) daně vládě.  V jiných zemích nebo oblastech jsou partneři obvykle zodpovědní za výběr daní od svých zákazníků a placení daní vládě. Pokud se rozhodnete prodávat v těchto zemích nebo oblastech, musíte mít možnost vypočítat a zaplatit místní daně.  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* Ceny nejsou měnitelné, jakmile nabídka bude v provozu. Můžete však stále přidávat nebo odebírat podporované oblasti. 
* Společnost Microsoft účtuje zákazníkům poplatky za standardní využití virtuálních počítačů Azure navíc k plánovaným poplatkům za skladovou položku.
* Ceny jsou nastaveny pro všechny oblasti v místní měně podle dostupných měnových kurzů v době nastavení cen.  <!-- TD: Meaning? - Offer created, published, other? -->
* Chcete-li nastavit cenu jednotlivých oblastí jednotlivě, exportujte tabulku cen, použijte vlastní ceny a pak importujte. 


## <a name="next-steps"></a>Další kroky

Volitelně zadáte informace [o testovací dispo- žití,](./cpp-test-drive-tab.md) pokud tuto funkci podporujete. v opačném případě poskytnete data [tržiště](./cpp-marketplace-tab.md) pro vaši nabídku.
