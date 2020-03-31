---
title: Zálohování a obnovení účtů pomocí aplikace Microsoft Authenticator – Azure AD
description: Přečtěte si, jak zálohovat přihlašovací údaje zálohovaného účtu pomocí aplikace Microsoft Authenticator.
services: active-directory
author: curtand
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 2ac761355fc528fe3e4b4b5f05ee0a6e317ff99a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78297960"
---
# <a name="back-up-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>Zálohování a obnovení přihlašovacích údajů účtu pomocí aplikace Microsoft Authenticator

**To platí pro:**

- zařízení se systémem iOS, se systémem verze 5.7.0 a novějším

- Zařízení se systémem Android se systémem verze 6.6.0 a novějším

Aplikace Microsoft Authenticator zálohuje přihlašovací údaje účtu a související nastavení aplikace, jako je pořadí vašich účtů, do cloudu. Po zálohování můžete také použít aplikaci k obnovení informací na novém zařízení, což by mohlo zabránit uzamčení nebo opětovnému vytvoření účtů.

Každé umístění úložiště záloh vyžaduje, abyste měli jeden osobní účet Microsoft, zatímco iOS také vyžaduje, abyste měli účet iCloud. V tomto jediném umístění můžete mít uloženo více účtů. Můžete mít například osobní účet, pracovní nebo školní účet a osobní účet, který není microsoft, jako je Facebook, Google a tak dále.

> [!IMPORTANT]
> Uloženy jsou pouze vaše osobní přihlašovací údaje a přihlašovací údaje k účtu třetí strany, které zahrnují vaše uživatelské jméno a ověřovací kód účtu, který je nutný k prokázání vaší identity. Neukládáme žádné další informace spojené s vašimi účty, včetně e-mailů nebo souborů. Vaše účty také žádným způsobem nespojujeme ani nesdílíme s žádným jiným produktem nebo službou. A konečně, váš správce IT nezíská žádné informace o žádném z těchto účtů.

## <a name="back-up-your-account-credentials"></a>Zálohování přihlašovacích údajů k účtu

Než budete moci zálohovat přihlašovací údaje, musíte mít:

- Osobní [účet Microsoft,](https://account.microsoft.com/account) který bude sloužit jako váš účet pro obnovení.

- **Pouze pro iOS** musíte mít [účet iCloud](https://www.icloud.com/) pro skutečné umístění úložiště.

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>Zapnutí zálohování v cloudu pro zařízení se systémem iOS

- Na iOS zařízení vyberte **Nastavení**, vyberte **Zálohovat**a pak zapněte zálohování na **iCloudu**.

    Přihlašovací údaje k vašemu účtu se zálohují na váš účet na iCloudu.

    ![obrazovka nastavení iOS zobrazující umístění nastavení zálohování iCloud](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>Zapnutí zálohování v cloudu pro zařízení se systémem Android

- Na zařízení Android vyberte **Nastavení**, vyberte **Zálohovat**a pak zapněte **zálohování v Cloudu**.

    Přihlašovací údaje vašeho účtu jsou zálohovány na váš cloudový účet.

    ![Obrazovka nastavení systému Android zobrazující umístění nastavení zálohování](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Obnovení přihlašovacích údajů k účtu v novém zařízení

Pověření účtu můžete obnovit ze svého cloudového účtu, ale musíte se nejprve ujistit, že účet, který obnovujete, neexistuje v aplikaci Microsoft Authenticator. Pokud například obnovujete svůj osobní účet Microsoft, musíte se ujistit, že v aplikaci pro ověřování už nemáte nastavený osobní účet Microsoft. Tato kontrola je důležitá, abychom si mohli být jisti, že nepřepisujeme nebo nemažeme existující účet omylem.

### <a name="to-recover-your-information"></a>Obnovení informací

1. Na mobilním zařízení otevřete aplikaci Microsoft Authenticator a v dolní části obrazovky vyberte **Zahájit obnovení.**

    ![Aplikace Microsoft Authenticator, která ukazuje, kde kliknout na tlačítko Zahájit obnovení](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Přihlaste se ke svému účtu pro obnovení pomocí stejného osobního účtu Microsoft, který jste použili během procesu zálohování.

    Pověření vašeho účtu se obnoví do nového zařízení.

Po dokončení obnovení si můžete všimnout, že vaše osobní ověřovací kódy účtu Microsoft v aplikaci Microsoft Authenticator se liší mezi starými a novými telefony. Kódy se liší, protože každé zařízení má své vlastní jedinečné pověření, ale oba jsou platné a pracují při přihlášení pomocí přidruženého telefonu.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Obnovení dalších účtů vyžadujících další ověření

Pokud používáte nabízená oznámení se svými osobními nebo pracovními nebo školními účty, zobrazí se upozornění na obrazovce, že před obnovením informací musíte poskytnout další ověření. Vzhledem k tomu, že nabízená oznámení vyžadují použití pověření, které je svázané s vaším konkrétním zařízením a nikdy neodeslané v síti, musíte před vytvořením přihlašovacích údajů v zařízení prokázat svou identitu.

U osobních účtů Microsoft můžete prokázat svou identitu zadáním hesla spolu s alternativním e-mailem nebo telefonním číslem. U pracovních nebo školních účtů musíte naskenovat QR kód, který vám dal poskytovatel účtu.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>Poskytnutí dodatečného ověření osobních účtů

1. Na obrazovce **Účty** aplikace Microsoft Authenticator vyberte účet, který chcete obnovit. Na zařízení se systémem Android vyberte šipku vedle účtu, který chcete obnovit.

    ![Aplikace Microsoft Authenticator zobrazující dostupné účty s přidruženými šipkami rozevíracího seznamu](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

    Na iOS zařízení klepnutím na účet, který chcete obnovit, otevřete zobrazení účtu na celou obrazovku.

    ![Aplikace Microsoft Authenticator zobrazující dostupné účty s přidruženými šipkami rozevíracího seznamu](./media/user-help-auth-app-backup-recovery/ios-select-account-to-recover.png)

2. Chcete-li obnovit účet, přihlaste se. Na zařízení se systémem Android vyberte **Přihlásit se, abyste obnovili**.

    ![Microsoft Authenticator aplikace pro zadání přihlašovacích údajů v systému Android](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

    Na iOS zařízení klepněte na dlaždici účtu, kterou chcete obnovit, a potom klepněte na možnost přihlášení a obnovení. Poté zadejte heslo a potvrďte svou e-mailovou adresu nebo telefonní číslo jako další adresu verification.unt.

    ![Microsoft Authenticator app zadejte své přihlašovací údaje v systému iOS](./media/user-help-auth-app-backup-recovery/ios-sign-in-to-recover.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>Poskytnutí dodatečného ověření pracovních nebo školních účtů

1. Chcete-li obnovit účet, přihlaste se. Na zařízení se systémem Android vyberte **Přihlásit se, abyste obnovili**.

    ![Aplikace Microsoft Authenticator obnoví pracovní nebo školní účet v systému Android](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

    Na iOS zařízení klepněte na účet, který chcete obnovit, a otevřete zobrazení účtu na celou obrazovku.

    ![Aplikace Microsoft Authenticator obnoví pracovní nebo školní účet v systému iOS](./media/user-help-auth-app-backup-recovery/ios-recover-work-or-school-account.png)

2. QR kód můžete použít k obnovení účtu. Vyberte **prohledávat QR kód, který chcete obnovit**, a pak naskenujte QR kód.

    V Androidu:

    ![Microsoft Authenticator aplikace na Android, což vám umožní skenovat QR kód](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    V iOS:

    ![Microsoft Authenticator aplikace na iOS, což vám umožní skenovat QR kód](./media/user-help-auth-app-backup-recovery/ios-scan-qr-code.png)

    >[!NOTE]
    >Další informace o QR kódech a o tom, jak je získat, najdete [v tématu Začínáme s aplikací Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) nebo [Nastavte bezpečnostní údaje tak, aby používaly ověřovací aplikaci](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app), na základě toho, jestli váš správce zapnul bezpečnostní údaje.
    >
    >Pokud nastavujete aplikaci Microsoft Authenticator poprvé, může se zobrazit výzva s dotazem, zda má aplikaci povolit přístup ke kameře (iOS) nebo zda aplikaci pořizovat snímky a nahrávat video (Android). Musíte vybrat **Povolit,** aby ověřovací aplikace mohla přistupovat k fotoaparátu a pořit qr kód v dalším kroku. Pokud fotoaparát nepovolíte, můžete aplikaci pro ověřování nastavit, ale budete muset přidat informace o kódu ručně. Informace o ručním přidání kódu najdete v [tématu Ruční přidání účtu do aplikace](user-help-auth-app-add-account-manual.md).

## <a name="troubleshoot-backup-and-recovery-problems"></a>Poradce při potížích se zálohováním a obnovením

Existuje několik důvodů, proč vaše záloha nemusí být k dispozici

- **Změna operačních systémů**: Záloha je uložena v iCloudu pro iOS a ve zprostředkovateli cloudového úložiště společnosti Microsoft pro Android. To znamená, že vaše záloha není k dispozici, pokud přepínáte mezi zařízeními android a iOS. Pokud provedete přepínač, musíte ručně znovu vytvořit své účty v aplikaci Microsoft Authenticator.

- **Problémy se sítí**: Pokud dochází k problémům se sítí, zkontrolujte, zda jste připojeni k síti a jste správně přihlášeni ke svému účtu.

- **Problémy s účtem**: Pokud máte problémy s účtem, ujistěte se, že jste ke svému účtu správně přihlášeni. Pro iOS to znamená, že musíte být přihlášeni k iCloudu pomocí stejného účtu AppleID jako váš iPhone.

- **Náhodné smazání**: Je možné, že jste odstranili záložní účet z předchozího zařízení nebo při správě účtu cloudového úložiště. V takovém případě je nutné ručně znovu vytvořit svůj účet v rámci aplikace.

- **Existující účty Microsoft Authenticator**: Pokud jste už nastavili účty v aplikaci Microsoft Authenticator, aplikace nebude moct zálohované účty obnovit. Prevence obnovení pomáhá zajistit, aby údaje o vašem účtu nebyly přepsány zastaralými informacemi. V takovém případě je nutné odebrat všechny existující informace o účtu z existujících účtů nastavených v aplikaci Authenticator před obnovením zálohy.

- **Zálohování je zastaralé**: Pokud jsou informace o zálohování zastaralé, můžete být požádáni o aktualizaci informací znovu přihlášením k účtu Microsoft Recovery. Váš účet pro obnovení je osobní účet Microsoft, který jste původně používali k uložení zálohy. Pokud je vyžadováno přihlášení, zobrazí se v nabídce nebo na panelu akcí červená tečka nebo se zobrazí vykřičník s výzvou k přihlášení a dokončení obnovení ze zálohy. Po výběru ikony budete vyzváni k novému přihlášení a aktualizovat informace.

## <a name="next-steps"></a>Další kroky

Teď, když jste zálohovali a obnovili přihlašovací údaje účtu do nového zařízení, můžete nadále používat aplikaci Microsoft Authenticator k ověření vaší identity. Další informace najdete [v tématu Přihlášení k účtům pomocí aplikace Microsoft Authenticator](user-help-sign-in.md).

## <a name="related-articles"></a>Související články

- [Co je aplikace Microsoft Authenticator?](user-help-auth-app-overview.md)

- [Nejčastější dotazy k aplikaci Microsoft Authenticator](user-help-auth-app-faq.md)

- [Vícefaktorové ověřování](https://docs.microsoft.com/azure/multi-factor-authentication/)
