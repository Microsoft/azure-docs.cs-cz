---
title: 'Kurz: Kopírování dat do Microsoft Azure Data Box zařízení prostřednictvím služby kopírování dat | Dokumentace Microsoftu'
description: V tomto kurzu se dozvíte, jak ke zkopírování dat do Azure Data Box zařízení prostřednictvím služby kopírování dat
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/24/2019
ms.author: alkohli
ms.openlocfilehash: 3f76721129906b57a05e597aade9f2febb609968
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56343523"
---
# <a name="tutorial-use-the-data-copy-service-to-copy-data-into-azure-data-box-preview"></a>Kurz: Použít službu data kopírování pro kopírování dat do Azure Data Box (preview)

Tento kurz popisuje, jak ingestovat data s využitím služby kopie dat bez zprostředkující hostitele. Službu kopírování dat běží místně na Microsoft Azure Data Box, připojí se k zařízení úložiště připojeného k síti (NAS) prostřednictvím protokolu SMB a kopíruje data do služby Data Box.

Použití služby kopie dat:

- V prostředích NAS kde zprostředkující hostitelé nemusí být k dispozici.
- Pomocí malé soubory, které trvat týdny pro příjem a odesílání data. Službu kopírování dat výrazně zlepšuje dobu příjem a odesílání pro malé soubory.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Kopírování dat do Data Boxu

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste tento kurz: [Nastavení Azure Data Box](data-box-deploy-set-up.md).
2. Vaše zařízení Data Box jste obdrželi a stav objednávky na portálu je **dodáno**.
3. Přihlašovací údaje zdroje NAS zařízení, které se připojíte k pro kopírování dat, které máte.
4. Jste připojeni k vysokorychlostní sítí. Důrazně doporučujeme, abyste měli aspoň jedno připojení Ethernet (10GbE) 10 gigabitů. Pokud není k dispozici 10 GbE připojení, můžete použít odkaz 1 GbE data, ale rychlosti kopírování bude mít vliv.

## <a name="copy-data-to-data-box"></a>Kopírování dat do Data Boxu

Po připojení k zařízení NAS, dalším krokem je si zkopírovat svoje data. Než začnete kopírování dat, projděte si následující aspekty:

- Při kopírování dat, ujistěte se, že velikost dat odpovídá omezení velikosti je popsáno v článku [služby Azure storage a omezení zařízení Data Box](data-box-limits.md).
- Pokud jiné aplikace mimo zařízení Data Box je současně nahrávaných dat odeslaný zařízení Data Box, může způsobit selhání úlohy odeslání a poškození dat.
- Pokud právě upravuje data jako jeho čtení služby kopie dat, může se zobrazit chyby nebo poškození dat.

Pro kopírování dat pomocí služby kopie dat, je potřeba vytvořit úlohu:

1. V místním webovém uživatelském rozhraní vašeho zařízení Data Box, přejděte na **spravovat** > **kopírování dat**.
2. Na **kopírování dat** stránce **vytvořit**.

    ![Vyberte vytvořit na stránce "Kopírování dat"](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. V **úlohy konfigurace a spuštění** dialogové okno, zadejte následující pole:
    
    |Pole                          |Hodnota    |
    |-------------------------------|---------|
    |**Název úlohy**                       |Jedinečný název méně než 230 znaků pro úlohu. V názvu úlohy nejsou povolené tyto znaky: \<, \>, \|, \?, \*, \\, \:, \/, a \\\.         |
    |**Umístění zdroje**                |Zadejte cestu k protokolu SMB pro zdroj dat ve formátu: `\\<ServerIPAddress>\<ShareName>` nebo `\\<ServerName>\<ShareName>`.        |
    |**Uživatelské jméno**                       |Uživatelské jméno v `\\<DomainName><UserName>` formát pro přístup ke zdroji dat.        |
    |**Heslo**                       |Heslo pro přístup ke zdroji dat           |
    |**Cílový účet úložiště**    |Vyberte cílový účet úložiště k nahrání dat do seznamu.         |
    |**Cílový typ**       |Vyberte typ cílového úložiště ze seznamu: **Objekt Blob bloku**, **objektů Blob stránky**, nebo **soubory Azure**.        |
    |**Cílový kontejner a sdílet**    |Zadejte název kontejneru nebo sdílet, že chcete nahrát data v účtu cílového úložiště. Název může obsahovat název sdílené složky ani název kontejneru. Například použít `myshare` nebo `mycontainer`. Můžete také zadat název ve formátu `sharename\directory_name` nebo `containername\virtual_directory_name`.        |
    |**Zkopírujte soubory odpovídající vzoru**    | Můžete zadat vzor odpovídající název souboru v následujících dvou způsobů:<ul><li>**Použití zástupných znaků výrazů:** Pouze `*` a `?` jsou podporovány v výrazy se zástupnými znaky. Například výraz `*.vhd` vyhledá všechny soubory, které mají `.vhd` rozšíření. Obdobně `*.dl?` vyhledá všechny soubory s příponou buď `.dl` nebo, které spouštějí `.dl`, jako například `.dll`. Obdobně `*foo` vyhledá všechny soubory, jejichž názvy končí `foo`.<br>Výraz se zástupnými znaky můžete zadat přímo do pole. Ve výchozím nastavení je hodnota, kterou zadáte v poli považován výraz se zástupnými znaky.</li><li>**Použijte regulární výrazy:** Na základě POSIX regulární výrazy nejsou podporovány. Například regulární výraz `.*\.vhd` bude odpovídat všechny soubory, které mají `.vhd` rozšíření. Regulární výrazy, zadejte `<pattern>` přímo jako `regex(<pattern>)`. Další informace o formátování regulárních výrazů, přejděte na [jazyk regulárních výrazů – Stručná referenční příručka](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).</li><ul>|
    |**Optimalizace souborů**              |Pokud je tato funkce povolena, jsou při ingestování zkomprimována soubory menší než 1 MB. Tento balení urychlí kopírování dat pro malé soubory. Také ukládá významné množství času, pokud počet souborů, které úplně překročí počet adresářů.        |
 
4. Vyberte **Start**. Vstupy se ověří a pokud je ověření úspěšné, pak spustí úloha. Může trvat několik minut, než se spuštění úlohy.

    ![Spustit úlohu v dialogovém okně "úlohy konfigurace a spuštění"](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. Vytvoření úlohy se zadanými nastaveními. Můžete pozastavit, obnovit, zrušit nebo restartujte úlohu. Zaškrtněte políčko vedle názvu úlohy a pak klikněte na příslušné tlačítko.

    ![Spravovat úlohy na stránce "Kopírování dat"](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - Úlohy můžete pozastavit, pokud to má vliv na prostředky zařízení NAS během špičky:

        ![Pozastavit úlohu na stránce "Kopírování dat"](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        Budete moct pokračovat později během hodiny mimo špičku:

        ![Obnovení úlohy na stránce "Kopírování dat"](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - Kdykoli můžete zrušit úlohu:

        ![Zrušení úlohy na stránce "Kopírování dat"](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)
        
        Při zrušení úlohy je vyžadován potvrzení:

        ![Potvrdit zrušení úlohy](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Pokud se rozhodnete zrušit úlohu, data, která ještě není zkopírovaný není odstraněn. Pokud chcete odstranit všechna data, která jste zkopírovali do vašeho zařízení Data Box, resetování zařízení.

        ![Resetování zařízení](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Je-li zrušit nebo pozastavit úlohu, velké soubory je možné jenom částečně zkopírovat. Tyto částečně zkopírované soubory se nahrávají ve stejném státě do Azure. Když zrušit nebo pozastavit úlohu, ujistěte se, že vaše soubory správně zkopírovaly. Pokud chcete ověřit soubory, podívejte se na sdílených složkách protokolu SMB nebo stažení souboru BOM.

    - Úlohu můžete restartovat, pokud se nezdařilo z důvodu přechodné chyby, jako je například síť poruchu. Úlohu nelze restartovat, pokud se po dosažení stavu terminálu, jako například, ale **Succeeded** nebo **byla dokončena s chybami**. Selhání úlohy mohou způsobovat problémy pojmenovávání souborů nebo velikosti souboru. Tyto chyby se protokolují, ale úlohu nelze restartovat po jeho dokončení.

        ![Restartovat neúspěšnou úlohu](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Pokud dochází k chybě a nelze restartovat úlohu, stáhněte si protokoly chyb a vyhledat chyby v souborech protokolu. Poté, co jste opravili problém, vytvořte novou úlohu kopírování souborů. Můžete také [kopírování souborů přes protokol SMB](data-box-deploy-copy-data.md).
    
    - V této verzi nelze odstranit úlohu.
    
    - Můžete vytvářet neomezený počet úloh, ale maximálně 10 úlohy můžou běžet paralelně v daný okamžik.
    - Pokud **Optimalizace souborů** zapnutá, jsou zkomprimována malých souborů na ingestování ke zlepšení výkonu kopírování. V těchto případech uvidíte komprimovaný soubor (bude mít identifikátor GUID jako název souboru). Neodstraňujte tento soubor. Bude možné rozbalit při nahrávání.

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
    - V **soubory** sloupci se zobrazí počet a celkovou velikost souborů, které jsou kopírovány.
    - V **zpracované** sloupci se zobrazí počet a celkovou velikost souborů, které se zpracovávají.
    - V **podrobnosti úlohy** sloupci vyberte **zobrazení** zobrazíte podrobnosti o úloze.
    - Pokud dojde k nějaké chybě během kopírování, jak je znázorněno **# chyby** sloupce, přejděte na **v protokolu chyb** sloupce a stažení. Chyba protokoly pro řešení potíží s.

Počkejte na dokončení úlohy kopírování. Protože některé chyby se protokolují pouze na **připojit a Kopírovat** stránky, ujistěte se, že úloha kopírování byla dokončena bez chyb, než přejdete k dalšímu kroku.

![Žádné chyby na stránce "Připojit a kopírovat"](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

K zajištění integrity dat, kontrolní součet je vypočítaný vloženě, jak se data kopírují. Jakmile se kopírování dokončí, vyberte **zobrazit řídicí panel** ověření využité místo a volné místo na vašem zařízení.
    
![Kontrola volného a využitého místa na řídicím panelu](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

Po dokončení kopírování úlohy můžete vybrat **přípravu k odeslání**.

>[!NOTE]
> **Příprava k odeslání** nelze spustit, když probíhají úlohy kopírování.

## <a name="next-steps"></a>Další postup

Přejděte k dalšímu kurzu se naučíte k odeslání vašeho zařízení Data Box zpět společnosti Microsoft.

> [!div class="nextstepaction"]
> [Dodávejte vaše zařízení Azure Data Box společnosti Microsoft](./data-box-deploy-picked-up.md)

