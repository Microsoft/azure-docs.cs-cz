---
title: Zálohování a obnovení účtů pomocí aplikace Microsoft Authenticator – Azure AD
description: Přečtěte si, jak zálohovat a obnovit přihlašovací údaje zálohovaného účtu pomocí aplikace Microsoft Authenticator.
services: active-directory
author: curtand
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: end-user-help
ms.date: 06/03/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: e7608196c962a6918a90b91457f856f3e11b04f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91530897"
---
# <a name="back-up-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>Zálohování a obnovení přihlašovacích údajů k účtu pomocí aplikace Microsoft Authenticator

**Platí pro:**

- zařízení s iOS, na kterých běží 5.7.0 verze a novější

- Zařízení s Androidem, běžící verze 6.6.0 a novější

Aplikace Microsoft Authenticator zálohuje vaše přihlašovací údaje k účtu a související nastavení aplikace, jako je například pořadí vašich účtů, do cloudu. Po zálohování můžete také použít aplikaci k obnovení svých informací na novém zařízení, což může zabránit uzamknutí nebo opětovnému vytvoření účtů.

Každé umístění úložiště pro zálohování vyžaduje, abyste měli jeden osobní účet Microsoft, zatímco iOS také vyžaduje, abyste měli účet iCloud. V jednom umístění může být uloženo více účtů. Můžete mít například osobní účet, pracovní nebo školní účet a osobní, neúčet Microsoft jako pro Facebook, Google atd.

> [!IMPORTANT]
> Ukládají se jenom vaše osobní přihlašovací údaje a přihlašovací údaje k účtu třetích stran, což zahrnuje vaše uživatelské jméno a ověřovací kód účtu, který je nezbytný k prokázání vaší identity. Neuložíme žádné další informace spojené s vašimi účty, včetně e-mailů nebo souborů. Nepřidružíme ani nesdílíme vaše účty žádným způsobem ani s žádným jiným produktem nebo službou. A konečně, váš správce IT nebude získávat žádné informace o žádné z těchto účtů.

## <a name="back-up-your-account-credentials"></a>Zálohování přihlašovacích údajů k účtu

Než budete moct zálohovat přihlašovací údaje, musíte mít:

- Osobní [účet Microsoft](https://account.microsoft.com/account) , která má sloužit jako účet pro obnovení.

- **Pouze pro iOS** musíte mít pro skutečné umístění úložiště [účet iCloud](https://www.icloud.com/) .

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>Zapnutí zálohování cloudu pro zařízení s iOS

- Na zařízení s iOS vyberte **Nastavení**, vyberte **zálohování**a pak zapněte **zálohování iCloud**.

    Přihlašovací údaje k účtu se zálohují na váš účet iCloud.

    ![obrazovka nastavení iOS zobrazující umístění nastavení zálohování iCloud](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>Zapnutí zálohování cloudu pro zařízení s Androidem

- Na zařízení s Androidem vyberte **Nastavení**, vyberte **zálohování**a pak zapněte **zálohování do cloudu**.

    Přihlašovací údaje k účtu se zálohují do svého cloudového účtu.

    ![Obrazovka nastavení Androidu, na které se zobrazuje umístění nastavení zálohování](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Obnovte přihlašovací údaje účtu na novém zařízení.

Přihlašovací údaje účtu můžete obnovit z účtu cloudu, musíte se nejdřív ale ujistit, že účet, který obnovujete, neexistuje v aplikaci Microsoft Authenticator. Pokud například obnovujete osobní účet Microsoft, je nutné zajistit, aby v ověřovací aplikaci již nebyla nastavena osobní účet Microsoft. Tato kontrolu je důležité, takže můžeme se ujistit, že nepřepisujete nebo nemažete existující účet omylem.

### <a name="to-recover-your-information"></a>Obnovení vašich informací

1. Na mobilním zařízení otevřete aplikaci Microsoft Authenticator a v dolní části obrazovky vyberte **zahájit obnovení** .

    ![Aplikace Microsoft Authenticator, kde se zobrazuje, kde kliknout na zahájit obnovení](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Přihlaste se ke svému účtu pro obnovení pomocí stejné osobní účet Microsoft, jakou jste použili během procesu zálohování.

    Přihlašovací údaje k účtu se obnoví na nové zařízení.

Po dokončení obnovení si můžete všimnout, že se ověřovací kódy vašich osobních účet Microsoft v aplikaci Microsoft Authenticator liší mezi starými a novými telefony. Kódy se liší, protože každé zařízení má své vlastní jedinečné přihlašovací údaje, ale obojí je platné a funguje při přihlášení pomocí přidruženého telefonu.

## <a name="recover-accounts-requiring-more-verification"></a>Obnovení účtů vyžadujících více ověření

Pokud používáte nabízená oznámení s osobními nebo pracovními nebo školními účty, zobrazí se výstraha na obrazovce, která uvádí, že před obnovením informací musíte poskytnout dodatečné ověření. Protože nabízená oznámení vyžadují použití přihlašovacích údajů, která je vázaná na konkrétní zařízení a nikdy se neodesílají přes síť, musíte prokázat svoji identitu, než se na svém zařízení vytvoří přihlašovací údaje.

U osobních účtů Microsoft můžete svou identitu prokázat zadáním hesla spolu s alternativním e-mailem nebo telefonním číslem. Pro pracovní nebo školní účty musíte zkontrolovat kód QR přidělený vaším poskytovatelem účtu.

### <a name="to-provide-more-verification-for-personal-accounts"></a>Zajištění většího ověřování pro osobní účty

1. Na obrazovce **účty** aplikace Microsoft Authenticator klepněte na účet, který chcete obnovit, a otevřete zobrazení celé obrazovky tohoto účtu.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png" alt-text="Snímek obrazovky zobrazující aplikaci Microsoft Authenticator s dostupnými dlaždicemi účtu" border="true":::

1. Klepněte na dlaždici pro účet, který obnovujete, a pak klepněte na možnost pro přihlášení k obnovení. Zadejte heslo a potvrďte svoji e-mailovou adresu nebo telefonní číslo jako další ověření.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/backup-and-recovery-codes.png" alt-text="Snímek obrazovky zobrazující aplikaci Microsoft Authenticator s dostupnými dlaždicemi účtu" border="true":::

### <a name="to-provide-more-verification-for-work-or-school-accounts"></a>Poskytnutí více ověření pro pracovní nebo školní účty

1. Na obrazovce **účty** aplikace Microsoft Authenticator klepněte na účet, který chcete obnovit, a otevřete zobrazení celé obrazovky tohoto účtu.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/work-or-school-recovery-arrow.png" alt-text="Snímek obrazovky zobrazující aplikaci Microsoft Authenticator s dostupnými dlaždicemi účtu" border="true":::

1. V zobrazení na celé obrazovce klepněte na možnost pro skenování kódu QR pro úplné obnovení.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/work-or-school-recovery-qr-code.png" alt-text="Snímek obrazovky zobrazující aplikaci Microsoft Authenticator s dostupnými dlaždicemi účtu" border="true":::

>[!NOTE]
>Další informace o kódech QR a o tom, jak ho získat, najdete v tématu Začínáme [s aplikací Microsoft Authenticator](./user-help-auth-app-download-install.md) nebo [Nastavení bezpečnostních údajů pro použití ověřovací aplikace](./security-info-setup-auth-app.md)na základě toho, jestli váš správce zapnul bezpečnostní údaje.
>
>Pokud Microsoft Authenticator aplikaci nakonfigurujete poprvé, může se zobrazit výzva s dotazem, jestli chcete, aby aplikace měla přístup k vaší kameře (iOS), nebo aby aplikaci mohla pořizovat snímky a nahrávat video (Android). Je nutné vybrat možnost **umožnit** , aby mohla aplikace ověřovatele získat přístup k fotoaparátu, aby pomohlo vytvořit obrázek kódu QR v dalším kroku. Pokud fotoaparát nepovolíte, můžete přesto nastavit ověřovací aplikaci, ale budete muset informace o kódu přidat ručně. Informace o tom, jak kód přidat ručně, najdete v tématu věnovaném [ručnímu přidání účtu do aplikace](user-help-auth-app-add-account-manual.md).

## <a name="troubleshoot-backup-and-recovery-problems"></a>Řešení problémů se zálohováním a obnovením

K dispozici je několik důvodů, proč vaše zálohování nemusí být k dispozici.

- **Změna operačních systémů**: vaše záloha je uložená v iCloud pro iOS a v poskytovateli cloudového úložiště Microsoftu pro Android. To znamená, že zálohování není k dispozici, Pokud přepínáte mezi zařízeními s Androidem a iOS. Pokud tento přepínač provedete, musíte ručně znovu vytvořit účty v rámci aplikace Microsoft Authenticator.

- **Problémy se sítí**: Pokud máte problémy související se sítí, ujistěte se, že jste připojení k síti a že jste se k účtu přihlásili správně.

- **Problémy s účty**: Pokud dochází k problémům souvisejícím s účtem, ujistěte se, že jste se k účtu přihlásili správně. Pro iOS to znamená, že musíte být přihlášeni k iCloud pomocí stejného účtu AppleID jako iPhone.

- **Náhodné odstranění**: je možné, že jste odstranili účet pro zálohování z předchozího zařízení nebo při správě účtu cloudového úložiště. V této situaci musíte účet ručně znovu vytvořit v rámci aplikace.

- **Existující účty Microsoft Authenticator**: Pokud jste už v aplikaci Microsoft Authenticator nastavili účty, aplikace nebude moct obnovit vaše zálohované účty. Prevence obnovení pomáhá zajistit, aby se podrobnosti o účtu nepsaly aktuálními informacemi. V této situaci musíte před obnovením zálohy z existujících účtů, které jste nastavili v ověřovací aplikaci, odebrat všechny informace o stávajícím účtu.

- **Záloha je zastaralá**: Pokud jsou informace o zálohování zastaralé, možná budete požádáni o aktualizaci informací opětovným přihlášením k účtu Microsoft Recovery. Váš účet pro obnovení je osobní účet Microsoft původně jste použili k uložení zálohy. Pokud se vyžaduje přihlášení, zobrazí se v nabídce nebo na panelu akcí červená tečka nebo se zobrazí ikona s upozorněním, abyste se mohli dokončit obnovení ze zálohy. Po výběru příslušné ikony se zobrazí výzva, abyste se znovu přihlásili a aktualizovali vaše informace.

## <a name="next-steps"></a>Další kroky

Teď, když jste zálohovali a obnovili přihlašovací údaje účtu na nové zařízení, můžete k ověření vaší identity použít aplikaci Microsoft Authenticator. Další informace najdete v tématu [přihlášení ke svým účtům pomocí aplikace Microsoft Authenticator](user-help-sign-in.md).

## <a name="related-articles"></a>Související články

- [Co je aplikace Microsoft Authenticator?](user-help-auth-app-overview.md)

- [Nejčastější dotazy k aplikaci Microsoft Authenticator](user-help-auth-app-faq.md)

- [Vícefaktorové ověřování](/azure/multi-factor-authentication/)