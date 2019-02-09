---
title: Předávat přihlašovací údaje do Azure s využitím Desired State Configuration
description: Zjistěte, jak bezpečně předávat přihlašovací údaje pro virtuální počítače Azure pomocí prostředí PowerShell Desired State Configuration (DSC).
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: 6618906f7b1b063de18a4f8a418c1c2744ca1533
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55975780"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Předávat přihlašovací údaje k obslužné rutině Azure DSCExtension

Tento článek se týká rozšíření Desired State Configuration (DSC) pro Azure. Přehled obslužné rutiny rozšíření DSC, naleznete v tématu [Úvod do obslužné rutiny rozšíření Azure Desired State Configuration](dsc-overview.md).

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="pass-in-credentials"></a>Předejte přihlašovacích údajů

Jako součást procesu konfigurace může být potřebujete nastavit uživatelské účty, přístup ke službám, nebo nainstalovat program v kontextu uživatele. To všechno budete muset zadat přihlašovací údaje.

Můžete nastavit parametry konfigurace DSC. V konfiguraci s parametry jsou přihlašovací údaje předané do konfigurace a bezpečně uložené v souborech MOF. Obslužná rutina rozšíření Azure zjednodušuje správu přihlašovacích údajů prostřednictvím automatické správy certifikátů.

Následující skript konfigurací DSC vytvoří místní uživatelský účet pomocí zadaného hesla:

```powershell
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )
    Node localhost {
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        }
    }
}
```

Je důležité zahrnout **uzel localhost** jako součást konfigurace. Obslužná rutina rozšíření konkrétně hledá **uzel localhost** příkazu. Pokud tento příkaz chybí, následující kroky nefungují. Je také důležité zahrnout typecast **[PsCredential]**. Tento konkrétní typ aktivuje rozšíření pro šifrování přihlašovacích údajů.

Chcete-li publikovat tento skript do Azure Blob storage:

`Publish-AzVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Chcete-li nastavit rozšíření DSC Azure a zadejte přihlašovací údaje:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzVM -Name 'example-1'

$vm = Set-AzVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzVM
```

## <a name="how-a-credential-is-secured"></a>Jak je zabezpečené přihlašovací údaje

Spuštěním tohoto kódu zobrazí výzvu k zadání přihlašovacích údajů. Jakmile přihlašovací údaje, které je k dispozici jsou stručně uložená v paměti. Při publikování pověření s použitím **Set-AzVMDscExtension** rutiny, přihlašovací údaje, které se přenášejí prostřednictvím protokolu HTTPS do virtuálního počítače. Na virtuálním počítači Azure ukládá přihlašovací údaje, které šifrují na disku s použitím certifikátu místního virtuálního počítače. Přihlašovací údaje se dešifrují stručně v paměti a potom je znovu zašifrován předat DSC.

Tento proces se liší od [pomocí zabezpečené konfigurace bez obslužné rutiny rozšíření](/powershell/dsc/securemof). Prostředí Azure poskytuje způsob, jak přenášet data konfigurace zabezpečené pomocí certifikátů. Když použijete obslužné rutiny rozšíření DSC, není potřeba zadávat **$CertificatePath** nebo **$CertificateID**/ **$Thumbprint** záznam v **ConfigurationData**.

## <a name="next-steps"></a>Další postup

- Získat [Úvod do obslužné rutiny rozšíření DSC Azure](dsc-overview.md).
- Zkontrolujte [šablony Azure Resource Manageru pro rozšíření DSC](dsc-template.md).
- Další informace o prostředí PowerShell DSC, přejděte [centrum dokumentace k Powershellu](/powershell/dsc/overview).
- Další funkce, které můžete spravovat pomocí prostředí PowerShell DSC a další prostředky DSC, přejděte [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).