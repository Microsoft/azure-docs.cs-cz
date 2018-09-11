---
title: Ochrana vašich počítačů a aplikací ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument popisuje doporučení ve službě Security Center, které vám pomůžou chránit virtuální počítače a počítače a webové aplikace a služby App Service Environment.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/28/2018
ms.author: rkarlin
ms.openlocfilehash: 1692e111d48a6e4574b2b114c0de84d9bc9f3203
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299827"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Ochrana vašich počítačů a aplikací ve službě Azure Security Center
Azure Security Center analyzuje stav zabezpečení vašich prostředků Azure. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří se doporučení, která vás provede procesem konfigurace potřebných kontrol. Doporučení platí pro typy prostředků Azure: virtuální počítače (VM) a počítačů, aplikací, sítí, SQL a identit a přístupů.

Tento článek se zabývá doporučení, která platí pro počítače a aplikace.

## <a name="monitoring-security-health"></a>Sledování stavu zabezpečení
Můžete monitorovat stav zabezpečení vašich prostředků na **Security Center – přehled** řídicího panelu. **Prostředky** část obsahuje počet problémů zjištěných a stavu zabezpečení pro každý typ prostředku.

Seznam všech problémů můžete zobrazit tak, že vyberete **doporučení**. Další informace o tom, jak používat doporučení, najdete v části [implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).

Úplný seznam výpočetní prostředky a aplikace služeb doporučení najdete v tématu [doporučení](security-center-virtual-machine-recommendations.md).

Chcete-li pokračovat, vyberte **výpočty a aplikace** pod **prostředky** nebo hlavní nabídce služby Security Center.
![Řídicí panel Security Center][1]

## <a name="monitor-compute-and-app-services"></a>Monitorování služby Compute a App services
V části **Compute**, existují čtyři karty:

- **Přehled**: monitorování a doporučení identifikované pomocí služby Security Center.
- **Virtuální počítače a počítače**: seznam virtuálních počítačů, počítačů a aktuální stav zabezpečení jednotlivých.
- **Cloud Services**: seznam vašich webových a pracovních rolí monitorovaných pomocí služby Security Center.
- **App Service (Preview)**: seznam prostředí App service a aktuální stav zabezpečení jednotlivých.
Chcete-li pokračovat, vyberte **výpočty a aplikace** pod **prostředky** nebo hlavní nabídce služby Security Center.

![Compute][2]

Na každé kartě můžete mít několik částí a v každé části můžete vybrat jednotlivé možnosti, abyste zobrazili další podrobnosti o doporučeném postupu k vyřešení konkrétního problému.

### <a name="monitoring-recommendations"></a>Doporučení pro monitorování
V této části ukazuje celkový počet virtuálních počítačů a počítačů, které byly inicializovány pro automatické zřizování a jejich aktuální stavy. Tento příklad obsahuje jedno doporučení, **Problémy s monitorováním stavu agentů**. Vyberte toto doporučení.

![Problémy se stavem agenta monitorování][3]

Doporučení **Problémy s monitorováním stavu agentů** se otevře. Tady budou uvedené virtuální počítače a počítače, které služba Security Center nedokáže úspěšně monitorovat. Výběrem virtuálního počítače nebo počítače zobrazíte podrobné informace. **STAV MONITOROVÁNÍ** uvádí důvod, proč Security Center nemůže monitorovat. Seznam hodnot, popisů a postupů řešení pro hodnoty **STAV MONITOROVÁNÍ** najdete v [průvodci odstraňováním potíží pro Security Center](security-center-troubleshooting-guide.md).

### Nemonitorované virtuální počítače a počítače <a name="unmonitored-vms-and-computers"></a>
Virtuální počítač nebo počítač je nemonitorovaný službou Security Center, pokud počítač není spuštěný rozšíření Microsoft Monitoring Agent. Na počítači může mít místní agent již nainstalovaný, například přímý agent OMS nebo SCOM agent. Počítače s těmito agenty se identifikují jako nemonitorované, protože tyto agenty plně nepodporuje ve službě Security Center. Pokud chcete naplno využívat všechny schopnosti služby Security Center, potřebujete rozšíření Microsoft Monitoring Agent.

Rozšíření můžete nainstalovat na nemonitorovaný virtuální počítač nebo počítač kromě již nainstalovanému místnímu agentu. Nakonfigurujte oba agenty stejným způsobem a připojte je ke stejnému pracovnímu prostoru. Tím umožníte službě Security Center pracovat s rozšířením Microsoft Monitoring Agent a shromažďovat data. Pokyny k instalaci rozšíření Microsoft Monitoring Agent najdete v tématu popisujícím [povolení rozšíření virtuálního počítače](../log-analytics/log-analytics-quick-collect-azurevm.md).

Další informace o důvodech, proč se službě Security Center nedaří úspěšně monitorovat virtuální počítače a počítače inicializované pro automatické zřizování, najdete v tématu popisujícím [problémy s monitorováním stavu agenta](security-center-troubleshooting-guide.md#mon-agent).

### <a name="recommendations"></a>Doporučení
Tato část obsahuje sadu doporučení jednotlivých virtuálních počítačů a počítačů, webových a pracovních rolí, Azure App Service Web Apps a Azure App Service Environment, která monitoruje Security Center. První sloupec uvádí doporučení. Druhý sloupec zobrazuje celkový počet prostředků, které jsou ovlivněny tímto doporučením. Třetí sloupec zobrazuje závažnost problému, jak je znázorněno na následujícím snímku obrazovky:

![Doporučení][4]

Každé doporučení obsahuje sadu akcí, které můžete provést po výběru. Pokud vyberete třeba **chybějící aktualizace systému**, počet virtuálních počítačů a počítačů s chybějícími opravami a závažnost chybějící aktualizace se zobrazí, jak je znázorněno na následujícím snímku obrazovky:

![Nainstalovat aktualizace systému][5]

**Nainstalovat aktualizace systému** obsahuje souhrn důležitých aktualizací ve formátu grafu, jeden pro Windows a jeden pro Linux. Druhá část obsahuje tabulku s následujícími informacemi:

- **NÁZEV:** Název chybějící aktualizace.
- **POČET VIRTUÁLNÍCH POČÍTAČŮ A POČÍTAČŮ:** Celkový počet virtuálních počítačů a počítačů, na kterých chybí daná aktualizace.
- **ZÁVAŽNOST aktualizace**: Popisuje závažnost tohoto konkrétního doporučení:

    - **Kritické**: ohrožení zabezpečení existuje u významného prostředku (aplikace, virtuálního počítače nebo skupiny zabezpečení sítě) a vyžaduje pozornost.
    - **Důležité**: nekritické nebo další kroky jsou potřebné k dokončení procesu nebo odstranění ohrožení.
    - **Střední**: ohrožení zabezpečení, mělo by se řešit, ale nevyžaduje okamžitou pozornost. (Ve výchozím nastavení nejsou doporučení s nízkou závažností uváděny, ale pokud je chcete zobrazit, je možné je vyfiltrovat.)


- **STAV**: Aktuální stav doporučení:

    - **Otevřené**: Doporučení dosud nebylo řešeno.
    - **Probíhá**: Doporučení se aktuálně na tyto prostředky používá a není třeba provádět žádnou akci.
    - **Vyřešeno**: Doporučení už je dokončené. (Pokud byl problém vyřešen, položka je vyšedlá.)

Pokud chcete zobrazit podrobnosti o doporučení, klikněte na název chybějící aktualizace v seznamu.

![Podrobnosti doporučení][6]

> [!NOTE]
> Bezpečnostní doporučení uvedená tady jsou stejné jako ty v rámci **doporučení** dlaždici. Zobrazit [implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) Další informace o tom, jak řešit doporučení.
>
>

### <a name="vms-and-computers"></a>Virtuální počítače a počítače
Část virtuálních počítačů a počítačů poskytuje přehled o všech doporučení pro virtuální počítač a počítač. Každý sloupec představuje jednu sadu doporučení, jak ukazuje následující snímek obrazovky:

![Doporučení pro virtuální počítač a počítač][7]

Existují čtyři typy ikon v tomto seznamu:

![Počítač umístěný mimo Azure][8] Počítač mimo Azure.

![Virtuální počítač Azure Resource Manageru][9] Virtuální počítač Azure Resource Manageru.

![Virtuální počítač Azure Classic][10] Virtuální počítač Azure Classic.

![Virtuální počítače uvedené v pracovním prostoru][11] Virtuální počítače identifikované pouze z pracovního prostoru, který je součástí zobrazeného předplatného. To zahrnuje virtuální počítače z jiných předplatných, které se hlásí k pracovnímu prostoru v tomto předplatném, a virtuální počítače nainstalované s přímým agentem SCOM, které nemají žádné ID prostředku.

Ikona, který se zobrazí pod každým doporučením, pomáhá rychle identifikovat virtuální počítač a počítač, který vyžaduje pozornost a typ doporučení se jedná. Můžete také použít možnost filtrování a jaké možnosti na této obrazovce se zobrazí.

![Filtr][12]

V předchozím příkladu má jeden virtuální počítač kritické doporučení týkající se služby endpoint protection. Vyberte virtuální počítač, chcete-li získat další informace:

![Kritické doporučení][13]

Tady vidíte podrobné informace o zabezpečení pro virtuální počítač nebo počítač. V dolní části vidíte doporučenou akci a závažnost jednotlivých problémů.

### <a name="cloud-services"></a>Cloud Services
Pro cloudové služby se doporučení vytvoří, když je verze operačního systému zastaralá, jak je znázorněno na následujícím snímku obrazovky:

![Cloud Services][14]

V případě, kdy máte doporučení (což není případ předchozího příkladu) budete muset postupovat podle kroků v doporučení a aktualizovat verzi operačního systému. Když je k dispozici aktualizace, obdržíte výstrahu (červenou nebo oranžovou – v závislosti na závažnosti problému). Když vyberete tuto výstrahu v řádku webová role 1 (spouští Windows Server s vaší webovou aplikací automaticky nasazenou do služby IIS) nebo WorkerRole1 (spouští Windows Server s vaší webovou aplikací automaticky nasazenou do služby IIS), zobrazí další podrobnosti o toto doporučení, jak je znázorněno Následující snímek obrazovky:

![WorkerRole1][15]

Pokud chcete zobrazit podrobnější vysvětlení tohoto doporučení, klikněte na **Aktualizovat verzi operačního systému** ve sloupci **POPIS**.

![Aktualizace verze operačního systému][16]

### <a name="app-services-preview"></a>App Services (Preview)

> [!NOTE]
> Monitorování služby App Service je ve verzi preview a je k dispozici jenom na úrovni Standard služby Security Center. Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md).
>
>

V části **App services**najdete seznam vaší služby App service Environment a provést shrnutí stavu na základě posouzení Security Center.

![Aplikační služby][17]

Existují tři typy ikon v tomto seznamu:

![Prostředí App services][18] Prostředí App services.

![Webová aplikace][19] Webová aplikace.

![Aplikace – funkce][24] Použití funkce.

1. Vyberte webovou aplikaci. Souhrnné zobrazení otevřete tři karty:

  - **Doporučení**: založené na posouzení, které provádí služba Security Center, která selhala.
  - **Předaný posouzení**: seznam posouzení, které provádí služba Security Center, který předává.
  - **Nedostupná posouzení**: seznam vyhodnocení, které se nepovedlo spustit kvůli chybě nebo doporučení není relevantní pro konkrétní službu App service

  V části **doporučení** se seznam doporučení pro vybranou webovou aplikaci a závažnost jednotlivých doporučení.

  ![Souhrnné zobrazení][20]

2. Vyberte doporučení pro popis doporučení a seznam prostředků není v pořádku, v dobrém stavu prostředků a nezkontrolované prostředky.

  ![Popis doporučení][21]

  V části **předaný posouzení** je seznam vyhovující posouzení.  Závažnost těchto hodnocení je vždy zelená.

  ![Vyhovující posouzení][22]

3. Vyberte ze seznamu popis posouzení, seznam prostředků není v pořádku a v pořádku a seznam nezkontrolované prostředky předané posouzení. Je na kartě pro prostředky není v pořádku, ale tento seznam je prázdný, vždy od předaný posouzení.

    ![Prostředky, které jsou v pořádku][23]



## <a name="next-steps"></a>Další postup
Další informace o doporučení, které se vztahují na jiné typy prostředků Azure, naleznete v následujících tématech:


* [Vysvětlení služby Azure Security Center doporučení pro virtuální počítače](security-center-virtual-machine-recommendations.md)
* [Monitorování identity a přístupu v Azure Security Center](security-center-identity-access.md)
* [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)
* [Ochrana služby Azure SQL ve službě Azure Security Center](security-center-sql-service-recommendations.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.

<!--Image references-->
[1]: ./media/security-center-virtual-machine-recommendations/overview.png
[2]: ./media/security-center-virtual-machine-recommendations/compute.png
[3]: ./media/security-center-virtual-machine-recommendations/monitoring-agent-health-issues.png
[4]: ./media/security-center-virtual-machine-recommendations/compute-recommendations.png
[5]: ./media/security-center-virtual-machine-recommendations/apply-system-updates.png
[6]: ./media/security-center-virtual-machine-recommendations/missing-update-details.png
[7]: ./media/security-center-virtual-machine-recommendations/vm-computers.png
[8]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png
[9]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png
[10]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png
[11]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png
[12]: ./media/security-center-virtual-machine-recommendations/filter.png
[13]: ./media/security-center-virtual-machine-recommendations/vm-detail.png
[14]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png
[15]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new3.png
[16]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png
[17]: ./media/security-center-virtual-machine-recommendations/app-services.png
[18]: ./media/security-center-virtual-machine-recommendations/ase.png
[19]: ./media/security-center-virtual-machine-recommendations/web-app.png
[20]: ./media/security-center-virtual-machine-recommendations/recommendation.png
[21]: ./media/security-center-virtual-machine-recommendations/recommendation-desc.png
[22]: ./media/security-center-virtual-machine-recommendations/passed-assessment.png
[23]: ./media/security-center-virtual-machine-recommendations/healthy-resources.png
[24]: ./media/security-center-virtual-machine-recommendations/function-app.png
