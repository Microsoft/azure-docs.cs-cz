---
title: Posouzení virtuálních počítačů VMware pro migraci do Azure pomocí Azure Migrate
description: Popisuje, jak vyhodnotit místní virtuální počítače VMware pro migraci do Azure pomocí Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/18/2019
ms.author: hamusa
ms.openlocfilehash: 7187cb41fa7ea499035c57e83e04038b1269b418
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158670"
---
# <a name="assess-vmware-vms-with-azure-migrate-server-assessment"></a>Posouzení virtuálních počítačů VMware pomocí Azure Migrate: posouzení serveru

V tomto článku se dozvíte, jak pomocí nástroje Azure Migrate: Server Assessment Tool zhodnotit místní virtuální počítače VMware.

[Azure Migrate](migrate-services-overview.md) poskytuje centrum nástrojů, které vám pomůžou zjišťovat, vyhodnocovat a migrovat aplikace, infrastrukturu a úlohy do Microsoft Azure. Centrum zahrnuje nástroje pro Azure Migrate a nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců.



Tento kurz je druhý v řadě, který ukazuje, jak vyhodnotit a migrovat virtuální počítače VMware do Azure. V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavte Azure Migrate projekt.
> * Nastavte zařízení Azure Migrate, která se místně spustí pro vyhodnocení virtuálních počítačů.
> * Spusťte nepřetržité zjišťování místních virtuálních počítačů. Zařízení odesílá údaje o konfiguraci a výkonu pro zjištěné virtuální počítače do Azure.
> * Seskupit zjištěné virtuální počítače a vyhodnotit skupinu virtuálních počítačů.
> * Projděte si posouzení.



> [!NOTE]
> Kurzy vám ukážou nejjednodušší cestu nasazení pro scénář, abyste mohli rychle nastavit zkušební verzi. Kurzy používají výchozí možnosti, pokud je to možné, a nezobrazují všechna možná nastavení a cesty. Podrobné pokyny najdete v článcích s postupy.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.


## <a name="prerequisites"></a>Požadavky

- [Dokončete](tutorial-prepare-vmware.md) první kurz v této sérii. Pokud to neuděláte, pokyny v tomto kurzu nebudou fungovat.
- Tady je seznam toho, co byste měli udělat v prvním kurzu:
    - [Nastavte oprávnění Azure](tutorial-prepare-vmware.md#prepare-azure) pro Azure Migrate.
    - [Připravte VMware](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) na posouzení. Měli byste ověřit nastavení VMware a měli byste mít oprávnění k vytvoření virtuálního počítače VMware se šablonou vajíček. Měli byste mít také nastavený účet pro zjišťování virtuálních počítačů. Požadované porty by měly být k dispozici a měli byste si uvědomit o adresách URL potřebných pro přístup k Azure.


## <a name="set-up-an-azure-migrate-project"></a>Nastavení Azure Migrateho projektu

Následujícím způsobem nastavte nový projekt Azure Migrate.

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
2. V části **Služby** vyberte **Azure Migrate**.
3. Na stránce **Přehled** v části **Zjistit, posoudit a migrovat servery** klikněte na **Posoudit a migrovat servery**.

    ![Zjišťování a vyhodnocení serverů](./media/tutorial-assess-vmware/assess-migrate.png)

4. V části **Začínáme** klikněte na **Přidat nástroje**.
5. V části **Projekt migrace** vyberte své předplatné Azure a vytvořte skupinu prostředků, pokud ji ještě nemáte.     
6. V části **Project Details (podrobnosti projektu**) zadejte název projektu a zeměpisnou oblast, ve které chcete vytvořit projekt. Podporují se Asie, Evropa, Spojené království a USA.

    - Zeměpisná oblast projektu slouží pouze k ukládání metadat shromážděných z místních virtuálních počítačů.
    - Při spouštění migrace můžete vybrat jakoukoli cílovou oblast.

    ![Vytvoření projektu Azure Migrate](./media/tutorial-assess-vmware/migrate-project.png)


7. Klikněte na **Další**.
8. V **nástroji vybrat nástroj pro posouzení**vyberte **Azure Migrate: posouzení serveru** > **Další**.

    ![Vytvoření projektu Azure Migrate](./media/tutorial-assess-vmware/assessment-tool.png)

9. V části **Vybrat nástroj pro migraci** vyberte **V tuto chvíli přeskočit přidání nástroje pro migraci** > **Další**.
10. V části **Kontrola a přidání nástrojů** zkontrolujte nastavení a klikněte na **Přidat nástroje**.
11. Počkejte několik minut, než se projekt Azure Migrate nasadí. Budete přesměrováni na stránku projektu. Pokud se projekt nezobrazí, můžete k němu přejít z části **Servery** na řídicím panelu služby Azure Migrate.


## <a name="set-up-the-appliance-vm"></a>Nastavení virtuálního počítače zařízení

Azure Migrate: posouzení serveru spouští odlehčené zařízení VMware VM.

- Toto zařízení provádí zjišťování virtuálních počítačů a odesílá údaje o metadatech a výkonu virtuálních počítačů do Azure Migrate posouzení serveru.
- Nastavení zařízení:
    - Stáhněte soubor šablony vajíček a naimportujte ho do vCenter Server.
    - Vytvořte zařízení a ověřte, že se může připojit k Azure Migrate posouzení serveru.
    - Nakonfigurujete zařízení poprvé a zaregistrujete ho do projektu Azure Migrate.
- Pro jeden Azure Migrate projekt můžete nastavit více zařízení. U všech zařízení se podporuje zjišťování až 35 000 virtuálních počítačů. Pro každé zařízení je možné zjistit maximálně 10 000 serverů.

### <a name="download-the-ova-template"></a>Stažení šablony pro VAJÍČKy

1. V ** > ** **cíli migrace** > **Azure Migrate: posouzení serveru**, klikněte na **zjistit**.
2. V části **Zjistit počítače** > **Máte počítače ve virtuální podobě?** klikněte na **Ano, s hypervisorem VMware vSphere**.
3. Pokud si chcete stáhnout soubor šablony .OVA, klikněte na **Stáhnout**.

    ![Stažení souboru .OVA](./media/tutorial-assess-vmware/download-ova.png)


### <a name="verify-security"></a>Ověřit zabezpečení

Před nasazením ověřte, zda je soubor sady vajíček zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz, kterým vygenerujete hodnotu hash pro soubor OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklady použití: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Pro 2.19.07.30 verze by se měla vygenerovaná hodnota hash shodovat s těmito hodnotami.

  **Algoritmus** | **Hodnota hash**
  --- | ---
  MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
  SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c


### <a name="create-the-appliance-vm"></a>Vytvoření virtuálního počítače zařízení

Naimportujte stažený soubor a vytvořte virtuální počítač.

1. V konzoli vSphere Client klikněte na **File** (Soubor) > **Deploy OVF Template** (Nasadit šablonu OVF).

    ![Nasazení šablony OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. V Průvodci nasazením šablony OVF > **zdroj**zadejte umístění souboru vajíček.
3. Do pole **název** a **umístění**zadejte popisný název virtuálního počítače. Vyberte objekt inventáře, do kterého bude virtuální počítač hostovat.
5. V části **hostitel nebo cluster**zadejte hostitele nebo cluster, na kterém se virtuální počítač spustí.
6. V části **Storage (úložiště**) zadejte cíl úložiště pro virtuální počítač.
7. V části **Disk Format** (Formát disku) zadejte typ a velikost disku.
8. V části **mapování sítě**určete síť, ke které se bude virtuální počítač připojovat. Síť potřebuje připojení k Internetu, aby odesílala metadata Azure Migrate posouzení serveru.
9. Zkontrolujte a ověřte všechna nastavení a pak klikněte na **Finish** (Dokončit).


### <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

Ujistěte se, že se virtuální počítač zařízení může připojit k [adresám URL Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


### <a name="configure-the-appliance"></a>Konfigurace zařízení

Zařízení nastavte pomocí následujících kroků.

1. V konzoli vSphere Client klikněte pravým tlačítkem na daný virtuální počítač a pak na **Open Console** (Otevřít konzolu).
2. Zadejte jazyk, časové pásmo a heslo pro zařízení.
3. Otevřete prohlížeč na jakémkoli počítači, který se může připojit k VIRTUÁLNÍmu počítači, a otevřete adresu URL webové aplikace zařízení: ***název zařízení https://nebo IP adresa*: 44368**.

   Alternativně můžete aplikaci otevřít z plochy zařízení kliknutím na zástupce aplikace.
4. Ve webové aplikaci > **nastavení požadavků**postupujte takto:
    - **Licence**: přijměte licenční podmínky a přečtěte si informace třetích stran.
    - **Připojení**: aplikace kontroluje, jestli má virtuální počítač přístup k Internetu. Pokud virtuální počítač používá proxy server:
        - Klikněte na **nastavení proxy serveru**a zadejte adresu proxy serveru a port naslouchání ve formuláři http://ProxyIPAddress nebo http://ProxyFQDN.
        - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
        - Podporuje se jen proxy protokolu HTTP.
    - **Čas synchronizace**: čas v zařízení by měl být synchronizovaný s internetovým časem, aby zjišťování fungovalo správně.
    - **Nainstalovat aktualizace**: zařízení zajišťuje, že jsou nainstalované nejnovější aktualizace.
    - **Instalace VDDK**: zařízení kontroluje, jestli je nainstalovaná sada Virtual disks Development Kit (VDDK) VMware vSphere.
        - Azure Migrate: Migrace serveru používá VDDK k replikaci počítačů během migrace do Azure.
        - Stáhněte si VDDK 6,7 z VMware a Extrahujte stažený obsah zip do zadaného umístění na zařízení.

### <a name="register-the-appliance-with-azure-migrate"></a>Zaregistrovat zařízení ve Azure Migrate

1. Klikněte na **Přihlásit se**. Pokud se nezobrazí, ujistěte se, že jste v prohlížeči zakázali blokování automaticky otevíraných oken.
2. Na nové kartě se přihlaste pomocí svých přihlašovacích údajů Azure.
    - Přihlaste se pomocí svého uživatelského jména a hesla.
    - Přihlášení pomocí PIN kódu se nepodporuje.
3. Po úspěšném přihlášení se vraťte k webové aplikaci.
2. Vyberte předplatné, ve kterém byl vytvořen Azure Migrate projekt, a pak vyberte projekt.
3. Zadejte název zařízení. Název by měl být alfanumerický a nesmí obsahovat více než 14 znaků.
4. Klikněte na **zaregistrovat**.

## <a name="start-continuous-discovery"></a>Spustit průběžné zjišťování

Aby bylo možné zjistit konfiguraci a údaje o výkonu virtuálních počítačů, musí se zařízení připojit k vCenter Server.

### <a name="specify-vcenter-server-details"></a>Zadání podrobností vCenter Serveru
1. V části **zadat vCenter Server podrobnosti**zadejte název (FQDN) nebo IP adresu vCenter Server. Můžete ponechat výchozí port nebo zadat vlastní port, na kterém vCenter Server naslouchá.
2. V části **uživatelské jméno** a **heslo**zadejte přihlašovací údaje účtu jen pro čtení, které zařízení použije ke zjištění virtuálních počítačů na serveru vCenter. Ujistěte se, že účet má [požadovaná oprávnění pro zjišťování](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions). Rozsah zjišťování můžete omezit tak, že omezíte přístup k účtu vCenter odpovídajícím způsobem; Další informace o zjišťování oboru [najdete tady](tutorial-assess-vmware.md#scoping-discovery).
3. Klikněte na **ověřit připojení** a ujistěte se, že se zařízení může připojit k vCenter Server.

### <a name="specify-vm-credentials"></a>Zadat přihlašovací údaje virtuálního počítače
Pro zjišťování aplikací, rolí a funkcí a vizualizací závislostí virtuálních počítačů můžete zadat přihlašovací údaje k virtuálnímu počítači, které mají přístup k virtuálním počítačům VMware. Můžete přidat jednu přihlašovací údaje pro virtuální počítače s Windows a jedno přihlašovací údaje pro virtuální počítače se systémem Linux. [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions) o požadovaných oprávněních pro přístup.

> [!NOTE]
> Tento vstup je nepovinný a je potřeba, aby se povolilo zjišťování aplikací a vizualizace závislostí bez agentů.

1. V nabídce **zjistit aplikace a závislosti na virtuálních počítačích**klikněte na **Přidat přihlašovací údaje**.
2. Vyberte **operační systém**.
3. Zadejte popisný název přihlašovacích údajů.
4. V části **uživatelské jméno** a **heslo**zadejte účet, který má alespoň přístup k hostům na virtuálních počítačích.
5. Klikněte na **Přidat**.

Po zadání vCenter Server a přihlašovacích údajů k virtuálnímu počítači (volitelné) klikněte na **Uložit a spusťte zjišťování** a spusťte zjišťování místního prostředí.

Zobrazení metadat zjištěných virtuálních počítačů na portálu trvá přibližně 15 minut. Zjišťování nainstalovaných aplikací, rolí a funkcí nějakou dobu trvá, doba trvání závisí na počtu zjištěných virtuálních počítačů. Pro virtuální počítače 500 trvá na portálu Azure Migrate přibližně 1 hodinu, než se inventář aplikace zobrazí.

### <a name="scoping-discovery"></a>Zjišťování rozsahu

Zjišťování může být vymezeno omezením přístupu k účtu vCenter použitému pro zjišťování. Obor můžete nastavit tak, aby vCenter Server datová centra, clustery, složku clusterů, hostitele, složku hostitelů nebo jednotlivé virtuální počítače.

Chcete-li nastavit obor, je nutné provést následující kroky:
1.  Vytvořte uživatelský účet vCenter.
2.  Definujte novou roli s požadovanými oprávněními. (<em>vyžaduje se pro migraci serveru bez agenta</em>)
3.  Přiřaďte oprávnění k uživatelskému účtu na vCenter objektech.

**Vytvoření uživatelského účtu vCenter**
1.  Přihlaste se k webovému klientovi vSphere jako správce vCenter Server.
2.  Klikněte na možnost **správa** > **Uživatelé a skupiny jednotného přihlašování** > **Uživatelé** .
3.  Klikněte na ikonu **nového uživatele** .
4.  Zadejte požadované informace pro vytvoření nového uživatele a klikněte na tlačítko **OK**.

**Definování nové role s požadovanými oprávněními** (<em>vyžadované pro migraci serveru bez agenta</em>)
1.  Přihlaste se k webovému klientovi vSphere jako správce vCenter Server.
2.  Přejděte do **administrace** > **správce rolí**.
3.  Z rozevírací nabídky vyberte svou vCenter Server.
4.  Klikněte na akce **vytvořit roli** .
5.  Zadejte název nové role. (například <em>Azure_Migrate</em>).
6.  Přiřaďte tato [oprávnění](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-vcenter-server-permissions) k nově definované roli.
7.  Klikněte na tlačítko **OK**.

**Přiřazení oprávnění pro objekty vCenter**

Existují dva přístupy k přiřazení oprávnění k objektům inventáře v vCenter k uživatelskému účtu vCenter s přiřazenou rolí.
- Pro vyhodnocování serveru je třeba použít roli jen **pro čtení** pro uživatelský účet vCenter pro všechny nadřazené objekty, které jsou hostovány virtuálními počítači, které mají být zjištěny. Do datového centra se mají zahrnout všechny nadřazené objekty – hostitel, složka hostitelů, cluster, složka clusterů v hierarchii. Tato oprávnění mají být šířena do podřízených objektů v hierarchii.

    Podobně jako u migrace serveru může být uživatelsky definovaná role (s názvem <em>Azure _Migrate</em>), které mají přiřazená tato [oprávnění](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-vcenter-server-permissions) , použita u všech nadřazených objektů, u kterých jsou virtuální počítače, které mají být migrovány, hostovány na uživatelském účtu vCenter.

![Přiřazení oprávnění](./media/tutorial-assess-vmware/assign-perms.png)

- Alternativním přístupem je přiřadit uživatelský účet a roli na úrovni datového centra a rozšířit je na podřízené objekty. Pak mu udělte účet **bez role přístupu** pro každý objekt (například virtuální počítače), který nechcete zjišťovat nebo migrovat. Tato konfigurace je nenáročný. Zpřístupňuje nechtěné řízení přístupu, protože každému novému podřízenému objektu je také automaticky udělen přístup, který je zděděný z nadřazené položky. Proto doporučujeme použít první přístup.

> [!NOTE]
> Dnes, posouzení serveru nemůže zjistit virtuální počítače, pokud má účet vCenter udělen přístup na úrovni složky virtuálního počítače vCenter. Pokud chcete určit obor zjišťování pomocí složek virtuálních počítačů, můžete to provést tak, že účet vCenter má přiřazený přístup jen pro čtení na úrovni virtuálního počítače.  Postup najdete v následujících pokynech:
>
> 1. Přiřaďte oprávnění jen pro čtení na všech virtuálních počítačích ve složkách virtuálních počítačů, na které chcete zjišťování oborovat.
> 2. Udělte přístup jen pro čtení ke všem nadřazeným objektům, kde jsou hostované virtuální počítače. Všechny nadřazené objekty – hostitel, složka hostitelů, cluster, složka clusterů – v hierarchii až do datového centra se mají zahrnout. Nemusíte šířit oprávnění ke všem podřízeným objektům.
> 3. Použijte přihlašovací údaje pro zjišťování pro výběr datového centra jako *oboru shromažďování*. Nastavení RBAC zajišťuje, že odpovídající uživatel vCenter bude mít přístup jenom k virtuálním počítačům specifickým pro tenanta.
>
> Všimněte si, že je podporovaná složka hostitelů a clusterů.

### <a name="verify-vms-in-the-portal"></a>Kontrola virtuálních počítačů na portálu

Po zjištění můžete ověřit, že se virtuální počítače objeví v Azure Portal.

1. Otevřete řídicí panel Azure Migrate.
2. V **Azure Migrate-servery** > **Azure Migrate: na stránce posouzení serveru** klikněte na ikonu, která zobrazuje počet **zjištěných serverů**.

## <a name="set-up-an-assessment"></a>Nastavení posouzení

Existují dva typy hodnocení, které můžete vytvořit pomocí Azure Migrate: posouzení serveru.

**Posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Posouzení na základě shromážděných dat o výkonu | **Doporučená velikost virtuálního počítače**: na základě dat využití procesoru a paměti.<br/><br/> **Doporučený typ disku (spravovaný disk Standard nebo Premium)** : na základě vstupně-výstupních operací a propustnosti místních disků.
**Jako místní** | Posouzení na základě místních velikostí. | **Doporučená velikost virtuálního počítače**: na základě velikosti místního virtuálního počítače<br/><br> **Doporučený typ disku**: na základě nastavení typu úložiště, které jste vybrali pro posouzení.


### <a name="run-an-assessment"></a>Spuštění posouzení

Proveďte posouzení následujícím způsobem:

1. Projděte si [osvědčené postupy](best-practices-assessment.md) pro vytváření hodnocení.
2. Na kartě **servery** na dlaždici **Azure Migrate: vyhodnocování serveru** klikněte na možnost **vyhodnotit**.

    ![Posouzení](./media/tutorial-assess-vmware/assess.png)

2. V poli **vyhodnotit servery**zadejte název posouzení.
3. Kliknutím na **Zobrazit vše** zobrazíte vlastnosti posouzení.

    ![Vlastnosti posouzení](./media/tutorial-assess-vmware/view-all.png)

3. V **Vyberte nebo vytvořte skupinu**vyberte **vytvořit novou**a zadejte název skupiny. Skupina shromažďuje jeden nebo více virtuálních počítačů dohromady pro posouzení.
4. V části **přidat počítače do skupiny**vyberte virtuální počítače, které chcete do skupiny přidat.
5. Kliknutím na **vytvořit posouzení** vytvořte skupinu a spusťte posouzení.

    ![Vytvoření posouzení](./media/tutorial-assess-vmware/assessment-create.png)

6. Po vytvoření posouzení ho zobrazte na stránce **servery** > **Azure Migrate: posouzení serveru** > **posouzení**.
7. Klikněte na **Exportovat posouzení** a stáhněte ho jako excelový soubor.



## <a name="review-an-assessment"></a>Kontrola posouzení

Posouzení popisuje:

- **Připravenost na Azure**: jestli jsou virtuální počítače vhodné pro migraci do Azure.
- **Odhad měsíčních nákladů**: Odhadované měsíční náklady na výpočetní prostředky a úložiště pro provoz virtuálních počítačů v Azure.
- **Odhad měsíčních nákladů na úložiště**: Odhadované náklady na diskové úložiště po migraci.

### <a name="view-an-assessment"></a>Zobrazit posouzení

1. V ** >  ** **cíli migrace** klikněte na **posouzení** v **Azure Migrate: posouzení serveru**.
2. V **posouzení**klikněte na posouzení a otevřete ho.

    ![Souhrn posouzení](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Kontrola připravenosti na Azure

1. V **Azure Readiness**ověřte, jestli jsou virtuální počítače připravené k migraci do Azure.
2. Zkontrolujte stav virtuálního počítače:
    - **Připraveno pro Azure**: Azure Migrate doporučuje velikost virtuálního počítače a odhad nákladů pro virtuální počítače ve vyhodnocování.
    - **Připraveno s podmínkami**: zobrazuje problémy a navrhovanou nápravu.
    - **Nepřipraveno pro Azure**: zobrazuje problémy a navrhovanou nápravu.
    - **Připravenost neznámá**: používá se, když Azure Migrate nedokáže vyhodnotit připravenost kvůli problémům s dostupností dat.

2. Klikněte na stav **připravenosti na Azure** . Můžete si prohlédnout podrobnosti připravenosti na virtuální počítač a přejít k podrobnostem, kde najdete podrobnosti o virtuálním počítači, včetně výpočetních prostředků, úložiště a nastavení sítě.



### <a name="review-cost-details"></a>Podrobnosti o kontrole nákladů

Toto zobrazení ukazuje odhadované náklady na výpočetní prostředky a úložiště pro provozování virtuálních počítačů v Azure.

1. Projděte si měsíční náklady na výpočetní prostředky a úložiště. Náklady se sčítají pro všechny virtuální počítače v hodnocené skupině.

    - Odhad nákladů vychází z doporučení na velikost počítače a jeho disků a vlastností.
    - Zobrazí se Odhadované měsíční náklady na výpočetní prostředky a úložiště.
    - Odhad nákladů slouží ke spuštění místních virtuálních počítačů jako virtuálních počítačů IaaS. Posouzení Azure Migrate serveru nebere v úvahu náklady na PaaS nebo SaaS.

2. Můžete zkontrolovat odhady měsíčních nákladů na úložiště. Toto zobrazení ukazuje agregované náklady na úložiště pro vyhodnocenou skupinu rozdělené přes různé typy úložných disků.
3. Můžete přejít k podrobnostem a zobrazit podrobnosti pro konkrétní virtuální počítače.


### <a name="review-confidence-rating"></a>Kontrola hodnocení spolehlivosti

Když spustíte posouzení na základě výkonu, bude posouzení k posouzení přiřazeno hodnocení spolehlivosti.

![Hodnocení spolehlivosti](./media/tutorial-assess-vmware/confidence-rating.png)

- Je uděleno hodnocení od 1 hvězdičky (nejnižší) do 5 hvězdiček (nejvyšší).
- Hodnocení spolehlivosti vám pomůže odhadnout spolehlivost doporučení týkajících se velikosti, která poskytuje posouzení.
- Hodnocení spolehlivosti je založeno na dostupnosti datových bodů potřebných k výpočtu posouzení.

Hodnocení spolehlivosti pro posouzení je následující.

**Dostupnost datového bodu** | **Hodnocení spolehlivosti**
--- | ---
0 až 20 % | 1 hvězdička
21 až 40 % | 2 hvězdičky
41 až 60 % | 3 hvězdičky
61 až 80 % | 4 hvězdičky
81 až 100 % | 5 hvězdiček

[Přečtěte si další](best-practices-assessment.md#best-practices-for-confidence-ratings) informace o osvědčených postupech pro hodnocení spolehlivosti.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Nastavení zařízení Azure Migrate
> * Vytvoření a přezkoumání posouzení

Další informace o tom, jak migrovat virtuální počítače VMware do Azure pomocí migrace Azure Migrate serveru, najdete v dalším kurzu v řadě.

> [!div class="nextstepaction"]
> [Migrace virtuálních počítačů VMware](./tutorial-migrate-vmware.md)
