---
title: Azure Multi-Factor Authentication – jak to funguje
description: Azure Multi-Factor Authentication pomáhá chránit přístup k datům a aplikacím a současně plní požadavky uživatelů na jednoduchý proces přihlašování.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/20/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 709fab070533984f94a72ff2136a8bc32fbe6ec6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33865931"
---
# <a name="how-azure-multi-factor-authentication-works"></a>Jak funguje Azure Multi-Factor Authentication
Zabezpečení dvoustupňové ověření spočívá v jeho vrstveného přístupu. Porušení zabezpečení několika faktory ověřování uvede významné výzvu pro útočníky. I v případě, že útočník dokázal další heslo uživatele, je zbytečné bez nutnosti důvěryhodné zařízení u sebe. 

![Ověření](./media/concept-mfa-howitworks/howitworks.png)

Azure Multi-Factor Authentication pomáhá chránit přístup k datům a aplikacím a současně plní požadavky uživatelů na jednoduchý proces přihlašování.  Poskytuje dodatečné zabezpečení vyžadováním druhou podobu ověřování a zajišťuje silné ověřování přes celou řadu možností snadno ověření.


## <a name="methods-available-for-two-step-verification"></a>Dostupné metody pro dvoustupňové ověření
Když se uživatel přihlásí, je uživateli odeslána dalšího ověření.  Následuje seznam metod, které lze použít pro tento druhý ověření.

| Metoda ověření | Popis |
| --- | --- |
| Telefonní hovor |Volání je umístěn na registrované Telefon uživatele. Uživatel zadá kód PIN, pokud potřeby pak stiskne klávesu #. |
| Textová zpráva |Odeslána textová zpráva na mobilní telefon uživatele s šestimístný kód. Uživatel zadá tento kód na stránce přihlášení. |
| Oznámení mobilní aplikace |Žádost o ověření posílá Smartphone uživatele. Uživatel zadá kód PIN, v případě potřeby pak vybere **ověřte** na mobilní aplikaci. |
| Ověřovací kód z mobilní aplikace |Mobilní aplikace, která běží na uživatele Smartphone, zobrazí ověřovací kód, který změní každých 30 sekund. Najde poslední kód a přejde na stránku přihlášení uživatele. |
| Tokeny OATH třetích stran | Azure Multi-Factor Authentication Server může být nastaven na přijímání metody ověřování třetích stran. |

Azure Multi-Factor Authentication poskytuje metody volitelný ověření pro cloud a serveru. Můžete zvolit, které metody jsou k dispozici pro vaše uživatele: telefonní hovor, text, oznámení aplikaci nebo aplikaci kódy. Další informace najdete v tématu [volitelný ověření metody](howto-mfa-mfasettings.md#selectable-verification-methods).

## <a name="next-steps"></a>Další postup

- Přečtěte si informace o různých [verze a spotřeba metody pro ověřování Azure Multi-Factor Authentication](concept-mfa-licensing.md)

- Vyberte, jestli chcete nasadit Azure MFA [v cloudu nebo místně](concept-mfa-whichversion.md)

- Přečtěte si odpovědi pro [nejčastější dotazy](multi-factor-authentication-faq.md)