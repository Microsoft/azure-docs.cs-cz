---
title: Přehled aplikace Microsoft Authenticator – Azure Active Directory | Microsoft Docs
description: Další informace o aplikaci Microsoft Authenticator, včetně toho, co to je, jak to funguje, a jaké informace jsou uvedeny v této části obsahu.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 01/24/2019
ms.author: lizross
ms.openlocfilehash: fa37ed942158ecc0f58aa5884ff93ce27bc42745
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55751068"
---
# <a name="what-is-the-microsoft-authenticator-app"></a>Co je aplikace Microsoft Authenticator?
Aplikace Microsoft Authenticator přispívá k přihlášení k účtům používáte dvojúrovňového ověřování. Dvoufaktorové ověření vám umožní přístup k účtům bezpečněji, zejména při zobrazování citlivé informace. Protože hesla mohou být vymazány, někdo je ukradne nebo ohrožení zabezpečení, dvojúrovňového ověřování je krok dodatečné zabezpečení, která pomáhá chránit váš účet kvůli tomu je těžší jinými lidmi, kteří možnost proniknout.

>[!Important]
>Aplikace Microsoft Authenticator funguje s každou aplikaci, která používá dvojúrovňového ověřování a jakýkoli účet, který podporuje podle času jednorázového hesla (TOTP) normami.

Můžete použít aplikaci Microsoft Authenticator několika různými způsoby, včetně:

- Poskytnutí výzvy pro druhé metody ověřování po přihlášení pomocí uživatelského jména a hesla.

- Zajištění přihlášení bez hesla, vaše uživatelské jméno a vašeho mobilního zařízení pomocí otisků prstů, rozpoznávání tváře nebo PIN kód.

>[!Important]
>Tento obsah je určený pro uživatele. Pokud jste správce, najdete další informace o nastavení a správě vašeho prostředí Azure Active Directory (Azure AD) v [dokumentaci k Azure Active Directory](https://docs.microsoft.com/azure/active-directory).<br><br>Pokud máte potíže s přihlášením k účtu, najdete v článku [když nemůžete se přihlásit ke svému účtu Microsoft](https://support.microsoft.com/help/12429) nápovědu.  Získat další informace o tom, jak postupovat, když se zobrazí ["Tento účet Microsoft neexistuje"](https://support.microsoft.com/help/13811) zpráva při pokusu o přihlášení k účtu Microsoft.

## <a name="terminology"></a>Terminologie
|Označení|Popis|
|----|-----------|
|Dvoufaktorové ověřování |Proces ověření, který vyžaduje, abyste konkrétně používají pouze dva druhy informací o ověření, jako jsou hesla a PIN kód. Aplikace Microsoft Authenticator podporuje i standardní dvojúrovňového ověřování a přihlašování.|
|Multi-Factor Authentication (MFA)|Všechny dvojúrovňového ověřování je ověřování službou Multi-Factor Authentication, které vyžadují, abyste pomocí *alespoň* dva druhy informací o ověření, založené na požadavcích vaší organizace.|
|Účet Microsoft (také nazývané, MSA)|Můžete vytvořit vlastní osobní účty, a získat přístup k vašim produktům Microsoft orientované příjemce cloudové služby, jako je Outlook, OneDrive, Xbox LIVE nebo Office 365. Váš účet Microsoft se vytvoří a uloží v Microsoft systémem identit uživatelů účtu, který se spouští společností Microsoft.|
|Pracovní nebo školní účet|Vaše organizace vytvoří pracovní nebo školní účet (například alain@contoso.com) umožňuje přístup k interní a potenciálně omezeným prostředkům, jako je například Microsoft Azure, Windows Intune a Office 365.|
|Ověřovací kód|Šestimístný kód, který se zobrazí v ověřovací aplikaci, v rámci každé přidání účtu. Ověřovací kód, který změní každých 30 sekund brání pomocí kódu více než jednou. Tím se taky říká jednorázové heslo (OTP).|

## <a name="how-two-factor-verification-works-with-the-app"></a>Jak funguje dvojúrovňového ověřování v aplikaci
Dvoufaktorové ověřování funguje s aplikací Microsoft Authenticator následujícími způsoby:

- **Oznámení.** Zadejte uživatelské jméno a heslo do zařízení budete přihlášení pro váš pracovní nebo školní účet nebo osobní účet Microsoft, a potom odešle oznámení s výzvou k aplikaci Microsoft Authenticator **schválit přihlášení**. Zvolte **schválit** Pokud pokus o přihlášení. Jinak klikněte na tlačítko **Odepřít**. Pokud se rozhodnete **Odepřít**, můžete také označit žádosti jako podvodné.

- **Ověřovací kód.** Zadejte uživatelské jméno a heslo do zařízení budete přihlášení pro váš pracovní nebo školní účet nebo osobní účet Microsoft a zkopírujte související ověřovací kód z **účty** obrazovky společnosti Microsoft Ověřovací aplikaci. Ověřovací kód, který se také označuje jako ověřování jednorázovým heslem (OTP).

- **Přihlašování.** Zadejte svoje uživatelské jméno do zařízení budete přihlášení pro váš pracovní nebo školní účet nebo osobní účet Microsoft a pak ověřte, že je můžete pomocí otisku prstu, rozpoznávání tváře nebo PIN kód pomocí vašeho mobilního zařízení. Pro tuto metodu není nutné zadat heslo.

### <a name="whether-to-use-your-devices-biometric-capabilities"></a>Jestli se má použít biometrické možnosti vašeho zařízení
Pokud používáte PIN k dokončení procesu ověřování, můžete nastavit aplikaci Microsoft Authenticator pro náhradní použití (biometrika) funkce rozpoznávání obličeje nebo otisků prstů vašeho zařízení. Tento parametr můžete nastavit až při prvním použití aplikace authenticator se ověřit svůj účet tak, že vyberete možnost použít vaše možnosti biometrických zařízení jako identifikace místo PIN kódu.

## <a name="who-decides-if-you-use-this-feature"></a>Který rozhodne, pokud pomocí této funkce?
V závislosti na typu účtu vaší organizace rozhodnout, že je nutné použít dvojúrovňové ověřování, nebo je možné rozhodnout sami.

- **Pracovní nebo školní účet.** Pokud používáte pracovní nebo školní účet (třeba alain@contoso.com), je to pro vaši organizaci, zda je nutné použít dvojúrovňové ověřování, spolu s konkrétní ověřovací metody. Další informace o přidávání svůj pracovní nebo školní účet do aplikace Microsoft Authenticator, naleznete v tématu [přidat pracovní nebo školní účty](microsoft-authenticator-app-add-work-account.md).

- **Osobní účet Microsoft.** Můžete také nastavit dvoufaktorového ověřování pro osobní účty Microsoft (třeba alain@outlook.com). Další informace o přidání vašeho osobního účtu Microsoft naleznete v tématu 

- **Účet od jiných výrobců.** Můžete také nastavit dvoufaktorového ověřování pro vaše účty od jiných výrobců (například alain@gmail.com). Vaše účty jiných společností než Microsoft nemusí použít výraz, dvojúrovňové ověřování, ale by měla být schopna najít funkce v rámci **zabezpečení** nebo **přihlášení** nastavení. Aplikace Microsoft Authenticator funguje s všechny účty, které podporují TOTP standardy.

## <a name="in-this-section"></a>V tomto oddílu

|Článek |Popis |
|------|------------|
|[Stažení a instalace aplikace](microsoft-authenticator-app-how-to.md)|Popisuje, kde a jak získat a nainstalovat aplikaci Microsoft Authenticator pro zařízení s Androidem a iOS.|
|[Přidejte svůj pracovní nebo školní účet](microsoft-authenticator-app-add-work-account.md)|Popisuje, jak přidat různé pracovní nebo školní a osobní účty do aplikace Microsoft Authenticator.|
|[Přidat osobního účtu Microsoft](microsoft-authenticator-app-add-personal-account.md)|Popisuje, jak přidat váš osobní účet Microsoft pro aplikaci Microsoft Authenticator.|
|[Přidat účet jiného subjektu než Microsoft](microsoft-authenticator-app-add-non-microsoft-account.md)|Popisuje, jak přidat své účty jiných společností než Microsoft k aplikaci Microsoft Authenticator.|
|[Ručně přidejte vaše účty](microsoft-authenticator-app-add-account-manual.md)|Pokud nemůžete poskytnutý kód QR naskenovat a popisuje, jak ručně přidat do aplikace Microsoft Authenticator, vaše účty.|
|[Přihlášení pomocí aplikace](microsoft-authenticator-app-phone-signin-faq.md)|Popisuje, jak se přihlásit k různým účtům, pomocí aplikace Microsoft Authenticator.|
|[Zálohování a obnovení přihlašovacích údajů k účtu](microsoft-authenticator-app-backup-and-recovery.md)| Poskytuje informace o tom, jak zálohovat a obnovit přihlašovací údaje k účtu pomocí aplikace Microsoft Authenticator.|
|[Nejčastější dotazy k aplikaci Microsoft Authenticator](microsoft-authenticator-app-faq.md)|Poskytuje odpovědi na nejčastější dotazy ohledně této aplikace.|
