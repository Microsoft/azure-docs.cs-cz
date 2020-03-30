---
title: Pravidla brány firewall na úrovni serveru
description: Pravidla brány firewall na úrovni serveru
keywords: připojení SQL,připojovací řetězec
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 8d0f9899dbb7599340b8d15ca010a0157011fb9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175086"
---
1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

2. V seznamu vlevo vyberte **Všechny služby**.

3. Posuňte a vyberte **servery SQL**.

    ![Vyhledání serveru Azure SQL Database na portálu][b21-FindServerInPortal]
5. Do textového pole filtru začněte psát název serveru. Zobrazí se řádek.

6. Vyberte řádek serveru. Zobrazí se okno pro váš server.

7. V okně serveru vyberte **Nastavení**.

8. Vyberte **bránu firewall**.

    ![Vybrat nastavení > brány firewall][b31-SettingsFirewallNavig]
9. Vyberte **přidat IP klienta**. Do prvního textového pole zadejte název nového pravidla.

10. Zadejte nízké a vysoké hodnoty IP adres pro rozsah, který chcete povolit.

    * Může být užitečné mít konec nízké hodnoty s **0,0** a konec vysoké hodnoty s **0,255**.

11. Vyberte **Uložit**.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
