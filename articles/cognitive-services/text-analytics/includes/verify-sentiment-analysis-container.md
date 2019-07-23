---
title: Ověření instance kontejneru analýzy mínění
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak ověřit instanci kontejneru analýzy mínění.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1303d753b1cbfabe7ddd3442e0880b0bffe089b3
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377446"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>Ověření instance kontejneru analýzy mínění

1. Vyberte kartu **Přehled** a zkopírujte IP adresu.
1. Otevřete novou kartu prohlížeče a zadejte IP adresu. Zadejte `http://<IP-address>:5000 (http://55.55.55.55:5000`například). Zobrazí se Domovská stránka kontejneru, která vám umožní zjistit, že je kontejner spuštěný.

    ![Zobrazte domovskou stránku kontejneru a ověřte, že je spuštěný.](../media/how-tos/container-instance/swagger-docs-on-container.png).

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

1. Nastavte **showStats** na true.

1. Výběrem příkazu **Execute** určete mínění textu.

    Model, který je zabalen v kontejneru, vygeneruje skóre, které je v rozsahu od 0 do 1, kde 0 je záporné a 1 je kladné.

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

Nyní můžeme sladit dokument `id` dat JSON datové části odpovědi s původním dokumentem `id`datové části požadavku. Skóre více než `.98` značí silně pozitivní mínění.