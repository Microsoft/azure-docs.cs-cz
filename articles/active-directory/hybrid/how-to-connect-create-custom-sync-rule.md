---
title: Jak přizpůsobit pravidlo synchronizace ve službě Azure AD Connect | Dokumenty společnosti Microsoft
description: Toto téma obsahuje postup řešení problémů s instalací služby Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a65d4c477d0e3aa9d5feea53e3e667ece651c83f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60351038"
---
# <a name="how-to-customize-a-synchronization-rule"></a>Přizpůsobení synchronizačního pravidla

## <a name="recommended-steps"></a>**Doporučené kroky**

Pomocí editoru pravidel synchronizace můžete upravit nebo vytvořit nové pravidlo synchronizace. Chcete-li provést změny pravidel synchronizace, musíte být pokročilým uživatelem. Jakékoli nesprávné změny mohou mít za následek odstranění objektů z cílového adresáře. Přečtěte si [prosím doporučené dokumenty](#recommended-documents) a získejte odborné znalosti v pravidlech synchronizace. Chcete-li upravit pravidlo synchronizace, postupujte takto:

* Spusťte editor synchronizace z nabídky aplikace na ploše, jak je znázorněno níže:

    ![Nabídka Editoru pravidel synchronizace](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* Chcete-li přizpůsobit výchozí pravidlo synchronizace, naklonujte existující pravidlo klepnutím na tlačítko Upravit v Editoru pravidel synchronizace, které vytvoří kopii standardního výchozího pravidla a zakáže ho. Uložte klonované pravidlo s prioritou menší než 100.  Priorita určuje, jaké pravidlo wins (nižší číselná hodnota) řešení konfliktů, pokud je konflikt toku atributu.

    ![Editor pravidel synchronizace](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* Při úpravě určitého atributu byste v ideálním případě měli zachovat pouze modifikující atribut v klonovaném pravidle.  Potom povolte výchozí pravidlo tak, aby upravený atribut pochází z klonovaného pravidla a další atributy jsou vybrány z výchozího standardního pravidla. 

* Vezměte prosím na vědomí, že v případě, kdy vypočtená hodnota změněného atributu je null v klonované pravidlo a není NULL ve výchozím standardním pravidle pak, ne NULL hodnota vyhraje a nahradí hodnotu NULL. Pokud nechcete, aby hodnota NULL byla nahrazena hodnotou not NULL, přiřaďte autoritativní hodnotu Null v klonovaném pravidle.

* Chcete-li upravit **odchozí** pravidlo, změňte filtr z editoru pravidel synchronizace.

## <a name="recommended-documents"></a>**Doporučené dokumenty**
* [Synchronizace služby Azure AD Connect: Technické koncepty](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Synchronizace Azure AD Connect: Principy architektury](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Synchronizace Azure AD Connect: Principy deklarativního zřizování](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Synchronizace azure a připojení služby Azure AD: principy výrazů deklarativního zřizování](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Synchronizace služby Azure AD Connect: Principy výchozí konfigurace](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Synchronizace azure ad připojení: principy uživatelů, skupin a kontaktů](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Synchronizace služby Azure AD Connect: Atributy Stín](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Další kroky
- [Synchronizace služby Azure AD Connect](how-to-connect-sync-whatis.md).
- [Co je hybridní identita?](whatis-hybrid-identity.md).
