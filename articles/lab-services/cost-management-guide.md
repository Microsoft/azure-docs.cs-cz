---
title: Průvodce správou nákladů pro Azure Lab Services
description: Seznamte se s různými způsoby, jak zobrazit náklady na služby testovacího prostředí.
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: article
ms.openlocfilehash: 29f6be5319c5a142ad3ea0d73deb2f95d8cb0d7a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94659722"
---
# <a name="cost-management-for-azure-lab-services"></a>Správa nákladů pro Azure Lab Services

V případě Azure Lab Services lze nákladovou správu rozdělit do dvou různých oblastí: odhad nákladů a analýza nákladů. Odhad nákladů proběhne při nastavení testovacího prostředí, aby se zajistilo, že počáteční struktura testovacího prostředí se bude vejít do očekávaného rozpočtu. Analýza nákladů obvykle probíhá na konci měsíce, aby se určily nezbytné akce příštího měsíce.

## <a name="estimate-the-lab-costs"></a>Odhad nákladů na testovací prostředí

V každém řídicím panelu testovacího prostředí se účtuje **náklady & fakturace** , která obsahuje hrubý odhad toho, co bude testovací prostředí platit za měsíc. Odhad nákladů shrnuje využití hodin s maximálním počtem uživatelů podle odhadovaných nákladů za hodinu. Pokud chcete získat nejpřesnější odhad, nastavte testovací prostředí včetně [plánu](how-to-create-schedules.md). Na řídicím panelu se odrážejí odhadované náklady. 

Tento odhad nemusí zobrazit všechny možné náklady. Není zahrnutý pár prostředků:

- Náklady na přípravu šablony Může se výrazně lišit v množství času potřebného k vytvoření šablony. Náklady na spuštění šablony jsou stejné jako celkové náklady na testovací prostředí za hodinu. 
- Všechny náklady na [galerii sdílených imagí](how-to-use-shared-image-gallery.md) , protože galerie lze sdílet mezi více cvičeními. 
- Hodiny vzniklé, když tvůrce testovacího prostředí spustí virtuální počítač (VM).

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky zobrazující odhad nákladů na řídicí panel](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-the-previous-months-usage"></a>Analyzovat využití v předchozím měsíci

Analýza nákladů slouží ke kontrole využití v předchozím měsíci, což vám pomůže určit případné úpravy testovacího prostředí. V části [Analýza nákladů předplatného](../cost-management-billing/costs/quick-acm-cost-analysis.md)můžete najít rozpis minulých nákladů. V Azure Portal můžete zadat **odběry** do vyhledávacího pole a pak vybrat možnost **předplatná** . 

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky, který zobrazuje vyhledávací pole a možnost předplatných.](./media/cost-management-guide/subscription-search.png)

Vyberte konkrétní předplatné, které chcete zkontrolovat.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky zobrazující výběr předplatného](./media/cost-management-guide/subscription-select.png)

V levém podokně v části **cost management** vyberte **Analýza nákladů** .

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky zobrazující analýzu nákladů předplatného v grafu](./media/cost-management-guide/subscription-cost-analysis.png)

Tento řídicí panel umožňuje podrobnou analýzu nákladů, včetně možnosti exportu do různých typů souborů podle plánu. Další informace najdete v tématu [cost management + Přehled fakturace](../cost-management-billing/cost-management-billing-overview.md).

Můžete filtrovat podle typu prostředku. Pomocí `microsoft.labservices/labaccounts` se zobrazí jenom náklady spojené se službou Lab Services.

## <a name="understand-the-usage"></a>Pochopení využití

Následující snímek obrazovky je příkladem analýzy nákladů.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky, který ukazuje příklad analýzy nákladů na předplatné.](./media/cost-management-guide/cost-analysis.png)

Ve výchozím nastavení existuje šest sloupců: **prostředek**, **typ prostředku**, **umístění**, **název skupiny prostředků**, **značky** a **náklady**. Sloupec **prostředek** obsahuje informace o účtu testovacího prostředí, názvu testovacího prostředí a virtuálním počítači. Řádky, které zobrazují účet testovacího prostředí, název testovacího prostředí a výchozí (druhý a třetí řádek), jsou náklady na testovací prostředí. Využívané virtuální počítače mají náklady, které vidíte pro řádky, které zobrazují účet testovacího prostředí, název testovacího prostředí, výchozí nastavení a název virtuálního počítače. 

V tomto příkladu přidáváme první a druhý řádek (začátek s **aaalab/dockerlab**) celkové náklady za testovací prostředí dockerlab v účtu testovacího prostředí "aaalab".

Chcete-li získat celkové náklady na galerii imagí, změňte typ prostředku na `Microsoft.Compute/Galleries` . V závislosti na tom, kde je galerie uložená, se v galerii sdílených imagí nemusí zobrazovat náklady.

> [!NOTE]
> Galerie sdílených imagí je připojená k účtu testovacího prostředí. To znamená, že více cvičení může používat stejnou bitovou kopii.

## <a name="separate-the-costs"></a>Oddělte náklady

Některé univerzity používaly účet testovacího prostředí a skupinu prostředků jako způsob oddělení tříd. Každá třída má svůj vlastní účet testovacího prostředí a skupinu prostředků. 

V podokně analýza nákladů přidejte filtr na základě názvu skupiny prostředků s odpovídajícím názvem skupiny prostředků pro danou třídu. Pak budou viditelné jenom náklady za tuto třídu. To umožňuje vymazat depřímení mezi třídami při prohlížení nákladů. Pomocí funkce [plánovaného exportu](../cost-management-billing/costs/tutorial-export-acm-data.md) analýzy nákladů můžete stáhnout náklady na jednotlivé třídy v samostatných souborech.

## <a name="manage-costs"></a>Správa nákladů

V závislosti na typu třídy existují způsoby, jak spravovat náklady ke snížení instancí virtuálních počítačů, které jsou spuštěny bez jejich použití studentem.

### <a name="automatic-shutdown-settings-for-cost-control"></a>Nastavení automatického vypnutí pro řízení nákladů

Funkce automatického vypnutí umožňují zabránit úniku hodin využití virtuálních počítačů v laboratořích. Následující nastavení zachytává většinu případů, kdy uživatelé omylem odejdou z virtuálních počítačů se systémem:

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky zobrazující tři nastavení automatického vypnutí](./media/cost-management-guide/auto-shutdown-disconnect.png)

Tato nastavení můžete nakonfigurovat na úrovni účtu testovacího prostředí i na úrovni testovacího prostředí. Pokud je povolíte na úrovni účtu testovacího prostředí, budou aplikovány na všechny laboratoře v rámci účtu testovacího prostředí. U všech nových účtů testovacího prostředí je tato nastavení ve výchozím nastavení zapnutá. 

#### <a name="automatically-disconnect-users-from-virtual-machines-that-the-os-deems-idle"></a>Automaticky odpojí uživatele z virtuálních počítačů, které operační systém považuje za nečinné.

> [!NOTE]
> Toto nastavení je k dispozici pouze pro virtuální počítače s Windows.

Když je zapnuté nastavení **Odpojit uživatele, když jsou virtuální počítače neaktivní** , je uživatel odpojený od všech počítačů v testovacím prostředí, když operační systém Windows považuje relaci za nečinné (včetně virtuálních počítačů šablony). [Definice operačního systému Windows pro nečinnost](/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state) používá dvě kritéria: 

* Absence uživatele: není zadána klávesnice ani myš.
* Nedostatek spotřeby prostředků: všechny procesory a všechny disky byly po určitou procentuální době nečinné.

Uživatelům se ve virtuálním počítači zobrazí zpráva, že se tato zpráva bude odpojí: 

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky zobrazující zprávu upozorňující, že relace byla v průběhu časového limitu nečinná a bude odpojena.](./media/cost-management-guide/idle-timer-expired.png)
 
Virtuální počítač je stále spuštěný, když je uživatel odpojen. Pokud se uživatel znovu připojí k virtuálnímu počítači Přihlaste se, okna nebo soubory, které byly otevřené nebo fungují, které byly neuložené, než se odpojování bude pořád nacházet. V tomto stavu, protože virtuální počítač je spuštěný, se pořád počítá jako aktivní a účtuje náklady. 
 
Chcete-li automaticky vypnout nečinné virtuální počítače s Windows, které jsou odpojené, použijte kombinaci **uživatelů odpojit, pokud jsou virtuální počítače nečinné** , a **vypněte virtuální počítače, když uživatelé odpojí** nastavení.

Pokud například nakonfigurujete nastavení následujícím způsobem:
 
* **Odpojit uživatele, když jsou virtuální počítače nečinné**: 15 minut po zjištění stavu nečinnosti.
* **Vypnutí virtuálních počítačů při odpojení uživatele**: 5 minut poté, co se uživatel odpojí.
 
Virtuální počítače s Windows se automaticky ukončí 20 minut poté, co je uživatel přestane používat. 
 
> [!div class="mx-imgBorder"]
> ![Diagram, který znázorňuje kombinaci nastavení, která vyplývají z automatického vypnutí virtuálního počítače.](./media/cost-management-guide/vm-idle-diagram.png)

#### <a name="automatically-shut-down-virtual-machines-when-users-disconnect"></a>Automaticky vypnout virtuální počítače při odpojení uživatele
 
**Vypnutí virtuálních počítačů při odpojení uživatele** podporuje virtuální počítače se systémem Windows i Linux. Pokud je toto nastavení zapnuté, k automatickému vypnutí dojde v těchto případech:
 
* V případě systému Windows je připojení vzdálené plochy (RDP) odpojeno.
* Pro Linux je připojení SSH odpojeno.
 
> [!NOTE]
> Jsou podporovány pouze [konkrétní distribuce a verze systému Linux](../virtual-machines/extensions/diagnostics-linux.md#supported-linux-distributions) .
 
Můžete určit, jak dlouho by měly virtuální počítače čekat na opětovné připojení uživatele, než se automaticky vypíná. 

#### <a name="automatically-shut-down-virtual-machines-that-are-started-but-users-dont-connect"></a>Automaticky vypne virtuální počítače, které jsou spuštěné, ale uživatelé se nepřipojí.
 
V testovacím prostředí může uživatel spustit virtuální počítač, ale nikdy se k němu nepřipojí. Například:
 
* Plán v testovacím prostředí spustí všechny virtuální počítače pro relaci třídy, ale někteří studenti se neobjeví ani se nepřipojí ke svým počítačům. 
* Uživatel spustí virtuální počítač, ale zapomene se připojit. 
 
Nastavení **vypnout virtuální počítače, když se uživatelé nepřipojují** , zachytí tyto případy a automaticky vypne virtuální počítače. 
 
Informace o tom, jak nakonfigurovat a povolit automatické vypnutí virtuálních počítačů při odpojení, najdete v těchto článcích:

* [Konfigurace automatického vypnutí virtuálních počítačů pro účet testovacího prostředí](how-to-configure-lab-accounts.md)
* [Konfigurace automatického vypnutí virtuálních počítačů pro testovací prostředí](how-to-enable-shutdown-disconnect.md)

### <a name="scheduled-time-vs-quota-time"></a>Naplánovaný čas oproti kvótě

Porozumění [plánovanému](classroom-labs-concepts.md#schedules) času a [kvótě](classroom-labs-concepts.md#quota) vám pomůže nakonfigurovat testovací prostředí, aby lépe vyhovovalo potřebám profesor a studentům. 

Naplánovaný čas je nastavený čas, kdy jsou všechny virtuální počítače studenta spuštěné a jsou k dispozici pro připojení. Naplánovaný čas se běžně používá v případě, že všichni studenti mají své vlastní virtuální počítače a za stanovený čas v průběhu dne se účtují podle pokynů pro profesor (například v hodinách třídy). Nevýhodou je, že všechny virtuální počítače studenta jsou spuštěné a účtují náklady, a to i v případě, že se student přihlašuje k virtuálnímu počítači. 

Doba kvóty je čas přidělený každému studentovi pro použití dle svého uvážení a často se používá pro nezávislou studiu. Virtuální počítače se nespustí, dokud student nespustí virtuální počítač. 

Testovací prostředí může použít buď časovou kvótu, nebo plánovaný čas, nebo kombinaci obou. Pokud třída nepotřebuje plánovaný čas, používejte pro nejúčinnější používání virtuálních počítačů jenom čas kvóty.

### <a name="scheduled-event-stop-only"></a>Naplánovaná událost: pouze zastavit

V plánu můžete přidat typ události, která zastaví všechny počítače v určitou dobu. Někteří vlastníci testovacího prostředí nastavili událost jenom pro zastavení na každý den o půlnoci, aby se snížily náklady a využití kvóty, když student zabrání vypnutí virtuálního počítače, který používají. Nevýhodou na tento typ události znamená, že všechny virtuální počítače se vypnou, a to i v případě, že student používá virtuální počítač.

### <a name="other-costs-related-to-labs"></a>Další náklady související s Labs 

Některé náklady nejsou zahrnuté do služby testovacího prostředí, ale můžou být vázané na službu testovacího prostředí. Sdílenou galerii imagí můžete připojit k testovacímu prostředí, ale nebude se zobrazovat v rámci nákladů služby Lab Services a má náklady. Aby se zachovaly celkové náklady, měli byste z Galerie odebrat všechny nepoužívané image, protože image mají vlastní náklady na úložiště. 

Laboratoře můžou mít připojení k jiným prostředkům Azure prostřednictvím virtuální sítě. Po odebrání testovacího prostředí byste měli virtuální síť a další prostředky odebrat.

## <a name="conclusion"></a>Závěr

Snad informace v tomto článku vám pomohou lépe porozumět nástrojům, které vám pomůžou snížit náklady na využití.