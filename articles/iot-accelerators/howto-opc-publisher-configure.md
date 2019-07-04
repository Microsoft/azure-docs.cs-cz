---
title: Konfigurace vydavatele OPC – Azure | Dokumentace Microsoftu
description: Konfigurace vydavatele OPC
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: d262c83b0ae886806b8c4bce2375ffc10ad22c75
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450755"
---
# <a name="configure-opc-publisher"></a>Konfigurace vydavatele OPC

Vydavatel OPC k určení můžete nakonfigurovat:

- Změny dat uzlu OPC UA k publikování
- Události OPC UA, které chcete publikovat.
- Formát telemetrická data.

Vydavatel OPC pomocí konfiguračních souborů nebo pomocí volání metody můžete nakonfigurovat.

## <a name="use-configuration-files"></a>Použití konfiguračních souborů

Tato část popisuje možnosti pro konfiguraci publikování uzlu OPC UA pro konfigurační soubory.

### <a name="use-a-configuration-file-to-configure-publishing-data-changes"></a>Konfigurace publikování změn dat pomocí konfiguračního souboru

Nejjednodušší způsob, jak nakonfigurovat budou publikovat uzly OPC UA je s konfiguračním souborem. Formát konfiguračního souboru je popsána v [publishednodes.json](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/publishednodes.json) v úložišti.

Syntaxe konfigurační soubor byl změněn v čase. Vydavatel OPC stále přečte staré formáty, ale při zůstává v konfiguraci je převede na nejnovější formát.

Následující příklad ukazuje Formát konfiguračního souboru:

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

### <a name="use-a-configuration-file-to-configure-publishing-events"></a>Pomocí konfiguračního souboru pro konfiguraci publikování událostí

K publikování událostí OPC UA, použít stejný konfigurační soubor jako u změny data.

Následující příklad ukazuje, jak nakonfigurovat publikování pro události generované modulem [SimpleEvents server](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/SimpleEvents/Server). SimpleEvents serveru najdete v [OPC Foundation úložiště](https://github.com/OPCFoundation/UA-.NETStandard) je:

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62563/Quickstarts/SimpleEventsServer",
    "OpcEvents": [
      {
        "Id": "i=2253",
        "DisplayName": "SimpleEventServerEvents",
        "SelectClauses": [
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "EventId"
            ]
          },
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "Message"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CycleId"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CurrentStep"
            ]
          }
        ],
        "WhereClause": [
          {
            "Operator": "OfType",
            "Operands": [
              {
                "Literal": "ns=2;i=235"
              }
            ]
          }
        ]
      }
    ]
  }
]
```

## <a name="use-method-calls"></a>Používat volání metody

Tato část popisuje volání metody, které můžete použít ke konfiguraci vydavatele OPC.

### <a name="configure-using-opc-ua-method-calls"></a>Konfigurace pomocí volání metody OPC UA

Vydavatel OPC zahrnuje Server OPC UA, který je přístupný na port 62222. Pokud je název hostitele **vydavatele**, pak je identifikátor URI koncového bodu: `opc.tcp://publisher:62222/UA/Publisher`.

Tento koncový bod poskytuje následující čtyři metody:

- PublishNode
- UnpublishNode
- GetPublishedNodes
- IoT HubDirectMethod

### <a name="configure-using-iot-hub-direct-method-calls"></a>Konfigurace prostřednictvím přímé metody volání služby IoT Hub

Vydavatel OPC implementuje tyto přímé metody volání služby IoT Hub:

- PublishNodes
- UnpublishNodes
- UnpublishAllNodes
- GetConfiguredEndpoints
- GetConfiguredNodesOnEndpoint
- GetDiagnosticInfo
- GetDiagnosticLog
- GetDiagnosticStartupLog
- ExitApplication
- GetInfo

Formát datové části JSON metoda požadavku a odpovědi, které jsou definovány v [opcpublisher/HubMethodModel.cs](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/HubMethodModel.cs).

Při volání Neznámá metoda v modulu, odpoví na řetězec, který říká, že metoda není implementována. Neznámá metoda může volat jako způsob, jak pomocí příkazu ping v modulu.

### <a name="configure-username-and-password-for-authentication"></a>Konfigurace uživatelského jména a hesla pro ověřování

Režim ověřování můžete nastavit prostřednictvím služby IoT Hub přímá volání metody. Datová část musí obsahovat vlastnost **OpcAuthenticationMode** a uživatelské jméno a heslo:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "UsernamePassword",
    "Username": "<Username>",
    "Password": "<Password>"
    ...
}
```

Heslo je zašifrováno klienta úlohy IoT Hub a uložené v jeho konfiguraci. Chcete-li změnit zpět na anonymní ověřování, použijte metodu s následující datové části:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "Anonymous"
    ...
}
```

Pokud **OpcAuthenticationMode** v datové části není nastavena vlastnost, zůstávají beze změny v konfiguraci nastavení ověřování.

## <a name="configure-telemetry-publishing"></a>Konfigurace telemetrie publikování

Vydavatel OPC obdrží oznámení o změně hodnot v publikované uzlu, vygeneruje formátovaných zpráv JSON, který je odeslán do služby IoT Hub.

Můžete nakonfigurovat obsah formátované zprávy JSON je používán konfigurační soubor. Pokud není určen žádný konfigurační soubor s `--tc` není použita možnost, výchozí konfigurace, který je kompatibilní s [akcelerátor řešení propojené továrny](https://github.com/Azure/azure-iot-connected-factory).

Pokud vydavatel OPC je konfigurován pro zprávy dávkových, se už odeslaný jako platné pole JSON.

Telemetrie je odvozen z následujících zdrojů:

- Konfigurace uzlu vydavatele OPC pro uzel
- **MonitoredItem** objektu sady OPC UA pro kterou vydavatel OPC obdržela oznámení.
- Argument předaný do toto oznámení, který obsahuje podrobné informace o změnu hodnoty data.

Telemetrická data, která se zařadí do formátovaná zpráva JSON je výběr důležité vlastnosti z těchto objektů. Pokud potřebujete další vlastnosti, musíte změnit základ kódu vydavatel OPC.

Syntaxe konfiguračního souboru je následující:

```json
// The configuration settings file consists of two objects:
// 1) The 'Defaults' object, which defines defaults for the telemetry configuration
// 2) An array 'EndpointSpecific' of endpoint specific configuration
// Both objects are optional and if they are not specified, then publisher uses
// its internal default configuration, which generates telemetry messages compatible
// with the Microsoft Connected factory Preconfigured Solution (https://github.com/Azure/azure-iot-connected-factory).

// A JSON telemetry message for Connected factory looks like:
//  {
//      "NodeId": "i=2058",
//      "ApplicationUri": "urn:myopcserver",
//      "DisplayName": "CurrentTime",
//      "Value": {
//          "Value": "10.11.2017 14:03:17",
//          "SourceTimestamp": "2017-11-10T14:03:17Z"
//      }
//  }

// The 'Defaults' object in the sample below, are similar to what publisher is
// using as its internal default telemetry configuration.
{
    "Defaults": {
        // The first two properties ('EndpointUrl' and 'NodeId' are configuring data
        // taken from the OpcPublisher node configuration.
        "EndpointUrl": {

            // The following three properties can be used to configure the 'EndpointUrl'
            // property in the JSON message send by publisher to IoT Hub.

            // Publish controls if the property should be part of the JSON message at all.
            "Publish": false,

            // Pattern is a regular expression, which is applied to the actual value of the
            // property (here 'EndpointUrl').
            // If this key is ommited (which is the default), then no regex matching is done
            // at all, which improves performance.
            // If the key is used you need to define groups in the regular expression.
            // Publisher applies the regular expression and then concatenates all groups
            // found and use the resulting string as the value in the JSON message to
            //sent to IoT Hub.
            // This example mimics the default behaviour and defines a group,
            // which matches the conplete value:
            "Pattern": "(.*)",
            // Here some more exaples for 'Pattern' values and the generated result:
            // "Pattern": "i=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of '2058'to be sent by publisher
            // "Pattern": "(i)=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of 'i2058' to be sent by publisher

            // Name allows you to use a shorter string as property name in the JSON message
            // sent by publisher. By default the property name is unchanged and will be
            // here 'EndpointUrl'.
            // The 'Name' property can only be set in the 'Defaults' object to ensure
            // all messages from publisher sent to IoT Hub have a similar layout.
            "Name": "EndpointUrl"

        },
        "NodeId": {
            "Publish": true,

            // If you set Defaults.NodeId.Name to "ni", then the "NodeId" key/value pair
            // (from the above example) will change to:
            //      "ni": "i=2058",
            "Name": "NodeId"
        },

        // The MonitoredItem object is configuring the data taken from the MonitoredItem
        // OPC UA object for published nodes.
        "MonitoredItem": {

            // If you set the Defaults.MonitoredItem.Flat to 'false', then a
            // 'MonitoredItem' object will appear, which contains 'ApplicationUri'
            // and 'DisplayNode' proerties:
            //      "NodeId": "i=2058",
            //      "MonitoredItem": {
            //          "ApplicationUri": "urn:myopcserver",
            //          "DisplayName": "CurrentTime",
            //      }
            // The 'Flat' property can only be used in the 'MonitoredItem' and
            // 'Value' objects of the 'Defaults' object and will be used
            // for all JSON messages sent by publisher.
            "Flat": true,

            "ApplicationUri": {
                "Publish": true,
                "Name": "ApplicationUri"
            },
            "DisplayName": {
                "Publish": true,
                "Name": "DisplayName"
            }
        },
        // The Value object is configuring the properties taken from the event object
        // the OPC UA stack provided in the value change notification event.
        "Value": {
            // If you set the Defaults.Value.Flat to 'true', then the 'Value'
            // object will disappear completely and the 'Value' and 'SourceTimestamp'
            // members won't be nested:
            //      "DisplayName": "CurrentTime",
            //      "Value": "10.11.2017 14:03:17",
            //      "SourceTimestamp": "2017-11-10T14:03:17Z"
            // The 'Flat' property can only be used for the 'MonitoredItem' and 'Value'
            // objects of the 'Defaults' object and will be used for all
            // messages sent by publisher.
            "Flat": false,

            "Value": {
                "Publish": true,
                "Name": "Value"
            },
            "SourceTimestamp": {
                "Publish": true,
                "Name": "SourceTimestamp"
            },
            // 'StatusCode' is the 32 bit OPC UA status code
            "StatusCode": {
                "Publish": false,
                "Name": "StatusCode"
                // 'Pattern' is ignored for the 'StatusCode' value
            },
            // 'Status' is the symbolic name of 'StatusCode'
            "Status": {
                "Publish": false,
                "Name": "Status"
            }
        }
    },

    // The next object allows to configure 'Publish' and 'Pattern' for specific
    // endpoint URLs. Those will overwrite the ones specified in the 'Defaults' object
    // or the defaults used by publisher.
    // It is not allowed to specify 'Name' and 'Flat' properties in this object.
    "EndpointSpecific": [
        // The following shows how a endpoint specific configuration can look like:
        {
            // 'ForEndpointUrl' allows to configure for which OPC UA server this
            // object applies and is a required property for all objects in the
            // 'EndpointSpecific' array.
            // The value of 'ForEndpointUrl' must be an 'EndpointUrl' configured in
            // the publishednodes.json confguration file.
            "ForEndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
            "EndpointUrl": {
                // We overwrite the default behaviour and publish the
                // endpoint URL in this case.
                "Publish": true,
                // We are only interested in the URL part following the 'opc.tcp://' prefix
                // and define a group matching this.
                "Pattern": "opc.tcp://(.*)"
            },
            "NodeId": {
                // We are not interested in the configured 'NodeId' value, 
                // so we do not publish it.
                "Publish": false
                // No 'Pattern' key is specified here, so the 'NodeId' value will be
                // taken as specified in the publishednodes configuration file.
            },
            "MonitoredItem": {
                "ApplicationUri": {
                    // We already publish the endpoint URL, so we do not want
                    // the ApplicationUri of the MonitoredItem to be published.
                    "Publish": false
                },
                "DisplayName": {
                    "Publish": true
                }
            },
            "Value": {
                "Value": {
                    // The value of the node is important for us, everything else we
                    // are not interested in to keep the data ingest as small as possible.
                    "Publish": true
                },
                "SourceTimestamp": {
                    "Publish": false
                },
                "StatusCode": {
                    "Publish": false
                },
                "Status": {
                    "Publish": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Další postup

Nyní jste zjistili, jak nakonfigurovat vydavatel OPC, navrhované dalším krokem je další způsob [spustit vydavatele OPC](howto-opc-publisher-run.md).
