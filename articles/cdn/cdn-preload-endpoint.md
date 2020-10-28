---
title: Předběžné načtení prostředků na Azure CDN koncový bod | Microsoft Docs
description: Naučte se, jak předem načíst obsah uložený v mezipaměti na koncovém bodu Azure Content Delivery Network. Tato funkce je k dispozici v některých verzích produktu.
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
ms.topic: how-to
ms.date: 02/12/2018
ms.author: allensu
ms.openlocfilehash: 7fb414680b8ff64d057f9f68d7b97380baeaa551
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92778201"
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Předběžné načtení prostředků v koncovém bodu Azure CDN
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Ve výchozím nastavení jsou prostředky ukládány do mezipaměti pouze v případě, že jsou požadovány. Vzhledem k tomu, že hraniční servery ještě neobsahují obsah do mezipaměti a potřebují přesměrovat požadavek na zdrojový server, první požadavek z každé oblasti může trvat déle než následné žádosti. Abyste se vyhnuli této latenci prvního volání, Předveďte své prostředky předem. Kromě poskytování lepšího prostředí pro zákazníky může před načtením prostředků v mezipaměti dojít k omezení síťového provozu na zdrojovém serveru.

> [!NOTE]
> Předem načtené prostředky jsou užitečné pro velké události nebo obsah, který se současně zpřístupní mnoha uživatelům, jako je například nová verze videa nebo aktualizace softwaru.
> 
> 

Tento kurz vás provede předběžným načtením obsahu v mezipaměti na všech Azure CDN hraničních uzlech.

## <a name="to-pre-load-assets"></a>Postup předběžného načtení prostředků
1. V [Azure Portal](https://portal.azure.com)přejděte do profilu CDN obsahujícího koncový bod, který chcete předem načíst. Otevře se podokno profil.
    
2. V seznamu klikněte na koncový bod. Otevře se podokno koncový bod.
3. V podokně koncový bod CDN vyberte **načíst** .
   
    ![Podokno koncového bodu CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    Otevře se podokno **zatížení** .
   
    ![Podokno zatížení CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. V poli **cesta k obsahu** zadejte úplnou cestu každého assetu, který chcete načíst (například `/pictures/kitten.png` ).
   
   > [!TIP]
   > Po zahájení zadávání textu se zobrazí další textová pole s dalšími **cestami k obsahu** , která vám umožní vytvořit seznam více prostředků. Pokud chcete odstranit assety ze seznamu, vyberte tlačítko se třemi tečkami (...) a pak vyberte **Odstranit** .
   > 
   > Každá cesta k obsahu musí být relativní adresa URL, která odpovídá následujícím [regulárním výrazům](/dotnet/standard/base-types/regular-expression-language-quick-reference):  
   > - Načtěte jednu cestu k souboru: `^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - Načtěte jeden soubor s řetězcem dotazu: `^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Vzhledem k tomu, že každý prostředek musí mít svou vlastní cestu, neexistuje žádná funkce zástupných znaků pro předem načtené prostředky.
   > 
   > 
   
    ![Tlačítko načíst](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Až skončíte s zadáváním cest k obsahu, vyberte **načíst** .
   

> [!NOTE]
> Omezení 10 požadavků na zatížení za minutu na jeden profil CDN a 50 souběžných cest je možné zpracovat najednou. Každá cesta má omezení délky cesty 1024 znaků.
> 
> 

## <a name="see-also"></a>Viz také
* [Vyprázdnit Azure CDN koncový bod](cdn-purge-endpoint.md)
* [Odkaz na Azure CDN REST API: předběžné načtení obsahu na koncový bod](/rest/api/cdn/endpoints/loadcontent)
* [Odkaz na Azure CDN REST API: vyprázdnění obsahu z koncového bodu](/rest/api/cdn/endpoints/purgecontent)