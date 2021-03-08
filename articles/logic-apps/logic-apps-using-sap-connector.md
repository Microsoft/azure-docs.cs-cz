---
title: Připojení k systémům SAP
description: Automatizace pracovních postupů pomocí Azure Logic Apps pro přístup k prostředkům SAP a jejich správa
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 03/08/2021
tags: connectors
ms.openlocfilehash: 3e98dc36b3d58ce5289fccde7b5f5a49973c9de6
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454222"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Připojení k systémům SAP z Azure Logic Apps

Tento článek vysvětluje, jak můžete získat přístup k prostředkům SAP z Logic Apps pomocí [konektoru SAP](/connectors/sap/).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [Zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky, ze které chcete získat přístup k prostředkům SAP. Pokud s Logic Apps začínáte, přečtěte si [Přehled služby Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý Start pro vytvoření první aplikace logiky v Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md).

    * Pokud jste použili předchozí verzi konektoru SAP, která se už nepoužívá, musíte [migrovat na aktuální konektor](#migrate-to-current-connector) , abyste se mohli připojit k serveru SAP.

    * Pokud spouštíte aplikaci logiky ve více tenantů Azure, přečtěte si téma [požadavky pro více tenantů](#multi-tenant-azure-prerequisites).

    * Pokud spouštíte aplikaci logiky v [prostředí ISE (Premium-Level Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), přečtěte si část [požadavky ISE](#ise-prerequisites).

* [Aplikační Server SAP](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) nebo [Server zpráv SAP](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm) , ze kterého chcete získat přístup Logic Apps. Informace o tom, jaké servery SAP a akce SAP můžete s konektorem používat, najdete v tématu [Kompatibilita SAP](#sap-compatibility).

* Obsah zprávy, který se pošle na Server SAP, jako je například ukázkový soubor IDoc. Tento obsah musí být ve formátu XML a zahrnovat obor názvů pro akci SAP, kterou chcete použít. IDocs můžete [poslat pomocí schématu plochého souboru, a to tak, že je zabalíte do obálky XML](#send-flat-file-idocs).

* Chcete-li použít **při přijetí zprávy z** triggeru SAP, je třeba provést následující akce:

    * Nastavte oprávnění zabezpečení brány SAP pomocí tohoto nastavení: `"TP=Microsoft.PowerBI.EnterpriseGateway HOST=<gateway-server-IP-address> ACCESS=*"`

    * Nastavením protokolování zabezpečení brány SAP získáte informace o seznamu Access Control (ACL). Další informace najdete v [tématu nápovědy SAP pro nastavení protokolování brány](https://help.sap.com/erp_hcm_ias2_2015_02/helpdata/en/48/b2a710ca1c3079e10000000a42189b/frameset.htm). V opačném případě se zobrazí tato chyba: `"Registration of tp Microsoft.PowerBI.EnterpriseGateway from host <host-name> not allowed"`

    > [!NOTE]
    > Tato aktivační událost používá stejné umístění URI pro obnovení i zrušení odběru odběru Webhooku. Operace obnovení používá `PATCH` metodu HTTP, zatímco operace zrušení odběru používá `DELETE` metodu HTTP. Toto chování může vést k tomu, že operace obnovení se objeví jako operace zrušení odběru v historii triggeru, ale operace se pořád obnovuje, protože Trigger používá `PATCH` jako metodu HTTP, ne `DELETE` .

### <a name="sap-compatibility"></a>Kompatibilita SAP

Konektor SAP je kompatibilní s následujícími typy systémů SAP:

* Místní a cloudové systémy SAP založené na HANA, jako je S/4 HANA.

* Klasické místní systémy SAP, například R/3 a ECC.

Konektor SAP podporuje následující typy integrace zpráv a dat ze systémů SAP NetWeaver:

* Převodní dokument (IDoc)

* Rozhraní BAPI (Business Application Programming Interface)

* Volání funkce Remote Functions (RFC) a transakčního RFC (tRFC)

Konektor SAP používá [knihovnu SAP .NET Connector (NCo)](https://support.sap.com/en/product/connectors/msnet.html). Pomocí konektoru můžete použít následující akce SAP a Trigger:

* **Odešlete zprávu do SAP** pro [odeslání IDOCs prostřednictvím akce tRFC](#send-idoc-action) , kterou můžete použít k těmto akcím:

    * [Volání funkcí BAPI přes RFC](#call-bapi-action)

    * Volání RFC/tRFC v systémech SAP

    * Vytvořit nebo zavřít stavové relace

    * Potvrďte nebo vraťte zpět transakce BAPI

    * Potvrďte identifikátor transakce.

    * Poslat IDocs, získat stav IDoc z jeho čísla a získat seznam IDocs pro transakci

    * Čtení tabulky SAP

* **Při přijetí zprávy z** triggeru SAP, kterou můžete použít k:

    * Přijmout IDocs přes tRFC

    * Volání funkcí BAPI přes tRFC

    * Volání RFC/tRFC v systémech SAP

* Akce **Generovat schémata** , kterou můžete použít ke generování schémat pro artefakty SAP pro IDOC, BAPI nebo RFC.

Chcete-li použít tyto akce SAP, je třeba nejprve ověřit připojení pomocí uživatelského jména a hesla. Konektor SAP podporuje také [zabezpečenou síťovou komunikaci (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). SNC můžete použít pro jednotné přihlašování SAP NetWeaver (SSO) nebo pro další funkce zabezpečení z externích produktů. Pokud používáte SNC, přečtěte si část [požadavky SNC](#snc-prerequisites).

### <a name="migrate-to-current-connector"></a>Migrovat na aktuální konektor

Předchozí konektory aplikačního serveru SAP a serveru zpráv SAP byly zastaralé 29. února 2020. K migraci na aktuální konektor SAP použijte následující postup:

1. Aktualizujte místní [bránu dat](https://www.microsoft.com/download/details.aspx?id=53127) na aktuální verzi. Další informace najdete v tématu [instalace místní brány dat pro Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

1. V aplikaci logiky, která používá zastaralý konektor SAP, odstraňte akci **Odeslat do SAP** .

1. Do akce SAP z aktuálního konektoru SAP přidejte **zprávu odeslat zprávu** .

1. Znovu se připojte k systému SAP v nové akci.

1. Uložte aplikaci logiky.

### <a name="multi-tenant-azure-prerequisites"></a>Požadavky na Azure pro více tenantů

Tyto požadavky platí v případě, že aplikace logiky běží ve více klientech Azure. Spravovaný konektor SAP neběží nativně v [ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

> [!TIP]
> Pokud používáte ISE úrovně Premium, můžete místo spravovaného konektoru SAP použít konektor SAP ISE. Další informace najdete v tématu [předpoklady pro ISE](#ise-prerequisites).

Spravovaný konektor SAP se integruje se systémy SAP prostřednictvím místní [brány dat](../logic-apps/logic-apps-gateway-connection.md). Například při odeslání zprávy z aplikace logiky do systému SAP bude brána dat fungovat jako klient RFC a přepošle žádosti přijaté z aplikace logiky do SAP. Stejně tak v případě, že se zobrazují zprávy, brána dat funguje jako server RFC, který přijímá žádosti od SAP a přesměruje je do aplikace logiky.

* Místní [bránu dat si můžete stáhnout a nainstalovat](../logic-apps/logic-apps-gateway-install.md) na hostitelském počítači nebo virtuálním počítači, který existuje ve stejné virtuální síti jako systém SAP, ke kterému se připojujete.

* [Vytvořte prostředek brány Azure](../logic-apps/logic-apps-gateway-connection.md#create-azure-gateway-resource) pro místní bránu dat v Azure Portal. Tato brána vám pomůže zajistit zabezpečený přístup k místním datům a prostředkům. Ujistěte se, že používáte podporovanou verzi brány.

    * Pokud dojde k potížím s bránou, zkuste [upgradovat na nejnovější verzi](https://aka.ms/on-premises-data-gateway-installer), která může zahrnovat aktualizace pro vyřešení vašeho problému.

* [Stáhněte si a nainstalujte nejnovější knihovnu klienta SAP](#sap-client-library-prerequisites) na stejný místní počítač jako místní bránu dat.

### <a name="ise-prerequisites"></a>Požadavky na ISE

Tyto požadavky platí, pokud spouštíte aplikaci logiky v ISE úrovně Premium. Neplatí to ale pro aplikace logiky spuštěné v ISE na úrovni vývojářů. ISE poskytuje přístup k prostředkům, které jsou chráněné službou Azure Virtual Network, a nabízí další ISEové konektory, které umožňují Logic Apps přímý přístup k místním prostředkům bez použití místní brány dat.

> [!NOTE]
> I když je konektor SAP ISE viditelný v ISE na úrovni vývojáře, pokusy o instalaci konektoru nebudou úspěšné.

1. Pokud ještě nemáte účet Azure Storage s kontejnerem objektů blob, vytvořte kontejner pomocí [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) nebo [Průzkumník služby Azure Storage](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

1. [Stáhněte a nainstalujte si nejnovější knihovnu klienta SAP](#sap-client-library-prerequisites) na místním počítači. Měli byste mít následující soubory sestavení:

   * libicudecnumber.dll

   * rscp4n.dll

   * sapnco.dll

   * sapnco_utils.dll

1. Vytvořte soubor. zip, který obsahuje tyto soubory sestavení. Nahrajte balíček do kontejneru objektů BLOB v Azure Storage.

1. V Azure Portal nebo Průzkumník služby Azure Storage přejděte do umístění kontejneru, kam jste nahráli soubor. zip.

1. Zkopírujte adresu URL umístění kontejneru. Ujistěte se, že jste zahrnuli token sdíleného přístupového podpisu (SAS), takže je token SAS autorizovaný. V opačném případě selže nasazení konektoru SAP ISE.

1. Nainstalujte a nasaďte konektor SAP v ISE. Další informace najdete v tématu [Přidání konektorů ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment).

   1. V [Azure Portal](https://portal.azure.com)vyhledejte a otevřete své ISE.

   1. V nabídce ISE vyberte **spravované konektory** &gt; **Přidat**. V seznamu konektory vyhledejte a vyberte **SAP**.

   1. V podokně **Přidat nový spravovaný konektor** do pole **balíček SAP** vložte adresu URL souboru. zip, který obsahuje sestavení SAP. Znovu nezapomeňte zahrnout token SAS.
 
  1. Výběrem **vytvořit** dokončete vytváření konektoru ISE.

1. Pokud se vaše instance SAP a ISE nacházejí v různých virtuálních sítích, musíte taky vytvořit [partnerský vztah k těmto sítím](../virtual-network/tutorial-connect-virtual-networks-portal.md) , aby byly připojené.

### <a name="sap-client-library-prerequisites"></a>Požadavky na knihovnu klienta SAP

Toto jsou požadavky na klientskou knihovnu SAP, kterou používáte s konektorem.

* Ujistěte se, že jste nainstalovali nejnovější verzi nástroje [SAP Connector (NCo 3,0) pro Microsoft .NET 3.0.22.0 zkompilované pomocí .NET Framework 4,0-Windows 64-bit (x64)](https://support.sap.com/en/product/connectors/msnet.html). Starší verze SAP NCo můžou narazit na problémy, když se ve stejnou dobu pošle více než jedna zpráva IDoc. Tento stav blokuje všechny pozdější zprávy odeslané do cíle SAP, což způsobí vypršení časového limitu zpráv.

* Je nutné, aby byla nainstalovaná verze 64 klientské knihovny SAP, protože brána dat běží pouze na 64 systémech. Instalace nepodporované verze 32 způsobí chybu "chybná image".

* Zkopírujte soubory sestavení z výchozí instalační složky do jiného umístění v závislosti na vašem scénáři následujícím způsobem.

    * Pro Logic Apps běžící v ISE použijte místo toho [požadavky ISE](#ise-prerequisites) .

    * Pro aplikace logiky spuštěné ve víceklientské architektuře Azure a používání místní brány dat zkopírujte soubory sestavení do instalační složky brány dat. 

        
        * Pokud se připojení SAP nepovede s chybovou zprávou, **Zkontrolujte prosím informace účtu nebo oprávnění a zkuste to znovu**. Ujistěte se, že jste zkopírovali soubory sestavení do instalační složky brány dat.
        
        * Řešení dalších potíží pomocí [prohlížeče protokolu vazby sestavení .NET](/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer). Tento nástroj umožňuje ověřit, zda jsou soubory sestavení ve správném umístění. 
        
        * Volitelně můžete při instalaci knihovny klienta SAP vybrat možnost **registrace globální mezipaměti sestavení** .

Všimněte si následujících vztahů mezi klientskou knihovnou SAP, .NET Framework, modulem runtime .NET a bránou:

* Adaptér Microsoft SAP i hostitelská služba brány používají .NET Framework 4.7.2.

* SAP NCo for .NET Framework 4,0 funguje s procesy, které používají .NET Runtime 4,0 na 4,8.

* SAP NCo for .NET Framework 2,0 funguje s procesy, které používají .NET runtime 2,0 na 3,5, ale už nefungují s nejnovější bránou.

### <a name="snc-prerequisites"></a>Požadavky na SNC

Pokud používáte místní bránu dat s volitelnou SNC, která je podporovaná jenom ve více klientech Azure, musíte nakonfigurovat tato další nastavení.

Pokud používáte SNC s SSO, zajistěte, aby byla služba brány dat spuštěná jako uživatel, který je namapovaný na uživatele SAP. Chcete-li změnit výchozí účet, vyberte možnost **změnit účet** a zadejte přihlašovací údaje uživatele.

![Snímek obrazovky s nastavením místní brány dat na webu Azure Portal zobrazující stránku nastavení služby s tlačítkem pro změnu vybraného účtu služby brány.](./media/logic-apps-using-sap-connector/gateway-account.png)

Pokud povolíte SNC prostřednictvím externího bezpečnostního produktu, zkopírujte knihovnu nebo soubory SNC na stejném počítači, kde je vaše brána dat nainstalovaná. Mezi příklady SNC produktů patří [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos a NTLM. Další informace o povolení SNC pro bránu dat najdete v tématu [Povolení zabezpečené síťové komunikace](#enable-secure-network-communications).

## <a name="send-idoc-messages-to-sap-server"></a>Posílání zpráv IDoc do SAP serveru

Pomocí těchto příkladů vytvoříte aplikaci logiky, která odešle zprávu IDoc serveru SAP a vrátí odpověď:

1. [Vytvořte aplikaci logiky, která se aktivuje požadavkem HTTP.](#create-http-request-trigger)

1. [Vytvořte ve svém pracovním postupu akci, která odešle zprávu do SAP.](#create-sap-action-to-send-message)

1. [V pracovním postupu vytvořte akci odpovědi HTTP.](#create-http-response-action)

1. [Pokud chcete přijímat odpovědi ze SAP ABAP, vytvořte vzor žádosti o odpověď (RFC) vzdálené volání funkce, pokud používáte dokument RFC.](#create-rfc-request-response)

1. [Otestujte aplikaci logiky.](#test-logic-app)

### <a name="create-http-request-trigger"></a>Aktivační událost vytvoření žádosti HTTP

> [!NOTE]
> Když aplikace logiky obdrží IDocs ze SAP, [aktivuje](../connectors/connectors-native-reqres.md) se v současnosti podpora formátu SAP v prostém formátu XML. Pokud chcete přijímat IDocs jako prostý kód XML, použijte Trigger **při přijetí zprávy ze SAP**. Nastavte formát parametru **IDOC** na **SapPlainXml**.

Nejdřív vytvořte aplikaci logiky s koncovým bodem v Azure, aby se odeslaly požadavky *http post* do vaší aplikace logiky. Když aplikace logiky obdrží tyto požadavky HTTP, [Trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) se aktivuje a spustí další krok v pracovním postupu.

1. V [Azure Portal](https://portal.azure.com)vytvořte prázdnou aplikaci logiky, která otevře **Návrháře Logic Apps**.

1. Do vyhledávacího pole zadejte `http request` jako filtr. V seznamu **triggery** vyberte, **kdy se přijme požadavek HTTP**.

   ![Snímek obrazovky návrháře Logic Apps, ve kterém se zobrazuje nový Trigger žádosti HTTP přidaný do aplikace logiky](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Uložte aplikaci logiky, abyste mohli vygenerovat adresu URL koncového bodu pro vaši aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**. Adresa URL koncového bodu se teď zobrazí ve triggeru. 

   ![Snímek obrazovky návrháře Logic Apps, ve kterém se zobrazuje aktivační událost požadavku HTTP s vygenerovanou adresou URL pro odeslání](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="create-sap-action-to-send-message"></a>Vytvoření akce SAP pro odeslání zprávy

Potom vytvořte akci, která odešle zprávu IDoc do SAP, když se aktivuje [Trigger požadavku HTTP](#create-http-request-trigger) .

1. V Návrháři Logic Apps pod triggerem vyberte **Nový krok**.

   ![Snímek obrazovky návrháře Logic Apps, který ukazuje úpravu aplikace logiky, která umožňuje přidat nový krok.](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Do vyhledávacího pole zadejte `sap` jako filtr. V seznamu **Akce** vyberte **Odeslat zprávu do SAP**.
  
   ![Snímek obrazovky návrháře Logic Apps, který zobrazuje výběr akce Odeslat zprávu do SAP](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Nebo můžete vybrat kartu **Enterprise** a pak vybrat akci SAP.

   ![Snímek obrazovky návrháře Logic Apps, ve kterém se zobrazuje výběr akce Odeslat zprávu do SAP na kartě organizace](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Pokud připojení již existuje, pokračujte k dalšímu kroku. Pokud budete vyzváni k vytvoření nového připojení, zadejte následující informace pro připojení k místnímu serveru SAP.

   1. Zadejte název připojení.

   1. Pokud používáte bránu dat, postupujte následovně:
   
      1. V části **Brána dat** v části **předplatné** nejdřív vyberte předplatné Azure pro prostředek brány dat, který jste vytvořili v Azure Portal pro instalaci brány dat.
   
      1. V části **Brána připojení** vyberte prostředek brány dat v Azure.

   1. Pokračujte v poskytování informací o připojení. U vlastnosti **typ přihlášení** postupujte podle kroků na základě toho, jestli je vlastnost nastavená na **aplikační server** nebo **skupinu**:
   
      * U **aplikačního serveru** se tyto vlastnosti, které se obvykle zobrazují jako volitelné, vyžadují:

        ![Vytvořit připojení aplikačního serveru SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * V případě **skupiny** jsou vyžadovány tyto vlastnosti, které se obvykle zobrazují jako volitelné:

        ![Vytvořit připojení k serveru zpráv SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Ve výchozím nastavení se silné zadání používá ke kontrole neplatných hodnot prováděním ověřování XML proti schématu. Toto chování vám může přispět k detekci problémů dříve. Možnost **bezpečného psaní** je k dispozici pro zpětnou kompatibilitu a kontroluje pouze délku řetězce. Přečtěte si další informace o [možnosti bezpečného psaní](#safe-typing).

   1. Až budete hotovi, vyberte **vytvořit**.

      Logic Apps nastaví a otestuje připojení, aby se zajistilo správné fungování připojení.

    > [!NOTE]

    > Pokud se zobrazí následující chyba, došlo k potížím s instalací klientské knihovny SAP NCo: 
    >
    > **Test připojení se nezdařil. Chyba při zpracování požadavku se nezdařila. Podrobnosti o chybě: nepovedlo se načíst soubor nebo sestavení sapnco, Version = 3.0.0.42, Culture = neutral, PublicKeyToken 50436dca5c7f7d23 nebo jedna z jejích závislostí. Systém nemůže najít zadaný soubor.**
    >
    > Ujistěte se, že jste [nainstalovali požadovanou verzi klientské knihovny SAP NCo a splnili všechny ostatní požadavky](#sap-client-library-prerequisites).

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

#### <a name="send-flat-file-idocs"></a>Odeslat plochý soubor IDocs

IDocs můžete použít s schématem plochého souboru, pokud je zabalíte do obálky XML. K odeslání nestrukturovaného souboru IDoc použijte obecné pokyny k [Vytvoření akce SAP pro odeslání zprávy IDOC](#create-sap-action-to-send-message) s následujícími změnami.

1. Pro akci **Odeslat zprávu do SAP** použijte operaci SAP Action URI `http://microsoft.lobservices.sap/2007/03/Idoc/SendIdoc` .

1. Naformátujte vstupní zprávu pomocí obálky XML. Příklad naleznete v následující ukázkové zprávě:

```xml
<?xml version="1.0" encoding="utf-8"?>
<SendIdoc xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/">
  <idocData>EDI_DC    300                      ORDERS052SAPMSS    LIMSFTABCSWI                                                                                           ED  93AORDERSOLP     VLTRFC    KUMSFTABCSWI                                                                                           13561                       231054476                                                                           20190523085430ORDERSORDERS05          US
E2EDK01005300                1     E2EDK010050     1       USD                                                                        Z4O14506907554
E2EDK03   300                2     E2EDK03   0     2   02220190523
E2EDKA1   300                3     E2EDKA1   0     2   RE                  MSFTASWI
E2EDKA1   300                4     E2EDKA1   0     2   US                  MSFTASWI
E2EDKA1   300                5     E2EDKA1   0     2   WE                  MSFTASWILIC
E2EDKA1   300                6     E2EDKA1   0     2   Z1 KKKKKKK                           ABC YYYYYYYYYYY ZZ                                                                                                                          BBBBBBBBBBBBBBBB 11                                                                                      ttttttttttt                                 6666              US                                                                                                999 999 99 99                                                                                                                SSSSSSS SSS SSSSSS                                                                                                                                SSSSSSS SSS SSSSSS
E2EDKA1   300                7     E2EDKA1   0     2   Z2 KKKKKKK                           BBBBBBBBBBBBBBBB DDDDDDDD ZZ                                                                                                                EEEEEEEEEEE 86                                                                                           rrrrrrrr                                    8888              US                                                                                                999 999 99 99                                                                                                                NNNNNN NNNNNN                                                                                                                                     NNNNNN NNNNNN
E2EDK02   300                8     E2EDK02   0     2   901Z
E2EDK02   300                9     E2EDK02   0     2   90399680096ZZS2002
E2EDK02   300                10    E2EDK02   0     2   902S
E2EDKT1   300                11    E2EDKT1   0     2   Z1EME
E2EDKT2   300                12    E2EDKT2   0     3   xxx@xxx-xx.xx
E2EDKT1   300                13    E2EDKT1   0     2   Z2EME
E2EDKT2   300                14    E2EDKT2   0     3   x.xxxxxx@xxxxxxxx-xxxxxxxxxx.xx
E2EDP01001300                15    E2EDP010010     2   10         1              EA                          999.9
E2EDP19   300                16    E2EDP19   0     3   00AAAA-11111</idocData>
</SendIdoc>
```



### <a name="create-http-response-action"></a>Vytvořit akci odpovědi HTTP

Teď přidejte akci odpovědi do pracovního postupu aplikace logiky a zahrňte výstup z akce SAP. Vaše aplikace logiky tak vrátí výsledky ze serveru SAP původnímu žadateli.

1. V Návrháři Logic Apps v části akce SAP vyberte **Nový krok**.

1. Do vyhledávacího pole zadejte `response` jako filtr. V seznamu **Akce** vyberte možnost **odpověď**.

1. Klikněte do pole **text** , aby se zobrazil seznam dynamického obsahu. V tomto seznamu v části **Odeslat zprávu do SAP** vyberte pole **text** .

   ![Dokončit akci SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Uložte aplikaci logiky.

#### <a name="create-rfc-request-response"></a>Vytvořit požadavek RFC – odpověď

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

### <a name="test-logic-app"></a>Test aplikace logiky

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

## <a name="receive-message-from-sap"></a>Přijmout zprávu z SAP

V tomto příkladu se používá aplikace logiky, která se aktivuje, když aplikace obdrží zprávu ze systému SAP.

### <a name="add-an-sap-trigger"></a>Přidání triggeru SAP

1. V Azure Portal vytvořte prázdnou aplikaci logiky, která otevře návrháře Logic Apps.

1. Do vyhledávacího pole zadejte `sap` jako filtr. V seznamu **triggery** vyberte, **kdy se má ze SAP přijmout zpráva**.

   ![Přidat aktivační událost SAP](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   Případně můžete vybrat kartu **Enterprise** a potom vybrat aktivační událost:

   ![Přidat aktivační událost SAP z karty Enterprise](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Pokud vaše připojení už existuje, pokračujte dalším krokem, abyste mohli nastavit akci SAP. Pokud se však zobrazí výzva k zadání podrobností o připojení, zadejte informace, abyste mohli nyní vytvořit připojení k místnímu serveru SAP.

   1. Zadejte název připojení.

   1. Pokud používáte bránu dat, postupujte následovně:

      1. V části **Brána dat** v části **předplatné** nejdřív vyberte předplatné Azure pro prostředek brány dat, který jste vytvořili v Azure Portal pro instalaci brány dat.

      1. V části **Brána připojení** vyberte prostředek brány dat v Azure.

   1. Pokračujte v poskytování informací o připojení. U vlastnosti **typ přihlášení** postupujte podle kroků na základě toho, jestli je vlastnost nastavená na **aplikační server** nebo **skupinu**:

      * U **aplikačního serveru** se tyto vlastnosti, které se obvykle zobrazují jako volitelné, vyžadují:

        ![Vytvořit připojení aplikačního serveru SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * V případě **skupiny** jsou vyžadovány tyto vlastnosti, které se obvykle zobrazují jako volitelné:

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

Pokud se zobrazí chybová zpráva **500 Chyba brány** se zprávou podobnou **službě ' sapgw00 ' Neznámá**, nahraďte název služby brány ve svém připojení rozhraní API a spusťte konfiguraci pomocí jejího čísla portu. V následujícím příkladu chyby je `sapgw00` nutné nahradit skutečným číslem portu, například `3300` . 

```json
"body": {
   "error": {
      "code": 500,
      "source": "EXAMPLE-FLOW-NAME.eastus.environments.microsoftazurelogicapps.net",
      "clientRequestId": "00000000-0000-0000-0000-000000000000",
      "message": "BadGateway",
      "innerError": {
         "error": {
            "code": "UnhandledException",
            "message": "\nERROR service 'sapgw00' unknown\nTIME Wed Nov 11 19:37:50 2020\nRELEASE 721\nCOMPONENT NI (network interface)\nVERSION 40\nRC -3\nMODULE ninti.c\nLINE 933\nDETAIL NiPGetServByName: 'sapgw00' not found\nSYSTEM CALL getaddrinfo\nCOUNTER 1\n\nRETURN CODE: 20"
         }
      }
```

#### <a name="parameters"></a>Parametry

Spolu s jednoduchým řetězcem a číselnými vstupy akceptuje konektor SAP následující parametry tabulky ( `Type=ITAB` vstupy):

* Parametry směru tabulky, vstupní i výstupní pro starší verze SAP.

* Změna parametrů, které nahradí parametry směru tabulky pro novější verze SAP.

* Parametry hierarchické tabulky

#### <a name="filter-with-sap-actions"></a>Filtrování pomocí akcí SAP

Volitelně můžete filtrovat zprávy, které aplikace logiky přijme ze serveru SAP, zadáním seznamu nebo pole s jednou nebo několika akcemi SAP. Ve výchozím nastavení je toto pole prázdné, což znamená, že aplikace logiky obdrží všechny zprávy ze serveru SAP bez filtrování. 

Když nastavíte filtr pole, aktivační událost přijímá jenom zprávy ze zadaných typů akcí SAP a odmítne všechny ostatní zprávy ze serveru SAP. Tento filtr ale nemá vliv na to, jestli je text obdržené datové části slabý nebo silný.

Jakékoli filtrování akcí SAP se stane na úrovni adaptéru SAP pro vaši místní bránu dat. Další informace najdete v tématu [Jak odeslat testovací IDOCs do Logic Apps z SAP](#test-sending-idocs-from-sap).

Pokud nemůžete odesílat IDoc pakety z SAP do triggeru vaší aplikace logiky, přečtěte si zprávu o odmítnutí volání transakčního RFC (tRFC) v dialogovém okně SAP tRFC (T-Code SM58). V rozhraní SAP se může zobrazit následující chybové zprávy, které jsou oříznuté z důvodu omezení podřetězců v **textovém poli Stav** .

* `The RequestContext on the IReplyChannel was closed without a reply being`: K neočekávaným chybám dochází, když popisovač catch-All pro kanál ukončí kanál z důvodu chyby a znovu sestaví kanál pro zpracování dalších zpráv.

  * Pokud chcete potvrdit, že aplikace logiky přijala IDoc, [přidejte akci odpovědi](../connectors/connectors-native-reqres.md#add-a-response-action) , která vrátí `200 OK` stavový kód. IDoc je přepravován prostřednictvím tRFC, což nepovoluje datovou část odpovědi.

  * Pokud potřebujete IDoc místo toho, odpovězte s jakýmkoli stavovým kódem HTTP, který je jiný než `200 OK` tak, že adaptér SAP vrátí výjimku zpět do SAP vaším jménem. 

* `The segment or group definition E2EDK36001 was not found in the IDoc meta`: K očekávaným chybám dochází s jinými chybami, jako je například selhání při generování datové části XML IDoc, protože její segmenty nejsou uvolněny SAP, takže chybí metadata typu segmentu požadovaná pro převod. 

  * Pokud chcete tyto segmenty uvolnit pomocí SAP, obraťte se na pracovníky ABAP pro váš systém SAP.
### <a name="asynchronous-request-reply-for-triggers"></a>Asynchronní požadavek-odpověď pro aktivační události

Konektor SAP podporuje pro aktivační události Logic Apps [asynchronní vzor požadavků a odpovědí](/azure/architecture/patterns/async-request-reply) pro Azure. Tento model můžete použít k vytvoření úspěšných požadavků, které by jinak selhaly s výchozím vzorem synchronního požadavku-odpovědi. 

> [!TIP]
> V Logic Apps s více akcemi odezvy musí všechny akce odpovědi používat stejný vzor požadavku a odpovědi. Například pokud vaše aplikace logiky používá ovládací prvek switch s více možnými akcemi reakce, je nutné nakonfigurovat všechny akce reakce na použití stejného vzoru požadavek-odpověď, buď synchronní, nebo asynchronní. 

Povolení asynchronní odezvy pro akci odpovědi umožní vaší aplikaci logiky reagovat na odpověď, `202 Accepted` když je žádost přijata ke zpracování. Odpověď obsahuje hlavičku umístění, kterou můžete použít k načtení konečného stavu vaší žádosti.

Konfigurace asynchronního vzoru požadavků a odpovědí pro vaši aplikaci logiky pomocí konektoru SAP:

1. Otevřete aplikaci logiky v **návrháři Logic Apps**.

1. Potvrďte, že konektor SAP je triggerem pro vaši aplikaci logiky.

1. Otevřete akci **reakce** aplikace logiky. V záhlaví akce vyberte nabídku (**...**) &gt; . **Nastavení**.

1. V **Nastavení** akce odpověď zapněte přepínač v části **asynchronní odezva**. Vyberte Hotovo.

1. Uložte změny do aplikace logiky.

## <a name="find-extended-error-logs"></a>Vyhledání rozšířených protokolů chyb

V případě úplných chybových zpráv se podívejte na rozšířené protokoly adaptéru SAP. Můžete také [Povolit rozšířený soubor protokolu pro konektor SAP](#extended-sap-logging-in-on-premises-data-gateway).

V případě verzí pro místní bránu dat od června 2020 a novějších můžete [Povolit protokoly brány v nastavení aplikace](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app). 

* Výchozí úroveň protokolování je **Upozornění**.

* Pokud povolíte  **Další protokolování** v nastavení **diagnostiky** aplikace místní brány dat, zvýší se úroveň protokolování na **informativní**.

* Pokud chcete zvýšit úroveň protokolování na **verbose**, aktualizujte následující nastavení konfiguračního souboru. Konfigurační soubor je obvykle umístěn na adrese `C:\Program Files\On-premises data gateway\Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config` .

```json
<setting name="SapTraceLevel" serializeAs="String">
   <value>Verbose</value>
</setting>
```

V případě verzí pro místní bránu dat od dubna 2020 a novějších jsou protokoly ve výchozím nastavení zakázané.

### <a name="extended-sap-logging-in-on-premises-data-gateway"></a>Rozšířené protokolování SAP v místní bráně dat

Pokud [pro Logic Apps používáte místní bránu dat](../logic-apps/logic-apps-gateway-install.md), můžete pro konektor SAP nakonfigurovat rozšířený soubor protokolu. Místní bránu dat můžete použít k přesměrování událostí trasování událostí pro Windows (ETW) do souborů rotujících protokolů, které jsou zahrnuté v souboru protokolování. zip brány. 

Do souboru. zip v nastavení aplikace brány můžete [exportovat všechny protokoly konfigurace a služby brány](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app) .

> [!NOTE]
> Rozšířené protokolování může ovlivnit výkon aplikace Logic Apps, pokud je vždy povoleno. Je osvědčeným postupem, jak vypnout rozšířené soubory protokolu po dokončení analýzy a řešení problémů.

#### <a name="capture-etw-events"></a>Zaznamenat události ETW

Volitelně můžou pokročilí uživatelé zachytit události ETW přímo. Data pak můžete [využívat v Azure Diagnostics Event Hubs](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md) nebo [shromažďovat data do protokolů Azure monitor](/azure/azure-monitor/agents/diagnostics-extension-logs). Další informace najdete v tématu [osvědčené postupy pro shromažďování a ukládání dat](/azure/architecture/best-practices/monitoring#collecting-and-storing-data). [PerfView](https://github.com/Microsoft/perfview/blob/master/README.md) můžete použít k práci s výslednými soubory ETL nebo můžete napsat vlastní program. Tento návod používá PerfView:

1. V nabídce PerfView vyberte **shromáždit** &gt; **shromáždění** a zachyťte události.

1. Do pole **Další zprostředkovatel** zadejte, pokud chcete `*Microsoft-LobAdapter` zadat zprostředkovatele SAP pro zachycení událostí adaptéru SAP. Pokud tyto informace nezadáte, zahrnuje trasování pouze obecné události ETW.

1. Ponechte ostatní výchozí nastavení. Pokud chcete, můžete změnit název nebo umístění souboru v poli **datový soubor** .

1. Vyberte **Spustit shromažďování** a začněte sledovat.

1. Po reprodukování problému nebo shromáždění dostatečných dat analýzy vyberte **Zastavit shromažďování**.

Pokud chcete svá data sdílet s jinou stranou, jako jsou technici podpory Azure, Zkomprimujte soubor ETL.

Zobrazení obsahu trasování:

1. V PerfView vyberte **soubor** &gt; **otevřít** a vyberte soubor ETL, který jste právě vygenerovali.

1. Na bočním panelu PerfView v části **události** v souboru ETL.

1. V části **filtrovat** filtr podle `Microsoft-LobAdapter` zobrazíte pouze relevantní události a procesy brány.

### <a name="test-your-logic-app"></a>Testování aplikace logiky

1. Pokud chcete aktivovat aplikaci logiky, odešlete zprávu ze systému SAP.

1. V nabídce aplikace logiky vyberte **Přehled**. Podívejte se na **historii spuštění** pro jakékoli nové běhy aplikace logiky.

1. Otevřete poslední spuštění, ve kterém se zobrazí zpráva odeslaná ze systému SAP v oddílu aktivační výstupy triggeru.

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
    
    * Jako **cíl RFC** zadejte název.
    
    * Na kartě **technické nastavení** pro **typ aktivace** vyberte **registrovaný serverový program**. Jako **ID programu** zadejte hodnotu. V SAP se Trigger vaší aplikace logiky zaregistruje pomocí tohoto identifikátoru.

    > [!IMPORTANT]
    > V **ID programu** SAP se rozlišují malá a velká písmena. Při konfiguraci aplikace logiky a serveru SAP nezapomeňte konzistentně používat stejný formát případu pro vaše **ID programu** . V opačném případě se může při pokusu o odeslání IDoc do SAP zobrazit následující chyby v monitoru tRFC (T-Code SM58):
    >
    > * **Funkce IDOC_INBOUND_ASYNCHRONOUS nenalezena**
    > * **Klient RFC bez ABAP (typ partnera) se nepodporuje.**
    >
    > Další informace ze SAP najdete v následujících poznámkách (vyžaduje se přihlášení) <https://launchpad.support.sap.com/#/notes/2399329> a <https://launchpad.support.sap.com/#/notes/353597> .
    
    * Na kartě **Unicode** pro **typ komunikace s cílovým systémem** vyberte **Unicode**.

1. Uložte provedené změny.

1. Zaregistrujte nové **ID programu** pomocí Azure Logic Apps.

1. Chcete-li otestovat připojení, vyberte v rozhraní SAP v části nový **cíl RFC** možnost **Test připojení**.

#### <a name="create-abap-connection"></a>Vytvořit připojení ABAP

1. Pokud chcete otevřít **konfiguraci nastavení připojení RFC** , použijte v rozhraní SAP kód transakce **Sm59** _ (T-Code) s předponou _ */n**.

1. Vyberte **připojení ABAP**  >  **vytvořit**.

1. V části **cíl RFC** zadejte identifikátor [testovacího systému SAP](#create-rfc-destination).

1. Uložte provedené změny.

1. Chcete-li otestovat připojení, vyberte možnost **Test připojení**.

#### <a name="create-receiver-port"></a>Vytvořit port přijímače

1. Chcete-li otevřít **porty v nastavení zpracování IDOC** , v rozhraní SAP použijte kód transakce **WE21** (T-Code) s předponou **/n** .

1. Vyberte **porty**  >  **transakční RFC**  >  **vytvořit**.

1. V okně nastavení, které se otevře, vyberte **vlastní název portu**. Jako port testu zadejte **název**. Uložte provedené změny.

1. V nastavení pro nový port přijímače pro **specifikaci RFC** zadejte do pole cíl [RFC testu](#create-rfc-destination)identifikátor.

1. Uložte provedené změny.

#### <a name="create-sender-port"></a>Vytvořit port odesílatele

1.  Chcete-li otevřít **porty v nastavení zpracování IDOC** , v rozhraní SAP použijte kód transakce **WE21** (T-Code) s předponou **/n** .

1. Vyberte **porty**  >  **transakční RFC**  >  **vytvořit**.

1. V okně nastavení, které se otevře, vyberte **vlastní název portu**. Pro port testu zadejte **název** , který začíná na **SAP**. Všechny názvy portů odesílatele musí začínat písmeny **SAP**, například **SAPTEST**. Uložte provedené změny.

1. V nastavení pro nový port odesílatele zadejte do pole **cíl RFC** zadání identifikátoru pro [připojení ABAP](#create-abap-connection).

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

1. V části **profily partnerů** vyberte **Typ partnera LS**  >  **vytvořit**.

1. Vytvořte nový profil partnera s následujícím nastavením:

    * Jako **partner – ne.** zadejte [identifikátor vašeho partnera logického systému](#create-logical-system-partner).

    * Pro **partn. Zadejte**, zadejte **ls**.

    * Pro **agenta** zadejte identifikátor pro uživatelský účet SAP, který se má použít při registraci identifikátorů programu pro Azure Logic Apps nebo jiné systémy bez SAP.

1. Uložte provedené změny. Pokud jste [nevytvořili partnera logického systému](#create-logical-system-partner), zobrazí se chyba, **Zadejte platné číslo partnera**.

1. V nastavení vašeho partnerského profilu v části **odchozí parmtrs** vyberte **vytvořit odchozí parametr**.

1. Vytvořte nový odchozí parametr s následujícím nastavením:

    * Zadejte **typ zprávy**, například **CREMAS**.

    * Zadejte [identifikátor portu přijímače](#create-receiver-port).

    * Zadejte IDoc velikost **balíčku. Velikost**. Nebo pokud chcete [Odeslat IDOCs po jednom z SAP](#receive-idoc-packets-from-sap), vyberte možnost **předat IDOC hned** po druhém.

1. Uložte provedené změny.

#### <a name="test-sending-messages"></a>Testování odesílání zpráv

1. Chcete-li otevřít **Nástroj test Tool pro IDOC zpracování** , v rozhraní SAP použijte kód transakce **we19** (T-Code) s předponou **/n** .

1. V části **Šablona pro test** vyberte **typ zprávy** a zadejte typ zprávy, například **CREMAS**. Vyberte **Vytvořit**.

1. Výběrem možnosti **pokračovat** potvrďte, **který typ IDOC?** zpráva.

1. Vyberte uzel **EDIDC** . Zadejte odpovídající hodnoty pro port příjemce a odesilatele. Vyberte **Pokračovat**.

1. Vyberte **standardní odchozí zpracování**.

1. Pokud chcete spustit odchozí IDoc zpracování, vyberte **pokračovat**. Po dokončení zpracování se zobrazí zpráva **IDOC odeslána do systému SAP nebo externího programu** .

1.  Chcete-li zjistit chyby zpracování, použijte kód transakce **SM58** (T-Code) s předponou **/n** .

## <a name="receive-idoc-packets-from-sap"></a>Příjem paketů IDoc ze SAP

Můžete nastavit SAP pro [posílání IDOCs v paketech](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/4ab38886549a6d8ce10000000a42189c.html), což jsou dávky nebo skupiny IDOCs. Aby bylo možné přijímat pakety IDoc, konektor SAP a konkrétně Trigger nevyžadují další konfiguraci. Pokud však chcete zpracovat každou položku v paketu IDoc poté, co Trigger obdrží paket, je nutné provést některé další kroky pro rozdělení paketu do jednotlivých IDocs.

Tady je příklad, který ukazuje, jak extrahovat jednotlivé IDocs z paketu pomocí [ `xpath()` funkce](./workflow-definition-language-functions-reference.md#xpath):

1. Než začnete, budete potřebovat aplikaci logiky s triggerem SAP. Pokud tuto aplikaci logiky ještě nemáte, pomocí předchozích kroků v tomto tématu [nastavte aplikaci logiky pomocí triggeru SAP](#receive-message-from-sap).

    > [!IMPORTANT]
    > V **ID programu** SAP se rozlišují malá a velká písmena. Při konfiguraci aplikace logiky a serveru SAP nezapomeňte konzistentně používat stejný formát případu pro vaše **ID programu** . V opačném případě se může při pokusu o odeslání IDoc do SAP zobrazit následující chyby v monitoru tRFC (T-Code SM58):
    >
    > * **Funkce IDOC_INBOUND_ASYNCHRONOUS nenalezena**
    > * **Klient RFC bez ABAP (typ partnera) se nepodporuje.**
    >
    > Další informace ze SAP najdete v následujících poznámkách (vyžaduje se přihlášení) <https://launchpad.support.sap.com/#/notes/2399329> a <https://launchpad.support.sap.com/#/notes/353597> .

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

Můžete použít šablonu pro rychlý Start pro tento model výběrem této šablony v Návrháři Logic Apps při vytváření nové aplikace logiky.

![Vybrat šablonu aplikace Batch Logic](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Generování schémat pro artefakty v SAP

V tomto příkladu se používá aplikace logiky, kterou můžete aktivovat pomocí požadavku HTTP. Chcete-li generovat schémata pro zadané IDoc a BAPI, odešle **schéma** akce SAP do systému SAP požadavek.

Tato akce SAP vrátí [schéma XML](#sample-xml-schemas), nikoli obsah nebo data samotného dokumentu XML. Schémata vrácená v odpovědi se odesílají na účet pro integraci pomocí konektoru Azure Resource Manager. Schémata obsahují tyto části:

* Struktura zprávy požadavku. Tyto informace slouží k vytvoření seznamu BAPI `get` .

* Struktura zprávy s odpovědí. Tyto informace slouží k analýze odpovědi. 

K odeslání zprávy s požadavkem použijte obecnou akci SAP **Odeslat zprávu do SAP** nebo cílené akce **BAPI volání** .

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

#### <a name="xml-samples-for-rfc-requests"></a>Ukázky XML pro požadavky RFC

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

#### <a name="xml-samples-for-bapi-requests"></a>Ukázky XML pro požadavky rozhraní BAPI

Následující ukázky XML jsou příklady požadavků na [volání metody BAPI](#call-bapi-action).

> [!NOTE]
> SAP zpřístupňuje obchodní objekty externím systémům tím, že je popisuje v reakci na RFC `RPY_BOR_TREE_INIT` , které Logic Apps problémy bez vstupního filtru. Logic Apps zkontroluje výstupní tabulku `BOR_TREE` . `SHORT_TEXT`Pole se používá pro názvy obchodních objektů. Obchodní objekty nevracené SAP ve výstupní tabulce nejsou k dispozici pro Logic Apps.
> 
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

#### <a name="xml-samples-for-idoc-requests"></a>Ukázky XML pro požadavky IDoc

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

1. V Azure Portal vytvořte prázdnou aplikaci logiky, která otevře návrháře Logic Apps.

1. Do vyhledávacího pole zadejte `http request` jako filtr. V seznamu **triggery** vyberte, **kdy se přijme požadavek HTTP**.

   ![Přidat aktivační událost požadavku HTTP](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Nyní uložte aplikaci logiky, abyste mohli vygenerovat adresu URL koncového bodu pro vaši aplikaci logiky.
Na panelu nástrojů návrháře vyberte **Uložit**.

   Adresa URL koncového bodu se teď zobrazí ve triggeru, například:

   ![Vygenerovat adresu URL pro koncový bod](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Přidání akce SAP pro generování schémat

1. V Návrháři Logic Apps pod triggerem vyberte **Nový krok**.

   ![Přidat nový krok do aplikace logiky](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Do vyhledávacího pole zadejte `sap` jako filtr. V seznamu **Akce** vyberte možnost **Generovat schémata**.
  
   ![Přidání akce generovat schémata do aplikace logiky](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Nebo můžete vybrat kartu **Enterprise** a pak vybrat akci SAP.

   ![Výběr možnosti SAP odeslat akci na kartě organizace](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Pokud vaše připojení už existuje, pokračujte dalším krokem, abyste mohli nastavit akci SAP. Pokud se však zobrazí výzva k zadání podrobností o připojení, zadejte informace, abyste mohli nyní vytvořit připojení k místnímu serveru SAP.

   1. Zadejte název připojení.

   1. V části **Brána dat** v části **předplatné** nejdřív vyberte předplatné Azure pro prostředek brány dat, který jste vytvořili v Azure Portal pro instalaci brány dat. 
   
   1. V části **Brána připojení** vyberte prostředek brány dat v Azure.

   1. Pokračujte v poskytování informací o připojení. U vlastnosti **typ přihlášení** postupujte podle kroků na základě toho, jestli je vlastnost nastavená na **aplikační server** nebo **skupinu**:
   
      * U **aplikačního serveru** se tyto vlastnosti, které se obvykle zobrazují jako volitelné, vyžadují:

        ![Vytvořit připojení aplikačního serveru SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * V případě **skupiny** jsou vyžadovány tyto vlastnosti, které se obvykle zobrazují jako volitelné:

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

1. V Návrháři Logic Apps pod triggerem vyberte **Nový krok**.

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

## <a name="enable-secure-network-communications"></a>Povolit zabezpečenou síťovou komunikaci

Než začnete, ujistěte se, že jste splnili výše uvedené [předpoklady](#prerequisites), které platí jenom v případě, že používáte bránu data Gateway a vaše aplikace logiky běží ve více klientech Azure:

* Ujistěte se, že místní brána dat je nainstalovaná na počítači, který je ve stejné síti jako váš systém SAP.

* Pro jednotné přihlašování je brána dat spuštěná jako uživatel, který je namapovaný na uživatele SAP.

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

1. V editoru pro **proměnnou inicializovat** akci nakonfigurujte následující nastavení. Pak změny uložte.

    1. Jako **název** zadejte název proměnné. Například, `IDOCtransferID`.

    1. Jako typ proměnné vyberte **typ** **řetězec** .

    1. V poli **hodnota** vyberte textové pole a **Zadejte počáteční hodnotu** . tím otevřete nabídku dynamického obsahu. Vyberte kartu **výrazy** . V seznamu funkcí zadejte funkci `guid()` . Pak kliknutím na **OK** uložte změny. Pole **hodnota** je nyní nastaveno na `guid()` funkci, která generuje identifikátor GUID.

1. Po akci **inicializovat proměnnou** přidejte akci **Odeslat IDOC**.

1. V editoru akce **Odeslat IDOC** nakonfigurujte následující nastavení. Pak změny uložte.

    1. Jako **typ IDOC** vyberte typ zprávy a zadejte zprávu do zprávy **input IDOC**.

    1. V případě **verze SAP Release** vyberte hodnoty konfigurace SAP.

    1. V případě **verze typů záznamů** vyberte hodnoty konfigurace SAP.

    1. Pro **Potvrdit TID** vyberte možnost **ne**.

    1. Vyberte **Přidat nový**  >  **identifikátor GUID ID transakce** seznamu parametrů. Výběrem textového pole otevřete nabídku dynamického obsahu. Na kartě **proměnné** vyberte název proměnné, kterou jste vytvořili. Například, `IDOCtransferID`.

1. V záhlaví akce **Odeslat IDOC** vyberte **...**  >  **Nastavení**. U **zásady opakování** doporučujeme vybrat možnost **výchozí** &gt; **Hotovo**. Místo toho ale můžete nakonfigurovat vlastní zásadu pro konkrétní potřeby. Pro vlastní zásady se doporučuje nakonfigurovat aspoň jeden pokus o překonání dočasných výpadků sítě.

1. Po **odeslání IDOC** akce přidejte **ID transakce potvrdit** akci.

1. V editoru pro akci **Potvrdit ID transakce** nakonfigurujte následující nastavení. Pak změny uložte.

    1. Jako **ID transakce** zadejte název proměnné znovu. Například, `IDOCtransferID`.

1. Volitelně můžete ověřit odstranění duplicit v testovacím prostředí. Opakujte akci **Odeslat IDOC** se stejným identifikátorem GUID **ID transakce** , který jste použili v předchozím kroku. Když odesíláte stejný IDoc dvakrát, můžete ověřit, že SAP dokáže identifikovat duplikaci volání tRFC a vyřešit dvě volání na jednu příchozí zprávu IDoc.

## <a name="known-issues-and-limitations"></a>Známé problémy a omezení

Tady jsou aktuálně známé problémy a omezení pro spravovaný konektor SAP (bez ISE): 

* Obecně platí, že aktivační událost SAP nepodporuje clustery brány dat. V některých případech převzetí služeb při selhání se uzel brány dat, který komunikuje se systémem SAP, může lišit od aktivního uzlu, což vede k neočekávanému chování.

  * V případě scénářů odesílání se podporují clustery služby data Gateway v režimu převzetí služeb při selhání. 

  * Clustery brány dat v režimu Vyrovnávání zatížení nejsou podporovány stavovým akcí SAP. Mezi tyto akce patří **Vytvoření stavové relace**, **potvrzení transakce BAPI**, **vrácení transakce BAPI**, **ukončení stavové relace** a všechny akce, které určují hodnotu **ID relace** . Stavová komunikace musí zůstat na stejném uzlu clusteru brány dat. 

  * U stavových akcí SAP použijte bránu dat buď v režimu bez clusteru, nebo v clusteru, který je nastavený jenom pro převzetí služeb při selhání.

* Konektor SAP aktuálně nepodporuje řetězce směrovače SAP. Místní brána dat musí existovat ve stejné síti LAN jako systém SAP, který chcete připojit.


## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o této spojnici, jako jsou triggery, akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete na [referenční stránce konektoru](/connectors/sap/). Další dokumentace k Logic Apps je k dispozici pro následující akce:

* [Volat BAPI](#call-bapi-action)

* [Odeslat IDOC](#send-idoc-action)

> [!NOTE]
> V případě Logic Apps v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Tato verze konektoru ISE-Label používá místo toho [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

### <a name="call-bapi-action"></a>Zavolat akci BAPI

Akce [volání BAPI ( `CallBapi` )](
https://docs.microsoft.com/connectors/sap/#call-bapi-(preview)) volá metodu BAPI na vašem serveru SAP. 

Pro vaše volání musíte použít následující parametry: 

* **Business Object** ( `businessObject` ), což je prohledávatelné rozevírací nabídky.

* **Metoda** ( `method` ), která vyplní dostupné metody po výběru **obchodního objektu**. Dostupné metody se liší v závislosti na vybraném **obchodním objektu**.

* **Vstupní parametry BAPI** ( `body` ), ve kterých zavoláte dokument XML, který obsahuje hodnoty vstupních parametrů metody BAPI pro volání, nebo identifikátor URI objektu BLOB úložiště, který obsahuje parametry BAPI.

Podrobné příklady, jak použít akci volání BAPI, naleznete v [ukázkách XML požadavků BAPI](#xml-samples-for-bapi-requests).

> [!TIP]
> Pokud používáte návrháře Logic Apps k úpravám vaší žádosti BAPI, můžete použít následující funkce hledání: 
> 
> * Vyberte objekt v návrháři, aby se zobrazila rozevírací nabídka dostupných metod.
> * Filtrování typů obchodních objektů podle klíčového slova pomocí prohledávatelných seznamů poskytovaných voláním rozhraní API BAPI.

### <a name="send-idoc-action"></a>Odeslat akci IDoc

Akce [Odeslat IDOC ( `SendIDoc` )](/connectors/sap/) odešle zprávu IDOC na Server SAP.

Pro vaše volání musíte použít následující parametry: 

* **Typ IDOC s volitelnou příponou** ( `idocType` ), což je rozevírací nabídka, kterou lze prohledávat.

    * Volitelný parametr **verze SAP** ( `releaseVersion` ) vyplní hodnoty po výběru typu IDoc a závisí na zvoleném typu IDoc.

* **Vstupní IDOC zpráva** ( `body` ), ve které zavoláte dokument XML obsahující datovou část IDOC, nebo identifikátor URI objektu BLOB úložiště, který obsahuje váš dokument XML IDOC. Tento dokument musí být v souladu se schématem SAP IDOC XML podle dokumentace WE60 IDoc, nebo vygenerovaným schématem pro odpovídající identifikátor URI pro SAP IDoc Action.

Podrobné příklady použití akce Odeslat IDoc najdete v [návodu k posílání zpráv IDOC na Server SAP](#send-idoc-messages-to-sap-server).

Informace o použití volitelného parametru **Confirm TID** ( `confirmTid` ) naleznete v [návodu k explicitnímu potvrzení transakce](#confirm-transaction-explicitly).

## <a name="next-steps"></a>Další kroky

* [Připojte se k místním systémům](../logic-apps/logic-apps-gateway-connection.md) z Azure Logic Apps.

* Naučte se, jak pomocí [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)ověřit, transformovat a používat jiné operace se zprávou.

* Přečtěte si o dalších [konektorech Logic Apps](../connectors/apis-list.md).