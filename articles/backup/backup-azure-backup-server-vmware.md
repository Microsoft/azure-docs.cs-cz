---
title: Zálohování virtuálních počítačů VMware pomocí Azure Backup Server
description: V tomto článku se dozvíte, jak pomocí Azure Backup Server zálohovat virtuální počítače VMware běžící na serveru VMware vCenter/ESXi.
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: d1c8ec249e010d75bbe96f5c70072f41b9738370
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173356"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Zálohování virtuálních počítačů VMware pomocí Azure Backup Server

Tento článek vysvětluje, jak zálohovat virtuální počítače VMware běžící na VMware ESXi hostitele nebo vCenter Server do Azure pomocí Azure Backup Server.

V tomto článku se dozvíte, jak:

- Nastavte zabezpečený kanál tak, aby Azure Backup Server mohl komunikovat se servery VMware přes protokol HTTPS.
- Nastavte účet VMware, který Azure Backup Server používá pro přístup k serveru VMware.
- Přidejte přihlašovací údaje účtu do Azure Backup.
- Přidejte Server vCenter nebo ESXi do Azure Backup Server.
- Nastavte skupinu ochrany, která obsahuje virtuální počítače VMware, které chcete zálohovat, zadejte nastavení zálohování a naplánujte zálohování.

## <a name="before-you-start"></a>Než začnete

- Ověřte, že používáte verzi vCenter/ESXi, která je podporovaná pro záložní verze 6,5, 6,0 a 5,5.
- Ujistěte se, že jste nastavili Azure Backup Server. Pokud jste to ještě neudělali, [udělejte to předtím,](backup-azure-microsoft-azure-backup.md) než začnete. Měli byste používat Azure Backup Server s nejnovějšími aktualizacemi.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Vytvoření zabezpečeného připojení k vCenter Server

Ve výchozím nastavení Azure Backup Server komunikuje se servery VMware pomocí protokolu HTTPS. Pokud chcete nastavit připojení HTTPS, Stáhněte si certifikát certifikační autority (CA) VMware a naimportujte ho na Azure Backup Server.

### <a name="before-you-begin"></a>Než začnete

- Pokud nechcete používat protokol HTTPS, můžete [zakázat ověřování certifikátu HTTPS pro všechny servery VMware](backup-azure-backup-server-vmware.md#disable-https-certificate-validation).
- Obvykle se připojujete z prohlížeče na Azure Backup Server počítači k serveru vCenter/ESXi pomocí webového klienta vSphere. Když to uděláte poprvé, připojení není zabezpečené a zobrazí se následující.
- Je důležité pochopit, jak Azure Backup Server zpracovává zálohy.
  - V prvním kroku Azure Backup Server zálohují data na místní diskové úložiště. Azure Backup Server používá fond úložiště, sadu disků a svazků, na kterých Azure Backup Server ukládá body obnovení disku pro chráněná data. Fond úložiště může být přímo připojený k úložišti (DAS), síti SAN s technologií Fibre Channel nebo úložnému zařízení iSCSI nebo SAN. Je důležité zajistit, abyste měli dostatečné úložiště pro místní zálohování dat virtuálních počítačů VMware.
  - Azure Backup Server pak zálohuje úložiště místního disku do Azure.
  - [Získejte pomoc](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need) , jak zjistit, kolik úložného prostoru potřebujete. Informace jsou určeny pro aplikaci DPM, ale lze je použít také pro Azure Backup Server.

### <a name="set-up-the-certificate"></a>Nastavení certifikátu

Zabezpečený kanál nastavte následujícím způsobem:

1. V prohlížeči na Azure Backup Server zadejte adresu URL webového klienta vSphere. Pokud se stránka pro přihlášení nezobrazuje, zkontrolujte připojení a nastavení proxy prohlížeče.

    ![Webový klient vSphere](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. Na stránce přihlášení webového klienta vSphere klikněte na **Stáhnout certifikáty důvěryhodných kořenových certifikačních autorit**.

    ![Stáhnout certifikát důvěryhodné kořenové certifikační autority](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. Stáhne se soubor s názvem **Stáhnout** . V závislosti na prohlížeči se zobrazí zpráva s dotazem, zda chcete soubor otevřít nebo Uložit.

    ![Stáhnout certifikát certifikační autority](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Uložte soubor na Azure Backup Server počítači s příponou. zip.

5. Klikněte pravým tlačítkem na **Stáhnout. zip** > **Extrahovat vše**. Soubor. zip extrahuje obsah do složky **certifikáty** , která obsahuje:
   - Soubor kořenového certifikátu s příponou začínající číslovanou sekvencí, například. 0 a. 1.
   - Soubor CRL má příponu, která začíná sekvencí, jako je například. r0 nebo. R1. Soubor CRL je přidružený k certifikátu.

    ![Stažené certifikáty](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

6. Ve složce **certifikáty** klikněte pravým tlačítkem na soubor kořenového certifikátu > **Přejmenovat**.

    ![Přejmenovat kořenový certifikát](./media/backup-azure-backup-server-vmware/rename-cert.png)

7. Změňte rozšíření kořenového certifikátu na. CRT a potvrďte ho. Ikona souboru se změní na jednu, která představuje kořenový certifikát.

8. Klikněte pravým tlačítkem na kořenový certifikát a v místní nabídce vyberte **nainstalovat certifikát**.

9. V **Průvodci importem certifikátu**vyberte jako cíl pro certifikát **místní počítač** a pak klikněte na **Další**. Potvrďte, jestli se vám zobrazí dotaz, jestli chcete v počítači povolené změny.

    ![Vítá vás Průvodce](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

10. Na stránce **úložiště certifikátů** vyberte možnost **všechny certifikáty umístit v následujícím úložišti**a pak klikněte na tlačítko **Procházet** a vyberte úložiště certifikátů.

    ![Úložiště certifikátů](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

11. V části **vybrat úložiště certifikátů**jako cílovou složku pro certifikáty vyberte **Důvěryhodné kořenové certifikační autority** a pak klikněte na **OK**.

    ![Cílová složka certifikátu](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

12. V části **dokončení Průvodce importem certifikátu**ověřte složku a pak klikněte na **Dokončit**.

    ![Ověřte, jestli je certifikát ve správné složce.](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

13. Po potvrzení importu certifikátu se přihlaste k vCenter Server a ověřte, že je připojení zabezpečené.

### <a name="disable-https-certificate-validation"></a>Zakázat ověřování certifikátu HTTPS

Pokud máte v rámci vaší organizace zabezpečený rozsah a nechcete používat protokol HTTPS mezi servery VMware a Azure Backup Serverm počítačem, zakažte protokol HTTPS následujícím způsobem:

1. Zkopírujte následující text do souboru. txt a vložte ho do něj.

       ```text
      Editor registru systému Windows verze 5,00 [HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Microsoft data Protection Manager\VMWare] "IgnoreCertificateValidation" = DWORD: 00000001
       ```

2. Uložte soubor na Azure Backup Server počítači s názvem **DisableSecureAuthentication. reg**.

3. Dvojím kliknutím na soubor aktivujte položku registru.

## <a name="create-a-vmware-role"></a>Vytvoření role VMware

Azure Backup Server potřebuje uživatelský účet s oprávněními pro přístup k serveru v-Center nebo hostiteli ESXi. Vytvořte roli VMware s konkrétními oprávněními a pak přidružte uživatelský účet k roli.

1. Přihlaste se k vCenter Server (nebo hostiteli ESXi, pokud nepoužíváte vCenter Server).
2. Na panelu **navigátor** klikněte na možnost **Správa**.

    ![Správa](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. V části správa **rolí** > klikněte na ikonu Přidat roli (symbol +).

    ![Přidat roli](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. V > **vytvořit roli** **název role**zadejte *BackupAdminRole*. Název role může být libovolný, ale měl by být rozpoznatelný pro účel role.

5. Vyberte oprávnění shrnutá v následující tabulce a pak klikněte na **OK**.  Nová role se zobrazí v seznamu na panelu **role** .
   - Kliknutím na ikonu vedle nadřazeného popisku rozbalte nadřazenou položku a zobrazte podřízená oprávnění.
   - Pokud chcete vybrat oprávnění VirtualMachine, musíte přejít na několik úrovní v nadřazené hierarchii podřízenosti.
   - V rámci nadřazeného oprávnění nemusíte vybírat všechna podřízená oprávnění.

    ![Nadřazená podřízená hierarchie oprávnění](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Oprávnění role

**verze 6.5/6.0** | **5.5**
--- | ---
Úložiště dat. AllocateSpace | Úložiště dat. AllocateSpace
Global.ManageCustomFields | Global.ManageCustomFields
Global.SetCustomField |
Host.Local.CreateVM | Síť. přiřadit
Síť. přiřadit |
Resource.AssignVMToPool |
VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk
VirtualMachine. config. AdvancedConfig| VirtualMachine. config. AdvancedConfig
VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking
VirtualMachine. config. HostUSBDevice |
VirtualMachine.Config.QueryUnownedFiles |
VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement
VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff
VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create
VirtualMachine.Provisioning.DiskRandomAccess |
VirtualMachine.Provisioning.DiskRandomRead | VirtualMachine.Provisioning.DiskRandomRead
VirtualMachine.State.CreateSnapshot | VirtualMachine.State.CreateSnapshot
VirtualMachine. State. RemoveSnapshot | VirtualMachine. State. RemoveSnapshot

## <a name="create-a-vmware-account"></a>Vytvoření účtu VMware

1. Na panelu vCenter Server **navigátor** klikněte na **Uživatelé a skupiny**. Pokud vCenter Server nepoužíváte, vytvořte účet na příslušném hostiteli ESXi.

    ![Možnost Uživatelé a skupiny](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Zobrazí se panel **Uživatelé a skupiny vCenter** .

2. Na panelu **Uživatelé a skupiny vCenter** vyberte kartu **Uživatelé** a potom klikněte na ikonu Přidat uživatele (symbol +).

    ![panel Uživatelé a skupiny vCenter](./media/backup-azure-backup-server-vmware/usersandgroups.png)

3. V dialogovém okně **Nový uživatel** přidejte informace o uživateli > **OK**. V tomto postupu je uživatelské jméno BackupAdmin.

    ![Nový uživatel – dialogové okno](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

4. Chcete-li přidružit uživatelský účet k roli, klikněte na panelu **navigátor** na možnost **globální oprávnění**. Na panelu **globální oprávnění** vyberte kartu **Spravovat** a potom klikněte na ikonu Přidat (symbol +).

    ![Panel globálních oprávnění](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

5. V části **globální oprávnění root – přidat oprávnění**klikněte na tlačítko **Přidat** a vyberte uživatele nebo skupinu.

    ![Vybrat uživatele nebo skupinu](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. V **možnosti vybrat uživatele nebo skupiny**zvolte **BackupAdmin** > **Přidat**. V části **Uživatelé**se pro uživatelský účet používá formát *doména \ uživatelské_jméno* . Pokud chcete použít jinou doménu, vyberte ji ze seznamu **domén** . Kliknutím na tlačítko **OK** přidejte vybrané uživatele do dialogového okna **Přidat oprávnění** .

    ![Přidat uživatele BackupAdmin](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. V poli **přiřazená role**v rozevíracím seznamu vyberte **BackupAdminRole** > **OK**.

    ![Přiřazení uživatele k roli](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

Na kartě **Spravovat** na panelu **globální oprávnění** se v seznamu zobrazí nový uživatelský účet a přidružená role.

## <a name="add-the-account-on-azure-backup-server"></a>Přidat účet na Azure Backup Server

1. Otevřete Azure Backup Server. Pokud ikonu na ploše nemůžete najít, otevřete Microsoft Azure Backup ze seznamu aplikací.

    ![Ikona Azure Backup Server](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. V konzole Azure Backup Server klikněte na **správa** >  **provozní servery** > **Spravovat VMware**.

    ![Konzola Azure Backup Server](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. V dialogovém okně **Spravovat pověření** klikněte na tlačítko **Přidat**.

    ![Dialogové okno Azure Backup Server Správa přihlašovacích údajů](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. V části **Přidat pověření**zadejte název a popis nového přihlašovacího údaje a zadejte uživatelské jméno a heslo, které jste definovali na serveru VMware. V tomto postupu se k identifikaci přihlašovacích údajů používá název *Contoso vCenter přihlašovací údaje* . Pokud server VMware a Azure Backup Server nejsou ve stejné doméně, zadejte v uživatelském jménu doménu.

    ![Dialogové okno Azure Backup Server přidat pověření](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Kliknutím na **Přidat** přidejte nové přihlašovací údaje.

    ![Dialogové okno Azure Backup Server Správa přihlašovacích údajů](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>Přidat vCenter Server

Přidejte vCenter Server do Azure Backup Server.

1. V konzole Azure Backup Server klikněte na **správa** > **provozní servery** > **Přidat**.

    ![Průvodce přidáním provozního serveru](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. V **Průvodci přidáním provozního serveru** > **Vyberte stránku typ provozního serveru** , vyberte **servery VMware**a pak klikněte na **Další**.

    ![Průvodce přidáním provozního serveru](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. V části **Vybrat počítače**  **název serveru/IP adresa**zadejte plně kvalifikovaný název domény nebo IP adresu serveru VMware. Pokud jsou všechny servery ESXi spravované stejným serverem vCenter, zadejte název vCenter. V opačném případě přidejte hostitele ESXi.

    ![Zadat server VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Do pole **port SSL**zadejte port, který se používá ke komunikaci se serverem VMware. 443 je výchozí port, ale můžete jej změnit, pokud server VMware naslouchá na jiném portu.

5. V části **zadat pověření**vyberte přihlašovací údaje, které jste vytvořili dříve.

    ![Zadat přihlašovací údaje](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Kliknutím na **Přidat** přidejte server VMware do seznamu servery. Potom klikněte na tlačítko **Další**.

    ![Přidat server VMWare a přihlašovací údaje](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Na stránce **Souhrn** klikněte na **Přidat** a přidejte server VMware pro Azure Backup Server. K okamžitému přidání nového serveru se na serveru VMware nepotřebuje žádný agent.

    ![Přidání serveru VMware pro Azure Backup Server](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Ověřte nastavení na stránce **Dokončit** .

   ![Stránka dokončit](./media/backup-azure-backup-server-vmware/summary-screen.png)

Pokud máte více hostitelů ESXi, které nejsou spravovány serverem vCenter nebo máte více instancí vCenter Server, je nutné znovu spustit průvodce a přidat servery.

## <a name="configure-a-protection-group"></a>Konfigurace skupiny ochrany

Přidejte virtuální počítače VMware pro zálohování. Skupiny ochrany shromažďují více virtuálních počítačů a používají stejná nastavení pro uchovávání dat a zálohování u všech virtuálních počítačů ve skupině.

1. V konzole Azure Backup Server klikněte na **ochrana**> **Nový**.

    ![Otevřete Průvodce vytvořením nové skupiny ochrany.](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Na úvodní stránce průvodce **vytvořením nové skupiny ochrany** klikněte na **Další**.

    ![Dialogové okno Průvodce vytvořením nové skupiny ochrany](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Na stránce **Vybrat typ skupiny ochrany** vyberte **servery** a potom klikněte na **Další**. Zobrazí se stránka **Vybrat členy skupiny** .

1. V části **Vybrat členy skupiny**vyberte virtuální počítače (nebo složky VM), které chcete zálohovat. Potom klikněte na tlačítko **Další**.

    - Když vyberete složku nebo virtuální počítače nebo složky v této složce jsou také vybrány pro zálohování. Můžete zrušit kontrolu složek nebo virtuálních počítačů, které nechcete zálohovat.
1. Pokud je už virtuální počítač nebo složka zálohovaný, nemůžete ho vybrat. Tím je zajištěno, že pro virtuální počítač nejsou vytvořeny duplicitní body obnovení.

    ![Vybrat členy skupiny](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Na stránce **Vybrat způsob ochrany dat** zadejte název skupiny ochrany a nastavení ochrany. Pokud chcete zálohovat do Azure, nastavte krátkodobou ochranu na **disk** a povolte online ochranu. Potom klikněte na tlačítko **Další**.

    ![Vyberte způsob ochrany dat](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. V **nastavení zadat krátkodobé cíle**určete, jak dlouho chcete uchovávat data zálohovaná na disk.
   - V **oblasti uchovávání**zadejte počet dní, po které mají být udržovány body obnovení disku.
   - V poli **četnost synchronizací**určete, jak často se mají vymezit body obnovení disku.
       - Pokud nechcete nastavit interval zálohování, můžete zaškrtnout možnost **těsně před bodem obnovení** , aby záloha běžela těsně před každým naplánovaným bodem obnovení.
       - Krátkodobé zálohy jsou úplné zálohy a nejsou přírůstkově.
       - Klikněte na **Upravit** a změňte časy a data, kdy dojde k krátkodobému zálohování.

         ![Zadat krátkodobé cíle](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. V části **Kontrola přidělení disku**Zkontrolujte místo na disku, které je k dispozici pro zálohování virtuálních počítačů. pro virtuální počítače.

   - Doporučené přidělení disku vychází z rozsahu uchování, který jste zadali, typu úlohy a velikosti chráněných dat. Proveďte požadované změny a potom klikněte na tlačítko **Další**.
   - **Velikost dat:** Velikost dat ve skupině ochrany.
   - **Místo na disku:** Doporučená velikost místa na disku pro skupinu ochrany. Pokud chcete toto nastavení změnit, měli byste přidělit celkové místo, které je mírně větší než množství odhadu jednotlivých zdrojů dat.
   - Společně **najít data:** Pokud zapnete společné umístění, může být více zdrojů dat v ochraně namapováno na jednu repliku a svazek bodu obnovení. Společné umístění se nepodporuje pro všechny úlohy.
   - **Automaticky rozšiřovat:** Pokud zapnete toto nastavení, když data v chráněné skupině přesáhne počáteční přidělení, Azure Backup Server se pokusí zvětšit velikost disku o 25 procent.
   - **Podrobnosti fondu úložiště:** Zobrazuje stav fondu úložiště včetně celkové a zbývající velikosti disku.

    ![Zkontrolovat přidělení disku](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. Na stránce **Vybrat způsob vytvoření repliky** určete, jak se má provést prvotní zálohování, a pak klikněte na **Další**.
   - Výchozí hodnota je **automaticky přes síť** a **nyní**.
   - Pokud použijete výchozí, doporučujeme zadat dobu mimo špičku. Zvolte **později** a zadejte den a čas.
   - U velkých objemů dat nebo méně optimálních síťových stavů zvažte replikaci dat offline pomocí vyměnitelného média.

    ![Výběr metody vytvoření repliky](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. V **Možnosti kontroly konzistence**vyberte, jak a kdy se mají automatizovat kontroly konzistence. Potom klikněte na tlačítko **Další**.
      - Můžete spouštět kontroly konzistence, když se data repliky neshodují, nebo podle nastaveného plánu.
      - Pokud nechcete konfigurovat automatické kontroly konzistence, můžete spustit ruční kontrolu. Provedete to tak, že kliknete pravým tlačítkem na skupinu ochrany > **provést kontrolu konzistence**.

1. Na stránce **zadat data online ochrany** vyberte virtuální počítače nebo složky virtuálních počítačů, které chcete zálohovat. Můžete vybrat členy jednotlivě nebo kliknout na **Vybrat vše** pro výběr všech členů. Potom klikněte na tlačítko **Další**.

    ![Zadat data online ochrany](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Na stránce **zadat plán online zálohování** určete, jak často chcete zálohovat data z místního úložiště do Azure.

    - Body obnovení cloudu pro data budou vygenerovány podle plánu. Potom klikněte na tlačítko **Další**.
    - Po vygenerování bodu obnovení se přenese do trezoru Recovery Services v Azure.

    ![Zadat plán online zálohování](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Na stránce **zadat zásady uchovávání online** určete, jak dlouho chcete zachovat body obnovení vytvořené z denních, týdenních nebo měsíčních záloh do Azure. pak klikněte na **Další**.

    - Neexistují žádná časová omezení, jak dlouho můžete uchovávat data v Azure.
    - Jediným omezením je, že nemůžete mít více než 9999 bodů obnovení na chráněnou instanci. V tomto příkladu je chráněná instance serverem VMware.

    ![Zadat zásady uchovávání informací online](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. Na stránce **Souhrn** zkontrolujte nastavení a klikněte na **vytvořit skupinu**.

    ![Souhrn a nastavení člena skupiny ochrany](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6,7

Pokud chcete zálohovat vSphere 6,7, udělejte toto:

- Povolit TLS 1,2 na serveru DPM
  >[!Note]
  >VMWare 6,7 a vyšší povolily TLS jako komunikační protokol.

- Klíče registru nastavte takto:

       ```text

        Windows Registry Editor Version 5.00

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v2.0.50727]
       "SystemDefaultTlsVersions"=dword:00000001
       "SchUseStrongCrypto"=dword:00000001

       [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
       "SystemDefaultTlsVersions"=dword:00000001
       "SchUseStrongCrypto"=dword:00000001

       [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v2.0.50727]
       "SystemDefaultTlsVersions"=dword:00000001
       "SchUseStrongCrypto"=dword:00000001

       [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
       "SystemDefaultTlsVersions"=dword:00000001
       "SchUseStrongCrypto"=dword:00000001
       ```

## <a name="next-steps"></a>Další kroky

Pokud chcete řešit problémy při nastavování záloh, přečtěte si [příručku pro řešení potíží pro Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
