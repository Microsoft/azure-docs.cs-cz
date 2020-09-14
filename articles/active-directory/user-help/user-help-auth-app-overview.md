---
title: Co je aplikace Microsoft Authenticator? -Azure Active Directory | Microsoft Docs
description: Přečtěte si o aplikaci Microsoft Authenticator, včetně toho, jak je, jak funguje a jaké informace jsou součástí této části obsahu.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 7f4c09a32794e13b0e0041ee916401b714d8509e
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2020
ms.locfileid: "90055227"
---
# <a name="what-is-the-microsoft-authenticator-app"></a>Co je aplikace Microsoft Authenticator?

Aplikace Microsoft Authenticator vám pomůže přihlásit se k vašim účtům, pokud použijete dvojúrovňové ověřování. Dvojúrovňové ověřování vám pomůže zajistit bezpečnější přístup k účtům, zejména při prohlížení citlivých informací. Vzhledem k tomu, že hesla můžou být zapomenutá, odcizená nebo ohrožená, je dvoustupňové ověřování další krok zabezpečení, který pomáhá chránit váš účet tím, že je těžší, aby ostatní uživatelé mohli rušit.

Aplikaci Microsoft Authenticator můžete použít několika způsoby, mezi které patří:

- Po přihlášení pomocí uživatelského jména a hesla odpovězte na výzvu k ověření.

- Přihlaste se bez zadání hesla, pomocí přihlašovacího jména, aplikace ověřovatele a vašeho mobilního zařízení pomocí otisku prstu, obličeje nebo PIN kódu.

- Jako generátor kódu pro všechny ostatní účty, které podporují ověřovací aplikace.

> [!Important]
> Aplikace Microsoft Authenticator pracuje s jakýmkoli účtem, který používá dvojúrovňové ověřování a podporuje standardy založené na čase na základě hesla (TOTP).
>
>Tento článek je určený pro uživatele, kteří se pokoušejí stáhnout a používat aplikaci Microsoft Authenticator jako metodu ověření zabezpečení. Pokud jste správcem a hledáte informace o tom, jak zapnout přihlášení bez hesla pomocí ověřovací aplikace pro vaše zaměstnance a jiné účely, přečtěte si téma [Povolení přihlášení bez hesla pomocí aplikace Microsoft Authenticator (Preview)](../authentication/howto-authentication-passwordless-phone.md).

## <a name="terminology"></a>Terminologie

| Období|Popis|
| ----|-----------|
| Dvojúrovňové ověřování | Proces ověření, který vyžaduje, abyste konkrétně použili jenom dvě části ověřovacích informací, jako je heslo a PIN kód. Aplikace Microsoft Authenticator podporuje standardní dvojúrovňové ověřování a přihlašování se všemi hesly. |
| Multi-Factor Authentication (MFA) | Každé dvojúrovňové ověřování je Multi-Factor Authentication a vyžaduje, abyste v závislosti na požadavcích vaší organizace používali *aspoň* dvě části ověřovacích informací. |
| Účet Microsoft (označovaný také jako MSA) | Vytvoříte si vlastní osobní účty, abyste získali přístup k vašim zákaznickým produktům a cloudovým službám Microsoftu, jako je například Outlook, OneDrive, Xbox LIVE nebo Microsoft 365. Vaše účet Microsoft je vytvořená a uložená v systému účtu Microsoft Consumer identity, který je spuštěný Microsoftem. |
| Pracovní nebo školní účet | Vaše organizace vytvoří svůj pracovní nebo školní účet (například alain@contoso.com ), aby vám umožnil přístup k internímu a potenciálně omezenému zdroji, jako je Microsoft Azure, Windows Intune a Microsoft 365. |
| Ověřovací kód | Osmimístný kód, který se zobrazí v ověřovací aplikaci v rámci každého přidaného účtu. Ověřovací kód se změní každých 30 sekund, což někomu brání v použití kódu několikrát. Tato verze se označuje také jako JEDNORÁZOVé heslo. |

## <a name="how-two-factor-verification-works-with-the-app"></a>Jak dvojúrovňové ověřování funguje s aplikací

Dvojúrovňové ověřování funguje s aplikací Microsoft Authenticator následujícími způsoby:

- **Zveřejnění.** Zadejte své uživatelské jméno a heslo do zařízení, ke kterému se přihlašujete, pro svůj pracovní nebo školní účet nebo osobní účet Microsoft a pak Microsoft Authenticator Aplikace pošle oznámení s žádostí o **schválení přihlášení**. Zvolením možnosti **schválit** poznáte pokus o přihlášení. V opačném případě vyberte **Odepřít**. Pokud zvolíte **Odepřít**, můžete žádost označit jako podvodný.

- **Ověřovací kód.** Zadejte své uživatelské jméno a heslo do zařízení, ke kterému se přihlašujete, do svého pracovního nebo školního účtu nebo do svého osobního účet Microsoft a pak zkopírujte přidružený ověřovací kód na obrazovce **účty** aplikace Microsoft Authenticator. Ověřovací kód je také označován jako ověřování jednorázovým heslem (jednorázové heslo).

- **Přihlášení se neheslem.** Zadejte své uživatelské jméno do zařízení, ke kterému se přihlašujete, pro svůj pracovní nebo školní účet nebo osobní účet Microsoft a pak použijte své mobilní zařízení, abyste ověřili, že jste použili otisk prstu, obličej nebo PIN. Pro tuto metodu není nutné zadávat heslo.

### <a name="whether-to-use-your-devices-biometric-capabilities"></a>Jestli se mají používat biometrické možnosti zařízení

Pokud k dokončení procesu ověřování použijete PIN kód, můžete nastavit aplikaci Microsoft Authenticator, aby místo toho používala otisk prstu zařízení nebo rozpoznávání obličeje (biometrické). Tuto možnost můžete nastavit při prvním použití aplikace ověřovatele k ověření vašeho účtu, a to tak, že vyberete možnost použít pro zařízení biometrické možnosti jako identifikaci namísto PIN kódu.

## <a name="who-decides-if-you-use-this-feature"></a>Kdo se rozhodne, jestli tuto funkci používáte?

V závislosti na typu účtu může vaše organizace rozhodnout, že je nutné použít dvojúrovňové ověřování, nebo se můžete rozhodnout sami.

- **Pracovní nebo školní účet.** Pokud používáte pracovní nebo školní účet (například alain@contoso.com ), je to až do vaší organizace bez ohledu na to, jestli musíte použít dvojúrovňové ověřování společně s konkrétními metodami ověřování. Další informace o tom, jak do aplikace Microsoft Authenticator přidat svůj pracovní nebo školní účet, najdete v tématu [Přidání pracovních nebo školních účtů](user-help-auth-app-add-work-school-account.md).

- **Osobní účet Microsoft.** Pro osobní účty Microsoft (například) si můžete nastavit dvojúrovňové ověřování alain@outlook.com . Další informace o přidání osobních účet Microsoft najdete v tématu [Přidání osobních účtů](user-help-auth-app-add-personal-ms-account.md).

- **Bez účet Microsoft.** Můžete nastavit dvojúrovňové ověřování pro účty jiných společností než Microsoft (například alain@gmail.com ). Vaše účty jiných společností než Microsoft nemůžou použít termín, dvojúrovňové ověřování, ale měli byste být schopni najít funkci v rámci **zabezpečení** nebo nastavení **přihlášení** . Microsoft Authenticator aplikace funguje se všemi účty, které podporují standardy TOTP. Další informace o přidání účtů jiných společností než Microsoft najdete v tématu [Přidání účtů jiných společností než Microsoft](user-help-auth-app-add-non-ms-account.md).

## <a name="in-this-section"></a>V této části

| Článek | Popis |
| ------ | ------------ |
| [Stažení a instalace aplikace](user-help-auth-app-download-install.md) | Popisuje, kde a jak získat a nainstalovat aplikaci Microsoft Authenticator pro zařízení s Androidem a iOS. |
| [Přidat pracovní nebo školní účty](user-help-auth-app-add-work-school-account.md) | V této části najdete popis postupu přidání různých pracovních nebo školních a osobních účtů do aplikace Microsoft Authenticator. |
| [Přidání osobních účtů](user-help-auth-app-add-personal-ms-account.md) | Popisuje, jak přidat osobní účty Microsoft do aplikace Microsoft Authenticator. |
| [Přidání účtů jiných společností než Microsoft](user-help-auth-app-add-non-ms-account.md) | V této části najdete popis postupu přidání účtů mimo Microsoft do aplikace Microsoft Authenticator. |
| [Ruční přidání účtů](user-help-auth-app-add-account-manual.md) | V této části najdete popis postupu ručního přidání účtů do aplikace Microsoft Authenticator, pokud nemůžete zkontrolovat zadaný kód QR. |
| [Přihlášení pomocí aplikace](user-help-auth-app-sign-in.md) | Popisuje, jak se přihlásit k různým účtům pomocí aplikace Microsoft Authenticator.|
| [Zálohování a obnovení přihlašovacích údajů k účtu](user-help-auth-app-backup-recovery.md) | Poskytuje informace o tom, jak zálohovat a obnovit přihlašovací údaje k účtu pomocí aplikace Microsoft Authenticator. |
| [Nejčastější dotazy k aplikaci Microsoft Authenticator](user-help-auth-app-faq.md) | Poskytuje odpovědi na nejčastější dotazy ohledně této aplikace. |