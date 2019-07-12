---
title: Posouzení virtuálních počítačů Hyper-V pro migraci do Azure pomocí služby Azure Migrate | Dokumentace Microsoftu
description: Popisuje, o posouzení místních virtuálních počítačů Hyper-V pro migraci do Azure pomocí služby Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 83567a45980b29931f9b68bd6d60df0d427b09de
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813022"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Posouzení virtuálních počítačů Hyper-V s využitím Azure Migrate Server Assessment

Tento článek ukazuje, jak posouzení virtuálních počítačů Hyper-V v místním, pomocí služby Azure Migrate: Nástroj pro vyhodnocení serveru.

[Azure Migrate](migrate-services-overview.md) představuje Centrum nástrojů, které vám umožní zjišťovat, posuzovat a migrovat aplikace, infrastruktury a úlohy Microsoft Azure. Centra zahrnuje nástroje Azure Migrate a nezávislé výrobce softwaru třetích stran výrobce (ISV) nabídky.



Tento kurz je druhá řady, který ukazuje, jak vyhodnocovat a migrovat virtuální počítače Hyper-V do Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte projekt Azure Migrate.
> * Nastavení a registrace zařízení Azure Migrate.
> * Spuštění průběžná zjišťování místních virtuálních počítačů.
> * Seskupení zjištěných virtuálních počítačů a vyhodnocení skupiny.
> * Projděte si hodnocení.

> [!NOTE]
> Kurzy vám ukážou, nejjednodušší způsob nasazení pro scénář tak, že můžete rychle nastavit testování konceptu. Kurzy použijte výchozí možnosti, kde je to možné a nezobrazovat všechna možná nastavení a cesty. Podrobné pokyny najdete v tématu články s postupy.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.


## <a name="prerequisites"></a>Požadavky

- [Kompletní](tutorial-prepare-hyper-v.md) prvního kurzu této série. Pokud to neuděláte, nebude fungovat podle pokynů v tomto kurzu.
- Zde je, co by měl mít uděláte z prvního kurzu:
    - [Nastavení oprávnění Azure](tutorial-prepare-hyper-v.md#prepare-azure) ke službě Azure Migrate.
    - [Příprava Hyper-V](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-assessment) clustery hostitelů a virtuálních počítačů pro posouzení.

## <a name="set-up-an-azure-migrate-project"></a>Nastavte projekt Azure Migrate

1. Na webu Azure Portal > **všechny služby**, vyhledejte **Azure Migrate**.
2. Ve výsledcích hledání vyberte **Azure Migrate**.
3. V **přehled**v části **zjišťování, vyhodnocení a migrace serverů**, klikněte na tlačítko **posouzení a migraci serverů**.

    ![Zkoumání a posouzení servery](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. V **Začínáme**, klikněte na tlačítko **přidat nástroje**.
5. V **migrace projektu** kartu, vyberte své předplatné Azure a vytvořte skupinu prostředků, pokud ho nemáte.
6. V **Project Details**, zadejte název projektu a oblasti, ve kterém chcete vytvořit projekt.


    ![Vytvoření projektu Azure Migrate](./media/tutorial-assess-hyper-v/migrate-project.png)

    Projekt Azure Migrate můžete vytvořit v těchto oblastech.

    **Zeměpisné oblasti** | **Oblast**
    --- | ---
    Asie  | Jihovýchodní Asie
    Evropa | Severní Evropa a západní Evropa
    Spojené království |  Velká Británie – jih nebo Velká Británie – západ
    Spojené státy | USA – východ, USA – západ 2 a střed USA – západ

    - Oblast projektu slouží pouze k uložení metadat shromážděných z místních virtuálních počítačů.
    - Při migraci virtuálních počítačů můžete vybrat jiný Azure cílové oblasti. Všechny oblasti Azure podporují pro cíl migrace.

7. Klikněte na **Další**.
8. V **nástroj pro vyhodnocení vyberte**vyberte **Azure Migrate: Server Assessment** > **Další**.

    ![Vytvoření projektu Azure Migrate](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. V **nástroj pro migraci vybrat**vyberte **přidat nástroj pro migraci prozatím přeskočit** > **Další**.
10. V **zkontrolujte + přidat nástroje**, zkontrolujte nastavení a klikněte na tlačítko **přidat nástroje**.
11. Počkejte několik minut, než projekt Azure Migrate k nasazení. Budete přesměrováni na stránku projektu. Pokud nevidíte projekt, k němu máte přístup z **servery** na řídicím panelu Azure Migrate.




## <a name="set-up-the-appliance-vm"></a>Nastavení zařízení virtuálního počítače

Azure Migrate Server Assessment spustí jednoduchý virtuální počítač Hyper-V zařízení.

- Toto zařízení provádí zjišťování virtuálních počítačů a odesílá data metadat a výkonu virtuálních počítačů do Azure Migrate: Server Assessment.
- Nastavení zařízení můžete:
    - Stáhněte si komprimovaný virtuálního pevného disku Hyper-V na webu Azure Portal.
    - Toto zařízení vytvoříte tak a zkontrolujte, že se může připojit k Azure Migrate Server Assessment.
    - Konfigurace zařízení poprvé a zaregistrujte je v projektu Azure Migrate.

### <a name="download-the-vhd"></a>Stažení virtuálního pevného disku

Stáhněte si komprimovaný šablonu virtuálního pevného disku pro zařízení.

1. V **cílů migrace** > **servery** > **Azure Migrate: Server Assessment**, klikněte na tlačítko **Discover**.
2. V **zjistit počítače** > **jsou vaše počítače virtualizované?** , klikněte na tlačítko **Ano, s technologií Hyper-V**.
3. Klikněte na tlačítko **Stáhnout** ke stažení souboru virtuálního pevného disku.

    ![Stažení virtuálního počítače](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Ověření zabezpečení

Zkontrolujte, že soubor s příponou ZIP zabezpečené, před jejím nasazením.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz, kterým vygenerujete hodnotu hash pro virtuální pevný disk
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklady použití: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  Pro verzi zařízení 1.19.06.27 vygenerovaná hodnota hash by měla odpovídat následujícímu nastavení.

  **Algoritmus** | **Hodnota hash**
  --- | ---
  MD5 | 3681f745fa2b0a0a6910707d85161ec5
  SHA256 | e6ca109afab9657bdcfb291c343b3e3abced9a273d25273059171f9954d25832



### <a name="create-the-appliance-vm"></a>Toto zařízení vytvoříte virtuální počítač

Importujte stažený soubor a vytvoření virtuálního počítače.

1. Extrahujte komprimovaný soubor virtuálního pevného disku do složky na hostiteli Hyper-V, který bude hostitelem zařízení virtuálního počítače. Tři složky jsou extrahovány.
2. Otevřete Správce technologie Hyper-V. V **akce**, klikněte na tlačítko **importovat virtuální počítač**.

    ![Nasazení virtuálního pevného disku](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. V Průvodci importem virtuálního počítače > **před zahájením**, klikněte na tlačítko **Další**.
3. V **vyhledání složky**, zadejte složku, která obsahuje extrahovaný virtuálního pevného disku. Pak klikněte na tlačítko **Další**.
1. V **vybrat virtuální počítač**, klikněte na tlačítko **Další**.
2. V **vybrat typ importu**, klikněte na tlačítko **zkopírovat virtuální počítač (vytvořit nové jedinečné ID)** . Pak klikněte na tlačítko **Další**.
3. V **zvolit cíl**, ponechte výchozí nastavení. Klikněte na **Další**.
4. V **složky úložiště**, ponechte výchozí nastavení. Klikněte na **Další**.
5. V **výběr sítě**, zadejte virtuální přepínač, který bude používat virtuální počítač. Přepínač vyžaduje připojení k Internetu k odesílání dat do Azure.
6. V **Souhrn**, zkontrolujte nastavení. Pak klikněte na tlačítko **Dokončit**.
7. Ve Správci technologie Hyper-V > **virtuálních počítačů**, spusťte virtuální počítač.


### <a name="verify-appliance-access-to-azure"></a>Ověřit přístup zařízení k Azure

Ujistěte se, že zařízení virtuálního počítače může připojit k [adresám URL služby Azure](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

### <a name="configure-the-appliance"></a>Konfigurace zařízení

Nastavení zařízení poprvé.

1. Ve Správci technologie Hyper-V > **virtuálních počítačů**, klikněte pravým tlačítkem na virtuální počítač > **připojit**.
2. Zadejte jazyk, časové pásmo a heslo pro zařízení.
3. Otevřete prohlížeč na jakémkoli počítači můžete připojit k virtuálnímu počítači a otevřít adresu URL webové aplikace pro zařízení: **https://*zařízení název nebo IP adresu*: 44368**.

   Alternativně můžete otevřít aplikaci z plochy zařízení kliknutím na zástupce aplikace.
1. Ve webové aplikaci > **nastavit požadavky**, postupujte takto:
    - **Licence**: Přijměte licenční podmínky a přečtěte si informace třetích stran.
    - **Připojení**: Je aplikace zkontroluje, že virtuální počítač má přístup k Internetu. Pokud virtuální počítač používá proxy server:
        - Klikněte na tlačítko **nastavení proxy serveru**a zadejte adresu proxy serveru a nastavte naslouchající port ve tvaru http://ProxyIPAddress nebo http://ProxyFQDN.
        - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
        - Podporuje se jen proxy protokolu HTTP.
    - **Čas synchronizace**: Čas je ověřený. Čas na zařízení by měl být synchronizovaný s internetovým časem pro zjišťování virtuálních počítačů fungovalo správně.
    - **Instalace aktualizací**: Azure Migrate Server Assessment kontroluje, zda je zařízení nainstalované nejnovější aktualizace.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrace zařízení pomocí služby Azure Migrate

1. Klikněte na tlačítko **přihlášení**. Pokud se nezobrazí, ujistěte se, že jsme deaktivovali blokování automaticky otevíraných oken v prohlížeči.
2. Na nové kartě Přihlaste se pomocí přihlašovacích údajů Azure.
    - Přihlaste se pomocí uživatelského jména a hesla.
    - Přihlaste se pomocí kódu PIN není podporován.
3. Po úspěšném přihlášení přejděte zpět do webové aplikace.
4. Vyberte předplatné, ve kterém byl vytvořen projekt Azure Migrate. Vyberte projekt.
5. Zadejte název pro zařízení. Název musí být alfanumerické znaky se 14 znaků nebo méně.
6. Klikněte na tlačítko **zaregistrovat**.


### <a name="delegate-credentials-for-smb-vhds"></a>Delegování přihlašovacích údajů pro virtuální pevné disky protokolu SMB

Pokud používáte virtuální pevné disky na SMB, je nutné povolit delegování přihlašovacích údajů ze zařízení na hostitele Hyper-V. Pokud jste to neudělali na každém hostiteli ve [předchozí kurz o službě](tutorial-prepare-hyper-v.md#enable-credssp-on-hosts), proveďte tuto chybu ze zařízení:

1. U zařízení, virtuální počítač spusťte tento příkaz. HyperVHost1/HyperVHost2 jsou ukázkové názvy hostitelů.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Můžete také proveďte ve skupině Editoru místních zásad na zařízení:
    - V **zásady místního počítače** > **konfigurace počítače**, klikněte na tlačítko **šablony pro správu** > **systému**  >  **Delegování pověření**.
    - Dvakrát klikněte na panel **povolit delegování nové přihlašovací údaje**a vyberte **povoleno**.
    - V **možnosti**, klikněte na tlačítko **zobrazit**, a přidejte každého hostitele Hyper-V chcete zjišťovat do seznamu, s **wsman /** jako předponu.
    - V **delegování pověření**, dvakrát klikněte na panel **povolit delegování nové přihlašovací údaje s ověřováním NTLM server**. Znovu, přidejte každý hostitel Hyper-V chcete zjišťovat do seznamu, s **wsman /** jako předponu.

## <a name="start-continuous-discovery"></a>Spustit průběžné vyhledávání

Připojení ze zařízení na hostitelích Hyper-V nebo clusterů a spusťte zjišťování virtuálních počítačů.

1. V **uživatelské jméno** a **heslo**, zadejte přihlašovací údaje účtu, které zařízení použije ke zjištění virtuálních počítačů. Zadejte popisný název pro přihlašovací údaje a klikněte na tlačítko **uložit podrobnosti**.
2. Klikněte na tlačítko **přidat hostitele**a zadejte podrobnosti o hostiteli/clusteru Hyper-V.
3. Klikněte na tlačítko **ověření**. Po ověření se zobrazí počet virtuálních počítačů, které můžete zjistit na každém hostiteli/clusteru.
    - Pokud se ověření nezdaří pro hostitele, vyhodnoťte chybu ukázáním myší na ikonu **stav** sloupce. Vyřešte problémy a znovu ověřit.
    - Odebrat hostitele nebo clustery, vyberte > **odstranit**.
    - Pouze určitého hostitele nelze odebrat z clusteru. Můžete pouze odebrat celý cluster.
    - Cluster, můžete přidat i v případě, že dojde k problémům s konkrétní hostitele v clusteru.
4. Po ověření klikněte na tlačítko **uložit a spustit zjišťování** zahájíte proces zjišťování.

Tím se spustí zjišťování. Trvá přibližně 15 minut metadat zjištěných virtuálních počítačů se zobrazí na webu Azure Portal.

### <a name="verify-vms-in-the-portal"></a>Kontrola virtuálních počítačů na portálu

Po dokončení zjišťování, můžete ověřit, že se virtuální počítače na portálu.

1. Otevřete řídicí panel Azure Migrate.
2. V **Azure Migrate – servery** > **Azure Migrate: Server Assessment** klikněte na ikonu, která se zobrazí počet **zjištěných serverů**.

## <a name="set-up-an-assessment"></a>Nastavení posouzení

Existují dva typy posouzení, která můžete spustit pomocí Azure Migrate Server Assessment.

**Posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Posouzení na základě výkonu shromážděných dat | **Doporučená velikost virtuálního počítače**: Na základě dat využití procesoru a paměti.<br/><br/> **Typ disku (standard nebo premium spravovaného disku) doporučuje**: Na základě vstupně-výstupních operací a propustnosti s místními disky.
**Jako místní** | Posouzení podle velikosti na místě. | **Doporučená velikost virtuálního počítače**: Na základě velikosti virtuálního počítače místní<br/><br> **Typ disku doporučujeme**: Podle nastavení typ úložiště, které vyberete pro posouzení.



### <a name="run-an-assessment"></a>Spuštění posouzení

Spusťte posouzení následujícím způsobem:

1. Zkontrolujte [osvědčené postupy](best-practices-assessment.md) pro vytváření posouzení.
2. V **servery** > **Azure Migrate: Server Assessment**, klikněte na tlačítko **posouzení**.

    ![Posouzení](./media/tutorial-assess-hyper-v/assess.png)

3. V **vyhodnocení servery**, zadejte název posouzení.
4. Kliknutím na **Zobrazit vše** zobrazíte vlastnosti posouzení.

    ![Vlastnosti posouzení](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. V **vyberte nebo vytvořte skupinu**vyberte **vytvořit nový** a zadejte název skupiny. Skupina shromažďuje jeden nebo více virtuálních počítačů dohromady pro posouzení.
4. V **přidat počítače do skupiny**, vyberte virtuální počítače, které přidáte do skupiny.
5. Klikněte na tlačítko **vytvořit posouzení** k vytvoření skupiny a spusťte posouzení.

    ![Vytvoření posouzení](./media/tutorial-assess-hyper-v/assessment-create.png)

6. Jakmile bude posouzení hotové, zobrazíte ho v **servery** > **Azure Migrate: Server Assessment**.
7. Klikněte na **Exportovat posouzení** a stáhněte ho jako excelový soubor.


## <a name="review-an-assessment"></a>Kontrola posouzení

Popisuje posouzení:

- **Připravenost pro Azure**: Určuje, zda virtuální počítače jsou vhodné pro migraci do Azure.
- **Odhad měsíčních nákladů**: Odhadované měsíční úložnou a výpočetní náklady pro spouštění virtuálních počítačů v Azure.
- **Odhad nákladů na úložiště měsíční**: Odhadované náklady za úložiště na disku po migraci.


### <a name="view-an-assessment"></a>Zobrazení posouzení

1. V **cílů migrace** >  **servery** > **Azure Migrate: Server Assessment**, klikněte na tlačítko **posouzení**.
2. V **posouzení**, klikněte na posouzení ho otevřete.

    ![Souhrn hodnocení](./media/tutorial-assess-hyper-v/assessment-summary.png)


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

![Hodnocení spolehlivosti](./media/tutorial-assess-hyper-v/confidence-rating.png)

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

I nadále třetí dílem série, se naučíte migrovat virtuální počítače Hyper-V do Azure pomocí služby Azure Migrate serveru migrace.

> [!div class="nextstepaction"]
> [Migrace virtuálních počítačů Hyper-V](./tutorial-migrate-hyper-v.md)
