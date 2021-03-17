---
title: Zařízení pro replikaci Azure Migrate
description: Přečtěte si o zařízení replikace Azure Migrate pro migraci VMWare založenou na agentech.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: ec277bcc3e361561f54e72c54526d65487c113b4
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754092"
---
# <a name="replication-appliance"></a>Replikační zařízení

Tento článek popisuje zařízení replikace používané službou [Azure Migrate: Nástroj pro migraci serveru](migrate-services-overview.md#azure-migrate-server-migration-tool) při migraci virtuálních počítačů VMware, fyzických počítačů a privátních a veřejných cloudových virtuálních počítačů do Azure pomocí migrace založené na agentech. 


## <a name="overview"></a>Přehled

Zařízení replikace se nasadí při nastavení migrace na základě agenta virtuálních počítačů VMware nebo fyzických serverů. Je nasazená jako jeden místní počítač, a to buď jako virtuální počítač VMware, nebo jako fyzický server. Spustí se:

- **Zařízení replikace**: zařízení replikace koordinuje komunikaci a spravuje replikaci dat pro místní virtuální počítače VMware a fyzické servery, které se replikují do Azure.
- **Procesový Server**: procesový Server, který je ve výchozím nastavení nainstalován na zařízení replikace a provádí následující akce:
    - **Brána replikace**: funguje jako brána replikace. Přijímá data replikace z počítačů, které jsou povoleny pro replikaci. Optimalizuje replikační data pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do Azure.
    - **Instalační program agenta**: provede nabízenou instalaci služby mobility. Tato služba musí být nainstalovaná a spuštěná na každém místním počítači, který chcete replikovat pro migraci.

## <a name="appliance-deployment"></a>Nasazení zařízení

**Použití** | **Podrobnosti**
--- |  ---
**Migrace založená na agentech virtuálních počítačů VMware** | Šablonu pro sadu vajíček si stáhnete z centra Azure Migrate a naimportujete ji do vCenter Server a vytvoříte virtuální počítač zařízení.
**Migrace založená na agentech fyzického počítače** | Pokud nemáte infrastrukturu VMware nebo pokud nemůžete vytvořit virtuální počítač VMware pomocí šablony vajíček, Stáhněte si instalační program softwaru z centra Azure Migrate a spusťte ho, abyste nastavili počítač zařízení.

> [!NOTE]
> Pokud nasazujete v Azure Government, použijte k nasazení zařízení replikace instalační soubor.

## <a name="appliance-requirements"></a>Požadavky na zařízení

Při nastavování zařízení replikace pomocí šablony vajíček, která je k dispozici v centru Azure Migrate, zařízení spustí systém Windows Server 2016 a splňuje požadavky na podporu. Pokud se zařízení replikace nastavuje ručně na fyzickém serveru, ujistěte se, že splňuje požadavky.

**Komponenta** | **Požadavek**
--- | ---
 | **Zařízení virtuálního počítače VMware**
PowerCLI | Pokud je na virtuálním počítači VMware spuštěno zařízení replikace, měla by být nainstalovaná [verze PowerCLI 6,0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) .
Typ síťové karty | VMXNET3 (Pokud je zařízení virtuálním počítačem VMware)
 | **Nastavení hardwaru**
Procesorová jádra | 8
Paměť RAM | 16 GB
Počet disků | Tři: disk s operačním systémem, disk mezipaměti procesového serveru a jednotka pro uchovávání.
Volné místo na disku (mezipaměť) | 600 GB
Volné místo na disku (disk pro uchovávání) | 600 GB
**Nastavení softwaru** |
Operační systém | Windows Server 2016 nebo Windows Server 2012 R2
Licence | Zařízení obsahuje zkušební licenci Windows Server 2016, která je platná po dobu 180 dnů.<br/><br/> Pokud se zkušební období blíží vypršení platnosti, doporučujeme, abyste si stáhli a nasadili nové zařízení nebo aktivovali licenci k operačnímu systému virtuálního počítače zařízení.
Národní prostředí operačního systému | Angličtina (en-us)
TLS | Je třeba povolit protokol TLS 1,2.
.NET Framework | Na počítači by se měla nainstalovat .NET Framework 4,6 nebo novější (se zapnutým silným kryptografií.
MySQL | Na zařízení by měl být nainstalován MySQL.<br/> Je potřeba nainstalovat MySQL. Instalaci můžete provést ručně, nebo ji Site Recovery můžete nainstalovat během nasazování zařízení.
Jiné aplikace | Na zařízení replikace nespouštějte jiné aplikace.
Role Windows Serveru | Nepovolujte tyto role: <br> – Active Directory Domain Services <br>– Internet Information Service <br> – Hyper-V
Zásady skupiny | Nepovolujte tyto zásady skupiny: <br> – Zabraňte přístupu k příkazovému řádku. <br> – Zabraňte přístup k nástrojům pro úpravu registru. <br> – Logika vztahu důvěryhodnosti pro přílohy souborů. <br> -Zapnout provádění skriptu. <br> [Další informace](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | -Žádný předdefinovaný výchozí web <br> -Žádný existující web nebo aplikace nenaslouchá na portu 443. <br>-Povolit  [anonymní ověřování](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> -Povolit nastavení [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10))
**Nastavení sítě** |
Typ IP adresy | Static
Porty | 443 (orchestrace řídicího kanálu)<br>9443 (přenos dat)
Typ síťové karty | VMXNET3

## <a name="mysql-installation"></a>Instalace MySQL 

V počítači se zařízením replikace musí být nainstalován MySQL. Dá se nainstalovat pomocí jedné z těchto metod.

**Metoda** | **Podrobnosti**
--- | ---
Stáhnout a nainstalovat ručně | Stáhněte si aplikaci MySQL & umístěte ji do složky C:\Temp\ASRSetup a pak ji nainstalujte ručně.<br/> Když nastavíte zařízení MySQL, bude se zobrazovat jako již nainstalované.
Bez online stažení | Umístěte aplikaci instalačního programu MySQL do složky C:\Temp\ASRSetup. Když nainstalujete zařízení a kliknete na stažení a instalaci MySQL, instalační program použije instalační program, který jste přidali.
Stažení a instalace v Azure Migrate | Po instalaci zařízení a zobrazení výzvy k MySQL vyberte **Stáhnout a nainstalovat**.

## <a name="url-access"></a>Přístup URL

Zařízení replikace potřebuje přístup k těmto adresám URL ve veřejném cloudu Azure.

**Adresa URL** | **Podrobnosti**
--- | ---
\*.backup.windowsazure.com | Slouží k přenosu replikovaných dat a jejich koordinaci.
\*.store.core.windows.net | Slouží k přenosu replikovaných dat a jejich koordinaci.
\*.blob.core.windows.net | Používá se pro přístup k účtu úložiště, který ukládá replikovaná data.
\*.hypervrecoverymanager.windowsazure.com | Slouží k operacím správy replikace a jejich koordinaci.
https:\//management.azure.com | Slouží k operacím správy replikace a jejich koordinaci.
*.services.visualstudio.com | Používá se pro účely telemetrie (je volitelné).
time.windows.com | Používá se ke kontrole synchronizace mezi systémovým a globálním časem.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https: \/ /Login.Live.com <br/> https: \/ /Graph.Windows.NET <br/> https:\//login.windows.net <br/> https: \/ /www.Live.com <br/> https: \/ /www.Microsoft.com  | Instalace zařízení potřebuje přístup k těmto adresám URL. Používají se k řízení přístupu a správě identit pomocí Azure Active Directory
https: \/ /dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Pro dokončení stažení MySQL. V několika oblastech může být stahování Přesměrováno na adresu URL CDN. V případě potřeby se ujistěte, že je povolená i adresa URL CDN.


## <a name="azure-government-url-access"></a>Přístup k adrese URL Azure Government

Zařízení replikace potřebuje přístup k těmto adresám URL v Azure Government.

**Adresa URL** | **Podrobnosti**
--- | ---
\*. backup.windowsazure.us | Slouží k přenosu replikovaných dat a jejich koordinaci.
\*.store.core.windows.net | Slouží k přenosu replikovaných dat a jejich koordinaci.
\*.blob.core.windows.net | Používá se pro přístup k účtu úložiště, který ukládá replikovaná data.
\*. hypervrecoverymanager.windowsazure.us | Slouží k operacím správy replikace a jejich koordinaci.
https:\//management.usgovcloudapi.net | Slouží k operacím správy replikace a jejich koordinaci.
*.services.visualstudio.com | Používá se pro účely telemetrie (je volitelné).
time.nist.gov | Používá se ke kontrole synchronizace mezi systémovým a globálním časem.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https: \/ /Login.Live.com <br/> https: \/ /Graph.Windows.NET <br/> https:\//login.windows.net <br/> https: \/ /www.Live.com <br/> https: \/ /www.Microsoft.com  | Nastavení zařízení pomocí vajíček potřebuje přístup k těmto adresám URL. Používají se k řízení přístupu a správě identit pomocí Azure Active Directory.
https: \/ /dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Pro dokončení stažení MySQL. V několika oblastech může být stahování Přesměrováno na adresu URL CDN. V případě potřeby se ujistěte, že je povolená i adresa URL CDN.

## <a name="port-access"></a>Přístup k portu

**Zařízení** | **Připojení**
--- | ---
Virtuální počítače | Služba mobility spuštěná na virtuálních počítačích komunikuje s místním zařízením replikace (konfiguračním serverem) na portu HTTPS 443 příchozím pro správu replikací.<br/><br/> Virtuální počítače odesílají data replikace na procesový Server (spuštěný na počítači konfiguračního serveru) na portu HTTPS 9443 příchozí. Tento port lze změnit.
Replikační zařízení | Zařízení replikace orchestruje replikaci pomocí Azure přes odchozí port HTTPS 443.
Procesový Server | Procesový server přijímá data replikace, optimalizuje je a šifruje je a odesílá je do Azure Storage přes odchozí port 443.<br/> Ve výchozím nastavení běží na zařízení replikace procesový Server.


## <a name="replication-process"></a>Proces replikace

1. Když pro virtuální počítač povolíte replikaci, začne počáteční replikace do Azure Storage s použitím zadaných zásad replikace. 
2. Provoz se replikuje do veřejných koncových bodů Azure Storage přes Internet. Replikace provozu přes virtuální privátní síť (VPN) typu Site-to-Site z místní lokality do Azure se nepodporuje.
3. Po dokončení počáteční replikace se spustí rozdílová replikace. Sledované změny pro počítač jsou protokolovány.
4. Komunikace probíhá takto:
    - Virtuální počítače komunikují se zařízením replikace na portu HTTPS 443 příchozí, pro správu replikací.
    - Zařízení replikace orchestruje replikaci pomocí Azure přes odchozí port HTTPS 443.
    - Virtuální počítače odesílají data replikace na procesový Server (spuštěný na zařízení replikace) na portu HTTPS 9443 příchozí. Tento port lze změnit.
    - Procesový server přijímá data replikace, optimalizuje je a šifruje je a odesílá je do Azure Storage přes odchozí port 443.
5. Data replikace zaprotokolují první půdu v účtu úložiště mezipaměti v Azure. Tyto protokoly jsou zpracovávány a data jsou uložena na spravovaném disku Azure.

![Diagram znázorňuje architekturu procesu replikace.](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Upgrady zařízení

Zařízení se upgraduje ručně z centra Azure Migrate. Doporučujeme vždy spustit nejnovější verzi.

1. V Azure Migrate > servery > Azure Migrate: posouzení serveru, servery infrastruktury, klikněte na **konfigurační servery**.
2. V **konfiguračních serverech** se odkaz zobrazuje v části **verze agenta** , pokud je k dispozici nová verze zařízení replikace. 
3. Stáhněte instalační program do počítače zařízení pro replikaci a nainstalujte upgrade. Instalační program zjistí aktuálně běžící verzi na zařízení.
 
## <a name="next-steps"></a>Další kroky

- [Naučte](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance) se, jak nastavit zařízení replikace pro migraci virtuálních počítačů VMware na základě agentů.
- [Přečtěte si, jak](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) nastavit zařízení replikace pro fyzické servery.
