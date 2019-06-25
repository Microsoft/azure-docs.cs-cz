---
title: Konektory pro Azure Logic Apps
description: Automatizace pracovních postupů s konektory pro Azure Logic Apps, včetně integrovaných, spravované, v místním, účtu pro integraci a podnikové konektory
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: c02361cf69b98da61a0f551ac037e6d35ea42efc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65551867"
---
# <a name="connectors-for-azure-logic-apps"></a>Konektory pro Azure Logic Apps

Konektory poskytují rychlý přístup z Azure Logic Apps na události, data a akcí napříč jiné aplikace, služby, systémy, protokoly a platformy. Pomocí konektorů ve svých aplikacích logiky rozšířili schopnosti pro vaše cloudové a místní aplikace provádět úlohy s daty, které vytvoříte a už máte.

Zatímco Logic Apps nabízí [~ více než 200 konektorů](https://docs.microsoft.com/connectors), tento článek popisuje Oblíbené a běžně používané konektory, které jsou úspěšně používat tisíce aplikací a miliony spuštění pro zpracování dat a informací. Úplný seznam konektorů a každý konektor referenční informace, jako jsou triggery, akce a omezení, najdete v tématu Referenční stránky konektoru v rámci [přehled konektorů](https://docs.microsoft.com/connectors). Také se dozvíte více o [aktivační události a akce](#triggers-actions), [cenový model Logic Apps](../logic-apps/logic-apps-pricing.md), a [podrobnosti o cenách Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/). 

> [!NOTE]
> Integrovat službu nebo rozhraní API, které nemá konektor, můžete přímo volat službu prostřednictvím protokolu, jako je například HTTP, nebo vytvořte [vlastního konektoru](#custom).

Konektory jsou dostupné buď jako integrované aktivační události a akce, nebo jako spravované konektory:

* [**Předdefinované**](#built-ins): Tyto předdefinované aktivační události a akce jsou "nativní" pro Azure Logic Apps a pomáhají vytvářet aplikace logiky, které spouštět na vlastní plány, komunikují s další koncové body, přijímat a reagovat na požadavky a volání funkce Azure, aplikace API Azure (webové aplikace), vaše vlastní rozhraní API spravovaná a publikovány pomocí Azure API Management a vnořené logic apps, které můžou přijímat požadavky. Můžete také použít integrované akce, které vám pomohou organizovat a řídit pracovní postup aplikace logiky a také pracovat s daty.

  > [!NOTE]
  > Logic apps v rámci [prostředí integrační služby (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) přímý přístup k prostředkům ve virtuální síti Azure.
  > Při použití ISE, integrované aktivační události a akce, která zobrazí **Core** popisek spouštění v prostředí ISE stejné jako aplikace logiky. Logic apps, integrované aktivační události a integrované akce, které běží ve vaší ISE cenový plán liší od založenou na skutečné spotřebě cenového plánu.
  >
  > Další informace o vytváření ISEs najdete v tématu [připojení k virtuálním sítím Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#create-logic-apps-environment). 
  > Další informace o cenách najdete v tématu [cenový model Logic Apps](../logic-apps/logic-apps-pricing.md).

* **Spravované konektory**: Nasazen a spravován společností Microsoft, tyto konektory poskytují triggery a akce pro přístup k cloud services, s místními systémy nebo obojí, včetně Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint a dalších. Některé konektory specificky podporují scénáře komunikace business-to-business (B2B) a vyžadovat [účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) připojený k vaší aplikaci logiky. Před použitím některých konektorů, budete muset nejprve vytvořit připojení, které jsou spravovány službou Azure Logic Apps. 

  Například pokud používáte Microsoft BizTalk Server, aplikace logiky můžete připojit k a komunikovat s BizTalk serverem pomocí [BizTalk Server místní konektor](#on-premises-connectors). 
  Potom můžete rozšířit nebo provádění operací BizTalk jako ve službě logic apps s využitím [konektory účtu pro integraci](#integration-account-connectors).

  Konektory jsou klasifikovány jako Standard nebo Enterprise. 
  [Podnikové konektory](#enterprise-connectors) poskytují přístup k podnikovým systémům, jako je například SAP, IBM MQ a IBM 3270 za poplatek. Pokud chcete zjistit, zda je konektor Standard nebo Enterprise, naleznete technické podrobnosti na stránce odkaz každý konektor v části [přehled konektorů](https://docs.microsoft.com/connectors). 

  Konektory můžete také poznat podle pomocí těchto kategorií, i když některé konektory můžete napříč více kategorií. 
  Například SAP je konektor služby Enterprise a místní konektor:

  |   |   |
  |---|---|
  | [**Spravované konektory rozhraní API**](#managed-api-connectors) | Vytváření aplikací logiky, které používají služby, jako je úložiště objektů Blob v Azure, Office 365, Dynamics, Power BI, Onedrivu, Salesforce, SharePoint Online a mnoho dalších. |
  | [**Místní konektory**](#on-premises-connectors) | Po instalaci a nastavení [na místní bránu dat][gateway-doc], tyto konektory nápovědy přístup aplikace logiky místní systémy, jako je SQL Server, SharePoint Server, Oracle DB, sdílené složky a další. |
  | [**Konektory účtu pro integraci**](#integration-account-connectors) | K dispozici při vytváření a platit za účtu pro integraci, tyto konektory transformace a ověřit XML, kódovat a dekódovat ploché soubory a zpracovat business-to-business (B2B) zprávy AS2, EDIFACT a X12 protokoly. |
  |||

  > [!NOTE]
  > Logic apps v rámci [prostředí integrační služby (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) přímý přístup k prostředkům ve virtuální síti Azure. Při použití ISE, standardních a podnikových konektorů, které zobrazení **ISE** popisek spouštění v prostředí ISE stejné jako aplikace logiky. Konektory, které nechcete zobrazit popisek ISE spustit v globální službě Logic Apps.
  >
  > Pro místní systémy, které jsou připojené ke službě Azure virtual network, vaše ISE vložit do této sítě tak, aby aplikace logiky můžete přímo přístup těchto systémů s použitím buď konektor, který má **ISE** popisek, akce HTTP nebo [vlastního konektoru](#custom). Logic apps a konektorů, které běží ve vaší ISE cenovou plánování liší od založenou na skutečné spotřebě cenového plánu. 
  >
  > Další informace o vytváření ISEs najdete v tématu [připojení k virtuálním sítím Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#create-logic-apps-environment).
  > Další informace o cenách najdete v tématu [cenový model Logic Apps](../logic-apps/logic-apps-pricing.md).

  Úplný seznam konektorů a každý konektor referenční informace, jako je například žádné triggery a akce, které jsou definovány pomocí rozhraní OpenAPI (dříve Swagger) popis a žádná omezení, najdete úplný seznam v části [přehled konektorů ](/connectors/). Informace o cenách najdete v tématu [cenový model Logic Apps](../logic-apps/logic-apps-pricing.md), a [podrobnosti o cenách Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/). 

<a name="built-ins"></a>

## <a name="built-ins"></a>Předdefinované

Logic Apps poskytuje integrované triggery a akce, můžete vytvořit pracovní postupy založené na plánu, pomáhají aplikace logiky komunikovat s jinými aplikace a služby, ovládací prvek pracovního postupu pomocí logic apps, spravovat a manipulaci s daty.

|   |   |   |   | 
|---|---|---|---| 
| [![Ikona rozhraní API][schedule-icon]<br/>**plán**][recurrence-doc] | -Spuštění aplikace logiky podle zadaného plánu, od základních až po komplexní opakování s **opakování** aktivační události. <p>-Pozastavení aplikace logiky po zadanou dobu s **zpoždění** akce. <p>-Pozastavení aplikace logiky do zadaného data a času s **zpoždění do** akce. | [![Ikona rozhraní API][http-icon]<br/>**HTTP**][http-doc] | Komunikují s jakýmkoli koncovým bodem přes HTTP s aktivačními událostmi a akce pro HTTP, HTTP + Swagger a HTTP + Webhooku. | 
| [![Ikona rozhraní API][http-request-icon]<br/>**žádosti**][http-request-doc] | – Ujistěte se, aplikace logiky z jiné aplikace nebo služby, aktivační události pro události prostředku Event gridu nebo aktivační událost pro reakce na výstrahy Azure Security Center se službou **žádosti** aktivační události. <p>– Odesílat odpovědi do aplikace nebo služby s **odpovědi** akce. | [![Ikona rozhraní API][batch-icon]<br/>**služby Batch**][batch-doc] | -Zpracování zpráv v dávkách s **dávkové zprávy** aktivační události. <p>– Aplikace logiky volání, které máte existující služby batch aktivačních událostí, jejichž **odesílat zprávy do služby batch** akce. | 
| [![Ikona rozhraní API][azure-functions-icon]<br/>**Azure Functions**][azure-functions-doc] | Volání funkce Azure, na kterých běží vlastních fragmentech kódu (C# nebo Node.js) z aplikace logiky. | [![Ikona rozhraní API][azure-api-management-icon]</br>**Azure API Management**][azure-api-management-doc] | Volání, triggery a akce definované ve vlastních rozhraní API, která můžete spravovat a publikovat ve službě Azure API Management. | 
| [![Ikona rozhraní API][azure-app-services-icon]<br/>**Azure App Services**][azure-app-services-doc] | Volání aplikace API Azure nebo webových aplikací hostovaných ve službě Azure App Service. Triggery a akce definované těmito tyto aplikace se zobrazí stejně jako všechny ostatní prvotřídní aktivační události a akce po zahrnuté Swagger. | [![Ikona rozhraní API][azure-logic-apps-icon]<br/>**Azure<br/>Logic Apps**][nested-logic-app-doc] | Volání jiných funkcí logic apps, které začínají triggerem požadavku. | 
||||| 

### <a name="control-workflow"></a>Řízení pracovního postupu

Logic Apps poskytuje integrované akce pro strukturování a řídit akce v pracovním postupu vaší aplikace logiky:

|   |   |   |   | 
|---|---|---|---| 
| [![Předdefinovanou ikonu][condition-icon]<br/>**podmínku**][condition-doc] | Vyhodnocení podmínky a spouštět různé akce podle toho, jestli je podmínka true nebo false. | [![Předdefinovanou ikonu][for-each-icon]</br>**For each**][for-each-doc] | Proveďte stejné akce na každou položku v poli. | 
| [![Předdefinovanou ikonu][scope-icon]<br/>**oboru**][scope-doc] | Skupiny akcí do *obory*, která získat svůj vlastní stav po dokončení spuštění akce v oboru. | [![Předdefinovanou ikonu][switch-icon]</br>**přepínače**][switch-doc] | Skupiny akcí do *případů*, které se přiřazují jedinečné hodnoty s výjimkou výchozí případ. Spusťte pouze tento případ, jejíž hodnota odpovídá výsledkem výrazů, objektu nebo token. Pokud neexistují žádné shody, spusťte výchozí případ. | 
| [![Předdefinovanou ikonu][terminate-icon]<br/>**ukončit**][terminate-doc] | Zastavte aktivně spuštěný pracovní postup aplikace logiky. | [![Předdefinovanou ikonu][until-icon]<br/>**dokud**][until-doc] | Akce opakujte, dokud je zadaná podmínka pravdivá nebo u některých stavů, se změnila. | 
||||| 

### <a name="manage-or-manipulate-data"></a>Správě nebo pracovat s daty

Služba Logic Apps poskytuje integrované akce pro práci s výstupů dat a jejich formáty:  

|   |   | 
|---|---| 
| [![Předdefinovanou ikonu][data-operations-icon]<br/>**operace s daty**][data-operations-doc] | Provádění operací s daty: <p>- **Compose**: Vytvoření jediného výstupu z více vstupů s různými typy. <br>- **Vytvoření tabulky CSV**: Vytvořte tabulku – oddělovači (CSV) z pole s objekty JSON. <br>- **Vytvoření tabulky HTML**: Vytvoření tabulky HTML z pole s objekty JSON. <br>- **Filtrování pole**: Vytvoří pole z položek v jiném poli, které splňují vaše kritéria. <br>- **Připojte se k**: Vytvoření řetězce z všechny položky v poli a tyto položky oddělujte zadaného oddělovače. <br>- **Parsování formátu JSON**: Vytvořte uživatelsky přívětivé tokeny z vlastností a jejich hodnoty v obsahu JSON tak tyto vlastnosti lze použít v pracovním postupu. <br>- **Vyberte**: Vytvoří pole s objekty JSON po transformacích položky nebo hodnoty v jiném poli a mapování těchto položek do zadané vlastnosti. | 
| ![Předdefinovanou ikonu][date-time-icon]<br/>**Datum a čas** | Provádění operací s časovými razítky: <p>- **Přičíst k času**: Přidáte zadaný počet jednotek do časového razítka. <br>- **Převést časové pásmo**: Převeďte časové razítko ze zdrojového časového pásma na časové pásmo cíle. <br>- **Aktuální čas**: Vrátí aktuální časové razítko jako řetězec. <br>- **Získat budoucí čas**: Vrátí aktuální časové razítko plus zadané časové jednotky. <br>- **Získat minulý čas**: Vrátí aktuální časové razítko minus zadané časové jednotky. <br>- **Odečíst od času**: Odečte počet časových jednotek z časové razítko. |
| [![Předdefinovanou ikonu][variables-icon]<br/>**proměnné**][variables-doc] | Provádění operací s proměnnými: <p>- **Připojení k proměnné pole**: Vložit hodnotu jako poslední položky v poli uložených v proměnné. <br>- **Připojení k proměnné řetězce**: Vložit hodnotu jako poslední znak v řetězci uložených v proměnné. <br>- **Dekrementuje proměnnou**: Snížit proměnnou s konstantní hodnotou. <br>- **Zvýšení hodnoty proměnné**: Zvýšit konstantní hodnotu proměnné. <br>- **Inicializace proměnné**: Vytvořte proměnnou a deklarovat jeho datový typ a počáteční hodnota. <br>- **Nastavit proměnnou**: Přiřadíte jinou hodnotu existující proměnné. |
|  |  | 

<a name="managed-api-connectors"></a>

## <a name="managed-api-connectors"></a>Spravované konektory rozhraní API

Služba Logic Apps poskytuje tyto Oblíbené konektory úrovně Standard pro automatizaci úloh, procesů a pracovních postupů s těmito službami nebo systémy.

|   |   |   |   | 
|---|---|---|---| 
| [![Ikona rozhraní API][azure-service-bus-icon]<br/>**Azure Service Bus**][azure-service-bus-doc] | Spravovat asynchronních zpráv, relace a odběry témat se nejčastěji používané konektor v Logic Apps. | [![Ikona rozhraní API][sql-server-icon]<br/>**systému SQL Server**][sql-server-doc] | Připojte se k serveru SQL Server v místním prostředí nebo Azure SQL Database v cloudu, můžete ke správě záznamů, spuštění uložené procedury nebo provádět dotazy. | 
| [![Ikona rozhraní API][office-365-outlook-icon]<br/>**Office 365<br/>aplikace Outlook**][office-365-outlook-doc] | Připojte se k vaší e-mailový účet Office 365, můžete vytvořit a spravovat e-mailů, úlohy, události kalendáře a schůzky, kontakty, požadavky a další. | [![Ikona rozhraní API][azure-blob-storage-icon]<br/>**objektů Blob v Azure<br/>úložiště**][azure-blob-storage-doc] | Připojte se k účtu úložiště, můžete vytvářet a spravovat obsah objektu blob. | 
| [![Ikona rozhraní API][sftp-icon]<br/>**SFTP**][sftp-doc] | Připojení k protokolu SFTP serverů, které se dá dostat z Internetu, abyste mohli pracovat se soubory a složkami. | [![Ikona rozhraní API][sharepoint-online-icon]<br/>**SharePoint<br/>Online**][sharepoint-online-doc] | Připojte se k Sharepointu Online, můžete spravovat soubory, přílohy, složky a další. | 
| [![Ikona rozhraní API][dynamics-365-icon]<br/>**Dynamics 365<br/>CRM Online**][dynamics-365-doc] | Připojte se ke svému účtu Dynamics 365, můžete vytvářet a spravovat záznamy, položky a další. | [![Ikona rozhraní API][ftp-icon]<br/>**FTP**][ftp-doc] | Připojení k serverům FTP, který se dá dostat z Internetu, abyste mohli pracovat se soubory a složkami. | 
| [![Ikona rozhraní API][salesforce-icon]<br/>**Salesforce**][salesforce-doc] | Připojte se ke svému účtu Salesforce, můžete vytvářet a spravovat položky jako záznamy, úlohy, objektů a další. | [![Ikona rozhraní API][twitter-icon]<br/>**na Twitteru**][twitter-doc] | Připojte se k svému účtu na Twitteru, můžete spravovat tweety, sledující, časové osy a další. Uložte svoje tweety do SQL, Excel nebo SharePoint. | 
| [![Ikona rozhraní API][azure-event-hubs-icon]<br/>**Azure Event Hubs**][azure-event-hubs-doc] | Zpracovávejte a publikujte události prostřednictvím centra událostí. Třeba získat výstup vaší aplikace logiky se službou Event Hubs a potom odešlete, jehož výstupem poskytovatele analýz v reálném čase. | [![Ikona rozhraní API][azure-event-grid-icon]<br/>**událostí Azure**</br>**mřížky**][azure-event-grid-doc] | Sledování událostí publikoval(a) Event Grid, například při změně prostředků Azure nebo prostředky třetích stran. | 
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Místní konektory 

Tady jsou některé běžně používané standardní konektory, které Logic Apps poskytuje pro přístup k datům a prostředkům v místních systémech. Než vytvoříte připojení k místnímu systému, je nutné nejprve [stažení, instalace a nastavení místní brány dat][gateway-doc]. Tato brána poskytuje zabezpečený komunikační kanál, aniž by bylo potřeba nastavovat nezbytné síťové infrastruktury. 

|   |   |   |   |   | 
|---|---|---|---|---| 
| ![Ikona rozhraní API][biztalk-server-icon]<br/>**BizTalk**</br> **Server** | [![Ikona rozhraní API][file-system-icon]<br/>**souboru</br> systému**][file-system-doc] | [![API icon][ibm-db2-icon]<br/>**IBM DB2**][ibm-db2-doc] | [![Ikona rozhraní API][ibm-informix-icon]<br/>**IBM** </br> **Informix**][ibm-informix-doc] | ![Ikona rozhraní API][mysql-icon]<br/>**MySQL** | 
| [![Ikona rozhraní API][oracle-db-icon]<br/>**Oracle DB**][oracle-db-doc] | ![Ikona rozhraní API][postgre-sql-icon]<br/>**PostgreSQL** | [![Ikona rozhraní API][sharepoint-server-icon]<br/>**SharePoint</br> serveru**][sharepoint-server-doc] | [![Ikona rozhraní API][sql-server-icon]<br/>**SQL</br> serveru**][sql-server-doc] | ![Ikona rozhraní API][teradata-icon]<br/>**Teradata** | 
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Konektory účtu pro integraci

Služba Logic Apps poskytuje standardní konektory pro sestavování řešení business-to-business (B2B) s logic apps, když vytvoříte a Plaťte za to, [účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), která je k dispozici prostřednictvím Enterprise Integration Pack (EIP) v Azure. S tímto účtem můžete vytvořit a ukládání artefaktů B2B, jako je například obchodní partnery, smlouvy, map, schémata, certifikáty a tak dále. Pokud chcete použít tyto artefakty, přidružte aplikace logiky účtu pro integraci. Pokud aktuálně používáte službu BizTalk Server, tyto konektory zdát, že známých již.

|   |   |   |   | 
|---|---|---|---| 
| [![Ikona rozhraní API][as2-icon]<br/>**AS2</br> – dekódování**][as2-doc] | [![Ikona rozhraní API][as2-icon]<br/>**AS2</br> kódování**][as2-doc] | [![Ikona rozhraní API][edifact-icon]<br/>**EDIFACT</br> – dekódování**][edifact-decode-doc] | [![Ikona rozhraní API][edifact-icon]<br/>**EDIFACT</br> kódování**][edifact-encode-doc] | 
| [![Ikona rozhraní API][flat-file-decode-icon]<br/>**plochého souboru</br> – dekódování**][flat-file-decode-doc] | [![Ikona rozhraní API][flat-file-encode-icon]<br/>**plochého souboru</br> kódování**][flat-file-encode-doc] | [![Ikona rozhraní API][integration-account-icon]<br/>**integrace<br/>účtu**][integration-account-doc] | [![Ikona rozhraní API][liquid-icon]<br/>**Liquid**</br>**transformace**][json-liquid-transform-doc] | 
| [![Ikona rozhraní API][x12-icon]<br/>**X12</br> – dekódování**][x12-decode-doc] | [![Ikona rozhraní API][x12-icon]<br/>**X12</br> kódování**][x12-encode-doc] | [![Ikona rozhraní API][xml-transform-icon]<br/>**XML**</br>**transformace**][xml-transform-doc] | [![Ikona rozhraní API][xml-validate-icon]<br/>**XML <br/>ověření**][xml-validate-doc] |  
||||| 

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Podnikové konektory

Logic Apps poskytuje tyto podnikové konektory pro přístup k podnikovým systémům, jako je SAP a IBM MQ:

|   |   |   | 
|---|---|---| 
| [![API icon][ibm-3270-icon]<br/>**IBM 3270**][ibm-3270-doc] | [![API icon][ibm-mq-icon]<br/>**IBM MQ**][ibm-mq-doc] | [![Ikona rozhraní API][sap-icon]<br/>**SAP**][sap-connector-doc] |
|||| 

<a name="triggers-actions"></a>

## <a name="triggers-and-actions---more-info"></a>Triggery a akce – Další informace

Konektory můžete zadat *triggery*, *akce*, nebo obojí. A *aktivační událost* je prvním krokem v jakékoli aplikaci logiky, obvykle určující událost, která aktivuje triggeru a spuštění aplikace logiky. Konektor FTP má například aktivační událost, která spustí vaši aplikaci logiky "při přidání nebo změně souboru". Některé triggery pravidelně kontrolovat zadané události nebo data a poté aktivují, když zjistí zadané události nebo data. Jiné triggery čekat, ale spustí okamžitě, když se stane konkrétní události nebo když nová data k dispozici. Aktivační události také máte předat všechna požadovaná data do aplikace logiky. Aplikace logiky může číst a používat tato data v rámci pracovního postupu.
Konektor Twitter má například aktivační událost, "Při nový tweet", které předává tweetu uživatele obsahu do pracovního postupu aplikace logiky. 

Po aktivační událost se aktivuje, Azure Logic Apps vytvoří instanci aplikace logiky a spustí *akce* v pracovním postupu vaší aplikace logiky. Akce jsou kroky, které podle aktivační události a provádět úlohy v pracovním postupu vaší aplikace logiky. Můžete například vytvořit aplikaci logiky, která vrací data zákazníků z databáze serveru SQL a zpracování dat v pozdějších akcích. 

Tady jsou obecné druhy aktivačních událostí, které poskytuje Azure Logic Apps:

* *Trigger opakování*: Tento trigger spustí podle zadaného plánu a není úzce spojeny s konkrétní služby nebo systému.

* *Cyklického dotazování aktivační událost*: Tato aktivační událost pravidelně dotazuje konkrétní služba nebo systém podle zadaného plánu kontroly nových dat nebo zda došlo k určité události. Pokud jsou k dispozici nová data nebo konkrétní události došlo, trigger se vytvoří a spustí novou instanci aplikace logiky, která teď můžete použít data, která je předána jako vstup.

* *Push aktivační událost*: Tato aktivační událost vyčká a naslouchá pro nová data nebo pro určité události. Pokud jsou k dispozici nová data nebo pokud k události dojde, trigger se vytvoří a spustí novou instanci aplikace logiky, která teď můžete použít data, která je předána jako vstup.

<a name="custom"></a>

## <a name="connector-configuration"></a>Konfigurace konektoru

Triggery a akce každý konektor poskytnout své vlastní vlastnosti můžete nakonfigurovat. Řada konektory také vyžadují, abyste nejdřív vytvořili *připojení* Cílová služba nebo systém a zadejte přihlašovací údaje pro ověření nebo další podrobnosti o konfiguraci triggeru nebo akce můžete používat ve vaší aplikaci logiky. Například musíte povolit připojení k účtu sítě Twitter pro přístup k datům nebo zadejte vaším jménem. 

Pro konektory, které používají OAuth vytvoření připojení znamená, že přihlášení k službě, jako je Office 365, Salesforce nebo Githubu, kde je váš přístupový token zašifrované a bezpečně uložené v Azure úložiště tajných kódů. Jiné konektory, jako je například FTP a SQL, vyžadují připojení obsahující podrobnosti o konfiguraci, třeba adresu serveru, uživatelské jméno a heslo. Podrobnosti o konfiguraci těchto připojení jsou také zašifrované a bezpečně uložené. 

Připojení můžete za předpokladu, že služby nebo systému umožňuje přístup Cílová služba nebo systém pro. Pro služby, které používají OAuth Azure Active Directory (AD) připojení, jako je Office 365 a Dynamics Azure Logic Apps obnoví přístupové tokeny po neomezenou dobu. Další služby může mít omezení na jak dlouho Azure Logic Apps můžete použít token bez obnovení. Obecně platí některé akce zneplatnit všechny přístupové tokeny, jako je například změna hesla.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>Vlastní rozhraní API a konektory

Pro volání rozhraní API, která spouští vlastní kód nebo nejsou k dispozici jako konektory, můžete rozšířit platformu Logic Apps [vytvoření vlastní funkce API Apps](../logic-apps/logic-apps-create-api-app.md). Můžete také [vytváření vlastních konektorů](../logic-apps/custom-connector-overview.md) pro *jakékoli* REST nebo založený na protokolu SOAP rozhraní API, která zpřístupnění těchto rozhraní API pro všechny aplikace logiky ve vašem předplatném Azure.
Chcete-li zveřejnit vlastní aplikace API nebo konektory pro každého, kdo pro použití v Azure, můžete [odeslání konektoru Microsoftu k certifikaci](../logic-apps/custom-connector-submit-certification.md).

> [!NOTE]
> Logic apps v rámci [prostředí integrační služby (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) přímý přístup k prostředkům ve virtuální síti Azure.
> Pokud máte vlastní konektory, které vyžadují místní brány dat a vytvoříte tyto konektory mimo ISE, aplikace logiky do ISE můžete také použít tyto konektory.
>
> Vlastních konektorů vytvořených v rámci ISE nefungují s místní bránou dat Ale tyto konektory můžete přímý přístup k místním zdrojům dat, které jsou připojené ke službě Azure virtual network hostování ISE. Tak aplikace logiky do ISE pravděpodobně nepotřebujete bránu dat při komunikaci s těmito prostředky.
>
> Další informace o vytváření ISEs najdete v tématu [připojení k virtuálním sítím Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#create-logic-apps-environment).

## <a name="next-steps"></a>Další postup

* Najít [úplný seznam konektorů.](https://docs.microsoft.com/connectors)
* [Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Vytváření vlastních konektorů pro logic apps](https://docs.microsoft.com/connectors/custom-connectors/)
* [Vytvoření vlastních rozhraní API pro aplikace logiky](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Připojení k místním zdrojům dat z aplikací logiky pomocí místní brány dat"

<!--Built-ins doc links-->
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integrace aplikací logiky se službou Azure Functions"
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Vytvoření instance Azure API Management pro správu a publikování rozhraní API"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integrace aplikací logiky s funkcí App Service API Apps"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Odesílání zpráv z front a témat služby Service Bus a příjem zpráv z front a odběrů služby Service Bus"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Zpracování zpráv ve skupinách, nebo jako dávek"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Vyhodnocení podmínky a spouštět různé akce podle toho, jestli je podmínka true nebo false"
[delay-doc]: ./connectors-native-delay.md "Provádění zpožděných akcí"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Provádět stejné akce na každou položku v poli"
[http-doc]: ./connectors-native-http.md "Provádění volání HTTP pomocí konektoru HTTP"
[http-request-doc]: ./connectors-native-reqres.md "Přidání akcí pro požadavky a odpovědi HTTP do aplikací logiky"
[http-response-doc]: ./connectors-native-reqres.md "Přidání akcí pro požadavky a odpovědi HTTP do aplikací logiky"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Provádění volání HTTP pomocí konektoru HTTP + Swagger"
[http-webook-doc]: ./connectors-native-webhook.md "Přidat triggery a akce webhooku HTTP do aplikací logiky"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integrace aplikací logiky s vnořenými pracovními postupy"
[query-doc]: ./connectors-native-query.md "Výběr a filtrování polí pomocí akce dotazu"
[recurrence-doc]:  ./connectors-native-recurrence.md "Aktivace opakujících se akcí pro aplikace logiky"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Uspořádání akcí do skupin, které získat vlastní stav po dokončení spuštění akcí ve skupině" 
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Uspořádání akcí do případů, které jsou přiřazeny jedinečné hodnoty. Spusťte pouze případ, jehož hodnota se shoduje s výsledkem výrazů, objektu nebo token. Pokud neexistují žádné shody, spusťte na výchozí případ."
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Zastavit nebo zrušit aktivně spuštěný pracovní postup aplikace logiky"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Opakujte akce, dokud je zadaná podmínka pravdivá nebo u některých stavů, se změnila."
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Provádět operace s daty jako je například filtrování pole nebo vytvoření tabulky CSV a HTML"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Provádění operací s proměnnými, jako je inicializace, sady, přírůstek, snížení a připojení k proměnné řetězce nebo pole"

<!--Managed API doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Správa souborů v kontejneru objektů blob pomocí konektoru služby Blob Storage"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md " Sledování událostí publikoval(a) Event Grid, například při změně prostředků Azure nebo prostředky třetích stran"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Připojení k Azure Event Hubs. Příjem a odesílání událostí mezi aplikacemi logiky a Event Hubs"
[box-doc]: ./connectors-create-api-box.md "Připojení ke službě Box. Nahrávání, získávání, odstraňování, vypisování souborů a provádění dalších akcí"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Připojení k Dropboxu. Nahrávání, získávání, odstraňování, vypisování souborů a provádění dalších akcí"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Připojení k aplikaci Dynamics CRM Online, abyste mohli pracovat s daty aplikace CRM Online"
[facebook-doc]: ./connectors-create-api-facebook.md "Připojení k Facebooku. Přidávání příspěvků na Timeline, získání kanálu stránky a provádění dalších akcí"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Připojení k místnímu systému souborů"
[ftp-doc]: ./connectors-create-api-ftp.md "Připojení k serverům FTP/FTPS a provádění úloh protokolu FTP, jako je odesílání, získávání a odstraňování souborů a provádění dalších akcí"
[github-doc]: ./connectors-create-api-github.md "Připojení ke GitHubu a sledování problémů"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Připojí se ke Kalendáři Google a může kalendář spravovat."
[google-drive-doc]: ./connectors-create-api-googledrive.md "Připojení k Disku Google a práce s daty"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Připojte se k tabulkám Google, upravování tabulek"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Připojení k Úkolům Google a správa úkolů"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Připojte se k aplikacím 3270 na sálové počítače IBM"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Připojení k IBM DB2 v cloudu nebo místním prostředí. Aktualizace řádku, získání tabulky a provádění dalších akcí"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Připojení k Informix v cloudu nebo místním prostředí. Čtení řádku, výpis tabulek a provádění dalších akcí"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Připojení k IBM MQ místně nebo v Azure a odesílání a příjem zpráv"
[instagram-doc]: ./connectors-create-api-instagram.md "Připojení k Instagramu. Aktivování nebo reakce na události"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "Připojení k účtu MailChimp. Správa a automatizace e-mailů"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Připojení k Mandrillu pro komunikaci"
[microsoft-translator-doc]: ./connectors-create-api-microsofttranslator.md "Připojení ke službě Microsoft Translator. Překlad textu, zjišťování jazyků a provádění dalších akcí" 
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Připojení k účtu Office 365. Odesílání a příjem e-mailů, správa kalendáře a kontaktů a provádění dalších akcí"
[office-365-users-doc]: ./connectors-create-api-office365-users.md 
[office-365-video-doc]: ./connectors-create-api-office365-video.md "Získání informací o videu, seznamů a kanálů videí a adres URL pro přehrávání videí Office 365"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Připojení k osobnímu Microsoft OneDrivu. Nahrávání, odstraňování, vypisování souborů a provádění dalších akcí"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Připojení k firemnímu Microsoft OneDrivu. Nahrávání, odstraňování, vypisování souborů a provádění dalších akcí"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Připojení k databázi Oracle a možnost přidávat, vkládat a odstraňovat řádky a další"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Připojení k poštovní schránce aplikace Outlook. Správa e-mailů, kalendářů, kontaktů a provádění dalších akcí"
[project-online-doc]: ./connectors-create-api-projectonline.md "Připojení k aplikaci Microsoft Project Online. Správa projektů, úloh, prostředků a provádění dalších akcí"
[rss-doc]: ./connectors-create-api-rss.md "Publikování a načítání položek informačních kanálů a aktivace operací při publikování nové položky v informačním kanálu RSS."
[salesforce-doc]: ./connectors-create-api-salesforce.md "Připojení k účtu Salesforce. Správa účtů, zájemců, příležitostí a provádění dalších akcí"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Připojení k místnímu systému SAP"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Připojení k SendGridu. Odeslání e-mailu a spravovat seznamy příjemců"
[sftp-doc]: ./connectors-create-api-sftp.md "Připojení k účtu SFTP. Nahrávání, získávání, odstraňování souborů a provádění dalších akcí"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Přípojení k místnímu serveru SharePoint. Správa dokumentů, vypisování položek a provádění dalších akcí"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Připojení k SharePointu Online. Správa dokumentů, vypisování položek a provádění dalších akcí"
[slack-doc]: ./connectors-create-api-slack.md "Připojení ke Slacku a odesílání zpráv do kanálů Slacku."
[smtp-doc]: ./connectors-create-api-smtp.md "Připojte se k serveru SMTP a odesílání e-mailů s přílohami"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Připojení ke SparkPostu pro komunikaci"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Připojte se k Azure SQL Database nebo SQL Server. Vytváření, aktualizace, získávání a odstraňování položek v tabulce databáze SQL."
[trello-doc]: ./connectors-create-api-trello.md "Připojení k Trello. Správa projektů a organizace čehokoli s kýmkoli"
[twilio-doc]: ./connectors-create-api-twilio.md "Připojení k Twiliu. Odesílání a získávání zpráv, získávání dostupných čísel, správa příchozích hovorů z telefonních čísel a provádění dalších akcí"
[twitter-doc]: ./connectors-create-api-twitter.md "Připojení k Twitteru. Získávání časových os, odesílání tweetů a provádění dalších akcí"
[wunderlist-doc]: ./connectors-create-api-wunderlist.md "Připojení k Wunderlistu. Správa úkolů a seznamů úkolů, udržování synchronizace všeho, co potřebujete k životu, a provádění dalších akcí"
[yammer-doc]: ./connectors-create-api-yammer.md "Připojení k Yammeru. Odesílání zpráv, získávání nových zpráv a provádění dalších akcí"
[youtube-doc]: ./connectors-create-api-youtube.md "Připojení k Youtube. Správa videí a kanálů"

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
