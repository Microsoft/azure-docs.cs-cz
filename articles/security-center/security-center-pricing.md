---
title: Povolený Azure Security Center zdarma vs Azure Defender
description: Přečtěte si o výhodách povolení ochrany cloudových úloh v Azure Defenderu v Azure Security Center
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/08/2021
ms.openlocfilehash: d45dae8b0b3725555bd83a05032339671a9595be
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454360"
---
# <a name="azure-security-center-free-vs-azure-defender-enabled"></a>Povolený Azure Security Center zdarma vs Azure Defender
Azure Defender je pro prvních 30 dnů zdarma. Po uplynutí 30 dnů se můžete rozhodnout, že budete službu dál používat, a automaticky začneme účtovat za využití.

Můžete upgradovat ze stránky **Nastavení cenové &** , jak je popsáno v tématu [rychlý Start: povolení programu Azure Defender](enable-azure-defender.md). Podrobnosti o cenách v měně zvolené a podle vaší oblasti najdete v tématu [Security Center ceny](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="what-are-the-benefits-of-enabling-azure-defender"></a>Jaké jsou výhody povolení programu Azure Defender?

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


## <a name="faq---pricing-and-billing"></a>Nejčastější dotazy – ceny a fakturace 

- [Jak mohu sledovat, kdo v organizaci povolil změny v Azure Defenderu v Security Center?](#how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center)
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

| Metoda                                          | Pokyny                                                                                                                                       |
|-------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Security Center stránky Azure Portal | [Povolení Azure Defenderu](enable-azure-defender.md)                                                                                                  |
| REST API                                        | [Rozhraní API pro ceny](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI                                       | [AZ Security Price](/cli/azure/security/pricing)                                                                                                 |
| PowerShell                                      | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy                                    | [Ceny sady prostředků](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|                                                 |                                                                                                                                                    |

### <a name="can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>Můžu Azure Defender povolit pro podmnožinu serverů v mém předplatném?
No. Pokud povolíte [Azure Defender pro servery](defender-for-servers-introduction.md) v rámci předplatného, budou všechny servery v tomto předplatném chráněny pomocí Azure Defenderu. 

Alternativou je povolení Azure Defenderu pro servery na úrovni pracovního prostoru Log Analytics. Pokud to uděláte, budou se chránit a fakturovat jenom servery, které se budou hlásit do tohoto pracovního prostoru. Některé možnosti však nebudou k dispozici. Mezi ně patří přístup k virtuálnímu počítači za běhu, detekce sítě, dodržování legislativních předpisů, adaptivní posílení zabezpečení sítě, adaptivní řízení aplikací a další. 

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Pokud již mám licenci pro program Microsoft Defender pro koncový bod, mohu získat slevu na Azure Defender?
Pokud jste už získali licenci na Microsoft Defender pro koncový bod, nebudete muset platit za tuto část vaší licence na Azure Defender.

Pokud chcete potvrdit svoji slevu, obraťte se na tým podpory Security Center a poskytněte příslušnému ID pracovního prostoru relevantní informace o licenci, oblasti a licence pro jednotlivé příslušné licence.

### <a name="my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>Má předplatné Azure Defender pro servery povolené, mám platit za Nespuštěné servery? 
No. Pokud povolíte [Azure Defender pro servery](defender-for-servers-introduction.md) v rámci předplatného, nebudou se vám účtovat žádné počítače, které jsou v nepřiděleném stavu napájení, zatímco jsou v tomto stavu. Počítače se účtují podle jejich stavu napájení, jak je znázorněno v následující tabulce:

| State        | Popis                                                                                                                                      | Využití instance se účtuje |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Spouštění     | Spouští se virtuální počítač.                                                                                                                               | Neúčtuje se            |
| Spuštěno      | Normální pracovní stav pro virtuální počítač                                                                                                                    | Účtuje                |
| Zastavování     | Toto je přechodný stav. Po dokončení se zobrazí jako zastaveno.                                                                           | Účtuje                |
| Zastaveno      | Virtuální počítač se vypnul v hostovaném operačním systému nebo pomocí rozhraní stavu PowerOff API. Hardware je stále přidělen k virtuálnímu počítači a zůstane na hostiteli. | Účtuje                |
| Rušení přidělení | Přechodný stav. Po dokončení se virtuální počítač zobrazí jako navrácené.                                                                             | Neúčtuje se            |
| Přidělení zrušeno  | Virtuální počítač se úspěšně zastavil a odebral z hostitele.                                                                                  | Neúčtuje se            |

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