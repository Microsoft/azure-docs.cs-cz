---
title: Programové přístupové paradigma
description: Seznamte se s vysokým tokem vzoru volání rozhraní API pro programovou analýzu. Pojednává také o rozhraních API pro přístup k analytickým sestavám na komerčním tržišti společnosti Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8e0b94a46e96dd8ba16040e16b421520eb67de19
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583864"
---
# <a name="programmatic-access-paradigm"></a>Programové přístupové paradigma

Tento diagram znázorňuje vzor volání rozhraní API, který se používá k vytvoření nové šablony sestavy, naplánování vlastní sestavy a načtení dat o chybách.

[ ![ Znázorňuje vzor volání rozhraní API, který se používá k vytvoření nové šablony sestavy, naplánování vlastní sestavy a načtení dat o chybách.](./media/analytics-programmatic-access/analytics-high-level-flow.png)](./media/analytics-programmatic-access/analytics-high-level-flow.png#lightbox) 
 ***Obrázek 1: tok na vysoké úrovni vzoru volání rozhraní API***

V tomto seznamu najdete další podrobnosti o obrázku 1.

1. Klientská aplikace může definovat vlastní schéma a šablonu sestavy voláním [rozhraní API pro vytvoření dotazu na sestavu](#create-report-query-api). Alternativně můžete použít šablonu sestavy ( `QueryId` ) ze [seznamu systémových dotazů](analytics-system-queries.md).
1. Po úspěšném dokončení vrátí rozhraní API šablony pro vytváření sestav `QueryId` .
1. Klientská aplikace pak zavolá [rozhraní API pro vytváření sestav](#create-report-api) pomocí `QueryID` spolu s počátečním datem sestavy, zopakuje interval, opakování a nepovinný identifikátor URI zpětného volání.
1. Po úspěšném dokončení vrátí [rozhraní API pro vytvoření sestavy](#create-report-api) `ReportID` .
1. Klientská aplikace se pošle oznámení na identifikátor URI zpětného volání, jakmile budou data sestavy připravena ke stažení.
1. Klientská aplikace potom používá [rozhraní API pro spuštění sestav](#get-report-executions-api) k dotazování na stav sestavy s `Report ID` rozsahem dat a.
1. Po úspěšném dokončení se odkaz na stažení sestavy vrátí a aplikace může zahájit stahování dat.

## <a name="report-query-language-specification"></a>Specifikace jazyka dotazu sestavy

Zatímco poskytujeme [systémové dotazy](analytics-system-queries.md) , které můžete použít k vytváření sestav, můžete také vytvářet vlastní dotazy založené na vašich obchodních potřebách. Další informace o vlastních dotazech najdete v tématu [vlastní specifikace dotazu](analytics-custom-query-specification.md).

## <a name="create-report-query-api"></a>Vytvoření rozhraní API pro dotazování sestav

Toto rozhraní API pomáhá vytvářet vlastní dotazy definující datovou sadu, ze které je nutné exportovat sloupce a metriky. Rozhraní API poskytuje flexibilitu pro vytvoření nové šablony vytváření sestav na základě vašich obchodních potřeb.

Můžete také použít [systémové dotazy](analytics-system-queries.md) , které poskytujeme. Pokud nepotřebujete vlastní šablony sestav, můžete zavolat [rozhraní API pro vytváření sestav](#create-report-api) přímo pomocí [QueryIds](analytics-system-queries.md) systémových dotazů, které poskytujeme.

Následující příklad ukazuje, jak vytvořit vlastní dotaz, který získá _normalizované využití a odhadované finanční poplatky za placené SKU_ z datové sady [ISVUsage](analytics-make-your-first-api-call.md#programmatic-api-call) za poslední měsíc.

*Syntaxe žádosti*

| Metoda | Identifikátor URI žádosti |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries` |
|||

*Hlavička žádosti*

| Hlavička | Typ | Description |
| ------------- | ------------- | ------------- |
| Autorizace | řetězec | Povinná hodnota. Přístupový token Azure Active Directory (Azure AD). Formát je `Bearer <token>` . |
| Typ obsahu | `string` | `application/JSON` |
||||

*Parametr cesty*

Žádné

*Parametr dotazu*

Žádné

*Příklad datové části požadavku*

```json
{
    "Name": "ISVUsageQuery",
    "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
    "Query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"
}
```

*Glosář*

Tato tabulka poskytuje klíčové definice prvků v datové části požadavku.

| Parametr | Povinné | Popis | Povolené hodnoty |
| ------------ | ------------- | ------------- | ------------- |
| `Name` | Yes | Popisný název dotazu | řetězec |
| `Description` | No | Popis toho, co dotaz vrátí | řetězec |
| `Query` | Yes | Řetězec dotazu sestavy | Datový typ: řetězec<br>[Vlastní dotaz](analytics-sample-queries.md) založený na obchodních potřebách |
|||||

> [!NOTE]
> Vlastní Ukázky dotazů najdete v tématu [Příklady ukázkových dotazů](analytics-sample-queries.md).

*Ukázková odpověď*

Datová část odpovědi je strukturována takto:

Kódy odpovědí: 200, 400, 401, 403, 500

Příklad datové části odpovědi:

```json
{
  "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": " ISVUsageQuery",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs”,
            "query": " SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34Z"
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

*Glosář*

Tato tabulka poskytuje klíčové definice prvků v odpovědi.

| Parametr | Popis |
| ------------ | ------------- |
| `QueryId` | Univerzálně jedinečný identifikátor (UUID) dotazu, který jste vytvořili |
| `Name` | Popisný název zadaný pro dotaz v datové části požadavku |
| `Description` | Popis zadaný při vytváření dotazu |
| `Query` | Dotaz na sestavu předaný jako vstup během vytváření dotazu |
| `Type` | Nastavit na `userDefined` |
| `User` | ID uživatele, které se použilo k vytvoření dotazu |
| `CreatedTime` | Čas UTC, kdy byl dotaz vytvořen v tomto formátu: RRRR-MM-ddTHH: mm: ssZ |
| `TotalCount` | Počet datových sad v poli hodnot |
| `StatusCode` | Kód výsledku<br>Možné hodnoty jsou 200, 400, 401, 403, 500 |
| `message` | Stavová zpráva od spuštění rozhraní API |
|||

## <a name="create-report-api"></a>Vytvoření rozhraní API pro sestavy

Po úspěšném vytvoření vlastní šablony sestavy a přijetí `QueryID` jako součást odpovědi na [dotaz na vytvoření sestavy](#create-report-query-api) lze toto rozhraní API volat, aby bylo možné naplánovat spuštění dotazu v pravidelných intervalech. Můžete nastavit četnost a plán pro doručení sestavy. Pro systémové dotazy, které poskytujeme, se dá rozhraní API pro vytváření sestav volat taky pomocí [QueryId](analytics-sample-queries.md).

*Syntaxe žádosti*

| Metoda | Identifikátor URI žádosti |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport` |
|||

*Hlavička žádosti*

| Hlavička | Typ | Description |
| ------ | ---- | ----------- |
| Autorizace | řetězec | Povinná hodnota. Přístupový token Azure Active Directory (Azure AD). Formát je `Bearer <token>` . |
| Typ obsahu | řetězec | `application/JSON` |
||||

*Parametr cesty*

Žádné

*Parametr dotazu*

Žádné

*Příklad datové části požadavku*

```json
{
  "ReportName": "ISVUsageReport",
  "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
  "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c ",
  "StartTime": "2021-01-06T19:00:00Z ",
  "RecurrenceInterval": 48,
  "RecurrenceCount": 20,
  "Format": "csv",
  "CallbackUrl": "https://<SampleCallbackUrl>"
}
```

*Glosář*

Tato tabulka poskytuje klíčové definice prvků v datové části požadavku.

| Parametr | Povinné | Popis | Povolené hodnoty |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | Yes | Název přiřazený k sestavě | řetězec |
| `Description` | No | Popis vytvořené sestavy | řetězec |
| `QueryId` | Yes | ID dotazu sestavy | řetězec |
| `StartTime` | Yes | Časové razítko UTC, na kterém bude zahájeno generování sestavy.<br>Formát by měl být: RRRR-MM-ddTHH: mm: ssZ | řetězec |
| `RecurrenceInterval` | Yes | Frekvence vygenerování sestavy v hodinách<br>Minimální hodnota je 4 a maximální hodnota je 90. | integer |
| `RecurrenceCount` | No | Počet sestav, které mají být vygenerovány. | integer |
| `Format` | No | Formát souboru exportovaného souboru<br>Výchozí formát je. Formát. | CSV/TSV |
| `CallbackUrl` | No | Veřejně dosažitelná adresa URL, která může být volitelně nakonfigurovaná jako cíl zpětného volání. | Řetězec (adresa URL protokolu HTTP) |
| `ExecuteNow` | No | Tento parametr by se měl použít k vytvoření sestavy, která se spustí jenom jednou. `StartTime`, `RecurrenceInterval` , a `RecurrenceCount` jsou ignorovány, pokud je tato nastavení nastavena na `true` . Sestava se spustí hned asynchronním způsobem. | true nebo false |
| `QueryStartTime` | No | Volitelně určuje počáteční čas pro dotazování extrahování dat. Tento parametr platí pouze pro jednu sestavu spuštění, která je `ExecuteNow` nastavena na hodnotu `true` . Formát by měl být RRRR-MM-ddTHH: mm: ssZ | Časové razítko jako řetězec |
| `QueryEndTime` | No | Volitelně určuje čas ukončení dotazu, který extrahuje data. Tento parametr platí pouze pro jednu sestavu spuštění, která je `ExecuteNow` nastavena na hodnotu `true` . Formát by měl být RRRR-MM-ddTHH: mm: ssZ | Časové razítko jako řetězec |
|||||

*Ukázková odpověď*

Datová část odpovědi je strukturována takto:

Kód odpovědi: 200, 400, 401, 403, 404, 500

Datová část odpovědi:

```json
{
  "Value": [
    {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVUsageReport",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": "https://<SampleCallbackUrl>",
            "format": "csv"    
    }
  ],
  "TotalCount": 1,
  "Message": "Report created successfully",
  "StatusCode": 200
}
```

*Glosář*

Tato tabulka poskytuje klíčové definice prvků v odpovědi.

| Parametr | Popis |
| ------------ | ------------- |
| `ReportId` | Univerzálně jedinečný identifikátor (UUID) sestavy, kterou jste vytvořili |
| `ReportName` | Název zadaný pro sestavu v datové části požadavku |
| `Description` | Popis zadaný při vytváření sestavy |
| `QueryId` | ID dotazu předané v době, kdy jste sestavu vytvořili |
| `Query` | Text dotazu, který se spustí pro tuto sestavu |
| `User` | ID uživatele, které se použilo k vytvoření sestavy |
| `CreatedTime` | Čas UTC, kdy byla sestava vytvořena v tomto formátu: RRRR-MM-ddTHH: mm: ssZ |
| `ModifiedTime` | Čas UTC, kdy se sestava naposledy změnila v tomto formátu: RRRR-MM-ddTHH: mm: ssZ |
| `StartTime` | Čas UTC, kdy bude spuštění sestavy začínat v tomto formátu: RRRR-MM-ddTHH: mm: ssZ |
| `ReportStatus` | Stav provádění sestavy Možné hodnoty jsou **pozastavené**, **aktivní** a **neaktivní**. |
| `RecurrenceInterval` | Interval opakování zadaný při vytváření sestavy |
| `RecurrenceCount` | Počet opakování poskytnutý během vytváření sestavy |
| `CallbackUrl` | Adresa URL zpětného volání poskytnutá v žádosti |
| `Format` | Formát souborů sestav Možné hodnoty jsou CSV nebo TSV. |
| `TotalCount` | Počet datových sad v poli hodnot |
| `StatusCode` | Kód výsledku<br>Možné hodnoty jsou 200, 400, 401, 403, 500 |
| `message` | Stavová zpráva od spuštění rozhraní API |
|||

## <a name="get-report-executions-api"></a>Získat rozhraní API pro spouštění sestav

Tuto metodu můžete použít k dotazování na stav spuštění sestavy pomocí metody `ReportId` přijaté z [rozhraní API pro vytvoření sestavy](#create-report-api). Metoda vrátí odkaz pro stažení sestavy, pokud je sestava připravena ke stažení. V opačném případě metoda vrátí stav. Toto rozhraní API můžete použít také k získání všech spuštění, ke kterým došlo pro danou sestavu.

> [!IMPORTANT]
> Toto rozhraní API má výchozí parametry dotazu nastavené pro `executionStatus=Completed` a  `getLatestExecution=true` . Proto volání rozhraní API před prvním úspěšným spuštěním sestavy vrátí 404. Nedokončené spouštění lze získat nastavením `executionStatus=Pending` .

*Syntaxe žádosti*

| Metoda | Identifikátor URI žádosti |
| ------------ | ------------- |
| Získat | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/{reportId}?executionId={executionId}&executionStatus={executionStatus}&getLatestExecution={getLatestExecution}` |
|||

*Hlavička žádosti*

| Hlavička | Typ | Description |
| ------ | ------ | ------ |
| Autorizace | řetězec | Povinná hodnota. Přístupový token Azure Active Directory (Azure AD). Formát je `Bearer <token>` . |
| Typ obsahu | řetězec | `application/json` |
||||

*Parametr cesty*

Žádné

*Parametr dotazu*

| Název parametru | Požaduje se | Typ | Popis |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Ano | řetězec | Filtr pro získání podrobností o spuštění pouze sestav, které jsou `reportId` uvedeny v tomto argumentu. Vícenásobný `reportIds` lze zadat oddělením středníkem ";". |
| `executionId` | No | řetězec | Filtr pro získání podrobností o pouze sestavách, které jsou `executionId` uvedeny v tomto argumentu. Vícenásobný `executionIds` lze zadat oddělením středníkem ";". |
| `executionStatus` | No | řetězec/výčet | Filtr pro získání podrobností o pouze sestavách, které jsou `executionStatus` uvedeny v tomto argumentu.<br>Platné hodnoty jsou: `Pending` , `Running` , `Paused` a. `Completed` <br>Výchozí hodnota je `Completed`. Více stavů je možné zadat tak, že je oddělíte středníkem (;). |
| `getLatestExecution` | No | boolean | Rozhraní API vrátí podrobnosti o posledním spuštění sestavy.<br>Ve výchozím nastavení je tento parametr nastaven na hodnotu `true` . Pokud se rozhodnete předat hodnotu tohoto parametru jako `false` , pak rozhraní API vrátí poslední 90 dní instance spuštění. |
|||||

*Datová část požadavku*

Žádné

*Ukázková odpověď*

Datová část odpovědi je strukturována takto:

Kódy odpovědí: 200, 400, 401, 403, 404, 500

Příklad datové části odpovědi:

```json
{
    "value": [
        {
            "executionId": "a0bd78ad-1a05-40fa-8847-8968b718d00f",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 4,
            "recurrenceCount": 10,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Completed",
            "reportAccessSecureLink": "https://<path to report for download>",
            "reportExpiryTime": null,
            "reportGeneratedTime": "2021-01-13T14:40:46Z"
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

Po dokončení zpracování sestavy se zobrazí stav spuštění `Completed` . Sestavu si můžete stáhnout tak, že vyberete adresu URL v `reportAccessSecureLink` .

*Glosář*

Definice klíčů prvků v odpovědi

| Parametr | Popis |
| ------------ | ------------- |
| `ExecutionId` | Univerzálně jedinečný identifikátor (UUID) instance spuštění |
| `ReportId` | ID sestavy přidružené k instanci spuštění |
| `RecurrenceInterval` | Interval opakování zadaný při vytváření sestavy |
| `RecurrenceCount` | Počet opakování poskytnutý během vytváření sestavy |
| `CallbackUrl` | Adresa URL zpětného volání přidružená k instanci spuštění |
| `Format` | Formát generovaného souboru na konci provádění |
| `ExecutionStatus` | Stav instance spuštění sestavy<br>Platné hodnoty jsou: `Pending` , `Running` , `Paused` a. `Completed` |
| `ReportAccessSecureLink` | Odkaz, přes který se k sestavě dá připojovat zabezpečeně |
| `ReportExpiryTime` | Čas UTC, po kterém vyprší platnost odkazu na sestavu v tomto formátu: RRRR-MM-ddTHH: mm: ssZ |
| `ReportGeneratedTime` | Čas UTC, kdy byla sestava generována v tomto formátu: RRRR-MM-ddTHH: mm: ssZ |
| `TotalCount` | Počet datových sad v poli hodnot |
| `StatusCode` | Kód výsledku <br>Možné hodnoty jsou 200, 400, 401, 403, 404 a 500. |
| `message` | Stavová zpráva od spuštění rozhraní API |
|||

## <a name="next-steps"></a>Další kroky
- Rozhraní API můžete vyzkoušet prostřednictvím [adresy URL rozhraní Swagger](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html) .
- Začínáme s programovým přístupem k analytickým datům
