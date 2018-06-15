---
title: Řídicí panel, sledování, měřítko, konfigurovat a hybridní připojení ve službě BizTalk Services | Microsoft Docs
description: Další informace o ovládací prvky a sledovat výkon služby BizTalk Services
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
ms.openlocfilehash: 351809cd5f165a863dc02bfadf78fa59cbaabfd7
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2017
ms.locfileid: "24103357"
---
# <a name="review-the-dashboard-monitor-scale-configure-and-hybrid-connection-tabs"></a>Kontrola karet řídicího panelu, monitorování, škálování, konfigurace a hybridní připojení

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Po vytvoření služby BizTalk a nasazení aplikace, můžete změnit některá nastavení služby BizTalk a monitorování výkonu aplikací. 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Otevře se nové okno s následujícími kartami. Toto téma popisuje těchto karet.

## <a name="quickstart-quickstartquickstart"></a>(Rychlý start![Rychlý start][Quickstart])
V závislosti na edici služby BizTalk nemusí být k dispozici všechny možnosti, které jsou uvedené. 

<table border="1">
    <tr>
        <td><strong>Získat nástroje</strong></td>
        <td>Stáhněte si sadu SDK BizTalk Services nainstalovat šablony projektů Visual Studio na vývojovém počítači místně. Tyto šablony vytvořit <strong>BizTalk Services</strong> (mostu) a <strong>artefaktů služby BizTalk</strong> projektů sady Visual Studio (transformace), které jsou nasazeny do vaší služby BizTalk.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335">Jak spustit pomocí sady SDK Azure BizTalk Services </a> a <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">instalace sady SDK Azure BizTalk Services</a> jsou uvedeny kroky, abyste mohli začít.
        </td>
    </tr>
    <tr>
        <td><strong>Vytvořte partnery smlouvy</strong></td>
        <td>Otevřete portál Azure BizTalk Services hostované v Azure, kde je přidat partnery a vytvořit X12 AS2, smlouvy EDIFACT EDI a.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Konfigurace součástí pro zasílání zpráv EDI na portálu služby BizTalk</a> jsou uvedeny kroky, abyste mohli začít.
        </td>
    </tr>

<tr>
        <td><strong>Další informace o BizTalk Services</strong></td>
        <td>Přejděte na <a HREF="http://azure.microsoft.com/documentation/services/biztalk-services/">učení center</a> Další informace o Azure BizTalk Services.</td>
</tr>
</table>


Na hlavním panelu v dolní části můžete:

<table border="1">

<tr>
<td><strong>Spravovat</strong> nasazení vaší aplikace</td>
<td>Otevřete portál služby Azure BizTalk Services. Na portálu služby BizTalk se vstupem EDI konfigurace, včetně přidání partnery a vytvoření X12 AS2, EDIFACT smlouvy a.
<br/><br/>
Je to stejné nastavení jako <strong>vytvořte smlouvy partnera</strong> na <strong>rychlý Start</strong> kartě.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Konfigurace součástí pro zasílání zpráv EDI na portálu služby BizTalk</a> poskytuje další informace o portálu služby BizTalk.</td>
</tr>

<tr>
<td><strong>Informace o připojení</strong> z Namespace řízení přístupu</td>
<td>Když vyberete informace o připojení, pak se zobrazují Namespace řízení přístupu, výchozí vystavitele a výchozí klíč. Můžete zkopírovat tyto hodnoty.
<br/><br/>
Můžete také otevřít portál pro řízení přístupu. <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Vytvoření ovládacího prvku přístup Namespace</a> poskytuje další informace o portálu řízení přístupu.</td>
</tr>

<tr>
<td><strong>Klíče synchronizovat</strong> v účtu úložiště</td>
<td>Při vytváření účtu služby Storage se automaticky vytvoří primární a sekundární klíč. Tyto šifrovací klíče řízení přístupu k účtu úložiště. Vaše služba BizTalk automaticky používá primární klíč. <strong>Klíče synchronizovat</strong> uživatelé přepínat mezi primární klíč a sekundární klíč bez přerušení služby BizTalk.
<br/><br/>
Například chcete službu BizTalk používat nový primární klíč pro účet úložiště. Použijte následující postup:
<br/><br/>
<ol>
<li>Vyberte svoji službu BizTalk a vyberte <strong>synchronizace klíčů</strong>. Vyberte sekundární klíč. Když to uděláte, spuštění služby BizTalk pomocí sekundární klíč.</li>
<li>Vyberte účet úložiště a obnovit primární klíč. Pamatujte si, že se vaše služba BizTalk používá sekundární klíč.</li>
<li>Vyberte svoji službu BizTalk a vyberte <strong>synchronizace klíčů</strong>. Nyní vyberte primární klíč. Toto je nový primární klíč, můžete se znova vygeneroval.</li>
<li>Vyberte účet úložiště a obnovit sekundární klíč.</li>
</ol>
<br/>
Tento proces se nazývá "výměny klíčů". Účelem je umožnit uživatelům přepínat mezi primární klíč a sekundární klíč bez přerušení služby BizTalk.</td>
</tr>

<tr>
<td><strong>Odstranit</strong> vaší aplikace</td>
<td>Když vyberete odstranit, vaše služba BizTalk a jsou odebrány všechny položky nasadili do ní.</td>
</tr>
</table>


## <a name="dashboard"></a>Řídicí panel
V závislosti na edici služby BizTalk nemusí být k dispozici všechny možnosti, které jsou uvedené. 

Když vyberete název vaší služby BizTalk, se zobrazí řídicí panel. Na řídicím panelu můžete:

##### <a name="usage-overview-shows-the-number-of-used-hybrid-connections"></a>Přehled využití: Zobrazuje počet použitých hybridní připojení
Také zobrazuje využití dat v GB. 

##### <a name="metric-graph-shows-a-fixed-list-of-performance-metrics"></a>Metriky grafu: Zobrazuje pevnou seznam metrik výkonu.
Tyto metriky zadejte v reálném čase hodnoty týkající se stavu služby BizTalk. Můžete také **relativní** nebo **absolutní** hodnoty a časové rozmezí **Interval** metrik, která se zobrazí v grafu. 

Popis tyto metriky výkonu, přejděte na [dostupné metriky](#Metrics) v tomto tématu.

##### <a name="quick-glance-lists-your-biztalk-service-properties"></a>Rychlého přehledu: Seznamy vlastnosti vaší služby BizTalk
<table border="1">

<tr>
<td><strong>Aktualizujte přihlašovací údaje databáze sledování</strong></td>
<td>Změní uživatelské jméno a heslo použité pro přihlášení k databázi pro sledování.</td>
</tr>
<tr>
<td><strong>Aktualizovat certifikát SSL</strong></td>
<td>Můžete aktualizovat službu BizTalk používat jiný certifikát SSL. Když automaticky vytvoří certifikát podepsaný svým držitelem SSL můžete <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">vytvořit službu BizTalk</a>.</td>
</tr>
<tr>
<td><strong>Stažení certifikátu</strong></td>
<td>Můžete si stáhnout certifikát SSL používaný službou BizTalk do místního počítače.</td>
</tr>
<tr>
<td><strong>Stav</strong></td>
<td>Zobrazí aktuální stav služby BizTalk. V tématu <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">BizTalk Services: Tabulka stavů služby</a>. </td>
</tr>
<tr>
<td><strong>Adresa URL služby</strong></td>
<td>Adresa URL pro svoji službu BizTalk. Je to stejné jako <strong>adresa URL domény</strong> zadali při vytváření služby BizTalk.</td>
</tr>
<tr>
<td><strong>Veřejná virtuální IP (VIP) Adresa</strong></td>
<td>IP adresa přiřazenou k vaší službě BizTalk. To se používá pro všechny vstupní koncové body a je zdrojovou adresu odchozího provozu. Tato IP adresa patří do vaší služby BizTalk, dokud se vytvoří. Pokud odstraníte službu BizTalk, je jiná služba BizTalk přidělit IP adresu.</td>
</tr>
<tr>
<td><strong>Namespace služby ACS</strong></td>
<td>Ověřuje se služba BizTalk.</td>
</tr>
<tr>
<td><strong>Edice</strong></td>
<td>Seznamy edice zadali při vytvoření služby BizTalk.</td>
</tr>
<tr>
<td><strong>Umístění</strong></td>
<td>Zobrazí zeměpisnou oblast, která je hostitelem služby BizTalk.</td>
</tr>
<tr>
<td><strong>Vytvořit</strong></td>
<td>Zobrazí datum a čas vytvoření služby BizTalk.</td>
</tr>
<tr>
<td><strong>Sledování databáze</strong></td>
<td>Název databáze SQL Azure, který ukládá sledovacích tabulek, které vaše služba BizTalk používá. 
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Požadavky na Explained</a> poskytuje podrobné informace o sledování databáze.</td>
</tr>
<tr>
<td><strong>Monitorování/archivaci úložiště</strong></td>
<td>Název účtu úložiště Azure, který ukládá výstup monitorování vaší služby BizTalk.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Požadavky na Explained</a> poskytuje podrobné informace o účtu úložiště.</td>
</tr>
<tr>
<td><strong>Název odběru</strong></td>
<td>Uvádí odběr, který je hostitelem služby BizTalk. Předplatné řídí přístup.</td>
</tr>
<tr>
<td><strong>ID předplatného</strong></td>
<td>Když je vytvořen předplatné, se automaticky vygeneroval ID předplatného. Při použití rozhraní REST API, budete muset zadat ID předplatného.</td>
</tr>
</table>

[BizTalk Services: Zřízení](http://go.microsoft.com/fwlink/p/?LinkID=302280) obsahuje kroky k vytvoření služby BizTalk.

##### <a name="manage-connection-information-sync-keys-and-delete-in-the-task-bar"></a>Spravovat informace o připojení, synchronizace klíče a odstranění na hlavním panelu:
<table border="1">

<tr>
<td><strong>Spravovat</strong> nasazení vaší aplikace</td>
<td>Otevře na portálu Azure BizTalk Services. Na portálu služby BizTalk se vstupem EDI konfigurace, včetně přidání partnery a vytvoření X12 AS2, EDIFACT smlouvy a.
<br/><br/>
Je to stejné nastavení jako <strong>vytvořte smlouvy partnera</strong> na <strong>rychlý Start</strong> kartě.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Konfigurace součástí pro zasílání zpráv EDI na portálu služby BizTalk</a> poskytuje další informace o portálu služby BizTalk.</td>
</tr>
<tr>
<td><strong>Informace o připojení</strong> z Namespace řízení přístupu</td>
<td>Zobrazí Namespace řízení přístupu, výchozí vystavitele a klíč výchozí hodnoty; které je možné zkopírovat.
<br/><br/>
Můžete také otevřít portál pro řízení přístupu. Tento portál pro řízení přístupu je stejná jako možnost služby Active Directory v levém navigačním podokně.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Správa vašeho Namespace ACS</a> poskytuje další informace o portálu řízení přístupu.</td>
</tr>
<tr>
<td><strong>Klíče synchronizovat</strong> v účtu úložiště</td>
<td>Při vytváření účtu služby Storage se automaticky vytvoří primární a sekundární klíč. Tyto šifrovací klíče řízení přístupu k účtu úložiště. Vaše služba BizTalk automaticky používá primární klíč. <strong>Klíče synchronizovat</strong> uživatelé přepínat mezi primární klíč a sekundární klíč bez přerušení služby BizTalk.
<br/><br/>
Například chcete službu BizTalk používat nový primární klíč pro účet úložiště. Použijte následující postup:
<br/><br/>
<ol>
<li>Vyberte svoji službu BizTalk a vyberte <strong>synchronizace klíčů</strong>. Vyberte sekundární klíč. Když to uděláte, spuštění služby BizTalk pomocí sekundární klíč.</li>
<li>Vyberte účet úložiště a obnovit primární klíč. Pamatujte si, že se vaše služba BizTalk používá sekundární klíč.</li>
<li>Vyberte svoji službu BizTalk a vyberte <strong>synchronizace klíčů</strong>. Nyní vyberte primární klíč. Toto je nový primární klíč, můžete se znova vygeneroval.</li>
<li>Vyberte účet úložiště a obnovit sekundární klíč.</li>
</ol>
<br/>
Tento proces se nazývá "výměny klíčů". Účelem je umožnit uživatelům přepínat mezi primární klíč a sekundární klíč bez přerušení služby BizTalk.</td>
</tr>

<tr>
<td><strong>Odstranit</strong> vaší aplikace</td>
<td>Vaše služba BizTalk a nasadili do ní všechny položky se odeberou.</td>
</tr>
</table>


## <a name="monitor"></a>Monitorování
Nevztahuje se na edice Free.

Když vyberete název vaší služby BizTalk, na kartě monitorování je k dispozici a zobrazí se následující:

##### <a name="metric-graph-displays-the-selected-performance-metrics"></a>Metriky grafu: Zobrazí metriky výkonu vybrané
Tyto metriky zadejte v reálném čase hodnoty týkající se stavu služby BizTalk. Zvolíte, které metriky výkonu zobrazují. Nesmí být delší než šest metriky výkonu lze zobrazit najednou. 

Můžete také **relativní** nebo **absolutní** hodnoty a časové rozmezí **Interval** metrik, která se zobrazí. 

##### <a name="to-remove-or-display-metrics-in-the-graph"></a>Odebrat nebo zobrazování metrik v grafu:
1. Vyberte **monitorování** kartě.
2. Vyberte **přidat metriky** na hlavním panelu:  
   ![Vyberte Přidat metriky][AddMetrics]
3. Zkontrolujte metrik výkonu, které chcete zobrazit.
4. Vyberte na značku zaškrtnutí se vrátíte do **monitorování** kartě.
5. Vyberte kroužek vedle metrika zobrazíte tuto metriku hodnota v grafu.  
   
    Například **využití procesoru** metrika je zobrazeno šedě; není v grafu zobrazí jeho výstup:  
   ![Je zobrazeno šedě metriky využití procesoru][GrayedMetric]  
   
    Vyberte si kruhu. Chcete-li povolit šedým **využití procesoru** metrika se používá k zobrazení jeho výstupu v grafu:  
   ![Využití procesoru metrika je povoleno.][EnabledMetric]
6. Metriky odebrat ze seznamu a zobrazení grafu, vyberte **odstranit metrika** na hlavním panelu. Chcete-li přidat metriky zpět do seznamu, vyberte **přidat metriky** na hlavním panelu metriku a vyberte značku zaškrtnutí se vraťte do **monitorování** kartě. Vyberte šedým kruhu. Chcete-li povolit metriku.

## <a name="Metrics"></a>Dostupné metriky
K dispozici jsou následující čítače výkonu nebo metriky:

<table border="1">

<tr>
<td><strong>Latence RountdTrip</strong></td>
<td>Průměrná doba v milisekundách (ms), jakou trvalo zpracování zprávy ze čas, že doručení zprávy, dokud služba BizTalk je plně zpracovat zprávu zobrazuje napříč všechny mostů. Započítávají se pouze zprávy úspěšně zpracoval.<br/><br/>
Když dojde k následujícím událostem, vytvoří se časovým razítkem:
<ul>
<li>Zpráva zadá brány</li>
<li>Zpráva směrována na cíl</li>
<li>Cílový odpověď přijata</li>
<li>Cílový potvrzení odpovědi odeslané k bráně.</li>
</ul>
<br/>
Tato metrika uvádí výsledek tohoto výpočtu:
<br/><br/>
[Cílové potvrzení odeslané odpovědi k bráně] - [zpráva zadá brány]</td>
</tr>
<tr>
<td><strong>Selhání u zdroje</strong></td>
<td>Zobrazí celkový počet zpráv, které se nezdařily službou BizTalk při stahování zprávy ze zdroje koncových bodů.</td>
</tr>
<tr>
<td><strong>Využití procesoru</strong></td>
<td>Zobrazí průměrný % času procesoru všech instancí rolí.</td>
</tr>
<tr>
<td><strong>Čekací doba zpracování</strong></td>
<td>Průměrná doba v milisekundách (ms) ke zpracování zprávy pomocí služby BizTalk se zobrazí napříč všechny mostů, s výjimkou času stráveného v cíle. Započítávají se pouze zprávy úspěšně zpracoval.<br/><br/>
Pokud dojde k každý z následujících událostí, je vytvořen časové razítko:

<ul>
<li>Zpráva zadá brány</li>
<li>Zpráva směrována na cíl</li>
<li>Cílový odpověď přijata</li>
<li>Cílový potvrzení odpovědi odeslané k bráně.</li>
</ul>
<br/>Tato metrika uvádí výsledek tohoto výpočtu:<br/><br/>
[Cílové potvrzení odeslané odpovědi k bráně] - [zpráva zadá brány] - [cílové odpověď přijata] + [zpráva směrována na cílovém]</td>
</tr>
<tr>
<td><strong>Selhání v procesu</strong></td>
<td>Zobrazí celkový počet zpráv, které selhaly během zpracování službou BizTalk mezi všechny mostů v časovém intervalu.</td>
</tr>
<tr>
<td><strong>Zprávy odeslané</strong></td>
<td>Zobrazí celkový počet zpráv odeslaných službou BizTalk mezi všechny mostů v časovém intervalu. Tato metrika se zvýší, když zprávy odeslané z kanálu dosáhne cílové trasy. Tato metrika neznamená, že je zpráva úspěšně zpracována.<br/><br/>
V případě požadavku a odpovědi metrika se zvýší, když cíl trasy odešle potvrzení přijetí zpátky do kanálu.</td>
</tr>
<tr>
<td><strong>Přijatých zpráv</strong></td>
<td>Zobrazí celkový počet zpráv přijatých službu BizTalk mezi všechny mostů v časovém intervalu. Tato metrika se zvýší, když novou zprávu přijetí v kanálu.</td>
</tr>
<tr>
<td><strong>Zprávy v procesu</strong></td>
<td>Zobrazí celkový počet zpráv, které jsou aktuálně zpracovávaných službu BizTalk v časovém intervalu.</td>
</tr>
<tr>
<td><strong>Počet zpracovaných zpráv</strong></td>
<td>Zobrazí celkový počet zpráv úspěšně zpracovaných službou BizTalk mezi všechny mostů v časovém intervalu. Tato metrika se zvýší, když zprávu úspěšně přijme kanál a úspěšně směrovány do cílového umístění.</td>
</tr>
</table>


## <a name="scale"></a>Měřítko
Na kartě škálování můžete přidat nebo odečíst počet jednotek, které používá vaše služba BizTalk. Ve výchozím nastavení není nakonfigurované jednotky. Škálování služby BizTalk můžete přidat další jednotky. Pokud zvýšíte měřítka, jsou zvýšit propustnost. Objem prostředků také zvyšuje, včetně nasazené mostech, smlouvy, obchodní připojení a výpočetní výkon. Například můžete zvýšit škále od 1 jednotka do 2 jednotky. V takovém případě můžete nasadit double počet mostů, dvakrát smlouvy, dvakrát LOB připojení a dvakrát výpočetní výkon.

Některých edicích BizTalk nenabízejí možnost škálování. V takovém případě je povolená jednu jednotku. Chcete-li zjistit, kolik jednotek je možné rozšířit vaše edice, přečtěte [BizTalk Services: Tabulka edic](biztalk-editions-feature-chart.md).

Zvýšením počtu jednotek může mít vliv na ceny. Pokud zvýšíte jednotky, pak výběrem **Uložit** zobrazí zpráva s informacemi, pokud je ovlivněno fakturace. Potom budete pokračovat. Pokud zvýšíte počet jednotek, změny stavu služby BizTalk z aktivní na aktualizace. Ve stavu aktualizace svoji službu BizTalk dál běžet.

[BizTalk Services: Tabulka edic](biztalk-editions-feature-chart.md) definuje "Jednotka".

## <a name="configure"></a>Konfigurace
Nevztahuje se na hybridní připojení.

Nastaví stav zálohování na hodnotu None nebo automaticky. Pokud nastavíte na hodnotu None, žádné zálohy se vytvoří automaticky. Pokud nastavíte na hodnotu automaticky, nakonfigurujte umístění zálohy, frekvenci zálohování a jak dlouho chcete zachovat záložní soubory. 

[Služba BizTalk Services: Zálohování a obnovení](biztalk-backup-restore.md) nabízí podrobné informace. 

## <a name="HybridConnections"></a>Hybridní připojení
Hybridní připojení aplikací Azure, jako je Web Apps nebo Mobile Apps v Azure App Service připojit k místnímu prostředku, který používá statický port TCP, jako je například SQL Server, MySQL, webové rozhraní API HTTP a většina vlastních webových služeb. Hybridní připojení se spravují v BizTalk Services.

Pokud chcete vytvořit ani spravovat hybridní připojení ve službě Azure BizTalk Services, přečtěte si téma [hybridní připojení](integration-hybrid-connection-overview.md).

## <a name="next"></a>Další
Teď, když jste obeznámeni s různých kartách, další informace o funkcích služby Azure BizTalk Services:

* [BizTalk Services: Omezování](biztalk-throttling-thresholds.md)  
* [BizTalk Services: Název a klíč vystavitele](biztalk-issuer-name-issuer-key.md)  
* [BizTalk Services: Zálohování a obnovení](biztalk-backup-restore.md)

## <a name="see-also"></a>Viz také
* [Hybridní připojení](integration-hybrid-connection-overview.md)  
* [BizTalk Services: Developer, Basic, Standard a Premium tabulka edic](biztalk-editions-feature-chart.md)  
* [BizTalk Services: zřízení](biztalk-provision-services.md)  
* [BizTalk Services: Tabulka stav služby BizTalk](biztalk-service-state-chart.md)  
* [Jak začít používat sadu SDK Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[Quickstart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png

