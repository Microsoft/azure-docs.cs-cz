---
title: Architektura zařízení Azure Migrate replikace
description: V této části najdete přehled zařízení replikace Azure Migrate pro migraci na základě agenta.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: ba14767bde5d6cdca3a82dbe4e8a115ec25cc911
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186562"
---
# <a name="replication-appliance"></a>Replikační zařízení

Tento článek popisuje zařízení replikace, které používá Azure Migrate: posouzení serveru při migraci virtuálních počítačů VMware, fyzických počítačů a privátních a veřejných cloudových virtuálních počítačů do Azure pomocí migrace založené na agentovi. 

Nástroj je k dispozici v centru [Azure Migrate](migrate-overview.md) . Centrum poskytuje nativní nástroje pro posuzování a migraci a také nástroje z jiných služeb Azure a od výrobců nezávislých výrobců softwaru (ISV).


## <a name="appliance-overview"></a>Přehled zařízení

Zařízení replikace se nasadí jako jeden místní počítač, a to buď jako virtuální počítač VMware, nebo jako fyzický server. Spustí se:
- **Zařízení replikace**: zařízení replikace koordinuje komunikaci a spravuje replikaci dat pro místní virtuální počítače VMware a fyzické servery, které se replikují do Azure.
- **Procesový Server**: procesový Server, který je ve výchozím nastavení nainstalován na zařízení replikace a provádí následující akce:
    - **Brána replikace**: funguje jako brána replikace. Přijímá data replikace z počítačů, které jsou povoleny pro replikaci. Optimalizuje replikační data pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do Azure.
    - **Instalační program agenta**: provede nabízenou instalaci služby mobility. Tato služba musí být nainstalovaná a spuštěná na každém místním počítači, který chcete replikovat pro migraci.

## <a name="appliance-deployment"></a>Nasazení zařízení

**Nasadit jako** | **Používá pro** | **Podrobnosti**
--- | --- |  ---
Virtuální počítač VMware | Obvykle se používá při migraci virtuálních počítačů VMware pomocí nástroje Azure Migrate Migration Tool s migrací založenou na agentech. | Šablonu pro sadu vajíček si stáhnete z centra Azure Migrate a naimportujete ji do vCenter Server a vytvoříte virtuální počítač zařízení.
Fyzický počítač | Používá se při migraci místních fyzických serverů, pokud nemáte infrastrukturu VMware, nebo pokud nemůžete vytvořit virtuální počítač VMware pomocí šablony vajíček. | Instalační program softwaru si můžete stáhnout z centra Azure Migrate a spustit ho k nastavení počítače zařízení.

## <a name="appliance-deployment-requirements"></a>Požadavky na nasazení zařízení

[Zkontrolujte](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements) požadavky na nasazení.



## <a name="appliance-license"></a>Licence na zařízení
Zařízení obsahuje zkušební licenci Windows Server 2016, která je platná po dobu 180 dnů. Pokud se zkušební období blíží vypršení platnosti, doporučujeme, abyste si stáhli a nasadili nové zařízení nebo aktivovali licenci k operačnímu systému virtuálního počítače zařízení.

## <a name="replication-process"></a>Proces replikace

1. Když pro virtuální počítač povolíte replikaci, začne počáteční replikace do Azure Storage s použitím zadaných zásad replikace. 
2. Provoz se replikuje do veřejných koncových bodů Azure Storage přes Internet. Replikace provozu přes virtuální privátní síť (VPN) typu Site-to-Site z místní lokality do Azure se nepodporuje.
3. Po dokončení počáteční replikace se spustí rozdílová replikace. Sledované změny pro počítač jsou protokolovány.
4. Komunikace probíhá takto:
    - Virtuální počítače komunikují se zařízením replikace na portu HTTPS 443 příchozí, pro správu replikací.
    - Zařízení replikace orchestruje replikaci pomocí Azure přes odchozí port HTTPS 443.
    - Virtuální počítače odesílají data replikace na procesový Server (spuštěný na zařízení replikace) na portu HTTPS 9443 příchozí. Tento port lze změnit.
    - Procesový server přijímá data replikace, optimalizuje je a šifruje a odesílá je do Azure Storage přes odchozí port 443.
5. Data replikace zaprotokolují první půdu v účtu úložiště mezipaměti v Azure. Tyto protokoly jsou zpracovávány a data jsou uložena na spravovaném disku Azure.

![Architektura](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Upgrady zařízení

Zařízení se upgraduje ručně z centra Azure Migrate. Doporučujeme vždy spustit nejnovější verzi.

1. V Azure Migrate > servery > Azure Migrate: posouzení serveru, servery infrastruktury, klikněte na **konfigurační servery**.
2. V **konfiguračních serverech**se odkaz zobrazuje v části **verze agenta** , pokud je k dispozici nová verze zařízení replikace. 
3. Stáhněte instalační program do počítače zařízení pro replikaci a nainstalujte upgrade. Instalační program zjistí aktuálně běžící verzi na zařízení.
 
## <a name="next-steps"></a>Další kroky

[Přečtěte si, jak](tutorial-assess-vmware.md#set-up-the-appliance-vm) nastavit zařízení pro VMware.
[Přečtěte si, jak](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) nastavit zařízení pro Hyper-V.

