---
title: Nastavení zařízení Azure Migrate pro fyzické servery
description: Přečtěte si, jak nastavit zařízení Azure Migrate pro posouzení fyzického serveru.
ms.service: azure-migrate
ms.topic: article
ms.date: 04/15/2020
ms.openlocfilehash: 6d9cc071ad5d81a09a14b12fe2acdf564c2ea6c8
ms.sourcegitcommit: 79508e58c1f5c58554378497150ffd757d183f30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84331776"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Nastavení zařízení pro fyzické servery

Tento článek popisuje, jak nastavit zařízení Azure Migrate, pokud vyhodnotili fyzické servery pomocí nástroje Azure Migrate: Server Assessment Tool.

Zařízení Azure Migrate je jednoduché zařízení, které využije Azure Migrate Server Assessment k tomu, abyste mohli provádět následující akce:

- Objevte místní servery.
- Odesílat metadata a data o výkonu pro zjištěné servery pro Azure Migrate posouzení serveru.

[Přečtěte si další informace](migrate-appliance.md) o zařízení Azure Migrate.


## <a name="appliance-deployment-steps"></a>Postup nasazení zařízení

Nastavení zařízení:
- Stáhněte si soubor ZIP pomocí skriptu Azure Migrate Installer z Azure Portal.
- Extrahujte obsah ze souboru ZIP. Spusťte konzolu PowerShellu s oprávněními správce.
- Spusťte skript prostředí PowerShell pro spuštění webové aplikace zařízení.
- Nakonfigurujete zařízení poprvé a zaregistrujete ho do projektu Azure Migrate.

## <a name="download-the-installer-script"></a>Stažení instalačního skriptu

Stáhněte si soubor zip pro zařízení.

1. V Azure Migrate **cíle migrace**  >  na**servery**  >  **: vyhodnocování serveru**klikněte na **zjistit**.
2. V nabídce **zjistit**počítače  >  **jsou vaše počítače virtualizované?** klikněte na **nevirtualizované/jiné**.
3. Kliknutím na **Stáhnout** Stáhněte soubor zip.

    ![Stáhnout virtuální počítač](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Ověřit zabezpečení

Před nasazením souboru ZIP ověřte, zda je soubor zip zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz, který vygeneruje hodnotu hash pro soubor zip:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklad použití pro veřejný cloud:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
    - Příklad použití pro oficiální Cloud:```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Ověřte nejnovější verzi zařízení a hodnoty hash:
 
    - Pro veřejný cloud:

        **Scénář** | **Stáhnout*** | **Hodnota hash**
        --- | --- | ---
        Fyzický (63,1 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2105112) | 0a27adf13cc5755e4b23df0c05732c6ac08d1fe8850567cb57c9906fbc3b85a0

    - Pro Azure Government:

        **Scénář** | **Stáhnout*** | **Hodnota hash**
        --- | --- | ---
        Fyzický (63,1 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2120100&clcid=0x409) | 93dfef131026e70acdfad2769cd208ff745ab96a96f013cdf3f9e1e61c9b37e1


## <a name="run-the-azure-migrate-installer-script"></a>Spusťte skript instalačního programu Azure Migrate
Skript instalačního programu provede následující akce:

- Nainstaluje agenty a webovou aplikaci pro zjišťování a hodnocení fyzických serverů.
- Nainstalujte role Windows, včetně aktivační služby Windows, služby IIS a prostředí PowerShell ISE.
- Stáhněte a nainstalujte zapisovatelný modul IIS. [Přečtěte si další informace](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klíč registru (HKLM) o trvalé podrobnosti nastavení pro Azure Migrate.
- Vytvoří následující soubory pod cestou:
    - **Konfigurační soubory**:%ProgramData%\Microsoft Azure\Config
    - **Soubory protokolu**:%ProgramData%\Microsoft Azure\Logs

Spusťte skript následujícím způsobem:

1. Extrahujte soubor zip do složky na serveru, který bude hostitelem zařízení.  Ujistěte se, že nespouštíte skript na počítači v existujícím zařízení Azure Migrate.
2. Na výše uvedeném serveru s oprávněním správce (zvýšené) spusťte PowerShell.
3. Změňte adresář PowerShellu na složku, do které byl obsah extrahován ze staženého souboru ZIP.
4. Spusťte skript s názvem **AzureMigrateInstaller. ps1** spuštěním následujícího příkazu:

    - Pro veřejný cloud:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - Pro Azure Government:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    Skript spustí webovou aplikaci zařízení po úspěšném dokončení.

Pokud přecházíte mezi všemi problémy, získáte přístup k protokolům skriptu na adrese C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log pro řešení potíží.



### <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

Ujistěte se, že se virtuální počítač zařízení může připojit k adresám URL Azure pro cloudy [veřejné](migrate-appliance.md#public-cloud-urls) a [státní správy](migrate-appliance.md#government-cloud-urls) .

## <a name="configure-the-appliance"></a>Konfigurace zařízení

Nastavte zařízení poprvé.

1. Otevřete prohlížeč na jakémkoli počítači, který se může připojit k VIRTUÁLNÍmu počítači, a otevřete adresu URL webové aplikace zařízení: ***název zařízení https://nebo IP adresa*: 44368**.

   Alternativně můžete aplikaci otevřít z plochy kliknutím na zástupce aplikace.
2. Ve webové aplikaci > **nastavení požadavků**postupujte takto:
    - **Licence**: přijměte licenční podmínky a přečtěte si informace třetích stran.
    - **Připojení**: aplikace kontroluje, jestli má virtuální počítač přístup k Internetu. Pokud virtuální počítač používá proxy server:
        - Klikněte na **nastavení proxy serveru**a zadejte adresu proxy serveru a port naslouchání ve formuláři http://ProxyIPAddress nebo http://ProxyFQDN .
        - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
        - Podporuje se jen proxy protokolu HTTP.
    - **Časová synchronizace**: čas je ověřený. Čas v zařízení by měl být synchronizovaný s internetovým časem, aby zjišťování virtuálních počítačů fungovalo správně.
    - **Instalovat aktualizace**: posouzení Azure Migrate serveru kontroluje, jestli má zařízení nainstalované nejnovější aktualizace.

### <a name="register-the-appliance-with-azure-migrate"></a>Zaregistrovat zařízení ve Azure Migrate

1. Klikněte na **Přihlásit se**. Pokud se nezobrazí, ujistěte se, že jste v prohlížeči zakázali blokování automaticky otevíraných oken.
2. Na nové kartě se přihlaste pomocí svých přihlašovacích údajů Azure.
    - Přihlaste se pomocí svého uživatelského jména a hesla.
    - Přihlášení pomocí PIN kódu se nepodporuje.
3. Po úspěšném přihlášení se vraťte k webové aplikaci.
4. Vyberte předplatné, ve kterém byl vytvořen Azure Migrate projekt. Pak vyberte projekt.
5. Zadejte název zařízení. Název by měl být alfanumerický a nesmí obsahovat více než 14 znaků.
6. Klikněte na **Zaregistrovat**.


## <a name="start-continuous-discovery"></a>Spustit průběžné zjišťování

Připojte se ze zařízení k fyzickým serverům a spusťte zjišťování.

1. Klikněte na **Přidat přihlašovací údaje** a zadejte přihlašovací údaje účtu, které zařízení použije k zjišťování serverů.  
2. Zadejte **operační systém**, popisný název přihlašovacích údajů a uživatelské jméno a heslo. Pak klikněte na **Přidat**.
Každé pro servery se systémem Windows a Linux můžete přidat jednu sadu přihlašovacích údajů.
4. Klikněte na **Přidat server**a zadejte podrobnosti o serveru – plně kvalifikovaný název domény/IP adresa a popisný název přihlašovacích údajů (jedna položka na řádek) pro připojení k serveru.
3. Klikněte na **Validate** (Ověřit). Po ověření se zobrazí seznam serverů, které se dají zjistit.
    - Pokud se ověření serveru nepovede, zkontrolujte chybu přesunutím ukazatele myši na ikonu ve sloupci **stav** . Opravte problémy a znovu ověřte.
    - Pokud chcete odebrat server, vyberte > **Odstranit**.
4. Po ověření klikněte na **Uložit a spusťte zjišťování a** spusťte proces zjišťování.

Spustí se zjišťování. Zobrazení metadat zjištěných virtuálních počítačů v Azure Portal trvá přibližně 15 minut.

## <a name="verify-servers-in-the-portal"></a>Ověřit servery na portálu

Po dokončení zjišťování můžete ověřit, že se servery zobrazují na portálu.

1. Otevřete řídicí panel Azure Migrate.
2. V **Azure Migrate-servery**  >  **Azure Migrate: na stránce posouzení serveru** klikněte na ikonu, která zobrazuje počet **zjištěných serverů**.


## <a name="next-steps"></a>Další kroky

Vyzkoušení [fyzických serverů](tutorial-assess-physical.md) s vyhodnocením Azure Migrate serveru.
