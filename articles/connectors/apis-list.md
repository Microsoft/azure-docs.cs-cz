---
title: Konektory pro Azure Logic Apps
description: Automatizace pracovních postupů pomocí konektorů pro azure logic apps, jako jsou integrované, spravované, místní, integrační účet, ISE a podnikové konektory
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/05/2020
ms.openlocfilehash: 3010f3c99a5b214c2503f890321cbb73427e3c20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247329"
---
# <a name="connectors-for-azure-logic-apps"></a>Konektory pro Azure Logic Apps

Konektory poskytují rychlý přístup z Azure Logic Apps k událostem, datům a akcím napříč dalšími aplikacemi, službami, systémy, protokoly a platformami. Použitím konektorů v aplikacích logiky můžete své cloudové i místní aplikace rozšířit o možnosti provádět úlohy s daty, která již máte nebo teprve vytvoříte.

Zatímco Logic Apps nabízí [stovky konektorů](https://docs.microsoft.com/connectors), tento článek popisuje *oblíbené a běžně používané* konektory, které jsou úspěšně používány tisíci aplikacemi a miliony spuštění pro zpracování dat a informací. Chcete-li najít úplný seznam konektorů a odkazové informace jednotlivých konektorů, jako jsou aktivační události, akce a omezení, zkontrolujte referenční stránky konektoru v části [Přehled konektorů](https://docs.microsoft.com/connectors). Další informace o [aktivačních událostech a akcích](#triggers-actions), [cenovém modelu Logic Apps](../logic-apps/logic-apps-pricing.md)a [podrobnostech o cenách aplikací Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

> [!TIP]
> Chcete-li integrovat se službou nebo rozhraním API, které nemá konektor, můžete buď přímo volat službu přes protokol, jako je například HTTP, nebo vytvořit [vlastní konektor](#custom).

## <a name="connector-types"></a>Typy konektorů

Konektory jsou k dispozici jako vestavěné aktivační události a akce nebo jako spravované konektory.

<a name="built-in"></a>

* [**Integrované:**](#built-ins)Předdefinované aktivační události a akce jsou "nativní" pro Azure Logic Apps a pomáhají vám provádět tyto úkoly pro vaše aplikace logiky:

  * Spouštějte podle vlastních a pokročilých plánů.

  * Uspořádejte a řiďte pracovní postup aplikace logiky, například smyčky a podmínky a také pro práci s proměnnými a datovými operacemi.

  * Komunikujte s ostatními koncovými body.

  * Přijímat požadavky a odpovídat na ně.

  * Volejte funkce Azure, aplikace Azure API (Webové aplikace), vlastní rozhraní API spravovaná a publikovaná pomocí Azure API Management a vnořené aplikace logiky, které můžou přijímat požadavky.

<a name="managed-connectors"></a>

* [**Spravované konektory**](#managed-api-connectors): Tyto konektory, které jsou nasazeny a spravovány společností Microsoft, poskytují aktivační události a akce pro přístup ke cloudovým službám, místním systémům nebo obojímu, včetně Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePointa a dalších. Některé konektory konkrétně podporují scénáře komunikace mezi podniky (B2B) a vyžadují [účet integrace,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) který je propojený s vaší aplikací logiky. Před použitím určitých konektorů budete muset nejprve vytvořit připojení, která jsou spravovaná pomocí Azure Logic Apps.

  Pokud například používáte Microsoft BizTalk Server, aplikace logiky se můžou připojit k BizTalk serveru a komunikovat s ním pomocí [místního konektoru BizTalk Serveru](#on-premises-connectors). Potom můžete rozšířit nebo provádět BizTalk podobné operace ve vašich aplikacích logiky pomocí [konektorů účtu integrace](#integration-account-connectors).

  Konektory jsou klasifikovány jako standardní nebo enterprise. [Podnikové konektory](#enterprise-connectors) poskytují za příplatek přístup k podnikovým systémům, jako jsou SAP, IBM MQ a IBM 3270. Chcete-li zjistit, zda je spojnice standardní nebo podnik, naleznete technické podrobnosti v jednotlivých spojnicích referenční stránky v části [Konektory přehled](https://docs.microsoft.com/connectors).

  Konektory můžete také identifikovat pomocí těchto kategorií, i když některé spojnice mohou protínat více kategorií. Sap je například podnikový konektor a místní konektor:

  |   |   |
  |---|---|
  | [**Spravované konektory**](#managed-api-connectors) | Vytvářejte aplikace logiky, které používají služby, jako je Azure Blob Storage, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online a mnoho dalších. |
  | [**Místní konektory**](#on-premises-connectors) | Po instalaci a nastavení [místní brány dat][gateway-doc]tyto konektory pomáhají vašim aplikacím logiky přistupovat k místním systémům, jako je SQL Server, SharePoint Server, Oracle DB, sdílené složky a další. |
  | [**Konektory účtu pro integraci**](#integration-account-connectors) | K dispozici při vytváření a placení za účet integrace, tyto konektory transformovat a ověřit XML, kódování a dekódování plochých souborů a zpracování business-to-business (B2B) zprávy s AS2, EDIFACT a X12 protokoly. |
  |||

<a name="integration-service-environment"></a>

### <a name="connect-from-an-integration-service-environment"></a>Připojení z prostředí integrační služby

Pro aplikace logiky, které potřebují přímý přístup k prostředkům ve virtuální síti Azure, můžete vytvořit prostředí izolované [integrační služby (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) kde můžete vytvářet, nasazovat a spouštět aplikace logiky na vyhrazených prostředcích. V Návrháři aplikace logiky při procházení konektory, které chcete použít pro aplikace logiky v ISE, **štítek CORE** se zobrazí na integrované aktivační události a akce, zatímco popisek **ISE** se zobrazí na některé konektory:

* **CORE**: Předdefinované aktivační události a akce s tímto popiskem běží ve stejné službě ISE jako aplikace logiky, například:

  ![Příklad konektoru ISE](./media/apis-list/example-core-connector.png)

* **ISE**: Spravované konektory s tímto popiskem běží ve stejné službě ISE jako aplikace logiky, například:

  ![Příklad konektoru ISE](./media/apis-list/example-ise-connector.png)

  Pokud máte místní systém, který je připojený k virtuální síti Azure, ise umožňuje vaše aplikace logiky přímý přístup k tomuto systému bez [místní brány dat](../logic-apps/logic-apps-gateway-connection.md). Místo toho můžete použít konektor **ISE** tohoto systému, pokud je k dispozici, akci HTTP nebo [vlastní konektor](#custom). Pro místní systémy, které nemají konektory **ISE,** použijte místní bránu dat. Informace o dostupných konektorech ISE naleznete [v tématu ISE connectors](#ise-connectors).

* Všechny ostatní konektory bez štítku **CORE** nebo **ISE,** které můžete nadále používat, běží v globální službě Logic Apps s více tenanty, například:

  ![Příklad víceklientského konektoru](./media/apis-list/example-multi-tenant-connector.png)

Aplikace logiky, které běží v ISE a jejich konektory, bez ohledu na to, kde tyto konektory spustit, postupujte podle pevného cenového plánu versus cenový plán založený na spotřebě. Další informace naleznete na těchto stránkách:

* [Cenový model Logic Apps](../logic-apps/logic-apps-pricing.md)
* [Podrobnosti o cenách aplikací Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Připojení k virtuálním sítím Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="built-ins"></a>

## <a name="built-in"></a>Integrované

Logic Apps poskytuje integrované aktivační události a akce, takže můžete vytvářet pracovní postupy založené na plánu, pomáhat aplikacím logiky komunikovat s jinými aplikacemi a službami, řídit pracovní postup prostřednictvím aplikací logiky a spravovat nebo manipulovat s daty.

|   |   |   |   |
|---|---|---|---|
| [![][schedule-icon]<br>**Ikona** rozhraní API Plán][schedule-doc] | - Spusťte aplikaci logiky na zadané opakování, od základních až po pokročilé plány s aktivační událostí [ **opakování** ][schedule-recurrence-doc]. <p>- Spusťte aplikaci logiky, která potřebuje zpracovávat data v souvislých blocích s aktivační událostí [ **posuvné okno** ][schedule-sliding-window-doc]. <p>- Pozastavte aplikaci logiky na určitou dobu pomocí akce [ **Zpoždění** ][schedule-delay-doc]. <p>- Pozastavte aplikaci logiky až do zadaného data a času s [ **Delay until** action][schedule-delay-until-doc]. | [![][batch-icon]<br>**Ikona** rozhraní API Batch][batch-doc] | - Zpracování zpráv v dávkách s aktivační událostí **Dávkové zprávy.** <p>- Volání aplikace logiky, které mají existující dávkové aktivační události s **odeslat zprávy do dávkové** akce. |
| [![Ikona][http-icon]<br>rozhraní API**HTTP**][http-doc] | Volání koncových bodů HTTP nebo HTTPS s aktivačními událostmi a akcemi pro protokol HTTP. Mezi další integrované aktivační události a akce protokolu HTTP patří [HTTP + Swagger][http-swagger-doc] a [HTTP + Webhook][http-webhook-doc]. | [![Žádost][http-request-icon]<br>ikony rozhraní**API**][http-request-doc] | – Pomocí aktivační události **Request** Trigger se aktivuje aplikace logiky z jiných aplikací nebo služeb, aktivuje se na událostech prostředků eventgridu nebo aktivuje odpovědi na výstrahy Centra zabezpečení Azure. <p>- Odeslat odpovědi do aplikace nebo služby s akcí **Odpověď.** |
| [![Ikona][azure-api-management-icon]<br>rozhraní API**Azure API <br>Management**][azure-api-management-doc] | Aktivace volání a akce definované vlastními rozhraními API, které spravujete a publikujete pomocí azure api managementu. | [![Ikona][azure-app-services-icon]<br>rozhraní API**Služby aplikace <br>Azure**][azure-app-services-doc] | Volejte aplikace Azure API nebo webové aplikace hostované ve službě Azure App Service. Aktivační události a akce definované těmito aplikacemi se zobrazují jako všechny ostatní aktivační události a akce první třídy, když je zahrnuto Swagger.|
| [![Ikona][azure-logic-apps-icon]<br>rozhraní API**Azure Logic <br>Apps**][nested-logic-app-doc] | Volání jiných aplikací logiky, které začínají **request** aktivační událost. |
|||||

### <a name="run-code-from-logic-apps"></a>Spuštění kódu z aplikací logiky

Logic Apps poskytuje předdefinované akce pro spuštění vlastního kódu v pracovním postupu aplikace logiky:

|   |   |   |   |
|---|---|---|---|
| [![Ikona][azure-functions-icon]<br>rozhraní API**Azure Functions**][azure-functions-doc] | Volání funkcí Azure, které spouštějí fragmenty vlastního kódu (C# nebo Node.js) z vašich aplikací logiky. | [![][inline-code-icon]<br>**Vysazovací kód** ikony rozhraní API][azure-functions-doc] | Přidejte a spusťte fragmenty kódu JavaScriptu z aplikací logiky. |
|||||

### <a name="control-workflow"></a>Pracovní postup ovládacího prvku

Logic Apps poskytuje integrované akce pro strukturování a řízení akcí v pracovním postupu aplikace logiky:

|   |   |   |   |
|---|---|---|---|
| [![Vestavěná][condition-icon]<br>**podmínka** ikony][condition-doc] | Vyhodnoťte podmínku a spusťte různé akce na základě toho, zda je podmínka pravdivá nebo nepravdivá. | [![Vestavěná][for-each-icon]<br>ikona**pro každého**][for-each-doc] | Proveďte stejné akce u každé položky v poli. |
| [![Vestavěný][scope-icon]<br>**rozsah** ikon][scope-doc] | Seskupte akce do *oborů*, které získají svůj vlastní stav po dokončení akce v oboru. | [![Vestavěný][switch-icon]<br>**přepínač** ikon][switch-doc] | Seskupte akce do *případů*, kterým jsou přiřazeny jedinečné hodnoty s výjimkou výchozího případu. Spusťte pouze případ, jehož přiřazená hodnota odpovídá výsledku z výrazu, objektu nebo tokenu. Pokud neexistují žádné shody, spusťte výchozí případ. |
| [![Ukončení vestavěné][terminate-icon]<br>**ikony**][terminate-doc] | Zastavte aktivně spuštěný pracovní postup aplikace logiky. | [![Vestavěná][until-icon]<br>ikona**do**][until-doc] | Opakujte akce, dokud není zadaná podmínka pravdivá nebo dokud se nezmění nějaký stav. |
|||||

### <a name="manage-or-manipulate-data"></a>Správa dat nebo manipulace s nimi

Logic Apps poskytuje integrované akce pro práci s datovými výstupy a jejich formáty:

|   |   |
|---|---|
| [![Předdefinované][data-operations-icon]<br>**operace s daty ikon**][data-operations-doc] | Provádění operací s daty: <p>- **Skládání**: Vytvořte jeden výstup z více vstupů s různými typy. <br>- **Vytvořit tabulku CSV**: Vytvořte tabulku csv (s čárkou) z pole s objekty JSON. <br>- **Vytvořit tabulku HTML**: Vytvoření tabulky HTML z pole s objekty JSON. <br>- **Pole filtru**: Vytvořte pole z položek v jiném poli, které splňují vaše kritéria. <br>- **Spojit**: Vytvořte řetězec ze všech položek v poli a oddělte tyto položky se zadaným oddělovačem. <br>- **Analyzovat JSON**: Vytvořte uživatelsky přívětivé tokeny z vlastností a jejich hodnot v obsahu JSON, abyste tyto vlastnosti mohli použít ve svém pracovním postupu. <br>- **Vyberte**: Vytvořte pole s objekty JSON transformací položek nebo hodnot v jiném poli a mapováním těchto položek na zadané vlastnosti. |
| ![Vestavěná ikona][date-time-icon]<br>**Čas data** | Provádět operace s časovými razítky: <p>- **Přidat do času**: Přidejte zadaný počet jednotek do časového razítka. <br>- **Převést časové pásmo**: Převést časové razítko ze zdrojového časového pásma na cílové časové pásmo. <br>- **Aktuální čas**: Vrátí aktuální časové razítko jako řetězec. <br>- **Získat budoucí čas**: Vrátí aktuální časové razítko plus zadané časové jednotky. <br>- **Získat minulý čas**: Vrátí aktuální časové razítko mínus zadané časové jednotky. <br>- **Odečíst od času**: Odečíst počet časových jednotek z časového razítka. |
| [![Vestavěné][variables-icon]<br>**proměnné ikon**][variables-doc] | Provádět operace s proměnnými: <p>- **Připojit k proměnné pole**: Vložte hodnotu jako poslední položku v poli uloženém proměnnou. <br>- **Připojit k proměnné řetězce**: Vložte hodnotu jako poslední znak v řetězci uloženém proměnnou. <br>- **Proměnná snížení**: Proměnná sníží konstantní hodnotu. <br>- **Proměnná přírůstek**: Proměnná se zvýší o konstantní hodnotu. <br>- **Inicializovat proměnnou**: Vytvořte proměnnou a deklarujte její datový typ a počáteční hodnotu. <br>- **Nastavit proměnnou**: Přiřazení jiné hodnoty existující proměnné. |
|  |  |

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>Spravované konektory

Logic Apps poskytuje tyto oblíbené standardní konektory pro automatizaci úkolů, procesů a pracovních postupů s těmito službami nebo systémy:

|   |   |   |   |
|---|---|---|---|
| [![Ikona][azure-service-bus-icon]<br>rozhraní API**Azure Service Bus**][azure-service-bus-doc] | Spravujte asynchronní zprávy, relace a odběry témat pomocí nejčastěji používaného konektoru v Logic Apps. | [![Ikona][sql-server-icon]<br>rozhraní API**SQL Server**][sql-server-doc] | Připojte se k sql serveru místně nebo k azure sql database v cloudu, abyste mohli spravovat záznamy, spouštět uložené procedury nebo provádět dotazy. |
| [![Ikona][azure-blob-storage-icon]<br>rozhraní API**Azure Blob<br>Storage**][azure-blob-storage-doc] | Připojte se ke svému účtu úložiště, abyste mohli vytvářet a spravovat obsah objektů blob. | [![Ikona][office-365-outlook-icon]<br>rozhraní API**Office 365<br>Outlook**][office-365-outlook-doc] | Připojte se ke svému e-mailovému účtu Office 365, abyste mohli vytvářet a spravovat e-maily, úkoly, události kalendáře a schůzky, kontakty, žádosti a další. |
| [![Ikona][sftp-ssh-icon]<br>rozhraní API**SFTP-SSH**][sftp-ssh-doc] | Připojte se k serverům SFTP, ke kterým máte přístup z Internetu pomocí protokolu SSH, abyste mohli pracovat se soubory a složkami. | [![Ikona][sharepoint-online-icon]<br>rozhraní API**SharePoint<br>online**][sharepoint-online-doc] | Připojte se k SharePointu Online, abyste mohli spravovat soubory, přílohy, složky a další. | 
| [![Ikona][dynamics-365-icon]<br>rozhraní API**Dynamics 365<br> **][dynamics-365-doc] | Připojte se ke svému účtu Dynamics 365, abyste mohli vytvářet a spravovat záznamy, položky a další. | [![Ikona][azure-queues-icon]<br>rozhraní API** <br>Azure Queues**][azure-queues-doc] | Připojení k účtu Azure Storage, abyste mohli vytvářet a spravovat fronty a zprávy |
| [![Ikona][ftp-icon]<br>rozhraní API**FTP**][ftp-doc] | Připojte se k serverům FTP, ke kterým máte přístup z Internetu, abyste mohli pracovat se soubory a složkami. | [![Ikona][file-system-icon]<br>rozhraní API**Systém souborů <br>**][file-system-doc] | Připojte se k místní sdílené složce, abyste mohli vytvářet a spravovat soubory. |
| [![Ikona][azure-event-hubs-icon]<br>rozhraní API**Azure Event Hubs**][azure-event-hubs-doc] | Využijte a publikujte události prostřednictvím centra událostí. Například získat výstup z aplikace logiky s Event Hubs a pak odeslat tento výstup v reálném čase poskytovatele analýzy. | [![Ikona][azure-event-grid-icon]<br>rozhraní API**Azure Event**<br>**Grid**][azure-event-grid-doc] | Sledujte události publikované sítí událostí, například když se změní prostředky Azure nebo prostředky třetích stran. |
| [![Ikona][salesforce-icon]<br>rozhraní API**Salesforce**][salesforce-doc] | Připojte se ke svému účtu Salesforce, abyste mohli vytvářet a spravovat položky, jako jsou záznamy, úlohy, objekty a další. | [![Ikona][twitter-icon]<br>API**Twitter**][twitter-doc] | Připojte se ke svému účtu na Twitteru, abyste mohli spravovat tweety, sledující, vaši časovou osu a další. Uložte si tweety do SQL, Excelu nebo SharePointu. |
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Místní konektory

Tady jsou některé běžně používané standardní konektory, které logic Apps poskytuje pro přístup k datům a prostředkům v místních systémech. Před vytvořením připojení k místnímu systému je třeba [nejprve stáhnout, nainstalovat a nastavit místní bránu dat][gateway-doc]. Tato brána poskytuje zabezpečený komunikační kanál bez nutnosti nastavovat potřebnou síťovou infrastrukturu.

|   |   |   |   |   |
|---|---|---|---|---|
| [![][biztalk-server-icon]<br>**BizTalk** <br> **server** ikony rozhraní API][biztalk-server-doc] | [![Ikona][file-system-icon]<br>rozhraní API**Systém souborů <br>**][file-system-doc] | [![Ikona][ibm-db2-icon]<br>rozhraní API**IBM DB2**][ibm-db2-doc] | [![Ikona][ibm-informix-icon]<br>rozhraní**API IBM** <br> **Informix**][ibm-informix-doc] | [![Ikona][mysql-icon]<br>rozhraní API**MySQL**][mysql-doc] |
| [![Ikona][oracle-db-icon]<br>rozhraní**API Oracle DB**][oracle-db-doc] | [![Ikona][postgre-sql-icon]<br>ROZHRANÍ API**PostgreSQL**][postgre-sql-doc] | [![Ikona][sharepoint-server-icon]<br>rozhraní API**SharePoint <br>Server**][sharepoint-server-doc] | [![Ikona][sql-server-icon]<br>rozhraní API**SQL <br>Server**][sql-server-doc] | [![Ikona][teradata-icon]<br>rozhraní API**Teradata**][teradata-doc] |
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Konektory účtu pro integraci

Logic Apps poskytuje standardní konektory pro vytváření řešení business-to-business (B2B) s aplikacemi logiky při vytváření a placení za [účet integrace](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), který je k dispozici prostřednictvím enterprise integration pack (EIP) v Azure. Pomocí tohoto účtu můžete vytvářet a ukládat artefakty B2B, jako jsou obchodní partneři, smlouvy, mapy, schémata, certifikáty a tak dále. Chcete-li použít tyto artefakty, přidružte aplikace logiky k účtu integrace. Pokud v současné době používáte BizTalk Server, tyto konektory se již mohou zdát známé.

|   |   |   |   |
|---|---|---|---|
| [![Ikona][as2-icon]<br>ROZHRANÍ API**AS2 <br>dekódování**][as2-doc] | [![Kódování][as2-icon]<br>ikony ROZHRANÍ**API AS2 <br>**][as2-doc] | [![Ikona][edifact-icon]<br>rozhraní API** <br>EDIFACT dekódování**][edifact-decode-doc] | [![Ikona][edifact-icon]<br>rozhraní API**EDIFACT <br>kódování**][edifact-encode-doc] |
| [![Ikona][flat-file-decode-icon]<br>rozhraní API**Dekódování plochého souboru <br>**][flat-file-decode-doc] | [![Ikona][flat-file-encode-icon]<br>ROZHRANÍ API**Kódování plochého souboru <br>**][flat-file-encode-doc] | [![Účet][integration-account-icon]<br>**integrace <br>** ikony rozhraní API][integration-account-doc] | [![Ikona][liquid-icon]<br>rozhraní API**Liquid** <br> **transformuje**][json-liquid-transform-doc] |
| [![Ikona][x12-icon]<br>rozhraní API** <br>X12 dekódování**][x12-decode-doc] | [![Ikona][x12-icon]<br>rozhraní API**X12 <br>kódování**][x12-encode-doc] | [![Transformace][xml-transform-icon]<br>**XML** <br> **transforms** ikony rozhraní API][xml-transform-doc] | [![Ověření][xml-validate-icon]<br>**XML <br>** ikony rozhraní API][xml-validate-doc] |  
|||||

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Podnikové konektory

Logic Apps poskytuje tyto podnikové konektory pro přístup k podnikovým systémům, jako jsou SAP a IBM MQ:

|   |   |   |
|---|---|---|
| [![Ikona][ibm-3270-icon]<br>ROZHRANÍ**API IBM 3270**][ibm-3270-doc] | [![Ikona][ibm-mq-icon]<br>rozhraní API**IBM MQ**][ibm-mq-doc] | [![Ikona][sap-icon]<br>rozhraní API**SAP**][sap-connector-doc] |
||||

<a name="ise-connectors"></a>

## <a name="ise-connectors"></a>Konektory ISE

Pro aplikace logiky, které vytvoříte a spustíte prostředí služby izolované [integrace (ISE),](#integration-service-environment)Návrhář aplikace logiky identifikuje předdefinované aktivační události a akce, které běží ve vaší službě ISE pomocí popisku **CORE.** Spravované konektory, které běží v ise zobrazit popisek **ISE,** zatímco konektory, které běží v globální, víceklientské služby Logic Apps nezobrazují ani popisek. Tento seznam zobrazuje konektory, které aktuálně mají verze ISE:

|   |   |   |   |   |
|---|---|---|---|---|
[![Ikona][as2-icon]<br>rozhraní API**AS2**][as2-doc] | [![Ikona][azure-blob-storage-icon]<br>rozhraní API**Azure Blob<br>Storage**][azure-blob-storage-doc] | [![Ikona][azure-cosmos-db-icon]<br>rozhraní API**Azure Cosmos <br> DB**][azure-cosmos-db-doc] | [![Ikona][azure-event-hubs-icon]<br>rozhraní API**Azure Event <br>Hubs**][azure-event-hubs-doc] | [![Ikona][azure-file-storage-icon]<br>rozhraní API**Azure File<br>Storage**][azure-file-storage-doc] |
| [![Ikona][azure-service-bus-icon]<br>rozhraní API**Azure Service <br>Bus**][azure-service-bus-doc] | [![Ikona][azure-sql-data-warehouse-icon]<br>rozhraní API**Azure SQL Data <br>Warehouse**][azure-sql-data-warehouse-doc] | [![Ikona][azure-table-storage-icon]<br>rozhraní API**Azure Table <br>Storage**][azure-table-storage-doc] | [![Ikona][azure-queues-icon]<br>rozhraní API** <br>Azure Queues**][azure-queues-doc] | [![Ikona][edifact-icon]<br>rozhraní**API EDIFACT**][edifact-doc] |
| [![Ikona][file-system-icon]<br>rozhraní API**Systém souborů <br>**][file-system-doc] | [![Ikona][ftp-icon]<br>rozhraní API**FTP**][ftp-doc] | [![Ikona][ibm-3270-icon]<br>ROZHRANÍ**API IBM 3270**][ibm-3270-doc] | [![Ikona][ibm-db2-icon]<br>rozhraní API**IBM DB2**][ibm-db2-doc] | [![Ikona][ibm-mq-icon]<br>rozhraní API**IBM MQ**][ibm-mq-doc] |
| [![Ikona][sap-icon]<br>rozhraní API**SAP**][sap-connector-doc] | [![Ikona][sftp-ssh-icon]<br>rozhraní API**SFTP-SSH**][sftp-ssh-doc] | [![Ikona][smtp-icon]<br>rozhraní API**SMTP**][smtp-doc] | [![Ikona][sql-server-icon]<br>rozhraní API**SQL <br>Server**][sql-server-doc] | [![Ikona][x12-icon]<br>rozhraní API**X12**][x12-doc] |
||||||

Další informace najdete v těchto tématech:

* [Přístup k prostředkům virtuální sítě Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Cenový model Logic Apps](../logic-apps/logic-apps-pricing.md)
* [Připojení k virtuálním sítím Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## <a name="triggers-and-action-types"></a>Aktivační události a typy akcí

Konektory mohou poskytovat *aktivační události*, *akce*nebo obojí. *Aktivační událost* je prvním krokem v libovolné aplikaci logiky, obvykle určující událost, která spustí aktivační událost a spustí aplikaci logiky. Například ftp konektor má aktivační událost, která spustí aplikaci logiky "při přidání nebo úpravě souboru". Některé aktivační události pravidelně kontrolují zadanou událost nebo data a poté se aktivují, když zjistí zadanou událost nebo data. Ostatní aktivační události čekají, ale okamžitě se spustí, když dojde k určité události nebo když jsou k dispozici nová data. Aktivační události také předat všechna požadovaná data do aplikace logiky. Vaše aplikace logiky můžete číst a používat tato data v celém pracovním postupu. Například konektor Twitter má aktivační událost "Když je publikován nový tweet", který předá obsah tweetu do pracovního postupu aplikace logiky.

Po spuštění aktivační události Azure Logic Apps vytvoří instanci aplikace logiky a spustí *akce* v pracovním postupu aplikace logiky. Akce jsou kroky, které následují aktivační událost a provádět úkoly v pracovním postupu aplikace logiky. Můžete například vytvořit aplikaci logiky, která získá zákaznická data z databáze SQL a zpracuje je v pozdějších akcích.

Tady jsou obecné typy aktivačních událostí, které azure logic apps poskytuje:

* *Aktivační událost opakování*: Tato aktivační událost je spuštěna podle zadaného plánu a není pevně spojena s konkrétní službou nebo systémem.

* *Aktivační událost dotazování*: Tato aktivační událost pravidelně dotazuje konkrétní službu nebo systém na základě zadaného plánu, kontroluje nová data nebo zda došlo k určité události. Pokud jsou k dispozici nová data nebo dojde k konkrétní události, aktivační událost vytvoří a spustí novou instanci aplikace logiky, která teď může použít data, která jsou předána jako vstup.

* *Nabízená aktivační událost*: Tato aktivační událost čeká a naslouchá novým datům nebo události. Když jsou k dispozici nová data nebo když dojde k události, aktivační událost vytvoří a spustí novou instanci aplikace logiky, která teď může použít data, která jsou předána jako vstup.

<a name="connections"></a>

## <a name="connector-configuration"></a>Konfigurace konektoru

Aktivační události a akce každého konektoru poskytují vlastní vlastnosti, které můžete konfigurovat. Mnoho konektorů také vyžaduje, abyste nejprve *vytvořili připojení* k cílové službě nebo systému a před použitím aktivační události nebo akce v aplikaci logiky poskytli ověřovací pověření nebo jiné podrobnosti o konfiguraci. Musíte například autorizovat připojení k účtu Twitter pro přístup k datům nebo k účtování vaším jménem.

Pro konektory, které používají Azure Active Directory (Azure AD) OAuth, vytvoření připojení znamená přihlášení ke službě, jako je Office 365, Salesforce nebo GitHub, kde je váš přístupový token [šifrovaný](../security/fundamentals/encryption-overview.md) a bezpečně uložený v tajném úložišti Azure. Jiné konektory, například FTP a SQL, vyžadují připojení, které má podrobnosti konfigurace, jako je adresa serveru, uživatelské jméno a heslo. Tyto podrobnosti konfigurace připojení jsou také šifrovány a bezpečně uloženy. Další informace o [šifrování v Azure](../security/fundamentals/encryption-overview.md).

Připojení mohou přistupovat k cílové službě nebo systému tak dlouho, dokud to tato služba nebo systém umožňuje. U služeb, které používají připojení Azure AD OAuth, jako je Office 365 a Dynamics, azure logic apps aktualizuje přístupové tokeny po neomezenou dobu. Ostatní služby mohou mít omezení na to, jak dlouho azure logic apps můžete použít token bez aktualizace. Obecně platí, že některé akce zneplatnit všechny přístupové tokeny, jako je například změna hesla.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>Vlastní rozhraní API a konektory

Chcete-li volat rozhraní API, která spouštějí vlastní kód nebo nejsou k dispozici jako konektory, můžete rozšířit platformu Logic Apps [vytvořením vlastních aplikací rozhraní API](../logic-apps/logic-apps-create-api-app.md). Můžete také [vytvořit vlastní konektory](../logic-apps/custom-connector-overview.md) pro *všechna* rozhraní API rest nebo soap, která tato rozhraní API zpřístupní libovolné aplikaci logiky ve vašem předplatném Azure. Chcete-li, aby byly vlastní aplikace rozhraní API nebo konektory veřejné pro každého, kdo bude v Azure používat, můžete [odeslat konektory pro certifikaci Microsoftu](../logic-apps/custom-connector-submit-certification.md).

> [!NOTE]
> Aplikace logiky, které nasadíte a spustíte v [prostředí integrační služby (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) můžou mít přímý přístup k prostředkům ve virtuální síti Azure. Pokud máte vlastní konektory, které vyžadují místní bránu dat a vytvořili jste tyto konektory mimo ISE, aplikace logiky v ISE můžete také použít tyto konektory.
>
> Vlastní konektory vytvořené v rámci služby ISE nefungují s místní bránou dat. Tyto konektory však můžete přímo přistupovat k místním zdrojům dat, které jsou připojené k virtuální síti Azure, která hostuje ise. Takže aplikace logiky v ISE s největší pravděpodobností nepotřebují bránu dat při komunikaci s těmito prostředky.
>
> Další informace o vytváření služeb Při vytváření služeb Google najdete v tématu [Připojení k virtuálním sítím Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

## <a name="next-steps"></a>Další kroky

* Zobrazit [úplný seznam konektorů](https://docs.microsoft.com/connectors)
* [Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Vytvoření vlastních konektorů pro aplikace logiky](https://docs.microsoft.com/connectors/custom-connectors/)
* [Vytvoření vlastních rozhraní API pro aplikace logiky](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Připojení k místním zdrojům dat z aplikací logiky pomocí místní brány dat"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Vytvoření instance služby Azure API Management pro správu a publikování rozhraní API"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integrace aplikací logiky s aplikacemi API služby App Service"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integrace aplikací logiky s funkcemi Azure"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Zpracování zpráv ve skupinách nebo jako dávky"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Vyhodnotit podmínku a spustit různé akce na základě toho, zda je podmínka pravdivá nebo nepravdivá"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Provedení stejných akcí u každé položky v poli"
[http-doc]: ./connectors-native-http.md "Volání koncových bodů HTTP nebo HTTPS z aplikací logiky"
[http-request-doc]: ./connectors-native-reqres.md "Příjem požadavků HTTP v aplikacích logiky"
[http-response-doc]: ./connectors-native-reqres.md "Odpověď na požadavky HTTP z aplikací logiky"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Volání koncových bodů REST z aplikací logiky"
[http-webhook-doc]: ./connectors-native-webhook.md "Čekání na konkrétní události z koncových bodů HTTP nebo HTTPS"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integrace aplikací logiky s vnořenými pracovními postupy"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Výběr a filtrování polí pomocí akce dotazu"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Spuštění plánu aplikací logiky"
[schedule-delay-doc]: ./connectors-native-delay.md "Zpoždění spuštění další akce"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Zpoždění spuštění další akce"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Spouštění aplikací logiky podle opakovaného plánu"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Spouštějte aplikace logiky, které potřebují zpracovávat data v souvislých blocích"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Uspořádání akcí do skupin, které získají svůj vlastní stav po spuštění akcí ve skupině"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Uspořádejte akce do případů, kterým jsou přiřazeny jedinečné hodnoty. Spusťte pouze případ, jehož hodnota odpovídá výsledku z výrazu, objektu nebo tokenu. Pokud neexistují žádné shody, spusťte výchozí případ"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Zastavení nebo zrušení aktivně spuštěného pracovního postupu pro aplikaci logiky"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Opakujte akce, dokud není zadaná podmínka pravdivá nebo pokud se nezmění nějaký stav"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Provádění datových operací, jako je filtrování polí nebo vytváření tabulek CSV a HTML"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Provádění operací s proměnnými, jako je inicializovat, nastavit, přírůstek, snížení a připojit k proměnné řetězce nebo pole"

<!--Managed connector doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Správa souborů v kontejneru objektů blob pomocí konektoru služby Blob Storage"
[azure-cosmos-db-doc]: https://docs.microsoft.com/connectors/documentdb/ "Připojte se k Azure Cosmos DB, abyste měli přístup k dokumentům a uloženým procedurám"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Sledování událostí publikovaných sítí událostí, například když se změní prostředky Azure nebo prostředky třetích stran"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Připojte se k Centru událostí Azure, abyste mohli přijímat a odesílat události mezi aplikacemi logiky a centry událostí"
[azure-file-storage-doc]: https://docs.microsoft.com/connectors/azurefile/ "Připojení k účtu Azure Storage, abyste mohli vytvářet, aktualizovat, získat a odstraňovat soubory"
[azure-queues-doc]: https://docs.microsoft.com/connectors/azurequeues/ "Připojení k účtu Azure Storage, abyste mohli vytvářet a spravovat fronty a zprávy"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Odesílání zpráv z front a témat služby Service Bus a přijímání zpráv z fronty a odběry služby Service Bus"
[azure-sql-data-warehouse-doc]: https://docs.microsoft.com/connectors/sqldw/ "Připojení k Azure SQL Data Warehouse, abyste si mohli zobrazit svá data"
[azure-table-storage-doc]: https://docs.microsoft.com/connectors/azuretables/ "Připojte se ke svému účtu Azure Storage, abyste mohli vytvářet, aktualizovat a dotazovat tabulky a další"
[biztalk-server-doc]: https://docs.microsoft.com/connectors/biztalk/ "Připojte se k BizTalk serveru, abyste mohli s Azure Logic Apps spouštět biztalkové aplikace vedle sebe"
[box-doc]: ./connectors-create-api-box.md "Připojte se k boxu. Nahrávání, získání, odstranění, seznam souborů a další"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Připojte se k Dropboxu. Nahrávání, získání, odstranění, seznam souborů a další"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Připojení k aplikaci Dynamics CRM Online, abyste mohli pracovat s daty aplikace CRM Online"
[facebook-doc]: ./connectors-create-api-facebook.md "Připojte se k Facebooku. Zveřejněte příspěvek na časové ose, získejte informační kanál stránky a další"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Připojení k místnímu systému souborů"
[ftp-doc]: ./connectors-create-api-ftp.md "Připojení k serverům FTP/FTPS a provádění úloh protokolu FTP, jako je odesílání, získávání a odstraňování souborů a provádění dalších akcí"
[github-doc]: ./connectors-create-api-github.md "Připojení ke GitHubu a sledování problémů"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Připojuje se ke Kalendáři Google a může spravovat kalendář"
[google-drive-doc]: ./connectors-create-api-googledrive.md "Připojte se k GoogleDrivi, abyste mohli pracovat se svými daty"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Připojte se k Listům Google, abyste mohli upravovat listy"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Připojuje se k úkolům Google, abyste mohli spravovat úkoly"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Připojení k 3270 aplikacím na sálových počítači IBM"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Připojte se k IBM DB2 v cloudu nebo místně. Aktualizace řádku, získání tabulky a další"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Připojte se k Informixu v cloudu nebo místně. Přečtěte si řádek, seznam tabulek a další"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Připojení k IBM MQ místně nebo v Azure pro odesílání a přijímání zpráv"
[instagram-doc]: ./connectors-create-api-instagram.md "Připojte se k Instagramu. Spuštění nebo jednání o událostech"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "Připojte se ke svému účtu MailChimp. Správa a automatizace e-mailů"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Připojení k Mandrillu pro komunikaci"
[mysql-doc]: https://docs.microsoft.com/connectors/mysql/ "Připojte se k místní databázi MySQL, abyste mohli číst a zapisovat data"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Připojení k účtu Office 365, abyste mohli odesílat a přijímat e-maily, spravovat kalendář a kontakty a další"
[office-365-users-doc]: ./connectors-create-api-office365-users.md
[onedrive-doc]: ./connectors-create-api-onedrive.md "Připojení k osobnímu Microsoft OneDrivu, abyste mohli nahrávat, mazat, seznamovat soubory a další"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Připojení k vaší firmě Microsoft OneDrive, abyste mohli nahrávat, odstraňovat, uvádět soubory a další"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Připojte se k databázi Oracle, abyste mohli přidávat, vkládat, odstraňovat řádky a další"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Připojení k poštovní schránce Outlooku, abyste mohli spravovat e-maily, kalendáře, kontakty a další"
[postgre-sql-doc]: https://docs.microsoft.com/connectors/postgresql/ "Připojte se k databázi PostgreSQL, abyste mohli číst data z tabulek"
[project-online-doc]: ./connectors-create-api-projectonline.md "Připojte se k Aplikaci Microsoft Project Online, abyste mohli spravovat projekty, úkoly, zdroje a další"
[rss-doc]: ./connectors-create-api-rss.md "Publikování a načtení položek informačního kanálu, aktivace operací při publikování nové položky do informačního kanálu RSS"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Připojte se ke svému účtu Salesforce. Správa obchodních vztahů, zájemců, příležitostí a dalších"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Připojení k místnímu systému SAP"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Připojte se k SendGrid. Odesílání e-mailů a správa seznamů příjemců"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Připojte se ke svému účtu SFTP pomocí SSH. Nahrávání, získání, odstranění souborů a další"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Připojte se k místnímu serveru SharePointu. Správa dokumentů, položek seznamu a dalších položek"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Připojte se k SharePointu Online. Správa dokumentů, položek seznamu a dalších položek"
[slack-doc]: ./connectors-create-api-slack.md "Připojení k Slacku a zveřejňování zpráv do kanálů Slack"
[smtp-doc]: ./connectors-create-api-smtp.md "Připojení k serveru SMTP a odeslání e-mailu s přílohami"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Připojení ke SparkPostu pro komunikaci"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Připojte se k Azure SQL Database nebo SQL Serveru. Vytváření, aktualizace, získání a odstranění položek v databázové tabulce SQL"
[teradata-doc]: https://docs.microsoft.com/connectors/teradata/ "Připojení k databázi Teradata pro čtení dat z tabulek"
[trello-doc]: ./connectors-create-api-trello.md "Spojte se s Trello. Spravujte své projekty a organizujte cokoli s kýmkoli"
[twilio-doc]: ./connectors-create-api-twilio.md "Připojte se k Twiliovi. Odesílání a odesílání zpráv, získání dostupných čísel, správa příchozích telefonních čísel a další"
[twitter-doc]: ./connectors-create-api-twitter.md "Připojte se k Twitteru. Získejte časové osy, zveřejňujte tweety a další"
[yammer-doc]: ./connectors-create-api-yammer.md "Připojte se k Yammeru. Zveřejňování zpráv, získání nových zpráv a další"
[youtube-doc]: ./connectors-create-api-youtube.md "Připojte se k YouTube. Správa videí a kanálů"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Kódování a dekódování zpráv používajích protokol AS2"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Kódování a dekódování zpráv používajích protokol EDIFACT"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Dekódování zpráv používajích protokol EDIFACT"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Kódování zpráv používajích protokol EDIFACT"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Informace o plochém souboru podnikové integrace"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Informace o plochém souboru podnikové integrace"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Správa metadat pro artefakty účtu integrace"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Transformace JSON pomocí tekutých šablon"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Kódování a dekódování zpráv používajích protokol X12"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Dekódování zpráv používajích protokol X12"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Kódování zpráv používajích protokol X12"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Transformace zpráv XML"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Ověřit zprávy XML"

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
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
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
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
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
[sftp-ssh-icon]: ./media/apis-list/sftp.png
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
