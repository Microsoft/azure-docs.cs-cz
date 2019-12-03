---
title: Co je nového? Poznámky k verzi – Azure Active Directory | Microsoft Docs
description: Přečtěte si, co je nového v Azure Active Directory, jako jsou například nejnovější poznámky k verzi, známé problémy, opravy chyb, zastaralé funkce a nadcházející změny.
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 793b9d1b33c244354841402babbd9177ce7ed19b
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687719"
---
# <a name="whats-new-in-azure-active-directory"></a>Co je nového v Azure Active Directory?

>Přečtěte si informace o tom, kdy se tato stránka na aktualizace znovu navštíví zkopírováním a vložením této adresy URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` do ![ikona čtečky informačního kanálu RSS](./media/whats-new/feed-icon-16x16.png) čtečku informačního kanálu.

Služba Azure AD průběžně přijímá vylepšení. V tomto článku najdete informace o tom, jak se chcete zabývat aktuálním vývojem.

- Nejnovější verze
- Známé problémy
- Opravy chyb
- Zastaralé funkce
- Plánuje změny

Tato stránka se aktualizuje měsíčně, takže ji můžete pravidelně znovu navštěvovat. Pokud hledáte položky, které jsou starší než šest měsíců, najdete je v archivu, abyste mohli [novinky v Azure Active Directory](whats-new-archive.md).

---

## <a name="october-2019"></a>Říjen 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Vyřazení rozhraní API identityRiskEvent pro detekci rizik Azure AD Identity Protection  

**Zadejte:** Plánování změn  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana

V reakci na zpětnou vazbu od vývojářů teď můžou předplatitelé Azure AD Premium P2 provádět komplexní dotazy na data o detekci rizika Azure AD Identity Protection pomocí nového rozhraní API riskDetection pro Microsoft Graph. Stávající beta verze rozhraní [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API přestane vracet data z **10. ledna 2020**. Pokud vaše organizace používá rozhraní identityRiskEvent API, měli byste přejít na nové rozhraní riskDetection API.

Další informace o novém rozhraní riskDetection API najdete v [referenční dokumentaci k rozhraní API pro detekci rizik](https://aka.ms/RiskDetectionsAPI).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Podpora proxy aplikací pro atribut SameSite a Chrome 80

**Zadejte:** Plánování změn  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control

Několik týdnů před vydáním prohlížeče Chrome 80, plánujeme aktualizovat způsob, jakým soubory cookie proxy aplikace zacházejí s atributem **SameSite** . S vydáním Chrome 80 se všechny soubory cookie, které nespecifikují atribut **SameSite** , považují za, i když byly nastavené na `SameSite=Lax`.

Abychom se vyhnuli potenciálně negativním dopadům z důvodu této změny, aktualizujeme přístup k proxy aplikací a soubory cookie relace pomocí:

- Nastavení výchozí hodnoty pro nastavení **použít zabezpečený soubor cookie** na **Ano**.

- Nastavení výchozí hodnoty pro atribut **SameSite** na **hodnotu None**.

    >[!NOTE]
    > Soubory cookie přístupu k proxy aplikacím se vždycky přenesly výhradně přes zabezpečené kanály. Tyto změny se vztahují pouze na soubory cookie relace.

Další informace o nastavení souborů cookie proxy aplikace najdete v tématu [nastavení souborů cookie pro přístup k místním aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>Registrace aplikací (starší verze) a sblížená Správa aplikací z portálu pro registraci aplikací (apps.dev.microsoft.com) již nebudou k dispozici.

**Zadejte:** Plánování změn  
**Kategorie služby:** NENÍ K DISPOZICI  
**Schopnost produktu:** Vývojářské prostředí

V blízké budoucnosti již uživatelé s účty Azure AD nebudou moci registrovat a spravovat sblížené aplikace pomocí portálu pro registraci aplikací (apps.dev.microsoft.com) nebo registrovat a spravovat aplikace v Registrace aplikací (starší verze). prostředí Azure Portal.

Další informace o novém prostředí Registrace aplikací najdete v [Registrace aplikací v průvodci školením Azure Portal](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Pro uživatele, kteří se při migraci z MFA na podmíněný přístup využívají, už se nevyžadují, aby se zaregistrovali znovu.

**Zadejte:** Určí  
**Kategorie služby:** VÍCEFAKTOROVÉHO  
**Schopnost produktu:** Zabezpečení identity & ochrana

Opravili jsme známý problém, kdy se uživatelé museli znovu zaregistrovat, pokud byli zakázáni pro jednotlivé uživatele Multi-Factor Authentication (MFA) a pak povolili MFA prostřednictvím zásad podmíněného přístupu.

Pokud chcete, aby se uživatelé znovu zaregistrovali, můžete z metod ověřování uživatele na portálu Azure AD vybrat **požadovanou možnost znovu zaregistrovat možnost MFA** . Další informace o migraci uživatelů z MFA na ověřování MFA na základě podmíněného přístupu najdete v tématu [převod uživatelů z MFA na ověřování MFA na základě vícefaktorového přístupu](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nové funkce pro transformaci a odeslání deklarací identity ve vašem tokenu SAML

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ

Přidali jsme další možnosti, které vám pomůžou přizpůsobit a poslat deklarace identity ve vašem tokenu SAML. Mezi tyto nové funkce patří:

- Další funkce pro transformaci deklarací identity, které vám pomůžou upravit hodnotu, kterou odešlete v deklaraci identity.

- Možnost použít více transformací na jednu deklaraci identity.

- Možnost zadat zdroj deklarací identity na základě typu uživatele a skupiny, do které uživatel patří

Podrobné informace o těchto nových funkcích, včetně způsobu jejich použití, najdete v tématu [přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Nová stránka moje přihlášení pro koncové uživatele v Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Monitorování & vytváření sestav

Přidali jsme novou stránku **Moje přihlášení** (https://mysignins.microsoft.com), aby si uživatelé vaší organizace mohli zobrazit poslední historii přihlášení, aby zkontrolovali, že neobvyklá aktivita. Tato nová stránka umožňuje uživatelům zobrazit:

- Pokud se někdo pokusí uhodnout svoje heslo.

- Pokud se útočník úspěšně přihlásil ke svému účtu a z kterého umístění.

- K jakým aplikacím se útočník pokusil získat přístup.

Další informace najdete v tématu [Uživatelé teď můžou zkontrolovat historii přihlášení pro neobvyklý blog aktivity](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) .

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migrace Azure AD Domain Services (Azure služba AD DS) z klasických do Azure Resource Manager virtuálních sítí

**Zadejte:** Nová funkce  
**Kategorie služby:** Azure AD Domain Services  
**Schopnost produktu:** Azure AD Domain Services

Našim zákazníkům, kteří se zablokovali na klasických virtuálních sítích – máme skvělé novinky. Nyní můžete provést jednorázovou migraci z klasické virtuální sítě do existující Správce prostředků virtuální sítě. Po přesunu do Správce prostředků virtuální sítě budete moci využít výhod dalších a upgradovaných funkcí, jako jsou podrobné zásady pro hesla, e-mailová oznámení a protokoly auditu.

Další informace najdete v tématu [verze Preview – migrace Azure AD Domain Services z modelu klasických virtuálních sítí do Správce prostředků](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Aktualizace rozložení kontraktu stránky Azure AD B2C

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C

Zavedli jsme některé nové změny verze 1.2.0 kontraktu stránky pro Azure AD B2C. V této aktualizované verzi teď můžete řídit pořadí načítání pro vaše prvky, což může také zabránit blikání, ke kterému dojde, když je načtena šablona stylů (CSS).

Úplný seznam změn provedených u kontraktu stránky najdete v [protokolu změn verze](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Aktualizace stránky Moje aplikace spolu s novými pracovními prostory (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Moje aplikace  
**Schopnost produktu:** Access Control

Nyní můžete přizpůsobit způsob, jakým uživatelé vaší organizace zobrazují a přistupují ke značce – nové prostředí moje aplikace, včetně použití funkce nové pracovní prostory, aby je bylo snazší najít. Funkce New Workspaces funguje jako filtr pro aplikace, ke kterým mají uživatelé vaší organizace přístup.

Další informace o tom, jak vytvářet nové prostředí moje aplikace a vytvářet pracovní prostory, najdete v tématu [vytvoření pracovních prostorů na portálu moje aplikace (Preview)](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Podpora měsíčního modelu fakturace založeného na aktivních uživatelích (Obecná dostupnost)

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C

Azure AD B2C teď podporuje fakturaci měsíčně aktivních uživatelů (MAU). Faktura MAU vychází z počtu jedinečných uživatelů s aktivitou ověřování v průběhu kalendářního měsíce. Stávající zákazníci můžou kdykoli přejít na tuto novou metodu fakturace.

Od 1. listopadu 2019 se budou všichni noví zákazníci automaticky účtovat pomocí této metody. Tato metoda účtování těží zákazníky díky cenovým výhodám a možnosti plánování předem.

Další informace najdete v tématu [upgrade modelu fakturace na měsíční aktivní uživatele](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Konsolidovaná položka nabídky zabezpečení na portálu Azure AD

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana

Teď máte přístup ke všem dostupným funkcím zabezpečení Azure AD z nové položky nabídky **zabezpečení** a z panelu **hledání** v Azure Portal. Kromě toho nová úvodní stránka **zabezpečení** , která se nazývá **zabezpečení – Začínáme**, bude obsahovat odkazy na naši veřejnou dokumentaci, pokyny pro zabezpečení a příručky pro nasazení.

Nová nabídka **zabezpečení** zahrnuje:

- Podmíněný přístup
- Ochrany identit
- Security Center
- Skóre Secure identity
- Metody ověření
- Vícefaktorové ověřování
- Sestavy rizik – rizikové uživatele, rizikové přihlašovacíky, detekce rizik
- A další...

Další informace najdete v tématu [zabezpečení – Začínáme](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Zásady vypršení platnosti skupin Office 365 se vylepšily pomocí automatické obnovy.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Správa životního cyklu identit

Zásady vypršení platnosti skupin Office 365 se rozšířily tak, aby automaticky obnovily skupiny, které aktivně používají její členové. Skupiny se v závislosti na aktivitě uživatelů na všech aplikacích Office 365, včetně Outlooku, SharePointu a týmů, obnoví.

Toto vylepšení pomáhá snižovat vaše oznámení o vypršení platnosti skupiny a pomáhá zajistit, aby byly aktivní skupiny dál dostupné. Pokud pro skupiny Office 365 už máte aktivní zásady vypršení platnosti, nemusíte nic dělat, abyste tuto novou funkci zapnuli.

Další informace najdete v tématu [Konfigurace zásad vypršení platnosti pro skupiny Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Prostředí pro vytváření aktualizací Azure AD Domain Services (Azure služba AD DS)

**Zadejte:** Změněná funkce  
**Kategorie služby:** Azure AD Domain Services  
**Schopnost produktu:** Azure AD Domain Services

Aktualizovali jsme Azure AD Domain Services (Azure služba AD DS), aby zahrnovala nové a vylepšené prostředí pro vytváření, což vám pomůže vytvořit spravovanou doménu pouhým třemi kliknutími! Kromě toho teď můžete nahrát a nasadit Azure služba AD DS ze šablony.

Další informace najdete v tématu [kurz: vytvoření a konfigurace instance Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance).

---

## <a name="september-2019"></a>Září 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Plán změny: vyřazení Power BI balíčků obsahu

**Zadejte:** Plánování změn  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

Od 1. října 2019 začne Power BI začínat všechny balíčky obsahu, včetně balíčku obsahu Power BI Azure AD. Jako alternativu k tomuto balíčku obsahu můžete použít sešity Azure AD a získat přehled o službách souvisejících s Azure AD. Další sešity se připravují, včetně sešitů o zásadách podmíněného přístupu v režimu pouze sestavy, přehledů na základě souhlasu aplikace a dalších.

Další informace o sešitech najdete v tématu [Jak používat Azure monitor sešity pro Azure Active Directory sestavy](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). Další informace o vyřazení balíčků obsahu najdete v blogovém příspěvku s [oznámením o obecných dostupnosti pro Power BI šablonových aplikací](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) .

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Můj profil se přejmenovává a integruje se stránkou systém Microsoft Office účtu.

**Zadejte:** Plánování změn  
**Kategorie služby:** Můj profil/účet  
**Schopnost produktu:** Prostřednictvím

Od října se moje prostředí mého profilu stane účtem. V rámci této změny všude, kde se aktuálně říká, se **můj profil** změní na **můj účet**. Na základě změny názvů a některých vylepšení návrhu nabízí aktualizovaný způsob další integraci se stránkou systém Microsoft Office účtu. Konkrétně budete mít přístup k instalacím a předplatným systému Office na stránce **Přehled účtu** , a to spolu s preferencemi kontaktů souvisejících s Office ze stránky **soukromí** .

Další informace o prostředí můj profil (Preview) najdete v tématu [Přehled portálu Profile (Preview)](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Hromadná Správa skupin a členů pomocí souborů CSV na portálu Azure AD (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

S potěšením oznamujeme, že v portálu Azure AD je dostupná veřejná verze Preview prostředí pro správu hromadných skupin. Teď můžete použít soubor CSV a portál Azure AD ke správě skupin a seznamů členů, včetně těchto:

- Přidávání nebo odebírání členů ze skupiny.

- Stahuje se seznam skupin z adresáře.

- Stahuje se seznam členů skupiny pro konkrétní skupinu.

Další informace najdete v tématu [hromadné přidání členů](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), [hromadné odebrání členů](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members), [seznam členů hromadného stahování](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)a [seznam hromadných stažení skupin](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Dynamický souhlas se teď podporuje prostřednictvím nového koncového bodu souhlasu správce.

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

Vytvořili jsme nového koncového bodu souhlasu správce, který podporuje dynamické vyjádření souhlasu, což je užitečné pro aplikace, které chtějí používat model dynamického souhlasu na platformě Microsoft identity.

Další informace o použití tohoto nového koncového bodu najdete v tématu [použití koncového bodu pro vyjádření souhlasu správce](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent).

---

### <a name="new-azure-ad-global-reader-role"></a>Nová role globálního čtecího modulu Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** RBAC  
**Schopnost produktu:** Access Control

Od 24. září 2019 začneme zavádět novou roli Azure Active Directory (AD) s názvem globální čtenář. Tato implementace začíná v rámci produkčního a globálního cloudového zákazníka (RSZ), dokončuje se v říjnu po celém světě.

Role globálního čtecího modulu je protějškem jen pro čtení pro globálního správce. Uživatelé v této roli můžou číst nastavení a informace pro správu napříč Microsoft 365 službami, ale nemůžou provádět akce správy. Vytvořili jsme roli globálního čtenáře, která vám pomůže snížit počet globálních správců ve vaší organizaci. Vzhledem k tomu, že účty globálních správců jsou výkonné a zranitelné vůči útokům, doporučujeme, abyste měli méně než pět globálních správců. Pro plánování, audity a vyšetřování doporučujeme použít roli globálního čtenáře. Doporučujeme také používat roli globálního čtecího modulu v kombinaci s jinými rolemi s omezeným oprávněním správce, jako je třeba správce Exchange, a získat tak práci, aniž byste museli provádět globální role správce.

Role globálního čtecího modulu funguje s novým centrem pro správu Microsoft 365, centrem pro správu Exchange, centrem pro správu týmů, Security Center, centrem dodržování předpisů, centrem pro správu Azure AD a centrem pro správu správy zařízení.

>[!NOTE]
> Na začátku veřejné verze Preview nebude role globálního čtecího zařízení fungovat s: SharePoint, Privileged Access Management, Customer Lockbox, popisky s citlivostí, životní cyklus týmů, vytváření sestav & Analytics, týmy pro správu telefonních zařízení a týmy. Zařazení. Všechny tyto služby mají za cíl pracovat s rolí v budoucnu.

Další informace najdete v tématu [oprávnění role správce v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Přístup k místnímu serveru sestav z aplikace Power BI Mobile pomocí Proxy aplikací služby Azure Active Directory

**Zadejte:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control

Nová integrace mezi Power BI mobilní aplikací a Azure Proxy aplikací služby AD umožňuje bezpečné přihlášení k Power BI mobilní aplikaci a zobrazení všech sestav vaší organizace hostovaných na místních Server sestav Power BI.

Informace o aplikaci Power BI Mobile, včetně místa, odkud si aplikaci stáhnout, najdete na [webu Power BI](https://powerbi.microsoft.com/mobile/). Další informace o tom, jak nastavit mobilní aplikaci Power BI pomocí Azure Proxy aplikací služby AD, najdete v tématu [Povolení vzdáleného přístupu k Power BI Mobile s využitím azure proxy aplikací služby AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Je dostupná nová verze modulu AzureADPreview PowerShellu.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Službě

Do modulu AzureADPreview byly přidány nové rutiny, které vám pomůžou definovat a přiřazovat vlastní role v Azure AD, včetně těchto:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nová verze Azure AD Connect

**Zadejte:** Změněná funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Službě

Vydali jsme aktualizovanou verzi Azure AD Connect pro zákazníky s automatickým upgradem. Tato nová verze zahrnuje několik nových funkcí, vylepšení a opravy chyb. Další informace o této nové verzi najdete v tématu [Azure AD Connect: Historie vydání verze](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Server Azure Multi-Factor Authentication (MFA), verze 8.0.2 je teď k dispozici.

**Zadejte:** Určí  
**Kategorie služby:** VÍCEFAKTOROVÉHO  
**Schopnost produktu:** Zabezpečení identity & ochrana

Pokud jste stávající zákazník, který vyaktivoval MFA Server před 1. červenci 2019, můžete si teď stáhnout nejnovější verzi MFA serveru (verze 8.0.2). V této nové verzi jsme:

- Opravili jsme problém, takže když Azure AD Sync změní uživatele na povoleno, pošle se uživateli e-mail.

- Opravili jsme problém, aby zákazníci mohli úspěšně upgradovat a přitom dál používat funkce značek.

- Přidání kódu země Kosova (+ 383).

- Do protokolu MultiFactorAuthSvc. log bylo přidáno protokolování auditu jednorázového přihlášení.

- Vylepšený výkon pro sadu SDK webové služby.

- Opravené další drobné chyby.

Od 1. července 2019 se Microsoftu zastavilo poskytování MFA serveru pro nová nasazení. Noví zákazníci, kteří vyžadují službu Multi-Factor Authentication, by měli používat cloudové Multi-Factor Authentication Azure. Další informace najdete v tématu [plánování cloudového nasazení Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="august-2019"></a>Srpen 2019

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Rozšířené hledání, filtrování a řazení pro skupiny je dostupné na portálu Azure AD (Public Preview).

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

S radostí oznamujeme dostupnost verze Public Preview pro rozšířené prostředí související se skupinami na portálu Azure AD. Tato vylepšení vám pomůžou lépe spravovat skupiny a seznamy členů tím, že poskytují:

- Rozšířené možnosti vyhledávání, jako je hledání v podřetězcůch v seznamech skupin.
- Rozšířené možnosti filtrování a řazení v seznamech členů a vlastníků.
- Nové možnosti vyhledávání pro seznamy členů a vlastníků.
- Přesnější počty skupin pro velké skupiny.

Další informace najdete v tématu [Správa skupin v Azure Portal](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Pro správu registrace aplikací jsou k dispozici nové vlastní role (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** RBAC  
**Schopnost produktu:** Access Control

Vlastní role (k dispozici v rámci předplatného Azure AD P1 nebo P2) vám teď můžou pomáhat s jemnou přístupem tím, že vám umožní vytvořit definice rolí s konkrétními oprávněními a pak tyto role přiřadit konkrétním prostředkům. V současné době vytvoříte vlastní role pomocí oprávnění pro správu registrací aplikací a potom přiřadíte roli ke konkrétní aplikaci. Další informace o vlastních rolích najdete v tématu [vlastní role správců v Azure Active Directory (Preview)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview).

Pokud potřebujete další oprávnění nebo prostředky, které se v tuto chvíli nezobrazuje, můžete poslat svůj názor na náš [web Azure Feedback](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) a my přidáme vaši žádost do naší mapy aktualizace.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Nové protokoly zřizování vám můžou pomoct monitorovat a řešit potíže s nasazením zřizování aplikací (Public Preview).

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit

K dispozici jsou nové protokoly zřizování, které vám pomůžou monitorovat a řešit potíže s nasazením zřizování uživatelů a skupin. Tyto nové soubory protokolu obsahují informace o:

- Které skupiny byly úspěšně vytvořeny v [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Jaké role byly naimportovány z [Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- Kteří zaměstnanci nebyli naimportovali z [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

Další informace najdete v tématu [zřizování sestav na portálu Azure Active Directory (Preview)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nové sestavy zabezpečení pro všechny správce Azure AD (všeobecně dostupné)

**Zadejte:** Nová funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana

Ve výchozím nastavení budou mít všichni správci služby Azure AD brzy přístup k moderním sestavám zabezpečení v rámci Azure AD. Až do konce září budete moct použít banner v horní části moderních sestav zabezpečení pro návrat k původním sestavám.

Moderní sestavy zabezpečení poskytují další možnosti ze starších verzí, včetně těchto:

- Rozšířené filtrování a řazení
- Hromadné akce, jako je například chybějící riziko uživatele
- Potvrzení napadených nebo bezpečných entit
- Stav rizika, který pokrývá: ohrožené, neúspěšné, opravené a potvrzené ohrožení
- Nové detekce související s riziky (k dispozici pro Azure AD Premium předplatitele)

Další informace najdete v tématech [rizikové uživatele](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users), [rizikové přihlašovací](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)údaje a [detekce rizik](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>Spravovaná identita přiřazená uživatelem je k dispozici pro Virtual Machines a Virtual Machine Scale Sets (Obecná dostupnost).

**Zadejte:** Nová funkce  
**Kategorie služby:** Spravované identity pro prostředky Azure  
**Schopnost produktu:** Vývojářské prostředí

Spravované identity přiřazené uživatelem jsou teď všeobecně dostupné pro Virtual Machines a Virtual Machine Scale Sets. V rámci této služby může Azure vytvořit identitu v tenantovi Azure AD, která je důvěryhodná pro používané předplatné, a dá se přiřadit k jedné nebo více instancím služby Azure. Další informace o spravovaných identitách přiřazených uživateli najdete v tématu [co jsou spravované identity pro prostředky Azure](https://aka.ms/azuremanagedidentity).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Uživatelé můžou resetovat svá hesla pomocí mobilní aplikace nebo hardwarového tokenu (všeobecně dostupná).

**Zadejte:** Změněná funkce  
**Kategorie služby:** Samoobslužné resetování hesla  
**Schopnost produktu:** Ověřování uživatelů

Uživatelé, kteří si zaregistrovali mobilní aplikaci ve vaší organizaci, teď můžou resetovat oznámení z aplikace Microsoft Authenticator nebo zadáním kódu z mobilní aplikace nebo hardwarového tokenu.

Další informace najdete v tématu [jak to funguje: Samoobslužné resetování hesla služby Azure AD](https://aka.ms/authappsspr). Další informace o uživatelském prostředí najdete v tématu [resetování vlastního pracovního nebo školního hesla](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignoruje sdílenou mezipaměť MSAL.NET pro scénáře v zastoupení.

**Zadejte:** Určí  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

Počínaje službou Azure AD Authentication Library (ADAL.NET) verze 5.0.0-Preview musí vývojáři aplikací [serializovat jednu mezipaměť na účet pro webové aplikace a webová rozhraní API](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). V opačném případě můžou některé scénáře, které používají [tok](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)za běhu, spolu s některými konkrétními případy použití `UserAssertion`, způsobit zvýšení oprávnění. Aby se zabránilo této chybě, ADAL.NET nyní ignoruje sdílenou mezipaměť Microsoft Authentication Library for dotnet (MSAL.NET) pro scénáře v zastoupení.

Další informace o tomto problému naleznete v tématu [Azure Active Directory zvýšení úrovně zabezpečení oprávnění v knihovně ověřování](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nové federované aplikace dostupné v Aplikace Azure AD galerii – srpen 2019

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V srpnu 2019 jsme do Galerie aplikací přidali tyto 26 nových aplikací s podporou federace:

[Civic Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [ProNovos](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial)Operations Manager, [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Viareport Inativ Portal (Evropa)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [Docházka pro Academy](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [prioritní matrice](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint Backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [CPQSync](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), Cincom, [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [doručovat. Media™ Portal](https://portal.deliver.media), [prvotní vzdělávání](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD připojení](https://www.stashcat.com), [blikání](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [ProNovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [sledování pomocí barev](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), [](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial) [přecházení a EAB navigace strategické péče](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>K dispozici jsou nové verze modulů PowerShellu pro AzureAD a prostředí AzureADPreview PowerShell.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Službě

K dispozici jsou nové aktualizace modulů PowerShellu pro AzureAD a AzureAD ve verzi Preview:

- Do parametru `Get-AzureADDirectoryRole` v modulu AzureAD byl přidán nový parametr `-Filter`. Tento parametr vám pomůže filtrovat role adresáře vrácené rutinou.
- Do modulu AzureADPreview byly přidány nové rutiny, které vám pomůžou definovat a přiřazovat vlastní role v Azure AD, včetně těchto:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Vylepšení uživatelského rozhraní tvůrce pravidla dynamické skupiny v Azure Portal

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

Provedli jsme některá vylepšení uživatelského rozhraní pro tvůrce pravidel dynamické skupiny, který je k dispozici v Azure Portal, abyste mohli snadněji nastavit nové pravidlo nebo změnit stávající pravidla. Toto vylepšení návrhu umožňuje vytvořit pravidla s až pěti výrazy, nikoli jenom s jedním. Aktualizovali jsme také seznam vlastností zařízení, aby se odebraly zastaralé vlastnosti zařízení.

Další informace najdete v tématu [Správa pravidel dynamického členství](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-update-rule).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Nové oprávnění aplikace Microsoft Graph k dispozici pro použití s recenzemi přístupu

**Zadejte:** Změněná funkce  
**Kategorie služby:** Kontroly přístupu  
**Schopnost produktu:** Zásady správného řízení identity

Zavedli jsme nové oprávnění aplikace Microsoft Graph `AccessReview.ReadWrite.Membership`, které umožňuje aplikacím automaticky vytvářet a načítat kontroly přístupu pro členství ve skupině a přiřazení aplikací. Toto oprávnění můžou použít naplánované úlohy nebo v rámci automatizace, aniž by bylo nutné přihlášený kontext uživatele.

Další informace najdete v [příkladu vytváření kontrol přístupu Azure AD pomocí Microsoft Graph oprávnění aplikace s blogem PowerShellu](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Protokoly aktivit Azure AD jsou teď dostupné pro státní cloudové instance v Azure Monitor

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

S radostí oznamujeme, že protokoly aktivit Azure AD jsou teď dostupné pro státní cloudové instance v Azure Monitor. Protokoly Azure AD teď můžete odesílat do svého účtu úložiště nebo do centra událostí, abyste je mohli integrovat s SIEM nástroji, jako je [SumoLogic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)a [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight). 

Další informace o nastavení Azure Monitor najdete [v tématu protokoly aktivit Azure AD v Azure monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Aktualizace uživatelů na nové a rozšířené prostředí informací o zabezpečení

**Zadejte:** Změněná funkce  
**Kategorie služby:**  Ověřování (přihlášení)   
**Schopnost produktu:** Ověřování uživatelů

25. září 2019 se vypíná staré, nerozšířené informace o zabezpečení pro registraci a správu bezpečnostních údajů uživatelů a zapínání nové [Rozšířené verze](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). To znamená, že uživatelé již nebudou moci používat staré prostředí.

Další informace o prostředí rozšířené informace o zabezpečení najdete v naší [dokumentaci pro správu](https://aka.ms/securityinfodocs) a v [dokumentaci k uživateli](https://aka.ms/securityinfoguide).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Pokud chcete toto nové prostředí zapnout, musíte:

1. Přihlaste se k Azure Portal jako globální správce nebo Správce uživatelů.

2. Přejděte na **Azure Active Directory > nastavení uživatele > spravovat nastavení pro přístupové panely funkce verze Preview**.

3. V části **Uživatelé můžou používat funkce verze Preview pro registraci a správu informací o zabezpečení** , vyberte **vybrané**a potom buď zvolte skupinu uživatelů, nebo zvolte možnost **vše** , pokud chcete tuto funkci zapnout pro všechny uživatele v tenantovi.

4. V části * * Uživatelé můžou používat funkce verze Preview pro registraci a správu zabezpečení * * * * * *, vyberte **žádné**.

5. Uložte nastavení.

    Po uložení nastavení už nebudete mít přístup k původnímu prostředí bezpečnostní informace.

>[!Important]
>Pokud tento postup nedokončíte před 25. září 2019, bude se váš tenant Azure Active Directory automaticky povolit pro vylepšené prostředí. Pokud máte nějaké otázky, kontaktujte nás prosím na registrationpreview@microsoft.com.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Požadavky na ověřování pomocí přihlašovacích údajů po přihlášení budou striktně ověřeny.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Zvyšování

Od 2. září 2019 budou žádosti o ověření pomocí metody POST u standardů protokolu HTTP striktně ověřeny. Konkrétně mezery a dvojité uvozovky (") již nebudou odebrány z hodnot formuláře žádosti. Tyto změny se neočekávají pro přerušení stávajících klientů a pomůžou zajistit, aby se požadavky odeslané do služby Azure AD spolehlivě zpracovávají pokaždé.

Další informace najdete v tématu o [nejnovějších změnách Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Červenec 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Plánování změn: aktualizace služby proxy aplikací pro podporu pouze TLS 1,2

**Zadejte:** Plánování změn  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control

Abychom vám pomohli získat naše nejsilnější šifrování, zahajte omezení přístupu služby proxy aplikací jenom na protokoly TLS 1,2. Toto omezení se zpočátku zavede na zákazníky, kteří už používají protokoly TLS 1,2, takže se jeho dopad neprojeví. Dokončení vyřazení protokolů TLS 1,0 a TLS 1,1 bude dokončeno 31. srpna 2019. Zákazníci, kteří pořád používají TLS 1,0 a TLS 1,1, budou dostávat Pokročilá oznámení o přípravě na tuto změnu.

Chcete-li zachovat připojení ke službě proxy aplikací v rámci této změny, doporučujeme, abyste se ujistili, že jsou kombinace klientských serverů a prohlížeče a serveru aktualizovány tak, aby používaly protokol TLS 1,2. Doporučujeme také, abyste měli všechny systémy klientů používané vašimi zaměstnanci k přístupu k aplikacím, které jsou publikované prostřednictvím služby proxy aplikací.

Další informace najdete v tématu [Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Plán změny: aktualizace návrhu přicházejí do Galerie aplikací.

**Zadejte:** Plánování změn  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ

Nové změny uživatelského rozhraní přicházejí do návrhu **Přidat z oblasti Galerie** v okně **Přidat aplikaci** . Tyto změny vám pomůžou snadněji najít vaše aplikace, které podporují Automatické zřizování, OpenID Connect, Security Assertion Markup Language (SAML) a jednotné přihlašování (SSO) k heslu.

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Plánování změn: odebrání IP adresy serveru MFA z IP adresy Office 365

**Zadejte:** Plánování změn  
**Kategorie služby:** VÍCEFAKTOROVÉHO  
**Schopnost produktu:** Zabezpečení identity & ochrana

Odebíráme IP adresu MFA serveru z [webové služby IP adresa a adresa URL sady Office 365](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Pokud při aktualizaci nastavení brány firewall aktuálně spoléháte na tyto stránky, musíte se ujistit, že máte také seznam IP adres, které jsou popsané v části **požadavky na bránu firewall pro azure Multi-Factor Authentication Server** v článku [začínáme s Azure Multi-Factor Authentication Server](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) .

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Tokeny jenom pro aplikace teď vyžadují, aby klientská aplikace existovala v tenantovi prostředků.

**Zadejte:** Určí  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

26. července 2019 jsme změnili, jak poskytujeme tokeny jenom pro aplikace prostřednictvím [udělení přihlašovacích údajů klienta](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Dříve mohli aplikace získat tokeny pro volání jiných aplikací bez ohledu na to, jestli klientská aplikace byla v tenantovi. Toto chování jsme aktualizovali tak, aby se prostředky jednoho tenanta, někdy označované jako webové rozhraní API, mohly volat jenom pro klientské aplikace, které existují v tenantovi prostředků.

Pokud se vaše aplikace nenachází v tenantovi prostředků, zobrazí se chybová zpráva s oznámením, že `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` tento problém vyřešit, musíte vytvořit instanční objekt klientské aplikace v tenantovi pomocí [koncového bodu souhlasu správce](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) nebo [prostřednictvím PowerShellu](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), který zaručuje, že má tenant oprávnění aplikace pro provoz v rámci tenanta.

Další informace najdete v tématu [co je nového pro ověřování?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Stávající souhlas mezi klientem a rozhraním API se dál nepožaduje. Aplikace by měly stále provádět vlastní kontroly autorizace.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nové přihlášení ke službě Azure AD pomocí klíčů zabezpečení FIDO2 pro nové heslo

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

Zákazníci Azure AD teď můžou nastavit zásady pro správu FIDO2 bezpečnostních klíčů pro uživatele a skupiny organizace. Koncoví uživatelé také mohou zaregistrovat své bezpečnostní klíče sami, pomocí klíčů se přihlásíte ke svým účtům Microsoft na webech, zatímco na zařízeních s podporou FIDO, a také se přihlásíte ke svým zařízením s Windows 10 připojeným k Azure AD.

Další informace najdete v tématu [Povolení přihlášení bez hesla pro Azure AD (Preview)](/azure/active-directory/authentication/concept-authentication-passwordless) pro informace týkající se správců a [Nastavení bezpečnostních údajů pro použití bezpečnostního klíče (Preview)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) pro informace týkající se koncových uživatelů.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nové federované aplikace dostupné v Aplikace Azure AD galerii – červenec 2019

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V červenci 2019 jsme do Galerie aplikací přidali tyto 18 nových aplikací s podporou federace:

[Ungerboeck software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [jasný vzor Omnichannel kontakt Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [chytřejší Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [LOOOP](https://www.looop.co/schedule-a-demo/), [Productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [přístup k aplikaci MS Azure SSO pro Ethidex dodržování předpisů Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [Hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [stoupání](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), JFrog [Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizace zřizování uživatelských účtů pro tyto nově podporované aplikace SaaS

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** Monitorování & vytváření sestav

Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federovaný adresář](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Další informace o tom, jak lépe zabezpečit organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) .

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nová značka služby Azure AD Domain Services pro skupinu zabezpečení sítě

**Zadejte:** Nová funkce  
**Kategorie služby:** Azure AD Domain Services  
**Schopnost produktu:** Azure AD Domain Services

Pokud už vás unavuje správu dlouhých seznamů IP adres a rozsahů, můžete použít novou značku síťové služby **AzureActiveDirectoryDomainServices** ve skupině zabezpečení sítě Azure, která vám pomůžou zabezpečit příchozí provoz do podsítě vaší Azure AD Domain Services virtuální sítě.

Další informace o této nové značce služby najdete v tématu [skupiny zabezpečení sítě pro Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nové audity zabezpečení pro Azure AD Domain Services (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Azure AD Domain Services  
**Schopnost produktu:** Azure AD Domain Services

S radostí oznamujeme vydání auditu zabezpečení Azure AD Domain Service do veřejné verze Preview. Auditování zabezpečení pomáhá poskytnout důležitý pohled na vaše služby ověřování – streamování událostí auditu zabezpečení do cílových prostředků, včetně Azure Storage, pracovních prostorů Azure Log Analytics a centra událostí Azure s využitím služby Azure AD Domain Services. bran.

Další informace najdete v tématu [Povolení auditů zabezpečení pro Azure AD Domain Services (Preview)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Nové použití metod ověřování & Insights (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Samoobslužné resetování hesla  
**Schopnost produktu:** Monitorování & vytváření sestav

Nové metody ověřování & sestavy Insights vám pomůžou pochopit, jak se registrují a používají funkce jako Azure Multi-Factor Authentication a Samoobslužné resetování hesla ve vaší organizaci, včetně počtu registrovaných Uživatelé pro každou funkci, jak často se Samoobslužné resetování hesla používá k resetování hesel a podle toho, jakou metodu se resetuje.

Další informace najdete v tématu [použití metod ověřování & Insights (Preview)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Pro všechny správce Azure AD jsou k dispozici nové sestavy zabezpečení (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana

Všichni Správci služby Azure AD teď můžou v horní části existujících sestav zabezpečení vybrat hlavičku, jako je například zpráva **Uživatelé označení příznakem rizika** , abyste mohli začít používat nové prostředí zabezpečení, jak je znázorněno v sestavách **rizikové uživatele** a **rizikových přihlášení** . V průběhu času se všechny sestavy zabezpečení přesunou ze starších verzí do nových verzí a nové sestavy poskytují následující další možnosti:

- Rozšířené filtrování a řazení

- Hromadné akce, jako je například chybějící riziko uživatele

- Potvrzení napadených nebo bezpečných entit

- Stav rizika, který pokrývá: ohrožené, neúspěšné, opravené a potvrzené ohrožení

Další informace najdete v tématu Sestava [rizikových uživatelů](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) a [sestavy rizikových přihlášení](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nové audity zabezpečení pro Azure AD Domain Services (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Azure AD Domain Services  
**Schopnost produktu:** Azure AD Domain Services

S radostí oznamujeme vydání auditu zabezpečení Azure AD Domain Service do veřejné verze Preview. Auditování zabezpečení pomáhá poskytnout důležitý pohled na vaše služby ověřování – streamování událostí auditu zabezpečení do cílových prostředků, včetně Azure Storage, pracovních prostorů Azure Log Analytics a centra událostí Azure s využitím služby Azure AD Domain Services. bran.

Další informace najdete v tématu [Povolení auditů zabezpečení pro Azure AD Domain Services (Preview)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nová federace B2B Direct pomocí SAML/WS-dodávání (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C

Přímá federace pomáhá lépe pracovat s partnery, jejichž řešení identity spravované IT není Azure AD, a to díky práci se systémy identit, které podporují standardy SAML nebo WS-dodávání. Po nastavení přímého federačního vztahu s partnerem může každý nový uživatel typu Host, kterého budete pozvat z této domény, spolupracovat s vámi pomocí stávajícího účtu organizace, takže uživatelské prostředí pro hosty bude bezproblémové.

Další informace najdete v tématu [Přímá federace pomocí AD FS a poskytovatelů třetích stran pro uživatele typu Host (Preview)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizace zřizování uživatelských účtů pro tyto nově podporované aplikace SaaS

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** Monitorování & vytváření sestav

Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federovaný adresář](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nová kontrolu duplicitních názvů skupin na portálu Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

Když teď vytváříte nebo aktualizujete název skupiny z portálu Azure AD, provedeme kontrolu a zjistíme, jestli duplikujete existující název skupiny v prostředku. Pokud určíme, že se tento název už používá v jiné skupině, zobrazí se výzva, abyste změnili své jméno.

Další informace najdete v tématu [Správa skupin na portálu Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD teď podporuje statické parametry dotazů v identifikátorech URI odpovědi (přesměrování).

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

Aplikace Azure AD teď můžou registrovat a používat identifikátory URI odpovědí (přesměrování) s parametry statického dotazu (například `https://contoso.com/oauth2?idp=microsoft`) pro žádosti OAuth 2,0. Parametr statického dotazu podléhá shodě řetězců pro identifikátory URI odpovědi, stejně jako všechny ostatní části identifikátoru URI odpovědi. Pokud neexistuje žádný registrovaný řetězec, který by odpovídal identifikátoru URI přesměrování adresy URL s dekódováním, požadavek se odmítne. Pokud se najde identifikátor URI odpovědi, použije se celý řetězec pro přesměrování uživatele, včetně parametru statického dotazu.

Dynamické identifikátory URI odpovědi jsou stále zakázané, protože představují bezpečnostní riziko a nelze je použít k uchování informací o stavu v rámci žádosti o ověření. Pro účely tohoto účelu použijte parametr `state`.

V současné době se na obrazovkách registrace aplikace Azure Portal pořád zablokují parametry dotazu. Manifest aplikace ale můžete upravit ručně a přidat a otestovat parametry dotazu v aplikaci. Další informace najdete v tématu [co je nového pro ověřování?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Protokoly aktivit (rozhraní API MS Graph) pro službu Azure AD jsou teď dostupné prostřednictvím rutin PowerShellu.

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

S radostí oznamujeme, že protokoly aktivit Azure AD (sestavy auditu a přihlášení) jsou teď dostupné prostřednictvím modulu Azure AD PowerShellu. Dřív jste mohli vytvořit vlastní skripty pomocí koncových bodů MS Graph API a teď jsme tuto možnost rozšířili na rutiny PowerShellu.

Další informace o tom, jak tyto rutiny použít, najdete v tématu [rutiny Azure AD PowerShell pro vytváření sestav](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Aktualizované ovládací prvky filtru pro audit a protokoly přihlašování v Azure AD

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

Aktualizovali jsme sestavy protokolu auditu a přihlašování, takže teď můžete použít různé filtry, aniž byste je museli přidávat jako sloupce na obrazovkách sestavy. Navíc teď můžete rozhodnout, kolik filtrů chcete na obrazovce zobrazit. Tyto aktualizace společně usnadňují čtení a větší rozsah vašich potřeb vašich sestav.

Další informace o těchto aktualizacích najdete v tématech [filtrování protokolů auditu](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) a [filtrování aktivit přihlášení](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>Červen 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nové rozhraní riskDetections API pro Microsoft Graph (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana

S potěšením oznamujeme, že nové rozhraní riskDetections API pro Microsoft Graph je teď ve verzi Public Preview. Toto nové rozhraní API můžete použít k zobrazení seznamu uživatelů s ochranou identity v organizaci a zjišťování rizik přihlašování. Toto rozhraní API můžete použít také k efektivnějšímu dotazování detekce rizik, včetně podrobností o typu detekce, stavu, úrovni a dalších.

Další informace najdete v [referenční dokumentaci k rozhraní API pro detekci rizik](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – červen 2019

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

Do června 2019 jsme do Galerie aplikací přidali tyto 22 nových aplikací s podporou federace:

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [klient VPN pro Azure](https://portal.azure.com/), [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), pomocná [pomoc](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz](https://me.secure.mercedes-benz.com/), skore, [CYBERARK, scrible](https://app.justskore.it/) [cloudová infrastruktura](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [PandaDoc ověřování SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), Perceptyx [edu](https://www.scrible.com/sign-in/#/create-account), [Proptimise](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Vtiger](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [OS](https://proptimise.co.uk/software/), [CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager pro Oracle Maloobchodní prodej, Oracle Access Manager pro Oracle E-Business Suite, Oracle IDCS pro E-Business Suite, Oracle IDCS pro PeopleSoft spouštěných místně, Oracle IDCS pro řešení JD Edwards

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizace zřizování uživatelských účtů pro tyto nově podporované aplikace SaaS

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** Monitorování & vytváření sestav

Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Přibliž](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Zástupné](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Další informace o tom, jak lépe zabezpečit organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) .

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Zobrazit průběh služby zřizování Azure AD v reálném čase

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit

Aktualizovali jsme prostředí pro zřizování Azure AD tak, aby obsahovalo nový indikátor průběhu, který vám ukáže, jak daleko jste v procesu zřizování uživatelů. Toto aktualizované prostředí obsahuje také informace o počtu uživatelů zřízených během aktuálního cyklu a také o tom, kolik uživatelů bylo zřízeno.

Další informace najdete v tématu o [kontrole stavu zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Branding společnosti se teď zobrazuje při odhlašování a chybových obrazovkách.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

Službu Azure AD jsme aktualizovali tak, aby se vaše firemní logo zobrazovalo na obrazovkách pro odhlášení a chyb a taky na přihlašovací stránce. Nemusíte nic dělat, abyste tuto funkci zapnuli, Azure AD jednoduše používá prostředky, které jste už nastavili v oblasti **značky společnosti** Azure Portal.

Další informace o nastavení firemního brandingu najdete v tématu [Přidání brandingu na stránky Azure Active Directory vaší organizace](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Server Azure Multi-Factor Authentication (MFA) už není k dispozici pro nová nasazení.

**Zadejte:** Zastaralé  
**Kategorie služby:** VÍCEFAKTOROVÉHO  
**Schopnost produktu:** Zabezpečení identity & ochrana

Od 1. července 2019 už Microsoft nenabídne MFA Server pro nová nasazení. Noví zákazníci, kteří chtějí vyžadovat službu Multi-Factor Authentication ve své organizaci, teď musí používat cloudové Multi-Factor Authentication Azure. Zákazníci, kteří si server MFA vyaktivovali před 1. července, se nezmění. Pořád budete moct stáhnout nejnovější verzi, získat budoucí aktualizace a vygenerovat přihlašovací údaje pro aktivaci.

Další informace najdete v tématu [Začínáme s Azure Multi-Factor Authentication Server](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Další informace o cloudových Multi-Factor Authenticationch Azure najdete v tématu [plánování cloudového nasazení azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>Květen 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Změna služby: budoucí podpora pro protokoly TLS 1,2 ve službě proxy aplikací

**Zadejte:** Plánování změn  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control

Abychom zákazníkům poskytli nejlepší šifrování v rámci své třídy, omezujeme přístup pouze na protokoly TLS 1,2 ve službě proxy aplikací. Tato změna se postupně zavádí pro zákazníky, kteří už používají jenom protokoly TLS 1,2, takže byste neměli vidět žádné změny.

Vyřazení TLS 1,0 a TLS 1,1 proběhne od 31. srpna 2019, ale budeme poskytovat další pokročilá oznámení, takže budete mít čas na tuto změnu připravovat. Aby se tato změna připravila, ujistěte se, že kombinace mezi klientem a serverem a prohlížečem, včetně všech klientů, které uživatelé používají pro přístup k aplikacím publikovaným pomocí proxy aplikací, se aktualizují tak, aby používaly protokol TLS 1,2 k údržbě připojení k aplikaci. Proxy služba. Další informace najdete v tématu [Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Pomocí sestavy využití a přehled můžete zobrazit přihlašovací údaje související s aplikacemi.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** Monitorování & vytváření sestav

Teď můžete použít sestavu využití a přehledů, která se nachází v oblasti **podnikové aplikace** Azure Portal, a získat tak pohled na vaše přihlašovací údaje zaměřené na aplikaci, včetně informací o:

- Nejpoužívanější aplikace používané pro vaši organizaci

- Aplikace s neúspěšnými přihlášeními

- Nejčastější chyby při přihlašování pro každou aplikaci

Další informace o této funkci najdete v tématu [Sestava využití a přehledů na portálu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report) .

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatizace zřizování uživatelů pro cloudové aplikace pomocí Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** Monitorování & vytváření sestav

Podle těchto nových kurzů použijte službu zřizování Azure AD k automatizaci vytváření, odstraňování a aktualizace uživatelských účtů pro následující cloudové aplikace:

- [Vyhovující](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Můžete také postupovat podle tohoto nového [kurzu Dropboxu](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), který poskytuje informace o tom, jak zřídit objekty skupiny.

Další informace o tom, jak lépe zabezpečit organizaci prostřednictvím automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Skóre Secure identity je teď dostupné ve službě Azure AD (Obecná dostupnost).

**Zadejte:** Nová funkce  
**Kategorie služby:** NENÍ K DISPOZICI  
**Schopnost produktu:** Zabezpečení identity & ochrana

Teď můžete monitorovat a zdokonalovat stav zabezpečení identity pomocí funkce skóre Secure identity ve službě Azure AD. Funkce skóre Secure identity využívá jeden řídicí panel, který vám může pomáhat:

- Objektivně změřte stav zabezpečení identity, a to na základě skóre mezi 1 a 223.

- Plánování vylepšení zabezpečení identity

- Kontrola úspěšnosti vašich vylepšení zabezpečení

Další informace o funkci skóre zabezpečení identity najdete v tématu [co je bezpečné skóre identity v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nyní je k dispozici nové prostředí Registrace aplikací (Obecná dostupnost).

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Vývojářské prostředí

Nové prostředí [Registrace aplikací](https://aka.ms/appregistrations) je teď všeobecně dostupné. Toto nové prostředí zahrnuje všechny klíčové funkce, se kterými jste obeznámeni z Azure Portal a portálu pro registraci aplikací a vylepšuje je prostřednictvím:

- **Lepší správa aplikací.** Místo prohlížení aplikací napříč různými portály teď můžete zobrazit všechny aplikace na jednom místě.

- **Zjednodušená registrace aplikace** Z vylepšeného prostředí navigace pro výběr oprávnění přepracované je teď snazší registrovat a spravovat vaše aplikace.

- **Podrobnější informace.** Můžete najít další podrobnosti o vaší aplikaci, včetně příruček pro rychlý Start a dalších.

Další informace najdete v části [Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/) a [prostředí registrace aplikací je teď všeobecně dostupné.](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) oznámení blogu

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nové funkce dostupné v rozhraní API pro rizikové uživatele pro identitu Protection

**Zadejte:** Nová funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana

S radostí oznamujeme, že teď můžete pomocí rozhraní API pro rizikové uživatele načítat historii rizik uživatelů, odvolat rizikové uživatele a potvrdit ohrožení uživatelů. Tato změna vám pomůže efektivněji aktualizovat stav rizika vašich uživatelů a pochopit jejich historii rizik.

Další informace najdete v [referenční dokumentaci k rozhraní API pro rizikové uživatele](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – květen 2019

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V květnu 2019 jsme do Galerie aplikací přidali tyto 21 nových aplikací s podporou federace:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [skutečné odkazy](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Replay](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo Sales](https://toutapp.com/login)propojování, [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [subsystéms](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [META4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), na [pracovišti](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [kobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Vylepšené vytváření a Správa skupin na portálu Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

Provedli jsme vylepšení prostředí souvisejících se skupinami na portálu Azure AD. Tato vylepšení umožňují správcům lépe spravovat seznamy skupin, seznamy členů a poskytovat další možnosti vytváření.

Mezi vylepšení patří:

- Základní filtrování podle typu členství a typu skupiny.

- Přidání nových sloupců, jako je zdrojová a e-mailová adresa.

- Možnost vícenásobného výběru skupin, členů a seznamů vlastníků pro snadné odstranění.

- Možnost výběru e-mailové adresy a přidání vlastníků během vytváření skupiny.

Další informace najdete v tématu [Vytvoření základní skupiny a přidání členů pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Konfigurace zásady pojmenování pro skupiny Office 365 na portálu Azure AD (Obecná dostupnost)

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

Správci teď můžou nakonfigurovat zásady pojmenování pro skupiny Office 365 pomocí portálu Azure AD. Tato změna pomáhá vymáhat konzistentní zásady vytváření názvů pro skupiny Office 365 vytvořené nebo upravované uživateli ve vaší organizaci.

Zásady pojmenování pro skupiny Office 365 můžete nakonfigurovat dvěma různými způsoby:

- Zadejte předpony nebo přípony, které se automaticky přidají do názvu skupiny.

- Nahrajte přizpůsobenou sadu blokovaných slov pro vaši organizaci, která nejsou povolená v názvech skupin (například generální ředitel, výplata, HR).

Další informace najdete v tématu [vymáhání zásady pojmenování pro skupiny Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Pro protokoly aktivit Azure AD jsou teď dostupné koncové body rozhraní Microsoft Graph API (Obecná dostupnost).

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

S radostí oznamujeme obecnou dostupnost Microsoft Graph Podpora koncových bodů rozhraní API pro protokoly aktivit služby Azure AD. V této verzi teď můžete používat protokol auditu Azure AD verze 1,0 jak v protokolu, tak i v protokolech rozhraní API pro přihlášení.

Další informace najdete v tématu [Přehled rozhraní API protokolu auditu služby Azure AD](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Správci teď můžou používat podmíněný přístup pro kombinovaný proces registrace (Public Preview).

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana  

Správci teď můžou vytvořit zásady podmíněného přístupu pro použití v rámci kombinované registrační stránky. To zahrnuje použití zásad pro povolení registrace v těchto případech:

- Uživatelé jsou v důvěryhodné síti.

- Uživatelé jsou nízkým rizikem při přihlašování.

- Uživatelé jsou na spravovaném zařízení.

- Uživatelé souhlasí s podmínkami použití organizace (podmínky použití).

Další informace o podmíněném přístupu a resetování hesla najdete v [blogovém příspěvku o podmíněném přístupu pro kombinované MFA a registraci resetování hesel služby Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Další informace o zásadách podmíněného přístupu pro kombinovaný proces registrace najdete v tématu [zásady podmíněného přístupu pro kombinovanou registraci](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Další informace o funkci Azure AD terms of use najdete v tématu [funkce Azure Active Directory podmínek použití](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---
