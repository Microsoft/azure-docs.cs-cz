---
title: Vzdálená správa Windows přes protokol HTTPS pro Azure | Dokumentace Microsoftu
description: .
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: pbutlerm
ms.openlocfilehash: 99c41dc010ae589cc43c093646fd1c05c1333f7e
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265120"
---
# <a name="windows-remote-management-over-https"></a>Vzdálená správa Windows přes protokol HTTPS

Tato část vysvětluje postup konfigurace virtuálního počítače s hostovaným v Azure, na základě Windows tak, aby ho můžete spravovat a nasazovat vzdáleně pomocí Powershellu.  Pokud chcete povolit vzdálenou komunikaci prostředí PowerShell, cílový virtuální počítač musí vystavit koncový bod HTTPS Vzdálená správa Windows (WinRM).  Další informace o vzdálené komunikace Powershellu najdete v tématu [spouštění vzdálených příkazů](https://docs.microsoft.com/powershell/scripting/core-powershell/running-remote-commands?view=powershell-6).  Další informace týkající se WinRM, naleznete v tématu [Vzdálená správa Windows](https://docs.microsoft.com/windows/desktop/WinRM/portal).

Pokud jste vytvořili virtuální počítač pomocí jednoho z postupů "klasickém" Azure – na portálu Service Manager Azure nebo zastaralá [Azure Service Management API](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100))– je automaticky nakonfigurován s koncovým bodem služby WinRM.  Ale pokud vytvoříte virtuální počítač pomocí kteréhokoli z těchto přístupů "moderní" Azure a pak se váš virtuální počítač *není* nakonfigurovat pro WinRM prostřednictvím protokolu HTTPS.  

- Použití [webu Azure portal](https://portal.azure.com/), typicky ze schválených base, jak je popsáno v části [vytvoření virtuálního pevného disku kompatibilního s Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd)
- [Pomocí šablon Azure Resource Manageru](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- Pomocí příkazového prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure.  Příklady najdete v tématu [rychlý start: Vytvoření virtuálního počítače s Windows v Azure pomocí prostředí PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) a [rychlý start: Vytvoření virtuálního počítače s Linuxem pomocí Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

Tento koncový bod služby WinRM je také potřeba spustit certifikační sadu nástrojů pro připojení virtuálních počítačů, jak je popsáno v [certifikaci vaší image virtuálního počítače](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm).

Naopak, obvykle virtuálních počítačů s Linuxem se vzdáleně spravovat buď pomocí [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure) nebo Linuxové příkazy z konzoly SSH.  Azure poskytuje také několik alternativních metod [spouštět skripty ve virtuálním počítačům s Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm).  Pro složitější scénáře existuje mnoho řešení automatizace a integrace k dispozici pro virtuální počítače s Windows a Linux.


## <a name="configure-and-deploy-with-winrm"></a>Konfigurace a nasazení pomocí WinRM

Koncový bod služby WinRM pro virtuální počítač s Windows můžete nakonfigurovat při dvou různých fázích vývoje:

- Při vytváření – při nasazení virtuálního počítače do existujícího virtuálního pevného disku.  Toto je upřednostňovaný způsob pro nové nabídky.  Tento přístup vyžaduje vytvoření certifikátu Azure pomocí šablon Azure Resource Manageru a případně i přizpůsobené skripty Powershellu pro spuštění. 
- Po nasazení – na existující virtuální počítač hostovaný v Azure.  Tuto metodu použijte, pokud již máte řešení virtuálních počítačů nasazených v Azure a se muset povolit vzdálené správy systému Windows pro něj.  Tento přístup vyžaduje ruční změny webu Azure portal a spuštění skriptu na cílovém virtuálním počítači. 


## <a name="next-steps"></a>Další postup
Pokud vytváříte nový virtuální počítač, můžete povolit WinRM během [nasazení virtuálního počítače z jeho virtuální pevné disky](./cpp-deploy-vm-vhd.md).  V opačném případě můžete v existujícím virtuálním počítači povolená služba WinRM  
