---
title: Připojení k systémům SAP – Azure Logic Apps
description: Přístup a Správa prostředků SAP díky automatizaci pracovních postupů pomocí Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 05/09/2019
tags: connectors
ms.openlocfilehash: bccefec80ef3afd6d312bb1048d3be5d8e708728
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258151"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Připojení k systémům SAP v Azure Logic Apps

Tento článek popisuje, jak můžete přístup k prostředkům v místním SAP z uvnitř aplikace logiky s využitím konektoru SAP. Konektor funguje s klasickými verzemi systému SAP, jako jsou R/3 nebo systémy ECC, v místním prostředí. Konektor také umožňuje integraci s novější systémy SAP HANA založené SAP například s/4 HANA, bez ohledu na to jsou hostované – v místním prostředí nebo v cloudu. Konektor SAP podporuje integraci zpráv nebo dat do a z systémy SAP NetWeaver prostřednictvím zprostředkující dokumentu (IDoc) nebo obchodní aplikace programovací rozhraní (BAPI) nebo vzdálené volání funkce (RFC).

Konektor SAP používá [knihovny .NET konektoru SAP (NCo)](https://support.sap.com/en/product/connectors/msnet.html) a poskytuje tyto operace nebo akce:

* **Poslat SAP**: Odeslání IDoc tRFC, volání funkce BAPI přes RFC nebo volání RFC/tRFC systémů SAP vyhrazené.
* **Přijímat od SAPU**: Přijímat IDoc přes tRFC, volání funkce BAPI přes tRFC nebo volání RFC/tRFC systémů SAP.
* **Vygenerovat schémata**: Vygenerujte schémata pro SAP artefakty pro IDoc, BAPI nebo RFC.

Pro všechny výše uvedené operace konektor SAP podporuje základní ověřování prostřednictvím uživatelského jména a hesla. Tento konektor podporuje také [zabezpečené komunikace sítě (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true), které lze použít pro SAP NetWeaver Single Sign-On nebo pro zvýšení zabezpečení poskytované o produkt poskytovaný jako externí zabezpečení.

Konektor SAP se integruje s místními systémy SAP prostřednictvím [na místní bránu dat](../logic-apps/logic-apps-gateway-connection.md). Ve scénářích odeslat například při odesílání zprávy z aplikací logiky se systémem SAP brána dat funguje jako klient RFC a předává požadavky přijatými od aplikace logiky k SAP.
V případech Receive, brána dat funguje jako server RFC přijímá žádosti od SAPU a předává do aplikace logiky.

Tento článek ukazuje, jak vytvořit příklad aplikace logiky, která se integrují s řešením SAP při pokrývající dříve popsaná integrační scénáře.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle tohoto článku, budete potřebovat tyto položky:

* Předplatné Azure. Pokud nemáte ještě předplatné Azure [zaregistrovat si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky, ze kterého má přístup k systému SAP a aktivační události, která spustí pracovní postup aplikace logiky. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý start: Vytvořte svou první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Vaše [aplikační Server SAP](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) nebo [Server zpráv SAP](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)

* Stáhněte a nainstalujte nejnovější [na místní bránu dat](https://www.microsoft.com/download/details.aspx?id=53127) v libovolném v místním počítači. Ujistěte se, že nastavíte bránu na webu Azure Portal, než budete pokračovat. Brána umožňuje bezpečný přístup k místním datům a prostředkům. Další informace najdete v tématu [instalace místní brány dat pro Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

* Pokud používáte SNC se jednotné přihlašování (SSO), ujistěte se, že je brána spuštěná jako uživatel, který je namapovaný proti uživatelům SAP. Chcete-li změnit výchozí účet, vyberte **změnit účet**a zadejte přihlašovací údaje.

  ![Změna účtu brány](./media/logic-apps-using-sap-connector/gateway-account.png)

* Chcete-li povolit SNC se o produkt poskytovaný jako externí zabezpečení, zkopírujte knihovna SNC nebo soubory ve stejném počítači, kde je nainstalovaná brána. Některé příklady SNC produkty [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos, NTLM, a tak dále.

* Stáhněte a nainstalujte klientské knihovny nejnovější SAP, která je aktuálně [konektoru SAP (NCo) 3.0.21.0 pro rozhraní Microsoft .NET Framework 4.0 a Windows 64 bit (x64)](https://softwaredownloads.sap.com/file/0020000001865512018), ve stejném počítači jako místní brána dat. Instalace této verze nebo novější z těchto důvodů:

  * Starší verze SAP NCo může stát zablokována odeslání zprávy více než jeden IDoc ve stejnou dobu. Tato podmínka blokuje všechny novější zprávy, které se odesílají do cílového umístění SAP, způsobí zprávy do vypršení časového limitu.

  * Místní brána dat se spustí pouze v 64bitových systémech. V opačném případě dojde k chybě "Chybný image", protože hostitelská služba Brána dat nepodporuje 32bitová sestavení instalujte.

  * Hostitelská služba brány dat a SAP adaptér Microsoft pomocí rozhraní .NET Framework 4.5. NCo SAP pro rozhraní .NET Framework 4.0 funguje s procesy, které používají modul runtime rozhraní .NET 4.0 na 4.7.1. NCo SAP pro rozhraní .NET Framework 2.0 pracuje s procesy, které používají modul runtime rozhraní .NET 2.0 na 3.5 a už spolupracuje s nejnovější místní bránu dat.

* Obsah zprávy zasílané na server SAP, jako je například ukázkový soubor IDoc. Tento obsah musí být ve formátu XML a musí obsahovat obor názvů pro SAP akci, kterou chcete použít.

<a name="add-trigger"></a>

## <a name="send-to-sap"></a>Poslat SAP

Tento příklad používá aplikace logiky, který můžete aktivovat pomocí požadavku HTTP. Aplikace logiky odešle zprostředkující dokumentu (IDoc) na SAP server a vrátí odpověď žadatel, který volá aplikaci logiky. 

### <a name="add-http-request-trigger"></a>Přidat aktivační událost požadavek HTTP

V Azure Logic Apps, musí začínat každá aplikace logiky [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts), který se aktivuje při určité události nebo když je splněna konkrétní podmínka. Pokaždé, když se trigger aktivuje, modul Logic Apps vytvoří instanci aplikace logiky a spustí pracovní postup vaší aplikace.

V tomto příkladu vytvoříte aplikaci logiky s koncovým bodem v Azure tak, aby mohlo odesílat *požadavků HTTP POST* do aplikace logiky. Pokud vaše aplikace logiky obdrží tyto požadavky HTTP, aktivuje se a spustí další krok v pracovním postupu.

1. V [webu Azure portal](https://portal.azure.com), vytvoření prázdné aplikace logiky, otevře se návrhář aplikace logiky.

1. Do vyhledávacího pole zadejte jako filtr "požadavek http". Ze seznamu triggerů vyberte tento trigger: **Při přijetí požadavku HTTP**

   ![Přidat aktivační událost požadavek HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

1. Abyste mohli generovat adresu URL koncového bodu pro vaši aplikaci logiky uložte aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

   Koncový bod adresy URL se teď zobrazí v aktivační událost, například:

   ![Generování adresy URL pro koncový bod](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-sap-action"></a>Přidání akce SAP

V Azure Logic Apps [akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je krok v pracovním postupu, který následuje aktivační události nebo jiné akce. Pokud trigger aplikace logiky zatím nepřidali a chcete postupujte podle tohoto příkladu [přidat aktivační události popisované v této části](#add-trigger).

1. V návrháři aplikace logiky pod triggerem zvolte **nový krok**.

   ![Zvolte možnost "Nový krok"](./media/logic-apps-using-sap-connector/add-action.png)

1. Do vyhledávacího pole zadejte jako filtr "sap". Ze seznamu akcí vyberte tuto akci: **Odeslat zprávu pro SAP**
  
   ![Výběr akce Odeslat SAP](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Alternativně namísto hledání, zvolte **Enterprise** kartu a vyberte akci SAP.

   ![Výběr akce Odeslat SAP z karty Enterprise](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Pokud se zobrazí výzva k zadání podrobností o připojení, vytvoření připojení k SAP nyní. Jinak Pokud připojení již existuje, pokračujte dalším krokem, můžete nastavit akci SAP.

   **Vytvoření připojení místních SAP**

   1. Zadání informací o připojení k serveru SAP. Pro **bránu Data Gateway** vlastnosti, vyberte bránu dat, který jste vytvořili na webu Azure Portal pro vaši instalaci brány.

      Pokud **typ přihlášení** je nastavena na **aplikační Server**, tyto vlastnosti, které se obvykle zobrazují volitelné, jsou požadovány:

      ![Vytvoření připojení k serveru aplikace SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      Pokud **typ přihlášení** je nastavena na **skupiny**, tyto vlastnosti, které se obvykle zobrazují volitelné, jsou požadovány:

      ![Vytvoření připojení k serveru zpráv SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Ve výchozím nastavení silné typování slouží ke kontrole neplatné hodnoty pomocí provádí ověření XML proti schématu. Toto chování vám může pomoci odhalit problémy dříve. **Bezpečné psaní** možnost je k dispozici kvůli zpětné kompatibilitě a pouze kontroluje délku řetězce. Další informace o [ **bezpečné psaní** možnost](#safe-typing).

   1. Jakmile budete hotoví, vyberte **Vytvořit**.

      Logic Apps vytvoří a otestuje připojení, ujistěte se, že připojení funguje správně.

1. Nyní vyhledejte a vyberte akci ze serveru SAP.

   1. V **SAP akce** vyberte ikonu složky. Ze seznamu souborů vyhledejte a vyberte SAP zprávu, kterou chcete použít. Se orientovat v seznamu, použijte šipky.

      Tento příklad vybere IDoc s **objednávky** typu.

      ![Vyhledání a výběr akce IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Pokud nemůžete najít požadovanou akci, můžete ručně zadat cestu, například:

      ![Ručně zadat cestu k IDoc akce](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Zadejte hodnotu pro akci SAP pomocí editoru výrazů. Díky tomu můžete použít stejnou akci pro různé typy zpráv.

      Další informace o operacích IDoc najdete v tématu [zprávy schémata pro operace IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Klikněte do **vstupní zprávy** pole tak, aby zobrazil seznam dynamického obsahu. Z tohoto seznamu v části **přijetí požadavku HTTP když**, vyberte **tělo** pole.

      Tento krok zahrnuje obsah textu z triggeru požadavku HTTP a odesílá, jehož výstupem k serveru SAP.

      ![Vyberte pole "Body"](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Až budete hotovi, vaše akce SAP bude vypadat jako v tomto příkladu:

      ![Dokončení akce SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

<a name="add-response"></a>

### <a name="add-http-response-action"></a>Přidání akce odpovědi HTTP

Teď přidejte akci odpovědi do pracovního postupu aplikace logiky a zahrnutí výstupu z akce SAP. Tímto způsobem, vaše aplikace logiky vrátí výsledky z vašeho serveru SAP ho původnímu žadateli.

1. V návrháři aplikace logiky podle akce SAP, zvolte **nový krok**.

1. Do vyhledávacího pole zadejte jako filtr "odpověď". Ze seznamu akcí vyberte tuto akci: **Odpověď**

1. Klikněte do **tělo** pole tak, aby zobrazil seznam dynamického obsahu. Z tohoto seznamu v části **odešle zprávu SAP**, vyberte **tělo** pole.

   ![Dokončení akce SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Uložte svou aplikaci logiky.

### <a name="test-your-logic-app"></a>Otestujte aplikaci logiky

1. Pokud vaše aplikace logiky již není povolena, v nabídce aplikace logiky, vyberte **přehled**. Na panelu nástrojů zvolte **povolit**.

1. Na panelu nástrojů návrháře aplikace logiky zvolte **spustit**. Tímto krokem ručně spustíte svou aplikaci logiky.

1. Odesláním požadavku HTTP POST na adresu URL na váš trigger HTTP žádosti o aktivaci vaší aplikace logiky a zahrnout zprávy obsahu při zpracování požadavku. K odeslání požadavku můžete použít nástroj, jako [Postman](https://www.getpostman.com/apps).

   Pro účely tohoto článku odešle požadavek IDoc soubor, který musí být ve formátu XML a obsahoval obor názvů pro SAP akci, kterou používáte, třeba:

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. Po odeslání požadavku HTTP čekat na odpověď z vaší aplikace logiky.

   > [!NOTE]
   > Aplikace logiky může být vypršení časového limitu, pokud nedokončíte všechny kroky potřebné pro odpověď v rámci [časový limit požadavku](./logic-apps-limits-and-config.md). Pokud k tomuto stavu dochází, požadavky zablokoval. Při diagnostice problémů, přečtěte si, jak [zkontrolujte a monitorování aplikací logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Blahopřejeme, právě jste vytvořili aplikaci logiky, který může komunikovat se serverem SAP. Teď, když nastavíte připojení k SAP pro aplikace logiky, můžete prozkoumat další dostupné akce SAP, jako je například BAPI a v dokumentu RFC.

## <a name="receive-from-sap"></a>Přijímat od SAPU.

Tento příklad používá aplikace logiky, která se aktivuje při přijetí zprávy z určitého systému SAP.

### <a name="add-sap-trigger"></a>Přidání triggeru SAP

1. Na webu Azure Portal vytvoření prázdné aplikace logiky, otevře se návrhář aplikace logiky.

1. Do vyhledávacího pole zadejte jako filtr "sap". Ze seznamu triggerů vyberte tento trigger: **Při doručení zprávy od SAPU.**

   ![Přidání triggeru SAP](./media/logic-apps-using-sap-connector/add-sap-trigger.png)

   Alternativně můžete přejít na **Enterprise** kartu a vyberte trigger:

   ![Přidání triggeru SAP z karty Enterprise](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Pokud se zobrazí výzva k zadání podrobností o připojení, vytvoření připojení k SAP nyní. Jinak Pokud připojení již existuje, pokračujte dalším krokem, můžete nastavit akci SAP.

   **Vytvoření připojení místních SAP**

   1. Zadání informací o připojení k serveru SAP. Pro **bránu Data Gateway** vlastnosti, vyberte bránu dat, který jste vytvořili na webu Azure Portal pro vaši instalaci brány.

      Pokud **typ přihlášení** je nastavena na **aplikační Server**, tyto vlastnosti, které se obvykle zobrazují volitelné, jsou požadovány:

      ![Vytvoření připojení k serveru aplikace SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      Pokud **typ přihlášení** je nastavena na **skupiny**, tyto vlastnosti, které se obvykle zobrazují volitelné, jsou požadovány:

      ![Vytvoření připojení k serveru zpráv SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Ve výchozím nastavení silné typování slouží ke kontrole neplatné hodnoty pomocí provádí ověření XML proti schématu. Toto chování vám může pomoci odhalit problémy dříve. **Bezpečné psaní** možnost je k dispozici kvůli zpětné kompatibilitě a pouze kontroluje délku řetězce. Další informace o [ **bezpečné psaní** možnost](#safe-typing).

1. Zadejte požadované parametry, které jsou založené na konfiguraci systému SAP.

   Volitelně můžete zadat jednu nebo více akcí SAP. Tento seznam akcí Určuje zprávy, které aktivační událost přijme ze serveru SAP prostřednictvím brány data. Prázdný seznam určuje, že aktivační událost přijímá všechny zprávy. Pokud seznam obsahuje více než jedna zpráva, obdrží aktivační událost pouze zprávy uvedený v seznamu. Všechny ostatní zprávy odeslané ze serveru SAP odmítne brána.

   Můžete vybrat akci SAP z nástroje pro výběr souborů:

   ![Vyberte akci SAP](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Nebo můžete ručně zadat akce:

   ![Ručně zadejte akce SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png) 

   Tady je příklad, který ukazuje, jak se zobrazí akce, když nastavíte aktivační událost pro příjem zprávy více než jeden.

   ![Například aktivační událost](media/logic-apps-using-sap-connector/example-trigger.png)  

   Další informace o akci SAP najdete v tématu [zprávy schémata pro operace IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. Nyní uložení aplikace logiky, takže můžete začít přijímat zprávy ze systému SAP.
Na panelu nástrojů návrháře zvolte **Uložit**.

Aplikace logiky je teď připravena přijímat zprávy ze systému SAP.

> [!NOTE]
> Aktivační událost SAP není cyklického dotazování aktivační událost, ale aktivační událost založené na webhoocích místo. Aktivační události je volána z brány pouze v případě, že zpráva existuje, tak žádné cyklického dotazování je nezbytné.

### <a name="test-your-logic-app"></a>Otestujte aplikaci logiky

1. Spustit aplikaci logiky, odešlete zprávu ze systému SAP.

1. V nabídce aplikace logiky zvolte **přehled**a projděte si **historie běhů** pro všechny nové běhy pro vaši aplikaci logiky.

1. Otevřete posledního spuštění, který zobrazuje zpráva odeslaná z vašeho systému SAP v část Outputs následujícím aktivační události.

## <a name="generate-schemas-for-artifacts-in-sap"></a>Vygenerovat schémata pro artefakty v SAP

Tento příklad používá aplikace logiky, který můžete aktivovat pomocí požadavku HTTP. Akce SAP odešle požadavek do systému SAP se vygenerovat schémata pro zadaný dokument zprostředkující (IDoc) a BAPI. Schémata, která vrátit v odpovědi se nahrají do účtu pro integraci s použitím konektoru Azure Resource Manageru.

### <a name="add-http-request-trigger"></a>Přidat aktivační událost požadavek HTTP

1. Na webu Azure Portal vytvoření prázdné aplikace logiky, otevře se návrhář aplikace logiky.

1. Do vyhledávacího pole zadejte jako filtr "požadavek http". Ze seznamu triggerů vyberte tento trigger: **Při přijetí požadavku HTTP**

   ![Přidat aktivační událost požadavek HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

1. Abyste mohli generovat adresu URL koncového bodu pro vaši aplikaci logiky uložte aplikaci logiky.
Na panelu nástrojů návrháře zvolte **Uložit**.

   Koncový bod adresy URL se teď zobrazí v aktivační událost, například:

   ![Generování adresy URL pro koncový bod](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-sap-action-to-generate-schemas"></a>Přidání akce SAP mají vygenerovat schémata

1. V návrháři aplikace logiky pod triggerem zvolte **nový krok**.

   ![Zvolte možnost "Nový krok"](./media/logic-apps-using-sap-connector/add-action.png)

1. Do vyhledávacího pole zadejte jako filtr "sap". Ze seznamu akcí vyberte tuto akci: **Vygenerovat schémata**
  
   ![Výběr akce Odeslat SAP](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Alternativně můžete také **Enterprise** kartu a vyberte akci SAP.

   ![Výběr akce Odeslat SAP z karty Enterprise](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Pokud se zobrazí výzva k zadání podrobností o připojení, vytvoření připojení k SAP nyní. Jinak Pokud připojení již existuje, pokračujte dalším krokem, můžete nastavit akci SAP.

   **Vytvoření připojení místních SAP**

   1. Zadání informací o připojení k serveru SAP. Pro **bránu Data Gateway** vlastnosti, vyberte bránu dat, který jste vytvořili na webu Azure Portal pro vaši instalaci brány.

      Pokud **typ přihlášení** je nastavena na **aplikační Server**, tyto vlastnosti, které se obvykle zobrazují volitelné, jsou požadovány:

      ![Vytvoření připojení k serveru aplikace SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      Pokud **typ přihlášení** je nastavena na **skupiny**, tyto vlastnosti, které se obvykle zobrazují volitelné, jsou požadovány:

      ![Vytvoření připojení k serveru zpráv SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Ve výchozím nastavení silné typování slouží ke kontrole neplatné hodnoty pomocí provádí ověření XML proti schématu. Toto chování vám může pomoci odhalit problémy dříve. **Bezpečné psaní** možnost je k dispozici kvůli zpětné kompatibilitě a pouze kontroluje délku řetězce. Další informace o [ **bezpečné psaní** možnost](#safe-typing).

   1. Jakmile budete hotoví, vyberte **Vytvořit**. 
   
      Logic Apps vytvoří a otestuje připojení, ujistěte se, že připojení funguje správně.

1. Zadejte cestu na artefakt, pro kterou chcete vygenerovat schéma.

   SAP akce můžete vybrat z nástroje pro výběr souborů:

   ![Vyberte akci SAP](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Nebo můžete ručně zadat akce:

   ![Ručně zadejte akce SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   K vygenerování schématu pro více než jeden artefakt, zadejte podrobnosti akce SAP pro každý artefakt, například:

   ![Vyberte Přidat novou položku](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Zobrazit dvě položky](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Další informace o akci SAP najdete v tématu [zprávy schémata pro operace IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

### <a name="test-your-logic-app"></a>Otestujte aplikaci logiky

1. Na panelu nástrojů návrháře zvolte **spustit** aktivuje spuštění aplikace logiky.

1. Otevřete spustit a zkontrolovat výstupy pro **vygenerovat schémata** akce.

   Zobrazit výstupy generované schémata pro zadaný seznam zpráv.

### <a name="upload-schemas-to-integration-account"></a>Nahrajte schémata do účtu pro integraci

Volitelně můžete stáhnout nebo uložit vygenerovaný schémata do úložiště, včetně objektů blob, úložiště nebo účet pro integraci. Účty pro integraci poskytovat prvotřídní prostředí s jinými akcemi XML, tak tento příklad ukazuje, jak nahrát schémata do účtu pro integraci pro stejnou aplikaci logiky s využitím konektoru Azure Resource Manageru.

1. V návrháři aplikace logiky, pod triggerem zvolte **nový krok**.

1. Do vyhledávacího pole zadejte jako filtr "Resource Manager". Vyberte tuto akci: **Vytvořit nebo aktualizovat prostředek**

   ![Zvolte akci Azure Resource Manageru](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Zadejte podrobnosti pro akci, včetně předplatného Azure, skupinu prostředků Azure a účet pro integraci. Chcete-li přidat tokeny SAP do polí, klikněte do pole pro tato pole a vyberte ze seznamu dynamického obsahu, který se zobrazí.

   1. Otevřít **přidat nový parametr** seznam a vyberte **umístění** a **vlastnosti** pole.

   1. Zadejte podrobnosti pro tato nová pole, jak je znázorněno v tomto příkladu.

      ![Zadejte podrobnosti pro akci Azure Resource Manageru](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   SAP **vygenerovat schémata** akce generuje schémat jako kolekce, takže návrhář automaticky přidá **pro každou** smyčky na akci. Tady je příklad, který ukazuje, jak se zobrazí tato akce:

   ![Azure Resource Manageru akce s "pro každý" smyčka](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)  

   > [!NOTE]
   > Schémata pomocí formátu s kódováním base64. K nahrání schémata do účtu pro integraci, se musí dekódováno pomocí `base64ToString()` funkce. Tady je příklad, který se zobrazí kód `"properties"` element:
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

### <a name="test-your-logic-app"></a>Otestujte aplikaci logiky

1. Na panelu nástrojů návrháře zvolte **spustit** k ruční aktivaci aplikace logiky.

1. Po úspěšném spuštění, přejděte na účet pro integraci a zkontrolujte, že existují generované schémata.

## <a name="enable-secure-network-communications-snc"></a>Povolení zabezpečené síťové komunikace (SNC)

Než začnete, ujistěte se, že splnění výše uvedených [požadavky](#pre-reqs):

* Místní brána dat je nainstalován na počítači, který je ve stejné síti jako systém SAP.

* Pro jednotné přihlašování, brána běží jako uživatel, který je namapovaný na uživatel systému SAP.

* Knihovna SNC, který poskytuje funkce pro zvýšení zabezpečení byl nainstalován na stejném počítači jako bránu data gateway. Mezi příklady těchto patří [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos, NTLM, a tak dále.

Chcete-li SNC pro vaše požadavky na nebo z systému SAP, vyberte **SNC použití** zaškrtávacího políčka v připojení k SAP a zadejte tyto vlastnosti:

   ![Konfigurace SAP SNC v připojení](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Vlastnost | Popis |
   |----------| ------------|
   | **Knihovna SNC** | Název knihovny SNC nebo relativní umístění instalace NCo cestu nebo absolutní cesta. Například `sapsnc.dll` nebo `.\security\sapsnc.dll` nebo `c:\security\sapsnc.dll` |
   | **SNC SSO** | Při připojování přes SNC SNC identity se obvykle používá pro ověřování volající. Další možností je přepsat tak, aby informace o uživateli a hesla je možné za účelem ověřování totožnosti volajícímu, ale řádku je šifrovaný. |
   | **SNC moje jméno** | Ve většině případů lze tuto vlastnost vynechat. Nainstalované řešení SNC obvykle ví názvu SNC. Pouze pro řešení, podpora "více identit" budete možná muset zadat identitě, kterou chcete použít pro tento konkrétní cíl nebo serveru. |
   | **Název partnera SNC** | Název back-endu SNC |
   | **SNC kvality ochrany** | Kvalita služby pro komunikaci SNC tento konkrétní cíl/serveru. Výchozí hodnota je definována v back-end systému. Maximální hodnota je definována pro SNC zabezpečení produktu. |
   |||

   > [!NOTE]
   > Proměnné prostředí SNC_LIB a SNC_LIB_64 neměla být nastavena na počítači, kde máte brány dat a knihovna SNC. Pokud nastavit, že by přednost knihovna SNC hodnoty předané prostřednictvím konektoru.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Bezpečné psaní

Ve výchozím nastavení při vytváření připojení k SAP silné typování slouží ke kontrole neplatné hodnoty pomocí provádí ověření XML proti schématu. Toto chování vám může pomoci odhalit problémy dříve. **Bezpečné psaní** možnost je k dispozici kvůli zpětné kompatibilitě a pouze kontroluje délku řetězce. Pokud se rozhodnete **bezpečné psaní**, typ dat a typ TIMS v SAP jsou zpracovávány jako řetězce, nikoli jako jejich ekvivalenty XML `xs:date` a `xs:time` kde `xmlns:xs="http://www.w3.org/2001/XMLSchema"`. Bezpečné psaní má vliv na chování pro všechny generování schématu, posílání zpráv pro datovou část "byl odeslán" a "byla přijata" odpověď i aktivační událost. 

Při použití silné typování (**bezpečné psaní** nepovoleno), schématu typy dat a TIMS mapuje na jednodušší typy XML:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Při odesílání zpráv pomocí silných typů dat a TIMS odpovědi splňuje odpovídající typ formátu XML:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Když **bezpečné psaní** je povoleno, schéma mapuje definice virů a TIMS typy XML například pole s pouze omezení délky řetězce:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

Při odesílání zpráv s **bezpečné psaní** povolena, dat a TIMS odpověď vypadá jako v tomto příkladu:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```


## <a name="known-issues-and-limitations"></a>Známé problémy a omezení

Tady jsou aktuálně známé problémy a omezení pro konektor SAP:

* Pouze jeden odeslat na SAP volání nebo zprávy funguje s tRFC. Vzor potvrzení obchodní aplikace programovací rozhraní (BAPI), jako je například volání více tRFC ve stejné relaci, se nepodporuje.

* Aktivační událost SAP nepodporuje přijímání batch Idoc ze SAP. Tato akce může způsobit RFC selhání připojení mezi systému SAP a bránou data gateway.

* Aktivační událost SAP nepodporuje data clusterů bran. V některých případech převzetí služeb při selhání uzel brány dat, který komunikuje se systémem SAP mohou lišit od aktivního uzlu, což vede k neočekávanému chování. Clustery bran s data odeslat scénáře jsou podporovány.

* Konektor SAP v současné době nepodporuje řetězce směrovačů SAP. Místní brána dat, musí existovat ve stejné síti jako systém SAP, kterou chcete připojit.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o omezení, akce a triggery, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, zkontrolujte [konektoru referenční stránce](/connectors/sap/).

## <a name="next-steps"></a>Další postup

* [Připojení k místním systémům](../logic-apps/logic-apps-gateway-connection.md) z aplikací logiky
* Zjistěte, jak ověřit, transformaci a další operace zprávu s [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
