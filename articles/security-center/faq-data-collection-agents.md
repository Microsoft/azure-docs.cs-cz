---
title: Nejčastější dotazy centra zabezpečení Azure – shromažďování dat a agenti
description: Nejčastější dotazy týkající se shromažďování dat, agentů a pracovních prostorů pro Azure Security Center, produkt, který vám pomůže předcházet hrozbám, zjišťovat je a reagovat na ně.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245470"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>Časté otázky – otázky týkající se shromažďování dat, agentů a pracovních prostorů

Security Center shromažďuje data z vašich virtuálních počítačů (VM), škálovací sady virtuálních počítačů, kontejnery IaaS a počítačů jiných než Azure (včetně místních počítačů) za účelem monitorování slabých míst zabezpečení a hrozeb. Data se shromažďují pomocí agenta Microsoft Monitoring Agent, který z počítače načítá různé protokoly událostí a konfigurace související se zabezpečením a kopíruje data k analýze do vašeho pracovního prostoru.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Účtují se mi protokoly Azure Monitoru v pracovních prostorech vytvořených Centrem zabezpečení?

Ne. Pracovní prostory vytvořené Security Center, zatímco nakonfigurované pro protokoly Azure Monitor na uzel fakturace, nevznikají poplatky za protokoly Azure Monitor. Fakturace centra zabezpečení je vždy založena na zásadách zabezpečení Centra zabezpečení a řešenínainstalovaných v pracovním prostoru:

- **Bezplatná úroveň** – Security Center umožňuje řešení SecurityCenterFree ve výchozím pracovním prostoru. Úroveň Free se vám nebude účtovat.

- **Standardní úroveň** – Security Center umožňuje řešení zabezpečení ve výchozím pracovním prostoru.

Další informace o cenách naleznete v tématu [Security Center pricing](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Cenová úroveň analýzy protokolů pracovních prostorů vytvořených službou Security Center nemá vliv na fakturaci centra zabezpečení.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Co kvalifikuje virtuální hosti pro automatické zřizování instalace Microsoft Monitoring Agent?

Virtuální počítače Windows nebo Linux IaaS se kvalifikují, pokud:

- Rozšíření Microsoft Monitoring Agent není aktuálně nainstalován na virtuálním počítači.
- Virtuální ho virtuálního provozu je ve spuštěném stavu.
- Je nainstalován [Agent virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) Windows nebo Linux Azure.
- Virtuální počítače se nepoužívá jako zařízení, jako je brána firewall webové aplikace nebo brány firewall nové generace.


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Je možné odstranit výchozí pracovní prostory vytvořené centrem zabezpečení?

**Odstranění výchozího pracovního prostoru se nedoporučuje.** Security Center používá výchozí pracovní prostory k ukládání dat zabezpečení z virtuálních počítačů. Pokud odstraníte pracovní prostor, centrum zabezpečení nebude moci shromažďovat tato data a některá doporučení a výstrahy zabezpečení nebudou k dispozici.

Chcete-li obnovit, odeberte Microsoft Monitoring Agent na virtuálních počítačích připojených k odstraněné pracovní hostova. Security Center přeinstaluje agenta a vytvoří nové výchozí pracovní prostory.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Jak můžu použít svůj stávající pracovní prostor Log Analytics?

Můžete vybrat existující pracovní prostor Log Analytics pro ukládání dat shromážděných Centrem zabezpečení. Použití existujícího pracovního prostoru Analýzy protokolů:

- Pracovní prostor musí být přidružený k vybranému předplatnému Azure.
- Minimálně musíte mít oprávnění ke čtení pro přístup k pracovnímu prostoru.

Výběr existujícího pracovního prostoru Analýzy protokolů:

1. V části **Zásady zabezpečení – Shromažďování dat**vyberte Použít jiný pracovní **prostor**.

    ![Použití jiného pracovního prostoru][4]

1. V rozbalené nabídce vyberte pracovní prostor pro ukládání shromážděných dat.

    > [!NOTE]
    > V rozbalovací nabídce se zobrazí pouze pracovní prostory, ke kterým máte přístup a které jsou ve vašem předplatném Azure.

1. Vyberte **Uložit**. Budete dotázáni, zda chcete překonfigurovat monitorované virtuální počítače.

    - Pokud chcete, aby se nové nastavení pracovního prostoru **použilo jenom na nové virtuální počítače ,** vyberte **ne** . Nové nastavení pracovního prostoru platí pouze pro instalace nových agentů. nově zjištěné virtuální servery, které nemají nainstalován microsoft monitoring agenta.
    - Pokud chcete, aby se nové nastavení pracovního prostoru **použilo na všechny virtuální počítače**, vyberte **ano.** Kromě toho každý virtuální virtuální movitý virtuální movitý virtuální míchaný k pracovnímu prostoru vytvořenému centrem zabezpečení se navíc znovu připojí k novému cílovému pracovnímu prostoru.

    > [!NOTE]
    > Pokud vyberete **Ano**, neodstraňujte žádné pracovní prostory vytvořené Centrem zabezpečení, dokud nebudou všechny virtuální aplikace znovu připojeny k novému cílovému pracovnímu prostoru. Tato operace se nezdaří, pokud je pracovní prostor odstraněn příliš brzy.

    - Chcete-li operaci zrušit, vyberte **možnost Storno**.

## <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>Co když microsoft monitoring agent byl již nainstalován jako rozšíření na virtuálním počítači?<a name="mmaextensioninstalled"></a>

Pokud je agent monitorování nainstalován jako rozšíření, konfigurace rozšíření umožňuje vytváření sestav pouze do jednoho pracovního prostoru. Centrum zabezpečení nepřepisuje existující připojení k uživatelským pracovním prostorům. Security Center bude ukládat data zabezpečení z virtuálního počítači v pracovním prostoru, který je již připojen, za předpokladu, že "Zabezpečení" nebo "SecurityCenterFree" řešení bylo nainstalováno na něm. Security Center může upgradovat verzi rozšíření na nejnovější verzi v tomto procesu.

Další informace naleznete [v tématu Automatické zřizování v případě již existující instalace agenta](security-center-enable-data-collection.md#preexisting).



## <a name="what-if-a-microsoft-monitoring-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>Co když je agent monitorování společnosti Microsoft nainstalován přímo v počítači, ale ne jako rozšíření (přímý agent)?<a name="directagentinstalled"></a>

Pokud je agent monitorování Microsoftu nainstalovaný přímo na virtuálním počítači (nikoli jako rozšíření Azure), Centrum zabezpečení nainstaluje rozšíření Microsoft Monitoring Agent a může upgradovat agenta Microsoft Monitoring na nejnovější verzi.

Nainstalovaný agent bude nadále podávat zprávy do svého již nakonfigurovaného pracovního prostoru a navíc bude podávat zprávy do pracovního prostoru nakonfigurovaného v Centru zabezpečení (Multi-navádění je podporováno v počítačích se systémem Windows).

Pokud je nakonfigurovaný pracovní prostor uživatelský mj.

U počítačů s Linuxem agent multi-navádění ještě není podporováno – proto pokud je zjištěna instalace existujícího agenta, nedojde k automatickému zřizování a konfigurace počítače se nezmění.

Pro existující počítače na předplatným i na palubě Security Center před 17 března 2019, kdy bude zjištěna existující agent, rozšíření Microsoft Monitoring Agent nebude nainstalován a počítač nebude ovlivněna. Pro tyto počítače naleznete v tématu "Řešení problémů se stavem agenta monitorování na vašich počítačích" doporučení k vyřešení problémů s instalací agenta na těchto počítačích

Další informace najdete v další části [Co se stane, když je na mém virtuálním počítači už nainstalovaný správce operací system center nebo přímý agent OMS?](#scomomsinstalled)

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>Co když je agent nástroje Operations Manager system center už na mém virtuálním počítači?<a name="scomomsinstalled"></a>

Centrum zabezpečení nainstaluje rozšíření Microsoft Monitoring Agent vedle sebe do stávajícího agenta nástroje System Center Operations Manager. Stávající agent bude nadále hlásit na serveru System Center Operations Manager normálně. Všimněte si, že agent operations managera a microsoft monitoring agent sdílejí společné knihovny za běhu, které budou během tohoto procesu aktualizovány na nejnovější verzi. Poznámka : Pokud je nainstalována verze 2012 agenta operations manageru, nezapínejte automatické zřizování (možnosti správy mohou být ztraceny, když je server Operations Manager také verze 2012).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Jaký je dopad odstranění těchto rozšíření?

Pokud odeberete rozšíření Microsoft Monitoring Extension, Security Center není schopen shromažďovat data zabezpečení z virtuálního počítače a některá doporučení zabezpečení a výstrahy nejsou k dispozici. Do 24 hodin Security Center zjistí, že virtuálním kontům chybí rozšíření a přeinstaluje rozšíření.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Jak zastavím automatickou instalaci agenta a vytváření pracovních prostorů?

Automatické zřizování předplatných můžete vypnout v zásadách zabezpečení, ale to se nedoporučuje. Vypnutí automatického zřizování omezuje doporučení a výstrahy Centra zabezpečení. Zakázání automatického zřizování:

1. Pokud je vaše předplatné nakonfigurované pro úroveň Standard, otevřete zásady zabezpečení pro toto předplatné a vyberte úroveň **Free.**

   ![Cenová úroveň][1]

1. Dále vypněte automatické zřizování výběrem **možnosti Vypnuto** na **stránce Zásady zabezpečení – shromažďování dat.**
   ![Shromažďování dat][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Mám se odhlásit z automatické instalace agenta a vytváření pracovního prostoru?

> [!NOTE]
> Nezapomeňte zkontrolovat části [Jaké jsou důsledky odhlášení?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) a [doporučené kroky při odhlášení,](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) pokud se rozhodnete odhlásit automatické zřizování.

Automatické zřizování můžete chtít odhlásit, pokud se vás týkají následující:

- Automatická instalace agenta pomocí centra zabezpečení se vztahuje na celé předplatné. Automatickou instalaci nelze použít na podmnožinu virtuálních zařízení. Pokud existují kritické virtuální chodů, které nelze nainstalovat s Microsoft Monitoring Agent, pak byste měli odhlásit automatické zřizování.
- Instalace rozšíření Microsoft Monitoring Agent (MMA) aktualizuje verzi agenta. To platí pro přímého agenta a agenta nástroje Operations Manager system center (v druhém případě nástroj Operations Manager a MMA sdílejí společné runtime knihovny – které budou v procesu aktualizovány). Pokud je nainstalovaný agent operations manageru verze 2012 a je upgradován, mohou být možnosti správy ztraceny, když je server Operations Manager také verze 2012. Zvažte odhlášení automatickézřivací, pokud je nainstalovaný agent Operations Manager verze 2012.
- Pokud máte vlastní pracovní prostor mimo předplatné (centralizovaný pracovní prostor), měli byste se odhlásit z automatického zřizování. Rozšíření Microsoft Monitoring Agent můžete nainstalovat ručně a připojit jej do pracovního prostoru, aniž by centrum zabezpečení přepsání připojení.
- Pokud se chcete vyhnout vytváření více pracovních prostorů na jedno předplatné a máte vlastní pracovní prostor v rámci předplatného, pak máte dvě možnosti:

   1. Automatické zřizování se můžete odhlásit. Po migraci nastavte výchozí nastavení pracovního prostoru, jak je popsáno v [části Jak můžu použít stávající pracovní prostor Log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)

   1. Nebo můžete povolit dokončení migrace, Microsoft Monitoring Agent, který má být nainstalován na virtuálních počítačích a virtuálních počítačích připojených k vytvořenému pracovnímu prostoru. Potom vyberte vlastní pracovní prostor nastavením výchozího nastavení pracovního prostoru s přihlášením se ke konfiguraci již nainstalovaných agentů. Další informace najdete v tématu [Jak lze použít svůj stávající pracovní prostor Log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Jaké jsou důsledky odhlášení z automatického zřizování?

Po dokončení migrace centrum zabezpečení nemůže shromažďovat data zabezpečení z virtuálního počítače a některá doporučení zabezpečení a výstrahy nejsou k dispozici. Pokud se odhlásíte, nainstalujte agenta microsoft monitoringu ručně. Podívejte se [na doporučené kroky při odhlášení](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Jaké jsou doporučené kroky při odhlášení z automatického zřizování?

Ručně nainstalujte rozšíření Microsoft Monitoring Agent, aby centrum zabezpečení mohli shromažďovat data zabezpečení z vašich virtuálních počítačů a poskytovat doporučení a výstrahy. Pokyny k instalaci najdete v tématu [instalace agenta pro virtuální počítač se systémem Windows](../virtual-machines/extensions/oms-windows.md) nebo instalace [agenta pro virtuální počítač s Linuxem.](../virtual-machines/extensions/oms-linux.md)

Agenta můžete připojit k libovolnému existujícímu vlastnímu pracovnímu prostoru nebo k centru zabezpečení vytvořenému pracovnímu prostoru. Pokud vlastní pracovní prostor nemá povolená řešení Zabezpečení nebo SecurityCenterFree, budete muset použít řešení. Chcete-li použít, vyberte vlastní pracovní prostor nebo předplatné a použít cenovou úroveň prostřednictvím **zásad zabezpečení – cena vrstvy** stránky.

   ![Cenová úroveň][1]

Security Center umožní správné řešení v pracovním prostoru na základě vybrané cenové úrovně.


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Jak odeberu rozšíření OMS nainstalovaná centrem zabezpečení?<a name="remove-oms"></a>

Agenta monitorování společnosti Microsoft můžete odebrat ručně. To se nedoporučuje, protože omezuje doporučení a výstrahy Centra zabezpečení.

> [!NOTE]
> Pokud je povoleno shromažďování dat, Centrum zabezpečení přeinstaluje agenta po odebrání.  Před ručním odebráním agenta je třeba zakázat shromažďování dat. Přečtěte si, jak zastavím automatickou instalaci agenta a vytváření pracovních prostorů? pokyny pro zakázání shromažďování dat.

Ruční odebrání agenta:

1.  Na portálu otevřete **Log Analytics**.

1.  Na stránce Log Analytics vyberte pracovní prostor:

1.  Vyberte virtuální počítač, který nechcete monitorovat, a vyberte **Odpojit**.

   ![Odebrání agenta][3]

> [!NOTE]
> Pokud virtuální počítač s Linuxem už má agenta OMS bez rozšíření, odebrání rozšíření odebere agenta také a budete ho muset přeinstalovat.


## <a name="how-do-i-disable-data-collection"></a>Jak zakážu shromažďování dat?

Automatické zřizování je ve výchozím nastavení vypnuté. Automatické zřizování prostředků můžete kdykoli zakázat vypnutím tohoto nastavení v zásadách zabezpečení. Automatické zřizování se důrazně doporučuje získat výstrahy zabezpečení a doporučení týkající se aktualizací systému, chyb zabezpečení operačního systému a ochrany koncových bodů.

Pokud chcete zakázat shromažďování dat, [přihlaste se na portál Azure](https://portal.azure.com), vyberte **Procházet**, vyberte **Security Center**a **vyberte Vybrat zásadu**. Vyberte předplatné, pro které chcete vypnout automatické zřizování. Když vyberete **zásady zabezpečení** předplatného – otevře se shromažďování dat. V části **Automatické zřizování**vyberte **Vypnout**.


## <a name="how-do-i-enable-data-collection"></a>Jak povolím shromažďování dat?

Shromažďování dat pro vaše předplatné Azure můžete povolit v zásadách zabezpečení. Chcete-li povolit shromažďování dat. [Přihlaste se na portál Azure](https://portal.azure.com), vyberte **Procházet**, vyberte **Security Center**a vyberte **zásady zabezpečení**. Vyberte předplatné, které chcete povolit automatické zřizování. Když vyberete **zásady zabezpečení** předplatného – otevře se shromažďování dat. V části **Automatické zřizování**vyberte **Možnost Zapnuto**.


## <a name="what-happens-when-data-collection-is-enabled"></a>Co se stane, když je povoleno shromažďování dat?

Když je povoleno automatické zřizování, Security Center zřídí Microsoft Monitoring Agent na všech podporovaných virtuálních počítačích Azure a všechny nové, které jsou vytvořeny. Automatické zřizování se doporučuje, ale k dispozici je také ruční instalace agenta. [Naučte se nainstalovat rozšíření Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Agent umožňuje událost vytvoření procesu 4688 a *pole příkazové řádky* uvnitř události 4688. Nové procesy vytvořené na virtuálním počítači jsou zaznamenány eventlogem a monitorovány detekčními službami Security Center. Další informace o podrobnostech zaznamenaných pro každý nový proces naleznete [v polích popisu v části 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Agent také shromažďuje události 4688 vytvořené na virtuálním počítači a ukládá je do hledání.

Agent také umožňuje shromažďování dat pro [adaptivní ovládací prvky aplikací](security-center-adaptive-application.md), Security Center konfiguruje místní zásady AppLocker v režimu auditování povolit všechny aplikace. Tato zásada způsobí, že nástroj AppLocker vygeneruje události, které pak shromažďuje a využívá Centrum zabezpečení. Je důležité si uvědomit, že tato zásada nebude nakonfigurována na všech počítačích, na kterých již nakonfigurované zásady nástroje AppLocker. 

Když Security Center zjistí podezřelou aktivitu na virtuálním počítači, zákazník je upozorněn e-mailem, pokud byly poskytnuty [bezpečnostní kontaktní informace.](security-center-provide-security-contact-details.md) Výstraha je také viditelná na řídicím panelu výstrah zabezpečení Centra zabezpečení.


## <a name="will-security-center-work-using-an-oms-gateway"></a>Bude Centrum zabezpečení fungovat pomocí brány OMS?

Ano. Azure Security Center využívá Azure Monitor ke shromažďování dat z virtuálních počítačů a serverů Azure pomocí Agenta monitorování Microsoftu.
Chcete-li shromažďovat data, každý virtuální server a server se musí připojit k Internetu pomocí protokolu HTTPS. Připojení může být přímé, pomocí proxy serveru nebo prostřednictvím [brány OMS](../azure-monitor/platform/gateway.md).


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Má agent monitorování vliv na výkon mých serverů?

Agent spotřebovává nominální množství systémových prostředků a měl by mít malý vliv na výkon. Další informace o dopadu na výkon a agenta a rozšíření naleznete v [příručce pro plánování a operace](security-center-planning-and-operations-guide.md#data-collection-and-storage).


## <a name="where-is-my-data-stored"></a>Kde jsou moje data uložena?

Data shromážděná od tohoto agenta jsou uložena buď v existujícím pracovním prostoru Log Analytics přidruženém k vašemu předplatnému, nebo v novém pracovním prostoru. Další informace naleznete v [tématu Zabezpečení dat](security-center-data-security.md).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png