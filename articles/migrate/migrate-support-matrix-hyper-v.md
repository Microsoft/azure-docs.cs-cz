---
title: Azure Migrate podpůrná matice pro posouzení Hyper-V a migrace
description: Obsahuje souhrn nastavení a omezení pro posouzení Hyper-V a migrace pomocí služby Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/02/2019
ms.author: raynew
ms.openlocfilehash: f6edbe19429b38d68aea1f1ecfe426c9b2d194d0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811346"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>Matice podpory pro posouzení Hyper-V a migrace

Můžete použít [služby Azure Migrate](migrate-overview.md) vyhodnocovat a migrovat počítače do cloudu Microsoft Azure. Tento článek shrnuje podporu nastavení a omezení pro vyhodnocení a migrace místních virtuálních počítačů Hyper-V.



## <a name="hyper-v-scenarios"></a>Scénáře technologie Hyper-V

Tabulka shrnuje Podporované scénáře pro virtuální počítače Hyper-V.

**Nasazení** | **Podrobnosti*** 
--- | --- 
**Posouzení místních virtuálních počítačů Hyper-V** | [Nastavit](tutorial-prepare-hyper-v.md) první posouzení.<br/><br/> [Spustit](scale-hyper-v-assessment.md) ve velkém měřítku posouzení.
**Migrace virtuálních počítačů Hyper-V do Azure** | [Vyzkoušejte si](tutorial-migrate-hyper-v.md) migraci do Azure.

    

## <a name="azure-migrate-projects"></a>Projekty Azure Migrate

**Podpora** | **Podrobnosti**
--- | ---
Oprávnění Azure | Potřebujete oprávnění přispěvatele nebo vlastníka v rámci předplatného pro vytvoření projektu Azure Migrate.
Virtuální počítače Hyper-V | Posouzení až 10 000 virtuálních počítačů Hyper-V v jednom projektu.

Projekt může obsahovat virtuální počítače VMware a virtuálních počítačů Hyper-V, až do omezení hodnocení.


## <a name="assessment-hyper-v-host-requirements"></a>Požadavky na hostitele posouzení Hyper-V

| **Podpora**                | **Podrobnosti**               
| :-------------------       | :------------------- |
| **Nasazení hostitele**       | Hostitel Hyper-V může být samostatný nebo nasazen v clusteru. |
| **Oprávnění**           | Musíte mít oprávnění správce na hostiteli Hyper-V. |
| **Hostitelský operační systém** | Windows Server 2016 nebo Windows Server 2012 R2.<br/> Nelze vyhodnotit virtuální počítače na hostitelích Hyper-V se systémem Windows Server 2019. |
| **Vzdálená komunikace Powershellu**   | Musí být na všech hostitelích povolená. |
| **Replika technologie Hyper-V**       | Pokud používáte repliku technologie Hyper-V (nebo máte několika virtuálních počítačů pomocí stejné identifikátory virtuálního počítače) a zjistit původní a replikovaný virtuální počítač pomocí služby Azure Migrate, posouzení vygenerované službou Azure Migrate nemusí být přesné. |


## <a name="assessment-hyper-v-vm-requirements"></a>Požadavky na virtuální počítač posouzení Hyper-V

| **Podpora**                  | **Podrobnosti**               
| :----------------------------- | :------------------- |
| **Operační systém** | Všechny [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) a [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) operační systémy, které se nepodporuje v Azure. |
| **Oprávnění**           | Musíte mít oprávnění správce na každém virtuálním počítači Hyper-V, kterou chcete posoudit. |
| **Integrační služby**       | [Integrační služby Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) musí běžet na virtuálních počítačích, které budete vyhodnocovat, aby bylo možné zaznamenat informace o operačním systému. |
| **Požadované změny pro Azure** | Některé virtuální počítače můžou vyžadovat změny, aby mohla spustit v Azure. Azure Migrate provede tyto změny automaticky pro následující operační systémy:<br/> – Red Hat Enterprise Linux verze 6.5 + 7.0 +<br/> -CentOS 6.5 + 7.0 +</br> -Operačním systémem SUSE Linux Enterprise Server 12 SP1 +<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> -Debian 7,8<br/><br/> Pro další operační systémy budete muset provést úpravy ručně před migrací. Související články obsahují pokyny ohledně toho, jak to udělat. |
| **Spouštění systému Linux**                 | Je-li Boot na vyhrazené oddílů, by měl být uložený na disk s operačním systémem a nesmí být rozděleny mezi několik disků.<br/> Pokud Boot v rámci oddílu kořenový adresář (/), pak oddílu '/' by měl být na disku s operačním systémem a ostatní disky nejsou span. |
| **Spouštění UEFI**                  | Virtuální počítače se spouštěním UEFI se pro migraci podporováno. |
| **Disky a svazky šifrované**    | Virtuální počítače s disky nebo svazky šifrované nejsou pro migraci podporováno. |
| **RDM/průchozí disky**      | Pokud virtuální počítače mají RDM nebo průchozí disky, nebude možné tyto disky replikovat do Azure. |
| **NFS**                        | Připojit jako svazky na virtuálních počítačích svazky systému souborů NFS, se replikovat nebudou. |
| **Cílový disk**                | Posouzení služby Azure Migrate doporučujeme migraci na virtuální počítače Azure se spravovanými disky pouze. |


## <a name="assessment-appliance-requirements"></a>Požadavky na vyhodnocení zařízení

Pro vyhodnocení Azure Migrate spustí zjednodušené zařízení zjištění virtuálních počítačů Hyper-V a odesílala data metadat a výkonu virtuálních počítačů Azure Migrate. Na zařízení běží na virtuálním počítači Hyper-V a nastavit pomocí komprimované Hyper-V virtuálního pevného disku, který můžete stáhnout z webu Azure portal. Následující tabulka shrnuje požadavky na zařízení.

| **Podpora**                | **Podrobnosti**               
| :-------------------       | :------------------- |
| **Projekt Azure Migrate**  |  Zařízení můžou být spojené s jednoho projektu.<br/> Je možné vyhledat až 5000 virtuálních počítačů Hyper-V s použitím jednoho zařízení.
| **Omezení Hyper-V**    |  Nasazení zařízení jako virtuální počítač Hyper-V.<br/> Toto zařízení k dispozici virtuální počítač má virtuálních počítačů Hyper-V verze 5.0.<br/> Na hostiteli virtuálního počítače musí běžet Windows Server 2012 R2 nebo novější.<br/> Vyžaduje dostatek místa k přidělení 16 GB paměti RAM, 4 virtuální procesory a 1 externí přepínač pro zařízení virtuálního počítače.<br/> Zařízení vyžaduje statickou nebo dynamickou IP adresu a přístup k Internetu.
| **Technologie Hyper-V zařízení**      |  Zařízení je nastavený jako virtuální počítač Hyper-V.<br/> K dispozici ke stažení virtuální pevný disk je virtuální počítač Hyper-V verze 5.0.
| **Hostitel**                   | Hostitele virtuálního počítače se spuštěnou zařízení virtuálního počítače musí běžet Windows Server 2012 R2 nebo novější.<br/> Musí být dostatek místa k přidělení 16 GB paměti RAM, 4 virtuální procesory a jeden externí přepínač pro zařízení virtuálního počítače.<br/> Zařízení vyžaduje statickou nebo dynamickou IP adresu a přístup k Internetu. |
| **Podpora migrace**      | Ke spuštění replikace počítačů, služby migraci brány na zařízení musí být 1.18.7141.12919 nebo novější. Přihlášení do webové aplikace zařízení chcete zkontrolovat verzi. |

## <a name="assessment-appliance-url-access"></a>Přístup k adrese URL hodnocení zařízení

Pokud chcete posouzení virtuálních počítačů, musí zařízení Azure Migrate připojení k Internetu.

- Při nasazování na zařízení Azure Migrate provede kontrolu připojení k adresám URL shrnuté v následující tabulce.
- Pokud používáte firewall.proxy založené na adrese URL, povolte přístup k adresám URL v tabulce, ujistěte se, že proxy server odstraňuje všechny záznamy CNAME přijaté při hledání adresy URL.
- Pokud máte prověřuje zachycovací proxy server, můžete potřebovat pro import certifikátu serveru z proxy serveru do tohoto zařízení. 

    
**Adresa URL** | **Podrobnosti**  
--- | --- 
*.portal.azure.com | Navigace na webu Azure portal
*.windows.net | Přihlaste se ke svému předplatnému Azure.
*.microsoftonline.com | Vytvoření z aplikace Azure Active Directory pro zařízení, abyste komunikace služby.
management.azure.com | Vytvoření z aplikace Azure Active Directory pro zařízení, abyste komunikace služby.
dc.services.visualstudio.com | Protokolování a monitorování 
*.vault.azure.net | Při komunikaci mezi zařízením a služby, spravujte tajné kódy ve službě Azure Key Vault.


## <a name="assessment-port-requirements"></a>Požadavky na porty posouzení

Následující tabulka shrnuje požadavky na porty pro posouzení.

**zařízení** | **připojení**
--- | --- 
**Zařízení** | Příchozí připojení na portu TCP 3389 umožňující připojení ke vzdálené ploše do tohoto zařízení.<br/> Příchozí připojení na portu 44368 vzdálený přístup k aplikaci pro správu zařízení pomocí adresy URL: https://<appliance-ip-or-name>:44368<br/> Odchozí připojení na portu 443 odeslat výkonu a zjišťování metadat pro Azure Migrate.
**Hostitele nebo clusteru Hyper-V** | Příchozí připojení na portech WinRM 5985 (HTTP) a 5986 (HTTPS), aby metadat konfigurace a výkonu virtuálních počítačů Hyper-V s použitím relaci Common Information Model (CIM).

## <a name="migration-hyper-v-host-requirements"></a>Požadavky na hostitele migrace Hyper-V

| **Podpora**                | **Podrobnosti**               
| :-------------------       | :------------------- |
| **Nasazení hostitele**       | Hostitel Hyper-V může být samostatný nebo nasazen v clusteru. |
| **Oprávnění**           | Musíte mít oprávnění správce na hostiteli Hyper-V. |
| **Hostitelský operační systém** | Windows Server. 2019, Windows Server 2016 nebo Windows Server 2012 R2. |

## <a name="migration-hyper-v-vm-requirements"></a>Požadavky na virtuální počítač migrace Hyper-V

| **Podpora**                  | **Podrobnosti**               
| :----------------------------- | :------------------- |
| **Operační systém** | Všechny [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) a [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) operační systémy, které se nepodporuje v Azure. |
| **Oprávnění**           | Musíte mít oprávnění správce na každém virtuálním počítači Hyper-V, kterou chcete posoudit. |
| **Integrační služby**       | [Integrační služby Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) musí běžet na virtuálních počítačích, které budete vyhodnocovat, aby bylo možné zaznamenat informace o operačním systému. |
| **Požadované změny pro Azure** | Některé virtuální počítače můžou vyžadovat změny, aby mohla spustit v Azure. Azure Migrate provede tyto změny automaticky pro následující operační systémy:<br/> – Red Hat Enterprise Linux verze 6.5 + 7.0 +<br/> -CentOS 6.5 + 7.0 +</br> -Operačním systémem SUSE Linux Enterprise Server 12 SP1 +<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> -Debian 7,8<br/><br/> Pro další operační systémy budete muset provést úpravy ručně před migrací. Související články obsahují pokyny ohledně toho, jak to udělat. |
| **Spouštění systému Linux**                 | Je-li Boot na vyhrazené oddílů, by měl být uložený na disk s operačním systémem a nesmí být rozděleny mezi několik disků.<br/> Pokud Boot v rámci oddílu kořenový adresář (/), pak oddílu '/' by měl být na disku s operačním systémem a ostatní disky nejsou span. |
| **Spouštění UEFI**                  | Virtuální počítače se spouštěním UEFI se pro migraci podporováno. |
| **Disky a svazky šifrované**    | Virtuální počítače s disky nebo svazky šifrované nejsou pro migraci podporováno. |
| **RDM/průchozí disky**      | Pokud virtuální počítače mají RDM nebo průchozí disky, nebude možné tyto disky replikovat do Azure. |
| **NFS**                        | Připojit jako svazky na virtuálních počítačích svazky systému souborů NFS, se replikovat nebudou. |
| **Cílový disk**                | Můžete migrovat na virtuální počítače Azure se spravovanými disky pouze. |




## <a name="migration-hyper-v-host-url-access"></a>Přístup k adrese URL migrace-hostitele technologie Hyper-V

Následující tabulka shrnuje požadavky na přístup k adresu URL pro hostitele Hyper-V.

**Adresa URL** | **Podrobnosti**  
--- | ---
login.microsoftonline.com | Řízení přístupu a identit správě pomocí služby Active Directory.
*.backup.windowsazure.com | Koordinaci a přenosu dat replikace.
*.hypervrecoverymanager.windowsazure.com | Připojení k adresám URL služby Azure Migrate.
*.blob.core.windows.net | Nahrání dat do účtů úložiště.
dc.services.visualstudio.com | Nahrajte protokoly aplikace používá pro interní monitorování.
time.windows.com | Ověří časové synchronizace mezi systémovým a globálním časem.

## <a name="migration-port-access"></a>Přístup k portu migrace

Následující tabulka shrnuje požadavky na porty na hostitele Hyper-V a virtuálních počítačů pro migraci virtuálních počítačů.

**zařízení** | **připojení**
--- | --- 
Hostitelé technologie Hyper-V nebo virtuální počítače | Odchozí připojení na protokol HTTPS port 443 pro odesílání dat replikace virtuálního počítače do Azure Migrate.

  
## <a name="migration-vm-disk-support"></a>Podporu pro disky migrace virtuálního počítače 

**Podpora** | **Podrobnosti**
--- | ---
Migrované disky | Virtuální počítače je možné migrovat pouze na spravované disky (HHD standard, premium SSD) v Azure.
   

## <a name="next-steps"></a>Další postup

[Příprava na posouzení virtuálních počítačů Hyper-V](tutorial-prepare-hyper-v.md) pro migraci.




 
