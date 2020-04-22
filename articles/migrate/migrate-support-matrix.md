---
title: Matice podpory migrace Azure
description: Obsahuje souhrn nastavení podpory a omezení pro službu Azure Migrate.
ms.topic: conceptual
ms.date: 04/19/2020
ms.author: raynew
ms.openlocfilehash: 44a971894f53a3f31c068b3c3ed4912bc7e00dab
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680697"
---
# <a name="azure-migrate-support-matrix"></a>Matice podpory migrace Azure

[Službu Azure Migrate](migrate-overview.md) můžete použít k posouzení a migraci počítačů do cloudu Microsoft Azure. Tento článek shrnuje obecné nastavení podpory a omezení pro scénáře a nasazení Migrace Azure.

## <a name="supported-assessmentmigration-scenarios"></a>Podporované scénáře hodnocení/migrace

Tabulka shrnuje podporované scénáře zjišťování, hodnocení a migrace.

**Nasazení** | **Podrobnosti** 
--- | --- 
**Zjišťování specifické pro aplikaci** | Můžete objevovat aplikace, role a funkce spuštěné na virtuálních počítačích VMware. V současné době je tato funkce omezena pouze na zjišťování. Hodnocení je v současné době na úrovni stroje. Zatím nenabízíme hodnocení aplikací, rolí nebo funkcí. 
**Místní hodnocení** | Vyhodnoťte místní úlohy a data spuštěná na virtuálních počítačích VMware, virtuálních počítačích Hyper-V a fyzických serverech. Vyhodnoťte pomocí Azure Migrate Server Assessment a Microsoft Data Migration Assistant (DMA), stejně jako další nástroje a nabídky neodvýrobci neřídit si je.
**Místní migrace do Azure** | Migrujte úlohy a data spuštěná na fyzických serverech, virtuálních počítačích VMware, virtuálních počítačích Hyper-V, fyzických serverech a cloudových virtuálních počítačích do Azure. Migrujte pomocí Azure Migrate Server Assessment a Azure Database Migration Service (DMS) a dalších nástrojů a nabídek neřídit si neřídit isv.

> [!NOTE]
> V současné době nástroje isv nelze odesílat data do Azure Migrate v Azure Government. Můžete použít integrované nástroje Společnosti Microsoft nebo používat partnerské nástroje nezávisle.

## <a name="supported-tools"></a>Podporované nástroje

Zvláštní podpora nástrojů je shrnuta v tabulce.

**Nástroj** | **Posoudit** | **Migrace** 
--- | --- | ---
Vyhodnocení serveru migrace Azure | Vyhodnoťte [virtuální mandy VMware](tutorial-prepare-vmware.md), [virtuální zařízení Hyper-V](tutorial-prepare-hyper-v.md)a fyzické [servery](tutorial-prepare-physical.md). |  Není k dispozici (NA)
Migrace serverů Azure Migrate | Není k dispozici | Migrace [virtuálních měn VMware](tutorial-migrate-vmware.md), [virtuálních zařízení Hyper-V](tutorial-migrate-hyper-v.md)a [fyzických serverů](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | Není k dispozici | Migrace virtuálních měn VMware, virtuálních zařízení Hyper-V, fyzických serverů, úloh veřejného cloudu. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Vyhodnoťte virtuální zařízení VMware, virtuální chod YF, fyzické servery, úlohy veřejného cloudu. | Není k dispozici
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | Vyhodnoťte a migrujte virtuální chod v systému VMware, virtuální chod YF, fyzické servery, úlohy veřejného cloudu. |  Migrace virtuálních měn VMware, virtuálních zařízení Hyper-V, fyzických serverů, úloh veřejného cloudu.
[Zařízení 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Vyhodnoťte virtuální zařízení VMware, virtuální chod YF, fyzické servery, úlohy veřejného cloudu.| Není k dispozici
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | Vyhodnoťte místní databáze serveru SQL Server. | Není k dispozici
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | Není k dispozici | Migrovat SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Posouzení infrastruktury virtuálních desktopů (VDI) | Není k dispozici
[Movere](https://go.microsoft.com/fwlink/?linkid=2109528) | Vyhodnoťte virtuální počítače VMWare, virtuální počítače Hyper-V, xenové virtuální počítače, fyzické počítače, pracovní stanice (včetně VDI), úlohy veřejného cloudu | Není k dispozici
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Není k dispozici | Migrace virtuálních počítačů VMWare, virtuálních počítačů Hyper-V, xenových virtuálních počítačů, virtuálních počítačů KVM, fyzických počítačů, úloh veřejného cloudu 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Vyhodnoťte virtuální zařízení VMware, virtuální chod YF, fyzické servery, úlohy veřejného cloudu. | Není k dispozici
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Vyhodnoťte virtuální moduly VMware, virtuální zařízení Hyper-V, fyzické servery, úlohy veřejného cloudu a databáze SQL Serveru. | Není k dispozici
[Pomocník pro migraci webapp](https://appmigration.microsoft.com/) | Posouzení webových aplikací | Migrace webových aplikací.


## <a name="azure-migrate-projects"></a>Projekty migrace Azure

**Podpora** | **Podrobnosti**
--- | ---
Předplatné | Můžete mít více projektů Migrace Azure v předplatném.
Oprávnění Azure | K vytvoření projektu Migrace Azure potřebujete oprávnění přispěvatele nebo vlastníka v předplatném.
Virtuální počítače VMware  | Vyhodnoťte až 35 000 virtuálních měn VMware v jednom projektu.
Virtuální počítače Hyper-V    | Vyhodnoťte až 35 000 virtuálních virtuálních<-v Hyper-V v jednom projektu.

Projekt může zahrnovat virtuální zařízení VMware i virtuální zařízení Hyper-V až do limitů hodnocení.

## <a name="azure-permissions"></a>Oprávnění Azure

Aby Azure Migroval, aby fungoval s Azure, potřebujete tato oprávnění, než začnete vyhodnocovat a migrovat počítače.

**Úkol** | **Oprávnění** | **Podrobnosti**
--- | --- | ---
Vytvoření projektu migrace Azure | Váš účet Azure potřebuje oprávnění k vytvoření projektu. | Nastavení pro [servery VMware](tutorial-prepare-vmware.md#assign-permissions-to-create-project), [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-create-project)nebo [fyzické servery](tutorial-prepare-physical.md#assign-permissions-to-create-project).
Registrace zařízení Azure Migrate| Azure Migrate používá zjednodušené [zařízení Azure Migrate](migrate-appliance.md) k posouzení počítačů s Azure Migrate Server Assessment a ke spuštění migrace virtuálních počítačů VMware bez [agenta](server-migrate-overview.md) s migrací serveru Azure. Toto zařízení zjišťuje počítače a odesílá metadata a data o výkonu do Migrace Azure.<br/><br/> Během registrace jsou poskytovatelé registru (Microsoft.OffAzure, Microsoft.Migrate a Microsoft.KeyVault) registrováni s předplatným zvoleným v zařízení, takže předplatné spolupracuje s poskytovatelem prostředků. Chcete-li se zaregistrovat, potřebujete přístup přispěvatele nebo vlastníka k předplatnému.<br/><br/> **VMware**– během registrace azure migrate vytvoří dvě aplikace Azure Active Directory (Azure AD). První aplikace komunikuje mezi agenty zařízení a službou Azure Migrate. Aplikace nemá oprávnění k volání azure správy prostředků nebo mají přístup RBAC pro prostředky. Druhá aplikace přistupuje k trezoru klíčů Azure vytvořenév rámci předplatného uživatele pouze pro migraci VMware bez agenta. Při migraci bez agenta azure migrate vytvoří trezor klíčů pro správu přístupových klíčů k účtu úložiště replikace ve vašem předplatném. Má přístup RBAC na Azure Key Vault (v tenantovi zákazníka) při zjišťování je zahájeno ze zařízení.<br/><br/> **Hyper-V**-Během onboardingu. Azure Migrate vytvoří jednu aplikaci Azure AD. Aplikace komunikuje mezi agenty zařízení a službou Azure Migrate. Aplikace nemá oprávnění k volání azure správy prostředků nebo mají přístup RBAC pro prostředky. | Nastavení pro [servery VMware](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance), [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-register-the-appliance)nebo [fyzické servery](tutorial-prepare-physical.md#assign-permissions-to-register-the-appliance).
Vytvoření trezoru klíčů pro migraci bez agentů společnosti VMware | Chcete-li migrovat virtuální počítače VMware s migrací serveru Azure bez agenta bez agenta, Azure Migrate vytvoří trezor klíčů pro správu přístupových klíčů k účtu úložiště replikace ve vašem předplatném. Chcete-li vytvořit úschovnu, nastavte oprávnění (vlastník nebo přispěvatel a správce přístupu uživatelů) ve skupině prostředků, ve které se nachází projekt Migrace Azure. | [Nastavte](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault) oprávnění.

## <a name="supported-geographies-public-cloud"></a>Podporované zeměpisné oblasti (veřejný cloud)

Projekt Migrace Azure můžete vytvořit v řadě zeměpisných oblastí ve veřejném cloudu. I když můžete vytvářet projekty pouze v těchto zeměpisných oblastech, můžete vyhodnotit nebo migrovat počítače pro jiná cílová umístění. Zeměpis projektu se používá pouze k ukládání zjištěných metadat.

**Geografie** | **Umístění úložiště metadat**
--- | ---
Asie a Tichomoří | Východní Asie nebo jihovýchodní Asie
Austrálie | Austrálie – východ nebo Austrálie – jihovýchod
Brazílie | Brazílie – jih
Kanada | Kanada – střední nebo kanada – východ
Evropa | Severní Evropa nebo Západní Evropa
Francie | Francie – střed
Indie | Střední Indie nebo Jižní Indie
Japonsko |  Japonsko – východ nebo Západ
Jižní Korea | Korea Střední nebo Korea Jižní
Spojené království | Spojené království – jih nebo Velká Británie – západ
Spojené státy | Střední USA nebo západní USA 2


## <a name="supported-geographies-azure-government"></a>Podporované zeměpisné oblasti (Azure Government)

**Úkol** | **Geografie** | **Podrobnosti**
--- | --- | ---
Vytvoření projektu | Spojené státy | Metadata jsou uložena v US Gov Arizona, US Gov Virginia
Cílové posouzení | Spojené státy | Cílové oblasti: US Gov Arizona, US Gov Virginia, US Gov Texas
Cílová replikace | Spojené státy | Cílové oblasti: US DoD Central, US DoD East, US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginia


## <a name="vmware-assessment-and-migration"></a>Hodnocení a migrace společnosti VMware

[Projděte si](migrate-support-matrix-vmware.md) matici vyhodnocení serveru azure a migrace serveru pro virtuální počítače VMware.

## <a name="hyper-v-assessment-and-migration"></a>Vyhodnocení a migrace Hyper-V

[Zkontrolujte](migrate-support-matrix-hyper-v.md) matici vyhodnocení serveru Azure a podpory migrace serveru pro virtuální počítače Hyper-V.



## <a name="azure-migrate-versions"></a>Verze migrace Azure

Existují dvě verze služby Azure Migrate:

- **Aktuální verze**: Pomocí této verze můžete vytvářet nové projekty Migrace Azure, zjišťovat místní hodnocení a organizovat hodnocení a migrace. [Další informace](whats-new.md).
- **Předchozí verze**: Pro zákazníka, kteří používají předchozí verzi Azure Migrate (bylo podporováno jenom posouzení místních virtuálních počítačích VMware), byste teď měli používat aktuální verzi. V předchozí verzi nemůžete vytvářet nové projekty Migrace Azure ani provádět nové zjišťování.

## <a name="next-steps"></a>Další kroky

- [Vyhodnoťte virtuální chod v oblasti VMware](tutorial-assess-vmware.md) pro migraci.
- [Vyhodnoťte virtuální chod Hyper-V](tutorial-assess-hyper-v.md) pro migraci.

