---
title: Nejčastější dotazy k Azure Security Center | Microsoft Docs
description: V této části Nejčastější dotazy najdete otázky týkající se Azure Security Center, produktu, který pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně.
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
ms.openlocfilehash: 896db06204188c4347fbdced0b1bb3f216f56ef9
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2019
ms.locfileid: "74558664"
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Nejčastější dotazy ohledně Azure Security Center
V této části Nejčastější dotazy najdete otázky týkající se Azure Security Center, což je služba, která pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně s lepším přehledem o zabezpečení prostředků Microsoft Azure a kontrolu nad nimi.

> [!NOTE]
> Security Center používá Microsoft Monitoring Agent ke shromažďování a ukládání dat. Další informace najdete v tématu [migrace platformy Azure Security Center](security-center-platform-migration.md).
>
>

## <a name="general-questions"></a>Obecné otázky
### <a name="what-is-azure-security-center"></a>Co je Centrum zabezpečení Azure?
Azure Security Center pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně a nabízí lepší přehled o zabezpečení prostředků Azure a kontrolu nad nimi. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

### <a name="how-do-i-get-azure-security-center"></a>Návody získat Azure Security Center?
Azure Security Center je povolená u vašeho předplatného Microsoft Azure a ke kterému se dostanete z [Azure Portal](https://azure.microsoft.com/features/azure-portal/). ([Přihlaste se k portálu](https://portal.azure.com), vyberte **Procházet**a přejděte na **Security Center**).  

## <a name="billing"></a>Vyúčtování
### <a name="how-does-billing-work-for-azure-security-center"></a>Jak funguje fakturace pro Azure Security Center?
Security Center se nabízí ve dvou úrovních:

**Úroveň Free** nabízí přehled o stavu zabezpečení vašich prostředků Azure, základní zásady zabezpečení, doporučení zabezpečení a integraci s produkty a službami zabezpečení od partnerů.

**Úroveň Standard** přináší pokročilé možnosti detekce hrozeb, včetně analýzy hrozeb, analýzy chování, detekce anomálií, incidentů zabezpečení a sestav pro navýšení hrozeb. Můžete spustit bezplatnou zkušební verzi na úrovni Standard. Pokud chcete upgradovat, vyberte [cenovou úroveň](https://docs.microsoft.com/azure/security-center/security-center-pricing) v zásadách zabezpečení. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).

### <a name="how-can-i-track-who-in-my-organization-performed-pricing-tier-changes-in-azure-security-center"></a>Jak mohu sledovat, kdo v naší organizaci prováděl změny cenové úrovně v Azure Security Center
Předplatná Azure můžou mít víc správců s oprávněními ke změně cenové úrovně. Pokud chcete zjistit, který uživatel provedl změnu cenové úrovně, použijte protokol aktivit Azure. Další informace najdete [tady](https://techcommunity.microsoft.com/t5/Security-Identity/Tracking-Changes-in-the-Pricing-Tier-for-Azure-Security-Center/td-p/390832).

## <a name="permissions"></a>Oprávnění
Azure Security Center používá [řízení přístupu na základě rolí (RBAC)](../role-based-access-control/role-assignments-portal.md). To poskytuje [předdefinované role](../role-based-access-control/built-in-roles.md), které se dají v Azure přiřadit uživatelům, skupinám a službám.

Security Center posuzuje konfiguraci vašich prostředků pro identifikaci problémů a ohrožení zabezpečení. V Security Center se zobrazí informace týkající se prostředku pouze v případě, že máte přiřazenou roli vlastníka, přispěvatele nebo čtenáře pro předplatné nebo skupinu prostředků, do které prostředek patří.

Další informace o rolích a povolených akcích v Security Center najdete v tématu věnovaném [oprávněním v Azure Security Center](security-center-permissions.md) .

## <a name="data-collection-agents-and-workspaces"></a>Shromažďování dat, agenti a pracovní prostory
Security Center shromažďuje data z virtuálních počítačů Azure, sady škálování virtuálních počítačů, kontejnerů IaaS a počítačů mimo Azure (včetně místních), aby se mohla monitorovat ohrožení zabezpečení a hrozby. Data se shromažďují pomocí agenta Microsoft Monitoring Agent, který z počítače načítá různé protokoly událostí a konfigurace související se zabezpečením a kopíruje data k analýze do vašeho pracovního prostoru.

### <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Účtují se mi Azure Monitor protokoly v pracovních prostorech vytvořených pomocí Security Center?
Ne. Pracovní prostory vytvořené pomocí Security Center, i když jsou nakonfigurované pro Azure Monitor protokolů za uzel, se neúčtují Azure Monitor poplatky. Security Center fakturace je vždycky založená na vašich Security Center zásadách zabezpečení a řešeních, která jsou nainstalovaná v pracovním prostoru:

- **Úroveň Free** – Security Center ve výchozím pracovním prostoru povolí řešení ' SecurityCenterFree '. Za bezplatnou úroveň se vám nebude účtovat.
- **Úroveň Standard** – Security Center ve výchozím pracovním prostoru povoluje řešení zabezpečení.

Další informace o cenách najdete v tématu [Security Center ceny](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Cenová úroveň Log Analytics pracovních prostorů vytvořených pomocí Security Center nemá vliv na Security Centerou fakturaci.
>
>

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Co má virtuální počítač pro Automatické zřizování Microsoft Monitoring Agent instalace?
Virtuální počítače s Windows nebo Linux IaaS mají nárok v těchto případech:

- Rozšíření Microsoft Monitoring Agent není aktuálně na virtuálním počítači nainstalováno.
- Virtuální počítač je ve spuštěném stavu.
- Je nainstalovaný [Agent virtuálního počítače Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) se systémem Windows nebo Linux.
- Virtuální počítač se nepoužívá jako zařízení, jako je třeba brána firewall webových aplikací nebo brána firewall nové generace.

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Můžu odstranit výchozí pracovní prostory vytvořené pomocí Security Center?
**Odstranění výchozího pracovního prostoru se nedoporučuje.** Security Center používá výchozí pracovní prostory k ukládání dat zabezpečení z vašich virtuálních počítačů.  Pokud pracovní prostor odstraníte, Security Center není schopen tato data shromáždit a některá doporučení a upozornění zabezpečení nejsou k dispozici.

Chcete-li provést obnovení, odeberte Microsoft Monitoring Agent na virtuálních počítačích připojených k odstraněnému pracovnímu prostoru. Security Center znovu nainstaluje agenta a vytvoří nové výchozí pracovní prostory.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Jak můžu použít stávající pracovní prostor Log Analytics?

Můžete vybrat existující pracovní prostor Log Analytics pro ukládání dat shromažďovaných pomocí Security Center. Chcete-li použít stávající pracovní prostor Log Analytics:

- Pracovní prostor musí být přidružený k vybranému předplatnému Azure.
- Aby bylo možné získat přístup k pracovnímu prostoru, musíte mít oprávnění ke čtení.

Vyberte existující pracovní prostor Log Analytics:

1. V části **zásady zabezpečení – shromažďování dat**vyberte **použít jiný pracovní prostor**.

   ![Použít jiný pracovní prostor][5]

2. V rozevírací nabídce vyberte pracovní prostor pro ukládání shromážděných dat.

   > [!NOTE]
   > V nabídce vyžádané replikace se zobrazí pouze pracovní prostory, ke kterým máte přístup a které jsou v rámci vašeho předplatného Azure.
   >
   >

3. Vyberte **Save** (Uložit).
4. Po výběru možnosti **Uložit**se zobrazí dotaz, zda chcete překonfigurovat monitorované virtuální počítače.

   - Tuto **možnost vyberte** , pokud chcete, aby se nové nastavení pracovního prostoru **projevilo jenom pro nové virtuální počítače**. Nové nastavení pracovního prostoru platí jenom pro nové instalace agenta. nově zjištěné virtuální počítače, u kterých není Microsoft Monitoring Agent nainstalováno.
   - Vyberte **Ano** , pokud chcete, aby se nové nastavení pracovního prostoru **projevilo na všech virtuálních počítačích**. Kromě toho se každý virtuální počítač připojený k Security Center vytvořenému pracovnímu prostoru znovu připojí k novému cílovému pracovnímu prostoru.

   > [!NOTE]
   > Pokud vyberete Ano, nesmíte odstranit pracovní prostory vytvořené pomocí Security Center, dokud se všechny virtuální počítače znovu nepřipojí k novému cílovému pracovnímu prostoru. Tato operace se nezdařila, pokud je pracovní prostor odstraněn příliš brzy.
   >
   >

   - Vyberte **Zrušit** a operaci se zruší.

### Co když už je Microsoft Monitoring Agent na virtuálním počítači nainstalovaná jako rozšíření?<a name="mmaextensioninstalled"></a>
Pokud je agent monitorování nainstalován jako rozšíření, konfigurace rozšíření umožňuje vytváření sestav pouze do jednoho pracovního prostoru. Security Center nepřepisují existující připojení k pracovním prostorům uživatelů. Security Center bude ukládat data zabezpečení z virtuálního počítače v pracovním prostoru, který je už připojený, za předpokladu, že je na něm nainstalované řešení Security nebo SecurityCenterFree. Security Center může v tomto procesu upgradovat verzi rozšíření na nejnovější verzi.

Další informace najdete v tématu [Automatické zřizování v případě již existující instalace agenta](security-center-enable-data-collection.md#preexisting).


### Co když mám Microsoft Monitoring Agent nainstalovanou přímo na počítači, ale ne jako rozšíření (přímý Agent)?<a name="directagentinstalled"></a>
Pokud je Microsoft Monitoring Agent nainstalovaná přímo na virtuálním počítači (ne jako rozšíření Azure), Security Center nainstaluje rozšíření Microsoft Monitoring Agent a může upgradovat agenta Microsoft Monitoring Agent na nejnovější verzi.
Nainstalovaný Agent bude pokračovat v hlášení do již nakonfigurovaných pracovních prostorů a kromě toho bude hlásit do pracovního prostoru nakonfigurovaného v Security Center (u počítačů s Windows je podporováno více domovských stránek).
Pokud je nakonfigurovaným pracovním prostorem pracovní prostor uživatele (ve výchozím nastavení není Security Center), bude nutné do něj nainstalovat řešení "Security/" SecurityCenterFree ", aby bylo možné Security Center zahájit zpracování událostí z virtuálních počítačů a počítačů, které do daného pracovního prostoru hlásí.

Pro počítače se systémem Linux zatím není podporována podpora více domovských stránek agenta, takže pokud je zjištěna existující instalace agenta, Automatické zřizování nebude provedeno a konfigurace počítače nebude změněna.

Pro existující počítače v předplatných, která se zaregistrují do Security Center do března 17 2019, se při zjištění existujícího agenta Microsoft Monitoring Agent rozšíření nenainstaluje a počítač nebude ovlivněn. Pro tyto počítače si přečtěte doporučení "vyřešit problémy se stavem agenta monitorování na vašich počítačích" a vyřešte problémy s instalací agenta na těchto počítačích.

 Další informace najdete v další části, [co se stane, když je na mém virtuálním počítači už nainstalovaný System Center Operations Manager nebo přímý agent OMS](#scomomsinstalled) .

### Co se stane, když je na mém virtuálním počítači už nainstalovaný Agent System Center Operations Manager?<a name="scomomsinstalled"></a>
Security Center nainstaluje rozšíření Microsoft Monitoring Agent vedle sebe stávajícímu agentu System Center Operations Manager. Stávající Agent bude nadále hlásit Server System Center Operations Manager normálně. Všimněte si, že agent Operations Manager a Microsoft Monitoring Agent sdílet běžné běhové knihovny, které budou během tohoto procesu aktualizovány na nejnovější verzi. Poznámka: Pokud je nainstalovaná verze 2012 agenta Operations Manager, nepovolujte Automatické zřizování (možnosti spravovatelnosti se můžou ztratit, pokud je Operations Manager Server verze 2012).

### <a name="what-is-the-impact-of-removing-these-extensions"></a>Jaký je dopad odebrání těchto rozšíření?
Pokud odeberete rozšíření Microsoft Monitoring, Security Center nebude moct shromažďovat data zabezpečení z virtuálního počítače a některá doporučení a upozornění zabezpečení nejsou k dispozici. Do 24 hodin Security Center zjistí, že virtuální počítač postrádá rozšíření a znovu nainstaluje rozšíření.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Návody zastavit automatickou instalaci agenta a vytvoření pracovního prostoru?
Automatické zřizování pro vaše předplatná můžete vypnout v zásadách zabezpečení, ale nedoporučuje se to. Vypnutí automatických omezení zřizování Security Center doporučení a výstrah. Zakázání automatického zřizování:

1. Pokud je vaše předplatné nakonfigurované pro úroveň Standard, otevřete pro toto předplatné zásadu zabezpečení a vyberte úroveň **Free** .

   ![Cenová úroveň][1]

2. V dalším kroku vypnout automatické zřizování výběrem možnosti **vypnuto** na stránce **zásady zabezpečení – shromažďování dat** .
   ![Shromažďování dat][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Doporučuje se odhlásit z automatické instalace agenta a vytvoření pracovního prostoru?

> [!NOTE]
> Nezapomeňte si projít oddíly, [které mají vliv na vypnutí?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) a [Doporučené kroky](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) , pokud se rozhodnete, že se chcete odhlásit z automatického zřizování.
>
>

Automatické zřizování můžete odhlásit, pokud platí následující:

- Automatická instalace agenta podle Security Center platí pro celé předplatné. Nemůžete použít automatickou instalaci na podmnožinu virtuálních počítačů. Pokud jsou k dispozici kritické virtuální počítače, které nelze nainstalovat pomocí Microsoft Monitoring Agent, měli byste se odhlásit z automatického zřizování.
- Instalace rozšíření Microsoft Monitoring Agent (MMA) aktualizuje verzi agenta. To platí pro přímého agenta a System Center Operations Managerho agenta (v druhé Operations Manager a MMA sdílet společné běhové knihovny, které se budou aktualizovat v procesu). Pokud je nainstalovaný agent Operations Manager verze 2012 a upgradovaný, můžou být možnosti spravovatelnosti ztraceny, pokud je Operations Manager Server také verze 2012. Pokud je nainstalovaný agent Operations Manager verze 2012, zvažte Automatické zřizování.
- Pokud máte vlastní pracovní prostor mimo předplatné (centralizovaný pracovní prostor), měli byste se odhlásit z automatického zřizování. Můžete ručně nainstalovat rozšíření Microsoft Monitoring Agent a připojit ho k pracovnímu prostoru bez Security Center přepsání připojení.
- Pokud se chcete vyhnout vytváření několika pracovních prostorů na jedno předplatné a máte vlastní pracovní prostor v rámci předplatného, máte dvě možnosti:

   1. Automatické zřizování se můžete odhlásit. Po dokončení migrace nastavte výchozí nastavení pracovního prostoru, jak je popsáno v tématu [Jak můžu použít můj stávající pracovní prostor Log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)
   2. Nebo můžete nechat migraci dokončit, Microsoft Monitoring Agent nainstalovat na virtuální počítače a virtuální počítače připojené k vytvořenému pracovnímu prostoru. Pak vyberte vlastní pracovní prostor nastavením výchozí nastavení pracovního prostoru s možnostmi přidružit se k překonfigurování již nainstalovaných agentů. Další informace najdete v tématu [Jak můžu použít stávající pracovní prostor Log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Jaké jsou důsledky vypnutí automatického zřizování?
Po dokončení migrace Security Center nemůžou shromažďovat data zabezpečení z virtuálního počítače a některá doporučení a upozornění zabezpečení nejsou k dispozici. Pokud se odhlásíte odhlásit, nainstalujte Microsoft Monitoring Agent ručně. Přečtěte si téma [Doporučené kroky při](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)odsouhlasení.

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Jaké jsou doporučené kroky při vypnutí automatického zřizování?

Ručně nainstalujte Microsoft Monitoring Agent rozšíření tak, aby Security Center mohl shromažďovat data zabezpečení z vašich virtuálních počítačů a poskytovat doporučení a výstrahy. Pokyny k instalaci najdete v tématu [instalace agenta pro virtuální počítač s Windows](../virtual-machines/extensions/oms-windows.md) nebo [instalace agenta pro virtuální počítač Linux](../virtual-machines/extensions/oms-linux.md) .

Agenta můžete připojit k jakémukoli existujícímu vlastnímu pracovnímu prostoru nebo Security Center vytvořenému pracovnímu prostoru. Pokud vlastní pracovní prostor nemá povolená řešení Security nebo SecurityCenterFree, budete muset použít řešení. Pokud ho chcete použít, vyberte vlastní pracovní prostor nebo předplatné a pomocí stránky **zásady zabezpečení – cenová** úroveň použijte cenovou úroveň.

   ![Cenová úroveň][1]

Security Center umožní správné řešení v pracovním prostoru na základě vybrané cenové úrovně.

### Návody odebrat rozšíření OMS nainstalovaná pomocí Security Center?<a name="remove-oms"></a>
Microsoft Monitoring Agent můžete odebrat ručně. Nedoporučuje se to, protože omezuje Security Center doporučení a upozornění.

> [!NOTE]
> Pokud je povolené shromažďování dat, Security Center po jeho odebrání agenta znovu nainstaluje.  Shromažďování dat je nutné před ručním odebráním agenta zakázat. Přečtěte si téma Návody zastavení automatické instalace agenta a vytvoření pracovního prostoru? pokyny k zakázání shromažďování dat.
>
>

Ruční odebrání agenta:

1.  Na portálu otevřete **Log Analytics**.
2.  Na stránce Log Analytics vyberte pracovní prostor:
3.  Vyberte virtuální počítače, které nechcete monitorovat, a vyberte **Odpojit**.

   ![Odebrat agenta][3]

> [!NOTE]
> Pokud už virtuální počítač se systémem Linux má agenta OMS bez rozšíření, odebrání rozšíření odebere i agenta a zákazník ho musí znovu nainstalovat.
>
>
### <a name="how-do-i-disable-data-collection"></a>Návody zakázat shromažďování dat?
Automatické zřizování je ve výchozím nastavení vypnuté. Automatické zřizování z prostředků můžete kdykoli zakázat vypnutím tohoto nastavení v zásadách zabezpečení. Automatické zřizování se důrazně doporučuje, aby se získaly výstrahy zabezpečení a doporučení týkající se aktualizací systému, chyb zabezpečení operačního systému a ochrany koncových bodů.

Chcete-li shromažďování dat zakázat, [Přihlaste se k Azure Portal](https://portal.azure.com), vyberte **procházet**, vyberte **Security Center**a vyberte **možnost vybrat zásadu**. Vyberte předplatné, pro které chcete vypnout automatické zřizování. Když vyberete zásadu zabezpečení předplatného, otevře se **kolekce dat** . V části **Automatické zřizování**vyberte **vypnuto**.

### <a name="how-do-i-enable-data-collection"></a>Návody Povolit shromažďování dat?
V zásadách zabezpečení můžete v rámci svého předplatného Azure Povolit shromažďování dat. Pro povolení shromažďování dat. [Přihlaste se k Azure Portal](https://portal.azure.com), vyberte **procházet**, vyberte **Security Center**a vyberte **zásady zabezpečení**. Vyberte předplatné, pro které chcete povolit Automatické zřizování. Když vyberete zásadu zabezpečení předplatného, otevře se **kolekce dat** . V části **Automatické zřizování**vyberte **zapnuto**.

### <a name="what-happens-when-data-collection-is-enabled"></a>Co se stane, když je povolené shromažďování dat?
Když je povolené Automatické zřizování, Security Center zřídí Microsoft Monitoring Agent na všech podporovaných virtuálních počítačích Azure a všech nově vytvořených. Automatické zřizování se doporučuje, ale k dispozici je také ruční instalace agenta. [Naučte se nainstalovat rozšíření Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Agent umožňuje událost vytvoření procesu 4688 a pole *CommandLine* v události 4688. Nové procesy vytvořené na virtuálním počítači se zaznamenávají protokolem událostí a monitorují služby zjišťování Security Center. Další informace o podrobnostech zaznamenaných pro každý nový proces najdete v tématu [Popis polí v 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Agent také shromažďuje události 4688 vytvořené na virtuálním počítači a ukládá je do vyhledávání.

Agent taky povoluje shromažďování dat pro [Adaptivní řízení aplikací](security-center-adaptive-application.md), Security Center v režimu auditování konfiguruje místní zásadu AppLockeru, aby povolovala všechny aplikace. Tato zásada způsobí, že AppLocker generuje události, které se pak shromažďují a využívají Security Center. Je důležité si uvědomit, že tato zásada nebude nakonfigurovaná na žádném počítači, na kterém je už nakonfigurovaná zásada AppLockeru. 

Když Security Center detekuje podezřelou aktivitu na virtuálním počítači, zákazník obdrží oznámení e-mailem, pokud byly poskytnuty [informace o kontaktu se zabezpečením](security-center-provide-security-contact-details.md) . Na řídicím panelu výstrahy zabezpečení Security Center se také zobrazí výstraha.

### <a name="will-security-center-work-using-an-oms-gateway"></a>Bude Security Center pracovat s bránou OMS?
Ano. Azure Security Center využívá Azure Monitor ke shromažďování dat z virtuálních počítačů a serverů Azure pomocí Microsoft Monitoring Agent.
Ke shromáždění dat se musí každý virtuální počítač a Server připojit k Internetu pomocí protokolu HTTPS. Připojení může být přímo, pomocí proxy serveru nebo pomocí [brány OMS](../azure-monitor/platform/gateway.md).

### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Ovlivňuje agent monitorování výkon mých serverů?
Agent spotřebovává jmenovité množství systémových prostředků a měl by mít malý dopad na výkon. Další informace o dopadu na výkon a o agentovi a rozšíření najdete v [Průvodci plánováním a provozem](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Kde jsou moje data uložená?
Data shromážděná z tohoto agenta se ukládají do existujícího pracovního prostoru Log Analytics přidruženého k vašemu předplatnému nebo novému pracovnímu prostoru. Další informace najdete v tématu [zabezpečení dat](security-center-data-security.md).

## Existující Azure Monitor protokolují zákazníky<a name="existingloganalyticscust"></a>

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Přepíší Security Center všechna existující připojení mezi virtuálními počítači a pracovními prostory?
Pokud má virtuální počítač již Microsoft Monitoring Agent nainstalován jako rozšíření Azure, Security Center existující připojení k pracovnímu prostoru nepřepisuje. Místo toho Security Center používá existující pracovní prostor. Virtuální počítač bude chráněn za předpokladu, že řešení "Security" nebo "SecurityCenterFree" bylo nainstalováno do pracovního prostoru, do kterého se hlásí. 

Řešení Security Center je nainstalováno v pracovním prostoru vybraném na obrazovce shromažďování dat, pokud již není přítomno, a toto řešení je použito pouze pro příslušné virtuální počítače. Když přidáte řešení, automaticky se nasadí ve výchozím nastavení na všechny agenty systému Windows a Linux připojené k vašemu pracovnímu prostoru Log Analytics. [Cílení na řešení](../operations-management-suite/operations-management-suite-solution-targeting.md) umožňuje použít obor pro vaše řešení.

Pokud je Microsoft Monitoring Agent nainstalovaná přímo na virtuálním počítači (ne jako rozšíření Azure), Security Center nenainstaluje Microsoft Monitoring Agent a monitorování zabezpečení je omezené.

### <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Je Security Center instalovat řešení do stávajících pracovních prostorů Log Analytics? Jaké jsou důsledky fakturace?
Když Security Center identifikuje, že je virtuální počítač už připojený k pracovnímu prostoru, který jste vytvořili, Security Center umožňuje řešení v tomto pracovním prostoru podle vaší cenové úrovně. Řešení se aplikují jenom na relevantní virtuální počítače Azure, a to prostřednictvím [cílení na řešení](../operations-management-suite/operations-management-suite-solution-targeting.md), takže účtování zůstane stejné.

- **Úroveň Free** – Security Center do pracovního prostoru nainstaluje řešení ' SecurityCenterFree '. Za bezplatnou úroveň se vám nebude účtovat.
- **Úroveň Standard** – Security Center do pracovního prostoru nainstaluje řešení zabezpečení.

   ![Řešení ve výchozím pracovním prostoru][4]

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Již mám v prostředí pracovní prostory, můžu je použít ke shromažďování dat zabezpečení?
Pokud má virtuální počítač již Microsoft Monitoring Agent nainstalován jako rozšíření Azure, Security Center používá existující připojený pracovní prostor. V pracovním prostoru je nainstalováno Security Center řešení, pokud již není přítomno a řešení se používá pouze pro příslušné virtuální počítače prostřednictvím [cílení na řešení](../operations-management-suite/operations-management-suite-solution-targeting.md).

Když Security Center nainstaluje Microsoft Monitoring Agent na virtuální počítače, použije výchozí pracovní prostory, které vytvořila Security Center.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Již mám řešení zabezpečení v pracovních prostorech. Jaké jsou důsledky fakturace?
Řešení auditu & zabezpečení se používá k povolení funkcí služby Security Center úrovně Standard pro virtuální počítače Azure. Pokud je v pracovním prostoru již nainstalováno řešení auditu zabezpečení &, Security Center používá existující řešení. Fakturace se nemění.

## <a name="using-azure-security-center"></a>Použití Azure Security Center
### <a name="what-is-a-security-policy"></a>Co je zásada zabezpečení?
Zásady zabezpečení definují sadu ovládacích prvků, které jsou doporučeny pro prostředky v rámci zadaného předplatného. V Azure Security Center definujete zásady pro vaše předplatná Azure podle požadavků na zabezpečení vaší společnosti a typu aplikací nebo citlivosti dat v jednotlivých předplatných.

Zásady zabezpečení, které jsou povolené v Azure Security Center, doporučení zabezpečení a monitorování. Další informace o zásadách zabezpečení najdete v tématu [monitorování stavu zabezpečení v Azure Security Center](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Kdo může upravit zásady zabezpečení?
Chcete-li upravit zásadu zabezpečení, musíte být správcem zabezpečení nebo vlastníkem nebo přispěvatelem daného předplatného.

Informace o tom, jak nakonfigurovat zásady zabezpečení, najdete [v tématu Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md).

### <a name="what-is-a-security-recommendation"></a>Co je to doporučení zabezpečení?
Azure Security Center analyzuje stav zabezpečení vašich prostředků Azure. Když se identifikují potenciální slabá místa zabezpečení, vytvoří se doporučení. Doporučení vás provede procesem konfigurace potřebného ovládacího prvku. Můžete například:

* Zřizování ochrany proti malwaru, které vám pomůžou identifikovat a odebrat škodlivý software
* [Skupiny zabezpečení sítě](../virtual-network/security-overview.md) a pravidla pro řízení provozu do virtuálních počítačů
* Zřizování firewallu webových aplikací, které vám pomůžou chránit před útoky zaměřenými na vaše webové aplikace
* Nasazení chybějících aktualizací systému
* Adresování konfigurací operačního systému, které neodpovídají doporučeným standardním hodnotám

Tady se zobrazí jenom doporučení, která jsou povolená v zásadách zabezpečení.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Jak se můžu podívat na aktuální stav zabezpečení prostředků Azure?
Na stránce **přehled Security Center** se zobrazuje celkové stav zabezpečení vašeho prostředí v členění podle COMPUTE, sítě, & úložiště dat a aplikací. Každý typ prostředku má ukazatel ukazující, zda byly zjištěny případné potenciální chyby zabezpečení. Kliknutím na každou dlaždici se zobrazí seznam problémů se zabezpečením, které identifikoval Security Center, spolu s inventářem prostředků ve vašem předplatném.

### <a name="what-triggers-a-security-alert"></a>Co aktivuje výstrahu zabezpečení?
Azure Security Center automaticky shromažďuje, analyzuje a uchovává data protokolu z vašich prostředků Azure, sítě a partnerských řešení, jako jsou antimalware a brány firewall. Při zjištění ohrožení zabezpečení se vytvoří výstraha zabezpečení. Příklady zahrnují zjišťování následujících situací:

* Ohrožené virtuální počítače, které komunikují se známými IP adresami se zlými úmysly
* Byl zjištěn rozšířený malware pomocí zasílání zpráv o chybách systému Windows.
* Útoky hrubou silou na virtuální počítače
* Výstrahy zabezpečení z integrovaných řešení zabezpečení partnerů, jako jsou například anti-malware nebo brány firewall webových aplikací

### Proč se hodnoty se zabezpečenými výsledky změnily? <a name="secure-score-faq"></a>
Od února 2019 Security Center upraveno skóre několika doporučení, aby lépe vyhovoval jejich závažnosti. Výsledkem této úpravy můžou být změny v celkových hodnotách bezpečného skóre.  Další informace o zabezpečeném skóre najdete v tématu [Secure skore Calculation](security-center-secure-score.md).

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Jaký je rozdíl mezi zjištěnými hrozbami a výstrahou z centra zabezpečení Microsoft Security Center oproti Azure Security Center?
Microsoft Security Response Center (MSRC) provádí výběr sledování zabezpečení sítě a infrastruktury Azure a přijímá analýzy hrozeb a stížnosti na zneužití od třetích stran. Když bude MSRC vědět, že k zákaznickým datům došlo v nezákonné nebo neoprávněné osobě nebo že její používání Azure nedodržuje podmínky přijatelného použití, správce incidentů zabezpečení ho upozorní zákazníka. K oznámení obvykle dochází odesláním e-mailu do kontaktů zabezpečení uvedených v Azure Security Center nebo vlastníka předplatného Azure, pokud není zadán kontakt zabezpečení.

Security Center je služba Azure, která nepřetržitě monitoruje prostředí Azure zákazníka a používá analýzu k automatické detekci široké škály potenciálně škodlivých aktivit. Tyto detekce jsou v řídicím panelu Security Center Surface výstrah zabezpečení.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Které prostředky Azure monitoruje Azure Security Center?
Azure Security Center sleduje následující prostředky Azure:

* Virtuální počítače (včetně [Cloud Services](../cloud-services/cloud-services-choose-me.md))
* Škálovací sady virtuálních počítačů
* Virtuální sítě Azure
* Služba Azure SQL
* Účet služby Azure Storage
* Azure Web Apps (v [App Service Environment](../app-service/environment/intro.md))
* Partnerská řešení integrovaná s vaším předplatným Azure, například firewall webových aplikací na virtuálních počítačích a na App Service Environment

Kromě toho je možné monitorovat také počítače jiné než Azure (včetně místních) Azure Security Center (podporuje se počítače se [systémem Windows](./quick-onboard-windows-computer.md) i [Linux](./quick-onboard-linux-computer.md) ).

## <a name="virtual-machines"></a>Služba Virtual Machines
### <a name="what-types-of-virtual-machines-are-supported"></a>Jaké typy virtuálních počítačů se podporují?
Monitorování a doporučení jsou k dispozici pro virtuální počítače vytvořené pomocí [modelu nasazení Classic i správce prostředků](../azure-classic-rm.md).

Seznam podporovaných platforem najdete [v tématu podporované platformy v Azure Security Center](security-center-os-coverage.md) .

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Proč Azure Security Center nerozpoznají antimalwarové řešení běžící na mém virtuálním počítači Azure?
Azure Security Center má přehled o antimalwaru nainstalovaném prostřednictvím rozšíření Azure. Security Center například nedokáže detekovat Antimalwarový program, který byl předem nainstalován na vámi poskytnutou bitovou kopii, nebo pokud jste nainstalovali antimalware na virtuálních počítačích pomocí vlastních procesů (například systémů pro správu konfigurace).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Proč se mi zobrazí zpráva "chybějící data kontroly" pro svůj virtuální počítač?
Tato zpráva se zobrazí, pokud na virtuálním počítači nejsou žádná data kontroly. Naplnění dat kontroly po povolení shromažďování dat ve službě Azure Security Center může nějakou chvíli trvat (méně než hodinu). Tato zpráva se vám může zobrazit po počátečním naplnění dat kontroly, protože neexistují vůbec žádná data kontroly ani žádná nová data kontroly. Kontroly se nenaplňují u virtuálních počítačů v zastaveném stavu. Tato zpráva se může zobrazit i v poslední době (v souladu se zásadami uchovávání informací pro agenta pro Windows, který má výchozí hodnotu 30 dnů).

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Jak často Security Center hledat ohrožení zabezpečení operačního systému, aktualizace systému a problémy s ochranou koncového bodu?
Níže jsou uvedeny doby latence Security Center prověřování chyb zabezpečení, aktualizací a problémů:

- Konfigurace zabezpečení operačního systému – data se aktualizují během 48 hodin.
- Aktualizace systému – data se aktualizují během 24 hodin.
- Problémy s Endpoint Protection – data se aktualizují během 8 hodin.

Security Center obvykle vyhledává nová data každou hodinu a příslušná doporučení aktualizuje. 

> [!NOTE]
> Security Center používá Microsoft Monitoring Agent ke shromažďování a ukládání dat. Další informace najdete v tématu [migrace platformy Azure Security Center](security-center-platform-migration.md).
>
>

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Proč se zobrazí zpráva "agent virtuálního počítače chybí?"
Na virtuálních počítačích musí být nainstalován agent virtuálního počítače, aby bylo možné shromažďování dat povolit. Agent virtuálního počítače je ve výchozím nastavení nainstalován na virtuálních počítačích nasazených z Azure Marketplace. Informace o tom, jak nainstalovat agenta virtuálního počítače na jiné virtuální počítače, najdete v blogovém příspěvku [agenta a rozšíření virtuálního počítače](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
