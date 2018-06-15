---
title: Nejčastější dotazy (FAQ) Azure Security Center | Microsoft Docs
description: Tyto nejčastější dotazy odpovědi na otázky o Azure Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/14/2018
ms.author: terrylan
ms.openlocfilehash: d5a9f2ba68574ba8cb99b01ce426ec77a5eecd3d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34363957"
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Nejčastější dotazy ohledně Azure Security Center
Tyto nejčastější dotazy odpovědi na otázky o Azure Security Center, služba, která pomáhá zabránit, zjistit a reagovat na hrozby nabízí lepší přehled a kontrolu nad zabezpečení vašich prostředků Microsoft Azure.

> [!NOTE]
> Od začátku června 2017 bude Security Center používat ke shromažďování a ukládání dat agenta Microsoft Monitoring Agent. Další informace najdete v tématu [Azure Security Center platformy migrace](security-center-platform-migration.md). Informace v tomto článku představují funkce služby Security Center po přechodu na agenta Microsoft Monitoring Agent.
>
>

## <a name="general-questions"></a>Obecné otázky
### <a name="what-is-azure-security-center"></a>Co je Azure Security Center?
Azure Security Center pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně a nabízí lepší přehled o zabezpečení prostředků Azure a kontrolu nad nimi. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

### <a name="how-do-i-get-azure-security-center"></a>Jak získám Azure Security Center?
Azure Security Center je povolená s vaším předplatným Microsoft Azure a k němu přistupovat z [portál Azure](https://azure.microsoft.com/features/azure-portal/). ([Přihlaste se k portálu](https://portal.azure.com), vyberte **Procházet**a přejděte k položce **Security Center**).  

## <a name="billing"></a>Fakturace
### <a name="how-does-billing-work-for-azure-security-center"></a>Jak funguje fakturace pro Azure Security Center?
Security Center je k dispozici v dvěma vrstvami:

**Úroveň Free** poskytuje přehled o stavu zabezpečení prostředků Azure, zásady základního zabezpečení, doporučení zabezpečení a integrace s zabezpečovací produkty a služby od partnerů.

**Úrovně Standard** přidá rozšířené hrozba možností detekce, včetně hrozby intelligence, analýzy chování, detekce anomálií, incidenty zabezpečení a hrozby uvedení sestavy. Úroveň Standard je po dobu prvních 60 dní zdarma. By měl vybrat dál používat službu nad rámec 60 dnů, můžeme automaticky začít účtují pro službu.  Pokud chcete upgradovat, vyberte [cenová úroveň](https://docs.microsoft.com/azure/security-center/security-center-pricing) v zásadě zabezpečení.

## <a name="permissions"></a>Oprávnění
Azure Security Center používá [řízení přístupu na základě Role (RBAC)](../role-based-access-control/role-assignments-portal.md), který poskytuje [předdefinované role](../role-based-access-control/built-in-roles.md) , lze přiřadit uživatelům, skupinám a službám v Azure.

Security Center vyhodnocuje konfigurace vaše prostředky a identifikují problémy se zabezpečením a ohrožení zabezpečení. V Centru zabezpečení zobrazí jenom informace týkající se prostředek v případě jsou přiřazenou roli vlastník, Přispěvatel nebo Čtenář pro předplatné nebo skupinu prostředků, které daný prostředek patří.

V tématu [oprávnění v Azure Security Center](security-center-permissions.md) Další informace o rolích a povolených akcí v Centru zabezpečení.

## <a name="data-collection"></a>Shromažďování dat
Security Center shromažďuje data z Azure virtuální počítače (VM) a počítače mimo Azure pro monitorování ohrožení zabezpečení a hrozbami. Data se shromažďují pomocí agenta Microsoft Monitoring Agent, který z počítače načítá různé protokoly událostí a konfigurace související se zabezpečením a kopíruje data k analýze do vašeho pracovního prostoru.

### <a name="how-do-i-disable-data-collection"></a>Jakým způsobem vypnout shromažďování dat?
Automatické zřizování je ve výchozím nastavení vypnuté. Můžete zakázat automatické zřizování z prostředků kdykoli vypnutím tohoto nastavení v zásadě zabezpečení. Automatické zřizování důrazně doporučujeme, aby bylo možné získat výstrahy zabezpečení a doporučení ohledně aktualizací systému, ohrožení zabezpečení operačního systému a endpoint protection.

Zakázání shromažďování dat [Přihlaste se k portálu Azure](https://portal.azure.com), vyberte **Procházet**, vyberte **Security Center**a vyberte **vyberte zásadu**. Vyberte předplatné, pro které chcete vypnout automatické zřizování. Když vyberete předplatné **zásady zabezpečení – shromažďování dat** otevře. V části **automatické zřizování**, vyberte **vypnout**.

### <a name="how-do-i-enable-data-collection"></a>Povolení shromažďování dat
Shromažďování dat můžete povolit pro vaše předplatné Azure v zásadě zabezpečení. Chcete-li povolit shromažďování dat. [Přihlaste se k portálu Azure](https://portal.azure.com), vyberte **Procházet**, vyberte **Security Center**a vyberte **zásady zabezpečení**. Vyberte předplatné, které chcete povolit automatické zřizování. Když vyberete předplatné **zásady zabezpečení – shromažďování dat** otevře. V části **automatické zřizování**, vyberte **na**.

### <a name="what-happens-when-data-collection-is-enabled"></a>Co se stane, pokud je povoleno shromažďování dat?
Pokud je povoleno automatické zřizování, podporované Security Center zřizuje agenta Microsoft Monitoring Agent na všech virtuálních počítačích Azure a všechny nové, které jsou vytvořeny. Automatické zřizování důrazně doporučujeme, ale instalace ručního agenta je také k dispozici. [Naučte se nainstalovat rozšíření pro Microsoft Monitoring Agent](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension).

Událost procesu vytvoření 4688 umožňuje agenta a *CommandLine* pole v rámci události 4688. Nové procesů vytvořených ve virtuálním počítači jsou zaznamenány v protokolu událostí a monitorovat pomocí zjišťování služby Security Center. Informace v podrobnostech pro každý nový proces zaznamenány v tématu [pole Popis v 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Agent také shromažďuje 4688 události vytvořené ve virtuálním počítači a ukládá je do vyhledávání.

Pokud Security Center zjistí podezřelou aktivitu na virtuálním počítači, zákazník obdrží oznámení e-mailem, pokud [kontaktní informace o zabezpečení](security-center-provide-security-contact-details.md) bylo zadáno. Výstraha je také zobrazené na řídicím panelu výstrahy zabezpečení Security Center.

### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Monitoring Agent vliv na výkon Moje servery?
Agent využívá nominální množství systémové prostředky a musí mít malý vliv na výkon. Další informace o vlivu na výkon a agent a rozšíření najdete v tématu [Průvodce plánováním a operace](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Kde jsou moje data uložená?
Data shromážděná z tohoto agenta je uložená v existující pracovní prostor analýzy protokolů spojené s vaším předplatným nebo nový pracovní prostor. Další informace najdete v tématu [zabezpečení dat](security-center-data-security.md).

## <a name="using-azure-security-center"></a>Pomocí Azure Security Center
### <a name="what-is-a-security-policy"></a>Co je zásady zabezpečení?
Zásady zabezpečení definuje sadu ovládacích prvků, které se doporučují pro prostředky v rámci předplatného. V Azure Security Center určíte zásady pro vaše předplatná Azure podle požadavků zabezpečení vaší společnosti a podle typu aplikací nebo citlivosti dat v každém předplatném.

Zásady zabezpečení povolené v Azure Security Center doporučení zabezpečení jednotky a monitorování. Další informace o zásadách zabezpečení najdete v tématu [sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Kdo může upravit zásady zabezpečení?
Pokud chcete upravit zásadu zabezpečení, musíte být správce zabezpečení nebo roli vlastníka nebo přispěvatele daného předplatného.

Informace o tom, jak konfigurovat zásady zabezpečení, najdete v části [nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md).

### <a name="what-is-a-security-recommendation"></a>Co je doporučeným zabezpečením?
Azure Security Center analyzuje stav zabezpečení vašich prostředků Azure. Když jsou identifikovány potenciální ohrožení zabezpečení, doporučení jsou vytvořeny. Doporučení vás provede procesem konfigurace potřebných ovládacího prvku. Mezi příklady patří:

* Zřizování pomáhají identifikovat a odebrat škodlivý software proti malwaru
* [Skupin zabezpečení sítě](../virtual-network/security-overview.md) a pravidla pro řízení přenosu do virtuálních počítačů
* Zřizování brány firewall webových aplikací zvýšení ochrany proti útokům na cílení na vaše webové aplikace
* Nasazení chybějících aktualizací systému
* Adresování konfigurací operačního systému, které neodpovídají doporučeným standardním hodnotám

Zde se zobrazují pouze doporučení, které jsou povolené v zásadách zabezpečení.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Jak lze zobrazit aktuální stav zabezpečení prostředků Moje Azure?
**Přehled Centra zabezpečení** okno ukazuje celkové postavení zabezpečení prostředí podle výpočty, sítě, úložiště a datům a aplikacím. Každý typ prostředku má zobrazení indikátoru, pokud byly nalezeny všechny potenciální ohrožení zabezpečení. Kliknutím na každou dlaždici zobrazí seznam problémy se zabezpečením identifikovaný Security Center spolu s inventář prostředky ve vašem předplatném.

### <a name="what-triggers-a-security-alert"></a>Co se aktivuje výstraha zabezpečení?
Azure Security Center automaticky shromažďuje, analyzuje a fuses dat protokolu z vašich prostředků Azure, sítě a řešení partnerů, jako Antimalwarový program a brány firewall. Při zjištění ohrožení zabezpečení se vytvoří výstraha zabezpečení. Příklady zahrnují zjišťování následujících situací:

* Ohrožené virtuální počítače, které komunikují se známými IP adresami se zlými úmysly
* Pokročilý malware zjištěný pomocí zasílání zpráv o chybách systému Windows
* Útoky hrubou silou na virtuální počítače
* Výstrahy zabezpečení z integrovaných partnerských řešení zabezpečení například proti malwaru nebo brány firewall webových aplikací

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Jaký je rozdíl mezi hrozeb zjistila a zobrazovat výstrahy na podle Microsoft Security Response Center versus Azure Security Center?
Microsoft Security Response Center (MSRC) provádí sledování zabezpečení vyberte síť Azure a infrastruktury a přijímá stížností intelligence a zneužití ohrožení od jiných výrobců. Když střediska MSRC zjistí, že data zákazníků měl strana nezákonné nebo neoprávněný přístup k nebo že zákazníka použití Azure není v souladu s podmínkami pro použít přípustné, upozorní incidentu správce zabezpečení zákazníka. Oznámení se obvykle dochází, zasláním e-mailu zabezpečení kontakty, zadaný v Azure Security Center nebo vlastníka předplatného Azure, pokud není zadán a obraťte se na zabezpečení.

Security Center je služba Azure, která nepřetržitě monitoruje prostředí Azure zákazníka a použije analytics automaticky zjišťovat širokou škálu potenciálně škodlivé aktivity. Tyto detekce jsou prezentované jako výstrahy zabezpečení v řídicím panelu Security Center.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Prostředky, ke kterým Azure jsou monitorovány pomocí Azure Security Center?
Azure Security Center monitoruje následující prostředky Azure:

* Virtuální počítače (VM) (včetně [cloudové služby](../cloud-services/cloud-services-choose-me.md))
* Virtuální sítě Azure
* Služba Azure SQL
* Účet služby Azure Storage
* Webové aplikace Azure (v [služby App Service Environment](../app-service/environment/intro.md))
* Partnerských řešení integrovaných ve vašem předplatném Azure například brány firewall webových aplikací na virtuálních počítačích a na službu App Service Environment

## <a name="virtual-machines"></a>Virtuální počítače
### <a name="what-types-of-virtual-machines-are-supported"></a>Jaké typy virtuálních počítačů jsou podporovány?
Monitorování a doporučení jsou k dispozici pro virtuální počítače (VM) vytvořené pomocí [classic a modelech nasazení Resource Manager](../azure-classic-rm.md).

V tématu [podporovaných platforem v Azure Security Center](security-center-os-coverage.md) seznam podporovaných platforem.

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Proč Azure Security Center nerozpoznal řešení proti malwaru, který je spuštěn na virtuální počítač Azure?
Azure Security Center obsahuje přehled antimalwarové nainstalované prostřednictvím rozšíření Azure. Například Security Center není schopna zjistit proti malwaru, který byl předem nainstalovaná na bitovou kopii, kterou jste zadali, nebo pokud jste nainstalovali antimalwarových na virtuální počítače pomocí vlastní procesy (například systémy pro správu konfigurace).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Proč se zobrazila zpráva "Chybí Data kontroly" pro virtuální počítač?
Tato zpráva se zobrazí, když nejsou žádná data kontroly pro virtuální počítač. Může trvat delší dobu (méně než jednu hodinu) pro data skenování k naplnění po povolení shromažďování dat v Azure Security Center. Po počáteční naplnění data skenování může se zobrazit tato zpráva protože chybí data kontroly vůbec nebo nejsou žádná data poslední kontroly. Prohledávání naplnit není pro virtuální počítač v zastaveném stavu. Tato zpráva se může zobrazit i v případě, že data skenování nebylo naplněno nedávno (v souladu se zásadami uchovávání informací pro agenta systému Windows, který má výchozí hodnota je 30 dní).

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Jak často Security Center kontrolovat ohrožení zabezpečení operačního systému, aktualizací systému a potíže s ochranou koncového bodu?
Latence v Centru zabezpečení hledá ohrožení zabezpečení, aktualizace, a je problémy:

- Konfigurace zabezpečení operačního systému – data je aktualizovat v rámci 48 hodin
- Aktualizace systému – data se aktualizuje do 24 hodin
- Problémy ochrany koncového bodu – data se aktualizují v rámci 8 hodin

Pro nová data Security Center obvykle kontroluje každou hodinu. Výše uvedené latence hodnoty jsou nejhoršího scénáře kde poslední kontroly není nebo kontroly se nezdařilo.

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Proč se zobrazila zpráva "Agenta virtuálního počítače je chybějící?"
Na virtuálních počítačích povolit shromažďování dat musí být nainstalován Agent virtuálního počítače. Agent virtuálního počítače je ve výchozím nastavení nainstalován na virtuálních počítačích nasazených z Azure Marketplace. Informace o tom, jak nainstalovat agenta virtuálního počítače na ostatních virtuálních počítačů, naleznete v příspěvku blogu [agenta virtuálního počítače a rozšíření](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).
