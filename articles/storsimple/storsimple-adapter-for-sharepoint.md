---
title: Nainstalovat adaptér StorSimple pro SharePoint | Microsoft Docs
description: Popisuje, jak nainstalovat a nakonfigurovat nebo odebrat adaptér StorSimple pro službu SharePoint ve farmě serverů SharePoint.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: alkohli
ms.openlocfilehash: 639efab3a9eaef400b3fbe3d0b134d02f458b865
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966288"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Instalace a konfigurace adaptéru StorSimple pro SharePoint
## <a name="overview"></a>Přehled
Adaptér StorSimple pro SharePoint je komponenta, která umožňuje poskytovat Microsoft Azure StorSimple flexibilní úložiště a ochranu dat pro serverové farmy služby SharePoint. Adaptér můžete použít k přesunu obsahu binárního Large Object (BLOB) z databáze SQL Server obsahu do Microsoft Azure StorSimple hybridního cloudového úložiště.

Adaptér StorSimple pro SharePoint funguje jako poskytovatel vzdáleného úložiště objektů BLOB (RBS) a používá funkci SQL Server vzdáleného úložiště objektů BLOB k ukládání nestrukturovaného obsahu SharePointu (ve formě objektů BLOB) na souborovém serveru, který je zálohovaný zařízením StorSimple.

> [!NOTE]
> Adaptér StorSimple pro SharePoint podporuje vzdálené úložiště objektů BLOB (RBS) serveru SharePoint Server 2010. Nepodporuje službu SharePoint Server 2010 externí BLOB Storage (EBS).


* Chcete-li stáhnout adaptér StorSimple pro službu SharePoint, na webu Microsoft Download Center klikněte na [adaptér StorSimple pro SharePoint][1] .
* Informace o plánování omezení RBS a RBS najdete v tomto rozhodnutí o [použití RBS ve službě SharePoint 2013][2] nebo [Naplánování pro RBS (SharePoint Server 2010)][3].

Zbytek tohoto přehledu krátce popisuje roli adaptéru StorSimple pro SharePoint a omezení kapacity a výkonu služby SharePoint, o které byste měli vědět před instalací a konfigurací adaptéru. Po kontrole těchto informací si přečtěte téma [Instalace adaptéru StorSimple pro službu SharePoint](#storsimple-adapter-for-sharepoint-installation) a začněte s nastavením adaptéru.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>StorSimple Adapter pro výhody služby SharePoint
V SharePointovém webu je obsah uložen jako nestrukturovaná data objektů BLOB v jedné nebo více databázích obsahu. Ve výchozím nastavení jsou tyto databáze hostovány na počítačích, na kterých běží SQL Server a nacházejí se v serverové farmě služby SharePoint. Objekty blob můžou rychle zvýšit velikost a spotřebovávat velké objemy místních úložišť. Z tohoto důvodu možná budete chtít najít další, levnější řešení úložiště. SQL Server poskytuje technologii s názvem Remote Blob Storage (RBS), která umožňuje uložit obsah objektu BLOB v systému souborů mimo databázi SQL Server. Pomocí RBS se můžou objekty blob nacházet v systému souborů na počítači, na kterém je spuštěný SQL Server, nebo můžou být uložené v systému souborů na jiném počítači se serverem.

RBS vyžaduje, abyste pro povolení RBS v SharePointu použili poskytovatele RBS, jako je například adaptér StorSimple pro službu SharePoint. Adaptér StorSimple pro SharePoint funguje se službou RBS a umožňuje přesunout objekty blob na server zálohovaný systémem Microsoft Azure StorSimple. Microsoft Azure StorSimple pak data objektů BLOB uloží místně nebo v cloudu na základě využití. Objekty blob, které jsou velmi aktivní (obvykle označované jako vrstva 1 nebo Hot data), jsou umístěny lokálně. Méně aktivní data a Archivovaná data se nacházejí v cloudu. Po povolení RBS v databázi obsahu se veškerý nový obsah objektu BLOB vytvořený v SharePointu uloží na zařízení StorSimple, a ne v databázi obsahu.

Microsoft Azure StorSimple implementace RBS přináší následující výhody:

* Přesunutím obsahu objektů BLOB na samostatný server můžete snížit zatížení dotazů na SQL Server, což může zlepšit odezvu SQL Server. 
* Azure StorSimple snižuje velikost dat pomocí odstranění duplicit a komprese.
* Azure StorSimple poskytuje ochranu dat ve formě místních a cloudových snímků. Pokud tedy umístíte samotnou databázi do zařízení StorSimple, můžete zálohovat databázi obsahu a objekty blob dohromady konzistentním způsobem. (Přesun databáze obsahu do zařízení se podporuje jenom pro zařízení řady StorSimple 8000. Tato funkce není podporována pro řady 5000 nebo 7000.)
* Azure StorSimple zahrnuje funkce zotavení po havárii, včetně převzetí služeb při selhání, obnovení souborů a svazků (včetně obnovení testů) a rychlého obnovení dat.
* Pomocí softwaru pro obnovení dat, jako je Kroll Ontrack PowerControls, můžete s StorSimple snímky dat objektů BLOB provádět obnovení obsahu SharePointu na úrovni položek. (Tento software pro obnovení dat je samostatný nákup.)
* Adaptér StorSimple pro SharePoint se připojuje na portál centrální správy služby SharePoint a umožňuje spravovat celé řešení služby SharePoint z centrálního umístění.

Přesunutí obsahu objektů BLOB do systému souborů může poskytovat úspory za jiné náklady a výhody. Například použití kódu RBS může snížit potřebu nákladného úložiště vrstvy 1 a, protože zmenší databázi obsahu, kód RBS může snížit počet databází vyžadovaných ve farmě serverů SharePoint. Požadavky na úložiště ale můžou ovlivnit i jiné faktory, jako jsou limity velikosti databáze a množství obsahu bez RBS. Další informace o nákladech a výhodách používání služby RBS najdete v tématu [Plan for RBS (SharePoint Foundation 2010)][4] a [rozhodnutí o použití RBS ve službě SharePoint 2013][5].

### <a name="capacity-and-performance-limits"></a>Omezení kapacity a výkonu
Než začnete používat RBS v řešení služby SharePoint, měli byste si být vědomi testovaných limitů výkonu a kapacity pro SharePoint Server 2010 a SharePoint Server 2013 a jak se tato omezení týkají přijatelného výkonu. Další informace najdete v tématu [hranice softwaru a omezení pro SharePoint 2013](/SharePoint/install/software-boundaries-and-limits-0).

Před konfigurací RBS zkontrolujte následující:

* Ujistěte se, že celková velikost obsahu (velikost databáze obsahu a velikost jakýchkoli přidružených externě propojených objektů BLOB) nepřekračuje limit velikosti RBS, který podporuje SharePoint. Toto omezení je 200 GB. 
  
    **Měření databáze obsahu a velikosti objektu BLOB**
  
  1. Spusťte tento dotaz na WFE centrální správy. Spusťte prostředí SharePoint Management Shell a potom zadejte následující příkaz prostředí Windows PowerShell, který získá velikost databází obsahu:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Tento krok získá velikost databáze obsahu na disku.
  2. Spusťte jeden z následujících dotazů SQL ve službě SQL Management Studio v poli SQL Server v každé databázi obsahu a přidejte výsledek k číslu získanému v kroku 1.
     
     V databázích obsahu SharePoint 2013 zadejte:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     V databázích obsahu SharePoint 2010 zadejte:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Tento krok získá velikost objektů blob, které byly externě.
* Veškerý obsah objektů BLOB a databází doporučujeme ukládat lokálně na zařízení StorSimple. Zařízení StorSimple je cluster se dvěma uzly, který slouží k zajištění vysoké dostupnosti. Umístění databází obsahu a objektů blob na zařízení StorSimple poskytuje vysokou dostupnost.
  
    K přesunu databáze obsahu do zařízení StorSimple použijte tradiční postupy migrace SQL Server. Přesuňte databázi až po přesunutí veškerého obsahu objektu BLOB z databáze do sdílené složky prostřednictvím souboru RBS. Pokud se rozhodnete přesunout databázi obsahu na zařízení StorSimple, doporučujeme, abyste nakonfigurovali úložiště databáze obsahu na zařízení jako primární svazek.
* Pokud v Microsoft Azure StorSimple používáte vrstvené svazky, neexistuje žádný způsob, jak zaručit, že obsah uložený místně na zařízení StorSimple nebude nastaven na Microsoft Azure cloudové úložiště. Proto doporučujeme používat místně připojené svazky StorSimple ve spojení se službou SharePoint RBS. Tím se zajistí, že veškerý obsah objektu BLOB zůstane místně na zařízení StorSimple a nepřesouvá se do Microsoft Azure.
* Pokud neuložíte databáze obsahu na zařízení StorSimple, použijte SQL Server tradiční osvědčené postupy pro vysokou dostupnost, které podporují RBS. SQL Server clusteringu podporuje RBS, zatímco SQL Server zrcadlení ne. 

> [!WARNING]
> Pokud jste kód RBS nepovolili, nedoporučujeme přesouvat databázi obsahu na zařízení StorSimple. Toto je netestovaná konfigurace.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>StorSimple adaptér pro instalaci služby SharePoint
Předtím, než budete moci nainstalovat adaptér StorSimple pro službu SharePoint, je nutné nakonfigurovat zařízení StorSimple a zajistit, aby farma serveru SharePoint a instance SQL Server splňovaly všechny požadavky. Tento kurz popisuje požadavky na konfiguraci a také postupy pro instalaci a upgrade adaptéru StorSimple pro službu SharePoint.

## <a name="configure-prerequisites"></a>Konfigurovat požadavky
Než budete moct nainstalovat adaptér StorSimple pro SharePoint, ujistěte se, že zařízení StorSimple, farma serverů SharePoint a instance SQL Server splňují následující požadavky.

### <a name="system-requirements"></a>Požadavky na systém
Adaptér StorSimple pro SharePoint funguje s následujícím hardwarem a softwarem:

* Podporovaný operační systém – Windows Server 2008 R2 SP1, Windows Server 2012 nebo Windows Server 2012 R2
* Podporované verze SharePointu – SharePoint Server 2010 nebo SharePoint Server 2013
* Podporované verze SQL Server – SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition nebo SQL Server 2012 Enterprise Edition
* Podporovaná zařízení StorSimple – StorSimple 8000 Series, StorSimple 7000 Series nebo StorSimple 5000 Series.

### <a name="storsimple-device-configuration-prerequisites"></a>Požadavky na konfiguraci zařízení StorSimple
Zařízení StorSimple je blokové zařízení a jako takové vyžaduje souborový server, na kterém je možné hostovat data. Doporučujeme místo stávajícího serveru z farmy služby SharePoint použít samostatný server. Tento souborový server musí být ve stejné místní síti (LAN) jako SQL Server počítač, který je hostitelem databází obsahu.

> [!TIP]
> * Pokud nakonfigurujete farmu služby SharePoint pro vysokou dostupnost, měli byste také nasadit souborový server s vysokou dostupností.
> * Pokud neuložíte databázi obsahu na zařízení StorSimple, použijte tradiční osvědčené postupy pro vysokou dostupnost, které podporují RBS. SQL Server clusteringu podporuje RBS, zatímco SQL Server zrcadlení ne. 


Ujistěte se, že je správně nakonfigurováno zařízení StorSimple a že příslušné svazky pro podporu nasazení služby SharePoint jsou nakonfigurovány a přístupné z počítače s SQL Server. Pokud jste ještě nenainstalovali a nakonfigurovali zařízení StorSimple, můžete přejít na [nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md) . Poznamenejte si IP adresu zařízení StorSimple. budete ho potřebovat při instalaci služby SharePoint v adaptéru StorSimple.

Kromě toho se ujistěte, že svazek, který se má použít pro externost objektů BLOB, splňuje následující požadavky:

* Svazek musí být naformátován velikostí alokační jednotky 64 KB.
* Váš webový front-end (WFE) a aplikační servery musí mít přístup ke svazku prostřednictvím cesty UNC (Universal Naming Convention).
* Farma serverů SharePoint musí být nakonfigurovaná tak, aby na tento svazek mohla zapisovat.

> [!NOTE]
> Po instalaci a konfiguraci adaptéru musí projít všechny externice objektů BLOB přes zařízení StorSimple (zařízení bude prezentovat svazky pro SQL Server a správu vrstev úložiště). Pro externing objektů BLOB nelze použít žádné jiné cíle.


Pokud máte v úmyslu použít Snapshot Manager StorSimple k pořizování snímků dat objektu BLOB a databáze, nezapomeňte na databázový server nainstalovat StorSimple Snapshot Manager, aby mohl použít službu zapisovače SQL k implementaci Windows služba Stínová kopie svazku (VSS).

> [!IMPORTANT]
> StorSimple Snapshot Manager nepodporuje zapisovač SharePoint VSS a nemůže provádět snímky dat SharePointu konzistentní vzhledem k aplikacím. Ve scénáři SharePoint StorSimple Snapshot Manager poskytuje jenom zálohy konzistentní s chybou.


## <a name="sharepoint-farm-configuration-prerequisites"></a>Požadavky na konfiguraci farmy služby SharePoint
Ujistěte se, že je farma serverů SharePoint správně nakonfigurovaná, a to takto:

* Ověřte, zda je farma serveru SharePoint v dobrém stavu, a zkontrolujte následující:
* Všechny služby SharePoint WFE a aplikační servery zaregistrované ve farmě jsou spuštěné a můžou se na serveru, na kterém budete instalovat adaptér StorSimple, použít příkazy pro službu SharePoint.
* Služba Časovač služby SharePoint (SPTimerV3 nebo SPTimerV4) je spuštěná na každém serveru WFE a aplikačním serveru.
* Služba Časovač služby SharePoint i fond aplikací služby IIS, pod kterým je spuštěn web centrální správy služby SharePoint, mají oprávnění správce.
* Zajistěte, aby byl zakázán kontext zabezpečení aplikace Internet Explorer (IE ESC). Pomocí těchto kroků zakážete funkci IE ESC:
  
  1. Zavřete všechny instance aplikace Internet Explorer.
  2. Spusťte Správce serveru.
  3. V levém podokně klikněte na **místní server**.
  4. V pravém podokně vedle **Možnosti konfigurace rozšířeného zabezpečení IE** klikněte **na zapnuto**.
  5. V části **Správci** klikněte na **vypnout**.
  6. Klikněte na **OK**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Požadavky na vzdálené úložiště objektů BLOB (RBS)
Ujistěte se, že používáte podporovanou verzi SQL Server. K dispozici jsou pouze následující verze, které mohou používat RBS:

* Edice SQL Server 2008 Enterprise
* SQL Server 2008 R2 Enterprise Edition
* Edice SQL Server 2012 Enterprise

Objekty blob můžou být externě jenom na svazcích, které zařízení StorSimple prezentuje SQL Server. Nejsou podporovány žádné další cíle pro externing objektů BLOB.

Po dokončení všech kroků konfigurace požadovaných součástí navštivte [instalaci adaptéru StorSimple pro SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Instalace adaptéru StorSimple pro SharePoint
K instalaci adaptéru StorSimple pro SharePoint použijte následující postup. Pokud přeinstalujete software, přečtěte si téma [upgrade nebo přeinstalace adaptéru StorSimple pro službu SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). Čas potřebný k instalaci závisí na celkovém počtu databází služby SharePoint ve farmě serverů SharePoint.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>Konfigurace RBS
Po instalaci adaptéru StorSimple pro službu SharePoint nakonfigurujte kód RBS, jak je popsáno v následujícím postupu.

> [!TIP]
> Adaptér StorSimple pro službu SharePoint se připojí na stránku centrální správy služby SharePoint a povoluje, aby bylo možné povolit nebo zakázat RBS v každé databázi obsahu ve farmě služby SharePoint. Povolení nebo zakázání RBS v databázi obsahu ale způsobí, že obnovení služby IIS, které v závislosti na konfiguraci farmy může mít za následek přerušení dostupnosti webového front-endu služby SharePoint (WFE). (Faktory, jako je použití front-end nástroje pro vyrovnávání zatížení, aktuálního zatížení serveru a tak dále, mohou omezit nebo odstranit toto přerušení.) Při ochraně uživatelů před přerušením doporučujeme povolit nebo zakázat kód RBS pouze během plánovaného časového období údržby.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>Konfigurace uvolňování paměti
Při odstranění objektů z webu služby SharePoint nejsou automaticky odstraněny ze svazku úložiště RBS. Místo toho se v rámci asynchronního programu pro údržbu na pozadí odstraní osamocené objekty BLOB z úložiště souborů. Správci systému můžou naplánovat pravidelné spouštění tohoto procesu nebo ho můžou spustit, kdykoli to bude nutné.

Tento program údržby (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) se automaticky nainstaluje na všechny servery SharePoint WFE a aplikační servery, když povolíte RBS. Program je nainstalován v následujícím umístění: *spouštěcí jednotka*: \Program Files\Microsoft SQL Remote BLOB Storage 10.50 \ údržba \

Informace o konfiguraci a používání programu údržby najdete v tématu [Správa RBS v SharePoint serveru 2013][8].

> [!IMPORTANT]
> Program správy RBS je náročný na prostředky. Měli byste naplánovat spuštění pouze během období s lehkými aktivitami na farmě služby SharePoint.


### <a name="delete-orphaned-blobs-immediately"></a>Okamžitě odstranit osamocené objekty blob
Pokud potřebujete okamžitě odstranit osamocené objekty blob, můžete použít následující pokyny. Všimněte si, že tyto pokyny jsou příkladem toho, jak to lze provést v prostředí SharePoint 2013 s následujícími součástmi:

* Název databáze obsahu je WSS_Content.
* Název SQL Server je SHRPT13-SQL12\SHRPT13..
* Název webové aplikace je SharePoint – 80.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Upgrade nebo přeinstalace adaptéru StorSimple pro SharePoint
Následující postup použijte k upgradu serveru SharePoint Server a následnému přeinstalaci adaptéru StorSimple pro službu SharePoint nebo k upgradu nebo přeinstalování adaptéru v existující farmě serverů SharePoint.

> [!IMPORTANT]
> Než se pokusíte upgradovat software SharePointu nebo upgradovat nebo přeinstalovat adaptér StorSimple pro službu SharePoint, přečtěte si následující informace:
> 
> * Všechny soubory, které byly dříve přesunuty do externího úložiště prostřednictvím RBS, nebudou k dispozici, dokud nebude dokončena opětovná instalace a funkce RBS je znovu povolena. Chcete-li omezit dopad na uživatele, proveďte upgrade nebo přeinstalaci během plánovaného časového období údržby.
> * Čas potřebný k upgradu nebo přeinstalaci se může lišit v závislosti na celkovém počtu databází služby SharePoint v serverové farmě služby SharePoint.
> * Po dokončení upgradu nebo přeinstalace musíte povolit RBS pro databáze obsahu. Další informace najdete v tématu [Konfigurace RBS](#configure-rbs) .
> * Pokud konfigurujete kód RBS pro farmu služby SharePoint, která má velký počet databází (větší než 200), může dojít k vypršení časového limitu stránky **centrální správy služby SharePoint** . Pokud k tomu dojde, aktualizujte stránku. To nemá vliv na proces konfigurace.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>Adaptér StorSimple pro odebrání služby SharePoint
Následující postupy popisují, jak přesunout objekty blob zpátky do SQL Server databází obsahu a pak odinstalovat adaptér StorSimple pro službu SharePoint. 

> [!IMPORTANT]
> Před odinstalací softwaru adaptéru musíte objekty blob přesunout zpátky do databází obsahu.


### <a name="before-you-begin"></a>Než začnete
Než přesunete data zpět do databáze obsahu SQL Server a zahájíte proces odebrání adaptéru, shromážděte následující informace:

* Názvy všech databází, pro které je povoleno RBS
* Cesta UNC nakonfigurovaného úložiště objektů BLOB

### <a name="move-the-blobs-back-to-the-content-databases"></a>Přesunout objekty blob zpátky do databází obsahu
Před odinstalací adaptéru StorSimple pro software služby SharePoint je nutné migrovat všechny objekty blob, které byly externě, do databází obsahu SQL Server. Pokud se pokusíte odinstalovat adaptér StorSimple pro službu SharePoint před přesunutím všech objektů BLOB zpět do databází obsahu, zobrazí se následující zpráva upozornění.

![Zpráva upozornění](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>Přesunutí objektů BLOB zpět do databází obsahu
1. Stáhněte si všechny externované objekty.
2. Otevřete stránku **centrální správy služby SharePoint** a přejděte do **nastavení systému**.
3. V části **Azure StorSimple** klikněte na **konfigurovat adaptér StorSimple**.
4. Na stránce **konfigurovat adaptér StorSimple** klikněte na tlačítko **Zakázat** pod každou databází obsahu, kterou chcete odebrat z externího úložiště objektů BLOB. 
5. Odstraňte objekty ze SharePointu a pak je znovu nahrajte.

Alternativně můžete použít `RBS Migrate()` rutinu prostředí Microsoft PowerShell, která je součástí služby SharePoint. Další informace najdete v tématu [migrace obsahu do RBS nebo](/previous-versions/office/sharepoint-foundation-2010/ff628255(v=office.14))z něj.

Po přesunutí objektů BLOB zpět do databáze obsahu přejděte k dalšímu kroku: [odinstalujte adaptér](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Odinstalujte adaptér.
Po přesunutí objektů BLOB zpět do databáze obsahu SQL Server použijte jednu z následujících možností k odinstalaci adaptéru StorSimple pro službu SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Použití instalačního programu k odinstalaci adaptéru
1. K přihlášení na webový front-end Server (WFE) použijte účet s oprávněními správce.
2. Dvakrát klikněte na adaptér StorSimple pro instalační službu SharePoint. Spustí se Průvodce instalací nástroje.
   
    ![Průvodce instalací](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Klikněte na **Next** (Další). Zobrazí se následující stránka.
   
    ![Průvodce instalací – odebrání stránky](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Kliknutím na **Odebrat** vyberte proces odebrání. Zobrazí se následující stránka.
   
    ![Stránka pro potvrzení Průvodce instalací](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Kliknutím na tlačítko **Odebrat** potvrďte odebrání. Zobrazí se následující stránka průběhu.
   
    ![Stránka průběh Průvodce instalací](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. Po dokončení odebrání se zobrazí stránka dokončení. Kliknutím na tlačítko **Dokončit** zavřete Průvodce instalací nástroje.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Postup při použití ovládacího panelu k odinstalaci adaptéru
1. Otevřete ovládací panely a potom klikněte na **programy a funkce**.
2. Vyberte **adaptér StorSimple pro SharePoint** a pak klikněte na **odinstalovat**.

## <a name="next-steps"></a>Další kroky
[Přečtěte si další informace o StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: /SharePoint/administration/rbs-planning
[3]: /previous-versions/office/sharepoint-server-2010/ff628583(v=office.14)
[4]: /previous-versions/office/sharepoint-foundation-2010/ff628569(v=office.14)
[5]: /SharePoint/administration/rbs-planning
[8]: /SharePoint/administration/maintain-rbs