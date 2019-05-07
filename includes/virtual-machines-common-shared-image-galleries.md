---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 0fe1de9bb674c66d1b665de25ee579bc86e42c75
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192370"
---
Sdílené Galerie Imagí je služba, která vám pomůže sestavit strukturu a organizace vlastní spravované Image virtuálních počítačů. Zadejte sdílený Galerie obrázků:

- Spravovat globální replikací obrázků.
- Správa verzí a seskupení obrázky pro snadnější správu.
- Zpřístupníte vaše Image s účty Zónově redundantního úložiště (ZRS) s vysokou dostupností v oblasti, které podporují zóny dostupnosti. ZRS nabízí lepší odolnost proti chybám oblastmi.
- Sdílení napříč předplatnými a dokonce i mezi tenanty používající RBAC.

Pomocí Galerie obrázků Shared můžete sdílet vaše Image jiným uživatelům, objektů služby nebo skupiny služby AD v rámci vaší organizace. Sdílené bitové kopie je možné replikovat do více oblastí, pro rychlejší škálování vašeho nasazení.

Spravované image je kopie buď úplná virtuálního počítače (včetně jakýchkoliv připojených datových disků) nebo jenom disk s operačním systémem, v závislosti na tom, jak vytvořit image. Při vytváření virtuálního počítače z image kopie virtuálních pevných disků na obrázku se používají k vytvoření disky pro nový virtuální počítač. Spravované image zůstává v úložišti a je možné znovu a znovu vytvořit nové virtuální počítače.

Pokud máte velký počet spravované Image, které je potřeba, abyste a chcete zpřístupnit v rámci vaší společnosti, můžete jako úložiště, který umožňuje snadno sdílet vaše Image Galerie obrázků Shared. 

Galerie obrázků sdílené funkce má více typů prostředků:

| Prostředek | Popis|
|----------|------------|
| **Spravované image** | Základní image, který můžete používat samostatně nebo použít k vytvoření **verze image** v Galerie obrázků. Vytváření spravovaných imagí z generalizovaného virtuálních počítačů. Spravované image je speciální typ virtuálního pevného disku, který je možné vytvořit několik virtuálních počítačů a můžete teď použít k vytvoření verze sdílené bitové kopie. |
| **Galerie obrázků** | Na webu Azure Marketplace, jako jsou **Galerie obrázků** je úložiště pro správu a sdílení imagí, ale vy řídíte, kdo má přístup. |
| **Definici Image** | Image jsou definovány v rámci Galerie a nesou informaci o image a požadavky pro použití v rámci vaší organizace. Můžete zahrnout informace, například, jestli je image Windows nebo Linux, požadavky na minimální a maximální paměť a zpráva k vydání verze. Je to definice typu bitové kopie. |
| **Verze bitové kopie** | **Verze image** se používá k vytvoření virtuálního počítače, když použijete galerii. Podle potřeby pro vaše prostředí můžete mít více verze Image. Při použití, jako jsou spravované image **verze image** vytvoření virtuálního počítače, verze image slouží k vytvoření nové disky pro virtuální počítač. Verze Image můžete použít více než jednou. |

<br>


![Obrázek znázorňující, jak může mít více verzí image v galerii](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Definice imagí

Definice bitové kopie jsou logické seskupení pro verze image. K definici image obsahuje informace o proč image byla vytvořena, jaké je pro operační systém a informace o pomocí image. Definici image je jako plán pro všechny podrobnosti související s vytvářením konkrétní image. Není-li nasadit virtuální počítač z definice bitové kopie, ale z verze image vytvořené z definice.


Existují tři parametry pro každou definici bitové kopie, které se používají v kombinaci - **vydavatele**, **nabízejí** a **SKU**. Ty se používají pro vyhledání definice konkrétní image. Verze bitové kopie, které sdílejí jeden nebo dva, ale ne všechny tři hodnoty může mít.  Tady jsou například tři definice, které image a jejich hodnoty:

|Definice image|Vydavatel|Nabídka|Skladová jednotka (SKU)|
|---|---|---|---|
|myImage1|Contoso|Finance|Back-end|
|myImage2|Contoso|Finance|Front-end|
|myImage3|Testování|Finance|Front-end|

Všechny tyto tři mají jedinečné sady hodnot. Formát je podobný jak aktuálně určíte vydavatele, nabídky a skladové položky pro [Image Azure Marketplace](../articles/virtual-machines/windows/cli-ps-findimage.md) v prostředí Azure PowerShell, abyste získali nejnovější verzi Marketplace image. Každá definice obrázek musí mít jedinečnou sadu tyto hodnoty.

Tady jsou další parametry, které lze nastavit ve vaší image definice, aby mohli snadněji sledovat prostředky:

* Stav operačního systému – stav operačního systému můžete nastavit na generalizovaný nebo specializovaná, ale pouze zobecněn je momentálně podporován. Bitové kopie musí být vytvořené z virtuálních počítačů, které byl zobecněn pomocí nástroje Sysprep pro Windows nebo `waagent -deprovision` pro Linux.
* Operační systém – může být Windows nebo Linux.
* Popis – popis použití poskytnout podrobné informace o Proč existuje k definici image. Například může mít definici image pro front-endového serveru, na němž byla aplikace předem nainstalována.
* Smlouva EULA – je možné tak, aby odkazoval na licenční smlouva s koncovým uživatelem specifické pro definici image.
* Zásady ochrany osobních údajů a verze poznámky – ukládání poznámky k verzi a ochrany osobních údajů ve službě Azure storage a zadejte identifikátor URI pro přístup k nim jako součást k definici image.
* Ukončenou životností datum – aby bylo možné pomocí služby automation můžete odstranit staré image definice připojení datem ukončení životnosti technologie definici image.
* Značka – značky můžete přidat, při vytváření vaší image definice. Další informace o značkách najdete v tématu [použití značek k uspořádání prostředků](../articles/azure-resource-manager/resource-group-using-tags.md)
* Minimální a maximální virtuálních procesorů a paměti doporučení – Pokud image obsahuje doporučení pro virtuální procesory a paměti, můžete připojit tyto informace do definice bitové kopie.
* Nepovolené typy disků – můžete zadat informace o potřeby úložiště pro virtuální počítač. Například pokud bitovou kopii není vhodné pro disky standard pevný disk, je přidáte do seznamu zakázat.


## <a name="regional-support"></a>Místní podpora

V následující tabulce jsou uvedeny zdrojových oblastí. Všech veřejných oblastech může být cílové oblasti, ale pokud chcete replikovat do Austrálie-střed a Austrálie – střed 2, musíte mít vaše předplatné povolený. Chcete-li požádat o přidávání na seznam povolených, přejděte na: https://www.microsoft.com/en-au/central-regions-eligibility/


| Zdrojových oblastí |
|---------------------|-----------------|------------------|-----------------|
| Austrálie – střed   | Střed USA – EUAP | Korea – střed    | Velká Británie – jih 2      |
| Austrálie – střed 2 | Východní Asie       | Jižní Korea – jih      | Spojené království – západ         |
| Austrálie – východ      | USA – východ         | Středoseverní USA | Západní střed USA |
| Austrálie – jihovýchod | Východní USA 2       | Severní Evropa     | Západní Evropa     |
| Brazílie – jih        | Východ USA 2 – EUAP  | Středojižní USA | Indie – západ      |
| Kanada – střed      | Francie – střed  | Indie – jih      | Západní USA         |
| Kanada – východ         | Francie – jih    | Jihovýchodní Asie   | Západní USA         |
| Střed Indie       | Japonsko – východ      | Velká Británie – sever         | Západní USA 2       |
| USA – střed          | Japonsko – západ      | Velká Británie – jih         |                 |



## <a name="limits"></a>Limits 

Existují omezení na jedno předplatné, pro nasazení prostředků pomocí Galerie sdílené bitové kopie:
- 100 Galerie sdílené bitové kopie na předplatné a oblast
- 1 000 obrázků definice na předplatné a oblast
- 10 000 obrázků verze na předplatné a oblast

Další informace najdete v tématu [kontrola využití prostředků proti omezení](https://docs.microsoft.com/azure/networking/check-usage-against-limits) příklady o tom, jak zkontrolovat aktuální využití.
 

## <a name="scaling"></a>Škálování
Sdílené Galerie Imagí můžete zadat počet replik, které chcete do imagí Azure. Tato funkce umožňuje scénáře nasazení více virtuálních počítačů při nasazení virtuálního počítače je možné rozdělit do různých replik snižuje pravděpodobnost vytvoření instance zpracování omezený přetížení jen jednu repliku.

![Obrázek znázorňující, jak můžete škálovat imagí](./media/shared-image-galleries/scaling.png)


## <a name="replication"></a>Replikace
Sdílené Galerie obrázků vám také umožní automaticky replikovat vaši Image do jiných oblastí Azure. Každá verze sdílené bitové kopie je možné replikovat do různých oblastí v závislosti na tom, co dává smysl pro vaši organizaci. Jedním z příkladů je vždy replikovat nejnovější image v několika oblastech, všechny starší verze jsou k dispozici pouze v 1 oblast. To může pomoct ušetřit na náklady na úložiště pro sdílené bitové kopie verze. 

Oblasti, kterou verzi sdílené bitové kopie se replikuje do je možné aktualizovat po času vytvoření. Čas potřebný k replikaci do různých oblastí závisí na množství dat, kopírování a počtem oblastí verze se replikuje do. V některých případech to může trvat několik hodin. Replikace se děje, ale když zobrazíte stav replikace v jedné oblasti. Po dokončení replikace image je v oblasti, pak můžete nasadit virtuální počítač nebo škálovací sadě pomocí této verze image v oblasti.

![Obrázek znázorňující, jak se dají replikovat imagí](./media/shared-image-galleries/replication.png)


## <a name="access"></a>Access

Galerie obrázků sdílené, sdílené bitové kopie a sdílené bitové kopie verze jsou všechny prostředky, je může sdílet, pomocí integrovaného Azure RBAC nativní ovládací prvky. Pomocí RBAC můžete sdílet tyto prostředky pro další uživatele, objektů služby a skupiny. Můžete sdílet i přístup osobám mimo tenanta, které byly vytvořeny v rámci. Jakmile uživatel má přístup k verzi sdílené bitové kopie, můžou třeba nasadit virtuální počítač nebo Škálovací sady virtuálních počítačů.  Tady je sdílení, která pomáhá pochopit, co uživatel získá přístup k matici:

| Sdílené s uživatelem     | Sdílená galerie obrázků | Sdílené bitové kopie | Sdílené verze Image |
|----------------------|----------------------|--------------|----------------------|
| Sdílená galerie obrázků | Ano                  | Ano          | Ano                  |
| Sdílené bitové kopie         | Ne                   | Ano          | Ano                  |
| Sdílené verze Image | Ne                   | Ne           | Ano                  |

Doporučujeme, abyste sdílení na úrovni Galerie pro dosažení co nejlepších výsledků. Další informace o RBAC najdete v tématu [spravovat přístup k prostředkům Azure pomocí RBAC](../articles/role-based-access-control/role-assignments-portal.md).

Image můžete také sdílet, ve velkém měřítku, mezi tenanty používající registrace aplikace s více tenanty. Další informace o sdílení imagí mezi tenanty najdete v tématu [sdílet Galerie imagí virtuálních počítačů tenantů Azure](../articles/virtual-machines/linux/share-images-across-tenants.md).

## <a name="billing"></a>Fakturace
Neexistuje žádné další poplatky za využívání služby Shared Galerie obrázků. Účtuje se pro následující prostředky:
- Náklady na úložiště ukládání verzí sdílených bitových kopií. Cena závisí na počtu replik verze image a počtem oblastí verze se replikuje do. Například pokud máte 2 bitových kopií a obě se replikují do 3 oblasti, pak jste se změní za 6 spravované disky na základě jejich velikosti. Další informace najdete v tématu [cenami služby Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).
- Poplatky za výchozí přenos dat sítě pro replikaci první verze image ze zdrojové oblasti na replikovaných oblastech. Následující repliky jsou zpracovány v rámci oblasti, takže se neúčtují žádné další poplatky. 

## <a name="updating-resources"></a>Aktualizují se prostředky.

Po vytvoření můžete provedete některé změny prostředků Galerie obrázků. Je omezený na:
 
Galerie sdílené bitové kopie:
- Popis

definice bitové kopie:
- Doporučené virtuálních procesorů
- Doporučená velikost paměti
- Popis
- Životnost datum ukončení

Verze Image:
- Počet replik místní
- Cílové oblasti
- Vyloučení z nejnovější
- Životnost datum ukončení


## <a name="sdk-support"></a>Podpora v sadě SDK

Následující sady SDK podporují vytváření sdílených Galerie obrázků:

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/azure-arm-compute/?view=azure-node-latest)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/go/azure/)

## <a name="templates"></a>Šablony

Můžete vytvořit Galerie obrázků sdílených prostředků pomocí šablon. Nejsou k dispozici několik šablon rychlý start Azure: 

- [Vytvořit sdílené Galerie obrázků](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Vytvoření definice Image v galerii sdílené bitové kopie](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Vytvoření Image verze v sdílené Galerie obrázků](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Vytvoření virtuálního počítače z Image verze](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Nejčastější dotazy 

**Otázka:** Jak vytvořit seznam všech prostředků Galerie obrázků sdílené napříč předplatnými? 
 
 A. Pokud chcete vypsat všechny prostředky Galerie obrázků sdílené napříč předplatnými, že máte přístup k webu Azure portal, postupujte podle následujících kroků:

1. Otevřete web [Azure Portal](https://portal.azure.com).
1. Přejděte na **všechny prostředky**.
1. Vyberte všechny odběry, za kterých byste chtěli vypíše všechny prostředky.
1. Vyhledejte prostředky typu **privátní Galerie**.
 
   Definice image a image verze zobrazíte musí také vybrat **zobrazit skryté typy**.
 
   Seznam všech prostředků Galerie obrázků sdílené napříč předplatnými, ke kterým máte oprávnění k, použijte následující příkaz v rozhraní příkazového řádku Azure:

   ```bash
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```


**Otázka:** Můžete přesunout svůj stávající obrázek v galerii sdílené bitové kopie
 
 A. Ano. Existují 3 scénáře na základě typů imagí, které máte uzavřeny.

 Scénář 1: Pokud máte spravované image, pak je můžete vytvořit definici image a verze image z něj.

 Scénář 2: Pokud máte nespravované generalizované image, můžete vytvoření spravované image z něj a pak vytvořte definici image a image verze z něj. 

 Scénář 3: Pokud máte virtuální pevný disk ve vašem místním systému souborů, budete muset nahrát virtuální pevný disk, vytvořte spravovanou image, můžete vytvořit a obrazu definice a verze image z něj.
- Pokud virtuální pevný disk z virtuálního počítače s Windows, přečtěte si téma [nahrání generalizovaného virtuálního pevného disku](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Pokud virtuální pevný disk pro virtuální počítač s Linuxem, přečtěte si téma [nahrání virtuálního pevného disku](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)


**Otázka:** Můžete vytvořit image verze ze specializovaného disku?

 A. Ne, nepodporujeme aktuálně specializované disky jako Image. Pokud budete mít speciální disk, budete muset [vytvoření virtuálního počítače z virtuálního pevného disku](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal#create-a-vm-from-a-disk) připojením specializovaného disku k novému virtuálnímu počítači. Jakmile budete mít spuštěný virtuální počítač, budete muset postupovat podle pokynů a vytvořte spravovanou image z [virtuálního počítače Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-custom-images) nebo [virtuálního počítače s Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Až budete mít generalizované image spravovaného, můžete zahájit proces vytvořit popis sdílené bitové kopie a verze image.

 
**Otázka:** Po vytvoření můžu přesunout Galerie obrázků sdíleného prostředku do jiného předplatného?

 A. Ne, nelze přesunout prostředek Galerie sdílené bitové kopie do jiného předplatného. Nicméně je možné replikovat do jiných oblastí podle potřeby verze image v galerii.

**Otázka:** Můžete replikovat Moje image verzí napříč cloudy – Azure China 21Vianet, Azure Germany a cloudu Azure Government? 

 A. Verze image Ne, nedokáže replikovat napříč cloudy.

**Otázka:** Můžete replikovat Moje image verzí napříč předplatnými? 

 A. Ne, můžete replikovat verze image mezi oblastmi v rámci předplatného a používat v jiných předplatných pomocí RBAC.

**Otázka:** Můžete sdílet verze image tenantů Azure AD? 

 A. Ano, vám pomůže RBAC sdílet s jednotlivci napříč tenanty. Sdílet ve velkém měřítku, ale v tématu "sdílené položky galerie obrázků tenantů Azure" použití [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) nebo [CLI](../articles/virtual-machines/linux/share-images-across-tenants.md).


**Otázka:** Jak dlouho trvá replikovat verze bitové kopie do cílové oblasti?

 A. Doba replikace verze image je zcela závisí na velikosti image a počtem oblastí, které je právě replikován pro. Jako osvědčený postup, je však doporučeno, zachovat malý obrázek a zavřete zdrojovou a cílovou oblastí pro dosažení co nejlepších výsledků. Můžete zkontrolovat stav replikace použitím příznaku - stav replikace.


**Otázka:** Jaký je rozdíl mezi oblast zdrojové a cílové oblasti?

 A. Zdrojová oblast je oblast, ve kterém se vytvoří image verze a cílové oblasti jsou oblasti, ve kterých se uloží kopie vašich verze image. Pro každou verzi image můžete mít jenom jeden zdrojové oblasti. Také ujistěte se, že když vytvoříte image verze předat oblast umístění zdroje jako jeden z cílových oblastí.  


**Otázka:** Jak určit zdrojové oblasti při vytváření verze image?

 A. Při vytváření image verze, můžete použít **– umístění** značek v rozhraní příkazového řádku a **– umístění** značku v prostředí PowerShell k určení zdrojové oblasti. Ujistěte se prosím, že spravované image, kterou používáte jako základní image k vytvoření verze image je ve stejném umístění jako umístění, ve kterém chcete vytvořit verzi image. Také ujistěte se, že když vytvoříte image verze předat oblast umístění zdroje jako jeden z cílových oblastí.  


**Otázka:** Jak určit počet replik verze image má být vytvořen v jednotlivých oblastech?

 A. Existují dva způsoby, jak můžete zadat počet replik verze image má být vytvořen v jednotlivých oblastech:
 
1. Počet místní repliku, která určuje počet replik, budete chtít vytvořit v jedné oblasti. 
2. Běžné počet replik, což je výchozí za počet oblastí v případě, že není zadaný počet místních replik. 

Chcete-li určit počet místních replik, předejte umístění spolu s počtem replik, které chcete vytvořit v dané oblasti: "Střední část jihu USA = 2". 

Pokud počet místní replika není zadán s každé umístění, bude výchozí počet replik běžné počet replik, které jste zadali. 

Pokud chcete nastavit společné počet replik v rozhraní příkazového řádku, použijte **– počet replik** argument v `az sig image-version create` příkazu.


**Otázka:** Můžete vytvořit sdílené bitové kopie Galerie v jiném umístění než ten, ve kterém chcete vytvořit definici image a image verze?

 A. Ano, je to možné. Ale jako osvědčený postup doporučujeme ponechat skupinu prostředků, galerie sdílené bitové kopie, definici image a image verze ve stejném umístění.


**Otázka:** Co jsou poplatky za používání galerie obrázků Shared?

 A. Neúčtují žádné poplatky za používání služby Shared Galerie obrázků, s výjimkou poplatky za úložiště pro ukládání verze image a poplatky za odchozí přenos sítě pro replikaci verze image ze zdrojové oblasti do cílové oblasti.

**Otázka:** Jaké verze rozhraní API použijte k vytvoření Galerie obrázků sdílené, definici Image, Image verze a VM/VMSS mimo verzi Image?

 A. Pro nasazení virtuálního počítače a Škálovací sady virtuálních počítačů používá verzi image, doporučujeme, abyste použili verzi 2018-04-01 rozhraní API nebo vyšší. Pro práci s galerií sdílené bitové kopie, definice image a image verze, doporučujeme že použít rozhraní API verze 2018-06-01. 
