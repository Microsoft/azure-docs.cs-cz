---
title: Správa seznamů řízení přístupu koncových bodů Azure | PowerShell | Klasický | Microsoft Docs
description: Naučte se spravovat seznamy ACL pomocí PowerShellu.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.assetid: c84e40af-f351-4572-b3f0-d572d46bafe7
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 4fe8349863c16a15886f9f1d33056f0bbfec31f2
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056768"
---
# <a name="manage-endpoint-access-control-lists-using-powershell-in-the-classic-deployment-model"></a>Správa seznamů řízení přístupu koncových bodů pomocí prostředí PowerShell v modelu nasazení Classic
Můžete vytvářet a spravovat seznamy Access Control sítě (ACL) pro koncové body pomocí Azure PowerShell nebo v Portál pro správu. V tomto tématu najdete postupy pro běžné úlohy seznamů ACL, které můžete dokončit pomocí PowerShellu. Seznam rutin Azure PowerShell najdete v tématu rutiny [pro správu Azure](https://go.microsoft.com/fwlink/?LinkId=317721). Další informace o seznamech ACL najdete v tématu [co je seznam ACL (Network Access Control List)?](virtual-networks-acl.md). Pokud chcete spravovat seznamy ACL pomocí Portál pro správu, přečtěte si téma [nastavení koncových bodů na virtuální počítač](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Správa seznamů ACL sítě pomocí Azure PowerShell
Rutiny Azure PowerShell můžete použít k vytvoření, odebrání a konfiguraci (nastavení) seznamů síťových Access Control (ACL). Zahrnuli jsme několik příkladů způsobů, jak můžete nakonfigurovat seznam ACL pomocí PowerShellu.

Pokud chcete načíst úplný seznam rutin PowerShellu pro seznam ACL, můžete použít jednu z následujících možností:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Vytvoření seznamu ACL sítě s pravidly, která povolují přístup ze vzdálené podsítě
Následující příklad znázorňuje způsob vytvoření nového seznamu ACL, který obsahuje pravidla. Tento seznam řízení přístupu se pak použije u koncového bodu virtuálního počítače. Pravidla seznamu ACL v následujícím příkladu budou umožňovat přístup ze vzdálené podsítě. Pokud chcete vytvořit nový seznam ACL sítě s pravidly povolení pro vzdálenou podsíť, otevřete Azure PowerShell ISE. Zkopírujte a vložte níže uvedený skript, nakonfigurujte skript s vlastními hodnotami a potom spusťte skript.

1. Vytvořte nový objekt seznamu ACL sítě.
   
        $acl1 = New-AzureAclConfig
2. Nastavte pravidlo, které povoluje přístup ze vzdálené podsítě. V následujícím příkladu nastavíte pravidlo *100* (které má prioritu vyšší než pravidlo 200 a vyšší), aby byla vzdálená podsíť *10.0.0.0/8* přístupná ke koncovému bodu virtuálního počítače. Nahraďte hodnoty vlastními požadavky na konfiguraci. Název "Konfigurace seznamu ACL pro SharePoint" by měl být nahrazen popisným názvem, který chcete zavolat na toto pravidlo.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
3. Pro další pravidla opakujte rutinu a nahraďte hodnoty vlastními požadavky na konfiguraci. Nezapomeňte změnit pořadí čísel pravidel tak, aby odráželo pořadí, ve kterém chcete pravidla použít. Číslo nižšího pravidla má přednost před vyšším číslem.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
4. V dalším kroku můžete buď vytvořit nový koncový bod (Přidat), nebo nastavit seznam ACL pro existující koncový bod (nastavit). V tomto příkladu přidáme nový koncový bod virtuálního počítače s názvem Web a aktualizujeme koncový bod virtuálního počítače pomocí nastavení seznamu ACL.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM
5. Dále Zkombinujte rutiny a spusťte skript. V tomto příkladu by kombinované rutiny vypadaly takto:
   
        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Odebrání pravidla seznamu ACL sítě, které povoluje přístup ze vzdálené podsítě
Následující příklad znázorňuje způsob, jak odebrat pravidlo seznamu ACL sítě.  Chcete-li odebrat pravidlo seznamu ACL sítě s pravidly povolení pro vzdálenou podsíť, otevřete Azure PowerShell ISE. Zkopírujte a vložte níže uvedený skript, nakonfigurujte skript s vlastními hodnotami a potom spusťte skript.

1. Prvním krokem je získání objektu seznamu ACL sítě pro koncový bod virtuálního počítače. Pak odeberete pravidlo seznamu ACL. V tomto případě ho odebíráme podle ID pravidla. Odebere se jenom ID pravidla 0 ze seznamu ACL. Neodebere objekt ACL z koncového bodu virtuálního počítače.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1
2. Dále je nutné použít objekt seznamu řízení přístupu k síti na koncový bod virtuálního počítače a aktualizovat virtuální počítač.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Odebrání seznamu ACL sítě z koncového bodu virtuálního počítače
V některých scénářích můžete chtít odebrat objekt seznamu ACL sítě z koncového bodu virtuálního počítače. Provedete to tak, že otevřete Azure PowerShell ISE. Zkopírujte a vložte níže uvedený skript, nakonfigurujte skript s vlastními hodnotami a potom spusťte skript.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Další postup
[Co je seznam Access Control sítě (ACL)?](virtual-networks-acl.md)

