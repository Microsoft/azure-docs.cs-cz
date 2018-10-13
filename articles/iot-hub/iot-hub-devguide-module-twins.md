---
title: Principy dvojčat modulů Azure IoT Hub | Dokumentace Microsoftu
description: Příručka pro vývojáře – použití dvojčat modulů k synchronizaci stavu a konfigurace dat mezi službou IoT Hub a zařízení
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 1b76bfded6562f61a0a9f4a870a18a1fff71bc6a
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309729"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Principy a použití dvojčat modulů ve službě IoT Hub

Tento článek předpokládá, že jste si přečetli [principy a použití dvojčat zařízení ve službě IoT Hub](iot-hub-devguide-device-twins.md) první. Ve službě IoT Hub pod každou identitu zařízení můžete vytvořit až 20 modul identity. Každý modul identity implicitně generuje dvojčete modulu. Podobně jako na dvojčata zařízení, dvojče modulu jsou dokumenty JSON, které obsahují informace o stavu modulu včetně metadata, konfigurace a podmínky. Azure IoT Hub udržuje dvojče modulu pro každého modulu, které se připojujete ke službě IoT Hub. 

Na straně zařízení se sadami SDK služby IoT Hub zařízení umožňují vytvářet moduly, kde každý z nich se otevře nezávislé připojení ke službě IoT Hub. Tato funkce umožňuje používat samostatné obory názvů pro různé součásti na vašem zařízení. Například máte prodejní počítač, který má tři různé senzory. Každý senzor se řídí různá oddělení ve vaší společnosti. Můžete vytvořit modul pro každý ze senzorů. Tímto způsobem každé oddělení je pouze možné odeslat úlohy nebo přímých metod do snímače, které se řídí, jak se vyhnout konfliktům nebo uživatelských chyb.

 Modul identity a dvojče zařízení poskytují stejné funkce jako identitu zařízení a dvojče zařízení ale podrobnější rozlišovací schopnosti. Tento rozlišovací schopnosti povolí podporuje zařízení, jako jsou založené na operační systém nebo zařízení firmwaru správy více komponenty a izolovat konfigurace a podmínky pro každou z těchto komponent. Modul identity a dvojčaty modulů poskytují při práci se zařízeními IoT, které mají modulární softwarových komponent správu oddělení oblastí zájmu. Usilujeme o na podporu všechny funkce dvojčete zařízení na úrovni dvojčete modulu podle všeobecné dostupnosti dvojčete modulu. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Tento článek popisuje:

* Struktura tohoto dvojčete modulu: *značky*, *požadované* a *ohlášené vlastnosti*.
* Operace, které může provádět moduly a back-EndY s dvojčaty modulů.

Odkazovat na [pokyny komunikace typu zařízení cloud](iot-hub-devguide-d2c-guidance.md) pokyny, pomocí ohlášených vlastností zprávy typu zařízení cloud a nahrání souboru.

Odkazovat na [pokyny komunikaci typu Cloud zařízení](iot-hub-devguide-c2d-guidance.md) informace o používání požadované vlastnosti, přímých metod nebo zprávy typu cloud zařízení.

## <a name="module-twins"></a>Dvojčaty modulů

Dvojčaty modulů ukládat informace týkající se modul, který:

* Modulů na mobilních a IoT Hub můžete použít k synchronizaci modul ujednání a konfigurace.

* Back-end řešení slouží k dotazu a cíl dlouho běžící operace.

Životní cyklus tohoto dvojčete modulu je propojený s odpovídající [modul identity](iot-hub-devguide-identity-registry.md). Dvojčaty modulů se implicitně vytvoří a odstraněn, když modul identity vytvoříte nebo odstraníte ve službě IoT Hub.

Dvojče modulu je dokument JSON, která zahrnuje:

* **Značky**. Část dokumentu JSON, který může číst z a zapisovat do back-endu řešení. Značky nejsou viditelné pro moduly na zařízení. Značky jsou nastavené pro účely dotazování.

* **Požadované vlastnosti**. Používat společně s ohlášené vlastnosti k synchronizaci konfigurace modulu nebo podmínky. Back-end řešení může nastavte požadované vlastnosti a je může číst aplikace modulu. Modul aplikace mohou také přijímat oznámení o změnách v požadované vlastnosti.

* **Ohlášené vlastnosti**. Používat společně s požadované vlastnosti pro synchronizaci konfigurace modulu nebo podmínky. Ohlášené vlastnosti můžete nastavit modul aplikace a back-end řešení může číst a jejich dotazování.

* **Vlastnosti modulu identity**. Obsahuje vlastnosti jen pro čtení z odpovídající identitu modulu, které jsou uložené v kořenu dokumentu JSON dvojčete modulu [registr identit](iot-hub-devguide-identity-registry.md).

![Architektury reprezentaci dvojčat zařízení](./media/iot-hub-devguide-device-twins/module-twin.jpg)

Následující příklad ukazuje dvojčete modulu dokumentu JSON:

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

V kořenovém objektu jsou modulu vlastnosti identity a kontejner objektů pro `tags` a obě `reported` a `desired` vlastnosti. `properties` Kontejner obsahuje některé prvky jen pro čtení (`$metadata`, `$etag`, a `$version`) podle [metadata dvojčete modulu](iot-hub-devguide-module-twins.md#module-twin-metadata) a [optimistického řízení souběžnosti](iot-hub-devguide-device-twins.md#optimistic-concurrency) oddíly.

### <a name="reported-property-example"></a>Příklad ohlášené vlastnosti

V předchozím příkladu obsahuje dvojčete modulu `batteryLevel` vlastnost, která je ohlášena modul aplikace. Tato vlastnost umožňuje dotazování a pracovat s moduly založené na poslední ohlášené baterie. Další příklady zahrnují možnosti vytváření sestav modul modul aplikace nebo možnosti připojení.

> [!NOTE]
> Ohlášené vlastnosti zjednodušení scénářů, kde je back-end řešení uvažujete o poslední známé hodnotě vlastnosti. Použití [zpráv typu zařízení cloud](iot-hub-devguide-messages-d2c.md) Pokud back-end řešení musí zpracovat modulu telemetrických dat ve formě pořadí časovým razítkem události, jako je časové řady.

### <a name="desired-property-example"></a>Příklad požadované vlastnosti

V předchozím příkladu `telemetryConfig` požadovaného dvojče zařízení a ohlášené vlastnosti tak, že back-end řešení a modul aplikace slouží k synchronizaci konfigurace telemetrie pro tento modul. Příklad:

1. Back-end řešení Nastaví požadovanou vlastnost s hodnotou požadované konfigurace. Tady je část dokumentu sadou požadované vlastnosti:

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

2. Modul aplikaci zasláno oznámení změny okamžitě, pokud připojení nebo při prvním volání metody reconnect. Modul aplikace poté podá informace aktualizovanou konfiguraci (nebo podmínku chybu pomocí `status` vlastnost). Tady je část ohlášených vlastností:

    ```json
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ```

3. Back-end řešení může sledovat výsledky operace konfigurace napříč mnoha modulů podle [dotazování](iot-hub-devguide-query-language.md) dvojčaty modulů.

> [!NOTE]
> Předchozí fragmenty jsou příklady, optimalizovaný pro čitelnost jeden způsob, jak kódovat konfiguraci modulu a její stav. IoT Hub nepředstavuje konkrétní schématu pro dvojče modulu požadovaného a ohlášené vlastnosti v dvojčaty modulů.
> 
> 

## <a name="back-end-operations"></a>Back endové operace
Back-end řešení pracuje dvojče zařízení pomocí následující atomických operací prostřednictvím protokolu HTTPS:

* **Načíst dvojče zařízení podle ID**. Tato operace vrátí dokumentu dvojčete modulu, včetně značky a požadované a ohlášené vlastnosti.

* **Částečné aktualizace dvojčete modulu**. Tato operace umožňuje back-end řešení do částečné aktualizace značek nebo požadované vlastnosti dvojčete modulu. Částečné aktualizace je vyjádřena jako dokument JSON, který přidá nebo aktualizuje všechny vlastnosti. Vlastnosti nastavené na `null` se odeberou. Následující příklad vytvoří novou požadovanou vlastnost s hodnotou `{"newProperty": "newValue"}`, přepíše stávající hodnotu `existingProperty` s `"otherNewValue"`a odebere `otherOldProperty`. Existující požadovaných vlastností nebo značky nebudou provedeny žádné další změny:

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

* **Nahraďte požadované vlastnosti**. Tato operace umožňuje back-end řešení úplně přepsat všechny existující požadované vlastnosti a nahraďte nový dokument JSON pro `properties/desired`.

* **Nahraďte značky**. Tato operace umožňuje back-end řešení úplně přepsat všechny existující značky a nahradit nový dokument JSON pro `tags`.

* **Dostávat oznámení dvojčete**. Tato operace umožňuje back-end řešení která vás upozorní, když se upraví dvojčeti. Uděláte to tak, musí vaše řešení IoT má být vytvořena trasa a nastavení zdroje dat rovná *twinChangeEvents*. Ve výchozím nastavení žádná oznámení dvojčete jsou odeslány, to znamená, předem neexistuje žádný takový trasy. Pokud je příliš vysoká frekvence změn nebo z jiných důvodů, jako je například interní chyby služby IoT Hub může odeslat pouze jedno oznámení, která obsahuje všechny změny. Proto pokud vaše aplikace potřebuje spolehlivé auditování a protokolování všech průběžných stavů, abyste používali zpráv typu zařízení cloud. Zpráva oznámení dvojčete obsahuje vlastnosti a text.

    - Vlastnosti

    | Název | Hodnota |
    | --- | --- |
    $content – typ | application/json |
    $iothub-enqueuedtime |  Čas odeslání oznámení |
    $iothub – zpráva – zdroj | twinChangeEvents |
    $content – kódování | utf-8 |
    deviceId | ID zařízení |
    ID modulu | ID modulu |
    HubName | Název služby IoT Hub |
    operationTimestamp | [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) časové razítko operace |
    schéma iothub zprávy | deviceLifecycleNotification |
    opType | "replaceTwin" nebo "updateTwin" |

    Vlastnosti zprávy systému začínají `$` symbol.

    - Tělo
        
    Tato část obsahuje všechny změny dvojčete ve formátu JSON. Používá stejný formát jako opravy, s tím rozdílem, že může obsahovat všechny oddíly dvojčete: značek, properties.reported, properties.desired a že obsahuje prvky "$metadata". Například:

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

Předchozí operace podporují [optimistického řízení souběžnosti](iot-hub-devguide-device-twins.md#optimistic-concurrency) a vyžadují **ServiceConnect** oprávnění, jak jsou definovány v [řízení přístupu ke službě IoT Hub](iot-hub-devguide-security.md) článku.

Kromě těchto operací se back-end řešení může dotazovat dvojče modulu pomocí podobném SQL [dotazovací jazyk služby IoT Hub](iot-hub-devguide-query-language.md).

## <a name="module-operations"></a>Modul operations

Modul app pracuje dvojče zařízení pomocí následující atomických operací:

* **Načíst dvojčete modulu**. Tato operace vrátí dokument dvojčete modulu (včetně značky a požadované a ohlášené vlastnosti) pro aktuálně připojený modul.

* **Částečně aktualizaci ohlášených vlastností**. Tato operace umožňuje částečnou aktualizaci ohlášených vlastností aktuálně připojeného modulu. Tato operace používá stejný formát JSON aktualizace, řešení back end použití částečné aktualizace požadované vlastnosti.

* **Sledujte požadované vlastnosti**. Můžete zvolit aktuálně připojeného modulu informováni o aktualizace požadovaných vlastností, když k nim dojde. Modul obdrží stejného formuláře aktualizace (částečné nebo úplné nahrazení) provedených back-endu řešení.

Předchozí operace vyžadují **ModuleConnect** oprávnění, jak jsou definovány v [řízení přístupu ke službě IoT Hub](iot-hub-devguide-security.md) článku.

[Sady SDK pro zařízení Azure IoT](iot-hub-devguide-sdks.md) usnadňují používání předchozí operace z mnoha jazyků a platforem.

## <a name="tags-and-properties-format"></a>Formát značky a vlastností

Ohlášené vlastnosti, značky a požadované vlastnosti jsou objekty JSON s následujícími omezeními:

* Všechny klíče v objektech JSON jsou malá a velká písmena 64 bajtů řetězce UNICODE UTF-8. Povolené znaky vyloučit řídící znaky UNICODE (segmenty C0 a C1), a `.`, SP a `$`.

* Všechny hodnoty v objektech JSON může být z následujících typů JSON: logická hodnota, číslo, řetězec, objekt. Pole nejsou povolena. Maximální hodnota celých čísel je 4503599627370495 a-4503599627370496 je minimální hodnota pro celá čísla.

* Všechny objekty JSON značky, požadovanou a ohlášené vlastnosti může mít maximální hloubky 5. Například je platný následující objekt:

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

* Všechny hodnoty řetězec může obsahovat nejvýše délce 512 bajtů.

## <a name="module-twin-size"></a>Velikost dvojčete modulu

IoT Hub vynucuje omezení velikosti 8KB na všech příslušných celkové hodnoty `tags`, `properties/desired`, a `properties/reported`, s výjimkou prvků jen pro čtení.

Velikost je vypočítán určeno spočítáním všechny znaky s výjimkou řídící znaky UNICODE (segmenty C0 a C1) a prostory, které se nachází mimo řetězcové konstanty.

IoT Hub s chybou odmítne všechny operace, které by dojít ke zvětšení těchto dokumentů přesahuje limit.

## <a name="module-twin-metadata"></a>Metadata dvojčete modulu

Služba IoT Hub udržuje požadované časové razítko poslední aktualizace pro každý objekt JSON v dvojče zařízení a ohlášené vlastnosti. Časová razítka se ve standardu UTC a zakódován do [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) formátu `YYYY-MM-DDTHH:MM:SS.mmmZ`.
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

Tyto informace se ukládají na všech úrovních (ne jenom listy strukturu JSON) Chcete-li zachovat aktualizace, které se odebrat klíče objektu.

## <a name="optimistic-concurrency"></a>Optimistická souběžnost

Značky, požadovaného a ohlášené vlastnosti všech podpora optimistické souběžnosti.
Značky mají značku ETag, jak je uvedeno [RFC7232]()https://tools.ietf.org/html/rfc7232, představující reprezentaci JSON na značku. Značek etag v operacích podmíněné aktualizace z back-end řešení můžete použít k zajištění konzistence.

Dvojče zařízení požadovaného a ohlášené vlastnosti nemají značek etag, ale jste `$version` hodnotu, která je zaručeně přírůstkové. Podobně jako na značku ETag na verzi umožňuje aktualizace stranou vynutit konzistenci aktualizací. Například modul aplikace s ohlášených vlastností nebo back-end řešení pro požadovanou vlastnost.

Verze jsou také užitečné, když observing agenta (například aplikace modulu Sledování požadované vlastnosti) musí odsouhlasit bude mezi výsledek operace načtení a oznámení o aktualizaci. V části [zařízení opětovné připojení toku](iot-hub-devguide-device-twins.md#device-reconnection-flow) poskytuje další informace. 

## <a name="next-steps"></a>Další postup

Vyzkoušet si některé koncepty popsané v tomto článku, najdete v následujících kurzech služby IoT Hub:

* [Začínáme s IoT Hub identit a modul dvojče zařízení pomocí back-endu .NET a .NET zařízení](iot-hub-csharp-csharp-module-twin-getstarted.md)
