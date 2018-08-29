---
title: Volání koncové body REST z Azure Logic Apps | Dokumentace Microsoftu
description: Automatizace úloh a pracovních postupů, které komunikují se koncové body REST s využitím HTTP + Swagger konektoru v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: eccfd87c-c5fe-4cf7-b564-9752775fd667
tags: connectors
ms.topic: article
ms.date: 07/18/2016
ms.openlocfilehash: e96e271fbb50a2485a22fab061ea160dc00cf3d6
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123168"
---
# <a name="call-rest-endpoints-with-http--swagger-connector-in-azure-logic-apps"></a>Koncové body REST s HTTP + Swagger pro volání konektoru v Azure Logic Apps

Prvotřídní konektor pro libovolný koncový bod REST prostřednictvím můžete vytvořit [dokument Swagger](https://swagger.io) při použití protokolu HTTP + Swagger akci pracovního postupu aplikace logiky. Můžete také rozšířit aplikace logiky pro volání libovolného koncového bodu REST s prvotřídní prostředí návrhář aplikace logiky.

Informace o vytváření aplikací logiky s využitím konektorů najdete v tématu [vytvořit novou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>Použít protokol HTTP + Swagger jako aktivační události nebo akce

HTTP + Swagger aktivovat a akce, které fungují stejně jako [akce HTTP](connectors-native-http.md) zveřejněním strukturu rozhraní API a vytvořené jako výstupy z poskytovat lepší prostředí v návrháři aplikace logiky, ale [Swagger metadata](https://swagger.io). Můžete také použít HTTP + Swagger konektor jako trigger. Pokud chcete implementovat cyklického dotazování aktivační událost, mají tvar dotazování, který je popsaný v [vytvoření vlastních rozhraní API volat jiné rozhraní API, služby a systémy z aplikací logiky](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

Další informace o [triggery aplikace logiky a akce](connectors-overview.md).

Tady je příklad toho, jak pomocí HTTP + Swagger operace jako akci v pracovním postupu v aplikaci logiky.

1. Vyberte **nový krok** tlačítko.
2. Vyberte **přidat akci**.
3. Zadejte do vyhledávacího pole Akce **swagger** seznamu HTTP + Swagger akce.
   
    ![Vyberte HTTP + Swagger akce](./media/connectors-native-http-swagger/using-action-1.png)
4. Zadejte adresu URL pro dokument Swagger:
   
   * Pro práci v návrháři aplikace logiky, musí být adresa URL koncového bodu HTTPS a povolili CORS.
   * Pokud dokument Swagger nesplňuje tento požadavek, můžete použít [služby Azure Storage s povolením CORS](#hosting-swagger-from-storage) má dokument uložit.
5. Klikněte na tlačítko **Další** ke čtení a vykreslení z dokumentu Swagger.
6. Přidejte všechny parametry, které jsou požadovány pro volání HTTP.
   
    ![Dokončení akce HTTP](./media/connectors-native-http-swagger/using-action-2.png)
7. Klepnutím na tlačítko Uložit a publikovat aplikaci logiky, **Uložit** na panelu nástrojů návrháře.

### <a name="host-swagger-from-azure-storage"></a>Swagger hostitele ze služby Azure Storage
Můžete tak, aby odkazovaly dokument Swagger, který není hostitelem nebo, který nesplňuje požadavky na nepůvodního zdroje pro návrháře a zabezpečení. Chcete-li vyřešit tento problém, můžete ukládat dokumentu Swagger ve službě Azure Storage a povolení CORS pro odkaz dokument.  

Tady je postup pro vytvoření, konfiguraci a ukládat dokumenty Swagger ve službě Azure Storage:

1. [Vytvoření účtu služby Azure storage s Azure Blob storage](../storage/common/storage-create-storage-account.md). Chcete-li provést tento krok, nastavit oprávnění, která **veřejný přístup**.

2. Povolení CORS na objekt blob. 

   Automaticky nakonfigurovat toto nastavení, můžete použít [tento skript Powershellu](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

3. Nahrajte soubor Swagger do objektu blob. 

   Tento krok můžete provést [webu Azure portal](https://portal.azure.com) nebo z nástroje, jako je [Průzkumníka služby Azure Storage](http://storageexplorer.com/).

4. Odkazovat na odkazu HTTPS do dokumentu ve službě Azure Blob storage. 

   Odkaz používá tento formát:

   `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`

## <a name="technical-details"></a>Technické podrobnosti
Toto jsou podrobnosti pro aktivační události a akce, která tento HTTP + Swagger konektor podporuje.

## <a name="http--swagger-triggers"></a>HTTP + Swagger aktivační události
Trigger je událost, která umožňuje spustit pracovní postup, který je definován v aplikaci logiky. [Další informace o aktivačních událostech.](connectors-overview.md) HTTP + Swagger konektor má jeden trigger.

| Trigger | Popis |
| --- | --- |
| HTTP + Swagger |Volání HTTP a vrátí obsah odpovědi |

## <a name="http--swagger-actions"></a>HTTP + Swagger akce
Akce je operace, která provádí pracovní postup, který je definován v aplikaci logiky. [Další informace o akcích.](connectors-overview.md) HTTP + Swagger konektor má jednu akci je to možné.

| Akce | Popis |
| --- | --- |
| HTTP + Swagger |Volání HTTP a vrátí obsah odpovědi |

### <a name="action-details"></a>Detaily akce
HTTP + Swagger konektoru obsahuje jednu akci je to možné. Toto je informace o jednotlivých akcí, jejich požadované a volitelné vstupní pole a odpovídající podrobnosti výstupu, které jsou spojeny s jejich využití.

#### <a name="http--swagger"></a>HTTP + Swagger
Ujistěte se, odchozí požadavek HTTP, podporu metadat Swagger.
Hvězdička (*) znamená, že požadované pole.

| Zobrazované jméno | Název vlastnosti | Popis |
| --- | --- | --- |
| Metoda * |method |Příkaz HTTP použít. |
| IDENTIFIKÁTOR URI * |identifikátor uri |Identifikátor URI pro požadavek HTTP. |
| Záhlaví |hlavičky |Objekt JSON záhlaví HTTP, které chcete zahrnout. |
| Tělo |hlavní část |Požadavku HTTP. |
| Authentication |Ověřování |Ověřování pro žádosti. Další informace najdete v tématu [konektor HTTP](connectors-native-http.md#authentication). |

**Podrobnosti výstupu**

Odpověď protokolu HTTP

| Název vlastnosti | Typ dat | Popis |
| --- | --- | --- |
| Záhlaví |objekt |Hlavičky odpovědi |
| Tělo |objekt |Objekt odpovědi |
| Stavový kód |int |Stavový kód HTTP |

### <a name="http-responses"></a>Odpovědi protokolu HTTP
Při volání různé akce, může se zobrazit některé odpovědi. Následuje tabulka, která bude uvádět odpovídající odpovědí a popisy.

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
* [Vyhledání dalších konektorů](apis-list.md)