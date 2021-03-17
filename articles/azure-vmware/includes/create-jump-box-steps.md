---
title: Vytvoření pole pro přechod řešení Azure VMware
description: Postup vytvoření pole pro přechod řešení Azure VMware.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: f746e11763e1df1686f3134960dea167bf1c9908
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462239"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-access-private-cloud.md -->

1. Ve skupině prostředků vyberte **+ Přidat** , vyhledejte a vyberte **Microsoft Windows 10** a pak vyberte **vytvořit**.

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
   | **Obrázek** | Vyberte image virtuálního počítače. |
   | **Velikost** | Ponechte výchozí hodnotu velikosti. |
   | **Typ ověřování**  | Vyberte **heslo**. |
   | **Uživatelské jméno** | Zadejte uživatelské jméno pro přihlášení k virtuálnímu počítači. |
   | **Heslo** | Zadejte heslo pro přihlášení k virtuálnímu počítači. |
   | **Potvrzení hesla** | Zadejte heslo pro přihlášení k virtuálnímu počítači. |
   | **Veřejné příchozí porty** | Vyberte **Žádná**. Pokud vyberete možnost žádné, můžete použít [přístup JIT](../../security-center/security-center-just-in-time.md#jit-configure) k řízení přístupu k virtuálnímu počítači pouze v případě, že k němu chcete přistupovat. Případně můžete použít [Azure bastionu](../../bastion/tutorial-create-host-portal.md) , pokud chcete zabezpečený přístup k serveru se seznamem odkazů z Internetu bez odhalení síťového portu.  |


1. Po úspěšném ověření vyberte **vytvořit** a spusťte tak proces vytváření virtuálního počítače.

