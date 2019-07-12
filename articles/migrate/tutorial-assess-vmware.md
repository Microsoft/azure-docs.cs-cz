---
title: Posouzení virtuálních počítačů VMware pro migraci do Azure pomocí služby Azure Migrate
description: Popisuje, jak posouzení místních virtuálních počítačů VMware pro migraci do Azure pomocí služby Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.openlocfilehash: 5dc1a05e93bf1e82269a4291f147bac6e8ba657a
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813003"
---
# <a name="assess-vmware-vms-with-azure-migrate-server-assessment"></a>Posouzení virtuálních počítačů VMware pomocí Azure Migrate: Server Assessment

Tento článek ukazuje, jak posouzení virtuálních počítačů VMware v místním, pomocí služby Azure Migrate: Nástroj pro vyhodnocení serveru.

[Azure Migrate](migrate-services-overview.md) představuje Centrum nástrojů, které vám umožní zjišťovat, posuzovat a migrovat aplikace, infrastruktury a úlohy Microsoft Azure. Centra zahrnuje nástroje Azure Migrate a nezávislé výrobce softwaru třetích stran výrobce (ISV) nabídky.



Tento kurz je druhá řady, který ukazuje, jak vyhodnocovat a migrovat virtuální počítače VMware do Azure. V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavte projekt Azure Migrate.
> * Nastavení zařízení, na kterém běží v místním na posouzení virtuálních počítačů Azure Migrate.
> * Spuštění průběžná zjišťování místních virtuálních počítačů. Zařízení odesílá data konfigurace a výkonu pro zjištěné virtuální počítače do Azure.
> * Seskupení zjištěných virtuálních počítačů a vyhodnocení skupiny virtuálních počítačů.
> * Projděte si hodnocení.



> [!NOTE]
> Kurzy vám ukážou, nejjednodušší způsob nasazení pro scénář tak, že můžete rychle nastavit testování konceptu. Kurzy použijte výchozí možnosti, kde je to možné a nezobrazovat všechna možná nastavení a cesty. Podrobné pokyny najdete v tématu články s postupy.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.


## <a name="prerequisites"></a>Požadavky

- [Kompletní](tutorial-prepare-vmware.md) prvního kurzu této série. Pokud to neuděláte, nebude fungovat podle pokynů v tomto kurzu.
- Zde je, co by měl mít uděláte z prvního kurzu:
    - [Nastavení oprávnění Azure](tutorial-prepare-vmware.md#prepare-azure) ke službě Azure Migrate.
    - [Příprava VMware](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) pro posouzení. Je třeba ověřit nastavení VMware a byste měli mít oprávnění k vytvoření virtuálního počítače VMware pomocí šablony OVA. Také byste měli mít účet ještě pro zjišťování virtuálních počítačů. Požadované porty by měl být k dispozici. proto byste měli vědět o adresy URL potřebné pro přístup k Azure.


## <a name="set-up-an-azure-migrate-project"></a>Nastavte projekt Azure Migrate

Nový projekt Azure Migrate nastavte následujícím způsobem.

1. Na webu Azure Portal > **všechny služby**, vyhledejte **Azure Migrate**.
2. V části **služby**vyberte **Azure Migrate**.
3. V **přehled**v části **zjišťování, vyhodnocení a migrace serverů**, klikněte na tlačítko **posouzení a migraci serverů**.

    ![Zkoumání a posouzení servery](./media/tutorial-assess-vmware/assess-migrate.png)

4. V **Začínáme**, klikněte na tlačítko **přidat nástroje**.
5. V **migrace projektu**, vyberte své předplatné Azure a pokud ho nemáte, vytvořte skupinu prostředků.     
6. V **Project Details**, zadejte název projektu a zeměpisné oblasti, ve kterém chcete vytvořit projekt. Asie, Evropa, Spojené království a USA jsou podporovány.

    - Zeměpisné oblasti projektu slouží pouze k uložení metadat shromážděných z místních virtuálních počítačů.
    - Při migraci spustíte, můžete vybrat libovolnou oblast cíl.

    ![Vytvoření projektu Azure Migrate](./media/tutorial-assess-vmware/migrate-project.png)


7. Klikněte na **Další**.
8. V **nástroj pro vyhodnocení vyberte**vyberte **Azure Migrate: Server Assessment** > **Další**.

    ![Vytvoření projektu Azure Migrate](./media/tutorial-assess-vmware/assessment-tool.png)

9. V **nástroj pro migraci vybrat**vyberte **přidat nástroj pro migraci prozatím přeskočit** > **Další**.
10. V **zkontrolujte + přidat nástroje**, zkontrolujte nastavení a klikněte na tlačítko **přidat nástroje**.
11. Počkejte několik minut, než projekt Azure Migrate k nasazení. Budete přesměrováni na stránku projektu. Pokud nevidíte projekt, k němu máte přístup z **servery** na řídicím panelu Azure Migrate.


## <a name="set-up-the-appliance-vm"></a>Nastavení zařízení virtuálního počítače

Azure Migrate: Spustí server Assessment jednoduchý virtuální počítač VMware zařízení.

- Toto zařízení provádí zjišťování virtuálních počítačů a odesílá data metadata a výkonu virtuálních počítačů do Azure migrovat Server Assessment.
- Nastavení zařízení můžete:
    - Stáhnout soubor OVA šablony a importovat ho do systému vCenter Server.
    - Toto zařízení vytvoříte tak a zkontrolujte, že se může připojit k Azure Migrate Server Assessment.
    - Konfigurace zařízení poprvé a zaregistrujte je v projektu Azure Migrate.

### <a name="download-the-ova-template"></a>Stáhnout šablonu pro soubory OVA

1. V **cílů migrace** > **servery** > **Azure Migrate: Server Assessment**, klikněte na tlačítko **Discover**.
2. V **zjistit počítače** > **jsou vaše počítače virtualizované?** , klikněte na tlačítko **Ano, s VMWare vSphere hypervisor**.
3. Klikněte na tlačítko **Stáhnout** ke stažení. Soubor OVA šablony.

    ![Stažení souboru .OVA](./media/tutorial-assess-vmware/download-ova.png)


### <a name="verify-security"></a>Ověření zabezpečení

Zkontrolujte, zda soubor OVA zabezpečené, před jejím nasazením.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz, kterým vygenerujete hodnotu hash pro soubor OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklady použití: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Pro verzi 1.19.06.27 vygenerovaná hodnota hash by měla odpovídat tyto hodnoty. 

  **Algoritmus** | **Hodnota hash**
  --- | ---
  MD5 | 605d208ac5f4173383f616913441144e
  SHA256 | 447d16bd55f20f945164a1189381ef6e98475b573d6d1c694f3e5c172cfc30d4


### <a name="create-the-appliance-vm"></a>Toto zařízení vytvoříte virtuální počítač

Importujte stažený soubor a vytvoření virtuálního počítače.

1. V konzoli vSphere Client klikněte na **File** (Soubor) > **Deploy OVF Template** (Nasadit šablonu OVF).

    ![Nasazení šablony OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. V Průvodci nasazením šablony OVF > **zdroj**, zadejte umístění souboru pro soubory OVA.
3. V **název** a **umístění**, zadejte popisný název pro virtuální počítač. Vyberte objekt inventáře, ve kterém bude virtuální počítač hostovaný.
5. V **hostiteli/clusteru**, zadejte hostitele nebo cluster, na kterého se spouští virtuální počítač.
6. V **úložiště**, zadejte cílové úložiště pro virtuální počítač.
7. V části **Disk Format** (Formát disku) zadejte typ a velikost disku.
8. V **mapování sítě**, zadejte síť, ke kterým se připojí virtuální počítač. Síť vyžaduje připojení k Internetu, chcete-li odesílat metadata do Azure migrovat Server Assessment.
9. Zkontrolujte a ověřte všechna nastavení a pak klikněte na **Finish** (Dokončit).


### <a name="verify-appliance-access-to-azure"></a>Ověřit přístup zařízení k Azure

Ujistěte se, že zařízení virtuálního počítače může připojit k [adresám URL služby Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


### <a name="configure-the-appliance"></a>Konfigurace zařízení

Nastavení zařízení pomocí následujících kroků.

1. V konzoli vSphere Client klikněte pravým tlačítkem na daný virtuální počítač a pak na **Open Console** (Otevřít konzolu).
2. Zadejte jazyk, časové pásmo a heslo pro zařízení.
3. Otevřete prohlížeč na jakémkoli počítači můžete připojit k virtuálnímu počítači a otevřít adresu URL webové aplikace pro zařízení: **https://*zařízení název nebo IP adresu*: 44368**.

   Alternativně můžete otevřít aplikaci z plochy zařízení kliknutím na zástupce aplikace.
4. Ve webové aplikaci > **nastavit požadavky**, postupujte takto:
    - **Licence**: Přijměte licenční podmínky a přečtěte si informace třetích stran.
    - **Připojení**: Je aplikace zkontroluje, že virtuální počítač má přístup k Internetu. Pokud virtuální počítač používá proxy server:
        - Klikněte na tlačítko **nastavení proxy serveru**a zadejte adresu proxy serveru a nastavte naslouchající port ve tvaru http://ProxyIPAddress nebo http://ProxyFQDN.
        - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
        - Podporuje se jen proxy protokolu HTTP.
    - **Čas synchronizace**: čas na zařízení by měl být synchronizovaný s internetovým časem zjišťování fungovala správně.
    - **Instalace aktualizací**: Zařízení se zajistí, že jsou nainstalované nejnovější aktualizace.
    - **Nainstalujte VDDK**: Zařízení zkontroluje, že VMWare vSphere, které se virtuální Disk Development Kit (VDDK) je nainstalována.
        - Azure Migrate: Migrace serveru VDDK používá k replikaci počítačů během migrace do Azure.
        - Stáhněte si VDDK 6.7 z VMware a extrahujte obsah stažený zip do zadaného umístění na zařízení.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrace zařízení pomocí služby Azure Migrate

1. Klikněte na tlačítko **přihlášení**. Pokud se nezobrazí, ujistěte se, že jsme deaktivovali blokování automaticky otevíraných oken v prohlížeči.
2. Na nové kartě Přihlaste se pomocí přihlašovacích údajů Azure.
    - Přihlaste se pomocí uživatelského jména a hesla.
    - Přihlaste se pomocí kódu PIN není podporován.
3. Po úspěšném přihlášení přejděte zpět do webové aplikace.
2. Vyberte předplatné, ve kterém byl vytvořen projekt Azure Migrate a pak vyberte projekt.
3. Zadejte název pro zařízení. Název musí být alfanumerické znaky se 14 znaků nebo méně.
4. Klikněte na tlačítko **zaregistrovat**.


## <a name="start-continuous-discovery"></a>Spustit průběžné vyhledávání

Nyní připojení ze zařízení do systému vCenter Server a spusťte zjišťování virtuálních počítačů.

1. V **zadejte podrobnosti vCenter serveru**, zadejte název (FQDN) nebo IP adresu vcenter serveru. Můžete ponechat výchozí port, nebo zadat vlastní port, na kterém vCenter Server naslouchá.
2. V **uživatelské jméno** a **heslo**, zadejte přihlašovací údaje účtu jen pro čtení, které zařízení použije ke zjištění virtuálních počítačů na serveru vCenter. Ujistěte se, že účet má [požadovaná oprávnění pro zjišťování](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions).
3. Klikněte na tlačítko **ověřit připojení** abyste měli jistotu, že zařízení může připojit k serveru vCenter.
4. Jakmile se naváže připojení, klikněte na tlačítko **uložit a spustit zjišťování**.


Tím se spustí zjišťování. Trvá přibližně 15 minut metadat zjištěných virtuálních počítačů se na portálu.

### <a name="verify-vms-in-the-portal"></a>Kontrola virtuálních počítačů na portálu

Po zjištění můžete ověřit, že se virtuální počítače zobrazí na webu Azure Portal.

1. Otevřete řídicí panel Azure Migrate.
2. V **Azure Migrate – servery** > **Azure Migrate: Server Assessment** klikněte na ikonu, která se zobrazí počet **zjištěných serverů**.

## <a name="set-up-an-assessment"></a>Nastavení posouzení

Existují dva typy posouzení, která můžete vytvořit pomocí Azure Migrate: Server Assessment.

**Posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Posouzení na základě výkonu shromážděných dat | **Doporučená velikost virtuálního počítače**: Na základě dat využití procesoru a paměti.<br/><br/> **Typ disku (standard nebo premium spravovaného disku) doporučuje**: Na základě vstupně-výstupních operací a propustnosti s místními disky.
**Jako místní** | Posouzení podle velikosti na místě. | **Doporučená velikost virtuálního počítače**: Na základě velikosti virtuálního počítače místní<br/><br> **Typ disku doporučujeme**: Podle nastavení typ úložiště, které vyberete pro posouzení.


### <a name="run-an-assessment"></a>Spuštění posouzení

Spusťte posouzení následujícím způsobem:

1. Zkontrolujte [osvědčené postupy](best-practices-assessment.md) pro vytváření posouzení.
2. V **servery** kartě **Azure Migrate: Server Assessment** dlaždici, klikněte na tlačítko **posouzení**.

    ![Posouzení](./media/tutorial-assess-vmware/assess.png)

2. V **vyhodnocení servery**, zadejte název posouzení.
3. Kliknutím na **Zobrazit vše** zobrazíte vlastnosti posouzení.

    ![Vlastnosti posouzení](./media/tutorial-assess-vmware/view-all.png)

3. V **vyberte nebo vytvořte skupinu**vyberte **vytvořit nový**a zadejte název skupiny. Skupina shromažďuje jeden nebo více virtuálních počítačů dohromady pro posouzení.
4. V **přidat počítače do skupiny**, vyberte virtuální počítače, které přidáte do skupiny.
5. Klikněte na tlačítko **vytvořit posouzení** k vytvoření skupiny a spusťte posouzení.

    ![Vytvoření posouzení](./media/tutorial-assess-vmware/assessment-create.png)

6. Jakmile bude posouzení hotové, zobrazíte ho v **servery** > **Azure Migrate: Server Assessment** > **posouzení**.
7. Klikněte na **Exportovat posouzení** a stáhněte ho jako excelový soubor.



## <a name="review-an-assessment"></a>Kontrola posouzení

Popisuje posouzení:

- **Připravenost pro Azure**: Určuje, zda virtuální počítače jsou vhodné pro migraci do Azure.
- **Odhad měsíčních nákladů**: Odhadované měsíční úložnou a výpočetní náklady pro spouštění virtuálních počítačů v Azure.
- **Odhad nákladů na úložiště měsíční**: Odhadované náklady za úložiště na disku po migraci.

### <a name="view-an-assessment"></a>Zobrazení posouzení

1. V **cílů migrace** >  **servery**, klikněte na tlačítko **posouzení** v **Azure Migrate: Server Assessment**.
2. V **posouzení**, klikněte na posouzení ho otevřete.

    ![Souhrn hodnocení](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Zkontrolovat připravenost pro Azure

1. V **připravenost pro Azure**, ověří, jestli virtuální počítače nejsou připravené k migraci do Azure.
2. Zkontrolujte stav virtuálního počítače:
    - **Připraveno pro Azure**: Azure Migrate doporučí velikost virtuálního počítače a náklady na odhady pro virtuální počítače v posouzení.
    - **Připraveno s podmínkami**: Zobrazí problémy a navrhované nápravné.
    - **Nepřipraveno pro Azure**: Zobrazí problémy a navrhované nápravné.
    - **Připravenost Neznámá**: Používá, když Azure Migrate nemůže vyhodnocení připravenosti z důvodu problémů s dostupností data.

2. Klikněte na **připravenost pro Azure** stav. Můžete zobrazit podrobnosti o připravenosti virtuálním počítači a potom přejít na podrobnosti o virtuálním počítači najdete v tématu, včetně pro výpočty, úložiště a nastavení sítě.



### <a name="review-cost-details"></a>Podrobnosti o kontrole nákladů

Toto zobrazení ukazuje odhadované náklady výpočetní výkon a úložiště spuštěných virtuálních počítačů v Azure.

1. Projděte si měsíční náklady na výpočetní výkon a úložiště. Náklady jsou agregované pro všechny virtuální počítače ve skupině posouzený.

    - Odhadované náklady jsou založeny na doporučené velikosti pro počítače a příslušné disky a vlastnosti.
    - Odhadované měsíční náklady na výpočetní výkon a úložiště jsou uvedeny.
    - Odhad nákladů je pro spuštění místních virtuálních počítačů jako virtuální počítače IaaS. Azure Migrate Server Assessment nebere v úvahu náklady PaaS nebo platformám SaaS.

2. Můžete zkontrolovat měsíční odhady nákladů úložiště. Toto zobrazení ukazuje náklady na agregované úložiště skupině posouzené rozdělit přes různé typy disků úložiště.
3. Kvůli najdete v podrobnostech přejití k podrobnostem pro konkrétní virtuální počítače.


### <a name="review-confidence-rating"></a>Kontrola hodnocení spolehlivosti

Při spuštění posouzení založená na výkon, je přiřazen hodnocení spolehlivosti posouzení.

![Hodnocení spolehlivosti](./media/tutorial-assess-vmware/confidence-rating.png)

- Je udělena hodnocení od 1 (nejnižší) do 5 hvězdiček (nejvyšší).
- Hodnocení spolehlivosti pomáhá odhadnout spolehlivost doporučení velikostí poskytovaných službou posouzení.
- Hodnocení spolehlivosti se zakládá na dostupnosti datových bodů potřebných pro výpočet posouzení.

Hodnocení spolehlivosti posouzení jsou následující.

**Dostupnost datového bodu** | **Hodnocení spolehlivosti**
--- | ---
0 až 20 % | 1 hvězdička
21 až 40 % | 2 hvězdičky
41 až 60 % | 3 hvězdičky
61 až 80 % | 4 Star
81 až 100 % | 5 hvězdiček

[Další informace](best-practices-assessment.md#best-practices-for-confidence-ratings) o osvědčených postupech pro hodnocení spolehlivosti.


## <a name="next-steps"></a>Další postup

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení zařízení Azure Migrate
> * Vytvoří a zkontroluje posouzení

I nadále třetí dílem série, se naučíte migrovat virtuální počítače VMware do Azure pomocí služby Azure Migrate serveru migrace.

> [!div class="nextstepaction"]
> [Migrace virtuálních počítačů VMware](./tutorial-migrate-vmware.md)
