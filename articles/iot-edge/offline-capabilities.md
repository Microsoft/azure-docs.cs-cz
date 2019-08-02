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
ms.openlocfilehash: 4a46128d3b0e77ff7921e1f4875c318a95309769
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598604"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Vysvětlení rozšířených funkcí offline pro IoT Edge zařízení, moduly a podřízená zařízení

Azure IoT Edge podporuje taky rozšířené offline operace na zařízeních IoT Edge a umožňuje také offline operace na podřízených zařízeních, která nejsou IoT Edge. Tak dlouho, dokud zařízení IoT Edge došlo jednu příležitost k připojení ke službě IoT Hub a všechny podřízené zařízení můžete nadále funkce s přerušovaným nebo žádné připojení k Internetu. 


## <a name="how-it-works"></a>Jak to funguje

Když IoT Edge zařízení přejde do offline režimu, bude Centrum IoT Edge trvat tři role. Nejprve ukládají se všechny zprávy, které přejde nadřazený a uloží je, dokud se zařízení znovu připojí. Za druhé funguje jménem služby IoT Hub k ověřování moduly a podřízená zařízení může i nadále fungovat. Třetí umožňuje komunikaci mezi zařízeními podřízený, které by normálně přejít prostřednictvím služby IoT Hub. 

Následující příklad ukazuje, jak funguje scénáři IoT Edge v režimu offline:

1. **Konfigurace zařízení**

   Zařízení IoT Edge mají automaticky offline Funkce povolené. Rozšíření této funkce s jinými zařízeními IoT, musíte deklarovat vztah nadřízenosti a podřízenosti mezi zařízeními ve službě IoT Hub. Potom nakonfigurujete podřízená zařízení tak, aby důvěřovala přiřazeným nadřazeným zařízením, a směrovat komunikaci typu zařízení-Cloud přes nadřazenou bránu jako bránu. 

2. **Synchronizovat s IoT Hub**

   Alespoň jednou po instalaci modulu runtime IoT Edge, zařízení IoT Edge musí být online pro synchronizaci se službou IoT Hub. V této synchronizace zařízení IoT Edge načte podrobnosti o všech podřízených zařízení přiřazených k němu. Zařízení IoT Edge zároveň se bezpečně aktualizuje místní mezipaměti, povolení offline operace a načte nastavení pro místní ukládání telemetrických zpráv. 

3. **Přejít offline**

   Při odpojení ze služby IoT Hub, můžete zařízení IoT Edge, jeho nasazené moduly a všechny podřízené objekty zařízení IoT fungovat po neomezenou dobu. Moduly a podřízená zařízení se můžou spustit a restartovat ověřováním pomocí centra IoT Edge v režimu offline. Ukládají se místně telemetrie upstream vázán ke službě IoT Hub. Komunikace mezi moduly nebo mezi zařízeními IoT podřízené zachovaný prostřednictvím přímé metody nebo zprávy. 

4. **Opětovné připojení a opětovná synchronizace s IoT Hub**

   Po obnovení připojení pomocí služby IoT Hub znovu synchronizuje zařízení IoT Edge. Místně uložené zprávy doručovaly ve stejném pořadí, ve kterém byly uloženy. Rozdíly mezi požadované a ohlášené vlastnosti moduly a zařízeními jsou sloučeny. Zařízení IoT Edge aktualizuje všechny změny na svou sadu zařízení IoT přiřazené podřízené.

## <a name="restrictions-and-limits"></a>Omezení a omezení

Rozšířené funkce offline popsané v tomto článku jsou k dispozici ve [verzi IoT Edge 1.0.7 nebo novější](https://github.com/Azure/azure-iotedge/releases). Starší verze mají podmnožinu offline funkcí. Existující hraničních zařízeních IoT zařízení, která nemají rozšířené možnosti offline nelze upgradovat tak, že změníte verzi modulu runtime, ale je potřeba překonfigurovat tak s novou identitu zařízení IoT Edge k získání těchto funkcí. 

Prodloužená odborná pomoc offline je k dispozici ve všech oblastech, kde je k dispozici, IoT Hub **s výjimkou** USA – východ.

Jako podřízená zařízení se dají přidat jenom zařízení, která nejsou IoT Edge. 

Zařízení IoT Edge a jejich zařízení přiřazené podřízené, můžou fungovat po synchronizaci počáteční, jednorázové po neomezenou dobu offline. Úložiště zpráv, které však závisí na time to live (TTL) nastavení a dostupné místo na disku pro ukládání zpráv. 

## <a name="set-up-parent-and-child-devices"></a>Nastavení nadřazených a podřízených zařízení

Aby zařízení IoT Edge rozšířilo rozšířené možnosti offline na podřízená zařízení IoT, je nutné provést dva kroky. Nejprve deklarujte vztahy nadřazenosti a podřízenosti v Azure Portal. Za druhé vytvořte vztah důvěryhodnosti mezi nadřazeným zařízením a všemi podřízenými zařízeními a pak nakonfigurujte komunikaci typu zařízení-Cloud tak, aby procházela přes nadřazenou bránu jako brána. 

### <a name="assign-child-devices"></a>Přiřazení zařízení podřízené

Podřízená zařízení můžou být všechna zařízení, která nejsou IoT Edge zaregistrovaná na stejný IoT Hub. Nadřazená zařízení můžou mít několik podřízených zařízení, ale v podřízeném zařízení máte jenom jednu nadřazenou položku. Existují tři možnosti, jak nastavit podřízená zařízení na hraniční zařízení: prostřednictvím Azure Portal, pomocí rozhraní příkazového řádku Azure nebo pomocí sady SDK služby IoT Hub. 

V následujících částech najdete příklady, jak deklarovat vztah nadřazenosti/podřízenosti v IoT Hub pro existující zařízení IoT. Pokud vytváříte nové identity zařízení pro vaše podřízená zařízení, přečtěte si téma [ověření zařízení pro příjem dat v Azure IoT Hub](how-to-authenticate-downstream-device.md) Další informace.

#### <a name="option-1-iot-hub-portal"></a>Možnost 1: Portál IoT Hub

Při vytváření nového zařízení můžete deklarovat relaci typu nadřazený-podřízený. Nebo u stávajících zařízení můžete deklarovat relaci ze stránky s podrobnostmi o zařízení nadřazeného IoT Edge zařízení nebo podřízeného zařízení IoT. 

   ![Správa podřízených zařízení ze stránky detaily zařízení IoT Edge](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>Možnost 2: Použití nástroje `az` příkazového řádku

Pomocí [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) s [rozšířením IoT](https://github.com/azure/azure-iot-cli-extension) (v 0.7.0 nebo novějším) můžete spravovat nadřazené podřízené relace pomocí dílčích příkazů [Device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) . Následující příklad používá dotaz k přiřazení všech zařízení, která nejsou IoT Edge v centru, aby byla podřízená zařízení IoT Edge zařízení. 

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

[Dotaz](../iot-hub/iot-hub-devguide-query-language.md) můžete upravit tak, aby vybral jinou podmnožinu zařízení. Pokud zadáte velkou sadu zařízení, může příkaz trvat několik sekund.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Možnost 3: Použití sady SDK služby IoT Hub 

Nakonec můžete spravovat vztahy nadřazených podřízených objektů programově pomocí C#sady SDK služby IoT Hub, Java nebo Node. js. Tady je [příklad přiřazení podřízeného zařízení](https://aka.ms/set-child-iot-device-c-sharp) pomocí C# sady SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Nastavení nadřazeného zařízení jako brány

Vztah nadřazený-podřízený můžete představit jako transparentní bránu, kde má podřízené zařízení svou vlastní identitu v IoT Hub, ale komunikuje přes Cloud přes svůj nadřazený objekt. Pro zabezpečenou komunikaci musí být podřízené zařízení schopné ověřit, jestli nadřazené zařízení pochází z důvěryhodného zdroje. V opačném případě mohou třetí strany nastavit škodlivá zařízení k zosobnění rodičů a zachycení komunikace. 

Jeden ze způsobů, jak vytvořit tento vztah důvěryhodnosti, je podrobně popsaný v následujících článcích: 
* [Konfigurace zařízení tak, aby fungoval jako transparentní brána IoT Edge](how-to-create-transparent-gateway.md)
* [Připojení zařízení pro příjem dat (podřízeného) k bráně Azure IoT Edge](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Zadat servery DNS 

Pro zvýšení odolnosti se důrazně doporučuje zadat adresy serverů DNS používané ve vašem prostředí. Pokud chcete [nastavit server DNS v článku věnovaném řešení potíží](troubleshoot.md#resolution-7), podívejte se na dvě možnosti.

## <a name="optional-offline-settings"></a>Volitelná nastavení

Pokud zařízení přejde do režimu offline, IoT Edge nadřazené zařízení uloží všechny zprávy ze zařízení do cloudu, dokud se připojení znovu nevytvoří. Modul IoT Edge hub spravuje ukládání a předávání offline zpráv. U zařízení, která můžou po dlouhou dobu přejít do režimu offline, Optimalizujte výkon konfigurací dvou IoT Edge centra. 

Nejdřív Zvyšte nastavení TTL (Time to Live), aby Centrum IoT Edge zanechalo dostatečně dlouhé zprávy, aby se vaše zařízení mohlo znovu připojit. Pak přidejte další místo na disku pro úložiště zpráv. 

### <a name="time-to-live"></a>Hodnota TTL (Time to Live)

Time to live nastavení je množství času (v sekundách), který zprávu počkat, který bude doručen před vypršením platnosti. Výchozí hodnota je 7200 sekund (dva hodin). Maximální hodnota je omezena pouze maximální hodnotou proměnné typu Integer, která je okolo 2 000 000 000. 

Toto nastavení je požadovaná vlastnost centra IoT Edge, která je uložená v modulu s dvojitou platností. Můžete ji nakonfigurovat v Azure Portal nebo přímo v manifestu nasazení. 

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

Zprávy jsou ve výchozím nastavení uloženy v systému souborů kontejnerů centra IoT Edge. Pokud toto množství úložiště není dostatečná pro vaše potřeby v režimu offline, který vyhradíte místní úložiště na zařízení IoT Edge. Vytvořte proměnnou prostředí pro IoT Edge centrum, která odkazuje na složku úložiště v kontejneru. Pak použijte možnosti vytvořit pro přiřazení této složky úložiště do složky na hostitelském počítači. 

Můžete nakonfigurovat proměnné prostředí a možnosti vytváření pro modul IoT Edge hub v Azure Portal v části **Konfigurace pokročilého nastavení modulu runtime** . Nebo můžete vytvořit přímo v manifestu nasazení. 

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

Nahraďte `<HostStoragePath>` a `<ModuleStoragePath>` cesta k úložišti cestu; hostitele i modul úložiště hostitele a modul musí být absolutní cesta. V možnostech vytvoření navažte cesty úložiště hostitele a modulu společně. Pak vytvořte proměnnou prostředí, která odkazuje na cestu úložiště modulu.  

Například znamená, `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` že adresář **/etc/iotedge/Storage** v hostitelském systému je namapován na adresář **/iotedge/Storage/** na kontejneru. Nebo jiný příklad pro systémy Windows znamená `"Binds":["C:\\temp:C:\\contemp"]` , že adresář **c:\\Temp** v hostitelském systému je namapovaný na adresář **c:\\pro dočasné** umístění na kontejneru. 

Můžete také najít další podrobnosti o možnostech vytváření z [Docker docs](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>Další postup

Přečtěte si další informace o tom, jak nastavit transparentní bránu pro připojení nadřazených a podřízených zařízení: 

* [Konfigurace zařízení tak, aby fungoval jako transparentní brána IoT Edge](how-to-create-transparent-gateway.md)
* [Ověření zařízení pro příjem dat do Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Připojení zařízení pro příjem dat k bráně Azure IoT Edge](how-to-connect-downstream-device.md)
