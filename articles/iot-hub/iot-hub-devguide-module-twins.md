---
title: Principy nevláken modulu Azure IoT Hub | Microsoft Docs
description: Příručka pro vývojáře – pomocí vláken modulu můžete synchronizovat stavová a konfigurační data mezi IoT Hub a vašimi zařízeními.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 064bfd7a51f3ccb0252f37fbaa11ebc122a4b97f
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807421"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Pochopení a použití vláken v modulech v IoT Hub

Tento článek předpokládá, že jste nejdřív [pochopili a používali vlákna zařízení v IoT Hub](iot-hub-devguide-device-twins.md) . V IoT Hub v rámci každé identity zařízení můžete vytvořit až 20 identit modulu. Každá identita modulu implicitně generuje nevlákenný modul. Podobně jako u stavů zařízení jsou vlákna v modulech dokumenty JSON, které ukládají informace o stavu modulu, včetně metadat, konfigurací a podmínek. Azure IoT Hub udržuje modul pro každý modul, ke kterému se připojujete, a který se IoT Hub. 

Na straně zařízení vám sady SDK pro IoT Hub zařízení umožňují vytvářet moduly, ve kterých každá z nich otevře nezávislé připojení k IoT Hub. Tato funkce umožňuje používat samostatné obory názvů pro různé komponenty na vašem zařízení. Máte například prodejní počítač, který má tři různé senzory. Jednotlivé senzory se řídí různými odděleními ve vaší společnosti. Pro každý senzor můžete vytvořit modul. Tímto způsobem může každé oddělení odesílat úlohy nebo přímé metody do snímače, které řídí, předcházení konfliktům a chybám uživatelů.

 Identita modulu a modul s dvojitou přesností poskytují stejné možnosti jako identita zařízení a vyzdvojené zařízení, ale s jemnější členitosti. Tato přesnější členitost umožňuje zařízením, jako jsou zařízení s operačním systémem nebo zařízení firmwaru, spravovat víc komponent, izolovat konfiguraci a podmínky pro každou z těchto součástí. Vlákna identity a modulu v modulech poskytují oddělení správy ke všem problémům při práci se zařízeními IoT, které mají modulární softwarové součásti. Zaměřujeme se na podporu všech funkcí, které jsou na úrovni vlákna, na úrovni vláken v modulu, protože modul funguje se základními funkcemi. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Tento článek popisuje:

* Struktura modulu je dvojitá: *značky*, *požadované* a *hlášené vlastnosti*.
* Operace, které moduly a back-endy můžou provádět u modulu s dvojitou vlákenou.

Pokyny k používání hlášených vlastností, zpráv zařízení-Cloud nebo nahrání souborů najdete v tématu [pokyny k komunikace zařízení a cloudu](iot-hub-devguide-d2c-guidance.md) .

Pokyny k používání požadovaných vlastností, přímých metod nebo zpráv z cloudu na zařízení najdete v tématu [pokyny k komunikaci z cloudu na zařízení](iot-hub-devguide-c2d-guidance.md) .

## <a name="module-twins"></a>Dvojčaty modulů

Modul se s dvojitou čárkou ukládá informace související s modulem:

* Moduly na zařízení a IoT Hub můžou použít k synchronizaci podmínek modulu a konfigurace.

* Back-end řešení může používat dotazování a cíle dlouhotrvajících operací.

Životní cyklus modulu s dvojitou vazbou je propojen s odpovídající [identitou modulu](iot-hub-devguide-identity-registry.md). Vlákna v modulech se při vytváření nebo odstraňování identity v IoT Hub implicitně vytvářejí a odstraňují.

Nevlákenný modul je dokument JSON, který obsahuje:

* **Značky**. Oddíl dokumentu JSON, ze kterého může back-end řešení číst a zapisovat do něj. Značky nejsou pro moduly v zařízení viditelné. Značky jsou nastaveny pro účely dotazování.

* **Požadované vlastnosti**. Používá se spolu s nahlášenými vlastnostmi k synchronizaci konfigurace nebo podmínek modulu. Back-end řešení může nastavit požadované vlastnosti a aplikace modulu je může číst. Aplikace modulu může také dostávat oznámení o změnách v požadovaných vlastnostech.

* **Hlášené vlastnosti**. Používá se společně s požadovanými vlastnostmi k synchronizaci konfigurace nebo podmínek modulu. Aplikace modulu může nastavit hlášené vlastnosti a back-end řešení může číst a dotazovat je.

* **Vlastnosti identity modulu** Kořen dokumentu JSON s neuloženým modulem obsahuje vlastnosti jen pro čtení z odpovídající identity modulu uložené v [registru identit](iot-hub-devguide-identity-registry.md).

![Strukturální reprezentace zařízení s dvojitou platností](./media/iot-hub-devguide-device-twins/module-twin.jpg)

Následující příklad ukazuje nevlákenný dokument JSON modulu:

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

V kořenovém objektu jsou vlastnosti identity modulu a objekty kontejneru pro `tags` a vlastnosti `reported` a `desired`. Kontejner `properties` obsahuje některé prvky jen pro čtení (`$metadata`, `$etag`a `$version`) popsané v částech nevyhovující [metadata modulu](iot-hub-devguide-module-twins.md#module-twin-metadata) a [Optimistická souběžnost](iot-hub-devguide-device-twins.md#optimistic-concurrency) .

### <a name="reported-property-example"></a>Příklad hlášené vlastnosti

V předchozím příkladu modul obsahuje ne`batteryLevel`ou vlastnost, která je hlášena aplikací modulu. Tato vlastnost umožňuje dotazování a práci s moduly založenými na poslední hlášené úrovni baterie. Mezi další příklady patří možnosti modulu vytváření sestav modulu App Module nebo možnosti připojení.

> [!NOTE]
> Hlášené vlastnosti zjednodušují scénáře, kde back-end řešení zajímá poslední známou hodnotu vlastnosti. [Zprávy typu zařízení-Cloud](iot-hub-devguide-messages-d2c.md) použijte v případě, že back-end řešení potřebuje zpracovat telemetrii modulu ve formě sekvence událostí s časovým razítkem, jako je například časová řada.

### <a name="desired-property-example"></a>Požadovaný příklad vlastnosti

V předchozím příkladu modul `telemetryConfig` neplní požadované a hlášené vlastnosti, které jsou používány back-end řešení a aplikace modulu k synchronizaci konfigurace telemetrie pro tento modul. Například:

1. Back-end řešení nastaví požadovanou vlastnost s požadovanou konfigurační hodnotou. Tady je část dokumentu s požadovanou sadou vlastností:

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

2. Aplikace modulu se okamžitě upozorní na změnu, pokud je připojená, nebo při prvním opětovném připojení. Aplikace modulu pak nahlásí aktualizovanou konfiguraci (nebo chybovou podmínku pomocí vlastnosti `status`). Tady je část hlášených vlastností:

    ```json
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ```

3. Back-end řešení může sledovat výsledky operace konfigurace napříč mnoha moduly, protože se [dotazuje](iot-hub-devguide-query-language.md) na vlákna modulu.

> [!NOTE]
> Předchozí fragmenty kódu jsou příklady optimalizované pro čitelnost, které zakódují konfiguraci modulu a jeho stav. IoT Hub neukládá konkrétní schéma pro nedokončené a hlášené vlastnosti modulu, které jsou v modulu vlákna.
> 
> 

## <a name="back-end-operations"></a>Back-endové operace
Back-end řešení funguje v modulu s dvojím použitím následujících atomických operací, které jsou zpřístupněny prostřednictvím protokolu HTTPS:

* **Načte modul s dvojitým ID**. Tato operace vrátí nedokončený dokument modulu, včetně značek a požadovaných a hlášených systémových vlastností.

* **Částečně aktualizovat modul s dvojitou aktualizací** Tato operace umožňuje back-endu řešení částečně aktualizovat značky nebo požadované vlastnosti v modulu s dvojitou funkčností. Částečná aktualizace se vyjádří jako dokument JSON, který přidá nebo aktualizuje libovolnou vlastnost. Vlastnosti nastavené na `null` se odeberou. Následující příklad vytvoří novou požadovanou vlastnost s hodnotou `{"newProperty": "newValue"}`, přepíše existující hodnotu `existingProperty` pomocí `"otherNewValue"`a odstraní `otherOldProperty`. U stávajících požadovaných vlastností nebo značek nejsou provedeny žádné další změny:

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

* **Nahraďte požadované vlastnosti**. Tato operace umožňuje back-endu řešení úplně přepsat všechny stávající vlastnosti a nahradit nový dokument JSON pro `properties/desired`.

* **Nahraďte značky**. Tato operace umožňuje back-endu řešení úplně přepsat všechny existující značky a nahradit nový dokument JSON pro `tags`.

* **Dostávat dvojitá oznámení**. Tato operace umožňuje, aby byl back-end řešení upozorněn při změně vlákna. K tomu je potřeba, aby vaše řešení IoT vytvořilo trasu a nastavilo zdroj dat na hodnotu *twinChangeEvents*. Ve výchozím nastavení nejsou odesílána žádná nevytvořená oznámení, tj. žádné takové trasy již neexistují. Pokud je frekvence změny příliš vysoká nebo z jiných důvodů, jako jsou například vnitřní chyby, může IoT Hub odeslat pouze jedno oznámení, které obsahuje všechny změny. Proto pokud vaše aplikace potřebuje spolehlivé auditování a protokolování všech zprostředkujících stavů, měli byste použít zprávy typu zařízení-Cloud. Zpráva s dvojitým oznámením obsahuje vlastnosti a text.

  - Vlastnosti

    | Name (Název) | Hodnota |
    | --- | --- |
    $content-type | application/json |
    $iothub-enqueuedtime |  Čas odeslání oznámení |
    $iothub-Message-source | twinChangeEvents |
    $content-encoding | utf-8 |
    deviceId | ID zařízení |
    moduleId | ID modulu |
    hubName | Název IoT Hub |
    operationTimestamp | [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) časové razítko operace |
    iothub-Message-Schema | twinChangeNotification |
    opType | "replaceTwin" nebo "updateTwin" |

    Vlastnosti systému zprávy mají předponu `$` symbol.

  - Tělo
        
    Tato část obsahuje všechny zdvojené změny ve formátu JSON. Používá stejný formát jako oprava, s rozdílem, který může obsahovat všechny nedokončené oddíly: Tagy, Properties. hlášené, Properties. revisioned a obsahuje prvky "$metadata". Například:

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

Všechny předchozí operace podporují [optimistickou souběžnost](iot-hub-devguide-device-twins.md#optimistic-concurrency) a vyžadují oprávnění **ServiceConnect** , jak je definováno v tématu [řízení přístupu k IoT Hub](iot-hub-devguide-security.md) článku.

Kromě těchto operací může back-end řešení dotazovat modul na vlákna pomocí [dotazovacího jazyka IoT Hub](iot-hub-devguide-query-language.md), který se podobá jazyku SQL.

## <a name="module-operations"></a>Operace modulu

Aplikace modulu funguje v modulu s dvojitou platností pomocí následujících atomických operací:

* **Načíst nevlákenný modul**. Tato operace vrátí nedokončený dokument modulu (včetně značek a požadovaných a hlášených systémových vlastností) aktuálně připojeného modulu.

* **Částečně aktualizované hlášené vlastnosti**. Tato operace umožňuje částečnou aktualizaci hlášených vlastností aktuálně připojeného modulu. Tato operace používá stejný formát aktualizace JSON, který back-end řešení používá pro částečnou aktualizaci požadovaných vlastností.

* **Sledujte požadované vlastnosti**. V aktuálně připojeném modulu se můžete rozhodnout, že se při jejich výskytu informují aktualizace požadovaných vlastností. Modul obdrží stejnou formu aktualizace (částečnou nebo úplnou náhradou) spuštěnou back-end řešení.

Všechny předchozí operace vyžadují oprávnění **ModuleConnect** , jak je definováno v tématu [řízení přístupu k IoT Hub](iot-hub-devguide-security.md) článku.

Sady [SDK pro zařízení Azure IoT](iot-hub-devguide-sdks.md) usnadňují používání předchozích operací z mnoha jazyků a platforem.

## <a name="tags-and-properties-format"></a>Formát značek a vlastností

Značky, požadované vlastnosti a hlášené vlastnosti jsou objekty JSON s následujícími omezeními:

* Všechny klíče v objektech JSON jsou v bajtech UNICODE-8 s rozlišením velkých a malých písmen 64. Povolené znaky vyloučí řídicí znaky UNICODE (segmenty C0 a C1) a `.`, SP a `$`.

* Všechny hodnoty v objektech JSON můžou být z následujících typů JSON: Boolean, Number, String, Object. Pole nejsou povolena. Maximální hodnota pro celá čísla je 4503599627370495 a minimální hodnota pro celá čísla je-4503599627370496.

* Všechny objekty JSON ve značkách, požadované a hlášené vlastnosti mohou mít maximální hloubku 5. Například následující objekt je platný:

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

* Všechny řetězcové hodnoty můžou být dlouhé maximálně 512 bajtů.

## <a name="module-twin-size"></a>Velikost vlákna modulu

IoT Hub vynutila omezení velikosti 8 KB pro hodnotu `tags`a omezení velikosti 32 KB každou na hodnotu `properties/desired` a `properties/reported`. Tyto součty jsou výhradně prvky jen pro čtení.

Velikost je vypočítána napočítáním všech znaků, kromě řídicích znaků UNICODE (segmenty C0 a C1) a mezer, které jsou mimo řetězcové konstanty.

IoT Hub se odmítne s chybou všech operací, které by zvýšily velikost těchto dokumentů nad rámec limitu.

## <a name="module-twin-metadata"></a>Moduly s dvojitou metadaty

IoT Hub udržuje časové razítko poslední aktualizace pro každý objekt JSON v modulu, který má požadovanou a nahlášené vlastnosti. Časová razítka jsou v UTC a kódovaná ve formátu [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) `YYYY-MM-DDTHH:MM:SS.mmmZ`.
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

Tyto informace jsou uchovávány na všech úrovních (nikoli pouze v pochodech struktury JSON), aby zachovaly aktualizace, které odstraňují klíče objektu.

## <a name="optimistic-concurrency"></a>Optimistická souběžnost

Značky, požadované a hlášené vlastnosti podporují optimistickou souběžnost.
Značky mají ETag, jako na [RFC7232](https://tools.ietf.org/html/rfc7232), které představují reprezentace značky JSON. Pomocí značek ETag v operacích podmíněné aktualizace z back-endu řešení můžete zajistit konzistenci.

Nemusíte mít značky ETag v modulu a hlášené vlastnosti, ale mají hodnotu `$version`, u které je zaručená přírůstková hodnota. Podobně jako ETag může být verze využívána stranou aktualizace k vymáhání konzistence aktualizací. Například aplikace modulu pro hlášené vlastnosti nebo back-end řešení pro požadovanou vlastnost.

Verze jsou užitečné také v případě, že pozorovatelský Agent (například aplikace modulu, který je pozorování požadovaných vlastností), musí sjednotit Races mezi výsledkem operace načtení a oznámením o aktualizacích. Další informace najdete v části [tok opětovného připojení zařízení](iot-hub-devguide-device-twins.md#device-reconnection-flow) . 

## <a name="next-steps"></a>Další kroky

Některé z konceptů popsaných v tomto článku si můžete vyzkoušet v následujících kurzech IoT Hub:

* [Začínáme s identitou modulu IoT Hub a modulem s dvojitým zprovozněním pomocí back-endu .NET a zařízení .NET](iot-hub-csharp-csharp-module-twin-getstarted.md)
