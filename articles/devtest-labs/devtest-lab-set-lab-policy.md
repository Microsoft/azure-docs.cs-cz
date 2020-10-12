---
title: Správa zásad testovacího prostředí v Azure DevTest Labs | Microsoft Docs
description: Naučte se definovat zásady testovacího prostředí, jako jsou velikosti virtuálních počítačů, maximální počet virtuálních počítačů na uživatele a automatizace vypnutí.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a7ada980b2a251b8164f2e1a20f65da54c89ba72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530348"
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Správa všech zásad pro testovací prostředí v Azure DevTest Labs

Azure DevTest Labs vám umožní řídit náklady a minimalizovat odpad v laboratoři tím, že spravuje zásady (nastavení) pro každé testovací prostředí. Tento článek vysvětluje podrobné informace o nastavení jednotlivých zásad.  

## <a name="set-allowed-virtual-machine-sizes"></a>Nastavit povolené velikosti virtuálních počítačů
Zásady pro nastavení povolených velikostí virtuálních počítačů pomáhají minimalizovat odpadní laboratoře tím, že vám umožní určit, jaké velikosti virtuálních počítačů jsou v testovacím prostředí povolené. Pokud se tyto zásady aktivují, dají se k vytváření virtuálních počítačů použít jenom velikosti virtuálních počítačů z tohoto seznamu.

1. V [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)vyberte testovací prostředí a pak vyberte **Konfigurace a zásady**.

    ![Přístup ke konfiguraci a zásadám testovacího prostředí](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. V podokně **Konfigurace a zásady** testovacího prostředí vyberte **povolené velikosti virtuálních počítačů**.
   
    ![Povolené velikosti virtuálních počítačů](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Výběrem **zapnuto** tuto zásadu povolíte a **Off** zakážete.

1. Pokud tuto zásadu povolíte, vyberte jednu nebo víc velikostí virtuálních počítačů, které se v testovacím prostředí dají vytvořit.

1. Vyberte **Uložit**.

## <a name="set-virtual-machines-per-user"></a>Nastavit virtuální počítače na uživatele
Zásady pro **virtuální počítače na uživatele** umožňují zadat počet virtuálních počítačů, které může jednotliví uživatelé vytvořit. Pokud se uživatel pokusí vytvořit nebo vyžádat virtuální počítač, když je dosaženo limitu pro uživatele, zobrazí se chybová zpráva s informacemi o tom, že virtuální počítač nelze vytvořit/získat. 

1. V podokně **Konfigurace a zásady** testovacího prostředí vyberte **virtuální počítače na uživatele**.
   
    ![Počet virtuálních počítačů na uživatele](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Pokud chcete omezit počet virtuálních počítačů na uživatele, vyberte **Ano** . Pokud nechcete omezit počet virtuálních počítačů na uživatele, vyberte možnost **ne**. Pokud vyberete **Ano**, zadejte číselnou hodnotu určující počet virtuálních počítačů, které mohou být vytvořeny nebo vyžádány uživatelem. 

1. Pokud chcete omezit počet virtuálních počítačů, které můžou používat SSD (Solid-State disk), vyberte **Ano** . Pokud nechcete omezit počet virtuálních počítačů, které můžou používat SSD, vyberte **ne**. Pokud vyberete **Ano**, zadejte hodnotu určující počet virtuálních počítačů, které lze vytvořit pomocí SSD. 

1. Vyberte **Uložit**.

## <a name="set-virtual-machines-per-lab"></a>Nastavení virtuálních počítačů na testovací prostředí
Zásady pro **virtuální počítače na testovací prostředí** umožňují určit počet virtuálních počítačů, které se dají vytvořit pro aktuální testovací prostředí. Pokud se uživatel pokusí vytvořit virtuální počítač po splnění limitu testovacího prostředí, zobrazí se chybová zpráva, že virtuální počítač nejde vytvořit. 

1. V podokně **Konfigurace a zásady** testovacího prostředí vyberte **virtuální počítače na testovací prostředí**.
   
    ![Virtuální počítače na testovací prostředí](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Vyberte **Ano** , pokud chcete omezit počet virtuálních počítačů na testovací prostředí. Pokud nechcete omezit počet virtuálních počítačů na testovací prostředí, vyberte možnost **ne**. Pokud vyberete **Ano**, zadejte číselnou hodnotu určující počet virtuálních počítačů, které mohou být vytvořeny nebo vyžádány uživatelem. 

1. Pokud chcete omezit počet virtuálních počítačů, které můžou používat SSD (Solid-State disk), vyberte **Ano** . Pokud nechcete omezit počet virtuálních počítačů, které můžou používat SSD, vyberte **ne**. Pokud vyberete **Ano**, zadejte hodnotu určující počet virtuálních počítačů, které lze vytvořit pomocí SSD. 

1. Vyberte **Uložit**.

## <a name="set-auto-shutdown"></a>Nastavit automatické vypnutí
Zásady automatického vypínání pomáhají minimalizovat testovací odpad tím, že vám umožní určit čas, kdy se virtuální počítače v testovacím prostředí vypnuly.

1. V podokně **Konfigurace a zásady** testovacího prostředí vyberte **Automatické vypnutí**.
   
    ![Automatické vypnutí](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Výběrem **zapnuto** tuto zásadu povolíte a **Off** zakážete.

1. Pokud tuto zásadu povolíte, zadejte čas (a časové pásmo) pro vypnutí všech virtuálních počítačů v aktuálním testovacím prostředí.

1. Zadejte **hodnotu yes** nebo **No** pro možnost odeslání oznámení 15 minut před zadaným časem automatického vypnutí. Pokud zvolíte **Ano**, zadejte koncový bod adresy URL Webhooku nebo e-mailovou adresu, která určuje, kam chcete oznámení publikovat nebo odeslat. Uživatel obdrží oznámení a získá možnost odložit vypnutí.

   Další informace o webhookech najdete v tématu [Vytvoření Webhooku nebo rozhraní API Azure Function](../azure-functions/functions-bindings-http-webhook.md). 

1. Vyberte **Uložit**.

Ve výchozím nastavení platí, že když je tato zásada povolená, vztahuje se na všechny virtuální počítače v aktuálním testovacím prostředí. Pokud chcete toto nastavení odebrat z konkrétního virtuálního počítače, otevřete podokno pro správu virtuálního počítače a změňte jeho nastavení **automatického vypnutí** .

## <a name="set-auto-shutdown-policy"></a>Nastavit zásady automatického vypnutí
Jako vlastník testovacího prostředí můžete nakonfigurovat plán vypnutí pro všechny virtuální počítače v testovacím prostředí. Díky tomu můžete ušetřit náklady ze spuštěných počítačů, které se nepoužívají (nečinné). Zásady vypnutí můžete vystavit na všech virtuálních počítačích v testovacím prostředí centrálně, ale také ukládat uživatele testovacího prostředí s úsilím od nastavení plánu pro jednotlivé počítače. Tato funkce umožňuje nastavit zásady pro plán testovacího prostředí od nabídky bez řízení k úplnému řízení uživatelům testovacího prostředí. Jako vlastník testovacího prostředí můžete nakonfigurovat tuto zásadu provedením následujících kroků:

1. Na domovské stránce testovacího prostředí vyberte **Konfigurace a zásady**.
2. V levé nabídce vyberte v části **plány** možnost **zásady automatického vypnutí** .
3. Vyberte jednu z možností. Následující části obsahují další podrobnosti o těchto možnostech: nastavení zásad se vztahuje pouze na nové virtuální počítače vytvořené v testovacím prostředí, nikoli na již existující virtuální počítače. 

    ![Možnosti zásad automatického vypnutí](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Uživatel nastaví plán a může se odhlásit.
Pokud nastavíte testovací prostředí na tuto zásadu, můžou uživatelé testovacího prostředí potlačit nebo odsouhlasit plán testovacího prostředí. Tato možnost uděluje uživatelům testovacího prostředí plnou kontrolu nad plánem automatického vypnutí svých virtuálních počítačů. Uživatelé testovacího prostředí uvidí na stránce plán automatického vypínání virtuálních počítačů žádné změny.

![Zásada automatického vypnutí – možnost 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Uživatel nastaví plán a nemůže se odhlásit.
Pokud nastavíte testovací prostředí na tuto zásadu, můžou uživatelé testovacího prostředí přepsat plán testovacího prostředí. Nemůžou ale odhlásit zásady automatického vypnutí. Tato možnost zajistí, že každý počítač v testovacím prostředí je v plánu automatického vypnutí. Uživatelé testovacího prostředí můžou aktualizovat plán automatického vypnutí svých virtuálních počítačů a nastavit oznámení o vypnutí.

![Možnost zásad automatického vypnutí – 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Uživatel nemá žádnou kontrolu nad plánem nastaveným správcem testovacího prostředí.
Pokud nastavíte testovací prostředí na tuto zásadu, uživatelé testovacího prostředí nebudou moct přepsat nebo odhlásit plán testovacího prostředí. Tato možnost nabízí správci testovacího prostředí úplnou kontrolu nad plánem pro každý počítač v testovacím prostředí. Uživatelé testovacího prostředí můžou pro svoje virtuální počítače nastavit jenom oznámení o automatickém vypnutí.

![Možnost zásad automatického vypnutí – 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="set-autostart"></a>Nastavit autostart
Zásada autostart umožňuje určit, kdy se mají spustit virtuální počítače v aktuálním testovacím prostředí.  

1. V podokně **Konfigurace a zásady** testovacího prostředí vyberte možnost **autostart**.
   
    ![Snímek obrazovky zobrazuje možnosti automatického spuštění pro virtuální počítač.](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Výběrem **zapnuto** tuto zásadu povolíte a **Off** zakážete.

3. Pokud tuto zásadu povolíte, určete naplánovaný čas zahájení, časové pásmo a dny v týdnu, pro které se použije čas. 

4. Vyberte **Uložit**.

Po povolení se tato zásada nepoužije automaticky pro žádné virtuální počítače v aktuálním testovacím prostředí. Pokud chcete toto nastavení použít na konkrétní virtuální počítač, otevřete podokno pro správu virtuálního počítače a změňte jeho nastavení pro **automatické spuštění** .

## <a name="set-expiration-date"></a>Nastavit datum vypršení platnosti
Když [vytváříte virtuální počítač](devtest-lab-add-vm.md), můžete nastavit datum vypršení platnosti. V části **Upřesnit nastavení**zvolte ikonu kalendáře a zadejte datum, kdy se má virtuální počítač automaticky odstranit. Ve výchozím nastavení virtuální počítač nikdy nevyprší.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další kroky
Po definování a použití různých nastavení zásad virtuálních počítačů pro testovací prostředí můžete vyzkoušet další věci:

* [Pochopení sdílených IP adres](devtest-lab-shared-ip.md) – vysvětluje, jak se ve DevTest Labs používají sdílené IP adresy k minimalizaci počtu veřejných IP adres potřebných pro připojení k virtuálním počítačům testovacího prostředí.
* [Konfigurace správy nákladů](devtest-lab-configure-cost-management.md) – ukazuje, jak používat graf **trendu odhadovaných nákladů měsíčně** .  
  Chcete-li zobrazit odhadované náklady na aktuální měsíc a předpokládané náklady na konci měsíce.
* [Vytvořit vlastní image](devtest-lab-create-template.md) – když vytvoříte virtuální počítač, zadáte základ, který může být buď vlastní image, nebo Image na webu Marketplace. Tento článek ukazuje, jak vytvořit vlastní image ze souboru VHD.
* [Konfigurace imagí Marketplace](devtest-lab-configure-marketplace-images.md) – Azure DevTest Labs podporuje vytváření virtuálních počítačů na základě Azure Marketplace imagí. V tomto článku se naučíte, jak určit, které Azure Marketplace image se můžou použít při vytváření virtuálních počítačů v testovacím prostředí.
* [Vytvoření virtuálního počítače v testovacím prostředí](devtest-lab-add-vm.md) – ukazuje, jak vytvořit virtuální počítač ze základní Image (ať už vlastní nebo tržiště) a jak pracovat s artefakty ve vašem virtuálním počítači.
