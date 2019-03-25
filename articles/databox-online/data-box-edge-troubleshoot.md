---
title: Řešení potíží s Azure Data Box Edge pomocí webu Azure portal | Dokumentace Microsoftu
description: Popisuje postup řešení potíží s Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/15/2019
ms.author: alkohli
ms.openlocfilehash: af72bc4ced556b8ea7493e4100b0ab06931e8e2d
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58405984"
---
# <a name="troubleshoot-your-azure-data-box-edge-issues"></a>Řešení problémů Azure Data Box Edge 

Tento článek popisuje postup řešení potíží na hranici vaší Azure Data Box. 

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Spuštění diagnostiky
> * Shromáždění balíčku pro podporu
> * Použití protokolů k řešení potíží


## <a name="run-diagnostics"></a>Spuštění diagnostiky

Pokud chcete diagnostikovat a vyřešit potíže s libovolnými chybami zařízení, můžete spustit diagnostické testy. Diagnostické testy spustíte pomocí následujících kroků v místním webovém uživatelském rozhraní vašeho zařízení.

1. V místním webovém uživatelském rozhraní přejděte na **Řešení potíží > Diagnostické testy**. Vyberte test, který chcete spustit a klikněte na tlačítko **Spustit test**. Tím se spustí testy, které provedou diagnostiku všech možných problémů se sítí, zařízením, webovým proxy serverem, časem nebo nastavením cloudu. Na spuštěné testy v zařízení budete upozorněni.

    ![Vyberte testy](media/data-box-edge-troubleshoot/run-diag-1.png)
 
2. Po dokončení testů se zobrazí výsledky. 

    ![Kontrola výsledků testu](media/data-box-edge-troubleshoot/run-diag-2.png)

    Pokud bude test neúspěšný, zobrazí se adresa URL s doporučenou akcí. Můžete na adresu URL kliknout a doporučenou akci zobrazit.
 
    ![Zkontrolujte varování pro neúspěšné testy](media/data-box-edge-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>Shromáždění balíčku pro podporu

Balíček protokolů se skládá z příslušných protokolů, které mohou podpoře Microsoftu pomoci s řešením potíží na libovolném zařízení. Balíček protokolů můžete vygenerovat prostřednictvím místního webového uživatelského rozhraní.

Pokud chcete balíček pro podporu vyzvednout, proveďte následující kroky. 

1. V místním webovém uživatelském rozhraní přejděte na **Řešení potíží > Podpora**. Klikněte na **Vytvořit balíček pro podporu**. Systém začne shromažďovat balíček pro podporu. Shromažďování balíčků může několik minut trvat.

    ![Přidání uživatele kliknutím](media/data-box-edge-troubleshoot/collect-logs-1.png)
 
2. Po vytvoření balíčku pro podporu klikněte na **Stáhnout balíček pro podporu**. Komprimovaný balíček se stáhne do vámi vybrané cesty. Balíček můžete rozbalit a zobrazit soubory systémového protokolu.

    ![Přidání uživatele kliknutím](media/data-box-edge-troubleshoot/collect-logs-2.png)

## <a name="use-logs-to-troubleshoot"></a>Použití protokolů k řešení potíží

Veškeré chyby, ke kterým během procesu nahrávání a aktualizace došlo, budou součástí příslušných chybových souborů.

1. Pokud chcete chybové soubory zobrazit, přejděte do sdílené složky a kliknutím na ni obsah zobrazte. 

      ![Připojte se k a zobrazit obsah sdílené složky](media/data-box-edge-troubleshoot/troubleshoot-logs-1.png)

2. Klikněte na tlačítko _složce Microsoft Data Box Edge_. Tato složka obsahuje dvě podsložky:

    - Složku nahrávání, která obsahuje soubory protokolů s chybami nahrávání.
    - Složku aktualizací s chybami, ke kterým došlo během aktualizace.

    Tady je ukázkový soubor protokolu pro aktualizace.

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. Když v tomto souboru uvidíte chybu (zvýrazněné v ukázce), poznamenejte si kód chyby – v tomto případě je to 16001. Vyhledejte popis tohoto kódu chyby v následující referenci k chybám.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]


## <a name="next-steps"></a>Další postup

- Další informace o [známých potížích v této verzi](data-box-gateway-release-notes.md).
