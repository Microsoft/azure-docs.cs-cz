---
title: Resetování hesel pro virtuální počítače testovacího prostředí učebny ve službě Azure Lab Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak resetovat hesla pro virtuální počítače (VM) v učebních testovacích prostředích Azure Lab Services.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 7c757ef8508f9364a46116e6ddf19207f23a4b6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73583664"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>Nastavení nebo resetování hesla pro virtuální počítače v učebních laboratořích (studenti)
V tomto článku se ukazuje, jak můžou studenti nastavit nebo resetovat heslo pro své virtuální počítače. 

## <a name="enable-resetting-of-passwords"></a>Povolit resetování hesel
V době vytváření testovacího prostředí může vlastník testovacího prostředí povolit nebo zakázat **použít stejné heslo pro všechny virtuální počítače**. Pokud byla tato možnost povolena, studenti nemohou obnovit heslo. Všechny virtuální počítače v testovacím prostředí budou mít stejné heslo, které nastaví instruktor. 

Pokud je tato možnost zakázána, uživatelé budou muset nastavit heslo při prvním pokusu o připojení k virtuálnímu počítače. Studenti mohou také resetovat heslo později kdykoli, jak je znázorněno v poslední části tohoto článku. 

## <a name="reset-password-for-the-first-time"></a>První resetování hesla
Pokud byla možnost **Použít stejné heslo pro všechny virtuální počítače** zakázána, když uživatelé (studenti) vyberte tlačítko **Připojit** na dlaždici testovacího prostředí na stránce Moje **virtuální počítače,** uživateli se zobrazí následující dialogové okno pro nastavení hesla pro virtuální počítač: 

![Resetovat heslo pro studenta](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>Resetovat heslo později
Student také může nastavit heslo kliknutím na přetečení menu **(svislé tři tečky)** na lab dlaždice, a vyberte **Reset heslo**. 

![Resetovat heslo později](../media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>Další kroky
Další informace o dalších možnostech využití studentů, které může vlastník testovacího prostředí konfigurovat, naleznete v následujícím článku: [Konfigurace využití studentů](how-to-configure-student-usage.md).
