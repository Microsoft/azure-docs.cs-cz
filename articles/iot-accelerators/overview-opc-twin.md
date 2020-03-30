---
title: Co je OPC Twin - Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje přehled OPC Twin. OPC Twin poskytuje zjišťování, registraci a dálkové ovládání průmyslových zařízení prostřednictvím rest API.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 91448f55f0ebb88ba6c685b960ece9d91cb98e25
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "73826228"
---
# <a name="what-is-opc-twin"></a>Co je OPC Twin?

OPC Twin se skládá z mikroslužeb, které používají Azure IoT Edge a IoT Hub k připojení cloudu a tovární sítě. OPC Twin poskytuje zjišťování, registraci a dálkové ovládání průmyslových zařízení prostřednictvím rest API. OPC Twin nevyžaduje OPC Unified Architecture (OPC UA) SDK, je programovací jazyk agnostik a mohou být zahrnuty do pracovního postupu bez serveru. Tento článek popisuje několik případů použití OPC Twin.

## <a name="discovery-and-control"></a>Zjišťování a řízení
Můžete použít OPC Twin pro jednoduché pro zjišťování a registraci.

### <a name="simple-discovery-and-registration"></a>Jednoduché zjišťování a registrace
OPC Twin umožňuje továrním operátorům skenovat tovární síť, takže servery OPC UA mohou být objeveny a registrovány. Jako alternativu mohou provozovatelé továrny také ručně zaregistrovat zařízení OPC UA pomocí známé adresy URL zjišťování. Chcete-li například připojit ke všem zařízením OPC UA po instalaci brány IoT Edge s modulem OPC Twin v výrobní množině, může operátor továrny vzdáleně spustit skenování sítě a vizuálně zobrazit všechny servery OPC UA. 

### <a name="simple-control"></a>Jednoduché ovládání
OPC Twin umožňuje továrním operátorům reagovat na události a překonfigurovat své stroje z továrního podlaží z cloudu buď automaticky, nebo ručně za chodu. OPC Twin poskytuje REST API pro vyvolání služeb na serveru OPC UA, procházení jeho adresního prostoru, stejně jako pro čtení a zápis proměnných a spouštění metod. Například kotel používá teplotní klíčový ukazatel výkonu pro řízení výrobní linky. Teplotní senzor publikuje změnu dat pomocí Aplikace OPC Publisher. Provozovatel továrny obdrží upozornění, že teplota dosáhla prahové hodnoty. Výrobní linka se automaticky ochladí prostřednictvím OPC Twin. Provozovatel továrny je informován o ochlazení.

## <a name="authentication"></a>Ověřování
OPC Twin můžete použít pro jednoduché ověřování a jednoduché vývojářské prostředí.

### <a name="simple-authentication"></a>Jednoduché ověřování 
OPC Twin používá ověřování a auditování na základě Azure Active Directory (AAD) od začátku do konce. Například OPC Twin umožňuje aplikace, které mají být postaveny na vrcholu OPC Twin k určení, co operátor provedl na počítači. Na straně stroje je to prostřednictvím auditování OPC UA. Na straně cloudu je to prostřednictvím ukládání neměnné protokolu auditu klienta a ověřování AAD v rozhraní REST API.

### <a name="simple-developer-experience"></a>Jednoduché vývojářské prostředí 
OPC Twin lze použít s aplikacemi napsanými v libovolném programovacím jazyce prostřednictvím rozhraní REST API. Vzhledem k tomu, že vývojáři integrují klienta OPC UA do řešení, není znalost sady OPC UA SDK nutná. OPC Twin se může bezproblémově integrovat do bezstavových architektur bez serveru. Například webový vývojář s úplným zásobníkem, který vyvíjí aplikaci pro řídicí panel alarmů a událostí, může napsat logiku pro reakci na události v jazyce JavaScript nebo TypeScript pomocí OPC Twin bez znalosti implementace zásobníku C, C# nebo úplného zásobníku OPC UA. 

## <a name="next-steps"></a>Další kroky

Nyní, když jste se dozvěděli o OPC Twin a jeho použití, zde je navrhl další krok:

> [!div class="nextstepaction"]
> [Co je OPC Vault](overview-opc-vault.md)
