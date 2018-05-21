---
title: Jaké úlohy je možné chránit pomocí Azure Site Recovery? | Dokumenty Microsoft
description: Popisuje úlohy, které se dají chránit pomocí zotavení po havárii se službou Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: cfreeman
editor: ''
ms.assetid: 4953948f-26c0-4699-8fe7-59d3bfc1d3da
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/02/2018
ms.author: raynew
ms.openlocfilehash: 4d6d3b776995233f3729a76788504678dc8979d7
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="what-workloads-can-you-protect-with-azure-site-recovery"></a>Jaké úlohy je možné chránit pomocí Azure Site Recovery?

Tento článek popisuje úlohy a aplikace, které můžete replikovat se službou [Azure Site Recovery](site-recovery-overview.md).



## <a name="overview"></a>Přehled

Organizace potřebují strategii pro provozní kontinuitu a zotavení po havárii (BCDR), která určuje strategii uchování úloh a dat zabezpečených a dostupných během plánovaných a neplánovaných výpadků a jakým způsobem se co nejdříve obnoví normální pracovní podmínky.

Site Recovery je služba Azure, která přispívá ke strategii BCDR. S využitím služby Site Recovery můžete nasadit replikaci s ohledem na aplikace do cloudu nebo do sekundární sítě. Ať již máte aplikace na bázi Windows nebo Linuxu a ať běží na fyzických serverech nebo na virtuálních počítačích VMware či Hyper-V, můžete pomocí Site Recovery orchestrovat replikaci, testovat zotavení po havárii, přebírat služby při selhání a vracet je po obnovení.

Site Recovery se integruje s aplikacemi Microsoftu, mezi které patří SharePoint, Exchange, Dynamics, SQL Server a Active Directory. Microsoft také úzce spolupracuje s předními dodavateli včetně společností Oracle, SAP a Red Hat. Řešení replikace můžete přizpůsobit na bázi jednotlivých aplikací.

## <a name="why-use-site-recovery-for-application-replication"></a>Proč pro replikaci aplikací používat Site Recovery?

Site Recovery pro ochranu a obnovení na úrovni aplikací přináší následující:

* Nerozlišování aplikací a poskytování replikace pro jakoukoli úlohu spuštěnou na podporovaném počítači
* Téměř synchronní replikace i při úrovni RPO stanovené na 30 sekund, což odpovídá nárokům nejdůležitějších firemních aplikací
* Snímky konzistentní s jednovrstvými i vícevrstvými aplikacemi
* Integrace s SQL Server AlwaysOn a partnerství s jinými technologiemi pro replikaci na úrovni aplikací, jako je replikace AD, SQL AlwaysOn, skupiny dostupnosti databáze Exchange (DAG) a Oracle Data Guard
* Flexibilní plány obnovení, které vám umožní obnovit celý zásobník aplikací jediným kliknutím, s možností zahrnout do plánu externí skripty a ručně prováděné akce
* Pokročilá správa sítě v Site Recovery a Azure pro zjednodušení požadavků na aplikační síť, včetně možnosti rezervovat IP adresy, konfigurovat vyrovnávání zatížení a integrace Azure Traffic Manager pro přepínání sítě s cílem dosáhnout nízké úrovně RTO
* Bohatá automatizační knihovna obsahující předpřipravené skripty specifické pro aplikace, které je možné stáhnout a integrovat do plánů obnovení

## <a name="workload-summary"></a>Souhrn úloh
Site Recovery dokáže replikovat jakoukoli aplikaci spuštěnou na podporovaném počítači. Kromě toho jsme ve spolupráci s produktovými týmy provedli dodatečné testování specifické pro aplikace.

| **Úloha** |**Replikace virtuálních počítačů Azure do Azure** |**Replikace virtuálních počítačů Hyper-V do sekundární lokality** | **Replikace virtuálních počítačů Hyper-V do Azure** | **Replikace virtuálních počítačů VMware do sekundární lokality** | **Replikace virtuálních počítačů VMware do Azure** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |Ano |Ano |Ano |Ano |Ano|
| Webové aplikace (IIS, SQL) |Ano |Ano |Ano |Ano |Ano|
| System Center Operations Manager |Ano |Ano |Ano |Ano |Ano|
| SharePoint |Ano |Ano |Ano |Ano |Ano|
| SAP<br/><br/>Replikace webu SAP do Azure k neclusterovému použití |Ano (testováno Microsoftem) |Ano (testováno Microsoftem) |Ano (testováno Microsoftem) |Ano (testováno Microsoftem) |Ano (testováno Microsoftem)|
| Exchange (ne DAG) |Ano |Ano |Ano |Ano |Ano|
| Vzdálená plocha/VDI |Ano |Ano |Ano |Ano |Ano|
| Linux (operační systém a aplikace) |Ano (testováno Microsoftem) |Ano (testováno Microsoftem) |Ano (testováno Microsoftem) |Ano (testováno Microsoftem) |Ano (testováno Microsoftem)|
| Dynamics AX |Ano |Ano |Ano |Ano |Ano|
| Oracle |Ano (testováno Microsoftem) |Ano (testováno Microsoftem) |Ano (testováno Microsoftem) |Ano (testováno Microsoftem) |Ano (testováno Microsoftem)|
| Souborový server systému Windows |Ano |Ano |Ano |Ano |Ano|
| Citrix XenApp a XenDesktop |Ano|neuvedeno |Ano |neuvedeno |Ano |

## <a name="replicate-active-directory-and-dns"></a>Replikace služby Active Directory a DNS
Pro většinu firemních aplikací má zásadní význam infrastruktura Active Directory a DNS. Během zotavování po havárii budete muset před obnovením aplikací a úloh ochránit a obnovit tyto součástí infrastruktury.

Pomocí Site Recovery můžete pro Active Directory a DNS vytvořit úplný automatizovaný plán zotavení po havárii. Pokud chcete například při selhání SharePointu a SAP převzít tyto služby z primární lokality do sekundární, můžete nastavit plán obnovení, který nejprve převezme Active Directory, a potom další plán specifický pro aplikace, na jehož základě se budou při selhání přebírat služby aplikací závislých na Active Directory.

[Zde jsou další informace](site-recovery-active-directory.md) o ochraně Active Directory a DNS.

## <a name="protect-sql-server"></a>Ochrana SQL Serveru
SQL Server poskytuje základnu pro datové služby využívané mnoha firemními aplikacemi v lokálním datovém centru.  Site Recovery lze používat společně s technologiemi SQL Server HA/DR k ochraně vícevrstvých firemních aplikací, které používají SQL Server. Site Recovery poskytuje následující:

* Jednoduché a nákladově efektivní řešení zotavení po havárii pro SQL Server; možnost replikovat různé verze a edice samostatných serverů a clusterů SQL Server do Azure nebo do sekundární lokality  
* Integrace se skupinami dostupnosti SQL AlwaysOn pro správu převzetí služeb při selhání a jejich navrácení po obnovení na základě plánů obnovení Azure Site Recovery
* Komplexní plány obnovení pro všechny vrstvy v aplikaci, včetně databází na SQL Serveru
* Škálování SQL Serveru podle zatížení ve špičce pomocí Site Recovery, a to rozložením zátěže na větší virtuální počítače IaaS v Azure
* Snadné testování zotavení SQL Serveru po havárii: Můžete spouštět testy převzetí služeb při selhání s cílem analyzovat data a kontrolovat dodržování předpisů, aniž by to mělo dopad na produkční prostředí.

[Zde jsou další informace](site-recovery-sql.md) o ochraně SQL Serveru.

## <a name="protect-sharepoint"></a>Ochrana SharePointu
Azure Site Recovery pomáhá chránit nasazení SharePointu následujícím způsobem:

* Eliminuje nutnost zajistit infrastrukturu (což by s sebou neslo náklady) pro záložní farmu k zotavení po havárii. Pomocí Site Recovery můžete replikovat celou farmu (webová, aplikační a databázová vrstva) do Azure nebo do sekundární lokality.
* Zjednodušuje nasazení a správu aplikace. Aktualizace zanesené do primární lokality se automaticky replikují, a jsou tedy k dispozici po převzetí služeb při selhání a obnovení farmy v sekundární lokalitě. Snižuje se také složitost správy a náklady spojené s udržováním záložní farmy v aktualizovaném stavu.
* Usnadňuje vývoj a testování aplikací SharePointu díky tomu, že je možné na vyžádání vytvořit repliku produkčního prostředí pro účely testování a ladění.
* Zjednodušuje přechod na cloud tím, že se pomocí Site Recovery provede migrace nasazení SharePointu do Azure.

[Zde jsou další informace](site-recovery-sharepoint.md) o ochraně SharePointu.

## <a name="protect-dynamics-ax"></a>Ochrana Dynamics AX
Azure Site Recovery zvyšuje ochranu vašeho řešení Dynamics AX ERP tímto způsobem:

* Orchestrace replikace celého prostředí Dynamics AX (webová a AOS vrstva, databázové vrstvy, SharePoint) do Azure nebo do sekundární lokality
* Zjednodušení migrace nasazení Dynamics AX do cloudu (Azure)
* Usnadnění vývoje a testování aplikací Dynamics AX díky tomu, že je možné na vyžádání vytvořit kopii produkčního prostředí pro účely testování a ladění

[Zde jsou další informace](site-recovery-dynamicsax.md) o ochraně Dynamic AX.

## <a name="protect-rds"></a>Ochrana Vzdálené plochy
Služba Vzdálená plocha (RDS) umožňuje nasazení infrastruktury virtuálních klientských počítačů (VDI), tedy stolních počítačů a aplikací na bázi relací, což umožňuje uživatelům pracovat odkudkoli. S Azure Site Recovery můžete:

* Replikovat spravované nebo nespravované virtuální desktopy ve fondu do sekundární lokality a vzdálené aplikace a relace do sekundární lokality nebo Azure

* Zde je seznam toho, co můžete replikovat:

| **Vzdálená plocha** |**Replikace virtuálních počítačů Azure do Azure** | **Replikace virtuálních počítačů Hyper-V do sekundární lokality** | **Replikace virtuálních počítačů Hyper-V do Azure** | **Replikace virtuálních počítačů VMware do sekundární lokality** | **Replikace virtuálních počítačů VMware do Azure** | **Replikace fyzických serverů do sekundární lokality** | **Replikace fyzických serverů do Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Virtuální desktop ve fondu (nespravovaný)** |Ne|Ano |Ne |Ano |Ne |Ano |Ne |
| **Virtuální desktop ve fondu (spravovaný a bez UPD)** |Ne|Ano |Ne |Ano |Ne |Ano |Ne |
| **Vzdálené aplikace a desktopové relace (bez UPD)** |Ano|Ano |Ano |Ano |Ano |Ano |Ano |

[Nastavení zotavení po havárii pro VP pomocí Azure Site Recovery](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure).

[Zde jsou další informace](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) o ochraně Vzdálené plochy.

## <a name="protect-exchange"></a>Ochrana Exchange
Site Recovery pomáhá chránit Exchange následujícím způsobem:

* Pro malá nasazení Exchange, jako jsou například jednotlivé nebo samostatné servery, může Site Recovery provádět replikaci a přebírat služby při selhání do Azure nebo do sekundární lokality.
* U větších nasazení se Site Recovery integruje se skupinami dostupnosti databáze (DAG) Exchange.
* Tyto skupiny představují doporučené řešení pro zotavení Exchange ve velké firmě po havárii.  Plány obnovení Site Recovery mohou zahrnovat skupiny DAG, které orchestrují přebírání služeb při selhání mezi lokalitami.

[Zde jsou další informace](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) o ochraně Exchange.

## <a name="protect-sap"></a>Ochrana nasazení SAP
Pomocí Site Recovery můžete své nasazení SAP chránit následujícím způsobem:

* Povolení ochrany místně spuštěných produkčních aplikací SAP NetWeaver a jiných než NetWeaver pomocí replikace komponent do Azure
* Povolení ochrany v Azure spuštěných produkčních aplikací SAP NetWeaver a jiných než NetWeaver pomocí replikace komponent do jiného datového centra Azure
* Zjednodušení migrace na cloud pomocí Site Recovery migrací nasazení SAP do Azure
* Zjednodušení upgradů, testování a vytváření prototypů projektů SAP pomocí vytváření produkčního klonu na vyžádání pro účely testování aplikací SAP.

[Zde jsou další informace](site-recovery-sap.md) o ochraně nasazení SAP.

## <a name="protect-iis"></a>Ochrana IIS
Pomocí Site Recovery můžete své nasazení IIS chránit následujícím způsobem:

Azure Site Recovery poskytuje zotavení po havárii replikací kritických komponent ve vašem prostředí do studené vzdálené lokality nebo do veřejného cloudu, jako je například Microsoft Azure. Vzhledem k tomu, že virtuální počítače s webovým serverem a databází se replikují do lokality pro obnovení, neexistuje žádný požadavek na zálohování konfiguračních souborů nebo certifikátů samostatně. Mapování aplikací a vazby závislé na proměnných prostředí, které se změní po převzetí služeb při selhání, lze aktualizovat pomocí skriptů integrovaných do plánů obnovení po havárii. Virtuální počítače se aktivují v lokalitě pro obnovení pouze v případě převzetí služeb při selhání. A nejen to, Azure Site Recovery vám také pomůže provádět orchestraci převzetí služeb při selhání od začátku do konce tím, že poskytuje následující možnosti:

-   Určování pořadí ukončování a spouštění virtuálních počítačů v různých vrstvách.
-   Přidávání skriptů k umožnění aktualizací závislostí a vazeb aplikací na virtuálních počítačích po jejich spuštění. Skripty lze také použít k aktualizaci serveru DNS, aby odkazoval na lokalitu pro obnovení.
-   Přidělení IP adres virtuálním počítačům před převzetím služeb při selhání pomocí mapování primární a obnovovací sítě, a tím používání skriptů, které nevyžadují aktualizaci po převzetí služeb při selhání.
-   Možnost převzetí služeb při selhání jedním kliknutím pro více webových aplikací na webových serverech, čímž se zmenšuje rozsah nedorozumění v případě havárie.
-   Možnost testovat plány obnovení v izolovaném prostředí kvůli nacvičení zotavení po havárii.

[Zde jsou další informace](https://aka.ms/asr-iis) o ochraně webové farmy IIS.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Ochrana pro Citrix XenApp a XenDesktop
K ochraně nasazení Citrix XenApp a XenDesktop použijte Site Recovery následujícím způsobem:

* Povolte ochranu nasazení Citrix XenApp a XenDesktop replikací různých vrstev nasazení (včetně serveru AD DNS, databázového serveru SQL, Citrix Delivery Controlleru, serveru StoreFront, XenApp Masteru (VDA) a Citrix XenApp License Serveru) do Azure.
* Zjednodušte migraci do cloudu tak, že použijete Site Recovery k migraci nasazení Citrix XenApp a XenDesktop do Azure.
* Usnadněte testování pro Citrix XenApp/XenDesktop tak, že vytvoříte na vyžádání kopii produkčního prostředí pro testování a ladění.
* Toto řešení jde použít jenom pro virtuální plochy operačního systému Windows Server, a ne virtuální plochy klienta, protože virtuální plochy klienta se ještě pro licencování v Azure nepodporují.
[Další informace](https://azure.microsoft.com/pricing/licensing-faq/) týkající se licencování pro plochy klienta nebo serveru v Azure

[Další informace](site-recovery-citrix-xenapp-and-xendesktop.md) o ochraně nasazení Citrix XenApp a XenDesktop Můžete si také projít [dokument whitepaper od Citrixu](https://aka.ms/citrix-xenapp-xendesktop-with-asr), kde najdete podrobnosti o tomtéž.

## <a name="next-steps"></a>Další kroky

[Začínáme](azure-to-azure-quickstart.md) s replikací virtuálních počítačů Azure.
