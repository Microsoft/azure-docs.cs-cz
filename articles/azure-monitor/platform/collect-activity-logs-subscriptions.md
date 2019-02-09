---
title: Shromažďování protokolů aktivit Azure do Log Analytics napříč předplatnými | Microsoft Docs
description: Použijte služby Event Hubs a Logic Apps ke shromažďování dat z protokolu aktivit Azure a jejich odesílání do pracovního prostoru Log Analytics v jiném tenantovi.
services: log-analytics, logic-apps, event-hubs
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: magoedte
ms.openlocfilehash: 4d336f657baef99d97568ba722e64a1f4440367f
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984575"
---
# <a name="collect-azure-activity-logs-into-log-analytics-across-subscriptions-in-different-azure-active-directory-tenants"></a>Shromažďování protokolů aktivit Azure do Log Analytics napříč předplatnými v různých tenantů Azure Active Directory

Tento článek prochází metodu shromažďování protokolů aktivit Azure do pracovního prostoru Log Analytics pomocí konektoru Kolekce dat Azure Log Analytics pro Logic Apps. V tomto článku pomocí procesu, když potřebujete odesílat protokoly do pracovního prostoru v jiném tenantovi Azure Active Directory. Například pokud jste poskytovatel spravované služby, můžete chtít shromažďovat protokoly aktivit z předplatného zákazníka a ukládat je do pracovního prostoru Log Analytics ve vlastním předplatném.

Pokud je pracovní prostor Log Analytics ve stejném předplatném Azure nebo v jiném předplatném, ale ve stejné službě Azure Active Directory, použijte ke shromažďování protokolů aktivit Azure postup uvedený v tématu [Řešení protokolu aktivit Azure](collect-activity-logs.md).

## <a name="overview"></a>Přehled

Strategie použitá v tomto scénáři spočívá v odesílání událostí protokolu aktivit Azure do [centra událostí](../../event-hubs/event-hubs-about.md), ze kterého je [aplikace logiky](../../logic-apps/logic-apps-overview.md) odesílá do vašeho prostoru Log Analytics. 

![obrázek toku dat z protokolu aktivit do Log Analytics](media/collect-activity-logs-subscriptions/data-flow-overview.png)

Mezi výhody tohoto přístupu patří:
- Nízká latence, protože se protokol aktivit Azure streamuje do centra událostí.  Následně se aktivuje aplikace logiky, která odešle data do Log Analytics. 
- Vyžaduje se minimální množství kódu a není potřeba nasazovat žádnou serverovou infrastrukturu.

Tento článek vás provede následujícími postupy:
1. Vytvoření centra událostí 
2. Export protokolů aktivit do centra událostí s využitím profilu exportu protokolu aktivit Azure
3. Vytvoření aplikace logiky, která načítá události z centra událostí a odesílá je do Log Analytics

## <a name="requirements"></a>Požadavky
Následují požadavky na prostředky Azure použité v tomto scénáři.

- Obor názvů centra událostí nemusí být ve stejném předplatném, jako je předplatné, které vysílá protokoly. Uživatel, který konfiguruje nastavení, musí mít odpovídající přístupová oprávnění k oběma předplatným. Pokud ve stejné službě Azure Active Directory máte více předplatných, můžete odesílat protokoly aktivit ze všech předplatných do jednoho centra událostí.
- Aplikace logiky může být v jiném předplatném než centrum událostí a nemusí být ve stejné službě Azure Active Directory. Aplikace logiky ke čtení z centra událostí používá sdílený přístupový klíč centra událostí.
- Pracovní prostor Log Analytics může být v jiném předplatném a službě Azure Active Directory než aplikace logiky, ale pro zjednodušení doporučujeme použít pro ně stejné předplatné. Aplikace logiky k odesílání do Log Analytics používá ID a klíč pracovního prostoru Log Analytics.



## <a name="step-1---create-an-event-hub"></a>Krok 1 – Vytvoření centra událostí

<!-- Follow the steps in [how to create an Event Hubs namespace and Event Hub](../../event-hubs/event-hubs-create.md) to create your event hub. -->

1. Na webu Azure Portal vyberte **Vytvořit prostředek** > **Internet věcí** > **Event Hubs**.

   ![Nové centrum událostí na Marketplace](media/collect-activity-logs-subscriptions/marketplace-new-event-hub.png)

3. V části **Vytvořit obor názvů** zadejte nový obor názvů nebo vyberte existující. Systém okamžitě kontroluje, jestli je název dostupný.

   ![obrázek dialogového okna pro vytvoření centra událostí](media/collect-activity-logs-subscriptions/create-event-hub1.png)

4. Zvolte pro nový prostředek cenovou úroveň (Basic nebo Standard), předplatné Azure, skupinu prostředků a umístění.  Kliknutím na **Vytvořit** vytvoříte obor názvů. Možná budete muset několik minut počkat, než systém úplně zřídí prostředky.
6. V seznamu klikněte na právě vytvořený obor názvů.
7. Vyberte **Zásady sdíleného přístupu** a pak klikněte na **RootManageSharedAccessKey**.

   ![obrázek se zásadami sdíleného přístupu k centru událostí](media/collect-activity-logs-subscriptions/create-event-hub7.png)
   
8. Kliknutím na tlačítko kopírovat zkopírujte připojovací řetězec **RootManageSharedAccessKey** do schránky. 

   ![obrázek se sdíleným přístupovým klíčem centra událostí](media/collect-activity-logs-subscriptions/create-event-hub8.png)

9. Do dočasného umístění, například do Poznámkového bloku, si uložte kopii názvu centra událostí a primárního nebo sekundárního připojovacího řetězce centra událostí. Tyto hodnoty vyžaduje aplikace logiky.  Jako připojovací řetězec centra událostí můžete použít připojovací řetězec **RootManageSharedAccessKey** nebo vytvořit nový.  Připojovací řetězec, který použijete, musí začínat na `Endpoint=sb://` a být určený pro zásadu obsahující zásady přístupu pro **správu**.


## <a name="step-2---export-activity-logs-to-event-hub"></a>Krok 2 – Export protokolů aktivit do centra událostí

Pokud chcete povolit streamování protokolu aktivit, vyberte obor názvů centra událostí a zásadu sdíleného přístup pro tento obor názvů. Centrum událostí se v tomto oboru názvů vytvoří při výskytu první nové události protokolu aktivit. 

Můžete použít obor názvů centra událostí, který není ve stejném předplatném, jako je předplatné, které vysílá protokoly, ale obě předplatná musí být ve stejné službě Azure Active Directory. Uživatel, který konfiguruje nastavení, musí mít odpovídající roli RBAC pro přístup k oběma předplatným. 

1. Na webu Azure Portal vyberte **Monitorování** > **Protokol aktivit**.
3. Klikněte na tlačítko **Exportovat** v horní části stránky.

   ![obrázek monitorování azure v navigaci](media/collect-activity-logs-subscriptions/activity-log-blade.png)

4. Vyberte **Předplatné**, ze kterého se má exportovat, a pak kliknutím na **Vybrat vše** v rozevírací nabídce **Oblasti** vyberte události všech prostředků ve všech oblastech. Klikněte na zaškrtávací políčko **Exportovat do centra událostí**.
7. Klikněte na **Obor názvů služby Service Bus** a pak vyberte **Předplatné** s centrem událostí, **obor názvů centra událostí** a **název zásady centra událostí**.

    ![obrázek stránky pro export do centra událostí](media/collect-activity-logs-subscriptions/export-activity-log2.png)

11. Kliknutím na **OK** a pak na **Uložit** nastavení uložte. Nastavení se okamžitě použijí pro vaše předplatné.

<!-- Follow the steps in [stream the Azure Activity Log to Event Hubs](../../azure-monitor/platform/activity-logs-stream-event-hubs.md) to configure a log profile that writes activity logs to an event hub. -->

## <a name="step-3---create-logic-app"></a>Krok 3 – Vytvoření aplikace logiky

Jakmile protokoly aktivit zapisují do centra událostí, vytvoříte aplikaci logiky, která bude shromažďovat protokoly z centra událostí a zapisovat je do Log Analytics.

Aplikace logiky se skládá z následujících částí:
- Trigger [konektoru centra událostí](https://docs.microsoft.com/connectors/eventhubs/) pro čtení z centra událostí.
- [Akce Parsovat JSON](../../logic-apps/logic-apps-content-type.md) pro extrahování událostí JSON.
- [Akce Vytvořit](../../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) pro převod formátu JSON na objekt.
- [Konektor pro odesílání dat do Log Analytics](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/).

   ![obrázek přidání triggeru centra událostí ve službě Logic Apps](media/collect-activity-logs-subscriptions/log-analytics-logic-apps-activity-log-overview.png)

### <a name="logic-app-requirements"></a>Požadavky aplikace logiky
Před vytvořením aplikace logiky se ujistěte, že z předchozích kroků máte následující informace:
- Název centra událostí
- Připojovací řetězec centra událostí (primární nebo sekundární) pro obor příslušný názvů centra událostí
- ID pracovního prostoru Log Analytics
- Sdílený klíč Log Analytics

Pokud chcete získat název a připojovací řetězec centra událostí, postupujte podle kroků v části [Kontrola oprávnění oboru názvů služby Event Hubs a vyhledání připojovacího řetězce](../../connectors/connectors-create-api-azure-event-hubs.md#permissions-connection-string).


### <a name="create-a-new-blank-logic-app"></a>Vytvoření nové prázdné aplikace logiky

1. Na webu Azure Portal zvolte **Vytvořit prostředek** > **Podniková integrace** > **Aplikace logiky**.

    ![nová aplikace logiky na Marketplace](media/collect-activity-logs-subscriptions/marketplace-new-logic-app.png)

2. Zadejte nastavení uvedená v následující tabulce.

    ![Vytvoření aplikace logiky](media/collect-activity-logs-subscriptions/create-logic-app.png)

   |Nastavení | Popis  |
   |:---|:---|
   | Název           | Jedinečný název aplikace logiky. |
   | Předplatné   | Vyberte předplatné Azure, které bude obsahovat aplikaci logiky. |
   | Skupina prostředků | Vyberte některou z existujících skupin prostředků Azure nebo pro aplikaci logiky vytvořte novou. |
   | Umístění       | Vyberte oblast datového centra pro nasazení aplikace logiky. |
   | Log Analytics  | Vyberte, jestli chcete v Log Analytics protokolovat stav každého spuštění aplikace logiky.  |

    
3. Vyberte **Vytvořit**. Po zobrazení oznámení **Nasazení bylo úspěšné** klikněte na **Přejít k prostředku** a otevřete svůj prostředek.

4. V části **Šablony** zvolte **Prázdná aplikace logiky**. 

V Návrháři pro Logic Apps se teď zobrazí dostupné konektory a jejich triggery, pomocí kterých spouštíte pracovní postup aplikace logiky.

<!-- Learn [how to create a logic app](../../logic-apps/quickstart-create-first-logic-app-workflow.md). -->

### <a name="add-event-hub-trigger"></a>Přidání triggeru centra událostí

1. Do vyhledávacího pole v Návrháři pro Logic Apps zadejte jako filtr *event hubs*. Vyberte trigger **Event Hubs – Když jsou k dispozici události v centru událostí** .

   ![obrázek přidání triggeru centra událostí ve službě Logic Apps](media/collect-activity-logs-subscriptions/logic-apps-event-hub-add-trigger.png)

2. Po zobrazení výzvy k zadání přihlašovacích údajů se připojte ke svému oboru názvů služby Event Hubs. Zadejte název připojení a pak připojovací řetězec, který jste si zkopírovali.  Vyberte **Vytvořit**.

   ![obrázek přidání připojení k centru událostí ve službě Logic Apps](media/collect-activity-logs-subscriptions/logic-apps-event-hub-add-connection.png)

3. Po vytvoření připojení upravte nastavení triggeru. Začněte tím, že v rozevírací nabídce **Název centra událostí** vyberete **insights-operational-logs**.

   ![Dialogové okno Když jsou k dispozici události](media/collect-activity-logs-subscriptions/logic-apps-event-hub-read-events.png)

5. Rozbalte položku **Zobrazit pokročilé možnosti** a změňte **Typ obsahu** na *application/json*.

   ![Dialogové okno konfigurace centra událostí](media/collect-activity-logs-subscriptions/logic-apps-event-hub-configuration.png)

### <a name="add-parse-json-action"></a>Přidání akce Parsovat JSON

Výstup z centra událostí obsahuje datovou část JSON s polem záznamů. Akce [Parsovat JSON](../../logic-apps/logic-apps-content-type.md) slouží k extrahování pouze pole záznamů pro odeslání do Log Analytics.

1. Klikněte na **Nový krok** > **Přidat akci**.
2. Do vyhledávacího pole zadejte jako filtr *parsovat json*. Vyberte akci **Operace s daty – Parsovat JSON**.

   ![Přidání akce Parsovat JSON ve službě Logic Apps](media/collect-activity-logs-subscriptions/logic-apps-add-parse-json-action.png)

3. Klikněte do pole **Obsah** a vyberte *Text*.

4. Zkopírujte a vložte následující schéma do pole **Schéma**.  Toto schéma odpovídá výstupu z akce centra událostí.  

   ![Dialogové okno Parsovat JSON](media/collect-activity-logs-subscriptions/logic-apps-parse-json-configuration.png)

``` json-schema
{
    "properties": {
        "body": {
            "properties": {
                "ContentData": {
                    "type": "string"
                },
                "Properties": {
                    "properties": {
                        "ProfileName": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                },
                "SystemProperties": {
                    "properties": {
                        "EnqueuedTimeUtc": {
                            "type": "string"
                        },
                        "Offset": {
                            "type": "string"
                        },
                        "PartitionKey": {},
                        "SequenceNumber": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "type": "object"
        },
        "headers": {
            "properties": {
                "Cache-Control": {
                    "type": "string"
                },
                "Content-Length": {
                    "type": "string"
                },
                "Content-Type": {
                    "type": "string"
                },
                "Date": {
                    "type": "string"
                },
                "Expires": {
                    "type": "string"
                },
                "Location": {
                    "type": "string"
                },
                "Pragma": {
                    "type": "string"
                },
                "Retry-After": {
                    "type": "string"
                },
                "Timing-Allow-Origin": {
                    "type": "string"
                },
                "Transfer-Encoding": {
                    "type": "string"
                },
                "Vary": {
                    "type": "string"
                },
                "X-AspNet-Version": {
                    "type": "string"
                },
                "X-Powered-By": {
                    "type": "string"
                },
                "x-ms-request-id": {
                    "type": "string"
                }
            },
            "type": "object"
        }
    },
    "type": "object"
}
```

>[!TIP]
> Ukázkovou datovou část můžete získat kliknutím na **Spustit** a zobrazením **nezpracovaného výstupu** z centra událostí.  Tento výstup pak můžete použít v akci **Použít ukázkovou datovou část k vygenerování schématu** v aktivitě **Parsovat JSON** k vygenerování schématu.

### <a name="add-compose-action"></a>Přidání akce Vytvořit
Akce [Vytvořit](../../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) převezme výstup JSON a vytvoří z něj objekt, který je možné použít v akci Log Analytics.

1. Klikněte na **Nový krok** > **Přidat akci**.
2. Jako filtr zadejte *vytvořit* a pak vyberte akci **Operace s daty – Vytvořit**.

    ![Přidání akce Vytvořit](media/collect-activity-logs-subscriptions/logic-apps-add-compose-action.png)

3. Klikněte do pole **Vstupy** a v části aktivity **Parsovat JSON** vyberte **Text**.


### <a name="add-log-analytics-send-data-action"></a>Přidání akce Odeslat data do Log Analytics
Akce [Kolekce dat Azure Log Analytics](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) převezme objekt z akce Vytvořit a odešle ho do Log Analytics.

1. Klikněte na **Nový krok** > **Přidat akci**.
2. Jako filtr zadejte *log analytics* a pak vyberte akci **Kolekce dat Azure Log Analytics – Odeslat data**.

   ![Přidání akce Odeslat data do Log Analytics ve službě Logic Apps](media/collect-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-connector.png)

3. Zadejte název připojení a vložte **ID pracovního prostoru** a **Klíč pracovního prostoru** pro váš pracovní prostor Log Analytics.  Klikněte na možnost **Vytvořit**.

   ![Přidání připojení k Log Analytics ve službě Logic Apps](media/collect-activity-logs-subscriptions/logic-apps-log-analytics-add-connection.png)

4. Po vytvoření připojení upravte nastavení podle následující tabulky. 

    ![Konfigurace akce Odeslat data](media/collect-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-configuration.png)

   |Nastavení        | Hodnota           | Popis  |
   |---------------|---------------------------|--------------|
   |Text požadavku JSON  | **Výstup** z akce **Vytvořit** | Načte záznamy z textu akce Vytvořit. |
   | Název vlastního protokolu | AzureActivity | Název vlastní tabulky protokolů, která se vytvoří v Log Analytics pro uchovávání importovaných dat. |
   | Pole Čas vygenerování | time | Jako hodnotu **time** nevybírejte pole JSON, stačí zadat slovo time. Pokud vyberete pole JSON, návrhář vloží akci **Odeslat data** do smyčky *For Each*, což nechcete. |




10. Kliknutím na **Uložit** uložte provedené změny aplikace logiky.

## <a name="step-4---test-and-troubleshoot-the-logic-app"></a>Krok 4 – Test aplikace logiky a řešení potíží
Když je pracovní postup dokončený, můžete ho v návrháři otestovat a ověřit, že funguje bez chyby.

V Návrháři pro Logic Apps klikněte na **Spustit** a otestujte aplikaci logiky. U každého kroku v aplikaci logiky se zobrazí ikona stavu, kdy bílá značka zaškrtnutí v zeleném kroužku značí úspěch.

   ![Test aplikace logiky](media/collect-activity-logs-subscriptions/test-logic-app.png)

Pokud chcete zobrazit podrobné informace o jednotlivých krocích, kliknutím na název krok rozbalte. Kliknutím na **Zobrazit nezpracované vstupy** a **Zobrazit nezpracované výstupy** zobrazíte další informace o přijímaných a odesílaných datech v každém kroku.

## <a name="step-5---view-azure-activity-log-in-log-analytics"></a>Krok 5 – Zobrazení protokolu aktivit Azure v Log Analytics
Posledním krokem je kontrola pracovního prostoru Log Analytics a ověření, že se data shromažďují podle očekávání.

1. Na webu Azure Portal klikněte v levém horním rohu na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
2. V seznamu pracovních prostorů Log Analytics vyberte svůj pracovní prostor.
3.  Klikněte na dlaždici **Prohledávání protokolu**, v podokně Prohledávání protokolu zadejte do pole dotazu `AzureActivity_CL` a stiskněte Enter nebo klikněte na tlačítko Vyhledat napravo od pole dotazu. Pokud jste pro vlastní protokol nepoužili název *AzureActivity*, zadejte název, který jste zvolili, s příponou `_CL`.

>[!NOTE]
> Po prvním odeslání nového vlastního protokolu do Log Analytics může trvat až hodinu, než bude vlastní protokol prohledávatelný.

>[!NOTE]
> Protokoly aktivit se zapisují do vlastní tabulky a nezobrazují se v [řešení protokolu aktivit](./collect-activity-logs.md).


![Test aplikace logiky](media/collect-activity-logs-subscriptions/log-analytics-results.png)

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořili aplikaci logiky, která načítá protokoly aktivit Azure z centra událostí a odesílá je k analýze do Log Analytics. Další informace o vizualizaci dat v Log Analytics, včetně vytváření řídicích panelů, najdete v kurzu vizualizace dat.

> [!div class="nextstepaction"]
> [Kurz vizualizace dat prohledávání protokolů](./../../azure-monitor/learn/tutorial-logs-dashboards.md)
