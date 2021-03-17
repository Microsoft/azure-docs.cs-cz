---
title: Aktualizace zařízení pro IoT Hub manifest aktualizace | Microsoft Docs
description: Informace o tom, jak se během aktualizace odesílají do zařízení vlastnosti ze služby aktualizace zařízení
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: bbd39f7752a1a482350a7231a0bb5a3710591b1d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030677"
---
# <a name="device-update-for-iot-hub-update-manifest"></a>Aktualizace zařízení pro IoT Hub manifest aktualizace

## <a name="overview"></a>Přehled

Aktualizace zařízení pro IoT Hub používá _manifest aktualizace_ k oznamování akcí a také metadatům podporujícím tyto akce prostřednictvím vlastností rozhraní [AzureDeviceUpdateCore. OrchestratorMetadata: 4](./device-update-plug-and-play.md).
Tento dokument popisuje základy způsobu, jakým se `updateManifest` vlastnost v `AzureDeviceUpdateCore.OrchestratorMetadata:4` rozhraní používá v agentovi aktualizace zařízení. `AzureDeviceUpdateCore.OrchestratorMetadata:4`Vlastnosti rozhraní jsou odesílány z aktualizace zařízení služby IoT Hub do agenta aktualizace zařízení. `updateManifest`Je serializovaným objektem JSON, který je analyzován agentem aktualizace zařízení.

### <a name="an-example-update-manifest"></a>Příklad manifestu aktualizace

```JSON
{
    "manifestVersion": "1",
    "updateId": {
        "provider": "DuTest",
        "name": "DuTestUser",
        "version": "2020.611.534.16"
    },
    "updateType": "microsoft/swupdate:1",
    "installedCriteria": "1.0",
    "files": {
        "00000": {
            "fileName": "image.swu",
            "sizeInBytes": 256000,
            "hashes": {
                "sha256": "IhIIxBJpLfazQOk/PVi6SzR7BM0jf4HDqw+6gdZ3vp8="
            }
        }
    },
    "createdDateTime": "2020-06-12T00:38:13.9350278"
}
```

Účelem manifestu aktualizace je popsání obsahu aktualizace, konkrétně její identity, typu, nainstalovaných kritérií a metadat souborů aktualizace. Kromě toho manifest aktualizace je kryptograficky podepsán, aby mohl agent aktualizace zařízení ověřit pravost. Další informace o způsobu použití manifestu aktualizace k bezpečnému importu obsahu najdete v dokumentaci [zabezpečení aktualizace zařízení](./device-update-security.md) .

## <a name="import-manifest-vs-update-manifest"></a>Importovat manifest vs aktualizovat manifest

Je důležité porozumět rozdílům mezi manifesty importu a koncepty manifestu aktualizace v aktualizaci zařízení pro IoT Hub. 
* [Manifest importu](./import-concepts.md) vytvoří hráč, který vytvoří odpovídající aktualizaci. Popisuje obsah aktualizace, která se naimportuje do aktualizace zařízení pro IoT Hub. 
* Manifest aktualizace je automaticky vygenerován aktualizací zařízení pro službu IoT Hub pomocí některých vlastností, které byly definovány v manifestu import. Slouží ke sdělování relevantních informací agentovi aktualizace zařízení během procesu aktualizace. 

Každý typ manifestu má vlastní schéma a verzi schématu.

## <a name="update-manifest-properties"></a>Aktualizovat vlastnosti manifestu

Definice vysoké úrovně vlastností manifestu aktualizace lze nalézt v části definice rozhraní, které se [zde](./device-update-plug-and-play.md)nacházejí. Abychom vám pomohli získat hlubší kontext, podíváme se na vlastnosti a jak se používají v systému.

### <a name="updateid"></a>UpdateID naformátovat

Obsahuje `provider` , `name` , a `version` , který představuje přesnou aktualizaci zařízení pro IoT Hub identitu aktualizace použitou k určení kompatibilních zařízení pro danou aktualizaci.

### <a name="updatetype"></a>Typ aktualizace

Představuje typ aktualizace, která je zpracována konkrétním typem obslužné rutiny aktualizace. Postupuje podle formy `microsoft/swupdate:1` aktualizace na základě bitové kopie a `microsoft/apt:1` pro aktualizaci založenou na balíčku (viz `Update Handler Types` část níže).

### <a name="installedcriteria"></a>installedCriteria

Řetězec obsahující informace, které vyžaduje obslužná rutina aktualizace agenta aktualizace zařízení k určení, jestli je aktualizace na zařízení nainstalovaná. `Update Handler Types`Oddíl dokumentuje formát `installedCriteria` pro každý typ aktualizace podporovaný aktualizací zařízení pro IoT Hub.

### <a name="files"></a>files

Přikáže agentovi aktualizace zařízení, které soubory se mají stáhnout, a hodnotu hash, která se použije k ověření, že se soubory stáhly správně.
Tady je bližší pohled na `files` obsah vlastností:

```json
"files":{
        <FILE_ID_STRING>:{
            "fileName":<STRING>,
            "sizeInBytes":<INTEGER>,
            "hashes":{
                <HASH-TYPE>:<HASH-STRING>
            }
        }
    }
```

Mimo `updateManifest` je `fileUrls` pole objektu JSON.

```json
"fileUrls":{
      <FILE_ID_STRING>: <URL-in-String-Format>
 }
```

V `FILE_ID_STRING` rámci `fileUrls` a `files` jsou stejné (například "0000" v " `files` má adresu URL v" 0000 "v rámci `fileUrls` ).

### <a name="manifestversion"></a>manifestVersion

Řetězec, který představuje verzi schématu.

## <a name="update-handler-types"></a>Aktualizovat typy obslužných rutin

|Update – metoda|Aktualizovat typ obslužné rutiny|Typ aktualizace|Nainstalovaná kritéria|Očekávané soubory pro publikování|
|-------------|-------------------|----------|-----------------|--------------|
|Na základě bitové kopie|SWUpdate|"Microsoft/SWUpdate: verze"|Referenční image uloží pomocný parametr jeho verze do souboru/etc/adu-Version.  |soubor. SWU, který obsahuje obrázek SWUpdate|
|Založený na balíčku|APT|"Microsoft/apt: verze"|`<name>` + "-" + `<version>` (definované vlastnosti v souboru MANIFESTU apt|`<APT Update Manifest>`. JSON, který obsahuje seznam APT a konfigurace balíčků|

