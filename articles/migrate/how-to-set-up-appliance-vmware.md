---
title: Nastavení zařízení Azure Migrate pro VMware
description: Naučte se, jak nastavit zařízení Azure Migrate pro vyhodnocení a migraci virtuálních počítačů VMware.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: adb78682e80d78aa4fa6b48507f1fcce789a3fe1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75360170"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Nastavení zařízení pro virtuální počítače VMware

Tento článek popisuje, jak nastavit zařízení Azure Migrate, pokud vyhodnotili virtuální počítače VMware pomocí nástroje pro vyhodnocení Azure Migrate serveru nebo migrujete virtuální počítače VMware do Azure s migrací bez agenta pomocí nástroje pro migraci Azure Migrate serveru.

Zařízení VMware VM je jednoduché zařízení, které používá Azure Migrate posouzení/migrace serveru, a to s těmito kroky:

- Umožňuje zjistit místní virtuální počítače VMware.
- Odesílat metadata a data o výkonu pro zjištěné virtuální počítače, aby bylo možné Azure Migrate posouzení/migrace serveru.

[Přečtěte si další informace](migrate-appliance.md) o zařízení Azure Migrate.


## <a name="appliance-deployment-steps"></a>Postup nasazení zařízení

Nastavení zařízení:
- Stáhněte soubor šablony vajíček a naimportujte ho do vCenter Server.
- Vytvořte zařízení a ověřte, že se může připojit k Azure Migrate posouzení serveru.
- Nakonfigurujete zařízení poprvé a zaregistrujete ho do projektu Azure Migrate.

## <a name="download-the-ova-template"></a>Stažení šablony pro VAJÍČKy

1. V ** > ** **cíli migrace** > **Azure Migrate: posouzení serveru**, klikněte na **zjistit**.
2. V části **Zjistit počítače** > **Máte počítače ve virtuální podobě?** klikněte na **Ano, s hypervisorem VMware vSphere**.
3. Pokud si chcete stáhnout soubor šablony .OVA, klikněte na **Stáhnout**.



### <a name="verify-security"></a>Ověřit zabezpečení

Před nasazením ověřte, zda je soubor sady vajíček zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz, který vygeneruje hodnotu hash pro VAJÍČKu:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklady použití: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Pro nejnovější verzi zařízení by se vygenerovaná hodnota hash měla shodovat s tímto nastavením.

  **Algoritmus** | **Hodnota hash**
  --- | ---
  MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
  SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c


## <a name="create-the-appliance-vm"></a>Vytvoření virtuálního počítače zařízení

Naimportujte stažený soubor a vytvořte virtuální počítač.

1. V konzoli vSphere Client klikněte na **File** (Soubor) > **Deploy OVF Template** (Nasadit šablonu OVF).
2. V Průvodci nasazením šablony OVF > **zdroj**zadejte umístění souboru vajíček.
3. Do pole **název** a **umístění**zadejte popisný název virtuálního počítače. Vyberte objekt inventáře, do kterého bude virtuální počítač hostovat.
5. V části **hostitel nebo cluster**zadejte hostitele nebo cluster, na kterém se virtuální počítač spustí.
6. V části **Storage (úložiště**) zadejte cíl úložiště pro virtuální počítač.
7. V části **Disk Format** (Formát disku) zadejte typ a velikost disku.
8. V části **mapování sítě**určete síť, ke které se bude virtuální počítač připojovat. Síť potřebuje připojení k Internetu, aby odesílala metadata Azure Migrate posouzení serveru.
9. Zkontrolujte a ověřte všechna nastavení a pak klikněte na **Finish** (Dokončit).


### <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

Ujistěte se, že se virtuální počítač zařízení může připojit k [adresám URL Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


## <a name="configure-the-appliance"></a>Konfigurace zařízení

Nastavte zařízení poprvé.

1. V konzoli vSphere Client klikněte pravým tlačítkem na daný virtuální počítač a pak na **Open Console** (Otevřít konzolu).
2. Zadejte jazyk, časové pásmo a heslo pro zařízení.
3. Otevřete prohlížeč na jakémkoli počítači, který se může připojit k VIRTUÁLNÍmu počítači, a otevřete adresu URL webové aplikace zařízení: ***název zařízení https://nebo IP adresa*: 44368**.

   Alternativně můžete aplikaci otevřít z plochy zařízení kliknutím na zástupce aplikace.
4. Ve webové aplikaci > **nastavení požadavků**postupujte takto:
    - **Licence**: přijměte licenční podmínky a přečtěte si informace třetích stran.
    - **Připojení**: aplikace kontroluje, jestli má virtuální počítač přístup k Internetu. Pokud virtuální počítač používá proxy server:
        - Klikněte na **nastavení proxy serveru**a zadejte adresu proxy serveru a port naslouchání ve formuláři http://ProxyIPAddress nebo http://ProxyFQDN.
        - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
        - Podporuje se jen proxy protokolu HTTP.
    - **Časová synchronizace**: čas je ověřený. Čas na zařízení by měl být synchronizovaný s internetovým časem, aby zjišťování fungovalo správně.
    - **Instalovat aktualizace**: Azure Migrate kontroluje, jestli jsou nainstalované nejnovější aktualizace zařízení.
    - **Instalace VDDK**: Azure Migrate kontroluje, jestli je nainstalovaná sada Virtual disks Development Kit (VDDK) VMware vSphere.
        - Migrace do Azure používá VDDK k replikaci počítačů během migrace do Azure.
        - Stáhněte si VDDK 6,7 z VMware a Extrahujte stažený obsah zip do zadaného umístění na zařízení.

## <a name="register-the-appliance-with-azure-migrate"></a>Zaregistrovat zařízení ve Azure Migrate

1. Klikněte na **Přihlásit se**. Pokud se nezobrazí, ujistěte se, že jste v prohlížeči zakázali blokování automaticky otevíraných oken.
2. Na nové kartě se přihlaste pomocí svých přihlašovacích údajů Azure.
    - Přihlaste se pomocí svého uživatelského jména a hesla.
    - Přihlášení pomocí PIN kódu se nepodporuje.
3. Po úspěšném přihlášení se vraťte k webové aplikaci.
2. Vyberte předplatné, ve kterém byl vytvořen Azure Migrate projekt. Pak vyberte projekt.
3. Zadejte název zařízení. Název by měl být alfanumerický a nesmí obsahovat více než 14 znaků.
4. Klikněte na **zaregistrovat**.


## <a name="start-continuous-discovery-by-providing-vcenter-server-and-vm-credential"></a>Spustit průběžné zjišťování poskytnutím vCenter Server a přihlašovacích údajů k virtuálnímu počítači

Aby bylo možné zjistit konfiguraci a údaje o výkonu virtuálních počítačů, musí se zařízení připojit k vCenter Server.

### <a name="specify-vcenter-server-details"></a>Zadání podrobností vCenter Serveru
1. V části **zadat vCenter Server podrobnosti**zadejte název (FQDN) nebo IP adresu vCenter Server. Můžete ponechat výchozí port nebo zadat vlastní port, na kterém vCenter Server naslouchá.
2. V části **uživatelské jméno** a **heslo**zadejte přihlašovací údaje účtu jen pro čtení, které zařízení použije ke zjištění virtuálních počítačů na serveru vCenter. Ujistěte se, že účet má [požadovaná oprávnění pro zjišťování](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions). Rozsah zjišťování můžete omezit tak, že omezíte přístup k účtu vCenter odpovídajícím způsobem; Další informace o zjišťování oboru [najdete tady](tutorial-assess-vmware.md#set-the-scope-of-discovery).
3. Klikněte na **ověřit připojení** a ujistěte se, že se zařízení může připojit k vCenter Server.

### <a name="specify-vm-credentials"></a>Zadat přihlašovací údaje virtuálního počítače
Pro zjišťování aplikací, rolí a funkcí a vizualizací závislostí virtuálních počítačů můžete zadat přihlašovací údaje k virtuálnímu počítači, které mají přístup k virtuálním počítačům VMware. Můžete přidat jednu přihlašovací údaje pro virtuální počítače s Windows a jedno přihlašovací údaje pro virtuální počítače se systémem Linux. [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions) o požadovaných oprávněních pro přístup.

> [!NOTE]
> Tento vstup je nepovinný a je potřeba, aby se povolilo zjišťování aplikací a vizualizace závislostí bez agentů.

1. V nabídce **zjistit aplikace a závislosti na virtuálních počítačích**klikněte na **Přidat přihlašovací údaje**.
2. Vyberte **operační systém**.
3. Zadejte popisný název přihlašovacích údajů.
4. V části **uživatelské jméno** a **heslo**zadejte účet, který má alespoň přístup k hostům na virtuálních počítačích.
5. Klikněte na tlačítko **Add** (Přidat).

Po zadání vCenter Server a přihlašovacích údajů k virtuálnímu počítači (volitelné) klikněte na **Uložit a spusťte zjišťování** a spusťte zjišťování místního prostředí.

Zobrazení metadat zjištěných virtuálních počítačů na portálu trvá přibližně 15 minut. Zjišťování nainstalovaných aplikací, rolí a funkcí nějakou dobu trvá, doba trvání závisí na počtu zjištěných virtuálních počítačů. Pro virtuální počítače 500 trvá na portálu Azure Migrate přibližně 1 hodinu, než se inventář aplikace zobrazí.

## <a name="next-steps"></a>Další kroky

Přečtěte si kurzy pro [vyhodnocení VMware](tutorial-assess-vmware.md) a [migraci bez agentů](tutorial-migrate-vmware.md).
