---
title: Protože RDP port není povolené v NSG se nemůže připojit k Azure Virtual Machines | Dokumentace Microsoftu
description: Zjistěte, jak řešit potíže, ve kterém RDP selže z důvodu konfigurace skupiny zabezpečení sítě na webu Azure portal | Dokumentace Microsoftu
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 89af30533e10df0968b60039d7ea15886e89bc25
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52266757"
---
#  <a name="cannot-rdp-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Nelze provést připojení RDP k virtuálnímu počítači, protože RDP port není povolené v NSG

Tento článek ukazuje, jak vyřešit problém, ve kterém nemůže připojit k Azure Windows Virtual Machines (VM), protože port není povolená ve skupině zabezpečení sítě.


> [!NOTE] 
> Azure nabízí dva různé modely nasazení pro vytváření a práci s prostředky: [nástroj Resource Manager a klasický režim](../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Resource Manageru, který vám doporučujeme používat pro nová nasazení namísto modelu nasazení classic. 

## <a name="symptom"></a>Příznak

Nelze provést protokol RDP (Remote Desktop) připojení k virtuálnímu počítači v Azure z důvodu RDP port není otevřený ve skupině zabezpečení sítě.

## <a name="solution"></a>Řešení 

Při vytváření nového virtuálního počítače blokován veškerý provoz z Internetu ve výchozím nastavení. 

Pokud chcete povolit port RDP ve skupině zabezpečení sítě, postupujte podle těchto kroků:
1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V **virtuálních počítačů**, virtuální počítač, který má problém. 
3. V **nastavení**vyberte **sítě**. 
4. V **příchozí pravidla portů**, zaškrtněte, pokud je port pro protokol RDP nastaven správně. Tady je ukázka konfigurace. 

    **Priorita**: 300 </br>
    **Port**: 3389 </br>
    **Název**: Port_3389 </br>
    **Port**: 3389 </br>
    **Protokol**: TCP </br>
    **Zdroj**: žádné </br>
    **Cíle**: žádné </br>
    **Akce**: Povolit </br>

V určíte zdrojovou IP adresu, toto nastavení umožní provoz pouze z konkrétní IP nebo rozsah IP adres pro připojení k virtuálnímu počítači. Ujistěte se, že počítač, který použijete k inicializaci relace protokolu RDP je v rozsahu.

Další informace o skupině zabezpečení sítě najdete v tématu [skupinu zabezpečení sítě](../../virtual-network/security-overview.md).

> [!NOTE]
> RDP port 3389 je přístupný Internetu. To se doporučuje jen pro účely testování. Pro produkční prostředí doporučujeme použít síť VPN nebo privátní připojení.

## <a name="next-steps"></a>Další postup

Pokud ve skupině zabezpečení sítě je již povolen RDP port, přečtěte si téma [řešení potíží s RDP obecné chybě ve virtuálním počítači Azure](./troubleshoot-rdp-general-error.md).



