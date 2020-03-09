---
title: Nejčastější dotazy k Azure Security Center – shromažďování dat a agenti
description: Nejčastější dotazy týkající se shromažďování dat, agentů a pracovních prostorů pro Azure Security Center, produkt, který pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně
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
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 8317a13b9ef87679836f55627268deefa4500dce
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373031"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>Nejčastější dotazy týkající se shromažďování dat, agentů a pracovních prostorů

Security Center shromažďuje data z virtuálních počítačů Azure, sady škálování virtuálních počítačů, kontejnerů IaaS a počítačů mimo Azure (včetně místních počítačů), aby se mohla monitorovat ohrožení zabezpečení a hrozby. Data se shromažďují pomocí agenta Microsoft Monitoring Agent, který z počítače načítá různé protokoly událostí a konfigurace související se zabezpečením a kopíruje data k analýze do vašeho pracovního prostoru.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Účtují se mi Azure Monitor protokoly v pracovních prostorech vytvořených pomocí Security Center?

Ne. Pracovní prostory vytvořené pomocí Security Center, i když jsou nakonfigurované pro Azure Monitor protokolů za uzel, se neúčtují Azure Monitor poplatky. Fakturace centra zabezpečení je vždy na základě zásad zabezpečení Security Center a řešení nainstalované v pracovním prostoru:

- **Úroveň Free** – Security Center ve výchozím pracovním prostoru povolí řešení ' SecurityCenterFree '. Za bezplatnou úroveň se vám nebude účtovat.

- **Úroveň Standard** – Security Center ve výchozím pracovním prostoru povoluje řešení zabezpečení.

Další informace o cenách najdete v tématu [Security Center ceny](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Cenová úroveň Log Analytics pracovních prostorů vytvořených pomocí Security Center nemá vliv na Security Centerou fakturaci.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Co splňuje podmínky virtuální počítač pro automatické zřizování instalace agenta Microsoft Monitoring Agent?

Windows nebo virtuální počítače IaaS s Linuxem kvalifikovat, pokud:

- Rozšíření Microsoft Monitoring Agent není nainstalována na virtuálním počítači.
- Virtuální počítač je ve spuštěném stavu.
- Je nainstalovaný [Agent virtuálního počítače Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) se systémem Windows nebo Linux.
- Virtuální počítač se nepoužívá jako zařízení jako jsou brány firewall webových aplikací nebo brána firewall příští generace.


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Můžete odstranit výchozí pracovních prostorů vytvořených službou Security Center?

**Odstranění výchozího pracovního prostoru se nedoporučuje.** Security Center používá výchozí pracovní prostory k uložení dat zabezpečení z virtuálních počítačů. Při odstranění pracovního prostoru, Security Center nedokáže shromažďování těchto dat a některá doporučení zabezpečení a výstrahy nejsou k dispozici.

Pokud chcete obnovit, odeberte agenta Microsoft Monitoring Agent na virtuální počítače připojené k pracovním prostoru. Security Center opětovná instalace agenta a vytvoří nový výchozí pracovní prostory.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Použití mé existující pracovní prostor Log Analytics

Můžete vybrat existující pracovní prostor Log Analytics ukládat data shromážděná službou Security Center. Použití existující pracovní prostor Log Analytics:

- Pracovní prostor musí být přidružen vašem vybraném předplatném Azure.
- Minimálně musí mít čtení oprávnění pro přístup k pracovnímu prostoru.

Vyberte existující pracovní prostor Log Analytics:

1. V části **zásady zabezpečení – shromažďování dat**vyberte **použít jiný pracovní prostor**.

    ![Použít jiný pracovní prostor][4]

1. Z rozevírací nabídky vyberte pracovní prostor pro ukládání shromážděných dat.

    > [!NOTE]
    > V rozevírací nabídku o přijetí změn jsou zobrazeny pouze pracovní prostory, které mají přístup k a jsou ve vašem předplatném Azure.

1. Vyberte **Save** (Uložit). Zobrazí se dotaz, jestli chcete překonfigurovat monitorované virtuální počítače.

    - Tuto **možnost vyberte** , pokud chcete, aby se nové nastavení pracovního prostoru **projevilo jenom pro nové virtuální počítače**. Nový pracovní prostor nastavení platí pouze pro nové instalace agentů; nově zjištěných virtuálních počítačů, které nemají agenta Microsoft Monitoring Agent nainstalována.
    - Vyberte **Ano** , pokud chcete, aby se nové nastavení pracovního prostoru **projevilo na všech virtuálních počítačích**. Kromě toho každý virtuální počítač připojený ke službě Security Center vytvořit pracovní prostor znovu nepřipojí do nového cílového pracovního prostoru.

    > [!NOTE]
    > Pokud vyberete **Ano**, neodstraňujte žádné pracovní prostory vytvořené pomocí Security Center, dokud se všechny virtuální počítače znovu nepřipojí k novému cílovému pracovnímu prostoru. Tato operace selže, pokud pracovní prostor je příliš brzké odstranit.

    - Chcete-li operaci zrušit, vyberte možnost **Zrušit**.

## Co když už je Microsoft Monitoring Agent na virtuálním počítači nainstalovaná jako rozšíření?<a name="mmaextensioninstalled"></a>

Pokud je agent monitorování nainstalován jako rozšíření, konfigurace rozšíření umožňuje vytváření sestav pouze do jednoho pracovního prostoru. Security Center nemůže přepsat existující připojení k pracovním prostorům uživatele. Security Center bude ukládat data zabezpečení z virtuálního počítače v pracovním prostoru, který je už připojený, za předpokladu, že je na něm nainstalované řešení Security nebo SecurityCenterFree. Security Center může v tomto procesu upgradovat verzi rozšíření na nejnovější verzi.

Další informace najdete v tématu [Automatické zřizování v případě již existující instalace agenta](security-center-enable-data-collection.md#preexisting).



## Co když je Microsoft Monitoring Agent přímo nainstalovaný na počítači, ale ne jako rozšíření (přímý Agent)?<a name="directagentinstalled"></a>

Pokud je Microsoft Monitoring Agent nainstalovaná přímo na virtuálním počítači (ne jako rozšíření Azure), Security Center nainstaluje rozšíření Microsoft Monitoring Agent a může upgradovat agenta Microsoft Monitoring Agent na nejnovější verzi.

Nainstalovaný Agent bude pokračovat v hlášení do již nakonfigurovaných pracovních prostorů a kromě toho bude hlásit do pracovního prostoru nakonfigurovaného v Security Center (u počítačů s Windows je podporováno více domovských stránek).

Pokud je nakonfigurovaným pracovním prostorem pracovní prostor uživatele (ve výchozím nastavení není Security Center), bude nutné do něj nainstalovat řešení "Security/" SecurityCenterFree ", aby bylo možné Security Center zahájit zpracování událostí z virtuálních počítačů a počítačů, které do daného pracovního prostoru hlásí.

Pro počítače se systémem Linux zatím není podporována podpora více domovských stránek agenta, takže pokud je zjištěna existující instalace agenta, Automatické zřizování nebude provedeno a konfigurace počítače nebude změněna.

Pro existující počítače v předplatných, která se zaregistrují do Security Center do března 17 2019, se při zjištění existujícího agenta Microsoft Monitoring Agent rozšíření nenainstaluje a počítač nebude ovlivněn. Pro tyto počítače si přečtěte doporučení "vyřešit problémy se stavem agenta monitorování na vašich počítačích" a vyřešte problémy s instalací agenta na těchto počítačích.

Další informace najdete v další části, [co se stane, když je na mém virtuálním počítači už nainstalovaný System Center Operations Manager nebo přímý agent OMS](#scomomsinstalled) .

## Co když je na mém virtuálním počítači už nainstalovaný Agent System Center Operations Manager?<a name="scomomsinstalled"></a>

Security Center nainstaluje rozšíření Microsoft Monitoring Agent vedle sebe stávajícímu agentu System Center Operations Manager. Stávající Agent bude nadále hlásit Server System Center Operations Manager normálně. Všimněte si, že agent Operations Manager a Microsoft Monitoring Agent sdílet běžné běhové knihovny, které budou během tohoto procesu aktualizovány na nejnovější verzi. Poznámka: Pokud je nainstalovaná verze 2012 agenta Operations Manager, nepovolujte Automatické zřizování (možnosti spravovatelnosti se můžou ztratit, pokud je Operations Manager Server verze 2012).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Co je dopady odebrání těchto rozšíření?

Pokud odeberete rozšíření Microsoft Monitoring, Security Center nedokáže ke shromažďování dat zabezpečení z virtuálního počítače a několik doporučení zabezpečení a výstrahy nejsou k dispozici. Security Center během 24 hodin, určuje chybí rozšíření a znovu nainstaluje rozšíření virtuálního počítače.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Jak můžu zastavit instalaci a pracovní prostor vytváření agentů pro automatickou diagnostiku?

Můžete ji vypnout automatické zřizování pro vaše předplatná v zásadě zabezpečení ale to se nedoporučuje. Vypnutí výstrahy a automatické zřizování omezení doporučení služby Security Center. Chcete-li zakázat automatické zřizování:

1. Pokud je vaše předplatné nakonfigurované pro úroveň Standard, otevřete pro toto předplatné zásadu zabezpečení a vyberte úroveň **Free** .

   ![Cenová úroveň][1]

1. V dalším kroku vypnout automatické zřizování výběrem možnosti **vypnuto** na stránce **zásady zabezpečení – shromažďování dat** .
   ![Shromažďování dat][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>By měl jsem vyjádřit výslovný nesouhlas automatickou instalaci agenta a vytváření pracovního prostoru?

> [!NOTE]
> Nezapomeňte si projít oddíly, [které mají vliv na vypnutí?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) a [Doporučené kroky](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) , pokud se rozhodnete, že se chcete odhlásit z automatického zřizování.

Můžete se odhlásit ze automatické zřizování, pokud pro vás platí následující:

- Automatickou instalaci agenta pomocí služby Security Center platí pro celé předplatné. Automatická instalace nelze použít pro některé virtuální počítače řady. Pokud existují kritických virtuálních počítačů, které nejde nainstalovat pomocí agenta Microsoft Monitoring Agent, pak by měl zakážete automatické zřizování.
- Instalace rozšíření Microsoft Monitoring Agent (MMA) aktualizuje verzi agenta. To platí pro přímého agenta a System Center Operations Managerho agenta (v druhé Operations Manager a MMA sdílet společné běhové knihovny, které se budou aktualizovat v procesu). Pokud je nainstalovaný agent Operations Manager verze 2012 a upgradovaný, můžou být možnosti spravovatelnosti ztraceny, pokud je Operations Manager Server také verze 2012. Pokud je nainstalovaný agent Operations Manager verze 2012, zvažte Automatické zřizování.
- Pokud máte vlastní pracovní prostor mimo předplatné (centralizovaný pracovní prostor), měli byste se odhlásit z automatického zřizování. Můžete ručně nainstalovat rozšíření Microsoft Monitoring Agent a připojit ho váš pracovní prostor bez Security Center přepsání připojení.
- Pokud chcete se vyhnout vytváření více pracovních prostorů na předplatné a máte vlastní vlastní pracovní prostor v rámci předplatného, máte dvě možnosti:

   1. Můžete se rozhodnout automatické zřizování. Po dokončení migrace nastavte výchozí nastavení pracovního prostoru, jak je popsáno v tématu [Jak můžu použít můj stávající pracovní prostor Log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)

   1. Nebo můžete povolit migraci tak, aby bylo možné provést, agenta Microsoft Monitoring Agent nainstalovaný na virtuálních počítačích, a virtuální počítače připojené do vytvořeného pracovního prostoru. Vyberte vlastní vlastní pracovní prostor tak, že nastavíte výchozí nastavení pracovního prostoru s vyjádření výslovného souhlasu s opětovná konfigurace již nainstalovaní agenti. Další informace najdete v tématu [Jak můžu použít stávající pracovní prostor Log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Jaké jsou důsledky přestanete používat automatické zřizování?

Po dokončení migrace Security Center nemůžou shromažďovat data zabezpečení z virtuálního počítače a některá doporučení a upozornění zabezpečení nejsou k dispozici. Pokud se odhlásíte odhlásit, nainstalujte Microsoft Monitoring Agent ručně. Přečtěte si téma [Doporučené kroky při](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)odsouhlasení.


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Co když přestanete používat automatické zřizování jsou doporučené kroky?

Ručně nainstalujte Microsoft Monitoring Agent rozšíření tak, aby Security Center mohl shromažďovat data zabezpečení z vašich virtuálních počítačů a poskytovat doporučení a výstrahy. Pokyny k instalaci najdete v tématu [instalace agenta pro virtuální počítač s Windows](../virtual-machines/extensions/oms-windows.md) nebo [instalace agenta pro virtuální počítač Linux](../virtual-machines/extensions/oms-linux.md) .

Můžete připojit agenta k jakékoli existující vlastní pracovní prostor nebo pracovní prostor vytvořený Security Center. Pokud vlastní pracovní prostor nemá "Zabezpečení" nebo "SecurityCenterFree" řešení povolené, je potřeba použít řešení. Pokud ho chcete použít, vyberte vlastní pracovní prostor nebo předplatné a pomocí stránky **zásady zabezpečení – cenová** úroveň použijte cenovou úroveň.

   ![Cenová úroveň][1]

Security Center vám umožní správným řešením v pracovním prostoru podle vybrané cenové úrovně.


## Návody odebrat rozšíření OMS nainstalovaná pomocí Security Center?<a name="remove-oms"></a>

Můžete ručně odebrat agenta Microsoft Monitoring Agent. Toto nastavení nedoporučujeme, protože se limity výstrahy a doporučení služby Security Center.

> [!NOTE]
> Pokud je povolené shromažďování dat, Security Center bude po odebrání již přeinstalujte agenta.  Je nutné zakázat shromažďování dat před ruční odebrání agenta. Přečtěte si téma Návody zastavení automatické instalace agenta a vytvoření pracovního prostoru? pokyny k zakázání shromažďování dat.

Chcete-li ručně odebrat agenta:

1.  Na portálu otevřete **Log Analytics**.

1.  Na stránce Log Analytics vyberte pracovní prostor:

1.  Vyberte virtuální počítače, které nechcete monitorovat, a vyberte **Odpojit**.

   ![Pokud chcete agenta odebrat][3]

> [!NOTE]
> Pokud již virtuální počítač se systémem Linux má agenta OMS bez přípony, odebrání rozšíření odebere i agenta a bude nutné ho znovu nainstalovat.


## <a name="how-do-i-disable-data-collection"></a>Jak zakázat shromažďování dat?

Automatické zřizování je ve výchozím nastavení vypnuté. Můžete vypnout automatické zřizování z prostředků v každém okamžiku vypnutím toto nastavení v zásadách zabezpečení. Automatické zřizování se důrazně doporučuje, aby se získaly výstrahy zabezpečení a doporučení týkající se aktualizací systému, chyb zabezpečení operačního systému a ochrany koncových bodů.

Chcete-li shromažďování dat zakázat, [Přihlaste se k Azure Portal](https://portal.azure.com), vyberte **procházet**, vyberte **Security Center**a vyberte **možnost vybrat zásadu**. Vyberte předplatné, pro které chcete vypnout automatické zřizování. Když vyberete zásadu zabezpečení předplatného, otevře se **kolekce dat** . V části **Automatické zřizování**vyberte **vypnuto**.


## <a name="how-do-i-enable-data-collection"></a>Jak povolit shromažďování dat?

Shromažďování dat můžete povolit u předplatného Azure v zásadě zabezpečení. Chcete-li povolit shromažďování dat. [Přihlaste se k Azure Portal](https://portal.azure.com), vyberte **procházet**, vyberte **Security Center**a vyberte **zásady zabezpečení**. Vyberte předplatné, pro kterého chcete povolit automatické zřizování. Když vyberete zásadu zabezpečení předplatného, otevře se **kolekce dat** . V části **Automatické zřizování**vyberte **zapnuto**.


## <a name="what-happens-when-data-collection-is-enabled"></a>Co se stane, když je povolené shromažďování dat?

Pokud je povoleno automatické zřizování, Security Center zřídí agenta Microsoft Monitoring Agent na všech podporovaných virtuálních počítačích Azure a nových, které jsou vytvořeny. Automatické zřizování se doporučuje, ale k dispozici je také ruční instalace agenta. [Naučte se nainstalovat rozšíření Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Agent umožňuje událost vytvoření procesu 4688 a pole *CommandLine* v události 4688. Nových procesů vytvořených ve virtuálním počítači se zaznamenávají v protokolu událostí a monitoruje detekce služby Security Center. Další informace o podrobnostech zaznamenaných pro každý nový proces najdete v tématu [Popis polí v 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Agent také 4688 událostí vytvořených ve virtuálním počítači shromáždí a uloží je v hledání.

Agent taky povoluje shromažďování dat pro [Adaptivní řízení aplikací](security-center-adaptive-application.md), Security Center v režimu auditování konfiguruje místní zásadu AppLockeru, aby povolovala všechny aplikace. Tato zásada způsobí, že AppLocker generuje události, které se pak shromažďují a využívají Security Center. Je důležité si uvědomit, že tyto zásady nenakonfigurují v počítačích, na kterých je už nakonfigurovaná zásada nástroje AppLocker. 

Když Security Center detekuje podezřelou aktivitu na virtuálním počítači, zákazník obdrží oznámení e-mailem, pokud byly poskytnuty [informace o kontaktu se zabezpečením](security-center-provide-security-contact-details.md) . Výstraha je také viditelné v řídicím panelu Security Center security výstrahy.


## <a name="will-security-center-work-using-an-oms-gateway"></a>Bude Security Center pracovat s bránou OMS?

Ano. Azure Security Center využívá Azure Monitor ke shromažďování dat z virtuálních počítačů a serverů Azure pomocí Microsoft Monitoring Agent.
Ke shromáždění dat se musí každý virtuální počítač a Server připojit k Internetu pomocí protokolu HTTPS. Připojení může být přímo, pomocí proxy serveru nebo pomocí [brány OMS](../azure-monitor/platform/gateway.md).


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Monitoring Agent vliv na výkon své servery?

Agent používá nominální množství systémových prostředků a mělo mít minimální vliv na výkon. Další informace o dopadu na výkon a o agentovi a rozšíření najdete v [Průvodci plánováním a provozem](security-center-planning-and-operations-guide.md#data-collection-and-storage).


## <a name="where-is-my-data-stored"></a>Kde jsou moje data uložená?

Data shromážděná z tohoto agenta se ukládají do existujícího pracovního prostoru Log Analytics přidružených k vašemu předplatnému nebo vytvořte nový. Další informace najdete v tématu [zabezpečení dat](security-center-data-security.md).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png