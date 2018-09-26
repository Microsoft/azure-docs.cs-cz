---
title: Výchozí velikost složky TEMP je pro roli příliš malá. | Dokumentace Microsoftu
description: Role cloudové služby má omezené množství místa pro dočasné složky. Tento článek obsahuje několik návrhů na tom, jak zamezit spuštění volné místo.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 5d60c5f2d759f3329997762270e9a0335ebbe1e9
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47095961"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Výchozí velikost složky TEMP je příliš malá v roli cloudové služby web/worker
Výchozí dočasný adresář role pracovního procesu nebo webové služby cloud má maximální velikost 100 MB, což může dojít v určitém okamžiku. Tento článek popisuje, jak se vyhnout nedostatku místa pro dočasný adresář.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Proč spouštění mimo místo
Standardní Windows proměnné prostředí TEMP a TMP jsou k dispozici pro kód, který běží ve vaší aplikaci. TEMP a TMP odkazovat na jeden adresář, který má maximální velikost 100 MB. Všechna data, která je uložena v tomto adresáři není zachován po životního cyklu cloudových služeb; Pokud jsou recyklovány instance rolí v cloudové službě, Probíhá čištění adresáře.

## <a name="suggestion-to-fix-the-problem"></a>Návrh na tento problém vyřešit
Implementujte jedno z následujících alternativy:

* Konfigurace prostředků místního úložiště a k němu přístup přímo namísto použití TEMP a TMP. Chcete-li přistupovat k prostředku úložiště Místní kód, který běží v rámci vaší aplikace, zavolejte [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) metody.
* Konfiguraci prostředku Místní úložiště a přejděte adresáře TEMP a TMP tak, aby odkazoval na cestu prostředku Místní úložiště. Tato změna by měl provádět v rámci [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) metody.

Následující příklad kódu ukazuje, jak upravit cílového adresáře TEMP a TMP z v rámci metody operace OnStart:

```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>Další postup
Přečtěte si blog, který popisuje [jak zvětšit velikost Azure Web Role dočasné složky ASP.NET](http://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Zobrazit další [článků o řešení potíží](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/vs-azure-tools-debugging-cloud-services-overview.md) pro cloudové služby.

Zjistěte, jak řešit problémy s cloudovou službou role pomocí Azure PaaS počítače diagnostická data, zobrazit [Kevin Williamson blogovou sérii](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
