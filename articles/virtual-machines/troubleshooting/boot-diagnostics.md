---
title: Diagnostika spouštění virtuálních počítačů v Azure | Dokumentace Microsoftu
description: Přehled dvou funkcí ladění pro virtuální počítače v Azure
services: virtual-machines
author: Deland-Han
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: 55feef336ade461d4e3936d2f76122e5b4b4c00e
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2019
ms.locfileid: "54401295"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>Jak používat diagnostiku spouštění k řešení potíží s virtuálními počítači v Azure

Může existovat mnoho důvodů, proč se, že virtuální počítač dostane do nespustitelného stavu. Pro řešení potíží s pomocí vašich virtuálních počítačů vytvořených pomocí modelu nasazení Resource Manageru můžete použít následující funkce ladění: Výstup konzoly a snímku obrazovky podporu pro virtuální počítače Azure. 

Pro virtuální počítače s Linuxem můžete zobrazit výstup protokolu konzoly z portálu. Pro virtuální počítače Windows a Linuxem Azure vám umožní zobrazit snímek virtuálního počítače z hypervisoru. Obě funkce jsou podporovány pro virtuální počítače, které jsou ve všech oblastech Azure. Mějte na paměti, že může trvat až 10 minut, než se snímky obrazovky a výstup zobrazí v účtu úložiště.

Můžete vybrat **Diagnostika spouštění** možnost zobrazit v protokolu a na snímku obrazovky.

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

## <a name="enable-diagnostics-on-a-virtual-machine-created-using-the-azure-portal"></a>Povolit diagnostiku na virtuálním počítači vytvořeném pomocí webu Azure Portal

Následující postup je pro virtuální počítače vytvořené pomocí modelu nasazení Resource Manager.

Na **správu** kartě **monitorování** části, ujistěte se, že **Diagnostika spouštění** zapnutý. Z **účet úložiště diagnostiky** rozevírací seznam, vyberte účet úložiště, ve které chcete umístit diagnostické soubory.
 
![Vytvoření virtuálního počítače](./media/virtual-machines-common-boot-diagnostics/enable-boot-diagnostics-vm.png)

> [!NOTE]
> Funkce diagnostiky spouštění nepodporuje účet premium storage. Pokud používáte účet premium storage pro diagnostiku spouštění, StorageAccountTypeNotSupported chybě může dojít při spuštění virtuálního počítače.
>

### <a name="deploying-from-an-azure-resource-manager-template"></a>Nasazení ze šablony Azure Resource Manageru

Pokud provádíte nasazení ze šablony Azure Resource Manageru, přejděte do prostředku vašeho virtuálního počítače a připojte část s diagnostickým profilem. Nastavte hlavičku verze rozhraní API na "2015-06-15" nebo novější. Nejnovější verze je "2018-10-01".

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

Další informace o nasazení prostředků pomocí šablon najdete v tématu [rychlý start: Vytvoření a nasazení šablon Azure Resource Manageru pomocí webu Azure portal](/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Povolení diagnostiky spouštění na existující virtuální počítač 

Povolení diagnostiky spouštění na virtuálním počítači, postupujte podle těchto kroků:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a potom vyberte virtuální počítač.
2. V **podpora a řešení potíží** vyberte **Diagnostika spouštění**a pak **nastavení** kartu.
3. V **Diagnostika spouštění** nastavení, změní stav na **na**a od **účtu úložiště** rozevíracího seznamu vyberte účet úložiště. 
4. Uložte změny.

    ![Aktualizace stávajícího virtuálního počítače](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

Je nutné restartovat virtuální počítač se změna projevila.

### <a name="enable-boot-diagnostics-using-the-azure-cli"></a>Povolte diagnostiku spouštění pomocí rozhraní příkazového řádku Azure

Rozhraní příkazového řádku Azure můžete použít k povolení diagnostiky spouštění na existující virtuální počítač Azure. Další informace najdete v tématu [az Diagnostika spouštění virtuálních počítačů –](
https://docs.microsoft.com/cli/azure/vm/boot-diagnostics?view=azure-cli-latest).
