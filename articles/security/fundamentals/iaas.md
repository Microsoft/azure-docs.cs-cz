---
title: Osvědčené postupy zabezpečení pro úlohy IaaS v Azure | Microsoft Docs
description: " Migrace úloh do Azure IaaS přináší příležitosti k přehodnocení našich návrhů. "
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
ms.openlocfilehash: 6f073777930b4d026d826d2c3586e0886f906206
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503075"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Osvědčené postupy zabezpečení pro úlohy IaaS v Azure
Tento článek popisuje osvědčené postupy zabezpečení pro virtuální počítače a operační systémy.

Osvědčené postupy jsou založené na shodě na základě konsensu a pracují s funkcemi a sadami funkcí platformy Azure. Vzhledem k tomu, že se názory a technologie můžou v průběhu času měnit, bude tento článek aktualizován tak, aby odrážel tyto změny.

Ve většině scénářů infrastruktury jako služby (IaaS) jsou [virtuální počítače Azure](../../virtual-machines/index.yml) hlavním zatížením pro organizace, které využívají cloud computing. Tento fakt je zřejmý v [hybridních scénářích](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) , ve kterých organizace chtějí pomalu migrovat úlohy do cloudu. V takových scénářích použijte [Obecné požadavky na zabezpečení pro IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)a použijte doporučené postupy zabezpečení pro všechny vaše virtuální počítače.

## <a name="protect-vms-by-using-authentication-and-access-control"></a>Ochrana virtuálních počítačů pomocí ověřování a řízení přístupu
Prvním krokem při ochraně virtuálních počítačů je zajištění toho, aby mohli vytvářet nové virtuální počítače a přistupovat k virtuálním počítačům jenom autorizovaným uživatelům.

> [!NOTE]
> Pro zlepšení zabezpečení virtuálních počítačů se systémem Linux v Azure můžete integrovat s ověřováním Azure AD. Když použijete [ověřování Azure AD pro virtuální počítače se systémem Linux](../../virtual-machines/linux/login-using-aad.md), centrálně ovládáte a vynutili zásady, které povolují nebo odmítnou přístup k virtuálním počítačům.
>
>

**Osvědčený postup**: řízení přístupu k virtuálnímu počítači.   
**Podrobnosti**: pomocí [zásad Azure](../../governance/policy/overview.md) můžete navázat konvence pro prostředky ve vaší organizaci a vytvářet přizpůsobené zásady. Tyto zásady použijte u prostředků, jako jsou třeba [skupiny prostředků](../../azure-resource-manager/management/overview.md). Virtuální počítače patřící do skupiny prostředků dědí své zásady.

Pokud má vaše organizace mnoho předplatných, můžete potřebovat způsob, jak efektivně u těchto předplatných spravovat přístup, zásady a dodržování předpisů. [Skupiny pro správu Azure](../../governance/management-groups/overview.md) poskytují úroveň rozsahu nad odběry. Předplatná uspořádáte do skupin pro správu (kontejnery) a podmínky zásad správného řízení aplikujete na tyto skupiny. Všechna předplatná v rámci skupiny pro správu automaticky přebírají podmínky použité pro skupinu. Skupiny pro správu poskytují správu na podnikové úrovni ve velkém měřítku bez ohledu na to, jaké typy předplatného případně máte.

**Osvědčený postup**: Snižte proměnlivost nastavení a nasazení virtuálních počítačů.   
**Podrobnosti**: pomocí šablon [Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) můžete posílit možnosti nasazení a usnadnit pochopení a inventarizaci virtuálních počítačů ve vašem prostředí.

**Osvědčený postup**: zabezpečený privilegovaný přístup.   
**Podrobnosti**: použijte [přístup s nejnižšími oprávněními](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) a předdefinované role Azure, které uživatelům umožní přístup k virtuálním počítačům a jejich nastavení:

- [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor): může spravovat virtuální počítače, ale ne virtuální síť nebo účet úložiště, ke kterým jsou připojené.
- [Přispěvatel klasických virtuálních počítačů](../../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor): může spravovat virtuální počítače vytvořené pomocí modelu nasazení Classic, ale ne virtuální sítě nebo účtu úložiště, ke kterým jsou virtuální počítače připojené.
- [Správce zabezpečení](../../role-based-access-control/built-in-roles.md#security-admin): pouze v Security Center: může zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, upravit zásady zabezpečení, zobrazit výstrahy a doporučení, zavřít výstrahy a doporučení.
- [Uživatel DevTest Labs](../../role-based-access-control/built-in-roles.md#devtest-labs-user): může zobrazit vše a připojit, spustit, restartovat a vypnout virtuální počítače.

Správci a spolusprávci předplatného můžou toto nastavení změnit, aby měli správci všech virtuálních počítačů v rámci předplatného. Ujistěte se, že všem správcům předplatného a spolusprávcem důvěřujete, abyste se přihlásili k některým z vašich počítačů.

> [!NOTE]
> Doporučujeme konsolidovat virtuální počítače se stejným životním cyklem do stejné skupiny prostředků. Pomocí skupin prostředků můžete nasazovat, monitorovat a shrnout náklady na fakturaci svých prostředků.
>
>

Organizace, které řídí přístup k VIRTUÁLNÍm počítačům a jejich celkové zabezpečení virtuálních počítačů, vylepšit.

## <a name="use-multiple-vms-for-better-availability"></a>Použití více virtuálních počítačů pro lepší dostupnost
Pokud váš virtuální počítač spouští kritické aplikace, které potřebují vysokou dostupnost, důrazně doporučujeme použít více virtuálních počítačů. Pro lepší dostupnost použijte [skupinu dostupnosti](../../virtual-machines/availability-set-overview.md) nebo [zóny](../../availability-zones/az-overview.md)dostupnosti.

Skupina dostupnosti je logické seskupení, které můžete v Azure použít k zajištění toho, že prostředky virtuálních počítačů, které do nich umístíte, jsou při jejich nasazení v datacentru Azure izolované od sebe. Azure zajišťuje, aby virtuální počítače, které umístíte do skupiny dostupnosti, běžely na několika fyzických serverech, výpočetních skříních, jednotkách úložiště a síťových přepínačích. Pokud dojde k selhání hardwaru nebo softwaru Azure, ovlivní to jenom podmnožinu vašich virtuálních počítačů a vaše celková aplikace bude dál k dispozici pro vaše zákazníky. Skupiny dostupnosti jsou zásadní funkcí, pokud chcete vytvářet spolehlivé cloudová řešení.

## <a name="protect-against-malware"></a>Ochrana před malwarem
Měli byste nainstalovat ochranu proti malwaru, která vám usnadní identifikaci a odstraňování virů, spywaru a dalšího škodlivého softwaru. Můžete nainstalovat [Microsoft Antimalware](antimalware.md) nebo řešení ochrany koncového bodu Microsoftu ([Trend Micro](https://help.deepsecurity.trendmicro.com/Welcome.html), [Broadcom](https://www.broadcom.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://www.microsoft.com/windows/comprehensive-security)a [System Center Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)).

Microsoft Antimalware obsahuje funkce, jako je ochrana v reálném čase, plánované prohledávání, náprava malwaru, aktualizace signatur, aktualizace modulu, vytváření sestav ukázek a shromažďování událostí vyloučení. Pro prostředí, která jsou hostovaná nezávisle na produkčním prostředí, můžete použít antimalwarové rozšíření, které vám pomůžou ochránit vaše virtuální počítače a cloudové služby.

Pomocí [Azure Security Center](../../security-center/index.yml) můžete integrovat řešení Microsoftu proti malwaru a partnerům, aby se usnadnilo nasazení a vestavěné detekce (výstrahy a incidenty).

**Osvědčený postup**: Nainstalujte antimalwarové řešení pro ochranu před malwarem.   
**Podrobnosti**: [instalace partnerského řešení Microsoftu nebo antimalwaru Microsoftu](../../security-center/security-center-services.md#supported-endpoint-protection-solutions-)

**Osvědčený postup**: Integrujte své antimalwarové řešení pomocí Security Center, abyste mohli monitorovat stav vaší ochrany.   
**Podrobnosti**: [Správa potíží s ochranou endpoint Protection pomocí Security Center](../../security-center/security-center-partner-integration.md)

## <a name="manage-your-vm-updates"></a>Správa aktualizací virtuálních počítačů
Virtuální počítače Azure, jako jsou všechny místní virtuální počítače, se považují za spravované uživatelem. Azure v nich nenabízí instalaci aktualizací Windows. Musíte spravovat aktualizace virtuálních počítačů.

**Osvědčený postup**: Udržujte své virtuální počítače aktuální.   
**Podrobnosti**: pomocí řešení [Update Management](../../automation/update-management/overview.md) v Azure Automation můžete spravovat aktualizace operačního systému pro počítače s Windows a Linux, které jsou nasazené v Azure, v místních prostředích nebo v jiných poskytovatelích cloudu. Můžete rychle vyhodnotit stav dostupných aktualizací na všech počítačích agenta a spravovat proces instalace požadovaných aktualizací pro servery.

Počítače spravované pomocí Update Managementu používají k provádění posuzovacích a aktualizačních nasazení následující konfigurace:

- Agenta Microsoft Monitoring Agent (MMA) pro Windows nebo Linux
- Konfiguraci požadovaného stavu (DSC) PowerShellu pro Linux
- Funkci Hybrid Runbook Worker služby Automation
- Služby Microsoft Update nebo Windows Server Update Services (WSUS) pro počítače s Windows

Pokud používáte web Windows Update, nechte automatické nastavení web Windows Update povolené.

**Osvědčené postupy**: Zajistěte, aby vytvořené image zahrnovaly nejnovější verzi aktualizací Windows.   
**Podrobnosti**: vyhledání a instalace všech aktualizací Windows jako prvního kroku při každém nasazení. Tato míra je obzvláště důležitá pro použití při nasazování bitových kopií, které pocházejí buď z vaší aplikace, nebo z vaší vlastní knihovny. I když se obrázky z Azure Marketplace ve výchozím nastavení automaticky aktualizují, může po veřejné verzi docházet k prodlevě (až na pár týdnů).

**Osvědčený postup**: pravidelně znovu nasaďte virtuální počítače, abyste vynutili novou verzi operačního systému.   
**Podrobnosti**: Definujte svůj virtuální počítač pomocí [šablony Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) , abyste ho mohli snadno znovu nasadit. Pomocí šablony získáte opravu a zabezpečený virtuální počítač, když ho potřebujete.

**Osvědčený postup**: rychlé použití aktualizací zabezpečení u virtuálních počítačů.   
**Podrobnosti**: Povolte Azure Security Center (úroveň Free nebo úroveň Standard) k [identifikaci chybějících aktualizací zabezpečení a jejich použití](../../security-center/asset-inventory.md).

**Osvědčený postup**: Nainstalujte nejnovější aktualizace zabezpečení.   
**Podrobnosti**: některé z prvních úloh, které zákazníci přesouvají do Azure, jsou laboratoře a systémy s přístupem k externímu prostředí. Pokud vaše virtuální počítače Azure hostují aplikace nebo služby, které musí být dostupné pro Internet, ostražití se o opravách. Oprava nad operačním systémem. Neopravované chyby zabezpečení u partnerských aplikací můžou také vést k problémům, které se dají vyvarovat, pokud je zavedená řádná správa oprav.

**Osvědčený postup**: nasazení a testování záložního řešení.   
**Podrobnosti**: záloha musí být zpracována stejným způsobem, jakým se zpracovává jakákoli jiná operace. To platí pro systémy, které jsou součástí produkčního prostředí, které se šíří do cloudu.

Testovací a vývojové systémy musí splňovat strategie zálohování, které poskytují možnosti obnovení, které jsou podobné těm, na které uživatelé zvyklí, na základě zkušeností s místními prostředími. Produkční úlohy přesunuté do Azure by se měly integrovat se stávajícími řešeními zálohování, pokud je to možné. Nebo můžete použít [Azure Backup](../../backup/backup-azure-vms-first-look-arm.md) k vyřešení požadavků na zálohování.

Organizace, které vynutily zásady aktualizace softwaru, jsou vystavené hrozbám, které využívají známé, dříve opravené chyby zabezpečení. Společnosti musí prokázat, že jsou pečlivé vylaďování a používají správné bezpečnostní mechanismy, aby bylo zajištěno zabezpečení jejich úloh v cloudu, aby vyhověly oborovým předpisům.

Osvědčené postupy pro aktualizace softwaru pro tradiční datové centrum a Azure IaaS mají spoustu podobnosti. Doporučujeme, abyste své aktuální zásady aktualizace softwaru vyhodnotili tak, aby zahrnovaly virtuální počítače umístěné v Azure.

## <a name="manage-your-vm-security-posture"></a>Správa stav zabezpečení virtuálních počítačů
Týká kybernetických hrozeb se vyvíjí. Ochrana virtuálních počítačů vyžaduje funkci monitorování, která dokáže rychle detekovat hrozby, zabránit neoprávněnému přístupu k prostředkům, aktivovat výstrahy a snížit falešně pozitivní výsledky.

K monitorování stav zabezpečení [virtuálních počítačů](../../security-center/security-center-introduction.md)s [Windows](../../security-center/security-center-introduction.md) a Linux použijte [Azure Security Center](../../security-center/security-center-introduction.md). V Security Center zabezpečíte své virtuální počítače, a využijte přitom následující možnosti:

- Použijte nastavení zabezpečení operačního systému s doporučenými konfiguračními pravidly.
- Identifikujte a stáhněte zabezpečení systému a důležité aktualizace, které mohou chybět.
- Nasaďte doporučení pro antimalwarovou ochranu Endpoint Protection.
- Ověřte šifrování disku.
- Vyhodnoťte a opravte chyby zabezpečení.
- Detekuje hrozby.

Security Center můžou aktivně monitorovat hrozby a potenciální hrozby se zveřejňují v výstrahách zabezpečení. Korelační hrozby jsou shrnuty v jednom zobrazení s názvem incident zabezpečení.

Security Center ukládá data v [protokolech Azure monitor](../../azure-monitor/logs/log-query-overview.md). Protokoly Azure Monitor poskytují dotazovací jazyk a analytické moduly, které vám poskytnou přehled o provozu aplikací a prostředků. Data se shromažďují taky z [Azure monitor](../../batch/monitoring-overview.md), řešení pro správu a agentů nainstalovaných na virtuálních počítačích v cloudu nebo místně. Tyto sdílené funkce umožňují, abyste si vytvořili úplný přehled o vašem prostředí.

Organizace, které nevyžadují silné zabezpečení pro své virtuální počítače, nevědí o potenciálních pokusech neautorizovaných uživatelů, aby mohli obejít bezpečnostní prvky zabezpečení.

## <a name="monitor-vm-performance"></a>Monitorování výkonu virtuálního počítače
Zneužití prostředků může být problém, když procesy virtuálních počítačů spotřebovávají víc prostředků, než by měly. Problémy s výkonem virtuálního počítače mohou způsobit přerušení služby, což porušuje princip zabezpečení dostupnosti. To je důležité zejména pro virtuální počítače, které hostují službu IIS nebo jiné webové servery, protože vysoké využití procesoru nebo paměti může znamenat útok na útok DoS (Denial of Service). Je nutné monitorovat přístup k virtuálnímu počítači nejen znovu, pokud k problému dojde, ale také aktivně na základě směrného výkonu, který je změřen během normálního provozu.

Doporučujeme použít [Azure monitor](../../azure-monitor/data-platform.md) , abyste získali přehled o stavu prostředku. Azure Monitor funkce:

- [Soubory protokolu diagnostiky prostředků](../../azure-monitor/essentials/platform-logs-overview.md): monitoruje prostředky virtuálních počítačů a identifikuje potenciální problémy, které by mohly ohrozit výkon a dostupnost.
- [Rozšíření Azure Diagnostics](../../azure-monitor/agents/diagnostics-extension-overview.md): poskytuje funkce pro monitorování a diagnostiku na virtuálních počítačích s Windows. Tyto funkce můžete povolit zahrnutím rozšíření v rámci [šablony Azure Resource Manager](../../virtual-machines/extensions/diagnostics-template.md).

Organizace, které nemonitorují výkon virtuálních počítačů, nemůžou určit, jestli jsou některé změny v vzorech výkonu normální nebo neobvyklé. Virtuální počítač, který spotřebovává více prostředků než obvykle, může indikovat útok z externího prostředku nebo napadený proces spuštěný ve virtuálním počítači.

## <a name="encrypt-your-virtual-hard-disk-files"></a>Šifrování souborů virtuálního pevného disku
Doporučujeme, abyste zašifroval virtuální pevné disky (VHD), které vám pomůžou chránit spouštěcí svazek a datové svazky v klidovém úložišti, a to spolu s šifrovacími klíči a tajnými kódy.

[Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) vám pomůže s šifrováním disků virtuálních počítačů s Windows a Linux IaaS. Azure Disk Encryption používá standardní funkci [nástroje BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) systému Windows a funkci [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) systému Linux k poskytování šifrování svazku pro operační systém a datové disky. Řešení je integrované s [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) , které vám pomůžou řídit a spravovat šifrovací klíče a tajné klíče disku v předplatném trezoru klíčů. Řešení také zajišťuje, že všechna data na discích virtuálních počítačů jsou v klidovém stavu zašifrovaná v Azure Storage.

Níže jsou uvedené osvědčené postupy pro používání Azure Disk Encryption:

**Osvědčený postup**: povolení šifrování na virtuálních počítačích.   
**Podrobnosti**: Azure Disk Encryption generuje a zapisuje šifrovací klíče do trezoru klíčů. Správa šifrovacích klíčů v trezoru klíčů vyžaduje ověřování Azure AD. Vytvořte pro tento účel aplikaci Azure AD. Pro účely ověřování můžete použít buď ověřování na základě tajného klíče klienta, nebo [ověřování Azure AD založené na certifikátech klienta](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

**Osvědčený postup**: použijte klíč šifrování klíče (KEK) pro další vrstvu zabezpečení šifrovacích klíčů. Přidejte KEK do trezoru klíčů.   
**Podrobnosti**: pomocí rutiny [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) vytvořte šifrovací klíč klíče v trezoru klíčů. KEK můžete také importovat z místního modulu hardwarového zabezpečení (HSM) pro správu klíčů. Další informace najdete v dokumentaci k [Key Vault](../../key-vault/keys/hsm-protected-keys.md). Když je zadaný klíč šifrování klíče, Azure Disk Encryption používá tento klíč k zabalení šifrovacích tajných kódů před zápisem do Key Vault. Udržování v úschově kopie tohoto klíče v modulu HSM místní správy klíčů nabízí další ochranu před náhodným odstraněním klíčů.

**Osvědčený postup**: před zašifrováním disků si pořídit [snímek](../../virtual-machines/windows/snapshot-copy-managed-disk.md) a/nebo zálohu. Pokud během šifrování dojde k neočekávané chybě, zálohování poskytuje možnost obnovení.   
**Podrobnosti**: virtuální počítače se spravovanými disky vyžadují zálohování před tím, než dojde k šifrování. Po provedení zálohy můžete použít rutinu **set-AzVMDiskEncryptionExtension** k šifrování spravovaných disků zadáním parametru *-skipVmBackup* . Další informace o zálohování a obnovení šifrovaných virtuálních počítačů najdete v článku o [Azure Backup](../../backup/backup-azure-vms-encryption.md) .

**Osvědčený postup**: aby se zajistilo, že šifrovací tajná klíč nepřekračuje regionální hranice, Azure Disk Encryption potřebuje Trezor klíčů a virtuální počítače umístěné ve stejné oblasti.   
**Podrobnosti**: Vytvořte a použijte Trezor klíčů, který je ve stejné oblasti jako virtuální počítač, který chcete zašifrovat.

Když použijete Azure Disk Encryption, můžete splnit následující obchodní potřeby:

- K zabezpečení neaktivních uložených virtuálních počítačů IaaS se používá standardní oborová šifrovací technologie, která řeší jak požadavky organizace na zabezpečení, tak požadavky na dodržování předpisů.
- Virtuální počítače s IaaS začínají na klíčích a zásadách řízených zákazníky a můžete auditovat jejich využití v trezoru klíčů.

## <a name="restrict-direct-internet-connectivity"></a>Omezení přímého připojení k Internetu
Monitorujte a omezte přímé připojení k Internetu z virtuálního počítače. Útočníci nepřetržitě prohledávají rozsahy IP adres veřejných cloudu pro otevřené porty pro správu a pokoušejí "jednoduché" útoky, jako jsou běžná hesla a známá slabá místa v opravených chybách. V následující tabulce jsou uvedeny osvědčené postupy, které vám pomůžou chránit před těmito útoky:

**Osvědčený postup**: zabránění nechtěnému vystavení síťovému směrování a zabezpečení.   
**Podrobnosti**: pomocí Azure RBAC zajistěte, aby přístup k síťovým prostředkům měla jenom centrální skupina sítě.

**Osvědčený postup**: identifikujte a opravte vystavené virtuální počítače, které umožňují přístup z jakékoli zdrojové IP adresy.   
**Podrobnosti**: použijte Azure Security Center. Security Center se doporučuje omezit přístup prostřednictvím internetových koncových bodů, pokud některá z vašich skupin zabezpečení sítě má jedno nebo několik příchozích pravidel, která umožňují přístup z jakékoli zdrojové IP adresy. Security Center bude doporučit, abyste tato příchozí pravidla upravili a [omezili přístup](../../security-center/security-center-network-recommendations.md) ke zdrojovým IP adresám, které skutečně potřebují přístup.

**Osvědčený postup**: Omezte porty pro správu (RDP, SSH).   
**Podrobnosti**: [přístup k virtuálnímu počítači JIT (just-in-time)](../../security-center/security-center-just-in-time.md) se dá použít k uzamknutí příchozího provozu do virtuálních počítačů Azure. tím se sníží riziko útoků na útoky a zároveň se vám umožní snadný přístup k virtuálním počítačům v případě potřeby. Když je kompilátor JIT povolený, Security Center zamkne příchozí provoz do vašich virtuálních počítačů Azure vytvořením pravidla skupiny zabezpečení sítě. Vyberete porty na virtuálním počítači, pro které bude příchozí provoz uzamčen. Tyto porty jsou ovládány řešením JIT.

## <a name="next-steps"></a>Další kroky
V článku [osvědčené postupy a vzory zabezpečení Azure](best-practices-and-patterns.md) najdete v tématu Doporučené postupy zabezpečení pro použití při navrhování, nasazování a správě cloudových řešení pomocí Azure.

K dispozici jsou následující prostředky, které poskytují obecnější informace o zabezpečení Azure a souvisejících službách Microsoftu:
* [Blog týmu pro zabezpečení Azure](/archive/blogs/azuresecurity/) – aktuální informace o nejnovější verzi v zabezpečení Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – kde můžou být chyby zabezpečení Microsoftu, včetně problémů s Azure, nahlášené nebo prostřednictvím e-mailu. secure@microsoft.com