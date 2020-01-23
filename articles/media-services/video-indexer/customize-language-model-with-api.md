---
title: Použití rozhraní API pro Video Indexer k přizpůsobení jazykových modelů – Azure
titlesuffix: Azure Media Services
description: V tomto článku se dozvíte, jak přizpůsobit jazykový model pomocí rozhraní Video Indexer API.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: e8df7ffd285b0d49f5d4a87585e769b5b0bbafe9
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513146"
---
# <a name="customize-a-language-model-with-the-video-indexer-apis"></a>Přizpůsobení jazykového modelu pomocí Video Indexer rozhraní API

Video Indexer vám umožní vytvářet vlastní jazykové modely pro přizpůsobení rozpoznávání řeči odesláním přizpůsobeného textu, konkrétně textu z domény, kde chcete, aby se modul přizpůsobil. Jakmile svůj model vytvoříte, budou se nová slova uvedená v textu přizpůsobení rozpoznat. 

Podrobný přehled a osvědčené postupy pro vlastní jazykové modely najdete v tématu [přizpůsobení jazykového modelu pomocí video indexer](customize-language-model-overview.md).

Rozhraní Video Indexer API můžete použít k vytvoření a úpravě vlastních jazykových modelů v účtu, jak je popsáno v tomto tématu. Web můžete také použít, jak je popsáno v tématu [přizpůsobení jazykového modelu pomocí webu video indexer](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Vytvoření jazykového modelu

Rozhraní API pro [Vytvoření jazykového modelu](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?) vytvoří nový vlastní jazykový model v zadaném účtu. V tomto volání můžete odeslat soubory pro jazykový model. Alternativně můžete vytvořit jazykový model zde a později nahrávat soubory pro model aktualizací jazykového modelu.

> [!NOTE]
> I když model budete chtít zjistit obsah svých souborů, musíte si i nadále proškolit model s povolenými soubory. Pokyny pro školení jazyka najdete v další části.

Chcete-li nahrát soubory, které mají být přidány do jazykového modelu, je nutné odeslat soubory do těla pomocí formuláře-data kromě poskytování hodnot požadovaných parametrů výše. Chcete-li to provést dvěma způsoby: 

1. Klíč bude název souboru a hodnota bude soubor txt.
2. Klíč bude název souboru a hodnota bude adresa URL souboru txt.

### <a name="response"></a>Odpověď

Odpověď poskytuje metadata nově vytvořeného jazykového modelu spolu s metadaty každého souboru modelu v následujícím formátu jako příklad výstupu JSON.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>Výuka jazykového modelu

Rozhraní API pro [jazykové modely](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train) vlaky nahraje vlastní jazykový model v zadaném účtu s obsahem souborů, které se nahrály do a povolily v jazykovém modelu. 

> [!NOTE]
> Nejprve musíte vytvořit jazykový model a nahrát jeho soubory. Soubory můžete nahrát buď při vytváření jazykového modelu, nebo aktualizací jazykového modelu. 

### <a name="response"></a>Odpověď

Odpověď poskytuje metadata nově vyučeného jazykového modelu spolu s metadaty v každém ze souborů modelu, které následují po formátu ukázkového výstupu JSON.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Při [nahrávání videa do indexu](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) a parametru **languageModelId** při [Reindexování videa](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)byste měli použít hodnotu vráceného **ID** modelu jazyka pro parametr **linguisticModelId** .

 
## <a name="delete-a-language-model"></a>Odstranění jazykového modelu

Rozhraní API pro [odstranění jazykových modelů](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete) odstraní z určeného účtu vlastní jazykový model. Všechna videa, která používala odstraněný jazykový model, budou mít stejný index, dokud video znovu neindexujte. Pokud video znovu indexovat, můžete video přiřadit nový jazykový model. V opačném případě Video Indexer použije výchozí model k opakovanému indexování videa.

### <a name="response"></a>Odpověď

Po úspěšném odstranění jazykových modelů se nevrátí žádný obsah.

## <a name="update-a-language-model"></a>Aktualizace jazykového modelu

Aktualizace rozhraní API [jazykových modelů](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update) aktualizuje v zadaném účtu vlastní model osoby jazyka.

> [!NOTE]
> Je nutné, abyste již vytvořili jazykový model. Pomocí tohoto volání můžete povolit nebo zakázat všechny soubory v modelu, aktualizovat název jazykového modelu a odeslat soubory, které mají být přidány do jazykového modelu.

Chcete-li nahrát soubory, které mají být přidány do jazykového modelu, je nutné odeslat soubory do těla pomocí formuláře-data kromě poskytování hodnot požadovaných parametrů výše. Chcete-li to provést dvěma způsoby: 

1. Klíč bude název souboru a hodnota bude soubor txt.
2. Klíč bude název souboru a hodnota bude adresa URL souboru txt.


### <a name="response"></a>Odpověď

Odpověď poskytuje metadata nově vyučeného jazykového modelu spolu s metadaty v každém ze souborů modelu, které následují po formátu ukázkového výstupu JSON.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Pro stažení obsahu souboru použijte **ID** souborů vrácených v odpovědi.

## <a name="update-a-file-from-a-language-model"></a>Aktualizace souboru z jazykového modelu

[Aktualizace souboru](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update) umožňuje aktualizovat název a **Povolit** stav souboru v rámci vlastního jazykového modelu v zadaném účtu.

### <a name="response"></a>Odpověď

Odpověď poskytuje metadata souboru, který jste aktualizovali podle formátu ukázkového výstupu JSON níže.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```
K stažení obsahu souboru použijte **ID** souboru vráceného v odpovědi.

## <a name="get-a-specific-language-model"></a>Získat konkrétní jazykový model

Rozhraní [Get](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) API vrátí informace o zadaném jazykovém modelu v zadaném účtu, jako je například jazyk a soubory, které jsou v jazykovém modelu. 

### <a name="response"></a>Odpověď

Odpověď poskytuje metadata v zadaném jazykovém modelu spolu s metadaty každého souboru modelu podle formátu ukázkového výstupu JSON níže.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

K stažení obsahu souboru použijte **ID** souboru vráceného v odpovědi.

## <a name="get-all-the-language-models"></a>Získat všechny jazykové modely

Rozhraní [Get All](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get) API vrátí všechny vlastní jazykové modely v zadaném účtu v seznamu.

### <a name="response"></a>Odpověď

Odpověď poskytuje seznam všech jazykových modelů ve vašem účtu a každé z jejich metadat a souborů podle formátu ukázkového výstupu JSON.

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>Odstranění souboru z jazykového modelu

Rozhraní API pro [odstranění](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete) Odstraní zadaný soubor ze zadaného jazykového modelu v zadaném účtu. 

### <a name="response"></a>Odpověď

Po úspěšném odstranění souboru z jazykového modelu se nevrátí žádný obsah.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Získání metadat pro soubor z jazykového modelu

[Metadata Get souboru](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model) rozhraní API vrátí obsah a metadata ze zadaného souboru z vybraného jazykového modelu ve vašem účtu.

### <a name="response"></a>Odpověď

Odpověď poskytuje obsah a metadata souboru ve formátu JSON, podobně jako v tomto příkladu:

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> Obsah tohoto ukázkového souboru jsou slova "Hello" a World "ve dvou samostatných řádcích.

## <a name="download-a-file-from-a-language-model"></a>Stažení souboru z jazykového modelu

[Stažení souborového](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?) rozhraní API stáhne textový soubor obsahující obsah zadaného souboru ze zadaného jazykového modelu v zadaném účtu. Tento textový soubor by měl odpovídat obsahu textového souboru, který byl původně nahrán.

### <a name="response"></a>Odpověď

Odpověď bude stahovat textový soubor s obsahem souboru ve formátu JSON. 

## <a name="next-steps"></a>Další kroky

[Přizpůsobení jazykového modelu pomocí webu](customize-language-model-with-website.md)
