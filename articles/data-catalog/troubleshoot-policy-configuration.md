---
title: Jak nakonfigurovat zásady služby Azure Active Directory pro Azure Data Catalog
description: Může nastat situace, kde se můžete přihlásit k portálu Azure Data Catalog, ale při pokusu o přihlášení k nástroji pro registraci zdroje dat, narazíte na chybovou zprávu.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/06/2019
ms.openlocfilehash: e69a7e3bd104d0fb82b248b6560d4fd082c88426
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "62116597"
---
# <a name="azure-active-directory-policy-configuration"></a>Konfigurace zásad Azure Active Directory

Může nastat situace, že se budete moci přihlásit k portálu Azure Data Catalog, ale při pokusu o přihlášení k nástroji pro registraci zdroje dat narazíte na chybovou zprávu, která vám přihlášení neumožní. K této chybě může dojít, když jste v podnikové síti nebo když se připojujete z místa mimo podnikovou síť.

## <a name="registration-tool"></a>Nástroj pro registraci

Nástroj pro registraci používá *ověřování pomocí formulářů*, aby ověřil přihlášení uživatelů vůči službě Azure Active Directory. K úspěšnému přihlášení je nutné, aby správce Azure Active Directory v *zásadách globálního ověřování* povolil možnost ověřování pomocí formulářů.

Pomocí zásad globálního ověřování můžete povolovat ověřování zvlášť pro připojení z intranetu a extranetu, jak ukazuje následující obrázek. Pokud není povoleno ověřování pomocí formulářů pro síť, ze kterého se připojujete, může dojít k chybám přihlášení.

 ![Zásady globálního ověřování Azure Active Directory](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Další informace naleznete v článku o [konfiguraci zásad ověřování](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Další postup

* [Vytvoření Azure Data Catalog](data-catalog-get-started.md)