---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: f84fe995e65d2b67aaaf4ff9acc4a6a44ce607dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175047"
---
> [!NOTE]
> Při provádění změn konfigurace StorSimple Adapter pro sharepointový adaptér RBS musíte být přihlášeni pomocí uživatelského účtu, který patří do skupiny Domain Admins. Kromě toho je nutné přistupovat ke stránce konfigurace z prohlížeče spuštěného na stejném hostiteli jako centrální správa.
> 
> 

#### <a name="to-configure-rbs"></a>Konfigurace služby RBS
1. Otevřete stránku Centrální správa SharePointu a přejděte na **Položku Nastavení systému**. 
2. V části **Azure StorSimple** klikněte na **Konfigurovat storsimple adaptér**.
   
    ![Konfigurace adaptéru StorSimple](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. Na stránce **Konfigurovat storsimple adaptér:**
   
   1. Ujistěte se, že je zaškrtnuté políčko **Povolit úpravy cesty.**
   2. Do textového pole zadejte cestu unc (Univerzální konvence pojmenování) úložiště BLOB.
      
      > [!NOTE]
      > Svazek úložiště objektů BLOB musí být hostován na svazku iSCSI nakonfigurovaném na zařízení StorSimple.

   3. Klikněte na tlačítko **Povolit** pod každou databází obsahu, kterou chcete nakonfigurovat pro vzdálené úložiště.
      
      > [!NOTE]
      > Úložiště objektů BLOB musí být sdíleno a dosažitelné všemi servery webového front-endu (WFE) a uživatelský účet, který je nakonfigurován pro farmu serveru SharePoint, musí mít přístup ke sdílené položce.
      
      ![Povolit zprostředkovatele RBS](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      Pokud povolíte nebo zakážete rbs, zobrazí se také následující zpráva.
      
      ![Konfigurovat zakázat funkci Zakázat adaptér StorSimple](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. Chcete-li použít konfiguraci, klepněte na tlačítko **Aktualizovat.** Po klepnutí na tlačítko **Aktualizovat** bude stav konfigurace RBS aktualizován na všech serverech WFE a celá farma bude povolena jako RBS. Zobrazí se následující zpráva.
      
      ![Zpráva o konfiguraci adaptéru](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > Pokud konfigurujete rbs pro farmu služby SharePoint s velkým počtem databází (větší než 200), může časový plán na webové stránce Centrální správy služby SharePoint. Pokud k tomu dojde, aktualizujte stránku. To nemá vliv na proces konfigurace.

4. Ověřte konfiguraci:
   
   1. Přihlaste se k webu Centrální správy SharePointu a přejděte na stránku **Konfigurovat adaptér StorSimple.**
   2. Zkontrolujte podrobnosti konfigurace, abyste se ujistili, že odpovídají zadanému nastavení. 
5. Ověřte, zda služba RBS funguje správně:
   
   1. Nahrajte dokument do SharePointu. 
   2. Přejděte na cestu UNC, kterou jste nakonfigurovali. Ujistěte se, že byla vytvořena adresářová struktura RBS a zda obsahuje nahraný objekt.
6. (Nepovinné) Rutina prostředí Microsoft `Migrate()` RBS PowerShell, která je součástí sharepointu, můžete použít k migraci existujícího obsahu objektu BLOB do zařízení StorSimple. Další informace najdete [v tématu Migrace obsahu do nebo z RBS v SharePointu 2013][6] nebo [Migrace obsahu do nebo z RBS (SharePoint Foundation 2010)][7].
7. (Nepovinné) V testovacích instalacích můžete ověřit, že objekty BLOB byly přesunuty z databáze obsahu následujícím způsobem: 
   
   1. Spusťte sql management studio.
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
      
      Pokud rbs byla nakonfigurována správně, hodnota NULL by se měla zobrazit ve sloupci SizeOfContentInDB pro všechny objekty, které byly odeslány a úspěšně externalizovány s RBS.
8. (Nepovinné) Po konfiguraci RBS a přesunutí veškerého obsahu blob do zařízení StorSimple můžete přesunout databázi obsahu do zařízení. Pokud se rozhodnete přesunout databázi obsahu, doporučujeme nakonfigurovat úložiště databáze obsahu v zařízení jako primární svazek. Potom použijte zavedené SQL Server osvědčené postupy pro migraci databáze obsahu do zařízení StorSimple. 
   
   > [!NOTE]
   > Přesunutí databáze obsahu do zařízení je podporováno pouze pro řadu StorSimple 8000 (není podporováno pro řadu 5000 nebo 7000).
   
   Pokud ukládáte objekty BLOB a databázi obsahu v samostatných svazcích na zařízení StorSimple, doporučujeme je nakonfigurovat ve stejném kontejneru svazků. Tím je zajištěno, že budou zálohovány společně.
   
   > [!WARNING]
   > Pokud jste nepovolili RBS, nedoporučujeme přesunutí databáze obsahu do zařízení StorSimple. Toto je netestovaná konfigurace.
   
9. Přejít k dalšímu kroku: [Konfigurace uvolňování paměti](#configure-garbage-collection).

[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx
