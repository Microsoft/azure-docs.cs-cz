---
title: Migrace místních virtuálních počítačů VMware do Azure s migrací na základě agenta Azure Migrate serveru | Microsoft Docs
description: Tento článek popisuje, jak provést migraci místních počítačů na základě agentů do Azure pomocí migrace Azure Migrate serveru.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 1ea736b560e2e910ede203e8ce8c0b157ebbce71
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640871"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Migrace virtuálních počítačů VMware do Azure (na základě agentů)

V tomto článku se dozvíte, jak migrovat místní virtuální počítače VMware do Azure pomocí migrace založené na agentech pomocí nástroje pro migraci Azure Migrate serveru.

[Azure Migrate](migrate-services-overview.md) poskytuje centrální centrum pro sledování zjišťování, hodnocení a migrace vašich místních aplikací a úloh a instancí virtuálních počítačů AWS/GCP do Azure. Centrum poskytuje Azure Migrate nástroje pro posuzování a migraci i nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců.


V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavte zdrojové prostředí a nasaďte Azure Migrate replikačního zařízení pro migraci založenou na agentech.
> * Nastavte cílové prostředí pro migraci.
> * Nastavte zásady replikace.
> * Povolte replikaci.
> * Spusťte test migrace a ujistěte se, že vše funguje podle očekávání.
> * Spusťte úplnou migraci do Azure.

> [!NOTE]
> Kurzy vám ukážou nejjednodušší cestu nasazení pro scénář, abyste mohli rychle nastavit zkušební verzi. Kurzy používají výchozí možnosti, pokud je to možné, a nezobrazují všechna možná nastavení a cesty. Podrobné pokyny najdete v tématu How to TOS for VMware Assessment and Migration.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.

## <a name="before-you-begin"></a>Před zahájením

Před migrací virtuálních počítačů do Azure doporučujeme, abyste si vyzkoušeli vyhodnocování virtuálního počítače s VMware pomocí posouzení serveru Azure Migrate. Proveďte vyhodnocení následujícím způsobem:

1. Postupujte podle kurzu [a připravte si Azure a VMware](tutorial-prepare-vmware.md) pro posouzení.
2. Pak postupujte podle [tohoto kurzu](tutorial-assess-vmware.md) a nastavte zařízení Azure Migrate pro posouzení a Objevte a vyhodnoťte virtuální počítače.


I když doporučujeme, abyste si vyzkoušeli posouzení, nemusíte před migrací virtuálních počítačů spustit posouzení.

## <a name="migration-methods"></a>Metody migrace

Virtuální počítače VMware můžete migrovat do Azure pomocí nástroje pro migraci Azure Migrate serveru. Tento nástroj nabízí několik možností migrace virtuálních počítačů VMware:

- Replikace bez agenta. Migrujte virtuální počítače, aniž byste museli instalovat cokoli.
- Migrace založená na agentovi. nebo replikace. Nainstalujte agenta (agenta služby mobility) na virtuální počítač pro replikaci.

Pokud chcete rozhodnout, jestli chcete použít migraci bez agenta nebo agenta, přečtěte si tyto články:

- [Přečtěte si informace o](server-migrate-overview.md) možnostech migrace VMware.
- [Porovnejte metody migrace](server-migrate-overview.md#compare-migration-methods).
- Pokud chcete vyzkoušet migraci bez agentů, [postupujte podle tohoto článku](tutorial-migrate-vmware.md) .



## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, musíte mít splněné následující požadavky:

1. [Zkontrolujte](migrate-architecture.md) architekturu migrace VMware.
2. Ujistěte se, že váš účet Azure má přiřazenou roli Přispěvatel virtuálních počítačů, takže máte oprávnění k těmto akcím:

    - Vytvoření virtuálního počítače ve vybrané skupině prostředků
    - Vytvoření virtuálního počítače ve vybrané virtuální síti
    - Zapište na spravovaný disk Azure. 

3. [Nastavte síť Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Místní počítače se replikují do Azure Managed disks. Když převezmete služby při selhání do Azure pro migraci, vytvoří se virtuální počítače Azure z těchto spravovaných disků a připojí se k síti Azure, kterou určíte při nastavování migrace.


## <a name="prepare-azure"></a>Příprava Azure

Pokud jste už vyhodnotili Azure Migrate posouzení serveru, můžete pokyny v této části přeskočit, protože jste už tyto kroky dokončili. 

Pokud jste ještě vyhodnotili, budete muset nastavit oprávnění Azure ještě předtím, než budete moct migrovat pomocí migrace serveru Azure Migrate.

- **Vytvořit projekt**: Váš účet Azure potřebuje oprávnění k vytvoření projektu Azure Migrate. 
- **Zaregistrujte zařízení replikace Azure Migrate**: Zařízení replikace vytvoří a zaregistruje aplikaci Azure Active Directory ve vašem účtu Azure. Pro tuto aplikaci je potřeba delegovat oprávnění.
- **Vytvořit Key Vault**: Pokud chcete migrovat virtuální počítače VMware pomocí migrace serveru Azure Migrate, vytvoří Azure Migrate ve skupině prostředků Key Vault pro správu přístupových klíčů k účtu úložiště replikace v rámci vašeho předplatného. K vytvoření trezoru potřebujete oprávnění přiřazení role ve skupině prostředků, ve které se nachází Azure Migrate projekt. 


### <a name="assign-permissions-to-create-project"></a>Přiřadit oprávnění k vytvoření projektu

1. V Azure Portal otevřete předplatné a vyberte **řízení přístupu (IAM)** .
2. V části **kontrolovat přístup**Najděte příslušný účet a kliknutím na něj Zobrazte oprávnění.
3. Měli byste mít oprávnění **Přispěvatel** nebo **Owner** .
    - Pokud jste právě vytvořili bezplatný účet Azure, jste vlastníkem svého předplatného.
    - Pokud nejste vlastníkem předplatného, pracujte s vlastníkem a přiřaďte roli.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Přiřazení oprávnění k registraci zařízení replikace

U migrace založené na agentech můžete delegovat oprávnění k migraci Azure Migrate serveru a vytvořit a zaregistrovat aplikaci Azure AD ve svém účtu. Můžete přiřadit oprávnění pomocí jedné z následujících metod:

- Tenant nebo globální správce může udělit oprávnění uživatelům v tenantovi, aby mohli vytvářet a registrovat aplikace služby Azure AD.
- Tenant nebo globální správce může k účtu přiřadit roli vývojáře aplikace (která má oprávnění).

Je potřeba poznamenat, že:

- Aplikace nemají žádná jiná přístupová oprávnění k předplatnému, kromě výše popsaných výše.
- Tato oprávnění budete potřebovat, jenom když zaregistrujete nové zařízení replikace. Po nastavení zařízení replikace můžete oprávnění odebrat. 


#### <a name="grant-account-permissions"></a>Udělení oprávnění účtu

Tenant nebo globální správce může udělit oprávnění následujícím způsobem.

1. V Azure AD by měl tenant nebo globální správce přejít na **Azure Active Directory** > **uživatelských nastavení** **uživatelů** > .
2. Správce by měl nastavit **Registrace aplikací** **Ano**.

    ![Oprávnění služby Azure AD](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Toto je výchozí nastavení, které není citlivé. [Další informace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Přiřazení role vývojáře aplikace 

Tenant/globální správce může přiřadit roli vývojář aplikace k účtu. [Další informace](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="assign-permissions-to-create-key-vault"></a>Přiřazení oprávnění k vytváření Key Vault

Přiřaďte oprávnění přiřazení role ke skupině prostředků, ve které se Azure Migrate projekt nachází, takto:

1. Ve skupině prostředků v Azure Portal vyberte **řízení přístupu (IAM)** .
2. V části **kontrolovat přístup**Najděte příslušný účet a kliknutím na něj Zobrazte oprávnění. Potřebujete oprávnění **vlastníka** (nebo **Přispěvatel** a **Správce přístupu uživatele**).
3. Pokud nemáte požadovaná oprávnění, požádejte je od vlastníka skupiny prostředků. 


## <a name="prepare-on-premises-vmware"></a>Příprava VMware v místním prostředí

### <a name="prepare-an-account-for-automatic-discovery"></a>Příprava účtu pro automatické zjišťování

Migrace Azure Migrate serveru potřebuje přístup k serverům VMware pro:

- Automatické zjišťování virtuálních počítačů. Vyžaduje se alespoň účet jen pro čtení.
- Orchestrace replikace, převzetí služeb při selhání a navrácení služeb po obnovení. Potřebujete účet, který může spouštět operace, jako jsou vytváření a odebírání disků a práce s virtuálními počítači.

Vytvořte účet následujícím způsobem:

1. Pokud chcete použít vyhrazený účet, vytvořte roli na úrovni vCenter. Roli pojmenujte například **Azure_Site_Recovery**.
2. Přiřaďte roli oprávnění uvedená v následující tabulce.
3. Vytvořte uživatele na serveru vCenter nebo hostiteli vSphere. Přiřaďte uživateli roli.

#### <a name="vmware-account-permissions"></a>Oprávnění účtu VMware

**Úloha** | **Role/oprávnění** | **Podrobnosti**
--- | --- | ---
**Zjišťování virtuálních počítačů** | Alespoň uživatel jen pro čtení<br/><br/> Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Pokud chcete omezit přístup, přiřaďte podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli **Žádný přístup** s objektem **Rozšířit na podřízený objekt**.
**Úplná replikace, převzetí služeb při selhání a navrácení služeb po obnovení** |  Vytvořte roli (Azure_Site_Recovery) s požadovanými oprávněními a pak ji přiřaďte uživateli nebo skupině VMware.<br/><br/> Objekt datového centra –> Rozšířit na podřízený objekt, role=Azure_Site_Recovery<br/><br/> Úložiště dat –> Přidělit prostor, procházet úložiště dat, operace se soubory nízké úrovně, odebrat soubor, aktualizovat soubory virtuálního počítače<br/><br/> Síť –> Přiřazení sítě<br/><br/> Prostředek –> Přiřadit virtuální počítač k fondu prostředků, migrovat vypnutý virtuální počítač, migrovat zapnutý virtuální počítač<br/><br/> Úlohy –> Vytvořit úlohu, aktualizovat úlohu<br/><br/> Virtuální počítač –> Konfigurace<br/><br/> Virtuální počítač –> Interakce –> zodpovědět dotazy, připojení zařízení, konfigurovat disk CD, konfigurovat disketu, vypnout, zapnout, instalace nástrojů VMware<br/><br/> Virtuální počítač –> Inventář –> Vytvořit, zaregistrovat, zrušit registraci<br/><br/> Virtuální počítač –> Zřizování –> Povolit stažení virtuálního počítače, povolit nahrávání souborů virtuálního počítače<br/><br/> Virtuální počítač –> Snímky –> Odebrat snímky | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Pokud chcete omezit přístup, přiřaďte podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli **Žádný přístup** s objektem **Rozšířit na podřízený objekt**.

### <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Na počítače, které chcete replikovat, bude nutné nainstalovat službu Mobility.

- Zařízení replikace Azure Migrate může provést nabízenou instalaci této služby, když povolíte replikaci pro určitý počítač, nebo ji můžete nainstalovat ručně nebo pomocí instalačních nástrojů.
- V tomto kurzu provedeme instalaci služby Mobility pomocí nabízené instalace.
- Pro nabízenou instalaci musíte připravit účet, který Azure Migrate migrace serveru použít pro přístup k virtuálnímu počítači.

Připravte účet následujícím způsobem:

1. Připravte účet domény nebo místní účet s oprávněními k instalaci na virtuální počítač.
2. Pokud pro virtuální počítače s Windows nepoužíváte doménový účet, zakažte na místním počítači řízení přístupu vzdáleného uživatele tak, že přidáte položku typu DWORD **LocalAccountTokenFilterPolicy**s hodnotou v registru v části **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\ Windows\CurrentVersion\Policies\System**
3. Pro virtuální počítače se systémem Linux Připravte kořenový účet na zdrojovém serveru Linux.


### <a name="check-vmware-requirements"></a>Kontrola požadavků na VMware

Ujistěte se, že servery a virtuální počítače VMware splňují požadavky na migraci do Azure. 


> [!NOTE]
> Migrace na základě agenta pomocí migrace serveru Azure Migrate je založená na funkcích služby Azure Site Recovery. Některé požadavky mohou odkazovat na dokumentaci Site Recovery.

1. [Ověřte](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) požadavky na servery VMware.
2. [Ověřit](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) Požadavky na podporu virtuálních počítačů pro migraci.
3. Ověřte nastavení virtuálního počítače. Místní virtuální počítače, které se replikují do Azure, musí splňovat [požadavky na virtuální počítače Azure](migrate-support-matrix-vmware.md#azure-vm-requirements).



## <a name="add-the-azure-migrate-server-migration-tool"></a>Přidání nástroje pro migraci Azure Migrate serveru

Pokud jste tento kurz nepoužili k vyhodnocení virtuálních počítačů VMware, nastavte projekt Azure Migrate a pak přidejte Nástroj pro migraci Azure Migrate serveru:

1. V Azure Portal > **všechny služby**vyhledejte **Azure Migrate**.
2. V části **služby**vyberte **Azure Migrate**.

    ![Nastavit Azure Migrate](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. V **přehledu**klikněte na **vyhodnotit a migrovat servery**.
4. V části **zjišťování, vyhodnocení a migrace serverů**klikněte na možnost **zhodnotit a migrovat servery**.

    ![Zjišťování a vyhodnocení serverů](./media/tutorial-migrate-vmware-agent/assess-migrate.png)

1. V nabídce **zjišťování, vyhodnocení a migrace serverů**klikněte na **Přidat nástroje**.
2. V rámci **migrace projektu**vyberte předplatné Azure a vytvořte skupinu prostředků, pokud ji nemáte.
3. V části **Project Details (podrobnosti projektu**) zadejte název projektu a zeměpisnou oblast, ve které chcete vytvořit projekt, a klikněte na tlačítko **Další** .

    ![Vytvoření projektu Azure Migrate](./media/tutorial-migrate-vmware-agent/migrate-project.png)

    Azure Migrate projekt můžete vytvořit v některém z těchto geografických oblastí.

    **Zeměpisné oblasti** | **Oblast**
    --- | ---
    Asie | Jihovýchodní Asie
    Evropa | Severní Evropa nebo Západní Evropa
    Spojené státy | Východní USA nebo Středozápadní USA

    Zeměpisné umístění vybrané pro tento projekt slouží jen k uložení metadat získaných z místních virtuálních počítačů. Pro vlastní migraci můžete vybrat libovolnou cílovou oblast.
4. V **nástroji vybrat nástroj pro posouzení**vyberte možnost **Přeskočit přidat nástroj pro vyhodnocení pro nyní** > **Další**.
5. V **nástroji vybrat nástroj**pro migraci **vyberte Azure Migrate: Migrace**serveru > **Next**
6. V části **Revize a přidat nástroje**zkontrolujte nastavení a klikněte na **Přidat nástroje** .
7. Po přidání je nástroj zobrazen v nástroji Azure Migrate Project > **servery** > pro**migraci**.

## <a name="set-up-the-replication-appliance"></a>Nastavení zařízení replikace

Prvním krokem migrace je nastavení zařízení replikace. Zařízení replikace je jediný vysoce dostupný místní virtuální počítač VMware, který je hostitelem těchto součástí:

- **Konfigurační server**: Konfigurační server koordinuje komunikaci mezi místním prostředím a Azure a spravuje replikaci dat.
- **Procesový server:** Procesní server funguje jako replikační brána. Přijímá data replikace; optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do účtu úložiště mezipaměti v Azure. Procesový Server také nainstaluje agenta služby mobility na virtuální počítače, které chcete replikovat, a provádí automatické zjišťování místních virtuálních počítačů VMware.


Pokud chcete nastavit zařízení replikace, Stáhněte si připravenou šablonu Open Application Virtualization (vajíček). Šablonu importujete do VMware a vytvoříte virtuální počítač pro zařízení replikace. 

### <a name="download-the-replication-appliance-template"></a>Stáhnout šablonu zařízení replikace

Stáhněte šablonu následujícím způsobem:

1. V Azure Migrate projektu klikněte v části **cíle migrace**na **servery** .
2. V **Azure Migrate-servery** > **Azure Migrate: Migrace**serveru klikněte na **Vyhledat**.

    ![Vyhledání virtuálních počítačů](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. V nabídce **zjistit** > počítače**jsou vaše počítače virtualizované?** klikněte na **Ano a hypervisor VMware vSphere**.
4. V tématu **jak chcete migrovat?** vyberte možnost **použít replikaci založenou na agentech**.
5. V části **cílová oblast**vyberte oblast Azure, do které chcete migrovat počítače.
6. Vyberte **potvrdit, že cílová oblast pro migraci je název regionu**.
7. Klikněte na **vytvořit prostředky**. Tím dojde k vytvoření trezoru Azure Site Recovery na pozadí.
    - Po kliknutí na toto tlačítko nemůžete změnit cílovou oblast tohoto projektu.
    - Všechny následné migrace jsou v této oblasti.

    ![Vytvoření trezoru služby Recovery Services](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. V nástroji chcete **nainstalovat nové replikační zařízení?** vyberte **nainstalovat zařízení replikace**.
9. Kliknutím na **Stáhnout**Stáhněte zařízení replikace. Tím se stáhne šablona OVF, kterou použijete k vytvoření nového virtuálního počítače VMware, na kterém se zařízení spouští.
    ![Stáhnout VAJÍČKy](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Poznamenejte si název skupiny prostředků a trezoru Recovery Services. Budete je potřebovat během nasazování zařízení.


### <a name="import-the-template-in-vmware"></a>Import šablony do VMware

Po stažení šablony OVF je můžete importovat do VMware a vytvořit tak aplikaci pro replikaci na virtuálním počítači VMware s Windows serverem 2016.

1. Pomocí klientské konzoly VMware vSphere Client se přihlaste k VMware vCenter Serveru nebo hostiteli vSphere ESXi.
2. V nabídce **File** (Soubor) vyberte **Deploy OVF Template** (Nasadit šablonu OVF). Spustíte tak **průvodce nasazením šablony OVF**. 
3. V části **Vybrat zdroj**zadejte umístění staženého ovf.
4. V **podrobnostech revize**vyberte **Další**.
5. V části **Vybrat název a složku** a **Vyberte Konfigurace**přijměte výchozí nastavení.
6. V **Vyberte úložiště** > **Vybrat formát virtuálního disku**pro nejlepší výkon vyberte **silné zřízení Eager s nulovou hodnotou**.
7. Na zbývajících stránkách průvodce přijměte výchozí nastavení.
8. V části **připraveno k dokončení**Chcete-li nastavit virtuální počítač s výchozím nastavením, vyberte možnost **zapnout po** > **dokončení**nasazení.

   > [!TIP]
   > Pokud chcete přidat další síťové rozhraní, zrušte zaškrtnutí políčka **Power on after deployment** (Spustit po nasazení) > **Finish** (Dokončit). Ve výchozím nastavení obsahuje šablona jeden síťový adaptér. Po nasazení můžete přidat další síťové adaptéry.

### <a name="kick-off-replication-appliance-setup"></a>Spustit nastavení replikačního zařízení

1. Z klientské konzole VMware vSphere Client zapněte požadovaný virtuální počítač.
2. Virtuální počítač se spustí do instalačního prostředí Windows Serveru 2016. Přijměte licenční smlouvu a zadejte heslo správce.
3. Až se instalace dokončí, přihlaste se k virtuálnímu počítači jako správce pomocí hesla správce.
4. Při prvním přihlášení se nástroj pro nastavení replikačního zařízení (Azure Site Recovery konfigurační nástroj) spustí během několika sekund.
5. Zadejte název, který se použije k registraci zařízení v Azure Migrate migraci serveru. Pak klikněte na tlačítko **Další**.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání spojení vyberte **Přihlásit se**, abyste se mohli přihlásit ke svému předplatnému Azure.
7. Počkejte, až nástroj dokončí registraci aplikace Azure AD k identifikaci zařízení. Zařízení se restartuje.
1. Znovu se přihlaste k počítači. Za chvilku se spustí automaticky průvodce správou konfiguračního serveru.

### <a name="register-the-replication-appliance"></a>Registrace zařízení replikace

Dokončete nastavení a zaregistrujte zařízení replikace.

1. V Průvodci správou konfiguračního serveru vyberte **nastavení připojení**.
2. Vyberte síťovou kartu (ve výchozím nastavení existuje jenom jedna síťová karta), kterou zařízení pro replikaci používá pro zjišťování virtuálních počítačů, a proveďte nabízenou instalaci služby mobility na zdrojové počítače.
3. Vyberte síťovou kartu, kterou zařízení replikace používá pro připojení k Azure. Potom vyberte **Uložit**. Po nakonfigurování tohoto nastavení už toto nastavení nemůžete změnit.
4. Pokud je zařízení umístěné za proxy server, je nutné zadat nastavení proxy serveru.
    - Zadejte název proxy jako **http://ip-address** nebo. **http://FQDN** Proxy servery HTTPS nejsou podporované.
5. Po zobrazení výzvy k zadání předplatného, skupin prostředků a podrobností o trezoru přidejte podrobnosti, které jste si poznamenali při stažení šablony zařízení.
6. V části **Nainstalovat software třetí strany** přijměte licenční smlouvu. Vyberte **Stáhnout a nainstalovat** a nainstalujte MySQL Server.
7. Vyberte **Nainstalovat VMware PowerCLI**. Než to uděláte, zkontrolujte, jestli jsou zavřená všechna okna prohlížeče. Potom vyberte **Pokračovat**.
8. Než budete pokračovat, v části **Ověřit konfiguraci zařízení** se ověří požadavky.
9. V části **Konfigurovat vCenter Server nebo server vSphere ESXi** zadejte plně kvalifikovaný název domény nebo IP adresu vCenter Serveru nebo hostitele vSphere, na kterém jsou umístěné virtuální počítače, které chcete replikovat. Zadejte port, na kterém server naslouchá. Zadejte popisný název, který se použije pro server VMware v trezoru.
10. Zadejte přihlašovací údaje pro účet, který jste [vytvořili](#prepare-an-account-for-automatic-discovery) pro zjišťování VMware. Vyberte **Přidat** > **pokračovat**.
11. V části **Konfigurovat přihlašovací údaje virtuálního počítače**zadejte přihlašovací údaje, které jste [vytvořili](#prepare-an-account-for-mobility-service-installation) pro nabízenou instalaci služby mobility, když povolíte replikaci pro virtuální počítače.  
    - Když chcete replikovat počítače s Windows, je nutné, aby na nich měl tento účet oprávnění místního správce.
    - U počítačů s Linuxem zadejte údaje superuživatele.
12. Vyberte **Dokončit konfiguraci** a dokončete registraci.


Po zaregistrování zařízení replikace se Azure Migrate posouzení serveru připojí k serverům VMware pomocí zadaného nastavení a vyhledá virtuální počítače. Zjištěné virtuální počítače můžete zobrazit v části **Správa** > **zjištěných položek**na **druhé** kartě.


## <a name="replicate-vms"></a>Replikace virtuálních počítačů

1. V Azure Migrate projektu > **servery** **Azure Migrate: Migrace**serveru klikněte na **replikovat**.

    ![Replikace virtuálních počítačů](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. V případě **replikace**> **Nastavení** > zdroje**jsou vaše počítače virtualizované?** vyberte **Ano s VMware vSphere**.
3. V části **místní zařízení**vyberte název zařízení Azure Migrate, které jste nastavili.
4. V systému **vCenter Server**zadejte název vCenter serveru, který spravuje virtuální počítače, nebo vSphere Server, na kterém jsou virtuální počítače hostované.
5. V části **procesový Server**vyberte název zařízení replikace.
6. Do pole **přihlašovací údaje hosta**zadejte účet správce virtuálních počítačů, který se bude používat pro nabízenou instalaci služby mobility. Pak klikněte **na další: Virtuální počítače**.

    ![Replikace virtuálních počítačů](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. V **Virtual Machines**vyberte počítače, které chcete replikovat.

    - Pokud jste spustili posouzení pro virtuální počítače, můžete použít doporučení pro velikost virtuálního počítače a typ disku (Premium/Standard) z výsledků hodnocení. Pokud to chcete provést, vyberte v části **Import nastavení migrace z Azure Migrate posouzení?** možnost **Ano** .
    - Pokud jste nespustili posouzení nebo nechcete použít nastavení posouzení, vyberte možnost **žádné** možnosti.
    - Pokud jste vybrali použití posouzení, vyberte skupinu virtuálních počítačů a název posouzení.

8. Ověřte každý virtuální počítač, který chcete migrovat. Pak klikněte **na další: Nastavení**cíle
9. V **Nastavení cíl**vyberte předplatné a cílovou oblast, do které migrujete, a určete skupinu prostředků, ve které se virtuální počítače Azure po migraci budou nacházet.
10. V **Virtual Network**vyberte virtuální síť nebo podsíť Azure, ke které se po migraci připojí virtuální počítače Azure.
11. V **zvýhodněné hybridní využití Azure**:

    - Vyberte možnost **ne** , pokud nechcete použít zvýhodněné hybridní využití Azure. Pak klikněte na tlačítko **Další**.
    - Vyberte **Ano** , pokud máte počítače s Windows serverem, které jsou zahrnuté v aktivním programu Software Assurance nebo předplatných Windows serveru, a chcete tuto výhodu využít pro počítače, které migrujete. Pak klikněte na tlačítko **Další**.

12. V části **COMPUTE**Zkontrolujte název virtuálního počítače, velikost, typ disku s operačním systémem a skupinu dostupnosti. Virtuální počítače musí splňovat [požadavky Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Velikost virtuálního počítače**: Pokud používáte doporučení pro vyhodnocení, bude rozevírací seznam velikost virtuálního počítače obsahovat doporučenou velikost. V opačném případě Azure Migrate vybere velikost na základě nejbližší shody v rámci předplatného Azure. Případně můžete vybrat ruční velikost ve **velikosti virtuálního počítače Azure**. 
    - **Disk s operačním systémem**: Zadejte operační systém (spouštěcí) disk pro virtuální počítač. Disk s operačním systémem je disk, který má zaváděcí program pro spouštění a instalaci operačního systému. 
    - **Skupina dostupnosti**: Pokud má být virtuální počítač v sadě dostupnosti Azure po migraci, zadejte sadu. Sada musí být v cílové skupině prostředků, kterou zadáte pro migraci.

13. V části **disky**určete, jestli se mají disky virtuálních počítačů replikovat do Azure, a v Azure vyberte typ disku (standardní disk SSD/HDD nebo Premium Managed Disks). Pak klikněte na tlačítko **Další**.
    - Z replikace můžete vyloučit disky.
    - Pokud disky vyloučíte, nebudou po migraci na virtuálním počítači Azure. 

14. V části **zkontrolovat a spustit replikaci**zkontrolujte nastavení a kliknutím na **replika** spusťte počáteční replikaci pro servery.

> [!NOTE]
> Nastavení replikace můžete aktualizovat kdykoli před spuštěním replikace, **Spravovat** > **replikační počítače**. Po spuštění replikace nelze změnit nastavení.




## <a name="track-and-monitor"></a>Sledování a sledování

- Po kliknutí na **replikace** se spustí úloha spustit replikaci. 
- Po úspěšném dokončení úlohy spuštění replikace začnou počítače počáteční replikaci do Azure.
- Po dokončení počáteční replikace se spustí rozdílová replikace. Přírůstkové změny na místních discích se pravidelně replikují na disky replik v Azure.


Stav úlohy můžete sledovat v oznámeních na portálu.

![Sledovat úlohu](./media/tutorial-migrate-vmware-agent/jobs.png)

Kliknutím na **replikace serverů** v **Azure Migrate můžete monitorovat stav replikace: Migrace**serveru.
![Monitorování replikace](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

## <a name="run-a-test-migration"></a>Spuštění testu migrace


Po zahájení rozdílové replikace můžete spustit testovací migraci pro virtuální počítače před spuštěním úplné migrace do Azure. Důrazně doporučujeme, abyste to pro každý počítač provedli aspoň jednou, a teprve potom ho migrujete.

- Při spuštění testu migrace proběhne kontrola, že migrace bude fungovat podle očekávání, aniž by to ovlivnilo místní počítače, které zůstávají v provozu, a pokračuje v replikaci. 
- Testovací migrace simuluje migraci tím, že vytvoří virtuální počítač Azure pomocí replikovaných dat (obvykle se migruje na virtuální síť, která není v produkčním prostředí, ve vašem předplatném Azure).
- Replikovaný virtuální počítač Azure můžete použít k ověření migrace, provádění testování aplikace a řešení všech problémů před úplnou migrací.

Proveďte migraci testu následujícím způsobem:


1. V Azure Migrate **cíle** > migrace na**servery** > : **Migrace**serveru klikněte na **test migrovaných serverů**.

     ![Servery, pro které proběhla testovací migrace](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Klikněte pravým tlačítkem na virtuální počítač, který chcete otestovat, a klikněte na **test migrovat**.

    ![Otestovat migraci](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. V části **test migrace**vyberte virtuální síť Azure, ve které bude virtuální počítač Azure umístěný po migraci. Doporučujeme použít virtuální síť, která není v produkčním prostředí.
4. Spustí se úloha **testování migrace** . Sledujte úlohu v oznámeních na portálu.
5. Po dokončení migrace si prohlédněte migrovaný virtuální počítač Azure v **Virtual Machines** Azure Portal. Název počítače má příponu **-test**.
6. Po dokončení testu klikněte pravým tlačítkem myši na virtuální počítač Azure v části **replikace**a klikněte na **vyčistit test migrace**.

    ![Vyčištění migrace](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Migrace virtuálních počítačů

Po ověření, že migrace testu funguje podle očekávání, můžete migrovat místní počítače.

1. V Azure Migrate >ch **serverech** > **projektu Azure Migrate: Migrace**serveru klikněte na **replikace serverů**.

    ![Replikují se servery.](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. V části **replikační počítače**klikněte pravým tlačítkem na virtuální počítač > **migrovat**.
3. V nástroji **migrovat** > vypínání**virtuálních počítačů a provádění plánované migrace bez ztráty dat**vyberte **Ano** > . ****
    - Ve výchozím nastavení Azure Migrate vypne místní virtuální počítač a spustí replikaci na vyžádání a provede synchronizaci všech změn virtuálních počítačů, ke kterým došlo od poslední replikace. To zajistí, že nedojde ke ztrátě dat.
    - Pokud nechcete virtuální počítač vypnout, vyberte **ne** .
4. Spustí se úloha migrace pro virtuální počítač. Sledujte úlohu v oznámeních Azure.
5. Po dokončení úlohy můžete virtuální počítač zobrazit a spravovat na stránce **Virtual Machines** .

## <a name="complete-the-migration"></a>Dokončení migrace

1. Po dokončení migrace klikněte pravým tlačítkem na virtuální počítač > **zastavit migraci**. Tím se zastaví replikace místního počítače a vyčistí se informace o stavu replikace pro virtuální počítač.
2. Na migrované počítače nainstalujte agenta Azure VM pro [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) nebo [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) .
3. Proveďte všechna vylepšení aplikace po migraci, například aktualizujte databázové připojovací řetězce a nakonfigurujte webové servery.
4. U migrované aplikace, která teď běží v Azure, proveďte finální akceptační testování aplikace a migrace.
5. Vyjmutí provozu do migrované instance virtuálního počítače Azure
6. Odeberte místní virtuální počítače z místního inventáře virtuálních počítačů.
7. Odeberte místní virtuální počítače ze záloh.
8. Aktualizujte veškerou interní dokumentaci tak, aby obsahovala nová umístění a IP adresy virtuálních počítačů Azure. 

## <a name="post-migration-best-practices"></a>Osvědčené postupy po migraci

- Pro zvýšení odolnosti:
    - Zálohujte virtuální počítače Azure pomocí služby Azure Backup, abyste měli data zabezpečená. [Další informace](../backup/quick-backup-vm-portal.md).
    - Replikujte virtuální počítače Azure do sekundární oblasti pomocí služby Site Recovery, aby úlohy mohly neustále běžet a byly dostupné. [Další informace](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Pro zvýšení zabezpečení:
    - Odblokujte a omezte přístup k příchozímu provozu pomocí [správy v čase Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Omezte síťový provoz na koncové body správy pomocí [skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Nasaďte službu [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview), která vám pomůže zabezpečit disky a zajistit bezpečnost dat před krádeží a neoprávněným přístupem.
    - Přečtěte si další informace o [zabezpečení prostředků IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) a navštivte [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Pro monitorování a správu:
-  Zvažte nasazení služby [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview), která bude monitorovat využití prostředků a útratu.


## <a name="next-steps"></a>Další kroky

Prozkoumejte [cestu k migraci](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) do cloudu v rozhraní Azure cloudu pro přijetí.
