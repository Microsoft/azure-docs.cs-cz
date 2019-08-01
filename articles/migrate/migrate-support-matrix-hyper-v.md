---
title: Tabulka podpory Azure Migrate pro vyhodnocení a migraci technologie Hyper-V
description: Shrnuje nastavení a omezení pro vyhodnocení a migraci technologie Hyper-V pomocí služby Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: raynew
ms.openlocfilehash: 787bcf28a64c98f82deb0725e2362bf8144d6c4e
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640862"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>Matice podpory pro vyhodnocení a migraci Hyper-V

[Službu Azure Migrate](migrate-overview.md) můžete použít k vyhodnocení a migraci počítačů do Microsoft Azure cloudu. Tento článek shrnuje nastavení podpory a omezení pro vyhodnocení a migraci místních virtuálních počítačů Hyper-V.



## <a name="hyper-v-scenarios"></a>Scénáře technologie Hyper-V

Tabulka shrnuje podporované scénáře pro virtuální počítače Hyper-V.

**Nasazení** | **Zobrazí***
--- | ---
**Posouzení místních virtuálních počítačů Hyper-V** | [Nastavte](tutorial-prepare-hyper-v.md) své první posouzení.<br/><br/> [Spusťte](scale-hyper-v-assessment.md) hodnocení ve velkém měřítku.
**Migrace virtuálních počítačů Hyper-V do Azure** | [Vyzkoušejte](tutorial-migrate-hyper-v.md) migraci do Azure.



## <a name="azure-migrate-projects"></a>Azure Migrate projekty

**Podpora** | **Podrobnosti**
--- | ---
Oprávnění Azure | Chcete-li vytvořit projekt Azure Migrate, potřebujete oprávnění přispěvatele nebo vlastníka v rámci předplatného.
Virtuální počítače Hyper-V | Vyhodnoťte až 10 000 virtuálních počítačů Hyper-V v jednom projektu. V předplatném Azure můžete mít více projektů. Projekt může zahrnovat virtuální počítače VMware i virtuální počítače Hyper-V, a to až do limitů hodnocení.
Geografie | Azure Migrate projekty lze vytvořit v řadě geografických oblastí. I když můžete vytvářet projekty v konkrétních ographies, můžete vyhodnotit nebo migrovat počítače pro jiná cílová umístění. Geografie projektu se používá pouze k uložení zjištěných metadat.

  **Zeměpisné oblasti** | **Umístění úložiště metadat**
  --- | ---
  Azure Government | USA (Gov) – Virginia
  Asie a Tichomoří | Jihovýchodní Asie nebo Východní Asie
  Evropa | Jižní Evropa nebo Západní Evropa
  Spojené království | Velká Británie – jih nebo Velká Británie – západ
  Spojené státy | Střed USA nebo Západní USA 2


 > [!NOTE]
 > Podpora Azure Government je v tuto chvíli dostupná jenom pro [starší verze](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) Azure Migrate.


## <a name="assessment-hyper-v-host-requirements"></a>Posouzení – požadavky na hostitele Hyper-V

| **Podpora**                | **Podrobnosti**               
| :-------------------       | :------------------- |
| **Nasazení hostitele**       | Hostitel Hyper-V může být samostatný nebo nasazený v clusteru. |
| **Oprávnění**           | Na hostiteli Hyper-V potřebujete oprávnění správce. |
| **Operační systém hostitele** | Windows Server 2016 nebo Windows Server 2012 R2.<br/> Nelze vyhodnotit virtuální počítače umístěné na hostitelích Hyper-V se systémem Windows Server 2019. |
| **Vzdálená komunikace PowerShellu**   | Musí být povoleno na každém hostiteli. |
| **Replika technologie Hyper-V**       | Pokud používáte repliku technologie Hyper-V (nebo máte více virtuálních počítačů se stejnými identifikátory virtuálních počítačů) a zjistíte jak původní, tak replikované virtuální počítače pomocí Azure Migrate, hodnocení vygenerované Azure Migrate nemusí být přesné. |


## <a name="assessment-hyper-v-vm-requirements"></a>Posouzení – požadavky na virtuální počítače Hyper-V

| **Podpora**                  | **Podrobnosti**               
| :----------------------------- | :------------------- |
| **Operační systém** | Všechny operační systémy [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) a [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) podporované Azure. |
| **Oprávnění**           | Pro každý virtuální počítač Hyper-V, který chcete vyhodnotit, potřebujete oprávnění správce. |
| **Integrační služby**       | Aby bylo možné zachytit informace o operačním systému, musí být na virtuálních počítačích, které jste vyhodnotili, spuštěny [integrační služby technologie Hyper-v](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) . |
| **Spouštění UEFI**                  | Virtuální počítače se spouštěním UEFI se pro migraci nepodporují. |
| **Šifrované disky/svazky**    | Virtuální počítače se zašifrovanými disky nebo svazky se nepodporují pro migraci. |
| **RDM/průchozí disky**      | Pokud virtuální počítače mají RDM nebo průchozí disky, tyto disky se nebudou replikovat do Azure. |
| **NFS**                        | Svazky NFS připojené jako svazky na virtuálních počítačích se nebudou replikovat. |
| **Cílový disk**                | Posouzení Azure Migrate doporučuje migraci na virtuální počítače Azure jenom se spravovanými disky. |


## <a name="assessment-appliance-requirements"></a>Posouzení – požadavky na zařízení

Pro posouzení Azure Migrate spouští odlehčené zařízení pro zjišťování virtuálních počítačů Hyper-V a posílání metadat a dat o výkonu virtuálních počítačů do Azure Migrate. Zařízení běží na virtuálním počítači s technologií Hyper-V a můžete ho nastavit pomocí komprimovaného virtuálního pevného disku Hyper-V, který stáhnete z Azure Portal. Následující tabulka shrnuje požadavky na zařízení.

| **Podpora**                | **Podrobnosti**               
| :-------------------       | :------------------- |
| **Azure Migrate projekt**  |  Zařízení může být přidruženo k jednomu projektu.<br/> Můžete zjistit až 5000 virtuálních počítačů Hyper-V s jedním zařízením.
| **Hyper-V**    |  Zařízení nasadíte jako virtuální počítač Hyper-V.<br/> Zadaný virtuální počítač zařízení je Hyper-V VM verze 5,0.<br/> Na hostiteli virtuálního počítače musí být spuštěný systém Windows Server 2012 R2 nebo novější.<br/> K alokaci 16 GB paměti RAM, 4 virtuálních procesorů a 1 externímu přepínači pro virtuální počítač zařízení potřebujete dostatek místa.<br/> Zařízení vyžaduje statickou nebo dynamickou IP adresu a přístup k Internetu.


## <a name="assessment-appliance-url-access"></a>Posouzení – přístup k adrese URL zařízení

K vyhodnocení virtuálních počítačů Azure Migrate zařízení potřebuje připojení k Internetu.

- Když zařízení nasadíte, Azure Migrate provede kontrolu připojení k adresám URL, které jsou shrnuté v následující tabulce.
- Pokud používáte proxy server založený na adrese URL, povolte přístup k adresám URL v tabulce. tím se zajistí, že proxy přeloží všechny záznamy CNAME přijaté při vyhledávání adres URL.
- Pokud máte zachycený proxy server, může být nutné importovat certifikát serveru z proxy server do zařízení.


**Adresa URL** | **Podrobnosti**  
--- | ---
*.portal.azure.com | Navigace na Azure Portal
*.windows.net | Přihlásit k předplatnému Azure
*.microsoftonline.com | Vytváření Azure Active Directorych aplikací pro komunikaci mezi zařízením a službami.
management.azure.com | Vytváření Azure Active Directorych aplikací pro komunikaci mezi zařízením a službami.
dc.services.visualstudio.com | Protokolování a monitorování
*.vault.azure.net | Správa tajných kódů v Azure Key Vault při komunikaci mezi zařízením a službou.
aka.ms/* | Povolí přístup k odkazům.
https://download.microsoft.com/download/* | Povoluje soubory ke stažení z webu Microsoft Download.



## <a name="assessment-port-requirements"></a>Posouzení – požadavky na port

Následující tabulka shrnuje požadavky na porty pro posouzení.

**zařízení** | **připojení**
--- | ---
**Náplně** | Příchozí připojení na portu TCP 3389 umožňující připojení ke vzdálené ploše zařízení.<br/> Příchozí připojení na portu 44368 pro vzdálený přístup k aplikaci pro správu zařízení pomocí adresy URL:``` https://<appliance-ip-or-name>:44368 ```<br/> Odchozí připojení na portu 443, která odesílají metadata zjišťování a výkonu Azure Migrate.
**Hostitel nebo cluster Hyper-V** | Příchozí připojení na portech WinRM 5985 (HTTP) a 5986 (HTTPS) k vyžádání metadat konfigurace a výkonu virtuálních počítačů Hyper-V pomocí relace model CIM (Common Information Model) (CIM).

## <a name="migration-hyper-v-host-requirements"></a>Migrace – požadavky na hostitele Hyper-V

| **Podpora**                | **Podrobnosti**               
| :-------------------       | :------------------- |
| **Nasazení hostitele**       | Hostitel Hyper-V může být samostatný nebo nasazený v clusteru. |
| **Oprávnění**           | Na hostiteli Hyper-V potřebujete oprávnění správce. |
| **Operační systém hostitele** | Windows Server 2019, Windows Server 2016 nebo Windows Server 2012 R2. |

## <a name="migration-hyper-v-vm-requirements"></a>Migrace – požadavky na virtuální počítače Hyper-V

| **Podpora**                  | **Podrobnosti**               
| :----------------------------- | :------------------- |
| **Operační systém** | Všechny operační systémy [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) a [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) podporované Azure. |
| **Oprávnění**           | Pro každý virtuální počítač Hyper-V, který chcete vyhodnotit, potřebujete oprávnění správce. |
| **Integrační služby**       | Aby bylo možné zachytit informace o operačním systému, musí být na virtuálních počítačích, které jste vyhodnotili, spuštěny [integrační služby technologie Hyper-v](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) . |
| **Požadované změny pro Azure** | Některé virtuální počítače můžou vyžadovat změny, aby je bylo možné spouštět v Azure. Azure Migrate provede tyto změny automaticky pro následující operační systémy:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> – CentOS 6.5 +, 7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> – Debian 7, 8<br/><br/> Pro jiné operační systémy je nutné provést úpravy ručně před migrací. Příslušné články obsahují pokyny k tomu, jak to provést. |
| **Spouštění ze systému Linux**                 | Pokud je/Boot ve vyhrazeném oddílu, měl by být umístěn na disku s operačním systémem a nesmí být rozložen na více disků.<br/> Pokud je/Boot součástí kořenového oddílu (/), musí být oddíl '/' na disku s operačním systémem a nesmí zabírat jiné disky. |
| **Spouštění UEFI**                  | Virtuální počítače se spouštěním UEFI se pro migraci nepodporují.  |
| **Velikost disku**                  | 2 TB pro disk s operačním systémem, 4 TB pro datové disky.
| **Číslo disku** | Maximálně 16 disků na virtuální počítač.
| **Šifrované disky/svazky**    | Migrace se nepodporuje. |
| **RDM/průchozí disky**      | Migrace se nepodporuje. |
| **Sdílený disk** | Virtuální počítače používající sdílené disky se pro migraci nepodporují.
| **NFS**                        | Svazky NFS připojené jako svazky na virtuálních počítačích se nebudou replikovat. |
| **ISCSI**                      | Virtuální počítače s cíli iSCSI se nepodporují pro migraci.
| **Cílový disk**                | Můžete migrovat jenom na virtuální počítače Azure se spravovanými disky. |
| **Protokolů** | Nepodporuje se.
| **Seskupování síťových adaptérů** | Nepodporuje se.
| **Azure Site Recovery** | Pokud je virtuální počítač povolený pro replikaci pomocí Azure Site Recovery, nejde replikovat pomocí Azure Migrate migrace serveru.





## <a name="migration-hyper-v-host-url-access"></a>Migrace – přístup k adrese URL hostitele Hyper-V

Následující tabulka shrnuje požadavky na přístup k adresám URL pro hostitele Hyper-V.

**Adresa URL** | **Podrobnosti**  
--- | ---
login.microsoftonline.com | Řízení přístupu a Správa identit pomocí služby Active Directory.
*.backup.windowsazure.com | Přenos a koordinace dat replikace.
*.hypervrecoverymanager.windowsazure.com | Připojte se k adresám URL služby Azure Migrate.
*.blob.core.windows.net | Nahrajte data do účtů úložiště.
dc.services.visualstudio.com | Nahrávat protokoly aplikací používané pro interní monitorování
time.windows.com | Ověřuje časovou synchronizaci mezi systémovým a globálním časem.

## <a name="migration-port-access"></a>Migrace – přístup k portu

Následující tabulka shrnuje požadavky portů na hostitele Hyper-V a virtuální počítače pro migraci virtuálních počítačů.

**zařízení** | **připojení**
--- | ---
Hostitelé nebo virtuální počítače Hyper-V | Odchozí připojení na portu HTTPS 443 pro odesílání dat replikace virtuálních počítačů do Azure Migrate.




## <a name="next-steps"></a>Další kroky

[Připravte se na posouzení virtuálního počítače Hyper-V](tutorial-prepare-hyper-v.md) pro migraci.
