---
title: Povolení a práce s protokoly diagnostiky Azure bastionu
description: V tomto článku se dozvíte, jak povolit a pracovat s diagnostickými protokoly Azure bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 2167a17d5d388c97ad357398c4ac2676e43be5a4
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989446"
---
# <a name="enable-and-work-with-bastion-diagnostic-logs"></a>Povolení a práce s diagnostickými protokoly bastionu

Uživatelé, kteří se připojují k úlohám pomocí Azure bastionu, můžou bastionu protokolovat diagnostiku vzdálených relací. Pak můžete pomocí diagnostiky zobrazit, kteří uživatelé se k nimž mají připojené úlohy, v jakém čase, kdy a dalších relevantních informací o protokolování. Aby bylo možné používat diagnostiku, musíte povolit diagnostické protokoly v Azure bastionu. Tento článek vám pomůže povolit diagnostické protokoly a pak zobrazit protokoly.

## <a name="enable"></a>Povolit diagnostický protokol

1. V [Azure Portal](https://portal.azure.com)přejděte do svého prostředku Azure bastionu a na stránce Azure bastionu vyberte **nastavení diagnostiky** .

   ![nastavení diagnostiky](./media/diagnostic-logs/1diagnostics-settings.png)
2. Vyberte **nastavení diagnostiky**a pak vyberte **+ Přidat nastavení diagnostiky** a přidejte tak cíl pro protokoly.

   ![Přidat nastavení diagnostiky](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. Na stránce **nastavení diagnostiky** vyberte typ účtu úložiště, který se má použít k ukládání diagnostických protokolů.

   ![vybrat umístění úložiště](./media/diagnostic-logs/3add-storage-account.png)
4. Po dokončení nastavení bude vypadat podobně jako v tomto příkladu:

   ![Příklad nastavení](./media/diagnostic-logs/4example-settings.png)

## <a name="view"></a>Zobrazit diagnostický protokol

Chcete-li získat přístup k diagnostickým protokolům, můžete přímo použít účet úložiště, který jste zadali, a povolit nastavení diagnostiky.

1. Přejděte k prostředku svého účtu úložiště a potom do **kontejnerů**. V kontejneru objektů BLOB v účtu úložiště se zobrazí objekt BLOB **Insights-logs-bastionauditlogs** .

   ![nastavení diagnostiky](./media/diagnostic-logs/1-navigate-to-logs.png)
2. Při přechodu dovnitř kontejneru uvidíte na svém blogu různé složky. Tyto složky označují hierarchii prostředků pro váš prostředek Azure bastionu.

   ![Přidat nastavení diagnostiky](./media/diagnostic-logs/2-resource-h.png)
3. Přejděte do úplné hierarchie prostředku Azure bastionu, jehož diagnostické protokoly chcete získat nebo zobrazit. "Y =", 'm = ", 'd =", "h =" a 'm = "označují rok, měsíc, den, hodinu a minutu v případě diagnostických protokolů.

   ![vybrat umístění úložiště](./media/diagnostic-logs/3-resource-location.png)
4. Vyhledejte soubor JSON vytvořený pomocí Azure bastionu, který obsahuje data diagnostického protokolu pro časový interval, na který přejdete.

5. Stáhněte soubor JSON z kontejneru objektů BLOB úložiště. Příklad položky ze souboru JSON je uveden níže pro referenci:

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

Přečtěte si [Nejčastější dotazy k bastionu](bastion-faq.md).
