---
title: Správa aktualizací pro několik virtuálních počítačů Azure
description: Tento článek popisuje, jak provádět správu aktualizací pro virtuální počítače Azure.
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 04/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 59a00f5605f7664148b65f2ec9a88fbaa9057ccf
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2018
ms.locfileid: "36946053"
---
# <a name="manage-updates-for-multiple-machines"></a>Správa aktualizací pro několik počítačů

Řešení pro správu aktualizací můžete spravovat aktualizace a opravy pro virtuální počítače Windows a Linux. Z účtu [Azure Automation](automation-offering-get-started.md) můžete:

- Připojit virtuální počítače
- Vyhodnocení stavu k dispozici aktualizace
- Plán instalace požadovaných aktualizací
- Zkontrolujte výsledky nasazení ověřte, zda byly aktualizace úspěšně použita pro všechny virtuální počítače, pro které je povolena Správa aktualizací

## <a name="prerequisites"></a>Požadavky

Chcete-li použít správy aktualizací, je třeba:

- Účet Azure Automation Spustit jako Zjistěte, jak vytvořit, najdete v tématu [Začínáme s Azure Automation](automation-offering-get-started.md).
- Virtuální počítač nebo počítač s nainstalovaným jedním z podporovaných operačních systémů.

## <a name="supported-operating-systems"></a>Podporované operační systémy

Správa aktualizací je podporován v následujících operačních systémů:

|Operační systém  |Poznámky  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Podporuje pouze aktualizovat vyhodnocování.         |
|Windows Server 2008 R2 SP1 a novější     |Vyžaduje se prostředí Windows PowerShell 4.0 nebo novější. ([Stáhnout WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))</br> Windows PowerShell 5.1 se doporučuje pro větší spolehlivost. ([Stáhnout WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))         |
|CentOS 6 (x86/x64) a 7 (x64)      | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|Red Hat Enterprise 6 (x86/x64) a 7 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|SUSE Linux Enterprise Server 11 (x86/x64) a 12 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|Ubuntu 12.04 LTS, 14.04 LTS a 16.04 LTS (x86/x64)      |Agenty Linux musí mít přístup k úložišti aktualizací.         |

> [!NOTE]
> Pokud se chcete zabránit tomu, aby se aktualizace používaly mimo časové období údržby v Ubuntu, změňte konfiguraci balíčku Unattended-Upgrade tak, aby automatické aktualizace byly zakázány. Další informace najdete v [tématu věnovaném automatickým aktualizacím v příručce k Ubuntu Serveru](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Agenty Linux musí mít přístup k úložišti aktualizací.

Toto řešení nepodporuje agenta Operations Management Suite (OMS) pro Linux, který je nakonfigurovaný k několik pracovních prostorů Azure Log Analytics sestavy.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Povolení správy aktualizací pro virtuální počítače Azure

Na portálu Azure otevřete účet Automation a pak vyberte **Správa aktualizací**.

Vyberte **přidat virtuální počítače Azure**.

![Přidat virtuální počítač Azure kartu](./media/manage-update-multi/update-onboard-vm.png)

Vyberte virtuální počítač, který chcete připojit. 

V části **povolit správu aktualizace**, vyberte **povolit** se budou registrovat virtuální počítač.

![Dialogové okno Povolit správu aktualizací](./media/manage-update-multi/update-enable.png)

Až po dokončení registrace, Správa aktualizací je povolena pro virtuální počítač.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Povolení správy aktualizací pro virtuální počítače mimo Azure a počítačů

Informace o povolení správy aktualizací pro počítače a virtuální počítače s Azure Windows najdete v tématu [počítače se systémem Windows se připojit ke službě Analýza protokolů v Azure](../log-analytics/log-analytics-windows-agent.md).

Informace o povolení správy aktualizací pro virtuální počítače Azure Linux a počítače, v tématu [připojení počítačů Linux k analýze protokolů](../log-analytics/log-analytics-agent-linux.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Zobrazit počítače připojené k účtu Automation

Po povolení správy aktualizací pro počítače, můžete zobrazit informace o počítači tak, že vyberete **počítače**. Zobrazí informace *název počítače*, *stav dodržování předpisů*, *prostředí*, *typ operačního systému*, *kritické a nainstalované aktualizace zabezpečení*, *jiné aktualizace, nainstalovaný*, a *aktualizovat agenta připravenosti* pro počítače.

  ![Zobrazení karty Počítače](./media/manage-update-multi/update-computers-tab.png)

Počítače, které byly povoleny nedávno ke správě aktualizací nemusí byly vyhodnoceny ještě. Stav stavu dodržování předpisů pro tyto počítače je **není vyhodnocení**. Tady je seznam možných hodnot pro stavu dodržování předpisů:

- **Kompatibilní**: počítače, které není chybí důležité aktualizace nebo aktualizace zabezpečení.

- **Nekompatibilní**: počítače, které chybí minimálně jeden kritické nebo aktualizaci zabezpečení.

- **Není vyhodnocení**: data pro vyhodnocení aktualizace nebyl přijat z počítače v očekávané časovém rámci. U počítačů, Linux je časový rámec expect v poslední 3 hodiny. Pro počítače se systémem Windows je očekávané časový rámec za posledních 12 hodin.

Chcete-li zobrazit stav agenta, vyberte odkaz v **aktualizace agenta připravenosti** sloupce. Výběrem této možnosti se otevře **hybridní pracovní proces** podokně a zobrazuje stav hybridní pracovní proces. Následující obrázek ukazuje příklad agenta, který nebyl připojen k správy aktualizací pro delší dobu:

![Zobrazení karty Počítače](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Zobrazení posouzení aktualizací

Po povolení správy aktualizací **Správa aktualizací** otevře se podokno. Na kartě **Chybějící aktualizace** můžete zobrazit seznam chybějících aktualizací.

## <a name="collect-data"></a>Shromažďování dat

Agenti, kteří jsou nainstalováni na virtuální počítače a počítače shromažďovat data o aktualizacích. Agenti posílat data do Azure aktualizace Management.

### <a name="supported-agents"></a>Podporovaní agenti

Následující tabulka popisuje připojené zdroje, které toto řešení podporuje:

| Připojený zdroj | Podporováno | Popis |
| --- | --- | --- |
| Agenti systému Windows |Ano |Správa aktualizací shromažďuje informace o aktualizacích systému z agentů v systému Windows a poté zahájí instalaci požadovaných aktualizací. |
| Agenti systému Linux |Ano |Správa aktualizací shromažďuje informace o aktualizacích systému od Linux agentů a poté zahájí instalaci požadovaných aktualizací na podporované distribuce. |
| Skupina pro správu Operations Manageru |Ano |Správa aktualizací shromažďuje informace o aktualizacích systému od agentů v připojené skupiny pro správu. |
| Účet služby Azure Storage |Ne |Úložiště Azure, neobsahuje informace o aktualizacích systému. |

### <a name="collection-frequency"></a>Četnost shromažďování dat

Spustí kontrolu dvakrát denně pro každého spravovaného počítače systému Windows. Každých 15 minut, se nazývá rozhraní API systému Windows k dotazu pro čas poslední aktualizace k určení, zda došlo ke změně stavu. Pokud se stav změnil, spustí se kontroly dodržování předpisů. Spustí kontrolu každých 3 hodiny pro každého spravovaného počítače systému Linux.

Může trvat až 30 minut, 6 hodin pro řídicí panel zobrazit aktualizovaná data ze spravovaných počítačů.

## <a name="schedule-an-update-deployment"></a>Naplánování nasazení aktualizace

K instalaci aktualizací, naplánujte nasazení, které zarovnaná s vaší verze okno plán a služby. Můžete zvolit typy aktualizací, které budou součástí nasazení. Můžete například zahrnout důležité aktualizace nebo aktualizace zabezpečení a vyloučit kumulativní aktualizace.

Při plánování nového nasazení aktualizace pro jeden nebo více virtuálních počítačů, v části **Správa aktualizací**, vyberte **nasazení aktualizace plánu**.

V **nové nasazení aktualizace** podokně zadejte následující informace:

- **Název**: Zadejte jedinečný název pro identifikaci nasazení aktualizace.
- **Operační systém**: vyberte **Windows** nebo **Linux**.
- **Počítače, které chcete aktualizovat**: vyberte virtuální počítače, které chcete aktualizovat. Připravenost počítače se zobrazí v **aktualizace agenta připravenosti** sloupce. Zobrazí se stav počítače a před naplánování nasazení aktualizace.

  ![Nové podokno aktualizace nasazení](./media/manage-update-multi/update-select-computers.png)

- **Klasifikace aktualizací**: Vyberte typy softwaru, které chcete zahrnout do nasazení aktualizace. Popis typů klasifikace najdete v tématu [klasifikace aktualizací](automation-update-management.md#update-classifications). Typy klasifikace jsou:
  - Důležité aktualizace
  - Aktualizace zabezpečení
  - Kumulativní aktualizace
  - Balíčky funkcí
  - Aktualizace Service Pack
  - Aktualizace definic
  - Nástroje
  - Aktualizace

- **Aktualizace pro vyloučení**: Výběrem této možnosti otevře **vyloučit** stránky. Zadejte článků znalostní báze KB nebo názvy balíčků, které chcete vyloučit.

- **Nastavení plánu:** Můžete přijmout výchozí datum a čas, což je 30 minut od aktuálního času. Můžete také zadat jiný čas.

   Můžete také určit, jestli nasazení proběhne jednou nebo opakovaně. V části Nastavení plánu opakování **opakování**, vyberte **opakovaná**.

   ![Dialogové okno Nastavení plánu](./media/manage-update-multi/update-set-schedule.png)
- **Časový interval (v minutách) pro údržbu**: Zadejte dobu, kterou chcete nasazení aktualizace proběhnout. Toto nastavení pomůže zajistit, že se změny provedou v rámci definovaných časových intervalů pro správu a údržbu.

Po dokončení konfigurace plánu, vyberte **vytvořit** tlačítko vrátit na řídicí panel stavu. **Naplánovaná** tabulka ukazuje plán nasazení, které jste vytvořili.

> [!WARNING]
> Aktualizace, které vyžadují restartování virtuální počítač automaticky restartuje.

## <a name="view-results-of-an-update-deployment"></a>Zobrazení výsledků nasazení aktualizací

Po spuštění naplánované nasazení, stav pro toto nasazení můžete zobrazit na **nasazení aktualizací** v části **Správa aktualizací**.

Pokud je nasazení aktuálně spuštěné, jeho stav je **Probíhá**. Po dokončení nasazení úspěšně, stav se změní na **úspěšné**.

Pokud u jedné nebo více aktualizací v nasazení dojde k chybě, stav je **Částečně neúspěšné**.

![Stav nasazení aktualizace](./media/manage-update-multi/update-view-results.png)

Pokud chcete zobrazit řídicí panel pro nasazení aktualizace, vyberte dokončené nasazení.

**Aktualizaci výsledků** podokně zobrazuje celkový počet aktualizací a výsledky nasazení pro virtuální počítač. V tabulce na pravé straně poskytuje podrobné rozpis jednotlivé aktualizace a výsledky instalace. Výsledkem instalace může být jedna z následujících hodnot:

- **Nebyl proveden pokus o**: aktualizace nebyla nainstalována, protože byl dostatek času k dispozici založené na definovaných údržby.
- **Úspěšné**: aktualizace byla úspěšná.
- **Se nezdařilo**: aktualizace se nezdařila.

Výběrem možnosti **Všechny protokoly** zobrazíte všechny položky protokolu, které toto nasazení vytvořilo.

Pokud chcete zobrazit stream úloh sady runbook, který spravuje nasazení aktualizací na cílový virtuální počítač, vyberte dlaždici výstup.

Kliknutím na **Chyby** zobrazíte podrobné informace o případných chybách tohoto nasazení.

## <a name="next-steps"></a>Další postup

- Další informace o správě aktualizací, včetně protokolů, výstupní a chyby, najdete v části [řešení správy aktualizací v Azure](../operations-management-suite/oms-solution-update-management.md).
