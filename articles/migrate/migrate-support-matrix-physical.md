---
title: Podpora posouzení fyzického serveru pomocí Azure Migrate
description: Přečtěte si o podpoře fyzického serveru pro posouzení pomocí Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 32080605217cde78bd648ca6192f73d1025dea4c
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028777"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Matice podpory pro posouzení fyzického serveru 

[Službu Azure Migrate](migrate-overview.md) můžete použít k vyhodnocení a migraci počítačů do Microsoft Azure cloudu. Tento článek shrnuje nastavení podpory a omezení pro vyhodnocení a migraci místních fyzických serverů.


## <a name="overview"></a>Přehled

K vyhodnocení místních počítačů pro migraci do Azure pomocí tohoto článku přidáte nástroj Azure Migrate: Server Assessment Tool do projektu Azure Migrate. Nasadíte [zařízení Azure Migrate](migrate-appliance.md). Zařízení nepřetržitě zjišťuje místní počítače a odesílá data o konfiguraci a výkonu do Azure. Po zjištění počítače shromáždíte zjištěné počítače do skupin a spustíte posouzení pro skupinu.

## <a name="limitations"></a>Omezení

**Podpora** | **Podrobnosti**
--- | ---
**Omezení hodnocení**| Objevte a posuďte až 35 000 fyzických serverů v jednom [projektu](migrate-support-matrix.md#azure-migrate-projects).
**Omezení projektu** | V předplatném Azure můžete vytvořit více projektů. Projekt může zahrnovat virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery až do limitů hodnocení.
**Zjišťování** | Zařízení Azure Migrate může zjistit až 250 fyzických serverů.
**Posouzení** | Do jedné skupiny můžete přidat až 35 000 počítačů.<br/><br/> V jednom posouzení můžete vyhodnotit až 35 000 počítačů.

[Přečtěte si další informace](concepts-assessment-calculation.md) o posouzení.




## <a name="physical-server-requirements"></a>Požadavky na fyzický server

| **Podpora**                | **Podrobnosti**               
| :-------------------       | :------------------- |
| **Nasazení fyzického serveru**       | Fyzický server může být samostatný nebo nasazený v clusteru. |
| **Oprávnění**           | **Windows:** Nastavte místní uživatelský účet na všech serverech Windows, které chcete zahrnout do zjišťování. Uživatelský účet musí být přidán do těchto skupin – Uživatelé vzdálené plochy, sledování výkonu a uživatelé protokolu výkonu. <br/> **Linux:** Na serverech se systémem Linux, které chcete zjistit, potřebujete kořenový účet. |
| **Operační systém** | Podporovány jsou všechny operační systémy [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) a [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) s výjimkou následujících:<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="azure-migrate-appliance-requirements"></a>Požadavky na zařízení Azure Migrate

Azure Migrate používá [zařízení Azure Migrate](migrate-appliance.md) ke zjišťování a hodnocení. Zařízení pro fyzické servery může běžet na VIRTUÁLNÍm počítači nebo na fyzickém počítači. Nastavíte ji pomocí skriptu prostředí PowerShell, který stáhnete z Azure Portal.

- Přečtěte si informace o [požadavcích na zařízení](migrate-appliance.md#appliance---physical) pro fyzické servery.
- Přečtěte si o [adresách URL](migrate-appliance.md#url-access) , ke kterým zařízení potřebuje mít přístup.

## <a name="port-access"></a>Přístup k portu

Následující tabulka shrnuje požadavky na porty pro posouzení.

**zařízení** | **připojení**
--- | ---
**Náplně** | Příchozí připojení na portu TCP 3389 umožňující připojení ke vzdálené ploše zařízení.<br/> Příchozí připojení na portu 44368 pro vzdálený přístup k aplikaci pro správu zařízení pomocí adresy URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Odchozí připojení na portech 443, 5671 a 5672 pro posílání metadat zjišťování a výkonu Azure Migrate.
**Fyzické servery** | **Windows:** Příchozí připojení na portu 443, porty WinRM 5985 (HTTP) a 5986 (HTTPS) k vyžádání metadat konfigurace a výkonu ze serverů Windows. <br/> **Linux:**  Příchozí připojení na portu 22 (UDP) pro vyžádání metadat konfigurace a výkonu ze serverů se systémem Linux. |


## <a name="next-steps"></a>Další kroky

[Příprava na posouzení fyzického serveru](tutorial-prepare-physical.md).
