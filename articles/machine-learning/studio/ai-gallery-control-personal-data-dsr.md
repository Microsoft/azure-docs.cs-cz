---
title: Správa dat v galerii Azure AI
titleSuffix: Azure Machine Learning Studio
description: Můžete exportovat a odstraňovat svá data v rámci produktu uživatele z Galerie Azure AI pomocí rozhraní nebo rozhraní API katalogu AI galerie. V tomto článku se dozvíte, jak.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: jmartens, mldocs
ms.openlocfilehash: 85d7286c1fd075da7034a2a43d46a158550d2fe9
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247710"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Zobrazení a odstranit data uživatelů v rámci produktu z Galerie Azure AI

Můžete zobrazit a odstranit v rámci produktu uživatelská data z Galerie Azure AI pomocí rozhraní nebo rozhraní API katalogu AI galerie. Tento článek vysvětluje, jak.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Zobrazení dat v galerii AI pomocí uživatelského rozhraní

Můžete zobrazit položky, kterou jste publikovali prostřednictvím webu galerii Azure AI uživatelského rozhraní. Uživatelé mohou zobrazit veřejné i neuvedené řešení, projekty, experimenty a další publikované položky:

1.  Přihlaste se k [galerii Azure AI](https://gallery.azure.ai/).
2.  Klikněte na tlačítko profilový obrázek v pravém horním rohu a pak název účtu se načíst stránku svého profilu.
3.  Na stránce profilu se zobrazí všechny položky v galerii, včetně neuvedené v seznamu položek publikovaných.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Umožňuje zobrazit vaše data pomocí rozhraní API Galerie katalogu AI

Můžete programově zobrazit data shromážděná prostřednictvím katalogu API Galerie AI, který je přístupný na adrese https://catalog.cortanaanalytics.com/entities. Chcete-li zobrazit data, budete potřebovat ID autora. Chcete-li zobrazit neuvedené v seznamu entit prostřednictvím rozhraní API katalogu, budete potřebovat přístupový token.

Katalog odpovědi jsou vráceny ve formátu JSON.

### <a name="get-an-author-id"></a>Získání ID autora
Autor ID je založeno na e-mailovou adresu používá při publikování na galerii Azure AI. Nelze změnit:

1.  Přihlaste se k [galerii Azure AI](https://gallery.azure.ai/).
2.  Klikněte na tlačítko profilový obrázek v pravém horním rohu a pak název účtu se načíst stránku svého profilu.
3.  Adresu URL do adresního řádku se zobrazí následující alfanumerické ID `authorId=`. Například pro adresu URL: `https://gallery.cortanaintelligence.com/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    ID autora: `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Získání přístupového tokenu

Budete potřebovat přístupový token k zobrazení neuvedené v seznamu entit prostřednictvím rozhraní API katalogu. Bez přístupový Token uživatelé mohou zobrazit veřejné entit a další informace o uživateli.

Chcete-li získat přístupový token, je potřeba zkontrolovat `DataLabAccessToken` hlavičku požadavku HTTP prohlížeč umožňuje rozhraní API katalogu, když jste přihlášení:

1.  Přihlaste se k [galerii Azure AI](https://gallery.azure.ai/).
2.  Klikněte na tlačítko profilový obrázek v pravém horním rohu a pak název účtu se načíst stránku svého profilu.
3.  Stisknutím klávesy F12 otevřete v podokně Developer Tools prohlížeče, vyberte kartu síť a aktualizujte stránku. 
4. Filtrovat požadavky na řetězec *katalogu* tak, že zadáte do textového pole filtru.
5.  V požadavcích na adresu URL `https://catalog.cortanaanalytics.com/entities`požadavek GET vyhledejte a vyberte *záhlaví* kartu. Přejděte dolů k položce *záhlaví požadavku* oddílu.
6.  Pod nadpisem `DataLabAccessToken` je alfanumerické token. Chcete-li pomoci zabezpečit data, Nesdílejte tento token.

### <a name="view-user-information"></a>Zobrazit informace o uživatelích
Pomocí ID autora jste získali v předchozích krocích, zobrazit informace v profilu uživatele tak, že nahradíte `[AuthorId]` v následující adrese URL:

    https://catalog.cortanaanalytics.com/users/[AuthorID]

Například tato adresa URL požadavku:
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

Vrátí odpověď jako například:

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>Zobrazení veřejné entity

Rozhraní API katalogu ukládá informace o publikovaných entity, které můžete také zobrazit přímo v galerii Azure AI [webu Galerie AI](https://gallery.azure.ai/). 

Chcete-li zobrazit publikované entity, přejděte na následující adresu URL, nahrazení `[AuthorId]` s ID Autor získaný v [získat ID Autor](#get-an-author-ID) výše.

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

Příklad:

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>Zobrazit neuvedené v seznamu a veřejné entity

Tento dotaz zobrazí pouze veřejné entity. Chcete-li zobrazit všechny entity, včetně neuvedené v seznamu těch, poskytují přístup token získaný z předchozí části.

1.  Pomocí některého nástroje, například [Postman](https://www.getpostman.com), vytvořit požadavek HTTP GET na adresu URL katalogu, jak je popsáno v [získat přístupový token](#get-your-access-token).
2.  Vytvořit hlavičku požadavku HTTP volá `DataLabAccessToken`, s hodnotou nastavenou na token přístupu.
3.  Odeslání požadavku HTTP.

> [!TIP]
> Pokud neuvedené v seznamu entit se nezobrazují v odpovědi z rozhraní API katalogu, uživatel může být neplatný nebo vypršení platnosti přístupového tokenu. Odhlaste se z Galerie Azure AI a opakujte kroky v [získat přístupový token](#get-your-access-token) k obnovení tokenu. 
