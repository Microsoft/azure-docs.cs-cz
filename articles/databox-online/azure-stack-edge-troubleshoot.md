---
title: Použijte Azure Portal k řešení potíží s Azure Stack Edge pro | Microsoft Docs
description: Naučte se řešit problémy s Azure Stack Edge pro. Můžete spustit diagnostiku, shromažďovat informace pro podporu a k řešení potíží používat protokoly.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 01/21/2021
ms.author: alkohli
ms.openlocfilehash: e6fd69d7016a364d92e1873e0df1521a27612b79
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791472"
---
# <a name="troubleshoot-your-azure-stack-edge-pro-issues"></a>Řešení potíží s Azure Stack Edge pro

Tento článek popisuje, jak řešit problémy s Azure Stack Edge pro. 

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Spuštění diagnostiky
> * Shromáždění balíčku pro podporu
> * Použití protokolů k řešení potíží
> * Řešení chyb IoT Edge

## <a name="run-diagnostics"></a>Spuštění diagnostiky

Pokud chcete diagnostikovat a vyřešit potíže s libovolnými chybami zařízení, můžete spustit diagnostické testy. Diagnostické testy spustíte pomocí následujících kroků v místním webovém uživatelském rozhraní vašeho zařízení.

1. V místním webovém uživatelském rozhraní přejděte na **Řešení potíží > Diagnostické testy**. Vyberte test, který chcete spustit, a vyberte **Spustit test**. Tím se spustí testy, které provedou diagnostiku všech možných problémů se sítí, zařízením, webovým proxy serverem, časem nebo nastavením cloudu. Zobrazí se upozornění, že zařízení spouští testy.

    ![Vybrat testy](media/azure-stack-edge-troubleshoot/run-diag-1.png)

2. Po dokončení testů se zobrazí výsledky.

    ![Kontrola výsledků testu](media/azure-stack-edge-troubleshoot/run-diag-2.png)

    Pokud bude test neúspěšný, zobrazí se adresa URL s doporučenou akcí. Vyberte adresu URL pro zobrazení doporučené akce.

    ![Zkontrolovat upozornění pro neúspěšné testy](media/azure-stack-edge-troubleshoot/run-diag-3.png)

## <a name="collect-support-package"></a>Shromáždění balíčku pro podporu

Balíček protokolů se skládá z příslušných protokolů, které mohou podpoře Microsoftu pomoci s řešením potíží na libovolném zařízení. Balíček protokolů můžete vygenerovat prostřednictvím místního webového uživatelského rozhraní.

Pokud chcete balíček pro podporu vyzvednout, proveďte následující kroky.

1. V místním webovém uživatelském rozhraní přejděte na **Řešení potíží > Podpora**. Vyberte **vytvořit balíček pro podporu**. Systém začne shromažďovat balíček pro podporu. Shromažďování balíčků může několik minut trvat.

    ![Přidání uživatele kliknutím](media/azure-stack-edge-troubleshoot/collect-logs-1.png)

2. Po vytvoření balíčku pro podporu vyberte **Stáhnout balíček pro podporu**. Komprimovaný balíček se stáhne do vámi vybrané cesty. Balíček můžete rozbalit a zobrazit soubory systémového protokolu.

    ![Klikněte na Přidat uživatele 2.](media/azure-stack-edge-troubleshoot/collect-logs-2.png)

## <a name="use-logs-to-troubleshoot"></a>Použití protokolů k řešení potíží

Veškeré chyby, ke kterým během procesu nahrávání a aktualizace došlo, budou součástí příslušných chybových souborů.

1. Soubory chyb zobrazíte tak, že přejdete do sdílené složky a vyberete ji pro zobrazení obsahu. 

      ![Připojení a zobrazení obsahu sdílení](media/azure-stack-edge-troubleshoot/troubleshoot-logs-1.png)

2. Vyberte _složku Microsoft Azure Stack Edge pro_. Tato složka obsahuje dvě podsložky:

    * Složku nahrávání, která obsahuje soubory protokolů s chybami nahrávání.
    * Složku aktualizací s chybami, ke kterým došlo během aktualizace.

    Tady je ukázkový soubor protokolu pro aktualizace.

    ```xml
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ```

3. Pokud se v tomto souboru zobrazí chyba (zvýrazněná v ukázce), poznamenejte si kód chyby, v tomto případě je to 16001. Vyhledejte popis tohoto kódu chyby v následující referenci k chybám.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]

## <a name="troubleshoot-iot-edge-errors"></a>Řešení chyb IoT Edge

[!INCLUDE [Troubleshoot IoT Edge runtime](../../includes/azure-stack-edge-iot-troubleshoot-compute.md)]

## <a name="next-steps"></a>Další kroky

* Další informace o [známých potížích v této verzi](../databox-gateway/data-box-gateway-release-notes.md).