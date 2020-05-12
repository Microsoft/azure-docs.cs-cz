---
title: Koncepce Labs v učebně – Azure Lab Services | Microsoft Docs
description: Seznamte se se základními koncepty služby Lab Service a s tím, jak usnadňují vytváření a správu cvičení.
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
ms.date: 05/08/2020
ms.author: spelluru
ms.openlocfilehash: a34af2ef779a41c19d4bfb831360948af1a66724
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115043"
---
# <a name="classroom-labs-concepts"></a>Koncepce testovacích prostředí v učebnách

Následující seznam obsahuje klíčové pojmy a definice služby testovacího prostředí:

## <a name="quota"></a>Kvóta

Kvóta je časový limit (v hodinách), který může učitel nastavit pro studenta k použití testovacího virtuálního počítače. Může být nastavené na 0 nebo na určitý počet hodin. Pokud je kvóta nastavená na 0, student může virtuální počítač použít jenom v případě, že je plán spuštěný, nebo když ho pro studenty ručně zapnete na virtuálním počítači.  

Doba kvóty se počítá, když Student spustí virtuální počítač testovacího prostředí sami.  Pokud učitel ručně spustí virtuální počítač testovacího prostředí pro studenta, nepoužijí se pro tohoto studenta kvóta.

## <a name="schedules"></a>Plány

Plány jsou časové sloty, které může učitel vytvořit pro třídu, takže virtuální počítače studenta jsou k dispozici pro čas třídy.  Plány můžou být jednorázové nebo opakované.  V případě, že je plán spuštěný, nepoužívají se hodiny kvóty.

Existují tři typy plánů: standardní, pouze spustit a zastavit.

- **Úroveň Standard**.  Tento plán spustí všechny virtuální počítače studenta v zadaném čase zahájení a vypíná všechny virtuální počítače studenta v zadané době zastavení.
- **Pouze spustit**.   Tento plán spustí všechny virtuální počítače studenta v zadanou dobu.  Virtuální počítače studenta se neukončí, dokud student nezastaví svůj virtuální počítač prostřednictvím portálu Azure Lab Services nebo dojde k zastavení pouze plánu.
- **Pouze zastavit**.  Tento plán zastaví všechny virtuální počítače studenta v zadanou dobu.  

## <a name="template-virtual-machine"></a>Virtuální počítač šablony

Virtuální počítač šablony v testovacím prostředí je základní image virtuálního počítače, ze které se vytvářejí virtuální počítače všech uživatelů. Autoři školitel/testovacích prostředí nastavili virtuální počítač šablony a nakonfigurují ho pomocí softwaru, který chtějí poskytnout pro školení účastníků na cvičení. Když publikujete virtuální počítač šablony, Azure Lab Services vytvoří nebo aktualizuje testovací virtuální počítače založené na virtuálním počítači šablony.

## <a name="user-profiles"></a>Profily uživatelů

Tento článek popisuje různé uživatelské profily v Azure Lab Services.

### <a name="lab-account-owner"></a>Vlastník účtu testovacího prostředí

Správcem IT cloudových prostředků organizace, který vlastní předplatné Azure, funguje jako vlastníkem účtu testovacího prostředí a provádí následující úlohy:

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
