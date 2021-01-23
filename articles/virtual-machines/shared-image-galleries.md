---
title: Sdílení imagí virtuálních počítačů se sdílenými galeriemi
description: Naučte se používat Galerie sdílených imagí ke sdílení imagí virtuálních počítačů se systémem Linux napříč vaší organizací.
author: axayjo
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 10/14/2020
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 3022e9c694d70359a90e71ecd1232e9274f92f10
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730318"
---
# <a name="shared-image-galleries-overview"></a>Přehled Galerie sdílených imagí

Shared Image Gallery je služba, která pomáhá vytvořit strukturu imagí a zajistit jejich organizaci. Galerie sdílených imagí poskytují:

- Globální replikace imagí.
- Správa verzí a seskupování imagí pro snadnější správu.
- Vysoce dostupné obrázky s účty zóny redundantního úložiště (ZRS) v oblastech, které podporují Zóny dostupnosti. ZRS nabízí lepší odolnost proti chybám v rámci oblast.
- Podpora úložiště úrovně Premium (Premium_LRS).
- Sdílení mezi předplatnými a dokonce i mezi klienty služby Active Directory (AD), a to pomocí RBAC.
- Škálování nasazení pomocí replik imagí v jednotlivých oblastech.

Pomocí Galerie sdílených imagí můžete své image sdílet s různými uživateli, instančními objekty nebo skupinami služby AD v rámci vaší organizace. Sdílené Image je možné replikovat do několika oblastí, pro rychlejší škálování vašich nasazení.

Image je kopie celého virtuálního počítače (včetně všech připojených datových disků) nebo jenom disku s operačním systémem v závislosti na tom, jak je vytvořená. Při vytváření virtuálního počítače z image se k vytvoření disků pro nový virtuální počítač použije kopie VHD v imagi. Bitová kopie zůstává v úložišti a je možné ji znovu použít a znovu vytvořit nové virtuální počítače.

Pokud máte velké množství imagí, které potřebujete zachovat, a chcete je zpřístupnit napříč vaší společností, můžete jako úložiště použít galerii sdílených imagí. 

Funkce Galerie sdílených imagí má více typů prostředků:

| Prostředek | Popis|
|----------|------------|
| **Zdroj obrázku** | Toto je prostředek, který se dá použít k vytvoření **verze image** v galerii imagí. Zdrojem imagí může být existující virtuální počítač Azure, který je [zobecněný nebo specializovaný](#generalized-and-specialized-images), spravovaná image, snímek, virtuální pevný disk nebo verze image v jiné galerii imagí. |
| **Galerie imagí** | Podobně jako u Azure Marketplace je **Galerie imagí** úložiště pro správu a sdílení imagí, ale Vy řídíte, kdo má přístup. |
| **Definice obrázku** | Definice imagí se vytvářejí v rámci Galerie a obsahují informace o imagi a požadavcích na jejich interní používání. To zahrnuje, zda se jedná o obrázek Windows nebo Linux, poznámky k verzi a minimální a maximální požadavky na paměť. Je definicí typu obrázku. |
| **Verze image** | **Verze image** je to, co použijete k vytvoření virtuálního počítače při použití galerie. V případě potřeby můžete mít v prostředí k dispozici více verzí bitové kopie. Podobně jako u spravované image při použití **verze image** k vytvoření virtuálního počítače se verze image používá k vytvoření nových disků pro virtuální počítač. Verze bitové kopie lze použít několikrát. |

<br>

![Obrázek znázorňující, jak můžete mít v galerii více verzí obrazu](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Definice obrázků

Definice obrázků jsou logické seskupení pro verze image. Definice image obsahuje informace o tom, proč se image vytvořila, jaký operační systém je k dispozici, a další informace o použití image. Definice obrázku je jako plán pro všechny podrobnosti o vytváření konkrétní image. Virtuální počítač nebudete nasazovat z definice image, ale z verzí imagí vytvořených z definice.

Existují tři parametry pro každou definici obrázku, které jsou používány v kombinaci – **Vydavatel**, **Nabídka** a **SKU**. Slouží k vyhledání konkrétní definice obrázku. Můžete mít verze bitové kopie, které sdílejí jednu nebo dvě, ale ne všechny tři hodnoty.  Tady jsou například tři definice obrázků a jejich hodnoty:

|Definice image|Publisher|Nabídka|Skladová jednotka (SKU)|
|---|---|---|---|
|myImage1|Contoso|Finance|Back-end|
|myImage2|Contoso|Finance|Front-end|
|myImage3|Testování|Finance|Front-end|

Všechny tři z nich mají jedinečné sady hodnot. Formát je podobný jako při současném zadání vydavatele, nabídky a SKU pro [Azure Marketplace imagí](./windows/cli-ps-findimage.md) v Azure PowerShell získat nejnovější verzi image na webu Marketplace. Každá definice obrázku musí mít jedinečnou sadu těchto hodnot.

Následující parametry určují, které typy verzí imagí můžou obsahovat:

- Stav operačního systému – stav operačního systému můžete nastavit na [generalizované nebo specializované](#generalized-and-specialized-images). Toto pole je vyžadováno.
- Operační systém může být buď Windows, nebo Linux. Toto pole je vyžadováno.
-   Technologie Hyper-V – určete, jestli se image vytvořila z virtuálního pevného disku Hyper-V generace 1 nebo generace [2](generation-2.md) . Výchozí hodnota je 1. generace.


Níže jsou uvedené další parametry, které je možné nastavit v definici image, abyste mohli snadněji sledovat své prostředky:

- Popis – použijte popis k poskytnutí podrobnějších informací o tom, proč existuje definice obrázku. Můžete mít například definici image pro front-end Server, ve kterém je aplikace předem nainstalovaná.
- Smlouva EULA – dá se použít k odkazování na licenční smlouvu s koncovým uživatelem, která je specifická pro definici image.
- Prohlášení o ochraně osobních údajů a poznámky k verzi – můžete ukládat poznámky k verzi a prohlášení o ochraně osobních údajů ve službě Azure Storage a zadat identifikátor URI pro přístup k nim jako součást definice image.
- Datum ukončení životnosti – připojte k definici obrázku datum ukončení životnosti, aby bylo možné pomocí automatizace odstranit staré definice imagí.
- Značka – při vytváření definice obrázku můžete přidat značky. Další informace o značkách najdete v tématu [použití značek k uspořádání prostředků](../azure-resource-manager/management/tag-resources.md) .
- Minimální a maximální doporučení pro vCPU a paměť – Pokud má vaše image doporučení vCPU a paměti, můžete tyto informace připojit k definici image.
- Nepovolené typy disků – můžete zadat informace o požadavcích na úložiště pro váš virtuální počítač. Pokud například bitová kopie není vhodná pro disky se standardním pevným diskem, přidáte je do seznamu zakázat.
- Informace o plánu nákupu pro Image Marketplace – `-PurchasePlanPublisher` , `-PurchasePlanName` a `-PurchasePlanProduct` . Další informace o nákupních plánech najdete v tématu [Vyhledání obrázků v Azure Marketplace](./windows/cli-ps-findimage.md) a [zadání informací o plánu Azure Marketplace nákupu při vytváření obrázků](marketplace-images.md).


## <a name="image-versions"></a>Verze bitové kopie

**Verze image** je to, co použijete k vytvoření virtuálního počítače. V případě potřeby můžete mít v prostředí k dispozici více verzí bitové kopie. Když použijete **verzi image** k vytvoření virtuálního počítače, verze image se použije k vytvoření nových disků pro virtuální počítač. Verze bitové kopie lze použít několikrát.

Vlastnosti verze image jsou:

- Číslo verze Používá se jako název verze image. Je vždycky ve formátu: MajorVersion. podverze. patch. Pokud při vytváření virtuálního počítače určíte, že se má použít **nejnovější** , vybere se na základě nejvyšší MajorVersion a potom oddálení a pak se vybere nejnovější obrázek. 
- Zdrojová. Zdrojem může být virtuální počítač, spravovaný disk, snímek, spravovaná Image nebo jiná verze image. 
- Vylučte z nejnovějších. Verzi můžete zachovat v používání jako nejnovější verzi image. 
- Datum konce životnosti. Datum, po kterém se virtuální počítače z této image nedají vytvořit


## <a name="generalized-and-specialized-images"></a>Generalizované a specializované image

Galerie sdílených imagí podporuje dva stavy operačních systémů. Image obvykle vyžadují, aby byl virtuální počítač použitý k vytvoření image zobecněný předtím, než Image převezme. Generalizace je proces, který z virtuálního počítače odebere informace specifické pro počítač a uživatele. Pro Windows se používá nástroj Sysprep. Pro Linux můžete použít [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` nebo `-deprovision+user` parametry.

Specializované virtuální počítače neprošly procesem odebrání informací a účtů specifických pro konkrétní počítač. Virtuální počítače vytvořené z specializovaných imagí navíc `osProfile` k nim přidruženy nejsou. To znamená, že speciální obrázky budou mít kromě některých výhod nějaká omezení.

- Virtuální počítače a sady škálování vytvořené z specializovaných imagí můžou být spuštěné rychleji. Vzhledem k tomu, že jsou vytvořeny ze zdroje, který již byl při prvním spuštění spuštěn, jsou virtuální počítače vytvořené z těchto imagí rychlejší.
- Účty, které se dají použít k přihlášení k virtuálnímu počítači, se dají použít taky na jakémkoli virtuálním počítači vytvořeném pomocí specializované image, která se vytvoří z tohoto virtuálního počítače.
- Virtuální počítače budou mít **název počítače** , ze kterého se image povedla. Měli byste změnit název počítače, aby se předešlo kolizím.
- `osProfile`Je způsob, jakým se do virtuálního počítače předávají nějaké citlivé informace pomocí `secrets` . To může způsobovat problémy s využitím trezoru klíčů, WinRM a dalších funkcí, které používají `secrets` `osProfile` . V některých případech můžete tato omezení obejít pomocí identit spravované služby (MSI).

## <a name="regional-support"></a>Místní podpora

Všechny veřejné oblasti můžou být cílové oblasti, ale replikovat do Austrálie Central a Austrálie – střed 2 potřebujete, aby bylo vaše předplatné přidané do seznamu povolených. Chcete-li požádat o přidání předplatných do seznamu povolených, použijte následující: https://azure.microsoft.com/global-infrastructure/australia/contact/

## <a name="limits"></a>Omezení 

Pro nasazení prostředků pomocí galerií sdílených imagí existují omezení pro každý odběr:
- Galerie sdílených imagí 100 na jedno předplatné, podle jednotlivých oblastí
- 1 000 definice imagí pro každé předplatné v jednotlivých oblastech
- verze image 10 000 na jedno předplatné v jednotlivých oblastech
- 10 replik verzí imagí na jedno předplatné v jednotlivých oblastech
- Všechny disky připojené k imagi musí být menší nebo rovny 1 TB.

Další informace najdete v tématu o tom, jak kontrolovat [využití prostředků proti omezením](../networking/check-usage-against-limits.md) , v příkladech, jak kontrolovat aktuální využití.
 
## <a name="scaling"></a>Škálování
Galerie sdílených imagí umožňuje zadat počet replik, které má Azure uchovávat pro image. To pomáhá scénářům nasazení ve více virtuálních počítačích, protože nasazení virtuálních počítačů je možné rozložit do různých replik, které omezují nutnost zpracování vytváření instancí z důvodu přetížení jedné repliky.

Pomocí Galerie sdílených imagí teď můžete nasadit až 1 000 instancí virtuálních počítačů v rámci sady škálování virtuálních počítačů (od 600 do spravovaných imagí). Repliky imagí poskytují lepší výkon, spolehlivost a konzistenci nasazení.  V každé cílové oblasti můžete nastavit jiný počet replik, a to na základě rozsahu potřeb pro oblast. Vzhledem k tomu, že každá replika je hluboká kopie vaší image, pomáhá škálovat vaše nasazení lineárně pomocí každé další repliky. I když nerozumíme, že žádné dva obrázky nebo oblasti jsou stejné, tady je naše obecné pokyny k používání replik v oblasti:

- U nasazení s nevirtuálními počítači na úrovni služby – pro každé 20 virtuálních počítačů, které vytvoříte souběžně, doporučujeme, abyste zachovali jednu repliku. Pokud například vytváříte virtuální počítače 120 souběžně pomocí stejné image v oblasti, doporučujeme, abyste zachovali aspoň 6 replik vaší image. 
- Pro nasazení sady škálování virtuálních počítačů – pro každé nasazení sady škálování s až 600 instancemi doporučujeme, abyste zachovali aspoň jednu repliku. Pokud například vytváříte 5 sad škálování souběžně, každý s 600 instancemi virtuálních počítačů pomocí stejné image v jedné oblasti, doporučujeme, abyste zachovali aspoň 5 replik vaší image. 

Vždycky doporučujeme, abyste převedli počet replik z důvodu faktorů, jako je velikost obrázku, obsah a typ operačního systému.

![Obrázek znázorňující, jak můžete škálovat obrázky](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Zajištění vysoké dostupnosti imagí

[Azure Zone redundantní úložiště (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) zajišťuje odolnost proti selhání zóny dostupnosti v oblasti. Díky obecné dostupnosti Galerie sdílených imagí si můžete vybrat ukládání imagí v účtech ZRS v oblastech s Zóny dostupnosti. 

Můžete také zvolit typ účtu pro každou cílovou oblast. Výchozí typ účtu úložiště je Standard_LRS, ale můžete zvolit Standard_ZRS pro oblasti s Zóny dostupnosti. [Tady se můžete](../storage/common/storage-redundancy.md)podívat na oblast dostupnosti ZRS.

![Obrázek znázorňující ZRS](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Replikace
Galerie sdílených imagí také umožňuje automatické replikace imagí do jiných oblastí Azure. Každá verze sdílené bitové kopie se dá replikovat do různých oblastí v závislosti na tom, co je pro vaši organizaci smysl. Jedním z příkladů je vždycky replikovat nejnovější image do více oblastí, zatímco všechny starší verze jsou dostupné jenom v jedné oblasti. To může přispět k úspory nákladů na úložiště pro verze sdílených imagí. 

Oblasti, do kterých se replikuje verze sdíleného obrázku, se dá po vytvoření aktualizovat. Doba potřebná k replikaci do různých oblastí závisí na množství kopírovaných dat a na počtu oblastí, do kterých je verze replikována. V některých případech to může trvat několik hodin. Při replikaci se můžete podívat na stav replikace v jednotlivých oblastech. Po dokončení replikace imagí v oblasti můžete nasadit virtuální počítač nebo sadu škálování s použitím této verze image v oblasti.

![Obrázek znázorňující, jak můžete replikovat image](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Access

Vzhledem k tomu, že je galerie sdílených imagí, definice obrázku a verze image, všechny prostředky, můžou se sdílet pomocí integrovaných nativních ovládacích prvků Azure RBAC. Pomocí RBAC můžete tyto prostředky sdílet ostatním uživatelům, instančním objektům a skupinám. Můžete dokonce sdílet přístup jednotlivcům mimo klienta, kterého vytvořili v rámci. Jakmile má uživatel přístup ke sdílené imagi verze, může nasadit virtuální počítač nebo sadu škálování virtuálního počítače.  Tady je tabulka sdílení, která pomáhá pochopit, k čemu uživatel přistupuje:

| Sdíleno s uživatelem     | Sdílená galerie obrázků | Definice image | Verze image |
|----------------------|----------------------|--------------|----------------------|
| Sdílená galerie obrázků | Ano                  | Ano          | Ano                  |
| Definice image     | No                   | Ano          | Ano                  |

Pro nejlepší prostředí doporučujeme sdílení na úrovni galerie. Nedoporučujeme sdílet jednotlivé verze imagí. Další informace o RBAC najdete v tématu [Správa přístupu k prostředkům Azure pomocí RBAC](../role-based-access-control/role-assignments-portal.md).

Image je také možné sdílet, ve velkém měřítku, a to i v rámci klientů pomocí registrace aplikace s více klienty. Další informace o sdílení imagí napříč klienty najdete v tématu "sdílení imagí virtuálních počítačů galerie v rámci tenantů Azure" pomocí [Azure CLI](./linux/share-images-across-tenants.md) nebo [PowerShellu](./windows/share-images-across-tenants.md).

## <a name="billing"></a>Fakturace
Za používání služby Galerie sdílených imagí se neúčtují žádné další poplatky. Budou se vám účtovat tyto prostředky:
-   Náklady na úložiště při ukládání každé repliky. Náklady na úložiště se účtují jako snímek a vycházejí z obsazené velikosti image, počtu replik verze image a počtu oblastí, do kterých se má verze replikovat. 
-   Poplatky za síťové přenosy za replikaci první verze image ze zdrojové oblasti do replikovaných oblastí. Další repliky se zpracovávají v rámci této oblasti, takže se neúčtují žádné další poplatky. 

Řekněme například, že máte image 127 GB disku s operačním systémem, který zabírá jenom 10 GB úložiště a jeden prázdný datový disk o velikosti 32 GB. Obsazená velikost každého obrázku by měla být jenom 10 GB. Bitová kopie se replikuje do 3 oblastí a každá oblast obsahuje dvě repliky. K dispozici bude šest celkových snímků, z nichž každý využívá 10 GB. Za každý snímek se vám budou účtovat náklady na úložiště na základě obsazené velikosti 10 GB. Za každou z následujících dvou oblastí platíte poplatky za výstup do sítě, které se mají zkopírovat. Další informace o cenách snímků v jednotlivých oblastech najdete v tématu ceny za [spravované disky](https://azure.microsoft.com/pricing/details/managed-disks/). Další informace o odchozím přenosu v síti najdete v tématu [ceny šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="updating-resources"></a>Aktualizace prostředků

Po vytvoření můžete provést některé změny v prostředcích Galerie imagí. Jsou omezeny na:
 
Galerie sdílených imagí:
- Popis

Definice Image:
- Doporučené vCPU
- Doporučená paměť
- Popis
- Datum konce životnosti

Verze Image:
- Počet místních replik
- Cílové oblasti
- Vyloučit z posledního
- Datum konce životnosti

## <a name="sdk-support"></a>Podpora SDK

Následující sady SDK podporují vytváření galerií sdílených imagí:

- [.NET](/dotnet/api/overview/azure/virtualmachines/management)
- [Java](/java/azure/)
- [Node.js](/javascript/api/@azure/arm-compute)
- [Python](/python/api/overview/azure/virtualmachines)
- [Přejít](/azure/go/)

## <a name="templates"></a>Šablony

Prostředek Galerie sdílených imagí můžete vytvořit pomocí šablon. K dispozici je několik šablon rychlého startu Azure: 

- [Vytvoření služby Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Vytvoření definici image v Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Vytvoření verze image v Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Vytvoření virtuálního počítače z verze image](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Nejčastější dotazy 

* [Jak můžu zobrazit seznam všech prostředků Galerie sdílených imagí v rámci předplatných?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [Můžu existující image přesunout do galerie sdílených imagí?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [Můžu vytvořit verzi image z specializovaného disku?](#can-i-create-an-image-version-from-a-specialized-disk)
* [Můžu po vytvoření přesunout prostředek Galerie sdílených imagí do jiného předplatného?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [Můžu replikovat verze imagí napříč cloudy, jako je Azure Čína 21Vianet nebo Azure Německo nebo cloud Azure Government?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [Můžu replikovat verze imagí v rámci předplatných?](#can-i-replicate-my-image-versions-across-subscriptions)
* [Můžu sdílet verze imagí napříč klienty Azure AD?](#can-i-share-image-versions-across-azure-ad-tenants)
* [Jak dlouho trvá replikace verzí imagí napříč cílovými oblastmi?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [Jaký je rozdíl mezi zdrojovou a cílovou oblastí?](#what-is-the-difference-between-source-region-and-target-region)
* [Návody určit zdrojovou oblast při vytváření verze image?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [Návody zadejte počet replik verzí imagí, které se mají v každé oblasti vytvořit?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [Je možné galerii sdílených imagí vytvořit v jiném umístění než u definice image a verze image?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Jaké jsou poplatky za používání Galerie sdílených imagí?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Jakou verzi rozhraní API mám použít k vytvoření sdílené image a její definice a verze image?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [Jakou verzi rozhraní API mám použít k vytvoření sdíleného virtuálního počítače nebo sady škálování virtuálního počítače z verze image?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)
* [Můžu aktualizovat sadu škálování virtuálního počítače vytvořenou pomocí spravované image pro použití imagí Galerie sdílených imagí?](#can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Jak můžu zobrazit seznam všech prostředků Galerie sdílených imagí v rámci předplatných?

Pokud chcete zobrazit seznam všech prostředků Galerie sdílených imagí v rámci předplatných, ke kterým máte přístup v Azure Portal, postupujte podle následujících kroků:

1. Otevřete [Azure Portal](https://portal.azure.com).
1. Posuňte se dolů na stránku a vyberte **všechny prostředky**.
1. Vyberte všechna předplatná, pod kterými chcete zobrazit seznam všech prostředků.
1. Vyhledejte prostředky pro galerii typu **sdílená image**.
  
Pokud chcete zobrazit seznam všech prostředků Galerie sdílených imagí v rámci předplatných, ke kterým máte oprávnění, použijte následující příkaz v rozhraní příkazového řádku Azure CLI:

```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
```

Další informace najdete v tématu **Správa prostředků Galerie** pomocí [Azure CLI](update-image-resources-cli.md) nebo [PowerShellu](update-image-resources-powershell.md).

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>Můžu existující image přesunout do galerie sdílených imagí?
 
Ano. Existují tři scénáře založené na typech imagí, které máte pravděpodobně k dispozici.

 Scénář 1: Pokud máte spravovanou bitovou kopii, můžete z ní vytvořit definici image a její verzi. Další informace najdete v tématu **migrace ze spravované image na verzi image** pomocí [Azure CLI](image-version-managed-image-cli.md) nebo [PowerShellu](image-version-managed-image-powershell.md).

 Scénář 2: Pokud máte nespravovanou bitovou kopii, můžete z ní vytvořit spravovanou image a pak z ní vytvořit definici image a její verzi. 

 Scénář 3: Pokud máte v místním systému souborů virtuální pevný disk, budete muset virtuální pevný disk nahrát do spravované image a pak z něj můžete vytvořit definici image a verzi image.

- Pokud virtuální pevný disk má virtuální počítač s Windows, přečtěte si téma [nahrání virtuálního pevného disku](./windows/upload-generalized-managed.md).
- Pokud je virtuální pevný disk pro virtuální počítač se systémem Linux, přečtěte si téma [nahrání VHD](./linux/upload-vhd.md#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>Můžu vytvořit verzi image z specializovaného disku?

Ano, může vytvořit virtuální počítač z specializované Image pomocí rozhraní příkazového [řádku](vm-specialized-image-version-cli.md), [PowerShellu](vm-specialized-image-version-powershell.md)nebo rozhraní API. 

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Můžu po vytvoření přesunout prostředek Galerie sdílených imagí do jiného předplatného?

Ne, prostředek Galerie sdílených imagí nemůžete přesunout do jiného předplatného. Můžete replikovat verze imagí v galerii do jiných oblastí nebo zkopírovat image z jiné galerie pomocí [Azure CLI](image-version-another-gallery-cli.md) nebo [PowerShellu](image-version-another-gallery-powershell.md).

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Můžu replikovat verze imagí napříč cloudy, jako je Azure Čína 21Vianet nebo Azure Německo nebo cloud Azure Government?

Ne, verze imagí nelze replikovat napříč cloudy.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>Můžu replikovat verze imagí v rámci předplatných?

Ne, v rámci předplatného můžete replikovat verze imagí do různých oblastí a použít je v jiných předplatných prostřednictvím RBAC.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Můžu sdílet verze imagí napříč klienty Azure AD? 

Ano, můžete použít RBAC ke sdílení jednotlivců napříč klienty. Pokud ale chcete sdílet se škálováním, přečtěte si téma "sdílení imagí Galerie mezi klienty Azure" pomocí [PowerShellu](./windows/share-images-across-tenants.md) nebo rozhraní příkazového [řádku](./linux/share-images-across-tenants.md).

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>Jak dlouho trvá replikace verzí imagí napříč cílovými oblastmi?

Doba replikace verze Image je zcela závislá na velikosti bitové kopie a počtu oblastí, na které se replikuje. Osvědčeným postupem je však doporučit, abyste zachovali obrázek malými a zdrojové a cílové oblasti byly blízko nejlepších výsledků. Stav replikace můžete zjistit pomocí příznaku-ReplicationStatus.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>Jaký je rozdíl mezi zdrojovou a cílovou oblastí?

Zdrojová oblast je oblast, ve které se vytvoří vaše verze image, a cílové oblasti jsou oblasti, ve kterých se uloží kopie verze image. Pro každou verzi image můžete mít jenom jednu zdrojovou oblast. Také se ujistěte, že při vytváření verze image předáte umístění zdrojové oblasti jako jednu z cílových oblastí.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>Návody určit zdrojovou oblast při vytváření verze image?

Při vytváření verze image můžete použít značku **--Location** v rozhraní příkazového řádku a značku **-Location** v PowerShellu k určení zdrojové oblasti. Ujistěte se prosím, že spravovaná bitová kopie, kterou používáte jako základní image pro vytvoření verze image, je ve stejném umístění jako umístění, ve kterém chcete vytvořit verzi image. Také se ujistěte, že při vytváření verze image předáte umístění zdrojové oblasti jako jednu z cílových oblastí.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>Návody zadejte počet replik verzí imagí, které se mají v každé oblasti vytvořit?

Existují dva způsoby, jak můžete zadat počet replik verze image, které se mají vytvořit v každé oblasti:
 
1. Počet místních replik, které určují počet replik, které chcete vytvořit pro jednotlivé oblasti. 
2. Běžný počet replik, který je výchozí hodnotou podle počtu oblastí v případě, že počet místních replik není zadaný. 

Chcete-li určit počet místních replik, předejte umístění spolu s počtem replik, které chcete v této oblasti vytvořit: "Střed USA – jih = 2". 

Pokud se pro každé umístění nezadá počet místních replik, bude výchozí počet replik stejný jako společný počet replik, který jste zadali. 

Pokud chcete v rozhraní příkazového řádku určit společný počet replik, použijte v příkazu argument **--Replica-Count** `az sig image-version create` .

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>Je možné galerii sdílených imagí vytvořit v jiném umístění než u definice image a verze image?

Ano, je to možné. Jako osvědčený postup doporučujeme, abyste zachovali skupinu prostředků, galerii sdílených imagí, definici image a verzi image ve stejném umístění.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Jaké jsou poplatky za používání Galerie sdílených imagí?

Za použití služby Galerie sdílených imagí se neúčtují žádné poplatky, s výjimkou poplatků za úložiště pro ukládání verzí imagí a poplatků za síťové přenosy pro replikaci verzí imagí ze zdrojové oblasti do cílových oblastí.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Jakou verzi rozhraní API mám použít k vytvoření sdílené image a její definice a verze image?

Pro práci s galeriemi sdílených imagí, definicemi obrázků a verzemi imagí doporučujeme použít rozhraní API verze 2018-06-01. Redundantní úložiště zóny (ZRS) vyžaduje verzi 2019-03-01 nebo novější.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Jakou verzi rozhraní API mám použít k vytvoření sdíleného virtuálního počítače nebo sady škálování virtuálního počítače z verze image?

Pro nasazení virtuálních počítačů a virtuálních počítačů pomocí verze image doporučujeme použít rozhraní API verze 2018-04-01 nebo vyšší.

### <a name="can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images"></a>Můžu aktualizovat sadu škálování virtuálního počítače vytvořenou pomocí spravované image pro použití imagí Galerie sdílených imagí?

Ano, můžete aktualizovat odkaz na obrázek sady škálování ze spravované image na Image Galerie sdílených imagí, pokud typ operačního systému, generaci technologie Hyper-V a rozložení datových disků odpovídají mezi obrázky.

## <a name="troubleshoot-shared-image-gallery-issues"></a>Řešení potíží s galerií sdílených imagí
Pokud máte problémy s prováděním operací s prostředky v galerii sdílených imagí, Projděte si seznam běžných chyb v [Průvodci odstraňováním potíží](troubleshooting-shared-images.md).

Kromě toho můžete svoji otázku publikovat a označit ji `azure-virtual-machines-images` na adrese [Q&A](/answers/topics/azure-virtual-machines-images.html).

## <a name="next-steps"></a>Další kroky

Naučte se nasazovat sdílené Image pomocí [Azure CLI](shared-images-cli.md) nebo [PowerShellu](shared-images-powershell.md).

