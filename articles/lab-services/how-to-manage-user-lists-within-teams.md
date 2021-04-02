---
title: Správa seznamů Azure Lab Services uživatelů z týmů
description: Naučte se spravovat Azure Lab Services uživatelských seznamů z týmů.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: fa8f96a84be5c4c91e0153216e15963ec0e3d6f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91946647"
---
# <a name="manage-lab-services-user-lists-from-teams"></a>Správa seznamů uživatelů testovacích služeb z týmů

Při vytváření testovacího prostředí v rámci týmů (viz [Začínáme a vytvoření testovacího prostředí testovacích služeb z týmů](how-to-get-started-create-lab-within-teams.md)) se seznam uživatelů testovacího prostředí automaticky vyplní a synchronizuje s členstvím v týmu. Všichni členové týmu, včetně vlastníků, členů a hostů, budou automaticky přidáni do seznamu uživatelů testovacího prostředí. Služba Azure Lab Services zajišťuje synchronizaci se členstvím v týmu a Automatická synchronizace se spouští každých 24 hodin. 

## <a name="sync-users"></a>Synchronizovat uživatele

Pedagogy můžou pomocí tlačítka **synchronizovat** aktivovat ruční synchronizaci, jakmile se členství týmu aktualizuje. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-users-with-teams/sync-users.png" alt-text="Synchronizovat uživatele":::

Po dokončení automatické nebo ruční synchronizace platí následující podmínky v závislosti na tom, jestli je testovací prostředí publikované.

* Pokud testovací prostředí nebylo alespoň jednou Publikováno:
    * Uživatelé budou přidáni nebo odstraněni v seznamu uživatelů testovacího prostředí, a to podle změn členství v týmu. 
* Pokud se testovací prostředí publikovalo aspoň jednou, kromě přidávání nebo odstraňování uživatelů se kapacita testovacího prostředí automaticky aktualizuje.
    * Pokud tým obsahuje nějaké nové doplňky, vytvoří se nové virtuální počítače.
    * Pokud někdo z týmu odstraní nějakého uživatele, odstraní se i příslušný virtuální počítač.

## <a name="next-steps"></a>Další kroky

Jakmile je virtuální počítač šablony nakonfigurovaný a když Educator vybere, aby se šablona publikovala, vytvoří se počet virtuálních počítačů, které odpovídají počtu uživatelů v seznamu uživatelů testovacího prostředí. Po publikování testovacího prostředí a vytvoření virtuálních počítačů se uživatelé automaticky zaregistrují do testovacího prostředí a virtuální počítače se jim při prvním přihlášení přiřadí k Azure Lab Services, když poprvé přistupují k kartě, která **Azure Lab Services** aplikaci. 

Pokud chcete publikovat virtuální počítač šablony, přejít do okna Team Lab Services, vyberte kartu **Šablona** -> **...**  ->  **Publikovat**.

Pokud chcete spravovat fondy virtuálních počítačů, přečtěte si téma [Správa fondu virtuálních počítačů ve službě Lab Services z týmů](how-to-manage-vm-pool-within-teams.md).

### <a name="also-review"></a>Také zkontrolovat

Viz následující články:

- [Přehled použití Azure Lab Services v rámci týmů](lab-services-within-teams-overview.md)
- [Začínáme a vytvoření testovacího prostředí testovacích služeb z týmů](how-to-get-started-create-lab-within-teams.md)
- [Vytváření plánů testovacích služeb z týmů](how-to-create-schedules-within-teams.md)
- [Přístup k virtuálnímu počítači (zobrazení student) ve službě Lab Services z týmů](how-to-access-vm-for-students-within-teams.md)

