---
title: Resetování hesel pro virtuální počítače v prostředí učebny v Azure Lab Services | Microsoft Docs
description: Naučte se resetovat hesla pro virtuální počítače v cvičeních Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1b0b13862ca4620da15606138c0a80adeac8056a
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436806"
---
# <a name="set-or-reset-password-for-virtual-machines-in-labs-students"></a>Nastavení nebo resetování hesla pro virtuální počítače v prostředí Labs (studenti)
V tomto článku se dozvíte, jak můžou studenti nastavit nebo resetovat heslo pro své virtuální počítače. 

## <a name="enable-resetting-of-passwords"></a>Povolit resetování hesel
V době vytváření testovacího prostředí může vlastník testovacího prostředí povolit nebo zakázat **použití stejného hesla pro všechny virtuální počítače**. Pokud je tato možnost povolená, studenti si nemůžou resetovat heslo. Všechny virtuální počítače v testovacím prostředí budou mít stejné heslo, které je nastavené instruktorem. 

Pokud je tato možnost zakázaná, uživatelé budou muset při prvním pokusu o připojení k virtuálnímu počítači nastavit heslo. Studenti si později můžou heslo resetovat kdykoli, jak je znázorněno v poslední části tohoto článku. 

## <a name="reset-password-for-the-first-time"></a>První resetování hesla
Pokud se možnost **použít stejné heslo pro všechny virtuální počítače** zakázala, když uživatelé (studenti) na dlaždici testovací prostředí na stránce **moje virtuální počítače** vyberou tlačítko **připojit** , uživateli se zobrazí následující dialogové okno s nastavením hesla pro virtuální počítač: 

![Resetování hesla pro studenta](./media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>Resetování hesla později
Student také může nastavit heslo kliknutím na nabídku přetečení (**svisle na tři tečky**) na dlaždici testovacího prostředí a výběrem možnosti **resetovat heslo**. 

![Resetování hesla později](./media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>Další kroky
Další informace o možnostech využití studenta, které může vlastník testovacího prostředí konfigurovat, najdete v následujícím článku: [Konfigurace využití studenta](how-to-configure-student-usage.md).
