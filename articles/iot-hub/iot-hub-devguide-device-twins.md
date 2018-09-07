---
title: Principy dvojčat zařízení Azure IoT Hub | Dokumentace Microsoftu
description: Příručka pro vývojáře – použití dvojčat zařízení k synchronizaci stavu a konfigurace dat mezi službou IoT Hub a zařízení
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: elioda
ms.openlocfilehash: dfdffaad63d23ecf9968292cd2a8075b9cd374bc
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44022349"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Principy a použití dvojčat zařízení ve službě IoT Hub

*Dvojčata zařízení* jsou dokumenty JSON, které obsahují informace o stavu zařízení včetně metadata, konfigurace a podmínky. Azure IoT Hub udržuje takové dvojče pro každé zařízení, které se připojujete ke službě IoT Hub. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Tento článek popisuje:

* Struktura dvojčeti zařízení: *značky*, *požadované* a *ohlášené vlastnosti*.
* Operace, které aplikace pro zařízení a back-EndY můžou provádět na dvojčata zařízení.

Použití dvojčat zařízení:

* Store metadata specifická pro zařízení v cloudu. Například nasazení umístění prodejní počítače.
* Aktuální informace o stavu jako jsou k dispozici možnosti a stav z aplikace pro zařízení. Například je zařízení připojené ke službě IoT hub přes mobilní nebo Wi-Fi.
* Synchronizace stavu dlouhotrvající pracovní postupy mezi zařízení a back endové aplikace. Při řešení zpět end Určuje novou verzi firmwaru pro instalaci a aplikace pro zařízení hlásí různé fáze procesu aktualizace.
* Dotazování metadat zařízení, konfigurace nebo stavu.

Odkazovat na [pokyny komunikace typu zařízení cloud] [ lnk-d2c-guidance] pokyny, pomocí ohlášených vlastností zprávy typu zařízení cloud a nahrání souboru.
Odkazovat na [pokyny komunikaci typu Cloud zařízení] [ lnk-c2d-guidance] informace o používání požadované vlastnosti, přímých metod nebo zprávy typu cloud zařízení.

## <a name="device-twins"></a>Dvojčata zařízení
Dvojčata zařízení ukládat informace týkající se zařízení, které:

* Končí zařízení a back můžete používat k synchronizaci zařízení podmínky a konfigurace.
* Back-end řešení slouží k dotazu a cíl dlouho běžící operace.

Životní cyklus dvojčete zařízení je propojený s odpovídající [identitu zařízení][lnk-identity]. Dvojčata zařízení jsou implicitně vytvoří a odstraní při vytvoření identity zařízení nebo odstraněn ve službě IoT Hub.

Dvojče zařízení je dokument JSON, která zahrnuje:

* **Značky**. Část dokumentu JSON, který může číst z a zapisovat do back-endu řešení. Značky nejsou viditelné pro aplikace pro zařízení.
* **Požadované vlastnosti**. Používat společně s ohlášené vlastnosti k synchronizaci konfigurace zařízení nebo podmínky. Back-end řešení může nastavte požadované vlastnosti a je může číst aplikace zařízení. Aplikace zařízení můžete také přijímat oznámení změn v požadované vlastnosti.
* **Ohlášené vlastnosti**. Používat společně s požadované vlastnosti pro synchronizaci konfigurace zařízení nebo podmínky. Aplikace zařízení můžete nastavit ohlášené vlastnosti, a back-end řešení může číst a jejich dotazování.
* **Vlastnosti identity zařízení**. Obsahuje vlastnosti jen pro čtení z odpovídající identitu zařízení, které jsou uložené v kořenu dokumentu JSON dvojčete zařízení [registr identit][lnk-identity].

![][img-twin]

Následující příklad ukazuje dvojčete zařízení dokumentu JSON:

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
            },
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

V kořenovém objektu jsou zařízení vlastnosti identity a kontejner objektů pro `tags` a obě `reported` a `desired` vlastnosti. `properties` Kontejner obsahuje některé prvky jen pro čtení (`$metadata`, `$etag`, a `$version`) podle [metadat dvojčete zařízení] [ lnk-twin-metadata] a [ Optimistická souběžnost] [ lnk-concurrency] oddíly.

### <a name="reported-property-example"></a>Příklad ohlášené vlastnosti
V předchozím příkladu obsahuje dvojče zařízení `batteryLevel` vlastnost, která je ohlášena aplikace pro zařízení. Tato vlastnost umožňuje dotazování a zpracování podle poslední ohlášené baterie zařízení. Další příklady zahrnují možnosti vytváření sestav zařízení zařízení aplikace nebo možnosti připojení.

> [!NOTE]
> Ohlášené vlastnosti zjednodušení scénářů, kde je back-end řešení uvažujete o poslední známé hodnotě vlastnosti. Použití [zpráv typu zařízení cloud] [ lnk-d2c] Pokud back-end řešení musí zpracovat telemetrie zařízení v podobě pořadí časovým razítkem události, jako je časové řady.

### <a name="desired-property-example"></a>Příklad požadované vlastnosti
V předchozím příkladu `telemetryConfig` požadovaného dvojče zařízení a ohlášené vlastnosti tak, že back-end řešení a aplikace pro zařízení s slouží k synchronizaci konfigurace telemetrie pro toto zařízení. Příklad:

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

2. Aplikace pro zařízení se zobrazí oznámení změny okamžitě, pokud připojení nebo při prvním volání metody reconnect. Aplikace pro zařízení hlásí aktualizovanou konfiguraci (nebo podmínku chybu pomocí `status` vlastnost). Tady je část ohlášených vlastností:

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

3. Back-end řešení může sledovat výsledky operace konfigurace různých zařízení, podle [dotazování] [ lnk-query] dvojčata zařízení.

> [!NOTE]
> Předchozí fragmenty jsou příklady, optimalizovaný pro čitelnost jeden způsob, jak kódovat konfigurace zařízení a její stav. Služby IoT Hub nepředstavuje konkrétní schéma pro požadované dvojče zařízení a ohlášené vlastnosti v dvojčata zařízení.
> 
> 

Pomocí dvojčat můžete synchronizovat dlouhotrvající operace, jako jsou aktualizace firmwaru. Další informace o tom, jak používat vlastnosti pro synchronizaci a sledování dlouho spuštěné operace napříč zařízeními najdete v tématu [použití požadované vlastnosti ke konfiguraci zařízení][lnk-twin-properties].

## <a name="back-end-operations"></a>Back endové operace
Back-end řešení funguje v dvojčeti zařízení pomocí následující atomických operací prostřednictvím protokolu HTTPS:

* **Načíst dvojče zařízení podle ID**. Tato operace vrátí dokumentu dvojčete zařízení, včetně značky a požadované a ohlášené vlastnosti.
* **Částečné aktualizace dvojčete zařízení**. Tato operace umožňuje back-end řešení do částečné aktualizace značek nebo požadované vlastnosti v dvojčeti zařízení. Částečné aktualizace je vyjádřena jako dokument JSON, který přidá nebo aktualizuje všechny vlastnosti. Vlastnosti nastavené na `null` se odeberou. Následující příklad vytvoří novou požadovanou vlastnost s hodnotou `{"newProperty": "newValue"}`, přepíše stávající hodnotu `existingProperty` s `"otherNewValue"`a odebere `otherOldProperty`. Existující požadovaných vlastností nebo značky nebudou provedeny žádné další změny:

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
    HubName | Název služby IoT Hub |
    operationTimestamp | [ISO8601] časové razítko operace |
    schéma iothub zprávy | deviceLifecycleNotification |
    opType | "replaceTwin" nebo "updateTwin" |

    Vlastnosti zprávy systému začínají `'$'` symbol.

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

Předchozí operace podporují [optimistického řízení souběžnosti] [ lnk-concurrency] a vyžadují **ServiceConnect** oprávnění, jak jsou definovány v [zabezpečení] [ lnk-security] článku.

Kromě těchto operací back-end řešení můžete:

* Dotaz dvojčata zařízení pomocí podobném SQL [dotazovací jazyk služby IoT Hub][lnk-query].
* Provádění operací na velkých sadách dvojčata zařízení pomocí [úlohy][lnk-jobs].

## <a name="device-operations"></a>Operace zařízení
Aplikace pro zařízení pracuje na dvojče zařízení pomocí následující atomických operací:

* **Načíst dvojče zařízení**. Tato operace vrátí dokument dvojčete zařízení (včetně značky a požadované a ohlášené vlastnosti) pro aktuálně připojené zařízení.
* **Částečně aktualizaci ohlášených vlastností**. Tato operace umožňuje částečnou aktualizaci ohlášených vlastností aktuálně připojené zařízení. Tato operace používá stejný formát JSON aktualizace, řešení back end použití částečné aktualizace požadované vlastnosti.
* **Sledujte požadované vlastnosti**. Aktuálně připojených zařízení můžete zvolit informováni o aktualizace požadovaných vlastností, když k nim dojde. Zařízení obdrží aktualizace (částečné nebo úplné nahrazení) provedených back-end řešení stejného formuláře.

Předchozí operace vyžadují **DeviceConnect** oprávnění, jak jsou definovány v [zabezpečení] [ lnk-security] článku.

[Sady SDK pro zařízení Azure IoT] [ lnk-sdks] usnadňují používání předchozí operace z mnoha jazyků a platforem. Další informace v podrobnostech primitiva služby IoT Hub pro požadované vlastnosti synchronizace najdete v tématu [zařízení opětovné připojení toku][lnk-reconnection].

## <a name="tags-and-properties-format"></a>Formát značky a vlastností
Ohlášené vlastnosti, značky a požadované vlastnosti jsou objekty JSON s následujícími omezeními:

* Všechny klíče v objektech JSON jsou malá a velká písmena 64 bajtů řetězce UNICODE UTF-8. Povolené znaky vyloučit řídící znaky UNICODE (segmenty C0 a C1), a `'.'`, `' '`, a `'$'`.
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

## <a name="device-twin-size"></a>Velikost dvojčete zařízení
IoT Hub vynucuje omezení velikosti 8KB na všech příslušných celkové hodnoty `tags`, `properties/desired`, a `properties/reported`, s výjimkou prvků jen pro čtení.
Velikost je vypočítán určeno spočítáním všechny znaky s výjimkou řídící znaky UNICODE (segmenty C0 a C1) a prostory, které se nachází mimo řetězcové konstanty.
IoT Hub s chybou odmítne všechny operace, které by dojít ke zvětšení těchto dokumentů přesahuje limit.

## <a name="device-twin-metadata"></a>Metadata dvojčete zařízení
Služba IoT Hub udržuje požadované časové razítko poslední aktualizace pro každý objekt JSON ve dvojčeti zařízení a ohlášené vlastnosti. Časová razítka se ve standardu UTC a zakódován do [ISO8601] formátu `YYYY-MM-DDTHH:MM:SS.mmmZ`.
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
Značky mají značku ETag, jak je uvedeno [RFC7232], představující reprezentaci JSON na značku. Značek etag v operacích podmíněné aktualizace z back-end řešení můžete použít k zajištění konzistence.

Dvojče zařízení požadovaného a ohlášené vlastnosti nemají značek etag, ale jste `$version` hodnotu, která je zaručeně přírůstkové. Podobně jako na značku ETag na verzi umožňuje aktualizace stranou vynutit konzistenci aktualizací. Například aplikace pro zařízení pro ohlášených vlastností nebo back-end řešení pro požadovanou vlastnost.

Verze jsou také užitečné, když observing agenta (například aplikace pro zařízení s sledování požadované vlastnosti) musí odsouhlasit bude mezi výsledek operace načtení a oznámení o aktualizaci. V části [zařízení opětovné připojení toku] [ lnk-reconnection] poskytuje další informace.

## <a name="device-reconnection-flow"></a>Tok opětovné připojení zařízení
IoT Hub Nezachovávat hodnotu požadované vlastnosti oznámení o aktualizacích pro odpojená zařízení. Z toho vyplývá, že zařízení, která se připojuje musí získat úplné požadované vlastnosti dokumentu, kromě přihlašování odběru oznámení o aktualizacích. Zadána možnost bude mezi oznámení o aktualizacích a úplné načtení, musí být zajištěno následující postup:

1. Aplikace pro zařízení se připojí do služby IoT hub.
2. Aplikace pro zařízení přihlásí pro požadované vlastnosti oznámení o aktualizacích.
3. Aplikace pro zařízení načte celý dokument pro požadované vlastnosti.

Aplikace zařízení můžete ignorovat všechna oznámení s `$version` menší nebo rovna verzi načtené celý dokument. Tento přístup je možné služby IoT Hub zaručuje, že verze vždy zvýšit.

> [!NOTE]
> Tato logika je již implementováno v [sady SDK pro zařízení Azure IoT][lnk-sdks]. Tento popis je užitečná pouze v případě aplikace pro zařízení nemůže používat žádné sady SDK pro zařízení Azure IoT a musí aplikaci rozhraní MQTT přímo.
> 
> 

## <a name="additional-reference-material"></a>Další referenční materiál
Další referenční témata v příručce pro vývojáře IoT Hub patří:

* [Koncové body IoT Hubu] [ lnk-endpoints] článek popisuje různé koncové body, které každý IoT hub zpřístupní pro operace za běhu a správy.
* [Omezování a kvótách] [ lnk-quotas] článek popisuje kvóty, které platí pro službu IoT Hub a omezování chování očekávat, když používáte službu.
* [Sady SDK Azure IoT zařízení a služby] [ lnk-sdks] článku jsou uvedené různé jazykové sady SDK můžete použít při vývoji aplikace s zařízení i služby, které pracují s centrem IoT.
* [Dotazovací jazyk služby IoT Hub pro dvojčata zařízení, úlohy a směrování zpráv] [ lnk-query] článku popisuje dotazovací jazyk služby IoT Hub můžete použít k načtení informací ze služby IoT Hub o dvojčata zařízení a úlohy.
* [Podpora IoT Hub MQTT] [ lnk-devguide-mqtt] článek obsahuje další informace o podpoře služby IoT Hub pro protokol MQTT.

## <a name="next-steps"></a>Další postup
Nyní jste se dozvěděli o dvojčata zařízení, vás může zajímat v následujících tématech příručky pro vývojáře IoT Hub:

* [Principy a použití dvojčat modulů ve službě IoT Hub][lnk-module-twins]
* [Vyvolání přímé metody v zařízení][lnk-methods]
* [Plánování úloh na několika zařízeních][lnk-jobs]

Vyzkoušet si některé koncepty popsané v tomto článku, najdete v následujících kurzech služby IoT Hub:

* [Jak používat dvojče zařízení][lnk-twin-tutorial]
* [Jak používat vlastnosti dvojčat zařízení][lnk-twin-properties]
* [Správa zařízení pomocí sady Azure IoT Toolkit pro VS Code][lnk-twin-vscode]

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
[lnk-twin-properties]: tutorial-device-twins.md
[lnk-twin-vscode]: iot-hub-device-management-iot-toolkit.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#device-twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow
[lnk-module-twins]:iot-hub-devguide-module-twins.md

[img-twin]: media/iot-hub-devguide-device-twins/twin.png
