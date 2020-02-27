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
ms.openlocfilehash: f920a789f1ec631c5d70863b10d8364b3eb81b6c
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650465"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Vytvoření plánu provozní kontinuity pro vaši službu QnA Maker

Hlavním cílem plánu kontinuity podnikových aplikací je vytvořit odolný koncový bod ve znalostní bázi Knowledge Base, který by pro něj neměl nic trvat ani to, jestli ho aplikace spotřebovává.

![Plán QnA Maker BCP](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Nejdůležitější nápad, jak je znázorněno výše, je následující:

1. Nastavte dvě paralelní [QnA maker služby](../How-To/set-up-qnamaker-service-azure.md) v [spárovaných oblastech Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Udržování primárních a sekundárních indexů Azure Search v synchronizaci. Pomocí ukázky na GitHubu [tady](https://github.com/pchoudhari/QnAMakerBackupRestore) zjistíte, jak zálohovat a obnovit indexy Azure.

3. Zazálohujte Application Insights pomocí [průběžného exportu](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. Po nastavení primárních a sekundárních zásobníků nakonfigurujte pomocí [Traffic Manageru](https://docs.microsoft.com/azure/traffic-manager/) dva koncové body a nastavte metodu směrování.

5. Pro koncový bod služby Traffic Manager byste museli vytvořit certifikát SSL (Secure Sockets Layer) (SSL). [Navažte certifikát SSL](https://docs.microsoft.com/azure/app-service/configure-ssl-bindings) ve svých App Services.

6. Nakonec v robotu nebo v aplikaci použijte koncový bod Traffic Manageru.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zvolit capactiy](./improve-knowledge-base.md)
