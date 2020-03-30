---
title: Funkce zabezpečení používané s virtuálními počítači Azure
titleSuffix: Azure security
description: Tento článek obsahuje přehled základních funkcí zabezpečení Azure, které se dá používat s virtuálními počítači Azure.
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
ms.openlocfilehash: a1726e18ea8c1ba86d77d7b9ca3d50c444620361
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657159"
---
# <a name="azure-virtual-machines-security-overview"></a>Přehled zabezpečení virtuálních počítačů Azure
Tento článek obsahuje přehled základních funkcí zabezpečení Azure, které lze použít s virtuálními počítači.

Virtuální počítače Azure můžete použít k agilnímu nasazení široké škály výpočetních řešení. Služba podporuje Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP a Azure BizTalk Services. Takže můžete nasadit libovolné úlohy a libovolný jazyk na téměř jakýkoli operační systém.

Virtuální počítač Azure vám nabídne flexibilitu virtualizace bez nutnosti zakoupení a údržby fyzického hardwaru, na kterém virtuální počítač běží. Můžete vytvářet a nasazovat aplikace s jistotou, že vaše data jsou chráněná a bezpečná ve vysoce zabezpečených datových centrech.

S Azure můžete vytvářet řešení s rozšířeným zabezpečením, která splňují požadavky na zabezpečení, která:

* Chraňte své virtuální počítače před viry a malwarem.
* Šifrujte citlivá data.
* Zabezpečený síťový provoz.
* Identifikovat a detekovat hrozby.
* Splňte požadavky na dodržování předpisů.  

## <a name="antimalware"></a>Antimalware

V Azure můžete používat antimalwarový software od dodavatelů zabezpečení, jako jsou Microsoft, Symantec, Trend Micro a Kaspersky. Tento software pomáhá chránit vaše virtuální počítače před škodlivými soubory, adwarem a dalšími hrozbami.

Microsoft Antimalware pro Cloud Services azure a virtuální počítače je funkce ochrany v reálném čase, která pomáhá identifikovat a odstranit viry, spyware a další škodlivý software.  Microsoft Antimalware pro Azure poskytuje konfigurovatelné výstrahy, když se známý škodlivý nebo nežádoucí software pokusí nainstalovat sám nebo spustit ve vašich systémech Azure.

Microsoft Antimalware pro Azure je řešení pro jednoho agenta pro aplikace a prostředí tenantů. Je navržen tak, aby běžel v pozadí bez lidského zásahu. Ochranu můžete nasadit na základě potřeb úloh aplikace pomocí základní vlastní konfigurace zabezpečené ve výchozím nastavení nebo pokročilé vlastní konfigurace, včetně antimalwarového monitorování.

Přečtěte si další informace o [Microsoft Antimalware pro Azure](antimalware.md) a základních dostupných funkcích.

Přečtěte si další informace o antimalwarovém softwaru, který pomáhá chránit vaše virtuální počítače:

* [Nasazování antimalwarových řešení na virtuálních počítačích Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Jak nainstalovat a nakonfigurovat Trend Micro Deep Security jako službu na virtuálním počítači se systémem Windows](/azure/virtual-machines/windows/classic/install-trend)
* [Jak nainstalovat a nakonfigurovat službu Symantec Endpoint Protection na virtuálním počítači se systémem Windows](/azure/virtual-machines/windows/classic/install-symantec)
* [Řešení zabezpečení na Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

Pro ještě výkonnější ochranu zvažte použití [programu Windows Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection). S programem Atp programu Windows Defender získáte:

* [Redukce povrchu útoku](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Ochrana nové generace](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Ochrana koncových bodů a odezva](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Automatizované vyšetřování a náprava](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Bezpečné skóre](/windows/security/threat-protection/microsoft-defender-atp/configuration-score)
* [Pokročilý lov](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Správa a API](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Ochrana před hrozbami společnosti Microsoft](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

Další informace:

* [Začínáme s WDATP](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [Přehled funkcí WDATP](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Modul hardwarového zabezpečení

Zlepšení zabezpečení klíčů může zvýšit ochranu šifrování a ověřování. Můžete zjednodušit správu a zabezpečení vašich důležitých tajných klíčů a klíčů jejich uložením v azure key vault.

Trezor klíčů nabízí možnost ukládat klíče do modulů hardwarového zabezpečení (HSM) certifikovaných podle standardů FIPS 140-2 Level 2. Šifrovací klíče serveru SQL Server pro zálohování nebo [transparentní šifrování dat](https://msdn.microsoft.com/library/bb934049.aspx) mohou být uloženy v trezoru klíčů s libovolnými klíči nebo tajnými klíči z vašich aplikací. Oprávnění a přístup k těmto chráněným položkám se spravují prostřednictvím [služby Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Další informace:

* [Co je Azure Key Vault?](/azure/key-vault/key-vault-overview)
* [Blog o úložišti klíčů Azure](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Šifrování disku virtuálního počítače

Azure Disk Encryption je nová funkce pro šifrování disků virtuálních počítačů s Windows a Linuxem. Azure Disk Encryption používá standardní funkci [Nástroje bitlocker](https://technet.microsoft.com/library/cc732774.aspx) u Windows a funkci [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) linuxu k zajištění šifrování svazku pro operační systém a datové disky.

Řešení je integrované s Azure Key Vault, které vám pomohou řídit a spravovat šifrovací klíče a tajné kódy disku v předplatném trezoru klíčů. Zajišťuje, že všechna data na discích virtuálního počítače jsou šifrována v klidovém stavu ve službě Azure Storage.

Další informace:

* [Šifrování disku Azure pro virtuální počítače IaaS](/azure/security/azure-security-disk-encryption-overview)
* [Rychlý start: Šifrování virtuálního počítače IaaS s Windows pomocí Azure PowerShellu](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Zálohování virtuálního počítače

Azure Backup je škálovatelné řešení, které pomáhá chránit data aplikací s nulovými kapitálovými investicemi a minimálními provozními náklady. Chyby aplikace mohou poškodit vaše data a lidské chyby mohou zavádět chyby do vašich aplikací. S Azure Backup jsou chráněné vaše virtuální počítače se systémem Windows a Linux.

Další informace:

* [Co je Azure Backup?](/azure/backup/backup-introduction-to-azure-backup)
* [Nejčastější dotazy ke službě Azure Backup](/azure/backup/backup-azure-backup-faq)

## <a name="azure-site-recovery"></a>Azure Site Recovery

Důležitou součástí strategie BCDR vaší organizace je přijít na to, jak udržet podnikové úlohy a aplikace spuštěné, když dojde k plánovaným a neplánovaným výpadkům. Azure Site Recovery pomáhá organizovat replikaci, převzetí služeb při selhání a obnovení úloh a aplikací tak, aby byly dostupné ze sekundárního umístění, pokud dojde k primárnímu umístění.

Obnovení webu:

* **Zjednodušuje strategii BCDR:** Obnovení webu usnadňuje zpracování replikace, převzetí služeb při selhání a obnovení více obchodních úloh a aplikací z jednoho umístění. Site Recovery orchestruje replikaci a převzetí služeb při selhání, ale nezachycuje data aplikace ani o nich nemá žádné informace.
* **Poskytuje flexibilní replikaci**: Pomocí site recovery můžete replikovat úlohy spuštěné na virtuálních počítačích Hyper-V, virtuálních počítačích VMware a fyzických serverech Windows/Linux.
* **Podporuje převzetí služeb při selhání a obnovení**: Obnovení webu poskytuje zkušební převzetí služeb při selhání pro podporu cvičení zotavení po havárii bez ovlivnění produkčních prostředí. Pro očekávané výpadky je možné spouštět plánovaná převzetí služeb při selhání bez ztráty dat. V případě neočekávaných havárií pak mohou proběhnout neplánovaná převzetí služeb s minimálními ztrátami dat (podle četnosti replikací). Po převzetí služeb při selhání můžete vrátit zpět do primárních lokalit. Site Recovery poskytuje plány obnovení, které mohou obsahovat skripty a sešity automatizace Azure, což vám umožní přizpůsobit si přebírání služeb při selhání a obnovování vícevrstvých aplikací.
* **Eliminuje sekundární datová centra**: Můžete replikovat do sekundární místní lokality nebo do Azure. Použití Azure jako cíle pro zotavení po havárii eliminuje náklady a složitost údržby sekundární lokality. Replikovaná data se ukládají ve službě Azure Storage.
* **Integruje se se stávajícími technologiemi BCDR:** Partneři site recovery s funkcemi BCDR jiných aplikací. Například můžete použít site recovery k ochraně SQL Server back-end podnikových úloh. To zahrnuje nativní podporu pro SQL Server vždy zapnutý pro správu převzetí služeb při selhání skupin dostupnosti.

Další informace:

* [Co je Azure Site Recovery?](/azure/site-recovery/site-recovery-overview)
* [Jak funguje Azure Site Recovery?](/azure/site-recovery/site-recovery-components)
* [Jaké úlohy jsou chráněné azure site recovery?](/azure/site-recovery/site-recovery-workload)

## <a name="virtual-networking"></a>Virtuální síť

Virtuální počítače potřebují připojení k síti. Pro podporu tohoto požadavku Azure vyžaduje, aby se virtuální počítače připojily k virtuální síti Azure.

Virtuální síť Azure je logická konstrukce postavená na fyzické síťové struktuře Azure. Každá logická virtuální síť Azure je izolovaná od všech ostatních virtuálních sítí Azure. Tato izolace pomáhá zajistit, že síťový provoz ve vašich nasazeních není přístupný ostatním zákazníkům Microsoft Azure.

Další informace:

* [Přehled zabezpečení sítě Azure](network-overview.md)
* [Přehled služby Virtual Network](/azure/virtual-network/virtual-networks-overview)
* [Síťové funkce a partnerství pro podnikové scénáře](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Správa a vykazování zásad zabezpečení

Azure Security Center pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně. Security Center vám poskytuje lepší přehled o zabezpečení vašich prostředků Azure a kontrolu nad tím, že nad tím máte kontrolu. Poskytuje integrované monitorování zabezpečení a správu zásad napříč vašimi předplatnými Azure. Pomáhá odhalovat hrozby, které by jinak mohly zůstat bez povšimnutí, a spolupracuje s širokým ekosystémem bezpečnostních řešení.

Security Center vám pomůže optimalizovat a sledovat zabezpečení vašich virtuálních počítačů:

* Poskytování [doporučení zabezpečení](/azure/security-center/security-center-recommendations) pro virtuální počítače. Příklady doporučení: použít aktualizace systému, konfigurovat koncové body ACLs, povolit antimalwar, povolit skupiny zabezpečení sítě a použít šifrování disku.
* Sledování stavu virtuálních počítačů.

Další informace:

* [Úvod do Azure Security Center](/azure/security-center/security-center-intro)
* [Nejčastější dotazy centra zabezpečení Azure](/azure/security-center/security-center-faq)
* [Plánování a provoz Azure Security Center](/azure/security-center/security-center-planning-and-operations-guide)

## <a name="compliance"></a>Dodržování předpisů

Virtuální počítače Azure jsou certifikované pro FISMA, FedRAMP, HIPAA, PCI DSS level 1 a další klíčové programy dodržování předpisů. Tato certifikace usnadňuje vašim vlastním aplikacím Azure splnění požadavků na dodržování předpisů a vaší firmě řešení široké škály domácích i mezinárodních regulačních požadavků.

Další informace:

* [Centrum zabezpečení microsoftu: Dodržování předpisů](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Trusted Cloud: Microsoft Azure zabezpečení, ochrana osobních údajů a dodržování předpisů](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Důvěrné výpočetní techniky

Zatímco důvěrné výpočty není technicky součástí zabezpečení virtuálních počítačů, téma zabezpečení virtuálních počítačů patří k vyšší úrovni předmětu "výpočetní" zabezpečení. Důvěrné výpočty patří do kategorie "výpočetní" zabezpečení.

Důvěrné výpočty zajišťují, že když jsou data "v jasném", což je vyžadováno pro efektivní zpracování, jsou data chráněna uvnitř důvěryhodného prostředí https://en.wikipedia.org/wiki/Trusted_execution_environment pro spuštění (TEE - označované také jako enkláva), jejíž příklad je znázorněn na obrázku níže.  

TEEs zajistit, že neexistuje žádný způsob, jak zobrazit data nebo operace uvnitř zvenčí, a to i s ladicím programem. Dokonce zajišťují, že pouze autorizovaný kód je povolen pro přístup k datům. Pokud je kód změněn nebo zfalšován, operace jsou odepřeny a prostředí zakázáno. TEE vynucuje tyto ochrany v průběhu provádění kódu v něm.

Další informace:

* [Představujeme azure důvěrné výpočetní techniky](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Azure důvěrné výpočetní techniky](https://azure.microsoft.com/blog/azure-confidential-computing/)  

## <a name="next-steps"></a>Další kroky

Přečtěte si o [doporučených postupech zabezpečení](iaas.md) pro virtuální počítače a operační systémy.
