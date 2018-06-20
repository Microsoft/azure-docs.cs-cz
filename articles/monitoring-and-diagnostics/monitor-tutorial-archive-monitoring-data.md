---
title: Archivace metrik Azure a dat protokolů s použitím Azure Storage
description: Archivujte data protokolů a metrik vygenerovaná v rámci Azure do účtu úložiště.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: johnkem
ms.custom: mvc
ms.component: metrics
ms.openlocfilehash: 4d08c4c7a76d7ed16ec57590ee0fd4ee978e5258
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263146"
---
# <a name="archive-azure-monitoring-data"></a>Archivace dat monitorování Azure

Různé vrstvy vašeho prostředí Azure generují data protokolů a metrik, která je možné archivovat do účtu Azure Storage. Můžete to provádět za účelem zachování historie dat monitorování v průběhu času v nenákladném a neprohledávatelném úložišti po uplynutí doby uchovávání těchto dat v Log Analytics nebo ve službě Azure Monitor. Tento kurz prochází proces konfigurace prostředí Azure pro archivaci dat do účtu úložiště.

> [!div class="checklist"]
> * Vytvoření účtu úložiště pro uchovávání dat monitorování
> * Směrování protokolů předplatného do tohoto účtu úložiště
> * Směrování dat prostředků do tohoto účtu úložiště
> * Směrování dat virtuálního počítače (hostovaného operačního systému) do tohoto účtu úložiště
> * Zobrazení dat monitorování v tomto účtu úložiště
> * Vyčištění prostředků

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Nejprve je potřeba nastavit účet úložiště, do kterého se data monitorování budou archivovat. Provedete to [podle těchto kroků](../storage/common/storage-create-storage-account.md).

## <a name="route-subscription-logs-to-the-storage-account"></a>Směrování protokolů předplatného do účtu úložiště

Teď jste připraveni začít nastavovat své prostředí Azure pro směrování dat monitorování do účtu úložiště. Nejprve nakonfigurujeme směrování dat na úrovni předplatného (obsažená v protokolu aktivit Azure) do účtu úložiště. [**Protokol aktivit Azure**](monitoring-overview-activity-logs.md) poskytuje historii událostí na úrovni předplatného v Azure. Najdete ho na webu Azure Portal a jeho procházením můžete určit, *kdo* vytvořil, aktualizoval nebo odstranil *jaké* prostředky a *kdy* k tomu došlo.

1. V levém navigačním seznamu klikněte na tlačítko **Monitorování** a pak na **Protokol aktivit**.

   ![Část Protokol aktivit](media/monitor-tutorial-archive-monitoring-data/activity-log-home.png)

2. V části Protokol aktivit, která se zobrazí, klikněte na tlačítko **Exportovat**.

3. V části **Export protokolu aktivit**, která se zobrazí, zaškrtněte políčko u možnosti **Exportovat do účtu úložiště** a klikněte na **Vybrat účet úložiště**.

   ![Export protokolu aktivit](media/monitor-tutorial-archive-monitoring-data/activity-log-export.png)

4. V části, která se zobrazí, pomocí rozevíracího seznamu **Účet úložiště** vyberte název účtu úložiště, který jste vytvořili v předchozím kroku **Vytvoření účtu úložiště**, a klikněte na **OK**.

   ![Výběr účtu úložiště](media/monitor-tutorial-archive-monitoring-data/activity-log-storage.png)

5. Nastavte posuvník **Doba uchování (dny)** na hodnotu 30. Tento posuvník nastavuje počet dnů, po který se mají data monitorování uchovávat v účtu úložiště. Azure Monitor automaticky odstraní data starší než zadaný počet dnů. Pokud jako dobu uchování nastavíte 0 dnů, data se uloží navždy.

6. Klikněte na **Uložit** a zavřete tuto části.

Data monitorování z vašeho předplatného se teď přenášejí do účtu úložiště.

## <a name="route-resource-data-to-the-storage-account"></a>Směrování dat prostředků do účtu úložiště

Teď nakonfigurujeme směrování dat na úrovni prostředku (metriky prostředků a diagnostické protokoly) do účtu úložiště tak, že nastavíme **nastavení diagnostiky prostředků**.

1. V levém navigačním seznamu klikněte na tlačítko **Monitorování** a pak na **Nastavení diagnostiky**. Tady se zobrazí seznam všech prostředků ve vašem předplatném, které generují data monitorování prostřednictvím služby Azure Monitor. Pokud se na tomto seznamu nezobrazí žádné prostředky, můžete [vytvořit aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) předtím, než budete pokračovat, abyste měli prostředek, u kterého můžete nakonfigurovat nastavení diagnostiky.

2. Klikněte na prostředek v seznamu a pak klikněte na **Zapnout diagnostiku**.

   ![Zapnout diagnostiku](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-turn-on.png)

   Pokud už existuje nakonfigurované nastavení, zobrazí se místo toho stávající nastavení a tlačítko **Přidat nastavení diagnostiky**. Klikněte na toto tlačítko.

   Nastavení diagnostiky prostředků je definice toho, *jaká* data monitorování se mají směrovat z konkrétního prostředku a *kam* se mají směrovat.

3. V části, která se zobrazí, zadejte **název** nastavení a zaškrtněte políčko u možnosti **Archivovat do účtu úložiště**.

   ![Část Nastavení diagnostiky](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-home.png)

4. Klikněte na tlačítko **Konfigurovat** pod možností **Archivovat do účtu úložiště** a vyberte účet úložiště, který jste vytvořili v předchozí části. Klikněte na **OK**.

   ![Nastavení diagnostiky – účet úložiště](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-storage.png)

5. Zaškrtněte všechna políčka v částech **Protokol** a **Metrika**. V závislosti na typu prostředku může být k dispozici pouze jedna z těchto možností. Tato zaškrtávací políčka řídí, jaké kategorie dat protokolů a metrik dostupné pro daný typ prostředku se odesílají do vybraného cíle, v tomto případě do účtu úložiště.

   ![Kategorie nastavení diagnostiky](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-categories.png)

6. Nastavte posuvník **Doba uchování (dny)** na hodnotu 30. Tento posuvník nastavuje počet dnů, po který se mají data monitorování uchovávat v účtu úložiště. Azure Monitor automaticky odstraní data starší než zadaný počet dnů. Pokud jako dobu uchování nastavíte 0 dnů, data se uloží navždy.

7. Klikněte na **Uložit**.

Data monitorování z vašeho prostředku se teď přenášejí do účtu úložiště.

> [!NOTE]
> Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
>
> *Příklad:* Metriku Příchozí zprávy v centru událostí je možné zkoumat a převést na graf na úrovni jednotlivých front. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.
>
>

## <a name="route-virtual-machine-guest-os-data-to-the-storage-account"></a>Směrování dat virtuálního počítače (hostovaného operačního systému) do účtu úložiště

1. Pokud ho ve svém předplatném ještě nemáte, [vytvořte virtuální počítač](../virtual-machines/windows/quick-create-portal.md).

2. V levém navigačním seznamu na portálu klikněte na **Virtuální počítače**.

3. V zobrazeném seznamu virtuálních počítačů klikněte na virtuální počítač, který jste vytvořili.

4. V části, která se zobrazí, klikněte v levém navigačním panelu na **Nastavení diagnostiky**. Tato část umožňuje nastavit integrované rozšíření monitorování ze služby Azure Monitor na vlastním virtuálním počítači a směrovat data generovaná systémem Windows nebo Linuxem do účtu úložiště.

   ![Přechod do nastavení diagnostiky](media/monitor-tutorial-archive-monitoring-data/guest-navigation.png)

5. V části, která se zobrazí, klikněte na **Povolit monitorování na úrovni hosta**.

   ![Povolení nastavení diagnostiky](media/monitor-tutorial-archive-monitoring-data/guest-enable.png)

6. Po správném uložení nastavení diagnostiky se na kartě **Přehled** zobrazí seznam shromažďovaných dat a umístění, kam se ukládají. Klikněte na část **Čítače výkonu** a zkontrolujte sadu shromažďovaných čítačů výkonu Windows.

   ![Nastavení čítačů výkonu](media/monitor-tutorial-archive-monitoring-data/guest-perf-counters.png)

7. Klikněte na kartu **Protokoly** a zaškrtněte políčka u úrovně **Informace** protokolů aplikací a systémových protokolů.

   ![Nastavení protokolů](media/monitor-tutorial-archive-monitoring-data/guest-logs.png)

8. Klikněte na kartu **Agent** a v části **Účet úložiště** klikněte na název zobrazeného účtu úložiště.

   ![Aktualizace účtu úložiště](media/monitor-tutorial-archive-monitoring-data/guest-storage-home.png)

9. V části, která se zobrazí, vyberte účet úložiště, který jste vytvořili v předchozím kroku **Vytvoření účtu úložiště**.

10. Klikněte na **Uložit**.

Data monitorování z vašich virtuálních počítačů se teď přenášejí do účtu úložiště.

## <a name="view-the-monitoring-data-in-the-storage-account"></a>Zobrazení dat monitorování v účtu úložiště

Pokud jste postupovali podle předchozích kroků, data se začala přenášet do vašeho účtu úložiště.

1. Pro některé typy dat, například pro protokol aktivit, musí existovat aktivita, která v účtu úložiště vygeneruje událost. Pokud chcete generovat aktivitu v protokolu aktivit, postupujte podle [těchto pokynů](./monitor-quick-audit-notify-action-in-subscription.md). Na zobrazení události v účtu úložiště možná budete muset počkat až pět minut.

2. Na portálu přejděte do části **Účty úložiště**, kterou najdete v levém navigačním panelu.

3. Identifikujte účet úložiště, který jste vytvořili v předchozí části, a klikněte na něj.

4. Klikněte na **Objekty blob**, pak na kontejner označený jako **insights-operational-logs** a nakonec na kontejner označený jako **name=default**. Toto je kontejner, který obsahuje váš protokol aktivit. Data monitorování se rozdělují do kontejnerů podle ID prostředku (v případě protokolu aktivit je to pouze ID předplatného) a pak podle data a času. Úplný formát těchto objektů blob je následující:

   insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{ID_předplatného}/y={čtyřmístné_číslo_roku}/m={dvoumístné_číslo_měsíce}/d={dvoumístné_číslo_dne}/h={dvoumístné_číslo_hodiny_ve_24hodinovém_formátu}/m=00/PT1H.json

5. Kliknutím na kontejnery pro ID prostředku, datum a čas přejděte k souboru PT1H.json. Klikněte na soubor PT1H.json a pak klikněte na **Stáhnout**. Každý objekt blob PT1H.json obsahuje objekt blob ve formátu JSON událostí, ke kterým došlo během hodiny zadané v adrese URL objektu blob (například h=12). Během aktuální hodiny se události připojují do souboru PT1H.json, když k nim dojde. Hodina minut (m=00) je vždy 00, protože události protokolu se rozdělují do jednotlivých objektů blob po hodinách.

   Teď můžete zobrazit událost JSON, která se uložila do účtu úložiště. V případě protokolů diagnostiky prostředků je formát objektů blob následující:

   insights-logs-{log category name}/resourceId=/{resource ID}/y={čtyřmístné_číslo_roku}/m={dvoumístné_číslo_měsíce}/d={dvoumístné_číslo_dne}/h={dvoumístné_číslo_hodiny_ve_24hodinovém_formátu}/m=00/PT1H.json

6. Data monitorování hostovaného operačního systému se ukládají do tabulek. Vraťte se na domovskou stránku účtu úložiště a klikněte na **Tabulky**. Zobrazí se tabulky pro metriky, čítače výkonu a protokoly událostí.

Právě jste úspěšně nastavili archivaci dat monitorování do účtu úložiště.

## <a name="clean-up-resources"></a>Vyčištění prostředků

1. Vraťte se do části **Export protokolu aktivit** z předchozího kroku **Směrování protokolů předplatného do účtu úložiště** a klikněte na **Resetovat**.

2. Přejděte do části **Nastavení diagnostiky**, klikněte na prostředek, pro který jste vytvořili nastavení diagnostiky v předchozím kroku **Směrování dat prostředků do účtu úložiště**, vyhledejte vytvořené nastavení, klikněte na tlačítko **Upravit nastavení** a pak klikněte na **Odstranit**.

3. Na virtuálním počítači, který jste nakonfigurovali v předchozím kroku **Směrování dat virtuálního počítače (hostovaného operačního systému) do účtu úložiště**, přejděte do části **Nastavení diagnostiky** a na kartě **Agent** klikněte na **Odebrat** (pod částí **Odebrat agenta diagnostiky Azure**).

4. Přejděte do účtu úložiště, který jste vytvořili v předchozím kroku **Vytvoření účtu úložiště**, a klikněte na **Odstranit účet úložiště**. Zadejte název účtu úložiště a klikněte na **Odstranit**.

5. Pokud jste v předchozích krocích vytvořili virtuální počítač nebo aplikaci logiky, odstraňte je také.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak nastavit archivaci dat monitorování z vašeho prostředí Azure (předplatné, prostředek a hostovaný operační systém) do účtu úložiště.


> [!div class="checklist"]
> * Vytvoření účtu úložiště pro uchovávání dat monitorování
> * Směrování protokolů předplatného do tohoto účtu úložiště
> * Směrování dat prostředků do tohoto účtu úložiště
> * Směrování dat virtuálního počítače (hostovaného operačního systému) do tohoto účtu úložiště
> * Zobrazení dat monitorování v tomto účtu úložiště
> * Vyčištění prostředků

Pokud chcete svá data využívat ještě lépe a získávat z nich další přehledy, odesílejte je také do Log Analytics.

> [!div class="nextstepaction"]
> [Začínáme se službou Log Analytics](../log-analytics/log-analytics-get-started.md)
