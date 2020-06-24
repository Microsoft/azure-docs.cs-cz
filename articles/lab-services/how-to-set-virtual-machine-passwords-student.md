---
title: Resetování hesel pro virtuální počítače v prostředí učebny v Azure Lab Services | Microsoft Docs
description: Naučte se resetovat hesla pro virtuální počítače v učebn Labs Azure Lab Services.
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
ms.openlocfilehash: f5b783b6c6afd634b7539326678d1caf91d3ad0c
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84895833"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>Nastavení nebo resetování hesla pro virtuální počítače v učebně Labs (studenti)
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
