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
ms.openlocfilehash: 40b44fd277eac14a5bf2c15f58fccfd9d5b156c4
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881481"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>Resetování cloudové služby Azure (Rozšířená podpora) 
Tyto ukázky zahrnují různé způsoby obnovení stávajícího nasazení cloudové služby Azure (Rozšířená podpora).

## <a name="reimage-role-instances-of-cloud-service"></a>Obnovení instancí role cloudové služby z Image
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances -Reimage
```
Tento příkaz obnoví z image 2 instance rolí **ContosoFrontEnd \_ v \_ 0** a **ContosoBackEnd \_ v \_ 1** cloudové službě s názvem ContosoCS, která patří do skupiny prostředků s názvem ContosOrg.

## <a name="reimage-all-roles-of-cloud-service"></a>Obnovení imagí všech rolí cloudové služby
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Reimage
```

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>Obnovení jedné instance role v cloudové službě
```powershell
Reset-AzCloudServiceRoleInstance -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0" -Reimage
```

## <a name="restart-a-single-role-instance-of-a-cloud-service"></a>Restartování jedné instance role cloudové služby
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Restart
```

## <a name="next-steps"></a>Další kroky

- Další informace o službě Azure Cloud Services (Rozšířená podpora) najdete v tématu [Přehled azure Cloud Services (Rozšířená podpora)](overview.md).
- Navštivte [úložiště ukázek Cloud Services (rozšířené podpory)](https://github.com/Azure-Samples/cloud-services-extended-support) .