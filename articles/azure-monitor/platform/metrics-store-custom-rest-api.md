---
title: Odesílání metrik do databáze metrik Azure Monitoru pomocí rozhraní REST API
description: Odeslání vlastních metrik pro prostředek Azure do úložiště metrik Azure Monitor pomocí rozhraní REST API
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 84709c022631543101889f784231158ebb96b6f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662260"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-by-using-a-rest-api"></a>Odeslání vlastních metrik pro prostředek Azure do úložiště metrik Azure Monitor pomocí rozhraní REST API

Tento článek ukazuje, jak odesílat vlastní metriky pro prostředky Azure do úložiště metrik Azure Monitor prostřednictvím rozhraní REST API. Po metriky jsou ve službě Azure Monitor, můžete dělat všechny věci s nimi, které děláte se standardní metriky. Příkladem jsou vytváření grafů, výstrahy a směrování do jiných externích nástrojů.  

>[!NOTE]  
>Rozhraní REST API povoluje odesílání vlastních metrik pro prostředky Azure. Chcete-li odesílat metriky pro prostředky v různých prostředích nebo v místním prostředí, můžete použít [Application Insights](../../azure-monitor/app/api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Vytvoření a autorizace instančního objektu pro vyzařují metriky 

Vytvořte instanční objekt v tenantovi služby Azure Active Directory pomocí pokynů najdete v části [Vytvořit instanční objekt](../../active-directory/develop/howto-create-service-principal-portal.md). 

Při procházení tímto procesem si všimněte následujícího: 

- Pro přihlašovací adresu URL můžete zadat libovolnou adresu URL.  
- Vytvořte nový tajný klíč klienta pro tuto aplikaci.  
- Uložte klíč a ID klienta pro použití v pozdějších krocích.  

Udělte aplikaci vytvořené jako součást kroku 1, Vydavatele metrik monitorování, oprávnění k prostředku, proti jehož vyzařování metrik. Pokud máte v plánu použít aplikaci k vyzařování vlastní metriky proti mnoha prostředkům, můžete udělit tato oprávnění na úrovni skupiny prostředků nebo předplatného. 

## <a name="get-an-authorization-token"></a>Získání autorizačního tokenu
Otevřete příkazový řádek a spusťte následující příkaz:

```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step>" -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Uložte přístupový token z odpovědi.

![Přístupový token](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>Vyzařovat metriku prostřednictvím rozhraní REST API 

1. Vložte následující jSON do souboru a uložte jej jako **custommetric.json** v místním počítači. Aktualizujte parametr time v souboru JSON: 
    
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

1. V okně příkazového řádku zveřejněte metrická data: 
   - **azureRegion**. Musí odpovídat oblasti nasazení prostředku, pro který vyzařujete metriky. 
   - **resourceID**.  ID prostředku prostředku Azure, proti které metriku sledujete.  
   - **AccessToken**. Vložte token, který jste získali dříve.

     ```Shell 
     curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId>/metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
     ```
1. Změňte časové razítko a hodnoty v souboru JSON. 
1. Opakujte předchozí dva kroky několikrát, takže máte data po dobu několika minut.

## <a name="troubleshooting"></a>Řešení potíží 
Pokud se zobrazí chybová zpráva s určitou částí procesu, zvažte následující informace o řešení potíží:

1. Jako prostředek Azure nelze vydávat metriky proti předplatnému nebo skupině prostředků. 
1. Do obchodu, který je starší než 20 minut, nelze vložit metriku. Úložiště metrik je optimalizované pro upozorňování a vytváření grafů v reálném čase. 
2. Počet názvů dimenzí by měl odpovídat hodnotám a naopak. Zkontrolujte hodnoty. 
2. Je možné, že vyzařujete metriky proti oblasti, která nepodporuje vlastní metriky. Viz [podporované oblasti](../../azure-monitor/platform/metrics-custom-overview.md#supported-regions). 



## <a name="view-your-metrics"></a>Zobrazení metrik 

1. Přihlaste se k portálu Azure. 

1. V levé nabídce vyberte **monitor .** 

1. Na stránce **Monitor** vyberte **Metriky**. 

   ![Vybrat metriky](./media/metrics-store-custom-rest-api/metrics.png) 

1. Změňte období agregace na **Posledních 30 minut**.  

1. V rozevírací nabídce **prostředků** vyberte prostředek, proti který jste metriku vypouštěli.  

1. V rozevírací nabídce **Obory názvů** vyberte **queueprocessing**. 

1. V rozevírací nabídce **metriky** vyberte **QueueDepth**.  

 
## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [vlastních metrikách](../../azure-monitor/platform/metrics-custom-overview.md).

