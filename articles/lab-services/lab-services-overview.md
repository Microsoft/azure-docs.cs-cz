---
title: O službě Azure Lab Services | Microsoft Docs
description: Zjistěte, jak lze pomocí služby Lab Services zjednodušit vytváření, správu a zabezpečení testovacího prostředí s virtuálními počítači, které můžou využívat vývojáři, testeři, pedagogové, studenti a další uživatelé.
ms.topic: overview
ms.date: 09/16/2020
ms.openlocfilehash: ad17ebb3a803a15d1ac9ef8cb71cf8ca7976243b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91333932"
---
# <a name="an-introduction-to-azure-lab-services"></a>Seznámení s Azure Lab Services
**Azure Lab Services** vám umožní vytvářet Labs, jejichž infrastruktura je spravovaná Azure. V současné době je prostředí učebny jediným typem spravovaného testovacího prostředí, které podporuje Azure Lab Services. Služba sám zpracovává veškerou správu infrastruktury pro typ spravovaného testovacího prostředí, od odstřeďování virtuálních počítačů po zpracování chyb a škálování infrastruktury. Jakmile správce IT vytvoří v Azure Lab Services účet testovacího prostředí, může instruktor rychle vytvořit testovací prostředí pro třídu, určit počet a typ virtuálních počítačů potřebných pro cvičení ve třídě a přidat uživatele do třídy. Jakmile se uživatel zaregistruje do třídy, uživatel může získat přístup k virtuálnímu počítači, aby mohl provádět cvičení pro třídu.  

## <a name="key-capabilities"></a>Klíčové funkce
Služba Azure Lab Services podporuje následující funkce:

- **Rychlé a flexibilní nastavení testovacích prostředí**. V Azure Lab Services si můžou vlastníci testovacích prostředí rychle nastavit testovací prostředí podle svých potřeb. Služba nabízí možnost pořídit veškerou práci v infrastruktuře Azure pro spravované typy testovacích prostředí. Služba má integrované možnosti škálování a nastavení odolnosti infrastruktury pro testovací prostředí, která pro vás spravuje.
- **Zjednodušené ovládání pro uživatele testovacích prostředí**. Uživatelé testovacího prostředí se můžou zaregistrovat do testovacího prostředí s registračním kódem a kdykoli můžou k testovacímu prostředí přistupovat, aby mohli používat prostředky testovacího prostředí. 
- **Optimalizace nákladů a analýza**. Vlastník testovacího prostředí může nastavit plány automatického vypínání a spouštění virtuálních počítačů. Vlastník testovacího prostředí může nastavit časový interval, po kterém jsou virtuální počítače testovacího prostředí přístupné pro uživatele, a nastavit zásady použití na uživatele nebo na testovací prostředí, aby se optimalizoval náklady. 

Pokud chcete jenom zadat, co potřebujete v testovacím prostředí, a nechat službu nastavenou a spravovat infrastrukturu potřebnou pro testovací prostředí, vyberte jeden ze **spravovaných typů testovacího prostředí**. V současné době je **prostředí učebny** jediným typem spravovaného testovacího prostředí, které můžete vytvořit pomocí Azure Lab Services.

Následující části poskytují další podrobnosti o těchto testovacích prostředích. 

## <a name="managed-lab-types"></a>Typy spravovaného testovacího prostředí
Azure Lab Services umožňuje vytvářet testovací prostředí, jejichž infrastrukturu spravuje Azure. Tento článek se na ně odkazuje jako na typy spravovaných testovacích prostředí. Spravované typy testovacího prostředí nabízejí různé typy cvičení, které odpovídají vašim konkrétním potřebám. V současné době se jako jediný podporovaný typ spravovaného testovacího prostředí používá **učebna**. 

Spravované typy testovacího prostředí vám umožňují hned začít s minimálním nastavením. Tato služba zpracovává veškerou správu infrastruktury pro testovací prostředí, od toho, aby vypnula virtuální počítače za účelem zpracování chyb a škálování infrastruktury.Chcete-li vytvořit spravovaný typ testovacího prostředí, jako je například učebna, musíte nejprve vytvořit účet testovacího prostředí pro vaši organizaci. Účet testovacího prostředí slouží jako centrální účet, ve kterém se spravují všechna testovací prostředí v organizaci. 

Při vytváření a používání prostředků Azure v těchto spravovaných typech testovacích prostředí služba vytvoří a spravuje prostředky v interních předplatných Microsoftu. Nevytvářejí se ve vašem vlastním předplatném Azure. Služba uchovává informace o využití těchto prostředků v interních předplatných Microsoftu. Toto využití se fakturuje zpět na vaše předplatné Azure, které obsahuje účet testovacího prostředí.   

Zde jsou některé **případy použití pro spravované typy testovacích prostředí**: 

- Poskytněte studentům testovací prostředí s virtuálními počítači, které jsou nakonfigurované přesně tak, jak je pro vyučování potřeba. Můžete pro studenty omezit používání virtuálních počítačů pro domácí úkoly nebo osobní projekty na určitý počet hodin.
- Nastavte fond virtuálních počítačů s vysokým výpočetním výkonem tak, aby bylo možné provádět výzkum náročný na výpočetní výkon nebo na výkon grafiky. Spouštějte virtuální počítače podle potřeby a po dokončení tyto počítače vyčistěte. 
- Přesuňte fyzickou počítačovou učebnu ve vaší škole do cloudu. Automaticky škálujte počet virtuálních počítačů tak, aby nedošlo k překročení limitů využití a nákladů, které jste pro testovací prostředí nastavili.  
- Vytvořte rychle testovací prostředí s virtuálními počítači pro hostování hackathonu. Jakmile testovací prostředí přestanete potřebovat, můžete ho jedním kliknutím odstranit. 

## <a name="next-steps"></a>Další kroky
V následujících kurzech najdete podrobné pokyny k vytvoření účtu testovacího prostředí a vytvoření prostředí pro učebnu.

- [Kurz: nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md)
- [Kurz: Vytvoření testovacího prostředí pro učebnu](tutorial-setup-classroom-lab.md)
