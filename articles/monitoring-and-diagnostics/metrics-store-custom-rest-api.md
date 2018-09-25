---
title: Odeslat vlastní metriky pro prostředek Azure do Azure monitoru metriky ukládat pomocí rozhraní REST API
description: Odeslat vlastní metriky pro prostředek Azure do Azure monitoru metriky ukládat pomocí rozhraní REST API
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: c01440437eae3cb076627ab9f2221e33b833f472
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977227"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-using-a-rest-api"></a>Odeslat vlastní metriky pro prostředek Azure do Azure monitoru metriky ukládat pomocí rozhraní REST API

Tento článek ukazuje, jak odeslat vlastní metriky pro prostředky Azure k úložišti Azure Monitor metriky přes rozhraní REST API.  Jakmile metriky ve službě Azure Monitor, lze vše, co s nimi, kterou děláte pomocí standardních metrik, jako je například vytváření grafů, výstrahy, směrování do dalších externích nástrojích, atd.  

>[!NOTE] 
>Rozhraní REST API povoluje odesílání vlastní metriky pro prostředky Azure. Chcete-li odeslat metriky pro prostředky v různých prostředích nebo místně, můžete použít [Application Insights](../application-insights/app-insights-api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Vytvoření a autorizaci instančního objektu pro generování metriky 

Vytvořit instanční objekt ve vašem tenantovi Azure Active Directory pomocí pokynů uvedených v [vytvoření instančního objektu](../azure-resource-manager/resource-group-create-service-principal-portal.md). 

Mějte na paměti následující při procházení tohoto procesu: 

- Můžete vložit libovolnou adresu URL pro adresu URL přihlašování.  
- Vytvořit nový tajný kód klienta pro tuto aplikaci  
- Uložte klíč a ID klienta pro použití v dalších krocích.  

Dejte aplikaci vytvořenou jako součást kroku 1 "Monitorování metrik vydavatele" oprávnění k prostředku, kterou chcete vygenerovat metrik pro. Pokud máte v plánu používat aplikace a vygenerovat vlastní metriky s mnoha prostředky, můžete udělit tato oprávnění na úrovni skupiny nebo předplatného resource. 

## <a name="get-an-authorization-token"></a>Získat autorizační token
Otevřete příkazový řádek a spusťte následující příkaz
```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step> " -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Uložit přístupový token z odpovědi

![Přístupový token](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>Generovat metriky přes rozhraní REST API 

1. Do souboru vložte následující kód JSON a uložte ho jako custommetric.json v místním počítači. Aktualizujte parametr času v souboru JSON. 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20”, 
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

1. V okně příkazového řádku publikovat data metriky 
    - Oblasti Azure – musí odpovídat oblast nasazení prostředku, který emitujete metriky pro. 
    - ResourceID – ID prostředku Azure, které sledujete metriky oproti prostředku.  
    - Přístupový Token – vložte token získané dříve

    ```Shell 
    curl -X POST curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId> /metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
    ```
1. Změňte časové razítko a hodnot v souboru JSON. 
1. Opakujte předchozí dva kroky několikrát tak budete mít data za několik minut.

## <a name="troubleshooting"></a>Řešení potíží 
Pokud se zobrazí chyba s některé části procesu, vzhledem k tomu následující

1. Nelze vydat metrik pro předplatné nebo skupinu prostředků jako váš prostředek Azure. 
1. Metrika nelze umístit do úložiště, který je více než 20 minut. Metriky úložiště je optimalizovaný pro výstrahy a vytváření grafů v reálném čase. 
2. Počet dimenzí názvů by měl odpovídat hodnoty a naopak. Zkontrolujte hodnoty. 
2. Může být generování metrik pro oblast, která nepodporuje vlastní metriky. Zobrazit [podporované oblasti vlastní metriky (preview)](metrics-custom-overview.md#supported-regions) 



## <a name="view-your-metrics"></a>Zobrazit metriky 

1. Přihlášení k portálu Azure Portal 

1. V nabídce vlevo klikněte na tlačítko **monitorování** 

1. Klikněte na stránce monitorování **metriky**. 

   ![Přístupový token](./media/metrics-store-custom-rest-api/metrics.png) 

1. Na období agregace změnit **posledních 30 minut**.  

1. V *prostředků* rozevíracího seznamu, vyberte prostředek vygenerován metriky oproti.  

1. V *obory názvů* rozevíracího seznamu, vyberte **QueueProcessing** 

1. V *metriky* rozevírací seznam, vyberte **QueueDepth**.  

 
## <a name="next-steps"></a>Další postup
- Další informace o [vlastní metriky](metrics-custom-overview.md).