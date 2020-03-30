---
title: Odesílání upozornění na e-maily a webhooku pomocí automatického škálování
description: Přečtěte si, jak pomocí akcí automatického škálování volat webové adresy URL nebo odesílat e-mailová oznámení ve službě Azure Monitor.
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: autoscale
ms.openlocfilehash: c82b170bb3801bdc701ed84230db57f5691523ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120684"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Použití akcí automatického škálování k odesílání e-mailů a upozornění webhooku v Azure Monitoru
Tento článek ukazuje, jak nastavit aktivační události, takže můžete volat konkrétní webové adresy URL nebo odesílat e-maily na základě akcí automatického škálování v Azure.  

## <a name="webhooks"></a>Webhooky
Webhooky umožňují směrovat upozornění Azure do jiných systémů pro následné zpracování nebo vlastní oznámení. Například směrování výstrahy na služby, které mohou zpracovat příchozí webový požadavek na odeslání SMS, protokolovat chyby, upozornit tým pomocí chatu nebo zasílání zpráv atd. Identifikátor URI webhooku musí být platný koncový bod HTTP nebo HTTPS.

## <a name="email"></a>E-mail
E-mail lze odeslat na libovolnou platnou e-mailovou adresu. Oznámení budou upozorněni také správci a spolusprávci předplatného, kde je pravidlo spuštěno.

## <a name="cloud-services-and-app-services"></a>Cloudové služby a služby aplikací
Můžete se přihlásit z portálu Azure pro cloudové služby a serverové farmy (App Services).

* Zvolte **měřítko podle metriky.**

![stupnici podle](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Škálovací sady virtuálních počítačů
Pro novější virtuální počítače vytvořené pomocí Správce prostředků (škálovací sady virtuálních počítačů) to můžete nakonfigurovat pomocí rozhraní REST API, šablon Správce prostředků, prostředí PowerShell a rozhraní příkazového příkazu. Rozhraní portálu ještě není k dispozici.
Při použití rozhraní REST API nebo správce prostředků šablony, zahrnout element oznámení v [nastavení automatického škálování](https://docs.microsoft.com/azure/templates/microsoft.insights/2015-04-01/autoscalesettings) s následujícími možnostmi.

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```

| Pole | Povinné? | Popis |
| --- | --- | --- |
| Operace |ano |hodnota musí být "Měřítko" |
| sendToSubscriptionAdministrator |ano |hodnota musí být "true" nebo "false" |
| sendToSubscriptionCoAdministrators |ano |hodnota musí být "true" nebo "false" |
| vlastníEneek |ano |hodnota může být null [] nebo řetězec pole e-mailů |
| webové háčky |ano |hodnota může být null nebo platný Uri |
| serviceUri |ano |platný https Uri |
| properties |ano |hodnota musí {} být prázdná nebo může obsahovat dvojice klíč-hodnota |

## <a name="authentication-in-webhooks"></a>Ověřování v webhookech
Webhook uasadat pomocí ověřování na základě tokenů, kde uložíte webhooku URI s ID tokenu jako parametr dotazu. Například https:\//mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue

## <a name="autoscale-notification-webhook-payload-schema"></a>Schéma datové části datové části automatického škálování oznámení
Při generování oznámení automatického škálování jsou do datové části webhooku zahrnuta následující metadata:

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


| Pole | Povinné? | Popis |
| --- | --- | --- |
| status |ano |Stav, který označuje, že byla vygenerována akce automatického škálování |
| Operace |ano |Pro zvýšení instancí bude "Horizontální navýšení kapacity" a pro snížení v instancích, bude "Scale In" |
| kontext |ano |Kontext akce automatického škálování |
| časové razítko |ano |Časové razítko při aktivaci akce automatického škálování |
| id |Ano |ID Správce prostředků nastavení automatického škálování |
| jméno |Ano |Název nastavení automatického škálování |
| Podrobnosti |Ano |Vysvětlení akce, kterou služba automatického škálování provedla, a změny v počtu instancí |
| subscriptionId |Ano |ID předplatného cílového prostředku, který je škálován |
| resourceGroupName |Ano |Název skupiny prostředků cílového prostředku, který je škálován |
| resourceName |Ano |Název cílového prostředku, který je škálován |
| resourceType |Ano |Tři podporované hodnoty: "microsoft.classiccompute/domainnames/slots/roles" - role cloudové služby, "microsoft.compute/virtualmachinescalesets" - Sady škálování virtuálních počítačů a "Microsoft.Web/serverfarms" - Web App |
| resourceId |Ano |ID Správce prostředků cílového prostředku, který je škálován |
| portalLink |Ano |Odkaz na portál Azure na stránku souhrnu cílového prostředku |
| oldCapacity |Ano |Aktuální (starý) počet instancí při automatickém škálování provedl akci škálování |
| nováKapacita |Ano |Počet nových instancí, který automatické škálování škálování prostředek |
| properties |Ne |Nepovinný parametr. Sada <klíč, dvojice hodnot> (například slovník <řetězec, řetězec>). Pole vlastností je volitelné. Ve vlastním uživatelském rozhraní nebo pracovním postupu založeném na aplikaci Logika můžete zadat klíče a hodnoty, které lze předat pomocí datové části. Alternativní způsob, jak předat vlastní vlastnosti zpět do odchozí webhooku volání je použít webhooku URI sám (jako parametry dotazu) |

