---
title: Cluster HPC Pack pro aplikaci Excel a SOA | Dokumentace Microsoftu
description: Začínáme se spouštěním rozsáhlých úloh Excelu a SOA v clusteru HPC Pack v Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,hpc-pack
ms.assetid: cb6a9abe-caf3-44da-b911-849a50f6cfb3
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/01/2017
ms.author: danlep
ms.openlocfilehash: f5b8d3aa69d6a141394395f012e5cc57873cafaf
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235929"
---
# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>Začínáme se spouštěním úloh Excelu a SOA v clusteru HPC Pack v Azure
Tento článek ukazuje, jak nasadit cluster Microsoft HPC Pack 2012 R2 na virtuálních počítačích Azure s použitím šablony Azure pro rychlý start, nebo volitelně nasazení skriptu Azure Powershellu. Cluster využívá určený ke spouštění úloh architektura orientovaná na služby (SOA) nebo Microsoft Excel pomocí sady HPC Pack Image virtuálního počítače Azure Marketplace. Můžete spustit HPC aplikace Excel a SOA služby z klientských počítačů v místním clusteru. Služby HPC pro Excel zahrnují snižování zátěže sešitu aplikace Excel a uživatelsky definovaných funkcí aplikace Excel nebo UDF.

> [!IMPORTANT] 
> Tento článek vychází z funkce, šablony a skripty pro prostředí HPC Pack 2012 R2. Tento scénář není aktuálně podporován v prostředí HPC Pack 2016.
>

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Následující diagram ukazuje na vysoké úrovni clusteru HPC Pack, že kterou vytvoříte.

![Cluster HPC s uzly, které běží úlohy aplikace Excel][scenario]

## <a name="prerequisites"></a>Požadavky
* **Klientský počítač** -potřebujete počítač klienta se systémem Windows k odesílání úloh Excelu a SOA ukázka do clusteru. Budete také potřebovat počítače Windows pro spuštění skriptu pro nasazení clusteru Azure PowerShell (Pokud zvolíte tuto metodu nasazení).
* **Předplatné Azure** – Pokud nemáte předplatné Azure, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) během několika minut.
* **Kvóta pro jádra** -může být potřeba zvýšit kvótu jader, zejména v případě, že nasadíte několik uzlů clusteru s vícejádrovými velikosti virtuálních počítačů. Pokud použijete šablony rychlý start Azure, každou oblast Azure je kvótu jader v Resource Manageru. V takovém případě bude potřeba zvýšit kvótu v určité oblasti. Zobrazit [limity předplatného Azure, kvóty a omezení](../../azure-subscription-service-limits.md). Na zvýšení této kvóty, [otevřete žádost o online zákaznickou podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) bez poplatků.
* **Licence Microsoft Office** – Pokud nasadíte výpočetních uzlů pomocí bitové kopie virtuálního počítače Marketplace HPC Pack 2012 R2 s aplikací Microsoft Excel, 30denní zkušební verze Microsoft Excelu Professional Plus 2013 je nainstalovaný. Po zkušebního období musíte zadat platnou licenci aplikace Microsoft Office aktivovat aplikaci Excel i nadále spouštět úlohy. Zobrazit [Excel aktivace](#excel-activation) dále v tomto článku. 

## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>Krok 1. Nastavení clusteru HPC Pack v Azure
Ukážeme dvě možnosti, jak nastavit cluster prostředí HPC Pack 2012 R2: první použití, šablony rychlý start Azure a webu Azure portal; a Zadruhé, pomocí skriptu nasazení prostředí Azure PowerShell.

### <a name="option-1-use-a-quickstart-template"></a>Možnost 1. Použít šablonu pro rychlý start
Pomocí šablony Azure pro rychlý start k rychlému nasazení clusteru HPC Pack na portálu Azure portal. Když otevřete šablonu na portálu, získáte jednoduché uživatelské rozhraní, kde zadejte nastavení pro váš cluster. Tady jsou kroky. 

> [!TIP]
> Pokud chcete, použijte [šabloně Azure Marketplace](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) vytvářející cluster podobné specificky pro úlohy aplikace Excel. Kroky se mírně liší od následující.
> 
> 

1. Přejděte [stránku šablony vytvořit Cluster prostředí HPC na Githubu](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Pokud chcete, projděte si informace o šabloně a zdrojový kód.
1. Klikněte na tlačítko **nasadit do Azure** pro spuštění nasazení pomocí šablony na webu Azure Portal.
   
   ![Nasazení šablony do Azure][github]
1. Na portálu použijte následující postup zadejte parametry pro šablonu clusteru prostředí HPC.
   
   a. Na **parametry** stránky zadejte nebo upravte hodnoty parametrů šablony. (Klikněte na ikonu vedle každého nastavení informace nápovědy.) Ukázkové hodnoty jsou uvedeny na následující obrazovce. Tento příklad vytvoří cluster s názvem *hpc01* v *hpc.local* domény skládající se z hlavního uzlu a 2 výpočetních uzlů. Výpočetní uzly se vytvoří z image virtuálního počítače HPC Pack, která obsahuje aplikaci Microsoft Excel.
   
   ![Zadání parametrů][parameters-new-portal]
   
   > [!NOTE]
   > Virtuální počítač je automaticky vytvořen z hlavního uzlu [nejnovější image z Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) HPC Pack 2012 R2 na Windows serveru 2012 R2. Na obrázku je aktuálně založená na HPC Pack 2012 R2 Update 3.
   > 
   > Výpočetní že uzel virtuální počítače vytvářejí z nejnovějšího image řady vybrané výpočetní uzel. Vyberte **ComputeNodeWithExcel** možnost pro nejnovější sady HPC Pack výpočetní uzel image, která zahrnuje zkušební verze Microsoft Excelu Professional Plus 2013. Chcete-li nasadit cluster obecných seminářů SOA nebo snižování zátěže systému souborů UDF Excelu, zvolte **ComputeNode** možnost (bez nainstalována aplikace Excel).
   > 
   > 
   
   b. Vyberte předplatné.
   
   c. Vytvořte skupinu prostředků clusteru, například *hpc01RG*.
   
   d. Vyberte umístění pro skupinu prostředků, jako je USA (střed).
   
   e. Na **právní podmínky** stránky, přečtěte si podmínky. Pokud souhlasíte, klikněte na tlačítko **nákupní**. Po dokončení nastavení hodnot šablony, klikněte **vytvořit**.
1. Až se nasazení dokončí (obvykle trvá přibližně 30 minut), export souboru certifikátu clusteru z hlavního uzlu clusteru. V pozdějším kroku importujte tento veřejný certifikát na klientském počítači se poskytovat ověřování na straně serveru pro bezpečnou vazbu protokolu HTTP.
   
   a. Na webu Azure Portal, přejděte na řídicí panel, vyberte hlavní uzel a klikněte na tlačítko **připojit** v horní části stránky a připojte se pomocí vzdálené plochy.
   
    <!-- ![Connect to the head node][connect] -->
   
   b. Použijte standardní postupy ve Správci certifikátů pro exportování certifikátu hlavní uzel (umístěný ve skupinovém rámečku Cert: \LocalMachine\My) bez soukromého klíče. V tomto příkladu exportovat *CN = hpc01.eastus.cloudapp.azure.com*.
   
   ![Export certifikátu][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>Možnost 2. Použít skript nasazení IaaS sady HPC Pack
Skriptem nasazení IaaS sady HPC Pack obsahuje další univerzální způsob, jak nasadit cluster prostředí HPC Pack. Vytvoří cluster v modelu nasazení classic, že šablona používá model nasazení Azure Resource Manageru. Skript je také kompatibilní s předplatným služby Azure Global nebo Azure China.

**Další požadavky**

* **Prostředí Azure PowerShell** - [nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview) (verze 0.8.10 nebo novější) na klientském počítači.
* **Skriptem nasazení IaaS sady HPC Pack** – stažení a rozbalení nejnovější verzi skriptu z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Zkontrolujte verzi skriptu spuštěním `New-HPCIaaSCluster.ps1 –Version`. Tento článek je založen na verzi 4.5.0 nebo později tohoto skriptu.

**Vytvoření konfiguračního souboru**

 Skriptem nasazení IaaS sady HPC Pack konfigurační soubor XML používá jako vstup, který popisuje infrastrukturu clusteru prostředí HPC. Pokud chcete nasadit cluster skládající se z hlavního uzlu a 18 výpočetních uzlů vytvořené z image výpočetního uzlu, který zahrnuje aplikace Microsoft Excel, nahraďte hodnoty pro vaše prostředí do následující vzorový konfigurační soubor. Další informace o konfiguračním souboru, naleznete v souboru Manual.rtf ve složce script a [vytvoření clusteru HPC se skriptem nasazení IaaS sady HPC Pack](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
    <PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**Poznámky o konfigurační soubor**

* **VMName** hlavního uzlu **musí** být stejné jako **ServiceName**, nebo spuštění úlohy architektury SOA se nepovede.
* Ujistěte se, že zadáte **EnableWebPortal** tak, aby certifikát hlavního uzlu je generována a exportovat.
* Soubor Určuje skript prostředí PowerShell po konfiguraci PostConfig.ps1, na kterém běží hlavního uzlu. Následující ukázkový skript nastaví připojovací řetězec služby Azure storage, odebere roli výpočetní uzel z hlavního uzlu a přináší všechny uzly online, když jsou nasazená. 

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**Spusťte skript**

1. Otevřete konzolu prostředí PowerShell v klientském počítači jako správce.
1. Změňte adresář na složku skriptu (E:\IaaSClusterScript v tomto příkladu).
   
   ```
   cd E:\IaaSClusterScript
   ```
1. K nasazení clusteru HPC Pack, spusťte následující příkaz. Tento příklad předpokládá, že konfigurační soubor umístěný v E:\HPCDemoConfig.xml.
   
   ```
   .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
   ```

Spuštění skriptu nasazení sady HPC Pack nechystáte nějakou dobu. Jednou z věcí, které skript je exportovat a stáhněte si certifikát clusteru a uložit ve složce Dokumenty aktuální uživatel v klientském počítači. Tento skript generuje zpráva podobná následující. V tomto kroku importujete certifikát v úložišti příslušný certifikát.    

    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>Krok 2. Snižte zátěž Excelové sešity a spuštění UDF z lokálního klienta
### <a name="excel-activation"></a>Aktivace aplikace Excel
Pokud používáte image virtuálního počítače ComputeNodeWithExcel pro produkční úlohy, musíte poskytnout platný klíč licence Microsoft Office aktivovat aplikace Excel na výpočetních uzlech. V opačném případě po 30 dnech vyprší platnost zkušební verze aplikace Excel a systémem Excelové sešity se nezdaří s COMException (0x800AC472). 

Obnovení aplikace Excel můžete aktivačního období o dalších 30 dní doba hodnocení: Přihlaste se k hlavnímu uzlu a clusrun `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` na všechny aplikace Excel výpočetních uzlů pomocí Správce clusteru HPC. Obnovení aktivačního období maximálně dvakrát. Poté je nutné zadat platný klíč licence Office.

Office Professional Plus 2013 v imagi virtuálního počítače nainstalované je multilicenční edici s obecné svazku licenční klíč (kód GVLK). Můžete si ji můžou aktivovat prostřednictvím služby správy klíčů (KMS) nebo Aktivace multilicence (AD BA) nebo klíče MAK (Multiple Activation). 

    * Použití služby správy KLÍČŮ/AD-BA, použít existující server služby správy KLÍČŮ nebo nastavit nové předplatné s použitím Microsoft Office 2013 svazku licenční balíček. (Pokud chcete, nastavení serveru na hlavní uzel.) Potom aktivujte klíč hostitele služby správy KLÍČŮ přes Internet nebo telefon. Potom clusrun `ospp.vbs` v aplikaci Excel na serveru služby správy KLÍČŮ a portu a aktivovat Office na všech výpočetních uzlech. 

    * Použít klíč k vícenásobné aktivaci, první clusrun `ospp.vbs` k zadání klíče a poté aktivovat všechny aplikace Excel výpočetních uzlů přes Internet nebo telefon. 

> [!NOTE]
> Maloobchodní kódy product key pro Office Professional Plus 2013 nelze použít u této image virtuálního počítače. Pokud máte platný klíčů a instalačními médii pro edice Office nebo aplikace Excel než tato edice Office Professional Plus 2013 svazku, můžete je použít místo. Nejdřív odinstalujte tuto edici svazku a instalaci edice, kterou máte. Přeinstalovaného výpočetním uzlu Excel se dají zachytit jako vlastní image virtuálního počítače pro použití v nasazení ve velkém měřítku.
> 
> 

### <a name="offload-excel-workbooks"></a>Přesměrování zpracování Excelových sešitů
Použijte následující postup snižování zátěže sešitu aplikace Excel, tak, aby běžel v clusteru HPC Pack v Azure. Chcete-li to provést, musí mít aplikace Excel 2010 nebo 2013 již nainstalována na klientském počítači.

1. Použijte jednu z možností v kroku 1 a nasaďte cluster prostředí HPC Pack pomocí aplikace Excel výpočetní uzel image. Získáte clusteru soubor certifikátu (.cer) a clusteru uživatelské jméno a heslo.
1. Na klientském počítači importujte certifikát clusteru v rámci Cert: \CurrentUser\Root.
1. Ujistěte se, že je nainstalována aplikace Excel. Vytvořte soubor s příponou Excel.exe.config s použitím následujícího obsahu ve stejné složce jako Excel.exe v klientském počítači. Tento krok zajistí, že – v prostředí HPC Pack 2012 R2 Excel COM úspěšně načte.
   
    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
1. Nastavení klienta a odesílat úlohy do clusteru HPC Pack. Jednou z možností je stažení kompletní [HPC Pack 2012 R2 Update 3 instalační](https://www.microsoft.com/download/details.aspx?id=49922) a instalace sady HPC Pack klienta. Můžete také stáhnout a nainstalovat [HPC Pack 2012 R2 Update 3 klientské nástroje](https://www.microsoft.com/download/details.aspx?id=49923) a odpovídající Visual C++ 2010 redistributable pro počítače ([x64](https://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555) ).
1. V tomto příkladu používáme ukázkového sešitu aplikace Excel s názvem ConvertiblePricing_Complete.xlsb. Můžete ji stáhnout [tady](https://www.microsoft.com/en-us/download/details.aspx?id=2939).
1. Pracovní složky, jako je například D:\Excel\Run zkopírujte Excelový sešit.
1. Otevřete Excelový sešit. Na **vývoj** pásu karet, klikněte na tlačítko **doplňky modelu COM** a potvrďte, že doplněk HPC Pack Excel COM byla úspěšně zavedena.
   
   ![Aplikace Excel add-in pro prostředí HPC Pack][addin]
1. Upravte změnou řádcích s komentářem, jak je znázorněno v následujícím skriptu – makro VBA HPCControlMacros v aplikaci Excel. Nahraďte příslušnými hodnotami pro vaše prostředí.
   
   ![Makro v Excelu pro HPC Pack][macro]
   
   ```
   'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
   Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"
   
   'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
   Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"
   
   'HPCExcelClient.Initialize ActiveWorkbook
   HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles
   
   'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
   HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"
   
   'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
   HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
   ```
1. Zkopírujte Excelový sešit do adresáře například D:\Excel\Upload nahrávání. Tento adresář je zadán v konstantě HPC_DependsFiles v makru VBA.
1. Ke spuštění v sešitu v clusteru v Azure, klikněte na tlačítko **clusteru** tlačítko na listu.

### <a name="run-excel-udfs"></a>Spuštění UDF Excelu
Ke spuštění souborů UDF Excelu, postupujte podle předchozích kroků 1 – 3 nastavení klientského počítače. U souborů UDF Excelu nemusíte mít aplikace Excel nainstalována na výpočetních uzlech. Ano při vytváření clusteru výpočetních uzlů, můžete se rozhodnout normální výpočetní uzel image místo image výpočetního uzlu pomocí aplikace Excel.

> [!NOTE]
> Existuje omezení 34 znaků v aplikaci Excel 2010 a 2013 dialogové okno konektoru clusteru. Toto dialogové okno vám zadejte požadovaný cluster, na kterém běží UDF. Pokud je název clusteru úplný delší (například hpcexcelhn01.southeastasia.cloudapp.azure.com), v dialogovém okně nevejde. Alternativním řešením je nastavit proměnnou celého systému, jako *CCP_IAASHN* s použitím hodnoty z clusteru dlouhý název. Potom zadejte *CCP_IAASHN %* v dialogovém okně jako název hlavního uzlu clusteru. 
> 
> 

Po úspěšném nasazení clusteru pokračovat tyto kroky a spusťte ukázku integrovaných UDF Excelu. Pro vlastní souborů UDF Excelu, informace najdete v těchto [prostředky](https://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) sestavení XLL a jejich nasazení v clusteru IaaS.

1. Otevřete nový Excelový sešit. Na **vývoj** pásu karet, klikněte na tlačítko **Add-Ins**. Klikněte v dialogovém okně **Procházet**, přejděte do složky %CCP_HOME%Bin\XLL32 a vybrat ukázku ClusterUDF32.xll. Pokud ClusterUDF32 neexistuje v klientském počítači, zkopírujte ho ve složce %CCP_HOME%Bin\XLL32 hlavního uzlu.
   
   ![Vyberte UDF][udf]
1. Klikněte na tlačítko **souboru** > **možnosti** > **Advanced**. V části **vzorce**, zkontrolujte **povolit uživatelem definované funkce XLL ke spuštění výpočetního clusteru**. Pak klikněte na tlačítko **možnosti** a zadejte název clusteru úplný **název hlavního uzlu clusteru**. (Jak je uvedeno dříve toto vstupní pole je omezená na 34 znaků, takže nemusí podle názvu dlouhá Clusterové. Můžete použít proměnnou celého názvu dlouhá Clusterové.)
   
   ![Konfigurace UDF][options]
1. Pro spuštění výpočtu systému souborů UDF v clusteru, klikněte na buňku s hodnotou =XllGetComputerNameC() a stiskněte klávesu Enter. Funkce jednoduše načte název výpočetním uzlu, na kterém běží UDF. Pro první spuštění dialogové okno pověření vyzve k zadání uživatelského jména a hesla pro připojení ke clusteru IaaS.
   
   ![Spuštění UDF][run]
   
   Pokud je počet buněk k výpočtu, stiskněte Alt-Shift-Ctrl + F9 pro spuštění výpočtu na všechny buňky.

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>Krok 3. Spuštění úlohy SOA z lokálního klienta
Pokud chcete spustit obecné aplikace SOA v clusteru IaaS sady HPC Pack, nejprve pomocí jedné z metod v kroku 1 k nasazení clusteru. Zadejte obecný výpočetní uzel bitové kopie v tomto případě, protože není nutné aplikaci Excel na výpočetních uzlech. Pak postupujte podle těchto kroků.

1. Po načtení certifikátu clusteru, importujte ho na klientském počítači v části Cert: \CurrentUser\Root.
1. Nainstalujte [HPC Pack 2012 R2 Update 3 SDK](https://www.microsoft.com/download/details.aspx?id=49921) a [HPC Pack 2012 R2 Update 3 klientské nástroje](https://www.microsoft.com/download/details.aspx?id=49923). Tyto nástroje umožňují vyvíjet a spouštět SOA klientské aplikace.
1. Stáhněte si HelloWorldR2 [ukázkový kód](https://www.microsoft.com/download/details.aspx?id=41633). Otevřete HelloWorldR2.sln v sadě Visual Studio 2010 nebo 2012. (Tento příklad není momentálně kompatibilní s novější verzí sady Visual Studio.)
1. Nejdřív sestavte projekt EchoService. Potom nasaďte služby do clusteru IaaS stejným způsobem, který nasazujete na místní cluster. Podrobné pokyny najdete v článku Readme.doc v HelloWordR2. Upravit a vytvořit HellWorldR2 a jiné projekty, jak je popsáno v následující části generovat klientské aplikace SOA, které běží v clusteru služby Azure IaaS.

### <a name="use-http-binding-with-azure-storage-queue"></a>Použití vazby protokolu Http s fronty Azure storage
Pro použití vazby protokolu Http s fronty služby Azure storage, proveďte několik změn do vzorového kódu.

* Aktualizujte název clusteru.
  
    ```
  // Before
  const string headnode = "[headnode]";
  // After e.g.
  const string headnode = "hpc01.eastus.cloudapp.azure.com";
  or
  const string headnode = "hpc01.cloudapp.net";
  ```
* Volitelně můžete použít výchozí TransportScheme v SessionStartInfo nebo explicitně nastavit na Http.

```
    info.TransportScheme = TransportScheme.Http;
```

* Použijte výchozí vazby BrokerClient.
  
    ```
  // Before
  using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
  // After
  using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
  ```
  
    Nebo nastavit explicitně pomocí basicHttpBinding.
  
    ```
  BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
  binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
  ```
* Volitelně můžete nastavte příznak UseAzureQueue na hodnotu true v SessionStartInfo. Pokud není nastavená, bude nastavena hodnotu true ve výchozím nastavení, pokud je název clusteru má přípon domén Azure a TransportScheme je Http.
  
    ```
    info.UseAzureQueue = true;
  ```

### <a name="use-http-binding-without-azure-storage-queue"></a>Použití vazby protokolu Http bez fronty Azure storage
Použití vazby protokolu Http bez fronty služby Azure storage explicitně nastavena na hodnotu false v SessionStartInfo UseAzureQueue příznak.

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>Použití NetTcp vazby
Konfigurace použití NetTcp vazby, je podobné propojení místního clusteru. Budete muset otevřít několik koncových bodů hlavního uzlu virtuálního počítače. Pokud jste použili k vytvoření clusteru skriptem nasazení IaaS sady HPC Pack, například nastavení koncových bodů na webu Azure Portal následujícím způsobem.

1. Zastavte virtuální počítač.
1. Porty TCP 9090, přidat 9087, 9091, 9094 pro relaci, zprostředkovatel, zprostředkovatel pracovní procesy a datové služby v uvedeném pořadí
   
    ![Konfigurace koncových bodů][endpoint-new-portal]
1. Spusťte virtuální počítač.

Klientská aplikace SOA nevyžaduje žádné změny, s výjimkou změnit hlavní název pro IaaS úplný název clusteru.

## <a name="next-steps"></a>Další postup
* Zobrazit [tyto prostředky](https://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) Další informace o spouštění úloh aplikace Excel pomocí sady HPC Pack.
* Zobrazit [Správa služby SOA v Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) Další informace o nasazení a správu služeb SOA se sadou HPC Pack.

<!--Image references-->
[scenario]: ./media/excel-cluster-hpcpack/scenario.png
[github]: ./media/excel-cluster-hpcpack/github.png
[template]: ./media/excel-cluster-hpcpack/template.png
[parameters]: ./media/excel-cluster-hpcpack/parameters.png
[parameters-new-portal]: ./media/excel-cluster-hpcpack/parameters-new-portal.png
[create]: ./media/excel-cluster-hpcpack/create.png
[connect]: ./media/excel-cluster-hpcpack/connect.png
[cert]: ./media/excel-cluster-hpcpack/cert.png
[addin]: ./media/excel-cluster-hpcpack/addin.png
[macro]: ./media/excel-cluster-hpcpack/macro.png
[options]: ./media/excel-cluster-hpcpack/options.png
[run]: ./media/excel-cluster-hpcpack/run.png
[endpoint]: ./media/excel-cluster-hpcpack/endpoint.png
[endpoint-new-portal]: ./media/excel-cluster-hpcpack/endpoint-new-portal.png
[udf]: ./media/excel-cluster-hpcpack/udf.png
