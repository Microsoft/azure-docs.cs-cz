---
title: Diagnostika spouštění pro virtuální počítače v Azure | Dokument Microsoft doc
description: Přehled dvou funkcí ladění pro virtuální počítače v Azure
services: virtual-machines
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: fa133e7c62b7970a1c48c7fd86c877bcf699e034
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201599"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>Řešení potíží s virtuálními počítači v Azure pomocí diagnostiky spouštění

Může existovat mnoho důvodů, proč virtuální počítač přejde do stavu bez spouštění. Pokud chcete řešit problémy s virtuálními počítači vytvořenými pomocí modelu nasazení Správce prostředků, můžete použít následující funkce ladění: výstup konzoly a podpora snímků obrazovky pro virtuální počítače Azure. 

Pro virtuální počítače se systémem Linux můžete zobrazit výstup protokolu konzoly z portálu. Pro virtuální počítače s Windows i Linuxem vám Azure umožní zobrazit snímek obrazovky virtuálního počítače z hypervisoru. Obě funkce jsou podporované pro virtuální počítače Azure ve všech oblastech. Poznámka: zobrazení snímků obrazovky a výstupu může trvat až 10 minut, než se zobrazí ve vašem účtu úložiště.

Můžete vybrat možnost **Diagnostika spouštění** a zobrazit protokol a snímek obrazovky.

![Resource Manager](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)

## <a name="common-boot-errors"></a>Běžné chyby spuštění

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [Operační systém nebyl nalezen](https://support.microsoft.com/help/4010142)
- [Chyba při spuštění nebo NEDOSTUPNÉ_SPOUŠTĚCÍ_ZAŘÍZENÍ](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-virtual-machine-created-using-the-azure-portal"></a>Povolení diagnostiky na virtuálním počítači vytvořeném pomocí webu Azure Portal

Následující postup se používá pro virtuální počítač vytvořený pomocí modelu nasazení Správce prostředků.

Na kartě **Správa** v části **monitorování** se ujistěte, že je zapnutá **Diagnostika spouštění** . V rozevíracím seznamu **účet úložiště diagnostiky** vyberte účet úložiště, do kterého se mají umístit diagnostické soubory.
 
![Vytvoření virtuálního počítače](./media/virtual-machines-common-boot-diagnostics/enable-boot-diagnostics-vm.png)

> [!NOTE]
> Funkce diagnostiky spouštění nepodporuje v účtu úložiště úrovně Premium nebo redundantní typy účtů úložiště zóny. Pokud pro diagnostiku spouštění použijete účet Premium Storage, může se při spuštění virtuálního počítače zobrazit chyba StorageAccountTypeNotSupported.
>

### <a name="deploying-from-an-azure-resource-manager-template"></a>Nasazení ze šablony Azure Resource Manager

Pokud nasazujete ze šablony Azure Resource Manager, přejděte k prostředku virtuálního počítače a přidejte část diagnostický profil. Nastavte hlavičku verze rozhraní API na "2015-06-15" nebo novější. Nejnovější verze je "2018-10-01".

```json
{
  "apiVersion": "2018-10-01",
  "type": "Microsoft.Compute/virtualMachines",
  … 
```

Diagnostický profil umožňuje vybrat účet úložiště pro ukládání protokolů.

```json
    "diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[concat('https://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
    }
    }
    }
}
```

Další informace o nasazení prostředků pomocí šablon najdete v tématu [rychlý Start: vytvoření a nasazení Azure Resource Manager šablon pomocí Azure Portal](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Povolit diagnostiku spouštění na stávajícím virtuálním počítači 

Pokud chcete povolit diagnostiku spouštění na stávajícím virtuálním počítači, postupujte podle těchto kroků:

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a potom vyberte virtuální počítač.
2. V části **Podpora a řešení potíží** vyberte **Diagnostika spouštění** a pak vyberte kartu **Nastavení** .
3. V nastavení **Diagnostika spouštění** změňte stav na **zapnuto** a v rozevíracím seznamu **účet úložiště** vyberte účet úložiště. 
4. Uložte změnu.

    ![Aktualizace stávajícího virtuálního počítače](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

### <a name="enable-boot-diagnostics-using-the-azure-cli"></a>Povolení diagnostiky spouštění pomocí Azure CLI

Rozhraní příkazového řádku Azure můžete použít k povolení diagnostiky spouštění na stávajícím virtuálním počítači Azure. Další informace najdete v tématu [AZ VM Boot-Diagnostics](/cli/azure/vm/boot-diagnostics).
