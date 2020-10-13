---
title: Konfigurace vydavatele OPC – Azure | Microsoft Docs
description: Tento článek popisuje, jak nakonfigurovat OPC Publisher tak, aby určoval změny dat uzlu OPC UA, události OPC UA pro publikování a také formát telemetrie.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.custom: devx-track-csharp
ms.openlocfilehash: b004bb4fbca768d782863f73d83ec5443bc66ae3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320605"
---
# <a name="configure-opc-publisher"></a>Konfigurace vydavatele OPC

> [!IMPORTANT]
> I když aktualizujeme Tento článek, přečtěte si nejaktuálnější obsah v tématu [Azure Data IoT](https://azure.github.io/Industrial-IoT/) .

Vydavatele OPC můžete nakonfigurovat tak, aby určovalo:

- Data uzlu OPC UA se mění pro publikování.
- Události OPC UA k publikování.
- Formát telemetrie.

Můžete nakonfigurovat vydavatele OPC pomocí konfiguračních souborů nebo pomocí volání metody.

## <a name="use-configuration-files"></a>Použití konfiguračních souborů

Tato část popisuje možnosti konfigurace publikování uzlu OPC UA pomocí konfiguračních souborů.

### <a name="use-a-configuration-file-to-configure-publishing-data-changes"></a>Konfigurace změn dat publikování pomocí konfiguračního souboru

Nejjednodušší způsob, jak nakonfigurovat uzly OPC UA na publikování, je pomocí konfiguračního souboru. Formát konfiguračního souboru je popsán v části [publishednodes.js](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/publishednodes.json) v úložišti.

Syntaxe konfiguračního souboru se v průběhu času změnila. OPC Publisher pořád čte staré formáty, ale převede je do nejnovějšího formátu, když se konfigurace uchovává.

Následující příklad ukazuje formát konfiguračního souboru:

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

### <a name="use-a-configuration-file-to-configure-publishing-events"></a>Konfigurace událostí publikování pomocí konfiguračního souboru

Pokud chcete publikovat události OPC UA, použijte stejný konfigurační soubor jako u změn dat.

Následující příklad ukazuje, jak konfigurovat publikování pro události vygenerované [SimpleEvents serverem](https://github.com/OPCFoundation/UA-.NETStandard-Samples/tree/master/Workshop/SimpleEvents/Server). Server SimpleEvents najdete v [úložišti OPC Foundation](https://github.com/OPCFoundation/UA-.NETStandard-Samples) :

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

## <a name="use-method-calls"></a>Použití volání metod

Tato část popisuje volání metod, která můžete použít ke konfiguraci vydavatele OPC.

### <a name="configure-using-opc-ua-method-calls"></a>Konfigurace pomocí volání metod OPC UA

Vydavatel OPC zahrnuje server OPC UA, který je k dispozici na portu 62222. Pokud je název hostitele **vydavatelem**, pak identifikátor URI koncového bodu je: `opc.tcp://publisher:62222/UA/Publisher` .

Tento koncový bod zpřístupňuje následující čtyři metody:

- PublishNode
- UnpublishNode
- GetPublishedNodes
- IoT HubDirectMethod

### <a name="configure-using-iot-hub-direct-method-calls"></a>Konfigurace pomocí IoT Hub přímé volání metod

Vydavatel OPC implementuje následující volání přímých metod IoT Hub:

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

Formát datové části JSON žádosti o metodu a odpovědi jsou definované v [opcpublisher/HubMethodModel. cs](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/HubMethodModel.cs).

Pokud voláte neznámou metodu v modulu, odpoví řetězcem, který říká, že metoda není implementována. Můžete zavolat neznámou metodu jako způsob, jak testovat modul pomocí příkazů.

### <a name="configure-username-and-password-for-authentication"></a>Konfigurace uživatelského jména a hesla pro ověřování

Režim ověřování lze nastavit prostřednictvím volání přímé metody IoT Hub. Datová část musí obsahovat vlastnost **OpcAuthenticationMode** a uživatelské jméno a heslo:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "UsernamePassword",
    "Username": "<Username>",
    "Password": "<Password>"
    ...
}
```

Heslo je šifrováno klientem IoT Hub úlohy a Uloženo v konfiguraci vydavatele. Chcete-li změnit ověřování zpět na anonymní, použijte metodu s následující datovou částí:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "Anonymous"
    ...
}
```

Pokud vlastnost **OpcAuthenticationMode** není v datové části nastavená, nastavení ověřování zůstane v konfiguraci beze změn.

## <a name="configure-telemetry-publishing"></a>Konfigurovat publikování telemetrie

Když Vydavatel OPC obdrží oznámení o změně hodnoty v publikovaném uzlu, vygeneruje zprávu ve formátu JSON, která se pošle IoT Hub.

Obsah této zprávy ve formátu JSON můžete nakonfigurovat pomocí konfiguračního souboru. Pokud není k dispozici konfigurační soubor s `--tc` možností, je použita výchozí konfigurace, která je kompatibilní s [akcelerátorem řešení propojená továrna](https://github.com/Azure/azure-iot-connected-factory).

Pokud je Vydavatel OPC nakonfigurovaný na dávkové zprávy, odešle se jako platné pole JSON.

Telemetrii je odvozena z následujících zdrojů:

- Konfigurace uzlu vydavatele OPC pro uzel
- Objekt **MonitoredItem** zásobníku OPC UA, pro který se Vydavatel OPC dostal oznámení.
- Argument předaný do tohoto oznámení, který poskytuje podrobnosti o změně hodnoty dat.

Telemetrii, která je vložená do zprávy ve formátu JSON, je výběr důležitých vlastností těchto objektů. Pokud potřebujete další vlastnosti, je nutné změnit základ kódu vydavatele OPC.

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
            // If this key is omitted (which is the default), then no regex matching is done
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

## <a name="next-steps"></a>Další kroky

Nyní jste se naučili, jak nakonfigurovat vydavatele OPC, navržený další krok se naučíte, jak [Spustit vydavatele OPC](howto-opc-publisher-run.md).
