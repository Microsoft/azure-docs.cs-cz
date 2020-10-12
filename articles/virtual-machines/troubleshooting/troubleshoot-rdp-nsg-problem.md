---
title: Nejde se připojit k virtuálním počítačům Azure, protože port RDP není v NSG povolený. | Microsoft Docs
description: Naučte se řešit potíže s tím, že se protokol RDP nezdařil z důvodu konfigurace NSG v Azure Portal | Microsoft Docs
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77918168"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Nejde se vzdáleně připojit k virtuálnímu počítači, protože port RDP není v NSG povolený.

Tento článek vysvětluje, jak vyřešit problém, ve kterém se nemůžete připojit k virtuálnímu počítači s Windows Azure (VM), protože port protokol RDP (Remote Desktop Protocol) (RDP) není ve skupině zabezpečení sítě (NSG) povolený.


## <a name="symptom"></a>Příznak

Nemůžete vytvořit připojení RDP k virtuálnímu počítači v Azure, protože ve skupině zabezpečení sítě není otevřený port RDP.

## <a name="solution"></a>Řešení 

Když vytváříte nový virtuální počítač, bude ve výchozím nastavení blokován veškerý provoz z Internetu. 

Pokud chcete povolit port RDP v NSG, postupujte takto:
1. Přihlaste se k [Azure Portal](https://portal.azure.com).
2. V **Virtual Machines**vyberte virtuální počítač, který má problém. 
3. V **Nastavení**vyberte **sítě**. 
4. V části **pravidla portů pro příchozí spojení**ověřte, zda je port pro protokol RDP nastaven správně. Následuje příklad konfigurace: 

    **Priorita**: 300 </br>
    **Název**: Port_3389 </br>
    **Port (cíl)**: 3389 </br>
    **Protokol**: TCP </br>
    **Zdroj**: Any </br>
    **Cíle**: libovolné </br>
    **Akce**: povolení </br>

Pokud zadáte zdrojovou IP adresu, toto nastavení povoluje provoz jenom z konkrétní IP adresy nebo rozsahu IP adres pro připojení k virtuálnímu počítači. Ujistěte se, že počítač, který používáte ke spuštění relace RDP, je v rozsahu.

Další informace o skupin zabezpečení sítě najdete v tématu [Skupina zabezpečení sítě](../../virtual-network/security-overview.md).

> [!NOTE]
> Port RDP 3389 je přístupný z Internetu. Proto doporučujeme použít tento port pouze pro doporučené testování. Pro produkční prostředí doporučujeme použít síť VPN nebo privátní připojení.

## <a name="next-steps"></a>Další kroky

Pokud je port RDP už v NSG povolený, podívejte se na téma [řešení potíží s obecnou chybou protokolu RDP na virtuálním počítači Azure](./troubleshoot-rdp-general-error.md).



