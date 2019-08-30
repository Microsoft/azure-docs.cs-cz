---
title: Připojení k systémům SAP – Azure Logic Apps
description: Automatizace pracovních postupů pomocí Azure Logic Apps pro přístup k prostředkům SAP a jejich správa
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 08/30/2019
tags: connectors
ms.openlocfilehash: 8712af60df2454b29c0691602260c8b826eae75c
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165006"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Připojení k systémům SAP z Azure Logic Apps

> [!IMPORTANT]
> Předchozí konektory serveru SAP Application Server a server zpráv SAP mají naplánované zastaralé. Aktuální konektor SAP slučuje tyto předchozí konektory SAP, takže nemusíte měnit typ připojení, je plně kompatibilní s předchozími konektory, poskytuje mnoho dalších možností a nadále používá knihovnu SAP .NET Connector ( SAP NCo).
>
> U Logic Apps, které používají starší konektory, [migrujte prosím na nejnovější konektor](#migrate) před datem vyřazení. V opačném případě se tyto aplikace logiky projeví při selhání spuštění a nebude možné odesílat zprávy do vašeho systému SAP.

Tento článek ukazuje, jak můžete přistupovat k místním prostředkům SAP zevnitř aplikace logiky pomocí konektoru SAP. Konektor pracuje s klasickými verzemi SAP, jako jsou R/3 a systémy ECC místně. Konektor taky umožňuje integraci s novějšími systémy SAP založenými na HANA, jako je S/4 HANA, ať už jsou hostované místně nebo v cloudu. Konektor SAP podporuje integraci zpráv nebo dat do systémů a ze systému SAP NetWeaver prostřednictvím zprostředkujícího dokumentu (IDoc), rozhraní BAPI (Business Application Programming Interface) nebo vzdáleného volání funkce (RFC).

Konektor SAP používá [knihovnu SAP .NET Connector (NCo)](https://support.sap.com/en/product/connectors/msnet.html) a poskytuje tyto operace nebo akce:

* **Odeslat do SAP**: Odešlete IDoc přes tRFC, zavolejte funkce BAPI přes RFC nebo zavolejte RFC/tRFC v systémech SAP.
* **Přijmout z SAP**: Přijímat IDoc přes tRFC, volat funkce BAPI přes tRFC nebo volat RFC/tRFC v systémech SAP.
* **Generovat schémata**: Generování schémat pro artefakty SAP pro IDoc, BAPI nebo RFC.

Pro tyto operace konektor SAP podporuje základní ověřování prostřednictvím uživatelských jmen a hesel. Konektor podporuje také [zabezpečenou síťovou komunikaci (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). SNC se dá použít pro jednotné přihlašování SAP NetWeaver (SSO) nebo pro další funkce zabezpečení poskytované externím produktem zabezpečení.

Konektor SAP se integruje s místními systémy SAP prostřednictvím místní [brány dat](../logic-apps/logic-apps-gateway-connection.md). Ve scénářích odeslání se například při odeslání zprávy z aplikace logiky do systému SAP chová brána dat jako klient RFC a přepošle žádosti přijaté z aplikace logiky do SAP. Obdobně platí, že brána data Gateway funguje jako server RFC, který přijímá žádosti od SAP a přesměruje je do aplikace logiky.

V tomto článku se dozvíte, jak vytvořit ukázkové aplikace logiky, které se integrují s SAP a které pokrývají dříve popsané scénáře integrace. V tomto článku se pro Logic Apps, které používají starší konektory SAP, dozvíte, jak migrovat aplikace logiky na nejnovější konektor SAP.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Požadavky

Pokud chcete postupovat podle tohoto článku, budete potřebovat tyto položky:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, zaregistrujte [si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky, ze které chcete získat přístup k systému SAP, a Trigger, který spouští pracovní postup vaší aplikace logiky. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md) a [rychlý Start: Vytvořte svou první aplikaci](../logic-apps/quickstart-create-first-logic-app-workflow.md)logiky.

* [Aplikační Server SAP](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) nebo [Server zpráv SAP](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).

* Stáhněte si a nainstalujte nejnovější místní [bránu dat](https://www.microsoft.com/download/details.aspx?id=53127) na libovolný místní počítač. Než budete pokračovat, ujistěte se, že jste si nastavili bránu v Azure Portal. Brána vám pomůže zabezpečený přístup k místním datům a prostředkům. Další informace najdete v tématu [instalace místní brány dat pro Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

* Pokud používáte SNC s SSO, ujistěte se, že je brána spuštěná jako uživatel, který je namapovaný na uživatele SAP. Chcete-li změnit výchozí účet, vyberte možnost **změnit účet**a zadejte přihlašovací údaje uživatele.

  ![Změnit účet brány](./media/logic-apps-using-sap-connector/gateway-account.png)

* Pokud SNC povolíte s externím produktem zabezpečení, zkopírujte knihovnu SNC nebo soubory do stejného počítače, ve kterém je brána nainstalovaná. Mezi příklady SNC produktů patří [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos a NTLM.

* Stáhněte a nainstalujte nejnovější knihovnu klienta SAP, která je aktuálně [konektorem SAP (NCo 3,0) pro Microsoft .NET 3.0.22.0 zkompilované pomocí .NET Framework 4,0-Windows 64-bit (x64)](https://softwaredownloads.sap.com/file/0020000001000932019)na stejném počítači jako místní brána dat. Nainstalujte tuto verzi nebo novější z těchto důvodů:

  * Starší verze SAP NCo můžou být zablokované, když se současně pošle víc než jedna zpráva IDoc. Tento stav blokuje všechny pozdější zprávy, které se odesílají do cíle SAP, což způsobí vypršení časového limitu zpráv.
  
  * Místní brána dat se spouští jenom v 64 systémech. V opačném případě se zobrazí chyba "chybná image", protože hostitelská služba brány dat nepodporuje 32 bitových sestavení.
  
  * Hostitelská služba brány dat i adaptér Microsoft SAP používají .NET Framework 4,5. SAP NCo for .NET Framework 4,0 funguje s procesy, které používají .NET Runtime 4,0 až 4.7.1. SAP NCo for .NET Framework 2,0 funguje s procesy, které používají .NET runtime 2,0 na 3,5, ale už nefungují s nejnovější místní bránou dat.

* Obsah zprávy, který můžete odeslat na Server SAP, například vzorový soubor IDoc, musí být ve formátu XML a zahrnovat obor názvů pro akci SAP, kterou chcete použít.

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>Migrovat na aktuální konektor

1. Pokud jste to ještě neudělali, aktualizujte místní [bránu dat](https://www.microsoft.com/download/details.aspx?id=53127) tak, aby byla k dispozici nejnovější verze. Další informace najdete v tématu [instalace místní brány dat pro Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

1. V aplikaci logiky, která používá starší konektor SAP, odstraňte akci **Odeslat do SAP** .

1. Z nejnovějšího konektoru SAP přidejte akci **Odeslat do SAP** . Než budete moct tuto akci použít, znovu vytvořte připojení k vašemu systému SAP.

1. Až budete hotovi, uložte aplikaci logiky.

<a name="add-trigger"></a>

## <a name="send-to-sap"></a>Odeslat do SAP

V tomto příkladu se používá aplikace logiky, kterou můžete aktivovat pomocí požadavku HTTP. Aplikace logiky pošle IDoc serveru SAP a vrátí odpověď žadateli, který se nazývá aplikace logiky.

### <a name="add-an-http-request-trigger"></a>Přidání triggeru požadavku HTTP

V Azure Logic Apps musí každá aplikace logiky začínat [triggerem](../logic-apps/logic-apps-overview.md#logic-app-concepts), který se aktivuje, když dojde ke konkrétní události nebo když dojde ke splnění určité podmínky. Pokaždé, když se Trigger aktivuje, modul Logic Apps vytvoří instanci aplikace logiky a začne spouštět pracovní postup vaší aplikace.

V tomto příkladu vytvoříte aplikaci logiky s koncovým bodem v Azure, abyste mohli odesílat *požadavky HTTP POST* do vaší aplikace logiky. Když aplikace logiky obdrží tyto požadavky HTTP, Trigger se aktivuje a spustí další krok v pracovním postupu.

1. V [Azure Portal](https://portal.azure.com)vytvořte prázdnou aplikaci logiky, která otevře návrháře aplikace logiky.

1. Do vyhledávacího pole zadejte jako filtr "požadavek HTTP". V seznamu **triggery** vyberte, **kdy se přijme požadavek HTTP**.

   ![Přidat aktivační událost požadavku HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

1. Nyní uložte aplikaci logiky, abyste mohli vygenerovat adresu URL koncového bodu pro vaši aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

   Adresa URL koncového bodu se teď zobrazí ve triggeru, například:

   ![Vygenerovat adresu URL pro koncový bod](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>Přidat akci SAP

V Azure Logic Apps [Akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je krok v pracovním postupu, který následuje po triggeru nebo jiné akci. Pokud jste ještě nepřidali Trigger do aplikace logiky a chcete postupovat podle tohoto příkladu, [přidejte Trigger popsaný v této části](#add-trigger).

1. V návrháři aplikace logiky pod triggerem vyberte **Nový krok**.

   ![Vyberte nový krok.](./media/logic-apps-using-sap-connector/add-action.png)

1. Do vyhledávacího pole zadejte "SAP" jako filtr. V seznamu **Akce** vyberte **Odeslat zprávu do SAP**.
  
   ![Vybrat akci SAP odeslat](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Nebo místo hledání zvolte kartu **Enterprise** a pak vyberte akci SAP.

   ![Výběr možnosti SAP odeslat akci na kartě organizace](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Pokud se zobrazí výzva k zadání podrobností o připojení, vytvořte připojení SAP hned teď. Jinak, pokud vaše připojení už existuje, pokračujte dalším krokem, abyste mohli nastavit akci SAP.

   **Vytvoření místního připojení SAP**

   Zadejte informace o připojení pro váš Server SAP. Pro vlastnost **Brána dat** vyberte bránu dat, kterou jste vytvořili v Azure Portal pro instalaci brány. Až budete hotovi, vyberte **vytvořit**. Logic Apps nastaví a otestuje připojení, aby se zajistilo správné fungování připojení.

   * Pokud je vlastnost **typ přihlášení** nastavená na **aplikační server**, vyžadují se tyto vlastnosti, které se obvykle zobrazují jako volitelné:

     ![Vytvořit připojení aplikačního serveru SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

   * Pokud je vlastnost **typ přihlášení** nastavená na **skupiny**, vyžadují se tyto vlastnosti, které se obvykle zobrazují jako volitelné:

     ![Vytvořit připojení k serveru zpráv SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

   Ve výchozím nastavení se silné zadání používá ke kontrole neplatných hodnot prováděním ověřování XML proti schématu. Toto chování vám může přispět k detekci problémů dříve. Možnost **bezpečného psaní** je k dispozici pro zpětnou kompatibilitu a kontroluje pouze délku řetězce. Přečtěte si další informace o [možnosti bezpečného psaní](#safe-typing).

1. Nyní vyhledejte a vyberte akci ze serveru SAP.

   1. V poli **Akce SAP** vyberte ikonu složky. V seznamu soubor vyhledejte a vyberte zprávu SAP, kterou chcete použít. Chcete-li procházet seznam, použijte šipky.

      Tento příklad vybere IDoc s typem **objednávky** .

      ![Najít a vybrat akci IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Pokud požadovanou akci nemůžete najít, můžete zadat cestu ručně, například:

      ![Ručně zadat cestu k IDoc akci](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Zadejte hodnotu pro **akci SAP** prostřednictvím editoru výrazů. Tímto způsobem můžete použít stejnou akci u různých typů zpráv.

      Další informace o operacích IDoc najdete v tématu [schémata zpráv pro operace IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Klikněte do pole **vstupní zpráva** , aby se zobrazil seznam dynamického obsahu. V tomto seznamu v části **když se přijme požadavek HTTP**, vyberte pole **body** .

      Tento krok zahrnuje obsah zprávy z triggeru požadavku HTTP a odesílá tento výstup do vašeho serveru SAP.

      ![Vybrat pole "tělo"](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Až budete hotovi, vaše akce SAP bude vypadat jako v tomto příkladu:

      ![Dokončit akci SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Přidat akci odpovědi HTTP

Teď přidejte akci odpovědi do pracovního postupu aplikace logiky a zahrňte výstup z akce SAP. Vaše aplikace logiky tak vrátí výsledky ze serveru SAP původnímu žadateli.

1. V návrháři aplikace logiky v akci SAP vyberte **Nový krok**.

1. Do vyhledávacího pole zadejte jako filtr "Response". V seznamu **Akce** vyberte možnost **odpověď**.

1. Klikněte do pole **text** , aby se zobrazil seznam dynamického obsahu. V tomto seznamu v části **Odeslat zprávu do SAP**vyberte pole **text** .

   ![Dokončit akci SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Uložte svou aplikaci logiky.

### <a name="test-your-logic-app"></a>Testování aplikace logiky

1. Pokud vaše aplikace logiky ještě není povolená, vyberte v nabídce aplikace logiky **Přehled**. Na panelu nástrojů vyberte **Povolit**.

1. Na panelu nástrojů návrháře vyberte **Spustit**. Tento krok ručně spustí vaši aplikaci logiky.

1. Aktivujte aplikaci logiky odesláním požadavku HTTP POST na adresu URL v triggeru požadavku HTTP.
Přidejte obsah zprávy s vaším požadavkem. K odeslání žádosti můžete použít nástroj, jako je například [post](https://www.getpostman.com/apps).

   V tomto článku požadavek pošle soubor IDoc, který musí být ve formátu XML a zahrnuje obor názvů pro akci SAP, kterou používáte, například:

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. Až odešlete požadavek HTTP, počkejte na odpověď z vaší aplikace logiky.

   > [!NOTE]
   > Vaše aplikace logiky může vypršet, pokud všechny kroky požadované pro odpověď nekončí v rámci [časového](./logic-apps-limits-and-config.md)limitu požadavku. Pokud k tomuto stavu dojde, můžou se požadavky zablokovat. Pro usnadnění diagnostiky problémů se dozvíte, jak můžete [kontrolovat a monitorovat aplikace logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Nyní jste vytvořili aplikaci logiky, která může komunikovat se serverem SAP. Teď, když jste nastavili připojení SAP pro vaši aplikaci logiky, můžete prozkoumat další dostupné akce SAP, například BAPI a RFC.

## <a name="receive-from-sap"></a>Příjem ze SAP

V tomto příkladu se používá aplikace logiky, která se aktivuje, když aplikace obdrží zprávu ze systému SAP.

### <a name="add-an-sap-trigger"></a>Přidání triggeru SAP

1. V Azure Portal vytvořte prázdnou aplikaci logiky, která otevře návrháře aplikace logiky.

1. Do vyhledávacího pole zadejte "SAP" jako filtr. V seznamu **triggery** vyberte, **kdy se má ze SAP přijmout zpráva**.

   ![Přidat aktivační událost SAP](./media/logic-apps-using-sap-connector/add-sap-trigger.png)

   Nebo můžete přejít na kartu **Enterprise** a vybrat aktivační událost:

   ![Přidat aktivační událost SAP z karty Enterprise](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Pokud se zobrazí výzva k zadání podrobností o připojení, vytvořte připojení SAP hned teď. Pokud vaše připojení už existuje, pokračujte dalším krokem, abyste mohli nastavit akci SAP.

   **Vytvoření místního připojení SAP**

   Zadejte informace o připojení pro váš Server SAP. Pro vlastnost **Brána dat** vyberte bránu dat, kterou jste vytvořili v Azure Portal pro instalaci brány. Až budete hotovi, vyberte **vytvořit**. Logic Apps nastaví a otestuje připojení, aby se zajistilo správné fungování připojení.

   * Pokud je vlastnost **typ přihlášení** nastavená na **aplikační server**, vyžadují se tyto vlastnosti, které se obvykle zobrazují jako volitelné:

     ![Vytvořit připojení aplikačního serveru SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

   * Pokud je vlastnost **typ přihlášení** nastavená na **skupiny**, vyžadují se tyto vlastnosti, které se obvykle zobrazují jako volitelné:

     ![Vytvořit připojení k serveru zpráv SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

   Ve výchozím nastavení se silné zadání používá ke kontrole neplatných hodnot prováděním ověřování XML proti schématu. Toto chování vám může přispět k detekci problémů dříve. Možnost **bezpečného psaní** je k dispozici pro zpětnou kompatibilitu a kontroluje pouze délku řetězce. Přečtěte si další informace o [možnosti bezpečného psaní](#safe-typing).

1. Zadejte požadované parametry na základě konfigurace systému SAP.

   Volitelně můžete zadat jednu nebo více akcí SAP. Tento seznam akcí určuje zprávy, které aktivační událost přijme ze serveru SAP prostřednictvím brány dat. Prázdný seznam určuje, že Trigger obdrží všechny zprávy. Pokud má seznam více než jednu zprávu, aktivační událost přijímá pouze zprávy uvedené v seznamu. Všechny ostatní zprávy odeslané z vašeho serveru SAP jsou bránou odmítnuté.

   Akci SAP můžete vybrat z výběru souboru:

   ![Vybrat akci SAP](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Můžete také zadat akci ručně:

   ![Zadat akci SAP ručně](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Tady je příklad, který ukazuje, jak se akce zobrazí při nastavení triggeru pro příjem více než jedné zprávy.

   ![Příklad triggeru](media/logic-apps-using-sap-connector/example-trigger.png)  

   Další informace o akci SAP najdete v tématu [schémata zpráv pro IDOC operace](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations) .

1. Nyní uložte aplikaci logiky, abyste mohli začít přijímat zprávy ze systému SAP.
Na panelu nástrojů návrháře vyberte **Uložit**.

Vaše aplikace logiky je teď připravená přijímat zprávy ze systému SAP.

> [!NOTE]
> Trigger SAP není Trigger cyklického dotazování, ale je místo toho Trigger založený na Webhooku. Trigger se volá z brány jenom v případě, že existuje zpráva, takže není potřeba žádné cyklické dotazování.

### <a name="test-your-logic-app"></a>Testování aplikace logiky

1. Pokud chcete aktivovat aplikaci logiky, odešlete zprávu ze systému SAP.

1. V nabídce aplikace logiky vyberte **Přehled**. Podívejte se na **historii spuštění** pro jakékoli nové běhy aplikace logiky.

1. Otevřete poslední spuštění, ve kterém se zobrazí zpráva odeslaná ze systému SAP v oddílu aktivační výstupy triggeru.

## <a name="receive-idoc-packets-from-sap"></a>Příjem paketů IDOC ze SAP

Můžete nastavit SAP pro posílání [IDOCs v paketech](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/en-US/4ab38886549a6d8ce10000000a42189c.html), což jsou dávky nebo skupiny IDOCs. Aby bylo možné přijímat pakety IDOC, konektor SAP a konkrétně Trigger nevyžadují další konfiguraci. Pokud však chcete zpracovat každou položku v paketu IDOC poté, co Trigger obdrží paket, je nutné provést některé další kroky pro rozdělení paketu do jednotlivých IDOCs.

Tady je příklad, který ukazuje, jak extrahovat jednotlivé IDOCs z paketu pomocí [ `xpath()` funkce](./workflow-definition-language-functions-reference.md#xpath):

1. Než začnete, budete potřebovat aplikaci logiky s triggerem SAP. Pokud tuto aplikaci logiky ještě nemáte, pomocí předchozích kroků v tomto tématu nastavte [aplikaci logiky pomocí triggeru SAP](#receive-from-sap).

   Příklad:

   ![Aktivační událost SAP](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. Získejte kořenový obor názvů z XML IDOC, který vaše aplikace logiky obdrží od SAP. Chcete-li tento obor názvů extrahovat z dokumentu XML, přidejte krok, který vytvoří místní řetězcovou proměnnou a uloží tento obor názvů `xpath()` pomocí výrazu:

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Získat obor názvů](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Chcete-li extrahovat jednotlivé IDOC, přidejte krok, který vytvoří proměnnou pole a uloží kolekci IDOC pomocí jiného `xpath()` výrazu:

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Získat pole položek](./media/logic-apps-using-sap-connector/get-array.png)

   Proměnná Array zpřístupňuje jednotlivé IDOC pro vaši aplikaci logiky, aby je bylo možné zpracovat individuálně pomocí výčtu kolekce. V tomto příkladu aplikace logiky přenáší jednotlivé IDOC na server SFTP pomocí smyčky:

   ![Odeslat IDOC](./media/logic-apps-using-sap-connector/loop-batch.png)

   Každý IDOC musí zahrnovat kořenový obor názvů, což je důvod, proč je obsah souboru zabalen uvnitř `<Receive></Receive` elementu společně s kořenovým oborem názvů před odesláním IDOC do aplikace pro příjem dat nebo serveru SFTP v tomto případě.

> [!TIP]
> Můžete použít šablonu pro rychlý Start pro tento model výběrem této šablony v návrháři aplikace logiky při vytváření nové aplikace logiky.
>
> ![Šablona Batch](./media/logic-apps-using-sap-connector/batch-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Generování schémat pro artefakty v SAP

V tomto příkladu se používá aplikace logiky, kterou můžete aktivovat pomocí požadavku HTTP. Akce SAP odešle požadavek do systému SAP, aby vygeneroval schémata pro zadané IDoc a BAPI. Schémata, která se vracejí v odpovědi, se nahrají na účet pro integraci pomocí konektoru Azure Resource Manager.

### <a name="add-an-http-request-trigger"></a>Přidání triggeru požadavku HTTP

1. V Azure Portal vytvořte prázdnou aplikaci logiky, která otevře návrháře aplikace logiky.

1. Do vyhledávacího pole zadejte jako filtr "požadavek HTTP". V seznamu **triggery** vyberte, **kdy se přijme požadavek HTTP**.

   ![Přidat aktivační událost požadavku HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

1. Nyní uložte aplikaci logiky, abyste mohli vygenerovat adresu URL koncového bodu pro vaši aplikaci logiky.
Na panelu nástrojů návrháře vyberte **Uložit**.

   Adresa URL koncového bodu se teď zobrazí ve triggeru, například:

   ![Vygenerovat adresu URL pro koncový bod](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Přidání akce SAP pro generování schémat

1. V návrháři aplikace logiky pod triggerem vyberte **Nový krok**.

   ![Vyberte nový krok.](./media/logic-apps-using-sap-connector/add-action.png)

1. Do vyhledávacího pole zadejte "SAP" jako filtr. V seznamu **Akce** vyberte možnost **Generovat schémata**.
  
   ![Vybrat akci SAP odeslat](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Nebo můžete také vybrat kartu **Enterprise** a pak vybrat akci SAP.

   ![Výběr možnosti SAP odeslat akci na kartě organizace](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Pokud se zobrazí výzva k zadání podrobností o připojení, vytvořte připojení SAP hned teď. Pokud vaše připojení už existuje, pokračujte dalším krokem, abyste mohli nastavit akci SAP.

   **Vytvoření místního připojení SAP**

   1. Zadejte informace o připojení pro váš Server SAP. Pro vlastnost **Brána dat** vyberte bránu dat, kterou jste vytvořili v Azure Portal pro instalaci brány.

      * Pokud je vlastnost **typ přihlášení** nastavená na **aplikační server**, vyžadují se tyto vlastnosti, které se obvykle zobrazují jako volitelné:

        ![Vytvořit připojení aplikačního serveru SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Pokud je vlastnost **typ přihlášení** nastavená na **skupiny**, vyžadují se tyto vlastnosti, které se obvykle zobrazují jako volitelné:

        ![Vytvořit připojení k serveru zpráv SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Ve výchozím nastavení se silné zadání používá ke kontrole neplatných hodnot prováděním ověřování XML proti schématu. Toto chování vám může přispět k detekci problémů dříve. Možnost **bezpečného psaní** je k dispozici pro zpětnou kompatibilitu a kontroluje pouze délku řetězce. Přečtěte si další informace o [možnosti bezpečného psaní](#safe-typing).

   1. Až budete hotovi, vyberte **vytvořit**.

      Logic Apps nastaví a otestuje připojení, aby se zajistilo správné fungování připojení.

1. Zadejte cestu k artefaktu, pro který chcete vytvořit schéma.

   Akci SAP můžete vybrat z výběru souboru:

   ![Vybrat akci SAP](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Nebo můžete ručně zadat tuto akci:

   ![Zadat akci SAP ručně](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Chcete-li generovat schémata pro více než jeden artefakt, zadejte podrobnosti o akci SAP pro každý artefakt, například:

   ![Vyberte Přidat novou položku.](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Zobrazit dvě položky](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Další informace o akci SAP najdete v tématu [schémata zpráv pro IDOC operace](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

### <a name="test-your-logic-app"></a>Testování aplikace logiky

1. Na panelu nástrojů návrháře vyberte **Spustit** , aby se spustil běh aplikace logiky.

1. Otevřete běh a podívejte se na výstupy pro akci **Generovat schémata** .

   Výstupy zobrazují vygenerovaná schémata pro zadaný seznam zpráv.

### <a name="upload-schemas-to-an-integration-account"></a>Nahrávání schémat na účet pro integraci

V případě potřeby můžete vygenerovaná schémata stáhnout nebo uložit v úložištích, jako je například objekt blob, úložiště nebo účet integrace. Účty pro integraci poskytují prvotřídní prostředí s dalšími akcemi XML, takže tento příklad ukazuje, jak nahrát schémata do účtu pro integraci pro stejnou aplikaci logiky pomocí konektoru Azure Resource Manager.

1. V návrháři aplikace logiky pod triggerem vyberte **Nový krok**.

1. Do vyhledávacího pole zadejte jako filtr "Správce prostředků". Vyberte **vytvořit nebo aktualizovat prostředek**.

   ![Vybrat Azure Resource Manager akci](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Zadejte podrobnosti o akci, včetně vašeho předplatného Azure, skupiny prostředků Azure a účtu pro integraci. Chcete-li do polí přidat tokeny SAP, klikněte do polí u těchto polí a vyberte ze seznamu dynamického obsahu, který se zobrazí.

   1. Otevřete seznam **Přidat nový parametr** a vyberte pole **umístění** a **vlastnosti** .

   1. Zadejte podrobnosti pro tato nová pole, jak je znázorněno v tomto příkladu.

      ![Zadat podrobnosti pro Azure Resource Manager akci](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   Akce SAP **generující schémata** generuje schémata jako kolekci, takže Návrhář **pro každou** smyčku do akce přidá automaticky. Tady je příklad, který ukazuje, jak se tato akce zobrazuje:

   ![Akce Azure Resource Manager se smyčkou "for each"](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)  
   > [!NOTE]
   > Schémata používají formát v kódování Base64. Chcete-li odeslat schémata do účtu pro integraci, je nutné je dekódovat pomocí `base64ToString()` funkce. Zde je příklad, který ukazuje kód pro `"properties"` element:
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

1. Na panelu nástrojů návrháře vyberte **Spustit** , aby se aktivovala aplikace logiky ručně.

1. Po úspěšném spuštění přejdete na účet pro integraci a zkontrolujete, že vygenerovaná schémata existují.

## <a name="enable-secure-network-communications"></a>Povolit zabezpečenou síťovou komunikaci

Než začnete, ujistěte se, že jste splnili výše uvedené [požadavky](#pre-reqs):

* Místní brána dat je nainstalovaná na počítači, který je ve stejné síti jako váš systém SAP.
* Pro jednotné přihlašování je brána spuštěná jako uživatel, který je namapovaný na uživatele SAP.
* Knihovna SNC, která poskytuje další funkce zabezpečení, je nainstalovaná na stejném počítači jako brána dat. Mezi příklady patří [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos a NTLM.

   Pokud chcete povolit SNC pro vaše požadavky na systém SAP nebo z něj, zaškrtněte políčko **použít SNC** v připojení SAP a zadejte tyto vlastnosti:

   ![Konfigurace SAP SNC v souvislosti s připojením](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Vlastnost | Popis |
   |----------| ------------|
   | **Cesta ke knihovně SNC** | Název knihovny SNC nebo cesta relativní k umístění instalace NCo nebo absolutní cesta. Příklady jsou `sapsnc.dll` `.\security\sapsnc.dll` nebo.`c:\security\sapsnc.dll` |
   | **JEDNOTNÉ PŘIHLAŠOVÁNÍ SNC** | Při připojení prostřednictvím SNC se obvykle používá identita SNC pro ověřování volajícího. Další možností je přepsat, aby se informace o uživateli a heslech mohly použít k ověřování volajícího, ale řádek je stále zašifrovaný. |
   | **SNC moje jméno** | Ve většině případů může být tato vlastnost vynechána. Nainstalované řešení SNC obvykle zná vlastní název SNC. Pouze pro řešení, která podporují více identit, bude pravděpodobně nutné zadat identitu, která se má použít pro tento konkrétní cíl nebo server. |
   | **Název partnera SNC** | Název back-endu SNC. |
   | **SNC kvality ochrany** | Kvalita služby, která se má použít pro SNC komunikaci tohoto konkrétního cíle nebo serveru. Výchozí hodnota je definována back-end systémem. Maximální hodnota je definovaná produktem zabezpečení použitým pro SNC. |
   |||

   > [!NOTE]
   > Nenastavte proměnné prostředí SNC_LIB a SNC_LIB_64 na počítači, kde máte bránu dat a knihovnu SNC. Pokud je nastaveno, mají přednost před hodnotou SNC knihovny předané prostřednictvím konektoru.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Bezpečné zadání

Ve výchozím nastavení se při vytváření připojení SAP používá silné psaní ke kontrole neplatných hodnot prováděním ověřování XML proti schématu. Toto chování vám může přispět k detekci problémů dříve. Možnost **bezpečného psaní** je k dispozici pro zpětnou kompatibilitu a kontroluje pouze délku řetězce. Pokud zvolíte možnost **bezpečného psaní**, typ dat a typ TIMS v SAP se považují za řetězce, nikoli jako jejich ekvivalenty XML `xs:date` , `xs:time`a, `xmlns:xs="http://www.w3.org/2001/XMLSchema"`kde. Bezpečné psaní má vliv na chování pro všechna generování schématu, zprávu Send pro datovou část "byla odeslána" a "přijatá" odpověď a Trigger. 

Když se použije silné zadání (**bezpečné zadání** není povolené), schéma MAPUJE typy dat a TIMS na POKROČILEJŠÍ typy XML:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Když posíláte zprávy pomocí silného typování, odpověď TIMS a odpověď na data jsou v souladu s odpovídajícím formátem typu XML:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Když je povoleno **bezpečné psaní** , schéma MAPUJE typy dat a TIMS na pole řetězce XML s omezením délky, například:

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

Když se odesílají zprávy s povoleným bezpečným typováním, TIMS odpověď dat a odpovědí bude vypadat jako v tomto příkladu:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>Pokročilé scénáře

### <a name="confirm-transaction-explicitly"></a>Potvrdit transakci explicitně

Když odesíláte transakce do SAP z Logic Apps, k tomuto systému Exchange dochází ve dvou krocích, jak je popsáno v dokumentu SAP, v tématu [transakční aplikace RFC serveru](https://help.sap.com/doc/saphelp_nwpi71/7.1/en-US/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). Ve výchozím nastavení zpracuje akce **Odeslat do SAP** jak kroky pro přenos funkce, tak potvrzení transakce v jednom volání. Konektor SAP vám dává možnost oddělit tyto kroky. Můžete odeslat IDOC a místo toho, aby transakce byla automaticky potvrzena, můžete použít akci explicitní **potvrzení ID transakce** .

Tato možnost oddělit potvrzení ID transakce je užitečná v případě, že nechcete provádět duplicitní transakce v SAP, například ve scénářích, kdy může dojít k selhání z důvodu příčin potíží se sítí. Potvrzením ID transakce samostatně se transakce v systému SAP pouze v jednom okamžiku dokončí.

Tady je příklad, který ukazuje tento vzor:

1. Vytvořte prázdnou aplikaci logiky a přidejte Trigger HTTP.

1. Z konektoru SAP přidejte akci **Odeslat IDOC** . Zadejte podrobnosti pro IDOC, které jste odeslali do svého systému SAP.

1. Chcete-li explicitně potvrdit ID transakce v samostatném kroku, ve vlastnosti **Potvrdit TID** vyberte možnost **ne**. U vlastnosti **ID GUID volitelné transakce** můžete buď ručně zadat hodnotu, nebo nechat konektor automaticky generovat a vrátit tento identifikátor GUID v odpovědi z akce Odeslat IDOC.

   ![Odeslat vlastnosti akce IDOC](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. Chcete-li explicitně potvrdit ID transakce, přidejte akci **Potvrdit ID transakce** . Klikněte do pole **ID transakce** , aby se zobrazil seznam dynamického obsahu. V tomto seznamu vyberte hodnotu **ID transakce** , která se vrátí z akce **Odeslat IDOC** .

   ![Akce potvrzení ID transakce](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   Po spuštění tohoto kroku je aktuální transakce označena jako úplná na obou koncích na straně konektoru SAP a na straně systému SAP.

## <a name="known-issues-and-limitations"></a>Známé problémy a omezení

Tady jsou aktuálně známé problémy a omezení konektoru SAP:

* Aktivační událost SAP nepodporuje clustery brány dat. V některých případech převzetí služeb při selhání se uzel brány dat, který komunikuje se systémem SAP, může lišit od aktivního uzlu, což vede k neočekávanému chování. Pro scénáře odeslání se podporují clustery služby data Gateway.

* Konektor SAP aktuálně nepodporuje řetězce směrovače SAP. Místní brána dat musí existovat ve stejné síti LAN jako systém SAP, který chcete připojit.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu OpenAPI konektoru (dříve Swagger), najdete na [referenční stránce konektoru](/connectors/sap/).

## <a name="next-steps"></a>Další postup

* [Připojte se k místním systémům](../logic-apps/logic-apps-gateway-connection.md) z Azure Logic Apps.
* Naučte se, jak pomocí [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)ověřit, transformovat a používat jiné operace se zprávou.
* Přečtěte si o dalších [konektorech Logic Apps](../connectors/apis-list.md).
