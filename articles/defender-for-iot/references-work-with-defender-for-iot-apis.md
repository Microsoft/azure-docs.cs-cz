---
title: Práce s rozhraními API Defenderu for IoT
description: Použijte externí REST API pro přístup k datům zjištěným senzory a konzolou pro správu a provádění akcí s těmito daty.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.topic: reference
ms.service: azure
ms.openlocfilehash: 73c5d1f31d9e0651ee710593aa4e1b68fe972560
ms.sourcegitcommit: 983eb1131d59664c594dcb2829eb6d49c4af1560
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2021
ms.locfileid: "99222089"
---
# <a name="defender-for-iot-sensor-and-management-console-apis"></a>Defender pro rozhraní API pro senzory IoT a konzolu pro správu

Použijte externí REST API pro přístup k datům zjištěným senzory a konzolou pro správu a provádění akcí s těmito daty.

Připojení jsou zabezpečená přes SSL.

## <a name="getting-started"></a>Začínáme

Obecně platí, že pokud používáte externí rozhraní API v Azure Defenderu pro senzory IoT nebo místní konzolu pro správu, musíte vygenerovat přístupový token. Pro rozhraní API pro ověřování, které používáte na senzoru a místní konzolu pro správu, se tokeny nevyžadují.

Vygenerování tokenu:

1. V okně **nastavení systému** vyberte **přístupové tokeny**.
  
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-tokens.png" alt-text="Snímek obrazovky s nastavením systému Windows zvýrazněním tlačítka přístupové tokeny.":::

2. Vyberte možnost **generovat nový token**.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/new-token.png" alt-text="Vyberte tlačítko pro vygenerování nového tokenu.":::

3. Popište účel nového tokenu a vyberte **Další**.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-name.png" alt-text="Vygenerujte nový token a zadejte název integrace s ním spojenou.":::

4. Zobrazí se přístupový token. Zkopírujte, protože se znovu nezobrazí.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-code.png" alt-text="Zkopírujte přístupový token pro integraci.":::

5. Vyberte **Dokončit**. Tokeny, které vytvoříte, se zobrazí v dialogovém okně **přístupové tokeny** .
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-token-window.png" alt-text="Snímek obrazovky s dialogovým oknem tokeny zařízení s vyplněnými tokeny":::

   **Používá** se k poslednímu přijetí externího volání s tímto tokenem.

   Pokud se v poli **použito** pro tento token zobrazuje není **N/a** , připojení mezi senzorem a připojeným serverem nefunguje.

6. Přidejte k žádosti záhlaví HTTP s názvem **authorization** a nastavte jeho hodnotu na token, který jste vygenerovali.

## <a name="sensor-api-specifications"></a>Specifikace rozhraní API pro senzory

Tato část popisuje následující rozhraní API snímače:

- [Načíst informace o zařízení –/API/v1/Devices](#retrieve-device-information---apiv1devices)

- [Načíst informace o připojení zařízení –/API/v1/Devices/Connections](#retrieve-device-connection-information---apiv1devicesconnections)

- [Načtení informací o CVEs-/API/v1/Devices/CVEs](#retrieve-information-on-cves---apiv1devicescves)

- [Načíst informace o výstraze –/API/v1/Alerts](#retrieve-alert-information---apiv1alerts)

- [Načtení událostí časové osy –/API/v1/Events](#retrieve-timeline-events---apiv1events)

- [Načíst informace o ohrožení zabezpečení –/API/v1/Reports/Vulnerabilities/Devices](#retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices)

- [Načtení slabých míst zabezpečení –/API/v1/Reports/Vulnerabilities/Security](#retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity)

- [Načtení provozní chyby zabezpečení –/API/v1/Reports/Vulnerabilities/Operational](#retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational)

- [Ověřit přihlašovací údaje uživatele –/API/External/Authentication/Validation](#validate-user-credentials---apiexternalauthenticationvalidation)

- [Změna hesla –/External/Authentication/set_password](#change-password---externalauthenticationset_password)

- [Aktualizace hesla uživatele správcem systému –/External/Authentication/set_password_by_admin](#user-password-update-by-system-admin---externalauthenticationset_password_by_admin)

### <a name="retrieve-device-information---apiv1devices"></a>Načíst informace o zařízení –/API/v1/Devices

Pomocí tohoto rozhraní API si můžete vyžádat seznam všech zařízení, která zjistil Defender pro IoT snímač.

#### <a name="method"></a>Metoda

**Čtěte**

Vyžádá seznam všech zařízení, která zjistil Defender pro IoT snímač.

#### <a name="query-parameters"></a>Parametry dotazů

- **autorizováno**: pro filtrování pouze autorizovaných a neautorizovaných zařízení.

  **Příklady**:

  `/api/v1/devices?authorized=true`

  `/api/v1/devices?authorized=false`

#### <a name="response-type"></a>Typ odpovědi

**JSON**

#### <a name="response-content"></a>Obsah odpovědi

Pole objektů JSON, které reprezentují zařízení.

#### <a name="device-fields"></a>Pole zařízení

| Název | Typ | Vynulovatelné | Seznam hodnot |
|--|--|--|--|
| **id** | Číselný | No | - |
| **IP adresy** | Pole JSON | Yes | IP adresy (můžou být víc než jedna adresa v případě internetových adres nebo zařízení se dvěma síťovými kartami) |
| **name** | Řetězec | No | - |
| **textový** | Řetězec | No | Neznámá, inženýrská stanice, PLC, HMI, historian, řadič domény, databázový server, bezdrátový přístupový bod, směrovač, přepínač, server, pracovní stanice, IP kamera, tiskárna, brána firewall, Terminálová stanice, VPN Gateway, Internet nebo vícesměrové vysílání a všesměrové vysílání |
| **macAddresses** | Pole JSON | Yes | Adresy MAC (v případě zařízení se dvěma síťovými kartami můžou být víc než jedna adresa) |
| **operatingSystem** | Řetězec | Yes | - |
| **engineeringStation** | Logická hodnota | No | True nebo false |
| **skener** | Logická hodnota | No | True nebo false |
| **cizí** | Logická hodnota | No | True nebo false |
| **dodavatelské** | Řetězec | Yes | - |
| **protokolů** | Pole JSON | Yes | Objekt protokolu |
| **Firmwar** | Pole JSON | Yes | Firmware – objekt |

#### <a name="protocol-fields"></a>Pole protokolu

| Název | Typ | Vynulovatelné | Seznam hodnot |
|--|--|--|--|
| **Název** | Řetězec | No |  |
| **Adresy** | Pole JSON | Yes | Hlavní nebo číselné hodnoty |

#### <a name="firmware-fields"></a>Pole firmwaru

| Název | Typ | Vynulovatelné | Seznam hodnot |
|--|--|--|--|
| **sér** | Řetězec | No | Není k dispozici nebo skutečná hodnota |
| **vzorový** | Řetězec | No | Není k dispozici nebo skutečná hodnota |
| **firmwareVersion** | dvojité | No | Není k dispozici nebo skutečná hodnota |
| **additionalData** | Řetězec | No | Není k dispozici nebo skutečná hodnota |
| **moduleAddress** | Řetězec | No | Není k dispozici nebo skutečná hodnota |
| **skříně** | Řetězec | No | Není k dispozici nebo skutečná hodnota |
| **slotu** | Řetězec | No | Není k dispozici nebo skutečná hodnota |
| **adresáře** | Řetězec | No | Není k dispozici nebo skutečná hodnota |

#### <a name="response-example"></a>Příklad odpovědi

```rest
[

    {
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        }
    
    ],
    
    "id": 79,
    
    "macAddresses": null,
    
    "authorized": true,
    
    "ipAddresses": [
    
        "10.4.14.102"
    
    ],
    
    "engineeringStation": false,
    
    "type": "PLC",
    
    "operatingSystem": null,
    
    "protocols": [
    
        {
        
            "addresses": [],
            
            "id": 62,
            
            "name": "Omron FINS"
        
        }
    
    ],
    
    "scanner": false
    
}

]
```

#### <a name="curl-command"></a>Příkaz cURL

| Typ | Rozhraní API | Příklad |
|--|--|--|
| GET | "Authorization-k-H" Authorization: <AUTH_TOKEN> "https://<IP_ADDRESS>/API/v1/Devices | "Authorization-k-H" autorizace: 1234b734a9244d54ab8d40aedddcabcd "https: <span> //127 <span> . 0.0.1/API/v1/zařízení? autorizováno = true |

### <a name="retrieve-device-connection-information---apiv1devicesconnections"></a>Načíst informace o připojení zařízení –/API/v1/Devices/Connections

Pomocí tohoto rozhraní API si můžete vyžádat seznam všech připojení na zařízení.

#### <a name="method"></a>Metoda

**Čtěte**

#### <a name="query-parameters"></a>Parametry dotazů

Pokud nenastavíte parametry dotazu, všechna připojení zařízení se vrátí.

**Příklad**:

`/api/v1/devices/connections`

- **deviceId**: filtrováním podle konkrétního ID zařízení zobrazíte jeho připojení.

  **Příklad**:

  `/api/v1/devices/<deviceId>/connections`

- **lastActiveInMinutes**: časový rámec od tohoto okamžiku zpět, po minutě, během kterých byla připojení aktivní.

  **Příklad**:

  `/api/v1/devices/2/connections?lastActiveInMinutes=20`

- **discoveredBefore**: filtruje jenom připojení zjištěná před určitým časem (v milisekundách, UTC).

  **Příklad**:

  `/api/v1/devices/2/connections?discoveredBefore=<epoch>`

- **discoveredAfter**: filtruje jenom připojení, která se zjistila po určitém čase (v milisekundách, UTC).

  **Příklad**:

  `/api/v1/devices/2/connections?discoveredAfter=<epoch>`

#### <a name="response-type"></a>Typ odpovědi

**JSON**

#### <a name="response-content"></a>Obsah odpovědi

Pole objektů JSON, která reprezentují připojení zařízení.

#### <a name="fields"></a>Pole

| Název | Typ | Vynulovatelné | Seznam hodnot |
|--|--|--|--|
| **firstDeviceId** | Číselný | No | - |
| **secondDeviceId** | Číselný | No | - |
| **lastSeen** | Číselný | No | Epocha (UTC) |
| **zjistil** | Číselný | No | Epocha (UTC) |
| **přístavu** | Číselné pole | No | - |
| **protokolů** | Pole JSON | No | Pole protokol |

#### <a name="protocol-field"></a>Pole protokol

| Název | Typ | Vynulovatelné | Seznam hodnot |
|--|--|--|--|
| **name** | Řetězec | No | - |
| **příkaz** | Pole řetězců | No | - |

#### <a name="response-example"></a>Příklad odpovědi

```rest
[

    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 22,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
    
        "protocols": [
        
        {
        
            name: "modbus",
            
            commands: [
            
                "Read Coils"
        
            ]
        
        },
    
        {
        
            name: "ams",
            
            commands: [
            
                "AMS Write"
        
            ]
        
        },
    
        {
        
            name: "http",
            
            commands: [
        
            ]
        
        }
    
    ]
    
    },
    
    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 23,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
        
        "protocols": [
        
            {
            
                name: "s7comm",
                
                commands: [
                
                    "Download block",
                    
                    "Upload"
            
                ]
            
            }
        
        ]
    
    }

]
```

#### <a name="curl-command"></a>Příkaz cURL

> [!div class="mx-tdBreakAll"]
> | Typ | Rozhraní API | Příklad |
> |--|--|--|
> | GET | "Authorization-k-H" Authorization: <AUTH_TOKEN> "https://<IP_ADDRESS>/API/v1/Devices/Connections | Autorizace typu kudrlinkou-k-H: 1234b734a9244d54ab8d40aedddcabcd "https:/ <span> /127.0.0.1/API/v1/Devices/Connections |
> | GET | "Authorization-k-H" Authorization: <AUTH_TOKEN> "" https://<IP_ADDRESS>/API/v1/Devices/ <deviceId> /Connections? lastActiveInMinutes =&discoveredBefore =&discoveredAfter = " | "Authorization-k-H" autorizace: 1234b734a9244d54ab8d40aedddcabcd "" https:/ <span> /127.0.0.1/API/v1/Devices/2/Connections? lastActiveInMinutes = 20&discoveredBefore = 1594550986000&discoveredAfter = 1594550986000 " |

### <a name="retrieve-information-on-cves---apiv1devicescves"></a>Načtení informací o CVEs-/API/v1/Devices/CVEs

Pomocí tohoto rozhraní API si můžete vyžádat seznam všech známých CVEs zjištěných na zařízeních v síti.

#### <a name="method"></a>Metoda

**Čtěte**

#### <a name="query-parameters"></a>Parametry dotazů

Ve výchozím nastavení toto rozhraní API poskytuje seznam všech IP adres zařízení s CVEs, které jsou až 100 CVEs pro každou IP adresu.

**Příklad**:

`/api/v1/devices/cves`

- **deviceId**: filtrujte podle konkrétní IP adresy zařízení, abyste získali až 100 CVEs na konkrétní zařízení.

  **Příklad**:

  `/api/v1/devices/<ipAddress>/cves`

- **Top**: počet CVEsů, které se mají načíst pro každou IP adresu zařízení shora skóre.

  **Příklad**:

  `/api/v1/devices/cves?top=50`

  `/api/v1/devices/<ipAddress>/cves?top=50`

#### <a name="response-type"></a>Typ odpovědi

**JSON**

#### <a name="response-content"></a>Obsah odpovědi

Pole objektů JSON, které reprezentují CVEs identifikované na IP adresách.

#### <a name="fields"></a>Pole

| Název | Typ | Vynulovatelné | Seznam hodnot |
|--|--|--|--|
| **cveId** | Řetězec | No | - |
| **Adresa** | Řetězec | No | IP adresa |
| **podtržítk** | Řetězec | No | 0,0 – 10,0 |
| **attackVector** | Řetězec | No | Síť, sousední síť, místní nebo fyzická |
| **název** | Řetězec | No | - |

#### <a name="response-example"></a>Příklad odpovědi

```rest
[

    {
    
        "cveId": "CVE-2007-0099",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Race condition in the msxml3 module in Microsoft XML Core
        
        Services 3.0, as used in Internet Explorer 6 and other
        
        applications, allows remote attackers to execute arbitrary
        
        code or cause a denial of service (application crash) via many
        
        nested tags in an XML document in an IFRAME, when synchronous
        
        document rendering is frequently disrupted with asynchronous
        
        events, as demonstrated using a JavaScript timer, which can
        
        trigger NULL pointer dereferences or memory corruption, aka
        
        \"MSXML Memory Corruption Vulnerability.\""
    
    },
    
    {
    
        "cveId": "CVE-2009-1547",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Unspecified vulnerability in Microsoft Internet Explorer 5.01
        
        SP4, 6, 6 SP1, and 7 allows remote attackers to execute
        
        arbitrary code via a crafted data stream header that triggers
        
        memory corruption, aka \"Data Stream Header Corruption
        
        Vulnerability.\""
    
    }

]
```

#### <a name="curl-command"></a>Příkaz cURL

| Typ | Rozhraní API | Příklad |
|--|--|--|
| GET | "Authorization-k-H" Authorization: <AUTH_TOKEN> "https://<IP_ADDRESS>/API/v1/Devices/CVEs | Autorizace typu kudrlinkou-k-H: 1234b734a9244d54ab8d40aedddcabcd "https:/ <span> /127.0.0.1/API/v1/Devices/CVEs |
| GET | "Authorization-k-H" autorizace: <AUTH_TOKEN> "https://<IP_ADDRESS>/API/v1/Devices/ <deviceIpAddress> /CVEs? Top = | Autorizace typu kudrlinkou-k-H: 1234b734a9244d54ab8d40aedddcabcd "https:/ <span> /127.0.0.1/API/v1/Devices/10.10.10.15/CVEs? Top = 50 |

### <a name="retrieve-alert-information---apiv1alerts"></a>Načíst informace o výstraze –/API/v1/Alerts

Pomocí tohoto rozhraní API můžete vyžádat seznam všech upozornění, které program Defender pro IoT snímač zjistil.

#### <a name="method"></a>Metoda

**Čtěte**

#### <a name="query-parameters"></a>Parametry dotazů

- **stav**: pro filtrování pouze zpracovávaných nebo nezpracovaných výstrah.

  **Příklad**:

  `/api/v1/alerts?state=handled`

- **fromTime**: můžete filtrovat výstrahy vytvořené z konkrétní doby (v milisekundách, UTC).

  **Příklad**:

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime**: pro filtrování výstrah vytvořených pouze před určitou dobou (v milisekundách, UTC).

  **Příklad**:

  `/api/v1/alerts?toTime=<epoch>`

- **typ**: pro filtrování výstrah podle konkrétního typu. Existující typy, podle kterých se mají filtrovat: Neočekávaná nová zařízení, odpojení.

  **Příklad**:

  `/api/v1/alerts?type=disconnections`

#### <a name="response-type"></a>Typ odpovědi

**JSON**

#### <a name="response-content"></a>Obsah odpovědi

Pole objektů JSON, které reprezentují výstrahy.

#### <a name="alert-fields"></a>Pole výstrah

| Název | Typ | Vynulovatelné | Seznam hodnot |
|--|--|--|--|
| **ID** | Číselný | No | - |
| **interval** | Číselný | No | Epocha (UTC) |
| **title** | Řetězec | No | - |
| **Zpráva** | Řetězec | No | - |
| **závažnost** | Řetězec | No | Upozornění, vedlejší, hlavní nebo kritická |
| **jádra** | Řetězec | No | Porušení protokolu, porušení zásad, malware, anomálie nebo provozní |
| **sourceDevice** | Číselný | Yes | ID zařízení |
| **destinationDevice** | Číselný | Yes | ID zařízení |
| **additionalInformation** | Objekt další informace | Yes | - |

#### <a name="additional-information-fields"></a>Další pole s informacemi

| Název | Typ | Vynulovatelné | Seznam hodnot |
|--|--|--|--|
| **název** | Řetězec | No | - |
| **informace** | Pole JSON | No | Řetězec |

#### <a name="response-example"></a>Příklad odpovědi

```rest
[

    {
    
        "engine": "Policy Violation",
        
        "severity": "Major",
        
        "title": "Internet Access Detected",
        
        "additionalinformation": {
        
            "information": [
            
                "170.60.50.201 over port BACnet (47808)"
            
            ],
            
            "description": "External Addresses"
        
        },
    
        "sourceDevice": null,
        
        "destinationDevice": null,
        
        "time": 1509881077000,
        
        "message": "Device 192.168.0.13 tried to access an external IP address which is an address in the Internet and is not allowed by policy. It is recommended to notify the security officer of the incident.",
        
        "id": 1
    
    },
    
    {
    
        "engine": "Protocol Violation",
        
        "severity": "Major",
        
        "title": "Illegal MODBUS Operation (Exception Raised by Master)",
        
        "sourceDevice": 3,
        
        "destinationDevice": 4,
        
        "time": 1505651605000,
        
        "message": "A MODBUS master 192.168.110.131 attempted to initiate an illegal operation.\nThe operation is considered to be illegal since it incorporated function code \#129 which should not be used by a master.\nIt is recommended to notify the security officer of the incident.",
        
        "id": 2,
        
        "additionalInformation": null,
    
    }

]

```

#### <a name="curl-command"></a>Příkaz cURL

> [!div class="mx-tdBreakAll"]
> | Typ | Rozhraní API | Příklad |
> |--|--|--|
> | GET | "Authorization-k-H" autorizace: <AUTH_TOKEN> "" https://<IP_ADDRESS>/API/v1/Alerts? State =&fromTime =&toTime =&typ = ' | "Authorization-k-H" autorizace: 1234b734a9244d54ab8d40aedddcabcd "" https:/ <span> /127.0.0.1/API/v1/Alerts? stav = neošetřený&fromTime = 1594550986000&toTime = 1594550986001&typ = odpojení |

### <a name="retrieve-timeline-events---apiv1events"></a>Načtení událostí časové osy –/API/v1/Events

Pomocí tohoto rozhraní API můžete vyžádat seznam událostí hlášených na časovou osu události.

#### <a name="method"></a>Metoda

**Čtěte**

#### <a name="query-parameters"></a>Parametry dotazů

- **minutesTimeFrame**: časový rámec od tohoto okamžiku zpět, po minutách, ve kterém byly události hlášeny.

  **Příklad**:

  `/api/v1/events?minutesTimeFrame=20`

- **typ**: pro filtrování seznamu událostí podle konkrétního typu.

  **Příklady**:

  `/api/v1/events?type=DEVICE_CONNECTION_CREATED`

  `/api/v1/events?type=REMOTE_ACCESS&minutesTimeFrame`

#### <a name="response-type"></a>Typ odpovědi

**JSON**

#### <a name="response-content"></a>Obsah odpovědi

Pole objektů JSON, které reprezentují výstrahy.

#### <a name="event-fields"></a>Pole události

| Název | Typ | Vynulovatelné | Seznam hodnot |
|--|--|--|--|--|
| **časové razítko** | Číselný | No | Epocha (UTC) |
| **title** | Řetězec | No | - |
| **závažnost** | Řetězec | No | INFORMACE, oznámení nebo upozornění |
| **Owner** | Řetězec | Yes | Pokud byla událost vytvořena ručně, bude toto pole obsahovat uživatelské jméno, které vytvořilo událost. |
| **obsah** | Řetězec | No | - |

#### <a name="response-example"></a>Příklad odpovědi

```rest
[

    {
    
        "severity": "INFO",
        
        "title": "Back to Normal",
        
        "timestamp": 1504097077000,
        
        "content": "Device 10.2.1.15 was found responsive, after being suspected as disconnected",
        
        "owner": null,
        
        "type": "BACK_TO_NORMAL"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504096909000,
        
        "content": "Device 10.2.1.15 is suspected to be disconnected (unresponsive).",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504094446000,
        
        "content": "A DNP3 Master 10.2.1.14 attempted to initiate a request which is not allowed by policy.\nThe policy indicates the allowed function codes, address ranges, point indexes and time intervals.\nIt is recommended to notify the security officer of the incident.",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "NOTICE",
        
        "title": "PLC Program Update",
        
        "timestamp": 1504094344000,
        
        "content": "Program update detected, sent from 10.2.1.25 to 10.2.1.14",
        
        "owner": null,
        
        "type": "PROGRAM_DEVICE"
    
    }

]

```

#### <a name="curl-command"></a>Příkaz cURL

| Typ | Rozhraní API | Příklad |
|--|--|--|
| GET | "Authorization-k-H" Authorization: <AUTH_TOKEN> "" https://<IP_ADDRESS>/API/v1/Events? minutesTimeFrame =&typ = " | "autorizace ze složeného k-H": 1234b734a9244d54ab8d40aedddcabcd "' https:/ <span> /127.0.0.1/API/v1/Events? minutesTimeFrame = 20&Type = DEVICE_CONNECTION_CREATED ' |

### <a name="retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices"></a>Načíst informace o ohrožení zabezpečení –/API/v1/Reports/Vulnerabilities/Devices

Toto rozhraní API použijte k vyžádání výsledků posouzení ohrožení zabezpečení pro každé zařízení.

#### <a name="method"></a>Metoda

**Čtěte**

#### <a name="response-type"></a>Typ odpovědi

**JSON**

#### <a name="response-content"></a>Obsah odpovědi

Pole objektů JSON, které reprezentují hodnocená zařízení.

Objekt zařízení obsahuje:

- Obecná data

- Skóre posouzení

- Ohrožení zabezpečení

#### <a name="device-fields"></a>Pole zařízení

| Název | Typ | Vynulovatelné | Seznam hodnot |
|--|--|--|--|
| **name** | Řetězec | No | - |
| **IP adresy** | Pole JSON | No | - |
| **securityScore** | Číselný | No | - |
| **dodavatelské** | Řetězec | Yes |  |
| **firmwareVersion** | Řetězec | Yes | - |
| **vzorový** | Řetězec | Yes | - |
| **isWirelessAccessPoint** | Logická hodnota | No | True nebo false |
| **operatingSystem** | Objekt operačního systému | Yes | - |
| **míst** | Objekt ohrožení zabezpečení | Yes | - |

#### <a name="operating-system-fields"></a>Pole operačního systému

| Název | Typ | Vynulovatelné | Seznam hodnot |
|--|--|--|--|
| **Název** | Řetězec | Yes | - |
| **Typ** | Řetězec | Yes | - |
| **Verze** | Řetězec | Yes | - |
| **latestVersion** | Řetězec | Yes | - |

#### <a name="vulnerabilities-fields"></a>Pole ohrožení zabezpečení
 
| Název | Typ | Vynulovatelné | Seznam hodnot |
|--|--|--|--|
| **antiviru** | Pole JSON | Yes | Názvy antivirové ochrany |
| **plainTextPasswords** | Pole JSON | Yes | Objekty hesla |
| **remoteAccess** | Pole JSON | Yes | Objekty vzdáleného přístupu |
| **isBackupServer** | Logická hodnota | No | True nebo false |
| **openedPorts** | Pole JSON | Yes | Otevřené objekty portu |
| **isEngineeringStation** | Logická hodnota | No | True nebo false |
| **isKnownScanner** | Logická hodnota | No | True nebo false |
| **cves** | Pole JSON | Yes | CVE – objekty |
| **Neautorizováno** | Logická hodnota | No | True nebo false |
| **malwareIndicationsDetected** | Logická hodnota | No | True nebo false |
| **weakAuthentication** | Pole JSON | Yes | Zjištěné aplikace, které používají slabé ověřování |

#### <a name="password-fields"></a>Pole hesla

| Název | Typ | Vynulovatelné | Seznam hodnot |
|--|--|--|--|
| **heslo** | Řetězec | No | - |
| **protokol** | Řetězec | No | - |
| **obsahem** | Řetězec | No | Velmi slabé, slabé, střední nebo silné |

#### <a name="remote-access-fields"></a>Pole vzdáleného přístupu

| Název | Typ | Vynulovatelné | Seznam hodnot |
|--|--|--|--|
| **přístavní** | Číselný | No | - |
| **přepravu** | Řetězec | No | Protokol TCP nebo UDP |
| **Služba** | Řetězec | No | IP adresa |
| **clientSoftware** | Řetězec | No | SSH, VNC, Vzdálená plocha nebo prohlížeč týmu |

#### <a name="open-port-fields"></a>Otevřít pole portů

| Název | Typ | Vynulovatelné | Seznam hodnot |
|--|--|--|--|
| **přístavní** | Číselný | No | - |
| **přepravu** | Řetězec | No | Protokol TCP nebo UDP |
| **protokol** | Řetězec | Yes | - |
| **isConflictingWithFirewall** | Logická hodnota | No | True nebo false |

#### <a name="cve-fields"></a>CVE – pole

| Název | Typ | Vynulovatelné | Seznam hodnot |
|--|--|--|--|
| **ID** | Řetězec | No | - |
| **podtržítk** | Číselný | No | dvojité |
| **název** | Řetězec | No | - |

#### <a name="response-example"></a>Příklad odpovědi

```rest
[

    {
    
        "name": "IED \#10",
        
        "ipAddresses": ["10.2.1.10"],
        
        "securityScore": 100,
        
        "vendor": "ABB Switzerland Ltd, Power Systems",
        
        "firmwareVersion": null,
        
        "model": null,
        
        "operatingSystem": {
        
            "name": "ABB Switzerland Ltd, Power Systems",
            
            "type": "abb",
            
            "version": null,
            
            "latestVersion": null
        
        },
        
        "vulnerabilities": {
            
        "antiViruses": [
            
        "McAfee"
            
        ],
            
        "plainTextPasswords": [
            
            {
            
                "password": "123456",
                
                "protocol": "HTTP",
                
                "lastSeen": 1462726930471,
                
                "strength": "Very Weak"
            
            }
            
        ],
            
        "remoteAccess": [
            
            {
            
                "port": 5900,
                
                "transport": "TCP",
                
                "clientSoftware": "VNC",
                
                "client": "10.2.1.20"
            
            }
            
        ],
            
        "isBackupServer": true,
            
        "openedPorts": [
            
            {
            
                "port": 445,
                
                "transport": "TCP",
                
                "protocol": "SMP Over IP",
                
                "isConflictingWithFirewall": false
            
            },
            
            {
            
                "port": 80,
                
                "transport": "TCP",
                
                "protocol": "HTTP",
                
                "isConflictingWithFirewall": false
            
            }
            
        ],
            
        "isEngineeringStation": false,
            
        "isKnownScanner": false,
            
        "cves": [
            
            {
            
                "id": "CVE-2015-6490",
                
                "score": 10,
                
                "description": "Frosty URL - Stack-based buffer overflow on Allen-Bradley MicroLogix 1100 devices before B FRN 15.000 and 1400 devices through B FRN 15.003 allows remote attackers to execute arbitrary code via unspecified vectors"
            
            },
            
            {
            
                "id": "CVE-2012-6437",
                
                "score": 10,
                
                "description": "MicroLogix 1100 and 1400 do not properly perform authentication for Ethernet firmware updates, which allows remote attackers to execute arbitrary code via a Trojan horse update image"
            
            },
            
            {
            
                "id": "CVE-2012-6440",
                
                "score": 9.3,
                
                "description": "MicroLogix 1100 and 1400 allows man-in-the-middle attackers to conduct replay attacks via HTTP traffic."
        
            }
        
        ],
        
        "isUnauthorized": false,
        
        "malwareIndicationsDetected": true
        
        }
    
    }

]

```

#### <a name="curl-command"></a>Příkaz cURL

| Typ | Rozhraní API | Příklad |
|--|--|--|
| GET | "Authorization-k-H" Authorization: <AUTH_TOKEN> "https://<IP_ADDRESS>/API/v1/Reports/Vulnerabilities/Devices | Autorizace typu kudrlinkou-k-H: 1234b734a9244d54ab8d40aedddcabcd "https:/ <span> /127.0.0.1/API/v1/Reports/Vulnerabilities/Devices |

### <a name="retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity"></a>Načtení slabých míst zabezpečení –/API/v1/Reports/Vulnerabilities/Security

Pomocí tohoto rozhraní API si můžete vyžádat výsledky obecného posouzení ohrožení zabezpečení. Toto posouzení poskytuje přehled o úrovni zabezpečení vašeho systému.

Toto posouzení je založeno na obecných síťových a systémových informacích a ne na konkrétním vyhodnocování zařízení.

#### <a name="method"></a>Metoda

**Čtěte**

#### <a name="response-type"></a>Typ odpovědi

**JSON**

#### <a name="response-content"></a>Obsah odpovědi

Objekt JSON, který představuje hodnocené výsledky. Každý klíč může mít hodnotu null. V opačném případě bude obsahovat objekt JSON s klíči, které neumožňují hodnotu null.

### <a name="result-fields"></a>Pole výsledku

**Klíče**

**unauthorizedDevices**

| Název pole | Typ | Seznam hodnot |
| ---------- | ---- | -------------- |
| **adresáře** | Řetězec | IP adresa |
| **name** | Řetězec | - |
| **firstDetectionTime** | Číselný | Epocha (UTC) |
| lastSeen | Číselný | Epocha (UTC) |

**illegalTrafficByFirewallRules**

| Název pole | Typ | Seznam hodnot |
| ---------- | ---- | -------------- |
| **WebServer** | Řetězec | IP adresa |
| **Služba** | Řetězec | IP adresa |
| **přístavní** | Číselný | - |
| **přepravu** | Řetězec | TCP, UDP nebo ICMP |

**weakFirewallRules**

| Název pole | Typ | Seznam hodnot |
| ---------- | ---- | -------------- |
| **sources** | Pole se zdroji JSON. Každý zdroj může být v libovolném ze čtyř formátů. | "Libovolná", "IP adresa (hostitel)", "z IP-na IP (rozsah)", "IP adresa, maska podsítě (síť)" |
| **cíle** | Pole JSON pro cíle. Každé umístění může být v libovolném ze čtyř formátů. | "Libovolná", "IP adresa (hostitel)", "z IP-na IP (rozsah)", "IP adresa, maska podsítě (síť)" |
| **přístavu** | Pole JSON portů v libovolném ze tří formátů | Libovolný "," port (protokol, pokud byl zjištěn) "," z portu na port (protokol, pokud byl zjištěn) " |

**accessPoints**

| Název pole | Typ | Seznam hodnot |
| ---------- | ---- | -------------- |
| **macAddress** | Řetězec | Adresa MAC |
| **dodavatelské** | Řetězec | Název dodavatele |
| **Adresa** | Řetězec | IP adresa nebo není k dispozici. |
| **name** | Řetězec | Název zařízení nebo není k dispozici. |
| **síti** | Řetězec | Ne, podezřelé nebo Ano |

**connectionsBetweenSubnets**

| Název pole | Typ | Seznam hodnot |
| ---------- | ---- | -------------- |
| **WebServer** | Řetězec | IP adresa |
| **Služba** | Řetězec | IP adresa |

**industrialMalwareIndicators**

| Název pole | Typ | Seznam hodnot |
| ---------- | ---- | -------------- |
| **detectionTime** | Číselný | Epocha (UTC) |
| **Zadaná hodnota alertmessage** | Řetězec | - |
| **název** | Řetězec | - |
| **signalizac** | Pole JSON | Názvy zařízení | 

**internetConnections**

| Název pole | Typ | Seznam hodnot |
| ---------- | ---- | -------------- |
| **internalAddress** | Řetězec | IP adresa |
| **cizí** | Logická hodnota | Ano nebo Ne | 
| **externalAddresses** | Pole JSON | IP adresa |

#### <a name="response-example"></a>Příklad odpovědi

```rest
{

    "unauthorizedDevices": [
    
        {
        
            "address": "10.2.1.14",
            
            "name": "PLC \#14",
            
            "firstDetectionTime": 1462645483000,
            
            "lastSeen": 1462645495000,
        
        }
    
    ],
    
    "redundantFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ],
    
    "connectionsBetweenSubnets": [
    
        {
        
            "server": "10.2.1.22",
            
            "client": "170.39.2.0"
        
        }
    
    ],
    
    "industrialMalwareIndications": [
    
        {
        
            "detectionTime": 1462645483000,
            
            "alertMessage": "Suspicion of Malicious Activity (Regin)",
            
            "description": "Suspicious network activity was detected. Such behavior might be attributed to the Regin malware.",
            
            "addresses": [
            
                "10.2.1.4",
                
                "10.2.1.5"
            
            ]
        
        }
    
    ],
    
    "illegalTrafficByFirewallRules": [
    
        {
        
            "server": "10.2.1.7",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.8",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.9",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        }
    
    ],
    
    "internetConnections": [
    
        {
        
            "internalAddress": "10.2.1.1",
            
            "authorized": "Yes",
            
            "externalAddresses": ["10.2.1.2",”10.2.1.3”]
        
        }
    
    ],
    
    "accessPoints": [
    
        {
        
            "macAddress": "ec:08:6b:0f:1e:22",
            
            "vendor": "TP-LINK TECHNOLOGIES",
            
            "ipAddress": "173.194.112.22",
            
            "name": "Enterprise AP",
            
            "wireless": "Yes"
        
        }
    
    ],
    
    "weakFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ]

}

```

#### <a name="curl-command"></a>Příkaz cURL

| Typ | Rozhraní API | Příklad |
|--|--|--|
| GET | "Authorization-k-H" Authorization: <AUTH_TOKEN> "https://<IP_ADDRESS>/API/v1/Reports/Vulnerabilities/Security | Autorizace typu kudrlinkou-k-H: 1234b734a9244d54ab8d40aedddcabcd "https:/ <span> /127.0.0.1/API/v1/Reports/Vulnerabilities/Security |

### <a name="retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational"></a>Načtení provozní chyby zabezpečení –/API/v1/Reports/Vulnerabilities/Operational

Pomocí tohoto rozhraní API si můžete vyžádat výsledky obecného posouzení ohrožení zabezpečení. Toto posouzení poskytuje přehled o provozním stavu vaší sítě. Vychází z obecných informací o síti a systému a nikoli na konkrétním vyhodnocování zařízení.

#### <a name="method"></a>Metoda

**Čtěte**

#### <a name="response-type"></a>Typ odpovědi

**JSON**

#### <a name="response-content"></a>Obsah odpovědi

Objekt JSON, který představuje hodnocené výsledky. Každý klíč obsahuje pole výsledků ve formátu JSON.

#### <a name="result-fields"></a>Pole výsledku

**Klíče**

**backupServer**

| Název pole | Typ | Seznam hodnot |
|--|--|--|
| **Zdrojová** | Řetězec | IP adresa |
| **tabulka** | Řetězec | IP adresa |
| **přístavní** | Číselný | - |
| **přepravu** | Řetězec | Protokol TCP nebo UDP |
| **backupMaximalInterval** | Řetězec | - |
| **lastSeenBackup** | Číselný | Epocha (UTC) |

**ipNetworks**

| Název pole | Typ | Seznam hodnot |
|--|--|--|
| **adresy** s | Číselný | - |
| **network** | Řetězec | IP adresa |
| **Vlastnost maska** | Řetězec | Maska podsítě |

**protocolProblems**

| Název pole | Typ | Seznam hodnot |
|--|--|--|
| **protokol** | Řetězec | - |
| **Adresa** | Pole JSON | IP adresy |
| **Upozornění** | Řetězec | - |
| **reportTime** | Číselný | Epocha (UTC) |

**protocolDataVolumes**

| Název pole | Typ | Seznam hodnot |
|--|--|--|
| protokol | Řetězec | - |
| svazků | Řetězec | "Volume Number MB" |

**odpojení**

| Název pole | Typ | Seznam hodnot |
|--|--|--|
| **assetAddress** | Řetězec | IP adresa |
| **zdroj prostředku** | Řetězec | - |
| **lastDetectionTime** | Číselný | Epocha (UTC) |
| **backToNormalTime** | Číselný | Epocha (UTC) |     

#### <a name="response-example"></a>Příklad odpovědi

```rest
{

    "backupServer": [
    
        {
        
            "backupMaximalInterval": "1 Hour, 29 Minutes",
            
            "source": "10.2.1.22",
            
            "destination": "170.39.2.14",
            
            "port": 10000,
            
            "transport": "TCP",
            
            "lastSeenBackup": 1462645483000
        
        }
    
    ],

    "ipNetworks": [
    
        {
        
            "addresses": "21",
            
            "network": "10.2.1.0",
            
            "mask": "255.255.255.0"
        
        },
        
        {
        
            "addresses": "3",
            
            "network": "170.39.2.0",
            
            "mask": "255.255.255.0"
        
        }
    
    ],

    "protocolProblems": [
    
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.7",
                
                "10.2.1.8"
            
            ],
            
            "alert": "Illegal DNP3 Operation",
            
            "reportTime": 1462645483000
        
        },
        
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.15"
            
            ],
            
            "alert": "Master Requested an Application Layer Confirmation",
            
            "reportTime": 1462645483000
        
        }
        
    ],

    "protocolDataVolumes": [
    
        {
        
            "protocol": "MODBUS (502)",
            
            "volume": "21.07 MB"
        
        },
        
        {
        
            "protocol": "SSH (22)",
            
            "volumn": "0.001 MB"
        
        }
    
    ],
    
    "disconnections": [
    
        {
        
            "assetAddress": "10.2.1.3",
            
            "assetName": "PLC \#3",
            
            "lastDetectionTime": 1462645483000,
            
            "backToNormalTime": 1462645484000
        
        }
    
    ]

}

```

#### <a name="curl-command"></a>Příkaz cURL

| Typ | Rozhraní API | Příklad |
|--|--|--|
| GET | "Authorization-k-H" Authorization: <AUTH_TOKEN> "https://<IP_ADDRESS>/API/v1/Reports/Vulnerabilities/Operational | Autorizace typu kudrlinkou-k-H: 1234b734a9244d54ab8d40aedddcabcd "https:/ <span> /127.0.0.1/API/v1/Reports/Vulnerabilities/Operational |

### <a name="validate-user-credentials---apiexternalauthenticationvalidation"></a>Ověřit přihlašovací údaje uživatele –/API/External/Authentication/Validation

Použijte toto rozhraní API k ověření uživatelského jména a hesla v programu IoT. S rozhraním API můžou pracovat všechny role uživatelů v rámci služby IoT.

Pro použití tohoto rozhraní API nepotřebujete k používání tohoto rozhraní API Defender pro přístup k IoT.

#### <a name="method"></a>Metoda

**POST**

#### <a name="request-type"></a>Typ žádosti

**JSON**

#### <a name="query-parameters"></a>Parametry dotazů

| **Název** | **Typ** | **Povoleno** |
|--|--|--|
| **jmen** | Řetězec | No |
| **heslo** | Řetězec | No |

#### <a name="request-example"></a>Příklad požadavku

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!"

}

```

#### <a name="response-type"></a>Typ odpovědi

**JSON**

#### <a name="response-content"></a>Obsah odpovědi

Řetězec zprávy s podrobnostmi o stavu operace:

- **Úspěch-zpráva**: ověřování bylo úspěšné.

- Chyba **-Chyba**: ověření přihlašovacích údajů se nezdařilo.

#### <a name="response-example"></a>Příklad odpovědi

```rest
response:

{

    "msg": "Authentication succeeded."

}

```

#### <a name="curl-command"></a>Příkaz cURL

| Typ | Rozhraní API | Příklad |
|--|--|--|
| GET | "Authorization-k-H" Authorization: <AUTH_TOKEN> "https://<IP_ADDRESS>/API/External/Authentication/Validation | Autorizace typu kudrlinkou-k-H: 1234b734a9244d54ab8d40aedddcabcd "https:/ <span> /127.0.0.1/API/External/Authentication/Validation |

### <a name="change-password---externalauthenticationset_password"></a>Změna hesla –/External/Authentication/set_password

Pomocí tohoto rozhraní API můžete uživatelům umožnit změnit si vlastní hesla. S rozhraním API můžou pracovat všechny role uživatelů v rámci služby IoT. Pro použití tohoto rozhraní API nepotřebujete k používání tohoto rozhraní API Defender pro přístup k IoT.

#### <a name="method"></a>Metoda

**POST**

#### <a name="request-type"></a>Typ žádosti

**JSON**

#### <a name="request-example"></a>Příklad požadavku

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>Typ odpovědi

**JSON**

#### <a name="response-content"></a>Obsah odpovědi

Řetězec zprávy s podrobnostmi o stavu operace:

- **Úspěch – zpráva**: heslo bylo nahrazeno.

- **Selhání – chyba**: ověření uživatele se nezdařilo

- **Selhání – chyba**: heslo neodpovídá zásadám zabezpečení.

#### <a name="response-example"></a>Příklad odpovědi

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Pole zařízení

| **Název** | **Typ** | **Povoleno** |
|--|--|--|
| **jmen** | Řetězec | No |
| **heslo** | Řetězec | No |
| **new_password** | Řetězec | No |

#### <a name="curl-command"></a>Příkaz cURL

| Typ | Rozhraní API | Příklad |
|--|--|--|
| POST | složené-k-d "{" username ":" <USER_NAME> "," Password ":" <CURRENT_PASSWORD> "," new_password ":" <NEW_PASSWORD> "}"-H "Content-Type: Application/JSON ' https://<IP_ADDRESS>/API/External/Authentication/set_password | složené až k-d "{" username ":" myUser "," Password ":" 1234@abcd "," new_password ":" abcd@1234 "}"-H "Content-Type: Application/JSON" https:/ <span> /127.0.0.1/API/External/Authentication/Set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Aktualizace hesla uživatele správcem systému –/External/Authentication/set_password_by_admin

Pomocí tohoto rozhraní API umožněte správcům systému měnit hesla pro zadané uživatele. V programu Defender pro role uživatelů Správce služby IoT můžete pracovat s rozhraním API. Pro použití tohoto rozhraní API nepotřebujete k používání tohoto rozhraní API Defender pro přístup k IoT.

#### <a name="method"></a>Metoda

**POST**

#### <a name="request-type"></a>Typ žádosti

**JSON**

#### <a name="request-example"></a>Příklad požadavku

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>Typ odpovědi

**JSON**

#### <a name="response-content"></a>Obsah odpovědi

Řetězec zprávy s podrobnostmi o stavu operace:

- **Úspěch – zpráva**: heslo bylo nahrazeno.

- **Selhání – chyba**: ověření uživatele se nezdařilo

- **Selhání – chyba**: uživatel neexistuje

- **Selhání – chyba**: heslo neodpovídá zásadám zabezpečení.

- **Selhání – chyba**: uživatel nemá oprávnění ke změně hesla.

#### <a name="response-example"></a>Příklad odpovědi

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>Pole zařízení

| **Název** | **Typ** | **Povoleno** |
|--|--|--|
| **admin_username** | Řetězec | No |
| **admin_password** | Řetězec | No |
| **jmen** | Řetězec | No |
| **new_password** | Řetězec | No |

#### <a name="curl-command"></a>Příkaz cURL

> [!div class="mx-tdBreakAll"]
> | Typ | Rozhraní API | Příklad |
> |--|--|--|
> | POST | složené-k-d ' {"admin_username": "<ADMIN_USERNAME>", "admin_password": "<ADMIN_PASSWORD>", "username": "<USER_NAME>", "new_password": "<NEW_PASSWORD>"} '-H ' Content-Type: Application/JSON ' https://<IP_address>/API/External/Authentication/set_password_by_admin | kudrlinkou-k-d "{" admin_user ":" adminUser "," admin_password ":" 1234@abcd "," username ":" myUser "," new_password ":" abcd@1234 "}"-H "Content-Type: Application/JSON ' https:/ <span> /127.0.0.1/API/External/Authentication/set_password_by_admin |

## <a name="on-premises-management-console-api-specifications"></a>Specifikace rozhraní API pro místní konzolu pro správu

Tato část popisuje následující místní rozhraní API konzoly pro správu:

- **/external/v1/alerts/<UUID>**

- **Vyloučení výstrah (časové období údržby)**

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="Okno vyloučení výstrahy zobrazující aktivní pravidla":::

Definujte podmínky, za kterých se výstrahy neodesílají. Například definujte a aktualizujte časy zastavení a zahájení, zařízení nebo podsítě, které mají být vyloučeny při aktivaci výstrah nebo Defender pro moduly IoT, které mají být vyloučeny. Například během časového období údržby můžete chtít zastavit doručování všech výstrah, s výjimkou upozornění na malware na důležitých zařízeních.

Rozhraní API, která tady definujete, se zobrazí v okně **vyloučení výstrah** v místní konzole pro správu jako pravidlo vyloučení jen pro čtení.

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

- **/external/authentication/validation**

- **Příklad odpovědi**

- **základě**

```rest
{
    "msg": "Authentication succeeded."
}

```

#### <a name="change-password---externalauthenticationset_password"></a>Změna hesla –/External/Authentication/set_password

Pomocí tohoto rozhraní API můžete uživatelům umožnit změnit si vlastní hesla. S rozhraním API můžou pracovat všechny role uživatelů v rámci služby IoT. Pro použití tohoto rozhraní API nepotřebujete k používání tohoto rozhraní API Defender pro přístup k IoT.

#### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Aktualizace hesla uživatele správcem systému –/External/Authentication/set_password_by_admin

Pomocí tohoto rozhraní API umožněte správcům systému měnit hesla pro konkrétní uživatele. Aplikace Defender pro role uživatelů správce IoT můžou pracovat s rozhraním API. Pro použití tohoto rozhraní API nepotřebujete k používání tohoto rozhraní API Defender pro přístup k IoT.

### <a name="retrieve-device-information---externalv1devices"></a>Načíst informace o zařízení –/External/v1/Devices

Toto rozhraní API vyžádá seznam všech zařízení zjištěných programem Defender pro senzory IoT, které jsou připojené k místní konzole pro správu.

#### <a name="method"></a>Metoda

**Čtěte**

#### <a name="response-type"></a>Typ odpovědi

**JSON**

#### <a name="response-content"></a>Obsah odpovědi

Pole objektů JSON, které reprezentují zařízení.

#### <a name="query-parameters"></a>Parametry dotazů

- **autorizováno**: pro filtrování pouze autorizovaných a neautorizovaných zařízení.

- **ID** serveru: pro filtrování pouze zařízení, která souvisí s konkrétními lokalitami.

- **zoneId**: pro filtrování pouze zařízení, která souvisí s konkrétními zónami. [1](#1)

- **sensorId**: umožňuje filtrovat jenom zařízení zjištěná konkrétními senzory. [1](#1)

###### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="1">1</a> *možná nemáte ID webu a zóny. V takovém případě se dotázat na všechna zařízení, aby se načetla lokalita a ID zóny.*

#### <a name="query-parameters-example"></a>Příklad parametrů dotazu

`/external/v1/devices?authorized=true`

`/external/v1/devices?authorized=false`

`/external/v1/devices?siteId=1,2`

`/external/v1/devices?zoneId=5,6`

`/external/v1/devices?sensorId=8`

#### <a name="device-fields"></a>Pole zařízení

| Název | Typ | Vynulovatelné | Seznam hodnot |
|--|--|--|--|
| **sensorId** | Číselný | No | - |
| **zoneId** | Číselný | Yes | - |
| **Počty** | Číselný | Yes | - |
| **IP adresy** | Pole JSON | Yes | IP adresy (můžou být víc než jedna adresa v případě internetových adres nebo zařízení se dvěma síťovými kartami) |
| **name** | Řetězec | No | - |
| **textový** | Řetězec | No | Neznámá, inženýrská stanice, PLC, HMI, historian, řadič domény, databázový server, bezdrátový přístupový bod, směrovač, přepínač, server, pracovní stanice, IP kamera, tiskárna, brána firewall, Terminálová stanice, VPN Gateway, Internet nebo vícesměrové vysílání a všesměrové vysílání |
| **macAddresses** | Pole JSON | Yes | Adresy MAC (v případě zařízení se dvěma síťovými kartami můžou být víc než jedna adresa) |
| **operatingSystem** | Řetězec | Yes | - |
| **engineeringStation** | Logická hodnota | No | True nebo false |
| **skener** | Logická hodnota | No | True nebo false |
| **cizí** | Logická hodnota | No | True nebo false |
| **dodavatelské** | Řetězec | Yes | - |
| **Protokoly** | Pole JSON | Yes | Objekt protokolu |
| **Firmwar** | Pole JSON | Yes | Firmware – objekt |

#### <a name="protocol-fields"></a>Pole protokolu

| Název | Typ | Vynulovatelné | Seznam hodnot |
|--|--|--|--|
| Name | Řetězec | No | - |
| Adresy | Pole JSON | Yes | Hlavní nebo číselné hodnoty |

#### <a name="firmware-fields"></a>Pole firmwaru

| Název | Typ | Vynulovatelné | Seznam hodnot |
|--|--|--|--|
| **sér** | Řetězec | No | Není k dispozici nebo skutečná hodnota |
| **vzorový** | Řetězec | No | Není k dispozici nebo skutečná hodnota |
| **firmwareVersion** | dvojité | No | Není k dispozici nebo skutečná hodnota |
| **additionalData** | Řetězec | No | Není k dispozici nebo skutečná hodnota |
| **moduleAddress** | Řetězec | No | Není k dispozici nebo skutečná hodnota |
| **skříně** | Řetězec | No | Není k dispozici nebo skutečná hodnota |
| **slotu** | Řetězec | No | Není k dispozici nebo skutečná hodnota |
| **adresáře** | Řetězec | No | Není k dispozici nebo skutečná hodnota |

#### <a name="response-example"></a>Příklad odpovědi

```rest
[

    {
    
    "sensorId": 7,
    
    "zoneId": 1,
    
    "siteId": 1,
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    }

],

"id": 79,

"macAddresses": null,

"authorized": true,

"ipAddresses": [

    "10.4.14.102"

],

"engineeringStation": false,

"type": "PLC",

"operatingSystem": null,

"protocols": [

    {
    
        "addresses": [],
        
        "id": 62,
        
        "name": "Omron FINS"
    
    }

],

"scanner": false

}

]
```

#### <a name="curl-command"></a>Příkaz cURL

| Typ | Rozhraní API | Příklad |
|--|--|--|
| GET | "Authorization-k-H" Authorization: <AUTH_TOKEN> "" https://<>IP_ADDRESS>/External/v1/Devices? ID =&zoneId =&sensorId =&autorizován = " | "autorizace ze složeného k-H": 1234b734a9244d54ab8d40aedddcabcd "' https:/ <span> /127.0.0.1/External/v1/Devices? ID = 1&zoneId = 2&sensorId = 5&autorizován = true ' |

### <a name="retrieve-alert-information---externalv1alerts"></a>Načíst informace o výstraze –/External/v1/Alerts

Pomocí tohoto rozhraní API můžete načíst všechny nebo filtrované výstrahy z místní konzoly pro správu.

#### <a name="method"></a>Metoda

**Čtěte**

#### <a name="query-parameters"></a>Parametry dotazů

- **stav**: pro filtrování pouze zpracovávaných a nezpracovaných výstrah.

  **Příklad**:

  `/api/v1/alerts?state=handled`

- **fromTime**: můžete filtrovat výstrahy vytvořené z konkrétní doby (v milisekundách, UTC).

  **Příklad**:

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime**: pro filtrování výstrah vytvořených pouze před určitou dobou (v milisekundách, UTC).

  **Příklad**:

  `/api/v1/alerts?toTime=<epoch>`

- **ID** serveru: lokalita, na které byla výstraha zjištěna. [2](#2)

- **zoneId**: zóna, ve které byla výstraha zjištěna. [2](#2)

- **senzor**: senzor, na kterém byla výstraha zjištěna.

##### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="2">2</a> *možná nemáte ID webu a zóny. V takovém případě se dotázat na všechna zařízení, aby se načetla lokalita a ID zóny.*

#### <a name="alert-fields"></a>Pole výstrah

| Název | Typ | Vynulovatelné | Seznam hodnot |
|--|--|--|--|
| **ID** | Číselný | No | - |
| **interval** | Číselný | No | Epocha (UTC) |
| **title** | Řetězec | No | - |
| **Zpráva** | Řetězec | No | - |
| **závažnost** | Řetězec | No | Upozornění, vedlejší, hlavní nebo kritická |
| **jádra** | Řetězec | No | Porušení protokolu, porušení zásad, malware, anomálie nebo provozní |
| **sourceDevice** | Číselný | Yes | ID zařízení |
| **destinationDevice** | Číselný | Yes | ID zařízení |
| **additionalInformation** | Objekt další informace | Yes | - |

#### <a name="additional-information-fields"></a>Další pole s informacemi

| Název | Typ | Vynulovatelné | Seznam hodnot |
|--|--|--|--|
| **název** | Řetězec | No | - |
| **informace** | Pole JSON | No | Řetězec |

#### <a name="response-example"></a>Příklad odpovědi

```rest
[

    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Traffic Detected on sensor Interface",
        
        "additionalInformation": null,
        
        "sourceDevice": 0,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808245000,
        
        "sensorId": 1,
        
        "message": "The sensor resumed detecting network traffic on ens224.",
        
        "destinationDevice": 0,
        
        "id": 1,
        
        "severity": "Warning"
    
    },
    
    {
    
        "engine": "Anomaly",
        
        "handled": false,
        
        "title": "Address Scan Detected",
        
        "additionalInformation": null,
        
        "sourceDevice": 4,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808260000,
        
        "sensorId": 1,
        
        "message": "Address scan detected.\nScanning address: 10.10.10.22\nScanned subnet: 10.11.0.0/16\nScanned addresses: 10.11.1.1, 10.11.20.1, 10.11.20.10, 10.11.20.100, 10.11.20.2, 10.11.20.3, 10.11.20.4, 10.11.20.5, 10.11.20.6, 10.11.20.7...\nIt is recommended to notify the security officer of the incident.",
        
        "destinationDevice": 0,
        
        "id": 2,
        
        "severity": "Critical"
    
    },
    
    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Suspicion of Unresponsive MODBUS Device",
        
        "additionalInformation": null,
        
        "sourceDevice": 194,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808285000,
        
        "sensorId": 1,
        
        "message": "Outstation device 10.13.10.1 (Protocol Address 53) seems to be unresponsive to MODBUS requests.",
        
        "destinationDevice": 0,
        
        "id": 3,
        
        "severity": "Minor"
    
    }
  
]
```

#### <a name="curl-command"></a>Příkaz cURL

> [!div class="mx-tdBreakAll"]
> | Typ | Rozhraní API | Příklad |
> |--|--|--|
> | GET | "Authorization-k-H" Authorization: <AUTH_TOKEN> "" https://<>IP_ADDRESS>/External/v1/Alerts? State =&zoneId =&fromTime =&toTime =&ID =&snímač = ' | "Authorization-k-H" autorizace: 1234b734a9244d54ab8d40aedddcabcd "" https:/ <span> /127.0.0.1/External/v1/Alerts? State = unošetřená&zoneId = 1&fromTime = 0&toTime = 1594551777000&ID = 1&senzor = 1 |

### <a name="qradar-alerts"></a>QRadar výstrahy

QRadar Integration with Defender for IoT vám pomůže identifikovat výstrahy vygenerované v programu Defender pro IoT a provádět akce s těmito výstrahami. QRadar přijímá data z Defenderu pro IoT a potom kontaktuje místní součást místní konzoly pro správu rozhraní API.

Chcete-li odeslat data zjištěná v programu Defender pro IoT do QRadar, definujte pravidlo předávání v programu Defender pro systém IoT a vyberte možnost **vzdáleného zpracování výstrah podpory** .

:::image type="content" source="media/references-work-with-defender-for-iot-apis/edit-forwarding-rules.png" alt-text="Upravte pravidla předávání tak, aby odpovídala vašim potřebám.":::

Když vyberete tuto možnost během procesu konfigurace předávacích pravidel, zobrazí se v QRadar následující další pole:

- **UUID**: jedinečný identifikátor výstrahy, například 1-1555245116250.

- **Lokalita**: lokalita, na které byla výstraha zjištěna.

- **Zóna**: zóna, ve které byla výstraha zjištěna.

Příklad datové části odeslané do QRadar:

```
<9>May 5 12:29:23 sensor_Agent LEEF:1.0|CyberX|CyberX platform|2.5.0|CyberX platform Alert|devTime=May 05 2019 15:28:54 devTimeFormat=MMM dd yyyy HH:mm:ss sev=2 cat=XSense Alerts title=Device is Suspected to be Disconnected (Unresponsive) score=81 reporter=192.168.219.50 rta=0 alertId=6 engine=Operational senderName=sensor Agent UUID=5-1557059334000 site=Site zone=Zone actions=handle dst=192.168.2.2 dstName=192.168.2.2 msg=Device 192.168.2.2 is suspected to be disconnected (unresponsive).
```

#### <a name="externalv1alertsltuuidgt"></a>/External/v1/Alerts/ &lt; UUID&gt;

#### <a name="method"></a>Metoda

**PUT**

#### <a name="request-type"></a>Typ žádosti

**JSON**

#### <a name="request-content"></a>Obsah žádosti

Objekt JSON, který představuje akci, která má být provedena u výstrahy obsahující identifikátor UUID.

#### <a name="action-fields"></a>Pole akcí

| Název | Typ | Vynulovatelné | Seznam hodnot |
|--|--|--|--|
| **akce** | Řetězec | No | popisovač nebo handleAndLearn |

#### <a name="request-example"></a>Příklad požadavku

```rest
{
    "action": "handle"
}

```

#### <a name="response-type"></a>Typ odpovědi

**JSON**

#### <a name="response-content"></a>Obsah odpovědi

Pole objektů JSON, které reprezentují zařízení.

#### <a name="response-fields"></a>Pole odpovědi


| Název | Typ | Vynulovatelné | Description |
|--|--|--|--|
| **obsah/chyba** | Řetězec | No | Pokud je požadavek úspěšný, zobrazí se vlastnost obsah. V opačném případě se zobrazí vlastnost Error. |

#### <a name="possible-content-values"></a>Možné hodnoty obsahu

| Stavový kód | Hodnota obsahu | Description |
|--|--|--|
| 200 | Žádost o aktualizaci výstrahy se úspěšně dokončila. | Žádost o aktualizaci se úspěšně dokončila. Žádné komentáře. |
| 200 | Výstraha již byla zpracována (**popisovač**). | Výstraha byla již zpracována, když byla přijata žádost o popisovač výstrahy.<br />Výstraha zůstane **zpracována**. |
| 200 | Výstraha již byla zpracována a zjištěna (**handleAndLearn**). | Výstraha již byla zpracována a zjištěna, když byla přijata žádost o **handleAndLearn** .<br />Výstraha zůstane ve stavu **handledAndLearn** . |
| 200 | Výstraha již byla zpracována (**zpracováno**).<br />Na výstraze se provedla rutina a učení (**handleAndLearn**). | Výstraha již byla zpracována, když byla přijata žádost o **handleAndLearn** .<br />Výstraha se bude **handleAndLearn**. |
| 200 | Výstraha již byla zpracována a zjištěna (**handleAndLearn**). Požadavek na ignorování popisovače. | Výstraha se už **handleAndLearn** , když se přijala žádost o zpracování výstrahy. Výstraha zůstane **handleAndLearn**. |
| 500 | Neplatná akce | Akce, která byla odeslána, není platnou akcí pro provedení výstrahy. |
| 500 | Došlo k neočekávané chybě. | Došlo k neočekávané chybě. Pokud chcete tento problém vyřešit, obraťte se na technickou podporu. |
| 500 | Požadavek nebylo možné provést, protože pro tento UUID nebylo nalezeno žádné upozornění. | Zadaný identifikátor UUID výstrahy nebyl v systému nalezen. |

#### <a name="response-example"></a>Příklad odpovědi

**Úspěšnou**

```rest
{
    "content": "Alert update request finished successfully"
}
```

**Neúspěšné**

```rest
{
    "error": "Invalid action"
}
```

#### <a name="curl-command"></a>Příkaz cURL

| Typ | Rozhraní API | Příklad |
|--|--|--|
| PUT | složené-k-X PUT-d "{" Action ":" <ACTION> "}"-H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/External/v1/Alerts/<UUID> | kudrlinkou-k-X PUT-d "{" Action ":" Handle "}-H" Authorization: 1234b734a9244d54ab8d40aedddcabcd "https:/ <span> /127.0.0.1/External/v1/Alerts/1-1594550943000 |

### <a name="alert-exclusions-maintenance-window---externalv1maintenancewindow"></a>Vyloučení výstrah (časové období údržby) –/external/v1/maintenanceWindow

Definujte podmínky, za kterých se výstrahy neodesílají. Například definujte a aktualizujte časy zastavení a zahájení, zařízení nebo podsítě, které mají být vyloučeny při aktivaci výstrah nebo Defender pro moduly IoT, které mají být vyloučeny. Například při časovém intervalu pro správu a údržbu můžete zastavit doručování výstrah všech výstrah, s výjimkou upozornění na malware na důležitých zařízeních.

Rozhraní API, která tady definujete, se zobrazí v okně **vyloučení výstrah** v místní konzole pro správu jako pravidlo vyloučení jen pro čtení.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="Okno vyloučení výstrah zobrazuje seznam všech pravidel vyloučení. ":::

#### <a name="method---post"></a>Metoda – POST

#### <a name="query-parameters"></a>Parametry dotazů

- **ticketId**: definuje ID lístku údržby v systémech uživatele.

- **TTL**: definuje hodnotu TTL (Time to Live), což je doba trvání časového období údržby během několika minut. Po časovém intervalu, který tento parametr definuje, systém automaticky spustí odesílání výstrah.

- **moduly**: definuje, ze kterého bezpečnostního modulu se výstrahy během procesu údržby potlačit:

   - ODCHYLKY

   - JINÝ

   - FUNKČNÍ

   - POLICY_VIOLATION

   - PROTOCOL_VIOLATION

- **sensorIds**: definuje, od kterého Defenderu má senzor IoT během procesu údržby potlačit výstrahy. Je to stejné ID načtené z/API/v1/Appliances (GET).

- **podsítě**: definuje, ze které podsítě se během procesu údržby mají potlačit výstrahy. Podsíť se pošle v následujícím formátu: 192.168.0.0/16.

#### <a name="error-codes"></a>Kódy chyb

- **201 (vytvořeno)**: akce byla úspěšně dokončena.

- **400 (chybný požadavek)**: zobrazí se v následujících případech:

   - Parametr **TTL** není numerický nebo není kladný.

   - Parametr **podsítí** byl definován v nesprávném formátu.

   - Chybí parametr **ticketId** .

   - Parametr **stroje** se neshoduje s existujícími moduly zabezpečení.

- **404 (Nenalezeno)**: jeden ze senzorů neexistuje.

- **409 (konflikt)**: ID lístku je propojeno s jiným otevřeným časovým obdobím údržby.

- **500 (interní chyba serveru)**: jakákoli jiná Neočekávaná chyba.

> [!NOTE]
> Ujistěte se, že ID lístku není propojeno s existujícím otevřeným oknem. Vygeneruje se následující pravidlo vyloučení: Údržba-{název tokenu}-{ID lístku}.

#### <a name="method---put"></a>Metoda – PUT

Umožňuje aktualizovat dobu trvání časového období údržby po spuštění procesu údržby změnou parametru **TTL** . Nová definice trvání přepíše předchozí.

Tato metoda je užitečná, když chcete nastavit delší dobu trvání, než je aktuálně nakonfigurovaná doba trvání.

#### <a name="query-parameters"></a>Parametry dotazů

- **ticketId**: definuje ID lístku údržby v systémech uživatele.

- **TTL**: definuje dobu trvání okna v řádu minut.

#### <a name="error-code"></a>Kód chyby

- **200 (ok)**: akce byla úspěšně dokončena.

- **400 (chybný požadavek)**: zobrazí se v následujících případech:

   - Parametr **TTL** není numerický nebo není kladný.

   - Chybí parametr **ticketId** .

   - Parametr **TTL** chybí.

- **404 (Nenalezeno)**: ID lístku není propojeno s otevřeným časovým obdobím údržby.

- **500 (interní chyba serveru)**: jakákoli jiná Neočekávaná chyba.

> [!NOTE]
> Ujistěte se, že ID lístku je propojeno s existujícím otevřeným oknem.

#### <a name="method---delete"></a>Metoda – odstranění

Zavře existující časový interval pro správu a údržbu.

#### <a name="query-parameters"></a>Parametry dotazů

- **ticketId**: zaznamená ID lístku údržby v systémech uživatele.

#### <a name="error-code"></a>Kód chyby

- **200 (ok)**: akce byla úspěšně dokončena.

- **400 (chybný požadavek)**: chybí parametr **ticketId** .

- **404 (Nenalezeno)**: ID lístku není propojeno s otevřeným časovým obdobím údržby.

- **500 (interní chyba serveru)**: jakákoli jiná Neočekávaná chyba.

> [!NOTE]
> Ujistěte se, že ID lístku je propojeno s existujícím otevřeným oknem.

#### <a name="method---get"></a>Metoda – získání

Načte protokol všech akcí otevření, zavření a aktualizace, které byly během údržby provedeny v systému. Protokol můžete načíst pouze pro časová období údržby, která byla aktivní v minulosti a byla zavřena.

#### <a name="query-parameters"></a>Parametry dotazů

- **fromDate**: filtruje protokoly z předdefinovaného data a později. Formát je 2019-12-30.

- do **data: filtruje** protokoly až do předdefinovaného data. Formát je 2019-12-30.

- **ticketId**: filtruje protokoly související s konkrétním ID lístku.

- název **tokenu**: filtruje protokoly související s konkrétním názvem tokenu.

#### <a name="error-code"></a>Kód chyby

- **200 (ok)**: akce byla úspěšně dokončena.

- **400 (chybný požadavek)**: formát data je nesprávný.

- **204 (žádný obsah)**: neexistují žádná data, která by bylo možné zobrazit.

- **500 (interní chyba serveru)**: jakákoli jiná Neočekávaná chyba.

#### <a name="response-type"></a>Typ odpovědi

**JSON**

#### <a name="response-content"></a>Obsah odpovědi

Pole objektů JSON, které reprezentují operace oken údržby

#### <a name="response-structure"></a>Struktura odpovědi

| Název | Typ | Komentář | Vynulovatelné |
|--|--|--|--|
| **Hodnotu** | Řetězec | Příklad: "2012-04-23T18:25:43.511 Z" | ne |
| **ticketId** | Řetězec | Příklad: "9a5fe99c-d914-4bda-9332-307384fe40bf" | ne |
| **token** | Řetězec | - | ne |
| **motoru** | Pole řetězců | - | ano |
| **sensorIds** | Pole řetězců | - | ano |
| **podsítě** | Pole řetězců | - | ano |
| **TTL** | Číselný | - | ano |
| **Typem operace OperationType** | Řetězec | Hodnoty jsou "otevřít", "aktualizovat" a "Zavřít". | ne |

#### <a name="curl-command"></a>Příkaz cURL

| Typ | Rozhraní API | Příklad |
|--|--|--|
| POST | složené-k-X POST-d ' {"ticketId": "<TICKET_ID>", TTL ": <TIME_TO_LIVE>," moduly ": [<ENGINE1, ENGINE2... ENGINEn>], "sensorIds": [<SENSOR_ID1, SENSOR_ID2... SENSOR_IDn>], "podsítě": [<SUBNET1, podsíť SUBNET2.... SUBNETn>]} '-H "autorizace: <AUTH_TOKEN>" https:/ <span> /127.0.0.1/External/v1/maintenanceWindow | složené-k-X POST-d "{" ticketId ":" a5fe99c-d914-4bda-9332-307384fe40bf "," TTL ":" 20 "," moduly ": [" ANOMÁLIe "];" sensorIds ": [" 5 "," 3 "]," podsítě ": [" 10.0.0.3 "]}"-H "autorizace: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/External/v1/maintenanceWindow |
| PUT | kudrlinkou-k-X PUT-d ' {"ticketId": "<TICKET_ID>", TTL ":" <TIME_TO_LIVE> "}"-H "autorizace: <AUTH_TOKEN>" https:/ <span> /127.0.0.1/External/v1/maintenanceWindow | kudrlinkou-k-X PUT-d ' {"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf", "TTL": "20"}-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/External/v1/maintenanceWindow |
| DELETE | složené až k-X DELETE-d ' {"ticketId": "<TICKET_ID>"} "-H" Authorization: <AUTH_TOKEN> "https:/ <span> /127.0.0.1/External/v1/maintenanceWindow | kudrlinkou-k-X DELETE-d ' {"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf"}-H "autorizace: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/External/v1/maintenanceWindow |
| GET | "Authorization-k-H" Authorization: <AUTH_TOKEN> "" https://<IP_ADDRESS>/external/v1/maintenanceWindow? fromDate =&do =&ticketId =&token = ' | "Authorization-k-H" autorizace: 1234b734a9244d54ab8d40aedddcabcd "" https:/ <span> /127.0.0.1/External/v1/maintenanceWindow? fromDate = 2020-01-01&do: 2020-07-14&ticketId = a5fe99c-d914-4bda-9332-307384fe40bf&tokenu = a " |

### <a name="authenticate-user-credentials---externalauthenticationvalidation"></a>Ověření přihlašovacích údajů uživatele –/External/Authentication/Validation

Pomocí tohoto rozhraní API ověříte přihlašovací údaje uživatele. S rozhraním API můžou pracovat všechny role uživatelů v rámci služby IoT. Pro použití tohoto rozhraní API nepotřebujete k používání tohoto rozhraní API Defender pro přístup k IoT.

#### <a name="method"></a>Metoda

**POST**

#### <a name="request-type"></a>Typ žádosti

**JSON**

#### <a name="request-example"></a>Příklad požadavku

```rest
request:

{

    "username": "test",

    "password": "Test12345\!"

}
```

#### <a name="response-type"></a>Typ odpovědi

**JSON**

#### <a name="response-content"></a>Obsah odpovědi

Řetězec zprávy s podrobnostmi o stavu operace:

- **Úspěch – zpráva**: ověřování bylo úspěšné.

- **Selhání – chyba**: ověření přihlašovacích údajů selhalo.

#### <a name="device-fields"></a>Pole zařízení

| **Název** | **Typ** | **Povoleno** |
|--|--|--|
| **jmen** | Řetězec | No |
| **heslo** | Řetězec | No |

#### <a name="response-example"></a>Příklad odpovědi

```rest
response:

{

    "msg": "Authentication succeeded."

}
```

#### <a name="curl-command"></a>Příkaz cURL

| Typ | Rozhraní API | Příklad |
|--|--|--|
| POST | složené – k-d "{" username ":" <USER_NAME> "," heslo ":" heslo "}" https://<IP_ADDRESS>/External/Authentication/Validation " | složené až k-d "{" username ":" myUser "," Password ":" 1234@abcd "}" https:/ <span> /127.0.0.1/External/Authentication/Validation " |

### <a name="change-password---externalauthenticationset_password"></a>Změna hesla –/External/Authentication/set_password

Pomocí tohoto rozhraní API můžete uživatelům umožnit změnit si vlastní hesla. S rozhraním API můžou pracovat všechny role uživatelů v rámci služby IoT. Pro použití tohoto rozhraní API nepotřebujete k používání tohoto rozhraní API Defender pro přístup k IoT.

#### <a name="method"></a>Metoda

**POST**

#### <a name="request-type"></a>Typ žádosti

**JSON**

#### <a name="request-example"></a>Příklad požadavku

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>Typ odpovědi

**JSON**

#### <a name="response-content"></a>Obsah odpovědi

Řetězec zprávy s podrobnostmi o stavu operace:

- **Úspěch – zpráva**: heslo bylo nahrazeno.

- **Selhání – chyba**: ověření uživatele se nezdařilo

- **Selhání – chyba**: heslo neodpovídá zásadám zabezpečení.

#### <a name="response-example"></a>Příklad odpovědi

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Pole zařízení

| **Název** | **Typ** | **Povoleno** |
|--|--|--|
| **jmen** | Řetězec | No |
| **heslo** | Řetězec | No |
| **new_password** | Řetězec | No |

#### <a name="curl-command"></a>Příkaz cURL

| Typ | Rozhraní API | Příklad |
|--|--|--|
| POST | složené-k-d "{" username ":" <USER_NAME> "," Password ":" <CURRENT_PASSWORD> "," new_password ":" <NEW_PASSWORD> "}"-H "Content-Type: Application/JSON ' https://<IP_ADDRESS>/External/Authentication/set_password | složené až k-d "{" username ":" myUser "," Password ":" 1234@abcd "," new_password ":" abcd@1234 "}"-H "Content-Type: Application/JSON" https:/ <span> /127.0.0.1/External/Authentication/Set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Aktualizace hesla uživatele správcem systému –/External/Authentication/set_password_by_admin

Pomocí tohoto rozhraní API umožněte správcům systému měnit hesla pro zadané uživatele. Aplikace Defender pro role uživatelů správce IoT můžou pracovat s rozhraním API. Pro použití tohoto rozhraní API nepotřebujete k používání tohoto rozhraní API Defender pro přístup k IoT.

#### <a name="method"></a>Metoda

**POST**

#### <a name="request-type"></a>Typ žádosti

**JSON**

#### <a name="request-example"></a>Příklad požadavku

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>Typ odpovědi

**JSON**

#### <a name="response-content"></a>Obsah odpovědi

Řetězec zprávy s podrobnostmi o stavu operace:

- **Úspěch – zpráva**: heslo bylo nahrazeno.

- **Selhání – chyba**: ověření uživatele se nezdařilo

- **Selhání – chyba**: uživatel neexistuje

- **Selhání – chyba**: heslo neodpovídá zásadám zabezpečení.

- **Selhání – chyba**: uživatel nemá oprávnění ke změně hesla.

#### <a name="response-example"></a>Příklad odpovědi

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>Pole zařízení

| **Název** | **Typ** | **Povoleno** |
|--|--|--|
| **admin_username** | Řetězec | No |
| **admin_password** | Řetězec | No |
| **jmen** | Řetězec | No |
| **new_password** | Řetězec | No |

#### <a name="curl-command"></a>Příkaz cURL

> [!div class="mx-tdBreakAll"]
> | Typ | Rozhraní API | Příklad |
> |--|--|--|
> | POST | složené-k-d ' {"admin_username": "<ADMIN_USERNAME>", "admin_password": "<ADMIN_PASSWORD>", "username": "<USER_NAME>", "new_password": "<NEW_PASSWORD>"} '-H ' Content-Type: Application/JSON ' https://<IP_address>/External/Authentication/set_password_by_admin | kudrlinkou-k-d "{" admin_user ":" adminUser "," admin_password ":" 1234@abcd "," username ":" myUser "," new_password ":" abcd@1234 "}"-H "Content-Type: Application/JSON ' https:/ <span> /127.0.0.1/External/Authentication/set_password_by_admin |

## <a name="next-steps"></a>Další kroky

- [Prošetření detekovaných senzorů v inventáři zařízení](how-to-investigate-sensor-detections-in-a-device-inventory.md)

- [Prošetření všech detekovaných podnikových senzorů v inventáři zařízení](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)
