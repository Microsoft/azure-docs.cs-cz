---
title: Nasazení řadiče dat ARC Azure na zařízení GPU Azure Stack Edge pro | Microsoft Docs
description: Popisuje, jak nasadit řadič dat ARC Azure a Data Services Azure na zařízení GPU Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 53058d27e94c9fdf18d726369f6a1b75a9f34db9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567538"
---
# <a name="deploy-azure-data-services-on-your-azure-stack-edge-pro-gpu-device"></a>Nasazení Azure Data Services na zařízení GPU pro Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Tento článek popisuje proces vytvoření řadiče dat ARC Azure a následné nasazení Azure Data Services na zařízení GPU Azure Stack Edge pro. 

Řadič dat ARC Azure je místní rovina ovládacího prvku, která umožňuje Data Services Azure v prostředích spravovaných zákazníky. Jakmile vytvoříte řadič dat ARC Azure v clusteru Kubernetes, který běží na vašem zařízení Azure Stack Edge pro, můžete na tento řadič dat nasadit Data Services Azure, jako je SQL Managed instance (Preview).

Postup vytvoření kontroleru dat a následná implementace spravované instance SQL zahrnuje použití PowerShellu a `kubectl` nativního nástroje, který poskytuje přístup k Kubernetes clusteru na zařízení pomocí příkazového řádku.


## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Máte přístup k zařízení Azure Stack Edge pro a aktivovali jste zařízení, jak je popsáno v tématu [aktivace Azure Stack Edge pro](azure-stack-edge-gpu-deploy-activate.md).

1. Na zařízení jste povolili výpočetní roli. V zařízení se vytvořil i cluster Kubernetes, když jste na zařízení nakonfigurovali výpočetní výkon podle pokynů v tématu [Konfigurace výpočetních prostředků na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Máte koncový bod rozhraní Kubernetes API ze stránky **zařízení** vašeho místního webového uživatelského rozhraní. Další informace najdete v pokynech v tématu [získání koncového bodu rozhraní API Kubernetes](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints).

1. Máte přístup ke klientovi, který se připojí k vašemu zařízení. 
    1. V tomto článku se k přístupu k zařízení používá klientský systém Windows se spuštěným PowerShellem 5,0 nebo novějším. Můžete použít libovolného jiného klienta s [podporovaným operačním systémem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device). 
    1. Nainstalujte `kubectl` na klienta. Pro verzi klienta:
        1. Identifikujte verzi serveru Kubernetes nainstalovanou na zařízení. V místním uživatelském rozhraní zařízení, přejít na stránku **aktualizace softwaru** . Všimněte si **verze Kubernetes serveru** na této stránce.
        1. Stáhněte si klienta, který se od hlavní verze neliší o více než jednu podverzi. Verze klienta, ale může vést k hlavnímu serveru až k jedné dílčí verzi. Například hlavní server v 1.3 by měl pracovat s uzly v 1.1, v 1.2 a v 1.3 a musí fungovat s klienty v 1.2, v 1.3 a v 1.4. Další informace o verzi klienta Kubernetes najdete v tématu [zásady podpory pro verzi Kubernetes a pro zkosení verzí](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew).
    
1. Volitelně můžete [nainstalovat klientské nástroje pro nasazení a správu datových služeb s podporou ARC Azure](../azure-arc/data/install-client-tools.md). Tyto nástroje se nevyžadují, ale doporučuje se.  
1. Ujistěte se, že máte na svém zařízení dostatek dostupných prostředků ke zřízení řadiče dat a jedné spravované instance SQL. Pro řadič dat a jednu spravovanou instanci SQL budete potřebovat minimálně 16 GB paměti RAM a 4 PROCESORových jader. Podrobné pokyny najdete v požadavcích na [minimální požadavky pro nasazení datových služeb s podporou ARC Azure](../azure-arc/data/sizing-guidance.md#minimum-deployment-requirements).


## <a name="configure-kubernetes-external-service-ips"></a>Konfigurace IP adres externích služeb Kubernetes

1. Přejít do místního webového uživatelského rozhraní zařízení a pak přejít na **výpočetní** výkon.
1. Vyberte síť, která je povolená pro výpočetní výkon. 

    ![Stránka COMPUTE v místním uživatelském rozhraní 2](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-1.png)

1. Ujistěte se, že zadáváte tři další IP adresy externích služeb Kubernetes (navíc k IP adresám, které jste už nakonfigurovali pro jiné externí služby nebo kontejnery). Řadič dat bude používat dvě IP adresy služby a třetí IP adresa se používá při vytváření spravované instance SQL. Pro každou další datovou službu, kterou budete nasazovat, budete potřebovat jednu IP adresu. 

    ![Stránka COMPUTE v místním uživatelském rozhraní 3](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-2.png)

1. Použijte nastavení a tyto nové IP adresy se okamžitě projeví v již existujícím clusteru Kubernetes. 


## <a name="deploy-azure-arc-data-controller"></a>Nasadit řadič dat ARC Azure

Než nasadíte řadič dat, budete muset vytvořit obor názvů.

### <a name="create-namespace"></a>Vytvoření oboru názvů 

Vytvořte nový vyhrazený obor názvů, do kterého budete nasazovat řadič dat. Také vytvoříte uživatele a potom udělíte uživateli přístup k vytvořenému oboru názvů. 

> [!NOTE]
> Pro obor názvů i uživatelská jména platí [konvence pojmenování subdomén DNS](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) .

1. [Připojte se k rozhraní PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Vytvořte obor názvů. Zadejte:

    `New-HcsKubernetesNamespace -Namespace <Name of namespace>`

1. Vytvořte uživatele. Zadejte: 

    `New-HcsKubernetesUser -UserName <User name>`

1. V prostém textu se zobrazí konfigurační soubor. Zkopírujte tento soubor a uložte ho jako *konfigurační* soubor. 

    > [!IMPORTANT]
    > Neukládejte konfigurační soubor jako soubor *. txt* a uložte ho bez přípony souboru.

1. Konfigurační soubor by měl být živý ve `.kube` složce vašeho uživatelského profilu na místním počítači. Zkopírujte soubor do této složky v profilu uživatele.

    ![Umístění konfiguračního souboru na klientovi](media/azure-stack-edge-gpu-create-kubernetes-cluster/location-config-file.png)
1. Udělte uživateli přístup k oboru názvů, který jste vytvořili. Zadejte: 

    `Grant-HcsKubernetesNamespaceAccess -Namespace <Name of namespace> -UserName <User name>`

    Zde je ukázkový výstup předchozích příkazů. V tomto příkladu vytvoříme `myadstest` obor názvů `myadsuser` a uživatele a udělíme mu přístup k oboru názvů.
    
    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesNamespace -Namespace myadstest
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName myadsuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBD=======//snipped//=======VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: myadsuser
      name: myadsuser@kubernetes
    current-context: myadsuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: myadsuser
      user:
        client-certificate-data: LS0tLS1CRUdJTiBDRV=========//snipped//=====EE9PQotLS0kFURSBLRVktLS0tLQo=
    
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myadstest -UserName myadsuser
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS>
    ```
1. Přidejte položku DNS do souboru Hosts ve vašem systému. 

    1. Spusťte Poznámkový blok jako správce a otevřete `hosts` soubor umístěný na adrese `C:\windows\system32\drivers\etc\hosts` . 
    2. Pomocí informací, které jste uložili ze stránky **zařízení** v místním uživatelském rozhraní (předpoklad), vytvořte záznam v souboru Hosts. 

        Například zkopírováním tohoto koncového bodu `https://compute.myasegpudev.microsoftdatabox.com/[10.100.10.10]` vytvoříte následující položku s IP adresou zařízení a doménou DNS: 

        `10.100.10.10 compute.myasegpudev.microsoftdatabox.com`

1. Pokud chcete ověřit, že se můžete připojit k Kubernetes luskům, spusťte příkazový řádek nebo relaci PowerShellu. Zadejte:
    
    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
    No resources found.
    PS C:\WINDOWS\system32>
    ```
Nyní můžete v oboru názvů nasadit své aplikace datového kontroleru a datových služeb a pak zobrazit aplikace a jejich protokoly.

### <a name="create-data-controller"></a>Vytvoření kontroleru dat

Řadič dat je kolekce lusků, které se nasazují do vašeho clusteru Kubernetes, aby poskytovaly rozhraní API, službu kontroleru, zaváděcí nástroj a databáze a řídicí panely pro monitorování. Pomocí těchto kroků vytvořte řadič dat v clusteru Kubernetes, který existuje na vašem zařízení Azure Stack Edge v oboru názvů, který jste vytvořili dříve.   

1. Shromážděte následující informace, které budete potřebovat k vytvoření kontroleru dat:

    
    |Sloupec1  |Sloupec2  |
    |---------|---------|
    |Název kontroleru dat     |Popisný název vašeho kontroleru dat. Například, `arctestdatacontroller`.         |
    |Uživatelské jméno kontroleru dat     |Jakékoli uživatelské jméno pro uživatele správce kontroleru dat. Uživatelské jméno a heslo řadiče dat slouží k ověřování v rozhraní API řadiče dat za účelem provádění funkcí správy.          |
    |Heslo kontroleru dat     |Heslo pro uživatele správce kontroleru dat. Vyberte zabezpečené heslo a sdílejte ho jenom s uživateli, kteří potřebují mít oprávnění správce clusteru.         |
    |Název vašeho oboru názvů Kubernetes     |Název oboru názvů Kubernetes, ve kterém chcete vytvořit řadič dat.         |
    |ID předplatného Azure     |Identifikátor GUID předplatného Azure, pro který chcete vytvořit prostředek řadiče dat v Azure.         |
    |Název skupiny prostředků Azure     |Název skupiny prostředků, ve které chcete vytvořit prostředek řadiče dat v Azure.         |
    |Umístění Azure     |Umístění Azure, kde se metadata prostředku řadiče dat uloží do Azure. Seznam oblastí, které jsou k dispozici, najdete v tématu globální infrastruktura/produkty Azure podle oblasti.|


1. Připojte se k rozhraní PowerShellu. Chcete-li vytvořit řadič dat, zadejte: 

    ```powershell
    Set-HcsKubernetesAzureArcDataController -SubscriptionId <Subscription ID> -ResourceGroupName <Resource group name> -Location <Location without spaces> -UserName <User you created> -Password <Password to authenticate to Data Controller> -DataControllerName <Data Controller Name> -Namespace <Namespace you created>    
    ```
    Zde je ukázkový výstup předchozích příkazů.

    ```powershell
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS> 
    ```
    
    Dokončení nasazení může trvat přibližně 5 minut.

    > [!NOTE]
    > Řadič dat vytvořený v clusteru Kubernetes na vašem zařízení Azure Stack Edge pro funguje v aktuálním vydání jenom v odpojeném režimu.

### <a name="monitor-data-creation-status"></a>Stav vytváření dat monitorování

1. Otevřete jiné okno prostředí PowerShell.
1. Pomocí následujícího `kubectl` příkazu můžete monitorovat stav vytváření řadiče dat. 

    ```powershell
    kubectl get datacontroller/<Data controller name> --namespace <Name of your namespace>
    ```
    Při vytvoření kontroleru by měl být stav `Ready` .
    Zde je ukázkový výstup předchozího příkazu:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get datacontroller/arctestcontroller --namespace myadstest
    NAME                STATE
    arctestcontroller   Ready
    PS C:\WINDOWS\system32>
    ```
1. K identifikaci IP adres přiřazených k externím službám běžícím na řadiči dat použijte `kubectl get svc -n <namespace>` příkaz. Zde je ukázkový výstup:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get svc -n myadstest
    NAME                      TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                       AGE
    controldb-svc             ClusterIP      172.28.157.130   <none>        1433/TCP,8311/TCP,8411/TCP                    3d21h
    controller-svc            ClusterIP      172.28.123.251   <none>        443/TCP,8311/TCP,8301/TCP,8411/TCP,8401/TCP   3d21h
    controller-svc-external   LoadBalancer   172.28.154.30    10.57.48.63   30080:31090/TCP                               3d21h
    logsdb-svc                ClusterIP      172.28.52.196    <none>        9200/TCP,8300/TCP,8400/TCP                    3d20h
    logsui-svc                ClusterIP      172.28.85.97     <none>        5601/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdb-svc             ClusterIP      172.28.255.103   <none>        8086/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdc-svc             ClusterIP      172.28.208.191   <none>        8300/TCP,8400/TCP                             3d20h
    metricsui-svc             ClusterIP      172.28.158.163   <none>        3000/TCP,8300/TCP,8400/TCP                    3d20h
    mgmtproxy-svc             ClusterIP      172.28.228.229   <none>        443/TCP,8300/TCP,8311/TCP,8400/TCP,8411/TCP   3d20h
    mgmtproxy-svc-external    LoadBalancer   172.28.166.214   10.57.48.64   30777:30621/TCP                               3d20h
    sqlex-svc                 ClusterIP      None             <none>        1433/TCP                                      3d20h
    PS C:\WINDOWS\system32>
    ```

## <a name="deploy-sql-managed-instance"></a>Nasadit spravovanou instanci SQL

Po úspěšném vytvoření řadiče dat můžete použít šablonu k nasazení spravované instance SQL na řadiči dat.

### <a name="deployment-template"></a>Šablona nasazení

Použijte následující šablonu nasazení k nasazení spravované instance SQL na řadiči dat na svém zařízení.

```yml
apiVersion: v1
data:
    password: UGFzc3dvcmQx
    username: bXlhZHN1c2Vy
kind: Secret
metadata:
    name: sqlex-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
    name: sqlex
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
    service:
    type: LoadBalancer
    storage:
    backups:
        className: ase-node-local
        size: 5Gi
    data:
        className: ase-node-local
        size: 5Gi
    datalogs:
        className: ase-node-local
        size: 5Gi
    logs:
        className: ase-node-local
        size: 1Gi
```


#### <a name="metadata-name"></a>Název metadat
    
Název metadat je název spravované instance SQL. Přidružená pod v předchozím poli `deployment.yaml` bude název jako `sqlex-n` ( `n` počet lusků přidružených k aplikaci). 
    
#### <a name="password-and-username-data"></a>Heslo a data uživatelského jména

Uživatelské jméno a heslo řadiče dat slouží k ověřování v rozhraní API řadiče dat za účelem provádění funkcí správy. Tajný klíč Kubernetes pro uživatelské jméno a heslo kontroleru dat v šabloně nasazení jsou řetězce kódované v kódování Base64. 

Můžete použít online nástroj k kódování Base64 vaše požadované uživatelské jméno a heslo nebo můžete v závislosti na vaší platformě použít integrované nástroje rozhraní příkazového řádku. Při použití online nástroje pro kódování Base64 zadejte v nástroji řetězce uživatelského jména a hesla (které jste zadali při vytváření kontroleru dat) a nástroj vygeneruje odpovídající řetězce kódované v kódování Base64.
    
#### <a name="service-type"></a>Typ služby

Typ služby by měl být nastaven na hodnotu `LoadBalancer` .
    
#### <a name="storage-class-name"></a>Název třídy úložiště

Můžete určit třídu úložiště na zařízení Azure Stack Edge, které bude nasazení používat pro data, zálohy, protokoly a protokoly dat. Pomocí  `kubectl get storageclass` příkazu můžete získat třídu úložiště nasazenou na vašem zařízení.

```powershell
PS C:\WINDOWS\system32> kubectl get storageclass
NAME             PROVISIONER      RECLAIMPOLICY  VOLUMEBINDINGMODE     ALLOWVOLUMEEXPANSION   AGE
ase-node-local   rancher.io/local-path   Delete  WaitForFirstConsumer  false                  5d23h
PS C:\WINDOWS\system32>
```
V předchozím ukázkovém výstupu `ase-node-local` by se měla v šabloně zadat třída úložiště v zařízení.
 
#### <a name="spec"></a>Úplný

Pokud chcete na zařízení Azure Stack Edge vytvořit spravovanou instanci SQL, můžete zadat požadavky na paměť a procesor v části specifikace v tématu `deployment.yaml` . Každá spravovaná instance SQL musí vyžadovat minimálně 2 GB paměti a 1 jádro procesoru, jak je znázorněno v následujícím příkladu. 

```yml
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
```  

Podrobné pokyny k přizpůsobení velikosti řadiče dat a 1 spravované instance SQL najdete v [podrobnostech o velikosti SQL Managed instance](../azure-arc/data/sizing-guidance.md#sql-managed-instance-sizing-details).

### <a name="run-deployment-template"></a>Spustit šablonu nasazení

Spusťte `deployment.yaml` příkaz pomocí následujícího příkazu:

```powershell
kubectl create -n <Name of namespace that you created> -f <Path to the deployment yaml> 
```

Tady je ukázkový výstup následujícího příkazu:

```powershell
PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
No resources found.
PS C:\WINDOWS\system32> 
PS C:\WINDOWS\system32> kubectl create -n myadstest -f C:\azure-arc-data-services\sqlex.yaml  secret/sqlex-login-secret created
sqlmanagedinstance.sql.arcdata.microsoft.com/sqlex created
PS C:\WINDOWS\system32> kubectl get pods --namespace myadstest
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-mv2cd   1/1     Running   0          83m
control-w9s9l        2/2     Running   0          78m
controldb-0          2/2     Running   0          78m
controlwd-4bmc5      1/1     Running   0          64m
logsdb-0             1/1     Running   0          64m
logsui-wpmw2         1/1     Running   0          64m
metricsdb-0          1/1     Running   0          64m
metricsdc-fb5r5      1/1     Running   0          64m
metricsui-56qzs      1/1     Running   0          64m
mgmtproxy-2ckl7      2/2     Running   0          64m
sqlex-0              3/3     Running   0          13m
PS C:\WINDOWS\system32>
```

`sqlex-0`Pod v ukázce výstupu označuje stav spravované instance SQL.

## <a name="remove-data-controller"></a>Odebrat řadič dat

Chcete-li odebrat řadič dat, odstraňte vyhrazený obor názvů, ve kterém jste jej nasadili.


```powershell
kubectl delete ns <Name of your namespace>
```


## <a name="next-steps"></a>Další kroky

- [Nasaďte bezstavovou aplikaci na Azure Stack Edge pro](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).