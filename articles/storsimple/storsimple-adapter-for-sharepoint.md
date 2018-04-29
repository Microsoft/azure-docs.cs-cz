---
title: Instalace adaptéru StorSimple pro službu SharePoint | Microsoft Docs
description: Popisuje, jak nainstalovat a nakonfigurovat nebo odebrat adaptér StorSimple pro službu SharePoint ve farmě serverů SharePoint.
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
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Instalace a konfigurace zařízení StorSimple adaptéru pro službu SharePoint
## <a name="overview"></a>Přehled
Adaptér StorSimple pro službu SharePoint je komponenta, která umožňuje flexibilní úložiště Microsoft Azure StorSimple a ochraně dat na serverové farmy služby SharePoint. Adaptér můžete přesunout binární velkého objektu (binární rozsáhlý OBJEKT) obsah z obsahu databáze systému SQL Server k zařízení úložiště Microsoft Azure StorSimple hybridní cloud.

Adaptér StorSimple pro službu SharePoint funguje jako zprostředkovatel vzdáleného úložiště objektů BLOB (RBS) a používá funkci SQL Server vzdáleného úložiště objektů BLOB k ukládání nestrukturovaných obsahu služby SharePoint (v podobě objektů BLOB) na souborovém serveru, který je zálohovaný díky zařízení StorSimple.

> [!NOTE]
> Adaptér StorSimple pro službu SharePoint podporuje SharePoint Server 2010 vzdálené objektu BLOB úložiště (RBS). SharePoint Server 2010 externí objekt BLOB úložiště (EBS) nejsou podporovány.


* Chcete-li stáhnout adaptér StorSimple pro službu SharePoint, přejděte na [StorSimple adaptéru pro službu SharePoint] [ 1] na webu Microsoft Download Center.
* Informace o plánování pro strukturu rozpisu zdrojů a RBS omezení, přejděte na [rozhodování o použití RBS v SharePoint 2013] [ 2] nebo [plánování RBS (SharePoint Server 2010)][3].

Zbytek tento přehled stručně popisuje roli adaptéru StorSimple pro služby SharePoint a SharePoint kapacitu a výkon limity, které byste měli vědět, než nainstalujete a nakonfigurujete adaptéru. Poté, co si tyto informace, přejděte na [StorSimple adaptéru pro instalaci služby SharePoint](#storsimple-adapter-for-sharepoint-installation) zahájíte nastavení adaptéru.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>Adaptér StorSimple pro výhody služby SharePoint
Na webu služby SharePoint obsah se ukládá jako nestrukturovaných dat objektů BLOB v jedné nebo více databází obsahu. Ve výchozím nastavení jsou tyto databáze hostované na počítačích se systémem SQL Server a jsou umístěny v serverové farmě služby SharePoint. Objekty BLOB můžete rychle zvětšují svou velikost, spotřebovává velké množství místní úložiště. Z tohoto důvodu můžete chtít najít jiné, levnější úložiště řešení. SQL Server poskytuje technologie, která volá vzdálenou objektu Blob úložiště (RBS), umožňuje uložit obsah objektu BLOB v systému souborů, mimo databázi systému SQL Server. S rozpisu zdrojů objekty BLOB mohou být v systému souborů v počítači, který se systémem SQL Server, nebo mohou být uloženy v systému souborů na jiném počítači serveru.

RBS vyžaduje, že používáte poskytovatele RBS, jako je adaptér StorSimple pro službu SharePoint, aby RBS ve službě SharePoint. Adaptér StorSimple pro službu SharePoint pracuje s rozpisu zdrojů umožňuje přesunout objekty BLOB na server zálohovat systém Microsoft Azure StorSimple. Microsoft Azure StorSimple uloží data objektu BLOB místně nebo v cloudu, na základě využití. Objekty BLOB, které jsou velmi aktivní (obvykle označuje jako vrstva 1 nebo aktivní data) nacházet místně. Archivace dat a míň aktivní dat jsou umístěny v cloudu. Jakmile povolíte RBS na databázi obsahu, žádný nový obsah objektu BLOB, které jsou vytvořeny ve službě SharePoint jsou uloženy v zařízení StorSimple a není v databázi obsahu.

Microsoft Azure StorSimple implementace RBS poskytuje následující výhody:

* Přesunutí obsah objektu BLOB na samostatný server můžete snížit zátěž dotazu na SQL serveru, což může zlepšit odezvu systému SQL Server. 
* Azure StorSimple používá ke snížení velikosti dat odstraňování duplicitních dat a komprese.
* Azure StorSimple poskytuje ochranu dat ve formě místních a cloudových snímků. Navíc pokud umístíte samotná databáze v zařízení StorSimple, můžete zálohovat databázi obsahu a objekty BLOB společně v zhroutí konzistentní způsob. (Přesunutí databáze obsahu do zařízení je podporována pouze pro zařízení řady StorSimple 8000. Tato funkce není podporována pro řad 5000 a 7000.)
* Azure StorSimple zahrnuje funkce obnovení po havárii, včetně rychlé obnovení dat, převzetí služeb při selhání a obnovení souborů a svazek (včetně zkušební obnovení).
* Software obnovení dat, například Kroll Ontrack PowerControls, můžete použít s StorSimple snímky dat objektů BLOB k provedení obnovení na úrovni položek obsahu služby SharePoint. (Tento software obnovení dat je samostatného nákupu.)
* Adaptér StorSimple pro službu SharePoint zapojení do portálu Centrální správa SharePoint díky tomu můžete spravovat celé řešení služby SharePoint z centrálního umístění.

Přesunutí obsah objektu BLOB do systému souborů může poskytovat další úsporu nákladů a výhody. Například pomocí RBS můžete snížit nutnost levnější úložiště vrstvy 1 a protože zaplňování databáze s obsahem, RBS můžete snížit počet databází, které jsou potřeba v serverové farmě služby SharePoint. Ale dalších faktorech, například limity velikosti databáze a množství obsahu, bez RBS, může také ovlivnit požadavky na úložiště. Další informace o nákladech a výhody použití RBS najdete v tématu [plánování RBS (SharePoint Foundation 2010)] [ 4] a [rozhodování o použití RBS v SharePoint 2013][5].

### <a name="capacity-and-performance-limits"></a>Omezení kapacitu a výkon
Před uvažujete o používání RBS ve vašem řešení služby SharePoint, byste měli být vědomi otestované výkonu a limity kapacity serveru SharePoint Server 2010 a SharePoint Server 2013 a jak tyto limity vztahují k přijatelný výkon. Další informace najdete v tématu [softwaru hranice a omezení pro SharePoint 2013](https://technet.microsoft.com/library/cc262787.aspx).

Před konfigurací RBS, prostudujte si následující:

* Ujistěte se, že celková velikost obsahu (velikost databáze obsahu) plus velikost všechny související objekty BLOB, externalized není delší než maximální velikost RBS podporované službou SharePoint. Tento limit je 200 GB. 
  
    **Databázi obsahu měr a velikost objektu BLOB**
  
  1. Spusťte tento dotaz na WFE centrální správy. Spusťte prostředí pro správu služby SharePoint a potom zadejte následující příkaz prostředí Windows PowerShell se získat velikost databází obsahu:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Tento krok získá velikost databáze s obsahem na disku.
  2. Spusťte jeden z následujících dotazů SQL v SQL Management Studio na pole serveru SQL v jednotlivých databázích obsahu a přidat výsledek k číslo získaný v kroku 1.
     
     U databází obsahu SharePoint 2013 zadejte:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     U databází obsahu SharePoint 2010 zadejte:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Tento krok získá velikost objektů BLOB, které mají byla externalized.
* Doporučujeme ukládat veškerý obsah objektu BLOB a databáze místně v zařízení StorSimple. Zařízení StorSimple je pro zajištění vysoké dostupnosti clusteru se dvěma uzly. Umístění databáze obsahu a objekty BLOB v zařízení StorSimple poskytuje vysokou dostupnost.
  
    Používejte tradiční osvědčené postupy migrace systému SQL Server pro přesun databáze obsahu do zařízení StorSimple. Až po veškerý obsah objektu BLOB z databáze byl přesunut do sdílené složky prostřednictvím RBS přesunete databázi. Pokud budete chtít přesunout databázi obsahu do zařízení StorSimple, doporučujeme nakonfigurovat databázi obsahu úložiště v zařízení jako primární svazek.
* V Microsoft Azure StorSimple, pokud používáte vrstvené svazky neexistuje žádný způsob, jak zajistit obsah místně uložených na zařízení nebude vrstvené do cloudového úložiště Microsoft Azure StorSimple. Proto doporučujeme používat svazky zařízení StorSimple místně vázaný ve spojení s RBS služby SharePoint. Tím bude zajištěno, že veškerý obsah objektu BLOB místně zůstává v zařízení StorSimple a není přesunut do služby Microsoft Azure.
* Pokud neukládáte databáze obsahu v zařízení StorSimple, použijte tradiční SQL Server vysokou dostupnost doporučené postupy, které podporují RBS. Clustering SQL Server podporuje RBS, při systému SQL Server zrcadlení neexistuje. 

> [!WARNING]
> Pokud jste nepovolili RBS, nedoporučujeme přesunutí databáze obsahu do zařízení StorSimple. Toto je netestované konfigurace.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>Adaptér StorSimple pro instalaci služby SharePoint
Před instalací adaptér StorSimple pro službu SharePoint, musíte nakonfigurovat nastavení zařízení StorSimple a ujistěte se, že serverové farmy služby SharePoint a vytváření instancí systému SQL Server splňují všechny požadavky. Tento kurz popisuje požadavky na konfiguraci, také s postupy pro instalaci a upgrade adaptéru StorSimple pro službu SharePoint.

## <a name="configure-prerequisites"></a>Konfigurace požadavků
Před instalací adaptér StorSimple pro službu SharePoint, ujistěte se, že zařízení StorSimple, serverové farmy služby SharePoint a vytváření instancí systému SQL Server splňují následující požadavky.

### <a name="system-requirements"></a>Požadavky na systém
Adaptér StorSimple pro službu SharePoint funguje s následující hardware a software:

* Podporovaný operační systém – Windows Server 2008 R2 SP1, Windows Server 2012 nebo Windows Server 2012 R2
* Podporované verze služby SharePoint – SharePoint Server 2010 nebo SharePoint Server 2013
* Podporované verze systému SQL Server – SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition nebo SQL Server 2012 Enterprise Edition
* Podporovaná zařízení StorSimple – řady StorSimple 8000, řady 7000 zařízení StorSimple nebo 5000 zařízení StorSimple řady.

### <a name="storsimple-device-configuration-prerequisites"></a>Předpoklady konfigurace zařízení StorSimple
Zařízení StorSimple se zařízení blokovat a jako takový vyžaduje souborový server, který může být hostitelem data. Doporučujeme používat samostatný server, nikoli existující server z farmy služby SharePoint. Tento souborový server musí být ve stejné místní síti (LAN) jako počítač s SQL serverem, který je hostitelem databáze obsahu.

> [!TIP]
> * Pokud provedete konfiguraci farmy služby SharePoint pro zajištění vysoké dostupnosti, měli byste také nasadit souborový server pro vysokou dostupnost.
> * Pokud je v zařízení StorSimple neukládejte databázi obsahu, použijte tradiční vysokou dostupnost osvědčené postupy, které podporují RBS. Clustering SQL Server podporuje RBS, při systému SQL Server zrcadlení neexistuje. 


Ujistěte se, zda zařízení StorSimple je správně nakonfigurován a zda jsou nakonfigurovány odpovídající svazky pro podporu nasazení služby SharePoint a přístupné z počítače serveru SQL Server. Přejděte na [nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md) Pokud ještě nebyla nasazená a nakonfigurovaná zařízení StorSimple. Všimněte si, IP adresu zařízení StorSimple; je nutné ho při StorSimple adaptéru pro instalaci služby SharePoint.

Kromě toho se ujistěte, že svazek, který má být použit pro objekt BLOB externalization splňuje následující požadavky:

* Svazek musí být naformátovaná velikost alokační jednotky 64 KB.
* Web front end (WFE) a aplikační servery musí být schopen přístup ke svazku prostřednictvím cestě Universal Naming Convention (UNC).
* Serverové farmy služby SharePoint musí být nakonfigurované k zápisu do svazku.

> [!NOTE]
> Po instalaci a konfiguraci adaptéru, všech objektů BLOB externalization musí projít zařízení StorSimple (zařízení bude k dispozici svazky pro SQL Server a spravovat vrstvy úložiště). Pro objekt BLOB externalization nelze použít jiné cíle.


Pokud máte v plánu používat StorSimple Snapshot Manager k pořízení snímků data objektů BLOB a databáze, ujistěte se, zda je instalace zařízení StorSimple Snapshot Manager na serveru databáze tak, aby služba zapisovače SQL může použít k implementaci Windows svazku stínové kopie Service (VSS).

> [!IMPORTANT]
> Snapshot Manager zařízení StorSimple nepodporuje zapisovač SharePoint VSS a snímky konzistentní s aplikacemi dat služby SharePoint nelze provést. V případě služby SharePoint poskytuje StorSimple Snapshot Manager pouze konzistentní při selhání zálohování.


## <a name="sharepoint-farm-configuration-prerequisites"></a>Předpoklady konfigurace farmy služby SharePoint
Ujistěte se, že server farmy služby SharePoint je správně nakonfigurována, následujícím způsobem:

* Ověřte, zda server farmy služby SharePoint je v pořádku a zkontrolujte následující:
* Všechny služby SharePoint WFE a zaregistrovaných ve farmě serverů aplikace běží a můžete otestovat pomocí příkazu ping ze serveru, na který budete instalovat adaptér StorSimple pro službu SharePoint.
* Na každém serveru WFE a aplikační server je spuštěna služba časovače služby SharePoint (SPTimerV3 nebo SPTimerV4).
* Služba časovače služby SharePoint a fond aplikací služby IIS, pod kterým běží webu Centrální správa SharePoint mít oprávnění správce.
* Ujistěte se, že Internet Explorer rozšířeného bezpečnostní kontext (IE ESC) je zakázán. Postupujte podle těchto kroků k zakázání IE ESC:
  
  1. Zavřete všechny instance Internet Exploreru.
  2. Spusťte správce serveru.
  3. V levém podokně klikněte na **místní Server**.
  4. V pravém podokně klikněte vedle **konfigurace rozšířeného zabezpečení aplikace Internet Explorer**, klikněte na tlačítko **na**.
  5. V části **správci**, klikněte na tlačítko **vypnout**.
  6. Klikněte na **OK**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Vzdálené úložiště objektů BLOB (RBS) požadavky
Ujistěte se, že používáte podporovanou verzi systému SQL Server. Podporované a moci používat RBS jsou pouze následující verze:

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

Objekty BLOB může být externalized na jenom svazky, které zařízení StorSimple uvede do systému SQL Server. Jsou podporovány žádné jiné cíle pro externalization objektů BLOB.

Pokud jste dokončili všechny kroky požadované konfigurace, přejděte na [instalovat adaptér StorSimple pro službu SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Instalace adaptéru StorSimple pro službu SharePoint
Použijte následující postup k instalaci adaptéru StorSimple pro službu SharePoint. Pokud provádíte přeinstalaci softwaru, přečtěte si téma [upgradovat nebo přeinstalovat adaptér StorSimple pro službu SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). Čas potřebný k instalaci závisí na celkový počet databází služby SharePoint v serverové farmě služby SharePoint.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>Konfigurace RBS
Po instalaci adaptéru StorSimple pro službu SharePoint, nakonfigurujte RBS, jak je popsáno v následujícím postupu.

> [!TIP]
> Adaptér StorSimple pro službu SharePoint zapojení do stránce Centrální správa SharePoint povolení RBS být povolit nebo zakázat v jednotlivých databázích obsahu ve farmě služby SharePoint. Resetování služby IIS, který v závislosti na konfiguraci farmy může přerušit na okamžik dostupnost části služby SharePoint web front end (WFE) způsobí však povolení nebo zakázání RBS na databázi obsahu. (Faktorů, třeba o použití služby Vyrovnávání zatížení front-end, aktuální zatížení serveru a tak dále, můžete omezit nebo odstranit tento přerušení.) Chcete-li chránit uživatele před narušení služeb, doporučujeme povolit nebo zakázat RBS pouze během plánované údržby.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>Konfigurace uvolňování paměti
Pokud jsou objekty odstraněny z webu služby SharePoint, nejsou odstraněny automaticky ze svazku úložiště RBS. Místo toho asynchronní, osamocených objektů BLOB program údržby pozadí odstraní z úložiště souborů. Správci systému můžou naplánovat tento proces pravidelně spouštět nebo můžou spustit se vždy, když je to nezbytné.

Tento program údržby (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) se automaticky nainstaluje na všechny servery SharePoint WFE a aplikační servery, pokud povolíte RBS. Je nainstalován program, který v následujícím umístění: *spouštěcí jednotka*: 10.50\Maintainer\ \Program Files\Microsoft SQL vzdáleného úložiště objektů Blob

Informace o konfiguraci a použití programu údržby najdete v tématu [udržovat RBS v SharePoint Server 2013][8].

> [!IMPORTANT]
> Program RBS funkce maintainer je náročný na prostředky. Měli byste naplánovat jeho spuštění pouze během období mírnou aktivitu na farmě služby SharePoint.


### <a name="delete-orphaned-blobs-immediately"></a>Okamžitě Odstranit osamocené objekty BLOB
Pokud potřebujete odstranit osamocené objekty BLOB okamžitě, můžete použít následující pokyny. Upozorňujeme, že tyto pokyny jsou příklady jak to lze provést v prostředí SharePoint 2013 s následující součásti:

* Název databáze obsahu je WSS_Content.
* Název systému SQL Server je SHRPT13 SQL12\SHRPT13.
* Název webové aplikace je SharePoint – 80.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Upgradovat nebo přeinstalovat adaptér StorSimple pro službu SharePoint
Použijte následující postup k upgradu serveru SharePoint server a poté znovu nainstalujte adaptér StorSimple pro službu SharePoint nebo k jednoduše upgradovat nebo přeinstalovat adaptér v existující serverové farmy služby SharePoint.

> [!IMPORTANT]
> Před dalším pokusem o upgrade softwaru SharePoint nebo upgradu nebo přeinstalovat adaptér StorSimple pro službu SharePoint, projděte si následující informace:
> 
> * Všechny soubory, které byly dříve přesunuty do externího úložiště prostřednictvím RBS nebudete mít k dispozici, dokud dokončení přeinstalování a je RBS funkce znovu povolena. Pokud chcete omezit dopad uživatele, proveďte všechny upgrade nebo přeinstalaci během plánované údržby.
> * Čas potřebný k upgradu nebo přeinstalování se může lišit v závislosti na celkový počet databází služby SharePoint v serverové farmě služby SharePoint.
> * Po dokončení upgradu nebo přeinstalování musíte povolit RBS pro databází obsahu. V tématu [konfigurace RBS](#configure-rbs) Další informace.
> * Pokud konfigurujete RBS pro farmu služby SharePoint, který má velký počet databází (větší než 200), **Centrální správa SharePoint** stránky může vypršení časového limitu. Pokud k tomu dojde, aktualizujte stránku. Proces konfigurace to nemá vliv.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>StorSimple adaptér pro odebrání služby SharePoint
Následující postupy popisují, jak přesunout objekty BLOB zpět do obsahu databáze systému SQL Server a poté odinstalovat adaptér StorSimple pro službu SharePoint. 

> [!IMPORTANT]
> Je nutné přesunout objekty BLOB zpět do databáze obsahu před odinstalací softwaru adaptéru.


### <a name="before-you-begin"></a>Než začnete
Předtím, než přesunout data zpět do obsahu databáze systému SQL Server a spustíte proces odebrání adaptéru, shromážděte následující údaje:

* Názvy všech databází, pro které je povoleno RBS
* Cesta UNC nakonfigurované úložiště objektů BLOB

### <a name="move-the-blobs-back-to-the-content-databases"></a>Přesunout objekty BLOB zpět do databáze obsahu
Před odinstalací adaptér StorSimple pro software služby SharePoint, je nutné migrovat všechny objekty BLOB, které byly externalized zpět do obsahu databáze systému SQL Server. Pokud se pokusíte odinstalovat adaptér StorSimple pro službu SharePoint, než je přesunout všechny objekty BLOB, zpět do databáze obsahu, zobrazí se následující zpráva upozornění.

![Zpráva upozornění](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>Chcete-li přesunout objekty BLOB zpět do databáze obsahu
1. Stáhněte si každý z externalized objektů.
2. Otevřete **Centrální správa SharePoint** stránky a přejděte do **nastavení systému**.
3. V části **Azure StorSimple**, klikněte na tlačítko **nakonfigurovat adaptér StorSimple**.
4. Na **nakonfigurovat adaptér StorSimple** klikněte na tlačítko **zakázat** tlačítko pod každý z databáze obsahu, které chcete odebrat z externího úložiště objektů BLOB. 
5. Odstranit objekty ze služby SharePoint a potom je odešlete znovu.

Alternativně můžete použít Microsoft` RBS Migrate()` rutiny prostředí PowerShell, které jsou součástí služby SharePoint. Další informace najdete v tématu [migraci obsahu do nebo z RBS](https://technet.microsoft.com/library/ff628255.aspx).

Po přesunutí objekty BLOB zpět do databáze s obsahem, přejděte k dalšímu kroku: [odinstalace adaptéru](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Odinstalace adaptéru
Po přesunutí objekty BLOB zpět obsahu databáze systému SQL Server, použijte jednu z následujících možností odinstalace adaptéru StorSimple pro službu SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>K odinstalaci adaptér použili instalační program
1. Použijte účet s oprávněním správce k přihlášení na webový server front-end (WFE).
2. Poklikejte na adaptér StorSimple pro instalační program služby SharePoint. Spustí se Průvodce instalací.
   
    ![Průvodce instalací](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Klikněte na **Další**. Zobrazí se následující stránka.
   
    ![Průvodce instalací odebrat stránka](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Klikněte na tlačítko **odebrat** vyberte proces odebrání. Zobrazí se následující stránka.
   
    ![Instalační program potvrzovací stránku průvodce](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Klikněte na tlačítko **odebrat** odebrání potvrďte. Zobrazí se následující stránka průběhu.
   
    ![Stránka s průběhem instalace Průvodce](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. Po dokončení odebrání, zobrazí se stránka dokončení. Klikněte na tlačítko **Dokončit** zavřete průvodce instalací.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Odinstalace adaptéru pomocí ovládacího panelu
1. Otevřete ovládací panely a pak klikněte na **programy a funkce**.
2. Vyberte **StorSimple adaptéru pro službu SharePoint**a potom klikněte na **odinstalovat**.

## <a name="next-steps"></a>Další postup
[Další informace o zařízení StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/library/ff943565.aspx
