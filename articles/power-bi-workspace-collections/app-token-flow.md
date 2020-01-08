---
title: Ověřování a autorizace Power BI kolekcí pracovních prostorů
description: Ověřování a autorizace pomocí Power BIch kolekcí pracovních prostorů.
services: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 8fcd7caffb041c57090d7256361421cb49a9a5fc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427115"
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Ověřování a autorizace pomocí Power BIch kolekcí pracovních prostorů

Power BI kolekce pracovních prostorů používají **klíče** a **tokeny aplikací** k ověřování a autorizaci místo explicitního ověřování koncových uživatelů. V tomto modelu vaše aplikace spravuje ověřování a autorizaci pro koncové uživatele. V případě potřeby aplikace vytvoří a pošle tokeny aplikace, které říká naší službě, aby vygenerovala požadovanou sestavu. Tento návrh nevyžaduje, aby vaše aplikace používala Azure Active Directory pro ověřování a autorizaci uživatelů, i když stále můžete.

> [!IMPORTANT]
> Kolekce Pracovních prostorů Power BI jsou zastaralé a dostupné do června 2018 nebo do data uvedeného ve vaší smlouvě. Doporučujeme naplánovat migraci do Power BI Embedded, předejdete tak výpadkům vaší aplikace. Informace o postupu migrace dat do Power BI Embedded najdete v tématu [Migrace obsahu kolekcí Pracovních prostorů Power BI do Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="two-ways-to-authenticate"></a>Dva způsoby ověřování

**Klíč** – můžete použít klíče pro všechny Power BI kolekce pracovních prostorů REST API volání. Klíče najdete v **portál Microsoft Azure** tím, že vyberete **všechna nastavení** a pak **přístupové klíče**. Klíč vždycky zacházet, jako by se jedná o heslo. Tyto klíče mají oprávnění k provedení libovolného REST API volání na konkrétní kolekci pracovních prostorů.

Pokud chcete použít klíč pro volání REST, přidejte následující autorizační hlavičku:

    Authorization: AppKey {your key}

**Tokeny aplikace** – tokeny aplikace se používají pro všechny požadavky na vložení. Jsou navržené tak, aby běžely na straně klienta. Token je omezený na jedinou sestavu a osvědčeným postupem pro nastavení času vypršení platnosti.

Tokeny aplikace jsou tokenem JWT (JSON Web Token), který je podepsán jedním z vašich klíčů.

Token vaší aplikace může obsahovat následující deklarace identity:

| Deklarovat | Popis |    
| --- | --- |
| **ver** |Verze tokenu aplikace 0.2.0 je aktuální verze. |
| **AUD** |Zamýšlený příjemce tokenu. Pro Power BI kolekce pracovních prostorů použijte: *https:\//Analysis.Windows.NET/PowerBI/API*. |
| **iss** |Řetězec, který označuje aplikaci, která token vystavila. |
| **type** |Typ tokenu aplikace, který se vytváří. Aktuální jediný podporovaný typ je **vložený**. |
| **WCN** |Název kolekce pracovních prostorů, pro kterou se token vystavuje |
| **WID** |ID pracovního prostoru, pro který se token vystavuje |
| **Mezinárodní** |ID sestavy, pro kterou se token vystavuje |
| **uživatelské jméno** (nepovinné) |V případě použití s RLS je uživatelské jméno řetězec, který může při použití pravidel zabezpečení na úrovni řádků identifikovat uživatele. |
| **role** (volitelné) |Řetězec obsahující role, které se mají vybrat při použití pravidel zabezpečení na úrovni řádků Při předávání více než jedné role by se měly předat jako Sting pole. |
| **spojovací bod** služby (volitelné) |Řetězec obsahující obory oprávnění. Při předávání více než jedné role by se měly předat jako Sting pole. |
| **exp** (volitelné) |Určuje čas, kdy platnost tokenu vyprší. Hodnota by měla být předána jako časová razítka systému UNIX. |
| **NBF** (volitelné) |Určuje čas, kdy se token spouští jako platný. Hodnota by měla být předána jako časová razítka systému UNIX. |

Vzorový token aplikace vypadá nějak takto:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

Při dekódování vypadá nějak takto:

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

V sadách SDK jsou k dispozici metody, které usnadňují vytváření tokenů aplikací. Například pro .NET se můžete podívat na třídu [Microsoft. PowerBI. Security. PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) a metody [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) .

Pro sadu .NET SDK můžete odkazovat na [obory](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>Obory

Při použití tokenů embed můžete chtít omezit využití prostředků, ke kterým udělíte přístup. Z tohoto důvodu můžete vygenerovat token s vymezenými oprávněními.

Níže jsou uvedené dostupné obory pro Power BI kolekce pracovních prostorů.

|Rozsah|Popis|
|---|---|
|DataSet. Read|Poskytuje oprávnění ke čtení zadaného objektu DataSet.|
|Dataset.Write|Poskytuje oprávnění k zápisu do zadané datové sady.|
|DataSet. nepřesný|Poskytuje oprávnění ke čtení a zápisu do zadané datové sady.|
|Sestava. Přečtěte si|Poskytuje oprávnění k zobrazení zadané sestavy.|
|Sestava. pročtení|Poskytuje oprávnění k zobrazení a úpravě zadané sestavy.|
|Pracovní prostor. Report. Create|Poskytuje oprávnění k vytvoření nové sestavy v rámci zadaného pracovního prostoru.|
|Pracovní prostor. Report. Copy|Poskytuje oprávnění ke klonování existující sestavy v zadaném pracovním prostoru.|

Můžete zadat více oborů pomocí mezery mezi rozsahy, podobně jako následující.

```csharp
string scopes = "Dataset.Read Workspace.Report.Create";
```

**Vyžadované deklarace identity – obory**

spojovací bod služby: {scopesClaim} scopesClaim může být řetězec nebo pole řetězců, který označuje povolená oprávnění pro prostředky pracovního prostoru (sestava, datová sada atd.).

Dekódový token s definovanými obory by vypadal podobně jako:

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

|Operace|Cílový prostředek|Oprávnění tokenu|
|---|---|---|
|Vytvoří novou sestavu na základě datové sady (v paměti).|Datová sada|DataSet. Read|
|Vytvoření nové sestavy na základě datové sady (v paměti) a uložení sestavy.|Datová sada|* DataSet. Read<br>* Pracovní prostor. Report. Create|
|Zobrazení a prozkoumání/úprava (v paměti) existující sestavy. Sestava. Read implikuje objekt DataSet. Read. Report. Read nepovoluje ukládání úprav.|Zpráva|Sestava. Přečtěte si|
|Úprava a uložení existující sestavy.|Zpráva|Sestava. pročtení|
|Uloží kopii sestavy (Uložit jako).|Zpráva|* Report. Read<br>* Pracovní prostor. Report. Copy|

## <a name="heres-how-the-flow-works"></a>Tady je postup, jak tok funguje.
1. Zkopírujte klíče rozhraní API do aplikace. Klíče můžete získat v **Azure Portal**.
   
    ![Kde najít klíče rozhraní API v Azure Portal](media/get-started-sample/azure-portal.png)
1. Token uplatňuje deklaraci identity a má čas vypršení platnosti.
   
    ![Deklarace tokenu aplikace – deklarace identity tokenu](media/get-started-sample/token-2.png)
1. Token se podepisuje pomocí přístupových klíčů rozhraní API.
   
    ![Tok tokenu aplikace – token se podepisuje.](media/get-started-sample/token-3.png)
1. Žádosti uživatelů o zobrazení sestavy.
   
    ![Token aplikace – požadavky uživatelů na zobrazení sestavy](media/get-started-sample/token-4.png)
1. Token se ověřuje pomocí přístupových klíčů rozhraní API.
   
   ![Tok tokenu aplikace – token je ověřený.](media/get-started-sample/token-5.png)
1. Power BI kolekce pracovních prostorů odesílá uživateli sestavu.
   
   ![Tok tokenu aplikace – služba odeslat zprávu uživateli](media/get-started-sample/token-6.png)

Po **Power BI kolekce pracovních prostorů** pošle uživateli sestavu, uživatel může sestavu zobrazit ve vlastní aplikaci. Pokud jste například importovali [ukázku analýzy Sales data PBIX](https://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), Ukázková webová aplikace by vypadala takto:

![Ukázka sestavy vložené v aplikaci](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Viz také

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[Začínáme s ukázkami kolekcí pracovních prostorů v Microsoft Power BI](get-started-sample.md)  
[Běžné scénáře kolekcí pracovních prostorů pro Microsoft Power BI](scenarios.md)  
[Začínáme s kolekcemi pracovních prostorů Microsoft Power BI](get-started.md)  
[PowerBI-CSharp úložiště Git](https://github.com/Microsoft/PowerBI-CSharp)

Chcete se na něco zeptat? [Vyzkoušejte komunitu Power BI](https://community.powerbi.com/)
