---
title: Pravidla brány firewall na úrovni serveru
description: Pravidla brány firewall na úrovni serveru
keywords: připojení SQL, připojovací řetězec
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 8d0f9899dbb7599340b8d15ca010a0157011fb9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "67175086"
---
1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

2. V seznamu na levé straně vyberte **všechny služby**.

3. Posuňte se a vyberte **SQL servery**.

    ![Vyhledání serveru Azure SQL Database na portálu][b21-FindServerInPortal]
5. Do textového pole Filtr začněte psát název vašeho serveru. Zobrazí se váš řádek.

6. Vyberte řádek pro server. Zobrazí se okno pro váš server.

7. V okně serveru vyberte **Nastavení**.

8. Vyberte **firewall**.

    ![Vyberte nastavení > bránu firewall.][b31-SettingsFirewallNavig]
9. Vyberte **Přidat IP adresu klienta**. Do prvního textového pole zadejte název nového pravidla.

10. Zadejte hodnoty nejnižší a vysoká IP adresa pro rozsah, který chcete povolit.

    * Může být užitečné, aby konec hodnoty končil hodnotou **0** a končila nejvyšší hodnotou **. 255**.

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
