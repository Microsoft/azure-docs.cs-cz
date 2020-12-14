---
title: Vysvětlení nevláken zařízení v Azure IoT Hub | Microsoft Docs
description: Příručka pro vývojáře – pomocí vláken zařízení můžete synchronizovat stav a konfigurační data mezi IoT Hub a vašimi zařízeními.
author: nehsin
manager: philmea
ms.author: nehsin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom:
- mqtt
- 'Role: Cloud Development'
ms.openlocfilehash: b83faecb16ac09a47a0ade25474f7a5b3ecd4296
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400924"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Pochopení a používání vláken zařízení v IoT Hub

*Vlákna zařízení* jsou dokumenty JSON, které ukládají informace o stavu zařízení včetně metadat, konfigurací a podmínek. Azure IoT Hub udržuje takové dvojče pro každé zařízení, které připojíte k IoT Hubu. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Tento článek popisuje:

* Struktura vlákna zařízení: *značky*, *požadované* a *hlášené vlastnosti*.
* Operace, které aplikace zařízení a back-end můžou provádět na vlákna zařízení.

Použít vlákna zařízení k těmto akcím:

* Ukládat metadata specifická pro zařízení v cloudu. Například umístění nasazení počítače prodejní Automate.

* Ohlaste informace o aktuálních stavech, jako jsou dostupné možnosti a podmínky z aplikace vašeho zařízení. Například zařízení je připojeno ke službě IoT Hub přes mobilní síť nebo Wi-Fi.

* Synchronizuje stav dlouhotrvajících pracovních postupů mezi aplikací zařízení a back-end aplikací. Například když back-end řešení určuje novou verzi firmwaru, která se má nainstalovat, a aplikace zařízení ohlásí různé fáze procesu aktualizace.

* Dotaz na metadata, konfiguraci nebo stav zařízení

Pokyny k používání hlášených vlastností, zpráv zařízení-Cloud nebo nahrání souborů najdete v tématu [pokyny k komunikace zařízení a cloudu](iot-hub-devguide-d2c-guidance.md) .

Pokyny k používání požadovaných vlastností, přímých metod nebo zpráv z cloudu na zařízení najdete v tématu [pokyny k komunikaci z cloudu na zařízení](iot-hub-devguide-c2d-guidance.md) .

## <a name="device-twins"></a>Vlákna zařízení

V zařízení se ukládají informace týkající se zařízení, které:

* Zařízení a back-endy se můžou použít k synchronizaci podmínek zařízení a konfigurace.

* Back-end řešení může používat dotazování a cíle dlouhotrvajících operací.

Životní cyklus vlákna zařízení je propojen s odpovídající [identitou zařízení](iot-hub-devguide-identity-registry.md). Vlákna zařízení se při vytvoření nebo odstranění identity zařízení v IoT Hub implicitně vytvoří a odstraní.

Nevlákenná zařízení je dokument JSON, který obsahuje:

* **Značky**. Oddíl dokumentu JSON, ze kterého může back-end řešení číst a zapisovat do něj. Značky nejsou viditelné pro aplikace zařízení.

* **Požadované vlastnosti**. Používá se společně s nahlášenými vlastnostmi k synchronizaci konfigurace nebo podmínek zařízení. Back-end řešení může nastavit požadované vlastnosti a aplikace zařízení je může číst. Aplikace zařízení může také dostávat oznámení o změnách v požadovaných vlastnostech.

* **Hlášené vlastnosti**. Používá se společně s požadovanými vlastnostmi k synchronizaci konfigurace nebo podmínek zařízení. Aplikace zařízení může nastavit hlášené vlastnosti a back-end řešení může číst a dotazovat je.

* **Vlastnosti identity zařízení**. Kořen dokumentu s dvojitou čárkou v zařízení JSON obsahuje vlastnosti jen pro čtení z odpovídající identity zařízení uložené v [registru identit](iot-hub-devguide-identity-registry.md). Vlastnosti `connectionStateUpdatedTime` a nebudou `generationId` zahrnuty.

![Snímek obrazovky se zdvojenými vlastnostmi zařízení](./media/iot-hub-devguide-device-twins/twin.png)

Následující příklad ukazuje dokument JSON s dvojím zápisem zařízení:

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

V kořenovém objektu jsou vlastnosti identity zařízení a objekty kontejneru pro a a `tags` zároveň i `reported` `desired` Vlastnosti. `properties`Kontejner obsahuje některé prvky jen pro čtení ( `$metadata` , `$etag` a `$version` ) popsané v oddílech [Dvojitá metadata zařízení](iot-hub-devguide-device-twins.md#device-twin-metadata) a [Optimistická souběžnost](iot-hub-devguide-device-twins.md#optimistic-concurrency) .

### <a name="reported-property-example"></a>Příklad hlášené vlastnosti

V předchozím příkladu obsahuje vlákna zařízení `batteryLevel` vlastnost, která je hlášena aplikací pro zařízení. Tato vlastnost umožňuje dotazovat se na zařízení a pracovat s nimi na základě poslední hlášené úrovně baterie. Mezi další příklady patří možnosti zařízení pro vytváření sestav aplikací pro zařízení nebo možnosti připojení.

> [!NOTE]
> Hlášené vlastnosti zjednodušují scénáře, kde back-end řešení zajímá poslední známou hodnotu vlastnosti. [Zprávy typu zařízení-Cloud](iot-hub-devguide-messages-d2c.md) použijte v případě, že back-end řešení potřebuje zpracovat telemetrii zařízení ve formě posloupností událostí s časovým razítkem, jako je například časová řada.

### <a name="desired-property-example"></a>Požadovaný příklad vlastnosti

V předchozím příkladu se v rámci `telemetryConfig` back-endu řešení a aplikace pro zařízení používá k synchronizaci konfigurace telemetrie pro toto zařízení, aby bylo požadované a hlášené vlastnosti zařízení. Například:

1. Back-end řešení nastaví požadovanou vlastnost s požadovanou konfigurační hodnotou. Tady je část dokumentu s požadovanou sadou vlastností:

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. Aplikace zařízení se okamžitě upozorní na změnu v případě připojení nebo při prvním opětovném připojení. Aplikace zařízení pak nahlásí aktualizovanou konfiguraci (nebo chybovou podmínku pomocí `status` Vlastnosti). Tady je část hlášených vlastností:

   ```json
   "reported": {
       "telemetryConfig": {
           "sendFrequency": "5m",
           "status": "success"
       }
       ...
   }
   ```

3. Back-end řešení může sledovat výsledky operace konfigurace napříč mnoha zařízeními pomocí [dotazování](iot-hub-devguide-query-language.md) na vlákna zařízení.

> [!NOTE]
> Předchozí fragmenty kódu jsou příklady optimalizované pro čitelnost, které zakódují konfiguraci zařízení a jeho stav. IoT Hub neukládá konkrétní schéma pro požadovanou a hlášené vlastnosti zařízení, které jsou v zařízení vlákna.
> 

Můžete použít vlákna k synchronizaci dlouhotrvajících operací, jako jsou například aktualizace firmwaru. Další informace o tom, jak pomocí vlastností synchronizovat a sledovat dlouhodobou běžící operaci napříč zařízeními, najdete v tématu věnovaném [použití požadovaných vlastností ke konfiguraci zařízení](tutorial-device-twins.md).

## <a name="back-end-operations"></a>Back-endové operace

Back-end řešení funguje na vlákna zařízení pomocí následujících atomických operací, které jsou vystavené prostřednictvím protokolu HTTPS:

* **Načíst zdvojení zařízení podle ID** Tato operace vrátí dokument s dvojitým označením zařízení, včetně značek a požadovaných a hlášených systémových vlastností.

* **Částečně aktualizovat zdvojené zařízení**. Tato operace umožňuje back-endu řešení částečně aktualizovat značky nebo požadované vlastnosti v zařízení s dvojitou funkčností. Částečná aktualizace se vyjádří jako dokument JSON, který přidá nebo aktualizuje libovolnou vlastnost. Vlastnosti nastavené na `null` jsou odebrané. Následující příklad vytvoří novou požadovanou vlastnost s hodnotou `{"newProperty": "newValue"}` , přepíše existující hodnotu `existingProperty` s `"otherNewValue"` a odebere `otherOldProperty` . U stávajících požadovaných vlastností nebo značek nejsou provedeny žádné další změny:

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

* **Nahraďte požadované vlastnosti**. Tato operace umožňuje back-endu řešení úplně přepsat všechny existující požadované vlastnosti a nahradit nový dokument JSON pro `properties/desired` .

* **Nahraďte značky**. Tato operace umožňuje back-endu řešení úplně přepsat všechny existující značky a nahradit nový dokument JSON pro `tags` .

* **Dostávat dvojitá oznámení**. Tato operace umožňuje, aby byl back-end řešení upozorněn při změně vlákna. K tomu je potřeba, aby vaše řešení IoT vytvořilo trasu a nastavilo zdroj dat na hodnotu *twinChangeEvents*. Ve výchozím nastavení už žádné takové trasy neexistují, takže se neodesílají žádná dvojitá oznámení. Pokud je frekvence změny příliš vysoká nebo z jiných důvodů, jako jsou například vnitřní chyby, může IoT Hub odeslat pouze jedno oznámení, které obsahuje všechny změny. Proto pokud vaše aplikace potřebuje spolehlivé auditování a protokolování všech zprostředkujících stavů, měli byste použít zprávy typu zařízení-Cloud. Zpráva s dvojitým oznámením obsahuje vlastnosti a text.

  - Vlastnosti

    | Název | Hodnota |
    | --- | --- |
    $content – typ | application/json |
    $iothub – enqueuedtime |  Čas odeslání oznámení |
    $iothub-Message-source | twinChangeEvents |
    $content – kódování | UTF-8 |
    deviceId | ID zařízení |
    hubName | Název IoT Hub |
    operationTimestamp | [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) časové razítko operace |
    iothub-Message-Schema | twinChangeNotification |
    opType | "replaceTwin" nebo "updateTwin" |

    Vlastnosti systému zprávy jsou předpony s `$` symbolem.

  - Text
        
    Tato část obsahuje všechny zdvojené změny ve formátu JSON. Používá stejný formát jako oprava, s rozdílem, který může obsahovat všechny nedokončené oddíly: Tagy, Properties. hlášené, Properties. revisioned a obsahuje prvky "$metadata". Třeba

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

Všechny předchozí operace podporují [optimistickou souběžnost](iot-hub-devguide-device-twins.md#optimistic-concurrency) a vyžadují oprávnění **ServiceConnect** , jak je definováno v [ovládacím prvku přístup k IoT Hub](iot-hub-devguide-security.md).

Kromě těchto operací může back-end řešení:

* Dotaz na práci se zařízením pomocí [dotazovacího jazyka IoT Hub](iot-hub-devguide-query-language.md), který se podobá jazyku SQL.

* Provádění operací s velkými sadami nevláken zařízení pomocí [úloh](iot-hub-devguide-jobs.md).

## <a name="device-operations"></a>Operace zařízení

Aplikace zařízení funguje na vlákna zařízení s použitím následujících atomických operací:

* **Načte nevlákennou zařízení**. Tato operace vrátí k aktuálně připojenému zařízení neodpovídající dokument (včetně požadovaných a hlášených vlastností systému) zařízení. (Značky nejsou viditelné pro aplikace zařízení.)

* **Částečně aktualizované hlášené vlastnosti**. Tato operace umožňuje částečnou aktualizaci hlášených vlastností aktuálně připojeného zařízení. Tato operace používá stejný formát aktualizace JSON, který back-end řešení používá pro částečnou aktualizaci požadovaných vlastností.

* **Sledujte požadované vlastnosti**. V aktuálně připojeném zařízení se můžete rozhodnout, že se při jejich výskytu informují o aktualizacích požadovaných vlastností. Zařízení obdrží stejnou formu aktualizace (částečnou nebo úplnou náhradou) spuštěnou back-end řešení.

Všechny předchozí operace vyžadují oprávnění **DeviceConnect** , jak je definováno v [ovládacím prvku přístup k IoT Hub](iot-hub-devguide-security.md).

Sady [SDK pro zařízení Azure IoT](iot-hub-devguide-sdks.md) usnadňují používání předchozích operací z mnoha jazyků a platforem. Další informace o podrobnostech IoT Hub primitiv pro požadovanou synchronizaci vlastností najdete v tématu [Postup opětovného připojení zařízení](iot-hub-devguide-device-twins.md#device-reconnection-flow).

## <a name="tags-and-properties-format"></a>Formát značek a vlastností

Značky, požadované vlastnosti a hlášené vlastnosti jsou objekty JSON s následujícími omezeními:

* **Keys**: všechny klíče v objektech JSON jsou v kódování UTF-8, Velká a malá písmena a dlouhé až 1 KB. Povolené znaky vyloučí řídicí znaky UNICODE (segmenty C0 a C1), a `.` , a `$` SP.

* **Hodnoty**: všechny hodnoty v objektech JSON můžou být z následujících typů JSON: Boolean, Number, String, Object. Pole jsou také podporována.

    * Celá čísla můžou mít minimální hodnotu-4503599627370496 a maximální hodnotu 4503599627370495.

    * Řetězcové hodnoty mají kódování UTF-8 a můžou mít maximální délku 4 KB.

* **Hloubka**: maximální hloubka objektů JSON ve značkách, požadovaných vlastností a hlášených vlastností je 10. Například následující objekt je platný:

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

## <a name="device-twin-size"></a>Velikost vlákna zařízení

IoT Hub vynutilo omezení velikosti 8 KB na hodnotě a `tags` omezení velikosti 32 KB na hodnotu `properties/desired` a `properties/reported` . Tyto součty jsou výhradně prvky jen pro čtení, například, `$etag` `$version` a `$metadata/$lastUpdated` .

Velikost vlákna je vypočítána následujícím způsobem:

* Pro každou vlastnost v dokumentu JSON IoT Hub kumulativně COMPUTE a přidá délku klíče a hodnoty vlastnosti.

* Klíče vlastností se považují za řetězce kódované pomocí UTF8.

* Jednoduché hodnoty vlastností se považují za řetězce kódované v kódování UTF8, číselné hodnoty (8 bajtů) nebo logické hodnoty (4 bajty).

* Velikost řetězců zakódovaných v kódování UTF8 se vypočítává pomocí počítání všech znaků, kromě řídicích znaků UNICODE (segmenty C0 a C1).

* Hodnoty komplexních vlastností (vnořené objekty) se vypočítávají na základě agregované velikosti klíčů vlastností a hodnot vlastností, které obsahují.

IoT Hub se odmítne s chybou všech operací, které by zvýšily velikost `tags` , `properties/desired` nebo `properties/reported` dokumentů nad rámec limitu.

## <a name="device-twin-metadata"></a>Zařízení s dvojitou metadatou

IoT Hub udržuje časové razítko poslední aktualizace pro každý objekt JSON v požadovaném a hlášeném vlastnosti zařízení. Časová razítka jsou v UTC a kódovaná ve [](https://en.wikipedia.org/wiki/ISO_8601) formátu ISO8601 `YYYY-MM-DDTHH:MM:SS.mmmZ` .

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
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
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

Požadovaná a nahlášené vlastnosti zařízení neobsahují značky ETag, ale mají `$version` hodnotu, která je zaručena k přírůstkové. Podobně jako ETag může být verze využívána stranou aktualizace k vymáhání konzistence aktualizací. Například aplikace zařízení pro hlášené vlastnosti nebo back-end řešení pro požadovanou vlastnost.

Verze jsou užitečné také v případě, že pozorovatelský Agent (například aplikace zařízení, který je pozorovatelem požadovaných vlastností) musí sjednotit Races mezi výsledkem operace načtení a oznámením o aktualizacích. Další informace najdete v [části tok opětovného připojení zařízení](iot-hub-devguide-device-twins.md#device-reconnection-flow) .

## <a name="device-reconnection-flow"></a>Tok opětovného připojení zařízení

IoT Hub nezachovává u odpojených zařízení oznámení o aktualizacích požadovaných vlastností. Postupuje podle toho, že zařízení, které se připojuje, musí kromě přihlášení k odběru oznámení o aktualizacích načítat úplný dokument požadovaných vlastností. Aby bylo možné Races mezi oznámeními aktualizací a úplným načítáním, je nutné zajistit následující tok:

1. Aplikace zařízení se připojuje ke centru IoT.
2. Aplikace zařízení se přihlásí k odběru požadovaných vlastností oznámení aktualizací.
3. Aplikace zařízení načte celý dokument pro požadované vlastnosti.

Aplikace zařízení může ignorovat všechna oznámení s `$version` nižší nebo rovnou verzi úplného načteného dokumentu. Tento přístup je možný, protože IoT Hub zaručuje, že se verze vždy zvýší.

> [!NOTE]
> Tato logika je už implementovaná v sadách [SDK pro zařízení Azure IoT](iot-hub-devguide-sdks.md). Tento popis je vhodný jenom v případě, že aplikace zařízení nemůže používat žádné sady SDK pro zařízení Azure IoT a musí naprogramovat rozhraní MQTT přímo.
> 

## <a name="additional-reference-material"></a>Další referenční materiály

Další referenční témata v IoT Hub příručce pro vývojáře zahrnují:

* Článek [IoT Hubch koncových bodů](iot-hub-devguide-endpoints.md) popisuje různé koncové body, které jednotlivé služby IoT Hub zpřístupňují pro operace run-time a Management.

* Článek o [omezeních a kvótách](iot-hub-devguide-quotas-throttling.md) popisuje kvóty, které se vztahují na službu IoT Hub, a chování omezování, které se má při používání služby očekávat.

* Článek sady [SDK pro zařízení a služby Azure IoT](iot-hub-devguide-sdks.md) obsahuje různé jazykové sady SDK, které můžete použít při vývoji aplikací pro zařízení i služby, které komunikují s IoT Hub.

* [IoT Hub dotazovací jazyk pro vlákna zařízení, úlohy a článek směrování zpráv](iot-hub-devguide-query-language.md) popisuje dotazovací jazyk IoT Hub, který můžete použít k načtení informací z IoT Hub o nečinnosti zařízení a úlohách.

* Článek [podpory IoT Hub MQTT](iot-hub-mqtt-support.md) poskytuje další informace o podpoře IoT Hub pro protokol MQTT.

## <a name="next-steps"></a>Další kroky

Seznámili jste se s tím, že jste se dozvěděli o nevlákenách zařízení. možná vás zajímá následující témata IoT Hub příručka pro vývojáře:

* [Pochopení a použití vláken v modulech v IoT Hub](iot-hub-devguide-module-twins.md)
* [Vyvolání přímé metody v zařízení](iot-hub-devguide-direct-methods.md)
* [Plánování úloh na několika zařízeních](iot-hub-devguide-jobs.md)

Některé z konceptů popsaných v tomto článku si můžete vyzkoušet v následujících kurzech IoT Hub:

* [Jak používat dvojitě dopředné zařízení](iot-hub-node-node-twin-getstarted.md)
* [Jak používat zdvojené vlastnosti zařízení](tutorial-device-twins.md)
* [Správa zařízení s využitím sady Azure IoT Tools pro VS Code](iot-hub-device-management-iot-toolkit.md)
