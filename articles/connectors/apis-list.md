---
title: Konektory pro Azure Logic Apps
description: Automatizace pracovních postupů pomocí konektorů pro Azure Logic Apps, včetně integrovaného, spravovaného, místního, integračního účtu a podnikových konektorů
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: 642eef8b804a7b71ec16f5834e4989cb7299c408
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789968"
---
# <a name="connectors-for-azure-logic-apps"></a>Konektory pro Azure Logic Apps

Konektory poskytují rychlý přístup z Azure Logic Apps k událostem, datům a akcím napříč dalšími aplikacemi, službami, systémy, protokoly a platformami. Použitím konektorů v aplikacích logiky můžete své cloudové i místní aplikace rozšířit o možnosti provádět úlohy s daty, která již máte nebo teprve vytvoříte.

I když Logic Apps nabízí [stovky konektorů](https://docs.microsoft.com/connectors), Tento článek popisuje oblíbené a častěji používané konektory, které jsou úspěšně používány tisíci aplikací a miliony spouštění pro zpracování dat a informací. Pokud chcete najít úplný seznam konektorů a referenčních informací o jednotlivých konektorech, jako jsou triggery, akce a omezení, přečtěte si referenční stránky konektoru v části [konektory přehled](https://docs.microsoft.com/connectors). Přečtěte si taky další informace o [aktivačních událostech a akcích](#triggers-actions), [Logic Apps cenovém modelu](../logic-apps/logic-apps-pricing.md)a [Logic Apps podrobnosti o cenách](https://azure.microsoft.com/pricing/details/logic-apps/). 

> [!NOTE]
> Pro integraci se službou nebo rozhraním API, které nemají konektor, můžete buď přímo zavolat službu přes protokol, jako je HTTP, nebo vytvořit [vlastní konektor](#custom).

Konektory jsou k dispozici buď jako integrované triggery a akce, nebo jako spravované konektory:

<a name="built-in"></a>

* [**Předdefinované**](#built-ins): tyto integrované triggery a akce jsou "nativní", které jsou Azure Logic Apps a umožňují vytvářet aplikace logiky spouštěné ve vlastních plánech, komunikovat s dalšími koncovými body, přijímat a reagovat na požadavky a volat služby Azure Functions, Azure API Apps (Web Apps), vaše vlastní rozhraní API spravovaná a publikovaná pomocí Azure API Management a vnořené aplikace logiky, které můžou přijímat požadavky. Můžete také použít integrované akce, které vám pomůžou organizovat a řídit pracovní postup vaší aplikace logiky a také pracovat s daty.

  > [!NOTE]
  > Logic Apps v rámci [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) mají přímý přístup k prostředkům ve službě Azure Virtual Network.
  > Když použijete ISE, integrované triggery a akce, které zobrazují **základní** popisek, se spouští ve stejném ISE jako vaše aplikace logiky. Logic Apps, integrované triggery a integrované akce, které běží ve vašem ISE, používají Cenový tarif, který se liší od cenového plánu založeného na spotřebě.
  >
  > Další informace o vytváření ISEs najdete v tématu [připojení k virtuálním sítím Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). 
  > Další informace o cenách najdete v tématu [Logic Apps cenového modelu](../logic-apps/logic-apps-pricing.md).

<a name="managed-connectors"></a>

* **Spravované konektory**: nasazené a spravované Microsoftem, tyto konektory poskytují triggery a akce pro přístup ke cloudovým službám, místním systémům nebo oběma, včetně Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint a dalších. Některé konektory konkrétně podporují komunikační scénáře B2B (Business-to-Business) a vyžadují [účet pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , který je propojený s vaší aplikací logiky. Než začnete používat určité konektory, možná budete muset nejdřív vytvořit připojení, která jsou spravovaná pomocí Azure Logic Apps. 

  Pokud například používáte Microsoft BizTalk Server, vaše aplikace logiky se mohou připojit ke svému BizTalk Server a komunikovat s nimi pomocí [konektoru BizTalk Server On-Premises Connector](#on-premises-connectors). 
  Pomocí [konektorů účtu pro integraci](#integration-account-connectors)pak můžete ve svých aplikacích logiky roztáhnout nebo provádět operace typu BizTalk.

  Konektory jsou klasifikovány buď jako standard, nebo jako podnikové. 
  [Podnikové konektory](#enterprise-connectors) poskytují pro další náklady přístup k podnikovým systémům, jako jsou SAP, IBM MQ a IBM 3270. Pokud chcete zjistit, jestli je konektor Standard nebo Enterprise, přečtěte si téma technické podrobnosti na referenční stránce každého konektoru v části [konektory – přehled](https://docs.microsoft.com/connectors). 

  Pomocí těchto kategorií můžete také identifikovat konektory, i když některé konektory mohou více kategorií. 
  Například SAP je konektor Enterprise a konektor On-Premises Connector:

  |   |   |
  |---|---|
  | [**Spravované konektory rozhraní API**](#managed-api-connectors) | Vytvářejte aplikace logiky, které používají služby, jako je Azure Blob Storage, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online a spoustu dalších. |
  | [**Místní konektory**](#on-premises-connectors) | Když nainstalujete a nastavíte místní [bránu dat][gateway-doc], tyto konektory pomůžou vašim aplikacím logiky přístup k místním systémům, jako je SQL Server, SharePoint Server, Oracle DB, sdílené složky a další. |
  | [**Konektory účtu pro integraci**](#integration-account-connectors) | K dispozici při vytváření a placení účtu pro integraci, tyto konektory transformují a ověřují nestrukturované soubory XML, kódují a dekódovat ploché soubory a zpracovávají zprávy B2B (Business-to-Business) pomocí protokolů AS2, EDIFACT a X12. |
  |||

  > [!NOTE]
  > Logic Apps v rámci [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) mají přímý přístup k prostředkům ve službě Azure Virtual Network. Když použijete konektory ISE, Standard a Enterprise, které zobrazují popisek **ISE** , běží ve stejném ISE jako vaše aplikace logiky. Konektory, které nezobrazují ISE, se spouštějí v globálním Logic Apps službě.
  >
  > Pro místní systémy, které jsou připojené k virtuální síti Azure, zapište své ISE do této sítě, aby vaše aplikace logiky mohly přímý přístup k těmto systémům pomocí konektoru s popiskem **ISE** , akce http nebo [vlastního konektoru](#custom). Logic Apps a konektory, které běží ve vašem ISE, používají Cenový tarif, který se liší od cenového plánu založeného na spotřebě. 
  >
  > Další informace o vytváření ISEs najdete v tématu [připojení k virtuálním sítím Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).
  > Další informace o cenách najdete v tématu [Logic Apps cenového modelu](../logic-apps/logic-apps-pricing.md).

  Úplný seznam konektorů a referenčních informací o jednotlivých konektorech, jako jsou akce a libovolné triggery, které jsou definovány OpenAPI (dříve Swagger), spolu s případnými limity, najdete v části [Přehled konektorů](/connectors/)úplný seznam. Informace o cenách najdete v tématu [Logic Apps cenového modelu](../logic-apps/logic-apps-pricing.md)a [Logic Apps podrobnosti o cenách](https://azure.microsoft.com/pricing/details/logic-apps/). 

<a name="built-ins"></a>

## <a name="built-ins"></a>Vestavěné moduly

Logic Apps poskytuje integrované triggery a akce, takže můžete vytvářet pracovní postupy založené na plánech, pomáhat aplikacím logiky komunikovat s dalšími aplikacemi a službami, řídit pracovní postup prostřednictvím aplikací logiky a spravovat nebo manipulovat s daty.

|   |   |   |   | 
|---|---|---|---| 
| [ikona rozhraní API ![][schedule-icon]<br/>**plán**][recurrence-doc] | – Spuštění aplikace logiky podle zadaného plánu, od Basic až po složitá opakování, s triggerem **opakování** . <p>– Pozastaví vaši aplikaci logiky o zadanou dobu s akcí **zpoždění** . <p>– Pozastavit aplikaci logiky do zadaného data a času s **prodlevou, dokud** neproběhne akce. | [ikona rozhraní ![API][http-icon]<br/>**http**][http-doc] | Komunikujte s libovolným koncovým bodem přes protokol HTTP s triggery a akcemi pro HTTP, HTTP + Swagger a HTTP + Webhook. | 
| [ikona rozhraní API ![][http-request-icon]<br/>**žádost**][http-request-doc] | – Aplikaci logiky můžete volat z jiných aplikací nebo služeb, aktivovat Event Grid události prostředků nebo aktivovat odpovědi na Azure Security Center výstrahy s triggerem **požadavku** . <p>– Odešlete odpovědi na aplikaci nebo službu s akcí **Response** . | [ikona rozhraní API ![][batch-icon]<br/>**Batch**][batch-doc] | – Zpracování zpráv v dávkách pomocí triggeru **zprávy Batch** . <p>– Volejte aplikace logiky, které mají existující triggery Batch, pomocí akce **Odeslat zprávy do dávky** . | 
| [][azure-functions-icon]<br/>**Azure Functions** ikona ![rozhraní API][azure-functions-doc] | Volejte Azure Functions, které spouštějí vlastní fragmentyC# kódu (nebo Node. js) z vašich aplikací logiky. | [ikona rozhraní ![API][azure-api-management-icon]</br>**Azure API Management**][azure-api-management-doc] | Volání triggerů a akcí definovaných vašimi rozhraními API, která spravujete a publikujete pomocí Azure API Management. | 
| [ikona rozhraní ![API][azure-app-services-icon]<br/>**Azure App Services**][azure-app-services-doc] | Zavolejte Azure API Apps nebo Web Apps hostovaný na Azure App Service. Aktivační události a akce, které tyto aplikace definují, se zobrazí stejně jako jakékoli jiné aktivační události první třídy a akce, když je součástí Swagger. | [ikona rozhraní ![API][azure-logic-apps-icon]<br/>**Azure<br/>Logic Apps**][nested-logic-app-doc] | Volejte jiné aplikace logiky, které začínají triggerem žádosti. | 
||||| 

### <a name="control-workflow"></a>Pracovní postup ovládacího prvku

Logic Apps poskytuje předdefinované akce pro strukturování a řízení akcí v pracovním postupu vaší aplikace logiky:

|   |   |   |   | 
|---|---|---|---| 
| [][condition-icon]<br/>**podmínku** ![vestavěné ikony][condition-doc] | Vyhodnoťte podmínku a spusťte různé akce na základě toho, zda je podmínka pravdivá, nebo false. | [![][for-each-icon]</br>vestavěné ikony **pro každý**][for-each-doc] | Proveďte stejné akce u každé položky v poli. | 
| [![**rozsah**][scope-icon]<br/>vestavěné ikony][scope-doc] | Seskupí akce do *oborů*, které po dokončení akcí v oboru dostanou svůj vlastní stav. | [][switch-icon]</br>**přepínač** ![vestavěné ikony][switch-doc] | Akce skupiny do *případů*, kterým jsou přiřazeny jedinečné hodnoty s výjimkou výchozího případu. Spustí pouze tento případ, jehož přiřazená hodnota odpovídá výsledku z výrazu, objektu nebo tokenu. Pokud žádné shody neexistují, spusťte výchozí případ. | 
| [![vestavěné ikony][terminate-icon]<br/>**ukončení**][terminate-doc] | Zastaví aktivně běžící pracovní postup aplikace logiky. | [![vestavěné ikony][until-icon]<br/>**až**][until-doc] | Akce opakujte, dokud není zadaná podmínka pravdivá nebo se změnil stav. | 
||||| 

### <a name="manage-or-manipulate-data"></a>Správa dat nebo manipulace s nimi

Logic Apps poskytuje předdefinované akce pro práci s datovými výstupy a jejich formáty:  

|   |   | 
|---|---| 
| [![vestavěné ikony][data-operations-icon]<br/>**datové operace**][data-operations-doc] | Provádět operace s daty: <p>- **vytváření**: vytvořte jeden výstup z více vstupů s různými typy. <br>- **Vytvoření tabulky CSV**: z pole s objekty JSON vytvořte tabulku s hodnotami oddělenými čárkou (CSV). <br>- **Vytvoření tabulky HTML**: vytvoření tabulky HTML z pole s objekty JSON. <br>- **pole filtru**: vytvořte pole z položek v jiném poli, které splňuje vaše kritéria. <br>- **Join**: Vytvořte řetězec ze všech položek v poli a oddělte tyto položky zadaným oddělovačem. <br>- **analyzovat JSON**: umožňuje vytvořit uživatelsky přívětivé tokeny z vlastností a jejich hodnot v obsahu JSON, abyste je mohli ve svém pracovním postupu použít. <br>- **Select**: vytvořte pole s objekty JSON transformacemi položek nebo hodnot v jiném poli a mapováním těchto položek na zadané vlastnosti. | 
| ![integrovaná ikona][date-time-icon]<br/>**Datum a čas** | Provádět operace s časovými razítky: <p>- **Přidat do času**: přidejte do časového razítka zadaný počet jednotek. <br>- **převést časové pásmo**: převede časové razítko ze zdrojového časového pásma na cílové časové pásmo. <br>- **aktuální čas**: vrátit aktuální časové razítko jako řetězec. <br>- **získat budoucí čas**: vrátit aktuální časové razítko a zadané časové jednotky. <br>- **získat uplynulý čas**: vrátí aktuální časové razítko minus zadané časové jednotky. <br>- **odečíst od času**: odečte počet časových jednotek od časového razítka. |
| [![**proměnných**][variables-icon]<br/>vestavěné ikony][variables-doc] | Provádět operace s proměnnými: <p>- **připojení k proměnné pole**: vloží hodnotu jako poslední položku v poli uloženém proměnnou. <br>- **připojení k proměnné řetězce**: vloží hodnotu jako poslední znak v řetězci uloženém proměnnou. <br>- **snížené proměnné**: Zmenšete proměnnou konstantní hodnotou. <br>- **přírůstku**: Zvyšte proměnnou na konstantní hodnotu. <br>- **inicializovat proměnnou**: Vytvořte proměnnou a deklarujte její datový typ a počáteční hodnotu. <br>**proměnná - sady**: přiřaďte existující proměnné jinou hodnotu. |
|  |  | 

<a name="managed-api-connectors"></a>

## <a name="managed-api-connectors"></a>Spravované konektory rozhraní API

Logic Apps poskytuje tyto oblíbené standardní konektory pro automatizaci úloh, procesů a pracovních postupů s těmito službami nebo systémy.

|   |   |   |   | 
|---|---|---|---| 
| [][azure-service-bus-icon]<br/>**Azure Service Bus** ikona ![rozhraní API][azure-service-bus-doc] | Spravujte asynchronní zprávy, relace a odběry témat pomocí nejčastěji používaného konektoru v Logic Apps. | [][sql-server-icon]<br/>**SQL Server** ikona ![rozhraní API][sql-server-doc] | Připojte se k vašemu SQL Server místně nebo Azure SQL Database v cloudu, abyste mohli spravovat záznamy, spouštět uložené procedury nebo provádět dotazy. | 
| [ikona rozhraní ![API][office-365-outlook-icon]<br/>**Office 365<br/>Outlook**][office-365-outlook-doc] | Připojte se k e-mailovému účtu Office 365, abyste mohli vytvářet a spravovat e-maily, úkoly, události kalendáře a schůzky, kontakty, žádosti a další. | [![ikona rozhraní API][azure-blob-storage-icon]<br/>**úložiště Azure Blob<br/>** ][azure-blob-storage-doc] | Připojte se k účtu úložiště, abyste mohli vytvářet a spravovat obsah objektů BLOB. | 
| [ikona rozhraní ![API][sftp-icon]<br/>**SFTP**][sftp-doc] | Připojte se k serverům SFTP, ke kterým máte přístup z internetu, abyste mohli pracovat se svými soubory a složkami. | [![ikona rozhraní API][sharepoint-online-icon]<br/>**SharePoint<br/>online**][sharepoint-online-doc] | Připojte se k SharePointu Online, abyste mohli spravovat soubory, přílohy, složky a další. | 
| [ikona rozhraní ![API][dynamics-365-icon]<br/>**Dynamics 365<br/>CRM Online**][dynamics-365-doc] | Připojte se k účtu Dynamics 365, abyste mohli vytvářet a spravovat záznamy, položky a další. | [ikona rozhraní API ![][ftp-icon]<br/>**FTP**][ftp-doc] | Připojte se k serverům FTP, ke kterým máte přístup z Internetu, abyste mohli pracovat se soubory a složkami. | 
| [ikona rozhraní API ![][salesforce-icon]<br/>**Salesforce**][salesforce-doc] | Připojte se k účtu Salesforce, abyste mohli vytvářet a spravovat položky, jako jsou záznamy, úlohy, objekty a další. | [ikona rozhraní API ![][twitter-icon]<br/>**Twitter**][twitter-doc] | Připojte se ke svému účtu na Twitteru, abyste mohli spravovat tweety, sledující, časovou osu a další funkce. Uložte si tweety do SQL, Excelu nebo SharePointu. | 
| [ikona rozhraní ![API][azure-event-hubs-icon]<br/>**Azure Event Hubs**][azure-event-hubs-doc] | Využití a publikování událostí prostřednictvím centra událostí. Můžete například získat výstup z aplikace logiky pomocí Event Hubs a potom tento výstup poslat poskytovateli analýz v reálném čase. | [![ikona rozhraní API][azure-event-grid-icon]<br/>**Azure Event**</br>**Grid**][azure-event-grid-doc] | Monitorujte události publikované Event Grid například při změně prostředků Azure nebo prostředků třetích stran. | 
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Místní konektory 

Tady jsou některé běžně používané standardní konektory, které Logic Apps poskytují přístup k datům a prostředkům v místních systémech. Než budete moct vytvořit připojení k místnímu systému, musíte nejdřív [Stáhnout, nainstalovat a nastavit místní bránu dat][gateway-doc]. Tato brána poskytuje zabezpečený komunikační kanál bez nutnosti nastavovat nezbytnou síťovou infrastrukturu. 

|   |   |   |   |   | 
|---|---|---|---|---| 
| ikona rozhraní ![API][biztalk-server-icon]<br/>**BizTalk**</br> **Server** | [ikona rozhraní ![API][file-system-icon]<br/>**systém souborů</br>** ][file-system-doc] | [ikona rozhraní API ![][ibm-db2-icon]<br/>**IBM DB2**][ibm-db2-doc] | [ikona rozhraní API ![][ibm-informix-icon]<br/>**IBM**</br> **Informix**][ibm-informix-doc] | ikona rozhraní ![API][mysql-icon]<br/>**MySQL** | 
| [][oracle-db-icon]<br/>**Oracle DB** ikona ![rozhraní API][oracle-db-doc] | ikona rozhraní ![API][postgre-sql-icon]<br/>**PostgreSQL** | [ikona rozhraní API ![][sharepoint-server-icon]<br/>**SharePoint</br> Server**][sharepoint-server-doc] | [ikona rozhraní ![API][sql-server-icon]<br/>**serveru SQL</br>** ][sql-server-doc] | ikona rozhraní ![API][teradata-icon]<br/>**Teradata** | 
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Konektory účtu pro integraci

Logic Apps poskytuje standardní konektory pro vytváření řešení B2B (Business-to-Business) s aplikacemi logiky při vytváření a placení účtu pro [integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), který je k dispozici prostřednictvím Enterprise Integration Pack (EIP) v Azure. Pomocí tohoto účtu můžete vytvářet a ukládat artefakty B2B, jako jsou obchodní partneři, smlouvy, mapy, schémata, certifikáty atd. Pokud chcete použít tyto artefakty, přidružte své aplikace logiky k účtu pro integraci. Pokud v tuto chvíli používáte BizTalk Server, tyto konektory se možná už dobře znají.

|   |   |   |   | 
|---|---|---|---| 
| [![ikona rozhraní API][as2-icon]<br/>**dekódování AS2</br>** ][as2-doc] | [![ikona rozhraní API][as2-icon]<br/>**AS2</br> kódování**][as2-doc] | [![ikona rozhraní API][edifact-icon]<br/>**dekódování EDIFACT</br>** ][edifact-decode-doc] | [![ikona rozhraní API][edifact-icon]<br/>**EDIFACT</br> kódování**][edifact-encode-doc] | 
| [![ikona rozhraní API][flat-file-decode-icon]<br/>**nestrukturovaného souboru</br> dekódování**][flat-file-decode-doc] | [ikona rozhraní API ![][flat-file-encode-icon]<br/>**nestrukturovaného souboru</br> kódování**][flat-file-encode-doc] | [ikona rozhraní API ![][integration-account-icon]<br/> **<br/>účet integrace**][integration-account-doc] | [ikona rozhraní API ![][liquid-icon]<br/>ch **kapalinových**</br>][json-liquid-transform-doc] | 
| [![ikona rozhraní API][x12-icon]<br/>**dekódování X12</br>** ][x12-decode-doc] | [![ikona rozhraní API][x12-icon]<br/>**X12</br> kódování**][x12-encode-doc] | [![ikona rozhraní API][xml-transform-icon]<br/>**transformace** **XML**</br>][xml-transform-doc] | [![ikona rozhraní API][xml-validate-icon]<br/>**ověřování XML <br/>** ][xml-validate-doc] |  
||||| 

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Podnikové konektory

Logic Apps poskytuje těmto podnikovým konektorům přístup k podnikovým systémům, jako jsou SAP a IBM MQ:

|   |   |   | 
|---|---|---| 
| [ikona rozhraní ![API][ibm-3270-icon]<br/>**IBM 3270**][ibm-3270-doc] | [ikona rozhraní ![API][ibm-mq-icon]<br/>**IBM MQ**][ibm-mq-doc] | [ikona rozhraní ![API][sap-icon]<br/>**SAP**][sap-connector-doc] |
|||| 

<a name="triggers-actions"></a>

## <a name="triggers-and-actions---more-info"></a>Aktivační události a akce – Další informace

Konektory můžou poskytovat *triggery*, *Akce*nebo obojí. *Trigger* je prvním krokem v libovolné aplikaci logiky, obvykle určující událost, která aktivuje Trigger a spouští aplikaci logiky. Konektor FTP má například Trigger, který spouští aplikaci logiky při přidání nebo úpravě souboru. Některé triggery pravidelně kontrolují zadanou událost nebo data a pak se aktivují, když zjišťují zadanou událost nebo data. Další triggery čekají, ale okamžitě se aktivují, když dojde ke konkrétní události nebo když jsou k dispozici nová data. Aktivační události jsou také předávány spolu s potřebnými daty do vaší aplikace logiky. Vaše aplikace logiky může tato data číst a používat v rámci pracovního postupu.
Například konektor pro Twitter má Trigger "" při zveřejnění nového prvku provedený ", který do pracovního postupu vaší aplikace logiky předává obsah. 

Po spuštění triggeru Azure Logic Apps vytvoří instanci aplikace logiky a začne spouštět *Akce* v pracovním postupu vaší aplikace logiky. Akce jsou kroky, které následují po triggeru a provádějí úkoly v pracovním postupu vaší aplikace logiky. Můžete například vytvořit aplikaci logiky, která získá zákaznická data z databáze SQL a zpracovávat tato data v pozdějších akcích. 

Tady jsou obecné typy aktivačních událostí, které Azure Logic Apps poskytuje:

* *Aktivační událost opakování*: Tato aktivační událost se spouští podle zadaného plánu a není úzce přidružena k určité službě nebo systému.

* *Aktivační událost cyklického dotazování*: Tato aktivační událost pravidelně provádí dotaz na konkrétní službu nebo systém na základě zadaného plánu, kontroly nových dat nebo určení, zda došlo k určité události. Pokud jsou k dispozici nová data nebo dojde k určité události, aktivační událost vytvoří a spustí novou instanci aplikace logiky, která teď může používat data předávaná jako vstup.

* *Aktivační událost push*: Tato aktivační událost čeká a naslouchá novým datům nebo k tomu, aby mohlo dojít k události. Když jsou k dispozici nová data nebo dojde k události, aktivační událost vytvoří a spustí novou instanci aplikace logiky, která teď může používat data předávaná jako vstup.

<a name="custom"></a>

## <a name="connector-configuration"></a>Konfigurace konektoru

Aktivační události a akce každého konektoru poskytují vlastní vlastnosti, které můžete konfigurovat. Mnoho konektorů také vyžaduje, abyste nejprve vytvořili *připojení* k cílové službě nebo systému a poskytovali přihlašovací údaje pro ověření nebo jiné konfigurační údaje, než můžete použít Trigger nebo akci v aplikaci logiky. Například musíte autorizovat připojení k účtu Twitteru pro přístup k datům nebo k příspěvku vaším jménem. 

Pro konektory, které používají Azure Active Directory (Azure AD) OAuth, vytvoření připojení znamená přihlášení ke službě, jako je například Office 365, Salesforce nebo GitHub, kde je váš přístupový token [zašifrovaný](../security/fundamentals/encryption-overview.md) a bezpečně uložený v úložišti tajného úložiště Azure. Jiné konektory, například FTP a SQL, vyžadují připojení, které má podrobnosti o konfiguraci, jako je adresa serveru, uživatelské jméno a heslo. Tyto podrobnosti konfigurace připojení jsou taky šifrované a bezpečně uložené. Přečtěte si další informace o [šifrování v Azure](../security/fundamentals/encryption-overview.md).

Připojení mají k cílové službě nebo systému přístup, pokud to služba nebo systém umožňuje. Pro služby, které používají připojení Azure AD OAuth, jako je například Office 365 a Dynamics, Azure Logic Apps aktualizuje přístupové tokeny po neomezenou dobu. Další služby můžou mít omezení, jak dlouho Azure Logic Apps můžou používat token bez aktualizace. Obecně některé akce mají za následek zrušení platnosti všech přístupových tokenů, jako je například změna hesla.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>Vlastní rozhraní API a konektory

Chcete-li volat rozhraní API, která spouští vlastní kód nebo nejsou k dispozici jako konektory, můžete Logic Apps platformu rozšíření [vytvořit vlastní API Apps](../logic-apps/logic-apps-create-api-app.md). Můžete také [vytvořit vlastní konektory](../logic-apps/custom-connector-overview.md) pro *jakákoli* rozhraní API založená na REST nebo protokolu SOAP, která zpřístupňují tato rozhraní API pro libovolnou aplikaci logiky ve vašem předplatném Azure.
Pokud chcete, aby byly vlastní API Apps nebo konektory veřejné pro použití v Azure, můžete [Odeslat konektory pro certifikaci Microsoftu](../logic-apps/custom-connector-submit-certification.md).

> [!NOTE]
> Logic Apps v rámci [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) mají přímý přístup k prostředkům ve službě Azure Virtual Network.
> Pokud máte vlastní konektory, které vyžadují místní bránu dat, a Vy jste tyto konektory vytvořili mimo ISE, můžete tyto konektory použít i v ISE.
>
> Vlastní konektory vytvořené v rámci ISE nefungují s místní bránou dat. Tyto konektory ale můžou přistupovat přímo k místním zdrojům dat, které jsou připojené k virtuální síti Azure hostující rozhraní ISE. Proto Logic Apps v ISE pravděpodobně nepotřebují bránu dat při komunikaci s těmito prostředky.
>
> Další informace o vytváření ISEs najdete v tématu [připojení k virtuálním sítím Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

## <a name="next-steps"></a>Další kroky

* Najít [úplný seznam konektorů](https://docs.microsoft.com/connectors)
* [Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Vytváření vlastních konektorů pro Logic Apps](https://docs.microsoft.com/connectors/custom-connectors/)
* [Vytvoření vlastních rozhraní API pro aplikace logiky](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Připojení k místním zdrojům dat z aplikací logiky pomocí místní brány dat"

<!--Built-ins doc links-->
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integrace aplikací logiky se službou Azure Functions"
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Vytvoření instance služby Azure API Management pro správu a publikování vašich rozhraní API"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integrace aplikací logiky s funkcí App Service API Apps"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Odesílání zpráv z front a témat služby Service Bus a příjem zpráv z front a odběrů služby Service Bus"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Zpracování zpráv ve skupinách nebo jako dávek"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Vyhodnotit podmínku a spustit různé akce na základě toho, jestli je podmínka pravdivá, nebo NEPRAVDA"
[delay-doc]: ./connectors-native-delay.md "Provádění zpožděných akcí"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Proveďte stejné akce u každé položky v poli."
[http-doc]: ./connectors-native-http.md "Provádění volání HTTP pomocí konektoru HTTP"
[http-request-doc]: ./connectors-native-reqres.md "Přidání akcí pro požadavky a odpovědi HTTP do aplikací logiky"
[http-response-doc]: ./connectors-native-reqres.md "Přidání akcí pro požadavky a odpovědi HTTP do aplikací logiky"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Provádění volání HTTP pomocí konektoru HTTP + Swagger"
[http-webook-doc]: ./connectors-native-webhook.md "Přidání akcí a triggerů webhooků HTTP do aplikací logiky"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integrace aplikací logiky s vnořenými pracovními postupy"
[query-doc]: ./connectors-native-query.md "Výběr a filtrování polí pomocí akce dotazu"
[recurrence-doc]:  ./connectors-native-recurrence.md "Aktivace opakujících se akcí pro aplikace logiky"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Uspořádat akce do skupin, které po akcích ve skupině dokončí spuštění, a získat jejich stav," 
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Uspořádat akce do případů, kterým jsou přiřazeny jedinečné hodnoty. Spustí pouze případ, jehož hodnota odpovídá výsledku z výrazu, objektu nebo tokenu. Pokud žádné shody neexistují, spusťte výchozí případ."
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Zastavení nebo zrušení aktivně běžícího pracovního postupu pro aplikaci logiky"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Akce opakujte, dokud není zadaná podmínka pravdivá nebo se změnil stav"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Provádění operací s daty, jako je filtrování polí nebo vytváření tabulek CSV a HTML"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Provádění operací s proměnnými, například inicializovat, nastavit, zvýšit, snížit a připojit k řetězci nebo proměnné pole"

<!--Managed API doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Správa souborů v kontejneru objektů blob pomocí konektoru služby Blob Storage"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Monitorování událostí publikovaných Event Grid, například při změně prostředků Azure nebo prostředků třetích stran"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Připojte se k Azure Event Hubs. Přijímání a posílání událostí mezi Logic Apps a Event Hubs"
[box-doc]: ./connectors-create-api-box.md "Připojte se k boxu. Nahrávání, získávání, odstraňování, vypsání souborů a další"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Připojte se k Dropboxu. Nahrávání, získávání, odstraňování, vypsání souborů a další"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Připojení k aplikaci Dynamics CRM Online, abyste mohli pracovat s daty aplikace CRM Online"
[facebook-doc]: ./connectors-create-api-facebook.md "Připojte se k Facebooku. Odeslání příspěvku na časovou osu, získání informačního kanálu stránky a dalších"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Připojení k místnímu systému souborů"
[ftp-doc]: ./connectors-create-api-ftp.md "Připojení k serverům FTP/FTPS a provádění úloh protokolu FTP, jako je odesílání, získávání a odstraňování souborů a provádění dalších akcí"
[github-doc]: ./connectors-create-api-github.md "Připojení ke GitHubu a sledování problémů"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Připojí se ke Kalendáři Google a může kalendář spravovat."
[google-drive-doc]: ./connectors-create-api-googledrive.md "Připojení k Disku Google a práce s daty"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Připojte se k tabulkám Google, abyste mohli upravovat své listy."
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Připojení k Úkolům Google a správa úkolů"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Připojení k aplikacím 3270 na sálových počítačích IBM"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Připojte se k IBM DB2 v cloudu nebo místně. Aktualizace řádku, získání tabulky a dalších"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Připojte se k Informix v cloudu nebo místně. Čtení řádku, seznam tabulek a další"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Připojení k IBM MQ v místním prostředí nebo v Azure pro odesílání a příjem zpráv"
[instagram-doc]: ./connectors-create-api-instagram.md "Připojte se k Instagramu. Trigger nebo činnost na událostech"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "Připojte se k účtu MailChimp. Správa a automatizace poštovních zásilek"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Připojení k Mandrillu pro komunikaci"
[microsoft-translator-doc]: ./connectors-create-api-microsofttranslator.md "Připojte se k Microsoft translatoru. Překládat text, detekovat jazyky a další" 
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Připojte se k účtu Office 365. Posílání a přijímání e-mailů, Správa kalendáře a kontaktů a další"
[office-365-users-doc]: ./connectors-create-api-office365-users.md 
[office-365-video-doc]: ./connectors-create-api-office365-video.md "Získání informací o videu, seznamů a kanálů videí a adres URL pro přehrávání videí Office 365"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Připojte se k osobnímu Microsoft OneDrivu. Nahrávání, odstraňování, vypsání souborů a další"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Připojte se k vašemu firemnímu Microsoft OneDrivu. Nahrávání, odstraňování, vypsání souborů a další"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Připojení k databázi Oracle a možnost přidávat, vkládat a odstraňovat řádky a další"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Připojte se k poštovní schránce Outlooku. Správa e-mailů, kalendářů, kontaktů a dalších"
[project-online-doc]: ./connectors-create-api-projectonline.md "Připojte se k Microsoft Projectu Online. Správa projektů, úloh, prostředků a dalších"
[rss-doc]: ./connectors-create-api-rss.md "Publikování a načítání položek informačních kanálů a aktivace operací při publikování nové položky v informačním kanálu RSS."
[salesforce-doc]: ./connectors-create-api-salesforce.md "Připojte se k účtu Salesforce. Správa účtů, zájemců, příležitostí a dalších"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Připojení k místnímu systému SAP"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Připojte se k SendGrid. Odesílání e-mailů a Správa seznamů příjemců"
[sftp-doc]: ./connectors-create-api-sftp.md "Připojte se k účtu SFTP. Nahrávání, získávání, odstraňování souborů a další"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Připojte se k místnímu serveru SharePoint. Správa dokumentů, položek seznamů a dalších"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Připojte se k SharePointu Online. Správa dokumentů, položek seznamů a dalších"
[slack-doc]: ./connectors-create-api-slack.md "Připojení ke Slacku a odesílání zpráv do kanálů Slacku."
[smtp-doc]: ./connectors-create-api-smtp.md "Připojení k serveru SMTP a odesílání e-mailů s přílohami"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Připojení ke SparkPostu pro komunikaci"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Připojení k Azure SQL Database nebo SQL Server. Vytvoření, aktualizace, získání a odstranění záznamů v tabulce databáze SQL."
[trello-doc]: ./connectors-create-api-trello.md "Připojte se k Trello. Spravujte své projekty a uspořádejte cokoli s kýmkoli"
[twilio-doc]: ./connectors-create-api-twilio.md "Připojte se k Twilio. Posílání a získávání zpráv, získávání dostupných čísel, Správa příchozích telefonních čísel a další"
[twitter-doc]: ./connectors-create-api-twitter.md "Připojte se k Twitteru. Získání časových os, post tweety a dalších"
[wunderlist-doc]: ./connectors-create-api-wunderlist.md "Připojte se k Wunderlistu. Spravujte své úkoly a seznamy úkolů, udržujte si synchronizaci v průběhu synchronizace a dalších"
[yammer-doc]: ./connectors-create-api-yammer.md "Připojte se ke Yammeru. Odesílání zpráv, získávání nových zpráv a další"
[youtube-doc]: ./connectors-create-api-youtube.md "Připojte se k YouTube. Správa videí a kanálů"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Přečtěte si víc o podnikové integraci AS2."
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Přečtěte si víc o dekódování pro podnikovou integraci EDIFACT."
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Přečtěte si víc o kódování pro podnikovou integraci EDIFACT."
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Přečtěte si víc o plochém souboru podnikové integrace."
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Přečtěte si víc o plochém souboru podnikové integrace."
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Vyhledání schémat, map, partnerů a dalších v účtu pro integraci"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Další informace o transformacích JSON pomocí Liquidu"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Přečtěte si víc o podnikové integraci X12."
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Přečtěte si víc o dekódování pro podnikovou integraci X12."
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Přečtěte si víc o kódování pro podnikovou integraci X12."
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Přečtěte si víc o transformacích podnikové integrace."
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Přečtěte si víc o validaci XML podnikové integrace."

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed API icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[box-icon]: ./media/apis-list/box.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dropbox-icon]: ./media/apis-list/dropbox.png
[dynamics-365-icon]: ./media/apis-list/dynamics-crm-online.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[facebook-icon]: ./media/apis-list/facebook.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mailchimp-icon]: ./media/apis-list/mailchimp.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[microsoft-translator-icon]: ./media/apis-list/microsoft-translator.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[office-365-video-icon]: ./media/apis-list/office-365-video.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[rss-icon]: ./media/apis-list/rss.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[trello-icon]: ./media/apis-list/trello.png
[twilio-icon]: ./media/apis-list/twilio.png
[twitter-icon]: ./media/apis-list/twitter.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[visual-studio-team-services-icon]: ./media/apis-list/visual-studio-team-services.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[wunderlist-icon]: ./media/apis-list/wunderlist.png
[yammer-icon]: ./media/apis-list/yammer.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png
