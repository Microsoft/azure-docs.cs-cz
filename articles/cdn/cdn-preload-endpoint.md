---
title: Předběžné načtení prostředků v koncovém bodě Azure CDN | Dokumenty společnosti Microsoft
description: Zjistěte, jak předem načíst obsah uložený v mezipaměti v koncovém bodě Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: allensu
ms.openlocfilehash: c45d0a9195a719d830753a9614cfa7efb6f1c23d
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260271"
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Předběžné načtení prostředků v koncovém bodu Azure CDN
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Ve výchozím nastavení jsou datové zdroje ukládány do mezipaměti pouze v případě, že jsou požadovány. Vzhledem k tomu, že hraniční servery ještě neuvázali obsah do mezipaměti a je třeba předat požadavek na zdrojový server, může první požadavek z každé oblasti trvat déle než následné požadavky. Chcete-li se vyhnout této latenci prvního přístupu, předem načtěte prostředky. Kromě lepšího zákaznického prostředí může předběžné načtení prostředků uložených v mezipaměti snížit zatížení sítě na původním serveru.

> [!NOTE]
> Předběžné načítání datových zdrojů je užitečné pro velké události nebo obsah, který je současně k dispozici mnoha uživatelům, například nové filmové verzi nebo aktualizaci softwaru.
> 
> 

Tento kurz vás provede předběžným načítáním obsahu uloženého v mezipaměti na všech hraničních uzlech Azure CDN.

## <a name="to-pre-load-assets"></a>Předběžné načtení prostředků
1. Na [webu Azure Portal](https://portal.azure.com)přejděte na profil CDN obsahující koncový bod, který chcete předem načíst. Otevře se podokno profilu.
    
2. Klikněte na koncový bod v seznamu. Otevře se podokno koncového bodu.
3. V podokně koncového bodu CDN vyberte **načíst**.
   
    ![Podokno koncového bodu CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    Otevře se podokno **Zatížení.**
   
    ![Podokno zatížení CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. Do **cesty K obsahu**zadejte úplnou cestu ke každému `/pictures/kitten.png`datovému zdroji, který chcete načíst (například).
   
   > [!TIP]
   > Po zadání textu se zobrazí další textová pole **cesty obsahu,** která vám umožní sestavit seznam více datových zdrojů. Chcete-li odstranit datové zdroje ze seznamu, vyberte tlačítko tři tečky (...) a pak vyberte **Odstranit**.
   > 
   > Každá cesta k obsahu musí být relativní adresou URL, která odpovídá následujícím [regulárním výrazům](/dotnet/standard/base-types/regular-expression-language-quick-reference):  
   > - Načtení jedné cesty k souboru:`^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - Načtení jednoho souboru s řetězcem dotazu:`^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Vzhledem k tomu, že každý prostředek musí mít svou vlastní cestu, neexistuje žádná funkce zástupných symbolů pro předběžné načítání prostředků.
   > 
   > 
   
    ![Tlačítko Načíst](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Po zadání cest obsahu vyberte **Načíst**.
   

> [!NOTE]
> Je limit 10 požadavků na zatížení za minutu na profil CDN a 50 souběžných cest lze zpracovat najednou. Každá cesta má limit délky cesty 1024 znaků.
> 
> 

## <a name="see-also"></a>Viz také
* [Vymazání koncového bodu Azure CDN](cdn-purge-endpoint.md)
* [Odkaz na rozhraní API Azure CDN: Předběžné načtení obsahu v koncovém bodě](https://docs.microsoft.com/rest/api/cdn/endpoints/loadcontent)
* [Odkaz na rozhraní API Azure CDN: Vymazání obsahu z koncového bodu](https://docs.microsoft.com/rest/api/cdn/endpoints/purgecontent)

