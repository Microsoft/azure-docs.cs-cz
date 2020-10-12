---
title: Připojení dat Symantec ICDx ke službě Azure Sentinel | Microsoft Docs
description: Naučte se používat konektor Symantec ICDx ke snadnému připojení všech protokolů řešení zabezpečení od Symantecu k Azure Sentinel.
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
ms.openlocfilehash: 67fc80b5f34cf3a98fd39ddc352cb2dd9a5e7151
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85564913"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Připojení zařízení Symantec ICDx 



Konektor Symantec ICDx vám umožňuje snadno propojit všechny protokoly řešení zabezpečení Symantec s vaší službou Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Získáte tak lepší přehled o síti vaší organizace a zlepšíte možnosti vaší operace zabezpečení. Integrace mezi Symantec ICDx a Azure Sentinel využívá REST API.


> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="configure-and-connect-symantec-icdx"></a>Konfigurace a připojení Symantec ICDx 

Symantec ICDx může integrovat a exportovat protokoly přímo do Azure Sentinel.

1. Otevřete konzolu pro správu ICDx a přidejte nástroje pro přeposílání Sentinel (Log Analytics) Microsoft Azure.
2. Na navigačním panelu ICDx klikněte na **Konfigurace**. 
3. V horní části obrazovky **Konfigurace** klikněte na nástroje **pro přeposílání**.
4. V části **pro předávané**aplikace vedle Microsoft Azure Sentinel (Log Analytics) klikněte na **Přidat**. 
4. V okně **Microsoft Azure Sentinel (Log Analytics)** klikněte na **Zobrazit Upřesnit**. 
5. V horní části okna Rozšířené do Microsoft Azure Sentinel (Log Analytics) proveďte následující kroky:
    -   **Název**: zadejte název pro server pro zakládání, který nemá více než 30 znaků. Vyberte jedinečný, smysluplný název. Tento název se zobrazí v seznamu služeb pro přesměrování na obrazovce **Konfigurace** a na řídicích panelech na obrazovce **řídicího panelu** . Například: Microsoft Azure Log Analytics východ. Toto pole je vyžadováno.
    -   **Popis**: zadejte popis pro server pro překládání. Tento popis se zobrazí také v seznamu služeb pro přesměrování na obrazovce **Konfigurace** . Zahrňte podrobnosti, jako je předávaný typ události, a skupinu, která potřebuje data kontrolovat.
    -   **Typ spuštění**: Vyberte metodu spuštění pro konfiguraci služby pro přeposílání. Vaše možnosti jsou ruční a automatické.<br>Výchozí hodnota je automatická. 
6. V části **události**proveďte následující: 
    - **Zdroj**: vyberte jeden nebo více archivů, ze kterých mají být události předávány. Můžete vybrat aktivní archivy kolektorů (včetně společného archivu), osamocené archivy kolektory (archivy pro kolekce, které jste odstranili), archivy přijímače ICDx nebo archiv systému. <br>Výchozím nastavením je běžné archivace.
      > [!NOTE]
      > Archivy přijímače ICDx jsou uvedeny samostatně podle názvu. 
 
    - **Filtr**: přidejte filtr, který určuje podmnožinu událostí, které mají být předány. Proveďte některou z následujících akcí:
        - Pokud chcete vybrat podmínku filtru, klikněte na typ, atribut, operátor a hodnotu. 
        - V poli filtr Zkontrolujte podmínku filtru. Můžete ho přímo v poli Upravit nebo ho podle potřeby odstranit.
        - Kliknutím na a nebo nebo přidejte do podmínky filtru.
        - Uložený dotaz můžete použít také v případě, že kliknete na uložené dotazy.
    - **Zahrnuté atributy**: Zadejte seznam atributů oddělených čárkami, které se mají zahrnout do předávaných dat. Zahrnuté atributy mají přednost před vyloučenými atributy.
    - **Vyloučené atributy**: Zadejte seznam atributů oddělených čárkami, které se mají vyloučit z předávaných dat.
    - **Velikost dávky**: Vyberte počet událostí k odeslání na jednu dávku. Máte tyto možnosti: 10, 50, 100, 500 a 1000.<br>Výchozí hodnota je 100. 
    - **Limit přenosové rychlosti**: Vyberte rychlost přeposílání událostí vyjádřených jako počet událostí za sekundu. Vaše možnosti jsou neomezené, 500, 1000, 5000, 10000. <br> Výchozí hodnota je 5000. 
7. V části **cíl Azure**postupujte takto: 
    - **ID pracovního prostoru**: níže vložte ID pracovního prostoru. Toto pole je vyžadováno.
    - **Primární klíč**: níže vložte primární klíč. Toto pole je vyžadováno.
    - **Název vlastního protokolu**: zadejte název vlastního protokolu v pracovním prostoru portál Microsoft Azure Log Analytics, do kterého chcete události přesměrovat. Výchozí hodnota je SymantecICDx. Toto pole je vyžadováno.
8. Kliknutím na *Uložit* dokončete konfiguraci pro přeposílání. 
9. Pokud chcete spustit server pro přeposílání, v části **Možnosti**klikněte na **Další** a pak na **Spustit**.
10. Pokud chcete pro události Symantec ICDx použít příslušné schéma v Log Analytics, vyhledejte **SymantecICDx_CL**.


## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 



## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Symantec ICDx ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.


