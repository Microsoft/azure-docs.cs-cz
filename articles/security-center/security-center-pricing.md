---
title: Upgradujte na úroveň Standard Security Center pro rozšířené zabezpečení | Microsoft Docs
description: Tento článek poskytuje informace o cenách pro Azure Security Center.
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
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 43ae34b946fada4d27c6843dbb8c3f9511aefd81
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520781"
---
# <a name="upgrade-to-security-centers-standard-tier-for-enhanced-security"></a>Upgradujte na úroveň Standard Security Center pro rozšířené zabezpečení
Azure Security Center zajišťuje jednotnou správu zabezpečení a pokročilou ochranu před hrozbami pro úlohy běžící v Azure, na místních systémech a v jiných cloudech. Poskytuje přehled a kontrolu nad úlohami hybridního cloudu, aktivní ochranou, která snižuje vaše nároky na hrozby a inteligentní zjišťování, které vám pomůže udržet krok s rychle se rozvíjejícími se internetovými útoky.

## <a name="pricing-tiers"></a>Cenové úrovně
Security Center se nabízí ve dvou úrovních:

- Úroveň **Free** je povolena ve všech předplatných Azure, jakmile navštívíte Azure Security Center řídicí panel v Azure Portal poprvé nebo pokud je povoleno prostřednictvím rozhraní API. Úroveň Free poskytuje zásady zabezpečení, průběžné posuzování zabezpečení a užitečná doporučení zabezpečení, která vám pomůžou chránit vaše prostředky Azure.
- Úroveň **Standard** rozšiřuje možnosti úrovně Free na úlohy spuštěné v privátních a dalších veřejných cloudech a zajišťuje jednotnou správu zabezpečení a ochranu před hrozbami napříč vašimi hybridními cloudy. Úroveň Standard také přináší pokročilé možnosti detekce hrozeb, které využívají integrované analýzy chování a strojové učení k identifikaci útoků a využívání funkcí pro přístup k síti a k omezení ohrožení při útokech na síť a malwaru, přístupu a řízení aplikací. aktuálnější. Úroveň Standard můžete vyzkoušet zdarma. Security Center Standard podporuje prostředky Azure, včetně virtuálních počítačů, virtuálních počítačů Scale Sets, App Service, serverů SQL a účtů úložiště. Pokud máte Azure Security Center Standard, můžete se odhlásit od podpory na základě typu prostředku. 

Většina posouzení zabezpečení na úrovni bezplatné úrovně pro virtuální počítače, stejně jako mnoho výstrah zabezpečení na úrovni Standard, vyžaduje instalaci funkce Microsoft Monitoring Agent (MMA). Automatické zřizování na Security Center můžete povolit pro automatické nasazení agenta pro virtuální počítače Azure.

## <a name="try-standard-free-for-30-days"></a>Vyzkoušejte si Standard zdarma po dobu 30 dnů
Úroveň Standard je po dobu prvních 30 dní zdarma. Po uplynutí 30 dnů se můžete rozhodnout, že budete službu dál používat, a automaticky se zahájí vyúčtováním využití.

Můžete upgradovat celé předplatné Azure na úroveň Standard, která se dědí ze všech prostředků v rámci předplatného.

Postup získání úrovně Standard:

1. V hlavní nabídce **Security Center** vyberte **cenové & nastavení** .
2. Vyberte předplatné, které chcete upgradovat na úroveň Standard.
3. Vyberte **Cenová úroveň**.
4. Vyberte možnost **Standard** pro upgrade.
5. Klikněte na **Uložit**.

(Ceny v imagi se poskytují jenom pro ilustrativní účely.) [ceny![Security Center](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> Pokud chcete povolit všechny funkce Security Center, musíte použít cenovou úroveň Standard u předplatného, které obsahuje příslušné virtuální počítače. Konfigurace cen pro pracovní prostor nepovoluje přístup k virtuálnímu počítači za běhu, adaptivní řízení aplikací a zjišťování sítě pro prostředky Azure.
>

## <a name="why-upgrade-to-standard"></a>Proč upgradovat na úroveň Standard?
Security Center nabízí vylepšené zabezpečení a ochranu před hrozbami pro úlohy hybridního cloudu, včetně:

- **Hybridní zabezpečení** – Získejte jednotný přehled o zabezpečení napříč všemi vašimi místními i Cloud úlohami. Použijte zásady zabezpečení a průběžně vyhodnoťte zabezpečení vašich hybridních cloudových úloh, abyste zajistili dodržování standardů zabezpečení. Shromažďovat, vyhledávat a analyzovat data zabezpečení z různých zdrojů, včetně bran firewall a dalších partnerských řešení.
- **Rozšířená detekce hrozeb** – použití pokročilých analýz a Microsoft Intelligent Security Graph k získání hraničních zařízení, která vyvíjejí počítačové útoky.  Využijte integrované analýzy chování a strojové učení k identifikaci útoků a zneužití nultého dne. Monitorujte v sítích, počítačích a cloudových službách příchozí útoky a aktivity po porušení zabezpečení. Zjednodušte vyšetřování pomocí interaktivních nástrojů a kontextové analýzy hrozeb.
- **Řízení přístupu a aplikací** – zablokuje malware a další nežádoucí aplikace pomocí doporučení pro používání služby Machine Learning, která jsou přizpůsobená konkrétním úlohám. Snižte úroveň útoku na síť pomocí řízeného přístupu za běhu k portům pro správu virtuálních počítačů Azure. To významně snižuje riziko útoků hrubou silou a dalších sítí.
- **Funkce zabezpečení kontejnerů** – výhody správy ohrožení zabezpečení a detekce hrozeb v reálném čase ve vašich kontejnerových prostředích. Při povolování prostředku registrů kontejnerů může trvat až 12hrs, než se povolí všechny funkce.


## <a name="next-steps"></a>Další kroky
V tomto článku jste zavedli ceny Security Center. Další informace o rozšířeném zabezpečení a Rozšířené ochraně před internetovými vrstvami úrovně Standard najdete v těchto tématech:

- [Pokročilá detekce hrozeb](security-center-threat-report.md)
- [Řízení přístupu k virtuálnímu počítači za běhu](security-center-just-in-time.md)
- [Přehled zabezpečení kontejnerů](container-security.md)
- [Podrobnosti o cenách v měně zvolené a podle vaší oblasti](https://azure.microsoft.com/pricing/details/security-center/)