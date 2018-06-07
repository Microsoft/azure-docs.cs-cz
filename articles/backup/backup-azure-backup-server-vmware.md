---
title: Zálohování serverů VMware s serveru Azure Backup
description: Používejte Azure Backup Server pro zálohování servery VMware vCenter/ESXi do Azure nebo disk. Tento článek obsahuje krok za krokem instrukce pro zálohování (nebo ochrany) = úlohy VMware.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/24/2017
ms.author: adigan
ms.openlocfilehash: 9cf3c9d5df11e19045cd47a41d7ab9ac93bdf700
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605419"
---
# <a name="back-up-a-vmware-server-to-azure"></a>Zálohovat VMware server do Azure

Tento článek vysvětluje postup konfigurace serveru Azure Backup k ochraně úlohy serveru VMware. Tento článek předpokládá, že je již nainstalován Server Azure Backup. Pokud nemáte nainstalované serveru Azure Backup, přečtěte si téma [Příprava zálohování úloh pomocí serveru Azure Backup](backup-azure-microsoft-azure-backup.md).

Azure Backup Server můžete zálohovat nebo pomoc při ochraně VMware vCenter Server verze 6.5, 6.0 a 5.5.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Vytvoření bezpečného připojení k systému vCenter Server

Ve výchozím serveru Azure Backup komunikuje se každý Server vCenter přes kanál protokolu HTTPS. Chcete-li zapnout zabezpečenou komunikaci, doporučujeme nainstalovat certifikát VMware certifikační autoritou (CA) na serveru Azure Backup. Pokud není zapotřebí zabezpečenou komunikaci a přejete zakažte požadavek na protokol HTTPS, najdete v části [zakázat zabezpečený komunikační protokol](backup-azure-backup-server-vmware.md#disable-secure-communication-protocol). K vytvoření bezpečného připojení mezi serverem pro zálohování Azure a vCenter Server, importujte důvěryhodný certifikát na serveru Azure Backup.

Obvykle použijete v prohlížeči na počítači serveru Azure Backup pro připojení k systému vCenter Server prostřednictvím vSphere webového klienta. Při prvním pomocí serveru Azure Backup prohlížeče pro připojení k systému vCenter Server, připojení není zabezpečení. Následující obrázek znázorňuje nezabezpečené připojení.

![Příklad nezabezpečené připojení k serveru VMware](./media/backup-azure-backup-server-vmware/unsecure-url.png)

Pokud chcete tento problém vyřešit a vytvoří zabezpečené připojení, stáhněte certifikáty důvěryhodné kořenové certifikační Autority.

1. V prohlížeči na serveru Azure Backup zadejte adresu URL vSphere webového klienta. Zobrazí se stránka vSphere webovému klientovi přihlášení.

    ![vSphere webového klienta](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

    V dolní části informace o Správci a vývojáři, vyhledejte **stažení důvěryhodné kořenové Certifikační autority** odkaz.

    ![Odkaz ke stažení certifikáty důvěryhodné kořenové certifikační Autority](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

  Pokud nevidíte přihlašovací stránky vSphere webovému klientovi, zkontrolujte nastavení proxy serveru v prohlížeči.

2. Klikněte na tlačítko **stažení důvěryhodné kořenové Certifikační autority**.

    VCenter Server stáhne soubor do místního počítače. Název souboru je s názvem **Stáhnout**. V závislosti na prohlížeči obdržíte zprávu s dotazem, zda chcete otevřít nebo uložit soubor.

    ![stáhnout zprávy, když se stáhnou certifikáty](./media/backup-azure-backup-server-vmware/download-certs.png)

3. Uložte soubor do umístění na serveru Azure Backup. Při ukládání souboru přidáte příponu názvu souboru .zip.

    Soubor je soubor ZIP, který obsahuje informace o certifikátech. S příponou .zip můžete použít nástroje extrakce.

4. Klikněte pravým tlačítkem na **download.zip**a potom vyberte **Extrahovat vše** extrahujte obsah.

    Soubor .zip extrahuje její obsah do složky s názvem **certifikátů**. Dva typy souborů se zobrazí ve složce pomocí certifikátů. Soubor kořenového certifikátu má příponu, která začíná číslem pořadí jako.0 a.1.
    
    Soubor seznamu CRL má příponu, která začíná pořadí jako .r0 nebo .r1. Soubor seznamu CRL je přidružen k certifikátu.

    ![Stáhněte si soubor extrahovat místně ](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. V **certifikátů** složku, klikněte pravým tlačítkem na soubor kořenového certifikátu a pak klikněte na **přejmenovat**.

    ![Přejmenujte kořenový certifikát ](./media/backup-azure-backup-server-vmware/rename-cert.png)

    Změňte rozšíření kořenový certifikát na .crt. Po zobrazení dotazu, pokud jste si jisti, kterou chcete změnit rozšíření, klikněte na tlačítko **Ano** nebo **OK**. Jinak můžete změnit funkce určený v souboru. Ikona souboru se změní na ikonu, která reprezentuje kořenový certifikát.

6. Klikněte pravým tlačítkem na kořenový certifikát a v místní nabídce vyberte **nainstalovat certifikát**.

    **Průvodce importem certifikátu** zobrazí se dialogové okno.

7. V **Průvodce importem certifikátu** dialogové okno, vyberte **místního počítače** jako cíl pro certifikát a pak klikněte na tlačítko **Další** pokračujte.

    ![Možnosti cílového úložiště certifikátů ](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

    Zobrazení dotazu, pokud chcete povolit změny v počítači, klikněte na tlačítko **Ano** nebo **OK**, všechny změny.

8. Na **úložiště certifikátů** vyberte **všechny certifikáty umístit v následujícím úložišti**a potom klikněte na **Procházet** vybrat úložiště certifikátů.

    ![Certifikáty umístit v pozici konkrétní úložiště](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

    **Vybrat úložiště certifikátů** zobrazí se dialogové okno.

    ![Hierarchie složky úložiště certifikátů](./media/backup-azure-backup-server-vmware/cert-store.png)

9. Vyberte **důvěryhodné kořenové certifikační autority** jako cílová složka pro certifikáty a pak klikněte na tlačítko **OK**.

    ![Certifikát cílovou složku](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

    **Důvěryhodné kořenové certifikační autority** složky je potvrzeno, že úložiště certifikátů. Klikněte na **Další**.

    ![Složka úložiště certifikátů](./media/backup-azure-backup-server-vmware/certificate-import-wizard2.png)

10. Na **dokončení Průvodce importem certifikátu** stránka, zkontrolujte, zda je certifikát na požadovanou složku a pak klikněte na tlačítko **Dokončit**.

    ![Ověřte, zda je certifikát ve správné složce](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

    Zobrazí se dialogové okno, import úspěšný certifikátu je potvrzen.

11. Přihlásit se k serveru vCenter a potvrďte, že je zabezpečené připojení.

  Pokud neproběhne import certifikátu úspěšně, a nemůže navázat zabezpečené připojení, v dokumentaci k VMware vSphere na [získání certifikátů serveru](http://pubs.vmware.com/vsphere-60/index.jsp#com.vmware.wssdk.dsg.doc/sdk_sg_server_certificate_Appendixes.6.4.html).

  Pokud máte zabezpečené hranice v rámci vaší organizace a nechcete zapnout protokol HTTPS, použijte následující postup zakázání zabezpečené komunikace.

### <a name="disable-secure-communication-protocol"></a>Zakažte zabezpečené komunikační protokol

Pokud vaše organizace nevyžaduje protokol HTTPS, použijte následující postup zakázání protokolu HTTPS. Výchozí chování zakázat, vytvořte klíč registru, který ignoruje výchozí chování.

1. Zkopírujte a vložte následující text do souboru TXT.

  ```
  Windows Registry Editor Version 5.00
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
  "IgnoreCertificateValidation"=dword:00000001
  ```

2. Uložte soubor do počítače serveru Azure Backup. Název souboru použijte DisableSecureAuthentication.reg.

3. Poklikejte na soubor aktivovat položku registru.


## <a name="create-a-role-and-user-account-on-the-vcenter-server"></a>Vytvoření účtu role a uživatele v systému vCenter Server

Role v systému vCenter Server, je předdefinovanou sadu oprávnění. Správce serveru vCenter vytvoří role. Přiřazení oprávnění správce páry uživatelské účty s rolí. Zavést potřebné uživatelské přihlašovací údaje k zálohování do počítače serveru vCenter, vytvořit roli s konkrétní oprávnění a pak přidružit uživatelský účet k roli.

Azure Backup Server používá uživatelské jméno a heslo k ověření pomocí systému vCenter Server. Azure Backup Server používá tyto přihlašovací údaje jako ověřování pro všechny operace zálohování.

Přidání role serveru vCenter a jeho oprávnění pro správce a zálohování:

1. Přihlaste se k systému vCenter Server a pak v systému vCenter Server **Navigátor** panelu, klikněte na tlačítko **správy**.

    ![Možnost správy panelu Navigátor serveru vCenter](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. V **správy** vyberte **role**a potom v **role** panelu klikněte na ikonu Přidat role (+ symbol).

    ![Přidat roli](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

    **Vytvořit roli** zobrazí se dialogové okno.

    ![Vytvoření role](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. V **vytvořit roli** dialogu **název Role** zadejte *BackupAdminRole*. Název role může být vám líbí, ale měla by být rozpoznatelném za účelem role.

4. Vyberte oprávnění pro příslušnou verzi vCenter a pak klikněte na tlačítko **OK**. V následující tabulce jsou uvedeny požadovaná oprávnění pro vCenter 6.0 a vCenter 5.5.

  Když vyberete oprávnění, klikněte na ikonu vedle nadřazený štítek rozbalit nadřazené a podřízené oprávnění zobrazit. Pokud chcete vybrat oprávnění virtuální počítač, budete muset přejít několik úrovní do hierarchie nadřazený-podřízený. Nemusíte vyberte všechny podřízené oprávnění v rámci nadřazené oprávnění.

  ![Oprávnění hierarchie nadřazený-podřízený](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  Po kliknutí na tlačítko **OK**, nová role se zobrazí v seznamu na panelu role.

|Oprávnění pro vCenter 6.0| Oprávnění pro vCenter 5,5|
|--------------------------|---------------------------|
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



## <a name="create-a-vcenter-server-user-account-and-permissions"></a>Vytvořte uživatelský účet systému vCenter Server a oprávnění

Po vytvoření role s oprávněními, vytvořte účet uživatele. Uživatelský účet má jméno a heslo, které poskytuje pověření, které se používají pro ověřování.

1. Chcete-li vytvořit účet uživatele v systému vCenter Server **Navigátor** panelu, klikněte na tlačítko **uživatelů a skupin**.

    ![Možnost uživatelé a skupiny](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    **VCenter uživatelů a skupin** se zobrazí panel.

    ![panel vCenter uživatelů a skupin](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. V **vCenter uživatelů a skupin** panel, vyberte **uživatelé** a pak klikněte na ikonu Přidat uživatele (+ symbol).

    **Nového uživatele** zobrazí se dialogové okno.

3. V **nového uživatele** dialogové okno pole, přidejte informace o uživateli a potom klikněte na **OK**. V tomto postupu je uživatelské jméno BackupAdmin.

    ![Dialogové okno Nový uživatel](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

    V seznamu se zobrazí nový uživatelský účet.

4. Chcete-li přidružit uživatelský účet s rolí, v **Navigátor** panelu, klikněte na tlačítko **globální oprávnění**. V **globální oprávnění** panel, vyberte **spravovat** a pak klikněte na ikonu Přidat (+ symbol).

    ![Globální oprávnění panely](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

    **Globální oprávnění Root - přidat oprávnění** zobrazí se dialogové okno.

5. V **globální oprávnění Root - přidat oprávnění** dialogové okno, klikněte na tlačítko **přidat** vybrat uživatele nebo skupinu.

    ![Vyberte uživatele nebo skupinu](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

    **Vyberte uživatele nebo skupiny** zobrazí se dialogové okno.

6. V **vyberte uživatele nebo skupiny** dialogovém okně vyberte **BackupAdmin** a pak klikněte na **přidat**.

    V **uživatelé**, *doména\uživatelské_jméno* formát se používá pro uživatelský účet. Pokud chcete použít jinou doménu, vyberte ho **domény** seznamu.

    ![Přidat uživatele BackupAdmin](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

    Klikněte na tlačítko **OK** přidání vybraných uživatelů k **přidat oprávnění** dialogové okno.

7. Teď, když jste zformulovali uživatele, přiřadíte uživatele k roli. V **přiřadit Role**, v rozevíracím seznamu vyberte **BackupAdminRole**a potom klikněte na **OK**.

    ![Přiřadit uživatele k roli](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  Na **spravovat** ve **globální oprávnění** panelů, nový uživatelský účet a přidružené role, které jsou uvedeny v tomto seznamu.


## <a name="establish-vcenter-server-credentials-on-azure-backup-server"></a>Vytvořit přihlašovací údaje serveru vCenter na serveru Azure Backup

Než přidáte VMware server na serveru Azure Backup, nainstalovat [aktualizace 1 pro Azure Backup Server](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server).

1. Otevřete Azure Backup Server, dvakrát klikněte na ikonu na ploše serveru Azure Backup.

    ![Zálohování serveru Azure – ikona](./media/backup-azure-backup-server-vmware/mabs-icon.png)

    Pokud nemůžete najít ikony na ploše, otevřete Azure Backup Server ze seznamu nainstalovaných aplikací. Název aplikace Azure Backup Server se nazývá Microsoft Azure Backup.

2. V konzole serveru Azure Backup, klikněte na **správy**, klikněte na tlačítko **produkční servery**a pak na pásu karet nástroje klikněte na **spravovat VMware**.

    ![Azure Backup Server konzoly](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

    **Spravovat pověření** zobrazí se dialogové okno.

    ![Dialogové okno Azure Backup Server spravovat pověření](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. V **spravovat pověření** dialogové okno, klikněte na tlačítko **přidat** otevřete **přidat přihlašovací údaje** dialogové okno.

4. V **přidat přihlašovací údaje** dialogovém okně zadejte název a popis nové přihlašovací údaje. Zadejte uživatelské jméno a heslo. Název, *přihlašovacích údajů Contoso Vcenter* slouží k identifikaci přihlašovací údaje v dalším postupu. Použijte stejné uživatelské jméno a heslo, které se používá pro vCenter Server. Pokud vCenter Server a Azure Backup Server nejsou ve stejné doméně, v **uživatelské jméno**, zadejte doménu.

    ![Dialogové okno Azure Backup Server přidat přihlašovací údaje](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

    Klikněte na tlačítko **přidat** pro přidání nových přihlašovacích údajů do serveru Azure Backup. Nových přihlašovacích údajů se zobrazí v seznamu **spravovat pověření** dialogové okno.
    
    ![Dialogové okno Azure Backup Server spravovat pověření](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. Zavřete **spravovat pověření** dialogové okno, klikněte **X** v pravém horním rohu.


## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Přidání systému vCenter Server do serveru Azure Backup

Průvodce přidání provozního serveru se používá k přidání systému vCenter Server na serveru Azure Backup.

Chcete-li spustit Průvodce přidání provozním serveru, proveďte následující postup:

1. V konzole serveru Azure Backup, klikněte na **správy**, klikněte na tlačítko **produkční servery**a potom klikněte na **přidat**.

    ![Průvodce přidání serveru otevřete výroby](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

    **Průvodce přidání serveru produkční** zobrazí se dialogové okno.

    ![Průvodce přidání provozním serveru](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. Na **provozním serveru vyberte typ** vyberte **servery VMware**a potom klikněte na **Další**.

3. V **název nebo IP adresa serveru**, zadejte plně kvalifikovaný název domény (FQDN) nebo IP adresa serveru VMware. Pokud stejný vCenter spravuje všechny servery ESXi, můžete název vCenter.

    ![Zadejte plně kvalifikovaný název domény nebo IP adresa serveru VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. V **SSL Port**, zadejte port, který se používá ke komunikaci se serverem VMware. Použijte port 443, který je výchozím portem, pokud si nejste jisti, že jiný port je povinný.

5. V **zadejte přihlašovací údaje**, vyberte pověření, které jste vytvořili dříve.

    ![Zadejte přihlašovací údaje](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Klikněte na tlačítko **přidat** přidat VMware server do seznamu **přidat servery VMware**a potom klikněte na **Další** pro přesun na další stránku průvodce.

    ![Přidání serveru VMWare a přihlašovacích údajů](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. V **Souhrn** klikněte na tlačítko **přidat** přidat zadaný server VMware do serveru Azure Backup.

    ![Přidat VMware server na serveru Azure Backup](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  Zálohování serveru VMware je bez agenta zálohování a okamžitě přidá nový server. **Dokončit** stránce se zobrazují výsledky.

  ![Stránku](./media/backup-azure-backup-server-vmware/summary-screen.png)

  Chcete-li více instancí systému vCenter Server přidat do serveru Azure Backup, opakujte předchozí kroky v této části.

Po přidání systému vCenter Server do serveru Azure Backup, dalším krokem je vytvoření skupiny ochrany. Skupiny ochrany určuje různé podrobnosti krátký nebo dlouhodobé uchovávání a je kde definovat a aplikovat zásady zálohování. Zásady zálohování je plán, když dojde k zálohování, a co se zálohuje.


## <a name="configure-a-protection-group"></a>Konfiguraci skupiny ochrany

Pokud nepoužijete System Center Data Protection Manager nebo serveru Azure Backup před, přečtěte si téma [plánování zálohování na disk](https://technet.microsoft.com/library/hh758026.aspx) při přípravě svého prostředí hardwaru. Po můžete zkontrolovat, zda máte správné úložiště, použijte Průvodce vytvořením nové skupiny ochrany přidat virtuální počítače VMware.

1. V konzole serveru Azure Backup, klikněte na **ochrany**a na pásu karet nástroje klikněte na tlačítko **nový** otevřete Průvodce vytvořením nové skupiny ochrany.

    ![Otevřete Průvodce vytvořením nové skupiny ochrany](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

    **Vytvořením nové skupiny ochrany** se zobrazí dialogové okno průvodce.

    ![Dialogové okno Průvodce vytvořením nové skupiny ochrany](./media/backup-azure-backup-server-vmware/protection-wizard.png)

    Klikněte na tlačítko **Další** pro přechod **vybrat typ skupiny ochrany** stránky.

2. Na **typ skupiny ochrany vyberte** vyberte **servery** a pak klikněte na **Další**. **Vybrat členy skupiny** se zobrazí stránka.

3. Na **vybrat členy skupiny** se zobrazí stránka, Dostupní členové a vybrané členy. Vyberte členy, které chcete chránit a pak klikněte na tlačítko **Další**.

    ![Vybrat členy skupiny](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

    Když vyberete člena, pokud vyberete složku obsahující jiné složky nebo virtuálních počítačů, budou vybrány také těchto složek a virtuálních počítačů. Zahrnutí složky a virtuální počítače v nadřazené složky se nazývá ochrany na úrovni složek. Chcete-li odebrat složku nebo virtuálního počítače, zrušte zaškrtnutí políčka.

    Pokud virtuální počítač nebo složku obsahující virtuální počítač, je již chráněné na Azure, nemůžete vybrat tohoto virtuálního počítače znovu. To znamená Jakmile je virtuální počítač chráněný do Azure, nemůže být chráněn akci, která zabraňuje pro jeden virtuální počítač se vytváří body obnovení duplicitní. Pokud chcete zobrazit, kterou instanci serveru Azure Backup již chrání členem, přejděte na člen zobrazíte název ochranu serveru.

4. Na **vyberte způsob ochrany dat** stránky, zadejte název pro skupinu ochrany. Nebyly vybrány krátkodobou ochranu (na disk) a online ochranu. Pokud chcete použít online ochrany (do Azure), musíte použít krátkodobou ochranu na disk. Klikněte na tlačítko **Další** pokračovat do rozsahu krátkodobé ochrany.

    ![Vyberte způsob ochrany dat](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. Na **zadat krátkodobé cíle** stránky, pro **rozsah uchování**, zadejte počet dní, které chcete zachovat body obnovení, které jsou *uložené na disk*. Pokud chcete změnit čas a dny v případě, že jsou pořizovány body obnovení, klikněte na tlačítko **upravit**. Krátkodobé body obnovení jsou úplné zálohy. Nejsou přírůstkové zálohy. Jakmile budete spokojeni s krátkodobé cíle, klikněte na tlačítko **Další**.

    ![Určení krátkodobých cílů](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. Na **zkontrolovat přidělení disku** zkontrolujte a v případě potřeby upravte místo na disku pro virtuální počítače. Doporučená přidělení disku jsou založené na rozsahu uchování, který je uveden v **zadat krátkodobé cíle** vyberte typ úlohy a velikosti chráněných dat (identifikovaného v kroku 3).  

  - **Velikost dat:** velikost dat ve skupině ochrany.
  - **Místo na disku:** doporučená velikost místa na disku pro skupinu ochrany. Pokud chcete toto nastavení změnit, měli byste přidělit celkové místo, které je o něco větší než velikost, kdy očekáváte, že každý zdroj dat roste.
  - **Společné umístění dat:** při povolení společné umístění, více datového zdroje v oblasti ochrany můžete namapovat na jednu repliku a obnovení bodu svazku. Společné umístění se nepodporuje pro všechny úlohy.
  - **Automaticky rozšiřovat:** Pokud povolíte toto nastavení, pokud data ve skupině ochrany přesáhnou počáteční přidělení, System Center Data Protection Manager pokusí a zvyšte velikost disku o 25 procent.
  - **Podrobnosti o fondu úložiště:** zobrazuje stav fondu úložiště včetně celkové a zbývající velikosti disku.

    ![Zkontrolovat přidělení disku](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

    Jakmile budete spokojeni s přidělení místa, klikněte na tlačítko **Další**.

7. Na **vyberte způsob vytvoření repliky** určete, jak chcete vygenerovat počáteční kopii nebo repliky chráněných dat na serveru Azure Backup.

    Výchozí hodnota je **automaticky přes síť** a **nyní**. Pokud chcete použít výchozí nastavení, doporučujeme vám, že zadáte dobu mimo špičku. Zvolte **později** a zadejte den a čas.

    Pro velké objemy dat nebo méně než optimální síťové podmínky zvažte replikaci dat offline pomocí vyměnitelného média.

    Po provedení volby, klikněte na tlačítko **Další**.

    ![Vyberte způsob vytvoření repliky](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. Na **možnosti kontroly konzistence** stránky, vyberte, jak a kdy automatizovat kontroly konzistence. Kontroly konzistence můžete spustit, když se stane nekonzistentní data repliky, nebo podle nastaveného plánu.

    Pokud nechcete konfigurovat automatické kontroly konzistence, můžete spustit ruční kontrolu. V oblasti Ochrana serveru Azure Backup konzoly klikněte pravým tlačítkem na skupinu ochrany a pak vyberte **provést kontrolu konzistence**.

    Klikněte na tlačítko **Další** pro přesun na další stránku.

9. Na **zadat Data Online ochrany** vyberte jeden nebo více zdrojů dat, které chcete chránit. Můžete vybrat členy jednotlivě, nebo klikněte na tlačítko **Vybrat vše** vybrat všechny členy. Po výběru členů, klikněte na tlačítko **Další**.

    ![Zadejte data pro online ochranu](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. Na **zadejte plán Online zálohování** stránky, zadejte plán pro generování bodů obnovení ze zálohy disku. Po vygenerování bod obnovení, bude převedena do trezoru služeb zotavení v Azure. Jakmile budete spokojeni s plán online zálohování, klikněte na tlačítko **Další**.

    ![Zadejte plán online zálohování](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. Na **zadejte zásady uchovávání Online** vyberte, jak dlouho chcete zachovat zálohovaná data v Azure. Po definování zásad klikněte na tlačítko **Další**.

    ![Zadejte zásady online uchovávání dat.](./media/backup-azure-backup-server-vmware/retention-policy.png)

    Neexistuje žádný časový limit pro jak dlouho ponechat data v Azure. Pokud ukládáte data bodu obnovení v Azure, jenom limit je, že nemůžete mít více než 9999 bodů obnovení za chráněné instance. V tomto příkladu je chráněný instance serveru VMware.

12. Na **Souhrn** stránka, zkontrolujte podrobnosti pro členy skupiny ochrany a nastavení a pak klikněte na tlačítko **vytvořit skupinu**.

    ![Souhrn nastavení a člena skupiny ochrany](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>Další postup
Pokud používáte Azure Backup Server k ochraně úloh VMware, může zajímat pomocí serveru Azure Backup k ochraně [Microsoft Exchange server](./backup-azure-exchange-mabs.md), [Microsoft SharePoint farmy](./backup-azure-backup-sharepoint-mabs.md), nebo [databáze systému SQL Server](./backup-azure-sql-mabs.md).

Podrobnosti o problémech s zaregistrováním agenta naleznete v tématu Konfigurace skupiny ochrany nebo zálohování úloh, [řešení potíží s Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
