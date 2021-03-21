---
title: Řešení potíží s Azure Data Catalog
description: Tento článek popisuje běžné otázky týkající se řešení potíží s Azure Data Catalog prostředky.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 201a7d1fce00323ef3f8eec1bdd415c4064d49ab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674984"
---
# <a name="troubleshooting-azure-data-catalog"></a>Řešení potíží se službou Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Tento článek popisuje běžné otázky týkající se řešení potíží s Azure Data Catalog prostředky. 

## <a name="functionality-limitations"></a>Omezení funkčnosti

Při použití Azure Data Catalog jsou omezené následující funkce:

- Účty s **rolí typu Host** se nepodporují. Účty hostů nemůžete přidat jako uživatele Azure Data Catalog a uživatelé typu Host nemůžou portál používat na [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com) .

- Vytváření Azure Data Catalogch prostředků pomocí šablon Azure Resource Manager nebo Azure PowerShellch příkazů se nepodporuje.

- Prostředek Azure Data Catalog nejde přesunout mezi klienty Azure.

## <a name="azure-active-directory-policy-configuration"></a>Konfigurace zásad Azure Active Directory

Může nastat situace, že se budete moci přihlásit k portálu Azure Data Catalog, ale při pokusu o přihlášení k nástroji pro registraci zdroje dat narazíte na chybovou zprávu, která vám přihlášení neumožní. K této chybě může dojít, pokud jste v podnikové síti nebo když se připojujete z oblasti mimo podnikovou síť.

Nástroj pro registraci používá *ověřování pomocí formulářů*, aby ověřil přihlášení uživatelů vůči službě Azure Active Directory. K úspěšnému přihlášení je nutné, aby správce Azure Active Directory v *zásadách globálního ověřování* povolil možnost ověřování pomocí formulářů.

Pomocí zásad globálního ověřování můžete povolovat ověřování zvlášť pro připojení z intranetu a extranetu, jak ukazuje následující obrázek. Pokud není povoleno ověřování pomocí formulářů pro síť, ze které se připojujete, může dojít k chybám při přihlašování.

 ![Zásady globálního ověřování Azure Active Directory](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Další informace naleznete v článku o [konfiguraci zásad ověřování](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Další kroky

* [Vytvoření Azure Data Catalogu](data-catalog-get-started.md)
