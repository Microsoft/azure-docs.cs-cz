---
title: Ovládání zařízení offline – Azure IoT Edge | Dokumenty společnosti Microsoft
description: Zjistěte, jak mohou zařízení a moduly IoT Edge pracovat bez připojení k internetu delší dobu a jak ioT Edge může povolit, aby běžná zařízení IoT fungovala i offline.
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 55512491121aee28404ab5f85b4223c67a2f0e1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80236064"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Principy rozšířených offline funkcí pro zařízení, moduly a podřízená zařízení IoT Edge

Azure IoT Edge podporuje rozšířené offline operace na vašich zařízeních IoT Edge a umožňuje offline operace i na podřízených zařízeních než IoT Edge. Dokud zařízení IoT Edge měl jednu možnost připojení k ioT hubu, toto zařízení a všechna podřízená zařízení mohou nadále fungovat s přerušovaným nebo žádným připojením k internetu.

## <a name="how-it-works"></a>Jak to funguje

Když zařízení IoT Edge přejde do offline režimu, centrum IoT Edge převezme tři role. Nejprve ukládá všechny zprávy, které by šlápnout nahoru a uloží je, dokud se zařízení znovu připojí. Za druhé, jedná jménem služby IoT Hub k ověření modulů a podřízených zařízení, aby mohly nadále fungovat. Za třetí umožňuje komunikaci mezi podřízenými zařízeními, která by normálně procházela službou IoT Hub.

Následující příklad ukazuje, jak scénář IoT Edge funguje v režimu offline:

1. **Konfigurovat zařízení**

   Zařízení IoT Edge mají automaticky povolené offline funkce. Chcete-li rozšířit tuto možnost na další zařízení IoT, musíte deklarovat vztah nadřazený podřízený mezi zařízeními v centru IoT Hub. Potom nakonfigurujete podřízená zařízení tak, aby důvěřovala přiřazenému nadřazenému zařízení a směrovala komunikaci mezi zařízeními a cloudem prostřednictvím nadřazeného zařízení jako brány.

2. **Synchronizace s ioT hubem**

   Alespoň jednou po instalaci runtime IoT Edge, zařízení IoT Edge musí být online pro synchronizaci s IoT Hub. V této synchronizaci zařízení IoT Edge získá podrobnosti o všech podřízených zařízeních, která jsou k němu přiřazena. Zařízení IoT Edge také bezpečně aktualizuje svou místní mezipaměť, aby povolilo operace offline a načítalo nastavení pro místní úložiště telemetrických zpráv.

3. **Přejít do offline**

   Při odpojení od služby IoT Hub může zařízení IoT Edge, jeho nasazené moduly a všechna poddajná zařízení IoT pracovat neomezeně dlouho. Moduly a podřízená zařízení se můžou spouštět a restartovat ověřováním pomocí centra IoT Edge v offline. Telemetrie vázaná proti proudu k IoT Hubu se ukládá místně. Komunikace mezi moduly nebo mezi podřízenými zařízeními IoT se udržuje prostřednictvím přímých metod nebo zpráv.

4. **Opětovné připojení a opětovná synchronizace pomocí služby IoT Hub**

   Po obnovení připojení k ioT hubu se zařízení IoT Edge znovu synchronizuje. Místně uložené zprávy se doručují do služby IoT Hub hned, ale jsou závislé na rychlosti připojení, latenci služby IoT Hub a souvisejících faktorech. Jsou dodávány ve stejném pořadí, ve kterém byly uloženy.

   Všechny rozdíly mezi požadované a hlášené vlastnosti modulů a zařízení jsou odsouhlaseny. Zařízení IoT Edge aktualizuje všechny změny své sady přiřazených podřízených zařízení IoT.

## <a name="restrictions-and-limits"></a>Omezení a omezení

Rozšířené offline funkce popsané v tomto článku jsou k dispozici v [IoT Edge verze 1.0.7 nebo vyšší](https://github.com/Azure/azure-iotedge/releases). Starší verze mají podmnožinu funkcí offline. Stávající zařízení IoT Edge, která nemají rozšířené offline funkce, nelze upgradovat změnou verze runtime, ale musí být překonfigurována s novou identitou zařízení IoT Edge, abyste získali tyto funkce.

Jako podřízená zařízení lze přidat pouze zařízení než IoT Edge.

Zařízení IoT Edge a jejich přiřazená podřízená zařízení můžou po počáteční jednorázové synchronizaci fungovat neomezeně dlouho offline. Úložiště zpráv však závisí na době aktivního nastavení (TTL) a dostupném místě na disku pro ukládání zpráv.

## <a name="set-up-parent-and-child-devices"></a>Nastavení nadřazených a podřízených zařízení

Aby zařízení IoT Edge rozšířilo své rozšířené offline funkce na podřízená zařízení IoT, je třeba provést dva kroky. Nejprve deklarujte vztahy nadřazený podřízený na webu Azure Portal. Za druhé vytvořte vztah důvěryhodnosti mezi nadřazeným zařízením a všemi podřízenými zařízeními a pak nakonfigurujte komunikaci mezi zařízeními a cloudem tak, aby procházela nadřazenou jako bránu.

### <a name="assign-child-devices"></a>Přiřazení podřízených zařízení

Podřízenými zařízeními může být libovolné zařízení než IoT Edge registrované do stejného iot hubu. Nadřazená zařízení mohou mít více podřízených zařízení, ale podřízené zařízení má pouze jednu nadřazenou položku. Existují tři možnosti nastavení podřízených zařízení na hraniční zařízení: prostřednictvím portálu Azure, pomocí rozhraní příkazového příkazu Azure nebo pomocí sady SDK služby IoT Hub.

V následujících částech jsou uvedeny příklady, jak můžete deklarovat vztah nadřazený/podřízený v centru IoT Hub pro existující zařízení IoT. Pokud vytváříte nové identity zařízení pro vaše podřízená zařízení, další informace najdete v [tématu Ověření podřízeného zařízení do služby Azure IoT Hub.](how-to-authenticate-downstream-device.md)

#### <a name="option-1-iot-hub-portal"></a>Možnost 1: Portál centra IoT Hub

Při vytváření nového zařízení můžete deklarovat vztah nadřazený-podřízený. Nebo pro stávající zařízení můžete deklarovat vztah ze stránky podrobností o zařízení nadřazeného zařízení IoT Edge nebo podřízeného zařízení IoT.

   ![Správa podřízených zařízení ze stránky podrobností o zařízení IoT Edge](./media/offline-capabilities/manage-child-devices.png)

#### <a name="option-2-use-the-az-command-line-tool"></a>Možnost 2: `az` Použití nástroje příkazového řádku

Pomocí [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) s [rozšířením IoT](https://github.com/azure/azure-iot-cli-extension) (v0.7.0 nebo novější) můžete spravovat nadřazené podřízené vztahy s podpříkazy [identity zařízení.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) Následující příklad používá dotaz k přiřazení všech zařízení než IoT Edge v rozbočovači jako podřízených zařízení zařízení IoT Edge.

```azurecli
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name
```

Dotaz můžete upravit [a](../iot-hub/iot-hub-devguide-query-language.md) vybrat jinou podmnožinu zařízení. Příkaz může trvat několik sekund, pokud zadáte velkou sadu zařízení.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Možnost 3: Použití sady SDK služby IoT Hub

Nakonec můžete spravovat nadřazené podřízené vztahy programově pomocí jazyka C#, Java nebo Node.js IoT Hub Service SDK. Tady je [příklad přiřazení podřízeného zařízení](https://aka.ms/set-child-iot-device-c-sharp) pomocí sady C# SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Nastavení nadřazeného zařízení jako brány

Vztah nadřazený/podřízený si můžete myslet jako transparentní bránu, kde podřízené zařízení má svou vlastní identitu v centru IoT Hub, ale komunikuje prostřednictvím cloudu prostřednictvím svého nadřazeného. Pro zabezpečenou komunikaci musí být podřízené zařízení schopno ověřit, zda nadřazené zařízení pochází z důvěryhodného zdroje. V opačném případě by třetí strany mohly nastavit škodlivá zařízení, která by se vydávala za rodiče a zachytila komunikaci.

Jeden způsob, jak vytvořit tento vztah důvěryhodnosti, je podrobně popsán v následujících článcích:

* [Konfigurace zařízení IoT Edge tak, aby fungovalo jako transparentní brána](how-to-create-transparent-gateway.md)
* [Připojení podřízeného (podřízeného) zařízení k bráně Azure IoT Edge](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Určení serverů DNS

Chcete-li zvýšit odolnost, důrazně doporučujeme zadat adresy serverů DNS používané ve vašem prostředí. Chcete-li nastavit server DNS pro IoT Edge, podívejte se na rozlišení [modulu Edge Agent neustále hlásí "prázdný konfigurační soubor" a žádné moduly spustit na zařízení](troubleshoot.md#edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device) v článku řešení potíží.

## <a name="optional-offline-settings"></a>Volitelná nastavení offline

Pokud vaše zařízení přejdou do režimu offline, nadřazené zařízení IoT Edge ukládá všechny zprávy mezi zařízeními a cloudem, dokud se připojení znovu nenapraví. Modul centra IoT Edge spravuje ukládání a předávání offline zpráv. U zařízení, která mohou přejít do režimu offline po delší dobu, optimalizujte výkon konfigurací dvou nastavení rozbočovačů IoT Edge.

Nejprve zvyšte dobu nastavení živého provozu, aby centrum IoT Edge uchovávaly zprávy dostatečně dlouho na to, aby se vaše zařízení mohlo znovu připojit. Potom přidejte další místo na disku pro ukládání zpráv.

### <a name="time-to-live"></a>Hodnota TTL (Time to Live)

Doba nastavení live je doba (v sekundách), po kterou může zpráva čekat na doručení před vypršením její platnosti. Výchozí hodnota je 7200 sekund (dvě hodiny). Maximální hodnota je omezena pouze maximální hodnotou celé proměnné, která je kolem 2 miliard.

Toto nastavení je požadovanou vlastností centra IoT Edge, která je uložena v dvojčeti modulu. Můžete ji nakonfigurovat na webu Azure Portal nebo přímo v manifestu nasazení.

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

### <a name="host-storage-for-system-modules"></a>Hostitelské úložiště pro systémové moduly

Zprávy a informace o stavu modulu jsou ve výchozím nastavení uloženy v místním kontejnerovém systému centra IoT Edge. Pro zvýšení spolehlivosti, zejména při práci v offline, můžete také vyhradit úložiště na hostitelském zařízení IoT Edge. Další informace naleznete v [tématu Poskytnutí přístupu modulům k místnímu úložišti zařízení.](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o nastavení transparentní brány pro připojení nadřazených a podřízených zařízení:

* [Konfigurace zařízení IoT Edge tak, aby fungovalo jako transparentní brána](how-to-create-transparent-gateway.md)
* [Ověření podřízeného zařízení pro Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Připojení podřízeného zařízení k bráně služby Azure IoT Edge](how-to-connect-downstream-device.md)
