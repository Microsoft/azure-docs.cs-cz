---
title: Spuštění diagnostiky pro virtuální počítače s Linuxem v Azure | Microsoft dokumentů
description: Přehled ladění dvou funkcí pro virtuální počítače s Linuxem v Azure
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: Deland-Han
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: delhan
ms.openlocfilehash: 0183da348a515787d9382df6db3df8524d584d93
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-linux-virtual-machines-in-azure"></a>Jak používat Diagnostika spouštění k řešení potíží s virtuální počítače s Linuxem v Azure

V Azure je teď dostupná podpora dvou funkcí ladění: Podpora výstupu konzoly a snímku obrazovky pro model nasazení virtuálních počítačů Azure Resource Manager. 

Při přenosu vlastní image do Azure nebo spouštění některé z imagí platformy může virtuální počítač přejít do nespustitelného stavu z mnoha důvodů. Tyto funkce umožňují snadnou diagnostiku a obnovení virtuálních počítačů v případě chyb při spuštění.

U virtuálních počítačů s Linuxem můžete snadno zobrazit výstup protokolu konzoly z portálu:

![Azure Portal](./media/boot-diagnostics/screenshot1.png)
 
U virtuálních počítačů s Windows i Linuxem však Azure umožňuje zobrazit také snímek obrazovky virtuálního počítače z hypervisoru:

![Chyba](./media/boot-diagnostics/screenshot2.png)

Obě tyto funkce jsou podporované pro virtuální počítače Azure ve všech oblastech. Mějte na paměti, že může trvat až 10 minut, než se snímky obrazovky a výstup zobrazí v účtu úložiště.

## <a name="common-boot-errors"></a>Běžné chyby spuštění

- [Problémy systému souborů](https://support.microsoft.com/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) 
- [Problémy jádra](https://support.microsoft.com/help/4091524/how-recovery-azure-linux-vm-from-kernel-related-boot-related-issues/) 
- [FSTAB chyby](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Povolení diagnostiky na novém virtuálním počítači
1. Při vytváření nového virtuálního počítače na portálu Preview vyberte z rozevíracího seznamu modelu nasazení **Azure Resource Manager**:
 
    ![Resource Manager](./media/boot-diagnostics/screenshot3.jpg)

2. Nakonfigurujte možnost Monitorování pro výběr účtu úložiště, do kterého chcete tyto diagnostické soubory ukládat.
 
    ![Vytvoření virtuálního počítače](./media/boot-diagnostics/screenshot4.jpg)

3. Pokud provádíte nasazení z šablony Azure Resource Manageru, přejděte do prostředku vašeho virtuálního počítače a připojte část s diagnostickým profilem. Nezapomeňte použít hlavičku verze rozhraní API s hodnotou 2015-06-15.

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

## <a name="update-an-existing-virtual-machine"></a>Aktualizace stávajícího virtuálního počítače

Pokud chcete povolit Diagnostika spouštění prostřednictvím portálu, můžete také aktualizovat existujícího virtuálního počítače přes portál. Vyberte možnost Diagnostika spuštění a klikněte na Uložit. Restartujte virtuální počítač, aby se projevily změny.

![Aktualizace stávajícího virtuálního počítače](./media/boot-diagnostics/screenshot5.png)

## <a name="next-steps"></a>Další postup

Pokud se zobrazí chybu "Nepodařilo se získat obsah protokolu" při použití Diagnostika spouštění virtuálních počítačů, přečtěte si téma [se nezdařilo načíst obsah Chyba protokolu v Diagnostika spouštění virtuálních počítačů](https://support.microsoft.com/help/4094480/failed-to-get-contents-of-the-log-error-in-vm-boot-diagnostics-in-azur).