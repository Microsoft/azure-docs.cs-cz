---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 100e0457627a04414ad2f0780a346933b11f4376
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
1. Kliknutím na **Připojit** vytvoříte a stáhnete soubor protokolu Remote Desktop Protocol (.rdp). Kliknutím na **Otevřít** tento soubor použijete.
2. Zobrazí se upozornění, že soubor `.rdp` je od neznámého vydavatele. To je normální. Pokračujte kliknutím na **Připojit** v okně Připojení ke vzdálené ploše.
   
    ![Snímek obrazovky upozornění na neznámého vydavatele](./media/virtual-machines-log-on-win-server/rdp-warn.png)
3. V okně **Zabezpečení systému Windows** vyberte **Další možnosti** a pak **Použít jiný účet**. Zadejte přihlašovací údaje k účtu virtuálního počítače a pak klikněte na **OK**.
   
     **Místní účet** – Většinou je to uživatelské jméno a heslo k místnímu účtu, které jste zadávali při vytváření virtuálního počítače. V tomto případě je doménou název virtuálního počítače ve formátu *název_virtuálního_počítače*&#92;*uživatelské_jméno*.  
   
    **Virtuální počítač připojený k doméně** – Pokud virtuální počítač patří do domény, zadejte uživatelské jméno ve formátu *doména*&amp;#92;*uživatelské_jméno*. Účet také musí být členem skupiny Administrators nebo musí mít udělené oprávnění ke vzdálenému přístupu k virtuálnímu počítači.
   
    **Řadič domény** – Pokud je virtuální počítač řadičem domény, zadejte uživatelské jméno a heslo účtu správce domény pro danou doménu.
4. Kliknutím na **Ano** ověřte identitu virtuálního počítače a dokončete přihlášení.
   
   ![Snímek obrazovky zobrazující zprávu o ověření identity virtuálního počítače](./media/virtual-machines-log-on-win-server/cert-warning.png)

