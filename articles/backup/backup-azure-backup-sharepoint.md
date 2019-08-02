---
title: Ochrana DPM/Azure Backup serveru SharePointové farmy do Azure
description: Tento článek poskytuje přehled ochrany DPM/Azure Backup serveru SharePointové farmy do Azure.
ms.reviewer: kasinh
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: dacurwin
ms.openlocfilehash: f17cc71e0666ccf08d90fcef8f18552c09761cbc
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689411"
---
# <a name="back-up-a-sharepoint-farm-to-azure"></a>Zálohování sharepointové farmy do Azure
Můžete zálohovat farmu služby SharePoint a Microsoft Azure pomocí nástroje System Center Data Protection Manager (DPM), a to téměř stejným způsobem jako v případě zálohování jiných zdrojů dat. Azure Backup poskytuje flexibilitu v plánu zálohování k vytvoření denních, týdenních, měsíčních nebo ročních záložních bodů a poskytuje možnosti zásad uchovávání pro různé body zálohování. DPM poskytuje možnost ukládat kopie místních disků pro cíle rychlého obnovení (RTO) a ukládat kopie do Azure pro účely ekonomického a dlouhodobého uchovávání.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Podporované verze SharePointu a související scénáře ochrany
Azure Backup pro DPM podporuje následující scénáře:

| Úloha | Version | Nasazení služby SharePoint | Typ nasazení DPM | DPM – System Center 2012 R2 | Ochrana a obnovení |
| --- | --- | --- | --- | --- | --- |
| SharePoint |SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3,0 |SharePoint nasazený jako fyzický server nebo virtuální počítač s technologií Hyper-V nebo VMware <br> -------------- <br> SQL AlwaysOn |Fyzický server nebo místní virtuální počítač Hyper-V |Podporuje zálohování do Azure z kumulativní aktualizace 5 |Chránit možnosti obnovení farmy služby SharePoint: Z bodů obnovení z disku se nachází farma, databáze a soubor nebo položka seznamu pro obnovení.  Farma a obnovení databáze z bodů obnovení Azure. |

## <a name="before-you-start"></a>Než začnete
Před zálohováním farmy služby SharePoint do Azure je třeba potvrdit několik věcí.

### <a name="prerequisites"></a>Požadavky
Než budete pokračovat, ujistěte se, že jste splnili všechny [předpoklady pro použití Microsoft Azure Backup](backup-azure-dpm-introduction.md#prerequisites-and-limitations) k ochraně úloh. Mezi úlohy týkající se požadavků patří: vytvoření trezoru záloh, stažení přihlašovacích údajů trezoru, instalace agenta Azure Backup a registrace DPM/Azure Backup Server s trezorem.

### <a name="dpm-agent"></a>Agent DPM
Agent aplikace DPM musí být nainstalován na serveru, na kterém je spuštěna služba SharePoint, na serverech se systémem SQL Server a na všech ostatních serverech, které jsou součástí farmy služby SharePoint. Další informace o tom, jak nastavit agenta ochrany, najdete v tématu [instalace agenta ochrany](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  Jedinou výjimkou je, že agenta nainstalujete jenom na jeden server s webovým front-end (WFE). DPM potřebuje agenta na jednom serveru WFE, aby sloužil jako vstupní bod pro ochranu.

### <a name="sharepoint-farm"></a>Farmy služby SharePoint
Pro každý 10 000 000 položek ve farmě musí být na svazku, kde se nachází složka DPM, aspoň 2 GB místa. Toto místo se vyžaduje pro generování katalogu. Aby aplikace DPM obnovila konkrétní položky (kolekce webů, weby, seznamy, knihovny dokumentů, složky, jednotlivé dokumenty a položky seznamu), generování katalogu vytvoří seznam adres URL, které jsou obsaženy v jednotlivých databázích obsahu. Seznam adres URL můžete zobrazit v podokně obnovitelné položky v oblasti úloh **obnovení** konzola správce aplikace DPM.

### <a name="sql-server"></a>SQL Server
DPM běží jako účet LocalSystem. Pro zálohování SQL Server databází potřebuje DPM na tomto účtu oprávnění správce systému pro server, na kterém běží SQL Server. Před zálohováním nastavte NT AUTHORITY\SYSTEM na *sysadmin* na serveru, na kterém běží SQL Server.

Pokud má farma služby SharePoint SQL Server databáze nakonfigurované s aliasy SQL Server, nainstalujte SQL Server komponenty klienta na front-end webový server, který bude chránit aplikace DPM.

### <a name="sharepoint-server"></a>SharePoint Server
I když výkon závisí na mnoha faktorech, jako je například velikost farmy služby SharePoint, jako obecné pokyny jeden server DPM může chránit 25 TB farmy služby SharePoint.

### <a name="dpm-update-rollup-5"></a>Kumulativní aktualizace DPM 5
Chcete-li začít chránit farmu služby SharePoint do Azure, je nutné nainstalovat kumulativní aktualizaci 5 nebo novější aplikace DPM. Kumulativní aktualizace 5 nabízí možnost chránit farmu služby SharePoint na Azure, pokud je farma nakonfigurovaná pomocí technologie SQL AlwaysOn.
Další informace najdete v blogovém příspěvku, který zavádí [kumulativní aktualizaci 5 pro DPM](https://blogs.technet.com/b/dpm/archive/2015/02/11/update-rollup-5-for-system-center-2012-r2-data-protection-manager-is-now-available.aspx) .

### <a name="whats-not-supported"></a>Co není podporováno
* DPM, který chrání farmu služby SharePoint, nechrání vyhledávací indexy ani databáze služby Application Service. Ochranu těchto databází bude nutné nakonfigurovat samostatně.
* DPM neposkytuje zálohu SQL Server databází SharePointu, které jsou hostovány na sdílených složkách souborového serveru se škálováním na více systému (SOFS).

## <a name="configure-sharepoint-protection"></a>Konfigurace ochrany SharePointu
Než budete moct použít DPM k ochraně SharePointu, musíte nakonfigurovat službu SharePoint VSS Writer (WSS Writer Service) pomocí **ConfigureSharePoint. exe**.

**ConfigureSharePoint. exe** najdete ve složce \bin [Instalační cesta aplikace DPM] na front-end webovém serveru. Tento nástroj poskytuje agentovi ochrany pověření pro farmu služby SharePoint. Spouštíte ji na jednom serveru WFE. Pokud máte více serverů WFE, při konfiguraci skupiny ochrany vyberte jenom jednu.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Konfigurace služby SharePoint VSS Writer
1. Na serveru WFE klikněte na příkazovém řádku na [umístění instalace aplikace DPM] \Bin\
2. Zadejte ConfigureSharePoint-EnableSharePointProtection.
3. Zadejte přihlašovací údaje správce farmy. Tento účet by měl být členem místní skupiny správců na serveru WFE. Pokud správce farmy není místní správce, udělte na serveru WFE tato oprávnění:
   * Udělte skupině WSS_Admin_WPG úplné řízení ke složce aplikace DPM (% Program Files%\Microsoft data Protection Manager\DPM).
   * Udělte skupině WSS_Admin_WPG přístup pro čtení ke klíči registru DPM (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Pokud dojde ke změně přihlašovacích údajů správce farmy služby SharePoint, budete muset znovu spustit ConfigureSharePoint. exe.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-dpm"></a>Zálohování farmy služby SharePoint pomocí aplikace DPM
Jakmile nakonfigurujete aplikaci DPM a farmu služby SharePoint, jak je vysvětleno dříve, SharePoint může být chráněn aplikací DPM.

### <a name="to-protect-a-sharepoint-farm"></a>Ochrana farmy služby SharePoint
1. Na kartě **ochrana** v konzola správce aplikace DPM klikněte na **Nový**.
    ![Nová karta ochrana](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Na stránce **Vybrat typ skupiny ochrany** v průvodci **vytvořením nové skupiny ochrany** vyberte **servery**a potom klikněte na **Další**.

    ![Vybrat typ skupiny ochrany](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Na obrazovce **Vybrat členy skupiny** zaškrtněte políčko pro server SharePoint, který chcete chránit, a klikněte na **Další**.

    ![Vybrat členy skupiny](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > S nainstalovaným agentem DPM můžete v průvodci zobrazit server. DPM také zobrazuje jeho strukturu. Vzhledem k tomu, že jste spustili ConfigureSharePoint. exe, aplikace DPM komunikuje se službou zapisovač VSS SharePoint a odpovídajícími databázemi SQL Server a rozpoznává strukturu farmy služby SharePoint, přidružených databází obsahu a všech odpovídajících položek.
   >
   >
4. Na stránce **Vyberte způsob ochrany dat** zadejte název **skupiny ochrany**a vyberte preferované *metody ochrany*. Klikněte na **Další**.

    ![Vyberte způsob ochrany dat](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > Metoda ochrany disku pomáhá plnit krátké cíle při obnovení. Azure je ekonomicky krátkodobý cíl ochrany v porovnání s páskami. Další informace najdete v tématu [použití Azure Backup k nahrazení páskové infrastruktury](https://azure.microsoft.com/documentation/articles/backup-azure-backup-cloud-as-tape/) .
   >
   >
5. Na stránce **zadat krátkodobé cíle** vyberte preferovaný **Rozsah uchování** a určete, kdy se mají zálohy provádět.

    ![Zadat krátkodobé cíle](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Vzhledem k tomu, že se obnovení nejčastěji vyžaduje pro data, která jsou starší než pět dnů, jsme vybrali dobu uchovávání pět dní na disku a zajistili, že záloha proběhne během neprodukčních hodin, a to v tomto příkladu.
   >
   >
6. Zkontrolujte místo na disku fondu úložiště přidělené skupině ochrany a klikněte na tlačítko **Další**.
7. Pro každou skupinu ochrany aplikace DPM přiděluje místo na disku pro ukládání a správu replik. V tomto okamžiku musí aplikace DPM vytvořit kopii vybraných dat. Vyberte, jak a kdy má být replika vytvořena, a poté klikněte na tlačítko **Další**.

    ![Výběr metody vytvoření repliky](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Pokud se chcete ujistit, že síťový provoz není ovlivněný, vyberte čas mimo provozní hodiny.
   >
   >
8. DPM zajišťuje integritu dat prováděním kontrol konzistence repliky. K dispozici jsou dvě možnosti. Můžete definovat plán pro spuštění kontroly konzistence, nebo DPM může spustit kontroly konzistence automaticky v replice, kdykoli bude nekonzistentní. Vyberte upřednostňovanou možnost a potom klikněte na tlačítko **Další**.

    ![Kontrola konzistence](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Na stránce **zadat data online ochrany** vyberte farmu služby SharePoint, kterou chcete chránit, a poté klikněte na tlačítko **Další**.

    ![DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Na stránce **zadat plán online zálohování** vyberte preferovaný plán a potom klikněte na **Další**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > DPM v různých časech poskytuje maximálně dva denní zálohy do Azure. Azure Backup může také řídit velikost šířky pásma sítě WAN, kterou lze použít pro zálohování v špičkách a mimo špičku pomocí [Azure Backup omezování sítě](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling).
    >
    >
11. V závislosti na plánu zálohování, který jste vybrali, vyberte na stránce **zadat zásady uchovávání online** zásady uchovávání informací pro denní, týdenní, měsíční a roční body zálohování.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > DPM používá schéma uchovávání dědečka-otců-syn, ve kterém je možné zvolit jiné zásady uchovávání informací pro různé body zálohování.
    >
    >
12. Podobně jako disk musí být v Azure vytvořená počáteční replika referenčního bodu. Vyberte upřednostňovanou možnost pro vytvoření prvotní záložní kopie do Azure a pak klikněte na **Další**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Zkontrolujte vybraná nastavení na stránce **Souhrn** a pak klikněte na **vytvořit skupinu**. Po vytvoření skupiny ochrany se zobrazí zpráva o úspěchu.

    ![Souhrn](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-dpm"></a>Obnovení položky SharePointu z disku pomocí DPM
V následujícím příkladu bylo obnovení *položky SharePointu* omylem odstraněno a je třeba je obnovit.
![DPM SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Otevřete **Konzola správce aplikace DPM**. Všechny farmy služby SharePoint, které jsou chráněny aplikací DPM, jsou zobrazeny na kartě **ochrana** .

    ![DPM SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Chcete-li začít obnovovat položku, vyberte kartu **obnovení** .

    ![DPM SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Můžete vyhledat SharePoint pro *Obnovení položky SharePointu* pomocí vyhledávání založeného na zástupných znacích v rámci rozsahu bodů obnovení.

    ![DPM SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Ve výsledcích hledání vyberte příslušný bod obnovení, klikněte na položku pravým tlačítkem myši a potom vyberte možnost **obnovit**.
5. Můžete také procházet různými body obnovení a vybrat databázi nebo položku, kterou chcete obnovit. Vyberte **datum > čas obnovení**a pak vyberte správnou **> sharepointovou farmu > bod obnovení, > položka**.

    ![DPM SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Klikněte na položku pravým tlačítkem myši a pak výběrem tlačítka **obnovit** otevřete **Průvodce obnovením**. Klikněte na **Další**.

    ![Kontrola výběru obnovení](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Vyberte typ obnovení, který chcete provést, a potom klikněte na tlačítko **Další**.

    ![Typ obnovení](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > Výběr možnosti **obnovit do původní** v příkladu obnoví položku na původní web služby SharePoint.
   >
   >
8. Vyberte **proces obnovení** , který chcete použít.

   * Vyberte možnost **Obnovit bez použití farmy obnovení** , pokud se farma služby SharePoint nezměnila a je stejná jako obnovený bod obnovení.
   * Vyberte možnost **obnovit pomocí farmy obnovení** , pokud se od vytvoření bodu obnovení změnila farma služby SharePoint.

     ![Proces obnovení](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Zadejte pracovní umístění SQL Server instance pro dočasné obnovení databáze a poskytněte pracovní sdílenou složku na serveru DPM a server, na kterém je spuštěna služba SharePoint pro obnovení položky.

    ![Pracovní proces location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    DPM připojí databázi obsahu, která je hostitelem položky SharePoint, do dočasné instance SQL Server. Z databáze obsahu server DPM obnoví položku a umístí ji do umístění pracovního souboru na serveru DPM. Obnovená položka, která je v pracovním umístění serveru DPM, se teď musí exportovat do pracovního umístění na SharePointové farmě.

    ![Pracovní Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Vyberte možnost **zadat možnosti obnovení**a použít nastavení zabezpečení pro farmu služby SharePoint nebo použít nastavení zabezpečení bodu obnovení. Klikněte na **Další**.

    ![Možnosti obnovení](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Můžete zvolit omezení využití šířky pásma sítě. Tím se minimalizuje dopad na provozní server během produkčních hodin.
    >
    >
11. Zkontrolujte souhrnné informace a potom kliknutím na tlačítko **obnovit** zahajte obnovení souboru.

    ![Souhrn obnovení](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Nyní vyberte kartu **monitorování** v **Konzola správce aplikace DPM** k zobrazení **stavu** obnovení.

    ![Stav obnovení](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Soubor je nyní obnoven. Můžete aktualizovat web služby SharePoint a ověřit obnovený soubor.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Obnovení databáze služby SharePoint z Azure pomocí DPM
1. Chcete-li obnovit databázi obsahu služby SharePoint, procházejte různými body obnovení (jak je uvedeno dříve) a vyberte bod obnovení, který chcete obnovit.

    ![DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dvojím kliknutím na bod obnovení služby SharePoint zobrazíte dostupné informace o katalogu služby SharePoint.

   > [!NOTE]
   > Protože je farma služby SharePoint chráněná pro dlouhodobé uchovávání v Azure, na serveru DPM nejsou k dispozici žádné informace o katalogu (metadata). V důsledku toho je potřeba obnovit katalog farmy služby SharePoint, kdykoli bude nutné obnovit databázi obsahu služby SharePoint v čase.
   >
   >
3. Klikněte na **znovu zařadit do katalogu**.

    ![DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Otevře se okno stav **opětovného zařazení do katalogu** v cloudu.

    ![DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Po dokončení katalogu se stav změní na *úspěch*. Klikněte na **Zavřít**.

    ![DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Klikněte na objekt služby SharePoint zobrazený na kartě **obnovení** DPM a získejte strukturu databáze obsahu. Klikněte na položku pravým tlačítkem myši a pak klikněte na tlačítko **obnovit**.

    ![DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. V tomto okamžiku postupujte podle kroků pro obnovení výše v tomto článku a obnovte databázi obsahu služby SharePoint z disku.

## <a name="next-steps"></a>Další kroky
* Další informace o ochraně SharePointu v DPM – viz [video Series – ochrana DPM služby SharePoint](https://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group)
* Přečtěte si [poznámky k verzi pro System Center 2012-Data Protection Manager](https://technet.microsoft.com/library/jj860415.aspx)
* Přečtěte si [poznámky k verzi pro data Protection Manager v produktu System Center 2012 SP1](https://technet.microsoft.com/library/jj860394.aspx)
