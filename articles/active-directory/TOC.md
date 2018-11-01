# [Dokumentace k Azure Active Directory](index.md)

# Přehled
## [Představení služby Azure Active Directory](fundamentals/active-directory-whatis.md)
## [Správa identit Azure](fundamentals/identity-fundamentals.md)
## [Vysvětlení řešení identit Azure](fundamentals/understand-azure-identity-solutions.md)
## [Přidružení předplatných Azure](fundamentals/active-directory-how-subscriptions-associated-directory.md)
## [Požadavky na data a rezidence](fundamentals/active-directory-data-storage-eu.md)
## [Nejčastější dotazy](fundamentals/active-directory-faq.md)
## [Co je nového](fundamentals/whats-new.md)


# Začínáme
## [Registrace do Azure AD Premium](fundamentals/active-directory-get-started-premium.md)
## [Přidání vlastního názvu domény](fundamentals/add-custom-domain.md)
## [Konfigurace značky společnosti](fundamentals/customize-branding.md)
## [Přidání uživatelů do Azure AD](fundamentals/add-users-azure-active-directory.md)
## [Přiřazení licencí k uživatelům](fundamentals/license-users-groups.md)
## [Konfigurace samoobslužného resetování hesla](authentication/quickstart-sspr.md)
## [Přidání informací o ochraně osobních údajů vaší organizace do Azure AD](active-directory-properties-area.md)
## [Přístup ke službě Azure Active Directory a vytvoření nového tenanta](fundamentals/active-directory-access-create-new-tenant.md)


# Postup
## Plánování a návrh
### [Pochopení architektury Azure AD](fundamentals/active-directory-architecture.md)
### [Mapování deklarací v Azure Active Directory](develop/active-directory-claims-mapping.md)
### [Nasazení řešení s hybridní identitou](hybrid/plan-hybrid-identity-design-considerations-overview.md)
#### Určení požadavků
##### [Identita](hybrid/plan-hybrid-identity-design-considerations-business-needs.md)
##### [Synchronizace adresářů](hybrid/plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Vícefaktorové ověřování](hybrid/plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Strategie životního cyklu identit](hybrid/plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [Plánování zabezpečení dat](hybrid/plan-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Ochrana dat](hybrid/plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Správa obsahu](hybrid/plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Řízení přístupu](hybrid/plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Reakce na incidenty](hybrid/plan-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Plánování životního cyklu identity
##### [Úlohy](hybrid/plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Strategie přijetí](hybrid/plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Další kroky](hybrid/plan-hybrid-identity-design-considerations-nextsteps.md)
#### [Porovnání nabídek](hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md)

## Správa uživatelů
### [Přidání nových uživatelů do Azure AD](fundamentals/add-users-azure-active-directory.md)
### [Správa uživatelských profilů](fundamentals/active-directory-users-profile-azure-portal.md)
### [Resetování uživatelského hesla](fundamentals/active-directory-users-reset-password-azure-portal.md)
### [Přiřazení uživatelů k rolím správce](fundamentals/active-directory-users-assign-role-azure-portal.md)
### [Přidání uživatelů typu host z jiného adresáře (B2B)](b2b/what-is-b2b.md)
#### [Správci přidávají uživatele B2B](b2b/add-users-administrator.md)
#### [Informační pracovníci přidávají uživatele B2B](b2b/add-users-information-worker.md)
#### [Rozhraní API a přizpůsobení](b2b/customize-invitation-api.md)
#### [Federace Googlu](b2b/google-federation.md)
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
### [Správa skupin](fundamentals/active-directory-groups-create-azure-portal.md)
### [Odstranění skupiny a jejích členů](fundamentals/active-directory-groups-delete-group.md)
### [Správa nastavení skupin](fundamentals/active-directory-groups-settings-azure-portal.md)
## [Správa sestav](reports-monitoring/overview-reports.md)
### [Aktivita přihlášení](reports-monitoring/concept-sign-ins.md)
### [Aktivita auditování](reports-monitoring/concept-audit-logs.md)
### [Ohrožení uživatelé](reports-monitoring/concept-user-at-risk.md)
### [Riziková přihlášení](reports-monitoring/concept-risky-sign-ins.md)
### [Rizikové události](reports-monitoring/concept-risk-events.md)
### [Monitorování protokolů pomocí Azure Monitoru](reports-monitoring/concept-activity-logs-in-azure-monitor.md)
### [Nejčastější dotazy](reports-monitoring/reports-faq.md)

### Úlohy
#### [Stažení sestavy přihlašování](reports-monitoring/quickstart-download-sign-in-report.md)
#### [Stažení sestavy auditu](reports-monitoring/quickstart-download-audit-report.md)
#### [Konfigurace pojmenovaných umístění](reports-monitoring/quickstart-configure-named-locations.md)
#### [Vyhledávání sestav aktivit](reports-monitoring/howto-find-activity-reports.md)
#### [Použití balíčku obsahu Power BI služby Azure AD](reports-monitoring/howto-power-bi-content-pack.md)
#### [Oprava uživatelů označených příznakem rizika](reports-monitoring/howto-remediate-users-flagged-for-risk.md)
#### [Směrování protokolů aktivit do centra událostí Azure](reports-monitoring/quickstart-azure-monitor-stream-logs-to-event-hub.md)
#### [Archivace protokolů aktivit do účtu úložiště Azure](reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)
#### [Integrace protokolů aktivit s využitím Splunku pomocí Azure Monitoru](reports-monitoring/tutorial-integrate-activity-logs-with-splunk.md)
#### [Integrace protokolů aktivit s využitím SumoLogicu pomocí Azure Monitoru](reports-monitoring/howto-integrate-activity-logs-with-sumologic.md)
#### [Integrace protokolů aktivit s využitím Log Analytics pomocí Azure Monitoru](reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

### Referenční informace
#### [Uchování](reports-monitoring/reference-reports-data-retention.md)
#### [Latence](reports-monitoring/reference-reports-latencies.md)
#### [Reference k aktivitě auditování](reports-monitoring/reference-audit-activities.md)
#### [Kódy chyb aktivit přihlašování](reports-monitoring/reference-sign-ins-error-codes.md)
#### [Interpretace schéma protokolu auditu ve službě Azure Monitor](reports-monitoring/reference-azure-monitor-audit-log-schema.md)
#### [Interpretace schéma protokolu přihlašování ve službě Azure Monitor](reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md)

### Řešení potíží
#### [Chybějící data v protokolech aktivit Azure AD](reports-monitoring/troubleshoot-missing-audit-data.md)
#### [Chybějící data v souborech ke stažení](reports-monitoring/troubleshoot-missing-data-download.md)
#### [Chyby balíčku obsahu protokolů aktivit Azure AD](reports-monitoring/troubleshoot-content-pack.md)
#### [Chyby v rozhraní API pro vytváření sestav Azure AD](reports-monitoring/troubleshoot-graph-api.md)

### [Programový přístup](reports-monitoring/concept-reporting-api.md)
#### [Požadavky](reports-monitoring/howto-configure-prerequisites-for-reporting-api.md)
#### [Použití certifikátů](reports-monitoring/tutorial-access-api-with-certificates.md)

## [Správa hesel](authentication/concept-sspr-howitworks.md)

## Správa aplikací
### [Přehled](manage-apps/what-is-application-management.md)
### [Začínáme](manage-apps/plan-an-application-integration.md)
### [Kurzy integrace aplikací SaaS](saas-apps/tutorial-list.md)

### [Zřizování a rušení zřízení uživatelů pro aplikace SaaS](manage-apps/user-provisioning.md) 
#### [Kurzy integrace aplikací](saas-apps/tutorial-list.md) 
#### [Automatizace zřizování pro aplikace s podporou SCIM](manage-apps/use-scim-to-provision-users-and-groups.md) 
#### [Přizpůsobení mapování atributů](manage-apps/customize-application-attributes.md) 
#### [Zápis výrazů pro mapování atributů](manage-apps/functions-for-customizing-application-data.md) 
#### [Použití filtrů oborů](manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) 
#### [Sestavy pro automatické zřizování uživatelů](manage-apps/check-status-user-account-provisioning.md) 
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
##### [Zástupné znaky](manage-apps/application-proxy-wildcard.md)
##### [Odebrání osobních údajů](manage-apps/application-proxy-remove-personal-data.md)


#### Názorné postupy publikování
##### [Vzdálená plocha](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Microsoft Teams](manage-apps/application-proxy-integrate-with-teams.md)
##### [Tableau](manage-apps/application-proxy-integrate-with-tableau.md)
##### [Qlik](manage-apps/application-proxy-qlik.md)
#### [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 

#### [Řešení problémů](manage-apps/application-proxy-troubleshoot.md)

### Správa podnikových aplikací
#### [Přidání aplikace](manage-apps/add-application-portal.md)
#### [Zobrazení aplikací klienta](manage-apps/view-applications-portal.md)
#### [Konfigurace jednotného přihlašování](manage-apps/configure-single-sign-on-portal.md)
#### [Přiřazení uživatelů](manage-apps/assign-user-or-group-access-portal.md)
#### [Přizpůsobení brandingu](manage-apps/change-name-or-logo-portal.md)
#### [Zákaz přihlašování uživatelů](manage-apps/disable-user-sign-in-portal.md)
#### [Odebrání uživatelů](manage-apps/remove-user-or-group-access-portal.md)

#### [Správa zřizování uživatelských účtů](manage-apps/configure-automatic-user-provisioning-portal.md)

#### [Pokročilé podepisování certifikátů pro aplikace SAML](manage-apps/certificate-signing-options.md)
#### [Skrytí aplikace v uživatelském prostředí](manage-apps/hide-application-from-user-portal.md)
### [Konfigurace automatického zrychlení přihlašování pomocí zásad HRD](manage-apps/configure-authentication-for-federated-users-portal.md)
### [Migrace aplikací AD FS do Azure AD](manage-apps/migrate-adfs-apps-to-azure.md) 
### [Správa přístupu k aplikacím](manage-apps/what-is-access-management.md)
#### [Přístup s jednotným přihlašováním](manage-apps/what-is-single-sign-on.md)
#### [Certifikáty pro jednotné přihlašování](manage-apps/manage-certificates-for-federated-single-sign-on.md)
#### [Exportní tenanta](manage-apps/tenant-restrictions.md)
#### [Použití uživatelů zřizování SCIM](manage-apps/use-scim-to-provision-users-and-groups.md)

### [Prostředí vyjádření souhlasu s aplikací Azure AD](develop/application-consent-experience.md)

### Řešení potíží



#### Přístupový panel
##### [Aplikace se nezobrazuje](manage-apps/access-panel-troubleshoot-application-not-appearing.md)
##### [Zobrazuje e neočekávaná aplikace](manage-apps/access-panel-troubleshoot-unexpected-application.md)
##### [Nejde se přihlásit](manage-apps/access-panel-troubleshoot-web-sign-in-problem.md)
##### [Chyba při instalaci rozšíření prohlížeče](manage-apps/access-panel-extension-problem-installing.md)
##### [Jak využívat samoobslužný přístup k aplikacím](manage-apps/access-panel-manage-self-service-access.md)
##### [Chyba při využívání samoobslužného přístupu k aplikacím](manage-apps/access-panel-troubleshoot-self-service-access.md)

#### Přidání aplikace
##### [Volba typu aplikace](manage-apps/choose-application-type.md)
##### [Běžné problémy – aplikace v galerii](manage-apps/adding-gallery-app-common-problems.md)
##### [Běžné problémy – aplikace mimo galerii](manage-apps/adding-non-gallery-app-common-problems.md)

#### Proxy aplikací
##### [Potíže při zobrazování stránky aplikace](manage-apps/application-proxy-page-appearance-broken-problem.md)
##### [Načítání aplikace trvá příliš dlouho](manage-apps/application-proxy-page-load-speed-problem.md)
##### [Odkazy na stránce aplikace nefungují](manage-apps/application-proxy-page-links-broken-problem.md)
##### [Jaké porty je potřeba odemknout pro mou aplikaci](manage-apps/application-proxy-connectivity-ports-how-to.md)
##### [Ve skupině konektorů není žádný fungující konektor pro mou aplikaci](manage-apps/application-proxy-connectivity-no-working-connector.md)
##### [Konfigurace na portálu pro správu](manage-apps/application-proxy-config-how-to.md)
##### [Konfigurace jednotného přihlašování k mé aplikaci](manage-apps/application-proxy-config-sso-how-to.md)
##### [Potíže při vytváření aplikace na portálu pro správu](manage-apps/application-proxy-config-problem.md)
##### [Konfigurace omezeného delegování Kerberos](manage-apps/application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
##### [Konfigurace s využitím PingAccess](manage-apps/application-proxy-back-end-ping-access-how-to.md)
##### [Chyba typu Není přístup k podnikové aplikaci](manage-apps/application-proxy-sign-in-bad-gateway-timeout-error.md)
##### [Potíže při instalaci konektoru agenta proxy aplikací](manage-apps/application-proxy-connector-installation-problem.md)


#### Registrace aplikací
##### [Zadání polí pro objekt aplikace](develop/registration-config-specific-application-property-how-to.md)
##### [Změna výchozího nastavení životnosti tokenu](develop/registration-config-change-token-lifetime-how-to.md)

#### Authentication
##### [Konfigurace koncových bodů](develop/registration-config-how-to.md)

#### Podmíněný přístup
##### [Zákazník nesplnil předběžné požadavky registrace zařízení](active-directory-conditional-access.md)
##### [Jak a kdy se uplatní vypnutá pravidla corpnet?](https://aka.ms/calocation)
##### [Jak zvýšit počet zařízení, které může uživatel zaregistrovat ve službě Azure AD?](active-directory-azureadjoin-setup.md)
##### [Jak nastavit podmíněný přístup pro Exchange Online?](https://aka.ms/csforexchange)
##### [Jak nastavit podmíněný přístup pro zařízení s Windows 7?](active-directory-conditional-access.md#device-based-conditional-access)
##### [Které aplikace jsou podporované s podmíněným přístupem?](active-directory-conditional-access-supported-apps.md)

#### Vyhledání rozhraní API
##### [Vyhledání rozhraní API](develop/api-find-an-api-how-to.md)

#### Správa přístupu
##### [Přiřazení uživatelů a skupin k aplikaci](manage-apps/methods-for-assigning-users-and-groups.md)
##### [Odebrání přístupu uživatele k aplikaci](manage-apps/methods-for-removing-user-access.md)
##### [Konfigurace samoobslužného přiřazení aplikace](manage-apps/manage-self-service-access.md)
##### [Přiřazený neočekávaný uživatel](manage-apps/ways-users-get-assigned-to-applications.md)
##### [Neočekávaná aplikace v seznamu aplikací](manage-apps/application-types.md)

#### Aplikace s více tenanty
##### [Konfigurace nové aplikace](develop/setup-multi-tenant-app.md)
##### [Přidání do galerie aplikací](develop/registration-config-multi-tenant-application-add-to-gallery-how-to.md)

#### Oprávnění
##### [Volba oprávnění pro rozhraní API](develop/perms-for-given-api.md)
##### [Delegovaná oprávnění versus oprávnění aplikace](develop/delegated-and-app-perms.md)
##### [Souhlas pro aplikaci](develop/consent-framework.md)

#### Zřizování
##### [Jak dlouho to trvá](manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)
##### [Hodiny – aplikace v galerii](manage-apps/application-provisioning-when-will-provisioning-finish.md)
##### [Konfigurace zřizování uživatelů – aplikace v galerii](manage-apps/application-provisioning-config-how-to.md)
##### [Potíže s konfigurací zřizování uživatelů – aplikace v galerii](manage-apps/application-provisioning-config-problem.md)
##### [Potíže při ukládání přihlašovacích údajů správce při konfigurací zřizování uživatelů – aplikace v galerii](manage-apps/application-provisioning-config-problem-storage-limit.md)
##### [Uživatelé nejsou zřízení – aplikace v galerii](manage-apps/application-provisioning-config-problem-no-users-provisioned.md)
##### [Zřízení nesprávných uživatelů konfigurací zřizování uživatelů – aplikace v galerii](manage-apps/application-provisioning-config-problem-wrong-users-provisioned.md)

#### Jednotné přihlašování
##### [Volba metody](manage-apps/single-sign-on-modes.md)
##### [Konfigurace](develop/registration-config-sso-how-to.md)
##### [Konfigurace federovaného jednotného přihlašování – aplikace v galerii](manage-apps/configure-federated-single-sign-on-gallery-applications.md)
##### [Běžné potíže s konfigurací federovaného jednotného přihlašování – aplikace v galerii](manage-apps/configure-federated-single-sign-on-gallery-applications-problems.md)
##### [Konfigurace federovaného jednotného přihlašování – aplikace mimo galerii](manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
##### [Běžné potíže s konfigurací federovaného jednotného přihlašování – aplikace mimo galerii](manage-apps/configure-federated-single-sign-on-non-gallery-applications-problems.md)
##### [Konfigurace hesel – aplikace v galerii](manage-apps/configure-password-single-sign-on-gallery-applications.md)
##### [Běžné potíže s konfigurací hesel – aplikace v galerii](manage-apps/configure-password-single-sign-on-gallery-applications-problems.md)
##### [Konfigurace hesel – aplikace mimo galerii](manage-apps/configure-password-single-sign-on-non-gallery-applications.md)
##### [Běžné potíže s konfigurací hesel – aplikace mimo galerii](manage-apps/configure-password-single-sign-on-non-gallery-applications-problems.md)

#### Potíže s přihlášením uživatelů
##### [Neočekávaná výzva k poskytnutí souhlasu](manage-apps/application-sign-in-unexpected-user-consent-prompt.md)
##### [Chyba souhlasu uživatele](manage-apps/application-sign-in-unexpected-user-consent-error.md)
##### [Potíže s přihlašováním z vlastního portálu](manage-apps/application-sign-in-other-problem-deeplink.md)
##### [Potíže s přihlašováním z přístupového panelu](manage-apps/application-sign-in-other-problem-access-panel.md)
##### [Chyba na přihlašovací stránce aplikace](manage-apps/application-sign-in-problem-application-error.md)
##### [Potíže s jednotným přihlašováním pomocí hesla – aplikace mimo galerii](manage-apps/application-sign-in-problem-password-sso-non-gallery.md)
##### [Potíže s jednotným přihlašováním pomocí hesla – aplikace v galerii](manage-apps/application-sign-in-problem-password-sso-gallery.md)
##### [Potíže s přihlášením k aplikaci Microsoftu](manage-apps/application-sign-in-problem-first-party-microsoft.md)
##### [Potíže s federovaným jednotným přihlašováním – aplikace mimo galerii](manage-apps/application-sign-in-problem-federated-sso-non-gallery.md)
##### [Potíže s federovaným jednotným přihlašováním – aplikace v galerii](manage-apps/application-sign-in-problem-federated-sso-gallery.md)
##### [Potíže s vlastní vyvinutou aplikací](manage-apps/application-sign-in-problem-custom-dev.md)
##### [Potíže s místní aplikací – proxy aplikací](manage-apps/application-sign-in-problem-on-premises-application-proxy.md)


## Správa adresáře
### [Azure AD Connect](hybrid/whatis-hybrid-identity.md)
### Vlastní názvy domén
#### [Rychlý start](fundamentals/add-custom-domain.md)
### [Správa adresáře](fundamentals/active-directory-administer.md)
### [Integrace místních identit pomocí Azure AD Connect](hybrid/whatis-hybrid-identity.md)

### [Konfigurace životnosti tokenů](develop/active-directory-configurable-token-lifetimes.md)

## Zabezpečení identit

### [Privileged Identity Management](privileged-identity-management/pim-configure.md?toc=%2fazure%2factive-directory%2ftoc.json)



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

# Související
## [Multi-Factor Authentication](/azure/multi-factor-authentication/)
## [Azure AD Connect](hybrid/whatis-hybrid-identity.md)
## [Azure AD Connect Health](hybrid/whatis-hybrid-identity-health.md)
## [Azure AD pro vývojáře](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/pim-configure.md)

# Zdroje a prostředky
## [Plány nasazení služby Azure AD](./fundamentals/active-directory-deployment-plans.md)
## [Fórum Azure pro názory](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Plány Azure do budoucna](https://azure.microsoft.com/roadmap/?category=security-identity)
## [Fórum MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Ceny](https://azure.microsoft.com/pricing/details/active-directory/)
## [ Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/)
## [Aktualizace služeb](https://azure.microsoft.com/updates/?product=active-directory)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Videa](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
