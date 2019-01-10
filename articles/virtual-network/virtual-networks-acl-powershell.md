---
title: Spravovat seznamy řízení přístupu koncový bod Azure | Prostředí PowerShell | Classic | Dokumentace Microsoftu
description: Zjistěte, jak spravovat seznamy řízení přístupu pomocí Powershellu
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: c84e40af-f351-4572-b3f0-d572d46bafe7
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: 1f6c14c15d4930902ced642bd02d1d1833e0b361
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54154806"
---
# <a name="manage-endpoint-access-control-lists-using-powershell-in-the-classic-deployment-model"></a>Spravovat seznamy řízení přístupu koncový bod pomocí prostředí PowerShell v modelu nasazení classic
Můžete vytvořit a spravovat síť seznamů řízení přístupu (ACL) pro koncové body pomocí prostředí Azure PowerShell nebo portálu pro správu. V tomto tématu najdete postupy pro seznam ACL běžné úkoly, které můžete absolvovat s použitím prostředí PowerShell. Seznam prostředí Azure PowerShell rutin najdete v části [rutiny pro správu Azure](https://go.microsoft.com/fwlink/?LinkId=317721). Další informace týkající se seznamů ACL, naleznete v tématu [co je seznamu pro řízení přístupu sítě (ACL)?](virtual-networks-acl.md). Pokud chcete spravovat vaše seznamy řízení přístupu pomocí portálu pro správu, najdete v článku [jak nastavit koncové body k virtuálnímu počítači](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Správa seznamů ACL sítě pomocí Azure Powershellu
Rutiny prostředí Azure PowerShell můžete použít k vytvoření, odebírat a konfigurovat (set) sítě seznamů řízení přístupu (ACL). Přidali jsme několik uvedené příklady některých možností konfigurace seznamu ACL portu pomocí Powershellu.

Pokud chcete načíst úplný seznam rutin Powershellu pro seznam ACL, můžete použít jednu z těchto:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Vytvoření seznamu ACL sítě s pravidly, které umožňují přístup ze vzdálené podsítě
Následující příklad ukazuje způsob, jak vytvořit nový seznam ACL, který obsahuje pravidla. Tento seznam ACL se následně použije na koncový bod virtuálního počítače. Pravidla seznamu ACL v následujícím příkladu vám umožní přístup ze vzdálené podsítě. K vytvoření nového seznamu ACL sítě s pravidly povolení pro vzdálené podsítě, otevřete prostředí PowerShell ISE Azure. Zkopírovat a Vložit skript níže, konfigurace skriptu s vlastními hodnotami a potom spusťte skript.

1. Vytvořte nový objekt seznamu ACL sítě.
   
        $acl1 = New-AzureAclConfig
2. Nastavte pravidlo, které povoluje přístup ze vzdálené podsítě. V následujícím příkladu nastavíte pravidlo *100* (který má přednost před pravidlo 200 a vyšší) umožňující vzdálené podsítě *10.0.0.0/8* přístup ke koncovému bodu virtuálního počítače. Hodnoty nahraďte vlastními požadavky na konfiguraci. Název "Konfigurace seznamu ACL SharePoint" by měl být nahrazen popisný název, který chcete volat toto pravidlo.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
3. Další pravidla opakujte rutinu a nahraďte hodnoty vlastními splnit požadavky na konfiguraci. Nezapomeňte změnit pravidla číslo pořadí tak, aby odrážely pořadí, ve kterém chcete pravidla použít. Nižší číslo pravidla má přednost před vyšší číslo.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
4. V dalším kroku můžete buď vytvořit nový koncový bod (Přidat) nebo nastavení seznamu ACL pro existující koncový bod (Set). V tomto příkladu jsme přidáte nový koncový bod virtuálního počítače nazývá "web" a aktualizujte nastavení seznamu ACL koncového bodu virtuálního počítače.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM
5. V dalším kroku kombinace rutiny a spusťte skript. V tomto příkladu rutiny kombinované bude vypadat takto:
   
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

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Odebrat pravidlo seznamu ACL sítě, které povoluje přístup ze vzdálené podsítě
Následující příklad ukazuje způsob, jak odebrat pravidlo seznamu ACL sítě.  Odebrat pravidlo seznamu ACL sítě s pravidly povolení pro vzdálené podsítě, otevřete prostředí PowerShell ISE Azure. Zkopírovat a Vložit skript níže, konfigurace skriptu s vlastními hodnotami a potom spusťte skript.

1. Prvním krokem je získání objektu sítě ACL pro koncový bod virtuálního počítače. Budete pak odebrat pravidlo seznamu ACL. V takovém případě odstraníme ho pomocí ID pravidla. Tato akce pouze odebere pravidlo ID 0 ze seznamu ACL. Seznam ACL objektu nejsou odebrány z koncového bodu virtuálního počítače.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1
2. V dalším kroku je nutné použít objekt sítě ACL pro koncový bod virtuálního počítače a aktualizovat virtuální počítač.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Odebrání seznamu ACL sítě z koncového bodu virtuálního počítače
V některých případech můžete chtít odebrat objekt seznamu ACL sítě z koncového bodu virtuálního počítače. Chcete-li to mohli udělat, otevřete ISE Powershellu Azure. Zkopírovat a Vložit skript níže, konfigurace skriptu s vlastními hodnotami a potom spusťte skript.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Další postup
[Co je seznamu pro řízení přístupu sítě (ACL)?](virtual-networks-acl.md)

