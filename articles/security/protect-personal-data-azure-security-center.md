---
title: "Ochrana osobních dat pomocí Azure Security Center | Microsoft Docs"
description: "Použijte Centrum zabezpečení k ochraně osobních údajů. To je možné použít úsilí k zajištění souladu s obecné Data Protection nařízení (GDPR)"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 118752a030a1c300306aa3e05571bb9b624a2c20
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="protect-personal-data-from-breaches-and-attacks-azure-security-center"></a>Ochrana osobních údajů z narušení a útoky: Azure Security Center

Tento článek vám pomůže pochopit, jak používat Azure Security Center k ochraně osobních údajů z narušení a útoky. Informace uvedené v tomto vám pomůže v vaše snaze v souladu s obecné Data Protection nařízení (GDPR).

## <a name="scenario"></a>Scénář 

Velké výletních společnosti, centrálou ve Spojených státech amerických, je rozšířit jeho operací a nabídnout itineráře v Středomoří a baltský moři, jakož i Britské ostrovy. Abyste v tomto úsilí získala menší výletních Víceřádkový na základě v Itálii, Německo, Dánsko a Spojené království

Společnost používá Microsoft Azure k ukládání firemních dat v cloudu. To zahrnuje osobní identifikovatelné údaje, jako je například jména, adresy, telefonních čísel a informace o kreditní kartě. Také obsahuje informace o lidských zdrojů, jako:

- Adresy
- telefonní čísla
- daň identifikační čísla
- lékařské informace

Na řádku výletních také udržuje velké databáze potřebu a věrnost program členů. Zaměstnanci společnosti přístup k síti ze vzdálených pobočkách společnosti a agenty cesta umístěné po celém světě mají přístup k některým prostředkům společnosti.
Osobní data se přenáší po síti mezi tyto umístění a datovém centru společnosti Microsoft.

## <a name="problem-statement"></a>Popis problému

Společnost se zajímá riziko útoků na svých prostředků Azure. Chtějí, aby se zabránilo ohrožení zaměstnanců a zákazníků osobních údajů na neoprávněné osoby. Chtějí pokyny k zabránění a odpovědi nebo nápravná, jak efektivní způsob, jak sledovat probíhající zabezpečení cloudových prostředků.
Potřebují silné linií obrany před dnešní sofistikovaná a uspořádány útočníkům.

## <a name="company-goal"></a>Cílem společnosti

Jedním z cílů společnosti je k zajištění ochrany osobních údajů zaměstnanců a zákazníků osobních údajů a chrání před hrozbami. Jeden z jejich cílů je okamžitě reagovat na známky porušení zabezpečení, aby se zmírnil dopad. Způsob, jak vyhodnotit aktuální stav zabezpečení, identifikovat citlivé konfigurace a opravit, vyžaduje.

## <a name="solutions"></a>Řešení

Microsoft Azure Security Center (ASC) poskytuje do řešení pro správu zásad a monitorování integrované zabezpečení. Zajišťuje threat snadno použitelné a efektivní funkce prevence, zjišťování a odpovědi.

### <a name="prevention"></a>Prevention (Prevence)

ASC pomáhá zabránit narušení tím, že vám nastavit zásady zabezpečení, poskytují přístup za běhu a implementovat doporučení zabezpečení.

Zásady zabezpečení definuje sadu ovládacích prvků, které jsou doporučené pro prostředky v rámci předplatného. Zamknout příchozí přenosy na virtuální počítače Azure, snižuje riziko napadení lze použít pouze ve čas přístupu. Doporučení zabezpečení jsou vytvořené pomocí ASC po analýze stav zabezpečení vašich prostředků Azure.

#### <a name="how-do-i-set-security-policies-in-asc"></a>Jak nastavit zásady zabezpečení v ASC?

Zásady zabezpečení můžete nakonfigurovat pro každé předplatné. Pokud chcete určitou zásadu zabezpečení upravit, musíte mít roli vlastníka nebo přispěvatele daného předplatného. Na portálu Azure postupujte takto:

1. Vyberte **zásad** v řídicím panelu ASC.

2. Vyberte předplatné, na kterém chcete povolit zásady.

3. Zvolte **zásada Zabránění** ke konfiguraci zásad podle předplatného. **Shromažďování dat z virtuálních počítačů** musí být nastavena na **na.**

4. V **zásada Zabránění** možnosti, vyberte **na** povolit doporučení zabezpečení, které jsou relevantní pro předplatné.

![](media/protect-personal-data-azure-security-center/prevention-policy.png)

Podrobné pokyny a vysvětlení jednotlivých zásad doporučení, které je možné povolit, najdete v části [nastavení zásad zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-policies#set-security-policies).

#### <a name="how-do-i-configure-just-in-time-access-jit"></a>Jak lze nakonfigurovat pouze v přístup čas (JIT)?

Pokud je povoleno JIT, Security Center zamyká příchozí přenosy na virtuální počítače Azure pomocí vytvoření pravidla NSG. Můžete vybrat porty na virtuálním počítači, do které se uzamkne příchozí provoz směrem dolů. Pokud chcete používat JIT přístup, postupujte takto:

1. Vyberte **těsně čas virtuálních počítačů přístup k dlaždici** v okně ASC.

2. Vyberte **doporučeno** kartě.

3. V části **virtuální počítače**, vyberte virtuální počítače, které chcete povolit. To převádí zatržení vedle virtuálního počítače. 
4. Vyberte **povolení JIT** na virtuálních počítačích.
5. Vyberte **Uložit**.

Potom můžete zobrazit výchozí porty, které doporučuje ASC povolený pro JIT. Můžete také přidávat a konfigurovat nový port, na kterém chcete povolit právě v době řešení. **Těsně v čas virtuálních počítačů přístup** dlaždice v Centru zabezpečení zobrazuje počet virtuálních počítačů nakonfigurovaná pro přístup za běhu. Také ukazuje počet požadavků schválené přístup provedených během posledního týdne.

![](media/protect-personal-data-azure-security-center/jit-vm.png)

Pokyny o tom, jak to provést a další informace o těsně v čas přístup, zobrazit [spravovat přístup k virtuálním počítačům pomocí právě v čase.](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

#### <a name="how-do-i-implement-asc-security-recommendations"></a>Jak implementace doporučení zabezpečení ASC?

Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří doporučení. Doporučení vás provedou procesem konfigurace potřebných kontrol. 
1. Vyberte **doporučení** dlaždici na řídicím panelu ASC.

2. Zobrazte doporučení, které jsou uvedeny ve formátu tabulky, kde každý řádek představuje jeden doporučení.

3. K filtru doporučení, vyberte **filtru** a vyberte stav a závažnost hodnoty, které chcete vidět.

4. Pro zavření doporučení, která se nedá použít, klikněte pravým tlačítkem a vyberte **přeskočení.**

5. Vyhodnoťte, které doporučení by měla být použije první.

6. Použijte doporučení v pořadí podle priority.

Seznam možných doporučení a návodů na tom, jak používat každý, naleznete v části [Správa doporučení zabezpečení v Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

### <a name="detection-and-response"></a>Detekce a reakce

Detekce a reakce přejděte společně, jak byste chtěli reagovat, co nejrychleji zjištěno ohrožení.
Detekce hrozeb ASC funguje tak, že automaticky shromažďování informací o zabezpečení z vašich prostředků Azure, sítě a připojených partnerských řešení. ASC můžete rychle aktualizovat své detekční algoritmy, protože útočníci vydání nové a stále sofistikované zneužití. Podrobnější informace o tom, jak funguje služba detekce hrozeb je ASC, najdete v části [funkce zjišťování služby Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities).

#### <a name="how-do-i-manage-and-respond-to-security-alerts"></a>Jak spravovat a reakce na výstrahy zabezpečení?

Seznam upřednostňovaných výstrah zabezpečení se zobrazí v Centru zabezpečení společně s informacemi, které potřebujete k rychlému prozkoumání problém. Security Center také obsahuje doporučení, jak se řešení útoku. Pokud chcete spravovat výstrahy zabezpečení, postupujte takto:

1. Vyberte **výstrahy zabezpečení** dlaždici na řídicím panelu ASC. To obsahuje podrobnosti pro každou výstrahu.

2. Chcete-li filtrovat podle data, stav a závažnost výstrahy, vyberte **filtru** a potom vyberte hodnoty, které chcete zobrazit.

3. Reagovat na upozornění, vyberte ho a zkontrolujte informace a potom vyberte prostředek, který byl napaden.

4. V **popis** poli se zobrazí podrobnosti, včetně doporučené nápravy.

![](media/protect-personal-data-azure-security-center/security-alerts.png)

Podrobné pokyny pro reakce na výstrahy zabezpečení, najdete v části [Správa a zpracování výstrah zabezpečení v Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

Další informace v prošetřování výstrah zabezpečení, společnost může integrovat ASC výstrahy vlastní řešení SIEM pomocí [integrace se službou Azure protokolu](https://aka.ms/AzLog).

#### <a name="how-do-i-manage-security-incidents"></a>Jak lze spravovat incidenty zabezpečení?

V ASC je incidentu zabezpečení agregaci všechny výstrahy pro prostředek, které zarovnat kill řetězu vzory. Incident odhalí seznam souvisejících výstrah, který vám umožní získat další informace o každém výskytu. Dlaždice výstrahy zabezpečení a okno zobrazí incidenty.

Lze prohlížet a spravovat incidenty zabezpečení, postupujte takto:

1. Vyberte **výstrahy zabezpečení** dlaždici. Pokud je zjištěn bezpečnostního incidentu, zobrazí se v grafu výstrahy zabezpečení. Bude mít ikonu, která se liší od dalších výstrah.

2. Vyberte incident, chcete-li zobrazit další podrobnosti o této incidentu zabezpečení. Další podrobnosti jsou jeho úplný popis jeho závažnost, jeho aktuální stav, attacked prostředků, nápravy kroky pro incident a výstrahy, které byly do tohoto incidentu.

Můžete filtrovat zobrazíte **pouze incidenty**, **výstrahy pouze**, nebo **i**.

#### <a name="how-do-i-access-the-threat-intelligence-report"></a>Přístupu Threat Intelligence sestavy

ASC analyzují informace z více zdrojů k identifikaci hrozeb. Security Center pomáhá s reakcí na incidenty týmy prozkoumat ohrožení a oprava, zahrnuje sestavu intelligence hrozba, která obsahuje informace o ohrožení, která byla zjištěna.

Security Center má tři typy sestav hrozeb, které se může lišit na útok.
K dispozici jsou tyto sestavy:

- Sestava skupiny aktivit: obsahuje přímý dives do útočníci, jejich cíle a svoji.

- Sestava kampaně: zaměřuje se na podrobnosti o konkrétní útoku kampaně.

- Souhrnná sestava Threat: obsahuje všechny položky v předchozích dvou sestav.

Tento typ informací je velmi užitečná během reakce na incidenty, kde je probíhajícím vyšetřování pochopit zdroji útoku, útočníka motivace a co dělat ke zmírnění tohoto problému postoupíte.

1. Pro přístup k sestavě intelligence hrozby, postupujte takto:

2. Vyberte **výstrahy zabezpečení** dlaždici na řídicím panelu ASC.

3. Vyberte výstrahu zabezpečení, pro které chcete získat další informace.

4. V **sestavy** pole, klikněte na odkaz na sestavu intelligence hrozeb.

5. Otevře se soubor PDF, kterou si můžete stáhnout.

![](media/protect-personal-data-azure-security-center/security-alerts-suspicious-process.png)

Další informace o sestavě ASC threat intelligence najdete v tématu [Azure Security Center Threat Intelligence sestavy.](https://docs.microsoft.com/azure/security-center/security-center-threat-report)

### <a name="assessment"></a>Posouzení

Usnadní testování, hodnocení a vyhodnocení lepšímu zabezpečení, ASC poskytuje pro vyhodnocení integrované ohrožení zabezpečení s Qualys cloudu agentů, jako součást své doporučení součásti virtuálního počítače.

Qualys agent sestav ohrožení zabezpečení dat Qualys platformy pro správu, které pak odešle ohrožení zabezpečení a sledování dat stavu zpět do ASC. Doporučení k přidání řešení pro vyhodnocení ohrožení zabezpečení se zobrazí v **doporučení** okno na řídicí panel ASC.

Po nainstalování řešení posouzení ohrožení zabezpečení na cílovém virtuálním počítači Security Center tento virtuální počítač prohledá a zjistí ohrožení zabezpečení systému a aplikací. Zjištěné problémy se zobrazí pod možností **Doporučení pro virtuální počítače**.

#### <a name="how-do-i-implement-a-vulnerability-assessment-solution"></a>Jak implementovat řešení pro vyhodnocení ohrožení zabezpečení? 

Pokud virtuální počítač nemá řešení pro vyhodnocení integrované ohrožení zabezpečení už nasazená, Security Center doporučuje ji nainstalovat.

1. Na řídicím panelu ASC na **doporučení** vyberte **přidat řešení pro vyhodnocení ohrožení zabezpečení.**

2. Vyberte virtuální počítače, ve které chcete nainstalovat řešení pro vyhodnocení ohrožení zabezpečení.

3. Klikněte na **nainstalovat na virtuální počítače [počet].**

4. Vyberte jedno partnerské řešení v Azure Marketplace, nebo pod **použít existující řešení** vyberte **Qualys.**

5. Aktualizace nastavení automatického můžete zapnout nebo vypnout **partnerských řešení** okno.

Další pokyny o tom, jak implementovat řešení pro vyhodnocení ohrožení zabezpečení najdete v tématu [vyhodnocení ohrožení zabezpečení v Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

## <a name="next-steps"></a>Další postup

- [Úvodní příručka Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-get-started)

- [Úvod do Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

- [Integrace Azure Security Center výstrahy s integrací protokolů Azure](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration)

- [Nárůst Azure Security Center s vyhodnocení integrované ohrožení zabezpečení](https://blogs.msdn.microsoft.com/azuresecurity/2016/12/16/boost-azure-security-center-with-integrated-vulnerability-assessment/)
