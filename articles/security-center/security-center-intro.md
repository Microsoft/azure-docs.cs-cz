---
title: Co je Azure Security Center? | Microsoft Docs
description: Můžete se dozvědět o službě Azure Security Center, jejích klíčových funkcích a způsobu práce.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2019
ms.author: memildin
ms.openlocfilehash: e02fd0de00b8feed15fcd51b657afcc015d816f2
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202104"
---
# <a name="what-is-azure-security-center"></a>Co je Azure Security Center?

Azure Security Center je jednotný systém pro správu zabezpečení infrastruktury, který posílí stav zabezpečení vašich datových center a poskytuje rozšířenou ochranu před hrozbami napříč vašimi hybridními úlohami v cloudu – ať už jsou v Azure, nebo ne, a také místně.

Zajištění bezpečnosti vašich prostředků je společné úsilí mezi vaším poskytovatelem cloudu, Azure a zákazníkem. Je nutné zajistit, aby vaše úlohy byly při přesunu do cloudu zabezpečené, a když přesunete na IaaS (infrastruktura jako služba), je k dispozici více zodpovědnosti zákazníka, než kolik jich bylo v PaaS (platforma jako služba), a SaaS (software jako služba). Azure Security Center poskytuje nástroje potřebné k posílení vaší sítě, zabezpečení služeb a zajištění, že jste na stav zabezpečení.

Azure Security Center řeší tři naléhavé problémy se zabezpečením:

-   **Rychle se měnící úlohy** – jedná se o sílu i test cloudu. Na jedné straně je koncoví uživatelé k tomu oprávněni. V ostatních případech se můžete ujistit, že stále se měnící služby, které používají a které se vytvářejí, patří do standardů zabezpečení a dodržují osvědčené postupy zabezpečení?

-   **Stále sofistikované útoky** – bez ohledu na to, jak vaše úlohy spouštíte, jsou útoky mnohem důmyslnější. Musíte zabezpečit svoje úlohy veřejného cloudu, což jsou vlastněné internetové úlohy, které vám pomůžou ještě více zranitelnější, pokud nedodržujete osvědčené postupy zabezpečení.

-   Důležité informace o **zabezpečení jsou v krátkém** vlastnictví – počet výstrah zabezpečení a systémů s výstrahami, které výrazně vycházejí z počtu správců, kteří potřebují zajistit, aby byla vaše prostředí chráněná. Udržování aktuálnosti díky nejnovějším útokům je stálou výzvou, takže není možné zůstat na místě, zatímco je svět zabezpečení neustále se měnícím frontou.

Abychom vám pomohli chránit před těmito výzvami, Security Center poskytuje nástroje pro:

-   **Posílit zabezpečení stav**: Security Center posuzuje vaše prostředí a umožňuje pochopit stav svých prostředků a to, jestli jsou zabezpečené.

-   **Ochrana před hrozbami**: Security Center posuzuje vaše úlohy a vyvolává doporučení prevence hrozeb a výstrahy detekce hrozeb.

-   **Rychlejší získání zabezpečení**: V Security Center se všechno provádí v rychlosti cloudu. Vzhledem k tomu, že je nativně integrovaná, nasazení Security Center je jednoduché a poskytuje vám možnost autozřizování a ochrana pomocí služeb Azure.

## <a name="architecture"></a>Architektura

Vzhledem k tomu, že Security Center je nativně součástí Azure, služby PaaS v Azure – včetně Service Fabric, databází SQL a účtů úložiště – jsou monitorovány a chráněny Security Center bez nutnosti jakéhokoli nasazení.

Kromě toho Security Center chránit i servery a virtuální počítače, které nejsou v Azure, v cloudu i v místním prostředí, a to pomocí instalace Microsoft Monitoring Agent na serverech se systémem Windows i Linux. Virtuální počítače Azure se automaticky zřídí v Security Center.

Události shromážděné z agentů a z Azure jsou spojené s modulem Security Analytics a poskytují vám přizpůsobená doporučení (posílení úloh), která vám pomůžou zajistit zabezpečení vašich úloh a upozornění detekce hrozeb. Tyto výstrahy byste měli prozkoumat co nejdřív, abyste se ujistili, že se na vašich úlohách neprovádějí škodlivé útoky.

Pokud povolíte Security Center, v části Security Center kategorie se tato zásada zabezpečení Security Center zařadí do Azure Policy jako integrovaná iniciativa. Předdefinovaná iniciativa se automaticky přiřadí všem Security Center registrovaným předplatným (úrovně Free nebo Standard). Integrovaná iniciativa obsahuje pouze zásady auditu. Další informace o zásadách Security Center v Azure Policy najdete v tématu [práce se zásadami zabezpečení](tutorial-security-policy.md).

## <a name="strengthen-security-posture"></a>Posílení stavu zabezpečení

Azure Security Center vám umožní posílit stav zabezpečení. To znamená, že vám pomůže identifikovat a provádět úlohy posílení zabezpečení doporučené jako osvědčené postupy zabezpečení a implementovat je napříč vašimi počítači, datovými službami a aplikacemi. To zahrnuje správu a vynucování zásad zabezpečení a zajištění, že vaše virtuální počítače Azure, servery mimo Azure a služby Azure PaaS splňují předpisy. Security Center poskytuje nástroje, které potřebujete k zobrazení očí na vašich úlohách, s soustředěnou viditelností na majetku zabezpečení vaší sítě. 

### <a name="manage-organization-security-policy-and-compliance"></a>Správa zásad zabezpečení a dodržování předpisů v organizaci

Je to základní zabezpečení, které se dozvíte a zajistěte, aby byly vaše úlohy zabezpečené a že se spustí s přizpůsobenými zásadami zabezpečení. Vzhledem k tomu, že všechny zásady v Security Center jsou postavené nad ovládacími prvky zásad Azure, získáte celou škálu a flexibilitu **špičkového řešení zásad**. V Security Center můžete nastavit, aby se zásady spouštěly ve skupinách pro správu, mezi předplatnými a dokonce i pro celého tenanta.

![Řídicí panel Security Center](media/security-center-intro/sc-dashboard.png)

Security Center vám pomůže **identifikovat Stínová předplatná**. Zobrazením předplatných, která **nejsou** pokrytá na řídicím panelu, můžete okamžitě zjistit, jestli existují nově vytvořená předplatná, a že jsou pokryté vašimi zásadami a chráněny pomocí Azure Security Center.

![Řídicí panel zásad Security Center](media/security-center-intro/sc-policy-dashboard.png)

Rozšířené možnosti monitorování v Security Center také umožňují **sledovat a spravovat dodržování předpisů a zásady správného řízení v průběhu času**. **Celkové dodržování předpisů** poskytuje míru, kolik vašich předplatných vyhovuje zásadám přidruženým k vašim úlohám. 

![Zásady Security Center v průběhu času](media/security-center-intro/sc-policy-time.png)

### <a name="continuous-assessments"></a>Nepřetržité hodnocení

Security Center nepřetržitě zjišťuje nové prostředky, které jsou nasazené napříč vašimi úlohami, a vyhodnocuje, jestli jsou nakonfigurované v souladu s osvědčenými postupy zabezpečení, pokud ne, jsou označeny a vy získáte seznam doporučení pro co Chcete-li chránit počítače, je třeba ji opravit.

Jedním z nejúčinnějších nástrojů, které Security Center nabízí nepřetržité monitorování stavu zabezpečení vaší sítě, je **Mapa sítě**. Mapa vám umožní zobrazit topologii vašich úloh, abyste viděli, jestli je každý uzel správně nakonfigurovaný. Vidíte, jak jsou uzly připojené, což vám pomůže zablokovat nechtěné připojení, která by mohla útočníkovi usnadnit nárůst vaší sítě.

![Mapa Security Center sítě](media/security-center-intro/sc-net-map.png)

Security Center zjednodušuje výstrahy zabezpečení o krokování v jednom kroku přidáním **zabezpečeného skóre**. Pro každé doporučení, které obdržíte, se teď přiřadí bezpečnostní skóre, které vám pomohou pochopit, jak důležité je, aby každé doporučení bylo stav zabezpečení. To je zásadní postup, který vám umožní **určit prioritu fungování zabezpečení**.

![Security Center zabezpečeného skóre](media/security-center-intro/sc-secure-score.png)

### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>Optimalizace a vylepšení zabezpečení konfigurací doporučených ovládacích prvků

Srdcová hodnota Azure Security Center leží ve svých doporučeních. Doporučení jsou přizpůsobená konkrétním problémům zabezpečení zjištěným na vašich úlohách a Security Center zajišťuje, aby správce zabezpečení pracoval jenom s vašimi hrozbami, ale poskytoval vám konkrétní pokyny, jak je zbavit.

![Doporučení služby Security Center](media/security-center-intro/sc-recommendations.png)

Tímto způsobem Security Center umožňuje nejen nastavovat zásady zabezpečení, ale používat standardy zabezpečené konfigurace napříč prostředky.

Doporučení vám pomůžou snížit plochu pro útok na jednotlivé prostředky. To zahrnuje virtuální počítače Azure, servery mimo Azure a služby Azure PaaS, jako jsou SQL a účty úložiště, a další informace o tom, kde se každý typ prostředku vyhodnocuje odlišně a má vlastní standardy.

![Příklad doporučení Security Center](media/security-center-intro/sc-recommendation-example.png)

## <a name="protect-against-threats"></a>Ochrana před hrozbami

Ochrana před hrozbami Security Center vám umožní detekovat a bránit hrozbám na úrovni infrastruktury jako služby (IaaS), serverech mimo Azure a také pro platformy jako služby (PaaS) v Azure.

Ochrana před hrozbami Security Center zahrnuje analýzu dezaktivačního řetězu fúze, která automaticky koreluje výstrahy ve vašem prostředí založené na počítačové analýze dezaktivačního řetězu, což vám pomůže lépe pochopit úplný příběh kampaně při útoku, kde začala a co druh dopadu, který měl na vašich prostředcích.



![Doporučení Security Centerho útoku](media/security-center-intro/sc-attack-recommendation.png)

### <a name="advanced-threat-protection"></a>Rozšířená ochrana před internetovými útoky

S Security Center získáte nativní integraci pomocí rozšířené ochrany před internetovými útoky v programu Windows Defender. To znamená, že bez jakékoli konfigurace jsou virtuální počítače a servery s Windows plně integrované s doporučeními a posouzením Security Center. Pokročilá detekce hrozeb se také nabízí mimo pole pro virtuální počítače a servery Linux.

Security Center navíc umožňuje automatizovat zásady řízení aplikací v prostředích serveru. Adaptivní řízení aplikací v Security Center umožní kompletní aplikaci, která je povolená pro všechny servery s Windows. Nemusíte vytvářet pravidla a kontrolovat porušení, je to vše hotovo automaticky za vás.

### <a name="protect-paas"></a>Ochrana PaaS

Security Center vám pomůže detekovat hrozby napříč službami Azure PaaS. Můžete zjišťovat hrozby, které cílí na služby Azure, včetně Azure App Service, Azure SQL, Azure Storage účtu a dalších datových služeb. Můžete také využít výhod nativní integrace s uživateli Microsoft Cloud App Security a UEBA (User-Analytics) a provádět detekci anomálií v protokolech aktivit Azure.

### <a name="block-brute-force-attacks"></a>Zablokovat útoky hrubou silou

Security Center vám pomůže omezit vystavení útokům hrubou silou. Omezením přístupu k portům virtuálních počítačů pomocí přístupu k virtuálnímu počítači za běhu můžete posílit svou síť tím, že zabráníte zbytečnému přístupu. Na vybraných portech můžete nastavit zásady zabezpečeného přístupu, jenom pro autorizované uživatele, povolené rozsahy IP adres nebo IP adresy a omezené množství času.

![Security Center hrubou silou](media/security-center-intro/sc-brute-force.png)

### <a name="protect-data-services"></a>Ochrana datových služeb

Security Center obsahuje možnosti, které vám pomůžou provést automatickou klasifikaci vašich dat v Azure SQL. Můžete také získat vyhodnocení potenciálních chyb zabezpečení napříč službou Azure SQL a služby úložiště a doporučení, jak je zmírnit.

### <a name="protect-iot-and-hybrid-cloud-workloads"></a>Ochrana úloh IoT a hybridního cloudu

Azure Security Center for IoT (Internet věcí) zjednodušuje ochranu hybridních úloh tím, že poskytuje jednotnou viditelnost a kontrolu, preventivní prevenci a inteligentní detekci hrozeb a reakce napříč úlohami běžícími na hraničních zařízeních, v místním prostředí. Azure a v jiných cloudech. Další informace najdete v tématu [Azure Security Center pro IoT](https://docs.microsoft.com/azure/asc-for-iot/).

## <a name="get-secure-faster"></a>Rychlejší získání zabezpečení

Nativní Integrace Azure (včetně protokolů Azure Policy a Azure Monitor) v kombinaci s bezproblémovou integrací s dalšími řešeními zabezpečení Microsoftu, jako je Microsoft Cloud App Security a Rozšířená ochrana před internetovými útoky v programu Windows Defender, zajišťuje, aby řešení zabezpečení je ucelené a umožňuje snadnou registraci a zavedení.

Kromě toho můžete rozšířit úplné řešení mimo Azure na úlohy spuštěné v jiných cloudech a v místních datových centrech.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Automatické zjišťování a zprovoznění prostředků Azure

Security Center poskytuje bezproblémovou a nativní integraci s prostředky Azure a Azure. To znamená, že si můžete vyžádat kompletní bezpečnostní příběh, který zahrnuje Azure Policy a předdefinované Security Center zásady napříč všemi prostředky Azure a zajistěte, aby se celá věc automaticky používala u nově zjištěných prostředků při jejich vytváření. v Azure.

Rozsáhlá kolekce protokolů – protokoly z Windows a Linux se využívají v modulu Security Analytics a používají se k vytváření doporučení a upozornění.

## <a name="next-steps"></a>Další kroky

- Pokud chcete začít využívat Security Center, musíte mít předplatné Microsoft Azure. Pokud předplatné nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
- Cenová úroveň Free služby Security Center je povolená s vaším předplatným Azure. Pokud chcete využívat pokročilé možnosti správy zabezpečení a detekce hrozeb, musíte upgradovat na cenovou úroveň Standard. Úroveň Standard může být vyzkoušená zdarma. Další informace najdete na [stránce s cenami za Security Center](https://azure.microsoft.com/pricing/details/security-center/).
- Pokud jste připraveni povolit Security Center Standard nyní, [rychlý Start: Připojte si předplatné Azure k Security Center Standard](security-center-get-started.md) vás provede kroky.

