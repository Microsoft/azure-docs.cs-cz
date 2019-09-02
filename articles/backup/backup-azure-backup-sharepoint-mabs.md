---
title: Použití serveru Azure Backup k zálohování farmy služby SharePoint do Azure
description: Pomocí Azure Backup Server můžete zálohovat a obnovovat data služby SharePoint. Tento článek poskytuje informace o konfiguraci farmy služby SharePoint, aby bylo možné ukládat požadovaná data do Azure. Chráněná data služby SharePoint můžete obnovit z disku nebo z Azure.
ms.reviewer: kasinh
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: dacurwin
ms.openlocfilehash: bdf0f64bf24f77d54a8fed8714a0cc7c3de814b1
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210470"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-mabs"></a>Zálohování farmy služby SharePoint do Azure pomocí MABS
Můžete zálohovat farmu služby SharePoint a Microsoft Azure pomocí Microsoft Azure Backup serveru (MABS), a to podobně jako při zálohování jiných zdrojů dat. Azure Backup poskytuje flexibilitu v plánu zálohování k vytvoření denních, týdenních, měsíčních nebo ročních záložních bodů a poskytuje možnosti zásad uchovávání pro různé body zálohování. Poskytuje taky možnost ukládat kopie místních disků pro cíle rychlého obnovení (RTO) a ukládat kopie do Azure pro účely ekonomického a dlouhodobého uchovávání.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Podporované verze SharePointu a související scénáře ochrany
Azure Backup pro DPM podporuje následující scénáře:

| Úloha | Version | Nasazení služby SharePoint | Ochrana a obnovení |
| --- | --- | --- | --- |
| SharePoint |SharePoint 2016, SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3,0 |SharePoint nasazený jako fyzický server nebo virtuální počítač s technologií Hyper-V nebo VMware <br> -------------- <br> SQL AlwaysOn | Chránit možnosti obnovení farmy služby SharePoint: Z bodů obnovení z disku se nachází farma, databáze a soubor nebo položka seznamu pro obnovení.  Farma a obnovení databáze z bodů obnovení Azure. |

## <a name="before-you-start"></a>Než začnete
Před zálohováním farmy služby SharePoint do Azure je třeba potvrdit několik věcí.

### <a name="prerequisites"></a>Požadavky
Než budete pokračovat, ujistěte se, že jste [nainstalovali a připravili Azure Backup Server](backup-azure-microsoft-azure-backup.md) pro ochranu úloh.

### <a name="protection-agent"></a>Agent ochrany
Agent Azure Backup musí být nainstalován na serveru, na kterém je spuštěna služba SharePoint, na serverech se systémem SQL Server a na všech ostatních serverech, které jsou součástí farmy služby SharePoint. Další informace o tom, jak nastavit agenta ochrany, najdete v tématu [instalace agenta ochrany](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  Jedinou výjimkou je, že agenta nainstalujete jenom na jeden server s webovým front-end (WFE). Azure Backup Server vyžaduje, aby byl agent na jednom WFE serveru sloužit jenom jako vstupní bod pro ochranu.

### <a name="sharepoint-farm"></a>Farmy služby SharePoint
Pro každý 10 000 000 položek ve farmě musí být na svazku, kde se nachází složka MABS, aspoň 2 GB místa. Toto místo se vyžaduje pro generování katalogu. Aby MABS obnovil konkrétní položky (kolekce webů, weby, seznamy, knihovny dokumentů, složky, jednotlivé dokumenty a položky seznamu), generování katalogu vytvoří seznam adres URL, které jsou obsaženy v jednotlivých databázích obsahu. Seznam adres URL můžete zobrazit v podokně obnovitelné položky v oblasti úlohy obnovení v konzole **pro** správu MABS.

### <a name="sql-server"></a>SQL Server
Azure Backup Server spouští jako účet LocalSystem. Pro zálohování SQL Server databází potřebuje MABS oprávnění správce systému pro tento účet pro server, na kterém běží SQL Server. Před zálohováním nastavte NT AUTHORITY\SYSTEM na *sysadmin* na serveru, na kterém běží SQL Server.

Pokud má farma služby SharePoint SQL Server databáze nakonfigurované s aliasy SQL Server, nainstalujte SQL Server součásti klienta na front-end webovém serveru, který MABS bude chránit.

### <a name="sharepoint-server"></a>SharePoint Server
I když výkon závisí na mnoha faktorech, jako je třeba velikost farmy služby SharePoint, protože obecné pokyny One MABS mohou chránit 25 TB farmy služby SharePoint.

### <a name="whats-not-supported"></a>Co není podporováno
* MABS, který chrání farmu služby SharePoint, nechrání vyhledávací indexy ani databáze služby Application Service. Ochranu těchto databází bude nutné nakonfigurovat samostatně.
* MABS neposkytuje zálohu databází SharePoint SQL Server hostovaných ve sdílených složkách souborového serveru se škálováním na více systému (SOFS).

## <a name="configure-sharepoint-protection"></a>Konfigurace ochrany SharePointu
Než budete moct použít MABS k ochraně SharePointu, musíte nakonfigurovat službu SharePoint VSS Writer (WSS Writer Service) pomocí **ConfigureSharePoint. exe**.

**ConfigureSharePoint. exe** najdete na front-end webovém serveru ve složce [Instalační cesta nástroje MABS]. Tento nástroj poskytuje agentovi ochrany pověření pro farmu služby SharePoint. Spouštíte ji na jednom serveru WFE. Pokud máte více serverů WFE, při konfiguraci skupiny ochrany vyberte jenom jednu.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Konfigurace služby SharePoint VSS Writer
1. Na serveru WFE, na příkazovém řádku, přejít na [umístění instalace MABS] \Bin\
2. Zadejte ConfigureSharePoint-EnableSharePointProtection.
3. Zadejte přihlašovací údaje správce farmy. Tento účet by měl být členem místní skupiny správců na serveru WFE. Pokud správce farmy není místní správce, udělte na serveru WFE tato oprávnění:
   * Udělte skupině WSS_Admin_WPG úplné řízení ke složce aplikace DPM (% Program Files%\Microsoft Azure Backup\DPM).
   * Udělte skupině WSS_Admin_WPG přístup pro čtení ke klíči registru DPM (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Pokud dojde ke změně přihlašovacích údajů správce farmy služby SharePoint, budete muset znovu spustit ConfigureSharePoint. exe.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>Zálohování farmy služby SharePoint pomocí MABS
Až nakonfigurujete MABS a farmu služby SharePoint, jak je vysvětleno dříve, SharePoint může být chráněn pomocí MABS.

### <a name="to-protect-a-sharepoint-farm"></a>Ochrana farmy služby SharePoint
1. Na kartě **ochrana** v konzole pro správu MABS klikněte na **Nový**.
    ![Nová karta ochrana](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Na stránce **Vybrat typ skupiny ochrany** v průvodci **vytvořením nové skupiny ochrany** vyberte **servery**a potom klikněte na **Další**.

    ![Vybrat typ skupiny ochrany](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Na obrazovce **Vybrat členy skupiny** zaškrtněte políčko pro server SharePoint, který chcete chránit, a klikněte na **Další**.

    ![Vybrat členy skupiny](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > S nainstalovaným agentem ochrany můžete v průvodci zobrazit server. MABS také zobrazuje jeho strukturu. Vzhledem k tomu, že jste spustili ConfigureSharePoint. exe, MABS komunikuje se službou zapisovač VSS SharePoint a odpovídajícími databázemi SQL Server a rozpoznává strukturu farmy služby SharePoint, přidružené databáze obsahu a všechny odpovídající položky.
   >
   >
4. Na stránce **Vyberte způsob ochrany dat** zadejte název **skupiny ochrany**a vyberte preferované *metody ochrany*. Klikněte na **Další**.

    ![Vyberte způsob ochrany dat](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > Metoda ochrany disku pomáhá plnit krátké cíle při obnovení.
   >
   >
5. Na stránce **zadat krátkodobé cíle** vyberte preferovaný **Rozsah uchování** a určete, kdy se mají zálohy provádět.

    ![Zadat krátkodobé cíle](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Vzhledem k tomu, že se obnovení nejčastěji vyžaduje pro data, která jsou starší než pět dnů, jsme vybrali dobu uchovávání pět dní na disku a zajistili, že záloha proběhne během neprodukčních hodin, a to v tomto příkladu.
   >
   >
6. Zkontrolujte místo na disku fondu úložiště přidělené skupině ochrany a klikněte na tlačítko **Další**.
7. Pro každou skupinu ochrany MABS přiděluje místo na disku pro ukládání a správu replik. V tomto okamžiku musí MABS vytvořit kopii vybraných dat. Vyberte, jak a kdy má být replika vytvořena, a poté klikněte na tlačítko **Další**.

    ![Výběr metody vytvoření repliky](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Pokud se chcete ujistit, že síťový provoz není ovlivněný, vyberte čas mimo provozní hodiny.
   >
   >
8. MABS zajišťuje integritu dat prováděním kontrol konzistence repliky. K dispozici jsou dvě možnosti. Můžete definovat plán pro spuštění kontroly konzistence, nebo DPM může spustit kontroly konzistence automaticky v replice, kdykoli bude nekonzistentní. Vyberte upřednostňovanou možnost a potom klikněte na tlačítko **Další**.

    ![Kontrola konzistence](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Na stránce **zadat data online ochrany** vyberte farmu služby SharePoint, kterou chcete chránit, a poté klikněte na tlačítko **Další**.

    ![DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Na stránce **zadat plán online zálohování** vyberte preferovaný plán a potom klikněte na **Další**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > MABS poskytuje do Azure maximálně dva denní zálohy z dostupného bodu zálohy na disku. Azure Backup může také řídit velikost šířky pásma sítě WAN, kterou lze použít pro zálohování v špičkách a mimo špičku pomocí [Azure Backup omezování sítě](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling).
    >
    >
11. V závislosti na plánu zálohování, který jste vybrali, vyberte na stránce **zadat zásady uchovávání online** zásady uchovávání informací pro denní, týdenní, měsíční a roční body zálohování.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > MABS používá schéma uchovávání dědečka-otců-syn, ve kterém se dá zvolit jiné zásady uchovávání informací pro různé body zálohování.
    >
    >
12. Podobně jako disk musí být v Azure vytvořená počáteční replika referenčního bodu. Vyberte upřednostňovanou možnost pro vytvoření prvotní záložní kopie do Azure a pak klikněte na **Další**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Zkontrolujte vybraná nastavení na stránce **Souhrn** a pak klikněte na **vytvořit skupinu**. Po vytvoření skupiny ochrany se zobrazí zpráva o úspěchu.

    ![Souhrn](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Obnovení položky SharePointu z disku pomocí MABS
V následujícím příkladu bylo obnovení *položky SharePointu* omylem odstraněno a je třeba je obnovit.
![MABS SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Otevřete **Konzola správce aplikace DPM**. Všechny farmy služby SharePoint, které jsou chráněny aplikací DPM, jsou zobrazeny na kartě **ochrana** .

    ![MABS SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Chcete-li začít obnovovat položku, vyberte kartu **obnovení** .

    ![MABS SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Můžete vyhledat SharePoint pro *Obnovení položky SharePointu* pomocí vyhledávání založeného na zástupných znacích v rámci rozsahu bodů obnovení.

    ![MABS SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Ve výsledcích hledání vyberte příslušný bod obnovení, klikněte na položku pravým tlačítkem myši a potom vyberte možnost **obnovit**.
5. Můžete také procházet různými body obnovení a vybrat databázi nebo položku, kterou chcete obnovit. Vyberte **datum > čas obnovení**a pak vyberte správnou **> sharepointovou farmu > bod obnovení, > položka**.

    ![MABS SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
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
9. Zadejte pracovní umístění SQL Server instance pro dočasné obnovení databáze a poskytněte pracovní sdílenou složku na MABS a server, na kterém je spuštěna služba SharePoint pro obnovení položky.

    ![Pracovní proces location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS připojí databázi obsahu, která je hostitelem položky SharePoint, do dočasné instance SQL Server. Z databáze obsahu obnoví položku a umístí ji do pracovního umístění souboru na MABS. Obnovená položka, která je v pracovním umístění, se teď musí exportovat do pracovního umístění ve farmě služby SharePoint.

    ![Pracovní Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Vyberte možnost **zadat možnosti obnovení**a použít nastavení zabezpečení pro farmu služby SharePoint nebo použít nastavení zabezpečení bodu obnovení. Klikněte na **Další**.

    ![Možnosti obnovení](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Můžete zvolit omezení využití šířky pásma sítě. Tím se minimalizuje dopad na provozní server během produkčních hodin.
    >
    >
11. Zkontrolujte souhrnné informace a potom kliknutím na tlačítko **obnovit** zahajte obnovení souboru.

    ![Souhrn obnovení](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Teď v **konzole pro správu MABS** vyberte kartu **monitorování** a zobrazte **stav** obnovení.

    ![Stav obnovení](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Soubor je nyní obnoven. Můžete aktualizovat web služby SharePoint a ověřit obnovený soubor.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Obnovení databáze služby SharePoint z Azure pomocí DPM
1. Chcete-li obnovit databázi obsahu služby SharePoint, procházejte různými body obnovení (jak je uvedeno dříve) a vyberte bod obnovení, který chcete obnovit.

    ![MABS SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dvojím kliknutím na bod obnovení služby SharePoint zobrazíte dostupné informace o katalogu služby SharePoint.

   > [!NOTE]
   > Vzhledem k tomu, že je farma služby SharePoint chráněná pro dlouhodobé uchovávání v Azure, nejsou v MABS k dispozici žádné informace o katalogu (metadata). V důsledku toho je potřeba obnovit katalog farmy služby SharePoint, kdykoli bude nutné obnovit databázi obsahu služby SharePoint v čase.
   >
   >
3. Klikněte na **znovu zařadit do katalogu**.

    ![MABS SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Otevře se okno stav **opětovného zařazení do katalogu** v cloudu.

    ![MABS SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Po dokončení katalogu se stav změní na *úspěch*. Klikněte na **Zavřít**.

    ![MABS SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Klikněte na objekt služby SharePoint zobrazený na kartě **obnovení** MABS a získejte strukturu databáze obsahu. Klikněte na položku pravým tlačítkem myši a pak klikněte na tlačítko **obnovit**.

    ![MABS SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. V tomto okamžiku postupujte podle kroků pro obnovení výše v tomto článku a obnovte databázi obsahu služby SharePoint z disku.

## <a name="next-steps"></a>Další kroky

Viz článek [zálohovaný Exchange Server](backup-azure-exchange-mabs.md) .
Viz článek o [zálohování SQL Server](backup-azure-sql-mabs.md) .
