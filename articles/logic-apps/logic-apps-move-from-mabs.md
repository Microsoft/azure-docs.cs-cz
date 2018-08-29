---
title: Přesun aplikací do Azure Logic Apps ze služby BizTalk Services | Dokumentace Microsoftu
description: Migrace ze služby Azure BizTalk Services (MABS) na Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 05/30/2017
ms.openlocfilehash: f27e82e780917e00625ef6a14ab8317d1f5b8ae8
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124795"
---
# <a name="migrate-from-biztalk-services-to-azure-logic-apps"></a>Migrace ze služby BizTalk Services na Azure Logic Apps

Microsoft Azure BizTalk Services (MABS) je vyřazení z provozu. Přesunutí řešení integrace MABS [Azure Logic Apps](../logic-apps/logic-apps-overview.md), postupujte podle pokynů v tomto článku. 

## <a name="introduction"></a>Úvod

BizTalk Services se skládá ze dvou subservices:

* Microsoft BizTalk Services – hybridní připojení
* EAI a EDI založenou na mostu integrace

[Azure App Service Hybrid Connections](../app-service/app-service-hybrid-connections.md) nahradí hybridní připojení BizTalk Services. Hybridní připojení Azure je dostupná pomocí služby Azure App Service na webu Azure portal. Tato služba poskytuje správce hybridního připojení, aby mohl spravovat existující hybridní připojení BizTalk Services a také nové hybridní připojení, které vytvoříte na portálu. 

[Logic Apps](../logic-apps/logic-apps-overview.md) nahradí EAI a EDI založenou na mostu integrace se všemi funkcemi, které jsou stejné v BizTalk Services a další. Tato služba poskytuje v cloudu škálovatelný založenou na skutečné spotřebě pracovních postupů a Orchestrace funkce můžete rychle a snadno vytvářet řešení pro komplexní integraci prostřednictvím prohlížeče, nebo pomocí sady Visual Studio.

Tato tabulka mapuje možnosti BizTalk Services na Logic Apps.

| BizTalk Services   | Logic Apps            | Účel                      |
| ------------------ | --------------------- | ---------------------------- |
| Spojovací čára          | Spojovací čára             | Odesílání a příjem dat.   |
| Přemostění             | Aplikace logiky             | Profilace procesoru           |
| Ověření fáze     | Ověření XML akce | Ověřit proti schématu dokumentu XML | 
| Obohacení fáze       | Tokeny dat           | Propagace vlastnosti do zprávy nebo pro rozhodování o směrování |
| Fáze transformace    | Transformace akce      | Převést XML zprávy z jednoho formátu do druhého |
| Dekódování fáze       | Plochý soubor dekódování akce | Převod z plochého souboru do XML |
| Kódování fáze       | Plochý soubor kódování akce | Převést XML na plochého souboru |
| Zpráva inspektoru  | Služba Azure Functions nebo API Apps | Spouští vlastní kód ve vašich integrace |
| Akce trasy       | Podmínka nebo přepínače | Směrování zpráv do jedné zadané konektorů |
|||| 

## <a name="biztalk-services-artifacts"></a>Artefakty BizTalk Services

BizTalk Services má několik druhů artefakty. 

## <a name="connectors"></a>Konektory

Konektory BizTalk Services pomoct mosty odesílat a přijímat data, včetně obousměrný přemostění, které umožňují interakce založené na protokolu HTTP požadavku nebo odpovědi. Logic Apps využívá stejnou terminologie a má více než 180 konektorů, které slouží stejnému účelu propojíte s širokou škálu technologií a služeb. Například konektory jsou dostupné pro cloud SaaS a službami, jako je OneDrive, Office 365, Dynamics CRM a další, a místními systémy přes místní bránu dat, který nahrazuje služba BizTalk Adapter Service pro službu BizTalk Services PaaS. Zdroje ve službě BizTalk Services jsou omezené na předplatné FTP, SFTP a fronty služby Service Bus nebo téma.

![](media/logic-apps-move-from-mabs/sources.png)

Ve výchozím nastavení má každý most koncový bod HTTP, který je konfigurován s adresou modulu Runtime a vlastnostech relativní adresa mostu. Chcete-li dosáhnete stejných výsledků pomocí funkce Logic Apps, použijte [žádostí a odpovědí](../connectors/connectors-native-reqres.md) akce.

## <a name="xml-processing-and-bridges"></a>Přemostění a zpracování XML

Ve službě BizTalk Services je obdobná kanálu zpracování most. Most může trvat data přijatá z konektoru, provádět některé pracovat s daty a výsledky odeslat do jiného systému. Logic Apps dělá to samé podporuje stejné vzorce založený na kanálech zásahu jako BizTalk Services a poskytuje také další způsoby integrace. [Most požadavek-odpověď XML](https://msdn.microsoft.com/library/azure/hh689781.aspx) v BizTalk Services se označuje jako kanál veter, který se skládá z fází, které provádějí tyto úlohy:

* (V) ověření
* (E) obohacení
* (T) transformace
* (E) obohacení
* (R) trasy

Tento obrázek ukazuje, jak zpracování je rozdělená mezi požadavku a odpovědi, která umožňuje řídit žádost a odpověď cesty samostatně, například s použitím různých mapy pro každou cestu:

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

Navíc XML jednosměrné most přidá fází dekódování a kódovat na začátku a konci zpracování. Most předávací obsahuje jedné fáze Enrich.

### <a name="message-processing-decoding-and-encoding"></a>Zpracování zpráv, dekódování a kódování

Ve službě BizTalk Services můžete přijímat odlišné typy zpráv XML a určení odpovídající schéma pro přijaté zprávy. Tato činnost je vykonána v *typy zpráv* fáze kanálu zpracování příjmu. Fáze dekódování použije typ zjištěné zprávy k dekódování zprávy pomocí v zadaném schématu. Pokud je schéma plochého souboru schématu, převede tuto fázi příchozí plochého souboru XML. 

Logic Apps poskytuje podobné funkce. Přijímat plochý soubor přes různé protokoly použití různých konektor triggerů (systém souborů, FTP, HTTP a tak dále) a použít [dekódování plochého souboru](../logic-apps/logic-apps-enterprise-integration-flatfile.md) akci příchozí data převést do formátu XML. Můžete přejít přímo k Logic Apps, nemusíte přitom dělat žádné změny vaší existující schémata plochého souboru a pak nahrajte schémata do účtu pro integraci.

### <a name="validation"></a>Ověření

Po převedení příchozích dat XML (nebo pokud XML byl formát zprávy přijaté), ověřování je spuštěno k určení, pokud zpráva odpovídá schématu XSD. Chcete-li provést tento úkol ve službě Logic Apps, použijte [ověření XML](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) akce. Můžete použít stejné schémat ze služby BizTalk Services bez uložení změn.

### <a name="transform-messages"></a>Transformujte zprávy

Fáze transformace ve službě BizTalk Services, převede jeden formát zprávy založený na formátu XML do jiného. Tuto práci provádí použitím mapu pomocí mapování na základě TRFM. V Logic Apps, která se podobá procesu. Akci transformace spustí mapu účtu integrace. Hlavní rozdíl je, že map ve službě Logic Apps jsou ve formátu XSLT. XSLT umožňuje znovu použít existující XSLT, který už máte, včetně mapy vytvořené pro BizTalk Server, které obsahují functoids. 

### <a name="routing-rules"></a>Pravidla směrování

BizTalk Services umožňuje směrování rozhodnutí o které koncového bodu nebo konektor k odeslání příchozí zprávy nebo data. Možnost vybrat z předem nakonfigurované koncové body je možné pomocí směrování možnosti filtru:

![](media/logic-apps-move-from-mabs/route-filter.png)

Ve službě BizTalk Services, pokud existují pouze dvě možnosti, použití *podmínku* je nejlepší způsob pro převod směrovacích filtrů v BizTalk Services. Pokud existuje více než dva, použijte **přepnout**.

Logic Apps poskytuje možnosti pokročilou logiku a navíc pokročilé řízení toku a směrování s [podmíněné příkazy](../logic-apps/logic-apps-control-flow-conditional-statement.md) a [příkazy switch](../logic-apps/logic-apps-control-flow-switch-statement.md).

### <a name="enrich"></a>Obohacení

Při zpracování BizTalk Services, přidá fázi Enrich vlastnosti zprávy kontext přidružený k data byla přijata. Například zvýšení úrovně vlastnosti, které chcete použít pro směrování z vyhledávání v databázi nebo extrahováním hodnotu vlastnosti autorefresh pomocí výrazu XPath. Logic Apps poskytuje přístup k všechny kontextové údaje výstupy z předchozích akcí, díky tomu je jednoduché replikovat stejné chování. Například použití `Get Row` akce připojení SQL, vrátí data z databáze SQL serveru a používat data v rámci akce rozhodnutí pro směrování. Podobně, vlastnosti v Service Bus příchozích zpráv zařazených do fronty pomocí aktivační události jsou adresovatelné, jakož i pomocí výrazu xpath pracovní postup definice jazyka XPath.

### <a name="run-custom-code"></a>Spouští vlastní kód

BizTalk Services umožňuje [spouští vlastní kód](https://msdn.microsoft.com/library/azure/dn232389.aspx) , který se nahraje do vlastní sestavení. Tato funkce je implementované [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector) rozhraní. Každá fáze v most obsahuje dvě vlastnosti (inspektor na zadejte a na konec inspektor), které poskytují typ formátu .NET, kterou jste vytvořili, který implementuje toto rozhraní. Vlastní kód umožňuje provádět složitější zpracování dat a umožňuje znovu použít existující kód ve třídě sestavení, které provádějí běžné obchodní logiku. 

Služba Logic Apps poskytuje dva základní způsoby spouštění vlastního kódu: Azure Functions a API Apps. Služba Azure Functions můžete vytvořit a volat z aplikace logiky. Zobrazit [přidat a spuštění vlastního kódu pro aplikace logiky pomocí Azure Functions](../logic-apps/logic-apps-azure-functions.md). Můžete vytvořit svoje vlastní aktivační události a akce součástí služby Azure App Service API Apps. Další informace o [vytváření vlastních rozhraní API pro použití s Logic Apps](../logic-apps/logic-apps-create-api-app.md). 

Pokud máte vlastní kód v sestavení, které volají ze služby BizTalk Services, můžete buď přesuňte tento kód do služby Azure Functions nebo vytvořit vlastní rozhraní API pomocí funkce API Apps, v závislosti na tom, co jste implementace. Například pokud máte kód, který obtéká jiné služby, pro kterou nemá Logic Apps konektor, vytvořte aplikaci API a použijte akce, které vaše aplikace API poskytuje v rámci vaší aplikace logiky. Pokud už máte pomocné funkce nebo knihovny, pak Azure Functions je pravděpodobně nejvhodnější.

### <a name="edi-processing-and-trading-partner-management"></a>EDI zpracování a správa obchodních partnerů

BizTalk Services a Logic Apps patří zpracování EDI a B2B díky podpoře pro AS2 (Applicability Statement 2), X12 a EDIFACT. Ve službě BizTalk Services vaší vytvořit přemostění EDI a vytvoření nebo správu obchodních partnerů a smluv v vyhrazený portál sledování a řízení.
Ve službě Logic Apps, získáte této funkce [Enterprise Integration Pack (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md). Poskytuje EIP [účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a akcí B2B pro zpracování EDI a B2B. Také použít k vytvoření a Správa účtu pro integraci [obchodními partnery](../logic-apps/logic-apps-enterprise-integration-partners.md) a [smlouvy](../logic-apps/logic-apps-enterprise-integration-agreements.md). Po vytvoření účtu pro integraci můžete propojit účet jednu nebo víc aplikací logiky. Potom můžete akcí B2B pro přístup k obchodním informace o partnerovi vaší aplikace logiky. Jsou k dispozici tyto akce:

* Kódování AS2
* Dekódování AS2
* X12 kódování
* Dekódování X12
* Kódování EDIFACT
* Dekódování EDIFACT

Na rozdíl od služby BizTalk Services tyto akce jsou odpojené od přenosové protokoly. Takže při vytváření aplikací logiky máte větší flexibilitu v které konektory, které umožňují odesílat a přijímat data. Například můžete zobrazit soubory X12 jako přílohy z e-mailu a potom proces těchto souborů v aplikaci logiky. 

## <a name="manage-and-monitor"></a>Správa a monitorování

Ve službě BizTalk Services vyhrazený portál poskytuje možnosti sledování pro monitorování a řešení potíží. Logic Apps nabízí širší sledování a monitorování možnosti prostřednictvím [webu Azure portal](../logic-apps/logic-apps-monitor-your-logic-apps.md)a obsahuje mobilní aplikaci pro zachování přehled na věcech, když jste na cestách.

## <a name="high-availability"></a>Vysoká dostupnost

Pro zajištění vysoké dostupnosti (HA) ve službě BizTalk Services můžete sdílet procesní zatížení s využitím více než jednu instanci v určité oblasti. Služba Logic Apps poskytuje vysokou dostupnost v oblastech bez dalších poplatků. 

Zotavení po havárii mimo oblast pro zpracování B2B ve službě BizTalk Services vyžaduje proces zálohování a obnovení. Zajišťuje nepřetržitý chod podniků, Logic Apps nabízí mezi různými oblastmi aktivní/pasivní [schopnost zotavení po Havárii](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md), který umožňuje synchronizovat B2B data mezi účty pro integraci v různých oblastech.

## <a name="next-steps"></a>Další postup

* [Co je Logic Apps?](../logic-apps/logic-apps-overview.md)
* [Vytvořte první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) nebo můžete rychle začít pomocí [předem připravené šablony](../logic-apps/logic-apps-create-logic-apps-from-templates.md).  
* [Zobrazení všech dostupných konektorů](../connectors/apis-list.md) , můžete použít ve funkci logic apps