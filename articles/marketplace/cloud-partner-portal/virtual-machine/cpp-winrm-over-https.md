---
title: Vzdálená správa Windows přes protokol HTTPS pro Azure | Azure Marketplace
description: Vysvětluje, jak nakonfigurovat virtuální počítač založený na Windows hostovaný v Azure tak, aby ho bylo možné vzdáleně spravovat pomocí PowerShellu.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: dsindona
ms.openlocfilehash: 7c799c4a56ee5fc2074e4d644bdbcbc6d2b1ca5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288746"
---
# <a name="windows-remote-management-over-https"></a>Vzdálená správa systému Windows v rámci protokolu HTTPS

Tato část vysvětluje, jak nakonfigurovat virtuální počítač založený na Windows hostovaný v Azure tak, aby ho bylo možné spravovat a nasadit vzdáleně pomocí PowerShellu.  Chcete-li povolit vzdálené komunikace prostředí PowerShell, cílový virtuální ms musí vystavit koncový bod HTTPS vzdálené správy systému Windows (WinRM).  Další informace o vzdálené modus lezení prostředí PowerShell upozornění naleznete v [tématu Spuštění vzdálených příkazů](https://docs.microsoft.com/powershell/scripting/learn/remoting/running-remote-commands).  Další informace o službě WinRM naleznete [v tématu Vzdálená správa systému Windows](https://docs.microsoft.com/windows/desktop/WinRM/portal).

Pokud jste vytvořili virtuální počítač pomocí jednoho z "klasických" přístupů Azure – buď portálu Azure Service Manager Portal, nebo zastaralého [rozhraní API pro správu služeb Azure](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100))– pak se automaticky nakonfiguruje s koncovým bodem WinRM.  Pokud ale vytvoříte virtuální počítač pomocí některého z následujících "moderních" *not* přístupů Azure, nebude váš virtuální počítač nakonfigurován pro WinRM přes PROTOKOL HTTPS.

- Použití [portálu Azure](https://portal.azure.com/), obvykle ze schválené základny, jak je popsáno v části [Vytvoření virtuálního pevného disku kompatibilního s Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd)
- [Použití šablon Azure Resource Manageru](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- Pomocí prostředí příkazů Azure PowerShell nebo Azure CLI.  Příklady najdete v [tématu Úvodní příručka: Vytvoření virtuálního počítače s Windows v Azure s PowerShellem](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) a [Úvodní příručka: Vytvoření virtuálního počítače linuxu pomocí azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

Tento koncový bod WinRM je také nutné spustit certifikační sadu nástrojů pro připojení virtuálního počítače, jak je popsáno v [certifikovat image virtuálního počítače](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm).

Naproti tomu virtuální počítače SB s Linuxem se obvykle spravují vzdáleně pomocí příkazů [Azure CLI](https://docs.microsoft.com/cli/azure) nebo Linux z konzoly SSH.  Azure také poskytuje několik alternativních metod pro [spouštění skriptů ve vašem virtuálním počítači s Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm).  Pro složitější scénáře existuje řada řešení automatizace a integrace, která jsou k dispozici pro virtuální počítače se systémem Windows nebo Linux.


## <a name="configure-and-deploy-with-winrm"></a>Konfigurace a nasazení pomocí služby WinRM

Koncový bod WinRM pro virtuální počítače založené na systému Windows lze nakonfigurovat během dvou různých fází jeho vývoje:

- Během vytváření – během nasazení virtuálního počítače do existujícího virtuálního pevného disku.  To je preferovaný přístup pro nové nabídky.  Tento přístup vyžaduje vytvoření certifikátu Azure, použití dodaných šablon Azure Resource Manager a spuštění přizpůsobených skriptů PowerShellu.
- Po nasazení – na existujícím virtuálním počítači hostovaném v Azure.  Tento přístup použijte, pokud už máte řešení virtuálního počítače nasazené v Azure a potřebujete pro něj povolit vzdálenou správu okna.  Tento přístup vyžaduje ruční změny na portálu Azure a spuštění skriptu na cílovém virtuálním počítači.


## <a name="next-steps"></a>Další kroky
Pokud vytváříte nový virtuální počítač, můžete povolit WinRM během [nasazení virtuálního počítače z jeho Virtuálních pevných netek](./cpp-deploy-vm-vhd.md).  V opačném případě může být winrm povolen v existujícím virtuálním m
