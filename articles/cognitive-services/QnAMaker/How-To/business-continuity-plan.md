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
ms.openlocfilehash: 0e748e81de39b2bef14b543063adeb51b8b3ecdb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486709"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Vytvoření plánu provozní kontinuity pro vaši službu QnA Maker

Hlavním cílem plánu kontinuity podnikových aplikací je vytvořit odolný koncový bod ve znalostní bázi Knowledge Base, který by pro něj neměl nic trvat ani to, jestli ho aplikace spotřebovává.

![Plán QnA Maker BCP](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Nejdůležitější nápad, jak je znázorněno výše, je následující:

1. Nastavte dvě paralelní [QnA maker služby](../How-To/set-up-qnamaker-service-azure.md) v [spárovaných oblastech Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Udržování primárních a sekundárních indexů Azure Search v synchronizaci. Pomocí ukázky na GitHubu [tady](https://github.com/pchoudhari/QnAMakerBackupRestore) zjistíte, jak zálohovat a obnovit indexy Azure.

3. Zazálohujte Application Insights pomocí [průběžného exportu](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. Po nastavení primárních a sekundárních zásobníků nakonfigurujte pomocí [Traffic Manageru](https://docs.microsoft.com/azure/traffic-manager/) dva koncové body a nastavte metodu směrování.

5. Je potřeba vytvořit certifikát SSL pro koncový bod služby Traffic Manager. [Navažte certifikát SSL](https://docs.microsoft.com/azure/app-service/configure-ssl-bindings) ve svých App Services.

6. Nakonec v robotu nebo v aplikaci použijte koncový bod Traffic Manageru.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Volba kapacity pro nasazení QnA Maker](../Tutorials/choosing-capacity-qnamaker-deployment.md)
