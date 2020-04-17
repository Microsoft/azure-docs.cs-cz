---
title: Nastavení zařízení Azure Migrate pro technologie Hyper-V
description: Zjistěte, jak nastavit zařízení Migrace Azure pro vyhodnocení a migraci virtuálních počítačů Hyper-V.
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: 77c13a3a8c87d116bd0863324d28669185c53c84
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538286"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Nastavení zařízení pro virtuální aplikace Hyper-V

V tomto článku nastavte zařízení Azure Migrate pro vyhodnocení virtuálních počítačích Hyper-V pomocí nástroje [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool)

[Zařízení Migrace Azure](migrate-appliance.md) je zjednodušené zařízení používané Azure Migrate:Server Assessment/Migration ke zjištění místních virtuálních počítačů Hyper-V a odesílání dat metadat a výkonu virtuálního počítače do Azure.

Zařízení můžete nasadit pomocí několika metod:

- Nastavení na virtuálním počítači Hyper-V pomocí staženého virtuálního pevného disku. Toto je metoda popsaná v tomto článku.
- Nastavení na virtuálním počítači Hyper-V nebo fyzickém počítači pomocí instalačního skriptu prostředí PowerShell. [Tato metoda](deploy-appliance-script.md) by se měla použít, pokud nemůžete nastavit virtuální počítač pomocí virtuálního pevného disku, nebo pokud jste ve službě Azure Government.

Po vytvoření zařízení zkontrolujte, zda se může připojit k Azure Migrate:Server Assessment, poprvé ho nakonfigurovat a zaregistrovat v projektu Azure Migrate.

## <a name="appliance-deployment-vhd"></a>Nasazení zařízení (VHD)

Nastavení zařízení pomocí šablony virtuálního pevného disku:

- Stáhněte si komprimovaný virtuální pevný disk Hyper-V z webu Azure Portal.
- Vytvořte zařízení a zkontrolujte, zda se může připojit k Azure Migrate Server Assessment.
- Nakonfigurujte zařízení poprvé a zaregistrujte ho pomocí projektu Azure Migrate.

## <a name="download-the-vhd"></a>Stáhněte si virtuální pevný disk

Stáhněte si šablonu VHD pro zařízení zip.

1. V **serverech cílů** > **migrace, které** > **Azure migruje: Vyhodnocení serveru**, klepněte na tlačítko **Objevit**.
2. V **počítačích** > Discover Jsou vaše **Yes, with Hyper-V****počítače virtualizované?**
3. Chcete-li stáhnout soubor VHD, klepněte na **tlačítko Stáhnout.**

    ![Stažení Virtuálního softwaru](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Ověření zabezpečení

Před nasazením zkontrolujte, zda je soubor zip zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spuštěním následujícího příkazu vygenerujte hash pro virtuální pevný disk.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklady použití: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.vhd SHA256```
3.  U zařízení verze 2.19.11.12 by se vygenerovaný hash měl shodovat s těmito [nastaveními](https://docs.microsoft.com/azure/migrate/tutorial-assess-hyper-v#verify-security).




## <a name="create-the-appliance-vm"></a>Vytvoření virtuálního virtuálního zařízení

Importujte stažený soubor a vytvořte virtuální ho.

1. Extrahujte soubor VHD zip do složky na hostiteli Hyper-V, který bude hostitelem virtuálního počítače zařízení. Jsou extrahovány tři složky.
2. Spusťte Správce technologie Hyper-V. V **části Akce**klepněte na **položku Importovat virtuální počítač**.

    ![Nasazení virtuálního pevného disku](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. V Průvodci importem virtuálního počítače > **než začnete**, klepněte na tlačítko **Další**.
3. V **části Vyhledat složku**zadejte složku obsahující extrahovaný virtuální pevný disk. Pak klikněte na **Další**.
1. V **povolte virtuální počítač**klepněte na tlačítko **Další**.
2. V **části Zvolit typ importu**klikněte na Kopírovat virtuální počítač **(vytvořit nové jedinečné ID).** Pak klikněte na **Další**.
3. V **části Zvolte cíl**ponechte výchozí nastavení. Klikněte na **Další**.
4. Ve **složkách úložiště**ponechejte výchozí nastavení. Klikněte na **Další**.
5. V **části Zvolit síť**zadejte virtuální přepínač, který bude virtuální počítač používat. Přepínač potřebuje připojení k internetu k odesílání dat do Azure.
6. V **souhrnu**zkontrolujte nastavení. Klikněte na **Dokončit**.
7. Ve Správci technologie Hyper-V > **virtuální počítače**spusťte virtuální počítač.


### <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

Ujistěte se, že virtuální počítač zařízení se může připojit k adresám URL Azure pro [veřejné](migrate-appliance.md#public-cloud-urls) a [vládní](migrate-appliance.md#government-cloud-urls) cloudy.

## <a name="configure-the-appliance"></a>Konfigurace zařízení

Přístroj nastavte poprvé. Pokud nasadíte zařízení pomocí skriptu namísto virtuálního pevného disku, první dva kroky v postupu nejsou použitelné.

1. Ve Správci technologie Hyper-V > **virtuálnípočítače**klikněte pravým tlačítkem myši na virtuální počítač > **Connect**.
2. Zadejte jazyk, časové pásmo a heslo pro zařízení.
3. Otevřete prohlížeč na libovolném počítači, který se může připojit k virtuálnímu počítači, a otevřete adresu URL webové aplikace zařízení: **https:// název zařízení nebo IP*adresu*: 44368**.

   Případně můžete aplikaci otevřít z plochy zařízení kliknutím na zástupce aplikace.
1. Ve webové aplikaci > **Nastavení požadavků**postupujte takto:
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


### <a name="delegate-credentials-for-smb-vhds"></a>Delegovat pověření pro virtuální dispozice SMB

Pokud používáte virtuální disku na seminárních podinám, musíte povolit delegování přihlašovacích údajů ze zařízení do hostitelů Hyper-V. Chcete-li to provést z přístroje:

1. Na virtuálním počítači zařízení spusťte tento příkaz. HyperVHost1/HyperVHost2 jsou příklady názvů hostitelů.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Případně to udělejte v Editoru místních zásad skupiny na zařízení:
    - V části Konfigurace počítače zásad > **místního** **počítače**klepněte na položku**Delegování****systémových** > pověření šablony **pro správu** > .
    - Poklepejte na **povolit delegování nových přihlašovacích údajů**a vyberte **Povoleno**.
    - V **části Možnosti**klepněte na tlačítko **Zobrazit**a přidejte do seznamu každého hostitele Hyper-V, který chcete zjistit, jako předponu **wsman/** .
    - V **delegování pověření**poklepejte na položku **Povolit delegování nových pověření pomocí ověřování serveru pouze s NTLM**. Opět přidejte každý hostitel Hyper-V, který chcete objevit, do seznamu, s **wsman /** jako předponou.

## <a name="start-continuous-discovery"></a>Spuštění průběžného zjišťování

Připojte se ze zařízení k hostitelům nebo clusterům Technologie Hyper-V a spusťte zjišťování virtuálních zařízení.

1. V **části Uživatelské jméno** a **heslo**zadejte přihlašovací údaje účtu, které zařízení použije ke zjišťování virtuálních počítače. Zadejte popisný název pověření a klepněte na **tlačítko Uložit podrobnosti**.
2. Klikněte na **Přidat hostitele**a zadejte podrobnosti o hostiteli/clusteru Hyper-V.
3. Klikněte na **Validate** (Ověřit). Po ověření se zobrazí počet virtuálních počítačů, které lze zjistit na každém hostiteli nebo clusteru.
    - Pokud se ověření hostitele nezdaří, zkontrolujte chybu tak, že najeďte na ikonu ve sloupci **Stav.** Opravte problémy a znovu ověřte.
    - Chcete-li odebrat hostitele nebo clustery, vyberte možnost > **Odstranit**.
    - Konkrétního hostitele nelze z clusteru odebrat. Můžete odebrat pouze celý cluster.
    - Můžete přidat cluster, i když existují problémy s konkrétními hostiteli v clusteru.
4. Po ověření klikněte na **Uložit a spusťte zjišťování** a spusťte proces zjišťování.

Tím se spustí zjišťování. Trvá přibližně 15 minut, než se metadata zjištěných virtuálních počítačů zobrazí na webu Azure Portal.

## <a name="verify-vms-in-the-portal"></a>Kontrola virtuálních počítačů na portálu

Po dokončení zjišťování můžete ověřit, že virtuální chody se zobrazí na portálu.

1. Otevřete řídicí panel Migrace Azure.
2. V **Azure Migrate – servery, které** > **Azure migruje:** Stránka Vyhodnocení serveru, klikněte na ikonu, která zobrazuje počet **zjištěných serverů**.


## <a name="next-steps"></a>Další kroky

Vyzkoušejte [hodnocení Hyper-V](tutorial-assess-hyper-v.md) pomocí Azure Migrate Server Assessment.
