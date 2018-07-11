### <a name="open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine"></a>Otevření portů TCP v bráně Windows Firewall pro výchozí instanci databázového stroje
1. Pomocí Vzdálené plochy se připojte k virtuálnímu počítači. Podrobné pokyny pro připojení k virtuálnímu počítači najdete v tématu věnovaném [otevření virtuálního počítače SQL pomocí Vzdálené plochy](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md#remotedesktop).
2. Po přihlášení na úvodní obrazovce zadejte **WF.msc**a potom stiskněte ENTER.
   
    ![Spuštění programu brány firewall](./media/virtual-machines-sql-server-connection-steps/12Open-WF.png)
3. V levém panelu okna **Brána Windows Firewall s pokročilým zabezpečením** klikněte pravým tlačítkem na **Příchozí pravidla** a potom v podokně akcí klikněte na **Nové pravidlo**.
   
    ![Nové pravidlo](./media/virtual-machines-sql-server-connection-steps/13New-FW-Rule.png)
4. V dialogovém okně **Průvodce vytvořením nového příchozího pravidla** v části **Typ pravidla** vyberte **Port** a potom klikněte na **Další**.
5. V dialogovém okně **Protokol a porty** použijte výchozí nastavení **TCP**. Do pole **Konkrétní místní porty** potom zadejte číslo portu instance databázového stroje (**1433** pro výchozí instanci, nebo vaši volbu pro privátní port vybranou v kroku pro koncový bod).
   
    ![Port 1433 protokolu TCP](./media/virtual-machines-sql-server-connection-steps/14Port-1433.png)
6. Klikněte na **Další**.
7. V dialogovém okně **Akce** vyberte **Povolit připojení** a potom klikněte na **Další**.
   
    **Poznámka k zabezpečení:** Výběr nastavení **Povolit připojení, je-li zabezpečené** může zvýšit úroveň zabezpečení. Tuto možnost vyberte, pokud chcete konfigurovat další možnosti zabezpečení ve vašem prostředí.
   
    ![Povolení připojení](./media/virtual-machines-sql-server-connection-steps/15Allow-Connection.png)
8. V dialogovém okně **Profil** vyberte **Veřejný**, **Privátní** a **Doména**. Pak klikněte na tlačítko **Další**.
   
    **Poznámka k zabezpečení:** Výběr možnosti **Veřejný** umožní přístup přes internet. Pokud je to možné, vybírejte vždy restriktivnější profil.
   
    ![Veřejný profil](./media/virtual-machines-sql-server-connection-steps/16Public-Private-Domain-Profile.png)
9. V dialogovém okně **Název** zadejte název a popis pro toto pravidlo a potom klikněte na **Dokončit**.
   
    ![Název pravidla](./media/virtual-machines-sql-server-connection-steps/17Rule-Name.png)

Podle potřeby otevřete další porty pro ostatní komponenty. Další informace najdete v tématu věnovaném [konfiguraci brány Windows Firewall pro povolení přístupu k SQL Serveru](http://msdn.microsoft.com/library/cc646023.aspx).

### <a name="configure-sql-server-to-listen-on-the-tcp-protocol"></a>Konfigurace naslouchání SQL Serveru pro protokol TCP

[!INCLUDE [Enable TCP](virtual-machines-sql-server-connection-tcp-protocol.md)]

### <a name="configure-sql-server-for-mixed-mode-authentication"></a>Konfigurace SQL Serveru pro kombinovaný režim ověřování
Databázový stroj SQL Serveru nemůže používat ověřování systému Windows bez doménového prostředí. Pokud se chcete připojit k databázovému stroji z jiného počítače, nakonfigurujte SQL Server ověřování ve smíšeném režimu. Smíšený režim ověřování umožňuje ověřování systému SQL Server i ověřování systému Windows.

> [!NOTE]
> Konfigurace ověřování ve smíšeném režimu nemusí být nutná v případě, že jste nakonfigurovali Azure Virtual Network s nakonfigurovaným doménovým prostředím.
> 
> 

1. Zůstaňte připojeni k virtuálnímu počítači a na úvodní stránce zadejte **SQL Server Management Studio** a klikněte na vybranou ikonu.
   
    Při prvním otevření sady Management Studio se musí vytvořit uživatelské prostředí sady Management Studio. Tato operace může chvíli trvat.
2. Management Studio zobrazí dialogové okno **Připojit k serveru**. Do pole **Název serveru** zadejte název virtuálního počítače, který se má připojit k databázovému stroji s využitím Průzkumníka objektů. (Místo názvu virtuálního počítače můžete také jako **název serveru** použít text **(local)** nebo jednu tečku). Vyberte **ověřování Windows**a nechat ***název_virtuálního_počítače * \your_local_administrator** v **uživatelské jméno** pole. Klikněte na **Připojit**.
   
    ![Připojení k serveru](./media/virtual-machines-sql-server-connection-steps/19Connect-to-Server.png)
3. V Průzkumníku objektů systému SQL Server klikněte pravým tlačítkem na název instance SQL Serveru (název virtuálního počítače) a potom klikněte na **Vlastnosti**.
   
    ![Vlastnosti serveru](./media/virtual-machines-sql-server-connection-steps/20Server-Properties.png)
4. Na stránce **Zabezpečení** v části **Ověření serveru** vyberte **SQL Server and Windows Authentication mode** (SQL Server a režim ověřování systému Windows) a potom klikněte na **OK**.
   
    ![Výběr režimu ověřování](./media/virtual-machines-sql-server-connection-steps/21Mixed-Mode.png)
5. Kliknutím na **OK** v dialogovém okně SQL Server Management Studio potvrďte požadavek na restartování SQL Serveru.
6. V Průzkumníku objektů klikněte pravým tlačítkem na server a potom klikněte na **Restartovat**. (Pokud je spuštěný agent systému SQL Server, musí se také restartovat.)
   
    ![Restartování](./media/virtual-machines-sql-server-connection-steps/22Restart2.png)
7. Kliknutím na **Ano** v dialogovém okně SQL Server Management Studio vyjádříte souhlas s restartováním SQL Serveru.

### <a name="create-sql-server-authentication-logins"></a>Vytvoření účtů ověřování serveru SQL
Pokud se chcete připojit k databázovému stroji z jiného počítače, musíte vytvořit nejméně jeden účet ověřování SQL Serveru.

1. V Průzkumníku objektů systému SQL Server rozbalte složku instance serveru, ve které chcete vytvořit nové přihlášení.
2. Klikněte pravým tlačítkem na **Zabezpečení**, přejděte na **Nové** a vyberte **Přihlášení...**.
   
    ![Nové přihlášení](./media/virtual-machines-sql-server-connection-steps/23New-Login.png)
3. V dialogovém okně **Přihlášení – nové** na stránce **Obecné** zadejte jméno nového uživatele do pole **Přihlašovací jméno**.
4. Vyberte **Ověřování SQL Serveru**.
5. Do pole **Heslo** zadejte heslo pro nového uživatele. Do pole **Potvrdit heslo** zadejte toto heslo ještě jednou.
6. Vyberte požadované možnosti pro vynucení hesla (**Enforce password policy** (Vynutit zásady hesla), **Enforce password expiration** (vynutit vypršení platnosti hesla) a **User must change password at next login** (Uživatel musí změnit heslo při příštím přihlášení)). Pokud toto přihlášení používáte pro sebe, nemusíte vyžadovat změnu hesla při příštím přihlášení.
7. V seznamu **Výchozí databáze** vyberte výchozí databázi pro toto přihlášení. Pro tuto možnost je výchozí nastavení **hlavní**. Pokud jste ještě nevytvořili uživatelská databázi, nastavení **hlavní** ponechejte.
   
    ![Vlastnosti přihlášení](./media/virtual-machines-sql-server-connection-steps/24Test-Login.png)
8. Pokud toto je první přihlášení, které vytváříte, budete ho pravděpodobně chtít nastavit jako správce SQL Serveru. Pokud tomu tak je, na stránce **Role serveru** zaškrtněte **správce systému**.
   
   > [!NOTE]
   > Členové pevné role správce serveru mají naprostou kontrolu nad databázovým strojem. Členství v této roli byste měli pečlivě omezit.
   > 
   > 
   
   ![Správce systému](./media/virtual-machines-sql-server-connection-steps/25sysadmin.png)
9. Klikněte na tlačítko OK.

Další informace o přihlášeních SQL Serveru najdete v tématu věnovaném [vytvoření přihlášení](http://msdn.microsoft.com/library/aa337562.aspx).

