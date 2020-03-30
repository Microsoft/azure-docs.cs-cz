---
title: Co je nového v Azure Migrate
description: Přečtěte si, co je nové a poslední aktualizace ve službě Migrace Azure.
ms.topic: overview
ms.date: 03/22/2020
ms.custom: mvc
ms.openlocfilehash: 9767f3ea31b57d23c8a6772ff5eb6500f7550802
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80127595"
---
# <a name="whats-new-in-azure-migrate"></a>Co je nového v Azure Migrate

[Migrace Azure](migrate-services-overview.md) vám pomůže zjišťovat, vyhodnocovat a migrovat místní servery, aplikace a data do cloudu Microsoft Azure. Tento článek shrnuje nové verze a funkce v Azure Migrate.

## <a name="update-march-2020"></a>Aktualizace (březen 2020)

Instalace založená na skriptech je teď k dispozici pro nastavení [zařízení Azure Migrate](migrate-appliance.md):

- Instalace založená na skriptech je alternativou k aplikaci . OVA (VMware)/VHD (Hyper-V) instalace přístroje.
- Poskytuje instalační skript prostředí PowerShell, který lze použít k nastavení zařízení pro VMware/Hyper-V na existujícím počítači se systémem Windows Server 2016.

## <a name="update-november-2019"></a>Aktualizace (listopad 2019)

Do migrace Azure byla přidána řada nových funkcí:

- **Hodnocení fyzického serveru**. Kromě fyzické migrace serveru, která je již podporována, je nyní podporováno i hodnocení místních fyzických serverů.
- **Posouzení založené na dovozu**. Nyní je podporováno hodnocení počítačů pomocí metadat a údajů o výkonu poskytovaných v souboru CSV.
- **Zjišťování aplikací**: Azure Migrate teď podporuje zjišťování aplikací, rolí a funkcí na úrovni aplikací pomocí zařízení Azure Migrate. To je aktuálně podporované pouze pro virtuální zařízení VMware a je omezena pouze na zjišťování (hodnocení není aktuálně podporováno). [Další informace](how-to-discover-applications.md)
- **Vizualizace závislostí bez agenta**: Již není nutné explicitně instalovat agenty pro vizualizaci závislostí. Jsou nyní podporovány bez agenta a na základě agenta.
- **Virtuální plocha:** Pomocí nástrojů pro řešení softwaru můžete vyhodnocovat a migrovat místní infrastrukturu virtuálních desktopů (VDI) do virtuální plochy Windows v Azure.
- **Webová aplikace**: Asistent migrace služby Azure App Service, který se používá k posuzování a migraci webových aplikací, se teď integruje do Migrace Azure.

Do Migrace Azure byly přidány nové nástroje pro hodnocení a migraci:

- **Rackware**: Nabízí migraci do cloudu.
- **Movere**: Nabídka hodnocení.

[Přečtěte si další informace](migrate-services-overview.md) o používání nástrojů a nabídek nerevitu pro hodnocení a migraci v Azure Migrate.

## <a name="azure-migrate-current-version"></a>Azure migrovat aktuální verzi

Aktuální verze Azure Migrate (vydané v červenci 2019) poskytuje řadu nových funkcí:

- **Sjednocená migrační platforma**: Azure Migrate teď poskytuje jediný portál pro centralizaci, správu a sledování vaší cesty k migraci do Azure s vylepšeným tokem nasazení a prostředím portálu.
- **Nástroje pro hodnocení a migraci**: Azure Migrate poskytuje nativní nástroje a integruje se s dalšími službami Azure a také s nástroji nezávislých dodavatelů softwaru (ISV). [Přečtěte si další informace](migrate-services-overview.md#isv-integration) o integraci isv.
- **Azure Migrate assessment**: Pomocí nástroje Azure Migrate Server Assessment můžete vyhodnotit virtuální počítače VMware a virtuální počítače Hyper-V pro migraci do Azure. Můžete také posoudit migraci pomocí jiných služeb Azure a nástrojů pro nenohování.
- **Migrace Migrace Azure:** Pomocí nástroje Migrace serveru Azure můžete migrovat místní virtuální počítače VMware a virtuální počítače Hyper-V do Azure, stejně jako fyzické servery, další virtualizované servery a virtuální počítače privátního/veřejného cloudu. Kromě toho můžete migrovat do Azure pomocí nástrojů pro nenožení.
- **Zařízení migrace Azure:** Azure Migrate nasazuje zjednodušené zařízení pro zjišťování a hodnocení místních virtuálních počítačů VMware a virtuálních počítačích Hyper-V.
    - Toto zařízení se používá Azure Migrate Server Assessment a Migrace migrace serveru Azure pro migraci bez agenta.
    - Zařízení průběžně zjišťuje metadata serveru a údaje o výkonu pro účely posouzení a migrace.  
- **Migrace virtuálních zařízení VMware**: Migrace serveru Azure poskytuje několik metod pro migraci místních virtuálních počítačů VMware do Azure.  Migrace bez agenta pomocí zařízení Azure Migrate a migrace na základě agenta, která používá zařízení replikace a nasazuje agenta na každý virtuální počítač, který chcete migrovat. [Další informace](server-migrate-overview.md)
 - **Vyhodnocení a migrace databáze**: Z migrace Azure můžete posoudit místní databáze pro migraci do Azure pomocí Pomocníka pro migraci databáze Azure. Databáze můžete migrovat pomocí služby Migrace databáze Azure.
- **Migrace webových aplikací**: Pomocí služby Azure App Service můžete vyhodnocovat webové aplikace pomocí adresy URL veřejného koncového bodu. Pro migraci interních aplikací .NET můžete stáhnout a spustit Pomocníka pro migraci služby App Service.
- **Datová schránka:** Importujte velké množství offline dat do Azure pomocí Azure Data Box v Azure Migrate.

## <a name="azure-migrate-previous-version"></a>Azure migrovat předchozí verzi

Pokud jste používali předchozí verzi Azure Migrate (bylo podporováno jenom hodnocení místních virtuálních počítačích VMware), měli byste teď používat aktuální verzi. V předchozí verzi už nemůžete vytvářet nové projekty Migrace Azure ani provádět nové zjišťování. Stále můžete přistupovat k existujícím projektům. Chcete-li to provést na webu Azure Portal > **Všechny služby**, **vyhledejte Azure Migrate**. V oznámeních migrace Azure je odkaz pro přístup ke starým projektům Migrace Azure.



## <a name="next-steps"></a>Další kroky

- [Další informace](https://azure.microsoft.com/pricing/details/azure-migrate/) o cenách služby Azure Migrate.
- [Přečtěte si nejčastější dotazy](resources-faq.md) ohledně služby Azure Migrate.
- Vyzkoušejte naše výukové programy k posouzení [virtuálních měn VMware](tutorial-assess-vmware.md) a [virtuálních virtuálních měn Hyper-V](tutorial-assess-hyper-v.md).
