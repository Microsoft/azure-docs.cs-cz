---
title: Funkce v různých edicích služby BizTalk Services | Dokumentace Microsoftu
description: 'Porovnejte možnosti různých edic služby BizTalk Services: Free, Developer, Basic, Standard a Premium. MABS, WABS.'
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: c589629f-06b1-44bb-b8ca-1db71826ea59
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 718b57a801a9ba62a0154ae42da2ac0c0741f203
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "22706644"
---
# <a name="biztalk-services-editions-chart"></a>BizTalk Services: Tabulka edic

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Služba Azure BizTalk Services je dostupná v několika edicích. Tento článek vám pomůže zjistit, která edice je vhodná pro vaši situaci a obchodní potřeby.

## <a name="compare-the-editions"></a>Porovnání edic
**Free (Preview)**

Dokáže vytvořit a spravovat hybridní připojení. Hybridní připojení je snadný způsob, jak připojit web Azure k místnímu systému, jako je třeba SQL Server.

**Developer**

Zahrnuje hybridní připojení, zpracování zpráv EAI a EDI se snadnou použitelným portálem pro správu obchodních partnerů a podporu společných schémat EDI a rozšířeného zpracování EDI přes X12 a AS2. Dokáže vytvářet společné scénáře EAI, které propojují služby v cloudu s protokoly HTTP/S, REST, FTP, WCF a SFTP pro čtení a zápis zpráv.  Využívá připojení k místním systémům LOB pomocí připravených adaptérů pro SAP, Oracle eBusiness, Oracle DB, Siebel a SQL Server. Používá prostředí zaměřené na vývojáře s nástroji sady Visual Studio, které umožňují snadný vývoj a nasazení. Omezuje se jenom na účely vývoje a testování a nenabízí smlouvu o úrovni služeb (SLA).

**Basic**

Obsahuje většinu funkcí edice Developer a k tomu rozšířené funkce hybridních připojení, mosty EAI, smlouvy EDI a připojení sady BizTalk Adapter Pack. Kromě toho nabízí vysokou dostupnost a možnost škálování se smlouvou o úrovni služeb (SLA).

**Standard**

Obsahuje všechny funkce edice Basic a k tomu rozšířené funkce hybridních připojení, mosty EAI, smlouvy EDI a připojení sady BizTalk Adapter Pack. Kromě toho nabízí vysokou dostupnost a možnost škálování se smlouvou o úrovni služeb (SLA).

**Premium**

Obsahuje všechny funkce edice Standard a k tomu rozšířené funkce hybridních připojení, mosty EAI, smlouvy EDI a připojení sady BizTalk Adapter Pack. Kromě toho zahrnuje archivaci, vysokou dostupnost a možnost škálování se smlouvou o úrovni služeb (SLA).

## <a name="editions-chart"></a>Tabulka edic
Rozdíly jsou uvedené v následující tabulce.

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Free (Preview)</th>
        <th>Developer</th>
        <th>Basic</th>
        <th>Standard</th>
        <th>Premium</th>
</tr>

<tr>
<td><strong>Počáteční cena</strong></td>
<td colspan="5"><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Ceník služby Azure BizTalk Services</a> <br/><br/> <a HREF="http://azure.microsoft.com/pricing/calculator/?scenario=full"> Cenová kalkulačka funkcí Azure</a></td>
</tr>
<tr>
<td><strong>Výchozí minimální konfigurace</strong></td>
<td>1 jednotka Free</td>
<td>1 jednotka Developer</td>
<td>1 jednotka Basic</td>
<td>1 jednotka Standard</td>
<td>1 jednotka Premium</td>
</tr>
<tr>
<td><strong>Škálování</strong></td>
<td>Bez škálování</td>
<td>Bez škálování</td>
<td>Ano, v přírůstcích po 1 jednotce Basic</td>
<td>Ano, v přírůstcích po 1 jednotce Standard</td>
<td>Ano, v přírůstcích po 1 jednotce Premium</td>
</tr>
<tr>
<td><strong>Maximální povolené horizontální navýšení kapacity</strong></td>
<td>Bez škálování</td>
<td>Bez škálování</td>
<td>Až 8 jednotek</td>
<td>Až 8 jednotek</td>
<td>Až 8 jednotek</td>
</tr>
<tr>
<td><strong>Mosty EAI na jednotku</strong></td>
<td>Nezahrnuje</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI, AS2</strong>
<br/><br/>
Zahrnuje smlouvy TPM</td>
<td>Nezahrnuje</td>
<td>Zahrnuje. 10 smluv na jednotku</td>
<td>Zahrnuje. 50 smluv na jednotku</td>
<td>Zahrnuje. 250 smluv na jednotku</td>
<td>Zahrnuje. 1000 smluv na jednotku</td>
</tr>
<tr>
<td><strong>Hybridní připojení na jednotku</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>Přenos dat hybridním připojením (v GB) na jednotku</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>Připojení služby BizTalk Adapter Service k místním systémům LOB</strong></td>
<td>Nezahrnuje</td>
<td>1 připojení</td>
<td>2 připojení</td>
<td>5 připojení</td>
<td>25 připojení</td>
</tr>
<tr>
<td align="left"><strong>Podporované protokoly/systémy:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Service Bus (SB)</li>
<li>Azure Blob</li>
<li>Rozhraní REST API</li>
</ul>
</td>
<td>Nezahrnuje</td>
<td>Zahrnuje</td>
<td>Zahrnuje</td>
<td>Zahrnuje</td>
<td>Zahrnuje</td>
</tr>
<tr>
<td><strong>Vysoká dostupnost</strong>
<br/><br/>
Informace o smlouvách o úrovni služeb (SLA) najdete v článku <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Ceník služby Azure BizTalk Services</a>.
</td>
<td>Nezahrnuje</td>
<td>Nezahrnuje</td>
<td>Zahrnuje</td>
<td>Zahrnuje</td>
<td>Zahrnuje</td>
</tr>
<tr>
<td><strong>Zálohování a obnovení</strong></td>
<td>Nezahrnuje</td>
<td>Zahrnuje</td>
<td>Zahrnuje</td>
<td>Zahrnuje</td>
<td>Zahrnuje</td>
</tr>
<tr>
<td><strong>Sledování</strong></td>
<td>Nezahrnuje</td>
<td>Zahrnuje</td>
<td>Zahrnuje</td>
<td>Zahrnuje</td>
<td>Zahrnuje</td>
</tr>
<tr>
<td><strong>Archivace</strong><br/><br/>
Zahrnuje neodvolatelnost příjmu a stahování sledovaných zpráv</td>
<td>Nezahrnuje</td>
<td>Zahrnuje</td>
<td>Nezahrnuje</td>
<td>Nezahrnuje</td>
<td>Zahrnuje</td>
</tr>
<tr>
<td><strong>Použití vlastního kódu</strong></td>
<td>Nezahrnuje</td>
<td>Zahrnuje</td>
<td>Zahrnuje</td>
<td>Zahrnuje</td>
<td>Zahrnuje</td>
</tr>
<tr>
<td><strong>Použití transformací, včetně vlastních transformací XSLT</strong></td>
<td>Nezahrnuje</td>
<td>Zahrnuje</td>
<td>Zahrnuje</td>
<td>Zahrnuje</td>
<td>Zahrnuje</td>
</tr>
</table>

> [!NOTE]
> V zájmu odolnosti proti selhání hardwaru s sebou funkce vysoké dostupnosti nese přítomnost víc virtuálních počítačů v rámci jedné jednotky BizTalk.
> 
> 

## <a name="faqs"></a>Nejčastější dotazy
#### <a name="what-is-a-biztalk-unit"></a>Co je to jednotka BizTalk?
„Jednotka“ je nejnižší úroveň nasazení služby Azure BizTalk Services. Každá edice se dodává s jednotkou, která má různou výpočetní kapacitu a paměť. Třeba jednotka Basic má vyšší výpočetní kapacitu než jednotka Developer, jednotka Standard má vyšší výpočetní kapacitu než jednotka Basic a tak dál. Horizontální snížení kapacity služeb BizTalk probíhá po jednotkách.

#### <a name="what-is-the-difference-between-biztalk-services-and-azure-biztalk-vm"></a>Jaký je rozdíl mezi službou BizTalk Services a virtuálním počítačem Azure BizTalk?
Služba BizTalk Services nabízí opravdovou architekturu modelu Platforma jako služba (PaaS) pro sestavování integračních řešení v cloudu. Model PaaS umožňuje soustředit se na aplikační logiku a nechat veškerou správu infrastruktury na Microsoftu:

* Nemusíte spravovat ani opravovat virtuální počítače.
* Microsoft zajišťuje dostupnost.
* Škálování řídíte podle potřeby – stačí si na webu Azure Portal vyžádat větší nebo menší kapacitu.

BizTalk Server na virtuálních počítačích Azure nabízí architekturu podle modelu Infrastruktura jako služba (IaaS). Vytvoříte virtuální počítače a nakonfigurujete je stejně jako v místním prostředí, aby se stávající aplikace daly snáz spouštět v cloudu beze změn kódu. V modelu IaaS musíte provádět konfiguraci a správu virtuálních počítačů (třeba instalaci softwaru a oprav operačního systému) a připravit aplikaci na vysokou dostupnost.

Pokud chcete sestavit nová integrační řešení, která sníží pracnost správy infrastruktury, použijte službu BizTalk Services. Pokud hledáte způsob, jak rychle provést migraci stávajících řešení BizTalk, nebo hledáte prostředí na vyžádání pro vývoj a testování aplikací BizTalk Serveru, použijte BizTalk Server na virtuálním počítači Azure.

#### <a name="what-is-the-difference-between-biztalk-adapter-service-and-hybrid-connections"></a>Jaký je rozdíl mezi službou BizTalk Adapter Service a hybridními připojeními?
Službu BizTalk Adapter Service používá služba Azure BizTalk Service. Služba BizTalk Adapter Service se pomocí sady BizTalk Adapter Pack připojuje k místnímu obchodnímu systému (LOB). Hybridní připojení poskytuje snadný a pohodlný způsob, jak připojit aplikace Azure, jako je funkce Web Apps ve službách Azure App Service a Azure Mobile Services, k místnímu prostředku.

#### <a name="what-does-hybrid-connection-data-transfer-gb-per-unit-mean-is-this-per-minutehourdayweekmonth-what-happens-when-the-limit-is-reached"></a>Co znamená „Přenos dat hybridním připojením (v GB) na jednotku“? Je to údaj za minutu, hodinu, den, týden, nebo měsíc? Co se stane po dosažení limitu?
Cena za jednotku hybridního připojení závisí na edici služby BizTalk Services. Jednoduše řečeno, cena závisí na tom, kolik dat přenášíte. Třeba přenesení 10 GB dat denně stojí méně než přenos 100 GB denně. Konkrétní náklady zjistíte pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/?scenario=full) služby BizTalk Services. Limity se většinou vynucují denně. Pokud limit překročíte, další přenosy stojí 1 USD za GB.

#### <a name="when-i-create-an-agreement-in-biztalk-services-why-does-the-number-of-bridges-go-up-by-two-instead-of-just-one"></a>Když vytvořím ve službě BizTalk Services smlouvu, proč vzroste počet mostů o dva, a ne o jeden?
Každá smlouva se skládá ze dvou různých mostů: z komunikačního mostu na straně odesílání a z komunikačního mostu na straně příjmu.

#### <a name="what-happens-when-i-hit-the-quota-limit-on-the-number-of-bridges-or-agreements"></a>Co se stane, když dosáhnu maximální kvóty počtu mostů nebo smluv?
Nebudete moct nasazovat žádné nové mosty ani vytvářet nové smlouvy. Pokud budete chtít nasadit další, budete muset vertikálně navýšit kapacitu na víc jednotek služby BizTalk nebo provést upgrade na vyšší edici.

#### <a name="how-do-i-migrate-from-one-tier-of-biztalk-services-to-another"></a>Jak provedu migraci z jedné vrstvy služby BizTalk Services na jinou?
Edice Free neumožňuje migraci ani vertikální navýšení kapacity na jinou vrstvu a nedá se zálohovat a obnovit v jiné vrstvě. Pokud potřebujete jinou vrstvu, vytvořte novou službu BizTalk pomocí nové vrstvy. Všechny artefakty vytvořené pomocí edice Free, včetně hybridních připojení, bude potřeba v nové službě BizTalk znovu vytvořit. 

U ostatních edic můžete k migraci artefaktů z jedné vrstvy do druhé použít funkci zálohování a obnovení. Můžete třeba zálohovat artefakty na vrstvě Standard a potom je obnovit na vrstvě |Premium. Článek [Služba BizTalk Services: Zálohování a obnovení](biztalk-backup-restore.md) popisuje podporované cesty migrace a uvádí, jaké artefakty se dají zálohovat. Upozorňujeme, že hybridní připojení se nezálohují. Po zálohování a obnovení na nové vrstvě musíte hybridní připojení znovu vytvořit.  

#### <a name="is-the-biztalk-adapter-service-included-in-the-service-how-do-i-receive-the-software"></a>Je součástí služby i služba BizTalk Adapter Service? Jak tento software získám?
Ano, součástí [stažení](http://www.microsoft.com/download/details.aspx?id=39087) sady SDK Azure BizTalk Services je také služba BizTalk Adapter Service se sadou BizTalk Adapter Pack.

## <a name="next-steps"></a>Další kroky
Pokud chcete vytvořit službu BizTalk Services na webu Azure Portal, přejděte na článek [BizTalk Services: Zřízení pomocí webu Azure Portal](biztalk-provision-services.md). Pokud chcete začít vytvářet aplikace, přejděte na článek [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="additional-resources"></a>Další zdroje
* [BizTalk Services: Zřízení pomocí webu Azure Portal](biztalk-provision-services.md)<br/>
* [BizTalk Services: Tabulka stavů zřízení](biztalk-service-state-chart.md)<br/>
* [BizTalk Services: Karty Řídicí panel, Sledování a Škálování](biztalk-dashboard-monitor-scale-tabs.md)<br/>
* [BizTalk Services: Zálohování a obnovení](biztalk-backup-restore.md)<br/>
* [BizTalk Services: Omezování](biztalk-throttling-thresholds.md)<br/>
* [BizTalk Services: Název a klíč vystavitele](biztalk-issuer-name-issuer-key.md)<br/>
* [Jak začít používat sadu SDK Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>

