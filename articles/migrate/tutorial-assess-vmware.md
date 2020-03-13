---
title: Posouzení virtuálních počítačů VMware pro migraci do Azure pomocí serveru Azure Migrate Assessment
description: Popisuje, jak vyhodnotit místní virtuální počítače VMware pro migraci do Azure pomocí Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: hamusa
ms.openlocfilehash: 7f161afe13bad8c548806d4b4ceb9372dc511cc3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239319"
---
# <a name="assess-vmware-vms-by-using-azure-migrate-server-assessment"></a>Posouzení virtuálních počítačů VMware pomocí posouzení serveru Azure Migrate

V tomto článku se dozvíte, jak vyhodnotit místní virtuální počítače VMware pomocí nástroje pro vyhodnocení serveru v Azure Migrate.

[Azure Migrate](migrate-services-overview.md) poskytuje centrum nástrojů, které vám pomůžou zjišťovat, vyhodnocovat a migrovat aplikace, infrastrukturu a úlohy do Microsoft Azure. Centrum zahrnuje Azure Migrate nástrojů a nabídky nezávislých výrobců softwaru (ISV) od partnerů Microsoftu.

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

[Dokončete první kurz](tutorial-prepare-vmware.md) v této sérii. Pokud to neuděláte, pokyny v tomto kurzu nebudou fungovat.

Tady je seznam toho, co byste měli udělat v prvním kurzu:

- [Nastavte oprávnění Azure](tutorial-prepare-vmware.md#prepare-azure) pro Azure Migrate.
- [Příprava VMware](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) na posouzení:
   - [Ověřit](migrate-support-matrix-vmware.md#vmware-requirements) Nastavení VMware.
   - Nastavte oprávnění ve VMware a vytvořte virtuální počítač VMware se šablonou vajíček.
   - Nastavte [účet pro zjišťování virtuálních počítačů](migrate-support-matrix-vmware.md#vmware-requirements). 
   - [Požadované porty](migrate-support-matrix-vmware.md#port-access) zpřístupníte.
   - Uvědomte si [adresy URL potřebné](migrate-replication-appliance.md#url-access) pro přístup k Azure.

## <a name="set-up-an-azure-migrate-project"></a>Nastavení Azure Migrateho projektu

Vytvořte nový Azure Migrate projekt následujícím způsobem:

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
1. V části **Služby** vyberte **Azure Migrate**.
1. V části **Přehled**v části **zjišťování, vyhodnocení a migrace serverů**vyberte možnost **zhodnotit a migrovat servery**.

   ![Tlačítko pro vyhodnocení a migraci serverů](./media/tutorial-assess-vmware/assess-migrate.png)

1. V části **Začínáme**vyberte **Přidat nástroje**.
1. V části **Projekt migrace** vyberte své předplatné Azure a vytvořte skupinu prostředků, pokud ji ještě nemáte.     
1. V části **Project Details (podrobnosti projektu**) zadejte název projektu a zeměpisnou oblast, ve které chcete vytvořit projekt. Podporují se Asie, Evropa, Spojené království a USA.

   Zeměpisná oblast projektu slouží pouze k ukládání metadat shromážděných z místních virtuálních počítačů. Při spouštění migrace můžete vybrat jakoukoli cílovou oblast.

   ![Pole pro název a oblast projektu](./media/tutorial-assess-vmware/migrate-project.png)

1. Vyberte **Další**.
1. V **nástroji vybrat nástroj pro posouzení**vyberte **Azure Migrate: posouzení serveru** > **Další**.

   ![Výběr nástroje pro vyhodnocování serveru](./media/tutorial-assess-vmware/assessment-tool.png)

1. V části **Vybrat nástroj pro migraci** vyberte **V tuto chvíli přeskočit přidání nástroje pro migraci** > **Další**.
1. V okně **Revize + přidat nástroje**zkontrolujte nastavení a vyberte **Přidat nástroje**.
1. Počkejte několik minut, než se projekt Azure Migrate nasadí. Budete přesměrováni na stránku projektu. Pokud se projekt nezobrazí, můžete k němu přejít z části **Servery** na řídicím panelu služby Azure Migrate.

## <a name="set-up-the-appliance-vm"></a>Nastavení virtuálního počítače zařízení

Vyhodnocování serveru Azure Migrate spouští odlehčené zařízení VMware VM. Toto zařízení provádí zjišťování virtuálních počítačů a shromažďuje metadata virtuálních počítačů a data o výkonu.

Pokud chcete zařízení nastavit, postupujte takto:

- Stáhněte soubor šablony vajíček a naimportujte ho do vCenter Server.
- Vytvořte zařízení a ověřte, že se může připojit k Azure Migrate posouzení serveru.
- Nakonfigurujete zařízení poprvé a zaregistrujete ho do projektu Azure Migrate.

Pro jeden Azure Migrate projekt můžete nastavit více zařízení. U všech zařízení vyhodnocování serveru podporuje zjišťování až 35 000 virtuálních počítačů. Může zjistit maximálně 10 000 serverů na jedno zařízení.

### <a name="download-the-ova-template"></a>Stažení šablony pro VAJÍČKy

1. V ** > ** **cíle migrace** > **Azure Migrate: vyhodnocování serveru**vyberte **Vyhledat**.
1. V > **zjistit počítače** , ve **kterých jsou počítače virtualizované?** vyberte **Ano a hypervisor VMware vSphere**.
1. Vyberte **Stáhnout** a Stáhněte soubor šablony vajíček.

   ![Výběry pro stažení souboru vajíček](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Ověřit zabezpečení

Před nasazením ověřte, zda je soubor sady vajíček zabezpečený:

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
1. Spusťte následující příkaz, který vygeneruje hodnotu hash pro soubor sady vajíček:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Příklady použití: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

V případě verze 2.19.07.30 by vygenerovaná hodnota hash měla odpovídat těmto hodnotám:

**Algoritmus** | **Hodnota hash**
--- | ---
MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c

### <a name="create-the-appliance-vm"></a>Vytvoření virtuálního počítače zařízení

Naimportujte stažený soubor a vytvořte virtuální počítač:

1. V klientské konzole vSphere vyberte **soubor** > **nasadit šablonu OVF**.

   ![Příkaz nabídky pro nasazení šablony OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

1. V Průvodci nasazením šablony OVF > **zdroj**zadejte umístění souboru vajíček.
1. Do pole **název** a **umístění**zadejte popisný název virtuálního počítače. Vyberte objekt inventáře, do kterého bude virtuální počítač hostovat.
1. V části **hostitel nebo cluster**zadejte hostitele nebo cluster, na kterém se virtuální počítač spustí.
1. V části **Storage (úložiště**) zadejte cíl úložiště pro virtuální počítač.
1. V části **Disk Format** (Formát disku) zadejte typ a velikost disku.
1. V části **mapování sítě**určete síť, ke které se bude virtuální počítač připojovat. Síť potřebuje připojení k Internetu, aby mohla odesílat metadata Azure Migrate posouzení serveru.
1. Zkontrolujte a potvrďte nastavení a pak vyberte **Dokončit**.

### <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

Ujistěte se, že se virtuální počítač zařízení může připojit k [adresám URL Azure](migrate-appliance.md#url-access).

### <a name="configure-the-appliance"></a>Konfigurace zařízení

Zařízení nastavte pomocí následujících kroků:

1. V klientské konzole vSphere klikněte pravým tlačítkem myši na virtuální počítač a vyberte možnost **otevřít konzolu**.
1. Zadejte jazyk, časové pásmo a heslo pro zařízení.
1. Otevřete prohlížeč na jakémkoli počítači, který se může připojit k VIRTUÁLNÍmu počítači, a otevřete adresu URL webové aplikace zařízení: ***název zařízení https://nebo IP adresa*: 44368**.

   Alternativně můžete aplikaci otevřít z plochy zařízení tak, že vyberete zástupce aplikace.
1. Ve webové aplikaci > **nastavení požadavků**postupujte takto:
   - **Licence**: přijměte licenční podmínky a přečtěte si informace třetích stran.
   - **Připojení**: aplikace kontroluje, jestli má virtuální počítač přístup k Internetu. Pokud virtuální počítač používá proxy server:
     - Vyberte **nastavení proxy serveru**a zadejte adresu proxy serveru a port naslouchání ve formě http://ProxyIPAddress nebo http://ProxyFQDN.
     - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
     - Všimněte si, že je podporován pouze Proxy Server HTTP.
   - **Čas synchronizace**: čas v zařízení by měl být synchronizovaný s internetovým časem, aby zjišťování fungovalo správně.
   - **Nainstalovat aktualizace**: zařízení zajišťuje, že jsou nainstalované nejnovější aktualizace.
   - **Instalace VDDK**: zařízení kontroluje, jestli je nainstalovaná sada Virtual disks Development Kit (VDDK) VMware vSphere. Pokud není nainstalovaný, Stáhněte si z VMware VDDK 6,7 a Extrahujte stažený obsah zip do zadaného umístění na zařízení.

     Migrace Azure Migrate serveru používá VDDK k replikaci počítačů během migrace do Azure.       

### <a name="register-the-appliance-with-azure-migrate"></a>Zaregistrovat zařízení ve Azure Migrate

1. Vyberte **Přihlásit se**. Pokud se nezobrazí, ujistěte se, že jste v prohlížeči zakázali blokování automaticky otevíraných oken.
1. Na nové kartě se přihlaste pomocí uživatelského jména a hesla Azure.
   
   Přihlášení pomocí PIN kódu se nepodporuje.
1. Po úspěšném přihlášení se vraťte k webové aplikaci.
1. Vyberte předplatné, ve kterém byl vytvořen Azure Migrate projekt, a pak vyberte projekt.
1. Zadejte název zařízení. Název by měl být alfanumerický a nesmí obsahovat více než 14 znaků.
1. Vyberte **Zaregistrovat**.

## <a name="start-continuous-discovery"></a>Spustit průběžné zjišťování

Aby bylo možné zjistit konfiguraci a údaje o výkonu virtuálních počítačů, musí se zařízení připojit k vCenter Server.

### <a name="specify-vcenter-server-details"></a>Zadání podrobností vCenter Serveru
1. V části **zadat vCenter Server podrobnosti**zadejte název (FQDN) nebo IP adresu instance vCenter Server. Můžete ponechat výchozí port nebo zadat vlastní port, na kterém vCenter Server naslouchá.
1. Do pole **uživatelské jméno** a **heslo**zadejte přihlašovací údaje účtu vCenter Server, které zařízení použije ke zjištění virtuálních počítačů v instanci vCenter Server. 

   Ujistěte se, že účet má [požadovaná oprávnění pro zjišťování](migrate-support-matrix-vmware.md#vmware-requirements). [Rozsah zjišťování](tutorial-assess-vmware.md#set-the-scope-of-discovery) můžete omezit omezením přístupu k účtu vCenter.
1. Vyberte **ověřit připojení** a ujistěte se, že se zařízení může připojit k vCenter Server.

### <a name="specify-vm-credentials"></a>Zadat přihlašovací údaje virtuálního počítače
Pro zjišťování aplikací, rolí a funkcí a pro vizualizaci závislostí virtuálních počítačů můžete zadat přihlašovací údaje pro virtuální počítače, které mají přístup k virtuálním počítačům VMware. Můžete přidat jednu přihlašovací údaje pro virtuální počítače s Windows a jedno přihlašovací údaje pro virtuální počítače se systémem Linux. [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) o potřebných přístupových oprávněních.

> [!NOTE]
> Tento vstup je nepovinný, ale budete ho potřebovat, pokud chcete povolit zjišťování aplikací a vizualizaci závislostí bez agentů.

1. V **nabídce zjistit aplikace a závislosti na virtuálních počítačích**vyberte **Přidat přihlašovací údaje**.
1. Vytvořte výběr pro **operační systém**.
1. Zadejte popisný název přihlašovacích údajů.
1. V části **uživatelské jméno** a **heslo**zadejte účet, který má alespoň přístup k hostům na virtuálních počítačích.
1. Vyberte **Přidat**.

Po zadání instance vCenter Server a přihlašovacích údajů k virtuálnímu počítači (volitelné) vyberte **Uložit a spustit zjišťování a** spusťte tak zjišťování místního prostředí.

Zobrazení metadat zjištěných virtuálních počítačů na portálu trvá přibližně 15 minut. Zjišťování nainstalovaných aplikací, rolí a funkcí nějakou dobu trvá. Doba trvání závisí na počtu zjištěných virtuálních počítačů. Pro virtuální počítače 500 trvá na portálu Azure Migrate přibližně 1 hodinu, než se inventář aplikace zobrazí.

### <a name="set-the-scope-of-discovery"></a>Nastavení oboru zjišťování

Zjišťování může být vymezeno omezením přístupu k účtu vCenter, který se používá pro zjišťování. Obor můžete nastavit tak, aby vCenter Server datová centra, clustery, složku clusterů, hostitele, složku hostitelů nebo jednotlivé virtuální počítače.

Chcete-li nastavit obor, proveďte následující postupy.

#### <a name="1-create-a-vcenter-user-account"></a>1. vytvoření uživatelského účtu vCenter
1.  Přihlaste se k webovému klientovi vSphere jako správce vCenter Server.
1.  Vyberte možnost **správa** > **uživatelů a skupin jednotného přihlašování**a pak vyberte kartu **Uživatelé** .
1.  Vyberte ikonu **nového uživatele** .
1.  Zadejte požadované informace pro vytvoření nového uživatele a pak vyberte **OK**.

#### <a name="2-define-a-new-role-with-required-permission"></a>2. definice nové role s požadovaným oprávněním
Tento postup je nutný pro migraci serveru bez agenta.
1.  Přihlaste se k webovému klientovi vSphere jako správce vCenter Server.
1.  Přejděte do **administrace** > **správce rolí**.
1.  Z rozevírací nabídky vyberte svou instanci vCenter Server.
1.  Vyberte **vytvořit roli**.
1.  Zadejte název nové role (například <em>Azure_Migrate</em>).
1.  Přiřaďte [oprávnění](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) k nově definované roli.
1.  Vyberte **OK**.

#### <a name="3-assign-permissions-on-vcenter-objects"></a>3. přiřazení oprávnění pro objekty vCenter

Existují dva způsoby, jak přiřadit oprávnění pro objekty inventáře v vCenter k uživatelskému účtu vCenter s přiřazenou rolí.

Pro vyhodnocení serveru musíte použít roli jen **pro čtení** pro uživatelský účet vCenter pro všechny nadřazené objekty, ve kterých jsou hostované virtuální počítače, které mají být zjištěny. Budou zahrnuty všechny nadřazené objekty: hostitel, složka hostitelů, cluster a složka clusterů v hierarchii až do datacentra. Tato oprávnění budou rozšířena na podřízené objekty v hierarchii.

Podobně jako u migrace serveru musíte použít uživatelsky definovanou roli s [oprávněními](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) k uživatelskému účtu vCenter pro všechny nadřazené objekty, u kterých jsou virtuální počítače, které se mají migrovat, hostované. Tato role může mít název <em>_Migrate Azure</em>.

![Přiřazení oprávnění](./media/tutorial-assess-vmware/assign-perms.png)

Alternativním přístupem je přiřadit uživatelský účet a roli na úrovni datového centra a rozšířit je na podřízené objekty. Pak mu udělte účet **bez role přístupu** pro každý objekt (například virtuální počítač), který nechcete zjišťovat nebo migrovat. 

Tato alternativní konfigurace je nenáročný. Zpřístupňuje nechtěné řízení přístupu, protože každému novému podřízenému objektu je také automaticky udělen přístup, který je zděděný z nadřazené položky. Proto doporučujeme použít první přístup.

> [!NOTE]
> V současné době posouzení serveru nemůže zjistit virtuální počítače, pokud má účet vCenter udělen přístup na úrovni složky virtuálních počítačů vCenter. Pokud chcete určit obor zjišťování pomocí složek virtuálního počítače, můžete to udělat pomocí následujícího postupu. Zajišťuje, aby měl účet vCenter přístup jen pro čtení přiřazený na úrovni virtuálního počítače.
>
> 1. Přiřaďte oprávnění jen pro čtení na všech virtuálních počítačích ve složkách virtuálních počítačů, na které chcete zjišťování oborovat.
> 1. Udělte přístup jen pro čtení ke všem nadřazeným objektům, kde jsou hostované virtuální počítače. Budou zahrnuty všechny nadřazené objekty (hostitel, složka hostitelů, cluster, složka clusterů) v hierarchii až do datacentra. Nemusíte šířit oprávnění ke všem podřízeným objektům.
> 1. Použijte přihlašovací údaje pro zjišťování tak, že vyberete datový datacentrum jako **Rozsah shromažďování**. Nastavení řízení přístupu na základě rolí zajišťuje, že odpovídající uživatel vCenter bude mít přístup jenom k virtuálním počítačům specifickým pro tenanta.
>
> Všimněte si, že jsou podporovány složky hostitelů a clusterů.

### <a name="verify-vms-in-the-portal"></a>Kontrola virtuálních počítačů na portálu

Po zjištění můžete ověřit, že se virtuální počítače zobrazují v Azure Portal:

1. Otevřete řídicí panel Azure Migrate.
1. V **Azure Migrate-servery** > **Azure Migrate: posouzení serveru**, vyberte ikonu, která zobrazuje počet **zjištěných serverů**.

## <a name="set-up-an-assessment"></a>Nastavení posouzení

Pomocí vyhodnocení Azure Migrate serveru můžete vytvořit dva typy posouzení:

**Posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Posouzení na základě shromážděných dat o výkonu | **Doporučená velikost virtuálního počítače**: na základě dat využití procesoru a paměti.<br/><br/> **Doporučený typ disku (spravovaný disk Standard nebo Premium)** : na základě vstupně-výstupních operací a propustnosti místních disků.
**Jako místní** | Posouzení na základě místních velikostí | **Doporučená velikost virtuálního počítače**: v závislosti na velikosti místního virtuálního počítače.<br/><br> **Doporučený typ disku**: na základě nastavení typu úložiště, které jste vybrali pro posouzení.

## <a name="run-an-assessment"></a>Spuštění posouzení

Proveďte posouzení následujícím způsobem:

1. Projděte si [osvědčené postupy](best-practices-assessment.md) pro vytváření hodnocení.
1. Na kartě **servery** na dlaždici **Azure Migrate: posouzení serveru** vyberte možnost **vyhodnotit**.

   ![Umístění tlačítka pro vyhodnocení](./media/tutorial-assess-vmware/assess.png)

1. V poli **vyhodnotit servery**zadejte název posouzení.
1. Vyberte **Zobrazit vše**a pak zkontrolujte vlastnosti posouzení.

   ![Vlastnosti posouzení](./media/tutorial-assess-vmware/view-all.png)

1. V **Vyberte nebo vytvořte skupinu**vyberte **vytvořit novou**a zadejte název skupiny. Skupina shromažďuje jeden nebo více virtuálních počítačů dohromady pro posouzení.
1. V části **přidat počítače do skupiny**vyberte virtuální počítače, které chcete do skupiny přidat.
1. Vyberte **vytvořit vyhodnocení** a vytvořte skupinu a spusťte posouzení.

   ![Vyhodnotit servery](./media/tutorial-assess-vmware/assessment-create.png)

1. Po vytvoření posouzení ho zobrazte na stránce **servery** > **Azure Migrate: posouzení serveru** > **posouzení**.
1. Vyberte **vyhodnocování exportu** a stáhněte ho jako excelový soubor.

## <a name="review-an-assessment"></a>Kontrola posouzení

Posouzení popisuje:

- **Připravenost na Azure**: jestli jsou virtuální počítače vhodné pro migraci do Azure.
- **Odhad měsíčních nákladů**: Odhadované měsíční náklady na výpočetní prostředky a úložiště pro provoz virtuálních počítačů v Azure.
- **Odhad měsíčních nákladů na úložiště**: Odhadované náklady na diskové úložiště po migraci.

Zobrazení posouzení:

1. V ** > ** **cíli migrace** vyberte **hodnocení** v **Azure Migrate: posouzení serveru**.
1. V **posouzení**vyberte posouzení, které chcete otevřít.

   ![Souhrn posouzení](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Kontrola připravenosti na Azure

1. V **Azure Readiness**ověřte, jestli jsou virtuální počítače připravené k migraci do Azure.
1. Zkontrolujte stav virtuálního počítače:
    - **Připraveno pro Azure**: používá se, když Azure Migrate doporučuje velikost virtuálního počítače a odhad nákladů pro virtuální počítače ve vyhodnocování.
    - **Připraveno s podmínkami**: zobrazuje problémy a navrhovanou nápravu.
    - **Nepřipraveno pro Azure**: zobrazuje problémy a navrhovanou nápravu.
    - **Připravenost neznámá**: používá se, když Azure Migrate nemůže posoudit připravenost z důvodu problémů s dostupností dat.

1. Vyberte stav **připravenosti na Azure** . Můžete si prohlédnout podrobnosti připravenosti na virtuální počítače. Můžete také přejít k podrobnostem a zobrazit podrobnosti o virtuálním počítači, včetně výpočetních prostředků, úložiště a nastavení sítě.

### <a name="review-cost-details"></a>Podrobnosti o kontrole nákladů

Souhrn posouzení zobrazuje odhadované náklady na výpočetní prostředky a úložiště pro provoz virtuálních počítačů v Azure. Náklady se sčítají pro všechny virtuální počítače v hodnocené skupině. Můžete přejít k podrobnostem a zobrazit podrobnosti o cenách pro konkrétní virtuální počítače.

> [!NOTE]
> Odhad nákladů vychází z doporučení týkajících se velikosti počítače, jeho disků a vlastností. Odhady slouží ke spouštění místních virtuálních počítačů jako virtuálních počítačů IaaS. Posouzení Azure Migrate serveru nebere v úvahu náklady na PaaS nebo SaaS.

Agregované náklady na úložiště pro vyhodnocenou skupinu jsou rozdělené do různých typů úložných disků. 

### <a name="review-confidence-rating"></a>Kontrola hodnocení spolehlivosti

Vyhodnocování Azure Migrate serveru přiřadí hodnocení spolehlivosti pro posouzení na základě výkonu, od 1 hvězdičky (nejnižší) po 5 hvězdiček (nejvyšší).

![Hodnocení spolehlivosti](./media/tutorial-assess-vmware/confidence-rating.png)

Hodnocení spolehlivosti vám pomůže odhadnout spolehlivost doporučení týkajících se velikosti posouzení. Hodnocení je založeno na dostupnosti datových bodů potřebných k výpočtu posouzení:

**Dostupnost datového bodu** | **Hodnocení spolehlivosti**
--- | ---
0 až 20 % | 1 hvězdička
21 až 40 % | 2 hvězdičky
41 až 60 % | 3 hvězdičky
61 až 80 % | 4 hvězdičky
81 až 100 % | 5 hvězdiček

[Seznamte se s osvědčenými postupy](best-practices-assessment.md#best-practices-for-confidence-ratings) pro hodnocení spolehlivosti.

## <a name="next-steps"></a>Další kroky

V tomto kurzu nastavíte zařízení Azure Migrate. Také jste vytvořili a zkontrolovali posouzení.

Pokud se chcete dozvědět, jak migrovat virtuální počítače VMware do Azure pomocí migrace serveru Azure Migrate, pokračujte na třetí kurz v této sérii:

> [!div class="nextstepaction"]
> [Migrace virtuálních počítačů VMware](./tutorial-migrate-vmware.md)
