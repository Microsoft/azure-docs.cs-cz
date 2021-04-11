---
title: Vytváření vlastních výstrah
description: Pochopení, vytváření a přiřazování vlastních výstrah zařízení pro službu zabezpečení Azure Defender for IoT.
ms.topic: how-to
ms.date: 09/04/2020
ms.openlocfilehash: 5d9bb7811396579ec9096715809a101aebbf36a3
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384540"
---
# <a name="create-custom-alerts"></a>Vytváření vlastních výstrah

Pomocí vlastních skupin zabezpečení a výstrah aplikace plně využívá kompletní informace o zabezpečení a znalosti zařízení kategorií, aby bylo zajištěno lepší zabezpečení v rámci řešení IoT.

## <a name="why-use-custom-alerts"></a>Proč používat vlastní výstrahy?

Víte, co nejlépe jsou vaše zařízení IoT.

Pro zákazníky, kteří plně pochopili své očekávané chování zařízení, vám Defender pro IoT umožní přeložit toto porozumění na zásady chování zařízení a upozornit na jakoukoli odchylku od očekávaného, normálního chování.

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

1. Otevřete IoT Hub a v nabídce **zabezpečení** vyberte **Nastavení** .

1. Vyberte **vlastní výstrahy**.

1. Vyberte skupinu zabezpečení, pro kterou chcete použít vlastní nastavení.

1. Vyberte **Přidat vlastní výstrahu**.

1. V rozevíracím seznamu vyberte vlastní výstrahu.

1. Upravte požadované vlastnosti a vyberte **OK**.

1. Ujistěte se, že jste vybrali **Uložit**. Bez uložení nové výstrahy se upozornění odstraní při příštím zavření IoT Hub.

## <a name="alerts-available-for-customization"></a>Výstrahy k dispozici pro přizpůsobení

Defender for IoT nabízí velký počet výstrah, které je možné přizpůsobit podle konkrétních potřeb. V [tabulce přizpůsobitelné výstrahy](concept-customizable-security-alerts.md) můžete zobrazit Závažnost výstrahy, zdroj dat, popis a naše doporučené kroky k nápravě, pokud a kdy byla přijata jednotlivá výstraha.

## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte, jak nasadit agenta zabezpečení...

> [!div class="nextstepaction"]
> [Nasazení agenta zabezpečení](how-to-deploy-agent.md)
