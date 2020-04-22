---
title: Brány pro zařízení pro příjem dat – Azure IoT Edge | Dokumenty společnosti Microsoft
description: Azure IoT Edge slouží k vytvoření průhledného, neprůhledného zařízení brány nebo zařízení brány proxy, které odesílá data z více podřazených zařízení do cloudu nebo je zpracovává místně.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 916eeaa60bc054301af039164ce1c14e77ceb91a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733519"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Jak se dá zařízení IoT Edge využít jako brána

Brány v řešeních IoT Edge poskytují připojení zařízení a analýzu výhod pro zařízení IoT, která by jinak tyto funkce neměla. Azure IoT Edge se dá použít k uspokojení všech potřeb brány IoT, ať už souvisí s připojením, identitou nebo analýzou na okraji. Vzory brány v tomto článku odkazují pouze na charakteristiky připojení k datům po proudu a identity zařízení, nikoli na způsob zpracování dat zařízení v bráně.

## <a name="patterns"></a>Vzory

Existují tři vzory pro použití zařízení IoT Edge jako brány: transparentní, překlad protokolu a překlad identity:

* **Transparentní** – zařízení, která se teoreticky můžou připojit k IoT Hubu, se můžou místo toho připojit k zařízení brány. Zařízení pro příjem dat mají vlastní identity služby IoT Hub a používají libovolný protokol YQTT, AMQP nebo HTTP. Brána jednoduše předává komunikaci mezi zařízeními a službou IoT Hub. Zařízení i uživatelé, kteří s nimi komunikují prostřednictvím služby IoT Hub, nevědí, že brána zprostředkuje jejich komunikaci. Tento nedostatek povědomí znamená, že brána je považována za *transparentní*. Viz [Vytvoření transparentní brány](how-to-create-transparent-gateway.md) pro specifika o použití zařízení IoT Edge jako transparentní brány.
* **Překlad protokolu** – označované také jako vzor neprůhledné brány, zařízení, která nepodporují MQTT, AMQP nebo HTTP můžete použít zařízení brány k odesílání dat do služby IoT Hub jejich jménem. Brána rozumí protokolu používanému zařízením pro příjem dat a je jediným zařízením, které má identitu v centru IoT Hub. Všechny informace vypadají, jako by přicházely z jednoho zařízení, brány. Zařízení pro příjem dat musí do svých zpráv vložit další identifikační informace, pokud cloudové aplikace chtějí analyzovat data na základě pro zařízení. Kromě toho ioT Hub primitiv, jako jsou dvojčata a metody jsou k dispozici pouze pro zařízení brány, nikoli pro příjem dat zařízení.
* **Překlad identity** – zařízení, která se nemohou připojit k službě IoT Hub, se místo toho můžou připojit k zařízení brány. Brána poskytuje identitu ioT hubu a překlad protokolu jménem zařízení pro příjem dat. Brána je dostatečně chytrá, aby pochopila protokol používaný navazujícími zařízeními, poskytla jim identitu a překlápěla primitiva služby IoT Hub. Zařízení pro příjem dat se v centru IoT Hub zobrazují jako prvotřídní zařízení s dvojčaty a metodami. Uživatel může komunikovat se zařízeními v centru IoT Hub a neví o zařízení zprostředkující brány.

![Diagram – transparentní, protokolové a brány identity vzory](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Případy použití

Všechny vzory brány poskytují následující výhody:

* **Analytika na hraničních zařízeních** – služby AI můžete využívat místně ke zpracování dat přicházejících z podřízeného zařízení bez odesílání telemetrie s plnou věrností do cloudu. Najděte přehledy a reagujte na ně místně a odesílejte jenom podmnožinu dat do služby IoT Hub.
* **Následná izolace zařízení** – zařízení brány může chránit všechna zařízení pro příjem dat před vystavením internetu. Může sedět mezi sítí OT, která nemá připojení, a sítí IT, která poskytuje přístup k webu.
* **Multiplexování připojení** – všechna zařízení, která se připojují k ioT hubu přes bránu IoT Edge, používají stejné základní připojení.
* **Vyhlazování provozu** – zařízení IoT Edge automaticky implementuje exponenciální backoff, pokud služba IoT Hub omezuje provoz, zatímco trvalé zprávy místně. Tato výhoda umožňuje vaše řešení odolné vůči špičkám v provozu.
* **Offline podpora** – zařízení brány ukládá zprávy a aktualizace dvojčete, které nelze doručit do služby IoT Hub.

Brána, která provádí překlad protokolu, může také provádět analýzu okrajů, izolaci zařízení, vyhlazování provozu a offline podporu stávajících zařízení a nových zařízení, která jsou omezena prostředky. Mnoho stávajících zařízení vytváří data, která mohou pohánět obchodní přehledy; nebyly však navrženy s ohledem na připojení ke cloudu. Neprůhledné brány umožňují odemknutí těchto dat a použití v řešení IoT.

Brána, která provádí překlad identit, poskytuje výhody překladu protokolů a navíc umožňuje plnou správu podřízeného zařízení z cloudu. Všechna zařízení ve vašem řešení IoT se zobrazí v ioT hubu bez ohledu na protokol, který používají.

## <a name="cheat-sheet"></a>Tahák

Tady je rychlý seznam, který porovnává primitiva služby IoT Hub při použití transparentních, neprůhledných (protokol) a proxy bran.

| &nbsp; | Transparentní brána | Překlad protokolu | Překlad identity |
|--------|-------------|--------|--------|
| Identity uložené v registru identit služby IoT Hub | Identity všech připojených zařízení | Pouze identita zařízení brány | Identity všech připojených zařízení |
| Dvojče zařízení | Každé připojené zařízení má své vlastní dvojče | Pouze brána má zařízení a dvojčata modulů | Každé připojené zařízení má své vlastní dvojče |
| Přímé metody a zprávy mezi cloudy | Cloud může řešit každé připojené zařízení individuálně | Cloud může adresovat pouze zařízení brány | Cloud může řešit každé připojené zařízení individuálně |
| [Omezení a kvóty centra IoT Hub](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Platí pro každé zařízení | Použití na zařízení brány | Platí pro každé zařízení |

Při použití vzoru neprůhledné brány (překlad protokolu) všechna zařízení, která se připojují prostřednictvím této brány, sdílejí stejnou frontu typu cloud zařízení, která může obsahovat maximálně 50 zpráv. Z toho vyplývá, že vzor neprůhledné brány by měl být používán pouze v případě, že se prostřednictvím každé brány pole připojuje jen málo zařízení a jejich provoz typu cloud zařízení je nízký.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak nastavit transparentní bránu:

* [Konfigurace zařízení IoT Edge tak, aby fungovalo jako transparentní brána](how-to-create-transparent-gateway.md)
* [Ověření podřízeného zařízení pro Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Připojení podřízeného zařízení k bráně služby Azure IoT Edge](how-to-connect-downstream-device.md)
