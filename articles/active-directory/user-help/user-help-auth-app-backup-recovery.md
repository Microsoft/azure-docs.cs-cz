---
title: Zálohování a obnovení pomocí aplikace Microsoft Authenticator – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak zálohovat a obnovovat vaše přihlašovací údaje účtu, pomocí aplikace Microsoft Authenticator.
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
ms.openlocfilehash: 734e337ba8dff996e9b9cecd7602115bf97b4810
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55773267"
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Zálohování a obnovení přihlašovacích údajů účtu v aplikaci Microsoft Authenticator

**Platí pro:**

- zařízení s Iosem

Aplikace Microsoft Authenticator zálohuje vaše přihlašovací údaje k účtu a související aplikace nastavení, například pořadí z vašich účtů do cloudu. Po dokončení zálohování, můžete také použít aplikace obnovte informace na novém zařízení, potenciálně zabránit získávání uzamčen na více instancí nebo nutnosti opětného vytváření účtů.

>[!IMPORTANT]
> Pro každé umístění úložiště pro zálohování budete potřebovat jeden osobní účet Microsoft a jeden účet serveru služby iCloud. Ale v tomto umístění úložiště, můžete zálohovat několik účtů. Například můžete mít osobní účet, školní účet a účet třetích stran, jako je Facebook, Google a tak dále.<br><br>Ukládají se jenom osobní a 3. stran přihlašovacích údajů svého účtu, což zahrnuje uživatelské jméno a ověřovací kód účtu, který je potřeba k prokázání své identity. Neukládáme nějakých jiných informací, které jsou přidružené k účtům, včetně e-mailů nebo souborů. Můžeme také není přidružení nebo sdílet vaše účty žádným způsobem nebo všem produktům a službám. A nakonec nebude váš správce IT získat všechny informace o všech těchto účtů.

## <a name="back-up-your-account-credentials"></a>Zálohování přihlašovacích údajů k účtu
Než budete zálohovat přihlašovací údaje, musíte mít:

- Osobní [účtu Microsoft](https://account.microsoft.com/account) tak, aby fungoval jako účtu pro obnovení.

- [Účtu iCloud](https://www.icloud.com/) pro skutečného umístění úložiště. 

Které vyžadují, abyste pro přihlášení k oba účty společně zajišťuje silnější zabezpečení pro vaše informace o zálohování.

**Chcete-li využívají Cloud zálohování**
-   Na zařízení s Iosem vyberte **nastavení**vyberte **zálohování**a pak zapněte **zálohy na Icloudu**.

    Přihlašovací údaje účtu se zálohují na váš účet iCloud.

    ![nastavení pro IOS, zobrazuje umístění na serveru služby iCloud nastavení zálohování](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Obnovení přihlašovacích údajů k účtu svoje nové zařízení
Obnovení přihlašovacích údajů k účtu z vašeho účtu iCloud pomocí stejného účtu obnovení Microsoftu, které jste nastavili při zálohování vašich informací.

### <a name="to-recover-your-information"></a>Chcete-li obnovit vaše informace
1.  Na zařízení s iOS otevřete aplikaci Microsoft Authenticator a vyberte **zahájit obnovování** v dolní části obrazovky.

    ![Aplikace Microsoft Authenticator, znázorňující, kde klikněte na tlačítko zahájit obnovení](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2.  Přihlaste se ke svému účtu obnovení pomocí stejného osobního účtu Microsoft, který jste použili během procesu zálohování.

    Přihlašovací údaje účtu jsou obnoveny do nové zařízení.

Po dokončení vaší obnovení můžete všimnout, že váš osobní Microsoft účtu ověřovací kódy v aplikaci Microsoft Authenticator se liší mezi původní a nové telefony. Kód se liší, protože každé zařízení má svůj vlastní jedinečný přihlašovacích údajů, ale oba jsou platná a práce při přihlášení pomocí telefonu přidružené.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Obnovit další účty, které vyžadují další ověření
Pokud používáte nabízených oznámení pomocí osobního, pracovní nebo školní účty, zobrazí se na obrazovce oznámení, která uvádí, že před obnovením vaše informace, je nutné zadat další ověření. Nabízená oznámení vyžadují pomocí přihlašovacích údajů, který je vázaný na vaše konkrétní zařízení a nikdy neodesílá přes síť, a proto musí prokázat vaši identitu, než přihlašovací údaje, které se vytvoří na vašem zařízení.

Pro osobní účty Microsoft můžete prokázat vaši identitu zadáním hesla spolu s alternativní e-mail nebo telefonní číslo. Pro pracovní nebo školní účty, musí naskenovat kód QR, který vám Dal poskytovatel vašeho účtu.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>Dodatečného ověření pro osobní účty
1.  V **účty** obrazovce v aplikaci Microsoft Authenticator, vyberte rozevírací šipku vedle účet, který chcete obnovit.

    ![Microsoft aplikace Authenticator s účty k dispozici s jejich přidružených šipky rozevíracího seznamu](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

2.  Vyberte **Přihlaste se k obnovení**, zadejte heslo a potvrďte e-mailovou adresu nebo telefonní číslo jako další ověření.

    ![Aplikace Microsoft Authenticator, abyste mohli zadat své přihlašovací údaje](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>Dodatečného ověření pro pracovní nebo školní účty
1.  V **účty** obrazovce v aplikaci Microsoft Authenticator, vyberte rozevírací šipku vedle účet, který chcete obnovit.

    ![Microsoft aplikace Authenticator s účty k dispozici s jejich přidružených šipky rozevíracího seznamu](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

2.  Vyberte **kontrolovat QR kód pro obnovení**a potom naskenovat kód QR.

    ![Aplikace Microsoft Authenticator, abyste mohli naskenovat QR kód](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    >[!NOTE]
    >Další informace o tom, jak získat kód QR, naleznete v tématu [Začínáme s aplikací Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install#add-accounts-to-the-app) nebo [nastavení bezpečnostní údaje pro použití ověřovací aplikaci](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app#to-use-the-microsoft-authenticator-app)závislosti na tom, zda váš správce aktivoval na bezpečnostní údaje.

## <a name="troubleshooting-backup-and-recovery-problems"></a>Řešení potíží se zálohování a obnovení
Existuje několik důvodů, proč vaše záloha nemusí být k dispozici:

-   **Změna operačních systémů.** Vaše záloha je uložen v možnost cloudového úložiště, které jsou poskytované váš telefon operačního systému, což znamená, že je záloha není k dispozici, můžete přepínat mezi platformami Android a iOS. V takovém případě musíte ručně znovu vytvořit váš účet v rámci aplikace.

-   **Problémy s sítě nebo heslo.** Ujistěte se, že jste připojení k síti a přihlášení k účtu iCloud pomocí stejné AppleID, které jste použili v Iphonu poslední.

-   **Nechtěnému odstranění.** Je možné, že jste odstranili účtu Zálohování z vašeho předchozího zařízení nebo při správě účtu cloudového úložiště. V takovém případě musíte ručně znovu vytvořit váš účet v rámci aplikace.

-   **Existující účty Microsoft Authenticator.** Pokud jste již nastavili účty v aplikaci Microsoft Authenticator, aplikace nebude možné obnovit své účty zálohování. Ochrana proti obnovení pomáhá zajistit, že podrobnosti o vašem účtu nejsou přepsána zastaralé informace. V takovém případě musíte odebrat všechny stávající informace o účtu z existujících účtů nastavení v aplikaci Authenticator před obnovením zálohy.

## <a name="next-steps"></a>Další postup
Teď, když máte zálohovat a obnovit svoje přihlašovací údaje k novým zařízením, můžete nadále používat aplikaci Microsoft Authenticator ověřit vaši identitu.

## <a name="related-topics"></a>Související témata
- [Začínáme s aplikací Microsoft Authenticator](user-help-auth-app-download-install.md)  
- [Nejčastější dotazy k aplikaci Microsoft Authenticator](user-help-auth-app-faq.md)

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
