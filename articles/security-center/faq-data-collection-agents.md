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
ms.date: 11/15/2020
ms.author: memildin
ms.openlocfilehash: 2ea9fdcb11bd88755c0972fa166d1d94068ce60e
ms.sourcegitcommit: 18046170f21fa1e569a3be75267e791ca9eb67d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2020
ms.locfileid: "94638788"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>Nejčastější dotazy týkající se shromažďování dat, agentů a pracovních prostorů

Security Center shromažďuje data z virtuálních počítačů Azure, sady škálování virtuálních počítačů, kontejnerů IaaS a počítačů mimo Azure (včetně místních počítačů), aby se mohla monitorovat ohrožení zabezpečení a hrozby. Data se shromažďují pomocí Log Analytics agenta, který čte různé konfigurace a protokoly událostí související se zabezpečením z počítače a kopíruje data do pracovního prostoru pro účely analýzy.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Účtují se mi Azure Monitor protokoly v pracovních prostorech vytvořených pomocí Security Center?

Ne. Pracovní prostory vytvořené pomocí Security Center, i když jsou nakonfigurované pro Azure Monitor protokolů za uzel, se neúčtují Azure Monitor poplatky. Security Center fakturace je vždycky založená na vašich Security Center zásadách zabezpečení a řešeních, která jsou nainstalovaná v pracovním prostoru:

- **Azure Defender off** – Security Center ve výchozím pracovním prostoru povolí řešení ' SecurityCenterFree '. Pokud je Azure Defender vypnutý, nebude se vám účtovat.

- **Azure Defender on** – Security Center povoluje řešení zabezpečení ve výchozím pracovním prostoru.

Další informace o cenách najdete v tématu [Security Center ceny](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Cenová úroveň Log Analytics pracovních prostorů vytvořených pomocí Security Center nemá vliv na Security Centerou fakturaci.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-is-the-log-analytics-agent"></a>Co je agent Log Analytics?

Pokud chcete monitorovat ohrožení zabezpečení a hrozby, Azure Security Center závisí na [agentu Log Analytics](../azure-monitor/platform/log-analytics-agent.md) – to je stejný agent, kterého používá služba Azure monitor. 

Agent se někdy označuje jako Microsoft Monitoring Agent (nebo "MMA"). 

Agent shromažďuje různé podrobnosti konfigurace a protokoly událostí související se zabezpečením z připojených počítačů a pak zkopíruje data do vašeho pracovního prostoru Log Analytics pro další analýzu. Příklady takových dat: typ a verze operačního systému, protokoly operačního systému (protokoly událostí systému Windows), spuštěné procesy, název počítače, IP adresy a přihlášený uživatel.

Zajistěte, aby počítače používaly jeden z podporovaných operačních systémů pro agenta, jak je popsáno na následujících stránkách:

* [Agent Log Analytics pro podporované operační systémy Windows](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

* [Agent Log Analytics pro podporované operační systémy Linux](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

Přečtěte si další informace o [datech shromažďovaných agentem Log Analytics](security-center-enable-data-collection.md).




## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-log-analytics-agent-installation"></a>Jaké jsou virtuální počítače pro Automatické zřizování instalace agenta Log Analytics?

Virtuální počítače s Windows nebo Linux IaaS mají nárok v těchto případech:

- Na virtuálním počítači není aktuálně nainstalované rozšíření agenta Log Analytics.
- Virtuální počítač je ve spuštěném stavu.
- Je nainstalovaný [Agent virtuálního počítače Azure](../virtual-machines/extensions/agent-windows.md) se systémem Windows nebo Linux.
- Virtuální počítač se nepoužívá jako zařízení, jako je třeba brána firewall webových aplikací nebo brána firewall nové generace.


## <a name="where-is-the-default-log-analytics-workspace-created"></a>Kde je vytvořen výchozí pracovní prostor Log Analytics?

Umístění výchozího pracovního prostoru závisí na vaší oblasti Azure:

- Pro virtuální počítače v USA a Brazílie je umístění pracovního prostoru USA
- Pro virtuální počítače v Kanadě je umístění pracovního prostoru Kanada.
- Pro virtuální počítače v Evropě je umístění pracovního prostoru Evropa.
- V případě virtuálních počítačů ve Spojeném království je umístění pracovního prostoru Velká Británie.
- Pro virtuální počítače v Východní Asie a jihovýchodní Asie je umístění pracovního prostoru Asie.
- V případě virtuálních počítačů v Koreji je umístění pracovního prostoru Korea.
- Pro virtuální počítače v Indii je umístění pracovního prostoru Indie.
- Pro virtuální počítače v Japonsku je umístění pracovního prostoru Japonsko.
- Pro virtuální počítače v Číně je umístění pracovního prostoru Čína.
- Pro virtuální počítače v Austrálii je umístění pracovního prostoru Austrálie.


## <a name="what-data-is-collected-by-the-log-analytics-agent"></a>Jaká data shromažďuje agent Log Analytics?

Úplný seznam aplikací a služeb monitorovaných agentem najdete v tématu [co je monitorované pomocí Azure monitor?](../azure-monitor/monitor-reference.md#azure-services).

> [!IMPORTANT]
> Pamatujte na to, že u některých služeb, jako je například Azure Firewall, pokud jste povolili protokolování a vybrali prostředek konverzace, které se mají protokolovat (například nastavením protokolu na *verbose* ), může se stát, že se v Log Analytics potřebuje úložiště pracovních prostorů významné dopady. 


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Můžu odstranit výchozí pracovní prostory vytvořené pomocí Security Center?

**Odstranění výchozího pracovního prostoru se nedoporučuje.** Security Center používá výchozí pracovní prostory k ukládání dat zabezpečení z vašich virtuálních počítačů. Pokud pracovní prostor odstraníte, Security Center není schopen tato data shromáždit a některá doporučení a upozornění zabezpečení nejsou k dispozici.

Chcete-li provést obnovení, odeberte agenta Log Analytics na virtuálních počítačích, které jsou připojeny k odstraněnému pracovnímu prostoru. Security Center znovu nainstaluje agenta a vytvoří nové výchozí pracovní prostory.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Jak můžu použít stávající pracovní prostor Log Analytics?

Můžete vybrat existující pracovní prostor Log Analytics pro ukládání dat shromažďovaných pomocí Security Center. Chcete-li použít stávající pracovní prostor Log Analytics:

- Pracovní prostor musí být přidružený k vybranému předplatnému Azure.
- Aby bylo možné získat přístup k pracovnímu prostoru, musíte mít oprávnění ke čtení.

Vyberte existující pracovní prostor Log Analytics:

1. V nabídce Security Center vyberte **cenové & nastavení**.
1. Vyberte příslušné předplatné.
1. Otevřít stránku **automatického zřizování** , s
1. U Log Analytics agenta vyberte **Upravit konfiguraci**. 

    :::image type="content" source="./media/security-center-enable-data-collection/edit-configuration-auto-deploy-agent.png" alt-text="Konfigurace agenta Log Analytics, který se má použít při použití automatického nasazení" lightbox="./media/security-center-enable-data-collection/edit-configuration-auto-deploy-agent.png":::

1. Vyberte **připojit virtuální počítače Azure k jinému pracovnímu prostoru** a vyberte svůj stávající pracovní prostor.

    :::image type="content" source="./media/security-center-enable-data-collection/choose-workspace.png" alt-text="Výběr jiného než výchozího pracovního prostoru, do kterého má agent Log Analytics nahlásit" lightbox="./media/security-center-enable-data-collection/choose-workspace.png":::

    > [!TIP]
    > Seznam obsahuje pouze pracovní prostory, ke kterým máte přístup a které jsou ve vašem předplatném Azure.

1. Vyberte **Uložit**. Zobrazí se dotaz, jestli chcete překonfigurovat monitorované virtuální počítače.

    - Tuto **možnost vyberte** , pokud chcete, aby se nové nastavení pracovního prostoru **projevilo jenom pro nové virtuální počítače**. Nové nastavení pracovního prostoru platí jenom pro nové instalace agenta. nově zjištěné virtuální počítače, které nemají nainstalovaného agenta Log Analytics.
    - Vyberte **Ano** , pokud chcete, aby se nové nastavení pracovního prostoru **projevilo na všech virtuálních počítačích**. Kromě toho se každý virtuální počítač připojený k Security Center vytvořenému pracovnímu prostoru znovu připojí k novému cílovému pracovnímu prostoru.

    > [!NOTE]
    > Pokud vyberete **Ano** , neodstraňujte žádné pracovní prostory vytvořené pomocí Security Center, dokud se všechny virtuální počítače znovu nepřipojí k novému cílovému pracovnímu prostoru. Tato operace se nezdařila, pokud je pracovní prostor odstraněn příliš brzy.


## <a name="what-if-the-log-analytics-agent-was-already-installed-as-an-extension-on-the-vm"></a>Co když už je agent Log Analytics na virtuálním počítači nainstalovaný jako rozšíření?<a name="mmaextensioninstalled"></a>

Pokud je agent monitorování nainstalován jako rozšíření, konfigurace rozšíření umožňuje vytváření sestav pouze do jednoho pracovního prostoru. Security Center nepřepisují existující připojení k pracovním prostorům uživatelů. Security Center bude ukládat data zabezpečení z virtuálního počítače v pracovním prostoru, který je už připojený, za předpokladu, že je na něm nainstalované řešení Security nebo SecurityCenterFree. Security Center může v tomto procesu upgradovat verzi rozšíření na nejnovější verzi.

Další informace najdete v tématu [Automatické zřizování v případě již existující instalace agenta](security-center-enable-data-collection.md#preexisting).



## <a name="what-if-a-log-analytics-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>Co když je agent Log Analytics přímo nainstalovaný na počítači, ale ne jako rozšíření (přímý Agent)?<a name="directagentinstalled"></a>

Pokud je agent Log Analytics nainstalovaný přímo na virtuálním počítači (ne jako rozšíření Azure), Security Center nainstaluje rozšíření agenta Log Analytics a může upgradovat agenta Log Analytics na nejnovější verzi.

Nainstalovaný Agent bude pokračovat v hlášení do již nakonfigurovaných pracovních prostorů a kromě toho bude hlásit do pracovního prostoru nakonfigurovaného v Security Center (u počítačů s Windows je podporováno více domovských stránek).

Pokud je nakonfigurovaným pracovním prostorem pracovní prostor uživatele (ve výchozím nastavení není Security Center), bude nutné do něj nainstalovat řešení "Security/" SecurityCenterFree ", aby bylo možné Security Center zahájit zpracování událostí z virtuálních počítačů a počítačů, které do daného pracovního prostoru hlásí.

Pro počítače se systémem Linux zatím není podporována podpora více domovských stránek agenta, takže pokud je zjištěna existující instalace agenta, Automatické zřizování nebude provedeno a konfigurace počítače nebude změněna.

Pro existující počítače v předplatných, která se zaregistrují do Security Center do března 17 2019, když se zjistí existující agent, nebude rozšíření agenta Log Analytics nainstalované a počítač nebude ovlivněn. Pro tyto počítače si přečtěte doporučení "vyřešit problémy se stavem agenta monitorování na vašich počítačích" a vyřešte problémy s instalací agenta na těchto počítačích.

Další informace najdete v další části, [co se stane, když je na mém virtuálním počítači už nainstalovaný System Center Operations Manager nebo přímý agent OMS](#scomomsinstalled) .

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>Co když je na mém virtuálním počítači už nainstalovaný Agent System Center Operations Manager?<a name="scomomsinstalled"></a>

Služba Security Center nainstaluje rozšíření agenta Log Analytics na straně stávajícího agenta System Center Operations Manager. Stávající Agent bude nadále hlásit Server System Center Operations Manager normálně. Všimněte si, že agent Operations Manager a Agent Log Analytics sdílejí běžné běhové knihovny, které se během tohoto procesu aktualizují na nejnovější verzi. Poznámka: Pokud je nainstalovaná verze 2012 agenta Operations Manager, nepovolujte Automatické zřizování (možnosti spravovatelnosti se můžou ztratit, pokud je Operations Manager Server verze 2012).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Jaký je dopad odebrání těchto rozšíření?

Pokud odeberete rozšíření Microsoft Monitoring, Security Center nebude moct shromažďovat data zabezpečení z virtuálního počítače a některá doporučení a upozornění zabezpečení nejsou k dispozici. Do 24 hodin Security Center zjistí, že virtuální počítač postrádá rozšíření a znovu nainstaluje rozšíření.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Návody zastavit automatickou instalaci agenta a vytvoření pracovního prostoru?

Automatické zřizování pro vaše předplatná můžete vypnout v zásadách zabezpečení, ale nedoporučuje se to. Vypnutí automatických omezení zřizování Security Center doporučení a výstrah. Zakázání automatického zřizování:

1. V nabídce Security Center vyberte **cenové & nastavení**.
1. Vyberte příslušné předplatné.
1. Pokud má vaše předplatné povolený program Azure Defender, otevřete **plány Azure Defenderu** a vyberte **Azure Defender vypnuto**.

    :::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="Povolit nebo zakázat Azure Defender":::

1. Na stránce **Automatické zřizování** vyberte možnost pero a vypněte Automatické zřizování na stránce  **zásady zabezpečení – shromažďování dat** .

    :::image type="content" source="./media/security-center-enable-data-collection/agent-toggles.png" alt-text="Povolit automatické nasazení pro agenta Log Analytics":::

1. Vyberte **Uložit**.


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Doporučuje se odhlásit z automatické instalace agenta a vytvoření pracovního prostoru?

> [!NOTE]
> Nezapomeňte si projít oddíly, [které mají vliv na vypnutí?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) a [Doporučené kroky](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) , pokud se rozhodnete, že se chcete odhlásit z automatického zřizování.

Automatické zřizování můžete odhlásit, pokud platí následující:

- Automatická instalace agenta podle Security Center platí pro celé předplatné. Nemůžete použít automatickou instalaci na podmnožinu virtuálních počítačů. Pokud jsou k dispozici kritické virtuální počítače, které nelze nainstalovat pomocí agenta Log Analytics, měli byste se odhlásit z automatického zřizování.
- Instalace rozšíření agenta Log Analytics aktualizuje verzi agenta. To platí pro přímého agenta a agenta System Center Operations Manager (v druhém případě agenti Operations Manager a Log Analytics sdílejí společné běhové knihovny, které se budou aktualizovat v procesu). Pokud je nainstalovaný agent Operations Manager verze 2012 a upgradovaný, můžou být možnosti spravovatelnosti ztraceny, pokud je Operations Manager Server také verze 2012. Pokud je nainstalovaný agent Operations Manager verze 2012, zvažte Automatické zřizování.
- Pokud máte vlastní pracovní prostor mimo předplatné (centralizovaný pracovní prostor), měli byste se odhlásit z automatického zřizování. Můžete ručně nainstalovat rozšíření agenta Log Analytics a připojit ho k pracovnímu prostoru bez Security Center přepsání připojení.
- Pokud se chcete vyhnout vytváření několika pracovních prostorů na jedno předplatné a máte vlastní pracovní prostor v rámci předplatného, máte dvě možnosti:

   1. Automatické zřizování se můžete odhlásit. Po dokončení migrace nastavte výchozí nastavení pracovního prostoru, jak je popsáno v tématu [Jak můžu použít můj stávající pracovní prostor Log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)

   1. Nebo můžete nechat migraci dokončit, Log Analytics agenta nainstalovat na virtuální počítače a virtuální počítače připojené k vytvořenému pracovnímu prostoru. Pak vyberte vlastní pracovní prostor nastavením výchozí nastavení pracovního prostoru s možnostmi přidružit se k překonfigurování již nainstalovaných agentů. Další informace najdete v tématu [Jak můžu použít stávající pracovní prostor Log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Jaké jsou důsledky vypnutí automatického zřizování?

Po dokončení migrace Security Center nemůžou shromažďovat data zabezpečení z virtuálního počítače a některá doporučení a upozornění zabezpečení nejsou k dispozici. Pokud se odhlásíte k odhlášení, nainstalujte agenta Log Analytics ručně. Přečtěte si téma [Doporučené kroky při](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)odsouhlasení.


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Jaké jsou doporučené kroky při vypnutí automatického zřizování?

Ručně nainstalujte rozšíření agenta Log Analytics, aby Security Center mohl shromažďovat data zabezpečení z vašich virtuálních počítačů a poskytovat doporučení a výstrahy. Pokyny k instalaci najdete v tématu [instalace agenta pro virtuální počítač s Windows](../virtual-machines/extensions/oms-windows.md) nebo [instalace agenta pro virtuální počítač Linux](../virtual-machines/extensions/oms-linux.md) .

Agenta můžete připojit k jakémukoli existujícímu vlastnímu pracovnímu prostoru nebo Security Center vytvořenému pracovnímu prostoru. Pokud vlastní pracovní prostor nemá povolená řešení Security nebo SecurityCenterFree, budete muset použít řešení. Pokud ho chcete použít, vyberte vlastní pracovní prostor nebo předplatné a pomocí stránky **zásady zabezpečení – cenová** úroveň použijte cenovou úroveň.

:::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="Povolit nebo zakázat Azure Defender":::

Security Center umožní správné řešení v pracovním prostoru na základě vybrané cenové úrovně.


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Návody odebrat rozšíření OMS nainstalovaná pomocí Security Center?<a name="remove-oms"></a>

Agenta Log Analytics můžete odebrat ručně. Nedoporučuje se to, protože omezuje Security Center doporučení a upozornění.

> [!NOTE]
> Pokud je povolené shromažďování dat, Security Center po jeho odebrání agenta znovu nainstaluje.  Shromažďování dat je nutné před ručním odebráním agenta zakázat. Přečtěte si téma Návody zastavení automatické instalace agenta a vytvoření pracovního prostoru? pokyny k zakázání shromažďování dat.

Ruční odebrání agenta:

1.    Na portálu otevřete **Log Analytics**.

1.    Na stránce Log Analytics vyberte pracovní prostor:

1.    Vyberte virtuální počítače, které nechcete monitorovat, a vyberte **Odpojit**.

   ![Odebrat agenta][3]

> [!NOTE]
> Pokud již virtuální počítač se systémem Linux má agenta OMS bez přípony, odebrání rozšíření odebere i agenta a bude nutné ho znovu nainstalovat.


## <a name="how-do-i-disable-data-collection"></a>Návody zakázat shromažďování dat?

Automatické zřizování se důrazně doporučuje, aby se získaly výstrahy zabezpečení a doporučení týkající se aktualizací systému, chyb zabezpečení operačního systému a ochrany koncových bodů. Ve výchozím nastavení je Automatické zřizování zakázané.

Pokud jste ho povolili, ale teď ho chcete zakázat:

1. Z [Azure Portal](https://portal.azure.com)otevřete **Security Center** a vyberte **ceny a nastavení**.

1. Vyberte předplatné, pro které chcete zakázat Automatické zřizování.

1. V části **Automatické zřizování** vypněte přepínač pro agenta Log Analytics.


## <a name="how-do-i-enable-data-collection"></a>Návody Povolit shromažďování dat?

V zásadách zabezpečení můžete v rámci svého předplatného Azure Povolit shromažďování dat. Pro povolení shromažďování dat. [Přihlaste se k Azure Portal](https://portal.azure.com), vyberte **procházet** , vyberte **Security Center** a vyberte **zásady zabezpečení**. Vyberte předplatné, pro které chcete povolit Automatické zřizování. Když vyberete zásadu zabezpečení předplatného, otevře se **kolekce dat** . V části **Automatické zřizování** vyberte **zapnuto**.


## <a name="what-happens-when-data-collection-is-enabled"></a>Co se stane, když je povolené shromažďování dat?

Když je povolené Automatické zřizování, Security Center zřídí agenta Log Analytics na všech podporovaných virtuálních počítačích Azure a všech nově vytvořených. Automatické zřizování se doporučuje, ale k dispozici je také ruční instalace agenta. [Přečtěte si, jak nainstalovat rozšíření agenta Log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Agent umožňuje událost vytvoření procesu 4688 a pole *CommandLine* v události 4688. Nové procesy vytvořené na virtuálním počítači se zaznamenávají protokolem událostí a monitorují služby zjišťování Security Center. Další informace o podrobnostech zaznamenaných pro každý nový proces najdete v tématu [Popis polí v 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Agent také shromažďuje události 4688 vytvořené na virtuálním počítači a ukládá je do vyhledávání.

Agent taky povoluje shromažďování dat pro [Adaptivní řízení aplikací](security-center-adaptive-application.md), Security Center v režimu auditování konfiguruje místní zásadu AppLockeru, aby povolovala všechny aplikace. Tato zásada způsobí, že AppLocker generuje události, které se pak shromažďují a využívají Security Center. Je důležité si uvědomit, že tato zásada nebude nakonfigurovaná na žádném počítači, na kterém je už nakonfigurovaná zásada AppLockeru. 

Když Security Center detekuje podezřelou aktivitu na virtuálním počítači, zákazník obdrží oznámení e-mailem, pokud byly poskytnuty [informace o kontaktu se zabezpečením](security-center-provide-security-contact-details.md) . Na řídicím panelu výstrahy zabezpečení Security Center se také zobrazí výstraha.


## <a name="will-security-center-work-using-an-oms-gateway"></a>Bude Security Center pracovat s bránou OMS?

Ano. Azure Security Center využívá Azure Monitor ke shromažďování dat z virtuálních počítačů a serverů Azure pomocí agenta Log Analytics.
Ke shromáždění dat se musí každý virtuální počítač a Server připojit k Internetu pomocí protokolu HTTPS. Připojení může být přímo, pomocí proxy serveru nebo pomocí [brány OMS](../azure-monitor/platform/gateway.md).


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Ovlivňuje agent monitorování výkon mých serverů?

Agent spotřebovává jmenovité množství systémových prostředků a měl by mít malý dopad na výkon. Další informace o dopadu na výkon a o agentovi a rozšíření najdete v [Průvodci plánováním a provozem](security-center-planning-and-operations-guide.md#data-collection-and-storage).




<!--Image references-->
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png