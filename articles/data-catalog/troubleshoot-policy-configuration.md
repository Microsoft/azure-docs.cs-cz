---
title: Řešení potíží s Azure Data Catalog
description: Tento článek popisuje běžné řešení potíží se pro prostředky Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 06/13/2019
ms.openlocfilehash: ed74e90e5e8ed55b75968f51cb50e6a1b4cdd75d
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203508"
---
# <a name="troubleshooting-azure-data-catalog"></a>Řešení potíží s Azure Data Catalog

Tento článek popisuje běžné řešení potíží se pro prostředky Azure Data Catalog. 

## <a name="functionality-limitations"></a>Omezení funkcí

Při použití Azure Data Catalog, je omezený následující funkce:

- Účty s typem **Role hosta** nejsou podporovány. Účet hosta. nelze přidat jako uživatele služby Azure Data Catalog a uživatele typu Host nemůže používat portál na www.azuredatacatalog.com.

- Vytváření prostředků Azure Data Catalog pomocí šablon Azure Resource Manageru nebo Azure Powershellu příkazů se nepodporuje.

- Azure Data Catalog prostředek nejde přesunout mezi Tenanty Azure.

## <a name="azure-active-directory-policy-configuration"></a>Konfigurace zásad Azure Active Directory

Může nastat situace, že se budete moci přihlásit k portálu Azure Data Catalog, ale při pokusu o přihlášení k nástroji pro registraci zdroje dat narazíte na chybovou zprávu, která vám přihlášení neumožní. K této chybě může dojít, když jste v podnikové síti nebo když se připojujete z místa mimo podnikovou síť.

Nástroj pro registraci používá *ověřování pomocí formulářů*, aby ověřil přihlášení uživatelů vůči službě Azure Active Directory. K úspěšnému přihlášení je nutné, aby správce Azure Active Directory v *zásadách globálního ověřování* povolil možnost ověřování pomocí formulářů.

Pomocí zásad globálního ověřování můžete povolovat ověřování zvlášť pro připojení z intranetu a extranetu, jak ukazuje následující obrázek. Pokud není povoleno ověřování pomocí formulářů pro síť, ze kterého se připojujete, může dojít k chybám přihlášení.

 ![Zásady globálního ověřování Azure Active Directory](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Další informace naleznete v článku o [konfiguraci zásad ověřování](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Další postup

* [Vytvoření Azure Data Catalog](data-catalog-get-started.md)
