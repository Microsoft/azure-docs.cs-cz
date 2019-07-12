---
title: Co je nového ve službě Azure Migrate | Dokumentace Microsoftu
description: Obsahuje přehled služby Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 06/10/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 2c3bc596076f3ec4f9d41f0da819ddd386fee63c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809992"
---
# <a name="whats-new-in-azure-migrate"></a>Co je nového ve službě Azure Migrate

[Azure Migrate](migrate-services-overview.md) vám umožní zjišťovat, posuzovat a migrovat servery, aplikace a data do cloudu Microsoft Azure. Tento článek shrnuje nové funkce ve službě Azure Migrate.



## <a name="azure-migrate-new-version"></a>Nová verze služby Azure Migrate

V červenci 2019 byla vydána nová verze Azure Migrate. 

- **Aktuální verze (nové)** : Pomocí této verze můžete vytvářet projekty Azure Migrate, zjišťování místních počítačů a orchestrovat migrace a posouzení. 
- **Předchozí verze**: Pro zákazníky pomocí předchozí verze služby Azure Migrate (se podporuje pouze posouzení místních virtuálních počítačů VMware) byste teď měli použít aktuální verzi. V předchozí verzi můžete už vytvořit nové projekty Azure Migrate nebo provedení nové zjišťování. Budete k němu přístup existujících projektů. Chcete-li to udělat na webu Azure Portal > vyhledat všechny služby Azure Migrate. V oznámení Azure Migrate je odkaz pro přístup k projektům staré Azure Migrate.


## <a name="azure-migrate-features"></a>Funkce Azure Migrate

Nová verze Azure Migrate nabízí celou řadu nových funkcí:


- **Sjednocené migrace platformy**: Azure Migrate nyní poskytuje jediného portálu pro centralizovat, Správa a sledování průběhu migrace do Azure, tok vylepšené nasazení a práce s portálem.
- **Nástroje pro vyhodnocení a migraci**: Azure Migrate nabízí nástroje pro nativní a integruje se s dalšími službami Azure, stejně jako nástroje pro nezávislé výrobce softwaru výrobce (ISV). [Další informace](migrate-services-overview.md#isv-integration) o integraci nezávislý výrobce softwaru.
- **Vyhodnocení Azure Migrate**: Pomocí nástroje Azure Migrate Server Assessment, můžete vyhodnotit virtuálních počítačů Hyper-V a virtuálních počítačů VMware pro migraci do Azure. Také můžete vyhodnotit migraci pomocí jiných služeb Azure a nástroje pro nezávislé výrobce softwaru.
- **Migrace do Azure Migrate**: Pomocí nástroje Azure Migrate serveru migrace, můžete migrovat místní virtuální počítače VMware a virtuálních počítačů Hyper-V do Azure, jakož i fyzických serverů, jiné virtualizované servery a soukromého a veřejného cloudu virtuálních počítačů. Kromě toho můžete migrovat do Azure pomocí nástroje pro nezávislé výrobce softwaru.
- **Zařízení Azure Migrate**: Azure Migrate nasadí zjednodušené zařízení pro zjišťování a posouzení místních virtuálních počítačů VMware a virtuálních počítačů Hyper-V.
    - Toto zařízení se používá posouzení migrace serveru Azure a Azure Migrate serveru migrace pro migraci bez agentů.
    - Zařízení průběžně zjišťuje data metadata a výkonu serveru, pro účely vyhodnocení a migraci.  
- **Migrace virtuálních počítačů VMware**:  Migrace serveru do Azure migrovat poskytuje několik metod pro migraci místních virtuálních počítačů VMware do Azure.  Migrace do bez agentů pomocí zařízení Azure Migrate a migrace založené na agentovi, který používá replikace zařízení a nasadí určitého agenta na každý virtuální počítač, který chcete migrovat. [Víc se uč](server-migrate-overview.md)
 - **Posouzení a migraci databáze**: Azure Migrate můžete posouzení místní databáze pro migraci do Azure s využitím Azure Database Migration Assistant. Můžete migrovat databáze s využitím Azure Database Migration Service.
- **Webová aplikace migrace**: Webové aplikace s využitím veřejného koncového bodu adresy URL ve službě Azure App Service můžete vyhodnotit. Pro migraci z interních aplikací .NET lze stáhnout a spustit aplikaci služby Pomocníka s migrací. 
- **Data Box**: Import offline velkých objemů dat do Azure pomocí Azure Data Box ve službě Azure Migrate.


## <a name="next-steps"></a>Další postup

- [Další informace](https://azure.microsoft.com/pricing/details/azure-migrate/) o cenách služby Azure Migrate.
- [Přečtěte si nejčastější dotazy](resources-faq.md) ohledně služby Azure Migrate.
- Vyzkoušejte si naše kurzy k vyhodnocení [virtuálních počítačů VMware](tutorial-assess-vmware.md) a [virtuálních počítačů Hyper-V](tutorial-assess-hyper-v.md).
