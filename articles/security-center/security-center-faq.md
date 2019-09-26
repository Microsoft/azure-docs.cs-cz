---
title: Azure Security Center – nejčastější dotazy (FAQ) | Dokumentace Microsoftu
description: Tyto nejčastější dotazy odpovědi na otázky o Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: memildin
ms.openlocfilehash: bbb34a0a9d8035ce8cbfd3f3283677133370a9f2
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316726"
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Nejčastější dotazy ohledně Azure Security Center
Tyto nejčastější dotazy odpovědi na otázky o Azure Security Center, služba, která vám pomůže zabránit, detekci a reakce na ně prostřednictvím zvýšené viditelnosti a kontroly nad zabezpečením vašich prostředků Microsoft Azure.

> [!NOTE]
> Security Center používá Microsoft Monitoring Agent ke shromažďování a ukládání dat. Další informace najdete v tématu [migrace platformy pro Azure Security Center](security-center-platform-migration.md).
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

**Úrovně Standard** přidá hrozeb pokročilé možnosti detekce, včetně analýzy hrozeb intelligence, analýzy chování, detekce anomálií, incidentů zabezpečení a hrozeb attribution sestavy. Můžete spustit bezplatnou zkušební verzi na úrovni Standard. Chcete-li provést upgrade, vyberte [cenová úroveň](https://docs.microsoft.com/azure/security-center/security-center-pricing) v zásadách zabezpečení. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).

### <a name="how-can-i-track-who-in-my-organization-performed-pricing-tier-changes-in-azure-security-center"></a>Jak mohu sledovat, kdo v naší organizaci prováděl změny cenové úrovně v Azure Security Center
Předplatná Azure můžou mít víc správců s oprávněními ke změně cenové úrovně. Pokud chcete zjistit, který uživatel provedl změnu cenové úrovně, použijte protokol aktivit Azure. Další informace najdete [tady](https://techcommunity.microsoft.com/t5/Security-Identity/Tracking-Changes-in-the-Pricing-Tier-for-Azure-Security-Center/td-p/390832).

## <a name="permissions"></a>Oprávnění
Azure Security Center používá [řízení přístupu na základě rolí (RBAC)](../role-based-access-control/role-assignments-portal.md). To poskytuje [předdefinované role](../role-based-access-control/built-in-roles.md), které se dají v Azure přiřadit uživatelům, skupinám a službám.

Security Center posuzuje konfiguraci vašich prostředků identifikovat problémy se zabezpečením a ohrožení zabezpečení. Ve službě Security Center zobrazí jenom informace související s prostředku, když máte přiřazenou roli vlastník, Přispěvatel nebo Čtenář pro předplatné nebo skupinu prostředků, které prostředek patří.

Zobrazit [oprávnění ve službě Azure Security Center](security-center-permissions.md) získat další informace o rolí a povolených akcí ve službě Security Center.

## <a name="data-collection-agents-and-workspaces"></a>Shromažďování dat, agentů a pracovní prostory
Security Center shromažďuje data z virtuálních počítačů Azure, sady škálování virtuálních počítačů, kontejnerů IaaS a počítačů mimo Azure (včetně místních), aby se mohla monitorovat ohrožení zabezpečení a hrozby. Data se shromažďují pomocí agenta Microsoft Monitoring Agent, který z počítače načítá různé protokoly událostí a konfigurace související se zabezpečením a kopíruje data k analýze do vašeho pracovního prostoru.

### <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Účtují se mi Azure Monitor protokoly v pracovních prostorech vytvořených pomocí Security Center?
Ne. Pracovní prostory vytvořené pomocí Security Center, i když jsou nakonfigurované pro Azure Monitor protokolů za uzel, se neúčtují Azure Monitor poplatky. Fakturace centra zabezpečení je vždy na základě zásad zabezpečení Security Center a řešení nainstalované v pracovním prostoru:

- **Úroveň Free** – Security Center umožňuje řešení 'SecurityCenterFree' ve výchozím pracovním prostoru. Za bezplatnou úroveň se vám nebude účtovat.
- **Úroveň standard** – Security Center umožňuje řešení "Zabezpečení" na výchozího pracovního prostoru.

Další informace o cenách najdete v tématu [ceny Security Center](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Cenová úroveň Log Analytics pracovních prostorů vytvořených pomocí Security Center nemá vliv na Security Centerou fakturaci.
>
>

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Co splňuje podmínky virtuální počítač pro automatické zřizování instalace agenta Microsoft Monitoring Agent?
Windows nebo virtuální počítače IaaS s Linuxem kvalifikovat, pokud:

- Rozšíření Microsoft Monitoring Agent není nainstalována na virtuálním počítači.
- Virtuální počítač je ve spuštěném stavu.
- Je nainstalovaný [Agent virtuálního počítače Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) se systémem Windows nebo Linux.
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

### Co když už je Microsoft Monitoring Agent na virtuálním počítači nainstalovaná jako rozšíření?<a name="mmaextensioninstalled"></a>
Pokud je agent monitorování nainstalován jako rozšíření, konfigurace rozšíření umožňuje vytváření sestav pouze do jednoho pracovního prostoru. Security Center nemůže přepsat existující připojení k pracovním prostorům uživatele. Security Center bude ukládat data zabezpečení z virtuálního počítače v pracovním prostoru, který je už připojený, za předpokladu, že je na něm nainstalované řešení Security nebo SecurityCenterFree. Security Center může v tomto procesu upgradovat verzi rozšíření na nejnovější verzi.

Další informace najdete v tématu [Automatické zřizování v případě již existující instalace agenta](security-center-enable-data-collection.md#preexisting).


### Co když mám Microsoft Monitoring Agent nainstalovanou přímo na počítači, ale ne jako rozšíření (přímý Agent)?<a name="directagentinstalled"></a>
Pokud je Microsoft Monitoring Agent nainstalovaná přímo na virtuálním počítači (ne jako rozšíření Azure), Security Center nainstaluje rozšíření Microsoft Monitoring Agent a může upgradovat agenta Microsoft Monitoring Agent na nejnovější verzi.
Nainstalovaný Agent bude pokračovat v hlášení do již nakonfigurovaných pracovních prostorů a kromě toho bude hlásit do pracovního prostoru nakonfigurovaného v Security Center (u počítačů s Windows je podporováno více domovských stránek).
Pokud je nakonfigurovaným pracovním prostorem pracovní prostor uživatele (ve výchozím nastavení není Security Center), bude nutné do něj nainstalovat řešení "Security/" SecurityCenterFree ", aby bylo možné Security Center zahájit zpracování událostí z virtuálních počítačů a počítačů, do kterých se hlásí. stejných.

Pro počítače se systémem Linux zatím není podporována podpora více domovských stránek agenta, takže pokud je zjištěna existující instalace agenta, Automatické zřizování nebude provedeno a konfigurace počítače nebude změněna.

Pro existující počítače v předplatných, která se zaregistrují do Security Center do března 17 2019, se při zjištění existujícího agenta Microsoft Monitoring Agent rozšíření nenainstaluje a počítač nebude ovlivněn. Pro tyto počítače si přečtěte doporučení "vyřešit problémy se stavem agenta monitorování na vašich počítačích" a vyřešte problémy s instalací agenta na těchto počítačích.

 Další informace najdete v další části, [co se stane, když je na mém virtuálním počítači už nainstalovaný System Center Operations Manager nebo přímý agent OMS](#scomomsinstalled) .

### Co se stane, když je na mém virtuálním počítači už nainstalovaný Agent System Center Operations Manager?<a name="scomomsinstalled"></a>
Security Center nainstaluje rozšíření Microsoft Monitoring Agent vedle sebe stávajícímu agentu System Center Operations Manager. Stávající Agent bude nadále hlásit Server System Center Operations Manager normálně. Všimněte si, že agent Operations Manager a Microsoft Monitoring Agent sdílet běžné běhové knihovny, které budou během tohoto procesu aktualizovány na nejnovější verzi. Poznámka: Pokud je nainstalovaná verze 2012 agenta Operations Manager, nepovolujte Automatické zřizování (možnosti spravovatelnosti se můžou ztratit, pokud je Operations Manager Server verze 2012).

### <a name="what-is-the-impact-of-removing-these-extensions"></a>Co je dopady odebrání těchto rozšíření?
Pokud odeberete rozšíření Microsoft Monitoring, Security Center nedokáže ke shromažďování dat zabezpečení z virtuálního počítače a několik doporučení zabezpečení a výstrahy nejsou k dispozici. Security Center během 24 hodin, určuje chybí rozšíření a znovu nainstaluje rozšíření virtuálního počítače.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Jak můžu zastavit instalaci a pracovní prostor vytváření agentů pro automatickou diagnostiku?
Můžete ji vypnout automatické zřizování pro vaše předplatná v zásadě zabezpečení ale to se nedoporučuje. Vypnutí výstrahy a automatické zřizování omezení doporučení služby Security Center. Chcete-li zakázat automatické zřizování:

1. Pokud vaše předplatné je nakonfigurován pro úroveň Standard, spustit nástroj Zásady zabezpečení pro dané předplatné a vyberte **Free** vrstvy.

   ![Cenová úroveň][1]

2. V dalším kroku vypnout automatické zřizování výběrem možnosti **vypnuto** na stránce **zásady zabezpečení – shromažďování dat** .
   ![Shromažďování dat][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>By měl jsem vyjádřit výslovný nesouhlas automatickou instalaci agenta a vytváření pracovního prostoru?

> [!NOTE]
> Nezapomeňte si přečíst oddíly [jaké jsou důsledky vyjádří svůj nesouhlas?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) a [doporučený postup při vyjádří svůj nesouhlas](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) Pokud chcete vyjádřit výslovný nesouhlas automatické zřizování.
>
>

Můžete se odhlásit ze automatické zřizování, pokud pro vás platí následující:

- Automatickou instalaci agenta pomocí služby Security Center platí pro celé předplatné. Automatická instalace nelze použít pro některé virtuální počítače řady. Pokud existují kritických virtuálních počítačů, které nejde nainstalovat pomocí agenta Microsoft Monitoring Agent, pak by měl zakážete automatické zřizování.
- Instalace rozšíření Microsoft Monitoring Agent (MMA) aktualizuje verzi agenta. To platí pro přímého agenta a System Center Operations Managerho agenta (v druhé Operations Manager a MMA sdílet společné běhové knihovny, které se budou aktualizovat v procesu). Pokud je nainstalovaný agent Operations Manager verze 2012 a upgradovaný, můžou být možnosti spravovatelnosti ztraceny, pokud je Operations Manager Server také verze 2012. Pokud je nainstalovaný agent Operations Manager verze 2012, zvažte Automatické zřizování.
- Pokud máte vlastní pracovní prostor mimo předplatné (centralizovaný pracovní prostor), měli byste se odhlásit z automatického zřizování. Můžete ručně nainstalovat rozšíření Microsoft Monitoring Agent a připojit ho váš pracovní prostor bez Security Center přepsání připojení.
- Pokud chcete se vyhnout vytváření více pracovních prostorů na předplatné a máte vlastní vlastní pracovní prostor v rámci předplatného, máte dvě možnosti:

   1. Můžete se rozhodnout automatické zřizování. Po dokončení migrace nastavení výchozí nastavení pracovního prostoru jak je popsáno v [jak můžete používat své existující pracovní prostor Log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)
   2. Nebo můžete povolit migraci tak, aby bylo možné provést, agenta Microsoft Monitoring Agent nainstalovaný na virtuálních počítačích, a virtuální počítače připojené do vytvořeného pracovního prostoru. Vyberte vlastní vlastní pracovní prostor tak, že nastavíte výchozí nastavení pracovního prostoru s vyjádření výslovného souhlasu s opětovná konfigurace již nainstalovaní agenti. Další informace najdete v tématu [jak můžete používat své existující pracovní prostor Log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Jaké jsou důsledky přestanete používat automatické zřizování?
Po dokončení migrace Security Center nemůžou shromažďovat data zabezpečení z virtuálního počítače a některá doporučení a upozornění zabezpečení nejsou k dispozici. Pokud se odhlásíte odhlásit, nainstalujte Microsoft Monitoring Agent ručně. Zobrazit [doporučený postup při vyjádří svůj nesouhlas](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Co když přestanete používat automatické zřizování jsou doporučené kroky?

Ručně nainstalujte Microsoft Monitoring Agent rozšíření tak, aby Security Center mohl shromažďovat data zabezpečení z vašich virtuálních počítačů a poskytovat doporučení a výstrahy. Zobrazit [instalace agenta pro virtuální počítač Windows](../virtual-machines/extensions/oms-windows.md) nebo [instalace agenta pro virtuální počítač s Linuxem](../virtual-machines/extensions/oms-linux.md) pokyny k instalaci.

Můžete připojit agenta k jakékoli existující vlastní pracovní prostor nebo pracovní prostor vytvořený Security Center. Pokud vlastní pracovní prostor nemá "Zabezpečení" nebo "SecurityCenterFree" řešení povolené, je potřeba použít řešení. Pokud ho chcete použít, vyberte vlastní pracovní prostor nebo předplatné a pomocí stránky **zásady zabezpečení – cenová** úroveň použijte cenovou úroveň.

   ![Cenová úroveň][1]

Security Center vám umožní správným řešením v pracovním prostoru podle vybrané cenové úrovně.

### Jak odeberu OMS rozšíření nainstalované pomocí služby Security Center?<a name="remove-oms"></a>
Můžete ručně odebrat agenta Microsoft Monitoring Agent. Toto nastavení nedoporučujeme, protože se limity výstrahy a doporučení služby Security Center.

> [!NOTE]
> Pokud je povolené shromažďování dat, Security Center bude po odebrání již přeinstalujte agenta.  Je nutné zakázat shromažďování dat před ruční odebrání agenta. Přečtěte si téma Návody zastavení automatické instalace agenta a vytvoření pracovního prostoru? pokyny k zakázání shromažďování dat.
>
>

Chcete-li ručně odebrat agenta:

1.  Na portálu otevřete **Log Analytics**.
2.  Na stránce Log Analytics vyberte pracovní prostor:
3.  Vyberte virtuální počítače, které nechcete monitorovat, a vyberte **Odpojit**.

   ![Pokud chcete agenta odebrat][3]

> [!NOTE]
> Pokud je virtuální počítač s Linuxem už agenta OMS není rozšířením, odebírá se rozšíření odebere také agenta a zákazník má k přeinstalování.
>
>
### <a name="how-do-i-disable-data-collection"></a>Jak zakázat shromažďování dat?
Automatické zřizování je ve výchozím nastavení vypnuté. Můžete vypnout automatické zřizování z prostředků v každém okamžiku vypnutím toto nastavení v zásadách zabezpečení. Automatické zřizování se důrazně doporučuje, aby se získaly výstrahy zabezpečení a doporučení týkající se aktualizací systému, chyb zabezpečení operačního systému a ochrany koncových bodů.

Zakázání shromažďování dat [Přihlaste se k webu Azure portal](https://portal.azure.com)vyberte **Procházet**vyberte **Security Center**a vyberte **vyberte zásadu**. Vyberte předplatné, pro které chcete vypnout automatické zřizování. Když vyberete předplatné **zásady zabezpečení – shromažďování dat** otevře. V části **automatického zřizování**vyberte **vypnout**.

### <a name="how-do-i-enable-data-collection"></a>Jak povolit shromažďování dat?
Shromažďování dat můžete povolit u předplatného Azure v zásadě zabezpečení. Chcete-li povolit shromažďování dat. [Přihlaste se k webu Azure portal](https://portal.azure.com)vyberte **Procházet**vyberte **Security Center**a vyberte **zásady zabezpečení**. Vyberte předplatné, pro kterého chcete povolit automatické zřizování. Když vyberete předplatné **zásady zabezpečení – shromažďování dat** otevře. V části **automatického zřizování**vyberte **na**.

### <a name="what-happens-when-data-collection-is-enabled"></a>Co se stane, když je povolené shromažďování dat?
Pokud je povoleno automatické zřizování, Security Center zřídí agenta Microsoft Monitoring Agent na všech podporovaných virtuálních počítačích Azure a nových, které jsou vytvořeny. Automatické zřizování se doporučuje, ale k dispozici je také ruční instalace agenta. [Zjistěte, jak nainstalovat rozšíření Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Agent povolí události vytváření procesů 4688 a *CommandLine* pole v rámci události 4688. Nových procesů vytvořených ve virtuálním počítači se zaznamenávají v protokolu událostí a monitoruje detekce služby Security Center. Další informace o podrobnostech zaznamenaných pro každý nový proces najdete v tématu [Popis polí v 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Agent také 4688 událostí vytvořených ve virtuálním počítači shromáždí a uloží je v hledání.

Agent také umožňuje shromažďovat data pro [adaptivní řízení aplikací](security-center-adaptive-application.md), Security Center je nakonfiguruje místní zásady nástroje AppLocker v režimu auditování povolit všechny aplikace. Tato zásada způsobí, že AppLocker generuje události, které se pak shromažďují a využívají Security Center. Je důležité si uvědomit, že tyto zásady nenakonfigurují v počítačích, na kterých je už nakonfigurovaná zásada nástroje AppLocker. 

Když Security Center detekuje podezřelou aktivitu na virtuálním počítači, je zákazník informuje e-mailem, pokud [kontaktní informace o zabezpečení](security-center-provide-security-contact-details.md) byl poskytnut. Výstraha je také viditelné v řídicím panelu Security Center security výstrahy.

### <a name="will-security-center-work-using-an-oms-gateway"></a>Bude Security Center pracovat s bránou OMS?
Ano. Azure Security Center využívá Azure Monitor ke shromažďování dat z virtuálních počítačů a serverů Azure pomocí Microsoft Monitoring Agent.
Ke shromáždění dat se musí každý virtuální počítač a Server připojit k Internetu pomocí protokolu HTTPS. Připojení může být přímo, pomocí proxy serveru nebo pomocí [brány OMS](../azure-monitor/platform/gateway.md).

### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Monitoring Agent vliv na výkon své servery?
Agent používá nominální množství systémových prostředků a mělo mít minimální vliv na výkon. Další informace o vlivu na výkon a agent a rozšíření najdete v článku [Průvodce plánováním a provozem](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Kde jsou moje data uložená?
Data shromážděná z tohoto agenta se ukládají do existujícího pracovního prostoru Log Analytics přidružených k vašemu předplatnému nebo vytvořte nový. Další informace najdete v tématu [zabezpečení dat](security-center-data-security.md).

## Existující Azure Monitor protokolují zákazníky<a name="existingloganalyticscust"></a>

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Security Center přepsat existující připojení mezi virtuálními počítači a pracovním prostorům?
Pokud je virtuální počítač už Microsoft Monitoring Agent nainstalován jako rozšíření Azure, Security Center nemůže přepsat existující připojení pracovního prostoru. Security Center místo toho využívá existující pracovní prostor. Virtuální počítač bude chráněn za předpokladu, že řešení "Security" nebo "SecurityCenterFree" bylo nainstalováno do pracovního prostoru, do kterého se hlásí. 

Řešení Security Center je nainstalováno v pracovním prostoru vybraném na obrazovce shromažďování dat, pokud již není přítomno, a toto řešení je použito pouze pro příslušné virtuální počítače. Když přidáte řešení, se automaticky nasadí ve výchozím nastavení pro všechny agenty Windows a Linuxem, připojený k pracovnímu prostoru Log Analytics. [Cílení na řešení](../operations-management-suite/operations-management-suite-solution-targeting.md) vám umožní použít obor do vašich řešení.

Pokud agenta Microsoft Monitoring Agent je nainstalovaný přímo na virtuálním počítači (ne jako rozšíření Azure), Security Center není možné nainstalovat agenta Microsoft Monitoring Agent a monitorování zabezpečení je omezený.

### <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Security Center se nenainstaluje řešení v mé existující pracovní prostory Log Analytics? Jaké jsou důsledky fakturace?
Když Security Center identifikuje, že virtuální počítač je již připojen k pracovnímu prostoru, který jste vytvořili, Security Center umožňuje řešení na tento pracovní prostor podle cenové úrovně. Řešení se použijí jenom u příslušných virtuálních počítačů Azure a prostřednictvím [cílení řešení](../operations-management-suite/operations-management-suite-solution-targeting.md), takže fakturace se nezmění.

- **Úroveň Free** – Security Center nainstaluje řešení "SecurityCenterFree" v pracovním prostoru. Za bezplatnou úroveň se vám nebude účtovat.
- **Úroveň standard** – Security Center nainstaluje řešení "Zabezpečení" na pracovním prostoru.

   ![Řešení na výchozího pracovního prostoru][4]

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Už mám pracovní prostory v Moje prostředí, můžu využít k shromažďování dat zabezpečení?
Pokud je virtuální počítač už Microsoft Monitoring Agent nainstalován jako rozšíření Azure, Security Center používá existující připojený pracovní prostor. Řešení Security Center je nainstalované v pracovním prostoru Pokud není k dispozici již a řešení se použijí jenom u příslušných virtuálních počítačů prostřednictvím [cílení řešení](../operations-management-suite/operations-management-suite-solution-targeting.md).

Security Center nainstaluje agenta Microsoft Monitoring Agent na virtuálních počítačích, použije výchozí pracovních prostorů vytvořených službou Security Center.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Už mám řešení zabezpečení na Moje pracovní prostory. Jaké jsou důsledky fakturace?
Řešení zabezpečení a Audit se používá k povolení funkce Security Center úrovně Standard úrovně pro virtuální počítače Azure. Pokud řešení Security and Audit je již nainstalován v pracovním prostoru, Security Center používá existující řešení. Není žádná změna ve fakturaci.

## <a name="using-azure-security-center"></a>Pomocí Azure Security Center
### <a name="what-is-a-security-policy"></a>Co jsou zásady zabezpečení?
Zásady zabezpečení definují sadu ovládacích prvků, které se doporučují pro prostředky v rámci zadaného předplatného. Ve službě Azure Security Center určíte zásady pro vaše předplatná Azure podle požadavků zabezpečení vaší společnosti a podle typu aplikací nebo citlivosti dat v každém předplatném.

Zásady zabezpečení povolené ve službě monitorování a doporučení pro zabezpečení Azure Security Center jednotky. Další informace o zásadách zabezpečení najdete v tématu [sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Kdo může upravit zásady zabezpečení?
Pokud chcete upravit nějakou zásadu zabezpečení, musíte být správce zabezpečení nebo vlastníka nebo přispěvatele daného předplatného.

Další informace o konfiguraci zásad zabezpečení, najdete v článku [nastavení zásad zabezpečení ve službě Azure Security Center](tutorial-security-policy.md).

### <a name="what-is-a-security-recommendation"></a>Co je doporučeným zabezpečením?
Azure Security Center analyzuje stav zabezpečení vašich prostředků Azure. Pokud se identifikují potenciální ohrožení zabezpečení, vytvoří se doporučení. Doporučení vás provede procesem konfigurace potřebných ovládacího prvku. Můžete například:

* Zřizování antimalwarového pomáhají identifikovat a odebrat škodlivý software
* [Skupiny zabezpečení sítě](../virtual-network/security-overview.md) a pravidla pro řízení přenosu do virtuálních počítačů
* Zřízení firewallu webových aplikací, které pomáhají bránit útokům, které cílí na vaše webové aplikace
* Nasazení chybějících aktualizací systému
* Adresování konfigurací operačního systému, které neodpovídají doporučeným standardním hodnotám

Jenom o doporučení, které jsou povolené v zásadách zabezpečení se tady zobrazí.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Jak lze zobrazit aktuální stav zabezpečení svoje prostředky Azure?
Na stránce **přehled Security Center** se zobrazuje celkové stav zabezpečení vašeho prostředí v členění podle COMPUTE, sítě, & úložiště dat a aplikací. Každý typ prostředku má zobrazení indikátoru, pokud byly identifikovány možných ohrožení zabezpečení. Kliknutím na každé dlaždici se zobrazí seznam zabezpečení problémů zjištěných pomocí Security Center spolu s inventář prostředků ve vašem předplatném.

### <a name="what-triggers-a-security-alert"></a>Co se aktivuje upozornění na zabezpečení?
Azure Security Center automaticky shromažďuje, analyzuje a fuses data protokolu z vašich prostředků Azure, sítě a řešení partnerů, jako jsou antimalwarové zásady a brány firewall. Při zjištění ohrožení zabezpečení se vytvoří výstraha zabezpečení. Příklady zahrnují zjišťování následujících situací:

* Ohrožené virtuální počítače, které komunikují se známými IP adresami se zlými úmysly
* Pokročilý malware zjištěný pomocí zasílání zpráv o chybách Windows
* Útoky hrubou silou na virtuální počítače
* Výstrahy zabezpečení z integrovaných partnerských řešení zabezpečení, jako je Anti-Malware nebo brány firewall webových aplikací

### Proč se hodnoty se zabezpečenými výsledky změnily? <a name="secure-score-faq"></a>
Od února 2019 Security Center upraveno skóre několika doporučení, aby lépe vyhovoval jejich závažnosti. Výsledkem této úpravy můžou být změny v celkových hodnotách bezpečného skóre.  Další informace o zabezpečeném skóre najdete v tématu [Secure skore Calculation](security-center-secure-score.md).

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Jaký je rozdíl mezi hrozeb zjištěných a oznamovaných na podle Microsoft Security Response Center a Azure Security Center?
Microsoft Security Response Center (MSRC) provádí monitorování zabezpečení vyberte síť Azure a infrastrukturou a přijímá threat intelligence a zneužití stížností od třetích stran. Při MSRC zjistí, že zákaznická data přistupovala nezákonně nebo neoprávněně strany nebo, že využití Azure pro zákazníka není v souladu s podmínkami přijatelné použití, upozorní správce incidentu zabezpečení zákazníka. Oznámení se obvykle dochází, pošlete e-mail na kontakty zabezpečení zadané v Azure Security Center nebo vlastník předplatného Azure, pokud není zadán kontaktu zabezpečení.

Security Center je služba Azure nepřetržitě monitoruje prostředí Azure zákazníka a použije analytics mohla zjišťovat automaticky širokou škálu potenciálně škodlivé aktivity. Tyto detekce jsou prezentované jako výstrahy zabezpečení v řídicím panelu Security Center.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Které prostředky Azure jsou monitorovány pomocí služby Azure Security Center?
Azure Security Center monitoruje prostředky Azure následující:

* Virtuální počítače (VM) (včetně [Cloud Services](../cloud-services/cloud-services-choose-me.md))
* Virtual Machine Scale Sets
* Virtuální sítě Azure
* Služba Azure SQL
* Účet služby Azure Storage
* Azure Web Apps (v [App Service Environment](../app-service/environment/intro.md))
* Partnerských řešení integrovaných ve vašem předplatném Azure, jako je například firewallu webových aplikací na virtuálních počítačích a ve službě App Service Environment

Kromě toho je možné monitorovat také počítače jiné než Azure (včetně místních) Azure Security Center (podporuje se počítače se [systémem Windows](./quick-onboard-windows-computer.md) i [Linux](./quick-onboard-linux-computer.md) ).

## <a name="virtual-machines"></a>Virtuální počítače
### <a name="what-types-of-virtual-machines-are-supported"></a>Jaké typy virtuálních počítačů jsou podporovány?
Monitorování a doporučení jsou k dispozici pro virtuální počítače (VM) vytvořené pomocí obou [klasické modely nasazení a modely nasazení Resource Manager](../azure-classic-rm.md).

Zobrazit [podporovaných platforem ve službě Azure Security Center](security-center-os-coverage.md) seznam podporovaných platforem.

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Proč Azure Security Center nerozpoznal antimalwarové řešení, které běží na mém virtuálním počítači Azure?
Azure Security Center obsahuje přehled antimalwarové nainstalované prostřednictvím rozšíření Azure. Například není Security Center schopna zjistit Antimalwarový program, který byl předem nainstalované na obrázku, který jste zadali, nebo pokud jste nainstalovali antimalwarové na vašich virtuálních počítačů pomocí vlastních procesů (jako jsou systémy pro správu konfigurace).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Proč se zobrazila zpráva "Chybí Data kontroly" pro můj virtuální počítač?
Tato zpráva se zobrazí, když nejsou žádná data kontroly pro virtuální počítač. Může trvat nějakou dobu (míň než hodinu) pro data kontroly k naplnění po povolení shromažďování dat ve službě Azure Security Center. Po počáteční počet obyvatel data kontroly může zobrazit tato zpráva vzhledem k tomu, že neexistuje žádná data kontroly vůbec nebo neexistuje žádná nová data kontroly. Prohledávání nenaplňuje pro virtuální počítač v zastaveném stavu. Tato zpráva se může zobrazit, pokud nebyla zadána data kontroly nedávno (v souladu se zásadami uchovávání informací pro agenta Windows, což je výchozí hodnota 30 dnů).

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Jak často Security Center zjišťovat existenci ohrožení zabezpečení operačního systému, aktualizací systému a problémy s endpoint protection?
Níže jsou uvedeny doby latence Security Center prověřování chyb zabezpečení, aktualizací a problémů:

- Konfigurace zabezpečení operačního systému – data se aktualizuje do 48 hodin
- Aktualizace systému – data se aktualizuje během 24 hodin
- Problémy s Endpoint Protection – data se aktualizují v 8 hodin

Security Center obvykle kontroluje dostupnost nových dat. každou hodinu a odpovídajícím způsobem aktualizuje doporučení. 

> [!NOTE]
> Security Center používá Microsoft Monitoring Agent ke shromažďování a ukládání dat. Další informace najdete v tématu [migrace platformy pro Azure Security Center](security-center-platform-migration.md).
>
>

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Proč se zobrazila zpráva "Agent virtuálního počítače je chybějící?"
Agent virtuálního počítače musí být nainstalovaný na virtuálních počítačích povolíte shromažďování dat. Agent virtuálního počítače je ve výchozím nastavení nainstalován na virtuálních počítačích nasazených z Azure Marketplace. Informace o tom, jak nainstalovat agenta virtuálního počítače na jiné virtuální počítače, naleznete v příspěvku blogu [agenta virtuálního počítače a rozšíření](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
