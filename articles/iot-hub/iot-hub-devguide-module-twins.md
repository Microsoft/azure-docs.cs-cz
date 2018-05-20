---
title: Pochopení dvojčata modulu Azure IoT Hub | Microsoft Docs
description: Příručka vývojáře - dvojčata použití modulu pro synchronizaci dat stavu a konfiguraci mezi IoT Hub a zařízení
services: iot-hub
documentationcenter: .net
author: chrissie926
manager: timlt
editor: ''
ms.assetid: 8a3da072-a5bf-46e5-8de4-24cdbb2a03fa
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: menchi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1e6f52da6f7524828cef928c2ee5d5cd92a1de1e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Rady pro pochopení a použití modulu dvojčata ve IoT Hub.

Tento článek předpokládá, že jste si přečetli [pochopit a používat dvojčata zařízení IoT hub] [ lnk-devguide-device-twins] první. V IoT Hub v části každou identitu zařízení, můžete vytvořit až 20 modulu identity. Každou identitu modulu implicitně generuje dvojici modulu. Velmi podobná dvojčata zařízení, modul dvojčata jsou dokumenty JSON, které obsahují informace o stavu modulu včetně metadata, konfigurace a podmínky. Azure IoT Hub uchovává twin modul pro každý modul, který je připojen ke službě IoT Hub. 

Na straně zařízení sady SDK zařízení IoT Hub umožňují vytvořit moduly, které každý otevře připojení k nezávislé do služby IoT Hub. To umožňuje používat samostatné obory názvů pro různé součásti na vašem zařízení. Například máte prodejních počítač, který má tři různé senzorů. Každý senzor řídí různá oddělení ve vaší společnosti. Modul pro každý senzor můžete vytvořit. Tímto způsobem každé oddělení je pouze odeslat úlohy nebo přímé metody na senzoru, která budou řídit, zabraňující konflikty a chyby uživatele.

 Modul identity a modul twin poskytuje stejné funkce jako identitu zařízení a dvojče zařízení, ale v jemnějšího členitosti. Toto podrobnější umožňuje zařízením, například operační systém na základě zařízení nebo správu více komponent izolovat podmínky pro každou z těchto součástí a konfigurace zařízení firmwaru. Modul identity a modul dvojčata poskytují správu oddělené oblasti zájmu při práci s zařízení IoT, které mají modulární softwarové součásti. Jsme cílem podporuje všechny funkce twin zařízení na úrovni twin modulu pomocí modulu twin obecné dostupnosti. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Tento článek popisuje:

* Struktura twin modulu: *značky*, *požadované* a *hlášené vlastnosti*.
* Operace, které moduly a back-EndY můžete provádět na dvojčata modulu.

Odkazovat na [pokyny komunikace zařízení cloud] [ lnk-d2c-guidance] pokyny k používání hlášen vlastnostech, zpráv typu zařízení cloud nebo nahrávání souborů.
Odkazovat na [Cloud zařízení komunikace pokyny] [ lnk-c2d-guidance] pokyny k použití požadované vlastnosti, přímé metody nebo zprávy typu cloud zařízení.

## <a name="module-twins"></a>Modul dvojčata
Modul dvojčata ukládat informace týkající se modul který:

* Moduly na zařízení a služby IoT Hub můžete používat k synchronizaci modul podmínky a konfigurace.
* Back-end řešení můžete použít k dotazu a cíl dlouho běžící operace.

Životní cyklus twin modulu je propojený s odpovídající [modulu identity][lnk-identity]. Moduly dvojčata se implicitně vytváří a odstraněn, když je vytvořené nebo odstraněné v IoT Hub identitu modulu.

Modul twin je dokument JSON, který zahrnuje:

* **Značky**. Části dokumentu JSON, který může číst z a zapisovat do back-end řešení. Značky nejsou viditelné pro moduly v zařízení. Značky jsou nastavené pro dotazování účel.
* **Požadovaného vlastnosti**. Používat společně s hlášené vlastnosti k synchronizaci konfigurace modulu nebo podmínky. Back-end řešení můžete nastavit požadované vlastnosti a aplikace modul může číst. Modul aplikace můžete také získat oznámení změn v požadované vlastnosti.
* **Hlášené vlastnosti**. Používat společně s požadované vlastnosti pro synchronizaci konfigurace modulu nebo podmínky. Modul aplikace můžete nastavit hlášené vlastnosti, a back-end řešení může číst a dotazujte je.
* **Vlastnosti modulu identity**. Kořen dokumentu JSON twin modul obsahuje vlastnosti jen pro čtení z odpovídající identity modulu uložené v [registru identit][lnk-identity].

![][img-module-twin]

Následující příklad ukazuje modulu twin dokumentu JSON:

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
            }
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

V kořenový objekt jsou modul Vlastnosti identity a kontejner objektů pro `tags` a obě `reported` a `desired` vlastnosti. `properties` Kontejner obsahuje některé prvky jen pro čtení (`$metadata`, `$etag`, a `$version`) popsané v [metadata modulu twin] [ lnk-module-twin-metadata] a [ Optimistickou metodu souběžného] [ lnk-concurrency] oddíly.

### <a name="reported-property-example"></a>Příklad hlášené vlastnost
V předchozím příkladu obsahuje twin modulu `batteryLevel` vlastnosti, který je hlášen modulu aplikace. Tato vlastnost umožňuje dotazování a pracovat na moduly založené na poslední hlášené stav baterie. Další příklady zahrnují možnosti vytváření sestav modulu modulu aplikace nebo možnosti připojení.

> [!NOTE]
> Hlášené vlastnosti zjednodušit scénáře, kde je back-end řešení zájem o poslední známé hodnotu vlastnosti. Použití [zpráv typu zařízení cloud] [ lnk-d2c] Pokud back-end řešení potřebuje ke zpracování modulu telemetrie ve formě pořadí označen časovým razítkem události, jako je například časové řady.

### <a name="desired-property-example"></a>Příklad požadovanou vlastnost
V předchozím příkladu `telemetryConfig` potřeby twin modulu a hlášené vlastnosti tak, že back-end řešení a aplikace modul slouží k synchronizaci telemetrická data konfigurace pro tento modul. Příklad:

1. Back-end řešení Nastaví požadovanou vlastnost s hodnotou požadované konfigurace. Zde je část dokumentu sadou požadovanou vlastnost:

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

2. Modul aplikace obdrží oznámení změny okamžitě, pokud připojení nebo při prvním volání metody reconnect. Modul aplikace hlásí aktualizovanou konfiguraci (nebo podmínku chyby pomocí `status` vlastnost). Zde je část hlášené vlastnosti:

    ```json
    ...
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ...
    ```

3. Back-end řešení můžete výsledky operace konfigurace sledovat napříč mnoha modulů pomocí [dotazování] [ lnk-query] dvojčata modulu.

> [!NOTE]
> Předchozí fragmenty kódu jsou příklady, optimalizované pro čitelnost jedním ze způsobů ke kódování konfigurace modul a jeho stav. IoT Hub nepředstavuje určité schéma pro dvojici modulu potřeby a který ohlásil vlastnosti v dvojčata modulu.
> 
> 

## <a name="back-end-operations"></a>Operace back-end
Back-end řešení funguje na twin modulu pomocí následující atomické operací vystavenou přes HTTPS:

* **Načtení modulu twin podle ID**. Tato operace vrátí modul twin dokumentu, včetně značky a vlastnosti požadované a oznámená systému.
* **Částečné aktualizace modulu twin**. Tato operace povolí back-end řešení částečně aktualizace značky nebo požadované vlastnosti v twin modulu. Částečné aktualizace je vyjádřen jako dokument JSON, který přidá nebo aktualizuje libovolné vlastnosti. Vlastnosti nastavit na `null` se odeberou. Následující příklad vytvoří novou požadovanou vlastnost s hodnotou `{"newProperty": "newValue"}`, přepíše existující hodnotu `existingProperty` s `"otherNewValue"`a také odebere `otherOldProperty`. Existující požadované vlastnosti a značky jsou provedeny žádné další změny:

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

* **Nahraďte požadované vlastnosti**. Tato operace povolí back-end řešení úplně přepsat všechny existující požadované vlastnosti a nahraďte nový dokument JSON pro `properties/desired`.
* **Nahraďte značky**. Tato operace povolí back-end řešení úplně přepsat všechny existující značky a nahraďte nový dokument JSON pro `tags`.
* **Přijímat oznámení twin**. Tato operace povoluje back-end řešení pro oznámení o změně twin. Uděláte to tak, musí vaše řešení IoT vytvořit trasu a nastavte zdroj dat na hodnotu *twinChangeEvents*. Ve výchozím nastavení žádné twin oznámení se odesílají, tedy předem neexistuje žádný takový trasy. Pokud je příliš vysoká rychlost změny, nebo z jiných důvodů, jako je například interní chyby, IoT Hub může odeslat pouze jedno oznámení, která obsahuje všechny změny. Pokud aplikace potřebuje spolehlivé auditování a protokolování všechny stavy přechodná, proto měli používat zpráv typu zařízení cloud. Oznámení twin zahrnuje vlastnosti a text.

    - Vlastnosti

    | Název | Hodnota |
    | --- | --- |
    $content – typ | application/json |
    $iothub-enqueuedtime |  Čas odeslání oznámení. |
    $iothub – zpráva – zdroj | twinChangeEvents |
    $content – kódování | utf-8 |
    deviceId | ID zařízení |
    ID modulu | ID modulu |
    hubName | Název centra IoT |
    operationTimestamp | [ISO8601] časové razítko operace |
    schéma zprávy iothub | deviceLifecycleNotification |
    opType | "replaceTwin" nebo "updateTwin" |

    Vlastnosti zprávu systému mají předponu `'$'` symbol.

    - Tělo
        
    Tato část obsahuje všechny změny twin ve formátu JSON. Používá stejný formát jako opravu, s tím rozdílem, které může obsahovat všechny části twin: značky, properties.reported, properties.desired a že obsahuje elementy "$metadata". Například:

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

Podporují všechny předchozí operace [optimistickou metodu souběžného] [ lnk-concurrency] a vyžadovat **ServiceConnect** oprávnění, jak jsou definovány v [zabezpečení] [ lnk-security] článku.

Kromě těchto operací back-end řešení může:

* Dotaz dvojčata modulu pomocí SQL like [IoT Hub dotazovací jazyk][lnk-query].

## <a name="module-operations"></a>Modul operations
Modul aplikace funguje na twin modulu pomocí následující atomické operací:

* **Načtení modulu twin**. Tato operace vrátí dokument twin modulu (včetně značky a vlastnosti požadované a oznámená systému) pro aktuálně připojený modul.
* **Částečné aktualizace hlášené vlastnosti**. Tato operace povolí částečné aktualizace hlášené vlastnosti modul aktuálně připojené. Tato operace používá stejný formát JSON aktualizace, řešení back end používá pro částečné aktualizace požadované vlastnosti.
* **Sledovat požadované vlastnosti**. Modul aktuálně připojené můžete zvolit reálném oznamovat aktualizace na požadované vlastnosti. Modul přijímá stejného formuláře aktualizace (náhrada částečně nebo zcela) provedený back-end řešení.

Předchozí operace vyžadují **ModuleConnect** oprávnění, jak jsou definovány v [zabezpečení] [ lnk-security] článku.

[Sady SDK pro zařízení Azure IoT] [ lnk-sdks] můžete snadno použít předchozí operace z mnoha jazyky a platformy.

## <a name="tags-and-properties-format"></a>Formát značky a vlastnosti
Značky, požadované vlastnosti a vlastnosti hlášené jsou objekty JSON s následujícími omezeními:

* Všechny klíče v objekty JSON jsou malá a velká písmena 64 bajtů řetězců v kódu UNICODE UTF-8. Povolené znaky vyloučit řídicí znaky UNICODE (segmenty C0 a C1), a `'.'`, `' '`, a `'$'`.
* Všechny hodnoty v objektů JSON může mít následující typy JSON: logická hodnota, číslo, řetězec, objekt. Pole nejsou povoleny. Maximální hodnota celá čísla je 4503599627370495 a-4503599627370496 je minimální hodnota celých čísel.
* Všechny objekty JSON ve značkách, požadovanou a oznámená vlastnosti může mít maximální hloubka začlenění na 5. Například následující objekt je neplatný:

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

* Všechny hodnoty řetězce může být maximálně 4 KB délku.

## <a name="module-twin-size"></a>Velikost modulu twin
IoT Hub vynucuje omezení velikosti 8KB na všech příslušných celkové hodnoty `tags`, `properties/desired`, a `properties/reported`, s výjimkou elementy jen pro čtení.
Velikost se počítá podle počítání všechny znaky, s výjimkou řídicí znaky UNICODE (segmenty C0 a C1) a prostory, které jsou mimo řetězcové konstanty.
IoT Hub s chybou odmítne všechny operace, které by zvětšete velikost tyto dokumenty nad limit.

## <a name="module-twin-metadata"></a>Metadata modulu twin
IoT Hub uchovává časové razítko poslední aktualizace pro každý objekt JSON v modulu twin potřeby a který ohlásil vlastnosti. Časová razítka v UTC a v kódování [ISO8601] formátu `YYYY-MM-DDTHH:MM:SS.mmmZ`.
Příklad:

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
            }
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": "5m",
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                }
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

Tyto informace jsou uchovávány v každé úrovni (ne jenom listy struktuře JSON) Chcete-li zachovat aktualizace, které se odebrat klíče objektu.

## <a name="optimistic-concurrency"></a>Optimistická souběžnost
Značky, potřeby a jsou uvedeny vlastnosti všech optimistickou metodu souběžného podpory.
Značky mají značku ETag dle [RFC7232], reprezentace JSON na značku, která představuje. Značky etag binárním rozsáhlým v operacích podmíněného aktualizace z back-end řešení slouží k zajištění konzistence.

Modul twin potřeby a který ohlásil vlastnosti nemají značky etag binárním rozsáhlým, ale mají `$version` hodnotu, která představuje záruku přírůstkové. Podobně jako na značku ETag verze lze aktualizace stranou Pokud chcete zajistit konzistenci aktualizací. Například modul aplikace pro hlášené vlastnost nebo řešení back-end pro požadovanou vlastnost.

Verze jsou užitečné také při observing agenta (například aplikace modul sledování požadované vlastnosti) musí sjednotit RAS mezi výsledek operace načtení a oznámení o aktualizaci. V části [tok opětovné připojení zařízení] [lnk-opětovné připojení] poskytuje další informace. 

## <a name="next-steps"></a>Další postup
Můžete vyzkoušet na některé z konceptů popsaných v tomto článku, najdete v následujících kurzech IoT Hub:

* [Začínáme s identity a modul twin serveru IoT Hub modulu pomocí rozhraní .NET zálohování a zařízení rozhraní .NET][lnk-module-twin-tutorial]

<!-- links and images -->

[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232

[lnk-module-twin-tutorial]: iot-hub-csharp-csharp-module-twin-getstarted.md
[lnk-module-twin-metadata]: iot-hub-devguide-module-twins.md#module-twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[img-module-twin]: media/iot-hub-devguide-device-twins/module-twin.jpg
