---
title: 'Synchronizace Azure AD Connect: Povolení odpadkového koše AD | Dokumentace Microsoftu'
description: Toto téma se doporučuje používat funkci Koš služby Active Directory s Azure AD Connect.
services: active-directory
keywords: Koš služby Active Directory, nechtěnému odstranění, zdrojové ukotvení
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
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56208210"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Synchronizace Azure AD Connect: Povolení odpadkového koše AD
Doporučuje se, že povolíte funkci Koš služby Active Directory pro vaše místní Active Directory, jichž, které jsou synchronizovány do Azure AD. 

Pokud omylem odstraníte místní objekt uživatele AD a obnovení pomocí funkce, Azure AD obnoví odpovídající objekt uživatele Azure AD.  Informace o funkci Koš služby Active Directory, najdete v článku [přehled scénářů pro obnovení odstranit objekty služby Active Directory](https://technet.microsoft.com/library/dd379542.aspx).

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Výhody povolení odpadkového koše AD
Tato funkce pomáhá s obnovováním objekty uživatelů Azure AD následujícím způsobem:

* Pokud omylem odstraníte místní objekt uživatele AD, odpovídající objekt uživatele Azure AD se odstraní v další cyklus synchronizace. Ve výchozím nastavení, Azure AD udržuje na odstraněný objekt uživatele Azure AD v obnovitelně odstraněný stavu po dobu 30 dnů.

* Pokud máte místní AD recyklovat povolení funkce Koš, můžete ho obnovit na odstraněný místní objekt uživatele AD beze změny jeho hodnotu zdrojové ukotvení. Když obnovené místní objekt uživatele AD se synchronizují do služby Azure AD, Azure AD bude objekt uživatele obnovení odpovídající obnovitelně odstraněný Azure AD. Informace o atribut zdrojového ukotvení, najdete v článku [Azure AD Connect: Koncepty návrhu](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor).

* Pokud nemáte místní povolení funkce Koš služby Active Directory může být potřeba k vytvoření objektu AD uživatele k nahrazení odstraněného objektu. Pokud služba Azure AD Connect synchronizaci je konfigurován pro použití systémem generovaných atribut AD (jako je ObjectGuid) pro atribut zdrojového ukotvení, nově vytvořený objekt uživatele AD nebudete mít stejnou hodnotu jako odstraněného objektu uživatele AD zdrojové ukotvení. Když je nově vytvořený objekt uživatele AD synchronizovány do Azure AD, Azure AD vytvoří nový objekt uživatele Azure AD místo obnovení obnovitelně odstraněný objekt uživatele Azure AD.

> [!NOTE]
> Ve výchozím nastavení Azure AD udržuje odstraněné objekty uživatele služby Azure AD v obnovitelně odstraněný stavu po dobu 30 dnů, než se trvale odstraní. Správci můžou urychlit však odstranění těchto objektů. Jakmile objekty se trvale odstraní, se už nebude možné obnovit, i když místní, že je povolená funkce Koš služby Active Directory.

## <a name="next-steps"></a>Další postup
**Témata s přehledem**

* [Synchronizace Azure AD Connect: Pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)

* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
