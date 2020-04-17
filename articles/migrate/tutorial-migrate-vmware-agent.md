---
title: Migrace virtuálních počítačů VMware pomocí migrace serveru Azure založené na agentovi
description: Zjistěte, jak spustit migraci virtuálních počítačů VMware na základě agenta pomocí migrace Azure.
ms.topic: tutorial
ms.date: 03/09/2020
ms.custom: MVC
ms.openlocfilehash: 6855c3e81aece0358146608b6cf179fb923c54c8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535328"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Migrace virtuálních počítačů VMware do Azure (na základě agenta)

Tento článek ukazuje, jak migrovat místní virtuální počítače VMware do Azure pomocí migrace na základě agenta pomocí nástroje migrace serveru Azure.


V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavte zdrojové prostředí a nasaďte zařízení replikace Azure Migrate pro migraci založenou na agentovi.
> * Nastavte cílové prostředí pro migraci.
> * Nastavte zásadu replikace.
> * Povolte replikaci.
> * Spusťte testovací migraci a ujistěte se, že vše funguje podle očekávání.
> * Spusťte úplnou migraci do Azure.

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu nasazení pro scénář, takže můžete rychle nastavit proof-of-concept. Kurzy používají výchozí možnosti tam, kde je to možné, a nezobrazují všechna možná nastavení a cesty. Podrobné pokyny najdete v návodech k vyhodnocení a migraci v oblasti VMware.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/pricing/free-trial/) než začnete.

## <a name="before-you-begin"></a>Než začnete

Doporučujeme vyzkoušet vyhodnocení virtuálních počítačů VMware pomocí Azure Migrate Server Assessment, než migrujete virtuální počítače do Azure. Nastavte hodnocení takto:

1. Postupujte podle kurzu [a připravte Azure a VMware](tutorial-prepare-vmware.md) na posouzení.
2. Potom postupujte podle [tohoto kurzu](tutorial-assess-vmware.md) nastavit zařízení Azure Migrate pro posouzení a zjišťovat a vyhodnocovat virtuální počítače.


I když doporučujeme vyzkoušet hodnocení, není třeba spustit hodnocení před migrací virtuálních mích.

## <a name="migration-methods"></a>Metody migrace

Virtuální počítače VMware můžete migrovat do Azure pomocí nástroje Migrace serveru Azure. Tento nástroj nabízí několik možností pro migraci virtuálních zařízení VMware:

- Replikace bez agenta. Migrujte virtuální počítače, aniž byste na ně museli nic instalovat.
- Migrace založená na agentovi. nebo replikaci. Nainstalujte agenta (agenta služeb mobility) na virtuální počítač pro replikaci.

Pokud se chcete rozhodnout, jestli chcete použít migraci bez agenta nebo na základě agenta, přečtěte si tyto články:

- [Přečtěte si o](server-migrate-overview.md) možnostech migrace vmware.
- [Porovnejte metody migrace](server-migrate-overview.md#compare-migration-methods).
- [V tomto článku](tutorial-migrate-vmware.md) vyzkoušejte migraci bez agentů.



## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, musíte mít splněné následující požadavky:

1. [Projděte si](migrate-architecture.md) architekturu migrace vrámci VMware.
2. Ujistěte se, že je vašemu účtu Azure přiřazena role Přispěvatel virtuálního počítače, abyste měli oprávnění k:

    - Vytvoření virtuálního počítače ve vybrané skupině prostředků
    - Vytvoření virtuálního počítače ve vybrané virtuální síti
    - Zapisovat na spravovaný disk Azure. 

3. [Nastavte síť Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Místní počítače se replikují na spravované disky Azure. Při převzetí služeb při selhání do Azure pro migraci, virtuální počítače Azure se vytvoří z těchto spravovaných disků a připojí se k síti Azure, kterou zadáte při nastavování migrace.


## <a name="prepare-azure"></a>Příprava Azure

Pokud jste již spustit hodnocení s Azure Migrate Server Assessment, můžete přeskočit pokyny v této části, protože jste již dokončili tyto kroky. 

Pokud jste nespustili hodnocení, musíte nastavit oprávnění Azure před migrací pomocí migrace serveru Azure.

- **Vytvoření projektu**: Váš účet Azure potřebuje oprávnění k vytvoření projektu Migrace Azure. 
- **Registrace replikačního zařízení Azure Migrate**: Zařízení pro replikaci vytvoří a zaregistruje aplikaci Azure Active Directory ve vašem účtu Azure. Delegujte oprávnění pro toto.
- **Vytvoření trezoru klíčů**: Chcete-li migrovat virtuální počítače VMware pomocí migrace serveru Azure, Azure Migrate vytvoří trezor klíčů ve skupině prostředků pro správu přístupových klíčů k účtu úložiště replikace ve vašem předplatném. Chcete-li vytvořit úschovnu, potřebujete oprávnění přiřazení role ve skupině prostředků, ve které se nachází projekt Migrace Azure. 


### <a name="assign-permissions-to-create-project"></a>Přiřazení oprávnění k vytvoření projektu

1. Na webu Azure Portal otevřete předplatné a vyberte **řízení přístupu (IAM).**
2. V **části Zkontrolovat přístup**vyhledejte příslušný účet a kliknutím na něj zobrazte oprávnění.
3. Měli byste mít oprávnění **přispěvatele** nebo **vlastníka.**
    - Pokud jste si právě vytvořili bezplatný účet Azure, jste vlastníkem předplatného.
    - Pokud nejste vlastníkem předplatného, spolupracujte s vlastníkem a přiřaďte roli.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Přiřazení oprávnění k registraci replikačního zařízení

U migrace založené na agentovi delegujte oprávnění pro migraci serveru Azure a vytvořte a zaregistrujte aplikaci Azure AD ve vašem účtu. Oprávnění můžete přiřadit jedním z následujících způsobů:

- Klient/globální správce můžete udělit oprávnění uživatelům v tenantovi, k vytvoření a registraci aplikací Azure AD.
- Klient/globální správce můžete přiřadit role Vývojář aplikace (která má oprávnění) k účtu.

Stojí za zmínku, že:

- Aplikace nemají k předplatnému jiná přístupová oprávnění než ta, která jsou popsána výše.
- Tato oprávnění potřebujete pouze při registraci nového zařízení replikace. Oprávnění můžete odebrat po nastavení replikačního zařízení. 


#### <a name="grant-account-permissions"></a>Udělit oprávnění k účtu

Klient/globální správce může udělit oprávnění následujícím způsobem

1. Ve službě Azure AD by měl klient/globální správce přejít na**nastavení uživatelů****služby** >  **Azure Active Directory** > .
2. Správce by měl nastavit **registrace aplikací** na **Ano**.

    ![Oprávnění Azure AD](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Toto je výchozí nastavení, které není citlivé. [Další informace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Přiřadit roli vývojáře aplikací 

Klient/globální správce můžete přiřadit roli vývojáře aplikací k účtu. [Další informace](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="assign-permissions-to-create-key-vault"></a>Přiřazení oprávnění k vytvoření trezoru klíčů

Přiřazení oprávnění přiřazení rolí skupině prostředků, ve které se nachází projekt Migrace Azure, postupujte takto:

1. Ve skupině prostředků na webu Azure Portal vyberte **Řízení přístupu (IAM).**
2. V **části Zkontrolovat přístup**vyhledejte příslušný účet a kliknutím na něj zobrazte oprávnění. Potřebujete oprávnění **vlastníka** (nebo **přispěvatele** a **správce přístupu uživatelů).**
3. Pokud nemáte požadovaná oprávnění, požádejte je od vlastníka skupiny prostředků. 


## <a name="prepare-on-premises-vmware"></a>Příprava VMware v místním prostředí

### <a name="prepare-an-account-for-automatic-discovery"></a>Příprava účtu pro automatické zjišťování

Migrace migrace serveru Azure vyžaduje přístup k serverům VMware k:

- Automatické zjišťování virtuálních počítačů. Vyžaduje se alespoň účet jen pro čtení.
- Orchestrace replikace, převzetí služeb při selhání a navrácení služeb po obnovení. Potřebujete účet, který může spouštět operace, jako jsou vytváření a odebírání disků a práce s virtuálními počítači.

Vytvořte účet následujícím způsobem:

1. Pokud chcete použít vyhrazený účet, vytvořte roli na úrovni vCenter. Roli pojmenujte například **Azure_Site_Recovery**.
2. Přiřaďte roli oprávnění uvedená v následující tabulce.
3. Vytvořte uživatele na serveru vCenter nebo hostiteli vSphere. Přiřaďte uživateli roli.

#### <a name="vmware-account-permissions"></a>Oprávnění účtu VMware

**Úkol** | **Role/oprávnění** | **Podrobnosti**
--- | --- | ---
**Zjišťování virtuálních počítačů** | Alespoň uživatel jen pro čtení<br/><br/> Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Chcete-li omezit přístup, přiřaďte roli **Žádný přístup** s **objektem Propagate podřízenému** objektu, podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální připojení a sítě).
**Úplná replikace, převzetí služeb při selhání a navrácení služeb po obnovení** |  Vytvořte roli (Azure_Site_Recovery) s požadovanými oprávněními a pak ji přiřaďte uživateli nebo skupině VMware.<br/><br/> Objekt datového centra –> Rozšířit na podřízený objekt, role=Azure_Site_Recovery<br/><br/> Úložiště dat –> Přidělit prostor, procházet úložiště dat, operace se soubory nízké úrovně, odebrat soubor, aktualizovat soubory virtuálního počítače<br/><br/> Síť –> Přiřazení sítě<br/><br/> Prostředek –> Přiřadit virtuální počítač k fondu prostředků, migrovat vypnutý virtuální počítač, migrovat zapnutý virtuální počítač<br/><br/> Úlohy –> Vytvořit úlohu, aktualizovat úlohu<br/><br/> Virtuální počítač –> Konfigurace<br/><br/> Virtuální počítač –> Interakce –> zodpovědět dotazy, připojení zařízení, konfigurovat disk CD, konfigurovat disketu, vypnout, zapnout, instalace nástrojů VMware<br/><br/> Virtuální počítač –> Inventář –> Vytvořit, zaregistrovat, zrušit registraci<br/><br/> Virtuální počítač –> Zřizování –> Povolit stažení virtuálního počítače, povolit nahrávání souborů virtuálního počítače<br/><br/> Virtuální počítač –> Snímky –> Odebrat snímky | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Chcete-li omezit přístup, přiřaďte roli **Žádný přístup** s **objektem Propagate podřízenému** objektu, podřízeným objektům (hostitelé vSphere, datastores, VMsa, nd sítě).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Na počítače, které chcete replikovat, bude nutné nainstalovat službu Mobility.

- Zařízení replikace Migrace Azure může provést nabízenou instalaci této služby, když povolíte replikaci pro počítač, nebo ji můžete nainstalovat ručně nebo pomocí instalačních nástrojů.
- V tomto kurzu provedeme instalaci služby Mobility pomocí nabízené instalace.
- Pro nabízenou instalaci je potřeba připravit účet, který migrace serveru Azure můžete použít pro přístup k virtuálnímu počítači. Tento účet se používá pouze pro nabízenou instalaci, pokud službu Mobility nenainstalujete ručně.

Připravte účet následujícím způsobem:

1. Připravte účet domény nebo místní účet s oprávněními k instalaci na virtuální počítač.
2. Pokud u virtuálních počítačů se systémem Windows nepoužíváte účet domény, zakažte řízení vzdáleného přístupu uživatelů v místním počítači přidáním položky DWORD **LocalAccountTokenFilterPolicy**s hodnotou v registru v části **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**
3. Pro virtuální počítače s Linuxem připravte kořenový účet na zdrojovém serveru Linux.


### <a name="check-vmware-requirements"></a>Kontrola požadavků na VMware

Ujistěte se, že servery a virtuální počítače VMware splňují požadavky na migraci do Azure. 


> [!NOTE]
> Migrace založená na agentovi s migrací serveru Azure je založená na funkcích služby Azure Site Recovery. Některé požadavky mohou odkazovat na dokumentaci site recovery.

1. [Ověřte](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) požadavky na servery VMware.
2. [Ověřit](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) Požadavky na podporu virtuálních her pro migraci.
3. Ověřte nastavení virtuálních počítače. Místní virtuální počítače, které replikujete do Azure, musí splňovat [požadavky na virtuální počítače Azure](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).



## <a name="add-the-azure-migrate-server-migration-tool"></a>Přidání nástroje migrace serveru Azure

Pokud jste nepostupovali podle kurzu k vyhodnocení virtuálních počítačů VMware, nastavte projekt Migrace Azure a pak přidejte nástroj migrace serveru Azure:

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
2. V části **Služby** vyberte **Azure Migrate**.

    ![Nastavení migrace Azure](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. V části **Přehled** klikněte na **Posoudit a migrovat servery**.
4. V části **Zjistit, vyhodnoťte a migrujte servery**na **možnost Posoudit a migrovat servery**.

    ! [Objevte a vyhodnocujte servery] (./media/tutorial-migrate-vmware-agent/assess-migrate.png

1. V části **Zjistit, posoudit a migrovat servery** klikněte na **Přidat nástroje**.
2. V části **Projekt migrace** vyberte své předplatné Azure a vytvořte skupinu prostředků, pokud ji ještě nemáte.
3. V části **Podrobnosti o projektu** zadejte název projektu a zeměpisnou oblast, ve které chcete projekt vytvořit, a klikněte na **Další**. Zkontrolujte podporované zeměpisné oblasti pro [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [vládní cloudy](migrate-support-matrix.md#supported-geographies-azure-government).

    ![Vytvoření projektu migrace Azure](./media/tutorial-migrate-vmware-agent/migrate-project.png)


4. V **nástroji pro výběr hodnocení**vyberte přeskočit přidání nástroje pro hodnocení pro tuto **chvíli** > **Další**.
5. V **nástroji Pro výběr migrace**vyberte Možnost Migrace **Azure: Migrace** > serveru**Další**.
6. V části **Zkontrolovat a přidat nástroje** zkontrolujte nastavení a klikněte na **Přidat nástroje**.
7. Po přidání nástroje se zobrazí v**nástrojích**Migrace projektu Azure > **servery** > .

## <a name="set-up-the-replication-appliance"></a>Nastavení zařízení pro replikaci

Prvním krokem migrace je nastavení zařízení replikace. Zařízení pro replikaci je jeden, vysoce dostupný místní virtuální počítač VMware, který hostuje tyto součásti:

- **Konfigurační server**: Konfigurační server koordinuje komunikaci mezi místním a Azure a spravuje replikaci dat.
- **Procesní server**: Procesový server funguje jako replikační brána. Přijímá data replikace; optimalizuje ji pomocí ukládání do mezipaměti, komprese a šifrování a odesílá ji do účtu úložiště mezipaměti v Azure. Procesní server také nainstaluje agenta služby Mobility service na virtuální chod, které chcete replikovat, a provede automatické zjišťování místních virtuálních počítačích VMware.


Zařízení pro replikaci můžete nastavit několika způsoby.

- Nastavte si stažené open virtualizační aplikace (OVA) šablony. Importujete šablonu do vmware a vytvořte virtuální počítače zařízení replikace. Toto je metoda použitá v tomto kurzu.
- Nastavte skript.

### <a name="download-the-replication-appliance-template"></a>Stažení šablony replikačního zařízení

Stáhněte si šablonu takto:

1. V projektu Migrace Azure klikněte na **Servery** v části **Cíle migrace**.
2. V **migraci Azure – servery, které** > **Azure mií: Migrace serveru**, klikněte na **Zjistit**.

    ![Vyhledání virtuálních počítačů](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. V **discover machines** > **Jsou vaše počítače virtualizované?**, klikněte na **Ano, s hypervisorem VMWare vSphere**.
4. V **souboru Jak chcete migrovat?** vyberte **možnost Použití replikace založené na agentovi**.
5. V **oblasti Cíl**vyberte oblast Azure, do které chcete počítače migrovat.
6. Vyberte **Možnost Potvrdit, že cílová oblast pro migraci je název oblasti**.
7. Klepněte na **tlačítko Vytvořit zdroje**. Tím se vytvoří trezor azure site recovery na pozadí.
    - Cílovou oblast pro tento projekt nelze změnit po klepnutí na toto tlačítko.
    - Všechny následné migrace jsou do této oblasti.

    ![Vytvoření trezoru služby Recovery Services](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. V části Chcete nainstalovat nové zařízení pro **Install a replication appliance** **replikaci?**
9. Chcete-li stáhnout zařízení pro replikaci, klepněte na tlačítko **Stáhnout**. Tím se stáhne šablona OVF, kterou použijete k vytvoření nového virtuálního počítače VMware, který zařízení spouští.
    ![Stažení OVA](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Poznamenejte si název skupiny prostředků a trezoru služby Recovery Services. Potřebujete je během nasazení zařízení.


### <a name="import-the-template-in-vmware"></a>Import šablony do VMware

Po stažení šablony OVF ji importujete do vmware a vytvoříte replikační aplikaci na virtuálním počítači VMware se systémem Windows Server 2016.

1. Pomocí klientské konzoly VMware vSphere Client se přihlaste k VMware vCenter Serveru nebo hostiteli vSphere ESXi.
2. V nabídce **Soubor** vyberte **možnost Nasadit šablonu OVF** a spusťte **Průvodce nasazením šablony OVF**. 
3. Do **položky Vybrat zdroj**zadejte umístění staženého OVF.
4. V **části Podrobnosti o kontrole**vyberte **Další**.
5. V **části Vybrat název a složku** a Vybrat konfiguraci přijměte výchozí nastavení. **Select configuration**
6. V **části Vybrat úložiště** > **Vyberte formát virtuálního disku**, chcete-li dosáhnout nejlepšího výkonu, vyberte možnost Silná **rezerva eager zeroed**.
7. Na zbývajících stránkách průvodce přijměte výchozí nastavení.
8. **Chcete-li**nastavit virtuální počítač s výchozím nastavením , vyberte **možnost Zapnout po dokončení nasazení** > a nastavte možnost**Zapnout**po dokončení nasazení .

   > [!TIP]
   > Pokud chcete přidat další nic, zrušte po > **dokončení** **nasazení možnost INIC**. Ve výchozím nastavení obsahuje šablona jeden síťový adaptér. Po nasazení můžete přidat další síťové adaptéry.

### <a name="kick-off-replication-appliance-setup"></a>Zahájení nastavení replikačního zařízení

1. Z klientské konzole VMware vSphere Client zapněte požadovaný virtuální počítač.
2. Virtuální počítač se spustí do instalačního prostředí Windows Serveru 2016. Přijměte licenční smlouvu a zadejte heslo správce.
3. Po dokončení instalace se přihlaste k virtuálnímu počítače jako správce pomocí hesla správce.
4. Při prvním přihlášení se během několika sekund spustí nástroj pro nastavení replikačního zařízení (Nástroj pro konfiguraci webu Azure.
5. Zadejte název, který se má použít pro registraci zařízení pomocí migrace serveru Azure. Pak klikněte na **Další**.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání spojení vyberte **Přihlásit se**, abyste se mohli přihlásit ke svému předplatnému Azure.
7. Počkejte, až nástroj dokončí registraci aplikace Azure AD k identifikaci zařízení. Přístroj se restartuje.
1. Znovu se přihlaste k počítači. Za chvilku se spustí automaticky průvodce správou konfiguračního serveru.

### <a name="register-the-replication-appliance"></a>Registrace zařízení pro replikaci

Dokončete nastavení a registraci zařízení pro replikaci.

1. V Průvodci správou konfiguračního serveru vyberte **možnost Nastavení připojení**.
2. Vyberte nic (ve výchozím nastavení je pouze jedna nic), které zařízení replikace používá pro zjišťování virtuálních počítačů a provést nabízenou instalaci služby Mobility na zdrojových počítačích.
3. Vyberte nic, které zařízení replikace používá pro připojení k Azure. Potom vyberte **Uložit**. Toto nastavení nelze po konfiguraci změnit.
4. Pokud je zařízení umístěno za proxy serverem, je třeba zadat nastavení proxy serveru.
    - Zadejte název **http://ip-address**serveru **http://FQDN**proxy jako , nebo . Proxy servery HTTPS nejsou podporovány.
5. Po zobrazení výzvy k zadání informací o předplatném, skupinách prostředků a trezoru přidejte podrobnosti, které jste si poznamenali při stahování šablony zařízení.
6. V části **Nainstalovat software třetí strany** přijměte licenční smlouvu. Vyberte **Stáhnout a nainstalovat** a nainstalujte MySQL Server.
7. Vyberte **Nainstalovat VMware PowerCLI**. Než to uděláte, zkontrolujte, jestli jsou zavřená všechna okna prohlížeče. Potom vyberte **Pokračovat**.
8. Než budete pokračovat, v části **Ověřit konfiguraci zařízení** se ověří požadavky.
9. V části **Konfigurovat vCenter Server nebo server vSphere ESXi** zadejte plně kvalifikovaný název domény nebo IP adresu vCenter Serveru nebo hostitele vSphere, na kterém jsou umístěné virtuální počítače, které chcete replikovat. Zadejte port, na kterém server naslouchá. Zadejte popisný název, který se použije pro server VMware v trezoru.
10. Zadejte přihlašovací údaje pro účet, který jste [vytvořili](#prepare-an-account-for-automatic-discovery) pro zjišťování společnosti VMware. Vyberte **Přidat** > **pokračovat**.
11. V **části Konfigurace přihlašovacích údajů virtuálního počítače**zadejte pověření, která jste [vytvořili](#prepare-an-account-for-mobility-service-installation) pro nabízenou instalaci služby Mobility, když povolíte replikaci pro virtuální počítače.  
    - Když chcete replikovat počítače s Windows, je nutné, aby na nich měl tento účet oprávnění místního správce.
    - U počítačů s Linuxem zadejte údaje superuživatele.
12. Vyberte **Dokončit konfiguraci** a dokončete registraci.


Po registraci zařízení replikace azure migrate server assessment připojí k serverům VMware pomocí zadaného nastavení a zjistí virtuální počítače. Zjištěné virtuální ms můžete zobrazit v části **Spravovat** > **zjištěné položky**na kartě **Ostatní.**


## <a name="replicate-vms"></a>Replikace virtuálních počítačů

Teď vyberte virtuální chod pro migraci.

> [!NOTE]
> Můžete replikovat až 10 počítačů dohromady. Pokud potřebujete replikovat více, replikujte je současně v dávkách po 10.

1. V projektu Azure Migrate > **servery** **, Azure Migrate: Migrace serveru**, klikněte na **Replikovat**.

    ![Replikace virtuálních počítačů](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. V části **Replikovat** > **Nastavení zdroje** > **Máte počítače ve virtuální podobě?** vyberte **Ano, s VMware vSphere**.
3. V **místním zařízení**vyberte název zařízení Azure Migrate, které jste nastavili.
4. Na **serveru vCenter**zadejte název serveru vCenter spravujícího virtuální počítač nebo serveru vSphere, na kterém jsou virtuální servery hostovány.
5. V **části Procesní server**vyberte název zařízení pro replikaci.
6. V **části Pověření hosta**zadejte účet správce virtuálního klienta, který se použije pro nabízenou instalaci služby Mobility. Pak klikněte na **Další: Virtuální počítače**.

    ![Replikace virtuálních počítačů](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. Ve **virtuálních počítačích**vyberte počítače, které chcete replikovat.

    - Pokud jste pro virtuální počítače spustili posouzení, můžete použít doporučenou velikost a typ disku (Premium nebo Standard) z výsledků posouzení. Pokud to chcete provést, v části **Importovat nastavení migrace z posouzení Azure Migrate?** vyberte možnost **Ano**.
    - Pokud jste neprovedli posouzení nebo pokud nechcete použít nastavení posouzení, vyberte možnost **Ne**.
    - Pokud jste se rozhodli použít posouzení, vyberte skupinu virtuálních počítačů a název posouzení.

8. Zkontrolujte každý virtuální virtuální mísa, který chcete migrovat. Pak klepněte na tlačítko **Další: Nastavení cíle**.
9. V části **Nastavení cíle** vyberte předplatné a cílovou oblast migrace a zadejte skupinu prostředků, ve které se po migraci budou nacházet virtuální počítače Azure.
10. V části **Virtuální síť** vyberte virtuální síť a podsíť Azure, ke kterým se po migraci připojí virtuální počítače Azure.
11. V části **Zvýhodněné hybridní využití Azure**:

    - Vyberte **Ne**, pokud nechcete využít Zvýhodněné hybridní využití Azure. Pak klikněte na **Další**.
    - Vyberte **Ano**, pokud máte počítače s Windows Serverem s aktivním Software Assurance nebo předplatným Windows Serveru a u migrovaných počítačů chcete využít tuto výhodu. Pak klikněte na **Další**.

12. V části **Výpočetní prostředky** zkontrolujte název, velikost, typ disku s operačním systémem a skupinu dostupnosti virtuálního počítače. Virtuální počítače musí splňovat [požadavky Azure](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms).

    - **Velikost virtuálního počítače**: Pokud používáte doporučení pro vyhodnocení, rozbalovací seznam velikosti virtuálního počítače bude obsahovat doporučenou velikost. Jinak Azure Migrate vybere velikost na základě nejbližší shody v předplatném Azure. Případně můžete velikost vybrat ručně v části **Velikost virtuálního počítače Azure**. 
    - **Disk operačního systému**: Zadejte disk operačního systému (boot) pro virtuální počítače. Disk s operačním systémem je disk, který obsahuje spouštěcí zavaděč a instalační program operačního systému. 
    - **Dostupnost set**: Pokud virtuální počítač by měl být v sadě dostupnosti Azure po migraci, zadejte sadu. Skupina musí být v cílové skupině prostředků, kterou pro migraci zadáte.

13. V **části Disky**určete, jestli mají být disky virtuálního počítače replikovány do Azure, a vyberte typ disku (standardní Disk SSD/HDD nebo disky spravovaný prémií) v Azure. Pak klikněte na **Další**.
    - Disky můžete z replikace vyloučit.
    - Pokud disky vyloučíte, po migraci nebudou na virtuálním počítači Azure. 

14. V části **Kontrola a zahájení replikace** zkontrolujte nastavení a kliknutím na **Replikovat** spusťte počáteční replikaci serverů.

> [!NOTE]
> Nastavení replikace můžete aktualizovat kdykoli před zahájením replikace, **Správa** > **replikačních počítačů**. Po spuštění replikace není možné nastavení změnit.




## <a name="track-and-monitor"></a>Sledování a sledování

- Po klepnutí na tlačítko **Replikovat** úlohu Zahájit replikaci se spustí. 
- Po úspěšném dokončení úlohy Spustit replikaci začnou počítače zahájit počáteční replikaci do Azure.
- Po dokončení počáteční replikace začíná rozdílová replikace. Přírůstkové změny na místních discích se pravidelně replikují na repliky disků v Azure.


Stav úlohy můžete sledovat v oznámeních portálu.

![Sledovat úlohu](./media/tutorial-migrate-vmware-agent/jobs.png)

Stav replikace můžete sledovat kliknutím na **Replikace serverů** v **Azure Migrate: Server Migration**.
![Monitorování replikace](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

## <a name="run-a-test-migration"></a>Spuštění testu migrace


Když začíná rozdílová replikace, můžete spustit testovací migraci pro virtuální počítače, před spuštěním úplné migrace do Azure. Důrazně doporučujeme provést alespoň jednou pro každý počítač, před migrací.

- Spuštění testovací migrace kontroluje, že migrace bude fungovat podle očekávání, aniž by to mělo vliv na místní počítače, které zůstávají funkční a pokračují v replikaci. 
- Migrace testu simuluje migraci vytvořením virtuálního počítače Azure pomocí replikovaných dat (obvykle migrace na neprodukční virtuální síť v předplatném Azure).
- Replikovaný testovací virtuální počítač Azure můžete použít k ověření migrace, provedení testování aplikací a řešení všech problémů před úplnou migrací.

Proveďte testovací migraci následujícím způsobem:


1. V **oblasti cílů** > migrace**Servery, které** > **Azure migruje: Migrace serveru**, klikněte na testovat **migrované servery**.

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

1. V projektu Azure Migrate > **servery, které** > **Azure migruje: Migrace serveru**klikněte na **Replikace serverů**.

    ![Replikace serverů](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. V části **Replikace počítačů** klikněte pravým tlačítkem na virtuální počítač a vyberte **Migrovat**.
3. V **migrovat** > **Vypnout virtuální počítače a provést plánovanou migraci bez ztráty dat**vyberte **Ano** > **OK**.
    - Ve výchozím nastavení Azure Migrate vypne místní virtuální počítač, aby se zajistila minimální ztráta dat. 
    - Pokud virtuální počítač nechcete vypnout, vyberte **Ne**.
4. Pro virtuální počítač se spustí úloha migrace. Tuto úlohu můžete sledovat pomocí oznámení Azure.
5. Po dokončení úlohy můžete virtuální počítač zobrazit a spravovat na stránce **Virtuální počítače**.

## <a name="complete-the-migration"></a>Dokončení migrace

1. Po dokončení migrace klikněte pravým tlačítkem myši na **> zastavit migraci**virtuálního . Tím postupujte takto:
    - Zastaví replikaci pro místní počítač.
    - Odebere počítač z počtu **replikačních serverů** v Azure Migrate: Migrace serveru.
    - Vyčistí informace o stavu replikace pro virtuální hosti.
2. Nainstalujte agenta Azure VM [Pro Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) nebo [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) do migrovaných počítačů.
3. Proveďte všechna vylepšení aplikace po migraci, například aktualizujte databázové připojovací řetězce a nakonfigurujte webové servery.
4. U migrované aplikace, která teď běží v Azure, proveďte finální akceptační testování aplikace a migrace.
5. Přeškrtněte provoz na migrovna v instanci virtuálního počítače Azure.
6. Odeberte místní virtuální počítače z místního inventáře virtuálních počítačů.
7. Odeberte místní virtuální počítače ze záloh.
8. Aktualizujte veškerou interní dokumentaci tak, aby obsahovala nová umístění a IP adresy virtuálních počítačů Azure. 

## <a name="post-migration-best-practices"></a>Osvědčené postupy po migraci

- Lokálně
    - Přesuňte provoz aplikace do aplikace, která běží na instanci migrovaného virtuálního počítače Azure.
    - Odeberte místní virtuální počítače z místního inventáře virtuálních počítačů.
    - Odeberte místní virtuální počítače ze záloh.
    - Aktualizujte veškerou interní dokumentaci tak, aby obsahovala nová umístění a IP adresy virtuálních počítačů Azure.
- Vyladit nastavení virtuálních počítačů Azure po migraci:
    - [Agent virtuálního počítače Azure](../virtual-machines/extensions/agent-windows.md) spravuje interakci virtuálního počítače s kontrolerem prostředků infrastruktury Azure. Agent se vyžaduje u některých služeb Azure, jako jsou Azure Backup, Site Recovery a Azure Security. Při migraci virtuálních počítačů VMare s migrací na základě agenta nainstaluje instalační služba mobility agenta Azure VM na počítačích s Windows. Na virtuálních počítačích s Linuxem doporučujeme nainstalovat agenta po migraci.
    - Ručně odinstalujte službu Mobility z virtuálního počítače Azure po migraci.
    - Po migraci ručně odinstalujte nástroje VMware.
- V Azure:
    - Proveďte všechna vylepšení aplikace po migraci, například aktualizujte databázové připojovací řetězce a nakonfigurujte webové servery.
    - U migrované aplikace, která teď běží v Azure, proveďte finální akceptační testování aplikace a migrace.
- Kontinuita provozu/zotavení po havárii
    - Zálohujte virtuální počítače Azure pomocí služby Azure Backup, abyste měli data zabezpečená. [Další informace](../backup/quick-backup-vm-portal.md).
    - Replikujte virtuální počítače Azure do sekundární oblasti pomocí služby Site Recovery, aby úlohy mohly neustále běžet a byly dostupné. [Další informace](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Pro zvýšení zabezpečení:
    - Uzamkněte a omezte přístup k příchozímu provozu pomocí [Azure Security Center – správa právě v čase](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Omezte síťový provoz na koncové body správy pomocí [skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Nasaďte službu [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview), která vám pomůže zabezpečit disky a zajistit bezpečnost dat před krádeží a neoprávněným přístupem.
    - Přečtěte si další informace o [zabezpečení prostředků IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) a navštivte [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Pro monitorování a správu:
    - Zvažte nasazení služby [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview), která bude monitorovat využití prostředků a útratu.




 ## <a name="next-steps"></a>Další kroky

Prozkoumejte [cestu migrace do cloudu](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) v rámci azure cloudpřijetí.
