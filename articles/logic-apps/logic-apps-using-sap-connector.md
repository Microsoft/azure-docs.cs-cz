---
title: Připojení k systémům SAP - Azure Logic Apps | Microsoft Docs
description: Přístupu a spravovat prostředky SAP automatizací pracovních službou Azure Logic Apps
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
ms.openlocfilehash: 3837896911b92361e0a6d0a7166a1b17651d6fe3
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112856"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Připojení k systémům SAP z Azure Logic Apps

Tento článek ukazuje, přístupu k prostředkům SAP z uvnitř aplikace logiky pomocí konektorů SAP aplikační Server a Server zpráv SAP. Tímto způsobem můžete automatizovat úlohy, procesy a pracovní postupy, které vytvoříte aplikace logiky spravovat vaše SAP data a prostředky.

Tento příklad používá aplikace logiky, která můžete spouštět s žádostí HTTP. Aplikace logiky odešle dokument zprostředkující (IDoc) na SAP server a vrátí odpověď žadatel, který volá aplikaci logiky.
Aktuální konektory SAP mají akce, ale není aktivačních událostí, takže tento příklad používá [aktivační událost požadavku HTTP](../connectors/connectors-native-reqres.md) jako první krok v postupu aplikace logiky. Technické informace specifické pro konektor SAP naleznete v článcích odkaz: 

* <a href="https://docs.microsoft.com/connectors/sapapplicationserver/" target="blank">Konektor aplikačního serveru SAP</a>
* <a href="https://docs.microsoft.com/connectors/sapmessageserver/" target="blank">Konektor zpráva serveru SAP</a>

Pokud nemáte předplatné Azure ještě <a href="https://azure.microsoft.com/free/" target="_blank">si zaregistrovat bezplatný účet Azure</a>.

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat, je třeba tyto položky:

* Aplikace logiky, ze kterého má přístup k systému SAP a aktivační událost, která spustí pracovní postup aplikace logiky. Konektory SAP aktuálně poskytuje pouze akce. Pokud jste nové aplikace logiky, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý start: vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Vaše <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">SAP aplikační Server</a> nebo <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">Server zpráv SAP</a>

* Stáhněte a nainstalujte nejnovější [místní brána dat](https://www.microsoft.com/download/details.aspx?id=53127) na libovolném počítači místně. Ujistěte se, že jste nastavili bránu na portálu Azure, než budete pokračovat. Brána pomáhá bezpečně přistupovat k datům a prostředky jsou místní. Další informace najdete v tématu [instalovat místní bránu data gateway pro Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

* Stáhněte a nainstalujte nejnovější knihovny klienta SAP, která je aktuálně <a href="https://softwaredownloads.sap.com/file/0020000000086282018" target="_blank">SAP konektoru (NCo) 3.0.20.0 pro rozhraní Microsoft .NET Framework 4.0 a Windows 64bitové (x64)</a>, na stejném počítači jako místní brána data gateway. Tuto verzi nainstalovat nebo novější z těchto důvodů:

  * Starší verze SAP NCo může stát zablokována při odesílání zpráv více než jeden IDoc ve stejnou dobu. 
  Tato podmínka blokuje všechny novější zprávy, které se odesílají do cílového umístění SAP, způsobuje zprávy vypršení časového limitu.

  * Místní brána dat se spustí pouze v 64bitových systémech. 
  Jinak hodnota zobrazí chyba "bad image", protože hostitelská služba brány dat nepodporuje 32bitové sestavení.

  * Hostitelská služba brány dat a SAP adaptéru Microsoft pomocí rozhraní .NET Framework 4.5. Spolupracuje se službou procesy, které používají modul runtime rozhraní .NET 4.0 na 4.7.1 NCo SAP pro rozhraní .NET Framework 4.0. 
  NCo SAP pro rozhraní .NET Framework 2.0 funguje s procesy, které používají modul runtime rozhraní .NET 2.0 až 3.5 a již pracuje s nejnovější bránu místní data.

* Obsah zprávy, které můžete odeslat ke svému serveru SAP, jako je například ukázkový soubor IDoc. Tento obsah musí být ve formátu XML a zahrnují obor názvů pro SAP akci, kterou chcete použít.

<a name="add-trigger"></a>

## <a name="add-http-request-trigger"></a>Přidat aktivační událost požadavku HTTP

V Azure Logic Apps, musí začínat každou aplikaci logiky [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts), která se stane, aktivuje se při určité události, nebo když je splněna určitá podmínka. Pokaždé, když aktivační událost je aktivována, modul Logic Apps vytvoří instanci aplikace logiky a spuštění pracovního postupu vaší aplikace.

V tomto příkladu vytvoříte aplikaci logiky s koncovým bodem v Azure tak, aby bylo možné odesílat *požadavky HTTP POST* do aplikace logiky. Pokud svou aplikaci logiky obdrží tyto požadavky HTTP, aktivační událost aktivuje a spustí další krok v pracovním postupu.

1. Na portálu Azure vytvořte prázdnou logiku aplikace, která otevře návrháře logiku aplikace. 

2. Do vyhledávacího pole zadejte "požadavek http" jako filtr. Ze seznamu aktivačních událostí, vyberte této aktivační události: **požadavku - obdrží žádost při HTTP**

   ![Přidat aktivační událost požadavku HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

3. Nyní uložte svou aplikaci logiky, takže můžete generovat adresu URL koncového bodu pro svou aplikaci logiky.
Na panelu nástrojů návrháře zvolte **Uložit**. 

   Koncový bod adresy URL se teď zobrazí v aktivační událost, například:

   ![Generování adresy URL pro koncový bod](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

## <a name="add-sap-action"></a>Přidat akci SAP

V Azure Logic Apps [akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je krok ve svém pracovním postupu, který následuje aktivační události nebo jiné akce. Pokud jste nepřidali aktivační událost pro svou aplikaci logiky ještě a chcete v tomto příkladu [přidat aktivační události popisované v této části](#add-trigger).

1. V návrháři aplikace logiky, v části aktivační událost, zvolte **nový krok** > **přidat akci**.

   ![Přidání akce](./media/logic-apps-using-sap-connector/add-action.png) 

2. Do vyhledávacího pole zadejte "serveru sap" jako filtr. Ze seznamu Akce vyberte akci pro váš server SAP: 

   * **Aplikace serveru SAP - odeslání pro SAP**
   * **Server zpráv SAP - odeslání pro SAP**

   Tento příklad používá tuto akci: **SAP aplikační Server - odeslání pro SAP**

   ![Vyberte "SAP aplikační Server" nebo "Serveru SAP zpráva"](media/logic-apps-using-sap-connector/select-sap-action.png)

3. Pokud se zobrazí výzva k podrobné informace o připojení, vytvořte připojením SAP. Jinak Pokud připojení již existuje, pokračujte dalším krokem, můžete nastavit akci SAP. 

   **Vytvořit místní SAP připojení**

   1. Pro **brány**, vyberte **připojit prostřednictvím místní brána dat** tak, aby vlastnostech připojení místní zobrazeny.

   2. Zadání informací o připojení pro váš server SAP. 
   Pro **brány** vlastnosti, vyberte bránu dat, který jste vytvořili na portálu Azure pro instalaci brány, například:

      **Aplikace serveru SAP**

      ![Vytvoření připojení k serveru aplikace SAP](./media/logic-apps-using-sap-connector/create-SAP-app-server-connection.png)  

      **Zpráva serveru SAP**

      ![Vytvoření připojení k serveru SAP zpráv](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png) 

   2. Jakmile budete hotoví, vyberte **Vytvořit**.

      Služba Logic Apps nastavuje a otestuje připojení, a ujistěte se, že připojení funguje správně.

4. Nyní najděte a vyberte akci, která z vašeho serveru SAP. 

   1. V **SAP akce** vyberte ikonu složky. 
   Ze seznamu složku najděte a vyberte akci, kterou chcete použít. 

      Tento příklad vybere **IDOC** kategorie pro akci IDoc. 

      ![Najděte a vyberte IDoc akce](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Pokud nemůžete najít požadovanou akci, lze zadat ručně cestu, například:

      ![Ručně zadat cestu k IDoc akce](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      Další informace o operacích IDoc najdete v tématu [zprávy schémata pro operace IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

   2. Klikněte na tlačítko uvnitř **zpráva při zadávání** pole tak, aby se zobrazí v seznamu dynamického obsahu. 
   V tomto seznamu, v části **obdrží žádost HTTP při**, vyberte **textu** pole. 

      Tento krok zahrnuje obsah textu z aktivační událost požadavku HTTP a odešle, výstup k serveru SAP.

      ![Vyberte pole "Body"](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Když jste hotovi, SAP akci vypadá v tomto příkladu:

      ![Dokončení akce SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

6. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

<a name="add-response"></a>

## <a name="add-http-response-action"></a>Přidat akce odpovědi HTTP

Nyní přidejte akce odpovědi svou aplikaci logiky pracovního postupu a zahrnují výstup z akce SAP. Tímto způsobem svou aplikaci logiky vrátí výsledky z vašeho serveru SAP do původní žadatele. 

1. V návrháři aplikace logiky v rámci akce SAP, zvolte **nový krok** > **přidat akci**.

2. Do vyhledávacího pole zadejte "odpověď" jako filtr. V seznamu akcí, vyberte tuto akci: **požadavku - odpověď**

3. Klikněte na tlačítko uvnitř **textu** pole tak, aby se zobrazí v seznamu dynamického obsahu. Z tohoto seznamu v části **poslat SAP**, vyberte **textu** pole. 

   ![Dokončení akce SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

4. Uložte svou aplikaci logiky. 

## <a name="test-your-logic-app"></a>Testování aplikace logiky

1. Pokud svou aplikaci logiky už není povolena, v nabídce aplikace logiky, vyberte **přehled**. Na panelu nástrojů vyberte **povolit**. 

2. Na panelu nástrojů návrháře aplikace logiky, vyberte **spustit**. Tento krok ručně spustí aplikaci logiky.

3. Aktivovat aplikaci logiky odesláním požadavku HTTP POST na adresu URL v aktivační událost požadavku HTTP a zahrnují zprávu s žádostí o obsahu. Odesílat žádosti, můžete použít nástroj, jako [Postman](https://www.getpostman.com/apps). 

   V tomto článku odešle žádost IDoc soubor, který musí být ve formátu XML a zahrnují obor názvů pro SAP akci, kterou používáte, například: 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//620/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. Po odeslání požadavku HTTP, čekejte na odpověď z aplikace logiky.

> [!NOTE]
> Aplikace logiky může vypršení časového limitu, pokud není v rámci dokončit všechny kroky potřebné k odpovědi [časový limit požadavku](./logic-apps-limits-and-config.md). Pokud k tomuto stavu dochází, může získat požadavky blokovány. Při diagnostice problémů, přečtěte si, jak [zkontrolujte a sledování funkce logic apps](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Blahopřejeme, nyní jste vytvořili aplikaci logiky, který může komunikovat se serverem SAP. Teď, když jste nastavili připojení k prostředí SAP pro svou aplikaci logiky, můžete si prostudovat další akce k dispozici SAP, jako je BAPI a RFC.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o konektoru, jak je popsáno konektory Swagger soubory naleznete v článcích odkaz: 

* [Aplikace serveru SAP](/connectors/sapapplicationserver/)
* [Zpráva serveru SAP](/connectors/sapmessageserver/)

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* [Připojení k místním systémům](../logic-apps/logic-apps-gateway-connection.md) z aplikace logiky
* Zjistěte, jak chcete ověřit, transformace a další operace zpráv s [Enterprise integračního balíčku](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
