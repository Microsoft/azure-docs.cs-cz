---
title: Azure Lab Services v rámci Microsoft Teams
description: Poskytuje přehled o používání Azure Lab Services v rámci Microsoft Teams.
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: f39613427ed174dfca80d4a48be9473ab7025e79
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433919"
---
# <a name="azure-lab-services-within-microsoft-teams"></a>Azure Lab Services v rámci Microsoft Teams

Azure Lab Services je možné využít v Microsoft Teams pomocí aplikace **Azure Lab Services** Teams. Každý vlastník týmu s přístupem Owner/Přispěvatel/Creator k účtům testovacího prostředí bude moci vytvářet Labs a zřizovat virtuální počítače pro všechny členy týmu.

Tento článek popisuje výhody použití Azure Lab Services v rámci týmů a obsahuje odkazy na další články, kde najdete pokyny k vytváření a správě cvičení v rámci týmů. 

> [!NOTE]
>**Azure Lab Services** Aplikaci týmů lze přidat pouze do týmu, nelze ji přidat do jednotlivých konverzací nebo do skupinových chatů.

## <a name="benefits"></a>Výhody

Azure Lab Services integrace s Microsoft Teams pomůže učitelům nastavovat prostředí učebny a poskytovat virtuální laboratorní prostředí v rámci týmu (třídy): 

* Pedagogy můžou nastavit testovací prostředí, aby studenti mohli získat přístup k virtuálním počítačům z týmů, aniž by museli opustit týmy a museli přejít na [web Azure Lab Services](https://labs.azure.com).
* Jednotné přihlašování (SSO) z týmů na Azure Lab Services.
* Vlastníci týmu a testovacího prostředí nemusí udržovat soupisy tříd ve dvou různých systémech – v seznamu uživatelů testovacího prostředí se automaticky vyplní členstvím v týmu a synchronizace se provádí automaticky každých 24 hodin. 
* Po počátečním publikování šablony virtuálního počítače se kapacita testovacího prostředí (tj. počet virtuálních počítačů v testovacím prostředí) automaticky upraví na základě Přidání nebo odstranění uživatelů ze členství v týmu. 
* Vlastníci týmu a testovacího prostředí budou zobrazovat jenom cvičení týkající se týmu a studenti budou zobrazovat jenom ty virtuální počítače, které jsou zřízené pro konkrétní tým. 
* Uživatelé budou automaticky registrováni do testovacího prostředí a virtuální počítače budou automaticky přiřazeny po prvním přihlášení po publikování testovacího prostředí. Pedagogy nepotřebují odesílat pozvánky a studenti nemusejí být pro testovací prostředí registrovat samostatně.  

## <a name="next-steps"></a>Další kroky

Viz následující články:

- [Začněte a vytvořte testovací prostředí v rámci týmů.](how-to-get-started-create-lab-within-teams.md)
- [Správa seznamů uživatelů testovacího prostředí v rámci týmů](how-to-manage-user-lists-within-teams.md)
- [Správa fondu virtuálních počítačů v testovacím prostředí v rámci týmů](how-to-manage-vm-pool-within-teams.md)
- [Vytváření a Správa plánů testovacího prostředí v rámci týmů](how-to-create-schedules-within-teams.md)
- [Přístup k virtuálnímu počítači v rámci týmů – zobrazení studenta](how-to-access-vm-for-students-within-teams.md)
