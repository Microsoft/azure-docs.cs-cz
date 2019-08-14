---
title: Nastavení dvoustupňového ověřování – Azure Active Directory | Microsoft Docs
description: Když vaše společnost konfiguruje Azure Multi-Factor Authentication, budete vyzváni k registraci dvoustupňového ověřování. Přečtěte si, jak ho nastavit.
services: active-directory
keywords: kurz použití Azure Directory, Active Directory v cloudu, kurz služby Active Directory
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75bc067bfe8a98ef2337f368243b3221be1677d6
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949893"
---
# <a name="set-up-my-account-for-two-step-verification"></a>Nastavení účtu pro dvoustupňové ověřování
Dvoustupňové ověřování je dodatečný krok zabezpečení, který pomáhá chránit váš účet tím, že je těžší, aby ostatní uživatelé mohli rušit. Pokud tento článek čtete, pravděpodobně jste od svého pracovního nebo školního správce o službě Multi-Factor Authentication dostali e-mail. Nebo možná jste se pokusili přihlásit a získali zprávu s výzvou k nastavení dalšího ověření zabezpečení. V takovém případě se nemůžete **přihlásit, dokud nedokončíte proces automatického zápisu**.

Tento článek vám pomůže nastavit svůj **pracovní nebo školní účet**. Pokud chcete povolit dvoustupňové ověřování pro vlastní, osobní účet Microsoft, přečtěte si [článek o dvoustupňovém ověřování](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="set-up-your-account"></a>Nastavit účet

Když vaše firemní podpora vyžaduje, abyste začali používat dvoustupňové ověřování, zobrazí se obrazovka s oznámením, že **váš správce vyžaduje, abyste u tohoto účtu nastavili další ověření zabezpečení**:

![Instalace](./media/multi-factor-authentication-end-user-first-time/first.png)

Začněte tím, že vyberete **nastavit hned.**

Pokud se vám po přihlášení nezobrazí žádná obrazovka, postupujte podle pokynů v tématu [Správa nastavení pro dvoustupňové ověřování](multi-factor-authentication-end-user-manage-settings.md#using-the-additional-security-verification-page) a Najděte stránku nastavení, kde můžete spravovat možnosti ověřování.

## <a name="decide-how-you-want-to-verify-your-sign-ins"></a>Rozhodněte, jak chcete ověřit vaše přihlášení.

První otázkou v procesu registrace je to, jak vás chceme kontaktovat. Podívejte se na možnosti v tabulce a pomocí odkazů nastavte kroky pro instalaci jednotlivých metod.

| Způsob kontaktování | Popis |
| --- | --- |
| [Mobilní aplikace](#use-a-mobile-app-as-the-contact-method) |- **Dostanou oznámení k ověření.** Tato možnost vloží oznámení do aplikace ověřovatele na telefonu Smartphone nebo tabletu. Podívejte se na oznámení a pokud je legitimní, vyberte v aplikaci **ověřit** . Vaše práce nebo škola může vyžadovat zadání kódu PIN před ověřením.<br>- **Použijte ověřovací kód.** V tomto režimu aplikace ověřovatele vygeneruje ověřovací kód, který se aktualizuje každých 30 sekund. Do přihlašovacího rozhraní zadejte nejaktuálnější ověřovací kód.<br>Aplikace Microsoft Authenticator je k dispozici pro [Android](https://go.microsoft.com/fwlink/?linkid=866594) a [iOS](https://go.microsoft.com/fwlink/?linkid=866594).|
| [Mobilní telefonní hovor nebo text](#use-your-mobile-phone-as-the-contact-method) |- **Telefonní hovor** vede automatizované hlasové volání na telefonní číslo, které zadáte. Přivolejte volání a stisknutím klávesy # na klávesnici telefonu proveďte ověření.<br>- **Textová zpráva** končí textovou zprávou obsahující ověřovací kód. Po zobrazení výzvy v textu odpovězte buď na textovou zprávu, nebo zadejte ověřovací kód, který jste zadali do přihlašovacího rozhraní. |
| [Volání na telefon do kanceláře](#use-your-office-phone-as-the-contact-method) |Vloží automatizované hlasové volání do telefonního čísla, které zadáte. Přihlaste se ke volání a stisknutím klávesy # na klávesnici telefonu proveďte ověření. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Použití mobilní aplikace jako způsobu kontaktu
Použití této metody vyžaduje, abyste na telefonu nebo tabletu nainstalovali ověřovací aplikaci. Kroky v tomto článku jsou založené na Microsoft Authenticator aplikaci, která je k dispozici pro [Android](https://go.microsoft.com/fwlink/?Linkid=825072) a [iOS](https://go.microsoft.com/fwlink/?Linkid=825073).

>[!NOTE]
>Nemusíte používat aplikaci Microsoft Authenticator. Pokud jste již používali jinou ověřovací aplikaci, můžete ji nadále používat.

1. V rozevíracím seznamu vyberte **mobilní aplikace** .
2. Vyberte buď **přijmout oznámení pro ověření** , nebo **použijte ověřovací kód**, a pak vyberte **nastavit**.

   ![Obrazovka dodatečného ověření zabezpečení](./media/multi-factor-authentication-end-user-first-time/mobileapp.png)

3. Na telefonu nebo tabletu otevřete aplikaci a vyberte možnost **+** přidat účet. (Na zařízeních s Androidem vyberte tři tečky a pak **Přidat účet**.)
4. Zadejte, že chcete přidat pracovní nebo školní účet. Otevře se skener kódu QR v telefonu. Pokud fotoaparát nepracuje správně, můžete vybrat možnost zadat informace o společnosti ručně. Další informace najdete v tématu [Ruční přidání účtu](#add-an-account-manually).  
5. Naskenujte si obrázek kódu QR, který se zobrazil na obrazovce pro konfiguraci mobilní aplikace.  Vyberte **Hotovo** , aby se zavřela obrazovka Code QR.  

   ![Obrazovka kódu QR](./media/multi-factor-authentication-end-user-first-time/scan2.png)

6. Po dokončení aktivace na telefonu vyberte **kontaktovat mě**.  Tento krok pošle oznámení nebo ověřovací kód na váš telefon. Vyberte **ověřit**.  
7. Pokud vaše společnost vyžaduje PIN kód pro schválení ověřování přihlášení, zadejte ji.

   ![Box pro zadání kódu PIN](./media/multi-factor-authentication-end-user-first-time/scan3.png)

8. Po dokončení zadání kódu PIN vyberte **Zavřít**. V tomto okamžiku by mělo být vaše ověření úspěšné.
9. Doporučujeme zadat číslo mobilního telefonu pro případ, že ztratíte přístup k mobilní aplikaci. V rozevíracím seznamu zadejte zemi nebo oblast a do pole vedle názvu země nebo oblasti zadejte číslo mobilního telefonu. Vyberte **Další**.
10. V tomto okamžiku se zobrazí výzva, abyste nastavili hesla aplikací pro neprohlížečové aplikace, jako je například Outlook 2010 nebo starší, nebo nativní e-mailová aplikace na zařízeních Apple. Důvodem je to, že některé aplikace nepodporují dvoustupňové ověřování. Pokud tyto aplikace nepoužíváte, klikněte na **Hotovo** a přeskočte zbývající kroky.
11. Pokud tyto aplikace používáte, zkopírujte zadané heslo aplikace a vložte ho do své aplikace místo běžného hesla. Můžete použít stejné heslo aplikace pro více aplikací. Další informace najdete v tématu [nápovědu s hesly aplikací].
12. Klikněte na **Done** (Hotovo).

### <a name="add-an-account-manually"></a>Přidat účet ručně
Pokud chcete ručně přidat účet do mobilní aplikace namísto použití čtečky QR, postupujte podle těchto kroků.

1. Vyberte tlačítko **zadat účet ručně** .  
2. Zadejte kód a adresu URL, které jsou k dispozici na stejné stránce, která zobrazuje čárový kód. Tato informace se odkazuje do polí **kód** a **Adresa URL** v mobilní aplikaci.

    ![Instalace](./media/multi-factor-authentication-end-user-first-time/barcode2.png)
3. Po dokončení aktivace vyberte **kontaktovat mě**. Tento krok pošle oznámení nebo ověřovací kód na váš telefon. Vyberte **ověřit**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Použijte svůj mobilní telefon jako způsob kontaktu
1. V rozevíracím seznamu vyberte **telefon pro ověření** .  

    ![Instalace](./media/multi-factor-authentication-end-user-first-time/phone.png)  
2. V rozevíracím seznamu vyberte zemi nebo oblast a zadejte číslo mobilního telefonu.
3. Vyberte metodu, kterou upřednostňujete k použití s mobilním telefonem nebo voláním.
4. Vyberte **kontakt mi** a ověřte si své telefonní číslo. V závislosti na vybraném režimu vám pošleme text nebo zavolat. Postupujte podle pokynů uvedených na obrazovce a pak vyberte **ověřit**.
5. V tomto okamžiku se zobrazí výzva, abyste nastavili hesla aplikací pro neprohlížečové aplikace, jako je například Outlook 2010 nebo starší, nebo nativní e-mailová aplikace na zařízeních Apple. Důvodem je to, že některé aplikace nepodporují dvoustupňové ověřování. Pokud tyto aplikace nepoužíváte, klikněte na **Hotovo** a přeskočte zbývající kroky.
6. Pokud tyto aplikace používáte, zkopírujte zadané heslo aplikace a vložte ho do své aplikace místo běžného hesla. Můžete použít stejné heslo aplikace pro více aplikací. Další informace najdete v tématu [nápovědu s hesly aplikací].
7. Klikněte na **Done** (Hotovo).

## <a name="use-your-office-phone-as-the-contact-method"></a>Použít telefon do kanceláře jako způsob kontaktu
1. V rozevíracím seznamu vyberte **telefon do kanceláře** .  

    ![Instalace](./media/multi-factor-authentication-end-user-first-time/office.png)  
2. Pole telefonní číslo se vyplní automaticky kontaktními informacemi společnosti. Pokud je číslo chybné nebo chybí, požádejte správce, aby provedl změny.
3. Pokud si chcete ověřit své telefonní číslo, vyberte **kontakt mi** . vaše číslo budeme volat. Postupujte podle pokynů uvedených na obrazovce a pak vyberte **ověřit**.
4. V tomto okamžiku se zobrazí výzva, abyste nastavili hesla aplikací pro neprohlížečové aplikace, jako je například Outlook 2010 nebo starší, nebo nativní e-mailová aplikace na zařízeních Apple. Důvodem je to, že některé aplikace nepodporují dvoustupňové ověřování. Pokud tyto aplikace nepoužíváte, klikněte na **Hotovo** a přeskočte zbývající kroky.
5. Pokud tyto aplikace používáte, zkopírujte zadané heslo aplikace a vložte ho do své aplikace místo běžného hesla. Můžete použít stejné heslo aplikace pro více aplikací. Další informace najdete v tématu [co jsou hesla aplikací](multi-factor-authentication-end-user-app-passwords.md).
6. Klikněte na **Done** (Hotovo).

## <a name="next-steps"></a>Další postup
* Změňte preferované možnosti a [Spravujte nastavení pro dvoustupňové ověřování](multi-factor-authentication-end-user-manage-settings.md) .
* Nastavte [hesla aplikací](multi-factor-authentication-end-user-app-passwords.md) pro nativní aplikace zařízení, které nepodporují dvoustupňové ověřování.
* Podívejte se na [aplikaci Microsoft Authenticator](user-help-auth-app-download-install.md) pro rychlé a zabezpečené ověřování i v případě, že nemáte službu pro buňky.
