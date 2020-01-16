---
title: Podpora pro vyhodnocení technologie Hyper-V v Azure Migrate
description: Přečtěte si o podpoře pro vyhodnocování technologie Hyper-V pomocí Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 20bdbb16d2f0610f6519424141b09190eae3cc42
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028781"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Matice podpory pro posouzení technologie Hyper-V

Tento článek shrnuje nastavení podpory a omezení pro vyhodnocování virtuálních počítačů Hyper-V s [Azure Migrate: posouzení serveru](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Pokud hledáte informace o migraci virtuálních počítačů Hyper-V do Azure, Projděte si [matici podpora pro migraci](migrate-support-matrix-hyper-v-migration.md).

## <a name="overview"></a>Přehled

K vyhodnocení místních počítačů pro migraci do Azure pomocí tohoto článku přidáte nástroj Azure Migrate: Server Assessment Tool do projektu Azure Migrate. Nasadíte [zařízení Azure Migrate](migrate-appliance.md). Zařízení nepřetržitě zjišťuje místní počítače a odesílá data o konfiguraci a výkonu do Azure. Po zjištění počítače shromáždíte zjištěné počítače do skupin a spustíte posouzení pro skupinu.


## <a name="limitations"></a>Omezení

**Podpora** | **Podrobnosti**
--- | ---
**Omezení hodnocení**| Vyhledejte a posuďte až 35 000 virtuálních počítačů Hyper-V v jednom [projektu](migrate-support-matrix.md#azure-migrate-projects).
**Omezení projektu** | V předplatném Azure můžete vytvořit více projektů. Projekt může zahrnovat virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery až do limitů hodnocení.
**Zjišťování** | Zařízení Azure Migrate může zjistit až 5000 virtuálních počítačů Hyper-V.<br/><br/> Zařízení se může připojit k až 300 hostitelům Hyper-V.
**Posouzení** | Do jedné skupiny můžete přidat až 35 000 počítačů.<br/><br/> V jednom posouzení můžete vyhodnotit až 35 000 virtuálních počítačů.

[Přečtěte si další informace](concepts-assessment-calculation.md) o posouzení.



## <a name="hyper-v-host-requirements"></a>Požadavky na hostitele Hyper-V

| **Podpora**                | **Podrobnosti**               
| :-------------------       | :------------------- |
| **Nasazení hostitele**       | Hostitel Hyper-V může být samostatný nebo nasazený v clusteru. |
| **Oprávnění**           | Na hostiteli Hyper-V potřebujete oprávnění správce. <br/> Případně, pokud nechcete přiřadit oprávnění správce, vytvořte místní účet nebo uživatelský účet domény a přidejte tohoto uživatele do těchto skupin – Uživatelé vzdálené správy, Správci technologie Hyper-V a uživatelé nástroje Performance Monitor. |
| **Operační systém hostitele** | Windows Server 2019, Windows Server 2016 nebo Windows Server 2012 R2.<br/> Virtuální počítače na hostitelích Hyper-V s Windows Serverem 2012 hodnotit nemůžete. |
| **Vzdálená komunikace PowerShellu**   | Musí být povoleno na každém hostiteli. |
| **Replika technologie Hyper-V**       | Pokud používáte repliku technologie Hyper-V (nebo máte více virtuálních počítačů se stejnými identifikátory virtuálních počítačů) a zjistíte jak původní, tak replikované virtuální počítače pomocí Azure Migrate, hodnocení vygenerované Azure Migrate nemusí být přesné. |


## <a name="hyper-v-vm-requirements"></a>Požadavky na virtuální počítače Hyper-V

| **Podpora**                  | **Podrobnosti**               
| :----------------------------- | :------------------- |
| **Operační systém** | Všechny operační systémy [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) a [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) podporované Azure. |
| **Integrační služby**       | Aby bylo možné zachytit informace o operačním systému, musí být na virtuálních počítačích, které jste vyhodnotili, spuštěny [integrační služby technologie Hyper-v](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) . |


## <a name="azure-migrate-appliance-requirements"></a>Požadavky na zařízení Azure Migrate

Azure Migrate používá [zařízení Azure Migrate](migrate-appliance.md) ke zjišťování a hodnocení. Zařízení pro technologii Hyper-V běží na virtuálním počítači s technologií Hyper-V a je nasazeno pomocí komprimovaného virtuálního pevného disku Hyper-V, který stáhnete z Azure Portal. 

- Přečtěte si informace o [požadavcích na zařízení](migrate-appliance.md#appliance---hyper-v) pro Hyper-V.
- Přečtěte si o [adresách URL](migrate-appliance.md#url-access) , ke kterým zařízení potřebuje mít přístup.

## <a name="port-access"></a>Přístup k portu

Následující tabulka shrnuje požadavky na porty pro posouzení.

**zařízení** | **připojení**
--- | ---
**Náplně** | Příchozí připojení na portu TCP 3389 umožňující připojení ke vzdálené ploše zařízení.<br/> Příchozí připojení na portu 44368 pro vzdálený přístup k aplikaci pro správu zařízení pomocí adresy URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Odchozí připojení na portech 443, 5671 a 5672 pro posílání metadat zjišťování a výkonu Azure Migrate.
**Hostitel nebo cluster Hyper-V** | Příchozí připojení na portech WinRM 5985 (HTTP) a 5986 (HTTPS) k vyžádání metadat konfigurace a výkonu virtuálních počítačů Hyper-V pomocí relace model CIM (Common Information Model) (CIM).



## <a name="next-steps"></a>Další kroky

[Příprava na posouzení virtuálních počítačů Hyper-V](tutorial-prepare-hyper-v.md)
