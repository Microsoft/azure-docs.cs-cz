---
title: Zálohování sharepointové farmy do Azure pomocí MABS
description: Pomocí Azure Backup Serveru zálohujte a obnovujte data SharePointu. Tento článek obsahuje informace o konfiguraci vaší farmy SharePoint tak, aby požadovaná data mohla být uložena v Azure. Chráněná sharepointová data můžete obnovit z disku nebo z Azure.
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: 441a896f2faa67a1380007ebb9474d7c311a4842
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673138"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-mabs"></a>Zálohování sharepointové farmy do Azure pomocí MABS

SharePointovou farmu zálohujete do Microsoft Azure pomocí Microsoft Azure Backup Server (MABS) v podstatě stejným způsobem jako zálohování jiných zdrojů dat. Azure Backup poskytuje flexibilitu v plánu zálohování k vytváření denních, týdenních, měsíčních nebo ročních bodů zálohování a poskytuje možnosti zásad uchovávání informací pro různé body zálohování. Poskytuje také možnost ukládat kopie místních disků pro rychlé cíle obnovení (RTO) a ukládat kopie do Azure pro ekonomické a dlouhodobé uchovávání.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Podporované verze SharePointu a související scénáře ochrany

Azure Backup for DPM podporuje následující scénáře:

| Úloha | Version | Nasazení SharePointu | Ochrana a obnovení |
| --- | --- | --- | --- |
| SharePoint |SharePoint 2016, SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3.0 |SharePoint nasazený jako fyzický server nebo virtuální počítač Hyper-V/VMware <br> -------------- <br> SQL AlwaysOn | Ochrana možností obnovení farmy SharePoint: Farma pro obnovení, databáze a položka souboru nebo seznamu z bodů obnovení disku.  Obnovení farmy a databáze z bodů obnovení Azure. |

## <a name="before-you-start"></a>Než začnete

Před zálohováním sharepointové farmy do Azure je potřeba potvrdit několik věcí.

### <a name="prerequisites"></a>Požadavky

Než budete pokračovat, ujistěte se, že jste [nainstalovali a připravili Azure Backup Server](backup-azure-microsoft-azure-backup.md) na ochranu úloh.

### <a name="protection-agent"></a>Ochranné činidlo

Agent Azure Backup musí být nainstalovaný na serveru se systémem SharePoint, na serverech se systémem SQL Server a na všech ostatních serverech, které jsou součástí farmy SharePointu. Další informace o nastavení agenta ochrany naleznete v tématu [Setup Protection Agent](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019).  Jedinou výjimkou je, že agenta nainstalujete pouze na jeden server webového front-endu (WFE). Azure Backup Server potřebuje agenta na jednom serveru WFE, aby sloužil pouze jako vstupní bod pro ochranu.

### <a name="sharepoint-farm"></a>Farmy služby SharePoint

Pro každých 10 milionů položek ve farmě musí být alespoň 2 GB místa na svazku, kde je umístěna složka MABS. Toto místo je nezbytné pro generování katalogu. Aby mabs obnovit konkrétní položky (kolekce webů, weby, seznamy, knihovny dokumentů, složky, jednotlivé dokumenty a seznam položek), generování katalogu vytvoří seznam adres URL, které jsou obsaženy v každé databázi obsahu. Seznam adres URL můžete zobrazit v podokně obnovitelné položky v oblasti úloh **obnovení** konzoly Správce MABS.

### <a name="sql-server"></a>SQL Server

Azure Backup Server běží jako účet LocalSystem. Chcete-li zálohovat databáze serveru SQL Server, mabs potřebuje oprávnění sysadmin na tento účet pro server, který je spuštěn SQL Server. Před zálohováním nastavte nt authority\system na *sysadmin* na serveru se spuštěným sql serverem.

Pokud má farma SharePoint databáze serveru SQL Server, které jsou nakonfigurovány pomocí aliasů serveru SQL Server, nainstalujte klientské součásti serveru SQL Server na webový server front-end, který bude mabs chránit.

### <a name="sharepoint-server"></a>SharePoint Server

Zatímco výkon závisí na mnoha faktorech, jako je velikost farmy SharePoint, jako obecné pokyny jeden MABS může chránit 25 TB sharepointové farmy.

### <a name="whats-not-supported"></a>Co není podporováno

* MABS, který chrání farmu služby SharePoint, nechrání indexy vyhledávání ani databáze aplikačních služeb. Ochranu těchto databází budete muset nakonfigurovat samostatně.
* MABS neposkytuje zálohování databází sharepointového serveru SQL Server, které jsou hostovány ve sdílených složek souborového serveru (SOFS).

## <a name="configure-sharepoint-protection"></a>Konfigurace ochrany služby SharePoint

Před použitím MABS k ochraně služby SharePoint je nutné nakonfigurovat službu Proznace Služby Proznace služby Služby WSS pomocí **programu ConfigureSharePoint.exe**.

**Soubor ConfigureSharePoint.exe** najdete ve složce [Instalační cesta MABS]\bin na webovém serveru front-end. Tento nástroj poskytuje agentovi ochrany pověření pro farmu Služby SharePoint. Spustíte jej na jednom serveru WFE. Pokud máte více serverů WFE, vyberte při konfiguraci skupiny ochrany pouze jeden server.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Konfigurace služby SharePoint VSS Writer

1. Na serveru WFE přejděte na příkazovém řádku na [Umístění instalace MABS]\bin\
2. Zadejte ConfigureSharePoint -EnableSharePointProtection.
3. Zadejte přihlašovací údaje správce farmy. Tento účet musí být členem místní skupiny Správci na serveru WFE. Pokud není správcem farmy místní správce, udělte na serveru WFE tato oprávnění:
   * Udělte skupině WSS_Admin_WPG úplné řízení ve složce DPM (%Program Files%\Microsoft Azure Backup\DPM).
   * Udělte skupině WSS_Admin_WPG přístup ke čtení klíči registru DPM (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Budete muset znovu spustit ConfigureSharePoint.exe vždy, když dojde ke změně pověření správce farmy SharePointu.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>Zálohování farmy služby SharePoint pomocí MABS

Po konfiguraci MABS a farmy služby SharePoint, jak bylo vysvětleno dříve, může být SharePoint chráněn mabs.

### <a name="to-protect-a-sharepoint-farm"></a>Ochrana sharepointové farmy

1. Na kartě **Ochrana** konzoly správce MABS klepněte na tlačítko **Nový**.
    ![Karta Nová ochrana](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Na stránce **Vybrat skupinu ochrany v** průvodci Vytvořit novou **skupinu ochrany** vyberte **položku Servery**a klepněte na tlačítko **Další**.

    ![Vybrat typ skupiny ochrany](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Na obrazovce **Vybrat členy skupiny** zaškrtněte políčko u serveru SharePoint, který chcete chránit, a klepněte na tlačítko **Další**.

    ![Výběr členů skupiny](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > S nainstalovaným agentem ochrany můžete zobrazit server v průvodci. MABS také ukazuje svou strukturu. Vzhledem k tomu, že jste spustili ConfigureSharePoint.exe, MABS komunikuje se službou SharePoint VSS Writer a jejími odpovídajícími databázemi serveru SQL Server a rozpozná strukturu farmy služby SharePoint, přidružené databáze obsahu a všechny odpovídající položky.
   >
   >
4. Na stránce **Vybrat metodu ochrany dat** zadejte název **skupiny ochrany**a vyberte preferované metody *ochrany*. Klikněte na **Další**.

    ![Výběr způsobu ochrany dat](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > Metoda ochrany disku pomáhá splnit krátké cíle doby obnovení.
   >
   >
5. Na stránce **Zadat krátkodobé cíle** vyberte preferovaný rozsah **uchování** a zjistěte, kdy chcete, aby k zálohování došlo.

    ![Určení krátkodobých cílů](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Vzhledem k tomu, že obnovení je nejčastěji vyžadováno pro data, která jsou mladší než pět dní, vybrali jsme rozsah uchování na disku pět dní a zajistili jsme, že k zálohování dojde během neprodukčních hodin, v tomto příkladu.
   >
   >
6. Zkontrolujte místo na disku fondu úložiště přidělené pro skupinu ochrany a klepněte na tlačítko **Další**.
7. Pro každou skupinu ochrany MABS přiděluje místo na disku pro ukládání a správu replik. V tomto okamžiku musí MABS vytvořit kopii vybraných dat. Vyberte, jak a kdy chcete repliku vytvořit, a klepněte na tlačítko **Další**.

    ![Zvolit metodu vytvoření repliky](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Chcete-li se ujistit, že se síťový provoz neprovádí, vyberte čas mimo výrobní hodiny.
   >
   >
8. MABS zajišťuje integritu dat provedením kontrol konzistence repliky. K dispozici jsou dvě možnosti. Můžete definovat plán pro spuštění kontrol konzistence nebo aplikace DPM může automaticky spouštět kontroly konzistence repliky, kdykoli se stane nekonzistentní. Vyberte upřednostňovanou možnost a klepněte na tlačítko **Další**.

    ![Kontrola konzistence](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Na stránce **Určit data ochrany online** vyberte sharepointovou farmu, kterou chcete chránit, a klepněte na tlačítko **Další**.

    ![Ochrana služby DPM sharepointu1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Na stránce **Zadat plán zálohování online** vyberte upřednostňovaný plán a klepněte na tlačítko **Další**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > MABS poskytuje maximálně dvě denní zálohy do Azure z tehdy dostupného nejnovějšího bodu zálohování disku. Azure Backup můžete také řídit velikost šířky pásma WAN, které lze použít pro zálohování ve špičce a mimo špičku pomocí [omezení sítě Azure Backup](backup-windows-with-mars-agent.md#enable-network-throttling).
    >
    >
11. V závislosti na vybraném plánu zálohování vyberte na stránce **Zadat zásady uchovávání informací online** zásady uchovávání informací zásady uchovávání informací pro denní, týdenní, měsíční a roční body zálohování.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > MABS používá schéma uchovávání dědeček-otec-syn, ve kterém lze zvolit různé zásady uchovávání informací pro různé záložní body.
    >
    >
12. Podobně jako na disku, musí být vytvořena replika počátečního referenčního bodu v Azure. Vyberte upřednostňovanou možnost, chcete-li vytvořit počáteční záložní kopii do Azure, a klepněte na tlačítko **Další**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Zkontrolujte vybraná nastavení na stránce **Souhrn** a potom klepněte na **tlačítko Vytvořit skupinu**. Po vytvoření skupiny ochrany se zobrazí zpráva o úspěchu.

    ![Souhrn](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Obnovení položky služby SharePoint z disku pomocí mabs

V následujícím příkladu byla položka *Obnovení služby SharePoint* omylem odstraněna a je třeba ji obnovit.
![Ochrana služby MABS sharepointové služby4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Otevřete **konzolu správce aplikace DPM**. Všechny farmy služby SharePoint, které jsou chráněny službou DPM, jsou zobrazeny na kartě **Ochrana.**

    ![Ochrana služby MABS sharepointové služby3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Chcete-li začít položku obnovovat, vyberte kartu **Obnovení.**

    ![Ochrana služby MABS pro službu SharePoint5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. SharePoint pro *obnovení položky SharePointu* můžete vyhledat pomocí hledání založeného na zástupných zástupcích v rozsahu bodů obnovení.

    ![Ochrana služby MABS sharepointové služby6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Ve výsledcích hledání vyberte příslušný bod obnovení, klepněte pravým tlačítkem myši na položku a pak vyberte **příkaz Obnovit**.
5. Můžete také procházet různé body obnovení a vybrat databázi nebo položku, kterou chcete obnovit. Vyberte **datum > doba obnovení**a pak vyberte správnou **databázi > položku obnovení > > položku farmy služby SharePoint**.

    ![Ochrana služby MABS pro službu SharePoint7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Klepněte pravým tlačítkem myši na položku a potom klepnutím na příkaz **Obnovit** otevřete **Průvodce obnovením**. Klikněte na **Další**.

    ![Zkontrolovat výběr obnovení](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Vyberte typ obnovení, který chcete provést, a klepněte na tlačítko **Další**.

    ![Typ obnovení](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > Výběr **obnovit na původní** v příkladu obnoví položku na původní sharepointový web.
   >
   >
8. Vyberte **proces obnovení,** který chcete použít.

   * Pokud se farma služby SharePoint nezměnila a je stejná jako obnovovací bod, který se obnovuje, vyberte **možnost Obnovit bez použití farmy pro obnovení.**
   * Pokud se od vytvoření bodu obnovení změnila farma služby SharePoint, vyberte **recover pomocí farmy pro obnovení.**

     ![Proces obnovení](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Zadejte umístění instance pracovního serveru SQL Server pro dočasné obnovení databáze a zadejte pracovní sdílenou složku na MABS a serveru, na kterém je spuštěn sharepoint, aby se položka obnovila.

    ![Pracovní umístění1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS připojí databázi obsahu, která je hostitelem položky služby SharePoint do instance dočasného serveru SQL Server. Z databáze obsahu obnoví položku a umístí ji na umístění pracovnísoubor na MABS. Obnovená položka, která je v pracovním umístění, teď musí být exportována do pracovního umístění na farmě SharePointu.

    ![Pracovní umístění2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Vyberte **Určit možnosti obnovení**a aplikujte nastavení zabezpečení na farmu služby SharePoint nebo použijte nastavení zabezpečení bodu obnovení. Klikněte na **Další**.

    ![Možnosti obnovení](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Můžete se rozhodnout omezit využití šířky pásma sítě. Tím se minimalizuje dopad na produkční server během výrobních hodin.
    >
    >
11. Zkontrolujte souhrnné informace a klepnutím na tlačítko **Obnovit** začněte s obnovou souboru.

    ![Souhrn obnovení](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Nyní vyberte kartu **Monitorování** v **konzole správce MABS** a zobrazte **stav** obnovení.

    ![Stav obnovení](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Soubor je nyní obnoven. Obnovený soubor můžete aktualizovat a zkontrolovat.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Obnovení databáze SharePointu z Azure pomocí aplikace DPM

1. Chcete-li obnovit databázi obsahu služby SharePoint, procházejte různé body obnovení (jak je znázorněno výše) a vyberte bod obnovení, který chcete obnovit.

    ![Ochrana služby MABS pro službu SharePoint8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Poklepáním na bod obnovení SharePointu zobrazte dostupné informace o katalogu SharePointu.

   > [!NOTE]
   > Vzhledem k tomu, že farma SharePoint je chráněná pro dlouhodobé uchovávání v Azure, žádné informace o katalogu (metadata) je k dispozici na MABS. V důsledku toho vždy, když je potřeba obnovit databázi obsahu sharepointového bodu, je třeba znovu katalogizovat farmu služby SharePoint.
   >
   >
3. Klepněte na **tlačítko Znovu katalogizovat**.

    ![Ochrana služby MABS služby SharePoint10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Otevře se okno stavu **Překatalogu cloudu.**

    ![Ochrana služby MABS sharepointové služby11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Po dokončení katalogizování se stav změní na *Úspěch*. Klikněte na **Zavřít**.

    ![Ochrana služby MABS služby SharePoint12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Kliknutím na objekt Služby SharePoint zobrazený na **kartě** Obnovení MABS získáte strukturu databáze obsahu. Klikněte pravým tlačítkem myši na položku a potom klikněte na **obnovit**.

    ![Ochrana služby MABS sharepointové služby13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. V tomto okamžiku postupujte podle kroků obnovení dříve v tomto článku obnovit databázi obsahu služby SharePoint z disku.

## <a name="next-steps"></a>Další kroky

Přečtěte si článek [Zálohovat server Exchange.](backup-azure-exchange-mabs.md)
Viz článek [Zálohovat SQL Server.](backup-azure-sql-mabs.md)
