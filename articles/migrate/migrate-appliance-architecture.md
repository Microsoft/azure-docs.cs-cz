---
title: Architektura zařízení Azure Migrate
description: Poskytuje přehled zařízení Azure Migrate používaných při posuzování a migraci serveru.
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: 623790568fb8d86d8065711439f148211fc7fd6b
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514559"
---
# <a name="azure-migrate-appliance-architecture"></a>Architektura zařízení Azure Migrate

Tento článek popisuje architekturu a procesy zařízení Azure Migrate. Zařízení Azure Migrate je odlehčené zařízení, které je nasazené místně, ke zjištění virtuálních počítačů a fyzických serverů pro migraci do Azure. 

## <a name="deployment-scenarios"></a>Scénáře nasazení

Zařízení Azure Migrate se používá v následujících scénářích.

**Scénář** | **Nástroj** | **Použití** 
--- | --- | ---
**Posouzení virtuálního počítače VMware** | Azure Migrate: posouzení serveru | Vyhledejte virtuální počítače VMware.<br/><br/> Objevte aplikace a závislosti počítačů.<br/><br/> Shromažďovat metadata počítačů a metadata o výkonu a odesílat je do Azure.
**Migrace virtuálních počítačů VMware (bez agentů)** | Azure Migrate: Migrace serveru | Zjistit virtuální počítače VMware<br/><br/>  Replikace virtuálních počítačů VMware s [migrací bez agentů](server-migrate-overview.md)
**Posouzení virtuálního počítače Hyper-V** | Azure Migrate: posouzení serveru | Objevte virtuální počítače Hyper-V.<br/><br/> Shromažďovat metadata počítačů a metadata o výkonu a odesílat je do Azure.
**Fyzický počítač** |  Azure Migrate: posouzení serveru |  Objevte fyzické servery.<br/><br/> Shromažďovat metadata počítačů a metadata o výkonu a odesílat je do Azure.

## <a name="appliance-components"></a>Součásti zařízení

Zařízení má řadu součástí.

- **Aplikace pro správu**: Toto je webová aplikace pro vstup uživatele během nasazování zařízení. Používá se při posuzování počítačů pro migraci do Azure.
- **Agent zjišťování**: Agent shromáždí data konfigurace počítače. Používá se při posuzování počítačů pro migraci do Azure. 
- **Agent kolektoru**: Agent shromažďuje údaje o výkonu. Používá se při posuzování počítačů pro migraci do Azure.
- **Agent DRA**: orchestruje replikaci virtuálních počítačů a koordinuje komunikaci mezi replikovanými počítači a Azure. Používá se jenom při replikaci virtuálních počítačů VMware do Azure pomocí migrace bez agentů.
- **Brána**: odesílá replikovaná data do Azure. Používá se jenom při replikaci virtuálních počítačů VMware do Azure pomocí migrace bez agentů.
- **Služba automatické aktualizace**: aktualizuje součásti zařízení (spouští se každých 24 hodin).



## <a name="appliance-deployment"></a>Nasazení zařízení

- Zařízení Azure Migrate se dá nastavit pomocí šablony pro [Hyper-V](how-to-set-up-appliance-hyper-v.md) nebo [VMware](how-to-set-up-appliance-vmware.md) nebo pomocí instalačního programu skriptu PowerShellu pro [VMware/Hyper-v](deploy-appliance-script.md)a [fyzických serverů](how-to-set-up-appliance-physical.md). 
- Požadavky na podporu zařízení a požadavky na nasazení jsou shrnuty v [matici podpora zařízení](migrate-appliance.md).


## <a name="appliance-registration"></a>Registrace zařízení

Během instalace zařízení zaregistrujete zařízení pomocí Azure Migrate a dojde k akcím shrnutým v tabulce.

**Akce** | **Podrobnosti** | **Oprávnění**
--- | --- | ---
**Registrovat zprostředkovatele zdrojů** | Tito poskytovatelé prostředků jsou zaregistrovaní v předplatném, které jste si zvolili během instalace zařízení: Microsoft. OffAzure, Microsoft. migruje a Microsoft. webtrezor.<br/><br/> Když zaregistrujete poskytovatele prostředků, nakonfigurujete vaše předplatné, aby fungovalo s poskytovatelem prostředků. | Pokud chcete zaregistrovat poskytovatele prostředků, potřebujete roli přispěvatel nebo Owner v předplatném.
**Vytvoření aplikace Azure AD – komunikace** | Azure Migrate vytvoří aplikaci Azure Active Directory (Azure AD) pro komunikaci (ověřování a autorizaci) mezi agenty běžícími na zařízení a jejich příslušnými službami běžícími na Azure.<br/><br/> Tato aplikace nemá oprávnění k provádění Azure Resource Manager volání nebo přístupu RBAC k jakémukoli prostředku. | K vytvoření aplikace budete potřebovat [Tato oprávnění](tutorial-prepare-vmware.md#assign-permissions-to-create-azure-ad-apps) pro Azure Migrate.
**Vytváření aplikací Azure AD – Trezor klíčů** | Tato aplikace se vytvoří jenom pro migraci virtuálních počítačů VMware bez agenta do Azure.<br/><br/> Používá se výhradně pro přístup k trezoru klíčů vytvořenému v předplatném uživatele pro migraci bez agentů.<br/><br/> Má přístup RBAC v trezoru klíčů Azure (vytvořeném v tenantovi zákazníka) při zahájení zjišťování ze zařízení. | K vytvoření aplikace budete potřebovat [Tato oprávnění](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault) pro Azure Migrate.



## <a name="collected-data"></a>Shromážděná data

Data shromážděná klientem pro všechny scénáře nasazení jsou shrnuta v [matici podpora zařízení](migrate-appliance.md).

## <a name="discovery-and-collection-process"></a>Proces zjišťování a shromažďování

![Architektura](./media/migrate-appliance-architecture/architecture1.png)

Zařízení komunikuje s vCenter servery a hostiteli nebo clustery Hyper-V pomocí následujícího procesu.

1. **Spustit zjišťování**:
    - Když zahájíte zjišťování na zařízení Hyper-V, komunikuje s hostiteli Hyper-V na portu WinRM 5985 (HTTP).
    - Když spustíte zjišťování na zařízení VMware, ve výchozím nastavení komunikuje s vCenter serverem na portu TCP 443. Pokud Server vCenter naslouchá na jiném portu, můžete ho nakonfigurovat ve webové aplikaci zařízení.
2. **Shromážděte metadata a data o výkonu**:
    - Zařízení používá relaci model CIM (Common Information Model) (CIM) ke shromažďování dat virtuálních počítačů Hyper-V z hostitele Hyper-V na portu 5985.
    - Zařízení ve výchozím nastavení komunikuje s portem 443, aby mohla shromažďovat data virtuálních počítačů VMware z vCenter Server.
3. **Odeslat data**: zařízení odesílá shromážděná data pro Azure Migrate posouzení serveru a migraci Azure Migrate serveru přes SSL port 443. Zařízení se může připojit k Azure přes Internet nebo můžete použít ExpressRoute s veřejným partnerským vztahem/Microsoftu.
    - Pro data o výkonu zařízení shromažďuje data o využití v reálném čase.
        - Údaje o výkonu se shromažďují každých 20 sekund pro VMware a každých 30 sekund pro Hyper-V pro každou metriku výkonu.
        - Shromážděná data jsou zahrnuta k vytvoření jednoho datového bodu po dobu 10 minut.
        - Hodnota maximálního využití se vybere ze všech 20/30 sekundových datových bodů a pošle se do Azure pro výpočet vyhodnocení.
        - Na základě hodnoty percentilu uvedené ve vlastnostech posouzení (50/90/95./99) se ve vzestupném pořadí seřadí body o deseti minutách a příslušná hodnota percentilu se použije k výpočtu posouzení.
    - U migrace serveru začne zařízení shromažďovat data virtuálních počítačů a replikuje je do Azure.
4. **Vyhodnocení a migrace**: teď můžete z metadat shromážděných zařízením vytvořit posouzení pomocí Azure Migrate posouzení serveru. Kromě toho můžete také začít migrovat virtuální počítače VMware pomocí migrace serveru Azure Migrate pro orchestraci replikace virtuálních počítačů bez agentů.

## <a name="appliance-upgrades"></a>Upgrady zařízení

Zařízení se upgraduje, protože Azure Migrate agenti, kteří běží na zařízení, se aktualizují. K tomu dochází automaticky, protože ve výchozím nastavení je na zařízení povolená Automatická aktualizace. Toto výchozí nastavení můžete změnit tak, aby se agenti aktualizovaly ručně.

Automatickou aktualizaci vypnete v registru nastavením klíče HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\AzureAppliance "AutoUpdate" na hodnotu 0 (DWORD).


## <a name="next-steps"></a>Další kroky

[Projděte si](migrate-appliance.md) matici podpora zařízení.

