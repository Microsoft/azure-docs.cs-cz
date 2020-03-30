---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 00d292b3ba2d1b6c7c425d4c9f89188e660ac80d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73182195"
---
V tomto postupu:

1. [Připravte se na spuštění spustitelného souboru správce](#to-prepare-to-run-the-maintainer) .
2. [Připravte databázi obsahu a koš pro okamžité odstranění osamocených objektů BLOB](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs).
3. [Spusťte soubor Maintainer.exe](#to-run-the-maintainer).
4. [Vraťte zpět databázi obsahu a nastavení koše](#to-revert-the-content-database-and-recycle-bin-settings).

#### <a name="to-prepare-to-run-the-maintainer"></a>Příprava na spuštění údržbáře
1. Na webovém front-endovém serveru otevřete prostředí pro správu SharePointu 2013 jako správce.
2. Přejděte na *spouštěcí jednotku*složky :\Program Files\Microsoft SQL Remote Blob Storage 10.50\Maintainer\.
3. Přejmenujte **soubor Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config** na **web.config**.
4. Slouží `aspnet_regiis -pdf connectionStrings` k dešifrování souboru web.config.
5. V dešifrovaném souboru web.config přidejte pod `connectionStrings` uzel připojovací řetězec pro instanci serveru SQL a název databáze obsahu. Prohlédněte si následující příklad.
   
    `<add name="RBSMaintainerConnectionWSSContent" connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. Slouží `aspnet_regiis –pef connectionStrings` k opětovnému šifrování souboru web.config. 
7. Přejmenujte web.config na Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config. 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>Příprava databáze obsahu a koše pro okamžité odstranění osamocených objektů BLOB
1. Na serveru SQL Server v aplikaci SQL Management Studio spusťte následující aktualizační dotazy pro cílovou databázi obsahu: 
   
       `use WSS_Content`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`
2. Na webovém front-endserveru v části **Centrální správa**upravte obecné nastavení **webové aplikace,** aby požadovaná databáze obsahu dočasně zakázala koš. Tato akce také vyprázdní koš pro všechny související kolekce webů. Chcete-li to provést, klepněte na **položku** -> Webové aplikace**správy** -> aplikací centrální správy **(Správa webových aplikací)** -> **sharepoint - 80** -> Obecné nastavení**aplikací**. Nastavte **stav koše** na **VYPNUTO**.
   
    ![Obecné nastavení webové aplikace](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>Spuštění správce
* Na webovém front-endovém serveru v prostředí pro správu SharePointu 2013 spusťte správce následujícím způsobem:
  
      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > Pouze `GarbageCollection` operace je podporována pro StorSimple v tomto okamžiku. Všimněte si také, že parametry vydané pro Microsoft.Data.SqlRemoteBlobs.Maintainer.exe rozlišují malá a velká písmena. 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>Vrácení databáze obsahu a nastavení koše
1. Na serveru SQL Server v aplikaci SQL Management Studio spusťte následující aktualizační dotazy pro cílovou databázi obsahu:
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. Na webovém front-endserveru v **centrální správě**upravte **obecné nastavení webové aplikace** pro požadovanou databázi obsahu a znovu povolte koš. Chcete-li to provést, klepněte na **položku** -> Webové aplikace**správy** -> aplikací centrální správy **(Správa webových aplikací)** -> **sharepoint - 80** -> Obecné nastavení**aplikací**. Nastavte stav koše na **ZAPNUTO**.

