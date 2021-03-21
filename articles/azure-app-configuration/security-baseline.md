---
title: Základní hodnoty zabezpečení Azure pro konfiguraci aplikací Azure
description: Základní hodnoty zabezpečení konfigurace aplikace Azure obsahují doprovodné materiály a zdroje pro implementaci doporučení zabezpečení, která jsou určená v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8b68e22e69bf2d4a1655883b249f883f98f565a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101091240"
---
# <a name="azure-security-baseline-for-azure-app-configuration"></a>Základní hodnoty zabezpečení Azure pro konfiguraci aplikací Azure

Tato základní hodnota zabezpečení se vztahuje na pokyny od [verze Azure Security test 2,0](../security/benchmarks/overview.md) na konfiguraci aplikací Azure. Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure. Obsah se seskupuje pomocí **ovládacích prvků zabezpečení** , které definuje srovnávací test zabezpečení Azure, a souvisejících pokynů týkajících se konfigurace aplikací Azure. Neplatí **ovládací prvky** pro konfiguraci aplikací Azure.

Pokud chcete zjistit, jak konfigurace aplikace Azure úplně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot zabezpečení Azure App Configuration](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementace zabezpečení pro interní provoz

**Pokyny**: Konfigurace aplikace Azure neimplementuje žádné prostředky přímo do virtuální sítě. Vzhledem k tomu, že služba není nasazena do virtuální sítě, nemůžete využít určité síťové funkce k zabezpečení interních přenosů služby, jako jsou například skupiny zabezpečení sítě, směrovací tabulky nebo jiná síťová zařízení, například Azure Firewall. Konfigurace aplikace ale umožňuje používat privátní koncové body pro zabezpečené připojení k Azure App Configuration z virtuální sítě.

Pomocí ověřování Azure můžete zjistit použití starších nezabezpečených protokolů, jako jsou SSL/TLSv1, SMBv1, LM/ověřovací NTLMv1, wDigest, nepodepsané vazby LDAP a slabá šifra v protokolu Kerberos.

- [Používání privátních koncových bodů pro konfiguraci aplikací Azure](concept-private-endpoint.md)

- [Sešit nezabezpečených protokolů služby Azure Sentinel](../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

### <a name="ns-2-connect-private-networks-together"></a>NS-2: spojování privátních sítí

**Doprovodné** materiály: Konfigurace aplikace Azure podporuje použití privátních koncových bodů k bezpečnému posílání dat prostřednictvím privátního propojení. Pomocí Azure ExpressRoute nebo virtuální privátní sítě (VPN) Azure můžete vytvořit privátní připojení mezi datacentry Azure a místní infrastrukturou v prostředí s okolním umístěním. Připojení ExpressRoute nevyužívají veřejný Internet a nabízejí spolehlivější, rychlejší a nižší latenci než typická připojení k Internetu. Pro sítě VPN typu Point-to-site a VPN typu Site-to-site můžete připojit místní zařízení nebo sítě k virtuální síti pomocí libovolné kombinace těchto možností sítě VPN a Azure ExpressRoute.

Pokud chcete propojit dvě nebo víc virtuálních sítí v Azure společně, použijte partnerský vztah virtuálních sítí. Síťový provoz mezi partnerskými virtuálními sítěmi je privátní a udržuje se v páteřní síti Azure.

- [Jaké jsou modely připojení ExpressRoute](../expressroute/expressroute-connectivity-models.md)

- [Azure VPN – přehled](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Partnerský vztah virtuální sítě](../virtual-network/virtual-network-peering-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Zřízení přístupu privátní sítě ke službám Azure

**Pokyny**: pomocí privátního odkazu Azure můžete z virtuálních sítí povolit privátní přístup ke konfiguraci aplikací Azure bez přechodu na Internet.

Soukromý přístup je kromě ověřování a zabezpečení provozu nabízených službami Azure další hloubkovou mírou ochrany.

- [Principy privátního odkazu Azure](../private-link/private-link-overview.md)

- [Postup nastavení privátního odkazu v konfiguraci aplikace Azure](concept-private-endpoint.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Ochrana aplikací a služeb před útoky z externích sítí

**Pokyny**: při přístupu k hodnotám konfigurace prostřednictvím virtuální sítě je třeba chránit prostředky proti útokům z externích sítí, včetně útoků DDoS (Distributed Denial of Service), útoků specifických pro aplikace a nevyžádaných a potenciálně škodlivých internetových přenosů. Pomocí Azure Firewall můžete chránit aplikace a služby před potenciálně škodlivým provozem z Internetu a dalších externích umístění. Chraňte své prostředky proti útokům DDoS tím, že ve svých virtuálních sítích Azure povolíte standardní ochranu DDoS. Použijte Azure Security Center k detekci rizik s konfigurací, které souvisí s vašimi prostředky souvisejícími se sítí.

Konfigurace aplikace Azure není určena ke spouštění webových aplikací, poskytuje konfiguraci pro tyto webové aplikace. Nemusíte konfigurovat žádné další nastavení ani nasazovat žádné další síťové služby, které byste měli chránit před útoky z externích sítí, které cílí na webové aplikace.

- [Dokumentace k Azure Firewall](../firewall/index.yml)

- [Správa Azure DDoS Protection Standard pomocí Azure Portal](../ddos-protection/manage-ddos-protection.md)

- [Doporučení pro Azure Security Center](../security-center/recommendations-reference.md#recs-networking)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: nasazení systémů pro zabránění vniknutí/prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: použití Azure firewall s filtrováním na základě hrozeb pro výstrahy a/nebo blokování provozu do a ze známých škodlivých IP adres a domén. Zdrojem těchto IP adres a domén je kanál analýzy hrozeb Microsoftu. Když je nutná kontrola datové části, můžete nasadit řešení ID a IP adresy třetích stran z Azure Marketplace s možnostmi kontroly zatížení. Alternativně můžete použít ID hostitele/IP adresy nebo řešení pro detekci a odpověď na základě hostitele (EDR) ve spojení s nebo místo síťových ID/IP adres.

Poznámka: Pokud máte regulativní nebo jiný požadavek na použití IDENTIFIKÁTORů/IP adres, ujistěte se, že je vždycky vyladěný tak, aby poskytoval vysoce kvalitní výstrahy pro řešení SIEM.

- [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace zahrnuje možnosti ID třetích stran.](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [EDR schopnost služby Microsoft Defender ATP](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: zjednodušení pravidel zabezpečení sítě

**Pokyny**: pomocí značek služeb Azure Virtual Network můžete definovat řízení přístupu k síti u skupin zabezpečení sítě nebo Azure firewall nakonfigurovaných pro prostředky konfigurace vaší aplikace. Při vytváření pravidel zabezpečení pro odchozí přenosy v síti aplikace můžete použít značku služby "AppConfiguration" místo konkrétních IP adres. Zadáním názvu značky služby v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

- [Pochopení a používání značek služeb](../virtual-network/service-tags-overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="identity-management"></a>Správa identit

*Další informace najdete v tématu [Azure Security Benchmark: správa identit](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardizace Azure Active Directory jako centrálního systému pro identifikaci a ověřování

**Doprovodné** materiály: Konfigurace aplikace Azure je integrovaná s Azure Active Directory (Azure AD), která je výchozí službou pro správu identit a přístupu Azure. Službu Azure AD byste měli standardizovat, abyste mohli řídit identitu a správu přístupu vaší organizace v nástroji:
- Microsoft Cloud prostředky, jako jsou Azure Portal, Azure Storage, virtuální počítače Azure (Linux a Windows), Azure Key Vault, PaaS a SaaS aplikace.
- prostředky vaší organizace, jako jsou aplikace v Azure nebo prostředky vaší podnikové sítě

Zabezpečení služby Azure AD by mělo mít vysokou prioritu v praxi cloudového zabezpečení vaší organizace. Azure AD poskytuje bezpečnostní skóre identity, které vám pomůže vyhodnotit stav zabezpečení identity vzhledem k doporučeným osvědčeným postupům od Microsoftu. Pomocí skóre můžete vyhodnotit, jak přesně vaše konfigurace vyhovuje doporučeným osvědčeným postupům, a provádět v zabezpečení vylepšení.

Azure poskytuje následující předdefinované role Azure pro autorizaci přístupu ke konfiguračním datům aplikací pomocí Azure AD a OAuth:

- Vlastník konfiguračních dat aplikací: pomocí této role můžete k datům konfigurace aplikace udělit přístup pro čtení, zápis a odstranění. Tím se neudělí přístup k prostředku konfigurace aplikace.

- Čtečka dat konfigurace aplikace: tuto roli použijte, pokud chcete udělit přístup pro čtení k datům konfigurace aplikace. Tím se neudělí přístup k prostředku konfigurace aplikace.

- Přispěvatel: pomocí této role můžete spravovat prostředek konfigurace aplikace. I když k datům konfigurace aplikace můžete přistupovat pomocí přístupových klíčů, tato role neuděluje přímý přístup k datům pomocí Azure AD.

- Čtecí modul: tuto roli použijte, pokud chcete udělit přístup pro čtení prostředku konfigurace aplikace. Tím se neudělí přístup k přístupovým klíčům prostředku ani k datům uloženým v konfiguraci aplikace.

Další informace najdete v následujících referenčních materiálech:

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Co je skóre zabezpečení identity v Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

- [Autorizace přístupu ke konfiguraci aplikací Azure pomocí Azure AD](concept-enable-rbac.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Zabezpečená a automatická správa identit aplikací

**Pokyny**: pomocí identit spravovaných Azure získáte přístup ke konfiguraci aplikací Azure z jiných než lidských účtů, jako jsou třeba jiné služby Azure. Místo vytvoření výkonnějšího lidského účtu pro přístup k prostředkům nebo jejich vykonání za účelem omezení nutnosti spravovat další přihlašovací údaje se doporučuje použít funkci Azure Managed identity. Ke konfiguraci aplikace Azure se dá také přiřadit spravovaná identita, která nativně ověřuje jiné služby a prostředky Azure, které podporují ověřování Azure AD. To může být užitečné, pokud chcete povolit snadný přístup z konfigurace aplikace na Azure Key Vault při načítání tajných kódů. Při použití spravovaných identit se identita spravuje na platformě Azure a nevyžaduje zřizování ani střídání tajných kódů.

Konfigurace aplikací Azure podporuje vaši aplikaci pro udělení dvou typů identit:
- Identita přiřazená systémem je svázána s vaším prostředkem konfigurace. Odstraní se, pokud je prostředek konfigurace odstraněný. Prostředek konfigurace může mít pouze jednu identitu přiřazenou systémem.
- Identita přiřazená uživatelem je samostatný prostředek Azure, který se dá přiřadit k prostředku konfigurace. Prostředek konfigurace může mít několik uživatelsky přiřazených identit.

Pokud se spravované identity nedají využít, vytvořte instanční objekt s omezenými oprávněními na úrovni prostředků konfigurace aplikace Azure. Konfigurujte tyto instanční objekty pomocí přihlašovacích údajů k certifikátu a vraťte se pouze k klientským tajným klíčům. V obou případech se Azure Key Vault dají použít ve spojení se spravovanými identitami Azure, takže běhové prostředí (například funkce Azure) může přihlašovací údaje získat z trezoru klíčů.

- [Jak používat spravované identity pro konfiguraci aplikací Azure](overview-managed-identity.md)

- [Spravované identity Azure](../active-directory/managed-identities-azure-resources/overview.md)

- [Služby, které podporují spravované identity prostředků Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Použití spravovaných identit pro přístup ke službě App Configuration](howto-integrate-azure-managed-service-identity.md)

- [Instanční objekt Azure](/powershell/azure/create-azure-service-principal-azureps) 

- [Vytvoření instančního objektu s certifikáty](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Použití Azure Key Vault k registraci objektu zabezpečení](../key-vault/general/authentication.md#app-identity-and-security-principals)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Použití jednotného přihlašování (SSO) Azure AD pro přístup k aplikacím

**Pokyny**: Konfigurace aplikace azure používá Azure Active Directory (Azure AD) k zajištění správy identit a přístupu k prostředkům Azure, cloudovým aplikacím a místním aplikacím. Patří sem podnikové identity, jako jsou zaměstnanci, i externí identity, jako jsou partneři a dodavatelé. Azure AD umožňuje jednotné přihlašování (SSO) spravovat službu konfigurace aplikací prostřednictvím Azure Portal pomocí všech synchronizovaných identit podnikových služeb Active Directory. Připojte všechny své uživatele, aplikace a zařízení k Azure AD, ať máte bezproblémový, zabezpečený přístup a lepší viditelnost a kontrolu.

- [Principy jednotného přihlašování k aplikacím pomocí Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Použití řídicích prvků silného ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Konfigurace aplikace Azure používá Azure Active Directory, která podporuje ovládací prvky silného ověřování prostřednictvím vícefaktorového ověřování (MFA) a silné metody bez hesla.
- Vícefaktorové ověřování – povolte vícefaktorové ověřování Azure AD a projděte si doporučení pro správu identit a přístupu služby Azure Security Center, odkud při instalaci MFA využijte některé osvědčené postupy. Vícefaktorové ověřování se dá vynutit u všech uživatelů, u vybraných uživatelů nebo u jednotlivých uživatelů na základě podmínek přihlašování a rizikových faktorů.
- Ověřování bez hesla – k dispozici jsou tři možnosti ověřování bez hesla: Windows Hello pro firmy, aplikace Microsoft Authenticator a místní metody ověřování, jako jsou čipové karty.

Pro správce a privilegované uživatele zkontrolujte, že je použitá nejvyšší úroveň silného ověřování, a pak uveďte příslušné zásady silného ověřování pro ostatní uživatele.

Poznámka: vícefaktorové ověřování se dá vyhovět na uživatelských účtech, které přistupují k konfiguraci aplikace, ale ne na programových účtech služby. Používejte ověřování bez hesla, například spravované identity, pokud je to možné, a vynuťte MFA u všech uživatelských účtů.

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Seznámení s možnostmi ověřování bez hesla pro Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorování a upozornění na anomálie účtů

**Doprovodné** materiály: Konfigurace aplikace Azure je integrovaná s Azure Active Directory v nástroji, která poskytuje následující zdroje dat:

-   Přihlášení – Sestava přihlášení poskytuje informace o využití spravovaných aplikací a aktivitách přihlašování uživatelů.

-   Protokoly auditu – poskytuje sledovatelnost prostřednictvím protokolů pro všechny změny provedené prostřednictvím různých funkcí služby Azure AD. Příklady protokolů auditu v protokolovaných změnách zahrnují přidávání nebo odebírání uživatelů, aplikací, skupin, rolí a zásad.

-   Riziková přihlášení – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.

-   Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Tyto zdroje dat je možné integrovat s Azure Monitor, s Sentinelou v Azure a systémy SIEM třetích stran.

Azure Security Center může také upozorňovat na určité podezřelé aktivity, jako je například nadměrný počet neúspěšných pokusů o ověření a zastaralých účtů v rámci předplatného. 

Rozšířená ochrana před internetovými útoky (ATP) je řešení zabezpečení, které umožňuje pomocí místních signálů služby Active Directory identifikovat, detekovat a prozkoumat pokročilé hrozby, ohrožené identity a škodlivé akce programu Insider.

- [Sestavy aktivit auditu v Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Zobrazení rizikových přihlášení Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Identifikace uživatelů Azure AD označených příznakem rizikové aktivity](../active-directory/identity-protection/overview-identity-protection.md)

- [Monitorování identit a aktivit přístupu uživatelů ve službě Azure Security Center](../security-center/security-center-identity-access.md)

- [Upozornění v modulu ochrany na základě analýzy hrozeb služby Azure Security Center](../security-center/alerts-reference.md)

- [Jak integrovat protokoly aktivit Azure do Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Připojení dat z Azure AD Identity Protection](../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Advanced Threat Protection](/azure-advanced-threat-protection/what-is-atp)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Omezení přístupu k prostředkům Azure na základě podmínek

**Pokyny**: Konfigurace aplikace Azure podporuje podmíněný přístup Azure Active Directory (Azure AD) pro přesnější řízení přístupu na základě uživatelem definovaných podmínek, jako je třeba přihlášení uživatelů z určitých ROZSAHŮ IP adres, aby se přihlášení používalo. Podrobné zásady správy relace ověřování se také dají použít pro různé případy použití. Tyto zásady podmíněného přístupu se budou vztahovat jenom na uživatelské účty, které se ověřují ve službě Azure AD pro přístup ke službě konfigurace aplikací a jejich správu, ale nevztahují se na instanční objekty ani připojovací řetězce, které se připojují ke konfiguračnímu prostředku.

- [Přehled podmíněného přístupu Azure](../active-directory/conditional-access/overview.md)

- [Obecné zásady podmíněného přístupu](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurace správy relací ověřování pomocí podmíněného přístupu](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminace nezamýšleného prozrazení přihlašovacích údajů

**Doprovodné** materiály: Konfigurace aplikace Azure umožňuje zákazníkům ukládat konfigurace, které můžou potenciálně obsahovat identity nebo tajné klíče. Pro identifikaci přihlašovacích údajů v konfiguracích doporučujeme implementovat kontrolu přihlašovacích údajů. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

Použijte službu Azure App Configuration Service společně s Azure Key Vault. Uložte přihlašovací údaje v Key Vault a pak je propojte s přihlašovacími údaji vytvořením Key Vault odkazu v prostředku konfigurace aplikace. Když konfigurace aplikace vytvoří tyto odkazy, uloží identifikátory URI Key Vault hodnoty místo samotných hodnot. Aplikace se mohou připojit ke konfiguraci aplikace, aby načetly jakékoli přihlašovací údaje z Key Vault.

V případě GitHubu můžete k identifikaci přihlašovacích údajů nebo jiné formy tajných kódů v kódu použít funkci nativního skenování tajných kódů.

- [Kurz použití Key Vaultových odkazů v aplikaci ASP.NET Core](use-key-vault-references-dotnet-core.md)

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Skenování tajných kódů GitHubu](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="privileged-access"></a>Privilegovaný přístup

*Další informace najdete v tématu [Azure Security Benchmark: privilegovaný přístup](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Ochrana a omezení vysoce privilegovaných uživatelů

**Doprovodné** materiály: Omezte počet vysoce privilegovaných účtů nebo rolí a chraňte tyto účty na vyšší úrovni, protože uživatelé s tímto oprávněním můžou přímo nebo nepřímo číst a upravovat všechny prostředky v prostředí Azure.

S využitím služby Azure AD Privileged Identity Management (PIM) můžete povolit privilegovaný přístup podle potřeby (JIT) k prostředkům Azure a Azure AD. JIT uděluje dočasná oprávnění k provádění privilegovaných úloh, pouze když je uživatelé potřebují. PIM může také generovat upozornění zabezpečení v případě podezřelých nebo nebezpečných aktivit ve vaší organizaci Azure AD.

Přístupové klíče jsou vysoce privilegované a měly by se pravidelně střídat jako osvědčený postup zabezpečení. Přístupové klíče obsahují připojovací řetězce, které obsahují informace o přihlašovacích údajích a jsou považovány za tajné. Tyto tajné klíče je potřeba uložit v Azure Key Vault a váš kód se musí ověřit, aby se Key Vault, aby se načetly. Přístupové klíče můžou aplikaci udělit přístup pro čtení i zápis nebo jen pro čtení. Zajistěte, aby byl vydán správný typ přístupového klíče, aby se zabránilo neoprávněnému přístupu. Abyste byli lépe zabezpečeni, použijte funkci spravované identity ve službě Azure AD. To vyžaduje, aby aplikace měly adresu URL koncového bodu konfigurace pro přístup k hodnotám konfigurace.

- [Osvědčené postupy konfigurace aplikace](howto-best-practices.md#app-configuration-bootstrap)

- [Použití spravovaných identit pro přístup ke službě App Configuration](howto-integrate-azure-managed-service-identity.md)
- [Oprávnění role správce v Azure AD](../active-directory/roles/permissions-reference.md)

- [Používání upozornění zabezpečení služby Azure Privileged Identity Management](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](../active-directory/roles/security-planning.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Omezení přístupu pro správu k nejdůležitějším podnikovým systémům

**Doprovodné** materiály: Konfigurace aplikace Azure využívá Azure RBAC k izolaci přístupu k podnikovým systémům tím, že omezí přístup k důležitým účtům. Konfigurace aplikací na úrovni prostředků podporuje Azure RBAC. Pro bezpečné vystavování důležitých podnikových konfigurací tyto informace ukládáte do vlastního prostředku konfigurace aplikace. V rámci prostředku je k dispozici také podrobný přístup prostřednictvím účtů nebo klíčů pro přístup jen pro čtení a také popisků a označování.

Všechny typy ovládacích prvků přístupu by se měly zarovnávat na strategii segmentace vaší organizace, aby se zajistilo jednotné řízení přístupu.

- [Přístup ke skupině pro správu](../governance/management-groups/overview.md#management-group-access)

- [Správci předplatného Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Integrace RBAC pomocí Azure AD s konfigurací aplikace](concept-enable-rbac.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Pravidelná kontrola a sjednocování přístupu uživatelů

**Pokyny**: Konfigurace aplikace Azure používá ke správě svých prostředků účty Azure Active Directory (Azure AD), kontrolu uživatelských účtů a přiřazení přístupu pravidelně, aby bylo zajištěno, že účty a jejich přístup jsou platné. 

Azure poskytuje následující předdefinované role Azure pro autorizaci přístupu ke konfiguračním datům aplikací pomocí Azure AD a OAuth:

- Vlastník konfiguračních dat aplikací: pomocí této role můžete k datům konfigurace aplikace udělit přístup pro čtení, zápis a odstranění. Tím se neudělí přístup k prostředku konfigurace aplikace.

- Čtečka dat konfigurace aplikace: tuto roli použijte, pokud chcete udělit přístup pro čtení k datům konfigurace aplikace. Tím se neudělí přístup k prostředku konfigurace aplikace.

Pomocí kontrol přístupu ke službě Azure AD můžete kontrolovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí, jako jsou výše uvedené role konfigurace aplikace. Vytváření sestav Azure AD může poskytovat protokoly, které vám pomůžou zjistit zastaralé účty. K usnadnění procesu kontroly můžete použít také Azure AD Privileged Identity Management k vytvoření pracovního postupu sestavy kontroly přístupu.

Poznámka: spravované identity jsou navržené tam, kde je to možné pro ověřování konfigurace aplikace z jiné služby nebo aplikace. Při použití bude nutné spravovat jakékoli instanční objekty nebo připojovací řetězce nakonfigurované s přístupem ke konfiguraci aplikace zvlášť.

- [Vytvoření kontroly přístupu pro role prostředků Azure v Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Používání kontrol přístupu a identit Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Autorizace přístupu ke konfiguraci aplikací Azure pomocí Azure AD](concept-enable-rbac.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Nastavení nouzového přístupu v Azure AD

**Doprovodné** materiály: Konfigurace aplikace Azure je integrovaná s Azure Active Directory pro správu prostředků. Pokud se chcete vyhnout náhodnému uzamčení vaší organizace Azure AD, nastavte účet pro nouzový přístup pro přístup, když nebudete moct použít normální účty pro správu. Účty pro nouzový přístup jsou obvykle vysoce privilegované a neměly by se přiřazovat konkrétním jednotlivcům. Jejich použití je omezené na nouzové nebo kritické situace, kdy se nedají použít běžné účty pro správu.

Měli byste zajistit, aby přihlašovací údaje (například heslo, certifikát nebo čipová karta) pro účty pro nouzový přístup byly zabezpečené a aby o nich věděli jenom ti, kteří jsou oprávněni je použít jenom v případě nouze.

- [Správa účtů pro nouzový přístup v Azure AD](../active-directory/roles/security-emergency-access.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizace správy nároků 

**Doprovodné** materiály: Konfigurace aplikace Azure je integrovaná s Azure Active Directory pro správu prostředků. Pomocí funkcí správy opravňujících ke službě Azure AD můžete automatizovat pracovní postupy pro žádosti o přístup, včetně přiřazení přístupu, revizí a vypršení platnosti. Podporuje se také duální nebo vícenásobné schválení.

- [Co jsou kontroly přístupu Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Co je Správa nároků Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Použití pracovních stanic s privilegovaným přístupem

**Pokyny:** Zabezpečené, izolované pracovní stanice jsou nesmírně důležité pro zabezpečení citlivých rolí, jako jsou správci, vývojáři a obsluha nejdůležitějších služeb. Pro úlohy správy související s konfigurací aplikace Používejte vysoce zabezpečené uživatelské pracovní stanice nebo Azure bastionu. K nasazení zabezpečené a spravované uživatelské pracovní stanice pro úlohy správy použijte Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP) nebo Microsoft Intune. Zabezpečené pracovní stanice se dají centrálně spravovat, aby byla vynucena zabezpečená konfigurace, včetně silného ověřování, základních úrovní softwaru a hardwaru a omezeného logického a síťového přístupu.

- [Pochopení pracovních stanic s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Nasazení pracovní stanice s privilegovaným přístupem](/security/compass/privileged-access-deployment)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Využívání správy jen v potřebném rozsahu (princip nejnižší úrovně nutných oprávnění) 

**Pokyny**: Konfigurace aplikace Azure je integrovaná s řízením přístupu na základě role (RBAC) Azure pro správu prostředků. Azure RBAC umožňuje spravovat přístup k prostředkům Azure prostřednictvím přiřazení rolí. Tyto role můžete přiřadit uživatelům, skupinám instančních objektů a spravovaným identitám. Pro konfiguraci aplikací Azure jsou předem definované předdefinované role a tyto role můžou být v inventáři nebo prostřednictvím nástrojů, jako je Azure CLI, Azure PowerShell nebo Azure Portal. Oprávnění k prostředkům přiřazená prostřednictvím Azure RBAC by vždy měla být omezená pouze na to, co konkrétní role vyžadují. Tento přístup doplňuje přístup založený na přístupu podle potřeby (JIT) ve službě Azure AD Privileged Identity Management (PIM) a měl by se pravidelně kontrolovat.

Azure poskytuje následující předdefinované role Azure pro autorizaci přístupu ke konfiguračním datům aplikací pomocí Azure AD a OAuth:
- Vlastník konfiguračních dat aplikací: pomocí této role můžete k datům konfigurace aplikace udělit přístup pro čtení, zápis a odstranění. Tím se neudělí přístup k prostředku konfigurace aplikace.
- Čtečka dat konfigurace aplikace: tuto roli použijte, pokud chcete udělit přístup pro čtení k datům konfigurace aplikace. Tím se neudělí přístup k prostředku konfigurace aplikace.

Pomocí integrovaných rolí můžete přidělit oprávnění a v případě potřeby vytvářet pouze vlastní role. 

Konfigurace aplikace podporuje ukládání konfigurace více aplikací v jednom prostředku konfigurace aplikace. Pokud chcete omezit přístup k informacím mezi aplikacemi, vytvořte prostředek konfigurace aplikace na aplikaci a odpovídajícím způsobem nastavte Azure RBAC.

- [Co je řízení přístupu na základě role v Azure (Azure RBAC)](../role-based-access-control/overview.md)

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Používání kontrol přístupu a identit Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Autorizace přístupu ke konfiguraci aplikací Azure pomocí Azure AD](concept-enable-rbac.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: výběr procesu schválení pro podporu Microsoftu  

**Pokyny**: Implementujte schvalovací proces organizace pro scénáře podpory, kde Microsoft může potřebovat přístup k datům konfigurace vaší aplikace. Customer Lockbox není aktuálně k dispozici pro scénáře podpory konfigurace aplikace.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1: zjištění, klasifikace a označení citlivých dat

**Doprovodné** materiály: Zjistěte, klasifikujte a označte svoje citlivá data, abyste mohli navrhnout příslušné ovládací prvky, abyste zajistili, že se citlivé informace ukládají, zpracovávají a bezpečně odesílají technologické systémy organizace. Označování citlivých informací ve formě označení je podporované pro prostředky konfigurace aplikace, ale ne pro hodnoty konfigurace, které jsou v nich obsažené. Jakmile má aplikace přístup pro čtení nebo čtení a zápis do úložiště konfigurace, má úplný přístup ke všem konfiguracím v úložišti.

- [Označování citlivých informací s využitím služby Azure Information Protection](/azure/information-protection/what-is-information-protection)

- [Označení klasifikací dat v Azure](/azure/cloud-adoption-framework/govern/policy-compliance/data-classification#tagging-data-classification-in-azure)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Ochrana citlivých dat

**Doprovodné** materiály: pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a chrání před ztrátou a expozicí zákaznických dat. V zájmu zajištění zabezpečení zákaznických dat v rámci Azure Microsoft implementovat určité výchozí kontrolní mechanismy a funkce ochrany dat. Ujistěte se, že jste pravidelně přeměňováni přístupové klíče k prostředkům konfigurace vaší aplikace. Přihlašovací údaje z připojovacích řetězců můžou být uložené v Azure Key Vault a váš kód se musí ověřit, aby se Key Vault načetl. Přístupové klíče můžou aplikaci udělit přístup pro čtení i zápis nebo jen pro čtení. Zajistěte, aby byl vydán správný typ přístupového klíče, aby se zabránilo neoprávněnému přístupu. Abyste byli lépe zabezpečeni, použijte funkci spravované identity ve službě Azure AD. To vyžaduje, aby aplikace měly adresu URL koncového bodu konfigurace pro přístup k hodnotám konfigurace.

Omezení přístupu pomocí řízení přístupu na základě role Azure (Azure RBAC):

- Přidělte citlivá data do svého vlastního prostředku konfigurace aplikace a pak podle potřeby přidělte zásady RBAC, aby byl povolen pouze autorizovaný přístup. 

- Použití řízení přístupu na základě sítě

- Konkrétní ovládací prvky ve službách Azure (například šifrování v SQL a dalších databázích) a zajišťují konzistentní řízení přístupu. všechny typy řízení přístupu by se měly zarovnávat do vaší strategie segmentace vaší organizace.

- Podniková strategie segmentace by měla vycházet také z umístění citlivých nebo důležitých obchodních dat a systémů.

Další informace najdete v následujících referenčních materiálech:

- [Autorizace přístupu ke konfiguraci aplikace Azure pomocí Azure Active Directory](concept-enable-rbac.md)

- [Šifrování dat konfigurace aplikace](faq.yml#does-app-configuration-encrypt-my-data)

- [Řízení přístupu na základě role (RBAC) Azure](../role-based-access-control/overview.md) 

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Sdílené

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Šifrování citlivých informací při přenosu

**Doprovodné** materiály: pro doplnění řízení přístupu by se data v přenosu měla chránit před útoky typu "vzdálené správy" pomocí šifrování. To pomáhá zajistit, že útočníci nemohou snadno číst nebo upravovat data.

Konfigurace aplikace Azure používá šifrování TLS pro všechny požadavky HTTP. Infrastruktura Azure nabízí přidanou vrstvu šifrování na úrovni sítě pro všechny požadavky mezi datovými centry Azure. Ujistěte se, že přenosy HTTP, které se připojují k prostředkům konfigurace vaší aplikace, můžou vyjednávat TLS verze 1.2 nebo vyšší.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Šifrování citlivých neaktivních uložených dat

**Doprovodné** materiály: pro doplnění řízení přístupu by měla být neaktivní data chráněná před útoky typu "vzdálené správy" (například přístup k podkladovým úložištím) pomocí šifrování. To pomáhá zajistit, že útočníci nemohou snadno číst nebo upravovat data.

Azure ve výchozím nastavení poskytuje šifrování dat v klidovém stavu. U vysoce citlivých dat máte možnost implementovat další šifrování v klidovém umístění všech prostředků Azure, kde jsou k dispozici. Azure spravuje vaše šifrovací klíče ve výchozím nastavení, ale Azure poskytuje možnost spravovat vlastní klíče (klíče spravované zákazníkem) pro konfiguraci aplikací Azure.

- [Použití spravovaných zákaznických klíčů k šifrování vašich dat v konfiguraci aplikací Azure](concept-customer-managed-keys.md)

- [Principy šifrování neaktivních uložených dat v Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Model šifrování a tabulka správy klíčů](../security/fundamentals/encryption-atrest.md#azure-resource-providers-encryption-model-support)

- [Data s dvojitým šifrováním v Azure](../security/fundamentals/double-encryption.md#data-at-rest)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="asset-management"></a>Správa aktiv

*Další informace najdete v tématu [Azure Security Benchmark: správa prostředků](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Zajištění přehledu bezpečnostního týmu o rizicích pro prostředky

**Pokyny:** Zajistěte udělení oprávnění Čtenář zabezpečení ve vašem tenantovi Azure a vašich předplatných bezpečnostním týmům, aby mohly monitorovat bezpečnostní rizika s využitím služby Azure Security Center. 

V závislosti na nastavení struktury odpovědnosti bezpečnostních týmů může být monitorování bezpečnostních rizik odpovědností centrálního nebo místního bezpečnostního týmu. Přehledy zabezpečení a bezpečnostní rizika se však v rámci organizace musí vždy agregovat centrálně. 

Oprávnění Čtenář zabezpečení je možné přidělit pro celého tenanta (kořenová skupina pro správu) nebo je vymezit na konkrétní skupiny pro správu nebo konkrétní předplatná. 

Poznámka: K získání přehledu o úlohách a službách se můžou vyžadovat další oprávnění. 

- [Přehled role Čtenář zabezpečení](../role-based-access-control/built-in-roles.md#security-reader)

- [Přehled skupin pro správu Azure](../governance/management-groups/overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Zajištění přístupu k inventáři a metadatům prostředků pro bezpečnostní tým

**Doprovodné** materiály: Ujistěte se, že týmy zabezpečení mají přístup k nepřetržitě aktualizovanému inventáři prostředků v Azure, jako je konfigurace aplikací Azure. Bezpečnostní týmy tento inventář často potřebují k tomu, aby vyhodnotily potenciální slabá místa organizace vůči vznikajícím rizikům, a jako vstup pro průběžná vylepšování zabezpečení. Vytvořte skupinu Azure Active Directory, která bude obsahovat autorizovaného týmu zabezpečení vaší organizace, a přiřaďte jim přístup pro čtení ke všem prostředkům konfigurace aplikace Azure. to může být zjednodušené v rámci vašeho předplatného o jedno přiřazení role na nejvyšší úrovni.

Funkce inventáře Azure Security Center a Azure Resource Graph se můžou dotazovat na všechny prostředky v předplatných, včetně služeb Azure, aplikací a síťových prostředků.

Použijte značky pro prostředky Azure, skupiny prostředků a předplatná k jejich logickému uspořádání do taxonomie. Každá značka se skládá z názvu a páru hodnot. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí.

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../governance/resource-graph/first-query-portal.md)

- [Správa inventáře Azure Security Center prostředků](../security-center/asset-inventory.md)

- [Další informace o označování prostředků najdete v průvodci pro pojmenování a označení prostředku.](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Používání jenom schválených služeb Azure

**Doprovodné** materiály: Konfigurace aplikace Azure podporuje nasazení na základě Azure Resource Manager a vynucení konfigurace pomocí Azure Policy. Pomocí Azure Policy můžete auditovat a omezovat služby, které můžou uživatelé zřídit ve vašem prostředí. Pomocí Azure Resource Graphu se můžete dotazovat na prostředky v rámci jejich předplatných a zjišťovat je. Pomocí Azure Monitoru můžete také vytvořit pravidla pro aktivaci upozornění při zjištění neschválené služby.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Zajištění zabezpečení správy životního cyklu prostředků

**Pokyny:** Vytvořte nebo aktualizujte zásady zabezpečení, které v rámci procesů správy životního cyklu prostředků řeší úpravy s potenciálně velkým dopadem. Tyto úpravy obsahují změny, ale nejsou omezeny na: zprostředkovatelé identity a přístup, citlivost dat, konfigurace sítě a přiřazení oprávnění správce.

Pokud už je nepotřebujete, odeberte prostředky Azure. Zajistěte, aby správci pravidelně přetočili přístupové klíče, aby měli přístup k prostředkům konfigurace jenom ověření uživatelé.

- [Otočit šifrovací klíče používané pro konfiguraci aplikace](concept-customer-managed-keys.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>DOP. 5: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů pracovat se správcem prostředků Azure tak, že nakonfigurujete "blokování přístupu" pro aplikaci "Microsoft Azure Management".

- [Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manageru](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="logging-and-threat-detection"></a>Protokolování a detekce hrozeb

*Další informace najdete v tématu [Azure Security Benchmark: protokolování a detekce hrozeb](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Povolení detekce hrozeb pro správu identit a přístupu v Azure

**Doprovodné** materiály: Konfigurace aplikace se integruje s Azure Active Directory (Azure AD). Tato funkce poskytuje následující protokoly uživatelů, které je možné zobrazit v sestavách Azure AD nebo je integrovat s Azure Monitor, Azure Sentinel nebo jiné nástroje SIEM/Monitoring pro výkonnější monitorování a analytické případy:
- Přihlášení – Sestava přihlášení poskytuje informace o využití spravovaných aplikací a aktivitách přihlašování uživatelů.
- Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny jakýchkoli prostředků v rámci Azure AD, jako jsou přidání nebo odebrání uživatelů, aplikací, skupin, rolí nebo zásad.
- Riziková přihlášení – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.
- Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Azure Security Center může také upozorňovat na určité podezřelé aktivity, jako jsou nadměrný počet neúspěšných pokusů o ověření nebo zastaralé účty v předplatném. Kromě základního monitorování hygieny zabezpečení může modul ochrany před internetovými útoky Azure Security Center taky shromažďovat podrobnější výstrahy zabezpečení pro vrstvy služeb Azure. Tato funkce poskytuje přehled o anomáliích účtů v jednotlivých prostředcích.

Další metodou získání přístupu k prostředku konfigurace vaší aplikace je použití přístupových klíčů. Je potřeba je pravidelně střídat, aby neautorizovaní agenti získali přístup k vašemu konfiguračnímu prostředku. Otočení je možné provést přímo na portálu v části přístupové klíče.

- [Povolení konfigurace aplikace Azure přístup k dalším prostředkům chráněným službou Azure AD pomocí spravované identity](overview-managed-identity.md)

- [Použití spravovaných identit s konfigurací aplikace Azure](howto-integrate-azure-managed-service-identity.md)

- [Sestavy aktivit auditu v Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Povolení ochrany identit Azure](../active-directory/identity-protection/overview-identity-protection.md)

- [Autorizace přístupu ke konfiguraci aplikace Azure pomocí Azure AD](concept-enable-rbac.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Povolení protokolování pro síťové aktivity Azure

**Pokyny**: Konfigurace aplikace Azure neimplementuje žádné prostředky přímo do virtuální sítě. Konfigurace aplikace ale umožňuje používat privátní koncové body pro zabezpečené připojení k Azure App Configuration z virtuální sítě. Konfigurace aplikací Azure taky nevytváří ani nezpracovává protokoly dotazů DNS, které by se musely povolit.

Povolte protokolování v nakonfigurovaných privátních koncových bodech konfigurace aplikace pro zachycení:
- Data zpracovaná privátním koncovým bodem (v/v)
- Data zpracovaná službou privátního propojení (v/v)
- Dostupnost portu NAT

Další informace najdete v následujících referenčních materiálech:

- [Monitorování privátních odkazů Azure](../private-link/private-link-overview.md#logging-and-monitoring)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Povolení protokolování pro prostředky Azure

**Doprovodné** materiály: protokoly aktivit, které jsou automaticky k dispozici, obsahují všechny operace zápisu (Put, post, DELETE) pro prostředky konfigurace aplikace s výjimkou operací čtení (Get). Protokoly aktivit se dají použít k nalezení chyby při odstraňování potíží nebo sledování, jak uživatel ve vaší organizaci změnil prostředek. V případě konfigurace aplikace jsou protokoly aktivit k dispozici pouze na řídicí rovině a jsou umístěny z Azure Resource Manager (ARM). Protokolování roviny zákaznických dat pro konfiguraci aplikace se v tuto chvíli nepodporuje. Protokoly prostředků Azure také nejsou k dispozici pro konfiguraci.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Principy protokolování a různých typů protokolů v Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizace správy a analýz protokolu zabezpečení

**Doprovodné** materiály: centralizace úložiště protokolování a analýzy pro povolení korelace. U každého zdroje protokolu se ujistěte, že jste přiřadili vlastníka dat, pokyny pro přístup, umístění úložiště, jaké nástroje se používají ke zpracování a přístupu k datům a k požadavkům na uchovávání dat.

Ujistěte se, že Integrujte protokoly aktivit Azure do svého centrálního protokolování. Ingestování protokolů prostřednictvím Azure Monitor k agregaci dat zabezpečení generovaných zařízeními koncového bodu, síťovými prostředky a dalšími systémy zabezpečení. V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

Kromě toho povolte a zaveďte data do Azure Sentinel nebo SIEM třetí strany. Řada organizací zvolí použití Azure Sentinel pro "horká" data, která se používají často a Azure Storage pro "studená" data, která se používají méně často.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurace uchovávání úložiště protokolů

**Pokyny**: Ujistěte se, že všechny účty úložiště nebo pracovní prostory Log Analytics používané pro ukládání protokolů konfigurace aplikací mají dobu uchování protokolu nastavenou v souladu s předpisy pro dodržování předpisů vaší organizace. Používejte Azure Storage, Data Lake nebo Log Analytics účty pracovního prostoru pro dlouhodobé a archivační úložiště.

V Azure Monitor můžete nastavit dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace.

- [Jak nakonfigurovat dobu uchování Log Analytics pracovního prostoru](../azure-monitor/logs/manage-cost-storage.md)

- [Ukládání protokolů prostředků v Azure Storagem účtu](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Příprava – aktualizace procesu reakce na incidenty pro Azure

**Pokyny:** Zajistěte, aby vaše organizace měla procesy, jak reagovat na incidenty zabezpečení, aby tyto procesy aktualizovala pro Azure a pravidelně je testovala za účelem zajištění připravenosti.

- [Implementace zabezpečení v podnikovém prostředí](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referenční příručka k reakcím na incidenty](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Příprava – nastavení oznámení o incidentu

**Pokyny:** Nastavte kontaktní informace pro incidenty zabezpečení ve službě Azure Security Center. Tyto kontaktní informace používá společnost Microsoft k tomu, aby vás kontaktovala v případě, že služba MSRC (Microsoft Security Response Center) zjistí, že s vašimi daty někdo nezákonně nebo neoprávněně pracoval. Máte také možnost přizpůsobit upozornění na incidenty a oznámení o incidentech v různých službách Azure v závislosti na tom, jak potřebujete na incidenty reagovat. 

- [Nastavení kontaktu ve službě Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Detekce a analýza – vytvoření incidentů na základě upozornění s vysokou kvalitou

**Pokyny:** Zajistěte, abyste měli proces pro vytváření vysoce kvalitních upozornění a měření jejich kvality. To vám umožní poučit se z minulých incidentů a stanovit priority upozornění pro analytiky, aby nemuseli ztrácet čas s falešně pozitivními výsledky. 

Vysoce kvalitní upozornění se dají vytvářet na základě zkušeností z minulých incidentů, ověřených zdrojů komunity a nástrojů určených k vygenerování a vyčištění upozornění spojením a sladěním různých zdrojů signálu. 

Azure Security Center poskytuje vysoce kvalitní výstrahy napříč mnoha prostředky Azure. Pomocí datového konektoru ASC můžete upozornění streamovat do služby Azure Sentinel. Azure Sentinel umožňuje vytvářet rozšířená pravidla upozornění, která automaticky generují incidenty pro účely šetření. 

Pomocí funkce exportu můžete upozornění a doporučení služby Azure Security Center exportovat, abyste mohli lépe identifikovat rizika pro prostředky Azure. Upozornění a doporučení můžete exportovat ručně nebo průběžně a nepřetržitě.

- [Konfigurace exportu](../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Detekce a analýza – Vyšetřování incidentu

**Pokyny:** Zajistěte, aby analytici mohli při vyšetřování potenciálních incidentů dotazovat a používat různorodé zdroje dat a mohli tak získat ucelenou představu o tom, co se stalo. Aby se zabránilo slepým místům, měly by se shromažďovat různorodé protokoly, které umožní sledovat aktivity potenciálního útočníka v celém průběhu útoku.  Měli byste také zajistit zaznamenávání přehledů a poznatků pro další analytiky a budoucí použití jako historické referenční informace.  

Mezi zdroje dat pro vyšetřování patří centrální zdroje protokolování, ve kterých se již shromažďují protokoly ze služeb v příslušném oboru a spuštěných systémů, ale můžou mezi ně patřit také:

- Síťová data – S využitím protokolů toků skupin zabezpečení sítě a služeb Azure Network Watcher a Azure Monitor můžete zachytávat protokoly toků sítě a další analytické informace. 

- Snímky spuštěných systémů: 

    - S využitím funkce vytváření snímků virtuálního počítače Azure můžete vytvořit snímek disku spuštěného systému. 

    - S využitím nativní funkce výpisu paměti operačního systému můžete vytvořit snímek paměti spuštěného systému.

    - S využitím funkce vytváření snímků služeb Azure nebo vlastních funkcí vašeho softwaru můžete vytvářet snímky spuštěných systémů.

Azure Sentinel nabízí rozsáhlé analýzy dat z prakticky jakéhokoli zdroje protokolů a portál pro správu případů, na kterém můžete spravovat celý životní cyklus incidentů. Analytické informace zjištěné během vyšetřování je možné přidružovat k incidentům pro účely sledování a generování sestav. 

- [Snímek disku počítače s Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Snímek disku počítače s Linuxem](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Shromažďování výpisu paměti a diagnostických informací ze strany podpory Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Vyšetřování incidentů s využitím služby Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Detekce a analýza – stanovení priorit incidentů

**Pokyny:** Poskytněte analytikům kontext, aby věděli, na jaké incidenty se zaměřit jako první v závislosti na závažnosti upozornění a citlivosti prostředků. 

Azure Security Center jednotlivým upozorněním přiřazuje závažnost, která vám pomůže určit, jaká upozornění by se měla vyšetřit jako první. Závažnost je založena na tom, jak se Security Center ve vyhledávání, nebo na analýze, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Kromě toho můžete prostředky označit pomocí značek a vytvořit systém vytváření názvů, který vám pomůže identifikovat a kategorizovat prostředky Azure, zejména prostředky zpracovávající citlivá data.  Je vaší zodpovědností určit prioritu nápravy upozornění v závislosti na důležitosti prostředků Azure a prostředí, ve kterém k incidentu došlo.

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Používání značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Zadržení, zničení a obnovení – automatizace zpracování incidentů

**Pokyny:** Automatizujte ruční opakující se úlohy, ay se zrychlila doba reakce a snížilo se zatížení analytiků. Ruční provádění úloh trvá déle, což zpomaluje jednotlivé incidenty a snižuje počet incidentů, které může analytik zvládnout. Ruční úlohy také analytiky více unavují, což zvyšuje riziko zpoždění v důsledku lidských chyb a snižuje schopnost analytiků efektivně se soustředit na složité úlohy. S využitím funkcí automatizace pracovních postupů ve službách Azure Security Center a Azure Sentinel můžete v reakci na příchozí upozornění zabezpečení automaticky aktivovat akce nebo spouštět playbooky. Playbook provádí akce, jako jsou odesílání oznámení, zakazování účtů nebo izolace problematických sítí. 

- [Konfigurace automatizace pracovních postupů ve službě Security Center](../security-center/workflow-automation.md)

- [Nastavení automatizovaných reakcí na hrozby ve službě Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Nastavení automatizovaných reakcí na hrozby ve službě Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="posture-and-vulnerability-management"></a>Stav a správa ohrožení zabezpečení

*Další informace najdete v tématu [Azure Security Benchmark: stav a správa ohrožení zabezpečení](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Zřízení zabezpečených konfigurací pro služby Azure 

**Pokyny**: Konfigurace aplikace Azure podporuje níže uvedené zásady pro konkrétní služby, které jsou k dispozici v Azure Security Center k auditování a prosazování konfigurací vašich prostředků Azure. Tato možnost se dá nakonfigurovat v Azure Security Center nebo Azure Policy iniciativ.
- Konfigurace aplikace by měla používat klíč spravovaný zákazníkem: klíče spravované zákazníkem poskytují rozšířenou ochranu dat tím, že vám umožní spravovat šifrovací klíče. To se často vyžaduje pro splnění požadavků na dodržování předpisů.
- Konfigurace aplikace by měla používat privátní odkaz: připojení privátního koncového bodu umožňuje klientům ve virtuální síti zabezpečený přístup k konfiguraci aplikací Azure přes privátní odkaz.

Plány Azure můžete použít k automatizaci nasazení a konfigurace služeb a prostředí aplikací včetně šablon Azure Resource Manageru, řízení a zásad Azure RBAC v jediné definici podrobného plánu.

- [Další informace o zásadách konfigurace aplikací](../governance/policy/samples/built-in-policies.md#app-configuration)

- [Práce se zásadami zabezpečení v Azure Security Center](../security-center/tutorial-security-policy.md)

- [Ilustrace implementace Guardrails v zóně pro vykládku na podnikové úrovni](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint](../governance/blueprints/overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Udržování zabezpečených konfigurací pro služby Azure

**Doprovodné** materiály: pomocí Azure Security Center můžete monitorovat standardní hodnoty konfigurace a vymáhat je pomocí Azure Policy. Azure Policy pro konfiguraci aplikace zahrnuje: 
- Konfigurace aplikace by měla používat klíč spravovaný zákazníkem: klíče spravované zákazníkem poskytují rozšířenou ochranu dat tím, že vám umožní spravovat šifrovací klíče. To se často vyžaduje pro splnění požadavků na dodržování předpisů.
- Konfigurace aplikace by měla používat privátní odkaz: připojení privátního koncového bodu umožňuje klientům ve virtuální síti zabezpečený přístup k konfiguraci aplikací Azure přes privátní odkaz.

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md) 

- [Vytváření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Provádění pravidelné simulace útoku

**Pokyny:** Podle potřeby provádějte testování průniku nebo aktivity červeného týmu na svých prostředcích Azure a zajistěte nápravu všech kritických zjištění v oblasti zabezpečení.
Postupujte podle pravidel zapojení testování průniku cloudových služeb Microsoftu (Microsoft Cloud Penetration Testing), abyste měli jistotu, že testy průniku neporušují zásady Microsoftu. Využijte strategii Microsoftu a spuštění červeného týmu a testování průniku na živém webu na cloudové infrastruktuře, službách a aplikacích spravovaný Microsoftem.

- [Testování průniku v Azure](../security/fundamentals/pen-testing.md)

- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="backup-and-recovery"></a>Zálohování a obnovy

*Další informace najdete v tématu [Azure Security Benchmark: zálohování a obnovení](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Zmírnění rizika ztracených klíčů

**Doprovodné** materiály: Ujistěte se, že máte zavedené míry, abyste zabránili ztrátě klíčů a obnovili je. Povolte v Azure Key Vaultu obnovitelné odstranění a ochranu před vymazáním, aby byly klíče chráněné proti náhodnému nebo zlomyslnému odstranění.

- [Jak povolit obnovitelné odstranění a ochranu před vymazáním v Key Vaultu](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="governance-and-strategy"></a>Zásady správného řízení a strategie

*Další informace najdete v tématu [Azure Security Benchmark: zásady správného řízení a strategie](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definování strategie správy prostředků a ochrany dat 

**Pokyny:** Zadokumentujte si a dále sdělujte jasnou strategii pro nepřetržité monitorování a ochranu systémů a dat. Stanovte priority zjišťování, hodnocení, ochrany a monitorování nejdůležitějších podnikových dat a systémů. 

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující prvky: 

-   Standard klasifikace dat v souladu s obchodními riziky

-   Přehled organizace zabezpečení o rizicích a inventáři prostředků 

-   Schválení služeb Azure k používání provedené organizací zabezpečení 

-   Zabezpečení prostředků po dobu jejich životního cyklu

-   Požadovaná strategie řízení přístupu v souladu s klasifikací dat organizace

-   Použití funkcí pro ochranu dat nativních pro Azure i třetích stran

-   Požadavky na šifrování dat pro případy použití v přenosech a u neaktivních uložených dat

-   Vhodné kryptografické standardy

Další informace najdete v následujících referenčních materiálech:
- [Doporučení architektury zabezpečení Azure – úložiště, data a šifrování](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Základy zabezpečení Azure – zabezpečení, šifrování a úložiště dat Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – osvědčené postupy zabezpečení a šifrování dat Azure](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark – správa prostředků](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure Security Benchmark – ochrana dat](../security/benchmarks/security-controls-v2-data-protection.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definování strategie segmentace podniku 

**Pokyny:** Zřiďte celopodnikovou strategii za účelem segmentace přístupu k prostředkům pomocí kombinace identity, sítě, aplikace, předplatného, skupiny pro správu a dalších řídicích prvků.

Pečlivě vyvažte nutnost oddělení zabezpečení s nutností povolit každodenní provoz systémů, které musí vzájemně komunikovat a pracovat s daty.

Zajistěte, aby se strategie segmentace implementovala konzistentně ve všech typech řídicích prvků včetně zabezpečení sítě, modelů identit a přístupu a modelů oprávnění/přístupu aplikací a řídicích prvků pro lidské procesy.

- [Pokyny ke strategii segmentace v Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Pokyny ke strategii segmentace v Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Soulad segmentace sítě a strategie segmentace podniku](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definování strategie správy stavu zabezpečení

**Pokyny:** Nepřetržitě měřte a zmírňujte rizika pro jednotlivé prostředky a prostředí, ve kterém jsou hostovány. Stanovte prioritu prostředků s vysokou hodnotou a míst nejvíce vystavených útokům, jako jsou publikované aplikace, body sítě pro příchozí a odchozí přenosy dat, koncové body uživatelů a správců atd.

- [Azure Security Benchmark – správa stavu a ohrožení zabezpečení](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Soulad rolí a odpovědností organizace

**Pokyny:** Je nutné zadokumentovat a dále sdělovat jasnou strategii pro role a odpovědnosti ve vaší organizaci zabezpečení. Stanovte prioritu poskytování jasné odpovědnosti při rozhodování o zabezpečení, zároveň vyškolte všechny uživatele na model sdílené odpovědnosti a vyškolte technické týmy na technologii pro zabezpečení cloudu.

- [Osvědčený postup zabezpečení Azure 1 – Lidé: školení týmů v oblasti zabezpečení cloudu](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Osvědčený postup zabezpečení Azure 2 – Lidé: školení týmů na technologii zabezpečení cloudu](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Osvědčený postup zabezpečení Azure 3 – Proces: přiřazení odpovědnosti za rozhodování o zabezpečení cloudu](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definování strategie zabezpečení sítě

**Pokyny:** Zřiďte postup pro zabezpečení sítě Azure jako součást celkové strategie řízení přístupu zabezpečení vaší organizace.  

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující prvky: 

-   Centralizovaná správa sítě a odpovědnost za zabezpečení

-   Model segmentace virtuální sítě v souladu se strategií segmentace podniku

-   Strategie náprav v různých situacích ohrožení a útoků

-   Strategie pro přechodový bod na internet a příchozí a odchozí přenosy

-   Strategie vzájemného propojení hybridního cloudu a místního připojení

-   Aktuální artefakty zabezpečení sítě (například síťové diagramy, referenční síťová architektura)

Další informace najdete v následujících referenčních materiálech:
- [Osvědčený postup zabezpečení Azure 11 – Architektura: jednotná strategie zabezpečení](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark – zabezpečení sítě](../security/benchmarks/security-controls-v2-network-security.md)

- [Přehled zabezpečení sítě v Azure](../security/fundamentals/network-overview.md)

- [Strategie architektury podnikové sítě](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definování strategie identity a privilegovaného přístupu

**Pokyny:** Zřiďte postupy pro identitu a privilegovaný přístup Azure jako součást celkové strategie řízení přístupu zabezpečení vaší organizace.  

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující prvky: 

-   Centralizovaný systém identit a ověřování a jeho vzájemné propojení s dalšími interními a externími systémy identit

-   Metody silného ověřování v různých případech a podmínkách použití

-   Ochrana vysoce privilegovaných uživatelů

-   Monitorování a zpracování anomálních aktivit uživatelů  

-   Uživatelská identita a kontrola přístupu a proces odsouhlasení

Další informace najdete v následujících referenčních materiálech:

- [Azure Security Benchmark – správa identit](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Security Benchmark – privilegovaný přístup](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Osvědčený postup zabezpečení Azure 11 – Architektura: jednotná strategie zabezpečení](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Přehled zabezpečení správy identit v Azure](../security/fundamentals/identity-management-overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definování strategie protokolování a reakcí na hrozby

**Pokyny:** Nastavte strategii protokolování a reakce na hrozby, abyste mohli rychle detekovat a zmírňovat hrozby při současném splnění požadavků na dodržování předpisů. Jako prioritu si stanovte poskytování vysoce kvalitních upozornění a bezproblémových prostředí analytikům, aby se mohli soustředit na hrozby, a ne na integraci a ruční kroky. 

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující aspekty: 

-   Role a odpovědnosti organizace v oblasti operací zabezpečení (SecOps) 

-   Dobře definovaný proces reakce na incidenty, který je v souladu s NIST nebo jinou oborovou architekturou 

-   Shromažďování a uchovávání protokolů pro podporu detekce hrozeb, reakce na incidenty a požadavků na dodržování předpisů

-   Centralizované zobrazení a korelační informace o hrozbách, používání systému SIEM (správa akcí a informací o zabezpečení), nativních možností Azure a dalších zdrojů 

-   Plán komunikace a oznámení pro vaše zákazníky, dodavatele a veřejné partnery

-   Použití nativních platforem Azure a platforem třetích stran pro zpracování incidentů, jako je například protokolování a detekce hrozeb, forenzní zkoumání a náprava po útocích či jejich zneškodnění

-   Procesy pro zpracování incidentů a aktivity po incidentech, jako je poučení a uchovávání důkazů

Další informace najdete v následujících referenčních materiálech:

- [Azure Security Benchmark – protokolování a detekce hrozeb](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark – reakce na incidenty](../security/benchmarks/security-controls-v2-incident-response.md)

- [Osvědčený postup zabezpečení Azure 4 – Proces: aktualizace procesů reakcí na incidenty pro cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Architektura přechodu na Azure a průvodce rozhodováním o protokolování a vytváření sestav](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Škálování, správa a monitorování Azure na podnikové úrovni](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](../security/benchmarks/overview.md).
- Další informace o [základních úrovních zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)