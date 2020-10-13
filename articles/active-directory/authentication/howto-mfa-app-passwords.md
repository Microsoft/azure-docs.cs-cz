---
title: Konfigurace hesel aplikací pro Azure Multi-Factor Authentication – Azure Active Directory
description: Naučte se konfigurovat a používat hesla aplikací pro starší verze aplikací v Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85031896a196dd742868466243dd401345b0bc97
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964498"
---
# <a name="enable-and-use-azure-multi-factor-authentication-with-legacy-applications-using-app-passwords"></a>Povolení a používání služby Azure Multi-Factor Authentication se staršími aplikacemi pomocí hesel aplikací

Některé starší, neprohlížečové aplikace, jako je Office 2010 nebo starší, a Apple Mail před iOS 11 nerozumí pozastavení nebo přerušení procesu ověřování. Pokud je uživatel pro Azure Multi-Factor Authentication povolený a pokusí se použít některou z těchto starších aplikací, které nepoužívají prohlížeč, nemůže se úspěšně ověřit. Pokud chcete používat tyto aplikace zabezpečeným způsobem s povoleným Azure Multi-Factor Authentication pro uživatelské účty, můžete použít hesla aplikací. Tato hesla aplikace nahradila tradiční heslo, aby mohla aplikace obejít službu Multi-Factor Authentication a správně pracovat.

Moderní ověřování se podporuje u klientů systém Microsoft Office 2013 a později. Klienti Office 2013, včetně Outlooku, podporují moderní protokoly ověřování a dají se povolit pro práci se dvěma kroky ověřování. Po povolení klienta se pro klienta nevyžadují hesla aplikací.

V tomto článku se dozvíte, jak povolit a používat hesla aplikací pro starší verze aplikací, které nepodporují výzvy služby Multi-Factor Authentication.

>[!NOTE]
> Hesla aplikací nefungují s použitím zásad vícefaktorového ověřování založeného na podmíněném přístupu a moderního ověřování.

## <a name="overview-and-considerations"></a>Přehled a požadavky

Když je pro Azure Multi-Factor Authentication povolený uživatelský účet, regulární výzvy k přihlášení přeruší požadavek na další ověření. Některé starší aplikace nerozumí tomuto přerušení v procesu přihlašování, takže ověření se nepovede. Pokud chcete zachovat zabezpečení uživatelského účtu a ponechat Azure Multi-Factor Authentication povolené, můžete místo normálního uživatelského jména a hesla použít hesla aplikací. Pokud heslo aplikace použité během přihlašování není k dispozici, zobrazí se žádné další ověřovací výzvy, aby ověření proběhlo úspěšně.

Hesla aplikací se generují automaticky, neurčuje ho uživatel. Díky tomuto automaticky generovanému heslu by útočník mohl odhadnout, takže je bezpečnější. Uživatelé nemusí sledovat hesla nebo je zadávat pokaždé, když jsou hesla aplikace zadána pouze jednou pro každou aplikaci.

Při použití hesel aplikací platí následující požadavky:

* Pro každého uživatele je povolený limit 40 hesel aplikací.
* Aplikace, které ukládá hesla do mezipaměti a používají je v místních scénářích, můžou selhat, protože heslo aplikace není známé mimo pracovní nebo školní účet. Příkladem tohoto scénáře jsou e-maily systému Exchange, které jsou místní, ale Archivovaná pošta je v cloudu. V tomto scénáři stejné heslo nefunguje.
* Po povolení služby Azure Multi-Factor Authentication na účtu uživatele se hesla aplikací dají použít u většiny klientů bez prohlížeče, jako je Outlook a Microsoft Skype pro firmy. Akce správy se ale nedají provádět pomocí hesel aplikací přes neprohlížečové aplikace, jako je Windows PowerShell. Akce se nedají provést, i když má uživatel účet správce.
    * Chcete-li spustit skripty prostředí PowerShell, vytvořte účet služby se silným heslem a nepovolujte účet pro dvoustupňové ověřování.
* Pokud se domníváte, že je uživatelský účet napadený, a odvoláte nebo resetujete heslo k účtu, měli byste taky aktualizovat hesla aplikací. Hesla aplikací nejsou automaticky odvolána, když je heslo nebo resetování hesla uživatelského účtu zrušeno. Uživatel by měl odstranit existující hesla aplikací a vytvořit nové.
   * Další informace najdete v tématu [Vytvoření a odstranění hesel aplikací ze stránky další ověření zabezpečení](../user-help/multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page).

>[!WARNING]
> Hesla aplikací nefungují v hybridních prostředích, kde klienti komunikují s místními i cloudy pro automatické zjišťování koncových bodů. K místnímu ověřování se vyžadují doménová hesla. K ověřování pomocí cloudu se vyžadují hesla aplikací.

### <a name="app-password-names"></a>Názvy hesel aplikací

Názvy hesel aplikací by měly odrážet zařízení, ve kterém se používají. Pokud máte přenosný počítač, který obsahuje neprohlížečové aplikace, jako je Outlook, Word a Excel, vytvořte pro tyto aplikace jedno heslo aplikace s názvem **notebook** . Vytvořte další heslo aplikace s názvem **Desktop** pro stejné aplikace, které běží na stolním počítači.

Doporučuje se vytvořit jedno heslo aplikace na jedno zařízení, nikoli jedno heslo aplikace na aplikaci.

## <a name="federated-or-single-sign-on-app-passwords"></a>Hesla federovaného nebo jednotného přihlašování k aplikacím

Azure AD podporuje federaci nebo jednotné přihlašování (SSO) s místními Active Directory Domain Services (služba AD DS). Pokud je vaše organizace federované s Azure AD a používáte Azure Multi-Factor Authentication, platí následující doporučení k heslům aplikací:

>[!NOTE]
> Následující body se vztahují jenom na zákazníky federovaného (SSO).

* Hesla aplikací ověřuje služba Azure AD, a proto se nepoužívají federace. Federace se aktivně používá jenom při nastavování hesel aplikací.
* Poskytovatel identity (IdP) se nekontaktuje na uživatele federovaného (SSO), na rozdíl od pasivního toku. Hesla aplikací se ukládají v pracovním nebo školním účtu. Pokud uživatel odejde ze společnosti, informace o uživateli se budou předávat do pracovního nebo školního účtu pomocí **DirSync** v reálném čase. Operace zakázat/odstranit může trvat až tři hodiny, než se synchronizuje, což může zpozdit zakázání a odstranění hesla aplikace ve službě Azure AD.
* Nastavení místních Access Control klienta nedodržuje funkce hesla aplikací.
* Pomocí funkce hesla aplikací nejsou k dispozici žádné místní možnosti protokolování ověřování ani auditování.

Některé pokročilé architektury vyžadují kombinaci přihlašovacích údajů pro službu Multi-Factor Authentication s klienty. Tyto přihlašovací údaje můžou zahrnovat uživatelské jméno a heslo školního účtu a hesla aplikací. Požadavky závisí na tom, jak je ověřování prováděno. U klientů, kteří se ověřují v místní infrastruktuře, je nutné zadat uživatelské jméno nebo uživatelské jméno a heslo školního účtu. U klientů, kteří se ověřují v Azure AD, je vyžadováno heslo aplikace.

Předpokládejme například, že máte následující architekturu:

* Vaše místní instance Active Directory je federované s Azure AD.
* Používáte Exchange Online.
* Používáte Skype pro firmy místně.
* Používáte Azure Multi-Factor Authentication.

V tomto scénáři použijete následující přihlašovací údaje:

* Pokud se chcete přihlásit ke službě Skype pro firmy, použijte uživatelské jméno nebo uživatelské jméno a heslo školního účtu.
* Pokud chcete získat přístup k adresáři z klienta Outlooku, který se připojuje k Exchangi Online, použijte heslo aplikace.

## <a name="allow-users-to-create-app-passwords"></a>Umožňuje uživatelům vytvářet hesla aplikací.

Ve výchozím nastavení uživatelé nemůžou vytvářet hesla aplikací. Funkce hesla aplikací musí být povolená, aby je uživatelé mohli používat. Pokud chcete uživatelům poskytnout možnost vytvářet hesla aplikací, proveďte následující kroky:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
2. Vyhledejte a vyberte **Azure Active Directory**a pak zvolte **Uživatelé**.
3. Na navigačním panelu v horní části okna *uživatele* vyberte **Multi-Factor Authentication** .
4. V části Multi-Factor Authentication vyberte **nastavení služby**.
5. Na stránce **nastavení služby** zaškrtněte políčko **dovolit uživatelům vytvářet hesla aplikací pro přihlášení k neprohlížečovým aplikacím** .

    ![Snímek obrazovky Azure Portal, který zobrazuje nastavení služby pro službu Multi-Factor Authentication, aby uživatel mohl použít hesla aplikací](media/concept-authentication-methods/app-password-authentication-method.png)
    
> [!NOTE]
>
> Když zakážete uživatelům vytvářet hesla aplikací, stávající hesla aplikací budou fungovat i nadále. Po zakázání této možnosti ale uživatelé nemůžou tato existující hesla aplikace spravovat ani odstranit.
>
> Když zakážete možnost vytvářet hesla aplikací, doporučujeme [vytvořit zásadu podmíněného přístupu, která zakáže použití starší verze ověřování](../conditional-access/block-legacy-authentication.md). Tento přístup zabraňuje stávajícím heslům aplikace v práci a vynucuje použití moderních metod ověřování.

## <a name="create-an-app-password"></a>Vytvoření hesla aplikace

Když uživatelé dokončí svoji počáteční registraci pro Azure Multi-Factor Authentication, je na konci procesu registrace k dispozici možnost vytvořit hesla aplikací.

Uživatelé můžou po registraci vytvářet taky hesla aplikací. Další informace a podrobné pokyny pro uživatele najdete v tématu [co jsou hesla aplikací v Azure Multi-Factor Authentication?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak uživatelům dovolit, aby se rychle zaregistrovali pro Azure Multi-Factor Authentication, najdete v článku [Přehled registrace informací o zabezpečení](concept-registration-mfa-sspr-combined.md).
