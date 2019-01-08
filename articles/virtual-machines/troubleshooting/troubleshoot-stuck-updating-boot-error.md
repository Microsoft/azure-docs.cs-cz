---
title: Azure při spuštění virtuálního počítače se zasekla na Windows Update | Dokumentace Microsoftu
description: Zjistěte, jak problém vyřešit. Pokud po spuštění virtuálního počítače Azure se zasekla na Windows update.
services: virtual-machines-windows
documentationCenter: ''
authors: genli
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: d56e96ca1fbc96261f6f526c792b0a53c74718ef
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063656"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Azure při spuštění virtuálního počítače se zasekla na Windows update

Tento článek pomůže problém vyřešit, když vašeho virtuálního počítače (VM) zasekl ve fázi aktualizace Windows během spouštění. 

> [!NOTE] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a classic](../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Resource Manager. Doporučujeme použít tento model pro nových nasazení namísto pomocí modelu nasazení classic.

 ## <a name="symptom"></a>Příznak

 Virtuální počítač Windows nespustí. Když vrátíte se změnami na snímcích obrazovky [Diagnostika spouštění](../troubleshooting/boot-diagnostics.md) okně uvidíte, že se zablokovala spuštění v procesu aktualizace. Následují příklady zpráv, které se mohou zobrazit:

- Instalace Windows ##% nevypínejte počítač. Bude to trvat nějakou dobu svůj počítač se několikrát restartovat.
- Dokud to neprovedete, zachovat na váš počítač. Instalace aktualizace # #... 
- Aktualizace, které vracejí se zpět změny nevypínejte počítač nejde dokončit
- Chyba konfigurace Windows aktualizace probíhá návrat změny nevypínejte počítač
- Chyba při < kód chyby > použití operace aktualizace ### z ### (\Regist...)
- Závažná chyba < kód chyby > použití operace aktualizace ### z ### ($$...)


## <a name="solution"></a>Řešení

V závislosti na počtu aktualizací, které se zobrazuje nainstalované nebo vrácena zálohování, proces aktualizace může chvíli trvat. Ponechejte virtuální počítač v tomto stavu po dobu 8 hodin. Pokud virtuální počítač je stále v tomto stavu po uplynutí této doby, restartujte virtuální počítač z portálu Azure portal a zobrazit, pokud se dá spustit normálně. Pokud tento krok nepomůže, použijte následující postup.

### <a name="remove-the-update-that-causes-the-problem"></a>Odebrání aktualizace, která způsobí, že problém

1. Pořízení snímku disku s operačním systémem virtuálního počítače ovlivněný jako záložní. Další informace najdete v tématu [pořízení snímku disku](../windows/snapshot-copy-managed-disk.md). 
2. [Připojte disk s operačním systémem pro virtuální počítač pro obnovení](troubleshoot-recovery-disks-portal-windows.md).
3. Po Virtuálním počítači pro obnovení je připojený disk s operačním systémem, spuštěním souboru **diskmgmt.msc** otevřete správu disků a zkontrolujte připojený disk je **ONLINE**. Poznamenejte si písmeno jednotky, která je přiřazena připojeného disku s operačním systémem obsahující složku \windows. Pokud je disk šifrovaný, dešifrujte disku, než budete pokračovat s dalšími kroky v tomto dokumentu.

4. Spusťte instanci příkazový řádek se zvýšenými oprávněními (Spustit jako správce). Spuštěním následujícího příkazu Získejte seznam balíčky aktualizací, které jsou v připojeném disku s operačním systémem:

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    Například pokud připojeném disku s operačním systémem je jednotka F, spusťte následující příkaz:

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
5. Otevřete soubor C:\temp\Patch_level.txt a přečíst ho zdola. Vyhledejte aktualizace, která je v **instalace čeká** nebo **odinstalovat čekající** stavu.  Tady je ukázka stav aktualizace:

     ```
    Package Identity : Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    State : Install Pending
    Release Type : Security Update
    Install Time :
    ```
6. Odebrání aktualizace, která způsobila Chyba:
    
    ```
    dism /Image:<Attached OS disk>:\ /Remove-Package /PackageName:<PACKAGE NAME TO DELETE>
    ```
    Příklad: 

    ```
    dism /Image:F:\ /Remove-Package /PackageName:Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    ```

    > [!NOTE] 
    > V závislosti na velikosti balíčku nástroje DISM bude chvíli trvat ke zpracování zrušení instalace. Proces bude obvykle dokončit do 16 minut.

7. [Odpojit disk s operačním systémem a znovu vytvořte virtuální počítač](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk). Zkontrolujte, zda je problém vyřešen.