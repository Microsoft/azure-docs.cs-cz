---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 0b5d9deacdd4266da30f17c95b6e575a652d2f76
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889540"
---
V tomto postupu provedete následující:

1. [Příprava ke spuštění spustitelného souboru Funkce Maintainer](#to-prepare-to-run-the-maintainer) .
2. [Příprava databáze obsahu a Koš pro okamžité odstranění objektů BLOB osamocené](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs).
3. [Spustit Maintainer.exe](#to-run-the-maintainer).
4. [Obnovit databázi obsahu a nastavení koše](#to-revert-the-content-database-and-recycle-bin-settings).

#### <a name="to-prepare-to-run-the-maintainer"></a>Abyste se připravili na spouštění Funkce Maintainer
1. Na webovém serveru front-end otevřete prostředí SharePoint 2013 Management Shell jako správce.
2. Přejděte do složky *spouštěcí jednotka*: \Program Files\Microsoft 10.50\Maintainer SQL vzdáleného úložiště objektů Blob\.
3. Přejmenovat **Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config** k **web.config**.
4. Použití `aspnet_regiis -pdf connectionStrings` pro dešifrování souboru web.config.
5. V souboru web.config dešifrovaný pod `connectionStrings` uzel, přidejte připojovací řetězec pro vaši instanci SQL serveru a název databáze obsahu. Prohlédněte si následující příklad.
   
    `<add name=”RBSMaintainerConnectionWSSContent” connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. Použití `aspnet_regiis –pef connectionStrings` k opětovnému zašifrování souboru web.config. 
7. Přejmenujte soubor web.config na Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config. 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>Příprava obsahu databáze a Koš, abyste mohli okamžitě odstranit osamocených objektů BLOB
1. Na serveru SQL v SQL Management studiu spusťte následující dotazy aktualizace pro cílovou databázi obsahu: 
   
       `use WSS_Content`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`
2. Na webovém serveru front-end v části **centrální správy**, upravit **obecné nastavení webové aplikace** pro požadovanou databázi obsahu dočasně zakázat odpadkového koše. Tato akce bude také vysypat Koš pro všechna související kolekce webů. Chcete-li to provést, klikněte na tlačítko **centrální správy** -> **správy aplikací** -> **webových aplikací (Správa webové aplikace)**  ->  **SharePoint - 80** -> **obecné nastavení aplikace**. Nastavte **Stav koše** k **OFF**.
   
    ![Obecné nastavení webové aplikace](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>Ke spuštění funkce Maintainer
* Na webovém serveru front-end v prostředí SharePoint 2013 Management Shell, spusťte Funkce Maintainer následujícím způsobem:
  
      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > Pouze `GarbageCollection` operace je v tuto chvíli nepodporuje pro StorSimple. Všimněte si také, že parametry vydané pro Microsoft.Data.SqlRemoteBlobs.Maintainer.exe jsou malá a velká písmena. 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>Obnova databáze obsahu a nastavení koše
1. Na serveru SQL v SQL Management studiu spusťte následující dotazy aktualizace pro cílovou databázi obsahu:
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. Na webovém serveru front-end v **centrální správy**, upravit **obecné nastavení webové aplikace** pro požadovanou databázi obsahu opětovné povolení odpadkového koše. Chcete-li to provést, klikněte na tlačítko **centrální správy** -> **správy aplikací** -> **webových aplikací (Správa webové aplikace)**  ->  **SharePoint - 80** -> **obecné nastavení aplikace**. Nastavit stav recyklace Bin **ON**.

