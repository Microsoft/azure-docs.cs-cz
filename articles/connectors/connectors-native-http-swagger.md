---
title: Volání REST koncové body pomocí protokolu HTTP + Swagger konektor pro Azure Logic Apps | Microsoft Docs
description: Připojit k koncové body REST z aplikace logiky prostřednictvím Swagger pomocí protokolu HTTP + Swagger konektoru
services: logic-apps
author: jeffhollan
manager: jeconnoc
editor: ''
documentationcenter: ''
tags: connectors
ms.assetid: eccfd87c-c5fe-4cf7-b564-9752775fd667
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: 4e4421b0dfe0f29c3d50764b9ca04471c73a1450
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296515"
---
# <a name="get-started-with-the-http--swagger-action"></a>Začínáme s HTTP + Swagger akce

Můžete vytvořit první třídy konektor pro libovolný koncový bod REST prostřednictvím [dokumentu Swagger](https://swagger.io) při použití protokolu HTTP + Swagger akce v pracovním postupu logiku aplikace. Můžete také rozšířit logic apps, abyste volat žádný koncový bod REST prvotřídní prostředí návrhář aplikace logiky.

Chcete-li informace o vytváření aplikací logiky s konektory, přečtěte si téma [vytvoření nové aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>Použít protokol HTTP + Swagger jako aktivační událost nebo akci.

HTTP + Swagger aktivovat a akce fungovat stejně jako [akce HTTP](connectors-native-http.md) ale zajistit lepší prostředí v návrháři aplikace logiky díky zpřístupnění strukturu rozhraní API a výstupy z [Swagger metadata](https://swagger.io). Můžete také použít HTTP + Swagger konektor jako trigger. Pokud chcete implementovat cyklického dotazování aktivační událost, postupujte podle vzoru dotazování, která je popsána v [vytvořte vlastní rozhraní API volat další rozhraní API, služeb a systémů z aplikace logiky](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

Další informace o [logiku aplikace triggery a akce](connectors-overview.md).

Tady je příklad toho, jak pomocí HTTP + operací Swagger jako akce v pracovním postupu v aplikaci logiky.

1. Vyberte **nový krok** tlačítko.
2. Vyberte **přidat akci**.
3. Zadejte do vyhledávacího pole Akce **swagger** seznamu HTTP + Swagger akce.
   
    ![Vyberte HTTP + Swagger akce](./media/connectors-native-http-swagger/using-action-1.png)
4. Zadejte adresu URL dokumentu Swagger:
   
   * Chcete-li z návrháře aplikace logiky, adresa URL musí být koncový bod HTTPS a povolení CORS.
   * Pokud dokumentu Swagger nesplňuje tento požadavek, můžete použít [Azure Storage s povolením CORS](#hosting-swagger-from-storage) k uložení dokumentů.
5. Klikněte na tlačítko **Další** ke čtení a vykreslování z dokumentu Swagger.
6. Přidejte do žádné parametry, které jsou požadovány pro volání protokolu HTTP.
   
    ![Dokončení akce HTTP](./media/connectors-native-http-swagger/using-action-2.png)
7. Pokud chcete uložit a publikovat aplikace logiky, klikněte na tlačítko **Uložit** na panelu nástrojů návrháře.

### <a name="host-swagger-from-azure-storage"></a>Swagger hostitele ze služby Azure Storage
Můžete tak, aby odkazovaly dokumentu Swagger, který není hostovaný nebo který nesplňuje požadavky cross-origin pro návrháře a zabezpečení. Chcete-li vyřešit tento problém, můžete ukládat dokumentu Swagger ve službě Azure Storage a zapnout CORS a odkazovat na dokumentu.  

Zde jsou kroky pro vytvoření, konfigurace a ukládat dokumenty Swagger ve službě Azure Storage:

1. [Vytvoření účtu úložiště Azure s Azure Blob storage](../storage/common/storage-create-storage-account.md). Chcete-li provést tento krok, nastavte oprávnění na **veřejný přístup**.

2. Povolení CORS u objektu blob. 

   Chcete-li automaticky konfigurovat toto nastavení, můžete použít [tento skript prostředí PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

3. Nahrajte soubor Swagger do objektu blob. 

   Můžete provést tento krok z [portál Azure](https://portal.azure.com) nebo z nástroje, jako je [Azure Storage Explorer](http://storageexplorer.com/).

4. Referenční odkazu HTTPS v dokumentu v Azure Blob storage. 

   Odkaz používá tento formát:

   `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`

## <a name="technical-details"></a>Technické podrobnosti
Následují podrobnosti triggery a akce, který tento HTTP + Swagger konektor podporuje.

## <a name="http--swagger-triggers"></a>HTTP + aktivační události Swagger
Aktivační událost je událost, která můžete použít ke spuštění pracovního postupu, který je definován v aplikaci logiky. [Další informace o aktivační události.](connectors-overview.md) HTTP + Swagger konektor má jedna aktivační událost.

| Trigger | Popis |
| --- | --- |
| HTTP + Swagger |Ujistěte se, volání protokolu HTTP a vrátit obsahu odpovědi |

## <a name="http--swagger-actions"></a>HTTP + Swagger akce
Akce je operace, která se provádí v pracovním postupu, který je definován v aplikaci logiky. [Další informace o akcích.](connectors-overview.md) HTTP + Swagger konektor má jednu možné akci.

| Akce | Popis |
| --- | --- |
| HTTP + Swagger |Ujistěte se, volání protokolu HTTP a vrátit obsahu odpovědi |

### <a name="action-details"></a>Detaily akce
HTTP + Swagger konektor se dodává s možné jednu akci. Dále najdete informace o jednotlivých akce, jejich povinné a nepovinné vstupní pole a odpovídající výstup podrobnosti, které jsou spojeny s jejich využití.

#### <a name="http--swagger"></a>HTTP + Swagger
Pomoc při metadat Swagger Zkontrolujte odchozí požadavku HTTP.
Znak hvězdičky (*) znamená povinné pole.

| Zobrazované jméno | Název vlastnosti | Popis |
| --- | --- | --- |
| Metoda * |method |Příkaz HTTP, používat. |
| IDENTIFIKÁTOR URI * |identifikátor uri |Identifikátor URI pro požadavek HTTP. |
| Záhlaví |hlavičky |Objekt JSON hlaviček HTTP, které chcete zahrnout. |
| Tělo |hlavní část |Požadavek HTTP. |
| Authentication |Ověřování |Ověřování chcete použít pro požadavek. Další informace najdete v tématu [HTTP konektor](connectors-native-http.md#authentication). |

**Podrobnosti o výstupu**

Odpověď protokolu HTTP

| Název vlastnosti | Typ dat | Popis |
| --- | --- | --- |
| Záhlaví |objekt |Hlavičky odpovědi |
| Tělo |objekt |Objekt odpovědi |
| Stavový kód |celá čísla |Stavový kód HTTP |

### <a name="http-responses"></a>Odpovědi HTTP
Při volání různé akce, může být určité odpovědi. Následuje tabulka, která popisuje odpovídající odpovědi a popisy.

| Název | Popis |
| --- | --- |
| 200 |OK |
| 202 |Přijato |
| 400 |Nesprávná žádost |
| 401 |Neautorizováno |
| 403 |Zakázáno |
| 404 |Nenalezené |
| 500 |Vnitřní chyba serveru Došlo k neznámé chybě. |

- - -
## <a name="next-steps"></a>Další postup

* [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Vyhledání jiných konektorů](apis-list.md)