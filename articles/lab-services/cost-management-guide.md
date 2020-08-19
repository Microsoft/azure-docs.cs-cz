---
title: Průvodce správou nákladů pro Azure Lab Services
description: Seznamte se s různými způsoby, jak zobrazit náklady na služby testovacího prostředí.
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: article
ms.openlocfilehash: 98ce4d5e82d65d911984dc45615253ddcae33ae1
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589848"
---
# <a name="cost-management-for-azure-lab-services"></a>Správa nákladů pro Azure Lab Services

Nákladová Správa se dá rozdělit do dvou různých oblastí: odhad nákladů a analýza nákladů.  Odhad nákladů nastane při nastavování testovacího prostředí, aby se zajistilo, že počáteční struktura testovacího prostředí se bude vejít do očekávaného rozpočtu.  Analýza nákladů obvykle probíhá na konci měsíce za účelem analýzy nákladů a určení akcí nezbytných pro příští měsíc.

## <a name="estimate-the-lab-costs"></a>Odhad nákladů na testovací prostředí

V každém řídicím panelu testovacího prostředí se účtuje **náklady & fakturace** , která obsahuje hrubý odhad toho, co bude testovací prostředí platit za měsíc.  Odhad nákladů shrnuje využití hodin s maximálním počtem uživatelů podle odhadovaných nákladů na hodiny.  Pokud chcete získat nejpřesnější odhad nastavení testovacího prostředí, včetně [plánu](how-to-create-schedules.md), a na řídicím panelu se budou zobrazovat odhadované náklady.  

Tento odhad nemusí být všechny možné náklady, ale existuje několik prostředků, které nejsou zahrnuté.  Náklady na přípravu šablony nejsou promítnuty do odhadu nákladů.  Může se výrazně lišit v množství času potřebného k vytvoření šablony. Náklady na spuštění šablony jsou stejné jako celkové náklady na testovací prostředí za hodinu. Žádné náklady na [galerii sdílených imagí](how-to-use-shared-image-gallery.md) nejsou zahrnuté do řídicího panelu testovacího prostředí, protože galerie může být sdílená mezi několika cvičeními.  A konečně hodiny, které vznikají, když autor testovacího prostředí spustí počítač, se z tohoto odhadu vyloučí.

> [!div class="mx-imgBorder"]
> ![Odhad nákladů na řídicí panel](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-previous-months-usage"></a>Analyzovat využití předchozích měsíců

Analýza nákladů je určena k revizi využití předchozích měsíců a pomůže vám určit případné úpravy testovacího prostředí.  Rozpis nákladů v minulosti najdete v části [Analýza nákladů na předplatné](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis).  V Azure Portal můžete do pole horní hledání zadat "Subscriptions" (odběry) a pak vybrat možnost předplatná.  

> [!div class="mx-imgBorder"]
> ![Hledání předplatného](./media/cost-management-guide/subscription-search.png)

Vyberte konkrétní předplatné, které se má zkontrolovat.

> [!div class="mx-imgBorder"]
> ![Výběr předplatného](./media/cost-management-guide/subscription-select.png)

 V levém podokně v části **cost management**vyberte analýza nákladů.

> [!div class="mx-imgBorder"]
> ![Analýza nákladů na předplatné](./media/cost-management-guide/subscription-cost-analysis.png)

Tento řídicí panel umožní detailní analýzu nákladů, včetně možnosti exportu do různých typů souborů podle plánu.  Cost Management má mnoho možností, jak získat další informace najdete v tématu [Přehled fakturace cost management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)

Filtrování podle typu prostředku: zobrazí `microsoft.labservices/labaccounts` jenom náklady spojené se službou Lab Services.

## <a name="understand-the-usage"></a>Pochopení využití

Níže je ukázka analýzy nákladů.

> [!div class="mx-imgBorder"]
> ![Analýza nákladů na předplatné](./media/cost-management-guide/cost-analysis.png)

Ve výchozím nastavení je zde šest sloupců: prostředek, typ prostředku, umístění, název skupiny prostředků, značky a náklady.  Sloupec prostředek obsahuje informace o účtu testovacího prostředí, názvu testovacího prostředí a virtuálním počítači.  Řádky s názvem účtu testovacího prostředí/testovacím prostředím/výchozím jsou náklady na testovací prostředí, které lze zobrazit na druhém a třetím řádku.  Za využité virtuální počítače bude účtováno náklady za účet testovacího prostředí/název testovacího prostředí/název virtuálního počítače.  V tomto příkladu přidáme první řádek s druhým řádkem, od "aaalab/dockerlab" získáte celkové náklady za testovací prostředí "dockerlab" v účtu testovacího prostředí "aaalab".

Chcete-li získat informace z Galerie sdílených imagí, změňte typ prostředku na `Microsoft.Compute/Galleries` , což vám poskytne celkové náklady na galerii imagí.  Galerie imagí sdílené složky se v závislosti na tom, kde je galerie uložená, nemusí zobrazovat v cenách.

> [!NOTE]
> Galerie sdílených imagí je připojená k účtu testovacího prostředí.  To znamená, že více cvičení může použít stejný obrázek.

## <a name="separating-costs"></a>Oddělení nákladů

Některé univerzity používaly účet testovacího prostředí a skupinu prostředků jako způsob, jak oddělit různé třídy.  Každá třída bude mít svůj vlastní účet testovacího prostředí a skupinu prostředků. V podokně analýza nákladů přidejte filtr na základě názvu skupiny prostředků s odpovídajícím názvem skupiny prostředků pro třídu a zobrazí se jenom náklady této třídy.  To umožňuje vymazat depřímení mezi různými třídami při prohlížení nákladů.  Funkce [plánovaného exportu](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) analýzy nákladů umožňuje stažení nákladů na jednotlivé třídy v samostatných souborech.

## <a name="managing-costs"></a>Správa nákladů

V závislosti na typu třídy existují způsoby, jak spravovat náklady, abyste snížili, že virtuální počítače jsou spuštěné bez studenta, který tento počítač používá.

### <a name="maximize-cost-control-with-auto-shutdown-settings"></a>Maximalizace řízení nákladů pomocí nastavení automatického vypnutí

Funkce kontroly nákladů automatického vypnutí proaktivně umožňují zabránit úniku hodin využití virtuálních počítačů v laboratořích. Kombinace následujících tří funkcí automatického vypnutí a odpojení zachytává většinu případů, kdy uživatelé omylem odejdou z virtuálních počítačů, na kterých běží:

> [!div class="mx-imgBorder"]
> ![Analýza nákladů na předplatné](./media/cost-management-guide/auto-shutdown-disconnect.png)

Tato nastavení se dají nakonfigurovat na úrovni účtu testovacího prostředí i na úrovni testovacího prostředí. Pokud jsou nastavení povolená na úrovni účtu testovacího prostředí, aplikují se na všechny laboratoře v rámci účtu testovacího prostředí. U všech nových účtů testovacího prostředí je tato nastavení ve výchozím nastavení zapnutá. 

#### <a name="details-about-auto-shutdown-settings"></a>Podrobnosti o nastavení automatického vypnutí

* Automaticky odpojí uživatele z virtuálních počítačů, které operační systém považuje za nečinné (pouze Windows).

    > [!NOTE]
    > Toto nastavení je dostupné jenom pro virtuální počítače s Windows.

    Když je nastavení zapnuté, bude uživatel odpojen od všech počítačů v testovacím prostředí, když operační systém Windows považuje relaci za nečinné (včetně virtuálních počítačů šablony). [Definice operačního systému Windows pro nečinnost](https://docs.microsoft.com/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state) používá dvě kritéria: 

    * Absence uživatele – bez zadání klávesnice nebo myši.
    * Nedostatek spotřeby prostředků – všechny procesory a všechny disky byly po určitou dobu nečinné.

    Uživatelům se zobrazí zpráva podobná této jako u virtuálního počítače před odpojením: 

    > [!div class="mx-imgBorder"]
    > ![Analýza nákladů na předplatné](./media/cost-management-guide/idle-timer-expired.png)
    
    Virtuální počítač je stále spuštěný, když je uživatel odpojen. Pokud se uživatel znovu připojí k virtuálnímu počítači, přihlaste se, Windows nebo otevřené soubory nebo neuložená práce, která se nachází v předchozí části odpojení. V tomto stavu, protože virtuální počítač je spuštěný, se pořád počítá jako aktivní a účtuje náklady. 
    
    Pro automatické vypnutí nečinných virtuálních počítačů s Windows, které jsou odpojené, použijte kombinaci **uživatelů odpojit, pokud jsou virtuální počítače nečinné** , a **vypněte virtuální počítače, když uživatelé odpojí** nastavení.

    Pokud například nakonfigurujete nastavení následujícím způsobem:
    
    * Odpojit uživatele, když jsou virtuální počítače neaktivní – 15 minut po nečinném stavu.
    * Vypněte virtuální počítače, když se uživatelé odpojí – 5 minut po odpojení uživatele.
    
    Virtuální počítače s Windows se automaticky vypíná 20 minut poté, co je uživatel přestane používat. 
    
    > [!div class="mx-imgBorder"]
    > ![Analýza nákladů na předplatné](./media/cost-management-guide/vm-idle-diagram.png)
* Automaticky vypne virtuální počítače, když se uživatelé odpojí (Windows & Linux).
    
    Toto nastavení podporuje virtuální počítače se systémem Windows i Linux. Pokud je toto nastavení zapnuté, k automatickému vypnutí dojde v těchto případech:
    
    * Pro Windows se připojení vzdálené plochy (RDP) odpojí.
    * Pro Linux je připojení SSH odpojeno.
    
    > [!NOTE]
    > Jsou podporovány pouze [konkrétní distribuce a verze systému Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux#supported-linux-distributions) .
    
    Můžete určit, jak dlouho by měly virtuální počítače čekat na opětovné připojení uživatele, než se automaticky vypíná. 
* Automaticky vypne virtuální počítače, které jsou spuštěné, ale uživatelé se nepřipojí.
     
    V testovacím prostředí může uživatel spustit virtuální počítač, ale nikdy se k němu nepřipojí. Příklad:
    
    * Plán v testovacím prostředí spustí všechny virtuální počítače pro relaci třídy, ale někteří studenti se neobjeví ani se nepřipojí ke svým počítačům.  
    * Uživatel spustí virtuální počítač, ale zapomenout se připojit. 
    
    Nastavení "vypnout virtuální počítače, když se uživatelé nepřipojují" zachytí tyto případy a automaticky vypne virtuální počítače.  
    
Informace o tom, jak nakonfigurovat a povolit automatické vypnutí virtuálních počítačů při odpojení, najdete v těchto článcích:

* [Konfigurace automatického vypnutí virtuálních počítačů při nastavení odpojení pro účet testovacího prostředí](how-to-configure-lab-accounts.md)
* [Povolit automatické vypnutí virtuálních počítačů při odpojení](how-to-enable-shutdown-disconnect.md)

### <a name="quota-vs-scheduled-time"></a>Kvóta vs naplánovaný čas

Porozumění [času kvóty](classroom-labs-concepts.md#quota) vs [naplánovaný čas](classroom-labs-concepts.md#schedules) umožní vlastníkovi testovacího prostředí nakonfigurovat prostředí tak, aby lépe vyhovovalo potřebám profesor a studentům.  Naplánovaný čas je nastavený čas, kdy jsou všechny virtuální počítače studenta spuštěné a jsou k dispozici pro připojení.  Běžně naplánované se používá v situaci, kdy všichni studenti budou mít svůj vlastní virtuální počítač, a v průběhu dne, jako jsou třeba hodiny třídy, se budou používat pokyny profesor v nastaveném čase.  Nevýhodou je, že všechny virtuální počítače studenta jsou spuštěné a účtují náklady, a to i v případě, že se student přihlašuje k virtuálnímu počítači.  Doba kvóty je čas přidělený každému studentovi, který můžou používat na svém uvážení a často se používá pro nezávislou studiu. Virtuální počítače se nespustí, dokud student nespustí virtuální počítač.  

Testovací prostředí může použít buď čas kvóty, plánovaný čas, nebo kombinaci obou. Pokud třída nepotřebuje naplánovaný čas, používejte pro nejúčinnější používání virtuálních počítačů jenom čas kvóty.

### <a name="scheduled-event---stop-only"></a>Naplánovaná událost – jenom zastavení

V plánu můžete přidat typ události jenom pro zastavení, který zastaví všechny počítače v určitou dobu.  Někteří vlastníci testovacího prostředí nastavili událost jenom pro zastavení na každý den o půlnoci, aby se snížily náklady a využití kvót, když student zabrání vypnutí virtuálního počítače, který používají.  Nevýhodou na tento typ události znamená, že všechny virtuální počítače budou vypnuté, i když student virtuální počítač používá.

### <a name="other-costs-related-to-labs"></a>Další náklady související s Labs 

Existují náklady, které nejsou zahrnuty do služby testovacího prostředí, ale mohou být vázány na službu testovacího prostředí.  Sdílenou galerii imagí je možné připojit k testovacím prostředím, ale nezobrazuje se v nákladech služby Lab Services a má náklady.  Abychom vám pomohli celkově snížit náklady, měli byste z Galerie odebrat všechny nepoužívané image, protože tyto image mají zdědit náklady na úložiště.  Laboratoře můžou mít připojení k jinému prostředku Azure pomocí virtuální sítě (VNet) po odebrání testovacího prostředí. měli byste virtuální síť a další prostředky odebrat.

## <a name="conclusion"></a>Závěr

Snad výše uvedené informace vám poskytne lepší přehled o nákladech na používání a o tom, jak tyto nástroje umožňují snížit náklady na nadměrné využívání.
