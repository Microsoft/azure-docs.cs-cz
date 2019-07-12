---
title: Připojení dat společnosti Symantec ICDx Sentinelu ve verzi Preview Azure | Dokumentace Microsoftu
description: Informace o připojení k Azure Sentinelu Symantec ICDx data.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 74169b4bd2654fb0ff7ec4cdb2f2b02c0f4cc6e8
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673755"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Připojit zařízení Symantec ICDx 

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Symantec ICDx konektoru můžete snadno připojit všechny protokoly řešení Symantec zabezpečení s vaší Azure Sentinelu zobrazení řídicích panelů, vytvářet vlastní výstrahy a zlepší. To poskytuje lepší přehled o síti vaší organizace a zlepšuje schopnosti operace zabezpečení. Integrace mezi Symantec ICDx a Sentinelu Azure využívá rozhraní REST API.


> [!NOTE]
> Data se uloží v zeměpisné oblasti pracovního prostoru, na kterém je spuštěný Sentinelu Azure.

## <a name="configure-and-connect-symantec-icdx"></a>Konfigurace a připojení Symantec ICDx 

Integrace a exportovat protokoly přímo do Azure Sentinelu Symantec ICDx.

1. Otevřete konzolu pro správu ICDx přidání serverů pro předávání Microsoft Azure Sentinelu (Log Analytics).
2. Klikněte na navigačním panelu ICDx **konfigurace**. 
3. V horní části **konfigurace** obrazovce, klikněte na tlačítko **předávání**.
4. V části **předávání**, vedle Microsoft Azure Sentinelu (Log Analytics), klikněte na tlačítko **přidat**. 
4. V **Microsoft Azure Sentinelu (Log Analytics)** okna, klikněte na tlačítko **zobrazit rozšířené**. 
5. V horní části rozbalených oknu Microsoft Azure Sentinelu (Log Analytics), postupujte takto:
    -   **Název**: Zadejte název pro předávání, která má více než 30 znaků. Zvolte jedinečný, smysluplný název. Tento název se zobrazí v seznamu serverů pro předávání na **konfigurace** obrazovky a na řídicích panelech na **řídicí panel** obrazovky. Příklad: Microsoft Azure Log Analytics – východ. Toto pole je povinné.
    -   **Popis**: Zadejte popis pro předávání. Tento popis se také zobrazí v seznamu serverů pro předávání na **konfigurace** obrazovky. Zahrnout podrobnosti, jako je typ události, které jsou předávané a skupiny, kterou je potřeba kontrolovat data.
    -   **Typ spouštění**: Vyberte metodu spouštěný pro konfiguraci předávání. Možnosti jsou ruční a automatická.<br>Výchozí hodnota je automaticky. 
6. V části **události**, postupujte takto: 
    - **Zdroj**: Vyberte jeden nebo více archivy, ze kterého se má přeposlat události. Můžete vybrat aktivní kolekcí archivy (včetně běžných archiv), osamocené kolekce archivy (to znamená, archivy pro kolekce, které jste odstranili), ICDx příjemce archivy nebo Archiv systému. <br>Výchozí hodnota je běžné archivu.
      > [!NOTE]
      > Archivy příjemce ICDx jsou uvedeny samostatně, podle názvu. 
 
    - **Filtr**: Přidáte filtr, který určuje podmnožinu událostí k předávání. Udělejte jednu z těchto věcí:
        - Chcete-li vybrat podmínku filtru, klikněte na typ, atribut, operátor a hodnotu. 
        - Do pole filtru projděte si vaše podmínka filtru. Můžete upravit přímo v poli nebo odstranit podle potřeby.
        - Klikněte na tlačítko a nebo nebo přidat pro vaše podmínka filtru.
        - Můžete také kliknout na uložené dotazy použít uložený dotaz.
    - **Zahrnuté atributy**: Zadejte čárkami oddělený seznam atributů, které chcete zahrnout do dat předávaných. Zahrnuté atributy mají přednost před atributy vyloučené.
    - **Vyloučené atributy**: Zadejte čárkami oddělený seznam atributů, které chcete vyloučit z předané data.
    - **Velikost dávky**: Vyberte počet události odesílat na dávku. Vaše možnosti jsou 10, 50, 100, 500 až 1000.<br>Výchozí hodnota je 100. 
    - **Omezení přenosové rychlosti**: Vyberte rychlost, jakou jsou předávány události, vyjádřené jako událostí za sekundu. Možnosti jsou neomezená, 500, 1000, 5000, 10000. <br> Výchozí hodnota je 5 000. 
7. V části **Azure cílové**, postupujte takto: 
    - **ID pracovního prostoru**: Vložení ID pracovního prostoru z následujícího seznamu. Toto pole je povinné.
    - **Primární klíč**: Vložte primární klíč z následujícího seznamu. Toto pole je povinné.
    - **Název vlastního protokolu**: Zadejte název vlastního protokolu Microsoft Azure portal pracovní prostor Log Analytics ke které se chystáte události. Výchozí hodnota je SymantecICDx. Toto pole je povinné.
8. Klikněte na tlačítko *Uložit* dokončete konfiguraci předávání. 
9. Spusťte předávání, v části **možnosti**, klikněte na tlačítko **Další** a potom **Start**.
10. Chcete-li použít příslušné schéma v Log Analytics pro události Symantec ICDx, vyhledejte **SymantecICDx_CL**.


## <a name="validate-connectivity"></a>Ověření připojení

Může trvat upwards of 20 minut, než vaše protokoly spuštění se zobrazí v Log Analytics. 



## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak se připojit k Azure Sentinelu Symantec ICDx. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).

