---
title: Přehled zabezpečení Azure Percept
description: Další informace o zabezpečení Azure Percept
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: b5a345139114842c83cb1f11792076efb1461870
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662703"
---
# <a name="azure-percept-security-overview"></a>Přehled zabezpečení Azure Percept

Zařízení Azure Percept DK jsou navržená s hardwarovým kořenem důvěryhodnosti – integrované zabezpečení na všech zařízeních. Pomáhá chránit senzory citlivé na soukromí, jako jsou kamery a mikrofony, odvozování dat, a umožňuje ověřování a autorizaci zařízení pro služby Azure Percept Studio.

> [!NOTE]
> Azure Percept DK je určena pro použití ve vývojových a testovacích prostředích a pro scénáře testování konceptů.

## <a name="devices"></a>Zařízení

### <a name="azure-percept-dk"></a>Azure Percept DK

Azure Percept DK obsahuje čip TPM (Trusted Platform Module) verze 2,0, který se dá využít k zabezpečenému připojení zařízení ke službám Azure Device Provisioning. TPM je podniková, standardně založená na standardech ISO od skupiny důvěryhodných Computingů a další informace o TPM najdete na [kompletní specifikaci tpm 2,0](https://trustedcomputinggroup.org/resource/tpm-library-specification/) nebo specifikaci ISO/IEC 11889. Další informace o tom, jak DPS může zřídit zařízení zabezpečeným způsobem, najdete v tématu [Azure IoT Hub Device Provisioning Service – ověření identity pomocí čipu TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation).

### <a name="azure-percept-system-on-module-som"></a>Systém Azure Percept v modulu (SOM)

Systém Azure Percept DK v modulu (SOM) a příslušenství Azure Percept audio SOM zahrnuje i jednotku mikrokontroléru (MCU) pro zajištění ochrany přístupu k integrovaným senzorům AI. Při každém spuštění firmware MCU ověřuje a autorizuje akcelerátor AI pomocí služby Azure Percept Studio Services pomocí architektury modulu složení identifikátoru zařízení (kost). KOST funguje tak, že rozbalí spouštění do vrstev a vytvoří jedinečné tajné klíče pro každou vrstvu a konfiguraci na základě jedinečného tajného klíče zařízení (UDS). Pokud je v jakémkoli okamžiku v řetězci spuštěný jiný kód nebo konfigurace, tajné klíče se budou lišit. Další informace o KOSTce si můžete přečíst na adrese [kostky v pracovní skupině](https://trustedcomputinggroup.org/work-groups/dice-architectures/). Pro konfiguraci přístupu ke službě Azure Percept Studio a požadovaných služeb najdete další informace v tématu **Konfigurace bran firewall pro Azure PERCEPT DK** .

Zařízení Azure Percept používají k zabezpečení firmwaru kořenový certifikát hardwaru. Spouštěcí paměť ROM zajišťuje integritu firmwaru mezi zavaděčem paměti ROM a operačním systémem (OS), který zase zajišťuje integritu ostatních softwarových součástí vytvářejících řetěz důvěryhodnosti.

## <a name="services"></a>Služby

### <a name="iot-edge"></a>IoT Edge

Služba Azure Percept DK se bezpečně připojuje k Azure Percept studiu a dalším službám Azure s využitím protokolu TLS (Transport Layer Security). Azure Percept DK je zařízení s povoleným Azure IoT Edge. IoT Edge runtime je kolekce programů, které zařízení přepíná do IoT Edge zařízení. Souhrnně IoT Edge komponenty modulu runtime umožňují zařízením IoT Edge přijímat kód, který se má spustit na hranici a sdělit výsledky. Azure Percept DK využívá kontejnery Docker k izolaci IoT Edge úloh z hostitelského operačního systému a aplikací s povoleným okrajem. Další informace o rozhraní Azure IoT Edge Security Framework najdete v článku o [IoT Edge Security Manager](https://docs.microsoft.com/azure/iot-edge/iot-edge-security-manager?view=iotedge-2018-06).

### <a name="device-update-for-iot-hub"></a>Aktualizace zařízení pro IoT Hub

Aktualizace zařízení pro IoT Hub umožňuje zabezpečená, škálovatelná a spolehlivá aktualizace prostřednictvím Air, která přináší obnovitelné zabezpečení na zařízení Azure Percept. Poskytuje rozsáhlé ovládací prvky pro správu a aktualizuje dodržování předpisů prostřednictvím přehledů. Azure Percept DK obsahuje předem integrované řešení aktualizace zařízení, které poskytuje odolnou aktualizaci (A/B) z firmwaru na vrstvy operačního systému.

<!---I think the below topics need to be somewhere else, (i.e. not on the main page)
--->

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Konfigurace bran firewall pro Azure Percept DK

Pokud instalace sítě vyžaduje, abyste výslovně povolili připojení vytvořená ze zařízení Azure Percept DK, Projděte si následující seznam součástí.

Tento kontrolní seznam je výchozím bodem pro pravidla brány firewall:

|Adresa URL (* = zástupný znak) |Odchozí porty TCP|    Využití|
|-------------------|------------------|---------|
|*. auth.azureperceptdk.azure.net|   443|    Ověřování a autorizace ve službě Azure DK SOM|
|*. auth.projectsantacruz.azure.net| 443|    Ověřování a autorizace ve službě Azure DK SOM|

Dále si Projděte seznam [připojení používaných službou Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/production-checklist?view=iotedge-2018-06#allow-connections-from-iot-edge-devices).

## <a name="additional-recommendations-for-deployment-to-production"></a>Další doporučení pro nasazení do produkčního prostředí

Azure Percept DK nabízí v krabici spoustu možností zabezpečení. Kromě těchto výkonných funkcí zabezpečení, které jsou součástí aktuální verze, společnost Microsoft také navrhuje při zvažování produkčního nasazení následující pokyny:

- Silná fyzická ochrana samotného zařízení
- Zajištění povolení šifrování v klidovém běhu
- Průběžné monitorování stav zařízení a rychlé reagování na výstrahy
- Omezení počtu správců, kteří mají k zařízení přístup

## <a name="next-steps"></a>Další kroky

Seznamte se s dostupnými [modely AI pro Azure Percept](./overview-ai-models.md).