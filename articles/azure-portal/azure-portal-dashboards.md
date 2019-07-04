---
title: Vytvoření a sdílení řídicích panelů Azure portal | Dokumentace Microsoftu
description: Tento článek popisuje, jak vytvořit, upravit, publikování a sdílení řídicích panelů na webu Azure Portal.
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
ms.date: 07/01/2019
ms.author: kfollis
ms.openlocfilehash: 8dd1349ca9ab62484eb6693291e3b869ff079dc1
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537329"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Vytvářejte a sdílejte řídicí panely na webu Azure Portal

Řídicí panely poskytují způsob, jak vytvářet zobrazení zaměření a uspořádané na webu Azure Portal cloudových prostředků. Pomocí řídicích panelů jako pracovní prostor, kde můžete rychle spustit úlohy pro každodenní operace a monitorovat prostředky.  Vytvářejte vlastní řídicí panely podle projektů, úloh a rolí uživatelů, třeba.  Na webu Azure portal poskytuje výchozí řídicí panel jako výchozí bod. Můžete upravit výchozí řídicí panel, vytvořit a přizpůsobit další řídicí panely a publikování a sdílení řídicích panelů je zpřístupnit ostatním uživatelům. Tento článek popisuje, jak vytvořit nový řídicí panel, rozhraní, přizpůsobení a publikování a sdílení řídicích panelů.

## <a name="create-a-new-dashboard"></a>Vytvoření nového řídicího panelu

V tomto příkladu jsme vytvořit nový privátní řídicí panel a přiřaďte název. Postupujte podle těchto kroků, abyste mohli začít:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyberte **řídicí panel** z horní části na levém bočním panelu. Výchozí zobrazení může být již nastaveno na řídicí panel.
1. Vyberte **+ nový řídicí panel**.

    ![Snímek obrazovky s výchozí řídicí panel](./media/azure-portal-dashboards/dashboard-new.png)

4. Tato akce otevře **Galerie dlaždic**, ze kterého vyberete dlaždice a kde budete uspořádat dlaždice prázdnou mřížku.

    ![Snímek obrazovky Galerie dlaždic a prázdnou mřížku](./media/azure-portal-dashboards/dashboard-name.png)

5. Vyberte **Můj řídicí panel** text na řídicím panelu označovat popisky a zadejte název, který vám umožní snadno identifikovat vlastní řídicí panel.
1. Vyberte **přizpůsobení dokončeno** v záhlaví stránky ukončete režim úprav.

Zobrazení řídicího panelu teď zobrazuje prázdný řídicí panel. Vyberte rozevírací seznam vedle názvu řídicího panelu zobrazíte řídicí panely, které jsou k dispozici – tento seznam může obsahovat řídicí panely, které mají vytvořené a sdílené jinými uživateli.

## <a name="edit-a-dashboard"></a>Upravit řídicí panel

Teď Pojďme upravit řídicí panel pro přidání, změna velikosti a uspořádání dlaždic, které představují vašich prostředků Azure.

### <a name="add-tiles"></a>Přidat dlaždice

Přidání dlaždice na řídicí panel, postupujte podle těchto kroků:
1. Vyberte ![upravit ikonu](./media/azure-portal-dashboards/dashboard-edit-icon.png) **upravit** ze záhlaví stránky.

    ![Snímek obrazovky řídicího panelu zvýraznění úpravy](./media/azure-portal-dashboards/dashboard-edit.png)

2. Přejděte **Galerie dlaždic** nebo pomocí vyhledávacího pole najít požadovanou dlaždici.
1. Vyberte **přidat** automaticky přidat dlaždice na řídicí panel s výchozí velikostí a umístění. Na dlaždici přetáhněte do mřížky a umístěte ho na místo, kam chcete.

Mnoho prostředků stránky (označované také jako "listy") obsahují ikonu připínáčku v příkazovém řádku. Pokud vyberete ikonu, dlaždice představující zdrojové stránky je připnout na řídicí panel, který je aktuálně aktivní. Tato metoda je alternativní způsob, jak přidat dlaždice na řídicí panel.

![Snímek obrazovky stránky panelu příkazů s ikona Připnutí](./media/azure-portal-dashboards/dashboard-pin-blade.png)

> [!TIP]
> Pokud pracujete s více než jedné organizace, přidejte **identity organizace** dlaždice na řídicí panel pro organizaci, které jasně označí prostředky patří.
>
>
### <a name="resize-or-rearrange-tiles"></a>Změnit velikost a uspořádání dlaždic
Chcete-li změnit velikost dlaždice nebo přeuspořádat dlaždice na řídicím panelu, postupujte takto:

1. Vyberte ![upravit ikonu](./media/azure-portal-dashboards/dashboard-edit-icon.png) **upravit** ze záhlaví stránky.
1. Vyberte příslušnou kontextovou nabídku v pravém horním rohu dlaždice. Zvolte velikost dlaždice. Dlaždice, které podporují všech velikostí také obsahovat "popisovač" v pravém dolním rohu, který umožňuje přetáhněte dlaždici na požadovanou velikost.

   ![Snímek obrazovky řídicího panelu s nabídkou velikost dlaždice otevřít](./media/azure-portal-dashboards/dashboard-tile-resize.png)

3. Vyberte dlaždici a přetáhněte ho do nového umístění v mřížce uspořádat řídicí panel.

### <a name="additional-tile-configuration"></a>Nastavení Další dlaždice

Některé dlaždice může být nutné další konfigurační informace, které chcete zobrazit. Například **graf metrik** dlaždice obsahuje nastavení pro zobrazení metriky z **Azure Monitor**. Můžete také přizpůsobit data dlaždice na přepsat nastavení času řídicího panelu výchozí.

Libovolnou dlaždici, kterou je potřeba nastavit zobrazí **konfigurovat dlaždice** banner až do přizpůsobení na dlaždici. Vyberte tento banner a pak proveďte požadované instalace.

![Snímek obrazovky dlaždice, která vyžaduje konfiguraci](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Markdown dlaždice umožňuje zobrazit vlastní, statický obsah na řídicím panelu. To může být základní pokyny, bitovou kopii, sadu hypertextové odkazy nebo dokonce kontaktní informace. Další informace o používání dlaždicí markdown, naleznete v tématu [použít vlastní markdown dlaždice](azure-portal-markdown-tile.md).
>
>
### <a name="customize-tile-data"></a>Přizpůsobit data dlaždice

Data na řídicím panelu automaticky zobrazí činnost za posledních 24 hodin. Chcete-li zobrazit různé časové rozpětí pro právě tuto dlaždici, postupujte takto:

1. Vyberte ![ikonu filtru](./media/azure-portal-dashboards/dashboard-filter.png) ikonu filtru v levém horním rohu dlaždice nebo vyberte **přizpůsobit data dlaždice** v místní nabídce.

   ![Snímek obrazovky dlaždice kontextové nabídky](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

2. Zaškrtnutím příslušného políčka **přepsat nastavení času řídicího panelu na úrovni dlaždice**.

   ![Snímek obrazovky dialogového okna ke konfiguraci nastavení času dlaždice](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

3. Zvolte časový rozsah pro tuto dlaždici zobrazit. Můžete vybrat z posledních 30 minut na posledních 30 dnů nebo definovat vlastní rozsah.
1. Vyberte časové intervaly pro zobrazení. Můžete zobrazit kdekoli v přírůstcích po jedné minuty na jeden měsíc.
1. Vyberte **Použít**.

## <a name="delete-a-tile"></a>Odstranění dlaždice

Odebrat dlaždici z řídicího panelu, postupujte takto:

* V pravém horním rohu dlaždice, vyberte příslušnou kontextovou nabídku a pak vyberte **odebrat z řídicího panelu**. nebo,
* Vyberte ![upravit ikonu](./media/azure-portal-dashboards/dashboard-edit-icon.png) **upravit** do režimu úprav. V pravém horním rohu dlaždice při najetí myší a potom vyberte ![ikona odstranění](./media/azure-portal-dashboards/dashboard-delete-icon.png) odstranit ikonu odebrat dlaždici na řídicím panelu.

   ![Snímek obrazovky ukazující, jak odebrat dlaždici na řídicím panelu](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Vytvořit kopii řídicího panelu

Chcete-li použít existující řídicí panel jako šablonu pro nový řídicí panel, postupujte takto:

1. Ujistěte se, že zobrazení řídicího panelu zobrazuje řídicí panel, který chcete zkopírovat.
1. V záhlaví stránky, vyberte ![ikona klonování](./media/azure-portal-dashboards/dashboard-clone.png) **klonování**.
1. Kopii řídicího panelu, s názvem "klon *váš název řídicího panelu*" se otevře v režimu úprav. Přejmenovat a přizpůsobit řídicí panel pomocí předchozích kroků v tomto článku.

## <a name="publish-and-share-a-dashboard"></a>Publikování a sdílení řídicího panelu

Když vytváříte řídicí panel, je ve výchozím nastavení, což znamená, že jste pouze jeden, který je můžou zobrazit privátní. Řídicí panely zpřístupnit ostatním uživatelům, můžete je sdílet s ostatními uživateli. Nejprve je nutné publikovat řídicí panel jako prostředek Azure. Chcete-li publikovat a sdílet vlastní řídicí panel, postupujte podle těchto kroků:

1. Vyberte ![ikona sdílet](./media/azure-portal-dashboards/dashboard-share-icon.png) **sdílet** ze záhlaví stránky. **Sdílení + řízení přístupu** se zobrazil formulář.
1. Ověřte, jestli je název správný řídicího panelu zobrazují.
1. Vyberte **název předplatného**. Uživatelé s přístupem k předplatnému můžete použít na sdílený řídicí panel. Přístup k prostředkům reprezentována jednotlivým dlaždicím se určuje podle řízení přístupu na základě rolí Azure.
1. Zaškrtněte políčko pro publikování tohoto řídicího panelu do skupiny prostředků "řídicí panely" pro vybrané předplatné. Nebo, zrušte zaškrtnutí políčka a zvolte místo toho publikovat na existující skupinu prostředků.
1. Vyberte umístění pro prostředek řídicího panelu. Doporučujeme, abyste že vyhledejte řídicí panel s jiným prostředkům. Poznámka: Pokud se rozhodnete z existující skupiny prostředků, řídicí panel nachází automaticky s danou skupinu prostředků.
1. Vyberte **Publikovat**.

   ![Snímek obrazovky dialogového okna publikování řídicího panelu](./media/azure-portal-dashboards/dashboard-publish.png)

### <a name="set-access-control-on-a-shared-dashboard"></a>Nastavení řízení přístupu na sdílený řídicí panel

Po publikování řídicího panelu spravujte, kdo má přístup k řídicímu panelu pomocí následujících kroků:

1. V **sdílení + řízení přístupu** vyberte **spravovat uživatele**.

   ![Snímek obrazovky řídicího panelu sdílení a přístup k řízení dialogového okna](./media/azure-portal-dashboards/dashboard-share-access-control.png)

2. **Řízení přístupu** otevře se stránka. Na této stránce můžete zkontrolovat úroveň přístupu pro uživatele nebo přidat nové přiřazení role. Při přidání přiřazení role, udělujete oprávnění k řídicímu panelu.

> [!NOTE]
> Dlaždice jsou reprezentativní zobrazení prostředků ve vaší organizaci. Přístup k prostředkům se spravuje prostřednictvím ovládacího prvku přiřazení přístupu podle role a oprávnění se dědí z předplatného na prostředek. Udělení přístupu k řídicímu panelu nebude automaticky přiřadit oprávnění prostředky uvedené na řídicím panelu. Další informace o oprávnění na sdílené řídicí panely a řízení přístupu na základě rolí pro prostředky, najdete v části [sdílení řídicích panelů prostřednictvím řízení přístupu na základě rolí](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Otevření sdíleného řídicího panelu

K vyhledání a otevření sdíleného řídicího panelu, postupujte podle těchto kroků:

1. Vyberte rozevírací seznam vedle názvu řídicího panelu.
1. Vyberte ze seznamu řídicích panelů nebo **procházet všechny řídicí panely** Pokud není uvedená řídicí panel, který chcete spustit.

   ![Snímek obrazovky řídicí panel výběru nabídky](./media/azure-portal-dashboards/dashboard-browse.png)

3. V **typ** pole, vyberte **sdílené řídicí panely**.
1. Vyberte jeden nebo více odběrů. Můžete také zadat text pro filtrování řídicí panely podle názvu.
1. Vyberte řídicí panel ze seznamu sdílených řídicích panelů.

## <a name="delete-a-dashboard"></a>Odstranění řídicího panelu

Pokud chcete trvale odstranit privátní nebo sdíleného řídicího panelu, postupujte takto:

1. Vyberte řídicí panel, který chcete odstranit z rozevíracího seznamu vedle názvu řídicího panelu.
1. Vyberte ![ikona odstranění](./media/azure-portal-dashboards/dashboard-delete-icon.png) **odstranit** ze záhlaví stránky.
1. Pro privátní řídicí panel, vyberte **OK** v potvrzovacím dialogovém okně k odebrání řídicího panelu. Sdílený řídicí panel, v potvrzovacím dialogovém okně zaškrtnutím příslušného políčka potvrďte, že publikovaný řídicí panel nebudou zobrazit nikdo jiný. Pak vyberte **OK**.

   ![Snímek obrazovky potvrzení odstranění](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>Další postup

* [Sdílení řídicích panelů prostřednictvím řízení přístupu na základě rolí](azure-portal-dashboard-share-access.md)
* [Vytváření řídicích panelů Azure prostřednictvím kódu programu](azure-portal-dashboards-create-programmatically.md)
