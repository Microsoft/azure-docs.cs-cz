---
title: Správa aktualizací pro více virtuálních počítačů v Azure Automation
description: V tomto článku se dozvíte, jak spravovat aktualizace pro víc virtuálních počítačů.
services: automation
ms.subservice: update-management
ms.date: 03/26/2020
ms.topic: conceptual
ms.openlocfilehash: a2d16bdca18b7fc0afab2a3deb325d1a75be3bb8
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185156"
---
# <a name="manage-updates-for-multiple-vms"></a>Správa aktualizací pro několik virtuálních počítačů

Pomocí Azure Automation Update Management můžete spravovat aktualizace a opravy pro virtuální počítače s Windows a Linux. Z účtu [Azure Automation](./index.yml) můžete:

- Povolte virtuální počítače pro správu aktualizací.
- Vyhodnotit stav dostupných aktualizací
- Naplánovat instalaci požadovaných aktualizací
- Zkontrolujte výsledky nasazení a ověřte, jestli se aktualizace úspěšně nastavily na všechny virtuální počítače, pro které je povolená Update Management.

Další informace o požadavcích na systém pro Update Management najdete v tématu [Update Management požadavky klienta](automation-update-management.md#client-requirements).

## <a name="prerequisites"></a>Požadavky

* VIRTUÁLNÍ počítač nebo počítač s některým z podporovaných operačních systémů.
* Přístup k úložišti aktualizací pro virtuální počítače se systémem Linux povolených pro Update Management.

## <a name="enable-update-management-for-azure-vms"></a>Povolení Update Management pro virtuální počítače Azure

1. V Azure Portal otevřete svůj účet Automation a pak vyberte **Update Management**.

2. Vyberte **Přidat virtuální počítače Azure**.

    ![Karta Přidat virtuální počítač Azure](./media/manage-update-multi/update-onboard-vm.png)

3. Vyberte virtuální počítač, který chcete povolit, a vyberte **Povolit** v části **Povolit Update Management**.

    ![Dialogové okno Povolit správu aktualizací](./media/manage-update-multi/update-enable.png)

    Po dokončení operace se ve vašem VIRTUÁLNÍm počítači povolí Update Management.

## <a name="enable-update-management-for-non-azure-vms-and-computers"></a>Povolit Update Management pro virtuální počítače a počítače mimo Azure

Agent Log Analytics pro systém Windows a Linux musí být nainstalován na virtuálních počítačích, které jsou spuštěny ve vaší podnikové síti nebo jiném cloudovém prostředí, aby je bylo možné povolit s Update Management. Informace o požadavcích na systém a podporovaných metodách nasazení agenta do počítačů hostovaných mimo Azure najdete v tématu [Přehled agenta Log Analytics](../azure-monitor/platform/log-analytics-agent.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Zobrazit počítače připojené k vašemu účtu Automation

Po povolení Update Management pro vaše počítače můžete zobrazit informace o počítači výběrem možnosti **počítače**. Můžete si prohlédnout informace o názvu počítače, stavu dodržování předpisů, prostředí, typu operačního systému, nainstalovaných důležitých a aktualizacích zabezpečení, nainstalovaných dalších aktualizacích a aktualizacích připravenosti agenta pro vaše počítače.

  ![Zobrazení karty Počítače](./media/manage-update-multi/update-computers-tab.png)

Počítače, které byly nedávno povolené pro Update Management, možná ještě nebyly vyhodnoceny. Stav dodržování předpisů pro tyto počítače je `Not assessed` . Tady je seznam možných hodnot pro stav dodržování předpisů:

- `Compliant`: Počítače, ve kterých chybí důležité aktualizace nebo aktualizace zabezpečení.
- `Non-compliant`: Počítače, ve kterých chybí alespoň jedna kritická aktualizace nebo aktualizace zabezpečení.
- `Not assessed`: Data posouzení aktualizace se z počítače nepřijala v očekávaném časovém rámci. V případě počítačů se systémem Linux je očekávaným časovým obdobím poslední hodina. V případě počítačů se systémem Windows je očekávaným časovým obdobím posledních 12 hodin.

Chcete-li zobrazit stav agenta, vyberte odkaz ve sloupci **připravenosti agenta aktualizace** . Když vyberete tuto možnost, otevře se podokno Hybrid Worker a zobrazí se stav Hybrid Worker. Následující obrázek ukazuje příklad agenta, který nebyl po delší dobu připojen k Update Management:

![Zobrazení karty Počítače](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Zobrazení posouzení aktualizací

Po povolení Update Management se otevře podokno Update Management. Na kartě **Chybějící aktualizace** můžete zobrazit seznam chybějících aktualizací.

## <a name="collect-data"></a>Shromažďování dat

Agenti, kteří jsou nainstalováni na virtuálních počítačích a počítačích, shromažďují data o aktualizacích. Agenti odesílají data do Azure Update Management.

### <a name="supported-agents"></a>Podporovaní agenti

Následující tabulka popisuje připojené zdroje, které Update Management podporuje:

| Připojený zdroj | Podporováno | Popis |
| --- | --- | --- |
| Agenti systému Windows |Ano |Update Management shromažďuje informace o aktualizacích systému z agentů Windows a pak inicializuje instalaci požadovaných aktualizací. |
| Agenti systému Linux |Ano |Update Management shromažďuje informace o aktualizacích systému od agentů systému Linux a následně inicializuje instalaci požadovaných aktualizací v podporovaných distribucích. |
| Skupina pro správu Operations Manageru |Ano |Update Management shromažďuje informace o aktualizacích systému z agentů v připojené skupině pro správu. |
| Účet služby Azure Storage |Ne |Azure Storage neobsahují informace o aktualizacích systému. |

### <a name="collection-frequency"></a>Četnost shromažďování dat

Když počítač dokončí kontrolu shody aktualizací, agent přepošle informace hromadně do Azure Monitor protokolů. V počítači se systémem Windows se kontrola kompatibility ve výchozím nastavení spouští každých 12 hodin.

Kromě plánu vyhledávání se kontrola kompatibility aktualizací zahájila do 15 minut od restartování MMA, před instalací aktualizace a po instalaci aktualizace.

U počítače se systémem Linux se kontrola dodržování předpisů provádí ve výchozím nastavení každou hodinu. Pokud se agent MMA restartuje, zahájí se kontrola kompatibility do 15 minut.

Zobrazení aktuálních dat o spravovaných počítačích může trvat 30 minut až 6 hodin.

## <a name="schedule-an-update-deployment"></a>Naplánování nasazení aktualizace

Pokud chcete nainstalovat aktualizace, naplánujte nasazení, které se zarovnává s plánem vydávání verzí a oknem služby. Můžete zvolit typy aktualizací, které budou součástí nasazení. Můžete například zahrnout důležité aktualizace nebo aktualizace zabezpečení a vyloučit kumulativní aktualizace.

>[!NOTE]
>Při plánování nasazení aktualizace se vytvoří prostředek [plánu](shared-resources/schedules.md) propojený s runbookm **patch-MicrosoftOMSComputers** , který zpracovává nasazení aktualizace na cílových počítačích. Pokud odstraníte prostředek plánu z Azure Portal nebo pomocí PowerShellu po vytvoření nasazení dojde k přerušení naplánovaného nasazení aktualizace a při pokusu o překonfiguraci z portálu se zobrazí chyba. Zdroj plánu lze odstranit pouze odstraněním odpovídajícího plánu nasazení.
>

Chcete-li naplánovat nové nasazení aktualizací pro jeden nebo více virtuálních počítačů, vyberte v části **Správa aktualizací**možnost **naplánovat nasazení aktualizace**.

V podokně **nové nasazení aktualizací** zadejte následující informace:

- **Název**: Zadejte jedinečný název pro identifikaci nasazení aktualizace.
- **Operační systém**: vyberte **Windows** nebo **Linux**.
- **Skupiny, které se mají aktualizovat**: Definujte dotaz založený na kombinaci předplatného, skupin prostředků, umístění a značek, abyste vytvořili dynamickou skupinu virtuálních počítačů Azure, které chcete zahrnout do nasazení. V případě virtuálních počítačů mimo Azure se uložená hledání použijí k vytvoření dynamické skupiny pro zahrnutí do nasazení. Další informace najdete v tématu [dynamické skupiny](automation-update-management-groups.md).
- **Počítače k aktualizaci**: pro výběr počítačů, které chcete aktualizovat, vyberte uložené hledání, importovanou skupinu nebo vyberte počítače.

   >[!NOTE]
   >Vyberete-li možnost uloženého hledání, nebudou vraceny identity počítačů, pouze jejich názvy. Pokud máte v několika skupinách prostředků několik virtuálních počítačů se stejným názvem, vrátí se výsledky. Použití možnosti **skupiny k aktualizaci** se doporučuje k tomu, abyste zajistili zahrnutí jedinečných virtuálních počítačů, které odpovídají vašim kritériím.

   Pokud zvolíte možnost **počítače**, připravenost počítače se zobrazí ve sloupci **připravenosti agenta aktualizace** . Před naplánováním nasazení aktualizace se můžete podívat na stav počítače. Další informace o různých metodách vytváření skupin počítačů v protokolu Azure Monitor najdete v tématu [skupiny počítačů v protokolech Azure monitor](../azure-monitor/platform/computer-groups.md)

  ![Podokno nasazení nové aktualizace](./media/manage-update-multi/update-select-computers.png)

- **Klasifikace aktualizace**: Vyberte typy softwaru, které se mají zahrnout do nasazení aktualizace. Popis typů klasifikace najdete v tématu [klasifikace aktualizací](automation-view-update-assessments.md#work-with-update-classifications). Typy klasifikace jsou:
  - Důležité aktualizace
  - Aktualizace zabezpečení
  - Kumulativní aktualizace
  - Balíčky funkcí
  - Aktualizace Service Pack
  - Aktualizace definic
  - Nástroje
  - Aktualizace

- **Aktualizace, které se mají zahrnout nebo vyloučit** – Otevře stránku Zahrnout nebo vyloučit. Aktualizace, které se mají zahrnout nebo vyloučit jsou na samostatných kartách. Další informace o způsobu zpracování zařazení najdete v tématu [Naplánování nasazení aktualizací](automation-tutorial-update-management.md#schedule-an-update-deployment).

> [!NOTE]
> Je důležité, abyste věděli, že vyloučení přepisují. Pokud například definujete pravidlo vyloučení `*` , nebudou nainstalovány žádné opravy ani balíčky, protože všechny jsou vyloučené. Vyloučené opravy se pořád na počítači zobrazují jako chybějící. Pro počítače se systémem Linux, pokud je zahrnut balíček, ale obsahuje závislý balíček, který byl vyloučen, není balíček nainstalován.

> [!NOTE]
> Aktualizace, které byly nahrazeny pro zahrnutí do nasazení aktualizace, nelze zadat.

- **Nastavení plánu:** Můžete přijmout výchozí datum a čas, což je 30 minut od aktuálního času. Můžete také zadat jiný čas.

   Můžete také určit, jestli nasazení proběhne jednou nebo opakovaně. Pokud chcete nastavit plán opakování, vyberte v části **opakování**možnost **opakující**se.

   ![Dialogové okno Nastavení plánu](./media/manage-update-multi/update-set-schedule.png)

- **Předzálohovací a pozálohovací skripty**: Vyberte skripty, které se mají spustit před vaším nasazením a po něm. Další informace najdete v tématu týkajícím se [správy předzálohovacích a pozálohovacích skriptů](pre-post-scripts.md).
- Časový **interval pro správu a údržbu (minuty)**: zadejte časové období, po které má dojít k nasazení aktualizací. Toto nastavení pomůže zajistit, že se změny provedou v rámci definovaných časových intervalů pro správu a údržbu.

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

V podokně Aktualizace výsledků se zobrazuje celkový počet aktualizací a výsledky nasazení pro virtuální počítač. Tabulka na pravé straně poskytuje podrobný rozpis každé aktualizace a výsledky instalace. Výsledkem instalace může být jedna z následujících hodnot:

- `Not attempted`: Aktualizace nebyla nainstalována, protože na základě definovaného časového období údržby byla k dispozici dostatek času.
- `Succeeded`: Aktualizace byla úspěšná.
- `Failed`: Aktualizace se nezdařila.

Výběrem možnosti **Všechny protokoly** zobrazíte všechny položky protokolu, které toto nasazení vytvořilo.

Pokud chcete zobrazit datový proud úlohy Runbooku, který spravuje nasazení aktualizace na cílovém virtuálním počítači, vyberte dlaždici výstup.

Kliknutím na **Chyby** zobrazíte podrobné informace o případných chybách tohoto nasazení.

## <a name="next-steps"></a>Další kroky

* Pokud potřebujete vyhledat protokoly aktualizace, přečtěte si téma [dotazy Update Management protokoly](automation-update-management-query-logs.md).
