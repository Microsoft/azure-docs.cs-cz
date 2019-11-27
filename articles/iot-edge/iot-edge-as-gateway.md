---
title: Brány pro příjem dat zařízení – Azure IoT Edge | Dokumentace Microsoftu
description: Vytvoření transparentní, neprůhledné nebo proxy zařízení brány, který odesílá data z více podřízených zařízení do cloudu nebo místně procesů pomocí Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ec8b6cf61f9fb92f888642d1de7d4d1b9b7ac3df
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456654"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Jak se dá zařízení IoT Edge využít jako brána

Brány v IoT Edge řešení poskytují připojení zařízení a hraniční analýzy pro zařízení IoT, která by jinak neposkytovala tyto možnosti. Azure IoT Edge umožňuje splňovat všechny požadavky pro brány IoT bez ohledu na to, zda se vztahují k připojení, identity nebo hraniční analýzu. Vzory brány v tomto článku odkazovat pouze na vlastnosti připojení zařízení příjem dat a identity zařízení, jak se zpracovávají data zařízení v bráně.

## <a name="patterns"></a>Vzory

Existují tři vzory pro používání zařízení IoT Edge jako brána: transparentní, protokol, překlad a překladu identity:
* **Transparentní** – zařízení, která se teoreticky můžou připojit k IoT Hub se můžou místo toho připojit k zařízení brány. Příjem dat zařízení mají své vlastní identity služby IoT Hub a používáte žádný protokol MQTT, AMQP nebo HTTP. Brána jednoduše předává komunikaci mezi zařízením a službou IoT Hub. Zařízení nevědí, že komunikují s cloudem přes bránu, a uživatel, který komunikuje se zařízeními v IoT Hub, nevědomá zařízení zprostředkující brány. Proto je transparentní brány. V tématu [Vytvoření transparentní brány](how-to-create-transparent-gateway.md) najdete konkrétní informace o použití zařízení IoT Edge jako transparentní brány.
* **Překlad protokolu** – známý taky jako neprůhledný model brány, zařízení, která nepodporují MQTT, AMQP nebo http, můžou používat zařízení brány k posílání dat, která se IoT Hub jménem. Brána rozumí protokolu používanému v zařízeních pro příjem dat a je jediné zařízení, které má v IoT Hub identitu. Informace o veškerém pravděpodobně pochází z jednoho zařízení brány. Příjem dat zařízení musíte vložit další identifikační informace ve zprávách, pokud cloudové aplikace, aby k analýze dat na každé zařízení zvlášť. Kromě toho služby IoT Hub primitiv, jako jsou dvojčata a metody jsou dostupné jenom pro zařízení brány, nejsou podřízené zařízení.
* **Překlad identity** – zařízení, která se nemůžou připojit k IoT Hub se můžou místo toho připojit k zařízení brány. Brána zajišťuje identity a protokolu překladu jménem podřízené zařízení služby IoT Hub. Brána je dostatečně inteligentní, aby pochopit protokol používá podřízené zařízení, je poskytovat identitu a překládat primitiv služby IoT Hub. Příjem dat zařízení objeví ve službě IoT Hub jako první kategorie zařízení pomocí dvojčat a metody. Uživatel může komunikovat se zařízeními ve službě IoT Hub a nebude vědět o zařízení zprostředkující brány.

![Diagram – transparentní, protokol a vzory brány identity](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Případy použití
Všechny vzory gateway poskytuje následující výhody:
* **Analýzy na hraničních** zařízeních – používají se místně služby AI ke zpracování dat přicházejících ze zařízení, která se neodesílají do cloudu, bez odesílání telemetrie s plnou věrností. Najít a reagovat na přehledy místně a odesílat pouze podmnožinu dat do služby IoT Hub. 
* **Izolace zařízení** po jednotlivých zařízeních – zařízení brány může chránit všechna zařízení pro příjem dat před expozicí Internetu. To může být umístěn souběžně mezi OT sítě, která nemá připojení a IT síť, která poskytuje přístup k webu. 
* **Multiplexing připojení** – všechna zařízení, která se připojují k IoT Hub přes IoT Edge bránu, používají stejné základní připojení.
* **Vyhlazení provozu** – IoT Edge zařízení automaticky implementuje exponenciální omezení rychlosti, pokud IoT Hub omezuje provoz, zatímco se zprávy ukládají místně. Tuto výhodu provede řešení odolné špičkami v provozu.
* **Podpora offline** – zařízení brány ukládá zprávy a zdvojené aktualizace, které nelze doručit IoT Hub.

Bránu, která překlad protokolu. můžete také provádět hraniční analýzu, izolace zařízení, vyhlazování provoz a podporu offline režimu stávající zařízení a nová zařízení, která jsou omezené prostředků. Mnoho existujících zařízení vytvářejí data, která může power byznysu; však obvykle nebyly navrženy pomocí připojení ke cloudu v úvahu. Neprůhledné brány umožňují, aby tato data byla odemčená a používána v řešení IoT.

Bránu, která provádí překlad identity přináší výhody překlad protokolu a kromě toho umožňuje úplné možnosti správy zařízení pro příjem dat z cloudu. Všechna zařízení v řešení IoT se zobrazí ve službě IoT Hub bez ohledu na to, které používají protokol.

## <a name="cheat-sheet"></a>Tahák
Tady je rychlý tahák, která porovnává primitiv služby IoT Hub, při použití transparentní, neprůhledné (protocol) a proxy server brány.

| &nbsp; | Transparentní brány | Překlad protokolu | Překlad identity |
|--------|-------------|--------|--------|
| Identity, které jsou uložené v registru identit služby IoT Hub | Identity všechna připojená zařízení | Pouze identity zařízení brány | Identity všechna připojená zařízení |
| Dvojče zařízení | Každého připojeného zařízení má svůj vlastní dvojče zařízení | Jenom brána má dvojčata zařízení a modul | Každého připojeného zařízení má svůj vlastní dvojče zařízení |
| Přímé metody a zprávy typu cloud zařízení | Cloud jednotlivě adresu každého připojeného zařízení | Cloud může vyřešit pouze zařízení brány | Cloud jednotlivě adresu každého připojeného zařízení |
| [Omezení IoT Hub a kvóty](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Platí pro každé zařízení | Platí pro zařízení brány | Platí pro každé zařízení |

Při použití model neprůhledné brány (překlad protokolu), všechna zařízení, připojení přes tuto bránu sdílet stejné fronty typu cloud zařízení, která může obsahovat maximálně 50 zprávy. Vyplývá, že model neprůhledné brány by měla sloužit pouze v případě, že se několik zařízení připojují prostřednictvím brány každé pole a je malý provoz jejich typu cloud zařízení.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak nastavit transparentní bránu: 

* [Konfigurace zařízení IoT Edge tak, aby fungovalo jako transparentní brána](how-to-create-transparent-gateway.md)
* [Ověření podřízeného zařízení ve službě Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Připojení podřízeného zařízení k bráně služby Azure IoT Edge](how-to-connect-downstream-device.md)