---
title: 'Synchronizace služby Azure AD Connect: Povolení koše služby AD | Dokumenty společnosti Microsoft'
description: Toto téma doporučuje použití funkce koše služby AD s Azure AD Connect.
services: active-directory
keywords: Koš ve shodě, náhodné odstranění, zdrojová kotva
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/17/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fe7d3ea7d4f6d648438efc1a484d5909ade2f23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60382891"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Synchronizace služby Azure AD Connect: Povolení koše služby AD
Doporučujeme povolit funkci koše služby AD pro místní aktivní adresáře, které jsou synchronizovány se službou Azure AD. 

Pokud jste omylem odstranili objekt místního uživatele služby AD a obnovili ho pomocí této funkce, Azure AD obnoví odpovídající objekt uživatele Azure AD.  Informace o funkci Koš služby AD naleznete v článku [Přehled scénářů pro obnovení odstraněných objektů služby Active Directory](https://technet.microsoft.com/library/dd379542.aspx).

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Výhody povolení koše ad
Tato funkce pomáhá s obnovením objektů uživatelů Azure AD tím, že provádí tenásledující:

* Pokud jste omylem odstranili objekt místního uživatele služby AD, odpovídající objekt uživatele Azure AD se odstraní v dalším cyklu synchronizace. Ve výchozím nastavení Azure AD udržuje odstraněný objekt uživatele Azure AD ve stavu obnovitelnéodstranění po dobu 30 dnů.

* Pokud máte povolenou místní funkci koše služby AD, můžete odstraněný objekt místního uživatele služby AD obnovit bez evidenční hodnoty ukotvení zdroje. Když se obnovený objekt místního uživatele Služby AD synchronizuje se službou Azure AD, Azure AD obnoví odpovídající objekt uživatele Azure AD s obnovitelným odstraněním. Informace o atributu Source Anchor naleznete v článku [Azure AD Connect: Design concepts](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor).

* Pokud nemáte povolenou funkci koše služby AD, může být nutné vytvořit objekt uživatele služby AD, který odstraněný objekt nahradí. Pokud je služba synchronizace připojení Azure AD nakonfigurována tak, aby pro atribut Source Anchor používala atribut AD generovaný systémem (například ObjectGuid), nově vytvořený objekt uživatele služby AD nebude mít stejnou hodnotu ukotvení zdroje jako odstraněný objekt uživatele služby AD. Když je nově vytvořený objekt uživatele služby AD synchronizován s Azure AD, Azure AD vytvoří nový objekt uživatele Azure AD namísto obnovení objektu uživatele Azure AD s obnovitelným odstraněním.

> [!NOTE]
> Ve výchozím nastavení Azure AD udržuje odstraněné objekty uživatelů Azure AD ve stavu obnovitelné odstranění po dobu 30 dnů před jejich trvalým odstraněním. Správci však mohou urychlit odstranění těchto objektů. Jakmile jsou objekty trvale odstraněny, již je nelze obnovit, a to ani v případě, že je povolena místní funkce koše služby AD.

## <a name="next-steps"></a>Další kroky
**Přehledná témata**

* [Synchronizace služby Azure AD Connect: Principy a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)

* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
