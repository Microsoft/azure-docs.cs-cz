---
title: Osvědčené postupy zabezpečení pro úlohy IaaS v Azure | Microsoft Docs
description: " Migrace úloh do Azure IaaS přináší příležitosti k přehodnocení našich návrhů. "
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/05/2019
ms.author: barclayn
ms.openlocfilehash: 7024094674bd83f5c23c160e66e8c53dd4988612
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68615483"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Osvědčené postupy zabezpečení pro úlohy IaaS v Azure
Tento článek popisuje osvědčené postupy zabezpečení pro virtuální počítače a operační systémy.

Osvědčené postupy jsou založené na shodě na základě konsensu a pracují s funkcemi a sadami funkcí platformy Azure. Vzhledem k tomu, že se názory a technologie můžou v průběhu času měnit, bude tento článek aktualizován tak, aby odrážel tyto změny.

Ve většině scénářů infrastruktury jako služby (IaaS) jsou [virtuální počítače Azure](/azure/virtual-machines/) hlavním zatížením pro organizace, které využívají cloud computing. Tento fakt je zřejmý v [hybridních scénářích](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) , ve kterých organizace chtějí pomalu migrovat úlohy do cloudu. V takových scénářích použijte [Obecné požadavky na zabezpečení pro IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)a použijte doporučené postupy zabezpečení pro všechny vaše virtuální počítače.

## <a name="shared-responsibility"></a>Sdílená odpovědnost
Vaše zodpovědnost za zabezpečení závisí na typu cloudové služby. Následující tabulka shrnuje rovnováhu mezi zodpovědností pro společnost Microsoft a vy:

![Oblasti odpovědnosti](./media/iaas/sec-cloudstack-new.png)

Požadavky na zabezpečení se liší v závislosti na řadě faktorů, včetně různých typů úloh. Jeden z těchto osvědčených postupů může sám o sobě zabezpečit vaše systémy. Stejně jako u cokoli jiného v zabezpečení je nutné vybrat vhodné možnosti a zjistit, jak mohou řešení navzájem doplňovat vyplněním mezer.

## <a name="protect-vms-by-using-authentication-and-access-control"></a>Ochrana virtuálních počítačů pomocí ověřování a řízení přístupu
Prvním krokem při ochraně virtuálních počítačů je zajištění toho, aby mohli vytvářet nové virtuální počítače a přistupovat k virtuálním počítačům jenom autorizovaným uživatelům.

> [!NOTE]
> Pro zlepšení zabezpečení virtuálních počítačů se systémem Linux v Azure můžete integrovat s ověřováním Azure AD. Když použijete [ověřování Azure AD pro virtuální počítače se systémem Linux](/azure/virtual-machines/linux/login-using-aad), centrálně ovládáte a vynutili zásady, které povolují nebo odmítnou přístup k virtuálním počítačům.
>
>

**Osvědčené postupy**: Řízení přístupu k virtuálnímu počítači.   
**Podrobnosti**: [Zásady Azure](/azure/azure-policy/azure-policy-introduction) můžete použít k vytvoření konvencí pro prostředky ve vaší organizaci a vytváření přizpůsobených zásad. Tyto zásady použijte u prostředků, jako jsou třeba [skupiny prostředků](/azure/azure-resource-manager/resource-group-overview). Virtuální počítače patřící do skupiny prostředků dědí své zásady.

Pokud má vaše organizace mnoho předplatných, možná budete potřebovat způsob, jak efektivně spravovat přístup, zásady a dodržování předpisů pro tyto odběry. [Skupiny pro správu Azure](/azure/azure-resource-manager/management-groups-overview) poskytují úroveň rozsahu nad odběry. Předplatná uspořádáte do skupin pro správu (kontejnery) a podmínky zásad správného řízení aplikujete na tyto skupiny. Všechna předplatná v rámci skupiny pro správu automaticky přebírají podmínky použité pro skupinu. Skupiny pro správu poskytují správu na podnikové úrovni ve velkém měřítku bez ohledu na to, jaké typy předplatného případně máte.

**Osvědčené postupy**: Snižte proměnlivost nastavení a nasazení virtuálních počítačů.   
**Podrobnosti**: Pomocí šablon [Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates) můžete posílit možnosti nasazení a usnadnit pochopení a inventarizaci virtuálních počítačů ve vašem prostředí.

**Osvědčené postupy**: Zabezpečený privilegovaný přístup.   
**Podrobnosti**: K povolení přístupu uživatelů k virtuálním počítačům a jejich nastavení použijte přístup s nejnižšími [oprávněními](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) a předdefinované role Azure:

- [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor): Může spravovat virtuální počítače, ale ne virtuální síť nebo účet úložiště, ke kterým jsou připojené.
- [Přispěvatel klasických virtuálních počítačů](../../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor): Může spravovat virtuální počítače vytvořené pomocí modelu nasazení Classic, ale ne virtuální sítě nebo účtu úložiště, ke kterým jsou virtuální počítače připojené.
- [Správce zabezpečení](../../role-based-access-control/built-in-roles.md#security-admin): Pouze v Security Center: Může zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, upravit zásady zabezpečení, zobrazit výstrahy a doporučení, zavřít výstrahy a doporučení.
- [Uživatel DevTest Labs](../../role-based-access-control/built-in-roles.md#devtest-labs-user): Může zobrazit vše a připojit, spustit, restartovat a vypnout virtuální počítače.

Správci a spolusprávci předplatného můžou toto nastavení změnit, aby měli správci všech virtuálních počítačů v rámci předplatného. Ujistěte se, že všem správcům předplatného a spolusprávcem důvěřujete, abyste se přihlásili k některým z vašich počítačů.

> [!NOTE]
> Doporučujeme konsolidovat virtuální počítače se stejným životním cyklem do stejné skupiny prostředků. Pomocí skupin prostředků můžete nasazovat, monitorovat a shrnout náklady na fakturaci svých prostředků.
>
>

Organizace, které řídí přístup k VIRTUÁLNÍm počítačům a jejich celkové zabezpečení virtuálních počítačů, vylepšit.

## <a name="use-multiple-vms-for-better-availability"></a>Použití více virtuálních počítačů pro lepší dostupnost
Pokud váš virtuální počítač spouští kritické aplikace, které potřebují vysokou dostupnost, důrazně doporučujeme použít více virtuálních počítačů. Pro lepší dostupnost použijte [skupinu dostupnosti](../../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

Skupina dostupnosti je logické seskupení, které můžete v Azure použít k zajištění toho, že prostředky virtuálních počítačů, které do nich umístíte, jsou při jejich nasazení v datacentru Azure izolované od sebe. Azure zajišťuje, aby virtuální počítače, které umístíte do skupiny dostupnosti, běžely na několika fyzických serverech, výpočetních skříních, jednotkách úložiště a síťových přepínačích. Pokud dojde k selhání hardwaru nebo softwaru Azure, ovlivní to jenom podmnožinu vašich virtuálních počítačů a vaše celková aplikace bude dál k dispozici pro vaše zákazníky. Skupiny dostupnosti jsou zásadní funkcí, pokud chcete vytvářet spolehlivé cloudová řešení.

## <a name="protect-against-malware"></a>Chraňte se před malwarem
Měli byste nainstalovat ochranu proti malwaru, která vám usnadní identifikaci a odstraňování virů, spywaru a dalšího škodlivého softwaru. Můžete nainstalovat [Microsoft Antimalware](antimalware.md) nebo řešení ochrany koncového bodu Microsoftu ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://www.microsoft.com/search/result.aspx?q=Windows+defender+endpoint+protection)a [System Center Endpoint Protection](https://www.microsoft.com/search/result.aspx?q=System+Center+endpoint+protection)).

Microsoft Antimalware obsahuje funkce, jako je ochrana v reálném čase, plánované prohledávání, náprava malwaru, aktualizace signatur, aktualizace modulu, vytváření sestav ukázek a shromažďování událostí vyloučení. Pro prostředí, která jsou hostovaná nezávisle na produkčním prostředí, můžete použít antimalwarové rozšíření, které vám pomůžou ochránit vaše virtuální počítače a cloudové služby.

Pomocí [Azure Security Center](https://docs.microsoft.com/azure/security-center/) můžete integrovat řešení Microsoftu proti malwaru a partnerům, aby se usnadnilo nasazení a vestavěné detekce (výstrahy a incidenty).

**Osvědčené postupy**: Nainstalujte antimalwarové řešení pro ochranu před malwarem.   
**Podrobnosti**: [Instalace partnerského řešení Microsoftu nebo antimalwarového programu Microsoftu](../../security-center/security-center-install-endpoint-protection.md)

**Osvědčené postupy**: Integrujte své antimalwarové řešení pomocí Security Center, abyste mohli monitorovat stav vaší ochrany.   
**Podrobnosti**: [Správa potíží s ochranou Endpoint Protection pomocí Security Center](../../security-center/security-center-partner-integration.md)

## <a name="manage-your-vm-updates"></a>Správa aktualizací virtuálních počítačů
Virtuální počítače Azure, jako jsou všechny místní virtuální počítače, se považují za spravované uživatelem. Azure do nich nevloží aktualizace Windows. Musíte spravovat aktualizace virtuálních počítačů.

**Osvědčené postupy**: Udržujte své virtuální počítače v aktuálním prostředí.   
**Podrobnosti**: Pomocí řešení [Update Management](../../automation/automation-update-management.md) v Azure Automation můžete spravovat aktualizace operačního systému pro počítače s Windows a Linux, které jsou nasazené v Azure, v místních prostředích nebo v jiných poskytovatelích cloudu. Můžete rychle vyhodnotit stav dostupných aktualizací na všech počítačích agenta a spravovat proces instalace požadovaných aktualizací pro servery.

Počítače spravované pomocí Update Management pro vyhodnocení a nasazení aktualizací používají následující konfigurace:

- Microsoft Monitoring Agent (MMA) pro Windows nebo Linux
- Konfiguraci požadovaného stavu (DSC) PowerShellu pro Linux
- Funkci Hybrid Runbook Worker služby Automation
- Microsoft Update nebo Windows Server Update Services (WSUS) pro počítače se systémem Windows

Pokud používáte web Windows Update, nechte automatické nastavení web Windows Update povolené.

**Osvědčené postupy**: Zajistěte, aby vytvořené image zahrnovaly nejnovější aktualizace Windows.   
**Podrobnosti**: Vyhledejte a nainstalujte všechny aktualizace systému Windows jako první krok při každém nasazení. Tato míra je obzvláště důležitá pro použití při nasazování bitových kopií, které pocházejí buď z vaší aplikace, nebo z vaší vlastní knihovny. I když se obrázky z Azure Marketplace ve výchozím nastavení automaticky aktualizují, může po veřejné verzi docházet k prodlevě (až na pár týdnů).

**Osvědčené postupy**: Pravidelně znovu nasaďte virtuální počítače, abyste vynutili novou verzi operačního systému.   
**Podrobnosti**: Definujte svůj virtuální počítač pomocí [šablony Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) , abyste ho mohli snadno znovu nasadit. Pomocí šablony získáte opravu a zabezpečený virtuální počítač, když ho potřebujete.

**Osvědčené postupy**: Rychlé použití aktualizací zabezpečení u virtuálních počítačů.   
**Podrobnosti**: Povolte Azure Security Center (úroveň Free nebo úroveň Standard) k [identifikaci chybějících aktualizací zabezpečení a jejich použití](../../security-center/security-center-apply-system-updates.md).

**Osvědčené postupy**: Nainstalujte nejnovější aktualizace zabezpečení.   
**Podrobnosti**: Některé z prvních úloh, které zákazníci přesouvají do Azure, jsou laboratoře a systémy s přístupem k externímu prostředí. Pokud vaše virtuální počítače Azure hostují aplikace nebo služby, které musí být dostupné pro Internet, ostražití se o opravách. Oprava nad operačním systémem. Neopravované chyby zabezpečení u partnerských aplikací můžou také vést k problémům, které se dají vyvarovat, pokud je zavedená řádná správa oprav.

**Osvědčené postupy**: Nasazení a testování řešení zálohování.   
**Podrobnosti**: Záloha musí být zpracována stejným způsobem, jakým se zpracovává jakákoli jiná operace. To platí pro systémy, které jsou součástí produkčního prostředí, které se šíří do cloudu.

Testovací a vývojové systémy musí splňovat strategie zálohování, které poskytují možnosti obnovení, které jsou podobné těm, na které uživatelé zvyklí, na základě zkušeností s místními prostředími. Produkční úlohy přesunuté do Azure by se měly integrovat se stávajícími řešeními zálohování, pokud je to možné. Nebo můžete použít [Azure Backup](../../backup/backup-azure-vms-first-look-arm.md) k vyřešení požadavků na zálohování.

Organizace, které vynutily zásady aktualizace softwaru, jsou vystavené hrozbám, které využívají známé, dříve opravené chyby zabezpečení. Společnosti musí prokázat, že jsou pečlivé vylaďování a používají správné bezpečnostní mechanismy, aby bylo zajištěno zabezpečení jejich úloh v cloudu, aby vyhověly oborovým předpisům.

Osvědčené postupy pro aktualizace softwaru pro tradiční datové centrum a Azure IaaS mají spoustu podobnosti. Doporučujeme, abyste své aktuální zásady aktualizace softwaru vyhodnotili tak, aby zahrnovaly virtuální počítače umístěné v Azure.

## <a name="manage-your-vm-security-posture"></a>Správa stav zabezpečení virtuálních počítačů
Týká kybernetických hrozeb se vyvíjí. Ochrana virtuálních počítačů vyžaduje funkci monitorování, která dokáže rychle detekovat hrozby, zabránit neoprávněnému přístupu k prostředkům, aktivovat výstrahy a snížit falešně pozitivní výsledky.

K monitorování stav zabezpečení [virtuálních počítačů](../../security-center/security-center-linux-virtual-machine.md)s [Windows](../../security-center/security-center-virtual-machine.md) a Linux použijte [Azure Security Center](../../security-center/security-center-intro.md). V Security Center zabezpečíte své virtuální počítače, a využijte přitom následující možnosti:

- Použijte nastavení zabezpečení operačního systému s doporučenými konfiguračními pravidly.
- Identifikujte a stáhněte zabezpečení systému a důležité aktualizace, které mohou chybět.
- Nasaďte doporučení pro antimalwarovou ochranu Endpoint Protection.
- Ověřte šifrování disku.
- Vyhodnoťte a opravte chyby zabezpečení.
- Detekuje hrozby.

Security Center můžou aktivně monitorovat hrozby a potenciální hrozby se zveřejňují v výstrahách zabezpečení. Korelační hrozby jsou shrnuty v jednom zobrazení s názvem incident zabezpečení.

Security Center ukládá data v [protokolech Azure monitor](/azure/log-analytics/log-analytics-overview). Protokoly Azure Monitor poskytují dotazovací jazyk a analytické moduly, které vám poskytnou přehled o provozu aplikací a prostředků. Data se shromažďují taky z [Azure monitor](../../batch/monitoring-overview.md), řešení pro správu a agentů nainstalovaných na virtuálních počítačích v cloudu nebo místně. Tyto sdílené funkce umožňují, abyste si vytvořili úplný přehled o vašem prostředí.

Organizace, které nevyžadují silné zabezpečení pro své virtuální počítače, nevědí o potenciálních pokusech neautorizovaných uživatelů, aby mohli obejít bezpečnostní prvky zabezpečení.

## <a name="monitor-vm-performance"></a>Monitorování výkonu virtuálního počítače
Zneužití prostředků může být problém, když procesy virtuálních počítačů spotřebovávají víc prostředků, než by měly. Problémy s výkonem virtuálního počítače mohou způsobit přerušení služby, což porušuje princip zabezpečení dostupnosti. To je důležité zejména pro virtuální počítače, které hostují službu IIS nebo jiné webové servery, protože vysoké využití procesoru nebo paměti může znamenat útok na útok DoS (Denial of Service). Je nutné monitorovat přístup k virtuálnímu počítači nejen znovu, pokud k problému dojde, ale také aktivně na základě směrného výkonu, který je změřen během normálního provozu.

Doporučujeme použít [Azure monitor](/azure/monitoring-and-diagnostics/monitoring-overview-metrics) , abyste získali přehled o stavu prostředku. Azure Monitor funkce:

- [Soubory protokolu diagnostiky prostředků](../../azure-monitor/platform/diagnostic-logs-overview.md): Monitoruje prostředky virtuálních počítačů a identifikuje potenciální problémy, které by mohly ohrozit výkon a dostupnost.
- [Rozšíření Azure Diagnostics](/azure/azure-monitor/platform/diagnostics-extension-overview): Poskytuje funkce pro monitorování a diagnostiku na virtuálních počítačích s Windows. Tyto funkce můžete povolit zahrnutím rozšíření v rámci [šablony Azure Resource Manager](/azure/virtual-machines/windows/extensions-diagnostics-template).

Organizace, které nemonitorují výkon virtuálních počítačů, nemůžou určit, jestli jsou některé změny v vzorech výkonu normální nebo neobvyklé. Virtuální počítač, který spotřebovává více prostředků než obvykle, může indikovat útok z externího prostředku nebo napadený proces spuštěný ve virtuálním počítači.

## <a name="encrypt-your-virtual-hard-disk-files"></a>Šifrování souborů virtuálního pevného disku
Doporučujeme, abyste zašifroval virtuální pevné disky (VHD), které vám pomůžou chránit spouštěcí svazek a datové svazky v klidovém úložišti, a to spolu s šifrovacími klíči a tajnými kódy.

[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) vám pomůže s šifrováním disků virtuálních počítačů s Windows a Linux IaaS. Azure Disk Encryption používá standardní funkci [nástroje BitLocker](https://technet.microsoft.com/library/cc732774.aspx) systému Windows a funkci [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) systému Linux k poskytování šifrování svazku pro operační systém a datové disky. Řešení je integrované s [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) , které vám pomůžou řídit a spravovat šifrovací klíče a tajné klíče disku v předplatném trezoru klíčů. Řešení také zajišťuje, že všechna data na discích virtuálních počítačů jsou v klidovém stavu zašifrovaná v Azure Storage.

Níže jsou uvedené osvědčené postupy pro používání Azure Disk Encryption:

**Osvědčené postupy**: Povolte šifrování na virtuálních počítačích.   
**Podrobnosti**: Azure Disk Encryption generuje a zapisuje šifrovací klíče do trezoru klíčů. Správa šifrovacích klíčů v trezoru klíčů se vyžaduje ověřování Azure AD. Vytvořte aplikaci Azure AD pro tento účel. Pro účely ověřování, můžete použít buď ověřování na základě tajný kód klienta nebo [ověřování klienta na základě certifikátů Azure AD](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

**Osvědčené postupy**: Pro další vrstvu zabezpečení pro šifrovací klíče použijte šifrovací klíč klíče (KEK). Přidejte KEK do trezoru klíčů.   
**Podrobnosti**: Pomocí rutiny [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) vytvořte šifrovací klíč klíče v trezoru klíčů. KEK můžete také importovat z místního modulu hardwarového zabezpečení (HSM) pro správu klíčů. Další informace najdete v dokumentaci k [Key Vault](../../key-vault/key-vault-hsm-protected-keys.md). Pokud je zadaný šifrovací klíč klíče, Azure Disk Encryption používá tento klíč k šifrování tajných kódů zabalení před zápisem do služby Key Vault. Udržování v úschově kopie tohoto klíče v modulu HSM místní správy klíčů nabízí další ochranu před náhodným odstraněním klíčů.

**Osvědčené postupy**: Před šifrováním disků si pořídit [snímek](../../virtual-machines/windows/snapshot-copy-managed-disk.md) a/nebo zálohu. Pokud během šifrování dojde k neočekávané chybě, zálohování poskytuje možnost obnovení.   
**Podrobnosti**: Virtuální počítače se spravovanými disky vyžadují zálohu, než dojde k šifrování. Po provedení zálohy můžete použít rutinu **set-AzVMDiskEncryptionExtension** k šifrování spravovaných disků zadáním parametru *-skipVmBackup* . Další informace o tom, jak zálohování a obnovení šifrovaných virtuálních počítačů najdete v tématu [Azure Backup](../../backup/backup-azure-vms-encryption.md) článku.

**Osvědčené postupy**: Abyste se ujistili, že šifrovací tajná klíče neobsahují různé regionální hranice, Azure Disk Encryption potřebuje Trezor klíčů a virtuální počítače umístěné ve stejné oblasti.   
**Podrobnosti**: Vytvořte a použijte Trezor klíčů, který je ve stejné oblasti jako virtuální počítač, který chcete zašifrovat.

Když použijete Azure Disk Encryption, můžete splnit následující obchodní potřeby:

- Virtuální počítače s IaaS jsou zabezpečené v klidové době prostřednictvím standardní šifrovací technologie, která řeší požadavky organizace na zabezpečení a dodržování předpisů.
- Virtuální počítače s IaaS začínají na klíčích a zásadách řízených zákazníky a můžete auditovat jejich využití v trezoru klíčů.

## <a name="restrict-direct-internet-connectivity"></a>Omezení přímého připojení k Internetu
Monitorujte a omezte přímé připojení k Internetu z virtuálního počítače. Útočníci nepřetržitě prohledávají rozsahy IP adres veřejných cloudu pro otevřené porty pro správu a pokoušejí "jednoduché" útoky, jako jsou běžná hesla a známá slabá místa v opravených chybách. V následující tabulce jsou uvedeny osvědčené postupy, které vám pomůžou chránit před těmito útoky:

**Osvědčené postupy**: Zabraňte nechtěnému vystavení síťovému směrování a zabezpečení.   
**Podrobnosti**: Pomocí RBAC zajistěte, aby přístup k síťovým prostředkům měla jenom centrální skupina sítě.

**Osvědčené postupy**: Identifikujte a opravte vystavené virtuální počítače, které umožňují přístup z jakékoli zdrojové IP adresy.   
**Podrobnosti**: Použijte Azure Security Center. Security Center se doporučuje omezit přístup prostřednictvím internetových koncových bodů, pokud některá z vašich skupin zabezpečení sítě má jedno nebo několik příchozích pravidel, která umožňují přístup z jakékoli zdrojové IP adresy. Security Center bude doporučit, abyste tato příchozí pravidla upravili a [omezili přístup](../../security-center/security-center-restrict-access-through-internet-facing-endpoints.md) ke zdrojovým IP adresám, které skutečně potřebují přístup.

**Osvědčené postupy**: Omezte porty pro správu (RDP, SSH).   
**Podrobnosti**: [Přístup k virtuálnímu počítači JIT (just-in-time)](../../security-center/security-center-just-in-time.md) se dá použít k uzamknutí příchozího provozu do virtuálních počítačů Azure. tím se sníží riziko útoků na útoky a zároveň se vám umožní snadný přístup k virtuálním počítačům v případě potřeby. Když je kompilátor JIT povolený, Security Center zamkne příchozí provoz do vašich virtuálních počítačů Azure vytvořením pravidla skupiny zabezpečení sítě. Vyberete porty na virtuálním počítači, pro které bude příchozí provoz uzamčen. Tyto porty jsou ovládány řešením JIT.

## <a name="next-steps"></a>Další postup
V článku [osvědčené postupy a vzory zabezpečení Azure](best-practices-and-patterns.md) najdete v tématu Doporučené postupy zabezpečení pro použití při navrhování, nasazování a správě cloudových řešení pomocí Azure.

K dispozici jsou následující prostředky, které poskytují obecnější informace o zabezpečení Azure a souvisejících službách Microsoftu:
* [Blog týmu pro zabezpečení Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – aktuální informace o nejnovější verzi v zabezpečení Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – kde můžou být chyby zabezpečení Microsoftu, včetně problémů s Azure, nahlášené nebo prostřednictvím e-mailu.secure@microsoft.com
