---
title: Plán provozní kontinuity – QnA Maker
titleSuffix: Azure Cognitive Services
description: Hlavním cílem plánu kontinuity podnikových aplikací je vytvořit odolný koncový bod ve znalostní bázi Knowledge Base, který by pro něj neměl nic trvat ani to, jestli ho aplikace spotřebovává.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 84f13f7e1d83f1ead00303b694b617d3ba1c8931
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876649"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Vytvoření plánu provozní kontinuity pro vaši službu QnA Maker

Hlavním cílem plánu kontinuity podnikových aplikací je vytvořit odolný koncový bod ve znalostní bázi Knowledge Base, který by pro něj neměl nic trvat ani to, jestli ho aplikace spotřebovává.

![Plán QnA Maker BCP](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Nejdůležitější nápad, jak je znázorněno výše, je následující:

1. Nastavte dvě paralelní [QnA maker služby](../How-To/set-up-qnamaker-service-azure.md) v [spárovaných oblastech Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Udržování primárních a sekundárních indexů Azure Search v synchronizaci. Pomocí ukázky na GitHubu [tady](https://github.com/pchoudhari/QnAMakerBackupRestore) zjistíte, jak zálohovat a obnovit indexy Azure.

3. Zazálohujte Application Insights pomocí [průběžného exportu](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. Po nastavení primárních a sekundárních zásobníků nakonfigurujte pomocí [Traffic Manageru](https://docs.microsoft.com/azure/traffic-manager/) dva koncové body a nastavte metodu směrování.

5. Je potřeba vytvořit certifikát SSL pro koncový bod služby Traffic Manager. [Navažte certifikát SSL](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl) ve svých App Services.

6. Nakonec v robotu nebo v aplikaci použijte koncový bod Traffic Manageru.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Volba kapacity pro nasazení QnA Maker](../Tutorials/choosing-capacity-qnamaker-deployment.md)
