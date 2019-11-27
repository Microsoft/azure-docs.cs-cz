---
title: YAML reference pro skupinu kontejnerů
description: Odkaz na soubor YAML podporovaný nástrojem Azure Container Instances ke konfiguraci skupiny kontejnerů
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 5603f2e0f63c4f83a6d3761feb540abb8b8b7d5c
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533492"
---
# <a name="yaml-reference-azure-container-instances"></a>Odkaz na YAML: Azure Container Instances

Tento článek se zabývá syntaxí a vlastnostmi souboru YAML, který podporuje Azure Container Instances ke konfiguraci [skupiny kontejnerů](container-instances-container-groups.md). Pomocí souboru YAML zadejte konfiguraci skupiny do příkazu [AZ Container Create][az-container-create] v rozhraní příkazového řádku Azure CLI. 

Soubor YAML je pohodlný způsob, jak nakonfigurovat skupinu kontejnerů pro reprodukovatelná nasazení. Je to Stručná alternativa k použití [šablony Správce prostředků](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) nebo sady Azure Container Instances SDK k vytvoření nebo aktualizaci skupiny kontejnerů.

> [!NOTE]
> Tento odkaz se týká souborů YAML Azure Container Instances REST API verze `2018-10-01`.

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

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  jméno | řetězec | Ano | Název skupiny kontejnerů. |
|  apiVersion | Výčet | Ano | 2018-10-01 |
|  location | řetězec | Ne | Umístění prostředku. |
|  značek | objekt | Ne | Značky prostředků. |
|  identity | objekt | Ne | Identita skupiny kontejnerů, je-li nakonfigurována. - [objekt ContainerGroupIdentity](#ContainerGroupIdentity) |
|  properties | objekt | Ano | [Objekt ContainerGroupProperties](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>Objekt ContainerGroupIdentity

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  type | Výčet | Ne | Typ identity použitý pro skupinu kontejnerů. Typ "SystemAssigned, UserAssigned" zahrnuje implicitně vytvořenou identitu a sadu identit přiřazených uživateli. Typ None odebere všechny identity ze skupiny kontejnerů. -SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, None |
|  userAssignedIdentities | objekt | Ne | Seznam identit uživatelů přidružených ke skupině kontejnerů. Odkazy na klíč uživatele slovníku identity budou Azure Resource Manager ID prostředků ve formátu: '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName} '. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>Objekt ContainerGroupProperties

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  containers | pole | Ano | Kontejnery v rámci skupiny kontejnerů. - [kontejnerový objekt](#Container) |
|  imageRegistryCredentials | pole | Ne | Přihlašovací údaje registru bitové kopie, ze kterých se vytvoří skupina kontejnerů. - [objekt ImageRegistryCredential](#ImageRegistryCredential) |
|  restartPolicy | Výčet | Ne | Restartujte zásady pro všechny kontejnery v rámci skupiny kontejnerů. - `Always` vždy restartovat – `OnFailure` restart při selhání – `Never` nikdy nerestartuje. – Vždycky, chyba, nikdy |
|  Adresa | objekt | Ne | Typ IP adresy skupiny kontejnerů.[objekt  - IPAddress](#IpAddress) |
|  osType | Výčet | Ano | Typ operačního systému vyžadovaný kontejnery ve skupině kontejnerů. – Windows nebo Linux |
|  volumes | pole | Ne | Seznam svazků, které mohou být připojeny kontejnery v této skupině kontejnerů.[objekt  - Volume](#Volume) |
|  Diagnostika | objekt | Ne | Diagnostické informace pro skupinu kontejnerů. - [objekt ContainerGroupDiagnostics](#ContainerGroupDiagnostics) |
|  networkProfile | objekt | Ne | Informace o profilu sítě pro skupinu kontejnerů. - [objekt ContainerGroupNetworkProfile](#ContainerGroupNetworkProfile) |
|  dnsConfig | objekt | Ne | Informace o konfiguraci DNS pro skupinu kontejnerů. - [objekt DnsConfiguration](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Objekt kontejneru

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  jméno | řetězec | Ano | Uživatelem zadaný název instance kontejneru. |
|  properties | objekt | Ano | Vlastnosti instance kontejneru - [objekt ContainerProperties](#ContainerProperties) |


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
|  ports | pole | Ano | Seznam portů, které jsou vystavené ve skupině kontejnerů.[objekt  - portu](#Port) |
|  type | Výčet | Ano | Určuje, jestli se IP adresa zveřejňuje pro veřejnou internetovou nebo privátní virtuální síť. – Veřejné nebo soukromé |
|  IP | řetězec | Ne | IP adresa veřejného Internetu. |
|  dnsNameLabel | řetězec | Ne | Popisek názvu DNS pro IP adresu. |


<a id="Volume" />

### <a name="volume-object"></a>Volume – objekt

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  jméno | řetězec | Ano | Název svazku. |
|  azureFile | objekt | Ne | Svazek souboru Azure. - [objekt AzureFileVolume](#AzureFileVolume) |
|  emptyDir | objekt | Ne | Prázdný svazek adresáře. |
|  Tajný kód | objekt | Ne | Tajný svazek. |
|  gitRepo | objekt | Ne | Svazek úložiště Git. - [objekt GitRepoVolume](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>Objekt ContainerGroupDiagnostics

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | objekt | Ne | Informace o Log Analytics skupiny kontejnerů. - [objekt LogAnalytics](#LogAnalytics) |


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
|  options | řetězec | Ne | Možnosti DNS pro skupinu kontejnerů. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>Objekt ContainerProperties

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  image | řetězec | Ano | Název Image, která se použila k vytvoření instance kontejneru |
|  command | pole | Ne | Příkazy, které se mají spustit v rámci instance kontejneru ve formuláři Exec. – řetězec |
|  ports | pole | Ne | Vystavené porty v instanci kontejneru. - [objekt ContainerPort](#ContainerPort) |
|  environmentVariables | pole | Ne | Proměnné prostředí, které se mají nastavit v instanci kontejneru - [objekt objekt EnvironmentVariable](#EnvironmentVariable) |
|  prostředky | objekt | Ano | Požadavky na prostředky pro instanci kontejneru. - [objekt ResourceRequirements](#ResourceRequirements) |
|  volumeMounts | pole | Ne | Připojené svazky jsou k dispozici pro instanci kontejneru. - [objekt VolumeMount](#VolumeMount) |
|  livenessProbe | objekt | Ne | Test živého provozu. - [objekt ContainerProbe](#ContainerProbe) |
|  readinessProbe | objekt | Ne | Test připravenosti. - [objekt ContainerProbe](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Port – objekt

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  protocol | Výčet | Ne | Protokol přidružený k portu – TCP nebo UDP |
|  port | celé číslo | Ano | Číslo portu. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>Objekt AzureFileVolume

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  shareName | řetězec | Ano | Název sdílené složky Azure, která se má připojit jako svazek. |
|  ReadOnly | Boolean | Ne | Příznak označující, zda je sdílené soubory Azure připojené jako svazek jen pro čtení. |
|  storageAccountName | řetězec | Ano | Název účtu úložiště, který obsahuje sdílenou složku Azure. |
|  storageAccountKey | řetězec | Ne | Přístupový klíč účtu úložiště, který se používá pro přístup ke sdílené složce Azure. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>Objekt GitRepoVolume

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  službě | řetězec | Ne | Název cílového adresáře Nesmí obsahovat ani začínat znakem '.. '.  Je-li zadán znak ".", bude adresář svazku úložištěm Git.  V opačném případě bude svazek obsahovat úložiště Git v podadresáři se zadaným názvem. |
|  úložiště | řetězec | Ano | Adresa URL úložiště |
|  Revize | řetězec | Ne | Potvrďte hodnotu hash pro zadanou revizi. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>Objekt LogAnalytics

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  ID pracovního prostoru | řetězec | Ano | ID pracovního prostoru pro Log Analytics |
|  workspaceKey | řetězec | Ano | Klíč pracovního prostoru pro Log Analytics |
|  logType | Výčet | Ne | Typ protokolu, který se má použít. – ContainerInsights nebo ContainerInstanceLogs |
|  zprostředkovatele identity | objekt | Ne | Metadata pro Log Analytics. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>Objekt ContainerPort

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  protocol | Výčet | Ne | Protokol přidružený k portu – TCP nebo UDP |
|  port | celé číslo | Ano | Číslo portu, které je zveřejněné ve skupině kontejnerů. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>Objekt objekt EnvironmentVariable

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  jméno | řetězec | Ano | Název proměnné prostředí |
|  hodnota | řetězec | Ne | Hodnota proměnné prostředí. |
|  secureValue | řetězec | Ne | Hodnota proměnné prostředí zabezpečení |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>Objekt ResourceRequirements

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  požadavků | objekt | Ano | Požadavky na prostředky této instance kontejneru - [objekt ResourceRequests](#ResourceRequests) |
|  lhůty | objekt | Ne | Omezení prostředků této instance kontejneru. - [objekt ResourceLimits](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>Objekt VolumeMount

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  jméno | řetězec | Ano | Název připojení svazku |
|  mountPath | řetězec | Ano | Cesta v kontejneru, do které má být svazek připojen. Nesmí obsahovat dvojtečku (:). |
|  ReadOnly | Boolean | Ne | Příznak označující, zda je připojení svazku jen pro čtení. |


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
|  procesor | číslo | Ano | Požadavek na procesor této instance kontejneru |
|  gpu | objekt | Ne | Požadavek GPU této instance kontejneru - [objekt GpuResource](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>Objekt ResourceLimits

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | číslo | Ne | Limit paměti v GB této instance kontejneru. |
|  procesor | číslo | Ne | Limit procesoru této instance kontejneru. |
|  gpu | objekt | Ne | Limit GPU této instance kontejneru - [objekt GpuResource](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>Objekt ContainerExec

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  command | pole | Ne | Příkazy, které se mají provést v rámci kontejneru. – řetězec |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>Objekt ContainerHttpGet

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  path | řetězec | Ne | Cesta k testu |
|  port | celé číslo | Ano | Číslo portu k testování. |
|  programu | Výčet | Ne | Schéma. – http nebo https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>Objekt GpuResource

|  Název | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  count | celé číslo | Ano | Počet prostředků GPU |
|  skladové | Výčet | Ano | SKU zdroje GPU. – K80, P100, V100 |


## <a name="next-steps"></a>Další kroky

Podívejte se na kurz [nasazení skupiny s více kontejnery pomocí souboru YAML](container-instances-multi-container-yaml.md).

Podívejte se na příklady použití souboru YAML k nasazení skupin kontejnerů ve [virtuální síti](container-instances-vnet.md) nebo [připojení externího svazku](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

