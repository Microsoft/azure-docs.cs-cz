---
title: Nastavení hesla pro virtuální počítače v Azure Lab Services | Dokumentace Microsoftu
description: Zjistěte, jak nastavit a resetování hesla pro virtuální počítače (VM) v testovacím prostředím v učebnách Azure Lab Services.
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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: 3701c69ff97d840f6cba175df8f02bc55ece498b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67123240"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs"></a>Nastavit nebo obnovit heslo pro virtuální počítače v testovacím prostředím v učebnách
Tento článek popisuje různé možnosti nastavení a resettings hesel pro přístup k virtuálním počítačům v testovacím prostředím v učebnách. 

## <a name="lab-owners-teachers"></a>Vlastníků testovacího prostředí (učitele)
Vlastník testovacího prostředí (učitele) můžete nastavit heslo pro virtuální počítače v testovacím prostředí na **nastavit přihlašovací údaje** stránky Průvodce vytvořením testovacího prostředí.

![Nastavení přihlašovacích údajů](../media/tutorial-setup-classroom-lab/set-credentials.png)

Nakonec vlastník testovacího prostředí můžete resetovat heslo (v případě potřeby) na **konfigurovat šablony** stránky Průvodce vytvořením testovacího prostředí. 

![Stránka Configure template (Konfigurace šablony) po dokončení konfigurace](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

Vlastník testovacího prostředí můžou také resetovat hesla, po vytvoření testovacího prostředí, na řídicím panelu. 

![Resetovat heslo nabídky na domovské stránce](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)

Zadejte nové heslo **nastavit heslo** stránku a vybrat **nastavit heslo**. 

![Resetovat heslo nabídky na domovské stránce](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="lab-users-students"></a>Uživatelé testovacího prostředí (studenti)
V době vytvoření testovacího prostředí. Vlastník testovacího prostředí ve stejnou dobu, můžete povolit nebo zakázat umožňuje studentům k nastavení hesla pro virtuální počítače. Pokud vlastník testovacího prostředí (učitele) povolí tuto možnost, uživatel testovacího prostředí (student) bude mít možnost nastavit heslo pro virtuální počítač, když student přihlášení k virtuálnímu počítači poprvé. Na **virtuálního počítače Windows**, stiskněte klávesu **CTRL + ALT + END**a vyberte **změnit heslo**. 

## <a name="next-steps"></a>Další postup
Další informace o další možnosti použití student vám (jako vlastník testovacího prostředí) můžete konfigurovat, najdete v následujícím článku: [Konfigurace využití student](how-to-configure-student-usage.md).
