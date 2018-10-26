---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 09/20/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 6ad38d2dc1c5c41dc10685d680f70c59e7983cd2
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50035388"
---
Sdílené Galerie Imagí je služba, která vám pomůže sestavit strukturu a organizace vlastních imagí virtuálních počítačů. Sdílené Galerie obrázků poskytuje tři hlavní prvky
- Jednoduchá správa
- Změny velikosti obrázků zákazníka
- Sdílení vašich imagí – sdílení imagí na jiné uživatele, instančních objektů nebo skupin AD v rámci vaší organizace i v různých oblastech pomocí replikace ve více oblastech

Spravované image je kopie buď úplná virtuálního počítače (včetně jakýchkoliv připojených datových disků) nebo jenom disk s operačním systémem, v závislosti na tom, jak vytvořit image. Při vytváření virtuálního počítače z image kopírování virtuálních pevných disků na obrázku se používají k vytvoření disky pro nový virtuální počítač. Spravované image zůstává v úložišti a je možné znovu a znovu vytvořit nové virtuální počítače.

Pokud máte velký počet spravované Image, které je potřeba, abyste a chcete zpřístupnit v rámci vaší společnosti, můžete jako úložiště, který umožňuje snadno aktualizovat a sdílet vaše Image Galerie sdílené bitové kopie. Poplatky za používání galerie sdílené bitové kopie jsou jenom náklady na úložiště využitá službou imagí a navíc všechny náklady na celkový výstup sítě při replikaci imagí ze zdrojové oblasti do publikované oblastí.

Galerie obrázků sdílené funkce má více typů prostředků:

| Prostředek | Popis|
|----------|------------|
| **Spravované image** | Toto je základní image, který můžete používat samostatně nebo použít k vytvoření více **sdílené bitové kopie verze** v Galerie obrázků.|
| **Galerie obrázků** | Na webu Azure Marketplace, jako jsou **Galerie obrázků** je úložiště pro správu a sdílení imagí, ale vy řídíte, kdo má přístup. |
| **Image z Galerie** | Bitové kopie jsou definovány v rámci Galerie a budou mít informace o požadavcích pro interní použití a image. To zahrnuje, jestli obrázek je Windows nebo Linux, poznámky k verzi a požadavky na minimální a maximální paměť. Tento typ bitové kopie je prostředek v rámci modelu nasazení Resource Manager, ale není použit přímo pro vytváření virtuálních počítačů. Je to definice typu bitové kopie. |
| **Verze sdílené bitové kopie** | **Verze image** se používá k vytvoření virtuálního počítače, když použijete galerii. Podle potřeby pro vaše prostředí můžete mít více verze Image. Při použití, jako jsou spravované image **verze image** vytvoření virtuálního počítače, verze image slouží k vytvoření nové disky pro virtuální počítač. Verze Image můžete použít více než jednou. |

<br>


![Obrázek znázorňující, jak může mít více verzí image v galerii](./media/shared-image-galleries/shared-image-gallery.png)

### <a name="regional-support"></a>Místní podpora

Místní podpora pro sdílené bitové kopie Galerie je omezený, ale se rozbalí v čase. Pro verzi preview, tady jsou seznamy ve kterém můžete vytvořit galerie a oblasti kde můžete replikovat libovolné galerii: 

| Vytvoření galerie v  | Verze, kterou chcete replikaci |
|--------------------|----------------------|
| Západní střed USA    |Střed USA – jih|
| Východní USA 2          |USA – východ|
| Střed USA – jih   |Východní USA 2|
| Jihovýchodní Asie     |USA – západ|
| Západní Evropa        |USA – západ 2|
|                    |USA – střed|
|                    |Střed USA – sever|
|                    |Kanada – střed|
|                    |Kanada – východ|
|                    |Severní Evropa|
|                    |Západní Evropa|
|                    |Indie – jih|
|                    |Jihovýchodní Asie|



## <a name="scaling"></a>Škálování
Sdílené Galerie Imagí můžete zadat počet replik pro obrázky, které chcete do Azure. Tato funkce umožňuje scénáře nasazení více virtuálních počítačů při nasazení virtuálního počítače je možné rozdělit do různých replik snižuje pravděpodobnost procesu vytváření instance omezený přetížení jen jednu repliku.

![Obrázek znázorňující, jak můžete škálovat imagí](./media/shared-image-galleries/scaling.png)


## <a name="replication"></a>Replikace
Sdílené Galerie obrázků vám také umožní automaticky replikovat vaši Image do jiných oblastí Azure. Každá verze sdílené bitové kopie je možné replikovat do různých oblastí v závislosti na tom, co dává smysl pro vaši organizaci. Jedním z příkladů je vždy replikovat nejnovější image v několika oblastech, všechny starší verze jsou k dispozici pouze v 1 oblast. To může pomoct ušetřit na náklady na úložiště pro sdílený verze image. Je možné aktualizovat, kterou verzi image Shared je replikovat do oblastí po času vytvoření. Čas potřebný k replikaci do různých oblastí závisí na množství dat, kopírování a počtem oblastí, které verze je replikovat do. V některých případech to může trvat několik hodin. Replikace se děje, ale když zobrazíte stav replikace v jedné oblasti. Po dokončení replikace image je v oblasti, pak můžete nasadit virtuální počítač nebo VMSS použití dané verze v oblasti.

![Obrázek znázorňující, jak se dají replikovat imagí](./media/shared-image-galleries/replication.png)


## <a name="access"></a>Access
Jako galerie sdílené bitové kopie sdílené bitové kopie a sdílené bitové kopie verze jsou všechny prostředky, je může sdílet pomocí integrovaného Azure RBAC nativní ovládací prvky. Pomocí RBAC můžete sdílet tyto prostředky pro další uživatele, objektů služby a skupiny ve vaší organizaci. Rozsah sdílení těchto prostředků je ve stejném tenantovi AD. Jakmile uživatel má přístup k verzi sdílené bitové kopie, můžou třeba nasadit virtuální počítač nebo škálovací sady v některém z předplatných, ke kterým mají přístup v rámci stejné AD tenanta jako Shared verze image virtuálních počítačů.  Tady je sdílení, která pomáhá pochopit, co uživatel získá přístup k matici:

| Sdílené s uživatelem     | Sdílená galerie obrázků | Sdílené bitové kopie | Verze sdílené bitové kopie |
|----------------------|----------------------|--------------|----------------------|
| Sdílená galerie obrázků | Ano                  | Ano          | Ano                  |
| Sdílené bitové kopie         | Ne                   | Ano          | Ano                  |
| Verze sdílené bitové kopie | Ne                   | Ne           | Ano                  |



## <a name="billing"></a>Fakturace
Neexistuje žádné další poplatky za využívání služby Shared Galerie obrázků. Účtuje se pro následující prostředky:
- Náklady na úložiště ukládání verzí sdílených bitových kopií. To závisí na počtu replik verze a počtem oblastí verze se replikuje do.
- Poplatky za výchozí přenos dat sítě pro replikaci z zdrojové oblasti verze do replikovaných oblastech.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy 

**Otázka:** Jak se mám zaregistrovat sdílené Image Galerie verze Public Preview?
 
 A. Pokud se chcete zaregistrovat ve galerii Imagí sdílené veřejné verzi preview, budete muset zaregistrovat pro funkci spuštěním následujících příkazů z každého předplatného, ve kterých máte v úmyslu vytvořit Galerie sdílené bitové kopie, definici image nebo image verze prostředků, a také pokud máte v úmyslu nasadit virtuální počítače pomocí verze image.

**ROZHRANÍ PŘÍKAZOVÉHO ŘÁDKU**: 

```bash 
az feature register --namespace Microsoft.Compute --name GalleryPreview
az provider register -n Microsoft.Compute
```

**Prostředí PowerShell**: 

```powershell
Register-AzureRmProviderFeature -FeatureName GalleryPreview -ProviderNamespace Microsoft.Compute
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

**Otázka:** Jak vytvořit seznam všech prostředků Galerie obrázků sdílené napříč předplatnými? 
 
 A. Pokud chcete vypsat všechny prostředky Galerie obrázků sdílené napříč předplatnými, že máte přístup k webu Azure portal, postupujte podle následujících kroků:

 1. Otevřete web [Azure Portal](https://portal.azure.com).
 1. Přejděte na **všechny prostředky**.
 1. Vyberte všechny odběry, za kterých byste chtěli vypíše všechny prostředky.
 1. Vyhledejte prostředky typu **privátní Galerie**.
 
 Definice image a image verze zobrazíte musí také vybrat **zobrazit skryté typy**.
 
 Seznam všech prostředků Galerie sdílené bitové kopie napříč předplatnými, ke kterým máte oprávnění, použijte následující příkaz v rozhraní příkazového řádku Azure:

 ```bash
 az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
 ```


**Otázka:** Jak se dá sdílet Moje Image napříč předplatnými?
 
 A. Image můžete sdílet napříč předplatnými pomocí na základě řízení přístupu Role (RBAC). Každý uživatel, který má oprávnění ke čtení na verzi image, dokonce i mezi předplatnými, budou moct nasadit virtuální počítač pomocí image verze.


**Otázka:** Můžete přesunout svůj stávající obrázek v galerii sdílené bitové kopie
 
 A. Ano. Existují 3 scénáře na základě typů imagí, které máte uzavřeny.

 Scénář 1: Pokud máte spravované image, pak je můžete vytvořit definici image a verze image z něj.

 Scénář 2: Pokud máte nespravované generalizované image, můžete vytvoření spravované image z něj a pak vytvořte definici image a image verze z něj. 

 Scénář 3: Pokud máte virtuální pevný disk ve vašem místním systému souborů, musíte nahrát virtuální pevný disk, vytvořte spravovanou image, můžete vytvořit a obrazu definice a verze image z něj. 
    - Pokud virtuální pevný disk z virtuálního počítače s Windows, přečtěte si téma [nahrání generalizovaného virtuálního pevného disku](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
    - Pokud virtuální pevný disk pro virtuální počítač s Linuxem, přečtěte si téma [nahrání virtuálního pevného disku](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)


**Otázka:** Můžete vytvořit image verze ze specializovaného disku?

 A. Ne, nepodporujeme aktuálně specializované disky jako Image. Pokud budete mít speciální disk, budete muset [vytvoření virtuálního počítače z virtuálního pevného disku](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal#create-a-vm-from-a-disk) připojením specializeddisk k novému virtuálnímu počítači. Jakmile budete mít spuštěný virtuální počítač, budete muset postupovat podle pokynů a vytvořte spravovanou image z [virtuálního počítače Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-custom-images) nebo [virtuálního počítače s Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Až budete mít generalizované image spravovaného, můžete zahájit proces vytvořit popis sdílené bitové kopie a verze image.


**Otázka:** Můžete vytvořit sdílené bitové kopie galerie, definici image a verze image na webu Azure portal?

 A. Ne, aktuálně nepodporujeme vytváření některý z Galerie Imagí sdílených prostředků prostřednictvím webu Azure portal. Nicméně podporujeme vytvoření Galerie obrázků sdílené prostředky přes rozhraní příkazového řádku, šablony a sady SDK. Také verze prostředí PowerShell bude brzy.

 
**Otázka:** Po vytvoření můžete aktualizovat definici image nebo verze image? Jaký druh podrobnosti můžete změnit?

 A. Níže jsou uvedené podrobnosti, které mohou být aktualizovány na všech prostředcích:
 
Galerie sdílené bitové kopie:
- Popis

definice bitové kopie:
- Doporučené virtuálních procesorů
- Memory (Paměť)
- Popis
- Životnost datum ukončení

Verze Image:
- Počet replik místní
- Cílové oblasti
- Vyloučení z nejnovější
- Životnost datum ukončení


**Otázka:** Po vytvoření můžu přesunout Galerie obrázků sdíleného prostředku do jiného předplatného?

 A. Ne, nelze přesunout prostředek Galerie sdílené bitové kopie do jiného předplatného. Nicméně je možné replikovat do jiných oblastí podle potřeby verze image v galerii.

**Otázka:** Můžete replikovat Moje image verzí napříč cloudy – Azure China 21Vianet, Azure Germany a cloudu Azure Government? 

 A. Verze image Ne, nedokáže replikovat napříč cloudy.

**Otázka:** Můžete replikovat Moje image verzí napříč předplatnými? 

 A. Ne, můžete replikovat verze image mezi oblastmi v rámci předplatného a používat v jiných předplatných pomocí RBAC.

**Otázka:** Můžete sdílet verze image mezi klienty AD? 

 A. Ne, galerie aktuálně sdílené bitové kopie nepodporuje sdílení verze image klientů AD. Ale může použít funkci soukromé nabídky na webu Azure Marketplace k dosažení tohoto cíle.


**Otázka:** Jak dlouho trvá replikovat verze bitové kopie do cílové oblasti?

 A. Doba replikace verze image je zcela závisí na velikosti image a počtem oblastí, které je právě replikován pro. Jako osvědčený postup, je však doporučeno, zachovat malý obrázek a zavřete zdrojovou a cílovou oblastí pro dosažení co nejlepších výsledků. Můžete zkontrolovat stav replikace použitím příznaku - stav replikace.


**Otázka:** Kolik sdílených image galerie můžete vytvořit v jednom předplatném?

 A. Je výchozí kvóta 
- 10 sdílených image Galerie na předplatné a oblast
- 200 definice bitové kopie, na předplatné a oblast
- verze image 2000, na předplatné a oblast


**Otázka:** Jaký je rozdíl mezi oblast zdrojové a cílové oblasti?

 A. Zdrojová oblast je oblast, ve kterém se vytvoří image verze a cílové oblasti jsou oblasti, ve kterých se uloží kopie vašich verze image. Pro každou verzi image můžete mít jenom jeden zdrojové oblasti. Také ujistěte se, že když vytvoříte image verze předat oblast umístění zdroje jako jeden z cílových oblastí.  


**Otázka:** Jak určit zdrojové oblasti při vytváření verze image?

 A. Při vytváření image verze, můžete použít **– umístění** značek v rozhraní příkazového řádku a **– umístění** značku v prostředí PowerShell k určení zdrojové oblasti. Ujistěte se prosím, že spravované image, kterou používáte jako základní image k vytvoření verze image je ve stejném umístění jako umístění, ve kterém chcete vytvořit verzi image. Také ujistěte se, že když vytvoříte image verze předat oblast umístění zdroje jako jeden z cílových oblastí.  


**Otázka:** Jak určit počet replik verze image má být vytvořen v jednotlivých oblastech?

 A. Existují dva způsoby, jak můžete zadat počet replik verze image má být vytvořen v jednotlivých oblastech:
 
1. Počet místní repliku, která určuje počet replik, budete chtít vytvořit v jedné oblasti. 
2. Běžné počet replik, což je výchozí za počet oblastí v případě, že není zadaný počet místních replik. 

Chcete-li určit počet místních replik, předat umístění spolu s počtem replik, kterou chcete vytvořit v dané oblasti takto: "USA (střed) – jih = 2". 

Pokud počet místní replika není zadán s každé umístění, bude výchozí počet replik běžné počet replik, které jste zadali. 

Pokud chcete nastavit společné počet replik v rozhraní příkazového řádku, použijte **– počet replik** argument v `az sig image-version create` příkazu.


**Otázka:** Můžete vytvořit sdílené bitové kopie Galerie v jiném umístění než ten, ve kterém chcete vytvořit definici image a image verze?

 A. Ano, je to možné. Ale jako osvědčený postup doporučujeme ponechat skupinu prostředků, galerie sdílené bitové kopie, definici image a image verze ve stejném umístění.


**Otázka:** Co jsou poplatky za používání galerie obrázků Shared?

 A. Neúčtují žádné poplatky za používání služby Shared Galerie obrázků, s výjimkou poplatky za úložiště pro ukládání verze image a poplatky za odchozí přenos sítě pro replikaci verze image ze zdrojové oblasti do cílové oblasti.

**Otázka:** Jaké verze rozhraní API použijte k vytvoření Galerie obrázků sdílené, definici Image, Image verze a VM/VMSS mimo verzi Image?

 A. Pro virtuální počítač a virtuální počítače škálovací sady nasazení používá verzi image, doporučujeme použít rozhraní API verze 2018-04-01 nebo vyšší. Pro práci s galerií sdílené bitové kopie, definice image a image verze, doporučujeme že použít rozhraní API verze 2018-06-01. 