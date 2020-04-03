---
title: Povolení diagnostických protokolů Azure Bastion a práce s ním
description: V tomto článku se dozvíte, jak povolit a pracovat s diagnostickými protokoly Azure Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 97f0cdb1e93ef2ad06d2daa04b2f4893fd5dfac2
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619268"
---
# <a name="enable-and-work-with-bastion-diagnostic-logs"></a>Povolení diagnostických protokolů Bastion a práce s ním

Jako uživatelé připojit k úlohám pomocí Azure Bastion, Bastion můžete protokolovat diagnostiku vzdálených relací. Pomocí diagnostiky pak můžete zobrazit, kteří uživatelé připojení k jakému zatížení, v kolik hodin, odkud a další takové relevantní informace o protokolování. Chcete-li použít diagnostiku, musíte povolit protokoly diagnostiky v Azure Bastion. Tento článek vám pomůže povolit protokoly diagnostiky a potom zobrazit protokoly.

## <a name="enable-the-diagnostics-log"></a><a name="enable"></a>Povolení diagnostického protokolu

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na prostředek Azure Bastion a na stránce Azure Bastion vyberte **nastavení diagnostiky.**

   ![nastavení diagnostiky](./media/diagnostic-logs/1diagnostics-settings.png)
2. Vyberte **Nastavení diagnostiky**, pak vyberte **+Přidat diagnostické nastavení** pro přidání cíle pro protokoly.

   ![přidat diagnostické nastavení](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. Na stránce **Nastavení diagnostiky** vyberte typ účtu úložiště, který se má použít pro ukládání protokolů diagnostiky.

   ![vybrat umístění úložiště](./media/diagnostic-logs/3add-storage-account.png)
4. Po dokončení nastavení bude vypadat podobně jako v tomto příkladu:

   ![ukázková nastavení](./media/diagnostic-logs/4example-settings.png)

## <a name="view-diagnostics-log"></a><a name="view"></a>Zobrazit protokol diagnostiky

Chcete-li získat přístup k protokolům diagnostiky, můžete přímo použít účet úložiště, který jste zadali při povolení nastavení diagnostiky.

1. Přejděte na prostředek účtu úložiště a potom na **Kontejnery**. Zobrazí se **objekt blob s protokoly přehledů-bastionaudit,** který se vytvořil v kontejneru objektů blob účtu úložiště.

   ![nastavení diagnostiky](./media/diagnostic-logs/1-navigate-to-logs.png)
2. Při navigaci do kontejneru se ve svém blogu zobrazují různé složky. Tyto složky označují hierarchii prostředků pro prostředek Azure Bastion.

   ![přidat diagnostické nastavení](./media/diagnostic-logs/2-resource-h.png)
3. Přejděte do úplné hierarchie prostředku Azure Bastion, jehož diagnostika protokoly, které chcete získat přístup nebo zobrazení. 'y =', 'm =', 'd =', 'h =' a 'm =' označují rok, měsíc, den, hodinu a minutu pro diagnostické protokoly.

   ![vybrat umístění úložiště](./media/diagnostic-logs/3-resource-location.png)
4. Vyhledejte soubor json vytvořený službou Azure Bastion, který obsahuje data protokolu diagnostiky pro časové období, na které se přecvádám.

5. Stáhněte soubor json z kontejneru objektů blob úložiště. Příklad položky ze souboru json je uveden níže pro referenci:

   ```json
   { 
   "time":"2019-10-03T16:03:34.776Z",
   "resourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.NETWORK/BASTIONHOSTS/MYBASTION-BASTION",
   "operationName":"Microsoft.Network/BastionHost/connect",
   "category":"BastionAuditLogs",
   "level":"Informational",
   "location":"eastus",
   "properties":{ 
      "userName":"<username>",
      "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36",
      "clientIpAddress":"131.107.159.86",
      "clientPort":24039,
      "protocol":"ssh",
      "targetResourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/LINUX-KEY",
      "subscriptionId":"<subscripionID>",
      "message":"Successfully Connected.",
      "resourceType":"VM",
      "targetVMIPAddress":"172.16.1.5",
      "tunnelId":"<tunnelID>"
   },
   "FluentdIngestTimestamp":"2019-10-03T16:03:34.0000000Z",
   "Region":"eastus",
   "CustomerSubscriptionId":"<subscripionID>"
   }
   ```

## <a name="next-steps"></a>Další kroky

Přečtěte si nejčastější dotazy k [baště](bastion-faq.md).
