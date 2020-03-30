---
title: Vytváření a sdílení řídicích panelů na webu Azure Portal
description: Tento článek popisuje, jak vytvořit, přizpůsobit, publikovat a sdílet řídicí panely na webu Azure Portal.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: mtillman
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 4c01321662b302103cdedfb5b78dadf89860fb8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132070"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Vytváření a sdílení řídicích panelů na webu Azure Portal

Řídicí panely jsou cílené a uspořádané zobrazení vašich cloudových prostředků na webu Azure Portal. Řídicí panely můžete používat jako pracovní prostor, kde můžete rychle spouštět úlohy pro každodenní operace a monitorovat prostředky. Vytvářejte například vlastní řídicí panely založené na projektech, úkolech nebo uživatelských rolích.

Portál Azure poskytuje výchozí řídicí panel jako výchozí bod. Můžete upravit výchozí řídicí panel. Vytvářejte a přizpůsobujte další řídicí panely a publikujte a sdílejte řídicí panely tak, aby byly dostupné ostatním uživatelům. Tento článek popisuje, jak vytvořit nový řídicí panel, přizpůsobit rozhraní a publikovat a sdílet řídicí panely.

## <a name="create-a-new-dashboard"></a>Vytvoření nového řídicího panelu

V tomto příkladu vytvoříme nový soukromý řídicí panel a přiřadíme název. Začněte těmito kroky:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. V nabídce portálu Azure vyberte **Řídicí panel**. Výchozí zobrazení už může být nastaveno na řídicí panel.

    ![Otevření řídicího panelu](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Vyberte **nový řídicí panel**.

    ![Snímek obrazovky s novým řídicím panelem](./media/azure-portal-dashboards/create-new-dashboard.png)

    Tato akce otevře **Galerii dlaždic**, ze které vyberete dlaždice, a prázdnou mřížku, do které dlaždice uspořádáte.

    ![Snímek obrazovky s galerií dlaždic a prázdnou mřížkou](./media/azure-portal-dashboards/dashboard-name.png)

1. Vyberte text **Můj řídicí panel** v popisku řídicího panelu a zadejte název, který vám pomůže snadno identifikovat vlastní řídicí panel.

1. Chcete-li ukončit režim úprav, vyberte **v** záhlaví stránky přizpůsobení.

Zobrazení řídicího panelu nyní zobrazuje nový řídicí panel. Výběrem šipky vedle názvu řídicího panelu zobrazíte řídicí panely, které máte k dispozici. Seznam může obsahovat řídicí panely, které vytvořili a sdíleli jiní uživatelé.

## <a name="edit-a-dashboard"></a>Úprava řídicího panelu

Teď upravíme řídicí panel a přidáme, změníme velikost a uspořádáme dlaždice, které představují vaše prostředky Azure.

### <a name="add-tiles-from-the-dashboard"></a>Přidání dlaždic z řídicího panelu

Chcete-li na řídicí panel přidat dlaždice, postupujte takto:

1. V ![záhlaví](./media/azure-portal-dashboards/dashboard-edit-icon.png) stránky vyberte upravit ikonu **Upravit.**

    ![Snímek obrazovky se zvýrazněním řídicího panelu](./media/azure-portal-dashboards/dashboard-edit.png)

1. Prohlédněte si **galerii dlaždic** nebo pomocí vyhledávacího pole vyhledejte požadovanou dlaždici.

1. Vyberte **Přidat,** chcete-li přidat dlaždici na řídicí panel s výchozí velikostí a umístěním. Nebo přetáhněte dlaždici do mřížky a umístěte ji na požadované místo.

> [!TIP]
> Pokud spolupracujete s více organizacemi, přidejte dlaždici **identity organizace** na řídicí panel, abyste jasně ukázali, do které organizace prostředky patří.

### <a name="add-tiles-from-a-resource-page"></a>Přidání dlaždic ze stránky zdroje

Existuje alternativní způsob, jak přidat dlaždice na řídicí panel. Mnoho stránek prostředků obsahuje ikonu připínáčku na panelu příkazů. Pokud vyberete ikonu, dlaždice představující zdrojovou stránku se připne k řídicímu panelu, který je aktuálně aktivní. 

![Snímek obrazovky panelu příkazů stránky s ikonou špendlíku](./media/azure-portal-dashboards/dashboard-pin-blade.png)

### <a name="resize-or-rearrange-tiles"></a>Změna velikosti nebo změny uspořádání dlaždic

Pokud chcete změnit velikost dlaždice nebo změnit uspořádání dlaždic na řídicím panelu, postupujte takto:

1. V ![záhlaví](./media/azure-portal-dashboards/dashboard-edit-icon.png) stránky vyberte upravit ikonu **Upravit.**

1. Vyberte místní nabídku v pravém horním rohu dlaždice. Pak zvolte velikost dlaždice. Dlaždice, které podporují libovolnou velikost, obsahují také "úchyt" v pravém dolním rohu, který umožňuje přetáhnout dlaždici na požadovanou velikost.

    ![Snímek obrazovky řídicího panelu s otevřenou nabídkou Velikost iktu](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. Vyberte dlaždici a přetáhněte ji do nového umístění v mřížce a uspořádejte řídicí panel.

### <a name="additional-tile-configuration"></a>Další konfigurace dlaždic

Některé dlaždice mohou vyžadovat větší konfiguraci, aby se zobrazily požadované informace. Například **metriky metriky grafu** musí být nastavena tak, aby zobrazovala metriku z **Azure Monitoru**. Můžete také přizpůsobit data dlaždic a přepsat výchozí nastavení času řídicího panelu.

Na libovolné dlaždici, kterou je třeba nastavit, se zobrazí nápis **Konfigurace dlaždice,** dokud dlaždici neupravíte. Přizpůsobení dlaždice:

1. Chcete-li ukončit režim úprav, vyberte **v** záhlaví stránky přizpůsobení.

1. Vyberte banner a proveďte požadované nastavení.

    ![Snímek obrazovky dlaždice, která vyžaduje konfiguraci](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Dlaždice markdown umožňuje zobrazit vlastní statický obsah na řídicím panelu. Může se jedná o základní pokyny, obrázek, sadu hypertextových odkazů nebo dokonce kontaktní informace. Další informace o použití dlaždice markdown najdete [v tématu Použití dlaždice markdown u řídicích panelů Azure k zobrazení vlastního obsahu](azure-portal-markdown-tile.md).

### <a name="customize-tile-data"></a>Přizpůsobení dat dlaždic

Data na řídicím panelu automaticky zobrazují aktivitu za posledních 24 hodin. Chcete-li zobrazit jiný časový rozsah pouze pro tuto dlaždici, postupujte takto:

1. Z kontextové nabídky nebo filtru ![ikony](./media/azure-portal-dashboards/dashboard-filter.png) filtru v levém horním rohu dlaždice vyberte **Přizpůsobit data dlaždic.**

    ![Snímek obrazovky s kontextovou nabídkou dlaždice](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Zaškrtněte políčko **Přepsat nastavení času řídicího panelu na úrovni dlaždice**.

    ![Snímek obrazovky s dialogovým oknem pro konfiguraci nastavení času dlaždic](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Zvolte časový rozsah, který se má pro tuto dlaždici zobrazit. Můžete si vybrat z posledních 30 minut na posledních 30 dní nebo definovat vlastní rozsah.

1. Zvolte časovou rozlišovací schopnost, která se má zobrazit. Můžete zobrazit kdekoli od jedné minuty přírůstky do jednoho měsíce.

1. Vyberte **Použít**.

## <a name="delete-a-tile"></a>Odstranění dlaždice

Chcete-li odebrat dlaždici z řídicího panelu, postupujte takto:

* Vyberte místní nabídku v pravém horním rohu dlaždice a pak vyberte **Odebrat z řídicího panelu**. Nebo:

* Chcete-li vstoupit do režimu vlastního nastavení, vyberte ![ikonu](./media/azure-portal-dashboards/dashboard-edit-icon.png) **upravit.** Najeďte přes v pravém horním rohu ![dlaždice](./media/azure-portal-dashboards/dashboard-delete-icon.png) a vyberte ikonu odstranění odstranit, abyste odstranili dlaždici z řídicího panelu.

   ![Snímek obrazovky znázorňující odebrání dlaždice z řídicího panelu](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Klonování řídicího panelu

Chcete-li použít existující řídicí panel jako šablonu pro nový řídicí panel, postupujte takto:

1. Ujistěte se, že zobrazení řídicího panelu zobrazuje řídicí panel, který chcete zkopírovat.

1. V záhlaví stránky ![vyberte](./media/azure-portal-dashboards/dashboard-clone.png) ikonu **klonování Klonování**.

1. V režimu úprav se otevře kopie řídicího panelu s názvem **Klonování** názvu *řídicího panelu.* Pomocí předchozích kroků v tomto článku přejmenujte a přizpůsobte řídicí panel.

## <a name="publish-and-share-a-dashboard"></a>Publikování a sdílení řídicího panelu

Když vytvoříte řídicí panel, je ve výchozím nastavení soukromý, což znamená, že jste jediný, kdo ho může vidět. Chcete-li řídicí panely zpřístupnit ostatním uživatelům, můžete je publikovat a sdílet. Další informace najdete [v tématu Sdílení řídicích panelů Azure pomocí řízení přístupu na základě rolí](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Otevření sdíleného řídicího panelu

Pokud chcete najít a otevřít sdílený řídicí panel, postupujte takto:

1. Vyberte šipku vedle názvu řídicího panelu.

1. Vyberte ze zobrazeného seznamu řídicích panelů. Pokud řídicí panel, který chcete otevřít, není v seznamu uveden:

    1. vyberte **Procházet všechny řídicí panely**.

        ![Snímek obrazovky s nabídkou výběru řídicího panelu](./media/azure-portal-dashboards/dashboard-browse.png)

    1. V poli **Typ** vyberte **Sdílené řídicí panely**.

        ![Snímek obrazovky se všemi nabídkami výběru řídicích panelů](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. Vyberte jedno nebo více předplatných. Můžete také zadat text pro filtrování řídicích panelů podle názvu.

    1. Vyberte řídicí panel ze seznamu sdílených řídicích panelů.

## <a name="delete-a-dashboard"></a>Odstranění řídicího panelu

Chcete-li trvale odstranit soukromý nebo sdílený řídicí panel, postupujte takto:

1. Ze seznamu vedle názvu řídicího panelu vyberte řídicí panel, který chcete odstranit.

1. V ![záhlaví](./media/azure-portal-dashboards/dashboard-delete-icon.png) stránky vyberte odstranit ikonu **Odstranit.**

1. U soukromého řídicího panelu vyberte **ok** v potvrzovacím dialogovém okně, abyste řídicí panel odebrali. U sdíleného řídicího panelu zaškrtněte v potvrzovacím dialogovém okně políčko a ověřte, zda publikovaný řídicí panel již nebude možné zobrazit ostatními uživateli. Potom vyberte **OK**.

    ![Snímek obrazovky s potvrzením odstranění](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>Další kroky

* [Sdílení řídicích panelů Azure prostřednictvím Řízení přístupu na základě role](azure-portal-dashboard-share-access.md)
* [Programově vytváření řídicích panelů Azure](azure-portal-dashboards-create-programmatically.md)
