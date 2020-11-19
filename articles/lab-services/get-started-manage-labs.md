---
title: Začínáme s Azure Lab Services
description: Tento článek popisuje, jak začít s Azure Lab Services.
ms.topic: article
ms.date: 11/18/2020
ms.openlocfilehash: bdc4f4af06d70a1798e7409b78f6b47530ddd7f4
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917099"
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

Řídicí panely pro učebn Labs v Azure Lab Services poskytují snímek různých aspektů konkrétního testovacího prostředí, včetně informací o virtuálním počítači, počtu přiřazených a nepřiřazených virtuálních počítačů, počtu registrovaných a neregistrovaných uživatelů a informace o plánech testovacího prostředí. 

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

Učitelé můžou přidat uživatele studenta do testovacího prostředí a monitorovat své hodinové kvóty. 

### <a name="add-users-by-email-address"></a>Přidat uživatele podle e-mailové adresy

1. Na [webu Azure Lab Services](https://labs.azure.com/) klikněte na **Uživatelé** na levé straně okna.
1. V horní části okna klikněte na **Přidat uživatele** a vyberte **Přidat podle e-mailové adresy**. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-button.png" alt-text="Tlačítko Přidat uživatele":::
1. V podokně **Přidat uživatele** , které se zobrazí napravo, zadejte e-mailové adresy studentů na samostatné řádky nebo na jeden řádek oddělený středníky.
1. Klikněte na **Uložit**.

    :::image type="content" source="./media/get-started-manage-labs/add-students.png" alt-text="Přidání studentů do testovacího prostředí":::
1. Seznam uživatelů se teď bude aktualizovat s e-maily, stavem, pozvánkou a hodinovou kvótou.

    Po registraci studentů pro testovací prostředí se jejich názvy aktualizují s křestními jmény a příjmeními z Azure Active Directory.

    > [!NOTE]
    > Pro uživatele nechejte zapnutý přepínač omezit přístup. To znamená, že se do testovacího prostředí můžou zaregistrovat jenom uživatelé, kteří si vypíšete pomocí registračního odkazu, který odešlete.

### <a name="add-users-using-a-spreadsheet"></a>Přidání uživatelů pomocí tabulky 

Uživatele můžete také přidat tak, že nahrajete soubor CSV, který obsahuje jejich e-mailové adresy.

1. V aplikaci Microsoft Excel vytvořte soubor CSV se seznamem e-mailových adres studentů v jednom sloupci.
1. Na [webu Azure Lab Services](https://labs.azure.com/)klikněte v horní části stránky **Uživatelé** na tlačítko **Přidat uživatele** .
1. Vyberte **nahrát sdílený svazek clusteru**.
1. Vyberte soubor CSV, který obsahuje e-mailové adresy studentů, a klikněte na **otevřít**.

    :::image type="content" source="./media/get-started-manage-labs/add-users-spreadsheet.png" alt-text="Přidání uživatelů pomocí tabulky":::
1. E-maily se teď zobrazí v okně na pravé straně. Klikněte na **Uložit**.

    :::image type="content" source="./media/get-started-manage-labs/register-users.png" alt-text="Registrovat uživatele":::

### <a name="register-users"></a>Registrovat uživatele

Po přidání do testovacího prostředí se uživatelé budou muset zaregistrovat, aby mohli přistupovat k virtuálním počítačům. To se dá udělat buď pozváním uživatelů z portálu, a pošle vám e-mail s odkazem na registraci testovacího prostředí. Nebo zkopírováním a vložením odkazu na registraci do e-mailu nebo jiné formy komunikace s studenty.

1. Na stránce **Uživatelé** vyberte studenta nebo více studentů v seznamu.

    V řádku pro studenta, který jste vybrali, vyberte ikonu obálky v seznamu nebo klikněte na **pozvat** v horní části obrazovky.

    :::image type="content" source="./media/get-started-manage-labs/send-invitation.png" alt-text="Odeslání pozvánky":::
    
    V okně **poslat pozvánku** e-mailem zadejte volitelnou zprávu (například uživatelské jméno a heslo) studentům a pak klikněte na **Odeslat**. 
    
    :::image type="content" source="./media/get-started-manage-labs/send-invitation-mail.png" alt-text="Poslat pozvánku e-mailem":::

    Případně můžete na stránce stejné **uživatele** kliknout na tlačítko **registrační odkaz** v horní části obrazovky. 

    :::image type="content" source="./media/get-started-manage-labs/registration-link.png" alt-text="Odkaz na registraci uživatele":::
    
    Zkopírujte odkaz na registraci z textového pole a vložte ho do e-mailu nebo preferovaného nástroje pro zabezpečené zasílání zpráv.  
    
    :::image type="content" source="./media/get-started-manage-labs/user-registration.png" alt-text="Odeslat registraci uživatele":::

Poté, co jste pozvaní uživatelé nebo nasdíleli odkaz, budete moci monitorovat, kteří uživatelé se úspěšně zaregistrovali na stránce **Uživatelé** ve sloupci **stav** . 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete nadále používat prostředky, které jste vytvořili v rámci tohoto rychlého startu, odstraňte prostředky.

## <a name="next-steps"></a>Další kroky

[Nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md)
