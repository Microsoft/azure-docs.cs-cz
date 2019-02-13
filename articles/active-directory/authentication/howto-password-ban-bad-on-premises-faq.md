---
title: Místní ochrana hesel Azure AD – nejčastější dotazy
description: Místní ochrana hesel Azure AD – nejčastější dotazy
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a4528cde92c5f738fa3fa7f4a649d84b1e79431
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175944"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Verze Preview: Azure AD ochrana heslem v místním – nejčastější dotazy

|     |
| --- |
| Ochrana hesel Azure AD je funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="general-questions"></a>Obecné otázky

**Otázka: Při dosažení obecné dostupnosti (GA) ochranu hesel služby Azure AD**

Verze GA plánujeme přidat Q1 CY2019 (konce března 2019). Děkujeme, že jste pro každého uživatele, který poskytl zpětnou vazbu na funkci tak, aby datum - Vážíme si ho!

**Otázka: Je místní ochrana hesel Azure AD nepodporuje v jiných veřejných cloudech?**

Ne – místní ochrana hesel Azure AD je podporován pouze ve veřejném cloudu.

**Otázka: Jak můžu použít výhody ochrany hesel služby Azure AD na určitou podskupinu uživatelů v místním?**

Nepodporuje se. Jakmile nasazená a aktivovaná, ochrana hesel Azure AD není rozlišení – všichni uživatelé obdrží stejné výhody.

**Otázka: Je podporováno nainstalovat ochranu hesel služby Azure AD souběžně s jinými produkty pomocí filtru hesla?**

Ano. Podpora více knihovny DLL filtru registrované heslo je základní funkcí Windows a nezávislé na ochranu hesel služby Azure AD. Všechny knihovny DLL filtru registrované hesla musí souhlasit, před přijetím heslo.

**Otázka: Proč je DFSR se vyžaduje k replikování adresáře sysvol?**

Služba replikace souborů (technologie předchůdce do DFSR) má mnoho známé problémy a není zcela podporována v novějších verzích Windows Server Active Directory. Nulové testování ochrana hesel Azure AD se nevytvoří FRS nakonfigurované domény.

Další informace najdete v následujících článcích:

[Platí pro migraci replikace adresáře sysvol pro službu DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[Je konec Nigh u služby FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**Otázka: Proč je třeba restartovat počítač pro instalaci nebo upgrade softwaru agenta řadiče domény?**

Tento požadavek je způsobeno jádra Windows chování.

**Otázka: Existuje nějaký způsob, jak nakonfigurovat agenta do řadiče domény použít konkrétní proxy server?**

Ne.

## <a name="next-steps"></a>Další postup

Pokud máte dotaz ochrana hesel Azure AD s místními, který jsem zde nenalezl odpověď, odešlete názor níže – Děkujeme!

[Nasazení ochrany hesel Azure AD](howto-password-ban-bad-on-premises-deploy.md)
