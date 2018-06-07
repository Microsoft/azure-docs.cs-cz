---
title: Pochopení dvojčata zařízení Azure IoT Hub | Microsoft Docs
description: Příručka vývojáře - dvojčata zařízení použít k synchronizaci dat stavu a konfiguraci mezi IoT Hub a zařízení
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: elioda
ms.openlocfilehash: c002685dfd3b8f86a8657b5d30dee29641cef932
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632872"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Rady pro pochopení a použít dvojčata zařízení IoT hub

*Dvojčata zařízení* jsou dokumenty JSON, které obsahují informace o stavu zařízení včetně metadata, konfigurace a podmínky. Azure IoT Hub uchovává dvojče zařízení pro každé zařízení, které se připojujete ke službě IoT Hub. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Tento článek popisuje:

* Struktura dvojče zařízení: *značky*, *požadované* a *hlášené vlastnosti*.
* Operace, které aplikace pro zařízení a back-EndY můžete provádět na dvojčata zařízení.

Použijte dvojčata zařízení na:

* Metadata specifická pro zařízení ukládat v cloudu. Například nasazení umístění prodejních počítače.
* Sestavy aktuální informace o stavu, jako jsou k dispozici funkce a podmínky z vaší aplikace zařízení. Například je zařízení připojené ke službě IoT hub přes mobilní nebo Wi-Fi.
* Synchronizujte stav pracovních dlouho běžící mezi aplikace zařízení a back-end aplikace. Při řešení back end Určuje novou verzi firmwaru pro instalaci a aplikace zařízení sestavy různé fáze procesu aktualizace.
* Dotaz na vaše zařízení metadata, konfigurace nebo stavu.

Odkazovat na [pokyny komunikace zařízení cloud] [ lnk-d2c-guidance] pokyny k používání hlášen vlastnostech, zpráv typu zařízení cloud nebo nahrávání souborů.
Odkazovat na [Cloud zařízení komunikace pokyny] [ lnk-c2d-guidance] pokyny k použití požadované vlastnosti, přímé metody nebo zprávy typu cloud zařízení.

## <a name="device-twins"></a>Dvojčata zařízení
Dvojčata zařízení ukládat informace týkající se zařízení který:

* Zařízení a zpět končí můžete používat k synchronizaci zařízení podmínky a konfigurace.
* Back-end řešení můžete použít k dotazu a cíl dlouho běžící operace.

Životní cyklus dvojče zařízení je propojený s odpovídající [identitu zařízení][lnk-identity]. Dvojčata zařízení jsou implicitně vytvořena a odstranit při vytvoření identity zařízení nebo odstranit v IoT Hub.

Dvojče zařízení je dokument JSON, který zahrnuje:

* **Značky**. Části dokumentu JSON, který může číst z a zapisovat do back-end řešení. Značky nejsou viditelné pro aplikace pro zařízení.
* **Požadovaného vlastnosti**. Používat společně s hlášené vlastnosti k synchronizaci konfigurace zařízení nebo podmínky. Back-end řešení můžete nastavit požadované vlastnosti a aplikace zařízení mohou přečíst. Aplikace zařízení můžete také získat oznámení změn v požadované vlastnosti.
* **Hlášené vlastnosti**. Používat společně s požadované vlastnosti pro synchronizaci konfigurace zařízení nebo podmínky. Aplikace zařízení můžete nastavit hlášené vlastnosti, a back-end řešení může číst a dotazujte je.
* **Vlastnosti identity zařízení**. Kořen dokumentu JSON twin zařízení obsahuje vlastnosti jen pro čtení z odpovídající identitu zařízení, které jsou uložené v [registru identit][lnk-identity].

![][img-twin]

Následující příklad ukazuje dvojče zařízení dokumentu JSON:

```json
{
    "deviceId": "devA",
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

V kořenový objekt jsou zařízení vlastnosti identity a kontejner objektů pro `tags` a obě `reported` a `desired` vlastnosti. `properties` Kontejner obsahuje některé prvky jen pro čtení (`$metadata`, `$etag`, a `$version`) popsané v [metadat zařízení twin] [ lnk-twin-metadata] a [ Optimistickou metodu souběžného] [ lnk-concurrency] oddíly.

### <a name="reported-property-example"></a>Příklad hlášené vlastnost
V předchozím příkladu obsahuje dvojče zařízení `batteryLevel` vlastnosti, který je hlášen aplikace zařízení. Tato vlastnost umožňuje dotazování a provozovat na zařízení podle poslední hlášené stav baterie. Další příklady zahrnují možnosti vytváření sestav zařízení zařízení aplikace nebo možnosti připojení.

> [!NOTE]
> Hlášené vlastnosti zjednodušit scénáře, kde je back-end řešení zájem o poslední známé hodnotu vlastnosti. Použití [zpráv typu zařízení cloud] [ lnk-d2c] Pokud back-end řešení potřebuje ke zpracování telemetrie zařízení ve formě pořadí označen časovým razítkem události, jako je například časové řady.

### <a name="desired-property-example"></a>Příklad požadovanou vlastnost
V předchozím příkladu `telemetryConfig` potřeby dvojče zařízení a hlášené vlastnosti tak, že back-end řešení a aplikace zařízení slouží k synchronizaci telemetrická data konfigurace pro toto zařízení. Příklad:

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

2. Aplikace zařízení obdrží oznámení změny okamžitě, pokud připojení nebo při prvním volání metody reconnect. Aplikace zařízení hlásí aktualizovanou konfiguraci (nebo podmínku chyby pomocí `status` vlastnost). Zde je část hlášené vlastnosti:

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

3. Back-end řešení můžete výsledky operace konfigurace sledovat prostřednictvím zařízení, pomocí [dotazování] [ lnk-query] dvojčata zařízení.

> [!NOTE]
> Předchozí fragmenty kódu jsou příklady, optimalizované pro čitelnost jedním ze způsobů ke kódování konfigurace zařízení a jeho stav. IoT Hub nepředstavuje určité schéma pro potřeby dvojče zařízení a který ohlásil vlastnosti v dvojčata zařízení.
> 
> 

Dvojčata slouží k synchronizaci dlouhotrvající operace, jako je aktualizace firmwaru. Další informace o tom, jak pomocí vlastnosti synchronizovat a sledovat operace probíhající dlouhou dobu na zařízeních najdete v tématu [použití požadovaného vlastnosti pro konfiguraci zařízení][lnk-twin-properties].

## <a name="back-end-operations"></a>Operace back-end
Back-end řešení funguje na dvojče zařízení pomocí následující atomické operací vystavenou přes HTTPS:

* **Načtení dvojče zařízení podle ID**. Tato operace vrátí dokument twin zařízení, včetně značky a vlastnosti požadované a oznámená systému.
* **Částečné aktualizace dvojče zařízení**. Tato operace povolí back-end řešení částečně aktualizace značky nebo požadované vlastnosti v dvojče zařízení. Částečné aktualizace je vyjádřen jako dokument JSON, který přidá nebo aktualizuje libovolné vlastnosti. Vlastnosti nastavit na `null` se odeberou. Následující příklad vytvoří novou požadovanou vlastnost s hodnotou `{"newProperty": "newValue"}`, přepíše existující hodnotu `existingProperty` s `"otherNewValue"`a také odebere `otherOldProperty`. Existující požadované vlastnosti a značky jsou provedeny žádné další změny:

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

* Dotaz dvojčata zařízení pomocí SQL like [IoT Hub dotazovací jazyk][lnk-query].
* Provádění operací na velkých sad dvojčata zařízení pomocí [úlohy][lnk-jobs].

## <a name="device-operations"></a>Operace zařízení
Aplikace zařízení funguje na dvojče zařízení pomocí následující atomické operací:

* **Načtení dvojče zařízení**. Tato operace vrátí dokument twin zařízení (včetně značky a vlastnosti požadované a oznámená systému) pro aktuálně připojené zařízení.
* **Částečné aktualizace hlášené vlastnosti**. Tato operace povolí částečné aktualizace hlášené vlastnosti aktuálně připojené zařízení. Tato operace používá stejný formát JSON aktualizace, řešení back end používá pro částečné aktualizace požadované vlastnosti.
* **Sledovat požadované vlastnosti**. Aktuálně připojené zařízení můžete zvolit reálném oznamovat aktualizace na požadované vlastnosti. Zařízení obdrží stejného formuláře aktualizace (náhrada částečně nebo zcela) provedený back-end řešení.

Předchozí operace vyžadují **DeviceConnect** oprávnění, jak jsou definovány v [zabezpečení] [ lnk-security] článku.

[Sady SDK pro zařízení Azure IoT] [ lnk-sdks] můžete snadno použít předchozí operace z mnoha jazyky a platformy. Další informace o podrobnostech primitiv IoT Hub pro požadované vlastnosti synchronizace najdete v tématu [toku opětovné připojení zařízení][lnk-reconnection].

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

## <a name="device-twin-size"></a>Velikost twin zařízení
IoT Hub vynucuje omezení velikosti 8KB na všech příslušných celkové hodnoty `tags`, `properties/desired`, a `properties/reported`, s výjimkou elementy jen pro čtení.
Velikost se počítá podle počítání všechny znaky, s výjimkou řídicí znaky UNICODE (segmenty C0 a C1) a prostory, které jsou mimo řetězcové konstanty.
IoT Hub s chybou odmítne všechny operace, které by zvětšete velikost tyto dokumenty nad limit.

## <a name="device-twin-metadata"></a>Metadata twin zařízení
IoT Hub uchovává časové razítko poslední aktualizace pro každý objekt JSON v dvojče zařízení potřeby a který ohlásil vlastnosti. Časová razítka v UTC a v kódování [ISO8601] formátu `YYYY-MM-DDTHH:MM:SS.mmmZ`.
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

Dvojče zařízení potřeby a který ohlásil vlastnosti nemají značky etag binárním rozsáhlým, ale mají `$version` hodnotu, která představuje záruku přírůstkové. Podobně jako na značku ETag verze lze aktualizace stranou Pokud chcete zajistit konzistenci aktualizací. Například zařízení aplikaci pro hlášené vlastnost nebo back-end řešení pro požadovanou vlastnost.

Verze jsou užitečné také při observing agenta (například aplikace zařízení sledování požadované vlastnosti) musí sjednotit RAS mezi výsledek operace načtení a oznámení o aktualizaci. V části [toku opětovné připojení zařízení] [ lnk-reconnection] poskytuje další informace.

## <a name="device-reconnection-flow"></a>Postup opětovné připojení zařízení
IoT Hub nezachovává oznámení o aktualizacích požadované vlastnosti pro odpojené zařízení. Z toho vyplývá, že zařízení, která se připojuje musí získat úplné požadované vlastnosti dokumentu, kromě odběr pro oznámení o aktualizaci. Zadána možnost RAS mezi oznámení o aktualizaci a úplné načtení vhodné zajistit následující postup:

1. Aplikace zařízení připojí ke službě IoT hub.
2. Aplikace zařízení pro požadované vlastnosti Odběratel oznámení o aktualizaci.
3. Aplikace zařízení načte celého dokumentu pro požadované vlastnosti.

Aplikace zařízení můžete ignorovat všechna oznámení s `$version` menší nebo roven verzi úplné načtené dokumentu. Tento přístup je možné, protože IoT Hub zaručuje, že verze vždy zvýšit.

> [!NOTE]
> Tato logika je už implementované v [sady SDK pro zařízení Azure IoT][lnk-sdks]. Tento popis je užitečný jenom v případě, že nemůžete použít žádnou z sady SDK pro zařízení Azure IoT a musí programu rozhraní MQTT přímo aplikace zařízení.
> 
> 

## <a name="additional-reference-material"></a>Odkaz na další materiály
Další témata referenční příručka vývojáře IoT Hub patří:

* [Koncové body centra IoT] [ lnk-endpoints] článek popisuje různé koncových bodů, které každý IoT hub zpřístupní pro spuštění a management operace.
* [Omezování a kvóty] [ lnk-quotas] článek popisuje kvóty, které platí pro službu IoT Hub a omezení chování se očekává při použití služby.
* [Sady SDK zařízení a služby Azure IoT] [ lnk-sdks] článku jsou uvedené různé jazykové sady SDK můžete použít při vývoji aplikace zařízení a služby, které interakci s centrem IoT.
* [IoT Hub dotazovacího jazyka pro dvojčata zařízení, úlohy a směrování zpráv] [ lnk-query] článek popisuje dotazovací jazyk Centrum IoT, můžete použít k načtení informací ze služby IoT Hub o úlohách a dvojčata zařízení.
* [IoT Hub MQTT podporu] [ lnk-devguide-mqtt] článek obsahuje další informace o podpoře služby IoT Hub pro protokol MQTT.

## <a name="next-steps"></a>Další postup
Nyní jste se naučili o dvojčata zařízení, může zajímat v následujících tématech Příručka vývojáře IoT Hub:

* [Rady pro pochopení a použití modulu dvojčata ve IoT Hub.][lnk-module-twins]
* [Volání metody přímé na zařízení][lnk-methods]
* [Plánování úloh na několika zařízeních][lnk-jobs]

Můžete vyzkoušet na některé z konceptů popsaných v tomto článku, najdete v následujících kurzech IoT Hub:

* [Jak používat dvojče zařízení][lnk-twin-tutorial]
* [Použití zařízení dvojici vlastností][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#device-twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow
[lnk-module-twins]:iot-hub-devguide-module-twins.md

[img-twin]: media/iot-hub-devguide-device-twins/twin.png
