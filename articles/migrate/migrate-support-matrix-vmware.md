---
title: Podpora pro vyhodnocení VMware v Azure Migrate
description: Přečtěte si o podpoře vyhodnocení VMware v Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 2a9c5504d99f439723a250b619b9f9b660ea9c59
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029001"
---
# <a name="support-matrix-for-vmware-assessment"></a>Matice podpory pro vyhodnocení VMware 

Tento článek shrnuje nastavení podpory a omezení pro vyhodnocování virtuálních počítačů VMware pomocí [Azure Migrate: posouzení serveru](migrate-services-overview.md#azure-migrate-server-migration-tool). Pokud hledáte informace o migraci virtuálních počítačů VMware do Azure, Projděte si [matici podpora pro migraci](migrate-support-matrix-vmware-migration.md).

## <a name="overview"></a>Přehled

K vyhodnocení místních počítačů pro migraci do Azure pomocí tohoto článku přidáte nástroj Azure Migrate: Server Assessment Tool do projektu Azure Migrate. Nasadíte [zařízení Azure Migrate](migrate-appliance.md). Zařízení nepřetržitě zjišťuje místní počítače a odesílá data o konfiguraci a výkonu do Azure. Po zjištění počítače shromáždíte zjištěné počítače do skupin a spustíte posouzení pro skupinu.


## <a name="limitations"></a>Omezení

**Podpora** | **Podrobnosti**
--- | ---
**Omezení hodnocení**| Vyhledejte a posuďte až 35 000 virtuálních počítačů VMware v jednom [projektu](migrate-support-matrix.md#azure-migrate-projects).
**Omezení projektu** | V předplatném Azure můžete vytvořit více projektů. Projekt může zahrnovat virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery až do limitů hodnocení.
**Zjišťování** | Zařízení Azure Migrate může na vCenter Server zjistit až 10 000 virtuálních počítačů VMware.
**Posouzení** | Do jedné skupiny můžete přidat až 35 000 počítačů.<br/><br/> V jednom posouzení můžete vyhodnotit až 35 000 virtuálních počítačů.

[Přečtěte si další informace](concepts-assessment-calculation.md) o posouzení.


## <a name="application-discovery"></a>Zjišťování aplikací

Kromě zjišťování počítačů Azure Migrate: posouzení serveru může zjišťovat aplikace, role a funkce běžící na počítačích. Zjišťování inventáře aplikací vám umožní identifikovat a naplánovat cestu migrace přizpůsobenou pro vaše místní úlohy. 

**Podpora** | **Podrobnosti**
--- | ---
Zjišťování | Zjišťování je bez agentů, používá přihlašovací údaje hosta počítače a vzdáleně přistupuje k počítačům pomocí volání WMI a SSH.
Podporované počítače | Místní virtuální počítače VMware.
Operační systém počítače | Všechny verze systému Windows a Linux
Přihlašovací údaje | Aktuálně podporuje použití jednoho pověření pro všechny servery se systémem Windows a jedno přihlašovací údaje pro všechny servery se systémem Linux.<br/><br/> Vytvoříte uživatelský účet hosta pro virtuální počítače s Windows a běžný/normální uživatelský účet (bez přístupu sudo) pro všechny virtuální počítače se systémem Linux.
Omezení | Pro zjišťování aplikací můžete zjistit až 10000 na každé zařízení. 

## <a name="vmware-requirements"></a>Požadavky VMware

**VMware** | **Podrobnosti**
--- | ---
**vCenter Server** | Počítače, které chcete zjišťovat a hodnotit, se musí spravovat pomocí vCenter Server verze 5,5, 6,0, 6,5 nebo 6,7.
**Oprávnění (posouzení)** | vCenter Server účet jen pro čtení.
**Oprávnění (zjišťování aplikací)** | vCenter Server účet s přístupem jen pro čtení a oprávnění povolená pro virtuální počítače > operace hostů.
**Oprávnění (Vizualizace závislostí)** | Účet serveru Center s přístupem jen pro čtení a oprávnění povolených pro **virtuální počítače** > **operací hostů**.


## <a name="azure-migrate-appliance-requirements"></a>Požadavky na zařízení Azure Migrate

Azure Migrate používá [zařízení Azure Migrate](migrate-appliance.md) ke zjišťování a hodnocení. Zařízení pro VMware je nasazeno pomocí šablony vajíček importované do vCenter Server. 

- Přečtěte si informace o [požadavcích na zařízení](migrate-appliance.md#appliance---vmware) pro VMware.
- Přečtěte si o [adresách URL](migrate-appliance.md#url-access) , ke kterým zařízení potřebuje mít přístup.

## <a name="port-access"></a>Přístup k portu

**zařízení** | **připojení**
--- | ---
Náplně | Příchozí připojení na portu TCP 3389 umožňující připojení ke vzdálené ploše zařízení.<br/><br/> Příchozí připojení na portu 44368 pro vzdálený přístup k aplikaci pro správu zařízení pomocí adresy URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Odchozí připojení na portu 443, 5671 a 5672 pro odeslání metadat zjišťování a výkonu pro Azure Migrate.
Server vCenter | Příchozí připojení na portu TCP 443, aby zařízení mohla shromažďovat metadata o konfiguraci a výkonu pro posouzení. <br/><br/> Ve výchozím nastavení se zařízení připojuje ke vCenter na portu 443. Pokud Server vCenter naslouchá na jiném portu, můžete změnit port při nastavení zjišťování.

## <a name="dependency-visualization"></a>Vizualizace závislostí

Vizualizace závislosti vám pomůže vizualizovat závislosti mezi počítači, které chcete vyhodnotit a migrovat. Mapování závislostí se obvykle používá, pokud chcete vyhodnotit počítače s vyšší úrovní spolehlivosti. V případě virtuálních počítačů VMware je Vizualizace závislostí podporována následujícím způsobem:

- **Vizualizace závislostí bez agentů**: Tato možnost je aktuálně ve verzi Preview. Nevyžaduje, abyste v počítačích nainstalovali žádné agenty.
    - Funguje tak, že zachytává data připojení TCP z počítačů, pro které je povolená. Po spuštění zjišťování závislostí zařízení shromáždí data z počítačů v intervalu dotazování po dobu pěti minut.
    - Shromažďují se následující data:
        - Připojení TCP
        - Názvy procesů, které mají aktivní připojení
        - Názvy nainstalovaných aplikací, které spouštějí výše uvedené procesy
        - Ne. připojení zjištěných při každém intervalu dotazování
- **Vizualizace závislostí na základě agenta**: Pokud chcete použít vizualizaci závislostí založenou na agentech, musíte si stáhnout a nainstalovat následující agenty na každý místní počítač, který chcete analyzovat.
    - Na každý počítač nainstalujte agenta Microsoft Monitoring Agent (MMA). [Přečtěte si další informace](how-to-create-group-machine-dependencies.md#install-the-mma) o tom, jak nainstalovat agenta MMA.
    - Nainstalujte na každý počítač agenta závislostí. [Přečtěte si další informace](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) o tom, jak nainstalovat agenta závislostí.
    - Pokud navíc máte počítače bez připojení k internetu, musíte na ně stáhnout a nainstalovat bránu Log Analytics.


## <a name="next-steps"></a>Další kroky

- [Projděte si](best-practices-assessment.md) osvědčené postupy pro vytváření hodnocení.
- [Příprava na vyhodnocení VMware](tutorial-prepare-vmware.md) .
