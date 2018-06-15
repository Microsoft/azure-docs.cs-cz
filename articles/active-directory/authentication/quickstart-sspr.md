---
title: Rychlý start pro samoobslužné resetování hesla – Azure Active Directory
description: Rychlé nasazené samoobslužného resetování hesla Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 856ae994e49f809ca2ef906ba6cdc7f8624dd201
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33870018"
---
# <a name="azure-ad-self-service-password-reset-rapid-deployment"></a>Rychlé nasazení samoobslužného resetování hesla Azure AD

> [!IMPORTANT]
> **Jste tady, protože máte potíže s přihlášením?** Pokud ano, přečtěte si téma [Nápověda k zapomenutému heslu Azure AD](../active-directory-passwords-update-your-own-password.md).

Samoobslužné resetování hesla nabízí správcům IT jednoduchý způsob, jak umožnit uživatelům resetovat nebo odemknout svá hesla nebo účty. Tento systém zahrnuje generování podrobných sestav, které sledují, kdy uživatelé k systému přistupují, spolu s oznámeními, která upozorňují na zneužití.

Tento průvodce předpokládá, že již máte funkčního tenanta Azure Active Directory (Azure AD) ve zkušební verzi nebo s licencí. Pokud potřebujete pomoc s nastavením Azure AD, přečtěte si téma [Začínáme s Azure AD](../get-started-azure-ad.md).

## <a name="enable-sspr-for-your-azure-ad-tenant"></a>Povolení SSPR pro tenanta Azure AD

> [!VIDEO https://www.youtube.com/embed/Pa0eyqjEjvQ]

1. Ve svém stávajícím tenantovi Azure AD na portálu **Azure Portal** vyberte v části **Azure Active Directory** volbu **Resetování hesla**.

2. Na stránce **Vlastnosti** v části **Samoobslužné resetování hesla povoleno** zvolte jednu z následujících možností:
   * **Nikdo:** Funkci samoobslužného resetování hesla nemůže využívat nikdo.
   * **Vybraní:** Funkci samoobslužného resetování hesla můžou využívat jenom členové určité skupiny Azure AD, kterou zvolíte. Doporučujeme definovat skupinu uživatelů a toto nastavení použít při nasazení této funkce pro testování konceptu. Vnoření skupin zabezpečení se v tomto případě podporuje.
   * **Všichni:** Funkci samoobslužného resetování hesla můžou využívat všichni uživatelé s účty ve vašem tenantovi Azure AD. Toto nastavení doporučujeme použít, až budete po dokončení testování konceptu připraveni nasadit tuto funkci do celého tenanta.

   > [!IMPORTANT]
   > Účty správců Azure budou mít vždycky možnost resetovat svá hesla bez ohledu na nastavení této možnosti. 

3. Na stránce **Metody ověřování** zvolte následující možnosti:
   * **Počet metod požadovaných k resetování:** Podporujeme minimálně jednu nebo maximálně dvě.
   * **Metody dostupné pro uživatele:** Potřebujeme alespoň jednu, ale nikdy neuškodí mít k dispozici jednu možnost navíc.
      * **E-mail:** Odešle e-mail s kódem na nastavenou ověřovací e-mailovou adresu uživatele.
      * **Mobilní telefon:** Umožňuje uživateli obdržet hovor nebo zprávu s kódem na nastavené číslo mobilního telefonu.
      * **Telefon do kanceláře:** Zavolá uživateli na nastavené telefonní číslo do kanceláře a sdělí mu kód.
      * **Bezpečnostní otázky:** Vyžadují, abyste zvolili:
         * **Počet otázek požadovaných k registraci:** Minimum pro úspěšnou registraci. Uživatel může odpovědět na více otázek a vytvořit tak fond, ze kterého se otázky budou načítat. Tuto možnost můžete nastavit na tři až pět otázek a tato hodnota musí být vyšší nebo rovna počtu otázek požadovaných k resetování hesla. Uživatelé můžou přidat vlastní otázky, pokud při výběru bezpečnostních otázek vyberou tlačítko **Vlastní**.
         * **Počet otázek požadovaných k resetování:** Můžete nastavit na tři až pět otázek, které je třeba správně zodpovědět, než bude umožněno resetování nebo odemknutí hesla uživatele.
            
    ![Ověřování][Authentication]

4. Doporučené: V části **Přizpůsobení** můžete změnit odkaz **Kontaktujte správce**, aby odkazoval na stránku nebo e-mailovou adresu, kterou zadáte. Doporučujeme nastavit tento odkaz na e-mailovou adresu nebo web, které vaši uživatelé již používají pro dotazy na podporu.

5. Volitelné: Stránka **Registrace** poskytuje správcům tyto možnosti:
   * Při přihlášení vyžadovat registraci uživatelů.
   * Nastavit počet dnů před vyzváním uživatelů k potvrzení ověřovacích údajů.

6. Volitelné: Stránka **Oznámení** poskytuje správcům tyto možnosti:
   * Upozornit uživatele na resetování hesla.
   * Upozornit všechny správce na resetování hesla jiného správce.

Právě jste pro svého tenanta Azure AD nakonfigurovali samoobslužné resetování hesla. Uživatelé teď mohou použít pokyny uvedené v článcích [Registrace pro samoobslužné resetování hesla](../active-directory-passwords-reset-register.md) a [Resetování nebo změna hesla](../active-directory-passwords-update-your-own-password.md) a aktualizovat svoje heslo bez zásahu správce. Tady můžete skončit, pokud pracujete jenom v cloudu. Nebo můžete pokračovat k další části a nakonfigurovat synchronizaci hesel s místní doménou Active Directory.

> [!TIP]
> Otestujte samoobslužné resetování hesla pomocí uživatele, a ne správce, protože Microsoft pro účty správců Azure vynucuje požadavky na silné ověřování. Další informace týkající se zásad hesel správců najdete v našem článku o [zásadách hesel](concept-sspr-policy.md#administrator-password-policy-differences).

## <a name="configure-synchronization-to-an-existing-identity-source"></a>Konfigurace synchronizace s existujícím zdrojem identit

Pokud chcete povolit místní synchronizaci identit s Azure AD, je nutné na serveru ve vaší organizaci nainstalovat a nakonfigurovat službu [Azure AD Connect](./../connect/active-directory-aadconnect.md). Tato aplikace se stará o synchronizaci uživatelů a skupin mezi vaším existujícím zdrojem identit a vaším tenantem Azure AD. Další informace naleznete v tématu:

* [Upgrade z nástroje DirSync nebo z Azure AD Sync na Azure AD Connect](./../connect/active-directory-aadconnect-dirsync-deprecated.md)
* [Začínáme se službou Azure AD Connect s použitím expresního nastavení](./../connect/active-directory-aadconnect-get-started-express.md)
* [Nakonfigurujte zpětný zápis hesel](howto-sspr-writeback.md#configure-password-writeback), aby se hesla z Azure AD zapsala zpátky do místního adresáře.

### <a name="on-premises-policy-change"></a>Změna místních zásad

Pokud synchronizujete uživatele z místní domény Active Directory a chcete jim okamžitě povolit resetováních vlastních hesel, v místních zásadách hesel proveďte následující změnu:

1. Přejděte do **Konfigurace počítače** > **Zásady** > **Nastavení systému Windows** > **Nastavení zabezpečení**  >  **Zásady účtů** > **Zásady hesel**.

2. Nastavte **Minimální stáří hesla** na **0 dnů**.

Toto nastavení zabezpečení určuje dobu (ve dnech), po kterou se heslo musí používat, než ho uživatel může změnit. Pokud nastavíte minimální dobu používání na **0 dnů**, uživatelé můžou použít samoobslužné resetování hesla, pokud jejich hesla změní týmy podpory.

![Zásady][Policy]

## <a name="disable-self-service-password-reset"></a>Zakázání samoobslužného resetování hesla

Zakázání samoobslužného resetování hesla je snadné. Otevřete svého tenanta Azure AD, přejděte do **Resetování hesla** > **Vlastnosti** a pak v části **Samoobslužné resetování hesla povoleno** vyberte **Nikdo**.

### <a name="learn-more"></a>Další informace
V následujících článcích najdete další informace o resetování hesla prostřednictvím Azure AD:

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../active-directory-passwords-update-your-own-password.md)
* [Registrace samoobslužného resetování hesla](../active-directory-passwords-reset-register.md)
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Jaká data používá SSPR a která data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco poškozené. Jak řešit problémy SSPR?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak pro vaše uživatele nakonfigurovat samoobslužné resetování hesla. Pokud chcete dokončit tento postup, pokračujte na Azure Portal:

> [!div class="nextstepaction"]
> [Povolení samoobslužného resetování hesla](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Dostupné metody ověřování Azure AD a požadované množství"
[Policy]: ./media/quickstart-sspr/password-policy.png "Nastavení místních zásad skupiny hesel na 0 dnů"

