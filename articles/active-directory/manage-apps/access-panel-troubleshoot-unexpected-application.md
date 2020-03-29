---
title: Jak se aplikace zobrazují na přístupovém panelu | Dokumenty společnosti Microsoft
description: Poradce při potížích s tím, proč se aplikace zobrazuje na přístupovém panelu
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.reviewr: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fa8ea75cc7fda05326c802c25a91d025b66b5ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784414"
---
# <a name="how-applications-appear-on-the-access-panel"></a>Jak se aplikace zobrazují na přístupovém panelu

Přístupový panel je webový portál, který umožňuje uživateli s pracovním nebo školním účtem ve službě Azure Active Directory (Azure AD) zobrazit a spustit cloudové aplikace, ke kterým jim správce Azure AD udělil přístup. Tyto aplikace jsou konfigurovány jménem uživatele na portálu Azure AD. Správce může zřídit aplikaci přímo uživateli nebo skupině, jejíž součástí je uživatel, což vede k tomu, že se aplikace zobrazí na přístupovém panelu uživatele.

## <a name="general-issues-to-check-first"></a>Obecné problémy, které je třeba nejprve zkontrolovat

-   Pokud byla aplikace odebrána uživateli nebo skupině, jejíž je uživatel členem, zkuste se po několika minutách znovu přihlásit a odhlásit na přístupovém panelu uživatele a zjistěte, zda je aplikace odebrána.

-   Pokud byla odebrána licence uživateli nebo skupině, může to to to trvat dlouho, v závislosti na velikosti a složitosti skupiny pro změny, které mají být provedeny. Před přihlášením na přístupový panel vyčkejte více času.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problémy související s přiřazování aplikací uživatelům

Uživatel může být vidět aplikace na jejich přístupový panel, protože byl dříve přiřazen k němu. Níže jsou uvedeny některé způsoby, jak zkontrolovat:

-   [Kontrola, zda je uživatel přiřazen k aplikaci](#check-if-a-user-is-assigned-to-the-application)

-   [Zkontrolujte, zda uživatel nemá licenci související s aplikací](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Kontrola, zda je uživatel přiřazen k aplikaci

Chcete-li zkontrolovat, zda je uživatel přiřazen k aplikaci, postupujte takto:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

6. **Vyhledejte** název dané žádosti.

7. klikněte na **Uživatelé a skupiny**.

8. Zkontrolujte, zda je uživatel přiřazen k aplikaci.

   * Chcete-li odebrat uživatele z aplikace, **klepněte na řádek** uživatele a vyberte **příkaz Odstranit**.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Zkontrolujte, zda uživatel nemá licenci související s aplikací

Chcete-li zkontrolovat přiřazené licence uživatele, postupujte takto:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V navigační nabídce klikněte na **Uživatelé a skupiny.**

5. klepněte na položku **Všichni uživatelé**.

6. **Vyhledejte** uživatele, který vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7. Chcete-li zjistit, které licence uživatel aktuálně přiřadil, klepněte na **položku Licence.**

   * Pokud je uživatel přiřazen k licenci sady Office, umožní se aplikace sady Office první strany zobrazit na přístupovém panelu uživatele.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problémy související s přiřazování žádostí skupinám

Uživatel může být vidět aplikace na jejich přístupový panel, protože jsou součástí skupiny, která byla přiřazena aplikace. Níže jsou uvedeny některé způsoby, jak zkontrolovat:

-   [Kontrola členství uživatele ve skupinách](#check-a-users-group-memberships)

-   [Kontrola, zda je uživatel členem skupiny přiřazené k licenci](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>Kontrola členství uživatele ve skupinách

Chcete-li zkontrolovat členství ve skupině, postupujte takto:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V navigační nabídce klikněte na **Uživatelé a skupiny.**

5. klepněte na položku **Všichni uživatelé**.

6. **Vyhledejte** uživatele, který vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7. klikněte na **Skupiny.**

8. Zkontrolujte, zda je uživatel součástí skupiny přiřazené k aplikaci.

   * Pokud chcete odebrat uživatele ze skupiny, **klikněte na řádek** skupiny a vyberte odstranit.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Kontrola, zda je uživatel členem skupiny přiřazené k licenci

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V navigační nabídce klikněte na **Uživatelé a skupiny.**

5. klepněte na položku **Všichni uživatelé**.

6. **Vyhledejte** uživatele, který vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7. klikněte na **Skupiny.**

8. klikněte na řádek určité skupiny.

9. Chcete-li zjistit, které licence k ní skupina přiřadila, klepněte na **položku Licence.**

   * Pokud je skupina přiřazena k licenci sady Office, může to umožnit, aby se některé aplikace sady Office první strany zobrazily na přístupovém panelu uživatele.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Pokud tyto kroky řešení potíží problém nevyřeší

otevřete lístek podpory s následujícími informacemi, pokud jsou k dispozici:

-   ID chyby korelace

-   UPN (e-mailová adresa uživatele)

-   ID tenanta

-   Typ prohlížeče

-   Časové pásmo a časový rámec během chyby

-   Fiddler stopy

## <a name="next-steps"></a>Další kroky
[Správa aplikací pomocí služby Azure Active Directory](what-is-application-management.md)
