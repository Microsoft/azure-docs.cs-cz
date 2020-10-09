---
title: Povolení protokolování diagnostiky v rozhraní Azure API pro FHIR®
description: Tento článek vysvětluje, jak povolit protokolování diagnostiky v Azure API pro FHIR®
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/01/2019
ms.openlocfilehash: 948ca03b5bf503c884df5df56c61951b381874a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84871698"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>Povolení protokolování diagnostiky v rozhraní Azure API pro FHIR®

V tomto článku se dozvíte, jak povolit protokolování diagnostiky v Azure API pro FHIR® a abyste mohli zkontrolovat některé ukázkové dotazy pro tyto protokoly. Přístup k diagnostickým protokolům je nezbytný pro jakoukoli službu zdravotní péče, kde se musí jednat o dodržování zákonných požadavků (například HIPAA). Funkce v rozhraní Azure API pro FHIR®, která umožňuje diagnostické protokoly, je [**nastavení diagnostiky**](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) v Azure Portal. 

## <a name="enable-audit-logs"></a>Povolit protokoly auditu
1. Pokud chcete povolit protokolování diagnostiky v Azure API pro FHIR®, vyberte rozhraní API Azure pro službu FHIR® v Azure Portal 
2. Přejít na nastavení diagnostiky **nastavení diagnostiky**  
 ![](media/diagnostic-logging/diagnostic-settings-screen.png) 

3. Vybrat **+ Přidat nastavení diagnostiky**

4. Zadejte název nastavení.

5. Vyberte metodu, kterou chcete použít pro přístup k diagnostickým protokolům:

    1. **Archivujte do účtu úložiště** pro auditování nebo ruční kontrolu. Účet úložiště, který chcete použít, musí být již vytvořen.
    2. **Streamování do centra událostí** pro ingestování prostřednictvím služby třetí strany nebo vlastního analytického řešení. Než budete moct nakonfigurovat tento krok, budete muset vytvořit obor názvů centra událostí a zásady centra událostí.
    3. **Streamování do** pracovního prostoru Log Analytics v Azure monitor. Než budete moct vybrat tuto možnost, budete muset vytvořit pracovní prostor analýzy protokolů.

6. Vyberte **AuditLogs** a všechny metriky, které chcete zachytit.

7. Kliknutí na Uložit

> [!Note] 
> Zobrazení prvních protokolů v Log Analytics může trvat až 15 minut.  
 
Další informace o tom, jak pracovat s diagnostickými protokoly, najdete v [dokumentaci k protokolu prostředků Azure](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-overview) .

## <a name="audit-log-details"></a>Podrobnosti protokolu auditu
V tuto chvíli vrátí služba Azure API pro FHIR® v protokolu auditu následující pole: 

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
|ResultType|Řetězec|Dostupné hodnoty jsou aktuálně **spuštěny**, **úspěšné**nebo **neúspěšné** .
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
Přístup k diagnostickým protokolům je nezbytný pro monitorování služby a poskytování sestav dodržování předpisů. Rozhraní Azure API pro FHIR® umožňuje provádět tyto akce prostřednictvím diagnostických protokolů. 
 
FHIR® je registrovaná ochranná známka změněného HL7 a používá se s oprávněním změněného HL7.

## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak povolit protokoly auditu pro Azure API pro FHIR®. V dalším kroku se dozvíte další informace o dalších nastaveních, která můžete nakonfigurovat v rozhraní API Azure pro FHIR.
 
>[!div class="nextstepaction"]
>[Další nastavení](azure-api-for-fhir-additional-settings.md)