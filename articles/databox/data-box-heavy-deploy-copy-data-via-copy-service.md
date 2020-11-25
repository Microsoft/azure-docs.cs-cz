---
title: 'Kurz: kopírování dat do Azure Data Box Heavy prostřednictvím služby kopírování dat'
description: V tomto kurzu se dozvíte, jak kopírovat data do zařízení Azure Data Box Heavy prostřednictvím služby kopírování dat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 601e5cf15c47b16c53ff9ca81a56cb613bcfc3f5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006555"
---
# <a name="tutorial-use-the-data-copy-service-to-copy-data-into-azure-data-box-heavy-preview"></a>Kurz: použití služby kopírování dat ke kopírování dat do Azure Data Box Heavy (Preview)

V tomto kurzu se dozvíte, jak ingestovat data pomocí služby kopírování dat bez zprostředkujícího hostitele. Služba kopírování dat běží místně na Azure Data Box Heavy, připojuje se k zařízení úložiště připojenému k síti (NAS) přes SMB a kopíruje data do Data Box Heavy.

Použijte službu kopírování dat:

- V prostředích NAS, kde nemusí být k dispozici zprostředkující hostitelé.
- S malými soubory, které berou v úvahu týdny pro příjem a nahrávání dat. Služba kopírování dat významně vylepšuje přijímání a dobu nahrávání malých souborů.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Kopírování dat do Data Boxu Heavy

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste tento kurz: [nastavte Azure Data box Heavy](data-box-heavy-deploy-set-up.md).
2. Obdrželi jste Data Box Heavy a stav objednávky na portálu je **Doručeno**.
3. Máte přihlašovací údaje zdrojového zařízení NAS, ke kterému se připojíte pro kopírování dat.
4. Jste připojení k síti s vysokou rychlostí. Největší rychlosti kopírování je možné dosáhnout použitím dvou paralelních připojení 40 GbE (jedno na uzel). Pokud nemáte k dispozici připojení 40 GbE, doporučujeme použít alespoň dvě připojení 10 GbE (jedno na uzel). 

## <a name="copy-data-to-data-box-heavy"></a>Kopírování dat do Data Boxu Heavy

Až budete připojeni k zařízení se serverem NAS, je dalším krokem kopírování vašich dat. Než začnete s kopírováním dat, projděte si následující důležité informace:

- Při kopírování dat se ujistěte, že velikost dat odpovídá limitům velikosti popsaným v článku [úložiště Azure a omezení data box Heavy](data-box-heavy-limits.md).
- Pokud jsou data nahraná pomocí Data Box Heavy souběžně nahraná jinými aplikacemi mimo Data Box Heavy, může to vést k selhání úloh a poškození dat.
- Pokud se data mění, protože ji služba kopírování dat čte, můžou se zobrazit chyby nebo poškození dat.

Chcete-li kopírovat data pomocí služby kopírování dat, je třeba vytvořit úlohu:

1. V místním webovém uživatelském rozhraní zařízení s data box Heavy můžete přejít na **Správa**  >  **kopírování dat**.
2. Na stránce **Kopírovat data** vyberte **vytvořit**.

    ![Na stránce kopírovat data vyberte vytvořit.](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. V dialogovém okně **Konfigurovat úlohu a spustit** vyplňte následující pole:
    
    |Pole                          |Hodnota    |
    |-------------------------------|---------|
    |**Název úlohy**                       |Jedinečný název pro úlohu je kratší než 230 znaků. Tyto znaky nejsou povoleny v názvu úlohy: \<, \> , \| , \? , \* , \\ , \: , \/ a \\\.         |
    |**Umístění zdroje**                |Zadejte cestu SMB ke zdroji dat ve formátu: `\\<ServerIPAddress>\<ShareName>` nebo `\\<ServerName>\<ShareName>` .        |
    |**Uživatelské jméno**                       |Uživatelské jméno ve `\\<DomainName><UserName>` formátu pro přístup ke zdroji dat. Pokud se připojujete k místnímu správci, budou potřebovat explicitní oprávnění zabezpečení. Klikněte pravým tlačítkem na složku, vyberte **vlastnosti** a pak vyberte **zabezpečení**. Měli byste přidat místního správce na kartě **zabezpečení** .       |
    |**Heslo**                       |Heslo pro přístup ke zdroji dat.           |
    |**Cílový účet úložiště**    |Vyberte cílový účet úložiště, do kterého se mají nahrát data ze seznamu.         |
    |**Cílový typ**       |V seznamu vyberte typ cílového úložiště: **objekt blob bloku**, **objekt blob stránky** nebo **soubory Azure**.        |
    |**Cílový kontejner/sdílená složka**    |Do cílového účtu úložiště zadejte název kontejneru nebo sdílené složky, do které chcete data nahrát. Název může být název sdílené složky nebo název kontejneru. Můžete například použít `myshare` nebo `mycontainer`. Můžete také zadat název ve formátu `sharename\directory_name` nebo `containername\virtual_directory_name` .        |
    |**Kopírovat soubory, které odpovídají vzoru**    | Následující dva způsoby můžete zadat podle názvu souboru a odpovídajícího vzoru:<ul><li>**Použít výrazy se zástupnými znaky:** `*` `?` Ve výrazech se zástupnými znaky jsou podporovány pouze a. Například výraz `*.vhd` odpovídá všem souborům, které mají `.vhd` rozšíření. Podobně se `*.dl?` shoduje se všemi soubory buď s příponou, `.dl` nebo začínající `.dl` , například `.dll` . Podobně `*foo` odpovídá všem souborům, jejichž názvy končí na `foo` .<br>Zástupný výraz můžete přímo zadat do pole. Ve výchozím nastavení je hodnota, kterou zadáte do pole, považována za zástupný výraz.</li><li>**Použít regulární výrazy:** Jsou podporovány regulární výrazy založené na standardu POSIX. Například regulární výraz `.*\.vhd` bude odpovídat všem souborům, které mají `.vhd` rozšíření. Pro regulární výrazy poskytněte `<pattern>` přímo jako `regex(<pattern>)` . Další informace o regulárních výrazech naleznete v [jazyce regulárních výrazů – stručná referenční](/dotnet/standard/base-types/regular-expression-language-quick-reference)dokumentace.</li><ul>|
    |**Optimalizace souborů**              |Když je tato funkce povolená, během příjmu se zabalí soubory menší než 1 MB. Toto balení zrychluje kopírování dat pro malé soubory. Šetří také významné množství času, kdy počet souborů mnohem překračuje počet adresářů.        |
 
4. Vyberte **Spustit**. Vstupy jsou ověřeny a v případě úspěšného ověření se spustí úloha. Spuštění úlohy může trvat několik minut.

    ![Spuštění úlohy z dialogového okna Konfigurovat úlohu a spustit](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. Vytvoří se úloha se zadaným nastavením. Úlohu můžete pozastavit, obnovit, zrušit nebo restartovat. Zaškrtněte políčko vedle názvu úlohy a pak vyberte příslušné tlačítko.

    ![Správa úlohy na stránce kopírovat data](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - Úlohu můžete pozastavit, pokud má vliv na prostředky zařízení NAS v době špičky:

        ![Pozastavení úlohy na stránce kopírovat data](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        Úlohu můžete obnovit později v době mimo špičku:

        ![Pokračování úlohy na stránce kopírovat data](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - Úlohu můžete kdykoli zrušit:

        ![Zrušení úlohy na stránce kopírovat data](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)
        
        Při zrušení úlohy se vyžaduje potvrzení:

        ![Potvrdit zrušení úlohy](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Pokud se rozhodnete úlohu zrušit, již kopírovaná data nebudou odstraněna. Pokud chcete všechna data, která jste zkopírovali do zařízení Data Box, odstranit, resetujte zařízení.

        ![Resetování zařízení](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Pokud úlohu zrušíte nebo pozastavíte, mohou být velké soubory pouze částečně zkopírovány. Tyto částečně zkopírované soubory se nahrají do Azure do stejného stavu. Když úlohu zrušíte nebo pozastavíte, ujistěte se, že byly soubory správně zkopírované. Chcete-li ověřit soubory, podívejte se na sdílené složky SMB nebo si stáhněte soubor BOM.

    - Úlohu můžete restartovat, pokud se nezdařila z důvodu přechodné chyby, jako je například porucha sítě. Nemůžete ale úlohu znovu spustit, pokud se dostal stav terminálu, například **úspěch** nebo **dokončeno s chybami**. Selhání úlohy může být způsobeno problémy s pojmenovávání souborů nebo velikostí souborů. Tyto chyby jsou protokolovány, ale úlohu po dokončení nelze restartovat.

        ![Restartuje neúspěšnou úlohu.](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Pokud dojde k chybě a nemůžete úlohu restartovat, Stáhněte protokoly chyb a vyhledejte chybu v souborech protokolu. Po opravě problému vytvořte novou úlohu pro zkopírování souborů. Soubory můžete také [Kopírovat přes protokol SMB](data-box-deploy-copy-data.md).
    
    - V této verzi nemůžete úlohu odstranit.
    
    - Můžete vytvářet neomezený počet úloh, ale souběžně můžete spustit maximálně 10 úloh najednou.
    - Pokud je zapnutá **Optimalizace souborů** , malé soubory se zabalí do ingestování, aby se zlepšil výkon při kopírování. V těchto případech se zobrazí sbalený soubor (bude mít jako název souboru identifikátor GUID). Tento soubor neodstraňujte. Během nahrávání se nebalí.

6. Zatímco probíhá úloha, na stránce **Kopírovat data** :

    - Ve sloupci **stav** můžete zobrazit stav úlohy kopírování. Stav může být následující:
        - **Spuštěný**
        - **Neúspěšný**
        - **Úspěšný**
        - **Pozastaveno**
        - **Pozastaveno**
        - **Probíhá zrušení**
        - **Zrušeno**
        - **Dokončeno s chybami**
    - Ve sloupci **soubory** můžete zobrazit počet a celkovou velikost kopírovaných souborů.
    - Ve sloupci **zpracované** můžete zobrazit počet a celkovou velikost zpracovávaných souborů.
    - Ve sloupci **Podrobnosti úlohy** vyberte **Zobrazit** a zobrazte podrobnosti o úloze.
    - Pokud během kopírování dojde k nějakým chybám, jak je znázorněno ve sloupci **# Errors** , přejít do sloupce **protokolu chyb** a Stáhněte si protokoly chyb pro řešení potíží.

Počkejte, než se úloha kopírování dokončí. Vzhledem k tomu, že se některé chyby protokolují jenom na stránce **připojit a kopírovat** , ujistěte se, že se úloha kopírování nedokončila bez chyb, než přejdete k dalšímu kroku.

![Na stránce připojit a kopírovat nejsou žádné chyby.](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

Aby se zajistila integrita dat, kontrolní součet se vypočítává jako vložený při kopírování dat. Po dokončení kopírování vyberte **zobrazit řídicí panel** , abyste ověřili využité místo a volné místo na vašem zařízení.
    
![Kontrola volného a využitého místa na řídicím panelu](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

> [!IMPORTANT]
> Opakujte stejné pokyny ke zkopírování dat do druhého uzlu na Data Box Heavy.

Po dokončení úlohy kopírování můžete vybrat **Příprava k odeslání**.

>[!NOTE]
> **Příprava k odeslání** nemůže běžet během probíhajících úloh kopírování.

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu kurzu, kde se dozvíte, jak odeslat Data Box Heavy zařízení zpátky do Microsoftu.

> [!div class="nextstepaction"]
> [Odeslání zařízení Azure Data Box Heavy Microsoftu](./data-box-heavy-deploy-picked-up.md)