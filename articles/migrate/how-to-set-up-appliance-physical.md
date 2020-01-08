---
title: Nastavení zařízení Azure Migrate pro fyzické servery
description: Přečtěte si, jak nastavit zařízení Azure Migrate pro posouzení fyzického serveru.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 548ebfca9a937e58dfc5c1aff1adf6f6cb2dcc5d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454599"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Nastavení zařízení pro fyzické servery

Tento článek popisuje, jak nastavit zařízení Azure Migrate, pokud vyhodnotili fyzické servery pomocí nástroje Azure Migrate: Server Assessment Tool.

> [!NOTE]
> Pokud jsou zde uvedené funkce, které se ještě nezobrazuje na portálu Azure Migrate, počkejte. Budou zobrazeny v průběhu příštího týdne.

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

1. V ** > ** **cíli migrace** > **Azure Migrate: posouzení serveru**, klikněte na **zjistit**.
2. V > **zjišťovat počítače** **jsou vaše počítače virtualizované?** klikněte na **nevirtualizované/jiné**.
3. Kliknutím na **Stáhnout** Stáhněte soubor zip.

    ![Stáhnout virtuální počítač](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Ověřit zabezpečení

Před nasazením souboru ZIP ověřte, zda je soubor zip zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz, který vygeneruje hodnotu hash pro virtuální pevný disk.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklady použití: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  Pro nejnovější verzi zařízení by se vygenerovaná hodnota hash měla shodovat s tímto nastavením.

  **Algoritmus** | **Hodnota hash**
  --- | ---
  MD5 | 96fd99581072c400aa605ab036a0a7c0
  SHA256 | f5454beef510c0aa38ac1c6be6346207c351d5361afa0c9cea4772d566fcdc36



## <a name="run-the-azure-migrate-installer-script"></a>Spusťte skript instalačního programu Azure Migrate
= Skript instalačního programu provede následující akce:

- Nainstaluje agenty a webovou aplikaci pro zjišťování a hodnocení fyzických serverů.
- Nainstalujte role Windows, včetně aktivační služby Windows, služby IIS a prostředí PowerShell ISE.
- Stáhněte a nainstalujte zapisovatelný modul IIS. [Další informace](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klíč registru (HKLM) o trvalé podrobnosti nastavení pro Azure Migrate.
- Vytvoří následující soubory pod cestou:
    - **Konfigurační soubory**:%ProgramData%\Microsoft Azure\Config
    - **Soubory protokolu**:%ProgramData%\Microsoft Azure\Logs

Spusťte skript následujícím způsobem:

1. Extrahujte soubor zip do složky na serveru, který bude hostitelem zařízení.
2. Na výše uvedeném serveru s oprávněním správce (zvýšené) spusťte PowerShell.
3. Změňte adresář PowerShellu na složku, do které byl obsah extrahován ze staženého souboru ZIP.
4. Spusťte skript spuštěním následujícího příkazu:
    ```
    AzureMigrateInstaller.ps1
    ```
Skript spustí webovou aplikaci zařízení po úspěšném dokončení.



### <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

Ujistěte se, že se virtuální počítač zařízení může připojit k požadovaným [adresám URL Azure](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

## <a name="configure-the-appliance"></a>Konfigurace zařízení

Nastavte zařízení poprvé.

1. Otevřete prohlížeč na jakémkoli počítači, který se může připojit k VIRTUÁLNÍmu počítači, a otevřete adresu URL webové aplikace zařízení: ***název zařízení https://nebo IP adresa*: 44368**.

   Alternativně můžete aplikaci otevřít z plochy kliknutím na zástupce aplikace.
2. Ve webové aplikaci > **nastavení požadavků**postupujte takto:
    - **Licence**: přijměte licenční podmínky a přečtěte si informace třetích stran.
    - **Připojení**: aplikace kontroluje, jestli má virtuální počítač přístup k Internetu. Pokud virtuální počítač používá proxy server:
        - Klikněte na **nastavení proxy serveru**a zadejte adresu proxy serveru a port naslouchání ve formuláři http://ProxyIPAddress nebo http://ProxyFQDN.
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
6. Klikněte na **zaregistrovat**.


## <a name="start-continuous-discovery"></a>Spustit průběžné zjišťování

Připojte se ze zařízení k fyzickým serverům a spusťte zjišťování.

1. Klikněte na **Přidat přihlašovací údaje** a zadejte přihlašovací údaje účtu, které zařízení použije k zjišťování serverů.  
2. Zadejte **operační systém**, popisný název přihlašovacích údajů, **uživatelské jméno** a **heslo** a klikněte na **Přidat**.
Každé pro servery se systémem Windows a Linux můžete přidat jednu sadu přihlašovacích údajů.
4. Klikněte na **Přidat server**a zadejte podrobnosti o serveru – plně kvalifikovaný název domény/IP adresa a popisný název přihlašovacích údajů (jedna položka na řádek) pro připojení k serveru.
3. Klikněte na tlačítko **ověřit**. Po ověření se zobrazí seznam serverů, které se dají zjistit.
    - Pokud se ověření serveru nepovede, zkontrolujte chybu přesunutím ukazatele myši na ikonu ve sloupci **stav** . Opravte problémy a znovu ověřte.
    - Pokud chcete odebrat server, vyberte > **Odstranit**.
4. Po ověření klikněte na **Uložit a spusťte zjišťování a** spusťte proces zjišťování.

Spustí se zjišťování. Zobrazení metadat zjištěných virtuálních počítačů v Azure Portal trvá přibližně 15 minut.

## <a name="verify-servers-in-the-portal"></a>Ověřit servery na portálu

Po dokončení zjišťování můžete ověřit, že se servery zobrazují na portálu.

1. Otevřete řídicí panel Azure Migrate.
2. V **Azure Migrate-servery** > **Azure Migrate: na stránce posouzení serveru** klikněte na ikonu, která zobrazuje počet **zjištěných serverů**.


## <a name="next-steps"></a>Další kroky

Vyzkoušení [fyzických serverů](tutorial-assess-physical.md) s vyhodnocením Azure Migrate serveru.
