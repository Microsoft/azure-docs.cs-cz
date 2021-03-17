---
title: Povolení protokolování diagnostiky v Azure API pro FHIR
description: Tento článek vysvětluje, jak povolit protokolování diagnostiky v Azure API pro FHIR®
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: zxue
ms.date: 03/03/2021
ms.openlocfilehash: d8f7a2b2f31fb192147c1950866cff77064a3671
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018274"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>Povolení protokolování diagnostiky v Azure API pro FHIR

V tomto článku se dozvíte, jak povolit protokolování diagnostiky v rozhraní Azure API pro FHIR a prohlédnout si některé ukázkové dotazy pro tyto protokoly. Přístup k diagnostickým protokolům je nezbytný pro jakoukoli službu zdravotní péče, kde se musí jednat o dodržování zákonných požadavků (například HIPAA). Funkce v rozhraní Azure API pro FHIR, která umožňuje diagnostické protokoly, je [**nastavení diagnostiky**](../../azure-monitor/essentials/diagnostic-settings.md) v Azure Portal. 

## <a name="view-and-download-fhir-metrics-data"></a>Zobrazení a stažení dat metrik FHIR

Metriky můžete zobrazit v části monitorování | Metriky z portálu. Metriky zahrnují počet požadavků, průměrnou latenci, počet chyb, velikost dat, použité ru, počet požadavků, které překročily kapacitu, a dostupnost (v%). Níže uvedený snímek obrazovky ukazuje ru, který se používá pro ukázkové prostředí s velmi malým počtem aktivit za posledních 7 dnů. Data si můžete stáhnout ve formátu JSON.

   :::image type="content" source="media/diagnostic-logging/fhir-metrics-rus-screen.png" alt-text="Rozhraní Azure API pro metriky FHIR z portálu" lightbox="media/diagnostic-logging/fhir-metrics-rus-screen.png":::

## <a name="enable-audit-logs"></a>Povolit protokoly auditu
1. Pokud chcete povolit protokolování diagnostiky v Azure API pro FHIR, vyberte rozhraní Azure API pro službu FHIR v Azure Portal 
2. Přejít na **nastavení diagnostiky** 

   :::image type="content" source="media/diagnostic-logging/diagnostic-settings-screen.png" alt-text="Přidejte nastavení diagnostiky Azure FHIR." lightbox="media/diagnostic-logging/diagnostic-settings-screen.png":::

3. Vybrat **+ Přidat nastavení diagnostiky**

4. Zadejte název nastavení.

5. Vyberte metodu, kterou chcete použít pro přístup k diagnostickým protokolům:

    1. **Archivujte do účtu úložiště** pro auditování nebo ruční kontrolu. Účet úložiště, který chcete použít, musí být již vytvořen.
    2. **Streamování do centra událostí** pro ingestování prostřednictvím služby třetí strany nebo vlastního analytického řešení. Než budete moct nakonfigurovat tento krok, budete muset vytvořit obor názvů centra událostí a zásady centra událostí.
    3. **Streamování do** pracovního prostoru Log Analytics v Azure monitor. Než budete moct vybrat tuto možnost, budete muset vytvořit pracovní prostor analýzy protokolů.

6. Vyberte **AuditLogs** a/nebo **AllMetrics**. Metriky zahrnují název služby, dostupnost, velikost dat, celkovou latenci, celkový počet požadavků, chyby a časové razítko. Další podrobnosti najdete v článku [podporované metriky](https://docs.microsoft.com/azure/azure-monitor/essentials/metrics-supported#microsofthealthcareapisservices). 

   :::image type="content" source="media/diagnostic-logging/fhir-diagnostic-setting.png" alt-text="Nastavení diagnostiky Azure FHIR. Vyberte AuditLogs a/nebo AllMetrics." lightbox="media/diagnostic-logging/fhir-diagnostic-setting.png":::

7. Vyberte **Uložit**.


> [!Note] 
> Zobrazení prvních protokolů v Log Analytics může trvat až 15 minut. Pokud je rozhraní Azure API pro FHIR přesunuté z jedné skupiny prostředků nebo předplatného na jiný, aktualizujte nastavení po dokončení přesunutí. 
 
Další informace o tom, jak pracovat s diagnostickými protokoly, najdete v [dokumentaci k protokolu prostředků Azure](../../azure-monitor/essentials/platform-logs-overview.md) .

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
