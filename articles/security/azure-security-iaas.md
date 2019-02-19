---
title: Osvědčené postupy zabezpečení pro IaaS úloh v Azure | Dokumentace Microsoftu
description: " Migrace úloh do Azure IaaS přináší příležitosti pro opětovné vyhodnocení našich návrhů "
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2018
ms.author: barclayn
ms.openlocfilehash: 6bf73bcc691e2ab27f3ec379530a59d3b616a070
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341212"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Osvědčené postupy zabezpečení pro úlohy IaaS v Azure

Ve většině infrastruktura jako služba (IaaS) scénáře [Azure virtuální počítače (VM)](https://docs.microsoft.com/azure/virtual-machines/) jsou hlavní úlohy pro organizace, které používají cloud computing. Tato skutečnost je zřejmé ve [hybridních scénářů](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) kde chcete pomalu migrace úloh do cloudu organizace. V takových scénářích, postupujte [obecné otázky zabezpečení pro IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)a použijte osvědčené postupy zabezpečení pro všechny virtuální počítače.

Vaše odpovědnosti pro zabezpečení podle typu cloudovou službu. Následující diagram obsahuje souhrn zůstatek odpovědnost za Microsoftu a můžete:

![Oblasti zodpovědnosti](./media/azure-security-iaas/sec-cloudstack-new.png)

Požadavky na zabezpečení se liší v závislosti na řadě faktorů včetně různé druhy úloh. Není jednou z těchto osvědčených postupů můžete samostatně zabezpečit vaše systémy. Jako nic jiného v zabezpečení budete muset vybrat odpovídající možnosti a naleznete v tématu Jak řešení můžete vzájemně doplňují vyplněním mezery.

Tento článek popisuje osvědčené postupy zabezpečení pro virtuální počítače a operační systémy.

Osvědčené postupy jsou založené na konsenzus názorů a práci s aktuální možnosti platformy Azure a sady funkcí. Protože můžou časem změnit názory a technologií, tento článek bude aktualizován tak, aby odrážela tyto změny.

## <a name="protect-vms-by-using-authentication-and-access-control"></a>Ochrana virtuálních počítačů s využitím ověřování a řízení přístupu
Prvním krokem při ochraně vašich virtuálních počítačů je, aby, který jen na autorizované uživatele můžete nastavit nové virtuální počítače a přístup k virtuálním počítačům.

**Osvědčený postup**: Řízení přístupu k virtuálním počítačům.   
**Podrobnosti o**: Použití [zásady Azure](../governance/policy/overview.md) k zahájení vytváření názvů pro prostředky ve vaší organizaci a vytvářet vlastní zásady. Použít tyto zásady na prostředky, jako například [skupiny prostředků](../azure-resource-manager/resource-group-overview.md). Virtuální počítače, které patří do skupiny prostředků dědit její zásady.

Pokud má vaše organizace více předplatných, můžete potřebovat způsob, jak efektivně spravovat přístup, zásady a dodržování předpisů u těchto předplatných. [Skupiny pro správu Azure](../azure-resource-manager/management-groups-overview.md) zajišťují určitou úroveň oboru nad předplatných. Uspořádání předplatných do skupin pro správu (kontejnerů) a použít vaše podmínky zásad správného řízení do těchto skupin. Všechna předplatná v rámci skupiny pro správu automaticky dědí podmínky na skupinu aplikují. Skupiny pro správu poskytují správu na podnikové úrovni ve velkém měřítku bez ohledu na to, jaké typy předplatného případně máte.

**Osvědčený postup**: Snižte proměnlivé instalace a nasazení virtuálních počítačů.   
**Podrobnosti o**: Použití [Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md) šablony, které posílí vaše možnosti nasazení a lépe pochopit a inventáře virtuálních počítačů ve vašem prostředí.

**Osvědčený postup**: Zabezpečení privilegovaného přístupu.   
**Podrobnosti o**: Použití [nejnižší oprávnění přístupu](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) a předdefinované role Azure pro umožnění uživatelům přístup a nastavení virtuálních počítačů:

- [Přispěvatel virtuálních počítačů](../role-based-access-control/built-in-roles.md#virtual-machine-contributor): Můžete spravovat virtuální počítače, ale virtuální sítě nebo úložiště účet ke kterému jsou připojené.
- [Přispěvatel klasických virtuálních počítačů](../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor): Můžete spravovat virtuální počítače vytvořené pomocí modelu nasazení classic, ale virtuální sítě nebo úložiště účet ke kterému jsou připojené virtuální počítače.
- [Správce zabezpečení](../role-based-access-control/built-in-roles.md#security-admin): Ve službě Security Center jenom: Můžete zobrazit zásady zabezpečení, zobrazení stavu zabezpečení, upravit zásady zabezpečení, zobrazení výstrah a doporučení, Zavřít upozornění a doporučení.
- [DevTest Labs User](../role-based-access-control/built-in-roles.md#devtest-labs-user): Můžete zobrazit vše, co připojení, spuštění, restartování a vypínání virtuálních počítačů.

Správci předplatného a coadmins můžete změnit toto nastavení, díky kterým jsou správci všech virtuálních počítačů v rámci předplatného. Ujistěte se, že důvěřujete všech správců předplatného a coadmins se přihlaste k některé z vašich počítačů.

> [!NOTE]
> Doporučujeme, abyste konsolidace virtuálních počítačů se stejným životním cyklem do stejné skupiny prostředků. Pomocí skupin prostředků, můžete nasadit, monitorovat a seskupit fakturační náklady pro vaše prostředky.
>
>

Organizace, které řídí přístup k virtuálním počítačům a instalační program vylepšit jejich celkové zabezpečení virtuálního počítače.

## <a name="use-multiple-vms-for-better-availability"></a>Použití několika virtuálních počítačů pro lepší dostupnost
Pokud váš virtuální počítač běží důležité aplikace, které je potřeba mít vysokou dostupnost, důrazně doporučujeme použít několik virtuálních počítačů. Pro lepší dostupnosti, použijte [dostupnosti](../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

Skupina dostupnosti je logické seskupení, které můžete použít k zajištění, že prostředky virtuálních počítačů, které do ní umístíte jsou od sebe navzájem izolované při nasazení v datacentru Azure v Azure. Azure tak zajišťuje jistotu, že virtuální počítače do skupiny dostupnosti umístíte nastavit spuštění napříč více fyzických serverů, stojany výpočetní prostředky, jednotkami úložiště a síťové přepínače. Pokud dojde k hardwaru nebo softwaru Azure selhání, se vztahuje pouze dílčí část vašich virtuálních počítačů a vaše celkové aplikace nadále být k dispozici zákazníkům. Skupiny dostupnosti představují základní funkci, pokud chcete vytváření spolehlivých cloudových řešení.

## <a name="protect-against-malware"></a>Chraňte se před malwarem
Měli byste nainstalovat ochrany proti malwaru na pomáhá s identifikací a likvidací virů, spywaru a dalšího škodlivého softwaru. Můžete nainstalovat [Microsoft Antimalware](azure-security-antimalware.md) nebo partnera společnosti Microsoft řešení ochrany koncových bodů ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Programu Windows Defender](https://www.microsoft.com/search/result.aspx?q=Windows+defender+endpoint+protection), a [System Center Endpoint Protection](https://www.microsoft.com/search/result.aspx?q=System+Center+endpoint+protection)).

Microsoft Antimalware zahrnuje funkce, jako je ochrana v reálném čase, naplánovanou kontrolu, malwarové nápravy, aktualizace signatur, aktualizace vyhledávacího stroje, ukázky reporting a vyloučení shromažďování událostí. Pro prostředí, které jsou hostované odděleně od produkčního prostředí můžete použít antimalwarové rozšíření k ochraně vašich virtuálních počítačů a cloudových služeb.

Můžete integrovat Antimalware od Microsoftu a partnerských řešení pomocí [Azure Security Center](https://docs.microsoft.com/azure/security-center/) pro snadné nasazení a integrované detekce (výstrah a incidentů).

**Osvědčený postup**: Nainstalujte antimalwarové řešení pro ochranu před škodlivým softwarem.   
**Podrobnosti o**: [Nainstalujte řešení partnera Microsoftu nebo Microsoft Antimalware](../security-center/security-center-install-endpoint-protection.md)

**Osvědčený postup**: Integrace antimalwarové řešení pomocí služby Security Center k monitorování stavu vaší ochrany.   
**Podrobnosti o**: [Spravovat problémy s endpoint protection pomocí služby Security Center](../security-center/security-center-partner-integration.md)

## <a name="manage-your-vm-updates"></a>Správa aktualizací vašeho virtuálního počítače
Virtuální počítače Azure, jako jsou všechny místní virtuální počítače, jsou určené jako spravované uživatelem. Azure nemá k nim nabízené aktualizace Windows. Potřebujete spravovat vaše aktualizace virtuálního počítače.

**Osvědčený postup**: Udržujte si přehled virtuálních počítačů.   
**Podrobnosti o**: Použití [Update Management](../automation/automation-update-management.md) řešení ve službě Azure Automation ke správě operačního systému aktualizací pro počítače s Windows a Linuxem, které jsou nasazené v Azure, v místním prostředí nebo v jiných cloudových poskytovatelů. Můžete rychle vyhodnotit stav dostupných aktualizací na všech počítačích agenta a spravovat proces instalace požadovaných aktualizací pro servery.

Počítače, které se spravují přes Update Management použít k provedení vyhodnocení a nasazení aktualizací následující konfigurace:

- Microsoft Monitoring Agent (MMA) pro Windows nebo Linux
- Konfiguraci požadovaného stavu (DSC) PowerShellu pro Linux
- Funkci Hybrid Runbook Worker služby Automation
- Microsoft Update nebo Windows Server Update Services (WSUS) pro počítače s Windows

Pokud používáte službu Windows Update, nechte nastavení automatické aktualizace Windows povolena.

**Osvědčený postup**: Při nasazení Ujistěte se, že Image, kterou jste vytvořili zahrnují nejnovější kruhové aktualizace Windows.   
**Podrobnosti o**: Kontrolovat a instalovat všechny aktualizace Windows jako první krok pro každé nasazení. Tato míra je obzvláště důležité pro použití při nasazování bitové kopie, které pocházejí od vás nebo vaší vlastní knihovny. I když se ve výchozím nastavení se automaticky aktualizují Image z Azure Marketplace, může být prodleva (až několik týdnů) po veřejném vydání.

**Osvědčený postup**: Pravidelně znovu nasadíte virtuální počítače k vynucení novou verzi operačního systému.   
**Podrobnosti o**: Definovat svého virtuálního počítače pomocí [šablony Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md) tak jej snadno znovu. Pomocí šablony vám virtuálního počítače s nainstalovanou a zabezpečené až ji budete potřebovat.

**Osvědčený postup**: Nainstalujte nejnovější aktualizace zabezpečení.   
**Podrobnosti o**: Některé z prvních úloh, které zákazníci přejít do Azure jsou cvičení a externích systémů. Pokud vaše virtuální počítače Azure ukládat aplikace nebo služby, které musí být přístupný na Internetu, se mimořádnou o opravy. Oprava rámec operačního systému. Spojená s neopravenými chybami v partnerských aplikací může také vést k problémům, které můžete se vyhnout, pokud je Správa dobré opravu na místě.

**Osvědčený postup**: Nasazení a testování řešení zálohování.   
**Podrobnosti o**: Zálohy musí být zpracována stejným způsobem, že zpracování jiné operace. To platí pro systémy, které jsou součástí vašeho produkčního prostředí rozšíření do cloudu.

Testování a vývoj systémů, musí dodržovat strategii zálohování, které poskytují možnosti obnovení, které jsou podobné uživatelé zvykli, na základě svých zkušeností s místními prostředími. Produkční úlohy přesunout do Azure se musí integrovat s existující řešení zálohování, pokud je to možné. Nebo můžete použít [Azure Backup](../backup/backup-azure-vms-first-look-arm.md) pomáhají řešit požadavky zálohy.

Organizace, které nevynucují zásady aktualizace softwaru jsou vystaveny více hrozby, kterých zneužívají známé, dříve oprava ohrožení zabezpečení. Pro dosažení souladu s předpisy odvětví, musí společnosti prokázat, že jsou pečlivé a použití ovládacích prvků správné zabezpečení pro zajištění zabezpečení své úlohy umístěný v cloudu.

Osvědčené postupy pro tradiční datacentrum aktualizace softwaru a Azure IaaS mnoho podobností. Doporučujeme vyhodnotit vaše aktuální zásady aktualizace softwaru zahrnout virtuální počítače v Azure.

## <a name="manage-your-vm-security-posture"></a>Správa stavu zabezpečení vašich virtuálních počítačů
Vyvíjejícími se kybernetickými hrozbami. Pomáhá chránit vaše virtuální počítače se vyžaduje možnost monitorování, které můžete rychle zjišťovat hrozby, zabránit neoprávněnému přístupu k prostředkům, upozornění a snížil počet falešných poplachů.

Monitorovat stav zabezpečení vašich [Windows](../security-center/security-center-virtual-machine.md) a [virtuální počítače s Linuxem](../security-center/security-center-linux-virtual-machine.md), použijte [Azure Security Center](../security-center/security-center-intro.md). Ve službě Security Center chránit své virtuální počítače s využitím následující možnosti:

- Použijte nastavení zabezpečení operačního systému s doporučenými konfiguračními pravidly.
- Identifikujte a stáhnout systému zabezpečení a kritických aktualizací, které můžou chybět.
- Nasazení doporučení týkající se ochrany proti malwaru endpoint.
- Ověření šifrování disku.
- Posouzení a náprava ohrožení zabezpečení.
- Detekce hrozeb.

Security Center může aktivně monitorovat hrozby a potenciálních hrozeb, které jsou přístupné na výstrahy zabezpečení. Korelační hrozby se agregují v rámci jednoho zobrazení volá incidentu zabezpečení.

Security Center ukládá data v [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Log Analytics poskytuje dotazovací jazyk a analytický modul, který poskytuje přehled o fungování vašich aplikací a prostředků. Data se shromažďují také z [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md), řešení pro správu a agenti nainstalovaní na virtuálních počítačích v cloudu nebo místně. Tyto sdílené funkce umožňují, abyste si vytvořili úplný přehled o vašem prostředí.

Organizace, které nevynucují silné zabezpečení pro své virtuální počítače zůstanou vědět o potenciální pokusy o neoprávnění uživatelé obejít kontrolní mechanismy zabezpečení.

## <a name="monitor-vm-performance"></a>Monitorování výkonu virtuálních počítačů
Zneužití prostředku může být problém, když procesy virtuální počítač využívat víc prostředků, než by měly. Problémy s výkonem v případě virtuálních počítačů může vést k přerušení služby, který porušuje Princip zabezpečení dostupnosti. To je důležité zejména pro virtuální počítače, které hostují služby IIS nebo jiné webové servery, protože vysoké využití procesoru nebo paměti může značit útoku služby (DoS). Je nutné sledovat přístup k virtuálním počítačům pouze nikoli reaktivně během dochází k problému, ale také aktivně proti standardních hodnot výkonu, protože při běžném provozu.

Doporučujeme, abyste použili [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md) získat přehled o stavu vašich prostředků. Funkce monitorování Azure:

- [Soubory protokolů diagnostiky prostředků](../azure-monitor/platform/diagnostic-logs-overview.md): Sleduje vaše prostředky virtuálních počítačů a identifikuje potenciální problémy, které by mohlo ohrozit výkon a dostupnost.
- [Rozšíření Azure Diagnostics](../azure-monitor/platform/diagnostics-extension-overview.md): Poskytuje funkce pro monitorování a Diagnostika na virtuálních počítačích s Windows. Můžete povolit tyto možnosti včetně rozšíření jako součást [šablony Azure Resource Manageru](../virtual-machines/windows/extensions-diagnostics-template.md).

Organizace, které nechcete monitorovat výkon virtuálního počítače nelze určit, jestli jsou určité změny ve výkonu vzorů normálního nebo neobvyklé. Virtuální počítač, který je spotřebovávat více prostředků, než je obvyklé může znamenat útok z externího zdroje nebo ohroženými procesu spuštěného ve virtuálním počítači.

## <a name="encrypt-your-virtual-hard-disk-files"></a>Šifrování souborů virtuálního pevného disku
Doporučujeme šifrovat virtuální pevné disky (VHD) pro zvýšení ochrany spouštěcího svazku a datové svazky v úložišti, spolu s šifrovací klíče a tajné kódy.

[Azure Disk Encryption](azure-security-disk-encryption-overview.md) umožňuje šifrovat disky virtuálních počítačů Windows a Linuxem v režimu IaaS. Azure Disk Encryption používá standard odvětví v oblasti [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funkce Windows a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkce Linux zajišťuje šifrování pro operační systém a datové disky. Toto řešení je integrovaná s [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) umožňují řídit a spravovat šifrování disku klíče a tajné kódy ve vašem předplatném služby key vault. Řešení také zajišťuje, že všechna data na discích virtuálních počítačů šifrování v klidovém stavu ve službě Azure Storage.

Doporučené postupy pro používání Azure Disk Encryption jsou následující:

**Osvědčený postup**: Povolte šifrování na virtuálních počítačích.   
**Podrobnosti o**: Azure Disk Encryption generuje a zapíše šifrovací klíče do trezoru klíčů. Správa šifrovacích klíčů v trezoru klíčů se vyžaduje ověřování Azure AD. Vytvořte aplikaci Azure AD pro tento účel. Pro účely ověřování, můžete použít buď ověřování na základě tajný kód klienta nebo [ověřování klienta na základě certifikátů Azure AD](../active-directory/active-directory-certificate-based-authentication-get-started.md).

**Osvědčený postup**: Použijte šifrovací klíč klíče (KEK) pro další úroveň zabezpečení pro šifrovací klíče. Přidání KEK do trezoru klíčů.   
**Podrobnosti o**: Použití [Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) rutina pro vytvoření šifrovací klíč klíče v trezoru klíčů. Můžete také importovat KEK z vašeho místního modulu hardwarového zabezpečení (HSM) pro správu klíčů. Další informace najdete v tématu [dokumentace ke službě Key Vault](../key-vault/key-vault-hsm-protected-keys.md). Pokud je zadaný šifrovací klíč klíče, Azure Disk Encryption používá tento klíč k šifrování tajných kódů zabalení před zápisem do služby Key Vault. Uchování v úschově kopii tohoto klíče v místní správy klíčů HSM nabízí další ochranu před náhodným odstraněním klíčů.

**Osvědčený postup**: Přijmout [snímku](../virtual-machines/windows/snapshot-copy-managed-disk.md) nebo zálohování před disky jsou šifrované. Zálohování poskytuje možnost obnovení, pokud dojde k neočekávané chybě při šifrování.   
**Podrobnosti o**: Virtuální počítače se spravovanými disky vyžadují zálohu, než dojde k šifrování. Po zálohování se provádí, můžete použít **Set-AzureRmVMDiskEncryptionExtension** rutiny k šifrování spravované disky tak, že zadáte *- skipVmBackup* parametru. Další informace o tom, jak zálohování a obnovení šifrovaných virtuálních počítačů najdete v tématu [Azure Backup](../backup/backup-azure-vms-encryption.md) článku.

**Osvědčený postup**: Pokud chcete mít jistotu, že šifrování tajných kódů není překračují hranice regionální, musí Azure Disk Encryption key vault a virtuální počítače umístěné ve stejné oblasti.   
**Podrobnosti o**: Vytvoření a použití služby key vault je ve stejné oblasti jako virtuální počítač k šifrování.

Při použití Azure Disk Encryption splňujete následující obchodní potřeby:

- Virtuální počítače IaaS jsou zabezpečená při nečinnosti pomocí standardní šifrovací technologie k vyřešení organizační požadavky na zabezpečení a dodržování předpisů.
- Spuštění virtuálních počítačů IaaS pod správou zákazníka klíčů a zásad a je můžete auditovat jejich využití v trezoru klíčů.

## <a name="next-steps"></a>Další postup
Zobrazit [osvědčené postupy zabezpečení Azure a vzory](security-best-practices-and-patterns.md) pro další doporučené postupy zabezpečení, mají použít, když jste návrhu, nasazení a správa cloudových řešení pomocí služby Azure.

Jsou následující prostředky vám poskytnou další obecné informace o zabezpečení Azure a související služby Microsoftu:
* [Blog týmu Azure zabezpečení](https://blogs.msdn.microsoft.com/azuresecurity/) – aktuální informace o nejnovější vydání v Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – tam, kde mohou být hlášeny chyby zabezpečení společnosti Microsoft, včetně problémů s Azure, nebo prostřednictvím e-mailu secure@microsoft.com
