---
title: Plán provozní kontinuity – QnA Maker
description: Hlavním cílem plánu kontinuity podnikových aplikací je vytvořit odolný koncový bod ve znalostní bázi Knowledge Base, který by pro něj neměl nic trvat ani to, jestli ho aplikace spotřebovává.
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 5d1501ecc42fe948959075cec7d728f6c9df908a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80887060"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Vytvoření plánu provozní kontinuity pro vaši službu QnA Maker

Hlavním cílem plánu kontinuity podnikových aplikací je vytvořit odolný koncový bod ve znalostní bázi Knowledge Base, který by pro něj neměl nic trvat ani to, jestli ho aplikace spotřebovává.

## <a name="business-continuity-with-traffic-manager"></a>Kontinuita podnikových aplikací pomocí Traffic Manageru

> [!div class="mx-imgBorder"]
> ![Plán QnA Maker BCP](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Nejdůležitější nápad, jak je znázorněno výše, je následující:

1. Nastavte dvě paralelní [QnA maker služby](set-up-qnamaker-service-azure.md) v [spárovaných oblastech Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. [Zálohujte](../../../app-service/manage-backup.md) primární QnA maker App Service a [obnovte](../../../app-service/web-sites-restore.md) ji v sekundární instalaci. Tím se zajistí, že obě nastavení budou fungovat se stejným názvem hostitele a klíči.

3. Udržování primárních a sekundárních indexů Azure Search v synchronizaci. Pomocí ukázky na GitHubu [tady](https://github.com/pchoudhari/QnAMakerBackupRestore) zjistíte, jak zálohovat a obnovit indexy Azure.

4. Zazálohujte Application Insights pomocí [průběžného exportu](../../../application-insights/app-insights-export-telemetry.md).

5. Po nastavení primárních a sekundárních zásobníků nakonfigurujte pomocí [Traffic Manageru](../../../traffic-manager/traffic-manager-overview.md) dva koncové body a nastavte metodu směrování.

6. Museli byste vytvořit protokol TLS (Transport Layer Security), dříve označovaný jako SSL (Secure Sockets Layer) (SSL), certifikát pro koncový bod služby Traffic Manager. [Navažte certifikát TLS/SSL](../../../app-service/configure-ssl-bindings.md) ve vašich aplikačních službách.

7. Nakonec v robotu nebo v aplikaci použijte koncový bod Traffic Manageru.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Volba kapacity](./improve-knowledge-base.md)