---
title: Nastavení zařízení Azure Migrate pro VMware
description: Naučte se, jak nastavit zařízení Azure Migrate pro vyhodnocení a migraci virtuálních počítačů VMware.
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 9a4e652180b236262ea57ae49d35410ebfbdc927
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448649"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Nastavení zařízení pro virtuální počítače VMware

Podle tohoto článku nastavte zařízení Azure Migrate pro posouzení pomocí nástroje [Azure Migrate: Nástroj pro vyhodnocení serveru](migrate-services-overview.md#azure-migrate-server-assessment-tool) a migraci bez agenta pomocí nástroje [Azure Migrate: Server pro migraci](migrate-services-overview.md#azure-migrate-server-migration-tool) .

[Zařízení Azure Migrate](migrate-appliance.md) je odlehčené zařízení, které používá Azure Migrate: Server Assessment and Server Migration pro zjišťování místních virtuálních počítačů VMware, posílání METADAT virtuálního počítače/dat výkonu do Azure a replikace virtuálních počítačů VMware během migrace bez agenta.

Zařízení můžete nasadit pomocí několika metod:

- Nastavte na virtuálním počítači VMware pomocí stažené šablony vajíček. Tato metoda je popsaná v tomto článku.
- Nastavte na VIRTUÁLNÍm počítači VMware nebo fyzickém počítači pomocí skriptu instalačního programu PowerShell. [Tato metoda](deploy-appliance-script.md) by se měla použít, pokud nemůžete nastavit virtuální počítač pomocí šablony pro vajíčka nebo pokud jste v rámci služby Azure pro státní správu.

Po vytvoření zařízení zkontrolujete, že se může připojit k Azure Migrate: posouzení serveru, nakonfigurovat ho poprvé a zaregistrujte ho v projektu Azure Migrate.


## <a name="appliance-deployment-ova"></a>Nasazení zařízení (VAJÍČKa)

K nastavení zařízení pomocí šablony vajíček:
- Zadejte název zařízení a vygenerujte Azure Migrate klíč projektu na portálu.
- Stáhněte soubor šablony vajíček a naimportujte ho do vCenter Server.
- Vytvořte zařízení a ověřte, že se může připojit k Azure Migrate posouzení serveru.
- Nakonfigurujte zařízení poprvé a zaregistrujte ho pomocí Azure Migrate projektu pomocí klíče Azure Migrate projektu.

### <a name="generate-the-azure-migrate-project-key"></a>Vygenerovat klíč projektu Azure Migrate

1. V části **Cíle migrace** > **Servery** > **Azure Migrate: Hodnocení serverů** vyberte **Zjistit**.
2. V možnosti **zjišťovat počítače**  >  **jsou vaše počítače virtualizované?** vyberte **Ano, pomocí VMware vSphere hypervisor**.
3. V **1: vygenerujte Azure Migrate klíč projektu**, zadejte název Azure Migrate zařízení, které nastavíte pro zjišťování virtuálních počítačů VMware. název by měl být alfanumerický a musí obsahovat maximálně 14 znaků.
1. Kliknutím na **vygenerovat klíč** spustíte vytváření požadovaných prostředků Azure. Během vytváření prostředků prosím Nezavírejte stránku zjišťovacích počítačů.
1. Po úspěšném vytvoření prostředků Azure se vygeneruje **klíč projektu Azure Migrate** .
1. Zkopírujte klíč, protože ho budete potřebovat k dokončení registrace zařízení během jeho konfigurace.

### <a name="download-the-ova-template"></a>Stažení šablony pro VAJÍČKy
V **2: Stáhněte zařízení Azure Migrate**vyberte. Soubor vajíček a klikněte na **Stáhnout**. 


   ![Výběry pro zjišťování počítačů](./media/tutorial-assess-vmware/servers-discover.png)


   ![Výběry pro vygenerování klíče](./media/tutorial-assess-vmware/generate-key-vmware.png)

### <a name="verify-security"></a>Ověřit zabezpečení

Před nasazením ověřte, zda je soubor sady vajíček zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz, který vygeneruje hodnotu hash pro VAJÍČKu:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklady použití: ```C:\>C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```
3. Pro nejnovější verzi zařízení by se vygenerovaná hodnota hash měla shodovat s tímto [nastavením](./tutorial-assess-vmware.md#verify-security).



## <a name="create-the-appliance-vm"></a>Vytvoření virtuálního počítače zařízení

Naimportujte stažený soubor a vytvořte virtuální počítač.

1. V klientské konzole vSphere klikněte na **soubor**  >  **nasadit šablonu OVF**.
![Příkaz nabídky pro nasazení šablony OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. V Průvodci nasazením šablony OVF > **zdroj**zadejte umístění souboru vajíček.
3. Do pole **název** a **umístění**zadejte popisný název virtuálního počítače. Vyberte objekt inventáře, do kterého bude virtuální počítač hostovat.
5. V části **hostitel nebo cluster**zadejte hostitele nebo cluster, na kterém se virtuální počítač spustí.
6. V části **Storage (úložiště**) zadejte cíl úložiště pro virtuální počítač.
7. V části **Disk Format** (Formát disku) zadejte typ a velikost disku.
8. V části **mapování sítě**určete síť, ke které se bude virtuální počítač připojovat. Síť potřebuje připojení k Internetu, aby odesílala metadata Azure Migrate posouzení serveru.
9. Zkontrolujte a ověřte všechna nastavení a pak klikněte na **Finish** (Dokončit).


## <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

Ujistěte se, že se virtuální počítač zařízení může připojit k adresám URL Azure pro cloudy [veřejné](migrate-appliance.md#public-cloud-urls) a [státní správy](migrate-appliance.md#government-cloud-urls) .


### <a name="configure-the-appliance"></a>Konfigurace zařízení

Nastavte zařízení poprvé.

> [!NOTE]
> Pokud zařízení nastavíte pomocí [skriptu PowerShellu](deploy-appliance-script.md) namísto stažené sady vajíček, první dva kroky v tomto postupu nejsou relevantní.

1. V klientské konzole vSphere klikněte pravým tlačítkem myši na virtuální počítač a vyberte možnost **otevřít konzolu**.
2. Zadejte jazyk, časové pásmo a heslo pro zařízení.
3. Otevřete prohlížeč na jakémkoli počítači, který se může připojit k VIRTUÁLNÍmu počítači, a otevřete adresu URL webové aplikace zařízení: ***název zařízení https://nebo IP adresa*: 44368**.

   Alternativně můžete aplikaci otevřít z plochy zařízení tak, že vyberete zástupce aplikace.
1. Přijměte **licenční podmínky**a přečtěte si informace třetích stran.
1. Ve webové aplikaci > **nastavení požadavků**postupujte takto:
   - **Připojení**: aplikace kontroluje, jestli má virtuální počítač přístup k Internetu. Pokud virtuální počítač používá proxy server:
     - Klikněte na **nastavit proxy server** a zadejte adresu proxy serveru (ve formuláři http://ProxyIPAddress nebo http://ProxyFQDN) portu pro naslouchání.
     - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
     - Podporuje se jen proxy protokolu HTTP.
     - Pokud jste přidali podrobnosti proxy serveru nebo zakážete proxy server nebo ověřování, kliknutím na **Uložit** spusťte kontrolu připojení znovu.
   - **Čas synchronizace**: čas v zařízení by měl být synchronizovaný s internetovým časem, aby zjišťování fungovalo správně.
   - **Nainstalovat aktualizace**: zařízení zajišťuje, že jsou nainstalované nejnovější aktualizace. Po dokončení kontroly můžete kliknout na **Zobrazit služby zařízení** a zobrazit stav a verze komponent spuštěných na zařízení.
   - **Instalace VDDK**: zařízení kontroluje, jestli je nainstalovaná VMware vSphere Virtual disk Development Kit (VDDK). Pokud není nainstalovaný, Stáhněte si z VMware VDDK 6,7 a Extrahujte stažený obsah souboru zip do zadaného umístění na zařízení, jak je uvedeno v **pokynech k instalaci**.

     Migrace Azure Migrate serveru používá VDDK k replikaci počítačů během migrace do Azure. 
1. Pokud chcete, můžete kdykoli **znovu spustit požadované součásti** , abyste zkontrolovali, jestli zařízení splňuje všechny požadavky.

### <a name="register-the-appliance-with-azure-migrate"></a>Zaregistrovat zařízení ve Azure Migrate

1. Vložte **klíč projektu Azure Migrate** zkopírovaný z portálu. Pokud tento klíč nemáte, Projděte si část **vyhodnocení serveru> zjistit> spravovat existující zařízení**, vyberte název zařízení, který jste zadali v době generování klíče, a zkopírujte odpovídající klíč.
1. Klikněte na **Přihlásit se**. Otevře se výzva k přihlášení Azure na nové kartě prohlížeče. Pokud se nezobrazí, ujistěte se, že jste v prohlížeči zakázali blokování automaticky otevíraných oken.
1. Na nové kartě se přihlaste pomocí uživatelského jména a hesla Azure.
   
   Přihlášení pomocí PIN kódu se nepodporuje.
3. Po úspěšném přihlášení se vraťte k webové aplikaci. 
4. Pokud má uživatelský účet Azure použitý k protokolování správná [oprávnění](tutorial-prepare-vmware.md#prepare-azure) k prostředkům Azure vytvořeným během generování klíče, zahájí se registrace zařízení.
1. Po úspěšné registraci zařízení si můžete zobrazit podrobnosti o registraci kliknutím na **Zobrazit podrobnosti**.


## <a name="start-continuous-discovery"></a>Spustit průběžné zjišťování

Aby bylo možné zjistit konfiguraci a údaje o výkonu virtuálních počítačů, musí se zařízení připojit k vCenter Server.

1. V **kroku 1: zadejte vCenter Server přihlašovací údaje**, klikněte na **Přidat přihlašovací údaje** , abyste zadali popisný název pro přihlašovací údaje, přidejte **uživatelské jméno** a **heslo** pro účet vCenter Server, který zařízení použije ke zjištění virtuálních počítačů v instanci služby vCenter Server.
    - V [předchozím kurzu](tutorial-prepare-vmware.md#set-up-permissions-for-assessment)byste měli mít nastavený účet s požadovanými oprávněními.
    - Chcete-li omezit obor zjišťování na konkrétní objekty VMware (vCenter Server datových center, clustery, složku clusterů, hostitele, složku hostitelů nebo jednotlivé virtuální počítače), přečtěte si pokyny v [tomto článku](set-discovery-scope.md) a omezte účet používaný v Azure Migrate.
1. V **kroku 2: zadejte vCenter Server podrobnosti**, klikněte na **Přidat zdroj zjišťování** a vyberte popisný název z rozevíracího seznamu, zadejte **IP adresu nebo plně kvalifikovaný název domény** instance vCenter Server. **Port** můžete ponechat výchozí (443) nebo zadejte vlastní port, na kterém vCenter Server naslouchá, a klikněte na **Uložit**.
1. Po kliknutí na Uložit se zařízení pokusí ověřit připojení k vCenter Server s poskytnutými přihlašovacími údaji a zobrazit **stav ověření** v tabulce proti vCenter Server IP adrese nebo plně kvalifikovanému názvu domény.
1. Můžete znovu **ověřit** připojení k vCenter Server kdykoli před zahájením zjišťování.
1. V **kroku 3: zadání přihlašovacích údajů k virtuálnímu počítači pro zjišťování nainstalovaných aplikací a provádění mapování závislostí bez agenta**, klikněte na **Přidat přihlašovací údaje**a zadejte operační systém, pro který se přihlašovací údaje poskytují, popisný název přihlašovacích údajů a **uživatelské jméno** a **heslo**. Pak klikněte na **Uložit**.

    - Přihlašovací údaje můžete volitelně přidat tady, pokud jste vytvořili účet, který se má používat pro [funkci zjišťování aplikací](how-to-discover-applications.md), nebo [funkci analýzy závislostí bez agenta](how-to-create-group-machine-dependencies-agentless.md).
    - Pokud tyto funkce nechcete používat, můžete tento krok přeskočit kliknutím na posuvník. Záměr můžete kdykoli vrátit později.
    - Zkontrolujte přihlašovací údaje potřebné pro [zjišťování aplikací](migrate-support-matrix-vmware.md#application-discovery-requirements)nebo pro [analýzu závislostí bez agenta](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

5. Kliknutím na **Spustit zjišťování Vyhajte**zjišťování virtuálních počítačů. Po úspěšném zahájení zjišťování můžete v tabulce zjistit stav zjišťování oproti vCenter Server IP adrese nebo plně kvalifikovanému názvu domény.

Zjišťování funguje následujícím způsobem:
- Zobrazení zjištěných metadat virtuálního počítače na portálu trvá přibližně 15 minut.
- Zjišťování nainstalovaných aplikací, rolí a funkcí nějakou dobu trvá. Doba trvání závisí na počtu zjištěných virtuálních počítačů. Pro virtuální počítače 500 trvá na portálu Azure Migrate přibližně jednu hodinu, než se inventář aplikace zobrazí.

## <a name="next-steps"></a>Další kroky

Přečtěte si kurzy pro [vyhodnocení VMware](tutorial-assess-vmware.md) a [migraci bez agentů](tutorial-migrate-vmware.md).
