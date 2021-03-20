---
title: Azure Stack Edge 2021. dopad na aktualizaci | Microsoft Docs
description: Tento článek popisuje dopad IoT Edge správy rolí v zařízeních Azure Stack Edge po instalaci aktualizace z ledna 2021.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 10/26/2020
ms.author: alkohli
ms.openlocfilehash: f16f33e9aadcc01427602a1bd81f81cb0710e4dd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94578735"
---
# <a name="iot-edge-role-management-changes-for-your-azure-stack-edge-device"></a>IoT Edge změny správy rolí pro zařízení Azure Stack Edge

Pro Azure IoT Edge správu rolí pro zařízení Azure Stack Edge použijte aktualizovanou verzi rozhraní API, sady SDK a Azure PowerShell, která je naplánovaná pro vydání z ledna 2021. 

Tento článek podrobně popisuje změny, které je třeba provést při použití této nejnovější verze.

Aktualizace od ledna 2021 bude k dispozici pouze pro Azure Stack hraničních procesorů pro-GPU, Azure Stack Edge pro R a v Azure Stack hraničních Mini R zařízeních. Informace v tomto článku se vztahují jenom na tato zařízení.

> [!NOTE]
> Nemusíte upgradovat na verzi z ledna 2021. Pokud se rozhodnete, že budete pokračovat v používání aktuální verze, nebudete mít žádný vliv na IoT Edge správu rolí. Pokud ale chcete využít výhod nových funkcí a snížit rizika zabezpečení, doporučujeme nainstalovat novější verzi. 

## <a name="iot-edge-role-management-changes"></a>IoT Edge změny správy rolí

Po instalaci volitelné aktualizace z ledna 2021 na zařízení Azure Stack Edge budete muset pro IoT Edge správu rolí použít nejnovější verzi rutin rozhraní API, sady SDK a PowerShellu.

Následující změny jsou požadovány pouze v případě, že je použita aktualizace z ledna 2021:

- Pokud aktuálně používáte rozhraní API pro správu rolí verze &nbsp; 2019-08-01, upgradujte na verzi rozhraní API, která bude vydána v lednu 2021. 
- Pokud aktuálně používáte správu rolí prostřednictvím sady SDK verze &nbsp; 1.0.0, upgradujte na verzi, která bude vydána v lednu 2021.
- Pokud používáte správu rolí s rutinami Azure PowerShell (Preview), například `Get-AzStackEdgeRole` ,, `New-AzStackEdgeRole` `Set-AzStackEdgeRole` nebo `Remove-AzStackEdgeRole` , počkejte na vydání nových rutin do února 2021.

## <a name="api-usage"></a>Použití rozhraní API

Pokud v tuto chvíli provádíte IoT Edge správu rolí prostřednictvím rozhraní API, měli byste použít novou verzi rozhraní API 2020-12-01, která se bude publikovat později. Pokud používáte aktuální rozhraní API role, po instalaci připravované verze softwarového zařízení se musíte přesunout do role PUT, GET nebo DELETE Kubernetes a potom pomocí rozhraní příkazového doplňku pro vložení IoT.

### <a name="for-the-put-method"></a>Pro metodu PUT

#### <a name="the-current-http-request"></a>Aktuální požadavek HTTP 

- Volání rozhraní API se provádí v tomto identifikátoru URI: ' https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01 '

- Text žádosti vypadá takto:

    ```json
    {
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        }
    }
    ```

#### <a name="the-upcoming-http-request"></a>Nadcházející požadavek HTTP 

- Volání rozhraní API pro roli Kubernetes jsou provedena na následujícím identifikátoru URI: 

  'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1?api-version=2020-12-01'

    Text žádosti bude vypadat takto:

    ```json
    {
        "kind": "Kubernetes",
        "properties": {
            "hostPlatform": "Linux",
            "kubernetesClusterInfo": {
                "version": "v1.17.3"
            },
            "kubernetesRoleResources": {
                "storage": {
                    "endpoints": []
                },
                "compute": {
                    "vmProfile": "DS1_v2"
                }
            }
        }
    }
    ```

- Volání rozhraní API pro doplněk IoT Edge jsou provedena na následujícím identifikátoru URI: 

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

    Text žádosti bude vypadat takto:

    ```json
    {
        "kind": "IoT",
        "properties": {
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            }
        }
    }
    ```


### <a name="for-the-get-method"></a>Pro metodu GET

#### <a name="the-current-http-response"></a>Aktuální odpověď HTTP

- Volání rozhraní API se provádí na následujícím identifikátoru URI:

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01'

- Tělo odpovědi vypadá takto:

    ```json
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1",
        "name": "IoTRole1",
        "type": "dataBoxEdgeDevices/roles"
    }    
    ```

#### <a name="the-upcoming-http-response"></a>Nadcházející odpověď HTTP

- Volání rozhraní API se provádí na následujícím identifikátoru URI: 

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

- Tělo odpovědi vypadá takto: 

    ```json
    {
        "kind": "IoT",
        "properties": {
            "provisioningState": "Creating",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "version": "0.1.0-beta10"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/res1/roles/kubernetesRole/addons/iotName",
        "name": " iotName",
        "type": "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addon",
    }
    ```

### <a name="for-the-delete-method"></a>Pro metodu DELETE

### <a name="the-current-api-calls"></a>Aktuální volání rozhraní API

Volání rozhraní API se provádí na následujícím identifikátoru URI: 

'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01'

### <a name="the-upcoming-api-calls"></a>Nadcházející volání rozhraní API

Volání rozhraní API se provádí na následujícím identifikátoru URI: 

'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

## <a name="sdk-usage"></a>Využití sady SDK

Pokud používáte sadu SDK, budete muset po instalaci aktualizace od ledna 2021 změnit způsob, jakým jste nastavili roli IoT Edge, jak je znázorněno v následující ukázce. Pak si stáhnete a nainstalujete nadcházející balíček NuGet, který se přesune na novou sadu SDK, jak je znázorněno zde.

### <a name="the-current-sdk-sample"></a>Ukázka aktuální sady SDK

```csharp
var iotRoleStatus = "Enabled";
var iotHostPlatform = "Linux";
var id = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/iotrole"; 
var name = "iotrole";
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var iotRoleName = "iotrole";
var ioTDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeAgentInfo = new IoTEdgeAgentInfo(...);
var shareMappings = new List<MountPointMap>(...);

var role = new IoTRole(roleStatus, 
    hostPlatform, 
    shareMappings, 
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    id, 
    name, 
    type);

DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, iotRoleName, role, resourceGroup);
```


### <a name="the-new-sdk-sample"></a>Nová ukázka sady SDK

```csharp
var k8sRoleStatus = "Enabled";
var k8sHostPlatform = "Linux";
var k8sId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole"; 
var k8sRoleName = "KubernetesRole";
var k8sClusterVersion = "v1.17.3"; //Final values will be updated here around January 2021
var k8sVmProfile = "DS1_v2"; //Final values will be updated here around January 2021
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var k8sRole = new KubernetesRole(
    roleStatus,
    hostPlatform,
    shareMappings,
    k8sClusterVersion,
    k8sVmProfile,
    k8sId,
    k8sRoleName,
    type
);
DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, k8sRoleName, k8sRole, resourceGroup); //Final usage will be updated here around January 2021

var ioTId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole/addons/iotaddon";
var ioTAddonName = "iotaddon";
var ioTAddonType = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addons";
var addon = new IoTAddon(
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    ioTId, 
    ioTAddonName, 
    ioTAddonType);
DataBoxEdgeManagementClient.AddOns.CreateOrUpdate(deviceName, k8sRoleName, addonName, addon, resourceGroup); //Final usage will be updated here around January 2021
```

## <a name="cmdlet-usage"></a>Využití rutiny

Pokud aktuálně používáte `Get-AzStackEdgeRole` `New-AzStackEdgeRole` `Set-AzStackEdgeRole` rutinu,, nebo, budete `Remove-AzStackEdgeRole` muset počkat na novou verzi, která je naplánovaná pro verzi z února 2021.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Používám Azure Stack Edge pro-FPGA. Ovlivňuje aktualizace z ledna 2021 model FPGA?**

No. Aktualizace od ledna 2021 se vztahuje pouze na Azure Stack Edge pro-FPGA, Azure Stack Edge pro R a na Azure Stack hraničních Mini R. Tato aktualizace nemá vliv na Azure Stack Edge pro FPGA a nevyžaduje žádné změny IoT Edge správy rolí.

**Po aktualizaci Azure Stack Edge pro-GPU na nový software zařízení v lednu 2021 jsou nějaké stávající služby zasažené?**

No. Vaše nakonfigurované služby nebudou ovlivněny po instalaci aktualizace zařízení z ledna 2021.

**Jaké jsou změny vysoké úrovně rozhraní API pro správu IoT Edge, sady SDK nebo rutiny?**

IoT Edge je doplněk v rámci role Kubernetes, což znamená, že nejprve budete muset zajistit, že Kubernetes je nakonfigurovaný, a pak provést konfiguraci IoT Edge.


## <a name="next-steps"></a>Další kroky

- [Informace o tom, jak použít aktualizace](azure-stack-edge-gpu-install-update.md)

