---
title: 'ML Studio (Classic): Správa dat Azure AI Gallery – Azure'
description: Data uživatelů v produktu můžete exportovat a odstranit z Azure AI Gallery pomocí rozhraní API katalogu Galerie rozhraní nebo AI. V tomto článku se dozvíte, jak.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: mldocs
ms.openlocfilehash: 1c549cd4a4d76e96f16eb04f3cf5aa3368725b8c
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2021
ms.locfileid: "98070537"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Umožňuje zobrazit a odstranit data uživatelů v produktu z Azure AI Gallery



Data uživatelů v produktu můžete zobrazit a odstranit z Azure AI Gallery pomocí rozhraní API katalogu Galerie rozhraní nebo AI. V tomto článku se dozvíte, jak.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Zobrazení dat v galerii AI pomocí uživatelského rozhraní

Můžete zobrazit položky, které jste publikovali prostřednictvím uživatelského rozhraní Azure AI Gallery webu. Uživatelé můžou zobrazit veřejná i neseznamovaná řešení, projekty, experimenty a další publikované položky:

1.    Přihlaste se k [Azure AI Gallery](https://gallery.azure.ai/).
2.    Klikněte na profilový obrázek v pravém horním rohu a pak na název účtu pro načtení stránky profilu.
3.    Na stránce profil se zobrazí všechny položky publikované do galerie, včetně neuvedených položek.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Použití rozhraní API katalogu Galerie AI k zobrazení vašich dat

Můžete programově zobrazit data shromážděná prostřednictvím rozhraní API katalogu Galerie AI, které je dostupné na adrese https://catalog.cortanaanalytics.com/entities . Chcete-li zobrazit data, budete potřebovat své ID autora. Pokud chcete zobrazit neuvedené entity prostřednictvím rozhraní API katalogu, budete potřebovat přístupový token.

Odpovědi katalogu se vrátí ve formátu JSON.

### <a name="get-an-author-id"></a>Získat ID autora
ID autora vychází z e-mailové adresy, která se používá při publikování do Azure AI Gallery. Nemění se:

1. Přihlaste se k [Azure AI Gallery](https://gallery.azure.ai/).
2. Klikněte na profilový obrázek v pravém horním rohu a pak na název účtu pro načtení stránky profilu.
3. Adresa URL na panelu Adresa zobrazuje následující alfanumerické ID `authorId=` . Například pro adresu URL: \
    `https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

    ID autora: \
    `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Získání přístupového tokenu

Abyste mohli zobrazit neuvedené entity prostřednictvím rozhraní API katalogu, potřebujete přístupový token. Bez přístupového tokenu můžou uživatelé dál zobrazovat veřejné entity a další informace o uživateli.

K získání přístupového tokenu je potřeba zkontrolovat `DataLabAccessToken` hlavičku požadavku HTTP, který prohlížeč vytvoří v katalogu rozhraní API, když se přihlásí:

1.    Přihlaste se k [Azure AI Gallery](https://gallery.azure.ai/).
2.    Klikněte na profilový obrázek v pravém horním rohu a pak na název účtu pro načtení stránky profilu.
3.    Stisknutím klávesy F12 otevřete podokno Vývojářské nástroje prohlížečem, vyberte kartu síť a aktualizujte stránku. 
4. Vyfiltrujte požadavky na *katalog* řetězců zadáním do textového pole Filtr.
5.    V části žádosti na adresu URL `https://catalog.cortanaanalytics.com/entities` Najděte požadavek GET a vyberte kartu *záhlaví* . Posuňte se dolů k části s *hlavičkou požadavků* .
6.    V záhlaví `DataLabAccessToken` je alfanumerický token. Pokud chcete zajistit zabezpečení vašich dat, tento token nesdílejte.

### <a name="view-user-information"></a>Zobrazit informace o uživateli
Pomocí ID autora, které jste získali v předchozích krocích, zobrazte informace v profilu uživatele nahrazením `[AuthorId]` následující adresy URL:

`https://catalog.cortanaanalytics.com/users/[AuthorID]`

Například tento požadavek adresy URL:

`https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

Vrátí odpověď, například:

```json
{"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}
```

### <a name="view-public-entities"></a>Zobrazit veřejné entity

Rozhraní API služby Catalog ukládá informace o publikovaných entitách do Azure AI Gallery, které můžete zobrazit také přímo na [webu Galerie AI](https://gallery.azure.ai/). 

Chcete-li zobrazit publikované entity, přejděte na následující adresu URL, kde nahraďte `[AuthorId]` ID autora získané v poli [získat ID autora](#get-an-author-id) výše.

`https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'`

Například:

`https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'`

### <a name="view-unlisted-and-public-entities"></a>Zobrazit neuvedené a veřejné entity

Tento dotaz zobrazí pouze veřejné entity. Pokud chcete zobrazit všechny vaše entity, včetně neuvedených seznamů, zadejte přístupový token získaný z předchozí části.

1.    Pomocí nástroje, jako je například [post](https://www.getpostman.com), vytvořte požadavek HTTP GET na adresu URL katalogu, jak je popsáno v části [získání přístupového tokenu](#get-your-access-token).
2.    Vytvořte hlavičku požadavku HTTP s názvem `DataLabAccessToken` , která má nastavenou hodnotu přístupového tokenu.
3.    Odešlete požadavek HTTP.

> [!TIP]
> Pokud neuvedené entity nejsou v odpovědích z katalogu rozhraní API zobrazeny, může mít uživatel neplatný nebo přístupový token s vypršenou platností. Odhlaste se ze Azure AI Gallery a pak opakujte postup v části [získání přístupového tokenu](#get-your-access-token) pro obnovení tokenu. 
