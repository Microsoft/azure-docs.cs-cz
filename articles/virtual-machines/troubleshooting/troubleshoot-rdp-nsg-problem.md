---
title: Nelze připojit k virtuálním počítačům Azure, protože RDP port není povolené v NSG | Dokumentace Microsoftu
description: Zjistěte, jak řešit potíže, ve kterém RDP selže z důvodu konfigurace skupiny zabezpečení sítě na webu Azure Portal | Dokumentace Microsoftu
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: c32612c411f275220f549eea79276fa5a7232fd0
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52954608"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Nelze vzdáleně připojit k virtuálnímu počítači protože RDP port není povolené v NSG

Tento článek vysvětluje, jak vyřešit problém, ve kterém nemůžete připojit k virtuálním počítači s Windows Azure (VM) vzhledem k tomu, že ve skupině zabezpečení sítě (NSG) není povolen port protokolu RDP (Remote Desktop).


> [!NOTE] 
> Azure nabízí dva modely nasazení pro vytváření a práci s prostředky: [Resource Manager a classic](../../azure-resource-manager/resource-manager-deployment-model.md). Doporučujeme vám použít model nasazení Resource Manager místo klasického modelu nasazení pro nová nasazení. 

## <a name="symptom"></a>Příznak

Nelze provést připojení RDP k virtuálnímu počítači v Azure, protože není otevřený RDP port ve skupině zabezpečení sítě.

## <a name="solution"></a>Řešení 

Při vytváření nového virtuálního počítače blokován veškerý provoz z Internetu ve výchozím nastavení. 

Pokud chcete povolit port RDP v skupinu zabezpečení sítě, postupujte podle těchto kroků:
1. Přihlaste se k [na webu Azure portal](https://portal.azure.com).
2. V **virtuálních počítačů**, vyberte virtuální počítač, u něhož došlo k potížím. 
3. V **nastavení**vyberte **sítě**. 
4. V **příchozí pravidla portů**, zkontrolujte, zda je správně nastaven port pro protokol RDP. Následuje příklad konfigurace: 

    **Priorita**: 300 </br>
    **Port**: 3389 </br>
    **Název**: Port_3389 </br>
    **Port**: 3389 </br>
    **Protokol**: TCP </br>
    **Zdroj**: žádné </br>
    **Cíle**: žádné </br>
    **Akce**: Povolit </br>

Pokud určíte zdrojovou IP adresu, toto nastavení umožní provoz pouze z konkrétní IP adresu nebo rozsah IP adres pro připojení k virtuálnímu počítači. Ujistěte se, že počítač, který používáte ke spuštění relace protokolu RDP je v rozsahu.

Další informace o skupinách Nsg najdete v tématu [skupinu zabezpečení sítě](../../virtual-network/security-overview.md).

> [!NOTE]
> RDP port 3389 je přístupný Internetu. Proto doporučujeme použít pouze pro tento port doporučuje pro účely testování. Pro produkční prostředí doporučujeme použít síť VPN nebo privátní připojení.

## <a name="next-steps"></a>Další postup

Pokud v NSG je již povolen RDP port, přečtěte si téma [řešení potíží s RDP obecné chybě ve virtuálním počítači Azure](./troubleshoot-rdp-general-error.md).



