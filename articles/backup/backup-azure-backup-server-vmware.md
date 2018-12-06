---
title: Zálohování serverů VMware pomocí Azure Backup serveru
description: Použití Azure Backup serveru k zálohování serverů vCenter/ESXi VMware do Azure nebo z disku. Tento článek obsahuje krok = podrobné pokyny pro zálohování (nebo ochraně) úloh VMware.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/24/2017
ms.author: adigan
ms.openlocfilehash: e39e5d12610164ca4a1372830cf25ea203fd382c
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968830"
---
# <a name="back-up-a-vmware-server-to-azure"></a>Zálohování serveru VMware do Azure

Tento článek vysvětluje postup konfigurace serveru Azure Backup k ochraně úloh VMware server. Tento článek předpokládá, že už máte nainstalovaný Azure Backup serveru. Pokud nemáte Azure Backup serveru nainstalovaná, přečtěte si téma [Příprava zálohování úloh pomocí Azure Backup serveru](backup-azure-microsoft-azure-backup.md).

Azure Backup Server můžete zálohovat nebo chránit, VMware vCenter Server verze 6.5, 6.0 a 5.5.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Vytvořit zabezpečené připojení k serveru vCenter

Ve výchozím nastavení Azure Backup serveru komunikuje s každou vCenter serveru přes kanál protokolu HTTPS. Chcete-li na zabezpečené komunikace, doporučujeme, nainstalujte certifikát VMware certifikační autoritu (CA) na Azure Backup serveru. Pokud nevyžadují zabezpečené komunikace a chcete zakázat požadavek na protokol HTTPS, přečtěte si téma [zakázat zabezpečený komunikační protokol](backup-azure-backup-server-vmware.md#disable-secure-communication-protocol). Chcete-li vytvořit zabezpečené připojení mezi Azure Backup serveru a vCenter Server, importujte důvěryhodného certifikátu v Azure Backup serveru.

Obvykle použijete v prohlížeči na počítači Azure Backup serveru k připojení k serveru vCenter prostřednictvím webovém klientovi vSphere. Při prvním použití Azure Backup serveru prohlížeč pro připojení k vCenter serveru, připojení není zabezpečený. Následující obrázek ukazuje zabezpečená připojení.

![Příklad nezabezpečené připojení k serveru VMware](./media/backup-azure-backup-server-vmware/unsecure-url.png)

Pokud chcete opravit tento problém a vytvořit zabezpečené připojení, stáhnou certifikáty důvěryhodné kořenové certifikační Autority.

1. V prohlížeči na serveru Azure Backup zadejte adresu URL na webovém klientovi vSphere. Zobrazí se přihlašovací stránky webovém klientovi vSphere.

    ![Webovém klientovi vSphere](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

    V dolní části informace pro správce a vývojáře, vyhledejte **stahování důvěryhodné kořenové Certifikační autority** odkaz.

    ![Odkaz ke stažení certifikáty důvěryhodné kořenové certifikační Autority](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

  Pokud nevidíte přihlašovací stránku webovém klientovi vSphere, zkontrolujte nastavení proxy serveru v prohlížeči.

2. Klikněte na tlačítko **stahování důvěryhodné kořenové Certifikační autority**.

    VCenter Server stáhne soubor do místního počítače. Má název souboru **Stáhnout**. V závislosti na prohlížeči obdržíte zprávu s dotazem, jestli se má otevřít nebo uložit soubor.

    ![Stáhnout zprávu, když se stáhnou certifikáty](./media/backup-azure-backup-server-vmware/download-certs.png)

3. Uložte soubor do umístění v Azure Backup serveru. Při ukládání souboru přidáte příponu názvu souboru .zip.

    Soubor je soubor .zip, který obsahuje informace o certifikátech. S příponou .zip můžete použít nástroje pro extrakci.

4. Klikněte pravým tlačítkem na **download.zip**a pak vyberte **Extrahovat vše** pro extrakci obsahu.

    Extrahuje soubor ZIP svůj obsah rozbalí do složky s názvem **certifikáty**. Dva typy souborů se zobrazí ve složce certifikátů. Soubor kořenového certifikátu má příponu, která začíná číslem pořadí jako.0 a.1.

    Soubor seznamu CRL má příponu, která začíná s pořadím jako .r0 nebo .r1. Soubor seznamu CRL je přidružen k certifikátu.

    ![Stáhněte si soubor extrahovat místně ](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. V **certifikátů** složku, klikněte pravým tlačítkem na soubor kořenového certifikátu a pak klikněte na tlačítko **přejmenovat**.

    ![Přejmenovat kořenového certifikátu ](./media/backup-azure-backup-server-vmware/rename-cert.png)

    Změňte příponu kořenový certifikát na CRT. Pokud budete vyzváni, pokud jste si jisti, kterou chcete změnit rozšíření, klikněte na tlačítko **Ano** nebo **OK**. V opačném případě změníte jeho funkce. Ikona souboru se změní na ikonu, která představuje kořenový certifikát.

6. Klikněte pravým tlačítkem na kořenový certifikát a v místní nabídce vyberte **nainstalovat certifikát**.

    **Průvodce importem certifikátu** zobrazí se dialogové okno.

7. V **Průvodce importem certifikátu** dialogu **místního počítače** jako cíl pro certifikát a pak klikněte na tlačítko **Další** pokračujte.

    ![Možnosti cílového úložiště certifikátů ](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

    Pokud budete vyzváni, pokud chcete povolit změny v počítači, klikněte na tlačítko **Ano** nebo **OK**, všechny změny.

8. Na **certifikát Store** stránce **všechny certifikáty umístit v následujícím úložišti**a potom klikněte na tlačítko **Procházet** vybrat úložiště certifikátů.

    ![Certifikáty umístit v místě konkrétní úložiště](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

    **Store vyberte certifikát** zobrazí se dialogové okno.

    ![Hierarchie složky úložiště certifikátů](./media/backup-azure-backup-server-vmware/cert-store.png)

9. Vyberte **důvěryhodných kořenových certifikačních autorit** jako cílovou složku pro certifikáty a pak klikněte na tlačítko **OK**.

    ![Certifikát cílovou složku](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

    **Důvěryhodných kořenových certifikačních autorit** složky je potvrzeno, že úložiště certifikátů. Klikněte na **Další**.

    ![Složka úložiště certifikátů](./media/backup-azure-backup-server-vmware/certificate-import-wizard2.png)

10. Na **dokončení Průvodce importem certifikátu** stránce, zkontrolujte, že certifikát je do požadované složky a klikněte na **Dokončit**.

    ![Ověřte, zda je certifikát do správné složky](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

    Zobrazí se dialogové okno, import úspěšný certifikátu je potvrzeno.

11. Přihlásit se k systému vCenter Server a potvrďte, že připojení je zabezpečené.

  Pokud importem certifikátu se nepovedlo úspěšně dokončit a nedá se navázat zabezpečené připojení, najdete v dokumentaci VMware vSphere na [získání certifikátů serveru](http://pubs.vmware.com/vsphere-60/index.jsp#com.vmware.wssdk.dsg.doc/sdk_sg_server_certificate_Appendixes.6.4.html).

  Pokud máte zabezpečené hranice ve vaší organizaci a nechcete zapnout protokol HTTPS, použijte následující postup zakázání zabezpečenou komunikaci.

### <a name="disable-secure-communication-protocol"></a>Zakázat protokol zabezpečenou komunikaci

Pokud vaše organizace nevyžaduje protokol HTTPS, postupujte následovně Chcete-li zakázat HTTPS. Výchozí chování zakázat, vytvořte klíč registru, které ignoruje výchozí chování.

1. Zkopírujte a vložte následující text do souboru .txt.

  ```
  Windows Registry Editor Version 5.00
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
  "IgnoreCertificateValidation"=dword:00000001
  ```

2. Uložte soubor do počítače serveru Azure Backup. Název souboru použijte DisableSecureAuthentication.reg.

3. Poklikejte na soubor k aktivaci položky registru.


## <a name="create-a-role-and-user-account-on-the-vcenter-server"></a>Vytvoření role a uživatelský účet v systému vCenter Server

Role v systému vCenter Server, je předdefinovanou sadu oprávnění. Správce serveru vCenter vytvoří role. Pokud chcete přiřadit oprávnění, dvojice správce uživatelských účtů s rolí. K navázání potřebné uživatelské přihlašovací údaje k zálohování do počítače se serverem vCenter, vytvořte roli s konkrétní oprávnění a přidružte účet uživatele s rolí.

Azure Backup Server používá uživatelské jméno a heslo k ověření pomocí systému vCenter Server. Azure Backup Server používá tyto přihlašovací údaje jako ověřování pro všechny operace zálohování.

Chcete-li přidat role serveru vCenter a jeho oprávnění pro správce zálohování:

1. Přihlaste se k serveru vCenter a pak v systému vCenter Server **Navigátor** panelu, klikněte na tlačítko **správu**.

    ![Možnost správy v panelu Navigátor Server vCenter](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. V **správu** vyberte **role**a pak v **role** panelu klikněte na ikonu Přidat role (+ symbol).

    ![Přidat roli](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

    **Vytvořit roli** zobrazí se dialogové okno.

    ![Vytvoření role](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. V **vytvořit roli** v dialogu **název Role** zadejte *BackupAdminRole*. Název role může být cokoli, co chcete, ale měla by být rozpoznatelných pro účely této role.

4. Vyberte oprávnění pro příslušnou verzi vCenter a pak klikněte na tlačítko **OK**. V následující tabulce jsou uvedeny požadovaná oprávnění pro vCenter 6.0 a vCenter 5.5.

  Když vyberete oprávnění, klikněte na ikonu vedle nadřazený popisek nadřazeného rozbalit a zobrazit podřízené oprávnění. Vybrat oprávnění jako virtuální počítač, budete muset přejít do hierarchie nadřazený-podřízený několik úrovní. Nemusíte vybrat všechny podřízené oprávnění v rámci nadřazené oprávnění.

  ![Oprávnění hierarchie nadřazený-podřízený](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  Po kliknutí na **OK**, nová role se zobrazí v seznamu na panelu role.

|Oprávnění pro vCenter verze 6.0 a 6.5| Oprávnění pro vCenter 5.5|
|----------------------------------|---------------------------|
|Datastore.AllocateSpace   | Datastore.AllocateSpace|
|Global.ManageCustomFields | Global.ManageCustomerFields|
|Global.SetCustomFields    |   |
|Host.Local.CreateVM       | Network.Assign |
|Network.Assign            |  |
|Resource.AssignVMToPool   |  |
|VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   |
|VirtualMachine.Config.AdvanceConfig| VirtualMachine.Config.AdvancedConfig|
|VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking |
|VirtualMachine.Config.HostUSBDevice||
|VirtualMachine.Config.QueryUnownedFiles|    |
|VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement |
|VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff |
|VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create |
|VirtualMachine.Provisioning.DiskRandomAccess| |
|VirtualMachine.Provisioning.DiskRandomRead|VirtualMachine.Provisioning.DiskRandomRead |
|VirtualMachine.State.CreateSnapshot| VirtualMachine.State.CreateSnapshot|
|VirtualMachine.State.RemoveSnapshot|VirtualMachine.State.RemoveSnapshot |
</br>



## <a name="create-a-vcenter-server-user-account-and-permissions"></a>Vytvořit uživatelský účet vCenter Server a oprávnění

Po nastavení role s oprávněními, vytvořte účet uživatele. Uživatelský účet má název a heslo, které poskytuje přihlašovací údaje, které se používají pro ověřování.

1. Chcete-li vytvořit uživatelský účet v systému vCenter Server **Navigátor** panelu, klikněte na tlačítko **uživatelů a skupin**.

    ![Možnost uživatelé a skupiny](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    **VCenter uživatelů a skupin** otevře se panel.

    ![vCenter uživatele a skupiny panelů](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. V **vCenter uživatelů a skupin** panelu, vyberte **uživatelé** kartu a potom klikněte na položku Přidat uživatele (+ symbol).

    **Nového uživatele** zobrazí se dialogové okno.

3. V **nového uživatele** dialogové okno Přidat informace o uživateli a klepněte na tlačítko **OK**. Uživatelské jméno v tomto postupu je BackupAdmin.

    ![Dialogové okno Nový uživatel](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

    Nový uživatelský účet zobrazí v seznamu.

4. Přidružení uživatelského účtu s rolí v **Navigátor** panelu, klikněte na tlačítko **globální oprávnění**. V **globální oprávnění** panelu, vyberte **spravovat** kartu a potom klikněte na položku Přidat (+ symbol).

    ![Globální oprávnění panel](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

    **Globální oprávnění Root - přidat oprávnění** zobrazí se dialogové okno.

5. V **globální oprávnění Root - přidat oprávnění** dialogové okno, klikněte na tlačítko **přidat** vybrat uživatele nebo skupinu.

    ![Zvolte uživatele nebo skupinu](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

    **Vyberte uživatele nebo skupiny** zobrazí se dialogové okno.

6. V **vyberte uživatele nebo skupiny** dialogového okna zvolte **BackupAdmin** a potom klikněte na tlačítko **přidat**.

    V **uživatelé**, *doména\uživatelské jméno* formátu se používá pro uživatelský účet. Pokud chcete použít jinou doménu, vyberte jej ze **domény** seznamu.

    ![Přidání BackupAdmin uživatele](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

    Klikněte na tlačítko **OK** přidat vybraným uživatelům umožní **přidat oprávnění** dialogové okno.

7. Teď, když jste identifikovali uživatele, přiřadíte uživatele k roli. V **přiřazené Role**, v rozevíracím seznamu vyberte **BackupAdminRole**a potom klikněte na tlačítko **OK**.

    ![Přiřadit uživatele k roli](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  Na **spravovat** kartu **globální oprávnění** panelu, nový uživatelský účet a přidružené role se zobrazí v seznamu.


## <a name="establish-vcenter-server-credentials-on-azure-backup-server"></a>Vytvořit přihlašovací údaje k vCenter serveru na Azure Backup serveru

Předtím, než přidáte VMware server do Azure Backup serveru, nainstalujte [aktualizace 1 pro Azure Backup serveru](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server).

1. Otevřete Azure Backup Server, dvojitým kliknutím na ikony na ploše Azure Backup serveru.

    ![Ikona Azure Backup serveru](./media/backup-azure-backup-server-vmware/mabs-icon.png)

    Pokud nemůžete najít ikony na ploše, otevřete seznam nainstalovaných aplikací Azure Backup serveru. Název aplikace Azure Backup serveru se nazývá Microsoft Azure Backup.

2. V konzole Azure Backup serveru klikněte na **správu**, klikněte na tlačítko **provozní servery**a potom na pásu karet nástroje klikněte na **spravovat VMware**.

    ![Konzole Azure Backup serveru](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

    **Spravovat přihlašovací údaje** zobrazí se dialogové okno.

    ![Dialogové okno Azure Backup Server spravovat přihlašovací údaje](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. V **spravovat přihlašovací údaje** dialogové okno, klikněte na tlačítko **přidat** otevřít **přidat přihlašovací údaje** dialogové okno.

4. V **přidat přihlašovací údaje** dialogovém okně zadejte název a popis nové přihlašovací údaje. Zadejte uživatelské jméno a heslo. Název, *přihlašovacích údajů Contoso Vcenter* slouží k identifikaci přihlašovací údaje, které v dalším postupu. Použijte stejné uživatelské jméno a heslo, které se používá pro vCenter Server. Pokud vCenter Server a Azure Backup serveru nejsou ve stejné doméně, v **uživatelské jméno**, zadejte doménu.

    ![Dialogové okno Azure Backup Server přidat přihlašovací údaje](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

    Klikněte na tlačítko **přidat** přidat nový přihlašovací údaj do Azure Backup serveru. Nový přihlašovací údaj se zobrazí v seznamu **spravovat přihlašovací údaje** dialogové okno.

    ![Dialogové okno Azure Backup Server spravovat přihlašovací údaje](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. Zavřete **spravovat přihlašovací údaje** dialogové okno, klikněte na tlačítko **X** v pravém horním rohu.


## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Přidání serveru vCenter do Azure Backup serveru

Průvodce přidáním provozního serveru se používá k přidání systému vCenter Server do Azure Backup serveru.

Chcete-li spustit Průvodce přidáním provozního serveru, proveďte následující postup:

1. V konzole Azure Backup serveru klikněte na **správu**, klikněte na tlačítko **provozní servery**a potom klikněte na tlačítko **přidat**.

    ![Průvodce přidáním otevřít provozního serveru](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

    **Průvodce přidáním provozního serveru** zobrazí se dialogové okno.

    ![Průvodce přidáním provozního serveru](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. Na **typ provozního serveru vyberte** stránce **servery VMware**a potom klikněte na tlačítko **Další**.

3. V **název nebo IP adresa serveru**, zadejte plně kvalifikovaný název domény (FQDN) nebo IP adresu serveru VMware (hostitelského serveru ESXi). Pokud všechny servery ESXi spravuje stejný počítač vCenter, můžete použít název vCenter.

    ![Zadejte plně kvalifikovaný název domény nebo IP adresa serveru VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. V **SSL Port**, zadejte port, který se používá ke komunikaci se serverem VMware. Používejte port 443, který je výchozím portem, pokud si nejste jisti, že jiný port je povinný.

5. V **zadejte přihlašovací údaje**, vyberte přihlašovací údaj, který jste vytvořili dříve.

    ![Zadejte přihlašovací údaje](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Klikněte na tlačítko **přidat** přidání serveru VMware do seznamu **přidat servery VMware**a potom klikněte na tlačítko **Další** přesunout na další stránku průvodce.

    ![Přidání serveru VMWare a přihlašovacích údajů](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. V **Souhrn** klikněte na **přidat** přidat zadaný server VMware do Azure Backup serveru.

    ![Přidání serveru VMware do Azure Backup serveru](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  Zálohování serveru VMware je bez agenta zálohování a se okamžitě přidá nový server. **Dokončit** stránky se zobrazí výsledky.

  ![Stránka dokončení](./media/backup-azure-backup-server-vmware/summary-screen.png)

  Chcete-li přidat více instancí systému vCenter Server do Azure Backup serveru, opakujte předchozí kroky v této části.

Po přidání systému vCenter Server do Azure Backup Server, dalším krokem je vytvoření skupiny ochrany. Určuje různé podrobnosti pro krátkodobé nebo dlouhodobé uložení uchování skupiny ochrany a kde definování a použití zásady zálohování. Zásada zálohování je plán, kdy dojde k zálohování a co se zálohuje.


## <a name="configure-a-protection-group"></a>Konfigurace skupiny ochrany

Pokud jste ještě nepoužívali, System Center Data Protection Manager nebo serveru Azure Backup, přečtěte si téma [plánování zálohování na disk](https://technet.microsoft.com/library/hh758026.aspx) připravovat své prostředí hardwaru. Až zkontrolujete, zda máte správné úložiště, přidejte virtuální počítače VMware pomocí Průvodce vytvořením nové skupiny ochrany.

1. V konzole Azure Backup serveru klikněte na **ochrany**a na pásu karet nástroje klikněte na tlačítko **nový** otevřete Průvodce vytvořením nové skupiny ochrany.

    ![Otevřete Průvodce vytvořením nové skupiny ochrany](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

    **Vytvořením nové skupiny ochrany** se zobrazí dialogové okno Průvodce podáním.

    ![Dialogové okno Průvodce vytvořením nové skupiny ochrany](./media/backup-azure-backup-server-vmware/protection-wizard.png)

    Klikněte na tlačítko **Další** k přechodu na **vybrat typ skupiny ochrany** stránky.

2. Na **typ skupiny ochrany vyberte** stránce **servery** a potom klikněte na tlačítko **Další**. **Vybrat členy skupiny** se zobrazí stránka.

3. Na **vybrat členy skupiny** se zobrazí stránka, Dostupní členové a vybrané členy. Vyberte členy, které chcete chránit a potom klikněte na tlačítko **Další**.

    ![Vybrat členy skupiny](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

    Při výběru člena, pokud vyberete složku, která obsahuje další složky nebo virtuální počítače, budou vybrány také tyto složky a virtuální počítače. Zahrnutí složky a virtuální počítače v nadřazené složce se nazývá ochrany na úrovni složek. Pokud chcete odebrat složku nebo virtuálního počítače, zrušte zaškrtnutí políčka.

    Pokud virtuální počítač nebo složku obsahující virtuální počítač, se už chránit v Azure, nelze znovu vyberte tento virtuální počítač. To znamená, že po aktivaci ochrany virtuálního počítače do Azure, nemůže být chráněn akci, která zabraňuje pro jeden virtuální počítač vytváří body obnovení duplicitní. Pokud chcete zobrazit, která instance Azure Backup serveru už chrání členem, přejděte na člen, který chcete zobrazit název serveru pro ochranu.

4. Na **vybrat způsob ochrany dat** stránky, zadejte název pro skupinu ochrany. Jsou vybrané krátkodobou ochranu (na disk) a online ochranu. Pokud chcete používat online ochrany (do Azure), je nutné použít krátkodobou ochranu na disk. Klikněte na tlačítko **Další** přejděte na rozsah krátkodobé ochrany.

    ![Vyberte způsob ochrany dat](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. Na **zadat krátkodobé cíle** stránky, pro **rozsah uchování**, zadejte počet dní, které chcete zachovat body obnovení, které jsou *uložený na disk*. Pokud chcete změnit čas a dny po pořízení bodů obnovení, klikněte na tlačítko **změnit**. Krátkodobé body obnovení jsou úplné zálohy. Nejsou přírůstkové zálohy. Pokud jste spokojeni s krátkodobé cíle, klikněte na tlačítko **Další**.

    ![Zadat krátkodobé cíle](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. Na **zkontrolovat přidělení disku** stránce zkontrolujte a v případě potřeby upravte místo na disku pro virtuální počítače. Doporučená přidělení disku jsou založené na rozsahu uchování, která je zadána v **zadat krátkodobé cíle** stránce, typu úlohy a velikosti chráněných dat (určenou v kroku 3).  

  - **Velikost dat:** velikost dat ve skupině ochrany.
  - **Volné místo na disku:** doporučenou velikost místa na disku pro skupinu ochrany. Pokud chcete toto nastavení změnit, měli byste přidělit celkové místo, které je o něco větší než hodnota, která odhadnout, že každý zdroj dat roste.
  - **Společné umístění dat:** Pokud povolíte společné umístění, svazek bodu více zdrojů dat v oblasti ochrany lze mapovat na jednu repliku a obnovení. Společné umístění se nepodporuje pro všechny úlohy.
  - **Automaticky zvětšit:** Pokud zapnete toto nastavení, pokud data ve skupině ochrany přesáhnou předběžné přidělení počáteční, System Center Data Protection Manager se pokusí zvětšit velikost disku o 25 procent.
  - **Podrobnosti fondu úložiště:** zobrazuje stav fondu úložiště včetně celkové a zbývající velikosti disku.

    ![Zkontrolovat přidělení disku](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

    Pokud jste spokojeni s přidělení místa, klikněte na tlačítko **Další**.

7. Na **vyberte způsob vytvoření repliky** stránky, zadejte, jak chcete generovat počáteční kopii nebo repliky chráněných dat v Azure Backup serveru.

    Výchozí hodnota je **automaticky přes síť** a **nyní**. Pokud používáte výchozí nastavení, doporučujeme, že zadáte čas mimo špičku. Zvolte **později** a zadejte datum a čas.

    Pro velké objemy dat nebo méně než optimální síťové podmínky zvažte replikaci dat offline pomocí vyměnitelného média.

    Po provedení volby, klikněte na tlačítko **Další**.

    ![Vyberte způsob vytvoření repliky](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. Na **možnosti kontroly konzistence** stránky, vyberte, jak a kdy chcete automatizovat kontroly konzistence. Kontroly konzistence můžete spustit, když se stane nekonzistentní data repliky, nebo podle nastaveného plánu.

    Pokud nechcete konfigurovat Automatická kontrola konzistence, můžete spustit ruční kontrolu. V oblasti ochrany Azure Backup serveru konzoly klikněte pravým tlačítkem na skupinu ochrany a pak vyberte **provést kontrolu konzistence**.

    Klikněte na tlačítko **Další** přesunout na další stránku.

9. Na **zadat Data Online ochrany** vyberte jeden nebo více zdrojů dat, které chcete chránit. Vyberte členy jednotlivě, nebo klikněte na **Vybrat vše** zvolit všechny členy. Po výběru členů, klikněte na tlačítko **Další**.

    ![Zadat data online ochrany](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. Na **zadejte plán Online zálohování** stránky, zadejte plán pro generování body obnovení ze zálohy disku. Po vygenerování bodu obnovení se přenesou do trezoru služby Recovery Services v Azure. Pokud jste spokojeni s plán online zálohování, klikněte na tlačítko **Další**.

    ![Zadejte plán online zálohování.](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. Na **zadat zásady Online uchovávání** stránce, označuje, jak dlouho chcete uchovat data záloh v Azure. Po definování zásady, klikněte na tlačítko **Další**.

    ![Zadejte zásady online uchovávání dat.](./media/backup-azure-backup-server-vmware/retention-policy.png)

    Neexistuje žádný časový limit pro dobu můžete ponechat data v Azure. Při ukládání data bodu obnovení v Azure jediným limitem je, že nemůže mít více než 9999 bodů obnovení na chráněnou instanci. V tomto příkladu je chráněná instance serveru VMware.

12. Na **Souhrn** stránky, zkontrolujte podrobnosti pro členy skupiny ochrany a nastavení a potom klikněte na tlačítko **vytvořit skupinu**.

    ![Souhrn nastavení a člena skupiny ochrany](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>Další postup
Pokud používáte Azure Backup serveru k ochraně úloh VMware, vás může zajímat pomocí Azure Backup serveru k ochraně [Microsoft Exchange server](./backup-azure-exchange-mabs.md), [Microsoft SharePoint farmu](./backup-azure-backup-sharepoint-mabs.md), nebo [Databáze systému SQL Server](./backup-azure-sql-mabs.md).

Informace o problémy s registrací agenta, najdete v konfiguraci skupiny ochrany nebo zálohování úloh, [Poradce při potížích s Azure Backup serveru](./backup-azure-mabs-troubleshoot.md).
