---
title: Principy dvojčat modulů Azure IoT Hub | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – použití dvojčat modulů k synchronizaci dat stavu a konfigurace mezi službou IoT Hub a vašimi zařízeními
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/01/2020
ms.author: menchi
ms.openlocfilehash: 5ef6c4de288a764abbe434c5d84fc99e154f7492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303592"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Principy a používání dvojčat modulů v centru IoT Hub

Tento článek předpokládá, že jste si přečetli [pochopit a používat dvojčata zařízení v centru IoT nejprve.](iot-hub-devguide-device-twins.md) V centru IoT Hub můžete pod každou identitou zařízení vytvořit až 20 identit modulů. Každá identita modulu implicitně generuje dvojče modulu. Podobně jako dvojčata zařízení jsou dvojčata modulu Dokumenty JSON, které ukládají informace o stavu modulu včetně metadat, konfigurací a podmínek. Azure IoT Hub udržuje dvojče modulu pro každý modul, který se připojíte k ioT hubu. 

Na straně zařízení umožňují sady SDK zařízení IoT Hub vytvářet moduly, kde každý z nich otevře nezávislé připojení k centru IoT Hub. Tato funkce umožňuje používat samostatné obory názvů pro různé součásti v zařízení. Například máte prodejní automat, který má tři různé senzory. Každý senzor je řízen různými odděleními ve vaší společnosti. Pro každý senzor můžete vytvořit modul. Tímto způsobem je každé oddělení schopno odesílat úlohy nebo přímé metody pouze senzoru, který řídí, a vyhnout se konfliktům a chybám uživatelů.

 Identita modulu a dvojče modulu poskytují stejné funkce jako identita zařízení a dvojče zařízení, ale s jemnější granularitou. Tato jemnější rozlišovací schopnost umožňuje schopným zařízením, jako jsou zařízení s operačním systémem nebo zařízení firmwaru spravující více součástí, izolovat konfiguraci a podmínky pro každou z těchto součástí. Dvojčata identity modulu a dvojčata modulů poskytují oddělení správy problémů při práci se zařízeními IoT, která mají modulární softwarové součásti. Zaměřujeme se na podporu všech funkcí dvojčete zařízení na úrovni dvojčete modulu podle všeobecné dostupnosti dvojčete modulu. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Tento článek popisuje:

* Struktura dvojčete modulu: *tagy*, *požadované* a *hlášené vlastnosti*.
* Operace, které moduly a back-endy lze provádět na dvojčata modulu.

Pokyny k používání ohlášených vlastností, zpráv mezi zařízeními a cloudem nebo nahrávání souborů naleznete v [pokynech pro komunikaci mezi zařízeními.](iot-hub-devguide-d2c-guidance.md)

Pokyny k používání požadovaných vlastností, přímých metod nebo zpráv o cloudu zařízení naleznete v [pokynech ke komunikaci](iot-hub-devguide-c2d-guidance.md) mezi cloudem.

## <a name="module-twins"></a>Dvojčata modulů

Dvojčata modulů ukládají informace týkající se modulu, které:

* Moduly na zařízení a IoT Hub lze použít k synchronizaci podmínek modulu a konfigurace.

* Back-end řešení můžete použít k dotazování a cíl dlouhotrvající operace.

Životní cyklus dvojčete modulu je propojen s odpovídající [identitou modulu](iot-hub-devguide-identity-registry.md). Dvojčata modulů jsou implicitně vytvořeny a odstraněny při vytvoření nebo odstranění identity modulu v centru IoT Hub.

Dvojče modulu je dokument JSON, který obsahuje:

* **Značky**. Část dokumentu JSON, ze které může back-end řešení číst a do které zapisovat. Značky nejsou viditelné pro moduly v zařízení. Značky jsou nastaveny pro dotazování účelu.

* **Požadované vlastnosti**. Používá se spolu s ohlášenými vlastnostmi k synchronizaci konfigurace modulu nebo podmínek. Back-end řešení můžete nastavit požadované vlastnosti a aplikace modulu můžete číst. Aplikace modulu může také přijímat oznámení o změnách v požadovaných vlastnostech.

* **Hlášené vlastnosti**. Používá se spolu s požadovanými vlastnostmi pro synchronizaci konfigurace modulu nebo podmínek. Aplikace modulu můžete nastavit hlášené vlastnosti a back-end řešení můžete číst a dotaz na ně.

* **Vlastnosti identity modulu**. Kořen dokumentu JSON dvojčete modulu obsahuje vlastnosti jen pro čtení z odpovídající identity modulu uložené v [registru identit](iot-hub-devguide-identity-registry.md).

![Architektonické znázornění dvojčete zařízení](./media/iot-hub-devguide-device-twins/module-twin.jpg)

Následující příklad ukazuje dvojče modulu Dokument JSON:

```json
{
    "deviceId": "devA",
    "moduleId": "moduleA",
    "etag": "AAAAAAAAAAc=", 
    "status": "enabled",
    "statusReason": "provisioned",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "connected",
    "lastActivityTime": "2015-02-30T16:24:48.789Z",
    "cloudToDeviceMessageCount": 0, 
    "authenticationType": "sas",
    "x509Thumbprint": {     
        "primaryThumbprint": null, 
        "secondaryThumbprint": null 
    }, 
    "version": 2, 
    "tags": {
        "$etag": "123",
        "deploymentLocation": {
            "building": "43",
            "floor": "1"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata" : {...},
            "$version": 1
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

V kořenovém objektu jsou vlastnosti identity modulu a objekty kontejneru a `tags` jak `reported` a `desired` vlastnosti. Kontejner `properties` obsahuje některé prvky`$metadata`jen `$etag`pro `$version`čtení ( , , a ) popsané v [modulem dvojče metadata](iot-hub-devguide-module-twins.md#module-twin-metadata) a [optimistické souběžnosti](iot-hub-devguide-device-twins.md#optimistic-concurrency) oddíly.

### <a name="reported-property-example"></a>Příklad hlášené vlastnosti

V předchozím příkladu dvojče modulu obsahuje `batteryLevel` vlastnost, která je hlášena aplikací modulu. Tato vlastnost umožňuje dotazovat a pracovat s moduly na základě poslední hlášené úrovně baterie. Mezi další příklady patří funkce modulu modulu pro vytváření sestav aplikace nebo možnosti připojení.

> [!NOTE]
> Hlášené vlastnosti zjednodušují scénáře, kde back-end řešení má zájem o poslední známou hodnotu vlastnosti. Zprávy [mezi zařízeními a cloudem](iot-hub-devguide-messages-d2c.md) použijte, pokud back-end řešení potřebuje ke zpracování telemetrie modulu ve formě sekvencí událostí s časovým razítkem, jako jsou časové řady.

### <a name="desired-property-example"></a>Příklad požadované vlastnosti

V předchozím příkladu `telemetryConfig` twin modulu požadované a hlášené vlastnosti jsou používány back-end řešení a aplikace modulu pro synchronizaci konfigurace telemetrie pro tento modul. Například:

1. Back-end řešení nastaví požadovanou vlastnost s požadovanou hodnotou konfigurace. Zde je část dokumentu s požadovanou sadou vlastností:

    ```json
    ...
    "desired": {
        "telemetryConfig": {
            "sendFrequency": "5m"
        },
        ...
    },
    ...
    ```

2. Aplikace modulu je upozorněna na změnu okamžitě, pokud je připojena, nebo při prvním opětovném připojení. Aplikace modulu pak hlásí aktualizovanou konfiguraci (nebo chybový stav pomocí vlastnosti). `status` Zde je část hlášené vlastnosti:

    ```json
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ```

3. Back-end řešení můžete sledovat výsledky operace konfigurace v mnoha modulech dotazováním dvojčata [modulu.](iot-hub-devguide-query-language.md)

> [!NOTE]
> Předchozí úryvky jsou příklady, optimalizované pro čitelnost, jeden způsob, jak zakódovat konfiguraci modulu a jeho stav. IoT Hub neukládá konkrétní schéma pro dvojče modulu požadované a hlášené vlastnosti v dvojčata modulu.
> 
> 

## <a name="back-end-operations"></a>Back-endové operace
Back-end řešení pracuje na dvojčeti modulu pomocí následujících atomových operací, vystavených prostřednictvím protokolu HTTPS:

* **Načíst dvojče modulu podle ID**. Tato operace vrátí dokument dvojčete modulu, včetně značek a požadovaných a oznámených vlastností systému.

* **Částečně aktualizovat modul dvojče**. Tato operace umožňuje back-endu řešení částečně aktualizovat značky nebo požadované vlastnosti v dvojčeti modulu. Částečná aktualizace je vyjádřena jako dokument JSON, který přidává nebo aktualizuje všechny vlastnosti. Vlastnosti `null` nastavené na jsou odebrány. Následující příklad vytvoří novou požadovanou `{"newProperty": "newValue"}`vlastnost s hodnotou `existingProperty` `"otherNewValue"`, přepíše `otherOldProperty`existující hodnotu with a odebere . V existujících požadovaných vlastnostech nebo značkách nejsou provedeny žádné další změny:

    ```json
    {
        "properties": {
            "desired": {
                "newProperty": {
                    "nestedProperty": "newValue"
                },
                "existingProperty": "otherNewValue",
                "otherOldProperty": null
            }
        }
    }
    ```

* **Nahraďte požadované vlastnosti**. Tato operace umožňuje back-endu řešení zcela přepsat všechny existující požadované vlastnosti a nahradit nový dokument JSON pro `properties/desired`.

* **Nahradit značky**. Tato operace umožňuje back-endu řešení zcela přepsat všechny existující značky `tags`a nahradit nový dokument JSON pro .

* **Dostávat oznámení dvojčete**. Tato operace umožňuje back-end řešení, které mají být oznámeny při změně dvojčete. Chcete-li tak učinit, vaše řešení IoT musí vytvořit trasu a nastavit zdroj dat rovná *twinChangeEvents*. Ve výchozím nastavení jsou odesílána žádná oznámení dvojčete, to znamená, že žádné takové trasy předem existují. Pokud je rychlost změny příliš vysoká nebo z jiných důvodů, jako jsou interní selhání, může centrum IoT Hub odeslat pouze jedno oznámení, které obsahuje všechny změny. Proto pokud vaše aplikace potřebuje spolehlivé auditování a protokolování všech zprostředkujících stavů, měli byste použít zprávy zařízení cloud. Dvojitá zpráva oznámení obsahuje vlastnosti a tělo.

  - Vlastnosti

    | Name (Název) | Hodnota |
    | --- | --- |
    $content typu | application/json |
    $iothub doba zařazení do fronty |  Čas odeslání oznámení |
    $iothub-zdroj zprávy | twinChangeEvents |
    kódování $content | utf-8 |
    deviceId | ID zařízení |
    modulId | ID modulu |
    název hubu | Název ioT hubu |
    operationTimestamp | [Iso8601](https://en.wikipedia.org/wiki/ISO_8601) časové razítko provozu |
    iothub-message-schéma | twinChangeNotification |
    opTyp | "replaceTwin" nebo "updateTwin" |

    Vlastnosti systému zpráv `$` jsou předponou se symbolem.

  - Tělo
        
    Tato část obsahuje všechny změny dvojčete ve formátu JSON. Používá stejný formát jako oprava, s tím rozdílem, že může obsahovat všechny oddíly dvojčete: tagy, properties.reported, properties.desired a že obsahuje prvky "$metadata". Například:

    ```json
    {
      "properties": {
          "desired": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          },
          "reported": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          }
      }
    }
    ```

Všechny předchozí operace podporují [optimistickou souběžnost](iot-hub-devguide-device-twins.md#optimistic-concurrency) a vyžadují oprávnění **ServiceConnect,** jak je definováno v [článku Řízení přístupu k centru IoT Hub.](iot-hub-devguide-security.md)

Kromě těchto operací může back-end řešení dotazovat dvojčata modulu pomocí [dotazovacího jazyka IoT Hub](iot-hub-devguide-query-language.md)podobné sql .

## <a name="module-operations"></a>Operace modulů

Aplikace modulu pracuje na dvojčeti modulu pomocí následujících atomových operací:

* **Načíst dvojče modulu**. Tato operace vrátí dokument dvojčete modulu (včetně značek a požadovaných a oznámených vlastností systému) pro aktuálně připojený modul.

* **Částečně aktualizovat hlášené vlastnosti**. Tato operace umožňuje částečnou aktualizaci vykazošených vlastností aktuálně připojeného modulu. Tato operace používá stejný formát aktualizace JSON, který back-end řešení používá pro částečnou aktualizaci požadovaných vlastností.

* **Dodržujte požadované vlastnosti**. Aktuálně připojený modul může být upozorněn na aktualizace požadovaných vlastností, když k nim dojde. Modul obdrží stejnou formu aktualizace (částečné nebo úplné nahrazení) provedené back-endem řešení.

Všechny předchozí operace vyžadují oprávnění **ModuleConnect,** jak je definováno v [článku Řízení přístupu k centru IoT Hub.](iot-hub-devguide-security.md)

Sady [SDK zařízení Azure IoT](iot-hub-devguide-sdks.md) usnadňují použití předchozích operací z mnoha jazyků a platforem.

## <a name="tags-and-properties-format"></a>Formát tagů a vlastností

Značky, požadované vlastnosti a ohlášené vlastnosti jsou objekty JSON s následujícími omezeními:

* **Klávesy**: Všechny klávesy v objektech JSON jsou rozlišování malých a velkých písmen 64 bajtů UTF-8 UNICODE řetězce. Povolené znaky vylučují řídicí znaky UNICODE `.`(segmenty `$`C0 a C1) a , SP a .

* **Hodnoty**: Všechny hodnoty v objektech JSON mohou být z následujících typů JSON: logické, číslo, řetězec, objekt. Pole nejsou povolena.

    * Celá čísla mohou mít minimální hodnotu -4503599627370496 a maximální hodnotu 4503599627370495.

    * Řetězcové hodnoty jsou kódovány UTF-8 a mohou mít maximální délku 512 bajtů.

* **Hloubka**: Všechny objekty JSON ve značkách, požadované a hlášené vlastnosti mohou mít maximální hloubku 5. Například je platný následující objekt:

    ```json
    {
        ...
        "tags": {
            "one": {
                "two": {
                    "three": {
                        "four": {
                            "five": {
                                "property": "value"
                            }
                        }
                    }
                }
            }
        },
        ...
    }
    ```

## <a name="module-twin-size"></a>Velikost dvojčete modulu

IoT Hub vynucuje limit velikosti `tags`8 kB na hodnotu , a `properties/desired` `properties/reported`32 kB limit velikosti každý na hodnotu a . Tyto součty jsou bez prvků `$etag`jen `$version`pro `$metadata/$lastUpdated`čtení, jako jsou , a .

Velikost dvojčete se vypočítá takto:

* Pro každou vlastnost v dokumentu JSON ioT Hub kumulativně vypočítá a přidá délku klíče a hodnoty vlastnosti.

* Klíče vlastností jsou považovány za řetězce kódované UTF8.

* Jednoduché hodnoty vlastností jsou považovány za řetězce kódované UTF8, číselné hodnoty (8 bajtů) nebo logické hodnoty (4 bajty).

* Velikost řetězců kódovaných UTF8 se vypočítá počítáním všech znaků s výjimkou řídicích znaků UNICODE (segmenty C0 a C1).

* Komplexní hodnoty vlastností (vnořené objekty) jsou vypočítány na základě agregační velikostklíče vlastností a hodnoty vlastností, které obsahují.

IoT Hub odmítne s chybou všechny operace, které by zvětšily velikost těchto dokumentů nad limit.

## <a name="module-twin-metadata"></a>Metadata dvojčete modulu

IoT Hub udržuje časové razítko poslední aktualizace pro každý objekt JSON v modulu twin požadované a hlášené vlastnosti. Časová razítka jsou v UTC a kódována ve `YYYY-MM-DDTHH:MM:SS.mmmZ`formátu [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) .
Například:

```json
{
    ...
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$lastUpdated": "2016-03-30T16:24:48.789Z"
                },
                "$lastUpdated": "2016-03-30T16:24:48.789Z"
            },
            "$version": 23
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": "5m",
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                },
                "batteryLevel": {
                    "$lastUpdated": "2016-04-01T16:35:48.789Z"
                },
                "$lastUpdated": "2016-04-01T16:24:48.789Z"
            },
            "$version": 123
        }
    }
    ...
}
```

Tyto informace jsou uchovávány na všech úrovních (nejen listy struktury JSON) zachovat aktualizace, které odeberou klíče objektu.

## <a name="optimistic-concurrency"></a>Optimistická souběžnost

Značky, požadované a hlášené vlastnosti podporují optimistickou souběžnost.
Značky mají etag, podle [RFC7232](https://tools.ietf.org/html/rfc7232), který představuje značku JSON reprezentace. ETags můžete použít v operacích podmíněné aktualizace z back-endu řešení k zajištění konzistence.

Modul dvojče požadované a hlášené vlastnosti nemají `$version` ETags, ale mají hodnotu, která je zaručena přírůstkové. Podobně jako ETag, verze může být použita aktualizační stranou k vynucení konzistence aktualizací. Například modulaplikace pro ohlášené vlastnosti nebo back-end řešení pro požadovanou vlastnost.

Verze jsou také užitečné, když agent pozorování (například aplikace modulu, který sleduje požadované vlastnosti) musí sladit rasy mezi výsledkem operace načtení a oznámením o aktualizaci. Další informace poskytuje část [Tok opětovného připojení zařízení.](iot-hub-devguide-device-twins.md#device-reconnection-flow) 

## <a name="next-steps"></a>Další kroky

Chcete-li vyzkoušet některé koncepty popsané v tomto článku, podívejte se na následující kurzy ioT Hub:

* [Začínáme s identitou modulu IoT Hub a dvojčetem modulu pomocí back-endu rozhraní .NET a zařízení .NET](iot-hub-csharp-csharp-module-twin-getstarted.md)
