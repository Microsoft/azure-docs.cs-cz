---
title: YAML reference pro skupinu kontejnerů
description: Odkaz na soubor YAML podporovaný nástrojem Azure Container Instances ke konfiguraci skupiny kontejnerů
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 8497330a327201c4c64e9f7ae57e6fc4225b52de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74896570"
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
      ports: # External-facing ports exposed on the instance, must also be set in group ipAddress property 
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

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  jméno | řetězec | Ano | Název skupiny kontejnerů. |
|  apiVersion | enum | Ano | 2018-10-01 |
|  location | řetězec | Ne | Umístění prostředku. |
|  tags | objekt | Ne | Značky prostředků. |
|  identity | objekt | Ne | Identita skupiny kontejnerů, je-li nakonfigurována. - [Objekt ContainerGroupIdentity](#ContainerGroupIdentity) |
|  properties | objekt | Ano | [Objekt ContainerGroupProperties](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>Objekt ContainerGroupIdentity

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  type | enum | Ne | Typ identity použitý pro skupinu kontejnerů. Typ "SystemAssigned, UserAssigned" zahrnuje implicitně vytvořenou identitu a sadu identit přiřazených uživateli. Typ None odebere všechny identity ze skupiny kontejnerů. -SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, None |
|  userAssignedIdentities | objekt | Ne | Seznam identit uživatelů přidružených ke skupině kontejnerů. Odkazy na klíč uživatele slovníku identity budou Azure Resource Manager ID prostředků ve formátu: '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName} '. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>Objekt ContainerGroupProperties

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  containers | pole | Ano | Kontejnery v rámci skupiny kontejnerů. - [Objekt kontejneru](#Container) |
|  imageRegistryCredentials | pole | Ne | Přihlašovací údaje registru bitové kopie, ze kterých se vytvoří skupina kontejnerů. - [Objekt ImageRegistryCredential](#ImageRegistryCredential) |
|  restartPolicy | enum | Ne | Restartujte zásady pro všechny kontejnery v rámci skupiny kontejnerů. - `Always`Vždy restartovat a `OnFailure` restartovat při selhání – `Never` Nikdy nerestartovat – Vždycky, chyba, nikdy |
|  Adresa | objekt | Ne | Typ IP adresy skupiny kontejnerů. - [IpAddress – objekt](#IpAddress) |
|  osType | enum | Ano | Typ operačního systému vyžadovaný kontejnery ve skupině kontejnerů. – Windows nebo Linux |
|  volumes | pole | Ne | Seznam svazků, které mohou být připojeny kontejnery v této skupině kontejnerů. - [Volume – objekt](#Volume) |
|  Diagnostika | objekt | Ne | Diagnostické informace pro skupinu kontejnerů. - [Objekt ContainerGroupDiagnostics](#ContainerGroupDiagnostics) |
|  networkProfile | objekt | Ne | Informace o profilu sítě pro skupinu kontejnerů. - [Objekt ContainerGroupNetworkProfile](#ContainerGroupNetworkProfile) |
|  dnsConfig | objekt | Ne | Informace o konfiguraci DNS pro skupinu kontejnerů. - [Objekt DnsConfiguration](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Objekt kontejneru

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  jméno | řetězec | Ano | Uživatelem zadaný název instance kontejneru. |
|  properties | objekt | Ano | Vlastnosti instance kontejneru - [Objekt ContainerProperties](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>Objekt ImageRegistryCredential

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  server | řetězec | Ano | Server registru Docker image bez protokolu, například http a HTTPS. |
|  uživatelské jméno | řetězec | Ano | Uživatelské jméno privátního registru |
|  heslo | řetězec | Ne | Heslo k privátnímu registru |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>IpAddress – objekt

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  ports | pole | Ano | Seznam portů, které jsou vystavené ve skupině kontejnerů. - [Port – objekt](#Port) |
|  type | enum | Ano | Určuje, jestli se IP adresa zveřejňuje pro veřejnou internetovou nebo privátní virtuální síť. – Veřejné nebo soukromé |
|  IP | řetězec | Ne | IP adresa veřejného Internetu. |
|  dnsNameLabel | řetězec | Ne | Popisek názvu DNS pro IP adresu. |


<a id="Volume" />

### <a name="volume-object"></a>Volume – objekt

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  jméno | řetězec | Ano | Název svazku. |
|  azureFile | objekt | Ne | Svazek souboru Azure. - [Objekt AzureFileVolume](#AzureFileVolume) |
|  emptyDir | objekt | Ne | Prázdný svazek adresáře. |
|  Tajný kód | objekt | Ne | Tajný svazek. |
|  gitRepo | objekt | Ne | Svazek úložiště Git. - [Objekt GitRepoVolume](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>Objekt ContainerGroupDiagnostics

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | objekt | Ne | Informace o Log Analytics skupiny kontejnerů. - [Objekt LogAnalytics](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>Objekt ContainerGroupNetworkProfile

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  id | řetězec | Ano | Identifikátor síťového profilu. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>Objekt DnsConfiguration

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  Názvové servery | pole | Ano | Servery DNS pro skupinu kontejnerů. – řetězec |
|  searchDomains | řetězec | Ne | Domény hledání DNS pro vyhledávání názvů hostitelů ve skupině kontejnerů. |
|  možnosti | řetězec | Ne | Možnosti DNS pro skupinu kontejnerů. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>Objekt ContainerProperties

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  image | řetězec | Ano | Název Image, která se použila k vytvoření instance kontejneru |
|  command | pole | Ne | Příkazy, které se mají spustit v rámci instance kontejneru ve formuláři Exec. – řetězec |
|  ports | pole | Ne | Vystavené porty v instanci kontejneru. - [Objekt ContainerPort](#ContainerPort) |
|  environmentVariables | pole | Ne | Proměnné prostředí, které se mají nastavit v instanci kontejneru - [Objekt objekt EnvironmentVariable](#EnvironmentVariable) |
|  resources | objekt | Ano | Požadavky na prostředky pro instanci kontejneru. - [Objekt ResourceRequirements](#ResourceRequirements) |
|  volumeMounts | pole | Ne | Připojené svazky jsou k dispozici pro instanci kontejneru. - [Objekt VolumeMount](#VolumeMount) |
|  livenessProbe | objekt | Ne | Test živého provozu. - [Objekt ContainerProbe](#ContainerProbe) |
|  readinessProbe | objekt | Ne | Test připravenosti. - [Objekt ContainerProbe](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Port – objekt

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  protokol | enum | Ne | Protokol přidružený k portu – TCP nebo UDP |
|  port | celé číslo | Ano | Číslo portu |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>Objekt AzureFileVolume

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  shareName | řetězec | Ano | Název sdílené složky Azure, která se má připojit jako svazek. |
|  readOnly | Boolean | Ne | Příznak označující, zda je sdílené soubory Azure připojené jako svazek jen pro čtení. |
|  storageAccountName | řetězec | Ano | Název účtu úložiště, který obsahuje sdílenou složku Azure. |
|  storageAccountKey | řetězec | Ne | Přístupový klíč účtu úložiště, který se používá pro přístup ke sdílené složce Azure. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>Objekt GitRepoVolume

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  adresář | řetězec | Ne | Název cílového adresáře Nesmí obsahovat ani začínat znakem '.. '.  Je-li zadán znak ".", bude adresář svazku úložištěm Git.  V opačném případě bude svazek obsahovat úložiště Git v podadresáři se zadaným názvem. |
|  úložiště | řetězec | Ano | Adresa URL úložiště |
|  revision | řetězec | Ne | Potvrďte hodnotu hash pro zadanou revizi. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>Objekt LogAnalytics

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  workspaceId | řetězec | Ano | ID pracovního prostoru pro Log Analytics |
|  workspaceKey | řetězec | Ano | Klíč pracovního prostoru pro Log Analytics |
|  logType | enum | Ne | Typ protokolu, který se má použít. – ContainerInsights nebo ContainerInstanceLogs |
|  zprostředkovatele identity | objekt | Ne | Metadata pro Log Analytics. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>Objekt ContainerPort

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  protokol | enum | Ne | Protokol přidružený k portu – TCP nebo UDP |
|  port | celé číslo | Ano | Číslo portu, které je zveřejněné ve skupině kontejnerů. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>Objekt objekt EnvironmentVariable

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  jméno | řetězec | Ano | Název proměnné prostředí |
|  value | řetězec | Ne | Hodnota proměnné prostředí. |
|  secureValue | řetězec | Ne | Hodnota proměnné prostředí zabezpečení |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>Objekt ResourceRequirements

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  požádal | objekt | Ano | Požadavky na prostředky této instance kontejneru - [Objekt ResourceRequests](#ResourceRequests) |
|  lhůty | objekt | Ne | Omezení prostředků této instance kontejneru. - [Objekt ResourceLimits](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>Objekt VolumeMount

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  jméno | řetězec | Ano | Název připojení svazku |
|  mountPath | řetězec | Ano | Cesta v kontejneru, do které má být svazek připojen. Nesmí obsahovat dvojtečku (:). |
|  readOnly | Boolean | Ne | Příznak označující, zda je připojení svazku jen pro čtení. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>Objekt ContainerProbe

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  Průměrná | objekt | Ne | Příkaz pro spuštění testu – [objekt ContainerExec](#ContainerExec) |
|  httpGet | objekt | Ne | Nastavení HTTP GET pro test- [ContainerHttpGet objekt](#ContainerHttpGet) |
|  initialDelaySeconds | celé číslo | Ne | Počáteční zpoždění sekund. |
|  periodSeconds | celé číslo | Ne | Doba v sekundách. |
|  failureThreshold | celé číslo | Ne | Prahová hodnota selhání. |
|  successThreshold | celé číslo | Ne | Prahová hodnota úspěšnosti. |
|  timeoutSeconds | celé číslo | Ne | Časový limit v sekundách |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>Objekt ResourceRequests

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | číslo | Ano | Požadavek na paměť v GB této instance kontejneru |
|  včetně | číslo | Ano | Požadavek na procesor této instance kontejneru |
|  procesor | objekt | Ne | Požadavek GPU této instance kontejneru - [Objekt GpuResource](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>Objekt ResourceLimits

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | číslo | Ne | Limit paměti v GB této instance kontejneru. |
|  včetně | číslo | Ne | Limit procesoru této instance kontejneru. |
|  procesor | objekt | Ne | Limit GPU této instance kontejneru - [Objekt GpuResource](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>Objekt ContainerExec

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  command | pole | Ne | Příkazy, které se mají provést v rámci kontejneru. – řetězec |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>Objekt ContainerHttpGet

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  cesta | řetězec | Ne | Cesta k testu |
|  port | celé číslo | Ano | Číslo portu k testování. |
|  scheme | enum | Ne | Schéma. – http nebo https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>Objekt GpuResource

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  count | celé číslo | Ano | Počet prostředků GPU |
|  skladové | enum | Ano | SKU zdroje GPU. – K80, P100, V100 |


## <a name="next-steps"></a>Další kroky

Podívejte se na kurz [nasazení skupiny s více kontejnery pomocí souboru YAML](container-instances-multi-container-yaml.md).

Podívejte se na příklady použití souboru YAML k nasazení skupin kontejnerů ve [virtuální síti](container-instances-vnet.md) nebo [připojení externího svazku](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

