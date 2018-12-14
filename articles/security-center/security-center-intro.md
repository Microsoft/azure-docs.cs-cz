---
title: Co je Azure Security Center? | Microsoft Docs
description: Můžete se dozvědět o službě Azure Security Center, jejích klíčových funkcích a způsobu práce.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/5/2018
ms.author: rkarlin
ms.openlocfilehash: 88aa9a9003363f86db9214c62adff962bbd345cf
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344155"
---
# <a name="what-is-azure-security-center"></a>Co je Azure Security Center?

Azure Security Center je jednotná infrastruktura zabezpečení správy, která posiluje stav zabezpečení svých datových center a poskytuje pokročilou ochranu před hrozbami napříč vašimi hybridními úlohami v cloudu –, ať už jste v Azure nebo ne - stejně jako v místním prostředí.

Udržování vašich prostředků bezpečné je podílí svému poskytovateli cloudu, Azure a vy, Zákazník. Budete muset zajistit, aby že vaše úlohy byly zabezpečené při přesunu do cloudu a současně, při přesunu na IaaS (infrastruktura jako služba), že existuje další odpovědnosti zákazníka, než došlo v PaaS (platforma jako služba) a SaaS (software jako služba). Azure Security Center zajišťuje nástroje potřebné k posílení vaší síti, zabezpečte vaše služby a ujistěte se, že jste to skutečně o stavu zabezpečení.

Azure Security Center řeší tři nejnaléhavějšími výzvám zabezpečení:

-   **Rychle se měnící úlohy** – jde šifrování a výzvy cloudu. Na jedné straně koncoví uživatelé získávají lepší. Na druhé jak můžete zajistit, že lidé neustále se měnící services používají a vytváření až vaše standardy zabezpečení a dodržujte doporučené postupy zabezpečení?

-   **Zdokonalují útoky** – bez ohledu na to bude spouštět úlohy, útoky dále zobrazovat další sofistikované. Máte k zabezpečení vašich úloh veřejného cloudu, které jsou aktivní, protilehlé zatížení sítě Internet, který bude zranitelný můžete ještě více pokud neodpovídají zabezpečení nejlepší postupy.

-   **Dovednosti zabezpečení je nedostatek** – počet výstrah zabezpečení a systémy pro výstrahy daleko outnumbers počet správců nezbytné pozadí a prostředí do Ujistěte se, že prostředí jsou chráněné. Zachování aktuální s nejnovějšími útoky výzvou je i konstantní, v důsledku toho nebudete zůstat na místě ve světě zabezpečení je neustále se měnící front.

Můžete chránit s těmito výzvami, Security Center vám poskytne nástroje pro:

-   **Posílení stavu zabezpečení**: Security Center vyhodnocuje prostředí a umožňuje porozumět stavu vašich prostředků, jsou zabezpečené, nebo ne?

-   **Ochrana proti hrozbám**: Security Center vyhodnocuje své pracovní zatížení a vyvolá ohrožení ochrany před únikem informací doporučení a výstrahy detekce hrozeb.

-   **Rychleji získat zabezpečené**: Ve službě Security Center všechno, co se provádí v cloudu rychlost. Protože je nativně integrováno, nasazení služby Security Center je jednoduché, vám poskytnou autoprovisioning a ochrana pomocí služeb Azure.

## <a name="architecture"></a>Architektura

Protože Security Center je nativně součástí Azure, služby PaaS v Azure – včetně Service Fabricu monitorovat databází SQL a účtů úložiště – a chráněné pomocí služby Security Center, aniž by se vyžadovala každého nasazení.

Kromě toho Security Center chrání servery umístěné mimo Azure a virtuální počítače v cloudu nebo místně a pro servery se systémy Windows a Linux, po instalaci agenta Microsoft Monitoring Agent do nich. Virtuální počítače Azure jsou ve službě Security Center automatické zřizování.

Události shromážděné z agentů a z Azure se korelují v modulu analýzy zabezpečení zajistit, že přizpůsobená doporučení (posílení zabezpečení úloh), že by měl postupovat podle zajistit, aby že vaše úlohy jsou v bezpečí a výstrahy detekce hrozeb. Měli byste prošetřit tyto výstrahy co nejdříve a ujistěte se, že útoky se zlými úmysly nejsou probíhat ve vašich úloh.

## <a name="strengthen-security-posture"></a>Posílení stavu zabezpečení

Azure Security Center umožňuje posílení stavu zabezpečení. To znamená, že pomáhá identifikovat a provádět úlohy posílení zabezpečení doporučujeme jako osvědčený postup zabezpečení a implementovat počítačů, datových služeb a aplikací. To zahrnuje správu a vynucování zásad zabezpečení a zajištění virtuálních počítačů Azure, mimo Azure serverů a služeb Azure PaaS jsou kompatibilní. Security Center vám poskytuje nástroje, je potřeba mít pohled z ptačí perspektivy na vaše úlohy s fokusem viditelnost na místo zabezpečení vaší sítě.

### <a name="manage-organization-security-policy-and-compliance"></a>Správa zásad zabezpečení organizace a dodržování předpisů

Je zabezpečení základní vědět a ujistěte se, že vaše úlohy jsou v bezpečí a začíná s přizpůsobené zavedené zásady zabezpečení. Vzhledem k tomu, že všechny zásady ve službě Security Center jsou postavené na Azure policy ovládací prvky, získáváte celý rozsah a flexibilitu **špičkové zásad řešení**. Ve službě Security Center můžete nastavit a spustit na skupiny pro správu, mezi předplatnými a to i pro celého tenanta.

![Řídicí panel Security Center](media/security-center-intro/sc-dashboard.png)

Security Center pomáhá **identifikovat stínového IT předplatná**. Pohledem na předplatné s názvem **nepokryto** na řídicím panelu, které znáte okamžitě, když budou existovat nově vytvořené předplatná a ujistěte se, že jsou předmětem zásad a chráněné službou Azure Security Center.

![Řídicí panel Security Center zásady](media/security-center-intro/sc-policy-dashboard.png)

Rozšířené možnosti monitorování ve službě Security Center vám také umožní **sledovat a spravovat dodržování předpisů a zásad správného řízení v čase**.  **Celkového dodržování předpisů** vám poskytne míra kolik předplatných jsou kompatibilní se zásadami, které jsou přidružené k vaší úlohy. 

![Zásad služby Security Center v čase](media/security-center-intro/sc-policy-time.png)

### <a name="continuous-assessments"></a>Průběžným vyhodnocováním

Security Center průběžně zjišťuje nové prostředky, které se nasazují napříč vašimi úlohami a vyhodnocuje, zda jsou nakonfigurovány podle osvědčených postupů zabezpečení, v opačném případě se příznakem a získat seznam seřazený podle priority doporučení pro co je potřeba vyřešit z důvodu ochrany vašich počítačů.

Jedním z nejúčinnějších nástrojů Security Center nabízí průběžné monitorování stavu zabezpečení sítě je **mapy sítě**. Mapy umožňuje zobrazit topologii úloh, abyste si mohli zobrazit, pokud každý uzel je správně nakonfigurované. Uvidíte jak uzly připojení, která vám blokovat nežádoucí připojení, které mohou potenciálně ovládat podél vaší síti útočník.

![Security Center mapování sítě](media/security-center-intro/sc-net-map.png)

Security Center umožňuje snížení rizik souvisejících s zabezpečení výstrahy jeden krok jednodušší, tak, že přidáte **zabezpečené skóre**. Zabezpečené hodnocení jsou nyní spojené s každou doporučení se zobrazí, které vám pomohou pochopit, jak jednotlivá doporučení je důležitá pro celkové postavení zabezpečení. To je důležité v umožňuje **stanovení priorit práce zabezpečení**.

![Skóre zabezpečení Security Center](media/security-center-intro/sc-secure-score.png)

### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>Optimalizace a zlepšit zabezpečení konfigurací doporučuje ovládacích prvků

Základem Azure Security Center hodnotu spočívá v jeho doporučení. Doporučení jsou přizpůsobená pro konkrétní zabezpečením na své pracovní zatížení a Security Center provádí správu práce na zabezpečení, nejen vyhledávání vaše ohrožení zabezpečení, ale poskytuje konkrétní pokyny, jak zbavit.

![Doporučení služby Security Center](media/security-center-intro/sc-recommendations.png)

Tímto způsobem Security Center vám umožní nejen k nastavení zásad zabezpečení, ale chcete-li použít konfiguraci zabezpečení standardů napříč vašimi prostředky.

Doporučení pomáhají omezit možnost útoku na každé z vašich prostředků. Která obsahuje virtuální počítače Azure, servery umístěné mimo Azure a služeb Azure PaaS, jako jsou účty SQL a službu Storage a další – kde jednotlivých typů prostředků se bude hodnotit jinak a má své vlastní standardy.

![Příklad doporučení Security Center](media/security-center-intro/sc-recommendation-example.png)

## <a name="protect-against-threats"></a>Ochrana před hrozbami

Ochrana před internetovými útoky Security Center umožňuje detekovat a ochranu před hrozbami v infrastruktury jako služby (IaaS) vrstvy, servery umístěné mimo Azure stejně jako u platformy jako služby (PaaS) v Azure.

Ochrana před internetovými útoky Security Center obsahuje fusion řetězci úkonů útočníka, analýzy, která koreluje s automaticky výstrahy ve vašem prostředí na základě analýzy kybernetických řetězu událostí, které vám pomůžou lépe porozumět celý příběh kampaních obsahujících útoky, kde byla spuštěna a co druh dopad, který měl na vaše prostředky.



![Doporučení služby Security Center útoku](media/security-center-intro/sc-attack-recommendation.png)

### <a name="advanced-threat-protection"></a>Rozšířená ochrana před internetovými útoky

Se službou Security Center získáte nativní integraci se sadou Windows Defender Advanced Threat Protection úprav. To znamená, že bez jakékoli konfigurace Windows virtual machines a servery jsou plně integrované s Security Center doporučení a posouzení. Pokročilá detekce hrozeb se také nabízí připravené pro servery a virtuální počítače s Linuxem.

Kromě toho Security Center vám umožní automatizovat zásady řízení aplikací v serverových prostředích. Ovládací prvky adaptivního řízení aplikací ve službě Security Center povolit vytváření seznamu povolených začátku do konce aplikace na Windows serverech. Není nutné vytvářet pravidla a zkontrolujte porušení, vše na jednom automaticky za vás.

### <a name="protect-paas"></a>Ochrana PaaS

Security Center pomáhá zjišťovat hrozby ve službách Azure PaaS. Můžete detekovat hrozby pro služby Azure, včetně služby Azure App Service, Azure SQL, účet služby Azure Storage a další datové služby. Můžete taky využít výhod nativní integraci s Microsoft Cloud App Security uživatelů a entit behaviorální analýzy (Behavioral) provádět detekci anomálií na vaše protokoly aktivit Azure.

### <a name="block-brute-force-attacks"></a>Blok hrubou vynutit útoky

Security Center pomáhá omezit vystavení útokům hrubou silou. Díky snížení přístup k portům virtuální počítač, pomocí přístup k virtuálním počítačům v čase, můžete posílení zabezpečení vaší sítě tím, že zabráníte zbytečným přístup. Na vybrané porty, můžete nastavit zásady zabezpečený přístup jenom Autorizovaní uživatelé, povolené zdroje rozsahy IP adres nebo IP adresy a po omezenou dobu.

![Security Center útok hrubou silou](media/security-center-intro/sc-brute-force.png)

### <a name="protect-data-services"></a>Ochrana dat služby

Security Center obsahuje funkce, které vám pomůžou provést automatické klasifikace dat v Azure SQL. Můžete také získat posouzeními assessments for potenciální ohrožení zabezpečení napříč službami Azure SQL a službu Storage a doporučení, jak je zmírnit.

## <a name="get-secure-faster"></a>Zajištění zabezpečení rychleji

Nativní integrace se službou Azure (včetně Azure Policy a Log Analytics) v kombinaci s bezproblémovou integraci s další řešení zabezpečení, jako je Microsoft Cloud App Security a Windows Defender Advanced Threat Protection nápovědy, ujistěte se, že vaše zabezpečení společnosti Microsoft řešení je komplexní, jakož i jednoduchý na připojení a zavedení.

Kromě toho můžete rozšířit nad rámec Azure kompletní řešení pro úlohy běžící v jiných cloudech a v místních datových centrech.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Automaticky rozpoznávat a připojení prostředků Azure

Security Center poskytuje bezproblémové a nativní integraci s Azure a Azure prostředky. To znamená, že vám přinášejí na jednom místě zabezpečení kompletní scénáře zahrnující Azure Policy a integrované zásady služby Security Center ve všech vašich prostředků Azure a ujistěte se, že celou věc, kterou se automaticky využije na nově nalezené zdroje během vytváření v Azure.

Shromažďování protokolů rozsáhlé – protokoly z Windows a Linuxu jsou všechny využívají v analytickém modulu zabezpečení a slouží k vytvoření doporučení a výstrahy.

## <a name="next-steps"></a>Další postup

- Pokud chcete začít využívat Security Center, musíte mít předplatné Microsoft Azure. Pokud předplatné nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
- Cenová úroveň Free služby Security Center je povolená s vaším předplatným Azure. Pokud chcete využívat pokročilé možnosti správy zabezpečení a detekce hrozeb, musíte upgradovat na cenovou úroveň Standard. Na úrovni Standard můžete volného vyzkoušeli. Další informace najdete na [stránce s cenami za Security Center](https://azure.microsoft.com/pricing/details/security-center/).
- Pokud jste připraveni povolit Security Center úrovně Standard, nyní [rychlý start: Připojení předplatného Azure na Security Center úrovně Standard](security-center-get-started.md) vás provede jednotlivými kroky.

