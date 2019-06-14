---
title: PROVOZUJTE zařízení offline – Azure IoT Edge | Dokumentace Microsoftu
description: Zjistěte, jak moduly a zařízení IoT Edge může fungovat i bez připojení k Internetu pro dlouhou dobu a jak IoT Edge můžete povolit běžná zařízení IoT příliš pracovat offline.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4f3e5c1566271573b43e24a1749b42daa7530555
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051952"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Seznamte se s rozšířenou offline funkcí pro zařízení, moduly a podřízená zařízení IoT Edge

Azure IoT Edge podporuje rozšířené offline operace s vašimi zařízeními IoT Edge a příliš umožňuje offline operace na podřízené zařízení IoT Edge. Tak dlouho, dokud zařízení IoT Edge došlo jednu příležitost k připojení ke službě IoT Hub a všechny podřízené zařízení můžete nadále funkce s přerušovaným nebo žádné připojení k Internetu. 


## <a name="how-it-works"></a>Jak to funguje

Když se zařízení IoT Edge dostane do offline režimu, Centrum IoT Edge přebírá tři role. Nejprve ukládají se všechny zprávy, které přejde nadřazený a uloží je, dokud se zařízení znovu připojí. Za druhé funguje jménem služby IoT Hub k ověřování moduly a podřízená zařízení může i nadále fungovat. Třetí umožňuje komunikaci mezi zařízeními podřízený, které by normálně přejít prostřednictvím služby IoT Hub. 

Následující příklad ukazuje, jak funguje scénáři IoT Edge v režimu offline:

1. **Konfigurace zařízení**

   Zařízení IoT Edge mají automaticky offline Funkce povolené. Rozšíření této funkce s jinými zařízeními IoT, musíte deklarovat vztah nadřízenosti a podřízenosti mezi zařízeními ve službě IoT Hub. Potom nakonfigurujte zařízení podřízené důvěřovat jejich přiřazené nadřazené zařízení a směrování komunikace typu zařízení cloud prostřednictvím nadřazené jako brána. 

2. **Synchronizace pomocí služby IoT Hub**

   Alespoň jednou po instalaci modulu runtime IoT Edge, zařízení IoT Edge musí být online pro synchronizaci se službou IoT Hub. V této synchronizace zařízení IoT Edge načte podrobnosti o všech podřízených zařízení přiřazených k němu. Zařízení IoT Edge zároveň se bezpečně aktualizuje místní mezipaměti, povolení offline operace a načte nastavení pro místní ukládání telemetrických zpráv. 

3. **Přejít do režimu offline**

   Při odpojení ze služby IoT Hub, můžete zařízení IoT Edge, jeho nasazené moduly a všechny podřízené objekty zařízení IoT fungovat po neomezenou dobu. Moduly a podřízená zařízení můžete spustit a restartovat díky ověřování pomocí centra IoT Edge při offline. Ukládají se místně telemetrie upstream vázán ke službě IoT Hub. Komunikace mezi moduly nebo mezi zařízeními IoT podřízené zachovaný prostřednictvím přímé metody nebo zprávy. 

4. **Znovu připojit a znovu synchronizujte službou IoT Hub**

   Po obnovení připojení pomocí služby IoT Hub znovu synchronizuje zařízení IoT Edge. Místně uložené zprávy doručovaly ve stejném pořadí, ve kterém byly uloženy. Rozdíly mezi požadované a ohlášené vlastnosti moduly a zařízeními jsou sloučeny. Zařízení IoT Edge aktualizuje všechny změny na svou sadu zařízení IoT přiřazené podřízené.

## <a name="restrictions-and-limits"></a>Omezení a omezení

Rozšířené možnosti offline popsaných v tomto článku jsou dostupné v [IoT Edge verze 1.0.4 nebo vyšší](https://github.com/Azure/azure-iotedge/releases). Starší verze mají podmnožinu offline funkcí. Existující hraničních zařízeních IoT zařízení, která nemají rozšířené možnosti offline nelze upgradovat tak, že změníte verzi modulu runtime, ale je potřeba překonfigurovat tak s novou identitu zařízení IoT Edge k získání těchto funkcí. 

Prodloužená odborná pomoc offline je k dispozici ve všech oblastech, kde je k dispozici, IoT Hub **s výjimkou** USA – východ.

Pouze zařízení IoT Edge se dá přidat jako podřízenou zařízení. 

Zařízení IoT Edge a jejich zařízení přiřazené podřízené, můžou fungovat po synchronizaci počáteční, jednorázové po neomezenou dobu offline. Úložiště zpráv, které však závisí na time to live (TTL) nastavení a dostupné místo na disku pro ukládání zpráv. 

## <a name="set-up-parent-and-child-devices"></a>Nastavení nadřazených a podřízených zařízení

Pro zařízení IoT Edge rozšířit možnosti rozšířené offline do zařízení IoT podřízené budete muset provést dva kroky. Nejprve deklarujte hierarchické vztahy na webu Azure Portal. Za druhé vytvoření vztahu důvěryhodnosti mezi zařízením nadřazené a podřízené zařízení a potom konfiguraci komunikace typu zařízení cloud a absolvovat nadřazené jako brána. 

### <a name="assign-child-devices"></a>Přiřazení zařízení podřízené

Podřízená zařízení může být jakékoli zařízení IoT Edge zaregistrované u stejné služby IoT Hub. Nadřazené zařízení může mít více podřízených zařízení, ale podřízené zařízení má pouze jeden nadřazený prvek. Existují tři možnosti, jak nastavit podřízené zařízení do hraničního zařízení: prostřednictvím portálu Azure pomocí Azure CLI, nebo pomocí sady SDK služby IoT Hub. 

Následující části obsahují příklady, jak je možné deklarovat vztah nadřízenosti/ve službě IoT Hub pro existující zařízení IoT. Pokud vytváříte nových identit zařízení pro vaše dítě zařízení, přečtěte si téma [ověření podřízené zařízení do služby Azure IoT Hub](how-to-authenticate-downstream-device.md) Další informace.

#### <a name="option-1-iot-hub-portal"></a>Option 1: IoT Hub Portal

Při vytváření nového zařízení je možné deklarovat vztah nadřízenosti a podřízenosti. Nebo pro existující zařízení, je možné deklarovat vztah vychází ze stránky podrobností zařízení buď nadřazené zařízení IoT Edge nebo podřízené zařízení IoT. 

   ![Správa podřízených zařízení ze stránky detaily zařízení IoT Edge](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>Option 2: Použití `az` nástroj příkazového řádku

Použití [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) s [rozšíření IoT](https://github.com/azure/azure-iot-cli-extension) (v0.7.0 nebo novější), můžete spravovat nadřazené vztahy podřízenosti a nadřízenosti s [identitu zařízení](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) dílčí příkazy. Následující příklad používá dotaz k přiřazení všech zařízení IoT Edge v centru tak, aby se podřízené zařízení zařízení IoT Edge. 

```shell
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

Můžete upravit [dotazu](../iot-hub/iot-hub-devguide-query-language.md) vybrat podmnožinu různých zařízení. Příkaz může trvat několik sekund, pokud zadáte velké sady zařízení.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Možnost 3: Použití sady SDK služby IoT Hub 

Nakonec můžete spravovat nadřazené vztahy podřízenosti a nadřízenosti prostřednictvím kódu programu pomocí C#, Java nebo Node.js sady SDK služby IoT Hub. Tady je [příklad přiřazení podřízené zařízení](https://aka.ms/set-child-iot-device-c-sharp) pomocí C# SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Nastavení nadřazené zařízení jako brána

Si můžete představit jako transparentní brána, kde má svou vlastní identitu ve službě IoT Hub podřízené zařízení, ale komunikuje přes cloud prostřednictvím svého nadřazeného objektu v relaci nadřazený/podřízený. Pro zabezpečenou komunikaci podřízené zařízení musí být schopný ověřit, že zařízení nadřazené pochází z důvěryhodného zdroje. V opačném případě třetích stran, může nastavit škodlivý zařízení k předstírání identity nadřazené položky a zachycení komunikace. 

Jedním ze způsobů vytvoření tohoto vztahu důvěryhodnosti je podrobně popsány v následujících článcích: 
* [Konfigurace zařízení tak, aby fungoval jako transparentní brána IoT Edge](how-to-create-transparent-gateway.md)
* [Připojte zařízení za příjem dat (podřízený) brány Azure IoT Edge](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Určit servery DNS 

Pokud chcete zlepšit odolnost, důrazně doporučujeme zadejte adresy serverů DNS ve svém prostředí. Zobrazit dvě možnosti, jak [nastavení serveru DNS v článku Poradce při potížích](troubleshoot.md#resolution-7).

## <a name="optional-offline-settings"></a>Volitelná nastavení

Pokud vaše zařízení přejdou do režimu offline, nadřazené zařízení IoT Edge ukládá všechny zprávy typu zařízení cloud, dokud se obnoví připojení. Modul IoT Edge hub spravuje úložiště a předávání zpráv v režimu offline. U zařízení, která může přejít do režimu offline delší časová období Optimalizujte výkon tím, že nakonfigurujete dvě nastavení centra IoT Edge. 

Nejprve zvýšit time to live nastavení tak, aby Centrum IoT Edge bude udržovat zprávy dostatečně dlouhé pro zařízení znovu připojit. Pak přidejte další místo na disku pro úložiště zpráv. 

### <a name="time-to-live"></a>Hodnota TTL (Time to Live)

Time to live nastavení je množství času (v sekundách), který zprávu počkat, který bude doručen před vypršením platnosti. Výchozí hodnota je 7200 sekund (dva hodin). Maximální hodnota je omezen pouze maximální hodnotu proměnnou celého čísla, což je přibližně 2 miliardy. 

Toto nastavení je požadovaná vlastnost centra IoT Edge, která je uložena ve dvojčeti modulu. Můžete ho nakonfigurovat na portálu Azure nebo přímo v manifestu nasazení. 

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

Zprávy jsou uloženy ve výchozím nastavení v systému souborů kontejneru hraničních zařízeních IoT hub. Pokud toto množství úložiště není dostatečná pro vaše potřeby v režimu offline, který vyhradíte místní úložiště na zařízení IoT Edge. Vytvořte proměnnou prostředí pro Centrum IoT Edge, který odkazuje na složku v kontejneru úložiště. Pak použijte možnosti vytvořit pro přiřazení této složky úložiště do složky na hostitelském počítači. 

Můžete nakonfigurovat proměnné prostředí a možnosti vytvořit pro modul IoT Edge hub na webu Azure Portal v **konfigurovat rozšířená nastavení modulu Runtime Edge** oddílu. Nebo můžete vytvořit přímo v manifestu nasazení. 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": {
            "HostConfig": {
                "Binds": ["<HostStoragePath>:<ModuleStoragePath>"],
                "PortBindings": {
                    "8883/tcp": [{"HostPort":"8883"}],
                    "443/tcp": [{"HostPort":"443"}],
                    "5671/tcp": [{"HostPort":"5671"}]
                }
            }
        }
    },
    "env": {
        "storageFolder": {
            "value": "<ModuleStoragePath>"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Nahraďte `<HostStoragePath>` a `<ModuleStoragePath>` cesta k úložišti cestu; hostitele i modul úložiště hostitele a modul musí být absolutní cesta. V možnosti vytvořit svázat dohromady cesty úložiště hostitele a modulu. Pak vytvořte proměnnou prostředí, která odkazuje na cestu úložiště modulu.  

Například `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` znamená, že adresář **/etc/iotedge/storage** na hostiteli systému je namapovaný na adresář **/iotedge/úložiště/** v kontejneru. Dalším příkladem pro systémy Windows, nebo `"Binds":["C:\\temp:C:\\contemp"]` znamená, že adresář **C:\\temp** na hostiteli systému je namapovaný na adresář **C:\\contemp** v kontejneru. 

Můžete také najít další podrobnosti o vytvoření možnosti z [docker dokumentace](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>Další postup

Další informace o tom, jak nastavit transparentní bránu pro připojení k nadřazené a podřízené zařízení: 

* [Konfigurace zařízení tak, aby fungoval jako transparentní brána IoT Edge](how-to-create-transparent-gateway.md)
* [Ověření podřízené zařízení do služby Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Připojte zařízení za příjem dat k bráně Azure IoT Edge](how-to-connect-downstream-device.md)
