---
title: Nastavit dvoustupňové ověřování pro pracovní nebo školní účet | Microsoft Docs
description: 'Pokud vaše společnost konfiguruje Azure Multi-Factor Authentication, zobrazí se výzva k zaregistrovat pro dvoustupňové ověření. Zjistěte, jak ho nastavit. '
services: multi-factor-authentication
keywords: jak používat azure, active directory v cloudu, služby active directory kurzu
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.reviewer: richagi
ms.assetid: 46f83a6a-dbdd-4375-8dc4-e7ea77c16357
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: ec96243a880272a67ed960d50baba43dba8ec207
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2018
ms.locfileid: "28201251"
---
# <a name="set-up-my-account-for-two-step-verification"></a>Nastavit účtu pro dvoustupňové ověření
Dvoustupňové ověření je na další bezpečnostní krok, který pomáhá chránit váš účet tak, že těžší jiní proniknout. Pokud přečtení tohoto článku pravděpodobně tu e-mailu ze svého pracovního nebo školního správce o službě Multi-Factor Authentication. Nebo možná pokusili přihlásit a zobrazí chybové hlášení výzvou k nastavení dalšího ověření zabezpečení. Pokud je to tento případ **nemůžete se přihlásit před dokončením procesu automatické registrace**.

Tento článek vám pomůže nastavit vaše **pracovní nebo školní účet**. Pokud chcete zapnout dvoustupňové ověřování pro vlastní, osobní účet Microsoft, přečtěte si téma [o dvoustupňovém ověřování](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="set-up-your-account"></a>Nastavení účtu

Při podpoře společnosti vyžaduje, abyste začít používat dvoustupňové ověřování, na obrazovce o tom, že **váš správce vyžaduje, abyste nastavili tento účet další bezpečnostní ověření** se zobrazí:

![Nastavení](./media/multi-factor-authentication-end-user-first-time/first.png)

Chcete-li začít, vyberte **nyní nastavit.**

Pokud se nezobrazí na obrazovku takto při přihlášení, postupujte podle pokynů v [spravovat nastavení pro dvoustupňové ověření](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page) najděte stránku nastavení, kde můžete spravovat vaše možnosti ověření. 

## <a name="decide-how-you-want-to-verify-your-sign-ins"></a>Rozhodněte, jak chcete ověřit vaše přihlášení

První otázku v procesu registrace je, jak chcete, abychom vás kontaktovali. Podívejte se na možnosti v tabulce a pokud chcete přejít na kroky nastavení pro každou metodu použijte odkazy.

| Způsob kontaktu | Popis |
| --- | --- |
| [Mobilní aplikace](#use-a-mobile-app-as-the-contact-method) |- **Přijímejte oznámení pro ověření.** Tato možnost odešle oznámení do aplikace Authenticator na tablet nebo smartphone. Zobrazit oznámení a pokud je oprávněné, vyberte **ověřit** v aplikaci. Vaše škola nebo pracoviště může vyžadovat zadání kódu PIN, než ověřování.<br>- **Použijte ověřovací kód.** V tomto režimu ověřovací aplikace generuje ověřovací kód, který aktualizuje každých 30 sekund. Zadejte aktuální ověřovací kód v rozhraní přihlášení.<br>Je k dispozici pro aplikaci Microsoft Authenticator [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594), a [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071). |
| [Volání na mobilní telefon nebo text.](#use-your-mobile-phone-as-the-contact-method) |- **Telefonní hovor** umístí automatický hlasový hovor na telefonní číslo, které zadáte. Odpovězte volání a stisknutím klávesy # v klávesnici telefonu provede ověření.<br>- **Textová zpráva** ukončí textovou zprávu s ověřovacím kódem. Následující řádku v textu odpovědi na textovou zprávu nebo zadejte ověřovací kód zadat do rozhraní přihlášení. |
| [Office telefonního hovoru](#use-your-office-phone-as-the-contact-method) |Umístí automatický hlasový hovor na telefonní číslo, které zadáte. Odpovězte hovor a stiskem tlačítka # na klávesnici telefonu provede ověření. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Použití mobilní aplikace jako způsob kontaktu
Pomocí této metody vyžaduje instalaci ověřovací aplikaci na telefonu nebo tabletu. Kroky v tomto článku jsou založeny na aplikaci Microsoft Authenticator, která je k dispozici pro [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), a [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

1. Vyberte **mobilní aplikace** z rozevíracího seznamu.
2. Vyberte buď **dostávat oznámení pro ověření** nebo **použít ověřovací kód**, pak vyberte **nastavit**.

   ![Další bezpečnostní ověření obrazovky](./media/multi-factor-authentication-end-user-first-time/mobileapp.png)

3. Na telefonu nebo tabletu, otevřete aplikaci a vyberte  **+**  přidat účet. (Na zařízeních s Androidem, vyberte se třemi tečkami, potom **přidejte účet**.)
4. Zadejte, zda chcete přidat pracovní nebo školní účet. Otevře se skeneru kód QR na váš telefon. Pokud svůj fotoaparát nepracuje správně, můžete vybrat ručně zadat informace o vaší společnosti. Další informace najdete v tématu [ručně přidat účet](#add-an-account-manually).  
5. Naskenujte obrázek kódu QR, který se objevil se na obrazovce konfigurace mobilní aplikace.  Vyberte **provádí** zavřete obrazovce kód QR.  

   ![Obrazovka kód QR](./media/multi-factor-authentication-end-user-first-time/scan2.png)

6. Po dokončení aktivace v telefonu, vyberte **kontaktujte mi**.  Tento krok odešle oznámení nebo ověřovací kód na váš telefon. Vyberte **ověřte**.  
7. Pokud vaše společnost vyžaduje kód PIN pro schválení ověření přihlášení, zadejte její název.

   ![Pole pro zadání kódu PIN](./media/multi-factor-authentication-end-user-first-time/scan3.png)

8. Po dokončení zadání kódu PIN, vyberte **Zavřít**. Ověření v tomto okamžiku by měla být úspěšné.
9. Doporučujeme, abyste zadali číslo svého mobilního telefonu v případě, že byste ztratili přístup k mobilní aplikaci. Vaší země z rozevíracího seznamu a zadejte své mobilní telefonní číslo do pole vedle názvu země. Vyberte **Next** (Další).
10. V tomto okamžiku budete vyzváni k nastavení hesla aplikací pro neprohlížečové aplikace, jako je například Outlook 2010 nebo starší nebo nativní e-mailové aplikace na zařízení Apple. Tato akce je vzhledem k tomu, že některé aplikace nepodporují dvoustupňové ověřování. Pokud nepoužijete těchto aplikací, klikněte na tlačítko **provádí** a přeskočte zbytek tyto kroky.
11. Pokud používáte tyto aplikace, kopírování heslo aplikace zadat a vložte jej do vaší aplikace místo regulární heslo. Stejné heslo aplikace můžete použít pro více aplikací. Další informace najdete [pomoci s hesly aplikací].
12. Klikněte na **Done** (Hotovo).

### <a name="add-an-account-manually"></a>Ručně přidat účet
Pokud chcete přidat účet do mobilní aplikace ručně, místo použití čtečky QR, postupujte podle těchto kroků.

1. Vyberte **ručně zadejte účet** tlačítko.  
2. Zadejte kód a adresu URL, která jsou k dispozici na stejné stránce zobrazující čárový kód. Tyto informace je třeba do **kód** a **URL** políček na mobilní aplikaci.

    ![Nastavení](./media/multi-factor-authentication-end-user-first-time/barcode2.png)
3. Po dokončení aktivace vyberte **kontaktujte mi**. Tento krok odešle oznámení nebo ověřovací kód na váš telefon. Vyberte **ověřte**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Pomocí mobilního telefonu jako způsob kontaktu
1. Vyberte **telefon pro ověření** z rozevíracího seznamu.  

    ![Nastavení](./media/multi-factor-authentication-end-user-first-time/phone.png)  
2. Zvolte vaší země z rozevíracího seznamu a zadejte své mobilní telefonní číslo.
3. Vyberte metodu, kterou chcete použít s mobilním telefonem - text nebo volání.
4. Vyberte **kontaktujte mi** k ověření svého telefonního čísla. V závislosti na režimu, které jste vybrali nemůžeme vám pošleme text nebo zavolat. Postupujte podle pokynů na obrazovce a potom vyberte **ověřte**.
5. V tomto okamžiku budete vyzváni k nastavení hesla aplikací pro neprohlížečové aplikace, jako je například Outlook 2010 nebo starší nebo nativní e-mailové aplikace na zařízení Apple. Tato akce je vzhledem k tomu, že některé aplikace nepodporují dvoustupňové ověřování. Pokud nepoužijete těchto aplikací, klikněte na tlačítko **provádí** a přeskočte zbytek postupu.
6. Pokud používáte tyto aplikace, kopírování heslo aplikace zadat a vložte jej do vaší aplikace místo regulární heslo. Stejné heslo aplikace můžete použít pro více aplikací. Další informace najdete [pomoci s hesly aplikací].
7. Klikněte na **Done** (Hotovo).

## <a name="use-your-office-phone-as-the-contact-method"></a>Použijte váš telefon do kanceláře jako způsob kontaktu
1. Vyberte **Telefon do kanceláře** z rozevíracího seznamu  

    ![Nastavení](./media/multi-factor-authentication-end-user-first-time/office.png)  
2. Pole telefonního čísla je automaticky vyplněno své kontaktní informace společnosti. Pokud je číslo nesprávnou nebo chybějící, požádejte správce, aby změny.
3. Vyberte **kontaktujte mi** ověřit vaše telefonní číslo a My zavolá vaše číslo. Postupujte podle pokynů na obrazovce a potom vyberte **ověřte**.
4. V tomto okamžiku budete vyzváni k nastavení hesla aplikací pro neprohlížečové aplikace, jako je například Outlook 2010 nebo starší nebo nativní e-mailové aplikace na zařízení Apple. Tato akce je vzhledem k tomu, že některé aplikace nepodporují dvoustupňové ověřování. Pokud nepoužijete těchto aplikací, klikněte na tlačítko **provádí** a přeskočte zbytek postupu.
5. Pokud používáte tyto aplikace, kopírování heslo aplikace zadat a vložte jej do vaší aplikace místo regulární heslo. Stejné heslo aplikace můžete použít pro více aplikací. Další informace najdete v tématu [co jsou hesla aplikací](multi-factor-authentication-end-user-app-passwords.md).
6. Klikněte na **Done** (Hotovo).

## <a name="next-steps"></a>Další postup
* Změňte upřednostňované možnosti a [spravovat nastavení pro dvoustupňové ověření](multi-factor-authentication-end-user-manage-settings.md)
* Nastavit [hesla aplikací](multi-factor-authentication-end-user-app-passwords.md) pro nativní zařízení s aplikací, které nepodporují dvoustupňové ověřování.
* Podívejte se [aplikaci Microsoft Authenticator](microsoft-authenticator-app-how-to.md) fast, zabezpečení ověřování i v případě, že nemáte buňky služby.

