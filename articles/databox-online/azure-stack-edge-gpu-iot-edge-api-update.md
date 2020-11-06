---
title: Azure Stack Edge 2021. dopad na aktualizaci | Microsoft Docs
description: Popisuje dopad IoT Edge správy rolí v zařízeních Azure Stack Edge po instalaci aktualizace z ledna 2021.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 10/26/2020
ms.author: alkohli
ms.openlocfilehash: 4b54f75b7d90e4b3a0a11d2ecdc676bb48eeee99
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335678"
---
# <a name="iot-edge-role-management-changes-for-your-azure-stack-edge"></a>IoT Edge změny správy rolí pro Azure Stack Edge

Pro IoT Edge správu rolí pro zařízení Azure Stack Edge použijte nejnovější verzi rozhraní API, sady SDK a Azure PowerShell z důvodu vydání v lednu 2021. Tento článek podrobně popisuje změny požadované při použití nejnovější verze rutin rozhraní API, sady SDK a prostředí PowerShell pro IoT Edge správu rolí na hraničních Azure Stackch.

Aktualizace od ledna 2021 je k dispozici pouze pro Azure Stack hraniční zařízení pro-GPU, Azure Stack Edge pro R a Azure Stack hraničních Mini R a informace v tomto článku se vztahují pouze na tato zařízení. 

## <a name="iot-edge-role-management-changes"></a>IoT Edge změny správy rolí

Po instalaci volitelné verze softwaru zařízení z ledna 2021 na zařízení Azure Stack Edge budete muset pro IoT Edge správu rolí použít nejnovější verzi rutin rozhraní API, sady SDK a prostředí PowerShell.

- Pokud používáte rozhraní API pro správu rolí verze 2019-08-01, upgradujte na verzi rozhraní API vydanou v lednu 2021. 
- Pokud používáte správu rolí prostřednictvím sady SDK verze 1.0.0, upgradujte na verzi vydanou v lednu 2021.
- Pokud používáte správu rolí s rutinami Azure PowerShell (Preview), jako je `Get-AzStackEdgeRole` ,, `New-AzStackEdgeRole` `Set-AzStackEdgeRole` nebo, budete `Remove-AzStackEdgeRole` muset počkat na nové rutiny, které budou vydané v únoru 2021.

Výše uvedené změny jsou požadovány pouze v případě, že je použita aktualizace ledna 2021. Pokud zůstanete na stávající verzi softwaru zařízení, nebudete mít žádný vliv na IoT Edge správu rolí. Doporučujeme však, abyste si v zařízení aktualizovali nové funkce a vylepšení zabezpečení. 


## <a name="api-usage"></a>Použití rozhraní API

Pokud jste prováděli IoT Edge správu rolí prostřednictvím rozhraní API, měli byste použít novou verzi rozhraní API (2020-12-01), která se bude publikovat později. Pokud používáte aktuální rozhraní API role a máte nainstalovanou verzi nadcházejícího softwaru, musíte přejít do role PUT, GET, DELETE Kubernetes a pak vložit rozhraní API pro addon addon.


### <a name="for-put-method"></a>Pro metodu PUT

#### <a name="current-http-request"></a>Aktuální požadavek http 

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

#### <a name="upcoming-http-request"></a>Nadcházející požadavek http 

- Volání rozhraní API pro roli Kubernetes jsou provedena na následujícím identifikátoru URI: ' https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1?api-version=2020-12-01 '

    Text žádosti vypadá takto:

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

- Volání rozhraní API pro doplněk IoT Edge jsou provedena na následujícím identifikátoru URI: ' https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01 '


    Text žádosti vypadá takto:

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


### <a name="for-get-method"></a>Pro metodu GET

#### <a name="current-http-response"></a>Aktuální odpověď http

- Volání rozhraní API se provádí na následujícím identifikátoru URI: ' https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01 '


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

#### <a name="upcoming-http-response"></a>Nadcházející odpověď http

- Volání rozhraní API se provádí na následujícím identifikátoru URI: ' https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01 '
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

### <a name="for-delete-method"></a>Pro metodu DELETE

### <a name="current"></a>Current

Volání rozhraní API se provádí na následujícím identifikátoru URI: ' https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01 '

### <a name="upcoming"></a>Nadcházející

Volání rozhraní API se provádí na následujícím identifikátoru URI: ' https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01 '


## <a name="sdk-usage"></a>Využití sady SDK

Pokud používáte sadu SDK a nainstalovali jste aktualizaci zařízení z ledna 2021, musíte změnit způsob, jakým jste nastavili roli IoT Edge, jak je znázorněno v následující tabulce. Pak byste si měli stáhnout a nainstalovat nadcházející balíček NuGet, který se přesune na novou sadu SDK, jak je znázorněno v následující ukázce.

### <a name="current-sdk-sample"></a>Ukázka aktuální sady SDK

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


### <a name="new-sdk-sample"></a>Ukázka nové sady SDK

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

Pokud používáte `Get-AzStackEdgeRole` `New-AzStackEdgeRole` rutiny,, `Set-AzStackEdgeRole` , nebo `Remove-AzStackEdgeRole` , musíte počkat na novou verzi, která je naplánovaná v únoru 2021.

## <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy

#### <a name="i-am-using-azure-stack-edge-pro--fpga-does-the-january-2021-update-affect-the-fpga-model"></a>Používám Azure Stack Edge pro – FPGA. Ovlivňuje aktualizace z ledna 2021 model FPGA?

Ne. Aktualizace z ledna 2021 se vztahuje pouze na Azure Stack Edge – GPU, Azure Stack Edge pro R a na Azure Stack hraničních Mini R zařízeních. Azure Stack Edge pro – FPGA není touto aktualizací ovlivněná a nevyžaduje žádnou změnu pro IoT Edge správu rolí.

#### <a name="after-i-update-my-azure-stack-edge-pro---gpu-to-the-new-device-software-in-january-2021-are-any-of-the-existing-services-affected"></a>Po aktualizaci Azure Stack Edge pro-GPU na nový software zařízení v lednu 2021 jsou nějaké stávající služby zasažené?

Ne. Nakonfigurované služby nebudou ovlivněny po instalaci aktualizace zařízení z ledna 2021. <!--check w/ Anoob, what existing services you are talking about in this question-->

#### <a name="what-are-the-high-level-changes-to-iot-edge-management-api-sdk-or-cmdlet"></a>Jaké jsou změny vysoké úrovně IoT Edge rozhraní API pro správu, sady SDK nebo rutiny?

IoT Edge je doplněk v rámci role Kubernetes. To znamená, že budete muset ověřit, že je nejdřív nakonfigurovaný Kubernetes a pak provedete konfiguraci IoT Edge.


## <a name="next-steps"></a>Další kroky

- Naučte se [instalovat aktualizace](azure-stack-edge-gpu-install-update.md).

