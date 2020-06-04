---
title: Co je nového v Azure Migrate
description: Seznamte se s novinkami a nejnovějšími aktualizacemi ve službě Azure Migrate.
ms.topic: overview
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: db52c8ce5e1b7e25d4af0c570189597e05b86588
ms.sourcegitcommit: 79508e58c1f5c58554378497150ffd757d183f30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84331861"
---
# <a name="whats-new-in-azure-migrate"></a>Co je nového v Azure Migrate

[Azure Migrate](migrate-services-overview.md) pomáhá zjišťovat, vyhodnocovat a migrovat místní servery, aplikace a data do cloudu Microsoft Azure. Tento článek shrnuje nové verze a funkce v Azure Migrate.

## <a name="update-june-2020"></a>Aktualizace (červen 2020)

Nové verze zařízení Azure Migrate.

### <a name="azure-public-cloud"></a>Veřejný cloud Azure

**Případě*** | **Stáhnout** | **SHA256**
--- | --- | ---
Hyper-V (8,93 MB) | [Nejnovější verze](https://aka.ms/migrate/appliance/hyperv) |  572be425ea0aca69a9aa8658c950bc319b2bdbeb93b440577264500091c846a1
VMware (10,9 GB) | [Nejnovější verze](https://aka.ms/migrate/appliance/vmware) | cacbdaef927fe5477fa4e1f494fcb7203cbd6b6ce7402b79f234bc0fe69663dd
Fyzický (63,1 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2105112) | 0a27adf13cc5755e4b23df0c05732c6ac08d1fe8850567cb57c9906fbc3b85a0


### <a name="azure-government"></a>Azure Government

**Případě*** | **Stáhnout** | **SHA256**
--- | --- | ---
Hyper-V (63,1 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2120200&clcid=0x409) |  2c5e73a1e5525d4fae468934408e43ab55ff397b7da200b92121972e683f9aa3
VMware (63,1 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2120300&clcid=0x409 ) | 3d5822038646b81f458d89d706832c0a2c0e827bfa9b0a55cc478eaf2757a4de
Fyzický (63,1 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2120100&clcid=0x409) | 93dfef131026e70acdfad2769cd208ff745ab96a96f013cdf3f9e1e61c9b37e1



## <a name="update-april-2020"></a>Aktualizace (duben 2020)

Azure Migrate podporuje nasazení v Azure Government. 

- Můžete zjišťovat a hodnotit virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery.
- Do Azure můžete migrovat virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery.
- Pro migraci VMware můžete použít migraci bez agentů nebo agenta na základě agentů. [Přečtěte si další informace](server-migrate-overview.md).
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

- **Rackware**: nabídka migrace do cloudu.
- **Stěhovací**společnost: posouzení nabídky.

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
- Vyzkoušejte si naše kurzy, abyste vyhodnotili [virtuální počítače VMware](tutorial-assess-vmware.md) a [virtuální počítače Hyper-V](tutorial-assess-hyper-v.md).
