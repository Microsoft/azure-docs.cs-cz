---
title: Poslat e-mail a webhook oznámení o výstrahách pomocí automatického škálování
description: 'Zjistit, jak pomocí akcí automatického škálování můžete volat webové adresy URL nebo odeslat e-mailová oznámení ve službě Azure Monitor. '
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/03/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: c655f117009c0c7c682ad92a951edc7e12255bc2
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53326045"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Pomocí akcí automatického škálování můžete poslat e-mail a webhook oznámení výstrah ve službě Azure Monitor
Tento článek popisuje, jak nastavit aktivační události tak, aby je volání adresy URL konkrétních webů nebo odeslat e-mailů na základě akcí automatického škálování v Azure.  

## <a name="webhooks"></a>Webhooky
Webhooky umožňují směrování Azure oznámení výstrah do jiných systémů pro následné zpracování nebo vlastních oznámení. Například směrování výstraze služby, které může zpracovávat příchozí webového požadavku k odeslání že serveru SMS, protokol chyb, upozornění týmu pomocí chat nebo zasílání zpráv služby atd. Webhook identifikátor URI musí být platný koncový bod HTTP nebo HTTPS.

## <a name="email"></a>Email
E-mailu můžete odeslaný na jakoukoli platnou e-mailovou adresu. Správci a spolusprávci předplatného, ve kterém je spuštěno pravidlo, budete také upozorněni.

## <a name="cloud-services-and-web-apps"></a>Cloud Services a webových aplikací
Je můžete vyjádřit výslovný souhlas na webu Azure Portal pro Cloud Services a serverové farmy (webové aplikace).

* Zvolte **škálovat** metriku.

![můžete škálovat](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Škálovací sady virtuálních počítačů
Pro novější virtuální počítače vytvořené pomocí Resource Manageru (škálovací sady virtuálních počítačů) to můžete nakonfigurovat pomocí rozhraní REST API, šablon Resource Manageru, Powershellu a rozhraní příkazového řádku. Na portálu rozhraní ještě není k dispozici.
Při použití rozhraní REST API nebo Resource Manageru šablony, zahrnují elementu oznámení s těmito možnostmi.

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
| operace |ano |Hodnota musí být "Škálovací" |
| sendToSubscriptionAdministrator |ano |Hodnota musí být "true" nebo "false" |
| sendToSubscriptionCoAdministrators |ano |Hodnota musí být "true" nebo "false" |
| customEmails |ano |Hodnota může být null [] nebo pole řetězců e-mailů |
| Webhooky |ano |Hodnota může být null nebo platný identifikátor Uri |
| serviceUri |ano |platný identifikátor Uri protokolu https |
| properties |ano |Hodnota musí být prázdná {} nebo může obsahovat páry klíč hodnota |

## <a name="authentication-in-webhooks"></a>Ověřování v webhooků
Webhook můžete ověřit pomocí ověřování založeného na tokenech, kam jste uložili webhooku identifikátor URI s token ID jako parametr dotazu. Například https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue.

## <a name="autoscale-notification-webhook-payload-schema"></a>Schéma datová část webhooku oznámení automatického škálování
Při generování oznámení automatického škálování je datová část webhooku součástí následující metadata:

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
| status |ano |Stav, který určuje, zda byla vygenerována akce automatického škálování |
| operace |ano |Pro zvýšení instancí je "Horizontální navýšení kapacity" a snížení v instancích, bude "Škálování v" |
| Kontext |ano |Kontext akce automatického škálování |
| časové razítko |ano |Časové razítko při aktivaci akce automatického škálování |
| id |Ano |ID správce prostředků zadaném nastavení automatického škálování |
| jméno |Ano |Název nastavení automatického škálování |
| Podrobnosti |Ano |Vysvětlení akci, která trvala služby automatického škálování a změna v počtu instancí |
| subscriptionId |Ano |ID předplatného cílového prostředku, který je právě škálovat |
| resourceGroupName |Ano |Název skupiny prostředků cílového prostředku, který je právě škálovat |
| resourceName |Ano |Název cílového prostředku, který je právě škálovat |
| Typ prostředku |Ano |Tři podporované hodnoty: "microsoft.classiccompute/domainnames/slots/roles" – Cloudová služba role, "microsoft.compute/virtualmachinescalesets" - Virtual Machine Scale Sets a "Microsoft.Web/serverfarms" – webové aplikace |
| resourceId |Ano |Resource Manager ID cílového prostředku, který je právě škálovat |
| portalLink |Ano |Azure portal odkaz na stránku Souhrn v cílový prostředek |
| oldCapacity |Ano |Aktuální (staré) počet instancí kdy automatického škálování je akce škálování |
| newCapacity |Ano |Nový počet instancí, který prostředek, který chcete škálovat automatického škálování |
| Vlastnosti |Ne |Volitelné. Sadu < klíč, hodnota > páry (například Dictionary < String, String >). Vlastnosti pole je volitelné. Vlastní uživatelské rozhraní nebo pracovní postup aplikace logiky na základě aplikace můžete zadat klíče a hodnoty, které mohou být předány pomocí datové části. Existuje alternativní způsob předat vlastní vlastnosti odchozí volání webhooku je použití webhooku URI samotného (jako parametry dotazu) |
