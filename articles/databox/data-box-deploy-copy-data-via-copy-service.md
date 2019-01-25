---
title: Kopírování dat do vaší aplikace Microsoft Azure Data Box přes SMB | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data do Azure Data Box přes službu pro kopírování dat
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/24/2019
ms.author: alkohli
ms.openlocfilehash: 9d271642a432d8a149fbe468087a0598c91e7c36
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54902375"
---
# <a name="tutorial-use-data-copy-service-to-directly-ingest-data-into-azure-data-box-preview"></a>Kurz: Pomocí služby kopie dat přímo ingestovat data do Azure Data Box (preview)

Tento kurz popisuje, jak k ingestování dat pomocí služby kopie dat bez nutnosti zprostředkujícího hostitele. Službu kopírování dat běží místně na zařízení Data Box, připojí k zařízení NAS prostřednictvím protokolu SMB a zkopíruje data do zařízení Data Box.

Použití služby kopie dat:

- V prostředí úložiště připojeného k síti (NAS), kde zprostředkující hostitelé nemusí být k dispozici.
- Pomocí malé soubory, které trvat týdny pro příjem a odesílání data. Tato služba výrazně zlepšuje dobu příjem a odesílání.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Požadavky
> * Kopírování dat do Data Boxu


## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste [kurzu: Nastavení Azure Data Box](data-box-deploy-set-up.md).
2. Obdrželi jste Data Box a stav objednávky na portálu je **dodáno**.
3. Pověření zdrojového serveru NAS zařízení se připojujete k pro kopírování dat, které máte.
4. Jste připojeni k vysokorychlostní sítí. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. Pokud 10 GbE připojení není k dispozici, použijte odkaz 1 GbE dat ale rychlosti kopírování bude mít vliv.

## <a name="copy-data-to-data-box"></a>Kopírování dat do Data Boxu

Po připojení k serveru NAS, dalším krokem je zkopírovat data. Než začnete kopírování dat, projděte si následující aspekty:

- Při kopírování dat, ujistěte se, že velikost dat odpovídá omezení velikosti podle [služby Azure storage a omezení zařízení Data Box](data-box-limits.md).
- Pokud jiné aplikace mimo zařízení Data Box je současně nahrávaných dat odeslaný zařízení Data Box, potom to může způsobit poškození nahrávání úlohy selhání a data.
- Pokud data se často Měněná jako jeho čtení služby kopie dat, je možné, uvidíte chyby nebo poškození dat.

Kopírování dat pomocí služby kopie dat, je potřeba vytvořit úlohu. Postupujte podle těchto kroků můžete vytvořit úlohu, která zkopíruje data.

1. Přejděte ve místního webového uživatelského rozhraní vašeho zařízení Data Box **spravovat > kopírování dat**.
2. V **kopírování dat** klikněte na **vytvořit**.

    ![Klikněte na ** vytvořit ** na ** Kopírovat data ** stránku](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. V **konfigurace a spuštění** dialogovém okně zadejte následující vstupy.
    
    |Pole                          |Hodnota    |
    |-------------------------------|---------|
    |Název úlohy                       |Jedinečný název méně než 230 znaků pro úlohu. Nejsou povolené tyto znaky v názvu úlohy - \<, \>, \|, \?, \*, \\, \:, \/, a \\\.         |
    |Umístění zdroje                |Zadejte cestu k protokolu SMB pro zdroj dat ve formátu: `\\<ServerIPAddress>\<ShareName>` nebo `\\<ServerName>\<ShareName>`.        |
    |Uživatelské jméno                       |Uživatelské jméno v `\\<DomainName><UserName>` formát pro přístup ke zdroji dat.        |
    |Heslo                       |Heslo pro přístup ke zdroji dat           |
    |Cílový účet úložiště    |Vyberte cílový účet úložiště k odeslání dat z rozevíracího seznamu.         |
    |Typ cílového úložiště       |Vyberte typ cílového úložiště objektů blob bloku, objekty blob stránky nebo soubory Azure.        |
    |Cílový kontejner nebo sdílená složka    |Zadejte název kontejneru nebo sdílet k odesílání dat v účtu cílového úložiště. Název může obsahovat název sdílené složky ani název kontejneru. Například `myshare` nebo `mycontainer`. Můžete také zadat ve formátu `sharename\directory_name` nebo `containername\virtual_directory_name` v cloudu.        |
    |Zkopírovat soubory, které odpovídají vzoru    | Zadejte odpovídající vzor názvů souborů v následujících dvou způsobů.<ul><li>**Použití zástupných znaků výrazů** pouze `*` a `?` jsou podporovány v výrazy se zástupnými znaky. Například tento výraz `*.vhd` vyhledá všechny soubory s příponou VHD. Obdobně `*.dl?` vyhledá všechny soubory, jejichž přípona je buď `.dl` nebo `.dll`. Navíc `*foo` bude odpovídat všechny soubory, jejichž názvy končí `foo`.<br>Výraz se zástupnými znaky můžete zadat přímo do pole. Ve výchozím nastavení je hodnota zadaná v poli považován výraz se zástupnými znaky.</li><li>**Použití regulárních výrazů** -založených na POSIX regulárních výrazů jsou podporovány. Například regulární výraz `.*\.vhd` bude odpovídat všechny soubory, které mají `.vhd` rozšíření. Regulární výraz, zadejte `<pattern>` přímo jako `regex(<pattern>)`. <li>Další informace o regulárních výrazech, přejděte na [jazyk regulárních výrazů – Stručná referenční příručka](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).</li><ul>|
    |Optimalizace souboru              |Když je povoleno, soubory menší než 1 MB jsou zkomprimována na ingestování. Tím se urychlí kopírování dat pro malé soubory. Úspora času významné se zobrazí, pokud počet souborů, které daleko překračuje počet adresářů.        |
 
4. Klikněte na tlačítko **Start**. Vstupy se ověří a pokud je ověření úspěšné, úlohu spustí. Může trvat několik minut, než se spuštění úlohy.

    ![Spustit úlohu z úlohy konfigurace a spustit dialogové okno](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. Vytvoření úlohy se zadanými nastaveními. Zaškrtněte políčko a potom můžete pozastavit a obnovit, zrušit nebo restartujte úlohu.

    ![Správa úlohy prostřednictvím stránky kopírování dat](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - Tuto úlohu můžete pozastavit, pokud ji je vliv na prostředky serveru NAS během špičky.

        ![Pozastavit úlohu](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        Budete moct pokračovat později v průběhu vypnuto ve špičce hodin.

        ![Obnovení úlohy](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - Úlohu můžete kdykoli zrušit.

        ![Zrušení úlohy](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png) potvrzení se vyžaduje při zrušení úlohy.

        ![Potvrdit zrušení úlohy](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Pokud se rozhodnete zrušit úlohu, data, která ještě není zkopírovaný není odstraněn. Pokud chcete odstranit všechna data, která jste zkopírovali na vaše zařízení Data Box, resetování zařízení.

        ![Resetování zařízení](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Je-li zrušit nebo pozastavit úlohu, velkých souborů, které se kopírují může zůstat polovině zkopírovány. Tyto soubory jsou odeslány ve stejném stavu do Azure. Při pokusu o zrušit nebo pozastavit, ověření se správně kopírování souborů. Pokud chcete ověřit soubory, podívejte se na sdílených složkách protokolu SMB nebo stažení souboru BOM.

    - Úlohu můžete restartovat, pokud se náhle nezdařilo z důvodu přechodné chyby například o síť poruchu. Úlohu nelze restartovat, pokud se po dosažení konečného stavu, jako byla úspěšně dokončena nebo byla dokončena s chybami. Chyby může být způsobeno pojmenovávání souborů nebo problémům s velikostí souboru. Tyto chyby jsou protokolovány ale úlohu nelze restartovat po dokončení.

        ![Restartovat neúspěšnou úlohu](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Pokud se vyskytnou selhání a úlohu nelze restartovat, stáhněte si protokoly chyb a podívejte se na chyby v souborech protokolu. Po opravení problému, můžete vytvořit novou úlohu kopírování souborů. Můžete také [kopírování souborů přes protokol SMB](data-box-deploy-copy-data.md).
    
    - V této verzi nelze odstranit úlohu.
    
    - Můžete vytvářet neomezený počet úloh, ale při paralelním spuštění maximálně 10 úlohy v daném okamžiku.
    - Je-li Optimalizace souborů na, jsou zkomprimována malých souborů na ingestování můžete zvýšit výkon kopírování. V těchto případech se zobrazí komprimovaný soubor (jako název GUID). Neodstraňujte tento soubor jako tento soubor bude rozbalené při nahrávání.

6. Probíhá úloha, na **kopírování dat** stránky:

    - V **stav** sloupce, můžete zobrazit stav úlohy kopírování. Stav může být:
        - **Spuštění**
        - **Se nezdařilo**
        - **Bylo úspěšně dokončeno**
        - **Pozastavení**
        - **Pozastaveno**
        - **Zrušení**
        - **Bylo zrušeno**
        - **Dokončeno s chybami**
    - V **soubory** sloupci se zobrazí počet a velikost celkový počet souborů, které jsou kopírovány.
    - V **zpracované** sloupci se zobrazí počet a velikost souborů, které se zpracovávají.
    - V **podrobnosti** sloupce, klikněte na tlačítko **zobrazení** zobrazíte podrobnosti o úloze.
    - Pokud máte nějaké chyby během kopírování jak je znázorněno **# chyby** sloupce, přejděte na **v protokolu chyb** sloupce a stažení. Chyba protokoly pro řešení potíží s.

Počkejte na dokončení úloh kopírování. Protože některé chyby se protokolují pouze **připojit a Kopírovat** stránky, ujistěte se, že úlohy kopírování dokončili bez chyb, než přejdete k dalšímu kroku.

![Žádné chyby na ** připojit a kopírovat ** stránky](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

Aby se zajistila integrita dat, při kopírování dat se počítá kontrolní součet. Po dokončení kopírování zkontrolujte využité a volné místo na zařízení.
    
![Kontrola volného a využitého místa na řídicím panelu](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

Po dokončení kopírování úlohy můžete přejít na **přípravu k odeslání**.

>[!NOTE]
> Příprava k odeslání nelze spustit, když probíhají úlohy kopírování.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Požadavky
> * Kopírování dat do Data Boxu


Přejděte k dalšímu kurzu se naučíte k odeslání vašeho zařízení Data Box zpět společnosti Microsoft.

> [!div class="nextstepaction"]
> [Odeslání Azure Data Boxu do Microsoftu](./data-box-deploy-picked-up.md)

