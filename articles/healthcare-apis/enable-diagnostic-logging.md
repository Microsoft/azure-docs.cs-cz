---
title: Povolení protokolování diagnostiky v Azure API pro FHIR
description: Tento článek vysvětluje, jak povolit protokolování diagnostiky v Azure API pro FHIR®
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/01/2019
ms.openlocfilehash: 54119585d4f1377b60b85fbad01fe90f097a304f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95905170"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>Povolení protokolování diagnostiky v Azure API pro FHIR

V tomto článku se dozvíte, jak povolit protokolování diagnostiky v rozhraní Azure API pro FHIR a prohlédnout si některé ukázkové dotazy pro tyto protokoly. Přístup k diagnostickým protokolům je nezbytný pro jakoukoli službu zdravotní péče, kde se musí jednat o dodržování zákonných požadavků (například HIPAA). Funkce v rozhraní Azure API pro FHIR, která umožňuje diagnostické protokoly, je [**nastavení diagnostiky**](../azure-monitor/platform/diagnostic-settings.md) v Azure Portal. 

## <a name="enable-audit-logs"></a>Povolit protokoly auditu
1. Pokud chcete povolit protokolování diagnostiky v Azure API pro FHIR, vyberte rozhraní Azure API pro službu FHIR v Azure Portal 
2. Přejít na nastavení diagnostiky **nastavení diagnostiky**  
 ![](media/diagnostic-logging/diagnostic-settings-screen.png) 

3. Vybrat **+ Přidat nastavení diagnostiky**

4. Zadejte název nastavení.

5. Vyberte metodu, kterou chcete použít pro přístup k diagnostickým protokolům:

    1. **Archivujte do účtu úložiště** pro auditování nebo ruční kontrolu. Účet úložiště, který chcete použít, musí být již vytvořen.
    2. **Streamování do centra událostí** pro ingestování prostřednictvím služby třetí strany nebo vlastního analytického řešení. Než budete moct nakonfigurovat tento krok, budete muset vytvořit obor názvů centra událostí a zásady centra událostí.
    3. **Streamování do** pracovního prostoru Log Analytics v Azure monitor. Než budete moct vybrat tuto možnost, budete muset vytvořit pracovní prostor analýzy protokolů.

6. Vyberte **AuditLogs** a všechny metriky, které chcete zachytit. Pokud používáte Azure IoT Connector pro FHIR, ujistěte se, že jste vybrali **chyby, provoz a latenci** pro metriky. 

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT Connector2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

7. Vyberte **Uložit**.


> [!Note] 
> Zobrazení prvních protokolů v Log Analytics může trvat až 15 minut.  
 
Další informace o tom, jak pracovat s diagnostickými protokoly, najdete v [dokumentaci k protokolu prostředků Azure](../azure-monitor/platform/platform-logs-overview.md) .

## <a name="audit-log-details"></a>Podrobnosti protokolu auditu
V tuto chvíli vrátí služba Azure API for FHIR v protokolu auditu následující pole: 

|Název pole  |Typ  |Poznámky  |
|---------|---------|---------|
|CallerIdentity|Dynamická|Obecný kontejner vlastností obsahující informace o identitě
|CallerIdentityIssuer|Řetězec|Vystavitel 
|CallerIdentityObjectId|Řetězec|Object_Id 
|CallerIPAddress|Řetězec|IP adresa volajícího 
|CorrelationId|Řetězec| ID korelace
|FhirResourceType|Řetězec|Typ prostředku, pro který se operace spustila
|LogCategory|Řetězec|Kategorie protokolu (aktuálně vracíme "AuditLogs" LogCategory)
|Umístění|Řetězec|Umístění serveru, který zpracoval požadavek (například Střed USA – jih)
|OperationDuration|Int|Čas potřebný k dokončení této žádosti v řádu sekund
|OperationName|Řetězec| Popisuje typ operace (například aktualizace, hledání typu).
|RequestUri|Řetězec|Identifikátor URI žádosti 
|ResultType|Řetězec|Dostupné hodnoty jsou aktuálně **spuštěny**, **úspěšné** nebo **neúspěšné** .
|StatusCode|Int|Stavový kód HTTP (např. 200) 
|TimeGenerated|DateTime|Datum a čas události|
|Vlastnosti|Řetězec| Popisuje vlastnosti fhirResourceType.
|SourceSystem|Řetězec| Zdrojový systém (v tomto případě vždy Azure)
|TenantId|Řetězec|ID tenanta
|Typ|Řetězec|Typ protokolu (v tomto případě vždy MicrosoftHealthcareApisAuditLog)
|_ResourceId|Řetězec|Podrobnosti o prostředku

## <a name="sample-queries"></a>Ukázkové dotazy

Tady je několik základních Application Insights dotazů, které můžete použít k prozkoumávání dat protokolu.

Spuštěním tohoto dotazu zobrazíte nejnovější protokoly **100** :

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

Spusťte tento dotaz, aby se seskupoval operace podle **typu prostředku FHIR**:

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by FhirResourceType
```

Spusťte tento dotaz, aby se zobrazily všechny **neúspěšné výsledky** .

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>Závěr 
Přístup k diagnostickým protokolům je nezbytný pro monitorování služby a poskytování sestav dodržování předpisů. Azure API pro FHIR umožňuje provádět tyto akce prostřednictvím diagnostických protokolů. 
 
FHIR je registrovaná ochranná známka HL7 a používá se s povolením HL7.

## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak povolit protokoly auditu pro Azure API pro FHIR. V dalším kroku se dozvíte další informace o dalších nastaveních, která můžete nakonfigurovat v rozhraní API Azure pro FHIR.
 
>[!div class="nextstepaction"]
>[Další nastavení](azure-api-for-fhir-additional-settings.md)
