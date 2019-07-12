---
title: Nastavení zařízení pro Azure Migrate posouzení/migrace serveru pro virtuální počítače Hyper-V | Dokumentace Microsoftu
description: Popisuje, jak nastavit zařízení pro zjišťování, vyhodnocení a migrace bez agentů virtuálních počítačů Hyper-V pomocí Azure Migrate posouzení/migrace serveru.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: c531fe49ebff6c021547c2d1c2f382bcd6c9caef
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811762"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Nastavení zařízení pro virtuální počítače Hyper-V

Tento článek popisuje, jak nastavit službu Azure Migrate zařízení, pokud posouzení virtuálních počítačů Hyper-V pomocí Azure Migrate Server Assessment tool, nebo migraci virtuálních počítačů VMware do Azure pomocí nástroje Azure Migrate migrace serveru.

Zařízení virtuálního počítače Hyper-V je zjednodušené zařízení pro pomocí Azure Migrate posouzení/migrace serveru postupujte takto:

- Zjištění místních virtuálních počítačů Hyper-V.
- Odeslání dat metadata a výkonu pro zjištěné virtuální počítače do Azure migrovat posouzení/migrace serveru.

[Další informace](migrate-appliance.md) o zařízení Azure Migrate.


## <a name="appliance-deployment-steps"></a>Postup nasazení zařízení

Nastavení zařízení můžete:
- Stáhněte si komprimovaný virtuálního pevného disku Hyper-V na webu Azure Portal.
- Toto zařízení vytvoříte tak a zkontrolujte, že se může připojit k Azure Migrate Server Assessment. 
- Konfigurace zařízení poprvé a zaregistrujte je v projektu Azure Migrate.

## <a name="download-the-vhd"></a>Stažení virtuálního pevného disku

Stáhněte si komprimovaný šablonu virtuálního pevného disku pro zařízení.

1. V **cílů migrace** > **servery** > **Azure Migrate: Server Assessment**, klikněte na tlačítko **Discover**.
2. V **zjistit počítače** > **jsou vaše počítače virtualizované?** , klikněte na tlačítko **Ano, s technologií Hyper-V**.
3. Klikněte na tlačítko **Stáhnout** ke stažení souboru virtuálního pevného disku.

    ![Stažení virtuálního počítače](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Ověření zabezpečení

Zkontrolujte, že soubor s příponou ZIP zabezpečené, před jejím nasazením.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz, kterým vygenerujete hodnotu hash pro virtuální pevný disk
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklady použití: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  Pro verzi zařízení 1.19.05.10 vygenerovaná hodnota hash by měla odpovídat následujícímu nastavení.

  **Algoritmus** | **Hodnota hash**
  --- | ---
  SHA256 | 598d2e286f9c972bb7f7382885e79e768eddedfe8a3d3460d6b8a775af7d7f79


  
## <a name="create-the-appliance-vm"></a>Toto zařízení vytvoříte virtuální počítač

Importujte stažený soubor a vytvoření virtuálního počítače.

1. Extrahujte komprimovaný soubor virtuálního pevného disku do složky na hostiteli Hyper-V, který bude hostitelem zařízení virtuálního počítače. Tři složky jsou extrahovány.
2. Otevřete Správce technologie Hyper-V. V **akce**, klikněte na tlačítko **importovat virtuální počítač**.

    ![Nasazení virtuálního pevného disku](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. V Průvodci importem virtuálního počítače > **před zahájením**, klikněte na tlačítko **Další**.
3. V **vyhledání složky**, zadejte složku, která obsahuje extrahovaný virtuálního pevného disku. Pak klikněte na tlačítko **Další**.
1. V **vybrat virtuální počítač**, klikněte na tlačítko **Další**.
2. V **vybrat typ importu**, klikněte na tlačítko **zkopírovat virtuální počítač (vytvořit nové jedinečné ID)** . Pak klikněte na tlačítko **Další**.
3. V **zvolit cíl**, ponechte výchozí nastavení. Klikněte na **Další**.
4. V **složky úložiště**, ponechte výchozí nastavení. Klikněte na **Další**.
5. V **výběr sítě**, zadejte virtuální přepínač, který bude používat virtuální počítač. Přepínač vyžaduje připojení k Internetu k odesílání dat do Azure.
6. V **Souhrn**, zkontrolujte nastavení. Pak klikněte na tlačítko **Dokončit**.
7. Ve Správci technologie Hyper-V > **virtuálních počítačů**, spusťte virtuální počítač.


### <a name="verify-appliance-access-to-azure"></a>Ověřit přístup zařízení k Azure

Ujistěte se, že zařízení virtuálního počítače může připojit k [adresám URL služby Azure](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

## <a name="configure-the-appliance"></a>Konfigurace zařízení

Nastavení zařízení poprvé.

1. Ve Správci technologie Hyper-V > **virtuálních počítačů**, klikněte pravým tlačítkem na virtuální počítač > **připojit**.
2. Zadejte jazyk, časové pásmo a heslo pro zařízení.
3. Otevřete prohlížeč na jakémkoli počítači můžete připojit k virtuálnímu počítači a otevřít adresu URL webové aplikace pro zařízení: **https://*zařízení název nebo IP adresu*: 44368**.

   Alternativně můžete otevřít aplikaci z plochy zařízení kliknutím na zástupce aplikace.
1. Ve webové aplikaci > **nastavit požadavky**, postupujte takto:
    - **Licence**: Přijměte licenční podmínky a přečtěte si informace třetích stran.
    - **Připojení**: Je aplikace zkontroluje, že virtuální počítač má přístup k Internetu. Pokud virtuální počítač používá proxy server:
        - Klikněte na tlačítko **nastavení proxy serveru**a zadejte adresu proxy serveru a nastavte naslouchající port ve tvaru http://ProxyIPAddress nebo http://ProxyFQDN.
        - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
        - Podporuje se jen proxy protokolu HTTP.
    - **Čas synchronizace**: Čas je ověřený. Čas na zařízení by měl být synchronizovaný s internetovým časem pro zjišťování virtuálních počítačů fungovalo správně.
    - **Instalace aktualizací**: Azure Migrate Server Assessment kontroluje, zda je zařízení nainstalované nejnovější aktualizace.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrace zařízení pomocí služby Azure Migrate

1. Klikněte na tlačítko **přihlášení**. Pokud se nezobrazí, ujistěte se, že jsme deaktivovali blokování automaticky otevíraných oken v prohlížeči.
2. Na nové kartě Přihlaste se pomocí přihlašovacích údajů Azure. 
    - Přihlaste se pomocí uživatelského jména a hesla.
    - Přihlaste se pomocí kódu PIN není podporován.
3. Po úspěšném přihlášení přejděte zpět do webové aplikace.
4. Vyberte předplatné, ve kterém byl vytvořen projekt Azure Migrate. Vyberte projekt.
5. Zadejte název pro zařízení. Název musí být alfanumerické znaky se 14 znaků nebo méně.
6. Klikněte na tlačítko **zaregistrovat**.


### <a name="delegate-credentials-for-smb-vhds"></a>Delegování přihlašovacích údajů pro virtuální pevné disky protokolu SMB

Pokud používáte virtuální pevné disky na SMB, je nutné povolit delegování přihlašovacích údajů ze zařízení na hostitele Hyper-V. Chcete-li to provést ze zařízení:

1. U zařízení, virtuální počítač spusťte tento příkaz. HyperVHost1/HyperVHost2 jsou ukázkové názvy hostitelů.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Můžete také proveďte ve skupině Editoru místních zásad na zařízení:
    - V **zásady místního počítače** > **konfigurace počítače**, klikněte na tlačítko **šablony pro správu** > **systému**  >  **Delegování pověření**.
    - Dvakrát klikněte na panel **povolit delegování nové přihlašovací údaje**a vyberte **povoleno**.
    - V **možnosti**, klikněte na tlačítko **zobrazit**, a přidejte každého hostitele Hyper-V chcete zjišťovat do seznamu, s **wsman /** jako předponu.
    - V **delegování pověření**, dvakrát klikněte na panel **povolit delegování nové přihlašovací údaje s ověřováním NTLM server**. Znovu, přidejte každý hostitel Hyper-V chcete zjišťovat do seznamu, s **wsman /** jako předponu.

## <a name="start-continuous-discovery"></a>Spustit průběžné vyhledávání

Připojení ze zařízení na hostitelích Hyper-V nebo clusterů a spusťte zjišťování virtuálních počítačů.

1. V **uživatelské jméno** a **heslo**, zadejte přihlašovací údaje účtu, které zařízení použije ke zjištění virtuálních počítačů. Zadejte popisný název pro přihlašovací údaje a klikněte na tlačítko **uložit podrobnosti**.
2. Klikněte na tlačítko **přidat hostitele**a zadejte podrobnosti o hostiteli/clusteru Hyper-V.
3. Klikněte na tlačítko **ověření**. Po ověření se zobrazí počet virtuálních počítačů, které můžete zjistit na každém hostiteli/clusteru.
    - Pokud se ověření nezdaří pro hostitele, vyhodnoťte chybu ukázáním myší na ikonu **stav** sloupce. Vyřešte problémy a znovu ověřit.
    - Odebrat hostitele nebo clustery, vyberte > **odstranit**.
    - Pouze určitého hostitele nelze odebrat z clusteru. Můžete pouze odebrat celý cluster.
    - Cluster, můžete přidat i v případě, že dojde k problémům s konkrétní hostitele v clusteru.
4. Po ověření klikněte na tlačítko **uložit a spustit zjišťování** zahájíte proces zjišťování.

Tím se spustí zjišťování. Trvá přibližně 15 minut metadat zjištěných virtuálních počítačů se zobrazí na webu Azure Portal. 

## <a name="verify-vms-in-the-portal"></a>Kontrola virtuálních počítačů na portálu

Po dokončení zjišťování, můžete ověřit, že se virtuální počítače na portálu.

1. Otevřete řídicí panel Azure Migrate.
2. V **Azure Migrate – servery** > **Azure Migrate: Server Assessment** klikněte na ikonu, která se zobrazí počet **zjištěných serverů**. 


## <a name="next-steps"></a>Další kroky

Vyzkoušejte si [posouzení Hyper-V](tutorial-assess-hyper-v.md) s Azure Migrate Server Assessment.