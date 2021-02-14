---
title: Ceny Azure Security Center
description: Azure Security Center se nabízí ve dvou režimech s Azure Defenderem a bez něj.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2021
ms.author: memildin
ms.openlocfilehash: c12ad505777111499fb354709ce606189398458f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391917"
---
# <a name="pricing-of-azure-security-center"></a>Ceny Azure Security Center
Azure Security Center zajišťuje jednotnou správu zabezpečení a pokročilou ochranu před hrozbami pro úlohy běžící v Azure, na místních systémech a v jiných cloudech. Poskytuje přehled a kontrolu nad úlohami hybridního cloudu, aktivní ochranou, která snižuje vaše nároky na hrozby a inteligentní zjišťování, které vám pomůže udržet krok s rychle se rozvíjejícími se internetovými riziky.


## <a name="free-option-vs-azure-defender-enabled"></a>Možnost Free vs – povolený Azure Defender

Security Center se nabízí ve dvou režimech:

- **Azure Defender off** (zdarma) – Security Center bez programu Azure Defender je zdarma zapnutá na všech vašich předplatných Azure, když navštívíte Azure Security Center řídicí panel v Azure Portal poprvé nebo pokud je povolený prostřednictvím rozhraní API. Tento bezplatný režim poskytuje zásady zabezpečení, průběžné posuzování zabezpečení a užitečná doporučení zabezpečení, která vám pomůžou chránit vaše prostředky Azure.

- **Azure Defender on** -umožňující Azure Defender rozšiřuje možnosti bezplatného režimu na úlohy spuštěné v privátních a dalších veřejných cloudech a poskytuje jednotnou správu zabezpečení a ochranu před hrozbami napříč vašimi hybridními cloudy. Některé z hlavních funkcí v Azure Defenderu:

    - **Microsoft Defender pro koncové body** – Azure Defender pro servery zahrnuje [Microsoft Defender pro koncové](https://www.microsoft.com/microsoft-365/security/endpoint-defender) body pro komplexní zjišťování koncových bodů a odpověď (EDR). Přečtěte si další informace o výhodách používání programu Microsoft Defender pro koncový bod společně s Azure Defenderem v [integrovaném řešení EDR Security Center](security-center-wdatp.md).
    - **Chyba při vyhledávání virtuálních počítačů a registrů kontejnerů** – snadno nasadíte skener na všechny virtuální počítače, které poskytují špičkové řešení pro správu ohrožení zabezpečení. Zobrazit, prozkoumat a opravit zjištění přímo v rámci Security Center. 
    - **Hybridní zabezpečení** – Získejte jednotný přehled o zabezpečení napříč všemi vašimi místními i Cloud úlohami. Použijte zásady zabezpečení a průběžně vyhodnoťte zabezpečení vašich hybridních cloudových úloh, abyste zajistili dodržování standardů zabezpečení. Shromažďovat, vyhledávat a analyzovat data zabezpečení z různých zdrojů, včetně bran firewall a dalších partnerských řešení.
    - **Výstrahy ochrany před hrozbami** – Pokročilá analýza chování a Microsoft Intelligent Security Graph poskytují hraniční rozšíření pro počítačové útoky. Integrované analýzy chování a strojové učení můžou identifikovat útoky a zneužití funkce Zero-Day. Monitorujte sítě, počítače a cloudové služby pro příchozí útoky a činnost po porušení zabezpečení. Zjednodušte šetření pomocí interaktivních nástrojů a kontextové analýzy hrozeb.
    - **Přístup a ovládací prvky aplikace** (AAC) – zablokuje malware a další nežádoucí aplikace, a to pomocí doporučení pro založenou na strojovém učení přizpůsobených vašim konkrétním úlohám a vytvoření seznamů povolených a odepřených Snižte úroveň útoku na síť pomocí řízeného přístupu za běhu k portům pro správu virtuálních počítačů Azure. AAC významně omezuje expozici útokům hrubou silou a dalším síťovým útokům.
    - **Funkce zabezpečení kontejnerů** – výhody správy ohrožení zabezpečení a ochrany před hrozbami v reálném čase ve vašich kontejnerových prostředích. Když povolíte **Azure Defender pro Registry kontejnerů**, může trvat až 12 hodin, než se všechny funkce povolí. Poplatky vycházejí z počtu jedinečných imagí kontejnerů, které jsou vloženy do připojeného registru. Po kontrole obrázku jednou se vám nebude nic účtovat, dokud ho neupravíte a znovu nepřidáte.
    - Škálování **ochrany před hrozbami pro prostředky připojené k prostředí Azure** – Azure Defender zahrnuje ochranu před internetovými útoky v Azure, která je společná pro všechny vaše prostředky: Azure Resource Manager, Azure DNS, síťová vrstva azure a Azure Key Vault. Azure Defender má jedinečný přehled o vrstvě správy Azure a vrstvě Azure DNS a může proto chránit cloudové prostředky, které jsou k těmto vrstvám připojené.


## <a name="try-azure-defender-free-for-30-days"></a>Vyzkoušení Azure Defenderu zdarma po dobu 30 dnů
Azure Defender je pro prvních 30 dnů zdarma. Po uplynutí 30 dnů se můžete rozhodnout, že budete službu dál používat, a automaticky začneme účtovat za využití.

## <a name="enable-azure-defender"></a>Povolení Azure Defenderu
Můžete chránit celé předplatné Azure pomocí Azure Defenderu a všechny prostředky v rámci tohoto předplatného zdědí všechny jeho ochrany.

Povolení služby Azure Defender:

1. V hlavní nabídce Security Center vyberte **cenové & nastavení**.
1. Vyberte předplatné, které chcete upgradovat.
1. Pro upgrade vyberte **Azure Defender** .
1. Vyberte **Uložit**.

Níže najdete stránku s cenami za ukázkové předplatné. Všimnete si, že každý plán v Azure Defenderu se účtuje samostatně a dá se individuálně nastavit na hodnotu Zapnuto nebo vypnuto.

:::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="Stránka s cenami Security Center na portálu":::

> [!NOTE]
> Pokud chcete povolit všechny Security Center funkce, včetně funkcí ochrany před hrozbami, musíte v předplatném, které obsahuje příslušné úlohy, povolit Azure Defender. Povolením na úrovni pracovního prostoru nepovolíte pro prostředky Azure přístup k virtuálnímu počítači za běhu, adaptivní řízení aplikací a zjišťování sítě. K dispozici jsou navíc jenom plány Azure Defenderu na úrovni pracovního prostoru, které jsou v Azure Defenderu pro servery a Azure Defender pro SQL servery na počítačích.
>
> **Azure Defender můžete povolit pro účty úložiště** buď na úrovni předplatného, nebo na úrovni prostředků.
> **Azure Defender pro SQL** můžete povolit buď na úrovni předplatného, nebo na úrovni prostředků.
> Ochranu před hrozbami pro **Azure Database for MariaDB/MySQL/PostgreSQL** můžete povolit jenom na úrovni prostředků.


## <a name="faq---pricing-and-billing"></a>Nejčastější dotazy – ceny a fakturace 

- [Jak mohu sledovat, kdo v organizaci povolil změny v Azure Defenderu v Azure Security Center?](#how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center)
- [Jaké jsou plány, které nabízí Security Center?](#what-are-the-plans-offered-by-security-center)
- [Jak pro předplatné povolím Azure Defender?](#how-do-i-enable-azure-defender-for-my-subscription)
- [Můžu Azure Defender povolit pro podmnožinu serverů v mém předplatném?](#can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription)
- [Pokud již mám licenci pro program Microsoft Defender pro koncový bod, mohu získat slevu na Azure Defender?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Má předplatné Azure Defender pro servery povolené, mám platit za Nespuštěné servery?](#my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [Budou se mi účtovat počítače bez nainstalovaného agenta Log Analytics?](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [Pokud agent Log Analytics nahlásí do několika pracovních prostorů, bude se mi účtovat dvakrát?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [Pokud agent Log Analytics nahlásí do více pracovních prostorů, je pro všechny z nich dostupná příjem dat 500 MB.](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [Počítá se příjem dat o volném 500 MB pro celý pracovní prostor nebo výhradně pro každý počítač?](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)

### <a name="how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center"></a>Jak mohu sledovat, kdo v organizaci povolil změny v Azure Defenderu v Security Center?
K předplatným Azure může mít několik správců oprávnění ke změně nastavení cen. Chcete-li zjistit, který uživatel provedl změnu, použijte protokol aktivit Azure.

:::image type="content" source="media/security-center-pricing/logged-change-to-pricing.png" alt-text="Protokol aktivit Azure zobrazující událost změny cen":::

Pokud informace o uživateli nejsou uvedené ve sloupci **událost iniciováno** , prozkoumejte ve formátu JSON události příslušné podrobnosti.

:::image type="content" source="media/security-center-pricing/tracking-pricing-changes-in-activity-log.png" alt-text="Průzkumník JSON protokolu aktivit Azure":::


### <a name="what-are-the-plans-offered-by-security-center"></a>Jaké jsou plány, které nabízí Security Center? 
Security Center má dvě nabídky: 

- Azure Security Center Free 
- Azure Defender  

### <a name="how-do-i-enable-azure-defender-for-my-subscription"></a>Jak pro předplatné povolím Azure Defender? 
Pro povolení služby Azure Defender pro vaše předplatné můžete použít některý z následujících způsobů: 

|Metoda  |Pokyny  |
|---------|---------|
|Azure Security Center stránky Azure Portal|[Povolení Azure Defenderu](#enable-azure-defender)|
|REST API|[Rozhraní API pro ceny](/rest/api/securitycenter/pricings)|
|Azure CLI|[AZ Security Price](/cli/azure/security/pricing)|
|PowerShell|[Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)|
|Azure Policy|[Ceny sady prostředků](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json)|
|||

### <a name="can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>Můžu Azure Defender povolit pro podmnožinu serverů v mém předplatném?
No. Pokud povolíte [Azure Defender pro servery](defender-for-servers-introduction.md) v rámci předplatného, budou všechny servery v tomto předplatném chráněny pomocí Azure Defenderu. 

Alternativou je povolení Azure Defenderu pro servery na úrovni pracovního prostoru Log Analytics. Pokud to uděláte, budou se chránit a fakturovat jenom servery, které se budou hlásit do tohoto pracovního prostoru. Některé možnosti však nebudou k dispozici. Mezi ně patří přístup k virtuálnímu počítači za běhu, detekce sítě, dodržování legislativních předpisů, adaptivní posílení zabezpečení sítě, adaptivní řízení aplikací a další. 

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Pokud již mám licenci pro program Microsoft Defender pro koncový bod, mohu získat slevu na Azure Defender?
Pokud jste už získali licenci na Microsoft Defender pro koncový bod, nebudete muset platit za tuto část vaší licence na Azure Defender.

Pokud chcete potvrdit svoji slevu, obraťte se na tým podpory Security Center a poskytněte příslušnému ID pracovního prostoru relevantní informace o licenci, oblasti a licence pro jednotlivé příslušné licence.

### <a name="my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>Má předplatné Azure Defender pro servery povolené, mám platit za Nespuštěné servery? 
No. Pokud povolíte [Azure Defender pro servery](defender-for-servers-introduction.md) v rámci předplatného, nebudou se vám účtovat žádné servery, které jsou ve stavu zrušeno přidělení, když jsou v tomto stavu.

:::image type="content" source="media/security-center-pricing/deallocated-virtual-machines.png" alt-text="Azure Virtual Machines zobrazující nepřidělený počítač":::

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>Budou se mi účtovat počítače bez nainstalovaného agenta Log Analytics?
Ano. Pokud povolíte [Azure Defender pro servery](defender-for-servers-introduction.md) v rámci předplatného, počítače v tomto předplatném získají rozsah ochrany i v případě, že jste nenainstalovali agenta Log Analytics.

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Pokud agent Log Analytics nahlásí do několika pracovních prostorů, bude se mi účtovat dvakrát? 
Ano. Pokud jste agenta Log Analytics nakonfigurovali tak, aby odesílal data do dvou nebo více různých pracovních prostorů Log Analytics (více domovských), bude se vám účtovat každý pracovní prostor, který má nainstalované řešení Security nebo antimalware. 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Pokud agent Log Analytics nahlásí do více pracovních prostorů, je pro všechny z nich dostupná příjem dat 500 MB.
Ano. Pokud jste agenta Log Analytics nakonfigurovali tak, aby odesílal data do dvou nebo více různých pracovních prostorů Log Analytics (více domovských), dostanete 500 až MB volného příjmu dat. Počítá se podle počtu uzlů, podle nahlášeného pracovního prostoru za den a je k dispozici pro každý pracovní prostor s nainstalovanými řešeními zabezpečení nebo antimalwaru. Budou se vám účtovat všechna data ingestovaná přes 500 MB.

### <a name="is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine"></a>Počítá se příjem dat o volném 500 MB pro celý pracovní prostor nebo výhradně pro každý počítač?
Pro každý počítač připojený k pracovnímu prostoru získáte 500 MB bezplatného příjmu dat za den. Konkrétně pro datové typy zabezpečení přímo shromažďované nástrojem Azure Security Center.

Tento objem dat představuje denní průměr napříč všemi uzly. Takže i když některé počítače odesílají 100-MB a jiné odesílají 800-MB, pokud celková hodnota nepřekročí limit **[počet počítačů] × 500-MB** , nebude se vám účtovat žádná další.

## <a name="next-steps"></a>Další kroky
Tento článek vysvětluje Security Center cenové možnosti. Související materiály najdete v tématech:

- [Jak optimalizovat náklady na úlohy Azure](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Podrobnosti o cenách v měně zvolené a podle vaší oblasti](https://azure.microsoft.com/pricing/details/security-center/)
- Můžete chtít spravovat náklady a omezit množství shromažďovaných dat pro řešení tím, že je omezíte na konkrétní sadu agentů. [Cílení na řešení](../azure-monitor/insights/solution-targeting.md) umožňuje použít obor pro řešení a cílit na podmnožinu počítačů v pracovním prostoru. Pokud používáte cílení řešení, Security Center zobrazí seznam pracovních prostorů, které nemají řešení.