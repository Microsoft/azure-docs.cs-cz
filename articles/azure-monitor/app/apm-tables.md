---
title: Schéma prostředků založené na pracovním prostoru Azure Monitor Application Insights
description: Přečtěte si o nové struktuře a schématu tabulek pro Azure Monitor Application Insights prostředky založené na pracovním prostoru.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/09/2020
ms.openlocfilehash: 1d7275c928b4d25e200a3a8d3d690c7575c056e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87323175"
---
# <a name="workspace-based-resource-changes-preview"></a>Změny prostředků na základě pracovního prostoru (Preview)

Před představením [Application Insights prostředků na základě pracovního prostoru](create-workspace-resource.md)se Application Insights data v Azure monitor uložila odděleně od jiných dat protokolů. Obě jsou založené na Azure Průzkumník dat a používají stejný dotazovací jazyk KQL (Kusto Query Language). Tento postup je popsaný v tématu [protokoly v Azure monitor](../platform/data-platform-logs.md).

Data prostředků Application Insights na základě pracovního prostoru se ukládají do pracovního prostoru Log Analytics s dalšími daty monitorování a daty aplikací. Tím se zjednoduší vaše konfigurace tím, že vám umožní snadněji analyzovat data napříč více řešeními a využívat možnosti pracovních prostorů.

## <a name="table-structure"></a>Struktura tabulky

| Starší verze názvu tabulky | Název nové tabulky | Description |
|:---|:---|:---|
| availabilityResults | AppAvailabilityResults |  Souhrnná data z testů dostupnosti.|
| browserTimings | AppBrowserTimings | Data o výkonu klienta, například čas potřebný ke zpracování příchozích dat.|
| závislosti | AppDependencies | Volání z aplikace do jiných komponent (včetně externích komponent) zaznamenaných prostřednictvím TrackDependency () – například volání REST API, databáze nebo systému souborů.  |
| customEvents | AppEvents | Vlastní události vytvořené vaší aplikací |
| customMetrics | AppMetrics | Vlastní metriky vytvořené vaší aplikací |
| pageViews | AppPageViews| Data o jednotlivých zobrazeních webu s informacemi v prohlížeči |
| Čítače výkonu | AppPerformanceCounters | Měření výkonu z výpočetních prostředků, které podporují aplikaci, například čítače výkonu systému Windows. |
| požádal | AppRequests | Žádosti přijaté vaší aplikací Například záznam samostatné žádosti se zaznamená do protokolu pro každý požadavek HTTP, který vaše webová aplikace přijme.  |
| výjimek | AppSystemEvents | Výjimky vyvolané modulem runtime aplikace zachycují výjimky na straně serveru i na straně klienta (prohlížeče). |
| trasování | AppTraces | Podrobné protokoly (trasování) emitované prostřednictvím rozhraní Code/protokolovacích rozhraní zaznamenaných prostřednictvím TrackTrace (). |

## <a name="table-schemas"></a>Schémata tabulek

V následujících částech se zobrazuje mapování mezi názvy klasických vlastností a novými názvy vlastností Application Insights na základě pracovního prostoru.  Tyto informace slouží k převodu všech dotazů pomocí starších tabulek.

Většina sloupců má stejný název s různou velikostí písmen. Vzhledem k tomu, že KQL rozlišuje velká a malá písmena, budete muset změnit název každého sloupce spolu s názvy tabulek v existujících dotazech. Jsou zvýrazněny i sloupce se změnami, které jsou v kombinaci s velkými písmeny. V rámci svého prostředku Application Insights můžete nadále používat své klasické Application Insights dotazy **i v případě** , že se jedná o prostředek založený na pracovním prostoru. Nové názvy vlastností jsou požadovány pro dotazování z kontextu prostředí Log Analytics pracovního prostoru.

### <a name="appavailabilityresults"></a>AppAvailabilityResults

Starší tabulka: dostupnost

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|řetězec|\_ResourceGUID|řetězec|
|application_Version|řetězec|AppVersion|řetězec|
|appName|řetězec|\_ResourceId|řetězec|
|client_Browser|řetězec|ClientBrowser|řetězec|
|client_City|řetězec|ClientCity|řetězec|
|client_CountryOrRegion|řetězec|ClientCountryOrRegion|řetězec|
|client_IP|řetězec|IP adresa klienta|řetězec|
|client_Model|řetězec|ClientModel|řetězec|
|client_OS|řetězec|ClientOS|řetězec|
|client_StateOrProvince|řetězec|ClientStateOrProvince|řetězec|
|client_Type|řetězec|ClientType|řetězec|
|cloud_RoleInstance|řetězec|AppRoleInstance|řetězec|
|cloud_RoleName|řetězec|AppRoleName|řetězec|
|customDimensions|dynamic|Vlastnosti|Dynamická|
|customMeasurements|dynamic|Měření|Dynamická|
|doba trvání|real|DurationMs|real|
|`id`|řetězec|`Id`|řetězec|
|iKey|řetězec|IKey|řetězec|
|Vlastnost ItemCount|int|Vlastnost ItemCount|int|
|itemId|řetězec|\_ItemId|řetězec|
|itemType|řetězec|Typ|Řetězec|
|location|řetězec|Umístění|řetězec|
|zpráva|řetězec|Zpráva|řetězec|
|name|řetězec|Name|řetězec|
|operation_Id|řetězec|OperationId|řetězec|
|operation_Name|řetězec|OperationName|řetězec|
|operation_ParentId|řetězec|OperationParentId|řetězec|
|operation_SyntheticSource|řetězec|OperationSyntheticSource|řetězec|
|performanceBucket|řetězec|PerformanceBucket|řetězec|
|sdkVersion|řetězec|SdkVersion|řetězec|
|session_Id|řetězec|SessionId|řetězec|
|size|real|Velikost|real|
|úspěch|řetězec|Success|Logická hodnota|
|časové razítko|datetime|TimeGenerated|datetime|
|user_AccountId|řetězec|UserAccountId|řetězec|
|user_AuthenticatedId|řetězec|UserAuthenticatedId|řetězec|
|user_Id|řetězec|UserId|řetězec|

### <a name="appbrowsertimings"></a>AppBrowserTimings

Starší tabulka: browserTimings

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|řetězec|\_ResourceGUID|řetězec|
|application_Version|řetězec|AppVersion|řetězec|
|appName|řetězec|\_ResourceId|řetězec|
|client_Browser|řetězec|ClientBrowser|řetězec|
|client_City|řetězec|ClientCity|řetězec|
|client_CountryOrRegion|řetězec|ClientCountryOrRegion|řetězec|
|client_IP|řetězec|IP adresa klienta|řetězec|
|client_Model|řetězec|ClientModel|řetězec|
|client_OS|řetězec|ClientOS|řetězec|
|client_StateOrProvince|řetězec|ClientStateOrProvince|řetězec|
|client_Type|řetězec|ClientType|řetězec|
|cloud_RoleInstance|řetězec|AppRoleInstance|řetězec|
|cloud_RoleName|řetězec|AppRoleName|řetězec|
|customDimensions|dynamic|Vlastnosti|Dynamická|
|customMeasurements|dynamic|Měření|Dynamická|
|iKey|řetězec|IKey|řetězec|
|Vlastnost ItemCount|int|Vlastnost ItemCount|int|
|itemId|řetězec|\_ItemId|řetězec|
|itemType|řetězec|Typ|řetězec|
|name|řetězec|Name|datetime|
|networkDuration|real|NetworkDurationMs|real|
|operation_Id|řetězec|OperationId|řetězec|
|operation_Name|řetězec|OperationName|řetězec|
|operation_ParentId|řetězec|OperationParentId|řetězec|
|operation_SyntheticSource|řetězec|OperationSyntheticSource|řetězec|
|performanceBucket|řetězec|PerformanceBucket|řetězec|
|processingDuration|real|ProcessingDurationMs|real|
|receiveDuration|real|ReceiveDurationMs|real|
|sdkVersion|řetězec|SdkVersion|řetězec|
|sendDuration|real|SendDurationMs|real|
|session_Id|řetězec|SessionId|řetězec|
|časové razítko|datetime|TimeGenerated|datetime|
|totalDuration|real|TotalDurationMs|real|
|url|řetězec|URL|řetězec|
|user_AccountId|řetězec|UserAccountId|řetězec|
|user_AuthenticatedId|řetězec|UserAuthenticatedId|řetězec|
|user_Id|řetězec|UserId|řetězec|

### <a name="appdependencies"></a>AppDependencies

Starší tabulka: závislosti

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|řetězec|\_ResourceGUID|řetězec|
|application_Version|řetězec|AppVersion|řetězec|
|appName|řetězec|\_ResourceId|řetězec|
|client_Browser|řetězec|ClientBrowser|řetězec|
|client_City|řetězec|ClientCity|řetězec|
|client_CountryOrRegion|řetězec|ClientCountryOrRegion|řetězec|
|client_IP|řetězec|IP adresa klienta|řetězec|
|client_Model|řetězec|ClientModel|řetězec|
|client_OS|řetězec|ClientOS|řetězec|
|client_StateOrProvince|řetězec|ClientStateOrProvince|řetězec|
|client_Type|řetězec|ClientType|řetězec|
|cloud_RoleInstance|řetězec|AppRoleInstance|řetězec|
|cloud_RoleName|řetězec|AppRoleName|řetězec|
|customDimensions|dynamic|Vlastnosti|Dynamická|
|customMeasurements|dynamic|Měření|Dynamická|
|data|řetězec|Data|řetězec|
|doba trvání|real|DurationMs|real|
|`id`|řetězec|`Id`|řetězec|
|iKey|řetězec|IKey|řetězec|
|Vlastnost ItemCount|int|Vlastnost ItemCount|int|
|itemId|řetězec|\_ItemId|řetězec|
|itemType|řetězec|Typ|Řetězec|
|name|řetězec|Name|řetězec|
|operation_Id|řetězec|OperationId|řetězec|
|operation_Name|řetězec|OperationName|řetězec|
|operation_ParentId|řetězec|OperationParentId|řetězec|
|operation_SyntheticSource|řetězec|OperationSyntheticSource|řetězec|
|performanceBucket|řetězec|PerformanceBucket|řetězec|
|resultCode|řetězec|ResultCode|řetězec|
|sdkVersion|řetězec|SdkVersion|řetězec|
|session_Id|řetězec|SessionId|řetězec|
|úspěch|řetězec|Success|Logická hodnota|
|cílové|řetězec|Cíl|řetězec|
|časové razítko|datetime|TimeGenerated|datetime|
|typ|řetězec|DependencyType|řetězec|
|user_AccountId|řetězec|UserAccountId|řetězec|
|user_AuthenticatedId|řetězec|UserAuthenticatedId|řetězec|
|user_Id|řetězec|UserId|řetězec|

### <a name="appevents"></a>AppEvents

Starší tabulka: customEvents

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|řetězec|\_ResourceGUID|řetězec|
|application_Version|řetězec|AppVersion|řetězec|
|appName|řetězec|\_ResourceId|řetězec|
|client_Browser|řetězec|ClientBrowser|řetězec|
|client_City|řetězec|ClientCity|řetězec|
|client_CountryOrRegion|řetězec|ClientCountryOrRegion|řetězec|
|client_IP|řetězec|IP adresa klienta|řetězec|
|client_Model|řetězec|ClientModel|řetězec|
|client_OS|řetězec|ClientOS|řetězec|
|client_StateOrProvince|řetězec|ClientStateOrProvince|řetězec|
|client_Type|řetězec|ClientType|řetězec|
|cloud_RoleInstance|řetězec|AppRoleInstance|řetězec|
|cloud_RoleName|řetězec|AppRoleName|řetězec|
|customDimensions|dynamic|Vlastnosti|Dynamická|
|customMeasurements|dynamic|Měření|Dynamická|
|iKey|řetězec|IKey|řetězec|
|Vlastnost ItemCount|int|Vlastnost ItemCount|int|
|itemId|řetězec|\_ItemId|řetězec|
|itemType|řetězec|Typ|řetězec|
|name|řetězec|Name|řetězec|
|operation_Id|řetězec|OperationId|řetězec|
|operation_Name|řetězec|OperationName|řetězec|
|operation_ParentId|řetězec|OperationParentId|řetězec|
|operation_SyntheticSource|řetězec|OperationSyntheticSource|řetězec|
|sdkVersion|řetězec|SdkVersion|řetězec|
|session_Id|řetězec|SessionId|řetězec|
|časové razítko|datetime|TimeGenerated|datetime|
|user_AccountId|řetězec|UserAccountId|řetězec|
|user_AuthenticatedId|řetězec|UserAuthenticatedId|řetězec|
|user_Id|řetězec|UserId|řetězec|

### <a name="appmetrics"></a>AppMetrics

Starší tabulka: customMetrics

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|řetězec|\_ResourceGUID|řetězec|
|application_Version|řetězec|AppVersion|řetězec|
|appName|řetězec|\_ResourceId|řetězec|
|client_Browser|řetězec|ClientBrowser|řetězec|
|client_City|řetězec|ClientCity|řetězec|
|client_CountryOrRegion|řetězec|ClientCountryOrRegion|řetězec|
|client_IP|řetězec|IP adresa klienta|řetězec|
|client_Model|řetězec|ClientModel|řetězec|
|client_OS|řetězec|ClientOS|řetězec|
|client_StateOrProvince|řetězec|ClientStateOrProvince|řetězec|
|client_Type|řetězec|ClientType|řetězec|
|cloud_RoleInstance|řetězec|AppRoleInstance|řetězec|
|cloud_RoleName|řetězec|AppRoleName|řetězec|
|customDimensions|dynamic|Vlastnosti|Dynamická|
|iKey|řetězec|IKey|řetězec|
|itemId|řetězec|\_ItemId|řetězec|
|itemType|řetězec|Typ|řetězec|
|name|řetězec|Name|řetězec|
|operation_Id|řetězec|OperationId|řetězec|
|operation_Name|řetězec|OperationName|řetězec|
|operation_ParentId|řetězec|OperationParentId|řetězec|
|operation_SyntheticSource|řetězec|OperationSyntheticSource|řetězec|
|sdkVersion|řetězec|SdkVersion|řetězec|
|session_Id|řetězec|SessionId|řetězec|
|časové razítko|datetime|TimeGenerated|datetime|
|user_AccountId|řetězec|UserAccountId|řetězec|
|user_AuthenticatedId|řetězec|UserAuthenticatedId|řetězec|
|user_Id|řetězec|UserId|řetězec|
|value|real|odstraněn||
|valueCount|int|ValueCount|int|
|valueMax|real|ValueMax|real|
|valueMin|real|ValueMin|real|
|valueStdDev|real|ValueStdDev|real|
|valueSum|real|ValueSum|real|

### <a name="apppageviews"></a>AppPageViews

Starší tabulka: pageViews

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|řetězec|\_ResourceGUID|řetězec|
|application_Version|řetězec|AppVersion|řetězec|
|appName|řetězec|\_ResourceId|řetězec|
|client_Browser|řetězec|ClientBrowser|řetězec|
|client_City|řetězec|ClientCity|řetězec|
|client_CountryOrRegion|řetězec|ClientCountryOrRegion|řetězec|
|client_IP|řetězec|IP adresa klienta|řetězec|
|client_Model|řetězec|ClientModel|řetězec|
|client_OS|řetězec|ClientOS|řetězec|
|client_StateOrProvince|řetězec|ClientStateOrProvince|řetězec|
|client_Type|řetězec|ClientType|řetězec|
|cloud_RoleInstance|řetězec|AppRoleInstance|řetězec|
|cloud_RoleName|řetězec|AppRoleName|řetězec|
|customDimensions|dynamic|Vlastnosti|Dynamická|
|customMeasurements|dynamic|Měření|Dynamická|
|doba trvání|real|DurationMs|real|
|`id`|řetězec|`Id`|řetězec|
|iKey|řetězec|IKey|řetězec|
|Vlastnost ItemCount|int|Vlastnost ItemCount|int|
|itemId|řetězec|\_ItemId|řetězec|
|itemType|řetězec|Typ|Řetězec|
|name|řetězec|Name|řetězec|
|operation_Id|řetězec|OperationId|řetězec|
|operation_Name|řetězec|OperationName|řetězec|
|operation_ParentId|řetězec|OperationParentId|řetězec|
|operation_SyntheticSource|řetězec|OperationSyntheticSource|řetězec|
|performanceBucket|řetězec|PerformanceBucket|řetězec|
|sdkVersion|řetězec|SdkVersion|řetězec|
|session_Id|řetězec|SessionId|řetězec|
|časové razítko|datetime|TimeGenerated|datetime|
|url|řetězec|URL|řetězec|
|user_AccountId|řetězec|UserAccountId|řetězec|
|user_AuthenticatedId|řetězec|UserAuthenticatedId|řetězec|
|user_Id|řetězec|UserId|řetězec|

### <a name="appperformancecounters"></a>AppPerformanceCounters

Starší tabulka: čítače výkonu

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|řetězec|\_ResourceGUID|řetězec|
|application_Version|řetězec|AppVersion|řetězec|
|appName|řetězec|\_ResourceId|řetězec|
|category|řetězec|Kategorie|řetězec|
|client_Browser|řetězec|ClientBrowser|řetězec|
|client_City|řetězec|ClientCity|řetězec|
|client_CountryOrRegion|řetězec|ClientCountryOrRegion|řetězec|
|client_IP|řetězec|IP adresa klienta|řetězec|
|client_Model|řetězec|ClientModel|řetězec|
|client_OS|řetězec|ClientOS|řetězec|
|client_StateOrProvince|řetězec|ClientStateOrProvince|řetězec|
|client_Type|řetězec|ClientType|řetězec|
|cloud_RoleInstance|řetězec|AppRoleInstance|řetězec|
|cloud_RoleName|řetězec|AppRoleName|řetězec|
|counter|řetězec|odstraněn||
|customDimensions|dynamic|Vlastnosti|Dynamická|
|iKey|řetězec|IKey|řetězec|
|případě|řetězec|Instance|řetězec|
|itemId|řetězec|\_ItemId|řetězec|
|itemType|řetězec|Typ|řetězec|
|name|řetězec|Name|řetězec|
|operation_Id|řetězec|OperationId|řetězec|
|operation_Name|řetězec|OperationName|řetězec|
|operation_ParentId|řetězec|OperationParentId|řetězec|
|operation_SyntheticSource|řetězec|OperationSyntheticSource|řetězec|
|sdkVersion|řetězec|SdkVersion|řetězec|
|session_Id|řetězec|SessionId|řetězec|
|časové razítko|datetime|TimeGenerated|datetime|
|user_AccountId|řetězec|UserAccountId|řetězec|
|user_AuthenticatedId|řetězec|UserAuthenticatedId|řetězec|
|user_Id|řetězec|UserId|řetězec|
|value|real|Hodnota|real|

### <a name="apprequests"></a>AppRequests

Starší tabulka: žádosti

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|řetězec|\_ResourceGUID|řetězec|
|application_Version|řetězec|AppVersion|řetězec|
|appName|řetězec|\_ResourceId|řetězec|
|client_Browser|řetězec|ClientBrowser|řetězec|
|client_City|řetězec|ClientCity|řetězec|
|client_CountryOrRegion|řetězec|ClientCountryOrRegion|řetězec|
|client_IP|řetězec|IP adresa klienta|řetězec|
|client_Model|řetězec|ClientModel|řetězec|
|client_OS|řetězec|ClientOS|řetězec|
|client_StateOrProvince|řetězec|ClientStateOrProvince|řetězec|
|client_Type|řetězec|ClientType|řetězec|
|cloud_RoleInstance|řetězec|AppRoleInstance|řetězec|
|cloud_RoleName|řetězec|AppRoleName|řetězec|
|customDimensions|dynamic|Vlastnosti|Dynamická|
|customMeasurements|dynamic|Měření|Dynamická|
|doba trvání|real|DurationMs|Skutečné|
|`id`|řetězec|`Id`|Řetězec|
|iKey|řetězec|IKey|řetězec|
|Vlastnost ItemCount|int|Vlastnost ItemCount|int|
|itemId|řetězec|\_ItemId|řetězec|
|itemType|řetězec|Typ|Řetězec|
|name|řetězec|Name|Řetězec|
|operation_Id|řetězec|OperationId|řetězec|
|operation_Name|řetězec|OperationName|řetězec|
|operation_ParentId|řetězec|OperationParentId|řetězec|
|operation_SyntheticSource|řetězec|OperationSyntheticSource|řetězec|
|performanceBucket|řetězec|PerformanceBucket|Řetězec|
|resultCode|řetězec|ResultCode|Řetězec|
|sdkVersion|řetězec|SdkVersion|řetězec|
|session_Id|řetězec|SessionId|řetězec|
|source|odkazy řetězců|Zdroj|Řetězec|
|úspěch|řetězec|Success|Logická hodnota|
|časové razítko|datetime|TimeGenerated|datetime|
|url|řetězec|URL|Řetězec|
|user_AccountId|řetězec|UserAccountId|řetězec|
|user_AuthenticatedId|řetězec|UserAuthenticatedId|řetězec|
|user_Id|řetězec|UserId|řetězec|

### <a name="appsystemevents"></a>AppSystemEvents

Starší tabulka: výjimky

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|řetězec|\_ResourceGUID|řetězec|
|application_Version|řetězec|AppVersion|řetězec|
|appName|řetězec|\_ResourceId|řetězec|
|sestavení|řetězec|Sestavení|řetězec|
|client_Browser|řetězec|ClientBrowser|řetězec|
|client_City|řetězec|ClientCity|řetězec|
|client_CountryOrRegion|řetězec|ClientCountryOrRegion|řetězec|
|client_IP|řetězec|IP adresa klienta|řetězec|
|client_Model|řetězec|ClientModel|řetězec|
|client_OS|řetězec|ClientOS|řetězec|
|client_StateOrProvince|řetězec|ClientStateOrProvince|řetězec|
|client_Type|řetězec|ClientType|řetězec|
|cloud_RoleInstance|řetězec|AppRoleInstance|řetězec|
|cloud_RoleName|řetězec|AppRoleName|řetězec|
|customDimensions|dynamic|Vlastnosti|dynamic|
|customMeasurements|dynamic|Měření|dynamic|
|zobrazí|dynamic|Podrobnosti|dynamic|
|handledAt|řetězec|HandledAt|řetězec|
|iKey|řetězec|IKey|řetězec|
|innermostAssembly|řetězec|InnermostAssembly|řetězec|
|innermostMessage|řetězec|InnermostMessage|řetězec|
|innermostMethod|řetězec|InnermostMethod|řetězec|
|innermostType|řetězec|InnermostType|řetězec|
|Vlastnost ItemCount|int|Vlastnost ItemCount|int|
|itemId|řetězec|\_ItemId|řetězec|
|itemType|řetězec|Typ|řetězec|
|zpráva|řetězec|Zpráva|řetězec|
|method|řetězec|Metoda|řetězec|
|operation_Id|řetězec|OperationId|řetězec|
|operation_Name|řetězec|OperationName|řetězec|
|operation_ParentId|řetězec|OperationParentId|řetězec|
|operation_SyntheticSource|řetězec|OperationSyntheticSource|řetězec|
|outerAssembly|řetězec|OuterAssembly|řetězec|
|outerMessage|řetězec|OuterMessage|řetězec|
|outerMethod|řetězec|OuterMethod|řetězec|
|outerType|řetězec|OuterType|řetězec|
|problemId|řetězec|ProblemId|řetězec|
|sdkVersion|řetězec|SdkVersion|řetězec|
|session_Id|řetězec|SessionId|řetězec|
|severityLevel|int|SeverityLevel|int|
|časové razítko|datetime|TimeGenerated|datetime|
|typ|řetězec|Typvýjimky|řetězec|
|user_AccountId|řetězec|UserAccountId|řetězec|
|user_AuthenticatedId|řetězec|UserAuthenticatedId|řetězec|
|user_Id|řetězec|UserId|řetězec|

### <a name="apptraces"></a>AppTraces

Starší tabulka: trasování

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|řetězec|\_ResourceGUID|řetězec|
|application_Version|řetězec|AppVersion|řetězec|
|appName|řetězec|\_ResourceId|řetězec|
|client_Browser|řetězec|ClientBrowser|řetězec|
|client_City|řetězec|ClientCity|řetězec|
|client_CountryOrRegion|řetězec|ClientCountryOrRegion|řetězec|
|client_IP|řetězec|IP adresa klienta|řetězec|
|client_Model|řetězec|ClientModel|řetězec|
|client_OS|řetězec|ClientOS|řetězec|
|client_StateOrProvince|řetězec|ClientStateOrProvince|řetězec|
|client_Type|řetězec|ClientType|řetězec|
|cloud_RoleInstance|řetězec|AppRoleInstance|řetězec|
|cloud_RoleName|řetězec|AppRoleName|řetězec|
|customDimensions|dynamic|Vlastnosti|dynamic|
|customMeasurements|dynamic|Měření|dynamic|
|iKey|řetězec|IKey|řetězec|
|Vlastnost ItemCount|int|Vlastnost ItemCount|int|
|itemId|řetězec|\_ItemId|řetězec|
|itemType|řetězec|Typ|řetězec|
|zpráva|řetězec|Zpráva|řetězec|
|operation_Id|řetězec|OperationId|řetězec|
|operation_Name|řetězec|OperationName|řetězec|
|operation_ParentId|řetězec|OperationParentId|řetězec|
|operation_SyntheticSource|řetězec|OperationSyntheticSource|řetězec|
|sdkVersion|řetězec|SdkVersion|řetězec|
|session_Id|řetězec|SessionId|řetězec|
|severityLevel|int|SeverityLevel|int|
|časové razítko|datetime|TimeGenerated|datetime|
|user_AccountId|řetězec|UserAccountId|řetězec|
|user_AuthenticatedId|řetězec|UserAuthenticatedId|řetězec|
|user_Id|řetězec|UserId|řetězec|

## <a name="next-steps"></a>Další kroky

* [Zkoumání metrik](../platform/metrics-charts.md)
* [Psaní analytických dotazů](../log-query/log-query-overview.md)

