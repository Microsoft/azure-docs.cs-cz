---
title: Migrace místních virtuálních počítačů VMware do Azure s využitím založené na agentovi migrace serveru služby Azure Migrate | Dokumentace Microsoftu
description: Tento článek popisuje, jak provést migraci založené na agentovi z místních počítačů do Azure pomocí služby Azure Migrate migrace serveru
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: fe83cd5f38e8c091ee72875da370b6929a99b727
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854143"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Migrace virtuálních počítačů VMware do Azure (založené na agentovi)

Tento článek popisuje, jak migrovat místní virtuální počítače VMware do Azure, pomocí migrace založené na agentovi s nástrojem Azure Migrate serveru migrace.

[Azure Migrate](migrate-services-overview.md) poskytuje centrální rozbočovač pro sledování zjišťování, vyhodnocení a migraci místních aplikací a úloh a instancí virtuálního počítače AWS a GCP, do Azure. Centrum poskytuje Azure Migrate nástroje pro vyhodnocení a migrace, jakož i nabídky softwaru třetích stran výrobce (ISV).


V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavení zdrojového prostředí a nasazení pro migrace na základě agenta replikace zařízení Azure Migrate.
> * Nastavení cílového prostředí pro migraci.
> * Nastavení zásady replikace.
> * Povolení replikace.
> * Spuštění testu migrace, abyste měli jistotu, že všechno funguje podle očekávání.
> * Spusťte úplné migrace do Azure.

> [!NOTE]
> Kurzy vám ukážou, nejjednodušší způsob nasazení pro scénář tak, že můžete rychle nastavit testování konceptu. Kurzy použijte výchozí možnosti, kde je to možné a nezobrazovat všechna možná nastavení a cesty. Podrobné pokyny najdete v tématu postupy pro VMware vyhodnocení a migraci.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.

## <a name="before-you-begin"></a>Před zahájením

Doporučujeme vám vyzkoušet posouzení virtuálních počítačů VMware pomocí Azure Migrate Server Assessment před migrací virtuálních počítačů do Azure. Posouzení wmm nastavte takto:

1. Postupujte podle kurzu a [Příprava Azure a VMware](tutorial-prepare-vmware.md) pro posouzení.
2. Pak použijte [v tomto kurzu](tutorial-assess-vmware.md) nastavení zařízení Azure Migrate pro posouzení, zjišťování a posouzení virtuálních počítačů.


I když vám doporučujeme vyzkoušet posouzení, není nutné spustit posouzení, před migrací virtuálních počítačů.

## <a name="migration-methods"></a>Metody migrace

Můžete migrovat virtuální počítače VMware do Azure pomocí nástroje Azure Migrate serveru migrace. Tento nástroj nabízí několik možností pro migraci virtuálních počítačů VMware:

- Bez agentů replikace. Migrace virtuálních počítačů, aniž by bylo potřeba nic instalovat na ně.
- Migrace na základě agenta. nebo replikace. Instalace agenta (agenta služby Mobility) na virtuální počítač pro replikaci.

Rozhodování o tom, zda chcete použít migraci bez agentů nebo založené na agentovi, projděte si tyto články:

- [Další informace o](server-migrate-overview.md) možnosti migrace VMware.
- [Přečtěte si omezení](server-migrate-overview.md#agentless-migration-limitations) migrace bez agenta.
- [Postupujte podle tohoto článku](tutorial-migrate-vmware.md) vyzkoušet migrace bez agenta.



## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, musíte mít splněné následující požadavky:

1. [Kontrola](migrate-architecture.md) architekturu migrace VMware.
2. Ujistěte se, že váš účet Azure je přiřazena role Přispěvatel virtuálních počítačů, tak, že máte oprávnění pro:

    - Vytvoření virtuálního počítače ve vybrané skupině prostředků
    - Vytvoření virtuálního počítače ve vybrané virtuální síti
    - Zapsat do služby Azure spravovaného disku. 

3. [Nastavit síť Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Počítače se replikují do Azure v místním spravované disky. Při převzetí služeb při selhání do Azure pro migraci, virtuální počítače Azure vytvořené z těchto spravovaných disků a připojený k síti můžete zadat při nastavení migrace.


## <a name="prepare-azure"></a>Příprava Azure

Pokud jste již spustili posouzení pomocí Azure Migrate Server Assessment, můžete přeskočit pokyny v této části vzhledem k tomu, že už jste dokončili postup. 

Pokud jste nespustili posouzení, budete muset nastavit oprávnění Azure předtím, než je možné migrovat pomocí migrace serveru migrace Azure.

- **Vytvoření projektu**: Váš účet Azure potřebuje oprávnění k vytvoření projektu Azure Migrate. 
- **Registrace zařízení Azure Migrate replikace**: Zařízení replikace vytváří a registruje aplikaci pro Azure Active Directory ve svém účtu Azure. Je nutné delegovat oprávnění pro tento.
- **Vytvoření služby Key Vault**: Migrace virtuálních počítačů VMware pomocí Azure Migrate serveru migrace, Azure Migrate vytvoří trezor klíčů ve skupině prostředků, ke správě přístupových klíčů k účtu úložiště replikace v rámci vašeho předplatného. Vytvoření trezoru, budete potřebovat oprávnění k přiřazení role u skupiny prostředků, ve kterém je umístěn projekt Azure Migrate. 


### <a name="assign-permissions-to-create-project"></a>Přidělování oprávnění k vytvoření projektu

1. Na webu Azure Portal, otevřete předplatné a vyberte **řízení přístupu (IAM)** .
2. V **zkontrolovat přístup**vyhledejte příslušný účet a klikněte na něj chcete-li zobrazit oprávnění.
3. Měli byste mít **Přispěvatel** nebo **vlastníka** oprávnění.
    - Pokud jste právě vytvořili bezplatný účet Azure, vlastník předplatného.
    - Pokud si nejste vlastník předplatného, pracujete s vlastníkem přiřazení role.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Přiřadit oprávnění k registraci zařízení replikace

Migrace na základě agenta delegování oprávnění pro Azure migrace serveru migrace k vytvoření a registrace aplikace Azure AD ve vašem účtu. Můžete přiřadit oprávnění pomocí jedné z následujících metod:

- Tenanta nebo globální správce může udělit oprávnění uživatelům v tenantovi, k vytvoření a registrace aplikace Azure AD.
- Tenanta nebo globální správce může k účtu přiřadit roli vývojář aplikace, (který má oprávnění).

Je vhodné poznamenat, že:

- Aplikace nemají žádné jiné oprávnění přístupu na předplatné, než je popsaný výše.
- Pouze potřebujete tato oprávnění při registraci nového zařízení replikace. Po nastavení replikace zařízení můžete odebrat oprávnění. 


#### <a name="grant-account-permissions"></a>Udělte účtu oprávnění

Tenanta nebo globální správce může udělit oprávnění následujícím způsobem

1. Ve službě Azure AD, by měl tenanta nebo globální správce přejděte na **Azure Active Directory** > **uživatelé** > **uživatelská nastavení**.
2. Správce by měl nastavit **registrace aplikací** k **Ano**.

    ![Oprávnění Azure AD](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Toto je výchozí nastavení, která nejsou citlivá. [Další informace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Přiřazení role aplikace pro vývojáře 

Tenanta nebo globální správce může přiřadit roli vývojář aplikace k účtu. [Další informace](https://docs.microsoft.comazure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

## <a name="assign-permissions-to-create-key-vault"></a>Přidělování oprávnění k vytvoření služby Key Vault

Přiřadíte oprávnění k přiřazení role u skupiny prostředků, ve kterém projektu Azure Migrate nachází, následujícím způsobem:

1. Ve skupině prostředků na webu Azure Portal, vyberte **řízení přístupu (IAM)** .
2. V **zkontrolovat přístup**vyhledejte příslušný účet a klikněte na něj chcete-li zobrazit oprávnění. Potřebujete **vlastníka** (nebo **Přispěvatel** a **správce uživatelských přístupů**) oprávnění.
3. Pokud nemáte potřebná oprávnění, o ně požádat vlastníka skupiny prostředků. 


## <a name="prepare-on-premises-vmware"></a>Příprava VMware v místním prostředí

### <a name="prepare-an-account-for-automatic-discovery"></a>Příprava účtu pro automatické zjišťování

Migrace serveru do Azure migrovat potřebuje přístup k serverům VMware:

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

- Replikace zařízení Azure Migrate můžete dělat nabízené instalace z této služby, když povolíte replikaci pro počítač, nebo je můžete nainstalovat ručně, nebo pomocí nástroje instalace.
- V tomto kurzu provedeme instalaci služby Mobility pomocí nabízené instalace.
- Pro nabízenou instalaci musíte připravit účet, který Azure Migrate migrace serveru můžete použít pro přístup k virtuálnímu počítači.

Připravte účet následujícím způsobem:

1. Připravte účet domény nebo místní účet s oprávněními k instalaci na virtuální počítač.
2. Pro virtuální počítače s Windows, pokud nepoužíváte doménový účet, zakažte vzdálené řízení přístupu uživatele na místním počítači tak, že přidáte položku DWORD **LocalAccountTokenFilterPolicy**, s hodnotou v registru, v části **HKEY_ LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**
3. Pro virtuální počítače s Linuxem připravte na zdrojovém serveru s Linuxem kořenový účet.


### <a name="check-vmware-requirements"></a>Kontrola požadavků na VMware

Ujistěte se, že servery VMware a virtuálních počítačů v souladu s požadavky pro migraci do Azure. 


> [!NOTE]
> Migrace na základě agenta s Azure Migrate migrace serveru vychází z funkcí služby Azure Site Recovery. Dokumentace ke službě Site Recovery může propojit některé požadavky.

1. [Ověřte](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) požadavky na servery VMware.
2. [Ověřte](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) virtuálního počítače podporují požadavky pro migraci.
3. Ověřte nastavení virtuálního počítače. Místní virtuální počítače replikovat do Azure musí být v souladu s [požadavky na virtuální počítač Azure](migrate-support-matrix-vmware.md#azure-vm-requirements).



## <a name="add-the-azure-migrate-server-migration-tool"></a>Přidat nástroj pro migraci serveru migrace Azure

Pokud nebyl postupovat podle kurzu a posouzení virtuálních počítačů VMware, nastavte projekt Azure Migrate a pak přidat nástroj pro migraci serveru migrace Azure:

1. Na webu Azure Portal > **všechny služby**, vyhledejte **Azure Migrate**.
2. V části **služby**vyberte **Azure Migrate**.

    ![Nastavte si Azure Migrate](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. V **přehled**, klikněte na tlačítko **posoudit a migrovat servery**.
4. V části **zjišťování, vyhodnocení a migrace serverů**, klikněte na tlačítko **posouzení a migraci serverů**.

    ![Zkoumání a posouzení servery](./media/tutorial-migrate-vmware-agent/assess-migrate.png)

1. V **zjišťování, vyhodnocení a migrace serverů**, klikněte na tlačítko **přidat nástroje**.
2. V **migrace projektu**, vyberte své předplatné Azure a pokud ho nemáte, vytvořte skupinu prostředků.
3. V **Project Details**, zadejte název projektu a zeměpisné oblasti, ve kterém chcete vytvořit projekt a klikněte na tlačítko **další**

    ![Vytvoření projektu Azure Migrate](./media/tutorial-migrate-vmware-agent/migrate-project.png)

    Projekt Azure Migrate můžete vytvořit v některém z těchto zeměpisných oblastech.

    **Zeměpisné oblasti** | **Oblast**
    --- | ---
    Asie | Jihovýchodní Asie
    Evropa | Severní Evropa a západní Evropa
    Spojené státy | USA – východ nebo USA (střed) – západ

    Zeměpisné umístění vybrané pro tento projekt slouží jen k uložení metadat získaných z místních virtuálních počítačů. Můžete vybrat libovolnou oblast cílového skutečné migrace.
4. V **nástroj pro vyhodnocení vyberte**vyberte **přeskočit přidávání nástroj pro vyhodnocení nyní** > **Další**.
5. V **nástroj pro migraci vybrat**vyberte **Azure Migrate: Migrace serveru** > **Další**.
6. V **zkontrolujte + přidat nástroje**, zkontrolujte nastavení a klikněte na tlačítko **přidat nástroje**
7. Po přidání nástroj, zobrazí se v projektu Azure Migrate > **servery** > **nástrojů pro migraci**.

## <a name="set-up-the-replication-appliance"></a>Nastavení replikace zařízení

Prvním krokem migrace je nastavení replikace zařízení. Zařízení replikace je jedinou s vysokou dostupností, místní virtuální počítač VMware, který je hostitelem tyto komponenty:

- **Konfigurační server**: Konfigurační server koordinuje komunikaci mezi místním prostředím a Azure a spravuje replikaci dat.
- **Procesový server:** Procesní server funguje jako replikační brána. Přijímá data replikace; optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do účtu úložiště mezipaměti v Azure. Procesní server nainstaluje agenta služby Mobility na virtuálních počítačích, které chcete replikovat, a automaticky vyhledá místní virtuální počítače VMware.


Pokud chcete nastavit zařízení replikace, je stáhněte připravenou šablonu otevřít virtualizace aplikace OVA (). Import šablony do VMware a vytvořit zařízení replikace virtuálního počítače. 

### <a name="download-the-replication-appliance-template"></a>Stáhněte si šablonu zařízení replikace

Stáhněte si šablonu následujícím způsobem:

1. V projektu Azure Migrate klikněte na tlačítko **servery** pod **cílů migrace**.
2. V **Azure Migrate – servery** > **Azure Migrate: Migrace serveru**, klikněte na tlačítko **Discover**.

    ![Vyhledání virtuálních počítačů](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. V **zjistit počítače** > **jsou vaše počítače virtualizované?** , klikněte na tlačítko **Ano, s VMWare vSphere hypervisor**.
4. V **jak chcete migrovat?** vyberte **pomocí replikace založené na agentovi**.
5. V **cílové oblasti**, vyberte oblast Azure, do kterého chcete migrovat počítače.
6. Vyberte **potvrdit, že cílová oblast pro migraci název oblasti**.
7. Klikněte na tlačítko **vytvářet prostředky**. Tím se vytvoří trezor služby Azure Site Recovery na pozadí.
    - Cílová oblast pro tento projekt nelze změnit po kliknutí na toto tlačítko.
    - Všechny následné migrace jsou do této oblasti.

    ![Vytvoření trezoru služby Recovery Services](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. V **chcete nainstalovat nové zařízení replikace?** vyberte **nainstalujte replikaci zařízení**.
9. Klikněte na tlačítko **Stáhnout**ke stažení zařízení replikace. Tato akce stáhne šablony OVF, můžete použít k vytvoření nového virtuálního počítače VMware, na kterém běží na zařízení.
    ![Stáhnout soubory OVA](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Poznamenejte si název skupiny prostředků a trezor služby Recovery Services. Budete je potřebovat při nasazování zařízení.


### <a name="import-the-template-in-vmware"></a>Import šablony do VMware

Po stažení šablony OVF, importovat do VMware. Tím vytvoříte aplikaci replikace na virtuálním počítači VMware s Windows serverem 2016.

1. Pomocí klientské konzoly VMware vSphere Client se přihlaste k VMware vCenter Serveru nebo hostiteli vSphere ESXi.
2. V nabídce **File** (Soubor) vyberte **Deploy OVF Template** (Nasadit šablonu OVF). Spustíte tak **průvodce nasazením šablony OVF**. 
3. V **výběr zdroje**, zadejte umístění staženého souboru OVF.
4. V **podrobnosti**vyberte **Další**.
5. V **vyberte název a složku** a **vyberte konfiguraci**, přijměte výchozí nastavení.
6. V **vyberte úložiště** > **Select virtual disk format**, nejlepší výkon **silný zřízení Eager Zeroed**.
7. Na zbývajících stránkách průvodce přijměte výchozí nastavení.
8. V **připraví k dokončení**, chcete-li vytvořit virtuální počítač s výchozím nastavením, vyberte **Power on po nasazení** > **Dokončit**.

   > [!TIP]
   > Pokud chcete přidat další síťové rozhraní, zrušte zaškrtnutí políčka **Power on after deployment** (Spustit po nasazení) > **Finish** (Dokončit). Ve výchozím nastavení obsahuje šablona jeden síťový adaptér. Po nasazení můžete přidat další síťové adaptéry.

### <a name="kick-off-replication-appliance-setup"></a>Pusťte se do zařízení nastavení replikace

1. Z klientské konzole VMware vSphere Client zapněte požadovaný virtuální počítač.
2. Virtuální počítač se spustí do instalačního prostředí Windows Serveru 2016. Přijměte licenční smlouvu a zadejte heslo správce.
3. Po dokončení instalace se přihlaste k virtuálnímu počítači jako správce s heslem správce.
4. Při prvním přihlášení se replikace zařízení instalační program nástroje (nástroj pro konfiguraci Azure Site Recovery) se spustí během několika sekund.
5. Zadejte název pro registraci zařízení s Azure Migrate serveru migrace. Pak klikněte na tlačítko **Další**.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání spojení vyberte **Přihlásit se**, abyste se mohli přihlásit ke svému předplatnému Azure.
7. Počkejte, až nástroj dokončí registraci aplikace Azure AD k identifikaci zařízení. Zařízení restartuje.
1. Znovu se přihlaste k počítači. Za chvilku se spustí automaticky průvodce správou konfiguračního serveru.

### <a name="register-the-replication-appliance"></a>Registrace zařízení replikace

Dokončení nastavení a registrace zařízení replikace.

1. Průvodce správou konfiguračního serveru, vyberte **nastavit připojení**.
2. Vyberte síťový adaptér (ve výchozím nastavení je pouze jednu síťovou kartu), replikace zařízení používá pro zjišťování virtuálních počítačů a provádět nabízenou instalaci služby Mobility na zdrojový počítače.
3. Vyberte síťový adaptér, který zařízení replikace používá pro připojení k Azure. Potom vyberte **Uložit**. Toto nastavení nelze změnit po dokončení konfigurace.
4. Pokud se zařízení nachází za proxy serverem, budete muset zadat nastavení proxy serveru.
    - Zadejte název proxy serveru jako **http://ip-address** , nebo **http://FQDN** . HTTPS proxy servery nejsou podporovány.
5. Po zobrazení výzvy pro dané předplatné, skupiny prostředků a podrobnosti trezoru, přidejte podrobnosti, které jste si poznamenali při jste stáhli šablonu zařízení.
6. V části **Nainstalovat software třetí strany** přijměte licenční smlouvu. Vyberte **Stáhnout a nainstalovat** a nainstalujte MySQL Server.
7. Vyberte **Nainstalovat VMware PowerCLI**. Než to uděláte, zkontrolujte, jestli jsou zavřená všechna okna prohlížeče. Potom vyberte **Pokračovat**.
8. Než budete pokračovat, v části **Ověřit konfiguraci zařízení** se ověří požadavky.
9. V části **Konfigurovat vCenter Server nebo server vSphere ESXi** zadejte plně kvalifikovaný název domény nebo IP adresu vCenter Serveru nebo hostitele vSphere, na kterém jsou umístěné virtuální počítače, které chcete replikovat. Zadejte port, na kterém server naslouchá. Zadejte popisný název, který se použije pro server VMware v trezoru.
10. Zadejte přihlašovací údaje pro účet vám [vytvořili](#prepare-an-account-for-automatic-discovery) zjišťování VMware. Vyberte **přidat** > **pokračovat**.
11. V **nakonfigurovat přihlašovací údaje virtuálního počítače**, zadejte přihlašovací údaje můžete [vytvořili](#prepare-an-account-for-mobility-service-installation) pro nabízenou instalaci služby Mobility, když povolíte replikaci pro virtuální počítače.  
    - Když chcete replikovat počítače s Windows, je nutné, aby na nich měl tento účet oprávnění místního správce.
    - U počítačů s Linuxem zadejte údaje superuživatele.
12. Vyberte **Dokončit konfiguraci** a dokončete registraci.


Po registraci zařízení replikace Azure Migrate Server Assessment připojí k serverům VMware pomocí zadaného nastavení a vyhledá virtuální počítače. Můžete zobrazit zjištěné virtuální počítače v **spravovat** > **zjištěných položek**v **jiných** kartu.


## <a name="replicate-vms"></a>Replikace virtuálních počítačů

1. V projektu Azure Migrate > **servery**, **Azure Migrate: Migrace serveru**, klikněte na tlačítko **replikovat**.

    ![Replikace virtuálních počítačů](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. V **replikovat**, > **nastavení zdroje** > **jsou vaše počítače virtualizované?** vyberte **Ano, s VMware vSphere**.
3. V **místní zařízení**, vyberte název zařízení, které jste nastavili Azure Migrate.
4. V **vCenter server**, zadejte název serveru vCenter, Správa virtuálních počítačů, nebo server vSphere, na kterém jsou hostované virtuální počítače.
5. V **procesový Server**, vyberte název zařízení replikace.
6. V **hosta pověření**, zadejte účet správce virtuálního počítače, který se použije pro nabízenou instalaci služby Mobility. Pak klikněte na tlačítko **Další: Virtuální počítače**.

    ![Replikace virtuálních počítačů](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. V **virtuálních počítačů**, vyberte počítače, které chcete replikovat.

    - Pokud spouštíte posouzení virtuálních počítačů můžete použít velikosti virtuálních počítačů a doporučení ohledně typu (premium nebo standard) disku z výsledků posouzení. Chcete-li to provést, v **importovat nastavení migrace z posouzení služby Azure Migrate?** , vyberte **Ano** možnost.
    - Pokud jste nespustili posouzení, nebo nechcete používat nastavení posouzení, vyberte **ne** možnosti.
    - Pokud jste se rozhodli použít posouzení, vyberte skupiny virtuálních počítačů a název posouzení.

8. Zkontrolujte jednotlivé virtuální počítače, které chcete migrovat. Pak klikněte na tlačítko **Další: Cílová nastavení**.
9. V **cílit na nastavení**, vyberte předplatné a cílit na oblast, do kterého budete migrovat a zadejte skupinu prostředků, ve kterém budou umístěné virtuální počítače Azure po migraci.
10. V **virtuální sítě**, vyberte Azure virtuální sítě nebo podsítě do které budou připojeny virtuální počítače Azure po migraci.
11. V **zvýhodněné hybridní využití Azure**:

    - Vyberte **ne** Pokud nechcete použít zvýhodněné hybridní využití Azure. Pak klikněte na tlačítko **Další**.
    - Vyberte **Ano** Pokud máte počítače s Windows serverem, které se vztahuje aktivní Software Assurance nebo smlouvu Services systému Windows Server odběry a chcete použít tuto výhodu pro počítače, kterou migrujete. Pak klikněte na tlačítko **Další**.

12. V **Compute**, zkontrolujte název virtuálního počítače, velikost, typ disku operačního systému a dostupnosti. Virtuální počítače musí splňovat [požadavky služby Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Velikost virtuálního počítače**: Pokud používáte doporučení pro interní hodnocení, rozevírací seznam velikostí virtuálních počítačů bude obsahovat doporučenou velikost. V opačném případě Azure Migrate použije velikost podle nejvíce odpovídá v rámci předplatného Azure. Můžete také vybrat ruční velikost v **velikost virtuálního počítače Azure**. 
    - **Disk s operačním systémem**: Zadejte disk s operačním systémem (spouštěcí) pro virtuální počítač. Disk s operačním systémem je disk, který obsahuje spouštěcí zavaděč operačního systému a instalační služby. 
    - **Skupina dostupnosti**: Pokud virtuální počítač by měl být ve skupině dostupnosti Azure po migraci, určete sadu. Sada musí být v cílové skupině prostředků, kterou zadáte pro migraci.

13. V **disky**, určete, jestli disky virtuálního počítače by měla být replikována do Azure a vyberte typ disku (SSD nebo pevný disk nebo premium spravované disky úrovně standard) v Azure. Pak klikněte na tlačítko **Další**.
    - Vyloučení disků z replikace.
    - Pokud se můžete vyloučit disky, nemusí být na virtuálním počítači Azure po migraci. 

14. V **zkontrolovat a zahájit replikaci**, zkontrolujte nastavení a klikněte na tlačítko **replikovat** má spustit počáteční replikace pro servery.

> [!NOTE]
> Můžete aktualizovat nastavení replikace kdykoli před zahájením replikace **spravovat** > **replikaci počítačů**. Nastavení nelze změnit, jakmile replikace spustí.




## <a name="track-and-monitor"></a>Sledování a monitorování

- Po kliknutí na **replikovat** začne úloha spuštění replikace. 
- Když úloha spuštění replikace skončí úspěšně, počítačích začít jejich počáteční replikace do Azure.
- Po dokončení počáteční replikace začne rozdílová replikace. Přírůstkové změny, které s místními disky se replikují pravidelně repliky disků v Azure.


Můžete sledovat stav úlohy v oznámeních portálu.

![Sledování úlohy](./media/tutorial-migrate-vmware-agent/jobs.png)

Stav replikace můžete sledovat kliknutím na **replikaci serverů** v **Azure Migrate: Migrace serveru**.
![Monitorování replikace](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

## <a name="run-a-test-migration"></a>Spuštění testu migrace


Po zahájení rozdílovou replikaci pro virtuální počítače, můžete spustit testovací migrace. před spuštěním úplné migrace do Azure. Důrazně doporučujeme to provést nejméně jednou pro každý počítač, před zahájením migrace.

- Spuštění testu migrace kontroly, které migrace budou fungovat podle očekávání, aniž by to ovlivnilo místních počítačů, které zůstat funkční a pokračovat v replikaci. 
- Migrace test simuluje migrace tím, že vytvoříte virtuální počítač Azure pomocí replikovaných dat (obvykle migrace na virtuální síť li se o neprodukční ve vašem předplatném Azure).
- Můžete ověřit migraci, proveďte testování aplikací pomocí replikovaných testovací virtuální počítač Azure a řešit případné problémy dříve než úplné migrace.

Testovací migrace postupujte následovně:


1. V **cílů migrace** > **servery** > **Azure Migrate: Migrace serveru**, klikněte na tlačítko **Test migrovat servery**.

     ![Testování migrované servery](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Klikněte pravým tlačítkem na virtuální počítač otestovat, a klikněte na **testovací migrace**.

    ![Testovací migrace.](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. V **testovací migrace**, vyberte virtuální síť Azure, ve kterém virtuální počítač Azure se umístí po migraci. Doporučujeme, že používáte-li se o neprodukční virtuální sítě.
4. **Testovací migrace** úlohy spustí. Monitorování úlohy v oznámeních portálu.
5. Po dokončení migrace se zobrazit migrovaný virtuální počítač Azure v **virtuálních počítačů** na webu Azure Portal. Název počítače má příponu **-Test**.
6. Po dokončení testu, klikněte pravým tlačítkem na virtuální počítač Azure v **replikaci počítačů**a klikněte na tlačítko **vyčistěte testovací migrace**.

    ![Vyčištění migrace](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Migrace virtuálních počítačů

Po ověření, že testovací migrace funguje podle očekávání, můžete migrovat na místních počítačích.

1. V projektu Azure Migrate > **servery** > **Azure Migrate: Migrace serveru**, klikněte na tlačítko **replikaci serverů**.

    ![Replikaci serverů](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. V **replikaci počítačů**, klikněte pravým tlačítkem na virtuální počítač > **migrace**.
3. V **migrace** > **vypnout virtuální počítače a provádět při plánované migraci bez ztráty**vyberte **Ano** > **OK** .
    - Ve výchozím nastavení Azure Migrate místní virtuální počítač vypne a spustí replikaci na vyžádání synchronizovat změny virtuálního počítače, ke kterým došlo od poslední replikace. Tím se zajistí bez ztráty.
    - Pokud nechcete vypnout virtuální počítač, vyberte **ne**
4. Spustí úlohu migrace pro virtuální počítač. Sledování úlohy v Azure oznámení.
5. Po dokončení úlohy můžete zobrazit a spravovat virtuální počítač z **virtuálních počítačů** stránky.

## <a name="complete-the-migration"></a>Dokončení migrace

1. Poté, co migrace dokončí, klikněte pravým tlačítkem na virtuální počítač > **zastavit migraci**. Zastaví se replikace místních počítačů a vyčistí informace o stavu replikace pro virtuální počítač.
2. Nainstalujte virtuální počítač Azure [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) nebo [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) agenta na migrované počítače.
3. Proveďte všechna vylepšení aplikace po migraci, například aktualizujte databázové připojovací řetězce a nakonfigurujte webové servery.
4. U migrované aplikace, která teď běží v Azure, proveďte finální akceptační testování aplikace a migrace.
5. Vyjmete provozu na migrované instance virtuálního počítače Azure.
6. Odeberte místní virtuální počítače z místního inventáře virtuálních počítačů.
7. Odeberte místní virtuální počítače ze záloh.
8. Aktualizujte veškerou interní dokumentaci tak, aby obsahovala nová umístění a IP adresy virtuálních počítačů Azure. 

## <a name="post-migration-best-practices"></a>Osvědčené postupy po migraci

- Pro zvýšení odolnosti:
    - Zálohujte virtuální počítače Azure pomocí služby Azure Backup, abyste měli data zabezpečená. [Další informace](../backup/quick-backup-vm-portal.md).
    - Replikujte virtuální počítače Azure do sekundární oblasti pomocí služby Site Recovery, aby úlohy mohly neustále běžet a byly dostupné. [Další informace](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Pro zvýšení zabezpečení:
    - Zamknout a omezit příchozí provoz přístup s [Azure Security Center – Správa Just in time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Omezte síťový provoz na koncové body správy pomocí [skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Nasaďte službu [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview), která vám pomůže zabezpečit disky a zajistit bezpečnost dat před krádeží a neoprávněným přístupem.
    - Přečtěte si další informace o [zabezpečení prostředků IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) a navštivte [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Pro monitorování a správu:
-  Zvažte nasazení služby [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview), která bude monitorovat využití prostředků a útratu.


## <a name="next-steps"></a>Další kroky

Prozkoumat [cesty k migraci do cloudu](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) v rámci přechodu na Cloud Azure.
