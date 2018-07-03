# [Dokumentace k Azure Active Directory](index.md)

# Přehled
## [Představení služby Azure Active Directory](fundamentals/active-directory-whatis.md)
## [Správa identit Azure](fundamentals/identity-fundamentals.md)
## [Vysvětlení řešení identit Azure](fundamentals/understand-azure-identity-solutions.md)
## [Volba řešení s hybridní identitou](choose-hybrid-identity-solution.md)
## [Přidružení předplatných Azure](fundamentals/active-directory-how-subscriptions-associated-directory.md)
## [Požadavky na data a rezidence](fundamentals/active-directory-data-storage-eu.md)
## [Nejčastější dotazy](fundamentals/active-directory-faq.md)
## [Co je nového](fundamentals/whats-new.md)


# Začínáme
## [Začínáme s Azure AD](fundamentals/get-started-azure-ad.md)
## [Registrace do Azure AD Premium](fundamentals/active-directory-get-started-premium.md)
## [Přidání vlastního názvu domény](fundamentals/add-custom-domain.md)
## [Konfigurace značky společnosti](fundamentals/customize-branding.md)
## [Přidání uživatelů do Azure AD](fundamentals/add-users-azure-active-directory.md)
## [Přiřazení licencí k uživatelům](fundamentals/license-users-groups.md)
## [Konfigurace samoobslužného resetování hesla](authentication/quickstart-sspr.md)
## [Přidání informací o ochraně osobních údajů vaší organizace do Azure AD](active-directory-properties-area.md)


# Postup
## Plánování a návrh
### [Pochopení architektury Azure AD](fundamentals/active-directory-architecture.md)
### [Mapování deklarací v Azure Active Directory](active-directory-claims-mapping.md)
### [Nasazení řešení s hybridní identitou](active-directory-hybrid-identity-design-considerations-overview.md)
#### Určení požadavků
##### [Identita](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [Synchronizace adresářů](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Vícefaktorové ověřování](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Strategie životního cyklu identit](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [Plánování zabezpečení dat](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Ochrana dat](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Správa obsahu](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Řízení přístupu](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Reakce na incidenty](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Plánování životního cyklu identity
##### [Úlohy](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Strategie přijetí](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Další kroky](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [Porovnání nabídek](active-directory-hybrid-identity-design-considerations-tools-comparison.md)

## Správa uživatelů
### [Přidání nových uživatelů do Azure AD](fundamentals/add-users-azure-active-directory.md)
### [Správa uživatelských profilů](fundamentals/active-directory-users-profile-azure-portal.md)
### [Sdílení účtů](active-directory-sharing-accounts.md)
### [Přiřazení uživatelů k rolím správce](fundamentals/active-directory-users-assign-role-azure-portal.md)
### [Obnovení odstraněného uživatele](fundamentals/active-directory-users-restore.md)
### [Přidání uživatelů typu host z jiného adresáře (B2B)](b2b/what-is-b2b.md)
#### [Správci přidávají uživatele B2B](b2b/add-users-administrator.md)
#### [Informační pracovníci přidávají uživatele B2B](b2b/add-users-information-worker.md)
#### [Rozhraní API a přizpůsobení](b2b/customize-invitation-api.md)
#### [Ukázky kódu a Azure PowerShellu](b2b/code-samples.md)
#### [Ukázka portálu se samoobslužnou registrací](b2b/self-service-portal.md)
#### [-mailová pozvánka](b2b/invitation-email-elements.md)
#### [Uplatnění pozvánky](b2b/redemption-experience.md)
#### [Přidání uživatelů B2B bez pozvánky](b2b/add-user-without-invite.md)
#### [Povolení nebo blokování pozvánek](b2b/allow-deny-list.md)
#### [Podmíněný přístup pro B2B](b2b/conditional-access.md)
#### [Zásady sdílení B2B](b2b/delegate-invitations.md)
#### [Přidání uživatele B2B do role](b2b/add-guest-to-role.md)
#### [Dynamické skupiny a uživatelé B2B](b2b/use-dynamic-groups.md)
#### [Opuštění organizace](b2b/leave-the-organization.md)
#### [Auditování a sestavy](b2b/auditing-and-reporting.md)
#### [B2B pro hybridní organizace](b2b/hybrid-organizations.md)
##### [Udělení přístupu uživatelům B2B k místním aplikacím](b2b/hybrid-cloud-to-on-premises.md)
##### [Udělení přístupu místním uživatelům ke cloudovým aplikacím](b2b/hybrid-on-premises-to-cloud.md)
#### [B2B a externí sdílení Office 365](b2b/o365-external-user.md)
#### [Licencování B2B](b2b/licensing-guidance.md)
#### [Aktuální omezení](b2b/current-limitations.md)
#### [Nejčastější dotazy](b2b/faq.md)
#### [Řešení potíží s B2B](b2b/troubleshoot.md)
#### [Principy uživatelů B2B](b2b/user-properties.md)
#### [Token uživatele B2B](b2b/user-token.md)
#### [B2B pro integrované aplikace Azure AD](b2b/configure-saas-apps.md)
#### [Mapování deklarací identity uživatelů B2B](b2b/claims-mapping.md)
#### [Porovnání spolupráce B2B s B2C](b2b/compare-with-b2c.md)
#### [Získání podpory pro B2B](b2b/get-support.md)

## [Správa skupin a členů](fundamentals/active-directory-manage-groups.md)
### Správa skupin
#### [Azure Portal](fundamentals/active-directory-groups-create-azure-portal.md)
#### [Azure AD PowerShell pro Graph (v2)](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
#### [Azure AD PowerShell MSOnline](active-directory-accessmanagement-groups-settings-cmdlets.md)
### [Správa členů skupin](fundamentals/active-directory-groups-members-azure-portal.md)
### [Správa vlastníků skupin](fundamentals/active-directory-accessmanagement-managing-group-owners.md)
### [Správa členství ve skupinách](fundamentals/active-directory-groups-membership-azure-portal.md)
### [Přiřazení licencí pomocí skupin](fundamentals/active-directory-licensing-whatis-azure-portal.md)
#### [Přiřazení licencí ke skupině](active-directory-licensing-group-assignment-azure-portal.md)
#### [Identifikace a řešení potíží s licencemi ve skupině](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [Migrace jednotlivě licencovaných uživatelů na licencování na základě skupin](active-directory-licensing-group-migration-azure-portal.md)
#### [Migrace uživatelů mezi licencemi produktů](active-directory-licensing-group-product-migration.md)
#### [Další scénáře pro licencování na základě skupin](active-directory-licensing-group-advanced.md)
#### [Příklady Azure PowerShellu pro licencování na základě skupin](active-directory-licensing-ps-examples.md)
#### [Referenční informace k produktům a plánům služeb v Azure AD](active-directory-licensing-product-and-service-plan-reference.md)
### [Nastavení vypršení platnosti skupin Office 365](active-directory-groups-lifecycle-azure-portal.md)
### [Vynucení zásad pojmenování pro skupiny](groups-naming-policy.md)
### [Zobrazení všech skupin](fundamentals/active-directory-groups-view-azure-portal.md)
### [Přidání skupiny k aplikacím SaaS](active-directory-accessmanagement-group-saasapps.md)
### [Obnovení odstraněné skupiny Office 365](fundamentals/active-directory-groups-restore-azure-portal.md)
### [Správa nastavení skupin](fundamentals/active-directory-groups-settings-azure-portal.md) 
### Vytváření rozšířených pravidel
#### [Azure Portal](active-directory-groups-dynamic-membership-azure-portal.md)
### [Vytvoření samoobslužných skupin](active-directory-accessmanagement-self-service-group-management.md)
### [Řešení problémů](active-directory-accessmanagement-troubleshooting.md)

## [Správa sestav](active-directory-reporting-azure-portal.md)
### [Aktivita přihlášení](active-directory-reporting-activity-sign-ins.md)
### [Aktivita auditování](active-directory-reporting-activity-audit-logs.md)
### [Ohrožení uživatelé](active-directory-reporting-security-user-at-risk.md)
### [Riziková přihlášení](active-directory-reporting-security-risky-sign-ins.md)
### [Rizikové události](active-directory-reporting-risk-events.md)
### [Nejčastější dotazy](active-directory-reporting-faq.md)
### Úlohy
#### [Konfigurace pojmenovaných umístění](active-directory-named-locations.md)
#### [Vyhledávání sestav aktivit](active-directory-reporting-migration.md)
#### [Použití sady Azure Active Directory Power BI Content Pack](active-directory-reporting-power-bi-content-pack-how-to.md)
#### [Oprava uživatelů označených příznakem rizika](active-directory-report-security-user-at-risk-remediation.md)
### Referenční informace
#### [Uchování](active-directory-reporting-retention.md)
#### [Latence](active-directory-reporting-latencies-azure-portal.md)
#### [Reference k aktivitě auditování](active-directory-reporting-activity-audit-reference.md)
#### [Kódy chyb aktivit přihlašování](active-directory-reporting-activity-sign-ins-errors.md)
#### [Multi-Factor Authentication](active-directory-reporting-activity-sign-ins-mfa.md)



### Řešení potíží
#### [Chybějící data auditu](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [Chybějící data v souborech ke stažení](active-directory-reporting-troubleshoot-missing-data-download.md)
#### [Chyby balíčku obsahu protokolů aktivit Azure Active Directory](active-directory-reporting-troubleshoot-content-pack.md)
### [Programový přístup](active-directory-reporting-api-getting-started-azure-portal.md)
#### [Požadavky](active-directory-reporting-api-prerequisites-azure-portal.md)
#### [Ukázky auditu](active-directory-reporting-api-audit-samples.md)
#### [Ukázky přihlašování](active-directory-reporting-api-sign-in-activity-samples.md)
#### [Použití certifikátů](active-directory-reporting-api-with-certificates.md)

## Správa hesel
### [Přehled hesel](authentication/active-directory-passwords-overview.md)
### Dokumenty uživatele
#### [Resetování nebo změna hesla](active-directory-passwords-update-your-own-password.md)
#### [Osvědčené postupy pro hesla](active-directory-secure-passwords.md)
#### [Registrace samoobslužného resetování hesla](active-directory-passwords-reset-register.md)
### [Jak funguje SSPR](authentication/concept-sspr-howitworks.md)
### [Průvodce nasazením SSPR](authentication/howto-sspr-deployment.md)
### [SSPR a Windows 10](authentication/tutorial-sspr-windows.md)
### [Zásady SSPR](authentication/concept-sspr-policy.md)
### [Přizpůsobení SSPR](authentication/concept-sspr-customization.md)
### [Požadavky SSPR na data](authentication/howto-sspr-authenticationdata.md)
### [Vytváření sestav SSPR](authentication/howto-sspr-reporting.md)
### [Inteligentní uzamčení](authentication/howto-password-smart-lockout.md)
### [Eliminace slabých hesel](authentication/concept-password-ban-bad.md)
### [Konfigurace seznamu zakázaných hesel](authentication/howto-password-ban-bad.md)
### [Místní integrace](authentication/concept-password-ban-bad-on-premises.md)
### [Nasazení ochrany hesel Azure AD](authentication/howto-password-ban-bad-on-premises.md)
### [Konfigurace ochrany hesel Azure AD](authentication/howto-password-ban-bad-on-premises-operations.md)
### [Monitorování ochrany hesel Azure AD](authentication/howto-password-ban-bad-on-premises-troubleshoot.md)
### Správci IT: Resetování hesel
#### [Azure Portal](fundamentals/active-directory-users-reset-password-azure-portal.md)
### [Samoobslužné resetování hesla licence](authentication/concept-sspr-licensing.md)
### [Zpětný zápis hesla](authentication/howto-sspr-writeback.md)
### [Řešení problémů](authentication/active-directory-passwords-troubleshoot.md)
### [Nejčastější dotazy](authentication/active-directory-passwords-faq.md)


## Správa zařízení
### [Úvod](device-management-introduction.md)
### [Pomocí webu Azure Portal](device-management-azure-portal.md)
### [Plánování služby Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
### [Nejčastější dotazy](device-management-faq.md)
### Úlohy
#### [Nastavení zařízení s Windows 10 registrovaných v Azure AD](device-management-azuread-registered-devices-windows10-setup.md)
#### [Nastavení zařízení připojených k Azure AD](device-management-azuread-joined-devices-setup.md)
#### [Nastavení hybridních zařízení připojených k Azure AD](device-management-hybrid-azuread-joined-devices-setup.md)
#### [Nasazení na místě](active-directory-device-registration-on-premises-setup.md)
#### [Připojení k Azure AD během prvního spuštění Windows 10](device-management-azuread-joined-devices-frx.md)
### Řešení potíží
#### [Hybridní zařízení s Windows 10 a Windows Serverem 2016 připojená k Azure AD](device-management-troubleshoot-hybrid-join-windows-current.md)
#### [Hybridní zařízení s Windows starší verze připojená k Azure AD](device-management-troubleshoot-hybrid-join-windows-legacy.md)

## Správa aplikací
### [Přehled](manage-apps/what-is-application-management.md)
### [Začínáme](manage-apps/plan-an-application-integration.md)
### [Kurzy integrace aplikací SaaS](saas-apps/tutorial-list.md)

### [Zřizování a rušení zřízení uživatelů pro aplikace SaaS](active-directory-saas-app-provisioning.md) 
#### [Kurzy integrace aplikací](saas-apps/tutorial-list.md) 
#### [Automatizace zřizování pro aplikace s podporou SCIM](manage-apps/use-scim-to-provision-users-and-groups.md) 
#### [Přizpůsobení mapování atributů](active-directory-saas-customizing-attribute-mappings.md) 
#### [Zápis výrazů pro mapování atributů](active-directory-saas-writing-expressions-for-attribute-mappings.md) 
#### [Použití filtrů oborů](active-directory-saas-scoping-filters.md) 
#### [Sestavy pro automatické zřizování uživatelů](active-directory-saas-provisioning-reporting.md) 
#### [Řešení potíží se zřizováním uživatelů](active-directory-application-provisioning-content-map.md) 

### [Vzdálený přístup k aplikací s App Proxy](manage-apps/application-proxy.md)
#### Začínáme
##### [Povolení proxy aplikace](manage-apps/application-proxy-enable.md)
##### [Publikování aplikací](manage-apps/application-proxy-publish-azure-portal.md)
##### [Vlastní domény](manage-apps/application-proxy-configure-custom-domain.md)
#### [Jednotné přihlašování](manage-apps/application-proxy-single-sign-on.md)
##### [Jednotné přihlašování s KCD](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
##### [Jednotné přihlašování se záhlavími](manage-apps/application-proxy-configure-single-sign-on-with-ping-access.md)
##### [Jednotné přihlašování s ukládáním hesel do trezoru](manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)
#### Koncepty
##### [Konektory](manage-apps/application-proxy-connectors.md)
##### [Zabezpečení](manage-apps/application-proxy-security.md)
##### [Sítě](manage-apps/application-proxy-network-topology.md)


##### [Upgrade z TMG nebo UAG](manage-apps/application-proxy-migration.md)

#### Pokročilá konfigurace
##### [Publikování v samostatných sítích](manage-apps/application-proxy-connector-groups.md)
##### [Proxy servery](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)
##### [Aplikace pracující s deklaracemi](manage-apps/application-proxy-configure-for-claims-aware-applications.md)
##### [Nativní klientské aplikace](manage-apps/application-proxy-configure-native-client-application.md)
##### [Bezobslužná instalace](manage-apps/application-proxy-register-connector-powershell.md)
##### [Vlastní domovská stránka](manage-apps/application-proxy-configure-custom-home-page.md)
##### [Převod vložených odkazů](manage-apps/application-proxy-configure-hard-coded-link-translation.md)
##### [Zástupné znaky](active-directory-application-proxy-wildcard.md)
##### [Odebrání osobních údajů](manage-apps/application-proxy-remove-personal-data.md)


#### Názorné postupy publikování
##### [Vzdálená plocha](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Microsoft Teams](manage-apps/application-proxy-integrate-with-teams.md)
##### [Tableau](manage-apps/application-proxy-integrate-with-tableau.md)
##### [Qlik](active-directory-application-proxy-qlik.md)
#### [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 

#### [Řešení problémů](manage-apps/application-proxy-troubleshoot.md)

### Správa podnikových aplikací
#### [Přiřazení uživatelů](manage-apps/assign-user-or-group-access-portal.md)
#### [Přizpůsobení brandingu](manage-apps/change-name-or-logo-portal.md)
#### [Zákaz přihlašování uživatelů](manage-apps/disable-user-sign-in-portal.md)
#### [Odebrání uživatelů](manage-apps/remove-user-or-group-access-portal.md)
#### [Zobrazení všech mých aplikací](manage-apps/view-applications-portal.md)
#### [Správa zřizování uživatelských účtů](manage-apps/configure-automatic-user-provisioning-portal.md)
#### [Správa jednotného přihlašování pro podnikové aplikace](manage-apps/configure-single-sign-on-portal.md)
#### [Pokročilé podepisování certifikátů pro aplikace SAML](manage-apps/certificate-signing-options.md)
#### [Skrytí aplikace v uživatelském prostředí](manage-apps/hide-application-from-user-portal.md)
### [Konfigurace automatického zrychlení přihlašování pomocí zásad HRD](manage-apps/configure-authentication-for-federated-users-portal.md)
### [Migrace aplikací AD FS do Azure AD](manage-apps/migrate-adfs-apps-to-azure.md) 
### [Správa přístupu k aplikacím](manage-apps/what-is-access-management.md)
#### [Přístup s jednotným přihlašováním](manage-apps/what-is-single-sign-on.md)
#### [Certifikáty pro jednotné přihlašování](manage-apps/manage-certificates-for-federated-single-sign-on.md)
#### [Exportní tenanta](manage-apps/tenant-restrictions.md)
#### [Použití uživatelů zřizování SCIM](manage-apps/use-scim-to-provision-users-and-groups.md)


### Řešení potíží



#### Přístupový panel
##### [Aplikace se nezobrazuje](application-access-panel-unexpected-application-not-appearing.md)
##### [Zobrazuje e neočekávaná aplikace](application-access-panel-unexpected-application-appears.md)
##### [Nejde se přihlásit](application-access-panel-web-sign-in-problem.md)
##### [Chyba při instalaci rozšíření prohlížeče](application-access-panel-extension-problem-installing.md)
##### [Jak využívat samoobslužný přístup k aplikacím](application-access-panel-self-service-applications-how-to.md)
##### [Chyba při využívání samoobslužného přístupu k aplikacím](application-access-panel-self-service-applications-problem.md)

#### Přidání aplikace
##### [Volba typu aplikace](application-config-add-app-problem-how-to-choose-application-type.md)
##### [Běžné problémy – aplikace v galerii](application-config-add-app-problem-problem-adding-gallery-app.md)
##### [Běžné problémy – aplikace mimo galerii](application-config-add-app-problem-problem-adding-non-gallery-app.md)

#### Proxy aplikací
##### [Potíže při zobrazování stránky aplikace](application-proxy-page-appearance-broken-problem.md)
##### [Načítání aplikace trvá příliš dlouho](application-proxy-page-load-speed-problem.md)
##### [Odkazy na stránce aplikace nefungují](application-proxy-page-links-broken-problem.md)
##### [Jaké porty je potřeba odemknout pro mou aplikaci](application-proxy-connectivity-ports-how-to.md)
##### [Ve skupině konektorů není žádný fungující konektor pro mou aplikaci](application-proxy-connectivity-no-working-connector.md)
##### [Konfigurace na portálu pro správu](application-proxy-config-how-to.md)
##### [Konfigurace jednotného přihlašování k mé aplikaci](application-proxy-config-sso-how-to.md)
##### [Potíže při vytváření aplikace na portálu pro správu](application-proxy-config-problem.md)
##### [Konfigurace omezeného delegování Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
##### [Konfigurace s využitím PingAccess](application-proxy-back-end-ping-access-how-to.md)
##### [Chyba typu Není přístup k podnikové aplikaci](application-proxy-sign-in-bad-gateway-timeout-error.md)
##### [Potíže při instalaci konektoru agenta proxy aplikací](application-proxy-connector-installation-problem.md)


#### Registrace aplikací
##### [Zadání polí pro objekt aplikace](application-dev-registration-config-specific-application-property-how-to.md)
##### [Změna výchozího nastavení životnosti tokenu](application-dev-registration-config-change-token-lifetime-how-to.md)

#### Authentication
##### [Konfigurace koncových bodů](application-dev-registration-config-how-to.md)

#### Podmíněný přístup
##### [Zákazník nesplnil předběžné požadavky registrace zařízení](active-directory-conditional-access.md)
##### [Tenant je blokovaný kvůli nesprávnému nastavení zásad podmíněného přístupu](active-directory-conditional-access-device-remediation.md)
##### [Jak a kdy se uplatní vypnutá pravidla corpnet?](https://aka.ms/calocation)
##### [Jak zvýšit počet zařízení, které může uživatel zaregistrovat ve službě Azure AD?](active-directory-azureadjoin-setup.md)
##### [Jak nastavit podmíněný přístup pro Exchange Online?](https://aka.ms/csforexchange)
##### [Jak nastavit podmíněný přístup pro zařízení s Windows 7?](active-directory-conditional-access.md#device-based-conditional-access)
##### [Které aplikace jsou podporované s podmíněným přístupem?](active-directory-conditional-access-supported-apps.md)

#### Vyhledání rozhraní API
##### [Vyhledání rozhraní API](application-dev-api-find-an-api-how-to.md)

#### Správa přístupu
##### [Přiřazení uživatelů a skupin k aplikaci](application-access-assignment-how-to-add-assignment.md)
##### [Odebrání přístupu uživatele k aplikaci](application-access-assignment-how-to-remove-assignment.md)
##### [Konfigurace samoobslužného přiřazení aplikace](application-access-self-service-how-to.md)
##### [Přiřazený neočekávaný uživatel](application-access-unexpected-user-assignment.md)
##### [Neočekávaná aplikace v seznamu aplikací](application-access-unexpected-application.md)

#### Aplikace s více tenanty
##### [Konfigurace nové aplikace](application-dev-setup-multi-tenant-app.md)
##### [Přidání do galerie aplikací](application-dev-registration-config-multi-tenant-application-add-to-gallery-how-to.md)

#### Oprávnění
##### [Volba oprávnění pro rozhraní API](application-dev-perms-for-given-api.md)
##### [Udělení oprávnění pro mou aplikaci](application-dev-registration-config-grant-permissions-how-to.md)
##### [Delegovaná oprávnění versus oprávnění aplikace](application-dev-delegated-and-app-perms.md)
##### [Souhlas pro aplikaci](application-dev-consent-framework.md)

#### Zřizování
##### [Jak dlouho to trvá](application-provisioning-when-will-provisioning-finish-specific-user.md)
##### [Hodiny – aplikace v galerii](application-provisioning-when-will-provisioning-finish.md)
##### [Konfigurace zřizování uživatelů – aplikace v galerii](application-provisioning-config-how-to.md)
##### [Potíže s konfigurací zřizování uživatelů – aplikace v galerii](application-provisioning-config-problem.md)
##### [Potíže při ukládání přihlašovacích údajů správce při konfigurací zřizování uživatelů – aplikace v galerii](application-provisioning-config-problem-storage-limit.md)
##### [Uživatelé nejsou zřízení – aplikace v galerii](application-provisioning-config-problem-no-users-provisioned.md)
##### [Zřízení nesprávných uživatelů konfigurací zřizování uživatelů – aplikace v galerii](application-provisioning-config-problem-wrong-users-provisioned.md)

#### Jednotné přihlašování
##### [Volba metody](application-config-sso-how-to-choose-sign-on-method.md)
##### [Konfigurace](application-dev-registration-config-sso-how-to.md)
##### [Konfigurace federovaného jednotného přihlašování – aplikace v galerii](application-config-sso-how-to-configure-federated-sso-gallery.md)
##### [Běžné potíže s konfigurací federovaného jednotného přihlašování – aplikace v galerii](application-config-sso-problem-configure-federated-sso-gallery.md)
##### [Konfigurace federovaného jednotného přihlašování – aplikace mimo galerii](application-config-sso-how-to-configure-federated-sso-non-gallery.md)
##### [Běžné potíže s konfigurací federovaného jednotného přihlašování – aplikace mimo galerii](application-config-sso-problem-configure-federated-sso-non-gallery.md)
##### [Konfigurace hesel – aplikace v galerii](application-config-sso-how-to-configure-password-sso-gallery.md)
##### [Běžné potíže s konfigurací hesel – aplikace v galerii](application-config-sso-problem-configure-password-sso-gallery.md)
##### [Konfigurace hesel – aplikace mimo galerii](application-config-sso-how-to-configure-password-sso-non-gallery.md)
##### [Běžné potíže s konfigurací hesel – aplikace mimo galerii](application-config-sso-problem-configure-password-sso-non-gallery.md)

#### Potíže s přihlášením uživatelů
##### [Neočekávaná výzva k poskytnutí souhlasu](application-sign-in-unexpected-user-consent-prompt.md)
##### [Chyba souhlasu uživatele](application-sign-in-unexpected-user-consent-error.md)
##### [Potíže s přihlašováním z vlastního portálu](application-sign-in-other-problem-deeplink.md)
##### [Potíže s přihlašováním z přístupového panelu](application-sign-in-other-problem-access-panel.md)
##### [Chyba na přihlašovací stránce aplikace](application-sign-in-problem-application-error.md)
##### [Potíže s jednotným přihlašováním pomocí hesla – aplikace mimo galerii](application-sign-in-problem-password-sso-non-gallery.md)
##### [Potíže s jednotným přihlašováním pomocí hesla – aplikace v galerii](application-sign-in-problem-password-sso-gallery.md)
##### [Potíže s přihlášením k aplikaci Microsoftu](application-sign-in-problem-first-party-microsoft.md)
##### [Potíže s federovaným jednotným přihlašováním – aplikace mimo galerii](application-sign-in-problem-federated-sso-non-gallery.md)
##### [Potíže s federovaným jednotným přihlašováním – aplikace v galerii](application-sign-in-problem-federated-sso-gallery.md)
##### [Potíže s vlastní vyvinutou aplikací](application-sign-in-problem-custom-dev.md)
##### [Potíže s místní aplikací – proxy aplikací](application-sign-in-problem-on-premises-application-proxy.md)

### [Vývoj aplikací](active-directory-applications-guiding-developers-for-lob-applications.md)
### [Knihovna dokumentů](active-directory-apps-index.md)

## Správa adresáře
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Vlastní názvy domén
#### [Rychlý start](fundamentals/add-custom-domain.md)
#### [Přidání vlastních názvů domén](active-directory-domains-manage-azure-portal.md)
### [Správa adresáře](fundamentals/active-directory-administer.md)
### [Odstranění adresáře](directory-delete-howto.md)
### [Několik adresářů](active-directory-licensing-directory-independence.md)
### [Samoobslužné přihlášení](active-directory-self-service-signup.md)
### [Převzetí kontroly nad nespravovaným adresářem](domains-admin-takeover.md)
### [Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Povolení](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Nastavení zásad skupiny](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Nastavení Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [Nejčastější dotazy](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Řešení problémů](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


### [Integrace místních identit pomocí Azure AD Connect](./connect/active-directory-aadconnect.md)

## Delegování přístupu k prostředkům
### [Zobrazení členů role správce](directory-manage-roles-portal.md)
### [Role správce](active-directory-assign-admin-roles-azure-portal.md)
#### [Přiřazení role správce uživateli](fundamentals/active-directory-users-assign-role-azure-portal.md)
#### [Porovnání oprávnění pro členy a uživatele typu host](fundamentals/users-default-permissions.md)
### [Posílení zabezpečení role správce](admin-roles-best-practices.md)  
#### [Vytvoření účtů správce pro nouzový přístup](active-directory-admin-manage-emergency-access-accounts.md)
### [Jednotky pro správu](active-directory-administrative-units-management.md)
### [Konfigurace životnosti tokenů](active-directory-configurable-token-lifetimes.md)

## Kontroly přístupu
### [Přehled kontrol přístupu](active-directory-azure-ad-controls-access-reviews-overview.md)
### [Dokončení kontroly přístupu](active-directory-azure-ad-controls-complete-access-review.md)
### [Vytvoření kontroly přístupu](active-directory-azure-ad-controls-create-access-review.md)
### [Provádění kontroly přístupu](active-directory-azure-ad-controls-perform-access-review.md)
### [Jak kontrolovat přístup](active-directory-azure-ad-controls-how-to-review-your-access.md)
### [Přístup hosta pomocí kontrol přístupu](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
### [Správa přístupu uživatelů pomocí kontrol](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
### [Správa programů a ovládacích prvků](active-directory-azure-ad-controls-manage-programs-controls.md)
### [Načtení výsledků kontroly přístupu](active-directory-azure-ad-controls-retrieve-access-review.md)

## Zabezpečení identit
### [Podmíněný přístup](active-directory-conditional-access-azure-portal.md)
#### [Začínáme](active-directory-conditional-access-azure-portal-get-started.md)
#### Šablony Rychlý start
##### [Konfigurace na základě MFA cloudových aplikací](active-directory-conditional-access-app-based-mfa.md)
##### [Vyžadování přijetí podmínek použití](active-directory-conditional-access-tou.md)
#### Kurzy
##### [Migrace klasických zásad MFA](active-directory-conditional-access-migration-mfa.md)
#### Koncepty
##### [Základní ochrana](active-directory-conditional-access-baseline-protection.md)
##### [Podmínky](active-directory-conditional-access-conditions.md)
##### [Podmínky umístění](active-directory-conditional-access-locations.md)
##### [Ovládací prvky](active-directory-conditional-access-controls.md)
##### [Nástroj pro analýzy „co kdyby“](active-directory-conditional-access-whatif.md)
##### [Vysvětlení zásad zařízení pro služby Office 365](active-directory-conditional-access-device-policies.md)
#### Návody
##### [Osvědčené postupy](active-directory-conditional-access-best-practices.md)
##### [Konfigurace zásad podmíněného přístupu pro pokusy o přístup z nedůvěryhodných sítí](active-directory-conditional-access-untrusted-networks.md)
##### [Nastavení podmíněného přístupu na základě zařízení](active-directory-conditional-access-policy-connected-applications.md)
##### [Nastavení podmíněného přístupu na základě aplikace](active-directory-conditional-access-mam.md)
##### [Podmínky použití pro uživatele a aplikace](active-directory-tou.md)
##### [Migrace klasických zásad](active-directory-conditional-access-migration.md)
##### [Nastavení možností připojení sítě VPN](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/always-on-vpn-deploy)
##### [Nastavení SharePointu a Exchange Online](active-directory-conditional-access-no-modern-authentication.md)
##### [Nápravy](active-directory-conditional-access-device-remediation.md)
#### [Technické referenční informace](active-directory-conditional-access-technical-reference.md)
#### [Nejčastější dotazy](active-directory-conditional-faqs.md)

### Ověřování pomocí certifikátů
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
#### [Začínáme](active-directory-certificate-based-authentication-get-started.md)

### [Azure AD Identity Protection](active-directory-identityprotection.md)
#### [Povolení](active-directory-identityprotection-enable.md)
#### [Rozpoznání ohrožení zabezpečení](active-directory-identityprotection-vulnerabilities.md)
#### [Rizikové události](active-directory-identity-protection-risk-events.md)
#### [Oznámení](active-directory-identityprotection-notifications.md)
#### [Přihlašovací prostředí](active-directory-identityprotection-flows.md)
#### [Simulace rizikových událostí](active-directory-identityprotection-playbook.md)
#### [Odblokování uživatelů](active-directory-identityprotection-unblock-howto.md)
#### [Nejčastější dotazy](active-directory-identity-protection-faqs.md)
#### [Glosář](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](active-directory-privileged-identity-management-configure.md)

## Integrace ostatních služeb s Azure AD 
### [Integrace LinkedInu s Azure AD](linkedin-integration.md)

## [Nasazení AD FS v Azure](active-directory-aadconnect-azure-adfs.md)
### [Vysoká dostupnost](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Změna podpisového hashovacího algoritmu](active-directory-federation-sha256-guidance.md)

## [Řešení problémů](fundamentals/active-directory-troubleshooting-support-howto.md)

## Nasazení testování konceptu (POC) Azure AD
### [Scénáře PoC: Úvod](active-directory-playbook-intro.md)
### [Scénáře PoC: Složky](active-directory-playbook-ingredients.md)
### [Scénáře PoC: Implementace](active-directory-playbook-implementation.md)
### [Scénáře PoC: Stavební bloky](active-directory-playbook-building-blocks.md)


# Referenční informace
## [Ukázky kódu](https://azure.microsoft.com/resources/samples/?service=active-directory)
## [Rutiny Azure PowerShellu](/powershell/azure/overview)
## [Referenční informace k Java API](/java/api)
## [.NET API](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)
## [Limity a omezení služby](active-directory-service-limits-restrictions.md)

# Související
## [Multi-Factor Authentication](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [Azure AD pro vývojáře](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

# Zdroje a prostředky
## [Fórum Azure pro názory](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Plány Azure do budoucna](https://azure.microsoft.com/roadmap/?category=security-identity)
## [Fórum MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Ceny](https://azure.microsoft.com/pricing/details/active-directory/)
## [ Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/)
## [Aktualizace služeb](https://azure.microsoft.com/updates/?product=active-directory)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Videa](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
