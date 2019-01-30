---
title: Plán obchodní kontinuity podnikových procesů – nástroj QnA Maker
titleSuffix: Azure Cognitive Services
description: Hlavním cílem plán obchodní kontinuity se a vytvořte koncový bod odolné znalostní báze, které by zajistily bez časové prodlevy robota a její použití aplikace.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.openlocfilehash: 66d2b4667adaac6fa5d6bba2a5a78af3a0c2c255
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221109"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Vytvořit plán obchodní kontinuity podnikových procesů pro vaši službu QnA Maker

Hlavním cílem plán obchodní kontinuity se a vytvořte koncový bod odolné znalostní báze, které by zajistily bez časové prodlevy robota a její použití aplikace.

![Nástroj QnA Maker bcp plán](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Základní reprezentovaný nad spočívá v následujícím způsobem:

1. Nastavit dva paralelní [services QnA Maker](../How-To/set-up-qnamaker-service-azure.md) v [spárovaných oblastech Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Služba Azure search primární a sekundární indexy udržujte synchronizované. Ukázky Githubu [tady](https://github.com/pchoudhari/QnAMakerBackupRestore) postup obnovení zálohování Azure indexy.

3. Zálohování pomocí služby Application Insights [průběžný export](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. Po nastavení primární a sekundární zásobníky použít [traffic Manageru](https://docs.microsoft.com/azure/traffic-manager/) konfigurovat dva koncové body a nastavit metodu směrování.

5. Je třeba vytvořit certifikát SSL pro koncový bod služby traffic manager. [Vytvoření vazby certifikátu SSL](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl) v App services.

6. Nakonec použijte koncový bod traffic manager ve Bot nebo aplikaci.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zvolte kapacitu pro vaše nasazení nástroje QnA Maker](../Tutorials/choosing-capacity-qnamaker-deployment.md)
