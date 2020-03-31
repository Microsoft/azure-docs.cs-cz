---
title: Obnovení po havárii pro místní aplikace s Azure Site Recovery
description: Popisuje úlohy, které se dají chránit pomocí zotavení po havárii se službou Azure Site Recovery.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 2b901425a0020c0ccc7b834ee36d965910028018
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062840"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>Informace o zotavení po havárii pro místní aplikace

Tento článek popisuje místní úlohy a aplikace, které můžete chránit pro zotavení po havárii pomocí služby [Azure Site Recovery.](site-recovery-overview.md)

## <a name="overview"></a>Přehled

Organizace potřebují strategii kontinuity podnikání a zotavení po havárii (BCDR), aby byly pracovní vytížení a data v bezpečí a během plánovaných a neplánovaných prostojů. A zotavit se z běžných pracovních podmínek.

Site Recovery je služba Azure, která přispívá ke strategii BCDR. S využitím služby Site Recovery můžete nasadit replikaci s ohledem na aplikace do cloudu nebo do sekundární sítě. Obnovení webu můžete použít ke správě replikace, testování zotavení po havárii a spuštění převzetí služeb při selhání a navrácení služeb po obnovení. Vaše aplikace mohou běžet na počítačích se systémem Windows nebo Linux, fyzických serverech, vmware nebo hyper-V.

Site Recovery se integruje s aplikacemi společnosti Microsoft, jako je SharePoint, Exchange, Dynamics, SQL Server a Active Directory. Společnost Microsoft úzce spolupracuje s předními dodavateli, včetně společností Oracle, SAP a Red Hat. Řešení replikace můžete přizpůsobit na bázi jednotlivých aplikací.

## <a name="why-use-site-recovery-for-application-replication"></a>Proč pro replikaci aplikací používat Site Recovery?

Site Recovery pro ochranu a obnovení na úrovni aplikací přináší následující:

- Aplikace a gnostik a poskytuje replikaci pro všechny úlohy spuštěné na podporovaném počítači.
- Téměř synchronní replikace s cíli bodů obnovení (RPO) již od 30 sekund, aby vyhovovaly potřebám nejdůležitějších obchodních aplikací.
- Snímky konzistentní s jednovrstvými i vícevrstvými aplikacemi
- Integrace s SQL Server AlwaysOn a partnerství s dalšími technologiemi replikace na úrovni aplikace. Například replikace služby Active Directory, SQL AlwaysOn a skupiny dostupnosti databáze Exchange (DAGs).
- Flexibilní plány obnovení, které umožňují obnovit celý zásobník aplikace jediným kliknutím a zahrnout do plánu externí skripty a ruční akce.
- Pokročilá správa sítě v site recovery a Azure pro zjednodušení požadavků na síť aplikací. Správa sítě, jako je možnost rezervovat IP adresy, konfigurovat vyrovnávání zatížení a integrace s Azure Traffic Manager pro nízké cíle obnovení cíle (RTO) síťové přepnutí.
- Bohatá automatizační knihovna obsahující předpřipravené skripty specifické pro aplikace, které je možné stáhnout a integrovat do plánů obnovení

## <a name="workload-summary"></a>Souhrn úloh

Site Recovery dokáže replikovat jakoukoli aplikaci spuštěnou na podporovaném počítači. Spolupracujeme s týmy produktů, abychom mohli provést další testování aplikací uvedených v následující tabulce.

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
| Citrix XenApp a XenDesktop |Ano|Není dostupné. |Ano |Není dostupné. |Ano |

## <a name="replicate-active-directory-and-dns"></a>Replikace služby Active Directory a DNS

Pro většinu firemních aplikací má zásadní význam infrastruktura Active Directory a DNS. Během zotavení po havárii budete muset chránit a obnovovat tyto součásti infrastruktury, než obnovíte úlohy a aplikace.

Pomocí Site Recovery můžete pro Active Directory a DNS vytvořit úplný automatizovaný plán zotavení po havárii. Chcete-li například převzít služby Při selhání služby SharePoint a SAP z primární do sekundární lokality, můžete nastavit plán obnovení, který nejprve převezme službu Active Directory. Pak použijte další plán obnovení specifické pro aplikaci k převzetí služeb při selhání jiných aplikací, které jsou závislé na službě Active Directory.

[Přečtěte si další informace](site-recovery-active-directory.md) o zotavení po havárii pro službu Active Directory a SLUŽBU DNS.

## <a name="protect-sql-server"></a>Ochrana SQL Serveru

SQL Server poskytuje základ datových služeb pro mnoho obchodních aplikací v místním datovém centru. Site Recovery lze použít s technologiemi SQL Server HA/DR k ochraně vícevrstvých podnikových aplikací, které používají SQL Server.

Site Recovery poskytuje následující:

- Jednoduché a nákladově efektivní řešení zotavení po havárii pro SQL Server; možnost replikovat různé verze a edice samostatných serverů a clusterů SQL Server do Azure nebo do sekundární lokality
- Integrace se skupinami dostupnosti SQL AlwaysOn pro správu převzetí služeb při selhání a jejich navrácení po obnovení na základě plánů obnovení Azure Site Recovery
- Komplexní plány obnovení pro všechny vrstvy v aplikaci, včetně databází na SQL Serveru
- Škálování SQL Serveru pro špičková zatížení s site recovery, _jejich roztržením_ do větších velikostí virtuálních počítačů IaaS v Azure.
- Snadné testování zotavení SQL Serveru po havárii: Můžete spouštět testy převzetí služeb při selhání s cílem analyzovat data a kontrolovat dodržování předpisů, aniž by to mělo dopad na produkční prostředí.

[Další informace](site-recovery-sql.md) o zotavení po havárii pro SQL server.

## <a name="protect-sharepoint"></a>Ochrana SharePointu

Azure Site Recovery pomáhá chránit nasazení SharePointu následujícím způsobem:

- Eliminuje nutnost zajistit infrastrukturu (což by s sebou neslo náklady) pro záložní farmu k zotavení po havárii. Pomocí site recovery replikujte celou farmu (webové, aplikační a databázové vrstvy) do Azure nebo do sekundární lokality.
- Zjednodušuje nasazení a správu aplikace. Aktualizace nasazené do primární lokality se automaticky replikují. Aktualizace jsou k dispozici po převzetí služeb při selhání a obnovení farmy v sekundární lokalitě. Snižuje složitost správy a náklady spojené s udržováním pohotovostní farmy v aktuálním stavu.
- Usnadňuje vývoj a testování aplikací SharePointu díky tomu, že je možné na vyžádání vytvořit repliku produkčního prostředí pro účely testování a ladění.
- Zjednodušuje přechod na cloud tím, že se pomocí Site Recovery provede migrace nasazení SharePointu do Azure.

[Přečtěte si další informace](site-recovery-sharepoint.md) o zotavení po havárii pro SharePoint.

## <a name="protect-dynamics-ax"></a>Ochrana Dynamics AX

Azure Site Recovery zvyšuje ochranu vašeho řešení Dynamics AX ERP tímto způsobem:

- Správa replikace celého prostředí Dynamics AX (webové a aos vrstvy, databázové vrstvy, SharePoint) do Azure nebo do sekundární lokality.
- Zjednodušení migrace nasazení Dynamics AX do cloudu (Azure)
- Usnadnění vývoje a testování aplikací Dynamics AX díky tomu, že je možné na vyžádání vytvořit kopii produkčního prostředí pro účely testování a ladění

[Přečtěte si další informace](site-recovery-dynamicsax.md) o zotavení po havárii pro dynamickou ax.

## <a name="protect-remote-desktop-services"></a>Ochrana služby Vzdálená plocha

Služba Vzdálená plocha (RDS) umožňuje infrastrukturu virtuálních ploch (VDI), plochy založené na relacích a aplikace, které uživatelům umožňují pracovat kdekoli.

Pomocí Azure Site Recovery můžete replikovat následující služby:

- Replikujte spravované nebo nespravované sdružené virtuální plochy do sekundární lokality.
- Replikujte vzdálené aplikace a relace do sekundární lokality nebo Azure.

V následující tabulce jsou uvedeny možnosti replikace:

| **Vzdálená plocha** |**Replikace virtuálních počítačů Azure do Azure** | **Replikace virtuálních počítačů Hyper-V do sekundární lokality** | **Replikace virtuálních počítačů Hyper-V do Azure** | **Replikace virtuálních počítačů VMware do sekundární lokality** | **Replikace virtuálních počítačů VMware do Azure** | **Replikace fyzických serverů do sekundární lokality** | **Replikace fyzických serverů do Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Virtuální desktop ve fondu (nespravovaný)** |Ne|Ano |Ne |Ano |Ne |Ano |Ne |
| **Virtuální desktop ve fondu (spravovaný a bez UPD)** |Ne|Ano |Ne |Ano |Ne |Ano |Ne |
| **Vzdálené aplikace a desktopové relace (bez UPD)** |Ano|Ano |Ano |Ano |Ano |Ano |Ano |

[Další informace](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure) o zotavení po havárii pro RDS.

## <a name="protect-exchange"></a>Ochrana Exchange

Site Recovery pomáhá chránit Exchange následujícím způsobem:

- Pro malá nasazení Exchange, jako jsou například jednotlivé nebo samostatné servery, může Site Recovery provádět replikaci a přebírat služby při selhání do Azure nebo do sekundární lokality.
- U větších nasazení se Site Recovery integruje se skupinami dostupnosti databáze (DAG) Exchange.
- Tyto skupiny představují doporučené řešení pro zotavení Exchange ve velké firmě po havárii.  Plány obnovení Site Recovery mohou zahrnovat skupiny DAG, které orchestrují přebírání služeb při selhání mezi lokalitami.

Další informace o zotavení po havárii pro Exchange, naleznete [v tématu Exchange DAGs](/Exchange/high-availability/database-availability-groups/database-availability-groups) a [Exchange zotavení po havárii](/Exchange/high-availability/disaster-recovery/disaster-recovery).

## <a name="protect-sap"></a>Ochrana nasazení SAP

Pomocí Site Recovery můžete své nasazení SAP chránit následujícím způsobem:

- Povolení ochrany místně spuštěných produkčních aplikací SAP NetWeaver a jiných než NetWeaver pomocí replikace komponent do Azure
- Povolení ochrany v Azure spuštěných produkčních aplikací SAP NetWeaver a jiných než NetWeaver pomocí replikace komponent do jiného datového centra Azure
- Zjednodušení migrace na cloud pomocí Site Recovery migrací nasazení SAP do Azure
- Zjednodušení upgradů, testování a vytváření prototypů projektů SAP pomocí vytváření produkčního klonu na vyžádání pro účely testování aplikací SAP.

[Přečtěte si další informace](site-recovery-sap.md) o zotavení po havárii pro SAP.

## <a name="protect-internet-information-services"></a>Ochrana internetových informačních služeb

Pomocí site recovery můžete chránit nasazení Internetové informační služby (IIS) následujícím způsobem:

Azure Site Recovery poskytuje zotavení po havárii replikací kritických komponent ve vašem prostředí do studené vzdálené lokality nebo do veřejného cloudu, jako je například Microsoft Azure. Vzhledem k tomu, že virtuální počítače s webovým serverem a databází jsou replikovány do webu pro obnovení, neexistuje žádný požadavek na samostatnou zálohu pro konfigurační soubory nebo certifikáty. Mapování aplikací a vazby závislé na proměnných prostředí, které se změní po převzetí služeb při selhání, lze aktualizovat pomocí skriptů integrovaných do plánů obnovení po havárii. Virtuální počítače jsou vyvolány v lokalitě pro obnovení pouze během převzetí služeb při selhání. Azure Site Recovery vám taky pomůže zorganizovat komplexní převzetí služeb při selhání tím, že vám poskytne následující funkce:

- Určování pořadí ukončování a spouštění virtuálních počítačů v různých vrstvách.
- Přidání skriptů umožňujících aktualizace závislostí aplikací a vazeb na virtuálních počítačích po jejich spuštění. Skripty lze také použít k aktualizaci serveru DNS, aby odkazoval na lokalitu pro obnovení.
- Přidělte IP adresy virtuálním počítačům před převzetím služeb při selhání mapováním primárních sítí a sítí pro obnovení a používejte skripty, které není nutné aktualizovat po převzetí služeb při selhání.
- Možnost převzetí služeb při selhání jedním kliknutím pro více webových aplikací, která eliminuje prostor pro nejasnosti během havárie.
- Možnost testovat plány obnovení v izolovaném prostředí kvůli nacvičení zotavení po havárii.

[Další informace](site-recovery-iis.md) o zotavení po havárii pro službu IIS.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Ochrana pro Citrix XenApp a XenDesktop

K ochraně nasazení Citrix XenApp a XenDesktop použijte Site Recovery následujícím způsobem:

- Povolte ochranu nasazení Citrix XenApp a XenDesktop. Replikujte různé vrstvy nasazení do Azure: Služba Active Directory, DNS server, databázový server SQL, řadič citrix Delivery Controller, server StoreFront, XenApp Master (VDA), licenční server Citrix XenApp.
- Zjednodušte migraci do cloudu tak, že použijete Site Recovery k migraci nasazení Citrix XenApp a XenDesktop do Azure.
- Usnadněte testování pro Citrix XenApp/XenDesktop tak, že vytvoříte na vyžádání kopii produkčního prostředí pro testování a ladění.
- Toto řešení se vztahuje pouze na virtuální plochy systému Windows Server, nikoli na virtuální plochy klienta. Klientské virtuální plochy ještě nejsou podporované pro licencování v Azure. [Další informace](https://azure.microsoft.com/pricing/licensing-faq/) týkající se licencování pro plochy klienta nebo serveru v Azure

[Přečtěte si další informace](site-recovery-citrix-xenapp-and-xendesktop.md) o zotavení po havárii pro nasazení Citrix XenApp a XenDesktop. Nebo se můžete podívat na [dokument white paper společnosti Citrix](https://aka.ms/citrix-xenapp-xendesktop-with-asr).

## <a name="next-steps"></a>Další kroky

[Další informace](azure-to-azure-quickstart.md) o zotavení po havárii pro virtuální počítač Azure.
