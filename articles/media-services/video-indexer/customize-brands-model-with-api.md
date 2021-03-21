---
title: Přizpůsobení modelu značek pomocí rozhraní Video Indexer API
titleSuffix: Azure Media Services
description: Naučte se, jak přizpůsobit model značek pomocí rozhraní Video Indexer API.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: c3056415d3432dbe64dd1f2bcf974a676bbf6c6b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97586053"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Přizpůsobení modelu značek pomocí rozhraní Video Indexer API

Video Indexer podporuje rozpoznávání značek od mluvené řeči a vizuálního textu během indexování a přeindexování videa a zvukového obsahu. Funkce detekce značky identifikuje zmínky o produktech, službách a společnostech, které navrhla databáze značek Bingu. Pokud je například Microsoft zmíněný ve videu nebo zvukovém obsahu nebo pokud se zobrazuje v obrazovém textu ve videu, Video Indexer ho detekuje jako značku v obsahu. Vlastní model značek umožňuje vyloučit z detekce některé značky a zahrnout značky, které by měly být součástí modelu, který nemusí být v databázi značek Bingu. Další informace najdete v tématu [Přehled](customize-brands-model-overview.md).

> [!NOTE]
> Pokud bylo video indexováno před přidáním značky, je nutné ho znovu indexovat.

Rozhraní Video Indexer API můžete použít k vytváření, používání a úpravám vlastních značek, které byly zjištěny ve videu, jak je popsáno v tomto tématu. Můžete také použít Video Indexer web, jak je popsáno v tématu [Přizpůsobení modelu značek pomocí webu video indexer](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Vytvoření značky

Rozhraní API pro [vytvoření značky](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand) vytvoří novou vlastní značku a přidá ji do vlastního modelu značek pro zadaný účet.

> [!NOTE]
> Nastavení `enabled` (v těle) na hodnotu true vloží značku do seznamu *vložených* video indexer k detekci. Nastavení `enabled` na false vloží značku do seznamu *vyloučení* , takže ji video indexer nerozpozná.

Některé další parametry, které lze nastavit v těle:

* `referenceUrl`Hodnota může být libovolná referenční webová stránka pro značku, jako je například odkaz na svou Wikipedii stránku.
* `tags`Hodnota je seznam značek pro značku. Tato značka se zobrazí v poli *kategorie* značky na webu video indexer. Například značka "Azure" může být označená nebo Kategorizovaná jako "Cloud".

### <a name="response"></a>Odpověď

Odpověď poskytuje informace o značce, kterou jste právě vytvořili, podle formátu níže uvedeného příkladu.

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

Rozhraní API pro [odstranění značky](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?) odstraní značku z vlastního modelu značek pro zadaný účet. Účet je zadán v `accountId` parametru. Po úspěšném volání už nebude značka v seznamech *zahrnutí* nebo *vyloučení* značek.

### <a name="response"></a>Odpověď

Po úspěšném odstranění značky se nevrátí žádný obsah.

## <a name="get-a-specific-brand"></a>Získat konkrétní značku

Rozhraní API [získat značku](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?) vám umožní vyhledat podrobnosti o značce v modelu vlastních značek pro zadaný účet pomocí ID značky.

### <a name="response"></a>Odpověď

Odpověď poskytuje informace o značce, kterou jste hledali (pomocí ID značky), a to podle formátu níže uvedeného příkladu.

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
> `enabled` je nastavené tak `true` , že se tato značka nachází v seznamu *Include* pro video indexer rozpoznat a že je `enabled` false znamená, že se značka nachází v seznamu *vyloučení* , takže video indexer ji nerozpozná.

## <a name="update-a-specific-brand"></a>Aktualizace konkrétní značky

Rozhraní API pro [aktualizaci značky](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?) vám umožní vyhledat podrobnosti o značce v modelu vlastních značek pro zadaný účet pomocí ID značky.

### <a name="response"></a>Odpověď

Odpověď poskytuje aktualizované informace o značce, kterou jste aktualizovali podle formátu níže uvedeného příkladu.

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

## <a name="get-all-of-the-brands"></a>Získání všech značek

Rozhraní API [získat všechny značky](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?) vrátí všechny značky v modelu vlastních značek pro zadaný účet bez ohledu na to, zda je značka v seznamu *zahrnutí* nebo *vyloučení* značek.

### <a name="response"></a>Odpověď

Odpověď poskytuje seznam všech značek ve vašem účtu a každé z jejich podrobností podle formátu níže uvedeného příkladu.

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
> Značka s názvem *example* je v seznamu *include* pro video indexer k detekci a značka s názvem *example2* je v seznamu *vyloučení* , takže video indexer ji nerozpozná.

## <a name="get-brands-model-settings"></a>Získat nastavení modelu značek

Rozhraní API pro [získání značek](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands) vrátí nastavení modelu značky v zadaném účtu. Nastavení modelu značek představuje, zda je povoleno zjišťování z databáze značek Bing. Pokud nejsou značky Bingu povolené, Video Indexer detekuje pouze značky z vlastního modelu značek zadaného účtu.

### <a name="response"></a>Odpověď

Odpověď ukazuje, jestli jsou značky Bingu povolené, a to podle formátu níže uvedeného příkladu.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn` nastavení na hodnotu true představuje, že jsou značky Bingu povolené. Pokud `useBuiltin` je hodnota false, značky Bingu jsou zakázané. `state`Hodnota může být ignorována, protože je zastaralá.

## <a name="update-brands-model-settings"></a>Aktualizace nastavení modelu značek

Rozhraní API pro [aktualizaci značek](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?) aktualizuje nastavení modelu značky v zadaném účtu. Nastavení modelu značek představuje, zda je povoleno zjišťování z databáze značek Bing. Pokud nejsou značky Bingu povolené, Video Indexer detekuje pouze značky z vlastního modelu značek zadaného účtu.

`useBuiltIn`Příznak nastavený na hodnotu true znamená, že jsou povolené značky Bingu. Pokud `useBuiltin` je hodnota false, značky Bingu jsou zakázané.

### <a name="response"></a>Odpověď

Po úspěšné aktualizaci nastavení modelu značek se nevrátí žádný obsah.

## <a name="next-steps"></a>Další kroky

[Přizpůsobení modelu značek pomocí webu](customize-brands-model-with-website.md)
