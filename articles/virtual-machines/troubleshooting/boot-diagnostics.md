---
title: Diagnostika spouštění virtuálních počítačů v Azure | Dokument Společnosti Microsoft
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
ms.openlocfilehash: fe2427d008b49daa6222ca981994f0dc2fbea355
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476582"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>Jak používat diagnostiku spouštění k řešení potíží s virtuálními počítači v Azure

Může existovat mnoho důvodů, proč virtuální počítač přejde do stavu bez spuštění. Chcete-li řešit problémy s virtuálními počítači vytvořenými pomocí modelu nasazení Správce prostředků, můžete použít následující funkce ladění: Konzolový výstup a Podpora snímků obrazovky pro virtuální počítače Azure. 

Pro virtuální počítače s Linuxem můžete zobrazit výstup protokolu konzoly z portálu. Pro virtuální počítače s Windows i Linux, Azure umožňuje zobrazit snímek obrazovky virtuálního počítače z hypervisoru. Obě funkce jsou podporované pro virtuální počítače Azure ve všech oblastech. Upozorňujeme, že zobrazení snímků obrazovky a výstupu může trvat až 10 minut, než se zobrazí ve vašem účtu úložiště.

Chcete-li zobrazit protokol a snímek obrazovky, můžete vybrat možnost **Diagnostika spouštění.**

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

## <a name="enable-diagnostics-on-a-virtual-machine-created-using-the-azure-portal"></a>Povolení diagnostiky na virtuálním počítači vytvořeném pomocí portálu Azure Portal

Následující postup je určen pro virtuální počítač vytvořený pomocí modelu nasazení Správce prostředků.

Na kartě **Správa** v části **Monitorování** zkontrolujte, zda je zapnutá **diagnostika spouštění.** V rozevíracím seznamu **Účet úložiště diagnostiky** vyberte účet úložiště, do kterého chcete umístit diagnostické soubory.
 
![Vytvoření virtuálního počítače](./media/virtual-machines-common-boot-diagnostics/enable-boot-diagnostics-vm.png)

> [!NOTE]
> Funkce Diagnostika spouštění nepodporuje účet úložiště premium. Pokud použijete účet úložiště premium pro diagnostiku spouštění, může se při spuštění virtuálního virtuálního zařízení zobrazit chyba StorageAccountTypeNotSupported.
>

### <a name="deploying-from-an-azure-resource-manager-template"></a>Nasazení ze šablony Správce prostředků Azure

Pokud nasazujete ze šablony Azure Resource Manager, přejděte na prostředek virtuálního počítače a přidejte část profilu diagnostiky. Nastavte záhlaví verze rozhraní API na "2015-06-15" nebo novější. Nejnovější verze je "2018-10-01".

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

Další informace o nasazení prostředků pomocí šablon najdete v [tématu Úvodní příručka: Vytvoření a nasazení šablon Azure Resource Manageru pomocí portálu Azure](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Povolení diagnostiky spouštění na existujícím virtuálním počítači 

Chcete-li povolit diagnostiku spouštění na existujícím virtuálním počítači, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a vyberte virtuální počítač.
2. V části **Podpora + řešení potíží** vyberte **Diagnostika spouštění**a pak vyberte kartu **Nastavení.**
3. V nastavení **diagnostiky spouštění** změňte stav **na Zapnuto**a v rozevíracím seznamu **Účet úložiště** vyberte účet úložiště. 
4. Uložte změnu.

    ![Aktualizace stávajícího virtuálního počítače](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

Chcete-li, aby se změna projevila, je nutné restartovat virtuální počítač.

### <a name="enable-boot-diagnostics-using-the-azure-cli"></a>Povolení diagnostiky spouštění pomocí Azure CLI

Pomocí azure cli můžete povolit diagnostiku spouštění na existujícím virtuálním počítači Azure. Další informace naleznete [v tématu az vm boot-diagnostics](
https://docs.microsoft.com/cli/azure/vm/boot-diagnostics?view=azure-cli-latest).
