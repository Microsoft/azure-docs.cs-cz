---
title: 'Azure AD Connect synchronizace: Povolit odpadkový koš služby Active Directory | Microsoft Docs'
description: Toto téma doporučuje použití funkce Koš služby AD s Azure AD Connect.
services: active-directory
keywords: Odpadkový koš služby Active Directory, náhodné odstranění, zdrojové ukotvení
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/17/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12073a75cd248c9226c7ce5ecc21b64617823b32
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89279631"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Azure AD Connect Sync: Povolit odpadkový koš služby AD
Doporučuje se povolit funkci Koš služby Active Directory pro místní aktivní adresáře, které jsou synchronizované s Azure AD. 

Pokud omylem odstraníte objekt místního uživatele služby AD a obnovíte ho pomocí této funkce, Azure AD obnoví odpovídající objekt uživatele Azure AD.  Informace o funkci Koš služby AD najdete v článku [Přehled scénářů pro obnovení odstraněných objektů služby Active Directory](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd379542(v=ws.10)).

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Výhody aktivace odpadkového koše služby AD
Tato funkce pomáhá při obnovování objektů uživatelů Azure AD pomocí následujícího postupu:

* Pokud jste omylem odstranili místní objekt uživatele služby AD, v dalším synchronizačním cyklu se odstraní odpovídající objekt uživatele Azure AD. Ve výchozím nastavení služba Azure AD udržuje odstraněný objekt uživatele Azure AD ve stavu tichého odstranění po dobu 30 dnů.

* Pokud máte zapnutou funkci koš místní služby Active Directory, můžete obnovit odstraněný objekt místního uživatele služby AD beze změny jeho zdrojové hodnoty kotvy. Když se obnovený objekt místního uživatele služby AD synchronizuje do Azure AD, Azure AD obnoví příslušný objekt uživatele Azure AD, který se místně odstranil. Informace o atributech zdrojového ukotvení najdete v článku [Azure AD Connect: koncepty návrhu](./plan-connect-design-concepts.md#sourceanchor).

* Pokud nemáte zapnutou funkci Koš služby Active Directory, může být potřeba vytvořit objekt uživatele AD, který nahradí odstraněný objekt. Pokud je služba Azure AD Connect Synchronization nakonfigurovaná tak, aby pro atribut zdrojového ukotvení používala systémový atribut AD (například ObjectGuid), nově vytvořený objekt uživatele služby AD nebude mít stejnou hodnotu zdrojového ukotvení jako odstraněný objekt uživatele služby AD. Když se nově vytvořený objekt uživatele služby AD synchronizuje do Azure AD, Azure AD vytvoří nový objekt uživatele Azure AD, místo aby obnovil místně odstraněný objekt uživatele Azure AD.

> [!NOTE]
> Ve výchozím nastavení služba Azure AD udržuje po dobu 30 dnů odstraněné objekty uživatelů Azure AD v tichém odstraněné stavu, než se trvale odstraní. Správci však mohou zrychlit odstranění takových objektů. Jakmile se objekty trvale odstraní, už se nedají obnovit, i když je povolená funkce Koš pro místní službu AD.

## <a name="next-steps"></a>Další kroky
**Témata s přehledem**

* [Azure AD Connect synchronizace: pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)

* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)