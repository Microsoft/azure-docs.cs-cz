---
title: Ověření instance kontejneru analýzu subjektivního hodnocení
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak ověřit instanci kontejneru analýzy mínění.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: f69d573e9e70a505018e94cca354f363097cc1b8
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229281"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>Ověření instance kontejneru analýzu subjektivního hodnocení

1. Vyberte **přehled** kartu a zkopírujte IP adresu.
1. Otevře nová karta prohlížeče a zadejte IP adresu. For example, zadejte `http://<IP-address>:5000 (http://55.55.55.55:5000`). Zobrazí se domovská stránka kontejneru, oznamující, kontejner je spuštěná.

    ![Zobrazit domovskou stránku kontejneru a ověřte, zda je spuštěna](../media/how-tos/container-instance/swagger-docs-on-container.png).

1. Vyberte **popis rozhraní API služby** odkaz přejděte na stránku swagger kontejneru.

1. Vybrat kteroukoli z **příspěvek** rozhraní API a vyberte **vyzkoušet**.  Zobrazí se parametry, včetně tento příklad vstupu:

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

1. Nahraďte následující obsah JSON vstupu:

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

1. Nastavte **showStats** na hodnotu true.

1. Vyberte **Execute** určit mínění textu.

    Model, který je zabalený ve kontejneru vygeneruje skóre od 0 do 1, kde je záporná 0 a 1 je kladný.

    Odpověď JSON, který je vrácen obsahuje subjektivního hodnocení pro aktualizovaný textový vstup:

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

Nyní jsme mohli porovnat dokumentu `id` datové části odpovědi JSON dat do původního dokumentu datové části požadavku `id`. Můžeme vidět skóre z více než `.98`, určující velmi pozitivní mínění.