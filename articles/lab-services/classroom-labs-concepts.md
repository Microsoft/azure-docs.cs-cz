---
title: Koncepty Labs – Azure Lab Services | Microsoft Docs
description: Seznamte se se základními koncepty služby Lab Service a s tím, jak usnadňují vytváření a správu cvičení.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: fa3a8dad195b4b3cbf0786c8923c8b330d148898
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96435514"
---
# <a name="labs-concepts"></a>Koncepce testovacích prostředí

Následující seznam obsahuje klíčové pojmy a definice služby testovacího prostředí:

## <a name="quota"></a>Kvóta

Kvóta je časový limit (v hodinách), který může Educator nastavit pro studenta k použití testovacího virtuálního počítače. Může být nastavené na 0 nebo na určitý počet hodin. Pokud je kvóta nastavená na 0, student může virtuální počítač použít jenom v případě, že je plán spuštěný, nebo když se Educator ručně aktivuje virtuální počítač pro studenta.  

Doba kvóty se počítá, když Student spustí virtuální počítač testovacího prostředí sami.  Pokud Educator spustí virtuální počítač testovacího prostředí pro studenta ručně, nepoužijí se pro tohoto studenta kvóta.

## <a name="schedules"></a>Plány

Plány jsou časové sloty, které může Educator vytvořit pro třídu, takže virtuální počítače studenta jsou k dispozici pro čas třídy.  Plány můžou být jednorázové nebo opakované.  V případě, že je plán spuštěný, nepoužívají se hodiny kvóty.

Existují tři typy plánů: standardní, pouze spustit a zastavit.

- **Standard:**  Tento plán spustí všechny virtuální počítače studenta v zadaném čase zahájení a vypíná všechny virtuální počítače studenta v zadané době zastavení.
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

### <a name="educator"></a>Pedagog

Obvykle uživatelé, jako je učitel nebo online Trainer, vytvoří Labs v rámci účtu testovacího prostředí. Pedagog má následující úkoly:

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
