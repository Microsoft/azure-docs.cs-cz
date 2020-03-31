---
title: Správa laboratorních zásad v laboratořích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Zjistěte, jak definovat zásady testovacího prostředí, jako jsou velikosti virtuálních počítače, maximální počet virtuálních počítače na uživatele a automatizace vypnutí.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 7756aa64-49ca-45a0-9f90-0fd101c7be85
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: aa0ffbd69e73ddbef72e0eabf79f2736079c3d23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270716"
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Správa všech zásad pro testovací prostředí v azure devtest labs

Azure DevTest Labs umožňuje řídit náklady a minimalizuje plýtvání ve vašich testovacích prostředích pomocí správy zásad (nastavení) pro každé testovací prostředí. Tento článek podrobně vysvětluje, jak nastavit jednotlivé zásady.  

## <a name="set-allowed-virtual-machine-sizes"></a>Nastavení povolených velikostí virtuálních strojů
Zásady pro nastavení povolené velikosti virtuálních počítače pomáhá minimalizovat odpad z testovacího prostředí tím, že umožňuje určit, které velikosti virtuálních počítače jsou povoleny v testovacím prostředí. Pokud je tato zásada aktivována, lze k vytvoření virtuálních počítače použít jenom velikosti virtuálních počítače z tohoto seznamu.

1. Na [webu Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)vyberte testovací prostředí a pak vyberte **Konfigurace a zásady**.

    ![Přístup ke konfiguraci a zásadám testovacího prostředí](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. V podokně **Konfigurace a zásady** testovacího prostředí vyberte **Povolené velikosti virtuálních počítačů**.
   
    ![Povolené velikosti virtuálních počítačů](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Výběrem **možnosti Zapnuto** tuto zásadu povolíte a **chcete** ji zakázat.

1. Pokud tuto zásadu povolíte, vyberte jednu nebo více velikostí virtuálních počítačů, které lze vytvořit ve vašem testovacím prostředí.

1. Vyberte **Uložit**.

## <a name="set-virtual-machines-per-user"></a>Nastavení virtuálních počítačů na uživatele
Zásady pro **virtuální počítače na uživatele** umožňuje určit počet virtuálních počítačů, které mohou být vytvořeny jednotlivými uživateli. Pokud se uživatel pokusí vytvořit nebo nárokovat virtuální hod, když byl splněn limit pro uživatele, zobrazí se chybová zpráva, že virtuální ho nelze vytvořit nebo nárokovat. 

1. V podokně **Konfigurace a zásady** testovacího prostředí vyberte **Virtuální počítače na uživatele**.
   
    ![Virtuální počítače na uživatele](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Výběrem **možnosti Ano** omezíte počet virtuálních uživatelů na uživatele. Pokud nechcete omezit počet virtuálních uživatelů na uživatele, vyberte **Ne**. Pokud vyberete **Ano**, zadejte číselnou hodnotu označující počet virtuálních uživatelů, které může uživatel vytvořit nebo nárokovat. 

1. Výběrem **možnosti Ano** omezíte počet virtuálních počítačů, které můžou používat SSD (ssd disk). Pokud nechcete omezit počet virtuálních discích, které můžou používat SSD, vyberte **Ne**. Pokud vyberete **Ano**, zadejte hodnotu označující počet virtuálních měn, které lze vytvořit pomocí ssd. 

1. Vyberte **Uložit**.

## <a name="set-virtual-machines-per-lab"></a>Nastavení virtuálních počítačů podle testovacího prostředí
Zásady pro **virtuální počítače na testovací prostředí** umožňuje určit počet virtuálních počítačů, které lze vytvořit pro aktuální testovací prostředí. Pokud se uživatel pokusí vytvořit virtuální hod, když byl splněn limit testovacího prostředí, zobrazí se chybová zpráva, že virtuální ho nelze vytvořit. 

1. V podokně **Konfigurace a zásady** testovacího prostředí vyberte **Virtuální počítače na testovací prostředí**.
   
    ![Virtuální počítače na testovací prostředí](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Výběrem **možnosti Ano** omezíte počet virtuálních virtuálních discích na testovací prostředí. Pokud nechcete omezit počet virtuálních disek na testovací prostředí, vyberte **Ne**. Pokud vyberete **Ano**, zadejte číselnou hodnotu označující počet virtuálních uživatelů, které může uživatel vytvořit nebo nárokovat. 

1. Výběrem **možnosti Ano** omezíte počet virtuálních počítačů, které můžou používat SSD (ssd disk). Pokud nechcete omezit počet virtuálních discích, které můžou používat SSD, vyberte **Ne**. Pokud vyberete **Ano**, zadejte hodnotu označující počet virtuálních měn, které lze vytvořit pomocí ssd. 

1. Vyberte **Uložit**.

## <a name="set-auto-shutdown"></a>Nastavení automatického vypnutí
Zásady automatického vypnutí pomáhá minimalizovat plýtvání v testovacím prostředí tím, že vám umožní určit čas, který virtuální počítače tohoto testovacího prostředí vypnout.

1. V podokně **Konfigurace a zásady** testovacího prostředí vyberte **možnost Automatické vypnutí**.
   
    ![Automatické vypnutí](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Výběrem **možnosti Zapnuto** tuto zásadu povolíte a **chcete** ji zakázat.

1. Pokud povolíte tuto zásadu, zadejte čas (a časové pásmo) pro vypnutí všech virtuálních zařízení v aktuálním testovacím prostředí.

1. Zadejte **ano** nebo **ne** pro možnost odeslat oznámení 15 minut před zadaným časem automatického vypnutí. Pokud zvolíte **Ano**, zadejte koncový bod adresy URL webhooku nebo e-mailovou adresu určující, kam má být oznámení odesláno nebo odesláno. Uživatel obdrží oznámení a je mu dána možnost odložit vypnutí.

   Další informace o webhooky najdete [v tématu vytvoření webhooku nebo funkce Azure rozhraní API](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Vyberte **Uložit**.

Ve výchozím nastavení, jakmile je tato zásada povolena, platí tato zásada pro všechny virtuální aplikace v aktuálním testovacím prostředí. Pokud chcete toto nastavení odebrat z určitého virtuálního počítače, otevřete podokno správy virtuálního počítače a změňte jeho nastavení **automatického vypnutí.**

## <a name="set-auto-shutdown-policy"></a>Nastavení zásad automatického vypnutí
Jako vlastník testovacího prostředí můžete nakonfigurovat plán vypnutí pro všechny virtuální počítače ve vašem testovacím prostředí. Tímto způsobem můžete ušetřit náklady ze spuštěných počítačů, které nejsou používány (nečinnosti). Můžete vynutit zásady vypnutí na všech virtuálních počítačích testovacího prostředí centrálně, ale také ušetřit uživatelům testovacího prostředí úsilí od nastavení plánu pro jejich jednotlivé počítače. Tato funkce umožňuje nastavit zásady v plánu testovacího prostředí od nabízení žádného ovládacího prvku až po úplné řízení pro uživatele testovacího prostředí. Jako vlastník testovacího prostředí můžete tuto zásadu nakonfigurovat následujícím postupem:

1. Na domovské stránce testovacího prostředí vyberte **Konfigurace a zásady**.
2. V levé nabídce vyberte **zásady** **automatického vypnutí.**
3. Vyberte jednu z možností. V následujících částech jsou uvedeny další podrobnosti o těchto možnostech: Nastavená zásada se vztahuje jenom na nové virtuální aplikace vytvořené v testovacím prostředí a ne na již existující virtuální chody. 

    ![Možnosti zásad automatického vypnutí](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Uživatel nastaví plán a může se odhlásit
Pokud nastavíte testovací prostředí na tuto zásadu, uživatelé testovacího prostředí můžete přepsat nebo odhlásit z plánu testovacího prostředí. Tato možnost uděluje uživatelům testovacího prostředí plnou kontrolu nad plánem automatického vypnutí jejich virtuálních počítačů. Uživatelům testovacího prostředí se na stránce plánu automatického vypnutí virtuálního počítače nezobrazují žádné změny.

![Možnost zásad automatického vypnutí - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Uživatel nastaví plán a nemůže se odhlásit
Pokud nastavíte testovací prostředí na tuto zásadu, uživatelé testovacího prostředí můžete přepsat plán testovacího prostředí. Nemohou se však odhlásit ze zásad automatického vypnutí. Tato možnost zajišťuje, že každý počítač ve vaší laboratoři je v rámci plánu automatického vypnutí. Uživatelé testovacího prostředí můžou aktualizovat plán automatického vypínání svých virtuálních počítačů a nastavit oznámení o vypnutí.

![Možnost zásad automatického vypnutí - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Uživatel nemá žádnou kontrolu nad plánem nastaveným správcem testovacího prostředí.
Pokud nastavíte testovací prostředí na tuto zásadu, uživatelé testovacího prostředí nelze přepsat nebo odhlásit z plánu testovacího prostředí. Tato možnost nabízí správce testovacího prostředí kompletní ovládací prvek podle plánu pro každý počítač v testovacím prostředí. Uživatelé testovacího prostředí můžou nastavit jenom oznámení o automatickém vypnutí pro své virtuální počítače.

![Možnost zásad automatického vypnutí - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="set-autostart"></a>Nastavení automatického spuštění
Zásady automatického spuštění umožňuje určit, kdy mají být spuštěny virtuální chod y v aktuálním testovacím prostředí.  

1. V podokně **Konfigurace a zásady** testovacího prostředí vyberte **možnost Automatické spuštění**.
   
    ![Automatické spuštění](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Výběrem **možnosti Zapnuto** tuto zásadu povolíte a **chcete** ji zakázat.

3. Pokud tuto zásadu povolíte, zadejte plánovaný čas zahájení, časové pásmo a dny v týdnu, pro které se čas vztahuje. 

4. Vyberte **Uložit**.

Jakmile je tato zásada povolena, nebude automaticky použita na žádné virtuální chody v aktuálním testovacím prostředí. Chcete-li toto nastavení použít na konkrétní virtuální ms, otevřete podokno správy virtuálního aplikace a změňte jeho nastavení **automatického spuštění.**

## <a name="set-expiration-date"></a>Nastavit datum vypršení platnosti
Můžete nastavit datum vypršení platnosti při [vytváření virtuálního mísy](devtest-lab-add-vm.md). V **rozšířenénastavení**, zvolte ikonu kalendáře určit datum, kdy se automaticky odstraní virtuální počítač. Ve výchozím nastavení virtuální ho dispone, nikdy vyprší.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další kroky
Až definujete a použijete různá nastavení zásad virtuálních zařízení pro testovací prostředí, vyzkoušejte následující věci:

* [Principy sdílených IP adres](devtest-lab-shared-ip.md) – vysvětluje, jak se sdílené IP adresy používají v devTest Labs, aby se minimalizoval počet veřejných IP adres potřebných k připojení k laboratorním virtuálním počítačům testovacího prostředí.
* [Konfigurace správy nákladů](devtest-lab-configure-cost-management.md) – ukazuje, jak používat graf **Trend měsíčních odhadovaných nákladů**  
  zobrazení odhadovaných nákladů aktuálního měsíce k datu a předpokládaných nákladů na konci měsíce.
* [Vytvořit vlastní image](devtest-lab-create-template.md) – Při vytváření virtuálního virtuálního montova, zadáte základnu, která může být buď vlastní image nebo image Marketplace. Tento článek ukazuje, jak vytvořit vlastní obrázek ze souboru VHD.
* [Konfigurace iontů Marketplace](devtest-lab-configure-marketplace-images.md) – Azure DevTest Labs podporuje vytváření virtuálních počítačů na základě ibi Azure Marketplace. Tento článek ukazuje, jak určit, které image Azure Marketplace se dá použít při vytváření virtuálních počítačů v testovacím prostředí.
* [Vytvoření virtuálního počítače v testovacím prostředí](devtest-lab-add-vm.md) – ukazuje, jak vytvořit virtuální počítač ze základní image (vlastní nebo Marketplace) a jak pracovat s artefakty ve vašem virtuálním počítači.

