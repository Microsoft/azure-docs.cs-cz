---
title: Nastavení časového lhůty nečinnosti na úrovni adresáře pro uživatele portálu Azure | Dokumenty společnosti Microsoft
description: Správci mohou vynutit maximální dobu nečinnosti před odhlášeným odhlášeným odjednáním relace. Zásady časového času nečinnosti jsou nastaveny na úrovni adresáře.
services: azure-portal
keywords: nastavení, časový rozsah
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: e27135d09da7060f2a948e37f6026fe66fbef5b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096609"
---
# <a name="set-directory-level-inactivity-timeout"></a>Nastavení časového času nečinnosti na úrovni adresáře

Nastavení časového omezení nečinnosti pomáhá chránit vaše prostředky před neoprávněným přístupem, pokud uživatelé zapomenou zabezpečit svou pracovní stanici. Když uživatel byl nečinný na chvíli, jejich relace portálu Azure se automaticky odhlásila. Správci v [roli globálního správce](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) mohou vynutit maximální dobu nečinnosti před odhlášeným odhlášeným odjednáním relace. Nastavení časového času nečinnosti platí na úrovni adresáře. Další informace o adresářích naleznete v tématu [Přehled služby Active Directory Domain Services](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

## <a name="configure-the-inactive-timeout-setting"></a>Konfigurace nastavení neaktivního časového času

Pokud jste globální správce a chcete vynutit nastavení časového limitu nečinnosti pro všechny uživatele portálu Azure, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V záhlaví globální stránky vyberte **Nastavení.**
3. Vyberte text odkazu **Konfigurovat časový čas na úrovni adresáře**.

    ![Snímek obrazovky se zvýrazněným nastavením portálu se zvýrazněným textem odkazu](./media/admin-timeout/settings.png)

4. Otevře se nová stránka. Na stránce **Configure directory level inactivity time** out vyberte Enable directory level idle **timeout for the Azure Portal** to turn on the setting.
5. Dále zadejte **hodiny** a **minuty** pro maximální dobu, po kterou může být uživatel nečinný, než je jeho relace automaticky odhlášena.
6. Vyberte **Použít**.

    ![Snímek obrazovky zobrazující stránku pro nastavení časového času nečinnosti na úrovni adresáře](./media/admin-timeout/configure.png)

Chcete-li ověřit, zda je zásada časového času nečinnosti správně nastavena, vyberte v záhlaví globální stránky **možnost Oznámení.** Ověřte, zda je uvedeno oznámení o úspěchu.

  ![Snímek obrazovky zobrazující úspěšný oznámení o časovém unutí nečinnosti na úrovni adresáře](./media/admin-timeout/confirmation.png)

Nastavení se projeví pro nové relace. Nebude se vztahovat okamžitě na žádné uživatele, kteří jsou již přihlášeni.

> [!NOTE]
> Pokud globální správce nakonfiguroval nastavení časového času na úrovni adresáře, mohou uživatelé přepsat zásadu a nastavit vlastní dobu trvání neaktivního odhlášení. Uživatel však musí zvolit časový interval, který je menší než to, co je nastaveno na úrovni adresáře globálním správcem.
>

## <a name="next-steps"></a>Další kroky

* [Nastavení předvoleb pro Azure Portal](set-preferences.md)
* [Export nebo odstranění uživatelských nastavení](azure-portal-export-delete-settings.md)
* [Zapnutí vysokého kontrastu nebo změna motivu](azure-portal-change-theme-high-contrast.md)
