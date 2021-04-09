---
title: Načíst protokoly IoT Edge – Azure IoT Edge
description: IoT Edge načítání protokolů modulu a nahrání do Azure Blob Storage.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/12/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: c06120d1a2e8aa6aa0c006c6f40fed6fab44c5b7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200700"
---
# <a name="retrieve-logs-from-iot-edge-deployments"></a>Načtení protokolů z nasazení IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Načtěte protokoly z nasazení IoT Edge bez potřeby fyzického přístupu nebo přístupu SSH k zařízení pomocí přímých metod, které jsou součástí modulu IoT Edge agent. Přímé metody jsou implementovány v zařízení a je možné je vyvolávat z cloudu. Agent IoT Edge obsahuje přímé metody, které vám pomůžou vzdáleně monitorovat a spravovat vaše IoT Edge zařízení. Přímé metody popsané v tomto článku jsou všeobecně dostupné ve verzi 1.0.10.

Chcete-li získat další informace o přímých metodách, jejich použití a způsobu jejich implementace ve vlastních modulech, přečtěte si téma [pochopení a vyvolání přímých metod z IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md).

Názvy těchto přímých metod se zpracovávají bez rozlišení velkých a malých písmen.

## <a name="recommended-logging-format"></a>Doporučený formát protokolování

I když není to nutné, pro zajištění nejlepší kompatibility s touto funkcí je doporučený formát protokolování:

```
<{Log Level}> {Timestamp} {Message Text}
```

`{Timestamp}` by měl být formátován jako `yyyy-MM-dd hh:mm:ss.fff zzz` a `{Log Level}` by měl postupovat podle následující tabulky, která odvozuje své úrovně závažnosti od [kódu závažnosti ve standardu syslog](https://wikipedia.org/wiki/Syslog#Severity_level).

| Hodnota | Severity |
|-|-|
| 0 | Zásah |
| 1 | Výstrahy |
| 2 | Kritické |
| 3 | Chyba |
| 4 | Upozornění |
| 5 | Šestiměsíční |
| 6 | Informační |
| 7 | Ladění |

[Třída protokolovacího nástroje v IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-util/src/Microsoft.Azure.Devices.Edge.Util/Logger.cs) slouží jako kanonická implementace.

## <a name="retrieve-module-logs"></a>Načíst protokoly modulů

K načtení protokolů IoT Edge modulu použijte metodu Direct **GetModuleLogs** .

Tato metoda přijímá datovou část JSON s následujícím schématem:

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "string",
                "until": "string",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| Název | Typ | Description |
|-|-|-|
| schemaVersion | řetězec | Nastavit na `1.0` |
| položek | Pole JSON | Pole s `id` a `filter` řazenými kolekcemi členů. |
| ID | řetězec | Regulární výraz, který poskytuje název modulu. Může odpovídat několika modulům na hraničním zařízení. Očekává se formát [regulárních výrazů .NET](/dotnet/standard/base-types/regular-expressions) . |
| filter | Oddíl JSON | Filtry protokolu, které se mají použít pro moduly, které odpovídají `id` regulárnímu výrazu v řazené kolekci členů. |
| kompatibilní | integer | Počet řádků protokolu v minulosti pro načtení od nejnovějšího. Volitelné. |
| doby | řetězec | Jenom od této doby vrátí protokoly, jako je doba trvání (1 d, 90 m, 2 dny 3 hodiny 2 minuty), časové razítko rfc3339 nebo UNIXové časové razítko.  Pokud `tail` `since` jsou zadány oba a, protokoly se načtou pomocí `since` hodnoty jako první. Pak se `tail` hodnota aplikuje na výsledek a vrátí se konečný výsledek. Volitelné. |
| Vrátí | řetězec | Vrátí jenom protokoly před určenou dobu, jako rfc3339 časové razítko, časové razítko v systému UNIX nebo doba trvání (1 d, 90 m, 2 dny 3 hodiny 2 minuty). Volitelné. |
| úroveň protokolování | integer | Filtrovat řádky protokolu menší nebo rovny zadané úrovni protokolu. Řádky protokolu by měly následovat za doporučeným formátem protokolování a používáním standardu [úrovně závažnosti SYSLOG](https://en.wikipedia.org/wiki/Syslog#Severity_level) . Volitelné. |
| regulární | řetězec | Filtruje řádky protokolu, které mají obsah odpovídající zadanému regulárnímu výrazu pomocí formátu [regulárních výrazů .NET](/dotnet/standard/base-types/regular-expressions) . Volitelné. |
| encoding | řetězec | `gzip` nebo `none`. Výchozí je `none`. |
| Třída | řetězec | `json` nebo `text`. Výchozí je `text`. |

> [!NOTE]
> Pokud obsah protokolů překročí limit velikosti odpovědí přímých metod, který je aktuálně 128 KB, vrátí odpověď chybu.

Úspěšné načtení protokolů vrátí **"stav": 200** následovaný datovou částí, která obsahuje protokoly načtené z modulu, filtrované podle nastavení, které zadáte v žádosti.

Například:

```azurecli
az iot hub invoke-module-method --method-name 'GetModuleLogs' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
'
```

V Azure Portal volejte metodu s názvem metody `GetModuleLogs` a následující datovou částí JSON:

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Vyvolat přímo metodu GetModuleLogs v Azure Portal](./media/how-to-retrieve-iot-edge-logs/invoke-get-module-logs.png)

Výstup rozhraní příkazového řádku (CLI) můžete také přesměrovat na nástroje pro Linux, jako je třeba [gzip](https://en.wikipedia.org/wiki/Gzip), a zpracovat tak komprimovanou odpověď. Například:

```azurecli
az iot hub invoke-module-method \
  --method-name 'GetModuleLogs' \
  -n <hub name> \
  -d <device id> \
  -m '$edgeAgent' \
  --method-payload '{"contentType": "text","schemaVersion": "1.0","encoding": "gzip","items": [{"id": "edgeHub","filter": {"since": "2d","tail": 1000}}],}' \
  -o tsv --query 'payload[0].payloadBytes' \
  | base64 --decode \
  | gzip -d
```

## <a name="upload-module-logs"></a>Nahrát protokoly modulů

Použijte metodu **UploadModuleLogs** Direct k odeslání požadovaných protokolů do zadaného kontejneru Azure Blob Storage.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> Pokud chcete nahrávat protokoly ze zařízení za zařízením brány, budete muset mít [proxy rozhraní API a moduly BLOB Storage](how-to-configure-api-proxy-module.md) nakonfigurované na nejvyšší vrstvě zařízení. Tyto moduly směrují protokoly ze zařízení nižší vrstvy přes vaše zařízení brány do úložiště v cloudu.

::: moniker-end

Tato metoda přijímá datovou část JSON podobnou **GetModuleLogs** a přidání klíče "sasUrl":

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "Full path to SAS URL",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "string",
                "until": "string",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| Název | Typ | Description |
|-|-|-|
| sasURL | řetězec (URI) | [Adresa URL sdíleného přístupového podpisu s přístupem pro zápis do kontejneru Azure Blob Storage](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer). |

Úspěšná žádost o nahrání protokolů vrátí **"status": 200** následovaný datovou částí s následujícím schématem:

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Název | Typ | Description |
|-|-|-|
| status | řetězec | Jedna z `NotStarted` , `Running` , `Completed` , `Failed` , nebo `Unknown` . |
| zpráva | řetězec | Zpráva v případě chyby, v opačném případě prázdný řetězec. |
| correlationId | řetězec   | ID, které se má dotazovat na stav žádosti o nahrání |

Například:

Následující vyvolání nahraje poslední řádky protokolu 100 ze všech modulů v komprimovaném formátu JSON:

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": ".*",
                "filter": {
                    "tail": 100
                }
            }
        ],
        "encoding": "gzip",
        "contentType": "json"
    }
'
```

Následující vyvolání nahraje poslední řádky protokolu 100 z edgeAgent a edgeHub s posledními řádky protokolu 1000 z modulu tempSensor v nekomprimovaném textovém formátu:

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": "edge",
                "filter": {
                    "tail": 100
                }
            },
            {
                "id": "tempSensor",
                "filter": {
                    "tail": 1000
                }
            }
        ],
        "encoding": "none",
        "contentType": "text"
    }
'
```

V Azure Portal volejte metodu s názvem metody `UploadModuleLogs` a následující datovou část JSON po naplnění sasURL s vašimi informacemi:

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "<sasUrl>",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Vyvolat přímo metodu UploadModuleLogs v Azure Portal](./media/how-to-retrieve-iot-edge-logs/invoke-upload-module-logs.png)

## <a name="upload-support-bundle-diagnostics"></a>Nahrát podporu diagnostiky sady prostředků

Pomocí metody **UploadSupportBundle** Direct nahrajte do dostupného kontejneru Azure Blob Storage a nahrajte do něj soubor zip IoT Edge protokolů. Tato přímá metoda spustí na [`iotedge support-bundle`](./troubleshoot.md#gather-debug-information-with-support-bundle-command) zařízení IoT Edge příkaz pro získání protokolů.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> Pokud chcete nahrávat protokoly ze zařízení za zařízením brány, budete muset mít [proxy rozhraní API a moduly BLOB Storage](how-to-configure-api-proxy-module.md) nakonfigurované na nejvyšší vrstvě zařízení. Tyto moduly směrují protokoly ze zařízení nižší vrstvy přes vaše zařízení brány do úložiště v cloudu.

::: moniker-end

Tato metoda přijímá datovou část JSON s následujícím schématem:

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

| Název | Typ | Description |
|-|-|-|
| schemaVersion | řetězec | Nastavit na `1.0` |
| sasURL | řetězec (URI) | [Adresa URL sdíleného přístupového podpisu s přístupem k zápisu do služby Azure Blob Storage Container](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer) |
| doby | řetězec | Jenom od této doby vrátí protokoly, jako je doba trvání (1 d, 90 m, 2 dny 3 hodiny 2 minuty), časové razítko rfc3339 nebo UNIXové časové razítko. Volitelné. |
| Vrátí | řetězec | Vrátí jenom protokoly před určenou dobu, jako rfc3339 časové razítko, časové razítko v systému UNIX nebo doba trvání (1 d, 90 m, 2 dny 3 hodiny 2 minuty). Volitelné. |
| edgeRuntimeOnly | boolean | Pokud má hodnotu true, vrátí jenom protokoly z agenta Edge, centra Edge a démona zabezpečení Edge. Výchozí hodnota: false.  Volitelné. |

> [!IMPORTANT]
> Sada podpory IoT Edge může obsahovat identifikovatelné osobní údaje.

Úspěšná žádost o nahrání protokolů vrátí **"status": 200** následovaný datovou částí se stejným schématem, jako je **UploadModuleLogs** odpověď:

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Název | Typ | Description |
|-|-|-|
| status | řetězec | Jedna z `NotStarted` , `Running` , `Completed` , `Failed` , nebo `Unknown` . |
| zpráva | řetězec | Zpráva v případě chyby, v opačném případě prázdný řetězec. |
| correlationId | řetězec   | ID, které se má dotazovat na stav žádosti o nahrání |

Například:

```azurecli
az iot hub invoke-module-method --method-name 'UploadSupportBundle' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
'
```

V Azure Portal volejte metodu s názvem metody `UploadSupportBundle` a následující datovou část JSON po naplnění sasURL s vašimi informacemi:

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

![Vyvolat přímo metodu UploadSupportBundle v Azure Portal](./media/how-to-retrieve-iot-edge-logs/invoke-upload-support-bundle.png)

## <a name="get-upload-request-status"></a>Získat stav žádosti o nahrání

K dotazování na stav žádosti o nahrání protokolů použijte metodu **GetTaskStatus** Direct. Datová část požadavku **GetTaskStatus** používá `correlationId` k získání stavu úlohy žádost o nahrání protokolů. V `correlationId` reakci na volání přímé metody **UploadModuleLogs** se vrátí.

Tato metoda přijímá datovou část JSON s následujícím schématem:

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

Úspěšná žádost o nahrání protokolů vrátí **"status": 200** následovaný datovou částí se stejným schématem, jako je **UploadModuleLogs** odpověď:

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Název | Typ | Description |
|-|-|-|
| status | řetězec | Jedna z `NotStarted` , `Running` , `Completed` , `Failed` , nebo `Unknown` . |
| zpráva | řetězec | Zpráva v případě chyby, v opačném případě prázdný řetězec. |
| correlationId | řetězec   | ID, které se má dotazovat na stav žádosti o nahrání |

Například:

```azurecli
az iot hub invoke-module-method --method-name 'GetTaskStatus' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
'
```

V Azure Portal volejte metodu s názvem metody `GetTaskStatus` a následující datovou část JSON po naplnění identifikátoru GUID pomocí vašich informací:

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

![Vyvolat přímo metodu GetTaskStatus v Azure Portal](./media/how-to-retrieve-iot-edge-logs/invoke-get-task-status.png)

## <a name="next-steps"></a>Další kroky

[Vlastnosti IoT Edge agenta a modulu IoT Edge centra pro vlákna](module-edgeagent-edgehub.md)
