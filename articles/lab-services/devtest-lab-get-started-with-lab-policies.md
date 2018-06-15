---
title: Správa zásad základní testovacího prostředí v Azure DevTest Labs | Microsoft Docs
description: Informace o nastavení některé základní zásady (nastavení) pro testovacího prostředí v DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 40b8fb360be7b08540e25886aaebe7f911607b6d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787546"
---
# <a name="manage-basic-policies-for-a-lab-in-azure-devtest-labs"></a>Spravovat základní zásady pro testovací prostředí v Azure DevTest Labs

Azure DevTest Labs umožňuje řídit náklady a minimalizovat odpady ve vaší laboratoře podle Správa zásad (nastavení) pro každý testovací prostředí. V tomto článku jste Začínáme se zásadami naučit, jak nastavit dva nejdůležitější zásad – omezení počtu virtuálních počítačů (VM), které můžou vytvořit nebo jeden jeden uživatel a konfigurací automaticky vypnutí. Jak nastavit každé zásadě testovacího prostředí najdete v tématu [definovat zásady testovacího prostředí v Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="accessing-a-labs-policies-in-azure-devtest-labs"></a>Přístup k zásady testovacího prostředí v Azure DevTest Labs
Následující postup vás provede nastavením zásad pro testovací prostředí v Azure DevTest Labs:

K zobrazení (a změna) zásady pro testovacím prostředí, postupujte takto:

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **všechny služby**a potom vyberte **DevTest Labs** ze seznamu.

1. Ze seznamu labs vyberte požadované testovací prostředí.   

1. Vyberte **konfiguraci a zásady**.

    ![Podokno nastavení zásad](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. **Konfiguraci a zásady** podokně obsahuje nabídky nastavení, které můžete zadat. Tento článek se týká jenom nastavení pro **virtuálních počítačů na uživatele**, **Auto-shutdown**, a **automatického spuštění**. Další informace o zbývající nastavení najdete v tématu [Správa všech zásad pro testovací prostředí v Azure DevTest Labs](./devtest-lab-set-lab-policy.md). 
   
## <a name="set-virtual-machines-per-user"></a>Sada virtuálních počítačů na uživatele
Zásady pro **virtuálních počítačů na uživatele** umožňuje určit maximální počet virtuálních počítačů, které je možné vytvářet podle jednotlivých uživatelů. Pokud se uživatel pokusí o vytvoření nebo deklarací identity virtuálního počítače, když byla splněna limit počtu uživatelů, chybová zpráva označuje, že virtuální počítač nelze vytvořit vyžádaná. 

1. V tomto prostředí **konfiguraci a zásady** nabídce vyberte možnost **virtuálních počítačů na uživatele**.
   
    ![Virtuální počítače na uživatele](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Vyberte **Ano** omezit počet virtuálních počítačů na uživatele. Pokud nechcete omezit počet virtuálních počítačů na uživatele, vyberte **ne**. Pokud vyberete **Ano**, zadejte číselnou hodnotu, která určuje maximální počet virtuálních počítačů, které můžou vytvořit nebo jeden uživatel. 

1. Vyberte **Ano** omezit počet virtuálních počítačů, které můžete použít SSD (solid-state disk). Pokud nechcete omezit počet virtuálních počítačů, které můžete použít SSD, vyberte **ne**. Pokud vyberete **Ano**, zadejte hodnotu, která určuje maximální počet virtuálních počítačů, které lze vytvořit pomocí SSD. 

1. Vyberte **Uložit**.

## <a name="set-auto-shutdown"></a>Sada auto vypnutí
Vypnutí automatického zásada pomáhá minimalizovat odpady testovacího prostředí tím, že se vám zadejte dobu, kterou vypnout virtuální počítače v tomto testovacím prostředí.

1. V tomto prostředí **konfiguraci a zásady** podokně, vyberte **Auto-shutdown**.
   
    ![Vypnutí automatického](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Vyberte **na** Chcete-li povolit tuto zásadu a **vypnout** ji zakázat.

1. Pokud povolíte tuto zásadu, zadejte dobu (a časového pásma) a ukončí se všechny virtuální počítače v aktuálním prostředí.

1. Zadejte **Ano** nebo **ne** pro možnost Odeslat oznámení 15 minut před časem zadaný auto vypnutí. Pokud se rozhodnete **Ano**, zadejte koncový bod adresy URL webhooku nebo e-mailová adresa určující, kde se má oznámení odesílat nebo odeslat. Uživatel obdrží oznámení a je zadána možnost zpoždění ukončení.

   Další informace o webhooky najdete v tématu [vytvoření webhooku nebo funkce rozhraní API Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Vyberte **Uložit**.

Ve výchozím nastavení, jakmile bude povoleno, tato zásada platí pro všechny virtuální počítače v aktuálním prostředí. Chcete-li toto nastavení odebrat z konkrétní virtuální počítač, otevřete podokno Správa Virtuálního počítače a změňte jeho **Auto-shutdown** nastavení.

## <a name="set-auto-start"></a>Sada automatického – spuštění
Automaticky spouštěná zásady umožňuje zadat při virtuálních počítačů v aktuálním prostředí by měl být spuštěn.  

1. V tomto prostředí **konfiguraci a zásady** podokně, vyberte **automatického spuštění**.
   
    ![Automatické spuštění](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Vyberte **na** Chcete-li povolit tuto zásadu a **vypnout** ji zakázat.

3. Pokud povolíte tuto zásadu, zadejte naplánovaný čas zahájení, časové pásmo a dny v týdnu, pro kterou platí čas. 

4. Vyberte **Uložit**.

Jakmile bude povoleno, není tato zásada použitá automaticky všechny virtuální počítače v aktuálním prostředí. Chcete-li použít tato nastavení pro existující virtuální počítač, otevřete podokno Správa Virtuálního počítače a změňte jeho **automatického spuštění** nastavení.

## <a name="next-steps"></a>Další postup

- [Definovat zásady testovacího prostředí v Azure DevTest Labs](devtest-lab-set-lab-policy.md) – zjistěte, jak upravit jiných zásad testovacího prostředí.
