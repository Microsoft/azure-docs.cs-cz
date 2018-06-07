---
title: Úvod do virtuálních počítačů Azure Stack
description: Další informace o virtuálních počítačích Azure zásobníku
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: kivenkat
ms.openlocfilehash: 967fcb86c1bf0c85517bc13c2066ed32e8fa28d9
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604127"
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>Úvod do virtuálních počítačů Azure Stack

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Zásobník Azure nabízí virtuální počítače (VM) jako jeden typ na vyžádání, škálovatelných výpočetních prostředků. Virtuální počítač můžete zvolit, pokud potřebujete větší kontrolu nad výpočetním prostředí než jiné možnosti. Tento článek obsahuje podrobné informace, než vytvoříte virtuální počítač.

Virtuální počítač Azure zásobníku poskytuje flexibilitu virtualizace bez nutnosti spravovat clustery nebo jednotlivé počítače. Však stále musíte udržovat virtuálního počítače provedením úloh, jako je například konfigurace, opravy a instalace softwaru, který běží na něm.

Zásobník Azure virtuální počítače můžete použít různé způsoby. Příklad:

- **Vývoj a testování**  
    Virtuální počítače Azure zásobníku nabízejí rychlý a snadný způsob, jak vytvořit počítač s konkrétní konfigurací pro kódování a testování aplikací.

- **Aplikace v cloudu**  
    Protože vyžádání pro vaši aplikaci můžete kolísá, může mít ekonomický smysl ji spustit na virtuálním počítači v Azure zásobníku. Za další virtuální počítače platíte, když je potřebujete, a když ne, tak je vypnete.

- **Rozšířené datacenter**  
    Virtuální počítače ve virtuální síť Azure zásobníku lze snadno připojit k síti vaší organizace nebo do Azure.

Virtuální počítače, které vaše aplikace používá můžete škálovat nebo horizontální navýšení kapacity pro vše, co je potřeba podle svých potřeb.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Co je třeba zvážit před vytvořením virtuálního počítače?

Stále existují množství aspekty návrhu při sestavování na infrastruktuře aplikace v Azure zásobníku. Tyto aspekty virtuálního počítače jsou důležité pro vezměte v úvahu před zahájením vytváření infrastruktury:

- Názvy prostředků vaší aplikace.
- Velikost virtuálního počítače.
- Maximální počet virtuálních počítačů, které lze vytvořit.
- Operační systém, který spouští virtuální počítač.
- Konfigurace virtuálního počítače po jeho spuštění.
- Související prostředky, které vyžaduje virtuální počítač.

### <a name="naming"></a>Pojmenování

Virtuální počítač má přiřazen název a má název počítače, který je nakonfigurovaný jako součást operačního systému. Název virtuálního může být až 15 znaků dlouhý.

Pokud použijete zásobník Azure k vytvoření disku operačního systému, název počítače a název virtuálního počítače jsou stejné. Názvy nahrát, používat vlastní image, která obsahuje dříve nakonfigurované operačního systému a použít k vytvoření virtuálního počítače, se může lišit. Při nahrávání souboru bitové kopie operačního systému zkontrolujte název počítače a název virtuálního počítače stejný jako osvědčený postup.

### <a name="vm-size"></a>Velikost virtuálního počítače

Velikost virtuálního počítače, který používáte, je dáno se zatížením, které chcete spustit. Velikost, kterou vyberete, pak určuje další faktory, jako například výpočetní výkon, paměť a kapacitu úložiště. Zásobník Azure nabízí širokou škálu velikostí, které podporují mnoho typů použití.

### <a name="vm-limits"></a>Omezení virtuálních počítačů

Vaše předplatné má výchozí kvótami v místě, ke kterému může mít vliv nasazení hodně virtuálních počítačů pro váš projekt. Aktuální limit jednoho předplatného je 20 virtuálních počítačů na oblast.

### <a name="operating-system-disks-and-images"></a>Disky a image operačních systémů

Virtuální počítače používají virtuální pevné disky (VHD), na které ukládají svůj operační systém (OS) a data. Virtuální pevné disky se používají i pro image, ze kterých si můžete nainstalovat operační systém.
Zásobník Azure poskytuje marketplace používat s různými verzemi a typy operačních systémů. Marketplace bitové kopie jsou určeny vydavatele bitové kopie, nabídky, sku a verzi (obvykle verze je zadán jako nejnovější.)

Následující tabulka uvádí některé způsoby, abyste nalezli informace pro bitovou kopii:

|Metoda|Popis|
|---------|---------|
|Portál Azure zásobníku|Hodnoty se pro vás zadají automaticky, když vyberete image, která se má použít.|
|Azure Stack PowerShell|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|Rozhraní REST API     |[Vypsat vydavatele imagí](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<br>[Vypsat nabídky imagí](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[Obrázek seznam SKU](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

Můžete nahrát a používat vlastní image. V takovém případě se nepoužívají název vydavatele, nabídky a sku.

### <a name="extensions"></a>Rozšíření

Rozšíření virtuálního počítače poskytují další možnosti vašeho virtuálního počítače prostřednictvím konfigurace nasazení post a automatizované úlohy.
Pomocí rozšíření můžete provádět tyto běžné úlohy:

- **Spustit vlastní skripty**  
    Rozšíření vlastních skriptů vám pomůže nakonfigurovat úlohy na virtuálním počítači tak, že spustíte skript při zřízení virtuálního počítače.

- **Nasazení a správa konfigurací**  
    Rozšíření prostředí PowerShell požadovaného stavu konfigurace (DSC) vám pomůže nastavit DSC na virtuálním počítači ke správě konfigurace a prostředí.

- **Shromažďování dat diagnostiky**  
    Rozšíření diagnostiky Azure vám pomůže nakonfigurovat virtuální počítač ke shromažďování dat diagnostiky, který slouží k monitorování stavu aplikace.

### <a name="related-resources"></a>Související prostředky

Prostředky v následující tabulce se používají ve virtuálním počítači a musí existovat nebo vytvořit při vytváření virtuálního počítače.


|Prostředek|Požaduje se|Popis|
|---------|---------|---------|
|Skupina prostředků|Ano|Virtuální počítač musí být součástí skupiny prostředků.|
|Účet úložiště|Ano|Virtuální počítač potřebuje účet úložiště k ukládání svých virtuálních pevných disků.|
|Virtuální síť|Ano|Virtuální počítač musí být členem virtuální sítě.|
|Veřejná IP adresa|Ne|Virtuální počítač může mít přiřazenou veřejnou IP adresu pro umožnění vzdáleného přístupu.|
|Síťové rozhraní|Ano|Virtuální počítač potřebuje síťové rozhraní ke komunikaci v síti.|
|Datové disky|Ne|Virtuální počítač může zahrnovat datové disky pro rozšíření možností úložiště.|

## <a name="create-your-first-vm"></a>Vytvoření vašeho prvního virtuálního počítače

Máte několik možností vytvoření virtuálního počítače. Výběr závisí na vašem prostředí.
Následující tabulka obsahuje informace, které vám pomůžou spuštěna, vytvoření virtuálního počítače.


|Metoda|Článek|
|---------|---------|
|Portál Azure zásobníku|Vytvoření virtuálního počítače s Windows pomocí portálu Azure zásobníku<br>[Vytvořit virtuální počítač s Linuxem pomocí portálu Azure zásobníku](azure-stack-quick-linux-portal.md)|
|Šablony|Šablony Azure rychlý start zásobníku jsou umístěné na adrese:<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[Vytvoření virtuálního počítače s Windows pomocí prostředí PowerShell v Azure zásobníku](azure-stack-quick-create-vm-windows-powershell.md)<br>[Vytvořit virtuální počítač s Linuxem pomocí prostředí PowerShell v Azure zásobníku](azure-stack-quick-create-vm-linux-powershell.md)|
|Rozhraní příkazového řádku|[Vytvoření virtuálního počítače s Windows pomocí rozhraní příkazového řádku v Azure zásobníku](azure-stack-quick-create-vm-windows-cli.md)<br>[Vytvořit virtuální počítač s Linuxem pomocí rozhraní příkazového řádku v Azure zásobníku](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="manage-your-vm"></a>Správa virtuálního počítače

Můžete spravovat virtuální počítače založené na prohlížeči portál, pomocí nástroje příkazového řádku s podporou pro skriptování, nebo přímo přes rozhraní API. Některé typické úlohy správy, které můžete provést jsou:

- Získání informací o virtuální počítač
- Připojení k virtuálnímu počítači
- Správa dostupnosti
- Provedení zálohy

### <a name="get-information-about-your-vm"></a>Získat informace o virtuální počítač

Následující tabulka ukazuje některé způsoby, které můžete získat informace o virtuální počítač.


|Metoda|Popis|
|---------|---------|
|Portál Azure zásobníku|V nabídce centra klikněte na virtuální počítače a potom vyberte virtuální počítač ze seznamu. Na stránce pro virtuální počítač máte přístup k přehled informací, monitorování metriky a nastavení hodnoty.|
|Azure PowerShell|Správa virtuálních počítačů je podobný v Azure a Azure zásobníku. Další informace o použití prostředí PowerShell najdete v následujícím tématu Azure:<br>[Vytvoření a správa virtuálních počítačů Windows s modulu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)|
|Klientské sady SDK|Použití jazyka C# ke správě virtuálních počítačů je podobný v Azure a Azure zásobníku. Další informace najdete v následujícím tématu Azure:<br>[Vytvářet a spravovat virtuální počítače Windows v Azure pomocí jazyka C#](https://docs.microsoft.com/azure/virtual-machines/windows/csharp)|

### <a name="connect-to-your-vm"></a>Připojení k virtuálnímu počítači

Můžete použít **Connect** tlačítko na portálu Azure zásobníku se připojit k virtuálnímu počítači.

## <a name="next-steps"></a>Další postup

- [Důležité informace pro virtuální počítače v Azure zásobníku](azure-stack-vm-considerations.md)