---
title: Rozdíly a aspekty u virtuálních počítačů ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o rozdíly a aspekty při práci s virtuálními počítači ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 12/19/2018
ms.openlocfilehash: 112e9aa023fb29bd960b61139861db4007c61b4d
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55962243"
---
# <a name="considerations-for-using-virtual-machines-in-azure-stack"></a>Důležité informace týkající se používání virtuálních počítačů ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Virtuální počítače Azure Stack poskytují na vyžádání, škálovatelných výpočetních prostředků. Před nasazením virtuálních počítačů (VM), musíte znát rozdíly mezi funkce virtuálních počítačů dostupných v Azure stacku a Microsoft Azure. Tento článek popisuje tyto rozdíly a identifikuje klíčové faktory týkající se plánování nasazení virtuálních počítačů. Další informace o základní rozdíly mezi Azure Stack a Azure, najdete v článku [klíče aspekty](azure-stack-considerations.md) článku.

## <a name="cheat-sheet-virtual-machine-differences"></a>Tahák: Virtuální počítač rozdíly

| Funkce | Azure (globální) | Azure Stack |
| --- | --- | --- |
| Image virtuálních počítačů | Na webu Azure Marketplace obsahuje obrázky, které můžete použít k vytvoření virtuálního počítače. Zobrazit [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) stránku a zobrazte seznam imagí, které jsou k dispozici na webu Azure Marketplace. | Ve výchozím nastavení nejsou k dispozici žádné Image dostupných v Tržišti Azure Stack. Správce cloudu Azure Stack měli publikovat nebo stažení Image marketplace služby Azure Stack, aby uživatelé mohli používat, je. |
| Velikosti virtuálních počítačů | Azure podporuje širokou škálu velikostí virtuálních počítačů. Další informace o dostupné velikosti a možnosti, naleznete [velikostech virtuálních počítačů Windows](../../virtual-machines/virtual-machines-windows-sizes.md) a [velikostí virtuálních počítačů Linux](../../virtual-machines/linux/sizes.md) témata. | Azure Stack podporuje podmnožinu velikosti virtuálních počítačů, které jsou k dispozici v Azure. Chcete-li zobrazit seznam podporovaných velikostí, najdete [velikostí virtuálních počítačů](#virtual-machine-sizes) části tohoto článku. |
| Kvóty virtuálních počítačů | [Kvóty](../../azure-subscription-service-limits.md#service-specific-limits) jsou nastavené microsoftem. | Správce cloudu Azure Stack musíte přiřadit kvóty, než nabízejí virtuální počítače na svoje uživatele. |
| Rozšíření virtuálních počítačů |Azure podporuje širokou škálu rozšíření virtuálních počítačů. Další informace o dostupných rozšíření, naleznete [funkce a rozšíření virtuálních počítačů](../../virtual-machines/windows/extensions-features.md) článku.| Azure Stack podporuje podmnožinu rozšíření, které jsou k dispozici v Azure a rozšíření mají konkrétní verze. Správce cloudu Azure Stack může zvolit, jaká rozšíření, které budou k dispozici pro uživatele. Chcete-li zobrazit seznam rozšíření, která podporuje, najdete [rozšíření virtuálních počítačů](#virtual-machine-extensions) části tohoto článku. |
| Síť virtuálních počítačů | Veřejné IP adresy přiřazené k tenantovi virtuální počítač jsou přístupné přes Internet.<br><br><br>Azure Virtual Machines má pevný název DNS | Veřejné IP adresy přiřazené k virtuálnímu počítači tenanta jsou přístupné pouze na prostředí Azure Stack Development Kit. Uživatel musí mít přístup k Azure Stack Development Kit prostřednictvím [RDP](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) nebo [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) pro připojení k virtuálnímu počítači, který se vytvoří ve službě Azure Stack.<br><br>Virtuální počítače vytvořené v rámci konkrétní instanci služby Azure Stack mít název DNS na základě hodnoty, který je nakonfigurovaný pomocí Správce cloudu. |
| Úložiště virtuálního počítače | Podporuje [spravované disky.](../../virtual-machines/windows/managed-disks-overview.md) | Spravované disky jsou podporovány ve službě Azure Stack s verzí 1808 a novější. |
| Výkon disků virtuálních počítačů | Závisí na typ a velikost disku. | Závisí na velikosti virtuálního počítače z virtuálního počítače, které disky jsou připojené k odkazování [velikostí virtuálních počítačů, které jsou podporované ve službě Azure Stack](azure-stack-vm-sizes.md) článku.
| Verze rozhraní API | Azure má vždy nejnovější verze rozhraní API pro všechny součásti virtuálního počítače. | Azure Stack podporuje konkrétních služeb Azure a konkrétní verze rozhraní API pro tyto služby. Chcete-li zobrazit seznam podporovaných verzí rozhraní API, přečtěte si [verze rozhraní API](#api-versions) části tohoto článku. |
| Azure Instance Metadata service | Služba Azure Instance Metadata poskytuje informace o spuštěných instancí virtuálních počítačů, které lze použít ke správě nebo konfiguraci virtuálních počítačů.  | Služba Instance metadata se nepodporuje v Azure stacku. |
| Skupinách dostupnosti virtuálních počítačů|Více domén selhání (2 nebo 3 oblasti)<br>Víc aktualizačních domén|Více domén selhání (2 nebo 3 oblasti)<br>Víc aktualizačních domén (až na 20)|
| Škálovací sady virtuálních počítačů|Nepodporuje automatické škálování|Automatické škálování není podporován.<br>Přidáte další instance škálovací sady s použitím webu portal, šablon Resource Manageru nebo prostředí PowerShell. |
| Diagnostika virtuálního počítače | Diagnostika virtuálních počítačů s Linuxem | Diagnostika virtuálních počítačů s Linuxem nejsou podporované ve službě Azure Stack. Při nasazení virtuálního počítače s Linuxem s povolenou diagnostikou virtuálního počítače, nasazení se nezdaří. Nasazení se také nezdaří, pokud povolíte základní metriky virtuálního počítače s Linuxem prostřednictvím nastavení diagnostiky.

## <a name="virtual-machine-sizes"></a>Velikosti virtuálních počítačů

Azure Stack ukládá omezení prostředků, aby se zabránilo za spotřebu prostředků (server místní úrovni a služeb.) Tyto limity zvýšit prostředí tenanta snižuje dopad spotřeby prostředků jiných tenantů.

- Pro sítě odchozího přenosu dat z virtuálního počítače existují omezení šířky pásma na místě. Omezení ve službě Azure Stack jsou stejné jako omezení v Azure.
- Azure Stack pro prostředky úložiště, implementuje omezení vstupně-výstupních operací úložiště, aby se zabránilo základní overconsumption prostředků tenanty pro přístup k úložišti.
- Maximální propustnost každý datový disk pro virtuální počítače s více připojenými datovými disky, je 500 IOPS pro pevné disky a 2300 vstupně-výstupních operací pro jednotky SSD.

V následující tabulce jsou uvedeny virtuálních počítačů, které jsou podporovány ve službě Azure Stack spolu s jejich konfigurace:

| Type           | Velikost          | Rozsah podporovaných velikostí |
| ---------------| ------------- | ------------------------ |
|Obecné účely |Basic A        |[A0 – A4](azure-stack-vm-sizes.md#basic-a)                   |
|Obecné účely |Standardní A     |[A0 – A7](azure-stack-vm-sizes.md#standard-a)              |
|Obecné účely |D-series       |[D1 - D4](azure-stack-vm-sizes.md#d-series)              |
|Obecné účely |Dv2-series     |[D1_v2 - D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|Obecné účely |DS-series      |[DS1 - DS4](azure-stack-vm-sizes.md#dv2-series)            |
|Obecné účely |DSv2-series    |[DS1_v2 - DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|Optimalizované z hlediska paměti|D-series       |[D11 - D14](azure-stack-vm-sizes.md#mo-d)            |
|Optimalizované z hlediska paměti|DS-series      |[DS11 - DS14](azure-stack-vm-sizes.md#mo-ds)|
|Optimalizované z hlediska paměti|Dv2-series     |[D11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|Optimalizované z hlediska paměti|DSv2-series –  |[DS11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |

Velikosti virtuálních počítačů a jejich množství přidružený prostředek jsou konzistentní mezi Azure Stack a Azure. To zahrnuje množství paměti, počet jader a počet/velikost datových disků, které je možné vytvořit. Výkon virtuálních počítačů se stejnou velikostí však závisí na základní vlastnosti konkrétního prostředí Azure Stack.

## <a name="virtual-machine-extensions"></a>Rozšíření virtuálních počítačů

Azure Stack zahrnuje malou sadu rozšíření. Další rozšíření a aktualizace jsou dostupné prostřednictvím webu Marketplace syndikace.

Pomocí následujícího skriptu prostředí PowerShell se načíst seznam rozšíření virtuálních počítačů, které jsou k dispozici ve vašem prostředí Azure Stack:

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

Pokud zřizování rozšíření na nasazení virtuálního počítače trvá příliš dlouho, dejte zřizování vypršení časového limitu namísto pokusu o zastavení procesu uvolnění nebo odstranění virtuálního počítače.

## <a name="api-versions"></a>Verze rozhraní API

Funkce virtuálních počítačů ve službě Azure Stack podporují následující verze rozhraní API:

![Typy prostředků virtuálních počítačů](media/azure-stack-vm-considerations/vm-resoource-types.png)

Následující skript prostředí PowerShell můžete použít k získání verze rozhraní API pro funkce virtuálních počítačů, které jsou k dispozici ve vašem prostředí Azure Stack:

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like “Microsoft.compute”}
```

Seznam podporovaných typů prostředků a verze rozhraní API se mohou lišit, pokud operátor cloudu aktualizuje vaším prostředím Azure Stack na novější verzi.

## <a name="windows-activation"></a>Aktivace Windows

Produkty Windows musí využívat v souladu s užívacích právech k produktu a licenční podmínky společnosti Microsoft. Azure Stack používá [automatická aktivace virtuálního počítače](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) (AVMA) Chcete-li aktivovat systém Windows Server virtuálních počítačů (VM).

- Azure Stack hostitele aktivuje Windows s klíče AVMA pro Windows Server 2016. Všechny virtuální počítače běžet Windows Server 2012 R2 nebo novější se automaticky aktivována.
- Virtuální počítače, které běží Windows Server 2012 nebo starší neaktivují automaticky a musí být aktivován pomocí [klíče k Vícenásobné aktivaci](https://technet.microsoft.com/library/ff793438.aspx). Pokud chcete používat klíče k Vícenásobné aktivaci, je nutné zadat kód product key.

Microsoft Azure používá aktivace prostřednictvím služby správy KLÍČŮ aktivovat virtuální počítače s Windows. Pokud přesunete virtuální počítač ze služby Azure Stack pro Azure a dojde k aktivaci problémy, přečtěte si téma [problémy s aktivací virtuálního počítače Windows Azure Poradce při potížích s](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems). Další informace najdete v [selhání aktivace řešení potíží s Windows na virtuálních počítačích Azure](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/) příspěvek na blogu týmu podpory Azure.

## <a name="next-steps"></a>Další postup

[Vytvoření virtuálního počítače s Windows pomocí prostředí PowerShell ve službě Azure Stack](azure-stack-quick-create-vm-windows-powershell.md)
