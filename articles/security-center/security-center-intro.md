---
title: Co je Azure Security Center? | Microsoft Docs
description: Tato stránka popisuje klíčové výhody Centra zabezpečení – zjištění stavu zabezpečení a jeho zlepšení pomocí pokrytí cloudových a on-prem prostředků.
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
ms.openlocfilehash: 11201d6560319dd34ec829f1fb34bd13d45011d7
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435816"
---
# <a name="what-is-azure-security-center"></a>Co je Azure Security Center?

Azure Security Center je jednotný systém správy zabezpečení infrastruktury, který posiluje stav zabezpečení vašich datových center a poskytuje pokročilou ochranu před hrozbami napříč hybridními úlohami v cloudu – ať už jsou v Azure nebo ne – i v místním prostředí.

Zajištění bezpečnosti vašich prostředků je společné úsilí mezi vaším poskytovatelem cloudu, Azure a vámi, zákazníkem. Musíte se ujistit, že vaše úlohy jsou zabezpečené při přechodu do cloudu a současně, když se přesunete do IaaS (infrastruktura jako služba), je větší odpovědnost zákazníků než v PaaS (platforma jako služba) a SaaS (software jako služba). Azure Security Center vám poskytuje nástroje potřebné k posílení vaší sítě, zabezpečení služeb a ujistěte se, že jste na vrcholu svého stavu zabezpečení.

Azure Security Center řeší tři nejnaléhavější nároky na zabezpečení:

-   **Rychle se měnící úlohy** – jedná se o silné a zároveň slabé místo cloudu. Na jedné straně mají koncoví uživatelé větší možnosti. Na straně druhé, jak zajistíte, aby stále se měnící služby, které uživatelé používají a vytvářejí, vyhovovaly standardům zabezpečení a dodržovaly osvědčené postupy zabezpečení?

-   **Stále důmyslnější útoky** – bez ohledu na to, kde své úlohy provozujete, jsou útoky stále důmyslnější. Musíte zabezpečit své úlohy ve veřejném cloudu, což jsou vlastně úlohy přístupné z internetu, které ještě více zvyšují vaši zranitelnost, pokud nedodržujete osvědčené postupy zabezpečení.

-   **Nedostatek dovedností souvisejících se zabezpečením** – počet výstrah zabezpečení a varovných systémů značně převyšuje počet správců s potřebnou praxí a zkušenostmi, kteří zajistí ochranu vašich prostředí. Udržení kroku s nejnovějšími útoky je nepřetržitý úkol, který vám neumožňuje ustrnout na místě, zatímco se oblast zabezpečení neustále proměňuje.

Kvůli ochraně před těmito hrozbami poskytuje Security Center nástroje, které umožňují:

-   **Posílení stavu zabezpečení**: Centrum zabezpečení vyhodnocuje vaše prostředí a umožňuje pochopit stav vašich prostředků a jejich zabezpečení.

-   **Ochrana před hrozbami**: Centrum zabezpečení vyhodnocuje vaše úlohy a zvyšuje doporučení pro prevenci hrozeb a výstrahy zabezpečení.

-   **Rychlejší zabezpečení**: V Centru zabezpečení se vše provádí rychlostí cloudu. Vzhledem k tomu, že je nativně integrovaná, nasazení Security Center je snadné, poskytuje vám automatické zřizování a ochranu se službami Azure.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="architecture"></a>Architektura

Vzhledem k tomu, že Security Center je nativně součástí Azure, služby PaaS v Azure – včetně Service Fabric, SQL databases a účty úložiště – jsou monitorovány a chráněny Security Center bez nutnosti nasazení.

Centrum zabezpečení navíc chrání servery a virtuální počítače jiného než Azure v cloudu nebo místně, pro servery Windows i Linux, a to instalací agenta Log Analytics. Virtuální počítače Azure se automaticky zřaží v Centru zabezpečení.

Události shromážděné od agentů a z Azure jsou korelovány v modulu analýzy zabezpečení, aby vám poskytovaly přizpůsobená doporučení (úlohy posílení zabezpečení), které byste měli dodržovat, abyste se ujistili, že vaše úlohy jsou zabezpečené a výstrahy zabezpečení. Tyto výstrahy byste měli prozkoumat co nejdříve, abyste se ujistili, že ve vašich úlohách nedochází ke škodlivým útokům.

Když povolíte Security Center, zásady zabezpečení integrované do Centra zabezpečení se projeví v Zásadách Azure jako vestavěná iniciativa v kategorii Centrum zabezpečení. Předdefinovaná iniciativa je automaticky přiřazena všem předplatným registrovaným v Centru zabezpečení (úrovně Free nebo Standard). Integrovaná iniciativa obsahuje pouze zásady auditu. Další informace o zásadách Centra zabezpečení v zásadách Azure najdete v [tématu Práce se zásadami zabezpečení](tutorial-security-policy.md).

## <a name="strengthen-security-posture"></a>Posílení stavu zabezpečení

Azure Security Center umožňuje posílit stav zabezpečení. To znamená, že vám pomůže identifikovat a provádět úlohy posílení zabezpečení doporučené jako doporučené postupy zabezpečení a implementovat je napříč počítači, datovými službami a aplikacemi. To zahrnuje správu a vynucování zásad zabezpečení a zajištění, aby vaše virtuální počítače Azure, servery než Azure a služby Azure PaaS byly kompatibilní. Security Center vám poskytuje nástroje, které potřebujete k tomu, abyste měli výhled z ptačí perspektivy na své úlohy, se soustředěnou viditelností na vašem zabezpečení sítě. 

### <a name="manage-organization-security-policy-and-compliance"></a>Správa zásad zabezpečení organizace a dodržování předpisů

Je to základní zabezpečení vědět a ujistěte se, že vaše úlohy jsou zabezpečené, a začíná s přizpůsobené zásady zabezpečení na místě. Vzhledem k tomu, že všechny zásady v Centru zabezpečení jsou postavené na ovládacích prvcích zásad Azure, získáte plný rozsah a flexibilitu **řešení zásad světové třídy**. V Centru zabezpečení můžete nastavit zásady tak, aby se spouštěla ve skupinách pro správu, napříč předplatnými a dokonce i pro celého tenanta.

![Řídicí panel Security Center](media/security-center-intro/sc-dashboard.png)

Security Center vám pomůže **identifikovat předplatná Shadow IT**. Když se podíváte na předplatná označená, že **nejsou zahrnuta** v řídicím panelu, můžete okamžitě vědět, kdy existují nově vytvořená předplatná, a ujistěte se, že se na ně vztahují vaše zásady a že jsou chráněna Centrem zabezpečení Azure.

![Řídicí panel zásad Centra zabezpečení](media/security-center-intro/sc-policy-dashboard.png)

Pokročilé funkce monitorování v Centru zabezpečení také umožňují **sledovat a spravovat dodržování předpisů a zásadsprávné řízení v průběhu času**. **Celkové dodržování předpisů** poskytuje míru, do jaké míry jsou vaše předplatná kompatibilní se zásadami přidruženými k vaší pracovní zátěži. 

![Zásady Centra zabezpečení v průběhu času](media/security-center-intro/sc-policy-time.png)

### <a name="continuous-assessments"></a>Průběžné hodnocení

Security Center průběžně zjišťuje nové prostředky, které jsou nasazovány v rámci vašich úloh, a vyhodnocuje, zda jsou nakonfigurovány podle osvědčených postupů zabezpečení, pokud ne, jsou označeny příznakem a získáte seznam doporučení s prioritou pro to, co potřebujete opravit, abyste ochránili vaše počítače.

Jedním z nejvýkonnějších nástrojů Security Center poskytuje pro průběžné sledování stavu zabezpečení vaší sítě je **mapa sítě**. Mapa umožňuje zobrazit topologii úloh, takže můžete zjistit, zda je každý uzel správně nakonfigurován. Můžete vidět, jak jsou vaše uzly připojeny, což vám pomůže blokovat nežádoucí připojení, která by mohla útočníkovi usnadnit přichůzka v síti.

![Mapa sítě Centra zabezpečení](media/security-center-intro/sc-net-map.png)

Centrum zabezpečení usnadňuje zmírnění výstrah zabezpečení o jeden krok tím, že přidává **zabezpečené skóre**. Zabezpečené skóre jsou nyní spojeny s každým doporučením, které obdržíte, aby vám pomohlo pochopit, jak důležité je každé doporučení pro celkové stav zabezpečení. To je zásadní pro to, abyste **upřednostnili práci v zabezpečení**.

![Skóre zabezpečení Centra zabezpečení](media/security-center-intro/sc-secure-score.png)

### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>Optimalizace a zlepšení zabezpečení konfigurací doporučených ovládacích prvků

Hlavní hodnota Azure Security Center spočívá v jeho doporučení. Doporučení jsou přizpůsobena konkrétním problémům se zabezpečením, které se nacházejí ve vašich úlohách, a Security Center dělá správce zabezpečení za vás, a to nejen vyhledáním vašich chyb zabezpečení, ale také poskytnutím konkrétních pokynů, jak se jich zbavit.

![Doporučení Centra zabezpečení](media/security-center-intro/sc-recommendations.png)

Tímto způsobem centrum zabezpečení umožňuje nejen nastavit zásady zabezpečení, ale použít standardy zabezpečené konfigurace napříč prostředky.

Doporučení vám pomohou snížit prostor pro útok napříč jednotlivými prostředky. To zahrnuje virtuální počítače Azure, servery než Azure a služby Azure PaaS, jako jsou účty SQL a Storage a další – kde se každý typ prostředku vyhodnocuje jinak a má své vlastní standardy.

![Příklad doporučení Centra zabezpečení](media/security-center-intro/sc-recommendation-example.png)

## <a name="protect-against-threats"></a>Ochrana před hrozbami

Ochrana zabezpečení Center umožňuje detekovat a předcházet hrozbám na vrstvě Infrastruktura jako služba (IaaS), serverů chod ů Azure i pro platformy jako služba (PaaS) v Azure.

Ochrana před hrozbami centra zabezpečení zahrnuje analýzu úhubek fusion, která automaticky koreluje výstrahy ve vašem prostředí na základě analýzy kybernetického řetězce, která vám pomůže lépe pochopit celý příběh útočné kampaně, kde začala a jaký dopad to mělo na vaše zdroje.



![Doporučení k útoku centra zabezpečení](media/security-center-intro/sc-attack-recommendation.png)

### <a name="integration-with-microsoft-defender-advanced-threat-protection"></a>Integrace s pokročilou ochranou před hrozbami v programu Microsoft Defender

Security Center zahrnuje automatickou nativní integraci s programem Microsoft Defender Advanced Threat Protection. To znamená, že bez jakékoli konfigurace jsou vaše počítače se systémem Windows a Linux plně integrovány s doporučeními a hodnoceními security centra.

Centrum zabezpečení navíc umožňuje automatizovat zásady řízení aplikací v serverových prostředích. Adaptivní ovládací prvky aplikací v Centru zabezpečení umožňují seznam povolených aplikací na serverech windows. Nemusíte vytvářet pravidla a kontrolovat porušení, to vše se provádí automaticky za vás.

### <a name="protect-paas"></a>Chraňte PaaS

Security Center pomáhá detekovat hrozby napříč službami Azure PaaS. Můžete zjistit hrozby cílené na služby Azure, včetně Azure App Service, Azure SQL, Azure Storage Account a dalších datových služeb. Můžete také využít nativní integraci s microsoft cloudovým zabezpečením zabezpečení aplikací user and entity Behavioral Analytics (UEBA) k provádění detekce anomálií v protokolech aktivit Azure.

### <a name="block-brute-force-attacks"></a>Blokovat útoky hrubou silou

Security Center pomáhá omezit vystavení útokům hrubou silou. Snížením přístupu k portům virtuálních počítačů pomocí přístupu k virtuálním počítači za chvíli můžete posílit síť tím, že zabráníte zbytečnému přístupu. Zásady zabezpečeného přístupu můžete nastavit na vybraných portech, pouze pro oprávněné uživatele, povolené rozsahy zdrojových ip adres nebo adresy IP a na omezenou dobu.

![Příměsí centra zabezpečení](media/security-center-intro/sc-brute-force.png)

### <a name="protect-data-services"></a>Ochrana datových služeb

Security Center obsahuje funkce, které vám pomohou provádět automatickou klasifikaci dat v Azure SQL. Můžete také získat hodnocení potenciálních chyb zabezpečení napříč službami Azure SQL a Storage a doporučení, jak je zmírnit.

### <a name="protect-iot-and-hybrid-cloud-workloads"></a>Ochrana úloh IoT a hybridního cloudu

Azure Security Center pro IoT (Internet věcí) zjednodušuje ochranu hybridních úloh tím, že poskytuje jednotnou viditelnost a řízení, adaptivní prevenci hrozeb a inteligentní ochranu před hrozbami a odezvu napříč úlohami běžícími na hraničních, místních, v Azure a v jiných cloudech. Další informace najdete v [tématu Azure Security Center for IoT](https://docs.microsoft.com/azure/asc-for-iot/).

## <a name="get-secure-faster"></a>Rychlejší zabezpečení

Nativní integrace Azure (včetně protokolů Azure Policy a Azure Monitor) v kombinaci s bezproblémovou integrací s dalšími řešeními zabezpečení Microsoftu, jako je Microsoft Cloud App Security a Windows Defender Advanced Threat Protection, pomáhají zajistit, aby vaše řešení zabezpečení bylo komplexní a jednoduché na připojení a zavedení.

Kromě toho můžete rozšířit úplné řešení mimo Azure na úlohy spuštěné v jiných cloudech a v místních datových centrech.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Automatické zjišťování a navšlápáme prostředky Azure

Security Center poskytuje bezproblémovou nativní integraci s prostředky Azure a Azure. To znamená, že můžete sestavit kompletní příběh zabezpečení zahrnující zásady Azure a integrované zásady Centra zabezpečení napříč všemi prostředky Azure a ujistěte se, že se celá věc automaticky použije na nově zjištěné prostředky při jejich vytváření v Azure.

Rozsáhlá kolekce protokolů – protokoly z Windows a Linuxu jsou všechny využívány v modulu analýzy zabezpečení a slouží k vytváření doporučení a výstrah.

## <a name="next-steps"></a>Další kroky

- Pokud chcete začít využívat Security Center, musíte mít předplatné Microsoft Azure. Pokud nemáte předplatné, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
- Bezplatná cenová úroveň Security Center je povolená s vaším předplatným Azure. Chcete-li využít výhod rozšířené správy zabezpečení a možností ochrany před hrozbami, je nutné upgradovat na standardní cenovou úroveň. Standardní úroveň lze vyzkoušet zdarma po dobu 30 dnů. Další informace naleznete na [stránce Scenní centrum zabezpečení](https://azure.microsoft.com/pricing/details/security-center/).
- Pokud jste připraveni povolit standard Security Center nyní, [rychlý start: Onboard předplatné Azure security center standard](security-center-get-started.md) vás provede kroky.