---
title: Použití testovacích prostředí v Učebnách pro školení – Azure Lab Services | Dokumentace Microsoftu
description: Zjistěte, jak pomocí Azure DevTest Labs pro trénovací scénáře.
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
ms.date: 02/22/2019
ms.author: spelluru
ms.openlocfilehash: 4d2ba11181977f1976b5ae933e8b93a92424fa96
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60695279"
---
# <a name="use-classroom-labs-for-trainings"></a>Použití testovacích prostředí v Učebnách pro školení
Můžete nastavit testovací prostředí pro školení. Classroom Labs z Azure Lab Services k vytvoření testovacího prostředí pro trénování povolit, kde každý účastník výcviku používá identické a izolované prostředí pro školení. Můžete využít zásady pro zajištění, že školení prostředí jsou k dispozici pro každý účastník výcviku pouze v případě potřeby a obsahovat dostatek prostředků – například virtual machines – požadováno pro vzdělávání. 

![Prostředí v učebně](../media/classroom-labs-scenarios/classroom.png)

Testovací prostředí v učebnách splňuje následující požadavky, které jsou nutné k provádějte školení v jakémkoli virtuální prostředí: 

- Stážisté můžete rychle zřídit prostředími školení
- Každý počítač školení by měly být shodné
- Stážisté neuvidí virtuální počítače vytvořené pomocí jiných prováděli úkoly
- Řízení nákladů díky zajištění, že stážisté nelze získat další virtuální počítače, než potřebují pro trénování a také vypnutí virtuálních počítačů, když nejsou na jejich používání
- Snadné sdílení pomocí každý účastník výcviku testovacího prostředí pro školení
- Znovu a znovu a znovu použít testovací prostředí pro školení

V tomto článku se dozvíte o různých funkcí Azure Lab Services, které lze použít ke splnění výš požadavkům a podrobné kroky, pomocí kterých můžete nastavit testovací prostředí pro školení.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Vytvořte účet testovacího prostředí jako správce účtu testovacího prostředí
Prvním krokem při používání Azure Lab Services je na webu Azure Portal vytvořit účet testovacího prostředí. Poté, co správce účtu lab vytvoří účet testovacího prostředí, Správce přidá uživatele, kteří chtějí vytvářet testovací prostředí na **Autor testovacího prostředí** role. Školitelů vytvářet testovací prostředí s virtuálními počítači, aby studenti mohli dělat cvičení na kurz, který se vyučují. Podrobnosti najdete v tématu [vytvořit a spravovat účet testovacího prostředí](how-to-manage-lab-accounts.md).

## <a name="create-and-manage-classroom-labs"></a>Vytvoření a správa testovacích prostředí v učebnách
Trainer, který je členem role Tvůrce prostředí v účtu testovacího prostředí, můžete vytvořit jeden nebo více testovacích prostředí v rámci účtu testovacího prostředí. Vytvořte a nakonfigurujte šablonu virtuálního počítače s požadovaný software plnit praktická cvičení v kurzu. Vyberte si předem připravená image z dostupných imagí pro vytvoření testovacího prostředí v učebně a potom jej přizpůsobte nainstalováním software potřebný pro testovací prostředí. Podrobnosti najdete v tématu [vytvořit a spravovat testovací prostředí v učebnách](how-to-manage-classroom-labs.md).

## <a name="configure-usage-settings-and-policies"></a>Konfigurace nastavení použití a zásady
Autor testovacího prostředí můžete přidat nebo odebrat uživatele do testovacího prostředí, získat registrační odkaz odesílat uživatelům testovacího prostředí, nastavení zásad, jako je nastavení jednotlivých kvót pro uživatele a aktualizovat počet virtuálních počítačů, které jsou k dispozici v testovacím prostředí a další. Podrobnosti najdete v tématu [konfiguraci zásad a nastavení využití](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Vytvoření a správa plánů
Plány umožňují konfigurovat testovacího prostředí v učebně, tak, aby virtuální počítače v testovacím prostředí automaticky spuštění a vypnutí v určenou dobu. Můžete definovat jednorázového plánu nebo plánu opakování. Podrobnosti najdete v tématu [vytvořit a spravovat plány pro testovací prostředí v učebnách](how-to-create-schedules.md).

## <a name="set-up-and-publish-a-template-vm"></a>Nastavení a publikování šablony virtuálního počítače
Šablona v testovacím prostředí je základní image virtuálního počítače, ze které se vytváří všechny virtuální počítače uživatelů. Nastavení šablony virtuálního počítače tak, aby je nakonfigurován s přesně co byste chtěli poskytnout účastníkům školení. Můžete zadat název a popis šablony, které uvidí uživatelé testovacího prostředí. Potom můžete publikovat šablony, která má instance šablony virtuálního počítače zpřístupnit uživatelům testovacího prostředí. Jakmile publikujete šablonu, vytvoří služba Azure Lab Services pomocí této šablony virtuální počítače v testovacím prostředí. Počet virtuálních počítačů, které se v tomto procesu vytvoří, se rovná maximálnímu počtu uživatelů, kteří mohou k testovacímu prostředí přistupovat. Tento počet můžete nastavit v zásadách používání testovacího prostředí. Všechny virtuální počítače mají stejnou konfiguraci jako šablona. Podrobnosti najdete v tématu [nastavit nahoru a publikovat šablony virtuálních počítačů](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Použití virtuálních počítačů v prostředí v učebně
Student nebo školení účastník zaregistruje do testovacího prostředí a připojí se k virtuálnímu počítači provést cvičení na kurz. Podrobnosti najdete v tématu [jak získat přístup k testovacímu prostředí v učebně](how-to-use-classroom-lab.md).

## <a name="next-steps"></a>Další postup
Začněte vytvořením účtu testovacího prostředí v testovacím prostředím v Učebnách podle pokynů v následujícím článku: [Kurz: Nastavení účtu testovacího prostředí pomocí Azure Lab Services](tutorial-setup-lab-account.md).