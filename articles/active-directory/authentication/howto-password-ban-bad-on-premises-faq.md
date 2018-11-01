---
title: Místní ochrana heslem služby Azure AD – nejčastější dotazy
description: Místní ochrana heslem služby Azure AD – nejčastější dotazy
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 59c89c81f618876de48de66a38e1063eb658fba4
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50743293"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Ve verzi Preview: Azure AD hesla ochrany místních – nejčastější dotazy

|     |
| --- |
| Ochrana hesel Azure AD je funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="general-questions"></a>Obecné otázky

**Otázka: kdy dosáhne ochrana hesel Azure AD obecné dostupnosti (GA)?**

Ještě jsme oznámili ještě datum všeobecné dostupnosti.

**Otázka: je místní ochrana heslem služby Azure AD nepodporuje v jiných veřejných cloudech?**

Ne – ochrana heslem v místním Azure AD je podporován pouze ve veřejném cloudu.

**Otázka: Jak můžu použít výhody ochrany hesla Azure AD na určitou podskupinu uživatelů v místním?**

Nepodporuje se. Jakmile nasazená a aktivovaná, ochrana hesel Azure AD není rozlišení – všichni uživatelé obdrží stejné výhody.

**Otázka: je podporována instalace ochrany hesla Azure AD souběžně s jinými produkty pomocí filtru hesla?**

Ano. Podpora více knihovny DLL filtru registrované heslo je základní funkcí Windows a nezávislé na ochrana hesel Azure AD. Všechny knihovny DLL filtru registrované hesla musí souhlasit, před přijetím heslo.

**Otázka: Proč se DFSR vyžaduje k replikování adresáře sysvol?**

Služba replikace souborů (technologie předchůdce do DFSR) má mnoho známé problémy a není zcela podporována v novějších verzích Windows Server Active Directory. Nulové testování ochrana hesel Azure AD se nevytvoří FRS nakonfigurované domény.

Další informace najdete v následujících článcích:

[Platí pro migraci replikace adresáře sysvol pro službu DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[Je konec Nigh pro službu DRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**Otázka: Proč je třeba restartovat počítač pro instalaci nebo upgrade softwaru agenta řadiče domény?**

Tento požadavek je způsobeno jádra Windows chování.

**Otázka: existuje žádný způsob, jak nakonfigurovat agenta do řadiče domény použít konkrétní proxy server?**

Ne.

## <a name="next-steps"></a>Další postup

Pokud máte dotaz místní služby Azure AD hesla ochrany, který jsem zde nenalezl odpověď, odešlete názor níže – Děkujeme!

[Nasazení ochrany hesel Azure AD](howto-password-ban-bad-on-premises-deploy.md)
