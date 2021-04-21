---
title: Co je nového v Azure Migrate
description: Seznamte se s novinkami a nejnovějšími aktualizacemi ve službě Azure Migrate.
ms.topic: overview
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: 3f49c0acc99f82cc2986ca896d40b3998cf47c5c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834997"
---
# <a name="whats-new-in-azure-migrate"></a>Co je nového v Azure Migrate

[Azure Migrate](migrate-services-overview.md) pomáhá zjišťovat, vyhodnocovat a migrovat místní servery, aplikace a data do cloudu Microsoft Azure. Tento článek shrnuje nové verze a funkce v Azure Migrate.

## <a name="update-march-2021"></a>Aktualizace (březen 2021)
- Podpora poskytování více přihlašovacích údajů serveru v zařízení Azure Migrate pro zjišťování nainstalovaných aplikací (inventář softwaru), analýzy závislostí bez agentů a zjišťování SQL Server instancí a databází v prostředí VMware. [Další informace](tutorial-discover-vmware.md#provide-server-credentials)
- Zjišťování a hodnocení instancí SQL Server a databází spuštěných ve vašem prostředí VMware je teď ve verzi Preview. [Další informace](concepts-azure-sql-assessment-calculation.md) Začněte tím, že najdete kurzy pro [zjišťování](tutorial-discover-vmware.md) a [hodnocení](tutorial-assess-sql.md) .
- Migrace VMware bez agentů teď podporuje souběžnou replikaci 500 virtuálních počítačů na vCenter.

## <a name="update-january-2021"></a>Aktualizace (leden 2021)
-  Azure Migrate: Nástroj pro migraci serveru teď umožňuje migrovat virtuální počítače VMware, fyzické servery a virtuální počítače z jiných cloudů na virtuální počítače Azure s disky šifrovanými pomocí šifrování na straně serveru pomocí klíčů spravovaných zákazníkem (CMK).

## <a name="update-december-2020"></a>Aktualizace (prosince 2020)
- Azure Migrate teď automaticky nainstaluje do virtuálních počítačů VMware agenta virtuálních počítačů Azure při jejich migraci do Azure pomocí metody migrace VMware bez agentů. (Windows Server 2008 R2 a novější)
- Migrace virtuálních počítačů VMware na virtuální počítače Azure s disky šifrovanými pomocí šifrování na straně serveru (SSE) pomocí klíčů spravovaných zákazníkem (CMK Azure Migrate) je teď k dispozici prostřednictvím služby Azure Portal.

## <a name="update-september-2020"></a>Aktualizace (září 2020)
- Migrace serverů do Zóny dostupnosti se teď podporuje.
- Migrace virtuálních počítačů založených na rozhraní UEFI a fyzických serverů do virtuálních počítačů Azure generace 2 se teď podporuje. V této verzi Azure Migrate: Nástroj pro migraci serveru neprovede převod z virtuálního počítače 1. generace na virtuální počítač 1. generace během migrace.
- K dispozici je nový řídicí panel posouzení Power BI Azure Migrate, který vám umožní porovnat náklady napříč různými nastaveními posouzení. Řídicí panel obsahuje nástroj PowerShell, který automaticky vytvoří posouzení, která se připojí k řídicímu panelu Power BI. [Další informace](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/assessment-utility)
- Analýza závislostí (bez agenta) se teď dá na virtuálních počítačích 1000 současně spustit současně.
- Analýza závislostí (bez agentů) se teď dá ve velkém měřítku povolit nebo zakázat pomocí skriptů PowerShellu. [Další informace](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale)
- Vizualizujte síťová připojení v Power BI pomocí dat shromážděných pomocí analýzy závislostí (bez agentů) [. Další informace najdete](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) v části.
- Migrace virtuálních počítačů VMware s velikostí datových disků až 32 TB je teď podporovaná pomocí Azure Migrate: Migrace serveru bez agenta pro migraci VMware.

## <a name="update-august-2020"></a>Aktualizace (srpen 2020)

- Vylepšené prostředí pro připojování, kde se vygeneruje Azure Migrate klíč projektu z portálu a používá se k dokončení registrace zařízení.
- Možnost stažení souborů vajíček/VHD nebo skriptů instalačního programu z portálu a nastavení zařízení VMware a Hyper-V v uvedeném pořadí.
- Aktualizovaný Správce konfigurace zařízení s pokročilým uživatelským prostředím
- Podpora více přihlašovacích údajů pro zjišťování virtuálních počítačů Hyper-V.

## <a name="update-july-2020"></a>Aktualizace (červenec 2020)

- Migrace VMware bez agentů teď podporuje souběžnou replikaci 300 virtuálních počítačů na vCenter.

## <a name="update-june-2020"></a>Aktualizace (červen 2020)

- Pro migraci místních virtuálních počítačů VMware do [Řešení Azure VMware (AVS)](./concepts-azure-vmware-solution-assessment-calculation.md) se teď podporuje vyhodnocení. [Další informace](how-to-create-azure-vmware-solution-assessment.md)
- Podpora více přihlašovacích údajů na zařízení pro zjištění fyzického serveru.
- Podpora pro povolení přihlášení Azure ze zařízení pro tenanta, ve kterém je nakonfigurované omezení tenanta.


## <a name="update-april-2020"></a>Aktualizace (duben 2020)

Azure Migrate podporuje nasazení v Azure Government. 

- Můžete zjišťovat a hodnotit virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery.
- Do Azure můžete migrovat virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery.
- Pro migraci VMware můžete použít migraci bez agentů nebo agenta na základě agentů. [Další informace](server-migrate-overview.md).
- [Kontrola](migrate-support-matrix.md#supported-geographies-azure-government) podporovaných geografických oblastí a oblastí pro Azure Government.
- [Analýza závislostí založená na agentech](concepts-dependency-visualization.md#agent-based-analysis) není v Azure Government podporovaná.
- Funkce ve verzi Preview jsou podporované v Azure Government, konkrétně v případě [analýzy závislostí bez agenta](concepts-dependency-visualization.md#agentless-analysis)a [zjišťování aplikací](how-to-discover-applications.md).


## <a name="update-march-2020"></a>Aktualizace (březen 2020)

K dispozici je nyní instalace pomocí skriptu pro nastavení [zařízení Azure Migrate](migrate-appliance.md):

- Instalace založená na skriptech je alternativou k. VAJÍČKa (VMware)/VHD (Hyper-V) instalace zařízení.
- Poskytuje skript instalačního programu PowerShellu, který se dá použít k nastavení zařízení pro VMware/Hyper-V na stávajícím počítači s Windows serverem 2016.

## <a name="update-november-2019"></a>Aktualizace (listopad 2019)

Do Azure Migrate bylo přidáno několik nových funkcí:

- **Posouzení fyzického serveru**. Kromě migrace fyzického serveru, která je už podporovaná, se teď podporuje hodnocení místních fyzických serverů.
- **Posouzení na základě importu**. Nyní je podporováno posouzení počítačů využívajících metadata a data o výkonu, které jsou k dispozici v souboru CSV.
- **Zjišťování aplikací**: Azure Migrate nyní podporuje zjišťování aplikací, rolí a funkcí na úrovni aplikace pomocí Azure Migrateho zařízení. Tato funkce se aktuálně podporuje jenom pro virtuální počítače VMware a je omezená jenom na zjišťování (posouzení se momentálně nepodporuje). [Další informace](how-to-discover-applications.md)
- **Vizualizace závislostí bez agentů**: už nemusíte explicitně instalovat agenty pro vizualizaci závislostí. Podporují se teď i agenty i založené na agentech.
- **Virtuální plocha**: pomocí nástrojů ISV můžete vyhodnotit a migrovat místní infrastrukturu virtuálních klientských počítačů (VDI) na virtuální plochu Windows v Azure.
- **Webová aplikace**: Azure App Service Pomocník s migrací, která se používá pro hodnocení a migraci webových aplikací, je teď integrovaná do Azure Migrate.

Do Azure Migrate byly přidány nové nástroje pro posouzení a migraci:

- **RackWare**: nabídka migrace do cloudu.
- **Stěhovací** společnost: posouzení nabídky.

[Přečtěte si další informace](migrate-services-overview.md) o používání nástrojů a nabídek ISV pro posouzení a migraci v Azure Migrate.

## <a name="azure-migrate-current-version"></a>Azure Migrate aktuální verze

Aktuální verze Azure Migrate (vydaná v červenci 2019) obsahuje řadu nových funkcí:

- **Sjednocená platforma pro migraci**: Azure Migrate teď nabízí jeden portál, který umožňuje centralizovat, spravovat a sledovat cestu migrace do Azure a nabízí vylepšený tok nasazení a možnosti portálu.
- **Nástroje pro posuzování a migraci**: Azure Migrate poskytuje nativní nástroje a integruje se s dalšími službami Azure i s nástroji nezávislého výrobce softwaru (ISV). [Přečtěte si další informace](migrate-services-overview.md#isv-integration) o integraci ISV.
- **Azure Migrate posouzení**: pomocí nástroje pro vyhodnocení Azure Migrate serveru můžete vyhodnotit virtuální počítače VMware a virtuální počítače Hyper-V pro migraci do Azure. Můžete také vyhodnotit migraci pomocí jiných služeb Azure a nástrojů ISV.
- **Migrace Azure Migrate**: pomocí nástroje pro migraci Azure Migrate serveru můžete migrovat místní virtuální počítače VMware a virtuální počítače Hyper-V do Azure a také fyzické servery, jiné virtualizované servery a virtuální a veřejné cloudové virtuální počítače. Kromě toho můžete migrovat do Azure pomocí nástrojů ISV.
- **Zařízení Azure Migrate**: Azure Migrate nasadí odlehčené zařízení pro zjišťování a hodnocení místních virtuálních počítačů VMware a virtuálních počítačů Hyper-V.
    - Toto zařízení používá Azure Migrate posouzení serveru a migrace Azure Migrate serveru pro migraci bez agenta.
    - Zařízení nepřetržitě zjišťuje metadata serveru a data o výkonu pro účely posouzení a migrace.  
- **Migrace virtuálních počítačů VMware**: migrace Azure Migrate serveru poskytuje několik způsobů migrace místních virtuálních počítačů VMware do Azure.  Migrace bez agentů pomocí zařízení Azure Migrate a migrace na základě agenta, která používá zařízení replikace, nasadí agenta na každý virtuální počítač, který chcete migrovat. [Další informace](server-migrate-overview.md)
 - **Vyhodnocení a migrace databáze**: z Azure Migrate můžete vyhodnotit místní databáze pro migraci do Azure pomocí Pomocník s migrací databáze Azure. Databáze můžete migrovat pomocí Azure Database Migration Service.
- **Migrace webové aplikace**: můžete vyhodnotit webové aplikace pomocí adresy URL veřejného koncového bodu s Azure App Service. Pro migraci interních aplikací .NET můžete stáhnout a spustit Pomocník s migrací App Service.
- **Data box**: Import velkých objemů dat do Azure pomocí Azure Data Box v Azure Migrate.

## <a name="azure-migrate-previous-version"></a>Azure Migrate předchozí verze

Pokud používáte předchozí verzi Azure Migrate (podporuje se jenom posouzení místních virtuálních počítačů VMware), měli byste teď použít aktuální verzi. V předchozí verzi už nemůžete vytvářet nové Azure Migrate projekty ani provádět nové zjišťování. Ke stávajícím projektům můžete pořád přistupovat. Pokud to chcete provést v Azure Portal > **všechny služby**, vyhledejte **Azure Migrate**. V oznámeních Azure Migrate existuje odkaz pro přístup k původním Azure Migratem projektům.



## <a name="next-steps"></a>Další kroky

- [Další informace](https://azure.microsoft.com/pricing/details/azure-migrate/) o cenách služby Azure Migrate.
- [Přečtěte si nejčastější dotazy](resources-faq.md) ohledně služby Azure Migrate.
- Vyzkoušejte si naše kurzy, abyste vyhodnotili [virtuální počítače VMware](./tutorial-assess-vmware-azure-vm.md) a [virtuální počítače Hyper-V](tutorial-assess-hyper-v.md).
