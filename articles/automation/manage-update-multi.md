---
title: Správa aktualizací pro několik virtuálních počítačů Azure
description: Tento článek popisuje, jak spravovat aktualizace pro virtuální počítače Azure a mimo Azure.
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 11/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 70f4f4163a143354cd1fe5adf031c4d9cd87a46e
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278662"
---
# <a name="manage-updates-for-multiple-machines"></a>Správa aktualizací pro několik počítačů

Pomocí řešení Update Management můžete spravovat aktualizace a opravy pro virtuální počítače s Windows a Linux. Z účtu [Azure Automation](automation-offering-get-started.md) můžete:

- Připojování virtuálních počítačů
- Vyhodnotit stav dostupných aktualizací
- Naplánovat instalaci požadovaných aktualizací
- Zkontrolujte výsledky nasazení a ověřte, jestli se aktualizace úspěšně nastavily na všechny virtuální počítače, pro které je povolená Update Management.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít Update Management, potřebujete:

- Virtuální počítač nebo počítač s nainstalovaným jedním z podporovaných operačních systémů.

- Přístup k úložišti aktualizací pro virtuální počítače se systémem Linux připojené k řešení.

## <a name="supported-operating-systems"></a>Podporované operační systémy

Update Management se podporuje v těchto operačních systémech:

|Operační systém  |Poznámky:  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Podporuje pouze posouzení aktualizací.         |
|Windows Server 2008 R2 SP1 a novější     |Je vyžadován Windows PowerShell 4,0 nebo novější. ([Stáhnout WMF 4,0](https://www.microsoft.com/download/details.aspx?id=40855))</br> Pro zvýšení spolehlivosti se doporučuje prostředí Windows PowerShell 5,1. ([Stáhnout WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616))         |
|CentOS 6 (x86/x64) a 7 (x64)      | |
|Red Hat Enterprise 6 (x86/x64) a 7 (x64)     | |
|SUSE Linux Enterprise Server 11 (x86/x64) a 12 (x64)     | |
|Ubuntu 14,04 LTS, 16,04 LTS a 18,04 LTS (x86/x64)      | |

> [!NOTE]
> Pokud se chcete zabránit tomu, aby se aktualizace používaly mimo časové období údržby v Ubuntu, změňte konfiguraci balíčku Unattended-Upgrade tak, aby automatické aktualizace byly zakázány. Další informace najdete v [tématu věnovaném automatickým aktualizacím v příručce k Ubuntu Serveru](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

## <a name="enable-update-management-for-azure-virtual-machines"></a>Povolit Update Management pro virtuální počítače Azure

V Azure Portal otevřete svůj účet Automation a pak vyberte **Update Management**.

Vyberte **Přidat virtuální počítače Azure**.

![Přidat kartu virtuálního počítače Azure](./media/manage-update-multi/update-onboard-vm.png)

Vyberte virtuální počítač, který chcete připojit.

V části **povolit Update Management**vyberte **Povolit** , aby se virtuální počítač připojil.

![Dialogové okno Povolit správu aktualizací](./media/manage-update-multi/update-enable.png)

Po dokončení registrace je Update Management pro váš virtuální počítač povolené.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Povolení Update Management pro virtuální počítače a počítače mimo Azure

Agent Log Analytics pro systém Windows a Linux musí být nainstalován na virtuálních počítačích, které jsou spuštěny ve vaší podnikové síti nebo jiném cloudovém prostředí, aby je bylo možné povolit s Update Management. Informace o požadavcích na systém a podporovaných metodách nasazení agenta do počítačů hostovaných mimo Azure najdete v tématu [Přehled agenta Log Analytics](../azure-monitor/platform/log-analytics-agent.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Zobrazit počítače připojené k vašemu účtu Automation

Po povolení Update Management pro vaše počítače můžete zobrazit informace o počítači výběrem možnosti **počítače**. Můžete si prohlédnout informace o *názvu počítače*, *stavu dodržování předpisů*, *prostředí*, *typu operačního systému*, *nainstalovaných důležitých a aktualizacích zabezpečení*, *nainstalovaných dalších aktualizacích*a *aktualizacích připravenosti agenta* pro vaše počítače.

  ![Zobrazení karty Počítače](./media/manage-update-multi/update-computers-tab.png)

Počítače, které byly nedávno povolené pro Update Management, možná ještě nebyly vyhodnoceny. Stav stavu dodržování předpisů pro tyto počítače není **posuzován**. Tady je seznam možných hodnot pro stav dodržování předpisů:

- **Kompatibilní**: počítače, u kterých chybí důležité aktualizace nebo aktualizace zabezpečení.

- **Nekompatibilní**: počítače, ve kterých chybí alespoň jedna kritická aktualizace nebo aktualizace zabezpečení.

- **Nehodnoceno**: data posouzení aktualizace nebyla přijata z počítače v očekávaném časovém rámci. V případě počítačů se systémem Linux se očekává časový rámec za poslední hodinu. V případě počítačů s Windows se očekává časový rámec za posledních 12 hodin.

Chcete-li zobrazit stav agenta, vyberte odkaz ve sloupci **připravenosti agenta aktualizace** . Když vyberete tuto možnost, otevře se podokno **Hybrid Worker** a zobrazí se stav Hybrid Worker. Následující obrázek ukazuje příklad agenta, který nebyl po delší dobu připojen k Update Management:

![Zobrazení karty Počítače](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Zobrazení posouzení aktualizací

Po povolení řešení Update Management se otevře podokno **Správa aktualizací**. Na kartě **Chybějící aktualizace** můžete zobrazit seznam chybějících aktualizací.

## <a name="collect-data"></a>Shromažďování dat

Agenti, kteří jsou nainstalováni na virtuálních počítačích a počítačích, shromažďují data o aktualizacích. Agenti odesílají data do Azure Update Management.

### <a name="supported-agents"></a>Podporovaní agenti

Následující tabulka popisuje připojené zdroje, které toto řešení podporuje:

| Připojený zdroj | Podporuje se | Popis |
| --- | --- | --- |
| Agenti systému Windows |Ano |Update Management shromažďuje informace o aktualizacích systému z agentů Windows a pak inicializuje instalaci požadovaných aktualizací. |
| Agenti systému Linux |Ano |Update Management shromažďuje informace o aktualizacích systému od agentů systému Linux a následně inicializuje instalaci požadovaných aktualizací v podporovaných distribucích. |
| Skupina pro správu Operations Manageru |Ano |Update Management shromažďuje informace o aktualizacích systému z agentů v připojené skupině pro správu. |
| Účet služby Azure Storage |Ne |Azure Storage neobsahují informace o aktualizacích systému. |

### <a name="collection-frequency"></a>Četnost shromažďování dat

Když počítač dokončí kontrolu shody aktualizací, agent přepošle informace hromadně do Azure Monitor protokolů. V počítači se systémem Windows se kontrola kompatibility ve výchozím nastavení spouští každých 12 hodin.

Kromě plánu vyhledávání se kontrola kompatibility aktualizací zahájila do 15 minut od restartování MMA, před instalací aktualizace a po instalaci aktualizace.

U počítače se systémem Linux se kontrola dodržování předpisů provádí ve výchozím nastavení každou hodinu. Pokud se agent MMA restartuje, zahájí se kontrola kompatibility do 15 minut.

Může trvat 30 minut a 6 hodin, než se na řídicím panelu zobrazí aktualizovaná data ze spravovaných počítačů.

## <a name="schedule-an-update-deployment"></a>Naplánování nasazení aktualizace

Pokud chcete nainstalovat aktualizace, naplánujte nasazení, které se zarovnává s plánem vydávání verzí a oknem služby. Můžete zvolit typy aktualizací, které budou součástí nasazení. Můžete například zahrnout důležité aktualizace nebo aktualizace zabezpečení a vyloučit kumulativní aktualizace.

Chcete-li naplánovat nové nasazení aktualizací pro jeden nebo více virtuálních počítačů, vyberte v části **Správa aktualizací**možnost **naplánovat nasazení aktualizace**.

V podokně **nové nasazení aktualizací** zadejte následující informace:

- **Název**: Zadejte jedinečný název pro identifikaci nasazení aktualizace.
- **Operační systém**: vyberte **Windows** nebo **Linux**.
- **Skupiny, které se mají aktualizovat**: Definujte dotaz založený na kombinaci předplatného, skupin prostředků, umístění a značek, abyste vytvořili dynamickou skupinu virtuálních počítačů Azure, které chcete zahrnout do nasazení. V případě virtuálních počítačů mimo Azure se uložená hledání použijí k vytvoření dynamické skupiny pro zahrnutí do nasazení. Další informace najdete v tématu [dynamické skupiny](automation-update-management-groups.md).
- **Počítače k aktualizaci**: pro výběr počítačů, které chcete aktualizovat, vyberte uložené hledání, importovanou skupinu nebo vyberte počítače.

   >[!NOTE]
   >Vyberete-li možnost uloženého hledání, nebudou vraceny identity počítačů, pouze jejich názvy. Pokud máte v několika skupinách prostředků několik virtuálních počítačů se stejným názvem, vrátí se výsledky. Použití možnosti **skupiny k aktualizaci** se doporučuje k tomu, abyste zajistili zahrnutí jedinečných virtuálních počítačů, které odpovídají vašim kritériím.

   Pokud zvolíte možnost **Počítače**, ve sloupci **PŘIPRAVENOST AGENTA AKTUALIZACE** se zobrazí připravenost počítačů. Před naplánováním nasazení aktualizace se můžete podívat na stav počítače. Další informace o různých metodách vytváření skupin počítačů v protokolu Azure Monitor najdete v tématu [skupiny počítačů v protokolech Azure monitor](../azure-monitor/platform/computer-groups.md)

  ![Podokno nasazení nové aktualizace](./media/manage-update-multi/update-select-computers.png)

- **Klasifikace aktualizace**: Vyberte typy softwaru, které se mají zahrnout do nasazení aktualizace. Popis typů klasifikace najdete v tématu [klasifikace aktualizací](automation-view-update-assessments.md#update-classifications). Typy klasifikace jsou:
  - Důležité aktualizace
  - Aktualizace zabezpečení
  - Kumulativní aktualizace
  - Balíčky funkcí
  - Aktualizace Service Pack
  - Aktualizace definic
  - Nástroje
  - Aktualizace

- **Aktualizace, které se mají zahrnout nebo vyloučit** – Otevře stránku **Zahrnout nebo vyloučit**. Aktualizace, které se mají zahrnout nebo vyloučit jsou na samostatných kartách. Další informace o způsobu zpracování zařazení najdete v tématu [Naplánování nasazení aktualizací](automation-tutorial-update-management.md#schedule-an-update-deployment).

- **Nastavení plánu:** Můžete přijmout výchozí datum a čas, což je 30 minut od aktuálního času. Můžete také zadat jiný čas.

   Můžete také určit, jestli nasazení proběhne jednou nebo opakovaně. Pokud chcete nastavit plán opakování, vyberte v části **opakování**možnost **opakující**se.

   ![Dialogové okno Nastavení plánu](./media/manage-update-multi/update-set-schedule.png)

- **Předzálohovací a pozálohovací skripty**: Vyberte skripty, které se mají spustit před vaším nasazením a po něm. Další informace najdete v tématu týkajícím se [správy předzálohovacích a pozálohovacích skriptů](pre-post-scripts.md).
- Časový **interval pro správu a údržbu (minuty)** : zadejte časové období, po které má dojít k nasazení aktualizací. Toto nastavení pomůže zajistit, že se změny provedou v rámci definovaných časových intervalů pro správu a údržbu.

- **Řízení restartování** – toto nastavení určuje, jak se budou zpracovávat restartování pro nasazení aktualizace.

   |Možnost|Popis|
   |---|---|
   |Restartovat v případě potřeby| **(Výchozí)** V případě potřeby se iniciuje restart, pokud je okno údržby povoleno.|
   |Vždy restartovat|Restart se iniciuje bez ohledu na to, jestli je nějaký povinný. |
   |Nikdy nerestartovat|Bez ohledu na to, jestli je restart nutný, se restarty potlačí.|
   |Pouze restartovat – nenainstalují se aktualizace|Tato možnost ignoruje instalaci aktualizací a inicializuje jenom restartování.|

Po dokončení konfigurace plánu vyberte tlačítko **vytvořit** a vraťte se na řídicí panel stavu. **Naplánovaná** tabulka zobrazuje plán nasazení, který jste vytvořili.

> [!NOTE]
> Update Management podporuje nasazování aktualizací od Microsoftu a předběžné stahování oprav. To vyžaduje změny v systémech, které jsou opravené, další informace o tom, jak nakonfigurovat tato nastavení v systémech, najdete v článku [Podpora první strany a předběžného stažení](automation-configure-windows-update.md#pre-download-updates) .

## <a name="view-results-of-an-update-deployment"></a>Zobrazení výsledků nasazení aktualizací

Po spuštění naplánovaného nasazení se stav tohoto nasazení zobrazí na kartě **Nasazení aktualizací** v části **Správa aktualizací**.

Pokud je nasazení aktuálně spuštěné, jeho stav je **Probíhá**. Po úspěšném dokončení nasazení se stav změní na **úspěch**.

Pokud u jedné nebo více aktualizací v nasazení dojde k chybě, stav je **Částečně neúspěšné**.

![Stav nasazení aktualizace](./media/manage-update-multi/update-view-results.png)

Pokud chcete zobrazit řídicí panel pro nasazení aktualizace, vyberte dokončené nasazení.

V podokně **aktualizovat výsledky** se zobrazuje celkový počet aktualizací a výsledky nasazení pro virtuální počítač. Tabulka na pravé straně poskytuje podrobný rozpis každé aktualizace a výsledky instalace. Výsledkem instalace může být jedna z následujících hodnot:

- **Neproběhl pokus**: aktualizace nebyla nainstalována, protože na základě definovaného časového období údržby bylo k dispozici dostatek času.
- **Úspěch:** Aktualizace byla úspěšná.
- **Neúspěch:** Aktualizace se nezdařila.

Výběrem možnosti **Všechny protokoly** zobrazíte všechny položky protokolu, které toto nasazení vytvořilo.

Chcete-li zobrazit datový proud úlohy Runbooku, který spravuje nasazení aktualizace na cílovém virtuálním počítači, vyberte dlaždici výstup.

Kliknutím na **Chyby** zobrazíte podrobné informace o případných chybách tohoto nasazení.

## <a name="next-steps"></a>Další kroky

Další informace o Update Management, včetně protokolů, výstupu a chyb, najdete v tématu [Update Management řešení v Azure](../operations-management-suite/oms-solution-update-management.md).

