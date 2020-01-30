---
title: 'Rychlý Start: vytvoření vlastních výstrah pro Azure Security Center pro IoT'
description: Naučte se vytvářet a přiřazovat vlastní výstrahy zařízení pro Azure Security Center pro službu zabezpečení IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/27/2020
ms.author: mlottner
ms.openlocfilehash: 510a2c3f04cc4c307acad18ee3012d9407155f9f
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76766448"
---
# <a name="quickstart-create-custom-alerts"></a>Rychlý Start: vytvoření vlastních výstrah


Pomocí vlastních skupin zabezpečení a výstrah aplikace plně využívá kompletní informace o zabezpečení a znalosti zařízení kategorií, aby bylo zajištěno lepší zabezpečení v rámci řešení IoT. 

## <a name="why-use-custom-alerts"></a>Proč používat vlastní výstrahy? 

Víte, co nejlépe jsou vaše zařízení IoT.

Pro zákazníky, kteří plně pochopili své očekávané chování zařízení, Azure Security Center pro IoT vám umožní přeložit toto porozumění na zásady chování zařízení a upozornit na jakoukoli odchylku od očekávaného, normálního chování.

## <a name="security-groups"></a>Skupiny zabezpečení

Skupiny zabezpečení umožňují definovat logické skupiny zařízení a spravovat jejich stav zabezpečení centralizovaným způsobem.

Tyto skupiny můžou představovat zařízení s konkrétním hardwarem, zařízeními nasazenými v určitém umístění nebo v jakékoli jiné skupině vhodné pro vaše konkrétní potřeby.

Skupiny zabezpečení jsou definovány pomocí vlastnosti značky zařízení s názvem **Security**. Ve výchozím nastavení má každé řešení IoT na IoT Hub jednu skupinu zabezpečení s názvem **Default**. Změňte hodnotu vlastnosti **securitycollection** , aby se změnila skupina zabezpečení zařízení.
 
Například:

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  }, 
```

Skupiny zabezpečení použijte k seskupení zařízení do logických kategorií. Po vytvoření skupin je přiřaďte k vlastním výstrahám podle vašeho výběru a získáte tak nejúčinnější řešení zabezpečení IoT od začátku až do konce. 

## <a name="customize-an-alert"></a>Přizpůsobení výstrahy

1. Otevřete IoT Hub. 
2. V části **zabezpečení** klikněte na **vlastní výstrahy** . 
3. Vyberte skupinu zabezpečení, pro kterou chcete použít vlastní nastavení. 
4. Klikněte na **Přidat vlastní výstrahu**.
5. V rozevíracím seznamu vyberte vlastní výstrahu. 
6. Upravte požadované vlastnosti a klikněte na tlačítko **OK**.
7. Nezapomeňte kliknout na **Uložit**. Bez uložení nové výstrahy se upozornění odstraní při příštím zavření IoT Hub.

 
## <a name="alerts-available-for-customization"></a>Výstrahy k dispozici pro přizpůsobení

Azure Security Center pro IoT nabízí velký počet výstrah, které je možné přizpůsobit podle vašich konkrétních potřeb. V [tabulce přizpůsobitelné výstrahy](concept-customizable-security-alerts.md) můžete zobrazit Závažnost výstrahy, zdroj dat, popis a naše doporučené kroky k nápravě, pokud a kdy se jednotlivé výstrahy přijaly. 
|


## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte, jak nasadit agenta zabezpečení...

> [!div class="nextstepaction"]
> [Nasazení agenta zabezpečení](how-to-deploy-agent.md)
