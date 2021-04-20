---
title: Vytvoření řídicího panelu v Azure Portal
description: Tento článek popisuje, jak vytvořit a přizpůsobit řídicí panel v Azure Portal.
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.topic: how-to
ms.date: 04/15/2021
ms.openlocfilehash: 0666a9f8ca9df2fa44a7eaa4045c9b5e9a724ff5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726044"
---
# <a name="create-a-dashboard-in-the-azure-portal"></a>Vytvoření řídicího panelu v Azure Portal

Řídicí panely jsou cílené a organizované zobrazení vašich cloudových prostředků v Azure Portal. Řídicí panely slouží jako pracovní prostor, kde můžete monitorovat prostředky a rychle spouštět úlohy pro každodenní operace. Můžete například vytvářet vlastní řídicí panely založené na projektech, úkolech nebo rolích uživatelů.

Azure Portal jako výchozí bod poskytuje výchozí řídicí panel. Můžete upravit výchozí řídicí panel a vytvořit a přizpůsobit další řídicí panely.

> [!NOTE]
> Každý uživatel může vytvořit až 100 privátních řídicích panelů. Pokud [řídicí panel publikujete a sdílíte](azure-portal-dashboard-share-access.md), bude v rámci vašeho předplatného implementován jako prostředek Azure a tento limit se nepočítá.

Tento článek popisuje, jak vytvořit nový řídicí panel a přizpůsobit ho. Informace o sdílení řídicích panelů najdete v tématu [sdílení řídicích panelů Azure pomocí řízení přístupu na základě role v Azure](azure-portal-dashboard-share-access.md).

## <a name="create-a-new-dashboard"></a>Vytvoření nového řídicího panelu

Tento příklad ukazuje, jak vytvořit nový privátní řídicí panel s přiřazeným názvem. Při vytváření jsou všechny řídicí panely soukromé, i když se můžete rozhodnout publikovat a sdílet řídicí panel s dalšími uživateli ve vaší organizaci.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. V nabídce Azure Portal vyberte možnost **řídicí panel**. Výchozí zobrazení již může být nastaveno na řídicí panel.

    ![Snímek obrazovky Azure Portal s vybraným řídicím panelem](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Vyberte **nový řídicí panel** a pak **prázdný řídicí panel**.

    ![Snímek obrazovky s novými možnostmi řídicího panelu](./media/azure-portal-dashboards/create-new-dashboard.png)

    Tato akce otevře **galerii dlaždic**, ze které můžete vybrat dlaždice a prázdnou mřížku, do které uspořádáte dlaždice.

1. V popisku řídicího panelu vyberte text **Můj řídicí panel** a zadejte název, který vám pomůže snadno identifikovat vlastní řídicí panel.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-name.png" alt-text="Snímek obrazovky s prázdnou mřížkou s galerií dlaždic":::

1. Pokud chcete řídicí panel uložit tak, jak je, vyberte **Hotovo přizpůsobení** v záhlaví stránky. Nebo přejděte k další části a přidejte dlaždice a uložte si řídicí panel.

V zobrazení řídicího panelu se teď zobrazí nový řídicí panel. Výběrem šipky vedle názvu řídicího panelu zobrazte řídicí panely, které máte k dispozici. Seznam může obsahovat řídicí panely, které vytvořili a sdíleli jiní uživatelé.

## <a name="edit-a-dashboard"></a>Úprava řídicího panelu

Teď tento řídicí panel upravíte tak, aby bylo možné přidat, změnit jeho velikost a uspořádat dlaždice, které reprezentují vaše prostředky Azure.

### <a name="add-tiles-from-the-tile-gallery"></a>Přidání dlaždic z Galerie dlaždic

Chcete-li přidat dlaždice na řídicí panel, použijte následující postup:

1. ![ ](./media/azure-portal-dashboards/dashboard-edit-icon.png)  V záhlaví stránky řídicího panelu vyberte upravit ikonu Upravit.

    ![Snímek obrazovky s řídicím panelem zvýraznění možnosti úprav](./media/azure-portal-dashboards/dashboard-edit.png)

1. Procházejte **galerii dlaždic** nebo vyhledejte určitou dlaždici pomocí vyhledávacího pole. Vyberte dlaždici, kterou chcete přidat na řídicí panel.

   :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-gallery.png" alt-text="Snímek obrazovky Galerie dlaždic":::

1. Pokud chcete dlaždici přidat na řídicí panel s výchozí velikostí a umístěním, vyberte **Přidat** . Nebo přetáhněte dlaždici do mřížky a umístěte ji tam, kde chcete. Přidejte libovolné požadované dlaždice, ale tady je několik nápadů:

    - Přidáním **všech prostředků** zobrazíte všechny prostředky, které jste už vytvořili.

    - Pokud pracujete s více než jednou organizací, přidejte na řídicí panel dlaždici **identity organizace** a jasně Zobrazte, do které organizace prostředky patří.

1. V případě potřeby změňte velikost dlaždice přetažením a přetažením pravého dolního rohu dlaždice.

1. Změny uložíte tak, že v záhlaví stránky vyberete **Uložit** . Můžete také zobrazit náhled změn bez uložení výběrem možnosti **Náhled** v záhlaví stránky. Z obrazovky Preview můžete vybrat **Uložit** a zachovat změny, **Zrušit** jejich odebrání nebo **Upravit** a přejít zpátky k možnostem úprav a provést další změny.

   :::image type="content" source="media/azure-portal-dashboards/dashboard-save.png" alt-text="Snímek obrazovky s možnostmi náhledu, uložení a zahození":::

### <a name="pin-content-from-a-resource-page"></a>Připnutí obsahu ze stránky prostředků

Další způsob, jak přidat dlaždice na řídicí panel, je přímo ze stránky prostředků.

Mnoho stránek prostředků zahrnuje ikonu připnutí na panelu příkazů. Pokud vyberete tuto ikonu, můžete připnout dlaždici představující zdrojovou stránku na existující řídicí panel nebo na nový řídicí panel, který vytvoříte.

![Snímek obrazovky s panelem příkazů stránky s ikonou připnutí](./media/azure-portal-dashboards/dashboard-pin-blade.png)

V některých případech může být ikona připnutí také na konkrétním obsahu stránky, což znamená, že můžete připnout dlaždici pro konkrétní obsah místo na celou stránku.

### <a name="resize-or-rearrange-tiles"></a>Změna velikosti nebo změny uspořádání dlaždic

Chcete-li změnit velikost dlaždice nebo změnit uspořádání dlaždic na řídicím panelu, postupujte podle následujících kroků:

1. ![V záhlaví stránky vyberte Upravit úpravu ikony ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Upravit** .

1. Vyberte kontextovou nabídku v pravém horním rohu dlaždice. Pak zvolte velikost dlaždice. Dlaždice, které podporují jakoukoli velikost, zahrnují také "popisovač" v pravém dolním rohu, který umožňuje přetáhnout dlaždici na požadovanou velikost.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-resize.png" alt-text="Snímek obrazovky řídicího panelu s otevřenou nabídkou velikost dlaždice":::

1. Vyberte dlaždici a přetáhněte ji na nové místo v mřížce a uspořádejte tak řídicí panel.

### <a name="additional-tile-configuration"></a>Další konfigurace dlaždice

Některé dlaždice můžou vyžadovat další konfiguraci, aby se zobrazily požadované informace. Například dlaždice **grafu metriky** musí být nastavená tak, aby zobrazila metriku z Azure monitor. Můžete také přizpůsobit data dlaždice a přepsat tak výchozí nastavení času řídicího panelu.

Všechny dlaždice, které je třeba nastavit, zobrazí banner, dokud neupravíte dlaždici. V případě **grafu metrik** se banner **v metrikách upraví**. Přizpůsobení dlaždice:

1. V záhlaví stránky vyberte **Uložit** a ukončete režim úprav.

1. Vyberte hlavičku a pak proveďte požadovanou instalaci.

    ![Snímek obrazovky dlaždice, která vyžaduje konfiguraci](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Dlaždice Markdownu vám umožní zobrazit na řídicím panelu vlastní a statický obsah. Může se jednat o základní pokyny, obrázek, sadu hypertextových odkazů nebo dokonce kontaktní informace. Další informace o použití dlaždice Markdownu najdete v tématu [použití dlaždice Markdownu na řídicích panelech Azure k zobrazení vlastního obsahu](azure-portal-markdown-tile.md).

### <a name="customize-tile-data"></a>Přizpůsobení dat dlaždic

Data na řídicím panelu automaticky zobrazují aktivitu za posledních 24 hodin. Chcete-li zobrazit pro tuto dlaždici jiný časový rozsah, postupujte podle následujících kroků:

1. V místní nabídce vyberte **přizpůsobit data dlaždice** nebo z ![ filtru ikony filtru ](./media/azure-portal-dashboards/dashboard-filter.png) v levém horním rohu dlaždice.

    ![Snímek kontextové nabídky dlaždice](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Zaškrtnutím políčka **potlačíte nastavení času řídicího panelu na úrovni dlaždice**.

    ![Snímek obrazovky dialogového okna pro konfiguraci nastavení času dlaždice](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Vyberte časový rozsah, který se má zobrazit pro tuto dlaždici. Můžete si vybrat z posledních 30 minut za posledních 30 dní nebo definovat vlastní rozsah.

1. Vyberte časové rozlišení, které se má zobrazit. Můžete se zobrazit kdekoli od 1 minuty po jeden měsíc.

1. Vyberte **Použít**.

## <a name="delete-a-tile"></a>Odstranění dlaždice

Chcete-li odebrat dlaždici z řídicího panelu, proveďte jednu z následujících akcí:

- Vyberte kontextovou nabídku v pravém horním rohu dlaždice a pak vyberte **Odebrat z řídicího panelu**.

- Vyberte ![ upravit ikonu ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Upravit** a zadejte režim přizpůsobení. Najeďte myší v pravém horním rohu dlaždice a pak vyberte ![ ikonu Odstranit ikona odstranit ](./media/azure-portal-dashboards/dashboard-delete-icon.png) pro odebrání dlaždice z řídicího panelu.

   ![Snímek obrazovky znázorňující, jak odebrat dlaždici z řídicího panelu](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Klonování řídicího panelu

Pokud chcete použít existující řídicí panel jako šablonu pro nový řídicí panel, postupujte takto:

1. Ujistěte se, že se v zobrazení řídicího panelu zobrazuje řídicí panel, který chcete zkopírovat.

1. V záhlaví stránky vyberte klonovat ![ ikonu klonu ](./media/azure-portal-dashboards/dashboard-clone.png) .

1. Kopie řídicího panelu, pojmenovaný **klon** *názvu řídicího panelu* , se otevře v režimu úprav. Pomocí předchozích kroků v tomto článku přejmenujete a přizpůsobíte řídicí panel.

## <a name="publish-and-share-a-dashboard"></a>Publikování a sdílení řídicího panelu

Když vytváříte řídicí panel, je ve výchozím nastavení privátní, což znamená, že jste ten, kdo ho uvidí. Aby byly řídicí panely dostupné ostatním uživatelům, můžete je publikovat a sdílet. Další informace najdete v tématu [sdílení řídicích panelů Azure pomocí řízení přístupu na základě role v Azure](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Otevření sdíleného řídicího panelu

Pokud chcete najít a otevřít sdílený řídicí panel, postupujte takto:

1. Vyberte šipku vedle názvu řídicího panelu.

1. Vyberte ze zobrazeného seznamu řídicích panelů. Pokud řídicí panel, který chcete otevřít, není uvedený:

    1. Vyberte **Procházet všechny řídicí panely**.

        ![Snímek nabídky výběru řídicího panelu](./media/azure-portal-dashboards/dashboard-browse.png)

    1. V poli **typ** vyberte **sdílené řídicí panely**.

        ![Snímek obrazovky nabídky výběr všech řídicích panelů](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. Vyberte jedno nebo více předplatných. Můžete také zadat text pro filtrování řídicích panelů podle názvu.

    1. Vyberte řídicí panel ze seznamu sdílených řídicích panelů.

## <a name="delete-a-dashboard"></a>Odstranění řídicího panelu

K trvalému odstranění privátního nebo sdíleného řídicího panelu použijte následující postup:

1. V seznamu vedle názvu řídicího panelu vyberte řídicí panel, který chcete odstranit.

1. ![V záhlaví stránky vyberte odstranit ikonu ](./media/azure-portal-dashboards/dashboard-delete-icon.png) **Odstranit** .

1. V případě privátního řídicího panelu v potvrzovacím dialogovém okně vyberte **OK** , aby se řídicí panel odebral. U sdíleného řídicího panelu kliknutím na zaškrtávací políčko v potvrzovacím dialogovém okně potvrďte, že publikovaný řídicí panel již nebude možné zobrazit jiní uživatelé. Pak vyberte **OK**.

    ![Snímek obrazovky s potvrzením odstranění](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="recover-a-deleted-dashboard"></a>Obnovení odstraněného řídicího panelu

Pokud jste v globálním cloudu Azure a odstraníte _publikovaný_ řídicí panel v Azure Portal, můžete tento řídicí panel obnovit do 14 dnů od odstranění. Další informace najdete v tématu [obnovení odstraněných řídicích panelů v Azure Portal](recover-shared-deleted-dashboard.md).

## <a name="next-steps"></a>Další kroky

- [Sdílení řídicích panelů Azure pomocí řízení přístupu na základě role v Azure](azure-portal-dashboard-share-access.md)
- [Vytváření řídicích panelů Azure prostřednictvím kódu programu](azure-portal-dashboards-create-programmatically.md)
