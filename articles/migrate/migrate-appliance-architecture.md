---
title: Architektura zařízení Azure Migrate
description: Obsahuje přehled zařízení Azure Migrate používaného při hodnocení serveru a migraci.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: d55d123bb056b46b5e78dd8ac836eeaf9b42fe70
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389014"
---
# <a name="azure-migrate-appliance-architecture"></a>Architektura zařízení Azure Migrate

Tento článek popisuje architekturu a procesy zařízení Azure Migrate. Zařízení Azure Migrate je zjednodušené zařízení, které se nasadí místně, aby bylo vidět virtuální počítače a fyzické servery pro migraci do Azure. 

## <a name="deployment-scenarios"></a>Scénáře nasazení

Zařízení Migrace Azure se používá v následujících scénářích.

**Scénář** | **Nástroj** | **Použití** 
--- | --- | ---
**Vyhodnocení virtuálního vana v vvware vv** | Migrace Azure:Vyhodnocení serveru | Objevte virtuální mise VMware.<br/><br/> Seznamte se s aplikacemi a závislostmi počítače.<br/><br/> Shromažďujte metadata počítače a informace o výkonu a odesílejte je do Azure.
**Migrace virtuálního zařízení VMware (bez agentů)** | Migrace Azure:Migrace serveru | Objevte virtuální mise VMware<br/><br/>  Replikujte virtuální virtuální mame VMware pomocí [migrace bez agenta](server-migrate-overview.md).
**Vyhodnocení virtuálního aplikace Hyper-V** | Migrace Azure:Vyhodnocení serveru | Objevte virtuální aplikace Hyper-V.<br/><br/> Shromažďujte metadata počítače a informace o výkonu a odesílejte je do Azure.
**Fyzický stroj** |  Migrace Azure:Vyhodnocení serveru |  Objevte fyzické servery.<br/><br/> Shromažďujte metadata počítače a informace o výkonu a odesílejte je do Azure.

## <a name="appliance-components"></a>Součásti spotřebičů

Přístroj má řadu součástí.

- **Aplikace pro správu**: Jedná se o webovou aplikaci pro vstup uživatele během nasazení zařízení. Používá se při posuzování počítačů pro migraci do Azure.
- **Agent zjišťování**: Agent shromažďuje data konfigurace počítače. Používá se při posuzování počítačů pro migraci do Azure. 
- **Agent hodnocení**: Agent shromažďuje údaje o výkonu. Používá se při posuzování počítačů pro migraci do Azure.
- **DRA agent:** Orchestruje replikaci virtuálních počítačů a koordinuje komunikaci mezi replikované počítače a Azure. Používá se pouze při replikaci virtuálních počítačů VMware do Azure pomocí migrace bez agenta.
- **Brána:** Odešle replikovaná data do Azure. Používá se pouze při replikaci virtuálních počítačů VMware do Azure pomocí migrace bez agenta.
- **Služba automatické aktualizace**: Aktualizuje součásti zařízení (běží každých 24 hodin).



## <a name="appliance-deployment"></a>Nasazení zařízení

- Zařízení Migrace Azure lze nastavit pomocí šablony pro [technologie Hyper-V](how-to-set-up-appliance-hyper-v.md) nebo [VMware](how-to-set-up-appliance-vmware.md) nebo pomocí instalačního programu skriptu PowerShell pro [vmware/hyper-V](deploy-appliance-script.md)a pro [fyzické servery](how-to-set-up-appliance-physical.md). 
- Požadavky na podporu zařízení a požadavky na nasazení jsou shrnuty v [matici podpory zařízení](migrate-appliance.md).


## <a name="appliance-registration"></a>Registrace spotřebiče

Během instalace zařízení zaregistrujete zařízení pomocí Migrace Azure a dojde k akcím sumarovaným v tabulce.

**Akce** | **Podrobnosti** | **Oprávnění**
--- | --- | ---
**Zaregistrovat poskytovatele zdrojů** | Tito zprostředkovatelé prostředků jsou registrováni v předplatném, které zvolíte během instalace zařízení: Microsoft.OffAzure, Microsoft.Migrate a Microsoft.KeyVault.<br/><br/> Registrace poskytovatele prostředků nakonfiguruje vaše předplatné tak, aby fungovalo s poskytovatelem prostředků. | Chcete-li zaregistrovat zprostředkovatele prostředků, potřebujete roli přispěvatele nebo vlastníka v předplatném.
**Vytvoření komunikace aplikací Azure AD** | Azure Migrate vytvoří aplikaci Azure Active Directory (Azure AD) pro komunikaci (ověřování a autorizaci) mezi agenty spuštěným na zařízení a jejich příslušnými službami spuštěným v Azure.<br/><br/> Tato aplikace nemá oprávnění k volání Azure Resource Manager nebo přístup RBAC na libovolný prostředek. | K vytvoření aplikace potřebujete [tato oprávnění](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) pro Migraci Azure.
**Vytvoření trezoru aplikací Azure AD s klíči** | Tato aplikace se vytvoří jenom pro migraci virtuálních počítačích VMware do Azure bez agentů.<br/><br/> Používá se výhradně pro přístup k trezoru klíčů vytvořeného v předplatném uživatele pro migraci bez agenta.<br/><br/> Má přístup RBAC v trezoru klíčů Azure (vytvořené v tenantovi zákazníka), při zjišťování je zahájeno ze zařízení. | K vytvoření aplikace potřebujete [tato oprávnění](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) pro Migraci Azure.



## <a name="collected-data"></a>Shromážděná data

Data shromážděná klientem pro všechny scénáře nasazení jsou shrnuta v [matici podpory zařízení](migrate-appliance.md).

## <a name="discovery-and-collection-process"></a>Proces zjišťování a sběru

![Architektura](./media/migrate-appliance-architecture/architecture.png)

Zařízení komunikuje se servery vCenter a hostiteli/clustery Technologie Hyper-V pomocí následujícího procesu.

1. **Spustit zjišťování**:
    - Při spuštění zjišťování na zařízení Hyper-V komunikuje s hostiteli Hyper-V na portech WinRM 5985 (HTTP) a 5986 (HTTPS).
    - Když začnete zjišťování na zařízení VMware, komunikuje se serverem vCenter na portu TCP 443 ve výchozím nastavení. Pokud server vCenter naslouchá na jiném portu, můžete jej nakonfigurovat ve webové aplikaci zařízení.
2. **Shromážděte metadata a údaje o výkonu**:
    - Zařízení používá relaci běžného informačního modelu (CIM) ke shromažďování dat virtuálního počítače Hyper-V z hostitele Hyper-V na portech 5985 a 5986.
    - Zařízení ve výchozím nastavení komunikuje s portem 443 a shromažďuje data virtuálního zařízení VMware ze serveru vCenter.
3. **Odeslat data**: Zařízení odešle shromážděná data do Azure Migrate Server Assessment a Migrace serveru Azure přes port SSL 443. Zařízení se může připojit k Azure přes internet, nebo můžete použít ExpressRoute s veřejným partnerem nebo partnerem Microsoftu.
    - Pro údaje o výkonu zařízení shromažďuje data o využití v reálném čase.
        - Údaje o výkonu se shromažďují každých 20 sekund pro vmware a každých 30 sekund pro Hyper-V pro každou metriku výkonu.
        - Shromážděná data jsou shrnuta nahoru a vytvoří se jeden datový bod po dobu 10 minut.
        - Hodnota využití ve špičce je vybraná ze všech 20/30sekundových datových bodů a odeslána do Azure k výpočtu hodnocení.
        - Na základě hodnoty percentilu zadané ve vlastnostech hodnocení (50.90.90.95.99.) jsou desetminutové body seřazeny vzestupně a příslušná hodnota percentilu se používá k výpočtu hodnocení.
    - Pro migraci serveru zařízení začne shromažďovat data virtuálních počítačí a replikuje je do Azure.
4. **Vyhodnoťte a migrujte**: Nyní můžete vytvářet hodnocení z metadat shromážděných zařízením pomocí Azure Migrate Server Assessment. Kromě toho můžete také začít migrovat virtuální počítače VMware pomocí migrace serveru Azure migrace orchestrace replikace virtuálních počítačů bez agenta.





## <a name="appliance-upgrades"></a>Vylepšení zařízení

Zařízení je upgradováno jako agenti Azure Migrate spuštěné na zařízení jsou aktualizovány. K tomu dochází automaticky, protože ve výchozím nastavení je v zařízení povolena automatická aktualizace. Toto výchozí nastavení můžete změnit tak, aby agenti aktualizovali ručně.

Automatickou aktualizaci v registru vypnete nastavením klíče HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\MicrosoftAppliance "AutoUpdate" na 0 (DWORD).

 

## <a name="next-steps"></a>Další kroky

[Zkontrolujte](migrate-appliance.md) matici podpory zařízení.

