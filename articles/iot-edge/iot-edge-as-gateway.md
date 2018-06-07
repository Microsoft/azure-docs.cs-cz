---
title: Použití zařízení Azure IoT okraj jako brány | Microsoft Docs
description: Pomocí Azure IoT Edge vytvořit transparentní, neprůhledné nebo zařízení brány proxy, který odesílá data z více podřízené zařízení do cloudu nebo místně procesy.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/27/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ff5e50df1f7a9059bf37fe99d89050a48aa6e64e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631594"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway---preview"></a>Jak IoT hraniční zařízení lze použít jako brána – náhled

Účelem brány v řešeních pro IoT je specifický pro řešení a kombinovat připojení zařízení s hraniční analytics. Azure IoT Edge slouží k uspokojení všechny potřeby brány IoT, bez ohledu na to, jestli se jedná o připojení, identity nebo hraniční analýzu. Vzory brány v tomto článku odkazovat pouze na vlastnosti připojení zařízení příjem dat a identity zařízení, jak není na bráně zpracování dat zařízení.

## <a name="patterns"></a>Vzory
Existují tři vzory pro používání IoT hraniční zařízení jako brána: transparentní, protokolu, překlad a překlad identity:
* **Transparentní** – zařízení, která teoreticky by se mohl připojit ke službě IoT Hub můžete místo toho připojit k zařízení brány. To znamená, že podřízené zařízení mají své vlastní identity služby IoT Hub a používají všechny protokoly MQTT, AMQP a HTTP. Brána jednoduše předává komunikaci mezi zařízeními a IoT Hub. Zařízení neberou v úvahu, že komunikuje se do cloudu přes bránu a uživatel interakci s zařízení IoT hub je nebere v úvahu zařízení zprostředkující brány. Brána je proto transparentní. Odkazovat [vytvořit bránu transparentní] [ lnk-iot-edge-as-transparent-gateway] s postupy pro podrobnosti o použití IoT hraniční zařízení jako brána transparentní.
* **Protokol překladu** – zařízení, které nepodporují MQTT, AMQP nebo HTTP pomocí zařízení brány k odesílání dat do služby IoT Hub. Brána je dostatečně inteligentní zjistit, že protokol použitý podřízené zařízení; je ale jenom zařízení, která obsahuje identit do služby IoT Hub. Informace o všech vypadá pocházejí z jednoho zařízení brány. To znamená, že podřízené zařízení musí vložit další identifikační údaje v jejich zprávy, pokud cloudové aplikace, které chcete důvod o data na základě vázané na zařízení. Kromě toho primitiv IoT Hub jako twin a metody jsou dostupné pouze pro zařízení brány, není podřízené zařízení.
* **Překlad identity** – zařízení, která se nemůže připojit ke službě IoT Hub připojit k zařízení brány, která poskytuje identitu a protokol překladu jménem podřízené zařízení IoT Hub. Brána je dostatečně inteligentní pochopit protokol použitý podřízené zařízení, je poskytovat identitu a převede primitiv IoT Hub. Podřízené zařízení nezobrazí v IoT Hub jako první třídy zařízení s dvojčata a metody. Uživatel může komunikovat se zařízeními IoT hub a nemá informace o zařízení zprostředkující brány.

![Diagramy schémat brány][1]

## <a name="use-cases"></a>Případy použití
Všechny brány vzory poskytovat následující výhody:
* **Okraj analytics** – AI použití služby místně ke zpracování dat pocházejících z podřízené zařízení bez odeslání úplné věrnosti telemetrie do cloudu. Najít a reagovat na Statistika místně a odesílat pouze podmnožinu dat do služby IoT Hub. 
* **Izolace podřízené zařízení** – zařízení brány, které můžete stínit všechna zařízení příjem dat z ohrožení k Internetu. Ho můžete pohodlně mezi síť typ objektu, který není připojen k a síť IT, která poskytuje přístup k webu. 
* **Připojení multiplexní** – všechna zařízení připojující se ke službě IoT Hub prostřednictvím okraj IoT bude zařízení používat stejné základní připojení.
* **Provoz vyhlazování** -služba IoT hraniční zařízení bude automaticky provádět exponenciálního omezení rychlosti v případě služby IoT Hub omezení při zachování zprávy v místním počítači. Odolné vaše řešení nebude nadále k špičky v provozu.
* **Omezenou podporu offline** – zařízení brány, které se uloží místně zprávy a twin aktualizace, které nelze doručit do služby IoT Hub.

Brána nepodporuje protokol překlad můžete také provést hraniční analýzu, zařízení izolace, provoz vyhlazování a offline podporu, aby stávající zařízení a nová zařízení, které jsou zdrojem omezené. Mnoho existující zařízení jsou generovala data, která může spotřeby podnikových statistik; však obvykle nebyly navrženy s připojením cloudu v paměti. Neprůhledné brány povolit tato data odemknout a využité v řešení IoT začátku do konce.

Bránu, která provádí překlad identity zadejte výhod překlad protokolu a kromě toho povolit úplné možnosti správy zařízení příjem dat z cloudu. Všechna zařízení ve vaší IoT řešení se zobrazí v centru IoT bez ohledu na protokol s jejich vyniknout.

## <a name="cheat-sheet"></a>Tahák
Zde je rychlý podvést listu, který porovnává primitiv IoT Hub, při použití transparentní, neprůhledných a proxy serveru brány.

| &nbsp; | Transparentní brány | Překlad protokolu | Překlad identity |
|--------|-------------|--------|--------|
| Identity, které jsou uložené v registru identit služby IoT Hub | Identity všech připojených zařízeních | Pouze identity zařízení brány | Identity všech připojených zařízeních |
| Dvojče zařízení | Každého připojeného zařízení má vlastní dvojče zařízení | Jenom brána má dvojčata zařízení a modulu | Každého připojeného zařízení má vlastní dvojče zařízení |
| Přímé metody a zprávy typu cloud zařízení | Cloud jednotlivě adres každého připojeného zařízení | Cloudu můžete vyřešit pouze zařízení brány | Cloud jednotlivě adres každého připojeného zařízení |
| [IoT Hub omezení a kvóty][lnk-iothub-throttles-quotas] | Platí pro každé zařízení | Platí pro zařízení brány | Platí pro každé zařízení |

Pokud používáte neprůhledného brány (překlad protokolu) vzor, všechna zařízení, připojení prostřednictvím této brány sdílet stejnou frontou cloud zařízení, která může obsahovat maximálně 50 zprávy. Vyplývá, že vzoru neprůhledného brány měli použít pouze v případě, že se velmi málo zařízení připojují prostřednictvím brány každé pole, a je malý provoz jejich cloud zařízení.

## <a name="next-steps"></a>Další postup
Pomocí zařízení IoT okraj jako [transparentní brány][lnk-iot-edge-as-transparent-gateway] 

[lnk-iot-edge-as-transparent-gateway]: ./how-to-create-transparent-gateway.md
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md

[1]: ./media/iot-edge-as-gateway/edge-as-gateway.png
