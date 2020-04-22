---
title: Informace o službě Azure Migrate
description: Přečtěte si o službě Migrace Azure.
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: fe6386346282cf182397f6420c541d629ba0aab3
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768396"
---
# <a name="about-azure-migrate"></a>Informace o službě Azure Migrate

Tento článek poskytuje rychlý přehled služby Azure Migrate.

Azure Migrate poskytuje centralizované centrum pro hodnocení a migraci na místní servery, infrastrukturu, aplikace a data Azure.

Azure Migrate poskytuje následující funkce:

- **Sjednocená migrační platforma:** Jeden portál pro spuštění, spuštění a sledování migrace do Azure.
- **Řada nástrojů**: Řada nástrojů pro hodnocení a migraci. Mezi nástroje patří Migrace Azure: Vyhodnocení serveru a Migrace Azure: Migrace serveru. Azure Migrate se integruje s dalšími službami Azure a s dalšími nástroji a nezávislými dodavateli softwaru (ISV).
- **Hodnocení a migrace**: V centru Migrace Azure můžete vyhodnocovat a migrovat:
    - **Servery:** Vyhodnoťte místní servery a migrujte je do virtuálních počítačů Azure.
    - **Databáze**: Vyhodnoťte místní databáze a migrujte je do Azure SQL Database nebo do spravované instance Azure SQL Database.
    - **Webové aplikace**: Vyhodnoťte místní webové aplikace a migrujte je do služby Azure App Service pomocí Pomocníka pro migraci služby Azure App Service.
    - **Virtuální plochy:** Vyhodnoťte místní infrastrukturu virtuálních desktopů (VDI) a migrujte ji do Virtuální plochy Windows v Azure.
    - **Data:** Migrace velkého množství dat do Azure rychle a nákladově efektivně pomocí produktů Azure Data Box.

## <a name="integrated-tools"></a>Integrované nástroje

Centrum Migrace Azure obsahuje tyto nástroje:

**Nástroj** | **Posoudit a migrovat** | **Podrobnosti**
--- | --- | ---
**Migrace Azure: Vyhodnocení serveru** | Posoudit servery. | Objevte a vyhodnoťte místní virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery v rámci přípravy na migraci do Azure.
**Migrace Azure: Migrace serveru** | Migrovat servery. | Migrace virtuálních počítačů VMware, virtuálních počítačů Hyper-V, fyzických serverů, dalších virtualizovaných počítačů a virtuálních počítačů veřejného cloudu do Azure.
**Data Migration Assistant** | Vyhodnoťte místní databáze SQL Serveru pro migraci do Azure SQL Database, instance spravované azure SQL database nebo virtuálních monů Azure se systémem SQL Server. | Pomocník pro migraci dat pomáhá určit potenciální problémy s blokováním migrace. Identifikuje nepodporované funkce, nové funkce, které vám po migraci mohou být přínosem, a správnou cestu pro migraci databáze. [Další informace](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).
**Azure Database Migration Service** | Migrujte místní databáze do virtuálních aplikací Azure se spuštěným SQL Serverem, Azure SQL Database nebo spravovanými instancemi Azure SQL Database. | [Přečtěte si další informace](https://docs.microsoft.com/azure/dms/dms-overview) o službě Migrace databáze.
**Movere** | Posoudit servery. | [Přečtěte si další informace](#movere) o Movere.
**Pomocník pro migraci webové aplikace** | Vyhodnoťte místní webové aplikace a migrujte je do Azure. |  Pomocí Pomocníka pro migraci služby Azure App Service můžete posoudit místní weby pro migraci do služby Azure App Service.<br/><br/> Pomocí Pomocníka pro migraci můžete migrovat webové aplikace .NET a PHP do Azure. [Přečtěte si další informace](https://appmigration.microsoft.com/) o Asistenti migrace služby Azure App Service.
**Azure Data Box** | Migrujte offline data. | Pomocí produktů Azure Data Box můžete přesunout velké množství offline dat do Azure. [Další informace](https://docs.microsoft.com/azure/databox/).

> [!NOTE]
> Pokud jste ve službě Azure Government, externí integrované nástroje a nabídky neveřejných virtuálních i nepřístupnících nelze odesílat data do projektů Migrace Azure. Nástroje můžete používat nezávisle.

## <a name="isv-integration"></a>Integrace isv

Azure Migrate se integruje s několika nabídkami isv. 

**Isv**    | **Funkce**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrovat servery.
[Cloudamize](https://www.cloudamize.com/platform) | Posoudit servery.
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Vyhodnoťte a migrujte servery.
[Device42](https://docs.device42.com/) | Posoudit servery.
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Posoudit VDI.
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrovat servery.
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Posoudit servery.
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Vyhodnoťte servery a databáze.

## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate: Nástroj pro vyhodnocení serveru

Nástroj Migrace Azure: Nástroj vyhodnocení serveru zjišťuje a vyhodnocuje místní virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery pro migraci do Azure.

Zde je to, co nástroj dělá:

- **Připravenost Azure**: Vyhodnotí, jestli jsou místní počítače připravené k migraci do Azure.
- **Velikost Azure**: Odhadne velikost virtuálních počítačích Azure po migraci.
- **Odhad nákladů Azure**: Odhadne náklady na provoz místních serverů v Azure.
- **Analýza závislostí**: Identifikuje závislosti mezi servery a strategie optimalizace pro přesun vzájemně závislých serverů do Azure. Další informace o vyhodnocení serveru pomocí [analýzy závislostí](concepts-dependency-visualization.md).

Vyhodnocení serveru používá zjednodušené [zařízení Azure Migrate,](migrate-appliance.md) které nasadíte místně.

- Zařízení běží na virtuálním počítači nebo fyzickém serveru. Můžete jej snadno nainstalovat pomocí stažené šablony.
- Přístroj zjišťuje místní stroje. Také průběžně odesílá metadata počítače a data o výkonu do Migrace Azure.
- Zjišťování zařízení je bez agenta. V zjištěných počítačích není nic nainstalováno.
- Po zjišťování zařízení můžete shromáždit zjištěné počítače do skupin a spustit hodnocení pro každou skupinu.

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate: Nástroj pro migraci serveru

Nástroj Migrace Azure: Migrace serveru vám pomůže migrovat do Azure:

- Místní virtuální počítače VMware
- Virtuální počítače Hyper-V
- Fyzické servery
- Ostatní virtualizované počítače
- Virtuální aplikace veřejného cloudu

Počítače můžete migrovat po jejich vyhodnocení nebo je migrovat bez posouzení.

Pro migraci virtuálních počítačů VMware bez agenta a migraci virtuálních počítačů Hyper-V používá migrace serveru zařízení Azure Migrate, které nasadíte místně. Zařízení se používá také v případě, že nastavíte posouzení serveru. Je to popsáno v předchozí části.

## <a name="selecting-assessment-and-migration-tools"></a>Výběr nástrojů pro hodnocení a migraci

V centru Migrace Azure vyberete nástroj, který chcete použít pro hodnocení nebo migraci, a přidáte ho do projektu Migrace Azure. Pokud přidáte nástroj ISV nebo Movere:

- Chcete-li začít, získat licenci nebo se zaregistrovat na bezplatnou zkušební verzi podle pokynů k nástroji. Každý isv nebo nástroj určuje licence nástroje.
- Každý nástroj má možnost připojení k Azure Migrate. Připojte se podle pokynů k nástroji.
- Sledujte migraci napříč všemi nástroji z projektu Azure Migrate.

## <a name="movere"></a>Movere

Movere je platforma pro software jako služba (SaaS). Zvyšuje business intelligence tím, že přesně prezentuje celé IT prostředí během jednoho dne. Organizace a podniky rostou, mění se a digitálně optimalizují. Movere jim přitom poskytuje potřebnou jistotu, aby mohli vidět a ovládat svá prostředí bez ohledu na platformu, aplikaci nebo geografii.

Společnost Microsoft [získala](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) movere a již se neprodává jako samostatná nabídka. Movere je k dispozici prostřednictvím microsoft solution assessment a Microsoft Cloud Economics Program. [Přečtěte si další informace](https://www.movere.io) o Movere.

Doporučujeme vám, abyste se také podívali na Azure Migrate, naši integrovanou službu migrace. Azure Migrate poskytuje centrální rozbočovač pro zjednodušení migrace do cloudu. Centrum komplexně podporuje úlohy, jako jsou fyzické a virtuální servery, databáze a aplikace. Viditelnost od konce umožňuje snadno sledovat průběh zjišťování, hodnocení a migrace.

Azure Migrate má bohatou škálu funkcí, včetně:

- Zjišťování virtuálních a fyzických serverů.
- Na základě výkonu rightsizing.
- Plánování nákladů.
- Hodnocení založená na dovozu.
- Analýza závislostí aplikací bez agenta.

Pokud hledáte odbornou pomoc, která vám pomůže začít, má společnost Microsoft kvalifikované [poskytovatele spravovaných služeb Azure Expert Managed Service,](https://azure.microsoft.com/partners) kteří vás provedou. Podívejte se na [web Migrace Azure](https://azure.microsoft.com/services/azure-migrate/). 

## <a name="azure-migrate-versions"></a>Verze migrace Azure

Existují dvě verze služby Azure Migrate.

- **Aktuální verze**: Tato verze slouží k vytváření projektů Migrace Azure, zjišťování místních počítačů a orchestraci hodnocení a migrace. [Přečtěte si další informace](whats-new.md) o novince v této verzi.
- **Předchozí verze**: Předchozí verze Azure Migrate podporuje jenom hodnocení místních virtuálních počítačích VMware. Pokud jste použili předchozí verzi, měli byste nyní použít aktuální verzi. Projekty Migrace Azure už nelze vytvářet pomocí předchozí verze. A doporučujeme, abyste s ním nedělali nové objevy.

    Chcete-li získat přístup k existujícím projektům na webu Azure Portal, vyhledejte a vyberte **Azure Migrate**. Řídicí panel **Migrace Azure** má oznámení a odkaz pro přístup ke starým projektům Migrace Azure.

## <a name="next-steps"></a>Další kroky

- Vyzkoušejte naše výukové programy k posouzení [virtuálních měn VMware](tutorial-prepare-vmware.md), [virtuálních zařízení Hyper-V](tutorial-prepare-hyper-v.md)nebo [fyzických serverů](tutorial-prepare-physical.md).
- [Přečtěte si nejčastější dotazy](resources-faq.md) ohledně služby Azure Migrate.
