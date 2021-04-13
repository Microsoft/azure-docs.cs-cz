---
title: Požadavky na zařízení s certifikací Azure
description: Požadavky na program zařízení Certified v Azure
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Azure Certified Device Certification Requirements
ms.service: certification
ms.openlocfilehash: 497ffa4b3026491d6aa95df87708b3b1f2f1619e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308281"
---
# <a name="azure-certified-device-requirements"></a>Požadavky na zařízení s certifikací Azure 
(dříve označované jako IoT Hub)

Tento dokument popisuje možnosti specifické pro zařízení, které se budou prezentovat v katalogu zařízení s certifikací Azure. Funkce je jednotný atribut zařízení, který může být implementace softwaru nebo kombinace softwarových a hardwarových implementací. 

## <a name="program-purpose"></a>Účel programu

Microsoft zjednodušuje certifikaci zařízení IoT a certifikace certifikovaných v Azure je základní certifikační autoritou, aby bylo zajištěno, že jsou všechny typy zařízení zřízené do Azure IoT Hub bezpečně.

Příslib certifikace zařízení s certifikací Azure je:

1. Telemetrie podpory zařízení, která funguje s IoT Hub
2.  Podpora zařízení IoT Hub Device Provisioning Service (DPS) pro bezpečné zřízení v Azure IoT Hub
3.  Zařízení podporuje snadné zadání cílového oboru ID DPS bez nutnosti, aby uživatel musel znovu kompilovat vložený kód.
4.  Volitelně ověří jiné prvky, jako je například Cloud na zprávy zařízení, přímé metody a vlákna zařízení. 

## <a name="requirements"></a>Požadavky

**Požadovanou Zařízení do cloudu: účelem testu je zajistit, aby zařízení, která odesílají telemetrii, fungovala IoT Hub**

| **Název**                | AzureCertified. D2C                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Cílová dostupnost** | Nově dostupné                                                |
| **Platí pro**          | Zařízení typu list/hraniční zařízení                                      |
| **OS**                  | Nelze určit                                                     |
| **Typ ověření**     | Automatizovaná                                                    |
| **Ověřování**          | Zařízení musí IoT Hub odesílat všechna schémata telemetrie. Microsoft poskytuje [pracovní postup na portálu](https://certify.azure.com/) pro provádění testů. Zařízení do cloudu (povinné): **1.** Ověří, jestli zařízení může odesílat zprávy do AICS spravované IoT Hub **2.** Uživatel musí zadat počet a četnost zpráv. **3.** AICS ověří, že telemetrie přijímá instance centra. |
| **Prostředky**           | [Postup certifikace](./overview.md) (má všechny další prostředky) |

**Požadovanou DPS: účelem testu je zkontrolovat, jestli zařízení implementuje a podporuje IoT Hub Device Provisioning Service pomocí jedné ze tří metod ověření identity.**

| **Název**                | AzureCertified. DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Cílová dostupnost** | Nová                                                          |
| **Platí pro**          | Jakékoli zařízení                                                   |
| **OS**                  | Nelze určit                                                     |
| **Typ ověření**     | Automatizovaná                                                    |
| **Ověřování**          | Zařízení podporuje snadné zadání cílového oboru ID DPS bez nutnosti překompilovat vložený kód. Microsoft poskytuje [pracovní postup na portálu](https://certify.azure.com) , který provede testy, aby ověřil, jestli zařízení podporuje DPS **1.** Uživatel musí vybrat jednu z metod ověřování (X. 509, TPM a klíč SAS) **2.** V závislosti na metodě ověřování uživatel potřebuje provést odpovídající akci, jako **je například,** odeslat certifikát X. 509 do AICS spravovaného **oddílu DPS b)** implementovat klíč SAS nebo ověřovací klíč do zařízení. |
| **Prostředky**           | [Služba Device Provisioning – přehled](../iot-dps/about-iot-dps.md) |

**[Pokud je implementován] Cloud do zařízení: účelem testu je zajistit, aby se zprávy mohly odesílat z cloudu do zařízení.**                                                              

| **Název**                | AzureCertified. C2D                                                  |
| ----------------------- | ------------------------------------------------------------ |
| **Cílová dostupnost** | Nově dostupné                                            |
| **Platí pro**          | Zařízení typu list/hraniční zařízení                                                   |
| **OS**                  | Nelze určit                                                     |
| **Typ ověření**     | Automatizovaná                                                    |
| **Ověřování**          | Zařízení musí být schopné z IoT Hub z cloudu až po zprávy ze zařízení. Microsoft poskytuje [pracovní postup na portálu](https://certify.azure.com) pro provedení těchto testů. Cloud do zařízení (Pokud je implementováno): **1.** Ověří, zda může zařízení přijímat zprávy z IoT Hub **2.** AICS posílá náhodnou zprávu a ověří zprávu prostřednictvím potvrzení ze zařízení.  |
| **Prostředky**           | **a)** [postup certifikace](./overview.md) (má všechny další prostředky) **b)** [posílání zpráv z cloudu na zařízení z IoT Hub](../iot-hub/iot-hub-devguide-messages-c2d.md) |

**[Pokud je implementován] Přímé metody: účelem testu je zajistit, aby zařízení fungovala s IoT Hub a podporovala přímé metody.**

| **Název**                | AzureCertified.DirectMethods                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Cílová dostupnost** | Nově dostupné                                            |
| **Platí pro**          | Zařízení typu list/hraniční zařízení                                                   |
| **OS**                  | Nelze určit                                                     |
| **Typ ověření**     | Automatizovaná                                                    |
| **Ověřování**          | Zařízení musí být schopné přijímat a odpovídat na požadavky příkazů od IoT Hub. Microsoft poskytuje [pracovní postup na portálu](https://certify.azure.com) pro provádění testů. Přímé metody (pokud jsou implementovány) **1.** Uživatel musí zadat datovou část metody přímé metody. **2.** AICS ověří, že zadaná žádost o datovou část je odeslána z centra a zprávy ACK přijaté zařízením. |
| **Prostředky**           | **a)** [postup certifikace](./overview.md) (má všechny další prostředky) **b)** [pochopit přímé metody z IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md) |

**[Pokud je implementován] Vlastnost vlákna zařízení: účelem testu je zajistit, aby zařízení, která odesílají telemetrii, fungovala s IoT Hub a podporovala některé IoT Hub možnosti, jako jsou přímé metody a vlastnost se zdvojeným zařízením.**

| **Název**                                  | AzureCertified.DeviceTwin                                      |
| ----------------------------------------- | ------------------------------------------------------------ |
| **Cílová dostupnost**                   | Nově dostupné                                            |
| **Platí pro**                            | Zařízení typu list/hraniční zařízení                                                   |
| **OS**                                    | Nelze určit                                                     |
| **Typ ověření**                       | Automatizovaná                                                       |
| **Ověřování**                            | Zařízení musí IoT Hub odesílat všechna schémata telemetrie. Microsoft poskytuje [pracovní postup na portálu](https://certify.azure.com) pro provádění testů. Vlastnost vlákna zařízení (Pokud se implementuje) **1.** AICS ověří vlastnost čtení/zápisu v zařízení s dvojitým zápisem JSON **2.** Uživatel musí určit datovou část JSON, která se má změnit na **3.** AICS ověří zadané požadované vlastnosti odeslané ze zprávy IoT Hub a ACK, kterou zařízení přijalo. |
| **Prostředky**                             | **a)** [certifikační kroky](./overview.md) (má všechny další prostředky) **b)** [používají vlákna zařízení s IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) |
