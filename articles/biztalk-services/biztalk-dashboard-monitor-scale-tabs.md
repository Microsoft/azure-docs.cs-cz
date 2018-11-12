---
title: Konfigurace řídicího panelu, monitorování, škálování a Hybrid Connections ve službě BizTalk Services | Dokumentace Microsoftu
description: Další informace o ovládacích prvcích a monitorování výkonu pro služby BizTalk Services
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 7a1815db-0de2-4274-8be0-198c1b077324
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 3f4763b5e15d4b9b84e868262a9e8538b8a407a2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228823"
---
# <a name="review-the-dashboard-monitor-scale-configure-and-hybrid-connection-tabs"></a>Kontrola karet řídicího panelu, monitorování, škálování, konfigurace a hybridní připojení

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Po vytvoření služby BizTalk a nasazení vaší aplikace, můžete změnit některá nastavení služby BizTalk a monitorování výkonu aplikací. 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Otevře se nové okno s následujícími kartami. Toto téma popisuje tyto karty.

## <a name="quickstart-quickstartquickstart"></a>(Rychlý start![Rychlý start][Quickstart])
V závislosti na edici BizTalk Services nemusí být k dispozici všechny možnosti uvedené. 

<table border="1">
    <tr>
        <td><strong>Získání nástrojů</strong></td>
        <td>Stáhněte sadu SDK služby BizTalk pro instalaci šablony projektů Visual Studio na vašem místním vývojovém počítači. Vytvořte tyto šablony <strong>BizTalk Services</strong> (Most) a <strong>artefaktů služby BizTalk</strong> projektů sady Visual Studio (transformace), které jsou nasazeny do vaší služby BizTalk.
        <br/><br/>
        <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=302335"> Jak začít používat sadu SDK Azure BizTalk Services </a> a <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=241589">nainstalovat sadu SDK Azure BizTalk Services</a> jsou uvedené kroky, abyste mohli začít.
        </td>
    </tr>
    <tr>
        <td><strong>Vytvořte partnerské smlouvy</strong></td>
        <td>Otevření portálu pro Azure BizTalk Services hostované v Azure, kde můžete přidat partnery a vytvořit X12, AS2 a smluv EDIFACT EDI.
        <br/><br/>
        <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=303653">Konfigurace součástí pro zasílání zpráv EDI na portálu BizTalk Services</a> jsou uvedené kroky, abyste mohli začít.
        </td>
    </tr>

<tr>
        <td><strong>Další informace o službě BizTalk Services</strong></td>
        <td>Přejděte <a HREF="https://azure.microsoft.com/documentation/services/biztalk-services/">výukové centrum</a> získat další informace o Azure BizTalk Services.</td>
</tr>
</table>


Na hlavním panelu v dolní části můžete:

<table border="1">

<tr>
<td><strong>Správa</strong> nasazení vaší aplikace</td>
<td>Otevře se na portálu Azure BizTalk Services. Portálu BizTalk Services je vstupem konfigurace EDI, včetně přidání partnerů a vytvoření X12, AS2 a smlouvy EDIFACT.
<br/><br/>
To je stejný jako <strong>vytvořte partnerské smlouvy</strong> na <strong>rychlý Start</strong> kartu.
<br/><br/>
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=303653">Konfigurace součástí pro zasílání zpráv EDI na portálu BizTalk Services</a> poskytuje další informace o portálu BizTalk Services.</td>
</tr>

<tr>
<td><strong>Informace o připojení</strong> z Namespace řízení přístupu</td>
<td>Když vyberete informace o připojení, pak se zobrazí Namespace řízení přístupu, výchozí vystavitele a výchozí klíč. Tyto hodnoty můžete zkopírovat.
<br/><br/>
Můžete také otevřít portál řízení přístupu. <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=285670">Vytvoření ovládacího prvku přístup Namespace</a> poskytuje další informace o portálu řízení přístupu.</td>
</tr>

<tr>
<td><strong>Synchronizovat klíče</strong> v účtu úložiště</td>
<td>Při vytváření účtu služby Storage se automaticky vytvoří primární a sekundární klíč. Těchto šifrovacích klíčů řídit přístup k vašemu účtu úložiště. Vaše služba BizTalk automaticky používá primární klíč. <strong>Synchronizovat klíče</strong> povolit uživatelům přepínat mezi primární klíč a sekundární klíče bez přerušení služby BizTalk.
<br/><br/>
Například chcete, aby služba BizTalk používat nový primární klíč pro účet úložiště. Použijte následující postup:
<br/><br/>
<ol>
<li>Vyberte svoji službu BizTalk a vyberte <strong>synchronizovat klíče</strong>. Vyberte sekundární klíč. Když toto provedete, spuštění služby BizTalk pomocí sekundárního klíče.</li>
<li>Vyberte svůj účet úložiště a znovu vygenerovat primární klíč. Nezapomeňte, že vaše služba BizTalk používá sekundární klíč.</li>
<li>Vyberte svoji službu BizTalk a vyberte <strong>synchronizovat klíče</strong>. Teď vyberte primární klíč. Toto je nový primární klíč, můžete se znova vygeneroval.</li>
<li>Vyberte svůj účet úložiště a znovu vygenerovat sekundární klíč.</li>
</ol>
<br/>
Tento proces se nazývá "vyměnit klíče". Účelem je umožnit uživatelům přepínat mezi primární klíč a sekundární klíč bez přerušení služby BizTalk.</td>
</tr>

<tr>
<td><strong>Odstranit</strong> vaší aplikace</td>
<td>Když vyberete odstranit, vaše služba BizTalk a odeberou se všechny položky v něm nasazené.</td>
</tr>
</table>


## <a name="dashboard"></a>Řídicí panel
V závislosti na edici BizTalk Services nemusí být k dispozici všechny možnosti uvedené. 

Když vyberete název vaší služby BizTalk, se zobrazí karta řídicí panel. Na řídicím panelu můžete:

##### <a name="usage-overview-shows-the-number-of-used-hybrid-connections"></a>Přehled využití: Zobrazuje počet použitých hybridních připojení
Také zobrazuje využití dat v GB. 

##### <a name="metric-graph-shows-a-fixed-list-of-performance-metrics"></a>Graf metrik: Ukazuje pevně daný seznam metrik výkonu
Tyto metriky zadat v reálném čase hodnoty ohledně kondice službu BizTalk. Můžete také **relativní** nebo **absolutní** hodnoty a časový rozsah **Interval** metrik, které jsou zobrazeny v grafu. 

Popis tyto metriky výkonu, přejděte na [dostupné metriky](#Metrics) v tomto tématu.

##### <a name="quick-glance-lists-your-biztalk-service-properties"></a>Rychlý přehled: Seznamy vlastností vaší služby BizTalk
<table border="1">

<tr>
<td><strong>Aktualizovat přihlašovací údaje databáze sledování</strong></td>
<td>Uživatelské jméno a heslo použili k přihlášení do databáze sledování se změní.</td>
</tr>
<tr>
<td><strong>Nahrajte certifikát SSL</strong></td>
<td>Můžete aktualizovat službu BizTalk používat jiný certifikát SSL. Certifikát SSL podepsaný svým držitelem je automaticky vytvořena, když jste <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=302280">vytvoření služby BizTalk</a>.</td>
</tr>
<tr>
<td><strong>Stáhnout certifikát</strong></td>
<td>Můžete stáhnout certifikát SSL používaný službou BizTalk do místního počítače.</td>
</tr>
<tr>
<td><strong>Stav</strong></td>
<td>Zobrazí aktuální stav vaší služby BizTalk. Zobrazit <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=329870">BizTalk Services: Tabulka stavu služeb</a>. </td>
</tr>
<tr>
<td><strong>Adresa URL služby</strong></td>
<td>Adresa URL pro vaši službu BizTalk. To je stejný jako <strong>adresa URL domény</strong> zadá, když vaše služba BizTalk vytvořená.</td>
</tr>
<tr>
<td><strong>Veřejné virtuální IP (VIP) Adresa</strong></td>
<td>IP adresu přiřazenou k vaší službě BizTalk. Používá se pro všechny vstupní koncové body a je zdrojovou adresu pro odchozí provoz. Tato IP adresa patří vaše služba BizTalk tak dlouho, dokud se vytvoří. Při odstranění služby BizTalk, IP adresa se přiřadí do jiné služby BizTalk.</td>
</tr>
<tr>
<td><strong>Namespace ACS</strong></td>
<td>Ověřuje se službou BizTalk.</td>
</tr>
<tr>
<td><strong>Edice</strong></td>
<td>Seznamy edice zadali při vytvoření služby BizTalk.</td>
</tr>
<tr>
<td><strong>Umístění</strong></td>
<td>Zobrazí zeměpisné oblasti, který je hostitelem služby BizTalk.</td>
</tr>
<tr>
<td><strong>Vytvoření</strong></td>
<td>Zobrazí datum a čas vytvoření služby BizTalk.</td>
</tr>
<tr>
<td><strong>Sledování databáze</strong></td>
<td>Název databáze SQL Azure, která ukládá sledovacích tabulek, kterou vaše služba BizTalk používat. 
<br/><br/>
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=302280">Požadavky na Explained</a> poskytuje podrobné informace o sledování databáze.</td>
</tr>
<tr>
<td><strong>Monitorování/archivaci úložiště</strong></td>
<td>Název účtu služby Azure Storage, který ukládá výstup monitorování vaší služby BizTalk.
<br/><br/>
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=302280">Požadavky na Explained</a> poskytuje podrobné informace o účtu úložiště.</td>
</tr>
<tr>
<td><strong>Název předplatného</strong></td>
<td>Zobrazí seznam odběr, který je hostitelem služby BizTalk. Předplatné se řídí přístup.</td>
</tr>
<tr>
<td><strong>ID předplatného</strong></td>
<td>Po vytvoření předplatného a ID předplatného není automaticky vygenerován. Při použití rozhraní REST API, budete muset zadat ID předplatného.</td>
</tr>
</table>

[BizTalk Services: Zřízení](https://go.microsoft.com/fwlink/p/?LinkID=302280) obsahuje kroky k vytvoření služby BizTalk.

##### <a name="manage-connection-information-sync-keys-and-delete-in-the-task-bar"></a>Spravovat informace o připojení, synchronizovat klíče a odstranění na hlavním panelu:
<table border="1">

<tr>
<td><strong>Správa</strong> nasazení vaší aplikace</td>
<td>Otevře se na portálu Azure BizTalk Services. Portálu BizTalk Services je vstupem konfigurace EDI, včetně přidání partnerů a vytvoření X12, AS2 a smlouvy EDIFACT.
<br/><br/>
To je stejný jako <strong>vytvořte partnerské smlouvy</strong> na <strong>rychlý Start</strong> kartu.
<br/><br/>
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=303653">Konfigurace součástí pro zasílání zpráv EDI na portálu BizTalk Services</a> poskytuje další informace o portálu BizTalk Services.</td>
</tr>
<tr>
<td><strong>Informace o připojení</strong> z Namespace řízení přístupu</td>
<td>Zobrazí Namespace řízení přístupu, výchozí vystavitele a klíč výchozí hodnoty. které je možné zkopírovat.
<br/><br/>
Můžete také otevřít portál řízení přístupu. Tento portál řízení přístupu je stejné jako při použití služby Active Directory možnost v levém navigačním podokně.
<br/><br/>
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=285670">Správa vašeho Namespace ACS</a> poskytuje další informace o portálu řízení přístupu.</td>
</tr>
<tr>
<td><strong>Synchronizovat klíče</strong> v účtu úložiště</td>
<td>Při vytváření účtu služby Storage se automaticky vytvoří primární a sekundární klíč. Těchto šifrovacích klíčů řídit přístup k vašemu účtu úložiště. Vaše služba BizTalk automaticky používá primární klíč. <strong>Synchronizovat klíče</strong> povolit uživatelům přepínat mezi primární klíč a sekundární klíče bez přerušení služby BizTalk.
<br/><br/>
Například chcete, aby služba BizTalk používat nový primární klíč pro účet úložiště. Použijte následující postup:
<br/><br/>
<ol>
<li>Vyberte svoji službu BizTalk a vyberte <strong>synchronizovat klíče</strong>. Vyberte sekundární klíč. Když toto provedete, spuštění služby BizTalk pomocí sekundárního klíče.</li>
<li>Vyberte svůj účet úložiště a znovu vygenerovat primární klíč. Nezapomeňte, že vaše služba BizTalk používá sekundární klíč.</li>
<li>Vyberte svoji službu BizTalk a vyberte <strong>synchronizovat klíče</strong>. Teď vyberte primární klíč. Toto je nový primární klíč, můžete se znova vygeneroval.</li>
<li>Vyberte svůj účet úložiště a znovu vygenerovat sekundární klíč.</li>
</ol>
<br/>
Tento proces se nazývá "vyměnit klíče". Účelem je umožnit uživatelům přepínat mezi primární klíč a sekundární klíč bez přerušení služby BizTalk.</td>
</tr>

<tr>
<td><strong>Odstranit</strong> vaší aplikace</td>
<td>Vaše služba BizTalk a nasadili do ní všechny položky se odeberou.</td>
</tr>
</table>


## <a name="monitor"></a>Monitorování
Nevztahuje se na edici Free.

Když vyberete název vaší služby BizTalk, je k dispozici na kartě monitorování a zobrazí následující informace:

##### <a name="metric-graph-displays-the-selected-performance-metrics"></a>Graf metrik: Zobrazí vybrané výkonové metriky
Tyto metriky zadat v reálném čase hodnoty ohledně kondice službu BizTalk. Rozhodnete, jaké metriky výkonu se zobrazí. Současně lze zobrazit až šest metrik výkonu. 

Můžete také **relativní** nebo **absolutní** hodnoty a časový rozsah **Interval** metrik, které jsou zobrazeny. 

##### <a name="to-remove-or-display-metrics-in-the-graph"></a>Odebrání nebo zobrazení metrik v grafu:
1. Vyberte **monitorování** kartu.
2. Vyberte **přidat metriky** na hlavním panelu:  
   ![Výběr možnosti Přidat metriky][AddMetrics]
3. Zkontrolujte metriky výkonu, které chcete zobrazit.
4. Vyberte značku zaškrtnutí vrátit **monitorování** kartu.
5. Vyberte kroužek vedle metrika k zobrazení hodnoty tuto metriku v grafu.  
   
    Například **využití procesoru** metrika je zobrazena šedě; není v grafu zobrazí jeho výstup:  
   ![Metrika využití procesoru je zobrazena šedě][GrayedMetric]  
   
    Vyberte si kruh, aby šedě **využití procesoru** metriky zobrazíte jeho výstup v grafu:  
   ![Metrika využití procesoru je povolená.][EnabledMetric]
6. Metrika odebrat ze zobrazení grafu a ze seznamu, vyberte **metriku odstranit** na hlavním panelu. Chcete-li přidat metriky zpět do seznamu, **přidat metriky** na hlavním panelu, zkontrolujte metriku a vyberte značku zaškrtnutí vrátit **monitorování** kartu. Vyberte potlačených kruh umožňující metriku.

## <a name="Metrics"></a>Dostupné metriky
K dispozici jsou následující čítače výkonu a metriky:

<table border="1">

<tr>
<td><strong>Latence RountdTrip</strong></td>
<td>Průměrná doba v milisekundách (ms), zpracování zprávu od doby, že dokud zprávy budou plně zpracovány službou BizTalk doručení zprávy zobrazí ve všech přemostění. Započítávají jenom zprávy byl úspěšně zpracován.<br/><br/>
Když dojde k následujícím událostem, vytvoří se časové razítko:
<ul>
<li>Zpráva přejde do brány</li>
<li>Zpráva směruje do cíle</li>
<li>Přijetí odpovědi cíl</li>
<li>Odeslání do brány cílový potvrzení odpovědi.</li>
</ul>
<br/>
Tato metrika zobrazuje výsledek výpočtu následující:
<br/><br/>
[Cílové potvrzení odeslání odpovědi k bráně] - [brána zadá zprávy]</td>
</tr>
<tr>
<td><strong>Chyby ve zdroji</strong></td>
<td>Zobrazí celkový počet zpráv, které se nezdařily službou BizTalk při přijetí zprávy z koncových bodů zdroje.</td>
</tr>
<tr>
<td><strong>Využití procesoru</strong></td>
<td>Uvádí průměrnou % času procesoru všech instancí rolí.</td>
</tr>
<tr>
<td><strong>Latence zpracování</strong></td>
<td>Průměrná doba v milisekundách (ms) ke zpracování zprávy službou BizTalk zobrazí ve všech mosty odečtení času stráveného cíle. Započítávají jenom zprávy byl úspěšně zpracován.<br/><br/>
Každý z následujících událostí dojde, časové razítko vytvoření:

<ul>
<li>Zpráva přejde do brány</li>
<li>Zpráva směruje do cíle</li>
<li>Přijetí odpovědi cíl</li>
<li>Odeslání do brány cílový potvrzení odpovědi.</li>
</ul>
<br/>Tato metrika zobrazuje výsledek výpočtu následující:<br/><br/>
[Cílové potvrzení odeslání odpovědi k bráně] - [zprávy zadá brány] - [přijetí odpovědi cílové] + [se zpráva směruje do cílového umístění]</td>
</tr>
<tr>
<td><strong>Selhání v procesu</strong></td>
<td>Zobrazí celkový počet zpráv, které selhaly během zpracování službou BizTalk přes všechny mosty v časovém intervalu.</td>
</tr>
<tr>
<td><strong>Zprávy odeslané</strong></td>
<td>Zobrazí celkový počet zpráv odeslaných službou BizTalk přes všechny mosty v časovém intervalu. Tato metrika se zvýší, když zpráva odeslaná z kanálu dosáhne cílové trasy. Tato metrika neznamená, že zpráva se úspěšně zpracuje.<br/><br/>
V případě požadavku a odpovědi metrika se zvýší, když cíl trasy přijetí potvrzení odešle zpět do kanálu.</td>
</tr>
<tr>
<td><strong>Přijaté zprávy</strong></td>
<td>Zobrazí celkový počet zpráv přijatých službou BizTalk přes všechny mosty v časovém intervalu. Tato metrika se zvýší, když přijme novou zprávu kanálu.</td>
</tr>
<tr>
<td><strong>Zprávy v procesu</strong></td>
<td>Zobrazí celkový počet zpráv aktuálně zpracovává služby BizTalk v časovém intervalu.</td>
</tr>
<tr>
<td><strong>Zpracované zprávy</strong></td>
<td>Zobrazí celkový počet zpráv úspěšně zpracovaných službou BizTalk přes všechny mosty v časovém intervalu. Tato metrika se zvýší, když zpráva se úspěšně přijal kanál a úspěšně směrovat do cíle.</td>
</tr>
</table>


## <a name="scale"></a>Měřítko
Na kartě škálování můžete přidat nebo odečíst počet jednotek, kterou vaše služba BizTalk používat. Ve výchozím nastavení je jedna jednotka nakonfigurována. Lze přidat další jednotky škálování služby BizTalk. Pokud zvýšíte škálování, se zvyšuje propustnost. Objem prostředků také zvyšuje, včetně nasazených přemostění, smlouvy, obchodní připojení a výpočetní výkon. Například můžete zvýšit škálování od 1 jednotka až 2 jednotky. V takovém případě můžete nasadit double počet mostů, dvakrát smlouvy, dvakrát obchodní připojení a poklikejte na výpočetním výkonu.

Některé edice BizTalk nenabízí možnost škálování. V takovém případě je povolený jednu jednotku. Pokud chcete zjistit, kolik jednotek je možné škálovat vaši verzi, přečtěte si [BizTalk Services: Tabulka edic](biztalk-editions-feature-chart.md).

Zvýšením počtu jednotek může mít vliv na ceny. Pokud zvýšíte jednotky, vyberete **Uložit** zobrazí zpráva s oznámením, pokud se to týká fakturace. Potom můžete pokračovat. Pokud zvýšíte počet jednotek, změny stavu služby BizTalk z aktivní na aktualizace. Ve stavu aktualizace vaše služba BizTalk zůstane spuštěný.

[BizTalk Services: Tabulka edic](biztalk-editions-feature-chart.md) definuje "Jednotka".

## <a name="configure"></a>Konfigurace
Nevztahuje se na hybridní připojení.

Nastaví stav zálohování na hodnotu None nebo automaticky. Pokud je nastavený na hodnotu None, žádné zálohy se vytvoří automaticky. Pokud je nastavený na hodnotu automaticky, nakonfigurujte umístění zálohy, frekvenci zálohování a jak dlouho udržovat záložní soubory. 

[BizTalk Services: Zálohování a obnovení](biztalk-backup-restore.md) nabízí podrobné informace. 

## <a name="HybridConnections"></a>Hybridní připojení
Hybridní připojení aplikací Azure, jako jsou Web Apps nebo Mobile Apps ve službě Azure App Service, připojení k místnímu prostředku, který používá statický port TCP, jako je například SQL Server, MySQL, webové rozhraní API HTTP a většina vlastních webových služeb. Hybridní připojení se spravují v BizTalk Services.

Pokud chcete vytvářet a spravovat hybridní připojení Azure BizTalk Services, přečtěte si téma [Hybrid Connections](integration-hybrid-connection-overview.md).

## <a name="next"></a>Další
Teď, když jste obeznámeni s různých kartách, můžete další informace o funkcích služby Azure BizTalk Services:

* [BizTalk Services: Omezování](biztalk-throttling-thresholds.md)  
* [BizTalk Services: Název a klíč vystavitele](biztalk-issuer-name-issuer-key.md)  
* [BizTalk Services: Zálohování a obnovení](biztalk-backup-restore.md)

## <a name="see-also"></a>Viz také
* [Hybridní připojení](integration-hybrid-connection-overview.md)  
* [BizTalk Services: Developer, Basic, Standard a Premium tabulka edic](biztalk-editions-feature-chart.md)  
* [BizTalk Services: zřízení](biztalk-provision-services.md)  
* [BizTalk Services: Tabulka stavu služby BizTalk](biztalk-service-state-chart.md)  
* [Jak začít používat sadu SDK Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302335)

[Quickstart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png

