---
title: Řešení potíží s připojením zařízení k Azure IoT Central | Microsoft Docs
description: Řešení potíží, proč se data ze zařízení nezobrazuje v IoT Central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2020
ms.topic: troubleshooting
ms.service: iot-central
ms.custom: device-developer, devx-track-azurecli
ms.openlocfilehash: 2bbf400840c968587de3a0a0951d28c7c35b210f
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94990886"
---
# <a name="troubleshoot-why-data-from-your-devices-isnt-showing-up-in-azure-iot-central"></a>Řešení potíží v případě nezobrazování dat ze zařízení v Azure IoT Central

Tento dokument pomáhá vývojářům zařízení zjistit, proč se data, která jejich zařízení odesílají do IoT Central, nemusí zobrazit v aplikaci.

Existují dvě hlavní oblasti, které je potřeba prozkoumat:

- Problémy s připojením zařízení
  - Problémy s ověřováním, jako je třeba zařízení, má neplatné přihlašovací údaje.
  - Problémy s připojením k síti
  - Zařízení není schválené nebo blokované.
- Problémy s tvarem datové části zařízení

Tato příručka pro odstraňování potíží se zaměřuje na problémy s připojením zařízení a problémy s obrazcem datových částí zařízení.

## <a name="device-connectivity-issues"></a>Problémy s připojením zařízení

Tato část vám pomůže určit, jestli se vaše data doIoT Central.

Pokud jste to ještě neudělali, nainstalujte `az cli` Nástroj a `azure-iot` rozšíření.

Informace o tom, jak nainstalovat `az cli` , najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Chcete-li [nainstalovat](/cli/azure/azure-cli-reference-for-IoT?view=azure-cli-latest#extension-reference-installation) `azure-iot` rozšíření, spusťte následující příkaz:

```cmd/bash
az extension add --name azure-iot
```

> [!NOTE]
> Může se zobrazit výzva k instalaci `uamqp` knihovny při prvním spuštění příkazu rozšíření.

Po instalaci `azure-iot` rozšíření spusťte zařízení, abyste viděli, jestli zprávy, které odesíláte, jsou způsobem IoT Central.

Pomocí následujících příkazů se přihlaste k předplatnému, kde máte aplikaci IoT Central:

```cmd/bash
az login
az set account --subscription <your-subscription-id>
```

K monitorování telemetrie, kterou vaše zařízení odesílá, použijte následující příkaz:

```cmd/bash
az iot central diagnostics monitor-events --app-id <app-id> --device-id <device-name>
```

Pokud se zařízení úspěšně připojilo k IoT Central, zobrazí se výstup podobný následujícímu:

```cmd/bash
Monitoring telemetry.
Filtering on device: device-001
{
    "event": {
        "origin": "device-001",
        "module": "",
        "interface": "",
        "component": "",
        "payload": {
            "temp": 65.57910343679293,
            "humid": 36.16224660107426
        }
    }
}
```

Pokud chcete monitorovat aktualizace vlastností, které zařízení vyměňuje pomocí IoT Central, použijte následující příkaz Preview:

```cmd/bash
az iot central diagnostics monitor-properties --app-id <app-id> --device-id <device-name>
```

Pokud zařízení úspěšně odesílá aktualizace vlastností, zobrazí se výstup podobný následujícímu:

```cmd/bash
Changes in reported properties:
version : 32
{'state': 'true', 'name': {'value': {'value': 'Contoso'}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac
': 200}, 'brightness': {'value': {'value': 2}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac': 200}, 'p
rocessorArchitecture': 'ARM', 'swVersion': '1.0.0'}
```

Pokud se zobrazí data v terminálu, data se tak budou zobrazovat stejně jako aplikace IoT Central.

Pokud se nezobrazí žádná data po několika minutách, zkuste stisknout klávesu `Enter` nebo `return` na klávesnici, pokud je výstup zablokovaný.

Pokud stále nevidíte žádná data v terminálu, je pravděpodobný, že vaše zařízení má problémy s připojením k síti nebo neodesílá data správně IoT Central.

### <a name="check-the-provisioning-status-of-your-device"></a>Ověřte stav zřizování vašeho zařízení.

Pokud se data na monitoru nezobrazují, ověřte stav zřizování vašeho zařízení spuštěním následujícího příkazu:

```cmd/bash
az iot central device registration-info --app-id <app-id> --device-id <device-name>
```

Následující výstup ukazuje příklad zařízení, u kterého se zablokuje připojení:

```json
{
  "@device_id": "v22upeoqx6",
  "device_registration_info": {
    "device_status": "blocked",
    "display_name": "Environmental Sensor - v22upeoqx6",
    "id": "v22upeoqx6",
    "instance_of": "urn:krhsi_k0u:modelDefinition:w53jukkazs",
    "simulated": false
  },
  "dps_state": {
    "error": "Device is blocked from connecting to IoT Central application. Unblock the device in IoT Central and retry. Learn more:
https://aka.ms/iotcentral-docs-dps-SAS",
    "status": null
  }
}
```

| Stav zřizování zařízení | Popis | Možné zmírnění |
| - | - | - |
| Zřízené | Žádný okamžitě rozpoznatelný problém. | – |
| Registrované | Zařízení ještě není připojené k IoT Central. | V protokolech zařízení ověřte problémy s připojením. |
| Blokované | Zařízení je zablokované v připojení k IoT Central. | Zařízení je zablokované v připojení k aplikaci IoT Central. Odblokovat zařízení v IoT Central a zkuste to znovu. Další informace najdete v tématu [blokování zařízení](concepts-get-connected.md#device-status-values). |
| Neschválených | Zařízení není schváleno. | Zařízení není schváleno pro připojení k aplikaci IoT Central. Schvalte zařízení v IoT Central a zkuste to znovu. Další informace najdete v tématu [schválení zařízení](concepts-get-connected.md#device-registration) . |
| Nepřidružený | Zařízení není přidruženo k šabloně zařízení. | Přidružte zařízení k šabloně zařízení, aby IoT Central ví, jak data analyzovat. |

Přečtěte si další informace o [stavových kódech zařízení](concepts-get-connected.md#device-status-values).

### <a name="error-codes"></a>Kódy chyb

Pokud stále nemůžete diagnostikovat, proč se data v nástroji nezobrazuje `monitor-events` , je dalším krokem hledání kódů chyb hlášených vaším zařízením.

Spusťte na svém zařízení ladicí relaci nebo Shromážděte protokoly ze svého zařízení. Vyhledejte všechny chybové kódy, které zařízení hlásí.

V následujících tabulkách jsou uvedeny běžné kódy chyb a možné akce, které je potřeba zmírnit.

Pokud se vám zobrazují problémy související s vaším tokem ověřování:

| Kód chyby | Popis | Možné zmírnění |
| - | - | - |
| 400 | Tělo požadavku není platné. Nelze jej například analyzovat nebo objekt nelze ověřit. | Ujistěte se, že posíláte správné tělo žádosti v rámci toku ověření identity, nebo použijete sadu SDK pro zařízení. |
| 401 | Autorizační token se nedá ověřit. Například vypršela platnost nebo neplatí pro identifikátor URI žádosti. Tento kód chyby se také vrátí do zařízení jako součást toku ověření čipem TPM. | Ujistěte se, že má vaše zařízení správné přihlašovací údaje. |
| 404 | Instance služby Device Provisioning nebo prostředek, jako je registrace, neexistuje. | [Zasouborte lístek s zákaznickou podporou](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). |
| 412 | `ETag`V žádosti se neshoduje s `ETag` existujícím prostředkem, a to podle RFC7232. | [Zasouborte lístek s zákaznickou podporou](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). |
| 429 | Služba omezuje operace. Omezení pro konkrétní služby najdete v tématu [omezení IoT Hub Device Provisioning Service](../../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits). | Snižte frekvenci zpráv, rozdělte zodpovědnosti mezi více zařízení. |
| 500 | Došlo k vnitřní chybě. | Zadáte [lístek s zákaznickou podporou](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) , abyste mohli zjistit, jestli vám může pomoci. |

## <a name="payload-shape-issues"></a>Problémy s tvarem datové části

Když jste nastavili, že vaše zařízení odesílá data IoT Central, je dalším krokem, aby bylo zajištěno, že zařízení odesílá data v platném formátu.

Existují dvě hlavní kategorie běžných problémů, které způsobují, že se data zařízení nezobrazují IoT Central:

- Neshoda mezi šablonou zařízení a daty zařízení:
  - Neshoda v názvech, jako jsou překlepy nebo problémy s rozlišováním velkých a malých písmen.
  - Nemodelované vlastnosti, kde schéma není definováno v šabloně zařízení.
  - Neshoda schématu, jako je například typ definovaný v šabloně jako `boolean` , ale data jsou řetězce.
  - Stejný název telemetrie je definovaný ve více rozhraních, ale zařízení není IoT technologie Plug and Play kompatibilní.
- Datový tvar není platný JSON. Další informace najdete v tématu [telemetrie, vlastnosti a datové části příkazů](concepts-telemetry-properties-commands.md).

Chcete-li zjistit, na kterých kategoriích je váš problém, spusťte nejvhodnější příkaz pro váš scénář:

- K ověření telemetrie použijte příkaz Preview:

    ```cmd/bash
    az iot central diagnostics validate-messages --app-id <app-id> --device-id <device-name>
    ```

- Chcete-li ověřit aktualizace vlastností, použijte příkaz Preview

    ```cmd/bash
    az iot central diagnostics validate-properties --app-id <app-id> --device-id <device-name>
    ```

Při prvním spuštění příkazu se může zobrazit výzva k instalaci `uamqp` knihovny `validate` .

Následující výstup ukazuje ukázkovou zprávu o chybách a upozorněních z příkazu ověřit:

```cmd/bash
Validating telemetry.
Filtering on device: v22upeoqx6.
Exiting after 300 second(s), or 10 message(s) have been parsed (whichever happens first).
[WARNING] [DeviceId: v22upeoqx6] No encoding found. Expected encoding 'utf-8' to be present in message header.

[WARNING] [DeviceId: v22upeoqx6] Content type '' is not supported. Expected Content type is 'application/json'.

[ERROR] [DeviceId: v22upeoqx6] [TemplateId: urn:krhsi_k0u:modelDefinition:w53jukkazs] Datatype of field 'humid' does not match the da
tatype 'double'. Data '56'. All dates/times/datetimes/durations must be ISO 8601 compliant.
```

Pokud dáváte přednost použití grafického uživatelského rozhraní, použijte zobrazení IoT Central **nezpracovaná data** , abyste viděli, jestli není něco namodelované. Zobrazení **nezpracovaná data** nezjistí, jestli zařízení odesílá nesprávně vytvořený kód JSON.

:::image type="content" source="media/troubleshoot-connection/raw-data-view.png" alt-text="Snímek obrazovky se zobrazením nezpracovaných dat":::

Když zjistíte problém, možná budete muset aktualizovat firmware zařízení nebo vytvořit novou šablonu zařízení, která modeloval dříve nemodelovaná data.

Pokud se rozhodnete vytvořit novou šablonu, která správně modeluje data, migrujte zařízení z původní šablony do nové šablony. Další informace najdete v tématu [Správa zařízení ve vaší aplikaci Azure IoT Central](howto-manage-devices.md).

## <a name="next-steps"></a>Další kroky

Pokud potřebujete další informace, můžete se obrátit na odborníky na Azure na [webu MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/community/). Případně můžete zasouborovat [lístek podpory Azure](https://portal.azure.com/#create/Microsoft.Support).

Další informace najdete v tématu [Podpora a možnosti pomoci pro Azure IoT](../../iot-fundamentals/iot-support-help.md).
