---
title: Postup vytvoření nasazení aktualizací pro Azure Automation Update Management
description: Tento článek popisuje, jak naplánovat nasazení aktualizací a zkontrolovat jejich stav.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 2a81376b284e0d1df84a69b969335c0e63999a00
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450259"
---
# <a name="how-to-deploy-updates-and-review-results"></a>Jak nasadit aktualizace a zkontrolovat výsledky

Tento článek popisuje, jak naplánovat nasazení aktualizací a zkontrolovat proces po dokončení nasazení.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="schedule-an-update-deployment"></a>Naplánování nasazení aktualizace

Při plánování nasazení aktualizace se vytvoří prostředek [plánu](../shared-resources/schedules.md) propojený s runbookm **patch-MicrosoftOMSComputers** , který zpracovává nasazení aktualizace na cílových počítačích. Pro instalaci aktualizací musíte naplánovat nasazení, které následuje po plánu vydání a okně služby. Můžete zvolit typy aktualizací, které chcete zahrnout do nasazení. Můžete například zahrnout důležité aktualizace nebo aktualizace zabezpečení a vyloučit kumulativní aktualizace.

>[!NOTE]
>Pokud odstraníte prostředek plánu z Azure Portal nebo pomocí PowerShellu po vytvoření nasazení dojde k přerušení naplánovaného nasazení aktualizace a při pokusu o překonfigurování prostředků plánu z portálu se zobrazí chyba. Zdroj plánu lze odstranit pouze odstraněním odpovídajícího plánu nasazení.  

Naplánování nového nasazení aktualizace:

1. V účtu Automation v části **Správa aktualizací**klikněte na **Správa aktualizací** a pak vyberte **naplánovat nasazení aktualizací**.

2. V části **nové nasazení aktualizací**do pole **název** zadejte jedinečný název pro vaše nasazení.

3. Vyberte operační systém, který má být cílem nasazení aktualizace.

4. V oblasti **skupiny pro aktualizaci (Preview)** definujte dotaz, který kombinuje předplatné, skupiny prostředků, umístění a značky, abyste vytvořili dynamickou skupinu virtuálních počítačů Azure, které chcete zahrnout do nasazení. Další informace najdete v tématu [použití dynamických skupin s Update Management](update-mgmt-groups.md).

5. V rozevírací nabídce **počítače k aktualizaci** vyberte uložené výsledky hledání, importovanou skupinu nebo vyberte **počítače** a vyberte jednotlivé počítače. Pomocí této možnosti můžete zobrazit připravenost agenta Log Analytics pro každý počítač. Další informace o různých metodách vytváření skupin počítačů v protokolu Azure Monitor najdete v tématu [skupiny počítačů v protokolech Azure monitor](../../azure-monitor/platform/computer-groups.md).

6. Pomocí oblasti **klasifikace aktualizací** můžete určit [klasifikace aktualizací](update-mgmt-view-update-assessments.md#work-with-update-classifications) pro produkty. U každého produktu zrušte výběr všech podporovaných klasifikací aktualizací, ale těch, které se mají zahrnout do nasazení aktualizace.

7. Pomocí oblasti **zahrnutí/vyloučení aktualizací** můžete vybrat konkrétní aktualizace pro nasazení. Stránka zahrnutí/vyloučení zobrazuje aktualizace podle čísel ID článků znalostní báze, které mají být zahrnuty nebo vyloučeny.
    
   > [!IMPORTANT]
   > Zapamatujte si, že vyloučení přepisují. Například pokud definujete pravidlo vyloučení `*` , Update Management vyloučí všechny opravy nebo balíčky z instalace. Vyloučené opravy se pořád na počítačích zobrazují jako chybějící. U počítačů se systémem Linux, pokud zahrnete balíček, který má vyloučený závislý balíček, Update Management nenainstaluje hlavní balíček.

   > [!NOTE]
   > Aktualizace, které se nahradily, se nedají zadat, aby se zahrnuly do nasazení aktualizace.

8. Vyberte **Nastavení plánu**. Výchozí čas spuštění je 30 minut po aktuálním čase. Čas spuštění můžete nastavit na jakýkoli čas minimálně 10 minut po aktuálním čase.

9. Pomocí pole **opakování** určete, jestli k nasazení dojde jednou, nebo použijte plán opakování a pak vyberte **OK**.

10. V oblasti **pre-Scripts + post-Scripts (Preview)** vyberte skripty, které chcete spustit před a po nasazení. Další informace najdete v tématu [Správa předběžných skriptů a pozálohovacích skriptů](update-mgmt-pre-post-scripts.md).
    
11. V poli časový **interval pro správu a údržbu** můžete zadat dobu, po kterou je možné nainstalovat aktualizace. Při určování časového období údržby Vezměte v úvahu následující podrobnosti:

    * Časové intervaly pro správu a údržbu určují, kolik aktualizací se instaluje.
    * Update Management neukončí instalaci nových aktualizací, pokud se blíží konec okna údržby.
    * Update Management neukončí probíhající aktualizace, pokud dojde k překročení časového období údržby.
    * Pokud dojde k překročení časového intervalu pro správu a údržbu v systému Windows, je často k instalaci aktualizace Service Pack trvat delší dobu.

    > [!NOTE]
    > Aby se aktualizace nepoužívaly mimo časové období údržby v Ubuntu, překonfigurujte `Unattended-Upgrade` balíček pro zákaz automatických aktualizací. Informace o tom, jak nakonfigurovat balíček, najdete v tématu [věnovaném automatickým aktualizacím v příručce k serveru Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

12. V poli **Možnosti restartování** můžete určit způsob, jakým se má při nasazení zpracovávat restarty. Dostupné jsou tyto možnosti: 
    * V případě potřeby restartování (výchozí)
    * Vždy restartovat
    * Nikdy nerestartovat
    * Pouze restart; Tato možnost neinstaluje aktualizace.

    > [!NOTE]
    > Klíče registru uvedené v části [klíče registru používané ke správě restartování](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) můžou způsobit restart události, pokud jsou **Možnosti restartování** nastavené na **Nikdy nerestartovat**.

13. Až skončíte s konfigurací plánu nasazení, vyberte **vytvořit**.

    ![Podokno nastavení plánu aktualizací](./media/update-mgmt-deploy-updates/manageupdates-schedule-win.png)

14. Vrátíte se na řídicí panel stavu. Vyberte **plánovaná nasazení aktualizací** , abyste zobrazili plán nasazení, který jste vytvořili.

## <a name="schedule-an-update-deployment-programmatically"></a>Naplánování nasazení aktualizace prostřednictvím kódu programu

Informace o tom, jak vytvořit nasazení aktualizace pomocí REST API, najdete v tématu [Konfigurace aktualizací softwaru-vytvořit](/rest/api/automation/softwareupdateconfigurations/create).

Pomocí ukázkové sady Runbook můžete vytvořit týdenní nasazení aktualizace. Další informace o této sadě Runbook najdete v tématu [Vytvoření týdenního nasazení aktualizací pro jeden nebo více virtuálních počítačů ve skupině prostředků](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="check-deployment-status"></a>Zkontroluje stav nasazení.

Po spuštění naplánovaného nasazení uvidíte jeho stav na kartě **nasazení aktualizací** v části **Správa aktualizací**. Pokud je nasazení aktuálně spuštěné, jeho stav je **Probíhá**. Po úspěšném dokončení nasazení se stav změní na **úspěch**. Pokud dojde k chybám s jednou nebo více aktualizacemi v nasazení, stav je **částečně neúspěšný**.

## <a name="view-results-of-a-completed-update-deployment"></a>Zobrazit výsledky dokončeného nasazení aktualizace

Až se nasazení dokončí, můžete ho vybrat a zobrazit jeho výsledky.

[![Řídicí panel stavu nasazení aktualizace pro konkrétní nasazení](./media/update-mgmt-deploy-updates/manageupdates-view-results.png)](./media/update-mgmt-deploy-updates/manageupdates-view-results-expanded.png#lightbox)

V části **výsledky aktualizace**najdete souhrn celkového počtu aktualizací a výsledků nasazení na cílových virtuálních počítačích. V tabulce na pravé straně se zobrazuje podrobný rozpis aktualizací a výsledků instalace pro každou z nich.

Dostupné hodnoty jsou:

* **Nebyl proveden pokus** – aktualizace se nenainstalovala, protože na základě definovaného trvání časového období údržby nebylo k dispozici dostatek času.
* **Nevybráno** – aktualizace není vybraná pro nasazení.
* **Úspěch** – aktualizace byla úspěšná.
* **Chyba** – aktualizace se nezdařila.

Výběrem **všechny protokoly** zobrazíte všechny položky protokolu, které nasazení vytvořilo.

Vyberte **výstup** , abyste viděli datový proud úlohy Runbooku zodpovědného za správu nasazení aktualizace na cílových virtuálních počítačích.

Výběrem možnosti **Chyby** zobrazíte podrobné informace o případných chybách tohoto nasazení.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak vytvořit výstrahy, které vás upozorní na výsledky nasazení aktualizace, najdete v tématu [vytvoření výstrah pro Update Management](update-mgmt-configure-alerts.md).