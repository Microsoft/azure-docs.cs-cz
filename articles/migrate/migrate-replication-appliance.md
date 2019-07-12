---
title: Architektura replikace zařízení služby Azure Migrate | Dokumentace Microsoftu
description: Obsahuje základní informace o replikaci zařízení Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: 4f4dc307bee4190a0e94ace493053e0cfd01150e
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811437"
---
# <a name="replication-appliance"></a>Replikace zařízení

Tento článek popisuje zařízení replikace používat Azure Migrate: Když migrace virtuálních počítačů VMware a fyzické počítače, privátní nebo veřejné cloudové virtuální počítače do Azure, pomocí migrace založené na agentovi do serveru posouzení. 

Tento nástroj je k dispozici v [Azure Migrate](migrate-overview.md) rozbočovače. Centrum poskytuje nativní nástroje pro vyhodnocení a migrace, jakož i nástroje od ostatních služeb Azure a od třetí strany nezávislých dodavatelů softwaru (ISV).


## <a name="appliance-overview"></a>Přehled zařízení

Zařízení replikace je nasazený jako jeden místní počítač, buď jako virtuální počítač VMware nebo fyzický server. Spuštění:
- **Replikace zařízení**: Zařízení replikace koordinuje komunikaci a spravuje replikaci dat, pro místní virtuální počítače VMware a fyzické servery replikované do Azure.
- **Procesový server:** Procesový server, který je nainstalovaný ve výchozím nastavení na zařízení replikace a provede následující akce:
    - **Brána replikace**: Funguje jako replikační brána. Přijímá data replikace z počítače povolena replikace. Optimalizuje data replikace pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do Azure.
    - **Instalační program agenta**: Provádí nabízenou instalaci služby Mobility. Tato služba musí být nainstalována a spuštěna na každém místní počítač, který chcete replikovat pro migraci.

## <a name="appliance-deployment"></a>Nasazení zařízení

**Nasadit jako** | **Používá pro** | **Podrobnosti**
--- | --- |  ---
Virtuální počítač VMware | Obvykle se používá při migraci virtuálních počítačů VMware, nástroj pro migraci Azure migrovat pomocí migrace založené na agentovi. | Stažení šablony pro soubory OVA z centra Azure Migrate a importovat do systému vCenter Server toto zařízení vytvoříte virtuální počítač.
Fyzický počítač | Použít při migraci místních fyzických serverů, pokud nemáte infrastrukturu VMware, nebo pokud nemůžete k vytvoření virtuálního počítače VMware pomocí OVA šablony. | Stáhněte si instalační program, který software z centra Azure Migrate a spusťte ho, abyste počítač zařízení.

## <a name="appliance-deployment-requirements"></a>Požadavky na nasazení zařízení

[Kontrola](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements) požadavky na nasazení.



## <a name="appliance-license"></a>Licence zařízení
Zařízení se dodává s zkušební licence Windows serveru 2016, který je platný po dobu 180 dnů. Pokud zkušební období blíží vypršení platnosti, doporučujeme stáhnout a nasadit nové zařízení nebo aktivaci licencí operačního systému zařízení virtuálního počítače.

## <a name="replication-process"></a>Proces replikace

1. Když povolíte replikaci pro virtuální počítač, začíná počáteční replikace do úložiště Azure, pomocí zásad replikace zadaný. 
2. Provoz replikuje do služby Azure storage veřejné koncové body přes internet. Přenos replikačních dat přes virtuální privátní síť site-to-site (VPN) z místní lokality do Azure se nepodporuje.
3. Po dokončení počáteční replikace začne rozdílová replikace. Sledované změny se pro počítač jsou protokolovány.
4. Komunikace se stane, následujícím způsobem:
    - Virtuální počítače komunikovat s zařízení replikace na port HTTPS 443 příchozí kvůli správě replikace.
    - Zařízení replikace orchestruje replikaci pomocí Azure přes odchozí port HTTPS 443.
    - Příchozí data replikace k procesového serveru (běžícího na zařízení replikace) na příchozím portu HTTPS 9443 poslat virtuálních počítačů. Tento port je možné upravit.
    - Procesový server přijímá data replikace, optimalizuje je zašifruje a odesílá je do úložiště Azure přes port 443 odchozí.
5. Data replikace zaznamená první pozemního v účtu úložiště mezipaměti v Azure. Tyto protokoly se zpracují a jsou data uložená v Azure spravovaného disku.

![Architektura](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Upgrade zařízení

Zařízení je upgradovány ručně z centra Azure Migrate. Doporučujeme vždy běží nejnovější verze.

1. V Azure Migrate > servery > Azure Migrate: Klikněte na server Assessment, servery infrastruktury, **konfigurační servery**.
2. V **konfigurační servery**, odkaz se zobrazí v **verze agenta** když je dostupná nová verze zařízení replikace. 
3. Stažení instalačního programu na počítači replikace zařízení a instalace upgradu. Instalační program zjistí, verzi aktuální spuštění na zařízení.
 
## <a name="next-steps"></a>Další kroky

[Zjistěte, jak](tutorial-assess-vmware.md#set-up-the-appliance-vm) nastavit zařízení pro VMware.
[Zjistěte, jak](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) nastavit zařízení pro technologii Hyper-V.

