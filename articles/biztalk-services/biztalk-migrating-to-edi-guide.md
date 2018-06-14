---
title: Migrace řešení BizTalk Server EDI technické příručce BizTalk Services | Microsoft Docs
description: Migrovat EDI MABS; Microsoft Azure BizTalk Services
services: biztalk-services
documentationcenter: na
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 61c179fa-3f37-495b-8016-dee7474fd3a6
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 1b70fc3d199d7f1521acb534dafec8fb3e69500e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23843487"
---
# <a name="migrating-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>Migrace řešení EDI BizTalk Server na služby BizTalk Services: technické příručce

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Autor: Tim Wieman a Nitin Mehrotra

Recenzenti: Karthik Bharthy

Vytvořené pomocí: verze služby Microsoft Azure BizTalk Services – únor 2014.

## <a name="introduction"></a>Úvod
Elektronické Data Interchange (EDI) je jedním ze současných převažujících prostředek, pomocí které firmám data serveru exchange elektronicky, také označovaných jako Business-to-Business nebo B2B transakce. BizTalk Server má obsahoval pro podporu EDI přes deset od původní verze BizTalk serveru. Společnost Microsoft s služby BizTalk Services nadále podporu EDI řešení na platformě Microsoft Azure. B2B transakce jsou většinou mimo organizaci, a proto je snazší implementovat, pokud již byl implementován na cloudové platformy. Tuto možnost prostřednictvím služby BizTalk Services nabízí Microsoft Azure.

Když někteří zákazníci podívejte se na službu BizTalk Services jako platforma "greenfield" nové řešení EDI, mít mnoho zákazníků aktuální řešení BizTalk serveru EDI, která může chcete provést migraci na Azure. Protože BizTalk Services EDI je navržen podle stejných klíče entit jako architektura BizTalk serveru EDI (obchodní partnery, entity, smluv), je možné migrovat BizTalk serveru EDI artefakty do služby BizTalk Services.

Tento dokument popisuje některé rozdíly spojené s migraci artefaktů BizTalk serveru EDI BizTalk Services. Tento dokument předpokládá praktické znalosti zpracování EDI BizTalk serveru a Trading Partner smlouvy. Další informace o EDI BizTalk serveru najdete v tématu [Trading Partner Management pomocí BizTalk Server](https://msdn.microsoft.com/library/bb259970.aspx).

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>Kterou verzi BizTalk serveru EDI artefakty se dají migrovat na službu BizTalk Services?
BizTalk Server EDI modulu došlo k výraznému rozšíření pro BizTalk Server 2010, pokud byla změna modelu partnerů, profilů a smlouvy. BizTalk Services používá stejný model pro uspořádání obchodními partnery a divizí v rámci těchto obchodními partnery. V důsledku toho migraci artefaktů EDI z BizTalk Server 2010 a novější verze do služby BizTalk Services, je mnohem víc splněny následující proces. K migraci artefaktů EDI přidružené verze starší než BizTalk Server 2010, musíte nejprve upgradovat na BizTalk Server 2010 a následná migrace EDI artefakty na služby BizTalk Services.

## <a name="scenariosmessage-flow"></a>Tok scénáře/zpráv
Jako BizTalk Server EDI zpracování ve službě BizTalk Services vytvořené kolem řešení Trading Partner Management (TPM). Řešení TPM má následující klíčové komponenty:

* Obchodních partnerů, které představují organizaci ve B2B transakce.
* Profily, které představují divizemi v rámci obchodního partnera.
* Trading partner smlouvy (nebo smluv), která představují obchodní smlouva mezi dva partneři nebo profily.

Následující obrázek znázorňuje podobnosti a také rozdíly mezi řešení BizTalk serveru EDI a řešení BizTalk Services EDI:

![][EDImessageflow]

Jsou hlavní rozdíly a podobnosti mezi tok EDI řešení BizTalk serveru a služby BizTalk Services:

* Stejně jako BizTalk Server používá kanál EDIReceive přijmout zprávu EDI a kanál EDISend k odeslání zprávy EDI, používá služba BizTalk Services EDI přijímat most pro příjem a odesílání EDI mostu k odeslání zprávy EDI. BizTalk serveru kanálů jsou spojeny s smlouvu pomocí odesílat nebo přijímat porty. Ve službě BizTalk Services smlouvu, samotné označuje odesílání nebo přijímat most.
* BizTalk serveru po kanálu EDIReceive zpracovává zprávy EDI zpráva vypsána k databázi systému SQL Server. Kanál EdiSend pak převezme zprávy z databáze serveru SQL, procesy a poté odešle do obchodního partnera.
  
    Ve službě BizTalk Services po EDI přijímat most EDI zprávu zpracuje, směruje zprávy do externího procesu. Externí proces může být spuštěn v Microsoft Azure nebo místně. Externího procesu by měl směrovat zprávy k odeslání mostu EDI; odeslání most ze své podstaty nestahuje zprávy. Po zpracování zprávy, most odesílání EDI směruje zprávy obchodního partnera.

BizTalk Services poskytuje snadno použít konfiguraci rychle vytvořit a nasadit smlouvy B2B mezi obchodních partnerů bez konfigurace žádné Microsoft Azure Compute instance (role Web nebo Worker), všechny databáze SQL Azure Microsoft nebo žádné Účty úložiště Microsoft Azure. Složitější scénáře bude vyžadovat příkazů v pracovních postupech nebo další zpracování služby "kolem okrajů" obchodování partnerskou smlouvu, který je před nebo po zpracování most Trading Partner smlouvy EDI. Podrobně následující pořadí událostí dojde během zprávu EDI zpracování ve službě BizTalk Services.

1. Zprávu EDI byl přijat z obchodního partnera, Fabrikam.  Pro příjem zpráv EDI z obchodními partnery, BizTalk Services podporuje protokoly s transportní například FTP, SFTP, AS2 a HTTP/S.
2. Provede zpětný překlad obchodním zpracování škálování na straně partnera smlouvy EDI zprávu formátu XML.  Na koncové body Service Bus jako koncový bod předávání přes Service Bus, téma sběrnice, frontou Service Bus nebo mostu BizTalk Services je možné směrovat bylo EDI zprávy (ve formátu XML).
3. Z koncového bodu pro další vlastní zpracování může pak přijat bylo zpráv XML.  Tyto koncové body nebylo možno zpracovat komponentu místně nebo do instance Microsoft Azure Compute pro zpracování zpráv ve službě Windows Workflow (WF) nebo Windows Communication Foundation (WCF), např.
4. "Zpracování na straně odesílání" z obchodního partnera smlouvy pak sestaví zprávy XML do formátu EDI a odešle ji do obchodním partnerem společnosti Contoso.  BizTalk Services pro odesílání zpráv EDI obchodním partnerům, podporuje stejné protokoly jako používaných pro příjem zpráv EDI.

Další tento dokument obsahuje koncepční pokyny na některé z různých artefaktů BizTalk serveru EDI migrace na službu BizTalk Services.

## <a name="sendreceive-ports-to-trading-partners"></a>Odesílání a přijímání porty do obchodních partnerů
BizTalk serveru, můžete nastavit přijímat umístění a Receive porty pro příjem zpráv EDI/XML z obchodními partnery a nastavíte odeslání porty pro odesílání zpráv EDI/XML pro obchodního partnera. Potom vytížit tyto porty, které se smlouvy s obchodním partnerem pomocí konzoly pro správu serveru BizTalk. Ve službě BizTalk Services, umístění, kde přijímat zprávy z obchodních partnerů a kde odesílání zpráv do obchodních partnerů jsou nakonfigurovány jako součást smlouvy obchodním partnerem samostatně (jako součást nastavení přenosu) na portálu služby BizTalk.  Proto není nutné skutečně koncepci "odesílání portů" a "přijímat umístění", samo o sobě, ve službě BizTalk Services. Další informace najdete v tématu [vytváření smlouvy](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx).

## <a name="pipelines-bridges"></a>Kanály (mostů)
Kanály v EDI BizTalk serveru, jsou entit zpracování zpráv, které může také obsahovat vlastní logiky pro zpracování specifické možnosti, podle potřeby aplikací. Služby BizTalk Services by ekvivalent EDI most. Ale ve službě BizTalk Services zatím mostů EDI "zavřeny".  To znamená nelze přidat vlastní aktivitu do mostu EDI. Všechny vlastní zpracování je třeba provést mimo most EDI ve vaší aplikaci, před nebo po zpráva zadá most nakonfigurovaný jako součást obchodování partnerskou smlouvu. Mosty EAI, máte možnost provést vlastní zpracování. Pokud chcete vlastní zpracování, můžete mosty EAI, před nebo po most EDI zpracovává zprávy. Další informace najdete v tématu [postupy zahrnují vlastní kód v mostů](https://msdn.microsoft.com/library/azure/dn232389.aspx).

Můžete vložit k publikování a přihlášení k odběru toku vlastního kódu a/nebo pomocí služby Service Bus zasílání zpráv fronty a témata před smlouvy obchodním partnerem obdrží zprávu, nebo po smlouvu zpracovává zprávy a směruje na koncový bod Service Bus.

V tématu **scénáře/zprávy toku** v tomto tématu vzoru toku zpráv.

## <a name="agreements"></a>Smlouvy
Pokud jste obeznámeni s BizTalk Server 2010 Trading Partner smlouvy použitý pro zpracování EDI, vyhledejte BizTalk Services smluv s obchodními partnery velmi dobře. Většina nastavení smlouvy jsou stejné a použít stejné technologiím. V některých případech jsou mnohem jednodušší nastavení smluv ve srovnání s stejné nastavení BizTalk serveru. Microsoft Azure BizTalk Services podporuje X12, EDIFACT a AS2 přenosu.

Služba Microsoft Azure BizTalk Services také poskytuje **migrace dat TPM** nástroj pro migraci obchodní partnery a smlouvy z modulu BizTalk serveru Trading Partner na portál služby BizTalk. Nástroj pro migraci dat čip TPM je k dispozici jako součást balíčku nástrojů, který si můžete stáhnout z [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057). Balíček obsahuje také soubor readme, který poskytuje pokyny o tom, jak používat nástroj a základní informace o odstraňování potíží pro nástroj.

## <a name="schemas"></a>Schémata
Služba BizTalk Services nabízí schémat EDI, které se dají použít v řešení BizTalk Services.  Kromě toho schémat BizTalk Server EDI lze také službou BizTalk Services protože do kořenového uzlu schémat EDI je stejná napříč BizTalk serveru, jakož i služba BizTalk Services. Proto bude možné přímo trvat vaší schémat EDI BizTalk serveru a použít je v EDI řešení, které vyvíjíte pomocí služby BizTalk Services. Můžete také stáhnout schémata z [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057).

## <a name="maps-transforms"></a>Mapy (transformací)
Mapy BizTalk serveru, se nazývají transformací ve službě BizTalk Services. Migrace mapy služby BizTalk Server do služby BizTalk Services může mít jednu z složitější úlohy k dosažení (v závislosti na složitosti mapy). Mapování nástroj, který slouží pro BizTalk Services se liší od BizTalk mapper. I když mapper vypadá většinou stejný, základní formát mapy se liší. Functoids (nazývá **mapy Operations** ve službě BizTalk Services) k dispozici pro uživatele se také liší.  V důsledku toho nelze použít přímo BizTalk mapy ve službě BizTalk Services. Ne všechny functoids k dispozici v BizTalk Server jsou také k dispozici jako mapy operace ve službě BizTalk Services.

### <a name="new-transform-operations"></a>Nové operace transformace.
Když se může zdát výrazně lišit od mapper BizTalk Server seznam operací na mapu transformace, které jsou k dispozici, transformuje služby BizTalk mít nové způsoby provádění úloh. Například transformuje služby BizTalk mít **operace výpisu** k dispozici. Nebyl k dispozici v BizTalk mapper.  **Operace výpisu** vám umožní vytvořit a provozovat na "Seznam", kde seznam je sada položek (také označované jako "řádky") a kde každá položka může mít více členů (také označované jako "sloupce").  Můžete seřaďte seznam, vyberte položky na základě podmínky, atd.

Další příklad nové funkce v BizTalk Services transformuje se **smyčky Operations**.  Je těžké vytvořit vnořené smyčky v mapper BizTalk Server.  Operace mapy smyčky jsou proto přidány pro transformuje služby BizTalk.

Ještě dalším příkladem je **If-potom Else** operace mapy výrazu.  Provádění operace if potom else bylo možné v BizTalk mapper, ale je vyžadován více functoids k provedení zdánlivě jednoduché úlohy.

### <a name="migrating-biztalk-server-maps"></a>Migrace serveru BizTalk mapy
Microsoft Azure BizTalk Services obsahuje nástroj pro migraci BizTalk Server mapuje transformací BizTalk Services. **BTMMigrationTool** je k dispozici jako součást **nástroje** součástí balíčku [stažení sady SDK služby BizTalk](http://go.microsoft.com/fwlink/p/?LinkId=235057). Další informace o tomto nástroji naleznete v tématu [převést mapu BizTalk k transformaci služby BizTalk](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx).

Můžete také prohlédnout ukázku Sandro Pereira, BizTalk MVP, o tom, jak [migrace map BizTalk Server do služby BizTalk Services transformací](http://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx).

## <a name="orchestrations"></a>Orchestrations
Pokud potřebujete migrovat BizTalk serveru orchestration zpracování do služby Microsoft Azure, orchestrations potřebovat třeba přepsat, protože Microsoft Azure nepodporuje spuštěné orchestrations BizTalk Server.  Může přepisování funkce orchestration ve službě Windows Workflow Foundation 4.0 (WF4).  To může být dokončení přepisování není aktuálně žádné migrace ze serveru BizTalk orchestrations WF4. Zde jsou některé prostředky pro pracovní postup systému Windows:

* [*Postup pro integraci služby pracovního postupu WCF témata a fronty služby Service Bus* ](https://msdn.microsoft.com/library/azure/hh709041.aspx) podle Paolo Salvatori. 
* [*Vytváření aplikací s Windows Workflow Foundation a Azure* relace](http://go.microsoft.com/fwlink/p/?LinkId=237314) z konference Build 2011.
* [*Centrum vývojářů pro Windows Workflow Foundation* ](http://go.microsoft.com/fwlink/p/?LinkId=237315) na webu MSDN.
* [*Dokumentaci k Windows Workflow Foundation 4 (WF4)* ](https://msdn.microsoft.com/library/dd489441.aspx) na webu MSDN.

## <a name="other-considerations"></a>Další důležité informace
Toto jsou několik aspekty, které musíte provést při používání služby BizTalk Services.

### <a name="fallback-agreements"></a>Záložní smlouvy
Zpracování BizTalk Server EDI obsahuje koncepci "Záložního smlouvy".  BizTalk Services nepodporuje **není** , pokud mají koncept záložního smlouvy.  Dokumentace tématech BizTalk [z smlouvy Role při zpracování EDI](http://go.microsoft.com/fwlink/p/?LinkId=237317) a [záložního smlouvy vlastnosti nebo konfigurace globální](https://msdn.microsoft.com/library/bb245981.aspx) informace o používání záložního smluv v BizTalk Server.

### <a name="routing-to-multiple-destinations"></a>Směrování do více cílů
BizTalk Services mostů v jejím aktuálním stavu nepodporuje směrování zpráv do více cílů pomocí publikovat-přihlášení k odběru modelu. Místo toho může směrovat zprávy ze služby BizTalk Services most do tématu Service Bus, který pak může mít několik odběrů pro příjem zprávy na více než jeden koncový bod.

## <a name="conclusion"></a>Závěr
Microsoft Azure BizTalk Services je aktualizován v pravidelných milníky přidat další funkce a možnosti. Při každé aktualizaci Těšíme podpory rozšířené funkce pro usnadnění vytváření řešení pro kompletní pomocí služby BizTalk Services a další technologie Azure.

## <a name="see-also"></a>Viz také
[Vývoj aplikací Enterprise s Azure](https://msdn.microsoft.com/library/azure/hh674490.aspx)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png
