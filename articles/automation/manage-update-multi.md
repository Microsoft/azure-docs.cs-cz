---
title: "Správa aktualizací pro několik virtuálních počítačů Azure"
description: "Toto téma popisuje, jak se spravují aktualizace pro virtuální počítače Azure."
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 29ab87b6afe31b7bbb40ef7d743e209f86811c9d
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="manage-updates-for-multiple-machines"></a>Správa aktualizací pro několik počítačů

Správa aktualizací umožňuje spravovat aktualizace a opravy pro počítače s Windows nebo Linuxem. Z účtu [Azure Automation](automation-offering-get-started.md) můžete:

- Připojit virtuální počítače
- Vyhodnotit stav dostupných aktualizací
- Naplánovat instalaci požadovaných aktualizací
- Zkontrolovat výsledky nasazení a ověřit, jestli se aktualizace úspěšně použily pro všechny virtuální počítače, pro které je povolená správa aktualizací.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít správu aktualizací, budete potřebovat:

* Účet Azure Automation Spustit jako Pokyny pro jeho vytvoření najdete v tématu [Začínáme s Azure Automation](automation-offering-get-started.md).

* Virtuální počítač nebo počítač s nainstalovaným jedním z podporovaných operačních systémů.

## <a name="supported-operating-systems"></a>Podporované operační systémy

Správa aktualizací je podporována v následujících operačních systémech.

### <a name="windows"></a>Windows

* Windows Server 2008 nebo novější a nasazení aktualizací do Windows Serveru 2008 R2 SP1 a novějšího Nano Server není podporován.

  Podpora pro nasazování aktualizací do Windows Serveru 2008 R2 SP1 vyžaduje .NET Framework 4.5 a Windows Management Framework 5.0 nebo novější.

* Klientské operační systémy Windows nejsou podporované.

Agenti Windows musí být buď nakonfigurovaní na komunikaci se službou Windows Server Update Services (WSUS), nebo musí mít přístup ke službě Microsoft Update.

> [!NOTE]
> Agenta Windows není možné spravovat současně s nástrojem System Center Configuration Manager.
>

### <a name="linux"></a>Linux

* CentOS 6 (x86/x64) a 7 (x64)  
* Red Hat Enterprise 6 (x86/x64) a 7 (x64)  
* SUSE Linux Enterprise Server 11 (x86/x64) a 12 (x64)  
* Ubuntu 12.04 LTS a novější (x86/x64)   

> [!NOTE]  
> Pokud se chcete zabránit tomu, aby se aktualizace používaly mimo časové období údržby v Ubuntu, změňte konfiguraci balíčku Unattended-Upgrade tak, aby automatické aktualizace byly zakázány. Další informace najdete v [tématu věnovaném automatickým aktualizacím v příručce k Ubuntu Serveru](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Agenty Linux musí mít přístup k úložišti aktualizací.

Toto řešení nepodporuje Agenta sady OMS pro Linux, který je nakonfigurovaný tak, aby předával data do několika pracovních prostorů sady Operations Management Suite.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Povolení správy aktualizací pro virtuální počítače Azure

1. Na webu Azure Portal otevřete účet Automation.
2. V levém podokně vyberte **Správa aktualizací**.
3. V horní části okna vyberte **Přidat virtuální počítač Azure**.
   ![Karta Přidat virtuální počítač Azure](./media/manage-update-multi/update-onboard-vm.png)
4. Vyberte virtuální počítač, který chcete připojit. Zobrazí se dialogové okno **Povolit správu aktualizací**.
5. Vyberte **Povolit**.

   ![Dialogové okno Povolit správu aktualizací](./media/manage-update-multi/update-enable.png)

Správa aktualizací pro váš virtuální počítač je povolena.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Povolení správy aktualizací pro počítače a virtuální počítače jiné než Azure

Pokyny k povolení správy aktualizací pro počítače a virtuální počítače s Windows jiné než Azure najdete v popisu [připojení počítačů s Windows ke službě Log Analytics v Azure](../log-analytics/log-analytics-windows-agent.md).

Pokyny k povolení správy aktualizací pro počítače a virtuální počítače s Linuxem jiné než Azure najdete v tématu věnovaném [připojení počítačů s Linuxem k Log Analytics](../log-analytics/log-analytics-agent-linux.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Zobrazení počítačů připojených k účtu Automation
Jakmile pro vaše počítače povolíte správu aktualizací, můžete o nich zobrazit související informace kliknutím na **Počítače**. K dispozici jsou informace o počítači, jako například *Název*, *Dodržování předpisů*, *Prostředí*, *Typ operačního systému*, *Kritické aktualizace a aktualizace zabezpečení* a *Další aktualizace*. 

  ![Zobrazení karty Počítače](./media/manage-update-multi/update-computers-tab.png)

Pro počítače, u kterých byla správa aktualizací povolená nedávno, tyto informace ještě nemusely být vyhodnocené. Stav dodržování předpisů pro tyto počítače bude *Nevyhodnoceno*.  Tady je seznam hodnot pro stav dodržování předpisů:
* Odpovídající – Počítače, kterým nechybí kritické aktualizace nebo aktualizace zabezpečení.
* Neodpovídající – Počítače, kterým chybí minimálně jedna kritická aktualizace nebo aktualizace zabezpečení.
* Nevyhodnoceno – Data o posouzení aktualizací nebyla z počítače přijata v očekávaném časovém rámci.  Pro počítače s Linuxem to jsou poslední tři hodiny a pro počítače s Windows to je posledních 12 hodin.  

## <a name="view-an-update-assessment"></a>Zobrazení posouzení aktualizací

Po povolení správy aktualizací se zobrazí dialogové okno **Správa aktualizací**. Na kartě **Chybějící aktualizace** můžete zobrazit seznam chybějících aktualizací.

## <a name="collect-data"></a>Shromažďování dat

Agenti nainstalovaní na virtuálních počítačích a počítačích shromažďují data o aktualizacích a odesílají je do správy aktualizací Azure.

### <a name="supported-agents"></a>Podporovaní agenti

Následující tabulka popisuje připojené zdroje, které toto řešení podporuje:

| Připojený zdroj | Podporováno | Popis |
| --- | --- | --- |
| Agenti systému Windows |Ano |Správa aktualizací shromažďuje informace o aktualizacích systému pro agenty Windows a inicializuje instalaci požadovaných aktualizací. |
| Agenti systému Linux |Ano |Správa aktualizací shromažďuje informace o aktualizacích systému od agentů systému Linux a zahajuje instalaci požadovaných aktualizací v podporovaných distribucích. |
| Skupina pro správu Operations Manageru |Ano |Správa aktualizací shromažďuje informace o aktualizacích systému z agentů v připojené skupině pro správu. |
| Účet služby Azure Storage |Ne |Úložiště Azure neobsahuje informace o aktualizacích systému. |

### <a name="collection-frequency"></a>Četnost shromažďování dat

Pro každý spravovaný počítač s Windows se kontrola provádí dvakrát denně. Každých 15 minut se volá rozhraní Windows API pro zadání dotazu na čas poslední aktualizace, podle kterého se zjistí, jestli se změnil stav. Pokud ano, zahájí se kontrola kompatibility. Pro každý spravovaný počítač s Linuxem se kontrola provádí každé tři hodiny.

Může trvat 30 minut až 6 hodin, než se na řídicím panelu zobrazí aktualizovaná data ze spravovaných počítačů.

## <a name="schedule-an-update-deployment"></a>Naplánování nasazení aktualizace

Pokud chcete nainstalovat aktualizace, naplánujte nasazení odpovídající vašemu plánu vydávání a časovému intervalu pro správu a údržbu.
Můžete zvolit typy aktualizací, které budou součástí nasazení. Můžete například zahrnout důležité aktualizace nebo aktualizace zabezpečení a vyloučit kumulativní aktualizace.

Naplánujte nové nasazení aktualizací pro jeden nebo více virtuálních počítačů výběrem **Naplánovat nasazení aktualizací** v horní části dialogového okna **Správa aktualizací**. V podokně **Nové nasazení aktualizace** zadejte následující údaje:

* **Název:** Zadejte jedinečný název pro identifikaci nasazení aktualizace.
* **Typ operačního systému:** Vyberte Windows nebo Linux.
* **Počítače k aktualizaci:** Vyberte virtuální počítače, které chcete aktualizovat.

  ![Podokno Nové nasazení aktualizace](./media/manage-update-multi/update-select-computers.png)

* **Klasifikace aktualizací:** Vyberte typy softwaru, které bude nasazení aktualizace zahrnovat. Typy klasifikace jsou:
  * Důležité aktualizace
  * Aktualizace zabezpečení
  * Kumulativní aktualizace
  * Balíčky funkcí
  * Aktualizace Service Pack
  * Aktualizace definic
  * Nástroje
  * Aktualizace
* **Nastavení plánu:** Můžete přijmout výchozí datum a čas, což je 30 minut od aktuálního času. Můžete ale zadat i jiný čas.
   Můžete také určit, jestli nasazení proběhne jednou nebo opakovaně. Pokud chcete nastavit plán opakování, klikněte na možnost **Opakuje se** v části **Opakování**.

   ![Dialogové okno Nastavení plánu](./media/manage-update-multi/update-set-schedule.png)

* **Časové období údržby (minuty):** Zadejte časové období, ve kterém má dojít k nasazení aktualizace. Toto nastavení pomůže zajistit, že se změny provedou v rámci definovaných časových intervalů pro správu a údržbu.

Jakmile dokončíte konfiguraci plánu, klikněte na tlačítko **Vytvořit**. Vrátíte se na řídicí panel stavu. V tabulce **Naplánováno** se zobrazí plán nasazení, který jste právě vytvořili.

> [!WARNING]
> V případě aktualizací, které vyžadují restartování, proběhne restartování virtuálního počítače automaticky.

## <a name="view-results-of-an-update-deployment"></a>Zobrazení výsledků nasazení aktualizací

Po spuštění naplánovaného nasazení se stav tohoto nasazení zobrazí na kartě **Nasazení aktualizace** v dialogovém okně **Správa aktualizací**.
Pokud je nasazení aktuálně spuštěné, jeho stav je **Probíhá**. Po úspěšném dokončení nasazení se stav změní na **Úspěch**.
Pokud u jedné nebo více aktualizací v nasazení dojde k chybě, stav je **Částečně neúspěšné**.

![Stav nasazení aktualizace](./media/manage-update-multi/update-view-results.png)

Pokud chcete zobrazit řídicí panel pro nasazení aktualizace, vyberte dokončené nasazení.

V podokně **Aktualizovat výsledky** se zobrazí celkový počet aktualizací a výsledky nasazení na virtuálním počítači.
V tabulce vpravo je podrobný rozpis všech aktualizací a výsledků instalace. Výsledkem instalace může být jedna z následujících hodnot:

* Nebyl provedený pokus: Aktualizace se nenainstalovala, protože na základě definovaného časového období údržby nebyl k dispozici dostatek času.
* Úspěch: Aktualizace byla úspěšná.
* Neúspěch: Aktualizace se nezdařila.

Výběrem možnosti **Všechny protokoly** zobrazíte všechny položky protokolu, které toto nasazení vytvořilo.

Pokud chcete zobrazit datový proud úlohy runbooku, který spravuje nasazení aktualizací na cílovém virtuálním počítači, vyberte dlaždici **Výstup**.

Kliknutím na **Chyby** zobrazíte podrobné informace o případných chybách tohoto nasazení.

## <a name="next-steps"></a>Další postup

* Další informace o správě aktualizací, včetně protokolů, výstupu a chyb, najdete v tématu věnovaném [řešení Update Management v OMS](../operations-management-suite/oms-solution-update-management.md).

