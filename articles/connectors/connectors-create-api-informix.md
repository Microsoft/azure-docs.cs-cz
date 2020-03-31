---
title: Připojení k databázi IBM Informix
description: Automatizace úloh a pracovních postupů, které spravují prostředky uložené v IBM Informix pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: plarsen
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/07/2020
tags: connectors
ms.openlocfilehash: dccb715c974037b4e3080f3e51576feae34c03df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76757964"
---
# <a name="manage-ibm-informix-database-resources-by-using-azure-logic-apps"></a>Správa databázových prostředků IBM Informix pomocí Azure Logic Apps

Pomocí [aplikací Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [konektoru Informix](/connectors/informix/)můžete vytvářet automatizované úlohy a pracovní postupy, které spravují prostředky v databázi IBM Informix. Tento konektor zahrnuje klienta Microsoftu, který komunikuje se vzdálenými serverovými počítači Informix v síti TCP/IP, včetně cloudových databází, jako je IBM Informix for Windows spuštěného v virtualizaci Azure, a místních databází při použití [místní brány dat](../logic-apps/logic-apps-gateway-connection.md). K těmto platformám a verzím Informixu se můžete připojit, pokud jsou nakonfigurovány tak, aby podporovaly připojení klientů drda (Distributed Relational Database Architecture):

* IBM Informix 12.1
* IBM Informix 11,7

Toto téma ukazuje, jak používat konektor v aplikaci logiky ke zpracování databázových operací.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Pro místní databáze [stáhněte a nainstalujte místní datovou bránu](../logic-apps/logic-apps-gateway-install.md) do místního počítače a pak [vytvořte prostředek brány dat Azure na webu Azure Portal](../logic-apps/logic-apps-gateway-connection.md).

* Aplikace logiky, kde potřebujete přístup k databázi Informix. Tento konektor poskytuje pouze akce, takže aplikace logiky musí již začít s aktivační událostí, například [aktivační událost opakování](../connectors/connectors-native-recurrence.md). 

## <a name="add-an-informix-action"></a>Přidání akce Informix

1. Na [webu Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v Návrháři aplikací logiky, pokud už není otevřená.

1. Pod krokem, kam chcete přidat akci Informix, vyberte **Nový krok**.

   Chcete-li přidat akci mezi existující kroky, přesuňte ukazatel myši na spojovací šipku. Vyberte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.

1. Do vyhledávacího pole `informix` zadejte jako filtr. Ze seznamu akcí vyberte požadovanou akci, například:

   ![Vyberte akci Informix, kterou chcete spustit.](./media/connectors-create-api-informix/select-informix-connector-action.png)

   Spojnice poskytuje tyto akce, které spouštějí odpovídající databázové operace:

   * Získat tabulky – seznam databázových tabulek pomocí příkazu `CALL`
   * Získat řádky – čtení všech `SELECT *` řádků pomocí příkazu
   * Získat řádek – čtení řádku `SELECT WHERE` pomocí příkazu
   * Přidání řádku pomocí `INSERT` příkazu
   * Úprava řádku pomocí `UPDATE` příkazu
   * Odstranění řádku pomocí `DELETE` příkazu

1. Pokud se zobrazí výzva k zadání podrobností o připojení k databázi Informix, [vytvořte připojení](#create-connection)podle pokynů a pokračujte dalším krokem.

1. Zadejte informace pro vybranou akci:

   | Akce | Popis | Vlastnosti a popisy |
   |--------|-------------|-----------------------------|
   | **Získat tabulky** | Seznam databázové tabulky spuštěním příkazu Informix CALL. | Žádný |
   | **Získat řádky** | Načíst všechny řádky v zadané tabulce spuštěním příkazu Informix. `SELECT *` | **Název tabulky**: Název požadované tabulky Informix <p><p>Chcete-li do této akce přidat další vlastnosti, vyberte je ze seznamu **Přidat nový parametr.** Další informace naleznete v [referenčním tématu konektoru](/connectors/informix/). |
   | **Získat řádek** | Načtení řádku ze zadané tabulky spuštěním `SELECT WHERE` příkazu Informix. | - **Název tabulky**: Název požadované tabulky Informix <br>- **ID řádku**: Jedinečné ID pro řádek, například`9999` |
   | **Vložení řádku** | Přidejte řádek do zadané tabulky Informix spuštěním příkazu Informix. `INSERT` | - **Název tabulky**: Název požadované tabulky Informix <br>- **položka**: Řádek s přidanými hodnotami |
   | **Aktualizovat řádek** | Změňte řádek v zadané tabulce Informix spuštěním příkazu Informix. `UPDATE` | - **Název tabulky**: Název požadované tabulky Informix <br>- **ID řádku**: Jedinečné ID pro řádek aktualizovat, například`9999` <br>- **Řádek**: Řádek s aktualizovanými hodnotami, například`102` |
   | **Odstranit řádek** | Odeberte řádek ze zadané tabulky Informix `DELETE` spuštěním příkazu Informix. | - **Název tabulky**: Název požadované tabulky Informix <br>- **ID řádku**: Jedinečné ID pro řádek, který má být odstraněn, například`9999` |
   ||||

1. Uložte svou aplikaci logiky. Teď buď [otestujte aplikaci logiky,](#test-logic-app) nebo pokračujte v vytváření aplikace logiky.

<a name="create-connection"></a>

## <a name="connect-to-informix"></a>Připojení k Informixu

1. Pokud se vaše aplikace logiky připojuje k místní databázi, vyberte **Připojit prostřednictvím místní brány dat**.

1. Zadejte tyto informace o připojení a vyberte **příkaz Vytvořit**.

   | Vlastnost | Vlastnost JSON | Požaduje se | Příklad hodnoty | Popis |
   |----------|---------------|----------|---------------|-------------|
   | Název připojení | `name` | Ano | `informix-demo-connection` | Název, který chcete použít pro připojení k databázi Informix |
   | Server | `server` | Ano | - Mrak:`informixdemo.cloudapp.net:9089` <br>- Místní:`informixdemo:9089` | Adresa nebo alias Protokolu TCP/IP ve formátu IPv4 nebo IPv6 následovaný dvojtečkou a číslem portu TCP/IP |
   | Databáze | `database` | Ano | `nwind` | Název relační databáze DRDA (RDBNAM) nebo Název databáze Informix (dbname). Informix přijímá řetězec 128 bajtů. |
   | Ověřování | `authentication` | Pouze v místním prostředí | **Základní** nebo **Windows** (kerberos) | Typ ověřování, který je vyžadován vaší databází Informix. Tato vlastnost se zobrazí pouze v případě, že vyberete **připojit prostřednictvím místní brány dat**. |
   | Uživatelské jméno | `username` | Ne | <*databáze-uživatelské jméno*> | Uživatelské jméno databáze |
   | Heslo | `password` | Ne | <*heslo databáze*> | Heslo pro databázi |
   | brána | `gateway` | Pouze v místním prostředí | - <*předplatného Azure*> <br>- <*Azure-on-premises-data-gateway-resource*> | Předplatné Azure a název prostředku Azure pro místní datovou bránu, kterou jste vytvořili na webu Azure Portal. Vlastnost **gateway** a dílčí vlastnosti se zobrazí pouze v případě, že vyberete **připojit prostřednictvím místní brány dat**. |
   ||||||

   Například:

   * **Cloudová databáze**

     ![Informace o připojení k cloudové databázi](./media/connectors-create-api-informix/informix-cloud-connection.png)

   * **Místní databáze**

     ![Informace o připojení k místní databázi](./media/connectors-create-api-informix/informix-on-premises-connection.png)

1. Uložte svou aplikaci logiky.

<a name="test-logic-app"></a>

## <a name="test-your-logic-app"></a>Testování aplikace logiky

1. Na panelu nástrojů Návrhář epoje vyberte **Spustit**. Po spuštění aplikace logiky můžete zobrazit výstupy z tohoto spuštění.

1. V nabídce aplikace logiky vyberte **Přehled**. V podokně přehledu vyberte v části **Historie souhrnných** > **spuštění**poslední spuštění.

1. V části **Spuštění aplikace logiky**vyberte **Spustit podrobnosti**.

1. Ze seznamu akcí vyberte akci s výstupy, které chcete zobrazit, například **Get_tables**.

   Pokud byla akce úspěšná, jejich **Stav** vlastnost je **označena**jako Úspěšné .

1. Chcete-li zobrazit vstupy, vyberte v části **Inputs Link**odkaz URL. Chcete-li zobrazit výstupy, vyberte v části **Odkaz na výstupy** odkaz URL. Zde jsou některé příklady výstupů:

   * **Get_tables** zobrazí seznam tabulek:

     ![Výstupy z akce Získat tabulky](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

   * **Get_rows** zobrazí seznam řádků:

     ![Výstupy z akce Získat řádky](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

   * **Get_row** zobrazí zadaný řádek:

     ![Výstupy z akce "Získat řádek"](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

   * **Insert_row** zobrazí nový řádek:

     ![Výstupy z akce Vložit řádek](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

   * **Update_row** zobrazí aktualizovaný řádek:

     ![Výstupy z akce Aktualizovat řádek](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

   * **Delete_row** zobrazí odstraněný řádek:

     ![Výstupy z akce Odstranit řádek](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="connector-specific-details"></a>Podrobnosti specifické pro konektor

Technické podrobnosti o aktivačních událostech, akcích a limitech, které jsou popsány v popisu swagger konektoru, zkontrolujte [referenční stránku konektoru](/connectors/informix/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](apis-list.md)
