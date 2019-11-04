---
title: Tabulka podpory Azure Migrate pro posouzení fyzického serveru a jejich migraci
description: Shrnuje nastavení a omezení pro posouzení fyzického serveru a migraci pomocí služby Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 56e49ab60733c9c0a813ca6e46712bcd8052a2ef
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518148"
---
# <a name="support-matrix-for-physical-server-assessment-and-migration"></a>Matice podpory pro posouzení fyzického serveru a jejich migraci

[Službu Azure Migrate](migrate-overview.md) můžete použít k vyhodnocení a migraci počítačů do Microsoft Azure cloudu. Tento článek shrnuje nastavení podpory a omezení pro vyhodnocení a migraci místních fyzických serverů.


## <a name="physical-server-scenarios"></a>Scénáře fyzického serveru

Tabulka shrnuje podporované scénáře pro fyzické servery.

**Nasazení** | **Podrobnosti***
--- | ---
**Posouzení místních fyzických serverů** | [Nastavte](tutorial-prepare-physical.md) své první posouzení.
**Migrace fyzických serverů do Azure** | [Vyzkoušejte](tutorial-migrate-physical-virtual-machines.md) migraci do Azure.


## <a name="azure-migrate-projects"></a>Azure Migrate projekty

**Podpora** | **Podrobnosti**
--- | ---
**Oprávnění Azure** | Chcete-li vytvořit projekt Azure Migrate, potřebujete oprávnění přispěvatele nebo vlastníka v rámci předplatného.
**Fyzické servery** | V jednom projektu posuďte až 250 fyzických serverů. V předplatném Azure můžete mít více projektů. Projekt může zahrnovat fyzické servery, virtuální počítače VMware a virtuální počítače Hyper-V až do limitů hodnocení.
**Zeměpisné oblasti** | Azure Migrate projekty lze vytvořit v řadě geografických oblastí. I když můžete vytvářet projekty v konkrétních geografických oblastech, můžete vyhodnotit nebo migrovat počítače pro jiná cílová umístění. Geografie projektu se používá pouze k uložení zjištěných metadat.

  **Zeměpisné oblasti** | **Umístění úložiště metadat**
  --- | ---
  Azure Government | USA – Virginie
  Asie a Tichomoří | Východní Asie nebo jihovýchodní Asie
  Austrálie | Austrálie – východ nebo Austrálie – jihovýchod
  Brazílie | Brazílie – jih
  Kanada | Kanada – střed nebo Kanada – východ
  Evropa | Severní Evropa nebo Západní Evropa
  Francie | Francie – střed
  Indie | Střed Indie nebo Jižní Indie
  Japonsko |  Japonsko – východ nebo Japonsko – západ
  Korea | Korea – střed nebo Korea – jih
  Spojené království | Velká Británie – jih nebo Velká Británie – západ
  Spojené státy | Střed USA nebo Západní USA 2


 > [!NOTE]
 > Podpora Azure Government je v tuto chvíli dostupná jenom pro [starší verze](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) Azure Migrate.


## <a name="assessment-physical-server-requirements"></a>Posouzení – požadavky na fyzický server

| **Podpora**                | **Podrobnosti**               
| :-------------------       | :------------------- |
| **Nasazení fyzického serveru**       | Fyzický server může být samostatný nebo nasazený v clusteru. |
| **Oprávnění**           | **Windows:** Nastavte místní uživatelský účet na všech serverech Windows, které chcete zahrnout do zjišťování. Uživatelský účet musí být přidán do těchto skupin – Uživatelé vzdálené plochy, sledování výkonu a uživatelé protokolu výkonu. <br/> **Linux:** Na serverech se systémem Linux, které chcete zjistit, potřebujete kořenový účet. |
| **Operační systém** | Všechny operační systémy [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) a [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) podporované Azure. |


## <a name="assessment-appliance-requirements"></a>Posouzení – požadavky na zařízení

Pro posouzení Azure Migrate spouští odlehčené zařízení pro zjišťování fyzických serverů a posílání metadat a dat o výkonu serveru do Azure Migrate. Zařízení můžete spustit buď na fyzickém serveru, nebo na virtuálním počítači a nastavíte ho pomocí skriptu prostředí PowerShell, který stáhnete z Azure Portal. Následující tabulka shrnuje požadavky na zařízení.

| **Podpora**                | **Podrobnosti**               
| :-------------------       | :------------------- |
| **Nasazení zařízení**   |  Zařízení nasazujete buď na fyzickém serveru, nebo na virtuálním počítači.<br/>  Na hostitelském počítači musí být spuštěný systém Windows Server 2012 R2 nebo novější.<br/> Hostitel potřebuje dostatek místa pro přidělení 16 GB paměti RAM, 8 vCPU, přibližně 80 GB úložného prostoru a externí přepínač pro virtuální počítač zařízení.<br/> Zařízení potřebuje statickou nebo dynamickou IP adresu a přístup k Internetu.
| **Azure Migrate projekt**  |  Zařízení může být přidruženo k jednomu projektu.<br/> K jednomu projektu může být přidružen libovolný počet zařízení.<br/> V projektu můžete vyhodnotit až 35 000 počítačů.
| **Rozpoznávání**              | Jedno zařízení může zjistit až 200 serverů.
| **Skupina posouzení**       | Do jedné skupiny můžete přidat až 35 000 počítačů.
| **Posouzení**             | V jednom posouzení můžete vyhodnotit až 35 000 počítačů.


## <a name="assessment-appliance-url-access"></a>Posouzení – přístup k adrese URL zařízení

K vyhodnocení virtuálních počítačů Azure Migrate zařízení potřebuje připojení k Internetu.

- Když zařízení nasadíte, Azure Migrate provede kontrolu připojení k adresám URL, které jsou shrnuté v následující tabulce.
- Pokud používáte proxy server založený na adrese URL, povolte přístup k adresám URL v tabulce. tím se zajistí, že proxy přeloží všechny záznamy CNAME přijaté při vyhledávání adres URL.
- Pokud máte zachycený proxy server, může být nutné importovat certifikát serveru z proxy server do zařízení.


**Adresa URL** | **Podrobnosti**  
--- | ---
*.portal.azure.com | Navigace na Azure Portal
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | Přihlaste se ke svému předplatnému Azure.
*.microsoftonline.com <br/> *. microsoftonline-p.com | Vytváření Azure Active Directorych aplikací pro komunikaci mezi zařízením a službami.
management.azure.com | Vytváření Azure Active Directorych aplikací pro komunikaci mezi zařízením a službami.
dc.services.visualstudio.com | Protokolování a monitorování
*.vault.azure.net | Správa tajných kódů v Azure Key Vault při komunikaci mezi zařízením a službou.
aka.ms/* | Povolí přístup k odkazům.
https://download.microsoft.com/download/* | Povoluje soubory ke stažení z webu Microsoft Download.



## <a name="assessment-port-requirements"></a>Posouzení – požadavky na port

Následující tabulka shrnuje požadavky na porty pro posouzení.

**Zařízení** | **Vázán**
--- | ---
**Náplně** | Příchozí připojení na portu TCP 3389 umožňující připojení ke vzdálené ploše zařízení.<br/> Příchozí připojení na portu 44368 pro vzdálený přístup k aplikaci pro správu zařízení pomocí adresy URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Odchozí připojení na portech 443, 5671 a 5672 pro posílání metadat zjišťování a výkonu Azure Migrate.
**Fyzické servery** | **Windows:** Příchozí připojení na portech 443, 5989, které vyžádají metadata o konfiguraci a výkonu ze serverů Windows. <br/> **Linux:**  Příchozí připojení na portu 22 (UDP) pro vyžádání metadat konfigurace a výkonu ze serverů se systémem Linux. |


## <a name="next-steps"></a>Další kroky

[Příprava na posouzení fyzického serveru](tutorial-prepare-physical.md) pro posouzení fyzického serveru a jeho migraci
