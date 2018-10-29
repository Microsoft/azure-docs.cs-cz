---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 3888242f0379cc97bbe511e49a31a0f7eb8c5372
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166399"
---
<!--author=SharS last changed: 1/14/2016 -->

> [!NOTE]
> Při provádění změn StorSimple Adapter pro SharePoint RBS konfigurace, musíte být přihlášeni pomocí uživatelského účtu, který patří do skupiny Domain Admins. Na stránce konfigurace musíte kromě toho přistupují z prohlížeče běží na stejném hostiteli jako centrální správy.
> 
> 

#### <a name="to-configure-rbs"></a>Ke konfiguraci RBS
1. Otevřete na stránce Centrální správa služby SharePoint a přejděte do **nastavení systému**. 
2. V **Azure StorSimple** klikněte na tlačítko **nakonfigurovat adaptér StorSimple**.
   
    ![Konfigurovat adaptér služby StorSimple](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. Na **nakonfigurovat adaptér StorSimple** stránky:
   
   1. Ujistěte se, že **povolit úpravy cestu** je zaškrtnuto políčko.
   2. Do textového pole zadejte cestu (Universal Naming Convention) z úložiště objektů BLOB.
      
      > [!NOTE]
      > Svazek úložiště objektů BLOB musí být hostovaný na svazku iSCSI nakonfigurované na zařízení StorSimple.

   3. Klikněte na tlačítko **povolit** tlačítko pod každou z databáze obsahu, které chcete konfigurovat pro vzdálené úložiště.
      
      > [!NOTE]
      > Úložiště objektů BLOB musí být sdílené a dostupné ve všech webových front-endu (WFE) serverů a uživatelský účet, který je nakonfigurovaný pro serverovou farmu SharePoint musí mít přístup ke sdílené složce.
      
      ![Povolit poskytovatele RBS](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      Když povolíte nebo zakážete RBS, zobrazí se také následující zpráva.
      
      ![Konfigurace StorSimple adaptéru povolit zakázat](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. Klikněte na tlačítko **aktualizace** tlačítko použít danou konfiguraci. Po kliknutí **aktualizace** tlačítko, stav konfigurace RBS budou aktualizovány na všechny servery WFE a celou farmu bude RBS povolena. Zobrazí se následující zpráva.
      
      ![Zpráva adaptéru konfigurace](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > Pokud konfigurujete RBS pro farmu služby SharePoint s velmi velký počet databází (více než 200), webové stránce Centrální správa SharePoint může být vypršení časového limitu. Pokud k tomu dojde, aktualizujte stránku. To nemá vliv proces konfigurace.

4. Ověření konfigurace:
   
   1. Přihlaste se k webu Centrální správy služby SharePoint a přejděte **nakonfigurovat adaptér StorSimple** stránky.
   2. Zkontrolujte podrobnosti o konfiguraci, abyste měli jistotu, že budou odpovídat nastavení, které jste zadali. 
5. Zkontrolujte, jestli RBS funguje správně:
   
   1. Nahrání dokumentu do Sharepointu. 
   2. Přejděte k cestě UNC, který jste nakonfigurovali. Ujistěte se, že byl vytvořen RBS adresářovou strukturu a obsahuje načtený objekt.
6. (Volitelné) Můžete použít Microsoft RBS `Migrate()` rutiny Powershellu, které jsou součástí služby SharePoint k migraci stávajícího obsahu objektu BLOB k zařízení StorSimple. Další informace najdete v tématu [migrovat do nebo z něj RBS v Sharepointu 2013] [ 6] nebo [migrovat do nebo z něj RBS (SharePoint Foundation 2010)] [7].
7. (Volitelné) Na zkušební instalace můžete ověřit, že se objekty BLOB přesunuly z databáze obsahu: 
   
   1. Spusťte aplikaci SQL Management Studio.
   2. Spusťte dotaz ListBlobsInDB_2010.sql nebo ListBlobsInDB_2013.sql následujícím způsobem.
      
      ```
      **ListBlobsInDB_2013.sql**
      
        USE WSS_Content
        GO
      
        SELECT DocStreams.DocId,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               DocStreams.RbsId,
               TimeLastModified
      
        FROM DocStreams
      
             INNER JOIN AllDocs ON DocStreams.DocId = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      
      **ListBlobsInDB_2010.sql**
      
        USE WSS_Content
        GO
      
        SELECT AllDocStreams.Id,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               RbsId,
               TimeLastModified
        FROM AllDocStreams
      
             INNER JOIN AllDocs ON AllDocStreams.Id = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      ```
      
      Pokud RBS byla správně nakonfigurovaná, by se zobrazit hodnotu NULL ve sloupci SizeOfContentInDB pro libovolný objekt, který jste nahráli a úspěšně externalized s RBS.
8. (Volitelné) Po nakonfigurování RBS a přesunout všechny obsah objektu BLOB k zařízení StorSimple, můžete přesunout databázi obsahu do zařízení. Pokud budete chtít přesunout databázi obsahu, doporučujeme nakonfigurovat databázi obsahu úložiště v zařízení jako primární svazek. Potom k dispozici mají zavedené osvědčené postupy SQL serveru k migraci databáze obsahu do zařízení StorSimple. 
   
   > [!NOTE]
   > Přesunutí databáze obsahu do zařízení je podporována pouze pro řadu StorSimple 8000 (není podporováno pro řady 5000 a 7000).
   
   Pokud ukládat objekty BLOB a databázi obsahu v oddělených svazků na zařízení StorSimple, doporučujeme vám nakonfigurovat ve stejném kontejneru svazku. Tím se zajistí, že bude možné zálohovat společně.
   
   > [!WARNING]
   > Pokud jste ještě nepovolili RBS, nedoporučujeme přesun databáze obsahu do zařízení StorSimple. Toto je netestované konfigurace.
   
9. Přejděte k dalšímu kroku: [konfigurace uvolňování](#configure-garbage-collection).

[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx
