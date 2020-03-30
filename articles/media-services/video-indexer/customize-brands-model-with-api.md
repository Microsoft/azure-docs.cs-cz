---
title: Přizpůsobení modelu značek pomocí rozhraní API videoindexeru
titleSuffix: Azure Media Services
description: Přečtěte si, jak přizpůsobit model značky pomocí rozhraní API videoindexeru.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: 79c3a7934e9152a4908f895c20ee6fbdc0f360cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127993"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Přizpůsobení modelu značek pomocí rozhraní API videoindexeru

Video Indexer podporuje detekci značky z řeči a vizuálního textu během indexování a reindexingu video a audio obsahu. Funkce detekce značek identifikuje zmínky o produktech, službách a společnostech navržených databází značek Bingu. Pokud je například společnost Microsoft zmíněna ve videonebo zvukovém obsahu nebo pokud se ve videu zobrazuje ve vizuálním textu, video indexer ji v obsahu rozpozná jako značku. Vlastní značky model umožňuje vyloučit určité značky z detekce a zahrnují značky, které by měly být součástí vašeho modelu, které nemusí být v databázi značek Bing.

Podrobný přehled naleznete v tématu [Přehled](customize-brands-model-overview.md).

Pomocí api videoindexeru můžete vytvářet, používat a upravovat vlastní modely značek zjištěné ve videu, jak je popsáno v tomto tématu. Můžete také použít web Video Indexer, jak je popsáno v [přizpůsobit značky modelu pomocí webu Video Indexer](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Vytvoření značky

Vytvoření rozhraní API [značky](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand) vytvoří novou vlastní značku a přidá ji do vlastního modelu značek pro zadaný účet.

> [!NOTE]
> Nastavení `enabled` (v těle) na hodnotu true umístí značku do seznamu *Zahrnout* pro indexer videa, aby byla detekována. Nastavení `enabled` na hodnotu false umístí značku do seznamu *Vyloučit,* takže ji indexer videa nerozpozná.

Některé další parametry, které můžete nastavit v těle:

* Hodnota `referenceUrl` může být jakékoliv referenční webové stránky pro značku, jako je odkaz na její stránku Wikipedie.
* Hodnota `tags` je seznam značek pro značku. Tato značka se zobrazí v poli *Kategorie značky* na webu Video Indexer. Například značka "Azure" může být označena nebo kategorizována jako "Cloud".

### <a name="response"></a>Odpověď

Odpověď poskytuje informace o značce, kterou jste právě vytvořili podle formátu níže uvedeného příkladu.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>Odstranění značky

Odstranění rozhraní API [značky](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?) odebere značku z modelu vlastní značky pro zadaný účet. Účet je určen `accountId` v parametru. Jakmile bude značka úspěšně vyzvána, nebude již uvedena v seznamech *Značek Zahrnout* nebo *Vyloučit.*

### <a name="response"></a>Odpověď

Při úspěšném odstranění značky není vrácen žádný vrácený obsah.

## <a name="get-a-specific-brand"></a>Získejte konkrétní značku

Rozhraní [API získání značky](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?) umožňuje vyhledat podrobnosti o značce v modelu vlastníznačky pro zadaný účet pomocí ID značky.

### <a name="response"></a>Odpověď

Odpověď poskytuje informace o značce, kterou jste hledali (pomocí ID značky) podle formátu níže uvedeného příkladu.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> `enabled`nastavení na `true` značku znamená, že značka je v seznamu *Zahrnout* pro `enabled` video indexer, který má detekovat, a je nepravdivé, což znamená, že značka je v seznamu *Vyloučit,* takže video indexer ji nerozpozná.

## <a name="update-a-specific-brand"></a>Aktualizace konkrétní značky

Aktualizace [značky](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?) ROZHRANÍ API umožňuje vyhledat podrobnosti o značce v modelu vlastní značky pro zadaný účet pomocí ID značky.

### <a name="response"></a>Odpověď

Odpověď poskytuje aktualizované informace o značce, kterou jste aktualizovali podle formátu následujícího příkladu.

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>Získejte všechny značky

Rozhraní [API get all brands](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?) vrátí všechny značky v modelu vlastníznačky pro zadaný účet bez ohledu na to, zda má být značka v seznamu *Zahrnout* nebo *Vyloučit* značky.

### <a name="response"></a>Odpověď

Odpověď poskytuje seznam všech značek ve vašem účtu a každý z jejich údajů podle formátu níže uvedeného příkladu.

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
    {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> Značka s názvem *Příklad* je v seznamu *Zahrnout* pro video Indexer zjistit a značka s názvem *Example2* je v seznamu *Vyloučit,* takže Video Indexer nebude detekovat.

## <a name="get-brands-model-settings"></a>Získat nastavení modelu značek

Rozhraní [API nastavení značek get](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands) vrátí nastavení modelu značky v zadaném účtu. Nastavení modelu Značky představují, zda je zjišťování z databáze značek Bing povoleno nebo ne. Pokud značky Bing nejsou povoleny, video indexer rozpozná pouze značky z vlastního modelu značek zadaného účtu.

### <a name="response"></a>Odpověď

Odpověď ukazuje, zda jsou značky Bing povoleny podle formátu následujícího příkladu.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn`nastavena na hodnotu true představuje, že jsou povoleny značky Bing. Pokud `useBuiltin` je false, bing značky jsou zakázány. Hodnota `state` může být ignorována, protože byla zastaralá.

## <a name="update-brands-model-settings"></a>Aktualizovat nastavení modelu značek

Rozhraní [API pro aktualizace značek](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?) aktualizuje nastavení modelu značky v zadaném účtu. Nastavení modelu Značky představují, zda je zjišťování z databáze značek Bing povoleno nebo ne. Pokud značky Bing nejsou povoleny, video indexer rozpozná pouze značky z vlastního modelu značek zadaného účtu.

Příznak `useBuiltIn` nastavený na hodnotu true znamená, že značky Bing jsou povoleny. Pokud `useBuiltin` je false, bing značky jsou zakázány.

### <a name="response"></a>Odpověď

Při úspěšné aktualizaci modelu značky není vrácen žádný vrácený obsah.

## <a name="next-steps"></a>Další kroky

[Přizpůsobení modelu značek pomocí webu](customize-brands-model-with-website.md)
