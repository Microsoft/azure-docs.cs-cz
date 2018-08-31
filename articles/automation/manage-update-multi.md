---
title: Správa aktualizací pro několik virtuálních počítačů Azure
description: Tento článek popisuje, jak spravovat aktualizace pro virtuální počítače Azure.
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 08/29/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 231a9876c7a84953a7d9a88b761a1da9475d1f48
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43248137"
---
# <a name="manage-updates-for-multiple-machines"></a>Správa aktualizací pro několik počítačů

Řešení Update Management můžete použít ke správě aktualizací a oprav pro virtuální počítače Windows a Linuxem. Z účtu [Azure Automation](automation-offering-get-started.md) můžete:

- Připojit virtuální počítače
- Vyhodnotit stav dostupných aktualizací
- Naplánovat instalaci požadovaných aktualizací
- Zkontrolovat výsledky nasazení za účelem ověření, že aktualizace úspěšně použily na všech virtuálních počítačích, u kterých je povolená Správa aktualizací

## <a name="prerequisites"></a>Požadavky

Chcete-li použít správu aktualizací, budete potřebovat:

- Účet Azure Automation Spustit jako Zjistěte, jak vytvořit, najdete v článku [Začínáme s Azure Automation](automation-offering-get-started.md).
- Virtuální počítač nebo počítač s nainstalovaným jedním z podporovaných operačních systémů.

## <a name="supported-operating-systems"></a>Podporované operační systémy

Správa aktualizací je podporována v následujících operačních systémech:

|Operační systém  |Poznámky  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Podporuje pouze aktualizovat posouzení.         |
|Windows Server 2008 R2 SP1 a novější     |Vyžaduje se Windows PowerShell 4.0 nebo novější. ([Stáhnout WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))</br> Windows PowerShell 5.1 se doporučuje pro zvýšení spolehlivosti. ([Stáhnout WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))         |
|CentOS 6 (x86/x64) a 7 (x64)      | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|Red Hat Enterprise 6 (x86/x64) a 7 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|SUSE Linux Enterprise Server 11 (x86/x64) a 12 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|Ubuntu 12.04 LTS, 14.04 LTS a 16.04 LTS (x86/x64)      |Agenty Linux musí mít přístup k úložišti aktualizací.         |

> [!NOTE]
> Pokud se chcete zabránit tomu, aby se aktualizace používaly mimo časové období údržby v Ubuntu, změňte konfiguraci balíčku Unattended-Upgrade tak, aby automatické aktualizace byly zakázány. Další informace najdete v [tématu věnovaném automatickým aktualizacím v příručce k Ubuntu Serveru](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Agenty Linux musí mít přístup k úložišti aktualizací.

Toto řešení nepodporuje agenta Operations Management Suite (OMS) pro Linux, který je nakonfigurovaný k ukládání dat do několika pracovních prostorů Azure Log Analytics.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Povolení řešení Update Management pro Azure virtual machines

Na webu Azure Portal otevřete svůj účet Automation a pak vyberte **Správa aktualizací**.

Vyberte **přidat virtuální počítače Azure**.

![Přidat kartu virtuálního počítače Azure](./media/manage-update-multi/update-onboard-vm.png)

Vyberte virtuální počítač, který chcete připojit. 

V části **povolit řešení Update Management**vyberte **povolit** pro připojení virtuálního počítače.

![Dialogové okno Povolit správu aktualizací](./media/manage-update-multi/update-enable.png)

Až po dokončení registrace, povolení správy aktualizací pro virtuální počítač.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Povolení správy aktualizací pro počítače a virtuální počítače umístěné mimo Azure

Informace o povolení správy aktualizací pro počítače a virtuální počítače Windows Azure najdete v tématu [počítače Windows se připojit ke službě Log Analytics v Azure](../log-analytics/log-analytics-windows-agent.md).

Další postup pro povolení správy aktualizací pro a virtuální počítače s Linuxem jiné než Azure najdete v tématu [připojení počítačů s Linuxem ke službě Log Analytics](../log-analytics/log-analytics-agent-linux.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Zobrazení počítačů připojených k účtu služby Automation

Po povolení správy aktualizací pro počítače, můžete zobrazit informace o počítači tak, že vyberete **počítače**. Zobrazí se informace *název počítače*, *stav dodržování předpisů*, *prostředí*, *typ operačního systému*, *kritické a nainstalované aktualizace zabezpečení*, *jiné nainstalované aktualizace*, a *připravenost agenta aktualizací* pro počítače.

  ![Zobrazení karty Počítače](./media/manage-update-multi/update-computers-tab.png)

Počítače, které se nedávno byly povoleny pro řízení aktualizace nemusí mít neposoudil. Stav stavu dodržování předpisů pro tyto počítače je **nevyhodnoceno**. Tady je seznam možných hodnot pro stav dodržování předpisů:

- **Kompatibilní**: počítače, které jsou nechybí kritické aktualizace nebo aktualizace zabezpečení.

- **Nekompatibilní**: počítače, kterým chybí minimálně jedna kritická aktualizace nebo aktualizace zabezpečení.

- **Nevyhodnoceno**: data o posouzení aktualizací nebyla přijata od počítače v očekávaném časovém rámci. Pro počítače s Linuxem je časový rámec expect za poslední 3 hodiny. Pro počítače s Windows je očekávaném časovém rámci za posledních 12 hodin.

Chcete-li zobrazit stav agenta, klikněte na odkaz ve **připravenost agenta aktualizací** sloupce. Výběrem této možnosti se otevře **Hybrid Worker** podokně a zobrazuje stav procesu Hybrid Worker. Následující obrázek ukazuje příklad agenta, který nebyl připojen ke správě aktualizací pro delší časové období:

![Zobrazení karty Počítače](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Zobrazení posouzení aktualizací

Po povolení řešení Update Management se otevře podokno **Správa aktualizací**. Na kartě **Chybějící aktualizace** můžete zobrazit seznam chybějících aktualizací.

## <a name="collect-data"></a>Shromažďování dat

Agenty, které jsou nainstalovány na virtuálních počítačích a počítačích shromažďují data o aktualizacích. Agenti odeslat data do Azure Update Management.

### <a name="supported-agents"></a>Podporovaní agenti

Následující tabulka popisuje připojené zdroje, které toto řešení podporuje:

| Připojený zdroj | Podporováno | Popis |
| --- | --- | --- |
| Agenti systému Windows |Ano |Správa aktualizací shromažďuje informace o aktualizacích systému pro agenty Windows a poté zahájí instalaci požadovaných aktualizací. |
| Agenti systému Linux |Ano |Správa aktualizací shromažďuje informace o aktualizacích systému od agentů systému Linux a pak zahajuje instalaci požadovaných aktualizací v podporovaných distribucích. |
| Skupina pro správu Operations Manageru |Ano |Správa aktualizací shromažďuje informace o aktualizacích systému z agentů v připojené skupině pro správu. |
| Účet služby Azure Storage |Ne |Úložiště Azure neobsahuje informace o aktualizacích systému. |

### <a name="collection-frequency"></a>Četnost shromažďování dat

Kontrola provádí dvakrát denně pro každý spravovaný počítač s Windows. Každých 15 minut se volá rozhraní Windows API dotazu na čas poslední aktualizace k určení, jestli se změnil stav. Pokud se stav změnil, zahájí se kontrola kompatibility. Kontrola provádí každé tři hodiny pro každý spravovaný počítač s Linuxem.

Může trvat 30 minut až 6 hodin na řídicím panelu zobrazí aktualizovaná data ze spravovaných počítačů.

## <a name="schedule-an-update-deployment"></a>Naplánování nasazení aktualizace

K instalaci aktualizací, naplánujte nasazení, které odpovídá verzi okno plánu a služby. Můžete zvolit typy aktualizací, které budou součástí nasazení. Můžete například zahrnout důležité aktualizace nebo aktualizace zabezpečení a vyloučit kumulativní aktualizace.

V části naplánovat nové nasazení aktualizací pro jeden nebo více virtuálních počítačů, **Správa aktualizací**vyberte **naplánovat nasazení aktualizací**.

V **nové nasazení aktualizací** podokně zadejte následující informace:

- **Název**: Zadejte jedinečný název pro identifikaci nasazení aktualizace.
- **Operační systém**: vyberte **Windows** nebo **Linux**.
- **Počítače k aktualizaci**: Zvolte Uložit hledání, importované skupiny, nebo vybrat počítače, které chcete vybrat počítače, které chcete aktualizovat. Pokud se rozhodnete **počítače**, připravenosti na počítači se zobrazí v **připravenost agenta aktualizací** sloupce. Zobrazí se stav počítače a před naplánovat nasazení aktualizace. Další informace o různých metod vytváření skupiny počítačů v Log Analytics najdete v tématu [skupiny počítačů v Log Analytics](../log-analytics/log-analytics-computer-groups.md)

  ![Podokno nasazení nové aktualizace](./media/manage-update-multi/update-select-computers.png)

- **Klasifikace aktualizací**: Vyberte typy softwaru, které chcete zahrnout do nasazení aktualizace. Popis typy klasifikace najdete v tématu [klasifikace aktualizací](automation-update-management.md#update-classifications). Typy klasifikace jsou:
  - Důležité aktualizace
  - Aktualizace zabezpečení
  - Kumulativní aktualizace
  - Balíčky funkcí
  - Aktualizace Service Pack
  - Aktualizace definic
  - Nástroje
  - Aktualizace

- **Aktualizace k vyloučení**: Tato volba otevře **vyloučit** stránky. Zadejte článků znalostní báze KB nebo názvy balíčků, které chcete vyloučit.

- **Nastavení plánu:** Můžete přijmout výchozí datum a čas, což je 30 minut od aktuálního času. Můžete také zadat jiný čas.

   Můžete také určit, jestli nasazení proběhne jednou nebo opakovaně. V části Nastavení plánu opakování **opakování**vyberte **periodický**.

   ![Dialogové okno Nastavení plánu](./media/manage-update-multi/update-set-schedule.png)
- **Časové období údržby (minuty)**: Zadejte dobu, po které má dojít k nasazení aktualizací. Toto nastavení pomůže zajistit, že se změny provedou v rámci definovaných časových intervalů pro správu a údržbu.

- **Restartovat ovládací prvek** – toto nastavení určuje, jak se zpracovává restartování počítače pro nasazení aktualizace.

   |Možnost|Popis|
   |---|---|
   |Restartování v případě potřeby| **(Výchozí)**  v případě potřeby je zahájeno restartování, pokud umožňuje časové období údržby.|
   |Vždy restartovat|Bez ohledu na to, zda je požadována je zahájeno restartování. |
   |Nikdy nerestartovat|Bez ohledu na to jsou Pokud restartování se vyžaduje, potlačit restartování počítače.|
   |Pouze restartovat – nenainstalují se aktualizace|Tato možnost bude ignorovat instalaci aktualizací a pouze zahájí restartovat počítač.|

Jakmile dokončíte konfiguraci plánu, vyberte **vytvořit** se vrátit na řídicí panel stavu. **Naplánované** tabulky zobrazí plán nasazení, který jste vytvořili.

## <a name="view-results-of-an-update-deployment"></a>Zobrazení výsledků nasazení aktualizací

Po spuštění naplánovaného nasazení se stav tohoto nasazení zobrazí na kartě **Nasazení aktualizací** v části **Správa aktualizací**.

Pokud je nasazení aktuálně spuštěné, jeho stav je **Probíhá**. Po úspěšném dokončení nasazení se stav změní na **Succeeded**.

Pokud u jedné nebo více aktualizací v nasazení dojde k chybě, stav je **Částečně neúspěšné**.

![Stav nasazení aktualizace](./media/manage-update-multi/update-view-results.png)

Pokud chcete zobrazit řídicí panel pro nasazení aktualizace, vyberte dokončené nasazení.

**Aktualizovat výsledky** podokno zobrazuje celkový počet aktualizací a výsledků nasazení pro virtuální počítač. V tabulce vpravo je podrobný rozpis všech aktualizací a výsledků instalace. Výsledkem instalace může být jedna z následujících hodnot:

- **Nebyl proveden pokus**: aktualizace se nenainstalovala, protože byl dostatek času k dispozici podle definovaného časového období údržby.
- **Úspěch:** Aktualizace byla úspěšná.
- **Neúspěch:** Aktualizace se nezdařila.

Výběrem možnosti **Všechny protokoly** zobrazíte všechny položky protokolu, které toto nasazení vytvořilo.

Pokud chcete zobrazit datový proud úlohy runbooku, který spravuje nasazení aktualizací na cílovém virtuálním počítači, vyberte dlaždici výstup.

Kliknutím na **Chyby** zobrazíte podrobné informace o případných chybách tohoto nasazení.

## <a name="next-steps"></a>Další postup

- Další informace o správě aktualizací, včetně protokolů, výstupu a chyb, naleznete v tématu [řešení Update Management v Azure](../operations-management-suite/oms-solution-update-management.md).
