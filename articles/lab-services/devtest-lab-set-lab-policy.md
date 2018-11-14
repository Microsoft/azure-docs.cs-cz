---
title: Správa zásad testovacího prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak definovat zásady testovacího prostředí, jako je například velikosti virtuálních počítačů, maximální virtuálních počítačů na uživatele a vypnutí služby automation.
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
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622019"
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Správa všech zásad pro testovací prostředí ve službě Azure DevTest Labs

Azure DevTest Labs umožňuje řídit náklady a snižuje plýtvání v oblasti vaše testovací prostředí tím, že spravuje zásady (nastavení) pro každý testovací prostředí. Tento článek popisuje podrobný podrobná nastavení jednotlivých zásad.  

## <a name="set-allowed-virtual-machine-sizes"></a>Sada povolené velikosti virtuálního počítače
Zásady pro nastavení povolených velikostí virtuálních počítačů pomáhá minimalizovat plýtvání testovacího prostředí tím, že vám určit, jaké velikosti virtuálních počítačů jsou povoleny v testovacím prostředí. Pokud tato zásada se aktivuje, jenom velikosti virtuálních počítačů z tohoto seznamu lze použít k vytvoření virtuálních počítačů.

1. V [webu Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040), vyberte testovacího prostředí a pak vyberte **konfigurace a zásad**.

    ![Přístup ke konfiguraci a zásady testovacího prostředí](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. Cvičení **konfigurace a zásad** vyberte **povolené velikosti virtuálních počítačů**.
   
    ![Velikosti virtuálních počítačů povolených](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Vyberte **na** pro tuto zásadu povolit a **vypnout** ho zakážete.

1. Pokud tuto zásadu povolit, vyberte jeden nebo více velikostí virtuálních počítačů, které mohou být vytvořeny ve vaší laboratoři.

1. Vyberte **Uložit**.

## <a name="set-virtual-machines-per-user"></a>Sada virtuálních počítačů na uživatele
Zásady pro **virtuálních počítačů na uživatele** umožňuje určit počet virtuálních počítačů, které lze vytvořit podle jednotlivých uživatelů. Pokud se uživatel pokusí o vytvoření nebo deklarací identity virtuálního počítače, pokud byly splněny limit počtu uživatelů, chybová zpráva označuje, že virtuální počítač nemůže být vytvořen nárokován. 

1. Cvičení **konfigurace a zásad** vyberte **virtuálních počítačů na uživatele**.
   
    ![Virtuálních počítačů na uživatele](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Vyberte **Ano** omezit počet virtuálních počítačů na uživatele. Pokud nechcete omezit počet virtuálních počítačů na uživatele, vyberte **ne**. Pokud vyberete **Ano**, zadejte číselnou hodnotu určující počet virtuálních počítačů, které můžou vytvořit nebo jeden uživatel. 

1. Vyberte **Ano** omezit počet virtuálních počítačů, které můžete použít SSD (SSD disk). Pokud nechcete omezit počet virtuálních počítačů, které můžete používat SSD, vyberte **ne**. Pokud vyberete **Ano**, zadejte hodnotu, která udává počet virtuálních počítačů, které lze vytvořit pomocí SSD. 

1. Vyberte **Uložit**.

## <a name="set-virtual-machines-per-lab"></a>Sada virtuálních počítačů podle testovacího prostředí
Zásady pro **virtuálních počítačů podle testovacího prostředí** umožňuje určit počet virtuálních počítačů, které lze vytvořit pro aktuálním testovacím prostředí. Pokud se uživatel pokusí o vytvoření virtuálního počítače, když jste dosáhli limitu testovacího prostředí, chybová zpráva znamená, že virtuální počítač nelze vytvořit. 

1. Cvičení **konfigurace a zásad** vyberte **virtuálních počítačů podle testovacího prostředí**.
   
    ![Počet virtuálních počítačů na testovací prostředí](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Vyberte **Ano** omezit počet virtuálních počítačů podle testovacího prostředí. Pokud nechcete omezit počet virtuálních počítačů podle testovacího prostředí, vyberte **ne**. Pokud vyberete **Ano**, zadejte číselnou hodnotu určující počet virtuálních počítačů, které můžou vytvořit nebo jeden uživatel. 

1. Vyberte **Ano** omezit počet virtuálních počítačů, které můžete použít SSD (SSD disk). Pokud nechcete omezit počet virtuálních počítačů, které můžete používat SSD, vyberte **ne**. Pokud vyberete **Ano**, zadejte hodnotu, která udává počet virtuálních počítačů, které lze vytvořit pomocí SSD. 

1. Vyberte **Uložit**.

## <a name="set-auto-shutdown"></a>Nastavení automatické vypnutí
Vypnutí zásad automatického pomáhá minimalizovat plýtvání testovacího prostředí tím, že umožní určit čas, který se vypnout virtuální počítače v tomto testovacím prostředí.

1. Cvičení **konfigurace a zásad** vyberte **automatické vypnutí**.
   
    ![Automatické vypnutí](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Vyberte **na** pro tuto zásadu povolit a **vypnout** ho zakážete.

1. Pokud tuto zásadu povolit, zadejte čas (a časové pásmo), vypnutí všech virtuálních počítačích v aktuálním testovacím prostředí.

1. Zadejte **Ano** nebo **ne** pro možnost poslat oznámení 15 minut před časem zadané automatického vypnutí. Pokud se rozhodnete **Ano**, zadejte koncový bod adresy URL webhooku nebo e-mailovou adresu, určení, kam chcete oznámení k odeslání nebo odeslání. Uživatel obdrží oznámení a je zadána možnost odložit vypnutí.

   Další informace o webhooků najdete v tématu [vytvoření webhooku nebo funkce rozhraní API Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Vyberte **Uložit**.

Ve výchozím nastavení, jakmile bude povoleno, tyto zásady platí pro všechny virtuální počítače v aktuálním testovacím prostředí. Tato nastavení odebrat z konkrétní virtuální počítač, otevřete panel pro správu Virtuálního počítače a změňte jeho **automatické vypnutí** nastavení.

## <a name="set-auto-shutdown-policy"></a>Nastavení automatického vypnutí zásad
Jako vlastník testovacího prostředí můžete nakonfigurovat plán vypnutí pro všechny virtuální počítače ve vaší laboratoři. Díky tomu dá ušetřit z počítače, které nejsou používány spuštěno (nečinné). Můžou vynutit zásadu vypínání na všechny virtuální počítače testovacího prostředí centrálně, ale také uložit uživatelům testovacího prostředí úsilí z nastavení plánu pro jejich jednotlivých počítačů. Tato funkce umožňuje nastavit zásady v testovacím plánu od nabídky žádný ovládací prvek pro úplné řízení ke uživatelům testovacího prostředí. Jako vlastník testovacího prostředí můžete nakonfigurovat tyto zásady podle následujících kroků:

1. Na domovské stránce testovacího prostředí, vyberte **konfigurace a zásad**.
2. Vyberte **automatické vypnutí zásad** v **plány** část v levé nabídce.
3. Vyberte jednu z možností. Následující části poskytují další podrobnosti o těchto možnostech: nastavení zásad platí pouze pro nové virtuální počítače vytvořené v testovacím prostředí a ne na už existující virtuální počítače. 

    ![Možnosti automatického vypnutí zásad](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Uživatel nastaví plánu a můžete je zrušit
Pokud nastavíte testovacího prostředí na tuto zásadu, uživatelé testovacího prostředí můžete přepsat nebo vyjádřit výslovný nesouhlas v testovacím plánu. Tato možnost umožňuje uživatelé testovacího prostředí plnou kontrolu nad plán vypnutí automatického jejich virtuálních počítačů. Uživatelé testovacího prostředí naleznete v tématu žádné změny v jejich stránce plán vypnutí virtuálního počítače automaticky.

![Automatické vypnutí zásad možnost - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Uživatel nastaví plán a nelze odhlásit
Pokud nastavíte testovacího prostředí na tuto zásadu, uživatelé testovacího prostředí můžete přepsat v testovacím plánu. Ale že nemohou výslovně nesouhlasit automatické vypnutí zásad. Tato možnost zajišťuje, každý počítač ve vaší laboratoři je v rámci plánu automatické vypnutí. Uživatelé testovacího prostředí můžete aktualizovat plán vypnutí automatického jejich virtuálních počítačů a nastavení oznámení o vypnutí.

![Automatické vypnutí zásad možnost - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Uživatel nemá žádnou kontrolu nad plánu nastaveného správcem testovacího prostředí
Pokud nastavíte testovacího prostředí na tuto zásadu, uživatelé testovacího prostředí nelze přepsat nebo vyjádřit výslovný nesouhlas v testovacím plánu. Tato možnost nabízí správce testovacího prostředí úplnou kontrolu nad plán pro každý počítač v testovacím prostředí. Uživatelé testovacího prostředí můžete nastavit jenom automatické vypnutí oznámení pro své virtuální počítače.

![Automatické vypnutí zásad možnost - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="set-autostart"></a>Nastavení automatické spuštění
Zásady autostart umožňuje zadat, pokud by měl být spuštěn virtuální počítače v aktuálním testovacím prostředí.  

1. Cvičení **konfigurace a zásad** vyberte **Autostart**.
   
    ![Automatické spuštění](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Vyberte **na** pro tuto zásadu povolit a **vypnout** ho zakážete.

3. Pokud tuto zásadu povolit, zadejte naplánovaný čas zahájení, časové pásmo a dny v týdnu, pro kterou platí čas. 

4. Vyberte **Uložit**.

Jakmile bude povoleno, není tato zásada použitá automaticky pro všechny virtuální počítače v aktuálním testovacím prostředí. Chcete-li toto nastavení platí pro konkrétní virtuální počítač, otevřete panel pro správu Virtuálního počítače a změňte jeho **Autostart** nastavení.

## <a name="set-expiration-date"></a>Nastavit datum vypršení platnosti
Můžete nastavit vypršení datum, kdy jste [vytvořit virtuální počítač](devtest-lab-add-vm.md). V **upřesňující nastavení**, zvolte ikonu kalendáře pro výběr data, na kterém je virtuální počítač automaticky odstraní. Ve výchozím nastavení virtuální počítač nikdy nevyprší.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další postup
Po definované a použít různé zásady nastavení virtuálních počítačů pro testovací prostředí, tady jsou dále vyzkoušejte:

* [Principy sdílených IP adres](devtest-lab-shared-ip.md) – vysvětluje, jak sdílených IP adresy se používají ve službě DevTest Labs, chcete-li minimalizovat počet veřejných IP adres, které jsou potřebné pro připojení k testovacího prostředí virtuálních počítačů.
* [Konfigurace správy nákladů](devtest-lab-configure-cost-management.md) -ukazuje, jak používat **měsíční Trend nákladů odhadované** grafu  
  Chcete-li zobrazit aktuální měsíc je odhadované náklady k datu a očekávané náklady na konci měsíce.
* [Vytvoření vlastní image](devtest-lab-create-template.md) – když vytvoříte virtuální počítač, určit základ, který může být buď vlastní image nebo Marketplace image. Tento článek ukazuje, jak vytvořit vlastní image ze souboru VHD.
* [Konfigurace imagí Marketplace](devtest-lab-configure-marketplace-images.md) – vytváření virtuálních počítačů založených na imagích Azure Marketplace podporuje Azure DevTest Labs. Tento článek ukazuje, jak určit, které případně Image Azure Marketplace lze použít při vytváření virtuálních počítačů v testovacím prostředí.
* [Vytvoření virtuálního počítače v testovacím prostředí](devtest-lab-add-vm.md) -ukazuje, jak vytvořit virtuální počítač ze základní image (buď vlastní nebo Marketplace) a způsobu práce s artefakty ve virtuálním počítači.

