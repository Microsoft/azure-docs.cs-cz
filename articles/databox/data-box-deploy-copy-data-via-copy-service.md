---
title: 'Kurz: Kopírování do zařízení pomocí služby kopírování dat'
titleSuffix: Azure Data Box
description: V tomto kurzu se dozvíte, jak zkopírovat data do zařízení Azure Data Box prostřednictvím služby kopírování dat
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: ef0d79cae11a382bcca0ddb61e1d4a04b5db41e9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79501874"
---
# <a name="tutorial-use-the-data-copy-service-to-copy-data-into-azure-data-box-preview"></a>Kurz: Kopírování dat do Datové schránky Azure pomocí služby kopírování dat (preview)

Tento kurz popisuje, jak ingestovat data pomocí služby kopírování dat bez zprostředkujícího hostitele. Služba kopírování dat běží místně v datové schránce Microsoft Azure, připojuje se k zařízení úložiště připojenému k síti (NAS) přes SMB a kopíruje data do datové schránky.

Použijte službu kopírování dat:

- V prostředích NAS, kde nemusí být k dispozici zprostředkující hostitelé.
- S malými soubory, které trvá týdny pro požití a nahrávání dat. Služba kopírování dat výrazně zlepšuje dobu při přijím a nahrávání malých souborů.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Kopírování dat do Data Boxu

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste tento kurz: [Nastavení Datové schránky Azure](data-box-deploy-set-up.md).
2. Obdrželi jste zařízení Data Box a stav objednávky na portálu je **Doručeno**.
3. Máte přihlašovací údaje zdrojového zařízení NAS, ke kterému se připojíte pro kopírování dat.
4. Jste připojeni k vysokorychlostní síti. Důrazně doporučujeme, abyste měli alespoň jedno připojení 10 Gigabit Ethernet (GbE). Pokud připojení 10 GbE není k dispozici, můžete použít datové spojení 1 GbE, ale rychlost kopírování bude ovlivněna.

## <a name="copy-data-to-data-box"></a>Kopírování dat do Data Boxu

Po připojení k zařízení NAS je dalším krokem kopírování dat. Než začnete s kopírováním dat, projděte si následující důležité informace:

* Při kopírování dat se ujistěte, že velikost dat odpovídá omezením velikosti popsaným v článku [Azure storage and Data Box limits](data-box-limits.md).

* Pokud data nahraná datovou schránkou jsou současně nahrána jinými aplikacemi mimo Data Box, může dojít k selhání úloh y nahrávání a poškození dat.

* Pokud jsou data upravována při čtení služby kopírování dat, může se zobrazit selhání nebo poškození dat.

* Ujistěte se, že udržujete kopii zdrojových dat, dokud nepotvrdíte, že datová schránka přenesla vaše data do Služby Azure Storage.

Chcete-li kopírovat data pomocí služby kopírování dat, musíte vytvořit úlohu:

1. V místním webovém uživatelském rozhraní zařízení Data Box přejděte na **Spravovat** > **data kopírování**.
2. Na stránce **Kopírovat data** vyberte **Vytvořit**.

    ![Na stránce Kopírovat data vyberte Vytvořit.](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. V dialogovém okně **Konfigurovat úlohu a start** vyplňte následující pole:
    
    |Pole                          |Hodnota    |
    |-------------------------------|---------|
    |**Název úlohy**                       |Jedinečný název méně než 230 znaků pro úlohu. Tyto znaky nejsou v názvu úlohy \> \|povoleny: \\ \< \:, \/, , \? \*, , , , a\\\.         |
    |**Umístění zdroje**                |Zadejte cestu SMB ke zdroji dat `\\<ServerIPAddress>\<ShareName>` `\\<ServerName>\<ShareName>`ve formátu: nebo .        |
    |**Username**                       |Uživatelské jméno `\\<DomainName><UserName>` ve formátu pro přístup ke zdroji dat. Pokud se připojuje místní správce, bude potřebovat explicitní oprávnění zabezpečení. Klepněte pravým tlačítkem myši na složku, vyberte **vlastnosti** a potom vyberte **možnost Zabezpečení**. To by mělo přidat místního správce na kartě **Zabezpečení.**       |
    |**Heslo**                       |Heslo pro přístup ke zdroji dat.           |
    |**Cílový účet úložiště**    |Vyberte cílový účet úložiště, do který chcete ze seznamu nahrát data.         |
    |**Typ cíle**       |Vyberte cílový typ úložiště ze seznamu: **Blok objektů blob**, **Objekt blob stránky**nebo Soubory **Azure**.        |
    |**Cílový kontejner/sdílená**    |Zadejte název kontejneru nebo sdílené složky, do které chcete nahrát data, do cílového účtu úložiště. Název může být název sdílené položky nebo název kontejneru. Můžete například použít `myshare` nebo `mycontainer`. Název můžete také zadat ve `sharename\directory_name` `containername\virtual_directory_name`formátu nebo .        |
    |**Kopírování odpovídajících souborů**    | Vzor pro porovnávání názvů souborů můžete zadat dvěma způsoby:<ul><li>**Používejte zástupné výrazy:** Pouze `*` `?` a jsou podporovány ve zástupných výrazech. Například výraz `*.vhd` odpovídá všem souborům, `.vhd` které mají příponu. Podobně `*.dl?` odpovídá všechny soubory s příponou `.dl` nebo `.dl`které `.dll`začínají , například . Podobně odpovídá `*foo` všechny soubory, jejichž `foo`názvy končí .<br>Do pole můžete přímo zadat zástupný výraz. Ve výchozím nastavení je hodnota, kterou zadáte do pole, považována za zástupný výraz.</li><li>**Používejte regulární výrazy:** Regulární výrazy založené na posix jsou podporovány. Regulární výraz `.*\.vhd` bude například odpovídat `.vhd` všem souborům, které mají příponu. Pro regulární výrazy zadejte `<pattern>` přímo jako `regex(<pattern>)`. Další informace o regulárních výrazech naleznete v části [Jazyk regulárních výrazů – stručný přehled](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).</li><ul>|
    |**Optimalizace souborů**              |Pokud je tato funkce povolena, soubory menší než 1 MB jsou zabaleny během požití. Toto balení urychluje kopírování dat pro malé soubory. Také šetří značné množství času, když počet souborů daleko přesahuje počet adresářů.        |
 
4. Vyberte **Spustit**. Vstupy jsou ověřeny a pokud je ověření úspěšné, pak se spustí úloha. Může trvat několik minut, než začne úloha.

    ![Spuštění úlohy z dialogového okna Konfigurovat úlohu a spuštění](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. Vytvoří se úloha se zadaným nastavením. Úlohu můžete pozastavit, obnovit, zrušit nebo restartovat. Zaškrtněte políčko vedle názvu úlohy a pak zaškrtněte příslušné tlačítko.

    ![Správa úlohy na stránce "Kopírovat data"](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - Úlohu můžete pozastavit, pokud ovlivňuje prostředky zařízení NAS ve špičce:

        ![Pozastavení úlohy na stránce "Kopírovat data"](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        V práci můžete pokračovat později mimo špičku:

        ![Pokračovat v práci na stránce "Kopírovat data"](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - Úlohu můžete kdykoli zrušit:

        ![Zrušení úlohy na stránce Kopírovat data](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)
        
        Při zrušení úlohy je vyžadováno potvrzení:

        ![Potvrdit zrušení úlohy](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Pokud se rozhodnete úlohu zrušit, data, která jsou již zkopírována, nebudou odstraněna. Chcete-li odstranit všechna data, která jste zkopírovali do zařízení Data Box, resetujte zařízení.

        ![Resetování zařízení](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Pokud úlohu zrušíte nebo pozastavíte, mohou být velké soubory zkopírovány pouze částečně. Tyto částečně zkopírované soubory se nahrají ve stejném stavu do Azure. Při zrušení nebo pozastavení úlohy zkontrolujte, zda byly soubory správně zkopírovány. Chcete-li soubory ověřit, podívejte se na sdílené složky SMB nebo stáhněte soubor kusovníku.

    - Úlohu můžete restartovat, pokud se nezdařila z důvodu přechodné chyby, například závady v síti. Úlohu však nelze restartovat, pokud dosáhla stavu terminálu, například **Proběhlo úspěšně** nebo **Dokončeno s chybami**. Selhání úloh může být způsobeno problémy s pojmenováním souboru nebo velikostí souboru. Tyto chyby jsou zaznamenány, ale úloha nelze restartovat po dokončení.

        ![Restartování neúspěšné úlohy](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Pokud dojde k selhání a nelze restartovat úlohu, stáhněte protokoly chyb a vyhledejte selhání v souborech protokolu. Po opravě problému vytvořte novou úlohu pro kopírování souborů. Můžete také [zkopírovat soubory přes SMB](data-box-deploy-copy-data.md).
    
    - V této verzi nelze odstranit úlohu.
    
    - Můžete vytvořit neomezené úlohy, ale můžete spustit pouze maximálně 10 úloh paralelně najednou.
    - Pokud je **optimalizace souborů zapnutá,** malé soubory jsou zabaleny při ingestování, aby se zlepšil výkon kopírování. V těchto případech se zobrazí zabalený soubor (bude mít jako název souboru identifikátor GUID). Neodstraňujte tento soubor. Během nahrávání bude rozbalen.

6. Během probíhající úlohy na stránce **Kopírovat data:**

    - Ve sloupci **Stav** můžete zobrazit stav úlohy kopírování. Stav může být:
        - **Spuštěno**
        - **Failed**
        - **Úspěch**
        - **Pozastaveno**
        - **Pozastaveno**
        - **Probíhá zrušení**
        - **Zrušeno**
        - **Dokončeno s chybami**
    - Ve sloupci **Soubory** uvidíte číslo a celkovou velikost kopírovaných souborů.
    - Ve **sloupci Zpracované** můžete zobrazit číslo a celkovou velikost zpracovávaných souborů.
    - Ve sloupci **Podrobnosti úlohy** vyberte **Zobrazit,** chcete-li zobrazit podrobnosti o úloze.
    - Pokud během procesu kopírování dojde k chybám, jak je znázorněno ve sloupci **# Chyby,** přejděte do sloupce **Protokol chyb** a stáhněte protokoly chyb pro řešení potíží.

Počkejte na dokončení úlohy kopírování. Vzhledem k tomu, že některé chyby jsou zaznamenány pouze na stránce **Připojit a kopírovat,** ujistěte se, že úloha kopírování byla dokončena bez chyb, než přejdete k dalšímu kroku.

![Žádné chyby na stránce "Připojit a kopírovat"](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

Aby byla zajištěna integrita dat, kontrolní součet se vypočítá v aktivní při kopírování dat. Po dokončení kopírování vyberte **Zobrazit řídicí panel** a ověřte využité místo a volné místo v zařízení.
    
![Kontrola volného a využitého místa na řídicím panelu](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

Po dokončení úlohy kopírování můžete vybrat **možnost Připravit k odeslání**.

>[!NOTE]
> **Příprava k odeslání** nelze spustit, zatímco probíhá kopírování úloh.

## <a name="next-steps"></a>Další kroky

Přejdete k dalšímu kurzu, kde se dozvíte, jak zařízení Data Box poslat zpět do Microsoftu.

> [!div class="nextstepaction"]
> [Odeslání zařízení Azure Data Box společnosti Microsoft](./data-box-deploy-picked-up.md)

