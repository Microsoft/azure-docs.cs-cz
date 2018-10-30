---
title: Připojení k systémům SAP – Azure Logic Apps | Dokumentace Microsoftu
description: Jak zobrazit a spravovat prostředky SAP díky automatizaci pracovních postupů pomocí Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/31/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, divswa, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 77d1e11c1400f9a3d6bb6bda8e935cd4d24a195e
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230892"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Připojení k systémům SAP v Azure Logic Apps

> [!NOTE]
> Tento konektor SAP bude brzy přestanou používat. Vydali jsme nové a rozšířené konektoru SAP a doporučujeme zvolit nebo přesunout [nový konektor SAP](./logic-apps-using-sap-connector.md).
>  

Tento článek popisuje, jak můžete přístup k prostředkům SAP z uvnitř aplikace logiky pomocí konektorů aplikační Server SAP a Server zpráv SAP. Tímto způsobem můžete automatizovat úlohy, procesy a pracovní postupy, které spravují vaše data systému SAP a prostředky tím, že vytvoříte aplikace logiky.

Tento příklad používá aplikace logiky, který můžete aktivovat pomocí požadavku HTTP. Aplikace logiky odešle zprostředkující dokumentu (IDoc) na SAP server a vrátí odpověď žadatel, který volá aplikaci logiky.
Aktuální konektory SAP mají akce, ale ne aktivačních událostí, takže v tomto příkladu [triggeru požadavku HTTP](../connectors/connectors-native-reqres.md) jako první krok v pracovním postupu vaší aplikace logiky. Technické informace specifické pro konektor SAP najdete v těchto článcích odkaz: 

* <a href="https://docs.microsoft.com/connectors/sapapplicationserver/" target="blank">Konektor aplikační Server SAP</a>
* <a href="https://docs.microsoft.com/connectors/sapmessageserver/" target="blank">Konektor Server zpráv SAP</a>

Pokud nemáte ještě předplatné Azure <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrovat si bezplatný účet Azure</a>.

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle tohoto článku, budete potřebovat tyto položky:

* Aplikace logiky, ze kterého má přístup k systému SAP a aktivační události, která spustí pracovní postup aplikace logiky. Konektory SAP aktuálně poskytuje pouze akce. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý start: vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Vaše <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">aplikační Server SAP</a> nebo <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">Server zpráv SAP</a>

* Stáhněte a nainstalujte nejnovější [na místní bránu dat](https://www.microsoft.com/download/details.aspx?id=53127) v libovolném v místním počítači. Ujistěte se, že nastavíte bránu na webu Azure Portal, než budete pokračovat. Brána umožňuje bezpečný přístup k datům a prostředky jsou v místním prostředí. Další informace najdete v tématu [instalace místní brány dat pro Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

* Stáhněte a nainstalujte klientské knihovny nejnovější SAP, která je aktuálně <a href="https://softwaredownloads.sap.com/file/0020000000086282018" target="_blank">konektoru SAP (NCo) 3.0.20.0 pro rozhraní Microsoft .NET Framework 4.0 a Windows 64-bit (x64)</a>, ve stejném počítači jako místní brána dat. Instalace této verze nebo novější z těchto důvodů:

  * Starší verze SAP NCo může stát zablokována odeslání zprávy více než jeden IDoc ve stejnou dobu. 
  Tato podmínka blokuje všechny novější zprávy, které se odesílají do cílového umístění SAP, způsobí zprávy do vypršení časového limitu.

  * Místní brána dat se spustí pouze v 64bitových systémech. 
  V opačném případě dojde k chybě "Chybný image", protože hostitelská služba Brána dat nepodporuje 32bitová sestavení instalujte.

  * Hostitelská služba brány dat a SAP adaptér Microsoft pomocí rozhraní .NET Framework 4.5. NCo SAP pro rozhraní .NET Framework 4.0 funguje s procesy, které používají modul runtime rozhraní .NET 4.0 na 4.7.1. 
  NCo SAP pro rozhraní .NET Framework 2.0 pracuje s procesy, které používají modul runtime rozhraní .NET 2.0 na 3.5 a už spolupracuje s nejnovější místní bránu dat.

* Obsah zprávy zasílané na server SAP, jako je například ukázkový soubor IDoc. Tento obsah musí být ve formátu XML a musí obsahovat obor názvů pro SAP akci, kterou chcete použít.

<a name="add-trigger"></a>

## <a name="add-http-request-trigger"></a>Přidat aktivační událost požadavek HTTP

V Azure Logic Apps, musí začínat každá aplikace logiky [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts), který se aktivuje při určité události nebo když je splněna konkrétní podmínka. Pokaždé, když se trigger aktivuje, modul Logic Apps vytvoří instanci aplikace logiky a spustí pracovní postup vaší aplikace.

V tomto příkladu vytvoříte aplikaci logiky s koncovým bodem v Azure tak, aby mohlo odesílat *požadavků HTTP POST* do aplikace logiky. Pokud vaše aplikace logiky obdrží tyto požadavky HTTP, aktivuje se a spustí další krok v pracovním postupu.

1. Na webu Azure Portal vytvoření prázdné aplikace logiky, otevře se návrhář aplikace logiky. 

2. Do vyhledávacího pole zadejte jako filtr "požadavek http". Ze seznamu triggerů vyberte tento trigger: **požadavek – přijetí požadavku HTTP je při**

   ![Přidat aktivační událost požadavek HTTP](./media/logic-apps-using-sap-connector-old/add-trigger.png)

3. Abyste mohli generovat adresu URL koncového bodu pro vaši aplikaci logiky uložte aplikaci logiky.
Na panelu nástrojů návrháře zvolte **Uložit**. 

   Koncový bod adresy URL se teď zobrazí v aktivační událost, například:

   ![Generování adresy URL pro koncový bod](./media/logic-apps-using-sap-connector-old/generate-http-endpoint-url.png)

<a name="add-action"></a>

## <a name="add-sap-action"></a>Přidání akce SAP

V Azure Logic Apps [akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je krok v pracovním postupu, který následuje aktivační události nebo jiné akce. Pokud trigger aplikace logiky zatím nepřidali a chcete postupujte podle tohoto příkladu [přidat aktivační události popisované v této části](#add-trigger).

1. V návrháři aplikace logiky pod triggerem zvolte **nový krok** > **přidat akci**.

   ![Přidání akce](./media/logic-apps-using-sap-connector-old/add-action.png) 

2. Do vyhledávacího pole zadejte jako filtr "sap server". Ze seznamu akcí vyberte akci pro váš server SAP: 

   * **Aplikační Server SAP - odeslat SAP**
   * **Server zpráv SAP - odeslat SAP**

   Tento příklad používá tuto akci: **aplikační Server SAP - odeslat SAP**

   ![Vyberte "aplikační Server SAP" nebo "Server zpráv SAP"](media/logic-apps-using-sap-connector-old/select-sap-action.png)

3. Pokud se zobrazí výzva k zadání podrobností o připojení, vytvoření připojení k SAP nyní. Jinak Pokud připojení již existuje, pokračujte dalším krokem, můžete nastavit akci SAP. 

   **Vytvoření připojení místních SAP**

   1. Pro **brány**vyberte **připojit přes místní bránu dat** tak, aby se zobrazí místní vlastnosti připojení.

   2. Zadání informací o připojení k serveru SAP. 
   Pro **brány** vlastnosti, vyberte bránu dat, který jste vytvořili na webu Azure Portal pro vaši instalaci brány, například:

      **Aplikační Server SAP**

      ![Vytvoření připojení k serveru aplikace SAP](./media/logic-apps-using-sap-connector-old/create-SAP-app-server-connection.png)  

      **Server zpráv SAP**

      ![Vytvoření připojení k serveru zpráv SAP](media/logic-apps-using-sap-connector-old/create-SAP-message-server-connection.png) 

   2. Jakmile budete hotoví, vyberte **Vytvořit**.

      Logic Apps vytvoří a otestuje připojení, ujistěte se, že připojení funguje správně.

4. Nyní vyhledejte a vyberte akci ze serveru SAP. 

   1. V **SAP akce** vyberte ikonu složky. 
   Ze seznamu složek vyhledejte a vyberte akci, kterou chcete použít. 

      Vybere v tomto příkladu **IDOC** kategorie pro akci IDoc. 

      ![Vyhledání a výběr akce IDoc](./media/logic-apps-using-sap-connector-old/SAP-app-server-find-action.png)

      Pokud nemůžete najít požadovanou akci, můžete ručně zadat cestu, například:

      ![Ručně zadat cestu k IDoc akce](./media/logic-apps-using-sap-connector-old/SAP-app-server-manually-enter-action.png)

      Další informace o operacích IDoc najdete v tématu [zprávy schémata pro operace IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

   2. Klikněte do **vstupní zprávy** pole tak, aby zobrazil seznam dynamického obsahu. 
   V tomto seznamu, v části **přijetí požadavku HTTP při**, vyberte **tělo** pole. 

      Tento krok zahrnuje obsah textu z triggeru požadavku HTTP a odesílá, jehož výstupem k serveru SAP.

      ![Vyberte pole "Body"](./media/logic-apps-using-sap-connector-old/SAP-app-server-action-select-body.png)

      Až budete hotovi, vaše akce SAP bude vypadat jako v tomto příkladu:

      ![Dokončení akce SAP](./media/logic-apps-using-sap-connector-old/SAP-app-server-complete-action.png)

6. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

<a name="add-response"></a>

## <a name="add-http-response-action"></a>Přidání akce odpovědi HTTP

Teď přidejte akci odpovědi do pracovního postupu aplikace logiky a zahrnutí výstupu z akce SAP. Tímto způsobem, vaše aplikace logiky vrátí výsledky z vašeho serveru SAP ho původnímu žadateli. 

1. V návrháři aplikace logiky podle akce SAP, zvolte **nový krok** > **přidat akci**.

2. Do vyhledávacího pole zadejte jako filtr "odpověď". Ze seznamu akcí vyberte tuto akci: **požadavku - odpovědi**

3. Klikněte do **tělo** pole tak, aby zobrazil seznam dynamického obsahu. Z tohoto seznamu v části **poslat SAP**, vyberte **tělo** pole. 

   ![Dokončení akce SAP](./media/logic-apps-using-sap-connector-old/select-sap-body-for-response-action.png)

4. Uložte svou aplikaci logiky. 

## <a name="test-your-logic-app"></a>Otestujte aplikaci logiky

1. Pokud vaše aplikace logiky již není povolena, v nabídce aplikace logiky, vyberte **přehled**. Na panelu nástrojů zvolte **povolit**. 

2. Na panelu nástrojů návrháře aplikace logiky zvolte **spustit**. Tímto krokem ručně spustíte svou aplikaci logiky.

3. Odesláním požadavku HTTP POST na adresu URL na váš trigger HTTP žádosti o aktivaci vaší aplikace logiky a zahrnout zprávy obsahu při zpracování požadavku. K odeslání požadavku můžete použít nástroj, jako [Postman](https://www.getpostman.com/apps). 

   Pro účely tohoto článku odešle požadavek IDoc soubor, který musí být ve formátu XML a obsahoval obor názvů pro SAP akci, kterou používáte, třeba: 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//620/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. Po odeslání požadavku HTTP čekat na odpověď z vaší aplikace logiky.

> [!NOTE]
> Aplikace logiky může být vypršení časového limitu, pokud nedokončíte všechny kroky potřebné pro odpověď v rámci [časový limit požadavku](./logic-apps-limits-and-config.md). Pokud k tomuto stavu dochází, požadavky zablokoval. Při diagnostice problémů, přečtěte si, jak [zkontrolujte a monitorování aplikací logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Blahopřejeme, právě jste vytvořili aplikaci logiky, který může komunikovat se serverem SAP. Teď, když nastavíte připojení k SAP pro aplikace logiky, můžete prozkoumat další dostupné akce SAP, jako je například BAPI a v dokumentu RFC.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o konektoru, jak je popsáno konektory Swagger soubory najdete v těchto článcích odkaz: 

* [Aplikační Server SAP](/connectors/sapapplicationserver/)
* [Server zpráv SAP](/connectors/sapmessageserver/)

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* [Připojení k místním systémům](../logic-apps/logic-apps-gateway-connection.md) z aplikací logiky
* Zjistěte, jak ověřit, transformaci a další operace zprávu s [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
