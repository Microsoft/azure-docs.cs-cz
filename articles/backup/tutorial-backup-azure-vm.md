---
title: 'Kurz: více zálohování virtuálních počítačů Azure pomocí PowerShellu'
description: Tento kurz podrobně popisuje zálohování několika virtuálních počítačů Azure do trezoru Recovery Services pomocí Azure PowerShell.
ms.topic: tutorial
ms.date: 03/05/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 53a5df430a3ea57201ecb19bac68ef9f073beb14
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90980962"
---
# <a name="back-up-azure-vms-with-powershell"></a>Zálohování virtuálních počítačů Azure s využitím PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

V tomto kurzu se dozvíte, jak nasadit Recovery Services trezoru [Azure Backup](backup-overview.md) pro zálohování několika virtuálních počítačů Azure pomocí PowerShellu.  

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
>
> * Vytvořte Trezor Recovery Services a nastavte kontext trezoru.
> * Definice zásady zálohování
> * Použití zásady zálohování k ochraně několika virtuálních počítačů
> * Aktivujte úlohu zálohování na vyžádání pro chráněné virtuální počítače, abyste mohli zálohovat virtuální počítač (nebo ho chránit), musíte splnit [předpoklady](backup-azure-arm-vms-prepare.md) pro přípravu vašeho prostředí pro ochranu vašich virtuálních počítačů.

> [!IMPORTANT]
> V tomto kurzu se předpokládá, že už máte vytvořenou skupinu prostředků a virtuální počítač Azure.

## <a name="sign-in-and-register"></a>Přihlášení a registrace

1. Přihlaste se ke svému předplatnému Azure pomocí příkazu `Connect-AzAccount` a postupujte podle pokynů na obrazovce.

    ```powershell
    Connect-AzAccount
    ```

2. Při prvním použití Azure Backup musíte ve svém předplatném zaregistrovat poskytovatele služby Azure Recovery Services pomocí [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider). Pokud jste už zaregistrovali, přeskočte tento krok.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

[Recovery Services trezor](backup-azure-recovery-services-vault-overview.md) je logický kontejner, který ukládá zálohovaná data pro chráněné prostředky, jako jsou například virtuální počítače Azure. Při spuštění úlohy zálohování se v rámci Recovery Services trezoru vytvoří bod obnovení. Pomocí některého z těchto bodů obnovení pak můžete obnovit data k danému bodu v čase.

* V tomto kurzu vytvoříte trezor ve stejné skupině prostředků a umístění jako virtuální počítač, který chcete zálohovat.
* Azure Backup automaticky zpracovává úložiště pro zálohovaná data. Ve výchozím nastavení používá trezor [geograficky redundantní úložiště (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage). Geografická redundance zajišťuje, že zálohovaná data se replikují do sekundární oblasti Azure a stovky kilometrů od primární oblasti.

Trezor vytvořte následujícím způsobem:

1. K vytvoření trezoru použijte  [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault). Zadejte název skupiny prostředků a umístění virtuálního počítače, který chcete zálohovat.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```

2. Řada rutin služby Azure Backup vyžaduje jako vstup objekt trezoru služby Recovery Services. Z tohoto důvodu je vhodné uložit objekt trezoru služby Backup Recovery Services do proměnné.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```

3. Nastavte kontext trezoru pomocí [set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext).

   * Kontext trezoru představuje typ chráněných dat v trezoru.
   * Po nastavení kontextu se použije na všechny následné rutiny.

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Zálohování virtuálních počítačů Azure

Zálohy běží v souladu s plánem zadaným v zásadách zálohování. Při vytváření trezoru služby Recovery Services se vytvoří i výchozí zásady ochrany a uchovávání informací.

* Výchozí zásady ochrany aktivují úlohu zálohování jednou denně v zadanou dobu.
* Výchozí zásady uchovávání informací uchovávají denní bod obnovení po dobu 30 dnů.

Pokud chcete povolit a zálohovat virtuální počítač Azure v tomto kurzu, provedeme následující:

1. V trezoru zadejte kontejner, který uchovává data záloh pomocí [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Každý virtuální počítač pro zálohování je položka. Pokud chcete spustit úlohu zálohování, získáte informace o virtuálním počítači pomocí [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Spusťte zálohování na vyžádání pomocí rutiny[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).
    * První úloha počátečního zálohování vytvoří úplný bod obnovení.
    * Po počátečním zálohování Každá úloha zálohování vytvoří přírůstkové body obnovení.
    * Přírůstkové body obnovení jsou efektivní z hlediska úložiště a času, protože přenášejí pouze změny provedené od posledního zálohování.

Zapněte a spusťte zálohování následujícím způsobem:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>Řešení potíží

Pokud narazíte na problémy při zálohování virtuálního počítače, přečtěte si tento [článek o řešení potíží](backup-azure-vms-troubleshoot.md).

### <a name="deleting-a-recovery-services-vault"></a>Odstranění trezoru služby Recovery Services

Pokud potřebujete odstranit trezor, nejprve odstraňte body obnovení v trezoru a pak zrušte registraci trezoru následujícím způsobem:

```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>Další kroky

* [Přečtěte](backup-azure-vms-automation.md) si podrobnější návod k zálohování a obnovení virtuálních počítačů Azure pomocí PowerShellu.
* [Správa a monitorování virtuálních počítačů Azure](backup-azure-manage-vms.md)
* [Obnovení virtuálních počítačů Azure](backup-azure-arm-restore-vms.md)
