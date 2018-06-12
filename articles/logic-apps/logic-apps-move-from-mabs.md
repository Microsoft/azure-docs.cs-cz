---
title: Přesunutí aplikace ze služby BizTalk Azure Logic Apps | Microsoft Docs
description: Přesunutí nebo migraci na Azure Logic Apps služby Azure BizTalk Services (MABS)
services: logic-apps
documentationcenter: ''
author: jonfancey
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: jonfan; LADocs
ms.openlocfilehash: 846386172c0221c217430e62c8560484f799fa7f
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299541"
---
# <a name="move-from-biztalk-services-to-azure-logic-apps"></a>Přesunutí ze služby BizTalk Azure Logic Apps

Microsoft Azure BizTalk Services (MABS) jsou vyřazována z provozu. Přesunout vaše řešení integrace MABS [Azure Logic Apps](../logic-apps/logic-apps-overview.md), postupujte podle pokynů v tomto článku. 

## <a name="introduction"></a>Úvod

BizTalk Services se skládá ze dvou subservices:

* Microsoft BizTalk Services hybridní připojení
* EAI a EDI na základě most integrace

[Azure App Service hybridní připojení](../app-service/app-service-hybrid-connections.md) nahrazuje BizTalk Services hybridní připojení. Azure hybridní připojení se službou Azure App Service k dispozici prostřednictvím portálu Azure. Tato služba poskytuje správce hybridního připojení, abyste mohli spravovat stávající hybridní připojení služby BizTalk Services a také nové hybridní připojení, které vytvoříte na portálu. 

[Služba Logic Apps](../logic-apps/logic-apps-overview.md) nahradí EAI a EDI na základě most integrace stejné funkce v BizTalk Services a další. Tato služba poskytuje cloudového škálovatelného na základě spotřeby pracovního postupu a orchestraci funkce můžete snadno a rychle vytvořit komplexní integrace řešení prostřednictvím prohlížeče nebo pomocí sady Visual Studio.

Tato tabulka mapuje funkce BizTalk Services Logic Apps.

| BizTalk Services   | Logic Apps            | Účel                      |
| ------------------ | --------------------- | ---------------------------- |
| Spojovací čára          | Spojovací čára             | Odesílat a přijímat data   |
| Most             | Aplikace logiky             | Procesor kanálu           |
| Ověření fáze     | Ověření XML akce | Ověření dokument XML pro schéma. | 
| Zlepšit komunikaci oddělení fáze       | Tokeny dat           | Povyšte vlastnosti do zprávy nebo pro rozhodnutí o směrování |
| Fáze transformace    | Transformace akce      | Převod XML zprávy z jednoho formátu do druhého |
| Dekódovat fáze       | Plochý soubor dekódovat akce | Převést z plochého souboru XML |
| Kódování fáze       | Plochý soubor kódování akce | Převod XML na plochý soubor |
| Zpráva Inspector  | Azure Functions nebo aplikace API | Spuštění vlastního kódu v vaše integrace |
| Akce trasy       | Podmínka nebo přepínače | Směrování zpráv do jednoho zadaného konektorů |
|||| 

## <a name="biztalk-services-artifacts"></a>BizTalk Services artefaktů

BizTalk Services má několik druhů artefakty. 

## <a name="connectors"></a>Konektory

BizTalk Services konektory pomoci mostů odesílat a přijímat data, včetně obousměrný mostů, které umožňují interakce založené na protokolu HTTP žádosti a odpovědi. Služba Logic Apps používá stejné terminologie a má 180 + konektory, které slouží ke stejnému účelu připojením k široké škále technologií a služeb. Například konektory jsou k dispozici pro cloud SaaS a službami, jako je OneDrive, Office 365, Dynamics CRM a další, plus místních systémů prostřednictvím brány místní Data, která nahrazuje služba BizTalk Adapter Service služby BizTalk Services PaaS. Zdroje ve službě BizTalk Services je omezen na předplatné FTP, SFTP a frontou Service Bus nebo téma.

![](media/logic-apps-move-from-mabs/sources.png)

Ve výchozím nastavení má každý most koncový bod HTTP, který je konfigurován s adresou Runtime a vlastnosti relativní adresy pro most. K dosažení stejné výsledky s Logic Apps, použít [žádostí a odpovědí](../connectors/connectors-native-reqres.md) akce.

## <a name="xml-processing-and-bridges"></a>Zpracování kódu XML a přemostění

Ve službě BizTalk Services mostu se podobá je zpracování kanálu. Most může trvat data přijatá z konektoru, provést některé práce s daty a odešlou výsledky do jiného systému. Služba Logic Apps dělá to stejné podpora stejné vzory interakce na základě kanálu jako službu BizTalk Services a také poskytnutím dalšími integrace vzory. [Most požadavek-odpověď XML](https://msdn.microsoft.com/library/azure/hh689781.aspx) ve službě BizTalk Services se označuje jako VETER kanálu, který se skládá z fází, které provádět tyto úlohy:

* (V) ověření
* (E) zlepšit komunikaci
* (T) transformace
* (E) zlepšit komunikaci
* (R) trasy

Tento obrázek ukazuje, jak zpracování je rozdělená mezi dotazů a odpovědí, který umožňuje řídit požadavku a odpovědi cesty samostatně, například pomocí různých mapy pro každou z cest:

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

Navíc XML jednosměrný most přidá dekódování a kódovat fázích na začátku a konci zpracování. Most průchozí obsahuje jeden úsek Enrich.

### <a name="message-processing-decoding-and-encoding"></a>Zpracování zpráv, dekódování a kódování

Ve službě BizTalk Services můžete přijímat odlišné typy zpráv XML a určit odpovídající schéma pro přijatá zpráva. Tato práce se provádí v *typy zpráv* fáze kanálu zpracování příjmu. Typ zjištěné zprávy fázi dekódování pak použije k dekódování zprávy pomocí zadané schéma. Pokud schéma plochého souboru schématu, tato fáze převede příchozí plochý soubor XML. 

Služba Logic Apps poskytuje podobné funkce. Můžete přijímat plochý soubor přes různé protokoly, pomocí různých konektor aktivačních událostí (systém souborů, FTP, HTTP a tak dále) a pomocí [plochý soubor dekódovat](../logic-apps/logic-apps-enterprise-integration-flatfile.md) akce příchozí data převést do formátu XML. Můžete přesunout vaší existující schémata plochý soubor přímo do aplikace logiky beze změn a pak nahrajte schémata ke svému účtu integrace.

### <a name="validation"></a>Ověření

Po převedení příchozích dat XML (nebo pokud XML byl formát zprávy přijaté), probíhá ověření k určení, pokud zpráva dodržuje schéma XSD. K provedení této úlohy v Logic Apps, použijte [ověření XML](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) akce. Můžete použít stejné schémat ze služby BizTalk Services beze změn.

### <a name="transform-messages"></a>Transformace zprávy

Ve službě BizTalk Services fázi transformace převod jednoho formátu zprávu formátu XML. Tyto akce jsou prováděny s použitím mapu, pomocí na základě TRFM mapper. V Logic Apps je podobný procesu. Transformace akci provede mapu z vašeho účtu integrace. Hlavní rozdíl je, že map v Logic Apps jsou ve formátu XSLT. XSLT zahrnuje možnost opakovaně použít existující XSLT je již, včetně map vytvořených pro BizTalk serveru, které obsahují functoids. 

### <a name="routing-rules"></a>Pravidla směrování

BizTalk Services umožňuje směrování rozhodnutí o které koncového bodu nebo konektor k odeslání příchozí zprávy nebo data. Umožňuje vybrat z předem nakonfigurované koncové body je možné pomocí možnosti směrování filtru:

![](media/logic-apps-move-from-mabs/route-filter.png)

Ve službě BizTalk Services, pokud existují pouze dvě možnosti, pomocí *podmínku* je nejlepší způsob pro převod filtrech směrování ve službě BizTalk Services. Pokud je větší než dvě, použijte **přepínač**.

Služba Logic Apps poskytuje možnosti pokročilou logiku plus pokročilé řízení toku a směrování s [podmíněné příkazy](../logic-apps/logic-apps-control-flow-conditional-statement.md) a [přepínač příkazy](../logic-apps/logic-apps-control-flow-switch-statement.md).

### <a name="enrich"></a>Zlepšit komunikaci oddělení

BizTalk Services zpracování fázi Enrich přidává vlastnosti k zpráva kontext přidružený získaná data. Například povýšení vlastnosti, které chcete použít pro směrování z vyhledávání v databázi nebo extrahováním hodnotu pomocí výrazu XPath. Služba Logic Apps poskytuje přístup k všechny výstupy kontextuální data z předchozích akce, což přehledné replikovat stejné chování. Například pomocí `Get Row` akce připojení SQL, můžete vrátit data z databáze systému SQL Server a pomocí dat v rámci akce rozhodnutí pro směrování. Podobně vlastnosti sběrnice příchozích zpráv zařazených do fronty aktivační procedura jsou adresovatelné, jakož i pomocí výraz xpath pracovní postup definice jazyka XPath.

### <a name="run-custom-code"></a>Spuštění vlastního kódu

BizTalk Services umožňuje [spuštění vlastního kódu](https://msdn.microsoft.com/library/azure/dn232389.aspx) , je nahrát vlastní sestavení. Tato funkce je implementované [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector) rozhraní. Každá fáze v most zahrnuje dvě vlastnosti (na zadejte Inspector a na konec Inspector), poskytující .NET typ, který jste vytvořili, který toto rozhraní implementuje. Vlastní kód umožňuje provádět složitější zpracování dat a můžete znovu použít existující kód v sestavení, které provádějí běžné obchodní logiku. 

Služba Logic Apps poskytuje dva primární způsoby, jak provést vlastní kód: Azure Functions a aplikace API. Azure Functions můžete vytvořit a volání z aplikace logiky. V tématu [přidat a spuštění vlastní kód pro logic apps prostřednictvím Azure Functions](../logic-apps/logic-apps-azure-functions.md). Pomocí aplikace API, součástí Azure App Service, můžete vytvořit vlastní triggery a akce. Další informace o [vytváření vlastní rozhraní API pro použití s Logic Apps](../logic-apps/logic-apps-create-api-app.md). 

Pokud máte vlastní kód v sestavení, které je možné volat z BizTalk Services, můžete přesunout tento kód do Azure Functions, nebo vytvořte vlastní rozhraní API s API Apps, v závislosti na tom, co jste implementace. Například pokud máte kód, který zabalí jiné služby, pro který Logic Apps nemá konektor, pak vytvořit aplikaci API a pomocí akce, které vaše aplikace API poskytuje v rámci aplikace logiky. Pokud máte podpůrné funkce nebo knihovny, pak Azure Functions je pravděpodobně nejlepší.

### <a name="edi-processing-and-trading-partner-management"></a>EDI zpracování a správa obchodních partnerů

BizTalk Services a Logic Apps obsahují zpracování EDI a B2B s podporou pro AS2 (použitelnosti příkaz 2), X12 a EDIFACT. Ve službě BizTalk Services vaší vytváření mostů EDI a vytvořit ani spravovat obchodní partnery a smluv na portálu správy a sledování vyhrazené.
V Logic Apps, získáte tato funkce [Enterprise Integration Pack (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md). Poskytuje EIP [integrace účet](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a B2B akce pro zpracování EDI a B2B. Používáte účet s integrace také k vytváření a správě [obchodních partnerů](../logic-apps/logic-apps-enterprise-integration-partners.md) a [smlouvy](../logic-apps/logic-apps-enterprise-integration-agreements.md). Po vytvoření účtu integrace, můžete propojit jeden nebo více aplikací logiky k účtu. Potom můžete B2B akce pro přístup k obchodním informace o partnerovi z aplikace logiky. Jsou k dispozici následující akce:

* Kódování AS2
* Dekódovat AS2
* X12 kódování
* Dekódovat X12
* EDIFACT kódování
* Dekódovat EDIFACT

Na rozdíl od služby BizTalk tyto akce jsou odpojené od přenosové protokoly. Proto při vytváření aplikace logiky, máte větší flexibilitu, ve které konektory, které umožňují odesílat a přijímat data. Například může přijímat X12 soubory jako přílohy z e-mailu a pak proces těchto souborů v aplikaci logiky. 

## <a name="manage-and-monitor"></a>Správa a sledování

Ve službě BizTalk Services vyhrazené portálu zadat možnosti sledování ke sledování a řešení problémů. Služba Logic Apps nabízí širší sledování a monitorování možnosti prostřednictvím [portál Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md)a obsahuje mobilní aplikaci pro když jste na cestách sledování věcí.

## <a name="high-availability"></a>Vysoká dostupnost

Pro zajištění vysoké dostupnosti (HA) ve službě BizTalk Services můžete sdílet zatížení pomocí více než jedna instance v určité oblasti. Služba Logic Apps poskytuje HA v oblasti bez dalších poplatků. 

Zotavení po havárii na více systémů oblast pro zpracování B2B ve službě BizTalk Services vyžaduje procesu zálohování a obnovení. Pro kontinuitu podnikových procesů Logic Apps nabízí mezi oblastmi aktivní/pasivní [možnost zotavení po Havárii](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md), která vám umožňuje synchronizovat B2B data mezi účty pro integraci v různých oblastech.

## <a name="next-steps"></a>Další postup

* [Co je Logic Apps?](../logic-apps/logic-apps-overview.md)
* [Vytvořte první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) nebo můžete rychle začít pomocí [předem připravené šablony](../logic-apps/logic-apps-create-logic-apps-from-templates.md).  
* [Zobrazit všechny dostupné konektory](../connectors/apis-list.md) , můžete použít v aplikacích logiky