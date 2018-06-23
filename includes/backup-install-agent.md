## <a name="download-install-and-register-the-azure-backup-agent"></a>Stažení, instalace a registrace agenta Azure Backup
Po vytvoření trezoru služeb zotavení, nainstalujte agenta zálohování na jednotlivých počítačů systému Windows (Windows Server, klient systému Windows, server System Center Data Protection Manager nebo počítače serveru Azure Backup) použít k zálohování dat do Azure.

1. Otevřít vaše předplatné v [portálu Azure](https://ms.portal.azure.com/).
2. V nabídce vlevo vyberte **Všechny služby** a v seznamu služeb zadejte **Recovery Services**. Klikněte na **Trezory služby Recovery Services**.

   ![otevření trezoru služby Recovery Services](../articles/backup/media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)
3. Na stránce Rychlý Start klikněte na **klienta pro systém Windows Server nebo System Center Data Protection Manager nebo Windows** možnost pod **stáhnout agenta**. Klikněte na tlačítko **Uložit** a zkopírujte ho do místního počítače.
   
    ![Uložit agenta](./media/backup-install-agent/agent.png)
4. Po instalaci agenta, klikněte dvakrát na MARSAgentInstaller.exe spustíte instalaci agenta Azure Backup agent. Vyberte instalační složku a pomocné složky, které jsou potřebné pro agenta. Umístění zadané mezipaměti má nejméně 5 % zálohovaná data jako volné místo.
5. Pokud používáte proxy server pro připojení k Internetu, v **konfiguraci proxy serveru** obrazovky, zadejte podrobnosti o proxy serveru. Pokud používáte ověřený server proxy, zadejte uživatelské jméno a heslo podrobnosti na této obrazovce.
6. Agent Azure Backup nainstaluje rozhraní .NET Framework 4.5 a prostředí Windows PowerShell (Pokud není k dispozici již) pro dokončení instalace.
7. Po instalaci agenta, klikněte na **přejít k registraci** tlačítko pokračujte s pracovním postupem.
   
   ![Registrace](./media/backup-install-agent/register.png)
8. Na obrazovce přihlašovací údaje trezoru vyberte soubor s stažené přihlašovacími údaji.
   
    ![Přihlašovací údaje trezoru](./media/backup-install-agent/vc.png)
   
    Soubor s přihlašovacími údaji je platná pouze pro 48 hodin (po jeho stažení z portálu). Pokud narazíte na chyby na této obrazovce (například "přihlašovací údaje trezoru poskytnuté souboru vypršela platnost"), přihlaste se k portálu Azure a znovu stáhnout soubor s přihlašovacími údaji.
   
    Zkontrolujte, zda instalační program aplikace mohou přistupovat k souboru s přihlašovacími údaji. Pokud narazíte na chyby týkající se přístupu, zkopírujte soubor s přihlašovacími údaji do dočasného umístění v místním počítači a operaci opakujte.
   
    Pokud narazíte na neplatný trezoru pověření chyby, například "zadané neplatné přihlašovací úložiště", trezoru soubor s přihlašovacími údaji je buď poškozený nebo neobsahuje poslední přihlašovací údaje nepřidružili službu obnovení. Opakujte operaci po stažení nový soubor s přihlašovacími údaji trezoru z portálu. K této chybě obvykle dochází, když uživatel klikne **přihlašovací údaje trezoru Stáhnout** možnost na portálu Azure rychle po sobě. V takovém případě je platný pouze druhý soubor přihlašovacích údajů trezoru.
9. V **nastavení šifrování** obrazovky, můžete buď vygenerovat přístupové heslo nebo zadat přístupové heslo (minimálně 16 znaků). Nezapomeňte uložit heslo v zabezpečeném umístění.
   
    ![Šifrování](./media/backup-install-agent/encryption.png)
   
   > [!WARNING]
   > Pokud heslo ztratíte nebo zapomenete; Microsoft vám nemůže pomoci obnovit zálohovaná data. Koncový uživatel vlastní šifrovací přístupové heslo a Microsoft nemá přehled přístupové heslo používané koncovým uživatelem. Uložte soubor na bezpečné místo, jako je vyžadována během operace obnovení.
   > 
   > 
10. Po kliknutí **Dokončit** tlačítko, počítač se úspěšně registrovaný k úložišti a je nyní připraven pro spuštění zálohování na Microsoft Azure.
11. Pokud používáte samostatnou Microsoft Azure Backup, můžete upravit nastavení určené během pracovního postupu registrace kliknutím na **změnit vlastnosti** možnost v Azure Backup konzoly MMC modul snap-in.
    
    ![Změnit vlastnosti](./media/backup-install-agent/change.png)
    
    Případně, pokud používáte Data Protection Manager, můžete upravit nastavení určené během pracovního postupu registrace kliknutím **konfigurace** možnost výběrem **Online** pod **Správu** kartě.
    
    ![Konfigurace služby Azure Backup](./media/backup-install-agent/configure.png)

