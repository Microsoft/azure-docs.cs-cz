---
title: Požadavky na certifikaci spravované přes Edge
description: Požadavky na certifikační program na Edge spravované
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Managed Certification Requirements
ms.service: certification
ms.openlocfilehash: 744f81d0544a765f60793ece1aa539c6c37e39cd
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969153"
---
# <a name="azure-certification-edge-managed"></a>Správa certifikačního autority Azure 

Tento dokument popisuje možnosti specifické pro zařízení, které se budou prezentovat v katalogu zařízení s certifikací Azure. Funkce je jednotný atribut zařízení, který může popisovat zařízení. 

## <a name="program-purpose"></a>Účel programu

Certifikace spravovaná přes Edge – přírůstkovou certifikaci nad rámec základní certifikace zařízení certifikované pro Azure. Hraniční Správa se zaměřuje na standardy správy zařízení pro připojená zařízení Azure a ověřuje kompatibilitu modulu IoT Edge runtime pro nasazení a správu modulů. (Dříve byl tento program identifikován jako IoT Edge certifikační program.) 

Certifikace spravovaná přes Edge ověřuje IoT Edge kompatibility modulu runtime pro nasazení a správu modulů. Tento program zajišťuje důvěru ve správě zařízení IoT připojených k Azure.

## <a name="requirements"></a>Požadavky

Certifikát spravovaný přes hranice vyžaduje, aby všechny požadavky ze [základního programu zařízení Certified v Azure](.\program-requirements-azure-certified-device.md).

**DPS: účelem testu je zkontrolovat, jestli zařízení implementuje a podporuje IoT Hub Device Provisioning Service pomocí jedné ze tří metod ověření identity.**

| **Název**                | AzureReady. DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Cílová dostupnost** | Ignite (ve verzi Preview)                                                |
| **Platí pro**          | Jakékoli zařízení                                      |
| **OS**                  | Nelze určit                                                     |
| **Typ ověření**     | Automatizovaná                                                    |
| **Ověřování**          | AICS ověří, že kód zařízení podporuje DPS. **1.** Uživatel musí vybrat jednu z metod ověřování (X. 509, TPM a klíč SAS). **2.** V závislosti na metodě ověření identity musí uživatel provést odpovídající akci, jako **je například,** odeslat certifikát X. 509 do AICS spravovaného **oddílu DPS b)** implementovat klíč SAS nebo ověřovací klíč do zařízení. **3.** Pak se uživateli zobrazí tlačítko připojit, aby se připojil k AICS spravovanému IoT Hub přes DPS.                                                    |
| **Prostředky**           |                                                      |
| **Doporučené Azure:**     | –                                                    |

## <a name="iot-edge"></a>IoT Edge

**Modul runtime Edge existuje: účelem testu je ujistit se, že zařízení obsahuje IoT Edge runtime ($edgehub a $edgeagent) funguje správně.**

| **Název**                | EdgeManaged.EdgeRT                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Cílová dostupnost** | Nově dostupné                                                          |
| **Platí pro**          | Zařízení IoT Edge                                                   |
| **OS**                  | [Operační systém Tier1 a 2](../iot-edge/support.md)                                                     |
| **Typ ověření**     | Automatizovaná                                                    |
| **Ověřování**          | AICS ověří možnosti nasazení nainstalovaného IoT Edge RT. **1.** Uživatel musí zadat konkrétní operační systém (operační systém není v seznamu Tier1/2 není přijatý) **2.** AICS vygeneruje svůj soubor config. yaml a nasadí kanonický [simulovaný modul senzorů dočasnou na okraji](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-iot.simulated-temperature-sensor?tab=Overview) **3.** AICS ověří, že je na zařízení 4 nainstalovaný podsystém kontejneru Moby (Docker Compatible) **.** Výsledek testu se určuje na základě úspěšného nasazení simulovaného modulu a funkce prostředí Docker Edge, který je kompatibilní s Docker.                                                    |
| **Prostředky**           | **a)** [AICSho týmu](https://azure.microsoft.com/en-in/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/), **b)** [postup certifikace](./overview.md) (má všechny další prostředky), **c)** [požadavky](./program-requirements-azure-certified-device.md) |
| **Doporučené Azure:**     | –                                                    |

### <a name="capability-template"></a>Šablona schopností:

**IoT Edge Easy Setup: účelem testu je zajistit, aby bylo IoT Edge zařízení snadno nastavované a při ověřování fyzických zařízení bylo předinstalováno IoT Edge modul runtime.**

| **Název**                | EdgeManaged.PhysicalDevice                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Cílová dostupnost** | Nyní k dispozici (aktuálně blokováno z důvodu COVID-19)                                            |
| **Platí pro**          | Zařízení IoT Edge                                                   |
| **OS**                  | [Operační systém Tier1 a 2](../iot-edge/support.md)                                                     |
| **Typ ověření**     | Ruční/testovací ověření                                                    |
| **Ověřování**          | Výrobce OEM musí dodávat fyzické zařízení do správy IoT (HCL). V případě, že je na fyzickém zařízení zaškrtnuto ruční ověření: **1.** EdgeRT používá podsystém Moby (povolená verze Redistribuce). Není Docker **2.** Vyberte modul nejnovějšího Edge, který ověří schopnost nasadit Edge.                                                     |
| **Prostředky**           | **a)** [AICSho týmu](https://azure.microsoft.com/en-in/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/), **b)** [požadavky](./program-requirements-azure-certified-device.md) na [certifikační kroky](./overview.md) , **c)** |
| **Doporučené Azure:**     | –                                                    |