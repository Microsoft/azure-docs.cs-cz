---
title: Příručka pro nasazení samoobslužného resetování hesla – Azure Active Directory
description: Tipy pro úspěšné zavedení samoobslužného resetování hesla Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: f786a20f61c8851c1d50a89edd392b9b974db076
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622316"
---
# <a name="how-to-successfully-roll-out-self-service-password-reset"></a>Úspěšné zavedení samoobslužného resetování hesla

K zajištění hladkého zavedení funkce samoobslužného resetování hesla (SSPR) služby Azure Active Directory (Azure AD) většina zákazníků provede následující kroky:

> [!VIDEO https://www.youtube.com/embed/OZn5btP6ZXw]

1. Dokončení pilotní zavedení s malou část vaší organizace.
   * Informace o tom, jak pilotního nasazení najdete v [kurz: dokončení pilotní resetování hesla pomocí samoobslužné služby Azure AD zavádět](tutorial-sspr-pilot.md).
1. Informujte pracovníky helpdesk.
   * Jak se bude pomáhají zajistit uživatelům?
   * Vynutí uživatelům používat samoobslužné resetování HESLA a helpdesk pomoci uživatelům, aby?
   * Mít zadané adresy URL pro registraci a resetování?
      * Registrace:  https://aka.ms/ssprsetup
      * Obnovení: https://aka.ms/sspr
1. Naučit vaše uživatele.
   * Následující části tohoto dokumentu se přenášejí prostřednictvím ukázka komunikace portály hesel, vynucování registrace a naplnění ověřovacích dat.
   * Produktová skupina Azure Active Directory vytvořila [podrobný plán nasazení](https://aka.ms/SSPRDeploymentPlan), které organizace mohou při vytváření obchodního případu a plánování nasazení samoobslužného obnovení hesel využít současně s dokumentací, kterou najdou na tomto webu.
1. Povolení samoobslužného resetování hesla pro celou organizaci.
   * Až budete připravení, povolte resetování hesla pro všechny uživatele tak, že nastavíte přepínač **Samoobslužné resetování hesla povoleno** na **Všichni**.

## <a name="sample-communication"></a>Ukázka komunikace

Nejjednodušším způsobem, jak uživatele přimět používat samoobslužné resetování hesla, pro mnoho zákazníků je e-mailová kampaň se snadno použitelnými pokyny. [Vytvořili jsme jednoduché e-maily a další materiály, které můžete použít jako šablony pro pomoc při zavádění](https://www.microsoft.com/download/details.aspx?id=56768):

* **Připravuje se:** Šablona e-mailu, kterou použijete během týdnů nebo dnů před zavedením, abyste uživatelům sdělili, že musí něco udělat.
* **Nově dostupné:** Šablona e-mailu, kterou použijete v den spuštění programu, abyste přiměli uživatele k registraci a potvrzení ověřovacích dat. Pokud se uživatelé teď zaregistrují, mohou samoobslužné resetování hesla v případě potřeby použít.
* **Připomenutí registrace:** Šablona e-mailu, kterou použijete několik dnů nebo týdnů po nasazení, abyste uživatelům připomněli, že se mají zaregistrovat a potvrdit ověřovací data.
* **Samoobslužné resetování HESLA plakáty**: plakáty můžete přizpůsobit a zobrazí kolem vaší organizace v dny a týdnů, až vedoucí a po vaší zavedení.
* **Samoobslužné resetování HESLA tabulky stany**: Tabulka karty můžete umístit v místnosti oběd, místnosti konference, nebo na portál upozorněte své uživatele, pro dokončení registrace.
* **Samoobslužné resetování HESLA nálepky**: šablony nálepku můžete upravit a vytisknout umístit přenosné počítače, monitorování, klávesnice nebo mobilní telefony zapamatovat si, jak pro přístup k samoobslužné resetování HESLA.

![Ukázky e-mailu samoobslužné resetování HESLA][Email]

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

Měli byste zvážit [předem naplnění některých ověřovací data pro vaše uživatele](howto-sspr-authenticationdata.md). Uživatelé se tak nebudou muset registrovat pro resetování hesla, dokud nebudou mít možnost používat SSPR. Dokud uživatelé budou mít ověřovací data splňující zásady resetování hesel, které jste definovali, budou mít možnost si svoje hesla resetovat.

## <a name="disable-self-service-password-reset"></a>Zakázání samoobslužného resetování hesla

Pokud vaše organizace rozhodne zakázání samoobslužného resetování hesla je jednoduchý proces. Otevřete svého tenanta Azure AD, přejděte do **Resetování hesla** > **Vlastnosti** a pak v části **Samoobslužné resetování hesla povoleno** vyberte **Nikdo**. Uživatelé budou stále udržovat své metody ověřování registrované pro budoucí použití.

## <a name="next-steps"></a>Další postup

* [Resetování nebo změna hesla](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrace samoobslužného resetování hesla](../user-help/active-directory-passwords-reset-register.md)
* [Povolit sblížené registraci pro resetování hesla pomocí samoobslužné služby Azure Multi-Factor Authentication a Azure AD](concept-registration-mfa-sspr-converged.md)
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Jaká data používá SSPR a která data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco poškozené. Jak řešit problémy SSPR?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která nebyla zodpovězena jinde](active-directory-passwords-faq.md)

[Email]: ./media/howto-sspr-deployment/sspr-emailtemplates.png "Přizpůsobení těchto e-mailových šablon podle požadavků vaší organizace"
