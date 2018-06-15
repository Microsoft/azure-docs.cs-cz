---
title: Ukázka výstrahy akce Webhooku v OMS Log Analytics | Microsoft Docs
description: Jednu z akcí, můžete spustit v reakci na výstrahy analýzy protokolů je * webhooku *, které umožňuje vyvolání externího procesu prostřednictvím jedné žádosti HTTP. Tento článek vás provede příklad vytvoření webhooku akce výstrahou analýzy protokolů pomocí Slack.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 13c39f0f-fd3c-472d-8324-ddf7538be45e
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: bwren
ms.openlocfilehash: 55b66132f7ec5c26c0a7cac1ec0a5c403dbd1082
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23855170"
---
# <a name="create-an-alert-webhook-action-in-oms-log-analytics-to-send-message-to-slack"></a>Vytvoří akci, výstrah webhooku v OMS analýzy protokolů k odeslání zprávy na Slack
Jednu z akcí můžete spustit v reakci na [analýzy protokolů výstraha](log-analytics-alerts.md) je *webhooku*, která umožňuje vyvolání externího procesu prostřednictvím jedné žádosti HTTP.  Další informace o podrobnosti o výstrahy a pomocí webhooků v [výstrahy v analýzy protokolů](log-analytics-alerts.md)

V tomto článku se budeme zabývat příklad vytvoření webhooku akce výstrahou analýzy protokolů pomocí Slack, což je službou zasílání zpráv.

> [!NOTE]
> Musíte mít účet Slack k dokončení této ukázce.  Můžete si zaregistrovat bezplatný účet v [slack.com](http://slack.com).
> 
> 

## <a name="step-1---enable-webhooks-in-slack"></a>Krok 1 – povolit webhooky v Slack
1. Přihlaste se k systému Slack v [slack.com](http://slack.com).
2. Vyberte kanál v **kanály** část v levém podokně.  Toto je kanál, který bude zaslána zpráva.  Můžete vybrat jednu z výchozích kanálů, jako **Obecné** nebo **náhodných**.  V případě produkční by pravděpodobně vytvoříte speciální kanál, jako **criticalservicealerts**. <br>
   
   ![Kanálů slack](media/log-analytics-alerts-webhooks/oms-webhooks01.png)
3. Klikněte na tlačítko **přidání aplikace nebo vlastní integrace** otevřete adresář aplikace.
4. Typ *webhooky* do vyhledávacího pole a pak vyberte **příchozí Webhooky**. <br>
   
   ![Kanálů slack](media/log-analytics-alerts-webhooks/oms-webhooks02.png)
5. Klikněte na tlačítko **nainstalovat** vedle vašeho jména týmu.
6. Klikněte na tlačítko **Přidat konfiguraci**.
7. Vyberte kanálu, který budete používat pro tento příklad, a pak klikněte na **přidat příchozí Webhooky integrace**.  
8. Kopírování **adresa URL Webhooku**.  Budete vkládat to do konfigurace výstrahy. <br>
   
    ![Kanálů slack](media/log-analytics-alerts-webhooks/oms-webhooks05.png)

## <a name="step-2---create-alert-rule-in-log-analytics"></a>Krok 2 – vytvořte pravidlo výstrahy v analýzy protokolů
1. [Vytvořit pravidlo výstrahy](log-analytics-alerts.md) s následujícím nastavením.
   * Dotaz:```    Type=Event EventLevelName=error ```
   * Kontrolovat tuto výstrahu každých: 5 minut
   * Počet výsledků je: větší než 10
   * Přes tento časový interval: 60 minut
   * Vyberte **Ano** pro **Webhooku** a **ne** pro další akce.
2. Vložte adresu URL Slack do **adresa URL Webhooku** pole.
3. Vyberte možnost **zahrnout vlastní datovou část JSON**.
4. Zvětralého očekává datové části ve formátu JSON s parametrem s názvem *text*.  Toto je text, který se zobrazí ve zprávě, kterou vytvoří.  Můžete použít jeden nebo více parametrů výstrah pomocí  *#*  symbolů, jako v následujícím příkladu.
   
    ```
    {
    "text":"#alertrulename fired with #searchresultcount records which exceeds the over threshold of #thresholdvalue ."
    }
    ```
   
    ![Příklad datové části JSON](media/log-analytics-alerts-webhooks/oms-webhooks07.png)
5. Klikněte na tlačítko **Uložit** uložte pravidlo výstrahy.
6. Počkejte, než dostatek času pro výstrahu určenou k vytvořit a potom zkontrolujte Slack pro zprávu, která bude podobný následujícímu.
   
   ![Příklad webhooku v Slack](media/log-analytics-alerts-webhooks/oms-webhooks08.png)

### <a name="advanced-webhook-payload-for-slack"></a>Rozšířené datové části webhooku pro Slack
Můžete upravit hojně příchozí zprávy s Slack. Další informace najdete v tématu [příchozí Webhooky](https://api.slack.com/incoming-webhooks) na webu Slack. Toto je složitější datové části pro vytvoření bohaté zprávy s formátování:

    {
        "attachments": [
            {
                "title":"OMS Alerts Custom Payload",
                "fields": [
                    {
                        "title": "Alert Rule Name",
                        "value": "#alertrulename"},
                    {
                        "title": "Link To SearchResults",
                        "value": "<#linktosearchresults|OMS Search Results>"},
                    {
                        "title": "Search Interval",
                        "value": "#searchinterval"},
                    {
                        "title": "Threshold Operator",
                        "value": "#thresholdoperator"},
                    {
                        "title": "Threshold Value",
                        "value": "#thresholdvalue"}
                ],
                "color": "#F35A00"
            }
        ]
    }


To by vygeneroval zprávu v systému Slack podobný následujícímu.

![Příklad zprávy v systému Slack](media/log-analytics-alerts-webhooks/oms-webhooks09.png)

## <a name="summary"></a>Souhrn
S pravidlo výstrahy na místě budete mít zprávy odeslané k systému Slack pokaždé, když je splněna kritéria.  

Toto je pouze jeden z příkladů akci, která můžete vytvořit v reakci na výstrahy.  Můžete vytvořit webhooku akce, která volá jinou službu externí, runbook akci pro spuštění sady runbook ve službě Azure Automation nebo e-mailu akci, kterou chcete odeslat e-mail s sobě nebo ostatní příjemci.   

## <a name="next-steps"></a>Další kroky
* Další informace o dalších [výstrahy akce v analýzy protokolů](log-analytics-alerts-actions.md) včetně dalších akcí.


