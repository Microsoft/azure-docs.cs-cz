---
title: Nastavit časový limit nečinnosti na úrovni adresáře pro uživatele Azure Portal | Microsoft Docs
description: Správci můžou vyhovět maximální době nečinnosti před odhlášením relace. Zásady časového limitu nečinnosti se nastavují na úrovni adresáře.
services: azure-portal
keywords: nastavení, časový limit
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: how-to
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 2efee29ba709607e1e7935215ca7689a39966de1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84764158"
---
# <a name="set-directory-level-inactivity-timeout"></a>Nastavit časový limit pro nečinnost na úrovni adresáře

Nastavení časového limitu nečinnosti pomáhá chránit vaše prostředky před neoprávněným přístupem, pokud uživatelé zapomene k zabezpečení své pracovní stanice. Když je uživatel v době nečinný, jejich Azure Portalá relace se automaticky odhlásí. Správci v [roli globálního správce](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) můžou vymáhat maximální dobu nečinnosti před odhlášením relace. Nastavení časového limitu nečinnosti se vztahuje na úrovni adresáře. Další informace o adresářích najdete v tématu [přehled Active Directory Domain Services](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

## <a name="configure-the-inactive-timeout-setting"></a>Konfigurace nastavení neaktivního časového limitu

Pokud jste globální správce a chcete vyhodnotit nastavení časového limitu nečinnosti pro všechny uživatele Azure Portal, postupujte podle následujících kroků:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
2. V záhlaví globální stránky vyberte **Nastavení** .
3. Vyberte text odkazu **konfigurace časového limitu na úrovni adresáře**.

    ![Snímek obrazovky s nastavením portálu s zvýrazněným textem odkazu](./media/admin-timeout/settings.png)

4. Otevře se nová stránka. Na stránce **Konfigurovat časový limit nečinnosti na úrovni adresáře** vyberte **Povolit časový limit nečinnosti na úrovni adresáře, Azure Portal** zapnout nastavení.
5. V dalším kroku zadejte **hodiny** a **minuty** pro maximální dobu, po kterou může být uživatel nečinný, než se automaticky odhlásí jeho relace.
6. Vyberte **Použít**.

    ![Snímek obrazovky zobrazující stránku pro nastavení časového limitu nečinnosti na úrovni adresáře](./media/admin-timeout/configure.png)

Pokud chcete potvrdit, že se správně nastavily zásady časového limitu nečinnosti, vyberte **oznámení** z hlavičky globální stránky. Ověřte, že je v seznamu uvedeno oznámení o úspěšném dokončení.

  ![Snímek obrazovky zobrazující zprávu o úspěšném oznámení pro časový limit nečinnosti na úrovni adresáře](./media/admin-timeout/confirmation.png)

Nastavení se projeví pro nové relace. Nebude okamžitě platit pro všechny uživatele, kteří jsou už přihlášení.

> [!NOTE]
> Pokud globální správce nakonfiguroval nastavení časového limitu na úrovni adresáře, uživatelé můžou zásadu přepsat a nastavit vlastní dobu neaktivního odhlašování. Uživatel však musí zvolit časový interval, který je menší, než je nastaveno na úrovni adresáře globálním správcem.
>

## <a name="next-steps"></a>Další kroky

* [Nastavení předvoleb pro Azure Portal](set-preferences.md)
* [Export nebo odstranění uživatelských nastavení](azure-portal-export-delete-settings.md)
* [Zapnutí vysokého kontrastu nebo změna motivu](azure-portal-change-theme-high-contrast.md)
