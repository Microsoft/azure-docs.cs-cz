---
title: YAML reference pro skupinu kontejnerů
description: Odkaz na soubor YAML podporovaný nástrojem Azure Container Instances ke konfiguraci skupiny kontejnerů
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: d0ec8d13eebba1c60f5a52f8c43bdd8b90eeb913
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87084756"
---
# <a name="yaml-reference-azure-container-instances"></a>Odkaz na YAML: Azure Container Instances

Tento článek se zabývá syntaxí a vlastnostmi souboru YAML, který podporuje Azure Container Instances ke konfiguraci [skupiny kontejnerů](container-instances-container-groups.md). Pomocí souboru YAML zadejte konfiguraci skupiny do příkazu [AZ Container Create][az-container-create] v rozhraní příkazového řádku Azure CLI. 

Soubor YAML je pohodlný způsob, jak nakonfigurovat skupinu kontejnerů pro reprodukovatelná nasazení. Je to Stručná alternativa k použití [šablony Správce prostředků](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups) nebo sady Azure Container Instances SDK k vytvoření nebo aktualizaci skupiny kontejnerů.

> [!NOTE]
> Tento odkaz se vztahuje na soubory YAML pro REST API verzi Azure Container Instances `2019-12-01` .

## <a name="schema"></a>Schéma 

Schéma pro soubor YAML je následující, včetně komentářů k vlastnostem klíč zvýraznění. Popis vlastností v tomto schématu naleznete v části [hodnoty vlastností](#property-values) .

```yml
name: string  # Name of the container group
apiVersion: '2019-12-01'
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
  sku: string # SKU for the container group
  encryptionProperties:
    vaultBaseUrl: string
    keyName: string
    keyVersion: string
  initContainers: # Array of init containers in the group
  - name: string
    properties:
      image: string
      command:
      - string
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      volumeMounts:
      - name: string
        mountPath: string
        readOnly: boolean
```

## <a name="property-values"></a>Hodnoty vlastností

V následujících tabulkách jsou popsány hodnoty, které je třeba nastavit ve schématu.



### <a name="microsoftcontainerinstancecontainergroups-object"></a>Objekt Microsoft. ContainerInstance/containerGroups

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  name | řetězec | Yes | Název skupiny kontejnerů. |
|  apiVersion | enum | Yes | 2018-10-01 |
|  location | řetězec | No | Umístění prostředku. |
|  tags | object | No | Značky prostředků. |
|  identity | object | No | Identita skupiny kontejnerů, je-li nakonfigurována. - [Objekt ContainerGroupIdentity](#containergroupidentity-object) |
|  properties | object | Yes | [Objekt ContainerGroupProperties](#containergroupproperties-object) |




### <a name="containergroupidentity-object"></a>Objekt ContainerGroupIdentity

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  typ | enum | No | Typ identity použitý pro skupinu kontejnerů. Typ "SystemAssigned, UserAssigned" zahrnuje implicitně vytvořenou identitu a sadu identit přiřazených uživateli. Typ None odebere všechny identity ze skupiny kontejnerů. -SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, None |
|  userAssignedIdentities | object | No | Seznam identit uživatelů přidružených ke skupině kontejnerů. Odkazy na klíč uživatele slovníku identity budou Azure Resource Manager ID prostředků ve formátu: '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName} '. |




### <a name="containergroupproperties-object"></a>Objekt ContainerGroupProperties

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  containers | array | Yes | Kontejnery v rámci skupiny kontejnerů. - [Objekt kontejneru](#container-object) |
|  imageRegistryCredentials | array | No | Přihlašovací údaje registru bitové kopie, ze kterých se vytvoří skupina kontejnerů. - [Objekt ImageRegistryCredential](#imageregistrycredential-object) |
|  restartPolicy | enum | No | Restartujte zásady pro všechny kontejnery v rámci skupiny kontejnerů. - `Always` Vždy restartovat a restartovat `OnFailure` při selhání – `Never` Nikdy nerestartovat – Vždycky, chyba, nikdy |
|  Adresa | object | No | Typ IP adresy skupiny kontejnerů. - [IpAddress – objekt](#ipaddress-object) |
|  osType | enum | Yes | Typ operačního systému vyžadovaný kontejnery ve skupině kontejnerů. – Windows nebo Linux |
|  volumes | array | No | Seznam svazků, které mohou být připojeny kontejnery v této skupině kontejnerů. - [Volume – objekt](#volume-object) |
|  Diagnostika | object | No | Diagnostické informace pro skupinu kontejnerů. - [Objekt ContainerGroupDiagnostics](#containergroupdiagnostics-object) |
|  networkProfile | object | No | Informace o profilu sítě pro skupinu kontejnerů. - [Objekt ContainerGroupNetworkProfile](#containergroupnetworkprofile-object) |
|  dnsConfig | object | No | Informace o konfiguraci DNS pro skupinu kontejnerů. - [Objekt DnsConfiguration](#dnsconfiguration-object) |
| skladové | enum | No | SKU pro skupinu kontejnerů – Standard nebo vyhrazené |
| encryptionProperties | object | No | Vlastnosti šifrování pro skupinu kontejnerů. - [Objekt EncryptionProperties](#encryptionproperties-object) | 
| initContainers | array | No | Kontejnery init pro skupinu kontejnerů. - [Objekt InitContainerDefinition](#initcontainerdefinition-object) |




### <a name="container-object"></a>Objekt kontejneru

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  name | řetězec | Yes | Uživatelem zadaný název instance kontejneru. |
|  properties | object | Yes | Vlastnosti instance kontejneru - [Objekt ContainerProperties](#containerproperties-object) |




### <a name="imageregistrycredential-object"></a>Objekt ImageRegistryCredential

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  server | řetězec | Yes | Server registru Docker image bez protokolu, například http a HTTPS. |
|  username | řetězec | Yes | Uživatelské jméno privátního registru |
|  heslo | řetězec | No | Heslo k privátnímu registru |




### <a name="ipaddress-object"></a>IpAddress – objekt

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  ports | array | Yes | Seznam portů, které jsou vystavené ve skupině kontejnerů. - [Port – objekt](#port-object) |
|  typ | enum | Yes | Určuje, jestli se IP adresa zveřejňuje pro veřejnou internetovou nebo privátní virtuální síť. – Veřejné nebo soukromé |
|  IP | řetězec | No | IP adresa veřejného Internetu. |
|  dnsNameLabel | řetězec | No | Popisek názvu DNS pro IP adresu. |




### <a name="volume-object"></a>Volume – objekt

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  name | řetězec | Yes | Název svazku. |
|  azureFile | object | No | Svazek souboru Azure. - [Objekt AzureFileVolume](#azurefilevolume-object) |
|  emptyDir | object | No | Prázdný svazek adresáře. |
|  Tajný kód | object | No | Tajný svazek. |
|  gitRepo | object | No | Svazek úložiště Git. - [Objekt GitRepoVolume](#gitrepovolume-object) |




### <a name="containergroupdiagnostics-object"></a>Objekt ContainerGroupDiagnostics

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | No | Informace o Log Analytics skupiny kontejnerů. - [Objekt LogAnalytics](#loganalytics-object) |




### <a name="containergroupnetworkprofile-object"></a>Objekt ContainerGroupNetworkProfile

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  id | řetězec | Yes | Identifikátor síťového profilu. |




### <a name="dnsconfiguration-object"></a>Objekt DnsConfiguration

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  Názvové servery | array | Yes | Servery DNS pro skupinu kontejnerů. – řetězec |
|  searchDomains | řetězec | No | Domény hledání DNS pro vyhledávání názvů hostitelů ve skupině kontejnerů. |
|  možnosti | řetězec | No | Možnosti DNS pro skupinu kontejnerů. |


### <a name="encryptionproperties-object"></a>Objekt EncryptionProperties

| Název  | Typ  | Vyžadováno  | Hodnota |
|  ---- | ---- | ---- | ---- |
| vaultBaseUrl  | řetězec    | Yes   | Základní adresa URL trezoru klíčů |
| Klíče   | řetězec    | Yes   | Název šifrovacího klíče |
| Verze    | řetězec    | Yes   | Verze šifrovacího klíče |

### <a name="initcontainerdefinition-object"></a>Objekt InitContainerDefinition

| Název  | Typ  | Vyžadováno  | Hodnota |
|  ---- | ---- | ---- | ---- |
| name  | řetězec |  Yes | Název inicializačního kontejneru. |
| properties    | object    | Yes   | Vlastnosti pro kontejner init - [Objekt InitContainerPropertiesDefinition](#initcontainerpropertiesdefinition-object)


### <a name="containerproperties-object"></a>Objekt ContainerProperties

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  image | řetězec | Yes | Název Image, která se použila k vytvoření instance kontejneru |
|  command | array | No | Příkazy, které se mají spustit v rámci instance kontejneru ve formuláři Exec. – řetězec |
|  ports | array | No | Vystavené porty v instanci kontejneru. - [Objekt ContainerPort](#containerport-object) |
|  environmentVariables | array | No | Proměnné prostředí, které se mají nastavit v instanci kontejneru - [Objekt objekt EnvironmentVariable](#environmentvariable-object) |
|  resources | object | Yes | Požadavky na prostředky pro instanci kontejneru. - [Objekt ResourceRequirements](#resourcerequirements-object) |
|  volumeMounts | array | No | Připojené svazky jsou k dispozici pro instanci kontejneru. - [Objekt VolumeMount](#volumemount-object) |
|  livenessProbe | object | No | Test živého provozu. - [Objekt ContainerProbe](#containerprobe-object) |
|  readinessProbe | object | No | Test připravenosti. - [Objekt ContainerProbe](#containerprobe-object) |




### <a name="port-object"></a>Port – objekt

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  protokol | enum | No | Protokol přidružený k portu – TCP nebo UDP |
|  port | integer | Yes | Číslo portu |




### <a name="azurefilevolume-object"></a>Objekt AzureFileVolume

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  shareName | řetězec | Yes | Název sdílené složky Azure, která se má připojit jako svazek. |
|  readOnly | boolean | No | Příznak označující, zda je sdílené soubory Azure připojené jako svazek jen pro čtení. |
|  storageAccountName | řetězec | Yes | Název účtu úložiště, který obsahuje sdílenou složku Azure. |
|  storageAccountKey | řetězec | No | Přístupový klíč účtu úložiště, který se používá pro přístup ke sdílené složce Azure. |




### <a name="gitrepovolume-object"></a>Objekt GitRepoVolume

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  adresář | řetězec | No | Název cílového adresáře Nesmí obsahovat ani začínat znakem '.. '.  Je-li zadán znak ".", bude adresář svazku úložištěm Git.  V opačném případě bude svazek obsahovat úložiště Git v podadresáři se zadaným názvem. |
|  úložiště | řetězec | Yes | Adresa URL úložiště |
|  revision | řetězec | No | Potvrďte hodnotu hash pro zadanou revizi. |



### <a name="loganalytics-object"></a>Objekt LogAnalytics

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  workspaceId | řetězec | Yes | ID pracovního prostoru pro Log Analytics |
|  workspaceKey | řetězec | Yes | Klíč pracovního prostoru pro Log Analytics |
|  logType | enum | No | Typ protokolu, který se má použít. – ContainerInsights nebo ContainerInstanceLogs |
|  zprostředkovatele identity | object | No | Metadata pro Log Analytics. |


### <a name="initcontainerpropertiesdefinition-object"></a>Objekt InitContainerPropertiesDefinition

| Název  | Typ  | Vyžadováno  | Hodnota |
|  ---- | ---- | ---- | ---- |
| image | řetězec    | No    | Obrázek inicializačního kontejneru. |
| command   | array | No    | Příkaz, který se má provést v kontejneru init ve formuláři Exec. – řetězec |
| environmentVariables | array  | No |Proměnné prostředí, které mají být nastaveny v kontejneru init. - [Objekt objekt EnvironmentVariable](#environmentvariable-object)
| volumeMounts |array   | No    | Připojené svazky jsou k dispozici pro kontejner init. - [Objekt VolumeMount](#volumemount-object)

### <a name="containerport-object"></a>Objekt ContainerPort

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  protokol | enum | No | Protokol přidružený k portu – TCP nebo UDP |
|  port | integer | Yes | Číslo portu, které je zveřejněné ve skupině kontejnerů. |




### <a name="environmentvariable-object"></a>Objekt objekt EnvironmentVariable

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  name | řetězec | Yes | Název proměnné prostředí |
|  value | řetězec | No | Hodnota proměnné prostředí. |
|  secureValue | řetězec | No | Hodnota proměnné prostředí zabezpečení |




### <a name="resourcerequirements-object"></a>Objekt ResourceRequirements

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  požádal | object | Yes | Požadavky na prostředky této instance kontejneru - [Objekt ResourceRequests](#resourcerequests-object) |
|  lhůty | object | No | Omezení prostředků této instance kontejneru. - [Objekt ResourceLimits](#resourcelimits-object) |




### <a name="volumemount-object"></a>Objekt VolumeMount

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  name | řetězec | Yes | Název připojení svazku |
|  mountPath | řetězec | Yes | Cesta v kontejneru, do které má být svazek připojen. Nesmí obsahovat dvojtečku (:). |
|  readOnly | boolean | No | Příznak označující, zda je připojení svazku jen pro čtení. |




### <a name="containerprobe-object"></a>Objekt ContainerProbe

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  Průměrná | object | No | Příkaz pro spuštění testu – [objekt ContainerExec](#containerexec-object) |
|  httpGet | object | No | Nastavení HTTP GET pro test- [ContainerHttpGet objekt](#containerhttpget-object) |
|  initialDelaySeconds | integer | No | Počáteční zpoždění sekund. |
|  periodSeconds | integer | No | Doba v sekundách. |
|  failureThreshold | integer | No | Prahová hodnota selhání. |
|  successThreshold | integer | No | Prahová hodnota úspěšnosti. |
|  timeoutSeconds | integer | No | Časový limit v sekundách |




### <a name="resourcerequests-object"></a>Objekt ResourceRequests

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | číslo | Yes | Požadavek na paměť v GB této instance kontejneru |
|  včetně | číslo | Yes | Požadavek na procesor této instance kontejneru |
|  procesor | object | No | Požadavek GPU této instance kontejneru - [Objekt GpuResource](#gpuresource-object) |




### <a name="resourcelimits-object"></a>Objekt ResourceLimits

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | číslo | No | Limit paměti v GB této instance kontejneru. |
|  včetně | číslo | No | Limit procesoru této instance kontejneru. |
|  procesor | object | No | Limit GPU této instance kontejneru - [Objekt GpuResource](#gpuresource-object) |




### <a name="containerexec-object"></a>Objekt ContainerExec

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  command | array | No | Příkazy, které se mají provést v rámci kontejneru. – řetězec |




### <a name="containerhttpget-object"></a>Objekt ContainerHttpGet

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  program | řetězec | No | Cesta k testu |
|  port | integer | Yes | Číslo portu k testování. |
|  scheme | enum | No | Schéma. – http nebo https |




### <a name="gpuresource-object"></a>Objekt GpuResource

|  Název | Typ | Vyžadováno | Hodnota |
|  ---- | ---- | ---- | ---- |
|  count | integer | Yes | Počet prostředků GPU |
|  skladové | enum | Yes | SKU zdroje GPU. – K80, P100, V100 |


## <a name="next-steps"></a>Další kroky

Podívejte se na kurz [nasazení skupiny s více kontejnery pomocí souboru YAML](container-instances-multi-container-yaml.md).

Podívejte se na příklady použití souboru YAML k nasazení skupin kontejnerů ve [virtuální síti](container-instances-vnet.md) nebo [připojení externího svazku](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

