---
title: Import aktualizací do aktualizace zařízení pro IoT Hub-Schema a další informace | Microsoft Docs
description: Schéma a další související informace (včetně objektů), které se používají při importu aktualizací do aktualizace zařízení pro IoT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/25/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 13044b8f087b403f83516a32a490d2dee8db700f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054602"
---
# <a name="importing-updates-into-device-update-for-iot-hub---schema-and-other-information"></a>Import aktualizací do aktualizace zařízení pro IoT Hub – schéma a další informace
Pokud chcete importovat aktualizaci do aktualizace zařízení pro IoT Hub, ujistěte se, že jste nejdřív zkontrolovali [Koncepty](import-concepts.md) [a návod.](import-update.md) Pokud vás zajímá podrobnosti schématu používaného při vytváření manifestu importu a také informace o souvisejících objektech, přečtěte si níže.

## <a name="import-manifest-schema"></a>Importovat schéma manifestu

| Název | Typ | Popis | Omezení |
| --------- | --------- | --------- | --------- |
| UpdateID naformátovat | `UpdateId` předmětů | Aktualizujte identitu. |
| Typ aktualizace | řetězec | Typ aktualizace: <br/><br/> * Určete `microsoft/apt:1` , kdy se má provést aktualizace založená na balíčku pomocí agenta reference.<br/> * Zadejte `microsoft/swupdate:1` při provádění aktualizace na základě bitové kopie pomocí referenčního agenta.<br/> * Určete `microsoft/simulator:1` při použití simulátoru ukázkového agenta.<br/> * Zadejte vlastní typ při vývoji vlastního agenta. | Formát: <br/> `{provider}/{type}:{typeVersion}`<br/><br/> Maximálně 32 znaků celkem |
| InstalledCriteria | řetězec | Řetězec, který je interpretován agentem k určení, jestli se aktualizace úspěšně použila:  <br/> * Zadejte **hodnotu** SWVersion pro typ aktualizace `microsoft/swupdate:1` .<br/> * Zadejte `{name}-{version}` pro typ aktualizace `microsoft/apt:1` , jehož název a verzi jsou získány ze souboru apt.<br/> * Zadejte hodnotu hash souboru aktualizace pro typ aktualizace `microsoft/simulator:1` .<br/> * Zadejte vlastní řetězec při vývoji vlastního agenta.<br/> | Maximálně 64 znaků |
| Kompatibilita | Pole `CompatibilityInfo` objektů | Informace o kompatibilitě zařízení kompatibilního s touto aktualizací. | Maximálně 10 položek |
| CreatedDateTime | Datum a čas | Datum a čas, kdy byla aktualizace vytvořena. | Formát data a času s oddělovači ISO 8601, v UTC |
| ManifestVersion | řetězec | Importovat verzi schématu manifestu. Zadejte `2.0` , který bude kompatibilní s `urn:azureiot:AzureDeviceUpdateCore:1` rozhraním a rozhraním `urn:azureiot:AzureDeviceUpdateCore:4` . | Musí být `2.0` |
| Soubory | Pole `File` objektů | Aktualizovat soubory datové části | Maximálně 5 souborů |

## <a name="updateid-object"></a>Objekt UpdateID naformátovat

| Název | Typ | Popis | Omezení |
| --------- | --------- | --------- | --------- |
| Poskytovatel | řetězec | Část poskytovatele identity aktualizace. | 1-64 znaků, alfanumerických, teček a pomlček. |
| Název | řetězec | Název součásti identity aktualizace. | 1-64 znaků, alfanumerických, teček a pomlček. |
| Verze | verze | Část verze identity aktualizace. | 2 až 4 část, číslo verze oddělené tečkou v rozmezí 0 až 2147483647. Počáteční nuly budou vynechány. |

## <a name="file-object"></a>Objekt File

| Název | Typ | Popis | Omezení |
| --------- | --------- | --------- | --------- |
| Bitmap | řetězec | Název souboru | Musí být jedinečné v rámci aktualizace. |
| SizeInBytes | Int64 | Velikost souboru v bajtech | Maximálně 800 MB na jeden soubor nebo 800 MB souhrnně na aktualizaci |
| Hodnoty hash | `Hashes` předmětů | Objekt JSON obsahující hodnoty hash (y) souboru |

## <a name="compatibilityinfo-object"></a>Objekt CompatibilityInfo

| Název | Typ | Popis | Omezení |
| --- | --- | --- | --- |
| DeviceManufacturer | řetězec | Výrobce zařízení, se kterým je aktualizace kompatibilní. | 1-64 znaků, alfanumerických, teček a pomlček. |
| DeviceModel | řetězec | Model zařízení, se kterým je aktualizace kompatibilní. | 1-64 znaků, alfanumerických, teček a pomlček. |

## <a name="hashes-object"></a>Hash – objekt

| Název | Požaduje se | Typ | Popis |
| --------- | --------- | --------- | --------- |
| SHA256 | True | řetězec | Hodnota hash souboru s kódováním base64 pomocí algoritmu SHA-256. |

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [konceptech importu](./import-concepts.md).

Až budete připraveni, vyzkoušejte si [Průvodce importem How-To](./import-update.md), který vás provede procesem importu krok za krokem.
