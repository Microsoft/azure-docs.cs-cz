---
title: Zaregistrovat pro resetování hesla pomocí samoobslužné služby – Azure Active Directory
description: Zaregistrovat data ověření pro hesla pomocí samoobslužné služby Azure AD resetovat
services: active-directory
keywords: ''
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: barlan
ms.custom: end-user;seohack1
ms.openlocfilehash: dbb82e125381a4307ed742294f880600612ffc52
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2018
ms.locfileid: "29460471"
---
# <a name="register-for-self-service-password-reset"></a>Registrace pro samoobslužné resetování hesla

> [!IMPORTANT]
> Jste tady vzhledem k tomu, že nemůžete se přihlásit? Pokud ano, najdete v části [resetovat heslo pracovního nebo školního](active-directory-passwords-update-your-own-password.md).

Jako koncový uživatel můžete resetovat heslo nebo odemknout sami podle účtu, pokud používáte Azure Active Directory (Azure AD) samoobslužné resetování hesla (SSPR). Před použitím této funkce budete muset registraci vaší metody ověřování nebo potvrďte metody předdefinované ověřování, které má naplněno správce.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registrace nebo potvrzení ověřovacích dat pro samoobslužné resetování hesla

1. Otevřete webový prohlížeč v zařízení a přejděte na [resetování hesla registrační stránce](https://aka.ms/ssprsetup).
2. Zadejte svoje uživatelské jméno a heslo, které poskytuje správce.
3. V závislosti na tom, jak pracovníci IT nakonfiguroval věcí jsou k dispozici pro konfiguraci a ověřte jeden nebo více z následujících možností. Pokud správce má oprávnění k použití vašich informací, že některé z informací můžete naplnit.
    * **Telefon do kanceláře**: pouze správce může nastavit tuto možnost.
    * **Telefon pro ověření**: nastavte tuto možnost na jiné telefonní číslo, které máte přístup. Příkladem je mobilní telefon, který může přijímat text nebo volání.
    * **Ověření e-mailu**: tuto možnost nastavte na alternativní e-mailovou adresu, kterému budete mít přístup bez použití heslo chcete resetovat.
    * **Bezpečnostní otázky**: Správce schválena tento seznam zadáte odpovědi na otázky. Nelze použít stejný dotaz nebo odpovědět více než jednou.
4. Zadejte a zkontrolujte informace, které váš správce vyžaduje. Pokud je k dispozici více než jednu možnost, doporučujeme zaregistrovat několik metod. To nabízí flexibilitu při jedné z metod není k dispozici. Příkladem je při cestujete a vy nelze získat přístup k váš telefon do kanceláře.

    ![Zaregistrujte metody ověřování a vyberte dokončit][Register]

5. Vyberte **Dokončit**. SSPR teď můžete použít, když potřebujete v budoucnu.

Pokud zadáte data pro **telefon pro ověření** nebo **ověřování e-mailu**, se nezobrazuje v globálním adresáři. Tyto údaje se zobrazí jen vám a vašim správcům. Pouze najdete odpovědi na své bezpečnostní otázky.

Správce může vyžadovat potvrďte ověřovací metody po určitou dobu a ujistěte se, že máte příslušné metody zaregistrován.

## <a name="common-problems-and-their-solutions"></a>Běžné problémy a jejich řešení

 Zde jsou některé běžné případy chyba a jejich řešení:

| Chyb| Jaké chyby se zobrazí?| Řešení |
| --- | --- | --- |
| Zobrazí na stránce ", kontaktujte prosím správce" po zadání ID uživatele | Kontaktujte svého správce. <br> <br> Zjistili jsme, že heslo vašeho uživatelského účtu nespravuje společnost Microsoft. Snažíme se v důsledku toho nelze automaticky resetovat heslo. <br> <br> Pracovníci IT požádejte o pomoc. | Tato zpráva se zobrazuje, protože pracovníci oddělení IT spravuje vaše heslo v místním prostředí a neumožňuje resetování hesla z **nelze získat přístup k účtu** odkaz. <br> <br> Pokud chcete resetovat heslo, obraťte se na vaši informatici přímo. Dáte jim vědět, že chcete resetovat heslo, takže se můžete povolit tuto funkci můžete.|
| Zobrazí chybu "váš účet není povolen pro resetování hesla" po zadání ID uživatele | Váš účet není povolen pro resetování hesla. <br> <br> Je nám líto, ale pracovníci IT nenastavil váš účet pro použití s touto službou. <br> <br> Pokud chcete, můžeme kontaktovat správce ve vaší organizaci k resetování hesla. | Tato zpráva se zobrazuje, protože pracovníci IT nepovolil resetování hesla pro vaší organizace **nelze získat přístup k účtu** odkaz nebo není licencovaná vám umožní používat funkci. <br> <br> Chcete-li resetovat heslo, vyberte **, kontaktujte správce** odkaz. Odešle e-mail pro vaši společnost zaměstnanců IT. E-mailu umožňuje, aby věděli, že chcete resetovat heslo, takže se můžete povolit tuto funkci můžete. |
| Zobrazí chybu "jsme nemohl ověřit váš účet" po zadání ID uživatele | Nemohli jsme ověřit váš účet. <br> <br> Pokud chcete, můžeme kontaktovat správce ve vaší organizaci k resetování hesla. | Tato zpráva se zobrazuje, protože jste povolen pro resetování hesla, ale nejsou zaregistrované pro používání služby. Chcete-li zaregistrovat pro resetování hesla, přejděte na [registrační stránku resetování hesla](https://aka.ms/ssprsetup) po jste znovu získali přístup ke svému účtu. <br> <br> Pokud chcete resetovat heslo, vyberte **, kontaktujte správce** odkaz Odeslat e-mail pro vaši společnost zaměstnanců IT. |

## <a name="next-steps"></a>Další postup

* [Změna hesla pomocí samoobslužné resetování hesla](active-directory-passwords-update-your-own-password.md)
* [Registrační stránka pro resetování hesla](https://aka.ms/ssprsetup)
* [Portál pro resetování hesla](https://passwordreset.microsoftonline.com/)
* [Pokud nemůžete se přihlásit ke svému účtu Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Stránka pro registraci pro resetování hesla zobrazující registrované metody a na tlačítko Dokončit"

