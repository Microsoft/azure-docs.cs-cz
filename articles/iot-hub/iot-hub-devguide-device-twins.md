---
title: Principy dvojčat zařízení Azure IoT Hub | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – pomocí dvojčat zařízení synchronizujte stav a konfigurační data mezi službou IoT Hub a vašimi zařízeními.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/01/2020
ms.openlocfilehash: 51e58de92f111c8854add613a299f2b8ccec0503
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79285237"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Principy a používání dvojčat zařízení v centru IoT Hub

*Dvojčata zařízení* jsou dokumenty JSON, které ukládají informace o stavu zařízení, včetně metadat, konfigurací a podmínek. Azure IoT Hub udržuje takové dvojče pro každé zařízení, které připojíte k IoT Hubu. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Tento článek popisuje:

* Struktura dvojčete zařízení: *značky*, *požadované* a *hlášené vlastnosti*.
* Operace, které aplikace zařízení a back-endy můžete provádět na dvojčata zařízení.

Pomocí dvojčat zařízení můžete:

* Uklápěte metadata specifická pro zařízení do cloudu. Například umístění nasazení prodejního automatu.

* Nahlaste aktuální informace o stavu, jako jsou dostupné možnosti a podmínky z aplikace zařízení. Zařízení je například připojené k vašemu centru IoT hub přes mobilní síť nebo Wi-Fi.

* Synchronizujte stav dlouhotrvajících pracovních postupů mezi aplikací zařízení a back-endovou aplikací. Například když back-end řešení určuje novou verzi firmwaru k instalaci a aplikace zařízení hlásí různé fáze procesu aktualizace.

* Dotaz na metadata, konfiguraci nebo stav zařízení.

Pokyny k používání ohlášených vlastností, zpráv mezi zařízeními a cloudem nebo nahrávání souborů naleznete v [pokynech pro komunikaci mezi zařízeními.](iot-hub-devguide-d2c-guidance.md)

Pokyny k používání požadovaných vlastností, přímých metod nebo zpráv o cloudu zařízení naleznete v [pokynech ke komunikaci](iot-hub-devguide-c2d-guidance.md) mezi cloudem.

## <a name="device-twins"></a>Dvojčata zařízení

Dvojčata zařízení ukládají informace týkající se zařízení, které:

* Zařízení a back-endy lze použít k synchronizaci podmínek a konfigurace zařízení.

* Back-end řešení můžete použít k dotazování a cíl dlouhotrvající operace.

Životní cyklus dvojčete zařízení je propojen s odpovídající [identitou zařízení](iot-hub-devguide-identity-registry.md). Dvojčata zařízení jsou implicitně vytvořeny a odstraněny při vytvoření nebo odstranění identity zařízení v centru IoT Hub.

Dvojče zařízení je dokument JSON, který obsahuje:

* **Značky**. Část dokumentu JSON, ze které může back-end řešení číst a do které zapisovat. Značky nejsou viditelné pro aplikace zařízení.

* **Požadované vlastnosti**. Používá se spolu s ohlášenými vlastnostmi k synchronizaci konfigurace zařízení nebo podmínek. Back-end řešení můžete nastavit požadované vlastnosti a aplikace zařízení můžete číst. Aplikace zařízení může také přijímat oznámení o změnách v požadovaných vlastnostech.

* **Hlášené vlastnosti**. Používá se spolu s požadovanými vlastnostmi pro synchronizaci konfigurace zařízení nebo podmínek. Aplikace zařízení můžete nastavit hlášené vlastnosti a back-end řešení můžete číst a dotaz ovat.

* **Vlastnosti identity zařízení**. Kořen dokumentu JSON dvojčete zařízení obsahuje vlastnosti jen pro čtení z odpovídající identity zařízení uložené v [registru identit](iot-hub-devguide-identity-registry.md).

![Snímek obrazovky s vlastnostmi dvojčete zařízení](./media/iot-hub-devguide-device-twins/twin.png)

Následující příklad ukazuje dvojče zařízení Dokument JSON:

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

V kořenovém objektu jsou vlastnosti identity `reported` zařízení `desired` a objekty kontejneru a `tags` jak a vlastnosti. Kontejner `properties` obsahuje některé prvky`$metadata`jen `$etag`pro `$version`čtení ( , , a ) popsané v [části metadat dvojčete zařízení](iot-hub-devguide-device-twins.md#device-twin-metadata) a [optimistická souběžnost.](iot-hub-devguide-device-twins.md#optimistic-concurrency)

### <a name="reported-property-example"></a>Příklad hlášené vlastnosti

V předchozím příkladu dvojčezařízení `batteryLevel` obsahuje vlastnost, která je hlášena aplikací zařízení. Tato vlastnost umožňuje dotazovat a pracovat na zařízeních na základě poslední hlášené úrovně baterie. Mezi další příklady patří možnosti zařízení pro vytváření sestav aplikací pro zařízení nebo možnosti připojení.

> [!NOTE]
> Hlášené vlastnosti zjednodušují scénáře, kde back-end řešení má zájem o poslední známou hodnotu vlastnosti. Zprávy [mezi zařízeními](iot-hub-devguide-messages-d2c.md) a cloudem použijte, pokud back-end řešení potřebuje ke zpracování telemetrie zařízení ve formě sekvencí událostí s časovým razítkem, jako jsou časové řady.

### <a name="desired-property-example"></a>Příklad požadované vlastnosti

V předchozím příkladu `telemetryConfig` dvojčete zařízení požadované a hlášené vlastnosti jsou používány back-end řešení a aplikace zařízení pro synchronizaci konfigurace telemetrie pro toto zařízení. Například:

1. Back-end řešení nastaví požadovanou vlastnost s požadovanou hodnotou konfigurace. Zde je část dokumentu s požadovanou sadou vlastností:

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. Aplikace zařízení je upozorněna na změnu okamžitě, pokud je připojena, nebo při prvním opětovném připojení. Aplikace zařízení pak hlásí aktualizovanou konfiguraci `status` (nebo chybový stav pomocí vlastnosti). Zde je část hlášené vlastnosti:

   ```json
   "reported": {
       "telemetryConfig": {
           "sendFrequency": "5m",
           "status": "success"
       }
       ...
   }
   ```

3. Back-end řešení můžete sledovat výsledky operace konfigurace napříč mnoha zařízeními [dotazování](iot-hub-devguide-query-language.md) dvojčatzařízení.

> [!NOTE]
> Předchozí úryvky jsou příklady, optimalizované pro čitelnost, jeden způsob, jak kódovat konfiguraci zařízení a jeho stav. IoT Hub neukládá konkrétní schéma pro dvojče zařízení požadované a hlášené vlastnosti v dvojčata zařízení.
> 

Dvojčata můžete použít k synchronizaci dlouhotrvajících operací, jako jsou aktualizace firmwaru. Další informace o použití vlastností k synchronizaci a sledování dlouhotrvající operace mezi zařízeními naleznete v [tématu Použití požadovaných vlastností ke konfiguraci zařízení](tutorial-device-twins.md).

## <a name="back-end-operations"></a>Back-endové operace

Back-end řešení pracuje na dvojčeti zařízení pomocí následujících atomových operací, vystavených prostřednictvím protokolu HTTPS:

* **Načíst dvojče zařízení podle ID**. Tato operace vrátí dokument dvojčete zařízení, včetně značek a požadovaných a oznámených vlastností systému.

* **Částečně aktualizovat dvojče zařízení**. Tato operace umožňuje back-endu řešení částečně aktualizovat značky nebo požadované vlastnosti v dvojčeti zařízení. Částečná aktualizace je vyjádřena jako dokument JSON, který přidává nebo aktualizuje všechny vlastnosti. Vlastnosti `null` nastavené na jsou odebrány. Následující příklad vytvoří novou požadovanou `{"newProperty": "newValue"}`vlastnost s hodnotou `existingProperty` `"otherNewValue"`, přepíše `otherOldProperty`existující hodnotu with a odebere . V existujících požadovaných vlastnostech nebo značkách nejsou provedeny žádné další změny:

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

* **Dostávat oznámení dvojčete**. Tato operace umožňuje back-end řešení, které mají být oznámeny při změně dvojčete. Chcete-li tak učinit, vaše řešení IoT musí vytvořit trasu a nastavit zdroj dat rovná *twinChangeEvents*. Ve výchozím nastavení žádné takové trasy předem existují, takže jsou odesílána žádná oznámení dvojčete. Pokud je rychlost změny příliš vysoká nebo z jiných důvodů, jako jsou interní selhání, může centrum IoT Hub odeslat pouze jedno oznámení, které obsahuje všechny změny. Proto pokud vaše aplikace potřebuje spolehlivé auditování a protokolování všech zprostředkujících stavů, měli byste použít zprávy zařízení cloud. Dvojitá zpráva oznámení obsahuje vlastnosti a tělo.

  - Vlastnosti

    | Name (Název) | Hodnota |
    | --- | --- |
    $content typu | application/json |
    $iothub doba zařazení do fronty |  Čas odeslání oznámení |
    $iothub-zdroj zprávy | twinChangeEvents |
    kódování $content | utf-8 |
    deviceId | ID zařízení |
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

Všechny předchozí operace podporují [optimistickou souběžnost](iot-hub-devguide-device-twins.md#optimistic-concurrency) a vyžadují oprávnění **ServiceConnect,** jak je definováno v [ovládacím prvku přístupu k centru IoT Hub](iot-hub-devguide-security.md).

Kromě těchto operací může back-end řešení:

* Dotaz dvojčata zařízení pomocí [dotazovacího jazyka IoT Hub](iot-hub-devguide-query-language.md)SQL .

* Provádět operace na velké sady dvojčat zařízení pomocí [úlohy](iot-hub-devguide-jobs.md).

## <a name="device-operations"></a>Provoz zařízení

Aplikace zařízení pracuje na dvojčeti zařízení pomocí následujících atomových operací:

* **Načíst dvojče zařízení**. Tato operace vrátí dokument dvojčete zařízení (včetně požadovaných a hlášených vlastností systému) pro aktuálně připojené zařízení. (Značky nejsou viditelné pro aplikace zařízení.)

* **Částečně aktualizovat hlášené vlastnosti**. Tato operace umožňuje částečnou aktualizaci ohlášených vlastností aktuálně připojeného zařízení. Tato operace používá stejný formát aktualizace JSON, který back-end řešení používá pro částečnou aktualizaci požadovaných vlastností.

* **Dodržujte požadované vlastnosti**. Aktuálně připojené zařízení může zvolit, že bude upozorněno na aktualizace požadovaných vlastností, když k nim dojde. Zařízení obdrží stejnou formu aktualizace (částečné nebo úplné nahrazení) provedené back-endem řešení.

Všechny předchozí operace vyžadují oprávnění **DeviceConnect,** jak je definováno v [ovládacím přístupu k centru IoT Hub](iot-hub-devguide-security.md).

Sady [SDK zařízení Azure IoT](iot-hub-devguide-sdks.md) usnadňují použití předchozích operací z mnoha jazyků a platforem. Další informace o podrobnostech primitiv služby IoT Hub pro synchronizaci požadovaných vlastností naleznete v [tématu Tok opětovného připojení zařízení](iot-hub-devguide-device-twins.md#device-reconnection-flow).

## <a name="tags-and-properties-format"></a>Formát tagů a vlastností

Značky, požadované vlastnosti a ohlášené vlastnosti jsou objekty JSON s následujícími omezeními:

* **Klíče**: Všechny klávesy v objektech JSON jsou kódovány UTF-8, rozlišují malá a velká písmena a mají délku až 1 kb. Povolené znaky vylučují řídicí znaky UNICODE `.` `$`(segmenty C0 a C1) a , a SP.

* **Hodnoty**: Všechny hodnoty v objektech JSON mohou být z následujících typů JSON: logické, číslo, řetězec, objekt. Pole nejsou povolena.

    * Celá čísla mohou mít minimální hodnotu -4503599627370496 a maximální hodnotu 4503599627370495.

    * Řetězcové hodnoty jsou kódovány UTF-8 a mohou mít maximální délku 4 KB.

* **Hloubka**: Maximální hloubka objektů JSON ve značkách, požadované vlastnosti a hlášené vlastnosti je 10. Například následující objekt je platný:

   ```json
   {
       ...
       "tags": {
           "one": {
               "two": {
                   "three": {
                       "four": {
                           "five": {
                               "six": {
                                   "seven": {
                                       "eight": {
                                           "nine": {
                                               "ten": {
                                                   "property": "value"
                                               }
                                           }
                                       }
                                   }
                               }
                           }
                       }
                   }
               }
           }
       },
       ...
   }
   ```

## <a name="device-twin-size"></a>Velikost dvojčete zařízení

IoT Hub vynucuje limit velikosti `tags`8 kB na hodnotu , a `properties/desired` `properties/reported`32 kB limit velikosti každý na hodnotu a . Tyto součty jsou bez prvků `$etag`jen `$version`pro `$metadata/$lastUpdated`čtení, jako jsou , a .

Velikost dvojčete se vypočítá takto:

* Pro každou vlastnost v dokumentu JSON ioT Hub kumulativně vypočítá a přidá délku klíče a hodnoty vlastnosti.

* Klíče vlastností jsou považovány za řetězce kódované UTF8.

* Jednoduché hodnoty vlastností jsou považovány za řetězce kódované UTF8, číselné hodnoty (8 bajtů) nebo logické hodnoty (4 bajty).

* Velikost řetězců kódovaných UTF8 se vypočítá počítáním všech znaků s výjimkou řídicích znaků UNICODE (segmenty C0 a C1).

* Komplexní hodnoty vlastností (vnořené objekty) jsou vypočítány na základě agregační velikostklíče vlastností a hodnoty vlastností, které obsahují.

IoT Hub odmítne s chybou všechny `tags`operace, které `properties/desired`by `properties/reported` zvětšily velikost , nebo dokumenty nad limit.

## <a name="device-twin-metadata"></a>Metadata dvojčete zařízení

IoT Hub udržuje časové razítko poslední aktualizace pro každý objekt JSON v objektu dvojčete zařízení požadované a hlášené vlastnosti. Časová razítka jsou v UTC a kódována ve `YYYY-MM-DDTHH:MM:SS.mmmZ`formátu [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) .

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

Dvojče zařízení požadované a hlášené vlastnosti nemají `$version` ETags, ale mají hodnotu, která je zaručena přírůstkové. Podobně jako ETag, verze může být použita aktualizační stranou k vynucení konzistence aktualizací. Například aplikace zařízení pro ohlášenou vlastnost nebo back-end řešení pro požadovanou vlastnost.

Verze jsou také užitečné, když agent pozorování (například aplikace zařízení, která sleduje požadované vlastnosti) musí sladit rasy mezi výsledkem operace načtení a oznámením o aktualizaci. Další informace obsahuje [část Tok opětovného připojení zařízení.](iot-hub-devguide-device-twins.md#device-reconnection-flow)

## <a name="device-reconnection-flow"></a>Tok opětovného připojení zařízení

Služba IoT Hub nezachová požadovaná oznámení o aktualizaci vlastností pro odpojená zařízení. Z toho vyplývá, že zařízení, které se připojuje, musí načíst dokument úplné požadované vlastnosti, kromě přihlášení k odběru oznámení o aktualizaci. Vzhledem k možnosti závodů mezi oznámeními o aktualizaci a úplným načítáním musí být zajištěn následující tok:

1. Aplikace zařízení se připojuje k centru IoT.
2. Aplikace zařízení se přihlásí k odběru oznámení o aktualizaci požadovaných vlastností.
3. Aplikace zařízení načte celý dokument pro požadované vlastnosti.

Aplikace zařízení můžete ignorovat všechna `$version` oznámení s menší nebo rovna než verze úplného načteného dokumentu. Tento přístup je možné, protože IoT Hub zaručuje, že verze vždy přírůstek.

> [!NOTE]
> Tato logika je již implementována v [sadách SDK zařízení Azure IoT](iot-hub-devguide-sdks.md). Tento popis je užitečný jenom v případě, že aplikace zařízení nemůže používat žádné sady SDK zařízení Azure IoT a musí naprogramovat rozhraní MQTT přímo.
> 

## <a name="additional-reference-material"></a>Doplňkový referenční materiál

Mezi další referenční témata v průvodci vývojáři služby IoT Hub patří:

* Článek [koncových bodů centra IoT](iot-hub-devguide-endpoints.md) popisuje různé koncové body, které každý centrum IoT hub zveřejňuje pro operace se spuštěním a správou.

* Omezení [a kvóty](iot-hub-devguide-quotas-throttling.md) článek popisuje kvóty, které platí pro službu IoT Hub a omezení chování očekávat při použití služby.

* V článku sady [Azure IoT zařízení a služby Sady SDK](iot-hub-devguide-sdks.md) uvádí různé jazyky sady SDK, které můžete použít při vývoji zařízení a služby aplikace, které interagují s IoT Hub.

* [Dotazovací jazyk služby IoT Hub pro dvojčata zařízení, úlohy a směrování zpráv](iot-hub-devguide-query-language.md) popisuje dotazovací jazyk služby IoT Hub, který můžete použít k načtení informací z ioT hubu o dvojčatech a úlohách vašeho zařízení.

* Článek [podpory služby IoT Hub MQTT](iot-hub-mqtt-support.md) obsahuje další informace o podpoře služby IoT Hub pro protokol MQTT.

## <a name="next-steps"></a>Další kroky

Teď jste se dozvěděli o dvojčata zařízení, může vás zajímat následující témata průvodce pro vývojáře služby IoT Hub:

* [Principy a používání dvojčat modulů v centru IoT Hub](iot-hub-devguide-module-twins.md)
* [Vyvolání přímé metody na zařízení](iot-hub-devguide-direct-methods.md)
* [Plánování úloh na několika zařízeních](iot-hub-devguide-jobs.md)

Chcete-li vyzkoušet některé koncepty popsané v tomto článku, podívejte se na následující kurzy ioT Hub:

* [Jak používat dvojče zařízení](iot-hub-node-node-twin-getstarted.md)
* [Jak používat vlastnosti dvojčete zařízení](tutorial-device-twins.md)
* [Správa zařízení s využitím sady Azure IoT Tools pro VS Code](iot-hub-device-management-iot-toolkit.md)
