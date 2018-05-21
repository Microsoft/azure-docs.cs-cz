---
title: Monitorování sad Azure Automation runbook s výstrahy a protokolování aktivity
description: Tento článek vás provede procesem monitorování sady runbook automatizace Azure pomocí protokolu aktivit
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/17/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: a0bd291af98477308cda898580fd52e33b1e6bbd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="monitoring-runbooks-with-azure-activity-log-alerts"></a>Monitorování sady runbook s výstrahami protokolu aktivita služby Azure

V tomto článku zjistěte, jak vytvořit výstrahy na základě stavu dokončení sad runbook.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure v https://portal.azure.com

## <a name="create-alert"></a>Vytvořit upozornění

Výstrahy umožňují definovat podmínku pro monitorování a akce se má provést při splnění této podmínky.

Na portálu Azure přejděte do **všechny služby** a vyberte **monitorování**. Na stránce monitorování vyberte **výstrahy** a klikněte na tlačítko **+ nové pravidlo výstrahy**.

### <a name="define-the-alert-condition"></a>Definování podmínka upozornění

1. V části **1. Definovat výstrahy podmínku**, klikněte na tlačítko **+ vyberte cíl**. Vyberte předplatné a v části **filtrovat podle typu prostředku**, vyberte **účty Automation**. Vyberte svůj účet Automation a klikněte na **provádí**.

   ![Vyberte prostředek pro výstrahy](./media/automation-alert-activity-log/select-resource.png)

### <a name="configure-alert-criteria"></a>Nakonfigurujte kritéria výstrahy

1. Klikněte na tlačítko **+ přidat kritéria**. Vyberte **metriky** pro **signál typ**a zvolte **celkový počet úloh** z tabulky.

1. **Konfigurovat signál logiku** stránka je, kde můžete definovat logiku, která aktivuje výstrahu. V části Historický graf, zobrazí se dvěma rozměry **název sady Runbook** a **stav**. Dimenze jsou různé vlastnosti pro metriku, které mohou být použity k filtrování výsledků. Pro **název sady Runbook**, vyberte sadu runbook, které chcete výstrahu na nebo ponechte pole prázdné výstrahy pro všechny sady runbook. Pro **stav**, vyberte z rozevírací nabídky, kterou chcete sledovat pro stav. Runbook název a stav hodnoty, které se zobrazují v rozevírací nabídce jsou pouze pro úlohy, které jste spustili minulého týdne.

   Pokud chcete upozornění na stav nebo sadu runbook, který není uveden v rozevírací nabídce, klikněte **\+** vedle dimenze. Otevře se dialog, který umožňuje zadat vlastní hodnotu, která nebyla pro daná dimenze vygenerované nedávno. Pokud zadáte hodnotu, která neexistuje pro vlastnost nebude aktivuje výstrahu.

1. V části **výstrahy logiku**, zadejte podmínku a prahová hodnota pro upozornění. Náhled vaší podmínky definované se zobrazí pod.

1. V části **Evaluated na základě** vyberte časový interval pro dotaz a jak často se má tento dotaz se spustil. Například pokud se rozhodnete **za posledních 5 minut** pro **období** a **každých 1 minutu** pro **frekvence**, výstraha vyhledává číslo sad runbook, které splnění kritérií za posledních 5 minut. Tento dotaz není spustili každou minutu a až kritéria výstrahy, které jste definovali již najít v okně 5 minut, vyřeší výstrahy sám sebe. Po dokončení klikněte na tlačítko **provádí**.

   ![Vyberte prostředek pro výstrahy](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Zadejte podrobnosti výstrahy

1. V části **2. Zadejte podrobnosti výstrahy**, poskytnout výstrahy popisný název a popis. Nastavte **závažnost** tak, aby odpovídaly výstražný stav. Nejsou k dispozici pět závažnosti rozsahu od 0 do 5. Výstrahy jsou považovány stejný bez ohledu na závažnost, může shodovat se závažností tak, aby odpovídala obchodní logiky.

1. V dolní části je tlačítko, které umožňuje povolit pravidlo po dokončení. Pravidla jsou ve výchozím nastavení povolená při vytváření. Pokud vyberete možnost Ne, můžete vytvořit výstrahu a je vytvořen ve **zakázané** stavu. Z **pravidla** stránky v Azure monitorování, vyberte ho a klikněte na tlačítko **povolit** povolit výstrahu, jakmile budete připraveni.

### <a name="define-the-action-to-take"></a>Definovat akci provést

1. V části **3. Definování skupiny akce**, klikněte na tlačítko **+ nové skupiny akce**. Skupinu akcí je skupina akcí, které můžete použít napříč více výstrah. Může obsahovat, ale nejsou omezeny na e-mailové oznámení, sady runbook, webhooky a mnoho dalších. Další informace o skupinách akce najdete v tématu [vytvořit a spravovat skupiny akce](../monitoring-and-diagnostics/monitoring-action-groups.md)

1. V **název skupiny akce** pole, zadejte pro něj popisný název a krátký název. Krátký název se používá namísto názvu skupiny úplné akce při odesílání oznámení pomocí této skupiny.

1. V **akce** oddílu pod **typ akce**, vyberte **e-mailu nebo SMS nebo nabízená nebo hlasové**.

1. Na **e-mailu nebo SMS nebo nabízená nebo hlasové** stránky, zadejte jeho název. Zkontrolujte **e-mailu** zaškrtávací políčko a zadejte platnou e-mailovou adresu k použití.

   ![Konfigurace skupiny akce e-mailu](./media/automation-alert-activity-log/add-action-group.png)

1. Klikněte na tlačítko **OK** na **e-mailu nebo SMS nebo nabízená nebo hlasové** stránku zavřete ho a klikněte na tlačítko **OK** zavřete **přidat akci skupinu** stránky. Název zadaný na této stránce se ukládá jako **název akce**.

1. Jakmile budete hotovi, klikněte na **Uložit**. Tím se vytvoří pravidlo, které zobrazuje výstrahy, pokud sada runbook byla dokončena s určitým stavem.

> [!NOTE]
> Při přidávání e-mailovou adresu do skupiny akce, je odeslána e-mailové oznámení s informacemi o tom, že adresa se přidal do skupiny akce.

## <a name="notification"></a>Oznámení

Při splnění kritérií výstrah skupiny akce se spustí akce definovaná. E-mail je odeslán v příkladu v tomto článku. Na následujícím obrázku je příkladem e-mailu, který se zobrazí po spuštění výstrahy:

![E-mailové výstrahy](./media/automation-alert-activity-log/alert-email.png)

Jakmile metrika je už mimo prahovou hodnotu definované, výstraha je deaktivována a skupině akce spuštěna akce definovaná. Pokud je vybraný typ e-mailu akce, řešení e-mail je odeslán informující o tom, že co byl vyřešen.

## <a name="next-steps"></a>Další postup

Pokračujte v následujícím článku na další informace o další způsoby, můžete integrovat alertings do vašeho účtu Automation.

> [!div class="nextstepaction"]
> [Použít výstrahu pro spuštění runbooku automatizace Azure](automation-create-alert-triggered-runbook.md)