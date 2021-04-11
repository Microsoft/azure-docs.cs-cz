---
title: Kurz – nastavení zotavení po havárii pro virtuální počítače se systémem Linux pomocí Azure Site Recovery
description: Naučte se, jak nastavit zotavení po havárii pro virtuální počítače se systémem Linux do jiné oblasti Azure pomocí služby Azure Site Recovery.
author: rayne-wiselman
ms.service: virtual-machines
ms.collection: linux
ms.subservice: recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b5e83f883b5e1e35842ab128e4732e993fb937a0
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383638"
---
# <a name="tutorial-set-up-disaster-recovery-for-linux-virtual-machines"></a>Kurz: nastavení zotavení po havárii pro virtuální počítače se systémem Linux

V tomto kurzu se dozvíte, jak nastavit zotavení po havárii pro virtuální počítače Azure se systémem Linux. V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> * Povolení zotavení po havárii pro virtuální počítač se systémem Linux
> * Spuštění postupu zotavení po havárii pro kontrolu, že funguje podle očekávání
> * Zastavení replikace virtuálního počítače po přechodu k podrobnostem

Když pro virtuální počítač povolíte replikaci, nainstalují se rozšíření služby Site Recovery mobility na virtuálním počítači a zaregistruje se v [Azure Site Recovery](../../site-recovery/site-recovery-overview.md). Během replikace se zápisy na disk virtuálních počítačů odesílají do účtu úložiště mezipaměti v oblasti zdrojového virtuálního počítače. Data se odesílají z umístění do cílové oblasti a body obnovení se generují z dat.  Když během zotavení po havárii dojde k selhání virtuálního počítače do jiné oblasti, použije se bod obnovení k vytvoření virtuálního počítače v cílové oblasti.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Požadavky

1. Ověřte, že vaše předplatné Azure umožňuje vytvořit virtuální počítač v cílové oblasti. Pokud jste právě vytvořili bezplatný účet Azure, jste správcem předplatného a máte potřebná oprávnění.
2. Pokud nejste správcem předplatného, obraťte se na správce, který vám přiřadí:
    - Buď předdefinovaná role přispěvatele virtuálních počítačů, nebo specifická oprávnění pro:
        - Vytvoření virtuálního počítače ve vybrané virtuální síti
        - Zápis do účtu služby Azure Storage.
        - Zapište na spravovaný disk Azure.
     - Předdefinovaná role Site Recovery přispěvatele pro správu operací Site Recovery v trezoru. 
3. Ověřte, že na virtuálním počítači se systémem Linux běží [podporovaný operační systém](../../site-recovery/azure-to-azure-support-matrix.md#linux).
4. Pokud odchozí připojení virtuálních počítačů používají proxy server založený na adrese URL, ujistěte se, že má přístup k těmto adresám URL. Použití ověřeného proxy serveru se nepodporuje.

    **Název** | **Veřejný cloud** | **Cloud pro státní správu** | **Podrobnosti**
    --- | --- | --- | ---
    Storage | `*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`| Zapište data z virtuálního počítače do účtu úložiště mezipaměti ve zdrojové oblasti. 
    Azure AD  | `login.microsoftonline.com` | `login.microsoftonline.us`| Autorizaci a ověření pro Site Recovery adres URL služby. 
    Replikace | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`  |Komunikace virtuálních počítačů se službou Site Recovery. 
    Service Bus | `*.servicebus.windows.net` | `*.servicebus.usgovcloudapi.net` | Virtuální počítače zapisuje Site Recovery pro monitorování a diagnostická data. 

4. Pokud k omezení síťového provozu pro virtuální počítače používáte skupiny zabezpečení sítě (skupin zabezpečení sítě), vytvořte pravidla NSG, která umožňují odchozí připojení (HTTPS 443) pro virtuální počítač pomocí těchto značek služeb (skupiny IP adres). Vyzkoušejte nejdřív pravidla na NSG testu.

    **Tag** | **Povolit** 
    --- | --- 
    Značka úložiště | Umožňuje zapisovat data z virtuálního počítače do účtu úložiště mezipaměti.
    Značka Azure AD | Umožňuje přístup ke všem IP adresám, které odpovídají službě Azure AD.
    Značka EventsHub | Umožňuje přístup k Site Recovery monitorování.
    Značka AzureSiteRecovery | Umožňuje přístup ke službě Site Recovery v libovolné oblasti.
    GuestAndHybridManagement | Tuto možnost použijte, pokud chcete automaticky upgradovat Site Recoveryho agenta mobility, který běží na virtuálních počítačích povolených pro replikaci.
5. Ujistěte se, že virtuální počítače mají nejnovější kořenové certifikáty. Na virtuálních počítačích se systémem Linux postupujte podle pokynů, které poskytuje distributor pro Linux, abyste získali nejnovější důvěryhodné kořenové certifikáty a seznam odvolaných certifikátů na virtuálním počítači.

## <a name="create-a-vm-and-enable-disaster-recovery"></a>Vytvoření virtuálního počítače a povolení zotavení po havárii

Případně můžete povolit zotavení po havárii při vytváření virtuálního počítače.

1. [Vytvořte virtuální počítač se systémem Linux](quick-create-portal.md).
2. Na kartě **Správa** v části **Site Recovery** vyberte **Povolit zotavení po havárii**.
3. V části **Sekundární oblast** vyberte cílovou oblast, do které chcete replikovat virtuální počítač pro zotavení po havárii.
4. V části **sekundární předplatné** vyberte cílové předplatné, ve kterém se vytvoří cílový virtuální počítač. Cílový virtuální počítač se vytvoří při převzetí služeb při selhání zdrojového virtuálního počítače ze zdrojové oblasti do cílové oblasti.
5. V části **trezor Recovery Services** vyberte trezor, který chcete pro replikaci použít. Pokud nemáte trezor, vyberte **vytvořit novou**. Vyberte skupinu prostředků, do které se má Trezor umístit, a název trezoru.
6. V části **zásady Site Recovery** ponechte výchozí zásadu nebo vyberte **vytvořit nové** a nastavte vlastní hodnoty.

    - Body obnovení jsou vytvářeny ze snímků disků virtuálních počítačů pořízených v určitém časovém okamžiku. Při převzetí služeb při selhání virtuálního počítače použijete bod obnovení k obnovení virtuálního počítače v cílové oblasti. 
    - Bod obnovení konzistentní vzhledem k selháním se vytvoří každých pět minut. Toto nastavení nelze změnit. Snímek konzistentní se selháním zachycuje data, která byla na disku při pořízení snímku. Neobsahuje žádné množství paměti. 
    - Ve výchozím nastavení Site Recovery udržuje body obnovení konzistentní vzhledem k chybě po dobu 24 hodin. Můžete nastavit vlastní hodnotu v rozmezí 0 až 72 hodin.
    - Snímek konzistentní vzhledem k aplikacím je pořízen každé 4 hodiny.
    - Ve výchozím nastavení Site Recovery ukládá body obnovení po dobu 24 hodin.

7. V části **Možnosti dostupnosti** určete, jestli se má virtuální počítač nasadit jako samostatný, v zóně dostupnosti nebo v sadě dostupnosti.

    :::image type="content" source="./media/tutorial-disaster-recovery/create-vm.png" alt-text="Povolte replikaci na stránce vlastností správy virtuálních počítačů.":::

8. Dokončete vytváření virtuálního počítače.

## <a name="enable-disaster-recovery-for-an-existing-vm"></a>Povolení zotavení po havárii pro existující virtuální počítač

Pokud chcete povolit zotavení po havárii pro existující virtuální počítač, použijte tento postup.

1. V Azure Portal otevřete stránku vlastností virtuálního počítače.
2. V části **Operace** vyberte **Zotavení po havárii**.

    :::image type="content" source="./media/tutorial-disaster-recovery/existing-vm.png" alt-text="Otevřete možnosti zotavení po havárii pro existující virtuální počítač.":::

3. V případě, že je virtuální počítač nasazený v zóně dostupnosti, můžete v nástroji **základy** vybrat možnost zotavení po havárii mezi zónami dostupnosti.
4. V části **cílová oblast** vyberte oblast, do které chcete virtuální počítač replikovat. Zdrojové a cílové oblasti musí být ve stejném Azure Active Directory tenantovi.

    :::image type="content" source="./media/tutorial-disaster-recovery/basics.png" alt-text="Nastavte základní možnosti zotavení po havárii pro virtuální počítač.":::

5. Vyberte **Další: Upřesnit nastavení**.
6. V **upřesňujících nastaveních** můžete zkontrolovat nastavení a upravit hodnoty na vlastní nastavení. Ve výchozím nastavení Site Recovery zrcadlí nastavení zdroje pro vytváření cílových prostředků.

    - **Cílové předplatné**. Předplatné, ve kterém se vytvoří cílový virtuální počítač po převzetí služeb při selhání.
    - **Cílová skupina prostředků virtuálního počítače** Skupina prostředků, ve které se vytvoří cílový virtuální počítač po převzetí služeb při selhání.
    - **Cílová virtuální síť**. Virtuální síť Azure, ve které se cílový virtuální počítač nachází při jeho vytvoření po převzetí služeb při selhání.
    - **Cílová dostupnost**. Když je cílový virtuální počítač vytvořený jako jediná instance, ve skupině dostupnosti nebo v zóně dostupnosti.
    - **Umístění blízkosti**. V případě potřeby vyberte skupinu umístění blízkosti, ve které se cílový virtuální počítač nachází po převzetí služeb při selhání.
    - **Nastavení úložiště – účet úložiště mezipaměti**. Obnovení používá účet úložiště ve zdrojové oblasti jako dočasné úložiště dat. Změny zdrojového virtuálního počítače se v tomto účtu ukládají do mezipaměti, než se replikují do cílového umístění.
        - Ve výchozím nastavení se pro každý trezor vytvoří jeden účet úložiště mezipaměti a znovu se použije.
        - Pokud chcete přizpůsobit účet mezipaměti pro virtuální počítač, můžete vybrat jiný účet úložiště.
    - **Nastavení úložiště – spravovaný disk repliky**. Ve výchozím nastavení Site Recovery vytvoří v cílové oblasti repliku spravované disky.
        -  Ve výchozím nastavení se na cílovém spravovaném disku zrcadlí zdrojové disky spravované virtuálním počítačem, a to pomocí stejného typu úložiště (standardní pevný disk/SSD nebo jednotka SSD úrovně Premium).
        - Typ úložiště můžete přizpůsobit podle potřeby.
    - **Nastavení replikace**. Zobrazuje trezor, ve kterém se virtuální počítač nachází, a zásady replikace používané pro virtuální počítač. Ve výchozím nastavení se body obnovení vytvořené Site Recovery pro virtuální počítač uchovávají po dobu 24 hodin.
    - **Nastavení rozšíření**. Indikuje, že Site Recovery spravuje aktualizace rozšíření služby mobility Site Recovery, které je nainstalované na virtuálních počítačích, které replikují.
        - Uvedený účet Azure Automation spravuje proces aktualizace.
        - Můžete přizpůsobit účet Automation.

    :::image type="content" source="./media/tutorial-disaster-recovery/settings-summary.png" alt-text="Stránka zobrazující souhrn nastavení cíle a replikace.":::

6. Vyberte **zkontrolovat a spustit replikaci**.

7. Vyberte **spustit replikaci**. Spustí se nasazení a Site Recovery spustí vytváření cílových prostředků. Průběh replikace můžete monitorovat v oznámeních.

    :::image type="content" source="./media/tutorial-disaster-recovery/notifications.png" alt-text="Oznámení o průběhu replikace":::

## <a name="check-vm-status"></a>Ověřte stav virtuálního počítače

Po dokončení úlohy replikace můžete zjistit stav replikace virtuálních počítačů.

1. Otevřete stránku vlastností virtuálního počítače.
2. V části **Operace** vyberte **Zotavení po havárii**.
3. V části **základy** si můžete prohlédnout výchozí informace o trezoru, zásadách replikace a nastaveních cíle.
4. V části stav **a stav** Získejte informace o stavu replikace pro virtuální počítač, verzi agenta, připravenost na převzetí služeb při selhání a nejnovější body obnovení. 

    :::image type="content" source="./media/tutorial-disaster-recovery/essentials.png" alt-text="Zobrazení základní verze pro zotavení po havárii virtuálního počítače.":::

5. V **zobrazení infrastruktura** Získejte vizuální přehled o zdrojovém a cílovém virtuálním počítači, spravovaných discích a účtu úložiště mezipaměti.

    :::image type="content" source="./media/tutorial-disaster-recovery/infrastructure.png" alt-text="vizuální mapa infrastruktury pro zotavení po havárii virtuálního počítače":::

## <a name="run-a-drill"></a>Spustit podrobnou analýzu

Spusťte podrobnou analýzu, abyste měli jistotu, že zotavení po havárii funguje podle očekávání. Při spuštění testovacího převzetí služeb při selhání se vytvoří kopie virtuálního počítače bez dopadu na probíhající replikaci nebo v produkčním prostředí.

1. Na stránce zotavení po havárii virtuálního počítače vyberte **Test převzetí služeb při selhání**.
2. V **testovacím převzetí služeb při selhání** ponechte výchozí nastavení **poslední zpracované (nízké RPO)** pro bod obnovení.

   Tato možnost poskytuje nejnižší cíl bodu obnovení (RPO) a obecně nastavuje virtuální počítač nejrychleji v cílové oblasti. Nejprve zpracuje všechna data, která byla odeslána do služby Site Recovery Service, aby bylo možné vytvořit bod obnovení pro každý virtuální počítač, než je služba převzala do provozu. V tomto bodu obnovení jsou všechna data replikována do Site Recovery při aktivaci převzetí služeb při selhání.

3. Vyberte virtuální síť, ve které se virtuální počítač bude nacházet po převzetí služeb při selhání. 

     :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-settings.png" alt-text="Stránku pro nastavení možností testovacího převzetí služeb při selhání.":::

4. Spustí se proces testovacího převzetí služeb při selhání. Průběh můžete sledovat v oznámeních.

    :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-notification.png" alt-text="Otestujte oznámení o převzetí služeb při selhání."::: 
    
   Po dokončení testovacího převzetí služeb při selhání se virtuální počítač nachází ve stavu *čeká na vyřízení testovacího převzetí služeb při selhání* na stránce **základy** . 
  
## <a name="clean-up-resources"></a>Vyčištění prostředků

Po zobrazení podrobností se virtuální počítač automaticky vyčistí Site Recovery. 
 
1. Chcete-li zahájit automatické vyčištění, vyberte možnost **vyčistit testovací převzetí služeb při selhání**.

    :::image type="content" source="./media/tutorial-disaster-recovery/start-cleanup.png" alt-text="Na stránce základy spusťte vyčištění."::: 

6. V části **čištění testovacího převzetí služeb při selhání** zadejte všechny poznámky, které chcete pro převzetí služeb při selhání zaznamenat, a pak vyberte **testování bylo dokončeno. Odstraňte virtuální počítač testovacího převzetí služeb při selhání**. Pak vyberte **OK**.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test.png" alt-text="Stránku pro záznam poznámek a odstranění testovacího virtuálního počítače."::: 

7. Proces odstranění začíná. Průběh můžete sledovat v oznámeních.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test-notification.png" alt-text="Oznámení pro monitorování odstranění testovacího virtuálního počítače"::: 


### <a name="stop-replicating-the-vm"></a>Zastavení replikace virtuálního počítače

Po dokončení postupu zotavení po havárii doporučujeme pokračovat ve vyzkoušení úplného převzetí služeb při selhání. Pokud nechcete provést úplné převzetí služeb při selhání, můžete replikaci zakázat. Provede následující akce:

- Odebere virtuální počítač ze seznamu Site Recovery replikovaných počítačů.
- Zastaví Site Recovery fakturaci pro virtuální počítač.
- Automaticky vyčistí nastavení replikace zdroje.

Následujícím způsobem zakažte replikaci:

1. Na stránce zotavení po havárii virtuálního počítače vyberte **Zakázat replikaci**.
2. V části **Zakázat replikaci** vyberte důvody, proč chcete replikaci zakázat. Pak vyberte **OK**.

    :::image type="content" source="./media/tutorial-disaster-recovery/disable-replication.png" alt-text="Na stránce zakažte replikaci a zadejte důvod."::: 


Rozšíření Site Recovery nainstalované na virtuálním počítači během replikace se neodebere automaticky. Pokud zakážete replikaci pro virtuální počítač a nechcete ho znovu replikovat později, můžete rozšíření Site Recovery odebrat ručně následujícím způsobem: 

1. Přejít na rozšíření **Nastavení** virtuálního počítače >  >  .
2. Na stránce **rozšíření** vyberte každou položku *Microsoft. Azure. RecoveryServices* pro Linux.
3. Na stránce vlastnosti rozšíření vyberte možnost **odinstalovat**.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali zotavení po havárii pro virtuální počítač Azure a spustili do něj podrobné pokyny pro zotavení po havárii. Nyní můžete pro virtuální počítač provést úplné převzetí služeb při selhání.

> [!div class="nextstepaction"]
> [Převzetí služeb při selhání virtuálního počítače do jiné oblasti](../../site-recovery/azure-to-azure-tutorial-dr-drill.md)
