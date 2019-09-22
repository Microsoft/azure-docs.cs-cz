---
title: Odkaz na Azure Container Instances YAML
description: Odkaz na soubor YAML podporovaný nástrojem Azure Container Instances ke konfiguraci skupiny kontejnerů
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 08/12/2019
ms.author: danlep
ms.openlocfilehash: 2e6be18371cf3ff96d1ce91d4dde26ff1f14021b
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2019
ms.locfileid: "71179901"
---
# <a name="yaml-reference-azure-container-instances"></a>Odkaz na YAML: Azure Container Instances

Tento článek se zabývá syntaxí a vlastnostmi souboru YAML, který podporuje Azure Container Instances ke konfiguraci [skupiny kontejnerů](container-instances-container-groups.md). Pomocí souboru YAML zadejte konfiguraci skupiny do příkazu [AZ Container Create][az-container-create] v rozhraní příkazového řádku Azure CLI. 

Soubor YAML je pohodlný způsob, jak nakonfigurovat skupinu kontejnerů pro reprodukovatelná nasazení. Je to Stručná alternativa k použití [šablony Správce prostředků](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) nebo sady Azure Container Instances SDK k vytvoření nebo aktualizaci skupiny kontejnerů.

> [!NOTE]
> Tento odkaz se vztahuje na soubory YAML pro REST API verzi `2018-10-01`Azure Container Instances.

## <a name="schema"></a>Schéma 

Schéma pro soubor YAML je následující, včetně komentářů k vlastnostem klíč zvýraznění. Popis vlastností v tomto schématu naleznete v části [hodnoty vlastností](#property-values) .

```yml
name: string  # Name of the container group
apiVersion: '2018-10-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # Exposed ports on the instance
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
```

## <a name="property-values"></a>Hodnoty vlastností

V následujících tabulkách jsou popsány hodnoty, které je třeba nastavit ve schématu.

<a id="Microsoft.ContainerInstance/containerGroups" />

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Objekt Microsoft. ContainerInstance/containerGroups

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  name | řetězec | Ano | Název skupiny kontejnerů. |
|  apiVersion | Výčet | Ano | 2018-10-01 |
|  location | řetězec | Ne | Umístění prostředku. |
|  značky | object | Ne | Značky prostředků. |
|  identity | object | Ne | Identita skupiny kontejnerů, je-li nakonfigurována. - [Objekt ContainerGroupIdentity](#ContainerGroupIdentity) |
|  properties | object | Ano | [Objekt ContainerGroupProperties](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>Objekt ContainerGroupIdentity

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  type | Výčet | Ne | Typ identity použitý pro skupinu kontejnerů. Typ "SystemAssigned, UserAssigned" zahrnuje implicitně vytvořenou identitu a sadu identit přiřazených uživateli. Typ None odebere všechny identity ze skupiny kontejnerů. -SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, None |
|  userAssignedIdentities | object | Ne | Seznam identit uživatelů přidružených ke skupině kontejnerů. Odkazy na klíč uživatele slovníku identity budou Azure Resource Manager ID prostředků ve formátu: '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName }'. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>Objekt ContainerGroupProperties

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  containers | array | Ano | Kontejnery v rámci skupiny kontejnerů. - [Objekt kontejneru](#Container) |
|  imageRegistryCredentials | array | Ne | Přihlašovací údaje registru bitové kopie, ze kterých se vytvoří skupina kontejnerů. - [Objekt ImageRegistryCredential](#ImageRegistryCredential) |
|  restartPolicy | Výčet | Ne | Restartujte zásady pro všechny kontejnery v rámci skupiny kontejnerů. - `Always`Vždy restartovat a `OnFailure` restartovat při selhání – `Never` Nikdy nerestartovat – Vždycky, chyba, nikdy |
|  Adresa | object | Ne | Typ IP adresy skupiny kontejnerů. - [IpAddress – objekt](#IpAddress) |
|  osType | Výčet | Ano | Typ operačního systému vyžadovaný kontejnery ve skupině kontejnerů. – Windows nebo Linux |
|  svazky | array | Ne | Seznam svazků, které mohou být připojeny kontejnery v této skupině kontejnerů. - [Volume – objekt](#Volume) |
|  diagnostika | object | Ne | Diagnostické informace pro skupinu kontejnerů. - [Objekt ContainerGroupDiagnostics](#ContainerGroupDiagnostics) |
|  networkProfile | object | Ne | Informace o profilu sítě pro skupinu kontejnerů. - [Objekt ContainerGroupNetworkProfile](#ContainerGroupNetworkProfile) |
|  dnsConfig | object | Ne | Informace o konfiguraci DNS pro skupinu kontejnerů. - [Objekt DnsConfiguration](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Objekt kontejneru

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  name | řetězec | Ano | Uživatelem zadaný název instance kontejneru. |
|  properties | object | Ano | Vlastnosti instance kontejneru - [Objekt ContainerProperties](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>Objekt ImageRegistryCredential

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  Server | řetězec | Ano | Server registru Docker image bez protokolu, například http a HTTPS. |
|  username | řetězec | Ano | Uživatelské jméno privátního registru |
|  password | řetězec | Ne | Heslo k privátnímu registru |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>IpAddress – objekt

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  ports | array | Ano | Seznam portů, které jsou vystavené ve skupině kontejnerů. - [Port – objekt](#Port) |
|  type | Výčet | Ano | Určuje, jestli se IP adresa zveřejňuje pro veřejnou internetovou nebo privátní virtuální síť. – Veřejné nebo soukromé |
|  ip | řetězec | Ne | IP adresa veřejného Internetu. |
|  dnsNameLabel | řetězec | Ne | Popisek názvu DNS pro IP adresu. |


<a id="Volume" />

### <a name="volume-object"></a>Volume – objekt

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  name | řetězec | Ano | Název svazku. |
|  AzureFile | object | Ne | Svazek souboru Azure. - [Objekt AzureFileVolume](#AzureFileVolume) |
|  emptyDir | object | Ne | Prázdný svazek adresáře. |
|  secret | object | Ne | Tajný svazek. |
|  gitRepo | object | Ne | Svazek úložiště Git. - [Objekt GitRepoVolume](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>Objekt ContainerGroupDiagnostics

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | Ne | Informace o Log Analytics skupiny kontejnerů. - [Objekt LogAnalytics](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>Objekt ContainerGroupNetworkProfile

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  id | řetězec | Ano | Identifikátor síťového profilu. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>Objekt DnsConfiguration

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  Názvové servery | array | Ano | Servery DNS pro skupinu kontejnerů. – řetězec |
|  searchDomains | řetězec | Ne | Domény hledání DNS pro vyhledávání názvů hostitelů ve skupině kontejnerů. |
|  options | řetězec | Ne | Možnosti DNS pro skupinu kontejnerů. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>Objekt ContainerProperties

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  image | řetězec | Ano | Název Image, která se použila k vytvoření instance kontejneru |
|  Příkaz | array | Ne | Příkazy, které se mají spustit v rámci instance kontejneru ve formuláři Exec. – řetězec |
|  ports | array | Ne | Vystavené porty v instanci kontejneru. - [Objekt ContainerPort](#ContainerPort) |
|  EnvironmentVariables | array | Ne | Proměnné prostředí, které se mají nastavit v instanci kontejneru - [Objekt objekt EnvironmentVariable](#EnvironmentVariable) |
|  zdroje | object | Ano | Požadavky na prostředky pro instanci kontejneru. - [Objekt ResourceRequirements](#ResourceRequirements) |
|  volumeMounts | array | Ne | Připojené svazky jsou k dispozici pro instanci kontejneru. - [Objekt VolumeMount](#VolumeMount) |
|  livenessProbe | object | Ne | Test živého provozu. - [Objekt ContainerProbe](#ContainerProbe) |
|  readinessProbe | object | Ne | Test připravenosti. - [Objekt ContainerProbe](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Port – objekt

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  protocol | Výčet | Ne | Protokol přidružený k portu – TCP nebo UDP |
|  port | integer | Ano | Číslo portu. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>Objekt AzureFileVolume

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  shareName | řetězec | Ano | Název sdílené složky Azure, která se má připojit jako svazek. |
|  ReadOnly | boolean | Ne | Příznak označující, zda je sdílené soubory Azure připojené jako svazek jen pro čtení. |
|  storageAccountName | řetězec | Ano | Název účtu úložiště, který obsahuje sdílenou složku Azure. |
|  storageAccountKey | řetězec | Ne | Přístupový klíč účtu úložiště, který se používá pro přístup ke sdílené složce Azure. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>Objekt GitRepoVolume

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  Adresář | řetězec | Ne | Název cílového adresáře Nesmí obsahovat ani začínat znakem '.. '.  Je-li zadán znak ".", bude adresář svazku úložištěm Git.  V opačném případě bude svazek obsahovat úložiště Git v podadresáři se zadaným názvem. |
|  repository | řetězec | Ano | Adresa URL úložiště |
|  Revize | řetězec | Ne | Potvrďte hodnotu hash pro zadanou revizi. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>Objekt LogAnalytics

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  ID pracovního prostoru | řetězec | Ano | ID pracovního prostoru pro Log Analytics |
|  WorkspaceKey | řetězec | Ano | Klíč pracovního prostoru pro Log Analytics |
|  logType | Výčet | Ne | Typ protokolu, který se má použít. – ContainerInsights nebo ContainerInstanceLogs |
|  zprostředkovatele identity | object | Ne | Metadata pro Log Analytics. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>Objekt ContainerPort

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  protocol | Výčet | Ne | Protokol přidružený k portu – TCP nebo UDP |
|  port | integer | Ano | Číslo portu, které je zveřejněné ve skupině kontejnerů. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>Objekt objekt EnvironmentVariable

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  name | řetězec | Ano | Název proměnné prostředí |
|  value | řetězec | Ne | Hodnota proměnné prostředí. |
|  secureValue | řetězec | Ne | Hodnota proměnné prostředí zabezpečení |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>Objekt ResourceRequirements

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  požadavků | object | Ano | Požadavky na prostředky této instance kontejneru - [Objekt ResourceRequests](#ResourceRequests) |
|  lhůty | object | Ne | Omezení prostředků této instance kontejneru. - [Objekt ResourceLimits](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>Objekt VolumeMount

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  name | řetězec | Ano | Název připojení svazku |
|  mountPath | řetězec | Ano | Cesta v kontejneru, do které má být svazek připojen. Nesmí obsahovat dvojtečku (:). |
|  ReadOnly | boolean | Ne | Příznak označující, zda je připojení svazku jen pro čtení. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>Objekt ContainerProbe

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  Průměrná | object | Ne | Příkaz pro spuštění testu – [objekt ContainerExec](#ContainerExec) |
|  httpGet | object | Ne | Nastavení HTTP GET pro test- [ContainerHttpGet objekt](#ContainerHttpGet) |
|  initialDelaySeconds | integer | Ne | Počáteční zpoždění sekund. |
|  periodSeconds | integer | Ne | Doba v sekundách. |
|  failureThreshold | integer | Ne | Prahová hodnota selhání. |
|  successThreshold | integer | Ne | Prahová hodnota úspěšnosti. |
|  timeoutSeconds | integer | Ne | Časový limit v sekundách |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>Objekt ResourceRequests

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | Ano | Požadavek na paměť v GB této instance kontejneru |
|  procesor | number | Ano | Požadavek na procesor této instance kontejneru |
|  gpu | object | Ne | Požadavek GPU této instance kontejneru - [Objekt GpuResource](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>Objekt ResourceLimits

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | Ne | Limit paměti v GB této instance kontejneru. |
|  procesor | number | Ne | Limit procesoru této instance kontejneru. |
|  gpu | object | Ne | Limit GPU této instance kontejneru - [Objekt GpuResource](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>Objekt ContainerExec

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  Příkaz | array | Ne | Příkazy, které se mají provést v rámci kontejneru. – řetězec |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>Objekt ContainerHttpGet

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  path | řetězec | Ne | Cesta k testu |
|  port | integer | Ano | Číslo portu k testování. |
|  schéma | Výčet | Ne | Schéma. – http nebo https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>Objekt GpuResource

|  Name | type | Požadováno | Value |
|  ---- | ---- | ---- | ---- |
|  count | integer | Ano | Počet prostředků GPU |
|  SKU | Výčet | Ano | SKU zdroje GPU. – K80, P100, V100 |


## <a name="next-steps"></a>Další kroky

Podívejte se na kurz [nasazení skupiny s více kontejnery pomocí souboru YAML](container-instances-multi-container-yaml.md).

Podívejte se na příklady použití souboru YAML k nasazení skupin kontejnerů ve [virtuální síti](container-instances-vnet.md) nebo [připojení externího svazku](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

