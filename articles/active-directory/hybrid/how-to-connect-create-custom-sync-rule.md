---
title: Postup přizpůsobení synchronizačního pravidla v Azure AD Connect | Microsoft Docs
description: V tomto tématu najdete postup řešení potíží při instalaci Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f33cbb8caaf81b862198d694b0562d461de8a74
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89279274"
---
# <a name="how-to-customize-a-synchronization-rule"></a>Přizpůsobení synchronizačního pravidla

## <a name="recommended-steps"></a>**Doporučené kroky**

K úpravám nebo vytváření nového pravidla synchronizace můžete použít Editor synchronizačního pravidla. Musíte být pokročilým uživatelem, aby bylo možné provádět změny pravidel synchronizace. Jakékoli špatné změny mohou způsobit odstranění objektů z cílového adresáře. Přečtěte si [Doporučené dokumenty](#recommended-documents) , které vám pomůžou získat zkušenosti s pravidly synchronizace. Pokud chcete upravit synchronizační pravidlo, Projděte následující kroky:

* Spusťte Editor synchronizace v nabídce aplikace na ploše, jak je znázorněno níže:

    ![Nabídka editoru synchronizačního pravidla](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* Chcete-li upravit výchozí pravidlo synchronizace, naklonujte stávající pravidlo kliknutím na tlačítko Upravit v editoru pravidel synchronizace, čímž se vytvoří kopie standardního výchozího pravidla a zakažte ho. Uložte naklonované pravidlo s prioritou menší než 100.  Priorita určuje, jaké pravidlo služba WINS (nižší číselná hodnota) je řešení konfliktů, pokud dojde ke konfliktu toku atributů.

    ![Editor pravidel synchronizace](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* Při úpravách konkrétního atributu byste v ideálním případě měli zachovat pouze úpravu atributu v klonovaném pravidle.  Pak povolte výchozí pravidlo, aby byl upravený atribut z klonovaného pravidla a další atributy převzaté z výchozího standardního pravidla. 

* Počítejte s tím, že v případě, že je Počítaná hodnota upraveného atributu v naklonovaném pravidle NULL a není NULL ve výchozím standardním pravidle, hodnota NOT NULL se načte a nahradí hodnotu NULL. Pokud nechcete, aby se hodnota NULL nahradila hodnotou, která není NULL, přiřaďte AuthoritativeNull do svého klonovaného pravidla.

* Chcete-li upravit **odchozí** pravidlo, změňte filtr z editoru synchronizačního pravidla.

## <a name="recommended-documents"></a>**Doporučené dokumenty**
* [Azure AD Connect synchronizace: technické koncepty](./how-to-connect-sync-technical-concepts.md)
* [Azure AD Connect synchronizace: Principy architektury](./concept-azure-ad-connect-sync-architecture.md)
* [Azure AD Connect synchronizace: principy deklarativního zřizování](./concept-azure-ad-connect-sync-declarative-provisioning.md)
* [Azure AD Connect synchronizace: principy deklarativních zřizovacích výrazů](./concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Synchronizace služby Azure AD Connect: Principy výchozí konfigurace](./concept-azure-ad-connect-sync-default-configuration.md)
* [Azure AD Connect synchronizace: principy uživatelů, skupin a kontaktů](./concept-azure-ad-connect-sync-user-and-contacts.md)
* [Azure AD Connect Sync: atributy stínů](./how-to-connect-syncservice-shadow-attributes.md)

## <a name="next-steps"></a>Další kroky
- [Azure AD Connect synchronizace](how-to-connect-sync-whatis.md).
- [Co je hybridní identita?](whatis-hybrid-identity.md).