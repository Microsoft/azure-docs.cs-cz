---
title: Podpora kontejnerů
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak ověřit instanci kontejneru analýzy mínění.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: f68d9c7098f2b1ca782e2522c632c2e267b35336
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455140"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>Ověření instance kontejneru analýzu subjektivního hodnocení

1. Vyberte **přehled** kartu a zkopírujte IP adresu.
1. Otevře nová karta prohlížeče a použijte IP adresu, například `http://<IP-address>:5000 (http://55.55.55.55:5000`). Domovská stránka kontejneru se zobrazí, oznamující, kontejner je spuštěná.

    ![Zobrazit domovskou stránku kontejneru a ověřte, zda že je spuštěna](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Vyberte **popis rozhraní API služby** odkaz přejděte na stránku swagger kontejnery.

1. Vybrat kteroukoli z **příspěvek** rozhraní API a vyberte **vyzkoušet**.  Parametry jsou zobrazeny, včetně příklad vstupu:

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

1. Vstup nahraďte následujícím kódem JSON:

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

    Model zabalené v kontejneru vygeneruje skóre od 0 do 1, kde je záporná 0 a 1 je kladný.

    Vrácená odpověď JSON obsahuje subjektivního hodnocení pro aktualizovaný textový vstup:

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

Nyní jsme mohli porovnat dokumentu `id` z odpovědi datové části JSON v původním dokumentu datové části požadavku `id`a podívejte se, že byl bodů přes `.98` označující velmi pozitivní mínění.