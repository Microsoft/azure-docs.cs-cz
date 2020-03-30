---
title: Správa aktualizací pro několik virtuálních počítačů Azure
description: Tento článek popisuje, jak spravovat aktualizace pro virtuální počítače Azure a než Azure.
services: automation
ms.subservice: update-management
ms.date: 03/26/2020
ms.topic: conceptual
ms.openlocfilehash: c9a3c88ea0c3e656adf0f8c514b418cfc07c9590
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335772"
---
# <a name="manage-updates-for-multiple-machines"></a>Správa aktualizací pro několik počítačů

Řešení správy aktualizací můžete použít ke správě aktualizací a oprav pro virtuální počítače se systémem Windows a Linux. Z účtu [Azure Automation](automation-offering-get-started.md) můžete:

- Palubní virtuální počítače
- Posouzení stavu dostupných aktualizací
- Naplánovat instalaci požadovaných aktualizací
- Zkontrolujte výsledky nasazení a ověřte, zda byly aktualizace úspěšně použity pro všechny virtuální počítače, pro které je povolena správa aktualizací.

## <a name="prerequisites"></a>Požadavky

Chcete-li používat službu Správa aktualizací, potřebujete:

- Virtuální počítač nebo počítač s nainstalovaným jedním z podporovaných operačních systémů.

- Přístup k úložišti aktualizací pro virtuální počítače s Linuxem na palubě řešení.

Další informace o systémových požadavcích na správu aktualizací naleznete v [tématu Požadavky klienta správy aktualizací](automation-update-management.md#clients).

## <a name="enable-update-management-for-azure-virtual-machines"></a>Povolení správy aktualizací pro virtuální počítače Azure

Na webu Azure Portal otevřete účet Automation a vyberte **Správa aktualizací**.

Vyberte **Přidat virtuální počítače Azure**.

![Karta Přidat virtuální počítač Azure](./media/manage-update-multi/update-onboard-vm.png)

Vyberte virtuální počítač, který chcete připojit.

V části **Povolit správu aktualizací**vyberte **Povolit** připojení virtuálního počítače.

![Dialogové okno Povolit správu aktualizací](./media/manage-update-multi/update-enable.png)

Po dokončení registrace je pro váš virtuální počítač povolena správa aktualizací.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Povolení správy aktualizací pro virtuální počítače a počítače, které nejsou azure

Agent Log Analytics pro Windows a Linux musí být nainstalován na virtuálních počítačích, které jsou spuštěny ve vaší podnikové síti nebo jiném cloudovém prostředí, aby je bylo možné povolit pomocí správy aktualizací. Informace o požadavcích na systém a podporovaných metodách nasazení agenta do počítačů hostovaných mimo Azure najdete [v tématu Přehled agenta Analýzy protokolů](../azure-monitor/platform/log-analytics-agent.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Zobrazení počítačů připojených k účtu automation

Po povolení správy aktualizací pro počítače můžete zobrazit informace o počítači výběrem **možnosti Počítače**. Můžete zobrazit informace o *názvu počítače*, stavu *dodržování předpisů*, *prostředí*, *typu operačního systému*, *nainstalovaných důležitých aktualizacích a aktualizacích zabezpečení*, *dalších nainstalovaných aktualizacích*a *připravenosti agenta aktualizace* pro počítače.

  ![Zobrazení karty Počítače](./media/manage-update-multi/update-computers-tab.png)

Počítače, které byly nedávno povoleny pro správu aktualizací, pravděpodobně ještě nebyly vyhodnoceny. Stav stavu dodržování předpisů pro tyto počítače **není hodnocen**. Tady je seznam možných hodnot pro stav dodržování předpisů:

- **Kompatibilní**: Počítače, které nechybí důležité nebo aktualizace zabezpečení.

- **Nekompatibilní**: Počítače, u kterých chybí alespoň jedna kritická aktualizace nebo aktualizace zabezpečení.

- **Nehodnoceno**: Data hodnocení aktualizace nebyla přijata z počítače v očekávaném časovém rámci. Pro počítače s Linuxem je časový rámec očekávání za poslední hodinu. U počítačů se systémem Windows je očekávaný časový rámec za posledních 12 hodin.

Chcete-li zobrazit stav agenta, vyberte odkaz ve sloupci **Připravenost agenta aktualizace.** Výběrem této možnosti se otevře podokno **Hybridní pracovník** a zobrazí se stav hybridního pracovníka. Následující obrázek znázorňuje příklad agenta, který nebyl delší dobu připojen ke správě aktualizací:

![Zobrazení karty Počítače](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Zobrazení posouzení aktualizací

Po povolení řešení Update Management se otevře podokno **Správa aktualizací**. Na kartě **Chybějící aktualizace** můžete zobrazit seznam chybějících aktualizací.

## <a name="collect-data"></a>Shromažďování dat

Agenti nainstalované ve virtuálních počítačích a počítačích shromažďují data o aktualizacích. Agenti odesílají data do Azure Update Management.

### <a name="supported-agents"></a>Podporovaní agenti

Následující tabulka popisuje připojené zdroje, které toto řešení podporuje:

| Připojený zdroj | Podporuje se | Popis |
| --- | --- | --- |
| Agenti systému Windows |Ano |Správa aktualizací shromažďuje informace o aktualizacích systému od agentů systému Windows a poté inicializuje instalaci požadovaných aktualizací. |
| Agenti systému Linux |Ano |Správa aktualizací shromažďuje informace o aktualizacích systému od agentů Linuxu a poté iniciuje instalaci požadovaných aktualizací podporovaných distribucí. |
| Skupina pro správu Operations Manageru |Ano |Správa aktualizací shromažďuje informace o aktualizacích systému od agentů v připojené skupině pro správu. |
| Účet služby Azure Storage |Ne |Azure Storage neobsahuje informace o aktualizacích systému. |

### <a name="collection-frequency"></a>Četnost shromažďování dat

Poté, co počítač dokončí hledání dodržování předpisů aktualizace, agent předá informace hromadně protokoly Azure Monitor. V počítači se systémem Windows je kontrola dodržování předpisů ve výchozím nastavení spouštěna každých 12 hodin.

Kromě plánu kontroly je kontrola dodržování předpisů aktualizace zahájena do 15 minut od restartování mma, před instalací aktualizace a po instalaci aktualizace.

U počítače se systémem Linux se kontrola dodržování předpisů provádí ve výchozím nastavení každou hodinu. Pokud je agent MMA restartován, kontrola dodržování předpisů je zahájena do 15 minut.

Zobrazení aktuálních dat o spravovaných počítačích může trvat 30 minut až 6 hodin.

## <a name="schedule-an-update-deployment"></a>Naplánování nasazení aktualizace

Chcete-li nainstalovat aktualizace, naplánujte nasazení, které je v souladu s plánem vydání a oknem služby. Můžete zvolit typy aktualizací, které budou součástí nasazení. Můžete například zahrnout důležité aktualizace nebo aktualizace zabezpečení a vyloučit kumulativní aktualizace.

>[!NOTE]
>Při plánování nasazení aktualizace vytvoří [plán](shared-resources/schedules.md) ový prostředek propojený se souborem runbook **Patch-MicrosoftOMSComputers,** který zpracovává nasazení aktualizace v cílových počítačích. Pokud odstraníte prostředek plánu z webu Azure portal nebo pomocí PowerShellu po vytvoření nasazení, přeruší plánované nasazení aktualizace a zobrazí chybu při pokusu o jeho překonfigurali z portálu. Zdroj plánu lze odstranit pouze odstraněním odpovídajícího plánu nasazení.
>

Chcete-li naplánovat nasazení nové aktualizace pro jeden nebo více virtuálních počítačů, vyberte v části **Správa aktualizací** **možnost Naplánovat nasazení aktualizace**.

V podokně **nasazení Nové aktualizace** zadejte následující informace:

- **Název**: Zadejte jedinečný název pro identifikaci nasazení aktualizace.
- **Operační systém**: Vyberte **Windows** nebo **Linux**.
- **Skupiny k aktualizaci**: Definujte dotaz na základě kombinace předplatného, skupin prostředků, umístění a značek a vytvořte dynamickou skupinu virtuálních počítačích Azure, která bude zahrnuta do vašeho nasazení. U virtuálních počítačů, které nejsou virtuálními počítači Azure, se uložená hledání používají k vytvoření dynamické skupiny, která bude zahrnuta do vašeho nasazení. Další informace naleznete v [tématu Dynamické skupiny](automation-update-management-groups.md).
- **Počítače, které chcete aktualizovat**: Vyberte uloženou hledat, importovnou skupinu nebo vyberte stroje, chcete-li vybrat počítače, které chcete aktualizovat.

   >[!NOTE]
   >Výběrem možnosti Uložené hledání se nevracejí identity počítače, pouze jejich názvy. Pokud máte několik virtuálních počítačů se stejným názvem napříč více skupinami prostředků, jsou vráceny ve výsledcích. Pomocí **možnosti Skupiny k aktualizaci** se doporučuje zajistit, že zahrnete jedinečné virtuální počítače odpovídající vašim kritériím.

   Pokud zvolíte **stroje**, připravenost stroje se zobrazí ve sloupci **Připravenost agenta aktualizace.** Můžete vidět stav počítače před naplánováním nasazení aktualizace. Informace o různých způsobech vytváření skupin počítačů v protokolech Azure Monitoru najdete [v tématu Skupiny počítačů v protokolech Azure Monitoru.](../azure-monitor/platform/computer-groups.md)

  ![Podokno nasazení nové aktualizace](./media/manage-update-multi/update-select-computers.png)

- **Klasifikace aktualizací**: Vyberte typy softwaru, které chcete zahrnout do nasazení aktualizace. Popis typů klasifikace naleznete v tématu [Aktualizace klasifikací](automation-view-update-assessments.md#update-classifications). Typy klasifikace jsou:
  - Důležité aktualizace
  - Aktualizace zabezpečení
  - Kumulativní aktualizace
  - Balíčky funkcí
  - Aktualizace Service Pack
  - Aktualizace definic
  - Nástroje
  - Aktualizace

- **Aktualizace, které se mají zahrnout nebo vyloučit** – Otevře stránku **Zahrnout nebo vyloučit**. Aktualizace, které se mají zahrnout nebo vyloučit jsou na samostatných kartách. Další informace o tom, jak je zpracována zahrnutí, naleznete [v tématu Plánování nasazení aktualizace](automation-tutorial-update-management.md#schedule-an-update-deployment).

> [!NOTE]
> Je důležité vědět, že vyloučení přepsat inkluze. Pokud například definujete pravidlo `*`vyloučení aplikace , nebudou nainstalovány žádné opravy ani balíčky, protože jsou všechny vyloučeny. Vyloučené záplaty se stále zobrazují jako chybějící ze stroje. Pro linuxové počítače, pokud je balíček součástí balení, ale má závislý balíček, který byl vyloučen, balíček není nainstalován.

> [!NOTE]
> Nelze zadat aktualizace, které byly nahrazeny pro zahrnutí s nasazením aktualizace.
>

- **Nastavení plánu:** Můžete přijmout výchozí datum a čas, což je 30 minut od aktuálního času. Můžete také zadat jiný čas.

   Můžete také určit, jestli nasazení proběhne jednou nebo opakovaně. Chcete-li nastavit opakovaný plán, vyberte v části **Opakování** **možnost Opakovat**.

   ![Dialogové okno Nastavení plánu](./media/manage-update-multi/update-set-schedule.png)

- **Předzálohovací a pozálohovací skripty**: Vyberte skripty, které se mají spustit před vaším nasazením a po něm. Další informace najdete v tématu týkajícím se [správy předzálohovacích a pozálohovacích skriptů](pre-post-scripts.md).
- **Okno údržby (minuty):** Zadejte dobu, po kterou má dojít k nasazení aktualizace. Toto nastavení pomůže zajistit, že se změny provedou v rámci definovaných časových intervalů pro správu a údržbu.

- **Řízení restartování** – toto nastavení určuje způsob zpracování restartování pro nasazení aktualizace.

   |Možnost|Popis|
   |---|---|
   |V případě potřeby restartujte počítač| **(Výchozí)** V případě potřeby je restartování zahájeno, pokud to okno údržby umožňuje.|
   |Vždy restartovat|Restartování je zahájeno bez ohledu na to, zda je vyžadováno. |
   |Nikdy nerestartovat|Bez ohledu na to, zda je nutné restartovat počítač, restartování jsou potlačeny.|
   |Pouze restartovat – nenainstalují se aktualizace|Tato možnost ignoruje instalaci aktualizací a pouze zahájí restartování.|

Po dokončení konfigurace plánu vyberte tlačítko **Vytvořit** a vraťte se na stavový řídicí panel. **Naplánovaná** tabulka zobrazuje plán nasazení, který jste vytvořili.

> [!NOTE]
> Update Management podporuje nasazování aktualizací od Microsoftu a předběžné stahování oprav. To vyžaduje změny v systémech, které jsou opravy, viz [první strana a pre download podporu](automation-configure-windows-update.md#pre-download-updates) se dozvíte, jak nakonfigurovat tato nastavení ve vašich systémech.

## <a name="view-results-of-an-update-deployment"></a>Zobrazení výsledků nasazení aktualizací

Po spuštění naplánovaného nasazení se stav tohoto nasazení zobrazí na kartě **Nasazení aktualizací** v části **Správa aktualizací**.

Pokud je nasazení aktuálně spuštěné, jeho stav je **Probíhá**. Po úspěšném dokončení nasazení se stav změní na **Succeeded**.

Pokud u jedné nebo více aktualizací v nasazení dojde k chybě, stav je **Částečně neúspěšné**.

![Stav nasazení aktualizace](./media/manage-update-multi/update-view-results.png)

Pokud chcete zobrazit řídicí panel pro nasazení aktualizace, vyberte dokončené nasazení.

Podokno **Výsledky aktualizace** zobrazuje celkový počet aktualizací a výsledky nasazení pro virtuální počítač. Tabulka vpravo poskytuje podrobný rozpis jednotlivých aktualizací a výsledků instalace. Výsledkem instalace může být jedna z následujících hodnot:

- **Nepokusil :** Aktualizace nebyla nainstalována, protože na základě definovaného časového okna údržby nebyl k dispozici dostatek času.
- **Úspěšné**: Aktualizace byla úspěšná.
- **Nezdařilo**se: Aktualizace se nezdařila.

Výběrem možnosti **Všechny protokoly** zobrazíte všechny položky protokolu, které toto nasazení vytvořilo.

Chcete-li zobrazit datový proud úloh sady Runbook, který spravuje nasazení aktualizace v cílovém virtuálním počítači, vyberte výstupní dlaždici.

Kliknutím na **Chyby** zobrazíte podrobné informace o případných chybách tohoto nasazení.

## <a name="next-steps"></a>Další kroky

Další informace o protokolech správy aktualizací, výstupu a chybách naleznete v [tématu Evidence aktualizací dotazu pro službu Správa aktualizací](automation-update-management-query-logs.md).
