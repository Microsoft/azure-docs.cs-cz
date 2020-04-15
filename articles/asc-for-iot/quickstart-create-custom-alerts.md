---
title: 'Úvodní příručka: Vytvoření vlastních výstrah'
description: Pochopit, vytvořit a přiřadit vlastní výstrahy zařízení pro Azure Security Center pro službu zabezpečení IoT.
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
ms.openlocfilehash: 8ba81b669c25d91efa8ad1f07b1a7835b42c5cf4
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310838"
---
# <a name="quickstart-create-custom-alerts"></a>Úvodní příručka: Vytvoření vlastních výstrah

Pomocí vlastních skupin zabezpečení a výstrah plně využívá informace o zabezpečení od konce na konec a znalosti kategorických zařízení, aby zajistil lepší zabezpečení v rámci řešení IoT.

## <a name="why-use-custom-alerts"></a>Proč používat vlastní výstrahy?

Znáte vaše zařízení IoT nejlépe.

Pro zákazníky, kteří plně pochopit jejich očekávané chování zařízení, Azure Security Center pro IoT umožňuje převést toto porozumění do zásadchování zařízení a upozornění na jakékoli odchylky od očekávané, normální chování.

## <a name="security-groups"></a>Skupiny zabezpečení

Skupiny zabezpečení umožňují definovat logické skupiny zařízení a centralizovaným způsobem spravovat jejich stav zabezpečení.

Tyto skupiny mohou představovat zařízení s konkrétním hardwarem, zařízení nasazená v určitém umístění nebo jakoukoli jinou skupinu vhodnou pro vaše specifické potřeby.

Skupiny zabezpečení jsou definovány vlastností značky dvojčete zařízení s názvem **SecurityGroup**. Ve výchozím nastavení má každé řešení IoT v iot hubu jednu skupinu zabezpečení s názvem **default**. Změňte hodnotu vlastnosti **SecurityGroup** a změňte skupinu zabezpečení zařízení.

Příklad:

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

Pomocí skupin zabezpečení můžete zařízení seskupit do logických kategorií. Po vytvoření skupin je přiřaďte k vlastním výstrahám podle vašeho výběru, abyste vytvořili nejefektivnější komplexní řešení zabezpečení IoT.

## <a name="customize-an-alert"></a>Přizpůsobení výstrahy

1. Otevřete svůj IoT Hub.
1. V části **Zabezpečení** klikněte na **Vlastní výstrahy.**
1. Vyberte skupinu zabezpečení, na kterou chcete použít vlastní nastavení.
1. Klikněte **na Přidat vlastní výstrahu**.
1. V rozevíracím seznamu vyberte vlastní výstrahu.
1. Upravte požadované vlastnosti, klepněte na **tlačítko OK**.
1. Nezapomeňte klepnout na tlačítko **ULOŽIT**. Bez uložení nové výstrahy se výstraha odstraní při příštím zavření centra IoT Hub.

## <a name="alerts-available-for-customization"></a>Upozornění, která jsou k dispozici pro vlastní nastavení

Azure Security Center pro IoT nabízí velké množství výstrah, které lze přizpůsobit podle vašich konkrétních potřeb. Zkontrolujte [přizpůsobitelnou tabulku výstrah,](concept-customizable-security-alerts.md) zda není závažnost výstrah, zdroj dat, popis a naše navrhované nápravné kroky, pokud a kdy je každá výstraha přijata.

## <a name="next-steps"></a>Další kroky

Přejdete k dalšímu článku a dozvíte se, jak nasadit agenta zabezpečení...

> [!div class="nextstepaction"]
> [Nasazení agenta zabezpečení](how-to-deploy-agent.md)
