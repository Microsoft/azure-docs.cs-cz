---
title: 'Synchronizace Azure AD Connect: zapnutí koše AD | Microsoft Docs'
description: Toto téma doporučuje použití funkce Koš služby AD s Azure AD Connect.
services: active-directory
keywords: Koš služby AD, náhodným odstraněním, zdrojové ukotvení
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: f80d16c36714a971e3878ad7dedffaac55103d92
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593070"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Synchronizace Azure AD Connect: zapnutí koše AD
Doporučujeme, abyste povolili funkce Koš služby AD pro adresáře Active na místě, které jsou synchronizovány do Azure AD. 

Pokud jste omylem odstranili místní objekt uživatele AD a obnovení je pomocí funkce Azure AD obnoví odpovídající objekt uživatele Azure AD.  Informace o funkci Koš služby AD, najdete v článku [přehled scénářů pro obnovení odstranit objekty služby Active Directory](https://technet.microsoft.com/library/dd379542.aspx).

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Výhody zapnutí koše AD
Tato funkce vám pomůže s obnovováním objekty uživatele Azure AD následujícím způsobem:

* Pokud jste omylem odstranili místní uživatele AD objektu, se odstraní odpovídající objekt uživatele Azure AD v příštím synchronizačním cyklu. Ve výchozím nastavení, Azure AD udržuje na odstraněný objekt uživatele Azure AD ve stavu odstraněno po dobu 30 dnů.

* Pokud máte místní recyklovat povolena funkce Koš služby AD, můžete obnovit odstraněné místní objekt uživatele AD beze změny jeho hodnota zdrojové ukotvení. Když obnovené místní objekt uživatele AD synchronizována do Azure AD, Azure AD bude objekt uživatele obnovení odpovídající dočasně odstraněné Azure AD. Informace o atributu zdrojové ukotvení najdete v článku [Azure AD Connect: konceptech návrhu](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor).

* Pokud nemáte místní povolena funkce Koš služby AD, může být potřeba vytvořit objekt uživatele AD nahradit odstraněného objektu. Pokud služba Azure AD Connect synchronizace je nakonfigurovaný na použití generována atribut AD (například identifikátoru GUID) pro atribut zdrojové ukotvení, nově vytvořený objekt uživatele AD nebude mít stejnou hodnotu jako odstraněného objektu uživatele AD zdrojové ukotvení. Když je nově vytvořený objekt uživatele AD synchronizovány do Azure AD, Azure AD vytvoří nový objekt uživatele Azure AD, místo obnovení obnovitelné odstranění objektu uživatele Azure AD.

> [!NOTE]
> Ve výchozím nastavení Azure AD udržuje odstraněných objektů uživatele Azure AD ve stavu odstraněno 30 dní, než se trvale odstraní. Správci mohou však urychlit odstranění tyto objekty. Jakmile objekty jsou trvale odstraněny, se již nebude možné obnovit, i když místní, že je povolena funkce Koš služby AD.



## <a name="next-steps"></a>Další postup
**Témata s přehledem**

* [Synchronizace Azure AD Connect: pochopení a přizpůsobení synchronizace](active-directory-aadconnectsync-whatis.md)

* [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md)
