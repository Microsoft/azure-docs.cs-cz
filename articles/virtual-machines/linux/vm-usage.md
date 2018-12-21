---
title: Principy používání virtuálních počítačů Azure | Dokumentace Microsoftu
description: Pochopit podrobnosti o použití virtuálního počítače
services: virtual-machines
documentationcenter: ''
author: mmccrory
manager: jeconnoc
editor: ''
tags: azure-virtual-machine
ms.assetid: ''
ms.service: ''
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 12/04/2017
ms.author: memccror
ms.openlocfilehash: d9df71f6fa2f64544cbefc7d9aca8c153c86ac98
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094251"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Principy používání virtuálních počítačů Azure
Díky analýze dat využití Azure, může být získány výkonné consumption insights – přehledy, které umožňují lepší nákladů správy a přidělení v rámci vaší organizace. Tento dokument obsahuje podrobné informace o si podrobnosti o spotřebě Azure Compute. Podrobné informace o obecných využití Azure, přejděte na [vysvětlení vašeho vyúčtování](https://docs.microsoft.com/azure/billing/billing-understand-your-bill).

## <a name="download-your-usage-details"></a>Stáhněte si podrobnosti o využití
Pokud chcete začít, [stáhnout podrobnosti o vašem používání](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-from-the-account-center-csv). Následující tabulka obsahuje definice a ukázkové hodnoty využití pro virtuální počítače nasazené prostřednictvím Azure Resource Manageru. Tento dokument neobsahuje podrobné informace pro virtuální počítače nasazené prostřednictvím našich Klasický model.


| Fields (Pole)             | Význam                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Příklad hodnoty                                                                                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Datum využití         | Datum, kdy byl použit prostředek.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |  “11/23/2017”                                                                                                                                                                                                                                                                                                                                                     |
| ID měření           | Identifikuje službu nejvyšší úrovně, pro které se využití týká k.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | "Virtuální počítače"                                                                                                                                                                                                                                                                                                                                               |
| Podkategorie měření | Identifikátor fakturovaného měření. <ul><li>Za výpočetní hodinu využití, je měřiče pro každý virtuální počítač velikost a operační systém (Windows, než Windows) a oblasti.</li><li>Pro využití softwaru Premium je měřiče pro každý typ softwaru. Většina imagí softwaru premium mají jiných měřičů pro jednotlivé velikosti core. Další informace najdete [Compute stránce s cenami.](https://azure.microsoft.com/pricing/details/virtual-machines/)</li></ul>                                                                                                                                                                                                                                                                                                                                         | "2005544f-659d-49c9-9094-8e0aea1be3a5"                                                                                                                                                                                                                                                                                                                           |
| Název měření         | To platí pro každou službu v Azure. Pro výpočetní prostředky je vždy "Výpočetních hodin".                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "Výpočetních hodin"                                                                                                                                                                                                                                                                                                                                                  |
| Oblast měření       | Určuje polohu datového centra. U některých služeb vycházejí ceny z umístění datového centra.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |  "Japonsko – východ                                                                                                                                                                                                                                                                                                                                                       |
| Jednotka               | Určuje jednotky, ve kterých služba účtuje. Výpočetní prostředky se účtují po hodinách.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "Hours"                                                                                                                                                                                                                                                                                                                                                          |
| Spotřebované           | Množství prostředku spotřebované za příslušný den. Pro výpočetní prostředky účtujeme za každou minutu, které virtuální počítač běžel příslušné hodiny (až 6 desetinných míst přesnosti).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |    “1”, “0.5”                                                                                                                                                                                                                                                                                                                                                    |
| Umístění prostředku  | Určuje datové centrum, ve kterém prostředek běží.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | "Japonsko – východ                                                                                                                                                                                                                                                                                                                                                        |
| Spotřebovaná služba   | Služby platformy Azure, který jste použili.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | "Microsoft.Compute"                                                                                                                                                                                                                                                                                                                                              |
| Skupina prostředků     | Skupina prostředků, ve které nasazený prostředek běží. Další informace najdete v tématu [přehled Azure Resource Manageru.](https://docs.microsoft.com/azure/virtual-machines/linux/vm-usage)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |    "MyRG"                                                                                                                                                                                                                                                                                                                                                        |
| ID instance        | Identifikátor prostředku. Tento identifikátor obsahuje název prostředku, který jste zadali při jeho vytváření. Pro virtuální počítače Instance ID bude obsahovat SubscriptionId, ResourceGroupName a VMName (nebo název pro využití sady škálování škálovací sady).                                                                                                                                                                                                                                                                                                                                                                                                                    | "/ předplatná/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx / resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1"<br><br>nebo<br><br>"/ předplatná/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx / resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1"                                                                                           |
| Značky               | Značka, které přiřadíte k prostředku. Použití značek k seskupení záznamů fakturace. Zjistěte, jak [označit virtuální počítače.](tag.md) To je k dispozici pro virtuální počítače Resource Manageru pouze.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | "{"myDepartment":"VP","myUser":"Jmeno"}"                                                                                                                                                                                                                                                                                                                        |
| Další informace    | Metadata konkrétních služeb. Pro virtuální počítače naplníme následující data v poli Další informace: <ul><li>Bitové kopie konkrétní typ bitové kopie, kterou jste spustili. Vyhledejte úplný seznam podporovaných řetězců níže v části tyto typy obrázků.</li><li>Typ služby: velikost, kterou jste nasadili.</li><li>VMName: název vašeho virtuálního počítače. Toto pole se vyplní pouze pro škálovací sadu virtuálních počítačů. Pokud potřebujete název vašeho virtuálního počítače pro škálovací nastavit virtuální počítače, můžete to zjistit Instance ID řetězce výše.</li><li>UsageType: Určuje druh využití, které představují.<ul><li>ComputeHR je výpočetní hodina nástroje využití základního virtuálního počítače, jako je Standard_D1_v2.</li><li>ComputeHR_SW je poplatek za software pro úrovně premium, pokud virtuální počítač používá premium softwarem, jako je Microsoft R serveru.</li></ul></li></ul>    | Virtuální počítače {"ImageType": "Canonical", "ServiceType": "Standard_DS1_v2", "VMName": "", "UsageType": "ComputeHR"}<br><br>Škálovací sady virtuálních počítačů {"ImageType": "Canonical", "ServiceType": "Standard_DS1_v2", "VMName": "myVM1", "UsageType": "ComputeHR"}<br><br>Premium Software {"ImageType": "","ServiceType": "Standard_DS1_v2", "VMName": "", "UsageType": "ComputeHR_SW"} |

## <a name="image-type"></a>Typ image
Pro některé obrázky v galerii Azure jsou v poli Další informace o vyplněné typ bitové kopie. To umožňuje uživatelům pochopit a sledovat, co bylo nasadit své virtuální počítače. Následující hodnoty, které se vyplní v tomto poli na základě image nasadíte:
  - BitRock 
  - Canonical 
  - FreeBSD 
  - Otevřít logiky 
  - Oracle 
  - SLES pro SAP 
  - SQL Server 14 ve verzi Preview systému Windows Server 2012 R2 Preview 
  - SUSE
  - SUSE Premium
  - StorSimple Cloud Appliance 
  - Red Hat
  - Red Hat for SAP Business Applications     
  - Red Hat pro SAP HANA 
  - Klient Windows BYOL 
  - Windows Server BYOL 
  - Windows Server ve verzi Preview 

## <a name="service-type"></a>Typ služby
Je v poli Typ služby do pole Další informace o odpovídá přesnou velikost virtuálního počítače, které jste nasadili. Storage úrovně Premium (založené na discích SSD) virtuálních počítačů a úložiště jiné úrovně než premium (založené na HDD) virtuálních počítačů jsou ceny stejné. Pokud provádíte nasazení založené na discích SSD velikost jako Standard\_DS2\_v2, naleznete v tématu velikost není SSD ("standardní\_D2\_virtuálního počítače v2") ve sloupci podkategorie měřiče a velikost SSD ("standardní\_DS2\_ v2 ") v poli Další informace.

## <a name="region-names"></a>Názvy oblastí
Název oblasti, v poli umístění prostředku v podrobnosti o využití se liší od oblasti název používaný v Azure Resource Manageru. Tady je mapování mezi hodnotami oblasti:

|    **Název oblasti Resource Manageru**       |    **Umístění prostředku v podrobnosti o použití**    |
|--------------------------|------------------------------------------|
|    australiaeast         |    Austrálie – východ                               |
|    australiasoutheast    |    Austrálie – jihovýchod                          |
|    brazilsouth           |    Brazílie – jih                              |
|    CanadaCentral         |    Kanada – střed                            |
|    CanadaEast            |    Kanada – východ                               |
|    CentralIndia          |    Indie – střed                            |
|    centralus             |    USA – střed                            |
|    chinaeast             |    Čína – východ                            |
|    chinanorth            |    Čína – sever                           |
|    eastasia              |    Východní Asie                             |
|    eastus                |    USA – východ                               |
|    eastus2               |    Východní USA 2                             |
|    GermanyCentral        |    Německo – střed                            |
|    GermanyNortheast      |    Německo – severovýchod                          |
|    japaneast             |    Japonsko – východ                               |
|    japanwest             |    Japonsko – západ                               |
|    KoreaCentral          |    Korea – střed                            |
|    KoreaSouth            |    Korea – jih                              |
|    northcentralus        |    Střed USA – sever                      |
|    northeurope           |    Severní Evropa                          |
|    southcentralus        |    Střed USA – jih                      |
|    southeastasia         |    Jihovýchodní Asie                        |
|    SouthIndia            |    Indie – jih                              |
|    UKNorth               |    USA – sever                              |
|    uksouth               |    Velká Británie – jih                              |
|    UKSouth2              |    Spojené království – jih 2                            |
|    ukwest                |    Spojené království – západ                               |
|    USDoDCentral          |    US DoD – střed                        |
|    USDoDEast             |    US DoD – východ                           |
|    USGovArizona          |    USGov Arizona                         |
|    usgoviowa             |    USGov Iowa                            |
|    USGovTexas            |    USGov Texas                           |
|    usgovvirginia         |    USGov Virginie                        |
|    westcentralus         |    USA – středozápad                       |
|    westeurope            |    Západní Evropa                           |
|    WestIndia             |    Indie – západ                               |
|    westus                |    Západní USA                               |
|    westus2               |    USA – západ 2                             |


## <a name="virtual-machine-usage-faq"></a>Nejčastější dotazy k využití virtuálního počítače
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Jaké prostředky se účtují při nasazování virtuálního počítače?    
Virtuální počítače získají náklady za virtuální počítač, premium spuštěná na virtuálním počítači a disk account\managed úložiště přidružený k virtuálnímu počítači, a šířku pásma sítě přenosy z virtuálního počítače.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Jak poznám, že pokud virtuální počítač používá ve sdíleném svazku clusteru využití zvýhodněné hybridní využití Azure?
Pokud provedete nasazení pomocí [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/), bude se vám účtovat sazby za Windows bez VM vzhledem k tomu, že jsou používáním vlastní licence do cloudu. Ve vašem vyúčtování možné rozlišit, které virtuální počítače Resource Manageru jsou spuštěny zvýhodněné hybridní využití Azure, protože mají buď "Windows\_Server BYOL" nebo "Windows\_klienta BYOL" ve sloupci ImageType.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Jak se základní vs. Standardní typy virtuálních počítačů se liší ve sdíleném svazku clusteru využití?
Základní a standardní virtuální počítače řady A-Series nabízí. Pokud provádíte nasazení základního virtuálního počítače, v dílčí kategorie měřiče, obsahuje řetězec "Basic". Pokud nasadíte virtuální počítače Standard A-Series, pak velikost virtuálního počítače se zobrazí jako "Virtuální počítač A1" od standardní verze je výchozí hodnota. Další informace o rozdílech mezi Basic a Standard najdete v tématu [stránce s cenami](https://azure.microsoft.com/pricing/details/virtual-machines/).
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>Co jsou: mimořádně malý, malá, střední, velký a mimořádně velký velikostí?
Starší názvy pro úroveň Standard jsou extraSmall – ExtraLarge\_A0 – Standard\_A4. V klasické záznamů využití virtuálních počítačů může se zobrazit tato konvence použít, pokud jste nasadili těchto velikostí.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Jaký je rozdíl mezi oblast měřiče a umístění prostředků?
Oblast měřiče je měřič přidružený. Pro některé služby Azure, kteří používají jednu cenu pro všechny oblasti může být prázdné pole oblast měřiče. Ale vzhledem k tomu, že virtuální počítače mají ceny v jedné oblasti vyhrazené pro virtuální počítače, toto pole se vyplní. Umístění prostředků pro virtuální počítače podobně, je umístění, kde je virtuální počítač nasazený. Oblasti Azure v obou polí jsou stejné, i když mohou mít různé řetězec konvence pro název oblasti.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Proč je hodnota ImageType prázdné pole Další informace?
ImageType pole se vyplní jenom pro podmnožinu bitové kopie. Pokud jste některé z výše uvedených imagí nebyly nasazeny, ImageType je prázdný.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Název je prázdný v další informace
Název je jenom vyplní v poli Další informace pro virtuální počítače ve škálovací sadě. Pole ID instance obsahuje název virtuálního počítače mimo škálovací sady virtuálních počítačů.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>Co znamená ComputeHR v poli UsageType v dodatečné informace?
ComputeHR jsou zahrnovaného výpočetních hodin, který představuje událost využití pro základní náklady na infrastrukturu. Pokud je UsageType ComputeHR\_SW, událost využití představuje poplatek za software premium pro virtuální počítač.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Jak poznám, že pokud se mi účtují softwaru premium?
Při zkoumání Image virtuálního počítače nejlépe vyhovuje vašim potřebám, nezapomeňte se podívat [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute). Bitová kopie je sazba plánu softwaru. Pokud se zobrazí "Free" pro rychlost, neexistuje žádné další poplatky za software. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Jaký je rozdíl mezi Microsoft.ClassicCompute a Microsoft.Compute služby spotřebované?
Microsoft.ClassicCompute představuje klasické prostředky nasazené prostřednictvím portálu Azure Service Manager. Pokud provádíte nasazení prostřednictvím Resource Manageru, jsou v využívaná služba vyplněné Microsoft.Compute. Další informace o [modely nasazení Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>InstanceID pole je prázdné, pokud využití virtuálního počítače
Pokud nasazujete prostřednictvím modelu nasazení classic, řetězec InstanceID není k dispozici.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Proč se značky pro svoje virtuální počítače není směřující do podrobnosti o využití?
Značky pouze flow vám použití sdíleného svazku clusteru pro virtuální počítače Resource Manageru pouze. Klasický prostředek značky nejsou k dispozici v podrobnosti o použití.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Jak lze spotřebované množství déle než 24 hodin jeden den?
V modelu Classic se fakturace za prostředky agregují na úrovni Cloudovou službu. Pokud máte více než jeden virtuální počítač v cloudové službě, která používá stejnou účtování počítat, vaše využití se agreguje společně. Virtuální počítače nasazené prostřednictvím Resource Manageru se účtují na úrovni virtuálního počítače, takže tato agregace se nepoužije.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Proč není k dispozici ceny pro velikosti DS/LS/FS nebo GS na stránce s cenami?
Storage úrovně Premium podporuje virtuální počítače se účtují za stejnou sazbu jako jiné úrovně než premium storage podporuje virtuální počítače. Liší se pouze tím náklady na úložiště. Přejděte [stránce s cenami za úložiště](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/) Další informace.

## <a name="next-steps"></a>Další postup
Další informace o vaší podrobnosti o použití najdete v tématu [vysvětlení vašeho vyúčtování služeb Microsoft Azure.](https://docs.microsoft.com/azure/billing/billing-understand-your-bill
)

