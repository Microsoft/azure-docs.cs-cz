---
title: Osvědčené postupy zabezpečení virtuálních počítačů s Windows – Azure
description: Osvědčené postupy pro zabezpečení prostředí virtuálních počítačů s Windows
author: heidilohr
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0ddbd4b798d37498af92cec40af6a80a88115fab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014889"
---
# <a name="security-best-practices"></a>Osvědčené postupy zabezpečení

Windows Virtual Desktop je spravovaná služba virtuálních ploch, která zahrnuje řadu možností zabezpečení pro zajištění bezpečnosti vaší organizace. V nasazení virtuálních klientů Windows spravuje společnost Microsoft části služeb jménem zákazníka. Služba obsahuje mnoho vestavěných pokročilých funkcí zabezpečení, jako je například zpětné připojení, které snižuje riziko související se vzdálenými plochami, které jsou přístupné odkudkoli.

Tento článek popisuje další kroky, které můžete provést jako správce, aby bylo možné zajistit zabezpečení nasazení virtuálních klientů ve Windows vašich zákazníků.

## <a name="security-responsibilities"></a>Bezpečnostní povinnosti

Způsob, jakým služba Cloud Services odlišuje od tradičních místních infrastruktur virtuálních ploch (VDIs), je způsob, jakým zpracovávají bezpečnostní povinnosti. Například v tradiční místní VDI je zákazník zodpovědný za všechny aspekty zabezpečení. Ve většině cloudových služeb se ale tyto odpovědnosti sdílí mezi zákazníkem a společností.

Pokud používáte virtuální počítač s Windows, je důležité si uvědomit, že i když některé komponenty jsou pro vaše prostředí již zabezpečené, budete muset nakonfigurovat další oblasti sami, aby vyhovovaly potřebám zabezpečení vaší organizace.

Tady jsou požadavky na zabezpečení, na které zodpovídáte při nasazení virtuálních počítačů s Windows:

| Nutnost zabezpečení | Je zákazník zodpovědný za to? |
|---------------|:-------------------------:|
|Identita|Yes|
|Zařízení uživatelů (mobilní zařízení a počítač)|Yes|
|Zabezpečení aplikací|Yes|
|Operační systém hostitele relace|Yes|
|Konfigurace nasazení|Yes|
|Správa sítě|Yes|
|Rovina řízení virtualizace|No|
|Fyzičtí hostitelé|No|
|Fyzická síť|No|
|Fyzické datacentrum|No|

Zabezpečení vyžaduje, aby zákazník nezodpovědný za zpracování Microsoftem.

## <a name="azure-security-best-practices"></a>Osvědčené postupy zabezpečení Azure

Virtuální plocha Windows je služba v Azure. Aby se zajistila bezpečnost nasazení virtuálních počítačů s Windows, měli byste zajistit, aby byla zajištěná i tato rovina správy Azure. Pokud chcete zajistit zabezpečení své infrastruktury, zvažte, jak virtuální plocha Windows zapadá do vašeho většího ekosystému Azure. Další informace o ekosystému Azure najdete v tématu [osvědčené postupy a vzory zabezpečení Azure](../security/fundamentals/best-practices-and-patterns.md).

Tato část popisuje osvědčené postupy pro zabezpečení ekosystému Azure.

### <a name="enable-azure-security-center"></a>Povolit Azure Security Center

Pro předplatná, virtuální počítače, trezory klíčů a účty úložiště doporučujeme povolit Azure Security Center Standard.

Díky Azure Security Center Standard můžete:

* Spravujte chyby zabezpečení.
* Vyhodnotit dodržování předpisů se společnými rozhraními, jako je PCI.
* Posílit celkové zabezpečení vašeho prostředí.

Další informace najdete v tématu připojení [předplatného Azure k Security Center Standard](../security-center/security-center-get-started.md).

### <a name="improve-your-secure-score"></a>Zlepšení bezpečnostního skóre

Bezpečné skóre poskytuje doporučení a Rady pro osvědčené postupy pro zlepšení celkového zabezpečení. Tato doporučení se stanovují podle priorit, aby vám pomohla vybrat, které z nich jsou nejdůležitější, a možnosti rychlé opravy vám pomůžou rychle řešit potenciální slabá místa zabezpečení. Tato doporučení se také aktualizují v průběhu času, takže budete mít k disdnešku nejlepší způsob, jak zajistit zabezpečení vašeho prostředí. Další informace najdete v tématu [vylepšení zabezpečeného skóre v Azure Security Center](../security-center/secure-score-security-controls.md).

## <a name="windows-virtual-desktop-security-best-practices"></a>Osvědčené postupy zabezpečení virtuálních počítačů s Windows

Virtuální počítač s Windows má mnoho vestavěných ovládacích prvků zabezpečení. V této části se seznámíte s ovládacími prvky zabezpečení, které můžete použít k zajištění bezpečnosti vašich uživatelů a dat.

### <a name="require-multi-factor-authentication"></a>Vyžadovat Multi-Factor Authentication

Vyžadování služby Multi-Factor Authentication pro všechny uživatele a správce ve virtuálním počítači s Windows vylepšuje zabezpečení celého nasazení. Další informace najdete v tématu [Povolení služby Azure AD Multi-Factor Authentication pro virtuální počítače s Windows](set-up-mfa.md).

### <a name="enable-conditional-access"></a>Povolit podmíněný přístup

Povolením [podmíněného přístupu](../active-directory/conditional-access/overview.md) můžete spravovat rizika před tím, než uživatelům udělíte přístup k prostředí virtuálních počítačů s Windows. Při rozhodování o tom, k jakým uživatelům chcete udělit přístup, doporučujeme také zvážit, kdo je uživatel, jak se přihlašuje a jaké zařízení používají.

### <a name="collect-audit-logs"></a>Shromažďovat protokoly auditu

Povolením shromažďování protokolů auditu můžete zobrazit aktivitu uživatelů a správců, která souvisí s virtuálním počítačem s Windows. Mezi klíčové protokoly auditu patří například:

-   [Protokol aktivit Azure](../azure-monitor/essentials/activity-log.md)
-   [Protokol aktivit Azure Active Directory](../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)
-   [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)
-   [Hostitelé relace](../azure-monitor/agents/agent-windows.md)
-   [Diagnostický protokol virtuálních klientů Windows](../virtual-desktop/diagnostics-log-analytics.md)
-   [Protokoly Key Vault](../key-vault/general/logging.md)

### <a name="use-remoteapps"></a>Použití vzdálených aplikací RemoteApp

Při výběru modelu nasazení můžete buď poskytnout vzdáleným uživatelům přístup k celým virtuálním plochám, nebo jen vybrat aplikace. Vzdálené aplikace nebo aplikace RemoteApp poskytují bezproblémové prostředí, které uživatel pracuje s aplikacemi na jejich virtuálním klientovi. Služba RemoteApp snižuje riziko tím, že umožňuje uživatelům pracovat s podmnožinou vzdáleného počítače vystaveného aplikací.

### <a name="monitor-usage-with-azure-monitor"></a>Monitorování využití pomocí Azure Monitor

Monitorujte využití a dostupnost služby virtuálních počítačů s Windows pomocí [Azure monitor](https://azure.microsoft.com/services/monitor/). Zvažte vytvoření [Upozornění na stav služby](../service-health/alerts-activity-log-service-notifications-portal.md) pro službu Virtual Desktop systému Windows pro příjem oznámení vždy, když dojde k události, která má dopad na službu.

## <a name="session-host-security-best-practices"></a>Osvědčené postupy zabezpečení hostitele relací

Hostitelé relací jsou virtuální počítače, které běží v rámci předplatného Azure a virtuální sítě. Celkové zabezpečení nasazení virtuálních klientů ve Windows závisí na řízeních zabezpečení, které jste umístili na hostitele relace. Tato část popisuje osvědčené postupy pro udržení zabezpečení relace.

### <a name="enable-screen-capture-protection-preview"></a>Povolit ochranu snímku obrazovky (Preview)

Funkce ochrany snímku obrazovky zabraňuje zachycení citlivých informací na koncových bodech klienta. Když tuto funkci povolíte, vzdálený obsah se na snímky obrazovky a sdílené složky automaticky zablokuje nebo skryje. Bude taky skrytá proti škodlivému softwaru, který může průběžně zachytit obsah vaší obrazovky. Doporučujeme zakázat přesměrování schránky, abyste zabránili kopírování vzdáleného obsahu do koncových bodů při použití této funkce.

Tato zásada se vynutila na úrovni hostitele nakonfigurováním klíče registru. Pokud chcete tuto zásadu povolit, otevřete PowerShell a nastavte klíč registru **fEnableScreenCaptureProtection** spuštěním této rutiny:

```powershell
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableScreenCaptureProtection /t REG_DWORD /d 1
```

Otestování této nové funkce:

- Ujistěte se, že jsou vaše fondy hostitelů zřízené v prostředí ověřování.
- Ujistěte se, že jste stáhli a nainstalovali desktopový klient Windows verze 1.2.1526 nebo novější.

>[!NOTE]
>Během období Preview podporují tuto funkci pouze kompletní připojení klientů z koncových bodů Windows 10.

### <a name="enable-endpoint-protection"></a>Povolit službu Endpoint Protection

Pro ochranu vašeho nasazení před známým škodlivým softwarem doporučujeme povolit službu Endpoint Protection na všech hostitelích relací. Můžete použít antivirovou ochranu v programu Windows Defender nebo program třetí strany. Další informace najdete v tématu [Průvodce nasazením antivirové ochrany v programu Windows Defender v prostředí VDI](/windows/security/threat-protection/windows-defender-antivirus/deployment-vdi-windows-defender-antivirus).

V případě profilových řešení, jako je FSLogix nebo jiná řešení, která připojovat soubory VHD, doporučujeme vyloučit přípony souborů VHD.

### <a name="install-an-endpoint-detection-and-response-product"></a>Instalace produktu pro detekci a odpověď koncového bodu

Doporučujeme, abyste nainstalovali produkt pro detekci a odpověď koncového bodu (EDR), abyste zajistili pokročilé možnosti zjišťování a odezvy. V případě serverových operačních systémů s povoleným [Azure Security Center](../security-center/security-center-services.md) bude instalace produktu EDR nasazena v programu Defender atp. Pro klientské operační systémy můžete do těchto koncových bodů nasadit program [Defender ATP](/windows/security/threat-protection/microsoft-defender-atp/onboarding) nebo produkt třetí strany.

### <a name="enable-threat-and-vulnerability-management-assessments"></a>Povolit vyhodnocení správy hrozeb a ohrožení zabezpečení

Identifikace slabých míst softwaru, která existují v operačních systémech a aplikacích, je zásadní pro zachování zabezpečení vašeho prostředí. Azure Security Center vám může pomáhat identifikovat problémové body prostřednictvím posouzení ohrožení zabezpečení pro serverové operační systémy. Můžete také využít program Defender ATP, který poskytuje správu hrozeb a ohrožení zabezpečení pro stolní operační systémy. Produkty třetích stran můžete použít také v případě, že máte na své práci, ale doporučujeme používat ATP Azure Security Center a Defender.

### <a name="patch-software-vulnerabilities-in-your-environment"></a>Oprava ohrožení zabezpečení softwaru ve vašem prostředí

Jakmile identifikujete chybu zabezpečení, je nutné ji opravit. To platí i pro virtuální prostředí, což zahrnuje běžící operační systémy, aplikace, které jsou v nich nasazené, a image, ze kterých vytváříte nové počítače. Postupujte podle sdělení oznámení o opravách od dodavatele a včas používejte opravy. Doporučujeme, abyste použili opravy základních imagí měsíčně, aby bylo zajištěno, že nově nasazené počítače budou co nejlépe zabezpečené.

### <a name="establish-maximum-inactive-time-and-disconnection-policies"></a>Stanovení maximálního neaktivního času a zásad odpojení

Podepisování uživatelů při jejich neaktivním zachovává prostředky a brání přístupu neautorizovaným uživatelům. Doporučujeme, abyste časový limit vyrovnali produktivitě uživatelů a využití prostředků. Pro uživatele, kteří komunikují s bezstavovým aplikacím, zvažte přísnější zásady, které vypne počítače a zachovají prostředky. Odpojení dlouho spuštěných aplikací, které se budou spouštět i v případě, že je uživatel nečinný, například simulace nebo vykreslování CAD, může rušit práci uživatele a může dokonce vyžadovat restartování počítače.

### <a name="set-up-screen-locks-for-idle-sessions"></a>Nastavit zámky obrazovky pro nečinné relace

Nežádoucímu systémovému přístupu můžete zabránit tak, že nakonfigurujete virtuální plochu Windows tak, aby se během nečinnosti pomohlo uzamknout obrazovku počítače a vyžádat si ověřování pro odemknutí

### <a name="establish-tiered-admin-access"></a>Vytvořit vrstvený přístup správce

Doporučujeme uživatelům, kteří nemají oprávnění správce, udělit přístup k virtuálním plochám. Pokud potřebujete softwarové balíčky, doporučujeme, abyste je měli k dispozici prostřednictvím nástrojů pro správu konfigurace, jako je Microsoft Endpoint Manager. V prostředí s více relacemi doporučujeme uživatelům Neumožnit instalaci softwaru přímo.

### <a name="consider-which-users-should-access-which-resources"></a>Zvažte, kteří uživatelé mají přístup k těmto prostředkům.

Zvažte možnost hostitele relace jako rozšíření stávajícího nasazení plochy. Přístup k síťovým prostředkům doporučujeme řídit stejným způsobem jako ostatní počítače ve vašem prostředí, například pomocí segmentace sítě a filtrování. Ve výchozím nastavení se hostitelé relace můžou připojit k jakémukoli prostředku na internetu. Existuje několik způsobů, jak můžete omezit provoz, včetně použití Azure Firewall, síťových virtuálních zařízení nebo proxy serverů. Pokud potřebujete omezit provoz, ujistěte se, že jste přidali správná pravidla, aby virtuální plocha Windows mohla správně fungovat.

### <a name="manage-office-pro-plus-security"></a>Spravovat zabezpečení sady Office pro plus

Kromě zabezpečení hostitelů relací je důležité také zabezpečit aplikace běžící v nich. Office pro plus je jednou z nejběžnějších aplikací nasazených v hostitelích relací. Pro zlepšení zabezpečení nasazení Office doporučujeme použít [Poradce zásad zabezpečení](/DeployOffice/overview-of-security-policy-advisor) pro Microsoft 365 aplikací pro podniky. Tento nástroj identifikuje zásady, které můžete použít pro vaše nasazení, aby se mohlo lépe zabezpečení provádět. Poradce zásad zabezpečení také doporučuje zásady na základě jejich dopadu na zabezpečení a produktivitu.

### <a name="other-security-tips-for-session-hosts"></a>Další tipy k zabezpečení pro hostitele relací

Omezením možností operačního systému můžete posílit zabezpečení hostitelů relací. Tady je několik věcí, které můžete udělat:

- Ovládat přesměrování zařízení přesměrováním jednotek, tiskáren a zařízení USB na místní zařízení uživatele v relaci vzdálené plochy. Doporučujeme, abyste vyhodnotili požadavky na zabezpečení a zkontrolovali, jestli by tyto funkce neměly být zakázané.

- Omezte přístup pomocí Průzkumníka Windows skrytím mapování místních a vzdálených jednotek. Uživatelé tak nebudou moci zjišťovat nechtěné informace o konfiguraci systému a uživatelích.

- Vyhněte se přímému přístupu protokolu RDP k hostitelům relací ve vašem prostředí. Pokud potřebujete přímý přístup přes protokol RDP ke správě nebo odstraňování potíží, povolte přístup za [běhu](../security-center/security-center-just-in-time.md) , abyste omezili potenciální útok na hostitele relace.

- Udělte uživatelům omezená oprávnění, když přistupují k místním a vzdáleným systémům souborů. Můžete omezit oprávnění tím, že zajistěte, aby místní a vzdálené souborové systémy používaly seznamy řízení přístupu s nejnižšími oprávněními. Uživatelé tak budou mít přístup jenom k tomu, co potřebují, a nemůžou měnit ani odstraňovat důležité prostředky.

- Zabraňte spuštění nežádoucího softwaru na hostitelích relací. Můžete povolit blokování aplikací pro další zabezpečení hostitelů relací, což zajistí, že se na hostiteli můžou spouštět jenom aplikace, které povolíte.

## <a name="windows-virtual-desktop-support-for-trusted-launch"></a>Podpora virtuálních počítačů s Windows pro důvěryhodné spuštění

Díky důvěryhodnému spuštění se Gen2 virtuální počítače Azure s rozšířenými funkcemi zabezpečení, které se mají chránit před "na konci zásobníku", a to prostřednictvím vektorů útoku, jako jsou rootkity, spouštěcí sady a malware na úrovni jádra. Níže jsou uvedené rozšířené funkce zabezpečení pro důvěryhodné spuštění, které jsou podporované ve virtuálním počítači s Windows. Další informace o důvěryhodném spuštění najdete na webu [důvěryhodné spuštění pro virtuální počítače Azure (Preview)](../virtual-machines/trusted-launch.md).

### <a name="secure-boot"></a>Zabezpečené spouštění

Zabezpečené spouštění je režim, který podporuje firmware platformy, který chrání firmware před malwaremi a spouštěcími sadami založenými na malwaru. Tento režim povoluje spuštění počítače pouze pomocí podepsaných operačních systémech a ovladačů. 

### <a name="monitor-boot-integrity-using-remote-attestation"></a>Monitorování integrity spouštění pomocí vzdáleného ověřování identity

Vzdálené ověření identity je skvělým způsobem, jak kontrolovat stav virtuálních počítačů. Vzdálené ověření identity ověřuje, zda jsou k dispozici měřené spouštěcí záznamy, originální a pochází z modulu Virtual Trusted Platform Module (vTPM). Při kontrole stavu poskytuje kryptografickou jistotu, že se platforma správně spustila. 

### <a name="vtpm"></a>vTPM

VTPM je virtualizovaná verze hardwaru Trusted Platform Module (TPM) s virtuální instancí čipu TPM na virtuální počítač. vTPM umožňuje vzdálené ověření identity pomocí měření integrity celého spouštěcího řetězu virtuálního počítače (UEFI, OS, System a Drivers). 

Doporučujeme povolit, aby vTPM na vašich virtuálních počítačích používali vzdálené ověřování identity. S povoleným vTPM můžete taky povolit funkce BitLockeru, která poskytuje úplné šifrování pro ochranu neaktivních dat. Všechny funkce využívající vTPM budou mít za následek vázání tajných kódů na konkrétní virtuální počítač. Když se uživatelé připojí ke službě Windows Virtual Desktop ve scénáři ve fondu, můžou se uživatelé přesměrovat na libovolný virtuální počítač ve fondu hostitelů. V závislosti na tom, jak je tato funkce navržena, může mít dopad.

>[!NOTE]
>BitLocker by se neměl používat k zašifrování konkrétního disku, na který ukládáte data profilu FSLogix.

### <a name="virtualization-based-security"></a>Zabezpečení založené na virtualizaci

Zabezpečení založené na virtualizaci (VBS) používá hypervisor k vytvoření a izolaci zabezpečené oblasti paměti, která je pro operační systém nepřístupná. HYPERVISOREM HVCI (integrity kódu Hypervisor-Protected) a ochrana přihlašovacích údajů v programu Windows Defender: k zajištění zvýšené ochrany před ohroženími zabezpečení použijte VBS. 

#### <a name="hypervisor-protected-code-integrity"></a>Hypervisor-Protected integrita kódu

HYPERVISOREM HVCI je výkonné omezení pro systém, které používá nástroj VBS k ochraně procesů režimu jádra systému Windows proti injektáže a spouštění škodlivého nebo neověřeného kódu.

#### <a name="windows-defender-credential-guard"></a>Ochrana Credential Guard v programu Windows Defender

Ochrana Credential Guard v programu Windows Defender používá k izolaci a ochraně tajných kódů možnost VBS, aby k nim měli přístup jenom software privilegovaného systému. To brání neoprávněnému přístupu k těmto tajným klíčům a útokům na krádeži přihlašovacích údajů, jako jsou útoky typu Pass-The-

### <a name="deploy-trusted-launch-in-your-windows-virtual-desktop-environment"></a>Nasazení důvěryhodného spuštění v prostředí virtuálních počítačů s Windows

Virtuální počítač s Windows v současné době nepodporuje automatickou konfiguraci důvěryhodného spuštění během procesu nastavení fondu hostitelů. Pokud chcete použít důvěryhodné spuštění v prostředí virtuálních počítačů s Windows, budete muset důvěryhodné spuštění nasadit normálně a pak ručně přidat virtuální počítač do požadovaného fondu hostitelů.

## <a name="nested-virtualization"></a>Vnořená virtualizace

Následující operační systémy podporují spuštění vnořené virtualizace na virtuálním počítači s Windows:

- Windows Server 2016
- Windows Server 2019
- Windows 10 Enterprise
- Windows 10 Enterprise s více relacemi.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak povolit vícefaktorové ověřování, najdete v tématu [nastavení vícefaktorového ověřování](set-up-mfa.md).
