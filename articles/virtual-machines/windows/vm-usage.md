---
title: Pochopení využití virtuálního počítače Azure | Microsoft Docs
description: Pochopení podrobnosti o použití virtuálního počítače
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
ms.openlocfilehash: 9980650008e2d2c2f768dcb3256ea5344119fa13
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31425176"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Porozumění využití virtuálního počítače Azure
Analýzou data o využití Azure, může být získávají efektivní využívání Statistika – přehledy, které umožňují lépe náklady správy a přidělení v celé organizaci. Tento dokument obsahuje podrobné informace do vaší spotřeby podrobnosti Azure Compute. Další informace o využití obecné Azure, přejděte na [pochopení vaše faktura](../../billing/billing-understand-your-bill.md).

## <a name="download-your-usage-details"></a>Stáhnout podrobností o využití
Pokud chcete začít, [stáhnout podrobností o využití](../../billing/billing-download-azure-invoice-daily-usage-date.md). Následující tabulka obsahuje definice a ukázkové hodnoty využití pro virtuální počítače nasazené prostřednictvím Správce Azure Resource Manager. Tento dokument neobsahuje podrobné informace o virtuálních počítačích nasazených pomocí našich klasického modelu.


| Pole             | Význam                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Příklad hodnoty                                                                                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Datum využití         | Datum, kdy byl použit k prostředku.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |  “11/23/2017”                                                                                                                                                                                                                                                                                                                                                     |
| ID měření           | Identifikuje nejvyšší úrovně služby, pro kterou toto použití patří.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | "Virtuální počítače"                                                                                                                                                                                                                                                                                                                                               |
| Podkategorie měření | Identifikátor fakturovaného měření. <ul><li>Pro použití výpočetní hodinu je měření každé velikost virtuálního počítače + operačního systému (Windows, nejsou pro Windows) + oblast.</li><li>Pro použití softwaru Premium je měření pro každý typ softwaru. Většina obrázků softwaru premium je jiný měřidla pro každou základní velikost. Další informace najdete v článku [výpočetní stránce s cenami.](https://azure.microsoft.com/pricing/details/virtual-machines/)</li></ul>                                                                                                                                                                                                                                                                                                                                         | "2005544f-659d-49c9-9094-8e0aea1be3a5"                                                                                                                                                                                                                                                                                                                           |
| Název měření         | To platí pouze pro každou službu v Azure. Pro výpočty je vždy "Výpočetní hodin".                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "Výpočetní čas"                                                                                                                                                                                                                                                                                                                                                  |
| Oblast měření       | Určuje polohu datového centra. U některých služeb vycházejí ceny z umístění datového centra.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |  "Japonsko – východ"                                                                                                                                                                                                                                                                                                                                                       |
| Jednotka               | Určuje jednotku, která služba je účtován v. Výpočetních prostředků se fakturují za hodinu.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "Hours"                                                                                                                                                                                                                                                                                                                                                          |
| Spotřebované           | Množství prostředků, které bylo spotřebováno pro daný den. Pro výpočty budeme účtovat pro každou minutu, kterou virtuální počítač spustil pro danou hodinu (až 6 desetinných míst přesnost).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |    “1”, “0.5”                                                                                                                                                                                                                                                                                                                                                    |
| Umístění prostředku  | Určuje datové centrum, ve kterém prostředek běží.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | "Japonsko – východ"                                                                                                                                                                                                                                                                                                                                                        |
| Spotřebovaná služba   | Služba platformy Azure, který jste použili.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | "Microsoft.Compute"                                                                                                                                                                                                                                                                                                                                              |
| Skupina prostředků     | Skupina prostředků, ve které nasazený prostředek běží. Další informace najdete v tématu [přehled Azure Resource Manageru.](../../azure-resource-manager/resource-group-overview.md)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |    "MyRG"                                                                                                                                                                                                                                                                                                                                                        |
| ID instance        | Identifikátor prostředku. Tento identifikátor obsahuje název prostředku, který jste zadali při jeho vytváření. U virtuálních počítačů Instance ID bude obsahovat ID předplatného, název skupiny prostředků a VMName (nebo název pro použití sady škálování sady škálování).                                                                                                                                                                                                                                                                                                                                                                                                                    | "/ odběry/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx / resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1"<br><br>nebo<br><br>"/ odběry/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx / resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1"                                                                                           |
| Značky               | Značky, který přiřadíte k prostředku. Použití značek k seskupení záznamů fakturace. Zjistěte, jak [značky virtuálních počítačů.](tag.md) To je k dispozici pro virtuální počítače správce prostředků pouze.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | "{"myDepartment":"VP","myUser":"myName"}"                                                                                                                                                                                                                                                                                                                        |
| Další informace    | Metadata specifická pro služby. U virtuálních počítačů naplníme následující v poli Další informace: <ul><li>Obrázek bitové kopie konkrétní typ, který jste spustili. Najít úplný seznam podporovaných řetězců níže v části typy obrázků.</li><li>Service Type: velikost který jste nasadili.</li><li>VMName: název vašeho virtuálního počítače. To je vyplněna pouze pro škálovací sady virtuálních počítačů. Potřebujete-li nastavit název vašeho virtuálního počítače pro více virtuálních počítačů, které můžete najít v Instance ID řetězec výše.</li><li>UsageType: Určuje typ použití, který reprezentuje.<ul><li>ComputeHR je výpočetní hodiny využití pro základní virtuální počítač, jako je Standard_D1_v2.</li><li>ComputeHR_SW je zdarma softwaru premium, pokud virtuální počítač používá premium softwaru, jako je Microsoft R Server.</li></ul></li></ul>    | Virtuální počítače {"ImageType": "Kanonický", "ServiceType": "Standard_DS1_v2", "VMName": "", "UsageType": "ComputeHR"}<br><br>Sady škálování virtuálního počítače {"ImageType": "Kanonický", "ServiceType": "Standard_DS1_v2", "VMName": "myVM1", "UsageType": "ComputeHR"}<br><br>Premium softwaru {"ImageType": "","ServiceType": "Standard_DS1_v2", "VMName": "", "UsageType": "ComputeHR_SW"} |

## <a name="image-type"></a>Typ image
Pro některé obrázky v galerii Azure se v poli Další informace o naplní typ obrázku. To umožňuje uživatelům pochopit a sledovat, co se mají nasadit na jejich virtuální počítač. Hodnoty, které jsou v tomto poli na základě bitové kopie, které jste nasadili naplněny jsou následující:
  - BitRock 
  - Canonical 
  - FreeBSD 
  - Otevřete logiky 
  - Oracle 
  - SLES pro SAP 
  - SQL Server 14 ve verzi Preview v systému Windows Server 2012 R2 Preview 
  - SUSE
  - SUSE Premium
  - StorSimple Cloud Appliance 
  - Red Hat
  - Red Hat pro SAP obchodní aplikace     
  - Red Hat pro SAP HANA 
  - Klient Windows BYOL 
  - Windows Server BYOL 
  - Windows Server Preview 

## <a name="service-type"></a>Typ služby
Pole s typem služby v poli Další informace o odpovídá přesnou velikost virtuálního počítače, které jste nasadili. Storage úrovně Premium (SSD na základě) virtuálních počítačů jsou cenově stejné neprémiové úložiště virtuálních počítačů (založené na HDD). Při nasazování velikost založená na SSD, jako je Standard\_DS2\_v2, najdete v části velikost bez SSD (' standardní\_D2\_v2 virtuální počítač ') ve sloupci dílčí kategorie měření a velikost SSD ("standardní\_DS2\_ v2') v poli Další údaje.

## <a name="region-names"></a>Názvy oblastí
Název oblasti vložené do pole umístění prostředku v podrobnosti o využití se liší od názvu oblasti použít ve Správci prostředků Azure. Tady je mapování mezi hodnotami oblasti:

|    **Název oblasti Resource Manageru**       |    **Umístění prostředků v podrobnosti o použití**    |
|--------------------------|------------------------------------------|
|    australiaeast         |    Austrálie – východ                               |
|    australiasoutheast    |    Austrálie – jihovýchod                          |
|    brazilsouth           |    Brazílie – jih                              |
|    CanadaCentral         |    Kanada – střed                            |
|    CanadaEast            |    Kanada – východ                               |
|    CentralIndia          |    Indie – střed                            |
|    centralus             |    Střed USA                            |
|    chinaeast             |    Čína – východ                            |
|    chinanorth            |    Čína – sever                           |
|    eastasia              |    Východní Asie                             |
|    eastus                |    Východ USA                               |
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
|    uksouth               |    Spojené království – jih                              |
|    UKSouth2              |    Spojené království – jih 2                            |
|    ukwest                |    Spojené království – západ                               |
|    USDoDCentral          |    US DoD – střed                        |
|    USDoDEast             |    US DoD – východ                           |
|    USGovArizona          |    USGov – Arizona                         |
|    usgoviowa             |    USGov (Iowa)                            |
|    USGovTexas            |    USGov – Texas                           |
|    usgovvirginia         |    USGov (Virginie)                        |
|    westcentralus         |    USA – středozápad                       |
|    westeurope            |    Západní Evropa                           |
|    WestIndia             |    Indie – západ                               |
|    westus                |    Západní USA                               |
|    westus2               |    USA – západ 2                             |


## <a name="virtual-machine-usage-faq"></a>Využití virtuálního počítače – nejčastější dotazy
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Při nasazování virtuálního počítače, budou se účtovat prostředky?    
Virtuální počítače pro virtuální počítač, premium software pro virtuální počítač, disk account\managed úložiště, který je přidružený virtuální počítač získat náklady a šířku pásma sítě přenosy z virtuálního počítače.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Jak poznám, jestli virtuální počítač používá Azure hybridní výhody ve sdíleném svazku clusteru využití?
Pokud nasadíte pomocí [Azure hybridní Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/), vzhledem k tomu, že jste do cloudu přináší vlastní licenci, budou se účtovat rychlost virtuálního počítače nejsou pro Windows. Ve vašem vyúčtování možné rozlišit, které správce prostředků virtuální počítače jsou spuštěné Azure hybridní výhody, protože mají buď "Windows\_Server BYOL" nebo "Windows\_klienta BYOL" ve sloupci ImageType.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Jak jsou základní vs. Standardní typy virtuálních počítačů rozlišené ve sdíleném svazku clusteru využití?
Jsou nabízena Basic a Standard virtuální počítače A-Series. Pokud nasadíte základní virtuální počítač, v kategorii dílčí měření obsahuje řetězec "Basic". Pokud nasadíte standardní virtuální počítač A-Series, pak velikost virtuálního počítače se zobrazí jako "Virtuální počítač A1" vzhledem k tomu, že výchozí nastavení je Standard. Další informace o rozdílech mezi Basic a Standard, najdete v článku [stránce s cenami](https://azure.microsoft.com/pricing/details/virtual-machines/).
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>Jaké jsou velikosti: mimořádně malý, malý, střední, velký a mimořádně velký?
ExtraSmall – ExtraLarge jsou starší verze názvy pro standardní\_A0 – Standard\_A4. V classic záznamů využití virtuálních počítačů může se zobrazit tato konvence použít, pokud jste nasadili tyto velikosti.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Jaký je rozdíl mezi oblasti měření a umístění prostředku?
Oblasti měření je přidružen měřidla. U některých služeb Azure, kteří používají jeden ceny pro všechny oblasti může být prázdné pole oblast měření. Ale vzhledem k tomu, že virtuální počítače mají ceny za oblast vyhrazené pro virtuální počítače, toto pole se vyplní. Podobně umístění prostředku pro virtuální počítače, je umístění, kde je nasazen virtuální počítač. Oblast Azure z obou polí jsou stejné, ale mohou mít jiný řetězec názvů pro název oblasti.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Proč je hodnota ImageType prázdné pole Další informace o?
Pole ImageType je vyplněný jenom pro podmnožinu bitové kopie. Pokud jste nenastavili jedné z výše uvedených bitových kopií, záznam ImageType je prázdný.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>VMName je prázdná v další informace
VMName je pouze vložené do pole Další informace pro virtuální počítače ve škálovací sadě. Identifikátor InstanceID pole obsahuje název virtuálního počítače pro jiný škálovací sady virtuálních počítačů.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>Co znamená ComputeHR v poli UsageType v další informace o?
ComputeHR je zkratka pro výpočetní hodiny, která představuje událost využití pro základní infrasturcture náklady. Pokud UsageType ComputeHR\_SW, představuje událost použití poplatků softwaru premium pro virtuální počítač.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Jak poznám, pokud mám mě účtovat premium softwaru?
Při prohlížení které Image virtuálního počítače nejlépe vyhovuje vašim potřebám, nezapomeňte se podívat [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute). Bitová kopie je míra plán softwaru. Pokud se zobrazí "Free" rychlost, je bez dalších nákladů pro software. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Jaký je rozdíl mezi Microsoft.Network a Microsoft.Compute ve službě Potřebováno?
Microsoft.Network představuje klasické prostředky nasazené prostřednictvím portálu Azure Service Manager. Pokud nasazujete prostřednictvím Resource Manager, je ve službě spotřebované naplněný Microsoft.Compute. Další informace o [modelech nasazení Azure](../../azure-resource-manager/resource-manager-deployment-model.md).
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Identifikátor InstanceID pole je prázdné, pokud mého používání virtuálního počítače
Pokud nasadíte pomocí modelu nasazení classic, řetězec Identifikátor InstanceID není k dispozici.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Proč se značky pro není předávaných do podrobnosti o využití virtuálních počítačů?
Značky pouze toku vám CSV využití pro virtuální počítače správce prostředků pouze. Značky Classic prostředku nejsou k dispozici v podrobnosti o využití.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Jak může být spotřebovaného množství víc než 24 hodin jeden den?
V klasickém modelu fakturace pro prostředky se shromažďují na úrovni cloudové služby. Pokud máte více než jeden virtuální počítač v rámci cloudové služby, který používá stejné fakturace měření, je spotřeba agregován společně. Virtuální počítače nasazené prostřednictvím Správce prostředků se fakturují na úrovni virtuálních počítačů, tak tato agregace se nepoužije.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Proč není k dispozici pro velikosti DS nebo služby FS nebo GS/LS v cenových na stránce s cenami?
Storage úrovně Premium podporuje virtuální počítače se účtují stejnou rychlostí jako neprémiové úložiště podporující virtuálních počítačů. Liší se pouze vaše náklady na úložiště. Přejděte [úložiště stránce s cenami](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/) Další informace.

## <a name="next-steps"></a>Další postup
Další informace o vaší podrobnosti o použití najdete v tématu [porozumět vaší faktuře pro Microsoft Azure.](../../billing/billing-understand-your-bill.md)

