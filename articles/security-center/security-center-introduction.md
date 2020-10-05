---
title: Co je Azure Security Center? | Microsoft Docs
description: Tato stránka popisuje klíčové výhody Security Center – zjišťuje stav zabezpečení a zlepšuje se s využitím cloudových a premch prostředků.
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
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 2eee46f99af247ffd0e11b262ace0a96453270d4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91323670"
---
# <a name="what-is-azure-security-center"></a>Co je Azure Security Center?

Azure Security Center je jednotný systém pro správu zabezpečení infrastruktury, který posílí stav zabezpečení vašich datových center a poskytuje rozšířenou ochranu před hrozbami napříč vašimi hybridními úlohami v cloudu – ať už jsou v Azure, nebo ne, i místně.

Zajištění bezpečnosti vašich prostředků je společné úsilí mezi vaším poskytovatelem cloudu, Azure a zákazníkem. Je nutné zajistit, aby vaše úlohy byly při přesunu do cloudu zabezpečené, a když přesunete na IaaS (infrastruktura jako služba), je k dispozici více zodpovědnosti zákazníka, než kolik jich bylo v PaaS (platforma jako služba), a SaaS (software jako služba). Azure Security Center poskytuje nástroje potřebné k posílení vaší sítě, zabezpečení služeb a zajištění, že jste na stav zabezpečení.

Azure Security Center řeší tři nejnaléhavější nároky na zabezpečení:

-   **Rychle se měnící úlohy** – jedná se o sílu i test cloudu. Na jedné straně mají koncoví uživatelé větší možnosti. Na straně druhé, jak zajistíte, aby stále se měnící služby, které uživatelé používají a vytvářejí, vyhovovaly standardům zabezpečení a dodržovaly osvědčené postupy zabezpečení?

-   **Stále důmyslnější útoky** – bez ohledu na to, kde své úlohy provozujete, jsou útoky stále důmyslnější. Musíte zabezpečit své úlohy ve veřejném cloudu, což jsou vlastně úlohy přístupné z internetu, které ještě více zvyšují vaši zranitelnost, pokud nedodržujete osvědčené postupy zabezpečení.

-   **Nedostatek dovedností souvisejících se zabezpečením** – počet výstrah zabezpečení a varovných systémů značně převyšuje počet správců s potřebnou praxí a zkušenostmi, kteří zajistí ochranu vašich prostředí. Udržení kroku s nejnovějšími útoky je nepřetržitý úkol, který vám neumožňuje ustrnout na místě, zatímco se oblast zabezpečení neustále proměňuje.

Kvůli ochraně před těmito hrozbami poskytuje Security Center nástroje, které umožňují:

-   **Posílit zabezpečení stav**: Security Center vyhodnocuje vaše prostředí a umožní vám pochopit stav vašich prostředků a to, jestli jsou zabezpečené.

-   **Ochrana před hrozbami**: Security Center posuzuje vaše úlohy a vyvolává doporučení prevence hrozeb a výstrahy zabezpečení.

-   **Rychleji se zabezpečit:** Ve službě Security Center probíhá vše rychlostí cloudu. Vzhledem k nativní integraci je nasazení služby Security Center jednoduché a poskytuje pro služby Azure automatické zřizování a ochranu.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="architecture"></a>Architektura

Vzhledem k tomu, že Security Center je nativně součástí Azure, služby PaaS v Azure – včetně Service Fabric, SQL Database, spravované instance SQL a účtů úložiště – jsou monitorovány a chráněny Security Center bez nutnosti jakéhokoli nasazení.

Kromě toho Security Center pro servery s Windows i Linux chránit i servery a virtuální počítače, které nejsou na Azure, a to tak, že na ně nainstalujete agenta Log Analytics. Virtuální počítače Azure se automaticky zřídí v Security Center.

Události shromážděné z agentů a z Azure jsou spojené s modulem Security Analytics, aby vám poskytovala přizpůsobená doporučení (posílení úloh), která vám pomůžou zajistit zabezpečení vašich úloh a výstrahy zabezpečení. Tyto výstrahy byste měli prozkoumat co nejdřív, abyste se ujistili, že se na vašich úlohách neprovádějí škodlivé útoky.

Pokud povolíte Security Center, v části Security Center kategorie se tato zásada zabezpečení Security Center zařadí do Azure Policy jako integrovaná iniciativa. Integrovaná iniciativa se automaticky přiřadí všem Security Center registrovaným předplatným (cenové úrovně Free nebo Standard). Integrovaná iniciativa obsahuje pouze zásady auditu. Další informace o zásadách Security Center v Azure Policy najdete v tématu [práce se zásadami zabezpečení](tutorial-security-policy.md).

## <a name="strengthen-security-posture"></a>Posílení stavu zabezpečení

Azure Security Center vám umožní posílit stav zabezpečení. To znamená, že vám pomůže identifikovat a provádět úlohy posílení zabezpečení doporučené jako osvědčené postupy zabezpečení a implementovat je napříč vašimi počítači, datovými službami a aplikacemi. To zahrnuje správu a vynucování zásad zabezpečení a zajištění, že vaše virtuální počítače Azure, servery mimo Azure a služby Azure PaaS splňují předpisy. Security Center poskytuje nástroje, které potřebujete k zobrazení očí na vašich úlohách, s soustředěnou viditelností na majetku zabezpečení vaší sítě. 

### <a name="manage-organization-security-policy-and-compliance"></a>Správa zásad zabezpečení a dodržování předpisů v organizaci

Je to základní zabezpečení, které se dozvíte a zajistěte, aby byly vaše úlohy zabezpečené a že se spustí s přizpůsobenými zásadami zabezpečení. Vzhledem k tomu, že všechny zásady v Security Center jsou postaveny nad Azure Policy ovládací prvky, získáte celou škálu a flexibilitu **špičkového řešení zásad**. V Security Center můžete nastavit, aby se zásady spouštěly ve skupinách pro správu, mezi předplatnými a dokonce i pro celého tenanta.

:::image type="content" source="./media/security-center-intro/sc-dashboard.png" alt-text="Stránka správy zásad":::

Security Center vám pomůže **identifikovat Stínová předplatná**. Zobrazením předplatných, která **nejsou pokrytá** na řídicím panelu, můžete okamžitě zjistit, jestli existují nově vytvořená předplatná, a že jsou pokryté vašimi zásadami a chráněny pomocí Azure Security Center.

:::image type="content" source="./media/security-center-intro/sc-policy-dashboard.png" alt-text="Stránka správy zásad":::

### <a name="continuous-assessments"></a>Nepřetržité hodnocení

Security Center nepřetržitě zjišťuje nové prostředky, které jsou nasazené napříč vašimi úlohami, a posuzuje, jestli jsou nakonfigurované v souladu s osvědčenými postupy zabezpečení, pokud ne, jsou označené jako prioritní a vy získáte seznam doporučení, která je potřeba opravit, aby bylo možné počítače chránit.

Abychom vám pomohli pochopit, jak důležité je, aby každé doporučení bylo stav zabezpečení, Security Center seskupení doporučení do řízení zabezpečení a přidá do každého ovládacího prvku **zabezpečenou hodnotu skóre** . To je zásadní postup, který vám umožní **určit prioritu fungování zabezpečení**.

:::image type="content" source="./media/security-center-intro/sc-secure-score.png" alt-text="Stránka správy zásad":::

### <a name="network-map"></a>Mapa sítě

Jedním z nejúčinnějších nástrojů, které Security Center nabízí nepřetržité monitorování stavu zabezpečení vaší sítě, je **Mapa sítě**. Mapa vám umožní zobrazit topologii vašich úloh, abyste viděli, jestli je každý uzel správně nakonfigurovaný. Vidíte, jak jsou uzly připojené, což vám pomůže zablokovat nechtěné připojení, která by mohla útočníkovi usnadnit nárůst vaší sítě.

:::image type="content" source="./media/security-center-intro/sc-net-map.png" alt-text="Stránka správy zásad":::


### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>Optimalizace a vylepšení zabezpečení konfigurací doporučených ovládacích prvků

Srdcová hodnota Azure Security Center leží ve svých doporučeních. Doporučení jsou přizpůsobená konkrétním problémům zabezpečení zjištěným na vašich úlohách a Security Center zajišťuje, aby správce zabezpečení pracoval jenom s vašimi hrozbami, ale poskytoval vám konkrétní pokyny, jak je zbavit.

Tímto způsobem Security Center umožňuje nejen nastavovat zásady zabezpečení, ale používat standardy zabezpečené konfigurace napříč prostředky.

Doporučení vám pomůžou snížit plochu pro útok na jednotlivé prostředky. To zahrnuje virtuální počítače Azure, servery mimo Azure a služby Azure PaaS, jako jsou SQL a účty úložiště, a další informace o tom, kde se každý typ prostředku vyhodnocuje odlišně a má vlastní standardy.

:::image type="content" source="./media/security-center-intro/sc-recommendation-example.png" alt-text="Stránka správy zásad":::

## <a name="protect-against-threats"></a>Ochrana před hrozbami

Ochrana před hrozbami Security Center vám umožní detekovat a bránit hrozbám na úrovni infrastruktury jako služby (IaaS), serverech mimo Azure a také pro platformy jako služby (PaaS) v Azure.

Ochrana před hrozbami Security Center zahrnuje analýzu dezaktivačního řetězu fúze, která automaticky koreluje výstrahy ve vašem prostředí založené na počítačové analýze dezaktivačního řetězu, které vám pomůžou lépe pochopit úplný příběh k útokům na útoky, kde začaly a jaký má dopad na vaše prostředky.

:::image type="content" source="./media/security-center-intro/sc-alerts.png" alt-text="Stránka správy zásad":::

### <a name="integration-with-microsoft-defender-advanced-threat-protection"></a>Integrace pomocí rozšířené ochrany před internetovými útoky v programu Microsoft Defender

Security Center zahrnuje automatickou nativní integraci s rozšířenou ochranou před internetovými útoky v programu Microsoft Defender. To znamená, že bez jakékoli konfigurace jsou počítače s Windows a Linux plně integrované s doporučeními a posouzením Security Center.

Security Center navíc umožňuje automatizovat zásady řízení aplikací v prostředích serveru. Adaptivní řízení aplikací v Security Center umožňuje, aby se v rámci serverů Windows nastalo úplný seznam schválení aplikací. Nemusíte vytvářet pravidla a kontrolovat porušení, je to vše hotovo automaticky za vás.

### <a name="protect-paas"></a>Ochrana PaaS

Security Center vám pomůže detekovat hrozby napříč službami Azure PaaS. Můžete zjišťovat hrozby, které cílí na služby Azure, včetně Azure App Service, Azure SQL, Azure Storage účtu a dalších datových služeb. Můžete také využít výhod nativní integrace s uživateli Microsoft Cloud App Security a UEBA (User-Analytics) a provádět detekci anomálií v protokolech aktivit Azure.

### <a name="block-brute-force-attacks"></a>Zablokovat útoky hrubou silou

Security Center vám pomůže omezit vystavení útokům hrubou silou. Omezením přístupu k portům virtuálních počítačů pomocí přístupu k virtuálnímu počítači za běhu můžete posílit svou síť tím, že zabráníte zbytečnému přístupu. Na vybraných portech můžete nastavit zásady zabezpečeného přístupu, jenom pro autorizované uživatele, povolené rozsahy IP adres nebo IP adresy a omezené množství času.

### <a name="protect-data-services"></a>Ochrana datových služeb

Security Center obsahuje možnosti, které vám pomůžou provést automatickou klasifikaci vašich dat v Azure SQL. Můžete také získat vyhodnocení potenciálních chyb zabezpečení napříč službou Azure SQL a služby úložiště a doporučení, jak je zmírnit.

### <a name="protect-iot-and-hybrid-cloud-workloads"></a>Ochrana úloh IoT a hybridního cloudu

Defender for IoT (Internet věcí) zjednodušuje ochranu hybridního pracovního vytížení tím, že nabízí jednotnou viditelnost a kontrolu, adaptivní prevenci hrozeb a inteligentní ochranu před hrozbami a reakci napříč úlohami běžícími na hraničních, místních i v Azure a v jiných cloudech. Další informace najdete v tématu [Defender for IoT](../defender-for-iot/index.yml).

## <a name="get-secure-faster"></a>Rychlejší získání zabezpečení

Nativní Integrace Azure (včetně protokolů Azure Policy a Azure Monitor) v kombinaci s bezproblémovou integrací s dalšími řešeními zabezpečení Microsoftu, jako je třeba Microsoft Cloud App Security a Rozšířená ochrana před internetovými útoky v programu Windows Defender, se ujistěte, že je vaše řešení zabezpečení komplexní a je snadno zaregistrované a zaveďte.

Kromě toho můžete rozšířit úplné řešení mimo Azure na úlohy spuštěné v jiných cloudech a v místních datových centrech.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Automatické zjišťování a zprovoznění prostředků Azure

Security Center poskytuje bezproblémovou a nativní integraci s prostředky Azure a Azure. To znamená, že můžete si projít kompletní příběh zabezpečení, který zahrnuje Azure Policy a předdefinované Security Center zásady napříč všemi prostředky Azure a zajistěte, aby se celá věc automaticky používala u nově zjištěných prostředků při jejich vytváření v Azure.

Rozsáhlá kolekce protokolů – protokoly z Windows a Linux se využívají v modulu Security Analytics a používají se k vytváření doporučení a upozornění.

## <a name="next-steps"></a>Další kroky

- Pokud chcete začít využívat Security Center, musíte mít předplatné Microsoft Azure. Pokud nemáte předplatné, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/).

- Cenová úroveň Free Security Center je povolená ve všech vašich současných předplatných Azure, když navštívíte Azure Security Center řídicí panel v Azure Portal poprvé nebo pokud je povolený prostřednictvím rozhraní API. Aby bylo možné využít pokročilé možnosti správy zabezpečení a detekce hrozeb, je nutné upgradovat na cenovou úroveň Standard. Úroveň Standard se dá zdarma zkoušet po dobu 30 dnů. Další informace najdete na [stránce s cenami za Security Center](https://azure.microsoft.com/pricing/details/security-center/).

- Pokud jste připraveni povolit Security Center Standard nyní, [rychlý Start: připojení předplatného Azure na Security Center Standard](security-center-get-started.md) vás provede jednotlivými kroky.