---
title: Spuštění exportu vyvoláním příkazu $export v rozhraní Azure API pro FHIR
description: Tento článek popisuje, jak nastavit a použít neidentifikovaný export.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: 83509b5f452ab7cf88774561c12d7aa2cf3b46cf
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482313"
---
# <a name="how-to-export-fhir-data"></a>Jak exportovat FHIR data

Pokud chcete konfigurovat nastavení exportu a vytvořit účet úložiště Azure, přečtěte si ho [tady](configure-export-data.md).

## <a name="exporting-fhir-resources-using-export-command"></a>Export prostředků FHIR pomocí příkazu $export

Po nakonfigurování rozhraní API Azure pro FHIR pro export teď můžeme použít příkaz $export a vyexportovat data ze služby do účtu úložiště, který jste zadali při konfiguraci exportu. Pokud se chcete dozvědět, jak vyvolat $export příkaz na serveru FHIR, přečtěte si dokumentaci ke specifikaci $export na adrese [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . 

Příkaz $export v rozhraní API Azure pro FHIR přebírá volitelný parametr _ \_ Conatiner_ , který se dá použít k určení kontejneru v nakonfigurovaném účtu úložiště, do kterého se mají data exportovat.

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

> [!IMPORTANT]
> Všimněte si, že v současné době Azure API pro FHIR podporuje pouze export na úrovni systému, jak je definováno ve specifikaci $export Specification [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . Také platí, _ \_ že_ v současné době je podporována pouze parametr dotazu.

## <a name="exporting-de-identified-data-preview"></a>Export neidentifikovaných dat (Preview)

Příkaz $export lze také použít k exportu neidentifikovaných dat ze serveru FHIR. Používá modul pro vybírání z [FHIR nástrojů pro anonymitu](https://github.com/microsoft/FHIR-Tools-for-Anonymization)a v parametrech dotazů přebírá podrobnosti o konfiguraci anonymity. Můžete vytvořit vlastní konfigurační soubor anonymity nebo použít jako výchozí bod [Ukázkový konfigurační soubor](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) pro metodu HIPAA Safe přístavu. 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

|Parametr dotazu            | Příklad |Volitelnost| Popis|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.jsna|Požadováno pro neidentifikovaný export |Název konfiguračního souboru [Tady](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format)najdete formát konfiguračního souboru. Tento soubor by měl být uložený v kontejneru s názvem **Anonyme** v rámci stejného účtu úložiště Azure, který je nakonfigurovaný jako umístění exportu. |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|Volitelné pro neidentifikovaný export|Toto je značka ETag konfiguračního souboru. Značku ETag můžete získat pomocí Průzkumník služby Azure Storage z vlastnosti objektu BLOB.|

> [!IMPORTANT]
> Počítejte s tím, že při exportování nezpracovaných i neidentifikovaných operací exportu do stejného účtu úložiště Azure zadaného jako součást konfigurace exportu. Doporučuje se používat různé kontejnery odpovídající různým neidentifikovaným konfiguracím a spravovat přístup uživatelů na úrovni kontejneru.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak exportovat FHIR prostředky pomocí příkazu $export, včetně neidentifikovaných dat. V dalším kroku můžete nakonfigurovat exportovaná data:
 
>[!div class="nextstepaction"]
>[konfigurovat exportovaná data](configure-export-data.md)
