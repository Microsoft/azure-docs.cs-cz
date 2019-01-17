---
title: 'Řešení potíží s Azure Backup selhání: Stav agenta hosta k dispozici'
description: Příznaky, příčiny a řešení Azure Backup chyby týkající se agenta, rozšíření a disky.
services: backup
author: genlin
manager: cshepard
keywords: Služba Azure backup; Agent virtuálního počítače; Připojení k síti;
ms.service: backup
ms.topic: troubleshooting
ms.date: 12/03/2018
ms.author: genli
ms.openlocfilehash: c779344f4cb0544009952423b6771b75482c3061
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353954"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Řešení potíží s Azure Backup selhání: Potíže s agentů nebo rozšíření

Tento článek popisuje postup řešení potíží, které vám může pomoct vyřešit Azure Backup chyby týkající se komunikace s agentem virtuálního počítače a rozšíření.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable - agent virtuálního počítače moci komunikovat se službou Azure Backup

**Kód chyby:**: UserErrorGuestAgentStatusUnavailable <br>
**Chybová zpráva**: Agent virtuálního počítače moci komunikovat se službou Azure Backup<br>

Po registraci a plánování virtuálních počítačů za službu Backup zahájí zálohování úlohy tím, že komunikuje s agentem virtuálního počítače k vytvoření snímku bodu v čase. Snímek některý z následujících podmínek může zabránit se aktivuje. Když se aktivuje snímku, zálohování se nemusí podařit. Proveďte následující kroky odstraňování potíží v uvedeném pořadí a opakujte operaci:<br>
**1. příčina: [Agent je nainstalovaný na virtuálním počítači, ale přestane reagovat (pro virtuální počítače s Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**2. příčina: [Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**3. důvod: [Nelze načíst stav snímku, nebo nelze pořídí snímek](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**    
**Příčina 4: [Rozšíření zálohování se nezdaří pro aktualizaci nebo načtení](#the-backup-extension-fails-to-update-or-load)**  
**Příčina 5: [Virtuální počítač nemá přístup k Internetu](#the-vm-has-no-internet-access)**

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError - nejde komunikovat s agentem virtuálního počítače kvůli stavu snímku

**Kód chyby:**: GuestAgentSnapshotTaskStatusError<br>
**Chybová zpráva**: Nejde komunikovat s agentem virtuálního počítače kvůli stavu snímku <br>

Po registraci a naplánovat virtuálního počítače pro služby Azure Backup zahájí zálohování úlohy tím, že komunikuje s virtuálního počítače rozšíření zálohování k vytvoření snímku bodu v čase. Snímek některý z následujících podmínek může zabránit se aktivuje. Pokud se snímek se aktivuje, může dojít k selhání zálohování. Proveďte následující kroky odstraňování potíží v uvedeném pořadí a opakujte operaci:  
**1. příčina: [Agent je nainstalovaný na virtuálním počítači, ale přestane reagovat (pro virtuální počítače s Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**2. příčina: [Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**3. důvod: [Virtuální počítač nemá přístup k Internetu](#the-vm-has-no-internet-access)**

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached - dosáhl maximálního limitu kolekcí bodů obnovení

**Kód chyby:**: UserErrorRpCollectionLimitReached <br>
**Chybová zpráva**: Bylo dosaženo maximálního limitu kolekcí bodů obnovení. <br>
* Tento problém může dojít, pokud zámek ve skupině prostředků. bod obnovení brání automatickému čištění bod obnovení.
* Tento problém může také dojít, pokud se aktivují více záloh za den. Aktuálně doporučujeme pouze jedna záloha za den jako rychlé RPs se uchovávají po dobu 7 dní a pouze 18 rychlé RPs můžou být spojené s virtuálním počítači v daném okamžiku. <br>

Doporučená akce:<br>
Pokud chcete tento problém vyřešit, odeberte zámek na skupinu prostředků virtuálního počítače a zkuste operaci zopakovat a aktivovat čištění.
> [!NOTE]
    > Služba Backup vytvoří samostatné skupiny prostředků než skupina prostředků virtuálního počítače k uložení kolekci bodů obnovení. Zákazníkům doporučujeme není zamknout skupiny prostředků vytvořené pro použití službou Backup. Formát názvu skupiny prostředků vytvořené pomocí služby Backup je: AzureBackupRG_`<Geo>`_`<number>` Eg: AzureBackupRG_northeurope_1

**Krok 1: [Odebrat zámek ze skupiny prostředků bodů obnovení](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Krok 2: [Odstranit kolekci bodů obnovení](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured – Backup nemá dostatečná oprávnění k trezoru klíčů pro zálohování šifrovaných virtuálních počítačů.

**Kód chyby:**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Chybová zpráva**: Zálohování nemá dostatečná oprávnění k trezoru klíčů pro zálohování šifrovaných virtuálních počítačů. <br>

Záložní operace úspěšná na šifrovaných virtuálních počítačů musí mít oprávnění pro přístup k trezoru klíčů. To lze provést pomocí [webu Azure portal](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption#provide-permissions-to-backup) nebo prostřednictvím [prostředí PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection)

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork - operace snímku nebyla úspěšná kvůli bez připojení k síti na virtuálním počítači

**Kód chyby:**: ExtensionSnapshotFailedNoNetwork<br>
**Chybová zpráva**: Operace vytvoření snímku se nepovedla, protože virtuální počítač není připojený k síti<br>

Po registraci a naplánovat virtuálního počítače pro služby Azure Backup zahájí zálohování úlohy tím, že komunikuje s virtuálního počítače rozšíření zálohování k vytvoření snímku bodu v čase. Snímek některý z následujících podmínek může zabránit se aktivuje. Pokud se snímek se aktivuje, může dojít k selhání zálohování. Proveďte následující kroky odstraňování potíží v uvedeném pořadí a opakujte operaci:    
**1. příčina: [Nelze načíst stav snímku, nebo nelze pořídí snímek](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**2. příčina: [Rozšíření zálohování se nezdaří pro aktualizaci nebo načtení](#the-backup-extension-fails-to-update-or-load)**  
**3. důvod: [Virtuální počítač nemá přístup k Internetu](#the-vm-has-no-internet-access)**

## <a name="ExtentionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtentionOperationFailedForManagedDisks - operace rozšíření VMSnapshot selhala

**Kód chyby:**: ExtentionOperationFailedForManagedDisks <br>
**Chybová zpráva**: Operace rozšíření VMSnapshot selhala<br>

Po registraci a naplánovat virtuálního počítače pro služby Azure Backup zahájí zálohování úlohy tím, že komunikuje s virtuálního počítače rozšíření zálohování k vytvoření snímku bodu v čase. Snímek některý z následujících podmínek může zabránit se aktivuje. Pokud se snímek se aktivuje, může dojít k selhání zálohování. Proveďte následující kroky odstraňování potíží v uvedeném pořadí a opakujte operaci:  
**1. příčina: [Nelze načíst stav snímku, nebo nelze pořídí snímek](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**2. příčina: [Rozšíření zálohování se nezdaří pro aktualizaci nebo načtení](#the-backup-extension-fails-to-update-or-load)**  
**3. důvod: [Agent je nainstalovaný na virtuálním počítači, ale přestane reagovat (pro virtuální počítače s Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Příčina 4: [Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 – Zálohování selhalo s interní chybou

**Kód chyby:**: BackUpOperationFailed / BackUpOperationFailedV2 <br>
**Chybová zpráva**: Zálohování selhalo s interní chybou – Zkuste prosím tuto operaci za pár minut <br>

Po registraci a naplánovat virtuálního počítače pro služby Azure Backup zahájí zálohování úlohy tím, že komunikuje s virtuálního počítače rozšíření zálohování k vytvoření snímku bodu v čase. Snímek některý z následujících podmínek může zabránit se aktivuje. Pokud se snímek se aktivuje, může dojít k selhání zálohování. Proveďte následující kroky odstraňování potíží v uvedeném pořadí a opakujte operaci:  
**1. příčina: [Agent nainstalovaný ve virtuálním počítači, ale přestane reagovat (pro virtuální počítače s Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**2. příčina: [Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**3. důvod: [Nelze načíst stav snímku, nebo nelze pořídí snímek](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Příčina 4: [Rozšíření zálohování se nezdaří pro aktualizaci nebo načtení](#the-backup-extension-fails-to-update-or-load)**  
**Příčina 5: [Služba zálohování nemá oprávnění k odstranění staré body obnovení z důvodu zámku skupiny prostředků](#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock)** <br>
**Příčina 6: [Virtuální počítač nemá přístup k Internetu](#the-vm-has-no-internet-access)**

## <a name="usererrorunsupporteddisksize---currently-azure-backup-does-not-support-disk-sizes-greater-than-1023gb"></a>UserErrorUnsupportedDiskSize – aktuálně Azure Backup nepodporuje disky větší než 1023GB

**Kód chyby:**: UserErrorUnsupportedDiskSize <br>
**Chybová zpráva**: Azure Backup momentálně nepodporuje disky větší než 1023 GB <br>

Vaše operace zálohování může selhat při zálohování virtuálního počítače s velikostí disku větší než 1023GB, protože trezoru není upgradována rychlé obnovení. Upgrade na rychlé obnovení bude poskytovat podporu až na 4TB, najdete v tomto [článku](backup-instant-restore-capability.md).  

## <a name="usererrorstandardssdnotsupported---currently-azure-backup-does-not-support-standard-ssd-disks"></a>UserErrorStandardSSDNotSupported – aktuálně Azure Backup nepodporuje disky SSD na úrovni Standard

**Kód chyby:**: UserErrorStandardSSDNotSupported <br>
**Chybová zpráva**: Azure Backup momentálně nepodporuje disky SSD na úrovni Standard <br>

Azure Backup teď podporuje disky SSD na úrovni Standard pouze pro trezory služby, které jsou upgradovány na [rychlé obnovení](backup-instant-restore-capability.md).


## <a name="causes-and-solutions"></a>Příčiny a řešení

### <a name="the-vm-has-no-internet-access"></a>Virtuální počítač nemá přístup k Internetu
Za požadavek na nasazení virtuální počítač nemá přístup k Internetu. Nebo může mít omezení, které brání přístupu infrastrukturou Azure.

Správné fungování rozšíření Backup vyžaduje připojení k veřejným IP adresám Azure. Rozšíření odesílá příkazy do služby Azure storage koncový bod (adresy URL HTTPs) ke správě snímky virtuálního počítače. Pokud přípona nemá přístup k veřejným Internetem, zálohování nakonec dojde k chybě.

####  <a name="solution"></a>Řešení
Chcete-li vyřešit problém sítě, naleznete v tématu [navázat připojení k síti](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agent je nainstalovaný na virtuálním počítači, ale přestane reagovat (pro virtuální počítače s Windows)

#### <a name="solution"></a>Řešení
Agent virtuálního počítače může být poškozený nebo služba zastavila. Opětovná instalace agenta virtuálního počítače pomáhá získat nejnovější verzi. Pomáhá také restartovat komunikaci se službou.

1. Určení, zda se službou Windows agenta hosta Azure VM službami (services.msc). Pokuste se restartovat službu agenta hosta Azure pro Windows a zahajte zálohování.    
2. Pokud nejsou viditelná ve službách, v Ovládacích panelech, službu agenta hosta Azure pro Windows, přejděte na **programy a funkce** k určení, zda je nainstalována Služba agenta hosta Azure pro Windows.
4. Pokud agenta hosta Azure Windows se zobrazí v **programy a funkce**, odinstalujte agenta hosta Azure Windows.
5. Stáhněte a nainstalujte [nejnovější verzi MSI agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Musíte mít oprávnění správce k dokončení instalace.
6. Ověřte, že služby agenta hosta Azure pro Windows do služby.
7. Spusťte zálohu na vyžádání:
    * Na portálu vyberte **zálohovat nyní**.

Kromě toho ověřte, že [je nainstalované rozhraní Microsoft .NET 4.5](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ve virtuálním počítači. Rozhraní .NET 4.5 je vyžadován pro agenta virtuálního počítače ke komunikaci se službou.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)

#### <a name="solution"></a>Řešení
Většina souvisejících s agenty nebo souvisejících s rozšířením selhání pro virtuální počítače s Linuxem jsou způsobeny problémy, které ovlivňují zastaralé agenta virtuálního počítače. Chcete-li tento problém vyřešit, postupujte podle následujících obecných pokynů:

1. Postupujte podle pokynů pro [aktualizace agenta virtuálního počítače s Linuxem](../virtual-machines/linux/update-agent.md).

 > [!NOTE]
 > Jsme *důrazně doporučujeme* , že aktualizujete agenta pouze prostřednictvím distribuce úložiště. Nedoporučujeme stažením kódu agenta přímo z Githubu a aktualizacích. Pokud nejnovější verzi agenta pro vaši distribuci není k dispozici, požádejte distribuce podporu pokyny o tom, jak ji nainstalovat. Vyhledat nejnovější agent, přejděte [Windows Azure Linux agent](https://github.com/Azure/WALinuxAgent/releases) stránky v úložišti GitHub.

2. Ujistěte se, že Azure agent běží na virtuálním počítači spuštěním následujícího příkazu: `ps -e`

 Pokud proces není spuštěn, můžete ji restartujte pomocí následujících příkazů:

 * Pro Ubuntu: `service walinuxagent start`
 * Pro jiné distribuce: `service waagent start`

3. [Konfigurace automatického restartování agenta](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Spusťte nové zálohování testu. Pokud chyba přetrvává, shromážděte následující protokoly z virtuálního počítače:

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * / var/protokolu/azure / *

Pokud pro waagent vyžadujeme podrobné protokolování, postupujte podle těchto kroků:

1. V souboru /etc/waagent.conf vyhledejte následující řádek: **Zapnout podrobné protokolování (y | n)**
2. Změnit **Logs.Verbose** hodnotu *n* k *y*.
3. Uložte změny a potom restartujte waagent provedením kroků popsaných dříve v této části.

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Nelze načíst stav snímku, nebo nelze pořídí snímek
Zálohování virtuálních počítačů se spoléhá na vydání příkazu snímku do podkladového účtu úložiště. Zálohování může selhat, protože nemá přístup k účtu úložiště, nebo zpoždění spuštění úlohy snímku.

#### <a name="solution"></a>Řešení
Následující podmínky mohou způsobit selhání úlohy snímku:

| Příčina | Řešení |
| --- | --- |
| Stav virtuálního počítače je uvedena nesprávně, protože virtuální počítač je vypnutý v protokolu RDP (Remote Desktop). | Pokud vypnete virtuální počítač v protokolu RDP, podívejte se na portál k určení, zda je stav virtuálního počítače správný. Pokud není správný, vypněte virtuální počítač na portálu pomocí **vypnutí** možnost na řídicím panelu virtuálních počítačů. |
| Virtuální počítač nelze získat adresu hostitele nebo prostředků infrastruktury ze serveru DHCP. | DHCP musí být povolené uvnitř hosta pro zálohování virtuálních počítačů IaaS pro práci. Pokud virtuální počítač nemůže získat adresu hostitele nebo prostředků infrastruktury z odpovědi DHCP 245, se nedá stáhnout nebo ji spustit žádná rozšíření. Pokud potřebujete statickou privátní IP adresu, měli byste nakonfigurovat prostřednictvím **webu Azure Portal** nebo **Powershellu** a ujistěte se, že je povolená možnost DHCP ve virtuálním počítači. Další informace o tom, jak nastavit statickou IP adresu prostřednictvím prostředí PowerShell, najdete v tématu [klasický virtuální počítač](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm) a [virtuální počítač Resource Manageru](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface).

### <a name="the-backup-extension-fails-to-update-or-load"></a>Rozšíření zálohování se nezdaří pro aktualizaci nebo načtení
Pokud rozšíření nelze načíst, zálohování se nezdaří, protože nelze pořídí snímek.

#### <a name="solution"></a>Řešení

Odinstalujte rozšíření přinutit rozšíření VMSnapshot znovu načíst. Další pokus o zálohování znovu načte rozšíření.

Chcete-li odinstalovat rozšíření:

1. V [webu Azure portal](https://portal.azure.com/), přejděte k virtuálnímu počítači, na kterém dochází k selhání zálohování.
2. Vyberte **nastavení**.
3. Vyberte **Extensions** (Rozšíření).
4. Vyberte **rozšíření Vmsnapshot**.
5. Vyberte **odinstalovat**.

Pro virtuální počítač s Linuxem, pokud rozšíření VMSnapshot není uveden na webu Azure Portal [aktualizace agenta Azure Linux](../virtual-machines/linux/update-agent.md), a pak spusťte zálohování.

Dokončení tohoto postupu způsobí, že rozšíření znovu při dalším zálohování.

### <a name="remove_lock_from_the_recovery_point_resource_group"></a>Odebrat zámek ze skupiny prostředků bodů obnovení
1. Přihlaste se k webu [Azure Portal](http://portal.azure.com/).
2. Přejděte na **možnost všechny prostředky**, vyberte skupinu prostředků kolekce bodů obnovení v následujícím formátu AzureBackupRG_`<Geo>`_`<number>`.
3. V **nastavení** vyberte **zámky** zobrazíte zámků.
4. Odebrat zámek, vyberte tři tečky a klikněte na **odstranit**.

    ![Zámek proti odstranění ](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a> Odstranit kolekci bodů obnovení
Po ze zařízení zámek odebral, mají body obnovení na vyčištění. Chcete-li vyčistit body obnovení, postupovat podle některého z metody:<br>
* [Odstranit kolekci bodů obnovení ve spuštěné zálohování ad hoc](#clean-up-restore-point-collection-by-running-ad-hoc-backup)<br>
* [Vyčistit obnovení bodu kolekce z webu Azure portal](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-ad-hoc-backup"></a>Odstranit kolekci bodů obnovení ve spuštěné zálohování ad hoc
Po odebrání zámku aktivace ad-hoc nebo ruční zálohování. Tím se zajistí, že se že body obnovení automaticky vyčištěna. Očekávané selhání poprvé; tato operace ad-hoc nebo ruční však zajistí automatické čištění místo ruční odstranění bodů obnovení. Po vyčištění uspěli na další naplánované zálohování.

> [!NOTE]
    > Automatické čištění se stane po několik hodin spouští se záloha ad-hoc nebo ruční. Pokud vaše naplánované zálohování se nezdaří, pak zkuste ručně odstranit kolekci bodů obnovení pomocí kroků uvedených [tady](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>Vyčistit obnovení bodu kolekce z webu Azure portal <br>

Ručně vymažte obnovení bodů kolekce, které nejsou zrušeno z důvodu zámku na skupinu prostředků, vyzkoušejte následující kroky:
1. Přihlaste se k webu [Azure Portal](http://portal.azure.com/).
2. Na **centra** nabídky, klikněte na tlačítko **všechny prostředky**, vyberte skupinu prostředků v následujícím formátu AzureBackupRG_`<Geo>`_`<number>` kde je umístěn virtuální počítač.

    ![Zámek proti odstranění ](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Klikněte na skupinu prostředků **přehled** zobrazí se okno.
4. Vyberte **zobrazit skryté typy** možnost zobrazit všechny skryté prostředky. Vyberte kolekce bodů obnovení v následujícím formátu AzureBackupRG_`<VMName>`_`<number>`.

    ![Zámek proti odstranění ](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Klikněte na tlačítko **odstranit**, vyčistit kolekci bodů obnovení.
6. Zkuste operaci zálohování znovu.
