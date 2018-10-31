---
title: Vývoj modulů pro Azure IoT Edge | Dokumentace Microsoftu
description: Zjistěte, jak vytvářet vlastní moduly pro Azure IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 761485de4bf52b7261ac8f1f8c3d937486c66546
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50247996"
---
# <a name="understand-the-requirements-and-tools-for-developing-iot-edge-modules"></a>Pochopení požadavků a nástroje pro vývoj modulů IoT Edge

Tento článek vysvětluje, jaké funkce jsou k dispozici při psaní aplikace, které běží jako modul IoT Edge a tom, jak využít.

## <a name="iot-edge-runtime-environment"></a>Prostředí modulu runtime IoT Edge
Modul runtime IoT Edge poskytuje infrastrukturu integrovat funkce více modulů IoT Edge a k jejich nasazení do zařízení IoT Edge. Na vysoké úrovni se dá zabalit jako modul IoT Edge žádný program. Však pokud chcete využívat všech výhod IoT Edge komunikační a funkce správy, můžete program spuštěný v modulu připojit k místní centra IoT Edge, integrované v modulu runtime IoT Edge.

## <a name="using-the-iot-edge-hub"></a>Pomocí centra IoT Edge
Centrum IoT Edge poskytuje dva hlavní funkce: proxy serveru do služby IoT Hub a místní komunikace.

### <a name="iot-hub-primitives"></a>IoT Hub primitiv
IoT Hub vidí a modul instance analogicky k zařízení, v tom smyslu, že:

* má dvojčete modulu, která je oddělená od [dvojče zařízení](../iot-hub/iot-hub-devguide-device-twins.md) a další modul dvojčata zařízení;
* můžete odeslat [zpráv typu zařízení cloud](../iot-hub/iot-hub-devguide-messaging.md);
* může přijímat [přímé metody](../iot-hub/iot-hub-devguide-direct-methods.md) zaměřené na svoji identitu.

V současné době modulu nemůže přijímat zprávy typu cloud zařízení ani pomocí funkce nahrání souboru.

Při psaní modulu, můžete jednoduše použít [sady SDK Azure IoT Device](../iot-hub/iot-hub-devguide-sdks.md) pro připojení k centru IoT Edge a použití výše uvedené funkce stejně jako při používání služby IoT Hub s aplikací zařízení, jediným rozdílem je, že z vaší back endové aplikace, potřebujete odkazovat na modul identitu místo na identitě zařízení.

V tématu [vývoj a nasazení modul IoT Edge na simulovaném zařízení](tutorial-csharp-module.md) příklad modul aplikace, která odesílá zprávy typu zařízení cloud a používá dvojčete modulu.

### <a name="device-to-cloud-messages"></a>Zprávy typu zařízení-cloud
Chcete-li povolit komplexní zpracování zpráv typu zařízení cloud, Centrum IoT Edge poskytuje deklarativní směrování zpráv mezi moduly a mezi moduly a IoT Hub.
Díky tomu modulů k zachycení a zpracování zpráv odesílaných ostatní moduly a jejich šíření kanálech složité.
Tento článek [složení modulu](module-composition.md) vysvětluje, jak sestavit modulů do složitých kanálů pomocí tras.

Modul IoT Edge, jinak než běžné aplikace zařízení služby IoT Hub může přijímat zprávy typu zařízení cloud, které se připojit přes proxy server pomocí její místní centra IoT Edge, aby bylo možné zpracovat.

Centrum IoT Edge šíří zprávy, které mají modul podle deklarativní směrování podle [složení modulu](module-composition.md) článku. Při vývoji modul IoT Edge, jak je znázorněno v tomto kurzu, mohou přijímat tyto zprávy nastavením obslužné rutiny zpráv [vývoj a nasazení modul IoT Edge na simulovaném zařízení] [lnk-tutorial2].

Aby bylo možné zjednodušit proces vytváření tras, IoT Edge přidá pojem modul *vstupní* a *výstup* koncových bodů. Modul může přijímat všechny zprávy typu zařízení cloud směrovat bez zadání jakékoli vstup a odesílat zprávy typu zařízení cloud bez zadání žádný výstup.
Pomocí explicitní vstupy a výstupy, ale zjednodušuje pravidla směrování pochopit. Zobrazit [složení modulu](module-composition.md) Další informace o pravidla směrování a vstupní a výstupní koncové body pro moduly.

Zprávy typu zařízení cloud zpracovat Centrum Edge se nakonec razítkem s následujícími vlastnostmi systému:

| Vlastnost | Popis |
| -------- | ----------- |
| $connectionDeviceId | ID zařízení klienta, který zprávu odeslal |
| $connectionModuleId | ID modulu modulu, který zprávu odeslal |
| $inputName | Vstup, který se tato zpráva zobrazila. Může být prázdný. |
| $outputName | Výstup používá k odeslání zprávy. Může být prázdný. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Připojení k centru IoT Edge z modulu
Připojení k místní Centrum IoT Edge z modulu zahrnuje dva kroky: použijte připojovací řetězec, pokud při spuštění modulu runtime IoT Edge a ujistěte se, že vaše aplikace přijímá certifikát předložený Centrum IoT Edge na tomto zařízení.

Modul runtime IoT Edge v proměnné prostředí se vloží řetězec připojení používaný `EdgeHubConnectionString`. Díky tomu dostupné všem programům, které chce, aby se jeho použití.

Analogicky, se vloží certifikát, který chcete použít k ověření připojení k centru IoT Edge modulem runtime IoT Edge v souboru, jehož cesta je k dispozici v proměnné prostředí `EdgeModuleCACertificateFile`.

Tento kurz [vývoj a nasazení modul IoT Edge na simulovaném zařízení] [lnk-tutorial2] ukazuje, jak zajistit, že je certifikát v úložišti počítače ve vaší aplikaci modulu. Je zřejmé jakékoliv jiné metody připojení pomocí ní certifikátu důvěřovat.

## <a name="packaging-as-an-image"></a>Balení jako obrázek
Moduly IoT Edge jsou dodávány jako Image Dockeru.
Jak je znázorněno v tomto kurzu můžete použít sadu nástrojů Dockeru přímo, nebo Visual Studio Code [vývoj a nasazení modul IoT Edge na simulovaném zařízení] [lnk-tutorial2].

## <a name="next-steps"></a>Další postup

Poté, co při vývoji modulu, zjistěte, jak [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md).

