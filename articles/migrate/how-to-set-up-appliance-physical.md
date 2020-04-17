---
title: Nastavení zařízení Azure Migrate pro fyzické servery
description: Zjistěte, jak nastavit zařízení Azure Migrate pro posouzení fyzického serveru.
ms.service: azure-migrate
ms.topic: article
ms.date: 04/15/2020
ms.openlocfilehash: ddc70ee9430d3a767ce01191824c150a4dbd5e6f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538269"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Nastavení zařízení pro fyzické servery

Tento článek popisuje, jak nastavit zařízení Migrace Azure, pokud vyhodnocujete fyzické servery pomocí nástroje Azure Migrate: Server Assessment.

Zařízení Azure Migrate je zjednodušené zařízení, které používá Azure Migrate Server Assessment k provedení následujících akcí:

- Zjišťujte místní servery.
- Odešlete metadata a údaje o výkonu zjištěných serverů do Azure Migrate Server Assessment.

[Přečtěte si další informace](migrate-appliance.md) o zařízení Azure Migrate.


## <a name="appliance-deployment-steps"></a>Kroky nasazení zařízení

Chcete-li přístroj nastavit, tak:
- Stáhněte si zip soubor s instalačním skriptem Azure Migrate z webu Azure Portal.
- Extrahujte obsah ze souboru zip. Spusťte konzolu PowerShell s oprávněními správce.
- Spusťte skript Prostředí PowerShell a spusťte webovou aplikaci zařízení.
- Nakonfigurujte zařízení poprvé a zaregistrujte ho pomocí projektu Azure Migrate.

## <a name="download-the-installer-script"></a>Stažení instalačního skriptu

Stáhněte si zip soubor pro zařízení.

1. V **serverech cílů** > **migrace, které** > **Azure migruje: Vyhodnocení serveru**, klepněte na tlačítko **Objevit**.
2. V **discover strojích** > **Jsou vaše počítače virtualizované?**, klikněte na **Není virtualizováno/Jiné**.
3. Chcete-li stáhnout soubor zip, klepněte na tlačítko **Stáhnout.**

    ![Stažení Virtuálního softwaru](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Ověření zabezpečení

Před nasazením zkontrolujte, zda je soubor zip zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz pro generování hash pro soubor zip:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklad využití pro veřejný cloud:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
    - Příklad využití pro vládní cloud:```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Ověřte hodnoty hash:
 
    - Pro veřejný cloud (pro nejnovější verzi zařízení):

        **Algoritmus** | **Hodnota hash**
          --- | ---
          MD5 | 1e92ede3e87c03bd148e56a708cdd33f
          SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e6de7b9f475b6542beef114b20bfdac3c

    - Pro azure government (pro nejnovější verzi zařízení):

        **Algoritmus** | **Hodnota hash**
          --- | ---
          MD5 | f81c155fc4a1409901caea948713913f


## <a name="run-the-azure-migrate-installer-script"></a>Spuštění skriptu instalačního programu Azure Migrate
Instalační skript provádí následující akce:

- Nainstaluje agenty a webovou aplikaci pro zjišťování a hodnocení fyzického serveru.
- Nainstalujte role systému Windows, včetně služby aktivace systému Windows, služby IIS a prostředí PowerShell ISE.
- Stáhněte a nainstaluje modul s přepychovým službou IIS. [Další informace](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klíč registru (HKLM) s trvalými podrobnostmi o nastavení pro Azure Migrate.
- Vytvoří pod cestou následující soubory:
    - **Soubory konfigurace**: %Programdata%\Microsoft Azure\Config
    - **Soubory protokolu**: %Programdata%\Microsoft Azure\Protokoly

Spusťte skript takto:

1. Extrahujte soubor zip do složky na serveru, která bude hostitelem zařízení.  Ujistěte se, že nespustíte skript na počítači na existujícízařízení Azure Migrate.
2. Spusťte prostředí PowerShell na výše uvedeném serveru s oprávněním správce (se zvýšenými oprávněními).
3. Změňte adresář prostředí PowerShell na složku, do které byl obsah extrahován ze staženého zipového souboru.
4. Spusťte skript s názvem **AzureMigrateInstaller.ps1** spuštěním následujícího příkazu:

    - Pro veřejný cloud:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - Pro Azure Government:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    Skript spustí webové aplikace zařízení po úspěšném dokončení.

Pokud narazíte na nějaké problémy, můžete získat přístup k protokolům skriptů na adrese C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log pro řešení potíží.



### <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

Ujistěte se, že virtuální počítač zařízení se může připojit k adresám URL Azure pro [veřejné](migrate-appliance.md#public-cloud-urls) a [vládní](migrate-appliance.md#government-cloud-urls) cloudy.

## <a name="configure-the-appliance"></a>Konfigurace zařízení

Přístroj nastavte poprvé.

1. Otevřete prohlížeč na libovolném počítači, který se může připojit k virtuálnímu počítači, a otevřete adresu URL webové aplikace zařízení: **https:// název zařízení nebo IP*adresu*: 44368**.

   Případně můžete aplikaci otevřít z plochy kliknutím na zástupce aplikace.
2. Ve webové aplikaci > **Nastavení požadavků**postupujte takto:
    - **Licence**: Přijměte licenční podmínky a přečtěte si informace třetích stran.
    - **Připojení**: Aplikace zkontroluje, zda má virtuální uživatel přístup k internetu. Pokud virtuální virtuální server používá proxy server:
        - Klepněte na **položku Nastavení proxy serveru**a http://ProxyIPAddress zadejte adresu proxy a port pro naslouchání ve formuláři nebo http://ProxyFQDN.
        - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
        - Podporuje se jen proxy protokolu HTTP.
    - **Synchronizace času**: Čas je ověřen. Čas na zařízení by měl být synchronizován s časem internetu, aby zjišťování virtuálního počítači fungovalo správně.
    - **Aktualizace instalace**: Azure Migrate Server Assessment zkontroluje, zda má zařízení nainstalovány nejnovější aktualizace.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrace zařízení pomocí Migrace Azure

1. Klepněte na tlačítko **Přihlásit se**. Pokud se nezobrazí, ujistěte se, že jste v prohlížeči zakázali blokování automaticky otevíraných míst.
2. Na nové kartě se přihlaste pomocí přihlašovacích údajů Azure.
    - Přihlaste se pomocí svého uživatelského jména a hesla.
    - Přihlášení pomocí kódu PIN není podporováno.
3. Po úspěšném přihlášení se vraťte do webové aplikace.
4. Vyberte předplatné, ve kterém byl vytvořen projekt Migrace Azure. Pak vyberte projekt.
5. Zadejte název zařízení. Název by měl být alfanumerický s 14 znaky nebo méně.
6. Klepněte na tlačítko **Registrovat**.


## <a name="start-continuous-discovery"></a>Spuštění průběžného zjišťování

Připojte se z zařízení k fyzickým serverům a spusťte zjišťování.

1. Kliknutím na **Přidat pověření** určete přihlašovací údaje účtu, které bude zařízení používat ke zjišťování serverů.  
2. Zadejte **operační systém**, popisný název pověření a uživatelské jméno a heslo. Pak klikněte na **Přidat**.
Můžete přidat jednu sadu pověření pro servery Windows a Linux.
4. Klepněte na tlačítko **Přidat server**a zadejte podrobnosti o serveru – adresa FQDN/IP a popisný název pověření (jedna položka na řádek) pro připojení k serveru.
3. Klikněte na **Validate** (Ověřit). Po ověření je zobrazen seznam serverů, které lze zjistit.
    - Pokud se ověření serveru nezdaří, zkontrolujte chybu umístěním na ikonu ve sloupci **Stav.** Opravte problémy a znovu ověřte.
    - Chcete-li odebrat server, vyberte možnost > **Odstranit**.
4. Po ověření klikněte na **Uložit a spusťte zjišťování** a spusťte proces zjišťování.

Tím se spustí zjišťování. Trvá přibližně 15 minut, než se metadata zjištěných virtuálních počítačů zobrazí na webu Azure Portal.

## <a name="verify-servers-in-the-portal"></a>Ověření serverů na portálu

Po dokončení zjišťování můžete ověřit, zda se servery zobrazují na portálu.

1. Otevřete řídicí panel Migrace Azure.
2. V **Azure Migrate – servery, které** > **Azure migruje:** Stránka Vyhodnocení serveru, klikněte na ikonu, která zobrazuje počet **zjištěných serverů**.


## <a name="next-steps"></a>Další kroky

Vyzkoušejte [vyhodnocení fyzických serverů](tutorial-assess-physical.md) pomocí Azure Migrate Server Assessment.
