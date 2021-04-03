---
title: Postup přizpůsobení synchronizačního pravidla v Azure AD Connect | Microsoft Docs
description: Naučte se používat editor pravidel synchronizace k úpravám nebo vytváření nového synchronizačního pravidla.
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
ms.openlocfilehash: e2bb86988454141dc692b4a9967997c4ff7574a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530484"
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