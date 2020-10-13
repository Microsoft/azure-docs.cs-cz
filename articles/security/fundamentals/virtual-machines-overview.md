---
title: Funkce zabezpečení používané s virtuálními počítači Azure
titleSuffix: Azure security
description: Tento článek obsahuje přehled základních funkcí zabezpečení Azure, které se dají používat s Azure Virtual Machines.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/2/2019
ms.author: terrylan
ms.openlocfilehash: d2862d5ab2c42ebdd1787022dc86119bc4e0f596
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86229359"
---
# <a name="azure-virtual-machines-security-overview"></a>Přehled zabezpečení služby Azure Virtual Machines
Tento článek obsahuje přehled základních funkcí zabezpečení Azure, které je možné používat s virtuálními počítači.

Můžete použít Azure Virtual Machines k nasazení široké škály výpočetních řešení, a to agilním způsobem. Služba podporuje Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP a Azure BizTalk Services. Takže můžete nasadit jakékoli zatížení a libovolný jazyk na téměř jakýkoli operační systém.

Virtuální počítač Azure vám nabídne flexibilitu virtualizace bez nutnosti zakoupení a údržby fyzického hardwaru, na kterém virtuální počítač běží. Můžete sestavovat a nasazovat aplikace s jistotou, že jsou vaše data chráněná a bezpečná ve vysoce zabezpečených datových centrech.

S Azure můžete vytvářet řešení kompatibilní se zabezpečením, která splňují požadavky:

* Chraňte své virtuální počítače před viry a malwarem.
* Šifrování citlivých dat.
* Zabezpečte síťový provoz.
* Identifikujte a zjišťují hrozby.
* Splnění požadavků na dodržování předpisů.  

## <a name="antimalware"></a>antimalware

S Azure můžete použít antimalwarový software od dodavatelů zabezpečení, jako jsou Microsoft, Symantec, Trend Micro a Kaspersky. Tento software pomáhá chránit vaše virtuální počítače před škodlivými soubory, adwarem a dalšími hrozbami.

Microsoft Antimalware pro Azure Cloud Services a Virtual Machines je schopnost ochrany v reálném čase, která pomáhá identifikovat a odebírat viry, spyware a další škodlivý software.  Microsoft Antimalware pro Azure poskytuje konfigurovatelné výstrahy, když se známý škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit v systémech Azure.

Microsoft Antimalware pro Azure je jediného agenta pro aplikace a klientská prostředí. Je navržena tak, aby běžela na pozadí bez zásahu člověka. Ochranu můžete nasadit v závislosti na potřebách úloh aplikací, a to buď pomocí základní zabezpečené, nebo pokročilé vlastní konfigurace, včetně antimalwarového monitorování.

Přečtěte si další informace o [programu Microsoft Antimalware pro Azure](antimalware.md) a dostupných základních funkcích.

Přečtěte si další informace o antimalwarovém softwaru, které vám pomůžou ochránit vaše virtuální počítače:

* [Nasazování antimalwarových řešení na virtuálních počítačích Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Postup instalace a konfigurace Trend Micro hlubokého zabezpečení jako služby na virtuálním počítači s Windows](/azure/virtual-machines/windows/classic/install-trend)
* [Jak nainstalovat a nakonfigurovat Symantec Endpoint Protection na virtuálním počítači s Windows](/azure/virtual-machines/windows/classic/install-symantec)
* [Řešení zabezpečení v Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

V případě ještě výkonnější ochrany zvažte použití [rozšířené ochrany před internetovými útoky v programu Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection). V rámci ochrany ATP v programu Windows Defender získáte:

* [Omezení možností útoku](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Ochrana nové generace](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Endpoint Protection a odpověď](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Automatizované šetření a náprava](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Bezpečnostní skóre](/windows/security/threat-protection/microsoft-defender-atp/tvm-microsoft-secure-score-devices)
* [Pokročilé lov](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Správa a rozhraní API](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Microsoft Threat Protection](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

Další informace:

* [Začínáme s rozšířená](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [Přehled schopností rozšířená](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Modul hardwarového zabezpečení

Vylepšení zabezpečení klíčů může zlepšit ochranu šifrování a ověřování. Můžete zjednodušit správu a zabezpečení důležitých tajných kódů a klíčů jejich uložením do Azure Key Vault.

Key Vault poskytuje možnost ukládat klíče v modulech hardwarového zabezpečení (HSM) s certifikací podle standardů FIPS 140-2 úrovně 2. Šifrovací klíče SQL Server pro zálohování nebo [transparentní šifrování dat](https://msdn.microsoft.com/library/bb934049.aspx) je možné ukládat do Key Vault pomocí jakýchkoli klíčů a tajných kódů z vašich aplikací. Oprávnění a přístup k těmto chráněným položkám se spravují prostřednictvím [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Další informace:

* [Co je Azure Key Vault?](/azure/key-vault/key-vault-overview)
* [Blog Azure Key Vault](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Šifrování disku virtuálního počítače

Azure Disk Encryption je nová možnost pro šifrování disků virtuálních počítačů se systémem Windows a Linux. Azure Disk Encryption používá standardní funkci [nástroje BitLocker](https://technet.microsoft.com/library/cc732774.aspx) systému Windows a funkci [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) systému Linux k poskytování šifrování svazku pro operační systém a datové disky.

Řešení je integrované s Azure Key Vault, které vám pomůžou řídit a spravovat klíče a tajné klíče pro šifrování disků v předplatném trezoru klíčů. Zajišťuje, aby všechna data v discích virtuálních počítačů byla v klidovém stavu zašifrovaná v Azure Storage.

Další informace:

* [Azure Disk Encryption pro virtuální počítače s IaaS](/azure/security/azure-security-disk-encryption-overview)
* [Rychlý start: Šifrování virtuálního počítače IaaS s Windows pomocí Azure PowerShellu](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Záloha virtuálního počítače

Azure Backup je škálovatelné řešení, které pomáhá chránit data vaší aplikace pomocí nulové kapitálové investice a minimální provozní náklady. Chyby aplikací můžou poškodit vaše data a lidské chyby můžou do vašich aplikací zavádět chyby. Díky Azure Backup jsou virtuální počítače se systémem Windows a Linux chráněny.

Další informace:

* [Co je Azure Backup?](/azure/backup/backup-introduction-to-azure-backup)
* [Nejčastější dotazy k Azure Backup službě](/azure/backup/backup-azure-backup-faq)

## <a name="azure-site-recovery"></a>Azure Site Recovery

Důležitou součástí strategie BCDR vaší organizace je zjistit, jak se mají udržovat firemní úlohy a aplikace spuštěné v případě, že dojde k plánovaným a neplánovaným výpadkům. Azure Site Recovery pomáhá orchestrovat replikaci, převzetí služeb při selhání a obnovení úloh a aplikací tak, aby byly k dispozici ze sekundárního umístění, pokud dojde k výpadku vašeho primárního umístění.

Site Recovery:

* **Zjednodušuje strategii BCDR**: Site Recovery usnadňuje zpracování replikace, převzetí služeb při selhání a obnovení více podnikových úloh a aplikací z jednoho místa. Site Recovery orchestruje replikaci a převzetí služeb při selhání, ale nezachycuje data aplikací ani informace o nich.
* **Poskytuje flexibilní replikaci**: pomocí Site Recovery můžete replikovat úlohy běžící na virtuálních počítačích s technologií Hyper-V, virtuálních počítačích VMware a fyzických serverech s Windows nebo Linux.
* **Podporuje převzetí služeb při selhání a obnovení**: Site Recovery poskytuje testovací převzetí služeb při selhání pro podporu zotavení po havárii bez ovlivnění produkčních prostředí. Pro očekávané výpadky je možné spouštět plánovaná převzetí služeb při selhání bez ztráty dat. V případě neočekávaných havárií pak mohou proběhnout neplánovaná převzetí služeb s minimálními ztrátami dat (podle četnosti replikací). Po převzetí služeb při selhání můžete provést navrácení služeb po obnovení do primární lokality. Site Recovery poskytuje plány obnovení, které mohou obsahovat skripty a sešity automatizace Azure, což vám umožní přizpůsobit si přebírání služeb při selhání a obnovování vícevrstvých aplikací.
* **Eliminuje sekundární datová centra**: můžete replikovat do sekundární místní lokality nebo do Azure. Použití Azure jako cíle pro zotavení po havárii eliminuje náklady a složitost údržby sekundární lokality. Replikovaná data jsou uložená v Azure Storage.
* **Integruje se se stávajícími technologiemi BCDR**: Site Recovery partneři s funkcemi BCDR aplikací. Můžete například použít Site Recovery k ochraně SQL Server back-endu podnikových úloh. Zahrnuje to i nativní podporu SQL Server vždy pro správu převzetí služeb skupin dostupnosti při selhání.

Další informace:

* [Co je Azure Site Recovery?](/azure/site-recovery/site-recovery-overview)
* [Jak funguje Azure Site Recovery?](/azure/site-recovery/site-recovery-components)
* [Jaké úlohy jsou chráněny pomocí Azure Site Recovery?](/azure/site-recovery/site-recovery-workload)

## <a name="virtual-networking"></a>Virtuální síť

Virtuální počítače potřebují síťové připojení. Pro podporu tohoto požadavku Azure vyžaduje, aby virtuální počítače byly připojené k virtuální síti Azure.

Virtuální síť Azure je logická konstrukce, která je postavená na fyzických prostředcích infrastruktury sítě Azure. Každá logická virtuální síť Azure je izolovaná od všech ostatních virtuálních sítí Azure. Tato izolace vám pomůže zajistit, že síťový provoz ve vašich nasazeních není přístupný ostatním Microsoft Azurem zákazníkům.

Další informace:

* [Přehled zabezpečení sítě Azure](network-overview.md)
* [Přehled Virtual Network](/azure/virtual-network/virtual-networks-overview)
* [Síťové funkce a partnerství pro podnikové scénáře](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Správa zásad zabezpečení a vytváření sestav

Azure Security Center pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně. Security Center poskytuje lepší přehled o zabezpečení prostředků Azure a kontrolu nad nimi. Poskytuje integrované monitorování zabezpečení a správu zásad napříč předplatnými Azure. Pomáhá detekovat hrozby, které by jinak neinformovaly, a spolupracuje s širokou ekosystémem řešení zabezpečení.

Security Center vám pomůže optimalizovat a monitorovat zabezpečení virtuálních počítačů pomocí:

* Poskytnutí [doporučení zabezpečení](/azure/security-center/security-center-recommendations) pro virtuální počítače. Příklad doporučení: použít aktualizace systému, konfigurovat koncové body seznamů ACL, povolit antimalware, povolit skupiny zabezpečení sítě a použít šifrování disku.
* Monitorování stavu virtuálních počítačů.

Další informace:

* [Úvod do Azure Security Center](/azure/security-center/security-center-intro)
* [Azure Security Center nejčastějších dotazech](/azure/security-center/security-center-faq)
* [Azure Security Center plánování a provozu](/azure/security-center/security-center-planning-and-operations-guide)

## <a name="compliance"></a>Dodržování předpisů

Azure Virtual Machines je certifikovaný pro FISMA, FedRAMP, HIPAA, PCI DSS úrovně 1 a další klíčové programy pro dodržování předpisů. Tato certifikace usnadňuje vašim vlastním aplikacím Azure, aby splnily požadavky na dodržování předpisů a pro vaši firmu, aby vyplnily široké spektrum vnitrostátních a mezinárodních regulativních předpisů.

Další informace:

* [Centrum zabezpečení Microsoftu: dodržování předpisů](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Důvěryhodný cloud: Microsoft Azure zabezpečení, ochrany osobních údajů a dodržování předpisů](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Důvěrné výpočetní operace

I když důvěrné výpočetní operace není technicky součástí zabezpečení virtuálních počítačů, téma zabezpečení virtuálních počítačů patří do předmětu vyšší úrovně zabezpečení Compute. Důvěrné výpočetní prostředky patří do kategorie zabezpečení Compute.

Důvěrné výpočetní operace zajišťuje, že pokud jsou data "v jasném formátu", který je vyžadován pro efektivní zpracování, data jsou chráněna v prostředí s důvěryhodným spouštěním  https://en.wikipedia.org/wiki/Trusted_execution_environment (TEE-také označované jako enklávy), což je příklad, který je znázorněn na následujícím obrázku.  

TEEs zajistěte, aby neexistoval žádný způsob, jak zobrazit data ani operace uvnitř vně, ani pomocí ladicího programu. Ještě zajistí, že přístup k datům je povolen pouze autorizovaným kódem. Pokud dojde ke změně nebo poškození kódu, operace jsou zamítnuté a prostředí je zakázané. TEE vynutila tyto ochrany v průběhu provádění kódu v rámci IT.

Další informace:

* [Představujeme důvěrný výpočetní výkon Azure](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Důvěrné výpočetní operace Azure](https://azure.microsoft.com/blog/azure-confidential-computing/)  

## <a name="next-steps"></a>Další kroky

Seznamte se s [osvědčenými postupy zabezpečení](iaas.md) pro virtuální počítače a operační systémy.
