---
title: Poskytujte doklad o konceptu – Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Zjistěte, jak dodat důkaz o konceptu, aby bylo možné azure devtest labs úspěšně začlenit do podnikového prostředí.
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
ms.openlocfilehash: ca843213760cee60799568a6f33059c2bd91c835
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75643283"
---
# <a name="deliver-a-proof-of-concept"></a>Dodat doklad o koncepci 

Jedním z klíčových scénářů pro Azure DevTest Labs je povolení vývojových a testovacích prostředí v cloudu. Příklady obsahují:

* Vytváření vývojářských ploch v cloudu.
* Konfigurace prostředí pro testování.
* Povolení přístupu k virtuálním počítačům a dalším prostředkům Azure.
* Nastavení oblasti izolovaného prostoru pro vývojáře, aby se učili a experimentovali.

DevTest Labs také poskytuje svodidla zásad a řízení nákladů, které umožňují podniku poskytovat "samoobslužný Azure" vývojářům, kteří dodržují zásady podnikového zabezpečení, předpisů a dodržování předpisů. 

Každý podnik má jiné požadavky na to, jak lze azure devtest labs úspěšně začlenit do svého prostředí. Tento článek popisuje nejběžnější kroky, které podniky potřebují k dokončení, aby zajistily úspěšný doklad o konceptu. Důkazem konceptu je první krok k úspěšnému nasazení od konce. 

## <a name="getting-started"></a>Začínáme 

Chcete-li začít s poskytováním důkazu koncepce. Je důležité strávit nějaký čas se dozvědět o Azure a DevTest Labs.  Zde jsou některé počáteční zdroje: 

* [Principy portálu Azure](https://azure.microsoft.com/features/azure-portal/)
* [Základy devTest Labs](devtest-lab-overview.md)
* [Podporované scénáře DevTest Labs](devtest-lab-guidance-get-started.md)
* [Podniková dokumentace devTest Labs](devtest-lab-guidance-prescriptive-adoption.md)
* [Úvod do sítě Azure](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Požadavky 

Chcete-li úspěšně dokončit pilotní nebo doklad o konceptu s DevTest Labs, existuje několik předpokladů: 

* **Předplatné Azure**: Podniky mají často existující [smlouvu Enterprise,](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) která umožňuje přístup k Azure, a můžou používat stávající nebo nové předplatné pro DevTest Labs. Alternativně podniky mohou používat [předplatné Visual Studio](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) během pilotního projektu (s využitím bezplatných kreditů Azure). Pokud není k dispozici ani jedna z těchto možností, může podnik vytvořit a používat [bezplatný účet Azure](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ). Pokud je na místě smlouva Enterprise, použití [předplatného Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) je skvělou volbou pro získání přístupu k klientským operačním systémům Windows 10/Windows 8.1 a zvýhodněným sazbám pro vývojové a testovací úlohy. 
* **Tenant Azure Active Directory**: Chcete-li povolit správu uživatelů (například přidání uživatelů nebo přidání vlastníků testovacího prostředí), tito uživatelé musí být součástí [tenanta Azure Active Directory](https://azure.microsoft.com/services/active-directory/) používaného v předplatném Azure pro pilotní projekt. Podniky často nastaví [hybridní identitu,](../active-directory/hybrid/whatis-hybrid-identity.md) aby uživatelům umožnily používat jejich místní identitu v cloudu, ale to není vyžadováno pro pilotní zařízení DevTest Labs. 

## <a name="scoping-of-the-pilot"></a>Vymezení rozsahu pilota 

Před zahájením implementace je důležité naplánovat pilotní projekt. Vědět předem, že zdroje nezůstanou po neomezenou dobu, nastaví příslušná očekávání pro uživatele pilota. 

> [!IMPORTANT]
> Nemůžeme dostatečně zdůraznit, že je důležité ostře posuzovat pilota a nastavovat očekávání dopředu.

Odpovězte na tyto klíčové otázky, než začnete pilota: 

* Co se chcete naučit a jak vypadá úspěch pilota? 
* Jaké úlohy nebo scénáře budou zahrnuty v pilotním projektu? Je důležité definovat pouze malou sadu, aby bylo zajištěno, že pilot může být rychle vymezen a dokončen. 
* Jaké prostředky musí být k dispozici v laboratoři? Například: vlastní obrázky, obrázky marketplace, zásady, topologie sítě. 
* Kdo jsou uživatelé a týmy, které budou zapojeny do pilotního ověření zkušenosti?  
* Jaká je doba trvání pilota? Zvolte časový rámec, který se dobře zarovná k plánovanému oboru, například dva týdny nebo jeden měsíc. 
* Co se stane s přidělenými zdroji, které byly během pilotního projektu použity, až bude pilot dokončen? Plánujete zahodit pilotní zdroje? Možná si myslíte:
   
   "Pokud můžeme naplánovat vyhození virtuálních počítačů a laboratoří na konci pilotního projektu, pak můžeme nastavit jediné předplatné pro pilota a dělat tam veškerou naši práci, zatímco budeme řešit otázky zavádění stupnice paralelně." 

Tam je obecná tendence, aby se pilot "perfektní", takže to identicky představuje to, co konečný stav bude po službě je válí ve společnosti. To je mylný předpoklad. Čím blíže se dostanete k "dokonalosti", tím více musíte *dokončit, než* začnete s pilotem. Účelem pilota je učinit správná rozhodnutí o rozšíření a zavedení konečné služby. 

Zaměření pilotního by měla být na výběr minimální potřebné úlohy a závislosti s cílem odpovědět na otázku, zda Azure DevTest Labs je správná služba pro váš podnik. Doporučujeme zvolit nejjednodušší úlohy s nejmenšími závislostmi, které vám pomohou zajistit rychlý a čistý úspěch. Pokud to není možné, vyberte nejreprezentativnější úlohu, která zveřejňuje potenciální složitosti tak, aby úspěch v pilotní fázi lze replikovat ve fázi horizontálního navýšení kapacity. 

Následující příklad ukazuje dobře vymezené proof koncepce. 

## <a name="example-proof-of-concept-plan"></a>Příklad: plán proof-of-concept 

Tato část ukazuje ukázku pro vymezení doklad o konceptu pilot pro DevTest Labs. 

> [!TIP]
> Chcete-li minimalizovat možnost nastavení projektu pro selhání, důrazně doporučujeme, abyste nepřeskočili příklad popsaný v této části. 

### <a name="overview"></a>Přehled 

Plánujeme vyvinout nové prostředí v Azure založené na DevTest Labs pro dodavatele, které budou používat jako izolované prostředí z podnikové sítě. Chcete-li zjistit, zda řešení bude splňovat požadavky, vyvineme ověření komplexního řešení. Zahrnuli jsme několik dodavatelů, abychom vyzkoušeli a ověřili zážitek. 

### <a name="outcomes"></a>Výsledky 

Při vytváření důkazu koncepce se zaměřujeme nejprve na výsledky (čeho se snažíme dosáhnout). Na konci dokladu koncepce očekáváme: 

* Pracovní komplexní řešení pro dodavatele, které používá účty hosta ve službě Azure Active Directory (Azure AD) pro přístup k izolovanému prostředí v Azure. Prostředí má zdroje potřebné k tomu, aby byly produktivní. 
* Všechny potenciální problémy blokování, které ovlivňují širší škálování použití a přijetí jsou uvedeny a pochopeny.
* Jednotlivci podílející se na vývoji důkaz koncepce mají dobré pochopení všech kódů. Oni také pochopit zajištění zapojeny a jsou přesvědčeni o širší přijetí.

### <a name="open-questions-and-prerequisites"></a>Otevřené otázky a předpoklady 

* Máme vytvořené předplatné, které můžeme použít pro tento projekt? 
* Máme klienta Azure AD a globálního správce Azure AD, kteří identifikovali, kdo může poskytnout pomoc a pokyny pro otázky související s Azure AD? 
* Máme místo pro spolupráci pro jednotlivce pracující na projektu? 

   * Zdrojový kód a skripty (například Azure Repos) 
   * Dokumenty (například Microsoft Teams nebo SharePoint)  
   * Konverzace (například Microsoft Teams) 
   * Pracovní položky (jako Azure Boards) 
* Jaké jsou požadované prostředky pro dodavatele? To zahrnuje aplikace dostupné v síti, místně na virtuálních počítačích i na jiných požadovaných serverech. 
* Připojí se virtuální počítače k doméně v Azure? Pokud ano, bude to Služba Azure Active Directory Domain Services (Azure AD DS) nebo něco jiného? 
* Identifikovali jsme tým nebo dodavatele, kteří budou cílem důkazu koncepce? Kdo bude zákazníkem pro životní prostředí?
* Kterou oblast Azure použijeme pro protektor? 
* Máme seznam služeb, které dodavatelé mohou používat prostřednictvím devTest Labs kromě IaaS (VM)? 
* Jak plánujeme vyškolit dodavatele/uživatele na používání testovacího prostředí? 

### <a name="components-of-the-proof-of-concept-solution"></a>Součásti řešení proof-of-concept 

Očekáváme, že řešení bude mít následující komponenty: 

* Různé týmy dodavatelů budou používat sadu testovacích prostředí v Azure.
* Laboratoře jsou připojeny k síťové infrastruktuře, která podporuje požadavky.
* Dodavatelé mají přístup k testovacím prostředím prostřednictvím služby Azure AD a přiřazení rolí.
* Dodavatelé mají způsob, jak úspěšně připojit ke svým prostředkům. Konkrétně síť VPN site-to-site umožňuje přímý přístup k virtuálním počítačům bez veřejných IP adres.
* Sada artefaktů pokrývá požadovaný software, který dodavatelé potřebují na virtuálních počítačích.

## <a name="additional-planning-and-design-decisions"></a>Další rozhodnutí o plánování a návrhu 

Před vydáním úplného řešení DevTest Labs musíte učinit důležitá rozhodnutí o plánování a návrhu. Zkušenosti s prací na důkazu koncepce vám mohou pomoci při těchto rozhodnutích. Další úvaha zahrnuje: 

* **Topologie předplatného**: Požadavky na podnikové úrovni pro prostředky v Azure mohou přesahovat [dostupné kvóty v rámci jednoho předplatného](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). To vyžaduje více předplatných Azure nebo požadavky na služby ke zvýšení počáteční limity předplatného. Je důležité se předem rozhodnout, jak distribuovat prostředky mezi předplatnými. Jeden cenný prostředek je [průvodce rozhodnutí předplatného,](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) protože je obtížné přesunout prostředky do jiného předplatného později. Například testovací prostředí nelze přesunout do jiného předplatného po jeho vytvoření.  
* **Topologie sítě**: [Výchozí síťová infrastruktura,](../app-service/networking-features.md) kterou devTest Labs automaticky vytvoří, nemusí být dostatečná ke splnění požadavků a omezení pro podnikové uživatele. Je běžné vidět [Azure ExpressRoute připojené virtuální sítě](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/), [rozbočovač a paprskpro](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) připojení napříč předplatnými a dokonce [i nucené směrování](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) k zajištění pouze místní připojení. DevTest Labs umožňuje připojení existujících virtuálních sítí k testovacímu prostředí, aby bylo možné je používat při vytváření nových virtuálních počítačů v testovacím prostředí. 
* **Vzdálený přístup virtuálních počítačů**: Existuje mnoho možností vzdáleného přístupu k virtuálním počítačům umístěným v DevTest Labs. Nejjednodušší je použít veřejné IP adresy nebo sdílené veřejné IP adresy. Jedná [se o nastavení, která](devtest-lab-shared-ip.md)jsou k dispozici v testovacím prostředí . Pokud tyto možnosti nejsou dostatečné, je také možnost použít bránu vzdáleného přístupu. Tato možnost je zobrazena na [podnikové referenční architektuře DevTest Labs](devtest-lab-reference-architecture.md) a dále popsána v [dokumentaci brány vzdálené plochy DevTest Labs](configure-lab-remote-desktop-gateway.md). Podniky mohou také používat ExpressRoute nebo síť VPN mezi lokalitami k připojení svých testovacích prostředí k místní síti. Tato možnost umožňuje přímé připojení vzdálené plochy nebo SSH k virtuálním počítačům na základě jejich privátní IP adresy bez vystavení internetu. 
* **Oprávnění zpracování**: Dvě klíčová oprávnění běžně používaná v laboratořích DevTest jsou [vlastník a uživatel testovacího prostředí](devtest-lab-add-devtest-user.md). Je důležité rozhodnout před zavedením DevTest Labs široce, kdo bude pověřen každou úroveň přístupu v laboratoři. Běžným modelem je vlastník rozpočtu (například vedoucí týmu) jako vlastník testovacího prostředí a členové týmu jako uživatelé testovacího prostředí. Tento model umožňuje osobě (vedoucímu týmu) odpovědné mu za rozpočet upravit nastavení zásad a udržet tým v rámci rozpočtu.  

## <a name="completing-the-proof-of-concept"></a>Dokončení dokladu o koncepci 

Poté, co byly pokryty očekávané poznatky, je čas dokončit pilota. Toto je čas shromáždit zpětnou vazbu od uživatelů, zjistit, zda byl pilot úspěšný, a rozhodnout, zda organizace bude pokračovat v zavádění ve velkém měřítku DevTest Labs v podniku. Je to také skvělý čas, aby zvážila automatizaci nasazení DevTest Labs a přidružené prostředky k zajištění konzistence v celém zavádění škálování. 

## <a name="next-steps"></a>Další kroky 

* [Podniková dokumentace devTest Labs](devtest-lab-guidance-prescriptive-adoption.md)
* [Referenční architektura pro podnik](devtest-lab-reference-architecture.md)
* [Škálování nasazení devTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
* [Orchestrace implementace Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
