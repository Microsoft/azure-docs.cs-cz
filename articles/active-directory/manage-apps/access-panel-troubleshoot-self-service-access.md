---
title: Problém s používáním samoobslužného přístupu k aplikacím | Dokumenty společnosti Microsoft
description: Poradce při potížích souvisejících se samoobslužným přístupem k aplikacím
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
ms.openlocfilehash: a981dfb1d72c21eccf2ad7119ea219114ed15aed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784277"
---
# <a name="problem-using-self-service-application-access"></a>Problém s použitím samoobslužného přístupu k aplikacím

Samoobslužný přístup k aplikacím je skvělý způsob, jak uživatelům umožnit vlastní zjišťování aplikací, volitelně umožnit obchodní skupině schvalovat přístup k těmto aplikacím. Obchodní skupině můžete povolit správu přihlašovacích údajů přiřazených těmto uživatelům pro aplikace s jedním přihlášením hesla přímo z jejich přístupových panelů.

Než budou uživatelé moci sami zjišťovat aplikace ze svého přístupového panelu, musíte povolit **samoobslužné aplikace přístup k** všem aplikacím, které chcete uživatelům umožnit vlastní zjišťování a požádat o přístup.

## <a name="general-issues-to-check-first"></a>Obecné problémy, které je třeba nejprve zkontrolovat

-   Zkontrolujte, zda je správně nakonfigurován samoobslužný přístup k aplikacím. Viz "Jak nakonfigurovat přístup k samoobslužným aplikacím".

-   Ujistěte se, že uživatel nebo skupina byla povolena požádat o samoobslužný přístup k aplikacím.

-   Ujistěte se, že uživatel navštěvuje správné místo pro přístup k samoobslužným aplikacím. Uživatelé mohou přejít na [přístupový panel aplikací](https://myapps.microsoft.com/) a klepnutím na tlačítko **+Přidat** vyhledat aplikace, ke kterým jste povolili samoobslužný přístup.

-   Pokud byl přístup k samoobslužným aplikacím nedávno nakonfigurován, zkuste se po několika minutách znovu přihlásit a odhlásit na přístupovém panelu uživatele a zjistit, zda se neobjevily změny samoobslužného přístupu.

## <a name="how-to-configure-self-service-application-access"></a>Konfigurace samoobslužného přístupu k aplikacím

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

    >[!NOTE]
    > Skupiny nejsou podporovány.
    >
    >

13. **Volitelné:** **U aplikací, které zveřejňují role**, chcete-li roli přiřadit samoobslužné schválené uživatele, klepněte na volič vedle **položky Komu by měli být uživatelé v této aplikaci přiřazeni?**

14. Klikněte na tlačítko **Uložit** v horní části okna dokončit.

Po dokončení konfigurace samoobslužných aplikací mohou uživatelé přejít na [přístupový panel pro aplikace](https://myapps.microsoft.com/) a klepnutím na tlačítko **+Přidat** najít aplikace, ke kterým jste povolili samoobslužný přístup. Obchodní schvalovatelé také vidět oznámení v jejich [přístupový panel aplikace](https://myapps.microsoft.com/). Můžete povolit e-mail s upozorněním, když uživatel požádal o přístup k aplikaci, která vyžaduje jejich schválení. 

Tato schválení podporují pouze pracovní postupy s jedním schválením, což znamená, že pokud zadáte více schvalovatelů, může přístup k aplikaci schválit každý jednotlivý schvalovatel.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Pokud tyto kroky řešení potíží problém nevyřeší 

otevřete lístek podpory s následujícími informacemi, pokud jsou k dispozici:

-   ID chyby korelace

-   UPN (e-mailová adresa uživatele)

-   ID tenanta

-   Typ prohlížeče

-   Časové pásmo a časový rámec během chyby

-   Fiddler stopy

## <a name="next-steps"></a>Další kroky
[Nastavení služby Azure Active Directory pro samoobslužnou správu skupin](../users-groups-roles/groups-self-service-management.md)
