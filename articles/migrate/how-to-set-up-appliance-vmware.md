---
title: Nastavení zařízení pro Azure Migrate posouzení/migrace serveru pro virtuální počítače VMware | Dokumentace Microsoftu
description: Popisuje, jak nastavit zařízení pro zjišťování, vyhodnocení a migrace bez agentů virtuálních počítačů VMware pomocí Azure Migrate posouzení/migrace serveru.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: fe190381df346278e75a3e6fd9876b80c33bd86b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811723"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Nastavení zařízení pro virtuální počítače VMware

Tento článek popisuje, jak nastavit službu Azure Migrate zařízení, pokud posouzení virtuálních počítačů VMware pomocí Azure Migrate Server Assessment tool, nebo migraci virtuálních počítačů VMware do Azure pomocí funkce agentless migrace pomocí nástroje Azure Migrate serveru migrace.

Virtuální počítač VMware zařízení je zjednodušené zařízení pro pomocí Azure Migrate posouzení/migrace serveru postupujte takto:

- Zjištění místních virtuálních počítačů VMware.
- Odeslání dat metadata a výkonu pro zjištěné virtuální počítače do Azure migrovat posouzení/migrace serveru.

[Další informace](migrate-appliance.md) o zařízení Azure Migrate.


## <a name="appliance-deployment-steps"></a>Postup nasazení zařízení

Nastavení zařízení můžete:
- Stáhnout soubor OVA šablony a importovat ho do systému vCenter Server.
- Toto zařízení vytvoříte tak a zkontrolujte, že se může připojit k Azure Migrate Server Assessment. 
- Konfigurace zařízení poprvé a zaregistrujte je v projektu Azure Migrate.

## <a name="download-the-ova-template"></a>Stáhnout šablonu pro soubory OVA

1. V **cílů migrace** > **servery** > **Azure Migrate: Server Assessment**, klikněte na tlačítko **Discover**.
2. V **zjistit počítače** > **jsou vaše počítače virtualizované?** , klikněte na tlačítko **Ano, s VMWare vSphere hypervisor**.
3. Klikněte na tlačítko **Stáhnout** ke stažení. Soubor OVA šablony.



### <a name="verify-security"></a>Ověření zabezpečení

Zkontrolujte, zda soubor OVA zabezpečené, před jejím nasazením.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spuštěním následujícího příkazu vygenerujete hodnotu hash pro soubor OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklady použití: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Pro verzi zařízení 1.0.0.5 vygenerovaná hodnota hash by měla odpovídat následujícímu nastavení. 

  **Algoritmus** | **Hodnota hash**
  --- | ---
  MD5 | ddfdf21c64af02a222ed517ce300c977


## <a name="create-the-appliance-vm"></a>Toto zařízení vytvoříte virtuální počítač

Importujte stažený soubor a vytvoření virtuálního počítače.

1. V konzoli vSphere Client klikněte na **File** (Soubor) > **Deploy OVF Template** (Nasadit šablonu OVF).
2. V Průvodci nasazením šablony OVF > **zdroj**, zadejte umístění souboru pro soubory OVA.
3. V **název** a **umístění**, zadejte popisný název pro virtuální počítač. Vyberte objekt inventáře, ve kterém bude virtuální počítač hostovaný.
5. V **hostiteli/clusteru**, zadejte hostitele nebo cluster, na kterého se spouští virtuální počítač.
6. V **úložiště**, zadejte cílové úložiště pro virtuální počítač.
7. V části **Disk Format** (Formát disku) zadejte typ a velikost disku.
8. V **mapování sítě**, zadejte síť, ke kterým se připojí virtuální počítač. Síť vyžaduje připojení k Internetu, chcete-li odesílat metadata do Azure migrovat Server Assessment.
9. Zkontrolujte a ověřte všechna nastavení a pak klikněte na **Finish** (Dokončit).


### <a name="verify-appliance-access-to-azure"></a>Ověřit přístup zařízení k Azure

Ujistěte se, že zařízení virtuálního počítače může připojit k [adresám URL služby Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


## <a name="configure-the-appliance"></a>Konfigurace zařízení

Nastavení zařízení poprvé.

1. V konzoli vSphere Client klikněte pravým tlačítkem na daný virtuální počítač a pak na **Open Console** (Otevřít konzolu).
2. Zadejte jazyk, časové pásmo a heslo pro zařízení.
3. Otevřete prohlížeč na jakémkoli počítači můžete připojit k virtuálnímu počítači a otevřít adresu URL webové aplikace pro zařízení: **https://*zařízení název nebo IP adresu*: 44368**.

   Alternativně můžete otevřít aplikaci z plochy zařízení kliknutím na zástupce aplikace.
4. Ve webové aplikaci > **nastavit požadavky**, postupujte takto:
    - **Licence**: Přijměte licenční podmínky a přečtěte si informace třetích stran.
    - **Připojení**: Je aplikace zkontroluje, že virtuální počítač má přístup k Internetu. Pokud virtuální počítač používá proxy server:
        - Klikněte na tlačítko **nastavení proxy serveru**a zadejte adresu proxy serveru a nastavte naslouchající port ve tvaru http://ProxyIPAddress nebo http://ProxyFQDN.
        - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
        - Podporuje se jen proxy protokolu HTTP.
    - **Čas synchronizace**: Čas je ověřený. Čas na zařízení by měl být synchronizovaný s internetovým časem zjišťování fungovala správně.
    - **Instalace aktualizací**: Azure Migrate ověří, že jsou nainstalované nejnovější aktualizace zařízení.
    - **Nainstalujte VDDK**: Azure Migrate ověřuje, že je nainstalovaná klientské konzole VMWare vSphere virtuální Disk Development Kit (VDDK).
        - Azure Migrates VDDK používá k replikaci počítačů během migrace do Azure.
        - Stáhněte si VDDK 6.7 z VMware a extrahujte obsah stažený zip do zadaného umístění na zařízení.

## <a name="register-the-appliance-with-azure-migrate"></a>Registrace zařízení pomocí služby Azure Migrate

1. Klikněte na tlačítko **přihlášení**. Pokud se nezobrazí, ujistěte se, že jsme deaktivovali blokování automaticky otevíraných oken v prohlížeči.
2. Na nové kartě Přihlaste se pomocí přihlašovacích údajů Azure. 
    - Přihlaste se pomocí uživatelského jména a hesla.
    - Přihlaste se pomocí kódu PIN není podporován.
3. Po úspěšném přihlášení přejděte zpět do webové aplikace.
2. Vyberte předplatné, ve kterém byl vytvořen projekt Azure Migrate. Vyberte projekt.
3. Zadejte název pro zařízení. Název musí být alfanumerické znaky se 14 znaků nebo méně.
4. Klikněte na tlačítko **zaregistrovat**.


## <a name="start-continuous-discovery"></a>Spustit průběžné vyhledávání

Nyní připojení ze zařízení do systému vCenter Server a spusťte zjišťování virtuálních počítačů. 

1. V **zadejte podrobnosti vCenter serveru**, zadejte název (FQDN) nebo IP adresu vcenter serveru. Můžete ponechat výchozí port, nebo zadat vlastní port, na kterém vCenter Server naslouchá.
2. V **uživatelské jméno** a **heslo**, zadejte přihlašovací údaje účtu jen pro čtení, které zařízení použije ke zjištění virtuálních počítačů na serveru vCenter. Ujistěte se, že účet má [požadovaná oprávnění pro zjišťování](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions).
3. Klikněte na tlačítko **ověřit připojení** abyste měli jistotu, že zařízení může připojit k serveru vCenter.
4. Jakmile se naváže připojení, klikněte na tlačítko **uložit a spustit zjišťování**.


Tím se spustí zjišťování. Trvá přibližně 15 minut metadat zjištěných virtuálních počítačů se na portálu. 


## <a name="next-steps"></a>Další postup

Přečtěte si podrobné pokyny pro [VMware posouzení](tutorial-assess-vmware.md) a [migrace bez agenta](tutorial-migrate-vmware.md).
