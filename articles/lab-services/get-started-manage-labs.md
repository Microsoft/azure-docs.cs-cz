---
title: Začínáme s Azure Lab Services
description: Tento článek popisuje, jak začít s Azure Lab Services.
ms.topic: article
ms.date: 11/18/2020
ms.openlocfilehash: d260ace7d7819c3ca2db96d6c4984bf834e170da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98165038"
---
# <a name="get-started-with-lab-services"></a>Začínáme se službami testovacího prostředí 

Azure Lab Services poskytuje studentům a učitelům přístup k virtuálním počítačovým laboratořím přímo ze svých počítačů.

Učitelé potřebují informace o tom, jak naučit studenty a rodiče využívat laboratorní služby ve svých pokynech prostřednictvím vydávaného hardwaru od jednoho k jednomu studenta. V důsledku toho studenti budou mít přístup ke standardnímu softwaru, který se vyžaduje pro své programy studia, prostřednictvím Virtual Machines (VM). 

Virtuální počítač je virtuální prostředí, které funguje jako virtuální počítač. Virtuální počítače mají vlastní procesor, paměť a úložiště. Virtuální počítače poskytují náhradu za skutečný počítač a můžou uživatelům udělit přístup k operačním systémům a softwaru, aniž by je museli mít na svém vlastním zařízení. Azure Lab Services poskytuje studentům Nástroj pro přístup k virtuálním počítačům a k navigaci na virtuální počítače a pro pracovníky ke správě svých virtuálních počítačových laboratoří. 

Tento článek poskytuje informace pro pedagogy, jak získat přístup, spravovat a naučit studenty/rodiče využívat Azure Lab Services.

## <a name="key-concepts"></a>Klíčové koncepty

### <a name="quota-hours"></a>Hodiny kvóty

Studenti můžou k virtuálním počítačům přistupovat kdykoli během plánovaného času třídy, aniž by to ovlivnilo jejich pracovní dobu. Hodiny jsou nastavené na celou dobu pololetí a určují počet hodin, po které student může použít svůj virtuální počítač mimo pravidelně naplánovaný čas třídy.

8 hodin za týden, resetování na neděli – nekumulativní.

Další informace najdete v tématu [nastavení kvóty](how-to-configure-student-usage.md#set-quotas-for-users).

### <a name="automatic-shut-down"></a>Automatické vypnutí

Pro zajištění snížení nákladů a úspory hodinových hodin studentů jsou pro cvičení povoleny automatické vypnutí. Automatické vypínání vypne virtuální počítače po určité době nečinnosti (žádné vstupy myši a klávesnice). Automatické vypínání funguje ve dvou fázích, první student se od virtuálního počítače po určité době nečinnosti odpojí. V tomto okamžiku je virtuální počítač stále **spuštěný** a studenti se můžou připojit. Po odpojení jiného období nečinnosti se virtuální počítač sám ukončí.

Automatické vypínání jsou důležitým nástrojem, který šetří náklady, ale prezentují výzvu pro studenty v souvislosti s ukládáním jejich práce a vykreslováním velkých souborů projektu. Pokud jsou vaši studenti často odpojeni nebo virtuální počítače jsou příliš rychle vypnuté, obraťte se na správce CTE. 

Další informace najdete v tématu [Konfigurace automatického vypnutí virtuálních počítačů pro účet testovacího prostředí](how-to-configure-lab-accounts.md).

### <a name="managing-virtual-machines"></a>Správa virtuálních počítačů

Správa testovacího prostředí umožňuje učitelům řídit věci, jako je například kapacita testovacího prostředí (počet virtuálních počítačů dostupných pro studenty) a ruční spouštění, zastavování nebo resetování virtuálních počítačů. učitelé se taky můžou připojit k virtuálním počítačům a vyzkoušet si rozhraní studenta, přistupovat k souborům a řešit potíže se softwarem nebo samotným virtuálním počítačem.

Nejdůležitějším aspektem při správě virtuálních počítačů je to, že když je počítač **spuštěný**, účtujeme vám náklady, i když k virtuálnímu počítači nikdo nepřipojí.

## <a name="lab-dashboards"></a>Řídicí panely testovacího prostředí

### <a name="overview"></a>Přehled

Řídicí panely pro cvičení v Azure Lab Services poskytují snímek různých aspektů konkrétního testovacího prostředí, včetně informací o virtuálním počítači, počtu přiřazených a nepřiřazených virtuálních počítačů, počtu registrovaných a neregistrovaných uživatelů a informace o plánech testovacího prostředí. 

> [!NOTE]
> I když se většina administrativních aspektů řídicího panelu a [Azure Lab Services Web](https://labs.azure.com/) bude zobrazovat učitelům, můžou oprávnění specifická pro vaši roli ovlivnit vaši možnost upravovat určitá kritéria na řídicím panelu. Pokud narazíte na problém s konkrétním nastavením testovacího prostředí, kontaktujte svého správce CTE.

:::image type="content" source="./media/use-dashboard/dashboard.png" alt-text="Portál Azure Lab Services":::

### <a name="examine-a-dashboard"></a>Kontrola řídicího panelu

1. Přejděte na [web Azure Lab Services](https://labs.azure.com/)a přihlaste se.
1. Vyberte testovací prostředí.
1. Na levé straně okna se zobrazí **řídicí panel** . Klikněte na **řídicí panel** a na řídicím panelu se zobrazí počet dlaždic.
1. Pod dlaždicí **náklady & fakturace** jsou také dlaždice pro šablony, fondy virtuálních počítačů, uživatele a plány, což vám umožní upravit aspekty a zobrazit další podrobnosti o prostředí učebny.

    * Šablona – popisuje datum vytvoření šablony a poslední publikování. 
    * Fond virtuálních počítačů – počet přiřazených a nepřiřazených virtuálních počítačů.
    * Uživatelé – počet registrovaných uživatelů a uživatelů, kteří byli přidáni do testovacího prostředí, ale nejsou zaregistrovaní.
    * Plány – zobrazí nadcházející naplánované události pro testovací prostředí a odkaz k zobrazení dalších událostí.

Další informace najdete v tématu [Použití řídicího panelu](use-dashboard.md).

### <a name="manually-starting-vms"></a>Ruční spouštění virtuálních počítačů

1. Na stránce **fond virtuálních počítačů** můžete spustit všechny virtuální počítače v testovacím prostředí kliknutím na tlačítko **Spustit vše** v horní části stránky.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png" alt-text="Spuštění virtuálních počítačů":::
1. Jednotlivé virtuální počítače lze spustit kliknutím na přepínač stavu. 

    Po **spuštění virtuálního** počítače se přepínací tlačítko spustí **a po jeho** spuštění se spustí.
1. Můžete také vybrat počet virtuálních počítačů pomocí kontrol vlevo od sloupce **název** . 

    Po výběru požadovaných virtuálních počítačů klikněte na tlačítko **Start** v horní části obrazovky.
1. Po spuštění můžete kliknutím na tlačítko **Zastavit vše** zastavit všechny virtuální počítače.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png" alt-text="Zastavení virtuálních počítačů":::

### <a name="stopping-and-resetting-vms"></a>Zastavení a obnovení továrního nastavení virtuálních počítačů

* Jednotlivé virtuální počítače můžete zastavit kliknutím na přepínač stavu.
* Můžete také zastavit více virtuálních počítačů pomocí kontrol a kliknutím na tlačítko Zastavit v horní části obrazovky.

Pokud má student problémy s připojením k virtuálnímu počítači nebo je potřeba resetovat virtuální počítač z jakéhokoli jiného důvodu, můžete použít funkci Reset.
1. Pokud chcete resetovat jeden nebo víc virtuálních počítačů, vyberte je pomocí kontrol a pak klikněte na tlačítko **obnovit** v horní části stránky.
1. V automaticky otevíraném okně klikněte na **resetovat**.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png" alt-text="Resetování virtuálního počítače":::

    > [!NOTE]
    > Zapnutím virtuálního počítače studenta nebude mít vliv na kvótu studenta. Kvóty pro uživatele určují počet hodin testovacího prostředí uživatele mimo plánovaný čas třídy.

### <a name="connect-to-vms"></a>Připojení k virtuálním počítačům

Učitelé se můžou připojit k virtuálnímu počítači studenta, pokud je zapnutý a student není připojený k virtuálnímu počítači. Připojením k virtuálnímu počítači budete mít přístup k místním souborům na virtuálním počítači a pomůžete studentům řešit problémy.

1. Pokud se chcete připojit k virtuálnímu počítači studenta, najeďte myší na virtuální počítač v seznamu a klikněte na tlačítko **připojit** . 
1. Potom postupujte podle příručky Začínáme pro studenty, ať už Chromebooks, Mac nebo PC.

:::image type="content" source="./media/how-to-set-virtual-machine-passwords/connect-student-vm.png" alt-text="Tlačítko připojit k virtuálnímu počítači studenta":::

## <a name="manage-users-in-a-lab"></a>Správa uživatelů v testovacím prostředí

Učitelé můžou přidat uživatele studenta do testovacího prostředí a monitorovat své hodinové kvóty. Podrobnosti o tom, jak přidat uživatele podle e-mailové adresy nebo pomocí seznamu tabulek a zaregistrovat uživatele, najdete v tématu [Přidání a Správa uživatelů testovacího prostředí](how-to-configure-student-usage.md).

Poté, co jste pozvaní uživatelé nebo nasdíleli odkaz, budete moci monitorovat, kteří uživatelé se úspěšně zaregistrovali na stránce **Uživatelé** ve sloupci **stav** . 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete nadále používat prostředky, které jste vytvořili v rámci tohoto rychlého startu, odstraňte prostředky.

## <a name="next-steps"></a>Další kroky

[Nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md)
