---
title: Ceny úrovní Azure Security Center
description: Azure Security Center se nabízí ve dvou úrovních – free a standard. Tato stránka ukazuje, jak upgradovat z volného na standardní.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2019
ms.author: memildin
ms.openlocfilehash: fd84058c8421d144678c91fac3e5671511d0fd4a
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435505"
---
# <a name="upgrade-to-standard-tier-for-enhanced-security"></a>Upgrade na úroveň Standard pro lepší zabezpečení
Azure Security Center zajišťuje jednotnou správu zabezpečení a pokročilou ochranu před hrozbami pro úlohy běžící v Azure, na místních systémech a v jiných cloudech. Poskytuje viditelnost a kontrolu nad hybridními cloudovými úlohami, aktivní obranu, která snižuje vaši expozici hrozebm, a inteligentní detekci, která vám pomůže udržet krok s rychle se vyvíjejícími kybernetickými útoky.

## <a name="pricing-tiers"></a>Cenové úrovně
Security Center se nabízí ve dvou úrovních:

- Úroveň **Free** je povolená ve všech vašich předplatných Azure, jakmile poprvé navštívíte řídicí panel Azure Security Center na webu Azure Portal nebo pokud je programově povolena prostřednictvím rozhraní API. Bezplatná vrstva poskytuje zásady zabezpečení, průběžné hodnocení zabezpečení a doporučení k zabezpečení, která vám pomohou chránit vaše prostředky Azure.
- Úroveň **Standard** rozšiřuje možnosti úrovně Free na úlohy spuštěné v privátních a jiných veřejných cloudech a poskytuje jednotnou správu zabezpečení a ochranu před hrozbami napříč hybridními cloudovými úlohami. Standardní úroveň také přidává funkce ochrany před hrozbami, které používají vestavěnou analýzu chování a strojové učení k identifikaci útoků a zero-day exploitů, řízení přístupu a aplikací, aby se snížila expozice síťovým útokům a malwaru a další. Kromě toho standardní úroveň přidává prohledávání zranitelnosti pro vaše virtuální počítače. Můžete vyzkoušet standardní úroveň zdarma. Standard Security Center podporuje prostředky Azure, včetně virtuálních počítačů, škálovacích sad virtuálních počítačů, služby App Service, serverů SQL a účtů úložiště. Pokud máte standard Azure Security Center, můžete se odhlásit z podpory na základě typu prostředků. 

Většina bezplatných hodnocení zabezpečení úrovně pro virtuální počítače a také mnoho výstrah zabezpečení standardní úrovně vyžaduje instalaci funkce agenta Log Analytics. Můžete povolit automatické zřizování v Centru zabezpečení k automatickému nasazení agenta pro vaše virtuální počítače Azure.

## <a name="try-standard-tier-free-for-30-days"></a>Vyzkoušejte standardní úroveň zdarma po dobu 30 dnů
Standardní úroveň je zdarma po dobu prvních 30 dnů. Na konci 30 dnů, pokud se rozhodnete pokračovat v používání služby, automaticky začneme účtovat za použití.

Celé předplatné Azure můžete upgradovat na standardní úroveň, která je zděděna všemi prostředky v rámci předplatného.

Chcete-li získat standardní úroveň:

1. V hlavní nabídce **Centra zabezpečení** vyberte nastavení cenové **&.**
2. Vyberte předplatné, které chcete upgradovat na standardní.
3. Vyberte **Cenová úroveň**.
4. Vyberte **Standardní** pro upgrade.
5. Klikněte na **Uložit**.

[![Ceny centra zabezpečení](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> Chcete-li povolit všechny funkce Security Center, musíte použít standardní cenovou úroveň pro předplatné obsahující příslušné virtuální počítače. Konfigurace cen pro pracovní prostor neumožňuje přístup k virtuálním počítačům just-in-time, adaptivní ovládací prvky aplikací a detekci sítě pro prostředky Azure.
>

## <a name="why-upgrade-to-standard"></a>Proč upgradovat na standard?
Security Center nabízí vylepšené zabezpečení a ochranu před hrozbami pro hybridní cloudové úlohy, včetně:

- **Hybridní zabezpečení** – Získejte jednotný přehled o zabezpečení napříč všemi místními a cloudovými úlohami. Používejte zásady zabezpečení a průběžně vyhodnocujte zabezpečení hybridních cloudových úloh, abyste zajistili dodržování bezpečnostních standardů. Shromažďujte, vyhledávejte a analyzujte data zabezpečení z různých zdrojů, včetně bran firewall a dalších partnerských řešení.
- **Výstrahy zabezpečení** – Pomocí pokročilých analýz a microsoftintelligent security graph získáte výhodu nad vyvíjejícími se kybernetickými útoky. Využijte integrovanou analýzu chování a strojové učení k identifikaci útoků a zero-day exploitů. Monitorujte sítě, počítače a cloudové služby pro příchozí útoky a aktivity po porušení zabezpečení. Zjednodušte vyšetřování pomocí interaktivních nástrojů a kontextové analýzy hrozeb.
- **Vyhledávání chyb zabezpečení pro virtuální počítače** – snadno nasaďte skener do všech virtuálních počítačů, který poskytuje nejpokročilejší řešení pro správu zranitelnosti v oboru. Zobrazení, prozkoumání a náprava zjištění přímo v centru zabezpečení. 
- **Ovládání přístupu a aplikací** – Zablokujte malware a další nežádoucí aplikace použitím doporučení pro whitelisting s technologií strojového učení přizpůsobená vašim konkrétním úlohám. Snižte povrch síťového útoku pomocí řízeného přístupu k portům pro správu na virtuálních počítačích Azure just-in-time. To drasticky snižuje expozici hrubou silou a dalším síťovým útokům.
- **Funkce zabezpečení kontejnerů** – využijte výhod správy zranitelnosti a ochrany před hrozbami v reálném čase ve vašich kontejnerizovaných prostředích. Při povolení prostředků registrů kontejnerů může trvat až 12 hodin, dokud nebudou povoleny všechny funkce.


## <a name="next-steps"></a>Další kroky
V tomto článku jste byli představeni ceny pro Security Center. Další informace o vylepšeném zabezpečení úrovně Standard a pokročilé ochraně před hrozbami najdete v následujících tématech:

- [Ochrana před hrozbami v Azure Security Center](threat-protection.md)
- [Řízení přístupu k virtuálním virtuálním montovna za chvíli](security-center-just-in-time.md)
- [Přehled zabezpečení kontejnerů](container-security.md)
- [Podrobnosti o cenách ve zvolené měně a podle vašeho regionu](https://azure.microsoft.com/pricing/details/security-center/)