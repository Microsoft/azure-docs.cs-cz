---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 842083f0f69552fb9076423386353dbb4dae73b5
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
1. Klikněte **Connect** tlačítko na stránce vlastností virtuálního počítače. 
2. V **připojit k virtuálnímu počítači** , zachovat, vyberte požadované možnosti a klikněte na tlačítko **soubor RDP Stáhnout**.
2. Otevřete stažený soubor RDP a klikněte na tlačítko **Connect** po zobrazení výzvy. 
2. Zobrazí se upozornění, že soubor `.rdp` je od neznámého vydavatele. To je normální. Pokračujte kliknutím na **Připojit** v okně Připojení ke vzdálené ploše.
   
    ![Snímek obrazovky upozornění na neznámého vydavatele](./media/virtual-machines-log-on-win-server/rdp-warn.png)
3. V okně **Zabezpečení systému Windows** vyberte **Další možnosti** a pak **Použít jiný účet**. Zadejte přihlašovací údaje k účtu virtuálního počítače a pak klikněte na **OK**.
   
     **Místní účet** – Většinou je to uživatelské jméno a heslo k místnímu účtu, které jste zadávali při vytváření virtuálního počítače. V tomto případě je doménou název virtuálního počítače ve formátu *název_virtuálního_počítače*&#92;*uživatelské_jméno*.  
   
    **Virtuální počítač připojený k doméně** – Pokud virtuální počítač patří do domény, zadejte uživatelské jméno ve formátu *doména*&amp;#92;*uživatelské_jméno*. Účet také musí být členem skupiny Administrators nebo musí mít udělené oprávnění ke vzdálenému přístupu k virtuálnímu počítači.
   
    **Řadič domény** – Pokud je virtuální počítač řadičem domény, zadejte uživatelské jméno a heslo účtu správce domény pro danou doménu.
4. Kliknutím na **Ano** ověřte identitu virtuálního počítače a dokončete přihlášení.
   
   ![Snímek obrazovky zobrazující zprávu o ověření identity virtuálního počítače](./media/virtual-machines-log-on-win-server/cert-warning.png)

