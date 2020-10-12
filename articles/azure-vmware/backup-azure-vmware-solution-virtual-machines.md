---
title: Zálohování virtuálních počítačů s řešeními VMware Azure pomocí Azure Backup Server
description: Nakonfigurujte prostředí řešení Azure VMware pro zálohování virtuálních počítačů pomocí Azure Backup Server.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: b8b5236a8da165efbb8e479e25b58872c4a735ee
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893012"
---
# <a name="back-up-azure-vmware-solution-vms-with-azure-backup-server"></a>Zálohování virtuálních počítačů s řešeními VMware Azure pomocí Azure Backup Server

V tomto článku se seznámíme s postupy pro zálohování virtuálních počítačů VMware, které běží na řešení Azure VMware, pomocí Azure Backup Server. Než začnete, ujistěte se, že jste důkladně provedli [nastavení Microsoft Azure Backup serveru pro řešení Azure VMware](set-up-backup-server-for-azure-vmware-solution.md).

Pak projdeme všemi potřebnými postupy:

> [!div class="checklist"] 
> * Nastavte zabezpečený kanál tak, aby Azure Backup Server mohl komunikovat se servery VMware přes protokol HTTPS. 
> * Přidejte přihlašovací údaje účtu do Azure Backup Server. 
> * Přidejte vCenter do Azure Backup Server. 
> * Nastavte skupinu ochrany, která obsahuje virtuální počítače VMware, které chcete zálohovat, zadejte nastavení zálohování a naplánujte zálohování.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Vytvoření zabezpečeného připojení k serveru vCenter

Ve výchozím nastavení Azure Backup Server komunikuje se servery VMware pomocí protokolu HTTPS. Pokud chcete nastavit připojení HTTPS, Stáhněte si certifikát certifikační autority (CA) VMware a naimportujte ho na Azure Backup Server.

### <a name="set-up-the-certificate"></a>Nastavení certifikátu

1. V prohlížeči na Azure Backup Server počítači zadejte adresu URL webového klienta vSphere.

   > [!NOTE] 
   > Pokud se stránka **Začínáme** VMware nezobrazuje, zkontrolujte připojení a nastavení proxy prohlížeče a zkuste to znovu.

1. Na stránce **Začínáme** VMware vyberte možnost **Stáhnout certifikáty důvěryhodných kořenových certifikačních autorit**.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="Webový klient vSphere":::

1. Uložte soubor **download.zip** do Azure Backup Server počítače a potom rozbalte jeho obsah do složky **certifikáty** , která obsahuje:

   - Soubor kořenového certifikátu s příponou začínající číslovanou sekvencí, například. 0 a. 1.
   - Soubor CRL s příponou, která začíná sekvencí, jako je například. r0 nebo. R1.

1. Ve složce **certifikáty** klikněte pravým tlačítkem na soubor kořenového certifikátu a výběrem tlačítka **Přejmenovat** změňte příponu na **. CRT**.

   Ikona souboru se změní na jednu, která představuje kořenový certifikát.

1. Klikněte pravým tlačítkem na kořenový certifikát a vyberte **nainstalovat certifikát**.

1. V **Průvodci importem certifikátu**vyberte jako cíl pro certifikát **místní počítač** a vyberte **Další**.

   ![Úvodní stránka průvodce](../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

   > [!NOTE] 
   > Pokud se zobrazí výzva, potvrďte, že chcete v počítači změny.

1. Vyberte možnost **umístit všechny certifikáty do následujícího úložiště**a výběrem možnosti **Procházet** vyberte úložiště certifikátů.

   ![Úložiště certifikátů](../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

1. Jako cílovou složku vyberte **Důvěryhodné kořenové certifikační autority** a vyberte **OK**.

1. Zkontrolujte nastavení a kliknutím na **Dokončit** spusťte Import certifikátu.

   ![Ověřte, jestli je certifikát ve správné složce.](../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

1. Po potvrzení importu certifikátu se přihlaste k serveru vCenter a potvrďte, že je připojení zabezpečené.

### <a name="enable-tls-12-on-azure-backup-server"></a>Povolit TLS 1,2 na Azure Backup Server

VMware 6,7 a vyšší má TLS povolený jako komunikační protokol. 

1. Zkopírujte následující nastavení registru a vložte je do poznámkového bloku. Pak soubor uložte jako TLS. REG bez přípony. txt.

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

1. Klikněte pravým tlačítkem na protokol TLS. Soubor REG a výběrem možnosti **Sloučit** nebo **otevřít** přidejte nastavení do registru.


## <a name="add-the-account-on-azure-backup-server"></a>Přidat účet na Azure Backup Server

1. Otevřete Azure Backup Server a v konzole Azure Backup server vyberte **Správa**  >  **produkčních serverů**  >  **Spravovat VMware**.

   ![Konzola Azure Backup Server](../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

1. V dialogovém okně **Správa přihlašovacích údajů** vyberte **Přidat**.

   ![V dialogovém okně Správa přihlašovacích údajů vyberte Přidat.](../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

1. V dialogovém okně **Přidat pověření** zadejte název a popis nového přihlašovacího údaje. Zadejte uživatelské jméno a heslo, které jste definovali na serveru VMware.

   > [!NOTE] 
   > Pokud server VMware a Azure Backup Server nejsou ve stejné doméně, zadejte doménu do pole **uživatelské jméno** .

   ![Dialogové okno Azure Backup Server přidat pověření](../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

1. Vyberte **Přidat** a přidejte nové přihlašovací údaje.

   ![Snímek obrazovky se zobrazí dialogové okno Azure Backup Server Správa přihlašovacích údajů s novými přihlašovacími údaji.](../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Přidejte Server vCenter pro Azure Backup Server

1. V konzole Azure Backup server vyberte **Management**  >  **produkční servery**  >  **Přidat**.

   ![Průvodce přidáním provozního serveru](../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

1. Vyberte **servery VMware**a vyberte **Další**.

   ![Průvodce přidáním provozního serveru](../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

1. Zadejte IP adresu vCenter.

   ![Zadat server VMware](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

1. Do pole **port SSL** zadejte port, který se používá ke komunikaci s vCenter.

   > [!TIP] 
   > Výchozím portem je port 443, ale můžete ho změnit, pokud se vCenter naslouchá na jiném portu.

1. V poli **zadat pověření** vyberte přihlašovací údaje, které jste vytvořili v předchozí části.

1. Vyberte **Přidat** , chcete-li přidat vCenter do seznamu servery a vyberte možnost **Další**.

   ![Přidat server VMware a přihlašovací údaje](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

1. Na stránce **Souhrn** vyberte **Přidat** a přidejte vCenter do Azure Backup Server.

   Nový server se přidá hned. vCenter nepotřebuje agenta.

   ![Přidání serveru VMware pro Azure Backup Server](../backup/media/backup-azure-backup-server-vmware/tasks-screen.png)

1. Na stránce **Dokončit** zkontrolujte nastavení a pak vyberte **Zavřít**.

   ![Stránka dokončit](../backup/media/backup-azure-backup-server-vmware/summary-screen.png)

   Měl by se zobrazit Server vCenter uvedený v části **provozní server** s typem jako **Server VMware** a **Stav agenta** jako **OK**. Pokud vidíte **Stav agenta** jako **Neznámý**, vyberte **aktualizovat**.

## <a name="configure-a-protection-group"></a>Konfigurace skupiny ochrany

Skupiny ochrany shromažďují více virtuálních počítačů a používají stejná nastavení pro uchovávání dat a zálohování u všech virtuálních počítačů ve skupině.

1. V konzole Azure Backup server vyberte **ochrana**  >  **nové**.

   ![Otevřete Průvodce vytvořením nové skupiny ochrany.](../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Na úvodní stránce průvodce **vytvořením nové skupiny ochrany** vyberte **Další**.

   ![Dialogové okno Průvodce vytvořením nové skupiny ochrany](../backup/media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Na stránce **Vybrat typ skupiny ochrany** vyberte **servery**a pak vyberte **Další**. Zobrazí se stránka **Vybrat členy skupiny** .

1. Na stránce **Vybrat členy skupiny** vyberte virtuální počítače (nebo složky VM), které chcete zálohovat, a pak vyberte **Další**.

   > [!NOTE]
   > Když vyberete složku nebo virtuální počítače, složky v této složce jsou také vybrány k zálohování. Můžete zrušit kontrolu složek nebo virtuálních počítačů, které nechcete zálohovat. Pokud se už virtuální počítač nebo složka zálohují, nemůžete ho vybrat, což zajistí, aby se pro virtuální počítač nevytvořily duplicitní body obnovení.

   ![Vybrat členy skupiny](../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Na stránce **Vyberte způsob ochrany dat** zadejte název skupiny ochrany a nastavení ochrany. 

1. Nastavte krátkodobou ochranu na **disk**, povolte online ochranu a pak vyberte **Další**.

   ![Výběr způsobu ochrany dat](../backup/media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Určete, jak dlouho chcete uchovat data zálohovaná na disk.

   - **Rozsah uchování**: počet dní, po které jsou uchovávány body obnovení disku.
   - **Expresní úplné zálohování**: jak často se odebírají body obnovení disku. Chcete-li změnit časy nebo datum, kdy dojde k krátkodobému zálohování, vyberte možnost **Upravit**.

   :::image type="content" source="media/azure-vmware-solution-backup/new-protection-group-specify-short-term-goals.png" alt-text="Webový klient vSphere":::

1. Na stránce **zkontrolovat disk Storage přidělení** Zkontrolujte místo na disku, které je k dispozici pro zálohování virtuálních počítačů.

   - Doporučené přidělení disku vychází z rozsahu uchování, který jste zadali, typu úlohy a velikosti chráněných dat. Proveďte požadované změny a pak vyberte **Další**.
   - **Velikost dat:** Velikost dat ve skupině ochrany.
   - **Místo na disku:** Doporučená velikost místa na disku pro skupinu ochrany. Pokud chcete toto nastavení změnit, přidělte celkové místo, které je mírně větší než množství odhadu jednotlivých zdrojů dat.
   - **Podrobnosti fondu úložiště:** Zobrazuje stav fondu úložiště, který obsahuje celkovou a zbývající velikost disku.

   :::image type="content" source="media/azure-vmware-solution-backup/review-disk-allocation.png" alt-text="Webový klient vSphere":::

   > [!NOTE]
   > V některých scénářích je nahlášená velikost dat vyšší než skutečná velikost virtuálního počítače. Víme o problému a právě ho zkoumáme.

1. Na stránce **Vybrat způsob vytvoření repliky** určete, jak se má provést prvotní zálohování, a vyberte **Další**.

   - Výchozí hodnota je **automaticky přes síť** a **nyní**. Pokud použijete výchozí hodnotu, zadejte čas mimo špičku. Pokud zvolíte možnost **pozdější**, zadejte den a čas.
   - U velkých objemů dat nebo méně optimálních síťových stavů zvažte replikaci dat offline pomocí vyměnitelného média.

   ![Výběr metody vytvoření repliky](../backup/media/backup-azure-backup-server-vmware/replica-creation.png)

1. Pro **Možnosti kontroly konzistence**vyberte, jak a kdy chcete automatizovat kontroly konzistence a vybrat **Další**.

   - Můžete spouštět kontroly konzistence, když se data repliky neshodují, nebo podle nastaveného plánu.
   - Pokud nechcete konfigurovat automatické kontroly konzistence, můžete spustit ruční kontrolu tak, že pravým tlačítkem myši kliknete na skupinu ochrany **provést kontrolu konzistence**.

1. Na stránce **zadat data online ochrany** vyberte virtuální počítače nebo složky virtuálních počítačů, které chcete zálohovat, a pak vyberte **Další**. 

   > [!TIP]
   > Můžete vybrat členy individuálně nebo zvolit **možnost Vybrat vše** pro výběr všech členů.

   ![Zadat data online ochrany](../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Na stránce **zadat plán online zálohování** určete, jak často chcete zálohovat data z místního úložiště do Azure, a pak vyberte **Další**. 

   - Body obnovení cloudu pro data, která se mají vygenerovat podle plánu. 
   - Jakmile se bod obnovení vygeneruje, přenese se do trezoru Recovery Services v Azure.

   ![Zadat plán online zálohování](../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Na stránce **zadat zásady uchovávání online** určete, jak dlouho chcete zachovat body obnovení, které se vytvoří z denní, týdenní, měsíční nebo roční zálohy do Azure, a pak vyberte **Další**.

   - Neexistují žádná časová omezení, jak dlouho můžete uchovávat data v Azure.
   - Jediným omezením je, že nemůžete mít více než 9 999 bodů obnovení na chráněnou instanci. V tomto příkladu je chráněná instance serverem VMware.

   ![Zadat zásady uchovávání informací online](../backup/media/backup-azure-backup-server-vmware/retention-policy.png)

1. Na stránce **Souhrn** zkontrolujte nastavení a pak vyberte **vytvořit skupinu**.

   ![Souhrn a nastavení člena skupiny ochrany](../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="monitor-with-the-azure-backup-server-console"></a>Monitorování pomocí konzoly Azure Backup Server

Až nakonfigurujete skupinu ochrany pro zálohování virtuálních počítačů řešení VMware Azure, můžete monitorovat stav úlohy zálohování a výstrahy pomocí konzoly Azure Backup Server. Tady je seznam toho, co můžete monitorovat.

- Na kartě **výstrahy** v podokně **monitorování** můžete monitorovat chyby, varování a obecné informace pro skupinu ochrany, pro určitý chráněný počítač nebo pro závažnost zprávy. Můžete zobrazit aktivní a neaktivní výstrahy a nastavit e-mailová oznámení.
- Na kartě **úlohy** v podokně **monitorování** můžete zobrazit úlohy iniciované nástrojem Azure Backup Server pro konkrétní chráněný zdroj dat nebo skupinu ochrany. Můžete sledovat průběh úlohy nebo kontrolovat prostředky spotřebované úlohami.
- V oblasti úloh **ochrana** můžete kontrolovat stav svazků a sdílených složek ve skupině ochrany. Můžete také kontrolovat nastavení konfigurace, jako je nastavení obnovení, přidělení disku a plán zálohování.
- V oblasti úloh **Správa** můžete zobrazit karty **disky, online**a **agenti** a ověřit stav disků ve fondu úložiště, registraci do Azure a nasazený stav agenta DPM.

:::image type="content" source="media/azure-vmware-solution-backup/monitor-backup-jobs.png" alt-text="Webový klient vSphere":::

## <a name="restore-vmware-virtual-machines"></a>Obnovení virtuálních počítačů VMware

V konzole pro správu Azure Backup Server existují dva způsoby, jak najít obnovitelná data. Můžete hledat nebo procházet. Při obnovování dat můžete nebo nebudete chtít obnovit data nebo virtuální počítač do stejného umístění. Z tohoto důvodu Azure Backup Server podporuje tři možnosti obnovení pro zálohy virtuálních počítačů VMware:

- **Obnovení původního umístění (OLR)**: pomocí OLR obnovte chráněný virtuální počítač do původního umístění. Virtuální počítač můžete obnovit do jeho původního umístění jenom v případě, že se nepřidaly nebo neodstranily žádné disky od chvíle, kdy došlo k zálohování. Pokud se disky přidaly nebo odstranily, musíte použít obnovení do alternativního umístění.
- **Obnovení do alternativního umístění (ALR)**: Pokud chybí původní virtuální počítač nebo pokud nechcete narušit původní virtuální počítač, OBNOVte virtuální počítač do alternativního umístění. Pokud chcete virtuální počítač obnovit do alternativního umístění, musíte zadat umístění hostitele ESXi, fondu zdrojů, složky a úložiště dat úložiště a cesty. Kvůli rozlišení obnoveného virtuálního počítače z původního virtuálního počítače Azure Backup Server připojí k názvu virtuálního počítače "-obnovený".
- **Individuální obnovení umístění souborů (ilr)**: Pokud je chráněný virtuální počítač s Windows serverem, můžete jednotlivé soubory nebo složky uvnitř virtuálního počítače obnovit pomocí funkce ilr Azure Backup Server. Postup obnovení jednotlivých souborů najdete v postupu dále v tomto článku. Obnovení jednotlivých souborů z virtuálního počítače je dostupné jenom pro body obnovení virtuálních počítačů s Windows a disků.

### <a name="restore-a-recovery-point"></a>Obnovení bodu obnovení

1. V konzole správce Azure Backup Server vyberte zobrazení **obnovení** . 

1. Pomocí podokna **procházení** vyhledejte nebo vyfiltrujte virtuální počítač, který chcete obnovit. Jakmile vyberete virtuální počítač nebo složku, zobrazí se v podokně **body obnovení** dostupné body obnovení.

   ![Dostupné body obnovení](../backup/media/restore-azure-backup-server-vmware/recovery-points.png)

1. V podokně **body obnovení pro** vyberte v nabídce kalendář a rozevírací nabídky datum, kdy byl bod obnovení proveden. Kalendářní data v tučném textu mají dostupné body obnovení. Alternativně můžete kliknout pravým tlačítkem myši na virtuální počítač a vybrat **Zobrazit všechny body obnovení** a pak vybrat bod obnovení ze seznamu.

   > [!NOTE] 
   > Pro krátkodobou ochranu vyberte bod obnovení založený na disku pro rychlejší obnovení. Po vypršení platnosti krátkodobých bodů obnovení se zobrazí pouze body obnovení **online** pro obnovení.

1. Před obnovením z online bodu obnovení ověřte, že pracovní umístění obsahuje dostatek volného místa pro uložení úplné nekomprimované velikosti virtuálního počítače, který chcete obnovit. Pracovní umístění můžete zobrazit nebo změnit spuštěním **Průvodce konfigurací nastavení předplatného**.

   :::image type="content" source="media/azure-vmware-solution-backup/mabs-recovery-folder-settings.png" alt-text="Webový klient vSphere":::

1. Kliknutím na tlačítko **obnovit** otevřete **Průvodce obnovením**.

   ![Průvodce obnovením, kontrola stránky pro výběr obnovení](../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png)

1. Kliknutím na tlačítko **Další** přejdete na obrazovku **zadat možnosti obnovení** . Dalším kliknutím na tlačítko **Další** přejdete na obrazovku **Vybrat typ obnovení** . 

   > [!NOTE]
   > Úlohy VMware nepodporují povolování omezení šířky pásma sítě.

1. Na stránce **Vybrat typ obnovení** vyberte, zda chcete provést obnovení do původní instance nebo do nového umístění, a poté vyberte možnost **Další**.

   - Pokud zvolíte **obnovit do původní instance**, nemusíte v průvodci dělat žádné další volby. Použijí se data pro původní instanci.
   - Pokud zvolíte možnost **Obnovit jako virtuální počítač na jakémkoli hostiteli**, pak na obrazovce **zadat cíl** zadejte informace o **hostiteli ESXi**, **fondu zdrojů**, **složce**a **cestě**.

   ![Stránka vybrat typ obnovení](../backup/media/restore-azure-backup-server-vmware/recovery-type.png)

1. Na stránce **Souhrn** zkontrolujte nastavení a výběrem možnosti **obnovit** spusťte proces obnovení. 

   Na obrazovce **stav obnovení** se zobrazuje průběh operace obnovení.

### <a name="restore-an-individual-file-from-a-vm"></a>Obnovení jednotlivého souboru z virtuálního počítače

Jednotlivé soubory můžete obnovit z chráněného bodu obnovení virtuálního počítače. Tato funkce je dostupná jenom pro virtuální počítače s Windows serverem. Obnovení jednotlivých souborů je podobné jako obnovení celého virtuálního počítače, s výjimkou procházení ke VMDK a vyhledání souborů, které chcete, před zahájením procesu obnovení. 

> [!NOTE]
> Obnovení jednotlivých souborů z virtuálního počítače je dostupné jenom pro body obnovení virtuálních počítačů s Windows a disků.

1. V konzole správce Azure Backup Server vyberte zobrazení **obnovení** .

1. Pomocí podokna **procházení** vyhledejte nebo vyfiltrujte virtuální počítač, který chcete obnovit. Jakmile vyberete virtuální počítač nebo složku, zobrazí se v podokně **body obnovení** dostupné body obnovení.

   ![Dostupné body obnovení](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

1. V podokně **body obnovení pro** vyberte pomocí kalendáře datum, které obsahuje požadované body obnovení. V závislosti na tom, jak byla zásada zálohování nakonfigurovaná, můžou mít data více než jeden bod obnovení. 

1. Po výběru dne, kdy byl proveden bod obnovení, se ujistěte, že jste zvolili správný **čas obnovení**. 

   > [!NOTE]
   > Pokud má vybrané datum více bodů obnovení, zvolte ho tak, že ho vyberete v rozevírací nabídce **Doba obnovení** . 

   Po zvolení bodu obnovení se seznam obnovitelných položek zobrazí v podokně **cesta** .

1. Chcete-li najít soubory, které chcete obnovit, v podokně **cesta** dvakrát klikněte na položku ve sloupci **obnovitelné položky** a otevřete ji. Pak vyberte soubor nebo složky, které chcete obnovit. Chcete-li vybrat více položek, vyberte při výběru jednotlivých položek klávesu **CTRL** . Pomocí podokna **cesta** můžete vyhledat seznam souborů nebo složek, které se zobrazí ve sloupci **obnovitelné položky** .
    
   > [!NOTE]
   > **Seznam hledání níže** nehledá podsložky. Pokud chcete prohledávat podsložky, poklikejte na složku. K přesunu z podřízené složky do nadřazené složky použijte tlačítko **nahoru** . Můžete vybrat více položek (soubory a složky), ale musí být ve stejné nadřazené složce. V rámci jedné úlohy obnovení nemůžete obnovit položky z více složek.

   ![Kontrola výběru obnovení](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

1. Po výběru položek pro obnovení klikněte na pásu karet nástroje konzoly pro správu na tlačítko **obnovit** a otevřete **Průvodce obnovením**. V **Průvodci obnovením**obrazovka **Kontrola výběru obnovení** zobrazuje vybrané položky, které mají být obnoveny.

1. Na obrazovce **zadat možnosti obnovení** proveďte jednu z následujících akcí:

   - Vyberte **Upravit** a povolte omezení šířky pásma sítě. V dialogovém okně **omezení** vyberte **Povolit omezení využití šířky pásma sítě** , aby bylo zapnuté. Po povolení nakonfigurujte **Nastavení** a **plán práce**.
   - Pokud chcete zakázat omezení sítě, vyberte možnost **Další** .

1. Na obrazovce **Vybrat typ obnovení** vyberte **Další**. Soubory nebo složky můžete obnovit pouze do síťové složky.

1. Na obrazovce **zadat cíl** vyberte **Procházet** a vyhledejte síťové umístění pro vaše soubory nebo složky. Azure Backup Server vytvoří složku, do které se zkopírují všechny obnovené položky. Název složky má předponu MABS_day měsíc-year. Když vyberete umístění pro obnovené soubory nebo složku, poskytnou se podrobnosti o tomto umístění, jako je například **cíl**, **cílová cesta**a **místo k dispozici**.

   ![Zadejte umístění pro obnovení souborů](../backup/media/restore-azure-backup-server-vmware/specify-destination.png)

1. Na obrazovce **zadat možnosti obnovení** vyberte, které nastavení zabezpečení se má použít. Můžete se rozhodnout změnit omezení využití šířky pásma sítě, ale omezení je ve výchozím nastavení zakázané. Nepovoluje se taky **obnovení sítě San** a **oznámení** .

1. Na obrazovce **Souhrn** zkontrolujte nastavení a výběrem možnosti **obnovit** spusťte proces obnovení. Na obrazovce **stav obnovení** se zobrazuje průběh operace obnovení.

## <a name="next-steps"></a>Další kroky

Pokud chcete řešit problémy při nastavování záloh, přečtěte si příručku pro řešení potíží pro Azure Backup Server.

> [!div class="nextstepaction"]
> [Průvodce odstraňováním potíží pro Azure Backup Server](../backup/backup-azure-mabs-troubleshoot.md)
