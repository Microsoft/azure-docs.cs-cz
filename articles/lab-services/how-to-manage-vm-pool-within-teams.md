---
title: Správa fondu virtuálních počítačů ve Azure Lab Services z týmů
description: Naučte se spravovat fond virtuálních počítačů v Azure Lab Services z týmů.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: b838e0561bb48b20450e49aaef37baf3c9ecb4d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91946676"
---
# <a name="manage-a-vm-pool-in-lab-services-from-teams"></a>Správa fondu virtuálních počítačů ve službě Lab Services z týmů

Vytvoření virtuálního počítače se spustí hned po prvním publikování šablony virtuálního počítače. Vytvoří se virtuální počítače, které se rovnají počtu uživatelů v seznamu uživatelů testovacího prostředí. Virtuální počítače se automaticky přiřazují studentům při jejich prvním přihlášení do Azure Lab Services. 

## <a name="publish-a-template-and-manage-a-vm-pool"></a>Publikování šablony a Správa fondu virtuálních počítačů

Šablonu publikujete tak, že přejdete do okna Team Lab Services, vyberete kartu **šablony** – > **...**  ->  **Publikovat**.

Jakmile je virtuální počítač šablony nakonfigurovaný a když Educator vybere, aby se šablona publikovala, vytvoří se počet virtuálních počítačů, které odpovídají počtu uživatelů v seznamu uživatelů testovacího prostředí. Po publikování testovacího prostředí a vytvoření virtuálních počítačů se uživatelé automaticky zaregistrují do testovacího prostředí a virtuální počítače se jim při prvním přihlášení přiřadí k Azure Lab Services, když poprvé přistupují k kartě, která **Azure Lab Services** aplikaci. 

Když se aktivuje synchronizace seznamu uživatelů, kapacita testovacího prostředí (počet virtuálních počítačů v testovacím prostředí) se automaticky aktualizuje na základě změn členství v týmu. Vytvoří se nové virtuální počítače, když se přidají noví uživatelé a virtuální počítače přiřazené uživatelům odebraným z týmu se taky odstraní. Další informace najdete v tématu [Správa uživatelů v rámci týmů](how-to-manage-user-lists-within-teams.md). 

Pedagogské můžou dál přistupovat k virtuálním počítačům studenta přímo z karty fond virtuálních počítačů. A přípravci mají přístup k virtuálním počítačům, které jsou k sobě přiřazeny, buď z karty **fondu virtuálních počítačů** , nebo kliknutím na tlačítko **Moje Virtual Machines** (v pravém horním rohu obrazovky). 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-vm-pool-with-teams/vm-pool.png" alt-text="Fond virtuálních počítačů":::

## <a name="next-steps"></a>Další kroky

Viz následující články:

- [Přehled použití Azure Lab Services v rámci týmů](lab-services-within-teams-overview.md)
- [Začínáme a vytvoření testovacího prostředí testovacích služeb z týmů](how-to-get-started-create-lab-within-teams.md)
- [Správa seznamů uživatelů testovacích služeb z týmů](how-to-manage-user-lists-within-teams.md)
- [Vytváření plánů testovacích služeb z týmů](how-to-create-schedules-within-teams.md)
- [Přístup k virtuálnímu počítači (zobrazení student) ve službě Lab Services z týmů](how-to-access-vm-for-students-within-teams.md)


