---
title: Nastavení hybridního clusteru HPC Pack v Azure | Dokumentace Microsoftu
description: Zjistěte, jak nastavit malá, vysoký výkon hybridního výpočetního prostředí (HPC) clusteru pomocí sady Microsoft HPC Pack a Azure
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
ms.openlocfilehash: 55dfd7e5ea93ae941d73612cc70ed82d48db725a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51236734"
---
# <a name="set-up-a-hybrid-high-performance-computing-hpc-cluster-with-microsoft-hpc-pack-and-on-demand-azure-compute-nodes"></a>Nastavení clusteru HPC s využitím sady Microsoft HPC Pack a výpočetních uzlů Azure na vyžádání
Použijte Microsoft HPC Pack 2012 R2 a Azure k nastavení malé, hybridní vysokovýkonného výpočetního prostředí (HPC) clusteru. Cluster, uvidíte v tomto článku se skládá z hlavního uzlu v místním sady HPC Pack a některé výpočetní uzly, které můžete nasadit na vyžádání v Azure cloudové služby. Poté lze výpočetní úlohy v hybridním clusteru.

![Hybridního clusteru HPC][Overview] 

Tento kurz ukazuje, jedním z přístupů, někdy nazývá "rozšíření do cloudu," clusteru se použije ke spuštění aplikace náročné na výpočetní prostředky Azure škálovatelné a na vyžádání.

Tento kurz předpokládá žádné předchozí zkušenosti s výpočetní clustery nebo sady HPC Pack. Je určena pouze usnadňuje nasazení hybridního výpočetního clusteru rychle pro demonstrační účely. Požadavky a kroky k nasazení hybridního clusteru HPC Pack ve větším měřítku v produkčním prostředí nebo použít HPC Pack 2016, najdete v článku [podrobné pokyny](https://go.microsoft.com/fwlink/p/?LinkID=200493). Pomocí sady HPC Pack jiné scénáře, včetně automatického nasazení clusteru v Azure virtual machines, přečtěte si téma [možností clusteru HPC pomocí sady Microsoft HPC Pack v Azure](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure** – Pokud nemáte předplatné Azure, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free/) během několika minut.
* **V místním počítači se systémem Windows Server 2012 R2 nebo Windows Server 2012** -použít tento počítač jako k hlavnímu uzlu clusteru prostředí HPC. Pokud již nemáte spuštěný Windows Server, můžete stáhnout a nainstalovat [zkušební verze](https://www.microsoft.com/evalcenter/evaluate-windows-server-2012-r2).
  
  * Počítač musí být připojený k doméně služby Active Directory. Pro účely testování můžete počítač hlavního uzlu nakonfigurovat jako řadič domény. Chcete-li přidat role serveru Active Directory Domain Services a počítač hlavního uzlu jako řadič domény povýšit, naleznete v dokumentaci k Windows serveru.
  * Pro podporu sady HPC Pack, musí být nainstalovaný operační systém v jednom z těchto jazyků: angličtina, japonštinu nebo čínštinu (zjednodušenou).
  * Ověřte, že jsou nainstalované důležité a důležitými aktualizacemi.
* **HPC Pack 2012 R2** - [Stáhnout](https://go.microsoft.com/fwlink/p/?linkid=328024) instalace balíčku pro nejnovější verzi zdarma a zkopírujte soubory do počítače hlavního uzlu. Zvolte instalační soubory ve stejném jazyce jako instalace Windows serveru.

    >[!NOTE]
    > Pokud chcete použít místo HPC Pack 2012 R2 HPC Pack 2016, není nutné provádět další konfiguraci. Zobrazit [podrobné pokyny](https://go.microsoft.com/fwlink/p/?LinkID=200493).
    > 
* **Účet domény** – tento účet musí mít nakonfigurovanou oprávnění místního správce k hlavnímu uzlu instalace sady HPC Pack.
* **Připojení TCP na portu 443** z hlavního uzlu do Azure.

## <a name="install-hpc-pack-on-the-head-node"></a>Instalace sady HPC Pack hlavního uzlu
První instalaci sady Microsoft HPC Pack v místní počítače se systémem Windows Server. Tento počítač stane k hlavnímu uzlu clusteru.

1. Přihlaste se k hlavnímu uzlu pomocí účtu domény, který má oprávnění místního správce.

2. Spusťte Průvodce instalací prostředí HPC Pack spuštěním Setup.exe z instalačních souborů sady HPC Pack.

3. Na **instalace sady HPC Pack 2012 R2** obrazovce, klikněte na tlačítko **nové instalace nebo přidání nových funkcí do existující instalace**.

    ![Instalace sady HPC Pack 2012][install_hpc1]

4. Na **stránka smlouvou Software**, klikněte na tlačítko **Další**.

5. Na **vybrat typ instalace** klikněte na **vytvořit nový cluster prostředí HPC tak, že vytvoříte hlavní uzel**a potom klikněte na tlačítko **Další**.

6. Spuštění Průvodce několik testů před instalací. Klikněte na tlačítko **Další** na **pravidla instalace** stránky, pokud všechny testy byly úspěšné. V opačném případě zkontrolujte zadané informace a proveďte potřebné změny ve vašem prostředí. Následné spuštění testů nebo v případě potřeby spusťte Průvodce instalací znovu.
7. Na **konfiguraci databáze HPC** stránky, ujistěte se, že **hlavní uzel** je vybrán pro všemi databázemi HPC a potom klikněte na tlačítko **Další**. 

    ![Konfiguraci databáze][install_hpc4]

8. Přijměte výchozí výběry na zbývajících stránkách průvodce. Na **nainstalovat požadované součásti** klikněte na **nainstalovat**.
   
    ![Instalace][install_hpc6]

9. Po dokončení instalace, zrušte zaškrtnutí políčka **spustit Správce clusterů HPC** a potom klikněte na tlačítko **Dokončit**. (V pozdějším kroku spustit Správce clusterů HPC.)
   
    ![Dokončit][install_hpc7]

## <a name="prepare-the-azure-subscription"></a>Příprava předplatného Azure
Proveďte následující kroky v [webu Azure portal](https://portal.azure.com) ve vašem předplatném Azure. Po dokončení těchto kroků můžete nasadit Azure uzly z hlavního uzlu místní. 
  
  > [!NOTE]
  > Také poznamenejte ID vašeho předplatného Azure, které budete potřebovat později. Najít ID v **předplatná** na portálu.
  > 

### <a name="upload-the-default-management-certificate"></a>Nahrát certifikát pro správu výchozí
HPC Pack nainstaluje certifikát podepsaný svým držitelem hlavního uzlu, nazývá výchozí Microsoft Azure Management HPC certifikát, který můžete nahrát jako certifikát pro správu Azure. Tento certifikát je k dispozici pro testování a testování konceptu nasazení na zabezpečené připojení mezi hlavní uzel a Azure.

1. Z hlavního uzlu počítače, přihlaste se k [webu Azure portal](https://portal.azure.com).

2. Klikněte na tlačítko **předplatná** > *your_subscription_name*.

3. Klikněte na tlačítko **certifikáty pro správu** > **nahrát**.

4. Přejděte na hlavní uzel pro 2012\Bin\hpccert.cer C:\Program Files\Microsoft HPC Pack souboru. Potom klikněte na **nahrát**.

   
**Výchozí HPC Azure Management** certifikátu se zobrazí v seznamu certifikáty pro správu.

### <a name="create-an-azure-cloud-service"></a>Vytvoření cloudové služby Azure
> [!NOTE]
> Pro zajištění nejlepšího výkonu vytvořte cloudovou službu a účet úložiště (v pozdějším kroku) ve stejné zeměpisné oblasti.
> 
> 

1. Na portálu klikněte na tlačítko **cloudové služby (klasické)** > **+ přidat**.

2.  Zadejte název DNS pro služby, zvolte skupinu prostředků a umístění a klikněte na **vytvořit**.


### <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure
1. Na portálu klikněte na tlačítko **účty úložiště (klasické)** > **+ přidat**.

2. Zadejte název účtu a vyberte **Classic** modelu nasazení.

3. Zvolte skupinu prostředků a umístění a další nastavení ponechte výchozí hodnoty. Poté klikněte na **Vytvořit**.

## <a name="configure-the-head-node"></a>Konfigurace hlavního uzlu
Nasazení Azure uzly a odesílání úloh pomocí Správce clusterů HPC, nejprve proveďte několik kroků konfigurace požadovaných clusterových.

1. Hlavního uzlu spusťte Správce clusterů HPC. Pokud **vyberte hlavní uzel** dialogové okno se zobrazí, klikněte na tlačítko **místního počítače**. **Seznam úkolů nasazení** se zobrazí.

2. V části **požadované úlohy nasazení**, klikněte na tlačítko **konfigurace sítě**.
   
    ![Konfigurace sítě][config_hpc2]

3. V Průvodci konfigurací sítě, vyberte **všechny uzly pouze v podnikové síti** (topologie 5). Tato konfigurace sítě je nejjednodušší pro demonstrační účely.
   
    ![Topologie 5][config_hpc3]
   
4. Klikněte na tlačítko **Další** přijměte výchozí hodnoty na zbývajících stránkách průvodce. Potom na **revize** klikněte na tlačítko **konfigurovat** k dokončení konfigurace sítě.

5. V **seznam úkolů nasazení**, klikněte na tlačítko **zadejte přihlašovací údaje instalace**.

6. V **instalační přihlašovací údaje** dialogového okna zadejte přihlašovací údaje účtu domény, který jste použili k instalaci sady HPC Pack. Pak klikněte na **OK**. 
   
    ![Instalační přihlašovací údaje][config_hpc6]
   
7. V **seznam úkolů nasazení**, klikněte na tlačítko **konfigurace pojmenování nové uzly**.

8. V **zadejte řadu názvů uzlu** dialogové okno pole, přijměte výchozí nastavení pojmenování řady a klikněte na tlačítko **OK**. Tento krok proveďte, i když Azure uzly, které v tomto kurzu přidáte jsou pojmenovány automaticky.
   
    ![Pojmenování uzlu][config_hpc8]
   
9. V **seznam úkolů nasazení**, klikněte na tlačítko **vytvoření šablony uzlu**. Později v tomto kurzu použijete šablony uzlu k přidání uzlů Azure do clusteru.

10. V uzlu Průvodce vytvořením šablony postupujte takto:
    
    a. Na **zvolte typ šablony uzlu** klikněte na **šablony uzlu Windows Azure**a potom klikněte na tlačítko **Další**.
    
    ![Uzel šablony][config_hpc10]
    
    b. Klikněte na tlačítko **Další** přijměte výchozí název šablony.
    
    c. Na **poskytují informace o předplatném** stránky, zadejte ID svého předplatného Azure (k dispozici v informace o vašem účtu Azure). Potom v **certifikát pro správu**, vyhledejte **výchozí Microsoft Azure Management HPC.** Pak klikněte na tlačítko **Další**.
    
    ![Uzel šablony][config_hpc12]
    
    d. Na **poskytují informace o službě** vyberte cloudovou službu a účet úložiště, který jste vytvořili v předchozím kroku. Pak klikněte na tlačítko **Další**.
    
    ![Uzel šablony][config_hpc13]
    
    e. Klikněte na tlačítko **Další** přijměte výchozí hodnoty na zbývajících stránkách průvodce. Potom na **revize** klikněte na tlačítko **vytvořit** k vytvoření šablony uzlu.
    
    > [!NOTE]
    > Ve výchozím nastavení uzlu Azure šablona obsahuje nastavení umožňující (zřizování) spouští a zastavují uzly ručně, pomocí Správce clusteru HPC. Volitelně můžete nakonfigurovat plán, který chcete spustit a zastavit uzlů Azure automaticky.
    > 
    > 

## <a name="add-azure-nodes-to-the-cluster"></a>Přidání uzlů Azure do clusteru
Teď pomocí šablony uzlu k přidání uzlů Azure do clusteru. Přidání uzlů do clusteru ukládá informace o jejich konfiguraci, aby mohla začít (zřizování) je v každém okamžiku v cloudové službě. Vaše předplatné získá účtovat jenom uzly Azure po instance běží v cloudové službě.

Postupujte podle těchto kroků přidejte dva uzly malé.

1. V modulu Správce clusteru HPC, klikněte na tlačítko **uzel správy** (volá **správy prostředků** v aktuálních verzích sady HPC Pack) > **přidat uzel**.
   
    ![Přidat uzel][add_node1]

2. V průvodci Přidat uzel na **vybrat způsob nasazení** klikněte na **uzlů Windows Azure přidat**a potom klikněte na tlačítko **Další**.
   
    ![Přidání uzlu Azure][add_node1_1]

3. Na **zadat nové uzly** vyberte dříve vytvořenou šablonu uzlu Azure (volá se ve výchozím nastavení **výchozí šablona AzureNode**). Pak zadejte **2** uzly velikosti **malé**a potom klikněte na tlačítko **Další**.
   
    ![Určete uzlů][add_node2]
   
4. Na **dokončuje se Průvodce přidáním uzlu** klikněte na **Dokončit**.
    
     Dva uzly Azure, s názvem **AzureCN 0001** a **AzureCN 0002**, teď budou zobrazovat v modulu Správce clusteru HPC. Obě jsou v **není nasazený** stavu.
   
    ![Přidání uzlů][add_node3]

## <a name="start-the-azure-nodes"></a>Spustit Azure uzly
Pokud chcete používat prostředky clusteru v Azure, použijte Správce clusterů HPC spustit uzly (zřizování) Azure a přiřaďte je online.

1. V modulu Správce clusteru HPC, klikněte na tlačítko **uzel správy** (volá **správy prostředků** v aktuálních verzích sady HPC Pack) a vyberte uzly, Azure.

2. Klikněte na tlačítko **Start**a potom klikněte na tlačítko **OK**.
   
   ![Počáteční uzly][add_node4]
   
    Pro přechod uzlů **zřizování** stavu. Zobrazte protokol zřizování můžete sledovat průběh zřizování.
   
    ![Zřízení uzly][add_node6]

3. Po několika minutách uzlů Azure dokončit zřizování a jsou v **Offline** stavu. V tomto stavu instance rolí jsou spuštěné, ale ještě nemůže přijímat úlohy clusteru.

4. Zkontrolujte, že instance role běží na webu Azure Portal, klikněte na tlačítko **cloudové služby (klasické)** > *your_cloud_service_name*.
   
   Měli byste vidět dvě **HpcWorkerRole** spuštěných ve službě instancí (uzly). HPC Pack také automaticky nasadí dvě **HpcProxy** instance (velikost) pro zpracování komunikaci mezi hlavní uzel a Azure.

   ![Spuštění instancí][view_instances1]

5. Na používání Azure uzly online můžete spouštět úlohy clusteru, vyberte uzly, klikněte pravým tlačítkem a pak klikněte na tlačítko **přepnout do režimu Online**.
   
    ![Uzly v režimu offline][add_node7]
   
    Správce clusterů HPC označuje, zda jsou uzly ve **Online** stavu.

## <a name="run-a-command-across-the-cluster"></a>Spuštění příkazu v clusteru
Kontrola instalace, použití sady HPC Pack **clusrun** příkaz ke spuštění příkazu nebo aplikací na jeden nebo více uzlech clusteru. Jako jednoduchý příklad, použijte **clusrun** získat konfiguraci IP uzlů Azure.

1. Na hlavní uzel otevřete příkazový řádek jako správce.

2. Zadejte následující příkaz:
   
    `clusrun /nodes:azurecn* ipconfig`

3. Pokud se zobrazí výzva, zadejte heslo správce clusteru. Měli byste vidět příkaz výstup podobný následujícímu.
   
    ![Clusrun][clusrun1]

## <a name="run-a-test-job"></a>Spustit testovací úlohy
Nyní odešlete testovací úlohy, na kterém běží v hybridním clusteru. V tomto příkladu je úloha jednoduché čištění parametrů (typ vnitřně paralelní zpracování). Tento příklad se spustí dílčí úkoly, které aplikacím dodávají celé sám na sebe pomocí **nastavit /a** příkazu. Všechny uzly v clusteru přispívat k dokončení dílčí úkoly pro celá čísla od 1 do 100.

1. V modulu Správce clusteru HPC, klikněte na tlačítko **Správa úloh** > **novou ukazatelů úlohu oblouku**.
   
    ![Nová úloha][test_job1]

2. V **novou ukazatelů úlohu oblouku** v dialogu **příkazového řádku**, typ `set /a *+*` (přepsání výchozího příkazového řádku, který se zobrazí). Ponechte výchozí hodnoty pro zbývající nastavení a potom klikněte na **odeslat** a odešlete úlohu.
   
    ![Čištění parametrů][param_sweep1]

3. Po dokončení úlohy se dvakrát klikněte **Moje úloha oblouku** úlohy.

4. Klikněte na tlačítko **úlohy v zobrazení**a potom klikněte na dílčí úkol k zobrazení počítané výstup dílčího úkolu.
   
    ![Výsledky úlohy][view_job361]

5. Pokud chcete zobrazit, který uzel provádí výpočet pro tuto dílčí úkol, klikněte na tlačítko **přidělené uzly**. (Váš cluster může zobrazovat název jiného uzlu.)
   
    ![Výsledky úlohy][view_job362]

## <a name="stop-the-azure-nodes"></a>Zastavit uzlů Azure
Po vyzkoušení clusteru zastavte Azure uzlů se vyhnout zbytečným poplatkům ke svému účtu. Tento krok cloudovou službu se zastaví a odebere instance Azure role.

1. V modulu Správce clusteru HPC v **uzel správy** (volá **správy prostředků** v předchozích verzích sady HPC Pack), vyberte oba uzly Azure. Potom klikněte na **Zastavit**.
   
    ![Zastavit uzly][stop_node1]

2. V **uzlů zastavit Windows Azure** dialogové okno, klikněte na tlačítko **Zastavit**. 
   
3. Pro přechod uzlů **zastavení** stavu. Po několika minutách se zobrazí Správce clusterů HPC, že uzly jsou **není nasazený**.
   
    ![Nenasazeno uzly][stop_node4]

4. Potvrďte, že instance rolí jsou spuštěné v Azure, na webu Azure Portal, klikněte na tlačítko **cloudové služby (klasické)** > *your_cloud_service_name*. Žádné instance se nasazují do produkčního prostředí. 
   
    Dokončení tohoto kurzu.

## <a name="next-steps"></a>Další postup
* Projděte si dokumentaci pro [sady HPC Pack](https://technet.microsoft.com/library/cc514029).
* Nastavení hybridní nasazení clusteru HPC Pack ve větším měřítku, najdete v článku [Burst to Azure instancí rolí pracovního procesu pomocí sady Microsoft HPC Pack](https://go.microsoft.com/fwlink/p/?LinkID=200493).
* Jiné způsoby vytvoření clusteru HPC Pack v Azure, včetně pomocí šablon Azure Resource Manageru, najdete v článku [možností clusteru HPC pomocí sady Microsoft HPC Pack v Azure](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


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
