---
title: Jak používat samoobslužný přístup k aplikacím | Dokumenty společnosti Microsoft
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
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55da8731855c8afda496edff33f3fbb7982cd44b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784544"
---
# <a name="how-to-use-self-service-application-access"></a>Jak používat samoobslužný přístup k aplikacím

Než budou uživatelé moci sami zjišťovat aplikace ze svého přístupového panelu, musíte povolit **samoobslužné aplikace přístup k** všem aplikacím, které chcete uživatelům umožnit vlastní zjišťování a požádat o přístup.

Tato funkce je skvělý způsob, jak ušetřit čas a peníze jako skupina IT a je vysoce doporučeno jako součást nasazení moderních aplikací s Azure Active Directory.

Tato funkce umožňuje:

-   Uchvátíte uživatele, aby sami zjišťovali aplikace z [panelu Pro přístup k aplikacím,](https://myapps.microsoft.com/) aniž by obtěžovali skupinu IT.

-   Přidejte tyto uživatele do předem nakonfigurované skupiny, abyste viděli, kdo požádal o přístup, odebral přístup a spravoval role, které jsou jim přiřazeny.

-   Volitelně povolit obchodní schvalovatel schválit žádosti o přístup k aplikacím, takže skupina IT nemusí.

-   Volitelně nakonfigurujte až 10 osob, které mohou schválit přístup k této aplikaci.

-   Volitelně umožňuje schvalovatelovi firmy nastavit hesla, která mohou uživatelé použít k přihlášení k aplikaci, přímo z [panelu přístupového panelu aplikací](https://myapps.microsoft.com/)schvalujícího podniku .

-   Volitelně automaticky přiřazuje samoobslužné uživatele přímo k roli aplikace.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Povolení samoobslužného přístupu k aplikacím, které uživatelům umožní najít vlastní aplikace

Samoobslužný přístup k aplikacím je skvělý způsob, jak uživatelům umožnit vlastní zjišťování aplikací, volitelně umožnit obchodní skupině schvalovat přístup k těmto aplikacím. Obchodní skupině můžete povolit správu přihlašovacích údajů přiřazených těmto uživatelům pro aplikace s jedním přihlášením hesla přímo z jejich přístupových panelů.

Chcete-li povolit přístup samoobslužných aplikací k aplikaci, postupujte podle následujících kroků:

1. Otevřete [**portál Azure Portal**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V navigační nabídce Azure Active Directory na **položku Podnikové aplikace** klikněte na podnikové aplikace.

5. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Ze seznamu vyberte aplikaci, ke které chcete povolit samoobslužný přístup.

7. Po načtení aplikace klikněte na **Samoobslužné v** levé navigační nabídce aplikace.

8. Chcete-li povolit samoobslužný přístup k aplikacím pro tuto aplikaci, přepněte přepínač **Povolit uživatelům žádost o přístup k této aplikaci** na **Ano.**

9. Dále vyberte skupinu, do které mají být přidáni uživatelé, kteří žádají o přístup k této aplikaci, klikněte na volič vedle **popisku, ke kterému má být skupina přiřazena?**

10. **Nepovinné:** Pokud chcete vyžadovat obchodní schválení před uživatelům je povolen přístup, nastavte vyžadovat schválení před **Yes** **udělením přístupu k této aplikaci?**

11. **Volitelné: U aplikací, které používají pouze jednotné přihlašování hesla,** pokud chcete těmto obchodním schvalovatelům povolit zadání hesel odeslaných do této **Yes**aplikace pro schválené uživatele, nastavte možnost **Povolit schvalovatelům nastavení uživatelských hesel pro tuto aplikaci?**

12. **Nepovinné:** Chcete-li určit obchodní schvalovatele, kteří mohou schválit přístup k této aplikaci, klepněte na volič vedle popisku **Kdo může schválit přístup k této aplikaci?**

    * Skupiny nejsou podporovány.

13. **Volitelné:** **U aplikací, které zveřejňují role**, chcete-li roli přiřadit samoobslužné schválené uživatele, klepněte na volič vedle **položky Komu by měli být uživatelé v této aplikaci přiřazeni?**

14. Klikněte na tlačítko **Uložit** v horní části okna dokončit.

Po dokončení konfigurace samoobslužných aplikací mohou uživatelé přejít na [přístupový panel pro aplikace](https://myapps.microsoft.com/) a klepnutím na tlačítko **+Přidat** najít aplikace, ke kterým jste povolili samoobslužný přístup. Obchodní schvalovatelé také vidět oznámení v jejich [přístupový panel aplikace](https://myapps.microsoft.com/). Můžete povolit e-mail s upozorněním, když uživatel požádal o přístup k aplikaci, která vyžaduje jejich schválení. 

Tato schválení podporují pouze pracovní postupy s jedním schválením, což znamená, že pokud zadáte více schvalovatelů, může přístup k aplikaci schválit každý jednotlivý schvalovatel.

## <a name="next-steps"></a>Další kroky
[Nastavení služby Azure Active Directory pro samoobslužnou správu skupin](../users-groups-roles/groups-self-service-management.md)
