---
title: Azure Security Center – nejčastější dotazy (FAQ) | Dokumentace Microsoftu
description: Tyto nejčastější dotazy odpovědi na otázky o Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2018
ms.author: rkarlin
ms.openlocfilehash: 320c7c483e865c85948d32ee2b5b70a92181920f
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160065"
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Nejčastější dotazy ohledně Azure Security Center
Tyto nejčastější dotazy odpovědi na otázky o Azure Security Center, služba, která vám pomůže zabránit, detekci a reakce na ně prostřednictvím zvýšené viditelnosti a kontroly nad zabezpečením vašich prostředků Microsoft Azure.

> [!NOTE]
> Od začátku června 2017 bude Security Center používat ke shromažďování a ukládání dat agenta Microsoft Monitoring Agent. Další informace najdete v tématu [migrace platformy pro Azure Security Center](security-center-platform-migration.md). Informace v tomto článku představují funkce služby Security Center po přechodu na agenta Microsoft Monitoring Agent.
>
>

## <a name="general-questions"></a>Obecné otázky
### <a name="what-is-azure-security-center"></a>Co je Azure Security Center?
Azure Security Center pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně a nabízí lepší přehled o zabezpečení prostředků Azure a kontrolu nad nimi. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

### <a name="how-do-i-get-azure-security-center"></a>Jak získat Azure Security Center?
Azure Security Center je povolená s vaším předplatným Microsoft Azure a přistupovat z procedur [webu Azure portal](https://azure.microsoft.com/features/azure-portal/). ([Přihlásit k portálu](https://portal.azure.com)vyberte **Procházet**, přejděte k **Security Center**).  

## <a name="billing"></a>Fakturace
### <a name="how-does-billing-work-for-azure-security-center"></a>Jak funguje fakturace pro Azure Security Center?
Security Center se nabízí ve dvou úrovních:

**Úroveň Free** poskytuje přehled o stavu zabezpečení prostředků Azure, základní zásady zabezpečení, doporučení týkající se zabezpečení a integraci s produkty zabezpečení a službami od partnerů.

**Úrovně Standard** přidá hrozeb pokročilé možnosti detekce, včetně analýzy hrozeb intelligence, analýzy chování, detekce anomálií, incidentů zabezpečení a hrozeb attribution sestavy. Úroveň Standard je po dobu prvních 60 dní zdarma. Rozhodnete pokračovat v používání služby se po uplynutí 60 dnů, automaticky začneme službu účtovat.  Chcete-li provést upgrade, vyberte [cenová úroveň](https://docs.microsoft.com/azure/security-center/security-center-pricing) v zásadách zabezpečení.

## <a name="permissions"></a>Oprávnění
Azure Security Center používá [řízení přístupu na základě Role (RBAC)](../role-based-access-control/role-assignments-portal.md), která poskytuje [předdefinované role](../role-based-access-control/built-in-roles.md) , které můžete přiřadit uživatelům, skupinám a službám v Azure.

Security Center posuzuje konfiguraci vašich prostředků identifikovat problémy se zabezpečením a ohrožení zabezpečení. Ve službě Security Center zobrazí jenom informace související s prostředku, když máte přiřazenou roli vlastník, Přispěvatel nebo Čtenář pro předplatné nebo skupinu prostředků, které prostředek patří.

Zobrazit [oprávnění ve službě Azure Security Center](security-center-permissions.md) získat další informace o rolí a povolených akcí ve službě Security Center.

## <a name="data-collection"></a>Shromažďování dat
Security Center shromažďuje data z Azure virtual machines (VM) a počítače mimo Azure monitorovat ohrožení zabezpečení a hrozby. Data se shromažďují pomocí agenta Microsoft Monitoring Agent, který z počítače načítá různé protokoly událostí a konfigurace související se zabezpečením a kopíruje data k analýze do vašeho pracovního prostoru.

### <a name="how-do-i-disable-data-collection"></a>Jak zakázat shromažďování dat?
Automatické zřizování je ve výchozím nastavení vypnuté. Můžete vypnout automatické zřizování z prostředků v každém okamžiku vypnutím toto nastavení v zásadách zabezpečení. Automatické zřizování se důrazně doporučujeme zajistí výstrahy zabezpečení a doporučení týkající se aktualizací systému, ohrožení zabezpečení operačního systému a endpoint protection.

Zakázání shromažďování dat [Přihlaste se k webu Azure portal](https://portal.azure.com)vyberte **Procházet**vyberte **Security Center**a vyberte **vyberte zásadu**. Vyberte předplatné, pro které chcete vypnout automatické zřizování. Když vyberete předplatné **zásady zabezpečení – shromažďování dat** otevře. V části **automatického zřizování**vyberte **vypnout**.

### <a name="how-do-i-enable-data-collection"></a>Jak povolit shromažďování dat?
Shromažďování dat můžete povolit u předplatného Azure v zásadě zabezpečení. Chcete-li povolit shromažďování dat. [Přihlaste se k webu Azure portal](https://portal.azure.com)vyberte **Procházet**vyberte **Security Center**a vyberte **zásady zabezpečení**. Vyberte předplatné, pro kterého chcete povolit automatické zřizování. Když vyberete předplatné **zásady zabezpečení – shromažďování dat** otevře. V části **automatického zřizování**vyberte **na**.

### <a name="what-happens-when-data-collection-is-enabled"></a>Co se stane, když je povolené shromažďování dat?
Pokud je povoleno automatické zřizování, Security Center zřídí agenta Microsoft Monitoring Agent na všech podporovaných virtuálních počítačích Azure a nových, které jsou vytvořeny. Automatické zřizování se důrazně doporučuje, ale ruční instalaci agentů je také k dispozici. [Zjistěte, jak nainstalovat rozšíření Microsoft Monitoring Agent](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Agent povolí události vytváření procesů 4688 a *CommandLine* pole v rámci události 4688. Nových procesů vytvořených ve virtuálním počítači se zaznamenávají v protokolu událostí a monitoruje detekce služby Security Center. Informace v podrobnostech pro každý nový proces naleznete v tématu [pole Popis v 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Agent také 4688 událostí vytvořených ve virtuálním počítači shromáždí a uloží je v hledání.

Když Security Center detekuje podezřelou aktivitu na virtuálním počítači, je zákazník informuje e-mailem, pokud [kontaktní informace o zabezpečení](security-center-provide-security-contact-details.md) byl poskytnut. Výstraha je také viditelné v řídicím panelu Security Center security výstrahy.

> [!NOTE]
> - Chcete-li povolit shromažďování dat pro [adaptivní řízení aplikací](security-center-adaptive-application.md), Security Center je nakonfiguruje místní zásady nástroje AppLocker v režimu auditování povolit všechny aplikace. To způsobí, že nástroj AppLocker generovat události, které jsou pak shromážděná a využít Security Center. Je důležité si uvědomit, že tyto zásady nenakonfigurují v počítačích, na kterých je už nakonfigurovaná zásada nástroje AppLocker. 

### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Monitoring Agent vliv na výkon své servery?
Agent používá nominální množství systémových prostředků a mělo mít minimální vliv na výkon. Další informace o vlivu na výkon a agent a rozšíření najdete v článku [Průvodce plánováním a provozem](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Kde jsou moje data uložená?
Data shromážděná z tohoto agenta se ukládají do existujícího pracovního prostoru Log Analytics přidružených k vašemu předplatnému nebo vytvořte nový. Další informace najdete v tématu [zabezpečení dat](security-center-data-security.md).

## <a name="using-azure-security-center"></a>Pomocí Azure Security Center
### <a name="what-is-a-security-policy"></a>Co jsou zásady zabezpečení?
Zásady zabezpečení definují sadu ovládacích prvků, které se doporučují pro prostředky v rámci zadaného předplatného. Ve službě Azure Security Center určíte zásady pro vaše předplatná Azure podle požadavků zabezpečení vaší společnosti a podle typu aplikací nebo citlivosti dat v každém předplatném.

Zásady zabezpečení povolené ve službě monitorování a doporučení pro zabezpečení Azure Security Center jednotky. Další informace o zásadách zabezpečení najdete v tématu [sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Kdo může upravit zásady zabezpečení?
Pokud chcete upravit nějakou zásadu zabezpečení, musíte být správce zabezpečení nebo vlastníka nebo přispěvatele daného předplatného.

Další informace o konfiguraci zásad zabezpečení, najdete v článku [nastavení zásad zabezpečení ve službě Azure Security Center](security-center-policies.md).

### <a name="what-is-a-security-recommendation"></a>Co je doporučeným zabezpečením?
Azure Security Center analyzuje stav zabezpečení vašich prostředků Azure. Pokud se identifikují potenciální ohrožení zabezpečení, vytvoří se doporučení. Doporučení vás provede procesem konfigurace potřebných ovládacího prvku. Mezi příklady patří:

* Zřizování antimalwarového pomáhají identifikovat a odebrat škodlivý software
* [Skupiny zabezpečení sítě](../virtual-network/security-overview.md) a pravidla pro řízení přenosu do virtuálních počítačů
* Zřízení firewallu webových aplikací, které pomáhají bránit útokům, které cílí na vaše webové aplikace
* Nasazení chybějících aktualizací systému
* Adresování konfigurací operačního systému, které neodpovídají doporučeným standardním hodnotám

Jenom o doporučení, které jsou povolené v zásadách zabezpečení se tady zobrazí.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Jak lze zobrazit aktuální stav zabezpečení svoje prostředky Azure?
**Přehled služby Security Center** okno ukazuje celkové postavení zabezpečení prostředí porušena výpočetní prostředky, sítě, úložiště a data a aplikace. Každý typ prostředku má zobrazení indikátoru, pokud byly identifikovány možných ohrožení zabezpečení. Kliknutím na každé dlaždici se zobrazí seznam zabezpečení problémů zjištěných pomocí Security Center spolu s inventář prostředků ve vašem předplatném.

### <a name="what-triggers-a-security-alert"></a>Co se aktivuje upozornění na zabezpečení?
Azure Security Center automaticky shromažďuje, analyzuje a fuses data protokolu z vašich prostředků Azure, sítě a řešení partnerů, jako jsou antimalwarové zásady a brány firewall. Při zjištění ohrožení zabezpečení se vytvoří výstraha zabezpečení. Příklady zahrnují zjišťování následujících situací:

* Ohrožené virtuální počítače, které komunikují se známými IP adresami se zlými úmysly
* Pokročilý malware zjištěný pomocí zasílání zpráv o chybách Windows
* Útoky hrubou silou na virtuální počítače
* Výstrahy zabezpečení z integrovaných partnerských řešení zabezpečení, jako je Anti-Malware nebo brány firewall webových aplikací

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Jaký je rozdíl mezi hrozeb zjištěných a oznamovaných na podle Microsoft Security Response Center a Azure Security Center?
Microsoft Security Response Center (MSRC) provádí monitorování zabezpečení vyberte síť Azure a infrastrukturou a přijímá threat intelligence a zneužití stížností od třetích stran. Při MSRC zjistí, že zákaznická data přistupovala nezákonně nebo neoprávněně strany nebo, že využití Azure pro zákazníka není v souladu s podmínkami přijatelné použití, upozorní správce incidentu zabezpečení zákazníka. Oznámení se obvykle dochází, pošlete e-mail na kontakty zabezpečení zadané v Azure Security Center nebo vlastník předplatného Azure, pokud není zadán kontaktu zabezpečení.

Security Center je služba Azure nepřetržitě monitoruje prostředí Azure zákazníka a použije analytics mohla zjišťovat automaticky širokou škálu potenciálně škodlivé aktivity. Tyto detekce jsou prezentované jako výstrahy zabezpečení v řídicím panelu Security Center.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Které prostředky Azure jsou monitorovány pomocí služby Azure Security Center?
Azure Security Center monitoruje prostředky Azure následující:

* Virtuální počítače (VM) (včetně [Cloud Services](../cloud-services/cloud-services-choose-me.md))
* Virtuální sítě Azure
* Služba Azure SQL
* Účet služby Azure Storage
* Azure Web Apps (v [App Service Environment](../app-service/environment/intro.md))
* Partnerských řešení integrovaných ve vašem předplatném Azure, jako je například firewallu webových aplikací na virtuálních počítačích a ve službě App Service Environment

## <a name="virtual-machines"></a>Virtuální počítače
### <a name="what-types-of-virtual-machines-are-supported"></a>Jaké typy virtuálních počítačů jsou podporovány?
Monitorování a doporučení jsou k dispozici pro virtuální počítače (VM) vytvořené pomocí obou [klasické modely nasazení a modely nasazení Resource Manager](../azure-classic-rm.md).

Zobrazit [podporovaných platforem ve službě Azure Security Center](security-center-os-coverage.md) seznam podporovaných platforem.

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Proč Azure Security Center nerozpoznal antimalwarové řešení, které běží na mém virtuálním počítači Azure?
Azure Security Center obsahuje přehled antimalwarové nainstalované prostřednictvím rozšíření Azure. Například není Security Center schopna zjistit Antimalwarový program, který byl předem nainstalované na obrázku, který jste zadali, nebo pokud jste nainstalovali antimalwarové na vašich virtuálních počítačů pomocí vlastních procesů (jako jsou systémy pro správu konfigurace).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Proč se zobrazila zpráva "Chybí Data kontroly" pro můj virtuální počítač?
Tato zpráva se zobrazí, když nejsou žádná data kontroly pro virtuální počítač. Může trvat nějakou dobu (míň než hodinu) pro data kontroly k naplnění po povolení shromažďování dat ve službě Azure Security Center. Po počáteční počet obyvatel data kontroly může zobrazit tato zpráva vzhledem k tomu, že neexistuje žádná data kontroly vůbec nebo neexistuje žádná nová data kontroly. Prohledávání nenaplňuje pro virtuální počítač v zastaveném stavu. Tato zpráva se může zobrazit, pokud nebyla zadána data kontroly nedávno (v souladu se zásadami uchovávání informací pro agenta Windows, což je výchozí hodnota 30 dnů).

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Jak často Security Center zjišťovat existenci ohrožení zabezpečení operačního systému, aktualizací systému a problémy s endpoint protection?
Latence ve službě Security Center vyhledává ohrožení zabezpečení, aktualizací, a je problémy:

- Konfigurace zabezpečení operačního systému – data se aktualizuje do 48 hodin
- Aktualizace systému – data se aktualizuje během 24 hodin
- Problémy s Endpoint Protection – data se aktualizují v 8 hodin

Dostupnost nových dat. Security Center obvykle prohledává každou hodinu. Výše uvedené hodnoty latence jsou nejhorší případu scénář, kde není k dispozici poslední kontroly nebo kontroly se nezdařilo.

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Proč se zobrazila zpráva "Agent virtuálního počítače je chybějící?"
Agent virtuálního počítače musí být nainstalovaný na virtuálních počítačích povolíte shromažďování dat. Agent virtuálního počítače je ve výchozím nastavení nainstalován na virtuálních počítačích nasazených z Azure Marketplace. Informace o tom, jak nainstalovat agenta virtuálního počítače na jiné virtuální počítače, naleznete v příspěvku blogu [agenta virtuálního počítače a rozšíření](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).
