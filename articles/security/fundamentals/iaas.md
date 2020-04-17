---
title: Doporučené postupy zabezpečení pro úlohy IaaS v Azure | Dokumenty společnosti Microsoft
description: " Migrace úloh do Azure IaaS přináší příležitosti k přehodnocení našich návrhů "
services: security
documentationcenter: na
author: terrylanfear
manager: rkarlin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 49a40d78b4ba3bc1e90bb341cca90bece0b998a8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450011"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Osvědčené postupy zabezpečení pro úlohy IaaS v Azure
Tento článek popisuje osvědčené postupy zabezpečení pro virtuální počítače a operační systémy.

Osvědčené postupy jsou založeny na konsensu názoru a pracují s aktuálními funkcemi platformy Azure a sadami funkcí. Vzhledem k tomu, že názory a technologie se mohou v průběhu času měnit, bude tento článek aktualizován tak, aby tyto změny odrážel.

Ve většině scénářů infrastruktury jako služby (IaaS) jsou [virtuální počítače Azure (VM)](/azure/virtual-machines/) hlavní úlohou pro organizace, které používají cloud computing. Tato skutečnost je zřejmá v [hybridních scénářích,](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) kde organizace chtějí pomalu migrovat úlohy do cloudu. V takových případech postupujte podle [obecných aspektů zabezpečení pro IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)a aplikujte doporučené postupy zabezpečení na všechny vaše virtuální počítače.

## <a name="protect-vms-by-using-authentication-and-access-control"></a>Ochrana virtuálních zařízení pomocí ověřování a řízení přístupu
Prvním krokem při ochraně virtuálních počítačů je zajistit, aby jenom oprávnění uživatelé můžou nastavit nové virtuální počítače a přistupovat k virtuálním počítačům.

> [!NOTE]
> Chcete-li zlepšit zabezpečení virtuálních počítačích s Linuxem v Azure, můžete integrovat s ověřováním Azure AD. Při použití [ověřování Azure AD pro virtuální počítače s Linuxem](/azure/virtual-machines/linux/login-using-aad), můžete centrálně řídit a vynucovat zásady, které povolují nebo odepírají přístup k virtuálním počítačům.
>
>

**Osvědčený postup:** Řízení přístupu k virtuálním můtím.   
**Podrobnosti**: Pomocí [zásad Azure](/azure/azure-policy/azure-policy-introduction) můžete vytvořit konvence pro prostředky ve vaší organizaci a vytvořit přizpůsobené zásady. Tyto zásady použijte u prostředků, jako jsou [například skupiny prostředků](/azure/azure-resource-manager/resource-group-overview). Virtuální společnosti, které patří do skupiny prostředků dědí své zásady.

Pokud má vaše organizace mnoho předplatných, můžete potřebovat způsob, jak efektivně u těchto předplatných spravovat přístup, zásady a dodržování předpisů. [Skupiny pro správu Azure](/azure/azure-resource-manager/management-groups-overview) poskytují úroveň oboru nad předplatnými. Můžete uspořádat odběry do skupin pro správu (kontejnery) a použít podmínky zásad správného řízení pro tyto skupiny. Všechna předplatná v rámci skupiny pro správu automaticky dědí podmínky použité pro skupinu. Skupiny pro správu poskytují správu na podnikové úrovni ve velkém měřítku bez ohledu na to, jaké typy předplatného případně máte.

**Osvědčený postup**: Snižte variabilitu v nastavení a nasazení virtuálních počítačích.   
**Podrobnosti:** Pomocí šablon [Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates) upevnit možnosti nasazení a usnadnit pochopení a inventuru virtuálních počítačů ve vašem prostředí.

**Osvědčený postup:** Zabezpečte privilegovaný přístup.   
**Podrobnosti**: Pomocí [přístupu s nejnižšími oprávněními](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) a integrovaných rolí Azure povolte uživatelům přístup a nastavení virtuálních počítačů:

- [Přispěvatel virtuálního počítače:](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)Můžete spravovat virtuální počítače, ale ne virtuální síť nebo účet úložiště, ke kterému jsou připojeny.
- [Klasický přispěvatel virtuálního počítače:](../../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor)Můžete spravovat virtuální počítače vytvořené pomocí klasického modelu nasazení, ale ne virtuální sítě nebo účtu úložiště, ke kterému jsou virtuální počítače připojené.
- [Správce zabezpečení](../../role-based-access-control/built-in-roles.md#security-admin): Pouze v Centru zabezpečení: Může zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, upravit zásady zabezpečení, zobrazit výstrahy a doporučení, zavřít výstrahy a doporučení.
- [Uživatel devTest Labs](../../role-based-access-control/built-in-roles.md#devtest-labs-user): Můžete zobrazit všechno a připojit, spustit, restartovat a vypnout virtuální počítače.

Správci a spolusprávci předplatného můžou toto nastavení změnit a udělat tak správce všech virtuálních počítačích v předplatném. Ujistěte se, že důvěřujete všem správcům předplatného a správcům, aby se přihlásili k libovolnému počítači.

> [!NOTE]
> Doporučujeme konsolidovat virtuální chod se stejným životním cyklem do stejné skupiny prostředků. Pomocí skupin prostředků můžete nasadit, monitorovat a shrnout náklady na fakturaci pro vaše prostředky.
>
>

Organizace, které řídí přístup k virtuálním počítačům a jejich nastavení, zlepšují celkové zabezpečení virtuálních počítačích.

## <a name="use-multiple-vms-for-better-availability"></a>Použití více virtuálních počítačů pro lepší dostupnost
Pokud váš virtuální počítač spouští kritické aplikace, které potřebují mít vysokou dostupnost, důrazně doporučujeme použít více virtuálních počítačů. Pro lepší dostupnost použijte [sadu dostupnosti](../../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) nebo [zóny dostupnosti](../../availability-zones/az-overview.md).

Sada dostupnosti je logické seskupení, které můžete použít v Azure k zajištění, že prostředky virtuálních mitů, které do něj umístíte, jsou od sebe navzájem izolované, když se nasazují v datovém centru Azure. Azure zajišťuje, že virtuální počítače, které umístíte do sady dostupnosti, běží na více fyzických serverech, výpočetních rackech, úložných jednotkách a síťových přepínačích. Pokud dojde k selhání hardwaru nebo softwaru Azure, budou ovlivněny pouze podmnožina virtuálních počítačích a vaše celková aplikace bude i nadále dostupná vašim zákazníkům. Sady dostupnosti jsou základní funkcí, pokud chcete vytvářet spolehlivá cloudová řešení.

## <a name="protect-against-malware"></a>Ochrana před malwarem
Měli byste nainstalovat antimalwarovou ochranu, která vám pomůže identifikovat a odstranit viry, spyware a další škodlivý software. Můžete nainstalovat [program Microsoft Antimalware](antimalware.md) nebo řešení ochrany koncových bodů partnera společnosti Microsoft ([Trend Micro](https://help.deepsecurity.trendmicro.com/Welcome.html), [Broadcom](https://www.broadcom.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://www.microsoft.com/windows/comprehensive-security)a System [Center Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)).

Microsoft Antimalware obsahuje funkce, jako je ochrana v reálném čase, plánované skenování, náprava malwaru, aktualizace podpisů, aktualizace motorů, ukázky hlášení a shromažďování událostí vyloučení. Pro prostředí, která jsou hostovaná odděleně od produkčního prostředí, můžete použít antimalwarové rozšíření k ochraně virtuálních počítačů a cloudových služeb.

Antimalwarová a partnerská řešení Microsoftu můžete integrovat pomocí [Azure Security Center](../../security-center/index.yml) pro snadné nasazení a integrované detekce (výstrahy a incidenty).

**Osvědčený postup:** Nainstalujte antimalwarové řešení, které chrání před malwarem.   
**Detail**: [Instalace partnerského řešení společnosti Microsoft nebo antimalwaru společnosti Microsoft](../../security-center/security-center-install-endpoint-protection.md)

**Osvědčený postup:** Integrujte antimalwarové řešení s Security Center a sledujte stav ochrany.   
**Detail:** [Správa problémů s ochranou koncového bodu pomocí Centra zabezpečení](../../security-center/security-center-partner-integration.md)

## <a name="manage-your-vm-updates"></a>Správa aktualizací virtuálních počítačů
Virtuální počítače Azure, stejně jako všechny místní virtuální počítače, jsou určeny pro spravované uživatelem. Azure v nich nenabízí instalaci aktualizací Windows. Je potřeba spravovat aktualizace virtuálních počítačových sítí.

**Osvědčený postup:** Udržujte virtuální počítače aktuální.   
**Podrobnosti**: Pomocí řešení [pro správu aktualizací](../../automation/automation-update-management.md) v Azure Automation můžete spravovat aktualizace operačního systému pro počítače s Windows a Linuxem, které jsou nasazené v Azure, v místních prostředích nebo v jiných cloudových zprostředkovatelích. Můžete rychle vyhodnotit stav dostupných aktualizací na všech počítačích agenta a spravovat proces instalace požadovaných aktualizací pro servery.

Počítače spravované pomocí Update Managementu používají k provádění posuzovacích a aktualizačních nasazení následující konfigurace:

- Agenta Microsoft Monitoring Agent (MMA) pro Windows nebo Linux
- Konfiguraci požadovaného stavu (DSC) PowerShellu pro Linux
- Funkci Hybrid Runbook Worker služby Automation
- Služby Microsoft Update nebo Windows Server Update Services (WSUS) pro počítače s Windows

Pokud používáte službu Windows Update, ponechte automatické nastavení služby Windows Update povolené.

**Osvědčený postup**: Při nasazení se ujistěte, že vytvořené bitové kopie obsahují nejnovější kolo aktualizací systému Windows.   
**Podrobnosti**: Jako první krok každého nasazení zkontrolujte a nainstalujte všechny aktualizace systému Windows. Toto opatření je obzvláště důležité použít při nasazování bitových kopií, které pocházejí z vás nebo vlastní knihovny. I když se image z Azure Marketplace aktualizují automaticky ve výchozím nastavení, může být po veřejné verzi prodleva (až několik týdnů).

**Osvědčený postup:** Pravidelně znovu nasazujte virtuální počítače tak, aby si vynutily novou verzi operačního systému.   
**Podrobnosti**: Definujte virtuální počítač pomocí [šablony Azure Resource Manager,](../../azure-resource-manager/templates/template-syntax.md) abyste ho mohli snadno znovu nasadit. Pomocí šablony získáte opravený a zabezpečený virtuální virtuální počítače, když ho potřebujete.

**Osvědčený postup**: Rychle použít aktualizace zabezpečení pro virtuální chod.   
**Podrobnosti**: Povolte Azure Security Center (úroveň Free nebo Standard) k [identifikaci chybějících aktualizací zabezpečení a jejich použití](../../security-center/security-center-apply-system-updates.md).

**Osvědčený postup:** Nainstalujte nejnovější aktualizace zabezpečení.   
**Podrobnosti:** Některé z prvních úloh, které zákazníci přesunoudo Azure jsou testovací prostředí a externí systémy. Pokud vaše virtuální počítače Azure hostují aplikace nebo služby, které musí být přístupné pro internet, buďte opatrní ohledně oprav. Oprava mimo operační systém. Neopravené chyby zabezpečení v partnerských aplikacích mohou také vést k problémům, kterým se lze vyhnout, pokud je zavedena dobrá správa oprav.

**Osvědčený postup:** Nasazení a testování řešení zálohování.   
**Podrobnosti**: Zálohování musí být zpracováno stejným způsobem jako při jakékoli jiné operaci. To platí pro systémy, které jsou součástí produkčního prostředí, které se rozšiřuje do cloudu.

Testovací a dev systémy musí dodržovat strategie zálohování, které poskytují možnosti obnovení, které jsou podobné tomu, na co si uživatelé zvykli, na základě svých zkušeností s místním prostředím. Produkční úlohy přesunuté do Azure by se měly integrovat s existujícími řešeními zálohování, pokud je to možné. Nebo můžete použít [Azure Backup](../../backup/backup-azure-vms-first-look-arm.md) k řešení vašich požadavků na zálohování.

Organizace, které nevynucují zásady aktualizace softwaru, jsou více vystaveny hrozbám, které zneužívají známé, dříve opravené chyby zabezpečení. Aby společnosti vyhověly oborovým předpisům, musí prokázat, že jsou důsledné a používají správné bezpečnostní kontroly, aby pomohly zajistit zabezpečení svých úloh umístěných v cloudu.

Doporučené postupy pro aktualizaci softwaru pro tradiční datové centrum a Azure IaaS mají mnoho podobností. Doporučujeme vyhodnotit aktuální zásady aktualizace softwaru tak, aby zahrnovaly virtuální počítače umístěné v Azure.

## <a name="manage-your-vm-security-posture"></a>Správa stavu zabezpečení virtuálního počítače
Kybernetické hrozby se vyvíjejí. Zabezpečení virtuálních počítačů vyžaduje funkci monitorování, která může rychle detekovat hrozby, zabránit neoprávněnému přístupu k prostředkům, aktivovat výstrahy a snížit falešně pozitivní výsledky.

Chcete-li sledovat stav zabezpečení [virtuálních počítačích](../../security-center/security-center-linux-virtual-machine.md) [s Windows](../../security-center/security-center-virtual-machine.md) a Linuxem , použijte Azure Security [Center](../../security-center/security-center-intro.md). V Centru zabezpečení zabezpečte své virtuální počítače využitím následujících funkcí:

- Použijte nastavení zabezpečení operačního systému s doporučenými pravidly konfigurace.
- Identifikujte a stáhněte zabezpečení systému a důležité aktualizace, které mohou chybět.
- Nasaďte doporučení pro antimalwarovou ochranu koncových bodů.
- Ověřte šifrování disku.
- Vyhodnocovat a narušovat zranitelná místa.
- Detekujte hrozby.

Centrum zabezpečení může aktivně sledovat ohrožení a potenciální hrozby jsou vystaveny v výstrahách zabezpečení. Korelované hrozby jsou agregovány v jediném zobrazení nazývaném bezpečnostní incident.

Security Center ukládá data v [protokolech Azure Monitor .](/azure/log-analytics/log-analytics-overview) Protokoly Azure Monitor poskytuje dotazovací jazyk a analytický modul, který vám poskytne přehled o provozu vašich aplikací a prostředků. Data se také shromažďují z [Azure Monitoru](../../batch/monitoring-overview.md), řešení pro správu a agentů nainstalovaných na virtuálních počítačích v cloudu nebo v místním prostředí. Tyto sdílené funkce umožňují, abyste si vytvořili úplný přehled o vašem prostředí.

Organizace, které nevynucují silné zabezpečení svých virtuálních počítačů, si stále nejsou vědomy potenciálních pokusů neoprávněných uživatelů obejít ovládací prvky zabezpečení.

## <a name="monitor-vm-performance"></a>Sledování výkonu virtuálních montovna
Zneužití prostředků může být problém, když procesy virtuálních ms spotřebovávají více prostředků, než by měly. Problémy s výkonem s virtuálním virtuálním ms může vést k přerušení služby, což porušuje princip zabezpečení dostupnosti. To je obzvláště důležité pro virtuální počítače, které jsou hostitelem služby IIS nebo jiných webových serverů, protože vysoké využití procesoru nebo paměti může znamenat útok odmítnutí služby (DoS). Je nezbytné sledovat přístup k virtuálním můly nejen reaktivně, když dochází k problému, ale také proaktivně proti výkonu podle směrného plánu, měřeno během normálního provozu.

Doporučujeme použít [Azure Monitor](/azure/monitoring-and-diagnostics/monitoring-overview-metrics) získat přehled o stavu vašeho prostředku. Funkce Azure Monitoru:

- [Soubory protokolu diagnostiky prostředků](../../azure-monitor/platform/platform-logs-overview.md): Monitoruje prostředky virtuálního počítače a identifikuje potenciální problémy, které by mohly ohrozit výkon a dostupnost.
- [Rozšíření Diagnostika Azure](/azure/azure-monitor/platform/diagnostics-extension-overview): Poskytuje funkce monitorování a diagnostiky na virtuálních počítačích s Windows. Tyto možnosti můžete povolit zahrnutím rozšíření jako součást [šablony Azure Resource Manager](/azure/virtual-machines/windows/extensions-diagnostics-template).

Organizace, které nesledují výkon virtuálního kapitálu, nemohou určit, jestli jsou určité změny ve vzorcích výkonu normální nebo abnormální. Virtuální počítač, který spotřebovává více prostředků, než je obvyklé, může znamenat útok z externího prostředku nebo kompromitovaného procesu spuštěného ve virtuálním počítači.

## <a name="encrypt-your-virtual-hard-disk-files"></a>Šifrování souborů virtuálního pevného disku
Doporučujeme zašifrovat virtuální pevné disky (VD), abyste ochránili spouštěcí svazek a datové svazky v klidovém stavu v úložišti spolu s šifrovacími klíči a tajnými klíči.

[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) vám pomůže šifrovat vaše disky virtuálních počítačů Windows a Linux IaaS. Azure Disk Encryption používá standardní funkci [Nástroje bitlockeru](https://technet.microsoft.com/library/cc732774.aspx) systému Windows a funkci [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) linuxu k zajištění šifrování svazku pro operační systém a datové disky. Řešení je integrované s [Azure Key Vault,](https://azure.microsoft.com/documentation/services/key-vault/) které vám pomohou řídit a spravovat klíče šifrování disku a tajné klíče v předplatném trezoru klíčů. Řešení také zajišťuje, že všechna data na discích virtuálních počítačů jsou šifrována v klidovém stavu ve službě Azure Storage.

Pro použití Azure Disk Encryption postupujte takto:

**Osvědčený postup**: Povolit šifrování na virtuálních počítačích.   
**Podrobnosti:** Azure Disk Encryption generuje a zapisuje šifrovací klíče do trezoru klíčů. Správa šifrovacích klíčů v trezoru klíčů vyžaduje ověřování Azure AD. Vytvořte aplikaci Azure AD pro tento účel. Pro účely ověřování můžete použít ověřování pomocí tajných klíče klienta nebo [ověřování Azure AD založené na klientských certifikátech](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

**Osvědčený postup:** Použijte šifrovací klíč (KEK) pro další vrstvu zabezpečení pro šifrovací klíče. Přidejte kek do trezoru klíčů.   
**Podrobnosti**: Pomocí rutiny [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) vytvořte v trezoru klíčů šifrovací klíč. KEK můžete také importovat z místního modulu hardwarového zabezpečení (HSM) pro správu klíčů. Další informace naleznete v [dokumentaci k trezoru klíčů](../../key-vault/keys/hsm-protected-keys.md). Když je zadán klíč šifrovací klíč, Azure Disk Encryption používá tento klíč k zabalení tajných kódů šifrování před zápisem do trezoru klíčů. Udržování escrow kopii tohoto klíče v místním klíč správy hsm nabízí další ochranu proti náhodnému odstranění klíčů.

**Osvědčený postup**: Před zašifrováním disků pořiďte [snímek](../../virtual-machines/windows/snapshot-copy-managed-disk.md) nebo zálohu. Zálohy poskytují možnost obnovení, pokud dojde k neočekávané chybě během šifrování.   
**Podrobnosti:** Virtuální počítače se spravovanými disky vyžadují zálohu před šifrováním. Po vytvoření zálohy můžete pomocí rutiny **Set-AzVMDiskEncryptionExtension** šifrovat spravované disky zadáním parametru *-skipVmBackup.* Další informace o tom, jak zálohovat a obnovovat šifrované virtuální počítače, najdete v článku [Zálohování Azure.](../../backup/backup-azure-vms-encryption.md)

**Osvědčený postup**: Chcete-li zajistit, aby tajné kódy šifrování nepřekračují místní hranice, azure disk encryption potřebuje trezor klíčů a virtuální počítače, které mají být umístěny ve stejné oblasti.   
**Detail**: Vytvořte a použijte trezor klíčů, který je ve stejné oblasti jako virtuální hod, který má být šifrován.

Při použití Azure Disk Encryption, můžete uspokojit následující obchodní potřeby:

- Virtuální ms IaaS jsou zabezpečené v klidovém stavu prostřednictvím standardní šifrovací technologie, která řeší požadavky na zabezpečení organizace a dodržování předpisů.
- Virtuální počítače IaaS se spouštějí pod klíči a zásadami řízenými zákazníky a můžete auditovat jejich využití v trezoru klíčů.

## <a name="restrict-direct-internet-connectivity"></a>Omezení přímého připojení k internetu
Monitorujte a omezte přímé připojení k internetu virtuálním můe. Útočníci neustále prohledává rozsahy IP adres veřejného cloudu, kde hledají otevřené porty pro správu, a pokoušejí se o "snadné" útoky, jako jsou běžná hesla a známé neopravené chyby zabezpečení. V následující tabulce jsou uvedeny doporučené postupy, které pomáhají chránit před těmito útoky:

**Osvědčený postup**: Zabraňte neúmyslnému vystavení směrování a zabezpečení sítě.   
**Podrobnosti**: Pomocí nástroje RBAC zajistíte, že k síťovým prostředkům má oprávnění pouze centrální síťová skupina.

**Osvědčený postup**: Identifikace a náprava exponovaných virtuálních počítačů, které umožňují přístup z libovolné zdrojové IP adresy.   
**Podrobnosti:** Použijte Azure Security Center. Security Center doporučuje omezit přístup prostřednictvím koncových bodů určených pro Internet, pokud má některá ze skupin zabezpečení sítě jedno nebo více příchozích pravidel, která umožňují přístup z libovolné zdrojové adresy IP. Security Center doporučuje upravit tato příchozí pravidla, abyste [omezili přístup](../../security-center/security-center-network-recommendations.md) ke zdrojovým IP adresám, které skutečně potřebují přístup.

**Osvědčený postup:** Omezit porty pro správu (RDP, SSH).   
**Podrobnosti:** [Přístup k virtuálním počítačům just-in-time (JIT)](../../security-center/security-center-just-in-time.md) lze použít k uzamčení příchozího provozu na virtuálních počítačích Azure, což snižuje vystavení útokům a zároveň poskytuje snadný přístup k připojení k virtuálním počítačům v případě potřeby. Když je jit povoleno, Security Center uzamkne příchozí provoz na vaše virtuální počítače Azure vytvořením pravidla skupiny zabezpečení sítě. Vyberete porty na virtuálním počítači, na které bude uzamčen příchozí provoz. Tyto porty jsou řízeny řešením JIT.

## <a name="next-steps"></a>Další kroky
Podívejte se na [osvědčené postupy a vzory zabezpečení Azure, kde](best-practices-and-patterns.md) najdete další doporučené postupy zabezpečení, které se používají při navrhování, nasazování a správě cloudových řešení pomocí Azure.

K dispozici jsou následující prostředky, které poskytují obecnější informace o zabezpečení Azure a souvisejících službách Microsoftu:
* [Blog týmu zabezpečení Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – aktuální informace o nejnovějších informacích o azure securityu
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – kde lze nahlásit chyby zabezpečení společnosti Microsoft, včetně problémů s Azure, nebo e-mailemsecure@microsoft.com
