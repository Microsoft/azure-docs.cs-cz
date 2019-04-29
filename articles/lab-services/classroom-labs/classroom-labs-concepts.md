---
title: Principy Labs Classroom – Azure Lab Services | Dokumentace Microsoftu
description: Informace o základních konceptech služby testovacího prostředí a jak ho můžete usnadňují vytváření a správa testovacích prostředí.
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
ms.openlocfilehash: 82f9d7090d7283e0b00b36e0928fffb3395ca4c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679081"
---
# <a name="classroom-labs-concepts"></a>Koncepce testovacích prostředí v učebnách
Následující seznam obsahuje klíčové koncepty služby testovacího prostředí a definice:

## <a name="quota"></a>Kvóta
Kvóta je časový limit (v hodinách), můžete nastavit učitel pro studenty použít testovací virtuální počítač. Můžete nastavit na hodnotu 0, neomezený, nebo určitý počet hodin. Kvóty je nastaven na hodnotu 0, student použít virtuální počítač pouze při spuštění plánu nebo když učitel se ručně změní na virtuálním počítači pro studenta.
 
## <a name="schedules"></a>Plány
Plány jsou časové úseky (jednorázově nebo opakovaně), které učitel můžete vytvořit pro třídu. Všechny virtuální počítače v testovacím prostředí se automaticky spustí na začátku plánu a se zastaví na konci plán. Hodiny kvóty nejsou použity při spuštění plánu.

## <a name="template-virtual-machine"></a>Šablona virtuálního počítače
Šablona virtuálního počítače v testovacím prostředí je image základního virtuálního počítače ze které se vytvářejí všechny uživatele počítače. Tvůrce školitelé/lab nastavení šablony virtuálního počítače a konfigurovat se softwarem, které chtějí poskytnout účastníkům školení provedete testovací prostředí. Když publikujete šablonu virtuálního počítače, Azure Lab Services vytvoří nebo aktualizuje testovacího prostředí virtuálních počítačů na základě šablony virtuálního počítače. 


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

## <a name="next-steps"></a>Další postup
Začněte vytvořením účtu testovacího prostředí, bez kterého není možné pomocí služby Azure Lab Services vytvořit testovací prostředí v učebnách:

- [Nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md)
