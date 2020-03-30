---
title: Řešení potíží s datovým katalogem Azure
description: Tento článek popisuje běžné problémy s řešením potíží pro prostředky katalogu dat Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 84bd14f8ae18527b4f6e9d8509a12555baec8771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68879543"
---
# <a name="troubleshooting-azure-data-catalog"></a>Řešení potíží se službou Azure Data Catalog

Tento článek popisuje běžné problémy s řešením potíží pro prostředky katalogu dat Azure. 

## <a name="functionality-limitations"></a>Omezení funkčnosti

Při použití Azure Data Catalog je omezená následující funkce:

- Účty s **typem Role hosta** nejsou podporovány. Účty hosta nelze přidat jako uživatele katalogu dat Azure [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com)a uživatelé typu Host nemohou používat portál na adrese .

- Vytváření prostředků datového katalogu Azure pomocí šablon Azure Resource Manager nebo příkazů Azure PowerShellu není podporované.

- Prostředek Katalogu dat Azure nelze přesunout mezi tenanty Azure.

## <a name="azure-active-directory-policy-configuration"></a>Konfigurace zásad Azure Active Directory

Může nastat situace, že se budete moci přihlásit k portálu Azure Data Catalog, ale při pokusu o přihlášení k nástroji pro registraci zdroje dat narazíte na chybovou zprávu, která vám přihlášení neumožní. K této chybě může dojít, pokud jste v podnikové síti nebo při připojování mimo síť společnosti.

Nástroj pro registraci používá *ověřování pomocí formulářů*, aby ověřil přihlášení uživatelů vůči službě Azure Active Directory. K úspěšnému přihlášení je nutné, aby správce Azure Active Directory v *zásadách globálního ověřování* povolil možnost ověřování pomocí formulářů.

Pomocí zásad globálního ověřování můžete povolovat ověřování zvlášť pro připojení z intranetu a extranetu, jak ukazuje následující obrázek. Chyby přihlášení může dojít, pokud ověřování pomocí formulářů není povoleno pro síť, ze které se připojujete.

 ![Zásady globálního ověřování Azure Active Directory](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Další informace naleznete v článku o [konfiguraci zásad ověřování](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Další kroky

* [Vytvoření Azure Data Catalogu](data-catalog-get-started.md)
