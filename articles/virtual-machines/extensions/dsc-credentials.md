---
title: Předání přihlašovacích údajů do Azure pomocí konfigurace požadovaného stavu
description: Zjistěte, jak bezpečně předat přihlašovací údaje virtuálním počítačům Azure pomocí konfigurace požadovaného stavu prostředí PowerShell (DSC).
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: Dsc
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: f7edbd0fd8791829a2d9ffaa4e7c0ee0e561cc5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73748968"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Předání přihlašovacích údajů obslužné rutině Azure DSCExtension

Tento článek popisuje rozšíření konfigurace požadovaného stavu (DSC) pro Azure. Přehled obslužné rutiny rozšíření DSC najdete [v tématu Úvod do obslužné rutiny rozšíření konfigurace požadovaného stavu Azure](dsc-overview.md).

 

## <a name="pass-in-credentials"></a>Předání přihlašovacích údajů

V rámci procesu konfigurace může být nutné nastavit uživatelské účty, přistupovat ke službám nebo nainstalovat program v uživatelském kontextu. Chcete-li tyto věci provést, musíte zadat pověření.

Pomocí DSC můžete nastavit parametrizované konfigurace. V parametrizované konfiguraci jsou pověření předána do konfigurace a bezpečně uložena v souborech MOF. Obslužná rutina rozšíření Azure zjednodušuje správu pověření tím, že poskytuje automatickou správu certifikátů.

Následující konfigurační skript DSC vytvoří místní uživatelský účet se zadaným heslem:

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

Je důležité zahrnout **uzel localhost** jako součást konfigurace. Obslužná rutina rozšíření konkrétně hledá příkaz **místního hostitele uzlu.** Pokud tento příkaz chybí, následující kroky nefungují. Je také důležité zahrnout typecast **[PsCredential]**. Tento konkrétní typ aktivuje rozšíření pro šifrování pověření.

Publikování tohoto skriptu do úložiště objektů Blob Azure:

`Publish-AzVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Nastavení rozšíření Azure DSC a zadání přihlašovacích údajů:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzVM -Name 'example-1'

$vm = Set-AzVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzVM
```

## <a name="how-a-credential-is-secured"></a>Jak je pověření zabezpečeno

Spuštění tohoto kódu vyzve k zadání pověření. Po zadání pověření je krátce uložena v paměti. Při publikování pověření pomocí rutiny **Set-AzVMDscExtension** se pověření přenáší prostřednictvím protokolu HTTPS do virtuálního virtuálního trhu. Ve virtuálním počítači Azure ukládá přihlašovací údaje zašifrované na disk pomocí místního certifikátu virtuálního počítače. Pověření je krátce dešifrována v paměti a pak je znovu zašifrována předat DSC.

Tento proces se liší od [použití zabezpečených konfigurací bez obslužné rutiny rozšíření](/powershell/scripting/dsc/pull-server/securemof). Prostředí Azure umožňuje bezpečně přenášet konfigurační data prostřednictvím certifikátů. Při použití obslužné rutiny rozšíření DSC není nutné poskytovat **$CertificatePath** nebo **$CertificateID**/ **$Thumbprint** položku v **ConfigurationData**.

## <a name="next-steps"></a>Další kroky

- Získejte [úvod do obslužné rutiny rozšíření Azure DSC](dsc-overview.md).
- Zkontrolujte [šablonu Správce prostředků Azure pro rozšíření DSC](dsc-template.md).
- Další informace o prostředí PowerShell DSC naleznete v [dokumentačním centru prostředí PowerShell](/powershell/scripting/dsc/overview/overview).
- Další funkce, které můžete spravovat pomocí prostředí PowerShell DSC, a další prostředky DSC naleznete v [galerii prostředí PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).