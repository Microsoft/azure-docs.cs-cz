---
title: Použití učebních laboratoří pro školení – Služby Azure Lab Services
description: Tento článek popisuje, jak používat Azure DevTest Labs pro vytváření testovacích prostředí v Azure pro scénáře školení.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 155806222f9e11fec177487b7147d81054ac06ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717982"
---
# <a name="use-classroom-labs-for-trainings"></a>Použití učebních laboratoří pro školení
Můžete nastavit laboratoř pro školení. Učebny Labs služby Azure Lab vám umožní vytvořit testovací prostředí pro vaše školení, kde každý stážista používá identické a izolované prostředí pro školení. Můžete použít zásady, které zajistí, že vzdělávací prostředí jsou k dispozici pro každého stážistu pouze v případě, že je potřebují a obsahují dostatek zdrojů - jako jsou virtuální počítače - potřebné pro školení. 

![Učebna laboratoř](../media/classroom-labs-scenarios/classroom.png)

Učebna Labs splňuje následující požadavky, které jsou nutné k provádění školení v jakémkoli virtuálním prostředí: 

- Stážisté mohou rychle zařídit své vzdělávací prostředí
- Každý tréninkový stroj by měl být totožný
- Stážisté nevidí virtuální chod vytvořené jinými stážisty.
- Řízení nákladů tím, že zajistí, že stážisté nemůže získat více virtuálních počítače, než potřebují pro školení a také vypnutí virtuálních počítače, když je nepoužíváte
- Snadno sdílejte tréninkovou laboratoř s každým stážistou
- Znovu a znovu použít tréninkovou laboratoř

V tomto článku se dozvíte o různých funkcích Azure Lab Services, které lze použít ke splnění dříve popsaných požadavků na školení a podrobné kroky, které můžete postupovat při nastavení testovacího prostředí pro školení.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Vytvoření účtu testovacího prostředí jako správce účtu testovacího prostředí
Prvním krokem při používání Azure Lab Services je vytvoření účtu testovacího prostředí na webu Azure Portal. Poté, co správce účtu testovacího prostředí vytvoří účet testovacího prostředí, správce přidá uživatele, kteří chtějí vytvořit testovací prostředí do role **Tvůrce testovacího prostředí.** Trenéři vytvořit laboratoře s virtuálními stroji pro studenty dělat cvičení pro kurz, který učí. Podrobnosti naleznete v [tématu Vytvoření a správa účtu testovacího prostředí](how-to-manage-lab-accounts.md).

## <a name="create-and-manage-classroom-labs"></a>Vytváření a správa testovacích prostředí v učebnách
Trenér, který je členem role Tvůrce testovacího prostředí v účtu testovacího prostředí, může vytvořit jedno nebo více testovacích prostředí v účtu testovacího prostředí. Vytvoříte a nakonfigurujete virtuální počítač šablony se všemi požadovanými softwarepro cvičení v kurzu. Z dostupných bitových kopií vyberete připravenou bitovou kopii pro vytvoření laboratoře ve třídě a pak ji přizpůsobíte instalací softwaru potřebného pro testovací prostředí. Podrobnosti najdete [v tématu Vytvoření a správa učebních testovacích prostředí](how-to-manage-classroom-labs.md).

## <a name="configure-usage-settings-and-policies"></a>Konfigurace nastavení a zásad použití
Tvůrce testovacího prostředí může přidávat nebo odebírat uživatele do testovacího prostředí, získat odkaz na registraci k odeslání uživatelům testovacího prostředí, nastavit zásady, jako je nastavení jednotlivých kvót na uživatele, aktualizovat počet virtuálních počítačů dostupných v testovacím prostředí a další. Podrobnosti naleznete v [tématu Konfigurace nastavení a zásad použití](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Vytvoření a správa plánů
Plány umožňují nakonfigurovat testovací prostředí učebny tak, aby se virtuální počítače v testovacím prostředí automaticky spouštěly a vypínaly v určený čas. Můžete definovat jednorázový nebo opakovaný plán. Podrobnosti najdete [v tématu Vytváření a správa plánů pro laboratoře ve třídě](how-to-create-schedules.md).

## <a name="set-up-and-publish-a-template-vm"></a>Nastavení a publikování virtuálního počítače šablony
Šablona v testovacím prostředí je základní image virtuálního počítače, ze které se vytváří všechny virtuální počítače uživatelů. Nastavte virtuální počítače šablony tak, aby byl nakonfigurován s přesně to, co chcete poskytnout účastníkům školení. Můžete zadat název a popis šablony, které uvidí uživatelé testovacího prostředí. Potom publikujete šablonu, abyste zpřístupnili instance virtuálního počítače šablony uživatelům testovacího prostředí. Jakmile publikujete šablonu, vytvoří služba Azure Lab Services pomocí této šablony virtuální počítače v testovacím prostředí. Počet virtuálních počítačů, které se v tomto procesu vytvoří, se rovná maximálnímu počtu uživatelů, kteří mohou k testovacímu prostředí přistupovat. Tento počet můžete nastavit v zásadách používání testovacího prostředí. Všechny virtuální počítače mají stejnou konfiguraci jako šablona. Podrobnosti najdete v tématu [Nastavení a publikování virtuálních počítačů šablony](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Použití virtuálních disek on-in v laboratoři učebny
Student nebo školení účastník zaregistruje do laboratoře a připojí se k virtuálnímu zařízení dělat cvičení pro kurz. Podrobnosti najdete v [tématu Jak získat přístup k testovacímu prostředí učebny](how-to-use-classroom-lab.md).

## <a name="next-steps"></a>Další kroky
Začněte s vytvořením testovacího účtu v učebních laboratořích podle pokynů v článku: [Návod: Nastavení účtu testovacího prostředí pomocí služby Azure Lab Services](tutorial-setup-lab-account.md).