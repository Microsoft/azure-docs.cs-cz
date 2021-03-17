---
title: Principy využití virtuálních počítačů Azure
description: Vysvětlení podrobností o využití virtuálních počítačů
services: virtual-machines
documentationcenter: ''
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 07/28/2020
ms.openlocfilehash: ba973bd5609dacf05eca842025d4e828d8a9f841
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550943"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Principy využití virtuálních počítačů Azure
Analýzou dat o využití Azure je možné získat výkonné přehledy o spotřebě – přehledy, které umožňují lepší správu nákladů a přidělování v celé organizaci. Tento dokument poskytuje podrobné podrobně k podrobnostem o spotřebě Azure Compute. Další informace o obecném využití Azure najdete v podrobnostech k [porozumění vaší faktuře](../cost-management-billing/understand/review-individual-bill.md).

## <a name="download-your-usage-details"></a>Stažení podrobností o využití
Začněte [stažením podrobností o použití](../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal). Následující tabulka poskytuje definice a příklady hodnot použití Virtual Machines nasazených prostřednictvím Azure Resource Manager. Tento dokument neobsahuje podrobné informace o virtuálních počítačích nasazených prostřednictvím našeho klasického modelu.


| Pole | Význam | Příklady hodnot | 
|---|---|---|
| Datum využití | Datum, kdy byl prostředek použit | `11/23/2017` |
| Meter ID | Identifikuje službu nejvyšší úrovně, pro kterou toto použití patří.| `Virtual Machines`|
| Meter Sub-Category | Identifikátor fakturovaného měření. <br><br> Pro využití výpočetních hodin je měřičem jednotlivých velikostí virtuálních počítačů a OS (Windows, non-Windows) + region. <br><br> V případě využívání softwaru úrovně Premium existuje měřič pro každý typ softwaru. Většina softwarových imagí úrovně Premium má různé měřiče pro jednotlivé základní velikosti. Další informace najdete na stránce s [cenami COMPUTE](https://azure.microsoft.com/pricing/details/virtual-machines/) .</li></ul>| `2005544f-659d-49c9-9094-8e0aea1be3a5`|
| Meter Name| To je specifické pro každou službu v Azure. U výpočetních prostředků je vždycky "Výpočetní hodiny".| `Compute Hours`|
| Meter Region| Určuje polohu datového centra. U některých služeb vycházejí ceny z umístění datového centra.|  `JA East`|
| Jednotka| Určuje jednotku, po které se služba účtuje. Výpočetní prostředky se účtují za hodinu.| `Hours`|
| Spotřebované| Množství prostředku spotřebovaného za tento den. U výpočetních prostředků účtujeme za každou minutu, po kterou se virtuální počítač spustil po určitou hodinu (až 6 desetinných míst přesnosti).| `1, 0.5`|
| Resource Location  | Určuje datové centrum, ve kterém prostředek běží.| `JA East`|
| Consumed Service | Služba platformy Azure, kterou jste použili.| `Microsoft.Compute`|
| Skupina prostředků | Skupina prostředků, ve které nasazený prostředek běží. Další informace najdete v tématu [přehled Azure Resource Manager.](../azure-resource-manager/management/overview.md)|`MyRG`|
| Instance ID | Identifikátor prostředku. Tento identifikátor obsahuje název prostředku, který jste zadali při jeho vytváření. Pro virtuální počítače bude ID instance obsahovat SubscriptionId, ResourceGroupName a VMName (nebo název sady škálování pro použití sady škálování).| `/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1`<br><br>nebo<br><br>`/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1`|
| Značky| Značka, kterou přiřadíte prostředku. Značky můžete použít k seskupení fakturačních záznamů. Naučte se, jak pomocí rozhraní příkazového [řádku](./tag-cli.md) nebo [prostředí PowerShell](./tag-portal.md) označit Virtual Machines k dispozici pouze pro virtuální počítače s správce prostředků.| `{"myDepartment":"RD","myUser":"myName"}`|
| Další informace | Metadata konkrétních služeb. V případě virtuálních počítačů v poli Další informace vyplníme následující data: <br><br> Obrázek konkrétního typu obrázku, který jste spustili. Úplný seznam podporovaných řetězců najdete níže v části typy imagí.<br><br> Typ služby: velikost, kterou jste nasadili.<br><br> VMName: název vašeho virtuálního počítače. Toto pole se naplní jenom pro virtuální počítače sady škálování. Pokud potřebujete název virtuálního počítače pro virtuální počítače sady škálování, najdete ho ve výše uvedeném řetězci ID instance.<br><br> Položku UsageType: Určuje typ použití, který představuje.<br><br> ComputeHR je využití výpočetních hodin pro příslušný virtuální počítač, jako je Standard_D1_v2.<br><br> ComputeHR_SW je poplatek za software Premium, pokud virtuální počítač používá software Premium, například Microsoft R Server. | Virtual Machines<br>`{"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR"}`<br><br>Virtual Machine Scale Sets<br> `{"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"myVM1", "UsageType":"ComputeHR"}`<br><br>Software Premium<br> `{"ImageType":"","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR_SW"}` |

## <a name="image-type"></a>Typ obrázku
V případě některých imagí v galerii Azure se typ obrázku vyplní v poli Další informace. To umožňuje uživatelům pochopit a sledovat, co mají na svém virtuálním počítači nasazené. Následující hodnoty, které jsou vyplněny v tomto poli na základě bitové kopie, kterou jste nasadili:
- BitRock 
- Kanonický FreeBSD 
- Otevřít logiku 
- Oracle 
- SLES pro SAP 
- SQL Server 14 Preview na Windows Serveru 2012 R2 Preview 
- SUSE
- SUSE Premium
- StorSimple Cloud Appliance 
- Red Hat
- Red Hat pro SAP Business Applications     
- Red Hat pro SAP HANA 
- BYOL klienta Windows 
- BYOL Windows serveru 
- Verze Preview Windows serveru 

## <a name="service-type"></a>Typ služby
Pole typ služby v poli Další informace odpovídá konkrétní velikosti virtuálního počítače, kterou jste nasadili. Ceny virtuálních počítačů služby Premium Storage (založené na jednotkách SSD) a virtuálních počítačů bez úrovně Premium (založené na hard) se účtují stejně. Pokud nasadíte velikost založenou na SSD, jako je třeba standard \_ DS2 \_ v2, zobrazí se ve sloupci měřič Sub-Category velikost (), která není SSD, `Standard\_D2\_v2 VM` a do pole Další informace se nastaví velikost SSD ( `Standard\_DS2\_v2` ).

## <a name="region-names"></a>Názvy oblastí
Název oblasti naplněný v poli umístění prostředku v podrobnostech o použití se liší od názvu oblasti používaného v Azure Resource Manager. Tady je mapování mezi hodnotami oblastí:

| **Název oblasti Správce prostředků** | **Umístění prostředku v podrobnostech o využití** |
|---|---|
| australiaeast |Austrálie – východ|
| australiasoutheast | Austrálie – jihovýchod|
| brazilsouth | Brazílie – jih|
| CanadaCentral | Kanada – střed|
| CanadaEast | Kanada – východ|
| CentralIndia | Indie – střed|
| centralus | USA – střed|
| chinaeast | Čína – východ|
| chinanorth | Čína – sever|
| eastasia | Východní Asie|
| eastus | East US|
| eastus2 | USA – východ 2|
| GermanyCentral | Německo – střed|
| GermanyNortheast | DE severovýchod|
| japaneast | Japonsko – východ|
| japanwest | Japonsko – západ|
| KoreaCentral | Korea – střed|
| KoreaSouth | Korea – jih|
| northcentralus | USA – středosever|
| northeurope | Severní Evropa|
| southcentralus | Středojižní USA|
| southeastasia | Southeast Asia|
| SouthIndia | Indie – jih|
| UKNorth | USA – sever|
| uksouth | Spojené království – jih|
| UKSouth2 | Velká Británie – jih 2|
| ukwest | Spojené království – západ|
| USDoDCentral | US DoD – střed|
| USDoDEast | US DoD – východ|
| USGovArizona | USGov Arizona|
| usgoviowa | USGov Iowa|
| USGovTexas | USGov Texas|
| usgovvirginia | USGov Virginie|
| Středozápadní USA | USA – středozápad|
| westeurope | West Europe|
| WestIndia | Indie – západ|
| westus | USA – západ|
| westus2 | USA – západ 2|


## <a name="virtual-machine-usage-faq"></a>Nejčastější dotazy k využití virtuálních počítačů
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Jaké prostředky se účtují při nasazování virtuálního počítače?    
Virtuální počítače získají náklady na samotný virtuální počítač, veškerý software Premium běžící na virtuálním počítači, disk account\managed úložiště přidružený k virtuálnímu počítači a přenos šířky pásma sítě z virtuálního počítače.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Jak poznám, jestli virtuální počítač používá Zvýhodněné hybridní využití Azure v CSV použití?
Pokud nasazujete pomocí [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/), účtuje se vám sazba za virtuální počítače s jiným systémem než Windows, protože do cloudu přinášíte vlastní licenci. Ve vyúčtování můžete rozlišit, které Správce prostředků virtuální počítače jsou spuštěné Zvýhodněné hybridní využití Azure, protože \_ ve sloupci ImageType mají buď Windows Server BYOL nebo Windows \_ Client BYOL.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Jak jsou základní a standardní typy virtuálních počítačů, které se liší v CSV použití?
K dispozici jsou virtuální počítače Basic i Standard a-Series. Pokud nasadíte základní virtuální počítač, v dílčí kategorii měřiče má řetězec "Basic". Pokud nasadíte virtuální počítač Standard A-Series, zobrazí se velikost virtuálního počítače jako virtuální počítač a1, protože Standard je výchozí. Další informace o rozdílech mezi Basic a Standard najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/virtual-machines/).
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>Co jsou ExtraSmall, malé, střední, velké a ExtraLarge velikosti?
ExtraSmall-ExtraLarge jsou starší názvy pro standard \_ a0 – standard \_ a4. V případě záznamů využívání klasických virtuálních počítačů se může zobrazit tato konvence, pokud jste tyto velikosti nasadili.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Jaký je rozdíl mezi oblastí měřičů a umístěním prostředku?
Oblast měřiče je přidružená k měřiči. U některých služeb Azure, které pro všechny oblasti používají jednu cenu, může být pole oblast měřiče prázdné. Ale vzhledem k tomu, že virtuální počítače mají vyhrazené ceny pro Virtual Machines, toto pole se vyplní. Podobně umístění prostředku pro Virtual Machines je umístění, kde je virtuální počítač nasazený. Oblasti Azure v obou polích jsou stejné, i když můžou mít pro název oblasti odlišnou konvenci řetězců.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Proč je hodnota ImageType prázdná v poli Další informace?
Pole ImageType se naplní jenom pro podmnožinu imagí. Pokud jste nasadili jednu z výše uvedených imagí, je ImageType prázdné.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Proč je VMName v dalších informacích prázdný?
VMName se naplní jenom v poli Další informace pro virtuální počítače v sadě škálování. Pole InstanceID obsahuje název virtuálního počítače pro virtuální počítače, které nejsou v sadě škálování.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>Co v dalších informacích ComputeHR význam v poli položku UsageType?
ComputeHR představuje výpočetní hodinu, která představuje událost využití pro základní náklady na infrastrukturu. Pokud je položku UsageType ComputeHR \_ SW, událost využití představuje poplatek za software Premium pro virtuální počítač.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Návody vědět, jestli se mi účtuje software Premium?
Při prozkoumávání, které image virtuálních počítačů nejlépe vyhovuje vašim potřebám, si nezapomeňte [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute). Obrázek má sazbu plánu softwaru. Pokud pro tuto sazbu vidíte "Free", pro software se neúčtují žádné další náklady. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Jaký je rozdíl mezi Microsoft. ClassicCompute a Microsoft. COMPUTE v spotřebované službě?
Microsoft. ClassicCompute představuje klasické prostředky nasazené prostřednictvím Azure Service Manager. Pokud nasazujete prostřednictvím Správce prostředků, pak se do využívané služby vyplní Microsoft. Compute. Přečtěte si další informace o [modelech nasazení Azure](../azure-resource-manager/management/deployment-models.md).
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Proč je pole InstanceID prázdné pro využití svého virtuálního počítače?
Při nasazení prostřednictvím modelu nasazení Classic není řetězec InstanceID k dispozici.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Proč značky pro moje virtuální počítače nejsou v toku k podrobnostem o využití?
Značky pouze přesměrují pouze do sdíleného svazku clusteru využití pouze pro Správce prostředků virtuální počítače. Klasické značky prostředků nejsou k dispozici v podrobnostech o využití.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Jak může být spotřebované množství delší než 24 hodin po dobu jednoho dne?
V klasickém modelu se fakturace za prostředky agreguje na úrovni cloudové služby. Pokud máte v cloudové službě více než jeden virtuální počítač, který používá stejný měřič fakturace, vaše využití se agreguje dohromady. Virtuální počítače nasazené prostřednictvím Správce prostředků se účtují na úrovni virtuálního počítače, takže tato agregace se nepoužije.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Proč ceny nejsou k dispozici pro velikosti DS/FS/GS/LS na stránce s cenami?
Virtuální počítače podporující Prémiové úložiště se účtují stejnou sazbou jako virtuální počítače podporující úložiště bez Premium. Liší se jenom náklady na úložiště. Další informace najdete na [stránce s cenami za úložiště](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/) .

## <a name="next-steps"></a>Další kroky
Další informace o vašich podrobnostech o využití najdete v tématu [vysvětlení faktury za Microsoft Azure.](../cost-management-billing/understand/review-individual-bill.md)