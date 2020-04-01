---
title: Plán kontinuity provozu - QnA Maker
titleSuffix: Azure Cognitive Services
description: Primárním cílem plánu kontinuity podnikání je vytvořit odolný koncový bod znalostní báze, který by zajistil, že bot nebo aplikace, která ji spotřebuje, nebude promítat čas.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 2100426923418104c31933bf9d1406a6441d3841
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410917"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Vytvoření plánu kontinuity provozu pro službu QnA Maker

Primárním cílem plánu kontinuity podnikání je vytvořit odolný koncový bod znalostní báze, který by zajistil, že bot nebo aplikace, která ji spotřebuje, nebude promítat čas.

![Plán bcp výrobce QnA](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Myšlenka na vysoké úrovni, jak je znázorněna výše, je následující:

1. Nastavte dvě paralelní [služby QnA Maker](../How-To/set-up-qnamaker-service-azure.md) ve [spárovaných oblastech Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Udržujte primární a sekundární indexy hledání Azure synchronizované. Tady se podívejte [na](https://github.com/pchoudhari/QnAMakerBackupRestore) ukázku GitHubu, jak zálohovat a obnovovat indexy Azure.

3. Zálohujte přehledy aplikací pomocí [průběžného exportu](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. Po nastavení primárnía sekundární zásobníky, použijte [Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) nakonfigurovat dva koncové body a nastavit metodu směrování.

5. Budete muset vytvořit zabezpečení transportní vrstvy (TLS), dříve známý jako SSL (Secure Sockets Layer), certifikát pro koncový bod správce provozu. [Svažte certifikát TLS/SSL](https://docs.microsoft.com/azure/app-service/configure-ssl-bindings) ve svých službách aplikací.

6. Nakonec použijte koncový bod správce provozu ve vašem botu nebo aplikaci.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyberte si capactiy](./improve-knowledge-base.md)
