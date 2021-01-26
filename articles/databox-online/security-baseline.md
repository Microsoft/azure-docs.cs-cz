---
title: Základní hodnoty zabezpečení Azure pro Azure Stack Edge
description: Základní hodnoty zabezpečení Azure Stack Edge poskytují pokyny a materiály k postupům pro implementaci doporučení zabezpečení, která jsou určená ve srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: databox
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1d7cd07ee8ae8b35d51fbdd25b34602c2e799c75
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787260"
---
# <a name="azure-security-baseline-for-azure-stack-edge"></a>Základní hodnoty zabezpečení Azure pro Azure Stack Edge

Tato základní hodnota zabezpečení platí pro Microsoft Azure Stack Edge pokyny od [verze Azure Security test 2,0](../security/benchmarks/overview.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure. Obsah je seskupen podle **ovládacích prvků zabezpečení** definovaných srovnávacím testem zabezpečení Azure a souvisejících pokynů vztahujících se na Azure Stack Edge. **Ovládací prvky** , které se nevztahují na Azure Stack Edge, jsou vyloučené.

Pokud chcete zjistit, jak Azure Stack Edge kompletně mapuje na srovnávací test zabezpečení Azure, přečtěte si [kompletní soubor mapování standardních hodnot zabezpečení Azure Stack Edge](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementace zabezpečení pro interní provoz

**Doprovodné** materiály: zákazníci nasadí fyzické a Azure Stack hraniční zařízení společnosti Microsoft do své privátní sítě pro zajištění interního přístupu a jejich další možnosti zabezpečení. Například zařízení Azure Stack Edge je přístupné prostřednictvím interní sítě zákazníka a vyžaduje IP adresu konfigurovanou zákazníkem. Pro přístup k uživatelskému rozhraní zařízení je navíc vybraný přístup k přístupovému heslu. 

Interní provoz se dále zabezpečuje pomocí:

- Pro Azure Portal a správu sady SDK Azure Stack hraničního zařízení se vyžaduje protokol TLS (Transport Layer Security) verze 1,2.

- Libovolný klientský přístup k zařízení je prostřednictvím místního webového uživatelského rozhraní, které používá standard TLS 1,2 jako výchozí zabezpečený protokol.

- Připojit se ke službě Azure Stack Edge je povolený jenom autorizovaným Azure Stack hraničnímu zařízení, které si zákazník ve svém předplatném Azure vytvoří.

Další informace jsou k dispozici na odkazovaných odkazech.
 
- [Konfigurace TLS 1,2 na klientech Windows, kteří přistupují k zařízení GPU Azure Stack Edge pro](azure-stack-edge-j-series-configure-tls-settings.md)

- [Rychlý Start – začněte s Azure Stack Edge pro s grafickým procesorem](azure-stack-edge-gpu-quickstart.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="ns-2-connect-private-networks-together"></a>NS-2: spojování privátních sítí

**Pokyny**: zákazníci si můžou vybrat virtuální privátní síť (VPN) typu Point-to-site, která připojí Azure Stack hraniční zařízení od místní privátní sítě k síti Azure. SÍŤ VPN poskytuje druhou vrstvu šifrování pro přenos dat při pohybu přes transportní vrstvu ze zařízení zákazníka do Azure. 

Zákazníci můžou nakonfigurovat virtuální privátní síť na svém zařízení Azure Stack Edge přes Azure Portal nebo prostřednictvím Azure PowerShell.

- [Konfigurace Azure VPN prostřednictvím Azure PowerShell skriptu pro Azure Stack Edge pro R a Azure Stack Edge Mini R](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [Konfigurace TLS 1,2 na klientech Windows, kteří přistupují k zařízení GPU Azure Stack Edge pro](azure-stack-edge-j-series-configure-tls-settings.md)

- [Kurz: Konfigurace certifikátů pro Azure Stack Edge pro R](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Zřízení přístupu privátní sítě ke službám Azure

**Pokyny**: zákazníci si můžou vybrat virtuální privátní síť (VPN) typu Point-to-site, která připojí Azure Stack hraniční zařízení od místní privátní sítě k síti Azure. SÍŤ VPN poskytuje druhou vrstvu šifrování pro přenos dat při pohybu přes transportní vrstvu ze zařízení zákazníka do Azure. 

- [Konfigurace Azure VPN prostřednictvím Azure PowerShell skriptu pro Azure Stack Edge pro R a Azure Stack Edge Mini R](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [Konfigurace TLS 1,2 na klientech Windows, kteří přistupují k zařízení GPU Azure Stack Edge pro](azure-stack-edge-j-series-configure-tls-settings.md)

- [Kurz: Konfigurace certifikátů pro Azure Stack Edge pro R](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Ochrana aplikací a služeb před útoky z externích sítí

**Doprovodné** materiály: zařízení Azure Stack Edge zahrnuje standardní funkce ochrany sítě Windows serveru, které se nedají konfigurovat pro zákazníky.

Zákazníci si můžou pomocí síťového virtuálního zařízení zabezpečit svoji privátní síť připojenou k Azure Stack hraničnímu zařízení z externích útoků, jako je třeba brána firewall s pokročilou distribucí distribuovaných útoků (DDoS).

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: nasazení systémů pro zabránění vniknutí/prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: koncové body, které používá Azure Stack hraniční zařízení, jsou spravovány společností Microsoft. Zákazníci zodpovídají za jakékoli další ovládací prvky, které chtějí nasadit do místních systémů.

Zařízení Azure Stack Edge používá k ochraně služby své vlastní funkce pro detekci vniknutí. 

- [Principy zabezpečení Azure Stack Edge](azure-stack-edge-security.md)

- [Informace o portu pro Azure Stack Edge](azure-stack-edge-gpu-system-requirements.md)

- [Získání protokolů zjišťování vniknutí hardwaru a softwaru](azure-stack-edge-gpu-troubleshoot.md#gather-advanced-security-logs)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="identity-management"></a>Správa identit

*Další informace najdete v tématu [Azure Security Benchmark: správa identit](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Zabezpečená a automatická správa identit aplikací

**Pokyny**: všechna Azure Stack hraniční zařízení automaticky mají spravovanou identitu přiřazenou systémem v Azure Active Directory (Azure AD). V současné době se spravovaná identita používá pro správu cloudu virtuálních počítačů hostovaných na Azure Stack hraničních zařízeních.

Azure Stack hraniční zařízení se spouští do uzamčeného stavu pro místní přístup. Pro účet správce místního zařízení se budete muset připojit k zařízení prostřednictvím místního webového uživatelského rozhraní nebo rozhraní PowerShellu a nastavit silné heslo. Uložte a spravujte přihlašovací údaje Správce zařízení v zabezpečeném umístění, jako je například Azure Key Vault a otáčejte heslo správce podle standardů vaší organizace.

- [Chránit Azure Stack hraniční zařízení prostřednictvím hesla](azure-stack-edge-security.md#protect-the-device-via-password)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Použití jednotného přihlašování (SSO) Azure AD pro přístup k aplikacím

**Doprovodné** materiály: jednotné přihlašování není pro zařízení s koncovým bodem Azure Stack Edge podporováno. Můžete ale povolit jednotné přihlašování založené na standardu Azure Active Directory (Azure AD) pro zabezpečení přístupu ke cloudovým prostředkům Azure.

- [Principy jednotného přihlašování k aplikacím pomocí Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Použití řídicích prvků silného ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: vícefaktorové ověřování je ovládací prvek silného ověřování, ale funkce pro uživatele služby Azure Stack Edge. Dá se využít pro podporované scénáře, jako je například Správa Edge Azure Stack hraničních zařízení v Azure Portal. Všimněte si, že místní přístup k Azure Stack hraničních zařízení nepodporuje vícefaktorové ověřování.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorování a upozornění na anomálie účtů

**Pokyny**: povolení Azure monitor pro shromáždění protokolů zařízení nebo kontejnerů pro službu Azure Stack Edge. Monitorujte kontejnery, jako jsou třeba ty, které spouštějí více výpočetních aplikací v clusteru Kubernetes na vašem zařízení.

Kromě toho balíček podpory, který obsahuje protokoly auditu, se může shromažďovat na místním webovém uživatelském rozhraní Azure Stack hraničního zařízení. Všimněte si, že balíček pro podporu není k dispozici na Azure Portal.
 
- [Povolení Azure Monitor na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-enable-azure-monitor.md) 

- [Shromáždění balíčku pro podporu](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Omezení přístupu k prostředkům Azure na základě podmínek

**Pokyny**: Azure Active Directory (Azure AD) podmíněný přístup je funkce výslovného souhlasu pro ověřování do služby Azure Stack Edge. Služba Azure Stack Edge je prostředek v Azure Portal, který umožňuje spravovat Azure Stack hraniční zařízení pro zařízení v různých geografických umístěních. 

- [Co je podmíněný přístup](../active-directory/conditional-access/overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminace nezamýšleného prozrazení přihlašovacích údajů

**Doprovodné** materiály: postupujte podle osvědčených postupů pro ochranu přihlašovacích údajů, jako jsou:

- Aktivační klíč, pomocí kterého se zařízení aktivuje s Azure Stackm hraničním prostředkem v Azure.
- Přihlašovací údaje pro přístup k Azure Stack hraničnímu zařízení.
- Klíčové soubory, které mohou usnadnit obnovení Azure Stack hraničních zařízení.
- Šifrovací klíč kanálu

Pravidelně otáčejte a synchronizujte klíče účtu úložiště, abyste chránili svůj účet úložiště před neoprávněnými uživateli.

- [Zabezpečení a ochrana dat v Azure Stack Edge pro](azure-stack-edge-security.md)

- [Synchronizace klíčů úložiště](azure-stack-edge-manage-shares.md#sync-storage-keys)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="privileged-access"></a>Privilegovaný přístup

*Další informace najdete v tématu [Azure Security Benchmark: privilegovaný přístup](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Omezení přístupu pro správu k nejdůležitějším podnikovým systémům

**Doprovodné** materiály: řešení Azure Stack Edge má několik komponent s bezpečným přístupem – ovládací prvky pro omezení přístupu k důležitým podnikovým zařízením. Aby vaše organizace mohla konfigurovat a spravovat zařízení, bude potřebovat smlouva Enterprise (EA) nebo poskytovatele Cloud Solution Provider (CSP) nebo předplatné Microsoft Azure Sponsorship: 

Služba Azure Stack Edge je chráněná funkcemi zabezpečení Azure jako služba pro správu hostovaná v Azure. Šifrovací klíč pro váš prostředek můžete získat ve vlastnostech zařízení pro všechny operace správy sady Software Development Kit, ale tento šifrovací klíč můžete zobrazit pouze v případě, že máte příslušná oprávnění pro Graph API prostředku.

Zařízení Azure Stack Edge pro je místní zařízení, které pomáhá transformovat data tak, že je zpracovává místně a odesílá je do Azure. Vaše zařízení:

- pro přístup ke službě Azure Stack Edge potřebuje aktivační klíč.
- je chráněna za všech okolností heslem zařízení.
- má povolené zabezpečené spouštění.

- je uzamčené zařízení. Řadič pro správu základní desky zařízení (BMC) a systém BIOS jsou chráněné heslem. Systém BIOS je chráněn omezeným přístupem uživatele.
- spustí ochranu zařízení v programu Windows Defender. Device Guard umožňuje spouštět jenom důvěryhodné aplikace, které definujete v zásadách integrity kódu.

Použijte osvědčené postupy pro ochranu všech přihlašovacích údajů a tajných kódů používaných pro přístup k Azure Stack Edge. 

- [Osvědčené postupy pro hesla](azure-stack-edge-security.md#protect-the-device-via-password)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Pravidelná kontrola a sjednocování přístupu uživatelů

**Doprovodné** materiály: Azure Stack Edge má uživatele s názvem "EdgeUser", který může zařízení konfigurovat. Má taky Azure Resource Manager uživatele "EdgeArmUser" pro místní funkce Azure Resource Manager na zařízení. 

Měli byste dodržovat osvědčené postupy pro ochranu:

- Přihlašovací údaje použité pro přístup k místnímu zařízení

- Přihlašovací údaje sdílené složky SMB

- Přístup k klientským systémům, které byly nakonfigurovány na používání sdílených složek systému souborů NFS.

- Klíče místního účtu úložiště, které se používají pro přístup k účtům místního úložiště při použití REST API objektů BLOB

Další informace jsou k dispozici na odkazovaném odkazu.

- [Informace o zabezpečení pro vaše Azure Stack hraniční zařízení](azure-stack-edge-security.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Použití pracovních stanic s privilegovaným přístupem

**Doprovodné** materiály: zabezpečené a izolované pracovní stanice jsou kriticky důležité pro zabezpečení citlivých rolí, jako jsou správci, vývojáři a kritické operátory služeb. Pro úlohy správy Používejte vysoce zabezpečené pracovní stanice s Azure bastionu nebo bez ní. Pomocí Azure Active Directory (Azure AD), programu Microsoft Defender Advanced Threat Protection (ATP) a Microsoft Intune nasaďte zabezpečenou a spravovanou pracovní stanici uživatelů pro úlohy správy. 

Zabezpečené pracovní stanice se dají centrálně spravovat, aby byla vynucena zabezpečená konfigurace, včetně silného ověřování, základních úrovní softwaru a hardwaru a omezeného logického a síťového přístupu.

- [Pochopení pracovních stanic s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Nasazení pracovní stanice s privilegovaným přístupem](/security/compass/privileged-access-deployment)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Ochrana citlivých dat

**Doprovodné** materiály: Azure Stack Edge zpracovává všechna citlivá data jako citlivá, že k těmto datům mají přístup jenom autorizovaní uživatelé. Měli byste dodržovat osvědčené postupy k ochraně přihlašovacích údajů používaných pro přístup k Azure Stack službě Edge.

- [Zabezpečení a ochrana dat v Azure Stack Edge pro](azure-stack-edge-security.md#protect-the-device-via-password)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Šifrování citlivých informací při přenosu

**Doprovodné** materiály: Azure Stack Edge používá pro data v letu zabezpečené kanály. Jsou to:

- Standard TLS 1,2 se používá pro data přenášená mezi zařízením a cloudem Azure. Neexistuje žádná záloha na TLS 1,1 a starší. Pokud se TLS 1,2 nepodporuje, komunikace agenta se zablokuje. Pro Azure Portal a správu sady SDK (Software Development Kit) se vyžaduje také protokol TLS 1,2.

- Když klienti přistupují k zařízení prostřednictvím místního webového uživatelského rozhraní prohlížeče, použije se jako výchozí zabezpečený protokol Standard TLS 1,2.

Osvědčeným postupem je nakonfigurovat v prohlížeči používání protokolu TLS 1,2. Pomocí protokolu SMB 3,0 se šifrováním můžete chránit data při kopírování z vašich datových serverů.

- [Osvědčené postupy pro ochranu dat v letu](azure-stack-edge-security.md#protect-data-in-flight)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="asset-management"></a>Správa aktiv

*Další informace najdete v tématu [Azure Security Benchmark: správa prostředků](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Zajištění přehledu bezpečnostního týmu o rizicích pro prostředky

**Pokyny:** Zajistěte udělení oprávnění Čtenář zabezpečení ve vašem tenantovi Azure a vašich předplatných bezpečnostním týmům, aby mohly monitorovat bezpečnostní rizika s využitím služby Azure Security Center. 

V závislosti na nastavení struktury odpovědnosti bezpečnostních týmů může být monitorování bezpečnostních rizik odpovědností centrálního nebo místního bezpečnostního týmu. Přehledy zabezpečení a bezpečnostní rizika se však v rámci organizace musí vždy agregovat centrálně. 

Oprávnění Čtenář zabezpečení je možné přidělit pro celého tenanta (kořenová skupina pro správu) nebo je vymezit na konkrétní skupiny pro správu nebo konkrétní předplatná. 

Mějte na paměti, že k získání přehledu o úlohách a službách může být potřeba další oprávnění. 

- [Přehled role Čtenář zabezpečení](../role-based-access-control/built-in-roles.md#security-reader)

- [Přehled skupin pro správu Azure](../governance/management-groups/overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>DOP. 5: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Doprovodné** materiály: k rozhraním API Azure Stack hraničních zařízení přes místní Azure Resource Manager můžou přistupovat jenom autorizovaní uživatelé, například "EdgeArmUser". Hesla uživatelského účtu lze spravovat pouze na Azure Portal. 

- [Nastavení hesla Azure Resource Manageru](azure-stack-edge-j-series-set-azure-resource-manager-password.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>DOP. 6: používejte ve výpočetních prostředcích jenom schválené aplikace.

**Doprovodné** materiály: můžete přenést vlastní aplikace, které se budou spouštět na všech místně vytvořených virtuálních počítačích. K vytvoření místních výpočetních virtuálních počítačů na hraničním zařízení zásobníku použijte PowerShellové skripty. Důrazně doporučujeme, abyste v místních virtuálních počítačích spouštěli jenom důvěryhodné aplikace, které by běžely. 

- [Řízení spouštění skriptu PowerShellu v prostředí Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy?preserve-view=true&amp;viewFallbackFrom=powershell-6&view=powershell-7.1)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="logging-and-threat-detection"></a>Protokolování a detekce hrozeb

*Další informace najdete v tématu [Azure Security Benchmark: protokolování a detekce hrozeb](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: povolení detekce hrozeb pro prostředky Azure

**Doprovodné** materiály: Azure Stack Edge nenabízí možnosti detekce hrozeb. Zákazníci ale můžou shromažďovat protokoly auditu v balíčku pro podporu pro detekci a analýzu hrozeb v režimu offline. 

- [Shromáždit balíček podpory pro Azure Stack hraniční zařízení](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Povolení protokolování pro síťové aktivity Azure

**Pokyny**: Azure Stack Edge má Povolené protokoly auditu sítě jako součást balíčku podpory ke stažení. Tyto protokoly je možné analyzovat, aby se pro vaše Azure Stack hraničních zařízení implementovalo monitorování po dobu částečně v reálném čase.

- [Azure Stack Edge shromažďuje balíček pro podporu.](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Povolení protokolování pro prostředky Azure

**Doprovodné** materiály: monitorování v reálném čase s protokoly se v současnosti nepodporuje u Azure Stack Edge. Existuje možnost shromažďování balíčku pro podporu, který umožňuje analyzovat různé protokoly, které jsou v něm obsažené, jako je například brána firewall, software, zaznamenání hardwaru a protokoly událostí systému pro zařízení Azure Stack Edge pro. Mějte na paměti, že při příchozím a odchozím provozu jsou shromažďovány neoprávněné vniknutí softwaru nebo výchozí protokoly brány firewall.

- [Shromáždění balíčku pro podporu pro Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizace správy a analýz protokolu zabezpečení

**Doprovodné** materiály: monitorování v reálném čase s protokoly se v současnosti nepodporuje u Azure Stack Edge. Můžete ale shromáždit balíček podpory, který vám umožní analyzovat různé protokoly, které jsou v něm obsažené.  Balíček pro podporu je komprimován a je stažen do cesty podle vašeho výběru. Rozbalíte balíček a zobrazíte soubory systémových protokolů, které jsou v něm obsažené. Tyto protokoly můžete také odeslat do nástroje pro správu událostí informací o zabezpečení nebo do jiného centrálního úložiště pro analýzu.

- [Shromáždění balíčku pro podporu pro Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurace uchovávání úložiště protokolů

**Pokyny**: období uchování úložiště protokolu nejde změnit na Azure Stack hraničním zařízení. Starší protokoly se podle potřeby vyprázdní. Balíčky podpory ze zařízení můžete shromáždit v pravidelných intervalech, abyste si všechny požadavky zachovali v protokolech delší dobu. 

- [Shromáždění balíčku pro podporu pro Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7: použití schválených zdrojů synchronizace času

**Pokyny**: Azure Stack Edge používá Time.Windows.com, server poskytovatele síťových časů od Microsoftu.  Azure Stack Edge taky umožňuje zákazníkovi nakonfigurovat server protokolu síťového času podle svého výběru.

- [Konfigurace nastavení času zařízení Azure Stack Edge](azure-stack-edge-gpu-deploy-set-up-device-update-time.md#configure-time)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Příprava – aktualizace procesu reakce na incidenty pro Azure

**Doprovodné** materiály: Zajistěte, aby váš plán reakce na incident organizace zahrnoval procesy: 

- reakce na incidenty zabezpečení

- které byly aktualizovány pro Azure Cloud
 
- jsou pravidelně vykonávány, aby se zajistila připravenost

Doporučuje se implementovat zabezpečení pomocí standardizovaných procesů odezvy na incidenty v podnikovém prostředí.

- [Implementace zabezpečení v podnikovém prostředí](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referenční příručka k reakcím na incidenty](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Příprava – nastavení oznámení o incidentu

**Pokyny:** Nastavte kontaktní informace pro incidenty zabezpečení ve službě Azure Security Center. Tyto kontaktní informace používá společnost Microsoft k tomu, aby vás kontaktovala v případě, že služba MSRC (Microsoft Security Response Center) zjistí, že s vašimi daty někdo nezákonně nebo neoprávněně pracoval. Máte také možnost přizpůsobit upozornění na incidenty a oznámení o incidentech v různých službách Azure v závislosti na tom, jak potřebujete na incidenty reagovat. 

- [Nastavení kontaktu ve službě Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detekce a analýza – vytvoření incidentů na základě vysoce kvalitních výstrah 

**Doprovodné** materiály: Ujistěte se, že máte proces vytváření vysoce kvalitních výstrah a měření kvality. To vám umožní naučit se lekce z minulých incidentů a upřednostňovat výstrahy pro analytiky, aby nedocházelo k nezpracovanému zpracování falešně pozitivních výstrah. Vytvářejte vysoce kvalitní výstrahy na základě vašich zkušeností z minulých incidentů, ověřených zdrojů a nástrojů komunity navržených pro generování a vyčištění výstrah pomocí protokolování a korelace pro různé zdroje výstrah. 

Azure Security Center poskytuje vysoce kvalitní výstrahy napříč mnoha prostředky Azure. Pomocí konektoru Security Center Data můžete streamovat výstrahy do Azure Sentinel. Vytvářejte Pokročilá pravidla výstrah pomocí funkce Sentinel Azure pro generování automatických incidentů pro šetření. 

Exportujte výstrahy Security Center a doporučení do Azure Sentinel pomocí funkce Export, která vám usnadní identifikaci rizik pro prostředky Azure. Doporučuje se vytvořit proces pro vyexportování výstrah a doporučení automatizovaným způsobem pro průběžné zabezpečení.

- [Konfigurace exportu](../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Detekce a analýza – prozkoumání incidentu

**Doprovodné** materiály: Zajistěte, aby Analytiki mohli dotazovat a používat různé zdroje dat k sestavení úplného zobrazení aktivity, ke které došlo při zkoumání potenciálních incidentů. K tomu, aby bylo možné sledovat aktivity potenciálního útočníka v rámci dezaktivačního řetězu, by měly být shromažďovány různé typy protokolů.  Také zajistěte, aby se přehledy a poznatky zachytil pro historický odkaz.  

Mezi zdroje dat pro vyšetřování patří centrální zdroje protokolování, ve kterých se již shromažďují protokoly ze služeb v příslušném oboru a spuštěných systémů, ale můžou mezi ně patřit také:

- Síťová data – S využitím protokolů toků skupin zabezpečení sítě a služeb Azure Network Watcher a Azure Monitor můžete zachytávat protokoly toků sítě a další analytické informace. 

- Snímky spuštěných systémů: 

    - S využitím funkce vytváření snímků virtuálního počítače Azure můžete vytvořit snímek disku spuštěného systému. 

    - Chcete-li vytvořit snímek paměti běžícího systému, vyberte možnost nativní výpis paměti operačního systému.

    - Pomocí funkce Snapshot služeb Azure nebo softwarové funkce třetích stran můžete vytvořit snímky spuštěných systémů.

Azure Sentinel nabízí rozsáhlé analýzy dat z prakticky jakéhokoli zdroje protokolů a portál pro správu případů, na kterém můžete spravovat celý životní cyklus incidentů. Analytické informace zjištěné během vyšetřování je možné přidružovat k incidentům pro účely sledování a generování sestav. 

- [Snímek disku počítače s Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Snímek disku počítače s Linuxem](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Shromažďování výpisu paměti a diagnostických informací ze strany podpory Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Vyšetřování incidentů s využitím služby Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Detekce a analýza – stanovení priorit incidentů

**Pokyny:** Poskytněte analytikům kontext, na které incidenty se zaměřit jako první, na základě závažnosti upozornění a citlivosti prostředků. Použijte přiřazenou závažnost pro všechny výstrahy od Azure Security Center, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost vychází z míry důvěry služby Security Center v závěr nebo analýzu, na základě kterých se upozornění vygenerovalo, a také z úrovně spolehlivosti, že za aktivitou, která vedla k upozornění, byl škodlivý záměr.

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

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>Souč_hod-3: Vytvoření zabezpečených konfigurací pro výpočetní prostředky

**Pokyny**: Azure Stack Edge nabízí podporu pro vytváření zabezpečených konfigurací pro místní virtuální počítače, které jsou vytvořeny zákazníky. Při vytváření místních virtuálních počítačů se důrazně doporučuje použít pokyny společnosti Microsoft k vytvoření standardních hodnot zabezpečení.

- [Stažení standardních hodnot zabezpečení, které jsou součástí sady nástrojů dodržování předpisů zabezpečení](/windows/security/threat-protection/windows-security-baselines)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4: udržování zabezpečených konfigurací pro výpočetní prostředky

**Pokyny**: Azure Stack Edge nenabízí žádnou podporu pro udržení zabezpečených konfigurací pro místní virtuální počítače, které vytvořili zákazníci. Důrazně doporučujeme, aby zákazníci používali sady nástrojů pro dodržování předpisů zabezpečení (SCT), které vám pomohou udržovat zabezpečené konfigurace pro výpočetní prostředky.

Hostitelský operační systém a virtuální počítače spravované nástrojem Azure Stack Edge udržují své konfigurace zabezpečení. 

- [Základní hodnoty zabezpečení systému Windows](/windows/security/threat-protection/windows-security-baselines#using-security-baselines-in-your-organization)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5: bezpečné uložení vlastních imagí operačního systému a kontejneru

**Pokyny**: hostitelské operační systémy a virtuální počítače spravované nástrojem Azure Stack Edge jsou bezpečně uložené. 

Zákazníci můžou přenášet své vlastní virtuální počítače a kontejnery a zodpovídá za jejich zabezpečenou správu.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: Rychlá a automatická náprava ohrožení zabezpečení softwaru

**Doprovodné** materiály: Azure Stack Edge poskytuje pravidelné aktualizace oprav a upozorňuje zákazníky, když jsou tyto aktualizace k dispozici pro nápravu ohrožení zabezpečení. Je zodpovědností zákazníka zajistit aktuálnost svých zařízení a virtuálních počítačů (vytvořených v nich) s nejnovějšími opravami.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Provádění pravidelné simulace útoku

**Pokyny:** Podle potřeby provádějte testování průniku nebo aktivity červeného týmu na svých prostředcích Azure a zajistěte nápravu všech kritických zjištění v oblasti zabezpečení.
Postupujte podle pravidel zapojení testování průniku cloudových služeb Microsoftu (Microsoft Cloud Penetration Testing), abyste měli jistotu, že testy průniku neporušují zásady Microsoftu. Využijte strategii Microsoftu a spuštění červeného týmu a testování průniku na živém webu na cloudové infrastruktuře, službách a aplikacích spravovaný Microsoftem.

- [Testování průniku v Azure](../security/fundamentals/pen-testing.md)

- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="endpoint-security"></a>Zabezpečení koncového bodu

*Další informace najdete v tématu [srovnávací zabezpečení Azure Security: Endpoint Security](../security/benchmarks/security-controls-v2-endpoint-security.md).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: použití detekce a odpovědi koncového bodu (EDR)

**Doprovodné** materiály: Azure Stack Edge nepodporuje přímo zjišťování koncových bodů a odpověď (EDR). Můžete ale shromáždit balíček podpory a načíst protokoly auditu. Tyto protokoly se pak dají analyzovat, aby se zkontrolovaly aktivity neobvyklé. 

- [Shromáždit balíček podpory pro Azure Stack hraniční zařízení](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: použití centrálně spravovaného malwarového softwaru pro moderní správu

**Doprovodné** materiály: Azure Stack Edge používá řízení aplikací v programu Windows Defender (WDAC) s zásadou striktní integrity kódu (CI), která umožňuje spustit pouze předem určené aplikace a skripty. Je také povolen Antimalwarový ochrana v reálném čase v programu Windows Defender (RTP). Zákazník se může rozhodnout spustit antimalware ve výpočetních virtuálních počítačích, které vytvoří pro místní spuštění na Azure Stack hraničním zařízení.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="backup-and-recovery"></a>Zálohování a obnovy

*Další informace najdete v tématu [Azure Security Benchmark: zálohování a obnovení](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: zajištění pravidelného automatizovaného zálohování

**Doprovodné** materiály: doporučuje se pravidelné zálohování Azure Stack hraničního zařízení a dá se provést s Azure Backup a dalšími řešeními ochrany dat třetích stran, která chrání data obsažená ve virtuálních počítačích nasazených na zařízení. 

Řešení ochrany dat třetích stran, jako jsou Cohesity, CommVault a Veritas, můžou také poskytnout řešení zálohování pro data v místních sdílených složkách SMB nebo NFS. 

Další informace jsou k dispozici na odkazovaných odkazech.

- [Příprava na selhání zařízení](azure-stack-edge-gpu-prepare-device-failure.md)

- [Ochrana souborů a složek na virtuálních počítačích](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="br-2-encrypt-backup-data"></a>BR-2: šifrování zálohovaných dat

**Doprovodné** materiály: Zajistěte, aby byly zálohy chráněny před útoky. To by mělo zahrnovat šifrování záloh v zájmu ochrany před ztrátou důvěrných informací.  Další informace najdete v řešení zálohování podle vlastního výběru.

- [Ochrana dat v místních sdílených složkách Edge](azure-stack-edge-gpu-prepare-device-failure.md#protect-data-in-edge-local-shares)

- [Ochrana souborů a složek ve virtuálních počítačích](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné** materiály: pravidelně provádějte obnovování dat zálohování. 

- [Postupy obnovení pro Azure Stack Edge](azure-stack-edge-gpu-recover-device-failure.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Zmírnění rizika ztracených klíčů

**Doprovodné** materiály: Zajistěte, aby všechny zálohy Azure Stack Edge, včetně všech klíčů spravovaných zákazníkem, byly chráněné v souladu s osvědčenými postupy organizace. Vaše zařízení Azure Stack Edge je přidruženo k účtu Azure Storage, který se používá jako cílové úložiště pro vaše data v Azure. 

Přístup k účtu Azure Storage řídí předplatná Azure a přístupové klíče k úložišti 2 512 přidružené k tomuto účtu úložiště. Jeden z přístupových klíčů se používá pro účely ověřování, když zařízení Azure Stack Edge přistupuje k účtu úložiště. Druhý klíč je držen v rezervovaném pro pravidelné střídání klíčů. Zajistěte, aby byly pro rotaci klíčů používány osvědčené postupy zabezpečení.

- [Ochrana dat Azure Stack hraniční zařízení v Azure Storage účtech](azure-stack-edge-pro-r-security.md#protect-data-in-storage-accounts)

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

- [Azure Security Benchmark – zabezpečení sítě](../security/benchmarks/index.yml)

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

**Pokyny:** Zřiďte strategii protokolování a reakcí na hrozby, která umožní rychle zjistit a zneškodnit hrozby při současném splnění požadavků na dodržování předpisů. Stanovte prioritu poskytování vysoce kvalitních upozornění a bezproblémového prostředí analytikům, aby se mohli soustředit na hrozby, nikoli na integraci a ruční kroky. 

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující prvky: 

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

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](../security/benchmarks/overview.md).
- Další informace o [základních úrovních zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)
