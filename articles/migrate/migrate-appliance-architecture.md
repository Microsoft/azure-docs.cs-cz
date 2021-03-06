---
title: Architektura zařízení Azure Migrate
description: Poskytuje přehled zařízení Azure Migrate používaných při zjišťování serveru, posuzování a migraci.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 4fc71f3242cc5607acebc68b62c5c0565b8f8e56
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715005"
---
# <a name="azure-migrate-appliance-architecture"></a>Architektura zařízení Azure Migrate

Tento článek popisuje architekturu a procesy zařízení Azure Migrate. Zařízení Azure Migrate je odlehčené zařízení, které je nasazené místně, aby bylo možné zjišťovat servery pro migraci do Azure.

## <a name="deployment-scenarios"></a>Scénáře nasazení

Zařízení Azure Migrate se používá v následujících scénářích.

**Scénář** | **Nástroj** | **Používá se pro**
--- | --- | ---
**Zjišťování a posouzení serverů běžících v prostředí VMware** | Azure Migrate: zjišťování a posouzení | Zjišťování serverů běžících ve vašem prostředí VMware<br/><br/> Proveďte zjišťování nainstalovaného inventáře softwaru, analýzu závislostí bez agentů a Objevte SQL Server instance a databáze.<br/><br/> Shromažďovat metadata o konfiguraci serveru a výkonu pro posouzení.
**Migrace serverů se systémem v prostředí VMware bez agentů** | Azure Migrate: Migrace serveru | Objevte servery běžící v prostředí VMware.<br/><br/> Replikace serverů bez instalace agentů.
**Zjišťování a posouzení serverů, které běží v prostředí Hyper-V** | Azure Migrate: zjišťování a posouzení | Objevte servery, které jsou spuštěné ve vašem prostředí Hyper-V.<br/><br/> Shromažďovat metadata o konfiguraci serveru a výkonu pro posouzení.
**Zjišťování a hodnocení fyzických nebo virtualizovaných serverů v místním prostředí** |  Azure Migrate: zjišťování a posouzení |  Objevte fyzické nebo virtualizované servery místně.<br/><br/> Shromažďovat metadata o konfiguraci serveru a výkonu pro posouzení.

## <a name="deployment-methods"></a>Metody nasazení

Zařízení je možné nasadit pomocí několika metod:

- Zařízení se dá nasadit pomocí šablony pro servery, které běží v prostředí VMware nebo Hyper-V ([Šablona vajíček pro VMware](how-to-set-up-appliance-vmware.md) nebo [VHD pro Hyper-v](how-to-set-up-appliance-hyper-v.md)).
- Pokud nechcete používat šablonu, můžete zařízení nasadit pro prostředí VMware nebo Hyper-V pomocí [skriptu instalačního programu PowerShell](deploy-appliance-script.md).
- V Azure Government byste zařízení měli nasadit pomocí skriptu instalačního programu PowerShell. [Tady](deploy-appliance-script-government.md)najdete postup nasazení.
- U fyzických nebo virtualizovaných serverů v místním prostředí nebo v jakémkoli jiném cloudu vždy nasadíte zařízení pomocí skriptu instalačního programu PowerShell. [Tady](how-to-set-up-appliance-physical.md)najdete postup nasazení.
- Odkazy ke stažení jsou k dispozici v následujících tabulkách.

## <a name="appliance-services"></a>Služby zařízení

Zařízení má následující služby:

- **Správce konfigurace zařízení**: Jedná se o webovou aplikaci, která se dá nakonfigurovat se zdrojovými podrobnostmi pro zahájení zjišťování a posouzení serverů. 
- **Agent zjišťování**: Agent shromažďuje metadata konfigurace serveru, která lze použít k vytvoření jako místní hodnocení.
- **Agent hodnocení**: Agent shromažďuje metadata výkonu serveru, která lze použít k vytvoření posouzení na základě výkonu.
- **Služba automatické aktualizace**: služba udržuje všechny agenty spuštěné v zařízení v aktuálním stavu. Automaticky se spustí každých 24 hodin.
- **Agent DRA**: orchestruje replikaci serveru a koordinuje komunikaci mezi replikovanými servery a Azure. Používá se jenom při replikaci serverů do Azure pomocí migrace bez agentů.
- **Brána**: odesílá replikovaná data do Azure. Používá se jenom při replikaci serverů do Azure pomocí migrace bez agentů.
- **SQL Discovery and Assessment agent**: odesílá metadata konfigurace a výkonu SQL Server instancí a databází do Azure.

> [!Note]
> Poslední 3 služby jsou dostupné jenom v zařízení, které se používá ke zjišťování a hodnocení serverů, které běží ve vašem prostředí VMware.<br/> Zjišťování a hodnocení instancí SQL Server a databází spuštěných ve vašem prostředí VMware je teď ve verzi Preview. Pokud chcete tuto funkci vyzkoušet, použijte [**tento odkaz**](https://aka.ms/AzureMigrate/SQL) a vytvořte projekt v oblasti **Austrálie – východ**. Pokud již máte projekt v oblasti Austrálie – východ a chcete tuto funkci vyzkoušet, na portálu se ujistěte, že jste splnili tyto [**požadavky**](how-to-discover-sql-existing-project.md).

## <a name="discovery-and-collection-process"></a>Proces zjišťování a shromažďování

:::image type="content" source="./media/migrate-appliance-architecture/architecture1.png" alt-text="Architektura zařízení":::

Zařízení komunikuje se zdroji zjišťování pomocí následujícího procesu.

**Proces** | **Zařízení VMware** | **Zařízení Hyper-V** | **Fyzické zařízení**
---|---|---|---
**Spustit zjišťování** | Zařízení ve výchozím nastavení komunikuje s vCenter serverem na portu TCP 443. Pokud Server vCenter naslouchá na jiném portu, můžete ho nakonfigurovat ve Správci konfigurace zařízení. | Zařízení komunikuje s hostiteli Hyper-V na portu WinRM 5985 (HTTP). | Zařízení komunikuje se servery Windows prostřednictvím protokolu WinRM 5985 (HTTP) se servery Linux přes port 22 (TCP).
**Shromažďování metadat konfigurace a výkonu** | Zařízení shromažďuje metadata serverů běžících na vCenter Server pomocí rozhraní API vSphere, a to připojením na portu 443 (výchozí port) nebo na jiném portu, na kterém naslouchá vCenter Server. | Zařízení shromažďuje metadata serverů, které běží na hostitelích Hyper-V, pomocí relace model CIM (Common Information Model) (CIM) s hostiteli na portu 5985.| Zařízení shromažďuje metadata ze serverů Windows pomocí relace model CIM (Common Information Model) (CIM) se servery na portu 5985 a ze serverů se systémem Linux pomocí připojení SSH na portu 22.
**Odeslat data zjišťování** | Zařízení odesílá shromážděná data do Azure Migrate: zjišťování a vyhodnocení a Azure Migrate: Migrace serveru přes SSL port 443.<br/><br/>  Zařízení se může připojit k Azure přes Internet nebo prostřednictvím privátního partnerského vztahu ExpressRoute nebo Microsoft peering okruhu. | Zařízení odesílá shromážděná data do Azure Migrate: zjišťování a posouzení přes port SSL 443.<br/><br/> Zařízení se může připojit k Azure přes Internet nebo prostřednictvím privátního partnerského vztahu ExpressRoute nebo Microsoft peering okruhu. | Zařízení odesílá shromážděná data do Azure Migrate: zjišťování a posouzení přes port SSL 443.<br/><br/> Zařízení se může připojit k Azure přes Internet nebo prostřednictvím privátního partnerského vztahu ExpressRoute nebo Microsoft peering okruhu. 
**Frekvence shromažďování dat** | Metadata konfigurace se shromažďují a odesílají každých 30 minut. <br/><br/> Metadata o výkonu se shromažďují každých 20 sekund a agreguje se, aby se do Azure odesílal datový bod každých 10 minut. <br/><br/> Data inventáře softwaru se do Azure odesílají každých 12 hodin. <br/><br/> Data závislostí bez agentů se shromažďují každých 5 minut, agregovaná na zařízení a odesílaná do Azure každých 6 hodin. <br/><br/> Konfigurační data SQL Server se aktualizují jednou za 24 hodin a data o výkonu se zaznamenávají každých 30 sekund.| Metadata konfigurace se shromažďují a odesílají každých 30 minut. <br/><br/> Metadata o výkonu se shromažďují každých 30 sekund a agreguje se, aby se do Azure odesílal datový bod každých 10 minut.|  Metadata konfigurace se shromažďují a odesílají každých 30 minut. <br/><br/> Metadata o výkonu se shromažďují každých 5 minut a agreguje se, aby se do Azure odesílal datový bod každých 10 minut.
**Posouzení a migrace** | Můžete vytvořit posouzení z metadat shromážděných zařízením pomocí nástroje Azure Migrate: Discovery and Assessment Tool.<br/><br/>Kromě toho můžete také začít migrovat servery běžící v prostředí VMware pomocí Azure Migrate: Nástroj pro migraci serveru pro orchestraci replikace serveru bez agenta.| Můžete vytvořit posouzení z metadat shromážděných zařízením pomocí nástroje Azure Migrate: Discovery and Assessment Tool. | Můžete vytvořit posouzení z metadat shromážděných zařízením pomocí nástroje Azure Migrate: Discovery and Assessment Tool.

## <a name="next-steps"></a>Další kroky

[Projděte si](migrate-appliance.md) matici podpora zařízení.