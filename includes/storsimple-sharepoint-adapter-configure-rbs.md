---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 45b2ac73ffcb4b777572f9ad62d8aae0e8277e1c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95563637"
---
> [!NOTE]
> Při provádění změn konfigurace StorSimple adaptéru pro konfiguraci služby SharePoint RBS musíte být přihlášeni pomocí uživatelského účtu, který patří do skupiny Domain Admins. Kromě toho musíte přistupovat ke konfigurační stránce z prohlížeče běžícího na stejném hostiteli jako centrální Správa.
> 
> 

#### <a name="to-configure-rbs"></a>Konfigurace RBS
1. Otevřete stránku centrální správy služby SharePoint a přejděte do **nastavení systému**. 
2. V části **Azure StorSimple** klikněte na **konfigurovat adaptér StorSimple**.
   
    ![Konfigurace adaptéru StorSimple](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. Na stránce **Konfigurace adaptéru StorSimple** :
   
   1. Ujistěte se, že je zaškrtnuté políčko **Povolit cestu pro úpravy** .
   2. Do textového pole zadejte cestu UNC (Universal Naming Convention) úložiště objektů BLOB.
      
      > [!NOTE]
      > Svazek úložiště objektů BLOB musí být hostovaný na svazku iSCSI nakonfigurovaném na zařízení StorSimple.

   3. Klikněte na tlačítko **Povolit** pod každou databází obsahu, kterou chcete nakonfigurovat pro vzdálené úložiště.
      
      > [!NOTE]
      > Úložiště objektů BLOB musí být sdíleno a dosažitelné všemi webovými front-end (WFE) servery a uživatelský účet nakonfigurovaný pro farmu serveru SharePoint musí mít přístup ke sdílené složce.
      
      ![Povolit poskytovatele RBS](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      Při povolování nebo zakazování RBS se zobrazí také následující zpráva.
      
      ![Konfigurace povolení zakázání adaptéru StorSimple](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. Kliknutím na tlačítko **aktualizovat** aplikujete konfiguraci. Po kliknutí na tlačítko **aktualizovat** se stav konfigurace RBS aktualizuje na všech serverech WFE a celá farma bude mít povolený kód RBS. Zobrazí se následující zpráva.
      
      ![Zpráva konfigurace adaptéru](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > Pokud konfigurujete kód RBS pro farmu služby SharePoint s velmi velkým počtem databází (větší než 200), může webová stránka centrální správy služby SharePoint vyprší časový limit. Pokud k tomu dojde, aktualizujte stránku. To nemá vliv na proces konfigurace.

4. Ověřte konfiguraci:
   
   1. Přihlaste se k webu centrální správy služby SharePoint a přejděte na stránku **Konfigurace adaptéru StorSimple** .
   2. Zkontrolujte podrobnosti o konfiguraci a ujistěte se, že odpovídají zadaným nastavením. 
5. Ověřte, že kód RBS funguje správně:
   
   1. Nahrajte dokument do SharePointu. 
   2. Přejděte na cestu UNC, kterou jste nakonfigurovali. Ujistěte se, že se vytvořila adresářová struktura RBS a že obsahuje nahraný objekt.
6. Volitelné `Migrate()` K migraci existujícího obsahu objektů blob do zařízení StorSimple můžete použít rutinu Microsoft RBS PowerShell, která je součástí SharePointu. Další informace najdete v tématu [migrace obsahu do RBS nebo z kódu RBS ve službě SharePoint 2013][6] nebo [migrace obsahu do nebo z kódu RBS (SharePoint Foundation 2010)][7].
7. Volitelné V případě testovacích instalací můžete ověřit, že se objekty blob přesunuly z databáze obsahu následujícím způsobem: 
   
   1. Spusťte SQL Management Studio.
   2. Spusťte dotaz ListBlobsInDB_2010. SQL nebo ListBlobsInDB_2013. SQL následujícím způsobem.
      
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
      
      Pokud byl kód RBS nakonfigurován správně, musí se ve sloupci SizeOfContentInDB zobrazit hodnota NULL pro libovolný objekt, který byl nahrán a úspěšně vytvořen externě pomocí RBS.
8. Volitelné Až nakonfigurujete RBS a přesunete veškerý obsah objektu BLOB na zařízení StorSimple, můžete přesunout databázi obsahu do zařízení. Pokud se rozhodnete přesunout databázi obsahu, doporučujeme, abyste nakonfigurovali úložiště databáze obsahu na zařízení jako primární svazek. Pak použijte zřízené SQL Server osvědčené postupy k migraci databáze obsahu do zařízení StorSimple. 
   
   > [!NOTE]
   > Přesunutí databáze obsahu do zařízení je podporováno pouze pro řady StorSimple 8000 (není podporováno pro řady 5000 nebo 7000).
   
   Pokud ukládáte objekty BLOB a databázi obsahu na samostatné svazky na zařízení StorSimple, doporučujeme, abyste je nakonfigurovali ve stejném kontejneru svazků. Tím zajistíte, že budou zálohovány společně.
   
   > [!WARNING]
   > Pokud jste kód RBS nepovolili, nedoporučujeme přesouvat databázi obsahu na zařízení StorSimple. Toto je netestovaná konfigurace.
   
9. Přejít k dalšímu kroku: [Konfigurace uvolňování paměti](#configure-garbage-collection).

[6]: /SharePoint/administration/migrate-content-into-or-out-of-rbs
[7]: /previous-versions/office/sharepoint-foundation-2010/ff628255(v=office.14)