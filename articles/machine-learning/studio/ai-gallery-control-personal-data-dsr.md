---
title: Správa dat galerie Azure AI
titleSuffix: ML Studio (classic) - Azure
description: Můžete exportovat a odstranit vaše in-product uživatelská data z Galerie Azure AI pomocí rozhraní nebo Rozhraní API galerie AI. Tento článek vám ukáže, jak na to.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: jmartens, mldocs
ms.openlocfilehash: 03341b9e663398f2c42266dead0d2dd01e97c3f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204541"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Zobrazení a odstranění uživatelských dat v produktu z Galerie AI Azure

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Můžete zobrazit a odstranit vaše in-product uživatelská data z Galerie Azure AI pomocí rozhraní nebo Rozhraní API galerie AI katalogu. Tento článek vám řekne, jak na to.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Zobrazení dat v Galerii ai s ui

Položky, které jste publikovali, můžete zobrazit prostřednictvím ui webu Azure AI Gallery. Uživatelé mohou zobrazit veřejná i neuvedená řešení, projekty, experimenty a další publikované položky:

1.    Přihlaste se do [Galerie Azure AI](https://gallery.azure.ai/).
2.    Klikněte na profilový obrázek v pravém horním rohu a potom na název účtu načtěte stránku profilu.
3.    Na stránce profilu se zobrazí všechny položky publikované v galerii, včetně neuvedených položek.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Zobrazení dat pomocí rozhraní API katalogu galerie AI

Můžete programově zobrazit data shromážděná prostřednictvím rozhraní API galerie https://catalog.cortanaanalytics.com/entitiesAI, které je přístupné na adrese . Chcete-li zobrazit data, potřebujete id autora. Chcete-li zobrazit neuvedené entity prostřednictvím rozhraní API katalogu, potřebujete přístupový token.

Odpovědi katalogu jsou vráceny ve formátu JSON.

### <a name="get-an-author-id"></a>Získání ID autora
ID autora je založené na e-mailové adrese použité při publikování v Galerii AI Azure. To se nemění:

1.    Přihlaste se do [Galerie AI Azure](https://gallery.azure.ai/).
2.    Klikněte na profilový obrázek v pravém horním rohu a potom na název účtu načtěte stránku profilu.
3.    Adresa URL v adresním řádku zobrazuje `authorId=`alfanumerické ID za . Například pro adresu URL:`https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    ID autora:`99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Získání přístupového tokenu

K zobrazení neuvedených entit prostřednictvím rozhraní API katalogu potřebujete přístupový token. Bez přístupového tokenu mohou uživatelé stále zobrazit veřejné entity a další informace o uživateli.

Chcete-li získat přístupový token, `DataLabAccessToken` musíte zkontrolovat záhlaví požadavku HTTP, který prohlížeč provede v rozhraní API katalogu při přihlášení:

1.    Přihlaste se do [Galerie Azure AI](https://gallery.azure.ai/).
2.    Klikněte na profilový obrázek v pravém horním rohu a potom na název účtu načtěte stránku profilu.
3.    Otevřete podokno Vývojářské nástroje prohlížeče stisknutím klávesy F12, vyberte kartu Síť a aktualizujte stránku. 
4. V *katalogu* řetězců můžete filtrovat požadavky zadáním do textového pole Filtr.
5.    V požadavcích na `https://catalog.cortanaanalytics.com/entities`adresu URL najděte požadavek GET a vyberte kartu *Záhlaví.* *Request Headers*
6.    Pod záhlavím `DataLabAccessToken` je alfanumerický token. Chcete-li zabezpečit data, nesdílejte tento token.

### <a name="view-user-information"></a>Zobrazit informace o uživateli
Pomocí ID autora, které jste získali v předchozích krocích, zobrazte informace v profilu uživatele nahrazením `[AuthorId]` v následující adrese URL:

    https://catalog.cortanaanalytics.com/users/[AuthorID]

Například tento požadavek na adresu URL:
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

Vrátí odpověď, například:

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>Zobrazit veřejné entity

Rozhraní API katalogu ukládá informace o publikovaných entitách do galerie Azure AI, které můžete také zobrazit přímo na [webu Galerie AI](https://gallery.azure.ai/). 

Chcete-li zobrazit publikované entity, navštivte `[AuthorId]` následující adresu URL, která nahradí ID autora získané v [části Získat ID autora](#get-an-author-id) výše.

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

Například:

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>Zobrazit neveřejné a veřejné entity

Tento dotaz zobrazuje pouze veřejné entity. Chcete-li zobrazit všechny entity, včetně neuvedených, zadejte přístupový token získaný z předchozí části.

1.    Pomocí nástroje, jako je [Postman](https://www.getpostman.com), vytvořte požadavek HTTP GET na adresu URL katalogu, jak je popsáno v [části Získání přístupového tokenu](#get-your-access-token).
2.    Vytvořte snázvem `DataLabAccessToken`hlavičku požadavku HTTP s hodnotou nastavenou na přístupový token.
3.    Odešlete požadavek HTTP.

> [!TIP]
> Pokud se neuvedené entity nezobrazují v odpovědích z rozhraní API katalogu, může mít uživatel neplatný přístupový token nebo přístupový token s ukončenou platností. Odhlaste se z Galerie Azure AI a opakujte kroky v [části Získat přístupový token](#get-your-access-token) k obnovení tokenu. 
