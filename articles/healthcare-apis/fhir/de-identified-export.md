---
title: Export neidentifikovaných dat (Preview) pro Azure API pro FHIR
description: Tento článek popisuje, jak nastavit a použít neidentifikovaný export.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 9/28/2020
ms.author: matjazl
ms.openlocfilehash: 60a2a41a8005e8bd0fbc313c9a177d54df6dac5e
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018292"
---
# <a name="exporting-de-identified-data-preview"></a>Export neidentifikovaných dat (Preview)

> [!Note] 
> Výsledky při použití exportu s označením de-identifikovaný se budou lišit v závislosti na faktorech, jako jsou data a funkce vybrané zákazníkem. Společnost Microsoft nedokáže vyhodnotit neidentifikované výstupní výstupy nebo stanovit možnost přijmout pro případy použití zákazníka a požadavky na dodržování předpisů. Zrušení identifikovaných vývozů není zaručené splňovat žádné konkrétní zákonné požadavky nebo předpisy pro dodržování předpisů.

Příkaz $export lze také použít k exportu neidentifikovaných dat ze serveru FHIR. Používá modul pro vybírání z [FHIR nástrojů pro anonymitu](https://github.com/microsoft/FHIR-Tools-for-Anonymization)a v parametrech dotazů přebírá podrobnosti o konfiguraci anonymity. Můžete vytvořit vlastní konfigurační soubor anonymity nebo použít jako výchozí bod [Ukázkový konfigurační soubor](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) pro metodu HIPAA Safe přístavu. 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

> [!Note] 
> V současné době rozhraní API Azure pro FHIR podporuje pouze neidentifikovaný export na úrovni systému ($export).

|Parametr dotazu            | Příklad |Volitelnost| Description|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.jsna|Požadováno pro neidentifikovaný export |Název konfiguračního souboru [Tady](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format)najdete formát konfiguračního souboru. Tento soubor by měl být uložený v kontejneru s názvem **Anonyme** v rámci stejného účtu úložiště Azure, který je nakonfigurovaný jako umístění exportu. |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|Volitelné pro neidentifikovaný export|Toto je značka ETag konfiguračního souboru. Značku ETag můžete získat pomocí Průzkumníka služby Azure Storage z vlastnosti objektu BLOB.|

> [!IMPORTANT]
> Nezpracovaný export i neidentifikovaný zápis pro export do stejného účtu úložiště Azure, který je zadaný jako součást konfigurace exportu. Doporučuje se používat různé kontejnery odpovídající různým neidentifikovaným konfiguracím a spravovat přístup uživatelů na úrovni kontejneru.