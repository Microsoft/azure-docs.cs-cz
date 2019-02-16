---
title: Zálohování virtuálních počítačů VMware pomocí Azure Backup serveru
description: Použití Azure Backup serveru k zálohování virtuálních počítačů VMware, které běží na serveru VMware vCenter/ESXi.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: raynew
ms.openlocfilehash: 5e5a6f32eeac674a6527d333b981bbdac20a9958
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56309757"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Zálohování virtuálních počítačů VMware pomocí Azure Backup serveru

Tento článek vysvětluje, jak zálohovat virtuální počítače VMware spuštěné na hostitelích VMware ESXi a vCenter Server do Azure pomocí Azure Backup serveru. 

Tento článek vysvětluje, jak:

- Nastavení zabezpečeného kanálu tak, aby Azure Backup Server může komunikovat se servery VMware prostřednictvím protokolu HTTPS.
- Nastavení účtu VMware, který Azure Backup Server používá pro přístup k serveru VMware.
- Přidáte přihlašovací údaje účtu do služby Azure Backup.
- Přidání serveru vCenter a ESXi server do Azure Backup serveru.
- Nastavte skupinu ochrany, která obsahuje virtuální počítače VMware, které chcete zálohovat, zadejte nastavení zálohování a plánování zálohování.

## <a name="before-you-start"></a>Než začnete
- Ověřte, že používáte verzi vCenter/ESXi, který se podporuje pro zálohování - verze 6.5, 6.0 nebo 5.5. 
- Ujistěte se, že jste nastavili Azure Backup serveru. Pokud jste tak dosud [udělat](backup-azure-microsoft-azure-backup.md) před zahájením. By měl běžet Azure Backup serveru s nejnovějšími aktualizacemi.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Vytvořit zabezpečené připojení k serveru vCenter

Ve výchozím nastavení Azure Backup serveru komunikuje se servery VMware prostřednictvím protokolu HTTPS. Nastavení připojení protokolu HTTPS, stáhněte si certifikát VMware certifikační autoritu (CA) a importujte ho na Azure Backup serveru. 


### <a name="before-you-start"></a>Než začnete

- Pokud už nechcete používat HTTPS můžete [zakázat výchozí nastavení](backup-azure-backup-server-vmware.md).
- Obvykle připojíte z prohlížeče na počítači Azure Backup serveru k serveru vCenter/ESXi pomocí webovém klientovi vSphere. Při prvním uděláte připojení není zabezpečený a zobrazí následující.
- Je důležité pochopit, jak Azure Backup Server zpracovává zálohy.
    - Jako první krok serveru Azure Backup zálohuje data do úložiště na místním disku. Azure Backup Server používá fond úložiště, sada disků a svazků, na kterých Azure Backup Server ukládá disku body obnovení pro chráněná data. Fond úložiště může být přímo připojeným úložištěm (DAS), Fibre channel SAN nebo iSCSI úložné zařízení nebo SAN. Je důležité zajistit, že máte dostatečné úložiště pro místní back up dat virtuálního počítače VMware.
    - Azure Backup Server pak zálohuje z úložiště na místním disku do Azure.
    - [Získejte pomoc](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need) zjistit, kolik úložného prostoru, budete potřebovat. Informace je aplikace DPM ale můžete použít pro Azure Backup serveru příliš.

### <a name="set-up-the-certificate"></a>Nastavení certifikátu 

Zabezpečený kanál nastavte následujícím způsobem:

1. V prohlížeči na serveru Azure Backup zadejte adresy URL webového klienta vSphere. Pokud na přihlašovací stránce nezobrazí, ověřte nastavení proxy serveru připojení a prohlížeče.

    ![Webovém klientovi vSphere](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. Na přihlašovací stránku ve webovém klientovi vSphere klikněte na tlačítko **stahování důvěryhodné kořenové Certifikační autority**. 

    ![Stáhněte si certifikát důvěryhodné kořenové certifikační Autority](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. Soubor s názvem **Stáhnout** je stažen. V závislosti na prohlížeči obdržíte zprávu s dotazem, jestli se má otevřít nebo uložit soubor.

    ![Stáhnout certifikát certifikační Autority](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Uložte soubor na počítači Azure Backup serveru s příponou .zip.

5. Klikněte pravým tlačítkem na **download.zip** > **extrahovat všechny**. Extrahuje svůj obsah rozbalí do souboru ZIP **certifikátů** složky, která obsahuje:
    - Soubor kořenového certifikátu s příponou, který začíná číslem pořadí jako.0 a.1.
    - Soubor seznamu CRL má příponu, která začíná s pořadím jako .r0 nebo .r1. Soubor seznamu CRL je přidružen k certifikátu.

    ![Stažený certifikáty](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. V **certifikátů** složky, klikněte pravým tlačítkem na soubor kořenového certifikátu > **přejmenovat**.

    ![Přejmenovat kořenového certifikátu ](./media/backup-azure-backup-server-vmware/rename-cert.png)

6. Změňte příponu kořenový certifikát na .crt a potvrďte. Ikona, který představuje kořenový certifikát.

7. Klikněte pravým tlačítkem na kořenový certifikát a v místní nabídce vyberte **nainstalovat certifikát**. 

8. V **Průvodce importem certifikátu**vyberte **místního počítače** jako cíl pro certifikát a pak klikněte na tlačítko **Další**. Potvrďte, pokud budete vyzváni, pokud chcete povolit změny v počítači.

    ![Vítá vás Průvodce](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)
 

9. Na **certifikát Store** stránce **všechny certifikáty umístit v následujícím úložišti**a potom klikněte na tlačítko **Procházet** vybrat úložiště certifikátů.

    ![Úložiště certifikátů](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

10. V **Store vyberte certifikát**vyberte **důvěryhodných kořenových certifikačních autorit** jako cílovou složku pro certifikáty a pak klikněte na tlačítko **OK**.

    ![Certifikát cílovou složku](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

11. V **dokončení Průvodce importem certifikátu**, zkontrolujte složku a klikněte na **Dokončit**.

    ![Ověřte, zda je certifikát do správné složky](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

    
12. Po importu certifikátu je potvrzen, přihlaste se k serveru vCenter potvrďte, že připojení je zabezpečené.


  

### <a name="disable-default-https"></a>Zakázat výchozí HTTPS

Pokud máte zabezpečené hranice ve vaší organizaci a nechcete používat protokol HTTPS mezi servery VMware a Azure Backup serveru počítače, zakažte HTTPS následujícím způsobem: u
1. Zkopírujte a vložte následující text do souboru .txt.

      ```
      Windows Registry Editor Version 5.00
      [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
      "IgnoreCertificateValidation"=dword:00000001
      ```

2. Uložte soubor na počítači Azure Backup serveru s názvem **DisableSecureAuthentication.reg**.

3. Poklikejte na soubor k aktivaci položky registru.


## <a name="create-a-vmware-role"></a>Umožňuje vytvořit roli VMware

Azure Backup Server potřebuje účet uživatele s oprávněními pro přístup k serveru Vcenter nebo hostiteli ESXi. Umožňuje vytvořit roli VMware s konkrétní oprávnění a přidružte účet uživatele s rolí.

1. Přihlaste se k serveru vCenter (nebo hostiteli ESXi, pokud nepoužíváte vCenter Server).
2. V **Navigátor** panelu, klikněte na tlačítko **správu**.

    ![Správa ](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. V **správu** > **role**, klikněte na ikonu Přidat roli (+ symbol).

    ![Přidat roli](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

    
4. V **vytvořit roli** > **název Role**, zadejte *BackupAdminRole*. Název role může být cokoli, co chcete, ale měla by být rozpoznatelných pro účely této role.

5. Vyberte oprávnění popsaná v následující tabulce a potom klikněte na tlačítko **OK**.  Nová role se zobrazí v seznamu **role** panelu.
    - Klikněte na ikonu vedle nadřazený popisek nadřazeného rozbalit a zobrazit podřízené oprávnění.
    - Vybrat oprávnění jako virtuální počítač, budete muset přejít do hierarchie nadřazený-podřízený několik úrovní.
    - Nemusíte vybrat všechny podřízené oprávnění v rámci nadřazené oprávnění.

    ![Oprávnění hierarchie nadřazený-podřízený](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Oprávnění role
**6.5/6.0** | **5.5**
--- | ---
Datastore.AllocateSpace | Datastore.AllocateSpace
Global.ManageCustomFields | Global.ManageCustomFields
Global.SetCustomField | 
Host.Local.CreateVM | Network.Assign 
Network.Assign | 
Resource.AssignVMToPool | 
VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   
VirtualMachine.Config.AdvancedConfig| VirtualMachine.Config.AdvancedConfig
VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking 
VirtualMachine.Config.HostUSBDevice | 
VirtualMachine.Config.QueryUnownedFiles | 
VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement 
VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff 
VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create 
VirtualMachine.Provisioning.DiskRandomAccess | 
VirtualMachine.Provisioning.DiskRandomRead | VirtualMachine.Provisioning.DiskRandomRead
VirtualMachine.State.CreateSnapshot | VirtualMachine.State.CreateSnapshot
VirtualMachine.State.RemoveSnapshot | VirtualMachine.State.RemoveSnapshot 




## <a name="create-a-vmware-account"></a>Vytvořte účet VMware

1. V systému vCenter Server **Navigátor** panelu, klikněte na tlačítko **uživatelů a skupin**. Pokud nepoužíváte vCenter Server, vytvořte účet na vhodného hostitele ESXi.

    ![Možnost uživatelé a skupiny](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    **VCenter uživatelů a skupin** otevře se panel.


2. V **vCenter uživatelů a skupin** panelu, vyberte **uživatelé** kartu a potom klikněte na položku Přidat uživatele (+ symbol).

     ![vCenter uživatele a skupiny panelů](./media/backup-azure-backup-server-vmware/usersandgroups.png)


3. V **nového uživatele** dialogovém okně Přidat informace o uživateli > **OK**. Uživatelské jméno v tomto postupu je BackupAdmin.

    ![Dialogové okno Nový uživatel](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)


4. Přidružení uživatelského účtu s rolí v **Navigátor** panelu, klikněte na tlačítko **globální oprávnění**. V **globální oprávnění** panelu, vyberte **spravovat** kartu a potom klikněte na položku Přidat (+ symbol).

    ![Globální oprávnění panel](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)


5. V **globální oprávnění Root - přidat oprávnění**, klikněte na tlačítko **přidat** vybrat uživatele nebo skupinu.

    ![Zvolte uživatele nebo skupinu](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. V **vyberte uživatele nebo skupiny**, zvolte **BackupAdmin** > **přidat**. V **uživatelé**, *doména\uživatelské jméno* formátu se používá pro uživatelský účet. Pokud chcete použít jinou doménu, vyberte jej ze **domény** seznamu. Klikněte na tlačítko **OK** přidat vybraným uživatelům umožní **přidat oprávnění** dialogové okno.

    ![Přidání BackupAdmin uživatele](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)


7.  V **přiřazené Role**, v rozevíracím seznamu vyberte **BackupAdminRole** > **OK**.

    ![Přiřadit uživatele k roli](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)


Na **spravovat** kartu **globální oprávnění** panelu, nový uživatelský účet a přidružené role se zobrazí v seznamu.


## <a name="add-the-account-on-azure-backup-server"></a>Přidat účet v Azure Backup serveru


1. Otevřete Azure Backup serveru. Pokud nemůžete najít ikony na ploše, otevřete Microsoft Azure Backup ze seznamu aplikací.

    ![Ikona Azure Backup serveru](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. V konzole Azure Backup serveru klikněte na **správu** >  **provozní servery** > **spravovat VMware**.

    ![Konzole Azure Backup serveru](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)


3. V **spravovat přihlašovací údaje** dialogové okno, klikněte na tlačítko **přidat**.

    ![Dialogové okno Azure Backup Server spravovat přihlašovací údaje](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. V **přidat přihlašovací údaje** , zadejte název a popis nové přihlašovací údaje a zadejte uživatelské jméno a heslo, které jsou definovány na serveru VMware. Název, *přihlašovacích údajů Contoso Vcenter* slouží k identifikaci přihlašovací údaje, které v tomto postupu. Pokud VMware server a Azure Backup serveru nejsou ve stejné doméně, zadejte doménu v uživatelské jméno.

    ![Dialogové okno Azure Backup Server přidat přihlašovací údaje](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Klikněte na tlačítko **přidat** přidat nový přihlašovací údaj.

    ![Dialogové okno Azure Backup Server spravovat přihlašovací údaje](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)


## <a name="add-the-vcenter-server"></a>Přidání serveru vCenter 

Přidání serveru vCenter do Azure Backup serveru.


1. V konzole Azure Backup serveru klikněte na **správu** > **provozní servery** > **přidat**.

    ![Průvodce přidáním otevřít provozního serveru](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)
   

2. V **Průvodce přidáním provozního serveru** > **typ provozního serveru vyberte** stránce **servery VMware**a potom klikněte na tlačítko **Další**.

     ![Průvodce přidáním provozního serveru](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. V **vybrat počítače****název nebo IP adresa serveru**, zadejte plně kvalifikovaný název domény nebo IP adresa serveru VMware. Pokud všechny servery ESXi spravuje stejný počítač vCenter, zadejte název serveru vCenter. V opačném případě přidání hostitele ESXi.

    ![Zadejte VMware server](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. V **SSL Port**, zadejte port, který se používá ke komunikaci se serverem VMware. Výchozí port je 443, ale můžete ho změnit, pokud VMware server naslouchá na jiném portu.

5. V **zadejte přihlašovací údaje**, vyberte přihlašovací údaj, který jste vytvořili dříve.

    ![Zadejte přihlašovací údaje](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Klikněte na tlačítko **přidat** přidání serveru VMware do seznamu serverů. Pak klikněte na tlačítko **Další**.

    ![Přidání serveru VMWare a přihlašovacích údajů](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. V **Souhrn** klikněte na **přidat** přidání serveru VMware do Azure Backup serveru. Nový server se přidá okamžitě, že je potřeba žádný agent na serveru VMware.

    ![Přidání serveru VMware do Azure Backup serveru](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Ověřte nastavení **Dokončit** stránky.

  ![Stránka dokončení](./media/backup-azure-backup-server-vmware/summary-screen.png)

Pokud máte více hostitelů ESXi, které nejsou spravovány serverem vCenter, nebo máte více instancí systému vCenter Server, budete muset znovu spustit průvodce a přidejte servery. 




## <a name="configure-a-protection-group"></a>Konfigurace skupiny ochrany

Přidání virtuálních počítačů VMware pro zálohy. Skupiny ochrany shromáždit několik virtuálních počítačů a použít stejné uchovávání dat a nastavení zálohování pro všechny virtuální počítače ve skupině. 


1. V konzole Azure Backup serveru klikněte na **ochrany**, > **nový**.

    ![Otevřete Průvodce vytvořením nové skupiny ochrany](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

2. V **vytvořením nové skupiny ochrany** úvodní stránka průvodce, klikněte na tlačítko **Další**.

    ![Dialogové okno Průvodce vytvořením nové skupiny ochrany](./media/backup-azure-backup-server-vmware/protection-wizard.png)

3. Na **typ skupiny ochrany vyberte** stránce **servery** a potom klikněte na tlačítko **Další**. **Vybrat členy skupiny** se zobrazí stránka.

3. V **vybrat členy skupiny** > vyberte virtuální počítače (nebo složek virtuálního počítače), který chcete zálohovat. Pak klikněte na tlačítko **Další**.

    - Když vyberete složku nebo virtuální počítače nebo složky v této složce jsou také vybrané pro zálohování. Není nutné zálohovat virtuální počítače nebo složky, můžete zrušit zaškrtnutí.
- Pokud virtuální počítač nebo složka již během zálohování, nelze ji vyberte. Tím je zajištěno, že pro virtuální počítač nejsou vytvořeny body obnovení duplicitní. .

    ![Vybrat členy skupiny](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)


4. V **vybrat způsob ochrany dat** stránky, zadejte název pro skupinu ochrany a nastavení ochrany. Zpět do Azure, nastavte krátkodobou ochranu na **disku** a povolení online ochrany. Pak klikněte na tlačítko **Další**.

    ![Vyberte způsob ochrany dat](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. V **zadat krátkodobé cíle**, určete, jak dlouho chcete uchovávat data zálohovat na disk.
    - V **rozsah uchování**, zadejte, kolik dní se uchovávají body obnovení disku. 
    - V **četnost synchronizací**, určete, jak často jsou pořizovány body obnovení disku.
        - Pokud nechcete nastavovat interval zálohování můžete zkontrolovat **těsně před bodem obnovení** tak, aby zálohování těsně před každým bodem obnovení naplánován.
        - Krátkodobé zálohy jsou úplné zálohy, ne přírůstková.
        - Klikněte na tlačítko **změnit** změnit časy a data při výskytu krátkodobé zálohy.

    ![Zadat krátkodobé cíle](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. V **zkontrolovat přidělení disku**, zkontrolujte místo na disku k dispozici pro zálohy virtuálních počítačů. pro virtuální počítače.

    - Doporučená přidělení disku jsou založené na rozsahu uchování, kterou jste zadali, typu úlohy a velikosti chráněných dat. Proveďte potřebné změny a pak klikněte na tlačítko **Další**.
    -  **Velikost dat:** Velikost dat ve skupině ochrany.
    - **Místo na disku:** Doporučené množství místa na disku pro skupinu ochrany. Pokud chcete toto nastavení změnit, měli byste přidělit celkové místo, které je o něco větší než hodnota, která odhadnout, že každý zdroj dat roste.
    - **Společné umístění dat:** Pokud povolíte společné umístění, můžete namapovat více zdrojů dat v oblasti ochrany na jednu repliku a svazek bodu obnovení. Společné umístění se nepodporuje pro všechny úlohy.
    - **Automaticky zvětšit:** Pokud zapnete toto nastavení, pokud data ve skupině ochrany přesáhnou předběžné přidělení počáteční, Azure Backup serveru se pokusí zvětšit velikost disku o 25 procent.
    - **Podrobnosti fondu úložiště:** Zobrazuje stav fondu úložiště včetně celkové a zbývající velikosti disku.

    ![Zkontrolovat přidělení disku](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

7. V **vyberte způsob vytvoření repliky** určete, jak chcete počáteční zálohu a poté klikněte na tlačítko, **Další**.
    - Výchozí hodnota je **automaticky přes síť** a **nyní**.
    - Pokud používáte výchozí nastavení, doporučujeme, že zadáte čas mimo špičku. Zvolte **později** a zadejte datum a čas.
    - Pro velké objemy dat nebo méně než optimální síťové podmínky zvažte replikaci dat offline pomocí vyměnitelného média.

    ![Vyberte způsob vytvoření repliky](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. V **možnosti kontroly konzistence**, vyberte, jak a kdy chcete automatizovat kontroly konzistence. Pak klikněte na tlačítko **Další**.
    - Kontroly konzistence můžete spustit, když se stane nekonzistentní data repliky, nebo podle nastaveného plánu.
    - Pokud nechcete konfigurovat Automatická kontrola konzistence, můžete spustit ruční kontrolu. Chcete-li to provést, klikněte pravým tlačítkem na skupinu ochrany > **provést kontrolu konzistence**.

9. V **zadat Data Online ochrany** vyberte virtuální počítač nebo virtuální počítače, složky, které chcete zálohovat. Vyberte členy jednotlivě, nebo klikněte na **Vybrat vše** zvolit všechny členy. Pak klikněte na tlačítko **Další**.

    ![Zadat data online ochrany](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. Na **zadat plán Online zálohování** stránce, určete, jak často chcete zálohovat data z místního úložiště do Azure.

    - Cloudových bodů obnovení pro data vygeneruje podle plánu. Pak klikněte na tlačítko **Další**.
    - Po vygenerování bodu obnovení se přenesou do trezoru služby Recovery Services v Azure. 
    
    ![Zadejte plán online zálohování.](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. Na **zadat zásady Online uchovávání** stránce, označuje, jak dlouho chcete zachovat body obnovení, které jsou vytvořeny z denních, týdenních nebo měsíčních/ročních záloh do Azure. Pak klikněte na tlačítko **Další**.

    - Neexistuje žádný časový limit pro dobu můžete ponechat data v Azure.
    - Jediným limitem je, že nemůže mít více než 9999 bodů obnovení na chráněnou instanci. V tomto příkladu je chráněná instance serveru VMware.

    ![Zadejte zásady online uchovávání dat.](./media/backup-azure-backup-server-vmware/retention-policy.png)

   
12. Na **Souhrn** stránky, zkontrolujte nastavení a potom klikněte na tlačítko **vytvořit skupinu**.

    ![Souhrn nastavení a člena skupiny ochrany](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>Další postup

Řešení potíží s problémy při nastavení zálohy, najdete v tématu [Průvodce odstraňováním potíží pro Azure Backup serveru](./backup-azure-mabs-troubleshoot.md).
