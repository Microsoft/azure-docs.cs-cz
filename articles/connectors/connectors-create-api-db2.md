---
title: Připojení k IBM DB2 – Azure Logic Apps | Dokumentace Microsoftu
description: Správa prostředků pomocí rozhraní REST API IBM DB2 a Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: plarsen, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 354e67183a36f511811d74a0685dea2e23d6c0e2
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818871"
---
# <a name="manage-ibm-db2-resources-with-azure-logic-apps"></a>IBM DB2 prostředky spravovat pomocí Azure Logic Apps

S Azure Logic Apps a konektor IBM DB2 můžete vytvořit automatizovaných úloh a pracovní postupy založené na prostředky uložené v databázi DB2. Vaše pracovní postupy můžete připojit k prostředkům ve vaší databázi, čtení a seznamu databázových tabulek, přidání řádků, změňte řádků, odstranit řádky a další. Můžete zahrnout akce ve svých aplikacích logiky, které odpovědi z databáze a zpřístupnit výstup pro další akce. 

Tento článek popisuje, jak můžete vytvořit aplikaci logiky, která provádí různých databázových operací. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="supported-platforms-and-versions"></a>Podporované platformy a verze

Konektor DB2 zahrnuje klienta společnosti Microsoft, který komunikuje přes síť TCP/IP se vzdálenými servery DB2. Tento konektor můžete použít pro přístup k databázím cloudu jako IBM Bluemix dashDB nebo IBM DB2 pro spuštění v Azure virtualizace Windows. Místní databáze DB2 se dá dostat taky po vás [instalace a nastavení na místní bránu dat](../logic-apps/logic-apps-gateway-connection.md). 

Konektor IBM DB2 podporuje tyto platformy IBM DB2 a verze IBM DB2 kompatibilní produktů, jako je například dashDB IBM Bluemix, které podporují distribuované relační databáze architektury (DRDA) SQL přístup správce (SQLAM) verze 10 a 11:

| Platforma | Verze | 
|----------|---------|
| IBM DB2 pro z/OS | 11.1, 10.1 |
| IBM DB2 pro mi | 7.3, 7.2, 7.1 |
| IBM DB2 pro LUW | 11, 10.5 |
|||

## <a name="supported-database-operations"></a>Podporované databázové operace

Konektor IBM DB2 podporuje tyto databázové operace, která mapují na odpovídající akce v konektoru:

| Operace databáze | Konektor akce | 
|--------------------|------------------|
| Seznam tabulek databáze | Získat tabulky | 
| Jeden řádek pro čtení používá vyberte | Získat řádek | 
| Číst všechny řádky pomocí vyberte | Získat řádky | 
| Přidat jeden řádek pomocí vložení | Vložit řádek | 
| Upravit jeden řádek pomocí UPDATE | Aktualizovat řádek | 
| Odebrat jeden řádek použití odstranění | Odstranit řádek | 
|||

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* Databáze IBM DB2, buď založené na cloudu nebo místně

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete přístup k databázi DB2. Tento konektor zajišťuje jenom akce, takže ke spuštění aplikace logiky vyberte samostatné aktivační událost, například, **opakování** aktivační události.
V příkladech v tomto článku používají **opakování** aktivační události.

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>Přidání akce DB2 – Get tabulky

1. V [webu Azure portal](https://portal.azure.com), otevření aplikace logiky v návrháři aplikace logiky, pokud není otevřen.

1. Pod triggerem zvolte **nový krok**.

1. Do vyhledávacího pole zadejte jako filtr "db2". V tomto příkladu v seznamu akcí vyberte tuto akci: **získat tabulky (Preview)**
   
   ![Vyberte akci](./media/connectors-create-api-db2/select-db2-action.png)

   Nyní budete vyzváni k poskytnutí podrobností o připojení pro databázi DB2. 

1. Postupujte podle kroků pro vytvoření připojení pro [cloudové databáze](#cloud-connection) nebo [místních databází](#on-premises-connection).

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>Připojit ke cloudu DB2

Chcete-li nastavit připojení, zadejte podrobnosti připojení po zobrazení výzvy, zvolte **vytvořit**a pak uložte aplikaci logiky:

| Vlastnost | Požaduje se | Popis | 
|----------|----------|-------------| 
| **Připojit přes místní bránu** | Ne | Platí pouze pro místní připojení. | 
| **Název připojení** | Ano | Název připojení, například "MyLogicApp DB2 – připojení" |
| **Server** | Ano | Číslo portu adresa nebo alias identit pro váš server DB2, například "myDB2server.cloudapp.net:50000" <p><p>**Poznámka:**: Tato hodnota je řetězec, který představuje adresu protokolu TCP/IP nebo alias, buď ve formátu IPv4 nebo IPv6, za nímž následuje dvojtečka a číslo portu TCP/IP. |
| **Database** | Ano | Název pro vaši databázi <p><p>**Poznámka:**: Tato hodnota je řetězec, který představuje architektury DRDA název pro relační databáze (RDBNAM): <p>-DB2 z/OS přijímá řetězec 16 bajtů, kde databáze se označuje jako "IBM DB2 z/OS" umístění. <br>-DB2 pro i přijímá jako řetězec 18 bajtů, kde je v databázi označované jako "IBM DB2 pro můžu" relační databáze. <br>-DB2 LUW přijímá řetězec 8 bajtů. |
| **Uživatelské jméno** | Ano | Vaše uživatelské jméno pro databázi <p><p>**Poznámka:**: Tato hodnota je řetězec, jehož délka je založen na konkrétní databázi: <p><p>-DB2 z/OS přijímá řetězec 8 bajtů. <br>-DB2 pro i přijímá řetězec 10 bajtů. <br>-DB2 pro Linux nebo UNIX přijímá řetězec 8 bajtů. <br>-DB2 pro Windows přijímá řetězec 30 bajtů. | 
| **Heslo** | Ano | Heslo pro databázi | 
|||| 

Příklad:

![Podrobné informace o připojení pro cloudové databáze](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>Připojit do DB2 lokálně

Před vytvořením připojení, musíte již mít vaše místní bránu dat nainstalovanou. Nastavení nelze dokončit, jinak vaše připojení. Pokud máte instalaci brány, pokračovat s poskytováním těchto podrobností o připojení a klikněte na tlačítko **vytvořit**.

| Vlastnost | Požaduje se | Popis | 
|----------|----------|-------------| 
| **Připojit přes místní bránu** | Ano | Platí, pokud chcete, aby připojení k místním a zobrazí místní vlastnosti připojení. | 
| **Název připojení** | Ano | Název připojení, například "MyLogicApp DB2 – připojení" | 
| **Server** | Ano | Číslo portu adresa nebo alias identit pro váš server DB2, například "myDB2server:50000" <p><p>**Poznámka:**: Tato hodnota je řetězec, který představuje adresu protokolu TCP/IP nebo alias, buď ve formátu IPv4 nebo IPv6, za nímž následuje dvojtečka a číslo portu TCP/IP. | 
| **Database** | Ano | Název pro vaši databázi <p><p>**Poznámka:**: Tato hodnota je řetězec, který představuje architektury DRDA název pro relační databáze (RDBNAM): <p>-DB2 z/OS přijímá řetězec 16 bajtů, kde databáze se označuje jako "IBM DB2 z/OS" umístění. <br>-DB2 pro i přijímá jako řetězec 18 bajtů, kde je v databázi označované jako "IBM DB2 pro můžu" relační databáze. <br>-DB2 LUW přijímá řetězec 8 bajtů. | 
| **Ověřování** | Ano | Typ ověřování pro připojení, například "Basic" <p><p>**Poznámka:**: Vyberte tuto hodnotu ze seznamu, který zahrnuje Basic nebo Windows (Kerberos). | 
| **Uživatelské jméno** | Ano | Vaše uživatelské jméno pro databázi <p><p>**Poznámka:**: Tato hodnota je řetězec, jehož délka je založen na konkrétní databázi: <p><p>-DB2 z/OS přijímá řetězec 8 bajtů. <br>-DB2 pro i přijímá řetězec 10 bajtů. <br>-DB2 pro Linux nebo UNIX přijímá řetězec 8 bajtů. <br>-DB2 pro Windows přijímá řetězec 30 bajtů. | 
| **Heslo** | Ano | Heslo pro databázi | 
| **Brána** | Ano | Název pro nainstalované místní brána dat <p><p>**Poznámka:**: Vyberte tuto hodnotu ze seznamu, který zahrnuje všechny brány data nainstalované v rámci vaší Azure předplatné a skupinu prostředků. | 
|||| 

Příklad:

![Podrobné informace o připojení pro místní databáze](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>Zobrazení výstupu tabulky

Chcete-li spustit aplikaci logiky ručně, na panelu nástrojů návrháře zvolte **spustit**. Po dokončení spuštění aplikace logiky se zobrazí výstup ze spuštění.

1. V nabídce aplikace logiky, vyberte **přehled**. 

1. V části **Souhrn**v **historie běhů** vyberte posledního spuštění, což je první položka v seznamu. 

   ![Zobrazení historie spuštění](./media/connectors-create-api-db2/run-history.png)

1. V části **běh aplikace logiky**, nyní můžete zkontrolovat stav vstupů a výstupů pro každou krok ve své aplikaci logiky. Rozbalte **získat tabulky** akce.

   ![Rozbalte akci](./media/connectors-create-api-db2/expand-action-step.png)

1. Chcete-li zobrazit vstupy, zvolte **zobrazit nezpracované vstupy**. 

1. Chcete-li zobrazit výstupy, zvolte **zobrazit nezpracované výstupy**. 

   Výstupy budou zahrnovat seznam tabulek. 
   
   ![Zobrazení výstupu tabulky](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>Získat řádek

Chcete-li načíst jeden záznam v tabulce databáze DB2, použijte **získat řádek** akce ve vaší aplikaci logiky. Tato akce spustí DB2 `SELECT WHERE` příkazu, například `SELECT FROM AREA WHERE AREAID = '99999'`.

1. Pokud jste nikdy nepoužívali DB2 akce před ve vaší aplikaci logiky, zkontrolujte jednotlivé kroky v [akce Přidat DB2 – Get tabulky](#add-db2-action) části, ale přidat **získat řádek** akce místo a pak se sem vraťte a pokračovat. 

   Po přidání **získat řádek** akce, zde je, jak se zobrazuje příklad aplikace logiky:

   ![Získat řádek akce](./media/connectors-create-api-db2/db2-get-row-action.png)

1. Zadejte hodnoty pro všechny požadované vlastnosti (*). Jakmile vyberete tabulku, zobrazuje akce relevantní vlastnosti, které jsou specifické pro záznamy v této tabulce.

   | Vlastnost | Požaduje se | Popis | 
   |----------|----------|-------------| 
   | **Název tabulky** | Ano | Tabulka, která má záznam, třeba "Oblasti" v tomto příkladu | 
   | **ID oblasti** | Ano | ID záznamu, třeba "99999" v tomto příkladu | 
   |||| 

   ![Vyberte tabulku](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. Jakmile budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**. 

### <a name="view-output-row"></a>Zobrazení výstupu řádek

Chcete-li spustit aplikaci logiky ručně, na panelu nástrojů návrháře zvolte **spustit**. Po dokončení spuštění aplikace logiky se zobrazí výstup ze spuštění.

1. V nabídce aplikace logiky, vyberte **přehled**. 

1. V části **Souhrn**v **historie běhů** vyberte posledního spuštění, což je první položka v seznamu. 

1. V části **běh aplikace logiky**, nyní můžete zkontrolovat stav vstupů a výstupů pro každou krok ve své aplikaci logiky. Rozbalte **získat řádek** akce.

1. Chcete-li zobrazit vstupy, zvolte **zobrazit nezpracované vstupy**. 

1. Chcete-li zobrazit výstupy, zvolte **zobrazit nezpracované výstupy**. 

   Výstupy zahrnují zadaný řádek. 
   
   ![Zobrazení výstupu řádek](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>Získat řádky

Chcete-li načíst všechny záznamy v tabulce databáze DB2, použijte **získat řádky** akce ve vaší aplikaci logiky. Tato akce spustí DB2 `SELECT` příkazu, například `SELECT * FROM AREA`.

1. Pokud jste nikdy nepoužívali DB2 akce před ve vaší aplikaci logiky, zkontrolujte jednotlivé kroky v [akce Přidat DB2 – Get tabulky](#add-db2-action) části, ale přidat **získat řádky** akce místo a pak se sem vraťte a pokračovat. 

   Po přidání **získat řádky** akce, zde je, jak se zobrazuje příklad aplikace logiky:

   ![Získat řádky akce](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. Otevřít **název tabulky** seznamu a potom vyberte tabulku, kterou, což je "Oblasti" v tomto příkladu: 

   ![Vyberte tabulku](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. Chcete-li zadat filtr nebo dotaz pro výsledky, zvolte **zobrazit pokročilé možnosti**.

1. Jakmile budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**. 

### <a name="view-output-rows"></a>Zobrazit výstup řádky

Chcete-li spustit aplikaci logiky ručně, na panelu nástrojů návrháře zvolte **spustit**. Po dokončení spuštění aplikace logiky se zobrazí výstup ze spuštění.

1. V nabídce aplikace logiky, vyberte **přehled**. 

1. V části **Souhrn**v **historie běhů** vyberte posledního spuštění, což je první položka v seznamu. 

1. V části **běh aplikace logiky**, nyní můžete zkontrolovat stav vstupů a výstupů pro každou krok ve své aplikaci logiky. Rozbalte **získat řádky** akce.

1. Chcete-li zobrazit vstupy, zvolte **zobrazit nezpracované vstupy**. 

1. Chcete-li zobrazit výstupy, zvolte **zobrazit nezpracované výstupy**. 

   Výstupy obsahují všechny záznamy v zadané tabulce.
   
   ![Zobrazit výstup řádky](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>Vložit řádek

Chcete-li přidat jeden záznam do DB2 databázové tabulky, použijte **vložit řádek** akce ve vaší aplikaci logiky. Tato akce spustí DB2 `INSERT` příkazu, například `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

1. Pokud jste nikdy nepoužívali DB2 akce před ve vaší aplikaci logiky, zkontrolujte jednotlivé kroky v [akce Přidat DB2 – Get tabulky](#add-db2-action) části, ale přidat **vložit řádek** akce místo a pak se sem vraťte a pokračovat. 

   Po přidání **vložit řádek** akce, zde je, jak se zobrazuje příklad aplikace logiky:

   ![Vložit řádek akce](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. Zadejte hodnoty pro všechny požadované vlastnosti (*). Jakmile vyberete tabulku, zobrazuje akce relevantní vlastnosti, které jsou specifické pro záznamy v této tabulce. 

   V tomto příkladu uvádíme vlastnosti:

   | Vlastnost | Požaduje se | Popis | 
   |----------|----------|-------------| 
   | **Název tabulky** | Ano | V tabulce kam přidat záznam, třeba "Oblasti" | 
   | **ID oblasti** | Ano | Pokud chcete přidat, jako je například "99999" Identifikátor pro oblasti | 
   | **Popis oblasti** | Ano | Popis pro oblasti, které chcete přidat, jako je například "Oblasti 99999" | 
   | **ID oblasti** | Ano | Pokud chcete přidat, jako je například "102" Identifikátor pro oblast | 
   |||| 

   Příklad:

   ![Vyberte tabulku](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. Jakmile budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**. 

### <a name="view-insert-row-outputs"></a>Zobrazení vložit řádek výstupy

Chcete-li spustit aplikaci logiky ručně, na panelu nástrojů návrháře zvolte **spustit**. Po dokončení spuštění aplikace logiky se zobrazí výstup ze spuštění.

1. V nabídce aplikace logiky, vyberte **přehled**. 

1. V části **Souhrn**v **historie běhů** vyberte posledního spuštění, což je první položka v seznamu. 

1. V části **běh aplikace logiky**, nyní můžete zkontrolovat stav vstupů a výstupů pro každou krok ve své aplikaci logiky. Rozbalte **vložit řádek** akce.

1. Chcete-li zobrazit vstupy, zvolte **zobrazit nezpracované vstupy**. 

1. Chcete-li zobrazit výstupy, zvolte **zobrazit nezpracované výstupy**. 

   Výstupy obsahovat záznam, který jste přidali do zadané tabulky.
   
   ![Zobrazit výstup s vloženého řádku](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>Aktualizovat řádek

Chcete-li aktualizovat jeden záznam v tabulce databáze DB2, použijte **aktualizovat řádek** akce ve vaší aplikaci logiky. Tato akce spustí DB2 `UPDATE` příkazu, například `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)`.

1. Pokud jste nikdy nepoužívali DB2 akce před ve vaší aplikaci logiky, zkontrolujte jednotlivé kroky v [akce Přidat DB2 – Get tabulky](#add-db2-action) části, ale přidat **aktualizovat řádek** akce místo a pak se sem vraťte a pokračovat. 

   Po přidání **aktualizovat řádek** akce, zde je, jak se zobrazuje příklad aplikace logiky:

   ![Akce Aktualizovat řádek](./media/connectors-create-api-db2/db2-update-row-action.png)

1. Zadejte hodnoty pro všechny požadované vlastnosti (*). Jakmile vyberete tabulku, zobrazuje akce relevantní vlastnosti, které jsou specifické pro záznamy v této tabulce. 

   V tomto příkladu uvádíme vlastnosti:

   | Vlastnost | Požaduje se | Popis | 
   |----------|----------|-------------| 
   | **Název tabulky** | Ano | V tabulce where aktualizovat record, jako je například "Oblasti" | 
   | **ID řádku** | Ano | ID záznamu, pokud chcete aktualizovat, jako je například "99999" | 
   | **ID oblasti** | Ano | Nové ID oblasti, jako je například "99999" | 
   | **Popis oblasti** | Ano | Nový popis oblasti, jako je například "Aktualizované 99999" | 
   | **ID oblasti** | Ano | Nové ID oblasti, jako je například "102" | 
   |||| 

   Příklad:

   ![Vyberte tabulku](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. Jakmile budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**. 

### <a name="view-update-row-outputs"></a>Vypíše zobrazení aktualizovat řádek

Chcete-li spustit aplikaci logiky ručně, na panelu nástrojů návrháře zvolte **spustit**. Po dokončení spuštění aplikace logiky se zobrazí výstup ze spuštění.

1. V nabídce aplikace logiky, vyberte **přehled**. 

1. V části **Souhrn**v **historie běhů** vyberte posledního spuštění, což je první položka v seznamu. 

1. V části **běh aplikace logiky**, nyní můžete zkontrolovat stav vstupů a výstupů pro každou krok ve své aplikaci logiky. Rozbalte **aktualizovat řádek** akce.

1. Chcete-li zobrazit vstupy, zvolte **zobrazit nezpracované vstupy**. 

1. Chcete-li zobrazit výstupy, zvolte **zobrazit nezpracované výstupy**. 

   Výstupy obsahovat záznam, který jste aktualizovali v zadané tabulky.
   
   ![Zobrazení výstupu se aktualizovaný řádek](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>Odstranit řádek

Chcete-li odstranit jednoho záznamu z tabulky DB2. databáze, použijte **odstranit řádek** akce ve vaší aplikaci logiky. Tato akce spustí DB2 `DELETE` příkazu, například `DELETE FROM AREA WHERE AREAID = '99999'`.

1. Pokud jste nikdy nepoužívali DB2 akce před ve vaší aplikaci logiky, zkontrolujte jednotlivé kroky v [akce Přidat DB2 – Get tabulky](#add-db2-action) části, ale přidat **odstranit řádek** akce místo a pak se sem vraťte a pokračovat. 

   Po přidání **odstranit řádek** akce, zde je, jak se zobrazuje příklad aplikace logiky:

   ![Odstranit řádek akce](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. Zadejte hodnoty pro všechny požadované vlastnosti (*). Jakmile vyberete tabulku, zobrazuje akce relevantní vlastnosti, které jsou specifické pro záznamy v této tabulce. 

   V tomto příkladu uvádíme vlastnosti:

   | Vlastnost | Požaduje se | Popis | 
   |----------|----------|-------------| 
   | **Název tabulky** | Ano | V tabulce where odstranit záznam, třeba "Oblasti" | 
   | **ID řádku** | Ano | ID pro záznam odstranit, jako je například "99999" | 
   |||| 

   Příklad:

   ![Vyberte tabulku](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. Jakmile budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**. 

### <a name="view-delete-row-outputs"></a>Zobrazení odstranění řádku výstupů

Chcete-li spustit aplikaci logiky ručně, na panelu nástrojů návrháře zvolte **spustit**. Po dokončení spuštění aplikace logiky se zobrazí výstup ze spuštění.

1. V nabídce aplikace logiky, vyberte **přehled**. 

1. V části **Souhrn**v **historie běhů** vyberte posledního spuštění, což je první položka v seznamu. 

1. V části **běh aplikace logiky**, nyní můžete zkontrolovat stav vstupů a výstupů pro každou krok ve své aplikaci logiky. Rozbalte **odstranit řádek** akce.

1. Chcete-li zobrazit vstupy, zvolte **zobrazit nezpracované vstupy**. 

1. Chcete-li zobrazit výstupy, zvolte **zobrazit nezpracované výstupy**. 

   Výstupy už nezahrnují záznam, který jste odstranili ze zadané tabulky.
   
   ![Zobrazení výstupu bez odstraněný řádek](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako jsou triggery, akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete v článku [konektoru referenční stránce](/connectors/db2/). 

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
