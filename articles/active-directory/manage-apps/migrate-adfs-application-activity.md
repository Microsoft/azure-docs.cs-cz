---
title: Pomocí sestavy aktivita můžete přesunout AD FS aplikace do Azure Active Directory | Microsoft Docs
description: Sestava aktivity aplikace Active Directory Federation Services (AD FS) (AD FS) umožňuje rychle migrovat aplikace z AD FS na Azure Active Directory (Azure AD). Tento nástroj pro migraci pro AD FS identifikuje kompatibilitu s Azure AD a poskytuje pokyny k migraci.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/14/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88a4d0f108d4e3c27ce17aaa83aafca38063c9ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589459"
---
# <a name="use-the-ad-fs-application-activity-report-to-migrate-applications-to-azure-ad"></a>Použití sestavy aktivity aplikace AD FS k migraci aplikací do Azure AD

Mnoho organizací používá Active Directory Federation Services (AD FS) (AD FS) k zajištění jednotného přihlašování ke cloudovým aplikacím. Při přesunu AD FSch aplikací do Azure AD se k ověřování připravují významné výhody, zejména v souvislosti se správou nákladů, řízením rizik, produktivitou, dodržováním předpisů a zásadami správného řízení. Ale porozumění aplikacím, které jsou kompatibilní s Azure AD, a určením konkrétních kroků migrace může být časově náročné.

Sestava aktivita aplikace AD FS v Azure Portal vám umožní rychle určit, které z vašich aplikací se můžou migrovat do služby Azure AD. Posuzuje všechny AD FS aplikace kvůli kompatibilitě s Azure AD, kontroluje případné problémy a poskytuje pokyny k přípravě jednotlivých aplikací pro migraci. Pomocí sestavy aktivity aplikace AD FS můžete:

* **Zjištění AD FS aplikací a určení rozsahu migrace** Sestava aktivita aplikace AD FS obsahuje seznam všech aplikací AD FS ve vaší organizaci, u kterých se během posledních 30 dnů přihlásilo aktivní uživatelské jméno. Tato sestava označuje připravenost aplikací pro migraci do služby Azure AD. Sestava nezobrazuje v AD FS, jako je například Office 365, žádné související předávající strany společnosti Microsoft. Například předávající strany s názvem "urn: Federation: MicrosoftOnline".

* **Určete prioritu aplikací pro migraci.** Získejte počet jedinečných uživatelů, kteří se přihlásili k aplikaci za posledních 1, 7 nebo 30 dní, abyste mohli určit závažnost nebo riziko migrace aplikace.
* **Spusťte testy migrace a opravte problémy.** Služba generování sestav automaticky spouští testy, aby zjistila, zda je aplikace připravena k migraci. Výsledky se zobrazí v sestavě aktivita aplikace AD FS jako stav migrace. Pokud konfigurace AD FS není kompatibilní s konfigurací služby Azure AD, získáte konkrétní pokyny pro řešení konfigurace ve službě Azure AD.

Data aktivity aplikace AD FS jsou k dispozici uživatelům, kteří mají přiřazenou některou z těchto rolí správce: globální správce, čtenář sestav, čtenář zabezpečení, správce aplikace nebo správce cloudové aplikace.

## <a name="prerequisites"></a>Požadavky

* Vaše organizace musí aktuálně používat AD FS k přístupu k aplikacím.
* Azure AD Connect Health musí být ve vašem tenantovi Azure AD povolené.
* Je nutné nainstalovat Azure AD Connect Health pro agenta AD FS.
   * [Další informace o Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md)
   * [Začněte s nastavením Azure AD Connect Health a nainstalujte agenta AD FS](../hybrid/how-to-connect-health-agent-install.md)

>[!IMPORTANT] 
>Existuje několik důvodů, proč po instalaci Azure AD Connect Health neuvidíte všechny aplikace, které očekáváte. Sestava aktivita aplikací AD FS aplikace zobrazuje v posledních 30 dnech pouze AD FS předávajících stran s přihlašovacími údaji uživatelů. Sestava také nezobrazuje Microsoft související předávající strany, jako je například Office 365.

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Zjištění AD FSch aplikací, které se dají migrovat 

Sestava aktivity aplikace AD FS je k dispozici v Azure Portal v části Azure AD **Usage & Insights** Reporting. Sestava aktivity aplikace AD FS aplikace analyzuje každou aplikaci AD FS a určí, jestli se dá migrovat tak, jak je, nebo pokud je potřeba další recenze. 

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí role správce, která má přístup k datům aktivity aplikace AD FS (globální správce, čtenář sestav, čtecí modul zabezpečení, Správce aplikací nebo správce cloudové aplikace).

2. Vyberte **Azure Active Directory** a pak vyberte **podnikové aplikace**.

3. V části **aktivita** vyberte **využití & přehledy** a pak vyberte **AD FS aktivita aplikace** a otevřete tak seznam všech AD FS aplikací ve vaší organizaci.

   ![Aktivita aplikace AD FS](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Pro každou aplikaci v seznamu AD FS aktivity aplikace zobrazte **stav migrace**:

   * **Připraveno k migraci** znamená, že konfigurace aplikace AD FS je plně podporovaná ve službě Azure AD a dá se migrovat tak, jak je.

   * **Je potřeba zkontrolovat** , že některá z nastavení aplikace se dají migrovat do Azure AD, ale budete muset zkontrolovat nastavení, která se nedají migrovat tak, jak je.

   * **Vyžadování dalších kroků** znamená, že Azure AD nepodporuje některá nastavení aplikace, takže aplikaci nelze migrovat v jejím aktuálním stavu.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>Vyhodnocení připravenosti aplikace na migraci 

1. V seznamu AD FS aktivity aplikace kliknutím na stav ve sloupci **stav migrace** otevřete podrobnosti migrace. Zobrazí se souhrn konfiguračních testů, které byly úspěšné, a případné případné problémy s migrací.

   ![Informace o migraci](media/migrate-adfs-application-activity/migration-details.png)

2. Kliknutím na zprávu otevřete další podrobnosti pravidla migrace. Úplný seznam testovaných vlastností naleznete níže v tabulce [AD FS testy konfigurace aplikací](#ad-fs-application-configuration-tests) níže.

   ![Podrobnosti pravidla migrace](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>AD FS testy konfigurace aplikace

V následující tabulce jsou uvedeny všechny testy konfigurace, které se provádí v AD FSch aplikacích.

|Výsledek  |Úspěch/upozornění/selhání  |Description  |
|---------|---------|---------|
|Test-ADFSRPAdditionalAuthenticationRules <br> Pro AdditionalAuthentication se zjistilo aspoň jedno pravidlo, které není migrovat.       | Úspěch/upozornění          | Předávající strana obsahuje pravidla pro dotazování služby Multi-Factor Authentication (MFA). Pokud se chcete přesunout do služby Azure AD, přeložte tato pravidla na zásady podmíněného přístupu. Pokud používáte místní MFA, doporučujeme přejít na Azure AD MFA. [Přečtěte si další informace o podmíněném přístupu](../authentication/concept-mfa-howitworks.md).        |
|Test-ADFSRPAdditionalWSFedEndpoint <br> Předávající strana má AdditionalWSFedEndpoint nastavenou na hodnotu true.       | Úspěch/neúspěch          | Předávající strana v AD FS umožňuje více koncových bodů kontrolního výrazu pro WS-Fed.V současné době Azure AD podporuje jenom jeden.Pokud máte scénář, kde tento výsledek blokuje migraci, [dejte nám prosím jistotu](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints).     |
|Test-ADFSRPAllowedAuthenticationClassReferences <br> Předávající strana má nastavenou AllowedAuthenticationClassReferences.       | Úspěch/neúspěch          | Toto nastavení v AD FS umožňuje určit, zda je aplikace nakonfigurována tak, aby povolovala pouze určité typy ověřování. K dosažení této možnosti doporučujeme použít podmíněný přístup. Pokud máte scénář, kde tento výsledek blokuje migraci, [dejte nám prosím jistotu](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication).  [Přečtěte si další informace o podmíněném přístupu](../authentication/concept-mfa-howitworks.md).          |
|Test-ADFSRPAlwaysRequireAuthentication <br> AlwaysRequireAuthenticationCheckResult      | Úspěch/neúspěch          | Toto nastavení v AD FS umožňuje určit, jestli je aplikace nakonfigurovaná tak, aby ignorovala soubory cookie jednotného přihlašování, a **vždycky se dotázat na ověřování**. V Azure AD můžete spravovat relaci ověřování pomocí zásad podmíněného přístupu, abyste dosáhli podobného chování. [Přečtěte si další informace o konfiguraci správy relací ověřování pomocí podmíněného přístupu](../conditional-access/howto-conditional-access-session-lifetime.md).          |
|Test-ADFSRPAutoUpdateEnabled <br> Předávající strana má AutoUpdateEnabled nastavenou na hodnotu true.       | Úspěch/upozornění          | Toto nastavení v AD FS umožňuje určit, jestli je AD FS nakonfigurovaná tak, aby automaticky aktualizovala aplikaci na základě změn v metadatech federace. Azure AD tuto službu ještě dnes nepodporuje, ale neměla by blokovat migraci aplikace do Azure AD.           |
|Test-ADFSRPClaimsProviderName <br> Předávající strana má povolených víc ClaimsProviders.       | Úspěch/neúspěch          | Toto nastavení v AD FS volá poskytovatele identity, ze kterých předávající strana přijímá deklarace identity. V Azure AD můžete povolit externí spolupráci pomocí Azure AD B2B. [Přečtěte si další informace o Azure AD B2B](../external-identities/what-is-b2b.md).          |
|Test-ADFSRPDelegationAuthorizationRules      | Úspěch/neúspěch          | Aplikace má definované vlastní autorizační pravidla delegování. Toto je WS-Trust koncept, který Azure AD podporuje pomocí moderních ověřovacích protokolů, jako je například OpenID Connect a OAuth 2,0. [Přečtěte si další informace o platformě Microsoft Identity](../develop/v2-protocols-oidc.md).          |
|Test-ADFSRPImpersonationAuthorizationRules       | Úspěch/upozornění          | Aplikace má definovaná vlastní autorizační pravidla zosobnění.Toto je WS-Trust koncept, který Azure AD podporuje pomocí moderních ověřovacích protokolů, jako je například OpenID Connect a OAuth 2,0. [Přečtěte si další informace o platformě Microsoft Identity](../develop/v2-protocols-oidc.md).          |
|Test-ADFSRPIssuanceAuthorizationRules <br> Pro IssuanceAuthorization se zjistilo aspoň jedno pravidlo, které není migrovat.       | Úspěch/upozornění          | Aplikace má vlastní autorizační pravidla vystavování, která jsou definována v AD FS.Azure AD podporuje tuto funkci pomocí podmíněného přístupu Azure AD. [Přečtěte si další informace o podmíněném přístupu](../conditional-access/overview.md). <br> Můžete taky omezit přístup k aplikaci podle uživatelů nebo skupin přiřazených k aplikaci. [Přečtěte si další informace o přiřazování uživatelů a skupin pro přístup k aplikacím](./assign-user-or-group-access-portal.md).            |
|Test-ADFSRPIssuanceTransformRules <br> Pro IssuanceTransform se zjistilo aspoň jedno pravidlo, které není migrovat.       | Úspěch/upozornění          | Aplikace má vlastní pravidla transformace vystavování, která jsou definována v AD FS. Azure AD podporuje přizpůsobení deklarací identity vystavených v tokenu. Další informace najdete v tématu [přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace](../develop/active-directory-saml-claims-customization.md).           |
|Test-ADFSRPMonitoringEnabled <br> Předávající strana má parametry monitoringenabled nastavenou na hodnotu true.       | Úspěch/upozornění          | Toto nastavení v AD FS umožňuje určit, jestli je AD FS nakonfigurovaná tak, aby automaticky aktualizovala aplikaci na základě změn v metadatech federace. Azure AD tuto službu ještě dnes nepodporuje, ale neměla by blokovat migraci aplikace do Azure AD.           |
|Test-ADFSRPNotBeforeSkew <br> NotBeforeSkewCheckResult      | Úspěch/upozornění          | AD FS umožňuje časové zkosení na základě časů NotBefore a NotOnOrAfter v tokenu SAML. Služba Azure AD je ve výchozím nastavení automaticky zpracovává.          |
|Test-ADFSRPRequestMFAFromClaimsProviders <br> Předávající strana má RequestMFAFromClaimsProviders nastavenou na hodnotu true.       | Úspěch/upozornění          | Toto nastavení v AD FS určuje chování vícefaktorového ověřování, pokud uživatel pochází z jiného zprostředkovatele deklarací identity. V Azure AD můžete povolit externí spolupráci pomocí Azure AD B2B. Pak můžete použít zásady podmíněného přístupu k ochraně přístupu hostů. Přečtěte si další informace o [Azure AD B2B](../external-identities/what-is-b2b.md) a [podmíněném přístupu](../conditional-access/overview.md).          |
|Test-ADFSRPSignedSamlRequestsRequired <br> Předávající strana má SignedSamlRequestsRequired nastavenou na hodnotu true.       | Úspěch/neúspěch          | Aplikace je nakonfigurovaná v AD FS k ověření podpisu v žádosti SAML. Azure AD přijímá podepsaný požadavek SAML; neověřuje ale signaturu. Azure AD má různé metody ochrany před škodlivými voláními. Azure AD například používá adresy URL odpovědí nakonfigurovaných v aplikaci k ověření požadavku SAML. Azure AD pošle token pro odpovědi na adresy URL nakonfigurované pro aplikaci. Pokud máte scénář, kde tento výsledek blokuje migraci, [dejte nám prosím jistotu](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature).          |
|Test-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | Úspěch/upozornění         | Aplikace je nakonfigurována pro vlastní životnost tokenu. Výchozí AD FS je jedna hodina.Azure AD tuto funkci podporuje pomocí podmíněného přístupu. Další informace najdete v tématu [Konfigurace správy relace ověřování pomocí podmíněného přístupu](../conditional-access/howto-conditional-access-session-lifetime.md).          |
|Předávající strana je nastavená na šifrování deklarací identity. Tato služba je podporovaná službou Azure AD.       | Úspěšné absolvování          | Pomocí Azure AD můžete šifrovat token odeslaný do aplikace. Další informace najdete v tématu [Konfigurace šifrování tokenů SAML v Azure AD](./howto-saml-token-encryption.md).          |
|EncryptedNameIdRequiredCheckResult      | Úspěch/neúspěch          | Aplikace je nakonfigurovaná k šifrování deklarace identity nameID v tokenu SAML.Pomocí Azure AD můžete zašifrovat celý token odeslaný do aplikace.Šifrování konkrétních deklarací se ještě nepodporuje. Další informace najdete v tématu [Konfigurace šifrování tokenů SAML v Azure AD](./howto-saml-token-encryption.md).         |

## <a name="check-the-results-of-claim-rule-tests"></a>Zkontroluje výsledky testů pravidla deklarace identity.

Pokud jste nakonfigurovali pravidlo deklarace identity pro aplikaci v AD FS, bude prostředí poskytovat podrobnou analýzu pro všechna pravidla deklarace identity. Uvidíte, která pravidla deklarace identity se dají přesunout do Azure AD a které vyžadují další kontrolu.

1. V seznamu AD FS aktivity aplikace kliknutím na stav ve sloupci **stav migrace** otevřete podrobnosti migrace. Zobrazí se souhrn konfiguračních testů, které byly úspěšné, a případné případné problémy s migrací.

2. Na stránce **Podrobnosti pravidla migrace** rozbalte výsledky pro zobrazení podrobností o potenciálních problémech s migrací a získejte další doprovodné materiály. Podrobný seznam všech testovaných pravidel deklarací identity najdete níže v tabulce [kontroly výsledků testů pro pravidla deklarací identity](#check-the-results-of-claim-rule-tests) .

   Následující příklad ukazuje Podrobnosti pravidla migrace pro pravidlo IssuanceTransform. Obsahuje seznam specifických částí deklarace identity, které je třeba před migrací do služby Azure AD zkontrolovat a vyřešit.

   ![Podrobnosti pravidla migrace – další doprovodné materiály](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Testy pravidla deklarace identity

V následující tabulce jsou uvedeny všechny testy pravidel deklarací identity, které se provádí v AD FSch aplikacích.

|Vlastnost  |Popis  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | Příkaz podmínky používá regulární výrazy k vyhodnocení, zda deklarace identity odpovídá určitému vzoru.Pokud chcete dosáhnout podobných funkcí v Azure AD, můžete použít předdefinovanou transformaci, jako je IfEmpty (), StartWith (), Contains (), a to mimo jiné. Další informace najdete v tématu [přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace](../develop/active-directory-saml-claims-customization.md).          |
|UNSUPPORTED_CONDITION_CLASS      | Příkaz Condition obsahuje více podmínek, které je třeba vyhodnotit před spuštěním příkazu k vystavení.Služba Azure AD může tuto funkci podporovat s transformačními funkcemi deklarace identity, kde můžete vyhodnotit více hodnot deklarace identity.Další informace najdete v tématu [přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace](../develop/active-directory-saml-claims-customization.md).          |
|UNSUPPORTED_RULE_TYPE      | Nebylo možné rozpoznat pravidlo deklarace identity. Další informace o tom, jak nakonfigurovat deklarace identity ve službě Azure AD, najdete v tématu [přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace](../develop/active-directory-saml-claims-customization.md).          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | Příkaz podmínky používá vystavitele, který není ve službě Azure AD podporován.V současné době Azure AD nepoužívá zdrojové deklarace identity z obchodů, které mají jinou službu Active Directory nebo Azure AD. Pokud vás zablokuje migrace aplikací do služby Azure AD, [dejte nám prosím jistotu](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).         |
|UNSUPPORTED_CONDITION_FUNCTION      | Příkaz podmínky používá agregační funkci k vystavení nebo přidání jediné deklarace identity bez ohledu na počet shod.Ve službě Azure AD můžete vyhodnotit atribut uživatele a rozhodnout se, jakou hodnotu použít pro deklaraci identity s funkcemi jako IfEmpty (), StartWith (), obsahuje (), a to mimo jiné.Další informace najdete v tématu [přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace](../develop/active-directory-saml-claims-customization.md).          |
|RESTRICTED_CLAIM_ISSUED      | Příkaz podmínky používá deklaraci identity, která je omezená v Azure AD. Může být možné vystavit omezenou deklaraci identity, ale nemůžete změnit její zdroj ani použít žádnou transformaci. Další informace najdete v tématu [přizpůsobení deklarací identity emitovaných v tokenech pro konkrétní aplikaci v Azure AD](../develop/active-directory-claims-mapping.md).          |
|EXTERNAL_ATTRIBUTE_STORE      | Příkaz vystavení používá úložiště atributů, které se liší v rámci služby Active Directory. V současné době Azure AD nepoužívá zdrojové deklarace identity z obchodů, které mají jinou službu Active Directory nebo Azure AD. Pokud se vám z tohoto důvodu blokuje migrace aplikací do služby Azure AD, [dejte nám prosím jistotu](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).          |
|UNSUPPORTED_ISSUANCE_CLASS      | Příkaz vystavení pomocí příkazu přidat přidá deklarace do příchozí sady deklarací. V Azure AD to může být nakonfigurované jako vícenásobné transformace deklarací identity.Další informace najdete v tématu [přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace](../develop/active-directory-claims-mapping.md).         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | Příkaz vystavení používá regulární výrazy k transformaci hodnoty deklarace, která se má vygenerovat.Chcete-li dosáhnout podobných funkcí v Azure AD, můžete použít předdefinovanou transformaci, jako je například Extract (), trim (), ToLower, mimo jiné. Další informace najdete v tématu [přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace](../develop/active-directory-saml-claims-customization.md).          |

## <a name="troubleshooting"></a>Řešení potíží

### <a name="cant-see-all-my-ad-fs-applications-in-the-report"></a>V sestavě se nezobrazuje všechny aplikace AD FS.

 Pokud jste nainstalovali Azure AD Connect stav, ale stále se zobrazuje výzva k jejímu instalaci, nebo Pokud nevidíte všechny AD FS aplikace v sestavě, může to být tím, že nemáte aktivní AD FS aplikace ani vaše AD FS aplikace od Microsoftu.
 
 Sestava aktivita aplikace AD FS obsahuje seznam všech aplikací AD FS ve vaší organizaci s aktivními uživateli přihlašování za posledních 30 dní. Sestava také nezobrazuje související předávající strany Microsoftu v AD FS, jako je například Office 365. Například předávající strany s názvem "urn: Federation: MicrosoftOnline", "microsoftonline", "Microsoft: winhello: CERT: prov: Server" se v seznamu nezobrazí.





## <a name="next-steps"></a>Další kroky

- [Video: jak používat sestavu aktivity AD FS k migraci aplikace](https://www.youtube.com/watch?v=OThlTA239lU)
- [Správa aplikací pomocí Azure Active Directory](what-is-application-management.md)
- [Správa přístupu k aplikacím](what-is-access-management.md)
- [Azure AD Connect federace](../hybrid/how-to-connect-fed-whatis.md)
