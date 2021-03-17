---
title: Postup vytvoření nasazení aktualizací pro Azure Automation Update Management
description: Tento článek popisuje, jak naplánovat nasazení aktualizací a zkontrolovat jejich stav.
services: automation
ms.subservice: update-management
ms.date: 12/09/2020
ms.topic: conceptual
ms.openlocfilehash: fb81d38b066e1abb54664225daa6840d895e9ace
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575898"
---
# <a name="how-to-deploy-updates-and-review-results"></a>Jak nasadit aktualizace a zkontrolovat výsledky

Tento článek popisuje, jak naplánovat nasazení aktualizací a zkontrolovat proces po dokončení nasazení. Můžete nakonfigurovat nasazení aktualizace z vybraného virtuálního počítače Azure, z vybraného serveru s povoleným obloukem nebo z účtu Automation ve všech nakonfigurovaných počítačích a serverech.

V každém scénáři je nasazení, které jste vytvořili, cíleno na vybraný počítač nebo server, nebo v případě vytvoření nasazení z účtu Automation, můžete cílit na jeden nebo více počítačů. Když naplánujete nasazení aktualizace z virtuálního počítače Azure nebo serveru s podporou ARC, postup se shoduje s nasazením z vašeho účtu Automation, a to s těmito výjimkami:

* Operační systém se automaticky vybere v závislosti na operačním systému počítače.
* Cílový počítač, který chcete aktualizovat, je nastaven na automatické zacílení.
* Při konfiguraci plánu můžete zadat **Update now**, nastane jednou nebo používá opakovaný plán.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

## <a name="schedule-an-update-deployment"></a>Naplánování nasazení aktualizace

Při plánování nasazení aktualizace se vytvoří prostředek [plánu](../shared-resources/schedules.md) propojený s runbookm **patch-MicrosoftOMSComputers** , který zpracovává nasazení aktualizace na cílovém počítači nebo počítačích. Pro instalaci aktualizací musíte naplánovat nasazení, které následuje po plánu vydání a okně služby. Můžete zvolit typy aktualizací, které chcete zahrnout do nasazení. Můžete například zahrnout důležité aktualizace nebo aktualizace zabezpečení a vyloučit kumulativní aktualizace.

>[!NOTE]
>Pokud odstraníte prostředek plánu z Azure Portal nebo pomocí PowerShellu po vytvoření nasazení dojde k přerušení naplánovaného nasazení aktualizace a při pokusu o překonfigurování prostředků plánu z portálu se zobrazí chyba. Zdroj plánu lze odstranit pouze odstraněním odpovídajícího plánu nasazení.  

K naplánování nového nasazení aktualizace proveďte následující kroky. V závislosti na vybraném prostředku (to znamená účet Automation, server s podporou ARC, virtuální počítač Azure) platí následující postup pro všechny s drobnými rozdíly při konfiguraci plánu nasazení.

1. Na portálu můžete naplánovat nasazení pro:

   * Jeden nebo víc počítačů, přejděte na **účty Automation** a v seznamu vyberte svůj účet Automation se zapnutým Update Management.
   * Pro virtuální počítač Azure přejděte na **virtuální počítače** a ze seznamu vyberte svůj virtuální počítač.
   * Pro server s podporou ARC přejděte na **servery – Azure ARC** a ze seznamu vyberte svůj server.

2. V závislosti na prostředku, který jste vybrali, přejděte na Update Management:

   * Pokud jste vybrali svůj účet Automation, v části **Správa aktualizací** klikněte na **Správa aktualizací** a pak vyberte **naplánovat nasazení aktualizace**.
   * Pokud jste vybrali virtuální počítač Azure, klikněte na **Host + aktualizace hostitelů** a pak vyberte **Přejít na Update Management**.
   * Pokud jste vybrali Server s podporou ARC, klikněte na **Update Management** a pak vyberte **naplánovat nasazení aktualizací**.

3. V části **nové nasazení aktualizací** do pole **název** zadejte jedinečný název pro vaše nasazení.

4. Vyberte operační systém, který má být cílem nasazení aktualizace.

    > [!NOTE]
    > Tato možnost není dostupná, pokud jste vybrali virtuální počítač Azure nebo server s podporou ARC. Operační systém se automaticky identifikuje.

5. V oblasti **skupiny pro aktualizaci** definujte dotaz, který kombinuje předplatné, skupiny prostředků, umístění a značky, abyste vytvořili dynamickou skupinu virtuálních počítačů Azure, které chcete zahrnout do nasazení. Další informace najdete v tématu [použití dynamických skupin s Update Management](configure-groups.md).

    > [!NOTE]
    > Tato možnost není dostupná, pokud jste vybrali virtuální počítač Azure nebo server s podporou ARC. Počítač je automaticky zaměřen na plánované nasazení.

6. V rozevírací nabídce **počítače k aktualizaci** vyberte uložené výsledky hledání, importovanou skupinu nebo vyberte **počítače** a vyberte jednotlivé počítače. Pomocí této možnosti můžete zobrazit připravenost agenta Log Analytics pro každý počítač. Další informace o různých metodách vytváření skupin počítačů v protokolu Azure Monitor najdete v tématu [skupiny počítačů v protokolech Azure monitor](../../azure-monitor/logs/computer-groups.md). V plánovaném nasazení aktualizace můžete zahrnout maximálně 1000 počítačů.

    > [!NOTE]
    > Tato možnost není dostupná, pokud jste vybrali virtuální počítač Azure nebo server s podporou ARC. Počítač je automaticky zaměřen na plánované nasazení.

7. Pomocí oblasti **klasifikace aktualizací** můžete určit [klasifikace aktualizací](view-update-assessments.md#work-with-update-classifications) pro produkty. U každého produktu zrušte výběr všech podporovaných klasifikací aktualizací, ale těch, které se mají zahrnout do nasazení aktualizace.

    Pokud je vaše nasazení určeno pro použití pouze vybrané sady aktualizací, je nutné zrušit výběr všech předem vybraných klasifikací aktualizací při konfiguraci možnosti **Zahrnout/vyloučit aktualizace** , jak je popsáno v následujícím kroku. Tím se zajistí, že se na cílových počítačích nainstalují jenom aktualizace, které jste zadali pro *zahrnutí* do tohoto nasazení.

8. Pomocí oblasti **zahrnout nebo vyloučit aktualizace** můžete přidat nebo vyloučit vybrané aktualizace z nasazení. Na stránce **zahrnutí/vyloučení** zadejte čísla ID článků znalostní báze, která se mají zahrnout nebo vyloučit pro aktualizace Windows. Pro podporovanou distribuce systému Linux zadáte název balíčku.

   > [!IMPORTANT]
   > Zapamatujte si, že vyloučení přepisují. Například pokud definujete pravidlo vyloučení `*` , Update Management vyloučí všechny opravy nebo balíčky z instalace. Vyloučené opravy se pořád na počítačích zobrazují jako chybějící. U počítačů se systémem Linux, pokud zahrnete balíček, který má vyloučený závislý balíček, Update Management nenainstaluje hlavní balíček.

   > [!NOTE]
   > Aktualizace, které se nahradily, se nedají zadat, aby se zahrnuly do nasazení aktualizace.

9. Vyberte **Nastavení plánu**. Výchozí čas spuštění je 30 minut po aktuálním čase. Čas spuštění můžete nastavit na jakýkoli čas minimálně 10 minut po aktuálním čase.

    > [!NOTE]
    > Tato možnost je odlišná, pokud jste vybrali Server s podporou ARC. Můžete vybrat **aktualizovat hned** nebo čas zahájení 20 minut do budoucna.

10. Pomocí **opakování** určete, jestli k nasazení dojde jednou, nebo použijte opakovaný plán, a pak vyberte **OK**.

11. V oblasti **pre-Scripts + post-Scripts** vyberte skripty, které chcete spustit před a po nasazení. Další informace najdete v tématu [Správa předběžných skriptů a pozálohovacích skriptů](pre-post-scripts.md).

12. V poli časový **interval pro správu a údržbu** můžete zadat dobu, po kterou je možné nainstalovat aktualizace. Při určování časového období údržby Vezměte v úvahu následující podrobnosti:

    * Časové intervaly pro správu a údržbu určují, kolik aktualizací se instaluje.
    * Update Management neukončí instalaci nových aktualizací, pokud se blíží konec okna údržby.
    * Update Management neukončí probíhající aktualizace, pokud dojde k překročení časového období údržby. Žádné zbývající aktualizace, které se mají nainstalovat, se nepokusí. Pokud k tomu dojde konzistentně, měli byste znovu vyhodnotit dobu trvání časového intervalu pro správu a údržbu.
    * Pokud dojde k překročení časového intervalu pro správu a údržbu v systému Windows, je často k instalaci aktualizace Service Pack trvat delší dobu.

    > [!NOTE]
    > Aby se aktualizace nepoužívaly mimo časové období údržby v Ubuntu, překonfigurujte `Unattended-Upgrade` balíček pro zákaz automatických aktualizací. Informace o tom, jak nakonfigurovat balíček, najdete v tématu [věnovaném automatickým aktualizacím v příručce k serveru Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

13. V poli **Možnosti restartování** můžete určit způsob, jakým se má při nasazení zpracovávat restarty. Dostupné jsou tyto možnosti: 
    * V případě potřeby restartování (výchozí)
    * Vždy restartovat
    * Nikdy nerestartovat
    * Pouze restart; Tato možnost neinstaluje aktualizace.

    > [!NOTE]
    > Klíče registru uvedené v části [klíče registru používané ke správě restartování](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) můžou způsobit restart události, pokud jsou **Možnosti restartování** nastavené na **Nikdy nerestartovat**.

14. Až skončíte s konfigurací plánu nasazení, vyberte **vytvořit**.

    ![Podokno nastavení plánu aktualizací](./media/deploy-updates/manageupdates-schedule-win.png)

    > [!NOTE]
    > Až dokončíte konfiguraci plánu nasazení pro vybraný server s povoleným obloukem, vyberte **zkontrolovat + vytvořit**.

15. Vrátíte se na řídicí panel stavu. Vyberte **plány nasazení** a zobrazte plán nasazení, který jste vytvořili. Uvádí se maximálně 500 plánů. Pokud máte více než 500 plánů a chcete zobrazit úplný seznam, přečtěte si téma [Konfigurace aktualizace softwaru – seznam](/rest/api/automation/softwareupdateconfigurations/list) REST API metoda. Zadejte rozhraní API verze 2019-06-01 nebo vyšší.

## <a name="schedule-an-update-deployment-programmatically"></a>Naplánování nasazení aktualizace prostřednictvím kódu programu

Informace o tom, jak vytvořit nasazení aktualizace pomocí REST API, najdete v tématu [Konfigurace aktualizací softwaru-vytvořit](/rest/api/automation/softwareupdateconfigurations/create).

Pomocí ukázkové sady Runbook můžete vytvořit týdenní nasazení aktualizace. Další informace o této sadě Runbook najdete v tématu [Vytvoření týdenního nasazení aktualizací pro jeden nebo více virtuálních počítačů ve skupině prostředků](https://github.com/azureautomation/create-a-weekly-update-deployment-for-one-or-more-vms-in-a-resource-group).

## <a name="check-deployment-status"></a>Zkontroluje stav nasazení.

Po spuštění naplánovaného nasazení uvidíte jeho stav na kartě **Historie** v části **Správa aktualizací**. Pokud je nasazení aktuálně spuštěné, jeho stav je **Probíhá**. Po úspěšném dokončení nasazení se stav změní na **úspěch**. Pokud dojde k chybám s jednou nebo více aktualizacemi v nasazení, stav se **nezdařil**.

## <a name="view-results-of-a-completed-update-deployment"></a>Zobrazit výsledky dokončeného nasazení aktualizace

Až se nasazení dokončí, můžete ho vybrat a zobrazit jeho výsledky.

[![Řídicí panel stavu nasazení aktualizace pro konkrétní nasazení](./media/deploy-updates/manageupdates-view-results.png)](./media/deploy-updates/manageupdates-view-results-expanded.png#lightbox)

V části **výsledky aktualizace** najdete souhrn celkového počtu aktualizací a výsledků nasazení na cílových virtuálních počítačích. V tabulce na pravé straně se zobrazuje podrobný rozpis aktualizací a výsledků instalace pro každou z nich.

Dostupné hodnoty jsou:

* **Nebyl proveden pokus** – aktualizace se nenainstalovala, protože na základě definovaného trvání časového období údržby nebylo k dispozici dostatek času.
* **Nevybráno** – aktualizace není vybraná pro nasazení.
* **Úspěch** – aktualizace byla úspěšná.
* **Chyba** – aktualizace se nezdařila.

Výběrem **všechny protokoly** zobrazíte všechny položky protokolu, které nasazení vytvořilo.

Vyberte **výstup** , abyste viděli datový proud úlohy Runbooku zodpovědného za správu nasazení aktualizace na cílových virtuálních počítačích.

Výběrem možnosti **Chyby** zobrazíte podrobné informace o případných chybách tohoto nasazení.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak vytvořit výstrahy, které vás upozorní na výsledky nasazení aktualizace, najdete v tématu [vytvoření výstrah pro Update Management](configure-alerts.md).
* Pokud chcete řešit obecné chyby Update Management, přečtěte si téma [řešení potíží s Update Management](../troubleshoot/update-management.md).
