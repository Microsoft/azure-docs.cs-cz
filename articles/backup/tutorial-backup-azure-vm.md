---
title: Zálohování několika virtuálních počítačů Azure pomocí Powershellu
description: Tento kurz podrobně popisuje zálohování několika virtuálních počítačů Azure do trezoru služby Recovery Services pomocí prostředí Azure PowerShell.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 2a35435f56a4bb09a8a1958fbc175ef7c889c380
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2019
ms.locfileid: "58863011"
---
# <a name="back-up-azure-vms-with-powershell"></a>Zálohování virtuálních počítačů Azure pomocí Powershellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tento kurz popisuje, jak nasadit [Azure Backup](backup-overview.md) trezor služby Recovery Services k zálohování několika virtuálních počítačů Azure pomocí Powershellu.  

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte trezor služby Recovery Services a nastavte kontext trezoru.
> * Definice zásady zálohování
> * Použití zásady zálohování k ochraně několika virtuálních počítačů
> * Aktivační událost úlohu zálohování na vyžádání pro chráněné virtuální počítače před můžete zálohovat (nebo chránit) virtuální počítač, je třeba provést [požadavky](backup-azure-arm-vms-prepare.md) Příprava prostředí pro ochranu virtuálních počítačů. 

> [!IMPORTANT]
> Tento kurz předpokládá, že už máte vytvořenou skupinu prostředků a virtuální počítač Azure.


## <a name="log-in-and-register"></a>Přihlaste se a registrace


1. Přihlaste se k předplatnému Azure pomocí příkazu `Connect-AzAccount` a postupujte podle pokynů na obrazovce.

    ```powershell
    Connect-AzAccount
    ```
2. Při prvním použití služby Azure Backup, zaregistrujte poskytovatele služeb zotavení Azure ve vašem předplatném pomocí [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider). Pokud jste už zaregistrovali, tento krok přeskočte.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```


## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

A [trezor služby Recovery Services](backup-azure-recovery-services-vault-overview.md) je logický kontejner, který zálohuje data na chráněný prostředek, jako jsou virtuální počítače Azure. Když se spouští úloha zálohování, vytvoří bod obnovení v trezoru služby Recovery Services. Pomocí některého z těchto bodů obnovení pak můžete obnovit data k danému bodu v čase.


- V tomto kurzu vytvoříte trezor ve stejné skupině prostředků a umístění jako virtuální počítač, které chcete zálohovat.
- Azure Backup automaticky zpracovává úložiště pro zálohovaná data. Ve výchozím nastavení používá trezor [geograficky redundantní úložiště (GRS)](../storage/common/storage-redundancy-grs.md). Geografická redundance zajišťuje, že zálohovat data se replikují do sekundární oblasti Azure, stovky mil od primární oblasti.

Vytvořte trezor následujícím způsobem:

1. Použití [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)vytvořit trezor. Zadejte název skupiny prostředků a umístění virtuálního počítače, které chcete zálohovat.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```
2. Řada rutin služby Azure Backup vyžaduje jako vstup objekt trezoru služby Recovery Services. Z tohoto důvodu je vhodné uložit objekt trezoru služby Recovery Services do proměnné.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```
    
3. Nastavte kontext trezoru pomocí [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext).

   - Kontext trezoru představuje typ chráněných dat v trezoru.
   - Po nastavení kontextu, má platit pro všechny další rutiny

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Zálohování virtuálních počítačů Azure

Zálohování se spouštějí podle plánu, zadaný v zásadě zálohování. Při vytváření trezoru služby Recovery Services se vytvoří i výchozí zásady ochrany a uchovávání informací.

- Výchozí zásada ochrany spouští úlohu zálohování jednou denně v zadanou dobu.
- Výchozí zásady uchovávání informací uchovávají denní bod obnovení po dobu 30 dnů. 

Pokud chcete povolit a zálohovat virtuální počítač Azure v tomto kurzu, postupujte takto:

1. Zadejte kontejner v trezoru, který uchovává vaše zálohovaná data s [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Každý virtuální počítač pro zálohování je položka. Spustit úlohu zálohování, získejte informace o virtuálním počítači s [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Spustit zálohování ad hoc s[zálohování AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem). 
    - První úlohy prvotního zálohování vytvoří úplný bod obnovení.
    - Po prvotní zálohy Každá úloha zálohování vytváří přírůstkové body obnovení.
    - Přírůstkové body obnovení jsou efektivní z hlediska úložiště a času, protože přenášejí pouze změny provedené od posledního zálohování.

Povolení a spuštění zálohování následujícím způsobem:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>Řešení potíží 

Pokud narazíte na problémy při zálohování virtuálního počítače, přečtěte si [článek pro řešení potíží](backup-azure-vms-troubleshoot.md).

### <a name="deleting-a-recovery-services-vault"></a>Odstranění trezoru služby Recovery Services

Pokud je potřeba odstranit trezor, nejprve odstraňte body obnovení v trezoru a potom zrušit registraci trezoru, následujícím způsobem:


```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>Další postup

- [Kontrola](backup-azure-vms-automation.md) podrobnější návod k zálohování a obnovení virtuálních počítačů Azure pomocí Powershellu. 
- [Spravovat a monitorovat virtuální počítače Azure](backup-azure-manage-vms.md)
- [Obnovení virtuálních počítačů Azure](backup-azure-arm-restore-vms.md)
