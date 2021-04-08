---
title: Správa watchlists v Azure Sentinel pomocí REST API | Microsoft Docs
description: Tento článek popisuje, jak se dá Azure Sentinel watchlists spravovat pomocí Log Analytics REST API k vytváření, úpravám a odstraňování watchlists a jejich položek.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2021
ms.author: yelevin
ms.openlocfilehash: ea571f9b033ba82709a13c6d32649f3228ee04b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98798406"
---
# <a name="manage-watchlists-in-azure-sentinel-using-rest-api"></a>Správa watchlists ve službě Azure Sentinel pomocí REST API

> [!IMPORTANT]
> Funkce watchlists je aktuálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

Pomocí služby Azure Sentinel, která je součástí služby Azure Monitor Log Analytics, můžete ke správě watchlists použít REST API Log Analytics. V tomto dokumentu se dozvíte, jak vytvořit, upravit a odstranit watchlists a jejich položky pomocí REST API.  Watchlists vytvořené tímto způsobem se zobrazí v uživatelském rozhraní služby Azure Sentinel.

## <a name="common-uri-parameters"></a>Společné parametry identifikátoru URI

Níže jsou uvedené společné parametry identifikátoru URI pro všechny příkazy rozhraní seznamu ke zhlédnutí API:

| Name | V | Požaduje se | Typ | Description |
|-|-|-|-|-|
| **SubscriptionId** | program | ano | Identifikátor GUID | ID předplatného Azure |
| **ResourceGroupName** | program | ano | řetězec | název skupiny prostředků v rámci předplatného |
| **WorkspaceName** | program | ano | řetězec | název pracovního prostoru Log Analytics |
| **{watchlistAlias}** | program | odpoví | řetězec | název daného seznamu ke zhlédnutí<br>\* Při načítání všech watchlists se nevyžaduje. |
| **{watchlistItemId}** | program | Ano * * | Identifikátor GUID | ID položky, která se má vytvořit, přidat nebo odstranit z seznamu ke zhlédnutí<br>\*\* Požadováno pouze pro příkazy seznamu ke zhlédnutí položky |
| **{API-Version}** | query | ano | řetězec | verze protokolu, který se používá k vytvoření tohoto požadavku. Od 29. října 2020 je verze rozhraní seznamu ke zhlédnutí API *2019-01-01-Preview* |
| **{bearerToken}** | autorizace | ano | token | autorizační token nosiče |
|  

## <a name="retrieve-all-watchlists"></a>Načíst všechny watchlists

Tento příkaz načte všechny watchlists přidružené k pracovnímu prostoru bez jejich položek.

### <a name="request-uri"></a>Identifikátor URI žádosti
(Identifikátor URI je jeden řádek, rozdělený ke snadné čitelnosti)

| Metoda | Identifikátor URI žádosti |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists?api-version={{api-version}}` |
|

### <a name="responses"></a>Odpovědi

| Stavový kód | Text odpovědi | Description |
|-|-|-|
| 200/OK | Seznam existujících watchlists nebo prázdné, pokud se nenašly žádné seznamu ke zhlédnutíy |  |
| 400/Chybný požadavek |  | Chybná syntaxe žádosti, neplatný parametr požadavku... |
|

## <a name="look-up-a-watchlist-by-name"></a>Vyhledat seznamu ke zhlédnutí podle jména

Tento příkaz načte konkrétní seznamu ke zhlédnutí přidružený k pracovnímu prostoru bez jeho položek.

### <a name="request-uri"></a>Identifikátor URI žádosti
(Identifikátor URI je jeden řádek, rozdělený ke snadné čitelnosti)

| Metoda | Identifikátor URI žádosti |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Odpovědi

| Stavový kód | Text odpovědi | Description |
|-|-|-|
| 200/OK | Požadovaný seznamu ke zhlédnutí |  |
| 400/Chybný požadavek |  | Chybná syntaxe žádosti, neplatný parametr požadavku... |
| 404/Nenalezeno |  | Nenašla se žádná seznamu ke zhlédnutí s požadovaným názvem. |
|

## <a name="create-a-watchlist"></a>Vytvoření seznamu ke zhlédnutí

Tento příkaz vytvoří seznamu ke zhlédnutí a přidá do něj položky. Má dvě volání tohoto koncového bodu pro vytvoření seznamu ke zhlédnutí a jeho položek: první z nich vytvoří seznamu ke zhlédnutí (nadřazený objekt) a druhý bude položky Přidat.

### <a name="request-uri"></a>Identifikátor URI žádosti
(Identifikátor URI je jeden řádek, rozdělený ke snadné čitelnosti)

| Metoda | Identifikátor URI žádosti |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>Text požadavku

Zde je ukázka těla žádosti žádosti o vytvoření seznamu ke zhlédnutí:

```json
{
    "properties": {
        "displayName": "High Value Assets Watchlist",
        "source": "Local file",
        "provider": "Microsoft",
        "numberOfLinesToSkip":"1",
        "rawContent": "metadata line\nheader1, header2\nval1, val2",
        "contentType": "text/csv"
    }
}
```

### <a name="responses"></a>Odpovědi

| Stavový kód | Text odpovědi | Description |
|-|-|-|
| 200/OK | Seznamu ke zhlédnutí vytvořený požadavkem, bez položek |  |
| 400/Chybný požadavek |  | Chybná syntaxe žádosti, neplatný parametr požadavku... |
| 409/konflikt |  | Operace selhala, existuje stávající seznamu ke zhlédnutí se stejným aliasem. |
|

## <a name="delete-a-watchlist"></a>Odstranit seznamu ke zhlédnutí

Tento příkaz odstraní seznamu ke zhlédnutí a jeho položky.

### <a name="request-uri"></a>Identifikátor URI žádosti
(Identifikátor URI je jeden řádek, rozdělený ke snadné čitelnosti)

| Metoda | Identifikátor URI žádosti |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Odpovědi

| Stavový kód | Text odpovědi | Description |
|-|-|-|
| 200/OK | Prázdné tělo odpovědi |  |
| 204/žádný obsah | Prázdné tělo odpovědi | Nic neodstraněno |
| 400/Chybný požadavek |  | Chybná syntaxe žádosti, neplatný parametr požadavku... |
|

## <a name="add-or-update-a-watchlist-item"></a>Přidat nebo aktualizovat položku seznamu ke zhlédnutí

Pokud se tento příkaz spustí na existujícím *watchlisItemId* (GUID), aktualizuje položku daty poskytnutými v textu žádosti. V opačném případě bude vytvořena nová položka.

### <a name="request-uri"></a>Identifikátor URI žádosti
(Identifikátor URI je jeden řádek, rozdělený ke snadné čitelnosti)

| Metoda | Identifikátor URI žádosti |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>Text požadavku

Tady je ukázka textu žádosti o žádost o přidání/aktualizaci seznamu ke zhlédnutí položky:

```json
{
    "properties": {
      "itemsKeyValue": {
           "Gateway subnet": "10.0.255.224/27",
           "Web Tier": "10.0.1.0/24",
           "Business tier": "10.0.2.0/24",
           "Private DMZ in": "10.0.0.0/27",
           "Public DMZ out": "10.0.0.96/27"
      }
}
}
```

### <a name="responses"></a>Odpovědi

| Stavový kód | Text odpovědi | Description |
|-|-|-|
| 200/OK | Položka seznamu ke zhlédnutí vytvořena nebo aktualizována žádostí |  |
| 400/Chybný požadavek |  | Chybná syntaxe žádosti, neplatný parametr požadavku... |
| 409/konflikt |  | Operace selhala, existuje stávající seznamu ke zhlédnutí se stejným aliasem. |
|

## <a name="delete-a-watchlist-item"></a>Odstranění položky seznamu ke zhlédnutí

Tento příkaz odstraní existující položku seznamu ke zhlédnutí.

### <a name="request-uri"></a>Identifikátor URI žádosti
(Identifikátor URI je jeden řádek, rozdělený ke snadné čitelnosti)

| Metoda | Identifikátor URI žádosti |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Odpovědi

| Stavový kód | Text odpovědi | Description |
|-|-|-|
| 200/OK | Prázdné tělo odpovědi |  |
| 204/žádný obsah | Prázdné tělo odpovědi | Nic neodstraněno |
| 400/Chybný požadavek |  | Chybná syntaxe žádosti, neplatný parametr požadavku... |
|

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak spravovat watchlists a jejich položky v Azure Sentinel pomocí rozhraní Log Analytics API. Další informace o Sentinel Azure najdete v následujících článcích:

- Další informace o [watchlists](watchlists.md)
- Prozkoumat další použití [rozhraní API Sentinel Azure](/rest/api/securityinsights/)