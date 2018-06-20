---
title: O službě Azure Lab Services | Microsoft Docs
description: Zjistěte, jak lze pomocí služby Lab Services zjednodušit vytváření, správu a zabezpečení testovacího prostředí s virtuálními počítači, které můžou využívat vývojáři, testeři, pedagogové, studenti a další uživatelé.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/21/2018
ms.author: spelluru
ms.openlocfilehash: e9c3cae7c7129cc489ddd38b5b2de18dd6f52e58
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660495"
---
# <a name="introduction-to-classroom-labs"></a>Úvod k testovacímu prostředí v učebnách
Služba Azure Lab Services vám umožňuje rychle vytvořit cloudové testovací prostředí v učebně. Vyučující vytvoří testovací prostředí v učebně, zřídí virtuální počítače s Windows nebo Linuxem, nainstaluje nezbytný software a nástroje a zpřístupní je studentům. Studenti se ve třídě připojí k virtuálním počítačům v testovacím prostředí a budou je používat na projekty, úkoly a aktivity ve vyučování. 

Testovací prostředí v učebnách jsou spravovanými testovacími prostředími, které spravuje Azure. Služba zajišťuje veškerou správu infrastruktury pro spravovaná testovací prostředí od poskytování potřebného výkonu virtuálních počítačů přes zpracovávání chyb až po škálování infrastruktury. Musíte zadat, jaký typ infrastruktury potřebujete, a nainstalovat veškeré nástroje nebo software potřebný k výuce. Spravovaná testovací prostředí jsou v současné době ve verzi Preview.  

## <a name="scenarios"></a>Scénáře
Toto je hlavní scénář, který testovací prostředí v učebnách služby Azure Lab Services podporují: 

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Nastavení cloudové počítačové učebny s proměnlivou velikostí  

- Vytvořte spravované testovací prostředí v učebně. Stačí, když zadáte své požadavky, a služba už se postará o vytvoření a správu infrastruktury testovacího prostředí. Vy se tak nemusíte zabývat technickými detaily testovacího prostředí a můžete se soustředit na učení a na své studenty. 
- Poskytněte studentům testovací prostředí s virtuálními počítači, které jsou nakonfigurované přesně tak, jak je pro vyučování potřeba. Můžete pro studenty omezit používání virtuálních počítačů k práci na školních projektech na určitý počet hodin.  
- Přesuňte fyzickou počítačovou učebnu ve vaší škole do cloudu. Automaticky škálujte počet virtuálních počítačů tak, aby nedošlo k překročení limitů využití a nákladů, které jste pro testovací prostředí nastavili. 
- Jakmile testovací prostředí přestanete potřebovat, můžete ho jedním kliknutím odstranit. 

## <a name="user-profiles"></a>Profily uživatelů
Tento článek popisuje různé uživatelské profily v Azure Lab Services. 

### <a name="lab-account-owner"></a>Vlastník účtu testovacího prostředí
Správce cloudových prostředků organizace, který je vlastníkem předplatného Azure, obvykle plní roli vlastníka účtu testovacího prostředí a má následující úkoly:   

- Nastavit účet testovacího prostředí pro organizaci
- Spravovat a konfigurovat zásady pro všechna testovací prostředí
- Udělovat uživatelům v organizaci oprávnění vytvářet v příslušném účtu testovací prostředí

### <a name="educator"></a>Pedagog
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
