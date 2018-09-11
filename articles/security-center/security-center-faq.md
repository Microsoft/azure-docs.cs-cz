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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: rkarlin
ms.openlocfilehash: d8313ec66f8b71102e63751e7cf07885c899a7e8
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301034"
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

## <a name="data-collection-agents-and-workspaces"></a>Shromažďování dat, agentů a pracovní prostory
Security Center shromažďuje data z Azure virtual machines (VM) a počítače mimo Azure monitorovat ohrožení zabezpečení a hrozby. Data se shromažďují pomocí agenta Microsoft Monitoring Agent, který z počítače načítá různé protokoly událostí a konfigurace související se zabezpečením a kopíruje data k analýze do vašeho pracovního prostoru.

### <a name="am-i-billed-for-log-analytics-on-the-workspaces-created-by-security-center"></a>Fakturují ke službě Log Analytics na pracovních prostorů vytvořených službou Security Center?
Ne. Pracovních prostorů vytvořených službou Security Center, i když nakonfigurovat pro Log Analytics podle počtu uzlů, fakturace, ne poplatky Log Analytics. Fakturace centra zabezpečení je vždy na základě zásad zabezpečení Security Center a řešení nainstalované v pracovním prostoru:

- **Úroveň Free** – Security Center umožňuje řešení 'SecurityCenterFree' ve výchozím pracovním prostoru. Za na úrovni Free se nic neúčtuje.
- **Úroveň standard** – Security Center umožňuje řešení "Zabezpečení" na výchozího pracovního prostoru.

Další informace o cenách najdete v tématu [ceny Security Center](https://azure.microsoft.com/pricing/details/security-center/). Stránce s cenami řeší změny úložiště dat zabezpečení a poměrné fakturace počínaje červnem 2017.

> [!NOTE]
> Cenová úroveň pracovních prostorů vytvořených službou Security Center Log Analytics nemá vliv na fakturaci Security Center.
>
>

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Co splňuje podmínky virtuální počítač pro automatické zřizování instalace agenta Microsoft Monitoring Agent?
Windows nebo virtuální počítače IaaS s Linuxem kvalifikovat, pokud:

- Rozšíření Microsoft Monitoring Agent není nainstalována na virtuálním počítači.
- Virtuální počítač je ve spuštěném stavu.
- Je nainstalovaný Windows nebo Linux agenta virtuálního počítače.
- Virtuální počítač se nepoužívá jako zařízení jako jsou brány firewall webových aplikací nebo brána firewall příští generace.

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Můžete odstranit výchozí pracovních prostorů vytvořených službou Security Center?
**Odstranění výchozího pracovního prostoru se nedoporučuje.** Security Center používá výchozí pracovní prostory k uložení dat zabezpečení z virtuálních počítačů.  Při odstranění pracovního prostoru, Security Center nedokáže shromažďování těchto dat a některá doporučení zabezpečení a výstrahy nejsou k dispozici.

Pokud chcete obnovit, odeberte agenta Microsoft Monitoring Agent na virtuální počítače připojené k pracovním prostoru. Security Center opětovná instalace agenta a vytvoří nový výchozí pracovní prostory.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Použití mé existující pracovní prostor Log Analytics

Můžete vybrat existující pracovní prostor Log Analytics ukládat data shromážděná službou Security Center. Použití existující pracovní prostor Log Analytics:

- Pracovní prostor musí být přidružen vašem vybraném předplatném Azure.
- Minimálně musí mít čtení oprávnění pro přístup k pracovnímu prostoru.

Vyberte existující pracovní prostor Log Analytics:

1. V části **zásady zabezpečení – shromažďování dat**vyberte **použít jiný pracovní prostor**.

   ![Použít jiný pracovní prostor][5]

2. Z rozevírací nabídky vyberte pracovní prostor pro ukládání shromážděných dat.

   > [!NOTE]
   > V rozevírací nabídku o přijetí změn jsou zobrazeny pouze pracovní prostory, které mají přístup k a jsou ve vašem předplatném Azure.
   >
   >

3. Vyberte **Uložit**.
4. Po výběru **Uložit**, zobrazí se výzva, pokud chcete změnit konfiguraci monitorovaných virtuálních počítačů.

   - Vyberte **ne** Pokud chcete, aby nová nastavení pracovního prostoru **použít na nové virtuální počítače pouze**. Nový pracovní prostor nastavení platí pouze pro nové instalace agentů; nově zjištěných virtuálních počítačů, které nemají agenta Microsoft Monitoring Agent nainstalována.
   - Vyberte **Ano** Pokud chcete, aby nová nastavení pracovního prostoru **platí pro všechny virtuální počítače**. Kromě toho každý virtuální počítač připojený ke službě Security Center vytvořit pracovní prostor znovu nepřipojí do nového cílového pracovního prostoru.

   > [!NOTE]
   > Pokud vyberete Ano, nemůže odstranit pracovních prostorů vytvořených službou Security Center, dokud se všechny virtuální počítače mají úspěšné automatické připojení k novému pracovnímu prostoru cíl. Tato operace selže, pokud pracovní prostor je příliš brzké odstranit.
   >
   >

   - Vyberte **zrušit** na zrušení operace.

### <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>Co když agenta Microsoft Monitoring Agent byl již nainstalován jako rozšíření ve virtuálním počítači?
Security Center nemůže přepsat existující připojení k pracovním prostorům uživatele. Security Center ukládá data zabezpečení z virtuálního počítače v pracovním prostoru již připojen. Security Center aktualizuje verzi rozšíření zahrnout ID prostředku Azure, virtuálního počítače pro podporu použití Security Center.

### <a name="what-if-i-had-a-microsoft-monitoring-agent-installed-on-the-machine-but-not-as-an-extension"></a>Co když mám měli agenta Microsoft Monitoring Agent nainstalovaný na počítači, ale ne jako rozšíření?
Pokud agenta Microsoft Monitoring Agent je nainstalovaný přímo na virtuálním počítači (ne jako rozšíření Azure), Security Center není možné nainstalovat agenta Microsoft Monitoring Agent a monitorování zabezpečení je omezený.

Další informace najdete v části Další [co se stane, když OMS nebo SCOM přímý agent je již nainstalován na mém virtuálním počítači?](#scomomsinstalled)

### Co se stane, když OMS nebo SCOM přímý agent je již nainstalován na mém virtuálním počítači?<a name="scomomsinstalled"></a>
Security Center nemůže identifikovat předem, že je nainstalován agent.  Security Center pokusí nainstalovat rozšíření Microsoft Monitoring Agent a nezdaří z důvodu existující nainstalovaného agenta.  Toto selhání zabrání přepsání nastavení připojení agenta k její pracovní prostor a zabraňuje vytváření vícenásobné navádění.

> [!NOTE]
> Verze agenta se aktualizuje na nejnovější verzi agenta OMS.  Také platí pro uživatele SCOM.
>
>

### <a name="what-is-the-impact-of-removing-these-extensions"></a>Co je dopady odebrání těchto rozšíření?
Pokud odeberete rozšíření Microsoft Monitoring, Security Center nedokáže ke shromažďování dat zabezpečení z virtuálního počítače a několik doporučení zabezpečení a výstrahy nejsou k dispozici. Security Center během 24 hodin, určuje chybí rozšíření a znovu nainstaluje rozšíření virtuálního počítače.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Jak můžu zastavit instalaci a pracovní prostor vytváření agentů pro automatickou diagnostiku?
Můžete ji vypnout automatické zřizování pro vaše předplatná v zásadě zabezpečení ale to se nedoporučuje. Vypnutí výstrahy a automatické zřizování omezení doporučení služby Security Center. Automatické zřizování se vyžaduje pro předplatná na cenovou úroveň Standard. Chcete-li zakázat automatické zřizování:

1. Pokud vaše předplatné je nakonfigurován pro úroveň Standard, spustit nástroj Zásady zabezpečení pro dané předplatné a vyberte **Free** vrstvy.

   ![Cenová úroveň][1]

2. V dalším kroku se vypnout automatické zřizování tak, že vyberete **vypnout** na **zásady zabezpečení – shromažďování dat** okno.
   ![Shromažďování dat][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>By měl jsem vyjádřit výslovný nesouhlas automatickou instalaci agenta a vytváření pracovního prostoru?

> [!NOTE]
> Nezapomeňte si přečíst oddíly [jaké jsou důsledky vyjádří svůj nesouhlas?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) a [doporučený postup při vyjádří svůj nesouhlas](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) Pokud chcete vyjádřit výslovný nesouhlas automatické zřizování.
>
>

Můžete se odhlásit ze automatické zřizování, pokud pro vás platí následující:

- Automatickou instalaci agenta pomocí služby Security Center platí pro celé předplatné.  Automatická instalace nelze použít pro některé virtuální počítače řady. Pokud existují kritických virtuálních počítačů, které nejde nainstalovat pomocí agenta Microsoft Monitoring Agent, pak by měl zakážete automatické zřizování.
- Instalace rozšíření Microsoft Monitoring Agent aktualizuje verzi agenta. To platí pro přímý agent a s agentem SCOM. Pokud nainstalovaného agenta nástroje SCOM je verze 2012 a upgradu, když SCOM server má také verze 2012 může dojít ke ztrátě možnosti správy. Měli byste zvážit přestanete používat automatické zřizování, pokud je nainstalovaný agent nástroje SCOM verze 2012.
- Pokud máte vlastní pracovní prostor mimo předplatné (centrálních pracovních prostorů), pak by měl zakážete automatické zřizování. Můžete ručně nainstalovat rozšíření Microsoft Monitoring Agent a připojit ho váš pracovní prostor bez Security Center přepsání připojení.
- Pokud chcete se vyhnout vytváření více pracovních prostorů na předplatné a máte vlastní vlastní pracovní prostor v rámci předplatného, máte dvě možnosti:

   1. Můžete se rozhodnout automatické zřizování. Po dokončení migrace nastavení výchozí nastavení pracovního prostoru jak je popsáno v [jak můžete používat své existující pracovní prostor Log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)
   2. Nebo můžete povolit migraci tak, aby bylo možné provést, agenta Microsoft Monitoring Agent nainstalovaný na virtuálních počítačích, a virtuální počítače připojené do vytvořeného pracovního prostoru. Vyberte vlastní vlastní pracovní prostor tak, že nastavíte výchozí nastavení pracovního prostoru s vyjádření výslovného souhlasu s opětovná konfigurace již nainstalovaní agenti. Další informace najdete v tématu [jak můžete používat své existující pracovní prostor Log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Jaké jsou důsledky přestanete používat automatické zřizování?
Po dokončení migrace bude Security Center nedokáže ke shromažďování dat zabezpečení z virtuálního počítače a několik doporučení zabezpečení a výstrahy nejsou k dispozici. Pokud odhlásit, měli byste nainstalovat agenta Microsoft Monitoring Agent ručně. Zobrazit [doporučený postup při vyjádří svůj nesouhlas](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Co když přestanete používat automatické zřizování jsou doporučené kroky?
By měl ručně nainstalovat agenta Microsoft Monitoring Agent, Security Center můžete shromažďování dat zabezpečení z virtuálních počítačů a poskytování doporučení a výstrah. Zobrazit [počítače Windows se připojit ke službě Log Analytics v Azure](../log-analytics/log-analytics-windows-agent.md) pokyny k instalaci.

Můžete připojit agenta k jakékoli existující vlastní pracovní prostor nebo pracovní prostor vytvořený Security Center. Pokud vlastní pracovní prostor nemá "Zabezpečení" nebo "SecurityCenterFree" řešení povolené, je potřeba použít řešení. Pokud chcete použít, vyberte vlastní pracovní prostor nebo předplatné a použít cenovou úroveň prostřednictvím **zásady zabezpečení – cenová úroveň** okno.

   ![Cenová úroveň][1]

Security Center vám umožní správným řešením v pracovním prostoru podle vybrané cenové úrovně.

### Jak odeberu OMS rozšíření nainstalované pomocí služby Security Center?<a name="remove-oms"></a>
Můžete ručně odebrat agenta Microsoft Monitoring Agent. Toto nastavení nedoporučujeme, protože se limity výstrahy a doporučení služby Security Center.

> [!NOTE]
> Pokud je povolené shromažďování dat, Security Center bude po odebrání již přeinstalujte agenta.  Je nutné zakázat shromažďování dat před ruční odebrání agenta. Zobrazit [jak zastavit instalaci a pracovní prostor vytváření agentů pro automatickou diagnostiku?](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation?) pokyny k zakázání shromažďování dat.
>
>

Chcete-li ručně odebrat agenta:

1.  Na portálu otevřete **Log Analytics**.
2.  V okně Log Analytics vyberte pracovní prostor:
3.  Vyberte jednotlivé virtuální počítače, které nechcete monitorovat a vyberte **odpojit**.

   ![Pokud chcete agenta odebrat][3]

> [!NOTE]
> Pokud je virtuální počítač s Linuxem už agenta OMS není rozšířením, odebírá se rozšíření odebere také agenta a zákazník má k přeinstalování.
>
>
### <a name="how-do-i-disable-data-collection"></a>Jak zakázat shromažďování dat?
Automatické zřizování je ve výchozím nastavení vypnuté. Můžete vypnout automatické zřizování z prostředků v každém okamžiku vypnutím toto nastavení v zásadách zabezpečení. Automatické zřizování se důrazně doporučujeme zajistí výstrahy zabezpečení a doporučení týkající se aktualizací systému, ohrožení zabezpečení operačního systému a endpoint protection.

Zakázání shromažďování dat [Přihlaste se k webu Azure portal](https://portal.azure.com)vyberte **Procházet**vyberte **Security Center**a vyberte **vyberte zásadu**. Vyberte předplatné, pro které chcete vypnout automatické zřizování. Když vyberete předplatné **zásady zabezpečení – shromažďování dat** otevře. V části **automatického zřizování**vyberte **vypnout**.

### <a name="how-do-i-enable-data-collection"></a>Jak povolit shromažďování dat?
Shromažďování dat můžete povolit u předplatného Azure v zásadě zabezpečení. Chcete-li povolit shromažďování dat. [Přihlaste se k webu Azure portal](https://portal.azure.com)vyberte **Procházet**vyberte **Security Center**a vyberte **zásady zabezpečení**. Vyberte předplatné, pro kterého chcete povolit automatické zřizování. Když vyberete předplatné **zásady zabezpečení – shromažďování dat** otevře. V části **automatického zřizování**vyberte **na**.

### <a name="what-happens-when-data-collection-is-enabled"></a>Co se stane, když je povolené shromažďování dat?
Pokud je povoleno automatické zřizování, Security Center zřídí agenta Microsoft Monitoring Agent na všech podporovaných virtuálních počítačích Azure a nových, které jsou vytvořeny. Automatické zřizování se důrazně doporučuje, ale ruční instalaci agentů je také k dispozici. [Zjistěte, jak nainstalovat rozšíření Microsoft Monitoring Agent](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Agent povolí události vytváření procesů 4688 a *CommandLine* pole v rámci události 4688. Nových procesů vytvořených ve virtuálním počítači se zaznamenávají v protokolu událostí a monitoruje detekce služby Security Center. Informace v podrobnostech pro každý nový proces naleznete v tématu [pole Popis v 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Agent také 4688 událostí vytvořených ve virtuálním počítači shromáždí a uloží je v hledání.

Agent také umožňuje shromažďovat data pro [adaptivní řízení aplikací](security-center-adaptive-application.md), Security Center je nakonfiguruje místní zásady nástroje AppLocker v režimu auditování povolit všechny aplikace. To způsobí, že nástroj AppLocker generovat události, které jsou pak shromážděná a využít Security Center. Je důležité si uvědomit, že tyto zásady nenakonfigurují v počítačích, na kterých je už nakonfigurovaná zásada nástroje AppLocker. 

Když Security Center detekuje podezřelou aktivitu na virtuálním počítači, je zákazník informuje e-mailem, pokud [kontaktní informace o zabezpečení](security-center-provide-security-contact-details.md) byl poskytnut. Výstraha je také viditelné v řídicím panelu Security Center security výstrahy.



### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Monitoring Agent vliv na výkon své servery?
Agent používá nominální množství systémových prostředků a mělo mít minimální vliv na výkon. Další informace o vlivu na výkon a agent a rozšíření najdete v článku [Průvodce plánováním a provozem](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Kde jsou moje data uložená?
Data shromážděná z tohoto agenta se ukládají do existujícího pracovního prostoru Log Analytics přidružených k vašemu předplatnému nebo vytvořte nový. Další informace najdete v tématu [zabezpečení dat](security-center-data-security.md).

## Stávající zákazníci Log Analytics<a name="existingloganalyticscust"></a>

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Security Center přepsat existující připojení mezi virtuálními počítači a pracovním prostorům?
Pokud je virtuální počítač už Microsoft Monitoring Agent nainstalován jako rozšíření Azure, Security Center nemůže přepsat existující připojení pracovního prostoru. Security Center místo toho využívá existující pracovní prostor.

Řešení Security Center je nainstalované v pracovním prostoru Pokud není k dispozici již a řešení se použijí jenom u příslušných virtuálních počítačů. Když přidáte řešení, se automaticky nasadí ve výchozím nastavení pro všechny agenty Windows a Linuxem, připojený k pracovnímu prostoru Log Analytics. [Cílení na řešení](../operations-management-suite/operations-management-suite-solution-targeting.md) vám umožní použít obor do vašich řešení.

Pokud agenta Microsoft Monitoring Agent je nainstalovaný přímo na virtuálním počítači (ne jako rozšíření Azure), Security Center není možné nainstalovat agenta Microsoft Monitoring Agent a monitorování zabezpečení je omezený.

### <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Security Center se nenainstaluje řešení v mé existující pracovní prostory Log Analytics? Jaké jsou důsledky fakturace?
Když Security Center identifikuje, že virtuální počítač je již připojen k pracovnímu prostoru, který jste vytvořili, Security Center umožňuje řešení na tento pracovní prostor podle cenové úrovně. Řešení se použijí jenom u příslušných virtuálních počítačů Azure a prostřednictvím [cílení řešení](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting), takže fakturace se nezmění.

- **Úroveň Free** – Security Center nainstaluje řešení "SecurityCenterFree" v pracovním prostoru. Za na úrovni Free se nic neúčtuje.
- **Úroveň standard** – Security Center nainstaluje řešení "Zabezpečení" na pracovním prostoru.

   ![Řešení na výchozího pracovního prostoru][4]

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Už mám pracovní prostory v Moje prostředí, můžu využít k shromažďování dat zabezpečení?
Pokud je virtuální počítač už Microsoft Monitoring Agent nainstalován jako rozšíření Azure, Security Center používá existující připojený pracovní prostor. Řešení Security Center je nainstalované v pracovním prostoru Pokud není k dispozici již a řešení se použijí jenom u příslušných virtuálních počítačů prostřednictvím [cílení řešení](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting).

Security Center nainstaluje agenta Microsoft Monitoring Agent na virtuálních počítačích, použije výchozí pracovních prostorů vytvořených službou Security Center.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Už mám řešení zabezpečení na Moje pracovní prostory. Jaké jsou důsledky fakturace?
Řešení zabezpečení a Audit se používá k povolení funkce Security Center úrovně Standard úrovně pro virtuální počítače Azure. Pokud řešení Security and Audit je již nainstalován v pracovním prostoru, Security Center používá existující řešení. Není žádná změna ve fakturaci.

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


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
