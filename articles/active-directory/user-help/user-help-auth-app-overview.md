---
title: Co je aplikace Microsoft Authenticator? - Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Přečtěte si o aplikaci Microsoft Authenticator, včetně toho, co to je, jak funguje a jaké informace jsou zahrnuty v této části obsahu.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: c95ae2e48a7d5a3b9e9e28b1d4e14b670205c043
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77062281"
---
# <a name="what-is-the-microsoft-authenticator-app"></a>Co je aplikace Microsoft Authenticator?

Aplikace Microsoft Authenticator vám pomůže přihlásit se k vašim účtům, pokud používáte dvoufaktorové ověření. Dvoufaktorové ověření vám pomůže získat přístup k účtům bezpečněji, zejména při prohlížení citlivých informací. Vzhledem k tomu, že hesla mohou být zapomenuta, odcizena nebo ohrožena, je dvoufaktorové ověření dalším krokem zabezpečení, který pomáhá chránit váš účet tím, že ostatním lidem ztěžuje vloupání.

Aplikaci Microsoft Authenticator můžete používat několika způsoby, například:

- Po přihlášení pomocí uživatelského jména a hesla reagujte na výzvu k ověření.

- Přihlaste se bez zadání hesla pomocí uživatelského jména, ověřovací aplikace a mobilního zařízení pomocí otisku prstu, obličeje nebo KÓDU PIN.

- Jako generátor kódu pro všechny ostatní účty, které podporují ověřovací aplikace.

> [!Important]
> Aplikace Microsoft Authenticator funguje s libovolným účtem, který používá dvoufaktorové ověření a podporuje standardy jednorázového hesla (TOTP) založené na čase.
>
>Tento článek je určen pro uživatele, kteří se pokoušejí stáhnout a použít aplikaci Microsoft Authenticator jako metodu ověření zabezpečení. Pokud jste správce, který hledá informace o tom, jak zapnout přihlášení bez hesla pomocí aplikace Authenticator pro zaměstnance a další použití, přečtěte si informace [o povolení přihlášení bez hesla pomocí aplikace Microsoft Authenticator (preview).](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-phone)

## <a name="terminology"></a>Terminologie

| Označení|Popis|
| ----|-----------|
| Dvoufaktorové ověření | Ověřovací proces, který vyžaduje, abyste konkrétně používali pouze dva ověřovací údaje, například heslo a KÓD PIN. Aplikace Microsoft Authenticator podporuje standardní dvoufaktorové ověření i přihlášení bez hesla. |
| Multi-Factor Authentication (MFA) | Všechny dvoufaktorové ověřování je vícefaktorové ověřování, které vyžaduje použití *alespoň* dvou částí ověřovacích informací na základě požadavků vaší organizace. |
| Účet Microsoft (také nazývaný, MSA) | Můžete si vytvořit vlastní osobní účty, abyste získali přístup ke svým produktům a cloudovým službám Microsoftu orientovaným na spotřebitele, jako je Outlook, OneDrive, Xbox LIVE nebo Office 365. Váš účet Microsoft je vytvořen a uložen v systému účtů identit y microsoftu, který provozuje microsoft. |
| Pracovní nebo školní účet | Vaše organizace vytvoří váš pracovní nebo alain@contoso.comškolní účet (například), abyste měli přístup k interním a potenciálně omezeným prostředkům, jako je Microsoft Azure, Windows Intune a Office 365. |
| Ověřovací kód | Šestimístný kód, který se zobrazí v aplikaci authenticator pod každým přidaným účtem. Ověřovací kód se mění každých 30 sekund, aby někdo mohl kód používat vícekrát. Tento kód se také označuje jako jednorázový přístupový kód (OTP). |

## <a name="how-two-factor-verification-works-with-the-app"></a>Jak dvoufaktorové ověření funguje s aplikací

Dvoufaktorové ověření funguje s aplikací Microsoft Authenticator následujícími způsoby:

- **Oznámení.** Do zařízení, ke které se přihlašujete, zadejte své uživatelské jméno a heslo pro svůj pracovní nebo školní účet nebo osobní účet Microsoft a potom aplikace Microsoft Authenticator odešle oznámení s **žádostí**o schválení přihlášení . Pokud poznáváte pokus o přihlášení, zvolte **Schválit.** V opačném případě zvolte **Odepřít**. Pokud zvolíte **Možnost Odepřít**, můžete žádost označit také jako podvodnou.

- **Ověřovací kód.** Zadejte své uživatelské jméno a heslo do zařízení, ke které se přihlašujete pro svůj pracovní nebo školní účet nebo osobní účet Microsoft, a zkopírujte související ověřovací kód z obrazovky **Účty** aplikace Microsoft Authenticator. Ověřovací kód se také označuje jako ověřování jednorázových přístupových kódů (OTP).

- **Přihlášení bez hesla.** Zadejte své uživatelské jméno do zařízení, ke kterému se přihlašujete pro svůj pracovní nebo školní účet nebo osobní účet Microsoft, a pak pomocí svého mobilního zařízení ověřte, zda jste to vy, pomocí otisku prstu, obličeje nebo KÓDU PIN. Pro tuto metodu není nutné zadávat heslo.

### <a name="whether-to-use-your-devices-biometric-capabilities"></a>Zda používat biometrické funkce vašeho zařízení

Pokud k dokončení procesu ověřování použijete KÓD PIN, můžete místo toho nastavit aplikaci Microsoft Authenticator tak, aby místo toho používala možnosti rozpoznávání otisků prstů nebo rozpoznávání obličeje (biometrické) vašeho zařízení. Můžete to nastavit při prvním použití ověřovací aplikace k ověření účtu tak, že vyberete možnost použít biometrické funkce zařízení jako identifikaci místo kódu PIN.

## <a name="who-decides-if-you-use-this-feature"></a>Kdo rozhoduje o tom, zda tuto funkci používáte?

V závislosti na typu účtu může vaše organizace rozhodnout, že musíte použít dvoufaktorové ověření, nebo se můžete rozhodnout sami.

- **Pracovní nebo školní účet.** Pokud používáte pracovní nebo školní účet (například ), je na vaší organizaci, alain@contoso.comzda musíte použít dvoufaktorové ověření spolu s konkrétními metodami ověřování. Další informace o přidání pracovního nebo školního účtu do aplikace Microsoft Authenticator najdete v tématu [Přidání pracovních nebo školních účtů](user-help-auth-app-add-work-school-account.md).

- **Osobní účet Microsoft.** Můžete nastavit dvoufaktorové ověření pro své osobní účty Microsoft alain@outlook.com(například). Další informace o přidání osobního účtu Microsoft najdete v tématu [Přidání osobních účtů](user-help-auth-app-add-personal-ms-account.md).

- **Účet mimo Microsoft.** Můžete nastavit dvoufaktorové ověření pro účty jiných společností než alain@gmail.comMicrosoft (například). Vaše účty jiných společností než Microsoft nemusí používat termín dvoufaktorové ověření, ale měli byste být schopni najít funkci v nastavení **zabezpečení** nebo **přihlášení.** Aplikace Microsoft Authenticator funguje se všemi účty, které podporují standardy TOTP. Další informace o přidání účtů jiných společností než Microsoft naleznete v tématu [Přidání účtů jiných než Microsoft](user-help-auth-app-add-non-ms-account.md).

## <a name="in-this-section"></a>V tomto oddílu

| Článek | Popis |
| ------ | ------------ |
| [Stažení a instalace aplikace](user-help-auth-app-download-install.md) | Popisuje, kde a jak získat a nainstalovat aplikaci Microsoft Authenticator pro zařízení se systémem Android a iOS. |
| [Přidání pracovních nebo školních účtů](user-help-auth-app-add-work-school-account.md) | Popisuje, jak přidat různé pracovní nebo školní a osobní účty do aplikace Microsoft Authenticator. |
| [Přidání osobních účtů](user-help-auth-app-add-personal-ms-account.md) | Popisuje, jak přidat své osobní účty Microsoft do aplikace Microsoft Authenticator. |
| [Přidání účtů jiných než Microsoft](user-help-auth-app-add-non-ms-account.md) | Popisuje, jak přidat účty jiných společností než Microsoft do aplikace Microsoft Authenticator. |
| [Ruční přidání účtů](user-help-auth-app-add-account-manual.md) | Popisuje, jak ručně přidat své účty do aplikace Microsoft Authenticator, pokud se vám nedaří naskenovat poskytnutý QR kód. |
| [Přihlášení pomocí aplikace](user-help-auth-app-sign-in.md) | Popisuje, jak se přihlásit k různým účtům pomocí aplikace Microsoft Authenticator.|
| [Zálohování a obnovení přihlašovacích údajů k účtu](user-help-auth-app-backup-recovery.md) | Poskytuje informace o tom, jak zálohovat a obnovit přihlašovací údaje k účtu pomocí aplikace Microsoft Authenticator. |
| [Nejčastější dotazy k aplikaci Microsoft Authenticator](user-help-auth-app-faq.md) | Poskytuje odpovědi na nejčastější dotazy ohledně této aplikace. |
