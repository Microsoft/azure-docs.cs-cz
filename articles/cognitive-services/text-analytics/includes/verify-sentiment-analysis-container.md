---
title: Ověření instance kontejneru Analýza mínění
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak ověřit instanci kontejneru Analýza mínění.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/21/2019
ms.author: dapine
ms.openlocfilehash: cbc5ad63dd944eb53d3a8052e75744cb5c3709ea
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051190"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>Ověření instance kontejneru Analýza mínění

1. Vyberte kartu **Přehled** a zkopírujte IP adresu.
1. Otevřete novou kartu prohlížeče a zadejte IP adresu. Zadejte `http://<IP-address>:5000 (http://55.55.55.55:5000`například). Zobrazí se Domovská stránka kontejneru, která vám umožní zjistit, že je kontejner spuštěný.

    ![Zobrazte domovskou stránku kontejneru a ověřte, že je spuštěný.](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Kliknutím na odkaz **Popis rozhraní API služby** přejdete na stránku Swagger kontejneru.

1. Zvolte kterékoli rozhraní API pro **post** a vyberte **vyzkoušet**. Zobrazí se parametry, které obsahují tento příklad vstupu:

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

1. Výběrem příkazu **Execute** určete mínění textu.

    Model, který je zabalen v kontejneru, vygeneruje skóre, které je v rozsahu od 0 do 1, kde 0 je záporný mínění a 1 je kladné mínění.

    Vrácená odpověď JSON obsahuje mínění pro aktualizovaný textový vstup:

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

Nyní můžeme sladit dokument `id` dat JSON datové části odpovědi s původním dokumentem `id`datové části požadavku. Skóre více než `0.98` značí velmi pozitivní mínění.