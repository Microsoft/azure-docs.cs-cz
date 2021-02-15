---
title: Šifrování zálohovaných dat s využitím klíčů spravovaných zákazníkem
description: Přečtěte si, jak Azure Backup umožňuje šifrovat zálohovaná data pomocí klíčů spravovaných zákazníkem (CMK).
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 230669e0a3543a0709dda3f7fee35a0cae300d5a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369454"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Šifrování zálohovaných dat s využitím klíčů spravovaných zákazníkem

Azure Backup slouží k šifrování zálohovaných dat pomocí klíčů spravovaných zákazníkem (CMK) namísto použití klíčů spravovaných platformou, které jsou ve výchozím nastavení povolené. Klíče, které slouží k šifrování zálohovaných dat, musí být uloženy v [Azure Key Vault](../key-vault/index.yml).

Šifrovací klíč, který se používá k šifrování záloh, může být jiný než ten, který se používá pro zdroj. Data jsou chráněná pomocí šifrovacího klíče založeného na standardu AES 256 (klíč DEK), který je zase chráněný pomocí vašich klíčů (KEK). Díky tomu máte plnou kontrolu nad daty a klíči. Chcete-li šifrování zakázat, je nutné, aby měl Recovery Services trezoru udělen přístup k šifrovacímu klíči v Azure Key Vault. Klíč můžete změnit podle potřeby.

Tento článek popisuje následující:

- Vytvoření trezoru Recovery Services
- Konfigurace trezoru Recovery Services pro šifrování zálohovaných dat pomocí klíčů spravovaných zákazníkem
- Provádění zálohování do trezorů šifrovaných pomocí klíčů spravovaných zákazníkem
- Obnovování dat ze záloh

## <a name="before-you-start"></a>Než začnete

- Tato funkce umožňuje šifrovat **jenom nové trezory Recovery Services**. Všechny trezory, které obsahují existující položky registrované nebo se do ní pokusily zaregistrovat, se nepodporují.

- Po povolení Recovery Services trezoru se šifrování pomocí zákaznických klíčů nedá vrátit zpátky na použití klíčů spravovaných platformou (výchozí). Šifrovací klíče můžete změnit podle svých požadavků.

- Tato funkce v současné době nepodporuje **zálohování pomocí agenta Mars** a možná nebudete moct používat CMK šifrované úložiště pro stejné. Agent MARS používá šifrování založené na uživatelském heslu. Tato funkce také nepodporuje zálohování klasických virtuálních počítačů.

- Tato funkce nesouvisí s [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md), která používá šifrování disků virtuálního počítače založeného na hostu pomocí nástroje BitLocker (pro Windows) a DM-Crypt (pro Linux).

- Trezor Recovery Services lze zašifrovat pouze s klíči uloženými v Azure Key Vault nacházející se ve **stejné oblasti**. Klíče také musí být pouze **klíče RSA 2048** a měly by být v **povoleném** stavu.

- Přesunutí šifrovaného trezoru Recovery Services CMK napříč skupinami prostředků a předplatnými není v současné době podporováno.
- Když přesunete Recovery Services trezor již zašifrovaný pomocí klíčů spravovaných zákazníkem na nového tenanta, budete muset aktualizovat Recovery Services trezor a znovu vytvořit a znovu nakonfigurovat spravovanou identitu trezoru a CMK (která by se měla nacházet v novém tenantovi). Pokud to neuděláte, operace zálohování a obnovení začnou selhat. Všechna oprávnění řízení přístupu na základě role (RBAC) nastavená v rámci předplatného se taky musí překonfigurovat.

- Tato funkce se dá nakonfigurovat pomocí Azure Portal a PowerShellu.

    >[!NOTE]
    >K použití zákaznických klíčů pro zálohy v úložišti Recovery Services použijte AZ Module 5.3.0 nebo vyšší.

Pokud jste úložiště Recovery Services nevytvořili a nenakonfigurujete, můžete [si ho přečíst zde](backup-create-rs-vault.md).

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Konfigurace trezoru pro šifrování pomocí klíčů spravovaných zákazníkem

Tato část zahrnuje následující kroky:

1. Povolení spravované identity pro váš Recovery Services trezor

1. Přiřaďte k trezoru oprávnění pro přístup k šifrovacímu klíči v Azure Key Vault

1. Povolení ochrany obnovitelného odstranění a vyprázdnění na Azure Key Vault

1. Přiřazení šifrovacího klíče k trezoru Recovery Services

Aby se dosáhlo zamýšlených výsledků, je nutné, aby všechny tyto kroky následovaly v uvedeném pořadí. Jednotlivé kroky jsou podrobněji popsány níže.

### <a name="enable-managed-identity-for-your-recovery-services-vault"></a>Povolení spravované identity pro váš Recovery Services trezor

Azure Backup používá spravovanou identitu přiřazenou systémem k ověření trezoru Recovery Services pro přístup k šifrovacím klíčům uloženým v Azure Key Vault. Pokud chcete pro svůj Recovery Services trezor povolit spravovanou identitu, postupujte podle níže uvedených kroků.

>[!NOTE]
>Po povolení nesmí být spravovaná identita zakázaná **(ještě dočasně** ). Zakázání spravované identity může vést k nekonzistentnímu chování.

**Na portálu:**

1. Přejít na váš Recovery Services trezor – > **Identita**

    ![Nastavení identity](./media/encryption-at-rest-with-cmk/managed-identity.png)

1. Změňte **stav** na **zapnuto** a vyberte **Uložit**.

1. Generuje se ID objektu, což je spravovaná identita trezoru přiřazená systémem.

**S prostředím PowerShell:**

Pomocí příkazu [Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault) povolte spravovanou identitu přiřazenou systémem pro trezor služby Recovery Services.

Příklad:

```AzurePowerShell
$vault=Get-AzRecoveryServicesVault -ResourceGroupName "testrg" -Name "testvault"

Update-AzRecoveryServicesVault -IdentityType SystemAssigned -VaultId $vault.ID

$vault.Identity | fl
```

Výstup:

```output
PrincipalId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
TenantId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Type        : SystemAssigned
```

### <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>Přiřaďte oprávnění k trezoru Recovery Services pro přístup k šifrovacímu klíči v Azure Key Vault

Pro přístup k Azure Key Vault, který obsahuje šifrovací klíč, teď musíte povolit Recovery Services trezor. To se provádí tak, že se pro přístup k Key Vault povolí spravovaná identita Recovery Servicesového trezoru.

Na **portálu**:

1. Přejděte do > **zásady přístupu** Azure Key Vault. Pokračujte a **přidejte zásady přístupu**.

    ![Přidat zásady přístupu](./media/encryption-at-rest-with-cmk/access-policies.png)

1. V části **oprávnění klíče** vyberte **získat**, **vypsat**, **Rozbalit klíč** a operace **zabalení klíče** . To určuje akce pro klíč, který bude povolen.

    ![Přiřadit klíčová oprávnění](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. Ve vyhledávacím poli **Vyberte objekt zabezpečení** a vyhledejte svůj trezor pomocí jeho názvu nebo spravované identity. Po zobrazení vyberte trezor a zvolte **Vybrat** v dolní části podokna.

    ![Vybrat objekt zabezpečení](./media/encryption-at-rest-with-cmk/select-principal.png)

1. Po dokončení vyberte **Přidat** a přidejte nové zásady přístupu.

1. Vyberte **Uložit** a uložte změny provedené v zásadách přístupu Azure Key Vault.

### <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>Povolení ochrany obnovitelného odstranění a vyprázdnění na Azure Key Vault

U Azure Key Vault, který ukládá šifrovací klíč, je potřeba **Povolit ochranu před odstraněním a vyprázdněním** . Můžete to provést z uživatelského rozhraní Azure Key Vault, jak je znázorněno níže. (Případně tyto vlastnosti lze nastavit při vytváření Key Vault). Přečtěte si další informace o těchto Key Vaultch [vlastnostech.](../key-vault/general/soft-delete-overview.md)

![Povolení obnovitelného odstranění a ochrany před vymazáním](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

Pomocí následujícího postupu můžete povolit ochranu pomocí obnovitelného odstranění a mazání prostřednictvím PowerShellu:

1. Přihlaste se ke svému účtu Azure.

    ```azurepowershell
    Login-AzAccount
    ```

1. Vyberte předplatné, které obsahuje váš trezor.

    ```azurepowershell
    Set-AzContext -SubscriptionId SubscriptionId
    ```

1. Povolení obnovitelného odstranění

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

1. Povolit vyprázdnit ochranu

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

### <a name="assign-encryption-key-to-the-rs-vault"></a>Přiřazení šifrovacího klíče k trezoru RS

>[!NOTE]
> Než budete pokračovat, zajistěte následující:
>
> - Všechny výše uvedené kroky byly úspěšně dokončeny:
>   - Byla povolena spravovaná identita Recovery Servicesového trezoru a byla mu přiřazena požadovaná oprávnění.
>   - Azure Key Vault má zapnutou ochranu proti odstranění a vyprázdnění
> - U trezoru Recovery Services, pro který chcete povolit šifrování CMK, **nejsou** žádné položky chráněné nebo zaregistrované.

Jakmile budou tyto možnosti zajištěny, pokračujte výběrem šifrovacího klíče pro váš trezor.

#### <a name="to-assign-the-key-in-the-portal"></a>Přiřazení klíče na portálu

1. Přejít na Recovery Services trezor – **vlastnosti** >

    ![Nastavení šifrování](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. V **nastavení šifrování** vyberte **aktualizovat** .

1. V podokně nastavení šifrování vyberte **použít vlastní klíč** a pokračujte v zadávání klíče jedním z následujících způsobů. **Ujistěte se, že klíč, který chcete použít, je klíč RSA 2048, který je v povoleném stavu.**

    1. Zadejte **identifikátor URI klíče** , se kterým chcete šifrovat data v tomto trezoru Recovery Services. Je také nutné zadat předplatné, ve kterém je k dispozici Azure Key Vault (obsahující tento klíč). Tento identifikátor URI klíče lze získat z odpovídajícího klíče ve vašem Azure Key Vault. Ujistěte se, že se identifikátor URI klíče zkopíroval správně. Doporučuje se použít tlačítko **Kopírovat do schránky** , které je k dispozici s identifikátorem klíče.

        >[!NOTE]
        >Při zadávání šifrovacího klíče pomocí identifikátoru URI klíče nebude klíč automaticky otočen. Proto je potřeba aktualizovat klíče ručně zadáním nového klíče v případě potřeby.

        ![Zadejte identifikátor URI klíče.](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. Procházet a vyberte klíč z Key Vault v podokně výběr klíče.

        >[!NOTE]
        >Při zadávání šifrovacího klíče pomocí podokna pro výběr klíče se klíč automaticky otočí, kdykoli je povolená nová verze klíče.

        ![Vyberte klíč z trezoru klíčů.](./media/encryption-at-rest-with-cmk/key-vault.png)

1. Vyberte **Uložit**.

1. **Sledování průběhu a stavu aktualizace šifrovacího klíče**: můžete sledovat průběh přiřazení šifrovacího klíče a jeho stav pomocí zobrazení **úlohy zálohování** na levém navigačním panelu. Stav by se brzy změnil na **dokončeno**. Váš trezor teď bude šifrovat všechna data se zadaným klíčem jako KEK.

    ![Stav dokončen](./media/encryption-at-rest-with-cmk/status-succeeded.png)

    Aktualizace šifrovacího klíče se zaznamenávají taky v protokolu aktivit trezoru.

    ![Protokol aktivit](./media/encryption-at-rest-with-cmk/activity-log.png)

#### <a name="to-assign-the-key-with-powershell"></a>Přiřazení klíče k PowerShellu

Pomocí příkazu [set-AzRecoveryServicesVaultProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultproperty) Povolte šifrování pomocí klíčů spravovaných zákazníkem a přiřaďte nebo aktualizujte šifrovací klíč, který se má použít.

Příklad:

```azurepowershell
$keyVault = Get-AzKeyVault -VaultName "testkeyvault" -ResourceGroupName "testrg" 
$key = Get-AzKeyVaultKey -VaultName $keyVault -Name "testkey" 
Set-AzRecoveryServicesVaultProperty -EncryptionKeyId $key.ID -KeyVaultSubscriptionId "xxxx-yyyy-zzzz"  -VaultId $vault.ID


$enc=Get-AzRecoveryServicesVaultProperty -VaultId $vault.ID
$enc.encryptionProperties | fl
```

Výstup:

```output
EncryptionAtRestType          : CustomerManaged
KeyUri                        : testkey
SubscriptionId                : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 
LastUpdateStatus              : Succeeded
InfrastructureEncryptionState : Disabled
```

>[!NOTE]
> Tento proces zůstává stejný, když chcete aktualizovat nebo změnit šifrovací klíč. Pokud chcete aktualizovat a používat klíč z jiné Key Vault (jiný než ten, který se právě používá), ujistěte se, že:
>
> - Key Vault se nachází ve stejné oblasti jako Recovery Services trezor.
>
> - Trezor klíčů má zapnutou ochranu před příčtením a mazáním
>
> - Recovery Services trezor má požadovaná oprávnění pro přístup k Key Vault.

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>Zálohování do trezoru zašifrovaného pomocí klíčů spravovaných zákazníkem

Než budete pokračovat v konfiguraci ochrany, důrazně doporučujeme, abyste měli jistotu, že je následující kontrolní seznam dodržen. To je důležité, protože jakmile je konfigurace položky nakonfigurovaná tak, aby se zálohovaná (nebo se pokusila nakonfigurovat) na neCMK zašifrovaný trezor, nejde na ni povolit šifrování pomocí klíčů spravovaných zákazníkem a bude dál používat klíče spravované platformou.

>[!IMPORTANT]
> Než budete pokračovat v konfiguraci ochrany, musíte **úspěšně** dokončit následující kroky:
>
>1. Vytvoření trezoru záloh
>1. Povoluje spravovanou identitu přiřazenou systémem úložiště záloh.
>1. Pro přístup k šifrovacím klíčům z vaší Key Vault jsou přiřazena oprávnění k vašemu trezoru záloh.
>1. Povolení obnovitelného odstranění a vymazání ochrany pro Key Vault
>1. Byl přiřazen platný šifrovací klíč pro úložiště záloh.
>
>Pokud byly všechny výše uvedené kroky potvrzené, pokračujte v konfiguraci zálohování.

Proces konfigurace a zálohování trezoru Recovery Services šifrovaných pomocí klíčů spravovaných zákazníkem je stejný jako trezor, který používá klíče spravované platformou bez jakýchkoli **změn v prostředí**. To platí pro [zálohování virtuálních počítačů Azure](./quick-backup-vm-portal.md) a také zálohování úloh spuštěných uvnitř virtuálního počítače (například [SAP HANA](./tutorial-backup-sap-hana-db.md) [SQL Server](./tutorial-sql-backup.md) databáze).

## <a name="restoring-data-from-backup"></a>Obnovování dat ze zálohy

### <a name="vm-backup"></a>Zálohování virtuálního počítače

Data uložená v úložišti Recovery Services lze obnovit podle kroků popsaných [zde](./backup-azure-arm-restore-vms.md). Při obnovení z trezoru Recovery Services šifrovaných pomocí klíčů spravovaných zákazníkem se můžete rozhodnout, že se obnovená data zašifrují pomocí sady Disk Encryption (DES).

#### <a name="restoring-vm--disk"></a>Obnovení virtuálního počítače nebo disku

1. Při obnovování disku nebo virtuálního počítače z bodu obnovení snímku se obnovená data šifrují pomocí DES, který se používá k šifrování disků zdrojového virtuálního počítače.

1. Při obnovování disku/virtuálního počítače z bodu obnovení s typem obnovení jako trezoru se můžete rozhodnout, že budou obnovená data šifrována pomocí algoritmu DES zadaného v době obnovení. Alternativně můžete zvolit pokračování v obnovování dat bez zadání algoritmu DES. v takovém případě bude šifrování použito pomocí klíčů spravovaných společností Microsoft.

Po dokončení obnovení můžete znovu zašifrovat obnovený disk nebo virtuální počítač bez ohledu na výběr, který jste provedli při zahájení obnovení.

![Body obnovení](./media/encryption-at-rest-with-cmk/restore-points.png)

#### <a name="select-a-disk-encryption-set-while-restoring-from-vault-recovery-point"></a>Vyberte sadu šifrování disku při obnovení z bodu obnovení trezoru.

Na **portálu**:

Sada šifrování disků je zadaná v části nastavení šifrování v podokně obnovení, jak je znázorněno níže:

1. Na **discích, které používají váš klíč**, vyberte **Ano**.

1. V rozevíracím seznamu vyberte algoritmus DES, který chcete použít pro obnovené disky. **Ujistěte se, že máte přístup k DES.**

>[!NOTE]
>Možnost zvolit algoritmus DES během obnovování není k dispozici, pokud obnovujete virtuální počítač, který používá Azure Disk Encryption.

![Šifrování disku pomocí klíče](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

**S prostředím PowerShell**:

Pomocí příkazu [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) s parametrem [ `-DiskEncryptionSetId <string>` ] [Určete algoritmus DES](/powershell/module/az.compute/get-azdiskencryptionset) , který se použije k šifrování obnoveného disku. Další informace o obnovení disků ze zálohy virtuálního počítače najdete v [tomto článku](./backup-azure-vms-automation.md#restore-an-azure-vm).

Příklad:

```azurepowershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $vault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $vault.ID
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $vault.ID
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -DiskEncryptionSetId “testdes1” -VaultId $vault.ID
```

#### <a name="restoring-files"></a>Obnovování souborů

Při provádění obnovení souboru budou obnovená data šifrována klíčem použitým k šifrování cílového umístění.

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>Obnovení databází SAP HANA/SQL na virtuálních počítačích Azure

Při obnovení ze zálohované SAP HANA/databáze SQL běžící na virtuálním počítači Azure se obnovená data šifrují pomocí šifrovacího klíče, který se používá v umístění cílového úložiště. Může se jednat o klíč spravovaný zákazníkem nebo o klíč spravovaný platformou, který se používá k šifrování disků virtuálního počítače.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="can-i-encrypt-an-existing-backup-vault-with-customer-managed-keys"></a>Je možné zašifrovat existující trezor služby Backup pomocí klíčů spravovaných zákazníkem?

Ne, CMK šifrování se dá povolit jenom pro nové trezory. Proto trezor nesmí mít nikdy žádné chráněné položky. Předtím, než povolíte šifrování pomocí klíčů spravovaných zákazníkem, je třeba provést žádné pokusy o ochranu položek do trezoru.

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-enable-cmk-encryption-for-this-vault"></a>Pokusili jste se chránit položku do trezoru, ale nedošlo k jejímu uložení a trezor neobsahuje žádné chráněné položky. Můžu pro tento trezor povolit šifrování CMK?

Ne, trezor nesmí mít žádné pokusy o ochranu jakýchkoli položek v minulosti.

### <a name="i-have-a-vault-thats-using-cmk-encryption-can-i-later-revert-to-encryption-using-platform-managed-keys-even-if-i-have-backup-items-protected-to-the-vault"></a>Mám trezor, který používá šifrování CMK. Můžu se později vrátit k šifrování pomocí klíčů spravovaných platformou, i když mám zálohované položky chráněné do trezoru?

Ne, po povolení šifrování CMK ho nejde vrátit, aby se používaly klíče spravované platformou. Můžete změnit klíče používané podle vašich požadavků.

### <a name="does-cmk-encryption-for-azure-backup-also-apply-to-azure-site-recovery"></a>Platí CMK šifrování pro Azure Backup taky pro Azure Site Recovery?

Ne, Tento článek popisuje pouze šifrování zálohovaných dat. Pro Azure Site Recovery je potřeba nastavit vlastnost samostatně, jak je dostupná ze služby.

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-cmk-encryption"></a>Vynechali jsme některý z kroků v tomto článku a zavedli jsme k ochraně zdroje dat. Můžu pořád používat šifrování CMK?

Není nutné postupovat podle kroků v článku a nadále chránit položky, což může způsobit, že trezor nebude moci používat šifrování pomocí klíčů spravovaných zákazníkem. Proto doporučujeme, abyste před pokračováním v ochraně položek přeodkazovali na [Tento kontrolní seznam](#backing-up-to-a-vault-encrypted-with-customer-managed-keys) .

### <a name="does-using-cmk-encryption-add-to-the-cost-of-my-backups"></a>Je použití CMK-Encryption přidané do nákladů na zálohy?

Použití šifrování CMK pro zálohování neplatí pro vás žádné další náklady. Může se ale stát, že se vám budou účtovat poplatky za použití Azure Key Vault, kde je váš klíč uložený.

## <a name="next-steps"></a>Další kroky

- [Přehled funkcí zabezpečení v Azure Backup](security-overview.md)
