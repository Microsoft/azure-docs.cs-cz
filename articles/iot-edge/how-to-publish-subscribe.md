---
title: Publikování a přihlášení k odběru pomocí Azure IoT Edge | Microsoft Docs
description: Použití zprostředkovatele IoT Edge MQTT pro publikování a odběr zpráv
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.reviewer: ebertra
ms.date: 11/09/2020
ms.topic: conceptual
ms.service: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 730680b0cb6e8a728ed3072419674346de649368
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200675"
---
# <a name="publish-and-subscribe-with-azure-iot-edge"></a>Publikování a přihlášení k odběru pomocí Azure IoT Edge

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

K publikování a odběru zpráv můžete použít zprostředkovatele Azure IoT Edge MQTT. V tomto článku se dozvíte, jak se připojit k tomuto zprostředkovateli, publikovat zprávy a přihlásit se k odběru zpráv pomocí uživatelsky definovaných témat a použít IoT Hub primitivních zpráv. Zprostředkovatel IoT Edge MQTT je integrovaný v centru IoT Edge. Další informace najdete v tématu [Možnosti zprostředkování centra IoT Edge](iot-edge-runtime.md).

> [!NOTE]
> Služba IoT Edge MQTT Broker je aktuálně ve verzi Public Preview.

## <a name="pre-requisites"></a>Požadavky

- Účet Azure s platným předplatným
- Rozhraní příkazového [řádku Azure](/cli/azure/) s `azure-iot` nainstalovaným rozšířením CLI. Další informace najdete v tématu věnovaném [postupům pro instalaci rozšíření Azure IoT pro Azure Azure CLI](/cli/azure/azure-cli-reference-for-iot).
- **IoT Hub** položky SKU buď F1, S1, S2 nebo S3.
- Mít **IoT Edge zařízení s verzí 1,2 nebo vyšší**. Vzhledem k tomu, že IoT Edge zprostředkovatel MQTT je aktuálně ve verzi Public Preview, nastavte následující proměnné prostředí na hodnotu true v kontejneru edgeHub pro povolení zprostředkovatele MQTT:

   | Name | Hodnota |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__mqttBrokerEnabled` | `true` |

- **Mosquitto klienti** nainstalují na zařízení IoT Edge. Tento článek používá oblíbené klienty Mosquitto [MOSQUITTO_PUB](https://mosquitto.org/man/mosquitto_pub-1.html) a [MOSQUITTO_SUB](https://mosquitto.org/man/mosquitto_sub-1.html). Místo toho se dají použít jiní MQTT klienti. Pokud chcete nainstalovat klienty Mosquitto na zařízení Ubuntu, spusťte následující příkaz:

    ```cmd
    sudo apt-get update && sudo apt-get install mosquitto-clients
    ```

    Neinstalujte server Mosquitto, protože může způsobit blokování portů MQTT (8883 a 1883) a je v konfliktu s centrem IoT Edge.

## <a name="connect-to-iot-edge-hub"></a>Připojení k centru IoT Edge

Připojení k IoT Edge hub se řídí stejnými kroky, jak je popsáno v tématu [připojení k IoT Hub s obecným klientským článkem MQTT](../iot-hub/iot-hub-mqtt-support.md) nebo v [koncepčním popisu článku IoT Edge centra](iot-edge-runtime.md). Tyto kroky:

1. Volitelně klient MQTT vytvoří *zabezpečené připojení* k IoT Edge centru pomocí protokolu TLS (Transport Layer Security).
2. Klient MQTT se sám *ověřuje* do centra IoT Edge.
3. Centrum IoT Edge *autorizuje* klienta MQTT na své zásady autorizace.

### <a name="secure-connection-tls"></a>Zabezpečené připojení (TLS)

Protokol TLS (Transport Layer Security) slouží k navázání šifrované komunikace mezi klientem a centrem IoT Edge.

Pokud chcete protokol TLS zakázat, použijte port 1883 (MQTT) a navažte kontejner edgeHub na port 1883.

Pokud chcete povolit protokol TLS, bude se iniciovat kanál TLS, pokud se klient připojí na portu 8883 (MQTTS) k zprostředkovateli MQTT. Zprostředkovatel odešle svůj řetěz certifikátů, který musí klient ověřit. Aby bylo možné ověřit řetěz certifikátů, musí být kořenový certifikát zprostředkovatele MQTT nainstalován jako důvěryhodný certifikát na klientovi. Pokud kořenový certifikát není důvěryhodný, klient služby MQTT odmítl knihovnu klienta s chybou ověřování certifikátu. Postup pro instalaci tohoto kořenového certifikátu zprostředkovatele na straně klienta je stejný jako v případě [transparentní brány](how-to-create-transparent-gateway.md) a je popsaný v dokumentaci [Příprava zařízení pro příjem dat](how-to-connect-downstream-device.md#prepare-a-downstream-device) .

### <a name="authentication"></a>Authentication

Aby se klient MQTT mohl sám ověřit, musí nejdřív odeslat paket připojení ke zprostředkovateli MQTT a iniciovat připojení v jeho názvu. Tento paket nabízí tři části ověřovacích informací: a `client identifier` , a `username` a `password` :

- `client identifier`Pole je název zařízení nebo modulu v IoT Hub. Používá následující syntaxi:

  - Pro zařízení: `<device_name>`

  - Pro modul: `<device_name>/<module_name>`

   Aby bylo možné se připojit ke zprostředkovateli MQTT, musí být zařízení nebo modul registrován v IoT Hub.

   Zprostředkovatel neumožní připojení z více klientů pomocí stejných přihlašovacích údajů. Zprostředkovatel odpojí již připojeného klienta, pokud se druhý klient připojí pomocí stejných přihlašovacích údajů.

- `username`Pole je odvozeno z názvu zařízení nebo modulu a název IoTHub, ke kterému zařízení patří, pomocí následující syntaxe:

  - Pro zařízení: `<iot_hub_name>.azure-devices.net/<device_name>/?api-version=2018-06-30`

  - Pro modul: `<iot_hub_name>.azure-devices.net/<device_name>/<module_name>/?api-version=2018-06-30`

- `password`Pole paketu připojení závisí na režimu ověřování:

  - Při použití [ověřování symetrických klíčů](how-to-authenticate-downstream-device.md#symmetric-key-authentication) `password` je pole token SAS.
  - Pokud používáte [ověřování pomocí X. 509 podepsané svým držitelem](how-to-authenticate-downstream-device.md#x509-self-signed-authentication), `password` pole není k dispozici. V tomto režimu ověřování je vyžadován kanál TLS. Aby bylo možné navázat připojení TLS, musí se klient připojit k portu 8883. Během metody handshake TLS zprostředkovatel MQTT požaduje certifikát klienta. Tento certifikát se používá k ověření identity klienta, takže `password` pole není potřeba později po odeslání paketu připojení. Odesláním klientského certifikátu a pole heslo dojde k chybě a připojení bude zavřeno. Knihovny MQTT a klientské knihovny TLS mají obvykle způsob, jak odeslat klientský certifikát při inicializaci připojení. Podrobný příklad najdete v části [použití certifikátu x509 pro ověřování klientů](how-to-authenticate-downstream-device.md#x509-self-signed-authentication).

Moduly nasazené pomocí IoT Edge používají [ověřování symetrických klíčů](how-to-authenticate-downstream-device.md#symmetric-key-authentication) a můžou volat místní [rozhraní API pro IoT Edge úlohy](https://github.com/Azure/iotedge/blob/40f10950dc65dd955e20f51f35d69dd4882e1618/edgelet/workload/README.md) , které programově získá token SAS, i když je offline.

### <a name="authorization"></a>Autorizace

Jakmile je klient MQTT ověřený pro IoT Edge centra, musí mít oprávnění k připojení. Po připojení je potřeba mít oprávnění k publikování nebo přihlášení k odběru určitých témat. Tato autorizace jsou udělována službou IoT Edge hub na základě zásad autorizace. Zásady autorizace jsou sady příkazů vyjádřené jako struktura JSON, která se odesílá do centra IoT Edge prostřednictvím jeho vlákna. Pokud chcete nakonfigurovat zásady autorizace, upravte stav vlákna IoT Edge hub.

> [!NOTE]
> V rámci verze Public Preview jsou úpravy zásad autorizace zprostředkovatele MQTT k dispozici pouze prostřednictvím sady Visual Studio, Visual Studio Code nebo rozhraní příkazového řádku Azure CLI. Azure Portal v současné době nepodporuje úpravu vláken centra IoT Edge a zásady autorizace.

Každý příkaz zásad autorizace se skládá z kombinace `identities` efektů, `allow` `deny` `operations` a `resources` :

- `identities` Popište předmět zásady. Musí se mapovat na `client identifier` zprávy odesílané klienty v jejich paketu připojení.
- `allow` nebo `deny` efekty definují, jestli se mají operace povolit nebo zakázat.
- `operations` Definujte akce, které mají být autorizovány. `mqtt:connect``mqtt:publish`a `mqtt:subscribe` jsou dnes tři podporované akce.
- `resources` Definujte objekt zásady. Může se jednat o téma nebo vzor tématu definovaný pomocí [zástupných znaků MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107).

Níže je uveden příklad zásad autorizace, které explicitně nepovolují, aby se klient rogue_client připojit, umožňuje všem klientům Azure IoT připojit se a povolit publikování do tématu pomocí "sensor_1" `events/alerts` .

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "routes":{
            "Route1":"FROM /messages/* INTO $upstream"
         },
         "storeAndForwardConfiguration":{
            "timeToLiveSecs":7200
         },
         "mqttBroker":{
            "authorizations":[
               {
                  "identities":[
                     "rogue_client"
                  ],
                  "deny":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "sensor_1"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "events/alerts"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

Při psaní zásad autorizace je potřeba mít na paměti několik věcí:

- Vyžaduje `$edgeHub` schéma s dvojitou verzí 1,2.
- Ve výchozím nastavení jsou všechny operace odepřeny.
- Příkazy autorizace jsou vyhodnocovány v pořadí, v jakém jsou uvedeny v definici JSON. Začne tím `identities` , že prohlíží a pak vybere první příkazy Allow nebo Deny, které odpovídají žádosti. V případě konfliktů mezi příkazy povolit a odepřít se jedná o příkaz Deny.
- V zásadách autorizace se dá použít několik proměnných (například náhrady):
    - `{{iot:identity}}` představuje identitu aktuálně připojeného klienta. Například identitu zařízení jako `myDevice` nebo identitu modulu `myEdgeDevice/SampleModule` , například.
    - `{{iot:device_id}}` představuje identitu aktuálně připojeného zařízení. Například identitu zařízení jako `myDevice` nebo identitu zařízení, na které se modul spouští `myEdgeDevice` .
    - `{{iot:module_id}}` představuje identitu aktuálně připojeného modulu. Tato proměnná je prázdná pro připojená zařízení nebo identitu modulu, jako je `SampleModule` .
    - `{{iot:this_device_id}}` představuje identitu zařízení IoT Edge, na kterém je spuštěná zásada autorizace. Například, `myIoTEdgeDevice`.

Ověřování pro témata služby IoT Hub se zpracovává trochu jinak než uživatelsky definovaná témata. Tady jsou klíčové body, které si zapamatujete:

- Zařízení nebo moduly Azure IoT potřebují explicitní autorizační pravidlo pro připojení ke službě IoT Edge hub MQTT Broker. Níže jsou uvedené výchozí zásady autorizace připojení.
- Zařízení nebo moduly Azure IoT mají ke svým vlastním tématům služby IoT Hub přístup ve výchozím nastavení bez explicitního autorizačního pravidla. V takovém případě se ale autorizace vyplňují z vztahů nadřazenosti a podřízenosti a tyto vztahy musí být nastavené. IoT Edge moduly se automaticky nastaví jako podřízené položky jejich IoT Edge zařízení, ale zařízení musí být explicitně nastavená jako podřízené pro bránu IoT Edge.

Tady je výchozí zásada autorizace, která se dá použít k tomu, aby se všechna zařízení nebo moduly Azure IoT mohla **připojit** ke zprostředkovateli:

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

Teď, když jste pochopili, jak se připojit k IoT Edge zprostředkovateli MQTT, Podívejme se, jak se dá použít k tomu, aby se zprávy nejdřív publikovaly a přihlásily k nim na základě uživatelsky definovaných témat, potom v tématech IoT Hub a nakonec na jiného zprostředkovatele MQTT.

## <a name="publish-and-subscribe-on-user-defined-topics"></a>Témata týkající se publikování a přihlášení k odběru uživatelem definovaných témat

V tomto článku použijete jednoho klienta s názvem **sub_client** , který se přihlásí k odběru tématu a jinému klientovi s názvem **pub_client** , který se publikuje v tématu. Použijeme ověřování pomocí [symetrického klíče](how-to-authenticate-downstream-device.md#symmetric-key-authentication) , ale totéž se dá udělat s ověřováním pomocí x. 509, které je podepsané [svým držitelem](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) nebo [ověřováním na základě CA x. 509](./how-to-authenticate-downstream-device.md#x509-ca-signed-authentication).

### <a name="create-publisher-and-subscriber-clients"></a>Vytváření klientů vydavatelů a odběratelů

V IoT Hub vytvořte dvě zařízení IoT a získejte jejich hesla. Použití Azure CLI z terminálu na:

1. V IoT Hub vytvořte dvě zařízení IoT, která jsou nadřazená vašim zařízením IoT Edge:

    ```azurecli-interactive
    az iot hub device-identity create --device-id  sub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    az iot hub device-identity create --device-id  pub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    ```

2. Získejte hesla vygenerováním tokenu SAS:

    - Pro zařízení:
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> --key-type primary --du 3600
       ```
    
       kde 3600 je doba trvání tokenu SAS v sekundách (například 3600 = 1 hodina).
    
    - Pro modul:
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> -m <module_name> --key-type primary --du 3600
       ```
    
       kde 3600 je doba trvání tokenu SAS v sekundách (například 3600 = 1 hodina).

3. Zkopírujte token SAS, což je hodnota odpovídající klíči "SAS" z výstupu. Tady je příklad výstupu z příkazu Azure CLI výše:

    ```
    {
       "sas": "SharedAccessSignature sr=example.azure-devices.net%2Fdevices%2Fdevice_1%2Fmodules%2Fmodule_a&sig=H5iMq8ZPJBkH3aBWCs0khoTPdFytHXk8VAxrthqIQS0%3D&se=1596249190"
    }
    ```

### <a name="authorize-publisher-and-subscriber-clients"></a>Autorizovat klienty vydavatelů a odběratelů

Pokud chcete autorizovat vydavatele a předplatitele, upravte IoT Edge centra vytvořením nasazení IoT Edge prostřednictvím rozhraní příkazového řádku Azure CLI, sady Visual Studio nebo Visual Studio Code, abyste zahrnuli následující zásady autorizace:

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/sub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:subscribe"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ],
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/pub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

### <a name="symmetric-keys-authentication-without-tls"></a>Ověřování symetrických klíčů bez TLS

#### <a name="subscribe"></a>Přihlásit k odběru

Připojte klienta **sub_client** MQTT ke zprostředkovateli MQTT a přihlaste se k odběru spuštěním `test_topic` následujícího příkazu na vašem zařízení IoT Edge:

```bash
mosquitto_sub \
    -t "test_topic" \
    -i "sub_client" \
    -u "<iot_hub_name>.azure-devices.net/sub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883
```

`<edge_device_address>`  =  `localhost` v tomto příkladu, protože klient je spuštěný na stejném zařízení jako IoT Edge.

Všimněte si, že v tomto prvním příkladu se používá port 1883 (MQTT) bez TLS. Další příklad s portem 8883 (MQTTS) s povoleným protokolem TLS je zobrazený v další části.

Klient **sub_client** MQTT je teď spuštěný a čeká na příchozí zprávy `test_topic` .

#### <a name="publish"></a>Publikovat

Připojte klienta **pub_client** MQTT ke zprostředkovateli MQTT a publikuje zprávu na stejném základě, a `test_topic` to spuštěním následujícího příkazu na zařízení IoT Edge z jiného terminálu:

```bash
mosquitto_pub \
    -t "test_topic" \
    -i "pub_client" \
    -u "<iot_hub_name>.azure-devices.net/pub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883 \
    -m "hello"
```

`<edge_device_address>`  =  `localhost` v tomto příkladu, protože klient je spuštěný na stejném zařízení jako IoT Edge.

Spuštění příkazu, klient **sub_client** MQTT obdrží zprávu "Hello".

### <a name="symmetric-keys-authentication-with-tls"></a>Ověřování symetrických klíčů pomocí protokolu TLS

Aby bylo možné protokol TLS povolit, musí být port změněn z 1883 (MQTT) na 8883 (MQTTS) a klienti musí mít kořenový certifikát zprostředkovatele MQTT, aby mohl ověřit řetěz certifikátů odesílaných zprostředkovatelem MQTT. To lze provést podle kroků uvedených v části [zabezpečené připojení (TLS)](#secure-connection-tls).

Vzhledem k tomu, že klienti běží na stejném zařízení jako služba MQTT Broker v příkladu výše, platí stejný postup jenom pro povolení TLS jenom změnou čísla portu z 1883 (MQTT) na 8883 (MQTTS).

## <a name="publish-and-subscribe-on-iot-hub-topics"></a>Témata týkající se publikování a přihlášení k odběru IoT Hub

Sady [SDK pro zařízení Azure IoT](https://github.com/Azure/azure-iot-sdks) již umožňují klientům provádět operace IoT Hub, ale neumožňují publikování a přihlašování k odběru uživatelem definovaných témat. IoT Hub operace se dají provádět pomocí všech klientů MQTT s využitím publikování a přihlášení k odběru, dokud jsou respektovány protokoly primitivních hub. Podíváme se na to, abychom porozuměli tomu, jak tyto protokoly fungují.

### <a name="send-telemetry-data-to-iot-hub"></a>Odeslat data telemetrie do IoT Hub

Posílání dat telemetrie do IoT Hub se podobá publikování v uživatelsky definovaném tématu, ale pomocí konkrétního IoT Hubho tématu:

- V případě zařízení se telemetrie odesílají v tématu: `devices/<device_name>/messages/events/`
- Pro modul se telemetrie pošle v tématu: `devices/<device_name>/<module_name>/messages/events/`

Navíc můžete vytvořit trasu, například `FROM /messages/* INTO $upstream` k odeslání telemetrie z zprostředkovatele IoT Edge MQTT do služby IoT Hub. Další informace o směrování naleznete v tématu [Declare Routes](module-composition.md#declare-routes).

### <a name="get-twin"></a>Získat dvojitou

Získání nevlákenných zařízení/modulu není typický vzor MQTT. Klient musí vystavit požadavek na vlákna, které IoT Hub bude sloužit.

Aby bylo možné přijímat vlákna, musí se klient přihlásit k odběru IoT Hub konkrétnímu tématu `$iothub/twin/res/#` . Název tohoto tématu se dědí z IoT Hub a všichni klienti se musí přihlásit k odběru stejného tématu. Neznamená to, že zařízení nebo moduly dostanou vlákna navzájem. Centrum IoT Hub a IoT Edge ví, která vlákna by měla být doručena tam, kde, i když všechna zařízení naslouchají stejnému názvu tématu. 

Po vytvoření předplatného musí klient požádat o vyzdvojení publikováním zprávy do IoT Hub konkrétního tématu, `$iothub/twin/GET/?rid=<request_id>/#` kde  `<request_id>` je libovolný identifikátor. Služba IoT Hub pak pošle odpověď s požadovanými daty v tématu `$iothub/twin/res/200/?rid=<request_id>` , ke kterému se klient přihlašuje. V takovém případě může klient spárovat své žádosti s odpověďmi.

### <a name="receive-twin-patches"></a>Příjem dvojitých oprav

Aby bylo možné přijímat zdvojené opravy, je nutné, aby se klient přihlásil k odběru speciálního tématu IoTHub `$iothub/twin/PATCH/properties/desired/#` . Po vytvoření předplatného obdrží klient dopředné opravy odesílané IoT Hub v tomto tématu. 

### <a name="receive-direct-methods"></a>Příjem přímých metod

Příjem přímé metody je podobný jako příjem úplných vláken s přidáním, který klient potřebuje k potvrzení, že přijal volání. Nejdřív se klient přihlašuje k odběru speciálního tématu ve službě IoT Hub `$iothub/methods/POST/#` . Poté, co je v tomto tématu přijata přímá metoda, musí klient extrahovat identifikátor požadavku `rid` z dílčího tématu, na kterém je přijata přímá metoda, a nakonec publikovat potvrzovací zprávu ve speciálním tématu služby IoT Hub `$iothub/methods/res/200/<request_id>` .

### <a name="send-direct-methods"></a>Odeslání přímých metod

Odeslání přímé metody je volání HTTP, a proto neprojde zprostředkovatelem MQTT. Chcete-li odeslat přímou metodu do centra IoT, přečtěte si téma [pochopení a vyvolání přímých metod](../iot-hub/iot-hub-devguide-direct-methods.md). Pokud chcete poslat přímou metodu místně do jiného modulu, přečtěte si [příklad volání metody Azure IoT C# SDK Direct](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/ModuleClient.cs#L597).

## <a name="publish-and-subscribe-between-mqtt-brokers"></a>Publikování a přihlášení k odběru mezi zprostředkovateli MQTT

Pro připojení dvou zprostředkovatelů MQTT zahrnuje centrum IoT Edge most MQTT. Most MQTT se běžně používá k připojení zprostředkovatele MQTT spuštěného k jinému zprostředkovateli MQTT. Pouze podmnožinu místních přenosů je obvykle vložena do jiného zprostředkovatele.

> [!NOTE]
> Most centra IoT Edge se dá v současné době použít jenom mezi vnořenými IoT Edge zařízeními. Nedá se použít k posílání dat do služby IoT Hub, protože IoT Hub není plnohodnotným MQTT brokerem. Další informace o podpoře funkcí zprostředkovatele služby IoT Hub MQTT najdete v tématu [komunikace se službou IoT Hub pomocí protokolu MQTT](../iot-hub/iot-hub-mqtt-support.md). Další informace o vnořování IoT Edge zařízení najdete v tématu [připojení zařízení IoT Edge pro Azure IoT Edge k bráně](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices) . 

V rámci vnořené Konfigurace přemostění služby IoT Edge hub MQTT funguje jako klient nadřazeného zprostředkovatele MQTT. proto musí být autorizační pravidla nastavena v nadřazeném EdgeHub, aby bylo možné podřízené EdgeHub publikovat a přihlásit se k odběru konkrétních uživatelsky definovaných témat, pro která je most nakonfigurovaný.

Most IoT Edge MQTT je nakonfigurován prostřednictvím struktury JSON, která se odesílá do centra IoT Edge prostřednictvím jeho vlákna. Pokud chcete nakonfigurovat svůj MQTT most, upravte dvojitou událost centra IoT Edge.

> [!NOTE]
> V rámci verze Public Preview je konfigurace mostu MQTT k dispozici pouze prostřednictvím sady Visual Studio, Visual Studio Code nebo rozhraní příkazového řádku Azure CLI. Azure Portal v současné době nepodporuje úpravu vláken centra IoT Edge a jeho konfigurace mostu MQTT.

Most MQTT je možné nakonfigurovat tak, aby se připojil k několika externím zprostředkovatelům IoT Edge prostřednictvím služby MQTT hub. U každého externího zprostředkovatele se vyžadují následující nastavení:

- `endpoint` je adresa vzdáleného zprostředkovatele MQTT, ke kterému se má připojit. V současné době jsou podporovány pouze nadřazené IoT Edge zařízení a jsou definovány proměnnou `$upstream` .
- `settings` definuje témata, která se mají Přemostit pro koncový bod. U každého koncového bodu může být víc nastavení a pro jeho konfiguraci se použijí tyto hodnoty:
    - `direction`: buď `in` k přihlášení k odběru témat vzdáleného zprostředkovatele nebo `out` k publikování do témat vzdáleného zprostředkovatele
    - `topic`: model základního tématu, který se má shodovat. K definování tohoto vzoru lze použít [zástupné znaky MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107) . Pro tento vzor tématu můžete použít jiné předpony na místním zprostředkovateli a vzdáleném zprostředkovateli.
    - `outPrefix`: Předpona, která se aplikuje na `topic` vzor na vzdáleném zprostředkovateli.
    - `inPrefix`: Předpona, která se aplikuje na `topic` vzor na místním zprostředkovateli.

Níže je uveden příklad konfigurace mostu IoT Edge MQTT, která znovu publikuje všechny zprávy přijaté v tématech `alerts/#` nadřazeného IoT Edge zařízení do podřízeného IoT Edge zařízení ve stejných tématech a znovu publikuje všechny zprávy odeslané v tématech `/local/telemetry/#` podřízeného IoT Edge zařízení do nadřazeného IoT Edge zařízení v tématech `/remote/messages/#` .

```json
{
    "schemaVersion": "1.2",
    "mqttBroker": {
        "bridges": [{
            "endpoint": "$upstream",
            "settings": [{
                    "direction": "in",
                    "topic": "alerts/#"
                },
                {
                    "direction": "out",
                    "topic": "",
                    "inPrefix": "/local/telemetry",
                    "outPrefix": "/remote/messages"
                }
            ]
        }]
    }
}
```
Další poznámky k MQTT mostu centra IoT Edge:
- Protokol MQTT se automaticky použije jako nadřazený protokol, pokud se použije zprostředkovatel MQTT a že se IoT Edge používá ve vnořené konfiguraci, například se `parent_hostname` zadaným. Další informace o nadřazených protokolech najdete v tématu [cloudová komunikace](iot-edge-runtime.md#cloud-communication). Další informace o vnořených konfiguracích najdete v tématu [připojení zařízení IoT Edge pro příjem dat k bráně Azure IoT Edge](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices).

## <a name="next-steps"></a>Další kroky

[Principy centra IoT Edge](iot-edge-runtime.md#iot-edge-hub)
