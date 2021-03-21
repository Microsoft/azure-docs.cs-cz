---
title: Přehled virtuálních počítačů s Windows v Azure
description: Přehled virtuálních počítačů s Windows v Azure.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 2432cc49d770471979e089a496b9d0351eec26fd
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550327"
---
# <a name="windows-virtual-machines-in-azure"></a>Virtuální počítače s Windows v Azure

Azure Virtual Machines (VM) je jedním z několika typů [škálovatelných výpočetních prostředků na vyžádání](/azure/architecture/guide/technology-choices/compute-decision-tree), které Azure nabízí. Obvykle zvolíte virtuální počítač, když potřebujete větší kontrolu nad výpočetním prostředí, než nabízí jiné možnosti. Tento článek obsahuje informace o tom, co byste měli zvážit před vytvořením virtuálního počítače, jak ho vytvořit a jak ho spravovat.

Virtuální počítač Azure vám nabídne flexibilitu virtualizace bez nutnosti zakoupení a údržby fyzického hardwaru, na kterém běží. Nevyhnete se však údržbě virtuálního počítače prováděním úloh, jako jsou konfigurace, aplikování oprav chyb a instalace softwaru, který na něm běží.

Virtuální počítače Azure lze použít různými způsoby. Tady je několik příkladů:

* **Vývoj a testování** – Virtuální počítače Azure nabízejí rychlý a snadný způsob, jak vytvořit počítač s konkrétní konfigurací pro kódování a testování aplikací.
* **Aplikace v cloudu** – Protože poptávka po aplikaci může kolísat, z ekonomického hlediska může mít smysl spouštět ji na virtuálním počítači v Azure. Za další virtuální počítače platíte, když je potřebujete, a když ne, tak je vypnete.
* **Rozšířené datové centrum** – Virtuální počítače ve virtuální síti Azure můžete snadno připojit k síti vaší organizace.

Počet virtuálních počítačů, které vaše aplikace používá, lze vertikálně nebo horizontálně navýšit pro splnění vašich požadavků.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Co je třeba zvážit před vytvořením virtuálního počítače?
Při sestavování infrastruktury aplikace v Azure vždy existuje velké množství [aspektů návrhu](/azure/architecture/reference-architectures/n-tier/windows-vm). Než začnete, je důležité zvážit následující aspekty virtuálního počítače:

* Názvy prostředků vaší aplikace
* Umístění, kam se ukládají prostředky
* Velikost virtuálního počítače
* Maximální počet virtuálních počítačů, které lze vytvořit
* Operační systém, který běží na virtuálním počítači
* Konfigurace virtuálního počítače po jeho spuštění
* Související prostředky, které virtuální počítač potřebuje

### <a name="locations"></a>Umístění
Všechny prostředky vytvořené v Azure jsou distribuované mezi několik [geografických oblastí](https://azure.microsoft.com/regions/) po celém světě. Při vytváření virtuálního počítače se oblast obvykle nazývá **umístění**. U virtuálního počítače umístění určuje, kde jsou uloženy virtuální pevné disky.

Tato tabulka ukazuje několik způsobů, jak můžete získat seznam dostupných umístění.

| Metoda | Popis |
| --- | --- |
| portál Azure |Při vytváření virtuálního počítače vyberte umístění ze seznamu. |
| Azure PowerShell |Použijte příkaz [Get-AzLocation](/powershell/module/az.resources/get-azlocation) . |
| REST API |Použijte operaci [Vypsat umístění](/rest/api/resources/subscriptions). |
| Azure CLI |Použijte operaci [az account list-locations](/cli/azure/account). |

## <a name="availability"></a>Dostupnost
Platforma Azure oznámila špičkovou smlouvu o úrovni služeb (SLA) s 99,9% dostupností pro samostatné instance virtuálních počítačů za předpokladu, že virtuální počítač nasadíte se službou Premium Storage pro všechny disky.  Aby se na vaše nasazení vztahovala standardní záruka 99,95% dostupnosti virtuálního počítače podle smlouvy SLA, stále je nutné nasadit alespoň dva virtuální počítače, které vaši úlohu spouští v rámci skupiny dostupnosti. Skupina dostupnosti zajistí, že vaše virtuální počítače jsou distribuované mezi několik domén selhání v datacentrech Azure a také nasazené na hostitele s různými časovými obdobími údržby. Úplná smlouva [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) vysvětluje garantovanou dostupnost Azure jako celku.


## <a name="vm-size"></a>Velikost virtuálního počítače
[Velikost](../sizes.md) virtuálního počítače, který použijete, se určuje podle úlohy, kterou chcete spustit. Velikost, kterou vyberete, pak určuje další faktory, jako například výpočetní výkon, paměť a kapacitu úložiště. Azure nabízí širokou škálu velikostí, které podporují mnoho typů použití.

Azure účtuje [hodinovou cenu](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) na základě velikosti virtuálního počítače a operačního systému. V případě neúplných hodin Azure účtuje jenom využité minuty. Služba Storage je oceněna a účtována samostatně.

## <a name="vm-limits"></a>Omezení virtuálního počítače
Vaše předplatné má nastavené výchozí [kvóty](../../azure-resource-manager/management/azure-subscription-service-limits.md), které mohou ovlivnit nasazení velkého počtu virtuálních počítačů pro váš projekt. Aktuální limit jednoho předplatného je 20 virtuálních počítačů na oblast. Limity můžete navýšit tak, že [vyplníte lístek podpory s žádostí o navýšení](../../azure-portal/supportability/resource-manager-core-quotas-request.md).

### <a name="operating-system-disks-and-images"></a>Disky a image operačních systémů
Virtuální počítače používají [virtuální pevné disky (VHD)](../managed-disks-overview.md) k ukládání jejich operačního systému (OS) a dat. Virtuální pevné disky se používají i pro image, ze kterých si můžete nainstalovat operační systém. 

Azure poskytuje mnoho [imagí na webu Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images%3Bwindows&page=1) s různými verzemi a typy operačních systémů Windows Server, které můžete použít. Image pořízené na webu Marketplace jsou identifikované vydavatelem image, nabídkou, skladovou jednotkou (SKU) a verzí (verze je obvykle uvedena jako poslední). Podporují se jen 64bitové operační systémy. Podrobnosti o podporovaných hostovaných operačních systémech, rolích a funkcích najdete v tématu věnovaném [podpoře serverového softwaru Microsoft pro virtuální počítače Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

Tato tabulka ukazuje několik způsobů, jak můžete najít informace o imagi.

| Metoda | Popis |
| --- | --- |
| portál Azure |Hodnoty se pro vás zadají automaticky, když vyberete image, která se má použít. |
| Azure PowerShell |[Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) – umístění *umístění*<BR>[Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer) *– umístění umístění –* Vydavatel *vydavatele*<BR>[Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) *– umístění umístění –* Vydavatel *vydavatele – nabídka* nabídky  |
| Rozhraní REST API |[Vypsat vydavatele imagí](/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[Vypsat nabídky imagí](/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[Vypsat skladové jednotky (SKU) imagí](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |
| Azure CLI |[AZ VM Image list-Publishers](/cli/azure/vm/image) – *umístění umístění*<BR>[AZ VM Image list-nabídky](/cli/azure/vm/image) -- *Location* Location--Publisher *Publisher*<BR>[AZ VM Image list-SKU](/cli/azure/vm) - *-Location Location--* *Publisher--* Offer *offername*|

Můžete se rozhodnout [nahrát a použít vlastní image](upload-generalized-managed.md). Pokud tak učiníte, název vydavatele, nabídka a skladová jednotka (SKU) se nepoužijí.

### <a name="extensions"></a>Rozšíření
[Rozšíření](../extensions/features-windows.md?toc=/azure/virtual-machines/windows/toc.json) virtuálního počítače poskytují vašemu virtuálnímu počítači další schopnosti prostřednictvím konfigurace po nasazení a automatizovaných úloh.

Pomocí rozšíření můžete provádět tyto běžné úlohy:

* **Spuštění vlastních skriptů** – [Rozšíření vlastních skriptů](../extensions/custom-script-windows.md?toc=/azure/virtual-machines/windows/toc.json) pomáhá konfigurovat úlohy na virtuálním počítači spuštěním vašeho skriptu při zřízení virtuálního počítače.
* **Nasazení a správa konfigurací** – [Rozšíření Konfigurace požadovaného stavu prostředí PowerShell](../extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json) pomáhá nastavit konfiguraci požadovaného stavu na virtuálním počítači za účelem správy konfigurací a prostředí.
* **Shromažďování diagnostických dat** – [Rozšíření Azure Diagnostics](../extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json) pomáhá konfigurovat virtuální počítač pro shromažďování diagnostických dat, která lze použít k monitorování stavu aplikace.

### <a name="related-resources"></a>Související prostředky
Prostředky v této tabulce používá virtuální počítač a je nutné, aby existovaly nebo byly vytvořeny při vytváření virtuálního počítače.

| Prostředek | Povinné | Popis |
| --- | --- | --- |
| [Skupina prostředků](../../azure-resource-manager/management/overview.md) |Yes |Virtuální počítač musí být součástí skupiny prostředků. |
| [Účet úložiště](../../storage/common/storage-account-create.md) |Yes |Virtuální počítač potřebuje účet úložiště k ukládání svých virtuálních pevných disků. |
| [Virtuální síť](../../virtual-network/virtual-networks-overview.md) |Yes |Virtuální počítač musí být členem virtuální sítě. |
| [Veřejná IP adresa](../../virtual-network/public-ip-addresses.md) |No |Virtuální počítač může mít přiřazenou veřejnou IP adresu pro umožnění vzdáleného přístupu. |
| [Síťové rozhraní](../../virtual-network/virtual-network-network-interface.md) |Yes |Virtuální počítač potřebuje síťové rozhraní ke komunikaci v síti. |
| [Datové disky](attach-managed-disk-portal.md) |No |Virtuální počítač může zahrnovat datové disky pro rozšíření možností úložiště. |


## <a name="data-residency"></a>Rezidence dat

Funkce pro ukládání zákaznických dat v rámci Azure je v tuto chvíli dostupná jenom v oblasti jihovýchodní Asie (Singapur) oblasti Asie a Tichomoří geografická a Brazílie – jih (stát Svatý Paulo) Brazílie geograficky. Pro všechny ostatní oblasti jsou zákaznická data uložená v geograficky. Další informace najdete v tématu [Centrum zabezpečení](https://azure.microsoft.com/global-infrastructure/data-residency/).


## <a name="next-steps"></a>Další kroky

Vytvořte svůj první virtuální počítač!

- [Azure Portal](quick-create-portal.md)
- [PowerShell](quick-create-powershell.md)
- [Azure CLI](quick-create-cli.md)
