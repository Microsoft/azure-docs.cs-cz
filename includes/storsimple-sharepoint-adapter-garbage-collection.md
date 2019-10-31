---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 00d292b3ba2d1b6c7c425d4c9f89188e660ac80d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73182195"
---
V tomto postupu provedete tyto kroky:

1. [Připravte se na spuštění spustitelného souboru Maintainer](#to-prepare-to-run-the-maintainer) .
2. [Připravte databázi obsahu a složku Koš pro okamžité odstranění osamocených objektů BLOB](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs).
3. [Spusťte soubor udržovatelner. exe](#to-run-the-maintainer).
4. [Obnovte nastavení databáze obsahu a složky Koš](#to-revert-the-content-database-and-recycle-bin-settings).

#### <a name="to-prepare-to-run-the-maintainer"></a>Příprava na spuštění údržby
1. Na webovém front-end serveru otevřete prostředí SharePoint 2013 Management Shell jako správce.
2. Přejděte na *spouštěcí jednotku*složky: \Program Files\Microsoft SQL Remote BLOB Storage 10.50 \ Maintainer\.
3. Přejmenujte soubor **Microsoft. data. SqlRemoteBlobs. Maintainer. exe. config** na **Web. config**.
4. K dešifrování souboru Web. config použijte `aspnet_regiis -pdf connectionStrings`.
5. V dešifrovaném souboru Web. config v uzlu `connectionStrings` přidejte připojovací řetězec pro instanci serveru SQL Server a název databáze obsahu. Prohlédněte si následující příklad.
   
    `<add name="RBSMaintainerConnectionWSSContent" connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. K opětovnému zašifrování souboru Web. config použijte `aspnet_regiis –pef connectionStrings`. 
7. Přejmenujte soubor Web. config na soubor Microsoft. data. SqlRemoteBlobs. Maintainer. exe. config. 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>Příprava databáze obsahu a složky Koš pro okamžité odstranění osamocených objektů BLOB
1. Na SQL Server v SQL Management Studio spusťte následující aktualizační dotazy pro cílovou databázi obsahu: 
   
       `use WSS_Content`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`
2. Na webovém front-end serveru v části **Centrální správa**upravte **Obecná nastavení webové aplikace** pro požadovanou databázi obsahu pro dočasné vypnutí odpadkového koše. Tato akce také vyprázdní odpadkový koš pro všechny související kolekce webů. Provedete to tak, že kliknete na **Centrální správa** -> **Správa aplikací** -> **webové aplikace (spravovat webové aplikace)**  -> **SharePoint-80** -> **Obecná nastavení aplikace**. Nastavte **stav odpadkového koše** na **off (vypnuto**).
   
    ![Obecná nastavení webové aplikace](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>Spuštění údržby
* Na webovém front-end serveru ve službě SharePoint 2013 Management Shell spusťte údržbu následujícím způsobem:
  
      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > Pro StorSimple se v tuto chvíli podporuje jenom operace `GarbageCollection`. Všimněte si také, že parametry vydané pro Microsoft. data. SqlRemoteBlobs. Maintainer. exe rozlišují velká a malá písmena. 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>Vrácení nastavení databáze obsahu a složky Koš
1. Na SQL Server v SQL Management Studio spusťte následující aktualizační dotazy pro cílovou databázi obsahu:
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. Na webovém front-end serveru v **centrální správě**upravte **Obecná nastavení webové aplikace** pro požadovanou databázi obsahu, aby bylo možné koš znovu povolit. Provedete to tak, že kliknete na **Centrální správa** -> **Správa aplikací** -> **webové aplikace (spravovat webové aplikace)**  -> **SharePoint-80** -> **Obecná nastavení aplikace**. Nastavte stav odpadkového koše **na zapnuto**.

