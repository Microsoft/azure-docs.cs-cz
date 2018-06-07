---
title: Zobrazení a odstranění dat z Galerie Azure AI | Microsoft Docs
description: Můžete exportovat a odstranit v produktu uživatelská data z Galerie Azure AI pomocí rozhraní nebo rozhraní API katalogu AI galerie. Tento článek ukazuje, jak.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
manager: cgronlun
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: studio
ms.topic: conceptual
ms.date: 05/25/2018
ms.openlocfilehash: bc6ffa912d7914c8662dbde623e04947540ac149
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654854"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Zobrazení a odstranění dat uživatele v produktu z Galerie Azure AI

Můžete zobrazit a odstranit v produktu uživatelská data z Galerie Azure AI pomocí rozhraní nebo rozhraní API katalogu AI galerie. Tento článek vysvětluje, jak.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Data zobrazit v galerii AI pomocí uživatelského rozhraní

Můžete zobrazit položky, které jste publikovali prostřednictvím webu Azure AI Galerie uživatelského rozhraní. Uživatelé mohou zobrazit veřejná i neuvedené řešení, projekty, experimenty a další publikované položky:

1.  Přihlaste se k [Galerie Azure AI](https://gallery.azure.ai/).
2.  Klepněte na obrázek profilu v pravém horním rohu a pak název účtu se načíst stránku profilu.
3.  Na stránce profil zobrazuje všechny položky, které jsou publikovány do galerie, včetně neuvedené položky.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Zobrazit vaše data pomocí rozhraní API Galerie katalogu AI

Můžete programově zobrazit data shromážděná prostřednictvím katalogu API AI galerie, která je přístupná na https://catalog.cortanaanalytics.com/entities. Chcete-li zobrazit data, je třeba ID autora. Chcete-li zobrazit neuvedené entity prostřednictvím rozhraní API katalogu, musíte token přístupu.

Katalog odpovědi jsou vráceny ve formátu JSON.

### <a name="get-an-author-id"></a>Získání ID Autor
Autor ID je založeno na e-mailovou adresu, používá při publikování do Galerie AI Azure. Nemění:

1.  Přihlaste se k [Galerie Azure AI](https://gallery.azure.ai/).
2.  Klepněte na obrázek profilu v pravém horním rohu a pak název účtu se načíst stránku profilu.
3.  Zobrazí adresu URL na panelu Adresa alfanumerické následující ID `authorId=`. Například pro adresu URL: `https://gallery.cortanaintelligence.com/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    ID autora: `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Získat přístupový token

Budete potřebovat token přístupu k zobrazení seznamu neuvedené entity prostřednictvím rozhraní API katalogu. Bez přístupu k tokenu uživatelé mohou zobrazit veřejné entity a další informace o uživateli.

Chcete-li získat přístupový token, je potřeba zkontrolovat `DataLabAccessToken` hlavičky požadavku HTTP v prohlížeči je pro rozhraní API katalogu během přihlášení:

1.  Přihlaste se k [Galerie Azure AI](https://gallery.azure.ai/).
2.  Klepněte na obrázek profilu v pravém horním rohu a pak název účtu se načíst stránku profilu.
3.  Otevřete podokno nástrojů pro vývojáře prohlížeče stisknutím klávesy F12, vyberte kartu sítě a aktualizujte stránku. 
4. Filtrování požadavků na řetězec *katalogu* tak, že zadáte do textového pole filtru.
5.  V požadavcích na adresu URL `https://catalog.cortanaanalytics.com/entities`, najděte požadavek GET a vyberte *hlavičky* kartě. Přejděte dolů k položce *záhlaví požadavku* části.
6.  Pod záhlavím `DataLabAccessToken` je alfanumerické token. Chcete-li pomoci zabezpečit vaše data, Nesdílejte tento token.

### <a name="view-user-information"></a>Zobrazit informace o uživatelích
Pomocí ID Autor jste získali v předchozím kroku, zobrazit informace v profilu uživatele tak, že nahradíte `[AuthorId]` v následující adresu URL:

    https://catalog.cortanaanalytics.com/users/[AuthorID]

Například tato adresa URL požadavku:
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

Vrátí odpověď, jako:

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>Zobrazení veřejného entity

Rozhraní API katalogu ukládá informace o publikovaných entity k AI Galerie Azure, kterou můžete také zobrazit přímo na [web Galerie AI](https://gallery.azure.ai/). 

Chcete zobrazit publikované entity, přejděte následující adresu URL, nahraďte `[AuthorId]` s ID Autor získaných v [získání ID Autor](#get-an-author-ID) výše.

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

Příklad:

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>Zobrazit neuvedené a veřejné entity

Tento dotaz zobrazí pouze veřejné entity. Chcete-li zobrazit všechny entity, včetně těch neuvedené, poskytují přístup získat token z předchozí části.

1.  Pomocí nástroje, například [Postman](https://www.getpostman.com), vytvořit požadavek HTTP GET na adresu URL katalogu, jak je popsáno v [získat přístupový token](#get-your-access-token).
2.  Vytvoření záhlaví požadavku HTTP volá `DataLabAccessToken`, hodnota nastavena na přístupový token.
3.  Odešlete požadavek HTTP.

> [!TIP]
> Pokud neuvedené entity se nezobrazují při odpovědi z rozhraní API katalogu, uživatel může mít neplatný nebo vypršela platnost tokenu přístupu. Z Galerie Azure AI odhlásit a potom opakujte kroky v [získat přístupový token](#get-your-access-token) k obnovení tokenu. 
