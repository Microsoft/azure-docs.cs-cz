---
title: Co je nového v Azure Migrate | Microsoft Docs
description: Obsahuje přehled služby Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 10/22/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 1ecd15b36b677e65eadbe4e979925723c9eedd51
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498436"
---
# <a name="whats-new-in-azure-migrate"></a>Co je nového v Azure Migrate

[Azure Migrate](migrate-services-overview.md) pomáhá zjišťovat, vyhodnocovat a migrovat místní servery, aplikace a data do cloudu Microsoft Azure. Tento článek shrnuje nové funkce v Azure Migrate.



## <a name="update-november-2019"></a>Aktualizace (listopad 2019)

Do Azure Migrate bylo přidáno několik nových funkcí:

- **Posouzení fyzického serveru**. Kromě migrace fyzického serveru, která je už podporovaná, se teď podporuje hodnocení místních fyzických serverů.
- **Posouzení na základě importu**. Nyní je podporováno posouzení počítačů využívajících metadata a data o výkonu, které jsou k dispozici v souboru CSV.
- **Zjišťování aplikací**: Azure Migrate nyní podporuje zjišťování aplikací, rolí a funkcí na úrovni aplikace pomocí Azure Migrateho zařízení. Tato funkce se aktuálně podporuje jenom pro virtuální počítače VMware a je omezená jenom na zjišťování (posouzení se momentálně nepodporuje). [Další informace](how-to-discover-applications.md)
- **Vizualizace závislostí bez agentů**: už nemusíte explicitně instalovat agenty pro vizualizaci závislostí. Podporují se teď i agenty i založené na agentech.
- **Virtuální plocha**: pomocí nástrojů ISV můžete vyhodnotit a migrovat místní infrastrukturu virtuálních klientských počítačů (VDI) na virtuální plochu Windows v Azure.
- **Webová aplikace**: Azure App Service Pomocník s migrací, která se používá pro hodnocení a migraci webových aplikací, je teď integrovaná do Azure Migrate.

## <a name="release-version-july-2019"></a>Verze vydaných verzí (červenec 2019)

Aktuální verze Azure Migrate byla vydaná v červenci 2019. 

- **Aktuální verze**: Tato verze slouží k vytváření Azure Migrate projektů, zjišťování místních počítačů a orchestraci posouzení a migrace. 
- **Předchozí verze**: pro zákazníky používající předchozí verzi Azure Migrate (podporuje se jenom posouzení místních virtuálních počítačů VMware), měli byste teď použít aktuální verzi. V předchozí verzi už nemůžete vytvářet nové Azure Migrate projekty ani provádět nové zjišťování. Ke stávajícím projektům můžete pořád přistupovat. Pokud to chcete provést v Azure Portal > **všechny služby**, vyhledejte **Azure Migrate**. V oznámeních Azure Migrate existuje odkaz pro přístup k původním Azure Migratem projektům.


### <a name="azure-migrate-features"></a>Azure Migrate funkce

Aktuální verze Azure Migrate poskytuje řadu nových funkcí:


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


## <a name="next-steps"></a>Další kroky

- [Další informace](https://azure.microsoft.com/pricing/details/azure-migrate/) o cenách služby Azure Migrate.
- [Přečtěte si nejčastější dotazy](resources-faq.md) ohledně služby Azure Migrate.
- Vyzkoušejte si naše kurzy, abyste vyhodnotili [virtuální počítače VMware](tutorial-assess-vmware.md) a [virtuální počítače Hyper-V](tutorial-assess-hyper-v.md).
