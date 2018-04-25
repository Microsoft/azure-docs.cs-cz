---
title: Příručka pro nasazení samoobslužného resetování hesla – Azure Active Directory
description: Tipy pro úspěšné zavedení samoobslužného resetování hesla Azure AD
services: active-directory
keywords: ''
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: e34bf6ec106976c1c3aab0f2b5c4ebf4b6ccc54b
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-successfully-roll-out-self-service-password-reset"></a>Úspěšné zavedení samoobslužného resetování hesla

K zajištění hladkého zavedení funkce samoobslužného resetování hesla (SSPR) služby Azure Active Directory (Azure AD) většina zákazníků provede následující kroky:

1. [Povolte resetování hesla ve svém adresáři](quickstart-sspr.md).
2. [Nakonfigurujte oprávnění místní služby Active Directory pro zpětný zápis hesla](howto-sspr-writeback.md#active-directory-permissions).
3. [Nakonfigurujte zpětný zápis hesel](howto-sspr-writeback.md#configure-password-writeback), aby se hesla z Azure AD zapsala zpátky do místního adresáře.
4. [Přiřaďte a ověřte požadované licence](concept-sspr-licensing.md).
5. Určete, jestli chcete provést postupné zavedení. Pokud chcete samoobslužné resetování hesla zavádět postupně, můžete omezit přístup jenom na konkrétní skupinu uživatelů a provést s nimi pilotní nasazení. Pokud chcete provést zavedení pro konkrétní skupinu, nastavte přepínač **Samoobslužné resetování hesla povoleno** na **Vybrané** a vyberte skupinu zabezpečení, která má mít možnost použít resetování hesel. 
6. Naplňte [ověřovací data](howto-sspr-authenticationdata.md) nutná k registraci uživatelů, například jejich telefonní číslo do kanceláře, mobilní telefon a alternativní e-mailovou adresu.
7. [Upravte prostředí přihlášení Azure AD, aby obsahovalo značku vaší společnosti](concept-sspr-customization.md).
8. Naučte vaše uživatele, jak používat samoobslužné resetování hesla. Pošlete jim pokyny s vysvětlením postupu registrace a resetování hesel.
9. Určete, jestli se registrace má vynucovat. K vynucení registrace se můžete rozhodnout kdykoli. Můžete také po uživatelích požadovat, aby po určité době znovu potvrdili svoje ověřovací informace.
10. Využijte možnosti vytváření sestav. V průběhu času můžete zkontrolovat registraci uživatelů a využití pomocí [možností vytváření sestav, které poskytuje Azure AD](howto-sspr-reporting.md).
11. Povolte resetování hesla. Až budete připravení, povolte resetování hesla pro všechny uživatele tak, že nastavíte přepínač **Samoobslužné resetování hesla povoleno** na **Všichni**. 

   > [!NOTE]
   > Změnou této možnosti z vybrané skupiny na Všichni nedojde k zneplatnění stávajících ověřovacích dat, která uživatel zaregistroval jako součást testovací skupiny. Nakonfigurovaní uživatelé se zaregistrovanými platnými ověřovacími daty budou fungovat i nadále.

12. [Povolte uživatelům Windows 10 resetování hesla na přihlašovací obrazovce](tutorial-sspr-windows.md).

   > [!IMPORTANT]
   > K otestování samoobslužného resetování hesla využijte uživatele, a ne správce, protože Microsoft pro účty správců Azure vynucuje požadavky na silné ověřování. Další informace týkající se zásad hesel správců najdete v našem článku o [zásadách hesel](concept-sspr-policy.md#administrator-password-policy-differences).

## <a name="email-based-rollout"></a>Zavedení přes e-mail

Nejjednodušším způsobem, jak uživatele přimět používat samoobslužné resetování hesla, pro mnoho zákazníků je e-mailová kampaň se snadno použitelnými pokyny. [Vytvořili jsme tři jednoduché e-maily, které můžete použít jako šablony pro pomoc se zaváděním](https://www.microsoft.com/download/details.aspx?id=56768).

* **Připravuje se:** Šablona e-mailu, kterou použijete během týdnů nebo dnů před zavedením, abyste uživatelům sdělili, že musí něco udělat.
* **Nově dostupné:** Šablona e-mailu, kterou použijete v den spuštění programu, abyste přiměli uživatele k registraci a potvrzení ověřovacích dat. Pokud se uživatelé teď zaregistrují, mohou samoobslužné resetování hesla v případě potřeby použít.
* **Připomenutí registrace:** Šablona e-mailu, kterou použijete několik dnů nebo týdnů po nasazení, abyste uživatelům připomněli, že se mají zaregistrovat a potvrdit ověřovací data.

![E-mail][Email]

## <a name="create-your-own-password-portal"></a>Vytvoření vlastního portálu hesel

Řada zákazníku volí hostování webové stránky a vytvoření kořenového záznamu DNS, například https://passwords.contoso.com. Tuto stránku naplní odkazy na následující informace:

* [Portál pro resetování hesel Azure AD – https://aka.ms/sspr](https://aka.ms/sspr)
* [Portál pro registraci k resetování hesel Azure AD – https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
* [Portál pro změnu hesel Azure AD – https://account.activedirectory.windowsazure.com/ChangePassword.aspx](https://account.activedirectory.windowsazure.com/ChangePassword.aspx)
* Další informace pro konkrétní organizaci

Do e-mailů nebo letáků, které rozesíláte, můžete vložit zapamatovatelnou adresu URL obsahující značku, na kterou uživatelé mohou přejít v případě, že tyto služby potřebují využít. Vytvořili jsme pro vás [ukázkovou stránku pro resetování hesla](https://github.com/ajamess/password-reset-page), kterou můžete použít a přizpůsobit potřebám vaší organizace.

## <a name="use-enforced-registration"></a>Použití vynucené registrace

Pokud chcete, aby se vaši uživatelé zaregistrovali k resetování hesla, můžete požadovat, aby se zaregistrovali při přihlášení s využitím Azure AD. Tuto možnost můžete povolit v podokně **Resetování hesla** vašeho adresáře tak, že na kartě **Registrace** povolíte možnost **Vyžadovat od uživatelů při přihlášení registraci**.

Správci mohou vyžadovat, aby se uživatelé po určité době registrovali znovu. Možnost **Počet dní, než se uživatelům zobrazí výzva k potvrzení jejich ověřovacích informací** mohou nastavit na hodnotu 0 až 730 dnů.

Pokud tuto možnost povolíte, uživatelům se při přihlášení zobrazí zpráva s informací, že po nich správce vyžaduje potvrzení ověřovacích informací.

## <a name="populate-authentication-data"></a>Naplnění ověřovacích dat

Měli byste [naplnit ověřovací data pro vaše uživatele](howto-sspr-authenticationdata.md). Uživatelé se tak nebudou muset registrovat pro resetování hesla, dokud nebudou mít možnost používat SSPR. Dokud uživatelé budou mít ověřovací data splňující zásady resetování hesel, které jste definovali, budou mít možnost si svoje hesla resetovat.

## <a name="disable-self-service-password-reset"></a>Zakázání samoobslužného resetování hesla

Zakázání samoobslužného resetování hesla je snadné. Otevřete svého tenanta Azure AD, přejděte do **Resetování hesla** > **Vlastnosti** a pak v části **Samoobslužné resetování hesla povoleno** vyberte **Nikdo**.

## <a name="next-steps"></a>Další kroky

* [Resetování nebo změna hesla](../active-directory-passwords-update-your-own-password.md)
* [Registrace samoobslužného resetování hesla](../active-directory-passwords-reset-register.md)
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Jaká data používá SSPR a která data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco poškozené. Jak řešit problémy SSPR?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která nebyla zodpovězena jinde](active-directory-passwords-faq.md)

[Email]: ./media/howto-sspr-deployment/sspr-emailtemplates.png "Přizpůsobení těchto e-mailových šablon podle požadavků vaší organizace"
