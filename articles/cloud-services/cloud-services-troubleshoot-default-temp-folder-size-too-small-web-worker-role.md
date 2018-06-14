---
title: Velikost výchozí dočasné složky je příliš malá pro role | Microsoft Docs
description: Cloudové služby role má omezené množství místa pro dočasné složky. Tento článek obsahuje některé návrhy na tom, jak zamezit nedostatku místa.
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
ms.date: 11/03/2017
ms.author: v-six
ms.openlocfilehash: 177438c4d6b416584f1797ec1ce744fc484ef688
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32154524"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Velikost výchozí dočasné složky je příliš malá v roli web nebo worker cloudové služby
Dočasný adresář výchozí role pracovního procesu nebo webové služby cloud má maximální velikost 100 MB, který může dojít k úplné v určitém okamžiku. Tento článek popisuje, jak se vyhnout nedostatku místa pro tento dočasný adresář.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Proč dochází volné místo?
Standardní proměnné prostředí systému Windows TEMP a TMP jsou k dispozici pro kód, který běží ve vaší aplikaci. TEMP a TMP bodu do jednoho adresáře, který má maximální velikost 100 MB. Žádná data, která je uložená v tomto adresáři není zachován po životního cyklu cloudové služby; Pokud jsou recyklovány instance rolí v cloudové službě, je vyčistit adresář.

## <a name="suggestion-to-fix-the-problem"></a>Návrh na opravě problému
Implementujte jednu z následujících alternativních:

* Nakonfigurujte místní úložiště prostředků a k němu přístup přímo místo použití TEMP a TMP. Chcete-li získat přístup k prostředku Místní úložiště z kódu, který běží v rámci vaší aplikace, zavolejte [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) metoda.
* Nakonfigurujte místní úložiště prostředků a bodu adresáře TEMP a TMP tak, aby odkazoval na cestu prostředků místní úložiště. Tato úprava měla v rámci [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) metoda.

Následující příklad kódu ukazuje, jak můžete upravit cíl adresáře TEMP a TMP z v rámci metody OnStart:

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
Přečtěte si blog, který popisuje [jak zvětšit velikost Azure webovou roli ASP.NET dočasnou složku](http://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Zobrazení [řešení potíží s články](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/vs-azure-tools-debugging-cloud-services-overview.md) pro cloudové služby.

Informace o tom potíží cloudové služby role pomocí Azure PaaS počítače diagnostická data, zobrazit [řady blogu kevina Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
