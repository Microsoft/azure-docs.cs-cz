---
title: Povolit Azure ARC v Kubernetes na zařízení GPU pro Azure Stack Edge pro | Microsoft Docs
description: Popisuje, jak povolit Azure ARC v existujícím clusteru Kubernetes na zařízení GPU Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/12/2020
ms.author: alkohli
ms.openlocfilehash: 342f6a2c4761104823694f2181b3ffa8726a441e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449424"
---
# <a name="enable-azure-arc-on-kubernetes-cluster-on-your-azure-stack-edge-pro-gpu-device"></a>Povolení Azure ARC v clusteru Kubernetes na zařízení GPU Azure Stack Edge pro

V tomto článku se dozvíte, jak povolit Azure ARC v existujícím clusteru Kubernetes na zařízení Azure Stack Edge pro. 

Tento postup je určený pro uživatele, kteří si zkontrolovali [úlohy Kubernetes na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-kubernetes-workload-management.md) a jsou obeznámeni s koncepty, [co je Azure ARC Enabled Kubernetes (Preview)?](../azure-arc/kubernetes/overview.md).


## <a name="prerequisites"></a>Předpoklady

Než budete moct povolit Azure ARC v clusteru Kubernetes, ujistěte se, že jste na svém zařízení Azure Stack Edge pro a klientovi, kterého budete používat pro přístup k zařízení, dokončili následující požadavky:

### <a name="for-device"></a>Zařízení

1. Máte přihlašovací údaje pro přihlášení k zařízení Azure Stack Edge pro s jedním uzlem.
    1. Zařízení se aktivuje. Viz [Aktivace zařízení](azure-stack-edge-gpu-deploy-activate.md).
    1. Zařízení má výpočetní roli nakonfigurovanou prostřednictvím Azure Portal a má cluster Kubernetes. Viz [Konfigurace výpočtů](azure-stack-edge-gpu-deploy-configure-compute.md).

1. K předplatnému máte přístup vlastník. Tento přístup budete potřebovat během kroku přiřazení role pro instanční objekt.
 

### <a name="for-client-accessing-the-device"></a>Pro klientský přístup k zařízení

1. Máte klientský systém Windows, který se bude používat pro přístup k zařízení Azure Stack Edge pro.
  
    - Na klientovi běží Windows PowerShell 5,0 nebo novější. Nejnovější verzi Windows PowerShellu si stáhnete tak, že přejdete na [nainstalovat Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows).
    
    - Můžete mít i jiné klienty s [podporovaným operačním systémem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . Tento článek popisuje postup při použití klienta systému Windows. 
    
1. Dokončili jste postup popsaný v tématu [přístup ke clusteru Kubernetes na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-create-kubernetes-cluster.md). Máte:
    
    - Nainstalováno `kubectl` na klienta.    
    - Ujistěte se, že `kubectl` verze klienta není ve verzi Kubernetes Master spuštěná na vašem zařízení Azure Stack Edge pro. 
      - Slouží `kubectl version` ke kontrole verze kubectl spuštěné v klientovi. Poznamenejte si plnou verzi.
      - V místním uživatelském rozhraní zařízení Azure Stack Edge pro, přejít na **Software Update** a poznamenejte si číslo verze Kubernetes serveru. 
    
        ![Ověřit číslo verze serveru Kubernetes](media/azure-stack-edge-gpu-connect-powershell-interface/verify-kubernetes-version-1.png)      
      
      - Ověřte, že tyto dvě verze jsou kompatibilní. 


## <a name="register-kubernetes-resource-providers"></a>Registrovat poskytovatele prostředků Kubernetes

Než povolíte Azure ARC v clusteru Kubernetes, budete muset povolit a zaregistrovat se k `Microsoft.Kubernetes` `Microsoft.KubernetesConfiguration` předplatnému. 

1. Pokud chcete poskytovatele prostředků povolit, v Azure Portal přejít do předplatného, které plánujete použít pro nasazení. Přejít na **poskytovatele prostředků**. 
1. V pravém podokně vyhledejte poskytovatele, které chcete přidat. V tomto příkladu `Microsoft.Kubernetes` a `Microsoft.KubernetesConfiguration` .

    ![Registrovat poskytovatele prostředků Kubernetes](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-1.png)

1. Vyberte poskytovatele prostředků a v horní části panelu příkazů vyberte **Registrovat**. Registrace trvá několik minut. 

    ![Registrovat poskytovatele prostředků Kubernetes 2](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-2.png)

1. Aktualizujte uživatelské rozhraní, dokud neuvidíte, že poskytovatel prostředků je registrovaný. Opakujte tento postup pro oba poskytovatele prostředků.
    
    ![Registrace poskytovatelů prostředků Kubernetes 3](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-4.png)

Poskytovatele prostředků můžete registrovat také prostřednictvím `az cli` . Další informace najdete v tématu [registrace dvou poskytovatelů pro Kubernetes s povoleným ARC Azure](../azure-arc/kubernetes/connect-cluster.md#register-the-two-providers-for-azure-arc-enabled-kubernetes) .

## <a name="create-service-principal-assign-role"></a>Vytvoření instančního objektu, přiřazení role

1. Ujistěte se, že máte `Subscription ID` a název skupiny prostředků, kterou jste použili pro nasazení prostředků služby Azure Stack Edge. ID předplatného získáte tak, že v Azure Portal přejdete na prostředek Azure Stack Edge. Přejděte na **přehled > Essentials**.

    ![Získat ID předplatného](media/azure-stack-edge-gpu-connect-powershell-interface/get-subscription-id-1.png)

    Název skupiny prostředků získáte tak, že přejdete na **vlastnosti**.

    ![Získat název skupiny prostředků](media/azure-stack-edge-gpu-connect-powershell-interface/get-resource-group-name-1.png)

1. Chcete-li vytvořit instanční objekt, použijte následující příkaz prostřednictvím `az cli` .

    `az ad sp create-for-rbac --skip assignment --name "<Informative name for service principal>"`  

    Informace o tom, jak se přihlásit ke službě `az cli` , můžete [Spustit Cloud Shell v Azure Portal](../cloud-shell/quickstart-powershell.md#start-cloud-shell)

    Zde je příklad. 
    
    ```azurecli
    PS /home/user> az ad sp create-for-rbac --skip-assignment --name "https://azure-arc-for-ase-k8s"
    {
      "appId": "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b",
      "displayName": "azure-arc-for-ase-k8s",
      "name": "https://azure-arc-for-ase-k8s",
      "password": "<password>",
      "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
    }
    PS /home/user>
    ```

1. Poznamenejte si `appID` , `name` , a, `password` `tenantID` jak ho použijete jako vstup v dalším příkazu.

1. Po vytvoření nového instančního objektu přiřaďte `Kubernetes Cluster - Azure Arc Onboarding` roli nově vytvořenému objektu zabezpečení. Toto je integrovaná role Azure (použijte ID role v příkazu) s omezenými oprávněními. Použijte následující příkaz:

    `az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee <appId-from-service-principal> --scope /subscriptions/<SubscriptionID>/resourceGroups/<Resource-group-name>`

    Zde je příklad.
    
    ```azurecli
    PS /home/user> az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b --scope /subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1
    {
      "canDelegate": null,
      "id": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1/providers/Microsoft.Authorization/roleAssignments/59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "name": "59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "principalId": "b045b3fe-8745-4097-9674-91cb0afaad91",
      "principalType": "ServicePrincipal",
      "resourceGroup": "myaserg1",
      "roleDefinitionId": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
      "scope": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1",
      "type": "Microsoft.Authorization/roleAssignments"
    }
    PS /home/user>
    ```
    Další informace o tom, jak vytvořit instanční objekt a provést přiřazení role, najdete v postupu v tématu [Vytvoření instančního objektu s podporou ARC Azure](../azure-arc/kubernetes/create-onboarding-service-principal.md).


## <a name="enable-arc-on-kubernetes-cluster"></a>Povolení služby Arc v clusteru Kubernetes

Pomocí těchto kroků nakonfigurujete cluster Kubernetes pro správu ARC Azure:

1. [Připojte se k rozhraní PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) vašeho zařízení.

1. Zadejte:

    `Set-HcsKubernetesAzureArcAgent -SubscriptionId "<Your Azure Subscription Id>" -ResourceGroupName "<Resource Group Name>" -ResourceName "<Azure Arc resource name (shouldn't exist already)>" -Location "<Region associated with resource group>" -TenantId "<Tenant Id of service principal>" -ClientId "<App id of service principal>" -ClientSecret "<Password of service principal>"`


    > [!NOTE]
    > - Pokud chcete na svém zařízení nasadit ARC Azure, ujistěte se, že používáte [podporovanou oblast pro Azure ARC](../azure-arc/kubernetes/overview.md#supported-regions). 
    > - Pomocí `az account list-locations` příkazu můžete zjistit přesný název umístění, který se má předat `Set-HcsKubernetesAzureArcAgent` rutině. Názvy umístění jsou obvykle formátované bez mezer.
    
    Tady je příklad:
   
    ```powershell
    [10.128.44.240]: PS>Set-HcsKubernetesAzureArcAgent -SubscriptionId "062c67a6-019b-40af-a775-c4dc1abe56ed" -ResourceGroupName "myaserg1" -ResourceName "myasetestresarc" -Location "westeurope" -TenantId "72f988bf-86f1-41af-91ab-2d7cd011db47" -ClientId "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b" -ClientSecret "<password>"
        [10.128.44.240]: PS>
    ```
    
    V Azure Portal by měl být vytvořen prostředek s názvem, který jste zadali v předchozím příkazu.

    ![Přejít na prostředek ARC Azure](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Pokud chcete ověřit, jestli je váš Azure ARC úspěšně povolený, spusťte následující příkaz z rozhraní PowerShellu:

    `kubectl get deployments -n azure-arc`

    Tento příkaz najde všechny aplikace, které jsou nasazené v `azure-arc` oboru názvů, který odpovídá ARC Azure.

    Tady je ukázkový výstup, který ukazuje agenty Azure ARC nasazené v clusteru Kubernetes v `azure-arc` oboru názvů. 


    ```powershell
    [10.128.44.240]: PS>kubectl get deployments -n azure-arc
    NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
    cluster-metadata-operator   1/1     1            1           45m
    clusteridentityoperator     1/1     1            1           45m
    config-agent                1/1     1            1           45m
    connect-agent               1/1     1            1           45m
    controller-manager          1/1     1            1           45m
    flux-logs-agent             1/1     1            1           45m
    metrics-agent               1/1     1            1           45m
    resource-sync-agent         1/1     1            1           45m
    [10.128.44.240]: PS>
    ```

    Můžete také získat seznam lusků spuštěných v clusteru Kubernetes v `azure-arc` oboru názvů. Pod je kontejner aplikace nebo proces spuštěný v clusteru Kubernetes. 

    Použijte následující příkaz:
    
    `kubectl get pods -n azure-arc`
    
    Zde je ukázkový výstup.
    
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n azure-arc
    NAME                                         READY   STATUS    RESTARTS   AGE
    cluster-metadata-operator-64cbdf95b4-s2q52   2/2     Running   0          16m
    clusteridentityoperator-6f6dbccf7-nwnxg      3/3     Running   0          16m
    config-agent-7df5bf497b-mjm8k                3/3     Running   0          16m
    connect-agent-5d4c766764-m7h46               1/1     Running   0          16m
    controller-manager-777555fb57-t7tdp          3/3     Running   0          16m
    flux-logs-agent-845476c899-zcmtj             2/2     Running   0          16m
    metrics-agent-84d6fc8f4d-g9jkm               2/2     Running   0          16m
    resource-sync-agent-8f88dbf96-zgxjj          3/3     Running   0          16m
    [10.128.44.240]: PS>
    ```


Jak ukazuje předchozí výstup, Kubernetes s povoleným Azure Arc se skládá z několika agentů (operátorů), které běží ve vašem clusteru nasazeném do `azure-arc` oboru názvů.

- `config-agent`: sleduje připojený cluster pro prostředky konfigurace správy zdrojového kódu použité v clusteru a aktualizuje stav dodržování předpisů.
- `controller-manager`: je operátor operátorů a orchestruje interakce mezi komponentami ARC Azure.
- `metrics-agent`: shromažďuje metriky jiných agentů ARC, aby se zajistilo, že tito agenti vykazují optimální výkon.
- `cluster-metadata-operator`: shromažďuje metadata clusteru – verze clusteru, počet uzlů a verzi agenta Azure ARC.
- `resource-sync-agent`: synchronizuje výše uvedená metadata clusteru do Azure.
- `clusteridentityoperator`: Kubernetes s podporou Azure ARC aktuálně podporuje identitu přiřazenou systémem. clusteridentityoperator udržuje certifikát MSI (Managed Service identity), který používají jiní agenti pro komunikaci s Azure.
- `flux-logs-agent`: shromažďuje protokoly z operátorů toku nasazených jako součást konfigurace správy zdrojového kódu.
- `connect-agent`: přednášky k prostředku Azure ARC.

### <a name="remove-arc-from-the-kubernetes-cluster"></a>Odebrat oblouk z clusteru Kubernetes

Pro odebrání správy ARC Azure použijte následující postup:

1. 1. [Připojte se k rozhraní PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) vašeho zařízení.
2. Zadejte:

    `Remove-HcsKubernetesAzureArcAgent` 


> [!NOTE]
> Ve výchozím nastavení platí, že při `yamls` odstranění prostředku z úložiště Git se odpovídající prostředky neodstraní z clusteru Kubernetes. Musíte nastavit `--sync-garbage-collection`  v Arc OperatorParams, aby bylo možné odstranit prostředky při odstranění z úložiště Git. Další informace najdete v tématu [Odstranění konfigurace](../azure-arc/kubernetes/use-gitops-connected-cluster.md#additional-parameters) .

## <a name="next-steps"></a>Další kroky

Informace o tom, jak spustit nasazení ARC v Azure, najdete v tématu [nasazení aplikace s bezstavovou sadou php pomocí Redis přes GitOps na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md) .