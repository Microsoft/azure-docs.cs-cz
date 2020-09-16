---
title: Vyhledání virtuálních počítačů VMware pomocí posouzení serveru Azure Migrate
description: Naučte se zjišťovat místní virtuální počítače VMware pomocí nástroje Azure Migrate Server Assessment.
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: cbe1561f58af8f65285ffb005b0232bff8225d3b
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604049"
---
# <a name="tutorial-discover-vmware-vms-with-server-assessment"></a>Kurz: Vyhledání virtuálních počítačů VMware pomocí posouzení serveru

Jako součást cesty migrace do Azure zjistíte místní inventář a zatížení. 

V tomto kurzu se dozvíte, jak zjistit místní virtuální počítače VMware pomocí nástroje Azure Migrate: Server Assessment Tool pomocí jednoduchého zařízení Azure Migrate. Zařízení nasadíte jako virtuální počítač VMware, abyste průběžně zjistili metadata o VIRTUÁLNÍm počítači a výkonu, aplikace běžící na virtuálních počítačích a závislosti virtuálních počítačů.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte účet Azure.
> * Připravte prostředí VMware pro zjišťování.
> * Jak vytvořit projekt Azure Migrate.
> * Nastavte zařízení Azure Migrate.
> * Spusťte nepřetržité zjišťování.

> [!NOTE]
> Kurzy ukazují nejrychlejší cestu k vyzkoušení scénáře a používají výchozí možnosti, pokud je to možné.  

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, Projděte si tyto požadavky.


**Požadavek** | **Podrobnosti**
--- | ---
**Hostitel vCenter Server/ESXi** | Potřebujete vCenter Server, na kterém běží verze 5,5, 6,0, 6,5 nebo 6,7.<br/><br/> Virtuální počítače musí být hostované na hostiteli ESXi, na kterém běží verze 5,5 nebo novější.<br/><br/> Na vCenter Server povolte příchozí připojení na portu TCP 443, aby zařízení mohlo shromažďovat data posouzení.<br/><br/> Ve výchozím nastavení se zařízení připojuje ke vCenter na portu 443. Pokud Server vCenter naslouchá na jiném portu, můžete port změnit při připojení zařízení k serveru a spustit zjišťování.<br/><br/> Na serveru EXSi, který je hostitelem virtuálních počítačů, se ujistěte, že je pro zjišťování aplikací povolený příchozí přístup na portu TCP 443.
**Náplně** | vCenter Server potřebuje prostředky pro přidělení virtuálního počítače pro Azure Migrate zařízení:<br/><br/> – Windows Server 2016<br/><br/> -32 GB paměti RAM, 8 vCPU a asi 80 GB diskového úložiště.<br/><br/> – Externí virtuální přepínač a přístup k Internetu na virtuálním počítači přímo nebo prostřednictvím proxy serveru.
**Virtuální počítače** | Pro použití tohoto kurzu musí na virtuálních počítačích s Windows běžet Windows Server 2016, 2012 R2, 2012 nebo 2008 R2.<br/><br/> Virtuální počítače se systémem Linux musí být spuštěny Red Hat Enterprise Linux 7/6/5, Ubuntu Linux 14.04/16.04, Debian 7/8, Oracle Linux 6/7 nebo CentOS 5/6/7.<br/><br/> Virtuální počítače potřebují nástroje VMware (verze novější než 10.2.0) nainstalované a spuštěné.<br/><br/> Na virtuálních počítačích s Windows by se mělo nainstalovat Windows PowerShell 2,0 nebo novější.


## <a name="prepare-an-azure-user-account"></a>Příprava uživatelského účtu Azure

Chcete-li vytvořit projekt Azure Migrate a zaregistrovat Azure Migrate zařízení, budete potřebovat účet s tímto:
- Oprávnění přispěvatele nebo vlastníka v předplatném Azure.
- Oprávnění k registraci aplikací Azure Active Directory.

Pokud jste právě vytvořili bezplatný účet Azure, jste vlastníkem svého předplatného. Pokud nejste vlastníkem předplatného, pracujte s vlastníkem a přiřaďte oprávnění následujícím způsobem:

1. V Azure Portal vyhledejte "předplatná" a v části **služby**vyberte **předplatná**.

    ![Vyhledávací pole pro hledání předplatného Azure](./media/tutorial-discover-vmware/search-subscription.png)

2. Na stránce **předplatná** vyberte předplatné, ve kterém chcete vytvořit projekt Azure Migrate. 
3. V předplatném vyberte **řízení přístupu (IAM)**  >  **Kontrola přístupu**.
4. V části **kontrolovat přístup**vyhledejte příslušný uživatelský účet.
5. V nabídce **Přidat přiřazení role**klikněte na **Přidat**.

    ![Vyhledejte uživatelský účet pro kontrolu přístupu a přiřazení role.](./media/tutorial-discover-vmware/azure-account-access.png)

6. V části **Přidat přiřazení role**vyberte roli přispěvatel nebo vlastník a v našem příkladu vyberte účet (azmigrateuser). Potom klikněte na **Uložit**.

    ![Otevře stránku přidat přiřazení role, která účtu přiřadí roli.](./media/tutorial-discover-vmware/assign-role.png)

7. Na portálu vyhledejte uživatele a v části **služby**vyberte **Uživatelé**.
8. V **nastavení uživatele**ověřte, že uživatelé Azure AD můžou registrovat aplikace (ve výchozím nastavení nastavené na **Ano** ).

    ![Ověřte v uživatelských nastaveních, která můžou uživatelé registrovat v aplikacích Active Directory.](./media/tutorial-discover-vmware/register-apps.png)


## <a name="prepare-vmware"></a>Příprava VMware

Na vCenter Server vytvořte účet, který zařízení může použít pro přístup k vCenter Server, a ověřte, že jsou otevřené požadované porty. Potřebujete také účet, který zařízení může použít pro přístup k virtuálním počítačům. 

### <a name="create-an-account-to-access-vcenter"></a>Vytvoření účtu pro přístup k serveru vCenter

V vSphere webovém klientovi nastavte účet následujícím způsobem:

1. Pomocí účtu s oprávněními správce ve webovém klientovi vSphere > vyberte možnost **Správa**.
2. **Přístup**, vyberte možnost **Uživatelé a skupiny jednotného přihlašování**.
3. V **Uživatelé**přidejte nového uživatele.
4. Do pole **Nový uživatel**zadejte podrobnosti o účtu. Pak klikněte na **OK**.
5. V části **globální oprávnění**vyberte uživatelský účet a přiřaďte k účtu roli jen **pro čtení** . Pak klikněte na **OK**.
6. V části **role** > vyberte roli **jen pro čtení** a v části **oprávnění**vyberte **operace hostů**. Tato oprávnění jsou potřebná ke zjišťování aplikací běžících na virtuálních počítačích a k analýze závislostí virtuálních počítačů.
 
    ![Zaškrtnutím políčka povolíte operace hostů u role jen pro čtení.](./media/tutorial-discover-vmware/guest-operations.png)


### <a name="create-an-account-to-access-vms"></a>Vytvoření účtu pro přístup k virtuálním počítačům

Zařízení přistupuje k virtuálním počítačům, aby mohli zjišťovat aplikace a analyzovat závislosti virtuálních počítačů. Zařízení neinstaluje žádné agenty na virtuální počítače.

1. Vytvořte účet místního správce, který zařízení může využít ke zjišťování aplikací a závislostí na virtuálních počítačích s Windows.
2. V případě počítačů se systémem Linux vytvořte uživatelský účet s oprávněními root nebo alternativním způsobem uživatelský účet s těmito oprávněními na/bin/netstat a/bin/LS soubory: CAP_DAC_READ_SEARCH a CAP_SYS_PTRACE.

> [!NOTE]
> Azure Migrate podporuje jedno přihlašovací údaje pro zjišťování aplikací na všech serverech Windows a jedno přihlašovací údaje pro zjišťování aplikací na všech počítačích se systémem Linux.


## <a name="set-up-a-project"></a>Nastavení projektu

Nastavte nový projekt Azure Migrate.

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
2. V části **Služby** vyberte **Azure Migrate**.
3. V **přehledu**vyberte **vytvořit projekt**.
5. V nástroji **vytvořit projekt**vyberte své předplatné Azure a skupinu prostředků. Vytvořte skupinu prostředků, pokud ji nemáte.
6. V části **Project Details (podrobnosti projektu**) zadejte název projektu a zeměpisnou oblast, ve které chcete vytvořit projekt. Projděte si podporované geografické oblasti pro cloudy [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [státní správy](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Pole pro název a oblast projektu](./media/tutorial-discover-vmware/new-project.png)

7. Vyberte **Vytvořit**.
8. Počkejte několik minut, než se projekt Azure Migrate nasadí.

**Azure Migrate: Nástroj pro vyhodnocení serveru** se ve výchozím nastavení přidá do nového projektu.

![Stránka zobrazující Nástroj pro vyhodnocení serveru přidaný ve výchozím nastavení](./media/tutorial-discover-vmware/added-tool.png)


## <a name="set-up-the-appliance"></a>Nastavení zařízení

K nastavení zařízení pomocí šablony vajíček:
- Zadejte název zařízení a vygenerujte Azure Migrate klíč projektu na portálu.
- Stáhněte soubor šablony vajíček a naimportujte ho do vCenter Server.
- Vytvořte zařízení a ověřte, že se může připojit k Azure Migrate posouzení serveru.
- Nakonfigurujte zařízení poprvé a zaregistrujte ho pomocí Azure Migrate projektu pomocí klíče Azure Migrate projektu.

> [!NOTE]
> Pokud z nějakého důvodu nemůžete zařízení nastavit pomocí šablony, můžete ho nastavit pomocí skriptu PowerShellu. [Přečtěte si další informace](deploy-appliance-script.md#set-up-the-appliance-for-vmware).


### <a name="deploy-with-ova"></a>Nasazení pomocí vajíček

K nastavení zařízení pomocí šablony vajíček:
- Zadejte název zařízení a vygenerujte Azure Migrate klíč projektu na portálu.
- Stáhněte soubor šablony vajíček a naimportujte ho do vCenter Server.
- Vytvořte zařízení a ověřte, že se může připojit k Azure Migrate posouzení serveru.
- Nakonfigurujte zařízení poprvé a zaregistrujte ho pomocí Azure Migrate projektu pomocí klíče Azure Migrate projektu.

### <a name="generate-the-azure-migrate-project-key"></a>Vygenerovat klíč projektu Azure Migrate

1. V **Azure Migrate cíle migrace**  >  **Servers**  >  **Azure Migrate: Server Assessment**vyberte **Vyhledat**.
2. V možnosti **zjišťovat počítače**  >  **jsou vaše počítače virtualizované?** vyberte **Ano, pomocí VMware vSphere hypervisor**.
3. V **1: vygenerujte Azure Migrate klíč projektu**, zadejte název Azure Migrate zařízení, které nastavíte pro zjišťování virtuálních počítačů VMware. název by měl být alfanumerický a musí obsahovat maximálně 14 znaků.
1. Kliknutím na **vygenerovat klíč** spustíte vytváření požadovaných prostředků Azure. Během vytváření prostředků prosím Nezavírejte stránku zjišťovacích počítačů.
1. Po úspěšném vytvoření prostředků Azure se vygeneruje **klíč projektu Azure Migrate** .
1. Zkopírujte klíč, protože ho budete potřebovat k dokončení registrace zařízení během jeho konfigurace.

### <a name="download-the-ova-template"></a>Stažení šablony pro VAJÍČKy

V **2: Stáhněte zařízení Azure Migrate**vyberte. Soubor vajíček a klikněte na **Stáhnout**. 


### <a name="verify-security"></a>Ověřit zabezpečení

Před nasazením ověřte, zda je soubor sady vajíček zabezpečený:

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz, který vygeneruje hodnotu hash pro soubor sady vajíček:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Příklady použití: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Ověřte nejnovější verze zařízení a hodnoty hash:

    - Pro veřejný cloud Azure:
    
        **Algoritmus** | **Stáhnout** | **SHA256**
        --- | --- | ---
        VMware (11,6 GB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - Pro Azure Government:
    
        **Algoritmus** | **Stáhnout** | **SHA256**
        --- | --- | ---
        VMware (85 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2140337) | 47179f47eba2842337bbe533c424dd1da56baccdcf68b1d87b71a5a4280108c2




### <a name="create-the-appliance-vm"></a>Vytvoření virtuálního počítače zařízení

Naimportujte stažený soubor a vytvořte virtuální počítač.

1. V klientské konzole vSphere klikněte na **soubor**  >  **nasadit šablonu OVF**.
2. V Průvodci nasazením šablony OVF > **zdroj**zadejte umístění souboru vajíček.
3. Do pole **název** a **umístění**zadejte popisný název virtuálního počítače. Vyberte objekt inventáře, do kterého bude virtuální počítač hostovat.
5. V části **hostitel nebo cluster**zadejte hostitele nebo cluster, na kterém se virtuální počítač spustí.
6. V části **Storage (úložiště**) zadejte cíl úložiště pro virtuální počítač.
7. V části **Disk Format** (Formát disku) zadejte typ a velikost disku.
8. V části **mapování sítě**určete síť, ke které se bude virtuální počítač připojovat. Síť potřebuje připojení k Internetu, aby odesílala metadata Azure Migrate posouzení serveru.
9. Zkontrolujte a ověřte všechna nastavení a pak klikněte na **Finish** (Dokončit).


### <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

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

- [Vyhodnoťte virtuální počítače VMware](tutorial-assess-vmware.md) pro migraci na virtuální počítače Azure.
- [Zkontrolujte data](migrate-appliance.md#collected-data---vmware) , která zařízení shromažďuje během zjišťování.
