---
title: Migrace aplikací z BizTalk služeb do Azure Logic Apps
description: Jak přesunout aplikace a řešení z Microsoft Azure BizTalk Services (MABS) do Azure Logic Apps
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/30/2017
ms.openlocfilehash: 97399635399c12022006ac95e60c5828bf2a9dc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905435"
---
# <a name="migrate-your-apps-and-solutions-from-biztalk-services-to-azure-logic-apps"></a>Migrace aplikací a řešení z BizTalk služeb do Azure Logic Apps

Služby Microsoft Azure BizTalk Services (MABS) odcházejí do důchodu. Pokud chcete přesunout řešení integrace MABS do [Azure Logic Apps](../logic-apps/logic-apps-overview.md), postupujte podle pokynů v tomto článku. 

## <a name="introduction"></a>Úvod

BizTalk služby se skládá ze dvou dílčích služeb:

* Hybridní připojení služeb BizTalk
* Integrace s Mostem EAI a EDI

[Hybridní připojení služby Azure App Service](../app-service/app-service-hybrid-connections.md) nahrazuje hybridní připojení služby BizTalk. Hybridní připojení Azure jsou dostupná ve službě Azure App Service prostřednictvím portálu Azure. Tato služba poskytuje správce hybridního připojení, takže můžete spravovat existující hybridní připojení BizTalk Services a také nová hybridní připojení, která vytvoříte na portálu. 

[Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje integraci na základě mostu EAI a EDI se všemi stejnými funkcemi v BizTalk službách a dalšími funkcemi. Tato služba poskytuje funkce pracovního postupu a orchestrace založené na cloudovém měřítku, abyste rychle a snadno vytvořili komplexní integrační řešení prostřednictvím prohlížeče nebo sady Visual Studio.

Tato tabulka mapuje možnosti BizTalk služeb na logice aplikace.

| BizTalk služby   | Logic Apps            | Účel                      |
| ------------------ | --------------------- | ---------------------------- |
| Konektor          | Konektor             | Odesílání a přijímání dat   |
| Bridge             | Aplikace logiky             | Procesor potrubí           |
| Ověřit fázi     | Akce ověření XML | Ověření dokumentu XML proti schématu | 
| Obohatit fázi       | Datové tokeny           | Povýšení vlastností na zprávy nebo pro rozhodnutí o směrování |
| Fáze transformace    | Akce Transformace      | Převod zpráv XML z jednoho formátu do jiného |
| Dekódovat fázi       | Akce Dekódování plochého souboru | Převést z plochého souboru na XML |
| Fáze kódování       | Akce Zakódování plochého souboru | Převést z XML na plochý soubor |
| Inspektor zprávy  | Funkce Azure nebo aplikace API | Spuštění vlastního kódu v integracích |
| Akce trasy       | Stav nebo přepínač | Směrovat zprávy do jedné ze zadaných spojnic |
|||| 

## <a name="biztalk-services-artifacts"></a>Artefakty BizTalk Services

BizTalk Services má několik druhů artefaktů. 

## <a name="connectors"></a>Konektory

Konektory Služby BizTalk pomáhají mostům odesílat a přijímat data, včetně obousměrných mostů, které umožňují interakce požadavků a odpovědí založených na protokolu HTTP. Logic Apps používá stejnou terminologii a má stovky konektorů, které slouží stejnému účelu připojením k široké škále technologií a služeb. Konektory jsou například k dispozici pro cloudové služby SaaS a PaaS, jako je OneDrive, Office365, Dynamics CRM a další, plus místní systémy prostřednictvím místní brány dat, která nahrazuje službu BizTalk adapter service pro BizTalk služby. Zdroje v BizTalk služby jsou omezeny na FTP, SFTP a service bus fronty nebo téma předplatného.

![](media/logic-apps-move-from-mabs/sources.png)

Ve výchozím nastavení má každý most koncový bod HTTP, který je nakonfigurován s runtime adresou a vlastnostmi relativní adresy pro most. Chcete-li dosáhnout stejných výsledků s Logic Apps, použijte [požadavky a odpovědi](../connectors/connectors-native-reqres.md) akce.

## <a name="xml-processing-and-bridges"></a>Zpracování XML a mosty

V BizTalk services je most analogický jako kanál zpracování. Most může přijímat data přijatá z konektoru, provést nějakou práci s daty a odeslat výsledky do jiného systému. Logic Apps dělá totéž tím, že podporuje stejné vzory interakce založené na kanálu jako BizTalk služby a také poskytuje další vzory integrace. [Přemostění požadavku a odpovědi XML](https://msdn.microsoft.com/library/azure/hh689781.aspx) ve službách BizTalk se označuje jako kanál VETER, který se skládá z fází, které provádějí tyto úkoly:

* (V) Ověřit
* (E) Obohatit
* (T) Transformace
* (E) Obohatit
* (R) Trasa

Tento obrázek ukazuje, jak je zpracování rozděleno mezi požadavek a odpověď, což poskytuje kontrolu nad požadavkem a cestami odpovědi samostatně, například pomocí různých map pro každou cestu:

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

Jednosměrný most XML také přidá fáze Dekódování a Kódování na začátku a na konci zpracování. Průchozí most obsahuje jednu fázi Enrich.

### <a name="message-processing-decoding-and-encoding"></a>Zpracování zpráv, dekódování a kódování

V BizTalk služby, můžete přijímat různé typy zpráv XML a určit odpovídající schéma pro přijaté zprávy. Tato práce se provádí ve fázi *Typy zpráv* kanálu zpracování příjmu. Fáze Dekódování pak používá zjištěný typ zprávy k dekódování zprávy pomocí zadaného schématu. Pokud je schéma schéma plochého souboru, tato fáze převede příchozí plochý soubor na XML. 

Logic Apps poskytuje podobné funkce. Obdržíte plochý soubor přes různé protokoly pomocí různých aktivačních událostí konektoru (systém souborů, FTP, HTTP a tak dále) a pomocí akce [Dekódování plochého souboru](../logic-apps/logic-apps-enterprise-integration-flatfile.md) převedete příchozí data na XML. Existující schémata plochých souborů můžete přesunout přímo do logic ových aplikací beze změn a potom nahrát schémata do účtu integrace.

### <a name="validation"></a>Ověřování

Po převodu příchozích dat do xml (nebo pokud byl přijatý formát zprávy XML), se spustí ověření, které určuje, zda zpráva dodržuje schéma XSD. Chcete-li tuto úlohu provést v logic apps, použijte akci [ověření XML.](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) Můžete použít stejná schémata z BizTalk služby bez jakýchkoli změn.

### <a name="transform-messages"></a>Transformace zpráv

Ve službě BizTalk převede fáze Transformace jeden formát zpráv založený na XML na jiný. Tato práce se provádí použitím mapy pomocí mapovače založeného na TRFM. V Logic Apps proces je podobný. Akce Transformace provede mapu z vašeho účtu integrace. Hlavní rozdíl je, že mapy v Logic Apps jsou ve formátu XSLT. XSLT zahrnuje možnost znovu použít stávající XSLT, které již máte, včetně map vytvořených pro BizTalk Server, které obsahují functoids. 

### <a name="routing-rules"></a>Pravidla směrování

BizTalk Services rozhoduje o tom, který koncový bod nebo konektor odesílá příchozí zprávy nebo data. Možnost výběru z předkonfigurovaných koncových bodů je možná pomocí možnosti filtru směrování:

![](media/logic-apps-move-from-mabs/route-filter.png)

V BizTalk služby, pokud existují pouze dvě možnosti, pomocí *podmínku* je nejlepší způsob, jak převést směrování filtrů v BizTalk služby. Pokud jich je více než dva, použijte **přepínač**.

Logic Apps poskytuje sofistikované logické funkce plus pokročilé řízení toku a směrování s [podmíněné příkazy](../logic-apps/logic-apps-control-flow-conditional-statement.md) a [switch příkazy](../logic-apps/logic-apps-control-flow-switch-statement.md).

### <a name="enrich"></a>Obohatit

Ve zpracování BizTalk Services fáze Enrich přidá vlastnosti do kontextu zprávy přidruženého k přijatým datům. Například povýšení vlastnosti pro směrování z vyhledávání databáze nebo extrahování hodnoty pomocí výrazu XPath. Logic Apps poskytuje přístup ke všem výstupům kontextových dat z předchozích akcí, takže je jednoduché replikovat stejné chování. Například pomocí `Get Row` akce připojení SQL vrátíte data z databáze serveru SQL Server a použijete data v akci Rozhodnutí pro směrování. Podobně vlastnosti příchozí chod service bus ve frontě zpráv aktivační události jsou adresovatelné, stejně jako XPath pomocí výrazu jazyka definice pracovního postupu xpath.

### <a name="run-custom-code"></a>Spustit vlastní kód

BizTalk Služby umožňuje [spustit vlastní kód,](https://msdn.microsoft.com/library/azure/dn232389.aspx) který je odeslán ve vlastních sestaveních. Tato funkce je implementována rozhraním [IMessageInspector.](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector) Každá fáze v mostě obsahuje dvě vlastnosti (Inspektor při vstupu a Inspektor při ukončení), které poskytují typ .NET, který jste vytvořili a který implementuje toto rozhraní. Vlastní kód umožňuje provádět složitější zpracování dat a umožňuje znovu použít existující kód v sestaveních, které provádějí společnou obchodní logiku. 

Logic Apps poskytuje dva primární způsoby spuštění vlastního kódu: Azure Functions a API Apps. Funkce Azure lze vytvořit a volat z aplikací logiky. Viz [Přidání a spuštění vlastního kódu pro aplikace logiky prostřednictvím Azure Functions](../logic-apps/logic-apps-azure-functions.md). Pomocí aplikací API, které jsou součástí služby Azure App Service, můžete vytvořit vlastní aktivační události a akce. Přečtěte si další informace o [vytváření vlastního rozhraní API pro použití s Logic Apps](../logic-apps/logic-apps-create-api-app.md). 

Pokud máte vlastní kód v sestaveních, které voláte z BizTalk služeb, můžete tento kód přesunout do Funkce Azure nebo vytvořit vlastní rozhraní API s aplikacemi rozhraní API, v závislosti na tom, co implementujete. Například pokud máte kód, který zabalí jinou službu, pro kterou Logic Apps nemá konektor, pak vytvořte aplikaci rozhraní API a použijte akce, které vaše aplikace rozhraní API poskytuje v rámci aplikace logiky. Pokud máte pomocné funkce nebo knihovny, pak Azure Functions je pravděpodobně nejvhodnější.

### <a name="edi-processing-and-trading-partner-management"></a>Edi zpracování a řízení obchodního partnera

BizTalk služby a logické aplikace zahrnují edi a b2b zpracování s podporou AS2 (prohlášení o použitelnosti 2), X12 a EDIFACT. V BizTalk Služby, vaše vytvářet EDI mosty a vytvářet nebo spravovat obchodní partnery a dohody ve vyhrazeném sledování a management portálu.
V logic Apps získáte tuto funkci prostřednictvím [sady Enterprise Integration Pack (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md). EIP poskytuje [integrační účet](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a b2B akce pro edi a b2b zpracování. Pomocí integračního účtu můžete také vytvářet a spravovat [obchodní partnery](../logic-apps/logic-apps-enterprise-integration-partners.md) a [smlouvy](../logic-apps/logic-apps-enterprise-integration-agreements.md). Po vytvoření účtu integrace můžete s účtem propojit jednu nebo více aplikací logiky. Potom můžete použít akce B2B pro přístup k informacím obchodního partnera z aplikace logiky. Jsou poskytovány následující akce:

* Kódování AS2
* DEKÓDOVÁNÍ AS2
* Kódování X12
* X12 Dekódování
* Edifact kódování
* Dekódovat EDIFACT

Na rozdíl od BizTalk services jsou tyto akce odděleny od přenosových protokolů. Takže při vytváření aplikací logiky máte větší flexibilitu, ve kterých konektory, které používáte k odesílání a přijímání dat. Například můžete přijímat soubory X12 jako přílohy z e-mailu a pak tyto soubory zpracovat v aplikaci logiky. 

## <a name="manage-and-monitor"></a>Správa a monitorování

V BizTalk Services, vyhrazený portál poskytuje možnosti sledování pro sledování a řešení problémů. Logic Apps poskytuje bohatší funkce sledování a monitorování pro [monitorování aplikací logiky na webu Azure Portal](../logic-apps/monitor-logic-apps.md)a obsahuje mobilní aplikaci, která sleduje věci, když jste na cestách.

## <a name="high-availability"></a>Vysoká dostupnost

Pro vysokou dostupnost (HA) v BizTalk services můžete sdílet zatížení zpracování pomocí více než jedné instance v určité oblasti. Logic Apps poskytuje v oblasti HA bez dalších poplatků. 

V BizTalk Services, zotavení po havárii mimo oblast pro zpracování B2B vyžaduje proces zálohování a obnovení. Pro kontinuitu podnikání logic apps poskytuje [aktivní/pasivní zotavení po havárii](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)mezi oblastmi , která umožňuje synchronizovat b2B data mezi integračními účty v různých oblastech.

## <a name="next-steps"></a>Další kroky

* [Co je Logic Apps?](../logic-apps/logic-apps-overview.md)
* [Vytvořte první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) nebo můžete rychle začít pomocí [předem připravené šablony](../logic-apps/logic-apps-create-logic-apps-from-templates.md).  
* [Zobrazení všech dostupných konektorů,](../connectors/apis-list.md) které můžete použít v aplikacích logiky