---
title: Řešení potíží s nasazení tak, aby Kubernetes pro Azure Stack | Dokumentace Microsoftu
description: Informace o řešení potíží s nasazení tak, aby Kubernetes pro Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 472dfc04cea65cab39d177bb214c417d229b71d2
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956716"
---
# <a name="troubleshoot-your-deployment-to-kubernetes-to-azure-stack"></a>Řešení potíží s nasazení tak, aby Kubernetes pro Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

> [!Note]  
> Kubernetes ve službě Azure Stack je ve verzi preview.

Následující článek vypadá na odstraňování potíží u clusteru Kubernetes. Můžete zkontrolovat výstrahu pro nasazení a zkontrolovat stav nasazení podle prvků vyžadovaných pro nasazení. Může být potřeba shromažďovat protokoly nasazení ze služby Azure Stack nebo virtuálních počítačů s Linuxem, které hostují Kubernetes. Potřebujete také pracovat se správcem vaší služby Azure Stack a protokoly načíst z koncového bodu správy.

## <a name="overview-of-deployment"></a>Přehled nasazení

Předtím, než se pustíte do odstraňování clusteru, můžete chtít zkontrolovat proces nasazení clusteru Kubernetes se službou Azure Stack. Nasazení používá šablonu Azure Resource Manageru řešení k vytvoření virtuálních počítačů a nainstaluje modul ACS pro váš cluster.

### <a name="deployment-workflow"></a>Pracovní postup nasazení

Následující diagram znázorňuje obecný postup nasazení clusteru.

![Nasazení Kubernetes procesu](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>Kroky nasazení

1. Shromažďování vstupních parametrů z položky marketplace.

    Zadejte hodnoty, budete muset nastavit Kubernetes cluster, včetně:
    -  **Uživatelské jméno**: uživatelské jméno pro virtuální počítače Linux, které jsou součástí clusteru Kubernetes a DVM.
    -  **Veřejný klíč SSH**: klíč, který se používá pro autorizaci všechny počítače s Linuxem, které byly vytvořeny jako součást clusteru Kubernetes a DVM.
    -  **Princip služby**: ID, který se používá od poskytovatele cloudu Kubernetes Azure. ID klienta, který je identifikován jako ID aplikace při vytváření instančního objektu služby. 
    -  **Tajný kód klienta**: jejich klíče vytvořené při vytváření instančního objektu služby.

2. Vytvoření nasazení virtuálního počítače a rozšíření vlastních skriptů.
    -  Vytvoření nasazení virtuálního počítače s Linuxem pomocí image Linuxu marketplace **Ubuntu Server 16.04-LTS**.
    -  Stažení a spuštění rozšíření vlastních skriptů z webu marketplace. Skript je **vlastních skriptů pro Linux 2.0**.
    -  Spustí vlastní skript DVM. Skript provede následující úlohy:
        1. Získá koncový bod Galerie z koncového bodu metadat Azure Resource Manageru.
        2. Získá ID prostředku služby active directory z koncového bodu metadat Azure Resource Manageru.
        3. Načte modelu rozhraní API pro modul ACS.
        4. Nasadí do clusteru Kubernetes ACS Engine a uloží profil cloudové služby Azure Stack na `/etc/kubernetes/azurestackcloud.json`.
3. Vytvoření hlavních virtuálních počítačů.

4. Stáhněte a spusťte skript rozšíření zákazníka.

5. Hlavní skript spusťte.

    Skript provede následující úlohy:
    - Nainstaluje etcd, Docker a Kubernetes prostředky, jako jsou kubelet. etcd je distribuovaná hodnota klíče úložiště, který poskytuje způsob, jak ukládat data napříč clusterem počítačů. Docker podporuje virtualizations holou úroveň operačního systému, známé jako kontejnery. Kubelet je agenta uzlu, na kterém běží na všech uzlech Kubernetes.
    - Nastaví etcd služby.
    - Nastaví kubelet služby.
    - Spustí kubelet. Tato úloha zahrnuje následující kroky:
        1. Spustí službu rozhraní API.
        2. Spustí službu řadiče.
        3. Spustí službu scheduler.
6. Vytvoření agenta pro virtuální počítače.

7. Stažení a spuštění rozšíření vlastních skriptů.

7. Spusťte skript agenta. Agent vlastní skript provede následující úlohy:
    - Nainstaluje etcd
    - Nastaví službu kubelet
    - Připojí ke clusteru Kubernetes

## <a name="steps-for-troubleshooting"></a>Kroky pro řešení potíží

Shromažďování protokolů na virtuálních počítačích, které podporují vašemu clusteru Kubernetes. Můžete také zkontrolovat protokol nasazení. Možná budete muset obraťte se na správce služby Azure Stack k ověření verze služby Azure Stack, které potřebujete k používání a získat protokoly z Azure Stack, která se vztahují na vaše nasazení.

1. Zkontrolujte [stav nasazení](#review-deployment-status) a [tyto protokoly načíst](#get-logs-from-a-vm) z hlavního uzlu v clusteru Kubernetes.
2. Ujistěte se, že používáte nejnovější verzi služby Azure Stack. Pokud si nejste jistí, kterou verzi používáte, obraťte se na svého správce služby Azure Stack. Čas marketplace clusteru Kubernetes 0.3.0 vyžaduje verzi služby Azure Stack 1808 nebo vyšší.
3.  Projděte si soubory vytvoření virtuálního počítače. Může mít vyskytly následující problémy:  
    - Veřejný klíč může být neplatný. Projděte si klíč, který jste vytvořili.  
    - Vytvoření virtuálního počítače může mít aktivuje vnitřní chybu nebo Chyba při vytváření aktivované. Několika faktory mohou způsobit chyby, včetně omezení kapacity pro vaše předplatné služby Azure Stack.
    - Ujistěte se, že plně kvalifikovaný název domény (FDQN) pro virtuální počítač začíná duplicitní předpona.
4.  Pokud je virtuální počítač **OK**, pak vyhodnotit DVM. Pokud DVM chybová zpráva:

    - Veřejný klíč může být neplatný. Projděte si klíč, který jste vytvořili.  
    - Budete muset požádat správce služby Azure Stack, aby tyto protokoly načíst s použitím privilegovaných koncových bodů pro službu Azure Stack. Další informace najdete v tématu [diagnostické nástroje služby Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics).
5. Pokud máte dotaz k vašemu nasazení, můžete ji publikovat nebo se pokud někdo už odpověděl na dotaz v [fórum pro Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="review-deployment-status"></a>Zkontrolujte stav nasazení

Při nasazování clusteru Kubernetes, můžete zkontrolovat stav nasazení zkontrolujte případné problémy.

1. Otevřít [portálu Azure Stack](https://portal.local.azurestack.external).
2. Vyberte **skupiny prostředků**a potom vyberte název skupiny prostředků, kterou jste použili při nasazování clusteru Kubernetes.
3. Vyberte **nasazení**a pak vyberte **název nasazení**.

    ![Řešení potíží](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  Naleznete v okně řešení potíží. Každý nasazený prostředek obsahuje následující informace:
    
    | Vlastnost | Popis |
    | ----     | ----        |
    | Prostředek | Název prostředku. |
    | Typ | Poskytovatel prostředků a typ prostředku. |
    | Status | Stav položky. |
    | Časové razítko | Časové razítko UTC času. |
    | Podrobnosti o operaci | Podrobnosti operace, jako je poskytovatel prostředků, která byla zahrnuta v operaci, koncový bod prostředku a název prostředku. |

    Každá položka má zelená nebo červená ikona stavu.

## <a name="get-logs-from-a-vm"></a>Získání protokolů z virtuálního počítače

Generovat protokoly, budete potřebovat připojení k hlavnímu virtuálnímu počítači pro váš cluster, otevřete příkazový řádek bash a spusťte skript. Hlavní najdete ve vaší skupině prostředků clusteru virtuálního počítače a názvem `k8s-master-<sequence-of-numbers>`. 

### <a name="prerequisites"></a>Požadavky

Je nutné bash výzvy na počítači, který používáte ke správě služby Azure Stack. Použijte bash spouštět skripty, které přístup k protokolům. Na počítači s Windows můžete použít příkazový řádek bash, který je nainstalován pomocí Gitu. Pokud chcete získat nejnovější verzi gitu, naleznete v tématu [pro stažení Gitu](https://git-scm.com/downloads).

### <a name="get-logs"></a>Získání protokolů

Pokud chcete získat protokoly, proveďte následující kroky:

1. Otevřete příkazový řádek bash. Pokud používáte Git na počítači s Windows, můžete otevřít příkazový řádek bash v následující cestě: `c:\programfiles\git\bin\bash.exe`.
2. Spusťte následující příkazy bash:

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    sudo chmod 744 getkuberneteslogs.sh
    ```

    > [!Note]  
    > Na Windows, není nutné ke spuštění `sudo`. Místo toho můžete použít jenom `chmod 744 getkuberneteslogs.sh`.

3. Ve stejné relaci, spusťte následující příkaz s parametry, aktualizovat, aby odpovídaly vašemu prostředí:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file id_rsa --user azureuser --vmdhost 192.168.102.37
    ```

4. Zkontrolujte parametry a nastavte hodnoty podle vašeho prostředí.
    | Parametr           | Popis                                                                                                      | Příklad:                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -i,-identity soubor | RSA souboru privátního klíče pro připojení Kubernetes hlavním virtuálním počítači. Tento klíč musí začínat znakem `-----BEGIN RSA PRIVATE KEY-----` | C:\data\privatekey.pem                                                        |
    | -h,--hostitele          | Veřejnou IP adresu nebo název plně kvalifikované domény (FQDN) hlavního uzlu clusteru Kubernetes virtuálního počítače. Název virtuálního počítače začíná `k8s-master-`.                       | IP: 192.168.102.37<br><br>Plně kvalifikovaný název domény: k8s-12345.local.cloudapp.azurestack.external      |
    | -u, - uživatel          | Uživatelské jméno hlavního uzlu clusteru Kubernetes virtuálního počítače. Tento název se nastavit při konfiguraci položku marketplace.                                                                    | azureuser                                                                     |
    | -d – vmdhost       | Veřejná IP adresa nebo plně kvalifikovaný název domény DVM. Název virtuálního počítače začíná `vmd-`.                                                       | IP: 192.168.102.38<br><br>Server DNS: vmd-dnsk8-frog.local.cloudapp.azurestack.external |

   Když přidáte všechny hodnoty parametrů, může vypadat jako v následujícím kódu:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\secretsecret.pem" --user azureuser --vmdhost 192.168.102.37
     ```

    Úspěšné spuštění vytvoří v protokolech.

    ![Generované protokoly](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-generated-logs.png)


4. Načtěte protokoly ve složkách, které byly vytvořeny pomocí příkazu. Tento příkaz vytvoří nové složky a opatří je.
    - KubernetesLogs*YYYY-MM-DD-XX-XX-XX-XXX*
        - Dvmlogs
        - Acsengine. kubernetes dvm.log

## <a name="next-steps"></a>Další postup

[Nasazení Kubernetes pro Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)

[Přidání clusteru Kubernetes na webu Marketplace (pro operátory Azure stacku)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes v Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
