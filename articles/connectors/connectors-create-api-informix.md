---
title: Připojení k databázi IBM Informix
description: Automatizace úloh a pracovních postupů, které spravují prostředky uložené v IBM Informix pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: daberry
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/07/2020
tags: connectors
ms.openlocfilehash: 4995a91783c2302f3bda5cc9409f017248ca29fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88761640"
---
# <a name="manage-ibm-informix-database-resources-by-using-azure-logic-apps"></a>Správa prostředků databáze IBM Informix pomocí Azure Logic Apps

Pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [konektoru Informix](/connectors/informix/)můžete vytvářet automatizované úlohy a pracovní postupy, které SPRAVUJÍ prostředky v databázi IBM Informix. Tento konektor zahrnuje klienta Microsoftu, který komunikuje se vzdálenými počítači Informix serveru napříč sítí TCP/IP, včetně cloudových databází, jako je například IBM Informix pro Windows spuštěné v virtualizaci Azure a v místních databázích, když použijete místní [bránu dat](../logic-apps/logic-apps-gateway-connection.md). K těmto platformám a verzím Informix se můžete připojit, pokud jsou nakonfigurované tak, aby podporovaly připojení klientů DRDA (Distributed relační Database Architecture):

* IBM Informix 12,1
* IBM Informix 11,7

V tomto tématu se dozvíte, jak pomocí konektoru v aplikaci logiky zpracovat operace databáze.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* V případě místních databází [Stáhněte a nainstalujte místní bránu dat](../logic-apps/logic-apps-gateway-install.md) na místním počítači a pak [vytvořte prostředek služby Azure data Gateway v Azure Portal](../logic-apps/logic-apps-gateway-connection.md).

* Aplikace logiky, kde potřebujete přístup k databázi Informix. Tento konektor nabízí jenom akce, takže vaše aplikace logiky už musí začínat triggerem, například [triggerem opakování](../connectors/connectors-native-recurrence.md). 

## <a name="add-an-informix-action"></a>Přidat akci Informix

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky, pokud ještě není otevřená.

1. V kroku, kam chcete přidat akci Informix, vyberte **Nový krok**.

   Pokud chcete přidat akci mezi stávajícími kroky, přesuňte ukazatel myši na šipku připojení. Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte `informix` jako filtr. V seznamu akce vyberte akci, kterou chcete, například:

   ![Vyberte akci Informix, která se má spustit.](./media/connectors-create-api-informix/select-informix-connector-action.png)

   Konektor poskytuje tyto akce, které spouštějí příslušné databázové operace:

   * Získat tabulky – vypíše tabulky databáze pomocí `CALL` příkazu.
   * Získá řádky – načte všechny řádky pomocí příkazu. `SELECT *`
   * Získat řádek a načíst řádek pomocí `SELECT WHERE` příkazu
   * Přidání řádku pomocí `INSERT` příkazu
   * Úprava řádku pomocí `UPDATE` příkazu
   * Odstranění řádku pomocí `DELETE` příkazu

1. Pokud se zobrazí výzva k zadání podrobností o připojení pro vaši databázi Informix, postupujte podle [pokynů pro vytvoření připojení](#create-connection)a pak pokračujte dalším krokem.

1. Zadejte informace pro vybranou akci:

   | Akce | Popis | Vlastnosti a popisy |
   |--------|-------------|-----------------------------|
   | **Získat tabulky** | Vypíše tabulky databáze spuštěním příkazu Informix. | Žádné |
   | **Získat řádky** | Načte všechny řádky v zadané tabulce spuštěním `SELECT *` příkazu Informix. | **Název tabulky**: název tabulky Informix, kterou chcete <p><p>Chcete-li do této akce přidat další vlastnosti, vyberte je ze seznamu **Přidat nový parametr** . Další informace naleznete v [tématu reference ke konektoru](/connectors/informix/). |
   | **Získat řádek** | Načte řádek ze zadané tabulky spuštěním `SELECT WHERE` příkazu Informix. | - **Název tabulky**: název tabulky Informix, kterou chcete <br>- **ID řádku**: jedinečné ID řádku, například `9999` |
   | **Vložení řádku** | Spuštěním příkazu Informix přidejte řádek do zadané tabulky Informix `INSERT` . | - **Název tabulky**: název tabulky Informix, kterou chcete <br>- **položka**: řádek s hodnotami, které se mají přidat |
   | **Aktualizovat řádek** | Spuštěním příkazu Informix změňte řádek v zadané tabulce Informix `UPDATE` . | - **Název tabulky**: název tabulky Informix, kterou chcete <br>- **ID řádku**: jedinečné ID řádku, který se má aktualizovat, například `9999` <br>- **Řádek**: řádek s aktualizovanými hodnotami, například `102` |
   | **Odstranit řádek** | Odebere řádek ze zadané tabulky Informix spuštěním `DELETE` příkazu Informix. | - **Název tabulky**: název tabulky Informix, kterou chcete <br>- **ID řádku**: jedinečné ID řádku, který se má odstranit, například `9999` |
   ||||

1. Uložte aplikaci logiky. Nyní buď [otestujte aplikaci logiky](#test-logic-app) , nebo pokračujte v sestavování aplikace logiky.

<a name="create-connection"></a>

## <a name="connect-to-informix"></a>Připojit k Informix

1. Pokud se aplikace logiky připojí k místní databázi, vyberte **připojit přes místní bránu dat**.

1. Zadejte tyto informace o připojení a pak vyberte **vytvořit**.

   | Vlastnost | Vlastnost JSON | Vyžadováno | Příklad hodnoty | Popis |
   |----------|---------------|----------|---------------|-------------|
   | Název připojení | `name` | Ano | `informix-demo-connection` | Název, který se má použít pro připojení k databázi Informix |
   | Server | `server` | Ano | Cloudu `informixdemo.cloudapp.net:9089` <br>– Místní: `informixdemo:9089` | Adresa TCP/IP nebo alias, který je ve formátu IPv4 nebo IPv6 následovaný dvojtečkou a číslem portu TCP/IP |
   | databáze | `database` | Ano | `nwind` | Název relační databáze DRDA (RDBNAM) nebo název databáze Informix (dbname). Informix akceptuje řetězec 128 bajtů. |
   | Authentication | `authentication` | Pouze místní | **Basic** nebo **Windows** (Kerberos) | Typ ověřování, který požaduje vaše databáze Informix. Tato vlastnost se zobrazí jenom v případě, že vyberete **připojit přes místní bránu dat**. |
   | Uživatelské jméno | `username` | Ne | <*databáze – uživatelské jméno*> | Uživatelské jméno pro databázi |
   | Heslo | `password` | Ne | <*databáze – heslo*> | Heslo pro databázi |
   | brána | `gateway` | Pouze místní | -<*Azure – předplatné*> <br>-<*Azure-on-premises-data-Gateway-Resource*> | Předplatné Azure a název prostředku Azure pro místní bránu dat, kterou jste vytvořili v Azure Portal. Vlastnost **brány** a dílčí vlastnosti se zobrazí jenom v případě, že vyberete **připojit přes místní bránu dat**. |
   ||||||

   Například:

   * **Cloudová databáze**

     ![Informace o připojení k databázi cloudu](./media/connectors-create-api-informix/informix-cloud-connection.png)

   * **Místní databáze**

     ![Informace o připojení k místní databázi](./media/connectors-create-api-informix/informix-on-premises-connection.png)

1. Uložte aplikaci logiky.

<a name="test-logic-app"></a>

## <a name="test-your-logic-app"></a>Testování aplikace logiky

1. Na panelu nástrojů návrháře aplikace logiky vyberte **Spustit**. Po spuštění aplikace logiky můžete zobrazit výstupy z tohoto spuštění.

1. V nabídce vaší aplikace logiky vyberte **Přehled**. V podokně Přehled v části **Souhrnná**  >  **historie spuštění** vyberte poslední spuštění.

1. V části **spuštění aplikace logiky** vyberte **Spustit podrobnosti**.

1. V seznamu akce vyberte akci s výstupy, které chcete zobrazit, například **Get_tables**.

   Pokud byla akce úspěšná, jejich vlastnost **status** je označena jako **úspěšná**.

1. Chcete-li zobrazit vstupy, klikněte v části **vstupy na odkaz** odkaz na adresu URL. Chcete-li zobrazit výstupy, klikněte v části odkaz na **výstup** odkazy na odkaz adresa URL. Tady je několik ukázkových výstupů:

   * **Get_tables** zobrazuje seznam tabulek:

     ![Výstupy z akce získat tabulky](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

   * **Get_rows** zobrazuje seznam řádků:

     ![Výstupy z akce získat řádky](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

   * **Get_row** zobrazuje zadaný řádek:

     ![Výstupy z akce získat řádek](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

   * **Insert_row** se zobrazuje nový řádek:

     ![Výstupy z akce "Vložit řádek"](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

   * **Update_row** se zobrazuje aktualizovaný řádek:

     ![Výstupy z akce aktualizovat řádek](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

   * **Delete_row** zobrazuje odstraněný řádek:

     ![Výstupy z akce Odstranit řádek](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="connector-specific-details"></a>Podrobnosti specifické pro spojnici

Technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu Swagger konektoru, najdete na [referenční stránce konektoru](/connectors/informix/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](apis-list.md)
