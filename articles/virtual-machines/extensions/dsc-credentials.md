---
title: Předávat přihlašovací údaje do Azure pomocí konfigurace požadovaného stavu
description: Zjistěte, jak bezpečně předávat přihlašovací údaje pro virtuální počítače Azure pomocí prostředí PowerShell požadovaného stavu konfigurace (DSC).
services: virtual-machines-windows
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: DSC
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: dacoulte
ms.openlocfilehash: 666253d16ac51dcc21174211f71794f8b0ede07d
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012542"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Přihlašovací údaje předat obslužná rutina Azure DSCExtension

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Tento článek se zabývá rozšíření konfigurace požadovaného stavu (DSC) pro Azure. Přehled obslužná rutina rozšíření DSC najdete v tématu [Úvod do rozšíření obslužné rutiny konfigurace požadovaného stavu Azure](dsc-overview.md).

## <a name="pass-in-credentials"></a>Předejte přihlašovací údaje

Jako součást procesu konfigurace vám může potřebovat nastavení uživatelských účtů, získat přístup ke službám, nebo nainstalujte program v kontextu uživatele. Provádět tyto kroky, budete muset zadat přihlašovací údaje.

Můžete nastavit parametry konfigurace DSC. V konfiguraci parametrizované jsou přihlašovací údaje předané do konfigurace a bezpečně uloženo na soubory MOF. Obslužná rutina rozšíření Azure zjednodušuje správu přihlašovacích údajů prostřednictvím automatické správy certifikátů.

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

Je důležité zahrnout **uzlu localhost** jako součást konfigurace. Obslužná rutina rozšíření konkrétně hledá **uzlu localhost** příkaz. Pokud tento příkaz chybí, následující kroky nefungují. Je také důležité zahrnout typecast **[PsCredential]**. Konkrétního typu aktivuje rozšíření pro šifrování přihlašovacích údajů.

Chcete-li publikovat tento skript do Azure Blob storage:

`Publish-AzureRmVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Nastavit rozšíření Azure DSC a zadejte přihlašovací údaje:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzureRmVM -Name 'example-1'

$vm = Set-AzureRmVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzureRmVM
```

## <a name="how-a-credential-is-secured"></a>Jak jsou zabezpečená pověření

Spuštění tohoto kódu vyzve k zadání pověření. Po přihlašovacích údajů je k dispozici, stručně uložené v paměti. Když je publikována pověření pomocí **Set-AzureRmVMDscExtension** rutiny, přihlašovací údaje se přenášejí přes HTTPS do virtuálního počítače. Ve virtuálním počítači Azure ukládá přihlašovací údaje zašifrovaná na disku pomocí certifikátu, místní počítač. Přihlašovací údaje se stručně dešifruje v paměti, a potom je znovu zašifrován mají být předány DSC.

Tento proces se liší od [pomocí zabezpečené konfigurace bez obslužná rutina rozšíření](/powershell/dsc/securemof). Prostředí Azure poskytuje způsob, jak přenášet data konfigurace bezpečně prostřednictvím certifikátů. Použijete-li obslužná rutina rozšíření DSC, nemusíte poskytují **$CertificatePath** nebo **$CertificateID**/ **$Thumbprint** položku v **ConfigurationData**.

## <a name="next-steps"></a>Další postup

- Získat [Úvod do rozšíření Azure DSC obslužná rutina](dsc-overview.md).
- Zkontrolujte [šablony Azure Resource Manageru pro rozšíření DSC](dsc-template.md).
- Další informace o DSC Powershellu, přejděte na [centru dokumentace prostředí PowerShell](/powershell/dsc/overview).
- Pro další funkce, které můžete spravovat pomocí DSC Powershellu a pro další prostředky DSC, přejděte [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).