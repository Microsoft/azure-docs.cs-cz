---
title: Principy využití virtuálního počítače Azure
description: Vysvětlení podrobností o využití virtuálního počítače
services: virtual-machines
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: ''
tags: azure-virtual-machine
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 12/04/2017
ms.author: memccror
ms.openlocfilehash: fe3c8a3b5d63c67813a5098742392d5658e5c204
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034232"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Principy využití virtuálního počítače Azure
Analýzou dat o využití Azure lze získat výkonné přehledy o spotřebě – přehledy, které mohou umožnit lepší správu nákladů a alokaci v celé organizaci. Tento dokument poskytuje podrobné informace o vaší spotřebě výpočetních prostředků Azure. Další podrobnosti o obecném využití Azure najdete v části [Principy vaší faktury](https://docs.microsoft.com/azure/billing/billing-understand-your-bill).

## <a name="download-your-usage-details"></a>Stažení podrobností o využití
Chcete-li začít, [stáhněte si podrobnosti o použití](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). V následující tabulce jsou uvedeny definice a příklady hodnot využití virtuálních počítačů nasazených prostřednictvím Správce prostředků Azure. Tento dokument neobsahuje podrobné informace pro virtuální chody nasazené prostřednictvím našeho klasického modelu.


| Pole             | Význam                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Příklady hodnot                                                                                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Datum využití         | Datum, kdy byl zdroj použit.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |  "11/23/2017"                                                                                                                                                                                                                                                                                                                                                     |
| ID měření           | Identifikuje službu nejvyšší úrovně, do které toto použití patří.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | "Virtuální počítače"                                                                                                                                                                                                                                                                                                                                               |
| Podkategorie měření | Identifikátor fakturovaného měření. <ul><li>Pro využití výpočetní hodiny je měřič pro každou velikost virtuálního počítače + os (Windows, non-Windows) + oblast.</li><li>Pro použití softwaru Premium je pro každý typ softwaru měřič. Většina prémiových softwarových obrázků má různé měřiče pro každou velikost jádra. Další informace najdete na [stránce S cenami výpočetních prostředků.](https://azure.microsoft.com/pricing/details/virtual-machines/)</li></ul>                                                                                                                                                                                                                                                                                                                                         | "2005544f-659d-49c9-9094-8e0aea1be3a5"                                                                                                                                                                                                                                                                                                                           |
| Název měření         | To je specifické pro každou službu v Azure. Pro výpočetní prostředky je vždy "Výpočetní hodiny".                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "Výpočetní hodiny"                                                                                                                                                                                                                                                                                                                                                  |
| Oblast měření       | Určuje polohu datového centra. U některých služeb vycházejí ceny z umístění datového centra.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |  "JA východ"                                                                                                                                                                                                                                                                                                                                                       |
| Jednotka               | Určuje jednotku, po které se služba účtuje. Výpočetní prostředky se účtují za hodinu.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "Hodiny"                                                                                                                                                                                                                                                                                                                                                          |
| Spotřebované           | Množství zdroje, který byl spotřebován pro tento den. U výpočetních prostředků účtujeme faktury za každou minutu, kdy virtuální počítač běžel za danou hodinu (až 6 desetinných míst přesnosti).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |    "1", "0.5"                                                                                                                                                                                                                                                                                                                                                    |
| Umístění prostředku  | Určuje datové centrum, ve kterém prostředek běží.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | "JA východ"                                                                                                                                                                                                                                                                                                                                                        |
| Spotřebovaná služba   | Služba platformy Azure, kterou jste použili.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | "Microsoft.Compute"                                                                                                                                                                                                                                                                                                                                              |
| Skupina prostředků     | Skupina prostředků, ve které nasazený prostředek běží. Další informace najdete v tématu [Přehled Správce prostředků Azure.](https://docs.microsoft.com/azure/virtual-machines/linux/vm-usage)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |    "MyRG"                                                                                                                                                                                                                                                                                                                                                        |
| ID instance        | Identifikátor prostředku. Tento identifikátor obsahuje název prostředku, který jste zadali při jeho vytváření. U virtuálních počítače bude ID instance obsahovat Id předplatného, Název_resourcegroupname a název vrámci škálování pro použití škálovací sady).                                                                                                                                                                                                                                                                                                                                                                                                                    | "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1"<br><br>– nebo –<br><br>"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1"                                                                                           |
| Značky               | Značka, kterou přiřadíte zdroji. Značky můžete použít k seskupení fakturačních záznamů. Přečtěte si, jak [označit virtuální počítače.](tag.md) To je k dispozici pouze pro virtuální chod správce prostředků.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | "{"myDepartment":"RD","myUser":"myName"}"                                                                                                                                                                                                                                                                                                                        |
| Další informace    | Metadata konkrétních služeb. U virtuálních vás naplníme následující data v poli další informace: <ul><li>Typ obrázku - specifický obrázek, který jste spustili. Úplný seznam podporovaných řetězců najdete níže v části Typy obrázků.</li><li>Typ služby: velikost, kterou jste nasadili.</li><li>VMName: název virtuálního počítače. Toto pole je vyplněno pouze pro virtuální chodškálové sady. Pokud potřebujete název virtuálního počítače pro virtuální počítače škálovací sady, můžete to najít v řetězci ID instance výše.</li><li>UsageType: Určuje typ použití, které představuje.<ul><li>ComputeHR je využití výpočetní hodiny pro základní virtuální počítač, jako je Standard_D1_v2.</li><li>ComputeHR_SW je poplatek za prémiový software, pokud virtuální počítače používá prémiový software, jako je Microsoft R Server.</li></ul></li></ul>    | Virtuální počítače {"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR"}<br><br>Škálovací sady virtuálních strojů {"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"myVM1", "UsageType":"ComputeHR"}<br><br>Premium Software {"ImageType":"","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR_SW"} |

## <a name="image-type"></a>Typ image
U některých bitových kopií v galerii Azure je typ obrázku naplněn v poli Další informace. To umožňuje uživatelům pochopit a sledovat, co nasadili na svém virtuálním počítači. Následující hodnoty, které jsou vyplněny v tomto poli na základě image, kterou jste nasadili:
  - BitRock 
  - Canonical 
  - FreeBSD 
  - Otevřít logiku 
  - Oracle 
  - SLES pro SAP 
  - SQL Server 14 Preview v systému Windows Server 2012 R2 Preview 
  - SUSE
  - SUSE Premium
  - Cloudové zařízení StorSimple 
  - Red Hat
  - Red Hat pro sap obchodní aplikace     
  - Červený klobouk pro SAP HANA 
  - Správce klienta Windows BYOL 
  - Windows Server BYOL 
  - Náhled systému Windows Server 

## <a name="service-type"></a>Typ služby
Pole typu služby v poli Další informace odpovídá přesné velikosti virtuálního počítače, kterou jste nasadili. Virtuální zařízení pro úložiště premium (na bázi SSD) a virtuální chod bez prémiového úložiště (na bázi HDD) jsou oceněny stejně. Pokud nasadíte velikost založenou na\_SSD, jako je standardní DS2\_v2, uvidíte velikost bez SSD ("Standardní\_virtuální počítače D2\_v2") ve sloupci Dílčí kategorie měřiče a velikost SSD ("Standardní\_DS2\_v2") v poli Další informace.

## <a name="region-names"></a>Názvy oblastí
Název oblasti vyplněný v poli Umístění prostředků v podrobnostech o využití se liší od názvu oblasti použitého ve Správci prostředků Azure. Zde je mapování mezi hodnotami oblasti:

|    **Název oblasti správce prostředků**       |    **Umístění zdroje v podrobnostech o využití**    |
|--------------------------|------------------------------------------|
|    australiaeast         |    Austrálie – východ                               |
|    australiasoutheast    |    Austrálie – jihovýchod                          |
|    brazilsouth           |    Brazílie – jih                              |
|    KanadaStřední         |    Kanada – střed                            |
|    KanadaVýchod            |    Kanada – východ                               |
|    Střední Indie          |    Indie – střed                            |
|    centralus             |    USA – střed                            |
|    chinaeast             |    Čína – východ                            |
|    chinanorth            |    Čína – sever                           |
|    eastasia              |    Východní Asie                             |
|    eastus                |    USA – východ                               |
|    eastus2               |    USA – východ 2                             |
|    NěmeckoStřední–        |    DE Centrální                            |
|    NěmeckoSeverovýchod      |    DE Severovýchod                          |
|    japaneast             |    Japonsko – východ                               |
|    japonskozápad             |    Japonsko – západ                               |
|    KoreaStřední          |    Korea – střed                            |
|    KoreaJižní            |    Korea – jih                              |
|    northcentralus        |    USA – středosever                      |
|    northeurope           |    Severní Evropa                          |
|    southcentralus        |    USA – středojih                      |
|    Jihovýchodní Asie         |    Jihovýchodní Asie                        |
|    Jižní Indie            |    Indie – jih                              |
|    Velká BritánieSeverní               |    Severní USA                              |
|    uksouth               |    Spojené království – jih                              |
|    Spojené královstvíJižní2              |    Velká Británie – jih 2                            |
|    ukwest                |    Spojené království – západ                               |
|    USDoDCentral          |    US DoD – střed                        |
|    USDoDEast             |    US DoD – východ                           |
|    USGovArizona          |    USGov Arizona                         |
|    usgoviowa             |    USGov Iowa                            |
|    USGovTexas            |    USGov Texas                           |
|    usgovvirginia         |    USGov Virginie                        |
|    Středozápadní USA         |    USA – středozápad                       |
|    westeurope            |    Západní Evropa                           |
|    Západní Indie             |    Indie – západ                               |
|    westus                |    USA – západ                               |
|    westus2               |    USA – západ 2                             |


## <a name="virtual-machine-usage-faq"></a>Nejčastější dotazy k využití virtuálního počítače
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Jaké prostředky se účtují při nasazování virtuálního ms?    
Virtuální počítače získat náklady na samotný virtuální počítač, jakýkoli prémiový software spuštěný na virtuálním počítači, účet úložiště\spravovaný disk přidružený k virtuálnímu počítači a síťové přenosy šířky pásma z virtuálního počítače.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Jak poznám, jestli virtuální počítač používá hybridní výhody Azure v CSV využití?
Pokud nasadíte pomocí [hybridní výhody Azure](https://azure.microsoft.com/pricing/hybrid-benefit/), bude vám účtována sazba virtuálních zařízení než Windows, protože do cloudu přinášíte vlastní licenci. Ve vaší vyúčtování můžete rozlišit, které virtuální počítače Správce prostředků\_jsou spuštěny Azure\_Hybrid Benefit, protože mají buď "Windows Server BYOL" nebo "Windows Client BYOL" ve sloupci ImageType.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Jak se základní vs standardní typy virtuálních počítačů liší v csv využití?
K dispozici jsou virtuální aplikace Basic i Standard řady A. Pokud nasadíte základní virtuální hod, v dílčí kategorii měřiče má řetězec "Basic". Pokud nasadíte standardní virtuální počítače řady A, velikost virtuálního počítače se zobrazí jako "Virtuální počítače A1", protože standard je výchozí. Další informace o rozdílech mezi základními a standardními informacemi najdete na [stránce Ceny](https://azure.microsoft.com/pricing/details/virtual-machines/).
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>Co jsou extramalé, malé, střední, velké a extravelké velikosti?
ExtraSmall - ExtraLarge jsou starší\_názvy\_pro standard A0 – standard A4. V klasických záznamech o využití virtuálních počítače se může zobrazit tato konvence, pokud jste nasadili tyto velikosti.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Jaký je rozdíl mezi oblastí měřiče a umístěním zdroje?
Oblast měřiče je přidružena k měřiči. U některých služeb Azure, které používají jednu cenu pro všechny oblasti, může být pole Oblast měřiče prázdné. Protože virtuální počítače mají vyhrazené ceny pro virtuální počítače pro oblast, toto pole je naplněna. Podobně umístění prostředků pro virtuální počítače je umístění, kde se nasadí virtuální počítač. Oblasti Azure v obou polích jsou stejné, i když mohou mít jiný řetězec konvence pro název oblasti.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Proč je hodnota ImageType v poli Další informace prázdná?
Pole ImageType je vyplněno pouze pro podmnožinu obrazů. Pokud jste nenasadili jeden z výše uvedených obrázků, ImageType je prázdný.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Proč je název VMName v dalších informacích prázdný?
Název vyvěšený jenom v poli Další informace pro virtuální hody ve škálovací sadě. Pole InstanceID obsahuje název virtuálního montu pro virtuální servery bez škálování.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>Co znamená ComputeHR v poli UsageType v dalších informacích?
ComputeHR je zkratka pro Compute Hour, která představuje událost využití pro základní náklady na infrastrukturu. Pokud UsageType je\_ComputeHR SW, událost využití představuje poplatek za prémiový software pro virtuální počítač.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Jak poznám, že se mi účtuje prémiový software?
Při zkoumání image virtuálního počítače, která nejlépe vyhovuje vašim potřebám, nezapomeňte se podívat na [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute). Obrázek má rychlost plánu softwaru. Pokud vidíte "Zdarma" pro sazbu, není žádné další náklady na software. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Jaký je rozdíl mezi Microsoft.ClassicCompute a Microsoft.Compute ve službě Spotřebované?
Microsoft.ClassicCompute představuje klasické prostředky nasazené prostřednictvím Správce služeb Azure. Pokud nasadíte prostřednictvím Správce prostředků, pak Microsoft.Compute je naplněna v spotřebované služby. Další informace o [modelech nasazení Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Proč je pole InstanceID prázdné pro použití virtuálního počítače?
Pokud nasadíte prostřednictvím modelu klasické nasazení, řetězec InstanceID není k dispozici.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Proč značky pro moje virtuální počítače nepřejdou do podrobností o využití?
Značky tok pouze k vám využití CSV pro virtuální počítače Správce prostředků pouze. Klasické značky prostředků nejsou k dispozici v podrobnostech o použití.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Jak může být spotřebované množství delší než 24 hodin denně?
V modelu Classic se fakturace prostředků agreguje na úrovni cloudové služby. Pokud máte více než jeden virtuální počítač v cloudové službě, která používá stejný měřič fakturace, vaše využití se agreguje společně. Virtuální aplikace nasazené prostřednictvím Správce prostředků se účtují na úrovni virtuálních služeb, takže tato agregace se nepoužije.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Proč nejsou ceny na stránce s cenami k dispozici pro velikosti DS/FS/GS/LS?
Virtuální zařízení s podporou úložiště s podporou úložiště se účtují stejnou sazbou jako virtuální zařízení s kapacitou neprémiového úložiště. Liší se pouze náklady na úložiště. Další informace najdete na [stránce s cenami úložiště.](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/)

## <a name="next-steps"></a>Další kroky
Další informace o podrobnostech o využití najdete v [tématu Vysvětlení faktury za Microsoft Azure.](https://docs.microsoft.com/azure/billing/billing-understand-your-bill
)

