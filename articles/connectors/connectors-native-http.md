---
title: Komunikovat s žádný koncový bod přes protokol HTTP - Azure Logic Apps | Microsoft Docs
description: Vytvoření aplikace logiky, která může komunikovat s žádný koncový bod přes protokol HTTP
services: logic-apps
author: jeffhollan
manager: anneta
editor: ''
documentationcenter: ''
tags: connectors
ms.assetid: e11c6b4d-65a5-4d2d-8e13-38150db09c0b
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: 355d1e02ef8195bcee469b77976f1a5834f192d8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="get-started-with-the-http-action"></a>Začínáme s akce HTTP

Pomocí akce HTTP můžete rozšířit pracovní postupy pro vaši organizaci a komunikaci pomocí protokolu HTTP pro libovolný koncový bod.

Můžete:

* Vytvořte logiku aplikace pracovní postupy, které aktivovat (aktivační události), kdy web, který spravujete přestane fungovat.
* Komunikovat s žádný koncový bod přes protokol HTTP rozšířit vaše pracovní postupy k dalším službám.

Chcete-li začít používat akce HTTP v aplikaci logiky, přečtěte si téma [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-http-trigger"></a>Pomocí triggeru protokolu HTTP
Aktivační událost je událost, která můžete použít ke spuštění pracovního postupu, který je definován v aplikaci logiky. [Další informace o aktivační události](connectors-overview.md).

Zde je v sekvenci příklad toho, jak nastavit triggeru protokolu HTTP v Návrháři logiku aplikace.

1. Přidejte triggeru protokolu HTTP v aplikaci logiky.
2. Zadejte parametry pro koncový bod HTTP, které chcete dotazovat.
3. Změna intervalu opakování na tom, jak často má dotazovat.

   Aplikace logiky teď aktivuje s obsahem, který je vrácen během každé kontroly.

   ![Trigger HTTP](./media/connectors-native-http/using-trigger.png)

### <a name="how-the-http-trigger-works"></a>Jak funguje triggeru protokolu HTTP

Aktivace protokolu HTTP odešle volání koncový bod protokolu HTTP na intervalu opakování. Ve výchozím kódu odpovědi HTTP, která je nižší než 300 způsobí, že aplikace logiky ke spuštění. Chcete-li určit, zda by měly aktivovat aplikaci logiky, můžete upravit aplikaci logiky v zobrazení kódu a přidejte podmínku, která vyhodnotí po volání protokolu HTTP. Tady je příklad HTTP aktivační událost, která aktivuje se v případě vrácený kód stavu je větší než nebo rovno `400`.

```javascript
"Http":
{
    "conditions": [
        {
            "expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
        }
    ],
    "inputs": {
        "method": "GET",
        "uri": "https://blogs.msdn.microsoft.com/logicapps/",
        "headers": {
            "accept-language": "en"
        }
    },
    "recurrence": {
        "frequency": "Second",
        "interval": 15
    },
    "type": "Http"
}
```

Úplné podrobnosti o parametrech aktivace protokolu HTTP jsou k dispozici na [MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger).

## <a name="use-the-http-action"></a>Pomocí akce HTTP

Akce je operace, která se provádí v pracovním postupu, který je definován v aplikaci logiky. 
[Další informace o akcích](connectors-overview.md).

1. Zvolte **nový krok** > **přidat akci**.
3. Zadejte do vyhledávacího pole Akce **http** seznam akce HTTP.
   
    ![Vyberte akci HTTP](./media/connectors-native-http/using-action-1.png)

4. Přidejte všechny požadované parametry pro volání protokolu HTTP.
   
    ![Dokončení akce HTTP](./media/connectors-native-http/using-action-2.png)

5. Na panelu nástrojů návrháře, klikněte na tlačítko **Uložit**. Aplikace logiky uložení a publikování (aktivovaný) ve stejnou dobu.

## <a name="http-trigger"></a>Trigger HTTP
Tady jsou uvedené podrobnosti pro aktivační událost, která tento konektor podporuje. Konektor protokolu HTTP má jedna aktivační událost.

| Trigger | Popis |
| --- | --- |
| HTTP |Provede volání protokolu HTTP a vrátí obsahu odpovědi. |

## <a name="http-action"></a>Akce HTTP
Tady jsou uvedené podrobnosti pro akci, která tento konektor podporuje. Konektor protokolu HTTP má jednu možné akci.

| Akce | Popis |
| --- | --- |
| HTTP |Provede volání protokolu HTTP a vrátí obsahu odpovědi. |

## <a name="http-details"></a>Podrobnosti protokolu HTTP
Následující tabulky popisují požadované a volitelné vstupních polí pro akce a odpovídající výstup podrobnosti, které jsou spojené s použitím akce.

#### <a name="http-request"></a>Požadavek protokolu HTTP
Níže jsou uvedeny vstupních polí pro akce, která umožňuje odchozí požadavku HTTP.
A * znamená, že je povinné pole.

| Zobrazované jméno | Název vlastnosti | Popis |
| --- | --- | --- |
| Metoda * |method |Příkaz protokolu HTTP k použití |
| IDENTIFIKÁTOR URI * |identifikátor uri |Identifikátor URI pro požadavek HTTP |
| Záhlaví |hlavičky |Objekt JSON zahrnují hlaviček protokolu HTTP |
| Tělo |hlavní část |Požadavek HTTP |
| Authentication |Ověřování |Podrobnosti v [ověřování](#authentication) části |

<br>

#### <a name="output-details"></a>Podrobnosti výstupu
Níže jsou uvedeny podrobnosti výstup pro odpověď HTTP.

| Název vlastnosti | Typ dat | Popis |
| --- | --- | --- |
| Záhlaví |objekt |Hlavičky odpovědi |
| Tělo |objekt |Objekt odpovědi |
| Stavový kód |celá čísla |Stavový kód HTTP |

## <a name="authentication"></a>Authentication
Funkce Logic Apps umožňuje používat různé typy ověřování proti koncových bodů protokolu HTTP. Můžete použít tento ověřování s **HTTP**,  **[HTTP + Swagger](connectors-native-http-swagger.md)**, a **[Webhooku protokolu HTTP](connectors-native-webhook.md)** konektory. Následující typy ověřování se dají konfigurovat:

* [Základní ověřování](#basic-authentication)
* [Ověřování certifikátu klienta](#client-certificate-authentication)
* [Ověřování Azure Active Directory (Azure AD) OAuth](#azure-active-directory-oauth-authentication)

#### <a name="basic-authentication"></a>Základní ověřování

Následující objekt ověřování je potřeba pro základní ověřování.
A * znamená, že je povinné pole.

| Název vlastnosti | Typ dat | Popis |
| --- | --- | --- |
| Typ * |type |Typ ověřování (musí být `Basic` pro základní ověřování) |
| Uživatelské jméno * |uživatelské jméno |Uživatelské jméno k ověření |
| Heslo * |heslo |Heslo k ověření |

> [!TIP]
> Pokud chcete použít heslo, které nelze načíst z definice, použití `securestring` parametr a `@parameters()`  
>  [funkce definice pracovního postupu](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-parameters-and-inputs-within-a-workflow).

Příklad:

```javascript
{
    "type": "Basic",
    "username": "user",
    "password": "test"
}
```

#### <a name="client-certificate-authentication"></a>Ověřování klientských certifikátů

Následující objekt ověřování je potřeba pro ověřování pomocí certifikátu klienta. A * znamená, že je povinné pole.

| Název vlastnosti | Typ dat | Popis |
| --- | --- | --- |
| Typ * |type |Typ ověřování (musí být `ClientCertificate` pro klientské certifikáty SSL) |
| PFX* |Soubor PFX |Obsah kódováním Base64 soubor Personal Information Exchange (PFX) |
| Heslo * |heslo |Heslo pro přístup k souboru PFX |

> [!TIP]
> Chcete-li použít parametr, který nebude možné číst v definici po uložení aplikaci logiky, můžete použít `securestring` parametr a `@parameters()`  
>  [funkce definice pracovního postupu](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-parameters-and-inputs-within-a-workflow).

Příklad:

```javascript
{
    "type": "ClientCertificate",
    "pfx": "aGVsbG8g...d29ybGQ=",
    "password": "@parameters('myPassword')"
}
```

#### <a name="azure-ad-oauth-authentication"></a>Ověřování služby Azure AD OAuth
Následující objekt ověřování je potřeba pro ověřování Azure AD OAuth. A * znamená, že je povinné pole.

| Název vlastnosti | Typ dat | Popis |
| --- | --- | --- |
| Typ * |type |Typ ověřování (musí být `ActiveDirectoryOAuth` pro Azure AD OAuth) |
| Klienta * |tenant |Identifikátor klienta pro klienta Azure AD |
| Cílová skupina * |Cílová skupina |Prostředek se požaduje autorizaci používat. Příklad: `https://management.core.windows.net/` |
| Klient ID * |clientId |Identifikátor klienta pro aplikaci Azure AD |
| Tajný klíč * |Tajný kód |Tajný klíč klienta, který požaduje tokenu |

> [!TIP]
> Můžete použít `securestring` parametr a `@parameters()` [funkce definice pracovního postupu](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-parameters-and-inputs-within-a-workflow) použít parametr, který nebude možné číst v definici po uložení.
> 
> 

Příklad:

```javascript
{
    "type": "ActiveDirectoryOAuth",
    "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "audience": "https://management.core.windows.net/",
    "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
    "secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
}
```

## <a name="next-steps"></a>Další postup
Teď vyzkoušet platformu a [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ostatní konektory k dispozici v Logic Apps můžete prozkoumat pohledem na našem [rozhraní API seznamu](apis-list.md).

