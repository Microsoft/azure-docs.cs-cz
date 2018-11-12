---
title: Ověřování a autorizace pomocí kolekcí pracovních prostorů Power BI | Dokumentace Microsoftu
description: Ověřování a autorizace pomocí kolekcí pracovních prostorů Power BI.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: 1c1369ea-7dfd-4b6e-978b-8f78908fd6f6
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: a51664144d0dba8eeb82999b212beaf79b4503b2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51236926"
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Ověřování a autorizace pomocí kolekcí pracovních prostorů Power BI

Power BI kolekce pracovních prostorů použití **klíče** a **tokenů aplikace** pro ověřování a autorizaci, namísto ověřování koncového uživatele. V tomto modelu aplikace spravuje ověřování a autorizace pro koncové uživatele. Pokud je to nezbytné, vaše aplikace vytvoří a odešle tokenů aplikace, které informují naše služby k vykreslení požadovanou sestavu. Tento návrh nevyžaduje, aby aplikace pro použití služby Azure Active Directory k ověřování uživatelů a autorizaci, i když je stále možné.

> [!IMPORTANT]
> Kolekce Pracovních prostorů Power BI jsou zastaralé a dostupné do června 2018 nebo do data uvedeného ve vaší smlouvě. Doporučujeme naplánovat migraci do Power BI Embedded, předejdete tak výpadkům vaší aplikace. Informace o postupu migrace dat do Power BI Embedded najdete v tématu [Migrace obsahu kolekcí Pracovních prostorů Power BI do Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="two-ways-to-authenticate"></a>Dva způsoby, jak ověřit

**Klíč** -klíče můžete použít pro všechna volání rozhraní REST API Power BI pracovního prostoru kolekce. Klíče najdete v **portálu Microsoft Azure** tak, že vyberete **všechna nastavení** a potom **přístupové klíče**. Je vždy nutné považovat klíč, pokud je heslo. Tyto klíče mají oprávnění k provádění jakékoli volání na kolekci pracovních prostorů konkrétní rozhraní REST API.

Použití klíče při volání REST, přidejte následující autorizační hlavičky:

    Authorization: AppKey {your key}

**Token aplikace** -tokenů aplikace se používají pro všechny požadavky vkládání. Jsou navržené ke spuštění na straně klienta. Token je omezena na jednu sestavu a její osvědčeným postupem je nastavit čas vypršení platnosti.

Token JWT (JSON Web Token), která je podepsána jeden ze svých klíčů jsou tokeny aplikací.

Platnost tokenu aplikace může obsahovat následující deklarace:

| Deklarovat | Popis |    
| --- | --- |
| **verze** |Verze tokenu aplikace. 0.2.0 je aktuální verze. |
| **AUD** |Zamýšlený příjemce tokenu. Pro kolekce pracovních prostorů Power BI používají: *https:\//analysis.windows.net/powerbi/api*. |
| **jednotky ISS – překročené** |Řetězec označující aplikaci, která token vydala. |
| **type** |Typ tokenu aplikace, který se vytváří. Aktuální je jediný podporovaný typ **vložení**. |
| **WCN** |Název kolekce pracovních prostorů token se vydává. |
| **interní databáze Windows** |ID pracovního prostoru token se vydává. |
| **identifikátorů RID** |ID sestavy token se vydává. |
| **uživatelské jméno** (volitelné) |Uživatelské jméno, používají zabezpečení na úrovni řádků, je řetězec, který vám pomůže identifikovat uživatele při použití pravidel zabezpečení na úrovni řádků. |
| **role** (volitelné) |Řetězec obsahující role, které chcete vybrat při použití pravidel zabezpečení na úrovni řádků. Pokud předáváte více než jednu roli, měli byste je předat jako pole stingu. |
| **spojovací bod služby** (volitelné) |Řetězec obsahující obory oprávnění. Pokud předáváte více než jednu roli, měli byste je předat jako pole stingu. |
| **Exp** (volitelné) |Označuje čas, ve kterém vyprší platnost tokenu. Hodnota by měla předaný jako systému Unix časová razítka. |
| **NBF** (volitelné) |Označuje čas, ve kterém začíná token je platný. Hodnota by měla předaný jako systému Unix časová razítka. |

Token ukázkové aplikace vypadá takto:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

Když dekódovat, to vypadá podobně jako:

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

Nejsou k dispozici v rámci sady SDK, které usnadňují vytváření tokenů aplikace metody. Například pro rozhraní .NET můžete prohlédnout [Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) třídy a [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_) metody.

Pro sadu .NET SDK, můžete se podívat do [obory](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>Obory

Při použití tokenů pro vložení, můžete omezit využití prostředků, které poskytuje přístup k. Z tohoto důvodu můžete vygenerovat token s s vymezeným oborem oprávnění.

Níže jsou k dispozici obory pro kolekce pracovních prostorů Power BI.

|Rozsah|Popis|
|---|---|
|Dataset.Read|Poskytuje oprávnění ke čtení zadané datové sady.|
|Dataset.Write|Poskytuje oprávnění k zápisu do zadané datové sady.|
|Dataset.ReadWrite|Poskytuje oprávnění ke čtení a zápisu do zadané datové sady.|
|Report.Read|Poskytuje oprávnění k zobrazení zadané sestavy.|
|Report.ReadWrite|Poskytuje oprávnění k zobrazení a úpravám zadané sestavy.|
|Workspace.Report.Create|Poskytuje oprávnění k vytvoření nové sestavy v rámci zadaný pracovní prostor.|
|Workspace.Report.Copy|Poskytuje oprávnění ke klonování existující sestavy v rámci zadaný pracovní prostor.|

Pomocí mezeru mezi obory takto můžete zadat víc oborů.

```
string scopes = "Dataset.Read Workspace.Report.Create";
```

**Požadované deklarace identit - obory**

spojovací bod služby: {scopesClaim} scopesClaim může být řetězec nebo pole řetězců, zmínku povolených oprávnění k prostředkům pracovního prostoru (sestavy, datové sady atd.)

Dekódovaný token s obory definované, by vypadalo podobně jako:

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "scp": "Report.Read",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

### <a name="operations-and-scopes"></a>Operace a obory

|Operace|Cílový prostředek|Token oprávnění|
|---|---|---|
|Vytvoření nové sestavy podle datové sady (v paměti).|Datová sada|Dataset.Read|
|Vytvoření nové sestavy podle datové sady (v paměti) a uložte sestavu.|Datová sada|* Dataset.Read<br>* Workspace.Report.Create|
|Zobrazit a prozkoumat nebo upravit (v paměti) existující sestavy. Report.Read znamená Dataset.Read. Report.Read neumožňuje ukládání úprav.|Sestava|Report.Read|
|Upravit a uložit stávající sestavu.|Sestava|Report.ReadWrite|
|Uložte kopii sestavy (Uložit jako).|Sestava|* Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>Jak tok funguje
1. Zkopírování klíče rozhraní API pro vaši aplikaci. Klíče můžete získat **webu Azure portal**.
   
    ![Pokud chcete najít klíče API na webu Azure Portal](media/get-started-sample/azure-portal.png)
1. Token nepodmíněné výrazy deklarace identity a má čas vypršení platnosti.
   
    ![Tok tokenu aplikace - token vyhodnocuje deklarace identity](media/get-started-sample/token-2.png)
1. Získá token podepsán pomocí přístupových klíčů rozhraní API.
   
    ![Získá podepsané toku aplikačních tokenů - token](media/get-started-sample/token-3.png)
1. Uživatel žádá o zobrazení sestavy.
   
    ![Tok tokenu aplikace - uživatel požádá o zobrazení sestavy](media/get-started-sample/token-4.png)
1. Token se ověří pomocí přístupových klíčů rozhraní API.
   
   ![Ověření toku aplikačních tokenů - token](media/get-started-sample/token-5.png)
1. Kolekce pracovních prostorů Power BI odešle zprávu o uživateli.
   
   ![Tok tokenu aplikace – služba odeslat zprávu pro uživatele](media/get-started-sample/token-6.png)

Po **kolekce pracovních prostorů Power BI** odešle zprávu pro uživatele, uživatel můžete zobrazit sestavu ve vaší vlastní aplikaci. Například, pokud jste naimportovali [ukázka analýzy PBIX prodejní Data](https://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), ukázková webová aplikace vypadat nějak takto:

![Ukázka vložení v aplikaci](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Viz také

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Začínáme s ukázkou kolekce pracovních prostorů Microsoft Power BI](get-started-sample.md)  
[Běžné scénáře kolekce pracovních prostorů Microsoft Power BI](scenarios.md)  
[Začínáme s kolekcemi pracovních prostorů Microsoft Power BI](get-started.md)  
[Úložiště Git PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)

Chcete se ještě na něco zeptat? [Vyzkoušejte komunitu Power BI](http://community.powerbi.com/)