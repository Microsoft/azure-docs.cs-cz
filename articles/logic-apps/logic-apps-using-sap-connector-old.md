---
title: Připojení k systémům SAP – Azure Logic Apps | Microsoft Docs
description: Jak získat přístup k prostředkům SAP a spravovat je díky automatizaci pracovních postupů pomocí Azure Logic Apps
author: ecfan
ms.author: estfan
ms.date: 05/31/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, divswa, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 31f6a3fc281b8dc309ddcd237246c870c85ae20b
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971631"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Připojení k systémům SAP z Azure Logic Apps

> [!NOTE]
> U tohoto konektoru SAP je naplánovaná jeho zastaralost. Použijte prosím nebo migrujte do [novějšího a](./logic-apps-using-sap-connector.md)pokročilejšího konektoru SAP. 

V tomto článku se dozvíte, jak získat přístup k prostředkům SAP z aplikace logiky pomocí konektorů aplikačního serveru SAP a zpráv serveru SAP. Tímto způsobem můžete automatizovat úlohy, procesy a pracovní postupy, které spravují data a prostředky SAP tím, že vytváříte Logic Apps.

V tomto příkladu se používá aplikace logiky, kterou můžete aktivovat pomocí požadavku HTTP. Aplikace logiky pošle zprostředkující dokument (IDoc) na Server SAP a vrátí odpověď žadateli, který se nazývá aplikace logiky.
Aktuální konektory SAP mají akce, ale ne triggery, takže v tomto příkladu se jako první krok v pracovním postupu aplikace logiky používá [Trigger požadavku HTTP](../connectors/connectors-native-reqres.md) . Technické informace týkající se konektoru SAP najdete v těchto referenčních článcích: 

* <a href="https://docs.microsoft.com/connectors/sap" target="blank">Konektor aplikačního serveru SAP</a>
* <a href="https://docs.microsoft.com/connectors/sap/#send-message-to-sap" target="blank">Konektor serveru zpráv SAP</a>

Pokud ještě nemáte předplatné Azure, zaregistrujte <a href="https://azure.microsoft.com/free/" target="_blank">si bezplatný účet Azure</a>.

## <a name="prerequisites"></a>Požadavky

Pokud chcete postupovat podle tohoto článku, budete potřebovat tyto položky:

* Aplikace logiky, ze které chcete získat přístup k systému SAP, a Trigger, který spouští pracovní postup vaší aplikace logiky. Konektory SAP aktuálně poskytují pouze akce. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý Start: Vytvořte svou první aplikaci](../logic-apps/quickstart-create-first-logic-app-workflow.md)logiky.

* <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">Aplikační Server SAP</a> nebo <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">Server zpráv SAP</a>

* Stáhněte si a nainstalujte nejnovější místní [bránu dat](https://www.microsoft.com/download/details.aspx?id=53127) na libovolný místní počítač. Než budete pokračovat, ujistěte se, že jste si nastavili bránu v Azure Portal. Brána vám pomůže zajistit zabezpečený přístup k datům a prostředkům místně. Další informace najdete v tématu [instalace místní brány dat pro Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

* Stáhněte a nainstalujte nejnovější knihovnu klienta SAP, která je aktuálně <a href="https://softwaredownloads.sap.com/file/0020000000086282018" target="_blank">konektorem SAP Connector (NCo) 3.0.20.0 pro Microsoft .NET Framework 4,0 a Windows 64 (x64)</a>, na stejném počítači jako místní brána dat. Nainstalujte tuto verzi nebo novější z těchto důvodů:

  * Starší verze SAP NCo se můžou zablokovat, když se odešlou víc než jedna zpráva IDoc. 
  Tento stav blokuje všechny pozdější zprávy, které se odesílají do cíle SAP, což způsobí vypršení časového limitu zpráv.

  * Místní brána dat se spouští jenom v 64 systémech. 
  V opačném případě se zobrazí chyba "chybná image", protože hostitelská služba brány dat nepodporuje 32 bitových sestavení.

  * Hostitelská služba brány dat i adaptér Microsoft SAP používají .NET Framework 4,5. SAP NCo for .NET Framework 4,0 funguje s procesy, které používají .NET Runtime 4,0 až 4.7.1. 
  SAP NCo for .NET Framework 2,0 funguje s procesy, které používají .NET runtime 2,0 na 3,5 a už nefungují s nejnovější místní bránou dat.

* Obsah zprávy, který můžete odeslat na Server SAP, například do ukázkového souboru IDoc. Tento obsah musí být ve formátu XML a zahrnovat obor názvů pro akci SAP, kterou chcete použít.

<a name="add-trigger"></a>

## <a name="add-http-request-trigger"></a>Přidat aktivační událost požadavku HTTP

V Azure Logic Apps musí každá aplikace logiky začínat [triggerem](../logic-apps/logic-apps-overview.md#logic-app-concepts), který se aktivuje, když dojde ke konkrétní události nebo když dojde ke splnění určité podmínky. Pokaždé, když se Trigger aktivuje, modul Logic Apps vytvoří instanci aplikace logiky a začne spouštět pracovní postup vaší aplikace.

V tomto příkladu vytvoříte aplikaci logiky s koncovým bodem v Azure, abyste mohli odesílat *požadavky HTTP POST* do vaší aplikace logiky. Když aplikace logiky obdrží tyto požadavky HTTP, Trigger se aktivuje a spustí další krok v pracovním postupu.

1. V Azure Portal vytvořte prázdnou aplikaci logiky, která otevře návrháře aplikace logiky. 

2. Do vyhledávacího pole zadejte jako filtr "požadavek HTTP". V seznamu triggery vyberte tuto aktivační událost: **Požadavek – při přijetí požadavku HTTP**

   ![Přidat aktivační událost požadavku HTTP](./media/logic-apps-using-sap-connector-old/add-trigger.png)

3. Nyní uložte aplikaci logiky, abyste mohli vygenerovat adresu URL koncového bodu pro vaši aplikaci logiky.
Na panelu nástrojů návrháře zvolte **Uložit**. 

   Adresa URL koncového bodu se teď zobrazí ve triggeru, například:

   ![Vygenerovat adresu URL pro koncový bod](./media/logic-apps-using-sap-connector-old/generate-http-endpoint-url.png)

<a name="add-action"></a>

## <a name="add-sap-action"></a>Přidat akci SAP

V Azure Logic Apps [Akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je krok v pracovním postupu, který následuje po triggeru nebo jiné akci. Pokud jste ještě nepřidali Trigger do aplikace logiky a chcete postupovat podle tohoto příkladu, [přidejte Trigger popsaný v této části](#add-trigger).

1. V návrháři aplikace logiky pod triggerem vyberte **Nový krok** > **přidat akci**.

   ![Přidat akci](./media/logic-apps-using-sap-connector-old/add-action.png) 

2. Do vyhledávacího pole zadejte jako filtr "SAP server". V seznamu akce vyberte akci pro Server SAP: 

   * **Aplikační Server SAP – odeslání do SAP**
   * **Server zpráv SAP – odeslání do SAP**

   Tento příklad používá tuto akci: **Aplikační Server SAP – odeslání do SAP**

   ![Vyberte "aplikační Server SAP" nebo "Server zpráv SAP"](media/logic-apps-using-sap-connector-old/select-sap-action.png)

3. Pokud se zobrazí výzva k zadání podrobností o připojení, vytvořte připojení SAP hned teď. Jinak, pokud vaše připojení už existuje, pokračujte dalším krokem, abyste mohli nastavit akci SAP. 

   **Vytvořit místní připojení SAP**

   1. V případě **bran**vyberte **připojit přes místní bránu dat** , aby se zobrazily vlastnosti místního připojení.

   2. Zadejte informace o připojení pro váš Server SAP. 
   U vlastnosti **Brána** vyberte bránu dat, kterou jste vytvořili v Azure Portal pro instalaci brány, například:

      **Aplikační Server SAP**

      ![Vytvořit připojení aplikačního serveru SAP](./media/logic-apps-using-sap-connector-old/create-SAP-app-server-connection.png)  

      **Server zpráv SAP**

      ![Vytvořit připojení k serveru zpráv SAP](media/logic-apps-using-sap-connector-old/create-SAP-message-server-connection.png) 

   2. Jakmile budete hotoví, vyberte **Vytvořit**.

      Logic Apps nastaví a otestuje připojení. tím se zajistí, že připojení funguje správně.

4. Nyní vyhledejte a vyberte akci ze serveru SAP. 

   1. V poli **Akce SAP** klikněte na ikonu složky. 
   V seznamu složek vyhledejte a vyberte akci, kterou chcete použít. 

      Tento příklad vybere kategorii **IDOC** pro akci IDOC. 

      ![Najít a vybrat akci IDoc](./media/logic-apps-using-sap-connector-old/SAP-app-server-find-action.png)

      Pokud požadovanou akci nemůžete najít, můžete zadat cestu ručně, například:

      ![Ručně zadat cestu k IDoc akci](./media/logic-apps-using-sap-connector-old/SAP-app-server-manually-enter-action.png)

      Další informace o operacích IDoc najdete v tématu [schémata zpráv pro operace IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations) .

   2. Klikněte do pole **vstupní zpráva** , aby se zobrazil seznam dynamického obsahu. 
   V tomto seznamu v části **když se přijme požadavek HTTP**, vyberte pole **body** . 

      Tento krok zahrnuje obsah zprávy z triggeru požadavku HTTP a odesílá tento výstup do vašeho serveru SAP.

      ![Vybrat pole "tělo"](./media/logic-apps-using-sap-connector-old/SAP-app-server-action-select-body.png)

      Až budete hotovi, vaše akce SAP bude vypadat jako v tomto příkladu:

      ![Dokončit akci SAP](./media/logic-apps-using-sap-connector-old/SAP-app-server-complete-action.png)

6. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

<a name="add-response"></a>

## <a name="add-http-response-action"></a>Přidat akci odpovědi HTTP

Teď přidejte akci odpovědi do pracovního postupu aplikace logiky a zahrňte výstup z akce SAP. Vaše aplikace logiky tak vrátí výsledky ze serveru SAP původnímu žadateli. 

1. V návrháři aplikace logiky v akci SAP vyberte **Nový krok** > **přidat akci**.

2. Do vyhledávacího pole zadejte jako filtr "Response". V seznamu akce vyberte tuto akci: **Požadavek – odpověď**

3. Klikněte do pole **text** , aby se zobrazil seznam dynamického obsahu. V tomto seznamu v části **Odeslat do SAP**vyberte pole **body** . 

   ![Dokončit akci SAP](./media/logic-apps-using-sap-connector-old/select-sap-body-for-response-action.png)

4. Uložte svou aplikaci logiky. 

## <a name="test-your-logic-app"></a>Testování aplikace logiky

1. Pokud vaše aplikace logiky ještě není povolená, klikněte v nabídce aplikace logiky na **Přehled**. Na panelu nástrojů vyberte možnost **Povolit**. 

2. Na panelu nástrojů návrháře aplikace logiky klikněte na **Spustit**. Tento krok ručně spustí vaši aplikaci logiky.

3. Aktivujte aplikaci logiky odesláním požadavku HTTP POST na adresu URL v triggeru požadavku HTTP a zahrňte obsah zprávy do vaší žádosti. K odeslání žádosti můžete použít nástroj, jako je například [post](https://www.getpostman.com/apps). 

   V tomto článku požadavek pošle soubor IDoc, který musí být ve formátu XML a zahrnuje obor názvů pro akci SAP, kterou používáte, například: 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//620/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. Až odešlete požadavek HTTP, počkejte na odpověď z vaší aplikace logiky.

> [!NOTE]
> Vaše aplikace logiky může vypršet, pokud všechny kroky požadované pro odpověď nekončí v rámci [časového](./logic-apps-limits-and-config.md)limitu požadavku. Pokud k tomuto stavu dojde, můžou se požadavky zablokovat. Pro usnadnění diagnostiky problémů se dozvíte, jak můžete [kontrolovat a monitorovat aplikace logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Gratulujeme, právě jste vytvořili aplikaci logiky, která může komunikovat se serverem SAP. Teď, když jste nastavili připojení SAP pro vaši aplikaci logiky, můžete prozkoumat další dostupné akce SAP, například BAPI a RFC.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o konektoru, jak je popsáno v souborech Swagger konektorů, najdete v těchto referenčních článcích: 

* [Aplikační Server SAP](/connectors/sap)
* [Server zpráv SAP](/connectors/sap/#send-message-to-sap)

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* [Připojení k](../logic-apps/logic-apps-gateway-connection.md) místním systémům z Logic Apps
* Zjistěte, jak ověřit, transformovat a další operace se zprávami pomocí [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
