---
title: Stažení a instalace aplikace Microsoft Authenticator – Azure AD
description: Stáhněte a nainstalujte aplikaci Microsoft Authenticator pro ověření vaší identity při použití dvojúrovňového ověřování.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.openlocfilehash: ba6f4d8d7c8bae73d998ee09de3adba7601e25b8
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76704710"
---
# <a name="download-and-install-the-microsoft-authenticator-app"></a>Stažení a instalace aplikace Microsoft Authenticator

>[!Important]
>Tento obsah je určený pro uživatele. Pokud jste správce, najdete další informace o nastavení a správě vašeho prostředí Azure Active Directory (Azure AD) v [dokumentaci k Azure Active Directory](https://docs.microsoft.com/azure/active-directory).

Aplikace Microsoft Authenticator vám pomůže přihlásit se k vašim účtům, pokud použijete dvojúrovňové ověřování. Dvojúrovňové ověřování vám pomůže zajistit bezpečnější přístup k účtům, zejména při prohlížení citlivých informací. Vzhledem k tomu, že hesla můžou být zapomenutá, odcizená nebo ohrožená, je dvoustupňové ověřování další krok zabezpečení, který pomáhá chránit váš účet tím, že je těžší, aby ostatní uživatelé mohli rušit.

Aplikaci Microsoft Authenticator můžete použít několika způsoby, mezi které patří:

- **Dvojúrovňové ověřování.** Standardní metoda ověřování, kde jeden z faktorů je vaše heslo. Po přihlášení pomocí uživatelského jména a hesla můžete buď schválit oznámení, nebo zadat poskytnutý ověřovací kód.

- **Přihlášení telefonem.** Verze dvojúrovňového ověřování, které vám umožní přihlásit se bez hesla, pomocí uživatelského jména a mobilního zařízení pomocí otisku prstu, obličeje nebo PIN kódu.

- **Generování kódu.** Jako generátor kódu pro všechny ostatní účty, které podporují ověřovací aplikace.

> [!Important]
> Aplikace Microsoft Authenticator pracuje s jakýmkoli účtem, který používá dvojúrovňové ověřování a podporuje standardy založené na čase na základě hesla (TOTP).
>
> Vaše organizace může vyžadovat, abyste použili ověřovací aplikaci pro přihlášení a přístup k datům a dokumentům vaší organizace. I když se vaše uživatelské jméno může zobrazit v aplikaci, účet není ve skutečnosti nastavený tak, aby fungoval jako metoda ověřování, dokud nedokončíte proces registrace. Další informace najdete v tématu [Přidání pracovního nebo školního účtu](user-help-auth-app-add-work-school-account.md).
> 
> [!NOTE]
> Pokud máte problémy s přihlášením k účtu, přečtěte si téma [nemůžete se přihlásit k vašemu účet Microsoft](https://support.microsoft.com/help/12429) , kde najdete nápovědu.  Získejte další informace o tom, co dělat, když se při pokusu o přihlášení ke účet Microsoft zobrazí zpráva ["tato účet Microsoft neexistují"](https://support.microsoft.com/help/13811) .

## <a name="download-and-install-the-app"></a>Stažení a instalace aplikace

Nainstalujte nejnovější verzi Microsoft Authenticator aplikace na základě vašeho operačního systému:

- **Google Android.** Na zařízení s Androidem můžete [Stáhnout a nainstalovat aplikaci Microsoft Authenticator](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator)tak, že přejdete na Google Play.

- **Apple iOS.** V zařízení se systémem Apple iOS [Stáhněte a nainstalujte aplikaci Microsoft Authenticator](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458)do obchodu App Store.

>[!Important]
>Pokud momentálně nepoužíváte mobilní zařízení, můžete aplikaci Microsoft Authenticator získat tak, že si pošlete odkaz ke stažení ze [stránky Microsoft Authenticator](https://www.microsoft.com/en-us/account/authenticator).

## <a name="next-steps"></a>Další kroky

Po stažení a instalaci aplikace je nutné přidat různé účty. Další informace:

- **Ověřovací aplikace** Stáhněte a použijte ověřovací aplikaci k získání oznámení o schválení nebo náhodně generovaného kódu schválení pro dvoustupňové ověřování nebo resetování hesla. Podrobné pokyny k nastavení a používání aplikace Microsoft Authenticator najdete v tématu [Nastavení bezpečnostních údajů pro použití ověřovací aplikace](security-info-setup-auth-app.md).

- **Text mobilního zařízení** Zadejte číslo mobilního zařízení a získejte text jako kód, který budete používat pro dvoustupňové ověřování nebo resetování hesla. Podrobné pokyny, jak ověřit identitu pomocí textové zprávy (SMS), najdete v tématu [Nastavení bezpečnostních údajů pro použití zasílání textových zpráv (SMS)](security-info-setup-text-msg.md).

- **Mobilní zařízení nebo pracovní telefonní hovor.** Zadejte číslo mobilního zařízení a získejte telefonní hovor pro dvoustupňové ověřování nebo resetování hesla. Podrobné pokyny, jak ověřit identitu pomocí telefonního čísla, najdete v tématu [Nastavení bezpečnostních údajů pro použití telefonních hovorů](security-info-setup-phone-number.md).

- **Bezpečnostní klíč** Zaregistrujte svůj bezpečnostní klíč kompatibilní s Microsoftem a použijte ho spolu s kódem PIN pro dvoustupňové ověřování nebo resetování hesla. Podrobné pokyny, jak ověřit identitu pomocí bezpečnostního klíče, najdete v tématu [Nastavení bezpečnostních údajů pro použití bezpečnostního klíče](security-info-setup-security-key.md).

- **E-mailová adresa** Zadejte svou pracovní nebo školní e-mailovou adresu, abyste získali e-mail pro resetování hesla. Tato možnost není k dispozici pro dvoustupňové ověřování. Podrobné pokyny k nastavení e-mailu najdete v tématu [Nastavení bezpečnostních údajů pro použití e-mailu](security-info-setup-email.md).

- **Bezpečnostní otázky.** Odpovězte na některé bezpečnostní otázky, které vytvořil správce vaší organizace. Tato možnost je k dispozici pouze pro resetování hesla a nikoli pro dvoustupňové ověřování. Podrobné pokyny k nastavení bezpečnostních otázek najdete v článku [Nastavení bezpečnostních informací pro použití bezpečnostních otázek](security-info-setup-questions.md) .
