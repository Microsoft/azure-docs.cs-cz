---
title: Přizpůsobení jazykového modelu pomocí rozhraní API pro indexování videa
titlesuffix: Azure Media Services
description: Přečtěte si, jak přizpůsobit jazykový model pomocí rozhraní API videoindexeru.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/04/2020
ms.author: anzaman
ms.openlocfilehash: 19067bbbaf93c9abc9a9220b09dd482ce9115655
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127979"
---
# <a name="customize-a-language-model-with-the-video-indexer-api"></a>Přizpůsobení jazykového modelu pomocí rozhraní API videoindexeru

Video Indexer umožňuje vytvářet vlastní jazykové modely pro přizpůsobení rozpoznávání řeči nahráním textu adaptace, konkrétně textu z domény, jehož slovní zásobu chcete, aby se motor přizpůsobil. Jakmile táhejte model, budou rozpoznána nová slova uvedená v textu přizpůsobení.

Podrobný přehled a doporučené postupy pro vlastní jazykové modely naleznete v [tématu Přizpůsobení jazykového modelu pomocí nástroje Video Indexer](customize-language-model-overview.md).

Rozhraní API videoindexeru můžete použít k vytvoření a úpravám vlastních jazykových modelů ve vašem účtu, jak je popsáno v tomto tématu. Můžete také použít webové stránky, jak je popsáno v [Přizpůsobit jazykový model pomocí webu Video Indexer](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Vytvoření jazykového modelu

Vytvoření rozhraní API [jazykového modelu](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?) vytvoří nový vlastní jazykový model v zadaném účtu. V tomto hovoru můžete nahrát soubory pro jazykový model. Případně můžete vytvořit jazykový model zde a nahrát soubory pro model později aktualizací jazykového modelu.

> [!NOTE]
> Stále je nutné trénovat model s jeho povolené soubory pro model se dozvíte obsah jeho souborů. Pokyny pro školení jazyka jsou v další části.

Chcete-li odeslat soubory, které mají být přidány do jazykového modelu, musíte nahrát soubory v těle pomocí FormData kromě poskytnutí hodnot pro požadované parametry výše. Tento úkol lze provést dvěma způsoby:

* Klíč bude název souboru a hodnota bude txt soubor.
* Klíč bude název souboru a hodnota bude URL souboru txt.

### <a name="response"></a>Odpověď

Odpověď poskytuje metadata na nově vytvořený jazykový model spolu s metadaty na každém ze souborů modelu podle formátu tohoto příkladu výstupu JSON:

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

## <a name="train-a-language-model"></a>Trénování jazykového modelu

[Trénování jazykového modelu](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train) rozhraní API trénuje vlastní jazykový model v zadaném účtu s obsahem v souborech, které byly odeslány do jazykového modelu a povoleny v tomto jazykovém modelu.

> [!NOTE]
> Nejprve musíte vytvořit jazykový model a nahrát jeho soubory. Soubory můžete nahrát při vytváření jazykového modelu nebo aktualizací jazykového modelu.

### <a name="response"></a>Odpověď

Odpověď poskytuje metadata na nově trénovaný jazykový model spolu s metadaty na každém ze souborů modelu podle formátu tohoto příkladu výstupu JSON:

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

Vrácené `id` je jedinečné ID používané k rozlišení `languageModelId` mezi jazykovými modely, zatímco se používá jak pro [nahrávání videa do indexu,](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) tak pro [reindexování](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) rozhraní API videa (označované také jako `linguisticModelId` rozhraní API pro nahrávání nebo přeindexování videoindexeru).

## <a name="delete-a-language-model"></a>Odstranění jazykového modelu

Odstranění rozhraní API [jazykového modelu](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete) odstraní vlastní jazykový model ze zadaného účtu. Jakékoli video, které používalo odstraněný jazykový model, bude mít stejný index, dokud video nepřeindexujete. Pokud video přeindexujete, můžete k videu přiřadit nový jazykový model. V opačném případě video Indexer použije svůj výchozí model k přeindexování videa.

### <a name="response"></a>Odpověď

Neexistuje žádný vrácený obsah při úspěšném odstranění jazykového modelu.

## <a name="update-a-language-model"></a>Aktualizace jazykového modelu

[Aktualizace jazykového modelu](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update) rozhraní API aktualizuje vlastní jazykovou osobu modelu v zadaném účtu.

> [!NOTE]
> Je nutné, abyste již vytvořili jazykový model. Toto volání můžete použít k povolení nebo zakázání všech souborů v rámci modelu, aktualizaci názvu jazykového modelu a nahrání souborů, které mají být přidány do jazykového modelu.

Chcete-li odeslat soubory, které mají být přidány do jazykového modelu, musíte nahrát soubory v těle pomocí FormData kromě poskytnutí hodnot pro požadované parametry výše. Tento úkol lze provést dvěma způsoby:

* Klíč bude název souboru a hodnota bude txt soubor.
* Klíč bude název souboru a hodnota bude URL souboru txt.

### <a name="response"></a>Odpověď

Odpověď poskytuje metadata na nově trénovaný jazykový model spolu s metadaty na každém ze souborů modelu podle formátu tohoto příkladu výstupu JSON:

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

Pomocí `id` souborů vrácených v odpovědi stáhněte obsah souboru.

## <a name="update-a-file-from-a-language-model"></a>Aktualizace souboru z jazykového modelu

Aktualizace [souboru](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update) umožňuje aktualizovat název `enable` a stav souboru ve vlastním jazykovém modelu v zadaném účtu.

### <a name="response"></a>Odpověď

Odpověď poskytuje metadata v souboru, který jste aktualizovali podle formátu ukázkového výstupu JSON níže.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```

Pomocí `id` souboru vráceného v odpovědi stáhněte obsah souboru.

## <a name="get-a-specific-language-model"></a>Získání konkrétního jazykového modelu

[Get](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) API vrátí informace o zadaný jazykový model v zadaném účtu, jako je například jazyk a soubory, které jsou v jazykovém modelu.

### <a name="response"></a>Odpověď

Odpověď poskytuje metadata na zadaný jazykový model spolu s metadaty na každém souboru modelu podle formátu tohoto příkladu výstupu JSON:

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

Pomocí `id` souboru vráceného v odpovědi stáhněte obsah souboru.

## <a name="get-all-the-language-models"></a>Získejte všechny jazykové modely

[Get all](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get) API vrátí všechny vlastní jazykové modely v zadaném účtu v seznamu.

### <a name="response"></a>Odpověď

Odpověď obsahuje seznam všech jazykových modelů ve vašem účtu a každého z jejich metadat a souborů podle formátu tohoto příkladu výstupu JSON:

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

Rozhraní [DELETE](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete) API odstraní zadaný soubor ze zadaného jazykového modelu v zadaném účtu.

### <a name="response"></a>Odpověď

Neexistuje žádný vrácený obsah při úspěšném odstranění souboru z jazykového modelu.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Získání metadat k souboru z jazykového modelu

Získat [metadata rozhraní](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model) API souboru vrátí obsah a metadata na zadaný soubor z vybraného jazykového modelu ve vašem účtu.

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
> Obsah tohoto ukázkového souboru jsou slova "hello" a world " ve dvou samostatných řádcích.

## <a name="download-a-file-from-a-language-model"></a>Stažení souboru z jazykového modelu

Při stahování rozhraní API [souboru](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?) se stáhne textový soubor obsahující obsah zadaného souboru ze zadaného jazykového modelu v zadaném účtu. Tento textový soubor by měl odpovídat obsahu textového souboru, který byl původně odeslán.

### <a name="response"></a>Odpověď

Odpovědí bude stažení textového souboru s obsahem souboru ve formátu JSON.

## <a name="next-steps"></a>Další kroky

[Přizpůsobení jazykového modelu pomocí webu](customize-language-model-with-website.md)
