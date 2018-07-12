---
title: Nainstalovat adaptér StorSimple pro SharePoint | Dokumentace Microsoftu
description: Popisuje, jak nainstalovat a nakonfigurovat nebo odebrat adaptér služby StorSimple pro SharePoint ve farmě serverů SharePoint.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: v-sharos
ms.openlocfilehash: 2e1b231a5cf13d2655ff66c7e48752729c580f48
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38232863"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Nainstalovat a nakonfigurovat StorSimple Adapter pro SharePoint
## <a name="overview"></a>Přehled
StorSimple Adapter pro SharePoint je komponenta, která umožňuje zřizovat flexibilního úložiště Microsoft Azure StorSimple a ochranu dat na Sharepointové serverové farmy. Adaptér můžete použít k přesunu obsahu binárních rozsáhlých objektů (BLOB) z obsahových databází SQL serveru do Microsoft Azure StorSimple hybrid cloud úložné zařízení.

StorSimple Adapter pro SharePoint funguje jako poskytovatel vzdálené úložiště objektů BLOB (RBS) a používá funkci SQL Server vzdáleného úložiště objektů BLOB pro ukládání nestrukturovaných obsahu služby SharePoint (ve formě objektů BLOB) na souborovém serveru, která je založená na zařízení StorSimple.

> [!NOTE]
> StorSimple Adapter pro SharePoint podporuje SharePoint Server 2010 vzdáleného objektu BLOB úložiště (RBS). SharePoint Server 2010 externí objekt BLOB Storage (EBS) nepodporuje.


* Chcete-li stáhnout StorSimple Adapter pro SharePoint, přejděte na [StorSimple Adapter pro SharePoint] [ 1] webu Microsoft Download Center.
* Další informace o plánování pro strukturu rozpisu zdrojů a RBS omezení [volbou RBS v Sharepointu 2013] [ 2] nebo [plánování RBS (SharePoint Server 2010)] [ 3].

Zbytek tohoto přehledu stručně popisuje role StorSimple Adapter pro SharePoint a omezení Sharepointu kapacitu a výkon, které byste měli vědět, než nainstalujete a nakonfigurujete adaptér. Poté, co si tyto informace, přejděte na [StorSimple Adapter pro SharePoint instalaci](#storsimple-adapter-for-sharepoint-installation) zahájíte nastavení adaptéru.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>StorSimple Adapter pro SharePoint výhody
Na webu služby SharePoint obsah se ukládá jako nestrukturovaných dat objektů BLOB v jedné nebo víc databází obsahu. Ve výchozím nastavení jsou tyto databáze hostované na počítačích se systémem SQL Server, jež jsou umístěny ve farmě serverů SharePoint. Objekty BLOB můžete rychle zvětšují svou velikost, spotřebovává velké množství místního úložiště. Z tohoto důvodu můžete chtít najít řešení jiného, levnější úložiště. SQL Server poskytuje technologie, volá vzdálenou Blob Storage (RBS), která umožňuje ukládat obsah objektu BLOB v systému souborů, mimo databázi serveru SQL Server. S rozpisu zdrojů objekty BLOB mohou být umístěny v systému souborů v počítači, na kterém běží SQL Server, nebo mohou být uloženy v systému souborů na jiném počítači serveru.

RBS vyžaduje použití RBS poskytovatele, jako je například StorSimple Adapter pro SharePoint, umožňuje RBS v Sharepointu. StorSimple Adapter pro SharePoint funguje s rozpisu zdrojů umožňuje přesunout objekty BLOB na server zálohovány v systému Microsoft Azure StorSimple. Microsoft Azure StorSimple pak uloží data objektů BLOB místně nebo v cloudu, na základě využití. Místně se nacházejí objekty BLOB, které jsou velmi aktivní (obvykle označuje jako vrstvy 1 nebo výměně dat). Míň aktivní data a archivní data se nacházejí v cloudu. Po povolení RBS na databázi obsahu jakéhokoli nového obsahu objektů BLOB v Sharepointu vytvoří jsou uloženy v zařízení StorSimple a není v databázi obsahu.

Microsoft Azure StorSimple provádění RBS poskytuje následující výhody:

* Přesunutí obsah objektu BLOB na samostatný server můžete snížit zátěž dotazy na SQL serveru, což může zlepšit rychlost odezvy serveru SQL Server. 
* Azure StorSimple využívá ke snížení velikosti dat deduplikace a komprese.
* Azure StorSimple poskytuje ochranu dat ve formě místních a cloudových snímků. Navíc pokud samotná databáze na zařízení StorSimple, můžete zálohovat databázi obsahu a objekty BLOB společně tak konzistentní pro případ chyby. (Přesunutí databáze obsahu do zařízení je podporována pouze pro zařízení řady StorSimple 8000. Tato funkce není podporována pro řady 5000 a 7000.)
* Azure StorSimple zahrnuje funkce pro zotavení po havárii, včetně převzetí služeb při selhání obnovení souborů a svazků (včetně zkušební obnovení) a rychlé obnovení dat.
* Software na obnovení dat, jako je například Kroll Ontrack PowerControls, můžete pomocí StorSimple snímky dat objektů BLOB k provádění obnovení na úrovni položek obsahu služby SharePoint. (Tento software pro obnovení dat je samostatný nákupní.)
* StorSimple Adapter pro SharePoint zpřístupní portál centrální správy služby SharePoint, vám umožní spravovat celé řešení služby SharePoint z centrálního umístění.

Přesunutí obsahu objektu BLOB do systému souborů můžete zadat další úspory nákladů a výhody. Například pomocí RBS můžete tak snížit potřeba levnější úložiště vrstvy 1 a protože se zmenšuje databáze obsahu, RBS můžete snížit počet databází ve farmě serverů SharePoint vyžaduje. Ale dalších faktorů, jako je například omezení velikosti databáze a množství obsahu bez RBS, může také ovlivnit požadavky na úložiště. Další informace o nákladech a výhody použití RBS najdete v tématu [plánování RBS (SharePoint Foundation 2010)] [ 4] a [volbou RBS v Sharepointu 2013] [5].

### <a name="capacity-and-performance-limits"></a>Limity kapacity a výkonu
Předtím, než byste zvážit použití RBS v řešení služby SharePoint, byste měli vědět, otestované výkon a limity kapacity serveru SharePoint Server 2010 a SharePoint Server 2013 a jak tato omezení se týkají přijatelný výkon. Další informace najdete v tématu [hranice softwaru a omezení pro službu SharePoint 2013](https://technet.microsoft.com/library/cc262787.aspx).

Než nakonfigurujete RBS, přečtěte si následující:

* Ujistěte se, že celková velikost obsahu (velikost databáze obsahu) plus velikost žádné přidružené externalized objekty BLOB nepřekračuje limit velikosti RBS podporované službou SharePoint. Toto omezení je 200 GB. 
  
    **Databázi obsahu měr a velikost objektu BLOB**
  
  1. Spuštění tohoto dotazu na Centrální správa WFE. Spusťte prostředí SharePoint Management Shell a potom zadejte následující příkaz prostředí Windows PowerShell získat velikost databáze obsahu:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Tento krok získá velikost databáze obsahu na disku.
  2. Spusťte některý z následujících dotazů SQL v SQL Management Studio v dialogovém okně SQL server v jednotlivých databázích obsahu a přidat výsledek číslo získaný v kroku 1.
     
     U databází obsahu SharePoint 2013 zadejte:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     U databází obsahu SharePoint 2010 zadejte:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Tento krok získá velikost objektů BLOB, které mají byla externalized.
* Doporučujeme ukládat veškerý obsah objektu BLOB a databázi místně v zařízení StorSimple. Zařízení StorSimple je pro zajištění vysoké dostupnosti clusteru se dvěma uzly. Umístění databáze obsahu a objekty BLOB na zařízení StorSimple poskytuje vysokou dostupnost.
  
    Přesunout databázi obsahu k zařízení StorSimple pomocí tradiční osvědčené postupy migrace systému SQL Server. Přesuňte databázi až po všech obsah objektu BLOB z databáze byl přesunut do sdílené složky prostřednictvím RBS. Pokud budete chtít přesunout databázi obsahu k zařízení StorSimple, doporučujeme nakonfigurovat databázi obsahu úložiště v zařízení jako primární svazek.
* V Microsoft Azure StorSimple, pokud používáte vrstvené svazky neexistuje žádný způsob, jak zajistit obsah místně uložené na zařízení nebude vrstvený do cloudového úložiště Microsoft Azure StorSimple. Proto doporučujeme použít StorSimple místně připojené svazky ve spojení s RBS služby SharePoint. Tím se zajistí, že veškerý obsah objektu BLOB zůstávají místně na zařízení StorSimple a nebyl přesunut do Microsoft Azure.
* V případě databází obsahu není uložený v zařízení StorSimple, používejte tradiční systém SQL Server vysoké dostupnosti osvědčené postupy, které podporují RBS. Clustering SQL serveru podporuje RBS, při v systému SQL Server zrcadlení nemá. 

> [!WARNING]
> Pokud jste ještě nepovolili RBS, nedoporučujeme přesun databáze obsahu do zařízení StorSimple. Toto je netestované konfigurace.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>StorSimple Adapter pro instalaci služby SharePoint
Před instalací StorSimple Adapter pro SharePoint, musíte nakonfigurovat zařízení StorSimple a ujistěte se, že server farmy služby SharePoint a instance systému SQL Server splňují všechny požadované součásti. Tento kurz popisuje požadavky na konfiguraci, také s postupy pro instalaci a upgrade StorSimple Adapter pro SharePoint.

## <a name="configure-prerequisites"></a>Konfigurovat požadavky související s
Před instalací StorSimple Adapter pro SharePoint, ujistěte se, že zařízení StorSimple, farmy služby SharePoint server a instance systému SQL Server splňují následující požadavky.

### <a name="system-requirements"></a>Systémové požadavky
StorSimple Adapter pro SharePoint funguje s níže uvedený hardware a software:

* Podporovaný operační systém – Windows Server 2008 R2 SP1, Windows Server 2012 nebo Windows Server 2012 R2
* Podporované verze služby SharePoint – SharePoint Server 2010 nebo SharePoint Server 2013
* Podporované verze systému SQL Server – SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition nebo SQL Server 2012 Enterprise Edition
* Podporovaná zařízení StorSimple – StorSimple řady 8000, StorSimple 7000 series nebo řady StorSimple 5000.

### <a name="storsimple-device-configuration-prerequisites"></a>Požadavky na konfiguraci zařízení StorSimple
Zařízení StorSimple je blok zařízení a v důsledku vyžaduje souborový server, na kterém je možné hostovat data. Doporučujeme použít samostatný server spíše než existující server od farmy služby SharePoint. Tento souborový server musí být ve stejné místní síti (LAN) jako počítač s SQL serverem, který je hostitelem databáze obsahu.

> [!TIP]
> * Při konfiguraci farmy služby SharePoint pro zajištění vysoké dostupnosti, měli byste také nasadit souborový server pro zajištění vysoké dostupnosti.
> * Pokud jste databázi obsahu neukládejte na zařízení StorSimple, používejte tradiční vysokou dostupnost osvědčené postupy, které podporují RBS. Clustering SQL serveru podporuje RBS, při v systému SQL Server zrcadlení nemá. 


Ujistěte se, že zařízení StorSimple je správně nakonfigurovaná a zda jsou nakonfigurovány odpovídající svazky pro podporu nasazení služby SharePoint a přístupné z počítače systému SQL Server. Přejděte na [nasazení zařízení StorSimple v místním](storsimple-8000-deployment-walkthrough-u2.md) Pokud máte ještě není nasazený a nakonfigurovaný zařízení StorSimple. Poznamenejte si adresu IP zařízení StorSimple; je třeba jej během StorSimple Adapter pro instalaci služby SharePoint.

Kromě toho Ujistěte se, že svazek, který má být použit pro objekt BLOB externalizace splňuje následující požadavky:

* Svazek musí být naformátovaná za použití velikosti 64 KB alokační jednotky.
* Webový front-end (WFE) a aplikační servery, musí mít pro přístup ke svazku přes cestu (Universal Naming Convention).
* Serverové farmy Sharepointu musí nakonfigurovat k zápisu do svazku.

> [!NOTE]
> Po instalaci a konfiguraci adaptéru všech objektů BLOB externalizace musí projít zařízení StorSimple (zařízení bude k dispozici svazky k systému SQL Server a správě vrstev úložiště). Ostatní cíle nelze použít pro externalizace objektů BLOB.


Pokud budete chtít dělat jeho snímky dat objektů BLOB a databáze pomocí StorSimple Snapshot Manageru, nezapomeňte nainstalovat Snapshot Manageru zařízení StorSimple na databázovém serveru tak, aby služba zapisovače SQL může použít k implementaci Windows svazku stínové kopie Service (VSS).

> [!IMPORTANT]
> StorSimple Snapshot Manageru nepodporuje zapisovač SharePoint VSS a nemůže převzít snímků konzistentních dat služby SharePoint. V případě Sharepointu StorSimple Snapshot Manageru poskytuje pouze zálohy konzistentní s havárií.


## <a name="sharepoint-farm-configuration-prerequisites"></a>Požadavky na konfiguraci farmy služby SharePoint
Ujistěte se, že server farmy služby SharePoint je správně nakonfigurována, následujícím způsobem:

* Ověřte, zda server farmy služby SharePoint je v pořádku a zkontrolujte následující:
* Všechny služby SharePoint WFE a aplikační servery zaregistrované ve farmě jsou spuštěné a příkazu ping ze serveru, na kterém budete instalovat adaptér služby StorSimple pro SharePoint.
* Na každém serveru WFE a aplikační server je spuštěna služba Časovač služby SharePoint (SPTimerV3 nebo SPTimerV4).
* Služba časovače služby SharePoint a fond aplikací IIS, pod kterým běží lokality centrální správy služby SharePoint oprávnění správce.
* Ujistěte se, že Internet Explorer rozšířeného bezpečnostní kontext (IE ESC) je zakázán. Postupujte podle těchto kroků k zakázání IE ESC:
  
  1. Zavřete všechny instance Internet Exploreru.
  2. Spusťte správce serveru.
  3. V levém podokně klikněte na tlačítko **místní Server**.
  4. V podokně vpravo vedle **konfigurace rozšířeného zabezpečení Internet Exploreru**, klikněte na tlačítko **na**.
  5. V části **správci**, klikněte na tlačítko **vypnout**.
  6. Klikněte na **OK**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Požadavky na vzdálené úložiště objektů BLOB (RBS)
Ujistěte se, že používáte podporovanou verzi systému SQL Server. Podporované a moct používat RBS jsou pouze následující verze:

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

Objekty BLOB můžete externalized na jenom svazky, které představuje zařízení StorSimple k systému SQL Server. Žádné cíle pro externalizace objektu BLOB se nepodporuje.

Po dokončení všech kroků konfigurace požadovaných součástí, přejděte na [nainstalovat adaptér StorSimple pro SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Nainstalovat adaptér StorSimple pro SharePoint
Nainstalovat adaptér StorSimple pro SharePoint pomocí následujících kroků. Pokud provádíte přeinstalaci programu softwaru, přečtěte si téma [upgradovat nebo přeinstalovat adaptér StorSimple pro SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). Čas potřebný k instalaci, závisí na celkovém počtu všech databáze SharePoint ve farmě služby SharePoint serveru.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>Konfigurace RBS
Až nainstalujete adaptér StorSimple pro SharePoint, nakonfigurujte RBS, jak je popsáno v následujícím postupu.

> [!TIP]
> StorSimple Adapter pro SharePoint zpřístupní na stránce Centrální správa služby SharePoint, což RBS má být povolena nebo zakázána v jednotlivých databázích obsahu farmy služby SharePoint. Nicméně povolení nebo zakázání RBS na databáze obsahu způsobí, že resetování služby IIS, což v závislosti na vaší konfiguraci farmy může chvíli dojít k přerušení dostupnosti SharePoint web front end (WFE). (Faktory, jako je použít nástroj pro vyrovnávání zatížení front-endový, aktuální zatížení serveru a tak dále, můžete omezit nebo odstranit toto přerušení.) Chcete-li chránit uživatele před přerušení, doporučujeme povolit nebo zakázat RBS pouze během naplánovaného časového období údržby.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>Konfigurace uvolnění paměti
Při odstranění objektů z webu služby SharePoint, že nejsou automaticky odstraněny ze svazku úložiště RBS. Místo toho asynchronní, osamocených objektů blob na pozadí Údržba programu odstraní z úložiště souborů. Správci systému můžete naplánovat pravidelné spouštění tohoto procesu nebo mohou začít ho kdykoli je to zapotřebí.

Tento program údržby (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) se automaticky nainstaluje na všechny servery SharePoint WFE a aplikační servery, když povolíte RBS. Program je nainstalován v následujícím umístění: *spouštěcí jednotka*: 10.50\Maintainer\ \Program Files\Microsoft SQL vzdáleného úložiště objektů Blob

Informace o konfiguraci a používání program údržby najdete v tématu [RBS udržovat v SharePoint serveru 2013][8].

> [!IMPORTANT]
> Program RBS funkce maintainer je náročná. Měli byste naplánovat jeho spuštění pouze během období mírnou aktivitu na farmě služby SharePoint.


### <a name="delete-orphaned-blobs-immediately"></a>Okamžitě odstranit osamocený objekty BLOB
Pokud chcete okamžitě odstranit osamocený objekty BLOB, můžete použít následující pokyny. Všimněte si, že tyto pokyny jsou příkladem, jak to lze provést v prostředí SharePoint 2013 pomocí následující komponenty:

* Název databáze obsahu je WSS_Content.
* Název systému SQL Server je SHRPT13 SQL12\SHRPT13.
* Je název webové aplikace SharePoint – 80.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Upgradovat nebo přeinstalovat adaptér StorSimple pro SharePoint
Pomocí následujícího postupu pro upgrade serveru SharePoint server a pak přeinstalovat adaptér StorSimple pro SharePoint nebo jednoduše upgradovat nebo přeinstalovat adaptér v existující serverové farmy služby SharePoint.

> [!IMPORTANT]
> Před pokusem o upgrade softwaru SharePoint nebo upgradu nebo přeinstalovat adaptér StorSimple pro SharePoint, přečtěte si následující informace:
> 
> * Všechny soubory, které byly dříve přesunuty do externího úložiště prostřednictvím RBS nebude k dispozici, až do dokončení cyklům přeinstalování a funkci RBS opět povolena. Pokud chcete omezit dopad na uživatele, provedení jakékoli upgradu nebo přeinstalování během naplánovaného časového období údržby.
> * Čas potřebný k upgradu/přeinstalování se může lišit v závislosti na počtu databází služby SharePoint ve farmě serverů SharePoint.
> * Po dokončení upgradu opětovná instalace je potřeba povolit RBS pro databázi obsahu. Zobrazit [konfigurace RBS](#configure-rbs) Další informace.
> * Pokud konfigurujete RBS pro farmu služby SharePoint, která má velmi velký počet databází (větší než 200), **Centrální správa SharePoint** stránky může být vypršení časového limitu. Pokud k tomu dojde, aktualizujte stránku. To nemá vliv proces konfigurace.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>StorSimple Adapter pro SharePoint odebrání
Následující postupy popisují, jak přesunout objekty BLOB zpět do obsahových databází SQL serveru a pak odinstalovat StorSimple Adapter pro SharePoint. 

> [!IMPORTANT]
> Budete muset přesunout objekty BLOB zpět do databáze obsahu před odinstalováním softwaru adaptér.


### <a name="before-you-begin"></a>Než začnete
Shromážděte následující informace předtím, než přesun dat zpět do databáze SQL serveru obsahu a zahájit proces odebrání adaptéru:

* Názvy všech databází, u kterých je povolená RBS
* Cesta UNC nakonfigurovaného úložiště objektů BLOB.

### <a name="move-the-blobs-back-to-the-content-databases"></a>Objekty BLOB přejděte zpět do databáze obsahu
Před odinstalováním StorSimple Adapter pro SharePoint softwaru, musí všechny objekty BLOB, které byly externalized migrovat zpět na obsahových databází SQL serveru. Pokud se pokusíte odinstalovat StorSimple Adapter pro SharePoint před přesunutím všech objektů BLOB zpět do databáze obsahu, zobrazí se následující zpráva upozornění.

![Zpráva upozornění](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>Chcete-li přesunout objekty BLOB zpět do databáze obsahu
1. Stahování, každý z externalized objektů.
2. Otevřít **Centrální správa SharePoint** stránky a přejděte do **nastavení systému**.
3. V části **Azure StorSimple**, klikněte na tlačítko **nakonfigurovat adaptér StorSimple**.
4. Na **nakonfigurovat adaptér StorSimple** stránky, klikněte na tlačítko **zakázat** tlačítko pod každou z databáze obsahu, které chcete odebrat z externího úložiště objektů BLOB. 
5. Odstranit objekty ze Sharepointu a potom je odešlete znovu.

Alternativně můžete použít Microsoft` RBS Migrate()` rutiny Powershellu, které jsou součástí služby SharePoint. Další informace najdete v tématu [migrovat do nebo z něj RBS](https://technet.microsoft.com/library/ff628255.aspx).

Po přesunutí objekty BLOB zpět do databáze obsahu, přejděte k dalšímu kroku: [odinstalace adaptéru](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Odinstalace adaptéru
Po přesunutí objekty BLOB zpět do databáze serveru SQL Server obsahu, použijte jednu z následujících možností odinstalace StorSimple Adapter pro SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Odinstalace adaptéru pomocí instalačního programu
1. Přihlaste se na webovém serveru front-end (WFE) pomocí účtu s oprávněními správce.
2. Poklikejte na adaptér StorSimple pro instalační program SharePoint. Spustí se Průvodce instalací.
   
    ![Průvodce instalací](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Klikněte na **Další**. Zobrazí se následující stránka.
   
    ![Průvodce instalací odebrat stránka](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Klikněte na tlačítko **odebrat** vyberte proces odebrání. Zobrazí se následující stránka.
   
    ![Stránka potvrzení Průvodce instalací](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Klikněte na tlačítko **odebrat** odebrání potvrďte. Zobrazí se následující stránka průběhu.
   
    ![Stránka průběhu Průvodce instalací](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. Při odstranění je dokončeno, zobrazí se stránka dokončení. Klikněte na tlačítko **Dokončit** zavřete průvodce instalací.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Pomocí ovládacího panelu odinstalovat adaptéru
1. Otevřete ovládací panely a pak klikněte na tlačítko **programy a funkce**.
2. Vyberte **StorSimple Adapter pro SharePoint**a potom klikněte na tlačítko **odinstalovat**.

## <a name="next-steps"></a>Další postup
[Další informace o StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/library/ff943565.aspx
