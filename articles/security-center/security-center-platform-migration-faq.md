---
title: Migrace platformy Security Center – nejčastější dotazy | Microsoft Docs
description: Tyto nejčastější dotazy odpovídá na dotazy týkající se migrace platformy Azure střediska zabezpečení.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2018
ms.author: terrylan
ms.openlocfilehash: 6a88fbadd8fbf05a4942e42b535770f6f068af28
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="security-center-platform-migration-faq"></a>Migrace platformy Security Center – nejčastější dotazy
V časná června 2017 začal Azure Security Center pomocí agenta Microsoft Monitoring Agent shromažďovat a ukládat data. Další informace najdete v tématu [Azure Security Center platformy migrace](security-center-platform-migration.md). Tyto nejčastější dotazy odpovídá na dotazy týkající se migrace platformy.

## <a name="data-collection-agents-and-workspaces"></a>Shromažďování dat, agentů a pracovních prostorů

### <a name="how-is-data-collected"></a>Jak se shromažďují data?
Security Center používá ke shromažďování dat zabezpečení z virtuálních počítačů Microsoft Monitoring Agent. Zabezpečení dat obsahuje následující informace:

- Konfigurace zabezpečení – používá k identifikaci ohrožení zabezpečení
- události zabezpečení – slouží k detekci hrozeb

Údaje shromážděné agentem je uložená v existující pracovní prostor analýzy protokolů připojený k virtuálnímu počítači nebo nový pracovní prostor vytvořené Security Center. Security Center vytvoří nový pracovní prostor, informace o zeměpisné poloze virtuálního počítače je vzít v úvahu.

> [!NOTE]
> Microsoft Monitoring Agent je stejného agenta používané služby analýzy protokolů a System Center Operations Manager (SCOM).
>
>

Pokud je povoleno automatické zřizování (dříve s názvem protokolu kolekce) nebo pokud se migrují vašich předplatných, zkontroluje Security Center, pokud Microsoft Monitoring Agent je již nainstalována jako rozšíření, která byla na všech virtuálních počítačů Azure. Pokud službu Microsoft Monitoring Agent není nainstalován, pak ve výchozím nastavení Security Center bude:

- Nainstalujte agenta Microsoft Monitoring Agent rozšíření ve virtuálním počítači.

   - Pokud pracovní prostor vytvořené Security Center již existuje ve stejné informace o zeměpisné poloze jako virtuální počítač, agenta je připojený k tomuto pracovnímu prostoru.
   - Pokud pracovní prostor neexistuje, Security Center vytvoří nový pracovní skupiny a výchozí prostředků v této informace o zeměpisné poloze a připojí agenta do tohoto pracovního prostoru. Zásady vytváření názvů pro skupinu pracovní prostor a prostředků je:

       Pracovní prostor: DefaultWorkspace-[ID předplatného]-[geograficky]

       Skupina prostředků: DefaultResouceGroup-[geograficky]

- Povolit Security Center řešení v pracovním prostoru pro každý virtuální počítač přidruženému cenová úroveň ve službě Security Center. Další informace o cenách najdete v tématu [Security Center ceny](https://azure.microsoft.com/pricing/details/security-center/).
- Pouze migrované odběry Security Center také odebere předchozí Azure Monitoring Agent.

> [!NOTE]
> Můžete přepsat automatická instalace agenta Microsoft Monitoring Agent a použít vlastní pracovní prostor.  V tématu [Postup zastavení agenta automatické instalace a prostoru vytváření](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation) a [použití pracovního prostoru existující](#how-can-i-use-my-existing-log-analytics-workspace).
>
>

Umístění pracovního prostoru je založena na umístění virtuálního počítače. Další informace najdete v tématu [zabezpečení dat](security-center-data-security.md). Pokud předplatné obsahuje virtuální počítače z více geolocations, Security Center vytvoří několik pracovních prostorů. Zachovat data o ochraně osobních údajů pravidla se vytvoří několik pracovních prostorů.

> [!NOTE]
> Před migrací platformy Security Center shromážděná data zabezpečení z virtuálních počítačů pomocí nástroje Azure Monitoring Agent a data byla uložena ve vašem účtu úložiště. Po dokončení migrace platformy Security Center používá Microsoft Monitoring Agent a prostoru shromáždit a uložit stejná data. Účet úložiště lze odebrat po migraci.  Dříve nainstalované agenty monitorování Azure Security Center také odebere po migraci platformy.
>
>

### <a name="am-i-billed-for-log-analytics-on-the-workspaces-created-by-security-center"></a>Mě účtuje analýzy protokolů na pracovních prostorů vytvořit pomocí služby Security Center?
Ne. Pracovní prostory, které jsou vytvořené pomocí služby Security Center, zatímco nakonfigurovaný pro analýzy protokolů na uzel fakturace, nevznikají poplatky analýzy protokolů. Fakturace Security Center je vždy založené na vaše zásady zabezpečení Security Center a řešení v pracovním prostoru nainstalován:

- **Úroveň Free** – Security Center umožňuje 'SecurityCenterFree' řešení na výchozí pracovní prostor. Fakturuje nejsou pro úroveň Free.
- **Úroveň standard** – Security Center umožňuje řešení, zabezpečení, v pracovním prostoru pro výchozí.

Další informace o cenách najdete v tématu [Security Center ceny](https://azure.microsoft.com/pricing/details/security-center/). Na stránce s cenami řeší změny do úložiště dat zabezpečení a poměrné fakturace od června 2017.

> [!NOTE]
> Analýzy protokolů cenová úroveň pracovních prostorů vytvořit pomocí služby Security Center neovlivňuje fakturace Security Center.
>
>

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Co vyfiltrování virtuálního počítače pro automatické zřizování instalace agenta Microsoft Monitoring Agent?
Windows nebo virtuální počítače IaaS Linux kvalifikaci, pokud:

- Rozšíření Microsoft Monitoring Agent není nainstalována na virtuálním počítači.
- Virtuální počítač je v běžícím stavu.
- Je nainstalovaný Windows nebo Linux Agent virtuálního počítače.
- Virtuální počítač není používána jako zařízení jako jsou brány firewall webových aplikací nebo brána firewall příští generace.

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Můžete odstranit výchozí pracovních prostorů vytvořit pomocí služby Security Center?
**Odstraňuje se pracovní prostor výchozí se nedoporučuje.** Security Center používá výchozí pracovní prostory k ukládání data zabezpečení z virtuálních počítačů.  Pokud odstraníte pracovního prostoru, Security Center se nepodařilo shromažďovat tato data a některé doporučení zabezpečení a výstrahy nejsou k dispozici.

Pokud chcete obnovit, odeberte agenta Microsoft Monitoring Agent na virtuální počítače připojené k odstraněné pracovního prostoru. Security Center opětovná instalace agenta a vytvoří nový výchozí pracovní prostory.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Jak je možné používat Můj stávající pracovní prostor analýzy protokolů

Můžete vybrat existující pracovní prostor analýzy protokolů pro ukládání dat shromažďovaných pomocí služby Security Center. Použít stávající pracovní prostor analýzy protokolů:

- V pracovním prostoru musí být přidružen vašem vybraném předplatném Azure.
- Minimálně musí mít čtení oprávnění pro přístup k pracovním prostoru.

Vyberte existující pracovní prostor analýzy protokolů:

1. V části **zásady zabezpečení – shromažďování dat**, vyberte **použijte jiný pracovní prostor**.

   ![Použít jiný pracovní prostor][5]

2. V rozevírací nabídce vyberte pracovní prostor pro ukládání shromážděných dat.

   > [!NOTE]
   > V seznamu vyžádaných nabídku se zobrazí pouze pracovní prostory, které mají přístup k a jsou ve vašem předplatném Azure.
   >
   >

3. Vyberte **Uložit**.
4. Po výběru **Uložit**, zobrazí se dotaz, pokud byste chtěli reconfigure monitorovat virtuální počítače.

   - Vyberte **ne** Pokud chcete, aby nová nastavení pracovního prostoru **použít na nové virtuální počítače pouze**. Nové nastavení pracovního prostoru se vztahují pouze na nové instalace agenta; nově zjištěných virtuálních počítačů, které nemají Microsoft Monitoring Agent nainstalována.
   - Vyberte **Ano** Pokud chcete, aby nová nastavení pracovního prostoru **použít na všech virtuálních počítačích**. Kromě toho každých virtuálních počítačů připojených k Security Center vytvořit pracovní prostor je znovu na nový cílový pracovní prostor.

   > [!NOTE]
   > Pokud vyberete Ano, odstraníte nesmí pracovních prostorů vytvářet pomocí služby Security Center, dokud se všechny virtuální počítače mají úspěšné automatické připojení k nové cílového pracovního prostoru. Tato operace selže, pokud je příliš brzké odstranit pracovní prostor.
   >
   >

   - Vyberte **zrušit** na tlačítko Storno.

### <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>Co když Microsoft Monitoring Agent byl již nainstalován jako rozšíření ve virtuálním počítači?
Security Center nemůže přepsat existující připojení k pracovním prostorům, uživatele. Security Center ukládá data zabezpečení z virtuálního počítače v pracovním prostoru již připojen. Security Center aktualizuje na verzi rozšíření zahrnují ID prostředků Azure pro podporu Security Center využití virtuálního počítače.

### <a name="what-if-i-had-a-microsoft-monitoring-agent-installed-on-the-machine-but-not-as-an-extension"></a>Co když měl Microsoft Monitoring Agent nainstalovaný na počítači, ale ne jako rozšíření?
Pokud je Microsoft Monitoring Agent nainstalován přímo na virtuálním počítači (ne jako Azure rozšíření), Security Center nenainstaluje agenta Microsoft Monitoring Agent a sledování zabezpečení je omezená.

Další informace najdete v části Další [co se stane, když SCOM nebo OMS směrovat na virtuální počítač je již nainstalován agent?](security-center-platform-migration-faq.md#what-happens-if-a-scom-or-oms-direct-agent-is-already-installed-on-my-vm)

### <a name="what-happens-if-a-scom-or-oms-direct-agent-is-already-installed-on-my-vm"></a>Co se stane, když SCOM nebo OMS přímé agent je již nainstalován na virtuální počítač?
Security Center nemůže identifikovat předem, že je agent instalován.  Security Center pokusu o instalaci agenta Microsoft Monitoring Agent rozšíření a selže z důvodu existující nainstalovaného agenta.  Toto selhání zabrání přepsání nastavení připojení agenta na jeho pracovní prostor a zabraňuje vytváření více domovských stránek.

> [!NOTE]
> Verze agenta se aktualizuje na nejnovější verzi agenta OMS.  To také platí pro uživatele SCOM.
>
>

### <a name="what-is-the-impact-of-removing-these-extensions"></a>Co je dopad odebrání těchto rozšíření?
Pokud odeberete rozšíření monitorování společnosti Microsoft, Security Center nebude moct shromažďovat data zabezpečení z virtuálního počítače a některé doporučení zabezpečení a výstrahy nejsou k dispozici. Do 24 hodin Security Center zjistí, že chybí rozšíření virtuálního počítače a přeinstaluje rozšíření.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Jak zabráním agentů pro automatickou diagnostiku instalace a prostoru vytváření?
Můžete vypnout automatické zřizování pro vaše předplatná v zásadě zabezpečení, ale to nedoporučujeme. Vypnutí výstrahy a automatické zřizování omezení doporučení služby Security Center. Automatické zřizování je vyžadována pro odběry cenová úroveň Standard. Chcete-li zakázat automatické zřizování:

1. Pokud váš odběr je nakonfigurován pro úroveň Standard, spustit nástroj Zásady zabezpečení pro toto předplatné a vyberte **volné** vrstvy.

   ![Cenová úroveň][1]

2. V dalším kroku vypnout automatické zřizování výběrem **vypnout** na **zásady zabezpečení – shromažďování dat** okno.
   ![Shromažďování dat][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Měli I vyjádření výslovného nesouhlasu instalace agentů pro automatickou diagnostiku a vytváření pracovního prostoru?

> [!NOTE]
> Nezapomeňte si přečíst části [jaké jsou důsledky odhlášení?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) a [doporučené kroky při odhlášení](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) Pokud si zvolíte pro vyjádření výslovného nesouhlasu automatické zřizování.
>
>

Můžete chtít vyjádření výslovného nesouhlasu s automatické zřizování, pokud pro vás platí následující:

- Instalace agentů pro automatickou diagnostiku pomocí služby Security Center platí pro celé předplatné.  Automatická instalace nemůže použít na podmnožinu virtuálních počítačů. Pokud jsou kritické virtuální počítače, které nelze nainstalovat s Microsoft Monitoring Agent, pak by měl zamítnutí automatické zřizování.
- Instalace agenta Microsoft Monitoring Agent rozšíření aktualizuje verze agenta. To platí pro přímé agenta a agenta SCOM. Pokud nainstalovaného agenta SCOM je verze 2012 a upgradu, pokud je SCOM server také verze 2012 může dojít ke ztrátě možnosti správy. Měli byste zvážit, zrušení automatické zřizování, pokud je verze 2012 s nainstalovaným agentem SCOM.
- Pokud máte vlastní pracovní prostor externí k předplatnému (centrálních pracovních prostorů), pak by měl vyjádření výslovného nesouhlasu s automatické zřizování. Můžete ručně nainstalovat agenta Microsoft Monitoring Agent rozšíření a připojte ho pracovního prostoru bez Security Center přepsání připojení.
- Pokud budete chtít vyhnout vytvoření několik pracovních prostorů podle předplatného a máte vlastní vlastní pracovní prostor v rámci předplatného, máte dvě možnosti:

   1. Můžete zvolit automatické zřizování. Po migraci nastavení výchozího nastavení pracovního prostoru jak je popsáno v [jak je možné používat Můj stávající pracovní prostor analýzy protokolů?](#how-can-i-use-my-existing-log-analytics-workspace)
   2. Můžete povolit dokončení agenta Microsoft Monitoring Agent nainstalována na virtuálních počítačích, migrace a virtuální počítače připojené k vytvořeným pracovním prostorem. Potom vyberte vlastní vlastní pracovní prostor nastavení výchozí nastavení pracovního prostoru s vyjádření výslovného souhlasu s rekonfiguraci již nainstalované agenty. Další informace najdete v tématu [jak je možné používat Můj stávající pracovní prostor analýzy protokolů?](#how-can-i-use-my-existing-log-analytics-workspace)

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Jaké jsou důsledky zrušení automatické zřizování?
Po dokončení migrace Security Center není moct shromažďovat data zabezpečení z virtuálního počítače a některé zabezpečení doporučení a výstrahy nejsou k dispozici. Pokud chodit, musíte ručně nainstalovat agenta Microsoft Monitoring Agent. V tématu [doporučené kroky při odhlášení](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Jaké jsou doporučené kroky při zrušení automatické zřizování?
Microsoft Monitoring Agent by měl ručně nainstalovat, aby mohl shromažďovat data zabezpečení z virtuálních počítačů a poskytovat doporučení a výstrahy Security Center. V tématu [počítače se systémem Windows se připojit ke službě Analýza protokolů v Azure](../log-analytics/log-analytics-windows-agent.md) pokyny k instalaci.

Agenta můžete připojit k žádné existující vlastní prostoru nebo Security Center vytvořen pracovní prostor. Pokud vlastní prostoru nemá 'Zabezpečení' nebo 'SecurityCenterFree' řešení, které jsou povolené, pak bude potřeba použít řešení. Pokud chcete použít, vyberte vlastní prostoru nebo předplatného a použít cenovou úroveň prostřednictvím **zásady zabezpečení – cenová úroveň** okno.

   ![Cenová úroveň][1]

Security Center vám umožní správné řešení v pracovním prostoru podle vybraná cenová úroveň.

### <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Odebrání OMS rozšíření nainstalované pomocí služby Security Center
Microsoft Monitoring Agent můžete odebrat ručně. Toto se nedoporučuje, protože se omezuje doporučení služby Security Center a výstrahy.

> [!NOTE]
> Pokud se shromažďování dat je povolené, Security Center bude po odebrání již přeinstalujte agenta.  Je nutné zakázat shromažďování dat před ručním odebráním agenta. V tématu [jak zastavit instalace a prostoru vytváření agentů pro automatickou diagnostiku?](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation?) pokyny pro zakázání shromažďování dat.
>
>

Chcete-li ručně odebrat agenta:

1.  Na portálu, otevřete **analýzy protokolů**.
2.  V okně Log Analytics vyberte pracovní prostor:
3.  Vyberte každý virtuální počítač, který nechcete, aby ke sledování a vyberte **odpojení**.

   ![Odeberte agenta][3]

> [!NOTE]
> Pokud virtuální počítač s Linuxem již má jiné rozšíření agenta OMS, odebírání rozšíření odebere i agenta a zákazník má ji přeinstalovat.
>
>

## <a name="existing-log-analytics-customers"></a>Stávající zákazníky služby analýzy protokolů

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Security Center přepsání všech existujících připojení mezi virtuální počítače a pracovní prostory?
Pokud virtuální počítač už má Microsoft Monitoring Agent nainstalována jako rozšíření Azure, Security Center nemůže přepsat existující připojení pracovního prostoru. Místo toho Security Center používá existujícímu pracovnímu prostoru.

Řešení Security Center je nainstalován v pracovním prostoru není-li již k dispozici, a řešení se použije jenom pro příslušné virtuální počítače. Když přidáte řešení, se automaticky nasadí ve výchozím nastavení všechny agenty systému Windows a Linux připojené k pracovní prostor analýzy protokolů. [Cílení na řešení](../operations-management-suite/operations-management-suite-solution-targeting.md) umožňuje použít obor pro vaše řešení.

Pokud je Microsoft Monitoring Agent nainstalován přímo na virtuálním počítači (ne jako Azure rozšíření), Security Center nenainstaluje agenta Microsoft Monitoring Agent a sledování zabezpečení je omezená.

### <a name="what-should-i-do-if-i-suspect-that-the-data-platform-migration-broke-the-connection-between-one-of-my-vms-and-my-workspace"></a>Co mám dělat, když je pravděpodobné že platformy migrace dat překročila připojení mezi jeden z virtuálních počítačů a pracovního prostoru?
To nemělo stát. Pokud k tomu dojít, pak [vytvoření žádosti o podporu Azure](../azure-supportability/how-to-create-azure-support-request.md) a uveďte následující podrobnosti:

- ID prostředku Azure ovlivněné virtuálního počítače
- ID prostředku Azure pracovního prostoru nakonfigurovaná na rozšíření před připojení bylo přerušeno
- Agent a verzi, která byla dříve nainstalovaná

### <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Security Center se nenainstaluje řešení v mé existující pracovní prostory Log Analytics? Jaké jsou důsledky fakturace?
Pokud Security Center identifikuje, že virtuální počítač je již připojen do pracovního prostoru, který jste vytvořili, Security Center umožňuje řešení na tento pracovní prostor podle cenové úrovně. Řešení se použijí jenom u příslušných virtuálních počítačích Azure, prostřednictvím [cílení na řešení](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting), takže fakturaci zůstává stejná.

- **Úroveň Free** – Security Center nainstaluje řešení 'SecurityCenterFree' v pracovním prostoru. Fakturuje nejsou pro úroveň Free.
- **Úroveň standard** – Security Center nainstaluje řešení, zabezpečení, v pracovním prostoru.

   ![Řešení na výchozí pracovní prostor][4]

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Už mám pracovní prostory v mé prostředí, můžete chci použít ke shromažďování dat zabezpečení?
Pokud virtuální počítač už má Microsoft Monitoring Agent nainstalována jako rozšíření Azure, Security Center používá existující připojené pracovního prostoru. Řešení Security Center je nainstalován v pracovním prostoru není-li již k dispozici a řešení se použije jenom pro příslušné virtuální počítače prostřednictvím [cílení na řešení](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting).

Pokud Security Center nainstaluje agenta Microsoft Monitoring Agent na virtuálních počítačích, používá výchozí pracovních prostorů, vytvořené Security Center. Brzy zákazníci budou moci konfigurovat, které pracovních prostorů se používají.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Už mám řešení zabezpečení na osobní pracovní prostory. Jaké jsou důsledky fakturace?
Řešení a auditu zabezpečení slouží k povolení funkce Security Center standardní úrovně pro virtuální počítače Azure. Pokud řešení zabezpečení a Audit je již nainstalován v pracovním prostoru, Security Center používá existující řešení. Neexistuje žádná změna v fakturace.

## <a name="next-steps"></a>Další postup
Další informace o migraci platformy Security Center najdete v tématu

- [Migrace pro platformu Azure Security Center](security-center-platform-migration.md)
- [Příručka pro řešení potíží s Azure Security Center](security-center-troubleshooting-guide.md)

<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
