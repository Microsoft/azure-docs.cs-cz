---
title: Připojení k Common Data Service
description: Vytváření a Správa záznamů Common Data Service pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 05/08/2020
tags: connectors
ms.openlocfilehash: de85a61cbd699ec9ac2669f8abb6217254038de9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91334578"
---
# <a name="create-and-manage-records-in-common-data-service-by-using-azure-logic-apps"></a>Vytváření a Správa záznamů v Common Data Service pomocí Azure Logic Apps

Pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [konektoru Common data Service](/connectors/commondataservice/)můžete vytvářet automatizované pracovní postupy, které spravují záznamy v databázi [Common data Service](/powerapps/maker/common-data-service/data-platform-intro) . Tyto pracovní postupy mohou vytvářet záznamy, aktualizovat záznamy a provádět jiné operace. Můžete také získat informace z databáze Common Data Service a zpřístupnit výstup ostatním akcím pro použití v aplikaci logiky. Například při aktualizaci záznamu ve vaší databázi Common Data Service můžete odeslat e-mail pomocí konektoru Office 365 Outlook.

Tento článek ukazuje, jak můžete vytvořit aplikaci logiky, která při každém vytvoření záznamu nového zájemce vytvoří záznam úkolu.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Prostředí Common data Service](/power-platform/admin/environments-overview), což je místo, kde vaše organizace ukládá, spravuje a sdílí obchodní data a databázi Common data Service. Další informace najdete v těchto zdrojích:<p>

  * [Další informace: Začínáme s Common Data Service](/learn/modules/get-started-with-powerapps-common-data-service/)
  * [Power Platform – Přehled prostředí](/power-platform/admin/environments-overview)

* Základní informace o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) a aplikace logiky, ze kterých chcete získat přístup k záznamům ve vaší databázi Common data Service. Pokud chcete spustit aplikaci logiky pomocí Common Data Service triggeru, budete potřebovat prázdnou aplikaci logiky. Pokud s Azure Logic Apps začínáte, Projděte si [rychlý Start: vytvoření prvního pracovního postupu pomocí Azure Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-common-data-service-trigger"></a>Přidat aktivační událost Common Data Service

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

V tomto příkladu přidejte Trigger Common Data Service, který se aktivuje při vytvoření nového záznamu.

1. V [Azure Portal](https://portal.azure.com)otevřete prázdnou aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. Do vyhledávacího pole zadejte `common data service`. V tomto příkladu vyberte v seznamu triggery tuto aktivační událost: **když se vytvoří záznam** .

   ![Při vytvoření záznamu vybrat se spustí](./media/connect-common-data-service/select-when-record-created-trigger.png)

1. Pokud se zobrazí výzva, přihlaste se k Common Data Service.

1. V aktivační události zadejte informace o prostředí, ve kterém chcete monitorovat nové záznamy "zájemce", například:

   ![Aktivovat informace o prostředí pro monitorování](./media/connect-common-data-service/when-record-created-trigger-details.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Prostředí** | Yes | Prostředí, které se má monitorovat, například "prodejní výroba Fabrikam". Další informace najdete v tématu [Power Platform – Přehled prostředí](/power-platform/admin/environments-overview). |
   | **Název entity** | Yes | Entita, která se má monitorovat například "Zájemci" |
   | **Rozsah** | Yes | Zdroj, který vytvořil nový záznam, například uživatel ve vaší obchodní jednotce nebo libovolný uživatel ve vaší organizaci. V tomto příkladu se používá "obchodní jednotka". |
   ||||

## <a name="add-common-data-service-action"></a>Přidat Common Data Service akci

Nyní přidejte Common Data Service akci, která vytvoří záznam úkolu pro nový záznam "zájem".

1. V části **když je vytvořen záznam** , vyberte možnost **Nový krok**.

1. Do vyhledávacího pole zadejte `common data service`. V seznamu akce vyberte tuto akci: **vytvořit nový záznam** .

   ![Vyberte akci vytvořit nový záznam.](./media/connect-common-data-service/select-create-new-record-action.png)

1. V akci zadejte informace o prostředí, ve kterém chcete vytvořit nový záznam úkolu. Pokud je k dispozici, zobrazí se další vlastnosti v závislosti na entitě, kterou jste vybrali pro tuto akci, například:

   ![Informace o akcích pro prostředí, kde se má záznam vytvořit](./media/connect-common-data-service/create-new-record-action-details.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název organizace** | Yes | Prostředí, ve kterém chcete záznam vytvořit, takže v triggeru nemusí být stejné prostředí, ale v tomto příkladu se jedná o prodejní produkci Fabrikam. |
   | **Název entity** | Yes | Entita, ve které chcete záznam vytvořit, například "úkoly" |
   | **Předmět** | Ano, na základě entity vybrané v tomto příkladu | Krátký popis cíle pro tuto úlohu |
   ||||

   1. Jako vlastnost **subject (předmět** ) zadejte tento text s koncovým místem:

      `Follow up with new lead:`

   1. Nechejte ukazatel v poli **Předmět** , aby seznam dynamického obsahu zůstal viditelný.
   
   1. V seznamu v části **při vytváření záznamu** vyberte výstupy triggerů, které chcete zahrnout do záznamu úlohy, například:

      ![Vyberte výstupy triggerů, které se mají použít v záznamu úlohy.](./media/connect-common-data-service/create-new-record-action-select-trigger-outputs.png)

      | Výstup triggeru | Description |
      |----------------|-------------|
      | **Jméno** | Křestní jméno z záznamu zájemce pro použití jako primární kontakt v záznamu úkolu |
      | **Příjmení** | Příjmení ze záznamu zájemce, které se má použít jako primární kontakt v záznamu úkolu |
      | **Popis** | Další výstupy, které se mají zahrnout do záznamu úlohy, jako je e-mailová adresa a telefonní číslo do zaměstnání |
      |||

   Až to budete mít, akce může vypadat jako v tomto příkladu:

   ![Dokončila se akce vytvořit nový záznam.](./media/connect-common-data-service/finished-create-record-action-details.png)

1. Uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

1. Pokud chcete aplikaci logiky spustit ručně, na panelu nástrojů návrháře vyberte **Spustit**. K otestování aplikace logiky vytvořte nový záznam "zájem".

## <a name="trigger-only-on-updated-attributes"></a>Aktivovat jenom u aktualizovaných atributů

Pro aktivační události, které se spouštějí při aktualizaci záznamů, jako je například akce **při aktualizaci záznamu** , můžete použít atributy filtru, aby vaše aplikace logiky běžela pouze v případě, že jsou zadané atributy aktualizovány. Tato možnost pomáhá zabránit zbytečnému spuštění aplikace logiky.

1. V aktivační události v seznamu **Přidat nový parametr** vyberte **filtry atributů**.

   ![Snímek obrazovky zobrazující akci aktualizovat záznam "když je aktualizován" a otevře se seznam přidat nový parametr s vybranou vlastností "filtry atributů".](./media/connect-common-data-service/when-record-updated-trigger-add-attribute-filters.png)

1. Pro každou **položku filtrování atributů**vyberte atribut, který chcete monitorovat, například:

   ![Přidat vlastnost "Filters" (filtry atributů)](./media/connect-common-data-service/when-record-updated-trigger-select-attribute-filter.png)

## <a name="list-records-based-on-a-filter"></a>Vypsání záznamů na základě filtru

U akcí, které vracejí záznamy, jako je například akce **seznam záznamů** , můžete použít dotaz OData, který vrátí záznamy na základě zadaného filtru. Například můžete mít seznam akcí pouze záznamy pro aktivní účty.

1. V akci otevřete seznam **Přidat nový parametr** a vyberte vlastnost **dotaz filtru** .

   ![Přidat vlastnost dotaz filtru](./media/connect-common-data-service/list-records-action-filter-query.png)

1. Do vlastnosti **dotaz filtru** , která se nyní zobrazí v akci, zadejte tento dotaz filtru OData: `statuscode eq 1`

   ![Zadejte dotaz filtru ODATA pro filtrování záznamů.](./media/connect-common-data-service/list-records-action-filter-query-value.png)

Další informace o `$filter` možnostech systémových dotazů naleznete v tématu [Common data Service-Filter Results](/powerapps/developer/common-data-service/webapi/query-data-web-api#filter-results).

## <a name="list-records-based-on-an-order"></a>Vypsání záznamů na základě objednávky

U akcí, které vracejí záznamy, jako je například akce **seznam záznamů** , můžete použít dotaz OData, který vrátí záznamy v zadaném pořadí, které se liší v závislosti na záznamech, které akce vrací. Například můžete mít v seznamu akce seznam záznamů na základě názvu účtu.

1. V akci otevřete seznam **Přidat nový parametr** a vyberte vlastnost **ORDER by** .

   ![Přidat vlastnost ORDER by](./media/connect-common-data-service/list-records-action-order-by.png)

1. Do vlastnosti **ORDER by** , která se teď zobrazí v akci, zadejte tento dotaz filtru OData: `name`

   ![Zadejte dotaz filtru ODATA pro řazení záznamů.](./media/connect-common-data-service/list-records-action-order-by-value.png)

Další informace o `$orderby` možnostech systémových dotazů naleznete v tématu [Common data Service-Order Results](/powerapps/developer/common-data-service/webapi/query-data-web-api#order-results).

## <a name="field-data-types"></a>Datové typy polí

Bez ohledu na to, zda ručně zadáte hodnotu nebo vyberete hodnotu ze seznamu dynamického obsahu pro pole v aktivační události nebo akci, datový typ hodnoty musí odpovídat požadovanému datovému typu pole.

Tato tabulka popisuje některé typy polí a typy dat, které tato pole vyžadují pro jejich hodnoty.

| Pole | Datový typ | Popis |
|-------|-----------|-------------|
| Textové pole | Jeden řádek textu | Vyžaduje buď jeden řádek textu nebo dynamický obsah, který má datový typ text, například tyto vlastnosti: <p><p>- **Název** <br>- **Kategorií** |
| Pole celé číslo | Celé číslo | Vyžaduje buď celočíselný nebo dynamický obsah, který má datový typ integer, například tyto vlastnosti: <p><p>- **Procento dokončení** <br>- **Úkolu** |
| Pole data | Datum a čas | Vyžaduje buď datum ve formátu MM/DD/YYY, nebo dynamický obsah, který má datový typ datum, například tyto vlastnosti: <p><p>- **Datum vytvoření** <br>- **Počáteční datum** <br>- **Skutečný začátek** <br>- **Skutečný konec** <br>- **Termín splnění** |
| Pole, které odkazuje na jiný záznam entity | Primární klíč | Vyžaduje ID záznamu, jako je identifikátor GUID, a typ vyhledávání, což znamená, že hodnoty ze seznamu dynamického obsahu nebudou fungovat, například tyto vlastnosti: <p><p>- **Vlastník**: musí se jednat o platné ID uživatele nebo ID záznamu týmu. <br>- **Typ vlastníka**: musí se jednat o typ vyhledávání `systemusers` , například `teams` nebo, v uvedeném pořadí. <p><p>- **Týkající se**: musí se jednat o platné ID záznamu, jako je ID účtu nebo ID záznamu kontaktu. <br>- **Týká se typu**: musí se jednat o typ vyhledávání `accounts` , například nebo `contacts` , v uvedeném pořadí. <p><p>- **Zákazník**: musí se jednat o platné ID záznamu, jako je ID účtu nebo ID záznamu kontaktu. <br>- **Typ zákazníka**: musí se jednat o typ vyhledávání, například `accounts` nebo `contacts` , v uvedeném pořadí. |
||||

Tento příklad ukazuje, jak akce **vytvořit nový záznam** vytvoří nový záznam "Tasks", který je přidružen k ostatním záznamům entity, konkrétně záznam uživatele a záznam účtu. Akce určuje ID a vyhledávací typy pro tyto záznamy entit pomocí hodnot, které odpovídají očekávaným datovým typům pro příslušné vlastnosti.

* Na základě vlastnosti **Owner** , která určuje ID uživatele a vlastnost **typ vlastníka** , která určuje `systemusers` typ vyhledávání, akce přiřadí nový záznam "Tasks" s konkrétním uživatelem.

* V závislosti na **vlastnosti s** názvem, která určuje ID záznamu a vlastnost **související typ** , která určuje `accounts` typ vyhledávání, akce přiřadí nový záznam "Tasks" s konkrétním účtem.

![Vytvoření záznamu "Tasks" přidruženého k identifikátorům a vyhledávacím typům](./media/connect-common-data-service/create-new-record-task-properties.png)

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické informace na základě popisu Swagger konektoru, jako jsou triggery, akce, omezení a další podrobnosti, najdete na [referenční stránce konektoru](/connectors/commondataservice/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech pro Azure Logic Apps](../connectors/apis-list.md)
