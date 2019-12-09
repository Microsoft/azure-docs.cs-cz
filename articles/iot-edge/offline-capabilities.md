---
title: PROVOZUJTE zařízení offline – Azure IoT Edge | Dokumentace Microsoftu
description: Zjistěte, jak moduly a zařízení IoT Edge může fungovat i bez připojení k Internetu pro dlouhou dobu a jak IoT Edge můžete povolit běžná zařízení IoT příliš pracovat offline.
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ba64dcdadc5fa670c4502a7d8d92cb35e3b0cacd
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74924851"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Vysvětlení rozšířených funkcí offline pro IoT Edge zařízení, moduly a podřízená zařízení

Azure IoT Edge podporuje taky rozšířené offline operace na zařízeních IoT Edge a umožňuje také offline operace na podřízených zařízeních, která nejsou IoT Edge. Pokud má IoT Edge zařízení jednu příležitost připojit se k IoT Hub, může toto zařízení a kterákoli podřízená zařízení fungovat i s přerušovaným připojením nebo bez připojení k Internetu.

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

   Po obnovení připojení pomocí služby IoT Hub znovu synchronizuje zařízení IoT Edge. Místně uložené zprávy se doručí IoT Hub hned, ale závisí na rychlosti připojení, IoT Hub latenci a souvisejících faktorech. Jsou dodávány ve stejném pořadí, ve kterém byly uloženy.

   Rozdíly mezi požadované a ohlášené vlastnosti moduly a zařízeními jsou sloučeny. Zařízení IoT Edge aktualizuje všechny změny na svou sadu zařízení IoT přiřazené podřízené.

## <a name="restrictions-and-limits"></a>Omezení a omezení

Rozšířené funkce offline popsané v tomto článku jsou k dispozici ve [verzi IoT Edge 1.0.7 nebo novější](https://github.com/Azure/azure-iotedge/releases). Starší verze mají podmnožinu offline funkcí. Existující hraničních zařízeních IoT zařízení, která nemají rozšířené možnosti offline nelze upgradovat tak, že změníte verzi modulu runtime, ale je potřeba překonfigurovat tak s novou identitu zařízení IoT Edge k získání těchto funkcí. 

Prodloužená odborná pomoc offline je k dispozici ve všech oblastech, kde je k dispozici, IoT Hub **s výjimkou** USA – východ.

Jako podřízená zařízení se dají přidat jenom zařízení, která nejsou IoT Edge. 

IoT Edge zařízení a jejich přiřazená podřízená zařízení můžou po počáteční a jednorázové synchronizaci fungovat po neomezenou dobu offline. Ukládání zpráv ale závisí na nastavení TTL (Time to Live) a na dostupném místě na disku pro uložení zpráv. 

## <a name="set-up-parent-and-child-devices"></a>Nastavení nadřazených a podřízených zařízení

Aby zařízení IoT Edge rozšířilo rozšířené možnosti offline na podřízená zařízení IoT, je nutné provést dva kroky. Nejprve deklarujte vztahy nadřazenosti a podřízenosti v Azure Portal. Za druhé vytvořte vztah důvěryhodnosti mezi nadřazeným zařízením a všemi podřízenými zařízeními a pak nakonfigurujte komunikaci typu zařízení-Cloud tak, aby procházela přes nadřazenou bránu jako brána. 

### <a name="assign-child-devices"></a>Přiřazení zařízení podřízené

Podřízená zařízení můžou být všechna zařízení, která nejsou IoT Edge zaregistrovaná na stejný IoT Hub. Nadřazená zařízení můžou mít několik podřízených zařízení, ale v podřízeném zařízení máte jenom jednu nadřazenou položku. Existují tři možnosti, jak nastavit podřízená zařízení na hraniční zařízení: prostřednictvím Azure Portal, pomocí rozhraní příkazového řádku Azure nebo pomocí sady SDK služby IoT Hub. 

V následujících částech najdete příklady, jak deklarovat vztah nadřazenosti/podřízenosti v IoT Hub pro existující zařízení IoT. Pokud vytváříte nové identity zařízení pro vaše podřízená zařízení, přečtěte si téma [ověření zařízení pro příjem dat v Azure IoT Hub](how-to-authenticate-downstream-device.md) Další informace.

#### <a name="option-1-iot-hub-portal"></a>Možnost 1: IoT Hub Portal

Při vytváření nového zařízení můžete deklarovat relaci typu nadřazený-podřízený. Nebo u stávajících zařízení můžete deklarovat relaci ze stránky s podrobnostmi o zařízení nadřazeného IoT Edge zařízení nebo podřízeného zařízení IoT. 

   ![Správa podřízených zařízení ze stránky detaily zařízení IoT Edge](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>Možnost 2: použití nástroje příkazového řádku `az`

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

#### <a name="option-3-use-iot-hub-service-sdk"></a>Možnost 3: použití sady SDK služby IoT Hub 

Nakonec můžete spravovat vztahy nadřazených podřízených objektů programově pomocí C#sady SDK služby IoT Hub, Java nebo Node. js. Tady je [příklad přiřazení podřízeného zařízení](https://aka.ms/set-child-iot-device-c-sharp) pomocí C# sady SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Nastavení nadřazeného zařízení jako brány

Vztah nadřazený-podřízený můžete představit jako transparentní bránu, kde má podřízené zařízení svou vlastní identitu v IoT Hub, ale komunikuje přes Cloud přes svůj nadřazený objekt. Pro zabezpečenou komunikaci musí být podřízené zařízení schopné ověřit, jestli nadřazené zařízení pochází z důvěryhodného zdroje. V opačném případě mohou třetí strany nastavit škodlivá zařízení k zosobnění rodičů a zachycení komunikace. 

Jeden ze způsobů, jak vytvořit tento vztah důvěryhodnosti, je podrobně popsaný v následujících článcích: 
* [Konfigurace zařízení IoT Edge tak, aby fungovalo jako transparentní brána](how-to-create-transparent-gateway.md)
* [Připojení zařízení pro příjem dat (podřízeného) k bráně Azure IoT Edge](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Zadat servery DNS 

Pro zvýšení odolnosti se důrazně doporučuje zadat adresy serverů DNS používané ve vašem prostředí. Pokud chcete nastavit server DNS pro IoT Edge, přečtěte si téma řešení for [Edge agent průběžně hlásí prázdný konfigurační soubor a](troubleshoot.md#edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device) v článku věnovaném řešení potíží se nespouštějí žádné moduly.

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

### <a name="host-storage-for-system-modules"></a>Úložiště hostitele pro systémové moduly

Zprávy a informace o stavu modulů se ve výchozím nastavení ukládají do místního systému souborů výchozího kontejneru IoT Edgeového centra. Pro lepší spolehlivost, zejména při provozu v režimu offline, můžete také vyhradit úložiště v hostitelském IoT Edgem zařízení. Další informace najdete v tématu [udělení přístupu k místním úložištím modulů v zařízení](how-to-access-host-storage-from-module.md) .

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak nastavit transparentní bránu pro připojení nadřazených a podřízených zařízení: 

* [Konfigurace zařízení IoT Edge tak, aby fungovalo jako transparentní brána](how-to-create-transparent-gateway.md)
* [Ověření podřízeného zařízení ve službě Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Připojení podřízeného zařízení k bráně služby Azure IoT Edge](how-to-connect-downstream-device.md)
