---
title: Konektory pro Azure Logic Apps | Dokumentace Microsoftu
description: Automatizovat pracovní postupy pomocí integrovaných spravované rozhraní API, místní účet integrace a enterprise konektory pro Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 06/29/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 2bb3e2ce29037372395aa0b30e9f76f3e712667d
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096607"
---
# <a name="connectors-for-azure-logic-apps"></a>Konektory pro Azure Logic Apps

Konektory přehrát nedílnou součástí automatizované pracovní postupy vytvořené službou Azure Logic Apps. Pomocí konektorů ve vašich logic apps můžete rozšířit možnosti pro místní a cloudových aplikací k provádění úloh s daty, které vytvoříte a už máte. Při Logic Apps nabízí ~ 200 + konektory, tento článek popisuje oblíbených a často používané konektory, které jsou úspěšně používané tisíců aplikací a miliony spuštěních pro zpracování dat a informace.
Konektory jsou k dispozici jako built-ins nebo spravovaných konektorů. 

* [**Built-INS**](#built-ins): Tyto vestavěné akce a aktivační události pomáhají vytvářet logiku aplikace, které běží na vlastní plány, komunikují s další koncové body přijímat a reagovat na požadavky a volání Azure functions se aplikace API Azure (webové aplikace), vlastní Rozhraní API spravovat a publikovat s Azure API Management a vnořené logiku aplikace, které může přijímat požadavky. Můžete také použít integrované akce, které vám pomůžou uspořádání a řízení aplikace logiky pracovního postupu a také pracovat s daty.

* **Spravované konektory**: těchto konektorů poskytuje triggery a akce pro přístup k dalším službám a systémy. Některé konektory vyžadují, abyste nejdřív vytvořili připojení, které jsou spravovány nástrojem Azure Logic Apps. Spravovaných konektorů jsou uspořádány do těchto skupin:

  |   |   |
  |---|---|
  | [**Spravovaných konektorů rozhraní API**](#managed-api-connectors) | Vytvoření aplikace logiky, které používají služby, jako je například úložiště objektů Blob Azure, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online a mnoho dalších. | 
  | [**Místní konektory**](#on-premises-connectors) | Po dokončení instalace a nastavení [místní brána dat][gateway-doc], tyto konektory nápovědy přístup aplikace logiky uspořádání místních systémů jako SQL Server, SharePoint Server, Oracle DB, sdílené složky a dalších. | 
  | [**Integrace účet konektory**](#integration-account-connectors) | K dispozici, při vytváření a platit pro účet integrace transformace tyto konektory a ověřit XML, kódování a dekódování plochých souborů a zpracovat business-to-business (B2B) zpráv s AS2, EDIFACT a X12 protokoly. | 
  | [**Konektory Enterprise**](#enterprise-connectors) | Poskytnout přístup k podnikových systémů, jako je například SAP a IBM MQ Provede další náklady. |
  ||| 

  Například pokud používáte Microsoft BizTalk Server, aplikace logiky můžete připojit k a komunikují se serverem BizTalk pomocí [konektor BizTalk Server](#on-premises-connectors). 
  Potom můžete rozšířit nebo provádět operace jako BizTalk ve vašich logic apps pomocí [integrace účet konektory](#integration-account-connectors). 

Technické informace o aktivační události a akce, které jsou definovány popis Swagger plus žádné limity, každý konektor najdete v části [Connector – podrobnosti](/connectors/). Informace o nákladech, najdete v části [Logic Apps podrobnosti o cenách](https://azure.microsoft.com/pricing/details/logic-apps/) a [Logic Apps cenový model](../logic-apps/logic-apps-pricing.md). 

<a name="built-ins"></a>

## <a name="built-ins"></a>Integrované

Služba Logic Apps poskytuje integrované aktivační události a akce, můžete vytvořit na základě plánu pracovních pomoci logic apps komunikovat s jinými aplikací a služeb, řízení prostřednictvím aplikace logiky pracovního postupu a spravovat nebo pracovat s daty. 

|   |   |   |   | 
|---|---|---|---| 
| [![Ikona rozhraní API][schedule-icon]<br/>**plán**][recurrence-doc] | -Spuštění aplikace logiky podle stanoveného plánu, od základní až po komplexní opakování s **opakování** aktivační události. <p>-Pozastavit aplikace logiky pro zadaná doba trvání s **zpoždění** akce. <p>-Pozastavit svou aplikaci logiky do určeného data a času s **zpoždění až** akce. | [![Ikona rozhraní API][http-icon]<br/>**HTTP**][http-doc] | Komunikujete se žádný koncový bod prostřednictvím protokolu HTTP s triggery a akce HTTP, HTTP + Swagger a HTTP + Webhooku. | 
| [![Ikona rozhraní API][http-request-icon]<br/>**požadavku**][http-request-doc] | – Zkontrolujte svou aplikaci logiky s z jiných aplikací nebo služeb, aktivační události mřížky prostředků událostí nebo aktivační událost pro odpovědi na Azure Security Center výstrahy s **požadavku** aktivační události. <p>-Odeslat odpovědi na aplikace nebo služby s **odpovědi** akce. | [![Ikona rozhraní API][batch-icon]<br/>**Batch**][batch-doc] | -Zpracování zpráv v dávkách s **dávky zprávy** aktivační události. <p>-Aplikace logiky volání, které mají existující dávky aktivačních událostí, jejichž **odesílání zpráv do dávky** akce. | 
| [![Ikona rozhraní API][azure-functions-icon]<br/>**Azure Functions**][azure-functions-doc] | Volání funkce Azure, spuštěné fragmenty vlastní kód (C# nebo Node.js) z vašich logic apps. | [![Ikona rozhraní API][azure-api-management-icon]</br>**Azure API Management**][azure-api-management-doc] | Volání triggery a akce definované vlastní rozhraní API, která můžete spravovat a publikovat s Azure API Management. | 
| [![Ikona rozhraní API][azure-app-services-icon]<br/>**Azure App Services**][azure-app-services-doc] | Volání aplikace API Azure nebo webové aplikace hostované v Azure App Service. Triggery a akce definované těmito tyto aplikace se zobrazí jako všechny ostatní prvotřídní triggery a akce po zahrnuté Swagger. | [![Ikona rozhraní API][azure-logic-apps-icon]<br/>**Azure<br/>Logic Apps**][nested-logic-app-doc] | Volejte další logiku aplikace, které začínat aktivační událost požadavku. | 
||||| 

### <a name="control-workflow"></a>Řízení pracovního postupu

Tady jsou předdefinované akce pro uspořádání a řízení akce v aplikaci logiky pracovního postupu:

|   |   |   |   | 
|---|---|---|---| 
| [![Předdefinované ikonu][condition-icon]<br/>**podmínky**][condition-doc] | Vyhodnocení podmínky a spouštět různé akce podle toho, zda je podmínka vyhodnocena jako true nebo false. | [![Předdefinované ikonu][for-each-icon]</br>**pro každou**][for-each-doc] | Proveďte stejné akce na každou položku v matici. | 
| [![Předdefinované ikonu][scope-icon]<br/>**oboru**][scope-doc] | Skupina akce do *obory*, který získat jejich stav po dokončení spuštění akce v oboru. | [![Předdefinované ikonu][switch-icon]</br>**přepínače**][switch-doc] | Skupina akce do *případech*, které se přiřazují s výjimkou případu výchozí jedinečné hodnoty. Spusťte pouze tento případ, jejíž přiřazené hodnota odpovídá výsledek z výrazu, objektu nebo token. Pokud neexistují žádné shody, spusťte tento případ výchozí. | 
| [![Předdefinované ikonu][terminate-icon]<br/>**ukončení**][terminate-doc] | Zastavte pracovního postupu aktivně spuštěná logiku aplikace. | [![Předdefinované ikonu][until-icon]<br/>**dokud**][until-doc] | Akce opakujte, dokud je zadaná podmínka vyhodnocena jako true, nebo došlo ke změně některých stavu. | 
||||| 

### <a name="manage-or-manipulate-data"></a>Spravovat nebo pracovat s daty

Tady jsou předdefinované akce pro práci s výstupy dat a jejich formátů:  

|   |   | 
|---|---| 
| ![Předdefinované ikonu][data-operations-icon]<br/>**Operace s daty** | Provádění operací s daty: <p>- **Napište**: vytvoření jediného výstupu z více vstupů s různými typy. <br>- **Vytvoření tabulky CSV**: vytvoření oddělené čárkou (CSV) pomocí pole s objekty JSON. <br>- **Vytvoření tabulky HTML**: vytvoření tabulky HTML z pole s objekty JSON. <br>- **Pole filtru**: vytvoření pole z položek v jiném poli, které splňují kritéria. <br>- **Připojení k**: vytvoření řetězce z všechny položky v poli a ty položky oddělujte zadaného oddělovače. <br>- **Analyzovat JSON**: vytváření uživatelsky přívětivý tokeny z vlastností a jejich hodnoty ve formátu JSON obsahu, můžete použít tyto vlastnosti v pracovním postupu. <br>- **Vyberte**: vytvořte pole s objekty JSON transformace položky nebo hodnoty v jiném poli a mapování těchto položek do zadané vlastnosti. | 
| ![Předdefinované ikonu][date-time-icon]<br/>**Datum a čas** | Provádění operací s časová razítka: <p>- **Přidat na dobu**: přidejte zadaný počet jednotek do časového razítka. <br>- **Převést časové pásmo**: převést časovým razítkem ze zdroje časové pásmo na cílové časové pásmo. <br>- **Aktuální čas**: Vrátí aktuální časové razítko jako řetězec. <br>- **Získat datum v budoucnosti**: Vrátí aktuální časové razítko plus jednotky zadané doby. <br>- **Získání po čase**: Vrátí aktuální časové razítko minus jednotky zadané doby. <br>- **Odečtena od času**: odečíst počet časových jednotek z časového razítka. |
| [![Předdefinované ikonu][variables-icon]<br/>**proměnné**][variables-doc] | Proveďte operace s proměnnými: <p>- **Připojit k proměnné pole**: vložení a hodnotu jako poslední položky v poli uložení proměnné. <br>- **Připojit k proměnné řetězce**: vložení a hodnotu jako poslední znak v řetězci uložení proměnné. <br>- **Proměnná snížení**: Snižte proměnné konstantní hodnotu. <br>- **Přírůstek proměnné**: zvýšit proměnnou konstantní hodnotu. <br>- **Inicializace proměnné**: Vytvoření proměnné a deklarovat jeho datový typ a počáteční hodnota. <br>- **Nastavená proměnná**: přiřadit jinou hodnotu existující proměnné. |
|  |  | 

<a name="managed-api-connectors"></a>

## <a name="managed-api-connectors"></a>Spravovaných konektorů rozhraní API

Zde jsou další oblíbené konektory pro automatizaci úloh, procesy a pracovní postupy se tyto služby nebo systémy:

|   |   |   |   | 
|---|---|---|---| 
| [![Ikona rozhraní API][azure-service-bus-icon]<br/>**Azure Service Bus**][azure-service-bus-doc] | Spravovat asynchronní zprávy, relací a odběry témat s nejčastěji používané konektorem v Logic Apps. | [![Ikona rozhraní API][sql-server-icon]<br/>**systému SQL Server**][sql-server-doc] | Připojte se k systému SQL Server na místní nebo Azure SQL Database v cloudu, můžete spravovat záznamy, spustit uložené procedury nebo provádět dotazy. | 
| [![Ikona rozhraní API][office-365-outlook-icon]<br/>**Office 365<br/>aplikace Outlook**][office-365-outlook-doc] | Připojte se k účtu služeb Office 365 e-mailu, můžete vytvořit a spravovat e-mailů, úlohy, kalendář události a schůzky, kontakty, požadavky a další. | [![Ikona rozhraní API][azure-blob-storage-icon]<br/>**objektů Blob v Azure<br/>úložiště**][azure-blob-storage-doc] | Připojte se k účtu úložiště, můžete vytvořit a spravovat obsah objektu blob. | 
| [![Ikona rozhraní API][sftp-icon]<br/>**protokolu SFTP**][sftp-doc] | Připojte k serverům pomocí protokolu SFTP, máte přístup z Internetu, abyste mohli pracovat se soubory a složky. | [![Ikona rozhraní API][sharepoint-online-icon]<br/>**SharePoint<br/>Online**][sharepoint-online-doc] | Připojení ke službě SharePoint Online, můžete spravovat soubory, přílohy, složky a další. | 
| [![Ikona rozhraní API][dynamics-365-icon]<br/>**Dynamics 365<br/>CRM Online**][dynamics-365-doc] | Připojte se k účtu Dynamics 365, můžete vytvořit a spravovat záznamy, položky a další. | [![Ikona rozhraní API][ftp-icon]<br/>**FTP**][ftp-doc] | Připojení k serverům FTP, které je přístupné z Internetu, abyste mohli pracovat se soubory a složky. | 
| [![Ikona rozhraní API][salesforce-icon]<br/>**Salesforce**][salesforce-doc] | Připojte se ke svému účtu Salesforce, můžete vytvořit a spravovat položek, jako jsou záznamy, úlohy, objekty a další. | [![Ikona rozhraní API][twitter-icon]<br/>**služby Twitter**][twitter-doc] | Připojte se k účtu služby Twitter, můžete spravovat tweetů, délky, časový harmonogram a další. Uložte vaše tweetů SQL, aplikace Excel nebo SharePoint. | 
| [![Ikona rozhraní API][azure-event-hubs-icon]<br/>**Azure Event Hubs**][azure-event-hubs-doc] | Spotřebovávají a publikovat události prostřednictvím centra událostí. Například získat výstup z aplikace logiky službou Event Hubs a potom odešlete, výstup poskytovateli analýzu v reálném čase. | [![Ikona rozhraní API][azure-event-grid-icon]<br/>**událostí Azure**</br>**mřížky**][azure-event-grid-doc] | Sledování událostí publikováno události mřížky, například při změně prostředků Azure nebo prostředky třetích stran. | 
||||| 

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Místní konektory 

Tady jsou některé běžně používané konektory, které poskytují přístup k datům a prostředkům v místních systémů. Než bude možné vytvořit připojení k místním systémem, je nutné nejprve [stáhnout, nainstalovat a nastavit bránu místní data][gateway-doc]. Tato brána poskytuje zabezpečenou komunikaci bez nutnosti nastavení nezbytné síťové infrastruktury. 

|   |   |   |   |   | 
|---|---|---|---|---| 
| ![Ikona rozhraní API][biztalk-server-icon]<br/>**BizTalk**</br> **Server** | [![Ikona rozhraní API][file-system-icon]<br/>**soubor</br> systému**][file-system-doc] | [![Ikona rozhraní API][ibm-db2-icon]<br/>**IBM DB2**][ibm-db2-doc] | [![Ikona rozhraní API][ibm-informix-icon]<br/>**IBM** </br> **Informix**][ibm-informix-doc] | ![Ikona rozhraní API][mysql-icon]<br/>**MySQL** | 
| [![Ikona rozhraní API][oracle-db-icon]<br/>**Oracle DB**][oracle-db-doc] | ![Ikona rozhraní API][postgre-sql-icon]<br/>**PostgreSQL** | [![Ikona rozhraní API][sharepoint-server-icon]<br/>**SharePoint</br> serveru**][sharepoint-server-doc] | [![Ikona rozhraní API][sql-server-icon]<br/>**SQL</br> serveru**][sql-server-doc] | ![Ikona rozhraní API][teradata-icon]<br/>**Teradata** | 
||||| 

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Konektory účtu pro integraci 

Tady jsou konektory pro vytváření řešení business-to-business (B2B) s logic apps, při vytváření a platit [integrace účet](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), která je dostupná prostřednictvím Integration Pack Enterprise (EIP) v Azure. S tímto účtem můžete vytvořit a ukládání artefaktů B2B například obchodní partnery, smlouvy, map, schémata, certifikáty a tak dále. Pokud chcete použít tyto artefakty, přidružte svůj účet integrace aplikace logiky. Pokud aktuálně používáte BizTalk serveru, tyto konektory zdát známé již.

|   |   |   |   | 
|---|---|---|---| 
| [![Ikona rozhraní API][as2-icon]<br/>**AS2</br> dekódování**][as2-decode-doc] | [![Ikona rozhraní API][as2-icon]<br/>**AS2</br> kódování**][as2-encode-doc] | [![Ikona rozhraní API][edifact-icon]<br/>**EDIFACT</br> dekódování**][edifact-decode-doc] | [![Ikona rozhraní API][edifact-icon]<br/>**EDIFACT</br> kódování**][edifact-encode-doc] | 
| [![Ikona rozhraní API][flat-file-decode-icon]<br/>**plochý soubor</br> dekódování**][flat-file-decode-doc] | [![Ikona rozhraní API][flat-file-encode-icon]<br/>**plochý soubor</br> kódování**][flat-file-encode-doc] | [![Ikona rozhraní API][integration-account-icon]<br/>**integrace<br/>účtu**][integration-account-doc] | [![Ikona rozhraní API][liquid-icon]<br/>**kapaliny**</br>**transformuje**][json-liquid-transform-doc] | 
| [![Ikona rozhraní API][x12-icon]<br/>**X12</br> dekódování**][x12-decode-doc] | [![Ikona rozhraní API][x12-icon]<br/>**X12</br> kódování**][x12-encode-doc] | [![Ikona rozhraní API][xml-transform-icon]<br/>**XML**</br>**transformuje**][xml-transform-doc] | [![Ikona rozhraní API][xml-validate-icon]<br/>**XML <br/>ověření**][xml-validate-doc] |  
||||| 

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Podnikové konektory

Aplikace logiky můžete dostat podnikových systémů, jako je například SAP a IBM MQ:

|   |   | 
|---|---| 
| [![Ikona rozhraní API][ibm-mq-icon]<br/>**IBM MQ**][ibm-mq-doc] | [![Ikona rozhraní API][sap-icon]<br/>**SAP**][sap-connector-doc] |
||| 

## <a name="more-about-triggers-and-actions"></a>Další informace o aktivační události a akce

Několik konektorů poskytuje *aktivační události* aplikace logiky, upozornit, když konkrétní události dojít. Proto pokud dojde k těmto událostem, aktivační událost vytvoří a spustí instanci aplikace logiky. Konektor FTP má například "při přidání nebo změny souboru" aktivační událost, který se spustí aplikace logiky při aktualizaci souboru. 

Služba Logic Apps poskytuje tyto druhy aktivační události:  

* *Cyklického dotazování aktivační události*: tyto triggery odesílají službě v zadaných intervalech dotazy a kontroly pro nová data. 

  Jakmile je k dispozici nová data, novou instanci aplikace logiky se vytvoří a spustí s daty, která je předána jako vstup. 

* *Push aktivační události*: tyto triggery naslouchají pro nová data na koncový bod, nebo pro událost provést, který vytvoří a spustí novou instanci aplikace logiky.

* *Aktivační událost opakování*: této aktivační události vytvoří a spustí instanci aplikace logiky podle zadaného plánu.

Konektorů také poskytuje *akce* , provádět úlohy v aplikaci logiky pracovního postupu. Například aplikace logiky můžete číst data a tato data použít v následujících krocích aplikace logiky. Přesněji řečeno aplikace logiky můžete najít zákazníkovi z databáze SQL a zpracování těchto dat později v pracovním postupu svou aplikaci logiky. 

Další informace o aktivační události a akce, najdete [konektory přehled](connectors-overview.md). 

## <a name="custom-apis-and-connectors"></a>Vlastní rozhraní API a konektorů 

K volání rozhraní API, která nejsou k dispozici jako konektory nebo spuštění vlastního kódu, můžete rozšířit platformy Logic Apps pomocí [vytváření vlastní aplikace API](../logic-apps/logic-apps-create-api-app.md). Můžete také [vytvořit vlastní konektory](../logic-apps/custom-connector-overview.md) pro *žádné* založený na protokolu SOAP rozhraní API, která tato rozhraní API zpřístupnit pro všechny aplikace logiky ve vašem předplatném Azure nebo REST.
Chcete-li zveřejnit vlastní aplikace API nebo konektory pro každý, kdo pro použití v Azure, můžete [odeslat konektory pro certifikaci Microsoft](../logic-apps/custom-connector-submit-certification.md).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Odeslání nebo hlasovat o nápady pro konektory a Azure Logic Apps, najdete [web pro zasílání názorů uživatele Logic Apps](http://aka.ms/logicapps-wish).

* Jsou dokumenty chybějící články nebo podrobnosti, které se domníváte, že jsou důležité? Pokud ano, můžete pomoct přidáním do stávající články nebo vytvořením vlastní. Dokumentace je open source a hostované na Githubu. Začínáme v Azure dokumentaci [úložiště GitHub](https://github.com/Microsoft/azure-docs). 

## <a name="next-steps"></a>Další postup

* [Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Vytvoření vlastních konektorů pro logic apps](https://docs.microsoft.com/connectors/custom-connectors/)
* [Vytvoření vlastních rozhraní API pro aplikace logiky](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Připojení k místním zdrojům dat z aplikací logiky pomocí místní brány dat"

<!--Built-ins doc links-->
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integrace aplikací logiky se službou Azure Functions"
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Vytvoření instance Azure API Management pro správu a publikování vašich rozhraní API"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integrace aplikací logiky s funkcí App Service API Apps"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Odesílání zpráv z front a témat služby Service Bus a příjem zpráv z front a odběrů služby Service Bus"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Zpracování zpráv ve skupinách, nebo jako balíků"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Vyhodnocení podmínky a spouštět různé akce podle toho, zda je podmínka vyhodnocena jako true nebo false"
[delay-doc]: ./connectors-native-delay.md "Provádění zpožděných akcí"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Provádět stejné akce na každou položku v pole"
[http-doc]: ./connectors-native-http.md "Provádění volání HTTP pomocí konektoru HTTP"
[http-request-doc]: ./connectors-native-reqres.md "Přidání akcí pro požadavky a odpovědi HTTP do aplikací logiky"
[http-response-doc]: ./connectors-native-reqres.md "Přidání akcí pro požadavky a odpovědi HTTP do aplikací logiky"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Provádění volání HTTP pomocí konektoru HTTP + Swagger"
[http-webook-doc]: ./connectors-native-webhook.md "Přidání akce webhooku protokolu HTTP a aktivační události do aplikace logiky"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integrace aplikací logiky s vnořenými pracovními postupy"
[query-doc]: ./connectors-native-query.md "Výběr a filtrování polí pomocí akce dotazu"
[recurrence-doc]:  ./connectors-native-recurrence.md "Aktivace opakujících se akcí pro aplikace logiky"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Uspořádání akcí do skupin, které získat jejich stav po dokončení spuštění akce ve skupině" 
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Uspořádání akcí do případů, které jsou přiřazeny jedinečné hodnoty. Spusťte pouze v případě, jejíž hodnota odpovídá výsledek z výrazu, objektu nebo token. Pokud neexistují žádné shody, spusťte tento případ výchozí"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Zastavení nebo zrušit aktivně spuštěný pracovní postup pro svou aplikaci logiky"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Opakujte akce, dokud je zadaná podmínka vyhodnocena jako true, nebo došlo ke změně některých stavu"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Provádění operací s proměnné, jako například inicializovat, sady, přírůstek a snížení a připojit k proměnná řetězce nebo pole"

<!--Managed API doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Správa souborů v kontejneru objektů blob pomocí konektoru služby Blob Storage"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md " Sledování událostí publikováno události mřížky, například při změně prostředků Azure nebo prostředky třetích stran."
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
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Připojte se k Google listy, můžete upravit své"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Připojení k Úkolům Google a správa úkolů"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Připojení k IBM DB2 v cloudu nebo místním prostředí. Aktualizace řádku, získání tabulky a provádění dalších akcí"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Připojení k Informix v cloudu nebo místním prostředí. Čtení řádku, výpis tabulek a provádění dalších akcí"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Připojení k IBM MQ místně nebo v Azure a odesílat a přijímat zprávy"
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
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Připojení k SendGridu. Odesílání e-mailů a Správa příjemců seznamů"
[sftp-doc]: ./connectors-create-api-sftp.md "Připojení k účtu SFTP. Nahrávání, získávání, odstraňování souborů a provádění dalších akcí"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Přípojení k místnímu serveru SharePoint. Správa dokumentů, vypisování položek a provádění dalších akcí"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Připojení k SharePointu Online. Správa dokumentů, vypisování položek a provádění dalších akcí"
[slack-doc]: ./connectors-create-api-slack.md "Připojení ke Slacku a odesílání zpráv do kanálů Slacku."
[smtp-doc]: ./connectors-create-api-smtp.md "Připojení k serveru SMTP a odesílání e-mailů s přílohami"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Připojení ke SparkPostu pro komunikaci"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Připojte k Azure SQL Database nebo SQL Server. Vytváření, aktualizace, získávání a odstraňování položek v tabulce databáze SQL."
[trello-doc]: ./connectors-create-api-trello.md "Připojení k Trello. Správa projektů a organizace čehokoli s kýmkoli"
[twilio-doc]: ./connectors-create-api-twilio.md "Připojení k Twiliu. Odesílání a získávání zpráv, získávání dostupných čísel, správa příchozích hovorů z telefonních čísel a provádění dalších akcí"
[twitter-doc]: ./connectors-create-api-twitter.md "Připojení k Twitteru. Získávání časových os, odesílání tweetů a provádění dalších akcí"
[wunderlist-doc]: ./connectors-create-api-wunderlist.md "Připojení k Wunderlistu. Správa úkolů a seznamů úkolů, udržování synchronizace všeho, co potřebujete k životu, a provádění dalších akcí"
[yammer-doc]: ./connectors-create-api-yammer.md "Připojení k Yammeru. Odesílání zpráv, získávání nových zpráv a provádění dalších akcí"
[youtube-doc]: ./connectors-create-api-youtube.md "Připojení k Youtube. Správa videí a kanálů"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Přečtěte si víc o podnikové integraci AS2."
[as2-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-as2-decode.md "Přečtěte si víc o dekódování pro podnikovou integraci AS2."
[as2-encode-doc]:../logic-apps/logic-apps-enterprise-integration-as2-encode.md "Přečtěte si víc o kódování pro podnikovou integraci AS2."
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