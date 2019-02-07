---
title: Jak přizpůsobit pravidla synchronizace ve službě Azure AD Connect | Dokumentace Microsoftu
description: Toto téma popisuje kroky pro řešení potíží s instalací Azure AD Connect.
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
ms.openlocfilehash: 1407df2dbc0cc590ea919ca0ead727959b1e08b4
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55773250"
---
# <a name="how-to-customize-a-synchronization-rule"></a>Jak upravit synchronizační pravidlo

## <a name="recommended-steps"></a>**Doporučené kroky**

Editor pravidel synchronizace můžete upravit nebo vytvořit nové pravidlo synchronizace. Musíte být pokročilý uživatel a provést změny synchronizační pravidla. Nesprávné změny může způsobit odstranění objektů z cílového adresáře. Přečtěte si prosím [doporučené dokumenty](#recommended-documents) získáte odborné znalosti v pravidlech synchronizace. Chcete-li upravit synchronizační pravidlo projděte si následující kroky:

* Spusťte editor synchronizace z nabídky aplikace v desktopu, jak je znázorněno níže:

    ![Synchronization Rule Editor nabídek](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* Aby bylo možné přizpůsobit výchozí synchronizační pravidlo, naklonujte existující pravidlo kliknutím na tlačítko "Upravit" na Editor pravidel synchronizace, který vytvoří kopii standardní výchozí pravidlo a jeho zakázání. Uložte klonovaný pravidlo s prioritou nižší než 100.  Priorita určuje, jaké pravidlo wins (nižší číselnou hodnotu) řešení konfliktů Pokud dojde ke konfliktu toku atributů.

    ![Synchronization Rule Editor](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* Při úpravě konkrétní atribut, v ideálním případě byste měli jenom zachovat změny atributů v klonovaném pravidlo.  Potom povolte výchozí pravidlo tak, aby upravený atribut pochází z klonovaného pravidla a další atributy se vybírají z výchozí standardní pravidla. 

* Mějte prosím na paměti, že v případě, že má hodnotu NULL v klonovaném pravidlo a nemá hodnotu NULL ve výchozím nastavení standardu vypočtenou hodnotu upravený atribut pravidlo potom, nikoli hodnotu NULL, vyhraje a nahradí hodnotu NULL. Pokud nechcete, aby hodnota NULL, být nahraďte jinou hodnotu než NULL a přiřadit AuthoritativeNull v klonovaném pravidlo.

* Chcete-li změnit **odchozí** pravidlo, změňte filtr z editor pravidel synchronizace.

## <a name="recommended-documents"></a>**Doporučené dokumenty**
* [Synchronizace Azure AD Connect: Technické koncepty](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Synchronizace Azure AD Connect: Principy architektury](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Synchronizace Azure AD Connect: Principy deklarativního zřizování](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Synchronizace Azure AD Connect: Principy výrazů deklarativního zřizování](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Synchronizace Azure AD Connect: Principy výchozí konfigurace](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Synchronizace Azure AD Connect: Principy uživatelů, skupin a kontaktů](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Synchronizace Azure AD Connect: Stínové atributy](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Další kroky
- [Synchronizace Azure AD Connect](how-to-connect-sync-whatis.md).
- [Co je hybridní identitu? ](whatis-hybrid-identity.md).