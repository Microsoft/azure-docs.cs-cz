---
title: Přihlášení pomocí aplikace Microsoft Authenticator – Azure AD
description: Pomocí aplikace Microsoft Authenticator se přihlaste ke svému pracovnímu nebo školnímu účtu nebo k osobním účtům společnosti Microsoft a jiným, a to Pomocí dvojúrovňového ověřování nebo přihlášení k telefonnímu programu.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 03/12/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 0fd0aa19d52adb4bf06c9d21d4182e27497a531b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471762"
---
# <a name="sign-in-to-your-accounts-using-the-microsoft-authenticator-app"></a>Přihlaste se ke svým účtům pomocí aplikace Microsoft Authenticator.

Aplikace Microsoft Authenticator vám pomůže přihlásit se k vašim účtům, pokud použijete dvojúrovňové ověřování. Dvojúrovňové ověřování vám pomůže zajistit bezpečnější přístup k účtům, zejména při prohlížení citlivých informací. Vzhledem k tomu, že hesla můžou být zapomenutá, odcizená nebo ohrožená, je dvoustupňové ověřování další krok zabezpečení, který pomáhá chránit váš účet tím, že je těžší, aby ostatní uživatelé mohli rušit.

Aplikaci Microsoft Authenticator můžete použít několika způsoby, mezi které patří:

- Po přihlášení pomocí uživatelského jména a hesla získáte výzvu k zadání druhé metody ověřování.

- Přihlaste se, aniž byste museli používat heslo, a to pomocí svého uživatelského jména a mobilního zařízení pomocí otisku prstu, obličeje nebo PIN kódu.

  >[!Important]
  >Tato metoda přihlašování k telefonu funguje jenom u vašich pracovních nebo školních a osobních účtů Microsoft. Vaše účty jiných společností než Microsoft vyžadují, abyste používali standardní proces ověřování dvou faktorů.

## <a name="prerequisites"></a>Předpoklady

Než budete moct použít aplikaci Microsoft Authenticator, musíte:

 1. Stáhněte a nainstalujte aplikaci Microsoft Authenticator. Pokud jste to ještě neudělali, přečtěte si téma [Stažení a instalace aplikace](user-help-auth-app-download-install.md).

 2. Přidejte do aplikace Microsoft Authenticator své pracovní nebo školní účty, osobní a třetí strany. Podrobný postup najdete v tématu [Přidání pracovního nebo školního účtu](user-help-auth-app-add-work-school-account.md), [Přidání osobních účtů](user-help-auth-app-add-personal-ms-account.md)a [Přidání účtů jiných společností než Microsoft](user-help-auth-app-add-non-ms-account.md).

## <a name="turn-on-and-use-phone-sign-in-for-your-work-or-school-account"></a>Zapnutí a používání přihlášení telefonem pro svůj pracovní nebo školní účet

Přihlášení telefonem je typ ověření se dvěma kroky. Vaši identitu musíte ještě ověřit tím, že zadáte věc, kterou máte, ale přihlásíte se, ale telefonní přihlášení vám umožní přeskočit zadání hesla k účtu a na vašem mobilním zařízení provést všechna ověření identity.

Než budete moct zapnout telefonický přihlašování, musíte zapnout dvojúrovňové ověřování. Další informace o tom, jak zapnout dvojúrovňové ověřování pro účet, najdete v tématu [Přidání pracovního nebo školního účtu](user-help-auth-app-add-work-school-account.md) a [Přidání vašich osobních účtů](user-help-auth-app-add-personal-ms-account.md).

Přihlášení telefonem je dostupné jenom v zařízeních s iOS a Androidem, na kterých běží Android 6,0 nebo vyšší.

### <a name="turn-on-phone-sign-in"></a>Zapnout přihlašování telefonem

Otevřete aplikaci Microsoft Authenticator, pokračujte na svůj pracovní nebo školní účet a zapněte přihlašování telefonem.

- **Po klepnutí na dlaždici účtu** se zobrazí zobrazení celé obrazovky účtu. Pokud se zobrazí **přihlašování telefonem** , znamená to, že jste plně nastavili, abyste se přihlásili bez hesla. Pokud se zobrazí možnost **Povolit přihlašování telefonem**, klepněte na něj a zapněte přihlášení k telefonu.
- **Pokud jste už aplikaci používali pro dvoustupňové ověřování**, můžete klepnutím na dlaždici účet zobrazit zobrazení celého účtu na celé obrazovce. Pak klepnutím na **Povolit přihlášení k telefonu** zapněte přihlášení telefonem.
- **Pokud svůj pracovní nebo školní účet nemůžete najít** na obrazovce **účty** aplikace, znamená to, že jste ho ještě nepřidali do aplikace. Přidejte svůj pracovní nebo školní účet podle kroků v [nápovědě k přidání pracovního nebo školního účtu](user-help-auth-app-add-work-school-account.md).

> [!NOTE]
> Microsoft nepodporuje kombinaci registrace zařízení a ověřování založeného na certifikátech v rámci ověřovatele v iOS. Místo toho musí před přihlášením uživatel zařízení zaregistrovat ručně prostřednictvím nastavení ověřovatele.

Po zapnutí telefonického přihlášení se můžete přihlásit jenom pomocí aplikace Microsoft Authenticator. Jak na to:

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu.

    Po zadání uživatelského jména se zobrazí obrazovka **schválit přihlášení** , která vám ukáže dvoumístné číslo a požádá vás, abyste se přihlásili prostřednictvím aplikace Microsoft Authenticator. Pokud nechcete používat tuto metodu přihlašování, můžete **místo toho vybrat použít heslo** a přihlásit se pomocí hesla.

    ![Schválení přihlašovacího pole v počítači](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in.png)

2. Otevřete oznámení nebo aplikaci Microsoft Authenticator na svém zařízení a potom klepněte na číslo odpovídající číslu, které vidíte na přihlašovací obrazovce pro **schválení** vašeho počítače.

    ![Schválení přihlašovacího pole na zařízení](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in-numbers.png)

3. Zvolením možnosti **schválit** poznáte pokus o přihlášení. V opačném případě vyberte **Odepřít**.

4. K dokončení ověřování použijte PIN nebo klíč biometriky vašeho telefonu.

## <a name="turn-on-and-use-phone-sign-in-for-your-personal-microsoft-accounts"></a>Zapnutí a používání přihlášení telefonem pro osobní účty Microsoft

Pro osobní účet Microsoft můžete zapnout přihlášení telefonem, například účet, který používáte pro přihlášení k Outlook.com, Xbox nebo Skypu.

>[!NOTE]
>Z důvodu ochrany vašeho účtu vyžaduje aplikace Microsoft Authenticator na svém zařízení PIN nebo biometrické zámek. Pokud váš telefon zůstane odemčený, před zapnutím přihlášení k telefonu vyžaduje aplikace, abyste si nastavili zámek zabezpečení.

### <a name="turn-on-phone-sign-in"></a>Zapnout přihlašování telefonem 

Otevřete aplikaci Microsoft Authenticator, pokračujte na svůj pracovní nebo školní účet a zapněte přihlašování telefonem.

- **Po klepnutí na dlaždici účtu** se zobrazí zobrazení celé obrazovky účtu. Pokud se zobrazí **přihlašování telefonem** , znamená to, že jste plně nastavili, abyste se přihlásili bez hesla. Pokud se zobrazí možnost **Povolit přihlašování telefonem**, klepněte na něj a zapněte přihlášení k telefonu.
- **Pokud už aplikaci používáte pro dvoustupňové ověřování**, můžete klepnutím na dlaždici účet zobrazit zobrazení celého účtu na celé obrazovce. Pak klepnutím na **Povolit přihlášení k telefonu** zapněte přihlášení telefonem.
- **Pokud váš účet nemůžete najít** na obrazovce **účty** aplikace, znamená to, že jste ho ještě nepřidali do aplikace. Pomocí postupu v článku [Přidání osobních účtů Microsoft](user-help-auth-app-add-personal-ms-account.md) přidejte své osobní účet Microsoft.

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>Přihlaste se ke svému účtu pomocí přihlášení telefonem.

1. Přejít na osobní účet Microsoft přihlašovací stránku a místo zadání hesla vyberte odkaz **použít aplikaci Microsoft Authenticator místo toho** .

    Microsoft pošle oznámení na váš telefon.

2. Schvalte oznámení.

## <a name="sign-in-using-two-factor-verification-for-your-account"></a>Přihlaste se Pomocí dvojúrovňového ověřování pro váš účet.

Standardní metoda dvojúrovňové ověřování vyžaduje zadání uživatelského jména a hesla do zařízení, ke kterému se přihlašujete, a pak zvolte, jestli aplikace Microsoft Authenticator obdrží oznámení nebo jestli chcete zkopírovat ověřovací kód z ověřovací aplikace. Na zařízení s Androidem se ověřovací kódy dají najít na obrazovce **účty** . Na zařízení se systémem iOS se tyto ověřovací kódy dají najít na obrazovce **účty** nebo zobrazení celé obrazovky účtu v závislosti na typu účtu. Při přidávání účtu do aplikace Microsoft Authenticator zapnete dvojúrovňové ověřování pro váš účet.

>[!Note]
>Pokud nevidíte svůj pracovní nebo školní účet nebo osobní účet na obrazovce **účty** aplikace Microsoft Authenticator, znamená to, že jste účet nepřidali do aplikace Microsoft Authenticator. Pokud chcete přidat svůj účet, přečtěte si téma [Přidání pracovního nebo školního účtu](user-help-auth-app-add-work-school-account.md) nebo [Přidání osobních účtů](user-help-auth-app-add-personal-ms-account.md).

Kroky nezbytné pro přihlášení k pracovnímu nebo školnímu účtu nebo k vašemu osobnímu účtu pomocí různých metod dvojúrovňového ověřování najdete v tématu [přihlášení pomocí dvoustupňového ověřování nebo informací o zabezpečení](user-help-sign-in.md).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

| Otázka | Řešení |
| -------- | -------- |
| Jak se přihlašujem pomocí mého telefonu bezpečnější než zadání hesla? | Dnešní uživatelé se přihlásí k webům nebo aplikacím pomocí uživatelského jména a hesla. Bohužel můžou být hesla ztracená, odcizená nebo považovat za hackeři.<br><br>Po nastavení Microsoft Authenticator aplikace se na telefonu vytvoří klíč, který odemkne váš účet chráněný PIN kódem nebo biometrickým zámkem. Tento klíč se pak použije k prokázání vaší identity během přihlašování.<br><br>**Důležité upozornění**<br>Vaše data se používají jenom k místní ochraně klíče. Nikdy se neposílá ani neukládá do cloudu. |
| Nahrazuje se telefonický ověřením dva kroky? Mám ho vypnout? | Přihlášení telefonem je typ dvou ověření kroku, kdy se oba kroky vyskytují v mobilním zařízení. Měli byste zajistit, aby bylo zapnuté dvoustupňové ověřování, které vám pomůžou zajistit další zabezpečení vašeho účtu. |
| Pokud je u svého účtu zapnuté dvoustupňové ověřování, musím ke schválení dvou oznámení? | No. Přihlášení k vašemu účet Microsoft pomocí telefonu se také počítá jako dvoustupňové ověřování, takže není nutné žádné druhé schválení. |
| Co když mi přijdem telefon nebo ho už nemáte? Návody přistupovat k mému účtu? | Na přihlašovací stránce můžete vždycky vybrat odkaz použít heslo místo toho a přejít zpátky k používání hesla. Pokud ale používáte dvoustupňové ověřování, budete k ověření vaší identity pořád potřebovat druhou metodu.<br><br>**Důležité upozornění**<br>Důrazně doporučujeme, abyste se ujistili, že máte k vašemu účtu přiřazenou více než jednu, aktuální metodu ověřování.<br><br>Metody ověřování pro osobní účty můžete spravovat ze stránky [nastavení zabezpečení](https://account.live.com/proofs/manage) . Pro pracovní nebo školní účty můžete přejít na stránku [Další ověření zabezpečení](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) vaší organizace nebo na stránku **zachovat zabezpečený účet** , pokud správce zapnul bezpečnostní údaje. Další informace o bezpečnostních informacích najdete v tématu [Přehled informací o zabezpečení (Preview)](./security-info-setup-signin.md).<br><br>Pokud nemůžete spravovat metody ověřování, musíte se obrátit na správce. |
| Návody ukončit používání této funkce a vrátit se k používání mého hesla? | U osobních účtů zaškrtněte políčko **použít místo toho heslo** odkaz při přihlášení. Vaše nejnovější volba se pamatuje a při příštím přihlášení se nabídne ve výchozím nastavení. Pokud se budete chtít kdykoli vrátit k používání telefonického přihlášení, vyberte při přihlášení odkaz **použít aplikaci** .<br><br>Pro pracovní nebo školní účty musíte buď zrušit registraci zařízení na stránce **Nastavení** aplikace Microsoft Authenticator, nebo zařízení zakázat v oblasti **aktivity & zařízení** vašeho profilu. Další informace o zakázání zařízení z vašeho profilu najdete v tématu [aktualizace informací o profilu a účtu na portálu moje aplikace](./my-account-portal-devices-page.md#disable-a-device). |
| Proč pro přihlašování telefonem nemůžem použít více než jeden pracovní nebo školní účet? | Telefon musí být zaregistrovaný do jednoho pracovního nebo školního účtu. Pokud chcete zapnout přihlášení k telefonu pro jiný pracovní nebo školní účet, musíte zrušit registraci účtu na tomto zařízení prostřednictvím stránky **Nastavení** . |
| Můžu se k počítači přihlásit pomocí telefonu? | Pro váš počítač doporučujeme přihlásit se pomocí Windows Hello ve Windows 10. Windows Hello vám umožní přihlašovat se pomocí svého obličeje, otisku prstu nebo PIN kódu. |

## <a name="next-steps"></a>Další kroky

- Pokud máte potíže s získáním ověřovacího kódu pro osobní účet Microsoft, přečtěte si část **problémy s ověřovacím kódem pro řešení potíží** v článku [účet Microsoft informace o zabezpečení & ověřovací kódy](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) .

- Pokud máte obecnější otázky týkající se aplikace, přečtěte si [Microsoft Authenticator Nejčastější dotazy](user-help-auth-app-faq.md) .

- Pokud chcete získat další informace o dvoustupňovém ověřování, přečtěte si téma [Nastavení mého účtu pro dvoustupňové ověřování](multi-factor-authentication-end-user-first-time.md) .

- Pokud chcete získat další informace o zabezpečení, přečtěte si téma [Přehled informací o zabezpečení (Preview)](./security-info-setup-signin.md) .