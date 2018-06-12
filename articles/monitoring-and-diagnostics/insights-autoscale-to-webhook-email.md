---
title: Používat automatické škálování k odesílání e-mailu a webhooku oznámení výstrah
description: 'Zjistit, jak používat akce škálování adres URL webových zavolat nebo poslat e-mailová oznámení v nástroji Sledování Azure. '
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/03/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 65405a6d7f1d49911da1e2a5d26b02098a261c01
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262218"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Akce škálování používat k odesílání e-mailu a webhooku oznámení výstrah v monitorování Azure
Tento článek ukazuje, jak nastavíte aktivační události tak, aby můžete volat konkrétní webové adresy URL nebo poslat e-mailů podle akce škálování v Azure.  

## <a name="webhooks"></a>Webhooky
Webhooky umožňují směrovat Azure oznámení výstrah do jiných systémů pro následné zpracování nebo vlastních oznámení. Například směrování upozornění do služby, které může zpracovávat příchozí webové žádosti pro odeslání že SMS, protokolu chyb, upozornění, že tým pomocí chatu nebo zasílání zpráv služby atd. Webhook identifikátor URI musí být platný koncový bod HTTP nebo HTTPS.

## <a name="email"></a>Email
Všechny platné e-mailovou adresu nelze odesílat e-mailu. Správci a spolusprávci předplatného, kde je spuštěno pravidlo bude také upozorněni.

## <a name="cloud-services-and-web-apps"></a>Cloudové služby a webové aplikace
Vám může výslovný souhlas z portálu Azure pro cloudové služby a serverové farmy (webové aplikace).

* Vyberte **škálovat podle** metriku.

![škálování podle](./media/insights-autoscale-to-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Sady škálování virtuálního počítače
Pro novější virtuální počítače vytvořené pomocí Resource Manageru (sady škálování virtuálního počítače) to můžete nakonfigurovat pomocí rozhraní REST API, šablony Resource Manageru, prostředí PowerShell a rozhraní příkazového řádku. Portál rozhraní dosud nejsou k dispozici.
Při použití šablony REST API nebo Resource Manager, obsahovat element oznámení s následujícími parametry.

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
| operace |ano |Hodnota musí být "Škálování" |
| sendToSubscriptionAdministrator |ano |Hodnota musí být "true" nebo "Nepravda" |
| sendToSubscriptionCoAdministrators |ano |Hodnota musí být "true" nebo "Nepravda" |
| customEmails |ano |Hodnota může být null [] nebo pole řetězců e-mailů |
| Webhooky |ano |Hodnota může být null nebo platný identifikátor Uri |
| serviceUri |ano |platný https identifikátor Uri |
| properties |ano |Hodnota musí být prázdná. {} nebo může obsahovat páry klíč hodnota |

## <a name="authentication-in-webhooks"></a>Ověřování v webhooky
Webhooku můžete ověřit pomocí ověřování založeného na token, kam jste uložili webhooku identifikátor URI s tokenu ID jako parametr dotazu. Například https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue.

## <a name="autoscale-notification-webhook-payload-schema"></a>Škálování oznámení webhooku datové části schématu
Když se vygeneruje oznámení škálování, je následující metadata zahrnuty do datové části webhooku:

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
| status |ano |Stav, který označuje, že bylo vygenerováno akce škálování |
| operace |ano |Pro zvýšení instancí bude "Horizontální navýšení kapacity" a pro snížení instancí, bude "Škálování v" |
| Kontext |ano |Kontext akce škálování |
| časové razítko |ano |Časové razítko, když byla spuštěna akce škálování |
| id |Ano |ID správce prostředků nastavení automatického škálování |
| jméno |Ano |Název nastavení automatického škálování |
| Podrobnosti |Ano |Vysvětlení akci, kterou trvalo službu automatické škálování a změna v počet instancí |
| subscriptionId |Ano |ID předplatného cílového prostředku, který bude změněno měřítko |
| resourceGroupName |Ano |Název skupiny prostředků cílového prostředku, který bude změněno měřítko |
| resourceName |Ano |Název cílového prostředku, který bude změněno měřítko |
| Typ prostředku |Ano |Tři podporované hodnoty: "microsoft.classiccompute/domainnames/slots/roles" – cloudové služby rolí, "microsoft.compute/virtualmachinescalesets" - sady škálování virtuálního počítače a "Microsoft.Web/serverfarms" - webové aplikace |
| resourceId |Ano |Správce prostředků ID cílového prostředku, který bude změněno měřítko |
| portalLink |Ano |Azure portálu odkaz na stránce Souhrn cílového prostředku |
| oldCapacity |Ano |Aktuální (starý) počet instancí při škálování trvalo akce škálování |
| newCapacity |Ano |Nový počet instancí, který škálování škálovat prostředek, který |
| Vlastnosti |Ne |Volitelné. Sada < klíč a hodnotu > páry (například slovník < String, String >). Vlastnosti pole je nepovinné. Do vlastní uživatelské rozhraní nebo aplikací na základě logiky pracovního postupu můžete zadat klíče a hodnoty, které lze předat pomocí datové části. Jiný způsob, jak předat vlastní vlastnosti odchozí voláním webhooku se má používat webhooku URI samotné (jako parametry dotazu) |
