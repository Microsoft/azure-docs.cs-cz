---
title: Instalace adaptéru StorSimple pro SharePoint | Dokumenty společnosti Microsoft
description: Popisuje, jak nainstalovat a nakonfigurovat nebo odebrat adaptér StorSimple pro SharePoint ve farmě serveru SharePoint.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: twooley
ms.openlocfilehash: a841ce8b664389ccd8fdf55de9965f09412fecf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75930210"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Instalace a konfigurace adaptéru StorSimple pro SharePoint
## <a name="overview"></a>Přehled
StorSimple Adaptér pro SharePoint je součást, která umožňuje poskytovat Microsoft Azure StorSimple flexibilní úložiště a ochranu dat pro farmy serveru SharePoint. Pomocí adaptéru můžete přesunout obsah binárního velkého objektu (BLOB) z databází obsahu serveru SQL Server do hybridního cloudového úložiště Microsoft Azure StorSimple.

StorSimple Adaptér pro SharePoint funguje jako zprostředkovatel vzdáleného úložiště BLOB (RBS) a používá funkci vzdáleného úložiště BLOB serveru SQL Server k ukládání nestrukturovaného obsahu služby SharePoint (ve formě objektů BLOB) na souborový server, který je zálohován zařízením StorSimple.

> [!NOTE]
> StorSimple Adaptér pro SharePoint podporuje sharepointové úložiště BLOB serveru 2010 (RBS). Nepodporuje sharepointový server 2010 externí úložiště BLOB (EBS).


* Chcete-li stáhnout adaptér StorSimple pro službu SharePoint, přejděte na [web StorSimple Adapter for SharePoint][1] v centru pro stahování Microsoft Download Center.
* Informace o plánování omezení RBS a RBS naleznete [v částce Rozhodnutí o použití služby RBS v sharepointu 2013][2] nebo [plán pro účely služby RBS (SharePoint Server 2010).][3]

Zbývající část tohoto přehledu stručně popisuje roli storsimple adaptéru pro SharePoint a limity kapacity a výkonu služby SharePoint, které byste měli znát před instalací a konfigurací adaptéru. Po kontrole těchto informací přejděte na [StorSimple Adapter pro instalaci služby SharePoint](#storsimple-adapter-for-sharepoint-installation) a začněte nastavovat adaptér.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>StorSimple Adaptér pro výhody SharePointu
Na webu služby SharePoint je obsah uložen jako nestrukturovaná data objektů BLOB v jedné nebo více databázích obsahu. Ve výchozím nastavení jsou tyto databáze hostovány v počítačích se systémem SQL Server a jsou umístěny ve farmě serveru SharePoint. Objekty BLOB se mohou rychle zvětšovat a spotřebovávají velké množství místního úložiště. Z tohoto důvodu možná budete chtít najít jiné, levnější řešení úložiště. SQL Server poskytuje technologii nazvanou Vzdálené úložiště objektů blob (RBS), která umožňuje ukládat obsah objektu BLOB v systému souborů mimo databázi serveru SQL Server. Pomocí služby RBS mohou být objekty BLOB umístěny v systému souborů v počítači se systémem SQL Server nebo mohou být uloženy v systému souborů v jiném počítači serveru.

RBS vyžaduje, abyste k povolení služby RBS v sharepointu použili zprostředkovatele RBS, například adaptér StorSimple pro službu SharePoint. StorSimple Adaptér pro SharePoint pracuje s RBS, umožňuje přesunout objekty BLOB na server zálohovaný systémem Microsoft Azure StorSimple. Microsoft Azure StorSimple pak ukládá data BLOB místně nebo v cloudu, na základě využití. Objekty BLOB, které jsou velmi aktivní (obvykle označované jako Tier 1 nebo hot data) jsou umístěny místně. Méně aktivní data a archivní data jsou umístěna v cloudu. Po povolení RBS v databázi obsahu je veškerý nový obsah blob vytvořený v sharepointovém bodě uložen na zařízení StorSimple a nikoli v databázi obsahu.

Implementace služby RBS služby Microsoft Azure StorSimple poskytuje následující výhody:

* Přesunutím obsahu objektu BLOB na samostatný server můžete snížit zatížení dotazů na serveru SQL Server, což může zlepšit odezvu serveru SQL Server. 
* Azure StorSimple používá odstranění duplicit a komprese ke snížení velikosti dat.
* Azure StorSimple poskytuje ochranu dat ve formě místních a cloudových snímků. Také pokud umístíte samotnou databázi na zařízení StorSimple, můžete zálohovat databázi obsahu a objekty BLOB společně konzistentním způsobem selhání. (Přesunutí databáze obsahu do zařízení je podporováno pouze pro zařízení řady StorSimple 8000. Tato funkce není podporována pro řadu 5000 nebo 7000.)
* Azure StorSimple obsahuje funkce zotavení po havárii, včetně převzetí služeb při selhání, obnovení souborů a svazků (včetně obnovení testu) a rychlé obnovení dat.
* Můžete použít software pro obnovu dat, jako je například Kroll Ontrack PowerControls, s StorSimple snímky dat BLOB k provedení obnovení na úrovni položky obsahu SharePoint. (Tento software pro obnovu dat je samostatný nákup.)
* StorSimple Adaptér pro SharePoint se zapojuje do portálu SharePoint Central Administration, což vám umožní spravovat celé řešení SharePointu z centrálního umístění.

Přesunutí obsahu blob do systému souborů může poskytnout další úspory nákladů a výhody. Například pomocí RBS může snížit potřebu nákladné úložiště Tier 1 a protože zmenší databázi obsahu, RBS může snížit počet databází požadovaných ve farmě serveru SharePoint. Požadavky na úložiště však mohou ovlivnit i další faktory, jako jsou omezení velikosti databáze a množství obsahu, který není součástí služby RBS. Další informace o nákladech a přínosech používání služby RBS naleznete v [tématech Plán pro službu RBS (SharePoint Foundation 2010)][4] a [Rozhodnutí o použití služby RBS v sharepointu 2013][5].

### <a name="capacity-and-performance-limits"></a>Kapacitní a výkonnostní limity
Než začnete v sharepointovém řešení používat službu RBS, měli byste si být vědomi testovaných omezení výkonu a kapacity SharePoint Serveru 2010 a SharePoint Serveru 2013 a toho, jak tato omezení souvisejí s přijatelným výkonem. Další informace najdete [v tématu Softwarové hranice a limity pro SharePoint 2013](https://technet.microsoft.com/library/cc262787.aspx).

Před konfigurací služby RBS zkontrolujte následující:

* Ujistěte se, že celková velikost obsahu (velikost databáze obsahu plus velikost všech přidružených externalizovaných objektů BLOB) nepřekračuje limit velikosti RBS podporovaný službou SharePoint. Tento limit je 200 GB. 
  
    **Měření databáze obsahu a velikosti objektu BLOB**
  
  1. Spusťte tento dotaz na wfe centrální správy. Spusťte prostředí SharePoint Management Shell a zadejte následující příkaz prostředí Windows PowerShell, abyste získali velikost databází obsahu:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Tento krok získá velikost databáze obsahu na disku.
  2. Spusťte jeden z následujících dotazů SQL v sql management studiu v poli sql serveru v každé databázi obsahu a přidejte výsledek k číslu získanému v kroku 1.
     
     V databázích obsahu SharePointu 2013 zadejte:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     V databázích obsahu SharePointu 2010 zadejte:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Tento krok získá velikost objektů BLOB, které byly externalizovány.
* Doporučujeme uložit veškerý obsah BLOB a databáze místně na zařízení StorSimple. Zařízení StorSimple je cluster se dvěma uzlinami pro vysokou dostupnost. Umístění databází obsahu a objektů BLOB na zařízení StorSimple poskytuje vysokou dostupnost.
  
    Pomocí tradičních doporučených postupů pro migraci serveru SQL Server přesuňte databázi obsahu do zařízení StorSimple. Databázi přesuňte až poté, co byl veškerý obsah blob z databáze přesunut do sdílené složky prostřednictvím služby RBS. Pokud se rozhodnete přesunout databázi obsahu do zařízení StorSimple, doporučujeme nakonfigurovat úložiště databáze obsahu v zařízení jako primární svazek.
* V Microsoft Azure StorSimple, pokud používáte vrstvené svazky, neexistuje žádný způsob, jak zaručit, že obsah uložený místně na zařízení StorSimple nebude vrstvené do cloudového úložiště Microsoft Azure. Proto doporučujeme používat místně vázaných svazků StorSimple ve spojení se službou SharePoint RBS. Tím zajistíte, že veškerý obsah blob zůstane místně na zařízení StorSimple a není přesunuta do Microsoft Azure.
* Pokud neukládáte databáze obsahu na zařízení StorSimple, použijte tradiční SQL Server vysoké dostupnosti osvědčené postupy, které podporují RBS. Clustering serveru SQL Server podporuje službu RBS, zatímco zrcadlení serveru SQL Server nikoli. 

> [!WARNING]
> Pokud jste nepovolili RBS, nedoporučujeme přesunutí databáze obsahu do zařízení StorSimple. Toto je netestovaná konfigurace.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>StorSimple Adaptér pro instalaci služby SharePoint
Před instalací adaptéru StorSimple pro službu SharePoint je nutné nakonfigurovat zařízení StorSimple a zajistit, aby serverová farma služby SharePoint a instance serveru SQL Server splňovaly všechny požadavky. Tento kurz popisuje požadavky na konfiguraci a také postupy pro instalaci a inovaci adaptéru StorSimple pro sharepoint.

## <a name="configure-prerequisites"></a>Konfigurace požadavků
Před instalací adaptéru StorSimple pro službu SharePoint se ujistěte, že instance zařízení StorSimple, serverové farmy služby SharePoint a serveru SQL Server splňují následující požadavky.

### <a name="system-requirements"></a>Požadavky na systém
Adaptér StorSimple pro sharepoint funguje s následujícím hardwarem a softwarem:

* Podporovaný operační systém – Windows Server 2008 R2 SP1, Windows Server 2012 nebo Windows Server 2012 R2
* Podporované verze SharePointu – SharePoint Server 2010 nebo SharePoint Server 2013
* Podporované verze serveru SQL Server – SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition nebo SQL Server 2012 Enterprise Edition
* Podporovaná zařízení StorSimple – řada StorSimple 8000, Řada StorSimple 7000 nebo StorSimple 5000.

### <a name="storsimple-device-configuration-prerequisites"></a>Požadavky na konfiguraci zařízení StorSimple
Zařízení StorSimple je blokové zařízení a jako takové vyžaduje souborový server, na kterém mohou být data hostována. Doporučujeme použít samostatný server, nikoli existující server z farmy služby SharePoint. Tento souborový server musí být ve stejné místní síti (LAN) jako počítač se serverem SQL Server, který je hostitelem databází obsahu.

> [!TIP]
> * Pokud nakonfigurujete sharepointovou farmu pro vysokou dostupnost, měli byste nasadit souborový server pro vysokou dostupnost také.
> * Pokud neukládáte databázi obsahu na zařízení StorSimple, použijte tradiční osvědčené postupy s vysokou dostupností, které podporují rbs. Clustering serveru SQL Server podporuje službu RBS, zatímco zrcadlení serveru SQL Server nikoli. 


Zkontrolujte, zda je zařízení StorSimple správně nakonfigurováno a zda jsou příslušné svazky pro podporu nasazení služby SharePoint nakonfigurovány a přístupné z počítače se serverem SQL Server. Přejděte na [nasazení místního zařízení StorSimple,](storsimple-8000-deployment-walkthrough-u2.md) pokud jste ještě nenasadili a nenakonfigurovali zařízení StorSimple. Poznamenejte si IP adresu zařízení StorSimple; budete potřebovat během instalace StorSimple Adapter pro sharepoint.

Kromě toho se ujistěte, že svazek, který má být použit pro externalizaci blob splňuje následující požadavky:

* Svazek musí být formátován s velikostí alokační jednotky kb 64 kB.
* Webový front-end (WFE) a aplikační servery musí mít přístup ke svazku prostřednictvím cesty UNC (Universal Naming Convention).
* Farma serveru SharePoint server musí být nakonfigurována pro zápis do svazku.

> [!NOTE]
> Po instalaci a konfiguraci adaptéru musí veškerá externalizace blob projít zařízením StorSimple (zařízení představí svazky serveru SQL Server a spravuje vrstvy úložiště). Nelze použít žádné jiné cíle pro externalizaci BLOB.


Pokud máte v plánu použít StorSimple Snapshot Manager pořizovat snímky blob a data databáze, nezapomeňte nainstalovat StorSimple Snapshot Manager na databázovém serveru tak, aby bylo možné použít SQL Writer Service k implementaci služby Windows Volume Shadow Copy Service (VSS).

> [!IMPORTANT]
> Správce snímků StorSimple nepodporuje zapisovač služby SharePoint VSS a nemůže pořizovat snímky dat služby SharePoint konzistentní s aplikací. Ve scénáři služby SharePoint správce snímků StorSimple poskytuje pouze zálohy konzistentní s havárií.


## <a name="sharepoint-farm-configuration-prerequisites"></a>Požadavky konfigurace farmy služby SharePoint
Zkontrolujte, zda je vaše serverová farma SharePoint správně nakonfigurovaná, a to následovně:

* Ověřte, zda je vaše serverová farma SharePoint u zdravě, a zkontrolujte následující:
* Všechny servery služby SharePoint WFE a aplikační servery registrované ve farmě jsou spuštěny a lze jej ze serveru, na který budete instalovat adaptér StorSimple pro službu SharePoint, příkaz ping.
* Služba Časovač služby SharePoint (SPTimerV3 nebo SPTimerV4) je spuštěna na každém serveru WFE a aplikačním serveru.
* Služba Časovač služby SharePoint i fond aplikací služby IIS, pod kterým je spuštěn web centrální správy služby SharePoint, mají oprávnění správce.
* Zkontrolujte, zda je zakázán kontext rozšířeného zabezpečení aplikace Internet Explorer (IE ESC). Chcete-li zakázat službu IE ESC, postupujte takto:
  
  1. Zavřete všechny instance aplikace Internet Explorer.
  2. Spusťte Správce serveru.
  3. V levém podokně klepněte na **položku Místní server**.
  4. V pravém podokně vedle **položky Konfigurace rozšířeného zabezpečení aplikace IE**klepněte na tlačítko **Zapnuto**.
  5. V části **Administrators**klepněte na **tlačítko Vypnout**.
  6. Klikněte na tlačítko **OK**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Požadavky vzdáleného úložiště OBJEKTŮ BLOB (RBS)
Ujistěte se, že používáte podporovanou verzi serveru SQL Server. Jsou podporovány a mohou používat pouze následující verze:

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

Objekty BLOB lze externalizovat pouze na svazcích, které zařízení StorSimple představuje sql serveru. Žádné další cíle pro externalizaci BLOB jsou podporovány.

Po dokončení všech nezbytných kroků konfigurace přejděte na [nainstalovat adaptér StorSimple pro službu SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Instalace adaptéru StorSimple pro SharePoint
Pomocí následujících kroků nainstalujte adaptér StorSimple pro SharePoint. Pokud software přeinstalujete, [přečtěte si informace o inovaci nebo přeinstalaci adaptéru StorSimple pro službu SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). Doba potřebná pro instalaci závisí na celkovém počtu databází služby SharePoint ve farmě serveru SharePoint Server.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>Konfigurace služby RBS
Po instalaci adaptéru StorSimple pro službu SharePoint nakonfigurujte službu RBS, jak je popsáno v následujícím postupu.

> [!TIP]
> StorSimple Adaptér pro SharePoint se zapojuje do stránky Centrální správa SharePointu, což umožňuje povolení nebo zakázání služby RBS v každé databázi obsahu ve farmě služby SharePoint. Povolení nebo zakázání kódu RBS v databázi obsahu však způsobí obnovení služby IIS, které v závislosti na konfiguraci farmy může na okamžik narušit dostupnost webového front-endu služby SharePoint (WFE). (Faktory, jako je například použití front-endu vyrovnávání zatížení, aktuální zatížení serveru a tak dále, můžete omezit nebo eliminovat toto narušení.) Chcete-li chránit uživatele před přerušením, doporučujeme povolit nebo zakázat službu RBS pouze během plánovaného okna údržby.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>Konfigurovat uvolňování paměti
Když jsou objekty odstraněny ze sharepointového webu, nejsou automaticky odstraněny ze svazku úložiště RBS. Místo toho asynchronní program údržby na pozadí odstraní osamocené objekty BLOB z úložiště souborů. Správci systému mohou naplánovat pravidelné spouštění tohoto procesu nebo jej mohou spustit, kdykoli je to nutné.

Tento program údržby (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) je automaticky nainstalován na všech serverech a aplikačních serverech služby SharePoint WFE, pokud povolíte službu RBS. Program je nainstalován v následujícím umístění: *spouštěcí jednotka*:\Program Files\Microsoft SQL Remote Blob Storage 10.50\Maintainer\

Informace o konfiguraci a používání programu údržby naleznete v tématu [Maintain RBS na SharePoint Serveru 2013][8].

> [!IMPORTANT]
> Program správce RBS je náročný na zdroje. Měli byste naplánovat jeho spuštění pouze během období lehké aktivity na farmě služby SharePoint.


### <a name="delete-orphaned-blobs-immediately"></a>Okamžité odstranění osamocených objektů BLOB
Pokud potřebujete odstranit osamocené objekty BLOB okamžitě, můžete použít následující pokyny. Všimněte si, že tyto pokyny jsou příkladem toho, jak to lze provést v prostředí SharePointu 2013 s následujícími součástmi:

* Název databáze obsahu je WSS_Content.
* Název serveru SQL Server je SHRPT13-SQL12\SHRPT13.
* Název webové aplikace je SharePoint – 80.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Upgrade nebo přeinstalace adaptéru StorSimple pro SharePoint
Pomocí následujícího postupu upgradujte sharepointový server a potom přeinstalujte adaptér StorSimple pro sharepoint nebo jednoduše upgradujte nebo přeinstalujte adaptér v existující farmě serveru SharePoint Server.

> [!IMPORTANT]
> Než se pokusíte upgradovat software SharePointu nebo upgradovat nebo přeinstalovat adaptér StorSimple pro SharePoint, zkontrolujte následující informace:
> 
> * Všechny soubory, které byly dříve přesunuty do externího úložiště prostřednictvím služby RBS, nebudou k dispozici, dokud nebude přeinstalace dokončena a funkce RBS znovu povolena. Chcete-li omezit dopad na uživatele, proveďte upgrade nebo přeinstalaci během plánovaného časového období údržby.
> * Doba potřebná pro upgrade nebo přeinstalaci se může lišit v závislosti na celkovém počtu databází služby SharePoint ve farmě serveru SharePoint Server.
> * Po dokončení upgradu/přeinstalace je třeba povolit službu RBS pro databáze obsahu. Další informace naleznete [v tématu Konfigurace služby RBS.](#configure-rbs)
> * Pokud konfigurujete rbs pro farmu služby SharePoint, která má velmi velký počet databází (větší než 200), může časový plán na stránce **Centrální správa služby SharePoint.** Pokud k tomu dojde, aktualizujte stránku. To nemá vliv na proces konfigurace.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>StorSimple Adaptér pro odebrání služby SharePoint
Následující postupy popisují, jak přesunout objekty BLOB zpět do databází obsahu serveru SQL Server a potom odinstalovat adaptér StorSimple pro službu SharePoint. 

> [!IMPORTANT]
> Před odinstalací softwaru adaptéru je nutné přesunout objekty BLOB zpět do databází obsahu.


### <a name="before-you-begin"></a>Než začnete
Než přesunete data zpět do databází obsahu serveru SQL Server a zahájíte proces odebrání adaptéru, shromážděte následující informace:

* Názvy všech databází, pro které je povolena rbs
* Cesta UNC nakonfigurovanéúložiště objektů BLOB

### <a name="move-the-blobs-back-to-the-content-databases"></a>Přesunutí objektů BLOB zpět do databází obsahu
Před odinstalací softwaru StorSimple Adapter pro sharepointový adaptér je nutné migrovat všechny objekty BLOB, které byly externalizovány, zpět do databází obsahu serveru SQL Server. Pokud se pokusíte odinstalovat StorSimple adaptér pro SharePoint před přesunutím všechny objekty BLOB zpět do databáze obsahu, zobrazí se následující varovná zpráva.

![Varovná zpráva](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>Přesunutí objektů BLOB zpět do databází obsahu
1. Stáhněte si každý z externalizovaných objektů.
2. Otevřete stránku **Centrální správa SharePointu** a přejděte na **Položku Nastavení systému**.
3. V **části Azure StorSimple**klikněte na **Konfigurovat storsimple adaptér**.
4. Na stránce **Konfigurovat storsimple adaptér** klepněte na tlačítko **Zakázat** pod každou databází obsahu, kterou chcete odebrat z externího úložiště BLOB. 
5. Odstraňte objekty ze SharePointu a pak je znovu nahrajte.

Případně můžete použít rutinu `RBS Migrate()` prostředí Microsoft PowerShell, která je součástí SharePointu. Další informace naleznete v tématu [Migrace obsahu do nebo z rbs](https://technet.microsoft.com/library/ff628255.aspx).

Po přesunutí objektů BLOB zpět do databáze obsahu přejděte k dalšímu kroku: [Odinstalujte adaptér](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Odinstalace adaptéru
Po přesunutí objektů BLOB zpět do databází obsahu serveru SQL Server použijte jednu z následujících možností k odinstalaci adaptéru StorSimple pro službu SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Odinstalace adaptéru pomocí instalačního programu
1. Pomocí účtu s oprávněními správce se přihlaste k serveru wfe (web front-end).
2. Poklepejte na instalační program StorSimple Adapter pro SharePoint. Spustí se Průvodce instalací.
   
    ![Průvodce instalací](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Klikněte na **Další**. Zobrazí se následující stránka.
   
    ![Průvodce instalací odebrat stránku](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Klepnutím na **tlačítko Odebrat** vyberte proces odebrání. Zobrazí se následující stránka.
   
    ![Stránka potvrzení průvodce instalací](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Chcete-li odebrání potvrdit, klepněte na **tlačítko Odebrat.** Zobrazí se následující stránka průběhu.
   
    ![Stránka průběhu průvodce instalací](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. Po dokončení odebrání se zobrazí stránka dokončení. Klepnutím na **tlačítko Dokončit** zavřete Průvodce instalací.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Odinstalace adaptéru pomocí Ovládacích panelů
1. Otevřete Ovládací panely a klepněte na **položku Programy a funkce**.
2. Vyberte **možnost StorSimple Adapter pro sharepoint a**klepněte na tlačítko **Odinstalovat**.

## <a name="next-steps"></a>Další kroky
[Další informace o StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/library/ff943565.aspx
