---
title: Používání Učebn Labs pro školení – Azure Lab Services
description: Tento článek popisuje, jak použít Azure DevTest Labs pro vytváření cvičení v Azure pro scénáře školení.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 582508968609ba53de14b95b71ee75b5fec9ba93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85445249"
---
# <a name="use-classroom-labs-for-trainings"></a>Používání Učebn Labs pro školení
Služby Azure Labs umožňují pedagogům (učitelům, profesoryům, instruktorům a pomocníkům pro vzdělávání atd.) rychle a snadno vytvořit online prostředí pro zřizování předem nakonfigurovaných výukových prostředí pro praktikanty. Každý praktikant by mohl pro školení použít stejné a izolované prostředí. Zásady je možné použít, aby se zajistilo, že školicí prostředí jsou k dispozici pro každého účastníka pouze v případě, že je potřebují a obsahují dostatek prostředků, jako jsou třeba virtuální počítače – vyžadované pro školení. 

![Testovací prostředí pro učebnu](./media/classroom-labs-scenarios/classroom.png)

Laboratoře v učebně splňují následující požadavky, které jsou nutné k provedení školení v jakémkoli virtuálním prostředí: 

- Stážisté můžou rychle zřídit svoje školicí prostředí.
- Každý školicí počítač by měl být stejný.
- Stážisté nemůžou zobrazit virtuální počítače vytvořené jinými praktikanty.
- Řízení nákladů zajištěním, že stážisté nemůžou získat více virtuálních počítačů, než potřebují pro školení a také vypnout virtuální počítače, když je nepoužívají
- Snadno sdílejte školicí prostředí s každým z praktikantů
- Znovu znovu spustit školicí prostředí a znovu

V tomto článku se dozvíte o různých funkcích Azure Lab Services, které se dají použít ke splnění předchozích popsaných požadavků na školení a podrobných kroků, které můžete provést při nastavení testovacího prostředí pro školení.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Vytvoření účtu testovacího prostředí jako správce účtu testovacího prostředí
Prvním krokem při použití Azure Lab Services je vytvoření účtu testovacího prostředí v Azure Portal. Po vytvoření účtu testovacího prostředí správcem účtu testovacího prostředí přidá správce uživatele, kteří chtějí vytvořit cvičení, do role **testovacího prostředí** . Pedagogy vytvářejí laboratoře s virtuálními počítači, které studentům povedou cvičení pro kurz, který jsou výukou. Podrobnosti najdete v tématu [Vytvoření a Správa účtu testovacího prostředí](how-to-manage-lab-accounts.md).

## <a name="create-and-manage-classroom-labs"></a>Vytváření a správa testovacích prostředí v učebnách
Educator, který je členem role tvůrce testovacího prostředí v účtu testovacího prostředí, může vytvořit jednu nebo více cvičení v účtu testovacího prostředí. Vytvoříte a nakonfigurujete virtuální počítač šablony s veškerým požadovaným softwarem pro provádění cvičení ve vašem kurzu. Předem vytvořenou image můžete vybrat z dostupných imagí pro vytvoření testovacího prostředí pro učebnu a pak ho přizpůsobit instalací softwaru potřebného pro testovací prostředí. Podrobnosti najdete v tématu [vytváření a Správa učebných cvičení](how-to-manage-classroom-labs.md).

## <a name="configure-usage-settings-and-policies"></a>Konfigurace nastavení a zásad použití
Tvůrce testovacího prostředí může přidat nebo odebrat uživatele do testovacího prostředí, získat odkaz na registraci pro odesílání na uživatele testovacího prostředí, nastavit zásady, jako je například nastavení jednotlivých kvót na uživatele, aktualizovat počet virtuálních počítačů dostupných v testovacím prostředí a další. Podrobnosti najdete v tématu [Konfigurace nastavení a zásad použití](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Vytvoření a správa plánů
Plány umožňují nakonfigurovat prostředí učebny tak, aby se virtuální počítače v testovacím prostředí automaticky spouštěly a vypnuly v určitou dobu. Můžete definovat Jednorázový plán nebo plán opakování. Podrobnosti najdete v tématu [vytváření a Správa plánů pro učebn Labs](how-to-create-schedules.md).

## <a name="set-up-and-publish-a-template-vm"></a>Nastavení a publikování virtuálního počítače šablony
Šablona v testovacím prostředí je základní image virtuálního počítače, ze které se vytváří všechny virtuální počítače uživatelů. Nastavte virtuální počítač šablony tak, aby byl nakonfigurovaný přesně s tím, co chcete poskytnout účastníkům školení. Můžete zadat název a popis šablony, které uvidí uživatelé testovacího prostředí. Pak publikujete šablonu, aby byly k dispozici instance virtuálního počítače šablony pro vaše uživatele testovacího prostředí. Jakmile publikujete šablonu, vytvoří služba Azure Lab Services pomocí této šablony virtuální počítače v testovacím prostředí. Počet virtuálních počítačů, které se v tomto procesu vytvoří, se rovná maximálnímu počtu uživatelů, kteří mohou k testovacímu prostředí přistupovat. Tento počet můžete nastavit v zásadách používání testovacího prostředí. Všechny virtuální počítače mají stejnou konfiguraci jako šablona. Podrobnosti najdete v tématu [nastavení a publikování virtuálních počítačů šablony](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Použití virtuálních počítačů v prostředí učebny
Účastník nebo školení účastníka zaregistruje do testovacího prostředí a připojí se k virtuálnímu počítači a provede cvičení pro daný kurz. Podrobnosti najdete v tématu [Jak získat přístup k prostředí učebny](how-to-use-classroom-lab.md).

## <a name="next-steps"></a>Další kroky
Začněte vytvořením účtu testovacího prostředí v učebně Labs podle pokynů v článku [kurz: nastavení účtu testovacího prostředí pomocí Azure Lab Services](tutorial-setup-lab-account.md).