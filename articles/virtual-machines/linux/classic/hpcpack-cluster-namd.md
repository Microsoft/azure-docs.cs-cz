---
title: NAMD pomocí sady Microsoft HPC Pack na virtuální počítače s Linuxem | Microsoft Docs
description: Nasazení clusteru s podporou sady Microsoft HPC Pack v Azure a spustit simulaci NAMD s charmrun v několika výpočetních uzlech Linux
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 76072c6b-ac35-4729-ba67-0d16f9443bd7
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/13/2016
ms.author: danlep
ms.openlocfilehash: 61dd49d4bd3183b6b9a78036d6d7d01798e4dc89
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="run-namd-with-microsoft-hpc-pack-on-linux-compute-nodes-in-azure"></a>Spuštění NAMD se sadou Microsoft HPC Pack ve výpočetních uzlech Linuxu v Azure
Tento článek ukazuje jeden ze způsobů spuštění Linux vysoce výkonné výpočty (HPC) zatížení na virtuálních počítačích Azure. Zde můžete nastavit [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) cluster v Azure s Linuxem výpočetních uzlů a spusťte [NAMD](http://www.ks.uiuc.edu/Research/namd/) simulace vypočítat a vizualizovat strukturu velké biomolekulárních systému.  

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

* **NAMD** (pro program molekulární Dynamics Nanoscale) je paralelní molekulární dynamics balíček určený pro vysoce výkonné simulace systémů velké biomolekulárních obsahující až miliony atomů. Mezi tyto systémy patří například viry, struktury buňky a velké bílkoviny. NAMD škáluje stovky jader pro typické simulace a více než 500 000 jader pro největší simulace.
* **Microsoft HPC Pack** poskytuje funkce, které chcete spouštět ve velkém měřítku HPC a paralelní aplikace v clusterech místního počítače nebo virtuální počítače Azure. Původně vyvinut jako řešení pro úlohy Windows HPC, HPC Pack teď podporuje spouštění aplikací prostředí HPC pro Linux v systému Linux výpočetní uzel virtuální počítače nasazené na clusteru HPC Pack. V tématu [začít pracovat s Linux výpočetní uzly v clusteru služby HPC Pack v Azure](hpcpack-cluster.md) úvod.

## <a name="prerequisites"></a>Požadavky
* **Výpočetní uzly clusteru HPC Pack operačního systému Linux** -nasazení clusteru HPC Pack s Linux výpočetní uzly v Azure pomocí buď [šablony Azure Resource Manageru](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) nebo [skript prostředí Azure PowerShell](hpcpack-cluster-powershell-script.md). V tématu [začít pracovat s Linux výpočetní uzly v clusteru služby HPC Pack v Azure](hpcpack-cluster.md) pro požadavky a kroky pro jednu z možností. Pokud zvolíte možnost nasazení skriptu prostředí PowerShell, naleznete v souboru konfigurace ukázka v ukázkové soubory na konci tohoto článku. Tento soubor nakonfiguruje clusteru služby založené na Azure HPC Pack skládající se z hlavního uzlu systému Windows Server 2012 R2 a čtyři výpočetní uzly velké 6.6 CentOS velikost. Tento soubor přizpůsobte podle potřeby pro vaše prostředí.
* **Soubory softwaru a kurzu NAMD** -NAMD stažení softwaru pro Linux z [NAMD](http://www.ks.uiuc.edu/Research/namd/) lokality (vyžaduje registraci). Tento článek je založena na NAMD verze 2.10 a používá [Linux-x86_64 (64-bit Intel nebo AMD s sítě Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310) archivu. Také stáhnout [NAMD kurz soubory](http://www.ks.uiuc.edu/Training/Tutorials/#namd). Stahování jsou soubory .tar a potřebujete nástroj Windows extrahujte soubory z hlavního uzlu clusteru. Extrahujte soubory, postupujte podle pokynů dále v tomto článku. 
* **VMD** (volitelné) – Chcete-li zobrazit výsledky úlohy NAMD, stáhněte a nainstalujte program molekulární vizualizace [VMD](http://www.ks.uiuc.edu/Research/vmd/) na počítači podle svého výběru. Aktuální verze je 1.9.2. V tématu VMD ke stažení začít pracovat.  

## <a name="set-up-mutual-trust-between-compute-nodes"></a>Nastavení vzájemného vztahu důvěryhodnosti mezi výpočetní uzly
Spuštění úlohy mezi uzly ve více uzlech Linux vyžaduje uzly důvěřovat navzájem (podle **rsh** nebo **ssh**). Při vytváření clusteru HPC Pack pomocí skriptu pro nasazení Microsoft HPC Pack IaaS, skript automaticky nastaví trvalé vzájemné vztahu důvěryhodnosti pro účet správce, který zadáte. Pro uživatele bez oprávnění správce, které vytvoříte v doméně do clusteru budete muset nainstalovat dočasné vzájemné vztah důvěryhodnosti mezi uzly, pokud je přidělen úlohu. Pak odstraňte relace po dokončení úlohy. Chcete-li to provést pro každého uživatele, poskytnout pár klíče RSA do clusteru, který HPC Pack používá k navázání vztahu důvěryhodnosti. Postupujte podle pokynů.

### <a name="generate-an-rsa-key-pair"></a>Vygenerovat pár klíče RSA
Snadno vygenerovat pár klíče RSA, který obsahuje veřejný klíč a soukromý klíč a spuštěním sady Linux **ssh-keygen** příkaz.

1. Přihlaste se na počítač se systémem Linux.
2. Spusťte následující příkaz:
   
   ```bash
   ssh-keygen -t rsa
   ```
   
   > [!NOTE]
   > Stiskněte klávesu **Enter** použít výchozí nastavení, dokud se nedokončí příkaz. Nezadávejte přístupové heslo zde; Po zobrazení výzvy k zadání hesla, stačí stisknout klávesu **Enter**.
   > 
   > 
   
   ![Vygenerovat pár klíče RSA][keygen]
3. Změňte adresář na adresář ~/.ssh. Privátní klíč uložen v id_rsa a veřejný klíč v id_rsa.pub.
   
   ![Privátní a veřejné klíče][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Přidat dvojici klíčů do clusteru HPC Pack
1. [Připojit pomocí vzdálené plochy](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) k hlavnímu uzlu virtuálních počítačů využívající tuto doménu můžete zadané údaje po nasazení clusteru (například hpc\clusteradmin). Spravujete z hlavního uzlu clusteru.
2. Použijte standardní postupy systému Windows Server k vytvoření uživatelského účtu domény v doméně služby Active Directory do clusteru. Například pomocí nástroje Active Directory uživatele a počítače z hlavního uzlu. V příkladech v tomto článku předpokládá, že vytvoříte uživatele domény s názvem hpcuser v doméně hpclab (hpclab\hpcuser).
3. Přidejte uživatele domény do clusteru HPC Pack jako uživatel clusteru. Pokyny najdete v tématu [přidat nebo odebrat uživatele clusteru](https://technet.microsoft.com/library/ff919330.aspx).
4. Vytvořte soubor s názvem C:\cred.xml a zkopírujte do ní data klíče RSA. Příklad najdete v ukázkových souborů na konci tohoto článku.
   
   ```
   <ExtendedData>
     <PrivateKey>Copy the contents of private key here</PrivateKey>
     <PublicKey>Copy the contents of public key here</PublicKey>
   </ExtendedData>
   ```
5. Otevřete příkazový řádek a zadejte následující příkaz pro nastavení data přihlašovací údaje pro účet hpclab\hpcuser. Můžete použít **extendeddata** parametr předat název souboru C:\cred.xml jste vytvořili pro klíčová data.
   
   ```command
   hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
   ```
   
   Tento příkaz se dokončí úspěšně bez výstupu. Po nastavení přihlašovacích údajů pro uživatelské účty, které potřebujete ke spuštění úloh, cred.xml soubor uložte na bezpečném místě, nebo ho odstranit.
6. Pokud jste vygenerovali páru klíčů RSA na jednom z uzlů Linux, nezapomeňte odstranit klíče po dokončení jejich používání. HPC Pack nejsou nastaveny vzájemné vztahu důvěryhodnosti, pokud najde existující soubor id_rsa nebo id_rsa.pub souboru.

> [!IMPORTANT]
> Nedoporučujeme spuštěna úlohu Linux jako správce clusteru na sdílený clusteru, protože úloha odeslána správce spustí pomocí kořenového účtu v uzlech systému Linux. Úloha odeslána uživatel není správcem spouští pod místním uživatelským účtem Linux se stejným názvem jako uživatel úlohu. V takovém případě HPC Pack nastaví vzájemné vztahu důvěryhodnosti pro tohoto uživatele Linux pro všechny uzly, které jsou přiděleny do úlohy. Můžete nastavit uživatele Linux na Linuxových uzlů ručně před spuštěním úlohy, nebo HPC Pack vytváří uživatele automaticky, když je úloha odeslána. Pokud uživatel vytvoří HPC Pack HPC Pack neodstraní po dokončení úlohy. Pokud chcete zkrátit ohrožení zabezpečení, se odeberou klíče po dokončení úlohy na uzlech.
> 
> 

## <a name="set-up-a-file-share-for-linux-nodes"></a>Nastavení sdílené složky pro Linuxové uzly
Teď nastavit sdílenou složku SMB a připojit sdílenou složku na všech uzlech Linux umožňující Linuxové uzly pro přístup k souborům NAMD s běžné cestou. Toto jsou kroky připojit sdílenou složku z hlavního uzlu. Sdílenou složku se doporučuje pro distribuce například CentOS 6.6, které aktuálně nepodporují službu Azure File. Pokud uzly Linux podporují sdílenou složku Azure, najdete v části [postup používání Azure File storage s Linuxem](../../../storage/files/storage-how-to-use-files-linux.md). Další možnosti sdílení s HPC Pack souborů, najdete v části [začít pracovat s Linux výpočetní uzly v clusteru HPC Pack v Azure](hpcpack-cluster.md).

1. Vytvořte složku na hlavního uzlu a sdílejte ji pro všechny uživatele, a to nastavením oprávnění pro čtení a zápis. V tomto příkladu \\ \\CentOS66HN\Namd je název složky, kde CentOS66HN je název hostitele hlavního uzlu.
2. Vytvořte podsložku s názvem namd2 ve sdílené složce. V namd2 vytvořte další podsložku s názvem namdsample.
3. Extrahujte soubory NAMD ve složce tak, že používáte verzi systému Windows **vkládání** nebo jiný nástroj Windows, který pracuje s archivy .tar. 
   
   * Extrahování archiv vkládání NAMD \\ \\CentOS66HN\Namd\namd2.
   * Extrahujte soubory kurzu v části \\ \\CentOS66HN\Namd\namd2\namdsample.
4. Otevřete okno prostředí Windows PowerShell a spusťte následující příkazy Připojit sdílenou složku na Linuxových uzlů.
   
    ```command
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

První příkaz vytvoří složku s názvem /namd2 ve všech uzlech v LinuxNodes skupiny. V druhém příkazu připojí //CentOS66HN/Namd/namd2 sdílené složky do složky s dir_mode a file_mode bits nastavený na 777. *Uživatelské jméno* a *heslo* v příkazu by měl být přihlašovací údaje uživatele z hlavního uzlu.

> [!NOTE]
> "\`" Symbol v druhém příkazu je symbol řídicí pro prostředí PowerShell. "\`," znamená "," (čárku) je součástí příkazu.
> 
> 

## <a name="create-a-bash-script-to-run-a-namd-job"></a>Vytvoření skriptu Bash a spusťte úlohu NAMD
Potřebuje vaše úlohy NAMD *seznamu* souboru **charmrun** k určení počtu uzlů pro použití při spouštění NAMD procesy. Použít skript Bash, který generuje souboru seznamu a spouští **charmrun** s tohoto souboru seznamu. Potom můžete odeslat úlohu NAMD ve Správci clusteru HPC který volá tento skript.

Pomocí textového editoru podle vaší volby, vytvořte skript Bash ve složce /namd2 obsahující programové soubory NAMD a pojmenujte ji hpccharmrun.sh. Pro rychlé testování konceptu, zkopírujte skript hpccharmrun.sh příklad uvedený na konci tohoto článku, přejděte na [odeslat úlohu NAMD](#submit-a-namd-job).

> [!TIP]
> Uložte skript jako textový soubor s Linuxem řádek zakončení (pouze LF, není CR LF). To zajišťuje, že ho běží správně na Linuxových uzlů.
> 
> 

Následují informace o jaké tento skript bash. 

1. Definujte některé proměnné.
   
   ```bash
   #!/bin/bash
   
   # The path of this script
   SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
   # Charmrun command
   CHARMRUN=${SCRIPT_PATH}/charmrun
   # Argument of ++nodelist
   NODELIST_OPT="++nodelist"
   # Argument of ++p
   NUMPROCESS="+p"
   ```
2. Získáte informace o uzlu ze proměnné prostředí. $NODESCORES ukládá seznam rozdělení slov z $CCP_NODES_CORES. $COUNT je velikost $NODESCORES.
   
   ```
   # Get node information from the environment variables
   NODESCORES=(${CCP_NODES_CORES})
   COUNT=${#NODESCORES[@]}
   ```    
   
   Formát pro proměnnou CCP_NODES_CORES $ vypadá takto:
   
   ```
   <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
   ```
   
   Tato proměnná uvádí celkový počet uzlů, názvy a počet jader na každém uzlu, které jsou přiděleny do úlohy. Například pokud úloha potřebuje 10 jader ke spuštění, $CCP_NODES_CORES hodnotu podobně jako:
   
   ```
   3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
   ```
3. Pokud není nastavena proměnná CCP_NODES_CORES $, spusťte **charmrun** přímo. (To by mělo pouze nastat při spuštění tohoto skriptu přímo na Linuxových uzlů.)
   
   ```
   if [ ${COUNT} -eq 0 ]
   then
     # CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
     #echo ${CHARMRUN} $*
     ${CHARMRUN} $*
   ```
4. Nebo vytvořte souboru seznamu pro **charmrun**.
   
   ```
   else
     # Create the nodelist file
     NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$
   
     # Write the head line
     echo "group main" > ${NODELIST_PATH}
   
     # Get every node name and number of cores and write into the nodelist file
     I=1
     while [ ${I} -lt ${COUNT} ]
     do
         echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
         let "I=${I}+2"
     done
   ```
5. Spustit **charmrun** s souboru seznamu získat návratový stav a odeberte souboru seznamu na konci.
   
   ${CCP_NUMCPUS} je nastaven hlavního uzlu HPC Pack jiné proměnné prostředí. Ukládá číslo celkový počet jader přidělené této úlohy. Můžeme použít k určení počtu procesů pro charmrun.
   
   ```
   # Run charmrun with nodelist arg
   #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   
   RTNSTS=$?
   rm -f ${NODELIST_PATH}
   fi
   
   ```
6. Ukončete s **charmrun** návratový stav.
   
   ```
   exit ${RTNSTS}
   ```

Níže najdete informace v souboru seznamu, který generuje skript:

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

Příklad:

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```

## <a name="submit-a-namd-job"></a>Odeslání úlohy NAMD
Nyní jste připraveni se odeslat úlohu NAMD ve Správci clusteru HPC.

1. Připojení k vaší hlavního uzlu clusteru a spusťte Správce clusteru HPC.
2. V **Správa prostředků**, zajistěte, aby výpočetní uzly Linux v **Online** stavu. Pokud tomu tak není, vyberte je a klikněte na tlačítko **přepnout do režimu Online**.
3. V **úlohy správy**, klikněte na tlačítko **nová úloha**.
4. Zadejte název úlohy, jako *hpccharmrun*.
   
   ![Nové úlohy HPC][namd_job]
5. Na **podrobnosti úlohy** v části **úlohy prostředky**, vyberte typ prostředku jako **uzlu** a nastavte **minimální** na 3. , jsme spustit úlohu na tři uzly Linux a každý uzel má čtyři jádra.
   
   ![Prostředky úlohy][job_resources]
6. Klikněte na tlačítko **upravit úlohy** v levém navigačním panelu a pak klikněte na tlačítko **přidat** k přidání úkolu do úlohy.    
7. Na **podrobností úlohy a přesměrování vstupu a výstupu** nastavte následující hodnoty:
   
   * **Příkazový řádek** -
     `/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`
     
     > [!TIP]
     > Předchozí příkazový řádek je jeden příkaz bez konce řádků. Se zobrazí na několika řádcích pod zabalí **příkazového řádku**.
     > 
     > 
   * **Pracovní adresář** -/namd2
   * **Minimální** – 3
     
     ![Detail úlohy][task_details]
     
     > [!NOTE]
     > Pracovní adresář tady nastavíte protože **charmrun** pokusí přejděte na stejný pracovní adresář na každém uzlu. Pokud není nastavena pracovní adresář, HPC Pack spustí příkaz náhodným názvem složky vytvořené v jednom z uzlů Linux. To způsobí, že k následující chybě na ostatních uzlech: `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` k tomuto problému nedošlo, zadejte cestu ke složce, která je přístupná ve všech uzlech jako pracovní adresář.
     > 
     > 
8. Klikněte na tlačítko **OK** a pak klikněte na **odeslání** ke spuštění této úlohy.
   
   Ve výchozím nastavení HPC Pack předá úlohu jako váš aktuální účet přihlášeného uživatele. Dialogové okno může zobrazit výzva k zadání uživatelského jména a hesla, po kliknutí na tlačítko **odeslání**.
   
   ![Přihlašovací údaje k úloze][creds]
   
   Za určitých podmínek HPC Pack pamatuje informace o uživateli, zadejte před a nezobrazí tohoto dialogového okna. Chcete-li HPC Pack jej znovu zobrazit, zadejte následující příkaz na příkazovém řádku a potom odeslání úlohy.
   
   ```command
   hpccred delcreds
   ```
9. Úloha trvá několik minut na dokončení.
10. Najít protokol úlohy v \\ <headnodeName>\Namd\namd2\namd2_hpccharmrun.log a výstupní soubory v \\ <headnodeName>\Namd\namd2\namdsample\1-2-sphere\.
11. Volitelně můžete spusťte VMD zobrazíte výsledky úlohy. Kroky pro vizualizaci NAMD výstupní soubory (v tomto případě ubiquitin bílkovin molekuly v oblasti horních) jsou nad rámec tohoto článku. V tématu [NAMD kurzu](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf) podrobnosti.
    
    ![Výsledky úlohy][vmd_view]

## <a name="sample-files"></a>Ukázkové soubory
### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Ukázkový soubor XML konfigurace pro nasazení clusteru pomocí skriptu prostředí PowerShell
```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
      <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS66HN</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
    <ServiceName>MyLnxCNService</ServiceName>
     <VMSize>Large</VMSize>
     <NodeCount>4</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>    
```

### <a name="sample-credxml-file"></a>Ukázkový soubor cred.xml
```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```

### <a name="sample-hpccharmrunsh-script"></a>Ukázkový skript hpccharmrun.sh
```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
    #echo ${CHARMRUN} $*
    ${CHARMRUN} $*
else
    # Create the nodelist file
    NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    # Write the head line
    echo "group main" > ${NODELIST_PATH}

    # Get every node name & cores and write into the nodelist file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 

<!--Image references-->
[keygen]:media/hpcpack-cluster-namd/keygen.png
[keys]:media/hpcpack-cluster-namd/keys.png
[namd_job]:media/hpcpack-cluster-namd/namd_job.png
[job_resources]:media/hpcpack-cluster-namd/job_resources.png
[creds]:media/hpcpack-cluster-namd/creds.png
[task_details]:media/hpcpack-cluster-namd/task_details.png
[vmd_view]:media/hpcpack-cluster-namd/vmd_view.png
