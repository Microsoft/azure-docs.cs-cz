---
title: Konektory pro Azure Logic Apps | Dokumentace Microsoftu
description: Vyberte si ze všech dostupných konektorů Microsoftu pro sestavování a vytváření aplikací logiky
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: f1f1fd50-b7f9-4d13-824a-39678619aa7a
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: estfan; ladocs
ms.openlocfilehash: caeb3bbf6e3106aff498b12906b730191e56a3d5
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294822"
---
# <a name="connectors-list"></a>Seznam konektorů
Triggery a akce definované v popisu Swaggeru každého konektoru a všechna omezení konektoru najdete v [podrobnostech o konektoru](/connectors/).

Konektory jsou nedílnou součástí vytváření aplikací logiky. Pomocí těchto konektorů můžete naplno využít svoje místní a cloudové aplikace k provádění nejrůznějších operací s daty, která vytváříte, a daty, která už máte. Konektory jsou dostupné buď jako integrované akce, nebo jako spravované konektory.

**Integrované akce:** Samotný modul Logic Apps poskytuje integrované akce pro komunikaci s koncovými body a provádění úloh. Tyto akce můžete použít například k volání koncových bodů HTTP, služby Azure Functions a operací služby Azure API Management nebo k manipulaci se zprávami pomocí operací s daty a proměnných.

**Spravované konektory:** Poskytují různým službám přístup k rozhraním API tím, že vytváří připojení rozhraní API, která hostuje a spravuje služba Logic Apps. Spravované konektory spadají do následujících kategorií:

* **Standardní konektory:** Jsou automaticky dostupné a zahrnuté při používání aplikací logiky. Mezi příklady patří Service Bus, Power BI, OneDrive a mnoho dalších.

* **Místní konektory:** Připojují se k místním serverovým aplikacím pomocí [místní brány dat][gatewaydoc]. Místní konektory zahrnují možnosti připojení k serverovým aplikacím, jako jsou SharePoint Server, SQL Server, Oracle DB, sdílené složky a další.

* **Konektory účtu pro integraci:** Jsou dostupné po zakoupení účtu pro integraci. Pomocí těchto konektorů můžete transformovat a ověřovat XML, zpracovávat zprávy typu business-to-business pomocí AS2, X12 nebo EDIFACT a kódovat a dekódovat ploché soubory. Pokud pracujete s BizTalk Serverem, tyto konektory jsou ideální k rozšíření pracovních postupů BizTalk do Azure.  

    BizTalk Server obsahuje také [Adaptér Logic Apps](https://msdn.microsoft.com/library/mt787163.aspx), který zahrnuje příjem z aplikace logiky a odesílání do aplikace logiky.

* **Podnikové konektory:** Zahrnují MQ a SAP. Dostupné za další poplatek. 

Další informace o cenách najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/logic-apps/) a [cenovém modelu](../logic-apps/logic-apps-pricing.md) pro Logic Apps. 

## <a name="popular-connectors"></a>Oblíbené konektory
Pomocí těchto konektorů úspěšně zpracovávají data a informace tisíce aplikací s miliony spuštění. 

### <a name="built-in-actions"></a>Integrované akce
Modul Logic Apps poskytuje akce umožňující manipulaci s daty, komunikaci přes protokol HTTP a řízení toku definice aplikace logiky. Mezi tyto akce patří:

| |  |  |  |
| --- | --- | --- | --- |
| [![Ikona rozhraní API][HTTPicon]<br/>**HTTP**][httpdoc] | Použijte aplikace logiky ke komunikaci s jakýmkoli koncovým bodem přes HTTP.| [![Ikona rozhraní API][Azure-Functionsicon]<br/>**Azure Functions**][azure-functionsdoc] | Vytvořte funkce, které spouští vlastní fragmenty kódu v jazyce C# nebo Node.js, a potom tyto funkce použijte ve svých aplikacích logiky.  |
| [![Ikona rozhraní API][HTTP-Requesticon]<br/>**Žádost**][HTTP-Requestdoc] | Poskytuje volatelnou adresu URL HTTPS, která se obvykle používá jako webhook v ostatních aplikacích. Po přijetí žádosti na této adrese URL se spustí aplikace logiky. | [![Ikona rozhraní API][Recurrenceicon]<br/>**Plán**][recurrencedoc] | Spouštějte aplikace logiky na základě jednoduchých nebo složitých plánů opakování. Můžete vytvářet jednoduché plány, které se opakují každý den, i složité plány, které se opakují poslední pátek v měsíci každou hodinu mezi 9:00 a 17:00. |
| [![Ikona rozhraní API][CallLogicApp-icon]<br/>**Volání<br/>aplikace logiky**][nested-logic-appdoc] | Zavolejte vnořenou aplikaci logiky. Jako vnořenou aplikaci logiky můžete volat každou aplikaci logiky s triggerem požadavku.| [![Ikona rozhraní API][API/Web-Appicon]<br/>**Aplikace API**][api/web-appdoc] | Zavolejte aplikaci API App Service. Funkce API Apps se Swaggerem se vykresluje stejně jako ostatní aplikace první třídy.|

### <a name="standard-connectors"></a>Standardní konektory
Následující tabulka uvádí nejoblíbenější konektory našich uživatelů:

| |  |  |  |
| --- | --- | --- | --- |
| [![Ikona rozhraní API][AzureBlobStorageicon]<br/>**Azure Blob<br/>Storage**][AzureBlobStoragedoc] | Pokud chcete automatizovat jakoukoli úlohu s vaším účtem úložiště, měli byste se podívat na tento konektor. Podporuje operace CRUD (vytvoření, čtení, aktualizace a odstranění). | [![Ikona rozhraní API][Dynamics-365icon]<br/>**Dynamics 365<br/>CRM Online**][Dynamics-365doc] | Toto je jeden z nejžádanějších konektorů. Obsahuje triggery a akce usnadňující automatizaci pracovních postupů pomocí vodítek a dalších možností. |
| [![Ikona rozhraní API][Event-Hubs-icon]<br/>**Event Hubs**][event-hubs-doc] | Zpracovávejte a publikujte události v centru událostí. Můžete například pomocí služby Event Hubs získat výstup vaší aplikace logiky a pak ho odeslat poskytovateli analýz v reálném čase. | [![Ikona rozhraní API][FTPicon]<br/>**FTP**][FTPdoc] | Pokud je váš server FTP přístupný z internetu, můžete automatizovat pracovní postupy pro práci se soubory a složkami. <br/><br/>V konektoru SFTP je k dispozici i SFTP. |
| [![Ikona rozhraní API][Office-365-Outlookicon]<br/>**Office 365<br/>Outlook**][office365-outlookdoc] | Velké množství triggerů a ještě více akcí umožňujících využití e-mailu a událostí Office 365 v rámci vašich pracovních postupů. <br/><br/>Tento konektor zahrnuje akci *e-mailu se schválením* pro schvalování žádostí o dovolenou, vyúčtování výdajů atd. <br/><br/>V konektoru Uživatelé Office 365 jsou k dispozici i uživatelé Office 365.| [![Ikona rozhraní API][Salesforceicon]<br/>**Salesforce**][salesforcedoc] | Jednoduše se přihlaste pomocí účtu Salesforce a získejte přístup k objektům, jako jsou vodítka, a dalším. | 
| [![Ikona rozhraní API][Service-Busicon]<br/>**Service Bus**][Service-Busdoc] | Nejoblíbenější konektor v aplikacích logiky. Zahrnuje triggery a akce zajišťující asynchronní zasílání zpráv a publikování a přihlašování k odběru dotazů, předplatných a témat. |  [![Ikona rozhraní API][SharePointicon]<br/>**SharePoint<br/>Online**][SharePointdoc] | Pokud SharePoint k ničemu nepoužíváte a mohli byste využít automatizaci, měli byste se podívat na tento konektor. Můžete ho použít s místní službou SharePoint nebo SharePoint Online. |
| [![Ikona rozhraní API][SQL-Servericon]<br/>**SQL Server**][SQL-Serverdoc] | Jeden z nejpoužívanějších konektorů. Dokáže se připojit k místnímu SQL Serveru a ke službě Azure SQL Database. | [![Ikona rozhraní API][Twittericon]<br/>**Twitter**][Twitterdoc] | Jednoduše se přihlaste pomocí účtu Twitteru a následně spouštějte pracovní postup, když se publikuje nový tweet. Pak tyto tweety uložte do databáze SQL nebo seznamu služby SharePoint. | 

### <a name="on-premises-connectors"></a>Místní konektory 

Místní konektory poskytují přístup k datům na místních serverech.  Vytvoření připojení k místnímu serveru vyžaduje [místní bránu dat][gatewaydoc], která zajišťuje zabezpečený komunikační kanál bez nutnosti konfigurovat síťovou infrastrukturu.  Mezi tyto konektory patří:

|  |  |  |  |
| --- | --- | --- | --- |
| [![Ikona rozhraní API][db2icon]<br/>**DB2**][db2doc] | [![Ikona rozhraní API][oracle-DB-icon]<br/>**Oracle DB**][oracle-db-doc] | [![Ikona rozhraní API][sharepointicon]<br/>**SharePoint</br>Server**][sharepointserver] | [![Ikona rozhraní API][filesystem-icon]<br/>**Systém</br>Souborů**][filesystemdoc] |
[![Ikona rozhraní API][sql-servericon]<br/>**SQL</br>Server**][sql-serverdoc] | ![Ikona rozhraní API][Biztalk-Servericon]<br/>**BizTalk</br>Server**| |

### <a name="integration-account-connectors"></a>Konektory účtu pro integraci 

Enterprise Integration Pack (EIP) obsahuje konektory, které jsou dobře známé komunitě BizTalk Serveru. Po zakoupení [účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) obdržíte také následující konektory: 

|  |  |  |  |
| --- | --- | --- | --- |
| [![Ikona rozhraní API][as2icon]<br/>**Dekódování</br>AS2**][as2decode] | [![Ikona rozhraní API][as2icon]<br/>**Kódování</br>AS2**][as2encode] | [![Ikona rozhraní API][x12icon]<br/>**Dekódování</br>EDIFACT**][EDIFACTdecode] | [![Ikona rozhraní API][x12icon]<br/>**Kódování</br>EDIFACT**][EDIFACTencode] |
[![Ikona rozhraní API][flatfileicon]<br/>**Kódování</br>plochého souboru**][flatfiledoc] | [![Ikona rozhraní API][flatfiledecodeicon]<br/>**Dekódování</br>plochého souboru**][flatfiledecodedoc] | [![Ikona rozhraní API][integrationaccounticon]<br/>**Účet<br/>integrace**][integrationaccountdoc] | [![Ikona rozhraní API][xmltransformicon]<br/>**Transformace<br/>XML**][xmltransformdoc] |
| [![Ikona rozhraní API][x12icon]<br/>**Dekódování</br>X12**][x12decode] | [![Ikona rozhraní API][x12icon]<br/>**Kódování</br>X12**][x12encode] | [![Ikona rozhraní API][xmlvalidateicon]<br/>**Ověření<br/>XML**][xmlvalidatedoc] | [![Ikona rozhraní API][liquidicon]<br/>**Transformace <br/>JSON**][JSONliquidtransformdoc] |

### <a name="enterprise-connectors"></a>Podnikové konektory

Připojte s podnikovým aplikacím v rámci vašich aplikací logiky.

|  |  |
| --- | --- |
|[![Ikona rozhraní API][MQicon]<br/>**MQ**][mqdoc]|[![Ikona rozhraní API][SAPicon]<br/>**SAP**][sapconnector]|

> [!TIP]
> Pokud chcete začít s Azure Logic Apps, ještě než si zaregistrujete účet Azure, přejděte k [možnosti vyzkoušet si Logic Apps](https://tryappservice.azure.com/?appservice=logic). Ihned si můžete vytvořit krátkodobou úvodní aplikaci logiky. Není vyžadována platební karta a nevzniká žádný závazek.

## <a name="connectors-as-triggers-and-actions"></a>Konektory jako triggery a akce

**Trigger** spouští instanci aplikace logiky. Některé konektory poskytují triggery, které vaší aplikaci odesílají upozornění na konkrétní události. Konektor FTP má například trigger `OnUpdatedFile`, který spustí vaši aplikaci při aktualizaci souboru. 

Aplikace logiky obsahují následující typy triggerů:  

* *Triggery dotazů:* Tyto triggery odesílají službě v zadaných intervalech dotazy a kontrolují jejich pomocí dostupnost nových dat. 

    Když jsou k dispozici nová data, spustí se nová instance aplikace logiky, která použije tato data jako vstup. 

* *Triggery nabízených oznámení:* Tyto triggery naslouchají datům v koncovém bodě nebo čekají na výskyt určité události a poté aktivují novou instanci vaší aplikace logiky.

* *Trigger opakování:* Tento trigger vytvoří instanci aplikace logiky podle předepsaného plánu.

Konektory také poskytují **akce**, které můžete použít ve vašem pracovním postupu. Vaše aplikace logiky například může vyhledávat data pro pozdější použití. Přesněji řečeno můžete vyhledat data o zákaznících z databáze SQL a pak tato data o zákaznících použít k sestavení pracovního postupu. 

> [!TIP]
> Další podrobnosti o triggerech a akcích najdete v tématu [Přehled konektorů](connectors-overview.md). 


## <a name="message-manipulation-actions"></a>Akce pro manipulaci se zprávami

Aplikace logiky zahrnují integrované akce, které můžou měnit datové části nebo s nimi manipulovat. Integrovaný konektor **Operace s daty** obsahuje následující akce: 

| | |
|---|---|
| **Vytvoření** | Sestaví nebo vygeneruje hodnoty nebo objekty pro pozdější použití nebo pro použití při sestavování pracovního postupu. Můžete například vytvořit objekt JSON s hodnotami získanými z několika kroků nebo vypočítat konstantu pro pozdější použití ve spuštěné aplikaci logiky. |
| **Vytvoření tabulky CSV**<br/>**Vytvoření tabulky HTML** | Vytvoří ze sedy výsledků tabulku CSV nebo HTML. Například přidáním akce CRM „Výpis záznamů“ a filtru můžete získat záznamy přidané dnes. Potom výsledky odešlete v e-mailu jako tabulku HTML. |
| **Pole filtru** (dotaz) | Umožňuje filtrovat sadu výsledků pro získání položek, které vás zajímají. Můžete například vyhledat všechny tweety se značkou `#Azure` a vrácené tweety pak filtrovat, aby se zobrazily jenom tweety s hodnotou `Tweeted_by_followers > 50`. |
| **Spojení** | Spojí pole s použitím nějakého oddělovače. Například operace Detekce klíčových frází vrací pole klíčových frází. Můžete je „spojit“ s použitím oddělovače `,` nebo podobného. Takže místo `["Some", "Phrase"]` budete mít `"Some, Phrase"`. |
| **Parsování formátu JSON** | Pomocí parsování získá hodnoty z objektu JSON a zpřístupní je v návrháři. Například pokud vaše funkce Azure vrací datovou část JSON, můžete ji parsovat, abyste později v dalším kroku měli přístup k vlastnostem JSON. Tato akce při spuštění také ověří, že formát JSON odpovídá zadanému schématu. | 
| **Výběr** | Vybere určité vlastnosti pole pro další zpracování. Pokud provedete výpis záznamů v SQL a vrátí se 15 sloupců, můžete pro další zpracování vybrat jenom některé z těchto sloupců. Výstupem je pole, které obsahuje pouze vlastnosti, které vyberete. |

## <a name="custom-connectors-and-azure-certification"></a>Vlastní konektory a certifikace Azure 

Pokud chcete volat rozhraní API, která spouští vlastní kód nebo nejsou k dispozici jako konektory, můžete rozšířit platformu Logic Apps [vytvořením funkcí API Apps založených na REST](../logic-apps/logic-apps-create-api-app.md). Můžete vytvořit také [vlastní konektory](../logic-apps/custom-connector-overview.md), které je možné zpřístupnit pro všechny aplikace logiky ve vašem předplatném.

Pokud chcete, aby byly vaše vlastní funkce API Apps veřejné a k dispozici pro použití v Azure, můžete [své konektory odeslat k ověření Microsoftem](../logic-apps/custom-connector-submit-certification.md).

## <a name="get-help"></a>Podpora

Klást otázky, odpovídat na ně a poučit se ze zkušeností jiných uživatelů Azure Logic Apps můžete ve [fóru Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Pokud chcete pomoci při vylepšování Azure Logic Apps a konektorů, hlasujte nebo zanechte své nápady na [webu zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

Chybí tu nějaké téma věnované konektorům nebo podrobnosti, které považuje za důležité? Pokud ano, rozšiřte naše stávající témata nebo přidejte nové a pomozte nám. Naše dokumentace je typu open source a je hostovaná v GitHubu. Začněte v našem [úložišti GitHub](https://github.com/Microsoft/azure-docs). 

## <a name="next-steps"></a>Další kroky
* [Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Vytvoření vlastních rozhraní API pro aplikace logiky](../logic-apps/logic-apps-create-api-app.md)
* [Monitorování aplikací logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md)

<!--Connectors Documentation-->

[gatewaydoc]: ../logic-apps/logic-apps-gateway-connection.md "Připojení k místním zdrojům dat z aplikací logiky pomocí místní brány dat"
[api/web-appdoc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integrace aplikací logiky s funkcí App Service API Apps"
[azureblobstoragedoc]: ./connectors-create-api-azureblobstorage.md "Správa souborů v kontejneru objektů blob pomocí konektoru služby Blob Storage"
[azure-functionsdoc]: ../logic-apps/logic-apps-azure-functions.md "Integrace aplikací logiky se službou Azure Functions"
[db2doc]: ./connectors-create-api-db2.md "Připojení k IBM DB2 v cloudu nebo místním prostředí. Aktualizace řádku, získání tabulky a provádění dalších akcí"
[Dynamics-365doc]: ./connectors-create-api-crmonline.md "Připojení k aplikaci Dynamics CRM Online, abyste mohli pracovat s daty aplikace CRM Online"
[event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Připojení k Azure Event Hubs. Příjem a odesílání událostí mezi aplikacemi logiky a Event Hubs"
[filesystemdoc]: ../logic-apps/logic-apps-using-file-connector.md "Připojení k místnímu systému souborů"
[ftpdoc]: ./connectors-create-api-ftp.md "Připojení k serverům FTP/FTPS a provádění úloh protokolu FTP, jako je odesílání, získávání a odstraňování souborů a provádění dalších akcí"
[httpdoc]: ./connectors-native-http.md "Provádění volání HTTP pomocí konektoru HTTP"
[http-requestdoc]: ./connectors-native-reqres.md "Přidání akcí pro požadavky a odpovědi HTTP do aplikací logiky"
[http-swaggerdoc]: ./connectors-native-http-swagger.md "Provádění volání HTTP pomocí konektoru HTTP + Swagger"
[informixdoc]: ./connectors-create-api-informix.md "Připojení k Informix v cloudu nebo místním prostředí. Čtení řádku, výpis tabulek a provádění dalších akcí"
[nested-logic-appdoc]: ../logic-apps/logic-apps-http-endpoint.md "Integrace aplikací logiky s vnořenými pracovními postupy"
[office365-outlookdoc]: ./connectors-create-api-office365-outlook.md "Připojení k účtu Office 365. Odesílání a příjem e-mailů, správa kalendáře a kontaktů a provádění dalších akcí"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Připojení k databázi Oracle a možnost přidávat, vkládat a odstraňovat řádky a další"
[mqdoc]: ./connectors-create-api-mq.md "Připojení k MQ místně nebo v Azure a odesílání a příjem zpráv"
[recurrencedoc]:  ./connectors-native-recurrence.md "Aktivace opakujících se akcí pro aplikace logiky"
[salesforcedoc]: ./connectors-create-api-salesforce.md "Připojení k účtu Salesforce. Správa účtů, zájemců, příležitostí a provádění dalších akcí"
[sapconnector]: ../logic-apps/logic-apps-using-sap-connector.md "Připojení k místnímu systému SAP"
[service-busdoc]: ./connectors-create-api-servicebus.md "Odesílání zpráv z front a témat služby Service Bus a příjem zpráv z front a odběrů služby Service Bus"
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "Připojení k SharePointu Online. Správa dokumentů, vypisování položek a provádění dalších akcí"
[sharepointserver]: ./connectors-create-api-sharepointserver.md "Přípojení k místnímu serveru SharePoint. Správa dokumentů, vypisování položek a provádění dalších akcí"
[sql-serverdoc]: ./connectors-create-api-sqlazure.md "Připojení ke službě Azure SQL Database nebo serveru SQL. Vytváření, aktualizace, získávání a odstraňování položek v tabulce databáze SQL."
[twitterdoc]: ./connectors-create-api-twitter.md "Připojení k Twitteru. Získávání časových os, odesílání tweetů a provádění dalších akcí"
[webhookdoc]: ./connectors-native-webhook.md "Přidávání akcí a aktivačních událostí webhooků do aplikací logiky"

[as2doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Přečtěte si víc o podnikové integraci AS2."
[x12doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Přečtěte si víc o podnikové integraci X12."
[flatfiledoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Přečtěte si víc o plochém souboru podnikové integrace."
[flatfiledecodedoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Přečtěte si víc o plochém souboru podnikové integrace."
[xmlvalidatedoc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Přečtěte si víc o validaci XML podnikové integrace."
[xmltransformdoc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Přečtěte si víc o transformacích podnikové integrace."
[as2decode]: ../logic-apps/logic-apps-enterprise-integration-as2-decode.md "Přečtěte si víc o dekódování pro podnikovou integraci AS2."
[as2encode]:../logic-apps/logic-apps-enterprise-integration-as2-encode.md "Přečtěte si víc o kódování pro podnikovou integraci AS2."
[X12decode]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Přečtěte si víc o dekódování pro podnikovou integraci X12."
[X12encode]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Přečtěte si víc o kódování pro podnikovou integraci X12."
[EDIFACTdecode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Přečtěte si víc o dekódování pro podnikovou integraci EDIFACT."
[EDIFACTencode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Přečtěte si víc o kódování pro podnikovou integraci EDIFACT."
[integrationaccountdoc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Vyhledání schémat, map, partnerů a dalších v účtu pro integraci"
[JSONliquidtransformdoc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Další informace o transformacích JSON pomocí Liquidu"


[boxDoc]: ./connectors-create-api-box.md "Připojení ke službě Box. Nahrávání, získávání, odstraňování, vypisování souborů a provádění dalších akcí"
[delaydoc]: ./connectors-native-delay.md "Provádění zpožděných akcí"
[dropboxdoc]: ./connectors-create-api-dropbox.md "Připojení k Dropboxu. Nahrávání, získávání, odstraňování, vypisování souborů a provádění dalších akcí"
[facebookdoc]: ./connectors-create-api-facebook.md "Připojení k Facebooku. Přidávání příspěvků na Timeline, získání kanálu stránky a provádění dalších akcí"
[githubdoc]: ./connectors-create-api-github.md "Připojení ke GitHubu a sledování problémů"
[google-drivedoc]: ./connectors-create-api-googledrive.md "Připojení k Disku Google a práce s daty"
[google-sheetsdoc]: ./connectors-create-api-googlesheet.md "Připojení k Tabulkám Google a upravování tabulek"
[google-tasksdoc]: ./connectors-create-api-googletasks.md "Připojení k Úkolům Google a správa úkolů"
[google-calendardoc]: ./connectors-create-api-googlecalendar.md "Připojí se ke Kalendáři Google a může kalendář spravovat."
[http-responsedoc]: ./connectors-native-reqres.md "Přidání akcí pro požadavky a odpovědi HTTP do aplikací logiky"
[instagramdoc]: ./connectors-create-api-instagram.md "Připojení k Instagramu. Aktivování nebo reakce na události"
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "Připojení k účtu MailChimp. Správa a automatizace e-mailů"
[mandrilldoc]: ./connectors-create-api-mandrill.md "Připojení k Mandrillu pro komunikaci"
[microsoft-translatordoc]: ./connectors-create-api-microsofttranslator.md "Připojení ke službě Microsoft Translator. Překlad textu, zjišťování jazyků a provádění dalších akcí" 
[office365-usersdoc]: ./connectors-create-api-office365-users.md 
[office365-videodoc]: ./connectors-create-api-office365-video.md "Získání informací o videu, seznamů a kanálů videí a adres URL pro přehrávání videí Office 365"
[onedrivedoc]: ./connectors-create-api-onedrive.md "Připojení k osobnímu Microsoft OneDrivu. Nahrávání, odstraňování, vypisování souborů a provádění dalších akcí"
[onedrive-for-businessdoc]: ./connectors-create-api-onedriveforbusiness.md "Připojení k firemnímu Microsoft OneDrivu. Nahrávání, odstraňování, vypisování souborů a provádění dalších akcí"
[outlook.comdoc]: ./connectors-create-api-outlook.md "Připojení k poštovní schránce aplikace Outlook. Správa e-mailů, kalendářů, kontaktů a provádění dalších akcí"
[project-onlinedoc]: ./connectors-create-api-projectonline.md "Připojení k aplikaci Microsoft Project Online. Správa projektů, úloh, prostředků a provádění dalších akcí"
[querydoc]: ./connectors-native-query.md "Výběr a filtrování polí pomocí akce dotazu"
[rssdoc]: ./connectors-create-api-rss.md "Publikování a načítání položek informačních kanálů a aktivace operací při publikování nové položky v informačním kanálu RSS."
[sendgriddoc]: ./connectors-create-api-sendgrid.md "Připojení k SendGridu. Odesílání e-mailů a správa seznamů příjemců"
[sftpdoc]: ./connectors-create-api-sftp.md "Připojení k účtu SFTP. Nahrávání, získávání, odstraňování souborů a provádění dalších akcí"
[slackdoc]: ./connectors-create-api-slack.md "Připojení ke Slacku a odesílání zpráv do kanálů Slacku."
[smtpdoc]: ./connectors-create-api-smtp.md "Připojení k serveru SMTP a odesílání e-mailů s přílohami"
[sparkpostdoc]: ./connectors-create-api-sparkpost.md "Připojení ke SparkPostu pro komunikaci"
[trellodoc]: ./connectors-create-api-trello.md "Připojení k Trello. Správa projektů a organizace čehokoli s kýmkoli"
[twiliodoc]: ./connectors-create-api-twilio.md "Připojení k Twiliu. Odesílání a získávání zpráv, získávání dostupných čísel, správa příchozích hovorů z telefonních čísel a provádění dalších akcí"
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "Připojení k Wunderlistu. Správa úkolů a seznamů úkolů, udržování synchronizace všeho, co potřebujete k životu, a provádění dalších akcí"
[yammerdoc]: ./connectors-create-api-yammer.md "Připojení k Yammeru. Odesílání zpráv, získávání nových zpráv a provádění dalších akcí"
[youtubedoc]: ./connectors-create-api-youtube.md "Připojení k Youtube. Správa videí a kanálů"


<!--Icon references-->
[appFiguresicon]: ./media/apis-list/appfigures.png
[AppServices-icon]: ./media/apis-list/AppServices.png
[Asanaicon]: ./media/apis-list/asana.png
[Azure-Automation-icon]: ./media/apis-list/azure-automation.png
[AzureBlobStorageicon]: ./media/apis-list/azureblob.png
[Azure-Data-Lake-icon]: ./media/apis-list/azure-data-lake.png
[Azure-MLicon]: ./media/apis-list/azureml.png
[Azure-Resource-Manager-icon]: ./media/apis-list/azure-resource-manager.png
[Azure-Queues-icon]: ./media/apis-list/azure-queues.png
[Basecamp-3icon]: ./media/apis-list/basecamp.png
[Bitbucket-icon]: ./media/apis-list/bitbucket.png
[Bitlyicon]: ./media/apis-list/bitly.png
[BizTalk-Servericon]: ./media/apis-list/biztalk.png
[Bloggericon]: ./media/apis-list/blogger.png
[Boxicon]: ./media/apis-list/box.png
[Campfireicon]: ./media/apis-list/campfire.png
[Cognitive-Services-Text-Analyticsicon]: ./media/apis-list/cognitiveservicestextanalytics.png
[DB2icon]: ./media/apis-list/db2.png
[Dropboxicon]: ./media/apis-list/dropbox.png
[Dynamics-365icon]: ./media/apis-list/dynamicscrmonline.png
[Dynamics-365-for-Financialsicon]: ./media/apis-list/madeira.png
[Dynamics-365-for-Operationsicon]: ./media/apis-list/dynamicsax.png
[Easy-Redmineicon]: ./media/apis-list/easyredmine.png
[Event-Hubs-icon]: ./media/apis-list/eventhubs.png
[Facebookicon]: ./media/apis-list/facebook.png
[FileSystem-icon]: ./media/apis-list/filesystem.png
[FileSystemIcon]: ./media/apis-list/filesystem.png
[FTPicon]: ./media/apis-list/ftp.png
[GitHubicon]: ./media/apis-list/github.png
[Google-Calendaricon]: ./media/apis-list/googlecalendar.png
[Google-Driveicon]: ./media/apis-list/googledrive.png
[Google-Sheetsicon]: ./media/apis-list/googlesheet.png
[Google-Tasksicon]: ./media/apis-list/googletasks.png
[HideKeyicon]: ./media/apis-list/hidekey.png
[HipChaticon]: ./media/apis-list/hipchat.png
[Informixicon]: ./media/apis-list/informix.png
[Insightlyicon]: ./media/apis-list/insightly.png
[Instagramicon]: ./media/apis-list/instagram.png
[Instapapericon]: ./media/apis-list/instapaper.png
[JIRAicon]: ./media/apis-list/jira.png
[MailChimpicon]: ./media/apis-list/mailchimp.png
[Mandrillicon]: ./media/apis-list/mandrill.png
[Microsoft-Translatoricon]: ./media/apis-list/microsofttranslator.png
[MQicon]: ./media/apis-list/mq.png
[Office-365-Outlookicon]: ./media/apis-list/office365.png
[Office-365-Usersicon]: ./media/apis-list/office365users.png
[Office-365-Videoicon]: ./media/apis-list/office365video.png
[OneDriveicon]: ./media/apis-list/onedrive.png
[OneDrive-for-Businessicon]: ./media/apis-list/onedriveforbusiness.png
[Oracle-DB-icon]: ./media/apis-list/oracle-db.png
[Outlook.comicon]: ./media/apis-list/outlook.png
[PagerDutyicon]: ./media/apis-list/pagerduty.png
[Pinteresticon]: ./media/apis-list/pinterest.png
[Project-Onlineicon]: ./media/apis-list/projectonline.png
[Redmineicon]: ./media/apis-list/redmine.png
[RSSicon]: ./media/apis-list/rss.png
[Common-Data-Serviceicon]: ./media/apis-list/runtimeservice.png
[Salesforceicon]: ./media/apis-list/salesforce.png
[SAPicon]: ./media/apis-list/sap.png
[SendGridicon]: ./media/apis-list/sendgrid.png
[Service-Busicon]: ./media/apis-list/servicebus.png
[SFTPicon]: ./media/apis-list/sftp.png
[SharePointicon]: ./media/apis-list/sharepointonline.png
[Slackicon]: ./media/apis-list/slack.png
[Smartsheeticon]: ./media/apis-list/smartsheet.png
[SMTPicon]: ./media/apis-list/smtp.png
[SparkPosticon]: ./media/apis-list/sparkpost.png
[SQL-Servericon]: ./media/apis-list/sql.png
[Todoisticon]: ./media/apis-list/todoist.png
[Trelloicon]: ./media/apis-list/trello.png
[Twilioicon]: ./media/apis-list/twilio.png
[Twittericon]: ./media/apis-list/twitter.png
[Vimeoicon]: ./media/apis-list/vimeo.png
[Visual-Studio-Team-Servicesicon]: ./media/apis-list/visualstudioteamservices.png
[WordPressicon]: ./media/apis-list/wordpress.png
[Wunderlisticon]: ./media/apis-list/wunderlist.png
[Yammericon]: ./media/apis-list/yammer.png
[YouTubeicon]: ./media/apis-list/youtube.png

<!-- Primitive Icons -->
[API/Web-Appicon]: ./media/apis-list/appservices.png
[Azure-Functionsicon]: ./media/apis-list/function.png
[CallLogicApp-icon]: ./media/apis-list/calllogicapp.png
[Delayicon]: ./media/apis-list/delay.png
[HTTPicon]: ./media/apis-list/http.png
[HTTP-Requesticon]: ./media/apis-list/request.png
[HTTP-Responseicon]: ./media/apis-list/response.png
[HTTP-Swaggericon]: ./media/apis-list/http_swagger.png
[Nested-Logic-Appicon]: ./media/apis-list/workflow.png
[Recurrenceicon]: ./media/apis-list/recurrence.png
[Queryicon]: ./media/apis-list/query.png
[Webhookicon]: ./media/apis-list/webhook.png

<!-- EIP Icons -->
[as2icon]: ./media/apis-list/as2.png
[x12icon]: ./media/apis-list/x12new.png
[flatfileicon]: ./media/apis-list/flatfileencoding.png
[flatfiledecodeicon]: ./media/apis-list/flatfiledecoding.png
[xmlvalidateicon]: ./media/apis-list/xmlvalidation.png
[xmltransformicon]: ./media/apis-list/xsltransform.png
[integrationaccounticon]: ./media/apis-list/integrationaccount.png
[liquidicon]: ./media/apis-list/liquidtransform.png
