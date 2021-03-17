---
title: Související projekty GitHubu pro Azure API pro FHIR
description: Vypíše všechna úložiště Open Source (GitHub) pro Azure API pro FHIR.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/01/2021
ms.author: ginle
ms.openlocfilehash: 9977765183bd567377592631d65f3e548bef4b34
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103548010"
---
# <a name="related-github-projects"></a>Související projekty GitHubu

Máme spoustu open source projektů na GitHubu, které vám poskytnou zdrojový kód a pokyny k nasazení služeb pro různé účely. Vždycky jste připraveni navštívit naše úložiště GitHub, abyste se seznámili s našimi funkcemi a produkty a experimentovat s nimi. 

## <a name="fhir-server"></a>Server FHIR
* [Microsoft/fhir-Server](https://github.com/microsoft/fhir-server/): Open Source Server fhir, který je základem pro Azure API pro fhir
* Pokud chcete zobrazit nejnovější verze, přečtěte si [poznámky k verzi](https://github.com/microsoft/fhir-server/releases) .
* [Microsoft/fhir-Server-Samples](https://github.com/microsoft/fhir-server-samples): ukázkové prostředí

## <a name="data-conversion--anonymization"></a>Konverze dat & anonymity

#### <a name="fhir-converter"></a>FHIR konvertor
* [Microsoft/FHIR-Converter](https://github.com/microsoft/FHIR-Converter): převodní nástroj pro překlad starších formátů dat do FHIR
* Integrace s rozhraním API Azure pro FHIR a FHIR serverem pro Azure v podobě operace $convert-data
* Průběžná vylepšení OSS a nepřetržitá integrace na servery FHIR
 
#### <a name="fhir-converter---vs-code-extension"></a>FHIR konvertor – rozšíření VS Code
* [Microsoft/FHIR-Tools-for-anonymita](https://github.com/microsoft/FHIR-Tools-for-Anonymization): sada nástrojů pro pomoc s daty (ve formátu FHIR) – anonymita
* Integrace s rozhraním API Azure pro FHIR a FHIR serverem pro Azure ve formě neidentifikovaných exportů

#### <a name="fhir-tools-for-anonymization"></a>FHIR nástroje pro anonymitu
* [Microsoft/VSCode-azurehealthcareapis-Tools](https://github.com/microsoft/vscode-azurehealthcareapis-tools): rozšíření vs Code, které obsahuje kolekci nástrojů pro práci s rozhraními API pro zdravotní péče Azure
* Vydáno pro Visual Studio Marketplace
* Používá se k vytváření šablon kapalin pro použití v převaděči FHIR.

## <a name="iot-connector"></a>Konektor IoT

#### <a name="integration-with-iot-hub-and-iot-central"></a>Integrace s IoT Hub a IoT Central
* [Microsoft/iomt-fhir](https://github.com/microsoft/iomt-fhir): integrace s IoT Hub nebo IoT Central pro fhir s normalizací dat a fhir konverze normalizovaných dat
* Normalizace: informace o datech zařízení se extrahují do společného formátu pro další zpracování.
* FHIR Conversion: normalizovaná a seskupená data jsou namapována na FHIR. Pozorování jsou vytvořena nebo aktualizována podle nakonfigurovaných šablon a propojená se zařízením a pacientem.
* [Nástroje, které vám pomůžou vytvořit mapu konverzace](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper): Vizualizujte konfiguraci mapování pro normalizaci vstupních dat zařízení a Transformujte ji na prostředky FHIR. Vývojáři mohou pomocí tohoto nástroje upravit a otestovat mapování, mapování zařízení a mapování FHIR a exportovat je pro odesílání do konektoru IoT v Azure Portal.

#### <a name="healthkit-and-fhir-integration"></a>Integrace HealthKit a FHIR
* [Microsoft/HealthKit-on-fhir](https://github.com/microsoft/healthkit-on-fhir): knihovna SWIFT, která automatizuje export dat Apple HealthKit na server fhir

 