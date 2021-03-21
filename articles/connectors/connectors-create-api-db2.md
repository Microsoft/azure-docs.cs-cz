---
title: Přístup k prostředkům IBM DB2 a jejich správa
description: Vytváření automatizovaných pracovních postupů pomocí Azure Logic Apps pro čtení, úpravy, aktualizaci a správu prostředků IBM DB2
services: logic-apps
ms.suite: integration
ms.reviewer: plarsen, logicappspm
ms.topic: conceptual
ms.date: 11/19/2020
tags: connectors
ms.openlocfilehash: 765bb66b572f0c046222cfb617fe4caa80925256
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94967397"
---
# <a name="access-and-manage-ibm-db2-resources-by-using-azure-logic-apps"></a>Přístup k prostředkům IBM DB2 a jejich správa pomocí Azure Logic Apps

Pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [konektoru IBM DB2](/connectors/db2/)můžete vytvářet automatizované úlohy a pracovní postupy na základě prostředků uložených v databázi DB2. Vaše pracovní postupy se můžou připojit k prostředkům ve vaší databázi, číst a vypisovat tabulky databáze, přidávat řádky, měnit řádky, odstraňovat řádky a další. Do svých aplikací logiky můžete zahrnout akce, které získají odpovědi z vaší databáze a zpřístupňují výstup pro jiné akce.

Tento článek ukazuje, jak můžete vytvořit aplikaci logiky, která provádí různé databázové operace. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="supported-platforms-and-versions"></a>Podporované platformy a verze

Konektor DB2 zahrnuje klienta Microsoftu, který komunikuje se vzdálenými servery DB2 napříč sítí TCP/IP. Tento konektor můžete použít pro přístup k cloudovým databázím, jako je IBM DB2 pro Windows běžící v virtualizaci Azure. Po [instalaci a nastavení místní brány dat](../logic-apps/logic-apps-gateway-connection.md)můžete k místním databázím DB2 přistupovat také.

Konektor IBM DB2 podporuje tyto platformy a verze IBM DB2 spolu s produkty kompatibilními s IBM DB2, které podporují DRDA (Distributed relační Database Architecture) verze 10 a 11:

| Platforma | Verze | 
|----------|---------|
| IBM DB2 pro z/OS | 12.11,1.10,1 |
| IBM DB2 pro i | 7,3, 7,2, 7,1 |
| IBM DB2 pro LUW | 11.10,5 |
|||

## <a name="supported-database-operations"></a>Podporované databázové operace

Konektor IBM DB2 podporuje tyto databázové operace, které se mapují na odpovídající akce v konektoru:

| Databázová operace | Akce konektoru |
|--------------------|------------------|
| Výpis tabulek databáze | Získat tabulky |
| Čtení jednoho řádku pomocí SELECT | Získat řádek |
| Čtení všech řádků pomocí SELECT | Získat řádky |
| Přidání jednoho řádku pomocí vložení | Vložení řádku |
| Úprava jednoho řádku pomocí aktualizace | Aktualizovat řádek |
| Odebrání jednoho řádku pomocí odstranění | Odstranit řádek |
|||

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Databáze IBM DB2 založená na cloudu nebo v místním prostředí

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ke které chcete získat přístup k databázi DB2. Tento konektor nabízí pouze akce, takže můžete spustit aplikaci logiky, vybrat samostatnou aktivační událost, například Trigger **opakování** .
Příklady v tomto článku používají Trigger **opakování** .

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>Přidat akci DB2 – získat tabulky

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky, pokud ještě není otevřená.

1. V části Trigger vyberte **Nový krok**.

1. Do vyhledávacího pole zadejte jako filtr "DB2". V tomto příkladu vyberte v seznamu akce tuto akci: **získat tabulky (Preview)** .

   ![Vybrat akci](./media/connectors-create-api-db2/select-db2-action.png)

   Nyní budete vyzváni k zadání podrobností o připojení pro databázi DB2.

1. Postupujte podle kroků pro vytváření připojení pro [cloudové databáze](#cloud-connection) nebo [místní databáze](#on-premises-connection).

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>Připojení ke cloudu DB2

Pokud chcete nastavit připojení, zadejte tyto podrobnosti o připojení po zobrazení výzvy, zvolte **vytvořit** a pak aplikaci logiky uložte:

| Vlastnost | Povinné | Popis |
|----------|----------|-------------|
| **Připojit přes místní bránu** | No | Platí jenom pro místní připojení. |
| **Název připojení** | Yes | Název připojení, například "MyLogicApp-DB2-připojení" |
| **Server** | Yes | Číslo portu nebo dvojtečku, které má adresa nebo alias pro server DB2, například "myDB2server.cloudapp.net:50000". <p><p>**Poznámka**: Tato hodnota je řetězec, který představuje adresu TCP/IP nebo alias, buď ve formátu IPv4 nebo IPv6 následovaný dvojtečkou a číslem portu TCP/IP. |
| **Databáze** | Yes | Název databáze <p><p>**Poznámka**: Tato hodnota je řetězec, který představuje název RELAČNÍ databáze DRDA (RDBNAM): <p>– DB2 pro z/OS akceptuje 16bajtový řetězec, ve kterém je databáze známá jako umístění IBM DB2 pro z/OS. <br>– DB2 pro Přijímám 18 bajtů, kde je databáze známá jako relační databáze IBM DB2 for i. <br>– DB2 pro LUW akceptuje řetězec o velikosti 8 bajtů. |
| **Uživatelské jméno** | Yes | Vaše uživatelské jméno pro databázi <p><p>**Poznámka**: Tato hodnota je řetězec, jehož délka je založena na konkrétní databázi: <p><p>– DB2 pro z/OS akceptuje řetězec o velikosti 8 bajtů. <br>– DB2 pro Přijímám řetězec 10 bajtů. <br>– DB2 pro Linux nebo UNIX akceptuje řetězec o velikosti 8 bajtů. <br>– DB2 pro Windows přijímá 30 bajtů řetězce. |
| **Heslo** | Yes | Heslo pro databázi |
||||

Například:

![Podrobnosti o připojení cloudových databází](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>Připojení k místnímu DB2

Před vytvořením připojení musíte mít už nainstalovanou místní bránu dat. Jinak nemůžete dokončit nastavování připojení. Pokud máte instalaci brány, pokračujte zadáním těchto podrobností o připojení a pak zvolte **vytvořit**.

| Vlastnost | Povinné | Popis |
|----------|----------|-------------|
| **Připojit přes místní bránu** | Yes | Použije se, pokud chcete místní připojení a zobrazuje vlastnosti místního připojení. |
| **Název připojení** | Yes | Název připojení, například "MyLogicApp-DB2-připojení" | 
| **Server** | Yes | Číslo portu nebo dvojtečku, které má adresa nebo alias pro server DB2, například "myDB2server: 50000". <p><p>**Poznámka**: Tato hodnota je řetězec, který představuje adresu TCP/IP nebo alias, buď ve formátu IPv4 nebo IPv6 následovaný dvojtečkou a číslem portu TCP/IP. |
| **Databáze** | Yes | Název databáze <p><p>**Poznámka**: Tato hodnota je řetězec, který představuje název RELAČNÍ databáze DRDA (RDBNAM): <p>– DB2 pro z/OS akceptuje 16bajtový řetězec, ve kterém je databáze známá jako umístění IBM DB2 pro z/OS. <br>– DB2 pro Přijímám 18 bajtů, kde je databáze známá jako relační databáze IBM DB2 for i. <br>– DB2 pro LUW akceptuje řetězec o velikosti 8 bajtů. |
| **Authentication** | Yes | Typ ověřování pro připojení, například "základní" <p><p>**Poznámka**: v seznamu vyberte tuto hodnotu, která zahrnuje základní nebo Windows (Kerberos). |
| **Uživatelské jméno** | Yes | Vaše uživatelské jméno pro databázi <p><p>**Poznámka**: Tato hodnota je řetězec, jehož délka je založena na konkrétní databázi: <p><p>– DB2 pro z/OS akceptuje řetězec o velikosti 8 bajtů. <br>– DB2 pro Přijímám řetězec 10 bajtů. <br>– DB2 pro Linux nebo UNIX akceptuje řetězec o velikosti 8 bajtů. <br>– DB2 pro Windows přijímá 30 bajtů řetězce. |
| **Heslo** | Yes | Heslo pro databázi |
| **brána** | Yes | Název nainstalované místní brány dat <p><p>**Poznámka**: v seznamu vyberte tuto hodnotu, která zahrnuje všechny nainstalované brány dat v rámci předplatného Azure a skupiny prostředků. |
||||

Například:

![Podrobnosti o připojení pro místní databáze](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>Zobrazit výstupní tabulky

Pokud chcete aplikaci logiky spustit ručně, na panelu nástrojů návrháře klikněte na **Spustit**. Po dokončení běhu aplikace logiky můžete zobrazit výstup z běhu.

1. V nabídce aplikace logiky vyberte **Přehled**.

1. V části **Souhrn** v části **historie spuštění** vyberte poslední spuštění, což je první položka v seznamu.

   ![Zobrazení historie spuštění](./media/connectors-create-api-db2/run-history.png)

1. V části **spuštění aplikace logiky** teď můžete zkontrolovat stav, vstupy a výstupy pro každý krok v aplikaci logiky.
Rozbalte akci **získat tabulky** .

   ![Rozbalit akci](./media/connectors-create-api-db2/expand-action-step.png)

1. Chcete-li zobrazit vstupy, vyberte možnost **Zobrazit nezpracované vstupy**.

1. Chcete-li zobrazit výstupy, vyberte možnost **Zobrazit nezpracované výstupy**.

   Výstupy obsahují seznam tabulek.

   ![Zobrazit výstupní tabulky](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>Získat řádek

K načtení jednoho záznamu v databázové tabulce DB2 použijte akci **získat řádek** ve vaší aplikaci logiky. Tato akce spustí příkaz DB2 `SELECT WHERE` , například `SELECT FROM AREA WHERE AREAID = '99999'` .

1. Pokud jste ještě nikdy nepoužili akce DB2 před ve vaší aplikaci logiky, přečtěte si postup v části [Přidání akce DB2 – získat tabulky](#add-db2-action) , ale místo toho přidejte akci **získat řádek** a vraťte se sem, abyste mohli pokračovat.

   Když přidáte akci **získat řádek** , tady je ukázka, jak se zobrazuje vaše ukázková aplikace logiky:

   ![Získat akci řádku](./media/connectors-create-api-db2/db2-get-row-action.png)

1. Zadejte hodnoty pro všechny požadované vlastnosti (*). Po výběru tabulky akce zobrazí příslušné vlastnosti, které jsou specifické pro záznamy v této tabulce.

   | Vlastnost | Povinné | Popis |
   |----------|----------|-------------|
   | **Název tabulky** | Yes | Tabulka s požadovaným záznamem, například "oblast" v tomto příkladu |
   | **ID oblasti** | Yes | ID požadovaného záznamu, například "99999" v tomto příkladu |
   ||||

   ![Snímek obrazovky zobrazující akci získat řádek (Preview) s otevřeným seznamem název tabulky a vybranou hodnotou oblast](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. Až budete hotovi, na panelu nástrojů návrháře klikněte na **Uložit**.

### <a name="view-output-row"></a>Zobrazit výstupní řádek

Pokud chcete aplikaci logiky spustit ručně, na panelu nástrojů návrháře klikněte na **Spustit**. Po dokončení běhu aplikace logiky můžete zobrazit výstup z běhu.

1. V nabídce aplikace logiky vyberte **Přehled**.

1. V části **Souhrn** v části **historie spuštění** vyberte poslední spuštění, což je první položka v seznamu.

1. V části **spuštění aplikace logiky** teď můžete zkontrolovat stav, vstupy a výstupy pro každý krok v aplikaci logiky.
Rozbalte akci **získat řádek** .

1. Chcete-li zobrazit vstupy, vyberte možnost **Zobrazit nezpracované vstupy**.

1. Chcete-li zobrazit výstupy, vyberte možnost **Zobrazit nezpracované výstupy**.

   Výstupy obsahují zadaný řádek.

   ![Zobrazit výstupní řádek](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>Získat řádky

K načtení všech záznamů v databázové tabulce DB2 použijte akci **získat řádky** ve vaší aplikaci logiky. Tato akce spustí příkaz DB2 `SELECT` , například `SELECT * FROM AREA` .

1. Pokud jste ještě nikdy nepoužili akce DB2 před ve vaší aplikaci logiky, přečtěte si postup v části [Přidání akce DB2 – získat tabulky](#add-db2-action) , ale místo toho přidejte akci **získat řádky** a vraťte se sem, abyste mohli pokračovat.

   Po přidání akce **získat řádky** tady vidíte, jak se zobrazuje vaše ukázková aplikace logiky:

   ![Akce získání řádků](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. Otevřete seznam **název tabulky** a poté vyberte požadovanou tabulku, což je "oblast" v tomto příkladu:

   ![Snímek obrazovky, který zobrazuje akci "načíst řádek (Preview)" s hodnotou "oblast" vybranou v seznamu název tabulky.](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. Chcete-li zadat filtr nebo dotaz pro výsledky, zvolte možnost **Zobrazit upřesňující možnosti**.

1. Až budete hotovi, na panelu nástrojů návrháře klikněte na **Uložit**.

### <a name="view-output-rows"></a>Zobrazit výstupní řádky

Pokud chcete aplikaci logiky spustit ručně, na panelu nástrojů návrháře klikněte na **Spustit**. Po dokončení běhu aplikace logiky můžete zobrazit výstup z běhu.

1. V nabídce aplikace logiky vyberte **Přehled**.

1. V části **Souhrn** v části **historie spuštění** vyberte poslední spuštění, což je první položka v seznamu.

1. V části **spuštění aplikace logiky** teď můžete zkontrolovat stav, vstupy a výstupy pro každý krok v aplikaci logiky.
Rozbalte akci **získat řádky** .

1. Chcete-li zobrazit vstupy, vyberte možnost **Zobrazit nezpracované vstupy**.

1. Chcete-li zobrazit výstupy, vyberte možnost **Zobrazit nezpracované výstupy**.

   Výstupy obsahují všechny záznamy v zadané tabulce.

   ![Zobrazit výstupní řádky](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>Vložení řádku

K přidání jednoho záznamu do tabulky databáze DB2 použijte akci **Vložit řádek** do aplikace logiky. Tato akce spustí příkaz DB2 `INSERT` , například `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)` .

1. Pokud jste ještě nikdy nepoužili akce DB2 před v aplikaci logiky, přečtěte si postup v části [Přidání akce DB2 – získat tabulky](#add-db2-action) , ale místo toho přidejte akci **Vložit řádek** a pak se vraťte sem, abyste mohli pokračovat.

   Když přidáte akci **Vložit řádek** , tady je ukázka, jak se zobrazuje vaše ukázková aplikace logiky:

   ![Vložit akci řádku](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. Zadejte hodnoty pro všechny požadované vlastnosti (*). Po výběru tabulky akce zobrazí příslušné vlastnosti, které jsou specifické pro záznamy v této tabulce.

   V tomto příkladu jsou zde vlastnosti:

   | Vlastnost | Povinné | Popis |
   |----------|----------|-------------|
   | **Název tabulky** | Yes | Tabulka, do které se má záznam přidat, například "oblast" |
   | **ID oblasti** | Yes | ID oblasti, která se má přidat, například "99999" |
   | **Popis oblasti** | Yes | Popis oblasti, která se má přidat, například "oblast 99999" |
   | **ID oblasti** | Yes | ID oblasti, která se má přidat, například "102" |
   |||| 

   Například:

   ![Snímek obrazovky, který zobrazuje návrháře Logic Apps s akcí vložit řádek (Preview) a příklady hodnot vlastností](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. Až budete hotovi, na panelu nástrojů návrháře klikněte na **Uložit**.

### <a name="view-insert-row-outputs"></a>Zobrazit vložené výstupy řádků

Pokud chcete aplikaci logiky spustit ručně, na panelu nástrojů návrháře klikněte na **Spustit**. Po dokončení běhu aplikace logiky můžete zobrazit výstup z běhu.

1. V nabídce aplikace logiky vyberte **Přehled**.

1. V části **Souhrn** v části **historie spuštění** vyberte poslední spuštění, což je první položka v seznamu.

1. V části **spuštění aplikace logiky** teď můžete zkontrolovat stav, vstupy a výstupy pro každý krok v aplikaci logiky.
Rozbalte akci **Vložit řádek** .

1. Chcete-li zobrazit vstupy, vyberte možnost **Zobrazit nezpracované vstupy**.

1. Chcete-li zobrazit výstupy, vyberte možnost **Zobrazit nezpracované výstupy**.

   Výstupy obsahují záznam, který jste přidali do zadané tabulky.

   ![Zobrazit výstup pomocí vloženého řádku](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>Aktualizovat řádek

Pokud chcete aktualizovat jeden záznam v databázové tabulce DB2, použijte akci **aktualizovat řádek** ve vaší aplikaci logiky. Tato akce spustí příkaz DB2 `UPDATE` , například `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)` .

1. Pokud jste ještě nikdy nepoužili akce DB2 před ve vaší aplikaci logiky, přečtěte si postup v části [Přidání akce DB2 – získat tabulky](#add-db2-action) , ale místo toho přidejte akci **aktualizovat řádek** a vraťte se sem, abyste mohli pokračovat.

   Po přidání akce **řádek aktualizace** tady vidíte, jak se zobrazuje vaše ukázková aplikace logiky:

   ![Aktualizovat řádek akce](./media/connectors-create-api-db2/db2-update-row-action.png)

1. Zadejte hodnoty pro všechny požadované vlastnosti (*). Po výběru tabulky akce zobrazí příslušné vlastnosti, které jsou specifické pro záznamy v této tabulce.

   V tomto příkladu jsou zde vlastnosti:

   | Vlastnost | Povinné | Popis |
   |----------|----------|-------------|
   | **Název tabulky** | Yes | Tabulka, ve které se má záznam aktualizovat, například "oblast" |
   | **ID řádku** | Yes | ID záznamu, který se má aktualizovat, například "99999" |
   | **ID oblasti** | Yes | Nové ID oblasti, například "99999" |
   | **Popis oblasti** | Yes | Popis nové oblasti, například "aktualizované 99999" |
   | **ID oblasti** | Yes | Nové ID oblasti, například "102" |
   ||||

   Například:

   ![Snímek obrazovky, který zobrazuje návrháře Logic Apps s akcí aktualizovat řádek (Preview), kde vyberete tabulku](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. Až budete hotovi, na panelu nástrojů návrháře klikněte na **Uložit**.

### <a name="view-update-row-outputs"></a>Zobrazit aktualizované výstupy řádků

Pokud chcete aplikaci logiky spustit ručně, na panelu nástrojů návrháře klikněte na **Spustit**. Po dokončení běhu aplikace logiky můžete zobrazit výstup z běhu.

1. V nabídce aplikace logiky vyberte **Přehled**.

1. V části **Souhrn** v části **historie spuštění** vyberte poslední spuštění, což je první položka v seznamu.

1. V části **spuštění aplikace logiky** teď můžete zkontrolovat stav, vstupy a výstupy pro každý krok v aplikaci logiky.
Rozbalte akci **aktualizovat řádek** .

1. Chcete-li zobrazit vstupy, vyberte možnost **Zobrazit nezpracované vstupy**.

1. Chcete-li zobrazit výstupy, vyberte možnost **Zobrazit nezpracované výstupy**.

   Výstupy obsahují záznam, který jste aktualizovali v zadané tabulce.

   ![Zobrazit výstup s aktualizovaným řádkem](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>Odstranit řádek

K odstranění jednoho záznamu z tabulky databáze DB2 použijte akci **Odstranit řádek** ve vaší aplikaci logiky. Tato akce spustí příkaz DB2 `DELETE` , například `DELETE FROM AREA WHERE AREAID = '99999'` .

1. Pokud jste ještě nikdy nepoužili akce DB2 před ve vaší aplikaci logiky, přečtěte si postup v části [Přidání akce DB2 – získat tabulky](#add-db2-action) , ale místo toho přidejte akci **Odstranit řádek** a pak se vraťte sem, abyste mohli pokračovat.

   Když přidáte akci **Odstranit řádek** , tady je ukázka, jak se zobrazuje vaše ukázková aplikace logiky:

   ![Akce odstranění řádku](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. Zadejte hodnoty pro všechny požadované vlastnosti (*). Po výběru tabulky akce zobrazí příslušné vlastnosti, které jsou specifické pro záznamy v této tabulce.

   V tomto příkladu jsou zde vlastnosti:

   | Vlastnost | Povinné | Popis |
   |----------|----------|-------------|
   | **Název tabulky** | Yes | Tabulka, ve které se má záznam odstranit, například "oblast" |
   | **ID řádku** | Yes | ID záznamu, který se má odstranit, například "99999" |
   ||||

   Například:

   ![Snímek obrazovky, který zobrazuje návrháře Logic Apps s akcí odstranit řádek (Preview), kde vyberete tabulku, která se má odstranit](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. Až budete hotovi, na panelu nástrojů návrháře klikněte na **Uložit**.

### <a name="view-delete-row-outputs"></a>Zobrazit odstranění výstupů řádků

Pokud chcete aplikaci logiky spustit ručně, na panelu nástrojů návrháře klikněte na **Spustit**. Po dokončení běhu aplikace logiky můžete zobrazit výstup z běhu.

1. V nabídce aplikace logiky vyberte **Přehled**.

1. V části **Souhrn** v části **historie spuštění** vyberte poslední spuštění, což je první položka v seznamu.

1. V části **spuštění aplikace logiky** teď můžete zkontrolovat stav, vstupy a výstupy pro každý krok v aplikaci logiky.
Rozbalte akci **Odstranit řádek** .

1. Chcete-li zobrazit vstupy, vyberte možnost **Zobrazit nezpracované vstupy**.

1. Chcete-li zobrazit výstupy, vyberte možnost **Zobrazit nezpracované výstupy**.

   Výstupy již neobsahují záznam, který jste odstranili ze zadané tabulky.

   ![Zobrazit výstup bez odstraněného řádku](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o této spojnici, jako jsou triggery, akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete na [referenční stránce konektoru](/connectors/db2/).

> [!NOTE]
> V případě Logic Apps v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Tato verze konektoru ISE-Label používá místo toho [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)

