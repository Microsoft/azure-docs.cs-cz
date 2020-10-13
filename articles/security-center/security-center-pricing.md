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
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: a01d4137217aa594d4636f3338d3f33dc03cc836
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91713746"
---
# <a name="pricing-of-azure-security-center"></a>Ceny Azure Security Center
Azure Security Center zajišťuje jednotnou správu zabezpečení a pokročilou ochranu před hrozbami pro úlohy běžící v Azure, na místních systémech a v jiných cloudech. Poskytuje přehled a kontrolu nad úlohami hybridního cloudu, aktivní ochranou, která snižuje vaše nároky na hrozby a inteligentní zjišťování, které vám pomůže udržet krok s rychle se rozvíjejícími se internetovými útoky.


## <a name="free-option-vs-azure-defender-enabled"></a>Možnost Free vs – povolený Azure Defender

Security Center se nabízí ve dvou režimech:

- **Azure Defender off** (zdarma) – Security Center bez programu Azure Defender je zdarma zapnutá na všech vašich předplatných Azure, když navštívíte Azure Security Center řídicí panel v Azure Portal poprvé nebo pokud je povolený prostřednictvím rozhraní API. Tento bezplatný režim poskytuje zásady zabezpečení, průběžné posuzování zabezpečení a užitečná doporučení zabezpečení, která vám pomůžou chránit vaše prostředky Azure.

- **Azure Defender on** -umožňující Azure Defender rozšiřuje možnosti bezplatného režimu na úlohy spuštěné v privátních a dalších veřejných cloudech a poskytuje jednotnou správu zabezpečení a ochranu před hrozbami napříč vašimi hybridními cloudy. Některé z hlavních funkcí v Azure Defenderu:

    - **Hybridní zabezpečení** – Získejte jednotný přehled o zabezpečení napříč všemi vašimi místními i Cloud úlohami. Použijte zásady zabezpečení a průběžně vyhodnoťte zabezpečení vašich hybridních cloudových úloh, abyste zajistili dodržování standardů zabezpečení. Shromažďovat, vyhledávat a analyzovat data zabezpečení z různých zdrojů, včetně bran firewall a dalších partnerských řešení.
    - **Výstrahy ochrany před hrozbami** – Pokročilá analýza chování a Microsoft Intelligent Security Graph poskytují hraniční rozšíření pro počítačové útoky. Využijte integrované analýzy chování a strojové učení k identifikaci útoků a využívání funkce Zero Day. Monitorujte sítě, počítače a cloudové služby pro příchozí útoky a činnost po porušení zabezpečení. Zjednodušte šetření pomocí interaktivních nástrojů a kontextové analýzy hrozeb.
    - **Chyba při vyhledávání virtuálních počítačů a registrů kontejnerů** – snadno nasadíte skener na všechny virtuální počítače, které poskytují špičkové řešení pro správu ohrožení zabezpečení. Zobrazit, prozkoumat a opravit zjištění přímo v rámci Security Center. 
    - **Řízení přístupu a aplikací** – zablokuje malware a další nežádoucí aplikace, a to pomocí doporučení pro založenou na strojovém učení přizpůsobených vašim konkrétním úlohám a vytváření seznamů povolených a zakázaných. Snižte úroveň útoku na síť pomocí řízeného přístupu za běhu k portům pro správu virtuálních počítačů Azure. To významně snižuje riziko útoků hrubou silou a dalších sítí.
    - **Funkce zabezpečení kontejnerů** – výhody správy ohrožení zabezpečení a ochrany před hrozbami v reálném čase ve vašich kontejnerových prostředích. Když povolíte **Azure Defender pro Registry kontejnerů**, může to trvat až 12hrs, než se všechny funkce povolí. Poplatky vycházejí z počtu jedinečných imagí kontejnerů, které jsou vloženy do připojeného registru. Po kontrole obrázku jednou se vám nebude nic účtovat, dokud ho neupravíte a znovu nepřidáte. 

## <a name="try-azure-defender-free-for-30-days"></a>Vyzkoušení Azure Defenderu zdarma po dobu 30 dnů

Azure Defender je pro prvních 30 dnů zdarma. Po uplynutí 30 dnů se můžete rozhodnout, že budete službu dál používat, a automaticky začneme účtovat za využití.

## <a name="enable-azure-defender"></a>Povolit Azure Defender

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


## <a name="next-steps"></a>Další kroky
V tomto článku jste zavedli ceny Security Center. Související materiál najdete v těchto tématech:

- [Jak optimalizovat náklady na úlohy Azure](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Podrobnosti o cenách v měně zvolené a podle vaší oblasti](https://azure.microsoft.com/pricing/details/security-center/)
- Můžete chtít spravovat náklady a omezit množství shromažďovaných dat pro řešení tím, že je omezíte na konkrétní sadu agentů. [Cílení na řešení](../operations-management-suite/operations-management-suite-solution-targeting.md) umožňuje použít obor pro řešení a cílit na podmnožinu počítačů v pracovním prostoru. Pokud používáte cílení řešení, Security Center zobrazí seznam pracovních prostorů, které nemají řešení.