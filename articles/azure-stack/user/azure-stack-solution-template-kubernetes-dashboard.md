---
title: Přístup k řídicímu panelu Kubernetes v Azure stacku | Dokumentace Microsoftu
description: Zjistěte, jak získat přístup k řídicímu panelu Kubernetes ve službě Azure Stack
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
ms.date: 03/07/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 02/27/2019
ms.openlocfilehash: 76af6d6585d52eee99548f69c92bd414068fa28d
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259206"
---
# <a name="access-the-kubernetes-dashboard-in-azure-stack"></a>Přístup k řídicímu panelu Kubernetes v Azure stacku 

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit* 
> [!Note]   
> Kubernetes ve službě Azure Stack je ve verzi preview. Azure Stack odpojené scénář není aktuálně podporován ve verzi preview. 

Kubernetes zahrnuje webové řídicí panel, který můžete použít pro operací základní správy. Tento řídicí panel umožňuje zobrazit základní stav a metriky pro vaše aplikace, vytvoření a nasazení služeb a upravit stávající aplikace. V tomto článku se dozvíte, jak nastavit řídicí panel Kubernetes ve službě Azure Stack.

## <a name="prerequisites-for-kubernetes-dashboard"></a>Požadavky na řídicí panel Kubernetes

* Cluster Azure Kubernetes zásobníku

    Je potřeba jste nasadili Kubernetes cluster pro Azure Stack. Další informace najdete v tématu [nasazení Kubernetes](azure-stack-solution-template-kubernetes-deploy.md).

* Klient SSH

    Budete potřebovat klienta SSH k zabezpečení připojení k vaší hlavní uzel v clusteru. Pokud používáte Windows, můžete použít [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm). Budete potřebovat privátní klíč, který jste použili při nasazení clusteru Kubernetes.

* FTP (PSCP)

    Možná bude nutné také klienta FTP, který podporuje SSH a SSH File Transfer Protocol přenos certifikáty z hlavního uzlu do Azure stacku správu počítače. Můžete použít [Filezilly](https://filezilla-project.org/download.php?type=client). Budete potřebovat privátní klíč, který jste použili při nasazení clusteru Kubernetes.

## <a name="overview-of-steps-to-enable-dashboard"></a>Přehled kroky, aby řídicí panel

1.  Exportujte certifikátů Kubernetes z hlavního uzlu v clusteru. 
2.  Naimportujte certifikáty do Azure stacku správu počítači.
2.  Otevřete řídicí panel Kubernetes web. 

## <a name="export-certificate-from-the-master"></a>Exportujte certifikát z hlavního serveru 

Adresu URL řídicího panelu můžete načíst z hlavního uzlu v clusteru.

1. Získejte veřejnou IP adresu a uživatelské jméno pro hlavní clusteru z řídicího panelu služby Azure Stack. Pokud chcete získat tyto informace:

    - Přihlaste se k [portálu Azure Stack](https://portal.local.azurestack.external/)
    - Vyberte **všechny služby** > **všechny prostředky**. Najdete hlavnímu serveru ve vaší skupině prostředků clusteru. Název hlavní `k8s-master-<sequence-of-numbers>`. 

2. Hlavní uzel otevřete na portálu. Kopírovat **veřejnou IP adresu** adresu. Klikněte na tlačítko **připojit** získat uživatelské jméno **přihlásit se pomocí místního účtu virtuálního počítače** pole. Toto je stejné uživatelské jméno, které jste nastavili při vytváření clusteru. Použijte veřejnou IP adresu než privátní IP adresy uvedené v okně připojení.

3.  Otevřete klienta SSH pro připojení k hlavnímu serveru. Pokud pracujete ve Windows, můžete použít [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm) k vytvoření připojení. Budete používat veřejnou IP adresu pro hlavní uzel uživatelské jméno a přidejte privátní klíč, který jste použili při vytváření clusteru.

4.  Když se připojí terminálu zadejte `kubectl` otevřete klienta příkazového řádku Kubernetes.

5. Spusťte následující příkaz:

    ```Bash   
    kubectl cluster-info 
    ``` 
    Vyhledejte adresu URL řídicího panelu. Například: `https://k8-1258.local.cloudapp.azurestack.external/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`

6.  Extrahování certifikátu podepsaného svým držitelem a převést do formátu PFX. Spusťte následující příkaz:

    ```Bash  
    sudo su 
    openssl pkcs12 -export -out /etc/kubernetes/certs/client.pfx -inkey /etc/kubernetes/certs/client.key  -in /etc/kubernetes/certs/client.crt -certfile /etc/kubernetes/certs/ca.crt 
    ```

7.  Získat seznam tajných kódů v **kube-system** oboru názvů. Spusťte následující příkaz:

    ```Bash  
    kubectl -n kube-system get secrets
    ```

    Poznamenejte si kubernetes – řídicí panel-token -\<XXXXX > hodnota. 

8.  Získat token a uložte ho. Aktualizace `kubernetes-dashboard-token-<####>` s hodnota tajného klíče z předchozího kroku.

    ```Bash  
    kubectl -n kube-system describe secret kubernetes-dashboard-token-<####>| awk '$1=="token:"{print $2}' 
    ```

## <a name="import-the-certificate"></a>Importovat certifikát

1. Otevřete Filezilly a připojte se k hlavnímu uzlu. Budete potřebovat:

    - veřejnou IP adresu hlavního uzlu
    - uživatelské jméno
    - privátní tajný klíč
    - Použití **SFTP - SSH File Transfer Protocol**

2. Kopírování `/etc/kubernetes/certs/client.pfx` a `/etc/kubernetes/certs/ca.crt` do Azure stacku správu počítače.

3. Poznamenejte si umístění souborů. Aktualizujte skript pomocí umístění a pak otevřete prostředí PowerShell s řádku se zvýšenými oprávněními. Spusťte skript aktualizace:  

    ```PowerShell   
    Import-Certificate -Filepath "ca.crt" -CertStoreLocation cert:\LocalMachine\Root 
    $pfxpwd = Get-Credential -UserName 'Enter password below' -Message 'Enter password below' 
    Import-PfxCertificate -Filepath "client.pfx" -CertStoreLocation cert:\CurrentUser\My -Password $pfxpwd.Password 
    ``` 

## <a name="open-the-kubernetes-dashboard"></a>Otevřete řídicí panel Kubernetes 

1. Zakáže blokování automaticky otevíraných oken ve webovém prohlížeči.

2. Bod prohlížeče na adresu URL jste si poznamenali, když jste spustili příkaz `kubectl cluster-info`. Například: https:\//azurestackdomainnamefork8sdashboard/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard: / proxy serveru 
3. Vyberte klientský certifikát.
4. Zadejte token. 
5. Znovu připojit k příkazovému řádku bash na hlavní uzel a udělit oprávnění k `kubernetes-dashboard`. Spusťte následující příkaz:

    ```Bash  
    kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard 
    ``` 

    Skript vypíše `kubernetes-dashboard` cloudu oprávnění správce. Další informace najdete v tématu [clustery s podporou pro RBAC](https://docs.microsoft.com/azure/aks/kubernetes-dashboard).

Mohou pomocí řídicího panelu. Další informace na řídicí panel Kubernetes najdete v tématu [řídicí panel Kubernetes webového uživatelského rozhraní](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) 

![Řídicí panel Kubernetes pro Azure Stack](media/azure-stack-solution-template-kubernetes-dashboard/azure-stack-kub-dashboard.png)

## <a name="next-steps"></a>Další postup 

[Nasazení Kubernetes pro Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)  

[Přidání clusteru Kubernetes na webu Marketplace (pro operátory Azure stacku)](../azure-stack-solution-template-kubernetes-cluster-add.md)  

[Kubernetes v Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)  
