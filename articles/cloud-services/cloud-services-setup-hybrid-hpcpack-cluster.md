---
title: Nastavení clusteru HPC Pack hybridní v Azure | Microsoft Docs
description: Zjistěte, jak nastavit malá, hybridní s vysokým výkonem (HPC) clusteru pomocí sady Microsoft HPC Pack a Azure
services: cloud-services
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: hpc-pack
ms.assetid: ''
ms.service: cloud-services
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: danlep
ms.openlocfilehash: ad5c13723eef352148a40e3e7f4f2ff616867296
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2017
ms.locfileid: "23985302"
---
# <a name="set-up-a-hybrid-high-performance-computing-hpc-cluster-with-microsoft-hpc-pack-and-on-demand-azure-compute-nodes"></a>Nastavení hybridní vysokovýkonného výpočetního prostředí (HPC) clusteru pomocí sady Microsoft HPC Pack a na vyžádání Azure výpočetních uzlů
Nastavit malé, hybridní vysokovýkonného výpočetního prostředí (HPC) clusteru pomocí Microsoft HPC Pack 2012 R2 a Azure. Cluster, uvidíte v tomto článku se skládá z hlavního uzlu místními HPC Pack a některé výpočetní uzly, které nasazujete na vyžádání v Azure cloud service. Když pak spustíte výpočetní úlohy v hybridní clusteru.

![Hybridní clusteru HPC][Overview] 

Tento kurz ukazuje jeden ze způsobů, někdy označuje jako "shluků do cloudu," clusteru se použije ke spuštění aplikace náročné na výpočetní prostředky Azure, škálovatelnou a na vyžádání.

Tento kurz předpokládá žádné předchozí zkušenosti s výpočetní clustery nebo HPC Pack. Je určena pouze k vám pomohou při nasazení hybridního výpočetního clusteru rychle pro demonstrační účely. Požadavky a kroky k nasazení clusteru HPC Pack hybridní větší škálované v produkčním prostředí, nebo použít HPC Pack 2016, najdete v článku [podrobné pokyny](http://go.microsoft.com/fwlink/p/?LinkID=200493). Pro scénáře s HPC Pack, včetně automatizované nasazení clusteru ve virtuálních počítačích Azure, najdete v části [možnosti clusteru HPC pomocí sady Microsoft HPC Pack v Azure](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure** – Pokud nemáte předplatné Azure, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free/) si během několika minut.
* **Místní počítač se systémem Windows Server 2012 R2 nebo Windows Server 2012** -použít tento počítač jako hlavního uzlu clusteru prostředí HPC. Pokud už používáte Windows Server, můžete stáhnout a nainstalovat [zkušební verze](https://www.microsoft.com/evalcenter/evaluate-windows-server-2012-r2).
  
  * Počítač musí být připojen k doméně služby Active Directory. Pro účely testování můžete počítač hlavního uzlu nakonfigurovat jako řadič domény. Chcete-li přidat roli serveru Active Directory Domain Services a hlavního uzlu počítač jako řadič domény povýšit, najdete v dokumentaci k systému Windows Server.
  * Pro podporu HPC Pack, musí být nainstalován operační systém v jednom z těchto jazyků: angličtina, japonština nebo čínština (zjednodušená).
  * Ověřte, že se nainstalují důležité a kritické aktualizace.
* **HPC Pack 2012 R2** - [Stáhnout](http://go.microsoft.com/fwlink/p/?linkid=328024) instalaci balíčku na nejnovější verzi zdarma a zkopírujte soubory do počítače hlavního uzlu. Zvolte instalační soubory ve stejném jazyce jako vaše instalace systému Windows Server.

    >[!NOTE]
    > Pokud chcete používat HPC Pack 2016 místo HPC Pack 2012 R2, je potřeba další konfiguraci. Najdete v článku [podrobné pokyny](http://go.microsoft.com/fwlink/p/?LinkID=200493).
    > 
* **Účet domény** -tento účet musí být nakonfigurované oprávnění místního správce hlavního uzlu instalace sady HPC Pack.
* **Připojení TCP na portu 443** z hlavního uzlu do Azure.

## <a name="install-hpc-pack-on-the-head-node"></a>Instalace sady HPC Pack hlavního uzlu
První instalaci sady Microsoft HPC Pack v místní počítače se spuštěným systémem Windows Server. Tento počítač se změní na hlavního uzlu clusteru.

1. Přihlaste se k hlavnímu uzlu pomocí účtu domény, který má oprávnění místního správce.

2. Spusťte Průvodce instalací HPC Pack spuštěním Setup.exe z instalačních souborů HPC Pack.

3. Na **instalace HPC Pack 2012 R2** obrazovky, klikněte na tlačítko **nové instalace nebo přidání nových funkcí do existující instalace**.

    ![Instalace sady HPC Pack 2012][install_hpc1]

4. Na **stránku smlouvy uživatele softwaru Microsoft**, klikněte na tlačítko **Další**.

5. Na **vybrat typ instalace** klikněte na tlačítko **vytvoření nového clusteru HPC vytvořením hlavního uzlu**a potom klikněte na **Další**.

6. Průvodce spustí několik testy před instalací. Klikněte na tlačítko **Další** na **pravidla instalace** Pokud všechny testy byly úspěšné. Jinak si prohlédněte informace a proveďte potřebné změny ve vašem prostředí. Poté znovu spusťte testy nebo v případě potřeby spustit Průvodce instalací znovu.
7. Na **HPC DB konfigurace** se přesvědčte, že **hlavní uzel** pro všechny databáze HPC je vybrána a potom klikněte na **Další**. 

    ![Konfigurace databáze][install_hpc4]

8. Přijměte výchozí nastavení na dalších stránkách průvodce. Na **nainstalovat požadované součásti** klikněte na tlačítko **nainstalovat**.
   
    ![Instalace][install_hpc6]

9. Po dokončení instalace, zrušte zaškrtnutí políčka **Start Správce clusteru HPC** a pak klikněte na **Dokončit**. (V pozdější fázi spusťte Správce clusteru HPC.)
   
    ![Dokončit][install_hpc7]

## <a name="prepare-the-azure-subscription"></a>Příprava předplatné Azure
Proveďte následující kroky v [portál Azure](https://portal.azure.com) s předplatným Azure. Po dokončení těchto kroků, můžete nasadit Azure uzly z hlavního uzlu na místě. 
  
  > [!NOTE]
  > Také si poznamenejte ID vašeho předplatného Azure, které budete potřebovat později. Najít ID v **odběry** na portálu.
  > 

### <a name="upload-the-default-management-certificate"></a>Odešlete certifikát správy výchozí
HPC Pack nainstaluje certifikát podepsaný svým držitelem z hlavního uzlu, nazývá výchozí Microsoft HPC Azure Management certifikát, který nahrajete jako certifikát pro správu Azure. Tento certifikát slouží pouze k testování a testování konceptu nasazení k zabezpečení připojení mezi Azure a hlavního uzlu.

1. Z hlavního uzlu počítače, přihlaste se k [portál Azure](https://portal.azure.com).

2. Klikněte na tlačítko **odběry** > *your_subscription_name*.

3. Klikněte na tlačítko **certifikáty pro správu** > **nahrát**.

4. Přejděte na hlavního uzlu pro 2012\Bin\hpccert.cer C:\Program Files\Microsoft HPC Pack souboru. Potom klikněte na **nahrát**.

   
**Výchozí HPC Azure Management** certifikát se zobrazí v seznamu certifikátů pro správu.

### <a name="create-an-azure-cloud-service"></a>Vytvoření cloudové služby Azure
> [!NOTE]
> Pro nejlepší výkon vytvoření cloudové služby a účet úložiště (v pozdější fázi) ve stejné geografické oblasti.
> 
> 

1. Na portálu, klikněte na tlačítko **cloudových služeb (klasické)** > **+ přidat**.

2.  Zadejte název DNS pro službu, zvolte skupinu prostředků a umístění a pak klikněte na tlačítko **vytvořit**.


### <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure
1. Na portálu, klikněte na tlačítko **účty úložiště (klasické)** > **+ přidat**.

2. Zadejte název pro účet, vyberte **Classic** modelu nasazení.

3. Vyberte skupinu prostředků a umístění a nechte ostatní nastavení na výchozí hodnoty. Poté klikněte na **Vytvořit**.

## <a name="configure-the-head-node"></a>Konfigurace hlavního uzlu
Pomocí Správce clusteru HPC nasazení uzlů Azure a odesílání úloh, nejprve provést některé požadované kroky konfigurace clusteru.

1. Z hlavního uzlu spusťte Správce clusteru HPC. Pokud **vyberte hlavní uzel** se zobrazí dialogové okno, klikněte na tlačítko **místního počítače**. **Nasazení na seznam úkolů** se zobrazí.

2. V části **požadované úlohy nasazení**, klikněte na tlačítko **konfigurace sítě**.
   
    ![Konfigurace sítě][config_hpc2]

3. V Průvodci konfigurací sítě vyberte **všechny uzly pouze v podnikové síti** (topologie 5). Tato konfigurace sítě je nejjednodušší pro demonstrační účely.
   
    ![Topologie 5][config_hpc3]
   
4. Klikněte na tlačítko **Další** přijměte výchozí hodnoty na dalších stránkách průvodce. Potom na **zkontrolujte** , klikněte na **konfigurace** k dokončení konfigurace sítě.

5. V **nasazení na seznam úkolů**, klikněte na tlačítko **zadejte pověření pro instalaci**.

6. V **pověření pro instalaci** dialogovém okně zadejte přihlašovací údaje účtu domény, který jste použili k instalaci sady HPC Pack. Pak klikněte na **OK**. 
   
    ![Pověření pro instalaci][config_hpc6]
   
7. V **nasazení na seznam úkolů**, klikněte na tlačítko **konfigurace pojmenování nové uzly**.

8. V **zadejte řady pojmenování uzlu** dialogové okno pole, přijměte výchozí názvy řady a klikněte na tlačítko **OK**. Dokončení tohoto kroku, i když Azure uzly, které přidáte v tomto kurzu jsou pojmenované automaticky.
   
    ![Pojmenování uzlu][config_hpc8]
   
9. V **nasazení na seznam úkolů**, klikněte na tlačítko **vytvoření šablony uzlu**. Později v tomto kurzu použijete k přidání uzlů Azure do clusteru šablony uzlu.

10. V uzlu Průvodce vytvořením šablony postupujte takto:
    
    a. Na **výběr typu šablony uzlu** klikněte na tlačítko **šablony uzlu služby Windows Azure**a potom klikněte na **Další**.
    
    ![Šablony uzlu][config_hpc10]
    
    b. Klikněte na tlačítko **Další** přijměte výchozí název šablony.
    
    c. Na **poskytují informace o předplatném** zadejte svoje ID předplatného Azure (k dispozici v informací o vašem účtu Azure). Potom v **certifikát pro správu**, vyhledejte **výchozí Microsoft HPC Azure Management.** Pak klikněte na tlačítko **Další**.
    
    ![Šablony uzlu][config_hpc12]
    
    d. Na **poskytují informace o službě** vyberte cloudové služby a účet úložiště, který jste vytvořili v předchozím kroku. Pak klikněte na tlačítko **Další**.
    
    ![Šablony uzlu][config_hpc13]
    
    e. Klikněte na tlačítko **Další** přijměte výchozí hodnoty na dalších stránkách průvodce. Potom na **zkontrolujte** , klikněte na **vytvořit** k vytvoření šablony uzlu.
    
    > [!NOTE]
    > Ve výchozím nastavení Azure uzlu šablona obsahuje nastavení pro spuštění (zřídit) a zastavte uzly ručně, pomocí Správce clusteru HPC. Volitelně můžete nakonfigurovat plán, který chcete spustit a zastavit uzlů Azure automaticky.
    > 
    > 

## <a name="add-azure-nodes-to-the-cluster"></a>Přidání uzlů Azure do clusteru
Uzel šablony je teď možné použijte k přidání uzlů Azure do clusteru. Přidání uzlu do clusteru ukládá informace o jejich konfiguraci tak, aby bylo možné spustit (zřídit) je kdykoli v rámci cloudové služby. Vaše předplatné pouze získá účtovat uzlů Azure po instance běží v cloudové službě.

Postupujte podle těchto kroků přidejte dva uzly malé.

1. Ve Správci clusteru HPC, klikněte na tlačítko **uzlu správy** (nazývá **Správa prostředků** v aktuálních verzích HPC Pack) > **přidat uzel**.
   
    ![Přidání uzlu][add_node1]

2. Průvodce přidáním uzlu na **vybrat způsob nasazení** klikněte na tlačítko **systému Windows Azure přidat uzly**a potom klikněte na **Další**.
   
    ![Přidání uzlu Azure][add_node1_1]

3. Na **zadejte nové uzly** vyberte dříve vytvořenou šablonu Azure uzlu (nazývá ve výchozím nastavení **výchozí šablonu AzureNode**). Pak zadejte **2** uzly velikosti **malé**a potom klikněte na **Další**.
   
    ![Určit uzlů][add_node2]
   
4. Na **dokončení Průvodce přidáním uzlu** klikněte na tlačítko **Dokončit**.
    
     Dva uzly Azure s názvem **AzureCN 0001** a **AzureCN 0002**, se nyní zobrazí ve Správci clusteru HPC. Jsou obě **není nasazena** stavu.
   
    ![Přidání uzlů][add_node3]

## <a name="start-the-azure-nodes"></a>Spuštění uzlů Azure
Pokud chcete použít prostředků clusteru ve službě Azure, pomocí Správce clusteru HPC spustíte uzly (zřídit) Azure a jejich převedení do online režimu.

1. Ve Správci clusteru HPC, klikněte na tlačítko **uzlu správy** (nazývá **Správa prostředků** v aktuálních verzích HPC Pack) a vyberte uzly Azure.

2. Klikněte na tlačítko **spustit**a potom klikněte na **OK**.
   
   ![Počáteční uzly][add_node4]
   
    Uzly přechod do **zřizování** stavu. Zobrazte zřizování protokolu, který chcete sledovat průběh zřizování.
   
    ![Zřízení uzly][add_node6]

3. Po několika minutách uzlů Azure zřídit a jsou v **Offline** stavu. V tomto stavu instance role běží, ale ještě nepovoluje úlohy clusteru.

4. Chcete-li potvrdit, že instance role běží na portálu Azure klikněte na tlačítko **cloudové služby (klasické)** > *your_cloud_service_name*.
   
   Měli byste vidět dvě **HpcWorkerRole** instancí (uzlů) spuštěných ve službě. HPC Pack také automaticky nasadí dvě **HpcProxy** instance (velikost střední) pro zpracování komunikace mezi hlavního uzlu a Azure.

   ![Spuštěné instance][view_instances1]

5. Aby Azure uzly online ke spuštění úloh clusteru vyberte uzly, klikněte pravým tlačítkem a pak klikněte na tlačítko **přepnout do režimu Online**.
   
    ![Offline uzly][add_node7]
   
    Správce clusteru HPC označuje, že uzly jsou v **Online** stavu.

## <a name="run-a-command-across-the-cluster"></a>Spuštění příkazu napříč clusterem
Pokud chcete zkontrolovat instalace, použití HPC Pack **clusrun** příkaz ke spuštění příkazu nebo aplikace na jeden nebo více uzlech clusteru. Jako jednoduchý příklad, použít **clusrun** získat konfiguraci IP uzlů Azure.

1. Z hlavního uzlu otevřete příkazový řádek jako správce.

2. Zadejte následující příkaz:
   
    `clusrun /nodes:azurecn* ipconfig`

3. Pokud se zobrazí výzva, zadejte heslo správce clusteru. Měli byste vidět výstup příkazu podobný následujícímu.
   
    ![Clusrun][clusrun1]

## <a name="run-a-test-job"></a>Spustit úlohu testu
Teď odešlete testovací úlohu, která běží na clusteru hybridní. V tomto příkladu je úloha jednoduché čištění parametrů (typ vnitřně paralelní výpočty). Tento příklad spustí dílčí úkoly, které přidat celé sama se sebou pomocí **nastavit /a** příkaz. Všechny uzly v clusteru podílet se na dokončení dílčí úkoly pro celá čísla od 1 do 100.

1. Ve Správci clusteru HPC, klikněte na tlačítko **úlohy správy** > **novou čištění úlohu oblouku**.
   
    ![Nová úloha][test_job1]

2. V **novou čištění úlohu oblouku** dialogu **příkazového řádku**, typ `set /a *+*` (přepsání výchozího příkazového řádku, který se zobrazí). Ponechte výchozí hodnoty pro zbývající nastavení a potom klikněte na **odeslání** k odeslání úlohy.
   
    ![Čištění parametrů][param_sweep1]

3. Po dokončení úlohy dvakrát klikněte **Moje oblouku – úloha** úlohy.

4. Klikněte na tlačítko **úlohy v zobrazení**a potom klikněte na dílčí úlohy k zobrazení výstupu počítané na stejné úrovni.
   
    ![Výsledky úlohy][view_job361]

5. Chcete-li zjistit, který uzel provádí výpočet pro tento dílčí úlohy, klikněte na tlačítko **přidělené uzly**. (Váš cluster může zobrazovat název jiného uzlu.)
   
    ![Výsledky úlohy][view_job362]

## <a name="stop-the-azure-nodes"></a>Zastavit uzlů Azure
Po můžete vyzkoušet na clusteru, zastavte uzlů Azure, aby se zabránilo zbytečným poplatky ke svému účtu. Tento krok zastaví cloudové služby a odebere instance rolí Azure.

1. V modulu Správce clusteru HPC v **uzlu správy** (nazývá **Správa prostředků** v předchozích verzích HPC Pack), vyberte oba uzly Azure. Potom klikněte na **Zastavit**.
   
    ![Zastavit uzly][stop_node1]

2. V **zastavit systému Windows Azure uzly** dialogové okno, klikněte na tlačítko **Zastavit**. 
   
3. Uzly přechod do **zastavení** stavu. Po několika minutách, Správce clusteru HPC ukazuje, že uzly jsou **nasazení není**.
   
    ![Nenasazeno uzly][stop_node4]

4. Chcete-li potvrďte, že instance role jsou spuštěné v Azure, na portálu Azure klikněte na tlačítko **cloudových služeb (klasické)** > *your_cloud_service_name*. Žádné instance jsou nasazeny v provozním prostředí. 
   
    Dokončení tohoto kurzu.

## <a name="next-steps"></a>Další kroky
* Prozkoumat v dokumentaci k [HPC Pack](https://technet.microsoft.com/library/cc514029).
* Hybridní nasazení clusteru HPC Pack větší Škálované, naleznete v tématu [rozšíření do instance rolí pracovního procesu Azure pomocí sady Microsoft HPC Pack](http://go.microsoft.com/fwlink/p/?LinkID=200493).
* Další způsoby vytváření clusteru HPC Pack v Azure, včetně pomocí šablon Azure Resource Manageru, najdete v tématu [možnosti clusteru HPC pomocí sady Microsoft HPC Pack v Azure](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


[Overview]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/hybrid_cluster_overview.png
[install_hpc1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc1.png
[install_hpc4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc4.png
[install_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc6.png
[install_hpc7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc7.png
[install_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc10.png
[config_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc2.png
[config_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc3.png
[config_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc6.png
[config_hpc8]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc8.png
[config_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc10.png
[config_hpc12]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc12.png
[config_hpc13]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc13.png
[add_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1.png
[add_node1_1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1_1.png
[add_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node2.png
[add_node3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node3.png
[add_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node4.png
[add_node6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node6.png
[add_node7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node7.png
[view_instances1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances1.png
[clusrun1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/clusrun1.png
[test_job1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/test_job1.png
[param_sweep1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/param_sweep1.png
[view_job361]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job361.png
[view_job362]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job362.png
[stop_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node1.png
[stop_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node4.png
[view_instances2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances2.png
