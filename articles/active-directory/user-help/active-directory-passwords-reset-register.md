---
title: Registrace pro resetování vlastního hesla – Azure AD
description: Zaregistrujte data ověřování pro Samoobslužné resetování hesla služby Azure AD, abyste mohli resetovat vlastní heslo, aniž byste museli pomáhat s správcem.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 796702b2189dcfb001f72913e4a01c0ae01462ad
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820454"
---
# <a name="register-to-reset-your-own-password"></a>Registrace pro resetování vlastního hesla

> [!IMPORTANT]
> Jste tady, protože se nemůžete přihlásit? Pokud ano, přečtěte si téma [resetování svého pracovního nebo školního hesla](active-directory-passwords-update-your-own-password.md).

Jako koncový uživatel můžete resetovat heslo nebo odemknout účet, pokud použijete službu Azure Active Directory (Azure AD) Samoobslužné resetování hesla (SSPR). Předtím, než budete moci tuto funkci používat, je nutné zaregistrovat metody ověřování nebo potvrdit předdefinované metody ověřování, které správce naplní.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registrace nebo potvrzení ověřovacích dat pro samoobslužné resetování hesla

1. Na zařízení otevřete webový prohlížeč a pak na [registrační stránku pro resetování hesla](https://aka.ms/ssprsetup).
2. Zadejte své uživatelské jméno a heslo, které zadal správce.
3. V závislosti na tom, jak vaši pracovníci IT nakonfigurovali nějaké věci, můžete nakonfigurovat a ověřit jednu z následujících možností. Pokud váš správce má vaše oprávnění k používání vašich informací, může pro vás naplnit některé informace.
    * **Telefon do kanceláře**: tuto možnost může nastavit jenom správce.
    * **Telefon pro ověření**: tuto možnost nastavte na jiné telefonní číslo, ke kterému máte přístup. Příkladem je mobilní telefon, který může přijímat text nebo volání.
    * **E-mail pro ověřování**: tuto možnost nastavte na alternativní e-mailovou adresu, ke které máte přístup bez použití hesla, které chcete resetovat.
    * **Bezpečnostní otázky**: váš správce schválil tento seznam dotazů, na které vám odpoví. Stejnou otázku ani odpověď nemůžete použít více než jednou.
4. Poskytněte a ověřte informace vyžadované správcem. Pokud je k dispozici více než jedna možnost, doporučujeme, abyste zaregistrovali více metod. Díky tomu budete mít flexibilitu, když jedna z metod není k dispozici. Příkladem je, že jste na cestách a nemůžete získat přístup k telefonu do kanceláře.

    ![Zaregistrujte metody ověřování a vyberte Dokončit.][Register]

5. Vyberte **Dokončit**. SSPR teď můžete v budoucnu používat v případě potřeby.

Pokud zadáte data pro **telefon pro ověření** nebo **ověřovací e-mail**, nebude se zobrazovat v globálním adresáři. Tyto údaje se zobrazí jen vám a vašim správcům. Odpovědi na vaše bezpečnostní otázky uvidíte jenom vy.

Správci můžou po určitou dobu vyžadovat, abyste své metody ověřování potvrdili, abyste měli jistotu, že máte stále zaregistrované příslušné metody.

## <a name="common-problems-and-their-solutions"></a>Běžné problémy a jejich řešení

 Tady jsou některé běžné chybové případy a jejich řešení:

| Případ chyby| Jakou chybu vidíte?| Řešení |
| --- | --- | --- |
| Po zadání ID uživatele se zobrazí stránka "kontaktujte správce" | Kontaktujte prosím správce. <br> <br> Zjistili jsme, že heslo ke svému uživatelskému účtu nespravuje Microsoft. V důsledku toho nemůžeme automaticky resetovat vaše heslo. <br> <br> Další pomoc vám poskytne váš personál oddělení IT. | Tato zpráva se zobrazuje, protože pracovníci IT spravují vaše heslo v místním prostředí a neumožňují resetovat heslo z důvodu **nedostatku přístupu k vašemu účtu** . <br> <br> Pokud si chcete resetovat heslo, obraťte se přímo na pracovníky IT a požádejte ho o nápovědu. Dejte jim jistotu, že chcete resetovat heslo, aby mohli tuto funkci povolit za vás.|
| Po zadání ID uživatele se zobrazí chyba, že účet není povolen pro resetování hesla. | Váš účet není pro resetování hesla povolený. <br> <br> Je nám líto, ale vaši zaměstnanci IT nevytvořili účet pro použití s touto službou. <br> <br> Pokud byste chtěli, můžeme kontaktovat správce ve vaší organizaci, aby vám heslo resetoval. | Tato zpráva se zobrazuje, protože vaši pracovníci IT nepovolili resetování hesla ve vaší organizaci z důvodu **nedostatku přístupu k vašemu účtu** nebo nemáte licenci k použití této funkce. <br> <br> Pokud chcete resetovat heslo, vyberte odkaz **kontaktujte správce** . E-mail se pošle pracovníkům IT vaší společnosti. E-mail jim umožní sdělit, že chcete resetovat heslo, aby tato funkce mohla mít za vás. |
| Po zadání ID uživatele se zobrazí chyba, že se vám nepovedlo ověřit váš účet. | Nepovedlo se nám ověřit váš účet. <br> <br> Pokud byste chtěli, můžeme kontaktovat správce ve vaší organizaci, aby vám heslo resetoval. | Tato zpráva se zobrazuje, protože jste povolili resetování hesla, ale nezaregistrovali jste ji k používání služby. Pokud se chcete zaregistrovat k resetování hesla, přejděte po opětovném získání přístupu k účtu na [registrační stránku pro resetování hesla](https://aka.ms/ssprsetup) . <br> <br> Pokud chcete resetovat heslo, vyberte odkaz **kontaktujte správce** a odešlete e-mail pracovníkům IT vaší společnosti. |

## <a name="next-steps"></a>Další kroky

* [Změna hesla pomocí samoobslužného resetování hesla](active-directory-passwords-update-your-own-password.md)
* [Registrační stránka pro resetování hesla](https://aka.ms/ssprsetup)
* [Portál pro resetování hesla](https://passwordreset.microsoftonline.com/)
* [Když se nemůžete přihlásit k účet Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Registrační stránka pro resetování hesla zobrazující registrované metody a tlačítko Dokončit"

