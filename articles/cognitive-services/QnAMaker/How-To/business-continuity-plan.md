---
title: Plán kontinuity provozu - QnA Maker
description: Primárním cílem plánu kontinuity podnikání je vytvořit odolný koncový bod znalostní báze, který by zajistil, že bot nebo aplikace, která ji spotřebuje, nebude promítat čas.
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 5d1501ecc42fe948959075cec7d728f6c9df908a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887060"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Vytvoření plánu kontinuity provozu pro službu QnA Maker

Primárním cílem plánu kontinuity podnikání je vytvořit odolný koncový bod znalostní báze, který by zajistil, že bot nebo aplikace, která ji spotřebuje, nebude promítat čas.

## <a name="business-continuity-with-traffic-manager"></a>Kontinuita provozu s dopravním manažerem

> [!div class="mx-imgBorder"]
> ![Plán bcp výrobce QnA](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Myšlenka na vysoké úrovni, jak je znázorněna výše, je následující:

1. Nastavte dvě paralelní [služby QnA Maker](set-up-qnamaker-service-azure.md) ve [spárovaných oblastech Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. [Zálohujte](../../../app-service/manage-backup.md) primární službu QnA Maker App service a [obnovte](../../../app-service/web-sites-restore.md) ji v sekundárním nastavení. Tím zajistíte, že obě nastavení budou pracovat se stejným názvem hostitele a klíči.

3. Udržujte primární a sekundární indexy hledání Azure synchronizované. Tady se podívejte [na](https://github.com/pchoudhari/QnAMakerBackupRestore) ukázku GitHubu, jak zálohovat a obnovovat indexy Azure.

4. Zálohujte přehledy aplikací pomocí [průběžného exportu](../../../application-insights/app-insights-export-telemetry.md).

5. Po nastavení primárnía sekundární zásobníky, použijte [Traffic Manager](../../../traffic-manager/traffic-manager-overview.md) nakonfigurovat dva koncové body a nastavit metodu směrování.

6. Budete muset vytvořit zabezpečení transportní vrstvy (TLS), dříve známý jako SSL (Secure Sockets Layer), certifikát pro koncový bod správce provozu. [Svažte certifikát TLS/SSL](../../../app-service/configure-ssl-bindings.md) ve svých službách aplikací.

7. Nakonec použijte koncový bod správce provozu ve vašem botu nebo aplikaci.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Volba kapacity](./improve-knowledge-base.md)