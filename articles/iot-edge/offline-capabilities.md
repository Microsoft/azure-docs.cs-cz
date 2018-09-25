---
title: Offline možnosti služby Azure IoT Edge | Dokumentace Microsoftu
description: Zjistěte, jak zařízení IoT Edge a moduly můžete pracovat offline dlouhou dobu, a jak IoT Edge můžete povolit běžná zařízení IoT příliš pracovat offline.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f4afad753da4a314ade3fb7433c6be3e489e05b0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033681"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices-preview"></a>Seznamte se s rozšířenou offline funkcí pro zařízení IoT Edge, moduly a podřízená zařízení (preview)

Azure IoT Edge podporuje rozšířené offline operace s vašimi zařízeními IoT Edge a umožní offline operace na zařízeních bez okrajů podřízené příliš. Tak dlouho, dokud zařízení IoT Edge došlo jednu příležitost k připojení ke službě IoT Hub a všechny podřízené zařízení můžete nadále funkce s přerušovaným nebo žádné připojení k Internetu. 

>[!NOTE]
>Podporu offline režimu pro IoT Edge je v [ve verzi public preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-it-works"></a>Jak to funguje

Když se zařízení IoT Edge dostane do offline režimu, Centrum Edge se provede na tři role. Nejprve ukládají se všechny zprávy, které přejde nadřazený a uloží je, dokud se zařízení znovu připojí. Za druhé funguje jménem služby IoT Hub k ověřování moduly a podřízená zařízení může i nadále fungovat. Třetí umožňuje komunikaci mezi zařízeními podřízený, které by normálně přejít prostřednictvím služby IoT Hub. 

Následující příklad ukazuje, jak funguje scénáři IoT Edge v režimu offline:

1. **Konfigurace zařízení IoT Edge.**

   Zařízení IoT Edge mají automaticky offline Funkce povolené. Rozšíření této funkce s jinými zařízeními IoT, musíte deklarovat vztah nadřízenosti a podřízenosti mezi zařízeními ve službě IoT Hub. 

2. **Synchronizace pomocí služby IoT Hub.**

   Alespoň jednou po instalaci modulu runtime IoT Edge, zařízení IoT Edge musí být online pro synchronizaci se službou IoT Hub. V této synchronizace zařízení IoT Edge načte podrobnosti o všech podřízených zařízení přiřazených k němu. Zařízení IoT Edge zároveň se bezpečně aktualizuje místní mezipaměti, povolení offline operace a načte nastavení pro místní ukládání telemetrických zpráv. 

3. **Přejdete do režimu offline.**

   Při odpojení ze služby IoT Hub, můžete zařízení IoT Edge, jeho nasazené moduly a všechny podřízené objekty zařízení IoT fungovat po neomezenou dobu. Moduly a podřízená zařízení můžete spustit a restartovat díky ověřování pomocí Centrum Edge při offline. Ukládají se místně telemetrie upstream vázán ke službě IoT Hub. Komunikace mezi moduly nebo mezi zařízeními IoT podřízené zachovaný prostřednictvím přímé metody nebo zprávy. 

4. **Znovu připojit a znovu synchronizujte službou IoT Hub.**

   Po obnovení připojení pomocí služby IoT Hub znovu synchronizuje zařízení IoT Edge. Místně uložené zprávy doručovaly ve stejném pořadí, ve kterém byly uloženy. Rozdíly mezi požadované a ohlášené vlastnosti moduly a zařízeními jsou sloučeny. Zařízení IoT Edge aktualizuje všechny změny na svou sadu zařízení IoT přiřazené podřízené.

## <a name="restrictions-and-limits"></a>Omezení a omezení

Rozšířené možnosti offline popsaných v tomto článku jsou dostupné v [IoT Edge verze 1.0.2 nebo novější](https://github.com/Azure/azure-iotedge/releases). Starší verze mají podmnožinu offline funkcí. Existující hraničních zařízeních IoT zařízení, která nemají rozšířené možnosti offline nelze upgradovat tak, že změníte verzi modulu runtime, ale je potřeba překonfigurovat tak s novou identitu zařízení IoT Edge k získání těchto funkcí. 

Prodloužená odborná pomoc offline je k dispozici ve všech oblastech, kde je k dispozici, s výjimkou východní USA a západní Evropa služby IoT Hub. 

Pouze zařízení Edge IoT se dá přidat jako podřízenou zařízení. 

Zařízení IoT Edge a jejich zařízení přiřazené podřízené, můžou fungovat po synchronizaci počáteční, jednorázové po neomezenou dobu offline. Úložiště zpráv, které však závisí na time to live (TTL) nastavení a dostupné místo na disku pro ukládání zpráv. 

## <a name="set-up-an-edge-device"></a>Nastavit hraniční zařízení

Pro libovolné zařízení IoT Edge, který chcete provést během delší dobu offline a nakonfigurujte modul runtime IoT Edge pro komunikaci pomocí protokolu MQTT. 

Pro zařízení IoT Edge rozšířit možnosti rozšířené offline do zařízení IoT podřízené musíte deklarovat vztahy nadřazenosti a podřízenosti na webu Azure Portal.

### <a name="set-the-upstream-protocol-to-mqtt"></a>Nastavit nadřazený protokol MQTT

Nakonfigurujte Centrum Edge a agent Edge ke komunikaci s jako nadřazený protokol MQTT. Tento protokol je deklarován pomocí proměnných prostředí v manifestu nasazení. 

Na webu Azure Portal, můžete přístup Centrum Edge a definice modulu agenta Edge tak, že vyberete **konfigurovat rozšířená nastavení modulu Runtime Edge** tlačítko při nastavování moduly pro nasazení. Pro oba moduly, vytvořte proměnnou prostředí volá **UpstreamProtocol** a nastavte jej na hodnotu **MQTT**. 

Nasazení šablony JSON proměnné prostředí jsou deklarovány, jak je znázorněno v následujícím příkladu: 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"
    },
    "env": {
        "UpstreamProtocol": {
            "value": "MQTT"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

### <a name="assign-child-devices"></a>Přiřazení zařízení podřízené

Podřízená zařízení může být jakékoli zařízení bez okrajů zaregistrované u stejné služby IoT Hub. Můžete spravovat relaci nadřazený podřízený na vytvoření nového zařízení nebo ze stránky detaily zařízení buď nadřazené zařízení IoT Edge nebo podřízené zařízení IoT. 

   ![Správa podřízených zařízení ze stránky detaily zařízení IoT Edge](./media/offline-capabilities/manage-child-devices.png)

Nadřazené zařízení může mít více podřízených zařízení, ale podřízené zařízení může mít pouze jeden nadřazený prvek.

## <a name="optional-offline-settings"></a>Volitelná nastavení

Pokud očekáváte, že vaše zařízení chcete vyzkoušet dlouho offline období, po které chcete shromažďovat všechny zprávy, které byly vygenerovány, nakonfigurovat Centrum Edge tak, aby ho můžete uložit všechny zprávy. Existují dvě změny, můžete pro Centrum Edge umožňuje dlouhodobé úložiště zpráv. Nejdřív zvýšit time to live nastavení a pak přidejte další místo na disku pro úložiště zpráv. 

### <a name="time-to-live"></a>Hodnota TTL (Time to Live)

Time to live nastavení je množství času (v sekundách), který zprávu počkat, který bude doručen před vypršením platnosti. Výchozí hodnota je 7200 sekund (dva hodin). 

Toto nastavení je požadovaná vlastnost Centrum Edge, která je uložena ve dvojčeti modulu. Můžete ho nakonfigurovat na webu Azure Portal, v **konfigurovat rozšířená nastavení modulu Runtime Edge** části nebo přímo v nasazení manifestu. 

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="additional-offline-storage"></a>Další úložiště v režimu offline

Ve výchozím nastavení zprávy jsou uloženy v systému souborů kontejneru Centrum Edge. Pokud toto množství úložiště není dostatečná pro vaše potřeby v režimu offline, který vyhradíte místní úložiště na zařízení IoT Edge. Je potřeba vytvořit proměnnou prostředí pro Centrum Edge, který odkazuje na složku v kontejneru úložiště. Pak použijte možnosti vytvořit pro přiřazení této složky úložiště do složky na hostitelském počítači. 

Na webu Azure Portal v můžete nakonfigurovat proměnné prostředí a možnosti vytvořit pro modul Centrum Edge **konfigurovat rozšířená nastavení modulu Runtime Edge** oddílu. Nebo můžete vytvořit přímo v manifestu nasazení. 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": "{\"HostConfig\":{\"Binds\":[\"C:\\\\HostStoragePath:C:\\\\ModuleStoragePath\"],\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"
    },
    "env": {
        "storageFolder": {
            "value": "C:\\\\ModuleStoragePath"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

## <a name="next-steps"></a>Další postup

Povolit rozšířené offline operace ve vašich scénářích transparentní Brána pro [Linux](how-to-create-transparent-gateway-linux.md) nebo [Windows](how-to-create-transparent-gateway-windows.md) zařízení. 