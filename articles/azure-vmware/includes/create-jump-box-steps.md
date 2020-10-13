---
title: Vytvoření pole pro přechod řešení Azure VMware
description: Postup vytvoření pole pro přechod řešení Azure VMware.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 5d5a5d82cf6e70e7ad55bcfa10c7f85372131c87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578409"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-access-private-cloud.md -->

1. Ve skupině prostředků vyberte **+ Přidat** , vyhledejte a vyberte **Microsoft Windows 10**a pak vyberte **vytvořit**.

   :::image type="content" source="../media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Přidejte nový virtuální počítač s Windows 10 pro pole s odkazem." border="true":::

1. Zadejte požadované informace do polí a pak vyberte **zkontrolovat + vytvořit**. 

   Další informace o polích naleznete v následující tabulce.

   | Pole | Hodnota |
   | --- | --- |
   | **Předplatné** | Hodnota je předem vyplněná odběrem patřícím do skupiny prostředků. |
   | **Skupina prostředků** | Hodnota je předem vyplněná pro aktuální skupinu prostředků, kterou jste vytvořili v předchozím kurzu.  |
   | **Název virtuálního počítače** | Zadejte jedinečný název virtuálního počítače. |
   | **Oblast** | Vyberte zeměpisnou polohu virtuálního počítače. |
   | **Možnosti dostupnosti** | Ponechte vybranou výchozí hodnotu. |
   | **Image** | Vyberte image virtuálního počítače. |
   | **Velikost** | Ponechte výchozí hodnotu velikosti. |
   | **Typ ověřování**  | Vyberte **heslo**. |
   | **Uživatelské jméno** | Zadejte uživatelské jméno pro přihlášení k virtuálnímu počítači. |
   | **Heslo** | Zadejte heslo pro přihlášení k virtuálnímu počítači. |
   | **Potvrzení hesla** | Zadejte heslo pro přihlášení k virtuálnímu počítači. |
   | **Veřejné příchozí porty** | Vyberte **Žádná**. Pokud vyberete možnost žádné, můžete použít [přístup JIT](../../security-center/security-center-just-in-time.md#jit-configure) k řízení přístupu k virtuálnímu počítači pouze v případě, že k němu chcete přistupovat.  |


1. Po úspěšném ověření vyberte **vytvořit** a spusťte tak proces vytváření virtuálního počítače.

   :::image type="content" source="../media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="Přidejte nový virtuální počítač s Windows 10 pro pole s odkazem." border="true":::