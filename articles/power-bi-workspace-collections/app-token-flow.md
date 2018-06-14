---
title: Ověřování a autorizaci s Power BI prostoru kolekce | Microsoft Docs
description: Ověřování a autorizaci s Power BI prostoru kolekce.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: 1c1369ea-7dfd-4b6e-978b-8f78908fd6f6
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 74d34e708fb74daa295642d50643b78af8f6cb7a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31412181"
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Ověřování a autorizaci s Power BI prostoru kolekce

Power BI prostoru kolekce použití **klíče** a **tokeny aplikací** pro ověřování a autorizaci, namísto ověřování explicitní koncového uživatele. V tomto modelu aplikaci spravuje ověřování a autorizace pro koncové uživatele. Pokud je to nezbytné, vaše aplikace vytvoří a odešle tokeny aplikací, které informují naši službu k vykreslení požadovanou sestavu. Tento návrh nevyžaduje vaše aplikace použít Azure Active Directory pro uživatele ověřování a autorizaci, i když stále můžete.

> [!IMPORTANT]
> Kolekce Pracovních prostorů Power BI jsou zastaralé a dostupné do června 2018 nebo do data uvedeného ve vaší smlouvě. Doporučujeme naplánovat migraci do Power BI Embedded, předejdete tak výpadkům vaší aplikace. Informace o postupu migrace dat do Power BI Embedded najdete v tématu [Migrace obsahu kolekcí Pracovních prostorů Power BI do Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="two-ways-to-authenticate"></a>Dva způsoby ověření

**Klíč** -klíče můžete použít pro všechny Power BI prostoru kolekce volání rozhraní REST API. Klíče lze nalézt v **portálu Microsoft Azure** výběrem **všechna nastavení** a potom **přístupové klíče**. Vždy považovat klíč jako by ji heslo. Tyto klíče mají oprávnění k provádění jakéhokoli rozhraní API REST volání na kolekci konkrétní pracovní prostor.

Při volání REST pomocí klíče, přidejte následující hlavičku autorizace:

    Authorization: AppKey {your key}

**Tokenu aplikace** -tokeny aplikací se používají pro všechny požadavky vnoření. Jsou navržené ke spuštění klienta. Token je omezený na jednu sestavu a jeho osvědčený postup, chcete-li nastavit čas vypršení platnosti.

Jsou aplikace tokeny JWT (JSON Web Token), který je podepsaný pomocí jedné z vašich klíčů.

Váš token zabezpečení aplikace může obsahovat následující deklarace identity:

| Deklarovat | Popis |
| --- | --- |
| **ver** |Verze tokenu aplikace. 0.2.0 je aktuální verze. |
| **oblast** |Zamýšlený příjemce tokenu. Kolekce pracovních prostorů Power BI použijte: "https://analysis.windows.net/powerbi/api." |
| **iss** |Řetězec označující aplikace, která vydala token. |
| **type** |Typ tokenu aplikace, který je právě vytvářena. Aktuální se podporuje jen typ **vložení**. |
| **WCN** |Název kolekce prostoru token se vydává. |
| **interní databáze Windows** |ID pracovního prostoru token se vydává. |
| **identifikátorů RID** |ID sestavy token se vydává. |
| **uživatelské jméno** (volitelné) |Používá se s RLS, uživatelské jméno je řetězec, který může pomoct identifikovat uživatele při použití pravidla RLS. |
| **role** (volitelné) |Řetězec obsahující rolí vyberte při použití pravidla zabezpečení na úrovni řádků. Pokud předávání víc než jedné role, že mají být předány jako pole palčivost. |
| **spojovací bod služby** (volitelné) |Řetězec obsahující obory oprávnění. Pokud předávání víc než jedné role, že mají být předány jako pole palčivost. |
| **Exp** (volitelné) |Označuje čas, kdy vyprší platnost tokenu. Hodnota mají být předány v jako systému Unix časová razítka. |
| **NBF** (volitelné) |Označuje čas, ve kterém začíná token je platný. Hodnota mají být předány v jako systému Unix časová razítka. |

Ukázkový token pro aplikaci vypadá takto:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

Když dekódovat, vypadá přibližně:

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

Nejsou k dispozici v rámci sady SDK, které usnadňují vytváření Tokeny aplikací metody. Například pro rozhraní .NET můžete prohlédnout [Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) třídy a [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_) metody.

Pro .NET SDK, můžete se podívat do [obory](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>Obory

Pokud používáte vložení tokeny, můžete omezit využití prostředků, které vám umožní získat přístup k. Z tohoto důvodu můžete vygenerovat token s vymezená oprávnění.

Níže jsou k dispozici obory pro kolekce pracovních prostorů Power BI.

|Rozsah|Popis|
|---|---|
|Dataset.Read|Poskytuje oprávnění ke čtení zadané sady dat.|
|Dataset.Write|Poskytuje oprávnění k zápisu do zadané sady dat.|
|Dataset.ReadWrite|Poskytuje oprávnění ke čtení a zápisu do zadané sady dat.|
|Report.Read|Poskytuje oprávnění k zobrazení zadané sestavy.|
|Report.ReadWrite|Poskytuje oprávnění k zobrazení a úpravě zadané sestavy.|
|Workspace.Report.Create|Poskytuje oprávnění k vytvoření nové sestavy v rámci zadaného pracovního prostoru.|
|Workspace.Report.Copy|Poskytuje oprávnění ke klonování existující sestavy v rámci zadaného pracovního prostoru.|

Můžete zadat více oborů pomocí mezeru mezi obory podobně jako tento.

```
string scopes = "Dataset.Read Workspace.Report.Create";
```

**Požadované deklarace - oborů**

spojovací bod služby: scopesClaim {scopesClaim} může být buď řetězec nebo pole řetězců, poznamenat povolené oprávnění k prostředkům prostoru (sestavy, datové sady atd.)

Token dekódované s obory, které jsou definovány, vypadat podobně jako:

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
|Vytvoření nové sestavy založené na datovou sadu (v paměti).|Datová sada|Dataset.Read|
|Vytvoření nové sestavy založené na datovou sadu (v paměti) a sestavu uložit.|Datová sada|* Dataset.Read<br>* Workspace.Report.Create|
|Zobrazení a prozkoumat či upravit (v paměti) existující sestavy. Report.Read znamená Dataset.Read. Report.Read neumožňuje ukládání úprav.|Sestava|Report.Read|
|Upravit a uložit existující sestavy.|Sestava|Report.ReadWrite|
|Uložte kopii sestavy (Uložit jako).|Sestava|* Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>Tady je Princip toku
1. Zkopírujte tyto klíče rozhraní API do vaší aplikace. Můžete získat klíče **portál Azure**.
   
    ![Kde najít klíče API na portálu Azure](media/get-started-sample/azure-portal.png)
1. Token vyhodnotí deklarace identity a má čas vypršení platnosti.
   
    ![Tok tokenu aplikace - token vyhodnotí deklarace identity](media/get-started-sample/token-2.png)
1. Získá token podepsán s klíči přístup rozhraní API.
   
    ![Získá podepsané tok tokenu aplikace - token](media/get-started-sample/token-3.png)
1. Uživatelské požadavky na Zobrazit sestavu.
   
    ![Tok tokenu aplikace - uživatel požádá o zobrazení sestavy](media/get-started-sample/token-4.png)
1. Token je ověřit s klíči přístup rozhraní API.
   
   ![Tok tokenu aplikace - token byl ověřen.](media/get-started-sample/token-5.png)
1. Kolekce pracovních prostorů Power BI odešle zprávu pro uživatele.
   
   ![Tok tokenu aplikace - service odeslat zprávu pro uživatele](media/get-started-sample/token-6.png)

Po **kolekce pracovních prostorů Power BI** zasílá sestav pro uživatele, uživatel může zobrazit sestavy ve vaší vlastní aplikaci. Například, pokud jste importovali [ukázkový soubor PBIX analýza dat prodej](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), bude vypadat ukázkovou webovou aplikaci:

![Ukázkové sestavy vložených v aplikaci](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Viz také

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Začínáme s ukázkou Microsoft Power BI prostoru kolekce](get-started-sample.md)  
[Běžné scénáře Microsoft Power BI prostoru kolekce](scenarios.md)  
[Začínáme s Microsoft Power BI prostoru kolekce](get-started.md)  
[Úložiště Git PowerBI CSharp](https://github.com/Microsoft/PowerBI-CSharp)

Chcete se ještě na něco zeptat? [Vyzkoušejte komunitu Power BI](http://community.powerbi.com/)