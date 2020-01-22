---
title: Nastavit časový limit nečinnosti na úrovni adresáře pro uživatele Azure Portal | Microsoft Docs
description: Správci můžou vyhovět maximální době nečinnosti před odhlášením relace. Zásady časového limitu nečinnosti se nastavují na úrovni adresáře.
services: azure-portal
keywords: nastavení, časový limit
author: mgblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 87de67eec9171825a1ebff9ab60782fea8836039
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310794"
---
# <a name="set-directory-level-inactivity-timeout"></a>Nastavit časový limit pro nečinnost na úrovni adresáře

Nastavení časového limitu nečinnosti pomáhá chránit vaše prostředky před neoprávněným přístupem, pokud uživatelé zapomene k zabezpečení své pracovní stanice. Když je uživatel v době nečinný, jejich Azure Portalá relace se automaticky odhlásí. Správci můžou vyhovět maximální době nečinnosti před odhlášením relace. Nastavení časového limitu nečinnosti se vztahuje na úrovni adresáře. Další informace o adresářích najdete v tématu [přehled Active Directory Domain Services](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

## <a name="configure-the-inactive-timeout-setting"></a>Konfigurace nastavení neaktivního časového limitu

Pokud jste správcem a chcete vynutilit nastavení časového limitu nečinnosti pro všechny uživatele Azure Portal, postupujte takto:

1. Přihlaste se k [Portálu Azure](https://portal.azure.com).
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
> Pokud správce nakonfiguroval nastavení časového limitu na úrovni adresáře, uživatelé můžou zásady přepsat a nastavit vlastní neaktivní dobu odhlašování. Uživatel však musí zvolit časový interval, který je menší, než je nastaveno na úrovni adresáře.
>

## <a name="next-steps"></a>Další kroky

* [Nastavení předvoleb Azure Portal](set-preferences.md)
* [Export nebo odstranění uživatelských nastavení](azure-portal-export-delete-settings.md)
* [Zapnutí vysokého kontrastu nebo změna motivu](azure-portal-change-theme-high-contrast.md)
