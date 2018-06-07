---
title: Aplikace DPM nebo Azure Backup server ochrany farmy služby SharePoint do Azure
description: Tento článek obsahuje přehled ochrany aplikace DPM nebo Azure Backup server farmy služby SharePoint do Azure
services: backup
author: adigan
manager: Nkolli1
ms.service: backup
ms.topic: conceptual
ms.date: 09/29/2016
ms.author: adigan
ms.openlocfilehash: 728850fe70fb3f9e64b0fa25b4ceebb1a1b51cd4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606649"
---
# <a name="back-up-a-sharepoint-farm-to-azure"></a>Zálohování sharepointové farmy do Azure
Můžete zálohovat farmy služby SharePoint do Microsoft Azure pomocí System Center Data Protection Manager (DPM) v mnohem stejným způsobem, který zálohujete jiných zdrojů dat. Azure Backup poskytuje flexibilitu při plán zálohování k vytvoření denní, týdenní, měsíční nebo roční zálohu odkazuje a poskytuje možnosti zásad uchovávání informací pro různé body zálohy. Aplikace DPM poskytuje možnost pro uložení kopie místního disku pro rychlé cíle doba obnovení (RTO) a slouží k uložení kopie do Azure pro ekonomické, dlouhodobé uchovávání.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Podporované verze služby SharePoint a související scénáře ochrany
Zálohování Azure pro DPM podporuje následující scénáře:

| Úloha | Verze | Nasazení služby SharePoint | Typ nasazení aplikace DPM | DPM – System Center 2012 R2 | Ochrana a obnovení |
| --- | --- | --- | --- | --- | --- |
| SharePoint |SharePoint 2013, SharePoint 2010 a SharePoint 2007 SharePoint 3.0 |SharePoint nasazená jako fyzický server nebo virtuální počítač technologie Hyper-V nebo VMware <br> -------------- <br> Technologie AlwaysOn serveru SQL |Fyzický server nebo místní počítač virtuální technologie Hyper-V |Podporuje zálohování Azure od kumulativní aktualizace 5 |Ochrana farmy služby SharePoint možnosti obnovení: farma obnovení, databáze a soubor nebo položka seznamu z bodů obnovení disku.  Obnovení z bodů obnovení Azure farmy a databáze. |

## <a name="before-you-start"></a>Než začnete
Existuje několik věcí, které je potřeba potvrdit před Zálohování farmy služby SharePoint do Azure.

### <a name="prerequisites"></a>Požadavky
Než budete pokračovat, ujistěte se, že jste splnili veškeré [požadavků na používání Microsoft Azure Backup](backup-azure-dpm-introduction.md#prerequisites) chránit úlohy. Některé úlohy pro požadavky patří: vytvořte úložiště záloh, stáhnout přihlašovací údaje trezoru, nainstalovat Azure Backup Agent a Server DPM nebo Azure Backup zaregistrovat u trezoru.

### <a name="dpm-agent"></a>Agent aplikace DPM
Agent aplikace DPM musí být nainstalován na serveru, na kterém běží SharePoint, serverech se systémem SQL Server a všechny ostatní servery, které jsou součástí farmy služby SharePoint. Další informace o tom, jak nastavit agenta ochrany najdete v tématu [instalace agenta ochrany](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  Jedinou výjimkou je, že instalujete agenta pouze na jednom webovém serveru front-end (WFE). Aplikace DPM vyžaduje agenta na jednom serveru WFE pouze sloužit jako vstupní bod pro ochranu.

### <a name="sharepoint-farm"></a>Farmy služby SharePoint
Pro každých 10 milionů položek ve farmě musí být alespoň 2 GB místa na svazku, kde je umístěna složka aplikace DPM. Tento prostor je nezbytné pro generování katalogu. Aplikace DPM k obnovení konkrétní položky (kolekce webů, weby, seznamy, knihovny dokumentů, složky, jednotlivé dokumenty a položky seznamu) generování katalogu vytvoří seznam adres URL, které jsou obsaženy v jednotlivých databázích obsahu. Seznam adres URL můžete zobrazit v podokně obnovitelných položek **obnovení** úloh konzoly správce aplikace DPM.

### <a name="sql-server"></a>SQL Server
Aplikace DPM běží pod účtem LocalSystem. Aplikace DPM k zálohování databází systému SQL Server, vyžaduje oprávnění správce systému na tento účet pro server, který se systémem SQL Server. Nastavte NT AUTHORITY\SYSTEM na *sysadmin* na serveru, který je spuštěn SQL Server předtím, než ho zálohovat.

Pokud farmy služby SharePoint databáze systému SQL Server, které jsou nakonfigurovány s aliasy systému SQL Server, nainstalujte komponenty klienta systému SQL Server na front-end webovém serveru, který bude chránit aplikace DPM.

### <a name="sharepoint-server"></a>SharePoint Server
Při výkonu závislá na mnoha faktorech, jako je například velikost farmy služby SharePoint, jako obecné pokyny jeden server DPM dokáže chránit 25 TB farmy služby SharePoint.

### <a name="dpm-update-rollup-5"></a>DPM s kumulativní aktualizací 5
Chcete-li zahájit ochranu farmy služby SharePoint do Azure, musíte nainstalovat DPM s kumulativní aktualizací 5 nebo novější. Kumulativní aktualizace 5 umožňuje chránit farmu služby SharePoint do Azure, pokud farmy se konfiguruje pomocí technologie AlwaysOn serveru SQL.
Další informace najdete v blogovém příspěvku, který představuje [DPM kumulativní aktualizace 5](http://blogs.technet.com/b/dpm/archive/2015/02/11/update-rollup-5-for-system-center-2012-r2-data-protection-manager-is-now-available.aspx)

### <a name="whats-not-supported"></a>Co není podporováno
* Aplikace DPM, který chrání farmy služby SharePoint nechrání indexy hledání nebo databáze aplikace služby. Musíte konfigurovat ochranu pro tyto databáze samostatně.
* Aplikace DPM neposkytuje zálohování databází serveru SQL služby SharePoint, které jsou hostované na sdílených složkách škálovatelného souborového serveru (SOFS).

## <a name="configure-sharepoint-protection"></a>Konfigurace ochrany Sharepointu
Před použitím aplikace DPM k ochraně služby SharePoint, musíte nakonfigurovat službu SharePoint VSS Writer (WSS Writer service) pomocí **ConfigureSharePoint.exe**.

Můžete najít **ConfigureSharePoint.exe** ve složce [instalační cestě aplikace DPM] \bin na front-end webovém serveru. Tento nástroj poskytuje agenta ochrany s přihlašovacími údaji pro farmu služby SharePoint. Spustíte ji na jeden server WFE. Pokud máte více serverů WFE, vyberte pouze jeden, když konfigurujete skupinu ochrany.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Konfigurace služby SharePoint VSS Writer
1. Na serveru WFE, na příkazovém řádku přejděte do \bin\ [umístění instalace aplikace DPM]
2. Zadejte ConfigureSharePoint - EnableSharePointProtection.
3. Zadejte přihlašovací údaje správce farmy. Tento účet by měl být členem místní skupiny správců na serveru WFE. Pokud není správcem farmy místní správce, udělte následující oprávnění na serveru WFE:
   * Udělte skupině WSS_Admin_WPG úplnou kontrolu ke složce aplikace DPM (% Program Files%\Microsoft Data Protection Manager\DPM).
   * Udělte oprávnění ke čtení skupině WSS_Admin_WPG ke klíči registru aplikace DPM (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Budete muset znovu spustit ConfigureSharePoint.exe vždy, když dojde ke změně v pověřeních správce farmy.
> 
> 

## <a name="back-up-a-sharepoint-farm-by-using-dpm"></a>Zálohování farmy služby SharePoint pomocí aplikace DPM
Po dokončení konfigurace aplikace DPM a farmy služby SharePoint, jak je popsáno dříve služby SharePoint můžete chránit pomocí DPM.

### <a name="to-protect-a-sharepoint-farm"></a>Chcete-li chránit farmu služby SharePoint
1. Z **ochrany** kartě konzole správce aplikace DPM klikněte na tlačítko **nový**.
    ![Nové karty ochrana](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Na **vybrat typ skupiny ochrany** stránky **vytvořením nové skupiny ochrany** průvodce, vyberte **servery**a potom klikněte na **Další**.
   
    ![Typ skupiny ochrany vyberte](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Na **vybrat členy skupiny** obrazovky, zaškrtněte políčko pro server SharePoint, které chcete chránit a klikněte na tlačítko **Další**.
   
    ![Vybrat členy skupiny](./media/backup-azure-backup-sharepoint/select-group-members2.png)
   
   > [!NOTE]
   > Instalaci agenta aplikace DPM můžete zobrazit serveru v průvodci. Aplikace DPM zobrazuje také jeho strukturu. Protože jste spustili ConfigureSharePoint.exe, aplikace DPM komunikuje se službou SharePoint VSS Writer service a její odpovídající databáze systému SQL Server a rozpozná strukturu farmy služby SharePoint, přidružených databázích obsahu a všechny odpovídající položky.
   > 
   > 
4. Na **vyberte způsob ochrany dat** stránky, zadejte název **skupiny ochrany**a vyberte upřednostňovanou *metody ochrany*. Klikněte na **Další**.
   
    ![Vyberte způsob ochrany dat](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)
   
   > [!NOTE]
   > Způsob ochrany disku pomáhá cíle krátkou dobu obnovení. Azure je cíl ekonomické, dlouhodobé ochrany ve srovnání s pásky. Další informace najdete v tématu [pomocí Azure Backup k nahrazení infrastruktury pásky](https://azure.microsoft.com/documentation/articles/backup-azure-backup-cloud-as-tape/)
   > 
   > 
5. Na **zadat krátkodobé cíle** vyberte upřednostňovanou **rozsah uchování** a zjistíte, kdy chcete vytváření záloh každý.
   
    ![Určení krátkodobých cílů](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)
   
   > [!NOTE]
   > Protože obnovení je nejčastěji požadované pro data, která je menší než pět dní, jsme vybrali rozsahem uchování 5 dní na disku a zajistit, že zálohování probíhá při mimo provozní hodiny, v tomto příkladu.
   > 
   > 
6. Zkontrolujte místo přidělené pro skupinu ochrany na disku fondu úložiště a potom na tlačítko **Další**.
7. Pro každou skupinu ochrany aplikace DPM přiděluje místo na disku k uložení a správě repliky. V tomto okamžiku DPM musí vytvořit kopii vybraná data. Vyberte, jak a kdy chcete repliku vytvořit a pak klikněte na tlačítko **Další**.
   
    ![Vyberte způsob vytvoření repliky](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)
   
   > [!NOTE]
   > Abyste měli jistotu, že není uskutečněn síťový provoz, vyberte dobu mimo provozní hodiny.
   > 
   > 
8. DPM zajišťuje integritu dat provedením kontroly konzistence na replice. Existují dvě možnosti k dispozici. Můžete definovat plán, který chcete spustit kontrolu konzistence, nebo aplikace DPM můžete spustit kontrolu konzistence na replice automaticky vždy, když se stane nekonzistentní. Vyberte požadovanou možnost a pak klikněte na tlačítko **Další**.
   
    ![Kontrola konzistence](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Na **zadat Data Online ochrany** vyberte farmy služby SharePoint, který chcete chránit a pak klikněte na tlačítko **Další**.
   
    ![Aplikace DPM Protection1 služby SharePoint](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Na **zadejte plán Online zálohování** vyberte upřednostňovaný plán a pak klikněte na tlačítko **Další**.
    
    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)
    
    > [!NOTE]
    > DPM poskytuje delší než dva denní zálohy do Azure v různých časech. Zálohování Azure můžete také ovládat velikost šířky pásma sítě WAN, který lze použít pro zálohování ve špičce a špičku pomocí [omezení sítě Azure Backup](https://azure.microsoft.com/en-in/documentation/articles/backup-configure-vault/#enable-network-throttling).
    > 
    > 
11. V závislosti na plán zálohování, který jste vybrali, na **zadejte zásady uchovávání Online** vyberte zásady uchovávání informací pro denní, týdenní, měsíční a roční body zálohy.
    
    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)
    
    > [!NOTE]
    > Aplikace DPM používá schéma uchovávání historických. otec SYN ve které je možné vybrat rozdílné zásady pro různé body zálohy.
    > 
    > 
12. Podobně jako u disku, repliku bodu počáteční odkaz musí být vytvořen v Azure. Vyberte upřednostňovanou možnost vytvořit kopii prvotní zálohy do Azure, a potom klikněte na **Další**.
    
    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Zkontrolujte vybrané nastavení na **Souhrn** a pak klikněte na tlačítko **vytvořit skupinu**. Zobrazí se zpráva o úspěšném provedení a po vytvoření skupiny ochrany.
    
    ![Souhrn](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-dpm"></a>Obnovení položky služby SharePoint z disku pomocí aplikace DPM
V následujícím příkladu *položky obnovení služby SharePoint* omylem odstraněný a je potřeba obnovit.
![Aplikace DPM Protection4 služby SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Otevřete **konzole správce aplikace DPM**. V jsou uvedeny všechny farmy služby SharePoint, které jsou chráněné službou DPM **ochrany** kartě.
   
    ![Aplikace DPM Protection3 služby SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Chcete-li začít obnovili položku, vyberte **obnovení** kartě.
   
    ![Aplikace DPM Protection5 služby SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Můžete hledat SharePoint pro *položky obnovení služby SharePoint* pomocí vyhledávání na základě zástupný znak v rámci obnovení bodu rozsahu.
   
    ![Aplikace DPM Protection6 služby SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Vyberte bod obnovení odpovídající ve výsledcích hledání, klikněte pravým tlačítkem položku a pak vyberte **obnovit**.
5. Také můžete procházet různé body obnovení a vyberte databázi nebo položku, kterou chcete obnovit. Vyberte **datum > čas obnovení**a potom vyberte správný **databáze > farmy služby SharePoint > bod obnovení > položky**.
   
    ![Aplikace DPM Protection7 služby SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Klikněte pravým tlačítkem položku a pak vyberte **obnovit** otevřete **Průvodce obnovením**. Klikněte na **Další**.
   
    ![Revidovat výběr obnovení](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Vyberte typ obnovení, který chcete provést a potom klikněte na **Další**.
   
    ![Typ obnovení](./media/backup-azure-backup-sharepoint/select-recovery-type.png)
   
   > [!NOTE]
   > Výběr **obnovit na původní** v příkladu obnoví položka k původní web služby SharePoint.
   > 
   > 
8. Vyberte **proces obnovení** , kterou chcete použít.
   
   * Vyberte **obnovit bez použití farmy obnovení** Pokud farmy služby SharePoint se nezměnila a je stejná jako bod obnovení, který se obnovuje.
   * Vyberte **obnovit použití farmy obnovení** Pokud od vytvoření bodu obnovení změnila farmy služby SharePoint.
     
     ![Proces obnovení](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Zadejte pracovní umístění instance SQL serveru k obnovení databáze dočasně a zadejte pracovní sdílené složky na serveru DPM a serveru se systémem SharePoint o obnovení položky.
   
    ![Pracovní Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)
   
    Aplikace DPM připojí databázi obsahu, který je hostitelem položky služby SharePoint dočasné instanci systému SQL Server. Z databáze obsahu server aplikace DPM obnoví položku a vloží ho na pracovní umístění souboru na serveru DPM. Položku obnovení, který je teď na pracovní umístění serveru DPM musí být exportovány do pracovní umístění na farmě služby SharePoint.
   
    ![Pracovní Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Vyberte **nastavte možnosti obnovení**a použít nastavení zabezpečení pro farmu služby SharePoint nebo použít nastavení zabezpečení bodu obnovení. Klikněte na **Další**.
    
    ![Možnosti obnovení](./media/backup-azure-backup-sharepoint/recovery-options.png)
    
    > [!NOTE]
    > Můžete k omezení využití šířky pásma sítě. Tím se minimalizují dopad na provozním serveru během pracovní doby.
    > 
    > 
11. Zkontrolujte souhrnné informace a pak klikněte na **obnovit** zahájíte obnovení souboru.
    
    ![Obnovení souhrn](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Nyní vybrat **monitorování** ve **konzole správce aplikace DPM** zobrazíte **stav** obnovení.
    
    ![Stav obnovení](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)
    
    > [!NOTE]
    > Soubor je nyní obnovit. Můžete obnovit web služby SharePoint k obnovené najdete v souboru.
    > 
    > 

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Obnovení databáze služby SharePoint z Azure pomocí aplikace DPM
1. Pokud chcete obnovit databázi obsahu služby SharePoint, procházet různé body obnovení (jak je uvedeno výše) a vyberte bod obnovení, který chcete obnovit.
   
    ![Aplikace DPM Protection8 služby SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dvakrát klikněte na bod obnovení služby SharePoint zobrazíte dostupné informace o katalogu služby SharePoint.
   
   > [!NOTE]
   > Protože pro dlouhodobé uchovávání v Azure je chráněn farmy služby SharePoint, nejsou dostupné na serveru DPM žádné informace katalogu (metadata). V důsledku toho vždy, když databáze obsahu služby SharePoint v daném okamžiku je možné obnovit, budete muset znovu katalogu farmy služby SharePoint.
   > 
   > 
3. Klikněte na tlačítko **opětovného zařazení do katalogu**.
   
    ![Aplikace DPM Protection10 služby SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)
   
    **Provést novou katalogizaci cloudu** otevře se okno stav.
   
    ![Aplikace DPM Protection11 služby SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)
   
    Po dokončení do katalogu se stav změní na *úspěch*. Klikněte na **Zavřít**.
   
    ![Aplikace DPM Protection12 služby SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Klikněte na objekt SharePoint uvedené v DPM **obnovení** karty lze získat strukturu databázi obsahu. Klikněte pravým tlačítkem položku a pak klikněte na **obnovit**.
   
    ![Aplikace DPM Protection13 služby SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Postupujte v tomto okamžiku [kroky obnovení dříve v tomto článku](#restore-a-sharepoint-item-from-disk-using-dpm) k obnovení databázi obsahu služby SharePoint z disku.

## <a name="faqs"></a>Nejčastější dotazy
Otázka: jaké verze aplikace DPM podporuje SQL Server 2014 a SQL 2012 (SP2)?<br>
Odpověď: Aplikace DPM 2012 R2 s kumulativní aktualizací 4 podporuje obě.

Otázka: je možné obnovit položky služby SharePoint do původního umístění, pokud je služba SharePoint nakonfigurována pomocí technologie AlwaysOn serveru SQL (s ochrany na disku)?<br>
Odpověď: Ano, položka je možné obnovit do původního webu služby SharePoint.

Otázka: je možné obnovit databázi služby SharePoint do původního umístění, pokud je služba SharePoint nakonfigurována pomocí technologie AlwaysOn serveru SQL?<br>
Odpověď: protože databáze služby SharePoint jsou konfigurované v SQL AlwaysOn, nemůže být upraven Pokud skupina dostupnosti je odebrána. Aplikace DPM v důsledku toho nelze obnovit databázi do původního umístění. Můžete obnovit databázi systému SQL Server do jiné instance systému SQL Server.

## <a name="next-steps"></a>Další postup
* Další informace o DPM ochrany služby SharePoint – viz [řady Video - DPM ochrany služby SharePoint](http://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group)
* Zkontrolujte [poznámky k verzi pro System Center 2012 – Data Protection Manager](https://technet.microsoft.com/library/jj860415.aspx)
* Zkontrolujte [poznámky k verzi pro aplikaci Data Protection Manager v System Center 2012 SP1](https://technet.microsoft.com/library/jj860394.aspx)

