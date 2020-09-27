---
title: Vytvoření kontroleru dat pomocí nástrojů pro Kubernetes
description: Vytvoření kontroleru dat pomocí nástrojů pro Kubernetes
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 087b021f088e344926a44f7e009f273d265dd82b
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397619"
---
# <a name="create-azure-arc-data-controller-using-kubernetes-tools"></a>Vytvoření řadiče dat ARC Azure pomocí nástrojů pro Kubernetes

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Požadavky

Přehled informací najdete v tématu [vytvoření řadiče dat ARC Azure](create-data-controller.md) .

Pokud chcete vytvořit řadič dat ARC Azure pomocí nástrojů Kubernetes, budete muset mít nainstalované nástroje Kubernetes.  Příklady v tomto článku budou použity `kubectl` , ale podobné přístupy lze použít s jinými Kubernetes nástroji, jako je řídicí panel Kubernetes, `oc` nebo `helm` Pokud s těmito nástroji a Kubernetes YAML/JSON znáte.

[Instalace nástroje kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

> [!NOTE]
> Některé kroky pro vytvoření kontroleru dat ARC Azure, které jsou uvedené níže, vyžadují oprávnění správce clusteru Kubernetes.  Pokud nejste správcem clusteru Kubernetes, budete muset mít správce clusteru Kubernetes provést tyto kroky vaším jménem.

#### <a name="cleanup-from-past-installations"></a>Vyčištění z minulých instalací

Pokud jste v minulosti nainstalovali řadič dat ARC Azure do stejného clusteru a odstranili jste řadič dat ARC Azure pomocí `azdata arc dc delete` příkazu, můžou existovat objekty na úrovni clusteru, které by se ještě musely odstranit. Spuštěním následujících příkazů odstraňte objekty na úrovni clusteru datového kontroleru Azure ARC:

```
# Cleanup azure arc data service artifacts
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete postgresql-11s.arcdata.microsoft.com 
kubectl delete postgresql-12s.arcdata.microsoft.com
kubectl delete clusterroles azure-arc-data:cr-arc-metricsdc-reader
kubectl delete clusterrolebindings azure-arc-data:crb-arc-metricsdc-reader
```

## <a name="overview"></a>Přehled

Vytvoření kontroleru dat ARC Azure má následující kroky vysoké úrovně:
1. Vytvořte vlastní definice prostředků pro řadič dat ARC, spravovanou instanci SQL Azure a PostgreSQL s měřítkem. **[Vyžaduje oprávnění správce clusteru Kubernetes]**
2. Vytvořte obor názvů, ve kterém bude vytvořen řadič dat. **[Vyžaduje oprávnění správce clusteru Kubernetes]**
3. Vytvořte službu zaváděcího nástroje, včetně sady replik, účtu služby, role a vazby role.
4. Vytvořte tajný klíč pro uživatelské jméno a heslo správce kontroleru dat.
5. Vytvořte řadič dat.
   
## <a name="create-the-custom-resource-definitions"></a>Vytvoření vlastních definic prostředků

Spuštěním následujícího příkazu vytvořte vlastní definice prostředků.  **[Vyžaduje oprávnění správce clusteru Kubernetes]**

```console
kubectl create -f https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/custom-resource-definitions.yaml
```

## <a name="create-a-namespace-in-which-the-data-controller-will-be-created"></a>Vytvoření oboru názvů, ve kterém bude vytvořen řadič dat

Spusťte příkaz podobný následujícímu a vytvořte nový vyhrazený obor názvů, ve kterém bude vytvořen řadič dat.  V tomto příkladu a zbytek příkladů v tomto článku bude použit název oboru názvů `arc` . Pokud se rozhodnete použít jiný název, použijte stejný název v celém.

```console
kubectl create namespace arc
```

Pokud tento obor názvů nebudou používat jiní uživatelé, kteří nejsou správci clusterů, doporučujeme vytvořit roli správce oboru názvů a tuto roli těmto uživatelům udělit prostřednictvím vazby role.  Správce oboru názvů by měl mít úplná oprávnění k oboru názvů.  Další informace budou k dispozici později, jak poskytnout uživatelům podrobnější přístup založený na rolích.

## <a name="create-the-bootstrapper-service"></a>Vytvoření služby zaváděcího nástroje

Služba zaváděcího nástroje zpracovává příchozí požadavky na vytváření, úpravy a odstraňování vlastních prostředků, jako je například řadič dat, spravovaná instance SQL nebo PostgreSQL serverová skupina s vlastním škálováním.

Spuštěním následujícího příkazu vytvořte službu zaváděcího nástroje, účet služby pro službu zaváděcího nástroje a vazbu role a role pro účet služby zaváděcího nástroje.

```console
kubectl create --namespace arc -f https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/bootstrapper.yaml
```

Pomocí následujícího příkazu ověřte, že je spuštěný zaváděcí nástroj pod.  Je možné, že ho budete muset několikrát spustit, dokud se stav nezmění na `Running` .

```console
kubectl get pod --namespace arc
```

Soubor šablony zaváděcího nástroje. yaml používá výchozí hodnotu pro stažení bitové kopie kontejneru zaváděcího nástroje z Microsoft Container Registry (MCR).  Pokud vaše prostředí nemá přístup přímo k Container Registry Microsoft, můžete postupovat takto:
- Postupujte podle pokynů pro stažení [imagí kontejneru z Microsoft Container registry a nahrajte je do soukromého registru kontejnerů](offline-deployment.md).
- [Vytvořte tajný klíč pro vyžádání image](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-lin) pro váš privátní registr kontejneru.
- Přidejte do kontejneru zaváděcího nástroje bitovou kopii, která se má načíst. Viz následující příklad.
- Změňte umístění bitové kopie pro Image zaváděcího nástroje. Viz následující příklad.

V následujícím příkladu se předpokládá, že jste vytvořili název tajného kódu pro vyžádání image, `regcred` jak je uvedeno v dokumentaci k Kubernetes.

```yaml
#just showing only the relevant part of the bootstrapper.yaml template file here
containers:
      - env:
        - name: ACCEPT_EULA
          value: "Y"
        #image: mcr.microsoft.com/arcdata/arc-bootstrapper:public-preview-sep-2020 <-- template value to change
        image: <your registry DNS name or IP address>/<your repo>/arc-bootstrapper:<your tag>
        imagePullPolicy: IfNotPresent
        name: bootstrapper
        resources: {}
        securityContext:
          runAsUser: 21006
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      imagePullSecrets:
      - name: regcred
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      serviceAccount: sa-mssql-controller
      serviceAccountName: sa-mssql-controller
      terminationGracePeriodSeconds: 30

```

## <a name="create-a-secret-for-the-data-controller-administrator"></a>Vytvoření tajného klíče pro správce řadiče dat

Uživatelské jméno a heslo správce řadiče dat slouží k ověřování v rozhraní API řadiče dat za účelem provádění funkcí správy.  Vyberte zabezpečené heslo a sdílejte ho jenom s uživateli, kteří potřebují mít oprávnění správce clusteru.

Tajný kód Kubernetes je uložen jako řetězec kódovaný v kódování Base64 – jeden pro uživatelské jméno a jeden pro heslo.

Můžete použít online nástroj k kódování Base64 vaše požadované uživatelské jméno a heslo nebo můžete v závislosti na vaší platformě použít integrované nástroje rozhraní příkazového řádku.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo '<your string to encode here>' | base64

#Example
# echo 'example' | base64
```

Po zakódování uživatelského jména a hesla můžete vytvořit soubor založený na [souboru šablony](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/controller-login-secret.yaml) a hodnoty uživatelského jména a hesla nahradit vlastními.

Pak spusťte následující příkaz pro vytvoření tajného klíče.

```console
kubectl create --namespace arc -f <path to your data controller secret file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\controller-login-secret.yaml
```

## <a name="create-the-data-controller"></a>Vytvoření kontroleru dat

Nyní jste připraveni vytvořit samotný řadič dat.

Nejprve vytvořte kopii [souboru šablony](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/data-controller.yaml) místně na počítači, abyste mohli změnit některá nastavení.

Podle potřeby upravte následující:

**POŽADOVANOU**
- **umístění**: tuto změnu změňte na umístění Azure, ve kterém se budou ukládat _metadata_ o řadiči dat.  Seznam dostupných umístění Azure najdete v článku [Přehled vytvoření kontroleru dat](create-data-controller.md) .
- **resourceGroup**skupina prostředků Azure, ve které chcete vytvořit prostředek Azure řadiče dat v Azure Resource Manager.  Obvykle by tato skupina prostředků měla existovat, ale není nutná až do doby, kdy odešlete data do Azure.
- **předplatné**: identifikátor GUID předplatného Azure pro předplatné, ve kterém chcete vytvořit prostředky Azure.

**DOPORUČUJE SE ZKONTROLOVAT A PŘÍPADNĚ ZMĚNIT VÝCHOZÍ NASTAVENÍ.**
- **úložiště... className**: třída úložiště, která se má použít pro data a soubory protokolu řadiče dat.  Pokud si nejste jistí dostupné třídy úložiště v clusteru Kubernetes, můžete spustit následující příkaz: `kubectl get storageclass` .  Ve výchozím nastavení `default` předpokládáme, že existuje třída úložiště, která existuje a je pojmenována, že existuje `default` třída úložiště, která _je_ výchozím nastavením.  Poznámka: Existují dvě nastavení className, která se mají nastavit na požadovanou třídu úložiště – jedna pro data a druhá pro protokoly.
- **ServiceType**: `NodePort` Pokud nepoužíváte Nástroj pro vyrovnávání zatížení, změňte typ služby na.  Poznámka: Existují dvě nastavení serviceType, která je potřeba změnit.

**VOLITELNÉ**
- **název**: výchozí název řadiče dat je `arc` , ale pokud chcete, můžete ho změnit.
- **DisplayName**: nastavte tuto hodnotu na stejnou hodnotu jako atribut Name v horní části souboru.
- **registr**: výchozí hodnota je Microsoft Container Registry.  Pokud nasazujete image z Microsoft Container Registry a [přesunete je do soukromého registru kontejnerů](offline-deployment.md), zadejte do tohoto pole IP adresu nebo název DNS vašeho registru.
- **dockerRegistry**: image pro vyžádání obsahu, která se má použít k vyžádání imagí z privátního registru kontejnerů, pokud je to potřeba.
- **úložiště**: výchozí úložiště v Microsoft Container Registry je `arcdata` .  Pokud používáte privátní registr kontejnerů, zadejte cestu ke složce nebo úložišti obsahujícím image datových služeb s povolenou službou Azure Arr.
- **imageTag**: aktuální značka nejnovější verze je v šabloně nastavena jako výchozí, ale pokud chcete použít starší verzi, můžete ji změnit.

Příklad dokončeného souboru YAML s datovým kontrolérem:
```yaml
apiVersion: arcdata.microsoft.com/v1alpha1
kind: datacontroller
metadata:
  generation: 1
  name: arc
spec:
  credentials:
    controllerAdmin: controller-login-secret
    #dockerRegistry: mssql-private-registry - optional if you are using a private container registry that requires authentication using an image pull secret
    serviceAccount: sa-mssql-controller
  docker:
    imagePullPolicy: Always
    imageTag: public-preview-sep-2020
    registry: mcr.microsoft.com
    repository: arcdata
  security:
    allowDumps: true
    allowNodeMetricsCollection: true
    allowPodMetricsCollection: true
    allowRunAsRoot: false
  services:
  - name: controller
    port: 30080
    serviceType: LoadBalancer
  - name: serviceProxy
    port: 30777
    serviceType: LoadBalancer
  settings:
    ElasticSearch:
      vm.max_map_count: "-1"
    azure:
      connectionMode: Indirect
      location: eastus
      resourceGroup: myresourcegroup
      subscription: c82c901a-129a-435d-86e4-cc6b294590ae
    controller:
      displayName: arc
      enableBilling: "True"
      logs.rotation.days: "7"
      logs.rotation.size: "5000"
  storage:
    data:
      accessMode: ReadWriteOnce
      className: default
      size: 15Gi
    logs:
      accessMode: ReadWriteOnce
      className: default
      size: 10Gi
```

Uložte upravený soubor do místního počítače a spuštěním následujícího příkazu vytvořte řadič dat:

```console
kubectl create --namespace arc -f <path to your data controller file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\data-controller.yaml
```

## <a name="monitoring-the-creation-status"></a>Monitorování stavu vytváření

Dokončení vytvoření kontroleru bude trvat několik minut. Průběh můžete sledovat v jiném okně terminálu pomocí následujících příkazů:

> [!NOTE]
>  V ukázkových příkazech se předpokládá, že jste vytvořili řadič dat a obor názvů Kubernetes s názvem `arc` .  Pokud jste použili jiný název oboru názvů nebo data Controller, můžete ho nahradit `arc` vaším jménem.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Stav vytvoření kteréhokoli z nich můžete také ověřit spuštěním příkazu podobného níže.  To je užitečné hlavně při řešení problémů.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

Rozšíření Azure ARC pro Azure Data Studio poskytuje Poznámkový blok, který vás provede prostředím, jak nastavit Kubernetes s povoleným rozšířením Azure ARC a nakonfigurovat ho na monitorování úložiště Git, které obsahuje ukázkový soubor YAML spravované instance SQL. Když je vše připojené, do clusteru Kubernetes se nasadí Nová spravovaná instance SQL.

Další informace najdete v tématu **nasazení spravované instance SQL pomocí poznámkového bloku Kubernetes a toků s povoleným zavedením** v rozšíření Azure arc pro Azure Data Studio.

## <a name="troubleshooting-creation-problems"></a>Řešení potíží s vytvářením

Pokud narazíte na potíže s vytvářením, přečtěte si [příručku Poradce při potížích](troubleshoot-guide.md).

## <a name="next-steps"></a>Další kroky

- [Vytvoření spravované instance SQL pomocí Kubernetes – nativních nástrojů](./create-sql-managed-instance-using-kubernetes-native-tools.md)
- [Vytvoření PostgreSQL skupiny serverů s škálovatelným škálováním pomocí Kubernetes – nativních nástrojů](./create-postgresql-hyperscale-server-group-kubernetes-native-tools.md)
