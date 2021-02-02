---
title: Ukázky Azure PowerShell – obnovení Azure Cloud Services (Rozšířená podpora)
description: Ukázkové skripty pro obnovení nasazení cloudové služby Azure (Rozšířená podpora)
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 5c43d61b1e7cd98674eab4c6d857cc1114a06013
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475316"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>Resetování cloudové služby Azure (Rozšířená podpora) 
Tyto ukázky zahrnují různé způsoby obnovení stávajícího nasazení cloudové služby Azure (Rozšířená podpora).

## <a name="reimage-role-instances-of-cloud-service"></a>Obnovení instancí role cloudové služby z Image
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Tento příkaz přeimagí 2 instance rolí ContosoFrontEnd_IN_0 a ContosoBackEnd_IN_1 cloudové služby s názvem ContosoCS, která patří do skupiny prostředků s názvem ContosOrg.

## <a name="reimage-all-roles-of-cloud-service"></a>Obnovení imagí všech rolí cloudové služby
```powershell
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Tento příkaz obnoví z image všechny instance rolí cloudové služby s názvem ContosoCS, které patří do skupiny prostředků s názvem ContosOrg.

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>Obnovení jedné instance role v cloudové službě
```powershell
Invoke-AzCloudServiceRoleInstanceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0"
```
Tento příkaz obnoví instanci role s názvem ContosoFrontEnd_IN_0 cloudové služby s názvem ContosoCS, která patří do skupiny prostředků s názvem ContosOrg.

## <a name="rebuild-role-instances-of-cloud-service"></a>Opětovné sestavení instancí rolí cloudové služby
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Tento příkaz znovu sestaví 2 instance rolí ContosoFrontEnd_IN_0 a ContosoBackEnd_IN_1 cloudové služby s názvem ContosoCS, která patří do skupiny prostředků s názvem ContosOrg.

## <a name="rebuild-all-roles-of-cloud-service"></a>Opětovné sestavení všech rolí cloudové služby
```powershell
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Tento příkaz znovu sestaví všechny instance rolí cloudové služby s názvem ContosoCS, které patří do skupiny prostředků s názvem ContosOrg.

## <a name="restart-role-instances-of-cloud-service"></a>Restartovat instance rolí cloudové služby
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Tento příkaz restartuje 2 instance rolí ContosoFrontEnd_IN_0 a ContosoBackEnd_IN_1 cloudové služby s názvem ContosoCS, která patří do skupiny prostředků s názvem ContosOrg.

## <a name="restart-all-roles-of-cloud-service"></a>Restartujte všechny role cloudové služby.
```powershell
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Tento příkaz restartuje všechny instance rolí cloudové služby s názvem ContosoCS, které patří do skupiny prostředků s názvem ContosOrg.

## <a name="next-steps"></a>Další kroky

- Další informace o službě Azure Cloud Services (Rozšířená podpora) najdete v tématu [Přehled azure Cloud Services (Rozšířená podpora)](overview.md).
- Navštivte [úložiště ukázek Cloud Services (rozšířené podpory)](https://github.com/Azure-Samples/cloud-services-extended-support) .
