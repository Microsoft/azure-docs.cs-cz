---
title: Vyhledání virtuálních počítačů VMware pomocí posouzení serveru Azure Migrate
description: Naučte se zjišťovat místní virtuální počítače VMware pomocí nástroje Azure Migrate Server Assessment.
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: 55a7f15233d32f5ee60a57ad34e8b0177ca29ff3
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064281"
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


## <a name="prerequisites"></a>Předpoklady

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

V tomto kurzu se nastavuje zařízení na virtuálním počítači VMware.
- Stáhnete šablonu zařízení a naimportujete ji do vCenter Server k vytvoření virtuálního počítače zařízení.
- Po vytvoření zařízení ho nastavíte poprvé a zaregistrujete ho do projektu Azure Migrate.

> [!NOTE]
> Pokud z nějakého důvodu nemůžete zařízení nastavit pomocí šablony, můžete ho nastavit pomocí skriptu PowerShellu. [Přečtěte si další informace](deploy-appliance-script.md#set-up-the-appliance-for-vmware).


### <a name="download-the-ova-template"></a>Stažení šablony pro VAJÍČKy

1. V **Azure Migrate cíle migrace**  >  **Servers**  >  **Azure Migrate: Server Assessment**vyberte **Vyhledat**.
2. V možnosti **zjišťovat počítače**  >  **jsou vaše počítače virtualizované?** vyberte **Ano s hypervisorem VMware vSphere**.
3. Vyberte **Stáhnout** a Stáhněte soubor šablony vajíček.

   ![Výběr pro stažení souboru vajíček](./media/tutorial-discover-vmware/download-ova.png)


### <a name="deploy-the-appliance-vm"></a>Nasazení virtuálního počítače zařízení

Naimportujte stažený soubor a vytvořte virtuální počítač:

1. Přihlaste se ke konzole klienta vSphere pomocí účtu vCenter, který jste vytvořili.
2. Na kartě **Virtual Machines** > nabídce **Akce** vyberte **nasadit šablonu OVF**a otevřete Průvodce nasazením šablony ovf.
3. V části **Vybrat šablonu OVF**vyberte **místní soubor**a vyhledejte šablonu ke stažení. 
   ![příkaz nabídky vSphere pro nasazení šablony OVF](./media/tutorial-discover-vmware/deploy-ovf.png)
3. Zadejte zbývající část průvodce nastavením průvodce, zahrnuje umístění nasazení, hostitele/cluster, na kterém se virtuální počítač spustí, a nastavení úložiště a sítě.
4. V nabídce **připraveno k dokončení**klikněte na **Dokončit**. Systém importuje a nasadí šablonu. 
5. Po nasazení se virtuální počítač zařízení zobrazí na kartě **Virtual Machines** .
6. Vyberte virtuální počítač, > **zapnout**.
7. Ve vzdálené konzole VMware > **licenční podmínky**si přečtěte nebo přijměte licenční podmínky.
8. V části **přizpůsobit nastavení**nastavte heslo pro uživatelský účet.


### <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

Ověřte přístup k virtuálnímu počítači zařízení.

1. Ověřte, že se virtuální počítač může připojit k Azure.
    - Ve veřejném cloudu by počítač zařízení měl být schopný se připojit k těmto [adresám URL](migrate-appliance.md#public-cloud-urls).
    - V cloudu pro státní správu by měl být virtuální počítač schopný připojit se k těmto [adresám URL státní správy](migrate-appliance.md#government-cloud-urls).
2. Ujistěte se, že jsou tyto porty otevřené na počítači zařízení:

    - Pokud chcete povolit připojení ke vzdálené ploše zařízení, povolte příchozí připojení na portu TCP 3389.
    - Povolit příchozí připojení na portu 44368 pro vzdálený přístup k webové aplikaci zařízení pomocí adresy URL: https://<zařízení-IP-nebo-Name>:44368.
    - Povolí odchozí připojení na portu 443 (HTTPS), aby se odesílaly metadata zjišťování a výkonu Azure Migrate.

### <a name="configure-the-appliance"></a>Konfigurace zařízení

Nastavte zařízení poprvé.

1. Přihlaste se k virtuálnímu počítači zařízení. 
    - Webová aplikace zařízení se automaticky otevře v prohlížeči.
    - Alternativně můžete aplikaci otevřít z plochy zařízení pomocí zástupce aplikace.
2. Ve webové aplikaci Azure Migrate zařízení > **nastavit požadavky**, přečtěte si nebo přijměte licenční podmínky a přečtěte si informace třetích stran.
3. Zařízení zkontroluje, jestli má virtuální počítač přístup k Internetu, a jestli je čas na virtuálním počítači synchronizovaný s internetovým časem.
    - Pokud používáte proxy server, klikněte na **nastavit proxy** a zadejte adresu a port proxy serveru (ve formátu http://ProxyIPAddress nebo http://ProxyFQDN) . 
    - Zadejte přihlašovací údaje, pokud proxy server vyžaduje ověření. Podporuje se jen proxy protokolu HTTP.
4. Zařízení nainstaluje nejnovější aktualizace Azure Migrate a zkontroluje, jestli je nainstalovaná sada VDDK (Virtual disk Development Kit) VMWare vSphere.
5. Pokud chcete nainstalovat VDDK 6,7, klikněte na **Stáhnout** a Stáhněte si ho z VMware a extrahujte obsah staženého souboru zip do zadaného umístění na zařízení. Pak klikněte na **ověřit a nainstalovat**.

    ![Stránka předpoklady pro ověření přístupu a nastavení Internetu pro nasazení zařízení](./media/tutorial-discover-vmware/prerequisites.png)
  
3. Po instalaci VDDK zkontrolujte nastavení a klikněte na **pokračovat**.

### <a name="register-the-appliance"></a>Registrace zařízení 

1. V **Azure Migrate registrovat v**vyberte **Přihlásit**. Pokud se nezobrazí, ujistěte se, že jste v prohlížeči zakázali blokování automaticky otevíraných oken.

    ![Kliknutím na přihlašovací jméno spusťte registraci zařízení.](./media/tutorial-discover-vmware/register.png)

1. Na **přihlašovací** stránce se přihlaste pomocí uživatelského jména a hesla Azure. Přihlášení pomocí PIN kódu se nepodporuje.

    ![Tlačítko přihlásit k registraci zařízení](./media/tutorial-discover-vmware/sign-in.png)
1. Po úspěšném přihlášení se vraťte do aplikace.
1. V části **zaregistrovat v Azure Migrate**vyberte předplatné, ve kterém byl vytvořen Azure Migrate projekt, a pak vyberte projekt.
1. Zadejte název zařízení. Název by měl být alfanumerický a nesmí obsahovat více než 14 znaků.
3. Vyberte **Zaregistrovat**. Pak klikněte na **pokračovat**. Zpráva zobrazuje registraci jako úspěšnou.

    ![Vyplňte předplatné, projekt a název zařízení a zaregistrujte zařízení.](./media/tutorial-discover-vmware/success-register.png)

## <a name="start-continuous-discovery"></a>Spustit průběžné zjišťování

Zařízení se musí připojit k vCenter Server pro zjišťování virtuálních počítačů.

### <a name="connect-to-vcenter-server"></a>Připojení k vCenter Server

Zařízení se musí připojit k vCenter Server pro zjišťování virtuálních počítačů.

1. V části **zadat vCenter Server**zadejte název (FQDN) nebo IP adresu vCenter Server.
2. Ponechte výchozí port nebo zadejte vlastní port, na kterém vCenter Server naslouchá.
3. V části **uživatelské jméno** a **heslo**zadejte přihlašovací údaje účtu vCenter Server, které zařízení použije ke zjištění virtuálních počítačů v vCenter Server.
3. Vyberte **ověřit připojení**a ujistěte se, že se zařízení může připojit k vCenter Server. 

    ![Podrobnosti a tlačítko serveru vCenter k ověření připojení k vCenter Server](./media/tutorial-discover-vmware/vcenter.png)

1. V nabídce **zjistit aplikace a závislosti na virtuálních počítačích**klikněte na **Přidat přihlašovací údaje**.
1. Zadejte přihlašovací údaje pro Windows/Linux, které používáte pro zjišťování aplikací a analýzu závislostí.
1. Kliknutím na **Uložit a spustit zjišťování zahajte**proces zjišťování.

    ![Přidejte přihlašovací údaje pro zjišťování aplikací a analýzu závislostí a spusťte průběžné zjišťování. ](./media/tutorial-discover-vmware/start-discovery.png)


Po zahájení zjišťování:

- Zobrazení zjištěných metadat virtuálního počítače na portálu trvá přibližně 15 minut.
- Zjišťování aplikací trvá nějakou dobu. Doba závisí na počtu zjištěných virtuálních počítačů. Pro virtuální počítače 500 trvá na portálu Azure Migrate přibližně jednu hodinu, než se inventář aplikace zobrazí.



## <a name="verify-discovered-vms-in-the-portal"></a>Ověřit zjištěné virtuální počítače na portálu

Po zjištění můžete ověřit, že se virtuální počítače zobrazují v Azure Portal:

1. Otevřete řídicí panel Azure Migrate.
2. V **Azure Migrate-servery**  >  **Azure Migrate: vyhodnocování serveru**vyberte ikonu, která zobrazuje počet **zjištěných serverů**.

## <a name="next-steps"></a>Další kroky

- [Vyhodnoťte virtuální počítače VMware](tutorial-assess-vmware.md) pro migraci na virtuální počítače Azure.
- [Zkontrolujte data](migrate-appliance.md#collected-data---vmware) , která zařízení shromažďuje během zjišťování.
