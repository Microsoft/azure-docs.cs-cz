---
title: Sledování zabezpečení v Azure Security Center | Dokumentace Microsoftu
description: Tento článek vám pomůže začít pracovat s funkcemi sledování v Azure Security Center.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2018
ms.author: yurid
ms.openlocfilehash: 330a12f851ef0191adc4dc46102b798f1b752589
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
ms.locfileid: "32777709"
---
# <a name="security-health-monitoring-in-azure-security-center"></a>Sledování stavu zabezpečení v Azure Security Center
Tento článek vám pomůže s využíváním možností v Azure Security Center ke sledování dodržování zásad.

## <a name="what-is-security-health-monitoring"></a>Co je sledování stavu zabezpečení?
Pod pojmem sledování si často představujeme pozorování a čekání, až se stane nějaká událost, abychom na situaci reagovali. Sledování zabezpečení odkazuje na používání proaktivní strategie, kdy se auditují vaše prostředky a identifikují ty systémy, které nesplňují organizační standardy nebo osvědčené postupy.

## <a name="monitoring-security-health"></a>Sledování stavu zabezpečení
Po povolení [zásad zabezpečení](security-center-policies.md) pro prostředky předplatného služba Security Center analyzuje zabezpečení vašich prostředků, aby identifikovala potenciální ohrožení zabezpečení. Informace o konfiguraci vaší sítě jsou k dispozici okamžitě. Získání informací o konfiguraci virtuálních počítačů a počítačů, jako je stav aktualizace zabezpečení nebo konfigurace operačního systému, může trvat hodinu i déle v závislosti na počtu virtuálních počítačů a počítačů, na kterých máte nainstalovaného agenta. V části **Prevence** můžete zobrazit stav zabezpečení svých prostředků a případné problémy. Seznam těchto problémů můžete zobrazit také na dlaždici **Doporučení**.

Další informace o tom, jak používat doporučení, najdete v článku [Implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).

V části **Prevence** můžete monitorovat stav zabezpečení svých prostředků. V následujícím příkladu vidíte, že dlaždice každého prostředku (Compute, Sítě, Úložiště a data a Aplikace) obsahuje celkový počet identifikovaných problémů.

![Dlaždice stavu zabezpečení prostředků](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)


### <a name="monitor-compute"></a>Monitorování služby Compute
Po kliknutí na dlaždici **Compute** se zobrazí tři karty:

- **Přehled:** Monitorování a doporučení.
- **Virtuální počítače a počítače:** Seznam všech virtuálních počítačů a počítačů a jejich aktuálního stavu zabezpečení.
- **Cloudová služby:** Seznam všech webových a pracovních rolí monitorovaných pomocí služby Security Center.

![Chybějící aktualizace systému podle virtuálních počítačů](./media/security-center-monitoring/security-center-monitoring-fig1-sep2017.png)

Na každé kartě můžete mít několik částí a v každé části můžete vybrat jednotlivé možnosti, abyste zobrazili další podrobnosti o doporučeném postupu k vyřešení konkrétního problému.

#### <a name="monitoring-recommendations"></a>Doporučení pro monitorování
Tato část uvádí celkový počet virtuálních počítačů a počítačů, které byly inicializovány pro automatické zřizování, a jejich aktuální stavy. Tento příklad obsahuje jedno doporučení, **Problémy s monitorováním stavu agentů**.  Vyberte toto doporučení.

![Problémy s monitorováním stavu agentů](./media/security-center-monitoring/security-center-monitoring-fig1-new003-2017.png)

Doporučení **Problémy s monitorováním stavu agentů** se otevře. Tady budou uvedené virtuální počítače a počítače, které služba Security Center nedokáže úspěšně monitorovat. Výběrem virtuálního počítače nebo počítače zobrazíte podrobné informace. **STAV MONITOROVÁNÍ** uvádí důvod, proč Security Center nemůže monitorovat. Seznam hodnot, popisů a postupů řešení pro hodnoty **STAV MONITOROVÁNÍ** najdete v [průvodci odstraňováním potíží pro Security Center](security-center-troubleshooting-guide.md#monitoring-agent-health-issues).

#### <a name="recommendations"></a>Doporučení
Tato část obsahuje sadu [doporučení pro každý virtuální počítač a počítač](security-center-virtual-machine-recommendations.md) monitorovaný pomocí Azure Security Center. První sloupec uvádí doporučení. Druhý sloupec zobrazuje celkový počet virtuálních počítačů a počítačů, které jsou ovlivněny tímto doporučením. Třetí sloupec zobrazuje závažnost problému, jak je znázorněno na následujícím snímku obrazovky:

![Doporučení pro virtuální počítače](./media/security-center-monitoring/security-center-monitoring-fig2-sep2017.png)

> [!NOTE]
> V seznamu **Síťová topologie** v okně **Stav sítě** se zobrazí jenom virtuální počítače s alespoň jedním veřejným koncovým bodem.
>

Každé doporučení obsahuje sadu akcí, které můžete provést, když na ni kliknete. Pokud například kliknete na **Chybějící aktualizace systému**, zobrazí se seznam virtuálních počítačů a počítačů s chybějícími opravami a závažnost chybějících aktualizací, jak je znázorněno na následujícím snímku obrazovky:

![Chybějící aktualizace systému pro virtuální počítače](./media/security-center-monitoring/security-center-monitoring-fig9-sep2017.png)

**Chybějící aktualizace systému** obsahují souhrn důležitých aktualizací ve formátu grafu, jeden pro Windows a jeden pro Linux. Druhá část obsahuje tabulku s následujícími informacemi:

* **NÁZEV:** Název chybějící aktualizace.
* **POČET VIRTUÁLNÍCH POČÍTAČŮ A POČÍTAČŮ:** Celkový počet virtuálních počítačů a počítačů, na kterých chybí daná aktualizace.
* **STAV**: Aktuální stav doporučení:
  * **Otevřené**: Doporučení dosud nebylo řešeno.
  * **Probíhá**: Doporučení se aktuálně na tyto prostředky používá a není třeba provádět žádnou akci.
  * **Vyřešeno**: Doporučení už je dokončené. (Pokud byl problém vyřešen, položka je vyšedlá.)
* **ZÁVAŽNOST**: Popisuje závažnost tohoto konkrétního doporučení:
  * **Vysoká**: Ohrožení zabezpečení existuje u významného prostředku (aplikace, virtuální počítač nebo skupina zabezpečení sítě) a vyžaduje pozornost.
  * **Střední**: Nekritické nebo další kroky jsou potřebné k dokončení procesu nebo odstranění ohrožení.
  * **Nízká**: Ohrožení zabezpečení by se mělo řešit, ale nevyžaduje okamžitou pozornost. (Ve výchozím nastavení nejsou doporučení s nízkou závažností uváděny, ale pokud je chcete zobrazit, je možné je vyfiltrovat.)

Pokud chcete zobrazit podrobnosti o doporučení, klikněte na název chybějící aktualizace v seznamu.

![Chybějící aktualizace systému pro konkrétní virtuální počítač](./media/security-center-monitoring/security-center-monitoring-fig4-sep2017.png)

> [!NOTE]
> Bezpečnostní doporučení uvedená tady jsou stejná jako v možnosti **Doporučení**. Další informace o tom, jak řešit doporučení, najdete v článku [Implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md). Týká se to nejen virtuálních počítačů a počítačů, ale také všech prostředků, které jsou k dispozici na dlaždici **Resource Health**.
>

#### <a name="unmonitored-vms"></a>Nemonitorované virtuální počítače
Virtuální počítač je nemonitorovaný službou Security Center v případě, že na něm není spuštěné rozšíření Microsoft Monitoring Agent. Na virtuálním počítači už může být nainstalovaný místní agent, například přímý agent OMS nebo agent SCOM. Virtuální počítače s těmito agenty se identifikují jako nemonitorované, protože Security Center tyto agenty plně nepodporuje. Pokud chcete naplno využívat všechny schopnosti služby Security Center, potřebujete rozšíření Microsoft Monitoring Agent.

Rozšíření můžete na nemonitorovaný virtuální počítač nainstalovat k již nainstalovanému místnímu agentu. Nakonfigurujte oba agenty stejným způsobem a připojte je ke stejnému pracovnímu prostoru. Tím umožníte službě Security Center pracovat s rozšířením Microsoft Monitoring Agent a shromažďovat data.  Pokyny k instalaci rozšíření Microsoft Monitoring Agent najdete v tématu popisujícím [povolení rozšíření virtuálního počítače](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension).

Další informace o důvodech, proč se službě Security Center nedaří úspěšně monitorovat virtuální počítače a počítače inicializované pro automatické zřizování, najdete v tématu popisujícím [problémy s monitorováním stavu agenta](security-center-troubleshooting-guide.md#monitoring-agent-health-issues).

#### <a name="vms--computers-section"></a>Část virtuálních počítačů a počítačů
Část virtuálních počítačů a počítačů poskytuje přehled doporučení pro všechny virtuální počítače a počítače. Každý sloupec představuje jednu sadu doporučení, jak ukazuje následující snímek obrazovky:

![Přehled všech virtuálních počítačů a doporučení](./media/security-center-monitoring/security-center-monitoring-fig5-sep2017.png)

V tomto seznamu se používají čtyři typy ikon, jak je popsáno v tomto seznamu:

![icon1](./media/security-center-monitoring/security-center-monitoring-icon1.png) Počítač mimo Azure.

![icon2](./media/security-center-monitoring/security-center-monitoring-icon2.png) Virtuální počítač Azure Resource Manageru.

![icon3](./media/security-center-monitoring/security-center-monitoring-icon3.png) Virtuální počítač Azure Classic.

![icon4](./media/security-center-monitoring/security-center-monitoring-icon4.png) Virtuální počítače identifikované pouze z pracovního prostoru, který je součástí zobrazeného předplatného. To zahrnuje virtuální počítače z jiných předplatných, které se hlásí k pracovnímu prostoru v tomto předplatném, a virtuální počítače nainstalované s přímým agentem SCOM, které nemají žádné ID prostředku.

Ikona, která se zobrazí pod každým doporučením, pomáhá rychle zjistit, které virtuální počítače a počítače vyžadují pozornost a o jaký typ doporučení se jedná. Pomocí možnosti **Filtr** můžete také vybrat, jaké možnosti se na obrazovce zobrazí.

![Filtr](./media/security-center-monitoring/security-center-monitoring-fig6-sep2017.png)

V předchozím příkladu má jeden virtuální počítač kritické doporučení týkající se ochrany koncových bodů. Pokud chcete získat další informace o tomto virtuálním počítači, klikněte na něj:

![Podrobné informace o zabezpečení virtuálního počítače](./media/security-center-monitoring/security-center-monitoring-fig7-sep2017.png)

Tady vidíte podrobné informace o zabezpečení pro virtuální počítač nebo počítač. V dolní části vidíte doporučenou akci a závažnost jednotlivých problémů.

#### <a name="cloud-services-section"></a>Část cloudových služeb
Pro cloudové služby se doporučení vytvoří, když je verze operačního systému zastaralá, jak je znázorněno na následujícím snímku obrazovky:

![Stav pro cloudové služby](./media/security-center-monitoring/security-center-monitoring-fig1-new006-2017.png)

V případě, kdy máte doporučení (což není případ předchozího příkladu), budete muset postupovat podle kroků v doporučení, abyste aktualizovali verzi operačního systému. Když je k dispozici aktualizace, obdržíte výstrahu (červenou nebo oranžovou – v závislosti na závažnosti problému). Když kliknete na tuto výstrahu v řádku Webová role 1 (spouští Windows Server s vaší webovou aplikací automaticky nasazenou do služby IIS) nebo Role pracovního procesu 1 (spouští Windows Server s vaší webovou aplikací automaticky nasazenou do služby IIS), zobrazí se další podrobnosti týkající se tohoto doporučení, jak je znázorněno na následujícím snímku obrazovky:

![Podrobnosti cloudové služby](./media/security-center-monitoring/security-center-monitoring-fig8-new3.png)

Pokud chcete zobrazit podrobnější vysvětlení tohoto doporučení, klikněte na **Aktualizovat verzi operačního systému** ve sloupci **POPIS**.

![Doporučení pro Cloud Services](./media/security-center-monitoring/security-center-monitoring-fig8-new4.png)  

### <a name="monitor-virtual-networks"></a>Monitorování virtuálních sítí
Po kliknutí na dlaždici **Sítě** se otevře okno **Sítě** s dalšími podrobnostmi, jak je znázorněno na následujícím snímku obrazovky:

![Okno Sítě](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

#### <a name="networking-recommendations"></a>Doporučení pro sítě
Podobně jako u informací o stavu prostředků virtuálních počítačů se tady zobrazí v horní části souhrnný seznam problémů a v dolní části seznam monitorovaných sítí.

Část rozpisu stavu sítí uvádí možné problémy zabezpečení a nabízí [doporučení](security-center-network-recommendations.md). Možné problémy mohou zahrnovat:

* Není nainstalována brána firewall příští generace (NGFW).
* Nejsou povolené skupiny zabezpečení sítě v podsítích.
* Nejsou povolené skupiny zabezpečení sítě ve virtuálních počítačích.
* Omezený externí přístup prostřednictvím veřejného externího koncového bodu.
* Internetové koncové body, které jsou v pořádku

Po kliknutí na doporučení se zobrazí další podrobnosti o doporučení, jak je znázorněno v následujícím příkladu:

![Podrobnosti o doporučení v okně Sítě](./media/security-center-monitoring/security-center-monitoring-fig9-ga.png)

V tomto příkladu obsahuje okno **Nakonfigurovat chybějící skupiny zabezpečení sítě pro podsítě** seznam podsítí a virtuálních počítačů, kde chybí ochrana skupiny zabezpečení sítě. Pokud kliknete na podsíť, na kterou chcete použít skupinu zabezpečení sítě, zobrazí se okno **Zvolit skupinu zabezpečení sítě**. Tady můžete vybrat nejvhodnější skupinu zabezpečení sítě pro podsíť nebo můžete vytvořit novou skupinu zabezpečení sítě.

#### <a name="internet-facing-endpoints-section"></a>Část internetových koncových bodů
V části **Internetové koncové body** uvidíte virtuální počítače, které jsou aktuálně nakonfigurované s internetovým koncovým bodem, a jejich aktuální stav.

![Virtuální počítače nakonfigurované s internetovým koncovým bodem a stav](./media/security-center-monitoring/security-center-monitoring-fig10-ga.png)

Tato tabulka obsahuje název koncového bodu, který představuje virtuální počítač, internetovou IP adresu, aktuální stav závažnosti skupiny zabezpečení sítě a bránu firewall příští generace. Tabulka je řazená podle závažnosti:

* Červená (nahoře): Vysoká priorita, mělo by se řešit okamžitě.
* Oranžová: Střední priorita, mělo by se řešit co nejdříve.
* Zelená (poslední): Stav v pořádku.

#### <a name="networking-topology-section"></a>Část topologie sítě
Část **Topologie sítě** obsahuje hierarchické zobrazení prostředků, jak je znázorněno na následujícím snímku obrazovky:

![Hierarchické zobrazení prostředků v části topologie sítě](./media/security-center-monitoring/security-center-monitoring-fig121-new4.png)

Tato tabulka je řazená podle závažnosti (virtuální počítače a podsítě):

* Červená (nahoře): Vysoká priorita, mělo by se řešit okamžitě.
* Oranžová: Střední priorita, mělo by se řešit co nejdříve.
* Zelená (poslední): Stav v pořádku.

V tomto zobrazení topologie obsahuje první úroveň [virtuální sítě](../virtual-network/virtual-networks-overview.md), [brány virtuální sítě](/vpn-gateway/vpn-gateway-site-to-site-create.md) a [virtuální sítě (klasické)](/virtual-network/virtual-networks-create-vnet-classic-pportal.md). Na druhé úrovni jsou podsítě a na třetí úrovni virtuální počítače, které patří do těchto podsítí. V pravém sloupci je aktuální stav skupiny zabezpečení sítě pro tyto prostředky, jak ukazuje následující příklad:

![Stav skupiny zabezpečení sítě v části topologie sítě](./media/security-center-monitoring/security-center-monitoring-fig12-ga.png)

Dolní část tohoto okna obsahuje doporučení pro tento virtuální počítač, podobně jako jsme už uváděli dříve. Kliknutím na doporučení získáte další informace nebo použijete potřebný ovládací prvek zabezpečení nebo konfiguraci zabezpečení.

### <a name="monitor-storage--data"></a>Monitorování úložiště a dat

Po kliknutí na **Úložiště a data** v části **Prevence** se otevře okno **Datové prostředky** s doporučeními pro SQL a službu Storage. Také obsahuje [doporučení](security-center-sql-service-recommendations.md) pro obecný stav databáze. Další informace o šifrování úložiště najdete v tématu [Povolení šifrování účtu úložiště Azure v Azure Security Center](security-center-enable-encryption-for-storage-account.md).

![Datové prostředky](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

V části **Doporučení SQL** můžete kliknout na jakékoli doporučení a získáte další podrobnosti o další akci pro řešení problému. Následující příklad ukazuje rozbalení doporučení **Auditování databáze a detekce hrozeb u databází SQL**.

![Podrobnosti o doporučení SQL](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

Okno **Povolit auditování a detekci hrozeb u databází SQL** obsahuje následující informace:

* Seznam databází SQL
* Server, na kterém jsou umístěné
* Informace o tom, zda bylo toto nastavení zděděno ze serveru nebo zda je v této databázi jedinečné
* Aktuální stav
* Závažnosti problému

Když kliknete na databázi, abyste vyřešili toto doporučení, otevře se okno **Auditování a detekce hrozeb**, jak je znázorněno na následující obrazovce.

![Auditování a detekce hrozeb](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

Pokud chcete povolit auditování, vyberte **Zapnuto** pod možností **Auditování**.

### <a name="monitor-applications"></a>Monitorování aplikací

Pokud má vaše úloha Azure aplikace, které jsou umístěné ve [virtuálních počítačích (vytvořených přes Azure Resource Managera)](../azure-resource-manager/resource-manager-deployment-model.md) se zpřístupněnými webovými porty (porty TCP 80 a 443), Security Center je může sledovat, aby se zjistily možné problémy zabezpečení a doporučily se kroky k nápravě. Když kliknete na dlaždici **Aplikace**, otevře se okno **Aplikace** s řadou doporučení v části **Doporučení pro aplikace**. Také ukazuje rozpis aplikací na hostitele, IP adresu nebo doménu a jestli je nainstalované řešení WAF:

![Stav zabezpečení aplikací](./media/security-center-monitoring/security-center-monitoring-fig8-sep2017.png)

Stejně jako u ostatních doporučení můžete kliknutím na doporučení zobrazit další podrobnosti o problému a způsobu řešení. Příklad uvedený na následujícím obrázku je aplikace, která byla identifikována jako nezabezpečená webová aplikace. Když vyberete aplikaci, která není považovaná za bezpečnou, zobrazí se následující možnost:

![Podrobnosti](./media/security-center-monitoring/security-center-monitoring-fig17-ga.png)

Tady máte seznam všech doporučení pro tuto aplikaci. Když kliknete na doporučení **Přidat firewall webových aplikací**, otevře se okno **Přidat firewall webových aplikací** s možnostmi instalace brány firewall webových aplikací (WAF) od partnera, jak je znázorněno na následujícím snímku obrazovky.

![Dialogové okno Přidat firewall webových aplikací](./media/security-center-monitoring/security-center-monitoring-fig18-ga.png)

## <a name="see-also"></a>Viz také
V tomto článku jste zjistili, jak ve službě Azure Security Center používat funkce sledování. Pokud se o službě Azure Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení ve službě Azure Security Center](security-center-policies.md): Zjistěte, jak se v Azure Security Center konfiguruje nastavení zabezpečení.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md): Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md): Zjistěte, jak sledovat stav vašich partnerských řešení.
* [Časté otázky k Azure Security Center](security-center-faq.md): Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/): Přečtěte si příspěvky o zabezpečení a dodržování předpisů Azure.
