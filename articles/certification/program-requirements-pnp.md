---
title: Požadavky na certifikaci technologie Plug and Play IoT
description: Požadavky na certifikační program IoT technologie Plug and Play
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: IoT Plug and Play Certification Requirements
ms.service: certification
ms.openlocfilehash: b26fab6f8b92e3cb996f545f1f6201d32b1eaced
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310508"
---
# <a name="iot-plug-and-play-certification-requirements"></a>Požadavky na certifikaci technologie Plug and Play IoT

Tento dokument popisuje možnosti specifické pro zařízení, které se budou prezentovat v katalogu zařízení Azure IoT. Funkce je jednotný atribut zařízení, který může být implementace softwaru nebo kombinace softwarových a hardwarových implementací.

## <a name="program-purpose"></a>Účel programu

IoT technologie Plug and Play Preview umožňuje tvůrcům řešení integrovat inteligentní zařízení s jejich řešeními bez jakékoli ruční konfigurace. V jádru IoT technologie Plug and Play je model zařízení, který zařízení používá k inzerování svých schopností k aplikaci s podporou technologie Plug and Play IoT. Tento model je strukturovaný jako sada elementů: telemetrie, vlastnosti a příkazy.

Potvrzení o certifikaci technologie Plug and Play IoT:

1.  Definované modely zařízení a rozhraní jsou kompatibilní s [digitálním jazykem definice](https://github.com/Azure/opendigitaltwins-dtdl) .  
2.  Zabezpečené zřizování a snadný přenos vlastnictví oboru ID v rámci služeb Device Provisioning
3.  Jednoduchá integrace s řešeními založenými na službě Azure IoT s využitím [digitálních vláken API](../iot-pnp/concepts-digital-twin.md)  : Azure IoT Hub a Azure IoT Central
4.  Ověřené pravdy produktu na certifikovaných zařízeních

## <a name="requirements"></a>Požadavky

**Požadovanou Zařízení do cloudu: účelem testu je zajistit, aby zařízení, která odesílají telemetrii, fungovala IoT Hub**

| **Název**                | IoTPnP. D2C                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Cílová dostupnost** | Nově dostupné                                                |
| **Platí pro**          | Zařízení typu list/hraniční zařízení                                      |
| **OS**                  | Nelze určit                                                     |
| **Typ ověření**     | Automatizovaná                                                    |
| **Ověřování**          | Zařízení musí IoT Hub odesílat všechna schémata telemetrie. Microsoft poskytuje [pracovní postup na portálu](https://certify.azure.com) pro provádění testů. Zařízení do cloudu (povinné): **1.** Ověří, jestli zařízení může odesílat zprávy do AICS spravované IoT Hub **2.** Uživatel musí zadat počet a četnost zpráv. **3.** AICS ověří, že telemetrie přijímá instance centra. |
| **Prostředky**           | [Postup certifikace](./overview.md) (má všechny další prostředky) |

**Požadovanou DPS: účelem testu je zkontrolovat, jestli zařízení implementuje a podporuje IoT Hub Device Provisioning Service pomocí jedné ze tří metod ověření identity.**

| **Název**                | IoTPnP. DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Cílová dostupnost** | Nově dostupné                                                |
| **Platí pro**          | Jakékoli zařízení                                                   |
| **OS**                  | Nelze určit                                                     |
| **Typ ověření**     | Automatizovaná                                                    |
| **Ověřování**          | Zařízení musí implementovat snadný přenos vlastnictví oboru ID DPS, aniž by bylo nutné znovu kompilovat vložený kód. Microsoft poskytuje [pracovní postup na portálu](https://certify.azure.com) , který provede testy, aby ověřil, jestli zařízení podporuje DPS **1.** Uživatel musí vybrat jednu z metod ověřování (X. 509, TPM a klíč SAS) **2.** V závislosti na metodě ověřování uživatel potřebuje provést odpovídající akci, jako **je například,** odeslat certifikát X. 509 do AICS spravovaného **oddílu DPS b)** implementovat klíč SAS nebo ověřovací klíč do zařízení. |
| **Prostředky**           | **a)** Ukázkový konfigurační soubor s [přehledem služby Device Provisioning](../iot-dps/about-iot-dps.md), **b)** [pro přenos rozsahu ID DPS](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview-pnp/serializer/samples/devicetwin_simplesample) |

**Požadovanou DTDL v2: účel testu, který zajistí, aby definované modely zařízení a rozhraní splňovaly požadavky na jazyk verze v2 pro digitální vlákna.**                                                              

| **Název**                | IoTPnP.DTDL                                                  |
| ----------------------- | ------------------------------------------------------------ |
| **Cílová dostupnost** | Nově dostupné                                                |
| **Platí pro**          | Jakékoli zařízení                                                   |
| **OS**                  | Nelze určit                                                     |
| **Typ ověření**     | Automatizovaná                                                    |
| **Ověřování**          | [Pracovní postup portálu](https://certify.azure.com) se ověří: **1.** Oznámení ID modelu a zkontrolujte, že je zařízení připojené, a to pomocí protokolu WebSockets MQTT nebo MQTT **.** Modely vyhovují DTDL v2 **3.** Telemetrie, vlastnosti a příkazy jsou správně naimplementované a komunikují mezi IoT Hubmi, které jsou na zařízení nefunkční, a na zařízení. |
| **Prostředky**           | [Public Preview aktualizace aktualizací](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |

**Požadovanou Modely zařízení se publikují v úložišti veřejného modelu.**

| **Název**                | IoTPnP.ModelRepo                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Cílová dostupnost** | Nově dostupné                                                |
| **Platí pro**          | Jakékoli zařízení                                                   |
| **OS**                  | Nelze určit                                                     |
| **Typ ověření**     | Automatizovaná                                                    |
| **Ověřování**          | Všechny modely zařízení musí být publikované ve veřejném úložišti. Modely zařízení jsou vyřešeny prostřednictvím modelů dostupných ve veřejném úložišti **1.** Před odesláním certifikátu musí uživatel ručně publikovat modely do veřejného úložiště. **2.** Všimněte si, že po publikování modelů není proměnlivá. Důrazně doporučujeme publikovat pouze v případě, že jsou dokončeny modely a kód zařízení. * 1 * 1 uživatel musí kontaktovat podporu Microsoftu, aby tyto modely odvolaly po publikování do úložiště modelu **3.** [Pracovní postup portálu](https://certify.azure.com) kontroluje existenci modelů ve veřejném úložišti, když je zařízení připojené k certifikační službě. |
| **Prostředky**           | [Úložiště modelů](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |

**Požadovanou Ověření fyzického zařízení pomocí GSG**

| **Název**                                  | IoTPnP.Physicaldevice                                      |
| ----------------------------------------- | ------------------------------------------------------------ |
| **Cílová dostupnost**                   | Nově dostupné                                                |
| **Platí pro**                            | Jakékoli zařízení                                                   |
| **OS**                                    | Nelze určit                                                     |
| **Typ ověření**                       | Ruční                                                       |
| **Ověřování**                            | Aby partneři mohli [iotcert@microsoft.com](mailto:iotcert@microsoft.com) provádět další ověřování na fyzickém zařízení, musí se zapojit do programu Microsoft Contact (). V důsledku situace COVID-19 zkoumáme různé způsoby, jak provádět ověřování fyzických zařízení bez odeslání zařízení do Microsoftu. |
| **Prostředky**                             | Podrobnosti jsou k dispozici později.                                 |
| **Doporučené Azure**       | –    |

**[Pokud je implementován] Rozhraní informace o zařízení: účelem testu je ověřit, jestli je v kódu zařízení správně implementované rozhraní informací o zařízení.**

| **Název**                | IoTPnP.DeviceInfoInterface                                   |
| ----------------------- | ------------------------------------------------------------ |
| **Cílová dostupnost** | Nově dostupné                                                |
| **Platí pro**          | Jakékoli zařízení                                                   |
| **OS**                  | Nelze určit                                                     |
| **Typ ověření**     | Automatizovaná                                                    |
| **Ověřování**          | [Pracovní postup portálu](https://certify.azure.com) ověřuje, že kód zařízení implementuje rozhraní informace o zařízení **1.** Zkontroluje, jestli se hodnoty vysílají kódem zařízení IoT Hub **2.** Kontroluje rozhraní, které je implementováno v DCM (Tato implementace se změní v DTDL v2) **3.** Kontroly vlastností nejsou možné zapisovat (jen pro čtení) **4.** Kontroluje, zda je typ schématu řetězec nebo hodnota Long, nikoli hodnota null. |
| **Prostředky**           | [Rozhraní definované společností Microsoft](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |
| **Doporučené Azure**  | –                                                          |

**[Pokud je implementován] Cloud do zařízení: účelem testu je zajistit, aby se zprávy mohly odesílat z cloudu do zařízení.**

| **Název**                | IoTPnP. C2D                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Cílová dostupnost** | Nově dostupné                                                |
| **Platí pro**          | Zařízení typu list/hraniční zařízení                                      |
| **OS**                  | Nelze určit                                                     |
| **Typ ověření**     | Automatizovaná                                                    |
| **Ověřování**          | Zařízení musí být schopné z IoT Hub z cloudu až po zprávy ze zařízení. Microsoft poskytuje [pracovní postup na portálu](https://certify.azure.com) pro provedení těchto testů. Cloud do zařízení (Pokud je implementováno): **1.** Ověří, zda může zařízení přijímat zprávy z IoT Hub **2.** AICS posílá náhodnou zprávu a ověří zprávu prostřednictvím potvrzení ze zařízení. |
| **Prostředky**           | **1.** [postup certifikace](./overview.md) (má všechny další prostředky), **2.** [Posílání zpráv z cloudu na zařízení z IoT Hub](../iot-hub/iot-hub-devguide-messages-c2d.md) |

**[Pokud je implementován] Přímé metody: účelem testu je zajistit, aby zařízení fungovala s IoT Hub a podporovala přímé metody.**

| **Název**                | IoTPnP.DirectMethods                                     |
| ----------------------- | ------------------------------------------------------------ |
| **Cílová dostupnost** | Nově dostupné                                                |
| **Platí pro**          | Zařízení typu list/hraniční zařízení                                      |
| **OS**                  | Nelze určit                                                     |
| **Typ ověření**     | Automatizovaná                                                    |
| **Ověřování**          | Zařízení musí být schopné přijímat a odpovídat na požadavky příkazů od IoT Hub. Microsoft poskytuje [pracovní postup na portálu](https://certify.azure.com) pro provádění testů. Přímé metody (Pokud je implementováno): **1.** Uživatel musí zadat datovou část metody přímé metody. **2.** AICS ověří, že zadaná žádost o datovou část je odeslána z centra a zprávy ACK přijaté zařízením. |
| **Prostředky**           | **1.** [postup certifikace](./overview.md) (má všechny další prostředky), **2.** [Pochopení přímých metod od IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md) |

**[Pokud je implementován] Vlastnost vlákna zařízení: účelem testu je zajistit, aby zařízení, která odesílají telemetrii, fungovala s IoT Hub a podporovala některé IoT Hub možnosti, jako jsou přímé metody a vlastnost se zdvojeným zařízením.**

| **Název**                | IoTPnP.DeviceTwin                                        |
| ----------------------- | ------------------------------------------------------------ |
| **Cílová dostupnost** | Nově dostupné                                                |
| **Platí pro**          | Zařízení typu list/hraniční zařízení                                      |
| **OS**                  | Nelze určit                                                     |
| **Typ ověření**     | Automatizovaná                                                    |
| **Ověřování**          | Zařízení musí IoT Hub odesílat všechna schémata telemetrie. Microsoft poskytuje [pracovní postup na portálu](https://certify.azure.com) pro provádění testů. Vlastnost vlákna zařízení (Pokud je implementovaná): **1.** AICS ověří vlastnost čtení/zápisu v zařízení s dvojitým zápisem JSON **2.** Uživatel musí určit datovou část JSON, která se má změnit na **3.** AICS ověří zadané požadované vlastnosti odeslané ze zprávy IoT Hub a ACK, kterou zařízení přijalo. |
| **Prostředky**           | **1.** [postup certifikace](./overview.md) (má všechny další prostředky), **2.** [Používat vlákna zařízení s IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) |
