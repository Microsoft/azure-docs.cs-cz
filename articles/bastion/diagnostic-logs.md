---
title: Povolení a práce s protokoly prostředků Azure bastionu
description: V tomto článku se dozvíte, jak povolit a pracovat s diagnostickými protokoly Azure bastionu.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 1e76fffd17ee565d4103ca8a7bf1523bbd16209d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91445386"
---
# <a name="enable-and-work-with-bastion-resource-logs"></a>Povolení protokolů prostředků služby Bastion a práce s nimi

Uživatelé, kteří se připojují k úlohám pomocí Azure bastionu, můžou bastionu protokolovat diagnostiku vzdálených relací. Pak můžete pomocí diagnostiky zobrazit, kteří uživatelé se k nimž mají připojené úlohy, v jakém čase, kdy a dalších relevantních informací o protokolování. Aby bylo možné používat diagnostiku, musíte povolit diagnostické protokoly v Azure bastionu. Tento článek vám pomůže povolit diagnostické protokoly a pak zobrazit protokoly.

## <a name="enable-the-resource-log"></a><a name="enable"></a>Povolení protokolu prostředků

1. V [Azure Portal](https://portal.azure.com)přejděte do svého prostředku Azure bastionu a na stránce Azure bastionu vyberte **nastavení diagnostiky** .

   ![Snímek obrazovky zobrazující stránku "nastavení diagnostiky".](./media/diagnostic-logs/1diagnostics-settings.png)
2. Vyberte **nastavení diagnostiky** a pak vyberte **+ Přidat nastavení diagnostiky** a přidejte tak cíl pro protokoly.

   ![Snímek obrazovky zobrazující stránku nastavení diagnostiky s vybraným tlačítkem Přidat nastavení diagnostiky](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. Na stránce **nastavení diagnostiky** vyberte typ účtu úložiště, který se má použít k ukládání diagnostických protokolů.

   ![Snímek stránky "nastavení diagnostiky" s oddílem pro výběr umístění úložiště zvýrazněné](./media/diagnostic-logs/3add-storage-account.png)
4. Po dokončení nastavení bude vypadat podobně jako v tomto příkladu:

   ![Příklad nastavení](./media/diagnostic-logs/4example-settings.png)

## <a name="view-diagnostics-log"></a><a name="view"></a>Zobrazit diagnostický protokol

Chcete-li získat přístup k diagnostickým protokolům, můžete přímo použít účet úložiště, který jste zadali, a povolit nastavení diagnostiky.

1. Přejděte k prostředku svého účtu úložiště a potom do **kontejnerů**. V kontejneru objektů BLOB v účtu úložiště se zobrazí objekt BLOB **Insights-logs-bastionauditlogs** .

   ![nastavení diagnostiky](./media/diagnostic-logs/1-navigate-to-logs.png)
2. Při přechodu dovnitř kontejneru uvidíte na svém blogu různé složky. Tyto složky označují hierarchii prostředků pro váš prostředek Azure bastionu.

   ![Přidat nastavení diagnostiky](./media/diagnostic-logs/2-resource-h.png)
3. Přejděte do úplné hierarchie prostředku Azure bastionu, jehož diagnostické protokoly chcete získat nebo zobrazit. "Y =", 'm = ", 'd =", "h =" a 'm = "označují rok, měsíc, den, hodinu a minutu v případě protokolů prostředků.

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
