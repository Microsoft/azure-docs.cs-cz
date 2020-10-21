---
title: Migrace virtuálních počítačů VMware pomocí migrace serveru na základě agentů Azure Migrate
description: Naučte se spouštět migraci virtuálních počítačů VMware založených na agentech pomocí Azure Migrate.
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: MVC
ms.openlocfilehash: ffdbdba0aeae33b04195c5a6bf6aeaff5658424b
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92314705"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Migrace virtuálních počítačů VMware do Azure (na základě agentů)

V tomto článku se dozvíte, jak migrovat místní virtuální počítače VMware do Azure pomocí nástroje [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) Tool s migrací založenou na agentech.  Virtuální počítače VMware můžete také migrovat pomocí migrace založené na agentech. [Porovnejte](server-migrate-overview.md#compare-migration-methods) metody.


 V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Připravte Azure pro práci s Azure Migrate.
> * Připravte se na migraci založenou na agentech. Nastavte účet VMware, aby Azure Migrate mohl zjišťovat počítače pro migraci. Nastavte účet tak, aby se agent služby mobility mohl nainstalovat na počítače, které chcete migrovat, a připravte počítač, který bude fungovat jako zařízení replikace.
> * Přidejte Azure Migrate: Nástroj pro migraci serveru
> * Nastavte zařízení replikace.
> * Replikace virtuálních počítačů.
> * Spusťte test migrace a ujistěte se, že vše funguje podle očekávání.
> * Spusťte úplnou migraci do Azure.

> [!NOTE]
> Kurzy vám ukážou nejjednodušší cestu nasazení pro scénář, abyste mohli rychle nastavit zkušební verzi. V těchto kurzech se v rámci možností používají jen výchozí možnosti a neuvádějí se všechny varianty nastavení ani všechny cesty. 

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Předpoklady

Než začnete s tímto kurzem, [Přečtěte si](./agent-based-migration-architecture.md) architekturu migrace založenou na agentech VMware.

## <a name="prepare-azure"></a>Příprava Azure

Dokončete úkoly v tabulce a připravte Azure na migraci založenou na agentech.

**Úloha** | **Podrobnosti**
--- | ---
**Vytvoření projektu Azure Migrate** | Váš účet Azure potřebuje k vytvoření projektu oprávnění přispěvatele nebo vlastníka.
**Ověření oprávnění účtu Azure** | Váš účet Azure potřebuje oprávnění k vytvoření virtuálního počítače a zápis na spravovaný disk Azure.
**Nastavení sítě Azure** | Nastavte síť, ke které se virtuální počítače Azure připojí po migraci.

### <a name="assign-permissions-to-create-project"></a>Přiřadit oprávnění k vytvoření projektu
Pokud nemáte projekt Azure Migrate, ověřte oprávnění a vytvořte ho.


1. Na webu Azure Portal otevřete předplatné a vyberte **Řízení přístupu (IAM)** .
2. V části **kontrolovat přístup**Najděte příslušný účet a kliknutím na něj Zobrazte oprávnění.
3. Ověřte, zda máte oprávnění **Přispěvatel** nebo **Owner** .

    - Pokud jste si právě vytvořili bezplatný účet Azure, jste vlastníkem vašeho předplatného.
    - Pokud nejste vlastníkem předplatného, přidělte odpovídající roli ve spolupráci s vlastníkem.
    
### <a name="assign-azure-account-permissions"></a>Přiřazení oprávnění účtu Azure

Přiřaďte k účtu roli Přispěvatel virtuálních počítačů, abyste měli oprávnění k těmto akcím:

- Vytvoření virtuálního počítače ve vybrané skupině prostředků
- Vytvoření virtuálního počítače ve vybrané virtuální síti
- Zapište na spravovaný disk Azure. 


### <a name="set-up-an-azure-network"></a>Nastavení sítě Azure

[Nastavte síť Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Místní počítače se replikují do Azure Managed disks. Když převezmete služby při selhání do Azure pro migraci, virtuální počítače Azure se z těchto spravovaných disků vytvoří a připojí se k síti Azure, kterou jste nastavili.

## <a name="prepare-for-migration"></a>Příprava migrace

Ověřte požadavky a oprávnění podpory a připravte se na nasazení replikačního zařízení. 

### <a name="prepare-an-account-to-discover-vms"></a>Příprava účtu pro zjišťování virtuálních počítačů

Migrace Azure Migrate serveru potřebuje přístup k serverům VMware ke zjištění virtuálních počítačů, které chcete migrovat. Vytvořte účet následujícím způsobem:

1. Pokud chcete použít vyhrazený účet, vytvořte roli na úrovni vCenter. Zadejte název role, například **Azure_Migrate**.
2. Přiřaďte roli oprávnění uvedená v následující tabulce.
3. Vytvořte uživatele na serveru vCenter nebo hostiteli vSphere. Přiřaďte uživateli roli.

#### <a name="vmware-account-permissions"></a>Oprávnění účtu VMware

**Úloha** | **Role/oprávnění** | **Podrobnosti**
--- | --- | ---
**Zjišťování virtuálních počítačů** | Alespoň uživatel jen pro čtení<br/><br/> Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Chcete-li omezit přístup, přiřaďte podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli **bez přístupu** s **podřízeným objektem rozšířit do podřízeného** objektu.
**Replikace** |  Vytvořte roli (Azure_Site_Recovery) s požadovanými oprávněními a pak ji přiřaďte uživateli nebo skupině VMware.<br/><br/> Objekt datového centra –> Rozšířit na podřízený objekt, role=Azure_Site_Recovery<br/><br/> Úložiště dat –> Přidělit prostor, procházet úložiště dat, operace se soubory nízké úrovně, odebrat soubor, aktualizovat soubory virtuálního počítače<br/><br/> Síť –> Přiřazení sítě<br/><br/> Prostředek –> Přiřadit virtuální počítač k fondu prostředků, migrovat vypnutý virtuální počítač, migrovat zapnutý virtuální počítač<br/><br/> Úlohy –> Vytvořit úlohu, aktualizovat úlohu<br/><br/> Virtuální počítač –> Konfigurace<br/><br/> Virtuální počítač –> Interakce –> zodpovědět dotazy, připojení zařízení, konfigurovat disk CD, konfigurovat disketu, vypnout, zapnout, instalace nástrojů VMware<br/><br/> Virtuální počítač –> Inventář –> Vytvořit, zaregistrovat, zrušit registraci<br/><br/> Virtuální počítač –> Zřizování –> Povolit stažení virtuálního počítače, povolit nahrávání souborů virtuálního počítače<br/><br/> Virtuální počítač –> Snímky –> Odebrat snímky | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Chcete-li omezit přístup, přiřaďte k podřízeným objektům (hostitelé vSphere, úložiště dat, VMsa, ND sítě) roli **bez přístupu** s **podřízeným objektem šířit do podřízeného** objektu.

### <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Na počítače, které chcete replikovat, bude nutné nainstalovat službu Mobility.

- Zařízení replikace Azure Migrate může provést nabízenou instalaci této služby, když povolíte replikaci pro určitý počítač, nebo ji můžete nainstalovat ručně nebo pomocí instalačních nástrojů.
- V tomto kurzu provedeme instalaci služby Mobility pomocí nabízené instalace.
- Pro nabízenou instalaci musíte připravit účet, který Azure Migrate migrace serveru použít pro přístup k virtuálnímu počítači. Tento účet se používá jenom pro nabízenou instalaci, pokud nenainstalujete službu mobility ručně.

Připravte účet následujícím způsobem:

1. Připravte účet domény nebo místní účet s oprávněními k instalaci na virtuální počítač.
2. Pokud v případě virtuálních počítačů s Windows nepoužíváte doménový účet, zakažte na místním počítači vzdálené řízení přístupu uživatele tak, že přidáte položku typu DWORD **LocalAccountTokenFilterPolicy**s hodnotou v registru, a to v části **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**
3. Pro virtuální počítače se systémem Linux Připravte kořenový účet na zdrojovém serveru Linux.


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Příprava počítače na zařízení replikace

Zařízení se používá k replikaci počítačů do Azure. Zařízení je jeden, vysoce dostupný, místní virtuální počítač VMware, který je hostitelem těchto součástí:

- **Konfigurační server**: konfigurační server koordinuje komunikaci mezi místními a Azure a spravuje replikaci dat.
- **Procesový Server**: procesový server funguje jako brána replikace. Přijímá data replikace; optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do účtu úložiště mezipaměti v Azure. Procesový Server také nainstaluje agenta služby mobility na virtuální počítače, které chcete replikovat, a provádí automatické zjišťování místních virtuálních počítačů VMware.

Připravte zařízení následujícím způsobem:

- [Zkontrolujte požadavky na zařízení](migrate-replication-appliance.md#appliance-requirements). Obecně platí, že se zařízení replikace nastavuje jako virtuální počítač VMware pomocí staženého souboru sady vajíček. Šablona vytvoří zařízení, které vyhovuje všem požadavkům.
- V zařízení musí být nainstalovaný MySQL. [Zkontrolujte](migrate-replication-appliance.md#mysql-installation) metody instalace.
- Zkontrolujte [adresy URL veřejného cloudu](migrate-replication-appliance.md#url-access)a [Azure Government adresy URL](migrate-replication-appliance.md#azure-government-url-access) , ke kterým musí mít počítač zařízení přístup.
- [Zkontrolujte porty](migrate-replication-appliance.md#port-access) , ke kterým musí počítač zařízení replikace přistupovat.



### <a name="check-vmware-requirements"></a>Kontrola požadavků na VMware

Ujistěte se, že servery a virtuální počítače VMware splňují požadavky na migraci do Azure. 

1. [Ověřte](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) požadavky na servery VMware.
2. [Ověřit](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) Požadavky na virtuální počítač pro migraci.
3. Ověřte nastavení Azure. Místní virtuální počítače, které se replikují do Azure, musí splňovat [požadavky na virtuální počítače Azure](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).
4. Před migrací do Azure jsou na virtuálních počítačích potřeba nějaké změny.
    - Před zahájením migrace je důležité tyto změny provést. Pokud před provedením změny migrujete virtuální počítač, nemusí se virtuální počítač spustit v Azure.
    - Zkontrolujte změny v [systému Windows](prepare-for-migration.md#windows-machines) a [Linux](prepare-for-migration.md#linux-machines) , které je třeba provést.

> [!NOTE]
> Migrace na základě agenta pomocí migrace serveru Azure Migrate je založená na funkcích služby Azure Site Recovery. Některé požadavky mohou odkazovat na dokumentaci Site Recovery.



## <a name="add-the-azure-migrateserver-migration-tool"></a>Přidejte Azure Migrate: Nástroj pro migraci serveru

Pokud ještě nemáte projekt Azure Migrate, [nastavte](how-to-add-tool-first-time.md) ho hned a přidejte Nástroj pro migraci serveru.

Pokud máte projekt, přidejte nástroj následujícím způsobem:

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
2. V části **Služby** vyberte **Azure Migrate**.

    ![Nastavit Azure Migrate](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. V části **Přehled** klikněte na **Posoudit a migrovat servery**.
4. V části **zjišťování, vyhodnocení a migrace serverů**klikněte na možnost **zhodnotit a migrovat servery**.

    ![Zjištění a posouzení serverů](./media/tutorial-migrate-vmware-agent/assess-migrate.png)

1. V části **Zjistit, posoudit a migrovat servery** klikněte na **Přidat nástroje**.
2. V části **Projekt migrace** vyberte své předplatné Azure a vytvořte skupinu prostředků, pokud ji ještě nemáte.
3. V části **Podrobnosti o projektu** zadejte název projektu a zeměpisnou oblast, ve které chcete projekt vytvořit, a klikněte na **Další**. Projděte si podporované geografické oblasti pro cloudy [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [státní správy](migrate-support-matrix.md#supported-geographies-azure-government).

    ![Vytvoření projektu Azure Migrate](./media/tutorial-migrate-vmware-agent/migrate-project.png)


4. V části **Vybrat nástroj pro posouzení** vyberte **V tuto chvíli přeskočit přidání nástroje pro posouzení** > **Další**.
5. V části **Vybrat nástroj pro migraci** vyberte **Azure Migrate: Migrace serverů** > **Další**.
6. V části **Zkontrolovat a přidat nástroje** zkontrolujte nastavení a klikněte na **Přidat nástroje**.
7. Po přidání je nástroj zobrazen v nástroji Azure Migrate Project > servery pro **Servers**  >  **migraci**.

## <a name="set-up-the-replication-appliance"></a>Nastavení zařízení replikace

Tento postup popisuje, jak nastavit zařízení s staženou šablonou aplikace Open Virtualization (vajíček). Pokud tuto metodu nemůžete použít, můžete zařízení nastavit [pomocí skriptu](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance). 

### <a name="download-the-replication-appliance-template"></a>Stáhnout šablonu zařízení replikace

Stáhněte šablonu následujícím způsobem:

1. V Azure Migrate projektu klikněte v části **cíle migrace**na **servery** .
2. V **Azure Migrate-servery**  >  **Azure Migrate: Migrace serveru**klikněte na **Vyhledat**.

    ![Vyhledání virtuálních počítačů](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. V nabídce **zjistit**počítače  >  **jsou vaše počítače virtualizované?** klikněte na **Ano, pomocí VMware vSphere hypervisoru**.
4. V tématu **jak chcete migrovat?** vyberte možnost **použít replikaci založenou na agentech**.
5. V části **cílová oblast**vyberte oblast Azure, do které chcete migrovat počítače.
6. Vyberte **potvrdit, že cílová oblast pro migraci je název regionu**.
7. Klikněte na **vytvořit prostředky**. Tím dojde k vytvoření trezoru Azure Site Recovery na pozadí. Po kliknutí na toto tlačítko nemůžete změnit cílovou oblast tohoto projektu a všechny následné migrace jsou v této oblasti.

    ![Vytvoření trezoru služby Recovery Services](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. V nástroji chcete **nainstalovat nové replikační zařízení?** vyberte **nainstalovat zařízení replikace**.
9. Klikněte na tlačítko **Stáhnout**. Tím se stáhne šablona OVF.
    ![Stáhnout VAJÍČKy](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Poznamenejte si název skupiny prostředků a trezoru Recovery Services. Budete je potřebovat během nasazování zařízení.


### <a name="import-the-template-in-vmware"></a>Import šablony do VMware

Po stažení šablony OVF je můžete importovat do VMware a vytvořit tak aplikaci pro replikaci na virtuálním počítači VMware s Windows serverem 2016.

1. Přihlaste se k serveru VMware vCenter nebo vSphere hostitele ESXi pomocí klienta VMware vSphere.
2. V nabídce **soubor** vyberte **nasadit šablonu OVF** a spusťte **Průvodce nasazením šablony OVF**. 
3. V části **Vybrat zdroj**zadejte umístění staženého ovf.
4. V **podrobnostech revize**vyberte **Další**.
5. V části **Vybrat název a složku** a **Vyberte Konfigurace**přijměte výchozí nastavení.
6. V **Vyberte úložiště**  >  **Vybrat formát virtuálního disku**pro nejlepší výkon vyberte **silné zřízení Eager s nulovou hodnotou**.
7. Na zbývajících stránkách průvodce přijměte výchozí nastavení.
8. V části **připraveno k dokončení**Chcete-li nastavit virtuální počítač s výchozím nastavením, vyberte možnost **zapnout po**  >  **dokončení**nasazení.

   > [!TIP]
   > Pokud chcete přidat další síťové rozhraní, zrušte **po**  >  **dokončení**nasazení volbu Zapnout. Ve výchozím nastavení obsahuje šablona jeden síťový adaptér. Po nasazení můžete přidat další síťové adaptéry.

### <a name="start-appliance-setup"></a>Spustit instalaci zařízení

1. V konzole klienta VMware vSphere zapněte virtuální počítač. Virtuální počítač se spustí do instalačního prostředí Windows Serveru 2016.
2. Přijměte licenční smlouvu a zadejte heslo správce.
3. Až se instalace dokončí, přihlaste se k virtuálnímu počítači jako správce pomocí hesla správce. Při prvním přihlášení se nástroj pro nastavení replikačního zařízení (Azure Site Recovery konfigurační nástroj) spustí během několika sekund.
5. Zadejte název, který se použije k registraci zařízení v rámci migrace serveru. Potom klikněte na **Další**.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání spojení vyberte **Přihlásit se**, abyste se mohli přihlásit ke svému předplatnému Azure.
7. Počkejte, až nástroj dokončí registraci aplikace Azure AD k identifikaci zařízení. Zařízení se restartuje.
1. Znovu se přihlaste k počítači. Za chvilku se spustí automaticky průvodce správou konfiguračního serveru.

### <a name="register-the-replication-appliance"></a>Registrace zařízení replikace

Dokončete nastavení a zaregistrujte zařízení replikace.

1. V nastavení zařízení vyberte **nastavení připojení**.
2. Vyberte síťovou kartu (ve výchozím nastavení existuje jenom jedna síťová karta), kterou zařízení pro replikaci používá pro zjišťování virtuálních počítačů, a proveďte nabízenou instalaci služby mobility na zdrojové počítače.
3. Vyberte síťovou kartu, kterou zařízení replikace používá pro připojení k Azure. Pak vyberte **Uložit**. Po nakonfigurování tohoto nastavení už toto nastavení nemůžete změnit.
4. Pokud je zařízení umístěné za proxy server, je nutné zadat nastavení proxy serveru.
    - Zadejte název proxy jako **http://ip-address** nebo **http://FQDN** . Proxy servery HTTPS nejsou podporované.
5. Po zobrazení výzvy k zadání předplatného, skupin prostředků a podrobností o trezoru přidejte podrobnosti, které jste si poznamenali při stažení šablony zařízení.
6. V části **Nainstalovat software třetí strany** přijměte licenční smlouvu. Vyberte **Stáhnout a nainstalovat** a nainstalujte MySQL Server.
7. Vyberte **Nainstalovat VMware PowerCLI**. Než to uděláte, zkontrolujte, jestli jsou zavřená všechna okna prohlížeče. Potom vyberte **Pokračovat**.
8. Než budete pokračovat, v části **Ověřit konfiguraci zařízení** se ověří požadavky.
9. V části **Konfigurovat vCenter Server nebo server vSphere ESXi** zadejte plně kvalifikovaný název domény nebo IP adresu vCenter Serveru nebo hostitele vSphere, na kterém jsou umístěné virtuální počítače, které chcete replikovat. Zadejte port, na kterém server naslouchá. Zadejte popisný název, který se použije pro server VMware v trezoru.
10. Zadejte přihlašovací údaje pro účet, který jste [vytvořili](#prepare-an-account-to-discover-vms) pro zjišťování VMware. Vyberte **Přidat**  >  **pokračovat**.
11. V části **Konfigurovat přihlašovací údaje virtuálního počítače**zadejte přihlašovací údaje, které jste [vytvořili](#prepare-an-account-for-mobility-service-installation) pro nabízenou instalaci služby mobility, když povolíte replikaci pro virtuální počítače.  
    - Když chcete replikovat počítače s Windows, je nutné, aby na nich měl tento účet oprávnění místního správce.
    - U počítačů s Linuxem zadejte údaje superuživatele.
12. Vyberte **Dokončit konfiguraci** a dokončete registraci.


Po zaregistrování zařízení replikace se Azure Migrate posouzení serveru připojí k serverům VMware pomocí zadaného nastavení a vyhledá virtuální počítače. Zjištěné virtuální počítače můžete zobrazit v části **Správa**  >  **zjištěných položek**na **druhé** kartě.



## <a name="replicate-vms"></a>Replikace virtuálních počítačů

Vyberte virtuální počítače pro migraci.

> [!NOTE]
> Na portálu můžete pro replikaci vybrat až 10 počítačů najednou. Pokud potřebujete replikovat více, seskupte je v dávkách po 10.

1. V Azure Migrate Project > **servery** **Azure Migrate: Migrace serveru**klikněte na **replikovat**.

    ![Snímek obrazovky serverů v Azure Migrate. Tlačítko replika je vybrané v Azure Migrate: Migrace serveru v části nástroje pro migraci.](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. V části **Replikovat** > **Nastavení zdroje** > **Máte počítače ve virtuální podobě?** vyberte **Ano, s VMware vSphere**.
3. V části **místní zařízení**vyberte název zařízení Azure Migrate, které jste nastavili.
4. V systému **vCenter Server**zadejte název vCenter serveru, který spravuje virtuální počítače, nebo vSphere Server, na kterém jsou virtuální počítače hostované.
5. V části **procesový Server**vyberte název zařízení replikace.
6. Do pole **přihlašovací údaje hosta**zadejte účet správce virtuálních počítačů, který se bude používat pro nabízenou instalaci služby mobility. Pak klikněte na **Další: virtuální počítače**.

    ![Snímek obrazovky karty nastavení zdroje na obrazovce replikace Pole s přihlašovacími údaji hosta je zvýrazněné a hodnota je nastavená na VM-admin-Account.](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. V **Virtual Machines**vyberte počítače, které chcete replikovat.

    - Pokud jste pro virtuální počítače spustili posouzení, můžete použít doporučenou velikost a typ disku (Premium nebo Standard) z výsledků posouzení. Pokud to chcete provést, v části **Importovat nastavení migrace z posouzení Azure Migrate?** vyberte možnost **Ano**.
    - Pokud jste neprovedli posouzení nebo pokud nechcete použít nastavení posouzení, vyberte možnost **Ne**.
    - Pokud jste se rozhodli použít posouzení, vyberte skupinu virtuálních počítačů a název posouzení.
8. V **Možnosti dostupnost**vyberte:
    -  Zóna dostupnosti pro připnutí migrovaného počítače ke konkrétní zóně dostupnosti v oblasti Tuto možnost použijte k distribuci serverů, které tvoří aplikační vrstvu s více uzly napříč Zóny dostupnosti. Pokud vyberete tuto možnost, budete muset zadat zónu dostupnosti, která se má použít pro každý z vybraných počítačů na kartě Compute. Tato možnost je dostupná jenom v případě, že cílová oblast vybraná pro migraci podporuje Zóny dostupnosti
    -  Skupina dostupnosti umístí migrovaný počítač do skupiny dostupnosti. Vybraná cílová skupina prostředků musí mít jednu nebo víc skupin dostupnosti, aby bylo možné tuto možnost použít.
    - Není nutná žádná možnost redundance infrastruktury, pokud nepotřebujete žádnou z těchto konfigurací dostupnosti pro migrované počítače.
9. Ověřte každý virtuální počítač, který chcete migrovat. Pak klikněte na **Další: nastavení cíle**.
10. V části **Nastavení cíle** vyberte předplatné a cílovou oblast migrace a zadejte skupinu prostředků, ve které se po migraci budou nacházet virtuální počítače Azure.
11. V části **Virtuální síť** vyberte virtuální síť a podsíť Azure, ke kterým se po migraci připojí virtuální počítače Azure.
12. V **Možnosti dostupnost**vyberte:
    -  Zóna dostupnosti pro připnutí migrovaného počítače ke konkrétní zóně dostupnosti v oblasti Tuto možnost použijte k distribuci serverů, které tvoří aplikační vrstvu s více uzly napříč Zóny dostupnosti. Pokud vyberete tuto možnost, budete muset zadat zónu dostupnosti, která se má použít pro každý z vybraných počítačů na kartě Compute. Tato možnost je dostupná jenom v případě, že cílová oblast vybraná pro migraci podporuje Zóny dostupnosti
    -  Skupina dostupnosti umístí migrovaný počítač do skupiny dostupnosti. Vybraná cílová skupina prostředků musí mít jednu nebo víc skupin dostupnosti, aby bylo možné tuto možnost použít.
    - Není nutná žádná možnost redundance infrastruktury, pokud nepotřebujete žádnou z těchto konfigurací dostupnosti pro migrované počítače.
    
13. V části **Zvýhodněné hybridní využití Azure**:

    - Vyberte **Ne**, pokud nechcete využít Zvýhodněné hybridní využití Azure. Potom klikněte na **Další**.
    - Vyberte **Ano**, pokud máte počítače s Windows Serverem s aktivním Software Assurance nebo předplatným Windows Serveru a u migrovaných počítačů chcete využít tuto výhodu. Potom klikněte na **Další**.

14. V části **COMPUTE**Zkontrolujte název virtuálního počítače, velikost, typ disku s operačním systémem a konfiguraci dostupnosti (Pokud jste zvolili v předchozím kroku). Virtuální počítače musí splňovat [požadavky Azure](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

   - **Velikost virtuálního počítače**: Pokud používáte doporučení pro vyhodnocení, zobrazuje se v rozevíracím seznamu velikost virtuálního počítače doporučená velikost. Jinak Azure Migrate vybere velikost na základě nejbližší shody v předplatném Azure. Případně můžete velikost vybrat ručně v části **Velikost virtuálního počítače Azure**. 
    - **Disk s operačním systémem**: zadejte operační systém (spouštěcí) disk pro virtuální počítač. Disk s operačním systémem je disk, který obsahuje spouštěcí zavaděč a instalační program operačního systému. 
    - **Zóna dostupnosti**: Zadejte zónu dostupnosti, která se má použít.
    - **Skupina dostupnosti**: Určete skupinu dostupnosti, která se má použít.

15. V části **disky**určete, jestli se mají disky virtuálních počítačů replikovat do Azure, a v Azure vyberte typ disku (standardní disk SSD/HDD nebo Premium Managed Disks). Potom klikněte na **Další**.
    - Disky můžete z replikace vyloučit.
    - Pokud disky vyloučíte, po migraci nebudou na virtuálním počítači Azure. 

16. V části **Kontrola a zahájení replikace** zkontrolujte nastavení a kliknutím na **Replikovat** spusťte počáteční replikaci serverů.

> [!NOTE]
> Nastavení replikace můžete aktualizovat kdykoli před spuštěním replikace, **Spravovat**  >  **replikační počítače**. Po spuštění replikace není možné nastavení změnit.


## <a name="track-and-monitor"></a>Sledování a sledování

1. Sledujte stav úlohy v oznámeních na portálu. 

    ![Sledovat úlohu](./media/tutorial-migrate-vmware-agent/jobs.png)
    
2. Pokud chcete monitorovat stav replikace, klikněte na **replikace serverů** v **Azure Migrate: Migrace serveru**.

    ![Monitorování replikace](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

Replikace probíhá takto:
- Po úspěšném dokončení úlohy spuštění replikace začnou počítače počáteční replikaci do Azure.
- Po dokončení počáteční replikace se spustí rozdílová replikace. Přírůstkové změny na místních discích se pravidelně replikují na disky replik v Azure.


## <a name="run-a-test-migration"></a>Spuštění testu migrace


Po zahájení rozdílové replikace můžete spustit testovací migraci pro virtuální počítače před spuštěním úplné migrace do Azure. Důrazně doporučujeme, abyste to pro každý počítač provedli aspoň jednou, a teprve potom ho migrujete.

- Při spuštění testu migrace proběhne kontrola, že migrace bude fungovat podle očekávání, aniž by to ovlivnilo místní počítače, které zůstávají v provozu, a pokračuje v replikaci. 
- Testovací migrace simuluje migraci tím, že vytvoří virtuální počítač Azure pomocí replikovaných dat (obvykle se migruje na virtuální síť, která není v produkčním prostředí, ve vašem předplatném Azure).
- Replikovaný virtuální počítač Azure můžete použít k ověření migrace, provádění testování aplikace a řešení všech problémů před úplnou migrací.

Proveďte migraci testu následujícím způsobem:


1. V Azure Migrate **cíle migrace**  >  na**servery**  >  **: Migrace serveru**klikněte na **test migrovaných serverů**.

     ![Test migrovaných serverů](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Klikněte pravým tlačítkem na virtuální počítač, který chcete otestovat, a klikněte na **Testovací migrace**.

    ![Testovací migrace](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. V části **Testovací migrace** vyberte virtuální síť Azure, ve které se po migraci bude nacházet virtuální počítač Azure. Doporučujeme použít jinou než produkční virtuální síť.
4. Spustí se úloha **Testovací migrace**. Tuto úlohu můžete monitorovat pomocí oznámení portálu.
5. Po dokončení migrace si můžete migrovaný virtuální počítač Azure prohlédnout na webu Azure Portal v části **Virtuální počítače**. Název počítače má příponu **-Test**.
6. Po dokončení testu v části **Replikace počítačů** klikněte pravým tlačítkem na virtuální počítač Azure a klikněte na **Vyčistit testovací migraci**.

    ![Vyčištění migrace](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Migrace virtuálních počítačů

Po ověření, že migrace testu funguje podle očekávání, můžete migrovat místní počítače.

1. V Azure Migrate Project > **servery**  >  **Azure Migrate: Migrace serveru**klikněte na **replikace serverů**.

    ![Replikace serverů](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. V části **Replikace počítačů** klikněte pravým tlačítkem na virtuální počítač a vyberte **Migrovat**.
3. V **Migrate**  >  **nástroji migrovat vypínání virtuálních počítačů a provádění plánované migrace bez ztráty dat**vyberte **Ano**  >  **OK**.
    - Ve výchozím nastavení Azure Migrate vypne místní virtuální počítač, aby se zajistila minimální ztráta dat. 
    - Pokud virtuální počítač nechcete vypnout, vyberte **Ne**.
4. Pro virtuální počítač se spustí úloha migrace. Tuto úlohu můžete sledovat pomocí oznámení Azure.
5. Po dokončení úlohy můžete virtuální počítač zobrazit a spravovat na stránce **Virtuální počítače**.

## <a name="complete-the-migration"></a>Dokončete migraci

1. Po dokončení migrace klikněte pravým tlačítkem na virtuální počítač > **zastavit migraci**. Provede následující akce:
    - Zastaví replikaci místního počítače.
    - Odebere počítač z počtu **replikačních serverů** v Azure Migrate: Migrace serveru.
    - Vyčistí informace o stavu replikace pro virtuální počítač.
2. Na migrované počítače nainstalujte agenta Azure VM pro [Windows](../virtual-machines/extensions/agent-windows.md) nebo [Linux](../virtual-machines/extensions/agent-linux.md) .
3. Proveďte všechna vylepšení aplikace po migraci, například aktualizujte databázové připojovací řetězce a nakonfigurujte webové servery.
4. U migrované aplikace, která teď běží v Azure, proveďte finální akceptační testování aplikace a migrace.
5. Vyjmutí provozu do migrované instance virtuálního počítače Azure
6. Odeberte místní virtuální počítače z místního inventáře virtuálních počítačů.
7. Odeberte místní virtuální počítače ze záloh.
8. Aktualizujte veškerou interní dokumentaci tak, aby obsahovala nová umístění a IP adresy virtuálních počítačů Azure. 

## <a name="post-migration-best-practices"></a>Osvědčené postupy po migraci

- Místní
    - Přesuňte provoz aplikace do aplikace, která běží na instanci migrovaného virtuálního počítače Azure.
    - Odeberte místní virtuální počítače z místního inventáře virtuálních počítačů.
    - Odeberte místní virtuální počítače ze záloh.
    - Aktualizujte veškerou interní dokumentaci tak, aby obsahovala nová umístění a IP adresy virtuálních počítačů Azure.
- Vylepšení nastavení virtuálních počítačů Azure po migraci:
    - [Agent virtuálního počítače Azure](../virtual-machines/extensions/agent-windows.md) spravuje interakci virtuálního počítače s kontrolerem prostředků infrastruktury Azure. Agent se vyžaduje u některých služeb Azure, jako jsou Azure Backup, Site Recovery a Azure Security. Při migraci virtuálních počítačů s VMare s migrací založenou na agentech nainstaluje instalační program služby mobility na počítače se systémem Windows Agent virtuálního počítače Azure. Na virtuálních počítačích se systémem Linux doporučujeme po migraci nainstalovat agenta.
    - Po migraci ručně odinstalujte službu mobility z virtuálního počítače Azure.
    - Po migraci ručně odinstalujte nástroje VMware.
- V Azure:
    - Proveďte všechna vylepšení aplikace po migraci, například aktualizujte databázové připojovací řetězce a nakonfigurujte webové servery.
    - U migrované aplikace, která teď běží v Azure, proveďte finální akceptační testování aplikace a migrace.
- Provozní kontinuita a zotavení po havárii
    - Zálohujte virtuální počítače Azure pomocí služby Azure Backup, abyste měli data zabezpečená. [Další informace](../backup/quick-backup-vm-portal.md).
    - Replikujte virtuální počítače Azure do sekundární oblasti pomocí služby Site Recovery, aby úlohy mohly neustále běžet a byly dostupné. [Další informace](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Pro zvýšení zabezpečení:
    - Odblokujte a omezte přístup k příchozímu provozu pomocí [správy v čase Azure Security Center](../security-center/security-center-just-in-time.md).
    - Omezte síťový provoz na koncové body správy pomocí [skupin zabezpečení sítě](../virtual-network/network-security-groups-overview.md).
    - Nasaďte službu [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md), která vám pomůže zabezpečit disky a zajistit bezpečnost dat před krádeží a neoprávněným přístupem.
    - Přečtěte si další informace o [zabezpečení prostředků IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) a navštivte [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Pro monitorování a správu:
    - Zvažte nasazení služby [Azure Cost Management](../cost-management-billing/cloudyn/overview.md), která bude monitorovat využití prostředků a útratu.




 ## <a name="next-steps"></a>Další kroky

Prozkoumejte [cestu k migraci do cloudu](/azure/architecture/cloud-adoption/getting-started/migrate) v rozhraní Azure cloudu pro přijetí.