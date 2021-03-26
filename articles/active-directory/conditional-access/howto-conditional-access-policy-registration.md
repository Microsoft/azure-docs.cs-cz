---
title: Podmíněný přístup – kombinované informace o zabezpečení – Azure Active Directory
description: Vytvoření vlastní zásady podmíněného přístupu pro registraci bezpečnostních údajů
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/24/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 711d4bdf2be2ad3158c12e4690a70fb83fe7a846
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559498"
---
# <a name="conditional-access-securing-security-info-registration"></a>Podmíněný přístup: Zabezpečení registrace bezpečnostních údajů

Zabezpečení, kdy a jak se uživatelé registrují pro Multi-Factor Authentication služby Azure AD a Samoobslužné resetování hesla je možné provést s akcemi uživatele v zásadách podmíněného přístupu. Tato funkce je k dispozici organizacím, které povolily [kombinovanou registraci](../authentication/concept-registration-mfa-sspr-combined.md). Tato funkce umožňuje organizacím zacházet v procesu registrace, jako je libovolná aplikace v zásadě podmíněného přístupu, a používat k zabezpečení tohoto prostředí plný výkon podmíněného přístupu. 

Některé organizace v minulosti mohly použít důvěryhodné síťové umístění nebo dodržování předpisů zařízením jako způsob zabezpečení registračního prostředí. Díky přidání [dočasného přístupového průchodu](../authentication/howto-authentication-temporary-access-pass.md) ve službě Azure AD můžou správci zřídit přihlašovací údaje pro uživatele, které jim umožní registraci z libovolného zařízení nebo místa. Pověření pro dočasný přístup k předávání splňuje požadavky na podmíněný přístup pro službu Multi-Factor Authentication.

## <a name="create-a-policy-to-secure-registration"></a>Vytvoření zásady pro zabezpečení registrace

Následující zásady platí pro vybrané uživatele, kteří se pokoušejí zaregistrovat pomocí kombinovaného prostředí pro registraci. Tato zásada vyžaduje, aby uživatelé prováděli službu Multi-Factor Authentication nebo používali přihlašovací údaje pro dočasné přístupový průchod.

1. V **Azure Portal** přejděte na **Azure Active Directory**  >  **zabezpečení**  >  **podmíněný přístup**.
1. Vyberte **nové zásady**.
1. Do název zadejte název pro tuto zásadu. Například **Kombinovaná registrace informací o zabezpečení pomocí klepnutí**.
1. V části **přiřazení** vyberte **Uživatelé a skupiny** a vyberte uživatele a skupiny, pro které chcete tuto zásadu použít.
   1. V části **Zahrnout** vyberte **Všichni uživatelé**.

      > [!WARNING]
      > Pro [kombinovanou registraci](../authentication/howto-registration-mfa-sspr-combined.md)je nutné povolit uživatele.

   1. V části **vyloučit**.
      1. Vyberte **Všichni uživatelé typu Host a externí uživatelé**.
      
         > [!NOTE]
         > Dočasný přístupový průchod nefunguje pro uživatele typu Host.

      1. Vyberte **Uživatelé a skupiny** a vyberte účty pro nouzový přístup nebo rozklad vaší organizace. 
1. V části **cloudové aplikace nebo akce** vyberte **akce uživatele** a zaškrtněte políčko **zaregistrovat informace o zabezpečení**.
1. V části **řízení přístupu**  >  **udělení oprávnění**.
   1. Vyberte **Udělit přístup**.
   1. Vyberte **vyžadovat službu Multi-Factor Authentication**.
   1. Klikněte na **Vybrat**.
1. Nastavte **Povolit zásadu** na **Zapnuté**.
1. Potom vyberte **Vytvořit**.

Správci teď budou muset vystavit přihlašovací údaje pro dočasný přístup pro nové uživatele, aby mohli splnit požadavky na službu Multi-Factor Authentication k registraci. Postup, jak tento úkol provést, najdete v části [vytvoření dočasného přístupového průchodu na portálu Azure AD](../authentication/howto-authentication-temporary-access-pass.md#create-a-temporary-access-pass-in-the-azure-ad-portal).

Organizace se mohou rozhodnout, že kromě toho vyžadují službu **Multi-Factor Authentication** v kroku 6B nebo místo toho vyžadovat jiné grantové ovládací prvky. Když vyberete více ovládacích prvků, je nutné vybrat přepínač odpovídající přepínači pro vyžadování **všech** nebo **jednoho** z vybraných ovládacích prvků, když tuto změnu provedete.

### <a name="guest-user-registration"></a>Registrace uživatele typu Host

Pro [uživatele typu Host](../external-identities/what-is-b2b.md) , kteří se potřebují zaregistrovat pro službu Multi-Factor Authentication ve vašem adresáři, se můžete rozhodnout blokovat registraci mimo [důvěryhodná síťová umístění](concept-conditional-access-conditions.md#locations) pomocí následující příručky.

1. V **Azure Portal** přejděte na **Azure Active Directory**  >  **zabezpečení**  >  **podmíněný přístup**.
1. Vyberte **nové zásady**.
1. Do název zadejte název pro tuto zásadu. Například **Kombinovaná registrace informací o zabezpečení v důvěryhodných sítích**.
1. V části **přiřazení** vyberte **Uživatelé a skupiny** a vyberte uživatele a skupiny, pro které chcete tuto zásadu použít.
   1. V části **Zahrnout** vyberte **Všichni host a externí uživatelé**.
1. V části **cloudové aplikace nebo akce** vyberte **akce uživatele** a zaškrtněte políčko **zaregistrovat informace o zabezpečení**.
1. V části  >  **umístění** podmínek.
   1. Nakonfigurujte **Ano**.
   1. Uveďte **libovolné umístění**.
   1. Vylučte **všechna důvěryhodná umístění**.
   1. V okně umístění vyberte **Hotovo** .
   1. V okně podmínky vyberte **Hotovo** .
1. V části **řízení přístupu**  >  **udělení oprávnění**.
   1. Vyberte **blokovat přístup**.
   1. Pak klikněte na **Vybrat**.
1. Nastavte **Povolit zásadu** na **Zapnuté**.
1. Pak vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

[Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

[Určení dopadu pomocí režimu pouze sestavy podmíněného přístupu](howto-conditional-access-insights-reporting.md)

[Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
