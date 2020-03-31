---
title: Odkaz YAML pro skupinu kontejnerů
description: Odkaz na soubor YAML podporovaný instancemi kontejnerů Azure pro konfiguraci skupiny kontejnerů
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 8497330a327201c4c64e9f7ae57e6fc4225b52de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896570"
---
# <a name="yaml-reference-azure-container-instances"></a>Odkaz na YAML: Instance kontejnerů Azure

Tento článek popisuje syntaxi a vlastnosti souboru YAML podporované instance azure kontejneru pro [konfiguraci skupiny kontejnerů](container-instances-container-groups.md). Pomocí souboru YAML zadejte konfiguraci skupiny do [příkazu az kontejneru vytvořit][az-container-create] v azure cli. 

Soubor YAML je pohodlný způsob konfigurace skupiny kontejnerů pro reprodukovatelná nasazení. Je stručnou alternativou k použití [šablony Správce prostředků](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) nebo sad SDK s instancemi kontejnerů Azure k vytvoření nebo aktualizaci skupiny kontejnerů.

> [!NOTE]
> Tento odkaz se vztahuje na soubory YAML pro `2018-10-01`verzi rozhraní REST ROZHRANÍ REST instance Azure Container Instances .

## <a name="schema"></a>Schéma 

Schéma pro soubor YAML následuje, včetně komentářů pro zvýraznění klíčových vlastností. Popis vlastností v tomto schématu naleznete v části [Hodnoty vlastností.](#property-values)

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

Následující tabulky popisují hodnoty, které je třeba nastavit ve schématu.

<a id="Microsoft.ContainerInstance/containerGroups" />

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Objekt Microsoft.ContainerInstance/containerGroups

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  jméno | řetězec | Ano | Název skupiny kontejnerů. |
|  apiVersion | enum | Ano | 2018-10-01 |
|  location | řetězec | Ne | Umístění zdroje. |
|  tags | objekt | Ne | Značky prostředků. |
|  identity | objekt | Ne | Identita skupiny kontejnerů, pokud je nakonfigurována. - [Objekt ContainerGroupIdentity](#ContainerGroupIdentity) |
|  properties | objekt | Ano | [Objekt ContainerGroupProperties](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>Objekt ContainerGroupIdentity

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  type | enum | Ne | Typ identity použité pro skupinu kontejnerů. Typ SystemAssigned, UserAssigned zahrnuje implicitně vytvořenou identitu a sadu identit přiřazených uživateli. Typ None odebere všechny identity ze skupiny kontejnerů. - Systémpřiřazený, Přiřazený uživatel, Přiřazený systém, Přiřazený uživatel, Žádný |
|  userAssignedIdentities | objekt | Ne | Seznam identit uživatelů přidružených ke skupině kontejnerů. Odkazy na klíče slovníku identity uživatele budou ID prostředků Správce prostředků Azure ve formuláři: '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>Objekt ContainerGroupProperties

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  containers | pole | Ano | Kontejnery v rámci skupiny kontejnerů. - [Objekt kontejneru](#Container) |
|  imageRegistryCredentials | pole | Ne | Pověření registru bitové kopie, ze kterých je skupina kontejnerů vytvořena. - [Objekt ImageRegistryCredential](#ImageRegistryCredential) |
|  restartpolicy | enum | Ne | Restartujte zásady pro všechny kontejnery v rámci skupiny kontejnerů. - `Always`Vždy restartovat- `OnFailure` Restartovat při selhání- `Never` Nikdy restartovat. - Vždy, OnFailure, Nikdy |
|  adresa IP | objekt | Ne | Typ adresy IP skupiny kontejnerů. - [Objekt IpAddress](#IpAddress) |
|  osTyp | enum | Ano | Typ operačního systému vyžadované kontejnery ve skupině kontejnerů. - Windows nebo Linux |
|  volumes | pole | Ne | Seznam svazků, které mohou být připojeny kontejnery v této skupině kontejnerů. - [Objekt svazku](#Volume) |
|  Diagnostika | objekt | Ne | Diagnostické informace pro skupinu kontejnerů. - [Objekt ContainerGroupDiagnostics](#ContainerGroupDiagnostics) |
|  networkProfile | objekt | Ne | Informace o profilu sítě pro skupinu kontejnerů. - [Objekt ContainerGroupNetworkProfile](#ContainerGroupNetworkProfile) |
|  Dnsconfig | objekt | Ne | Informace o konfiguraci DNS pro skupinu kontejnerů. - [Objekt DnsConfiguration](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Objekt kontejneru

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  jméno | řetězec | Ano | Uživatelem poskytnutý název instance kontejneru. |
|  properties | objekt | Ano | Vlastnosti instance kontejneru. - [Objekt ContainerProperties](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>Objekt ImageRegistryCredential

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  server | řetězec | Ano | Server registru bitových obrázků Dockeru bez protokolu, například "http" a "https". |
|  uživatelské jméno | řetězec | Ano | Uživatelské jméno soukromého registru. |
|  heslo | řetězec | Ne | Heslo pro soukromý registr. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>Objekt IpAddress

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  ports | pole | Ano | Seznam portů vystavených ve skupině kontejnerů. - [Objekt portu](#Port) |
|  type | enum | Ano | Určuje, zda je adresa IP vystavena veřejné síti nebo privátní virtuální síti. - Veřejné nebo soukromé |
|  Ip | řetězec | Ne | IP vystavena veřejnému internetu. |
|  dnsNameLabel | řetězec | Ne | Popisek názvu DNS pro adresu IP. |


<a id="Volume" />

### <a name="volume-object"></a>Objekt svazku

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  jméno | řetězec | Ano | Název svazku. |
|  soubor azureFile | objekt | Ne | Svazek souboru Azure. - [Objekt AzureFileVolume](#AzureFileVolume) |
|  emptyDir | objekt | Ne | Prázdný svazek adresáře. |
|  Tajný kód | objekt | Ne | Tajný svazek. |
|  gitRepo | objekt | Ne | Git repo svazek. - [GitRepoVolume, objekt](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>Objekt ContainerGroupDiagnostics

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | objekt | Ne | Informace o analýze protokolu skupiny kontejnerů. - [Objekt LogAnalytics](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>Objekt ContainerGroupNetworkProfile

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  id | řetězec | Ano | Identifikátor pro profil sítě. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>Objekt DnsConfiguration

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  Nameservery | pole | Ano | Servery DNS pro skupinu kontejnerů. - řetězec |
|  searchDomains | řetězec | Ne | Vyhledávací domény DNS pro vyhledávání názvů hostitelů ve skupině kontejnerů. |
|  možnosti | řetězec | Ne | Možnosti DNS pro skupinu kontejnerů. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>Objekt ContainerProperties

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  image | řetězec | Ano | Název bitové kopie použité k vytvoření instance kontejneru. |
|  command | pole | Ne | Příkazy, které mají být provedeny v rámci instance kontejneru ve formě exec. - řetězec |
|  ports | pole | Ne | Vystavené porty na instanci kontejneru. - [Objekt ContainerPort](#ContainerPort) |
|  environmentVariables | pole | Ne | Proměnné prostředí nastavit v instanci kontejneru. - [Objekt EnvironmentVariable](#EnvironmentVariable) |
|  resources | objekt | Ano | Požadavky na prostředky instance kontejneru. - [Objekt ResourceRequirements](#ResourceRequirements) |
|  volumeMounts | pole | Ne | Svazek se připojí k dispozici pro instanci kontejneru. - [Objekt VolumeMount](#VolumeMount) |
|  livenessProbe | objekt | Ne | Sonda živosti. - [Objekt ContainerProbe](#ContainerProbe) |
|  připravenostSonda | objekt | Ne | Sonda připravenosti. - [Objekt ContainerProbe](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Objekt portu

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  Protokol | enum | Ne | Protokol přidružený k portu. - TCP nebo UDP |
|  port | celé číslo | Ano | Číslo portu |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>Objekt AzureFileVolume

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  Název_sdílené_položky | řetězec | Ano | Název sdílené složky Azure File, který se má připojit jako svazek. |
|  Readonly | Boolean | Ne | Příznak označující, zda sdílený soubor Azure připojen jako svazek je jen pro čtení. |
|  storageAccountName | řetězec | Ano | Název účtu úložiště, který obsahuje sdílenou složku Azure. |
|  storageAccountKey | řetězec | Ne | Přístupový klíč účtu úložiště, který se používá pro přístup ke sdílené složce Azure. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>GitRepoVolume, objekt

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  adresář | řetězec | Ne | Cílový název adresáře. Nesmí obsahovat ani začínat na '...  Pokud je zadáno '.' , bude adresář svazku úložiště git.  V opačném případě, pokud je zadán, bude svazek obsahovat úložiště git v podadresáři s daným názvem. |
|  úložiště | řetězec | Ano | Adresa URL úložiště |
|  revision | řetězec | Ne | Potvrzení hash pro zadanou revizi. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>Objekt LogAnalytics

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  workspaceId | řetězec | Ano | ID pracovního prostoru pro analýzu protokolů |
|  klíč pracovního prostoru | řetězec | Ano | Klíč pracovního prostoru pro analýzu protokolů |
|  logType | enum | Ne | Typ protokolu, který má být použit. - ContainerInsights nebo ContainerInstanceLogs |
|  zprostředkovatele identity | objekt | Ne | Metadata pro analýzu protokolu. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>Objekt ContainerPort

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  Protokol | enum | Ne | Protokol přidružený k portu. - TCP nebo UDP |
|  port | celé číslo | Ano | Číslo portu vystavené v rámci skupiny kontejnerů. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>Objekt EnvironmentVariable

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  jméno | řetězec | Ano | Název proměnné prostředí. |
|  value | řetězec | Ne | Hodnota proměnné prostředí. |
|  secureValue | řetězec | Ne | Hodnota proměnné zabezpečeného prostředí. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>Objekt ResourceRequirements

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  Požadavky | objekt | Ano | Požadavky na prostředky této instance kontejneru. - [Objekt ResourceRequests](#ResourceRequests) |
|  Limity | objekt | Ne | Omezení prostředků této instance kontejneru. - [ResourceLimits objekt](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>Objekt VolumeMount

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  jméno | řetězec | Ano | Název připojení svazku. |
|  mountPath | řetězec | Ano | Cesta v kontejneru, kde by měl být svazek namontován. Nesmí obsahovat dvojtečku (:). |
|  Readonly | Boolean | Ne | Příznak označující, zda je připojení svazku jen pro čtení. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>Objekt ContainerProbe

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  Exec | objekt | Ne | Příkaz spuštění pro sondu - [Objekt ContainerExec](#ContainerExec) |
|  httpGet | objekt | Ne | Http Get nastavení pro sondu - [ContainerHttpGet objektu](#ContainerHttpGet) |
|  initialDelaySeconds | celé číslo | Ne | Počáteční zpoždění sekund. |
|  periodSeconds | celé číslo | Ne | Období sekund. |
|  failureThreshold | celé číslo | Ne | Prahová hodnota selhání. |
|  successThreshold | celé číslo | Ne | Práh úspěchu. |
|  timeoutSeconds | celé číslo | Ne | Časový rozsah sekund. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>Objekt ResourceRequests

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | číslo | Ano | Požadavek na paměť v GB této instance kontejneru. |
|  Cpu | číslo | Ano | Požadavek procesoru této instance kontejneru. |
|  Gpu | objekt | Ne | Požadavek GPU této instance kontejneru. - [Objekt GpuResource](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>ResourceLimits objekt

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | číslo | Ne | Limit paměti v GB této instance kontejneru. |
|  Cpu | číslo | Ne | Limit procesoru této instance kontejneru. |
|  Gpu | objekt | Ne | Limit GPU této instance kontejneru. - [Objekt GpuResource](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>Objekt ContainerExec

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  command | pole | Ne | Příkazy, které mají být provedeny v kontejneru. - řetězec |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>Objekt ContainerHttpGet

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  cesta | řetězec | Ne | Cesta ke sondě. |
|  port | celé číslo | Ano | Číslo portu pro sondu. |
|  scheme | enum | Ne | Schéma. - http nebo https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>Objekt GpuResource

|  Name (Název) | Typ | Požaduje se | Hodnota |
|  ---- | ---- | ---- | ---- |
|  count | celé číslo | Ano | Počet prostředků GPU. |
|  Sku | enum | Ano | Skladová položka prostředku GPU. - K80, P100, V100 |


## <a name="next-steps"></a>Další kroky

Podívejte se na kurz [Nasazení skupiny s více kontejnery pomocí souboru YAML](container-instances-multi-container-yaml.md).

Podívejte se na příklady použití souboru YAML k nasazení skupin kontejnerů ve [virtuální síti](container-instances-vnet.md) nebo připojení [externího svazku](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

