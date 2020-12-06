---
title: Metoda ověřování aplikací Microsoft Authenticator – Azure Active Directory
description: Další informace o používání aplikace Microsoft Authenticator v Azure Active Directory k vylepšení a zabezpečení událostí přihlašování
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7309d9d32eba5cae350bbe8a1df325dfb2ab0c8
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744359"
---
# <a name="authentication-methods-in-azure-active-directory---microsoft-authenticator-app"></a>Metody ověřování v aplikaci Azure Active Directory-Microsoft Authenticator

Microsoft Authenticator aplikace poskytuje další úroveň zabezpečení pro pracovní nebo školní účet služby Azure AD nebo vaši účet Microsoft a je dostupná pro [Android](https://go.microsoft.com/fwlink/?linkid=866594) a [iOS](https://go.microsoft.com/fwlink/?linkid=866594). S aplikací Microsoft Authenticator se uživatelé můžou během přihlašování ověřovat heslem bez hesla nebo jako další možnost ověřování během samoobslužného resetování hesla (SSPR) nebo Multi-Factor Authenticationch událostí služby Azure AD.

Uživatelé mohou obdržet oznámení prostřednictvím mobilní aplikace, aby je schválili nebo zakázali, nebo pomocí aplikace ověřovatele vygenerovat ověřovací kód OATH, který lze zadat v přihlašovacím rozhraní. Pokud povolíte jak oznámení, tak ověřovací kód, můžou uživatelé, kteří si zaregistrují ověřovací aplikaci, použít kteroukoli z metod k ověření jejich identity.

Pokud chcete použít aplikaci ověřovatele na příkazovém řádku, nikoli na kombinaci uživatelského jména a hesla, přečtěte si téma [Povolení přihlášení bez hesla pomocí aplikace Microsoft Authenticator (Preview)](howto-authentication-passwordless-phone.md).

> [!NOTE]
> Uživatelé nemají možnost registraci své mobilní aplikace, když povolují SSPR. Místo toho mohou uživatelé zaregistrovat svou mobilní aplikaci na [https://aka.ms/mfasetup](https://aka.ms/mfasetup) nebo jako součást registrace v rámci kombinovaného zabezpečení údajů na adrese [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

## <a name="passwordless-sign-in"></a>Přihlášení se neheslem

Namísto zobrazení výzvy k zadání hesla po zadání uživatelského jména se uživateli s povoleným přihlášením k telefonu z aplikace Microsoft Authenticator zobrazí zpráva, že v aplikaci klepne na číslo. Pokud je vybráno správné číslo, proces přihlášení je dokončen.

![Příklad přihlášení prohlížeče, které žádá uživatele o schválení přihlášení](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Tato metoda ověřování poskytuje vysokou úroveň zabezpečení a odstraňuje nutnost, aby uživatel při přihlašování zadali heslo. Přihlašování se neheslem pomocí aplikace Microsoft Authenticator je momentálně ve verzi Preview.

Pokud chcete začít přihlašovat bez hesla, přečtěte si téma [Povolení přihlášení bez hesla pomocí aplikace Microsoft Authenticator](howto-authentication-passwordless-phone.md).

## <a name="notification-through-mobile-app"></a>Oznámení prostřednictvím mobilní aplikace

Ověřovací aplikace může pomáhat zabránit neoprávněnému přístupu k účtům a zastavovat podvodné transakce tím, že do telefonu Smartphone nebo tabletu zadají oznámení. Uživatelé zobrazí oznámení a pokud je to legitimní, vyberte **ověřit**. V opačném případě mohou vybrat **Odepřít**.

![Snímek obrazovky s ukázkovým dotazem na přihlašovací aplikaci webového prohlížeče pro dokončení procesu přihlašování](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Pokud má vaše organizace zaměstnanci pracující v nebo na cestách v Číně, *oznámení prostřednictvím metody mobilní aplikace* na zařízeních s Androidem nefunguje v dané zemi nebo oblasti jako služby Google Play (včetně nabízených oznámení) v této oblasti blokované. Oznámení pro iOS ale funguje. Pro zařízení s Androidem by se měli pro tyto uživatele zpřístupnit alternativní metody ověřování.

## <a name="verification-code-from-mobile-app"></a>Ověřovací kód z mobilní aplikace

Ověřovací kód OATH lze vytvořit pomocí aplikace ověřovatele jako softwarový token. Po zadání uživatelského jména a hesla zadáte kód poskytnutý ověřovací aplikací do přihlašovacího rozhraní. Ověřovací kód poskytuje druhý způsob ověřování.

Uživatelé můžou mít kombinaci až pěti hardwarových tokenů OATH nebo ověřovacích aplikací, jako je například aplikace Microsoft Authenticator, nakonfigurovaná pro použití kdykoli.

> [!WARNING]
> Aby se zajistila nejvyšší úroveň zabezpečení pro Samoobslužné resetování hesla, když se pro resetování vyžaduje jenom jedna metoda, je ověřovací kód jedinou možností, kterou uživatelé mají k dispozici.
>
> Pokud jsou vyžadovány dvě metody, mohou uživatelé kromě jakékoli jiné povolené metody resetovat pomocí oznámení nebo ověřovacího kódu.

## <a name="next-steps"></a>Další kroky

Pokud chcete začít přihlašovat bez hesla, přečtěte si téma [Povolení přihlášení bez hesla pomocí aplikace Microsoft Authenticator](howto-authentication-passwordless-phone.md).

Přečtěte si další informace o konfiguraci metod ověřování pomocí [Microsoft Graph REST API beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).
