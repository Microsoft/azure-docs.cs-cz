---
title: Metoda ověřování bezpečnostních otázek – Azure Active Directory
description: Přečtěte si, jak používat bezpečnostní otázky v Azure Active Directory k vylepšení a zabezpečení přihlašovacích událostí.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7229dabd690e5932fbd297992e09782eda85a002
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744138"
---
# <a name="authentication-methods-in-azure-active-directory---security-questions"></a>Metody ověřování v Azure Active Directory – bezpečnostní otázky

Bezpečnostní otázky se během přihlašovací události nepoužívají jako metoda ověřování. Místo toho se bezpečnostní otázky dají použít během procesu samoobslužného resetování hesla (SSPR), abyste se ujistili, kdo jste. Účty správců nemůžou použít bezpečnostní otázky jako metodu ověřování pomocí SSPR.

Když se uživatelé registrují pro SSPR, zobrazí se jim výzva k výběru metod ověřování, které se mají použít. Pokud se rozhodnete použít bezpečnostní otázky, vyberou ze sady otázek výzvu a pak poskytnou vlastní odpovědi.

![Snímek obrazovky Azure Portal, který zobrazuje metody a možnosti ověřování pro bezpečnostní otázky](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> Bezpečnostní otázky se ukládají soukromě a bezpečně na objekt uživatele v adresáři a můžou na ně odpovědět jenom uživatelé během registrace. Neexistuje žádný způsob, jak může správce číst ani upravovat otázky a odpovědi uživatele.

Bezpečnostní otázky můžou být méně bezpečné než jiné metody, protože někteří lidé můžou znát odpovědi na otázky jiného uživatele. Pokud používáte bezpečnostní otázky v SSPR, doporučujeme je používat ve spojení s jinou metodou. Uživatel může být vyzván, aby k ověření své identity během procesu SSPR použili aplikaci Microsoft Authenticator nebo telefon, a zvolit bezpečnostní otázky pouze v případě, že k nim nemají telefon nebo registrované zařízení.

## <a name="predefined-questions"></a>Předdefinované otázky

Následující předdefinované bezpečnostní otázky jsou k dispozici pro použití jako metoda ověřování pomocí SSPR. Všechny tyto bezpečnostní otázky jsou přeloženy a lokalizovány do úplné sady Microsoft 365 jazyků na základě národního prostředí prohlížeče uživatele:

* V jakém městě jste se vyplnili jako první manželka/partner?
* V jakém městě se vaše rodiče naplnily?
* V jakém městě je vaše nejbližší na stejné úrovni jako v reálném čase?
* V jakém městě se narodil váš otce?
* V jakém městě jste měli první úlohu?
* V jakém městě se narodila vaše matka?
* V jakém městě jste byli na novém roce 2000?
* Jaký je příjmení vašeho oblíbeného učitele v rámci střední školy?
* Jaký je název školy, na kterou jste použili, ale neúčastnili jste se?
* Jaký je název místa, ve kterém jste si podrželi svou první svatbu?
* Co je prostřední jméno vašeho otce?
* Co je vaše oblíbená jídla?
* Co je jméno a příjmení babičkye jméno?
* Jaké je prostřední jméno matky?
* Kolik je měsíc a rok narození na stejné úrovni? (například listopad 1985)
* Jaké je druhé křestní jméno vašeho nejstaršího sourozence?
* Co je jméno a příjmení dědečkae dědečka?
* Jaké je prostřední jméno na stejné úrovni nejmladšího sourozence?
* Jakou školu jste se účastnili pro šestou třídu?
* Jak se jednalo o křestní jméno a příjmení nejlepšího přítele v dětství?
* Jak se jednalo o první a poslední jméno vašeho prvního
* Jak se jednalo o příjmení vašeho oblíbeného učitele na úrovni školy?
* Jaká byla značka a model vašeho prvního automobilu nebo motocyklu?
* Jak se jednalo o název první školy, na kterou jste navštěvovali?
* Jak se jednalo o název nemocnice, ve které jste se narodili?
* Jak se jednalo o název ulice vaší první dětství v dětství?
* Jak se jednalo o název vaší dětství Hero?
* Jaký byl název vašeho oblíbeného zvířete?
* Jaký byl název vaší první PET?
* Jaká byla vaše dětská Přezdívka?
* Jaký byl váš oblíbený sport ve vysoké škole?
* Jaká byla vaše první úloha?
* Jaké byly poslední čtyři číslice telefonního čísla v dětství?
* Když jste byli mladí, co byste chtěli mít v době, kdy jste ho rozrostli?
* Kdo je to nejvíc slavnýchá osoba, kterou jste dřív splnili?

## <a name="custom-security-questions"></a>Vlastní bezpečnostní otázky

Pro větší flexibilitu můžete definovat vlastní bezpečnostní otázky. Maximální délka vlastní bezpečnostní otázky je 200 znaků.

Vlastní bezpečnostní otázky nejsou automaticky lokalizovány jako výchozí bezpečnostní otázky. Všechny vlastní otázky se zobrazí ve stejném jazyce jako v uživatelském rozhraní pro správu, a to i v případě, že se národní prostředí prohlížeče uživatele liší. Pokud potřebujete lokalizované otázky, měli byste použít předdefinované otázky.

## <a name="security-question-requirements"></a>Požadavky na bezpečnostní otázku

Pro výchozí i vlastní bezpečnostní otázky platí následující požadavky a omezení:

* Minimální povolený počet znaků odpovědi je tři znaky.
* Maximální povolený počet znaků odpovědi je 40 znaků.
* Uživatelé nemůžou odpovědět na stejnou otázku více než jednou.
* Uživatelé nemůžou stejnou odpověď poskytnout na více než jednu otázku.
* Všechny znakové sady lze použít k definování otázek a odpovědí, včetně znaků Unicode.
* Počet definovaných otázek musí být větší nebo roven počtu otázek, které byly požadovány k registraci.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak začít, najdete v [kurzu samoobslužného resetování hesla (SSPR)][tutorial-sspr].

Další informace o konceptech SSPR najdete v tématu [Jak funguje Samoobslužné resetování hesla služby Azure AD][concept-sspr].

Přečtěte si další informace o konfiguraci metod ověřování pomocí [Microsoft Graph REST API beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[concept-sspr]: concept-sspr-howitworks.md
