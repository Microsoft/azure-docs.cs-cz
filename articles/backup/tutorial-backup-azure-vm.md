---
title: 'Kurz: Zálohování více virtuálních virtuálních počítačů Azure s PowerShellem'
description: Tento kurz podrobně popisuje zálohování více virtuálních počítačů Azure do trezoru služby Recovery Services pomocí Azure PowerShellu.
ms.topic: tutorial
ms.date: 03/05/2019
ms.custom: mvc
ms.openlocfilehash: 154238eae78ce44b9fc91058e58d9a11e254c0f9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74171781"
---
# <a name="back-up-azure-vms-with-powershell"></a>Zálohování virtuálních počítačů Azure s využitím PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tento kurz popisuje, jak nasadit trezor služby [Obnovení zálohování Azure](backup-overview.md) pro zálohování pro zálohování více virtuálních počítačů Azure pomocí PowerShellu.  

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
>
> * Vytvořte trezor služby Recovery Services a nastavte kontext úschovny.
> * Definice zásady zálohování
> * Použití zásady zálohování k ochraně několika virtuálních počítačů
> * Aktivace úlohy zálohování na vyžádání pro chráněné virtuální počítače Než budete moct zálohovat (nebo chránit) virtuální počítač, musíte dokončit [předpoklady](backup-azure-arm-vms-prepare.md) pro přípravu prostředí pro ochranu virtuálních počítačů.

> [!IMPORTANT]
> Tento kurz předpokládá, že už máte vytvořenou skupinu prostředků a virtuální počítač Azure.

## <a name="sign-in-and-register"></a>Přihlaste se a zaregistrujte se

1. Přihlaste se ke svému předplatnému Azure pomocí příkazu `Connect-AzAccount` a postupujte podle pokynů na obrazovce.

    ```powershell
    Connect-AzAccount
    ```

2. Při prvním použití služby Azure Backup je nutné zaregistrovat poskytovatele služby Azure Recovery Service ve svém předplatném pomocí [register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider). Pokud jste se již zaregistrovali, tento krok přeskočte.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

[Trezor služby Recovery Services](backup-azure-recovery-services-vault-overview.md) je logický kontejner, který ukládá záložní data pro chráněné prostředky, jako jsou virtuální počítače Azure. Při spuštění úlohy zálohování vytvoří bod obnovení uvnitř trezoru služby Recovery Services. Pomocí některého z těchto bodů obnovení pak můžete obnovit data k danému bodu v čase.

* V tomto kurzu vytvoříte trezor ve stejné skupině prostředků a umístění jako virtuální ho dispozičního virtuálního uživatele, který chcete zálohovat.
* Azure Backup automaticky zpracovává úložiště pro zálohovaná data. Ve výchozím nastavení používá úložiště [geograficky redundantní úložiště (GRS).](../storage/common/storage-redundancy-grs.md) Geografická redundance zajišťuje, že zálohovaná data se replikují do sekundární oblasti Azure, stovky kilometrů od primární oblasti.

Vytvořte úschovnu takto:

1. K vytvoření trezoru použijte trezor [New-AzRecoveryServicesVault.](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) Zadejte název skupiny prostředků a umístění virtuálního virtuálního serveru, který chcete zálohovat.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```

2. Řada rutin služby Azure Backup vyžaduje jako vstup objekt trezoru služby Recovery Services. Z tohoto důvodu je vhodné uložit objekt trezoru služby Recovery Services do proměnné.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```

3. Nastavte kontext úschovny pomocí [sady Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext).

   * Kontext trezoru představuje typ chráněných dat v trezoru.
   * Jakmile je kontext nastaven, platí pro všechny následující rutiny

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Zálohování virtuálních počítačů Azure

Zálohy jsou spuštěny v souladu s plánem uvedeným v zásadách zálohování. Při vytváření trezoru služby Recovery Services se vytvoří i výchozí zásady ochrany a uchovávání informací.

* Výchozí zásady ochrany spustí úlohu zálohování jednou denně v určený čas.
* Výchozí zásady uchovávání informací uchovávají denní bod obnovení po dobu 30 dnů.

Chcete-li povolit a zálohovat virtuální počítač Azure v tomto kurzu, uděláme následující:

1. Zadejte kontejner v úschovně, který obsahuje záložní data pomocí [get-azrecoveryservicesbackupcontainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Každý virtuální virtuální počítače pro zálohování je položka. Chcete-li spustit úlohu zálohování, získáte informace o virtuálním počítače s [službou Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Spusťte zálohu na vyžádání pomocí[položky Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).
    * První počáteční úloha zálohování vytvoří úplný bod obnovení.
    * Po počáteční mši vytvoří každá úloha zálohování přírůstkové body obnovení.
    * Přírůstkové body obnovení jsou efektivní z hlediska úložiště a času, protože přenášejí pouze změny provedené od posledního zálohování.

Povolte a spusťte zálohu následujícím způsobem:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>Řešení potíží

Pokud při zálohování virtuálního počítače narazíte na problémy, přečtěte si tento [článek o řešení potíží](backup-azure-vms-troubleshoot.md).

### <a name="deleting-a-recovery-services-vault"></a>Odstranění trezoru služby Recovery Services

Pokud potřebujete trezor odstranit, nejprve odstraňte body obnovení v úschovně a potom trezor zrušte registraci následujícím způsobem:

```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>Další kroky

* [Projděte si](backup-azure-vms-automation.md) podrobnější návod zálohování a obnovení virtuálních počítačích Azure pomocí PowerShellu.
* [Správa a monitorování virtuálních počítačů Azure](backup-azure-manage-vms.md)
* [Obnovení virtuálních počítačů Azure](backup-azure-arm-restore-vms.md)
