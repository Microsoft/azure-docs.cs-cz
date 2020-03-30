---
title: Přesunutí aplikací služby AD FS do služby Azure Active Directory pomocí sestavy aktivit | Dokumenty společnosti Microsoft
description: Sestava aktivit y aplikace služby AD FS (AD FS) služby AD FS umožňuje rychle migrovat aplikace ze služby AD FS do služby Azure Active Directory (Azure AD). Tento nástroj pro migraci pro službu AD FS identifikuje kompatibilitu s Azure AD a poskytuje pokyny pro migraci.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/14/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 333e440fdd5f5062dda45fb12a83543c63e66c04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978034"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>Použití sestavy aktivity aplikace služby AD FS (preview) k migraci aplikací do služby Azure AD

Mnoho organizací používá službu AD Federation Services (AD FS) k poskytování jednotného přihlašování ke cloudovým aplikacím. Přesun aplikací služby AD FS do Azure AD pro ověřování přináší významné výhody, zejména pokud jde o řízení nákladů, řízení rizik, produktivitu, dodržování předpisů a zásadsprávné řízení. Ale pochopení, které aplikace jsou kompatibilní s Azure AD a určení konkrétní kroky migrace může být časově náročné.

Sestava aktivity aplikace služby AD FS (preview) na webu Azure Portal umožňuje rychle určit, které z vašich aplikací se lze migrovat do Služby Azure AD. Vyhodnotí všechny aplikace služby AD FS pro kompatibilitu s Azure AD, zkontroluje všechny problémy a poskytuje pokyny pro přípravu jednotlivých aplikací pro migraci. Pomocí sestavy aktivity aplikace služby AD FS můžete:

* **Seznamte se s aplikacemi ad fs a rozsah migrace.** Sestava aktivit aplikace služby AD FS uvádí seznam všech aplikací služby AD FS ve vaší organizaci a označuje jejich připravenost k migraci do služby Azure AD.
* **Upřednostněte aplikace pro migraci.** Získejte počet jedinečných uživatelů, kteří se přihlásili k aplikaci v posledních 1, 7 nebo 30 dnech, abyste mohli určit kritičnost nebo riziko migrace aplikace.
* **Spusťte testy migrace a opravte problémy.** Služba zasílání zpráv automaticky spustí testy k určení, zda je aplikace připravena k migraci. Výsledky jsou zobrazeny v sestavě aktivity aplikace služby AD FS jako stav migrace. Pokud jsou zjištěny potenciální problémy s migrací, získáte konkrétní pokyny, jak tyto problémy řešit.

Data aktivity aplikace služby AD FS jsou k dispozici uživatelům, kterým je přiřazena některá z těchto rolí správce: globální správce, čtečka sestav, čtečka zabezpečení, správce aplikace nebo správce cloudových aplikací.

## <a name="prerequisites"></a>Požadavky

* Vaše organizace musí aktuálně používat službu AD FS pro přístup k aplikacím.
* Azure AD Connect Health musí být povolené ve vašem tenantovi Azure AD.
   * [Další informace o azure ad připojení stavu](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)
   * [Začínáme s nastavením azure ad connect health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Objevte aplikace ad FS, které lze migrovat 

Sestava aktivit y aplikace Služby AD FS je dostupná na webu Azure Portal v části Azure AD **Usage & přehledy.** Sestava aktivity aplikace služby AD FS analyzuje každou aplikaci služby AD FS a zjišťuje, zda ji lze migrovat tak, jak je, nebo zda je nutná další kontrola. 

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí role správce, která má přístup k datům aktivit aplikací služby AD FS (globální správce, čtečka sestav, čtečka zabezpečení, správce aplikací nebo správce cloudových aplikací).

2. Vyberte **Službu Azure Active Directory**a pak vyberte **podnikové aplikace**.

3. V části **Aktivita**vyberte **Přehledy využití & (Náhled)** a pak vyberte **aktivitu aplikace služby AD FS,** chcete-li otevřít seznam všech aplikací služby AD FS ve vaší organizaci.

   ![Aktivita aplikace služby AD FS](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Pro každou aplikaci v seznamu aktivit aplikace služby AD FS zobrazte **stav Migrace**:

   * **Připraveno k migraci** znamená, že konfigurace aplikace služby AD FS je plně podporovaná ve službě Azure AD a dá se migrovat tak, jak je.

   * **Potřebuje kontrolu** znamená, že některá nastavení aplikace lze migrovat do Azure AD, ale budete muset zkontrolovat nastavení, která nelze migrovat tak, jak je.

   * **Další požadované kroky** znamená, že Azure AD nepodporuje některá nastavení aplikace, takže aplikaci nelze migrovat v aktuálním stavu.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>Vyhodnocení připravenosti aplikace pro migraci 

1. V seznamu aktivit aplikace služby AD FS otevřete podrobnosti o migraci kliknutím na stav ve **sloupci Stav migrace.** Zobrazí se souhrn konfiguračních testů, které prošly, spolu s případnými problémy s migrací.

   ![Informace o migraci](media/migrate-adfs-application-activity/migration-details.png)

2. Kliknutím na zprávu otevřete další podrobnosti pravidla migrace. Úplný seznam testovaných vlastností naleznete v tabulce [konfiguračních testů aplikací služby AD FS](#ad-fs-application-configuration-tests) níže.

   ![Podrobnosti pravidla migrace](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>Konfigurační testy aplikací služby AD FS

V následující tabulce jsou uvedeny všechny konfigurační testy, které jsou prováděny v aplikacích služby AD FS.

|Výsledek  |Předat/Varování/Selhání  |Popis  |
|---------|---------|---------|
|Test-ADFSRPAdditionalAuthenticationRules <br> Pro další ověřování bylo zjištěno alespoň jedno nemizírovatelné pravidlo.       | Průchod/Upozornění          | Předávající strana má pravidla pro výzvu k vícefaktorovéověřování (MFA). Chcete-li přejít na Azure AD, přeložte tato pravidla do zásad podmíněného přístupu. Pokud používáte místní vícefaktorové zabezpečení, doporučujeme přejít na Azure MFA. [Další informace o podmíněném přístupu](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).        |
|Test-ADFSRPAdditionalWSFedEndpoint <br> Předávající strana má AdditionalWSFedEndpoint nastavena na true.       | Úspěch/neúspěch          | Předávající strana ve sf fs umožňuje více WS-Fed kontrolní koncové body.V současné době Azure AD podporuje jenom jeden.Pokud máte scénář, kde tento výsledek blokuje migraci, [dejte nám vědět](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints).     |
|Test-ADFSRPAllowedAuthenticationClassReferences <br> Předávající strana nastavila odkazy AllowedAuthenticationClassReference.       | Úspěch/neúspěch          | Toto nastavení ve službě AD FS umožňuje určit, zda je aplikace nakonfigurována tak, aby umožňovala pouze určité typy ověřování. K dosažení této funkce doporučujeme použít podmíněný přístup. Pokud máte scénář, kde tento výsledek blokuje migraci, [dejte nám vědět](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication).  [Další informace o podmíněném přístupu](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).          |
|Test-ADFSRPAlwaysrequireAuthentication Test-ADFSRPAlwaysRequireAuthentication Test-ADFSRPAlwaysRequireAuthentication Test- <br> VždyrequireAuthenticationCheckResult      | Úspěch/neúspěch          | Toto nastavení ve službě AD FS umožňuje určit, zda je aplikace nakonfigurována tak, aby ignorovala soubory cookie služby SSO, a **vždy zobrazit výzvu k ověření**. Ve službě Azure AD můžete spravovat relaci ověřování pomocí zásad podmíněného přístupu k dosažení podobného chování. [Další informace o konfiguraci správy ověřovacích relací pomocí podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).          |
|Test-ADFSRPAutoUpdatepovolena <br> Předávající strana má možnost automatické aktualizace nastavena na hodnotu true.       | Průchod/Upozornění          | Toto nastavení ve službě AD FS umožňuje určit, zda je služba AD FS nakonfigurována tak, aby automaticky aktualizovala aplikaci na základě změn v metadatech federace. Azure AD nepodporuje to dnes, ale neměl by blokovat migraci aplikace do Azure AD.           |
|Test-ADFSRPClaimsProviderName <br> Předávající strana má povoleno více zprostředkovatelů deklarací identity.       | Úspěch/neúspěch          | Toto nastavení ve službě AD FS volá zprostředkovatele identity, ze kterých předávající strana přijímá deklarace identity. Ve službě Azure AD můžete povolit externí spolupráci pomocí Azure AD B2B. [Další informace o Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b).          |
|Test-ADFSRPdelegationAuthorizationRules      | Úspěch/neúspěch          | Aplikace má definována vlastní pravidla autorizace delegování. Toto je koncept WS-Trust, který podporuje Azure AD pomocí moderních ověřovacích protokolů, jako je OpenID Connect a OAuth 2.0. [Další informace o platformě Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc).          |
|Test-ADFSRPImpersonationAuthorizationRules       | Průchod/Upozornění          | Aplikace má vlastní pravidla autorizace zosobnění definována.Toto je koncept WS-Trust, který podporuje Azure AD pomocí moderních ověřovacích protokolů, jako je OpenID Connect a OAuth 2.0. [Další informace o platformě Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc).          |
|Test-ADFSRPIssuanceAuthorizationRules <br> Pro autorizaci vydání bylo zjištěno alespoň jedno nemizírovatelné pravidlo.       | Průchod/Upozornění          | Aplikace má vlastní pravidla autorizace vystavování definovaná ve službě AD FS.Azure AD podporuje tuto funkci s podmíněným přístupem Azure AD. [Další informace o podmíněném přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/overview). <br> Můžete také omezit přístup k aplikaci podle uživatele nebo skupin přiřazených k aplikaci. [Další informace o přiřazování uživatelů a skupin k přístupu k aplikacím](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups).            |
|Test-ADFSRPVydáníTransformRules <br> Pro Nástroj Ímtransformace bylo zjištěno alespoň jedno nemizírovatelné pravidlo.       | Průchod/Upozornění          | Aplikace má vlastní pravidla transformace vystavování definovaná ve službě AD FS. Azure AD podporuje přizpůsobení deklarací vydaných v tokenu. Další informace naleznete [v tématu Přizpůsobení deklarací vydaných v tokenu SAML pro podnikové aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).           |
|Test-ADFSRPMonitoringEnabled <br> Předávající strana má MonitorEnabled nastavena na hodnotu true.       | Průchod/Upozornění          | Toto nastavení ve službě AD FS umožňuje určit, zda je služba AD FS nakonfigurována tak, aby automaticky aktualizovala aplikaci na základě změn v metadatech federace. Azure AD nepodporuje to dnes, ale neměl by blokovat migraci aplikace do Azure AD.           |
|Test-ADFSRPNotbeforeSkew <br> NotbeforeSkewCheckResult      | Průchod/Upozornění          | AD FS umožňuje časové zkosení na základě NotBefore a NotOnOrAfter časy v tokenu SAML. Azure AD automaticky zpracovává ve výchozím nastavení.          |
|Test-ADFSRPRequestMFAFromFromClaimsProviders <br> Předávající strana má RequestMFAFromClaimsProviders nastavena na hodnotu true.       | Průchod/Upozornění          | Toto nastavení ve službě AD FS určuje chování pro vícefaktorové ověřování, když uživatel pochází od jiného poskytovatele deklarací identity. Ve službě Azure AD můžete povolit externí spolupráci pomocí Azure AD B2B. Potom můžete použít zásady podmíněného přístupu k ochraně přístupu hosta. Další informace o [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) a [podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).          |
|Test-ADFSRPSignedSamlRequestsRequired <br> Předávající strana má signedSamlRequestsRequired nastaveno na hodnotu true.       | Úspěch/neúspěch          | Aplikace je konfigurována ve službě AD FS k ověření podpisu v požadavku SAML. Azure AD přijímá podepsanou žádost SAML; však nebude ověřovat podpis. Azure AD má různé metody ochrany proti škodlivým voláním. Azure AD například používá adresy URL odpovědí nakonfigurované v aplikaci k ověření požadavku SAML. Azure AD odešle token pouze pro odpověď adresy URL nakonfigurované pro aplikaci. Pokud máte scénář, kde tento výsledek blokuje migraci, [dejte nám vědět](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature).          |
|Životnost tokenu Test-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | Průchod/Upozornění         | Aplikace je nakonfigurována pro vlastní životnost tokenu. Výchozí hodnota ad FS je jedna hodina.Azure AD podporuje tuto funkci pomocí podmíněného přístupu. Další informace najdete [v tématu Konfigurace správy relací ověřování pomocí podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).          |
|Předávající strana je nastavena na šifrování deklarací identity. To je podporováno službou Azure AD.       | Předat          | Pomocí Azure AD můžete zašifrovat token odeslaný do aplikace. Další informace najdete [v tématu Konfigurace šifrování tokenů Azure AD SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).          |
|EncryptedNameIdRequiredCheckResult      | Úspěch/neúspěch          | Aplikace je nakonfigurována pro šifrování deklarace názvu nameID v tokenu SAML.Pomocí Azure AD můžete zašifrovat celý token odeslaný do aplikace.Šifrování konkrétních deklarací identity ještě není podporováno. Další informace najdete [v tématu Konfigurace šifrování tokenů Azure AD SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).         |

## <a name="check-the-results-of-claim-rule-tests"></a>Kontrola výsledků testů pravidel deklarací

Pokud jste nakonfigurovali pravidlo deklarace pro aplikaci ve službě AD FS, prostředí poskytne podrobnou analýzu pro všechna pravidla deklarace. Uvidíte, která pravidla deklarací se dá přesunout do Azure AD a která z nich potřebují další kontrolu.

1. V seznamu aktivit aplikace služby AD FS otevřete podrobnosti o migraci kliknutím na stav ve **sloupci Stav migrace.** Zobrazí se souhrn konfiguračních testů, které prošly, spolu s případnými problémy s migrací.

2. Na stránce **podrobnosti pravidla migrace** rozbalte výsledky, abyste zobrazili podrobnosti o možných problémech s migrací a získali další pokyny. Podrobný seznam všech testovaných pravidel deklarací reklam y naleznete v tabulce [Kontrola výsledků testů pravidel deklarací pohledávky](#check-the-results-of-claim-rule-tests) níže.

   Následující příklad ukazuje podrobnosti pravidla migrace pro pravidlo IssuanceTransform. Uvádí konkrétní části deklarace, které je třeba zkontrolovat a řešit před migrací aplikace do Služby Azure AD.

   ![Podrobnosti pravidla migrace jsou podrobně](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Testy pravidel deklarací

V následující tabulce jsou uvedeny všechny testy pravidel deklarací, které jsou prováděny v aplikacích ad fs.

|Vlastnost  |Popis  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | Příkaz condition používá regulární výrazy k vyhodnocení, zda deklarace odpovídá určitému vzoru.Chcete-li dosáhnout podobné funkce ve službě Azure AD, můžete použít předdefinované transformace, jako je Například IfEmpty(), StartWith(), Contains(), mimo jiné. Další informace naleznete [v tématu Customize claims issued in the SAML token for enterprise applications](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_CONDITION_CLASS      | Příkaz podmínek má několik podmínek, které je třeba vyhodnotit před spuštěním příkazu vystavování.Azure AD může podporovat tuto funkci s funkcemi transformace deklarace identity, kde můžete vyhodnotit více hodnot deklarací identity.Další informace naleznete [v tématu Customize claims issued in the SAML token for enterprise applications](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_RULE_TYPE      | Pravidlo reklamace nebylo možné rozpoznat. Další informace o konfiguraci deklarací identity ve službě Azure AD naleznete v [tématu Customize claims issued in the SAML token for enterprise applications](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | Příkaz podmínka používá vystavitna, který není podporován ve službě Azure AD.V současné době Azure AD není zdroj deklarací z obchodů odlišných, které služby Active Directory nebo Azure AD. Pokud vám to blokuje migraci aplikací do Azure AD, [dejte nám vědět](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).         |
|UNSUPPORTED_CONDITION_FUNCTION      | Příkaz condition používá agregační funkci k vydání nebo přidání jedné deklarace bez ohledu na počet shod.Ve službě Azure AD můžete vyhodnotit atribut uživatele a rozhodnout, jakou hodnotu použít pro deklaraci pomocí funkcí jako IfEmpty(), StartWith(), Contains(), mimo jiné.Další informace naleznete [v tématu Customize claims issued in the SAML token for enterprise applications](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|RESTRICTED_CLAIM_ISSUED      | Příkaz podmínka používá deklaraci, která je omezena ve službě Azure AD. Je možné vydat omezenou deklaraci, ale nemůžete upravit její zdroj ani použít žádnou transformaci. Další informace najdete [v tématu Přizpůsobení deklarací vysílaných v tokenech pro konkrétní aplikaci ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).          |
|EXTERNAL_ATTRIBUTE_STORE      | Příkaz vystavování používá uložení atributů jiné než služba Active Directory. V současné době Azure AD není zdroj deklarací z obchodů odlišných, které služby Active Directory nebo Azure AD. Pokud tento výsledek blokuje migraci aplikací do Azure AD, [dejte nám vědět](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).          |
|UNSUPPORTED_ISSUANCE_CLASS      | Příkaz vystavování používá ADD k přidání deklarací do příchozí sady deklarací. Ve službě Azure AD to může být nakonfigurované jako více transformací deklarací.Další informace naleznete [v tématu Customize claims issued in the SAML token for enterprise applications](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | Příkaz vystavování používá regulární výrazy k transformaci hodnoty deklarace, která má být emitována.Chcete-li dosáhnout podobné funkce ve službě Azure AD, můžete použít předdefinované transformace, jako je Extract(), Trim(), ToLower, mimo jiné. Další informace naleznete [v tématu Customize claims issued in the SAML token for enterprise applications](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |


## <a name="next-steps"></a>Další kroky

- [Video: Jak použít sestavu aktivit služby AD FS k migraci aplikace](https://www.youtube.com/watch?v=OThlTA239lU)
- [Správa aplikací pomocí Azure Active Directory](what-is-application-management.md)
- [Správa přístupu k aplikacím](what-is-access-management.md)
- [Azure AD Connect federace](../hybrid/how-to-connect-fed-whatis.md)
