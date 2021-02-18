---
title: Odeslat metriky do databáze metriky Azure Monitor pomocí REST API
description: Odesílat vlastní metriky pro prostředek Azure do úložiště metrik Azure Monitor pomocí REST API
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: c315cc8c9dfcfa66999ff263fab95f414061e54e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609807"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-by-using-a-rest-api"></a>Odesílat vlastní metriky pro prostředek Azure do úložiště metrik Azure Monitor pomocí REST API

V tomto článku se dozvíte, jak odesílat vlastní metriky pro prostředky Azure do úložiště Azure Monitor metriky prostřednictvím REST API. Až budou metriky v Azure Monitor, můžete s nimi provádět všechny akce, které se budou používat se standardními metrikami. Příklady jsou grafy, výstrahy a směrování do jiných externích nástrojů.  

>[!NOTE]  
>REST API povoluje pouze odesílání vlastních metrik pro prostředky Azure. Pokud chcete odesílat metriky pro prostředky v různých prostředích nebo v místním prostředí, můžete použít [Application Insights](../app/api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Vytvoření a autorizace instančního objektu pro vygenerování metrik 

Pomocí pokynů, které najdete v tématu [Vytvoření instančního objektu](../../active-directory/develop/howto-create-service-principal-portal.md), vytvořte v tenantovi Azure Active Directory objekt služby. 

Při procházení tohoto procesu mějte na paměti následující: 

- Pro přihlašovací adresu URL můžete zadat libovolnou adresu URL.  
- Vytvoří nový tajný klíč klienta pro tuto aplikaci.  
- Uložte klíč a ID klienta pro použití v pozdějších krocích.  

Poskytněte aplikaci vytvořenou jako součást kroku 1, monitoruje vydavatele metrik a oprávnění k prostředku, na který chcete metriky vygenerovat. Pokud plánujete použití aplikace k vygenerování vlastních metrik pro mnoho prostředků, můžete tato oprávnění udělit na úrovni skupiny prostředků nebo předplatného. 

## <a name="get-an-authorization-token"></a>Získání autorizačního tokenu
Otevřete příkazový řádek a spusťte následující příkaz:

```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step>" -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Uložte přístupový token z odpovědi.

![Přístupový token](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>Generování metriky prostřednictvím REST API 

1. Vložte následující JSON do souboru a uložte ho jako **custommetric.jsna** místním počítači. Aktualizujte časový parametr v souboru JSON: 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20", 
        "data": { 
            "baseData": { 
                "metric": "QueueDepth", 
                "namespace": "QueueProcessing", 
                "dimNames": [ 
                  "QueueName", 
                  "MessageType" 
                ], 
                "series": [ 
                  { 
                    "dimValues": [ 
                      "ImagesToProcess", 
                      "JPEG" 
                    ], 
                    "min": 3, 
                    "max": 20, 
                    "sum": 28, 
                    "count": 3 
                  } 
                ] 
            } 
        } 
    } 
    ``` 

1. V okně příkazového řádku publikujte data metriky: 
   - **a**. Musí se shodovat s oblastí nasazení prostředku, pro který vydáváte metriky. 
   - **ResourceID**.  ID prostředku prostředku Azure, na kterém sledujete metriku  
   - **AccessToken**. Vložte token, který jste získali dříve.

     ```Shell 
     curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId>/metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
     ```
1. Změna časového razítka a hodnot v souboru JSON. 
1. Předchozí dva kroky zopakujte několikrát, takže budete mít data několik minut.

## <a name="troubleshooting"></a>Řešení potíží 
Pokud se vám v některé části procesu zobrazí chybová zpráva, vezměte v úvahu následující informace pro řešení potíží:

1. Nemůžete vystavovat metriky pro předplatné nebo skupinu prostředků jako prostředek Azure. 
1. Nemůžete vložit metriku do úložiště, které je starší než 20 minut. Úložiště metrik je optimalizované pro upozorňování a vytváření grafů v reálném čase. 
2. Počet názvů dimenzí by měl odpovídat hodnotám a naopak. Ověřte hodnoty. 
2. Můžete vysílat metriky v oblasti, která nepodporuje vlastní metriky. Viz [podporované oblasti](./metrics-custom-overview.md#supported-regions). 



## <a name="view-your-metrics"></a>Zobrazit vaše metriky 

1. Přihlaste se k portálu Azure. 

1. V nabídce na levé straně vyberte **monitor**. 

1. Na stránce **monitor** vyberte **metriky**. 

   ![Vybrat metriky](./media/metrics-store-custom-rest-api/metrics.png) 

1. Změňte období agregace na **posledních 30 minut**.  

1. V rozevírací nabídce **prostředek** vyberte prostředek, oproti kterému jste tuto metriku vygenerovali.  

1. V rozevírací nabídce **obory názvů** vyberte **QueueProcessing**. 

1. V rozevírací nabídce **metriky** vyberte **QueueDepth**.  

 
## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [vlastních metrikách](./metrics-custom-overview.md).

