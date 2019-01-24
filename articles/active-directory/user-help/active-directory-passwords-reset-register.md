---
title: Registrace pro samoobslužné resetování hesla – Azure Active Directory | Dokumentace Microsoftu
description: Registrace ověřovacích dat pro hesla pomocí samoobslužné služby Azure AD resetování
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: lizross
ms.openlocfilehash: 2a9e65e36e31f4b2b382fac2bd9898580be18900
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54821664"
---
# <a name="register-for-self-service-password-reset"></a>Registrace pro samoobslužné resetování hesla

> [!IMPORTANT]
> Jste tady, protože nemůžete se přihlásit? Pokud ano, přečtěte si téma [obnovit heslo pracovního nebo školního](active-directory-passwords-update-your-own-password.md).

Jako koncový uživatel můžete obnovit heslo nebo odemknout účet sami, pokud používáte Azure Active Directory (Azure AD) samoobslužné resetování hesla (SSPR). Tuto funkci mohli používat, budete muset zaregistrovat metody ověřování nebo potvrdit předdefinované metody ověřování, které připravil správce.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registrace nebo potvrzení ověřovacích dat pro samoobslužné resetování hesla

1. Otevřete webový prohlížeč na zařízení a přejděte [registrační stránku pro resetování hesla](https://aka.ms/ssprsetup).
2. Zadejte svoje uživatelské jméno a heslo, které vám poskytl správce.
3. V závislosti na tom, jak vaši pracovníci IT nakonfiguroval věcí jsou k dispozici pro konfiguraci a ověřit jeden nebo více z následujících možností. Pokud správce má váš souhlas s používáním vašich informací, jsou některé z informací můžete naplnit.
    * **Telefon do kanceláře**: Tuto možnost můžete nastavit pouze správce.
    * **Telefon pro ověření**: Tuto možnost nastavte na jiné telefonní číslo, které máte přístup. Příkladem je mobilní telefon, který může být text nebo volání.
    * **E-mail pro ověření**: Tuto možnost nastavte na alternativní e-mailovou adresu, která lze přistupovat bez použití hesla, které chcete obnovit.
    * **Bezpečnostní otázky**: Správce schválil tento seznam otázek, na které odpovíte. Nelze použít stejný dotaz nebo odpověď více než jednou.
4. Zadejte a zkontrolujte informace, které vyžaduje váš správce. Pokud je k dispozici více než jednu možnost, doporučujeme, že zaregistrujete několik metod. To nabízí flexibilitu při jedné z metod není k dispozici. Je například při cestování a budete moci přistupovat k telefonu do kanceláře.

    ![Zaregistrujte si metody ověřování a klepnutím na tlačítko Dokončit][Register]

5. Vyberte **Dokončit**. Můžete teď použít samoobslužné resetování HESLA, když budete chtít v budoucnu.

Pokud zadáte údaj pro **telefon pro ověření** nebo **E-mail pro ověření**, není zobrazená v globálním adresáři. Tyto údaje se zobrazí jen vám a vašim správcům. Můžete jenom zobrazit odpovědi na své bezpečnostní otázky.

Správce může vyžadovat budete vyzváni k potvrzení své metody ověřování po určité době, abyste měli jistotu, že máte stále zaregistrované odpovídající metody.

## <a name="common-problems-and-their-solutions"></a>Běžné problémy a jejich řešení

 Tady jsou některé běžné případy chyb a jejich řešení:

| Případ chyby| Jaká chyba se zobrazí?| Řešení |
| --- | --- | --- |
| Můžu získat stránky "kontaktujte správce" po zadání ID uživatele | Kontaktujte svého správce. <br> <br> Zjistili jsme, že heslo vašeho uživatelského účtu nespravuje Microsoft. V důsledku toho nemůžeme automaticky resetovat své heslo. <br> <br> Další pomoc, obraťte se na pracovníky IT. | Tato zpráva se zobrazuje, protože pracovníci IT spravuje vaše heslo v místním prostředí a neumožňuje resetovat heslo **nemá přístup k účtu** odkaz. <br> <br> Resetovat své heslo, požádejte přímo o pomoc pracovníky IT. Informujte je, že chcete resetovat heslo, abyste tuto funkci, můžete povolit za vás.|
| Získat chybu "váš účet není povolen pro resetování hesla" po zadání ID uživatele | Váš účet není povolen pro resetování hesla. <br> <br> Je nám líto, ale vaši pracovníci IT nenastavila váš účet pro použití s touto službou. <br> <br> Pokud chcete, můžeme kontaktovat správce ve vaší organizaci k vám heslo resetoval. | Tato zpráva se zobrazuje, protože pracovníci IT nepovolil resetování hesla pro vaši organizaci **nemá přístup k účtu** propojení nebo nebyl licenci, abyste použili funkci. <br> <br> Chcete-li resetovat heslo, vyberte **obraťte se na správce** odkaz. Odešle e-mail pro vaši společnost pracovníků IT. E-mailu umožňuje věděli, že chcete obnovit heslo, abyste tuto funkci, můžete povolit za vás. |
| Získat chybu "máme nelze ověřit váš účet" po zadání ID uživatele | Nemohli jsme ověřit váš účet. <br> <br> Pokud chcete, můžeme kontaktovat správce ve vaší organizaci k vám heslo resetoval. | Zobrazuje se vám tato zpráva je povolená pro resetování hesla, protože jste se ještě nezaregistrovali pro používání služby. K registraci pro resetování hesla, pokračujte [registrační stránku pro resetování hesla](https://aka.ms/ssprsetup) po jste znovu získali přístup ke svému účtu. <br> <br> Pokud chcete resetovat heslo, vyberte **obraťte se na správce** odkaz k odesílání e-mailu pro vaši společnost pracovníků IT. |

## <a name="next-steps"></a>Další postup

* [Změna hesla pomocí samoobslužné resetování hesla](active-directory-passwords-update-your-own-password.md)
* [Registrační stránka pro resetování hesla](https://aka.ms/ssprsetup)
* [Portál pro resetování hesla](https://passwordreset.microsoftonline.com/)
* [Pokud nemůžete se přihlásit ke svému účtu Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Stránky registraci pro resetování hesla zobrazující registrované metody a na tlačítko Dokončit"

