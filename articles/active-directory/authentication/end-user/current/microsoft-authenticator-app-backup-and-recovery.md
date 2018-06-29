---
title: Zálohování a obnovení pomocí aplikace Microsoft Authenticator – Azure AD | Microsoft Docs
description: Zjistěte, jak zálohovat a obnovovat přihlašovací údaje účtu, pomocí aplikace Microsoft Authenticator.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: lizross
ms.reviewer: olhaun
ms.custom: end-user
ms.openlocfilehash: a9c950ecafd2eb5f3aed1bee3707f57be6ec3b62
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102149"
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Zálohování a obnovení přihlašovací údaje účtu v aplikaci Microsoft Authenticator

**Platí pro:**

- zařízení s iOS

Aplikace Microsoft Authenticator zálohuje vaše přihlašovací údaje účtu a související aplikace nastavení, například pořadí vaše účty, do cloudu. Po dokončení zálohování, můžete také použít aplikace k obnovení vaše informace o nové zařízení, potenciálně zabraňující získávání uzamčena na více systémů nebo nutnosti opětného vytváření účtů.

>[!IMPORTANT]
> Pro každé umístění úložiště zálohy je nutné jeden osobní účet Microsoft a jeden účet serveru služby iCloud. Ale v tomto umístění úložiště, můžete zálohovat některé účty. Například můžete mít osobního účtu, školní účet a účet třetích stran, jako je Facebook, Google a tak dále.<br><br>Ukládají se jenom svoje osobní, tak i 3. stran přihlašovací údaje, které zahrnují vaše uživatelské jméno a ověřovací kód účtu, které je nutné k prokázání své identity. Neukládejte jsme všechny ostatní informace související s vaše účty, včetně e-mailů a souborů. Můžeme také nemáte přidružit nebo sdílet vaše účty žádným způsobem nebo jiné produkty nebo služby. A nakonec nebude váš správce IT získat žádné informace o všech těchto účtů.

## <a name="back-up-your-account-credentials"></a>Zálohovat přihlašovací údaje účtu
Předtím, než můžete zálohovat přihlašovací údaje, musí mít obě:

- Osobní [účtu Microsoft](https://account.microsoft.com/account) tak, aby fungoval jako účet pro obnovení.

- [Účtem Icloudu](https://www.icloud.com/) pro skutečného umístění úložiště. 

Vyžadující, abyste Přihlaste se k oba účty společně zajišťuje silnější zabezpečení pro vaše informace o zálohování.

**Chcete-li v cloudu zálohování**
-   Na zařízení s iOS, vyberte **nastavení**, vyberte **zálohování**a pak zapněte **automatické zálohování**.

    Přihlašovací údaje účtu jsou zálohovány ke svému účtu serveru služby iCloud.

    ![obrazovka nastavení iOS, zobrazuje umístění automatického nastavení zálohování](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-turn-on.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Obnovit přihlašovací údaje účtu na nové zařízení
Přihlašovací údaje účtu můžete obnovit z vašeho účtu Icloudu pomocí stejného účtu Microsoft obnovení, kterou vytvoříte, když jste zálohovali vaše informace.

**Obnovte informace**
1.  Na zařízení s iOS, otevřete aplikaci Microsoft Authenticator a vyberte **zahájit obnovení** v dolní části obrazovky.

    ![Aplikaci Microsoft Authenticator ukazující, kde klikněte na začátek obnovení](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-begin-recovery.png)

2.  Přihlaste se k obnovení účtu, pomocí stejné osobní účet Microsoft, které jste použili při procesu zálohování.

    Přihlašovací údaje účtu jsou obnoveny do nové zařízení.

Po dokončení vaše obnovení, můžete si všimnout, že vaše osobní Microsoft účet ověřovací kódy v aplikaci Microsoft Authenticator se liší vaše staré a nové telefony. Kódy se liší, protože každé zařízení má svůj vlastní jedinečný přihlašovacích údajů, ale i při přihlášení pomocí přidružených telefonních jsou platné a pracují.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Obnovit další účty, které vyžadují další ověření
Pokud používáte nabízených oznámení pomocí osobní, pracovní nebo školní účty, získáte na obrazovce výstrahy které říká, je nutné zadat další ověření, než bude možné obnovit vaše informace. Nabízená oznámení vyžadují pomocí přihlašovacích údajů, který je vázaný na konkrétní zařízení a nikdy neodesílá přes síť, a proto musí prokázat vaši identitu, před vytvořením přihlašovací údaje na vašem zařízení.

Pro osobní účty Microsoft můžete prokázání své identity tím, že zadáte heslo společně s alternativní e-mail nebo telefonní číslo. Pro pracovní nebo školní účty musíte naskenovat kód QR získané od vašeho poskytovatele účtů.

**Dodatečného ověření pro osobní účty**
1.  V **účty** obrazovky aplikace Microsoft Authenticator, vyberte z rozevírací nabídky šipkou účet, kterou chcete obnovit.

    ![Aplikace Microsoft Authenticator, zobrazuje dostupné účty s jejich přidružené šipky rozevíracího seznamu](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-arrow.png)

2.  Vyberte **Přihlaste se k obnovení**, zadejte heslo a potvrďte e-mailovou adresu nebo telefonní číslo jako další ověření.

    ![Aplikaci Microsoft Authenticator, abyste mohli zadat své přihlašovací údaje](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-sign-in.png)

**Dodatečného ověření pro pracovní nebo školní účty**
1.  V **účty** obrazovky aplikace Microsoft Authenticator, vyberte z rozevírací nabídky šipkou účet, kterou chcete obnovit.

    ![Aplikace Microsoft Authenticator, zobrazuje dostupné účty s jejich přidružené šipky rozevíracího seznamu](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-additonal-accts.png)

2.  Vyberte **kód QR kontrolovat obnovení**a pak naskenujte kód QR od svého správce.

    ![Aplikaci Microsoft Authenticator, což umožňuje kontrolovat kód QR](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-scan-qr-code.png)

    >[!NOTE]
    >Další informace o tom, jak získat kód QR, najdete v článku [začít pracovat s aplikací Microsoft Authenticator](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to) článku.

## <a name="troubleshooting-backup-and-recovery-problems"></a>Řešení potíží zálohování a obnovení
Existují z několika důvodů, proč nemusí být k dispozici zálohování:

-   **Změna operační systémy.** V možnosti úložiště cloudu poskytované váš telefon operační systém, což znamená, že je záloha není k dispozici, přepínání mezi Android a iOS je uložena záloha. V takovém případě musíte ručně znovu vytvořit účet v rámci aplikace.

-   **Problémy sítě nebo heslo.** Ujistěte se, že jste připojení k síti a přihlášení k serveru služby iCloud účtu pomocí stejné načte jste použili na vaší poslední zařízení iPhone.

-   **Náhodného odstranění.** Je možné odstranit účtu zálohování, z předchozí zařízení nebo při správě vašeho účtu úložiště v cloudu. V takovém případě musíte ručně znovu vytvořit účet v rámci aplikace.

-   **Existující účty Microsoft Authenticator.** Pokud jste již nastavili účty v aplikaci Microsoft Authenticator, aplikace nebude možné obnovit vaše zálohovaná účty. Ukládání obnovení pomáhá zajistit, že údaje o vašem účtu nejsou přepsána zastaralé informace. V takovém případě musíte odebrat všechny stávající informace o účtu z existujících účtů nastavit ve vaší ověřovací aplikací, než bude možné obnovit zálohování.

## <a name="next-steps"></a>Další postup
Teď, když jste zálohovat a obnovovat svoje přihlašovací údaje do nové zařízení, můžete nadále používat aplikaci Microsoft Authenticator ověřit vaši identitu.

## <a name="related-topics"></a>Související témata
- [Začínáme s aplikací Microsoft Authenticator](microsoft-authenticator-app-how-to.md)  

- [Přihlášení pomocí telefonu](microsoft-authenticator-app-phone-signin-faq.md)

- [Aplikace Microsoft Authenticator – nejčastější dotazy](microsoft-authenticator-app-faq.md)

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
