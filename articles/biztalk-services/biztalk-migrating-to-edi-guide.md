---
title: Migrace řešení EDI z BizTalk serveru do BizTalk Services – technický průvodce | Dokumentace Microsoftu
description: Migrace EDI MABS; Microsoft Azure BizTalk Services
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
ms.openlocfilehash: aaa7028bb37ac4c2c313efce2afebc1dc5e814d2
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860074"
---
# <a name="migrating-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>Migrace řešení EDI z BizTalk serveru do BizTalk Services: technický průvodce

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Autora: Tim Wieman a Nitin Mehrotra

Recenzenti: Karthik Bharthy

Napsané s využitím: verze Microsoft Azure BizTalk Services – únor 2014.

## <a name="introduction"></a>Úvod
Elektronické výměny dat (EDI) je jedním z nejrozšířenější prostředky dat systému exchange které firmám elektronicky, také označován jako Business-to-Business nebo B2B transakce. BizTalk Server EDI podporu pro víc než dekádu, od počáteční verze BizTalk severu došlo. Microsoft pokračuje s BizTalk Services v podpory pro řešení EDI z na platformě Microsoft Azure. B2B transakce jsou většinou externí vůči organizaci, a proto je snadnější implementovat, pokud bylo implementováno na cloudové platformě. Microsoft Azure poskytuje tuto funkci služby BizTalk Services.

Zatímco některé zákazníků si prohlédne BizTalk Services jako platforma "úplně nové" nová řešení EDI, řada zákazníků má aktuální řešení EDI z BizTalk serveru, která může být vhodné k migraci do Azure. Protože BizTalk Services EDI je navržena podle stejných klíčů entit jako architektura EDI z BizTalk serveru (obchodní partneři, entity, smlouvy), je možné k migraci artefaktů EDI z BizTalk serveru do BizTalk Services.

Tento dokument popisuje některé rozdíly spojené s migraci artefaktů EDI z BizTalk serveru do BizTalk Services. Tento dokument předpokládá praktické znalosti zpracování EDI z BizTalk serveru a obchodních partnerů smlouvy. Další informace o EDI z BizTalk serveru najdete v tématu [Trading Partner pomocí BizTalk serveru pro správu](https://msdn.microsoft.com/library/bb259970.aspx).

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>Kterou verzi EDI z BizTalk serveru artefakty se dají migrovat do služby BizTalk Services?
Modul EDI z BizTalk serveru došlo k výraznému rozšíření pro BizTalk Server 2010, když se změna modelu partnerů, profilů a smlouvy. BizTalk Services používá stejný model pro uspořádání obchodními partnery a divizí v rámci těchto obchodními partnery. V důsledku toho migraci artefaktů EDI z BizTalk Server 2010 a novějších verzí do BizTalk Services, je mnohem více přímo dopředné proces. K migraci EDI artefakty spojené s verze starší než BizTalk Server 2010, musíte nejprve upgradovat na BizTalk Server 2010 a potom migrovat EDI artefaktů do BizTalk Services.

## <a name="scenariosmessage-flow"></a>Scénáře a zprávu toku
Jako BizTalk Server EDI zpracování ve službě BizTalk Services je postavené na řešení správy obchodních partnerů (TPM). TPM řešení má následující klíčové komponenty:

* Obchodní partneři, které představují organizaci v rámci transakce B2B.
* Profily, které představují divizemi v rámci obchodního partnera.
* Obchodní partner smlouvy (nebo smluv), které představují obchodní případ nevztahuje Smlouva mezi dva partneři a profily.

Následující obrázek znázorňuje podobnosti, jakož i rozdíly mezi řešení EDI z BizTalk serveru a BizTalk Services EDI řešení:

![][EDImessageflow]

Hlavní rozdíly a podobnosti mezi tok, který řešení EDI BizTalk serveru a BizTalk Services jsou:

* Stejně jako BizTalk Server používá kanál EDIReceive na zprávu EDI a kanál EDISend k odeslání zprávy EDI, BizTalk Services používá EDI přijímat bridge pro příjem a odesílání EDI mostu k odesílání zpráv EDI. BizTalk serveru kanály jsou spojeny se smlouvou pomocí odesílání nebo příjmu porty. Ve službě BizTalk Services smlouvu samotné označuje odesílání nebo příjmu most.
* Po EDIReceive kanál zpracování zpráv EDI, BizTalk serveru vypsána zprávy k databázi SQL serveru. Kanál EdiSend pak převezme zprávy z databáze SQL serveru, zpracovává je a odesílá je do obchodního partnera.
  
    Ve službě BizTalk Services po EDI přijímat most zpracovává zprávy EDI, směruje zprávy k externímu procesu. Externí proces může běžet na Microsoft Azure nebo místně. Externí proces by měl zprávy směruje do most odeslat EDI; Most odeslat ze své podstaty nestahuje zprávy. Po zpracování zprávy, směruje most EDI odeslat zprávu do obchodního partnera.

BizTalk Services umožňují snadno použít konfiguraci prostředí rychle vytvořit a nasadit smlouvy s B2B mezi obchodními partnery, bez jakékoli Microsoft Azure Compute konfigurace instance (webové nebo pracovní role), všechny databáze Microsoft Azure SQL nebo některý Účet úložiště Microsoft Azure. Složitější scénáře budou vyžadovat propojí pracovní postupy nebo další zpracování služby "kolem okraje" Trading Partner smlouvy před nebo po zpracování most obchodní Partner smlouvy EDI. Podrobně následující posloupnosti událostí během zpracování ve službě BizTalk Services zpráv EDI.

1. Přijme zprávu EDI z obchodní partner, společnost Fabrikam.  Pro příjem zpráv EDI z obchodní partneři, služby BizTalk Services podporují přenosové protokoly, jako je například FTP, SFTP, AS2 a HTTP/S.
2. Obchodní partner zpracování škálování na straně smlouvy zpětně přeloží zpráv EDI do formátu XML.  Zpětně přeložený zpráv EDI (ve formátu XML) lze směrovat na koncové body služby Service Bus jako koncový bod Service Bus Relay, téma služby Service Bus, frontu služby Service Bus nebo Most BizTalk Services.
3. Zpětně přeložený zprávy XML může být přijata pak z koncového bodu pro další vlastní zpracování.  Tyto koncové body může být zpracována jako součást v místním nebo instance Microsoft Azure Compute pro zpracování zpráv ve službě Windows pracovního postupu (WF) nebo Windows Communication Foundation (WCF), třeba.
4. "Zpracování na straně odesílání" z obchodního partnera smlouvy poté sestaví zprávy XML do formátu EDI a odesílá je do obchodního partnera společnosti Contoso.  Služby BizTalk Services pro odesílání zpráv EDI obchodní partneři, podporují stejné protokoly používané pro příjem zpráv EDI.

Tento dokument dále poskytuje koncepční pokyny na migraci některé jiné artefakty EDI z BizTalk serveru do BizTalk Services.

## <a name="sendreceive-ports-to-trading-partners"></a>Posílání a přijímání porty na obchodními partnery
V BizTalk serveru nastavíte umístění příjmu a Receive porty pro příjem zpráv EDI a XML z obchodními partnery a nastavíte odeslání portů pro odesílání zpráv EDI a XML do obchodního partnera. Potom vytížit tyto porty pro smlouvy s obchodním partnerem pomocí konzoly pro správu BizTalk serveru. Ve službě BizTalk Services, umístění, kam budete dostávat zprávy z obchodních partnerů a kam poslat zprávy obchodními partnery jsou nakonfigurované v rámci obchodní partnerskou smlouvu samostatně (jako součást přenosu nastavení) v portálu BizTalk Services.  Proto není nutné ve skutečnosti koncept "send porty" a "zobrazí umístění", samo o sobě, ve službě BizTalk Services. Další informace najdete v tématu [vytváření smluv](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx).

## <a name="pipelines-bridges"></a>Kanály (přemostění)
Kanály v EDI z BizTalk serveru, jsou entity zpracování zpráv, které může také obsahovat vlastní logiku zpracování specifické pro funkce, podle potřeb aplikace. Pro služby BizTalk Services by ekvivalent most EDI. Ale ve službě BizTalk Services teď mosty EDI jsou "uzavřený".  To znamená nelze přidat vlastní vlastní aktivity do mostu EDI. Všechny vlastní zpracování musí provést mimo most EDI ve vaší aplikaci před nebo po zprávy zadá most nakonfigurovaný jako součást obchodování partnerskou smlouvu. Mosty EAI máte možnost provést vlastní zpracování. Pokud chcete vlastní zpracování, můžete použít přemostění EAI, před nebo po zpracovává zprávy EDI mostu. Další informace najdete v tématu [jak zahrnout vlastní kód v mosty](https://msdn.microsoft.com/library/azure/dn232389.aspx).

Můžete vložit tok publikování nebo odběr pomocí vlastního kódu nebo použití předtím, než smlouvy s obchodním partnerem obdrží zprávu, nebo po její zprávu zpracuje a směruje na koncový bod služby Service Bus zasílání zpráv front a témat Service Bus.

V tématu **tok scénáře/zprávy** v tomto tématu pro vzor tok zpráv.

## <a name="agreements"></a>Smlouvy
Pokud jste se seznámili s BizTalk Server 2010 Trading Partner smlouvy používá ke zpracování EDI, pak BizTalk Services smluv s obchodními partnery velmi povědomá. Většinu nastavení smlouvy jsou stejné a použít stejné terminologie. V některých případech jsou mnohem jednodušší nastavení smlouvy ve srovnání s stejné nastavení BizTalk serveru. Microsoft Azure BizTalk Services podporuje X12 a EDIFACT, AS2 přenosu.

Microsoft Azure BizTalk Services také poskytuje **migrace dat TPM** nástroj pro migraci obchodních partnerů a smluv z modulu BizTalk Server Trading Partner k portálu BizTalk Services. Nástroj pro migraci dat čip TPM je k dispozici jako součást balíčku nástrojů, který si můžete stáhnout z [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057). Balíček obsahuje také soubor readme, který poskytuje pokyny o tom, jak použít nástroje a základní informace o odstraňování potíží pro nástroj.

## <a name="schemas"></a>Schémata
BizTalk Services zajišťují funkce schémat EDI, které je možné použít v řešeních služby BizTalk Services.  Navíc schémat EDI z BizTalk serveru můžete také použít s BizTalk Services protože kořenový uzel schémat EDI je stejná napříč BizTalk serveru a BizTalk Services. Proto bude možné přímo využít vaše schémat EDI z BizTalk serveru a používat je v řešení EDI, které vyvíjíte pomocí BizTalk Services. Můžete si také stáhnout schémata z [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057).

## <a name="maps-transforms"></a>Maps (transformace)
Mapování v BizTalk serveru se nazývají transformací v BizTalk Services. Migrace map z BizTalk serveru do BizTalk Services můžou mít jednu z složitější úlohy k dosažení (v závislosti na složitosti mapy). Mapování nástroj používaný pro služby BizTalk Services se liší od Mapovač BizTalk. I v případě, Mapovač vypadá převážně stejně, se liší základní formát mapování. Functoids (volá **mapy Operations** v BizTalk Services) k dispozici pro uživatele se také liší.  V důsledku toho nelze použít přímo BizTalk map ve službě BizTalk Services. Také ne všechny functoids dostupné v BizTalk serveru jsou k dispozici jako operace map ve službě BizTalk Services.

### <a name="new-transform-operations"></a>Nové operace transformace.
Zatímco seznam operací mapy transformace, které jsou k dispozici se může zdát zcela liší od Mapovač BizTalk Server, transformuje BizTalk Services mají nových způsobech provádění úloh. Například BizTalk Services transformuje mít **operace výpisu** k dispozici. Nebyl k dispozici v mapování BizTalk.  **Operace výpisu** vám umožní vytvořit a pracovat v "Seznamu", kde je seznam sadu položek (označované také jako "řádků") a každá položka může mít více členů (označované také jako "sloupce").  Můžete seřadit, vyberte položky podle stavu, atd.

Další příklad nové funkce transformuje BizTalk Services jsou **smyčky operace**.  Je těžké vytvořit vnořené smyčky v mapování BizTalk serveru.  Díky tomu se operace mapy smyčky jsou přidány pro transformace BizTalk Services.

Dalším příkladem je, ale **If-Then-Else** operaci mapování výrazů.  If-then-else operace to bylo možné v mapování BizTalk, ale vyžaduje více functoids či provést určitý úkol zdánlivě jednoduché.

### <a name="migrating-biztalk-server-maps"></a>Mapování migrace BizTalk serveru
Microsoft Azure BizTalk Services zajišťují, že nástroj pro migraci BizTalk serveru se mapuje na transformace BizTalk Services. **BTMMigrationTool** je k dispozici jako součást **nástroje** balíčku, opatřeného [stáhnout sadu SDK pro BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkId=235057). Další informace o tomto nástroji naleznete v tématu [převést BizTalk mapy pro transformaci BizTalk Services](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx).

Můžete se také podívat na ukázku Sandro Pereira, MVP pro BizTalk, o tom, jak [migrace mapy BizTalk serveru do BizTalk Services transformace](http://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx).

## <a name="orchestrations"></a>Orchestrace
Pokud je potřeba migrovat BizTalk Server Orchestrace zpracování do Microsoft Azure, Orchestrace bude nutné, aby se povolilo, protože Microsoft Azure nepodporuje spuštěné Orchestrace BizTalk serveru.  Může přepsat funkci Orchestrace ve službě Windows Workflow Foundation 4.0 (WF4).  To může být kompletní revize, protože není aktuálně žádné migrace z Orchestrace BizTalk serveru do WF4. Tady jsou některé prostředky pracovního postupu Windows:

* [*Postup při integraci služby pracovního postupu WCF pomocí front Service Bus a témat* ](https://blogs.msdn.microsoft.com/paolos/2013/04/09/how-to-integrate-a-wcf-workflow-service-with-service-bus-queues-and-topics/) podle Paolo Salvatori. 
* [*Vytváření aplikací pomocí Windows Workflow Foundation a Azure* relace](http://go.microsoft.com/fwlink/p/?LinkId=237314) z konference Build 2011.
* [*Středisko pro vývojáře aplikace Windows Workflow Foundation* ](http://go.microsoft.com/fwlink/p/?LinkId=237315) na webové stránce MSDN.
* [*Dokumentaci k Windows Workflow Foundation 4 (WF4)* ](https://msdn.microsoft.com/library/dd489441.aspx) na webové stránce MSDN.

## <a name="other-considerations"></a>Další důležité informace
Následuje několik důležitých informací, které je nutné provést při používání služby BizTalk Services.

### <a name="fallback-agreements"></a>Použití náhradní lokality smlouvy
Zpracování EDI z BizTalk serveru nemá koncept "Záložní smlouvy".  BizTalk Services nemá **není** zatím jste koncept smlouvu pro použití náhradní lokality.  Najdete v tématech dokumentace ke službě BizTalk [ze smluv roli při zpracování EDI](http://go.microsoft.com/fwlink/p/?LinkId=237317) a [Konfigurace globálních nebo vlastnosti smlouvy záložní](https://msdn.microsoft.com/library/bb245981.aspx) informace o používání záložního smluv v BizTalk Server.

### <a name="routing-to-multiple-destinations"></a>Směrování do více cílů
Mosty BizTalk Services v jejím aktuálním stavu nepodporuje směrování zpráv do více cílů pomocí publikování-odběru modelu. Místo toho může směrovat zprávy z most BizTalk Services do tématu Service Bus, které pak může obsahovat víc předplatných pro příjem zprávy na víc než jednom koncovém bodě.

## <a name="conclusion"></a>Závěr
Microsoft Azure BizTalk Services se aktualizuje v pravidelných milníky, chcete-li přidat další funkce a možnosti. Při každé z nich Těšíme se podporuje rozšířené funkce pro usnadnění vytváření začátku do konce řešení využívající BizTalk Services a jiné technologie Azure.

## <a name="see-also"></a>Viz také
[Vývoj podnikových aplikací s využitím Azure](https://msdn.microsoft.com/library/azure/hh674490.aspx)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png
