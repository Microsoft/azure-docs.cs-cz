---
title: Připojení k systémům SAP
description: Přístup k prostředkům SAP a správa díky automatizaci pracovních postupů pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/30/2019
tags: connectors
ms.openlocfilehash: 39ab222f64d964e95b16e043c9cdeccd8170ace3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651011"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Připojení k systémům SAP z Azure Logic Apps

> [!IMPORTANT]
> 29. února 2020 se zastaraly dřívější konektory sap aplikačního serveru a serveru zpráv SAP. Aktuální konektor SAP konsoliduje tyto předchozí konektory SAP, takže nemusíte měnit typ připojení, je plně kompatibilní s předchozími konektory, poskytuje mnoho dalších funkcí a nadále používá knihovnu konektorů SAP .Net ( SAP NCo).
>
> Pro aplikace logiky, které používají starší konektory, [migrujte na nejnovější konektor](#migrate) před datem vyřazení. V opačném případě tyto aplikace logiky dojde k selhání spuštění a nebude moci odesílat zprávy do systému SAP.

Tento článek ukazuje, jak můžete přistupovat k místním prostředkům SAP z aplikace logiky pomocí konektoru SAP. Konektor pracuje s klasickými verzemi SAP, jako jsou systémy R/3 a ECC v místním prostředí. Konektor také umožňuje integraci s novějšími systémy SAP založené na HANA, jako je S/4 HANA, ať už jsou hostované místně nebo v cloudu. Konektor SAP podporuje integraci zpráv nebo dat do a ze systémů založených na SAP NetWeaver prostřednictvím zprostředkujícího dokumentu (IDoc), rozhraní BAPI (Business Application Programming Interface) nebo vzdáleného volání funkcí (RFC).

Konektor SAP používá [knihovnu KONEKTORSAP .NET (NCo)](https://support.sap.com/en/product/connectors/msnet.html) a poskytuje tyto akce:

* **Odeslat zprávu do SAP**: Odeslat IDoc přes tRFC, volat bapi funkce přes RFC, nebo volejte RFC/tRFC v systémech SAP.
* **Když je zpráva přijata z SAP**: Příjem IDoc přes tRFC, volání bapi funkce přes tRFC, nebo volání RFC/tRFC v systémech SAP.
* **Generovat schémata**: Generovat schémata pro artefakty SAP pro IDoc, BAPI nebo RFC.

Pro tyto operace konektor SAP podporuje základní ověřování prostřednictvím uživatelských jmen a hesel. Konektor také podporuje [secure network communications (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). SNC lze použít pro sap netweaver jednotné přihlašování (SSO) nebo pro další možnosti zabezpečení poskytované externím bezpečnostním produktem.

Konektor SAP se integruje s místními systémy SAP prostřednictvím [místní brány dat](../logic-apps/logic-apps-gateway-connection.md). V odesílání scénáře, například při odeslání zprávy z aplikace logiky do systému SAP, brána dat funguje jako klient RFC a předá požadavky přijaté z aplikace logiky sap. Podobně v případě příjmu brána dat funguje jako server RFC, který přijímá požadavky ze sap a předává je do aplikace logiky.

Tento článek ukazuje, jak vytvořit ukázkové aplikace logiky, které se integrují se SAP a zároveň pokrývají dříve popsané scénáře integrace. Pro aplikace logiky, které používají starší konektory SAP, tento článek ukazuje, jak migrovat aplikace logiky na nejnovější konektor SAP.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat spolu s tímto článkem, potřebujete tyto položky:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky, odkud chcete získat přístup k systému SAP a aktivační událost, která spustí pracovní postup aplikace logiky. Pokud s aplikacemi logiky teprve začínáte, přečtěte si informace o [tématech Co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md) a [Úvodní příručka: Vytvořte první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Aplikační [server SAP](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) nebo server zpráv [SAP](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).

* Stáhněte a nainstalujte nejnovější [místní bránu dat](https://www.microsoft.com/download/details.aspx?id=53127) do libovolného místního počítače. Než budete pokračovat, ujistěte se, že jste bránu nastavili na webu Azure Portal. Brána vám pomůže bezpečně přistupovat k místním datům a prostředkům. Další informace najdete [v tématu Instalace místní brány dat pro Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

* Pokud používáte SNC s jednotného přihlašování, ujistěte se, že brána běží jako uživatel, který je mapován proti uživateli SAP. Chcete-li změnit výchozí účet, vyberte **možnost Změnit účet**a zadejte pověření uživatele.

  ![Změna účtu brány](./media/logic-apps-using-sap-connector/gateway-account.png)

* Pokud povolíte SNC s externím bezpečnostním produktem, zkopírujte knihovnu SNC nebo soubory ve stejném počítači, ve kterém je brána nainstalována. Některé příklady produktů SNC patří [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos a NTLM.

* Stáhněte a nainstalujte nejnovější klientskou knihovnu SAP, která je aktuálně [konektorem SAP Connector (NCo 3.0) pro microsoft .NET 3.0.22.0 zkompilovaný s rozhraním .NET Framework 4.0 - Windows 64-bit (x64)](https://softwaredownloads.sap.com/file/0020000001000932019), do stejného počítače jako místní brána dat. Nainstalujte tuto verzi nebo novější z těchto důvodů:

  * Starší verze SAP NCo může uvíznout ve slepé uličce při odeslání více než jednu zprávu IDoc současně. Tato podmínka blokuje všechny pozdější zprávy, které jsou odesílány do cíle SAP, což způsobí, že zprávy časový mat.
  
  * Místní brána dat běží pouze v 64bitových systémech. V opačném případě se zobrazí chyba "chybný obrázek", protože hostitelská služba brány dat nepodporuje 32bitová sestavení.
  
  * Hostitelská služba brány dat i adaptér Microsoft SAP používají rozhraní .NET Framework 4.5. Sap NCo pro rozhraní .NET Framework 4.0 pracuje s procesy, které používají běh .NET 4.0 až 4.7.1. SAP NCo pro rozhraní .NET Framework 2.0 pracuje s procesy, které používají nosné runtime .NET 2.0 až 3.5, ale už nepracují s nejnovější místní bránou dat.

* Obsah zprávy, který můžete odeslat na server SAP, například ukázkový soubor IDoc, musí být ve formátu XML a musí obsahovat obor názvů pro akci SAP, kterou chcete použít.

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>Migrace na aktuální spojnici

1. Pokud jste tak ještě neučinili, aktualizujte [místní bránu dat](https://www.microsoft.com/download/details.aspx?id=53127) tak, abyste měli nejnovější verzi. Další informace najdete [v tématu Instalace místní brány dat pro Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

1. V aplikaci logiky, která používá starší konektor SAP, odstraňte akci **Odeslat do SAP.**

1. Z nejnovějšího konektoru SAP přidejte **akci Odeslat zprávu do SAP.** Před použitím této akce znovu vytvořte připojení k systému SAP.

1. Až budete hotovi, uložte aplikaci logiky.

<a name="add-trigger"></a>

## <a name="send-message-to-sap"></a>Odeslat zprávu do SAP

Tento příklad používá aplikaci logiky, kterou můžete aktivovat pomocí požadavku HTTP. Aplikace logiky odešle IDoc na server SAP a vrátí odpověď žadateli, který volal aplikaci logiky.

### <a name="add-an-http-request-trigger"></a>Přidání aktivační události požadavku HTTP

V Azure Logic Apps musí každá aplikace logiky začínat [aktivační událostí](../logic-apps/logic-apps-overview.md#logic-app-concepts), která se aktivuje, když dojde k určité události nebo když je splněna určitá podmínka. Pokaždé, když se spustí aktivační událost, modul Logic Apps vytvoří instanci aplikace logiky a spustí pracovní postup aplikace.

V tomto příkladu vytvoříte aplikaci logiky s koncovým bodem v Azure, takže můžete odesílat požadavky HTTP POST do aplikace *logiky.* Když vaše aplikace logiky obdrží tyto požadavky HTTP, aktivační událost se spustí a spustí další krok v pracovním postupu.

1. Na [webu Azure Portal](https://portal.azure.com)vytvořte prázdnou aplikaci logiky, která otevře Návrhář aplikace logiky.

1. Do vyhledávacího pole `http request` zadejte jako filtr. V seznamu **Aktivační události** vyberte při přijetí **požadavku HTTP**.

   ![Přidat aktivační událost požadavku HTTP](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Teď uložte aplikaci logiky, abyste mohli vygenerovat adresu URL koncového bodu pro aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

   Adresa URL koncového bodu se nyní zobrazí v aktivační události, například:

   ![Generovat adresu URL pro koncový bod](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>Přidání akce SAP

V Azure Logic Apps [je akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) krokem ve vašem pracovním postupu, který následuje po aktivační události nebo jiné akci. Pokud jste ještě nepřidali aktivační událost do aplikace logiky a chcete postupovat podle tohoto [příkladu, přidejte aktivační událost popsanou v této části](#add-trigger).

1. V Návrháři aplikace logiky vyberte pod aktivační událostí **nový krok**.

   ![Přidání nového kroku do aplikace logiky](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Do vyhledávacího pole `sap` zadejte jako filtr. V seznamu **Akce** vyberte **Odeslat zprávu do SYSTÉMU SAP**.
  
   ![Vyberte akci "Odeslat zprávu do SAP".](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Nebo můžete vybrat kartu **Enterprise** a vybrat akci SAP.

   ![Výběr akce Odeslat zprávu do SAP na kartě Enterprise](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Pokud vaše připojení již existuje, pokračujte dalším krokem, abyste mohli nastavit akci SAP. Pokud se však zobrazí výzva k zadání podrobností o připojení, zadejte informace, abyste mohli nyní vytvořit připojení k místnímu serveru SAP.

   1. Zadejte název připojení.

   1. V části **Brána dat** v části **Předplatné**nejprve vyberte předplatné Azure pro prostředek brány, který jste vytvořili na webu Azure Portal pro instalaci brány. 
   
   1. V části **Brána připojení**vyberte prostředek brány.

   1. Pokračujte v poskytování informací o připojení. U vlastnosti **Typ přihlášení** postupujte podle kroku podle toho, zda je vlastnost nastavena na **aplikační server** nebo **skupinu**:
   
      * Pro **aplikační server**jsou tyto vlastnosti, které se obvykle zobrazují jako volitelné, povinné:

        ![Vytvoření připojení aplikačního serveru SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Pro **skupinu**jsou tyto vlastnosti, které se obvykle zobrazují jako volitelné, povinné:

        ![Vytvořit připojení serveru zpráv SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Ve výchozím nastavení se silné psaní používá ke kontrole neplatných hodnot provedením ověření XML proti schématu. Toto chování vám může pomoci zjistit problémy dříve. Možnost **Bezpečné psaní** je k dispozici pro zpětnou kompatibilitu a kontroluje pouze délku řetězce. Přečtěte si další informace o [možnosti Bezpečné psaní](#safe-typing).

   1. Až budete hotovi, vyberte **Vytvořit**.

      Logic Apps nastaví a otestuje připojení a ujistěte se, že připojení funguje správně.

1. Nyní vyhledejte a vyberte akci ze serveru SAP.

   1. V poli **Akce SAP** vyberte ikonu složky. Ze seznamu souborů vyhledejte a vyberte zprávu SAP, kterou chcete použít. Chcete-li procházet seznam, použijte šipky.

      Tento příklad vybere IDoc s typem **Objednávky.**

      ![Hledání a výběr akce IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Pokud nemůžete najít požadovanou akci, můžete ručně zadat cestu, například:

      ![Ručně poskytnout cestu k akci IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Zadejte hodnotu pro **akci SAP** prostřednictvím editoru výrazů. Tímto způsobem můžete použít stejnou akci pro různé typy zpráv.

      Další informace o operacích IDoc naleznete [v tématu Schémata zpráv pro operace IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Klikněte do pole **Vstupní zpráva,** aby se zobrazil seznam dynamického obsahu. V tomto seznamu včásti **Při přijetí požadavku HTTP**vyberte pole **Tělo.**

      Tento krok zahrnuje obsah těla z aktivační události požadavku HTTP a odešle tento výstup na server SAP.

      ![Vyberte vlastnost "Tělo" z aktivační události](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Po dokončení vypadá vaše akce SAP tak, že:

      ![Dokončení akce SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Přidání akce odpovědi HTTP

Teď přidejte akci odpovědi do pracovního postupu aplikace logiky a zahrňte výstup z akce SAP. Tímto způsobem aplikace logiky vrátí výsledky ze serveru SAP původnímu žadateli.

1. V Návrháři aplikace logiky vyberte v akci SAP **nový krok**.

1. Do vyhledávacího pole `response` zadejte jako filtr. V seznamu **Akce** vyberte **odpověď**.

1. Klikněte do pole **Tělo,** aby se zobrazil seznam dynamického obsahu. V tomto seznamu vyberte v části **Odeslat zprávu do systému SAP**pole **Tělo.**

   ![Dokončit akci SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Uložte svou aplikaci logiky.

### <a name="test-your-logic-app"></a>Testování aplikace logiky

1. Pokud vaše aplikace logiky ještě není povolená, v nabídce aplikace logiky vyberte **Přehled**. Na panelu nástrojů vyberte **Povolit**.

1. Na panelu nástrojů návrháře vyberte **Spustit**. Tento krok ručně spustí aplikaci logiky.

1. Aktivujte aplikaci logiky odesláním požadavku HTTP POST na adresu URL v aktivační události požadavku HTTP.
Zahrňte obsah zprávy do svého požadavku. Chcete-li odeslat požadavek, můžete použít nástroj, jako je [Pošťák](https://www.getpostman.com/apps).

   V tomto článku požadavek odešle soubor IDoc, který musí být ve formátu XML a musí obsahovat obor názvů pro akci SAP, kterou používáte, například:

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. Po odeslání požadavku HTTP počkejte na odpověď z aplikace logiky.

   > [!NOTE]
   > Váš aplikace logiky může časový limit, pokud všechny kroky potřebné pro odpověď nekončí v rámci [limitu časového limitu požadavku](./logic-apps-limits-and-config.md). Pokud k této podmínce dojde, mohou být požadavky blokovány. Chcete-li diagnostikovat problémy, přečtěte si, jak můžete [kontrolovat a monitorovat aplikace logiky](../logic-apps/monitor-logic-apps.md).

Nyní jste vytvořili aplikaci logiky, která může komunikovat se serverem SAP. Teď, když jste nastavili připojení SAP pro aplikaci logiky, můžete prozkoumat další dostupné akce SAP, jako je NAPŘÍKLAD BAPI a RFC.

<a name="receive-from-sap"></a>

## <a name="receive-message-from-sap"></a>Přijímat zprávy ze systému SAP

Tento příklad používá aplikaci logiky, která se aktivuje, když aplikace obdrží zprávu ze systému SAP.

### <a name="add-an-sap-trigger"></a>Přidání aktivační události SAP

1. Na webu Azure Portal vytvořte prázdnou aplikaci logiky, která otevře Návrhář aplikace logiky.

1. Do vyhledávacího pole `sap` zadejte jako filtr. V seznamu **Aktivační události** vyberte při přijetí zprávy **ze systému SAP**.

   ![Přidání aktivační události SAP](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   Nebo můžete vybrat kartu **Organizace** a pak vybrat aktivační událost:

   ![Přidání aktivační události SAP z karty Enterprise](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Pokud vaše připojení již existuje, pokračujte dalším krokem, abyste mohli nastavit akci SAP. Pokud se však zobrazí výzva k zadání podrobností o připojení, zadejte informace, abyste mohli nyní vytvořit připojení k místnímu serveru SAP.

   1. Zadejte název připojení.

   1. V části **Brána dat** v části **Předplatné**nejprve vyberte předplatné Azure pro prostředek brány, který jste vytvořili na webu Azure Portal pro instalaci brány. 

   1. V části **Brána připojení**vyberte prostředek brány.

   1. Pokračujte v poskytování informací o připojení. U vlastnosti **Typ přihlášení** postupujte podle kroku podle toho, zda je vlastnost nastavena na **aplikační server** nebo **skupinu**:

      * Pro **aplikační server**jsou tyto vlastnosti, které se obvykle zobrazují jako volitelné, povinné:

        ![Vytvoření připojení aplikačního serveru SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Pro **skupinu**jsou tyto vlastnosti, které se obvykle zobrazují jako volitelné, povinné:

        ![Vytvořit připojení serveru zpráv SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Ve výchozím nastavení se silné psaní používá ke kontrole neplatných hodnot provedením ověření XML proti schématu. Toto chování vám může pomoci zjistit problémy dříve. Možnost **Bezpečné psaní** je k dispozici pro zpětnou kompatibilitu a kontroluje pouze délku řetězce. Přečtěte si další informace o [možnosti Bezpečné psaní](#safe-typing).

   1. Až budete hotovi, vyberte **Vytvořit**.

      Logic Apps nastaví a otestuje připojení a ujistěte se, že připojení funguje správně.

1. Zadejte požadované parametry na základě konfigurace systému SAP.

   Volitelně můžete zadat jednu nebo více akcí SAP. Tento seznam akcí určuje zprávy, které aktivační událost přijímá ze serveru SAP prostřednictvím brány dat. Prázdný seznam určuje, že aktivační událost přijímá všechny zprávy. Pokud seznam obsahuje více než jednu zprávu, aktivační událost obdrží pouze zprávy zadané v seznamu. Všechny ostatní zprávy odeslané ze serveru SAP jsou bránou odmítnuty.

   Akci SAP můžete vybrat z výběru souborů:

   ![Přidání akce SAP do aplikace logiky](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Nebo můžete ručně zadat akci:

   ![Ruční zadání akce SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Tady je příklad, který ukazuje, jak se akce zobrazí, když nastavíte aktivační událost tak, aby přijímali více než jednu zprávu.

   ![Příklad aktivační události, který přijímá více zpráv](media/logic-apps-using-sap-connector/example-trigger.png)

   Další informace o akci SAP naleznete v tématu [Schémata zpráv pro operace IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. Nyní uložte aplikaci logiky, abyste mohli začít přijímat zprávy ze systému SAP. Na panelu nástrojů návrháře vyberte **Uložit**.

Vaše aplikace logiky je nyní připravena přijímat zprávy ze systému SAP.

> [!NOTE]
> Aktivační událost SAP není aktivační událost dotazování, ale místo toho je aktivační událost založená na webhooku. Aktivační událost je volána z brány pouze v případě, že existuje zpráva, takže není nutné dotazování.

### <a name="test-your-logic-app"></a>Testování aplikace logiky

1. Chcete-li spustit aplikaci logiky, odešlete zprávu ze systému SAP.

1. V nabídce aplikace logiky vyberte **Přehled**. Zkontrolujte **historii spuštění** pro všechny nové spuštění pro aplikaci logiky.

1. Otevřete poslední spuštění, které zobrazuje zprávu odeslanou ze systému SAP v části spouštěcí výstupy.

## <a name="receive-idoc-packets-from-sap"></a>Příjem paketů IDOC ze systému SAP

Sap můžete nastavit tak, aby [odesílá IDOC v paketech](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/en-US/4ab38886549a6d8ce10000000a42189c.html), což jsou dávky nebo skupiny IDOC. Chcete-li přijímat pakety IDOC, konektor SAP a konkrétně aktivační událost, nepotřebuje další konfiguraci. Ke zpracování každé položky v paketu IDOC po přijetí aktivační události jsou však nutné některé další kroky k rozdělení paketu na jednotlivé idocy.

Zde je příklad, který ukazuje, jak extrahovat jednotlivé [ `xpath()` ](./workflow-definition-language-functions-reference.md#xpath)IDOC z paketu pomocí funkce :

1. Než začnete, potřebujete aplikaci logiky s aktivační událostí SAP. Pokud ještě nemáte tuto aplikaci logiky, postupujte podle předchozích kroků v tomto tématu [nastavit aplikaci logiky s aktivační událostí SAP](#receive-from-sap).

   Například:

   ![Přidání aktivační události SAP do aplikace logiky](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. Získejte kořenový obor názvů z XML IDOC, který vaše aplikace logiky přijímá ze SAP. Chcete-li extrahovat tento obor názvů z dokumentu XML, přidejte krok, `xpath()` který vytvoří místní proměnnou řetězce a uloží tento obor názvů pomocí výrazu:

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Získání kořenového oboru názvů z Dokumentu IDOC](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Chcete-li extrahovat jednotlivé IDOC, přidejte krok, který vytvoří `xpath()` proměnnou pole a uloží kolekci IDOC pomocí jiného výrazu:

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Získat pole položek](./media/logic-apps-using-sap-connector/get-array.png)

   Proměnná pole zpřístupní každý IDOC pro aplikaci logiky pro zpracování jednotlivě výčet přes kolekci. V tomto příkladu aplikace logiky přenáší každý IDOC na server SFTP pomocí smyčky:

   ![Odeslat IDOC na server SFTP](./media/logic-apps-using-sap-connector/loop-batch.png)

   Každý dokument IDOC musí obsahovat kořenový obor názvů, což je `<Receive></Receive` důvod, proč je obsah souboru zabalen uvnitř prvku spolu s kořenovým oborem názvů před odesláním IDOC do podřízené aplikace nebo serveru SFTP v tomto případě.

Šablonu rychlého startu pro tento vzor můžete použít tak, že vyberete tuto šablonu v Návrháři aplikace logiky při vytváření nové aplikace logiky.

![Vybrat šablonu dávkové aplikace logiky](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Generovat schémata pro artefakty v SAP

Tento příklad používá aplikaci logiky, kterou můžete aktivovat pomocí požadavku HTTP. Akce SAP odešle požadavek do systému SAP generovat schémata pro zadané IDoc a BAPI. Schémata, která se vrátí v odpovědi, se nahrají do účtu integrace pomocí konektoru Azure Resource Manager.

### <a name="add-an-http-request-trigger"></a>Přidání aktivační události požadavku HTTP

1. Na webu Azure Portal vytvořte prázdnou aplikaci logiky, která otevře Návrhář aplikace logiky.

1. Do vyhledávacího pole `http request` zadejte jako filtr. V seznamu **Aktivační události** vyberte při přijetí **požadavku HTTP**.

   ![Přidat aktivační událost požadavku HTTP](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Teď uložte aplikaci logiky, abyste mohli vygenerovat adresu URL koncového bodu pro aplikaci logiky.
Na panelu nástrojů návrháře vyberte **Uložit**.

   Adresa URL koncového bodu se nyní zobrazí v aktivační události, například:

   ![Generovat adresu URL pro koncový bod](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Přidání akce SAP pro generování schémat

1. V Návrháři aplikace logiky vyberte pod aktivační událostí **nový krok**.

   ![Přidání nového kroku do aplikace logiky](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Do vyhledávacího pole `sap` zadejte jako filtr. V seznamu **Akce** vyberte **Generovat schémata**.
  
   ![Přidání akce Generovat schémata do aplikace logiky](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Nebo můžete vybrat kartu **Enterprise** a vybrat akci SAP.

   ![Vybrat akci odeslání SAP na kartě Enterprise](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Pokud vaše připojení již existuje, pokračujte dalším krokem, abyste mohli nastavit akci SAP. Pokud se však zobrazí výzva k zadání podrobností o připojení, zadejte informace, abyste mohli nyní vytvořit připojení k místnímu serveru SAP.

   1. Zadejte název připojení.

   1. V části **Brána dat** v části **Předplatné**nejprve vyberte předplatné Azure pro prostředek brány, který jste vytvořili na webu Azure Portal pro instalaci brány. 
   
   1. V části **Brána připojení**vyberte prostředek brány.

   1. Pokračujte v poskytování informací o připojení. U vlastnosti **Typ přihlášení** postupujte podle kroku podle toho, zda je vlastnost nastavena na **aplikační server** nebo **skupinu**:
   
      * Pro **aplikační server**jsou tyto vlastnosti, které se obvykle zobrazují jako volitelné, povinné:

        ![Vytvoření připojení aplikačního serveru SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Pro **skupinu**jsou tyto vlastnosti, které se obvykle zobrazují jako volitelné, povinné:

        ![Vytvořit připojení serveru zpráv SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Ve výchozím nastavení se silné psaní používá ke kontrole neplatných hodnot provedením ověření XML proti schématu. Toto chování vám může pomoci zjistit problémy dříve. Možnost **Bezpečné psaní** je k dispozici pro zpětnou kompatibilitu a kontroluje pouze délku řetězce. Přečtěte si další informace o [možnosti Bezpečné psaní](#safe-typing).

   1. Až budete hotovi, vyberte **Vytvořit**.

      Logic Apps nastaví a otestuje připojení a ujistěte se, že připojení funguje správně.

1. Zadejte cestu k artefaktu, pro který chcete generovat schéma.

   Akci SAP můžete vybrat z výběru souborů:

   ![Vybrat akci SAP](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Nebo můžete ručně zadat akci:

   ![Ruční zadání akce SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Chcete-li generovat schémata pro více než jeden artefakt, zadejte podrobnosti akce SAP pro každý artefakt, například:

   ![Vybrat přidat novou položku](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Zobrazit dvě položky](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Další informace o akci SAP naleznete v tématu [Schémata zpráv pro operace IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

### <a name="test-your-logic-app"></a>Testování aplikace logiky

1. Na panelu nástrojů návrháře vyberte **Spustit,** chcete-li spustit spuštění aplikace logiky.

1. Otevřete spuštění a zkontrolujte výstupy pro **akci Generovat schémata.**

   Výstupy zobrazit generované schémata pro zadaný seznam zpráv.

### <a name="upload-schemas-to-an-integration-account"></a>Nahrání schémat do účtu integrace

Volitelně můžete stáhnout nebo uložit generované schémata v úložištích, jako je například objekt blob, úložiště nebo účet integrace. Účty integrace poskytují prvotřídní prostředí s jinými akcemi XML, takže tento příklad ukazuje, jak nahrát schémata do účtu integrace pro stejnou aplikaci logiky pomocí konektoru Azure Resource Manager.

1. V Návrháři aplikace logiky vyberte pod aktivační událostí **nový krok**.

1. Do vyhledávacího pole `Resource Manager` zadejte jako filtr. Vyberte **Vytvořit nebo aktualizovat prostředek**.

   ![Vybrat akci Správce prostředků Azure](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Zadejte podrobnosti o akci, včetně předplatného Azure, skupiny prostředků Azure a účtu integrace. Chcete-li do polí přidat tokeny SAP, klepněte do polí pro tato pole a vyberte ze seznamu dynamického obsahu, který se zobrazí.

   1. Otevřete seznam **Přidat nový parametr** a vyberte pole **Umístění** a **Vlastnosti.**

   1. Zadejte podrobnosti pro tato nová pole, jak je znázorněno v tomto příkladu.

      ![Zadání podrobností pro akci Správce prostředků Azure](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   Akce SAP **Generovat schémata** generuje schémata jako kolekce, takže návrhář automaticky přidá **For každou** smyčku do akce. Zde je příklad, který ukazuje, jak se tato akce zobrazí:

   ![Akce Správce prostředků Azure se smyčkou "pro každý"](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > Schémata používají base64 kódovaný formát. Chcete-li nahrát schémata do účtu integrace, musí být `base64ToString()` dekódována pomocí funkce. Tady je příklad, který ukazuje `"properties"` kód prvku:
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

### <a name="test-your-logic-app"></a>Testování aplikace logiky

1. Na panelu nástrojů návrháře vyberte **Spustit,** chcete-li ručně aktivovat aplikaci logiky.

1. Po úspěšném spuštění přejděte na účet integrace a zkontrolujte, zda existují generované schémata.

## <a name="enable-secure-network-communications"></a>Povolit zabezpečenou síťovou komunikaci

Než začnete, ujistěte se, že jste splnili dříve uvedené [požadavky](#pre-reqs):

* Místní brána dat je nainstalovaná v počítači, který je ve stejné síti jako váš systém SAP.
* Pro jednotné přibíjení brány běží jako uživatel, který je mapován na uživatele SAP.
* Knihovna SNC, která poskytuje další funkce zabezpečení, je nainstalována ve stejném počítači jako brána dat. Některé příklady zahrnují [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos a NTLM.

   Chcete-li povolit SNC pro vaše požadavky na nebo ze systému SAP, zaškrtněte políčko **Použít SNC** ve spojení SAP a zadejte tyto vlastnosti:

   ![Konfigurace SAP SNC v připojení](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Vlastnost | Popis |
   |----------| ------------|
   | **Cesta knihovny SNC** | Název knihovny SNC nebo cesta vzhledem k umístění instalace NCo nebo absolutní cesta. Příklady `sapsnc.dll` jsou `.\security\sapsnc.dll` `c:\security\sapsnc.dll`nebo nebo . |
   | **SNC SSO** | Při připojení prostřednictvím SNC, SNC identity se obvykle používá pro ověřování volajícího. Další možností je přepsat tak, aby informace o uživateli a hesle lze použít pro ověření volajícího, ale řádek je stále šifrována. |
   | **SNC Mé jméno** | Ve většině případů lze tuto vlastnost vynechat. Nainstalované Řešení SNC obvykle zná svůj vlastní název SNC. Pouze pro řešení, která podporují více identit, může být nutné zadat identitu, která má být použita pro tento konkrétní cíl nebo server. |
   | **Název partnera SNC** | Název back-end SNC. |
   | **SNC Kvalita ochrany** | Kvalita služeb, které mají být použity pro SNC komunikaci tohoto konkrétního místa určení nebo serveru. Výchozí hodnota je definována systémem back-end. Maximální hodnota je definována bezpečnostním produktem používaným pro SNC. |
   |||

   > [!NOTE]
   > Nenastavovat proměnné prostředí SNC_LIB a SNC_LIB_64 na počítači, kde máte bránu dat a knihovnu SNC. Pokud je nastavena, mají přednost před hodnotou knihovny SNC projeté konektorem.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Bezpečné psaní

Ve výchozím nastavení se při vytváření připojení SAP používá silné psaní ke kontrole neplatných hodnot provedením ověření XML proti schématu. Toto chování vám může pomoci zjistit problémy dříve. Možnost **Bezpečné psaní** je k dispozici pro zpětnou kompatibilitu a kontroluje pouze délku řetězce. Pokud zvolíte **bezpečné psaní**, typ DATS a typ TIMS v SYSTÉMU SAP budou `xs:date` `xs:time`považovány `xmlns:xs="http://www.w3.org/2001/XMLSchema"`za řetězce, nikoli za jejich ekvivalenty XML, a , kde . Bezpečné psaní ovlivňuje chování pro všechny generování schématu, odeslat zprávu pro "byla odeslána" datové části a "byla přijata" odpověď a aktivační událost. 

Při použití silného psaní **(Bezpečné psaní** není povoleno) schéma mapuje typy DATS a TIMS na přímočařejší typy XML:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Při odesílání zpráv pomocí silného psaní odpovídá odpověď DATS a TIMS odpovídajícímu formátu typu XML:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Je-li povoleno **bezpečné zadávání,** schéma mapuje typy DATS a TIMS pouze na pole řetězců XML s omezeními délky, například:

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

Při odeslání zpráv s **povolenou funkcí Bezpečné psaní** vypadá odpověď DATS a TIMS jako tento příklad:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>Pokročilé scénáře

### <a name="confirm-transaction-explicitly"></a>Potvrdit transakci explicitně

Při odesílání transakcí do SAP z Logic Apps, tato výměna se stane ve dvou krocích, jak je popsáno v dokumentu SAP transakční [RFC Server Programy](https://help.sap.com/doc/saphelp_nwpi71/7.1/en-US/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). Ve výchozím nastavení **akce Odeslat do SAP** zpracovává jak kroky pro přenos funkce, tak pro potvrzení transakce v jednom volání. Konektor SAP nabízí možnost oddělit tyto kroky. Můžete odeslat IDOC a spíše než automaticky potvrdit transakci, můžete použít explicitní **potvrzení ID transakce** akce.

Tato možnost oddělit potvrzení ID transakce je užitečná, pokud nechcete duplikovat transakce v systému SAP, například ve scénářích, kde může dojít k selhání z důvodu příčin, jako jsou problémy se sítí. Potvrzením ID transakce samostatně je transakce dokončena pouze jednou ve vašem systému SAP.

Zde je příklad, který ukazuje tento vzor:

1. Vytvořte prázdnou aplikaci logiky a přidejte aktivační událost HTTP.

1. Z konektoru SAP přidejte akci **Odeslat IDOC.** Zadejte podrobnosti o idoc, které odešlete do systému SAP.

1. Chcete-li explicitně potvrdit ID transakce v samostatném kroku, vyberte v poli **Potvrdit TID** **možnost Ne**. Pro volitelné pole **GUID ID transakce** můžete buď ručně zadat hodnotu, nebo nechat konektor automaticky generovat a vrátit tento identifikátor GUID v odpovědi z akce Odeslat IDOC.

   ![Odeslat vlastnosti akce IDOC](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. Chcete-li explicitně potvrdit ID transakce, přidejte akci **Potvrdit ID transakce.** Klikněte do pole **ID transakce,** aby se zobrazil seznam dynamického obsahu. V tomto seznamu vyberte hodnotu **ID transakce,** která je vrácena z akce **Odeslat IDOC.**

   ![Potvrdit akci ID transakce](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   Po spuštění tohoto kroku je aktuální transakce označena jako dokončená na obou koncích, na straně konektoru SAP a na straně systému SAP.

## <a name="known-issues-and-limitations"></a>Známé problémy a omezení

Zde jsou aktuálně známé problémy a omezení pro konektor SAP:

* Aktivační událost SAP nepodporuje clustery datových bran. V některých případech převzetí služeb při selhání uzel brány dat, který komunikuje se systémem SAP se může lišit od aktivního uzlu, což má za následek neočekávané chování. Pro scénáře odesílání jsou podporovány clustery datových bran.

* Konektor SAP v současné době nepodporuje řetězce směrovačů SAP. Místní brána dat musí existovat ve stejné síti LAN jako systém SAP, který chcete připojit.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o tomto konektoru, jako jsou aktivační události, akce a omezení, jak je popsáno v souboru Swagger konektoru, naleznete [na referenční stránce konektoru](https://docs.microsoft.com/connectors/sap/).

> [!NOTE]
> Pro aplikace logiky v [prostředí služby integrace (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), tento konektor je ise označené verze používá [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) místo.

## <a name="next-steps"></a>Další kroky

* [Připojte se k místním systémům](../logic-apps/logic-apps-gateway-connection.md) z Azure Logic Apps.
* Zjistěte, jak ověřit, transformovat a používat další operace zpráv pomocí sady [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).
* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md).
