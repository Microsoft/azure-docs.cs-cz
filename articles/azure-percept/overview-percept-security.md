---
title: Přehled zabezpečení Azure Percept
description: Další informace o zabezpečení Azure Percept
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: template-concept
ms.openlocfilehash: 93884fb87f87651054ffff0a04c4910de634a5eb
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567640"
---
# <a name="azure-percept-security-overview"></a>Přehled zabezpečení Azure Percept

Zařízení Azure Percept jsou navržená s hardwarovým kořenem důvěryhodnosti. Toto integrované zabezpečení pomáhá chránit odvozená data a senzory citlivé na soukromí, jako jsou kamery a mikrofony, a umožňuje ověřování a autorizaci zařízení pro služby Azure Percept Studio.

> [!NOTE]
> Azure Percept DK má licenci pro použití jenom ve vývojových a testovacích prostředích.

## <a name="devices"></a>Zařízení

### <a name="azure-percept-dk"></a>Azure Percept DK

Azure Percept DK obsahuje čip TPM (Trusted Platform Module) verze 2,0, který se dá využít k připojení zařízení ke službám Azure Device Provisioning (DPS) s vyšším zabezpečením. TPM je podniková Standard ISO od skupiny Trusted Computing. Další informace o kompletní specifikaci TPM 2,0 nebo specifikaci ISO/IEC 11889 najdete na [webu důvěryhodných výpočetních skupin](https://trustedcomputinggroup.org/resource/tpm-library-specification/) . Další informace o tom, jak DPS může zřídit zařízení zabezpečeně, najdete v tématu [ověření identity pomocí Azure IoT Hub Device Provisioning Service-TPM](../iot-dps/concepts-tpm-attestation.md).

### <a name="azure-percept-system-on-modules-soms"></a>Azure Percept – moduly (SoMs)

Azure Percept Vision System – on-Module (SoM) a Azure Percept audio SoM zahrnuje i jednotku mikrokontrolerů (MCU) pro ochranu přístupu k vestavěným senzorům AI. Při každém spuštění firmware MCU ověřuje a autorizuje akcelerátor AI pomocí služby Azure Percept Studio Services pomocí architektury modulu složení identifikátoru zařízení (kost). KOST funguje rozbalením spouštěcích vrstev a vytvořením jedinečných tajných kódů zařízení (UDS) pro každou vrstvu a konfiguraci. Pokud je v jakémkoli bodě řetězce spuštěný jiný kód nebo konfigurace, tajné klíče se budou lišit. Další informace o KOSTce si můžete přečíst na adrese [kostky v pracovní skupině](https://trustedcomputinggroup.org/work-groups/dice-architectures/). Informace o konfiguraci přístupu k Azure Percept studia a požadovaných služeb najdete v článku věnovaném [konfiguraci bran firewall pro Azure PERCEPT DK](concept-security-configuration.md).

Zařízení Azure Percept používají k zabezpečení firmwaru kořenový adresář hardwaru důvěryhodnosti. Spouštěcí paměť ROM zajišťuje integritu firmwaru mezi zavaděčem paměti ROM a operačním systémem (OS), což zase zajišťuje integritu ostatních softwarových součástí a vytváří tak řetěz důvěryhodnosti.

## <a name="services"></a>Služby

### <a name="iot-edge"></a>IoT Edge

Azure Percept DK se připojuje ke službě Azure Percept Studio s dalším zabezpečením a dalšími službami Azure, které využívají protokol TLS (Transport Layer Security). Azure Percept DK je zařízení s podporou Azure IoT Edge. IoT Edge runtime je kolekce programů, které zařízení přepíná do IoT Edge zařízení. Souhrnně IoT Edge komponenty modulu runtime umožňují zařízením IoT Edge přijímat kód, který se má spustit na hranici a sdělit výsledky. Azure Percept DK využívá kontejnery Docker k izolaci IoT Edge úloh z hostitelského operačního systému a aplikací s podporou Edge. Další informace o rozhraní Azure IoT Edge Security Framework najdete v článku o [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

### <a name="device-update-for-iot-hub"></a>Aktualizace zařízení pro IoT Hub

Aktualizace zařízení pro IoT Hub umožňuje bezpečnější, škálovatelnou a spolehlivou aktualizaci prostřednictvím Air, která přináší obnovitelné zabezpečení na zařízení Azure Percept. Poskytuje rozsáhlé ovládací prvky pro správu a aktualizuje dodržování předpisů prostřednictvím přehledů. Azure Percept DK obsahuje předem integrované řešení aktualizace zařízení, které poskytuje odolnou aktualizaci (A/B) z firmwaru na vrstvy operačního systému.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o konfiguracích brány firewall a doporučeních pro zabezpečení](concept-security-configuration.md)

> [!div class="nextstepaction"]
> [Kupte si Azure Percept ze Storu z online obchodu Microsoftu](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
