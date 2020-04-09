---
title: Ověření instance kontejneru extrakce klíčových frází
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak ověřit instanci kontejneru extrakce klíčových frází.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5c177517ec18d7526b1cc09da74e35cb5434766d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876400"
---
### <a name="verify-the-key-phrase-extraction-container-instance"></a>Ověření instance kontejneru extrakce klíčových frází

1. Vyberte kartu **Přehled** a zkopírujte adresu IP.
1. Otevřete novou kartu prohlížeče a zadejte adresu IP. Zadejte `http://<IP-address>:5000 (http://55.55.55.55:5000`například ). Zobrazí se domovská stránka kontejneru, která vás dozví, že je kontejner spuštěn.

    ![Zobrazení domovské stránky kontejneru k ověření, zda je spuštěná](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Vyberte odkaz **Popis rozhraní API služby** a přejděte na stránku Swagger kontejneru.

1. Zvolte libovolné z api **POST** a vyberte **Vyzkoušet**. Zobrazí se parametry, které zahrnují tento příklad vstupu:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello world"
        },
        {
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        },
        {
          "id": "4",
          "text": ":) :( :D"
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

    Model, který je zabalen v kontejneru generuje skóre, které se pohybuje od 0 do 1, kde 0 je negativní a 1 je pozitivní.

    Vrácená odpověď JSON zahrnuje mínění pro aktualizovaný textový vstup:

    ```json
    {
      "documents": [
        {
          "id": "7",
          "keyPhrases": [
            "Great people",
            "great sessions",
            "KubeCon Conference",
            "Barcelona",
            "best conferences"
          ],
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

Nyní můžeme korelovat `id` dokument dat JSON datové části odpovědi s původním `id`dokumentem datové části požadavku . Výsledný dokument má `keyPhrases` pole, které obsahuje seznam klíčových frází, které byly extrahovány z odpovídajícího vstupního dokumentu. Kromě toho existují různé statistiky, jako `characterCount` je a `transactionCount` pro každý výsledný dokument.