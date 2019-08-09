---
title: Zálohování a obnovení pomocí aplikace Microsoft Authenticator App-Azure Active Directory | Microsoft Docs
description: Naučte se zálohovat a obnovovat přihlašovací údaje k účtu pomocí aplikace Microsoft Authenticator.
services: active-directory
author: eross-msft
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba99001159277d9d221910cafa4f5165ae2bd812
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880760"
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Zálohování a obnovení přihlašovacích údajů k účtu pomocí aplikace Microsoft Authenticator

**Platí pro:**

- zařízení s iOS

Aplikace Microsoft Authenticator zálohuje vaše přihlašovací údaje k účtu a související nastavení aplikace, jako je například pořadí vašich účtů, do cloudu. Po zálohování můžete také použít aplikaci k obnovení svých informací na novém zařízení, což může zabránit uzamknutí nebo opětovnému vytvoření účtů.

> [!IMPORTANT]
> Pro každé umístění úložiště zálohy potřebujete jeden osobní účet Microsoft a jeden účet iCloud. Ale v tomto umístění úložiště můžete zálohovat několik účtů. Můžete mít například osobní účet, školní účet a účet třetí strany, jako je Facebook, Google atd.
>
> Ukládají se jenom vaše osobní přihlašovací údaje a přihlašovací údaje k účtu třetích stran, což zahrnuje vaše uživatelské jméno a ověřovací kód účtu, který je nezbytný k prokázání vaší identity. Neuložíme žádné další informace spojené s vašimi účty, včetně e-mailů nebo souborů. Nepřidružíme ani nesdílíme vaše účty žádným způsobem ani s žádným jiným produktem nebo službou. A konečně, váš správce IT nebude získávat žádné informace o žádné z těchto účtů.

## <a name="back-up-your-account-credentials"></a>Zálohování přihlašovacích údajů k účtu

Než budete moct zálohovat svoje přihlašovací údaje, musí mít:

- Osobní [účet Microsoft](https://account.microsoft.com/account) , která má sloužit jako účet pro obnovení.

- [ICloud účet](https://www.icloud.com/) pro skutečné umístění úložiště.

Vyžadování přihlášení do obou účtů zajišťuje silnější zabezpečení informací o zálohování.

### <a name="to-turn-on-cloud-backup"></a>Zapnutí zálohování v cloudu

- Na zařízení s iOS vyberte **Nastavení**, vyberte **zálohování**a pak zapněte **zálohování iCloud**.

    Přihlašovací údaje k účtu se zálohují na váš účet iCloud.

    ![obrazovka nastavení iOS zobrazující umístění nastavení zálohování iCloud](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Obnovte přihlašovací údaje účtu na novém zařízení.

Přihlašovací údaje účtu můžete obnovit z účtu iCloud pomocí stejného účtu Microsoft Recovery, který jste nastavili při zálohování vašich informací.

### <a name="to-recover-your-information"></a>Obnovení vašich informací

1. Na zařízení s iOS otevřete aplikaci Microsoft Authenticator a v dolní části obrazovky vyberte **zahájit obnovení** .

    ![Aplikace Microsoft Authenticator, kde se zobrazuje, kde kliknout na zahájit obnovení](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Přihlaste se ke svému účtu pro obnovení pomocí stejné osobní účet Microsoft, jakou jste použili během procesu zálohování.

    Přihlašovací údaje k účtu se obnoví na nové zařízení.

Po dokončení obnovení si můžete všimnout, že se ověřovací kódy vašich osobních účet Microsoft v aplikaci Microsoft Authenticator liší mezi starými a novými telefony. Kódy se liší, protože každé zařízení má své vlastní jedinečné přihlašovací údaje, ale obojí je platné a funguje při přihlášení pomocí přidruženého telefonu.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Obnovení dalších účtů vyžadujících více ověření

Pokud používáte nabízená oznámení s vašimi osobními, pracovními nebo školními účty, zobrazí se upozornění na obrazovce s oznámením, že před obnovením informací musíte poskytnout dodatečné ověření. Protože nabízená oznámení vyžadují použití přihlašovacích údajů, která je vázaná na konkrétní zařízení a nikdy se neodesílají přes síť, musíte prokázat svoji identitu, než se na svém zařízení vytvoří přihlašovací údaje.

U osobních účtů Microsoft můžete svou identitu prokázat zadáním hesla spolu s alternativním e-mailem nebo telefonním číslem. Pro pracovní nebo školní účty musíte zkontrolovat kód QR přidělený vaším poskytovatelem účtu.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>Poskytnutí dodatečného ověření pro osobní účty

1. Na obrazovce **účty** aplikace Microsoft Authenticator vyberte šipku rozevíracího seznamu vedle účtu, který chcete obnovit.

    ![Aplikace Microsoft Authenticator, kde se zobrazují dostupné účty s příslušnými šipkami rozevíracího seznamu](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

2. Vyberte **Přihlásit se**a obnovte, zadejte heslo a potvrďte svou e-mailovou adresu nebo telefonní číslo jako další ověření.

    ![Microsoft Authenticator aplikaci, která vám umožní zadat přihlašovací údaje](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>Poskytnutí dodatečného ověření pro pracovní nebo školní účty

1. Na obrazovce **účty** aplikace Microsoft Authenticator vyberte šipku rozevíracího seznamu vedle účtu, který chcete obnovit.

    ![Aplikace Microsoft Authenticator, kde se zobrazují dostupné účty s příslušnými šipkami rozevíracího seznamu](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

2. Vyberte možnost **skenovat kód QR pro obnovení**a pak NASKENUJTE kód QR.

    ![Microsoft Authenticator aplikaci, která vám umožní kontrolovat kód QR](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    >[!NOTE]
    >Pokud Microsoft Authenticator aplikaci nakonfigurujete poprvé, může se zobrazit výzva s dotazem, jestli chcete, aby aplikace měla přístup k vaší kameře (iOS), nebo aby aplikaci mohla pořizovat snímky a nahrávat video (Android). Je nutné vybrat možnost **umožnit** , aby mohla aplikace ověřovatele získat přístup k fotoaparátu, aby pomohlo vytvořit obrázek kódu QR v dalším kroku. Pokud fotoaparát nepovolíte, můžete přesto nastavit ověřovací aplikaci, ale budete muset informace o kódu přidat ručně. Informace o tom, jak kód přidat ručně, najdete v tématu věnovaném [ručnímu přidání účtu do aplikace](user-help-auth-app-add-account-manual.md).
    >
    >Další informace o tom, jak získat kód QR, najdete v tématu [Začínáme s aplikací Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) nebo [Nastavení bezpečnostních údajů pro použití ověřovací aplikace](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app)na základě toho, jestli váš správce zapnul bezpečnostní údaje.

## <a name="troubleshooting-backup-and-recovery-problems"></a>Řešení potíží se zálohováním a obnovením

K dispozici je několik důvodů, proč vaše zálohování nemusí být k dispozici:

- **Mění se operační systémy.** Vaše záloha je uložená v cloudovém úložišti, které poskytuje operační systém telefonu, což znamená, že Pokud přepínáte mezi Androidem a iOS, zálohování není dostupné. V této situaci musíte účet ručně znovu vytvořit v rámci aplikace.

- **Problémy se sítí nebo heslem.** Ujistěte se, že jste připojení k síti a přihlásili se k účtu iCloud pomocí stejné AppleID, jakou jste použili na posledním iPhonu.

- **Náhodné odstranění.** Je možné, že jste odstranili účet zálohování z předchozího zařízení nebo spravujete svůj účet cloudového úložiště. V této situaci musíte účet ručně znovu vytvořit v rámci aplikace.

- **Existující účty Microsoft Authenticator.** Pokud jste už v aplikaci Microsoft Authenticator nastavili účty, aplikace nebude moct obnovit vaše zálohované účty. Prevence obnovení pomáhá zajistit, aby se podrobnosti o účtu nepsaly aktuálními informacemi. V této situaci musíte před obnovením zálohy z existujících účtů, které jste nastavili v ověřovací aplikaci, odebrat všechny informace o stávajícím účtu.

## <a name="next-steps"></a>Další postup

Teď, když jste zálohovali a obnovili přihlašovací údaje účtu na nové zařízení, můžete k ověření vaší identity použít aplikaci Microsoft Authenticator. Další informace najdete v tématu [přihlášení ke svým účtům pomocí aplikace Microsoft Authenticator](user-help-sign-in.md).

## <a name="related-topics"></a>Související témata

- [Co je aplikace Microsoft Authenticator?](user-help-auth-app-overview.md)

- [Nejčastější dotazy k aplikaci Microsoft Authenticator](user-help-auth-app-faq.md)

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
