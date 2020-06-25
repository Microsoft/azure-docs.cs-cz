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
ms.openlocfilehash: ed71e53a8cedc2907ac06dd75f11f9c762a78772
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2020
ms.locfileid: "85357201"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Azure AD Connect Sync: Povolit odpadkový koš služby AD
Doporučuje se povolit funkci Koš služby Active Directory pro místní aktivní adresáře, které jsou synchronizované s Azure AD. 

Pokud omylem odstraníte objekt místního uživatele služby AD a obnovíte ho pomocí této funkce, Azure AD obnoví odpovídající objekt uživatele Azure AD.  Informace o funkci Koš služby AD najdete v článku [Přehled scénářů pro obnovení odstraněných objektů služby Active Directory](https://technet.microsoft.com/library/dd379542.aspx).

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Výhody aktivace odpadkového koše služby AD
Tato funkce pomáhá při obnovování objektů uživatelů Azure AD pomocí následujícího postupu:

* Pokud jste omylem odstranili místní objekt uživatele služby AD, v dalším synchronizačním cyklu se odstraní odpovídající objekt uživatele Azure AD. Ve výchozím nastavení služba Azure AD udržuje odstraněný objekt uživatele Azure AD ve stavu tichého odstranění po dobu 30 dnů.

* Pokud máte zapnutou funkci koš místní služby Active Directory, můžete obnovit odstraněný objekt místního uživatele služby AD beze změny jeho zdrojové hodnoty kotvy. Když se obnovený objekt místního uživatele služby AD synchronizuje do Azure AD, Azure AD obnoví příslušný objekt uživatele Azure AD, který se místně odstranil. Informace o atributech zdrojového ukotvení najdete v článku [Azure AD Connect: koncepty návrhu](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor).

* Pokud nemáte zapnutou funkci Koš služby Active Directory, může být potřeba vytvořit objekt uživatele AD, který nahradí odstraněný objekt. Pokud je služba Azure AD Connect Synchronization nakonfigurovaná tak, aby pro atribut zdrojového ukotvení používala systémový atribut AD (například ObjectGuid), nově vytvořený objekt uživatele služby AD nebude mít stejnou hodnotu zdrojového ukotvení jako odstraněný objekt uživatele služby AD. Když se nově vytvořený objekt uživatele služby AD synchronizuje do Azure AD, Azure AD vytvoří nový objekt uživatele Azure AD, místo aby obnovil místně odstraněný objekt uživatele Azure AD.

> [!NOTE]
> Ve výchozím nastavení služba Azure AD udržuje po dobu 30 dnů odstraněné objekty uživatelů Azure AD v tichém odstraněné stavu, než se trvale odstraní. Správci však mohou zrychlit odstranění takových objektů. Jakmile se objekty trvale odstraní, už se nedají obnovit, i když je povolená funkce Koš pro místní službu AD.

## <a name="next-steps"></a>Další kroky
**Témata s přehledem**

* [Azure AD Connect synchronizace: pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)

* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
