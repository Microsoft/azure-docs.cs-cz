---
title: Osvědčené postupy zabezpečení virtuální počítač Azure
description: Tento článek obsahuje celou řadu osvědčené postupy zabezpečení pro použití ve virtuálních počítačích, které jsou umístěné v Azure.
services: security
documentationcenter: na
author: barclayn
manager: mbaldwin
editor: ''
ms.assetid: 5e757abe-16f6-41d5-b1be-e647100036d8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 409ed4618b8ddf022cfc3457851cf434ba810b94
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="best-practices-for-azure-vm-security"></a>Doporučené postupy pro zabezpečení virtuálního počítače Azure

Ve většině infrastruktura jako služba (IaaS) scénáře [Azure virtuální počítače (VM)](https://docs.microsoft.com/azure/virtual-machines/) jsou hlavní zatížení pro organizace, které používají cloud computing. Tuto skutečnost je obzvláště zřejmé ve [hybridní scénáře](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) kde organizací chtít pomalu migraci úloh do cloudu. V takových scénářů, postupujte podle [obecné bezpečnostní aspekty IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)a použijte osvědčené postupy zabezpečení pro všechny virtuální počítače.

Tento článek popisuje různé osvědčené postupy zabezpečení virtuálních počítačů, všechny odvozené od našich zákazníků a vlastní přímé prostředí s virtuálními počítači.

Osvědčené postupy jsou založené na konsenzu názorů a budou pracovat s aktuální možnostech platformy Azure a sady funkcí. Protože časem změnit názory a technologie, plánujeme aktualizovat tento článek pravidelně tak, aby odrážela tyto změny.

Pro každý osvědčený postup Tento článek vysvětluje:

* Co je doporučený postup.
* Proč je vhodné ho chcete zapnout.
* Jak další povolit.
* Co může dojít, pokud se nepodaří ji povolit.
* Možné alternativy doporučený postup.

Článek prozkoumá následující osvědčené postupy zabezpečení virtuálních počítačů:

* Virtuální počítač ověřování a řízení přístupu
* Přístup k dostupnosti a sítě virtuálních počítačů
* Ochrana dat v klidovém stavu uložených ve virtuálních počítačích vynucením šifrování
* Spravovat vaše aktualizace virtuálního počítače
* Spravovat lepšímu zabezpečení virtuálního počítače
* Monitorování výkonu virtuálních počítačů

## <a name="vm-authentication-and-access-control"></a>Virtuální počítač ověřování a řízení přístupu

Prvním krokem při ochraně virtuálního počítače je zajistit, aby mohly jenom autorizovaným uživatelům nastavit nové virtuální počítače. Můžete použít [Azure zásady](../azure-policy/azure-policy-introduction.md) zavést konvence pro prostředky ve vaší organizaci, vytvářet vlastní zásady a použít tyto zásady k prostředkům, jako například [skupiny prostředků](../azure-resource-manager/resource-group-overview.md).

Virtuální počítače, které patří do skupiny prostředků přirozeně dědit jejími zásadami. Doporučujeme tuto metodu pro správu virtuálních počítačů, ale můžete taky řídit přístup ke jednotlivé zásady virtuálních počítačů pomocí [řízení přístupu na základě role (RBAC)](../role-based-access-control/role-assignments-portal.md).

Když povolíte zásady Resource Manager a RBAC pro řízení přístupu virtuálních počítačů, můžete k vylepšování celkové zabezpečení virtuálních počítačů. Doporučujeme, abyste konsolidovat virtuálních počítačů s stejný životní cyklus do stejné skupiny prostředků. Pomocí skupin prostředků, můžete nasadit, monitorování a souhrnné náklady pro vaše prostředky fakturace. Pokud chcete povolit uživatelům přístup a nastavení virtuálních počítačů, použijte [alespoň oprávnění přístupu](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models). A při přiřazení oprávnění pro uživatele, v úmyslu používat následující předdefinované role Azure:

- [Virtuální počítač Přispěvatel](../role-based-access-control/built-in-roles.md#virtual-machine-contributor): můžete spravovat virtuální počítače, ale není virtuální sítě nebo úložiště účet ke kterému jsou připojené.
- [Classic Přispěvatel virtuálních počítačů](../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor): můžete spravovat virtuální počítače vytvořené pomocí modelu nasazení classic, ale není virtuální sítě nebo úložiště účet ke kterému jsou připojené virtuální počítače.
- [Správce zabezpečení](../role-based-access-control/built-in-roles.md#security-manager): můžete spravovat součásti zabezpečení, zásady zabezpečení a virtuálních počítačů.
- [Uživatel DevTest Labs](../role-based-access-control/built-in-roles.md#devtest-labs-user): můžete zobrazit vše, co a připojení, spuštění, restartování a vypnout virtuální počítače.

Nesdílejte účty a hesla mezi správci a nemáte opakovaně používat hesla mezi více uživatelských účtů nebo služeb, zejména hesla pro sociálních médií nebo dalšími aktivitami bez oprávnění správce. V ideálním případě byste měli používat [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) šablony bezpečně nastavit virtuální počítače. Pomocí tohoto přístupu může posílit vaše volby nasazení a vynucení nastavení zabezpečení v rámci nasazení.

Organizace, které nebudou vynucovat řízení přístupu k datům díky funkcí, jako je RBAC může být jejich uživatelům udělen další oprávnění, než je nezbytné. Nevhodný uživatel přístup k určitým datům může ohrozit přímo tato data.

## <a name="vm-availability-and-network-access"></a>Přístup k dostupnosti a sítě virtuálních počítačů

Pokud virtuální počítač používá kritické aplikace, které je potřeba mít vysokou dostupnost, důrazně doporučujeme použít víc virtuálních počítačů. Pro lepší dostupnost vytvořit aspoň dva virtuální počítače v [skupinu dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md).

[Azure Vyrovnávání zatížení](../load-balancer/load-balancer-overview.md) také vyžaduje, aby Vyrovnávání zatížení sítě virtuálních počítačů patří do stejné skupiny dostupnosti. Pokud tyto virtuální počítače musí mít přístup k Internetu, musíte nakonfigurovat [nástroj pro vyrovnávání zatížení internetového](../load-balancer/load-balancer-internet-overview.md).

Když jsou virtuální počítače přístup k Internetu, je důležité, které [řízení toku provozu sítě s skupiny zabezpečení sítě (Nsg)](../virtual-network/virtual-networks-nsg.md). Vzhledem k tomu, že skupiny Nsg můžete použít na podsítě, můžete minimalizovat počet skupin Nsg svoje prostředky seskupíte podle podsítí a potom použijete skupiny Nsg na podsítě. Je cílem vytvořit úroveň izolace sítě, což lze provést tak, že správně nakonfigurujete [zabezpečení sítě](../best-practices-network-security.md) možnosti v Azure.

Také můžete funkci za běhu (JIT) virtuálních počítačů přístup z Azure Security Center určí, kdo má vzdáleného přístupu pro konkrétní virtuální počítač a jak dlouho.

Organizace, které není vynutit omezení přístup k síti na straně Internetu virtuální počítače jsou vystaveny rizika zabezpečení, jako je například k útoku hrubou silou protokol RDP (Remote Desktop).

## <a name="protect-data-at-rest-in-your-vms-by-enforcing-encryption"></a>Chránit data uložená v virtuální počítače vynucením šifrování

[Šifrování dat v klidovém stavu](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) je povinný krok směrem k suverenity data o ochraně osobních údajů a dodržování předpisů a data. [Azure Disk Encryption](../security/azure-security-disk-encryption.md) umožňuje správcům IT šifrování disků systému Windows a virtuálních počítačů IaaS Linux. Šifrování disku kombinuje funkci Windows BitLocker standardní a funkci Linux dm-crypt zajistit šifrování svazku operačního systému a datové disky.

Můžete použít šifrování disku, což zajistí ochranu dat, aby splňovaly vaše požadavky na dodržování předpisů a zabezpečení organizace. Vaše organizace měli zvážit použití šifrování pro zmírnění rizika data související s neoprávněný přístup. Doporučujeme také šifrování jednotky, než k nim napíšete citlivá data.

Ujistěte se, že šifrování dat svazků virtuálních počítačů k ochraně je uložená v účtu úložiště Azure. Zabezpečit šifrovacích klíčů a tajný klíč pomocí [Azure Key Vault](https://azure.microsoft.com/documentation/articles/key-vault-whatis/).

Organizace, které není vynuceno šifrování dat se zveřejňují více pro problémy s integritou dat. Například může neoprávněným nebo neautorizovaných serverů uživatelů odcizit data v ohrožené účty nebo získání neoprávněného přístupu k datům programového v ClearFormat. Kromě toho, s ohledem na těchto rizik, pro dosažení souladu s předpisy odvětví, společnosti musí prokázat jejich výkonu opatrností a použití ovládacích prvků správné zabezpečení pro zvýšení zabezpečení svá data.

Další informace o šifrování disku najdete v tématu [Azure Disk Encryption pro systém Windows a virtuálních počítačů IaaS Linux](azure-security-disk-encryption.md).


## <a name="manage-your-vm-updates"></a>Spravovat vaše aktualizace virtuálního počítače

Protože virtuální počítače Azure, stejně jako všechny virtuální počítače na místě, by měla být spravovaná uživatelem, Azure nemá push aktualizace systému Windows k nim. Jste, ale doporučujeme ponechat povolené automatické nastavení služby Windows Update. Další možností je nasadit [Windows Server Update Services (WSUS)](https://technet.microsoft.com/windowsserver/bb332157.aspx) nebo jiné vhodné správy aktualizací produktu buď na jiný počítač nebo místní. Služba WSUS a Windows Update zachovat virtuální počítače aktuální. Doporučujeme také ověřte, zda jsou všechny virtuální počítače IaaS aktuální pomocí prohledávání produktu.

Uložené Image poskytovaný platformou Azure se pravidelně aktualizují zahrnout nejnovější odezvy aktualizací systému Windows. Však není zaručeno, že bude aktuální v době nasazení bitové kopie. Mírné prodleva (maximálně několik týdnů) následující veřejné verze může být možné. Pro kontrolu a instalaci všech aktualizací systému Windows musí být prvním krokem každé nasazení. Tato míra je obzvláště důležité pro použití při nasazování bitových kopií, které pocházejí z vy nebo vaše vlastní knihovny. Ve výchozím nastavení se automaticky aktualizují bitové kopie, které jsou k dispozici v rámci Azure Marketplace.

Organizace, které nejsou vynutit zásady aktualizace softwaru jsou vystaveny více hrozeb, které zneužívají ohrožení zabezpečení známé, dříve pevný. Kromě riskujete tyto hrozby, abyste dosáhli souladu s předpisy odvětví, společnosti musí prokázat, že jsou výkonu opatrností a použití ovládacích prvků správné zabezpečení k zajištění zabezpečení jejich zatížení umístěný v cloudu.

Je nutné zdůraznit, že aktualizace softwaru osvědčené postupy pro tradičních datových center a Azure IaaS mají mnoho podobností. Proto doporučujeme vyhodnotit stávající zásady aktualizace softwaru a zahrnují virtuální počítače.

## <a name="manage-your-vm-security-posture"></a>Spravovat lepšímu zabezpečení virtuálního počítače

Internetový hrozeb se vyvíjejí a zabezpečení virtuální počítače vyžaduje s formátováním monitorovací schopností, které můžete rychle zjišťovat hrozby, zabránit neoprávněnému přístupu k prostředkům, výstrahy aktivovat a snížil počet falešných poplachů. Postavení zabezpečení pro taková zatížení zahrnuje všechny aspekty zabezpečení virtuálního počítače ze správy aktualizací zabezpečit přístup k síti.

Ke sledování postavení zabezpečení vaší [Windows](../security-center/security-center-virtual-machine.md) a [virtuální počítače s Linuxem](../security-center/security-center-linux-virtual-machine.md), použijte [Azure Security Center](../security-center/security-center-intro.md). V Azure Security Center chrání virtuální počítače díky následující možnosti:

* Použít nastavení zabezpečení operačního systému s doporučenou konfiguraci pravidla
* Identifikovat a stáhnout systému zabezpečení a důležité aktualizace, které může být chybějící
* Nasazení doporučení ochrany proti malwaru Endpoint
* Ověření šifrování disku
* Hodnocení a opravu chyb zabezpečení
* Detekce hrozeb

Security Center můžete sledovat aktivně hrozby a potenciální hrozby jsou viditelné v rámci **výstrahy zabezpečení**. Korelační hrozeb jsou agregován v rámci jednoho zobrazení názvem **incidentu zabezpečení**.

Abyste pochopili, jak Security Center můžete identifikovat potenciální hrozby v virtuální počítače umístěné v Azure, v následujícím videu:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response/player]

Organizace, které nevynucují postavení silné zabezpečení pro jejich virtuální počítače zůstanou nebere v úvahu potenciální pokusů neoprávnění uživatelé k obcházení ovládací prvky zavedené zabezpečení.

## <a name="monitor-vm-performance"></a>Monitorování výkonu virtuálních počítačů

Zneužití prostředku může být problém, když počítač procesy spotřebovávají více prostředků, než by měly. Problémy s výkonem se virtuální počítač může vést k přerušení služby, která porušuje zásadu zabezpečení dostupnosti. Z tohoto důvodu je nutné k monitorování virtuálních počítačů přístupu není pouze reaktivně při problému dochází, ale také proaktivně proti základní výkon naměřenou při běžném provozu.

Analýzou [soubory protokolů Azure diagnostiky](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/), můžete sledovat vaše prostředky virtuálních počítačů a identifikovat potenciální problémy, které může dojít k ohrožení výkon a dostupnost. Rozšíření diagnostiky Azure nabízí funkce monitorování a Diagnostika na virtuálních počítačích se systémem Windows. Tyto možnosti můžete povolit jako součást, přiložením rozšíření [šablony Azure Resource Manageru](../virtual-machines/windows/extensions-diagnostics-template.md).

Můžete také použít [Azure monitorování](../monitoring-and-diagnostics/monitoring-overview-metrics.md) získat přehled o stavu vaší prostředků.

Organizace, které nejsou sledovat výkon virtuálního počítače není možné určit, jestli jsou některé změny v vzory výkonu normální nebo neobvyklé. Pokud virtuální počítač je spotřebovávat více prostředků, než je obvyklé, takové anomálií by to znamenat potenciální útoky z externí prostředek nebo ohroženými proces, který běží ve virtuálním počítači.
