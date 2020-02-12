---
title: Správa dat Azure AI Gallery
titleSuffix: ML Studio (classic) - Azure
description: Můžete exportovat a odstraňovat svá data v rámci produktu uživatele z Galerie Azure AI pomocí rozhraní nebo rozhraní API katalogu AI galerie. V tomto článku se dozvíte, jak.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: jmartens, mldocs
ms.openlocfilehash: 8406da7a429fb5b9429845628a00cdb6cc715540
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77148688"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Zobrazení a odstranit data uživatelů v rámci produktu z Galerie Azure AI

Můžete zobrazit a odstranit v rámci produktu uživatelská data z Galerie Azure AI pomocí rozhraní nebo rozhraní API katalogu AI galerie. Tento článek vysvětluje, jak.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Zobrazení dat v galerii AI pomocí uživatelského rozhraní

Můžete zobrazit položky, kterou jste publikovali prostřednictvím webu galerii Azure AI uživatelského rozhraní. Uživatelé mohou zobrazit veřejné i neuvedené řešení, projekty, experimenty a další publikované položky:

1.  Přihlaste se k [Azure AI Gallery](https://gallery.azure.ai/).
2.  Klikněte na tlačítko profilový obrázek v pravém horním rohu a pak název účtu se načíst stránku svého profilu.
3.  Na stránce profilu se zobrazí všechny položky v galerii, včetně neuvedené v seznamu položek publikovaných.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Umožňuje zobrazit vaše data pomocí rozhraní API Galerie katalogu AI

Můžete programově zobrazit data shromážděná prostřednictvím rozhraní API katalogu Galerie AI, které je přístupné na https://catalog.cortanaanalytics.com/entities. Chcete-li zobrazit data, budete potřebovat ID autora. Chcete-li zobrazit neuvedené v seznamu entit prostřednictvím rozhraní API katalogu, budete potřebovat přístupový token.

Katalog odpovědi jsou vráceny ve formátu JSON.

### <a name="get-an-author-id"></a>Získání ID autora
Autor ID je založeno na e-mailovou adresu používá při publikování na galerii Azure AI. Nelze změnit:

1.  Přihlaste se k [Azure AI Gallery](https://gallery.azure.ai/).
2.  Klikněte na tlačítko profilový obrázek v pravém horním rohu a pak název účtu se načíst stránku svého profilu.
3.  Adresa URL na panelu Adresa zobrazuje alfanumerické ID následující `authorId=`. Například pro adresu URL: `https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    ID autora: `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Získání přístupového tokenu

Budete potřebovat přístupový token k zobrazení neuvedené v seznamu entit prostřednictvím rozhraní API katalogu. Bez přístupový Token uživatelé mohou zobrazit veřejné entit a další informace o uživateli.

K získání přístupového tokenu je potřeba zkontrolovat hlavičku `DataLabAccessToken` požadavku HTTP, který prohlížeč vytvoří v katalogu rozhraní API, když se přihlásí:

1.  Přihlaste se k [Azure AI Gallery](https://gallery.azure.ai/).
2.  Klikněte na tlačítko profilový obrázek v pravém horním rohu a pak název účtu se načíst stránku svého profilu.
3.  Stisknutím klávesy F12 otevřete v podokně Developer Tools prohlížeče, vyberte kartu síť a aktualizujte stránku. 
4. Vyfiltrujte požadavky na *katalog* řetězců zadáním do textového pole Filtr.
5.  V části požadavky na adresu URL `https://catalog.cortanaanalytics.com/entities`Najděte požadavek GET a vyberte kartu *záhlaví* . Posuňte se dolů k části s *hlavičkou požadavků* .
6.  V záhlaví `DataLabAccessToken` je alfanumerický token. Chcete-li pomoci zabezpečit data, Nesdílejte tento token.

### <a name="view-user-information"></a>Zobrazit informace o uživatelích
Pomocí ID autora, které jste získali v předchozích krocích, zobrazte informace v profilu uživatele nahrazením `[AuthorId]` v následující adrese URL:

    https://catalog.cortanaanalytics.com/users/[AuthorID]

Například tato adresa URL požadavku:
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

Vrátí odpověď jako například:

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>Zobrazení veřejné entity

Rozhraní API služby Catalog ukládá informace o publikovaných entitách do Azure AI Gallery, které můžete zobrazit také přímo na [webu Galerie AI](https://gallery.azure.ai/). 

Chcete-li zobrazit publikované entity, přejděte na následující adresu URL, kde nahraďte `[AuthorId]` číslem autora získaným v části [získání ID autora](#get-an-author-id) výše.

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

Například:

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>Zobrazit neuvedené v seznamu a veřejné entity

Tento dotaz zobrazí pouze veřejné entity. Chcete-li zobrazit všechny entity, včetně neuvedené v seznamu těch, poskytují přístup token získaný z předchozí části.

1.  Pomocí nástroje, jako je například [post](https://www.getpostman.com), vytvořte požadavek HTTP GET na adresu URL katalogu, jak je popsáno v části [získání přístupového tokenu](#get-your-access-token).
2.  Vytvořte hlavičku požadavku HTTP s názvem `DataLabAccessToken`s hodnotou nastavenou na přístupový token.
3.  Odeslání požadavku HTTP.

> [!TIP]
> Pokud neuvedené v seznamu entit se nezobrazují v odpovědi z rozhraní API katalogu, uživatel může být neplatný nebo vypršení platnosti přístupového tokenu. Odhlaste se ze Azure AI Gallery a pak opakujte postup v části [získání přístupového tokenu](#get-your-access-token) pro obnovení tokenu. 
