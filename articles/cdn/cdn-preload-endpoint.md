---
title: Předběžné načtení prostředků na koncový bod Azure CDN | Microsoft Docs
description: Zjistěte, jak předem načíst obsah uložený v mezipaměti na koncový bod Azure CDN.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: mazha
ms.openlocfilehash: bf3161d756759e4b278e48ad7a49615e4a73d17f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Předběžné načtení prostředků v koncovém bodu Azure CDN
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Ve výchozím nastavení jsou prostředky do mezipaměti jenom v případě, že jste požadovali. Protože servery edge nebyly dosud do mezipaměti obsah a potřebujete předat požadavek na původní server, může trvat déle než následné žádosti první požadavek z každé oblasti. Abyste se vyhnuli tento první podle latence, předem načíst vaše prostředky. Kromě zajištění lepší zkušeností zákazníků, předem načítání vaše prostředky uložené v mezipaměti může snížit síťový provoz na původním serveru.

> [!NOTE]
> Předběžné načítání prostředků je užitečné pro velké události nebo obsah, který současně dostupný pro mnoho uživatelů, jako je nová verze film nebo aktualizace softwaru.
> 
> 

Tento kurz vás provede předběžné načítání obsahu v mezipaměti na všech uzlech edge Azure CDN.

## <a name="to-pre-load-assets"></a>Chcete-li předběžné načtení prostředků
1. V [portál Azure](https://portal.azure.com), vyhledejte profil CDN obsahující chcete předběžné načtení koncový bod. Otevře se podokno profilu.
    
2. Klikněte na koncový bod v seznamu. Otevře se podokno koncový bod.
3. V podokně koncového bodu CDN, vyberte **zatížení**.
   
    ![Podokno koncový bod CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    **Zatížení** otevře se podokno.
   
    ![Podokno zatížení CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. Pro **cestu obsahu**, zadejte úplnou cestu každý prostředek, který chcete načíst (například `/pictures/kitten.png`).
   
   > [!TIP]
   > Po spuštění zadáním textu, další **cestu obsahu** textová pole se zobrazí umožňují vytvořit seznam více prostředků. Pokud chcete odstranit prostředky ze seznamu, vyberte tlačítko se třemi tečkami (...) a pak vyberte **odstranit**.
   > 
   > Každá cesta obsahu musí být relativní adresa URL, která odpovídá následující [regulární výrazy](https://msdn.microsoft.com/library/az24scfc.aspx):  
   > - Načte cestu k jednoho souboru: `^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - Načtěte jeden soubor s řetězec dotazu: `^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Protože každý asset musí mít svůj vlastní cesta, není žádná funkce zástupných znaků pro předběžné načítání prostředků.
   > 
   > 
   
    ![Tlačítko zatížení](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Když skončíte se zadáním cesty k obsahu, vyberte **zatížení**.
   

> [!NOTE]
> Existuje limit 10 zatížení požadavků za minutu za profil CDN a 50 cest pro souběžné lze současně zpracovávat. Každá cesta může mít délku cesty 1024 znaků.
> 
> 

## <a name="see-also"></a>Další informace najdete v tématech
* [Vyprázdnění koncového bodu Azure CDN](cdn-purge-endpoint.md)
* [Azure CDN REST API – referenční informace: předem načíst obsah na koncový bod](https://docs.microsoft.com/rest/api/cdn/endpoints/loadcontent)
* [Azure CDN REST API – referenční informace: Vymazat obsah z koncového bodu](https://docs.microsoft.com/rest/api/cdn/endpoints/purgecontent)

