---
title: Cluster HPC Pack pro aplikaci Excel a SOA | Microsoft Docs
description: Začínáme s rozsáhlé úlohy aplikace Excel a SOA v clusteru HPC Pack v Azure
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
ms.openlocfilehash: aaf26e04fdb38fd76f4ab8211f9fdda8ebafd668
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30917400"
---
# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>Začít a spustit úlohy aplikace Excel a SOA v clusteru HPC Pack v Azure
Tento článek ukazuje, jak nasadit cluster Microsoft HPC Pack 2012 R2 na virtuálních počítačích Azure pomocí šablony Azure rychlý start, nebo volitelně skript nasazení Azure PowerShell. Cluster využívá Image virtuálního počítače Azure Marketplace, které jsou navrženy pro spouštění úloh orientované na služby architektura (SOA) nebo Microsoft Excel pomocí sady HPC Pack. Clusteru můžete použít ke spuštění aplikace Excel HPC a SOA služby z klientského počítače k místní. Služby HPC pro Excel obsahovat snižování zátěže sešitu aplikace Excel a uživatelem definované funkce aplikace Excel nebo UDF.

> [!IMPORTANT] 
> Tento článek vychází z funkce, šablony a skripty pro HPC Pack 2012 R2. Tento scénář není aktuálně podporován v prostředí HPC Pack 2016.
>

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Následující diagram znázorňuje na vysoké úrovni clusteru HPC Pack, že který vytvoříte.

![Cluster HPC s uzly, které jsou spuštěné úlohy aplikace Excel][scenario]

## <a name="prerequisites"></a>Požadavky
* **Klientský počítač** -potřebujete počítač klienta se systémem Windows k odesílání ukázkové aplikace Excel a SOA úloh do clusteru. Musíte taky počítač se systémem Windows pro spuštění skriptu nasazení clusteru Azure PowerShell (Pokud zvolíte tuto metodu nasazení).
* **Předplatné Azure** – Pokud nemáte předplatné Azure, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) si během několika minut.
* **Kvóta jader** -možná budete muset zvýšit kvótu jádra, zvlášť pokud nasadíte několik uzlů clusteru s vícejádrovými velikosti virtuálních počítačů. Pokud používáte šablonu Azure rychlý start, kvóta jádra ve službě Správce prostředků je za oblast Azure. V takovém případě je potřeba zvýšit kvótu v určité oblasti. V tématu [limity předplatného Azure, kvóty a omezení](../../azure-subscription-service-limits.md). Pokud chcete zvýšit kvótu, [otevřete žádosti o podporu online zákazníka](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) zdarma.
* **Licenci aplikace Microsoft Office** – Pokud nasadíte výpočetní uzly bitovou kopii virtuálního počítače Marketplace HPC Pack 2012 R2 pomocí aplikace Microsoft Excel, 30denní zkušební verzi Microsoft Excelu Professional Plus 2013 je nainstalována. Po uplynutí zkušebního období musíte zadat platnou licenci aplikace Microsoft Office Excel nadále spouštět úlohy aktivovat. V tématu [aktivace v aplikaci Excel](#excel-activation) dále v tomto článku. 

## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>Krok 1. Nastavení clusteru služby HPC Pack v Azure
Ukážeme dvě možnosti pro nastavení clusteru HPC Pack 2012 R2: první, pomocí šablonu Azure rychlý start a webu Azure portal; a Zadruhé, pomocí skript nasazení Azure PowerShell.

### <a name="option-1-use-a-quickstart-template"></a>Možnost 1. Použít šabloně pro rychlý start
Rychlé nasazení clusteru HPC Pack na portálu Azure pomocí šablony Azure rychlý start. Když otevřete šablonu na portálu, získáte jednoduchého uživatelského rozhraní, kde můžete zadat nastavení pro váš cluster. Tady jsou kroky. 

> [!TIP]
> Pokud chcete, použijte [šablony Azure Marketplace](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) vytvářející cluster podobné speciálně pro zatížení aplikace Excel. Kroky mírně lišit od následující.
> 
> 

1. Přejděte [stránku šablony vytvořit Cluster prostředí HPC na Githubu](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Pokud chcete, přečtěte si informace o šabloně a zdrojový kód.
2. Klikněte na tlačítko **nasadit do Azure** zahájíte nasazení pomocí šablony na portálu Azure.
   
   ![Nasazení šablony Azure][github]
3. Na portálu použijte následující postup zadat parametry šablony clusteru prostředí HPC.
   
   a. Na **parametry** stránky zadejte nebo upravte hodnoty pro parametry šablony. (Klikněte na ikonu u kteréhokoli nastavení pro informace nápovědy.) Ukázkové hodnoty jsou uvedené v následující obrazovku. Tento příklad vytvoří cluster s názvem *hpc01* v *hpc.local* domény, který se skládá z hlavního uzlu a 2 výpočetních uzlů. Výpočetní uzly jsou vytvořené z virtuálních počítačů HPC Pack obrázek, který obsahuje aplikace Microsoft Excel.
   
   ![Zadání parametrů][parameters-new-portal]
   
   > [!NOTE]
   > Virtuální počítač je automaticky vytvořen z hlavního uzlu [nejnovější image Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) HPC Pack 2012 R2 na Windows Server 2012 R2. Aktuálně bitovou kopii je založena na HPC Pack 2012 R2 Update 3.
   > 
   > Výpočetní uzel virtuální počítače jsou vytvořeny z nejnovější bitové kopie rodiny vybrané výpočetního uzlu. Vyberte **ComputeNodeWithExcel** možnost pro nejnovější aktualizaci HPC Pack výpočetní uzel image, která obsahuje zkušební verzi Microsoft Excelu Professional Plus 2013. Chcete-li nasadit cluster s podporou pro obecné SOA relací nebo snižování zátěže systému souborů UDF Excelu, zvolte **ComputeNode** možnost (bez nainstalované aplikace Excel).
   > 
   > 
   
   b. Zvolte předplatné.
   
   c. Vytvořte skupinu prostředků clusteru, například *hpc01RG*.
   
   d. Vyberte umístění pro skupinu prostředků, jako je například střed USA.
   
   e. Na **právní podmínky** stránky, přečtěte si podmínky. Pokud souhlasíte, klikněte na tlačítko **nákupu**. Po dokončení nastavení hodnoty pro šablonu, klepněte na **vytvořit**.
4. Po dokončení nasazení (obvykle trvá přibližně 30 minut), exportovat soubor certifikátu clusteru z hlavního uzlu clusteru. V pozdější fázi importujete tento veřejný certifikát na klientském počítači, aby poskytovala ověřování na straně serveru pro zabezpečené vazby HTTP.
   
   a. V portálu Azure, přejděte na řídicí panel, vyberte z hlavního uzlu a klikněte na **Connect** v horní části stránky a připojte se pomocí vzdálené plochy.
   
    <!-- ![Connect to the head node][connect] -->
   
   b. Použijte standardní postupy ve Správci certifikátů a vyexportovat si certifikát hlavního uzlu (nachází se v části Cert: \LocalMachine\My) bez soukromého klíče. V tomto příkladu exportovat *CN = hpc01.eastus.cloudapp.azure.com*.
   
   ![Export certifikátu][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>Možnost 2. Pomocí tohoto skriptu nasazení IaaS HPC Pack
Skript nasazení HPC Pack IaaS poskytuje další univerzální způsob nasazení clusteru HPC Pack. Vytvoří cluster v modelu nasazení classic, zatímco šablona používá model nasazení Azure Resource Manager. Skript je také kompatibilní s předplatným ve službě Azure globální nebo Azure China.

**Další požadavky**

* **Prostředí Azure PowerShell** - [nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview) (verze 0.8.10 nebo novější) na klientském počítači.
* **Skript nasazení HPC Pack IaaS** – stáhněte a rozbalte nejnovější verzi skript z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Zkontrolujte verzi skript spuštěním `New-HPCIaaSCluster.ps1 –Version`. Tento článek je založen na verzi 4.5.0 nebo později skript.

**Vytvoření konfiguračního souboru**

 Skript nasazení HPC Pack IaaS používá jako vstup, který popisuje infrastruktura clusteru HPC konfigurační soubor XML. Chcete-li nasadit cluster, který se skládá z hlavního uzlu a 18 výpočetních uzlů vytvořené z image výpočetního uzlu, která obsahuje aplikaci Microsoft Excel, nahraďte hodnoty pro vaše prostředí do následující vzorový konfigurační soubor. Další informace o konfiguračním souboru, najdete v souboru Manual.rtf ve složce skriptu a [vytvoření clusteru prostředí HPC pomocí skriptu pro nasazení HPC Pack IaaS](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

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

**Poznámky k konfiguračního souboru**

* **VMName** hlavního uzlu **musí** být stejný jako **ServiceName**, nebo úlohy architektury SOA se nepodaří spustit.
* Je nutné zadat **EnableWebPortal** tak, aby je generována a exportovat certifikát hlavního uzlu.
* Soubor Určuje skript prostředí PowerShell po konfiguraci PostConfig.ps1, která běží na hlavního uzlu. Následující ukázkový skript nakonfiguruje připojovací řetězec úložiště Azure, odebere roli výpočetní uzel z hlavního uzlu a při jejich nasazení přináší všechny uzly online. 

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
2. Změňte adresář na složku skriptu (E:\IaaSClusterScript v tomto příkladu).
   
   ```
   cd E:\IaaSClusterScript
   ```
3. Nasazení clusteru HPC Pack, spusťte následující příkaz. Tento příklad předpokládá, že konfigurační soubor nachází v E:\HPCDemoConfig.xml.
   
   ```
   .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
   ```

Skript nasazení HPC Pack spustí nějakou dobu. Jednou z věcí, které nemá skript je exportovat a stáhněte certifikát clusteru a uložit do složky Dokumenty aktuální uživatel v klientském počítači. Tento skript generuje zprávu podobný následujícímu. V tomto kroku importujete certifikát v úložišti příslušný certifikát.    

    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>Krok 2. Snižování zátěže sešitů aplikace Excel a spusťte UDF z místního klienta
### <a name="excel-activation"></a>Aktivace aplikace Excel
Pokud používáte image virtuálního počítače ComputeNodeWithExcel pro úlohy v produkčním prostředí, je třeba zadat platný klíč licence Microsoft Office k aktivaci aplikace Excel na výpočetních uzlech. Jinak po 30 dnech vyprší platnost zkušební verzi aplikace Excel a systémem sešitů aplikace Excel se nezdaří s COMException (0x800AC472). 

Obnovení aplikace Excel můžete aktivačního období pro jiné 30 dnů od doby vyhodnocení: Přihlaste se k hlavnímu uzlu a clusrun `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` na všechny aplikace Excel výpočetní uzly prostřednictvím Správce clusteru HPC. Po obnovení aktivačního období maximálně dvakrát. Potom je nutné zadat platný klíč licence Office.

Office Professional Plus 2013 nainstalovaný na bitovou kopii virtuálního počítače je multilicenční edici s obecné svazku licenční klíč (kód GVLK). Aktivujte ji prostřednictvím služby správy klíčů (KMS) nebo aktivaci prostřednictvím služby (AD-BA) nebo klíč k vícenásobné aktivaci (MAK). 

    * Pomocí služby správy KLÍČŮ/AD-BA, použít existující server služby správy KLÍČŮ nebo nastavit novou pomocí sady Microsoft Office 2013 svazku licence. (Pokud chcete, nastavení serveru z hlavního uzlu.) Potom aktivujte klíč hostitele služby správy KLÍČŮ přes Internet nebo telefon. Potom clusrun `ospp.vbs` nastavit server služby správy KLÍČŮ a port a aktivovat Office na všechny aplikace Excel výpočetních uzlů. 

    * Použít klíč k vícenásobné aktivaci, první clusrun `ospp.vbs` k zadejte klíč a poté znovu aktivovat všechny aplikace Excel výpočetní uzly přes Internet nebo telefon. 

> [!NOTE]
> Prodejní kódy product key pro Office Professional Plus 2013 nelze použít s touto bitovou kopií virtuálního počítače. Pokud máte platné klíče a instalační médium pro edice Office nebo aplikace Excel než tato edice Office Professional Plus 2013 svazku, můžete je používat místo. Nejprve odinstalujte tento multilicenční edici a nainstalujte na edici, ke které máte. Jako vlastní image virtuálního počítače pro nasazení ve velkém měřítku, se dají zachytit přeinstalovaného výpočetním uzlu aplikace Excel.
> 
> 

### <a name="offload-excel-workbooks"></a>Snižování zátěže sešitů aplikace Excel
Postupujte podle těchto kroků snižování zátěže sešitu aplikace Excel, tak, aby běžel v clusteru HPC Pack v Azure. Chcete-li to provést, musí mít aplikaci Excel 2010 nebo 2013 v klientském počítači již nainstalována.

1. Použijte jednu z možností v kroku 1 k nasazení clusteru HPC Pack pomocí aplikace Excel výpočetní uzel image. Získejte clusteru soubor certifikátu (.cer) a cluster uživatelské jméno a heslo.
2. Na klientském počítači importujte certifikát clusteru pod Cert: \CurrentUser\Root.
3. Zkontrolujte, zda že je nainstalována aplikace Excel. Vytvořte soubor Excel.exe.config s následující obsah ve stejné složce jako Excel.exe na klientském počítači. Tento krok zajistí, že-in prostředí HPC Pack 2012 R2 Excel COM úspěšně načten.
   
    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
4. Nastavení klienta k odesílání úloh do clusteru HPC Pack. Jednou z možností je stáhnout kompletní [HPC Pack 2012 R2 Update 3 instalace](http://www.microsoft.com/download/details.aspx?id=49922) a instalace sady HPC Pack klienta. Můžete taky stáhnout a nainstalovat [HPC Pack 2012 R2 Update 3 klienta nástroje](https://www.microsoft.com/download/details.aspx?id=49923) a příslušné Visual C++ 2010 redistributable pro tento počítač ([x64](http://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555) ).
5. V tomto příkladu používáme Ukázka sešitu aplikace Excel s názvem ConvertiblePricing_Complete.xlsb. Můžete ho stáhnout [zde](https://www.microsoft.com/en-us/download/details.aspx?id=2939).
6. Pracovní složky, například D:\Excel\Run zkopírujte sešitu aplikace Excel.
7. Otevřete sešit aplikace Excel. Na **vývoj** pásu karet, klikněte na tlačítko **doplňky modelu COM** a potvrďte, že-in prostředí HPC Pack Excel COM byla úspěšně zavedena.
   
   ![Add-in pro prostředí HPC Pack v aplikaci Excel][addin]
8. Změna řádky komentářů, jak je znázorněno v následující skript upravte makro VBA HPCControlMacros v aplikaci Excel. Nahraďte příslušnými hodnotami pro vaše prostředí.
   
   ![Makro aplikace Excel pro HPC Pack][macro]
   
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
9. Zkopírujte adresář nahrávání například D:\Excel\Upload sešitu aplikace Excel. Tento adresář je určen v konstanta HPC_DependsFiles v makro VBA pro vytváření.
10. Pokud chcete spustit sešit v clusteru v Azure, klikněte na tlačítko **clusteru** tlačítko na listu.

### <a name="run-excel-udfs"></a>Spusťte systém souborů UDF Excelu
Pokud chcete spustit systém souborů UDF Excelu, postupujte podle předchozích kroků 1 – 3 můžete nastavit v klientském počítači. Pro systém souborů UDF Excelu nemusíte mít nainstalovanou na výpočetních uzlech aplikaci Excel. Ano při vytváření clusteru výpočetních uzlů, mohli byste normální výpočetní uzel image místo image výpočetního uzlu pomocí aplikace Excel.

> [!NOTE]
> Existuje limit 34 znak v aplikaci Excel 2010 a 2013 dialogové okno konektor clusteru. Toto dialogové okno pomůže zadejte požadovaný cluster, který běží UDF. Pokud je název clusteru úplné delší (například hpcexcelhn01.southeastasia.cloudapp.azure.com), v dialogovém okně nevejde. Řešením je třeba nastavit proměnnou celého systému *CCP_IAASHN* s hodnotou název dlouho clusteru. Potom zadejte *CCP_IAASHN %* v dialogovém okně jako název hlavního uzlu clusteru. 
> 
> 

Po úspěšném nasazení clusteru, pokračujte tyto kroky a spusťte ukázku předdefinované UDF Excelu. Vlastní systém souborů UDF Excelu, najdete v těchto [prostředky](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) sestavení XLL a nasadit je na IaaS clusteru.

1. Otevřete nový sešit aplikace Excel. Na **vývoj** pásu karet, klikněte na tlačítko **doplňky**. Klikněte v dialogovém okně **Procházet**, přejděte do složky %CCP_HOME%Bin\XLL32 a vyberte vzorek ClusterUDF32.xll. Pokud ClusterUDF32 neexistuje na klientský počítač, zkopírujte jej ze složky %CCP_HOME%Bin\XLL32 z hlavního uzlu.
   
   ![Vyberte UDF][udf]
2. Klikněte na tlačítko **soubor** > **možnosti** > **rozšířené**. V části **vzorce**, zkontrolujte **povolit uživatelsky definované funkce XLL ke spuštění výpočetního clusteru**. Pak klikněte na tlačítko **možnosti** a zadejte název clusteru úplné v **název hlavního uzlu clusteru**. (Jak je uvedeno dříve toto vstupní pole je omezený na 34 znaků, proto nemusí podle názvu dlouho clusteru. Proměnnou celého systému může použít pro název dlouho clusteru.)
   
   ![Konfigurace UDF][options]
3. Chcete-li spustit výpočet UDF v clusteru, klikněte na buňky s hodnotou =XllGetComputerNameC() a stiskněte Enter. Funkce jednoduše načte název výpočetním uzlu, na kterém běží UDF. Pro první spustit dialogové okno přihlašovací údaje vyzve k zadání uživatelského jména a hesla se připojit ke clusteru IaaS.
   
   ![Spustit UDF][run]
   
   Když dojde k výpočtu velké množství buněk, stiskněte klávesu Alt-Shift-Ctrl + F9 ke spuštění na všechny buňky výpočtu.

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>Krok 3. Spuštění úlohy architektury SOA z místního klienta
Pokud chcete spustit obecné aplikace SOA v clusteru HPC Pack IaaS, nejprve pomocí jedné z metod v kroku 1 nasazení clusteru. Zadejte obecný výpočetní uzel bitové kopie v tomto případě, protože není nutné aplikaci Excel na výpočetních uzlech. Potom postupujte podle těchto kroků.

1. Po načtení certifikátu clusteru, importujte ho na klientském počítači v rámci Cert: \CurrentUser\Root.
2. Nainstalujte [HPC Pack 2012 R2 Update 3 SDK](http://www.microsoft.com/download/details.aspx?id=49921) a [HPC Pack 2012 R2 Update 3 klienta nástroje](https://www.microsoft.com/download/details.aspx?id=49923). Tyto nástroje vám umožňují vývoj a spusťte SOA klientské aplikace.
3. Stažení HelloWorldR2 [ukázkový kód](https://www.microsoft.com/download/details.aspx?id=41633). Otevřete HelloWorldR2.sln v sadě Visual Studio 2010 nebo 2012. (Tato ukázka není momentálně kompatibilní s novější verzí sady Visual Studio.)
4. Sestavení projektu EchoService nejdřív. Potom nasaďte službu do clusteru IaaS stejným způsobem, který můžete nasadit na místní cluster. Podrobné pokyny najdete v části Readme.doc v HelloWordR2. Upravit a vytvořit HellWorldR2 a další projekty, jak je popsáno v následující části ke generování SOA klientské aplikace, které běží na clusteru služby Azure IaaS.

### <a name="use-http-binding-with-azure-storage-queue"></a>Vazba Http pomocí fronty Azure storage
Pro použití vazby Http s frontu úložiště Azure, proveďte několik změny ukázkový kód.

* Aktualizujte název clusteru.
  
    ```
  // Before
  const string headnode = "[headnode]";
  // After e.g.
  const string headnode = "hpc01.eastus.cloudapp.azure.com";
  or
  const string headnode = "hpc01.cloudapp.net";
  ```
* V případě potřeby použijte výchozí TransportScheme v SessionStartInfo nebo explicitně nastavit na Http.

```
    info.TransportScheme = TransportScheme.Http;
```

* Používejte výchozí vazbu pro BrokerClient.
  
    ```
  // Before
  using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
  // After
  using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
  ```
  
    Nebo nastavte explicitně pomocí basicHttpBinding.
  
    ```
  BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
  binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
  ```
* Volitelně můžete nastavte na hodnotu true v SessionStartInfo příznak UseAzureQueue. Pokud není nastavená, nastaví se hodnotu true ve výchozím nastavení, když se název clusteru má přípony domény Azure a TransportScheme, je Http.
  
    ```
    info.UseAzureQueue = true;
  ```

### <a name="use-http-binding-without-azure-storage-queue"></a>Použít vazbu Http bez fronty Azure storage
Pokud chcete používat vazby Http bez frontu úložiště Azure, explicitně nastavte příznak UseAzureQueue na hodnotu false v SessionStartInfo.

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>NetTcp vazbu používají
Pokud chcete použít NetTcp vazby, konfigurace je podobný připojení k místní cluster. Budete muset otevřít několik koncových bodů z hlavního uzlu virtuálního počítače. Pokud skript nasazení HPC Pack IaaS jste použili k vytvoření clusteru, například nastavte koncové body na portálu Azure následujícím způsobem.

1. Zastavte virtuální počítač.
2. Přidat porty TCP 9090, 9087, 9091, 9094 pro relaci, zprostředkovatel, respektive zprostředkovatel worker a datové služby
   
    ![Konfigurace koncových bodů][endpoint-new-portal]
3. Spusťte virtuální počítač.

Klientská aplikace SOA nevyžaduje žádné změny kromě změna head název, který má úplný název clusteru IaaS.

## <a name="next-steps"></a>Další postup
* V tématu [tyto prostředky](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) pro další informace o spuštění úlohy aplikace Excel pomocí sady HPC Pack.
* V tématu [Správa služeb SOA v Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) Další informace o nasazení a správě SOA services pomocí sady HPC Pack.

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
