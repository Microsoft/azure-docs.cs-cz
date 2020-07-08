---
title: Ceny Azure Security Center úrovní
description: Azure Security Center se nabízí ve dvou úrovních – Free a Standard. Na této stránce se dozvíte, jak upgradovat z bezplatného na standard.
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
ms.date: 04/28/2020
ms.author: memildin
ms.openlocfilehash: 4487d1452611b269eec756cdbc76e8e55c466cd2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85801134"
---
# <a name="upgrade-to-standard-tier-for-enhanced-security"></a>Upgradovat na úroveň Standard pro rozšířené zabezpečení

Azure Security Center zajišťuje jednotnou správu zabezpečení a pokročilou ochranu před hrozbami pro úlohy běžící v Azure, na místních systémech a v jiných cloudech. Poskytuje přehled a kontrolu nad úlohami hybridního cloudu, aktivní ochranou, která snižuje vaše nároky na hrozby a inteligentní zjišťování, které vám pomůže udržet krok s rychle se rozvíjejícími se internetovými útoky.

## <a name="pricing-tiers"></a>Cenové úrovně
Security Center se nabízí ve dvou úrovních:

- Úroveň **Free** je povolená ve všech vašich předplatných Azure, když navštívíte Azure Security Center řídicí panel v Azure Portal poprvé nebo pokud je povolený prostřednictvím rozhraní API. Úroveň Free poskytuje zásady zabezpečení, průběžné posuzování zabezpečení a užitečná doporučení zabezpečení, která vám pomůžou chránit vaše prostředky Azure.

- Úroveň **Standard** rozšiřuje možnosti úrovně Free na úlohy spuštěné v privátních a dalších veřejných cloudech a zajišťuje jednotnou správu zabezpečení a ochranu před hrozbami napříč vašimi hybridními cloudy. Úroveň Standard také přináší možnosti ochrany před hrozbami, které využívají integrované analýzy chování a strojové učení k identifikaci útoků a využívání funkcí pro přístup a používání aplikací k omezení ohrožení síťových útoků a malwaru. Úroveň Standard navíc přináší kontrolu ohrožení zabezpečení pro vaše virtuální počítače. Úroveň Standard můžete vyzkoušet zdarma. Security Center Standard podporuje prostředky Azure, včetně virtuálních počítačů, virtuálních počítačů Scale Sets, App Service, serverů SQL a účtů úložiště. Pokud máte Azure Security Center Standard, můžete se odhlásit od podpory na základě typu prostředku. 

Většina posouzení zabezpečení na úrovni bezplatné úrovně pro virtuální počítače, stejně jako mnoho výstrah zabezpečení na úrovni Standard, vyžaduje instalaci agenta Log Analytics. Automatické zřizování na Security Center můžete povolit pro automatické nasazení agenta na virtuální počítače Azure.

## <a name="try-standard-tier-free-for-30-days"></a>Vyzkoušení úrovně Standard na 30 dnů zdarma
Úroveň Standard je po dobu prvních 30 dnů zdarma. Po uplynutí 30 dnů se můžete rozhodnout, že budete službu dál používat, a automaticky se zahájí vyúčtováním využití.

Můžete upgradovat celé předplatné Azure na úroveň Standard, která se dědí ze všech prostředků v rámci předplatného.

Postup získání úrovně Standard:

1. V hlavní nabídce **Security Center** vyberte **cenové & nastavení** .
2. Vyberte předplatné, které chcete upgradovat na úroveň Standard.
3. Vyberte **cenovou úroveň**.
4. Vyberte možnost **Standard** pro upgrade.
5. Klikněte na **Uložit**.

[![Ceny Security Center](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> Pokud chcete povolit všechny Security Center funkce včetně funkcí ochrany před hrozbami, musíte použít cenovou úroveň Standard na předplatné, které obsahuje příslušné úlohy. Konfigurace cen pro pracovní prostor nepovoluje přístup k virtuálnímu počítači za běhu, adaptivní řízení aplikací a zjišťování sítě pro prostředky Azure. 
>
> Ochranu před hrozbami pro **účty Azure Storage** můžete povolit na úrovni předplatného nebo na úrovni prostředků.
> Ochranu před hrozbami pro **Azure SQL Database SQL servery** můžete povolit buď na úrovni předplatného, nebo na úrovni prostředků.
> Ochranu před hrozbami pro **Azure Database for MariaDB/MySQL/PostgreSQL** můžete povolit jenom na úrovni prostředků.


## <a name="why-upgrade-to-standard"></a>Proč upgradovat na úroveň Standard?
Security Center nabízí vylepšené zabezpečení a ochranu před hrozbami pro úlohy hybridního cloudu, včetně:

- **Hybridní zabezpečení** – Získejte jednotný přehled o zabezpečení napříč všemi vašimi místními i Cloud úlohami. Použijte zásady zabezpečení a průběžně vyhodnoťte zabezpečení vašich hybridních cloudových úloh, abyste zajistili dodržování standardů zabezpečení. Shromažďovat, vyhledávat a analyzovat data zabezpečení z různých zdrojů, včetně bran firewall a dalších partnerských řešení.
- **Výstrahy zabezpečení** – pomocí pokročilých analýz a Microsoft Intelligent Security Graph můžete získat hraniční přístup k nově vznikajícím internetovým útokům. Využijte integrované analýzy chování a strojové učení k identifikaci útoků a využívání funkce Zero Day. Monitorujte sítě, počítače a cloudové služby pro příchozí útoky a činnost po porušení zabezpečení. Zjednodušte šetření pomocí interaktivních nástrojů a kontextové analýzy hrozeb.
- **Kontrola ohrožení zabezpečení pro virtuální počítače** – snadno nasadíte skener na všechny virtuální počítače, které poskytují špičkové řešení pro správu ohrožení zabezpečení. Zobrazit, prozkoumat a opravit zjištění přímo v rámci Security Center. 
- **Řízení přístupu a aplikací** – zablokuje malware a další nežádoucí aplikace pomocí doporučení pro používání služby Machine Learning, která jsou přizpůsobená konkrétním úlohám. Snižte úroveň útoku na síť pomocí řízeného přístupu za běhu k portům pro správu virtuálních počítačů Azure. To významně snižuje riziko útoků hrubou silou a dalších sítí.
- **Funkce zabezpečení kontejnerů** – výhody správy ohrožení zabezpečení a ochrany před hrozbami v reálném čase ve vašich kontejnerových prostředích. Při povolování prostředku registrů kontejnerů může trvat až 12hrs, než se povolí všechny funkce. Poplatky vycházejí z počtu jedinečných imagí kontejnerů, které jsou vloženy do připojeného registru. Po prohledání obrazu se tato image nebude účtovat znovu, pokud se neupraví a znovu nevloží. 




## <a name="next-steps"></a>Další kroky
V tomto článku jste zavedli ceny Security Center. Další informace o rozšířeném zabezpečení a Rozšířené ochraně před internetovými vrstvami úrovně Standard najdete v těchto tématech:

- [Ochrana před hrozbami v Azure Security Center](threat-protection.md)
- [Řízení přístupu k virtuálnímu počítači za běhu](security-center-just-in-time.md)
- [Přehled zabezpečení kontejnerů](container-security.md)
- [Podrobnosti o cenách v měně zvolené a podle vaší oblasti](https://azure.microsoft.com/pricing/details/security-center/)