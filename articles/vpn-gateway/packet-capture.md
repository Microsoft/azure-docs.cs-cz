---
title: 'Azure VPN Gateway: Konfigurace zachycení paketů'
description: Přečtěte si o funkcích zachytávání paketů, které můžete používat na branách VPN.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 3ba3046367ceece6bf0ddf157451025c79977324
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077211"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>Konfigurace zachycení paketů pro brány VPN

Problémy související s připojením a výkonem jsou často složité a využívají velký čas a úsilí jenom k zúžení příčiny problému. Schopnost zachytávání paketů významně pomáhá snižovat čas při zúžení rozsahu problému na určité části sítě, třeba na tom, jestli je problém na straně zákazníka sítě, na straně Azure v síti nebo někde mezi. Po zúžení problému je mnohem efektivnější ladit a provádět nápravné akce.

K dispozici jsou některé běžně dostupné nástroje pro zachytávání paketů. Získání relevantních zachycení paketů pomocí těchto nástrojů může být náročné, zejména při práci s vysokými scénáři provozu. Funkce filtrování, které poskytuje zachytávání paketů brány VPN, se staly významnými odlišnostmi. Kromě běžně dostupných nástrojů pro zachytávání paketů můžete použít také zachytávání paketů brány VPN.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Možnosti filtrování zachytávání paketů brány VPN

Zachytávání paketů služby VPN Gateway můžete v bráně nebo konkrétním připojení spustit v závislosti na potřebách zákazníka. Zachytávání paketů můžete také spouštět na více tunelech současně. Můžete zachytit provoz s jedním nebo obousměrným směrováním, provoz IKE a ESP a vnitřní pakety spolu s filtrováním v bráně VPN.

Při izolaci potíží s vysokým objemem přenosů je užitečné použít filtr s pěti řazenými kolekcemi členů (zdrojová podsíť, cílová podsíť, zdrojový port, cílový port, protokol) a příznaky TCP (SYN, ACK, FIN, URG, PSH, RST).

Viz příklad schématu JSON a JSON s vysvětlením jednotlivých vlastností. Všimněte si také některých omezení při spuštění zachycení paketů:
- Ve schématu se filtr zobrazuje jako pole, ale v současné době lze použít pouze jeden filtr.
- Více zachycení paketů v rámci brány se nepovoluje.
- V jednom okamžiku není povoleno více zachycení paketů na jednom připojení. Můžete spustit zachycení paketů v různých připojeních současně.
- Pro každou bránu je možné souběžně spustit maximálně pět zachycení paketů. Tato zachycení paketů můžou obsahovat kombinaci zachycení paketů v rámci brány nebo zachytávání paketů pro připojení.

### <a name="example-json"></a>Ukázkový kód JSON
```JSON-interactive
{
  "TracingFlags": 11,
  "MaxPacketBufferSize": 120,
  "MaxFileSize": 200,
  "Filters": [
    {
      "SourceSubnets": [
        "20.1.1.0/24"
      ],
      "DestinationSubnets": [
        "10.1.1.0/24"
      ],
      "SourcePort": [
        500
      ],
      "DestinationPort": [
        4500
      ],
      "Protocol": [
        6
      ],
      "TcpFlags": 16,
      "CaptureSingleDirectionTrafficOnly": true
    }
  ]
}
```
### <a name="json-schema"></a>Schéma JSON
```JSON-interactive
{
    "type": "object",
    "title": "The Root Schema",
    "description": "The root schema input JSON filter for packet capture",
    "default": {},
    "additionalProperties": true,
    "required": [
        "TracingFlags",
        "MaxPacketBufferSize",
        "MaxFileSize",
        "Filters"
    ],
    "properties": {
        "TracingFlags": {
            "$id": "#/properties/TracingFlags",
            "type": "integer",
            "title": "The Tracingflags Schema",
            "description": "Tracing flags that customer can pass to define which packets are to be captured. Supported values are CaptureESP = 1, CaptureIKE = 2, CaptureOVPN = 8. The final value is OR of the bits.",
            "default": 11,
            "examples": [
                11
            ]
        },
        "MaxPacketBufferSize": {
            "$id": "#/properties/MaxPacketBufferSize",
            "type": "integer",
            "title": "The Maxpacketbuffersize Schema",
            "description": "Maximum buffer size of each packet. The capture will only contain contents of each packet truncated to this size.",
            "default": 120,
            "examples": [
                120
            ]
        },
        "MaxFileSize": {
            "$id": "#/properties/MaxFileSize",
            "type": "integer",
            "title": "The Maxfilesize Schema",
            "description": "Maximum file size of the packet capture file. It is a circular buffer.",
            "default": 100,
            "examples": [
                100
            ]
        },
        "Filters": {
            "$id": "#/properties/Filters",
            "type": "array",
            "title": "The Filters Schema",
            "description": "An array of filters that can be passed to filter inner ESP traffic.",
            "default": [],
            "examples": [
                [
                    {
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true,
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ]
                    }
                ]
            ],
            "additionalItems": true,
            "items": {
                "$id": "#/properties/Filters/items",
                "type": "object",
                "title": "The Items Schema",
                "description": "An explanation about the purpose of this instance.",
                "default": {},
                "examples": [
                    {
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ],
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true
                    }
                ],
                "additionalProperties": true,
                "required": [
                    "SourceSubnets",
                    "DestinationSubnets",
                    "SourcePort",
                    "DestinationPort",
                    "Protocol",
                    "TcpFlags",
                    "CaptureSingleDirectionTrafficOnly"
                ],
                "properties": {
                    "SourceSubnets": {
                        "$id": "#/properties/Filters/items/properties/SourceSubnets",
                        "type": "array",
                        "title": "The Sourcesubnets Schema",
                        "description": "An array of source subnets that need to match the Source IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "20.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourceSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "20.1.1.0/24"
                            ]
                        }
                    },
                    "DestinationSubnets": {
                        "$id": "#/properties/Filters/items/properties/DestinationSubnets",
                        "type": "array",
                        "title": "The Destinationsubnets Schema",
                        "description": "An array of destination subnets that need to match the Destination IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "10.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "10.1.1.0/24"
                            ]
                        }
                    },
                    "SourcePort": {
                        "$id": "#/properties/Filters/items/properties/SourcePort",
                        "type": "array",
                        "title": "The Sourceport Schema",
                        "description": "An array of source ports that need to match the Source port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourcePort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                500
                            ]
                        }
                    },
                    "DestinationPort": {
                        "$id": "#/properties/Filters/items/properties/DestinationPort",
                        "type": "array",
                        "title": "The Destinationport Schema",
                        "description": "An array of destination ports that need to match the Destination port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                4500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationPort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                4500
                            ]
                        }
                    },
                    "Protocol": {
                        "$id": "#/properties/Filters/items/properties/Protocol",
                        "type": "array",
                        "title": "The Protocol Schema",
                        "description": "An array of protocols that need to match the Protocol of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                6
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/Protocol/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                6
                            ]
                        }
                    },
                    "TcpFlags": {
                        "$id": "#/properties/Filters/items/properties/TcpFlags",
                        "type": "integer",
                        "title": "The Tcpflags Schema",
                        "description": "A list of TCP flags. The TCP flags set on the packet must match any flag in the list of flags provided. FIN = 0x01,SYN = 0x02,RST = 0x04,PSH = 0x08,ACK = 0x10,URG = 0x20,ECE = 0x40,CWR = 0x80. An OR of flags can be provided.",
                        "default": 0,
                        "examples": [
                            16
                        ]
                    },
                    "CaptureSingleDirectionTrafficOnly": {
                        "$id": "#/properties/Filters/items/properties/CaptureSingleDirectionTrafficOnly",
                        "type": "boolean",
                        "title": "The Capturesingledirectiontrafficonly Schema",
                        "description": "A flags which when set captures reverse traffic also.",
                        "default": false,
                        "examples": [
                            true
                        ]
                    }
                }
            }
        }
    }
}
```

## <a name="setup-packet-capture-using-powershell"></a>Nastavení zachytávání paketů pomocí prostředí PowerShell

Pokud chcete spustit a zastavit zachytávání paketů, podívejte se na příklady níže pro příkazy prostředí PowerShell. Další informace o možnostech parametrů najdete v tomto [dokumentu](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)PowerShellu.

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Spustit zachytávání paketů pro bránu sítě VPN

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Volitelný parametr **-fulltextových** lze použít k použití filtru.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Zastavení zachytávání paketů pro bránu sítě VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Spustit zachytávání paketů pro připojení brány VPN

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Volitelný parametr **-fulltextových** lze použít k použití filtru.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Zastavit zachytávání paketů u připojení brány sítě VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Klíčové aspekty

- Spuštění zachycení paketů může mít vliv na výkon. Nezapomeňte zachytávání paketů zastavit, pokud není potřeba.
- Navrhovaná minimální doba zachytávání paketů je 600 sekund. Pokud má kratší doba trvání zachytávání paketů, nesmí doplňovat kompletní data kvůli synchronizaci problémů mezi několika součástmi v cestě.
- Soubory dat zachycení paketů se generují ve formátu PCAP. K otevření souborů PCAP použijte Wireshark nebo jiné běžně dostupné aplikace.

## <a name="next-steps"></a>Další kroky

Další informace o VPN Gateway najdete v tématu [o VPN Gateway](vpn-gateway-about-vpngateways.md)
