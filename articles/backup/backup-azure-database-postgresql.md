---
title: Zálohování Azure Database for PostgreSQL
description: Přečtěte si o Azure Database for PostgreSQL zálohování s dlouhodobou uchováváním (Preview).
ms.topic: conceptual
ms.date: 04/06/2021
ms.custom: references_regions
ms.openlocfilehash: 5eba9d78dda45197c0d1e92195980f3d731734a8
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011700"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>Azure Database for PostgreSQL zálohování s dlouhodobou dobou uchovávání (Preview)

Služba Azure Backup a Azure Database Services společně vytvořila řešení zálohování na podnikové úrovni pro servery Azure Database for PostgreSQL, které uchovávají zálohy po dobu až 10 let.

Kromě dlouhodobého uchovávání řešení má také mnoho dalších možností, jak je uvedeno níže:

- Řízení přístupu na základě role Azure (Azure RBAC) do databáze pomocí ověřování Azure Active Directory a Identita spravované služby (MSI).
- Plánované zálohování řízené zákazníky a zálohování na vyžádání na úrovni jednotlivých databází.
- Obnovení na úrovni databáze na libovolný server Postgres nebo přímo do úložiště objektů BLOB.
- Dlouhodobé uchovávání:
- Centrální monitorování všech operací a úloh.
- Zálohy jsou uloženy v samostatných doménách zabezpečení a selhání. Takže i v případě, že byl zdrojový server napadený nebo dokonce usmrcený, zálohování zůstane v [trezoru záloh](backup-vault-overview.md)v bezpečí.
- Použití **pg_dump** umožňuje větší flexibilitu v obnovení tak, aby bylo možné obnovit napříč verzemi databáze nebo dokonce obnovit pouze část zálohy.

Toto řešení můžete použít nezávisle nebo kromě nativního řešení zálohování, které nabízí Azure PostgreSQL, které nabízí uchovávání až 35 dnů. Nativní řešení je vhodné pro provozní obnovení, například když chcete provést obnovení z poslední zálohy. Řešení Azure Backup vám pomůže s požadavky na dodržování předpisů a podrobněji a flexibilním a flexibilním zálohováním a obnovením.

## <a name="support-matrix"></a>Matice podpory

|Podpora  |Podrobnosti  |
|---------|---------|
|Podporovaná nasazení   |  [Azure Database for PostgreSQL – Jeden server](../postgresql/overview.md#azure-database-for-postgresql---single-server)     |
|Podporované oblasti Azure |  Východní USA, Východní USA 2, Střed USA Střed USA – jih, Západní USA, Západní USA 2, Středozápadní USA, Brazílie – jih, Kanada – střed, Severní Evropa, Západní Evropa, Velká Británie – jih, Velká Británie – západ, Německo – středozápad, Švýcarsko – sever, Švýcarsko – západ, Východní Asie, Jižní Východní Asie, Japonsko – východ, Japonsko – západ, Korea – střed, USA – střed, Spojené arabské emiráty – sever, Kanada – střed, východní Austrálie  |
|Podporované verze Azure PostgreSQL    |   9,5, 9,6, 10, 11      |

## <a name="feature-considerations-and-limitations"></a>Hlediska a omezení funkcí

- Všechny operace jsou podporovány pouze z Azure Portal.
- Doporučený limit pro maximální velikost databáze je 400 GB.
- Zálohování mezi oblastmi není podporováno. To znamená, že nemůžete zálohovat server Azure PostgreSQL do trezoru v jiné oblasti. Podobně můžete obnovit zálohu jenom na server ve stejné oblasti jako trezor.
- V době obnovení se obnovují jenom data. Role nejsou obnoveny.
- Ve verzi Preview doporučujeme, abyste řešení spustili pouze v testovacím prostředí.

## <a name="backup-process"></a>Proces zálohování

1. Toto řešení používá **pg_dump** k převzetí zálohy databází Azure PostgreSQL.

2. Jakmile zadáte databáze Azure PostgreSQL, které chcete zálohovat, služba ověří, jestli má správnou sadu oprávnění a přístup k provedení operace zálohování na serveru a v databázi.

3. Azure Backup rozmíchuje roli pracovního procesu s nainstalovaným rozšířením zálohování. Toto rozšíření komunikuje se serverem Postgres.

4. Toto rozšíření se skládá z koordinátora a modulu plug-in Azure Postgres. I když je koordinátor zodpovědný za aktivaci pracovních postupů pro různé operace, jako je konfigurace zálohování, zálohování a obnovení, je za skutečný tok dat zodpovědný modul plug-in.
  
5. Po aktivaci konfigurace ochrany u vybraných databází služba zálohování nastaví koordinátora s plány zálohování a dalšími podrobnostmi o zásadách.

6. V naplánovaném čase koordinátor komunikuje s modulem plug-in a spustí streamování zálohovaných dat ze serveru Postgres pomocí **pg_dump**.

7. Modul plug-in odesílá data přímo do trezoru služby Backup, takže nepotřebuje pracovní umístění. Data se šifrují pomocí klíčů spravovaných Microsoftem a ukládají se službou Azure Backup v účtech úložiště.

8. Po dokončení přenosu dat koordinátor potvrdí potvrzení u služby zálohování.

    ![Proces zálohování](./media/backup-azure-database-postgresql/backup-process.png)

## <a name="configure-backup-on-azure-postgresql-databases"></a>Konfigurace zálohování pro databáze Azure PostgreSQL

Zálohování můžete nakonfigurovat na více databázích na několika serverech Azure PostgreSQL. Ujistěte se, že požadovaná [oprávnění](#prerequisite-permissions-for-configure-backup-and-restore) , která služba vyžaduje k zálohování serverů Postgres, jsou už nakonfigurovaná.

V následujících pokynech jsou podrobné pokyny ke konfiguraci zálohování v databázích Azure PostgreSQL pomocí Azure Backup:

1. Existují dva způsoby, jak spustit proces:

    1. Přejít na zálohování přehled [služby Backup](backup-center-overview.md)  ->    ->  .

        ![Přejít na centrum zálohování](./media/backup-azure-database-postgresql/backup-center.png)

        Pod položkou **zahájit: Konfigurace zálohování** vyberte **typ zdroje dat** **Azure Database for PostgreSQL**.

        ![V iniciování: Konfigurace zálohování, výběr typu zdroje dat](./media/backup-azure-database-postgresql/initiate-configure-backup.png)

    1. Případně můžete přímo přejít na zálohu [trezorů služby Backup](backup-vault-overview.md)  ->  .

        ![Přejít na trezory služby Backup](./media/backup-azure-database-postgresql/backup-vaults.png)

        ![Vybrat zálohu v úložišti záloh](./media/backup-azure-database-postgresql/backup-backup-vault.png)

1. V části **Konfigurovat zálohování** vyberte **úložiště záloh** , do kterého chcete zálohovat databáze Postgres. Tyto informace jsou předem vyplněné, pokud jste již v kontextu trezoru.

    ![Výběr úložiště záloh v konfiguraci zálohování](./media/backup-azure-database-postgresql/configure-backup.png)

1. Vyberte nebo vytvořte **zásadu zálohování**.

    ![Zvolit zásady zálohování](./media/backup-azure-database-postgresql/backup-policy.png)

1. Vyberte prostředky nebo databáze Postgres, které chcete zálohovat.

    ![Vyberte prostředky, které chcete zálohovat.](./media/backup-azure-database-postgresql/select-resources.png)

1. Můžete si vybrat ze seznamu všech serverů Azure PostgreSQL napříč předplatnými, pokud jsou ve stejné oblasti jako trezor. Rozbalením šipky zobrazíte seznam databází v rámci serveru.

    ![Zvolit servery](./media/backup-azure-database-postgresql/choose-servers.png)

1. Služba spustí tyto kontroly ve vybraných databázích a ověří, zda má Trezor oprávnění zálohovat vybrané servery a databáze Postgres.
    1. Aby bylo možné pokračovat, je nutné, aby se pro všechny databáze **úspěšně** četla **připravenost na zálohování** .
    1. Pokud dojde k chybě, **opravte** chybu a znovu **Ověřte** nebo odeberte databázi z výběru.

    ![Chyby ověření, které se mají opravit](./media/backup-azure-database-postgresql/validation-errors.png)

1. Potvrďte všechny podrobnosti v části **Kontrola a konfigurace** a vyberte **Konfigurovat zálohování** pro odeslání této operace.

    ![Potvrdit podrobnosti v kontrole a konfiguraci](./media/backup-azure-database-postgresql/review-and-configure.png)

1. Jakmile se aktivuje, operace **Konfigurace zálohování** vytvoří instanci zálohování. Stav operace můžete sledovat v podokně [instance zálohování](backup-center-monitor-operate.md#backup-instances) v zobrazení zálohovacího centra nebo trezoru.

    ![Instance zálohování](./media/backup-azure-database-postgresql/backup-instances.png)

1. Zálohy se spouštějí podle plánu zálohování definovaného v zásadách. Úlohy můžete sledovat v části [úlohy zálohování](backup-center-monitor-operate.md#backup-jobs). V současné době můžete zobrazit úlohy za posledních sedm dní.

    ![Úlohy zálohování](./media/backup-azure-database-postgresql/backup-jobs.png)

## <a name="create-backup-policy"></a>Vytvořit zásady zálohování

1. Do **centra zálohování** přejít  ->  **zásady zálohování**  ->  **Přidat**. Případně můžete přejít na zásady zálohování **trezoru záloh**  ->    ->  **Přidat**.

    ![Přidat zásady zálohování](./media/backup-azure-database-postgresql/add-backup-policy.png)

1. Zadejte **název** nové zásady.

    ![Zadejte název zásady.](./media/backup-azure-database-postgresql/enter-policy-name.png)

1. Zadejte plán zálohování. V současné době podporujeme **týdenní** zálohování. Zálohy můžete naplánovat na jeden nebo více dní v týdnu.

    ![Definice plánu zálohování](./media/backup-azure-database-postgresql/define-backup-schedule.png)

1. Definujte nastavení **uchování** . Můžete přidat jedno nebo více pravidel uchovávání. Každé pravidlo uchovávání informací předpokládá vstupy pro konkrétní zálohy a úložiště dat a dobu uchování pro tyto zálohy.

1. Zálohy si můžete uložit v jednom ze dvou úložišť dat (nebo vrstev): **záložní úložiště dat** (standardní úroveň) nebo **úložiště dat archivu** (ve verzi Preview).

   Můžete zvolit **vypršení platnosti** , pokud chcete přesunout zálohu do úložiště dat archivu po jeho vypršení platnosti v úložišti zálohovaných dat.

1. **Výchozí pravidlo uchovávání** se použije při absenci jiného pravidla uchovávání informací a má výchozí hodnotu tří měsíců.

    - Doba uchování v **úložišti zálohovaných dat** se pohybuje v rozmezí sedmi dní až 10 let.
    - Doba uchování v **úložišti dat archivu** je šest měsíců až 10 let.

    ![Upravit dobu uchování](./media/backup-azure-database-postgresql/edit-retention.png)

>[!NOTE]
>Pravidla uchovávání jsou vyhodnocována v předem určeném pořadí priority. Priorita je nejvyšší pro **roční** pravidlo, za nímž následuje **měsíční** a **týdenní** pravidlo. Výchozí nastavení uchování se použije, když žádná jiná pravidla neopravňují. Například stejný bod obnovení může být první úspěšná záloha pokaždé týdne a první úspěšná záloha povedená každý měsíc. Vzhledem k tomu, že je priorita měsíčního pravidla vyšší než týdenní pravidlo, platí uchovávání odpovídající první úspěšné zálohy v každém měsíci.

## <a name="restore"></a>Obnovení

Databázi můžete obnovit na libovolný server Azure PostgreSQL v rámci stejného předplatného, pokud má služba odpovídající sadu oprávnění na cílovém serveru. Ujistěte se, že požadovaná [oprávnění](#prerequisite-permissions-for-configure-backup-and-restore) , která služba vyžaduje k zálohování serverů Postgres, jsou už nakonfigurovaná.

Pomocí tohoto podrobného průvodce můžete spustit obnovení:

1. Existují dva způsoby, jak spustit proces obnovení:
    1. Přejít na [](backup-center-overview.md)  ->    ->  **obnovení** přehled služby Backup Center

    ![Výběr obnovení v centru zálohování](./media/backup-azure-database-postgresql/backup-center-restore.png)

    V části **iniciovat: obnovit** vyberte **typ DataSource** **Azure Database for PostgreSQL**. Vyberte **instanci zálohování**.

    ![Vybrat typ DataSource v iniciování: obnovit](./media/backup-azure-database-postgresql/initiate-restore.png)

    1. Případně můžete přímo přejít na instance zálohování **trezoru služby Backup**  ->  . Vyberte **instanci zálohování** odpovídající databázi, kterou chcete obnovit.

    ![Instance zálohování pro obnovení](./media/backup-azure-database-postgresql/backup-instances-restore.png)

    ![Seznam instancí zálohování](./media/backup-azure-database-postgresql/list-backup-instances.png)

    ![Výběr obnovení](./media/backup-azure-database-postgresql/select-restore.png)

1. **Vyberte bod obnovení** ze seznamu všech úplných záloh dostupných pro vybranou instanci zálohování. Ve výchozím nastavení je vybrán nejnovější bod obnovení.

    ![Vybrat bod obnovení](./media/backup-azure-database-postgresql/select-recovery-point.png)

    ![Seznam bodů obnovení](./media/backup-azure-database-postgresql/list-recovery-points.png)

1. **Parametry obnovení** vstupu. V tomto okamžiku můžete vybrat ze dvou typů obnovení: **Obnovit jako databázi** a **obnovit soubory**.

1. **Obnovit jako databázi**: obnovit zálohovaná data a vytvořit novou databázi na cílovém PostgreSQL serveru.

    - Cílový server může být stejný jako zdrojový server. Přepsání původní databáze však není podporováno.
    - Můžete si vybrat ze serveru ve všech předplatných, ale ve stejné oblasti jako trezor.
    - Vyberte **zkontrolovat a obnovit**. Tím se aktivuje ověření, aby se zkontrolovalo, jestli má služba příslušná oprávnění k obnovení na cílovém serveru.

    ![Obnovit jako databázi](./media/backup-azure-database-postgresql/restore-as-database.png)

1. **Obnovit jako soubory**: vypíše záložní soubory do cílového účtu úložiště (BLOB).

    - Můžete si vybrat z účtů úložiště ve všech předplatných, ale ve stejné oblasti jako trezor.
    - Vyberte cílový kontejner ze seznamu kontejnerů filtrovaného pro vybraný účet úložiště.
    - Vyberte **zkontrolovat a obnovit**. Tím se aktivuje ověření, aby se zkontrolovalo, jestli má služba příslušná oprávnění k obnovení na cílovém serveru.

    ![Obnovit jako soubory](./media/backup-azure-database-postgresql/restore-as-files.png)

1. Pokud je bod obnovení v archivní úrovni, je nutné před obnovením obnovit bod obnovení.
   
   ![Nastavení pro dehydrataci](./media/backup-azure-database-postgresql/rehydration-settings.png)
   
   Zadejte následující další parametry požadované pro vysazování:
   - **Priorita pro vysazování:** Výchozí hodnota je **Standard**.
   - **Doba vysazování:** Maximální doba pro vysazování je 30 dní a minimální doba přisazení je 10 dní. Výchozí hodnota je **15**.
   
   Bod obnovení je uložený v **záložním úložišti dat** pro zadanou dobu trvání opětovného vysazování.


1. Zkontrolujte informace a vyberte **obnovit**. Tím se aktivuje odpovídající úloha obnovení, kterou je možné sledovat v rámci **úloh zálohování**.

>[!NOTE]
>Podpora archivu pro Azure Database for PostgreSQL je v omezeném verzi Public Preview.

## <a name="prerequisite-permissions-for-configure-backup-and-restore"></a>Požadovaná oprávnění pro konfiguraci zálohování a obnovení

Azure Backup tyto přísné bezpečnostní pokyny. I když se jedná o nativní službu Azure, nepředpokládá se oprávnění prostředku a uživatel ho musí explicitně udělit.  Podobně se přihlašovací údaje pro připojení k databázi neukládají. To je důležité pro ochranu vašich dat. Místo toho používáme Azure Active Directory ověřování.

[Stáhněte si tento dokument](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) a získejte automatizovaný skript a související pokyny. Pro účely zálohování a obnovení udělí serveru Azure PostgreSQL odpovídající sadu oprávnění.

## <a name="manage-the-backed-up-azure-postgresql-databases"></a>Správa zálohovaných databází Azure PostgreSQL

Jedná se o operace správy, které můžete provádět na **instancích zálohování**:

### <a name="on-demand-backup"></a>Zálohování na vyžádání

Pokud chcete spustit zálohování, které není v plánu zadaném v zásadách, klikněte na **zálohovat instance** zálohovat  ->  **nyní**.
Vyberte ze seznamu pravidel uchovávání, která byla definována v přidružených zásadách zálohování.

![Spustit zálohování hned](./media/backup-azure-database-postgresql/backup-now.png)

![Vybrat ze seznamu pravidel uchovávání](./media/backup-azure-database-postgresql/retention-rules.png)

### <a name="stop-protection"></a>Zastavení ochrany

Ochranu zálohované položky můžete zastavit. Tím se také odstraní přidružené body obnovení pro danou zálohovanou položku. Pokud body obnovení nejsou v archivní úrovni po dobu minimálně šesti měsíců, bude odstranění těchto bodů obnovení znamenat náklady na předčasné odstranění. Ještě neposkytujeme možnost Zastavit ochranu a přitom zachovat existující body obnovení.

![Zastavení ochrany](./media/backup-azure-database-postgresql/stop-protection.png)

### <a name="change-policy"></a>Změnit zásady

Přidruženou zásadu můžete změnit pomocí instance zálohování.

1. Vyberte   ->  **zásadu změny** instance zálohování.

    ![Změnit zásady](./media/backup-azure-database-postgresql/change-policy.png)

1. Vyberte novou zásadu, kterou chcete použít pro databázi.

    ![Změna přiřazení zásad](./media/backup-azure-database-postgresql/reassign-policy.png)

## <a name="troubleshooting"></a>Řešení potíží

V této části najdete informace o řešení potíží při zálohování databází Azure PostgreSQL pomocí Azure Backup.

### <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

Poskytněte záložnímu úložišti MSI přístup **pro čtení** na serveru PG, který chcete zálohovat nebo obnovit:

K navázání zabezpečeného připojení k databázi PostgreSQL používá Azure Backup model ověřování [Identita spravované služby (MSI)](../active-directory/managed-identities-azure-resources/overview.md) . To znamená, že úložiště záloh bude mít přístup jenom k prostředkům, kterým uživatel výslovně udělil oprávnění.

K trezoru se v době vytváření automaticky přiřadí systémový soubor MSI. K tomuto trezoru je potřeba poskytnout přístup k serverům PostgreSQL, ze kterých máte v úmyslu zálohovat databáze.

Kroky:

1. Na serveru Postgres otevřete podokno **Access Control (IAM)** .

    ![Access Control podokno](./media/backup-azure-database-postgresql/access-control-pane.png)

1. Vyberte **Přidat přiřazení role**.

    ![Přidat přiřazení role](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. V pravém podokně v kontextu, které se otevře, zadejte následující:<br>

    **Role:** Modulu<br>
    **Přiřaďte přístup k:** Zvolit **úložiště záloh**<br>
    Pokud v rozevíracím seznamu nemůžete najít možnost **trezoru záloh** , vyberte **možnost uživatel, skupina nebo objekt služby Azure AD** .<br>

    ![Vybrat roli](./media/backup-azure-database-postgresql/select-role.png)

    **Vyberte:** Zadejte název trezoru záloh, do kterého chcete zálohovat tento server a jeho databáze.<br>

    ![Zadejte název trezoru záloh.](./media/backup-azure-database-postgresql/enter-backup-vault-name.png)

### <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

Vytvořte uživatele zálohy databáze, který se může ověřit pomocí Azure Active Directory:

Tato chyba může pocházet z absence správce Azure Active Directory pro server PostgreSQL nebo v nepřítomnosti záložního uživatele, který se může ověřit pomocí Azure Active Directory.

Kroky:

Přidejte do serveru OSS Správce služby Active Directory:

Tento krok je nutný pro připojení k databázi prostřednictvím uživatele, který se dá ověřit pomocí Azure Active Directory místo hesla. Uživatel s rolí správce Azure AD v Azure Database for PostgreSQL bude mít **azure_ad_admin** role. Jenom role **azure_ad_admin** může vytvořit nové uživatele databáze, kteří se můžou ověřit pomocí Azure AD.

1. V levém navigačním podokně zobrazení serveru klikněte na kartu Správce služby Active Directory a přidejte sami sebe (nebo někoho jiného) jako správce služby Active Directory.

    ![Nastavení správce služby Active Directory](./media/backup-azure-database-postgresql/set-admin.png)

1. Ujistěte se, že jste **uložili** nastavení uživatele Správce služby AD.

    ![Uložit uživatelské nastavení pro správu služby Active Directory](./media/backup-azure-database-postgresql/save-admin-setting.png)

V [tomto dokumentu](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) najdete seznam kroků, které je třeba provést k dokončení kroků pro udělení oprávnění.

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

Vytvořením přístupnosti sítě v síti povolíte příznak **Povolit přístup ke službám Azure** v zobrazení serveru. V zobrazení serveru v podokně **zabezpečení připojení** nastavte příznak **Povolení přístupu ke službám Azure** na **Ano**.

![Povolení přístupu ke službám Azure](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

### <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

#### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>Oprávnění k obnovení kontejneru účtu úložiště při obnovení souborů

1. Dejte záložnímu úložišti MSI oprávnění k přístupu ke kontejnerům účtu úložiště pomocí Azure Portal.
    1. Přejít na **účet úložiště**  ->  **Access Control**  ->  **Přidat přiřazení role**.
    1. Přiřaďte roli **Přispěvatel dat objektu BLOB úložiště** ke službě MSI trezoru záloh.

    ![Přiřazení role Přispěvatel dat objektu BLOB služby Storage](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. Další možností je poskytnout podrobné oprávnění ke konkrétnímu kontejneru, na který obnovujete, pomocí příkazu Azure CLI [AZ role Assignment Create](/cli/azure/role/assignment) .

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. Nahraďte parametr zmocnění **ID aplikace** MSI trezoru a parametrem oboru, který bude odkazovat na váš konkrétní kontejner.
    1. Chcete-li získat **ID aplikace** pro soubor MSI trezoru, vyberte možnost **všechny aplikace** v části **Typ aplikace**:

        ![Vybrat všechny aplikace](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. Vyhledejte název trezoru a zkopírujte ID aplikace:

        ![Hledat název trezoru](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>Další kroky

- [Přehled trezorů služby Backup](backup-vault-overview.md)