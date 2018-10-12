---
title: Předběžné načtení prostředků v koncovém bodu Azure CDN | Dokumentace Microsoftu
description: Zjistěte, jak se předem načíst obsah uložený v mezipaměti v koncovém bodu Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: magattus
ms.openlocfilehash: d2dc8ad1e4b7e429dc758a96e49aa4825ae108e5
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091313"
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Předběžné načtení prostředků v koncovém bodu Azure CDN
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Ve výchozím nastavení jsou prostředky do mezipaměti pouze v případě, že jste žádali. Protože hraniční servery nebyly dosud načteny do mezipaměti obsah a potřebujete předat požadavek na původním serveru, může trvat déle než následné žádosti první požadavek z každé oblasti. Aby se zabránilo latence první přístupů, předběžné načtení vaše prostředky. Kromě toho, že lepší prostředí pro zákazníky, předběžné načítání mezipaměti prostředky můžete snížit síťový provoz na původním serveru.

> [!NOTE]
> Předběžné načítání prostředků je užitečné pro velké události nebo obsah, který bude současně k dispozici pro mnoho uživatelů, jako je například nový film verze nebo aktualizace softwaru.
> 
> 

Tento kurz vás provede předběžné načtení obsahu v mezipaměti na všechny hraniční uzly Azure CDN.

## <a name="to-pre-load-assets"></a>Chcete-li předběžné načtení prostředků
1. V [webu Azure portal](https://portal.azure.com), přejděte do profilu CDN obsahujícího koncový bod chcete předběžné načtení. Otevře se podokno profil.
    
2. Klikněte na koncový bod v seznamu. Otevře se podokno koncový bod.
3. V podokně koncového bodu CDN vyberte **zatížení**.
   
    ![Koncový bod CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    **Zatížení** se otevře podokno.
   
    ![Podokno zatížení CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. Pro **cestu k obsahu**, zadejte úplnou cestu každý prostředek, který chcete načíst (například `/pictures/kitten.png`).
   
   > [!TIP]
   > Po spuštění zadávání textu, další **cestu k obsahu** textová pole se zobrazí a umožňuje tak vytvářet seznam více prostředků. Odstranit prostředky ze seznamu, vyberte tlačítko se třemi tečkami (...) a potom vyberte **odstranit**.
   > 
   > Každá cesta obsahu musí být relativní adresu URL, která odpovídá následující [regulární výrazy](https://msdn.microsoft.com/library/az24scfc.aspx):  
   > - Načte cestu jednoho souboru: `^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - Načtěte jeden soubor s řetězci dotazu: `^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Protože každý prostředek musí mít svůj vlastní cestu, není žádná funkce zástupných znaků pro předběžné načtení prostředků.
   > 
   > 
   
    ![Tlačítko Načíst](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Až budete mít, zadáním cesty k obsahu, vyberte **zatížení**.
   

> [!NOTE]
> Je stanovený limit 10 zatížení požadavků za minutu za profil CDN a 50 souběžných cesty může najednou zpracovat. Každá cesta maximální povolenou délku cesty 1024 znaků.
> 
> 

## <a name="see-also"></a>Další informace najdete v tématech
* [Vyprázdnění koncového bodu Azure CDN](cdn-purge-endpoint.md)
* [Reference k rozhraní REST API služby CDN Azure: předběžné načtení obsahu na koncový bod](https://docs.microsoft.com/rest/api/cdn/endpoints/loadcontent)
* [Reference k rozhraní REST API služby CDN Azure: vymazání obsahu z koncového bodu](https://docs.microsoft.com/rest/api/cdn/endpoints/purgecontent)

