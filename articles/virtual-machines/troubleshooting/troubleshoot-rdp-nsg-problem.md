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
ms.openlocfilehash: 27f86660de4f37142829114780ab9c198cb1858d
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088507"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Nejde se vzdáleně připojit k virtuálnímu počítači, protože port RDP není v NSG povolený.

Tento článek vysvětluje, jak vyřešit problém, ve kterém se nemůžete připojit k virtuálnímu počítači s Windows Azure (VM), protože port protokol RDP (Remote Desktop Protocol) (RDP) není ve skupině zabezpečení sítě (NSG) povolený.


> [!NOTE] 
> Azure má dva modely nasazení pro vytváření prostředků a práci s nimi: [Správce prostředků a klasický](../../azure-resource-manager/resource-manager-deployment-model.md). Pro nová nasazení doporučujeme použít model nasazení Správce prostředků místo modelu nasazení Classic. 

## <a name="symptom"></a>Příznak

Nemůžete vytvořit připojení RDP k virtuálnímu počítači v Azure, protože ve skupině zabezpečení sítě není otevřený port RDP.

## <a name="solution"></a>Řešení 

Když vytváříte nový virtuální počítač, bude ve výchozím nastavení blokován veškerý provoz z Internetu. 

Pokud chcete povolit port RDP v NSG, postupujte takto:
1. Přihlaste se k [na webu Azure portal](https://portal.azure.com).
2. V **Virtual Machines**vyberte virtuální počítač, který má problém. 
3. V **Nastavení**vyberte **sítě**. 
4. V části **pravidla portů pro příchozí spojení**ověřte, zda je port pro protokol RDP nastaven správně. Následuje příklad konfigurace: 

    **Priorita**: 300 </br>
    **Port**: 3389 </br>
    **Název**: Port_3389 </br>
    **Port**: 3389 </br>
    **Protokol**: TCP </br>
    **Zdroj**: Any </br>
    **Cílová umístění**: Any </br>
    **Akce**: Allow </br>

Pokud zadáte zdrojovou IP adresu, toto nastavení povoluje provoz jenom z konkrétní IP adresy nebo rozsahu IP adres pro připojení k virtuálnímu počítači. Ujistěte se, že počítač, který používáte ke spuštění relace RDP, je v rozsahu.

Další informace o skupin zabezpečení sítě najdete v tématu [Skupina zabezpečení sítě](../../virtual-network/security-overview.md).

> [!NOTE]
> Port RDP 3389 je přístupný z Internetu. Proto doporučujeme použít tento port pouze pro doporučené testování. Pro produkční prostředí doporučujeme použít síť VPN nebo privátní připojení.

## <a name="next-steps"></a>Další kroky

Pokud je port RDP už v NSG povolený, podívejte se na téma [řešení potíží s obecnou chybou protokolu RDP na virtuálním počítači Azure](./troubleshoot-rdp-general-error.md).



