---
title: Zálohování virtuálních počítačů VMware pomocí Azure Backup Server
description: V tomto článku se dozvíte, jak pomocí Azure Backup Server zálohovat virtuální počítače VMware běžící na serveru VMware vCenter/ESXi.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: deb72ad1f2b9b18368ef5134ecc23048b483f3f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84628441"
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

- Ověřte, že používáte verzi vCenter/ESXi, která je podporovaná pro zálohování. Další informace najdete v [tématu](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix)věnovaném matici podpory.
- Ujistěte se, že jste nastavili Azure Backup Server. Pokud jste to ještě neudělali, [udělejte to předtím,](backup-azure-microsoft-azure-backup.md) než začnete. Měli byste používat Azure Backup Server s nejnovějšími aktualizacemi.
- Ujistěte se, že jsou otevřené následující síťové porty:
    - TCP 443 mezi MABS a vCenter
    - TCP 443 a TCP 902 mezi MABS a hostiteli ESXi


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

5. Klikněte pravým tlačítkem **download.zip**  >  **Extrahovat vše**. Soubor. zip extrahuje obsah do složky **certifikáty** , která obsahuje:
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
    Windows Registry Editor Version 5.00
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
    "IgnoreCertificateValidation"=dword:00000001
    ```

2. Uložte soubor na Azure Backup Server počítači s názvem **DisableSecureAuthentication. reg**.

3. Dvojím kliknutím na soubor aktivujte položku registru.

## <a name="create-a-vmware-role"></a>Vytvoření role VMware

Azure Backup Server potřebuje uživatelský účet s oprávněními pro přístup k serveru v-Center nebo hostiteli ESXi. Vytvořte roli VMware s konkrétními oprávněními a pak přidružte uživatelský účet k roli.

1. Přihlaste se k vCenter Server (nebo hostiteli ESXi, pokud nepoužíváte vCenter Server).
2. Na panelu **navigátor** klikněte na možnost **Správa**.

    ![Správa](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. V části role **pro správu**  >  **Roles**klikněte na ikonu Přidat roli (symbol +).

    ![Přidat roli](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. Do **Create Role**  >  **název role**vytvoření role zadejte *BackupAdminRole*. Název role může být libovolný, ale měl by být rozpoznatelný pro účel role.

5. Vyberte oprávnění shrnutá v následující tabulce a pak klikněte na **OK**.  Nová role se zobrazí v seznamu na panelu **role** .
   - Kliknutím na ikonu vedle nadřazeného popisku rozbalte nadřazenou položku a zobrazte podřízená oprávnění.
   - Pokud chcete vybrat oprávnění VirtualMachine, musíte přejít na několik úrovní v nadřazené hierarchii podřízenosti.
   - V rámci nadřazeného oprávnění nemusíte vybírat všechna podřízená oprávnění.

    ![Nadřazená podřízená hierarchie oprávnění](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Oprávnění role

| Oprávnění pro uživatelský účet vCenter 6,7                     | Oprávnění pro uživatelský účet vCenter 6,5                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Úložiště dat cluster.Configurovat clusteru datatstore            | Úložiště dat cluster.Configurovat clusteru datatstore            |
| Úložiště dat. AllocateSpace                                      | Úložiště dat. AllocateSpace                                      |
| Úložiště dat. procházet úložiště dat                                   | Úložiště dat. procházet úložiště dat                                   |
| Úložiště dat. operace se soubory na nízké úrovni                          | Úložiště dat. operace se soubory na nízké úrovni                          |
| Metody Global. disable                                       | Metody Global. disable                                       |
| Metody Global. Enable                                        | Metody Global. Enable                                        |
| Globální. licence                                              | Globální. licence                                              |
| Global. log – událost                                             | Global. log – událost                                             |
| Globální. Správa vlastních atributů                              | Globální. Správa vlastních atributů                              |
| Global. set – vlastní atribut                                  | Global. set – vlastní atribut                                  |
| Host. Local – operace. Vytvořit virtuální počítač                | Host. Local – operace. Vytvořit virtuální počítač                |
| Síť. přiřaďte síť                                       | Síť. přiřaďte síť                                       |
| Partner. Přiřadit virtuální počítač k fondu zdrojů           | Partner. Přiřadit virtuální počítač k fondu zdrojů           |
| vApp. Add – virtuální počítač                                     | vApp. Add – virtuální počítač                                     |
| vApp. Assign – fond zdrojů                                    | vApp. Assign – fond zdrojů                                    |
| vApp. Unregister                                              | vApp. Unregister                                              |
| VirtualMachine.Configuration. Přidat nebo odebrat zařízení          | VirtualMachine.Configuration. Přidat nebo odebrat zařízení          |
| Virtuální machine.Configuration. Získání zapůjčení disku            | Virtuální machine.Configuration. Zapůjčení disku                     |
| Virtuální machine.Configuration. Přidat nový disk                   | Virtuální machine.Configuration. Přidat nový disk                   |
| Virtuální machine.Configuration. Pokročilá konfigurace        | Virtuální machine.Configuration. Upřesnit                       |
| Virtuální machine.Configuration. Přepnout sledování změn disků   | Virtuální machine.Configuration. Sledování změn disků          |
| Zařízení USB hostitele Virtual machine.Configuration.Configurovat     | Virtuální machine.Configuration. Hostitelské zařízení USB               |
| Virtuální machine.Configuration. Zvětšit virtuální disk           | Virtuální machine.Configuration. Zvětšit virtuální disk           |
| Virtuální machine.Configuration. Dotazování nevlastněných souborů           | Virtuální machine.Configuration. Dotazování nevlastněných souborů           |
| Virtuální machine.Configuration. Změnit umístění swapfile     | Virtuální machine.Configuration. Umístění swapfile            |
| Virtuální počítač. Operace hosta. spuštění programu operace hosta | Virtuální počítač. Operace hosta. spuštění programu operace hosta |
| Virtuální počítač. Operace hosta. změny operací hosta | Virtuální počítač. Operace hosta. změny operací hosta |
| Virtuální počítač. Operace hosta. dotazy operace hosta    | Virtuální počítač. Operace hosta. dotazy operace hosta    |
| Virtuální počítač. Působení. Připojení zařízení             | Virtuální počítač. Působení. Připojení zařízení             |
| Virtuální počítač. Působení. Správa hostovaného operačního systému pomocí rozhraní VIX API | Virtuální počítač. Působení. Správa hostovaného operačního systému pomocí rozhraní VIX API |
| Virtuální počítač. Působení. Vypnutí napájení                      | Virtuální počítač. Působení. Vypnutí napájení                      |
| Virtuální počítač. Inventář. vytvořit nový                        | Virtuální počítač. Inventář. vytvořit nový                        |
| Virtuální počítač. Inventář. odebrat                            | Virtuální počítač. Inventář. odebrat                            |
| Virtuální počítač. Inventarizace. Register                          | Virtuální počítač. Inventarizace. Register                          |
| Virtuální počítač. Zřizování. povolení přístupu k disku             | Virtuální počítač. Zřizování. povolení přístupu k disku             |
| Virtuální počítač. Zřizování. povolení přístupu k souborům             | Virtuální počítač. Zřizování. povolení přístupu k souborům             |
| Virtuální počítač. Zřizování. povolení přístupu k disku jen pro čtení   | Virtuální počítač. Zřizování. povolení přístupu k disku jen pro čtení   |
| Virtuální počítač. Zřizování. povolení stahování virtuálního počítače | Virtuální počítač. Zřizování. povolení stahování virtuálního počítače |
| Virtuální počítač. Správa snímků.  Vytvoření snímku       | Virtuální počítač. Správa snímků.  Vytvoření snímku       |
| Virtuální počítač. Správa snímků. Odebrat snímek        | Virtuální počítač. Správa snímků. Odebrat snímek        |
| Virtuální počítač. Správa snímků. Vrátit se ke snímku     | Virtuální počítač. Správa snímků. Vrátit se ke snímku     |

<br>

| **Oprávnění pro uživatelský účet vCenter 6,0**                | **Oprávnění pro uživatelský účet vCenter 5,5** |
| ---------------------------------------------------------- | ------------------------------------------- |
| Úložiště dat. AllocateSpace                                    | Síť. přiřadit                              |
| Globální. Správa vlastních atributů                           | Úložiště dat. AllocateSpace                     |
| Global. set – vlastní atribut                               | VirtualMachine.Config. Sledování změn ve        |
| Host. Local – operace. Vytvořit virtuální počítač              | VirtualMachine. State. RemoveSnapshot         |
| Sítě.  Přiřadit síť                                   | VirtualMachine. State. CreateSnapshot         |
| Partner.  Přiřadit virtuální počítač k fondu zdrojů         | VirtualMachine. Provisioning. DiskRandomRead  |
| Virtuální machine.Configuration. Přidat nový disk                | VirtualMachine. interaktivně. stavu poweroff            |
| Virtuální machine.Configuration. Upřesnit                    | VirtualMachine. Inventory. Create             |
| Virtuální machine.Configuration. Sledování změn disků        | VirtualMachine.Config. AddNewDisk            |
| Virtuální machine.Configuration. Hostitelské zařízení USB             | VirtualMachine.Config. HostUSBDevice         |
| Virtuální machine.Configuration. Dotazování nevlastněných souborů         | VirtualMachine.Config. AdvancedConfig        |
| Virtuální machine.Configuration. Umístění swapfile          | VirtualMachine.Config. SwapPlacement         |
| Virtuální počítač. Interakce. vypnutí                     | Global. ManageCustomFields                   |
| Virtuální počítač. Inventáře. Vytvořit nový                     |                                             |
| Virtuální počítač. Zřizování. povolení přístupu k disku            |                                             |
| Virtuální počítač. Zřizování. Povolení přístupu k disku jen pro čtení |                                             |
| Virtuální počítač. Správa snímků. Vytvořit snímek       |                                             |
| Virtuální počítač. Správa snímků. Odebrat snímek       |                                             |

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

6. V **možnosti vybrat uživatele nebo skupiny**zvolte **BackupAdmin**  >  **Přidat**. V části **Uživatelé**se pro uživatelský účet používá formát *doména \ uživatelské_jméno* . Pokud chcete použít jinou doménu, vyberte ji ze seznamu **domén** . Kliknutím na tlačítko **OK** přidejte vybrané uživatele do dialogového okna **Přidat oprávnění** .

    ![Přidat uživatele BackupAdmin](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. V poli **přiřazená role**v rozevíracím seznamu vyberte **BackupAdminRole**  >  **OK**.

    ![Přiřazení uživatele k roli](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

Na kartě **Spravovat** na panelu **globální oprávnění** se v seznamu zobrazí nový uživatelský účet a přidružená role.

## <a name="add-the-account-on-azure-backup-server"></a>Přidat účet na Azure Backup Server

1. Otevřete Azure Backup Server. Pokud ikonu na ploše nemůžete najít, otevřete Microsoft Azure Backup ze seznamu aplikací.

    ![Ikona Azure Backup Server](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. V konzole Azure Backup Server klikněte na **Správa**  >   **provozní servery**  >  **Spravovat VMware**.

    ![Konzola Azure Backup Server](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. V dialogovém okně **Spravovat pověření** klikněte na tlačítko **Přidat**.

    ![Dialogové okno Azure Backup Server Správa přihlašovacích údajů](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. V části **Přidat pověření**zadejte název a popis nového přihlašovacího údaje a zadejte uživatelské jméno a heslo, které jste definovali na serveru VMware. V tomto postupu se k identifikaci přihlašovacích údajů používá název *Contoso vCenter přihlašovací údaje* . Pokud server VMware a Azure Backup Server nejsou ve stejné doméně, zadejte v uživatelském jménu doménu.

    ![Dialogové okno Azure Backup Server přidat pověření](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Kliknutím na **Přidat** přidejte nové přihlašovací údaje.

    ![Dialogové okno Azure Backup Server Správa přihlašovacích údajů](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>Přidat vCenter Server

Přidejte vCenter Server do Azure Backup Server.

1. V konzole Azure Backup Server klikněte na **Správa**  >  **provozní servery**  >  **Přidat**.

    ![Průvodce přidáním provozního serveru](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. V **Průvodci přidáním provozního serveru**  >  **Vyberte stránku typ provozního serveru** , vyberte **servery VMware**a pak klikněte na **Další**.

    ![Průvodce přidáním provozního serveru](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. V části **Vybrat počítače**  **název serveru/IP adresa**zadejte plně kvalifikovaný název domény nebo IP adresu serveru VMware. Pokud jsou všechny servery ESXi spravované stejným serverem vCenter, zadejte název vCenter. V opačném případě přidejte hostitele ESXi.

    ![Zadat server VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Do pole **port SSL**zadejte port, který se používá ke komunikaci se serverem VMware. 443 je výchozí port, ale můžete jej změnit, pokud server VMware naslouchá na jiném portu.

5. V části **zadat pověření**vyberte přihlašovací údaje, které jste vytvořili dříve.

    ![Zadat přihlašovací údaje](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Kliknutím na **Přidat** přidejte server VMware do seznamu servery. Potom klikněte na **Další**.

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

1. V části **Vybrat členy skupiny**vyberte virtuální počítače (nebo složky VM), které chcete zálohovat. Potom klikněte na **Další**.

    - Když vyberete složku nebo virtuální počítače nebo složky v této složce jsou také vybrány pro zálohování. Můžete zrušit kontrolu složek nebo virtuálních počítačů, které nechcete zálohovat.
1. Pokud je už virtuální počítač nebo složka zálohovaný, nemůžete ho vybrat. Tím je zajištěno, že pro virtuální počítač nejsou vytvořeny duplicitní body obnovení.

    ![Vybrat členy skupiny](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Na stránce **Vybrat způsob ochrany dat** zadejte název skupiny ochrany a nastavení ochrany. Pokud chcete zálohovat do Azure, nastavte krátkodobou ochranu na **disk** a povolte online ochranu. Potom klikněte na **Další**.

    ![Výběr způsobu ochrany dat](./media/backup-azure-backup-server-vmware/name-protection-group.png)

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

1. V **Možnosti kontroly konzistence**vyberte, jak a kdy se mají automatizovat kontroly konzistence. Potom klikněte na **Další**.
      - Můžete spouštět kontroly konzistence, když se data repliky neshodují, nebo podle nastaveného plánu.
      - Pokud nechcete konfigurovat automatické kontroly konzistence, můžete spustit ruční kontrolu. Provedete to tak, že kliknete pravým tlačítkem na skupinu ochrany > **provést kontrolu konzistence**.

1. Na stránce **zadat data online ochrany** vyberte virtuální počítače nebo složky virtuálních počítačů, které chcete zálohovat. Můžete vybrat členy jednotlivě nebo kliknout na **Vybrat vše** pro výběr všech členů. Potom klikněte na **Další**.

    ![Zadat data online ochrany](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Na stránce **zadat plán online zálohování** určete, jak často chcete zálohovat data z místního úložiště do Azure.

    - Body obnovení cloudu pro data budou vygenerovány podle plánu. Potom klikněte na **Další**.
    - Po vygenerování bodu obnovení se přenese do trezoru Recovery Services v Azure.

    ![Zadat plán online zálohování](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Na stránce **zadat zásady uchovávání online** určete, jak dlouho chcete zachovat body obnovení vytvořené z denních, týdenních nebo měsíčních záloh do Azure. pak klikněte na **Další**.

    - Neexistují žádná časová omezení, jak dlouho můžete uchovávat data v Azure.
    - Jediným omezením je, že nemůžete mít více než 9999 bodů obnovení na chráněnou instanci. V tomto příkladu je chráněná instance serverem VMware.

    ![Zadat zásady uchovávání informací online](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. Na stránce **Souhrn** zkontrolujte nastavení a klikněte na **vytvořit skupinu**.

    ![Souhrn a nastavení člena skupiny ochrany](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-parallel-backups"></a>Paralelní zálohování VMware

>[!NOTE]
> Tato funkce se vztahuje na MABS V3 UR1.

V dřívějších verzích MABS byly souběžné zálohy provedeny pouze v rámci skupin ochrany. Díky MABS V3 UR1 jsou všechny zálohy virtuálních počítačů VMWare v rámci jedné skupiny ochrany paralelní, což vede k rychlejšímu zálohování virtuálních počítačů. Všechny úlohy rozdílové replikace VMWare běží paralelně. Ve výchozím nastavení je počet spuštěných úloh paralelně nastaven na hodnotu 8.

Počet úloh můžete upravit pomocí klíče registru, jak je znázorněno níže (ve výchozím nastavení není k dispozici), je třeba ji přidat:

**Cesta ke klíči**:`Software\Microsoft\Microsoft Data Protection Manager\Configuration\ MaxParallelIncrementalJobs\VMWare`<BR>
**Typ klíče**: hodnota DWORD (32-bit).

> [!NOTE]
> Počet úloh můžete změnit na vyšší hodnotu. Pokud nastavíte počet úloh na 1, úlohy replikace se spouštějí sériově. Chcete-li zvýšit číslo na vyšší hodnotu, je nutné vzít v úvahu výkon VMWare. Vezměte v úvahu počet používaných prostředků a další použití požadované na serveru VMWare vSphere a určete počet úloh rozdílové replikace, které mají být spuštěny paralelně. Tato změna také ovlivní jenom nově vytvořenou skupinu ochrany. Pro existující skupiny ochrany musíte dočasně přidat další virtuální počítač do skupiny ochrany. To by mělo aktualizovat konfiguraci skupiny ochrany odpovídajícím způsobem. Tento virtuální počítač můžete po dokončení postupu odebrat ze skupiny ochrany.

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6,7

Pokud chcete zálohovat vSphere 6,7, udělejte toto:

- Povolit TLS 1,2 na serveru DPM

>[!NOTE]
>VMWare 6,7 a vyšší má TLS povolený jako komunikační protokol.

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

## <a name="exclude-disk-from-vmware-vm-backup"></a>Vyloučení disku ze zálohy virtuálního počítače VMware

> [!NOTE]
> Tato funkce se vztahuje na MABS V3 UR1.

S MABS V3 UR1 můžete konkrétní disk vyloučit ze zálohy virtuálního počítače VMware. Konfigurační skript **ExcludeDisk.ps1** se nachází v `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin folder` .

Pokud chcete nakonfigurovat vyloučení disku, postupujte podle následujících kroků:

### <a name="identify-the-vmware-vm-and-disk-details-to-be-excluded"></a>Identifikujte informace o virtuálním počítači VMWare a disku, které se mají vyloučit.

  1. V konzole VMware klikněte na nastavení virtuálního počítače, pro který chcete disk vyloučit.
  2. Vyberte disk, který chcete vyloučit, a poznamenejte si cestu k tomuto disku.

        Pokud například chcete vyloučit pevný disk 2 z TestVM4, cesta k pevnému disku 2 je **[datastore1] TestVM4/TestVM4 \_ 1. vmdk**.

        ![Pevný disk, který se má vyloučit](./media/backup-azure-backup-server-vmware/test-vm.png)

### <a name="configure-mabs-server"></a>Konfigurace serveru MABS

Přejděte na server MABS, kde je virtuální počítač VMware nakonfigurovaný pro ochranu a nakonfigurujte vyloučení disku.

  1. Získejte podrobnosti o hostiteli VMware, který je chráněný na serveru MABS.

        ```powershell
        $psInfo = get-DPMProductionServer
        $psInfo
        ```

        ```output
        ServerName   ClusterName     Domain            ServerProtectionState
        ----------   -----------     ------            ---------------------
        Vcentervm1                   Contoso.COM       NoDatasourcesProtected
        ```

  2. Vyberte hostitele VMware a seznamte se s ochranou virtuálních počítačů pro hostitele VMware.

        ```powershell
        $vmDsInfo = get-DPMDatasource -ProductionServer $psInfo[0] -Inquire
        $vmDsInfo
        ```

        ```output
        Computer     Name     ObjectType
        --------     ----     ----------
        Vcentervm1  TestVM2      VMware
        Vcentervm1  TestVM1      VMware
        Vcentervm1  TestVM4      VMware
        ```

  3. Vyberte virtuální počítač, pro který chcete vyloučit disk.

        ```powershell
        $vmDsInfo[2]
        ```

        ```output
        Computer     Name      ObjectType
        --------     ----      ----------
        Vcentervm1   TestVM4   VMware
        ```

  4. Pokud chcete disk vyloučit, přejděte do `Bin` složky a spusťte skript *ExcludeDisk.ps1* s následujícími parametry:

        > [!NOTE]
        > Před spuštěním tohoto příkazu zastavte službu DPMRA na serveru MABS. V opačném případě skript vrátí úspěch, ale neaktualizuje seznam vyloučení. Před zastavením služby zajistěte, aby neprobíhaly žádné úlohy.

     **Pokud chcete přidat nebo odebrat disk z vyloučení, spusťte následující příkaz:**

      ```powershell
      ./ExcludeDisk.ps1 -Datasource $vmDsInfo[0] [-Add|Remove] "[Datastore] vmdk/vmdk.vmdk"
      ```

     **Příklad**:

     Chcete-li přidat vyloučení disku pro TestVM4, spusťte následující příkaz:

       ```powershell
      C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -Add "[datastore1] TestVM4/TestVM4\_1.vmdk"
       ```

      ```output
       Creating C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\excludedisk.xml
       Disk : [datastore1] TestVM4/TestVM4\_1.vmdk, has been added to disk exclusion list.
      ```

  5. Ověřte, že je disk přidaný k vyloučení.

     **Pokud chcete zobrazit existující vyloučení pro konkrétní virtuální počítače, spusťte následující příkaz:**

        ```powershell
        ./ExcludeDisk.ps1 -Datasource $vmDsInfo[0] [-view]
        ```

     **Příklad**

        ```powershell
        C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -view
        ```

        ```output
        <VirtualMachine>
        <UUID>52b2b1b6-5a74-1359-a0a5-1c3627c7b96a</UUID>
        <ExcludeDisk>[datastore1] TestVM4/TestVM4\_1.vmdk</ExcludeDisk>
        </VirtualMachine>
        ```

     Po nakonfigurování ochrany tohoto virtuálního počítače nebude vyloučený disk uveden během ochrany.

        > [!NOTE]
        > Pokud tento postup provádíte pro již chráněný virtuální počítač, je nutné spustit kontrolu konzistence ručně po přidání disku k vyloučení.

### <a name="remove-the-disk-from-exclusion"></a>Odebrat disk z vyloučení

Pokud chcete odebrat disk z vyloučení, spusťte následující příkaz:

```powershell
C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -Remove "[datastore1] TestVM4/TestVM4\_1.vmdk"
```

## <a name="next-steps"></a>Další kroky

Pokud chcete řešit problémy při nastavování záloh, přečtěte si [příručku pro řešení potíží pro Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
