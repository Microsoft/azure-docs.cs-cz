---
title: Úvod do virtuálních počítačů Azure Stack
description: Další informace o virtuálních počítačích Azure Stack
services: azure-stack
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.topic: get-started-article
ms.date: 09/05/2018
ms.author: sethm
ms.reviewer: kivenkat
ms.openlocfilehash: 143e6c2856485e33022254abfcf0cf3806b30cef
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44025769"
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>Úvod do virtuálních počítačů Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Azure Stack nabízí virtuální počítače (VM) jako jednoho typu na vyžádání, škálovatelných výpočetních prostředků. Virtuální počítač můžete zvolit, když potřebujete větší kontrolu nad výpočetním prostředí než jiné možnosti. Tento článek obsahuje podrobné informace předtím, než vytvoříte virtuální počítač.

Virtuální počítač Azure Stack nabídne flexibilitu virtualizace bez nutnosti provádět správu clusterů nebo jednotlivých počítačů. Nicméně stále potřebujete k údržbě virtuálního počítače prováděním úloh, jako jsou konfigurace, opravy a instalaci softwaru, na kterém běží na ní.

Virtuální počítače Azure Stack můžete různými způsoby. Příklad:

- **Vývoj a testování**  
    Virtuální počítače Azure Stack nabízí rychlý a snadný způsob, jak vytvořit počítač s konkrétní konfigurací pro kódování a testování aplikací.

- **Aplikace v cloudu**  
    Protože poptávka po aplikaci může kolísat, může mít ekonomický smysl spouštět ji na virtuálním počítači v Azure stacku. Za další virtuální počítače platíte, když je potřebujete, a když ne, tak je vypnete.

- **Rozšířené datové centrum**  
    Virtuální počítače ve virtuální síti Azure Stack můžete snadno připojit k síti vaší organizace nebo do Azure.

Virtuální počítače, které vaše aplikace využívá můžete vertikálně navýšit kapacitu nebo horizontální navýšení kapacity na cokoli, co je potřeba ke splnění vašich potřeb.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Co je třeba zvážit před vytvořením virtuálního počítače?

Stále existují mnoho aspekty návrhu při sestavování infrastruktury aplikace ve službě Azure Stack. Tyto aspekty virtuálního počítače je důležité zvážit předtím, než začnete vytvářet infrastrukturu:

- Názvy prostředků vaší aplikace.
- Velikost virtuálního počítače.
- Maximální počet virtuálních počítačů, které je možné vytvořit.
- Operační systém, na kterém běží virtuální počítač.
- Konfigurace virtuálního počítače po jeho spuštění.
- Související prostředky, které vyžaduje virtuální počítač.

### <a name="naming"></a>Pojmenování

Virtuální počítač má název přiřazený k jeho a má název počítače, který je nakonfigurovaný jako součást operačního systému. Název virtuálního může být až 15 znaků dlouhý.

Pokud použijete k vytvoření disku s operačním systémem Azure Stack, název počítače a název virtuálního počítače jsou stejné. Pokud nahrát a použít vlastní image, která obsahuje dříve nakonfigurovaný operační systém a použijte ji k vytvoření virtuálního počítače, může být jiné názvy. Při nahrávání souboru bitové kopie operačního systému zkontrolujte název počítače a jako název virtuálního počítače stejná jako osvědčený postup.

### <a name="vm-size"></a>Velikost virtuálního počítače

Velikost virtuálního počítače, který používáte se určuje podle úlohy, které chcete spustit. Velikost, kterou vyberete, pak určuje další faktory, jako například výpočetní výkon, paměť a kapacitu úložiště. Azure Stack nabízí širokou škálu velikostí, které podporují mnoho typů použití.

### <a name="vm-limits"></a>Omezení virtuálního počítače

Vaše předplatné má výchozí kvóty na místě, které můžou ovlivnit nasazení velkého počtu virtuálních počítačů pro váš projekt. Aktuální limit jednoho předplatného je 20 virtuálních počítačů na oblast.

### <a name="operating-system-disks-and-images"></a>Disky a image operačních systémů

Virtuální počítače používají virtuální pevné disky (VHD), na které ukládají svůj operační systém (OS) a data. Virtuální pevné disky se používají i pro image, ze kterých si můžete nainstalovat operační systém.
Azure Stack nabízí tržiště pro použití s různými verzemi a typy operačních systémů. Image z Marketplace identifikované vydavatelem image, nabídky, sku a verze (verze je obvykle uvedena jako poslední.)

Následující tabulka ukazuje několik způsobů informace o určité imagi najdete:

|Metoda|Popis|
|---------|---------|
|Portál Azure Stack|Hodnoty se pro vás zadají automaticky, když vyberete image, která se má použít.|
|Azure Stack PowerShell|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|Rozhraní REST API     |[Vypsat vydavatele imagí](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<br>[Vypsat nabídky imagí](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[Obrázek seznam skladových položek](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

Můžete nahrát a použít vlastní image. Pokud tak učiníte, název vydavatele, nabídky a skladové položky nejsou používány.

### <a name="extensions"></a>Rozšíření

Rozšíření virtuálního počítače přidělit virtuálnímu počítači další schopnosti prostřednictvím konfigurace po nasazení a automatizovaných úloh.
Pomocí rozšíření můžete provádět tyto běžné úlohy:

- **Spuštění vlastních skriptů**  
    Rozšíření vlastních skriptů pomáhá konfigurovat úlohy na virtuálním počítači spuštěním vašeho skriptu při zřízení virtuálního počítače.

- **Nasazení a správa konfigurací**  
    Rozšíření Powershellu Desired State Configuration (DSC) vám pomůže nastavit DSC na virtuálním počítači pro správu konfigurací a prostředí.

- **Shromažďování diagnostických dat**  
    Rozšíření Azure Diagnostics vám pomůže nakonfigurovat virtuální počítač pro shromažďování diagnostických dat, která slouží k monitorování stavu vaší aplikace.

### <a name="related-resources"></a>Související prostředky

Prostředky v následující tabulce se používají ve virtuálním počítači a muset existovaly nebo byly vytvořeny při vytvoření virtuálního počítače.


|Prostředek|Požaduje se|Popis|
|---------|---------|---------|
|Skupina prostředků|Ano|Virtuální počítač musí být součástí skupiny prostředků.|
|Účet úložiště|Ne|Virtuální počítač nemá nutné účet úložiště k ukládání svých virtuálních pevných disků, pokud využití služby Managed Disks. <br>Virtuální počítač potřebuje účet úložiště k ukládání svých virtuálních pevných disků, pokud používáte nespravované disky.|
|Virtuální síť|Ano|Virtuální počítač musí být členem virtuální sítě.|
|Veřejná IP adresa|Ne|Virtuální počítač může mít přiřazenou veřejnou IP adresu pro umožnění vzdáleného přístupu.|
|Síťové rozhraní|Ano|Virtuální počítač potřebuje síťové rozhraní ke komunikaci v síti.|
|Datové disky|Ne|Virtuální počítač může zahrnovat datové disky pro rozšíření možností úložiště.|

## <a name="create-your-first-vm"></a>Vytvoření vašeho prvního virtuálního počítače

Máte několik možností pro vytvoření virtuálního počítače. Podle vašeho výběru závisí na vašem prostředí.
Následující tabulka obsahuje informace, které vám pomůžou začít vytvářet svůj virtuální počítač.


|Metoda|Článek|
|---------|---------|
|Portál Azure Stack|Vytvoření virtuálního počítače s Windows pomocí portálu Azure Stack<br>[Vytvoření virtuálního počítače s Linuxem pomocí portálu Azure Stack](azure-stack-quick-linux-portal.md)|
|Šablony|Šablony Azure Quickstart zásobníku jsou umístěné na:<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[Vytvoření virtuálního počítače s Windows pomocí prostředí PowerShell ve službě Azure Stack](azure-stack-quick-create-vm-windows-powershell.md)<br>[Vytvoření virtuálního počítače s Linuxem pomocí prostředí PowerShell ve službě Azure Stack](azure-stack-quick-create-vm-linux-powershell.md)|
|Rozhraní příkazového řádku|[Vytvoření virtuálního počítače s Windows pomocí příkazového řádku ve službě Azure Stack](azure-stack-quick-create-vm-windows-cli.md)<br>[Vytvoření virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku ve službě Azure Stack](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="manage-your-vm"></a>Správa virtuálního počítače

Můžete spravovat virtuální počítače založené na prohlížeči portál, pomocí nástroje příkazového řádku s podporou pro skriptování, nebo přímo prostřednictvím rozhraní API. Mezi obvyklé úlohy správy, které můžete provést jsou:

- Získání informací o virtuálním počítači
- Připojení k virtuálnímu počítači
- Správa dostupnosti
- Provádění zálohování

### <a name="get-information-about-your-vm"></a>Získání informací o virtuálním počítači

V následující tabulce se dozvíte některé způsoby, které získáte informace o virtuálním počítači.


|Metoda|Popis|
|---------|---------|
|Portál Azure Stack|V nabídce centra klikněte na virtuální počítače a potom vyberte virtuální počítač ze seznamu. Na stránce pro virtuální počítač máte přístup k souhrnným informacím, hodnotám nastavení a metrikám monitorování.|
|Azure PowerShell|Správa virtuálních počítačů je podobné jako v Azure a Azure Stack. Další informace o použití prostředí PowerShell najdete v následujícím tématu Azure:<br>[Vytvoření a správa virtuálních počítačů Windows pomocí modulu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)|
|Klientské sady SDK|Pomocí jazyka C# ke správě virtuálních počítačů je podobné jako u Azure a Azure Stack. Další informace naleznete v následujícím tématu Azure:<br>[Vytvoření a správa virtuálních počítačů s Windows v Azure pomocí jazyka C#](https://docs.microsoft.com/azure/virtual-machines/windows/csharp)|

### <a name="connect-to-your-vm"></a>Připojte se ke svému virtuálnímu počítači

Můžete použít **připojit** tlačítko na portálu Azure Stack pro připojení k vašemu virtuálnímu počítači.

## <a name="next-steps"></a>Další postup

- [Důležité informace týkající se virtuálních počítačů v Azure stacku](azure-stack-vm-considerations.md)