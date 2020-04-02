---
title: Zálohování virtuálních počítačů VMware pomocí serveru Azure Backup Server
description: V tomto článku se dozvíte, jak pomocí Azure Backup Server zálohovat virtuální počítače VMware spuštěné na serveru VMware vCenter/ESXi.
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: 951016d393b095b0329ff18861421402e0e18a1a
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529502"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Zálohování virtuálních počítačů VMware pomocí serveru Azure Backup Server

Tento článek vysvětluje, jak zálohovat virtuální počítače VMware spuštěné na hostitelích VMware ESXi/vCenter Serveru do Azure pomocí Azure Backup Server.

Tento článek vysvětluje, jak:

- Nastavte zabezpečený kanál, aby Azure Backup Server mohl komunikovat se servery VMware přes protokol HTTPS.
- Nastavte účet VMware, který Azure Backup Server používá pro přístup k serveru VMware.
- Přidejte přihlašovací údaje účtu do služby Azure Backup.
- Přidejte server vCenter nebo ESXi na server Azure Backup Server.
- Nastavte skupinu ochrany, která obsahuje virtuální počítače VMware, které chcete zálohovat, zadejte nastavení zálohování a naplánujte zálohování.

## <a name="before-you-start"></a>Než začnete

- Ověřte, zda používáte verzi vCenter/ESXi, která je podporovaná pro zálohování. Naleznete na podporu matice [zde](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix).
- Ujistěte se, že jste nastavili Azure Backup Server. Pokud jste tak neučinili, [udělejte to,](backup-azure-microsoft-azure-backup.md) než začnete. Měli byste spustit Azure Backup Server s nejnovějšími aktualizacemi.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Vytvoření zabezpečeného připojení k serveru vCenter

Ve výchozím nastavení azure backup server komunikuje se servery VMware přes protokol HTTPS. Chcete-li nastavit připojení HTTPS, stáhněte si certifikát Certifikační autority VMware (CA) a importujte jej na server Zálohování Azure.

### <a name="before-you-begin"></a>Než začnete

- Pokud nechcete používat protokol HTTPS, můžete [zakázat ověřování certifikátů HTTPS pro všechny servery VMware](backup-azure-backup-server-vmware.md#disable-https-certificate-validation).
- Obvykle se připojujete z prohlížeče v počítači Azure Backup Server k serveru vCenter/ESXi pomocí webového klienta vSphere. Při prvním provedení není připojení zabezpečené a zobrazí následující.
- Je důležité pochopit, jak Azure Backup Server zpracovává zálohy.
  - Jako první krok Azure Backup Server zálohuje data do místního úložiště disků. Azure Backup Server používá fond úložiště, sadu disků a svazků, na kterých Azure Backup Server ukládá body obnovení disku pro jeho chráněná data. Fond úložiště může být přímo připojené úložiště (DAS), fiber channel SAN nebo iSCSI paměťové zařízení nebo SAN. Je důležité zajistit, abyste měli dostatečné úložiště pro místní zálohování dat virtuálního počítače VMware.
  - Azure Backup Server pak zálohuje z místního úložiště disku do Azure.
  - [Získejte pomoc,](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need) abyste zjistili, kolik úložného prostoru potřebujete. Informace jsou pro DPM, ale lze použít i pro Azure Backup Server.

### <a name="set-up-the-certificate"></a>Nastavení certifikátu

Zabezpečený kanál nastavte takto:

1. V prohlížeči na Serveru zálohování Azure zadejte adresu URL webového klienta vSphere. Pokud se přihlašovací stránka nezobrazí, ověřte nastavení připojení a proxy prohlížeče.

    ![Webový klient vSphere](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. Na přihlašovací stránce webového klienta vSphere klepněte na **tlačítko Stáhnout důvěryhodné kořenové certifikáty certifikační autority**.

    ![Stažení důvěryhodného kořenového certifikátu certifikační autority](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. Soubor s názvem **download** je stažen. V závislosti na prohlížeči se zobrazí zpráva s dotazem, zda chcete soubor otevřít nebo uložit.

    ![Stáhnout certifikát certifikační autority](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Uložte soubor do počítače Azure Backup Server s příponou ZIP.

5. Klepněte pravým tlačítkem myši **na soubor download.zip** > **Extract All**. Soubor ZIP extrahuje jeho obsah do složky **certs,** která obsahuje:
   - Kořenový soubor certifikátu s příponou, která začíná číslovkou jako .0 a .1.
   - Soubor CRL má příponu, která začíná sekvencí jako .r0 nebo .r1. Soubor seznamu odvolaných certifikátů je přidružen k certifikátu.

    ![Stažené certifikáty](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

6. Ve složce **certifikáty** klepněte pravým tlačítkem myši na kořenový soubor certifikátu > **Přejmenovat**.

    ![Přejmenování kořenového certifikátu](./media/backup-azure-backup-server-vmware/rename-cert.png)

7. Změňte rozšíření kořenového certifikátu na .crt a potvrďte. Ikona souboru se změní na ikonu, která představuje kořenový certifikát.

8. Klepněte pravým tlačítkem myši na kořenový certifikát a v rozbalovací nabídce vyberte **příkaz Instalovat certifikát**.

9. V **Průvodci importem certifikátu**vyberte jako cíl certifikátu **místní počítač** a klepněte na tlačítko **Další**. Potvrďte, zda jste dotázáni, zda chcete povolit změny v počítači.

    ![Průvodce vítání](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

10. Na stránce **Úložiště certifikátů** vyberte **Umístit všechny certifikáty do následujícího úložiště**a pak klikněte na **Procházet** a vyberte úložiště certifikátů.

    ![Úložiště certifikátů](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

11. V **seznamu Vybrat úložiště certifikátů**vyberte jako cílovou složku certifikátů důvěryhodné **kořenové certifikační úřady** a klepněte na tlačítko **OK**.

    ![Cílová složka certifikátu](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

12. Při **vyplňování Průvodce importem certifikátu**ověřte složku a klepněte na tlačítko **Dokončit**.

    ![Ověření certifikátu ve správné složce](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

13. Po potvrzení importu certifikátu se přihlaste k serveru vCenter A ověřte, zda je připojení zabezpečené.

### <a name="disable-https-certificate-validation"></a>Zakázání ověření certifikátu HTTPS

Pokud máte v rámci organizace zabezpečené hranice a nechcete používat protokol HTTPS mezi servery VMware a počítačem Azure Backup Server, zakažte protokol HTTPS následujícím způsobem:

1. Zkopírujte a vložte následující text do souboru TXT.

```text
Windows Registry Editor Version 5.00
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
"IgnoreCertificateValidation"=dword:00000001
```

2. Uložte soubor do počítače Azure Backup Server s názvem **DisableSecureAuthentication.reg**.

3. Poklepáním na soubor aktivujte položku registru.

## <a name="create-a-vmware-role"></a>Vytvoření role vmware

Azure Backup Server potřebuje uživatelský účet s oprávněními pro přístup k hostiteli v-Center Server/ESXi. Vytvořte roli vmware s určitými oprávněními a přidružte k roli uživatelský účet.

1. Pokud server vCenter nepoužíváte, přihlaste se k serveru vCenter (nebo k hostiteli ESXi).
2. V panelu **Navigátor** klepněte na **položku Správa**.

    ![Správa](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. V**části Role** **pro správu** > klikněte na ikonu přidat roli (symbol + ).

    ![Přidat roli](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. V **části Vytvořit** > **název role**role zadejte *hodnotu BackupAdminRole*. Název role může být, co se vám líbí, ale to by mělo být rozpoznatelné pro účel role.

5. Vyberte oprávnění sumarovaná v následující tabulce a klepněte na tlačítko **OK**.  Nová role se zobrazí v seznamu v panelu **Role.**
   - Kliknutím na ikonu vedle nadřazeného popisku rozbalte nadřazený popisek a zobrazte podřízená oprávnění.
   - Chcete-li vybrat oprávnění VirtualMachine, musíte přejít několik úrovní do nadřazené podřízené hierarchie.
   - Není nutné vybírat všechna oprávnění dítěte v rámci oprávnění rodiče.

    ![Nadřazená podřízená hierarchie oprávnění](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Oprávnění role

| **Oprávnění pro uživatelský účet vCenter 6.7**              | **Oprávnění pro uživatelský účet vCenter 6.5**             |
| --------------------------------------------------------- | -------------------------------------------------------- |
| Datastore.Přidělit místo                                  | Datastore.Přidělit místo                                 |
| Událost Global.Log                                          | Událost Global.Log                                         |
| Global.Manage vlastní atributy                           | Global.Manage vlastní atributy                          |
| Síť.Přiřadit                                            | Síť.Přiřadit                                           |
| Zdrojů. Přiřazení virtuálního počítače do fondu prostředků        | Zdrojů. Přiřazení virtuálního počítače do fondu prostředků       |
| VirtualMachine.Configuration.AddNewDisk                   | VirtualMachine.Configuration.AddNewDisk                  |
| VirtualMachine.Configuration. Přidání nebo odebrání zařízení       | VirtualMachine.Configuration. Přidání nebo odebrání zařízení      |
| VirtualMachine.Configuration.Upřesnit                     | VirtualMachine.Configuration.Upřesnit                    |
| VirtualMachine.Configuration.Přepnout sledování změn disku | VirtualMachine.Configuration.Disk Sledování změn       |
| VirtualMachine.Configuration.Konfigurovat hostitelské zařízení USB   | VirtualMachine.Configuration.Host ZAŘÍZENÍ USB            |
| VirtualMachine.Configuration.Query Unowned Files         | VirtualMachine.Configuration.Query Unowned Files        |
| VirtualMachine.Configuration.Change Umístění odkládacího souboru   | Umístění virtualmachine.configuration.swapového souboru         |
| VirtualMachine.Interaction.Power Off                      | VirtualMachine.Interaction.Power Off                     |
| VirtualMachine.Inventory.Vytvořit nový                       | VirtualMachine.Inventory.Vytvořit nový                      |
| VirtualMachine.Provisioning.Povolit přístup k disku            | VirtualMachine.Provisioning.Povolit přístup k disku           |
| VirtualMachine.Provisioning.Povolit přístup k souborům            | VirtualMachine.Provisioning.Povolit přístup k souborům           |
| VirtualMachine.Provisioning.Povolit přístup k disku jen pro čtení  | VirtualMachine.Provisioning.Povolit přístup k disku jen pro čtení |
| VirtualMachine.Snapshot Management.Create Snímek       | VirtualMachine.Snapshot Management.Create Snímek      |
| VirtualMachine.Snapshot Management.Remove Snímek       | VirtualMachine.Snapshot Management.Remove Snímek      |

<br>

| **Oprávnění pro uživatelský účet vCenter 6.0**                | **Oprávnění pro uživatelský účet vCenter 5.5** |
| ---------------------------------------------------------- | ------------------------------------------- |
| Úložiště dat.Přidělit prostor                                    | Síť.Přiřadit                              |
| Globální.Správa vlastních atributů                           | Úložiště dat.Přidělit prostor                     |
| Global.Set vlastní atribut                               | VirtualMachine.Config.ChangeTracking        |
| Host.Local operace. Vytvoření virtuálního počítače              | VirtualMachine.State.RemoveSnapshot         |
| Síťové.  Přiřadit síť                                   | VirtualMachine.State.CreateSnapshot         |
| Zdrojů.  Přiřazení virtuálního počítače do fondu prostředků         | VirtualMachine.Provisioning.DiskRandomRead  |
| Virtuální počítač. Configuration.Add nový disk                | VirtualMachine.Interact.PowerOff            |
| Virtuální počítač. Konfigurace.Upřesnit                    | VirtualMachine.Inventory.Create             |
| Virtuální počítač. Sledování změn na konfigurace.Disk        | VirtualMachine.Config.PřidatNewDisk            |
| Virtuální počítač. Configuration.Host USB zařízení             | VirtualMachine.Config.HostUSBDevice         |
| Virtuální počítač. Konfigurační.Dotaz nevlastněných souborů         | VirtualMachine.Config.AdvancedConfig        |
| Virtuální počítač. Umístění configuration.Swapfile          | VirtualMachine.Config.SwapPlacement         |
| Virtuální počítač. Interaction.Power Off                     | Global.ManageCustomFields                   |
| Virtuální počítač. Zásob. Vytvořit nový                     |                                             |
| Virtuální počítač. Zřizování.Povolit přístup k disku            |                                             |
| Virtuální počítač. Zajišťování. Povolit přístup k disku jen pro čtení |                                             |
| Virtuální počítač. Správa snímků. Vytvořit snímek       |                                             |
| Virtuální počítač. Správa snímků. Odebrat snímek       |                                             |



## <a name="create-a-vmware-account"></a>Vytvoření účtu VMware

1. V panelu vCenter Server **Navigator** klepněte na **položku Uživatelé a skupiny**. Pokud nepoužíváte vCenter Server, vytvořte účet na příslušném hostiteli ESXi.

    ![Možnost Uživatelé a skupiny](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Zobrazí se panel **Uživatelé a skupiny vCenter.**

2. V panelu **Uživatelé a skupiny vCentra** vyberte kartu **Uživatelé** a klikněte na ikonu Přidat uživatele (symbol + ).

    ![panel Uživatelé a skupiny vCenter](./media/backup-azure-backup-server-vmware/usersandgroups.png)

3. V dialogovém okně **Nový uživatel** přidejte informace o uživateli > **OK**. V tomto postupu je uživatelské jméno BackupAdmin.

    ![Dialogové okno Nový uživatel](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

4. Pokud chcete uživatelský účet přidružit k roli, klikněte v panelu **Navigátor** na **Globální oprávnění**. V panelu **Globální oprávnění** vyberte kartu **Spravovat** a klikněte na ikonu Přidat (symbol + ).

    ![Panel Globální oprávnění](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

5. V **části Kořenová globální oprávnění – Přidat oprávnění**klikněte na **Přidat** a vyberte uživatele nebo skupinu.

    ![Výběr uživatele nebo skupiny](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. V **seznamu Vybrat uživatele/skupiny**zvolte **BackupAdmin** > **Add**. V **poli Uživatelé**se pro uživatelský účet používá formát *domain\username.* Pokud chcete použít jinou doménu, vyberte ji ze seznamu **Doména.** Klepnutím na **OK** přidáte vybrané uživatele do dialogového okna **Přidat oprávnění.**

    ![Přidat uživatele BackupAdmin](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. V **rozevíracím**seznamu vyberte v rozevíracím seznamu **záložní správce role** > **OK**.

    ![Přiřazení uživatele k roli](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

Na kartě **Spravovat** v panelu **Globální oprávnění** se v seznamu zobrazí nový uživatelský účet a přidružená role.

## <a name="add-the-account-on-azure-backup-server"></a>Přidání účtu na Azure Backup Server

1. Otevřete server Zálohování Azure. Pokud ikonu na ploše nenajdete, otevřete microsoft azure backup ze seznamu aplikací.

    ![Ikona zálohovacího serveru Azure](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. V konzole Azure Backup Server klikněte na **položku Správa** >  **produkčních serverů** > **Spravovat vmware**.

    ![Konzola zálohovacího serveru Azure](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. V dialogovém okně **Spravovat pověření** klepněte na tlačítko **Přidat**.

    ![Dialogové okno Spravovat přihlašovací údaje serveru Azure Backup Server](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. Do **pole Přidat pověření**zadejte název a popis nového pověření a zadejte uživatelské jméno a heslo, které jste definovali na serveru VMware. Název *Contoso Vcenter pověření* se používá k identifikaci pověření v tomto postupu. Pokud server VMware a Server zálohování Azure nejsou ve stejné doméně, zadejte doménu v uživatelském jménu.

    ![Dialogové okno Přidat přihlašovací údaje v Azure Backup Server](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Kliknutím na **Přidat** přidejte nové přihlašovací údaje.

    ![Dialogové okno Spravovat přihlašovací údaje serveru Azure Backup Server](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>Přidání serveru vCenter

Přidejte server vCenter na server Azure Backup Server.

1. V konzole Azure Backup Server klikněte na Položku Přidání**produkčních serverů** >  **pro správu** > .**Add**

    ![Průvodce přidáním otevřeného produkčního serveru](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. V **Průvodci přidáním** > výrobního serveru Vyberte stránku**typu produkčního serveru** vyberte **položku Servery vsystému VMware**a klepněte na tlačítko **Další**.

    ![Průvodce přidáním produkčního serveru](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. V **části Select Computers**Server**Name/IP Address**zadejte adresu FQDN nebo IP serveru VMware.   Pokud jsou všechny servery ESXi spravovány stejným programem vCenter, zadejte název vCenter. V opačném případě přidejte hostitele ESXi.

    ![Určení serveru VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. V **portu SSL**zadejte port, který slouží ke komunikaci se serverem VMware. 443 je výchozí port, ale můžete jej změnit, pokud váš server VMware naslouchá na jiném portu.

5. V **poli Zadejte pověření**vyberte pověření, která jste vytvořili dříve.

    ![Určení pověření](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Chcete-li přidat server VMware do seznamu serverů, klepněte na tlačítko **Přidat.** Pak klikněte na **Další**.

    ![Přidání serveru a pověření vsystému VMWare](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Na stránce **Souhrn** klikněte na **Přidat** a přidejte server VMware na server Azure Backup Server. Nový server je přidán okamžitě, žádný agent je potřeba na serveru VMware.

    ![Přidání serveru VMware na server Azure Backup Server](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Ověřte nastavení na stránce **Dokončení.**

   ![Stránka dokončení](./media/backup-azure-backup-server-vmware/summary-screen.png)

Pokud máte více hostitelů ESXi, které nejsou spravovány serverem vCenter, nebo máte více instancí serveru vCenter, je třeba znovu spustit průvodce a přidat servery.

## <a name="configure-a-protection-group"></a>Konfigurace skupiny ochrany

Přidejte virtuální počítače VMware pro zálohování. Skupiny ochrany shromáždit více virtuálních počítačů a použít stejné nastavení uchovávání dat a zálohování pro všechny virtuální počítače ve skupině.

1. V konzole Serveru zálohování Azure klepněte na **položku Ochrana**, > **Nový**.

    ![Otevření průvodce Vytvořit novou skupinu ochrany](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Na úvodní stránce Průvodce **vytvořením nové skupiny ochrany** klepněte na tlačítko **Další**.

    ![Dialogové okno Vytvořit novou skupinu ochrany](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Na stránce **Typ skupiny Vybrat ochranu** vyberte **Servery** a klepněte na tlačítko **Další**. Zobrazí se stránka **Vybrat členy skupiny.**

1. V **části Vybrat členy skupiny**vyberte virtuální uživatele (nebo složky virtuálních zařízení), které chcete zálohovat. Pak klikněte na **Další**.

    - Když vyberete složku, nebo virtuální počítače nebo složky uvnitř této složky jsou také vybrány pro zálohování. Můžete zrušit zaškrtnutí složek nebo virtuálních měn, které nechcete zálohovat.
1. Pokud se virtuální_virtuální vír nebo složka už zálohovat, nemůžete ho vybrat. Tím zajistíte, že duplicitní body obnovení nejsou vytvořeny pro virtuální ho.

    ![Výběr členů skupiny](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Na stránce **Vybrat metodu ochrany dat** zadejte název skupiny ochrany a nastavení ochrany. Chcete-li zálohovat na Azure, nastavte krátkodobou ochranu na **disk** a povolte ochranu online. Pak klikněte na **Další**.

    ![Výběr způsobu ochrany dat](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. V **poli Určení krátkodobých cílů**určete, jak dlouho chcete uchovávat data zálohovaná na disk.
   - V **rozsahu uchování**určete, kolik dní mají být body obnovení disku zachovány.
   - V **četnosti synchronizace**určete, jak často jsou body obnovení disku přijímány.
       - Pokud nechcete nastavit interval zálohování, můžete zkontrolovat **těsně před bodem obnovení,** aby se záloha spustila těsně před plánovaným každým bodem obnovení.
       - Krátkodobé zálohy jsou úplné zálohy a nejsou přírůstkové.
       - Chcete-li změnit časy a data, kdy dojde ke krátkodobým zálohám, klepněte na **tlačítko Změnit.**

         ![Určení krátkodobých cílů](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. V **části Revize přidělení disku**zkontrolujte místo na disku, které je k dispozici pro zálohy virtuálních počítačů. pro virtuální chod.

   - Doporučená přidělení disku jsou založena na zadaném rozsahu uchování, typu pracovního vytížení a velikosti chráněných dat. Proveďte požadované změny a klepněte na tlačítko **Další**.
   - **Velikost dat:** Velikost dat ve skupině ochrany.
   - **Místo na disku:** Doporučené množství místa na disku pro skupinu ochrany. Pokud chcete toto nastavení upravit, měli byste přidělit celkové místo, které je o něco větší než částka, kterou odhadujete, že každý zdroj dat roste.
   - **Colocate data:** Pokud zapnete společné umístění, může více zdrojů dat v ochraně mapovat na jednu repliku a svazek bodů obnovení. Společné umístění není podporováno pro všechny úlohy.
   - **Automaticky růst:** Pokud zapnete toto nastavení, pokud data v chráněné skupině převýšce počáteční přidělení, Azure Backup Server se pokusí zvětšit velikost disku o 25 procent.
   - **Podrobnosti o fondu úložiště:** Zobrazuje stav fondu úložiště, včetně součtu a zbývající velikosti disku.

    ![Zkontrolovat přidělení disku](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. Na stránce **Zvolit metodu vytvoření repliky** určete, jak chcete provést počáteční zálohu, a klepněte na tlačítko **Další**.
   - Výchozí hodnota je **Automaticky v síti** a **nyní**.
   - Pokud použijete výchozí, doporučujeme zadat čas mimo špičku. Zvolte **Později** a zadejte den a čas.
   - U velkého množství dat nebo méně než optimálních podmínek v síti zvažte replikaci dat offline pomocí vyměnitelného média.

    ![Zvolit metodu vytvoření repliky](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. V **části Možnosti kontroly konzistence**vyberte způsob a kdy automatizaci kontrol konzistence. Pak klikněte na **Další**.
      - Kontroly konzistence můžete spustit v případě, že data repliky budou nekonzistentní nebo podle nastaveného plánu.
      - Pokud nechcete konfigurovat automatické kontroly konzistence, můžete spustit ruční kontrolu. Chcete-li to provést, klepněte pravým tlačítkem myši na skupinu ochrany > **provést kontrolu konzistence**.

1. Na stránce **Určit data ochrany online** vyberte složky virtuálních stránek nebo virtuálních stránek, které chcete zálohovat. Členy můžete vybrat jednotlivě nebo kliknutím na **Vybrat vše** vybrat všechny členy. Pak klikněte na **Další**.

    ![Určení dat ochrany online](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Na stránce **Zadejte plán zálohování online** určete, jak často chcete zálohovat data z místního úložiště do Azure.

    - Body obnovení cloudu pro data budou generovány podle plánu. Pak klikněte na **Další**.
    - Po vygenerování bodu obnovení se přenese do trezoru služby Recovery Services v Azure.

    ![Určení plánu zálohování online](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Na stránce **Určit zásady uchovávání informací online** určete, jak dlouho chcete zachovat body obnovení, které jsou vytvořeny z denních/týdenních/měsíčních/ročních záloh do Azure. a potom klepněte na tlačítko **Další**.

    - Neexistuje žádný časový limit, jak dlouho můžete uchovávat data v Azure.
    - Jediným limitem je, že nemůžete mít více než 9999 bodů obnovení na chráněnou instanci. V tomto příkladu je chráněnou instanci server VMware.

    ![Určení zásad uchovávání informací online](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. Na stránce **Souhrn** zkontrolujte nastavení a klikněte na **Vytvořit skupinu**.

    ![Člen skupiny ochrany a souhrn nastavení](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6.7

Chcete-li zálohovat vSphere 6.7, postupujte takto:

- Povolení tls 1.2 na serveru DPM

>[!NOTE]
>VMWare 6.7 a dále měl TLS povoleno jako komunikační protokol.

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

Problémy s problémy při nastavování záloh najdete v [průvodci odstraňováním potíží pro Server zálohování Azure](./backup-azure-mabs-troubleshoot.md).
