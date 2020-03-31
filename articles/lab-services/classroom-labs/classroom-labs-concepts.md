---
title: Koncepty učebních laboratoří – Služby Azure Lab Services | Dokumenty společnosti Microsoft
description: Seznamte se se základními koncepty služeb Lab services a jak může usnadnit vytváření a správu testovacích prostředí.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2019
ms.author: spelluru
ms.openlocfilehash: 15fd3c18d059466c2b2bd5e2431013f393092b4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526225"
---
# <a name="classroom-labs-concepts"></a>Koncepce testovacích prostředí v učebnách

Následující seznam obsahuje klíčové koncepty a definice služeb Lab Services:

## <a name="quota"></a>Kvóta

Kvóta je časový limit (v hodinách), který může učitel nastavit pro studenta, aby používal virtuální virtuální virtuální ho diář v testovacím prostředí. Může být nastavena na 0 nebo určitý počet hodin. Pokud je kvóta nastavena na 0, může student virtuální počítač používat jenom v případě, že běží plán nebo když učitel ručně zapne virtuální počítač pro studenta.  

Kvóta hodin se počítají, když student spustí virtuální ho diář.  Pokud učitel ručně spustí virtuální ho diář pro studenta, kvóta hodiny se nepoužívají pro tohoto studenta.

## <a name="schedules"></a>Plány

Plány jsou časové úseky, které může učitel vytvořit pro kurz, aby byly virtuální virtuální měničy studentů k dispozici pro čas kurzu.  Plány mohou být jednorázové nebo opakované.  Kvóta hodin se nepoužívají, pokud je spuštěn plán.

Existují tři typy plánů: Standardní, Pouze start a Zastavit.

- **Standardní**.  Tento plán spustí všechny virtuální počítače studentů v zadaném čase zahájení a vypnutí všech virtuálních počítače studentů v zadaném čase zastavení.
- **Spustit pouze**.   Tento plán spustí všechny virtuální chodstudentů v určený čas.  Virtuální počítače pro studenty se nezastaví, dokud student nezastaví svůj virtuální počítač prostřednictvím portálu Azure Lab Services nebo dokud nedojde k plánu pouze pro zastavení.
- **Zastavit pouze**.  Tento plán zastaví všechny virtuální chodstudentů v určený čas.  

## <a name="template-virtual-machine"></a>Šablona virtuální hopočítače

Virtuální počítač šablony v testovacím prostředí je základní image virtuálního počítače, ze kterého se vytvářejí virtuální počítače všech uživatelů. Školitelé a tvůrci testovacího prostředí nastavují virtuální počítač šablony a nakonfigurují jej pomocí softwaru, který chtějí poskytnout účastníkům školení k testovacím prostředím. Když publikujete šablonu virtuálního počítače, Azure Lab Services vytvoří nebo aktualizuje virtuální počítače testovacího prostředí na základě šablony virtuálního počítače.

## <a name="user-profiles"></a>Profily uživatelů

Tento článek popisuje různé uživatelské profily v Azure Lab Services.

### <a name="lab-account-owner"></a>Vlastník účtu testovacího prostředí

Správce cloudových prostředků organizace, který je vlastníkem předplatného Azure, obvykle plní roli vlastníka účtu testovacího prostředí a má následující úkoly:

- Nastavit účet testovacího prostředí pro organizaci
- Spravovat a konfigurovat zásady pro všechna testovací prostředí
- Udělovat uživatelům v organizaci oprávnění vytvářet v příslušném účtu testovací prostředí

### <a name="professor"></a>Profesor

Uživatelé jako například učitel nebo online instruktor si většinou vytváří testovací prostředí v učebnách na účtu testovacího prostředí. Pedagog má následující úkoly:

- Vytvořit testovacího prostředí v učebně
- Vytvořit v testovacím prostředí virtuální počítače
- Nainstalovat na virtuálních počítačích příslušný software
- Určit, kdo má mít k testovacímu prostředí přístup
- Poskytnout studentům odkaz pro registraci do testovacího prostředí

### <a name="student"></a>Student

Student má následující úkoly:

- Zaregistrovat se pomocí registračního odkazu, který obdrží od tvůrce testovacího prostředí
- Připojit se k virtuálnímu počítači v testovacím prostředí a používat ho k aktivitám ve vyučování a k práci na úkolech a projektech.

## <a name="next-steps"></a>Další kroky

Začněte vytvořením účtu testovacího prostředí, bez kterého není možné pomocí služby Azure Lab Services vytvořit testovací prostředí v učebnách:

- [Nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md)
