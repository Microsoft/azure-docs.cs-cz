---
title: Migrace aplikací z BizTalk Services do Azure Logic Apps
description: Jak přesunout aplikace a řešení z Microsoft Azure BizTalk Services (MABS) do Azure Logic Apps
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/30/2017
ms.openlocfilehash: 6c07ab4b18c017bd29723d2640129b8e67374e3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023648"
---
# <a name="migrate-your-apps-and-solutions-from-biztalk-services-to-azure-logic-apps"></a>Migrace aplikací a řešení z BizTalk Services na Azure Logic Apps

Vyřazení z Microsoft Azure BizTalk Services (MABS). Pokud chcete přesunout řešení pro integraci MABS do [Azure Logic Apps](../logic-apps/logic-apps-overview.md), postupujte podle pokynů v tomto článku. 

## <a name="introduction"></a>Úvod

BizTalk Services se skládá ze dvou podslužby:

* Microsoft BizTalk Services Hybrid Connections
* Integrace založená na mostech EAI a EDI

[Azure App Service Hybrid Connections](../app-service/app-service-hybrid-connections.md) nahrazuje BizTalk Services Hybrid Connections. Azure Hybrid Connections je k dispozici pro Azure App Service prostřednictvím Azure Portal. Tato služba poskytuje Správce hybridního připojení, abyste mohli spravovat existující BizTalk Services hybridní připojení a také nová hybridní připojení, která vytvoříte na portálu. 

[Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje integraci na základě mostu EAI a EDI se všemi stejnými možnostmi v BizTalk Services a dalších. Tato služba poskytuje pracovní postupy a funkce orchestrace na základě spotřeby na úrovni cloudu, abyste mohli rychle a snadno vytvářet složitá integrační řešení prostřednictvím prohlížeče nebo sady Visual Studio.

Tato tabulka mapuje BizTalk Services možností Logic Apps.

| BizTalk Services   | Logic Apps            | Účel                      |
| ------------------ | --------------------- | ---------------------------- |
| Konektor          | Konektor             | Odesílání a příjem dat   |
| Bridge             | Aplikace logiky             | Procesor kanálu           |
| Ověřit fázi     | Akce ověřování XML | Ověření dokumentu XML proti schématu | 
| Fáze obohacení       | Datové tokeny           | Zvýšení úrovně vlastností na zprávy nebo pro rozhodování o směrování |
| Fáze transformace    | Akce transformace      | Převod zpráv XML z jednoho formátu na jiný |
| Dekódovat fázi       | Akce dekódování plochého souboru | Převod z plochého souboru na XML |
| Zakódovat fázi       | Akce kódování plochého souboru | Převést z XML na plochý soubor |
| Kontrola zpráv  | Azure Functions nebo API Apps | Spouštění vlastního kódu v integrcích |
| Akce směrování       | Podmínka nebo přepínač | Směrování zpráv do jednoho ze zadaných konektorů |
|||| 

## <a name="biztalk-services-artifacts"></a>BizTalk Services artefakty

BizTalk Services má několik typů artefaktů. 

## <a name="connectors"></a>Konektory

Konektory BizTalk Services umožňují mostům odesílat a přijímat data, včetně obousměrných mostů, které umožňují interakci s požadavky a odpovědí založenými na protokolu HTTP. Logic Apps používá stejnou terminologii a má stovky konektorů, které slouží ke stejnému účelu připojením k široké škále technologií a služeb. Například konektory jsou k dispozici pro cloudové služby SaaS a PaaS, jako jsou OneDrive, Office 365, Dynamics CRM a další, a navíc místní systémy prostřednictvím místní brány dat, která nahrazuje službu BizTalk Adapter Service pro BizTalk Services. Zdroje v BizTalk Services jsou omezeny na FTP, SFTP a Service Bus a v předplatném tématu.

![Diagram znázorňující tok BizTalk Services.](media/logic-apps-move-from-mabs/sources.png)

Ve výchozím nastavení má každý most koncový bod HTTP, který je nakonfigurovaný pomocí běhové adresy a relativních vlastností adresy pro daný most. Chcete-li dosáhnout stejných výsledků s Logic Apps, použijte akci [a akce odpovědi](../connectors/connectors-native-reqres.md) .

## <a name="xml-processing-and-bridges"></a>Zpracování a mosty XML

V BizTalk Services je most analogicky podobný kanálu zpracování. Most může přijímat data přijatá z konektoru, dělat práci s daty a odesílat výsledky do jiného systému. Logic Apps stejně podporuje stejné vzory interakce založené na kanálech jako BizTalk Services a také poskytuje další způsoby integrace. [Request-Replyový most](/previous-versions/azure/hh689781(v=azure.100)) v BizTalk Services je známý jako kanál VETER, který se skládá z fází, které provádějí tyto úlohy:

* (V) ověřit
* (E) obohacení
* (T) transformace
* (E) obohacení
* (R) trasa

Tento obrázek ukazuje, jak je rozděleno mezi požadavky a odpověďmi, což zajišťuje kontrolu nad žádostí a cestami odpovědí samostatně, například pomocí různých map pro každou cestu:

![Snímek obrazovky, který ukazuje, jak je zpracování rozděleno mezi požadavky a odpověď.](media/logic-apps-move-from-mabs/xml-request-reply.png)

Také One-Wayový most XML přidá fáze dekódování a kódování na začátku a na konci zpracování. Most Pass-Through obsahuje jednu fázi obohacení.

### <a name="message-processing-decoding-and-encoding"></a>Zpracování, dekódování a kódování zpráv

V BizTalk Services můžete přijímat různé typy zpráv XML a určit, jaké schéma má zpráva přijata. Tato práce se provádí ve fázi *typů zpráv* v kanálu pro zpracování příjmu. Fáze dekódování potom pomocí poskytnutého schématu dekóduje zprávu pomocí typu zjištěné zprávy. Pokud je schéma schéma plochého souboru, tato fáze převede příchozí nezpracovaný soubor na XML. 

Logic Apps poskytuje podobné možnosti. Pomocí různých triggerů konektoru (systém souborů, FTP, HTTP atd.) obdržíte plochý soubor přes různé protokoly a pomocí akce [plochý dekódovat soubory](../logic-apps/logic-apps-enterprise-integration-flatfile.md) převeďte data do XML. Stávající schémata plochých souborů můžete přesunout přímo do Logic Apps bez jakýchkoli změn a pak do svého účtu pro integraci nahrát schémata.

### <a name="validation"></a>Ověřování

Poté, co jsou příchozí data převedena do formátu XML (nebo pokud byl XML přijat formátu zprávy), spustí ověřování a určí, zda zpráva dodržuje vaše schéma XSD. K provedení této úlohy v Logic Apps použijte akci [ověření XML](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) . Můžete použít stejná schémata z BizTalk Services bez jakýchkoli změn.

### <a name="transform-messages"></a>Transformovat zprávy

V BizTalk Services fáze transformace převede jeden formát zprávy založený na jazyce XML na jiný. Tato práce se provádí použitím mapy pomocí mapovače založeného na TRFM. V Logic Apps je proces podobný. Akce Transform spustí mapu z účtu pro integraci. Hlavním rozdílem je, že mapy v Logic Apps jsou ve formátu XSLT. XSLT nabízí možnost znovu použít existující XSLT, které už máte, včetně map vytvořených pro BizTalk Server, které obsahují functoids. 

### <a name="routing-rules"></a>Pravidla směrování

BizTalk Services provede rozhodnutí směrování, ke kterému koncovému bodu nebo konektoru odesílají příchozí zprávy nebo data. Možnost výběru z předem nakonfigurovaných koncových bodů je možná pomocí možnosti směrovacího filtru:

![Snímek obrazovky, který zobrazuje možnost směrovacího filtru.](media/logic-apps-move-from-mabs/route-filter.png)

V BizTalk Services platí, že pokud jsou k dispozici pouze dvě možnosti, použití *podmínky* je nejlepším způsobem, jak převést filtry směrování v BizTalk Services. Pokud existuje více než dva, použijte **přepínač**.

Logic Apps poskytuje sofistikované možnosti logiky a pokročilý tok řízení a směrování s [podmíněnými příkazy](../logic-apps/logic-apps-control-flow-conditional-statement.md) a [příkazy Switch](../logic-apps/logic-apps-control-flow-switch-statement.md).

### <a name="enrich"></a>Rozšiřování

Při zpracování BizTalk Services přidá fáze obohacení vlastnosti do kontextu zprávy přidruženého k přijímaným datům. Například zvýšení úrovně vlastnosti pro použití při směrování z vyhledávání databáze nebo extrakcí hodnoty pomocí výrazu XPath. Logic Apps poskytuje přístup ke všem datovým výstupům kontextových dat z předchozích akcí, takže je snadné replikovat stejné chování. Například pomocí `Get Row` akce připojení SQL vrátíte data z databáze SQL Server a použijete data v rozhodovací akci pro směrování. Stejně tak vlastnosti příchozích Service Bus zpráv ve frontě pomocí triggeru jsou adresovatelné a také XPath pomocí výrazu jazyka definice pracovního postupu XPath.

### <a name="run-custom-code"></a>Spustit vlastní kód

BizTalk Services umožňuje [Spustit vlastní kód](/previous-versions/azure/dn232389(v=azure.100)) , který se nahraje ve vlastních sestaveních. Tuto funkci implementuje rozhraní [IMessageInspector]() . Každá fáze v mostu obsahuje dvě vlastnosti (při kontrole a při ukončení), které poskytují typ rozhraní .NET, který jste vytvořili, který implementuje toto rozhraní. Vlastní kód umožňuje provádět složitější zpracování dat a umožňuje znovu použít existující kód v sestaveních, která provádějí běžné obchodní logiky. 

Logic Apps poskytuje dva hlavní způsoby spouštění vlastního kódu: Azure Functions a API Apps. Azure Functions lze vytvořit a volat z Logic Apps. Přečtěte si téma [Přidání a spuštění vlastního kódu pro Logic Apps prostřednictvím Azure Functions](../logic-apps/logic-apps-azure-functions.md). K vytvoření vlastních triggerů a akcí použijte API Apps, součást Azure App Service. Přečtěte si další informace o [Vytvoření vlastního rozhraní API pro použití s Logic Apps](../logic-apps/logic-apps-create-api-app.md). 

Pokud máte vlastní kód v sestaveních, které voláte z BizTalk Services, můžete buď přesunout tento kód do Azure Functions, nebo vytvořit vlastní rozhraní API s API Apps, v závislosti na tom, co implementujete. Například pokud máte kód, který zabalí jinou službu, pro kterou Logic Apps nemá konektor, vytvořte aplikaci API a použijte akce, které vaše aplikace API poskytuje v rámci vaší aplikace logiky. Pokud máte pomocné funkce nebo knihovny, pak Azure Functions pravděpodobně nejlépe.

### <a name="edi-processing-and-trading-partner-management"></a>Řízení pro zpracování a obchodní partnery v EDI

BizTalk Services a Logic Apps zahrnují zpracování EDI a B2B s podporou pro AS2 (Applicability Statement 2), X12 a EDIFACT. V BizTalk Services vaše vytváření mostů EDI a vytváření nebo Správa obchodních partnerů a smluv na vyhrazeném portálu pro sledování a správu.
V Logic Apps získáte tuto funkci prostřednictvím [Enterprise Integration Pack (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md). EIP poskytuje [integrační účet](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a akce B2B pro zpracování EDI a B2B. Účet pro integraci také slouží k vytváření a správě [obchodních partnerů](../logic-apps/logic-apps-enterprise-integration-partners.md) a [smluv](../logic-apps/logic-apps-enterprise-integration-agreements.md). Po vytvoření účtu pro integraci můžete propojit jednu nebo více aplikací logiky s účtem. Pak můžete pomocí akcí B2B získat přístup k informacím o obchodních partnerech z aplikace logiky. K dispozici jsou následující akce:

* Kódování AS2
* Dekódování AS2
* Kódování X12
* Dekódování X12
* Kódování EDIFACT
* Dekódování EDIFACT

Na rozdíl od BizTalk Services jsou tyto akce oddělené od přenosových protokolů. Takže při vytváření aplikací logiky máte větší flexibilitu, ve které konektory slouží k odesílání a přijímání dat. Můžete například přijmout soubory X12 jako přílohy z e-mailu a pak tyto soubory zpracovat v aplikaci logiky. 

## <a name="manage-and-monitor"></a>Správa a monitorování

V BizTalk Services vyhrazený portál poskytoval funkce sledování, které sledují a řeší problémy. Logic Apps poskytuje bohatší možnosti sledování a monitorování pro [monitorování aplikací logiky v Azure Portal](../logic-apps/monitor-logic-apps.md)a zahrnuje mobilní aplikace, které vám při přesunu pomůžou sledovat věci.

## <a name="high-availability"></a>Vysoká dostupnost

Pro zajištění vysoké dostupnosti v BizTalk Services můžete sdílet zatížení zpracování pomocí více než jedné instance v konkrétní oblasti. Logic Apps poskytuje v oblasti HA žádné další náklady. 

V BizTalk Services zotavení po havárii mimo oblast pro zpracování B2B vyžaduje proces zálohování a obnovení. Pro zajištění kontinuity podnikových služeb Logic Apps nabízí aktivní a pasivní [funkce zotavení po havárii](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)mezi oblastmi, které umožňují synchronizaci dat B2B napříč účty pro integraci v různých oblastech.

## <a name="next-steps"></a>Další kroky

* [Co je Logic Apps?](../logic-apps/logic-apps-overview.md)
* [Vytvořte první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) nebo můžete rychle začít pomocí [předem připravené šablony](../logic-apps/logic-apps-create-logic-apps-from-templates.md).  
* [Zobrazit všechny dostupné konektory](../connectors/apis-list.md) , které můžete použít ve službě Logic Apps