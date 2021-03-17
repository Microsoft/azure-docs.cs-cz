---
title: Zotavení po havárii pro místní aplikace pomocí Azure Site Recovery
description: Popisuje úlohy, které se dají chránit pomocí zotavení po havárii se službou Azure Site Recovery.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 2b901425a0020c0ccc7b834ee36d965910028018
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80062840"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>Informace o zotavení po havárii pro místní aplikace

Tento článek popisuje místní úlohy a aplikace, které můžete chránit při zotavení po havárii pomocí služby [Azure Site Recovery](site-recovery-overview.md) .

## <a name="overview"></a>Přehled

Organizace potřebují strategii pro provozní kontinuitu a zotavení po havárii (BCDR), která zajistí, aby byly úlohy a data bezpečně a dostupné během plánovaných a neplánovaných výpadků. A obnovit běžné pracovní podmínky.

Site Recovery je služba Azure, která přispívá ke strategii BCDR. S využitím služby Site Recovery můžete nasadit replikaci s ohledem na aplikace do cloudu nebo do sekundární sítě. Site Recovery můžete použít ke správě replikace, testování zotavení po havárii a spuštění převzetí služeb při selhání a navrácení služeb po obnovení. Vaše aplikace můžou běžet na počítačích se systémem Windows nebo Linux, na fyzických serverech, VMware nebo Hyper-V.

Site Recovery se integruje s aplikacemi Microsoftu, jako jsou SharePoint, Exchange, Dynamics, SQL Server a Active Directory. Microsoft úzce spolupracuje s předními dodavateli včetně Oracle, SAP a Red Hat. Řešení replikace můžete přizpůsobit na bázi jednotlivých aplikací.

## <a name="why-use-site-recovery-for-application-replication"></a>Proč pro replikaci aplikací používat Site Recovery?

Site Recovery pro ochranu a obnovení na úrovni aplikací přináší následující:

- App-nezávislá a poskytuje replikaci pro všechny úlohy běžící v podporovaném počítači.
- Téměř synchronní replikace s cíli bodu obnovení (RPO) po dobu 30 sekund, aby splňovala požadavky nejdůležitějších obchodních aplikací.
- Snímky konzistentní s jednovrstvými i vícevrstvými aplikacemi
- Integrace s SQL Server AlwaysOn a partnerství s dalšími technologiemi replikace na úrovni aplikace. Například Active Directory Replication, SQL AlwaysOn a skupiny dostupnosti databáze Exchange (DAG).
- Flexibilní plány obnovení, které vám umožní obnovit celý zásobník aplikací jediným kliknutím a zahrnout do plánu externí skripty a ručně prováděné akce.
- Pokročilá správa sítě v Site Recovery a Azure pro zjednodušení požadavků na aplikační síť. Správa sítě, jako je možnost rezervace IP adres, konfigurace vyrovnávání zatížení a integrace s Azure Traffic Manager pro přepínání sítě s nízkou dobou obnovení (RTO).
- Bohatá automatizační knihovna obsahující předpřipravené skripty specifické pro aplikace, které je možné stáhnout a integrovat do plánů obnovení

## <a name="workload-summary"></a>Souhrn úloh

Site Recovery dokáže replikovat jakoukoli aplikaci spuštěnou na podporovaném počítači. Spolupracujeme s produktovým týmem a provedeme další testování pro aplikace, které jsou uvedené v následující tabulce.

| **Úloha** |**Replikace virtuálních počítačů Azure do Azure** |**Replikace virtuálních počítačů Hyper-V do sekundární lokality** | **Replikace virtuálních počítačů Hyper-V do Azure** | **Replikace virtuálních počítačů VMware do sekundární lokality** | **Replikace virtuálních počítačů VMware do Azure** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |Ano |Ano |Ano |Ano |Ano|
| Webové aplikace (IIS, SQL) |Ano |Ano |Ano |Ano |Ano|
| System Center Operations Manager |Ano |Ano |Ano |Ano |Ano|
| SharePoint |Ano |Ano |Ano |Ano |Ano|
| SAP<br/><br/>Replikace webu SAP do Azure k neclusterovému použití |Ano (testováno společností Microsoft) |Ano (testováno společností Microsoft) |Ano (testováno společností Microsoft) |Ano (testováno společností Microsoft) |Ano (testováno společností Microsoft)|
| Exchange (ne DAG) |Ano |Ano |Ano |Ano |Ano|
| Vzdálená plocha/VDI |Ano |Ano |Ano |Ano |Ano|
| Linux (operační systém a aplikace) |Ano (testováno společností Microsoft) |Ano (testováno společností Microsoft) |Ano (testováno společností Microsoft) |Ano (testováno společností Microsoft) |Ano (testováno společností Microsoft)|
| Dynamics AX |Ano |Ano |Ano |Ano |Ano|
| Souborový server systému Windows |Ano |Ano |Ano |Ano |Ano|
| Citrix XenApp a XenDesktop |Ano|Není k dispozici |Ano |Není k dispozici |Ano |

## <a name="replicate-active-directory-and-dns"></a>Replikace služby Active Directory a DNS

Pro většinu firemních aplikací má zásadní význam infrastruktura Active Directory a DNS. Během zotavení po havárii budete muset tyto součásti infrastruktury chránit a obnovit před tím, než obnovíte úlohy a aplikace.

Pomocí Site Recovery můžete pro Active Directory a DNS vytvořit úplný automatizovaný plán zotavení po havárii. Pokud například chcete převzít služby SharePoint a SAP z primární na sekundární lokalitu, můžete nastavit plán obnovení, který nejprve převezme služby Active Directory za službu. Pak použijte další plán obnovení konkrétní aplikace a převzetí služeb při selhání jinými aplikacemi, které spoléhají na službu Active Directory.

[Přečtěte si další informace](site-recovery-active-directory.md) o zotavení po havárii pro Active Directory a DNS.

## <a name="protect-sql-server"></a>Ochrana SQL Serveru

SQL Server poskytuje základ datových služeb pro mnoho obchodních aplikací v místním datacentru. Site Recovery lze použít s SQL Server technologie HA/DR k ochraně vícevrstvých podnikových aplikací, které používají SQL Server.

Site Recovery poskytuje následující:

- Jednoduché a nákladově efektivní řešení zotavení po havárii pro SQL Server; možnost replikovat různé verze a edice samostatných serverů a clusterů SQL Server do Azure nebo do sekundární lokality
- Integrace se skupinami dostupnosti SQL AlwaysOn pro správu převzetí služeb při selhání a jejich navrácení po obnovení na základě plánů obnovení Azure Site Recovery
- Komplexní plány obnovení pro všechny vrstvy v aplikaci, včetně databází na SQL Serveru
- Škálování SQL Server pro zatížení ve _špičce pomocí Site Recovery_ je rozIaaS do větších velikostí virtuálních počítačů v Azure.
- Snadné testování zotavení SQL Serveru po havárii: Můžete spouštět testy převzetí služeb při selhání s cílem analyzovat data a kontrolovat dodržování předpisů, aniž by to mělo dopad na produkční prostředí.

[Přečtěte si další informace](site-recovery-sql.md) o zotavení po havárii pro SQL Server.

## <a name="protect-sharepoint"></a>Ochrana SharePointu

Azure Site Recovery pomáhá chránit nasazení SharePointu následujícím způsobem:

- Eliminuje nutnost zajistit infrastrukturu (což by s sebou neslo náklady) pro záložní farmu k zotavení po havárii. Pomocí Site Recovery můžete replikovat celou farmu (web, aplikaci a databázové vrstvy) do Azure nebo do sekundární lokality.
- Zjednodušuje nasazení a správu aplikace. Aktualizace nasazené do primární lokality se automaticky replikují. Aktualizace jsou k dispozici po převzetí služeb při selhání a obnovení farmy v sekundární lokalitě. Snižuje složitost správy a náklady spojené s udržováním samostatné farmy v aktuálním stavu.
- Usnadňuje vývoj a testování aplikací SharePointu díky tomu, že je možné na vyžádání vytvořit repliku produkčního prostředí pro účely testování a ladění.
- Zjednodušuje přechod na cloud tím, že se pomocí Site Recovery provede migrace nasazení SharePointu do Azure.

[Přečtěte si další informace](site-recovery-sharepoint.md) o zotavení po havárii pro SharePoint.

## <a name="protect-dynamics-ax"></a>Ochrana Dynamics AX

Azure Site Recovery zvyšuje ochranu vašeho řešení Dynamics AX ERP tímto způsobem:

- Správa replikace celého prostředí Dynamics AX (webová a AOS vrstva, databázové vrstvy, SharePoint) do Azure nebo do sekundární lokality.
- Zjednodušení migrace nasazení Dynamics AX do cloudu (Azure)
- Usnadnění vývoje a testování aplikací Dynamics AX díky tomu, že je možné na vyžádání vytvořit kopii produkčního prostředí pro účely testování a ladění

[Přečtěte si další informace](site-recovery-dynamicsax.md) o zotavení po havárii pro dynamickou AX.

## <a name="protect-remote-desktop-services"></a>Ochrana služby Vzdálená plocha

Vzdálená plocha (RDS) umožňuje infrastrukturu virtuálních klientských počítačů (VDI), klientů založených na relacích a aplikací, které uživatelům umožňují pracovat odkudkoli.

Pomocí Azure Site Recovery můžete replikovat následující služby:

- Replikace spravovaných nebo nespravovaných virtuálních klientů ve fondu do sekundární lokality.
- Replikace vzdálených aplikací a relací do sekundární lokality nebo do Azure.

Možnosti replikace najdete v následující tabulce:

| **Service** |**Replikace virtuálních počítačů Azure do Azure** | **Replikace virtuálních počítačů Hyper-V do sekundární lokality** | **Replikace virtuálních počítačů Hyper-V do Azure** | **Replikace virtuálních počítačů VMware do sekundární lokality** | **Replikace virtuálních počítačů VMware do Azure** | **Replikace fyzických serverů do sekundární lokality** | **Replikace fyzických serverů do Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Virtuální desktop ve fondu (nespravovaný)** |No|Ano |No |Ano |No |Ano |No |
| **Virtuální desktop ve fondu (spravovaný a bez UPD)** |No|Ano |No |Ano |No |Ano |No |
| **Vzdálené aplikace a desktopové relace (bez UPD)** |Ano|Ano |Ano |Ano |Ano |Ano |Ano |

[Přečtěte si další informace](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure) o zotavení po havárii pro RDS.

## <a name="protect-exchange"></a>Ochrana Exchange

Site Recovery pomáhá chránit Exchange následujícím způsobem:

- Pro malá nasazení Exchange, jako jsou například jednotlivé nebo samostatné servery, může Site Recovery provádět replikaci a přebírat služby při selhání do Azure nebo do sekundární lokality.
- U větších nasazení se Site Recovery integruje se skupinami dostupnosti databáze (DAG) Exchange.
- Tyto skupiny představují doporučené řešení pro zotavení Exchange ve velké firmě po havárii.  Plány obnovení Site Recovery mohou zahrnovat skupiny DAG, které orchestrují přebírání služeb při selhání mezi lokalitami.

Další informace o zotavení po havárii pro Exchange najdete v tématu [zotavení po havárii](/Exchange/high-availability/disaster-recovery/disaster-recovery) [Exchange DAG](/Exchange/high-availability/database-availability-groups/database-availability-groups) a Exchange.

## <a name="protect-sap"></a>Ochrana nasazení SAP

Pomocí Site Recovery můžete své nasazení SAP chránit následujícím způsobem:

- Povolení ochrany místně spuštěných produkčních aplikací SAP NetWeaver a jiných než NetWeaver pomocí replikace komponent do Azure
- Povolení ochrany v Azure spuštěných produkčních aplikací SAP NetWeaver a jiných než NetWeaver pomocí replikace komponent do jiného datového centra Azure
- Zjednodušení migrace na cloud pomocí Site Recovery migrací nasazení SAP do Azure
- Zjednodušení upgradů, testování a vytváření prototypů projektů SAP pomocí vytváření produkčního klonu na vyžádání pro účely testování aplikací SAP.

[Přečtěte si další informace](site-recovery-sap.md) o zotavení po havárii pro SAP.

## <a name="protect-internet-information-services"></a>Ochrana Internetová informační služba

K ochraně nasazení služby Internetová informační služba (IIS) použijte Site Recovery následujícím způsobem:

Azure Site Recovery poskytuje zotavení po havárii replikací kritických komponent ve vašem prostředí do studené vzdálené lokality nebo do veřejného cloudu, jako je například Microsoft Azure. Vzhledem k tomu, že virtuální počítače s webovým serverem a databází se replikují do lokality pro obnovení, neexistuje žádný požadavek na samostatné zálohování konfiguračních souborů nebo certifikátů. Mapování aplikací a vazby závislé na proměnných prostředí, které se změní po převzetí služeb při selhání, lze aktualizovat pomocí skriptů integrovaných do plánů obnovení po havárii. Virtuální počítače se v lokalitě pro obnovení zanesou jenom během převzetí služeb při selhání. Azure Site Recovery také pomáhají s orchestrací komplexního převzetí služeb při selhání, které poskytuje následující možnosti:

- Určování pořadí ukončování a spouštění virtuálních počítačů v různých vrstvách.
- Přidávání skriptů umožňujících aktualizace závislostí aplikace a vazeb na virtuálních počítačích po jejich spuštění. Skripty lze také použít k aktualizaci serveru DNS, aby odkazoval na lokalitu pro obnovení.
- Přidělování IP adres virtuálním počítačům před převzetím služeb při selhání mapováním primárních sítí a sítí pro obnovení a používání skriptů, které není nutné aktualizovat po převzetí služeb při selhání.
- Možnost převzetí služeb při selhání jedním kliknutím pro více webových aplikací, které eliminují rozsah nejasností během havárie.
- Možnost testovat plány obnovení v izolovaném prostředí kvůli nacvičení zotavení po havárii.

[Přečtěte si další informace](site-recovery-iis.md) o zotavení po havárii pro službu IIS.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Ochrana pro Citrix XenApp a XenDesktop

K ochraně nasazení Citrix XenApp a XenDesktop použijte Site Recovery následujícím způsobem:

- Povolte ochranu nasazení Citrix XenApp a XenDesktop. Replikace různých vrstev nasazení do Azure: Active Directory, DNS Server, SQL Database Server, Citrix Delivery Controller, prezentace Server, XenApp Master (VDA), Citrix XenApp License Server.
- Zjednodušte migraci do cloudu tak, že použijete Site Recovery k migraci nasazení Citrix XenApp a XenDesktop do Azure.
- Usnadněte testování pro Citrix XenApp/XenDesktop tak, že vytvoříte na vyžádání kopii produkčního prostředí pro testování a ladění.
- Toto řešení platí jenom pro virtuální počítače s Windows serverem a ne pro virtuální klienty klienta. Virtuální klientské počítače se zatím nepodporují pro licencování v Azure. [Další informace](https://azure.microsoft.com/pricing/licensing-faq/) týkající se licencování pro plochy klienta nebo serveru v Azure

[Přečtěte si další informace](site-recovery-citrix-xenapp-and-xendesktop.md) o zotavení po havárii pro nasazení Citrix XenApp a XenDesktop. Nebo můžete použít odkaz na dokument [Citrix White Paper](https://aka.ms/citrix-xenapp-xendesktop-with-asr).

## <a name="next-steps"></a>Další kroky

[Přečtěte si další informace](azure-to-azure-quickstart.md) o zotavení po havárii pro virtuální počítač Azure.
