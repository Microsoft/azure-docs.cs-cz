---
title: Správa seznamů řízení přístupu koncového bodu Azure | Prostředí PowerShell | Classic | Microsoft Docs
description: Naučte se spravovat seznamy ACL v prostředí PowerShell
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
ms.openlocfilehash: c3476908447380ccd7e8b9c0f1c2a55ae763cc1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23928850"
---
# <a name="manage-endpoint-access-control-lists-using-powershell-in-the-classic-deployment-model"></a>Správa seznamů řízení přístupu koncový bod pomocí prostředí PowerShell v modelu nasazení classic
Můžete vytvořit a spravovat sítě seznamy řízení přístupu (ACL) pro koncové body pomocí prostředí Azure PowerShell nebo na portálu Management Portal. V tomto tématu najdete postupy pro seznam ACL běžné úkoly, které můžete dokončit pomocí prostředí PowerShell. Seznam prostředí Azure PowerShell rutin najdete v části [rutiny pro správu Azure](http://go.microsoft.com/fwlink/?LinkId=317721). Další informace o seznamy řízení přístupu najdete v tématu [co je seznamu pro řízení přístupu sítě (ACL)?](virtual-networks-acl.md). Pokud chcete spravovat vaše seznamy ACL pomocí portálu pro správu, najdete v části [jak nastavit koncové body k virtuálnímu počítači](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Spravovat seznamy ACL sítě pomocí prostředí Azure PowerShell
Můžete použít rutiny prostředí Azure PowerShell k vytváření, odebrat a konfigurovat (set) sítě seznamy řízení přístupu (ACL). Jsme zahrnuli několik příkladů způsoby, kterými můžete nakonfigurovat seznam ACL pomocí prostředí PowerShell.

Načtení úplný seznam rutin prostředí PowerShell seznamu ACL, můžete použít jednu z těchto věcí:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Vytvoření seznamu ACL sítě pomocí pravidel, které umožňují přístup ze vzdálené podsíti
Následující příklad ukazuje způsob, jak vytvořit nový seznamu ACL, který obsahuje pravidla. Tento seznam ACL se pak použije pro koncový bod virtuálního počítače. Pravidla seznamu ACL v následujícím příkladu povolí přístup ze vzdálené podsíti. Chcete-li vytvořit nové sítě ACL s povolení pravidla pro vzdálené podsíti, otevřete Azure PowerShell ISE. Zkopírujte a vložte níže, konfigurace skriptu s vlastními hodnotami skript a spusťte skript.

1. Vytvořte nový objekt seznamu ACL sítě.
   
        $acl1 = New-AzureAclConfig
2. Nastavte pravidlo, která umožňuje přístup ze vzdálené podsíti. V následujícím příkladu můžete nastavit pravidlo *100* (která má přednost před pravidlo 200 a vyšší) umožňující vzdálené podsíti *10.0.0.0/8* přístup k koncový bod virtuálního počítače. Nahraďte hodnoty vlastními požadavky na konfiguraci. Název "Konfigurace služby SharePoint ACL" by měl být nahrazen popisný název, který chcete volat toto pravidlo.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
3. Pro další pravidla opakujte rutinu a nahraďte hodnoty s vlastní požadavky na konfiguraci. Nezapomeňte změnit pravidlo číslo pořadí tak, aby odrážela pořadí, ve kterém chcete pravidla, která má být použita. Nižší číslo pravidla mají přednost před vyšší číslo.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
4. Dále můžete buď vytvořit nový koncový bod (Přidat) nebo nastavit seznam řízení přístupu pro existující koncový bod (Set). V tomto příkladu jsme přidáte nový koncový bod virtuálního počítače názvem "web" a aktualizovat koncový bod virtuálního počítače s nastavením seznamu ACL.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM
5. V dalším kroku kombinace rutiny a spusťte skript. V tomto příkladu rutiny kombinované bude vypadat takto:
   
        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "Sharepoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Odebrat pravidlo seznamu ACL sítě, která umožňuje přístup ze vzdálené podsíti
Následující příklad ukazuje způsob, jak odebrat pravidlo seznamu ACL sítě.  Chcete-li odebrat pravidlo seznamu ACL sítě s povolení pravidla pro vzdálené podsíti, otevřete Azure PowerShell ISE. Zkopírujte a vložte níže, konfigurace skriptu s vlastními hodnotami skript a spusťte skript.

1. Prvním krokem je GET pro objekt sítě ACL pro koncový bod virtuálního počítače. Budete pak odeberte pravidlo seznamu ACL. V takovém případě jsme se odebrat ji podle ID pravidla. ID pravidla 0 tato akce odebere pouze ze seznamu ACL. Objekt seznamu ACL nejsou odebrány z koncový bod virtuálního počítače.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1
2. Dále musíte použít objekt sítě ACL pro koncový bod virtuálního počítače a aktualizovat virtuální počítač.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Odeberte seznam ACL sítě z koncový bod virtuálního počítače
V některých případech můžete chtít odebrat objekt seznamu ACL sítě z koncový bod virtuálního počítače. K tomu, otevřete PowerShell ISE Azure. Zkopírujte a vložte níže, konfigurace skriptu s vlastními hodnotami skript a spusťte skript.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Další kroky
[Co je seznamu pro řízení přístupu sítě (ACL)?](virtual-networks-acl.md)

