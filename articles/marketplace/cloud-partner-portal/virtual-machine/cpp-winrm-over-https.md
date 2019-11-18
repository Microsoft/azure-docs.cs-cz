---
title: Vzdálená správa systému Windows přes HTTPS pro Azure | Azure Marketplace
description: Vysvětluje, jak nakonfigurovat virtuální počítač s hostovaným systémem Windows, aby jej bylo možné spravovat vzdáleně pomocí prostředí PowerShell.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: pabutler
ms.openlocfilehash: e39f83b2ed715afbfff69770c151cfc4d527105d
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132255"
---
# <a name="windows-remote-management-over-https"></a>Vzdálená správa systému Windows přes HTTPS

V této části se dozvíte, jak nakonfigurovat virtuální počítač s hostovaným prostředím Azure, aby se mohl vzdáleně spravovat a nasazovat pomocí PowerShellu.  Aby bylo možné povolit vzdálenou komunikaci PowerShellu, cílový virtuální počítač musí vystavit koncový bod HTTPS Vzdálená správa systému Windows (WinRM).  Další informace o vzdálené komunikaci PowerShellu najdete v tématu [spouštění vzdálených příkazů](https://docs.microsoft.com/powershell/scripting/learn/remoting/running-remote-commands).  Další informace o WinRM najdete v tématu [Vzdálená správa systému Windows](https://docs.microsoft.com/windows/desktop/WinRM/portal).

Pokud jste virtuální počítač vytvořili pomocí některého z "klasických" přístupů do Azure – buď portál Azure Service Manager, nebo zastaralý [rozhraní API pro správu služeb Azure](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100)), pak se automaticky nakonfiguruje s koncovým bodem WinRM.  Pokud ale vytvoříte virtuální počítač pomocí některého z následujících "moderních" Azure přístupů, váš virtuální *počítač nebude nakonfigurovaný* pro WINRM přes HTTPS.

- Použití [Azure Portal](https://portal.azure.com/), obvykle od schválené základny, jak je popsáno v části [Vytvoření virtuálního pevného disku kompatibilního s Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd)
- [Používání šablon Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- Použijte Azure PowerShell nebo příkazové prostředí rozhraní příkazového řádku Azure CLI.  Příklady najdete v tématu [rychlý Start: Vytvoření virtuálního počítače s Windows v Azure pomocí PowerShellu](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) a [rychlého startu: Vytvoření virtuálního počítače se systémem Linux pomocí Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

Tento koncový bod WinRM je taky nutný ke spuštění certifikační sady nástrojů pro registraci virtuálního počítače, jak je popsáno v části [certifikace image virtuálního počítače](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm).

Na rozdíl od toho se obvykle vzdálené virtuální počítače se systémem Linux spravují pomocí příkazů [Azure CLI](https://docs.microsoft.com/cli/azure) nebo Linux z konzoly SSH.  Azure také nabízí několik alternativních metod [spouštění skriptů na virtuálním počítači Linux](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm).  U složitějších scénářů je k dispozici několik řešení pro automatizaci a integraci pro virtuální počítače se systémem Windows nebo Linux.


## <a name="configure-and-deploy-with-winrm"></a>Konfigurace a nasazení pomocí WinRM

Koncový bod WinRM pro virtuální počítač se systémem Windows se dá nakonfigurovat během dvou různých fází vývoje:

- Během vytváření – během nasazování virtuálního počítače do existujícího virtuálního pevného disku.  Toto je upřednostňovaný přístup pro nové nabídky.  Tento přístup vyžaduje vytvoření certifikátu Azure pomocí zadaných šablon Azure Resource Manager a spuštění přizpůsobených skriptů PowerShellu.
- Po nasazení – na existujícím virtuálním počítači hostovaném v Azure.  Tento postup použijte, pokud už máte řešení virtuálního počítače nasazené v Azure a potřebujete pro něj Povolit vzdálenou správu systému Windows.  Tento přístup vyžaduje ruční změny v Azure Portal a spuštění skriptu na cílovém virtuálním počítači.


## <a name="next-steps"></a>Další kroky
Pokud vytváříte nový virtuální počítač, můžete WinRM během [nasazování virtuálního počítače z jeho VHD](./cpp-deploy-vm-vhd.md)povolit.  V opačném případě se WinRM dá povolit v existujícím virtuálním počítači.
