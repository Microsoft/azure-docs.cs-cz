---
title: 'Rychlý Start: vytvoření vlastních výstrah pro Azure Security Center pro IoT'
description: V tomto rychlém startu vytvoříte a přiřadíte vlastní výstrahy zařízení Azure Security Center pro IoT.
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
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: eca5d69efb04cf8210b0b2aa502bcee5cd4f5264
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904123"
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

Následující tabulka poskytuje souhrn výstrah dostupných pro přizpůsobení.


| Severity | Název | Zdroj dat | Popis | Navrhovaná náprava|
|---|---|---|---|---|
| Nízká      | Vlastní upozornění – počet zpráv v cloudu na zařízení v protokolu AMQP je mimo povolený rozsah.          | IoT Hub     | Počet zpráv z cloudu na zařízení (AMQP Protocol) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah.||
| Nízká      | Vlastní upozornění – počet odmítnutých zpráv v cloudu na zařízení v protokolu AMQP je mimo povolený rozsah. | IoT Hub     | Počet zpráv z cloudu na zařízení (Protokol AMQP) zamítnutý zařízením v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah.||
| Nízká      | Vlastní upozornění – počet zpráv ze zařízení na Cloud v protokolu AMQP je mimo povolený rozsah.      | IoT Hub     | Množství zpráv ze zařízení do cloudu (AMQP Protocol) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah.|   |
| Nízká      | Vlastní výstraha – počet volání přímé metody je mimo povolený rozsah. | IoT Hub     | Množství přímé metody vyvolání v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah.||
| Nízká      | Vlastní výstraha – počet nahrávání souborů je mimo povolený rozsah. | IoT Hub     | Velikost nahrávání souborů v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah.| |
| Nízká      | Vlastní upozornění – počet zpráv v cloudu na zařízení v protokolu HTTP je mimo povolený rozsah. | IoT Hub     | Množství zpráv z cloudu na zařízení (protokol HTTP) v časovém okně není v nakonfigurovaném povoleném rozsahu.                                  |
| Nízká      | Vlastní výstraha – počet odmítnutých zpráv v cloudu na zařízení v protokolu HTTP není v povoleném rozsahu. | IoT Hub     | Množství zpráv z cloudu na zařízení (protokol HTTP) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |
| Nízká      | Vlastní upozornění – počet zpráv ze zařízení na Cloud v protokolu HTTP je mimo povolený rozsah. | IoT Hub| Velikost zařízení pro cloudové zprávy (protokol HTTP) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah.|    |
| Nízká      | Vlastní upozornění – počet zpráv v cloudu na zařízení v protokolu MQTT je mimo povolený rozsah. | IoT Hub     | Množství zpráv z cloudu na zařízení (MQTT Protocol) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah.|   |
| Nízká      | Vlastní upozornění – počet odmítnutých zpráv v cloudu na zařízení v protokolu MQTT je mimo povolený rozsah. | IoT Hub     | Množství zpráv z cloudu na zařízení (protokol MQTT) odmítnuté zařízením v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |
| Nízká      | Vlastní upozornění – počet zpráv ze zařízení na Cloud v protokolu MQTT je mimo povolený rozsah.          | IoT Hub     | Množství zpráv ze zařízení do cloudu (MQTT Protocol) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah.|
| Nízká      | Vlastní upozornění – počet vyprázdnění fronty příkazů je mimo povolený rozsah.                               | IoT Hub     | Velikost fronty příkazů v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah.||
| Nízká      | Vlastní výstraha – počet nevlákenných aktualizací modulu je mimo povolený rozsah.                                       | IoT Hub     | Množství nespuštěných aktualizací v rámci určitého časového okna je mimo aktuálně nakonfigurovaný a povolený rozsah.|
| Nízká      | Vlastní výstraha – počet neautorizovaných operací je mimo povolený rozsah.  | IoT Hub     | Množství neautorizovaných operací v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah.|
| Nízká      | Vlastní upozornění – počet aktivních připojení je mimo povolený rozsah.  | Agent       | Počet aktivních připojení v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah.|  Prozkoumejte protokoly zařízení. Zjistěte, kde připojení pochází, a zjistěte, jestli je neškodný nebo škodlivý. Pokud máte škodlivou, odstraňte možný malware a porozumět zdroji. Pokud je neškodný, přidejte zdroj do seznamu povolených připojení.  |
| Nízká      | Vlastní výstraha – odchozí připojení vytvořené na IP adresu, která není povolená                             | Agent       | Odchozí připojení bylo vytvořeno na IP adresu, která je mimo povolený seznam IP adres. |Prozkoumejte protokoly zařízení. Zjistěte, kde připojení pochází, a zjistěte, jestli je neškodný nebo škodlivý. Pokud máte škodlivou, odstraňte možný malware a porozumět zdroji. Pokud je neškodný, přidejte zdroj do seznamu povolených IP adres.                        |
| Nízká      | Vlastní upozornění – počet neúspěšných místních přihlášení je mimo povolený rozsah.                               | Agent       | Množství neúspěšných místních přihlášení v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |   |
| Nízká      | Vlastní výstraha – přihlášení uživatele, který není v seznamu povolených uživatelů | Agent       | Místní uživatel mimo seznam povolených uživatelů, přihlášený k zařízení.|  Pokud ukládáte nezpracovaná data, přejděte k účtu Log Analytics a pomocí dat Prozkoumejte zařízení, identifikujte zdroj a pak pro tato nastavení opravte seznam povolených/blokovaných dat. Pokud v současné době neukládáte nezpracovaná data, přečtěte si zařízení a opravte seznam povolených a blokovaných dat pro tato nastavení.|
| Nízká      | Vlastní výstraha – proces byl spuštěn, což není povoleno. | Agent       | V zařízení se spustil proces, který není povolený. |Pokud ukládáte nezpracovaná data, přejděte k účtu Log Analytics a pomocí dat Prozkoumejte zařízení, identifikujte zdroj a pak pro tato nastavení opravte seznam povolených/blokovaných dat. Pokud v současné době neukládáte nezpracovaná data, přečtěte si zařízení a opravte seznam povolených a blokovaných dat pro tato nastavení.  |
|


## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte, jak nasadit agenta zabezpečení...

> [!div class="nextstepaction"]
> [Nasazení agenta zabezpečení](how-to-deploy-agent.md)
