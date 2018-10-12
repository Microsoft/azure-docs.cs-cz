---
title: Řešení potíží s nasazení tak, aby Kubernetes (K8) do služby Azure Stack | Dokumentace Microsoftu
description: Informace o řešení potíží s nasazení tak, aby Kubernetes (K8) do služby Azure Stack.
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
ms.date: 10/11/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: fbb51d8dc3b1ea4c6b34120e8fe35474ae949cf2
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116908"
---
# <a name="troubleshoot-your-deployment-to-kubernetes-to-azure-stack"></a>Řešení potíží s nasazení tak, aby Kubernetes pro Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

> [!Note]  
> Kubernetes ve službě Azure Stack je ve verzi preview.

Následující článek vypadá na odstraňování potíží u clusteru Kubernetes. Můžete zkontrolovat výstrahu pro nasazení a zkontrolovat stav nasazení podle prvků vyžadovaných pro nasazení. Můžete shromažďovat protokoly nasazení ze služby Azure Stack nebo virtuálních počítačů s Linuxem, které hostují Kubernetes. Kromě toho budete muset pracovat se správcem vaší služby Azure Stack a protokoly načíst z koncového bodu správy.

## <a name="overview-of-deployment"></a>Přehled nasazení

Než se ponoříte do kroků pro řešení potíží s vašeho clusteru, můžete zkontrolovat proces nasazení clusteru Kubernetes se službou Azure Stack. Nasazení šablony Azure Resource Manageru řešení používá k vytvoření virtuálních počítačů a nainstaluje modul ACS pro váš cluster.

### <a name="deployment-workflow"></a>Pracovní postup nasazení

Následující diagram znázorňuje obecný postup nasazení clusteru.

![Nasazení Kubernetes procesu](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>Kroky nasazení

1. Shromažďuje vstupní parametry z položky marketplace.

    Zadejte hodnoty, které je potřeba nastavit v clusteru Kubernetes včetně:
    -  **Uživatelské jméno** uživatelské jméno pro virtuální počítače Linux, které jsou součástí clusteru Kubernetes a DVM.
    -  **Veřejný klíč SSH** klíč používaný k autorizaci vytvořenou jako součást clusteru Kubernetes a DVM všechny počítače s Linuxem
    -  **Princip služby** ID použít od poskytovatele cloudu Kubernetes Azure. ID klienta, který je identifikován jako ID aplikace při vytváření instančního objektu služby. 
    -  **Tajný kód klienta** jejich klíče vytvořené při vytváření instančního objektu služby.

2. Vytvoří nasazení virtuálního počítače a rozšíření vlastních skriptů.
    -  Vytvoří nasazení virtuálního počítače s Linuxem pomocí Linuxové image marketplace, **Ubuntu Server 16.04-LTS**.
    -  Stažení a spuštění rozšíření vlastních skriptů z webu marketplace. Skript je **vlastních skriptů pro Linux 2.0**.
    -  Spustí vlastní skript DVM. Tento skript:
        1. Získá koncový bod Galerie z koncového bodu metadat Azure Resource Manageru.
        2. Získá ID prostředku služby active directory z koncového bodu metadat Azure Resource Manageru.
        3. Načte modelu rozhraní API pro modul ACS.
        4. Nasadí do clusteru Kubernetes ACS Engine a uloží profil cloudové služby Azure Stack na `/etc/kubernetes/azurestackcloud.json`.
3. Vytvoří hlavních virtuálních počítačů.

    Stáhne a spustí rozšíření vlastních skriptů.

4. Spouští hlavní skript.

    Tento skript:
    - Nainstaluje etcd, Docker a Kubernetes prostředky, jako jsou kubelet. etcd je distribuovaná hodnota klíče úložiště, který poskytuje způsob, jak ukládat data napříč clusterem počítačů. Docker podporuje operační systém úrovně virtualizations holou označované jako kontejnery. Kubelet je agenta uzlu, na kterém běží na všech uzlech Kubernetes.
    - Nastaví etcd služby.
    - Nastaví Kubelet služby.
    - Spustí kubelet. To zahrnuje následující:
        1. Spustí službu rozhraní API.
        2. Spustí službu řadiče.
        3. Spustí službu Plánovač.
5. Vytvoří virtuální počítače agenta.

    Stáhne a spustí rozšíření vlastních skriptů.

6. Spustí skript agenta. Vlastní skript agenta:
    - Nainstalujte etcd.
    - Nastavení služby Kubelet.
    - Připojí ke clusteru Kubernetes.

## <a name="steps-for-troubleshooting"></a>Kroky pro řešení potíží

Shromažďování protokolů na virtuální počítače podporuje svůj cluster Kubernetes. Můžete také zkontrolovat protokol nasazení. Také budete muset požádat správce služby Azure Stack k ověření verze služby Azure Stack, který používáte a získat protokoly ze související s nasazením služby Azure Stack.

1. Zkontrolujte [stav nasazení](#review-deployment-status) a [tyto protokoly načíst](#get-logs-from-a-vm) z hlavního uzlu v clusteru Kubernetes.
2. Budete muset použít nejnovější verzi služby Azure Stack. Pokud si nejste jisti vaši verzi sady Azure Stack, obraťte se na svého správce služby Azure Stack. Čas marketplace clusteru Kubernetes 0.3.0 vyžaduje verzi služby Azure Stack 1808 nebo vyšší.
3.  Projděte si soubory vytvoření virtuálního počítače. Může mít došlo k následujícím problémům:  
    - Veřejný klíč může být neplatný. Projděte si klíč, který jste vytvořili.  
    - Vytvoření virtuálního počítače může mít aktivuje vnitřní chybu nebo Chyba při vytváření aktivované. Chyby může být způsobeno několika faktory, včetně omezení kapacity pro vaše předplatné služby Azure Stack.
    - Začněte plně kvalifikovaný název domény (FDQN) pro virtuální počítač s předponou duplicitní?
4.  Pokud je virtuální počítač **OK**, pak vyhodnotit DVM. Pokud DVM chybová zpráva:

    - Veřejný klíč může být neplatný. Projděte si klíč, který jste vytvořili.  
     - Musíte kontaktovat správce služby Azure Stack, aby tyto protokoly načíst pro službu Azure Stack pomocí privilegovaných koncové body. Další informace najdete v tématu [diagnostické nástroje služby Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics).
5. Pokud máte dotazy k vašemu nasazení, můžete se zveřejněte svůj dotaz nebo se pokud někdo už odpověděl na dotaz v [fórum pro Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="review-deployment-status"></a>Zkontrolujte stav nasazení

Při nasazování clusteru Kubernetes můžete zobrazit všechny problémy, můžete zkontrolovat stav nasazení.

1. Otevřít [portálu Azure Stack](https://portal.local.azurestack.external).
2. Vyberte **skupiny prostředků**, a pak vyberte název skupiny prostředků, použije při nasazování clusteru Kubernetes.
3. Vyberte **nasazení** a pak **název nasazení**.

    ![Řešení potíží](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  Naleznete v okně řešení potíží. Každý nasazený prostředek obsahuje následující informace.
    
    | Vlastnost | Popis |
    | ----     | ----        |
    | Prostředek | Název prostředku. |
    | Typ | Poskytovatel prostředků a typ prostředku. |
    | Status | Stav položky. |
    | Časové razítko | Časové razítko UTC času. |
    | Podrobnosti o operaci | Podrobnosti operace, jako je poskytovatel prostředků zahrnutých v operaci, koncový bod prostředků a název prostředku. |

    Každá položka bude mít červenou nebo zelenou ikona stavu.

## <a name="get-logs-from-a-vm"></a>Získání protokolů z virtuálního počítače

Bude potřebovat pro připojení k hlavnímu virtuálnímu počítači pro váš cluster, otevřete příkazový řádek bash a spusťte skript, který bude generovat protokoly. Hlavní najdete ve vaší skupině prostředků clusteru a názvem `k8s-master-<sequence-of-numbers>`. 

### <a name="prerequisites"></a>Požadavky

Budete potřebovat bash výzvu na počítači využití ke správě služby Azure Stack. Použijte bash spouštět skripty, které přístup k protokolům. Na počítači s Windows můžete použít příkazový řádek bash, které jsou nainstalované s úložištěm Git. Pokud chcete získat nejnovější verzi gitu, naleznete v tématu [pro stažení gitu](https://git-scm.com/downloads).

### <a name="get-logs"></a>Získání protokolů

1. Otevřete příkazový řádek bash. Pokud používáte git na počítači s Windows, můžete otevřít příkazový řádek bash v následující cestě: `c:\programfiles\git\bin\bash.exe`.
2. Spusťte následující příkazy bash:

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    sudo chmod 744 getkuberneteslogs.sh
    ```

    > [!Note]  
    > Na Windows, není nutné ke spuštění `sudo` a můžete pouze `chmod 744 getkuberneteslogs.sh`.

3. Ve stejné relaci, spusťte následující příkaz s parametry, aktualizovat, aby odpovídaly vašemu prostředí.

    ```Bash  
    ./getkuberneteslogs.sh --identity-file id_rsa --user azureuser --vmdhost 192.168.102.37
    ```

    Zkontrolujte parametry a nastavte hodnoty podle vašeho prostředí.
    | Parametr           | Popis                                                                                                      | Příklad:                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -i,-identity soubor | Soubor RSA privátní klíč pro připojení kubernetes hlavnímu virtuálnímu počítači. Tento klíč musí začínat znakem `-----BEGIN RSA PRIVATE KEY-----` | C:\data\privatekey.pem                                                        |
    | -h,--hostitele          | Veřejnou IP adresu nebo název plně kvalifikované domény (FQDN) hlavního uzlu clusteru Kubernetes virtuálního počítače. Název virtuálního počítače začíná `k8s-master-`.                       | IP: 192.168.102.37<br><br>Plně kvalifikovaný název domény: k8s-12345.local.cloudapp.azurestack.external      |
    | -u, - uživatel          | Uživatelské jméno hlavního uzlu clusteru Kubernetes virtuálního počítače. Tento název se nastavit při konfiguraci položky marketplace.                                                                    | azureuser                                                                     |
    | -d – vmdhost       | Veřejná IP adresa nebo plně kvalifikovaný název domény DVM. Název virtuálního počítače začíná `vmd-`.                                                       | IP: 192.168.102.38<br><br>Server DNS: vmd-dnsk8-frog.local.cloudapp.azurestack.external |

   Když přidáte hodnoty parametrů, může vypadat podobně jako:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\secretsecret.pem" --user azureuser --vmdhost 192.168.102.37
     ```

    Úspěšné spuštění vytvoří v protokolech.

    ![Generované protokoly](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-generated-logs.png)


4. Načtěte protokoly ve složce vytvořené pomocí příkazu. Tento příkaz vytvoří novou složku a časová razítka ho.
    - KubernetesLogs*YYYY-MM-DD-XX-XX-XX-XXX*
        - Dvmlogs
        - Acsengine. kubernetes dvm.log

## <a name="next-steps"></a>Další postup

[Nasazení Kubernetes pro Azure Stack](azure-stack-solution-template-kubernetes-deploy.md).

[Přidání Kubernetes na webu Marketplace (pro operátory Azure stacku)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes v Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
