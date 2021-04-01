---
title: Zálohování farmy služby SharePoint do Azure pomocí MABS
description: Pomocí Azure Backup Server můžete zálohovat a obnovovat data služby SharePoint. Tento článek poskytuje informace o konfiguraci farmy služby SharePoint, aby bylo možné ukládat požadovaná data do Azure. Chráněná data služby SharePoint můžete obnovit z disku nebo z Azure.
ms.topic: conceptual
ms.date: 04/26/2020
ms.openlocfilehash: 837aabf739431eebaa6406770620329fe6345eb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89375393"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-mabs"></a>Zálohování farmy služby SharePoint do Azure pomocí MABS

Můžete zálohovat farmu služby SharePoint a Microsoft Azure pomocí Microsoft Azure Backup serveru (MABS), a to podobně jako při zálohování jiných zdrojů dat. Azure Backup poskytuje flexibilitu v plánu zálohování k vytvoření denních, týdenních, měsíčních nebo ročních záložních bodů a poskytuje možnosti zásad uchovávání pro různé body zálohování. MABS poskytuje možnost ukládat kopie místních disků pro cíle rychlého obnovení (RTO) a ukládat kopie do Azure pro účely ekonomického a dlouhodobého uchovávání.

Zálohování SharePointu do Azure pomocí MABS je podobný postup pro zálohování SharePointu do DPM (Data Protection Manager) místně. V tomto článku se budou poznamenat konkrétní předpoklady pro Azure.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Podporované verze SharePointu a související scénáře ochrany

Seznam podporovaných verzí služby SharePoint a verze MABS, které jsou nutné k jejich zálohování, najdete v části [MABS Protection Matrix](./backup-mabs-protection-matrix.md) .

## <a name="before-you-start"></a>Než začnete

Před zálohováním farmy služby SharePoint do Azure je třeba potvrdit několik věcí.

### <a name="whats-not-supported"></a>Co není podporováno

* MABS, který chrání farmu služby SharePoint, nechrání vyhledávací indexy ani databáze služby Application Service. Ochranu těchto databází bude nutné nakonfigurovat samostatně.

* MABS neposkytuje zálohu databází SharePoint SQL Server hostovaných ve sdílených složkách souborového serveru se škálováním na více systému (SOFS).

### <a name="prerequisites"></a>Požadavky

Než budete pokračovat, ujistěte se, že jste splnili všechny [předpoklady pro použití Microsoft Azure Backup](backup-azure-dpm-introduction.md#prerequisites-and-limitations) k ochraně úloh. Mezi úlohy pro požadavky patří: vytvoření trezoru služby Backup, stažení přihlašovacích údajů trezoru, instalace agenta Azure Backup a registrace Azure Backup Server s trezorem.

Další předpoklady a omezení:

* Ve výchozím nastavení se při ochraně služby SharePoint budou chránit všechny databáze obsahu (a SharePoint_Config a SharePoint_AdminContent * databází). Pokud chcete přidat přizpůsobení, například prohledávání indexů, šablon, databází aplikačních služeb nebo služby profilů uživatelů, budete muset jejich ochranu nakonfigurovat samostatně. Nezapomeňte povolit ochranu všech složek, které obsahují tyto typy funkcí nebo soubory vlastního nastavení.

* Databáze SharePoint nejde chránit jako zdroj dat SQL Serveru. Můžete obnovit jednotlivé databáze ze zálohy farmy.

* Mějte na paměti, že MABS běží jako **místní systém** a že k zálohování SQL Server databází potřebuje oprávnění správce systému pro tento účet SQL serveru. Na SQL Server, kterou chcete zálohovat, nastavte NT AUTHORITY\SYSTEM na **sysadmin**.

* Pro každý 10 000 000 položek ve farmě musí být na svazku, kde se nachází složka MABS, aspoň 2 GB místa. Toto místo je nezbytné pro generování katalogu. Aby bylo možné používat MABS k provedení konkrétního obnovení položek (kolekce webů, weby, seznamy, knihovny dokumentů, složky, jednotlivé dokumenty a položky seznamu), generování katalogu vytvoří seznam adres URL obsažených v jednotlivých databázích obsahu. Seznam adres URL můžete zobrazit v podokně obnovitelné položky v oblasti úloh obnovení v konzole pro správu MABS.

* Pokud máte ve farmě služby SharePoint SQL Server databáze, které jsou nakonfigurovány s aliasy SQL Server, nainstalujte SQL Server součásti klienta na front-end webovém serveru, který MABS bude chránit.

* V SharePointu 2013 není ochrana uložených položek aplikace podporovaná.

* MABS nepodporuje ochranu vzdáleného FILESTREAM. Proud FILESTREAM musí být součástí databáze.

## <a name="configure-backup"></a>Konfigurace zálohování

Chcete-li zálohovat farmu služby SharePoint, nakonfigurujte ochranu pro službu SharePoint pomocí ConfigureSharePoint.exe a pak vytvořte skupinu ochrany v MABS.

1. **Spusťte ConfigureSharePoint.exe** – tento nástroj nakonfiguruje službu Zapisovač VSS SharePointu \(WSS\) a předá agentovi ochrany přihlašovací údaje k farmě SharePointu. Po nasazení agenta ochrany lze soubor ConfigureSharePoint.exe najít ve `<MABS Installation Path\>\bin` složce na front- \- End webovém serveru.  Pokud máte více serverů WFE, je třeba ji nainstalovat pouze na jeden z nich. Spuštění proveďte takto:

    * Na serveru WFE přejděte na příkazovém řádku do `\<MABS installation location\>\\bin\\` a spusťte `ConfigureSharePoint \[\-EnableSharePointProtection\] \[\-EnableSPSearchProtection\] \[\-ResolveAllSQLAliases\] \[\-SetTempPath <path>\]` , kde:

        * **EnableSharepointProtection** umožňuje ochranu farmy služby SharePoint, povoluje zapisovač VSS a registruje IDENTITU aplikace DCOM WssCmdletsWrapper, která se má spustit jako uživatel, jehož přihlašovací údaje jsou zadané pomocí této možnosti. Tento účet musí být správcem farmy a také místním správcem na front\-end webovém serveru.

        * **EnableSPSearchProtection** povolí ochranu vyhledávací služby WSS 3.0 SP pomocí klíče registru SharePointSearchEnumerationEnabled v části HKLM\\Software\\Microsoft\\ Microsoft Data Protection Manager\\Agent\\2.0\\ na front\-end webovém serveru a zaregistruje identitu objektu WssCmdletsWrapper aplikace DCOM, aby se spouštěla jako uživatel, jehož přihlašovací údaje byly zadány pomocí této možnosti. Tento účet musí být správcem farmy a také místním správcem na front\-end webovém serveru.

        * **ResolveAllSQLAliases** zobrazí všechny aliasy hlášené zapisovačem SharePoint VSS a přeloží je pro odpovídající SQL Server. Zobrazí také jejich přeložené názvy instancí. Pokud jsou servery zrcadlené, zobrazí i zrcadlený server. Oznamuje všechny aliasy, které nejsou přeloženy na SQL Server.

        * **SetTempPath** nastaví proměnné prostředí TEMP a TMP na určenou cestu. Obnovení na úrovni položek se nepodaří, pokud se obnovuje velká kolekce webů, lokalita, seznam nebo položka a v dočasné složce správce farmy není dostatek místa. Pomocí této možnosti lze cestu ke složce dočasných souborů nahradit cestou ke svazku, ve kterém je dostatek místa pro uložení obnovované kolekce lokalit nebo lokality.

    * Zadejte přihlašovací údaje správce farmy. Tento účet musí být členem místní skupiny Správci na serveru WFE. Pokud správce farmy není místní správce, udělte na serveru WFE tato oprávnění:

        * Udělte skupině **WSS_Admin_WPG** úplné řízení ke složce MABS ( `%Program Files%\Data Protection Manager\DPM\` ).

        * Udělte skupině **WSS_Admin_WPG** oprávnění ke čtení klíče registru MABS ( `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager` ).

        Po spuštění ConfigureSharePoint.exe budete muset znovu spustit, pokud dojde ke změně přihlašovacích údajů správce farmy služby SharePoint.

1. Chcete-li vytvořit skupinu ochrany, vyberte možnost Akce **ochrany**  >    >  **vytvořit skupinu ochrany** a v konzole MABS otevřete průvodce **Vytvoření nové skupiny ochrany** .

1. V **Vyberte typ skupiny ochrany** vyberte **servery**.

1. V části **Vybrat členy skupiny** rozbalte server, který obsahuje roli WFE. Pokud existuje více než jeden server WFE, vyberte ten, na kterém jste nainstalovali ConfigureSharePoint.exe.

    Když rozbalíte službu VSS dotazů serveru SharePoint Server MABS, abyste viděli, co data MABS může chránit.  Pokud je databáze SharePointu vzdálená, MABS se k ní připojí. Pokud se zdroje dat služby SharePoint nezobrazí, zkontrolujte, zda je zapisovač VSS spuštěn na serveru SharePoint a všech vzdálených SQL Server a zda je agent MABS nainstalován na serveru SharePoint a ve vzdáleném SQL Server. Také se ujistěte, že databáze služby SharePoint nejsou chráněny jinde jako SQL Server databáze.

1. V **Vyberte způsob ochrany dat** určete, jak chcete zpracovat krátkodobé a dlouhodobé \- zálohování. Krátkodobé \- zálohování je vždycky vždy na disku, s možností zálohování z disku do cloudu Azure s Azure Backup \( pro krátkodobé nebo dlouhodobé \- období \) .

1. V nabídce **Vybrat krátkodobé \- cíle** určete, jak se má na disk zálohovat na krátkodobé \- úložiště.   V poli **Rozsah uchování** zadejte dobu, po kterou chcete data na disku uchovávat. V poli **četnost synchronizací** můžete určit, jak často chcete spouštět přírůstkové zálohování na disk. Pokud nechcete nastavit interval zálohování, můžete zaškrtnout možnost těsně před bodem obnovení, takže MABS spustí expresní úplné zálohování těsně před každým naplánovaným bodem obnovení.

1. Na stránce zkontrolovat přidělení disku zkontrolujte přidělené místo na disku fondu úložiště pro skupinu ochrany.

    **Celková velikost dat** je velikost dat, která chcete zálohovat, a **místo na disku, které se má zřídit v MABS** , je místo, které MABS pro skupinu ochrany doporučuje. MABS vybere ideální záložní svazek na základě nastavení. Možnosti záložního svazku ale můžete upravit v části **Podrobnosti přidělení disku**. V rozevírací nabídce vyberte požadované úložiště pro úlohy. Úpravy, které provedete, změní hodnoty v polích **Celková velikost úložiště** a **Volný úložný prostor** v podokně **Dostupný úložný prostor na disku**. Za nezřízené místo je množství MABS úložiště, které vám nabídne přidání do svazku, aby bylo možné v budoucnu pokračovat v zálohování.

1. V části **Vybrat způsob vytvoření repliky** vyberte, jak chcete zpracovat počáteční úplnou replikaci dat.  Pokud zvolíte replikaci přes síť, doporučujeme vám vybrat dobu mimo špičku. Když máte velké objemy dat nebo ne úplně optimální síťové podmínky, zvažte replikaci dat offline pomocí vyměnitelného média.

1. V možnosti **Vybrat nastavení kontroly konzistence** vyberte, jak chcete kontroly konzistence automatizovat. Spuštění kontroly můžete povolit jenom pro případ, že začnou být nekonzistentní data repliky, nebo podle plánu. Pokud nechcete konfigurovat automatickou kontrolu konzistence, můžete kdykoli spustit ruční kontrolu tak, že pravým tlačítkem myši kliknete na skupinu ochrany v oblasti **ochrana** konzoly MABS a vyberete **provést kontrolu konzistence**.

1. Pokud jste vybrali možnost zálohování do cloudu pomocí služby Azure Backup, na stránce **Zadání dat pro online ochranu** zkontrolujte, jestli jsou vybrané úlohy, které chcete do Azure zálohovat.

1. V **Nastavení plán online zálohování** určete, jak často se má provést přírůstkové zálohování do Azure. Spouštění zálohování můžete naplánovat na každý den, týden, měsíc nebo rok a k datu a v čase, ve kterém se mají spustit. Zálohování se může spouštět až dvakrát denně. Pokaždé, když se zálohování spustí, vytvoří se v Azure bod obnovení dat z kopie zálohovaných dat uložených na disku MABS.

1. V části **zadat zásady online uchovávání dat** můžete určit způsob, jakým se v Azure uchovávají body obnovení vytvořené z denních, týdenních, měsíčních nebo ročních záloh.

1. V části **Zvolit online replikaci** určete, jak se bude provádět počáteční Úplná replikace dat. Replikaci můžete provést po síti nebo můžete provést offline zálohování (offline předvyplnění). Zálohování offline používá funkci Azure Import. [Další informace](./backup-azure-backup-import-export.md).

1. Na stránce **Souhrn** zkontrolujte nastavení. Po výběru **vytvořit skupinu** se spustí počáteční replikace dat. Po dokončení se stav skupiny ochrany na stránce **stav** zobrazí jako **OK** . Potom se provede záloha podle nastavení skupiny ochrany.

## <a name="monitoring"></a>Monitorování

Po vytvoření skupiny ochrany dojde k počáteční replikaci a MABS spustí zálohování a synchronizaci dat SharePointu. MABS sleduje počáteční synchronizaci a následné zálohy.  Data SharePointu můžete sledovat několika způsoby:

* Pomocí výchozího monitorování MABS můžete nastavit oznámení pro proaktivní monitorování publikováním výstrah a konfigurací oznámení. Oznámení můžete odesílat e-mailem pro klíčové, varovné nebo informační výstrahy a stav instancí obnovení.

* Pokud používáte Operations Manager, můžete výstrahy publikovat centrálně.

### <a name="set-up-monitoring-notifications"></a>Nastavení oznámení monitorování

1. V konzole pro správu MABS vyberte   >    >  **Možnosti** akce monitorování.

2. Vyberte **Server SMTP**, zadejte název serveru, port a e-mailovou adresu, ze které se budou odesílat oznámení. Adresa musí být platná.

3. V případě **ověřeného serveru SMTP** zadejte uživatelské jméno a heslo. Uživatelské jméno a heslo musí být název doménového účtu osoby, jejíž adresa "od" je popsaná v předchozím kroku. V opačném případě se doručení oznámení nezdařilo.

4. Pokud chcete otestovat nastavení serveru SMTP, vyberte **Odeslat zkušební E-mail**, zadejte e-mailovou adresu, na kterou má MABS odeslat zkušební zprávu, a pak vyberte **OK**. Vyberte **Možnosti**  >  **oznámení** a vyberte typy výstrah, o kterých chcete příjemce informovat. Do pole **příjemci** zadejte e-mailové adresy všech příjemců, kterým chcete MABS posílat kopie oznámení.

### <a name="publish-operations-manager-alerts"></a>Publikování výstrah Operations Manageru

1. V konzole pro správu MABS vyberte akce **monitorování**  >    >  **Možnosti** akce  >  **Publikování výstrah**  >  **publikovat aktivní výstrahy** .

2. Po povolení **Publikování výstrah** budou všechny existující výstrahy MABS, které mohou vyžadovat akci uživatele, publikovány do protokolu událostí **MABS Alerts** . Agent Operations Manager, který je nainstalovaný na serveru MABS, pak tyto výstrahy publikuje do Operations Manager a nadále aktualizuje konzolu, když se generují nové výstrahy.

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Obnovení položky SharePointu z disku pomocí MABS

V následujícím příkladu bylo obnovení *položky SharePointu* omylem odstraněno a je třeba je obnovit.
![MABS SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Otevřete **konzolu pro správu MABS**. Všechny farmy služby SharePoint, které jsou chráněny pomocí MABS, se zobrazí na kartě **ochrana** .

    ![MABS SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Chcete-li začít obnovovat položku, vyberte kartu **obnovení** .

    ![MABS SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Můžete vyhledat SharePoint pro *Obnovení položky SharePointu* pomocí vyhledávání založeného na zástupných znacích v rámci rozsahu bodů obnovení.

    ![MABS SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Ve výsledcích hledání vyberte příslušný bod obnovení, klikněte na položku pravým tlačítkem myši a potom vyberte možnost **obnovit**.
5. Můžete také procházet různými body obnovení a vybrat databázi nebo položku, kterou chcete obnovit. Vyberte **datum > čas obnovení** a pak vyberte správnou **> sharepointovou farmu > bod obnovení, > položka**.

    ![MABS SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Klikněte na položku pravým tlačítkem myši a pak výběrem tlačítka **obnovit** otevřete **Průvodce obnovením**. Vyberte **Další**.

    ![Kontrola výběru obnovení](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Vyberte typ obnovení, který chcete provést, a potom vyberte **Další**.

    ![Typ obnovení](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > Výběr možnosti **obnovit do původní** v příkladu obnoví položku na původní web služby SharePoint.
   >
   >
8. Vyberte **proces obnovení** , který chcete použít.

   * Vyberte možnost **Obnovit bez použití farmy obnovení** , pokud se farma služby SharePoint nezměnila a je stejná jako obnovený bod obnovení.
   * Vyberte možnost **obnovit pomocí farmy obnovení** , pokud se od vytvoření bodu obnovení změnila farma služby SharePoint.

     ![Proces obnovení](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Zadejte pracovní umístění SQL Server instance pro dočasné obnovení databáze a poskytněte pracovní sdílenou složku na MABS a server, na kterém je spuštěna služba SharePoint pro obnovení položky.

    ![Pracovní proces location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS připojuje databázi obsahu, která je hostitelem položky SharePoint, do dočasné instance SQL Server. Z databáze obsahu obnoví položku a umístí ji do pracovního umístění souboru na MABS. Obnovená položka, která je v pracovním umístění, se teď musí exportovat do pracovního umístění ve farmě služby SharePoint.

    ![Pracovní Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Vyberte možnost **zadat možnosti obnovení** a použít nastavení zabezpečení pro farmu služby SharePoint nebo použít nastavení zabezpečení bodu obnovení. Vyberte **Další**.

    ![Možnosti obnovení](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Můžete zvolit omezení využití šířky pásma sítě. Tím se minimalizuje dopad na provozní server během produkčních hodin.
    >
    >
11. Zkontrolujte souhrnné informace a pak vyberte **obnovit** a zahajte tak obnovení souboru.

    ![Souhrn obnovení](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Teď v **konzole pro správu MABS** vyberte kartu **monitorování** a zobrazte **stav** obnovení.

    ![Stav obnovení](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Soubor je nyní obnoven. Můžete aktualizovat web služby SharePoint a ověřit obnovený soubor.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-mabs"></a>Obnovení databáze služby SharePoint z Azure pomocí MABS

1. Chcete-li obnovit databázi obsahu služby SharePoint, procházejte různými body obnovení (jak je uvedeno dříve) a vyberte bod obnovení, který chcete obnovit.

    ![MABS SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dvojím kliknutím na bod obnovení služby SharePoint zobrazíte dostupné informace o katalogu služby SharePoint.

   > [!NOTE]
   > Protože je farma služby SharePoint chráněná pro dlouhodobé uchovávání v Azure, na serveru MABS nejsou k dispozici žádné informace o katalogu (metadata). V důsledku toho je potřeba obnovit katalog farmy služby SharePoint, kdykoli bude nutné obnovit databázi obsahu služby SharePoint v čase.
   >
   >
3. Vyberte **znovu katalog**.

    ![MABS SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Otevře se okno stav **opětovného zařazení do katalogu v cloudu** .

    ![MABS SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Po dokončení katalogu se stav změní na *úspěch*. Vyberte **Zavřít**.

    ![MABS SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Vyberte objekt služby SharePoint zobrazený na kartě **obnovení** MABS a získejte strukturu databáze obsahu. Klikněte na položku pravým tlačítkem a pak vyberte **obnovit**.

    ![MABS SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. V tomto okamžiku postupujte podle kroků pro obnovení výše v tomto článku a obnovte databázi obsahu služby SharePoint z disku.

## <a name="switching-the-front-end-web-server"></a>Přepínání Front-Endho webového serveru

Pokud máte více než jeden front-end webový server a chcete přepnout Server, který MABS používá k ochraně farmy, postupujte podle pokynů:

Následující postup využívá příklad serverové farmy se dvěma front-end webovými servery *Server1* a *Server2*. MABS používá k ochraně farmy *Server1* . Změňte front-end webový server, který MABS používá pro *Server2* , abyste mohli z farmy odebrat *Server1* .

> [!NOTE]
> Pokud je front-end webový server, který MABS používá k ochraně farmy, nedostupný, pomocí následujícího postupu změňte front-end webový server tak, že začnete v kroku 4.

### <a name="to-change-the-front-end-web-server-that-mabs-uses-to-protect-the-farm"></a>Změna předřazeného webového serveru, který MABS používá k ochraně farmy

1. Zastavte službu zapisovač VSS SharePointu na *Server1* spuštěním následujícího příkazu na příkazovém řádku:

    ```CMD
    stsadm -o unregisterwsswriter
    ```

1. V *Server1* otevřete Editor registru a přejděte na následující klíč:

   **HKLM\System\CCS\Services\VSS\VssAccessControl**

1. Zkontroluje všechny hodnoty uvedené v podklíči VssAccessControl. Pokud má nějaká položka data s hodnotou 0 a v rámci přidružených přihlašovacích údajů k účtu je spuštěný jiný zapisovač VSS, změňte údaj hodnoty na 1.

1. Nainstalujte agenta ochrany na *Server2*.

   > [!WARNING]
   > Webové servery front-end můžete přepínat pouze v případě, že jsou oba servery ve stejné doméně.

1. Na počítači *Server2* v příkazovém řádku změňte adresář na `_MABS installation location_\bin\` a spusťte **ConfigureSharePoint**. Další informace o ConfigureSharePoint najdete v tématu [Konfigurace zálohování](#configure-backup).

1. Vyberte skupinu ochrany, do které patří serverová farma, a pak vyberte **Upravit skupinu ochrany**.

1. V průvodci úpravou skupiny rozbalte na stránce **Vybrat členy skupiny** položku *Server2* a vyberte serverovou farmu a potom průvodce dokončete.

   Spustí se kontrola konzistence.

1. Pokud jste provedli krok 6, můžete nyní odebrat svazek ze skupiny ochrany.

## <a name="next-steps"></a>Další kroky

Viz článek [zálohovaný Exchange Server](backup-azure-exchange-mabs.md) .
Viz článek o [zálohování SQL Server](backup-azure-sql-mabs.md) .
