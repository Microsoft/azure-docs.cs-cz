---
title: Připojení dat ICDx společnosti Symantec k Azure Sentinelu| Dokumenty společnosti Microsoft
description: Přečtěte si, jak připojit data IcDx společnosti Symantec k Azure Sentinelu.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: cac63aee5f9ebf3859b138e6444e40b1e2dd30f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588089"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Připojte zařízení Symantec ICDx 



Konektor ICDx společnosti Symantec umožňuje snadno propojit všechny protokoly řešení zabezpečení společnosti Symantec s azure sentinelem, zobrazit řídicí panely, vytvořit vlastní výstrahy a zlepšit vyšetřování. Získáte tak lepší přehled o síti vaší organizace a zlepšíte možnosti operací zabezpečení. Integrace mezi symantec ICDx a Azure Sentinel využívá rozhraní REST API.


> [!NOTE]
> Data se budou ukládat v geografickém umístění pracovního prostoru, ve kterém používáte Azure Sentinel.

## <a name="configure-and-connect-symantec-icdx"></a>Konfigurace a připojení programu Symantec ICDx 

Symantec ICDx můžete integrovat a exportovat protokoly přímo do Azure Sentinelu.

1. Otevřete konzolu pro správu ICDx a přidejte servery pro předávání Microsoft Azure Sentinel (Log Analytics).
2. Na navigačním panelu ICDx klepněte na tlačítko **Konfigurace**. 
3. V horní části obrazovky **Konfigurace** klikněte na **Možnost Předávání .**
4. V části **Předávání**stránek klikněte vedle Microsoft Azure Sentinel (Log Analytics) na **Přidat**. 
4. V okně **Microsoft Azure Sentinel (Log Analytics)** klikněte na **Zobrazit upřesnit**. 
5. V horní části rozšířeného do okna Microsoft Azure Sentinel (Log Analytics) postupujte takto:
    -   **Název**: Zadejte název pro předávání, který nemá více než 30 znaků. Vyberte jedinečný a smysluplný název. Tento název se zobrazí v seznamu předaných položek na obrazovce **Konfigurace** a na řídicích panelech na obrazovce **Řídicí panel.** Například: Microsoft Azure Log Analytics východ. Toto pole je vyžadováno.
    -   **Popis**: Zadejte popis pro předávání. Tento popis se také zobrazí v seznamu předávání na obrazovce **Konfigurace.** Uveďte podrobnosti, jako je například typ události předávaný a skupina, která potřebuje zkontrolovat data.
    -   **Typ spuštění**: Vyberte metodu spuštění konfigurace serveru pro předávání. Vaše možnosti jsou manuální a automatické.<br>Výchozí hodnota je Automaticky. 
6. V části **Události**postupujte takto: 
    - **Zdroj**: Vyberte jeden nebo více archivů, ze kterých chcete předávat události. Můžete vybrat aktivní archivy sběratelů (včetně Společného archivu), osamocené archivy sběratelů (tj. archivy pro kolektory, které jste odstranili), archivy přijímačů ICDx nebo systémový archiv. <br>Výchozí hodnota je Společný archiv.
      > [!NOTE]
      > ICDx přijímač archivy jsou uvedeny samostatně, podle názvu. 
 
    - **Filtr**: Přidejte filtr, který určuje podmnožinu událostí, které mají být předány dál. Proveďte jednu z těchto akcí:
        - Chcete-li vybrat podmínku filtru, klepněte na typ, atribut, operátor a hodnotu. 
        - V poli Filtr zkontrolujte podmínku filtru. Můžete jej upravit přímo v poli nebo jej podle potřeby odstranit.
        - Kliknutím na tlačítko A nebo OR přidejte do stavu filtru.
        - Můžete také kliknout na uložené dotazy a použít uložený dotaz.
    - **Zahrnuté atributy**: Zadejte seznam atributů oddělených čárkami, který chcete zahrnout do předávaných dat. Zahrnuté atributy mají přednost před vyloučenými atributy.
    - **Vyloučené atributy**: Zadejte seznam atributů oddělených čárkami, který chcete vyloučit z předávaných dat.
    - **Velikost dávky**: Vyberte počet událostí, které mají být odeslány na dávku. Vaše možnosti jsou 10, 50, 100, 500 a 1000.<br>Výchozí hodnota je 100. 
    - **Limit rychlosti**: Vyberte rychlost, s jakou jsou události předávány, vyjádřené jako události za sekundu. Vaše možnosti jsou Neomezené, 500, 1000, 5000, 10000. <br> Výchozí hodnota je 5000. 
7. V části **Azure Destination**postupujte takto: 
    - **ID pracovního prostoru**: Vložte ID pracovního prostoru zespodu. Toto pole je vyžadováno.
    - **Primární klíč**: Vložte primární klíč zespodu. Toto pole je vyžadováno.
    - **Vlastní název protokolu**: Do pracovního prostoru Log Analytics portálu Microsoft Azure zadejte vlastní název protokolu, do kterého budete předávat události. Výchozí hodnota je SymantecICDx. Toto pole je vyžadováno.
8. Chcete-li dokončit konfiguraci serveru pro předávání, klepněte na tlačítko *Uložit.* 
9. Chcete-li spustit předávání, klikněte v části **Možnosti**na **další** a potom na **tlačítko Start**.
10. Chcete-li použít příslušné schéma v log analytics pro události IcDx společnosti Symantec, vyhledejte **SymantecICDx_CL**.


## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat více než 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 



## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit symantec ICDx k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).
- Ke sledování dat [použijte sešity.](tutorial-monitor-your-data.md)


