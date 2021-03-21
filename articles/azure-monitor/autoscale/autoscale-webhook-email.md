---
title: Použití automatického škálování k odesílání oznámení o výstrahách e-mailu a Webhooku
description: Naučte se používat akce automatického škálování pro volání webových adres URL nebo posílání e-mailových oznámení v Azure Monitor.
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: autoscale
ms.openlocfilehash: 3b1f13fd1ce8bedcbe58385d4cee321f1d1405df
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100610841"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Použití akcí automatického škálování k odesílání oznámení o výstrahách e-mailu a Webhooku v Azure Monitor
V tomto článku se dozvíte, jak nastavit triggery, abyste mohli volat konkrétní webové adresy URL nebo posílat e-maily na základě akcí automatického škálování v Azure.  

## <a name="webhooks"></a>Webhooky
Webhooky umožňují směrovat oznámení o výstrahách Azure do jiných systémů pro následné zpracování nebo vlastní oznámení. Například směrování výstrahy do služeb, které mohou zpracovávat příchozí webový požadavek k odeslání serveru SMS, protokolování chyb, upozornění týmu pomocí služby chat nebo zasílání zpráv atd. Identifikátor URI Webhooku musí být platný koncový bod HTTP nebo HTTPS.

## <a name="email"></a>E-mail
E-mail je možné odeslat do jakékoli platné e-mailové adresy. Budou se informovat i správci a spolusprávci předplatného, kde se pravidlo spouští.

## <a name="cloud-services-and-app-services"></a>Cloud Services a App Services
Pro Cloud Services a serverové farmy (App Services) se můžete přihlásit z Azure Portal.

* Vyberte možnost **škálovat podle** metriky.

![škálovat podle](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Škálovací sady virtuálních počítačů
Pro novější Virtual Machines vytvořené pomocí Správce prostředků (Virtual Machine Scale Sets) ho můžete nakonfigurovat pomocí REST API, šablon Správce prostředků, PowerShellu a rozhraní příkazového řádku. Rozhraní portálu ještě není k dispozici.
Při použití šablony REST API nebo Správce prostředků zahrňte do [autoscalesettings](/azure/templates/microsoft.insights/2015-04-01/autoscalesettings) element Notifications s následujícími možnostmi.

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

| Pole | Závaznou? | Description |
| --- | --- | --- |
| operation |ano |hodnota musí být Scale (škálovat). |
| sendToSubscriptionAdministrator |ano |hodnota musí být true nebo false. |
| sendToSubscriptionCoAdministrators |ano |hodnota musí být true nebo false. |
| customEmails |ano |hodnota může být null [] nebo pole řetězců e-mailů. |
| Webhooky |ano |hodnota může být null nebo platný identifikátor URI. |
| serviceUri |ano |platný identifikátor URI https |
| properties |ano |hodnota musí být prázdná {} nebo může obsahovat páry klíč-hodnota. |

## <a name="authentication-in-webhooks"></a>Ověřování ve webhookech
Webhook se může ověřit pomocí ověřování založeného na tokenech, kam uložíte identifikátor URI Webhooku s ID tokenu jako parametr dotazu. Například https: \/ /mysamplealert/webcallback? tokenu tokenId = sometokenid&someparameter = someValue

## <a name="autoscale-notification-webhook-payload-schema"></a>Schéma datové části Webhooku oznámení automatického škálování
Když se vygeneruje oznámení automatického škálování, do datové části Webhooku se zahrnou tato metadata:

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


| Pole | Závaznou? | Description |
| --- | --- | --- |
| status |ano |Stav, který indikuje, že se vygenerovala akce automatického škálování. |
| operation |ano |U zvýšení instancí se bude "horizontální navýšení kapacity" a snížení počtu instancí bude "škálovat". |
| kontext |ano |Kontext akce automatického škálování |
| časové razítko |ano |Časové razítko, kdy se aktivovala akce automatického škálování |
| id |Yes |ID Správce prostředků nastavení automatického škálování |
| name |Yes |Název nastavení automatického škálování |
| zobrazí |Yes |Vysvětlení akce, kterou služba automatického škálování trvala, a změny počtu instancí |
| subscriptionId |Yes |ID předplatného cílového prostředku, který se škáluje |
| resourceGroupName |Yes |Název skupiny prostředků cílového prostředku, který se škáluje |
| resourceName |Yes |Název cílového prostředku, který se škáluje |
| resourceType |Yes |Tři podporované hodnoty: Microsoft. classiccompute/DomainNames/sloty/Roles – role cloudových služeb, Microsoft. COMPUTE/virtualmachinescalesets "-Virtual Machine Scale Sets a" Microsoft. Web/serverových farem "-Web App |
| resourceId |Yes |Správce prostředků ID cílového prostředku, který se škáluje |
| portalLink |Yes |Azure Portal odkaz na stránku souhrnu cílového prostředku |
| oldCapacity |Yes |Aktuální (starý) počet instancí, když automatické škálování trvalo akci škálování |
| newCapacity |Yes |Nový počet instancí, na které se má znovu škálovat prostředek |
| properties |No |Nepovinný parametr. Sada <klíč, hodnota> páry (například slovník <řetězec, řetězec>). Pole vlastností je volitelné. Ve vlastním uživatelském rozhraní nebo pracovním postupu založeném na aplikaci logiky můžete zadat klíče a hodnoty, které lze předat pomocí datové části. Alternativní způsob, jak předat vlastní vlastnosti zpátky odchozímu volání Webhooku, je použití samotného identifikátoru URI Webhooku (jako parametrů dotazu). |
