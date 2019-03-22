---
title: Řešení potíží s Kubernetes nasazení do služby Azure Stack | Dokumentace Microsoftu
description: Informace o řešení potíží s Kubernetes nasazení do služby Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.author: mabvrigg
ms.date: 03/20/2019
ms.reviewer: waltero
ms.lastreviewed: 03/20/2019
ms.openlocfilehash: 01a9405c98160149782ab2cf248f64818d631dde
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58293783"
---
# <a name="troubleshoot-your-kubernetes-deployment-to-azure-stack"></a>Řešení potíží s Kubernetes nasazení do služby Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

> [!Note]  
> Kubernetes ve službě Azure Stack je ve verzi preview. Azure Stack odpojené scénář není aktuálně podporován ve verzi preview.

Následující článek vypadá na odstraňování potíží u clusteru Kubernetes. Můžete zkontrolovat výstrahu pro nasazení a zkontrolovat stav nasazení podle prvků vyžadovaných pro nasazení. Může být potřeba shromažďovat protokoly nasazení ze služby Azure Stack nebo virtuálních počítačů s Linuxem, které hostují Kubernetes. Potřebujete také pracovat se správcem vaší služby Azure Stack a protokoly načíst z koncového bodu správy.

## <a name="overview-of-deployment"></a>Přehled nasazení

Předtím, než se pustíte do odstraňování clusteru, můžete chtít zkontrolovat proces nasazení clusteru Kubernetes se službou Azure Stack. Nasazení používá šablonu Azure Resource Manageru řešení k vytvoření virtuálních počítačů a nainstaluje modul ACS pro váš cluster.

### <a name="deployment-workflow"></a>Pracovní postup nasazení

Následující diagram znázorňuje obecný postup nasazení clusteru.

![Nasazení Kubernetes procesu](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>Kroky nasazení

1. Shromažďování vstupních parametrů z položky marketplace.

    Zadejte hodnoty, budete muset nastavit Kubernetes cluster, včetně:
    -  **Uživatelské jméno**: Uživatelské jméno pro virtuální počítače Linux, které jsou součástí clusteru Kubernetes a DVM.
    -  **Veřejný klíč SSH**: Klíč, který se používá pro autorizaci všechny počítače s Linuxem, které byly vytvořeny jako součást clusteru Kubernetes a DVM.
    -  **Instanční objekt služby**: Identifikátor, který se používá od poskytovatele cloudu Kubernetes Azure. ID klienta, který je identifikován jako ID aplikace při vytváření instančního objektu služby. 
    -  **Tajný kód klienta**: Tyto klíče při vytváření instančního objektu služby.

2. Vytvoření nasazení virtuálního počítače a rozšíření vlastních skriptů.
    -  Vytvoření nasazení virtuálního počítače s Linuxem pomocí image Linuxu marketplace **Ubuntu Server 16.04-LTS**.
    -  Stažení a spuštění rozšíření vlastních skriptů z webu marketplace. Skript je **vlastních skriptů pro Linux 2.0**.
    -  Spustí vlastní skript DVM. Skript provede následující úlohy:
        1. Získá koncový bod Galerie z koncového bodu metadat Azure Resource Manageru.
        2. Získá ID prostředku služby active directory z koncového bodu metadat Azure Resource Manageru.
        3. Načte modelu rozhraní API pro modul ACS.
        4. Nasadí do clusteru Kubernetes ACS Engine a uloží profil cloudové služby Azure Stack na `/etc/kubernetes/azurestackcloud.json`.
3. Vytvoření hlavních virtuálních počítačů.

4. Stažení a spuštění rozšíření vlastních skriptů.

5. Hlavní skript spusťte.

    Skript provede následující úlohy:
    - Nainstaluje etcd, Docker a Kubernetes prostředky, jako jsou kubelet. etcd je distribuovaná hodnota klíče úložiště, který poskytuje způsob, jak ukládat data napříč clusterem počítačů. Docker podporuje virtualizations holou úroveň operačního systému, známé jako kontejnery. Kubelet je agenta uzlu, na kterém běží na všech uzlech Kubernetes.
    - Nastaví **etcd** služby.
    - Nastaví **kubelet** služby.
    - Spustí kubelet. Tato úloha zahrnuje následující kroky:
        1. Spustí službu rozhraní API.
        2. Spustí službu řadiče.
        3. Spustí službu scheduler.
6. Vytvoření agenta pro virtuální počítače.

7. Stažení a spuštění rozšíření vlastních skriptů.

7. Spusťte skript agenta. Agent vlastní skript provede následující úlohy:
    - Nainstaluje **etcd**.
    - Nastaví **kubelet** služby.
    - Připojí ke clusteru Kubernetes.

## <a name="steps-for-troubleshooting"></a>Kroky pro řešení potíží

Shromažďování protokolů na virtuálních počítačích, které podporují vašemu clusteru Kubernetes. Můžete také zkontrolovat protokol nasazení. Možná budete muset obraťte se na správce služby Azure Stack k ověření verze služby Azure Stack, které potřebujete k používání a získat protokoly z Azure Stack, která se vztahují na vaše nasazení.

1. Zkontrolujte [stav nasazení](#review-deployment-status) a [tyto protokoly načíst](#get-logs-from-a-vm) z hlavního uzlu v clusteru Kubernetes.
2. Ujistěte se, že používáte nejnovější verzi služby Azure Stack. Pokud si nejste jistí, kterou verzi používáte, obraťte se na svého správce služby Azure Stack.
3.  Projděte si soubory vytvoření virtuálního počítače. Může mít vyskytly následující problémy:  
    - Veřejný klíč může být neplatný. Projděte si klíč, který jste vytvořili.  
    - Vytvoření virtuálního počítače může mít aktivuje vnitřní chybu nebo Chyba při vytváření aktivované. Několika faktory mohou způsobit chyby, včetně omezení kapacity pro vaše předplatné služby Azure Stack.
    - Ujistěte se, že plně kvalifikovaný název domény (FQDN) pro virtuální počítač začíná duplicitní předpona.
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
    | Type | Poskytovatel prostředků a typ prostředku. |
    | Status | Stav položky. |
    | Časové razítko | Časové razítko UTC času. |
    | Podrobnosti o operaci | Podrobnosti operace, jako je poskytovatel prostředků, která byla zahrnuta v operaci, koncový bod prostředku a název prostředku. |

    Každá položka má zelená nebo červená ikona stavu.

## <a name="review-deployment-logs"></a>Zkontrolujte protokoly nasazení

Pokud na portálu Azure Stack neposkytuje dostatek informací k řešení potíží nebo překonat selhání nasazení, dalším krokem je podívat do protokolů clusteru. Pokud chcete ručně načíst protokoly nasazení, je obvykle potřeba připojit k hlavní virtuálních počítačů clusteru. Jednodušší alternativním přístupem je stáhněte a spusťte následující příkaz [skriptu Bash](https://aka.ms/AzsK8sLogCollectorScript) poskytované týmem služby Azure Stack. Tento skript připojí ke clusteru virtuálních počítačů a DVM, shromažďuje relevantní systému a protokolů clusteru a stáhne je zpátky do pracovní stanice.

### <a name="prerequisites"></a>Požadavky

Budete potřebovat příkazovém řádku Bash na počítači, který používáte ke správě služby Azure Stack. Na počítači s Windows můžete získat Bash výzvy nainstalováním [Git pro Windows](https://git-scm.com/downloads). Po instalaci, vyhledejte _Git Bash_ v nabídce start.

### <a name="retrieving-the-logs"></a>Načítají se protokoly

Postupujte podle těchto kroků ke shromažďování a stažení protokolů clusteru:

1. Otevřete příkazový řádek Bash. Z počítače s Windows otevřete _Git Bash_ nebo spustit: `C:\Program Files\Git\git-bash.exe`.

2. Spuštěním následujících příkazů v vaše příkazovém řádku Bash Stáhněte skript kolektoru protokolů:

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    chmod 744 getkuberneteslogs.sh
    ```

3. Vyhledání informací o vyžaduje skript a spusťte ho:

    | Parametr           | Popis                                                                                                      | Příklad:                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -d, --vmd-host      | Veřejná IP adresa nebo plně kvalifikovaný název (FQDN) DVM. Název virtuálního počítače začíná `vmd-`. | IP adresa: 192.168.102.38<br>DNS: vmd-myk8s.local.cloudapp.azurestack.external |
    | -h, – Nápověda  | Tisk použití příkazu. | |
    | -i,-identity soubor | Soubor privátního klíče RSA předán položky marketplace při vytváření clusteru Kubernetes. Třeba do vzdáleného úložiště v uzlech Kubernetes. | C:\data\id_rsa.pem (Putty)<br>~/.ssh/id_rsa (SSH)
    | -m, --master-host   | Veřejnou IP adresu nebo název plně kvalifikované domény (FQDN) hlavního uzlu Kubernetes. Název virtuálního počítače začíná `k8s-master-`. | IP adresa: 192.168.102.37<br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u, --user          | Uživatelské jméno předané do položky marketplace při vytváření clusteru Kubernetes. Třeba do vzdáleného úložiště v uzlech Kubernetes | azureuser (výchozí hodnota) |


   Když přidáte všechny hodnoty parametrů, váš příkaz může vypadat přibližně takto:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\id_rsa.pem" --user azureuser --vmd-host 192.168.102.37
     ```

4. Za pár minut, bude výstup skriptu shromažďovat protokoly a adresář s názvem `KubernetesLogs_{{time-stamp}}`. Zde najdete adresář pro každý virtuální počítač, který patří do clusteru.

    Skript kolektoru protokolů také vyhledávání chyb v souborech protokolu a zahrnují potíží, když se stane najít známý problém. Ujistěte se, že používáte nejnovější verzi souboru, který chcete zvýšit pravděpodobnost hledání známých problémů.

> [!Note]  
> Podívejte se na tomto Githubu [úložiště](https://github.com/msazurestackworkloads/azurestack-gallery/tree/master/diagnosis) další podrobnosti o skriptu kolektoru protokolů.

## <a name="next-steps"></a>Další postup

[Nasazení Kubernetes pro Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)

[Přidání clusteru Kubernetes na webu Marketplace (pro operátory Azure stacku)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes v Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
