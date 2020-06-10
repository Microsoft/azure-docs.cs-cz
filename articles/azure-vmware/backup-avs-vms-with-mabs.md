---
title: Zálohování virtuálních počítačů řešení Azure VMware (AVS) pomocí serveru Microsoft Azure Backup
description: Nakonfigurujte prostředí Azure VMware Solution (AVS) pro zálohování virtuálních počítačů pomocí serveru Microsoft Azure Backup.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 16941753d614bbfacbc63e4ae518986901153662
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84612758"
---
# <a name="back-up-avs-vms-with-microsoft-azure-backup-server"></a>Zálohování virtuálních počítačů se službou AVS pomocí serveru Microsoft Azure Backup

V tomto článku se seznámíme s postupy pro zálohování virtuálních počítačů VMware, které běží na řešení Azure VMware (AVS) pomocí Azure Backup Server. Než začnete, ujistěte se, že jste důkladně provedli [nastavení Microsoft Azure Backup serveru pro funkci AVS](set-up-mabs-for-avs.md). 

Pak projdeme všemi potřebnými postupy:

> [!div class="checklist"] 
> * Nastavte zabezpečený kanál tak, aby Azure Backup Server mohl komunikovat se servery VMware přes protokol HTTPS. 
> * Přidejte přihlašovací údaje účtu do Azure Backup Server. 
> * Přidejte vCenter do Azure Backup Server. 
> * Nastavte skupinu ochrany, která obsahuje virtuální počítače VMware, které chcete zálohovat, zadejte nastavení zálohování a naplánujte zálohování.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Vytvoření zabezpečeného připojení k vCenter Server

Ve výchozím nastavení Azure Backup Server komunikuje se servery VMware pomocí protokolu HTTPS. Pokud chcete nastavit připojení HTTPS, Stáhněte si certifikát certifikační autority (CA) VMware a naimportujte ho na Azure Backup Server.

### <a name="set-up-the-certificate"></a>Nastavení certifikátu

1. V prohlížeči v Azure Backup Server zadejte adresu URL webového klienta vSphere.

   > [!NOTE] 
   > Pokud se stránka Začínáme VMware nezobrazuje, zkontrolujte připojení a nastavení proxy prohlížeče a zkuste to znovu.

1. Na stránce Začínáme VMware klikněte na **Stáhnout certifikáty důvěryhodných kořenových certifikačních autorit**.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="Webový klient vSphere":::

1. Uložte soubor **download. zip** do Azure Backup Server počítače a potom rozbalte jeho obsah do složky **certifikáty** , která obsahuje:

   - Soubor kořenového certifikátu s příponou začínající číslovanou sekvencí, například. 0 a. 1.

   - Soubor CRL má příponu, která začíná sekvencí, jako je například. r0 nebo. R1.

1. Ve složce **certifikáty** klikněte pravým tlačítkem na soubor kořenového certifikátu a výběrem tlačítka **Přejmenovat** změňte příponu na **. CRT**.

   Ikona souboru se změní na jednu, která představuje kořenový certifikát.

1. Klikněte pravým tlačítkem na kořenový certifikát a vyberte **nainstalovat certifikát**.

1. V **Průvodci importem certifikátu**vyberte jako cíl pro certifikát **místní počítač** a klikněte na **Další**.

   ![Vítá vás Průvodce](../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

   > [!NOTE] 
   > Pokud se zobrazí výzva, potvrďte, že chcete v počítači změny.    

1. Vyberte možnost **umístit všechny certifikáty do následujícího úložiště**a klikněte na tlačítko **Procházet** a vyberte úložiště certifikátů.

   ![Úložiště certifikátů](../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

1. Jako cílovou složku vyberte **Důvěryhodné kořenové certifikační autority** a klikněte na **OK**.

1. Zkontrolujte nastavení a kliknutím na **Dokončit** spusťte Import certifikátu.

   ![Ověřte, jestli je certifikát ve správné složce.](../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

1. Po potvrzení importu certifikátu se přihlaste k vCenter Server a ověřte, že je připojení zabezpečené.

### <a name="enable-tls-12-on-azure-backup-server"></a>Povolit TLS 1,2 na Azure Backup Server

VMWare 6,7 a vyšší má TLS povolený jako komunikační protokol. 

1. Zkopírujte následující nastavení registru a vložte ho do poznámkového bloku a pak soubor uložte jako TLS. REG bez přípony. txt.

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

## <a name="add-the-provisioning-ip-address-for-avs-esxi-hosts-on-azure-backup-server"></a>Přidejte IP adresu zřízení pro hostitele služby AVS ESXi na Azure Backup Server

Během období Preview nevyřešila aplikace AVS hostitele ESX z virtuálního počítače nasazeného ve virtuální síti. Chcete-li přidat položku souboru hostitele na virtuálním počítači s Azure Backup Server, je nutné provést další kroky.

### <a name="identify-ip-address-for-esxi-hosts"></a>Identifikace IP adresy pro hostitele ESXi

1. Otevřete prohlížeč a přihlaste se k adresám URL vCenter. 

   > [!TIP]
   > Adresy URL najdete v článku [připojení k místnímu vCenter vašeho privátního cloudu](tutorial-access-private-cloud.md#connect-to-the-local-vcenter-of-your-private-cloud) .

1. V klientovi vSphere vyberte cluster, u kterého chcete povolit zálohování.

   :::image type="content" source="media/avs-backup/vsphere-client-select-host.png" alt-text="VSphere klient – vybrat hostitele":::

1. Vyberte **Konfigurovat**  >  **síťové**  >  **adaptéry VMKernel**a v seznamu zařízení Identifikujte síťový adaptér, který má povolenou roli **zřizování** , a pak si poznamenejte **IP adresu** a název hostitele ESXi.

   :::image type="content" source="media/avs-backup/vmkernel-adapters-provisioning-enabled.png" alt-text="Adaptéry VMKernel – zřizování povolených zařízení":::

1. Opakujte předchozí krok pro každého hostitele ESXi v rámci každého clusteru, u kterého chcete povolit zálohování.

### <a name="update-the-host-file-on-the-azure-backup-server"></a>Aktualizujte soubor hostitele na Azure Backup Server

1. Otevřete **Poznámkový blok** jako správce.

2. Vyberte **soubor > otevřít** a vyhledejte c:\Windows\System32\Drivers\etc\hosts..

3. Přidejte položku pro každého hostitele ESXi společně s IP adresou, kterou jste identifikovali v předchozí části.

4. Uložte změny a zavřete Poznámkový blok.

## <a name="add-the-account-on-azure-backup-server"></a>Přidat účet na Azure Backup Server


1. Otevřete Azure Backup Server a v konzole Azure Backup server vyberte **Správa**  >  **produkčních serverů**  >  **Spravovat VMware**.

   ![Konzola Azure Backup Server](../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

1. V dialogovém okně **Spravovat pověření** klikněte na tlačítko **Přidat**.

   ![Dialogové okno Azure Backup Server Správa přihlašovacích údajů](../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

1. V části **Přidat pověření**zadejte název a popis nového přihlašovacího údaje a zadejte uživatelské jméno a heslo, které jste definovali na serveru VMware.

   > [!NOTE] 
   > Pokud server VMware a Azure Backup Server nejsou ve stejné doméně, zadejte v uživatelském jménu doménu.

   ![Dialogové okno Azure Backup Server přidat pověření](../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

1. Kliknutím na **Přidat** přidejte nové přihlašovací údaje.

   ![Dialogové okno Azure Backup Server Správa přihlašovacích údajů](../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Přidání vCenter Server do Azure Backup Server

1. V konzole Azure Backup Server klikněte na **Správa**  >  **provozní servery**  >  **Přidat**.

   ![Průvodce přidáním provozního serveru](../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

1. Vyberte **servery VMware** a klikněte na **Další**.

   ![Průvodce přidáním provozního serveru](../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

1. Zadejte IP adresu vCenter.

   ![Zadat server VMware](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

1. Do pole **port SSL**zadejte port, který se používá ke komunikaci s vCenter.

   > [!TIP] 
   > Výchozím portem je port 443, ale můžete ho změnit, pokud se vCenter naslouchá na jiném portu.

1. V části **zadat pověření**vyberte přihlašovací údaje, které jste vytvořili v předchozí části.

1. Kliknutím na tlačítko **Přidat** přidejte Server vCenter do seznamu servery a klikněte na tlačítko **Další**.

   ![Přidat server VMWare a přihlašovací údaje](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

1. Na stránce **Souhrn** kliknutím na tlačítko **Přidat** přidejte vCenter do Azure Backup Server.

   Nový server se přidá hned. vCenter nepotřebuje agenta.

   ![Přidání serveru VMware pro Azure Backup Server](../backup/media/backup-azure-backup-server-vmware/tasks-screen.png)

1. Na stránce **Dokončit** zkontrolujte nastavení a pak klikněte na **Zavřít**.

   ![Stránka dokončit](../backup/media/backup-azure-backup-server-vmware/summary-screen.png)

   Měl by se zobrazit Server vCenter uvedený v části provozní server s typem jako server VMware a stav agenta jako *OK*. Pokud vidíte stav agenta jako *Neznámý*, klikněte na **aktualizovat**.

## <a name="configure-a-protection-group"></a>Konfigurace skupiny ochrany

Skupiny ochrany shromažďují více virtuálních počítačů a používají stejná nastavení pro uchovávání dat a zálohování u všech virtuálních počítačů ve skupině.

1. V konzole Azure Backup Server klikněte na **ochrana**  >  **nové**.

   ![Otevřete Průvodce vytvořením nové skupiny ochrany.](../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Na úvodní stránce průvodce **vytvořením nové skupiny ochrany** klikněte na **Další**.

   ![Dialogové okno Průvodce vytvořením nové skupiny ochrany](../backup/media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Na stránce **Vybrat typ skupiny ochrany** vyberte **servery** a potom klikněte na **Další**. Zobrazí se stránka **Vybrat členy skupiny** .

1. V části **Vybrat členy skupiny**vyberte virtuální počítače (nebo složky VM), které chcete zálohovat. Potom klikněte na **Další**.

   > [!NOTE]
   > Když vyberete složku nebo virtuální počítače nebo složky uvnitř této složky, budou také vybrány pro zálohování. Můžete zrušit kontrolu složek nebo virtuálních počítačů, které nechcete zálohovat. Pokud se už virtuální počítač nebo složka zálohují, nemůžete ho vybrat, což zajistí, aby se pro virtuální počítač nevytvořily duplicitní body obnovení.

   ![Vybrat členy skupiny](../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Na stránce **Vyberte způsob ochrany dat** zadejte název skupiny ochrany a nastavení ochrany. 

1. Nastavte krátkodobou ochranu na **disk**, povolte online ochranu a pak klikněte na **Další**.

   ![Výběr způsobu ochrany dat](../backup/media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Určete, jak dlouho chcete uchovat data zálohovaná na disk.

   - **Rozsah uchování** – počet dnů uchovávání bodů obnovení disku.
   - **Expresní úplné zálohování** – jak často se odebírají body obnovení disku. Chcete-li změnit časy a kalendářní data v případě krátkodobého zálohování, klikněte na tlačítko **Upravit**.

   :::image type="content" source="media/avs-backup/new-protection-group-specify-short-term-goals.png" alt-text="Určení krátkodobých cílů ochrany na disku":::

1. V části **Kontrola přidělení disku**Zkontrolujte místo na disku, které je k dispozici pro zálohování virtuálních počítačů.

   - Doporučené přidělení disku vychází z rozsahu uchování, který jste zadali, typu úlohy a velikosti chráněných dat. Proveďte požadované změny a potom klikněte na tlačítko **Další**.

   - **Velikost dat:** Velikost dat ve skupině ochrany.

   - **Místo na disku:** Doporučená velikost místa na disku pro skupinu ochrany. Pokud chcete toto nastavení změnit, měli byste přidělit celkové místo, které je mírně větší než množství odhadu jednotlivých zdrojů dat.

   - **Podrobnosti fondu úložiště:** Zobrazuje stav fondu úložiště včetně celkové a zbývající velikosti disku.

   :::image type="content" source="media/avs-backup/review-disk-allocation.png" alt-text="Zkontrolujte místo na disku přidělené ve fondu úložiště.":::

   > [!NOTE]
   > V některých scénářích je nahlášená velikost dat vyšší než skutečná velikost virtuálního počítače. Víme o problému a právě ho zkoumáme.

1. Na stránce **Vybrat způsob vytvoření repliky** určete, jak se má provést prvotní zálohování, a klikněte na **Další**.

   - Výchozí hodnota je **automaticky přes síť** a **nyní**.   Pokud použijete výchozí, doporučujeme zadat dobu mimo špičku. Zvolte **později** a zadejte den a čas.

   - U velkých objemů dat nebo méně optimálních síťových stavů zvažte replikaci dat offline pomocí vyměnitelného média.

   ![Výběr metody vytvoření repliky](../backup/media/backup-azure-backup-server-vmware/replica-creation.png)

1. V **Možnosti Kontrola konzistence**vyberte, jak a kdy se mají automatizovat kontroly konzistence, a klikněte na **Další**.

   - Můžete spouštět kontroly konzistence, když se data repliky neshodují, nebo podle nastaveného plánu.

   - Pokud nechcete konfigurovat automatické kontroly konzistence, můžete spustit ruční kontrolu tak, že pravým tlačítkem myši kliknete na skupinu ochrany **provést kontrolu konzistence**.

1. Na stránce **zadat data online ochrany** vyberte virtuální počítače nebo složky virtuálních počítačů, které chcete zálohovat, a pak klikněte na **Další**. 

   > [!TIP]
   > Můžete vybrat členy jednotlivě nebo kliknout na **Vybrat vše** pro výběr všech členů.

   ![Zadat data online ochrany](../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Na stránce **zadat plán online zálohování** určete, jak často chcete zálohovat data z místního úložiště do Azure, a klikněte na **Další**. 

   - Body obnovení cloudu pro data, která se mají vygenerovat podle plánu. 

   - Až se bod obnovení vygeneruje, přenese se do trezoru Recovery Services v Azure.

   ![Zadat plán online zálohování](../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Na stránce **zadat zásady uchovávání online** určete, jak dlouho chcete zachovat body obnovení, které se vytvoří z záloh denně, týdně/měsíčně/ročních záloh do Azure, a potom klikněte na **Další**.

   - Neexistují žádná časová omezení, jak dlouho můžete uchovávat data v Azure.

   - Jediným omezením je, že nemůžete mít více než 9999 bodů obnovení na chráněnou instanci. V tomto příkladu je chráněná instance serverem VMware.

   ![Zadat zásady uchovávání informací online](../backup/media/backup-azure-backup-server-vmware/retention-policy.png)

1. Na stránce **Souhrn** zkontrolujte nastavení a klikněte na **vytvořit skupinu**.

   ![Souhrn a nastavení člena skupiny ochrany](../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="monitor-with-azure-backup-server-console"></a>Monitorování pomocí konzoly Azure Backup Server

Po nakonfigurování skupiny ochrany pro zálohování virtuálních počítačů funkce AVS můžete monitorovat stav úlohy zálohování a výstrahy pomocí konzoly Azure Backup Server. Tady je seznam toho, co můžete monitorovat.

- Na kartě Výstrahy v podokně monitorování můžete monitorovat chyby, varování a obecné informace pro skupinu ochrany, pro určitý chráněný počítač nebo pro závažnost zprávy. Můžete zobrazit aktivní i neaktivní výstrahy a nastavit oznámení e-mailem.

- Na kartě úlohy v podokně monitorování můžete zobrazit úlohy iniciované nástrojem Azure Backup Server pro konkrétní chráněný zdroj dat nebo skupinu ochrany. Můžete sledovat průběh úlohy nebo kontrolovat prostředky spotřebované úlohami.

- V oblasti úloh **ochrana** můžete kontrolovat stav svazků a sdílených složek ve skupině ochrany a kontrolovat nastavení konfigurace, jako je nastavení obnovení, přidělení disku a plán zálohování.

- V oblasti úloh **Správa** můžete zobrazit kartu **disky, online**a **agenti** a ověřit stav disků ve fondu úložiště, registraci do Azure a nasazený stav agenta DPM.

:::image type="content" source="media/avs-backup/monitor-backup-jobs-in-mabs.png" alt-text="Monitorovat stav úloh zálohování v Azure Backup Server":::

## <a name="restore-vmware-virtual-machines"></a>Obnovení virtuálních počítačů VMware

V konzole pro správu Azure Backup Server existují dva způsoby, jak najít obnovitelná data – prohledávání nebo procházení. Při obnovování dat můžete nebo nebudete chtít obnovit data nebo virtuální počítač do stejného umístění. Z tohoto důvodu Azure Backup Server podporuje tři možnosti obnovení pro zálohy virtuálních počítačů VMware:

- **Obnovení původního umístění (OLR)** – k obnovení CHRÁNĚNÉHO virtuálního počítače do původního umístění použijte OLR. Virtuální počítač můžete obnovit do původního umístění jenom v případě, že se od zálohování nepřidaly nebo neodstranily žádné disky. Pokud byly disky přidány nebo odstraněny, je nutné použít obnovení do alternativního umístění.

- **Obnovení do alternativního umístění (ALR)** – Pokud původní virtuální počítač chybí nebo pokud nechcete narušit původní virtuální počítač, OBNOVte virtuální počítač do alternativního umístění. Pokud chcete virtuální počítač obnovit do alternativního umístění, musíte zadat umístění hostitele ESXi, fondu zdrojů, složky a úložiště dat úložiště a cesty. Kvůli rozlišení obnoveného virtuálního počítače z původního virtuálního počítače Azure Backup Server připojí k názvu virtuálního počítače "-obnovený".

- **Individuální obnovení umístění souborů (ilr)** – Pokud je chráněný virtuální počítač s Windows serverem, můžete jednotlivé soubory nebo složky uvnitř virtuálního počítače obnovit pomocí schopnosti ilr Azure Backup Server. Postup obnovení jednotlivých souborů najdete v postupu dále v tomto článku. Obnovení jednotlivých souborů z virtuálního počítače je dostupné jenom pro body obnovení virtuálních počítačů s Windows a disků.

### <a name="restore-a-recovery-point"></a>Obnovení bodu obnovení

1. V konzole správce Azure Backup Server klikněte na zobrazení obnovení. 

1. Pomocí podokna procházení, procházením nebo filtrem Najděte virtuální počítač, který chcete obnovit. Jakmile vyberete virtuální počítač nebo složku, zobrazí se v podokně body obnovení dostupné body obnovení.

   ![Dostupné body obnovení](../backup/media/restore-azure-backup-server-vmware/recovery-points.png)

1. V poli **body obnovení pro** vyberte v nabídce kalendář a rozevírací nabídky datum, kdy byl bod obnovení proveden. Kalendářní data v tučném textu mají dostupné body obnovení. Alternativně můžete kliknout pravým tlačítkem myši na virtuální počítač a vybrat **Zobrazit všechny body obnovení** a pak vybrat bod obnovení ze seznamu.

   > [!NOTE] 
   > Pro krátkodobou ochranu vyberte bod obnovení založený na disku pro rychlejší obnovení. Po vypršení platnosti krátkodobých bodů obnovení uvidíte pouze body obnovení online pro obnovení.

1. Před obnovením z online bodu obnovení ověřte, že pracovní umístění obsahuje dostatek volného místa pro uložení úplné nekomprimované velikosti virtuálního počítače, který chcete obnovit. Pracovní umístění můžete zobrazit nebo změnit spuštěním **Průvodce konfigurací nastavení předplatného**.

   :::image type="content" source="media/avs-backup/mabs-recovery-folder-settings.png" alt-text="Nastavení složky pro obnovení Azure Backup Server":::

1. Kliknutím na tlačítko **obnovit** otevřete **Průvodce obnovením**.

   ![Průvodce obnovením, Kontrola výběru obnovení](../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png)

1. Kliknutím na tlačítko **Další** přejdete na obrazovku **zadat možnosti obnovení** a **dalším** kliknutím na tlačítko Další přejdete na obrazovku **Vybrat typ obnovení** . 

   > [!NOTE]
   > Úlohy VMware nepodporují povolování omezení šířky pásma sítě.

1. Na stránce **Vybrat typ obnovení** vyberte, zda chcete provést obnovení do původní instance nebo do nového umístění, a poté klikněte na tlačítko **Další**.

   - Pokud zvolíte **obnovit do původní instance**, nemusíte v průvodci dělat žádné další volby. Použijí se data pro původní instanci.

   - Pokud zvolíte možnost **Obnovit jako virtuální počítač na jakémkoli hostiteli**, pak na obrazovce **zadat cíl** zadejte informace o **hostiteli ESXi, fondu zdrojů, složce**a **cestě**.

   ![Vybrat typ obnovení](../backup/media/restore-azure-backup-server-vmware/recovery-type.png)

1. Na stránce **Souhrn** zkontrolujte nastavení a kliknutím na tlačítko **obnovit** spusťte proces obnovení. 

   Na obrazovce stav obnovení se zobrazuje průběh operace obnovení.

### <a name="restore-an-individual-file-from-a-vm"></a>Obnovení jednotlivého souboru z virtuálního počítače

Jednotlivé soubory můžete obnovit z chráněného bodu obnovení virtuálního počítače. Tato funkce je dostupná jenom pro virtuální počítače s Windows serverem. Obnovení jednotlivých souborů je podobné jako obnovení celého virtuálního počítače, s výjimkou přechodu na VMDK a vyhledání souborů, které chcete před zahájením procesu obnovení najít. 

> [!NOTE]
> Obnovení jednotlivých souborů z virtuálního počítače je dostupné jenom pro body obnovení virtuálních počítačů s Windows a disků.

1. V konzole správce Azure Backup Server klikněte na zobrazení **obnovení** .

1. Pomocí podokna **procházení** vyhledejte nebo vyfiltrujte virtuální počítač, který chcete obnovit. Jakmile vyberete virtuální počítač nebo složku, zobrazí se v podokně body obnovení dostupné body obnovení.

   ![Dostupné body obnovení](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

1. V podokně **body obnovení pro** vyberte datum, které obsahuje požadovaný bod obnovení (y). V závislosti na tom, jak byla zásada zálohování nakonfigurovaná, můžou mít data více než jeden bod obnovení. 

1. Po výběru dne, kdy byl proveden bod obnovení, se ujistěte, že jste zvolili správný **čas obnovení**. 

   > [!NOTE]
   > Pokud má vybrané datum více bodů obnovení, zvolte ho tak, že ho vyberete v rozevírací nabídce doba obnovení. 

   Po zvolení bodu obnovení se seznam obnovitelných položek zobrazí v podokně **cesta** .  

1. Chcete-li najít soubory, které chcete obnovit, klikněte v podokně **cesta** dvakrát na položku ve sloupci **obnovitelné položky** a vyberte soubor nebo složky, které chcete obnovit. Chcete-li vybrat více položek, stiskněte klávesu **CTRL** při výběru jednotlivých položek. Pomocí podokna **cesta** můžete vyhledat seznam souborů nebo složek zobrazených ve sloupci **obnovitelné položky** .
    
   > [!NOTE]
   > **Seznam hledání dále** nehledá podsložky. Pokud chcete prohledávat podsložky, poklikejte na složku. K přesunu z podřízené složky do nadřazené složky použijte tlačítko **nahoru** . Můžete vybrat více položek (soubory a složky), ale musí být ve stejné nadřazené složce. Položky z více složek nelze obnovit v rámci jedné úlohy obnovení.

   ![Kontrola výběru obnovení](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

1. Po výběru položek pro obnovení klikněte na pásu karet nástroje konzoly pro správu na tlačítko **obnovit** . tím otevřete **Průvodce obnovením**. V Průvodci obnovením obrazovka **Kontrola výběru obnovení** zobrazuje vybrané položky, které mají být obnoveny.

1. Na obrazovce **zadat možnosti obnovení** proveďte jednu z následujících akcí:

   - Klikněte na **Upravit** a povolte omezení šířky pásma sítě.  V dialogovém okně omezení vyberte **Povolit omezení využití šířky pásma sítě** , aby bylo zapnuté. Po povolení nakonfigurujte **Nastavení** a **plán práce**.
    
   - Kliknutím na tlačítko **Další** necháte zakázat omezení sítě.

1. Na obrazovce **Vybrat typ obnovení** klikněte na **Další**. Soubory nebo složky můžete obnovit pouze do síťové složky.

1. Na obrazovce **zadat cíl** klikněte na **Procházet** a vyhledejte síťové umístění pro vaše soubory nebo složky. Azure Backup Server vytvoří složku, do které se zkopírují všechny obnovené položky. Název složky má předponu, MABS_day měsíc-rok. Když vyberete umístění obnovených souborů nebo složky, poskytnou se podrobnosti pro toto umístění (cíl, cílová cesta a dostupný prostor).

   ![Zadejte umístění pro obnovení souborů](../backup/media/restore-azure-backup-server-vmware/specify-destination.png)

1. Na obrazovce **zadat možnosti obnovení** vyberte, které nastavení zabezpečení se má použít. Můžete se rozhodnout změnit omezení využití šířky pásma sítě, ale omezení je ve výchozím nastavení zakázané. Také není povoleno **obnovení sítě San** a **oznámení** .

1. Na obrazovce **Souhrn** zkontrolujte nastavení a kliknutím na tlačítko **obnovit** spusťte proces obnovení. Na obrazovce **stav obnovení** se zobrazuje průběh operace obnovení.

## <a name="next-steps"></a>Další kroky

Pokud chcete řešit problémy při nastavování záloh, přečtěte si příručku pro řešení potíží pro Azure Backup Server.



> [!div class="nextstepaction"]
> [Průvodce odstraňováním potíží pro Azure Backup Server](../backup/backup-azure-mabs-troubleshoot.md)
