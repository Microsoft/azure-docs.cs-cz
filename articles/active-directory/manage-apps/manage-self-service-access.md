---
title: Konfigurace přiřazení samoobslužných aplikací | Dokumenty společnosti Microsoft
description: Povolení samoobslužného přístupu k aplikacím, které uživatelům umožní najít vlastní aplikace
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
ms.date: 04/20/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89e09fd63ee6121ac9bf7f3c2be00f0ac22f752f
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731706"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Konfigurace přiřazení samoobslužné aplikace

Než se uživatelé budou moci sami zjišťovat aplikace z přístupového panelu Moje aplikace, musíte povolit **samoobslužné aplikace přístup k** jakýmkoli aplikacím, které chcete uživatelům umožnit, aby sami zjišťovali a žádali o přístup. Tato funkce je k dispozici pro aplikace, které byly přidány z [Galerie Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app), [Proxy aplikací Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) nebo byly přidány prostřednictvím [souhlasu uživatele nebo správce](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience). 

Tato funkce je skvělý způsob, jak ušetřit čas a peníze jako skupina IT a je vysoce doporučeno jako součást nasazení moderních aplikací s Azure Active Directory.

Tato funkce umožňuje:

-   Umožňte uživatelům, aby sami objevovali aplikace z [přístupového panelu Moje aplikace,](https://myapps.microsoft.com/) aniž by obtěžovali skupinu IT.

-   Přidejte tyto uživatele do předem nakonfigurované skupiny, abyste viděli, kdo požádal o přístup, odebral přístup a spravoval role, které jsou jim přiřazeny.

-   Volitelně povolit obchodní schvalovatel schválit žádosti o přístup k aplikacím, takže skupina IT nemusí.

-   Volitelně nakonfigurujte až 10 osob, které mohou schválit přístup k této aplikaci.

-   Volitelně umožňuje schvalovatelovi firmy nastavit hesla, která mohou uživatelé použít k přihlášení k aplikaci, přímo z [panelu přístupového panelu aplikací](https://myapps.microsoft.com/)schvalujícího podniku .

-   Volitelně automaticky přiřazuje samoobslužné uživatele přímo k roli aplikace.

> [!NOTE]
> Licence Azure Active Directory Premium (P1 nebo P2) je vyžadována, aby uživatelé požádali o připojení k samoobslužné aplikaci a aby vlastníci schvalovali nebo zamítali žádosti. Bez licence Azure Active Directory Premium uživatelé nemohou přidávat samoobslužné aplikace.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Povolení samoobslužného přístupu k aplikacím, které uživatelům umožní najít vlastní aplikace

Samoobslužný přístup k aplikacím je skvělý způsob, jak uživatelům umožnit vlastní zjišťování aplikací a volitelně umožnit obchodní skupině schvalovat přístup k těmto aplikacím. U aplikací s jedním přihlášením k heslu můžete také povolit obchodní skupině spravovat přihlašovací údaje přiřazené těmto uživatelům z jejich vlastních přístupových panelů Moje aplikace.

Chcete-li povolit přístup samoobslužných aplikací k aplikaci, postupujte podle následujících kroků:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako globální správce.

2. Vyberte **Azure Active Directory**. V levé navigační nabídce vyberte **možnost Podnikové aplikace**.

3. Vyberte aplikaci ze seznamu. Pokud aplikaci nevidíte, začněte do vyhledávacího pole psát její název. Nebo pomocí ovládacích prvků filtru vyberte typ, stav nebo viditelnost aplikace a pak vyberte **Použít**.

4. V levé navigační nabídce vyberte **Možnost Samoobslužná služba**.

5. Chcete-li povolit samoobslužný přístup k aplikacím pro tuto aplikaci, přepněte přepínač **Povolit uživatelům žádost o přístup k této aplikaci** na **Ano.**

6. Vedle **položky Ke skupině, která má být přiřazena uživatelům,** klepněte na tlačítko **Vybrat skupinu**. Vyberte skupinu a klepněte na tlačítko **Vybrat**. Po schválení požadavku uživatele bude požadavek přidán do této skupiny. Při prohlížení členství v této skupině uvidíte, komu byl udělen přístup k aplikaci prostřednictvím samoobslužného přístupu.
  
    > [!NOTE]
    > Toto nastavení nepodporuje skupiny synchronizované z místního prostředí.

7. **Nepovinné:** Chcete-li vyžadovat schválení podniku před tím, než je uživatelům povolen přístup, nastavte **před udělením přístupu k této aplikaci možnost Vyžadovat schválení?** **Yes**

8. Volitelné: Chcete-li, aby obchodní schvalovatelé mohli pro schválené uživatele zadávat hesla odesílaná do této aplikace pro schválené uživatele, nastavte pro aplikace, které **používají pouze jednotné přihlašování k heslu,** možnost **povolit schvalovatelům nastavení uživatelských hesel pro tuto aplikaci?** **Yes**

9. **Nepovinné:** Chcete-li určit obchodní schvalovatele, kteří mohou schvalovat přístup k této **Select approvers**aplikaci, vedle **položky Kdo může schválit přístup k této aplikaci?** Pak klikněte na **Vybrat**.

    >[!NOTE]
    >Skupiny nejsou podporovány. Můžete vybrat až 10 jednotlivých obchodních schvalovatelů. Pokud zadáte více schvalovatelů, může žádost o přístup schválit každý jednotlivý schvalovatel.

10. **Volitelné:** **U aplikací, které zveřejňují role**, přiřazení samoobslužných schválených uživatelů k roli **Select Role**vedle **položky Komu mají být uživatelé v této aplikaci přiřazeni?** Pak klikněte na **Vybrat**.

11. Klikněte na tlačítko **Uložit** v horní části podokna a dokončete.

Po dokončení konfigurace samoobslužných aplikací mohou uživatelé přejít na [přístupový panel Moje aplikace](https://myapps.microsoft.com/) a kliknutím na tlačítko **Přidat samoobslužné aplikace** najít aplikace, které jsou povolané se samoobslužným přístupem. Schvalovatelé firmy také vidí oznámení na [přístupovém panelu Moje aplikace](https://myapps.microsoft.com/). Můžete povolit e-mail s upozorněním, když uživatel požádal o přístup k aplikaci, která vyžaduje jejich schválení.

## <a name="next-steps"></a>Další kroky
[Nastavení služby Azure Active Directory pro samoobslužnou správu skupin](../users-groups-roles/groups-self-service-management.md)
