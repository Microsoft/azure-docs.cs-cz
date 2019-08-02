---
title: Poskytněte zkoušku konceptu – Azure DevTest Labs | Microsoft Docs
description: Naučte se, jak doručovat koncept, aby se Azure DevTest Labs mohl úspěšně začlenit do podnikového prostředí.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: takamath
ms.openlocfilehash: 6739679b1687393737dda1ded4265a95c4fce169
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501543"
---
# <a name="delivering-a-proof-of-concept"></a>Doručení zkušebního konceptu 

Jedním z klíčových scénářů pro Azure DevTest Labs je povolení prostředí pro vývoj a testování v cloudu. Příklady obsahují:

* vytváření desktopových ploch pro vývojáře v cloudu
* Konfigurace prostředí pro testování,
* povolení přístupu k virtuálním počítačům a jinému prostředku Azure,
* nastavením oblasti izolovaného prostoru pro vývojáře se naučíte a Experimentujte.

DevTest Labs taky poskytuje zásady guardrails a cost Controls, které organizacím umožňují poskytovat svým vývojářům "samoobslužné služby Azure" pro vývojáře, kteří dodržují zásady zabezpečení, regulativní předpisy a dodržování předpisů v podniku. 

Každý podnik má různé požadavky na způsob, jakým je možné Azure DevTest Labs úspěšně začlenit do svého prostředí. Tento článek popisuje nejběžnější kroky, které se obvykle musí dokončit, aby bylo zajištěno úspěšné ověření konceptu, což je první krok k úspěšnému dokončení nasazení. 

## <a name="getting-started"></a>Začínáme 

Abyste mohli začít dodávat zkoušku konceptu. Je důležité věnovat nějakou dobu více informací o Azure a DevTest Labs.  Tady je několik počátečních prostředků: 

* [Princip Azure Portal](https://azure.microsoft.com/features/azure-portal/)
* [Základy DevTest Labs](devtest-lab-overview.md)
* [Scénáře podporované DevTest Labs](devtest-lab-guidance-get-started.md)
* [Dokumentace k DevTest Labs Enterprise](devtest-lab-guidance-prescriptive-adoption.md)
* [Úvod do sítě Azure](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Požadavky 

K úspěšnému dokončení pilotního nebo zkušebního konceptu pomocí DevTest Labs je potřeba provést několik nezbytných součástí: 

* **Předplatné Azure**: Podniky mají často existující [smlouva Enterprise](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) , které umožňují přístup k Azure a stávajícímu nebo novému předplatnému, které je možné použít pro DevTest Labs. Alternativně můžete [Visual Studio Subscription](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) použít během pilotního nasazení (využití bezplatného kreditu Azure). Pokud není k dispozici žádná z těchto možností, je možné vytvořit a použít [bezplatný účet Azure](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ) . Pokud je smlouva Enterprise na místě, použití předplatného [Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/) je skvělý způsob, jak získat přístup k Windows 10/Windows 8.1 klientským operačním systémům a zlevněné sazby pro úlohy vývoje a testování. 
* **Tenant Azure Active Directory**:  Pokud chcete povolit správu uživatelů (například přidávání uživatelů nebo přidávání vlastníků testovacího prostředí), musí být uživatelé součástí [klienta Azure Active Directory](https://azure.microsoft.com/services/active-directory/) , který se používá v předplatném Azure pro pilotní nasazení. Podniky budou často nastavovat [hybridní identitu](../active-directory/hybrid/whatis-hybrid-identity.md) , aby mohli uživatelé využívat své místní identity v cloudu, ale nevyžadují se pro pilotní prostředí DevTest Labs. 

## <a name="scoping-of-the-pilot"></a>Rozsah pilotního nasazení 

Před zahájením implementace je velmi důležité naplánovat správný pilotní projekt. Předem zajistěte, aby prostředky nezůstaly po neomezenou dobu, a pro uživatele pilotního projektu nastaví odpovídající očekávání. 

> [!IMPORTANT]
> Nemůžeme zdůraznit důležitost, která by měla výrazným způsobem začínat na pilotní nasazení a nastavení očekávat od začátku.

Před zahájením pilotního nasazení je nutné zodpovědět klíčové otázky: 

* K čemu se chcete dozvědět a co se pro pilotní nasazení zdá, co je úspěšné? 
* Jaké úlohy nebo scénáře budou zahrnuty do pilotního projektu? Je důležité definovat jenom malou sadu, aby bylo zajištěno, že pilotní modul může být v oboru a dokončený rychleji. 
* Jaké prostředky musí být k dispozici v testovacím prostředí? Například: vlastní image, image Marketplace, zásady, topologie sítě atd. 
* Kteří koncoví uživatelé/týmy, kteří budou zapojeni do pilotního projektu za účelem ověření prostředí?  
* Jaká je doba trvání pilotního projektu? Vyberte časový rámec, který bude správně zarovnán k plánovanému oboru, například dva týdny nebo jeden měsíc. 
* Co se stane s přidělenými prostředky, které se používaly v rámci pilotního projektu, po dokončení pilotního nasazení? Plánujete zrušit pilotní prostředky?
   
   Pokud můžeme naplánovat vystavování virtuálních počítačů a cvičení na konci pilotního projektu, můžeme jednoduše nastavit jedno předplatné pro pilotní nasazení a udělat veškerou práci tam, kde máme paralelní řešení otázek při povýšení možností škálování. 

Je všeobecně větší očekávání, aby pilotní projekt "perfektní" představoval stejně tak, jak bude konečný stav po zavedení služby ve společnosti. Toto je nepravdivý předpoklad. Bližší informace o tom, co se dostanete k "dokonalému", budete muset ještě *před* zahájením pilotního nasazení dokončit. Účelem pilotního nasazení je učinit správná rozhodnutí o škálování a zavedení finální služby. 

Cílem pilotního projektu by měl být vybírání absolutních potřebných úloh a závislostí, aby bylo možné odpovědět na otázku, jestli je Azure DevTest Labs správnou službou pro váš podnik. Doporučuje se zvolit nejjednodušší úlohu s minimálními závislostmi, aby se zajistila rychlejší a čistá úspěšnost. Pokud to není možné, další nejlepší možností je vybrat si nejvíc reprezentativní úlohy, která zveřejňuje potenciální složitost, aby bylo možné úspěch ve fázi pilotního nasazení replikovat do fáze škálování na více instancí. 

Následující příklad ukazuje dobře vymezený důkaz konceptu. 

## <a name="example-proof-of-concept-plan"></a>Příklad: zkušební plán konceptu 

Tato část obsahuje ukázku, která se používá k určení konceptu pilotního projektu pro DevTest Labs. 

> [!TIP]
> Aby se minimalizovala možnost nastavení projektu pro selhání, důrazně doporučujeme, abyste tento příklad popsaný v této části nepřeskočili. 

### <a name="overview"></a>Přehled 

Plánujeme vývoj nového prostředí v Azure založeného na DevTest Labs, aby se dodavatelé mohli použít jako izolované prostředí z podnikové sítě. Abychom zjistili, jestli řešení splňuje požadavky, vyvineme zkoušku konceptu, který ověří kompletní řešení a nabídne několik dodavatelů k vyzkoušení a ověření prostředí. 

### <a name="outcomes"></a>Výsledek 

Při sestavování konceptu konceptu se nejprve zaměřte na výsledky (co se snaží dosáhnout) – ty jsou uvedeny zde.  Na konci zkušebního konceptu očekáváme: 

* Pracovní koncové řešení pro dodavatele, které umožňuje využívat účty hostů v Azure Active Directory (Azure AD) pro přístup k izolovanému prostředí v Azure, které má prostředky potřebné k zajištění produktivity. 
* Jakékoli potenciální blokující problémy, které mají vliv na širší použití škálování a jejich přijetí, jsou vyhodnoceny a chápány.
* Jednotlivci, kteří se podílejí na vývoji konceptu, mají dobrou představu o veškerém kódu. Také porozuměli dotyčným materiálům a jsou v širším přijetí jistotu.

### <a name="open-questions--prerequisites"></a>Otevřené otázky & požadavky 

* Máme vytvořené předplatné, které bychom mohli použít pro tento projekt? 
* Máme tenanta Azure AD a globální správce Azure AD, kteří vám můžou poskytnout pomoc & doprovodné materiály týkající se otázek týkajících se Azure AD? 
* Místo pro spolupráci jednotlivců pracujících na projektu: 

   * Zdrojový kód a skripty (například Azure Repos) 
   * Dokumenty (jako týmy nebo SharePoint)  
   * Konverzace (jako Microsoft Teams) 
   * Pracovní položky (například Azure Boards) 
* Jaké jsou požadované prostředky pro dodavatele? To zahrnuje místní virtuální počítače a další požadované servery a aplikace, které jsou v síti k dispozici. 
* Budou virtuální počítače připojené k doméně v Azure? Pokud ano, bude to Azure AD Domain Services nebo něco jiného? 
* Máte identifikovaný tým nebo dodavatelé, kteří budou cílem zkušebního konceptu? Kdo budou zákazníci pro prostředí?
* Kterou oblast Azure použijeme k ověření konceptu? 
* Máme seznam služeb, které můžou dodavatelé používat přes DevTest Labs kromě IaaS (VM)? 
* Jak Plánujem školení dodavatelů/uživatelů na používání testovacího prostředí? 

### <a name="proof-of-concept-solution-components"></a>Ověření součástí řešení konceptu 

Očekáváme, že řešení má následující komponenty: 

* Sada DevTest Labs v Azure pro různé týmy dodavatelů.
* Laboratoře jsou připojené k síťové infrastruktuře, která podporuje požadavky.
* Dodavatelé mají přístup k testovacím prostředím přes Azure AD a udělují přiřazení rolí do testovacího prostředí.
* Způsob, jakým se dodavatelé úspěšně připojí ke svým prostředkům. Konkrétně síť VPN typu Site-to-site, která umožňuje přístup k virtuálním počítačům přímo bez veřejných IP adres.
* Sada artefaktů, které pokrývají požadovaný software, který potřebují dodavatelé na virtuálních počítačích.

## <a name="additional-planning-and-design-decisions"></a>Další rozhodnutí pro plánování a návrh 

Před vydáním kompletního řešení DevTest Labs je potřeba udělat několik důležitých rozhodnutí o plánování a návrhu. Činnost při práci na zkušebním konceptu vám může usnadnit provádění těchto rozhodnutí. Mezi další aspekty patří: 

* **Topologie**předplatného: Požadavky na podnikové úrovni pro prostředky v Azure můžou rozšiřovat nad rámec [dostupných kvót v rámci jednoho](https://docs.microsoft.com/azure/azure-subscription-service-limits)předplatného, což vyžaduje více předplatných Azure nebo žádostí o služby na zvýšení počátečních limitů předplatného. Je důležité se rozhodnout, jak distribuovat prostředky mezi předplatnými, jedním z důležitých zdrojů je [Příručka pro rozhodování](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) o předplatném, protože je obtížné později přesunout prostředky do jiného předplatného. Například testovací prostředí DevTest nelze po vytvoření přesunout do jiného předplatného.  
* **Topologie sítě**: [Výchozí síťová infrastruktura](../app-service/networking-features.md) , která je automaticky vytvořená DevTest Labs, nemusí být dostačující pro splnění požadavků a omezení pro podnikové uživatele. Je běžné, že se pro připojení mezi předplatnými a i vynuceným směrováním zajišťuje jenom místní připojení [](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) [ExpressRoute připojené virtuální sítě](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/), [centrum a paprsek](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) . DevTest Labs umožňuje, aby se existující virtuální sítě připojovaly k testovacímu prostředí, které umožňuje jejich použití při vytváření nových virtuálních počítačů v testovacím prostředí. 
* **Vzdálený přístup Virtual Machines**: Existuje spousta možností vzdáleného přístupu k virtuálním počítačům umístěným v DevTest Labs. Nejjednodušší je použít veřejné IP adresy nebo sdílené veřejné IP adresy, jedná se [o nastavení, které je dostupné v testovacím prostředí](devtest-lab-shared-ip.md). Pokud tyto možnosti nestačí, je použití brány vzdáleného přístupu také možností, jak je znázorněno v [referenční architektuře DevTest Labs Enterprise](devtest-lab-reference-architecture.md) a dále popsané v dokumentaci ke službě [Brána vzdálené plochy DevTest Labs](configure-lab-remote-desktop-gateway.md). Podniky můžou k připojení DevTest Labs k místní síti použít taky Express Route nebo VPN typu Site-to-site. To umožňuje přímé připojení vzdálené plochy nebo SSH k Virtual Machines na základě jejich privátní IP adresy bez vystavení Internetu. 
* **Oprávnění ke zpracování**: Dvě klíčová oprávnění běžně používaná v DevTest Labs jsou ["vlastník" a "uživatel testovacího prostředí"](devtest-lab-add-devtest-user.md). Je důležité se rozhodnout před tím, než DevTest laboratoře budou v podstatě, které budou pověřit každou úroveň přístupu v testovacím prostředí. Běžným modelem je vlastník rozpočtu (například vedoucí týmu) jako vlastník testovacího prostředí a členové týmu jako uživatelé testovacího prostředí. To umožní osobě (vedoucí týmu) odpovědnost za rozpočet upravit nastavení zásad, aby se tým zachoval v rámci rozpočtu.  

## <a name="completing-the-proof-of-concept"></a>Dokončení zkušebního konceptu 

Po dokončení předpokládaných kurzů je čas zabalit a dokončit pilotní nasazení. To je čas, kdy je potřeba shromáždit zpětnou vazbu od uživatelů, zjistit, jestli se pilotní projekt úspěšně nacházel, a rozhodnout se, jestli se organizace bude pohybovat na škále DevTest Labs v podniku. Je také skvělý čas zvážit automatizaci nasazení DevTest Labs a přidružených prostředků, aby se zajistila konzistence v celém rozsahu. 

## <a name="next-steps"></a>Další kroky 

* [Dokumentace k DevTest Labs Enterprise](devtest-lab-guidance-prescriptive-adoption.md)
* [Referenční architektura pro podnik](devtest-lab-reference-architecture.md)
* [Škálování nasazení DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
* [Orchestrace implementace Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
