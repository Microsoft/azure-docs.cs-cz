---
title: Výchozí velikost složky TEMP je pro roli příliš malá. Microsoft Docs
description: Role cloudové služby má omezené množství místa pro dočasnou složku. Tento článek poskytuje některé návrhy, jak zabránit nedostatku místa.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 77c4f627668b91c4bc21ad73b3511c1775943229
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89460197"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Výchozí velikost složky TEMP je pro web nebo roli pracovního procesu cloudové služby moc malá.
Výchozí dočasný adresář pracovního procesu cloudové služby nebo webové role má maximální velikost 100 MB, která může být v určitém okamžiku plná. Tento článek popisuje, jak se vyhnout nedostatku místa pro dočasný adresář.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Proč mám dostatek místa?
Standardní proměnné prostředí Windows TEMP a TMP jsou k dispozici pro kód, který běží ve vaší aplikaci. Dočasná i TMP ukazují na jeden adresář, který má maximální velikost 100 MB. Data, která jsou uložená v tomto adresáři, se neukládají v životním cyklu cloudové služby. Pokud dojde k recyklování instancí rolí v cloudové službě, vyčistí se adresář.

## <a name="suggestion-to-fix-the-problem"></a>Návrh k vyřešení problému
Implementujte jednu z následujících možností:

* Nakonfigurujte místní prostředek úložiště a k němu přistoupit přímo místo pomocí TEMP nebo TMP. Pro přístup k místnímu prostředku úložiště z kódu, který běží v rámci aplikace, zavolejte metodu [RoleEnvironment. GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) .
* Nakonfigurujte prostředek místního úložiště a nasměrujte adresáře TEMP a TMP tak, aby odkazovaly na cestu k místnímu prostředku úložiště. Tato úprava by se měla provádět v rámci metody [RoleEntryPoint. OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) .

Následující příklad kódu ukazuje, jak v rámci metody OnStart upravit cílové adresáře pro dočasné a TMP:

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

## <a name="next-steps"></a>Další kroky
Přečtěte si blog, který popisuje, [jak zvýšit velikost ASP.NET dočasné složky webové role Azure](https://docs.microsoft.com/archive/blogs/kwill/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder).

Podívejte se na další články týkající se [řešení potíží](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-debugging-cloud-services-overview) pro Cloud Services.

Pokud se chcete dozvědět, jak řešit problémy s rolí cloudové služby pomocí dat diagnostiky počítačů Azure PaaS, podívejte se na [blogovou řadu Kevin Williamson](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).
