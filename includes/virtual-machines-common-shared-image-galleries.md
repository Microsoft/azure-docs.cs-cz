---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh
ms.custom: include file
ms.openlocfilehash: a477114bda7d138a6860d21f2fad75e27d968833
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80117120"
---
Galerie sdílených imagí je služba, která pomáhá sestavovat strukturu a organizaci kolem spravovaných imagí. Galerie sdílených imagí poskytují:

- Spravovaná globální replikace imagí.
- Správa verzí a seskupování imagí pro snadnější správu.
- Vysoce dostupné obrázky s účty zóny redundantního úložiště (ZRS) v oblastech, které podporují Zóny dostupnosti. ZRS nabízí lepší odolnost proti chybám v rámci oblast.
- Sdílení mezi předplatnými a dokonce i mezi klienty služby Active Directory (AD), a to pomocí RBAC.
- Škálování nasazení pomocí replik imagí v jednotlivých oblastech.

Pomocí Galerie sdílených imagí můžete své image sdílet s různými uživateli, instančními objekty nebo skupinami služby AD v rámci vaší organizace. Sdílené Image je možné replikovat do několika oblastí, pro rychlejší škálování vašich nasazení.

Spravovaná Image je kopie celého virtuálního počítače (včetně všech připojených datových disků) nebo jenom disku s operačním systémem v závislosti na tom, jak bitovou kopii vytvořit. Při vytváření virtuálního počítače z image se k vytvoření disků pro nový virtuální počítač použije kopie VHD v imagi. Spravovaná bitová kopie zůstává v úložišti a je možné ji znovu použít a znovu vytvořit nové virtuální počítače.

Pokud máte velký počet spravovaných imagí, které potřebujete udržovat a chtějí je zpřístupnit v celé firmě, můžete použít galerii sdílených imagí jako úložiště, které usnadňuje sdílení imagí. 

Funkce Galerie sdílených imagí má více typů prostředků:

| Prostředek | Popis|
|----------|------------|
| **Spravovaná image** | Základní image, která se dá použít samostatně nebo použít k vytvoření **verze image** v galerii imagí. Spravované image se vytvářejí z [zobecněných](#generalized-and-specialized-images) virtuálních počítačů. Spravovaná bitová kopie je speciální typ VHD, který se dá použít k vytvoření více virtuálních počítačů a dá se teď použít k vytváření verzí sdílených imagí. |
| **Snímek** | Kopie VHD, která se dá použít k vytvoření **Image verze** Snímky se dají považovat ze [specializovaného](#generalized-and-specialized-images) virtuálního počítače (který se nezobecněný) pak použít samostatně, nebo se snímky datových disků, aby se vytvořila specializovaná verze image.
| **Galerie imagí** | Podobně jako u Azure Marketplace je **Galerie imagí** úložiště pro správu a sdílení imagí, ale Vy řídíte, kdo má přístup. |
| **Definice obrázku** | Image jsou definované v rámci Galerie a obsahují informace o imagi a požadavcích na jejich použití v rámci vaší organizace. Můžete zahrnout informace, jako je například to, zda je obrázek zobecněný nebo specializovaný, operační systém, minimální a maximální požadavky na paměť a poznámky k verzi. Je definicí typu obrázku. |
| **Verze image** | **Verze image** je to, co použijete k vytvoření virtuálního počítače při použití galerie. V případě potřeby můžete mít v prostředí k dispozici více verzí bitové kopie. Podobně jako u spravované image při použití **verze image** k vytvoření virtuálního počítače se verze image používá k vytvoření nových disků pro virtuální počítač. Verze bitové kopie lze použít několikrát. |

<br>

![Obrázek znázorňující, jak můžete mít v galerii více verzí obrazu](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Definice obrázků

Definice obrázků jsou logické seskupení pro verze image. Definice image obsahuje informace o tom, proč se image vytvořila, jaký operační systém je k dispozici, a informace o použití bitové kopie. Definice obrázku je jako plán pro všechny podrobnosti o vytváření konkrétní image. Virtuální počítač nebudete nasazovat z definice image, ale z verze image vytvořené z definice.

Existují tři parametry pro každou definici obrázku, které jsou používány v kombinaci – **Vydavatel**, **Nabídka** a **SKU**. Slouží k vyhledání konkrétní definice obrázku. Můžete mít verze bitové kopie, které sdílejí jednu nebo dvě, ale ne všechny tři hodnoty.  Tady jsou například tři definice obrázků a jejich hodnoty:

|Definice image|Vydavatel|Nabídka|Skladová jednotka (SKU)|
|---|---|---|---|
|myImage1|Contoso|Finance|Back-end|
|myImage2|Contoso|Finance|Front-end|
|myImage3|Testování|Finance|Front-end|

Všechny tři z nich mají jedinečné sady hodnot. Formát je podobný jako při současném zadání vydavatele, nabídky a SKU pro [Azure Marketplace imagí](../articles/virtual-machines/windows/cli-ps-findimage.md) v Azure PowerShell získat nejnovější verzi image na webu Marketplace. Každá definice obrázku musí mít jedinečnou sadu těchto hodnot.

Níže jsou uvedené další parametry, které je možné nastavit v definici image, abyste mohli snadněji sledovat své prostředky:

* Stav operačního systému – stav operačního systému můžete nastavit na [generalizované nebo specializované](#generalized-and-specialized-images).
* Operační systém může být buď Windows, nebo Linux.
* Popis – použijte popis k poskytnutí podrobnějších informací o tom, proč existuje definice obrázku. Můžete mít například definici image pro front-end Server, ve kterém je aplikace předem nainstalovaná.
* Smlouva EULA – dá se použít k odkazování na licenční smlouvu s koncovým uživatelem, která je specifická pro definici image.
* Prohlášení o ochraně osobních údajů a poznámky k verzi – můžete ukládat poznámky k verzi a prohlášení o ochraně osobních údajů ve službě Azure Storage a zadat identifikátor URI pro přístup k nim jako součást definice image.
* Datum ukončení životnosti – připojte k definici obrázku datum ukončení životnosti, aby bylo možné pomocí automatizace odstranit staré definice imagí.
* Značka – při vytváření definice obrázku můžete přidat značky. Další informace o značkách najdete v tématu [použití značek k uspořádání prostředků](../articles/azure-resource-manager/management/tag-resources.md) .
* Minimální a maximální doporučení pro vCPU a paměť – Pokud má vaše image doporučení vCPU a paměti, můžete tyto informace připojit k definici image.
* Nepovolené typy disků – můžete zadat informace o požadavcích na úložiště pro váš virtuální počítač. Pokud například bitová kopie není vhodná pro disky se standardním pevným diskem, přidáte je do seznamu zakázat.

## <a name="generalized-and-specialized-images"></a>Generalizované a specializované image

Galerie sdílených imagí podporuje dva stavy operačních systémů. Image obvykle vyžadují, aby byl virtuální počítač použitý k vytvoření image zobecněný předtím, než Image převezme. Generalizace je proces, který z virtuálního počítače odebere informace specifické pro počítač a uživatele. V systému Windows se používá také nástroj Sysprep. Pro Linux můžete použít [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` nebo `-deprovision+user` parametry.

Specializované virtuální počítače neprošly procesem odebrání informací a účtů specifických pro konkrétní počítač. Virtuální počítače vytvořené z specializovaných imagí navíc `osProfile` k nim přidruženy nejsou. To znamená, že speciální obrázky budou mít určitá omezení.

- Účty, které se dají použít k přihlášení k virtuálnímu počítači, se dají použít taky na jakémkoli virtuálním počítači vytvořeném pomocí specializované image, která se vytvoří z tohoto virtuálního počítače.
- Virtuální počítače budou mít **název počítače** , ze kterého se image povedla. Měli byste změnit název počítače, aby se předešlo kolizím.
- `osProfile` Je způsob, jakým se do virtuálního počítače předávají nějaké citlivé `secrets`informace pomocí. To může způsobovat problémy s využitím trezoru klíčů, WinRM a dalších `secrets` funkcí, `osProfile`které používají. V některých případech můžete tato omezení obejít pomocí identit spravované služby (MSI).

> [!IMPORTANT]
> Specializované obrázky jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Známá omezení verze Preview** Virtuální počítače se dají vytvářet jenom z specializovaných imagí pomocí portálu nebo rozhraní API. Pro verzi Preview není podporována podpora rozhraní příkazového řádku nebo PowerShellu.


## <a name="regional-support"></a>Místní podpora

Zdrojové oblasti jsou uvedené v následující tabulce. Všechny veřejné oblasti můžou být cílové oblasti, ale pokud je chcete replikovat do Austrálie Central a Austrálie – střed 2, musíte mít své předplatné na seznamu povolených. Pokud chcete požádat o seznam povolených, navštivte:https://azure.microsoft.com/global-infrastructure/australia/contact/


| Zdrojové oblasti        |                   |                    |                    |
| --------------------- | ----------------- | ------------------ | ------------------ |
| Austrálie – střed     | Čína – východ        | Indie – jih        | Západní Evropa        |
| Austrálie – střed 2   | Čína – východ 2      | Jihovýchodní Asie     | Spojené království – jih           |
| Austrálie – východ        | Čína – sever       | Japonsko – východ         | Spojené království – západ            |
| Austrálie – jihovýchod   | Čína – sever 2     | Japonsko – západ         | US DoD – střed     |
| Brazílie – jih          | Východní Asie         | Jižní Korea – střed      | US DoD – východ        |
| Střední Kanada        | USA – východ           | Jižní Korea – jih        | USA (Gov) – Arizona     |
| Kanada – východ           | USA – východ 2         | USA – středosever   | USA (Gov) – Texas       |
| Indie – střed         | Východní USA 2 EUAP    | Severní Evropa       | USA (Gov) – Virginia    |
| USA – střed            | Francie – střed    | USA – středojih   | Indie – západ         |
| Střed USA EUAP       | Francie – jih      | USA – středozápad    | USA – západ            |
|                       |                   |                    | USA – západ 2          |



## <a name="limits"></a>Omezení 

Pro nasazení prostředků pomocí galerií sdílených imagí existují omezení pro každý odběr:
- Galerie sdílených imagí 100 na jedno předplatné, podle jednotlivých oblastí
- 1 000 definice imagí pro každé předplatné v jednotlivých oblastech
- verze image 10 000 na jedno předplatné v jednotlivých oblastech
- Všechny disky připojené k imagi musí být menší nebo rovny 1 TB.

Další informace najdete v tématu o tom, jak kontrolovat [využití prostředků proti omezením](https://docs.microsoft.com/azure/networking/check-usage-against-limits) , v příkladech, jak kontrolovat aktuální využití.
 
## <a name="scaling"></a>Škálování
Galerie sdílených imagí umožňuje zadat počet replik, které má Azure uchovávat pro image. To pomáhá scénářům nasazení ve více virtuálních počítačích, protože nasazení virtuálních počítačů je možné rozložit do různých replik, které omezují nutnost zpracování vytváření instancí z důvodu přetížení jedné repliky.

Pomocí Galerie sdílených imagí teď můžete nasadit až 1 000 instancí virtuálních počítačů v rámci sady škálování virtuálních počítačů (od 600 do spravovaných imagí). Repliky imagí poskytují lepší výkon, spolehlivost a konzistenci nasazení. V každé cílové oblasti můžete nastavit jiný počet replik, a to na základě rozsahu potřeb pro oblast. Vzhledem k tomu, že každá replika je hluboká kopie vaší image, pomáhá škálovat vaše nasazení lineárně pomocí každé další repliky. I když nerozumíme, že žádné dva obrázky nebo oblasti jsou stejné, tady je naše obecné pokyny k používání replik v oblasti:

- U nasazení VMSS (Virtual Machine Scale set) – pro každé 20 virtuálních počítačů, které vytvoříte souběžně, doporučujeme zachovat jednu repliku. Pokud například vytváříte virtuální počítače 120 souběžně pomocí stejné image v oblasti, doporučujeme, abyste zachovali aspoň 6 replik vaší image. 
- Pro nasazení VMSS (Virtual Machine Scale set) – pro každé nasazení sady škálování s až 600 instancemi Doporučujeme zachovat aspoň jednu repliku. Pokud například vytváříte 5 sad škálování souběžně, každý s 600 instancemi virtuálních počítačů pomocí stejné image v jedné oblasti, doporučujeme, abyste zachovali aspoň 5 replik vaší image. 

Vždycky doporučujeme, abyste převedli počet replik z důvodu faktorů, jako je velikost obrázku, obsah a typ operačního systému.

![Obrázek znázorňující, jak můžete škálovat obrázky](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Zajištění vysoké dostupnosti imagí

[Azure Zone redundantní úložiště (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) zajišťuje odolnost proti selhání zóny dostupnosti v oblasti. Díky obecné dostupnosti Galerie sdílených imagí si můžete vybrat ukládání imagí v účtech ZRS v oblastech s Zóny dostupnosti. 

Můžete také zvolit typ účtu pro každou cílovou oblast. Výchozí typ účtu úložiště je Standard_LRS, ale můžete zvolit Standard_ZRS pro oblasti s Zóny dostupnosti. [Tady se můžete](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)podívat na oblast dostupnosti ZRS.

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
| Definice image     | Ne                   | Ano          | Ano                  |

Pro nejlepší prostředí doporučujeme sdílení na úrovni galerie. Nedoporučujeme sdílet jednotlivé verze imagí. Další informace o RBAC najdete v tématu [Správa přístupu k prostředkům Azure pomocí RBAC](../articles/role-based-access-control/role-assignments-portal.md).

Image je také možné sdílet, ve velkém měřítku, a to i v rámci klientů pomocí registrace aplikace s více klienty. Další informace o sdílení imagí napříč klienty najdete v tématu [sdílení imagí virtuálních počítačů galerie v rámci tenantů Azure](../articles/virtual-machines/linux/share-images-across-tenants.md).

## <a name="billing"></a>Fakturace
Za používání služby Galerie sdílených imagí se neúčtují žádné další poplatky. Budou se vám účtovat tyto prostředky:
- Náklady na úložiště pro ukládání verzí sdílených imagí Náklady závisí na počtu replik verze image a na počtu oblastí, na které se má verze replikovat. Pokud máte například 2 bitové kopie a obě jsou replikovány do 3 oblastí, bude vám účtováno 6 spravovaných disků na základě jejich velikosti. Další informace najdete v tématu [Managed disks ceny](https://azure.microsoft.com/pricing/details/managed-disks/).
- Poplatky za síťové přenosy za replikaci první verze image ze zdrojové oblasti do replikovaných oblastí. Další repliky se zpracovávají v rámci této oblasti, takže se neúčtují žádné další poplatky. 

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

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/@azure/arm-compute)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Přejít](https://docs.microsoft.com/azure/go/)

## <a name="templates"></a>Šablony

Prostředek Galerie sdílených imagí můžete vytvořit pomocí šablon. K dispozici je několik šablon rychlého startu Azure: 

- [Vytvoření galerie sdílených imagí](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Vytvoření definice obrázku v galerii sdílených imagí](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Vytvoření verze image v galerii sdílených imagí](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
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

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Jak můžu zobrazit seznam všech prostředků Galerie sdílených imagí v rámci předplatných?

Pokud chcete zobrazit seznam všech prostředků Galerie sdílených imagí v rámci předplatných, ke kterým máte přístup v Azure Portal, postupujte podle následujících kroků:

1. Otevřete [Azure Portal](https://portal.azure.com).
1. Přejít na **všechny prostředky**.
1. Vyberte všechna předplatná, pod kterými chcete zobrazit seznam všech prostředků.
1. Vyhledejte prostředky typu **privátní Galerie**.
 
   Chcete-li zobrazit definice obrázků a verze imagí, měli byste také vybrat možnost **Zobrazit skryté typy**.
 
   Pokud chcete zobrazit seznam všech prostředků Galerie sdílených imagí v rámci předplatných, ke kterým máte oprávnění, použijte následující příkaz v rozhraní příkazového řádku Azure CLI:

   ```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>Můžu existující image přesunout do galerie sdílených imagí?
 
Ano. Existují tři scénáře založené na typech imagí, které máte pravděpodobně k dispozici.

 Scénář 1: Pokud máte spravovanou image ve stejném předplatném jako váš podpis, můžete z ní vytvořit definici image a image.

 Scénář 2: Pokud máte nespravovanou bitovou kopii ve stejném předplatném jako váš SIG, můžete z ní vytvořit spravovanou image a pak z ní vytvořit definici image a image. 

 Scénář 3: Pokud máte v místním systému souborů virtuální pevný disk, budete muset virtuální pevný disk nahrát do spravované image a pak z něj můžete vytvořit definici image a verzi image.

- Pokud virtuální pevný disk má virtuální počítač s Windows, přečtěte si téma [nahrání virtuálního pevného disku](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Pokud je virtuální pevný disk pro virtuální počítač se systémem Linux, přečtěte si téma [nahrání VHD](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>Můžu vytvořit verzi image z specializovaného disku?

Ano, podpora specializovaných disků jako imagí je ve verzi Preview. Virtuální počítač můžete vytvořit jenom z specializované Image pomocí portálu ([Windows](../articles/virtual-machines/linux/shared-images-portal.md) nebo [Linux](../articles/virtual-machines/linux/shared-images-portal.md)) a rozhraní API. Pro verzi Preview neexistuje žádná podpora prostředí PowerShell.

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Můžu po vytvoření přesunout prostředek Galerie sdílených imagí do jiného předplatného?

Ne, prostředek Galerie sdílených imagí nemůžete přesunout do jiného předplatného. Verze image v galerii ale budete moct replikovat do jiných oblastí podle potřeby.

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Můžu replikovat verze imagí napříč cloudy, jako je Azure Čína 21Vianet nebo Azure Německo nebo cloud Azure Government?

Ne, verze imagí nelze replikovat napříč cloudy.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>Můžu replikovat verze imagí v rámci předplatných?

Ne, v rámci předplatného můžete replikovat verze imagí do různých oblastí a použít je v jiných předplatných prostřednictvím RBAC.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Můžu sdílet verze imagí napříč klienty Azure AD? 

Ano, můžete použít RBAC ke sdílení jednotlivců napříč klienty. Pokud ale chcete sdílet se škálováním, přečtěte si téma "sdílení imagí Galerie mezi klienty Azure" pomocí [PowerShellu](../articles/virtual-machines/windows/share-images-across-tenants.md) nebo rozhraní příkazového [řádku](../articles/virtual-machines/linux/share-images-across-tenants.md).

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

Pokud chcete v rozhraní `az sig image-version create` příkazového řádku určit společný počet replik, použijte v příkazu argument **--Replica-Count** .

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>Je možné galerii sdílených imagí vytvořit v jiném umístění než u definice image a verze image?

Ano, je to možné. Jako osvědčený postup doporučujeme, abyste zachovali skupinu prostředků, galerii sdílených imagí, definici image a verzi image ve stejném umístění.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Jaké jsou poplatky za používání Galerie sdílených imagí?

Za použití služby Galerie sdílených imagí se neúčtují žádné poplatky, s výjimkou poplatků za úložiště pro ukládání verzí imagí a poplatků za síťové přenosy pro replikaci verzí imagí ze zdrojové oblasti do cílových oblastí.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Jakou verzi rozhraní API mám použít k vytvoření sdílené image a její definice a verze image?

Pro práci s galeriemi sdílených imagí, definicemi obrázků a verzemi imagí doporučujeme použít rozhraní API verze 2018-06-01. Redundantní úložiště zóny (ZRS) vyžaduje verzi 2019-03-01 nebo novější.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Jakou verzi rozhraní API mám použít k vytvoření sdíleného virtuálního počítače nebo sady škálování virtuálního počítače z verze image?

Pro nasazení virtuálních počítačů a virtuálních počítačů pomocí verze image doporučujeme použít rozhraní API verze 2018-04-01 nebo vyšší.
