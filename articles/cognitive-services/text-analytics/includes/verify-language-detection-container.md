---
title: Ověření instance kontejneru rozpoznávání jazyka
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak ověřit instanci kontejneru rozpoznávání jazyků.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: dc52586550f89ddae147d79458584331ed984eea
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876402"
---
### <a name="verify-the-language-detection-container-instance"></a>Ověření instance kontejneru rozpoznávání jazyka

1. Vyberte kartu **Přehled** a zkopírujte adresu IP.
1. Otevřete novou kartu prohlížeče a zadejte adresu IP. Zadejte `http://<IP-address>:5000 (http://55.55.55.55:5000`například ). Zobrazí se domovská stránka kontejneru, která vás dozví, že je kontejner spuštěn.

    ![Zobrazení domovské stránky kontejneru k ověření, zda je spuštěná](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Vyberte odkaz **Popis rozhraní API služby** a přejděte na stránku Swagger kontejneru.

1. Zvolte libovolné z api **POST** a vyberte **Vyzkoušet**. Zobrazí se parametry, které zahrnují tento příklad vstupu:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. Nastavte **showStats** na `true`.

1. Chcete-li určit mínění textu, vyberte **spustit.**

    Model, který je zabalen v kontejneru generuje skóre, které se pohybuje od 0 do 1, kde 0 je negativní mínění a 1 je pozitivní mínění.

    Vrácená odpověď JSON zahrnuje mínění pro aktualizovaný textový vstup:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "English",
              "iso6391Name": "en",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 11,
            "transactionsCount": 1
          }
        },
        {
          "id": "2",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 21,
            "transactionsCount": 1
          }
        },
        {
          "id": "3",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 65,
            "transactionsCount": 1
          }
        },
        {
          "id": "4",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 8,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 4,
        "validDocumentsCount": 4,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 4
      }
    }
    ```

Nyní můžeme korelovat dokumenty dat JSON datové části odpovědi s původními dokumenty `id`datové části požadavku podle jejich odpovídající . Každý dokument je zpracován nezávisle, který `characterCount` obsahuje `transactionCount`různé statistiky, jako jsou a . Navíc každý výsledný dokument má `detectedLanguages` pole `name`s `iso6391Name`, `score` a pro každý zjištěný jazyk. Pokud je zjištěno více `score` jazyků, slouží k určení nejpravděpodobnější jazyk.