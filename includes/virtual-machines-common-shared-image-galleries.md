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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117120"
---
Galerie sdílených obrázků je služba, která vám pomůže vytvořit strukturu a organizaci kolem spravovaných bitových kopií. Sdílené galerie obrázků poskytují:

- Řízená globální replikace bitových kopií.
- Správa verzí a seskupování obrázků pro snadnější správu.
- Vysoce dostupné image s účty zónového redundantního úložiště (ZRS) v oblastech, které podporují zóny dostupnosti. ZRS nabízí lepší odolnost proti selhání zonální.
- Sdílení mezi předplatnými a dokonce i mezi klienty služby Active Directory (AD) pomocí nástroje RBAC.
- Škálování nasazení s replikami bitové kopie v každé oblasti.

Pomocí Galerie sdílených obrázků můžete sdílet obrázky s různými uživateli, instančními objekty nebo skupinami služby AD v rámci organizace. Sdílené bitové kopie lze replikovat do více oblastí pro rychlejší škálování vašich nasazení.

Spravovaná bitová kopie je kopie úplného virtuálního počítače (včetně všech připojených datových disků) nebo pouze disku operačního systému v závislosti na tom, jak bitovou kopii vytvoříte. Když vytvoříte virtuální hod z image, kopie virtuálních pevných disků v bitové kopii se použije k vytvoření disků pro nový virtuální počítače. Spravovaná bitová kopie zůstane v úložišti a dá se použít znovu a znovu k vytvoření nových virtuálních zařízení.

Pokud máte velký počet spravovaných bitových kopií, které je třeba udržovat, a chcete je zpřístupnit v celé společnosti, můžete použít sdílenou galerii obrázků jako úložiště, které usnadňuje sdílení obrázků. 

Funkce Galerie sdílených obrázků má více typů prostředků:

| Prostředek | Popis|
|----------|------------|
| **Spravovaná bitová kopie** | Základní obrázek, který lze použít samostatně nebo použít k vytvoření **verze obrázku** v galerii obrázků. Spravované bitové kopie se vytvářejí z [generalizovaných](#generalized-and-specialized-images) virtuálních měn. Spravovaná bitová kopie je speciální typ virtuálního pevného disku, který lze použít k vytvoření více virtuálních počítačů a nyní lze použít k vytvoření verzí sdílených bitových obrázků. |
| **Snímek** | Kopie virtuálního pevného disku, který lze použít k vytvoření **verze bitové kopie**. Snímky lze pořizovat ze [specializovaného](#generalized-and-specialized-images) virtuálního počítače (ten, který nebyl zobecněn) a pak použit samostatně nebo se snímky datových disků, k vytvoření verze specializované bitové kopie.
| **Galerie obrázků** | Stejně jako Azure Marketplace, **galerie obrázků** je úložiště pro správu a sdílení bitových kopií, ale máte řízení, kdo má přístup. |
| **Definice obrázku** | Obrázky jsou definovány v galerii a nesou informace o obrázku a požadavky na jeho použití v rámci organizace. Můžete zahrnout informace, jako je zobecnění nebo specializované, operační systém, minimální a maximální požadavky na paměť a poznámky k verzi. Jedná se o definici typu obrazu. |
| **Verze obrázku** | **Verze bitové kopie** je to, co používáte k vytvoření virtuálního počítačů při použití galerie. Podle potřeby pro vaše prostředí můžete mít více verzí bitové kopie. Stejně jako spravovaná **image version** bitová kopie, když k vytvoření virtuálního počítače použijete verzi image k vytvoření nových disků pro virtuální počítače. Verze obrázků lze použít vícekrát. |

<br>

![Obrázek znázorňující, jak můžete mít v galerii více verzí obrázku](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Definice obrázků

Definice obrázků jsou logickým seskupením pro verze obrazu. Definice obrázku obsahuje informace o tom, proč byl obrázek vytvořen, k jakému osu je určen, a informace o jeho používání. Definice obrázku je jako plán pro všechny detaily kolem vytvoření konkrétního obrázku. Virtuální ho nelze nasadit z definice bitové kopie, ale z verze image vytvořené z definice.

Existují tři parametry pro každou definici obrázku, které se používají v kombinaci - **Vydavatel**, **Nabídka** a **Skladová položka**. Ty se používají k nalezení konkrétní definice obrázku. Můžete mít verze bitové kopie, které sdílejí jednu nebo dvě, ale ne všechny tři hodnoty.  Zde jsou například tři definice obrázků a jejich hodnoty:

|Definice image|Vydavatel|Nabídka|Skladová jednotka (SKU)|
|---|---|---|---|
|myImage1|Contoso|Finance|Back-end|
|myImage2|Contoso|Finance|Front-end|
|myImage3|Testování|Finance|Front-end|

Všechny tři z nich mají jedinečné sady hodnot. Formát je podobný, jak můžete aktuálně určit vydavatele, nabídky a skladové položky pro [Azure Marketplace image](../articles/virtual-machines/windows/cli-ps-findimage.md) v Azure PowerShell získat nejnovější verzi image Marketplace. Každá definice obrázku musí mít jedinečnou sadu těchto hodnot.

Následují další parametry, které lze nastavit v definici obrázku, takže můžete snadněji sledovat prostředky:

* Stav operačního systému – stav operačního systému můžete nastavit na [generalizované nebo specializované](#generalized-and-specialized-images).
* Operační systém - může být buď Windows nebo Linux.
* Popis - pomocí popisu uveďte podrobnější informace o tom, proč definice obrázku existuje. Můžete mít například definici bitové kopie pro server front-end, který má předinstalovanou aplikaci.
* Eula - lze použít k odkazna licenční smlouvu koncového uživatele specifickou pro definici obrázku.
* Prohlášení o zásadách ochrany osobních údajů a poznámky k verzi – uložte poznámky k verzi a prohlášení o zásadách ochrany osobních údajů v úložišti Azure a poskytněte identifikátor URI pro přístup k nim jako součást definice image.
* Datum ukončení životnosti – připojte k definici obrázku datum ukončení životnosti, abyste mohli pomocí automatizace odstranit staré definice obrázků.
* Tag - můžete přidat značky při vytváření definice obrázku. Další informace o značkách najdete v tématu [Použití značek k uspořádání prostředků.](../articles/azure-resource-manager/management/tag-resources.md)
* Minimální a maximální doporučení virtuálního procesoru a paměti – pokud má vaše image doporučení virtuálního procesoru a paměti, můžete tyto informace připojit k definici bitové kopie.
* Nepovolené typy disků – můžete poskytnout informace o potřebách úložiště pro váš virtuální počítač. Pokud například obraz není vhodný pro standardní disky PEVNÉHO DISKU, přidejte je do seznamu zakázaných položek.

## <a name="generalized-and-specialized-images"></a>Generalizované a specializované obrázky

Sdílená galerie obrázků podporuje dva stavy operačního systému. Obrazy obvykle vyžadují, aby virtuální počítač použitý k vytvoření bitové kopie byl před pořizováním obrázku zobecněn. Generalizace je proces, který odebere informace specifické pro počítač a uživatele z virtuálního počítače. Pro systém Windows se používá také program Sysprep. Pro Linux můžete použít [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` nebo `-deprovision+user` parametry.

Specializované virtuální počítače nebyly prostřednictvím procesu odebrání informací a účtů specifických pro počítač. Virtuální chody vytvořené ze specializovaných imitací také nemají `osProfile` přidružené k nim. To znamená, že specializované obrázky budou mít určitá omezení.

- Účty, které by se daly použít k přihlášení k virtuálnímu počítači, se můžou taky použít na libovolném virtuálním počítači vytvořeném pomocí specializované image, která se vytvoří z tohoto virtuálního počítače.
- Virtuální počítače budou mít **název počítače** virtuálního počítače, ze kterého byla odebrána bitová kopie. Měli byste změnit název počítače, abyste se vyhnuli kolizím.
- Je, `osProfile` jak některé citlivé informace jsou předávány do virtuálního soudu pomocí `secrets`. To může způsobit problémy s použitím KeyVault, `secrets` WinRM a další funkce, které používá v `osProfile`. V některých případech můžete použít identity spravované služby (MSI) k řešení těchto omezení.

> [!IMPORTANT]
> Specializované obrázky jsou v současné době ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Známá omezení náhledu** Virtuální aplikace lze vytvořit pouze z specializovaných bitových kopií pomocí portálu nebo rozhraní API. Není žádná podpora cli nebo PowerShell pro náhled.


## <a name="regional-support"></a>Regionální podpora

Zdrojové oblasti jsou uvedeny v následující tabulce. Všechny veřejné oblasti mohou být cílové oblasti, ale k replikaci do austrálie – střed a austrálie – střed 2 musíte mít předplatné na seznamu povolených. Chcete-li požádat o zařazení na seznam povolených, přejděte na:https://azure.microsoft.com/global-infrastructure/australia/contact/


| Zdrojové oblasti        |                   |                    |                    |
| --------------------- | ----------------- | ------------------ | ------------------ |
| Austrálie – střed     | Čína – východ        | Indie – jih        | Západní Evropa        |
| Austrálie – střed 2   | Čína východ 2      | Jihovýchodní Asie     | Spojené království – jih           |
| Austrálie – východ        | Čína – sever       | Japonsko – východ         | Spojené království – západ            |
| Austrálie – jihovýchod   | Čína Sever 2     | Japonsko – západ         | US DoD – střed     |
| Brazílie – jih          | Východní Asie         | Jižní Korea – střed      | US DoD – východ        |
| Střední Kanada        | USA – východ           | Jižní Korea – jih        | USA (Gov) – Arizona     |
| Kanada – východ           | USA – východ 2         | USA – středosever   | USA (Gov) – Texas       |
| Indie – střed         | Východní USA 2 EUAP    | Severní Evropa       | USA (Gov) – Virginia    |
| USA – střed            | Francie – střed    | USA – středojih   | Indie – západ         |
| Centrální US EUAP       | Francie – jih      | USA – středozápad    | USA – západ            |
|                       |                   |                    | USA – západ 2          |



## <a name="limits"></a>Omezení 

Existují omezení pro nasazení prostředků pomocí sdílených galerií obrázků na jedno předplatné:
- 100 sdílených galerií obrázků na jedno předplatné a na region
- 1 000 definic obrázků na jedno předplatné a na oblast
- 10 000 verzí obrázků na jedno předplatné a na oblast
- Každý disk připojený k obrázku musí být menší nebo roven velikosti 1 TB.

Další informace naleznete v [tématu Kontrola využití prostředků proti omezení](https://docs.microsoft.com/azure/networking/check-usage-against-limits) mj.
 
## <a name="scaling"></a>Škálování
Galerie sdílených bitových kopií umožňuje určit počet replik, které má Azure uchovávat. To pomáhá ve scénářích nasazení více virtuálních počítače, protože nasazení virtuálních počítače lze rozšířit na různé repliky, což snižuje pravděpodobnost, že zpracování vytvoření instance bude omezeno z důvodu přetížení jedné repliky.

Pomocí Galerie sdílených obrázků teď můžete nasadit až 1 000 instancí virtuálních počítačů ve škálovací sadě virtuálních počítačů (až od 600 se spravovanými bitovými kopiemi). Repliky bitových obrázků poskytují lepší výkon, spolehlivost a konzistenci nasazení. Můžete nastavit jiný počet replik v každé cílové oblasti, na základě potřeb škálování pro oblast. Vzhledem k tomu, že každá replika je hlubokou kopií bitové kopie, pomáhá to lineárně škálovat nasazení s každou další replikou. I když chápeme, že žádné dva obrázky nebo oblasti nejsou stejné, zde je naše obecné pokyny, jak používat repliky v oblasti:

- Pro nasazení sady škálování na virtuálním počítači (VMSS) – pro každých 20 virtuálních počítače, které vytvoříte souběžně, doporučujeme zachovat jednu repliku. Pokud například vytváříte 120 virtuálních počítačů souběžně pomocí stejné bitové kopie v oblasti, doporučujeme zachovat alespoň 6 replik bitové kopie. 
- Pro nasazení sady velikosti virtuálního počítače (VMSS) – pro každé nasazení škálovací sady s až 600 instancemi doporučujeme zachovat alespoň jednu repliku. Pokud například vytváříte 5 škálovacích sad souběžně, každá s 600 instancemi virtuálních počítače pomocí stejného obrázku v jedné oblasti, doporučujeme zachovat alespoň 5 replik bitové kopie. 

Vždy doporučujeme, abyste překročili počet replik kvůli faktorům, jako je velikost obrázku, obsah a typ operačního systému.

![Obrázek znázorňující, jak lze změnit velikost obrázků](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Vysoce dostupné obrázky

[Azure Zone Redundant Storage (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) poskytuje odolnost proti selhání zóny dostupnosti v oblasti. S obecnou dostupností Galerie sdílených obrázků můžete ukládat obrázky v účtech ZRS v oblastech s zónami dostupnosti. 

Můžete také zvolit typ účtu pro každou z cílových oblastí. Výchozí typ účtu úložiště je Standard_LRS, ale můžete zvolit Standard_ZRS pro oblasti s zónami dostupnosti. Zkontrolujte regionální dostupnost ZRS [zde](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs).

![Obrázek znázorňující ZRS](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Replikace
Galerie sdílených bitových kopií také umožňuje automaticky replikovat vaše image do jiných oblastí Azure. Každou verzi sdílené bitové kopie lze replikovat do různých oblastí v závislosti na tom, co dává smysl pro vaši organizaci. Jedním z příkladů je vždy replikovat nejnovější bitové kopie ve více oblastech, zatímco všechny starší verze jsou k dispozici pouze v 1 oblasti. To může pomoci ušetřit náklady na úložiště pro verze sdílené image. 

Oblasti, do kterých je replikována verze sdílené bitové kopie, lze po vytvoření aktualizovat. Doba, kterou trvá replikace do různých oblastí, závisí na množství kopírovaných dat a na počtu oblastí, do kterých je verze replikována. To může trvat několik hodin v některých případech. Během replikace můžete zobrazit stav replikace podle oblasti. Po dokončení replikace bitové kopie v oblasti, pak můžete nasadit virtuální ho virtuálního serveru nebo škálování nastavit pomocí této verze bitové kopie v oblasti.

![Obrázek znázorňující, jak lze replikovat obrázky](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Access

Jako sdílené image Galerie, definice bitové kopie a image verze jsou všechny prostředky, které lze sdílet pomocí integrované nativní ovládací prvky Azure RBAC. Pomocí RBAC můžete sdílet tyto prostředky s ostatními uživateli, instanční objekty a skupiny. Můžete dokonce sdílet přístup k jednotlivcům mimo tenanta, ve které byly vytvořeny. Jakmile má uživatel přístup k verzi sdílené image, může nasadit virtuální počítač nebo škálovací sadu virtuálního počítače.  Zde je matice sdílení, která pomáhá pochopit, k čemu uživatel získá přístup:

| Sdíleno s uživatelem     | Sdílená galerie obrázků | Definice image | Verze image |
|----------------------|----------------------|--------------|----------------------|
| Sdílená galerie obrázků | Ano                  | Ano          | Ano                  |
| Definice image     | Ne                   | Ano          | Ano                  |

Doporučujeme sdílení na úrovni galerie pro nejlepší zážitek. Nedoporučujeme sdílet jednotlivé verze obrázků. Další informace o RBAC najdete [v tématu Správa přístupu k prostředkům Azure pomocí RBAC](../articles/role-based-access-control/role-assignments-portal.md).

Obrázky lze také sdílet ve velkém měřítku, a to i mezi tenanty pomocí registrace více klientské aplikace. Další informace o sdílení ibi obrazů mezi tenanty najdete v [tématu Sdílení image virtuálních počítačů galerie napříč tenanty Azure](../articles/virtual-machines/linux/share-images-across-tenants.md).

## <a name="billing"></a>Fakturace
Za použití služby Galerie sdílených obrázků se neúčtuje žádný příplatek. Bude vám účtovány následující zdroje:
- Náklady na úložiště pro ukládání verzí sdílených obrázků. Náklady závisí na počtu replik verze bitové kopie a počtu oblastí, do kterých je verze replikována. Pokud máte například 2 bitové kopie a obě jsou replikovány do 3 oblastí, bude vám účtováno 6 spravovaných disků na základě jejich velikosti. Další informace naleznete v tématu [Ceny spravovaných disků](https://azure.microsoft.com/pricing/details/managed-disks/).
- Poplatky za odchozí přenos sítě pro replikaci první verze bitové kopie ze zdrojové oblasti do replikovaných oblastí. Následné repliky jsou zpracovány v rámci oblasti, takže nejsou žádné další poplatky. 

## <a name="updating-resources"></a>Aktualizace prostředků

Po vytvoření můžete provést některé změny ve zdrojích galerie obrázků. Ty jsou omezeny na:
 
Sdílená galerie obrázků:
- Popis

Definice obrázku:
- Doporučené virtuální procesory
- Doporučená paměť
- Popis
- Datum konce životnosti

Verze obrázku:
- Počet regionálních replik
- Cílové oblasti
- Vyloučit z nejnovějších
- Datum konce životnosti

## <a name="sdk-support"></a>Podpora SDK

Vytváření sdílených galerií podporují následující sady SDK:

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/@azure/arm-compute)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Přejít](https://docs.microsoft.com/azure/go/)

## <a name="templates"></a>Šablony

Prostředek Galerie sdílených obrázků můžete vytvořit pomocí šablon. K dispozici je několik šablon Azure QuickStart: 

- [Vytvoření galerie sdílených obrázků](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Vytvoření definice obrazu ve sdílené galerii obrázků](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Vytvoření verze obrázku ve sdílené galerii obrázků](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Vytvoření virtuálního virtuálního virtuálního mísy z verze bitové kopie](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Nejčastější dotazy 

* [Jak lze vypsat všechny prostředky Galerie sdílených obrázků napříč předplatnými?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [Můžu přesunout svůj stávající obrázek do sdílené galerie obrázků?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [Mohu vytvořit verzi bitové kopie ze specializovaného disku?](#can-i-create-an-image-version-from-a-specialized-disk)
* [Mohu přesunout prostředek Galerie sdílených obrázků do jiného předplatného po jeho vytvoření?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [Můžu replikovat verze image napříč cloudy, jako je Azure China 21Vianet nebo Azure Germany nebo Azure Government Cloud?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [Mohu replikovat verze bitových obrázků napříč předplatnými?](#can-i-replicate-my-image-versions-across-subscriptions)
* [Můžu sdílet verze image mezi klienty Azure AD?](#can-i-share-image-versions-across-azure-ad-tenants)
* [Jak dlouho trvá replikace verzí bitových obrázků v cílových oblastech?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [Jaký je rozdíl mezi zdrojovou a cílovou oblastí?](#what-is-the-difference-between-source-region-and-target-region)
* [Jak lze zadat zdrojovou oblast při vytváření verze bitové kopie?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [Jak lze zadat počet replik verzí bitových obrázků, které mají být vytvořeny v jednotlivých oblastech?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [Mohu vytvořit sdílenou galerii obrázků na jiném místě, než je pro definici obrazu a verzi obrázku?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Jaké jsou poplatky za používání Galerie sdílených obrázků?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Jakou verzi rozhraní API mám použít k vytvoření galerie sdílených obrázků a definice obrázků a verze obrázku?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [Jakou verzi rozhraní API mám použít k vytvoření sady sdíleného virtuálního počítače nebo škálování virtuálního počítače z verze bitové kopie?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Jak lze vypsat všechny prostředky Galerie sdílených obrázků napříč předplatnými?

Chcete-li vypsat všechny prostředky Sdílené galerie obrázků napříč předplatnými, ke kterým máte přístup na webu Azure Portal, postupujte podle následujících kroků:

1. Otevřete [portál Azure](https://portal.azure.com).
1. Přejděte na **všechny zdroje**.
1. Vyberte všechna předplatná, pod kterými chcete uvést všechny prostředky.
1. Vyhledejte zdroje typu **Soukromá galerie**.
 
   Chcete-li zobrazit definice obrázků a verze obrázků, měli byste také vybrat **možnost Zobrazit skryté typy**.
 
   Chcete-li vypsat všechny prostředky Galerie sdílených obrázků napříč předplatnými, ke kterým máte oprávnění, použijte následující příkaz v příkazu Azure CLI:

   ```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>Můžu přesunout svůj stávající obrázek do sdílené galerie obrázků?
 
Ano. Existují 3 scénáře založené na typech obrázků, které můžete mít.

 Scénář 1: Pokud máte spravovanou bitovou kopii ve stejném předplatném jako vaše SIG, můžete z ní vytvořit definici bitové kopie a verzi bitové kopie.

 Scénář 2: Pokud máte nespravovanou bitovou kopii ve stejném předplatném jako vaše SIG, můžete z ní vytvořit spravovanou bitovou kopii a potom z ní vytvořit definici bitové kopie a verzi bitové kopie. 

 Scénář 3: Pokud máte v místním systému souborů virtuální pevný disk, musíte nahrát virtuální pevný disk do spravované bitové kopie, pak z ní můžete vytvořit definici bitové kopie a verzi bitové kopie.

- Pokud je virtuální pevný disk virtuálního virtuálního mísu Windows, přečtěte si část [Nahrání virtuálního pevného disku](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Pokud je virtuální disk pro virtuální počítač s Linuxem, přečtěte si informace [o nahrání virtuálního pevného disku](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>Mohu vytvořit verzi bitové kopie ze specializovaného disku?

Ano, podpora pro specializované disky jako obrázky je ve verzi Preview. Virtuální počítač můžete vytvořit jenom ze specializované bitové kopie pomocí portálu ([Windows](../articles/virtual-machines/linux/shared-images-portal.md) nebo [Linux](../articles/virtual-machines/linux/shared-images-portal.md)) a rozhraní API. Neexistuje žádná podpora prostředí PowerShell pro náhled.

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Mohu přesunout prostředek Galerie sdílených obrázků do jiného předplatného po jeho vytvoření?

Ne, prostředek sdílené galerie obrázků nelze přesunout do jiného předplatného. Budete však moci replikovat verze bitových obrázků v galerii do jiných oblastí podle potřeby.

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Můžu replikovat verze image napříč cloudy, jako je Azure China 21Vianet nebo Azure Germany nebo Azure Government Cloud?

Ne, verze bitové kopie nelze replikovat napříč cloudy.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>Mohu replikovat verze bitových obrázků napříč předplatnými?

Ne, můžete replikovat verze bitové kopie napříč oblastmi v předplatném a použít ji v jiných předplatných prostřednictvím RBAC.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Můžu sdílet verze image mezi klienty Azure AD? 

Ano, rbac můžete sdílet s jednotlivci napříč tenanty. Ale chcete-li sdílet ve velkém měřítku, najdete v tématu "Sdílet galerie image mezi tenanty Azure" pomocí [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) nebo [CLI](../articles/virtual-machines/linux/share-images-across-tenants.md).

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>Jak dlouho trvá replikace verzí bitových obrázků v cílových oblastech?

Doba replikace verze bitové kopie je zcela závislá na velikosti bitové kopie a počtu oblastí, do kterých je replikována. Jako osvědčený postup se však doporučuje zachovat bitovou kopii a zdrojové a cílové oblasti blízko pro dosažení nejlepších výsledků. Stav replikace můžete zkontrolovat pomocí příznaku -ReplicationStatus.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>Jaký je rozdíl mezi zdrojovou a cílovou oblastí?

Zdrojová oblast je oblast, ve které bude vytvořena verze bitové kopie, a cílové oblasti jsou oblasti, ve kterých bude uložena kopie verze bitové kopie. Pro každou verzi bitové kopie můžete mít pouze jednu zdrojové oblasti. Při vytváření verze bitové kopie se také ujistěte, že předáte umístění zdrojové oblasti jako jednu z cílových oblastí.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>Jak lze zadat zdrojovou oblast při vytváření verze bitové kopie?

Při vytváření verze bitové kopie můžete k určení zdrojové oblasti použít značku **--location** v cli a značku **-Location** v prostředí PowerShell. Ujistěte se, že spravovaná bitová kopie, kterou používáte jako základní bitovou kopii k vytvoření verze bitové kopie, je ve stejném umístění jako umístění, ve kterém chcete vytvořit verzi bitové kopie. Při vytváření verze bitové kopie se také ujistěte, že předáte umístění zdrojové oblasti jako jednu z cílových oblastí.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>Jak lze zadat počet replik verzí bitových obrázků, které mají být vytvořeny v jednotlivých oblastech?

Počet replik verzí bitových bitových žerek, které mají být vytvořeny v jednotlivých oblastech, můžete zadat dvěma způsoby:
 
1. Počet regionálních replik, který určuje počet replik, které chcete vytvořit pro oblast. 
2. Společný počet replik, který je výchozí počet na oblast v případě, že není zadán počet regionálních replik. 

Chcete-li určit počet regionálních replik, předejte umístění spolu s počtem replik, které chcete vytvořit v této oblasti: "Jižní střed USA = 2". 

Pokud není u každého umístění zadán počet místních replik, bude výchozím počtem replik společný počet replik, který jste zadali. 

Chcete-li určit společný počet replik v příkazu cli, použijte argument **--replica-count** v příkazu. `az sig image-version create`

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>Mohu vytvořit sdílenou galerii obrázků na jiném místě, než je pro definici obrazu a verzi obrázku?

Ano, je to možné. Jako osvědčený postup však doporučujeme zachovat skupinu prostředků, sdílenou galerii obrázků, definici obrázku a verzi obrázku ve stejném umístění.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Jaké jsou poplatky za používání Galerie sdílených obrázků?

Za použití služby Galerie sdílených obrázků se neúčtují žádné poplatky, s výjimkou poplatků za ukládání verzí bitové kopie a poplatků za odchozí síťové přenosy pro replikaci verzí bitové kopie ze zdrojové oblasti do cílových oblastí.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Jakou verzi rozhraní API mám použít k vytvoření galerie sdílených obrázků a definice obrázků a verze obrázku?

Chcete-li pracovat se sdílenými galeriemi obrázků, definicemi obrázků a verzemi obrázků, doporučujeme použít rozhraní API verze 2018-06-01. Zónové redundantní úložiště (ZRS) vyžaduje verzi 2019-03-01 nebo novější.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Jakou verzi rozhraní API mám použít k vytvoření sady sdíleného virtuálního počítače nebo škálování virtuálního počítače z verze bitové kopie?

Pro nasazení škálování virtuálních počítačů a virtuálních strojů pomocí verze image doporučujeme použít rozhraní API verze 2018-04-01 nebo vyšší.
