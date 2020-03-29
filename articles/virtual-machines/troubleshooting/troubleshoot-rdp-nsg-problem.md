---
title: Nelze se připojit k virtuálním počítačům Azure, protože port RDP není v souboru zabezpečení sítě povolen | Dokumenty společnosti Microsoft
description: Zjistěte, jak vyřešit problém, ve kterém rdp selže z důvodu konfigurace služby Zabezpečení připojení k zabezpečení na webu Azure Portal | Dokumenty společnosti Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 6c877690cf27edd73f1d828b8a1dda6f4f34e780
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918168"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Vzdáleně se nelze připojit k virtuálnímu virtuálnímu virtuálnímu serveru, protože port RDP není v souboru služby NSG povolen.

Tento článek vysvětluje, jak vyřešit problém, ve kterém se nemůžete připojit k virtuálnímu počítači Azure Windows (VM), protože port RDP (RdP) protokolu RDP není povolen ve skupině zabezpečení sítě (NSG).


## <a name="symptom"></a>Příznak

Nelze vytvořit připojení RDP k virtuálnímu počítači v Azure, protože port RDP není otevřen ve skupině zabezpečení sítě.

## <a name="solution"></a>Řešení 

Když vytvoříte nový virtuální hod, veškerý provoz z Internetu je ve výchozím nastavení blokován. 

Chcete-li povolit port RDP v nsg, postupujte takto:
1. Přihlaste se [k portálu Azure](https://portal.azure.com).
2. Ve **virtuálních počítačích**vyberte virtuální počítač, který má problém. 
3. V **nastavení**vyberte **možnost Síť .** 
4. V **pravidlech příchozího portu**zkontrolujte, zda je port pro prv správně nastaven. Následuje příklad konfigurace: 

    **Priorita**: 300 </br>
    **Název**: Port_3389 </br>
    **Přístav (cíl)**: 3389 </br>
    **Protokol**: TCP </br>
    **Zdroj**: Jakýkoli </br>
    **Destinace**: Všechny </br>
    **Akce**: Povolit </br>

Pokud zadáte zdrojovou ADRESU IP, toto nastavení povolí přenosy pouze z určité adresy IP nebo rozsahu ADRES IP pro připojení k virtuálnímu virtuálnímu počítačům. Ujistěte se, že počítač, který používáte ke spuštění relace RDP, je v rozsahu.

Další informace o skupinách zabezpečení sítě naleznete v tématu [skupina zabezpečení sítě](../../virtual-network/security-overview.md).

> [!NOTE]
> Rdp port 3389 je vystaven internetu. Proto doporučujeme použít tento port pouze pro doporučené pro testování. V produkčních prostředích doporučujeme použít síť VPN nebo soukromé připojení.

## <a name="next-steps"></a>Další kroky

Pokud je port RDP v souboru zabezpečení sítě už povolený, [přečtěte si článek Řešení potíží s obecnou chybou RDP v virtuálním počítači Azure](./troubleshoot-rdp-general-error.md).



