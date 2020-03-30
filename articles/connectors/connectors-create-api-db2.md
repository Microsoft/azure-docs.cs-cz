---
title: Přístup k prostředkům IBM DB2 a jejich správa
description: Čtení, úpravy, aktualizace a správa prostředků IBM DB2 vytvářením automatizovaných pracovních postupů pomocí aplikací Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: plarsen, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 32b482607827ee4420e39b1936586d64f9ea3139
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651377"
---
# <a name="access-and-manage-ibm-db2-resources-by-using-azure-logic-apps"></a>Přístup k prostředkům IBM DB2 a správa pomocí aplikací Azure Logic Apps

Pomocí [aplikací Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [konektoru IBM DB2](/connectors/db2/)můžete vytvářet automatizované úlohy a pracovní postupy na základě prostředků uložených v databázi DB2. Pracovní postupy se mohou připojit k prostředkům v databázi, číst a vypsat databázové tabulky, přidávat řádky, měnit řádky, odstraňovat řádky a další. Můžete zahrnout akce ve vašich aplikacích logiky, které získat odpovědi z databáze a zpřístupnit výstup pro jiné akce.

Tento článek ukazuje, jak můžete vytvořit aplikaci logiky, která provádí různé databázové operace. Pokud s aplikacemi logiky tečujete, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="supported-platforms-and-versions"></a>Podporované platformy a verze

Konektor DB2 obsahuje klienta společnosti Microsoft, který komunikuje se vzdálenými servery DB2 v síti TCP/IP. Tento konektor můžete použít pro přístup ke cloudovým databázím, jako je IBM DB2 for Windows spuštěné v virtualizaci Azure. Po instalaci a nastavení místní brány dat můžete také přistupovat k místním [databázím DB2](../logic-apps/logic-apps-gateway-connection.md).

Konektor IBM DB2 podporuje tyto platformy a verze IBM DB2 spolu s produkty kompatibilními s IBM DB2, které podporují sql access manager (DRDA) distribuované relační databáze (DRDA) verze 10 a 11:

| Platforma | Version | 
|----------|---------|
| IBM DB2 pro z/OS | 11.1, 10.1 |
| IBM DB2 pro i | 7.3, 7.2, 7.1 |
| IBM DB2 pro LUW | 11, 10.5 |
|||

## <a name="supported-database-operations"></a>Podporované databázové operace

Konektor IBM DB2 podporuje tyto databázové operace, které jsou mapována na odpovídající akce v konektoru:

| Operace databáze | Akce spojnice |
|--------------------|------------------|
| Seznam databázových tabulek | Získat tabulky |
| Čtení jednoho řádku pomocí funkce SELECT | Získat řádek |
| Čtení všech řádků pomocí SELECT | Získat řádky |
| Přidání jednoho řádku pomocí funkce INSERT | Vložení řádku |
| Úprava jednoho řádku pomocí funkce UPDATE | Aktualizovat řádek |
| Odebrání jednoho řádku pomocí funkce DELETE | Odstranit řádek |
|||

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Databáze IBM DB2, založená na cloudu nebo místně

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, kde chcete získat přístup k databázi DB2. Tento konektor poskytuje pouze akce, takže ke spuštění aplikace logiky vyberte samostatnou aktivační událost, například aktivační událost **opakování.**
Příklady v tomto článku používají aktivační událost **opakování.**

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>Přidat akci DB2 – získat tabulky

1. Na [webu Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v Návrháři aplikací logiky, pokud už není otevřená.

1. Pod aktivační událostí zvolte **Nový krok**.

1. Do vyhledávacího pole zadejte jako filtr "db2". V tomto příkladu vyberte v seznamu akcí tuto akci: **Získat tabulky (náhled)**

   ![Vybrat akci](./media/connectors-create-api-db2/select-db2-action.png)

   Nyní se zobrazí výzva k zadání podrobností o připojení pro databázi DB2.

1. Postupujte podle pokynů pro vytváření připojení pro [cloudové databáze](#cloud-connection) nebo [místní databáze](#on-premises-connection).

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>Připojení ke cloudu DB2

Chcete-li nastavit připojení, zadejte tyto podrobnosti o připojení po zobrazení výzvy, zvolte **Vytvořit**a uložte aplikaci logiky:

| Vlastnost | Požaduje se | Popis |
|----------|----------|-------------|
| **Připojení přes místní bránu** | Ne | Platí pouze pro místní připojení. |
| **Název připojení** | Ano | Název vašeho připojení, například "MyLogicApp-DB2-connection" |
| **Server** | Ano | Číslo portu dvojtečky adresy nebo aliasu serveru DB2, například "myDB2server.cloudapp.net:50000" <p><p>**Poznámka:** Tato hodnota je řetězec, který představuje adresu nebo alias Protokolu TCP/IP, a to buď ve formátu IPv4 nebo IPv6, následovaný dvojtečkou a číslem portu TCP/IP. |
| **Databáze** | Ano | Název databáze <p><p>**Poznámka:** Tato hodnota je řetězec, který představuje název relační databáze DRDA (RDBNAM): <p>- DB2 pro z/OS přijímá řetězec 16 bajtů, kde je databáze označována jako umístění "IBM DB2 for z/OS". <br>- DB2 for i přijímá 18bajtova řetězec, kde je databáze známá jako relační databáze "IBM DB2 for i". <br>- DB2 pro LUW přijímá řetězec 8 bajtů. |
| **Username** | Ano | Vaše uživatelské jméno pro databázi <p><p>**Poznámka**: Tato hodnota je řetězec, jehož délka je založena na konkrétní databázi: <p><p>- DB2 pro z/OS přijímá řetězec 8 bajtů. <br>- DB2 pro i přijímá řetězec 10 bajtů. <br>- DB2 pro Linux nebo UNIX přijímá řetězec 8 bajtů. <br>- DB2 pro Windows přijímá řetězec 30 bajtů. |
| **Heslo** | Ano | Vaše heslo pro databázi |
||||

Například:

![Podrobnosti o připojení pro cloudové databáze](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>Připojení k místnímu DB2

Před vytvořením připojení už musíte mít nainstalovanou místní bránu dat. V opačném případě nelze dokončit nastavení připojení. Pokud máte instalaci brány, pokračujte v poskytování těchto podrobností o připojení a pak zvolte **Vytvořit**.

| Vlastnost | Požaduje se | Popis |
|----------|----------|-------------|
| **Připojení přes místní bránu** | Ano | Platí, pokud chcete místní připojení a zobrazí vlastnosti místního připojení. |
| **Název připojení** | Ano | Název vašeho připojení, například "MyLogicApp-DB2-connection" | 
| **Server** | Ano | Číslo portu dvojtečky adresy nebo aliasu serveru DB2, například myDB2server:50000 <p><p>**Poznámka:** Tato hodnota je řetězec, který představuje adresu nebo alias Protokolu TCP/IP, a to buď ve formátu IPv4 nebo IPv6, následovaný dvojtečkou a číslem portu TCP/IP. |
| **Databáze** | Ano | Název databáze <p><p>**Poznámka:** Tato hodnota je řetězec, který představuje název relační databáze DRDA (RDBNAM): <p>- DB2 pro z/OS přijímá řetězec 16 bajtů, kde je databáze označována jako umístění "IBM DB2 for z/OS". <br>- DB2 for i přijímá 18bajtova řetězec, kde je databáze známá jako relační databáze "IBM DB2 for i". <br>- DB2 pro LUW přijímá řetězec 8 bajtů. |
| **Ověřování** | Ano | Typ ověřování pro připojení, například "Základní" <p><p>**Poznámka:** Tuto hodnotu vyberte ze seznamu, který zahrnuje základní nebo systém Windows (Kerberos). |
| **Username** | Ano | Vaše uživatelské jméno pro databázi <p><p>**Poznámka**: Tato hodnota je řetězec, jehož délka je založena na konkrétní databázi: <p><p>- DB2 pro z/OS přijímá řetězec 8 bajtů. <br>- DB2 pro i přijímá řetězec 10 bajtů. <br>- DB2 pro Linux nebo UNIX přijímá řetězec 8 bajtů. <br>- DB2 pro Windows přijímá řetězec 30 bajtů. |
| **Heslo** | Ano | Vaše heslo pro databázi |
| **Brána** | Ano | Název nainstalované místní brány dat <p><p>**Poznámka:** Tuto hodnotu vyberte ze seznamu, který zahrnuje všechny nainstalované brány dat v rámci vašeho předplatného Azure a skupiny prostředků. |
||||

Například:

![Podrobnosti o připojení pro místní databáze](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>Zobrazení výstupních tabulek

Chcete-li aplikaci logiky spustit ručně, na panelu nástrojů návrháře zvolte **Spustit**. Po dokončení spuštění aplikace logiky můžete zobrazit výstup z běhu.

1. V nabídce aplikace logiky vyberte **Přehled**.

1. V části **Souhrn**vyberte v části **Historie spuštění** poslední spuštění, což je první položka v seznamu.

   ![Zobrazení historie spuštění](./media/connectors-create-api-db2/run-history.png)

1. V části **Spuštění aplikace logiky**teď můžete zkontrolovat stav, vstupy a výstupy pro každý krok v aplikaci logiky.
Rozbalte akci **Získat tabulky.**

   ![Rozbalit akci](./media/connectors-create-api-db2/expand-action-step.png)

1. Chcete-li zobrazit vstupy, zvolte **Zobrazit nezpracované vstupy**.

1. Chcete-li zobrazit výstupy, zvolte **Zobrazit nezpracované výstupy**.

   Výstupy obsahují seznam tabulek.

   ![Zobrazení výstupních tabulek](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>Získat řádek

Chcete-li načíst jeden záznam v databázové tabulce DB2, použijte akci **Získat řádek** v aplikaci logiky. Tato akce spustí `SELECT WHERE` příkaz DB2, `SELECT FROM AREA WHERE AREAID = '99999'`například .

1. Pokud jste nikdy nepoužívali db2 akce před ve vaší aplikaci logiky, zkontrolujte kroky v [přidat DB2 akce – získat tabulky](#add-db2-action) části, ale místo toho přidejte akce získat **řádek** a pak se sem vrátit pokračovat.

   Po přidání akce **Získat řádek,** tady je, jak se zobrazí ukázková aplikace logiky:

   ![Získat akci řádku](./media/connectors-create-api-db2/db2-get-row-action.png)

1. Zadejte hodnoty pro všechny požadované vlastnosti (*). Po výběru tabulky se v akci zobrazí příslušné vlastnosti, které jsou specifické pro záznamy v této tabulce.

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název tabulky** | Ano | Tabulka, která obsahuje požadovaný záznam, například "AREA" v tomto příkladu |
   | **ID oblasti** | Ano | ID pro záznam, který chcete, například "99999" v tomto příkladu |
   ||||

   ![Vybrat tabulku](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. Až budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**.

### <a name="view-output-row"></a>Zobrazit výstupní řádek

Chcete-li aplikaci logiky spustit ručně, na panelu nástrojů návrháře zvolte **Spustit**. Po dokončení spuštění aplikace logiky můžete zobrazit výstup z běhu.

1. V nabídce aplikace logiky vyberte **Přehled**.

1. V části **Souhrn**vyberte v části **Historie spuštění** poslední spuštění, což je první položka v seznamu.

1. V části **Spuštění aplikace logiky**teď můžete zkontrolovat stav, vstupy a výstupy pro každý krok v aplikaci logiky.
Rozbalte akci **Získat řádek.**

1. Chcete-li zobrazit vstupy, zvolte **Zobrazit nezpracované vstupy**.

1. Chcete-li zobrazit výstupy, zvolte **Zobrazit nezpracované výstupy**.

   Výstupy obsahují zadaný řádek.

   ![Zobrazit výstupní řádek](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>Získat řádky

Chcete-li načíst všechny záznamy v databázové tabulce DB2, použijte akci **Získat řádky** v aplikaci logiky. Tato akce spustí `SELECT` příkaz DB2, `SELECT * FROM AREA`například .

1. Pokud jste nikdy nepoužívali db2 akce před ve vaší aplikaci logiky, zkontrolujte kroky v [přidat DB2 akce – získat tabulky](#add-db2-action) části, ale místo toho přidejte akce Získat **řádky** a pak se sem vrátit a pokračovat.

   Po přidání akce **Získat řádky,** tady je, jak se zobrazí ukázková aplikace logiky:

   ![Akce Získat řádky](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. Otevřete seznam **názvů tabulek** a vyberte požadovanou tabulku, která je v tomto příkladu "OBLAST":

   ![Vybrat tabulku](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. Chcete-li zadat filtr nebo dotaz na výsledky, zvolte **Zobrazit upřesňující možnosti**.

1. Až budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**.

### <a name="view-output-rows"></a>Zobrazit výstupní řádky

Chcete-li aplikaci logiky spustit ručně, na panelu nástrojů návrháře zvolte **Spustit**. Po dokončení spuštění aplikace logiky můžete zobrazit výstup z běhu.

1. V nabídce aplikace logiky vyberte **Přehled**.

1. V části **Souhrn**vyberte v části **Historie spuštění** poslední spuštění, což je první položka v seznamu.

1. V části **Spuštění aplikace logiky**teď můžete zkontrolovat stav, vstupy a výstupy pro každý krok v aplikaci logiky.
Rozbalte akci **Získat řádky.**

1. Chcete-li zobrazit vstupy, zvolte **Zobrazit nezpracované vstupy**.

1. Chcete-li zobrazit výstupy, zvolte **Zobrazit nezpracované výstupy**.

   Výstupy zahrnují všechny záznamy v zadané tabulce.

   ![Zobrazit výstupní řádky](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>Vložení řádku

Chcete-li přidat jeden záznam do tabulky databáze DB2, použijte akci **Vložit řádek** v aplikaci logiky. Tato akce spustí `INSERT` příkaz DB2, `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`například .

1. Pokud jste nikdy nepoužívali db2 akce před ve vaší aplikaci logiky, zkontrolujte kroky v [přidat DB2 akce – získat tabulky](#add-db2-action) části, ale místo toho přidejte akci Vložit **řádek** a pak se sem vrátit pokračovat.

   Po přidání akce **Vložit řádek,** tady je, jak se zobrazí ukázková aplikace logiky:

   ![Akce Vložit řádek](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. Zadejte hodnoty pro všechny požadované vlastnosti (*). Po výběru tabulky se v akci zobrazí příslušné vlastnosti, které jsou specifické pro záznamy v této tabulce.

   V tomto příkladu jsou zde vlastnosti:

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název tabulky** | Ano | Tabulka, do které chcete záznam přidat, například "AREA" |
   | **ID oblasti** | Ano | ID pro oblast přidat, například "99999" |
   | **Popis oblasti** | Ano | Popis oblasti, kterou chcete přidat, například "Oblast 99999" |
   | **ID oblasti** | Ano | ID pro oblast přidat, například "102" |
   |||| 

   Například:

   ![Vybrat tabulku](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. Až budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**.

### <a name="view-insert-row-outputs"></a>Zobrazení výstupů řádků vložení

Chcete-li aplikaci logiky spustit ručně, na panelu nástrojů návrháře zvolte **Spustit**. Po dokončení spuštění aplikace logiky můžete zobrazit výstup z běhu.

1. V nabídce aplikace logiky vyberte **Přehled**.

1. V části **Souhrn**vyberte v části **Historie spuštění** poslední spuštění, což je první položka v seznamu.

1. V části **Spuštění aplikace logiky**teď můžete zkontrolovat stav, vstupy a výstupy pro každý krok v aplikaci logiky.
Rozbalte akci **Vložit řádek.**

1. Chcete-li zobrazit vstupy, zvolte **Zobrazit nezpracované vstupy**.

1. Chcete-li zobrazit výstupy, zvolte **Zobrazit nezpracované výstupy**.

   Výstupy zahrnují záznam, který jste přidali do zadané tabulky.

   ![Zobrazit výstup s vloženým řádkem](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>Aktualizovat řádek

Chcete-li aktualizovat jeden záznam v databázové tabulce DB2, použijte akci **Aktualizovat řádek** v aplikaci logiky. Tato akce spustí `UPDATE` příkaz DB2, `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)`například .

1. Pokud jste nikdy nepoužívali db2 akce před ve vaší aplikaci logiky, zkontrolujte kroky v [přidat DB2 akce – získat tabulky](#add-db2-action) části, ale místo toho přidejte akci řádek **aktualizace** a pak se sem vrátit pokračovat.

   Po přidání akce **Aktualizovat řádek,** zde je, jak se zobrazí ukázková aplikace logiky:

   ![Aktualizovat akci řádku](./media/connectors-create-api-db2/db2-update-row-action.png)

1. Zadejte hodnoty pro všechny požadované vlastnosti (*). Po výběru tabulky se v akci zobrazí příslušné vlastnosti, které jsou specifické pro záznamy v této tabulce.

   V tomto příkladu jsou zde vlastnosti:

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název tabulky** | Ano | Tabulka, kde chcete aktualizovat záznam, například "AREA" |
   | **ID řádku** | Ano | ID pro záznam aktualizovat, například "99999" |
   | **ID oblasti** | Ano | Nové ID oblasti, například "99999" |
   | **Popis oblasti** | Ano | Nový popis oblasti, například "Aktualizováno 99999" |
   | **ID oblasti** | Ano | Nové ID oblasti, například "102" |
   ||||

   Například:

   ![Vybrat tabulku](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. Až budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**.

### <a name="view-update-row-outputs"></a>Zobrazit výstupy řádků aktualizace

Chcete-li aplikaci logiky spustit ručně, na panelu nástrojů návrháře zvolte **Spustit**. Po dokončení spuštění aplikace logiky můžete zobrazit výstup z běhu.

1. V nabídce aplikace logiky vyberte **Přehled**.

1. V části **Souhrn**vyberte v části **Historie spuštění** poslední spuštění, což je první položka v seznamu.

1. V části **Spuštění aplikace logiky**teď můžete zkontrolovat stav, vstupy a výstupy pro každý krok v aplikaci logiky.
Rozbalte akci **Aktualizovat řádek.**

1. Chcete-li zobrazit vstupy, zvolte **Zobrazit nezpracované vstupy**.

1. Chcete-li zobrazit výstupy, zvolte **Zobrazit nezpracované výstupy**.

   Výstupy zahrnují záznam, který jste aktualizovali v zadané tabulce.

   ![Zobrazit výstup s aktualizovaným řádkem](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>Odstranit řádek

Chcete-li odstranit jeden záznam z databáze DATABÁZE DB2, použijte akci **Odstranit řádek** v aplikaci logiky. Tato akce spustí `DELETE` příkaz DB2, `DELETE FROM AREA WHERE AREAID = '99999'`například .

1. Pokud jste nikdy nepoužívali db2 akce před ve vaší aplikaci logiky, zkontrolujte kroky v [přidat DB2 akce – získat tabulky](#add-db2-action) části, ale místo toho přidejte odstranit **řádek** akce a pak se sem vrátit pokračovat.

   Po přidání akce **Odstranit řádek,** zde je, jak se zobrazí ukázková aplikace logiky:

   ![Odstranit akci řádku](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. Zadejte hodnoty pro všechny požadované vlastnosti (*). Po výběru tabulky se v akci zobrazí příslušné vlastnosti, které jsou specifické pro záznamy v této tabulce.

   V tomto příkladu jsou zde vlastnosti:

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název tabulky** | Ano | Tabulka, kde chcete záznam odstranit, například "AREA" |
   | **ID řádku** | Ano | ID záznamu odstranit, například "99999" |
   ||||

   Například:

   ![Vybrat tabulku](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. Až budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**.

### <a name="view-delete-row-outputs"></a>Zobrazit výstupy odstranit řádky

Chcete-li aplikaci logiky spustit ručně, na panelu nástrojů návrháře zvolte **Spustit**. Po dokončení spuštění aplikace logiky můžete zobrazit výstup z běhu.

1. V nabídce aplikace logiky vyberte **Přehled**.

1. V části **Souhrn**vyberte v části **Historie spuštění** poslední spuštění, což je první položka v seznamu.

1. V části **Spuštění aplikace logiky**teď můžete zkontrolovat stav, vstupy a výstupy pro každý krok v aplikaci logiky.
Rozbalte akci **Odstranit řádek.**

1. Chcete-li zobrazit vstupy, zvolte **Zobrazit nezpracované vstupy**.

1. Chcete-li zobrazit výstupy, zvolte **Zobrazit nezpracované výstupy**.

   Výstupy již neobsahují záznam, který jste odstranili ze zadané tabulky.

   ![Zobrazit výstup bez odstraněného řádku](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o tomto konektoru, jako jsou aktivační události, akce a omezení, jak je popsáno v souboru Swagger konektoru, naleznete [na referenční stránce konektoru](https://docs.microsoft.com/connectors/db2/).

> [!NOTE]
> Pro aplikace logiky v [prostředí služby integrace (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), tento konektor je ise označené verze používá [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) místo.

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)
