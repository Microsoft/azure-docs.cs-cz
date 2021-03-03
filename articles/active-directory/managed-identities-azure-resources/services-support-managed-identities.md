---
title: Služby Azure, které podporují spravované identity – Azure AD
description: Seznam služeb, které podporují spravované identity pro prostředky Azure a ověřování Azure AD
services: active-directory
author: barclayn
ms.author: barclayn
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: references_regions
ms.openlocfilehash: 3021e9155e229fcaf52166fd0e9dc908f9b7cfb6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650069"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Služby, které podporují spravované identity prostředků Azure

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou v Azure Active Directory. Pomocí spravované identity se můžete ověřit u jakékoli služby, která podporuje ověřování Azure AD, aniž byste museli mít přihlašovací údaje ve svém kódu. V procesu integruje spravované identity pro prostředky Azure a ověřování Azure AD v Azure. Aktualizace se často kontrolují.

> [!NOTE]
> Spravované identity prostředků Azure jsou novým názvem služby, která se dříve jmenovala Identita spravované služby (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Služby Azure, které podporují spravované identity pro prostředky Azure

Spravované identity pro prostředky Azure podporují následující služby Azure:


### <a name="azure-api-management"></a>Azure API Management

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici | ![K dispozici][check] |
| Přiřazená uživatelem | Preview | Preview | Není k dispozici | Preview |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure API Management (v oblastech, kde je dostupná), podívejte se na následující seznam:

- [Šablona Azure Resource Manageru](../../api-management/api-management-howto-use-managed-service-identity.md)

### <a name="azure-app-configuration"></a>Azure App Configuration

| Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici | ![K dispozici][check] |
| Přiřazená uživatelem | ![K dispozici][check] | ![K dispozici][check]  | Není k dispozici  | ![K dispozici][check] |

Pokud chcete nakonfigurovat spravovanou identitu pro konfiguraci aplikace Azure (v oblastech, kde je dostupná), podívejte se na následující seznam:

- [Azure CLI](../../azure-app-configuration/overview-managed-identity.md)

### <a name="azure-app-service"></a>Azure App Service

| Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | ![K dispozici][check] | ![K dispozici][check] | ![K dispozici][check] | ![K dispozici][check] |
| Přiřazená uživatelem | ![K dispozici][check] | ![K dispozici][check]  | ![K dispozici][check]  | ![K dispozici][check] |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure App Service (v oblastech, kde je dostupná), použijte následující seznam:

- [Azure Portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Šablona Azure Resource Manageru](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes s podporou Azure Arc

| Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | Preview | Není k dispozici | Není k dispozici | Není k dispozici |
| Přiřazená uživatelem | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |

Kubernetes s povoleným ARC Azure aktuálně [podporuje identitu přiřazenou systémem](../../azure-arc/kubernetes/connect-cluster.md#azure-arc-agents-for-kubernetes). Certifikát identity spravované služby používá všechny agenti Kubernetes s podporou ARC Azure pro komunikaci s Azure.

### <a name="azure-automanage"></a>Azure Automanage

| Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | Preview | Není k dispozici | Není k dispozici | Není k dispozici |
| Přiřazená uživatelem | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |

Pokud jste vaše předplatné přesunuli na nového tenanta, přečtěte si následující dokument, který překonfiguruje spravovanou identitu:
* [Opravit poškozený účet automanage](../../automanage/repair-automanage-account.md)

### <a name="azure-blueprints"></a>Azure Blueprints

|Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici | Není k dispozici |
| Přiřazená uživatelem | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici | Není k dispozici |

Pokud chcete použít spravovanou identitu s podrobnými [plány Azure](../../governance/blueprints/overview.md), přečtěte si následující seznam:

- [Azure Portal – přiřazení podrobného plánu](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API – přiřazení podrobného plánu](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici | ![K dispozici][check] |
| Přiřazená uživatelem | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |

### <a name="azure-cognitive-services"></a>Azure Cognitive Services

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici | ![K dispozici][check] |
| Přiřazená uživatelem | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |


### <a name="azure-communication-services"></a>Azure Communication Services

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | ![K dispozici][check] | Není k dispozici | Není k dispozici | Není k dispozici |
| Přiřazená uživatelem | ![K dispozici][check] | Není k dispozici | Není k dispozici | Není k dispozici |


### <a name="azure-container-instances"></a>Azure Container Instances

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | Linux: verze Preview<br>Windows: není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |
| Přiřazená uživatelem | Linux: verze Preview<br>Windows: není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure Container Instances (v oblastech, kde je dostupná), použijte následující seznam:

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Šablona Azure Resource Manageru](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Úlohy Azure Container Registry

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | ![K dispozici][check] | Není k dispozici | Není k dispozici | Není k dispozici |
| Přiřazená uživatelem | Preview | Není k dispozici | Není k dispozici | Není k dispozici |

Pokud chcete nakonfigurovat spravovanou identitu pro úlohy Azure Container Registry (v oblastech, kde je dostupná), použijte následující seznam:

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-data-explorer"></a>Průzkumník dat Azure

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici | ![K dispozici][check] |
| Přiřazená uživatelem | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici | ![K dispozici][check] |
| Přiřazená uživatelem | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure Data Factory v2 (v oblastech, kde je dostupná), použijte následující seznam:

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SADĚ](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-digital-twins"></a>Azure Digital Twins

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | ![K dispozici][check] | Není k dispozici | Není k dispozici | Není k dispozici |
| Přiřazená uživatelem | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |

Pokud chcete nakonfigurovat spravovanou identitu pro digitální vlákna Azure (v oblastech, kde je dostupná), podívejte se na následující seznam:

- [Azure Portal](../../digital-twins/how-to-enable-managed-identities-portal.md)

### <a name="azure-event-grid"></a>Azure Event Grid

Typ spravované identity |Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | Preview | Preview | Není k dispozici | Preview |
| Přiřazená uživatelem | Není k dispozici | Není k dispozici  | Není k dispozici  | Není k dispozici |

### <a name="azure-firewall-policy"></a>Zásady Azure Firewall

Typ spravované identity |Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |
| Přiřazená uživatelem | Preview | Není k dispozici  | Není k dispozici  | Není k dispozici |

### <a name="azure-functions"></a>Azure Functions

Typ spravované identity |Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | ![K dispozici][check] | ![K dispozici][check] | ![K dispozici][check] | ![K dispozici][check] |
| Přiřazená uživatelem | ![K dispozici][check] | ![K dispozici][check]  | ![K dispozici][check]  | ![K dispozici][check]  |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure Functions (v oblastech, kde je dostupná), použijte následující seznam:

- [Azure Portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Šablona Azure Resource Manageru](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Azure IoT Hub

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici | ![K dispozici][check] |
| Přiřazená uživatelem | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure IoT Hub (v oblastech, kde je dostupná), podívejte se na následující seznam:

- [Azure Portal](../../iot-hub/virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

### <a name="azure-importexport"></a>Azure Import/Export

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | --- | --- | --- | --- |
| Přiřazená systémem | K dispozici v oblasti, kde je k dispozici služba Azure import export | Preview | K dispozici | K dispozici |
| Přiřazená uživatelem | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

| Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici | Není k dispozici |
| Přiřazená uživatelem | Preview | Není k dispozici | Není k dispozici | Není k dispozici |


Další informace najdete v tématu [použití spravovaných identit ve službě Azure Kubernetes](../../aks/use-managed-identity.md).


### <a name="azure-logic-apps"></a>Azure Logic Apps

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici | ![K dispozici][check] |
| Přiřazená uživatelem | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici | ![K dispozici][check] |


Pokud chcete nakonfigurovat spravovanou identitu pro Azure Logic Apps (v oblastech, kde je dostupná), použijte následující seznam:

- [Azure Portal](../../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)
- [Šablona Azure Resource Manageru](../../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)

### <a name="azure-machine-learning"></a>Azure Machine Learning

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | Preview | Není k dispozici | Není k dispozici | Není k dispozici |
| Přiřazená uživatelem | Preview | Není k dispozici | Není k dispozici | Není k dispozici |

Další informace najdete v tématu [použití spravovaných identit pomocí Azure Machine Learning](../../machine-learning/how-to-use-managed-identities.md).

### <a name="azure-policy"></a>Azure Policy

|Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | ![K dispozici][check] | ![K dispozici][check] | ![K dispozici][check] | ![K dispozici][check] |
| Přiřazená uživatelem | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure Policy (v oblastech, kde je dostupná), použijte následující seznam:

- [Azure Portal](../../governance/policy/tutorials/create-and-manage.md#assign-a-policy)
- [PowerShell](../../governance/policy/how-to/remediate-resources.md#create-managed-identity-with-powershell)
- [Azure CLI](/cli/azure/policy/assignment#az-policy-assignment-create)
- [Šablony Azure Resource Manageru](/azure/templates/microsoft.authorization/policyassignments)
- [REST](/rest/api/resources/policyassignments/create)


### <a name="azure-service-fabric"></a>Azure Service Fabric

[Spravovaná identita pro aplikace Service Fabric](../../service-fabric/concepts-managed-identity.md) je dostupná ve všech oblastech.

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | ![K dispozici][check] | Není k dispozici | Není k dispozici | není k dispozici |
| Přiřazená uživatelem | ![K dispozici][check] | Není k dispozici | Není k dispozici |Není k dispozici |

Pokud chcete nakonfigurovat spravovanou identitu pro aplikace Service Fabric Azure ve všech oblastech, přečtěte si následující seznam:

- [Šablona Azure Resource Manageru](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

### <a name="azure-spring-cloud"></a>Azure Spring Cloud

| Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | ![K dispozici][check] | Není k dispozici | Není k dispozici | ![K dispozici][check] |
| Přiřazená uživatelem | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |


Další informace najdete v tématu [Povolení spravované identity přiřazené systémem pro aplikaci Azure jaře Cloud](~/articles/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity.md).

### <a name="azure-stack-edge"></a>Azure Stack Edge

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | --- | --- | --- | --- |
| Přiřazená systémem | K dispozici v oblasti, kde je k dispozici služba Azure Stack Edge | Není k dispozici | Není k dispozici | Není k dispozici |
| Přiřazená uživatelem | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |

### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | ![K dispozici][check] | ![K dispozici][check] | ![K dispozici][check] | ![K dispozici][check] |
| Přiřazená uživatelem | ![K dispozici][check] | ![K dispozici][check] | ![K dispozici][check] | ![K dispozici][check] |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure Virtual Machine Scale Sets (v oblastech, kde je dostupná), podívejte se na následující seznam:

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Šablony Azure Resource Manageru](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | ![K dispozici][check] | ![K dispozici][check] | ![K dispozici][check] | ![K dispozici][check] |
| Přiřazená uživatelem | ![K dispozici][check] | ![K dispozici][check] | ![K dispozici][check] | ![K dispozici][check] |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure Virtual Machines (v oblastech, kde je dostupná), podívejte se na následující seznam:

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Šablony Azure Resource Manageru](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)
- [Sady Azure SDK](qs-configure-sdk-windows-vm.md)


### <a name="azure-vm-image-builder"></a>Tvůrce imagí virtuálních počítačů Azure

| Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |
| Přiřazená uživatelem | [K dispozici v podporovaných oblastech](../../virtual-machines/image-builder-overview.md#regions) | Není k dispozici | Není k dispozici | Není k dispozici |

Informace o tom, jak nakonfigurovat spravovanou identitu pro sestavovatele imagí virtuálních počítačů Azure (v oblastech, kde je k dispozici), najdete v tématu [Přehled Tvůrce imagí](../../virtual-machines/image-builder-overview.md#permissions).
### <a name="azure-signalr-service"></a>Služba Azure SignalR

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | Preview | Preview | Není k dispozici | Preview |
| Přiřazená uživatelem | Preview | Preview | Není k dispozici | Preview |

Pokud chcete nakonfigurovat spravovanou identitu pro službu signalizace Azure (v oblastech, kde je dostupná), podívejte se na následující seznam:

- [Šablona Azure Resource Manageru](../../azure-signalr/howto-use-managed-identity.md)

### <a name="azure-resource-mover"></a>Azure Resource Mover

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure (Čína) 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazená systémem | K dispozici v oblastech, kde je služba Azure Resource Stěhovacís k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |
| Přiřazená uživatelem | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |

Pokud chcete používat Azure Resource stěhovací, přečtěte si následující dokument:

- [Azure Resource Mover](../../resource-mover/overview.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Služby Azure, které podporují ověřování Azure AD

Následující služby podporují ověřování Azure AD a byly testovány pomocí služeb klienta, které využívají spravované identity pro prostředky Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Pokud chcete nakonfigurovat přístup k Azure Resource Manager, přečtěte si následující seznam:

- [Přiřazení přístupu přes Azure Portal](howto-assign-access-portal.md)
- [Přiřazení přístupu přes PowerShell](howto-assign-access-powershell.md)
- [Přiřazení přístupu přes rozhraní příkazového řádku Azure](howto-assign-access-CLI.md)
- [Přiřazení přístupu prostřednictvím šablony Azure Resource Manager](../../role-based-access-control/role-assignments-template.md)

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Globální Azure | `https://management.azure.com/`| ![K dispozici][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![K dispozici][check] |
| Azure (Německo) | `https://management.microsoftazure.de/` | ![K dispozici][check] |
| Azure (Čína) 21Vianet | `https://management.chinacloudapi.cn` | ![K dispozici][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Globální Azure | `https://vault.azure.net`| ![K dispozici][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![K dispozici][check] |
| Azure (Německo) |  `https://vault.microsoftazure.de` | ![K dispozici][check] |
| Azure (Čína) 21Vianet | `https://vault.azure.cn` | ![K dispozici][check] |

### <a name="azure-data-lake"></a>Azure Data Lake

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Globální Azure | `https://datalake.azure.net/` | ![K dispozici][check] |
| Azure Government |  | Není k dispozici |
| Azure (Německo) |   | Není k dispozici |
| Azure (Čína) 21Vianet |  | Není k dispozici |

### <a name="azure-sql"></a>Azure SQL

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Globální Azure | `https://database.windows.net/` | ![K dispozici][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![K dispozici][check] |
| Azure (Německo) | `https://database.cloudapi.de/` | ![K dispozici][check] |
| Azure (Čína) 21Vianet | `https://database.chinacloudapi.cn/` | ![K dispozici][check] |

### <a name="azure-data-explorer"></a>Průzkumník dat Azure

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Globální Azure | `https://<account>.<region>.kusto.windows.net` | ![K dispozici][check] |
| Azure Government | `https://<account>.<region>.kusto.usgovcloudapi.net` | ![K dispozici][check] |
| Azure (Německo) | `https://<account>.<region>.kusto.cloudapi.de` | ![K dispozici][check] |
| Azure (Čína) 21Vianet | `https://<account>.<region>.kusto.chinacloudapi.cn` | ![K dispozici][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Globální Azure | `https://eventhubs.azure.net` | ![K dispozici][check] |
| Azure Government |  | Není k dispozici |
| Azure (Německo) |   | Není k dispozici |
| Azure (Čína) 21Vianet |  | Není k dispozici |

### <a name="azure-service-bus"></a>Azure Service Bus

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Globální Azure | `https://servicebus.azure.net`  | ![K dispozici][check] |
| Azure Government |  | ![K dispozici][check] |
| Azure (Německo) |   | Není k dispozici |
| Azure (Čína) 21Vianet |  | Není k dispozici |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage objekty BLOB a fronty

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Globální Azure | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![K dispozici][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![K dispozici][check] |
| Azure (Německo) | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![K dispozici][check] |
| Azure (Čína) 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![K dispozici][check] |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Globální Azure | `https://*.asazure.windows.net` | ![K dispozici][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![K dispozici][check] |
| Azure (Německo) | `https://*.asazure.cloudapi.de` | ![K dispozici][check] |
| Azure (Čína) 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![K dispozici][check] |

> [!Note]
> Microsoft Power BI také [podporuje spravované identity](../../stream-analytics/powerbi-output-managed-identity.md).


[check]: media/services-support-managed-identities/check.png "K dispozici"