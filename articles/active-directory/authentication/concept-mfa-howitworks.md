---
title: Azure Multi-Factor Authentication – jak to funguje
description: Azure Multi-Factor Authentication pomáhá chránit přístup k datům a aplikacím a současně plní požadavky uživatelů na jednoduchý proces přihlašování.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: ad9d517be930f68dcddba87fc59eab8b830a2b1c
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159147"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Jak to funguje: ověřování Azure Multi-Factor Authentication

Zabezpečení dvoustupňové ověřování spočívá v jeho vrstveného přístupu. Tím bylo narušeno několika faktory ověřování představuje velkou výzvou pro útočníky. I v případě, že útočník dokázal další heslo uživatele, je zbytečné bez nutnosti vlastnictví dodatečnou metodu ověřování. Funguje tak, že vyžaduje dva nebo více z následujících metod ověřování:

* Něco víte (obvykle heslo)
* Něco, co máte (důvěryhodné zařízení, které není lehké duplikovat, jako je telefon)
* Něco, co že je (vaše biometrika)

<center>![Koncepční ověřovací metody image](./media/concept-mfa-howitworks/methods.png)</center>

Azure Multi-Factor Authentication (MFA) pomáhá chránit přístup k datům a aplikacím při zachování jednoduchosti pro uživatele. Poskytuje dodatečné zabezpečení vyžadováním druhou formu ověřování a poskytuje silné ověřování přes celou řadu snadno použitelné [metody ověřování](concept-authentication-methods.md).

## <a name="how-to-get-multi-factor-authentication"></a>Jak získat ověření službou Multi-Factor Authentication?

Ověřování službou Multi-Factor Authentication je součástí následujících nabídek:

* **Licence Azure Active Directory Premium** -plně vybavené použití služby Azure Multi-Factor Authentication (Cloud) nebo ověřování Azure Multi-Factor Authentication Server (místní).
   * **Služba Azure MFA (Cloud)** - **tato možnost je doporučený pro nová nasazení**. Azure MFA v cloudu vyžaduje žádnou místní infrastrukturu a je možné s uživateli federované nebo jenom pro cloud.
   * **Azure MFA Server** – Pokud vaše organizace potřebuje ke správě prvky přidružené infrastruktury a je nasazena služba AD FS ve vašem prostředí místní tímto způsobem může být možnost.
* **Multi-Factor Authentication pro Office 365** -podmnožinu funkcí Azure Multi-Factor Authentication jsou k dispozici jako součást vašeho předplatného. Další informace o vícefaktorové ověřování pro Office 365, najdete v článku [plánování ověřování službou Multi-Factor Authentication pro Office 365 nasazení](https://support.office.com/article/plan-for-multi-factor-authentication-for-office-365-deployments-043807b2-21db-4d5c-b430-c8a6dee0e6ba).
* **Azure Active Directory globální správci** -podmnožinu funkcí Azure Multi-Factor Authentication jsou k dispozici jako prostředek k ochraně účty globálních správců.

### <a name="auth-provider-or-mfa-license"></a>Zprostředkovatel vícefaktorového ověřování nebo vícefaktorové ověřování licencí

Pokud už máte Azure AD Premium nebo sadu licenci, která obsahuje Azure AD Premium, už máte Azure MFA. Vaše organizace není nutné žádné další rozšířit funkci dvoustupňové ověření pro všechny uživatele. Potřebujete pouze přiřadit licenci uživateli, a pak můžete zapnout vícefaktorové ověřování.

Pokud nemáte licence, které patří Azure MFA, nebo nemáte dostatek licencí pro pokrytí všech uživatelů, můžete vytvořit [zprostředkovatel vícefaktorového ověřování MFA](concept-mfa-authprovider.md) rozšířit všechny možnosti služby MFA pro uživatele, kteří je potřebují. 

> [!IMPORTANT]
> Pokud nemáte k dispozici dostatek licencí pro vaše uživatele, můžete vytvořit poskytovatele Multi-Factor Auth jednotlivých uživatelů k pokrytí zbytku organizace. Nevytvářejte poskytovatele služby Multi-Factor Auth na ověřování. Pokud tak učiníte, můžou být nakonec platíte za ověření požadavků od uživatelů, které už máte licence.

## <a name="supportability"></a>Možnosti podpory

Protože většina uživatelů jsou zvyklí používat jenom hesla pro ověření, je důležité, že vaše organizace komunikuje se všichni uživatelé týkající se tohoto procesu. Sledování může snížit pravděpodobnost, že uživatelé volat vaše oddělení technické podpory pro menší problémy související s MFA. Existují však některé scénáře, kde dočasným zákazem MFA je nezbytné. Chcete-li pochopit, jak zvládnout tyto scénáře pomocí následujících pokynů:

* Trénování pracovníci podpory pro zpracování scénářů, kde uživatel nemůže přihlásit, protože nemají přístup ke své metody ověřování, nebo nejsou správně funguje.
   * Pomocí zásad podmíněného přístupu pro službu Azure MFA, pracovníci podpory můžete přidat uživatele do skupiny, která je vyloučena ze zásad, které vyžadují vícefaktorové ověřování.
   * Pracovníci podpory zákazníků můžete povolit dočasné jednorázové přihlášení pro uživatele Azure MFA Server chcete, aby uživatel možné ověřit bez dvoustupňové ověřování. Jednorázové přihlášení je dočasné a vyprší po zadaném počtu sekund.
   * Pomocí zásad podmíněného přístupu pro službu Azure MFA pracovníkům podpory můžete přidat uživatele do skupiny, která je vyloučena ze zásad, které vyžadují vícefaktorové ověřování.
* Zvažte použití důvěryhodné IP adresy nebo pojmenovaná umístění jako způsob, jak minimalizovat dvoustupňové ověřování výzvy. Pomocí této funkce můžou správci tenanta spravované nebo federované obejít dvoustupňové ověřování pro uživatele, kteří se přihlašují ze důvěryhodné síťové umístění, například intranetu organizace.
* Nasazení [Azure AD Identity Protection](../active-directory-identityprotection.md) a aktivovat dvoustupňové ověřování založené na rizikové události.

## <a name="next-steps"></a>Další postup

- Najít podrobnosti o [Licencování uživatelů](concept-mfa-licensing.md)

- Přečtěte si podrobnosti o [která verze se má nasadit](concept-mfa-whichversion.md)

- Najděte odpovědi na [– nejčastější dotazy](multi-factor-authentication-faq.md)