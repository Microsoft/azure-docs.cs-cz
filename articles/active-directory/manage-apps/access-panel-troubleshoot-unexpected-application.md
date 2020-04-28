---
title: Jak se aplikace zobrazují na přístupovém panelu | Microsoft Docs
description: Řešení potíží s tím, proč se aplikace zobrazuje na přístupovém panelu
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "65784414"
---
# <a name="how-applications-appear-on-the-access-panel"></a>Jak se aplikace zobrazují na přístupovém panelu

Přístupový panel je webový portál, který umožňuje uživateli s pracovním nebo školním účtem ve službě Azure Active Directory (Azure AD) zobrazovat a spouštět cloudové aplikace, kterým správce Azure AD udělil přístup k systému. Tyto aplikace jsou nakonfigurované jménem uživatele na portálu Azure AD. Správce může aplikaci zřídit přímo uživateli nebo skupině, která je součástí výsledného uživatele, v aplikaci, která se zobrazuje na přístupovém panelu uživatele.

## <a name="general-issues-to-check-first"></a>Obecné problémy k první kontrole

-   Pokud se aplikace odebrala z uživatele nebo skupiny, které je uživatel členem, zkuste se znovu přihlásit a přejít na přístupový panel uživatele a zjistit, jestli je aplikace odebraná.

-   Pokud byla licence odebrána od uživatele nebo skupiny, které je uživatel členem této služby, může trvat dlouhou dobu v závislosti na velikosti a složitosti skupiny pro změny, které mají být provedeny. Před přihlášením k přístupovému panelu povolte dodatečnou dobu.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problémy související s přiřazováním aplikací uživatelům

Uživatel může na svém přístupovém panelu vidět aplikaci, protože k ní dříve byla přiřazena. Níže jsou uvedeny některé způsoby, jak ověřit:

-   [Zjistit, jestli je uživatel přiřazený k aplikaci](#check-if-a-user-is-assigned-to-the-application)

-   [Zjistit, jestli se uživatel nachází v licenci týkající se aplikace](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Zjistit, jestli je uživatel přiřazený k aplikaci

Pokud chcete zjistit, jestli je uživatel přiřazený k aplikaci, postupujte podle těchto kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

6. **Vyhledejte** název příslušné aplikace.

7. klikněte na **Uživatelé a skupiny**.

8. Zkontrolujte, jestli je váš uživatel přiřazený k aplikaci.

   * Chcete-li odebrat uživatele z aplikace, **klikněte na řádek** uživatele a vyberte možnost **Odstranit**.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Zjistit, jestli se uživatel nachází v licenci týkající se aplikace

Pokud chcete ověřit přiřazené licence uživatele, postupujte podle těchto kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce klikněte na **Uživatelé a skupiny** .

5. klikněte na **Všichni uživatelé**.

6. **Vyhledejte** uživatele, kterého vás zajímá, a **klikněte na řádek** , který chcete vybrat.

7. Kliknutím na **licence** zobrazíte licence, které uživatel aktuálně přiřadil.

   * Pokud je uživatel přiřazen k licenci Office, umožní to, aby se na přístupovém panelu uživatele zobrazovaly aplikace Office první strany.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problémy související s přiřazováním aplikací do skupin

Uživatel může zobrazit aplikaci na svém přístupovém panelu, protože je součástí skupiny, které byla aplikace přiřazena. Níže jsou uvedeny některé způsoby, jak ověřit:

-   [Ověřit členství uživatele ve skupinách](#check-a-users-group-memberships)

-   [Zkontroluje, jestli je uživatel členem skupiny, která je přiřazená k licenci.](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>Ověřit členství uživatele ve skupinách

Chcete-li ověřit členství ve skupině, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce klikněte na **Uživatelé a skupiny** .

5. klikněte na **Všichni uživatelé**.

6. **Vyhledejte** uživatele, kterého vás zajímá, a **klikněte na řádek** , který chcete vybrat.

7. klikněte na **skupiny.**

8. Zkontrolujte, jestli je váš uživatel součástí skupiny přiřazené k aplikaci.

   * Pokud chcete uživatele odebrat ze skupiny, **klikněte na řádek** skupiny a vyberte Odstranit.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Zkontroluje, jestli je uživatel členem skupiny, která je přiřazená k licenci.

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce klikněte na **Uživatelé a skupiny** .

5. klikněte na **Všichni uživatelé**.

6. **Vyhledejte** uživatele, kterého vás zajímá, a **klikněte na řádek** , který chcete vybrat.

7. klikněte na **skupiny.**

8. klikněte na řádek konkrétní skupiny.

9. Kliknutím na **licence** zobrazíte licence, ke kterým se skupina přiřadí.

   * Pokud je skupina přiřazená k licenci Office, může to povolit, aby se některé aplikace Office, které se na přístupovém panelu zobrazovaly, zobrazily na přístupovém panelu uživatele.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Pokud tyto kroky pro řešení potíží nevyřešily problém

Otevřete lístek podpory s následujícími informacemi, pokud jsou k dispozici:

-   ID chyby korelace

-   UPN (uživatelská e-mailová adresa)

-   ID tenanta

-   Typ prohlížeče

-   Časové pásmo a čas/časový rámec při výskytu chyby

-   Fiddler trasování

## <a name="next-steps"></a>Další kroky
[Správa aplikací pomocí Azure Active Directory](what-is-application-management.md)
