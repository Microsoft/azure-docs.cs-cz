---
title: Poskytněte zkoušku konceptu – Azure DevTest Labs | Microsoft Docs
description: Naučte se, jak doručovat koncept, aby se Azure DevTest Labs mohl úspěšně začlenit do podnikového prostředí.
ms.topic: article
ms.date: 06/2/2020
ms.openlocfilehash: 9c28cf9eebd8a39a2edce48e4fb8b96dc7608d80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87288019"
---
# <a name="deliver-a-proof-of-concept"></a>Doručovat zkoušku konceptu 

Jedním z klíčových scénářů pro Azure DevTest Labs je povolení prostředí pro vývoj a testování v cloudu. Příklady:

* Vytváření desktopových ploch pro vývojáře v cloudu.
* Konfigurace prostředí pro testování.
* Povolení přístupu k virtuálním počítačům a dalším prostředkům Azure.
* Nastavením oblasti izolovaného prostoru pro vývojáře se naučíte a Experimentujte.

DevTest Labs taky poskytuje zásady guardrails a cost Controls, které organizacím umožňují poskytovat svým vývojářům "samoobslužné služby Azure" pro vývojáře, kteří dodržují zásady zabezpečení, regulativní předpisy a dodržování předpisů v podniku. 

Každý podnik má různé požadavky na způsob, jakým je možné Azure DevTest Labs úspěšně začlenit do svého prostředí. Tento článek popisuje nejběžnější kroky, které musí podniky provést, aby bylo zajištěno úspěšného ověření konceptu. Důkaz konceptu je prvním krokem k úspěšnému dokončení nasazení na konci. 

## <a name="getting-started"></a>Začínáme 

Abyste mohli začít dodávat zkoušku konceptu. Je důležité věnovat nějakou dobu více informací o Azure a DevTest Labs.  Tady je několik počátečních prostředků: 

* [Princip Azure Portal](https://azure.microsoft.com/features/azure-portal/)
* [Základy DevTest Labs](devtest-lab-overview.md)
* [Scénáře podporované DevTest Labs](devtest-lab-guidance-get-started.md)
* [Dokumentace k DevTest Labs Enterprise](devtest-lab-guidance-prescriptive-adoption.md)
* [Úvod do sítě Azure](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Požadavky 

K úspěšnému dokončení pilotního nebo zkušebního konceptu pomocí DevTest Labs existuje několik požadavků: 

* **Předplatné Azure**: podniky mají často existující [smlouva Enterprise](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) , které umožňují přístup k Azure, a můžou používat stávající nebo nové předplatné pro DevTest Labs. Případně můžou podniky použít předplatné sady [Visual Studio](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) během pilotního nasazení (využijí bezplatné kredity Azure). Pokud není k dispozici žádná z těchto možností, může podnik vytvořit a použít [bezplatný účet Azure](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ). Pokud je smlouva Enterprise na místě, používá [Enterprise pro vývoj/testování předplatné](https://azure.microsoft.com/offers/ms-azr-0148p/) skvělou možnost pro přístup k Windows 10/Windows 8.1 klientským operačním systémům a zlevněné sazby pro úlohy vývoje a testování. 
* **Azure Active Directory tenant**: Pokud chcete povolit správu uživatelů (například přidávání uživatelů nebo přidávání vlastníků testovacího prostředí), musí být uživatelé součástí [klienta Azure Active Directory](https://azure.microsoft.com/services/active-directory/) používaného v předplatném Azure pro pilotní nasazení. Podniky často nastaví [hybridní identitu](../active-directory/hybrid/whatis-hybrid-identity.md) , aby uživatelům umožnili používat místní identitu v cloudu, ale to není vyžadováno pro pilotní nasazení DevTest Labs. 

## <a name="scoping-of-the-pilot"></a>Rozsah pilotního nasazení 

Než začnete s implementací, je důležité naplánovat pilotní nasazení. Zajistěte si předem vědět, že prostředky nebudou zůstat neomezeně, a to pro uživatele pilotního projektu. 

> [!IMPORTANT]
> Nemůžeme zdůraznit důležitost, která má výrazný význam na začátku vytváření pilotního projektu a nastavování očekávání.

Než se pustíte do pilotního projektu, odpovězte na tyto klíčové otázky: 

* Co se chcete dozvědět a co se u pilotního projektu zdá, co je úspěšné? 
* Jaké úlohy nebo scénáře budou zahrnuty do pilotního projektu? Je důležité definovat jenom malou sadu, aby bylo zajištěno, že pilotní modul může být v oboru a dokončený rychleji. 
* Jaké prostředky musí být k dispozici v testovacím prostředí? Například: vlastní image, image Marketplace, zásady, topologie sítě. 
* Kteří uživatelé a týmy, kteří budou zapojeni do pilotního projektu, chtějí ověřit prostředí?  
* Jaká je doba trvání pilotního projektu? Vyberte časový rámec, který bude správně zarovnán k plánovanému oboru, například dva týdny nebo jeden měsíc. 
* Co se po dokončení pilotního nasazení stane s přidělenými prostředky, které se používaly během pilotního projektu? Plánujete zrušit pilotní prostředky? Můžete si představit:
   
   "Pokud můžeme naplánovat vystavování virtuálních počítačů a cvičení na konci pilotního projektu, můžeme pro pilotní nasazení nastavit jedno předplatné a udělat veškerou práci tam, kde máme paralelní řešení otázek při uvádění na škálování." 

Je všeobecně k dispozici možnost pilotního projektu, takže identický přesně představuje to, co konečný stav bude po zavedení služby ve společnosti. Toto je nepravdivý předpoklad. Bližší informace o tom, co se dostane k "dokonalému", je další, že musíte dokončit *před* zahájením pilotního nasazení. Účelem pilotního nasazení je učinit správná rozhodnutí o škálování a zavedení finální služby. 

Cílem pilotního projektu by měl být vyzvednutí minimálních potřebných zatížení a závislostí, aby bylo možné odpovědět na otázku, zda Azure DevTest Labs je správná služba pro váš podnik. Doporučujeme, abyste zvolili nejjednodušší úlohy s minimálními závislostmi, které vám pomůžou zajistit rychlou a čistou úspěch. Pokud to není možné, vyberte nejvíce reprezentativní zatížení, které zveřejňuje potenciální složitosti, aby bylo možné úspěch ve fázi pilotního nasazení replikovat ve fázi škálování na více instancí. 

Následující příklad ukazuje dobře vymezený koncept konceptu. 

## <a name="example-proof-of-concept-plan"></a>Příklad: plán pro stanovení konceptu 

Tato část obsahuje ukázku, která se používá k určení konceptu pilotního projektu pro DevTest Labs. 

> [!TIP]
> Aby se minimalizovala možnost nastavení projektu pro selhání, důrazně doporučujeme, abyste tento příklad popsaný v této části nemuseli vynechat. 

### <a name="overview"></a>Přehled 

Plánujeme vyvíjet nové prostředí v Azure založené na DevTest Labs, aby se dodavatelé mohli použít jako izolované prostředí z podnikové sítě. Abychom zjistili, jestli řešení splňuje požadavky, vyvineme zkoušku konceptu pro ověření kompletního řešení. Zahrnuli jsme několik dodavatelů, než si vyzkoušíme a ověříte prostředí. 

### <a name="outcomes"></a>Výsledek 

Při vytváření zkušebního konceptu se nejprve zaměřte na výsledky (co se snaží dosáhnout). Na konci zkušebního konceptu očekáváme: 

* Pracovní ucelené řešení pro dodavatele, které používá účty hosta v Azure Active Directory (Azure AD) pro přístup k izolovanému prostředí v Azure. Prostředí má prostředky potřebné k zajištění produktivity. 
* Všechny potenciální blokující problémy, které mají vliv na širší použití škálování a přijetí, jsou výčtové a srozumitelné.
* Jednotlivci, kteří se podílejí na vývoji konceptu, mají dobrou představu o veškerém kódu. Také porozuměli dotyčným materiálům a jsou v širším přijetí jistotu.

### <a name="open-questions-and-prerequisites"></a>Otevřené otázky a požadavky 

* Máme vytvořené předplatné, které můžeme použít pro tento projekt? 
* Máme tenanta Azure AD a globální správce Azure AD, kteří můžou poskytnout nápovědu a pokyny pro otázky týkající se služby Azure AD? 
* Máme místo pro spolupráci jednotlivců, kteří pracují na projektu? 

   * Zdrojový kód a skripty (například Azure Repos) 
   * Dokumenty (například Microsoft Teams nebo SharePoint)  
   * Konverzace (jako Microsoft Teams) 
   * Pracovní položky (například Azure Boards) 
* Jaké jsou požadované prostředky pro dodavatele? To zahrnuje aplikace, které jsou k dispozici v síti, místně na virtuálních počítačích a na jiných požadovaných serverech. 
* Budou virtuální počítače připojené k doméně v Azure? Pokud ano, bude to Azure Active Directory Domain Services (Azure služba AD DS) nebo něco jiného? 
* Identifikovali jsme tým nebo dodavatele, kteří budou cílem zkušebního konceptu? Kdo budou zákazníci pro prostředí?
* Kterou oblast Azure použijeme k ověření konceptu? 
* Máme seznam služeb, které můžou dodavatelé používat přes DevTest Labs kromě IaaS (VM)? 
* Jak Plánujem vzdělávání dodavatelů a uživatelů pomocí testovacího prostředí? 

### <a name="components-of-the-proof-of-concept-solution"></a>Součásti řešení pro ověření konceptu 

Očekáváme, že řešení má následující komponenty: 

* Různé týmy dodavatele budou v Azure používat sadu laboratoří.
* Laboratoře jsou připojené k síťové infrastruktuře, která podporuje požadavky.
* Dodavatelé mají přístup k testovacím prostředím prostřednictvím služby Azure AD a přiřazení rolí.
* Dodavatelé mají možnost se úspěšně připojit ke svým prostředkům. Konkrétně síť VPN typu Site-to-site umožňuje přístup k virtuálním počítačům přímo bez veřejných IP adres.
* Sada artefaktů pokrývá požadovaný software, který dodavatelé potřebují na virtuálních počítačích.

## <a name="additional-planning-and-design-decisions"></a>Další rozhodnutí pro plánování a návrh 

Před vydáním kompletního řešení DevTest Labs musíte udělat nějaká důležitá rozhodnutí pro plánování a návrh. Činnost při práci na zkušebním konceptu vám může usnadnit provádění těchto rozhodnutí. Mezi další aspekty patří: 

* **Topologie předplatného**: požadavky na podnikové úrovni pro prostředky v Azure můžou překračovat rámec [dostupných kvót v rámci jednoho předplatného](../azure-resource-manager/management/azure-subscription-service-limits.md). To vyžaduje několik předplatných Azure a/nebo žádostí o služby, aby se zvýšily počáteční limity předplatného. Před distribucí prostředků mezi předplatnými je důležité se rozhodnout. Jedním z cenných prostředků je [Příručka pro rozhodování o předplatném](/azure/architecture/cloud-adoption/decision-guides/subscriptions/) , protože později je obtížné přesunout prostředky do jiného předplatného. Testovací prostředí se například nedá po vytvoření přesunout do jiného předplatného.  
* **Síťová topologie**: [Výchozí síťová infrastruktura](../app-service/networking-features.md) , kterou DevTest Labs automaticky vytvoří, nemusí být dostačující pro splnění požadavků a omezení pro podnikové uživatele. Je běžné, že [Azure ExpressRoute připojené virtuální sítě](/azure/architecture/reference-architectures/hybrid-networking/), [centrum a paprsky](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) pro připojení mezi předplatnými a dokonce i [vynucené směrování](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) , aby se zajistilo jenom místní připojení. DevTest Labs umožňuje, aby se existující virtuální sítě připojovaly k testovacímu prostředí, které umožňuje jejich použití při vytváření nových virtuálních počítačů v testovacím prostředí. 
* **Vzdálený přístup k virtuálním počítačům**: existuje spousta možností vzdáleného přístupu k virtuálním počítačům, které se nacházejí v DevTest Labs. Nejjednodušším způsobem je použít veřejné IP adresy nebo sdílené veřejné IP adresy. Toto jsou [nastavení, která jsou k dispozici v testovacím prostředí](devtest-lab-shared-ip.md). Pokud tyto možnosti nestačí, je použití brány vzdáleného přístupu také možnost. Tato možnost se zobrazuje v [referenční architektuře Enterprise DevTest Labs](devtest-lab-reference-architecture.md) a dále se popisuje v dokumentaci ke službě [Brána vzdálené plochy DevTest Labs](configure-lab-remote-desktop-gateway.md). Podniky můžou použít taky ExpressRoute nebo VPN typu Site-to-site pro připojení svých cvičení k místní síti. Tato možnost umožňuje přímé vzdálené plochy nebo připojení SSH k virtuálním počítačům na základě jejich privátní IP adresy bez vystavení pro Internet. 
* **Oprávnění ke zpracování**: dvě klíčová oprávnění běžně používaná v DevTest Labs jsou [vlastníkem a uživatelem testovacího prostředí](devtest-lab-add-devtest-user.md). Je důležité se rozhodnout před tím, než DevTest laboratoře budou v podstatě, které budou pověřit každou úroveň přístupu v testovacím prostředí. Běžným modelem je vlastník rozpočtu (například vedoucí týmu) jako vlastník testovacího prostředí a členové týmu jako uživatelé testovacího prostředí. Tento model umožňuje osobě (vedoucí týmu) odpovědnost za rozpočet upravit nastavení zásad a udržovat tým v rámci rozpočtu.  

## <a name="completing-the-proof-of-concept"></a>Dokončení zkušebního konceptu 

Po pokrytí očekávaných kurzů je čas dokončit pilotní nasazení. Je to čas, kdy se mají získat názory uživatelů, zjistit, jestli se pilotní projekt úspěšně nacházel, a rozhodnout se, jestli se organizace předá na zavedení DevTest Labs v podniku na škále. Je také skvělý čas zvážit automatizaci nasazení DevTest Labs a přidružených prostředků, aby se zajistila konzistence v celém rozsahu zavedení. 

## <a name="next-steps"></a>Další kroky 

* [Dokumentace k DevTest Labs Enterprise](devtest-lab-guidance-prescriptive-adoption.md)
* [Referenční architektura pro podnik](devtest-lab-reference-architecture.md)
* [Škálování nasazení DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
* [Orchestrace implementace Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
