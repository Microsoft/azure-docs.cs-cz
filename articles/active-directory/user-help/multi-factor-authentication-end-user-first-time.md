---
title: Nastavit dvoustupňové ověřování – Azure Active Directory | Dokumentace Microsoftu
description: Pokud vaše společnost umožňuje konfigurovat ověřování Azure Multi-Factor Authentication, se výzva k registraci pro dvoustupňové ověřování. Zjistěte, jak ho nastavit.
services: active-directory
keywords: jak používat azure, active directory v cloudu, kurz služby active directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: 46f83a6a-dbdd-4375-8dc4-e7ea77c16357
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: lizross
ms.openlocfilehash: 9fd7a3c03c72a63f4163f85ac41248e200397206
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55770716"
---
# <a name="set-up-my-account-for-two-step-verification"></a>Nastavit účtu pro dvoustupňové ověřování.
Dvoustupňové ověření je krok dodatečné zabezpečení, která pomáhá chránit váš účet kvůli tomu je těžší jinými lidmi, kteří možnost proniknout. Pokud čtete tento článek, pravděpodobně máte e-mailu ze svého pracovního nebo školního správce o ověřování službou Multi-Factor Authentication. Nebo možná jste zkoušeli přihlásit a zpráva s výzvou k nastavení dalšího ověření zabezpečení. Pokud je to tento případ **nemůžete se přihlásit před dokončením procesu automatické registrace**.

Tento článek vám pomůže nastavit vaši **pracovního nebo školního účtu**. Pokud chcete povolit dvoustupňové ověřování pro vlastní, osobní účet Microsoft, přečtěte si téma [o dvoustupňovém ověřování](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="set-up-your-account"></a>Nastavit účet

Pokud vaše firemní podpora požaduje spuštění pomocí dvoustupňového ověření, zobrazí se vám obrazovka s upozorněním **váš správce vyžaduje, abyste nastavili tento účet pro dodatečné ověření zabezpečení**:

![Nastavení](./media/multi-factor-authentication-end-user-first-time/first.png)

Abyste mohli začít, vyberte **nyní nastavit.**

Pokud nevidíte obrazovku při přihlášení, postupujte podle pokynů v [spravovat nastavení pro dvoustupňové ověřování](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page) najít na stránce nastavení, kde můžete spravovat vaše možnosti ověřování.

## <a name="decide-how-you-want-to-verify-your-sign-ins"></a>Rozhodnutí o způsobu ověření přihlášení

Na první otázku v procesu registrace je, jak chcete, abychom vás kontaktovali. Podívejte se na možnosti v tabulce a pomocí odkazů můžete přejít na kroky nastavení pro jednotlivé metody.

| Způsob kontaktování | Popis |
| --- | --- |
| [Mobilní aplikace](#use-a-mobile-app-as-the-contact-method) |- **Dostávat oznámení o ověření.** Tato možnost odešle oznámení do aplikace authenticator na tablet nebo smartphone. Oznámení zobrazte a pokud je legitimní, vyberte **ověřit** v aplikaci. Vaše firma nebo škola může vyžadovat zadání kódu PIN, než můžete ověřit.<br>- **Použijte ověřovací kód.** V tomto režimu se ověřovací aplikace generuje ověřovací kód, který aktualizuje každých 30 sekund. Zadejte aktuální ověřovací kód v rozhraní přihlášení.<br>Aplikace Microsoft Authenticator je dostupná pro [Android](https://go.microsoft.com/fwlink/?linkid=866594) a [iOS](https://go.microsoft.com/fwlink/?linkid=866594).|
| [Volání na mobilní telefon nebo text](#use-your-mobile-phone-as-the-contact-method) |- **Telefonní hovor** umístí automatizovaný hlasový hovor na telefonní číslo, které zadáte. Stačí odpovědět volání a stisknutím klávesy # na klávesnici telefonu provede ověření.<br>- **Textová zpráva** ukončí textovou zprávu s ověřovacím kódem. Po zobrazení výzvy v textu odpovědět na textovou zprávu nebo zadejte ověřovací kód do rozhraní pro přihlášení k dispozici. |
| [Volání na telefon do kanceláře](#use-your-office-phone-as-the-contact-method) |Umístí automatizovaný hlasový hovor na telefonní číslo, které zadáte. Odpovězte volání a stiskem tlačítka # na klávesnici telefonu provede ověření. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Použití mobilní aplikace jako způsob kontaktu
Pomocí této metody vyžaduje instalaci ověřovací aplikace na telefonu nebo tabletu. Kroky v tomto článku jsou založeny na aplikaci Microsoft Authenticator, která je k dispozici pro [Android](https://go.microsoft.com/fwlink/?Linkid=825072) a [iOS](https://go.microsoft.com/fwlink/?Linkid=825073).

>[!NOTE]
>Není nutné použít aplikaci Microsoft Authenticator. Pokud už používáte jinou ověřovací aplikaci, můžete nadále používat ho.

1. Vyberte **mobilní aplikace** z rozevíracího seznamu.
2. Vyberte buď **dostávat oznámení o ověření** nebo **použít ověřovací kód**a pak vyberte **nastavit**.

   ![Další bezpečnostní ověření obrazovky](./media/multi-factor-authentication-end-user-first-time/mobileapp.png)

3. Na telefonu nebo tabletu, otevřete aplikaci a vyberte **+** přidat účet. (Na zařízeních s Androidem, vyberte tři tečky, pak **přidat účet**.)
4. Určete, jestli chcete přidat pracovní nebo školní účet. Otevře se skener kódů QR na váš telefon. Pokud vaše kamera nepracuje správně, můžete vybrat ručně zadat informace o vaší společnosti. Další informace najdete v tématu [ručně přidat účet](#add-an-account-manually).  
5. Naskenujte obrázek kódu QR, které se zobrazovalo obrazovku pro konfiguraci mobilní aplikace.  Vyberte **provádí** zavřete obrazovky kód QR.  

   ![Obrazovka kód QR](./media/multi-factor-authentication-end-user-first-time/scan2.png)

6. Po dokončení aktivace na telefonu vyberte **Kontaktujte mě**.  Tento krok na váš telefon odešle oznámení nebo ověřovací kód. Vyberte **ověřte**.  
7. Pokud vaše společnost vyžaduje, PIN kód pro schvalování ověřování přihlášení, zadejte ji.

   ![Pole pro zadání kódu PIN](./media/multi-factor-authentication-end-user-first-time/scan3.png)

8. Po dokončení zadávání kódu PIN, vyberte **Zavřít**. V tomto okamžiku by měl být ověřování úspěšné.
9. Doporučujeme vám, zadejte číslo svého mobilního telefonu v případě, že ztratíte přístup k mobilní aplikaci. Vyberte svou zemi z rozevíracího seznamu a zadejte své mobilní telefonní číslo do pole vedle názvu země. Vyberte **Další**.
10. V tomto okamžiku budete vyzváni k nastavení hesla aplikací pro neprohlížečové aplikace, jako je například Outlook 2010 nebo starší, nebo nativní e-mailové aplikace na zařízení Apple. Je to proto, že některé aplikace nepodporují dvoustupňové ověřování. Pokud tyto aplikace nepoužíváte, klikněte na tlačítko **Hotovo** a přeskočit zbývající kroky.
11. Pokud tyto aplikace používáte, kopírovat heslo aplikace k dispozici a vložte ho do své aplikace namísto hesla regulární. Můžete použít stejné heslo aplikace pro více aplikací. Další informace [pomoct s hesly aplikací].
12. Klikněte na **Done** (Hotovo).

### <a name="add-an-account-manually"></a>Ručně přidat účet
Pokud chcete přidat účet do mobilní aplikace ručně, namísto použití čtečky QR, postupujte podle těchto kroků.

1. Vyberte **ručně zadejte účet** tlačítko.  
2. Zadejte kód a adresu URL, která jsou k dispozici na stejné stránce, které vám poskytnou čárového kódu. Tyto informace ukládá **kód** a **URL** polí v mobilní aplikaci.

    ![Nastavení](./media/multi-factor-authentication-end-user-first-time/barcode2.png)
3. Po dokončení aktivace vyberte **Kontaktujte mě**. Tento krok na váš telefon odešle oznámení nebo ověřovací kód. Vyberte **ověřte**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Mobilní telefon používat jako způsob kontaktu
1. Vyberte **telefon pro ověření** z rozevíracího seznamu.  

    ![Nastavení](./media/multi-factor-authentication-end-user-first-time/phone.png)  
2. Vyberte svou zemi z rozevíracího seznamu a zadejte své mobilní telefonní číslo.
3. Vyberte metodu, kterou chcete použít s mobilním telefonem - textová zpráva nebo volání.
4. Vyberte **Kontaktujte mě** k ověření svého telefonního čísla. V závislosti na vybraném režimu jsme odeslala textová zpráva nebo volání. Postupujte podle pokynů na obrazovce a potom vyberte **ověřte**.
5. V tomto okamžiku budete vyzváni k nastavení hesla aplikací pro neprohlížečové aplikace, jako je například Outlook 2010 nebo starší, nebo nativní e-mailové aplikace na zařízení Apple. Je to proto, že některé aplikace nepodporují dvoustupňové ověřování. Pokud tyto aplikace nepoužíváte, klikněte na tlačítko **Hotovo** a přeskočit zbývající kroky.
6. Pokud tyto aplikace používáte, kopírovat heslo aplikace k dispozici a vložte ho do své aplikace namísto hesla regulární. Můžete použít stejné heslo aplikace pro více aplikací. Další informace [pomoct s hesly aplikací].
7. Klikněte na **Done** (Hotovo).

## <a name="use-your-office-phone-as-the-contact-method"></a>Použijte váš telefon do kanceláře jako způsob kontaktu
1. Vyberte **telefonní číslo do kanceláře** z rozevíracího seznamu  

    ![Nastavení](./media/multi-factor-authentication-end-user-first-time/office.png)  
2. Pole telefonního čísla se automaticky vyplní vaše kontaktní informace společnosti. Pokud je číslo chybné nebo nebylo nalezeno, požádejte správce, aby provádět změny.
3. Vyberte **Kontaktujte mě** ověřit vaše telefonní číslo a My se zavolejte na číslo. Postupujte podle pokynů na obrazovce a potom vyberte **ověřte**.
4. V tomto okamžiku budete vyzváni k nastavení hesla aplikací pro neprohlížečové aplikace, jako je například Outlook 2010 nebo starší, nebo nativní e-mailové aplikace na zařízení Apple. Je to proto, že některé aplikace nepodporují dvoustupňové ověřování. Pokud tyto aplikace nepoužíváte, klikněte na tlačítko **Hotovo** a přeskočit zbývající kroky.
5. Pokud tyto aplikace používáte, kopírovat heslo aplikace k dispozici a vložte ho do své aplikace namísto hesla regulární. Můžete použít stejné heslo aplikace pro více aplikací. Další informace najdete v tématu [co jsou hesla aplikací](multi-factor-authentication-end-user-app-passwords.md).
6. Klikněte na **Done** (Hotovo).

## <a name="next-steps"></a>Další postup
* Změňte upřednostňované možnosti a [spravovat nastavení pro dvoustupňové ověřování.](multi-factor-authentication-end-user-manage-settings.md)
* Nastavit [hesla aplikací](multi-factor-authentication-end-user-app-passwords.md) nativní pro aplikace pro zařízení, ve kterých se dvoustupňové ověřování.
* Podívejte se [aplikaci Microsoft Authenticator](user-help-auth-app-download-install.md) rychlé, zabezpečené ověřování i v případě, že nemáte služby buňky.
