---
title: Připojení k systémům SAP
description: Automatizace pracovních postupů pomocí Azure Logic Apps pro přístup k prostředkům SAP a jejich správa
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 07/21/2020
tags: connectors
ms.openlocfilehash: 4afd6f0cc3b4b5e135d80b420d8260c50d9ca46c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89488843"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Připojení k systémům SAP z Azure Logic Apps

> [!IMPORTANT]
> Předchozí konektory aplikačního serveru SAP a serveru zpráv SAP jsou zastaralé od 29. února 2020. Aktuální konektor SAP slučuje tyto předchozí konektory SAP, takže nemusíte měnit typ připojení, je plně kompatibilní s předchozími konektory, poskytuje mnoho dalších možností a pokračuje v používání knihovny SAP .NET Connector Library (SAP NCo).
>
> U Logic Apps, které používají starší konektory, [migrujte prosím na nejnovější konektor](#migrate) před datem vyřazení. V opačném případě se tyto aplikace logiky projeví při selhání spuštění a nebude možné odesílat zprávy do vašeho systému SAP.

Tento článek ukazuje, jak můžete přistupovat k místním prostředkům SAP zevnitř aplikace logiky pomocí konektoru SAP. Konektor pracuje s klasickými verzemi SAP, jako jsou R/3 a systémy ECC místně. Konektor umožňuje také integraci s novějšími systémy SAP založenými na HANA, jako je S/4 HANA, a to bez ohledu na to, jestli jsou hostované v místním prostředí, nebo v cloudu. Konektor SAP podporuje integraci dat nebo zpráv do a ze systémů SAP NetWeaver prostřednictvím formátu IDoc (Intermediate Document) nebo volání BAPI (Business Application Programming Interface) nebo RFC (Remote Function Call).

Konektor SAP používá [knihovnu SAP .NET Connector (NCo)](https://support.sap.com/en/product/connectors/msnet.html) a poskytuje tyto akce:

* **Odeslat zprávu do SAP**: Odeslat IDOC prostřednictvím tRFC, volat funkce BAPI přes RFC nebo volat RFC/tRFC v systémech SAP.

* **Při přijetí zprávy ze SAP**: přijmout IDOC over tRFC, volat funkce BAPI přes tRFC nebo volat RFC/tRFC v systémech SAP.

* **Generování schémat**: generování schémat pro artefakty SAP pro IDOC, BAPI nebo RFC.

Pro tyto operace konektor SAP podporuje základní ověřování prostřednictvím uživatelských jmen a hesel. Konektor podporuje také [zabezpečenou síťovou komunikaci (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). SNC se dá použít pro jednotné přihlašování SAP NetWeaver (SSO) nebo pro další funkce zabezpečení poskytované externím produktem zabezpečení.

V tomto článku se dozvíte, jak vytvořit ukázkové aplikace logiky, které se integrují s SAP a které pokrývají dříve popsané scénáře integrace. V tomto článku se pro Logic Apps, které používají starší konektory SAP, dozvíte, jak migrovat aplikace logiky na nejnovější konektor SAP.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Požadavky

Pokud chcete postupovat podle tohoto článku, budete potřebovat tyto položky:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [Zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky, ze které chcete získat přístup k systému SAP, a Trigger, který spouští pracovní postup vaší aplikace logiky. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md) a [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Aplikační Server SAP](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) nebo [Server zpráv SAP](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).

* Obsah zprávy, který odešlete do serveru SAP, jako je například ukázkový soubor IDoc, musí být ve formátu XML a zahrnovat obor názvů pro akci SAP, kterou chcete použít.

* Chcete-li použít **při přijetí zprávy z** triggeru SAP, je nutné provést tyto kroky nastavení:
  
  > [!NOTE]
  > Tato aktivační událost používá stejné umístění URI pro obnovení i zrušení odběru odběru Webhooku. Operace obnovení používá `PATCH` metodu HTTP, zatímco operace zrušení odběru používá `DELETE` metodu HTTP. Toto chování může vést k tomu, že operace obnovení se objeví jako operace zrušení odběru v historii triggeru, ale operace se pořád obnovuje, protože Trigger používá `PATCH` jako metodu HTTP, ne `DELETE` .

  * Nastavte oprávnění zabezpečení brány SAP pomocí tohoto nastavení:

    `"TP=Microsoft.PowerBI.EnterpriseGateway HOST=<gateway-server-IP-address> ACCESS=*"`

  * Nastavte protokolování zabezpečení brány SAP, které pomáhá najít chyby seznamu Access Control (ACL) a není ve výchozím nastavení povolené. V opačném případě se zobrazí následující chyba:

    `"Registration of tp Microsoft.PowerBI.EnterpriseGateway from host <host-name> not allowed"`

    Další informace najdete v tématu nápovědy SAP, [nastavení protokolování brány](https://help.sap.com/erp_hcm_ias2_2015_02/helpdata/en/48/b2a710ca1c3079e10000000a42189b/frameset.htm).

<a name="multi-tenant"></a>

### <a name="multi-tenant-azure-prerequisites"></a>Požadavky Azure na více tenantů

Tyto požadavky platí v případě, že vaše aplikace logiky běží ve více tenantů Azure a chcete použít spravovaný konektor SAP, který neběží nativně v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). V opačném případě, pokud používáte ISE úrovně Premium a chcete použít konektor SAP spouštěný nativně v ISE, přečtěte si část [požadavky na ISE (Integration Service Environment)](#sap-ise).

Spravovaný (bez ISE) konektor SAP se integruje s místními systémy SAP prostřednictvím místní [brány dat](../logic-apps/logic-apps-gateway-connection.md). Například při odeslání zprávy z aplikace logiky do systému SAP bude brána dat fungovat jako klient RFC a přepošle žádosti přijaté z aplikace logiky do SAP. Stejně tak v případě, že se zobrazují zprávy, brána dat funguje jako server RFC, který přijímá žádosti od SAP a přesměruje je do aplikace logiky.

* [Stáhněte a nainstalujte místní bránu dat](../logic-apps/logic-apps-gateway-install.md) do místního počítače. Pak [vytvořte prostředek brány Azure](../logic-apps/logic-apps-gateway-connection.md#create-azure-gateway-resource) pro tuto bránu v Azure Portal. Brána vám pomůže zabezpečený přístup k místním datům a prostředkům.

  V rámci osvědčeného postupu se ujistěte, že používáte podporovanou verzi místní brány dat. Společnost Microsoft každý měsíc vydává novou verzi. V současné době podporuje Microsoft poslední šest verzí. Pokud dojde k potížím s bránou, zkuste [upgradovat na nejnovější verzi](https://aka.ms/on-premises-data-gateway-installer), která může zahrnovat aktualizace pro vyřešení vašeho problému.

* [Stáhněte si a nainstalujte nejnovější knihovnu klienta SAP](#sap-client-library-prerequisites) do stejného počítače, ve kterém je místní brána dat.

<a name="sap-ise"></a>

### <a name="integration-service-environment-ise-prerequisites"></a>Požadavky na prostředí ISE (Integration Service Environment)

Tyto požadavky platí v případě, že aplikace logiky běží v [prostředí ISE (Integration Service Environment](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)) úrovně Premium a vy chcete použít konektor SAP, který běží nativně v ISE. ISE poskytuje přístup k prostředkům, které jsou chráněné službou Azure Virtual Network, a nabízí další ISEové konektory, které umožňují Logic Apps přímý přístup k místním prostředkům bez použití místní brány dat.

> [!NOTE]
> I když je konektor SAP ISE viditelný v ISE na úrovni vývojáře, pokusy o instalaci konektoru nebudou úspěšné.

1. Pokud ještě nemáte účet Azure Storage a kontejner objektů blob, vytvořte tento kontejner buď pomocí [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) nebo [Průzkumník služby Azure Storage](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

1. [Stáhněte a nainstalujte si nejnovější knihovnu klienta SAP](#sap-client-library-prerequisites) na místním počítači. Měli byste mít následující soubory sestavení:

   * libicudecnumber.dll
   * rscp4n.dll
   * sapnco.dll
   * sapnco_utils.dll

1. Vytvořte soubor. zip, který obsahuje tato sestavení a nahrajte tento balíček do kontejneru objektů BLOB v Azure Storage.

1. V Azure Portal nebo Průzkumník služby Azure Storage přejděte do umístění kontejneru, kam jste nahráli soubor. zip.

1. Zkopírujte adresu URL tohoto umístění a ujistěte se, že jste zahrnuli token sdíleného přístupového podpisu (SAS).

   V opačném případě token SAS nezíská autorizaci a nasazení konektoru SAP ISE se nezdaří.

1. Než budete moct použít konektor SAP ISE, musíte nainstalovat a nasadit konektor v ISE.

   1. V [Azure Portal](https://portal.azure.com)vyhledejte a otevřete své ISE.
   
   1. V nabídce ISE vyberte **spravované konektory**  >  **Přidat**. V seznamu konektory vyhledejte a vyberte **SAP**.
   
   1. V podokně **Přidat nový spravovaný konektor** do pole **balíček SAP** vložte adresu URL souboru. zip, který obsahuje sestavení SAP. *Ujistěte se, že jste zahrnuli token SAS.*

   1. Po dokončení vyberte **Vytvořit**.

   Další informace najdete v tématu [Přidání konektorů ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment).

1. Pokud se vaše instance SAP a ISE nacházejí v různých virtuálních sítích, musíte taky vytvořit [partnerský vztah k těmto sítím](../virtual-network/tutorial-connect-virtual-networks-portal.md) , aby byla vaše virtuální síť ISE připojená k virtuální síti vaší instance SAP.

<a name="sap-client-library-prerequisites"></a>

### <a name="sap-client-library-prerequisites"></a>Požadavky na knihovnu klienta SAP

* Ujistěte se, že jste nainstalovali nejnovější verzi nástroje [SAP Connector (NCo 3,0) pro Microsoft .NET 3.0.22.0 zkompilované pomocí .NET Framework 4,0-Windows 64-bit (x64)](https://support.sap.com/en/product/connectors/msnet.html). Starší verze můžou mít za následek problémy s kompatibilitou. Další informace najdete v tématu [verze klientské knihovny SAP](#sap-library-versions).

* Ve výchozím nastavení instalační program SAP umístí soubory sestavení do výchozí instalační složky. Tyto soubory sestavení je nutné zkopírovat do jiného umístění na základě vašeho scénáře, a to takto:

  * Pro Logic Apps, které běží na ISE, postupujte podle kroků popsaných v tématu [předpoklady pro prostředí integrační služby](#sap-ise). Pro aplikace logiky, které běží ve víceklientské architektuře Azure a používají místní bránu dat, zkopírujte soubory sestavení z výchozí instalační složky do instalační složky brány dat. Pokud narazíte na problémy s bránou data Gateway, přečtěte si následující problémy:

  * Pro klientskou knihovnu SAP je nutné nainstalovat 64 verzi, protože brána dat se spouští pouze v systémech 64. V opačném případě se zobrazí chyba "chybná image", protože hostitelská služba brány dat nepodporuje 32 bitových sestavení.

  * Pokud se připojení SAP nezdaří s chybovou zprávou, zkontrolujte prosím informace účtu nebo oprávnění a zkuste to znovu. soubory sestavení můžou být v nesprávném umístění. Ujistěte se, že jste zkopírovali soubory sestavení do instalační složky brány dat.

    Pro usnadnění odstraňování potíží [použijte prohlížeč protokolu vazby sestavení .NET](/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer), který vám umožní ověřit, zda jsou soubory sestavení ve správném umístění. Volitelně můžete vybrat možnost **registrace globální mezipaměti sestavení** při instalaci knihovny klienta SAP.

<a name="sap-library-versions"></a>

#### <a name="sap-client-library-versions"></a>Verze klientské knihovny SAP

Starší verze SAP NCo můžou být zablokované, když se současně pošle víc než jedna zpráva IDoc. Tento stav blokuje všechny pozdější zprávy, které se odesílají do cíle SAP, což způsobí vypršení časového limitu zpráv.

Tady jsou vztahy mezi klientskou knihovnou SAP, .NET Framework, modulem runtime .NET a bránou:

* Adaptér Microsoft SAP i hostitelská služba brány používají .NET Framework 4,5.

* SAP NCo for .NET Framework 4,0 funguje s procesy, které používají .NET Runtime 4,0 až 4.7.1.

* SAP NCo for .NET Framework 2,0 funguje s procesy, které používají .NET runtime 2,0 na 3,5, ale už nefungují s nejnovější bránou.

### <a name="secure-network-communications-prerequisites"></a>Požadavky na zabezpečenou síťovou komunikaci

Pokud používáte místní bránu dat s volitelnou zabezpečenou síťovou komunikací (SNC), která je podporovaná jenom ve více klientech Azure, musíte taky nakonfigurovat tato nastavení:

* Pokud používáte SNC s jednotným přihlašováním (SSO), ujistěte se, že je brána dat spuštěná jako uživatel, který je namapovaný na uživatele SAP. Chcete-li změnit výchozí účet, vyberte možnost **změnit účet**a zadejte přihlašovací údaje uživatele.

  ![Změnit účet brány dat](./media/logic-apps-using-sap-connector/gateway-account.png)

* Pokud SNC povolíte s externím produktem zabezpečení, zkopírujte knihovnu SNC nebo soubory na stejném počítači, kde je nainstalována brána dat. Mezi příklady SNC produktů patří [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos a NTLM.

Další informace o povolení SNC pro bránu dat najdete v tématu [Povolení zabezpečené síťové komunikace](#secure-network-communications).

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>Migrovat na aktuální konektor

Pokud chcete migrovat ze staršího konektoru SAP spravovaného (bez ISE) na aktuální spravovaný konektor SAP, postupujte podle těchto kroků:

1. Pokud jste to ještě neudělali, aktualizujte místní [bránu dat](https://www.microsoft.com/download/details.aspx?id=53127) tak, aby byla k dispozici nejnovější verze. Další informace najdete v tématu [instalace místní brány dat pro Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

1. V aplikaci logiky, která používá starší konektor SAP, odstraňte akci **Odeslat do SAP** .

1. Z nejnovějšího konektoru SAP přidejte akci **Odeslat zprávu do SAP** . Než budete moct tuto akci použít, znovu vytvořte připojení k vašemu systému SAP.

1. Až budete hotovi, uložte aplikaci logiky.

<a name="add-trigger"></a>

## <a name="send-message-to-sap"></a>Odeslat zprávu do SAP

V tomto příkladu se používá aplikace logiky, kterou můžete aktivovat pomocí požadavku HTTP. Aplikace logiky pošle IDoc serveru SAP a vrátí odpověď žadateli, který se nazývá aplikace logiky.

### <a name="add-an-http-request-trigger"></a>Přidání triggeru požadavku HTTP

V Azure Logic Apps musí každá aplikace logiky začínat [triggerem](../logic-apps/logic-apps-overview.md#logic-app-concepts), který se aktivuje, když dojde ke konkrétní události nebo když dojde ke splnění určité podmínky. Pokaždé, když se Trigger aktivuje, modul Logic Apps vytvoří instanci aplikace logiky a začne spouštět pracovní postup vaší aplikace.

> [!NOTE]
> Když aplikace logiky přijme IDoc pakety z SAP, [aktivační událost žádosti](../connectors/connectors-native-reqres.md) nepodporuje "jednoduché" schéma XML generované v dokumentaci ke službě SAP WE60 IDOC. Schéma XML "obyčejného" je však podporováno pro scénáře, které odesílají zprávy z Logic Apps *do* SAP. Můžete použít Trigger žádosti s IDoc XML SAP, ale ne s IDoc přes RFC. Nebo můžete transformovat XML na potřebný formát. 

V tomto příkladu vytvoříte aplikaci logiky s koncovým bodem v Azure, abyste mohli odesílat *požadavky HTTP POST* do vaší aplikace logiky. Když aplikace logiky obdrží tyto požadavky HTTP, Trigger se aktivuje a spustí další krok v pracovním postupu.

1. V [Azure Portal](https://portal.azure.com)vytvořte prázdnou aplikaci logiky, která otevře návrháře aplikace logiky.

1. Do vyhledávacího pole zadejte `http request` jako filtr. V seznamu **triggery** vyberte, **kdy se přijme požadavek HTTP**.

   ![Přidat aktivační událost požadavku HTTP](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Nyní uložte aplikaci logiky, abyste mohli vygenerovat adresu URL koncového bodu pro vaši aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

   Adresa URL koncového bodu se teď zobrazí ve triggeru, například:

   ![Vygenerovat adresu URL pro koncový bod](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>Přidat akci SAP

V Azure Logic Apps [Akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je krok v pracovním postupu, který následuje po triggeru nebo jiné akci. Pokud jste ještě nepřidali Trigger do aplikace logiky a chcete postupovat podle tohoto příkladu, [přidejte Trigger popsaný v této části](#add-trigger).

1. V návrháři aplikace logiky pod triggerem vyberte **Nový krok**.

   ![Přidat nový krok do aplikace logiky](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Do vyhledávacího pole zadejte `sap` jako filtr. V seznamu **Akce** vyberte **Odeslat zprávu do SAP**.
  
   ![Vyberte akci odeslat zprávu do SAP.](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Nebo můžete vybrat kartu **Enterprise** a pak vybrat akci SAP.

   ![Výběr akce Odeslat zprávu na SAP na kartě organizace](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Pokud vaše připojení už existuje, pokračujte dalším krokem, abyste mohli nastavit akci SAP. Pokud se ale zobrazí výzva k zadání podrobností o připojení, zadejte informace, abyste mohli vytvořit připojení k místnímu serveru SAP.

   1. Zadejte název připojení.

   1. Pokud používáte bránu dat, postupujte následovně:
   
      1. V části **Brána dat** v části **předplatné**nejdřív vyberte předplatné Azure pro prostředek brány dat, který jste vytvořili v Azure Portal pro instalaci brány dat.
   
      1. V části **Brána připojení**vyberte prostředek brány dat v Azure.

   1. Pokračujte v poskytování informací o připojení. U vlastnosti **typ přihlášení** postupujte podle kroků na základě toho, jestli je vlastnost nastavená na **aplikační server** nebo **skupinu**:
   
      * U **aplikačního serveru**se tyto vlastnosti, které se obvykle zobrazují jako volitelné, vyžadují:

        ![Vytvořit připojení aplikačního serveru SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * V případě **skupiny**jsou vyžadovány tyto vlastnosti, které se obvykle zobrazují jako volitelné:

        ![Vytvořit připojení k serveru zpráv SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Ve výchozím nastavení se silné zadání používá ke kontrole neplatných hodnot prováděním ověřování XML proti schématu. Toto chování vám může přispět k detekci problémů dříve. Možnost **bezpečného psaní** je k dispozici pro zpětnou kompatibilitu a kontroluje pouze délku řetězce. Přečtěte si další informace o [možnosti bezpečného psaní](#safe-typing).

   1. Až budete hotovi, vyberte **vytvořit**.

      Logic Apps nastaví a otestuje připojení, aby se zajistilo správné fungování připojení.

1. Nyní vyhledejte a vyberte akci ze serveru SAP.

   1. V poli **Akce SAP** vyberte ikonu složky. V seznamu soubor vyhledejte a vyberte zprávu SAP, kterou chcete použít. Chcete-li procházet seznam, použijte šipky.

      Tento příklad vybere IDoc s typem **objednávky** .

      ![Najít a vybrat akci IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Pokud požadovanou akci nemůžete najít, můžete zadat cestu ručně, například:

      ![Ručně zadat cestu k IDoc akci](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Zadejte hodnotu pro **akci SAP** prostřednictvím editoru výrazů. Tímto způsobem můžete použít stejnou akci u různých typů zpráv.

      Další informace o operacích IDoc najdete v tématu [schémata zpráv pro operace IDOC](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Klikněte do pole **vstupní zpráva** , aby se zobrazil seznam dynamického obsahu. V tomto seznamu v části **když se přijme požadavek HTTP**, vyberte pole **body** .

      Tento krok zahrnuje obsah zprávy z triggeru požadavku HTTP a odesílá tento výstup do vašeho serveru SAP.

      ![Vybrat vlastnost "tělo" z triggeru](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Až budete hotovi, vaše akce SAP bude vypadat jako v tomto příkladu:

      ![Dokončení akce SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Přidat akci odpovědi HTTP

Teď přidejte akci odpovědi do pracovního postupu aplikace logiky a zahrňte výstup z akce SAP. Vaše aplikace logiky tak vrátí výsledky ze serveru SAP původnímu žadateli.

1. V návrháři aplikace logiky v akci SAP vyberte **Nový krok**.

1. Do vyhledávacího pole zadejte `response` jako filtr. V seznamu **Akce** vyberte možnost **odpověď**.

1. Klikněte do pole **text** , aby se zobrazil seznam dynamického obsahu. V tomto seznamu v části **Odeslat zprávu do SAP**vyberte pole **text** .

   ![Dokončit akci SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Uložte aplikaci logiky.

#### <a name="add-rfc-request-response"></a>Přidat požadavek RFC – odpověď

> [!NOTE]
> Aktivační událost SAP přijímá IDocs nad tRFC, která nemá parametr odpovědi navržený. 

Je nutné vytvořit vzor požadavků a odpovědí, pokud potřebujete přijmout odpovědi pomocí vzdáleného volání funkce (RFC) a Logic Apps ze SAP ABAP. Pokud chcete přijímat IDocs ve vaší aplikaci logiky, měli byste provést první akci [požadavku HTTP](../connectors/connectors-native-reqres.md#add-a-response-action) se stavovým kódem `200 OK` a bez obsahu. Tento doporučený krok dokončí Asynchronní přenos SAP LUW přes tRFC hned, což ponechá konverzaci SAP CPIC k dispozici. Potom můžete do aplikace logiky přidat další akce pro zpracování přijatých IDoc bez blokování dalších přenosů.

Pokud chcete implementovat vzor požadavků a odpovědí, musíte nejdřív zjistit schéma RFC pomocí [ `generate schema` příkazu](#generate-schemas-for-artifacts-in-sap). Vygenerované schéma má dva možné kořenové uzly: 

1. Uzel žádosti, což je volání, které obdržíte od SAP.
1. Uzel odpovědi, který odpovídá vaší odpovědi zpět na SAP.

V následujícím příkladu se v modulu RFC generuje vzor požadavků a odpovědí `STFC_CONNECTION` . Požadavek XML je analyzován za účelem extrakce hodnoty uzlu, ve které požadavky SAP `<ECHOTEXT>` . Odpověď vloží aktuální časové razítko jako dynamickou hodnotu. Po odeslání `STFC_CONNECTION` dokumentu RFC z aplikace logiky do SAP obdržíte podobnou odpověď.

```http

<STFC_CONNECTIONResponse xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <ECHOTEXT>@{first(xpath(xml(triggerBody()?['Content']), '/*[local-name()="STFC_CONNECTION"]/*[local-name()="REQUTEXT"]/text()'))}</ECHOTEXT>
  <RESPTEXT>Azure Logic Apps @{utcNow()}</RESPTEXT>


```

### <a name="test-your-logic-app"></a>Testování aplikace logiky

1. Pokud vaše aplikace logiky ještě není povolená, vyberte v nabídce aplikace logiky **Přehled**. Na panelu nástrojů vyberte **Povolit**.

1. Na panelu nástrojů návrháře vyberte **Spustit**. Tento krok ručně spustí vaši aplikaci logiky.

1. Aktivujte aplikaci logiky odesláním požadavku HTTP POST na adresu URL v triggeru požadavku HTTP.
Přidejte obsah zprávy s vaším požadavkem. K odeslání žádosti můžete použít nástroj, jako je například [post](https://www.getpostman.com/apps).

   V tomto článku požadavek pošle soubor IDoc, který musí být ve formátu XML a zahrnuje obor názvů pro akci SAP, kterou používáte, například:

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. Až odešlete požadavek HTTP, počkejte na odpověď z vaší aplikace logiky.

   > [!NOTE]
   > Vaše aplikace logiky může vypršet, pokud všechny kroky požadované pro odpověď nekončí v rámci [časového](./logic-apps-limits-and-config.md)limitu požadavku. Pokud k tomuto stavu dojde, můžou se požadavky zablokovat. Pro usnadnění diagnostiky problémů se dozvíte, jak můžete [kontrolovat a monitorovat aplikace logiky](../logic-apps/monitor-logic-apps.md).

Nyní jste vytvořili aplikaci logiky, která může komunikovat se serverem SAP. Teď, když jste nastavili připojení SAP pro vaši aplikaci logiky, můžete prozkoumat další dostupné akce SAP, například BAPI a RFC.

<a name="receive-from-sap"></a>

## <a name="receive-message-from-sap"></a>Přijmout zprávu z SAP

V tomto příkladu se používá aplikace logiky, která se aktivuje, když aplikace obdrží zprávu ze systému SAP.

### <a name="add-an-sap-trigger"></a>Přidání triggeru SAP

1. V Azure Portal vytvořte prázdnou aplikaci logiky, která otevře návrháře aplikace logiky.

1. Do vyhledávacího pole zadejte `sap` jako filtr. V seznamu **triggery** vyberte, **kdy se má ze SAP přijmout zpráva**.

   ![Přidat aktivační událost SAP](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   Případně můžete vybrat kartu **Enterprise** a potom vybrat aktivační událost:

   ![Přidat aktivační událost SAP z karty Enterprise](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Pokud vaše připojení už existuje, pokračujte dalším krokem, abyste mohli nastavit akci SAP. Pokud se však zobrazí výzva k zadání podrobností o připojení, zadejte informace, abyste mohli nyní vytvořit připojení k místnímu serveru SAP.

   1. Zadejte název připojení.

   1. Pokud používáte bránu dat, postupujte následovně:

      1. V části **Brána dat** v části **předplatné**nejdřív vyberte předplatné Azure pro prostředek brány dat, který jste vytvořili v Azure Portal pro instalaci brány dat.

      1. V části **Brána připojení**vyberte prostředek brány dat v Azure.

   1. Pokračujte v poskytování informací o připojení. U vlastnosti **typ přihlášení** postupujte podle kroků na základě toho, jestli je vlastnost nastavená na **aplikační server** nebo **skupinu**:

      * U **aplikačního serveru**se tyto vlastnosti, které se obvykle zobrazují jako volitelné, vyžadují:

        ![Vytvořit připojení aplikačního serveru SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * V případě **skupiny**jsou vyžadovány tyto vlastnosti, které se obvykle zobrazují jako volitelné:

        ![Vytvořit připojení k serveru zpráv SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Ve výchozím nastavení se silné zadání používá ke kontrole neplatných hodnot prováděním ověřování XML proti schématu. Toto chování vám může přispět k detekci problémů dříve. Možnost **bezpečného psaní** je k dispozici pro zpětnou kompatibilitu a kontroluje pouze délku řetězce. Přečtěte si další informace o [možnosti bezpečného psaní](#safe-typing).

   1. Až budete hotovi, vyberte **vytvořit**.

      Logic Apps nastaví a otestuje připojení, aby se zajistilo správné fungování připojení.

1. Zadejte [požadované parametry](#parameters) na základě konfigurace systému SAP. 

   Můžete [filtrovat zprávy, které obdržíte ze serveru SAP, zadáním seznamu akcí SAP](#filter-with-sap-actions).

   Akci SAP můžete vybrat z výběru souboru:

   ![Přidat akci SAP do aplikace logiky](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Nebo můžete ručně zadat akci:

   ![Ručně zadejte akci SAP, kterou chcete použít.](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Tady je příklad, který ukazuje, jak se akce zobrazí při nastavení triggeru pro příjem více než jedné zprávy.

   ![Příklad triggeru, který přijímá více zpráv](media/logic-apps-using-sap-connector/example-trigger.png)

   Další informace o akci SAP najdete v tématu [schémata zpráv pro IDOC operace](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations) .

1. Nyní uložte aplikaci logiky, abyste mohli začít přijímat zprávy ze systému SAP. Na panelu nástrojů návrháře vyberte **Uložit**.

Vaše aplikace logiky je teď připravená přijímat zprávy ze systému SAP.

> [!NOTE]
> Trigger SAP není Trigger cyklického dotazování, ale je místo toho Trigger založený na Webhooku. Pokud používáte bránu dat, Trigger se z brány dat zavolá jenom v případě, že existuje zpráva, takže není potřeba žádné cyklické dotazování.

<a name="parameters"></a>

#### <a name="parameters"></a>Parametry

Spolu s jednoduchým řetězcem a číselnými vstupy akceptuje konektor SAP následující parametry tabulky ( `Type=ITAB` vstupy):

* Parametry směru tabulky, vstupní i výstupní pro starší verze SAP.
* Změna parametrů, které nahradí parametry směru tabulky pro novější verze SAP.
* Parametry hierarchické tabulky

<a name="filter-with-sap-actions"></a>

#### <a name="filter-with-sap-actions"></a>Filtrování pomocí akcí SAP

Volitelně můžete filtrovat zprávy, které aplikace logiky přijme ze serveru SAP, zadáním seznamu nebo pole s jednou nebo několika akcemi SAP. Ve výchozím nastavení je toto pole prázdné, což znamená, že aplikace logiky obdrží všechny zprávy ze serveru SAP bez filtrování. 

Když nastavíte filtr pole, aktivační událost přijímá jenom zprávy ze zadaných typů akcí SAP a odmítne všechny ostatní zprávy ze serveru SAP. Tento filtr ale nemá vliv na to, jestli je text obdržené datové části slabý nebo silný.

Jakékoli filtrování akcí SAP se stane na úrovni adaptéru SAP pro vaši místní bránu dat. Další informace najdete v tématu [Jak odeslat testovací IDOCs do Logic Apps z SAP](#send-idocs-from-sap).

Pokud nemůžete odesílat IDoc pakety z SAP do triggeru vaší aplikace logiky, přečtěte si zprávu o odmítnutí volání transakčního RFC (tRFC) v dialogovém okně SAP tRFC (T-Code SM58). V rozhraní SAP se může zobrazit následující chybové zprávy, které jsou oříznuté z důvodu omezení podřetězců v **textovém poli Stav** .

* `The RequestContext on the IReplyChannel was closed without a reply being`: K neočekávaným chybám dochází, když popisovač catch-All pro kanál ukončí kanál z důvodu chyby a znovu sestaví kanál pro zpracování dalších zpráv.

  * Pokud chcete potvrdit, že aplikace logiky přijala IDoc, [přidejte akci odpovědi](../connectors/connectors-native-reqres.md#add-a-response-action) , která vrátí `200 OK` stavový kód. IDoc je přepravován prostřednictvím tRFC, což nepovoluje datovou část odpovědi.

  * Pokud potřebujete IDoc místo toho, odpovězte s jakýmkoli stavovým kódem HTTP, který je jiný než `200 OK` tak, že adaptér SAP vrátí výjimku zpět do SAP vaším jménem. 

* `The segment or group definition E2EDK36001 was not found in the IDoc meta`: K očekávaným chybám dochází s jinými chybami, jako je například selhání při generování datové části XML IDoc, protože její segmenty nejsou uvolněny SAP, takže chybí metadata typu segmentu požadovaná pro převod. 

  * Pokud chcete tyto segmenty uvolnit pomocí SAP, obraťte se na pracovníky ABAP pro váš systém SAP.

<a name="find-extended-error-logs"></a>

## <a name="find-extended-error-logs"></a>Najít rozšířené protokoly chyb

V případě úplných chybových zpráv se podívejte na rozšířené protokoly adaptéru SAP. 

V případě verzí pro místní bránu dat od června 2020 a novějších můžete [Povolit protokoly brány v nastavení aplikace](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app).

V případě verzí pro místní bránu dat od dubna 2020 a novějších jsou protokoly ve výchozím nastavení zakázané. Pokud chcete načíst rozšířené protokoly, postupujte podle těchto kroků:

1. V instalační složce místní brány dat otevřete `Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config` soubor. 

1. Pro nastavení **SapExtendedTracing** změňte hodnotu z **false** na **true**.

1. Volitelně můžete pro méně událostí změnit hodnotu **SapTracingLevel** z **informativní** (výchozí) na **Error** nebo **Warning**. Nebo pro další události změňte **informativní** na **verbose**.

1. Uložte konfigurační soubor.

1. Restartujte bránu dat. Otevřete aplikaci pro místní instalaci brány dat a přejděte do nabídky **nastavení služby** . V části **restartovat bránu**vyberte **restartovat nyní**.

1. Reprodukování problému.

1. Exportujte protokoly brány. V aplikaci pro instalaci brány dat přejděte do nabídky **Diagnostika** . V části **protokoly brány**vyberte **exportovat protokoly**. Tyto soubory obsahují protokoly SAP seřazené podle data. V závislosti na velikosti protokolu můžou existovat pro jedno datum více souborů protokolu.

1. V konfiguračním souboru vraťte nastavení **SapExtendedTracing** na **hodnotu false**.

1. Restartujte službu brány.

### <a name="test-your-logic-app"></a>Testování aplikace logiky

1. Pokud chcete aktivovat aplikaci logiky, odešlete zprávu ze systému SAP.

1. V nabídce aplikace logiky vyberte **Přehled**. Podívejte se na **historii spuštění** pro jakékoli nové běhy aplikace logiky.

1. Otevřete poslední spuštění, ve kterém se zobrazí zpráva odeslaná ze systému SAP v oddílu aktivační výstupy triggeru.

<a name="send-idocs-from-sap"></a>

### <a name="test-sending-idocs-from-sap"></a>Test odesílání IDocs ze SAP

K odeslání IDocs z SAP do aplikace logiky potřebujete následující minimální konfiguraci:

> [!IMPORTANT]
> Tyto kroky použijte jenom v případě, že testujete konfiguraci SAP pomocí aplikace logiky. Provozní prostředí vyžadují další konfiguraci.

1. [Konfigurace cíle RFC v SAP](#create-rfc-destination)

1. [Vytvoření připojení ABAP k cíli RFC](#create-abap-connection)

1. [Vytvoření portu přijímače](#create-receiver-port)

1. [Vytvořit port odesílatele](#create-sender-port)

1. [Vytvoření partnera logického systému](#create-logical-system-partner)

1. [Vytvořit profil partnera](#create-partner-profiles)

1. [Testování odesílání zpráv](#test-sending-messages)

#### <a name="create-rfc-destination"></a>Vytvořit cíl RFC

1. Pokud chcete otevřít **konfiguraci nastavení připojení RFC** , v rozhraní SAP použijte kód transakce **sm59** (T-Code) s předponou **/n** .

1. Vyberte **připojení TCP/IP**  >  **vytvořit**.

1. Vytvořte nový cíl RFC s následujícím nastavením:
    
    * Jako **cíl RFC**zadejte název.
    
    * Na kartě **technické nastavení** pro **typ aktivace**vyberte **registrovaný serverový program**. Jako **ID programu**zadejte hodnotu. V SAP se Trigger vaší aplikace logiky zaregistruje pomocí tohoto identifikátoru.
    
    * Na kartě **Unicode** pro **typ komunikace s cílovým systémem**vyberte **Unicode**.

1. Uložte provedené změny.

1. Zaregistrujte nové **ID programu** pomocí Azure Logic Apps.

1. Chcete-li otestovat připojení, vyberte v rozhraní SAP v části nový **cíl RFC**možnost **Test připojení**.

#### <a name="create-abap-connection"></a>Vytvořit připojení ABAP

1. Pokud chcete otevřít **konfiguraci nastavení připojení RFC** , v rozhraní SAP použijte kód transakce **Sm59*** (T-Code) s předponou **/n** .

1. Vyberte **připojení ABAP**  >  **vytvořit**.

1. V části **cíl RFC**zadejte identifikátor [testovacího systému SAP](#create-rfc-destination).

1. Uložte provedené změny.

1. Chcete-li otestovat připojení, vyberte možnost **Test připojení**.

#### <a name="create-receiver-port"></a>Vytvořit port přijímače

1. Chcete-li otevřít **porty v nastavení zpracování IDOC** , v rozhraní SAP použijte kód transakce **WE21** (T-Code) s předponou **/n** .

1. Vyberte **porty**  >  **transakční RFC**  >  **vytvořit**.

1. V okně nastavení, které se otevře, vyberte **vlastní název portu**. Jako port testu zadejte **název**. Uložte provedené změny.

1. V nastavení pro nový port přijímače pro **specifikaci RFC**zadejte do pole cíl [RFC testu](#create-rfc-destination)identifikátor.

1. Uložte provedené změny.

#### <a name="create-sender-port"></a>Vytvořit port odesílatele

1.  Chcete-li otevřít **porty v nastavení zpracování IDOC** , v rozhraní SAP použijte kód transakce **WE21** (T-Code) s předponou **/n** .

1. Vyberte **porty**  >  **transakční RFC**  >  **vytvořit**.

1. V okně nastavení, které se otevře, vyberte **vlastní název portu**. Pro port testu zadejte **název** , který začíná na **SAP**. Všechny názvy portů odesílatele musí začínat písmeny **SAP**, například **SAPTEST**. Uložte provedené změny.

1. V nastavení pro nový port odesílatele zadejte do pole **cíl RFC**zadání identifikátoru pro [připojení ABAP](#create-abap-connection).

1. Uložte provedené změny.

#### <a name="create-logical-system-partner"></a>Vytvořit partnera logického systému

1. Chcete-li otevřít **zobrazení změna "logické systémy": Přehled** nastavení v rozhraní SAP, použijte kód transakce **bd54** (T-Code).

1. Přijměte zobrazenou zprávu upozornění: **Upozornění: tabulka je mezi klientem**

1. Nad seznamem, který zobrazuje vaše existující logické systémy, vyberte **nové položky**.

1. Pro nový logický systém zadejte **Log.Sysidentifikátor tem** a popis krátkého **názvu** . Uložte provedené změny.

1. Po zobrazení **výzvy pro Workbench** vytvořit novou žádost zadáním popisu, nebo pokud jste už žádost vytvořili, tento krok přeskočte.

1. Po vytvoření žádosti Workbench propojte požadavek na požadavek na aktualizaci tabulky. Pokud chcete potvrdit, že se tabulka aktualizovala, uložte změny.

#### <a name="create-partner-profiles"></a>Vytvoření profilů partnerů

V produkčních prostředích musíte vytvořit dva profily partnerů. První profil je pro odesílatele, který je vaší organizací a systémem SAP. Druhý profil je pro příjemce, což je vaše aplikace logiky.

1. Pokud chcete otevřít nastavení **partnerských profilů** , použijte v rozhraní SAP kód transakce **we20** (T-Code) s předponou **/n** .

1. V části **profily partnerů**vyberte **Typ partnera LS**  >  **vytvořit**.

1. Vytvořte nový profil partnera s následujícím nastavením:

    * Jako **partner – ne.** zadejte [identifikátor vašeho partnera logického systému](#create-logical-system-partner).

    * Pro **partn. Zadejte**, zadejte **ls**.

    * Pro **agenta**zadejte identifikátor pro uživatelský účet SAP, který se má použít při registraci identifikátorů programu pro Azure Logic Apps nebo jiné systémy bez SAP.

1. Uložte provedené změny. Pokud jste [nevytvořili partnera logického systému](#create-logical-system-partner), zobrazí se chyba, **Zadejte platné číslo partnera**.

1. V nastavení vašeho partnerského profilu v části **odchozí parmtrs**vyberte **vytvořit odchozí parametr**.

1. Vytvořte nový odchozí parametr s následujícím nastavením:

    * Zadejte **typ zprávy**, například **CREMAS**.

    * Zadejte [identifikátor portu přijímače](#create-receiver-port).

    * Zadejte IDoc velikost **balíčku. Velikost**. Nebo pokud chcete [Odeslat IDOCs po jednom z SAP](#receive-idoc-packets-from-sap), vyberte možnost **předat IDOC hned**po druhém.

1. Uložte provedené změny.

#### <a name="test-sending-messages"></a>Testování odesílání zpráv

1. Chcete-li otevřít **Nástroj test Tool pro IDOC zpracování** , v rozhraní SAP použijte kód transakce **we19** (T-Code) s předponou **/n** .

1. V části **Šablona pro test**vyberte **typ zprávy**a zadejte typ zprávy, například **CREMAS**. Vyberte **Vytvořit**.

1. Výběrem možnosti **pokračovat**potvrďte, **který typ IDOC?** zpráva.

1. Vyberte uzel **EDIDC** . Zadejte odpovídající hodnoty pro port příjemce a odesilatele. Vyberte **Pokračovat**.

1. Vyberte **standardní odchozí zpracování**.

1. Pokud chcete spustit odchozí IDoc zpracování, vyberte **pokračovat**. Po dokončení zpracování se zobrazí zpráva **IDOC odeslána do systému SAP nebo externího programu** .

1.  Chcete-li zjistit chyby zpracování, použijte kód transakce **SM58** (T-Code) s předponou **/n** .

## <a name="receive-idoc-packets-from-sap"></a>Příjem paketů IDoc ze SAP

Můžete nastavit SAP pro [posílání IDOCs v paketech](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/4ab38886549a6d8ce10000000a42189c.html), což jsou dávky nebo skupiny IDOCs. Aby bylo možné přijímat pakety IDoc, konektor SAP a konkrétně Trigger nevyžadují další konfiguraci. Pokud však chcete zpracovat každou položku v paketu IDoc poté, co Trigger obdrží paket, je nutné provést některé další kroky pro rozdělení paketu do jednotlivých IDocs.

Tady je příklad, který ukazuje, jak extrahovat jednotlivé IDocs z paketu pomocí [ `xpath()` funkce](./workflow-definition-language-functions-reference.md#xpath):

1. Než začnete, budete potřebovat aplikaci logiky s triggerem SAP. Pokud tuto aplikaci logiky ještě nemáte, pomocí předchozích kroků v tomto tématu [nastavte aplikaci logiky pomocí triggeru SAP](#receive-from-sap).

   Například:

   ![Přidání triggeru SAP do aplikace logiky](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. Získejte kořenový obor názvů z XML IDoc, který vaše aplikace logiky obdrží od SAP. Chcete-li tento obor názvů extrahovat z dokumentu XML, přidejte krok, který vytvoří místní řetězcovou proměnnou a uloží tento obor názvů pomocí `xpath()` výrazu:

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Získat kořenový obor názvů z IDoc](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Chcete-li extrahovat jednotlivé IDoc, přidejte krok, který vytvoří proměnnou pole a uloží kolekci IDoc pomocí jiného `xpath()` výrazu:

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Získat pole položek](./media/logic-apps-using-sap-connector/get-array.png)

   Proměnná Array zpřístupňuje jednotlivé IDoc pro vaši aplikaci logiky, aby je bylo možné zpracovat individuálně pomocí výčtu kolekce. V tomto příkladu aplikace logiky přenáší jednotlivé IDoc na server SFTP pomocí smyčky:

   ![Odeslání IDoc serveru SFTP](./media/logic-apps-using-sap-connector/loop-batch.png)

   Každý IDoc musí zahrnovat kořenový obor názvů, což je důvod, proč je obsah souboru zabalen uvnitř `<Receive></Receive` elementu společně s kořenovým oborem názvů před odesláním IDOC do aplikace pro příjem dat nebo serveru SFTP v tomto případě.

Můžete použít šablonu pro rychlý Start pro tento model výběrem této šablony v návrháři aplikace logiky při vytváření nové aplikace logiky.

![Vybrat šablonu aplikace Batch Logic](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Generování schémat pro artefakty v SAP

V tomto příkladu se používá aplikace logiky, kterou můžete aktivovat pomocí požadavku HTTP. Chcete-li generovat schémata pro zadané IDoc a BAPI, odešle **schéma** akce SAP do systému SAP požadavek.

Tato akce SAP vrátí [schéma XML](#sample-xml-schemas), nikoli obsah nebo data samotného dokumentu XML. Schémata vrácená v odpovědi se odesílají na účet pro integraci pomocí konektoru Azure Resource Manager. Schémata obsahují tyto části:

* Struktura zprávy požadavku. Tyto informace slouží k vytvoření seznamu BAPI `get` .
* Struktura zprávy s odpovědí. Tyto informace slouží k analýze odpovědi. 

K odeslání zprávy s požadavkem použijte obecnou akci SAP **Odeslat zprávu do SAP**nebo cílené akce **BAPI volání** .

### <a name="sample-xml-schemas"></a>Vzorová schémata XML

Pokud se naučíte generovat schéma XML pro použití při vytváření ukázkového dokumentu, přečtěte si následující ukázky. Tyto příklady ukazují, jak můžete pracovat s mnoha typy datových částí, včetně:

* [Žádosti RFC](#xml-samples-for-rfc-requests)
* [Žádosti BAPI](#xml-samples-for-bapi-requests)
* [Žádosti IDoc](#xml-samples-for-idoc-requests)
* Jednoduché nebo složité datové typy schématu XML
* Parametry tabulky
* Volitelné chování XML

Schéma XML můžete začít pomocí volitelného prologu XML. Konektor SAP funguje s prologem XML nebo bez něj.

```xml

<?xml version="1.0" encoding="utf-8">

```

#### <a name="xml-samples-for-rfc-requests"></a>Ukázky XML pro žádosti RFC

Následující příklad je základní volání RFC. Název RFC je `STFC_CONNECTION` . Tato žádost používá výchozí obor názvů `xmlns=` , ale můžete přiřadit a používat aliasy oboru názvů, jako je `xmmlns:exampleAlias=` . Hodnota oboru názvů je obor názvů pro všechny dokumenty RFC v SAP pro služby společnosti Microsoft. V požadavku je jednoduchý vstupní parametr `<REQUTEXT>` .

```xml

<STFC_CONNECTION xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <REQUTEXT>exampleInput</REQUTEXT>
</STFC_CONNECTION>

```

V následujícím příkladu je volání RFC s parametrem tabulky. Toto ukázkové volání a jeho skupina testovacích dokumentů RFC jsou k dispozici jako součást všech systémů SAP. Název parametru tabulky je `TCPICDAT` . Typ řádku tabulky je `ABAPTEXT` a tento element se opakuje pro každý řádek v tabulce. Tento příklad obsahuje jeden řádek s názvem `LINE` . Žádosti s parametrem tabulky mohou obsahovat libovolný počet polí, kde číslo je kladné celé číslo (*n*). 

```xml

<STFC_WRITE_TO_TCPIC xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <RESTART_QNAME>exampleQName</RESTART_QNAME>
    <TCPICDAT>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput1</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput2</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput3</LINE>
      </ABAPTEXT>
    </TCPICDAT>
</STFC_WRITE_TO_TCPIC>

```

V následujícím příkladu je volání RFC s parametrem tabulky, který má anonymní pole. Anonymní pole je v případě, že k poli není přiřazen žádný název. Komplexní typy jsou deklarovány v rámci samostatného oboru názvů, ve kterém deklarace nastaví novou výchozí hodnotu pro aktuální uzel a všechny jeho podřízené prvky. V příkladu se používá hexadecimální kód `x002F` jako řídicí znak pro symbol */* , protože tento symbol je vyhrazen v názvu pole SAP.

```xml

<RFC_XML_TEST_1 xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <IM_XML_TABLE>
    <RFC_XMLCNT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
      <_x002F_AnonymousField>exampleFieldInput</_x002F_AnonymousField>
    </RFC_XMLCNT>
  </IM_XML_TABLE>
</RFC_XML_TEST_1>

```

Následující příklad obsahuje předpony pro obory názvů. Můžete deklarovat všechny předpony najednou nebo můžete deklarovat libovolný počet předpon jako atributy uzlu. Alias oboru názvů RFC `ns0` se používá jako kořenový adresář a parametry pro základní typ.

> [!NOTE]
> komplexní typy jsou deklarovány v rámci jiného oboru názvů pro typy RFC s aliasem `ns3` namísto regulárního oboru názvů RFC s aliasem `ns0` .

```xml

<ns0:BBP_RFC_READ_TABLE xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Rfc/" xmlns:ns3="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc/">
  <ns0:DELIMITER>0</ns0:DELIMITER>
  <ns0:QUERY_TABLE>KNA1</ns0:QUERY_TABLE>
  <ns0:ROWCOUNT>250</ns0:ROWCOUNT>
  <ns0:ROWSKIPS>0</ns0:ROWSKIPS>
  <ns0:FIELDS>
    <ns3:RFC_DB_FLD>
      <ns3:FIELDNAME>KUNNR</ns3:FIELDNAME>
    </ns3:RFC_DB_FLD>
  </ns0:FIELDS>
</ns0:BBP_RFC_READ_TABLE>

```

#### <a name="xml-samples-for-bapi-requests"></a>Ukázky XML pro žádosti BAPI

> [!TIP]
> Pokud používáte návrháře Logic Apps k úpravám vaší žádosti BAPI, můžete použít následující funkce hledání: 
> 
> * Vyberte objekt v návrháři, aby se zobrazila rozevírací nabídka dostupných metod.
> * Filtrování typů obchodních objektů podle klíčového slova pomocí prohledávatelných seznamů poskytovaných voláním rozhraní API BAPI.

> [!NOTE]
> SAP zpřístupňuje obchodní objekty externím systémům tím, že je popisuje v reakci na RFC `RPY_BOR_TREE_INIT` , které Logic Apps problémy bez vstupního filtru. Logic Apps zkontroluje výstupní tabulku `BOR_TREE` . `SHORT_TEXT`Pole se používá pro názvy obchodních objektů. Obchodní objekty nevracené SAP ve výstupní tabulce nejsou k dispozici pro Logic Apps.
> Pokud používáte vlastní obchodní objekty, musíte tyto obchodní objekty publikovat a uvolnit v SAP. V opačném případě SAP neuvádí vlastní obchodní objekty ve výstupní tabulce `BOR_TREE` . Nemůžete získat přístup k vlastním obchodním objektům v Logic Apps, dokud nezveřejňujete obchodní objekty ze SAP. 

Následující příklad načte seznam bank pomocí metody BAPI `GETLIST` . Tato ukázka obsahuje obchodní objekt pro banku, `BUS1011` . 

```xml

<GETLIST xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_CTRY>US</BANK_CTRY>
  <MAX_ROWS>10</MAX_ROWS>
</GETLIST>

```

Následující příklad vytvoří objekt banky pomocí `CREATE` metody. V tomto příkladu se používá stejný obchodní objekt jako předchozí příklad `BUS1011` . Při použití `CREATE` metody k vytvoření banky nezapomeňte změny potvrdit, protože tato metoda není ve výchozím nastavení potvrzena.

> [!TIP]
> Ujistěte se, že dokument XML dodržuje jakákoli ověřovací pravidla konfigurovaná v systému SAP. Například v tomto ukázkovém dokumentu musí být bankovní klíč ( `<BANK_KEY>` ) číslo směrování banky, označovaný také jako ABA číslo v USA.

```xml

<CREATE xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_ADDRESS>
    <BANK_NAME xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleBankName</BANK_NAME>
    <REGION xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleRegionName</REGION>
    <STREET xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleStreetAddress</STREET>
    <CITY xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">Redmond</CITY>
  </BANK_ADDRESS>
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</CREATE>

```

Následující příklad získá podrobnosti pro banku pomocí čísla směrování banky, což je hodnota pro `<BANK_KEY>` . 

```xml

<GETDETAIL xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</GETDETAIL>

```

#### <a name="xml-samples-for-idoc-requests"></a>Ukázky XML pro žádosti IDoc

K vygenerování jednoduchého schématu XML pro SAP IDoc použijte **přihlašovací aplikaci SAP** a kód T-Code `WE-60` . Přístup k dokumentaci SAP prostřednictvím grafického uživatelského rozhraní a generování schémat XML ve formátu XSD pro vaše typy a rozšíření IDoc. Vysvětlení obecných formátů a datových částí SAP a jejich vestavěných dialogových oken najdete v dokumentaci ke službě [SAP](https://help.sap.com/viewer/index).

Tento příklad deklaruje kořenový uzel a obory názvů. Identifikátor URI v ukázkovém kódu `http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send` deklaruje následující konfiguraci:

* `/IDoc` je kořenová Poznámka pro všechny IDocs
* `/3` je verze typů záznamů pro společné definice segmentů.
* `/ORDERS05` je IDoc typ
* `//` je prázdný segment, protože není k dispozici rozšíření IDoc.
* `/700` je verze SAP
* `/Send` je akce odeslání informací do SAP

```xml

<ns0:Send xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send" xmlns:ns3="http://schemas.microsoft.com/2003/10/Serialization" xmlns:ns1="http://Microsoft.LobServices.Sap/2007/03/Types/Idoc/Common/" xmlns:ns2="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700">
  <ns0:idocData>

```

Uzel můžete opakovat `idocData` pro odeslání dávky IDOCs v jednom volání. V následujícím příkladu je k dispozici jeden záznam ovládacího prvku, `EDI_DC40` a více datových záznamů.

```xml

<...>
  <ns0:idocData>
    <ns2:EDI_DC40>
      <ns1:TABNAM>EDI_DC40</ns1:TABNAM>
<...>
      <ns1:ARCKEY>Cor1908207-5</ns1:ARCKEY>
    </ns2:EDI_DC40>
    <ns2:E2EDK01005>
      <ns2:DATAHEADERCOLUMN_SEGNAM>E23DK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:CURCY>USD</ns2:CURCY>
    </ns2:E2EDK01005>
    <ns2:E2EDK03>
<...>
  </ns0:idocData>

```

Následující příklad je ukázkový záznam ovládacího prvku IDoc, který používá předponu `EDI_DC` . Hodnoty je nutné aktualizovat tak, aby odpovídaly vaší instalaci SAP a IDoc typu. Například váš klientský kód IDoc nemusí být `800` . Obraťte se na tým SAP, abyste měli jistotu, že používáte správné hodnoty pro instalaci SAP.

```xml

<ns2:EDI_DC40>
  <ns:TABNAM>EDI_DC40</ns1:TABNAM>
  <ns:MANDT>800</ns1:MANDT>
  <ns:DIRECT>2</ns1:DIRECT>
  <ns:IDOCTYP>ORDERS05</ns1:IDOCTYP>
  <ns:CIMTYP></ns1:CIMTYP>
  <ns:MESTYP>ORDERS</ns1:MESTYP>
  <ns:STD>X</ns1:STD>
  <ns:STDVRS>004010</ns1:STDVRS>
  <ns:STDMES></ns1:STDMES>
  <ns:SNDPOR>SAPENI</ns1:SNDPOR>
  <ns:SNDPRT>LS</ns1:SNDPRT>
  <ns:SNDPFC>AG</ns1:SNDPFC>
  <ns:SNDPRN>ABAP1PXP1</ns1:SNDPRN>
  <ns:SNDLAD></ns1:SNDLAD>
  <ns:RCVPOR>BTSFILE</ns1:RCVPOR>
  <ns:RCVPRT>LI</ns1:RCVPRT>

```

Následující příklad je ukázkový datový záznam s prostými segmenty. V tomto příkladu se používá formát data SAP. Dokumenty se silným typem můžou používat nativní formáty data XML, jako je například `2020-12-31 23:59:59` .

```xml

<ns2:E2EDK01005>
  <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
    <ns2:CURCY>USD</ns2:CURCY>
    <ns2:BSART>OR</ns2:BSART>
    <ns2:BELNR>1908207-5</ns2:BELNR>
    <ns2:ABLAD>CC</ns2:ABLAD>
  </ns2>
  <ns2:E2EDK03>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK03</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:IDDAT>002</ns2:IDDAT>
      <ns2:DATUM>20160611</ns2:DATUM>
  </ns2:E2EDK03>

```

Následující příklad je datový záznam se seskupenými segmenty. Záznam obsahuje nadřazený uzel skupiny, `E2EDKT1002GRP` a několik podřízených uzlů, včetně `E2EDKT1002` a `E2EDKT2001` . 

```xml

<ns2:E2EDKT1002GRP>
  <ns2:E2EDKT1002>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT1002</ns2:DATAHEADERCOLUMN_SEGNAM>
      <NS2:TDID>ZONE</ns2:TDID>
  </ns2:E2EDKT1002>
  <ns2:E2EDKT2001>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT2001</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDLINE>CRSD</ns2:TDLINE>
  </ns2:E2EDKT2001>
</ns2:E2EDKT1002GRP>

```

Doporučenou metodou je vytvořit identifikátor IDoc pro použití s tRFC. Tento identifikátor transakce můžete nastavit `tid` pomocí [operace Odeslat IDOC](/connectors/sap/#send-idoc) v rozhraní SAP Connector API.

Následující příklad je alternativní metoda pro nastavení identifikátoru transakce nebo `tid` . V tomto příkladu jsou zavřeny poslední uzel segment záznamu dat a IDoc data uzel. Pak identifikátor GUID `guid` je použit jako identifikátor tRFC k detekci duplicit. 

```xml

    </E2STZUM002GRP>
  </idocData>
  <guid>8820ea40-5825-4b2f-ac3c-b83adc34321c</guid>
</Send>

```

### <a name="add-an-http-request-trigger"></a>Přidání triggeru požadavku HTTP

1. V Azure Portal vytvořte prázdnou aplikaci logiky, která otevře návrháře aplikace logiky.

1. Do vyhledávacího pole zadejte `http request` jako filtr. V seznamu **triggery** vyberte, **kdy se přijme požadavek HTTP**.

   ![Přidat aktivační událost požadavku HTTP](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Nyní uložte aplikaci logiky, abyste mohli vygenerovat adresu URL koncového bodu pro vaši aplikaci logiky.
Na panelu nástrojů návrháře vyberte **Uložit**.

   Adresa URL koncového bodu se teď zobrazí ve triggeru, například:

   ![Vygenerovat adresu URL pro koncový bod](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Přidání akce SAP pro generování schémat

1. V návrháři aplikace logiky pod triggerem vyberte **Nový krok**.

   ![Přidat nový krok do aplikace logiky](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Do vyhledávacího pole zadejte `sap` jako filtr. V seznamu **Akce** vyberte možnost **Generovat schémata**.
  
   ![Přidání akce generovat schémata do aplikace logiky](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Nebo můžete vybrat kartu **Enterprise** a pak vybrat akci SAP.

   ![Výběr možnosti SAP odeslat akci na kartě organizace](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Pokud vaše připojení už existuje, pokračujte dalším krokem, abyste mohli nastavit akci SAP. Pokud se však zobrazí výzva k zadání podrobností o připojení, zadejte informace, abyste mohli nyní vytvořit připojení k místnímu serveru SAP.

   1. Zadejte název připojení.

   1. V části **Brána dat** v části **předplatné**nejdřív vyberte předplatné Azure pro prostředek brány dat, který jste vytvořili v Azure Portal pro instalaci brány dat. 
   
   1. V části **Brána připojení**vyberte prostředek brány dat v Azure.

   1. Pokračujte v poskytování informací o připojení. U vlastnosti **typ přihlášení** postupujte podle kroků na základě toho, jestli je vlastnost nastavená na **aplikační server** nebo **skupinu**:
   
      * U **aplikačního serveru**se tyto vlastnosti, které se obvykle zobrazují jako volitelné, vyžadují:

        ![Vytvořit připojení aplikačního serveru SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * V případě **skupiny**jsou vyžadovány tyto vlastnosti, které se obvykle zobrazují jako volitelné:

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

   Další informace o akci SAP najdete v tématu [schémata zpráv pro IDOC operace](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

### <a name="test-your-logic-app"></a>Testování aplikace logiky

1. Na panelu nástrojů návrháře vyberte **Spustit** , aby se spustil běh aplikace logiky.

1. Otevřete běh a podívejte se na výstupy pro akci **Generovat schémata** .

   Výstupy zobrazují vygenerovaná schémata pro zadaný seznam zpráv.

### <a name="upload-schemas-to-an-integration-account"></a>Nahrávání schémat na účet pro integraci

V případě potřeby můžete vygenerovaná schémata stáhnout nebo uložit v úložištích, jako je například objekt blob, úložiště nebo účet integrace. Účty pro integraci poskytují prvotřídní prostředí s dalšími akcemi XML, takže tento příklad ukazuje, jak nahrát schémata do účtu pro integraci pro stejnou aplikaci logiky pomocí konektoru Azure Resource Manager.

1. V návrháři aplikace logiky pod triggerem vyberte **Nový krok**.

1. Do vyhledávacího pole zadejte `Resource Manager` jako filtr. Vyberte **vytvořit nebo aktualizovat prostředek**.

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

1. Uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

### <a name="test-your-logic-app"></a>Testování aplikace logiky

1. Na panelu nástrojů návrháře vyberte **Spustit** , aby se aktivovala aplikace logiky ručně.

1. Po úspěšném spuštění přejdete na účet pro integraci a zkontrolujete, že vygenerovaná schémata existují.

<a name="secure-network-communications"></a>

## <a name="enable-secure-network-communications"></a>Povolit zabezpečenou síťovou komunikaci

Než začnete, ujistěte se, že jste splnili výše uvedené [předpoklady](#pre-reqs), které platí jenom v případě, že používáte bránu data Gateway a vaše aplikace logiky běží ve více klientech Azure:

* Ujistěte se, že místní brána dat je nainstalovaná na počítači, který je ve stejné síti jako váš systém SAP.

* V případě jednotného přihlašování (SSO) je brána dat spuštěna jako uživatel, který je namapován na uživatele SAP.

* Knihovna SNC, která poskytuje další funkce zabezpečení, je nainstalovaná na stejném počítači jako brána dat. Mezi příklady patří [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos a NTLM.

   Pokud chcete povolit SNC pro vaše požadavky na systém SAP nebo z něj, zaškrtněte políčko **použít SNC** v připojení SAP a zadejte tyto vlastnosti:

   ![Konfigurace SAP SNC v souvislosti s připojením](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Vlastnost | Popis |
   |----------| ------------|
   | **Cesta ke knihovně SNC** | Název knihovny SNC nebo cesta relativní k umístění instalace NCo nebo absolutní cesta. Příklady jsou `sapsnc.dll` nebo `.\security\sapsnc.dll` `c:\security\sapsnc.dll` . |
   | **JEDNOTNÉ PŘIHLAŠOVÁNÍ SNC** | Při připojení prostřednictvím SNC se obvykle používá identita SNC pro ověřování volajícího. Další možností je přepsat, aby se informace o uživateli a heslech mohly použít k ověřování volajícího, ale řádek je stále zašifrovaný. |
   | **SNC moje jméno** | Ve většině případů může být tato vlastnost vynechána. Nainstalované řešení SNC obvykle zná vlastní název SNC. Pouze pro řešení, která podporují více identit, bude pravděpodobně nutné zadat identitu, která se má použít pro tento konkrétní cíl nebo server. |
   | **Název partnera SNC** | Název back-endu SNC. |
   | **SNC kvality ochrany** | Kvalita služby, která se má použít pro SNC komunikaci tohoto konkrétního cíle nebo serveru. Výchozí hodnota je definována back-end systémem. Maximální hodnota je definovaná produktem zabezpečení použitým pro SNC. |
   |||

   > [!NOTE]
   > Nenastavte proměnné prostředí SNC_LIB a SNC_LIB_64 na počítači, kde máte bránu dat a knihovnu SNC. Pokud je nastaveno, mají přednost před hodnotou SNC knihovny předané prostřednictvím konektoru.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Bezpečné zadání

Ve výchozím nastavení se při vytváření připojení SAP používá silné psaní ke kontrole neplatných hodnot prováděním ověřování XML proti schématu. Toto chování vám může přispět k detekci problémů dříve. Možnost **bezpečného psaní** je k dispozici pro zpětnou kompatibilitu a kontroluje pouze délku řetězce. Pokud zvolíte možnost **bezpečného psaní**, typ dat a typ TIMS v SAP se považují za řetězce, nikoli jako jejich ekvivalenty XML, `xs:date` a `xs:time` , kde `xmlns:xs="http://www.w3.org/2001/XMLSchema"` . Bezpečné psaní má vliv na chování pro všechna generování schématu, zprávu Send pro datovou část "byla odeslána" a "přijatá" odpověď a Trigger. 

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

Když se odesílají zprávy s povoleným **bezpečným typováním** , TIMS odpověď dat a odpovědí bude vypadat jako v tomto příkladu:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>Pokročilé scénáře

### <a name="change-language-headers"></a>Změna jazykových hlaviček

Když se připojíte k SAP z Logic Apps, výchozím jazykem pro připojení je angličtina. Jazyk pro připojení můžete nastavit pomocí [standardní HLAVIČKY `Accept-Language` http](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4) se vstupními požadavky.

> [!TIP]
> Většina webových prohlížečů přidá `Accept-Language` hlavičku na základě nastavení uživatele. Webový prohlížeč použije tuto hlavičku při vytváření nového připojení SAP v Návrháři Logic Apps. Pokud nechcete vytvářet připojení SAP v preferovaném jazyce ve webovém prohlížeči, aktualizujte nastavení webového prohlížeče tak, aby používala preferovaný jazyk, nebo vytvořte připojení SAP pomocí Azure Resource Manager místo návrháře Logic Apps. 

Můžete například odeslat žádost s `Accept-Language` hlavičkou do aplikace logiky pomocí triggeru **požadavku HTTP** . Všechny akce v aplikaci logiky obdrží hlavičku. Pak SAP používá zadané jazyky ve svých systémových zprávách, jako jsou například chybové zprávy BAPI.

Parametry připojení SAP pro aplikaci logiky nemají vlastnost Language. Takže pokud použijete `Accept-Language` hlavičku, může se zobrazit následující chyba: **Zkontrolujte prosím informace o účtu nebo oprávnění a zkuste to znovu.** V takovém případě místo toho ověřte protokoly chyb komponenty SAP. K chybě ve skutečnosti dojde v součásti SAP, která používá hlavičku, takže se může zobrazit jedna z těchto chybových zpráv:

* `"SAP.Middleware.Connector.RfcLogonException: Select one of the installed languages"`
* `"SAP.Middleware.Connector.RfcAbapMessageException: Select one of the installed languages"`

### <a name="confirm-transaction-explicitly"></a>Potvrdit transakci explicitně

Když odesíláte transakce do SAP z Logic Apps, k tomuto systému Exchange dochází ve dvou krocích, jak je popsáno v dokumentu SAP, v tématu [transakční aplikace RFC serveru](https://help.sap.com/doc/saphelp_nwpi71/7.1/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). Ve výchozím nastavení zpracuje akce **Odeslat do SAP** jak kroky pro přenos funkce, tak potvrzení transakce v jednom volání. Konektor SAP vám dává možnost oddělit tyto kroky. Můžete odeslat IDoc a místo toho, aby transakce byla automaticky potvrzena, můžete použít akci explicitní **potvrzení ID transakce** .

Tato možnost oddělit potvrzení ID transakce je užitečná v případě, že nechcete provádět duplicitní transakce v SAP, například ve scénářích, kdy může dojít k selhání z důvodu příčin potíží se sítí. Potvrzením ID transakce samostatně se transakce v systému SAP pouze v jednom okamžiku dokončí.

Tady je příklad, který ukazuje tento vzor:

1. Vytvořte prázdnou aplikaci logiky a přidejte Trigger HTTP.

1. Z konektoru SAP přidejte akci **Odeslat IDOC** . Zadejte podrobnosti pro IDoc, které jste odeslali do svého systému SAP.

1. Chcete-li explicitně potvrdit ID transakce v samostatném kroku, v poli **Potvrdit TID** vyberte možnost **ne**. Pro volitelné pole **GUID ID transakce** můžete buď ručně zadat hodnotu, nebo nechat konektor automaticky generovat a vrátit tento identifikátor GUID v odpovědi z akce Odeslat IDOC.

   ![Odeslat vlastnosti akce IDOC](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. Chcete-li explicitně potvrdit ID transakce, přidejte akci **Potvrdit ID transakce** a ujistěte se, že [neposíláte duplicitní IDOCs do SAP](#avoid-sending-duplicate-idocs). Klikněte do pole **ID transakce** , aby se zobrazil seznam dynamického obsahu. V tomto seznamu vyberte hodnotu **ID transakce** , která se vrátí z akce **Odeslat IDOC** .

   ![Akce potvrzení ID transakce](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   Po spuštění tohoto kroku je aktuální transakce označena jako úplná na obou koncích na straně konektoru SAP a na straně systému SAP.

#### <a name="avoid-sending-duplicate-idocs"></a>Vyhněte se posílání duplicitních IDocs

Pokud dojde k potížím s duplicitními IDocs, které se odesílají do SAP z aplikace logiky, vytvořte pomocí těchto kroků řetězcovou proměnnou, která bude sloužit jako identifikátor transakce IDoc. Vytvoření tohoto identifikátoru transakce pomáhá zabránit duplicitám síťových přenosů, když dojde k problémům, jako jsou dočasné výpadky, problémy se sítí nebo ztracené potvrzování.

> [!NOTE]
> Systémy SAP zapomenou v zadaném čase identifikátor transakce nebo 24 hodin ve výchozím nastavení. V důsledku toho SAP nikdy nedokáže potvrdit identifikátor transakce, pokud ID nebo identifikátor GUID nejsou známy.
> Pokud se potvrzení pro identifikátor transakce nezdaří, toto selhání indikuje, že komunikaci SSL (se systémem SAP selhal před tím, než SAP dokázala potvrdit potvrzení.

1. V Návrháři Logic Apps přidejte do své aplikace logiky **proměnnou inicializovat** akci. 
1. V editoru pro **proměnnou inicializovat**akci nakonfigurujte následující nastavení. Pak změny uložte.
    1. Jako **název**zadejte název proměnné. Například, `IDOCtransferID`.
    2. Jako typ proměnné vyberte **typ** **řetězec** .
    3. V poli **hodnota**vyberte textové pole a **Zadejte počáteční hodnotu** . tím otevřete nabídku dynamického obsahu. Vyberte kartu **výrazy** . V seznamu funkcí zadejte funkci `guid()` . Pak kliknutím na **OK** uložte změny. Pole **hodnota** je nyní nastaveno na `guid()` funkci, která generuje identifikátor GUID.
1. Po akci **inicializovat proměnnou** přidejte akci **Odeslat IDOC**.
1. V editoru akce **Odeslat IDOC**nakonfigurujte následující nastavení. Pak změny uložte.
    1. Jako **typ IDOC** vyberte typ zprávy a zadejte zprávu do zprávy **input IDOC**.
    1. V případě **verze SAP Release**vyberte hodnoty konfigurace SAP.
    1. V případě **verze typů záznamů**vyberte hodnoty konfigurace SAP.
    1. Pro **Potvrdit TID**vyberte možnost **ne**.
    1. Vyberte **Přidat nový**  >  **identifikátor GUID ID transakce**seznamu parametrů. Výběrem textového pole otevřete nabídku dynamického obsahu. Na kartě **proměnné** vyberte název proměnné, kterou jste vytvořili. Například, `IDOCtransferID`.
1. V záhlaví akce **Odeslat IDOC**vyberte **...**  >  **Nastavení**. V případě **zásady opakování**vyberte **žádná**  >  **Dokončená**.
1. Po **odeslání IDOC**akce přidejte **ID transakce potvrdit**akci.
1. V editoru pro akci **Potvrdit ID transakce**nakonfigurujte následující nastavení. Pak změny uložte.
    1. Jako **ID transakce**zadejte název proměnné znovu. Například, `IDOCtransferID`.

## <a name="known-issues-and-limitations"></a>Známé problémy a omezení

Tady jsou aktuálně známé problémy a omezení pro spravovaný konektor SAP (bez ISE):

* Aktivační událost SAP nepodporuje clustery brány dat. V některých případech převzetí služeb při selhání se uzel brány dat, který komunikuje se systémem SAP, může lišit od aktivního uzlu, což vede k neočekávanému chování. Pro scénáře odeslání se podporují clustery služby data Gateway.

* Konektor SAP aktuálně nepodporuje řetězce směrovače SAP. Místní brána dat musí existovat ve stejné síti LAN jako systém SAP, který chcete připojit.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o této spojnici, jako jsou triggery, akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete na [referenční stránce konektoru](/connectors/sap/).

> [!NOTE]
> V případě Logic Apps v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Tato verze konektoru ISE-Label používá místo toho [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

## <a name="next-steps"></a>Další kroky

* [Připojte se k místním systémům](../logic-apps/logic-apps-gateway-connection.md) z Azure Logic Apps.
* Naučte se, jak pomocí [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)ověřit, transformovat a používat jiné operace se zprávou.
* Přečtěte si o dalších [konektorech Logic Apps](../connectors/apis-list.md).
