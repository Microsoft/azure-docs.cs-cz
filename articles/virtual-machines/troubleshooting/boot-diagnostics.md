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
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 64641f8acfe7b58763756e2a0707fa799ee804b2
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47413407"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>Jak používat diagnostiku spouštění k řešení potíží s virtuálními počítači v Azure

Podpora dvou funkcí ladění je nyní k dispozici v Azure: podpora výstupu konzoly a snímku obrazovky modelu nasazení Resource Manageru pro virtuální počítače Azure. 

Při přenosu vlastní image do Azure nebo spouštění některé z imagí platformy, může být mnoho důvodů, proč virtuální počítač dostane do nespustitelného stavu. Tyto funkce umožňují snadnou diagnostiku a obnovení virtuálních počítačů z chyb při spuštění.

Pro virtuální počítače s Linuxem můžete snadno zobrazit výstup protokolu konzoly z portálu. Pro virtuální počítače Windows a Linuxem Azure také umožňuje zobrazit snímek virtuálního počítače z hypervisoru. Obě tyto funkce jsou podporovány pro virtuální počítače, které jsou ve všech oblastech Azure. Mějte na paměti, že může trvat až 10 minut, než se snímky obrazovky a výstup zobrazí v účtu úložiště.

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

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Povolení diagnostiky na novém virtuálním počítači
1. Při vytváření nového virtuálního počítače na webu Azure Portal, vyberte **Azure Resource Manageru** z rozevíracího seznamu modelu nasazení:
 
    ![Resource Manager](./media/virtual-machines-common-boot-diagnostics/screenshot3.jpg)

2. V **nastavení**, povolte **Diagnostika spouštění**a pak vyberte účet úložiště, že chcete tyto diagnostické soubory.
 
    ![Vytvoření virtuálního počítače](./media/virtual-machines-common-boot-diagnostics/create-storage-account.png)

    > [!NOTE]
    > Funkce diagnostiky spouštění nepodporuje účet premium storage. Pokud používáte účet premium storage pro diagnostiku spouštění, StorageAccountTypeNotSupported chybě může dojít při spuštění virtuálního počítače.
    >
    > 

3. Pokud provádíte nasazení ze šablony Azure Resource Manageru, přejděte do prostředku vašeho virtuálního počítače a připojte část s diagnostickým profilem. Nezapomeňte použít hlavičku verze rozhraní API s hodnotou 2015-06-15.

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. Diagnostický profil umožňuje vybrat účet úložiště pro ukládání protokolů.

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

Pokud chcete nasadit ukázkový virtuální počítač s povolenou diagnostikou spuštění, podívejte se sem na naše úložiště.

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Povolení diagnostiky spouštění na existující virtuální počítač 

Povolení diagnostiky spouštění na virtuálním počítači, postupujte podle těchto kroků:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a potom vyberte virtuální počítač.
2. V **podpora a řešení potíží**vyberte **Diagnostika spouštění** > **nastavení**, změňte stav na **na**a pak Vyberte účet úložiště. 
4. Ujistěte se, že je vybraná možnost Diagnostika spuštění a uložte změny.

    ![Aktualizace stávajícího virtuálního počítače](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

3. Restartujte virtuální počítač, aby se projevily změny.


