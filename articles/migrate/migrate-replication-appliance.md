---
title: Zařízení pro replikaci Azure Migrate
description: Přečtěte si o zařízení replikace Azure Migrate pro migraci v systému VMWare založené na agentovi.
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 85641f514fc4367f02901eb1dd394cfa204c3ec4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535209"
---
# <a name="replication-appliance"></a>Zařízení pro replikaci

Tento článek popisuje replikační zařízení používané [Azure Migrate: Nástroj migrace serveru](migrate-services-overview.md#azure-migrate-server-migration-tool) při migraci virtuálních počítačů VMware, fyzických počítačů a virtuálních počítačů privátního/veřejného cloudu do Azure pomocí migrace na základě agenta. 


## <a name="overview"></a>Přehled

Zařízení replikace se nasadí při nastavování migrace virtuálních zařízení VMware nebo fyzických serverů založených na agentech. Nasadí se jako jeden místní počítač, buď jako virtuální počítač VMware nebo jako fyzický server. Běží:

- **Zařízení replikace**: Zařízení replikace koordinuje komunikaci a spravuje replikaci dat pro místní virtuální počítače VMware a fyzické servery, které se replikují do Azure.
- **Procesní server**: Procesový server, který je ve výchozím nastavení nainstalován v zařízení replikace, a provádí následující akce:
    - **Replikační brána**: Funguje jako replikační brána. Přijímá replikační data z počítačů povolených pro replikaci. Optimalizuje replikační data pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do Azure.
    - **Instalátor agenta**: Provádí nabízenou instalaci služby mobility. Tato služba musí být nainstalována a spuštěna v každém místním počítači, který chcete replikovat pro migraci.

## <a name="appliance-deployment"></a>Nasazení zařízení

**Použití** | **Podrobnosti**
--- |  ---
**Migrace založená na agentech virtuálních vm vsystému VMware** | Šablonu OVA si stáhnete z centra Migrace Azure a importujete na server vCenter a vytvoříte virtuální počítač zařízení.
**Migrace založená na agentovi fyzikálního počítače** | Pokud nemáte infrastrukturu VMware nebo pokud nemůžete vytvořit virtuální počítač VMware pomocí šablony OVA, stáhněte si instalační program softwaru z centra Migrace Azure a spusťte ho k nastavení počítače zařízení.

> [!NOTE]
> Pokud nasazujete ve službě Azure Government, použijte instalační soubor k nasazení zařízení replikace.

## <a name="appliance-requirements"></a>Požadavky na spotřebiče

Když nastavíte zařízení pro replikaci pomocí šablony OVA uvedené v centru Migrace Azure, zařízení spustí Windows Server 2016 a splňuje požadavky na podporu. Pokud zařízení replikace nastavíte ručně na fyzickém serveru, ujistěte se, že splňuje požadavky.

**Komponenta** | **Požadavek**
--- | ---
 | **Zařízení VMWare VM**
PowerCLI | [PowerCLI verze 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) by měla být nainstalována, pokud zařízení replikace běží na virtuálním počítači VMware.
Typ nic | VMXNET3 (pokud je zařízení VMware VM)
 | **Nastavení hardwaru**
Procesorová jádra | 8
Paměť RAM | 16 GB
Počet disků | Tři: Disk operačního systému, disk mezipaměti procesu a udržovací jednotka.
Volné místo na disku (cache) | 600 GB
Volné místo na disku (retenční disk) | 600 GB
**Nastavení softwaru** |
Operační systém | Windows Server 2016 nebo Windows Server 2012 R2
Licence | Zařízení je dodáváno s zkušební licencí windows serveru 2016, která je platná po dobu 180 dnů.<br/><br/> Pokud se zkušební doba blíží vypršení platnosti, doporučujeme stáhnout a nasadit nové zařízení nebo aktivovat licenci operačního systému virtuálního zařízení.
Národní prostředí operačního systému | Angličtina (en-us)
TLS | TLS 1.2 by měla být povolena.
.NET Framework | Rozhraní .NET Framework 4.6 nebo novější by mělo být nainstalováno v počítači (s povolenou silnou kryptografií.
MySQL | MySQL by měl být instalován na zařízení.<br/> MySQL by měla být nainstalována. Můžete nainstalovat ručně nebo site recovery můžete nainstalovat během nasazení zařízení.
Jiné aplikace | Nespouštějte v zařízení replikace jiné aplikace.
Role Windows Serveru | Nepovolujte tyto role: <br> – Active Directory Domain Services <br>– Internet Information Service <br> – Hyper-V
Zásady skupiny | Nepovolujte tyto zásady skupiny: <br> - Zabránit přístupu k příkazovému řádku. <br> - Zabránit přístupu k nástrojům pro úpravu registru. <br> - Logika důvěryhodnosti pro přílohy souborů. <br> - Zapněte spuštění skriptu. <br> [Další informace](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Žádné pre-existující výchozí webové stránky <br> - Žádné pre-existující webové stránky / aplikace poslech na portu 443 <br>- Povolit [anonymní ověřování](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Povolit nastavení [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)
**Nastavení sítě** |
Typ IP adresy | Statická
Porty | 443 (orchestrace řídicího kanálu)<br>9443 (přenos dat)
Typ nic | VMXNET3

## <a name="mysql-installation"></a>Instalace MySQL 

MySQL musí být nainstalován v počítači zařízení replikace. Může být instalován pomocí jedné z těchto metod.

**Metoda** | **Podrobnosti**
--- | ---
Ruční stažení a instalace | Stáhnout aplikaci MySQL & umístit ji do složky C:\Temp\ASRSetup a nainstalovat ji ručně.<br/> Při nastavte zařízení MySQL se zobrazí jako již nainstalován.
Bez online stahování | Umístěte instalační aplikaci MySQL do složky C:\Temp\ASRSetup. Při instalaci zařízení a kliknutím stáhnout a nainstalovat MySQL, instalační program bude používat instalační program, který jste přidali.
Stažení a instalace v Azure Migrate | Když nainstalujete zařízení a zobrazí se výzva k aplikaci MySQL, vyberte **možnost Stáhnout a nainstalovat**.

## <a name="url-access"></a>Přístup k adrese URL

Zařízení pro replikaci potřebuje přístup k těmto adresám URL ve veřejném cloudu Azure.

**Adresa URL** | **Podrobnosti**
--- | ---
\*.backup.windowsazure.com | Používá se pro replikovaný přenos dat a koordinaci
\*.store.core.windows.net | Používá se pro replikovaný přenos dat a koordinaci
\*.blob.core.windows.net | Slouží k přístupu k účtu úložiště, který ukládá replikovaná data.
\*.hypervrecoverymanager.windowsazure.com | Používá se pro operace správy replikace a koordinaci
https:\//management.azure.com | Používá se pro operace správy replikace a koordinaci
*.services.visualstudio.com | Používá se pro telemetrické účely (je volitelný)
time.windows.com | Používá se ke kontrole synchronizace mezi systémovým a globálním časem.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | Nastavení zařízení vyžaduje přístup k těmto adresám URL. Používají se pro řízení přístupu a správu identit službou Azure Active Directory.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Chcete-li dokončit MySQL ke stažení. V několika oblastech může být stahování přesměrováno na adresu URL CDN. Ujistěte se, že adresa URL CDN je v případě potřeby povolena také.


## <a name="azure-government-url-access"></a>Přístup k adresám URL Azure Government

Zařízení pro replikaci potřebuje přístup k těmto adresám URL v Azure Government.

**Adresa URL** | **Podrobnosti**
--- | ---
\*.backup.windowsazure.us | Používá se pro replikovaný přenos dat a koordinaci
\*.store.core.windows.net | Používá se pro replikovaný přenos dat a koordinaci
\*.blob.core.windows.net | Slouží k přístupu k účtu úložiště, který ukládá replikovaná data.
\*.hypervrecoverymanager.windowsazure.us | Používá se pro operace správy replikace a koordinaci
https:\//management.usgovcloudapi.net | Používá se pro operace správy replikace a koordinaci
*.services.visualstudio.com | Používá se pro telemetrické účely (je volitelný)
time.nist.gov | Používá se ke kontrole synchronizace mezi systémovým a globálním časem.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | Nastavení zařízení s OVA potřebuje přístup k těmto adresám URL. Používají se pro řízení přístupu a správu identit službou Azure Active Directory.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Chcete-li dokončit MySQL ke stažení. V několika oblastech může být stahování přesměrováno na adresu URL CDN. Ujistěte se, že adresa URL CDN je v případě potřeby povolena také.

## <a name="port-access"></a>Přístup k portu

**Zařízení** | **Připojení**
--- | ---
Virtuální počítače | Služba Mobility spuštěná na virtuálních počítačích komunikuje s místním replikačním zařízením (konfiguračním serverem) na příchozím portu HTTPS 443 pro správu replikace.<br/><br/> Virtuální počítače odesílají data replikace na procesní server (spuštěný na konfiguračním serveru) na příchozím portu HTTPS 9443. Tento port lze upravit.
Zařízení pro replikaci | Replikační zařízení orchestruje replikaci s Azure přes port HTTPS 443 odchozí.
Procesní server | Procesní server přijímá replikační data, optimalizuje je a šifruje a odesílá je do úložiště Azure přes odchozí port 443.<br/> Ve výchozím nastavení je na zařízení replikace spuštěn procesní server.


## <a name="replication-process"></a>Proces replikace

1. Když povolíte replikaci pro virtuální počítač, začne počáteční replikace do úložiště Azure pomocí zadaných zásad replikace. 
2. Provoz se replikuje do veřejných koncových bodů úložiště Azure přes internet. Replikace provozu přes virtuální privátní síť (VPN) mezi lokalitami z místního webu do Azure není podporována.
3. Po dokončení počáteční replikace začíná rozdílová replikace. Sledované změny pro počítač jsou protokolovány.
4. Komunikace probíhá takto:
    - Virtuální počítači komunikují s replikačním zařízením na příchozím portu HTTPS 443 pro správu replikace.
    - Replikační zařízení orchestruje replikaci s Azure přes port HTTPS 443 odchozí.
    - Virtuální počítači odesílají data replikace na procesní server (spuštěný na zařízení replikace) na příchozím portu HTTPS 9443. Tento port lze upravit.
    - Procesní server přijímá replikační data, optimalizuje je a šifruje a odesílá je do úložiště Azure přes odchozí port 443.
5. Protokoly dat replikace se nejprve připonou v účtu úložiště mezipaměti v Azure. Tyto protokoly jsou zpracovány a data se ukládají na spravovaném disku Azure.

![Architektura](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Vylepšení zařízení

Zařízení se upgraduje ručně z centra Azure Migrate. Doporučujeme vždy spustit nejnovější verzi.

1. V Azure Migrate > Servery > Azure Migrate: Server Assessment, Infrastruktura servery, klikněte na **konfigurační servery**.
2. V **konfiguračních serverech**se ve **verzi agenta** zobrazí odkaz, pokud je k dispozici nová verze zařízení replikace. 
3. Stáhněte instalační program do počítače zařízení replikace a nainstalujte upgrade. Instalační program detekuje aktuální verzi, která je spuštěna na zařízení.
 
## <a name="next-steps"></a>Další kroky

- [Přečtěte si, jak](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance) nastavit zařízení replikace pro migraci virtuálních mís v y vsystému VMware založené na agentovi.
- [Přečtěte si, jak](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) nastavit zařízení replikace pro fyzické servery.
