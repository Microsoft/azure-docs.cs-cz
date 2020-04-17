---
title: Nastavení zařízení Azure Migrate pro VMware
description: Zjistěte, jak nastavit zařízení Azure Migrate pro posouzení a migraci virtuálních počítačích VMware.
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: b32c6a9b703e4d341fe353d6b472ea7a18adadf3
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538252"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Nastavení zařízení pro virtuální zařízení VMware

V tomto článku nastavte zařízení Azure Migrate pro vyhodnocení pomocí nástroje [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) a migrace bez agenta pomocí nástroje [Migrace:server Azure.](migrate-services-overview.md#azure-migrate-server-migration-tool)

[Zařízení Migrace Azure](migrate-appliance.md) je zjednodušené zařízení používané Azure Migrate:Server Assessment a Migrace serveru ke zjišťování místních virtuálních měn VMware, odesílání dat metadat a výkonu virtuálního počítače do Azure a replikace virtuálních měn VMware během migrace bez agenta.

Zařízení můžete nasadit pomocí několika metod:

- Nastavení virtuálního počítače VMware pomocí stažené šablony OVA. Toto je metoda popsaná v tomto článku.
- Nastavení na virtuálním počítači VMware nebo fyzickém počítači se skriptem instalačního programu prostředí PowerShell. [Tato metoda](deploy-appliance-script.md) by se měla použít, pokud nemůžete nastavit virtuální počítač pomocí šablony OVA, nebo pokud jste ve vládě Azure.

Po vytvoření zařízení zkontrolujte, zda se může připojit k Azure Migrate:Server Assessment, poprvé ho nakonfigurovat a zaregistrovat v projektu Azure Migrate.


## <a name="appliance-deployment-ova"></a>Nasazení zařízení (OVA)

Chcete-li přístroj nastavit pomocí šablony OVA, tak:
- Stáhněte soubor šablony OVA a importujte jej do serveru vCenter.
- Vytvořte zařízení a zkontrolujte, zda se může připojit k Azure Migrate Server Assessment.
- Nakonfigurujte zařízení poprvé a zaregistrujte ho pomocí projektu Azure Migrate.

## <a name="download-the-ova-template"></a>Stažení šablony OVA

1. V **serverech cílů** > **migrace, které** > **Azure migruje: Vyhodnocení serveru**, klepněte na tlačítko **Objevit**.
2. V **discover machines** > **Jsou vaše počítače virtualizované?**, klikněte na **Ano, s hypervisorem VMWare vSphere**.
3. Pokud si chcete stáhnout soubor šablony .OVA, klikněte na **Stáhnout**.

  ![Výběry pro stažení souboru OVA](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Ověření zabezpečení

Před nasazením souboru OVA zkontrolujte, zda je soubor OVA zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz, chcete-li generovat hash pro OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklady použití: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Pro nejnovější verzi zařízení by se vygenerovaná hash měla shodovat s těmito [nastaveními](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware#verify-security).



## <a name="create-the-appliance-vm"></a>Vytvoření virtuálního virtuálního zařízení

Importujte stažený soubor a vytvořte virtuální hod.

1. V konzole klienta vSphere klepněte na **položku Nasazení souboru** > **ovf šablony**.
![Příkaz nabídky pro nasazení šablony OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. V Průvodci nasazením šablony OVF > **zdroj**zadejte umístění souboru OVA.
3. V **části Název** a **umístění**zadejte popisný název virtuálního soudu. Vyberte objekt zásob, ve kterém bude virtuální ms hostován.
5. V **hostiteli/clusteru**určete hostitele nebo cluster, na kterém bude virtuální počítač spuštěn.
6. V **úložišti**zadejte cíl úložiště pro virtuální ho.
7. V části **Disk Format** (Formát disku) zadejte typ a velikost disku.
8. V **mapování sítě**určete síť, ke které se bude virtuální montovna připojovat. Síť potřebuje připojení k internetu, k odeslání metadat do Azure Migrate Server Assessment.
9. Zkontrolujte a ověřte všechna nastavení a pak klikněte na **Finish** (Dokončit).


## <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

Ujistěte se, že virtuální počítač zařízení se může připojit k adresám URL Azure pro [veřejné](migrate-appliance.md#public-cloud-urls) a [vládní](migrate-appliance.md#government-cloud-urls) cloudy.


## <a name="configure-the-appliance"></a>Konfigurace zařízení

Přístroj nastavte poprvé. Pokud nasadíte zařízení pomocí skriptu namísto šablony OVA, první dva kroky v postupu nejsou použitelné.

1. V konzoli vSphere Client klikněte pravým tlačítkem na daný virtuální počítač a pak na **Open Console** (Otevřít konzolu).
2. Zadejte jazyk, časové pásmo a heslo pro zařízení.
3. Otevřete prohlížeč na libovolném počítači, který se může připojit k virtuálnímu počítači, a otevřete adresu URL webové aplikace zařízení: **https:// název zařízení nebo IP*adresu*: 44368**.

   Případně můžete aplikaci otevřít z plochy zařízení kliknutím na zástupce aplikace.
4. Ve webové aplikaci > **Nastavení požadavků**postupujte takto:
    - **Licence**: Přijměte licenční podmínky a přečtěte si informace třetích stran.
    - **Připojení**: Aplikace zkontroluje, zda má virtuální uživatel přístup k internetu. Pokud virtuální virtuální server používá proxy server:
        - Klepněte na **položku Nastavení proxy serveru**a http://ProxyIPAddress zadejte adresu proxy a port pro naslouchání ve formuláři nebo http://ProxyFQDN.
        - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
        - Podporuje se jen proxy protokolu HTTP.
    - **Synchronizace času**: Čas je ověřen. Čas na zařízení by měl být synchronizován s časem internetu, aby zjišťování fungovalo správně.
    - **Aktualizace instalace**: Azure Migrate kontroluje, že jsou nainstalovány nejnovější aktualizace zařízení.
    - **Instalace VDDK**: Migrace Azure kontroluje, zda je nainstalována sada VMWare vSphere Virtual Disk Development Kit (VDDK).
        - Azure Migrates používá VDDK k replikaci počítačů během migrace do Azure.
        - Stáhněte si vddk 6.7 ze společnosti VMware a extrahujte stažený obsah zipu do určeného umístění na zařízení.

## <a name="register-the-appliance-with-azure-migrate"></a>Registrace zařízení pomocí Migrace Azure

1. Klepněte na tlačítko **Přihlásit se**. Pokud se nezobrazí, ujistěte se, že jste v prohlížeči zakázali blokování automaticky otevíraných míst.
2. Na nové kartě se přihlaste pomocí přihlašovacích údajů Azure.
    - Přihlaste se pomocí svého uživatelského jména a hesla.
    - Přihlášení pomocí kódu PIN není podporováno.
3. Po úspěšném přihlášení se vraťte do webové aplikace.
2. Vyberte předplatné, ve kterém byl vytvořen projekt Migrace Azure. Pak vyberte projekt.
3. Zadejte název zařízení. Název by měl být alfanumerický s 14 znaky nebo méně.
4. Klepněte na tlačítko **Registrovat**.


## <a name="start-continuous-discovery-by-providing-vcenter-server-and-vm-credential"></a>Spuštění průběžného zjišťování poskytnutím pověření serveru vCenter a virtuálního zařízení

Zařízení se musí připojit k serveru vCenter, aby zjistilo údaje o konfiguraci a výkonu virtuálních počítačů.

### <a name="specify-vcenter-server-details"></a>Zadání podrobností vCenter Serveru
1. V **části Zadejte podrobnosti o serveru vCenter**zadejte název (FQDN) nebo ADRESU IP serveru vCenter. Můžete ponechat výchozí port nebo zadat vlastní port, na kterém váš server vCenter naslouchá.
2. V **části Uživatelské jméno** a **heslo**zadejte přihlašovací údaje účtu jen pro čtení, které bude zařízení používat ke zjišťování virtuálních počítačů na serveru vCenter. Zjišťování můžete obor omezením přístupu k účtu vCenter. [Další informace](set-discovery-scope.md).
3. Klikněte na **Ověřit připojení** a ujistěte se, že se zařízení může připojit k serveru vCenter.

### <a name="specify-vm-credentials"></a>Určení pověření virtuálního virtuálního ms
Pro zjišťování aplikací, rolí a funkcí a vizualizaci závislostí virtuálních aplikací můžete poskytnout pověření virtuálního zařízení, které má přístup k virtuálním mům VMware. Můžete přidat jedno pověření pro virtuální počítače se systémem Windows a jedno pověření pro virtuální počítače s Linuxem. [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) o potřebných přístupových oprávněních.

> [!NOTE]
> Tento vstup je volitelný a je potřeba k povolení zjišťování aplikací a vizualizace závislostí bez agenta.

1. V **discover aplikacích a závislostech na virtuálních počítačích**klikněte na **Přidat pověření**.
2. Vyberte **možnost Operační systém**.
3. Zadejte popisný název pověření.
4. V **poli Uživatelské jméno** a **heslo**zadejte účet, který má alespoň přístup hosta na virtuálních počítačích.
5. Klikněte na tlačítko **Add** (Přidat).

Po zadání pověření vCenter Serveru a virtuálního počítače (volitelné), klepněte na tlačítko **Uložit a spustit zjišťování** zahájit zjišťování místního prostředí.

Trvá přibližně 15 minut, než se metadata zjištěných virtuálních volání zobrazí na portálu. Zjišťování nainstalovaných aplikací, rolí a funkcí nějakou dobu trvá, doba trvání závisí na počtu zjištěných virtuálních počítačích. U 500 virtuálních počítačů trvá přibližně 1 hodinu, než se inventář aplikací zobrazí na portálu Migrace Azure.

## <a name="next-steps"></a>Další kroky

Projděte si kurzy pro [hodnocení vmware](tutorial-assess-vmware.md) a [migraci bez agentů](tutorial-migrate-vmware.md).
