---
title: Odstranění Azure Lab Servicesho testovacího prostředí z týmů
description: Naučte se, jak odstranit Azure Lab Services testovací prostředí z týmů.
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 1d2fe73d33a88e595f42b47c1e7122dcbdfe1063
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094332"
---
# <a name="delete-labs-within-teams"></a>Odstranění cvičení v rámci týmů

Tento článek popisuje, jak odstranit testovací prostředí z aplikace **Azure Lab Services** .

## <a name="prerequisites"></a>Předpoklady

* [Vytvořte účet služby testovacího prostředí](tutorial-setup-lab-account.md#create-a-lab-account) v Azure Portal.
* Začněte [a vytvořte testovací prostředí testovacích služeb v rámci týmů](how-to-get-started-create-lab-within-teams.md).

## <a name="delete-labs"></a>Odstranění testovacích prostředí

Testovací prostředí vytvořené v rámci týmů je možné odstranit na [webu testovacích služeb](https://labs.azure.com) , a to odstraněním testovacího prostředí přímo, jak je popsáno v tématu [Správa učebnních cvičení v Azure Lab Services](how-to-manage-classroom-labs.md). 

Odstranění testovacího prostředí se také aktivuje při odstranění týmu. Pokud se tým, ve kterém testovací prostředí vytváří, odstraní, testovací prostředí se automaticky odstraní 24 hodin po aktivaci automatického seznamu uživatelů. 

> [!IMPORTANT]
> Odstraněním karty nebo odinstalováním aplikace nebudete mít za následek odstranění testovacího prostředí. 

Pokud je karta Odstraněná, uživatelé v seznamu členství v týmu budou mít i nadále přístup k virtuálním počítačům na [webu testovacích služeb](https://labs.azure.com) , pokud se odstranění testovacího prostředí explicitně neaktivuje odstraněním testovacího prostředí na webu nebo odstraněním týmu. 

## <a name="next-steps"></a>Další kroky

- [Přehled použití Azure Lab Services v rámci týmů](lab-services-within-teams-overview.md)
- [Správa seznamů uživatelů testovacího prostředí v rámci týmů](how-to-manage-user-lists-within-teams.md)
- [Správa fondu virtuálních počítačů v testovacím prostředí v rámci týmů](how-to-manage-vm-pool-within-teams.md)
- [Vytváření a Správa plánů testovacího prostředí v rámci týmů](how-to-create-schedules-within-teams.md)
- [Přístup k virtuálnímu počítači v rámci týmů – zobrazení studenta](how-to-access-vm-for-students-within-teams.md)

