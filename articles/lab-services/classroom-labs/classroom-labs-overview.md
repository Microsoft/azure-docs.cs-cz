---
title: O laboratořích ve třídách ve službě Azure Lab Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak rychle nastavit testovací prostředí učebny v cloudu – nakonfigurujte testovací prostředí se šablonovým virtuálním počítačem se softwarem požadovaným pro třídu a zpřístupníte kopii virtuálního počítače každému studentovi ve třídě.
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
ms.date: 11/26/2019
ms.author: spelluru
ms.openlocfilehash: bbbea0c943758c2682f7b166e461807d2914f790
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74561679"
---
# <a name="introduction-to-classroom-labs"></a>Úvod k testovacímu prostředí v učebnách
Služba Azure Lab Services vám umožňuje rychle vytvořit cloudové testovací prostředí v učebně. Vyučující vytvoří testovací prostředí v učebně, zřídí virtuální počítače s Windows nebo Linuxem, nainstaluje nezbytný software a nástroje a zpřístupní je studentům. Studenti se ve třídě připojí k virtuálním počítačům v testovacím prostředí a budou je používat na projekty, úkoly a aktivity ve vyučování. 

Testovací prostředí učebny jsou spravované typy testovacích prostředí, které spravuje Azure. Samotná služba zpracovává veškerou správu infrastruktury pro typ spravovaného testovacího prostředí, od střídání virtuálních počítačů (VM) až po zpracování chyb a škálování infrastruktury. Musíte zadat, jaký typ infrastruktury potřebujete, a nainstalovat veškeré nástroje nebo software potřebný k výuce. 

## <a name="automatic-management-of-azure-infrastructure-and-scale"></a>Automatická správa infrastruktury Azure a škálování 
Azure Lab Services je spravovaná služba, což znamená, že zřizování a správu základní infrastruktury testovacího prostředí je automaticky zpracována službou. Můžete se soustředit pouze na přípravu správného laboratorního prostředí pro vaše uživatele. Nechte službu zpracovat zbytek a zaveďte virtuální počítače vaší laboratoře k publiku. Jediným kliknutím škálujte testovací prostředí na stovky virtuálních počítačů.

## <a name="simple-experience-for-your-lab-users"></a>Jednoduché prostředí pro uživatele testovacího prostředí 
Uživatelé, kteří jsou pozváni do testovacího prostředí, získají okamžitý přístup k prostředkům, které jim poskytnete v testovacích prostředích. Stačí se přihlásit, aby viděli úplný seznam virtuálních počítačů, ke kterým mají přístup napříč několika testovacími prostředími. Mohou kliknout na jediné tlačítko pro připojení k virtuálním počítačům a začít pracovat. Uživatelé nepotřebují předplatná Azure k používání služby. 

## <a name="cost-optimization-and-tracking"></a>Optimalizace nákladů a sledování  
Udržujte svůj rozpočet pod kontrolou tím, že přesně řídí, kolik hodin mohou uživatelé testovacího prostředí používat virtuální počítače. Nastavte plány v testovacím prostředí, aby uživatelé mohli používat virtuální počítače pouze během určených časových úseků nebo nastavit opakované automatické vypnutí a časy spuštění. Sledujte využití jednotlivých uživatelů a nastavte limity.

## <a name="example-class-types"></a>Příklady typů tříd
Pomocí služby Azure Lab Services můžete nastavit testovací prostředí pro několik typů tříd. Najdete [v článku Příklad typy tříd na Azure Lab Services](class-types.md) článku pro několik příkladů typů tříd, pro které můžete nastavit testovací prostředí pomocí Azure Lab Services. 

## <a name="next-steps"></a>Další kroky
Začněte vytvořením účtu testovacího prostředí, bez kterého není možné pomocí služby Azure Lab Services vytvořit testovací prostředí v učebnách:

- [Nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md)
