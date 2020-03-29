---
title: Ověření instance kontejneru Analýzy mínění
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak ověřit instanci kontejneru Analýzy mínění.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 2e201b4ec0d1364ea99b376171efabad65af0a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "70968560"
---
### <a name="verify-the-sentiment-analysis-container-instance"></a>Ověření instance kontejneru Analýzy mínění

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

1. Nahraďte vstup následujícím obsahem JSON:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
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
        "id": "7",
        "score": 0.9826303720474243,
        "statistics": {
          "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

Nyní můžeme korelovat `id` dokument dat JSON datové části odpovědi s původním `id`dokumentem datové části požadavku . Skóre více než `0.98` naznačuje velmi pozitivní sentiment.