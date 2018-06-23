---
title: Vytvoření plánu continuty obchodní služby QnA Maker - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Postup vytvoření plánu obchodní kontinuity služby QnA Maker
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: ca6e54b8a8ca8b38e8ef6b1a148f8b2c54bd43da
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343232"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Vytvoření plánu obchodní kontinuity služby QnA Maker

Primární cílem plán kontinuity činnosti je vytvoření odolné znalostní báze knowledge base koncového bodu zajistí žádný výpadek robota nebo aplikace využívající ho.

![Plán bcp QnA Maker](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Na vysoké úrovni nápad reprezentovaný výše je následující:

1. Nastavit dva paralelní [QnA Maker služby](../How-To/set-up-qnamaker-service-azure.md) v [Azure spárovat oblasti](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions).

2. Synchronizujte indexů primární a sekundární Azure search. Použijte ukázku, která githubu [zde](https://github.com/pchoudhari/QnAMakerBackupRestore) zobrazíte postup obnovení zálohování Azure indexy.

3. Zálohování pomocí Application Insights [průběžné export](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-export-telemetry).

4. Jakmile zásobníky primární a sekundární již byly vytvořeny, použijte [traffic Manageru](https://docs.microsoft.com/en-us/azure/traffic-manager/) nakonfigurovat dva koncové body a nastavte metodu směrování.

5. Musíte vytvořit certifikát SSL pro koncový bod správce provozu. [Vytvoření vazby certifikátu SSL](https://docs.microsoft.com/en-us/azure/app-service/app-service-web-tutorial-custom-ssl) v aplikační služby.

6. Nakonec použijte koncový bod manager provoz v robota nebo aplikace.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zvolte kapacitu pro vaše nasazení QnA Maker](../Tutorials/choosing-capacity-qnamaker-deployment.md)
