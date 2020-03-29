---
title: Výchozí velikost složky TEMP je pro roli příliš malá | Dokumenty společnosti Microsoft
description: Role cloudové služby má omezené množství místa pro složku TEMP. Tento článek obsahuje některé návrhy, jak se vyhnout vyčerpání místa.
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
ms.openlocfilehash: 0b869b73a79872d9263058bedfead018e18721c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71154988"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Výchozí velikost složky TEMP je příliš malá na webové roli/pracovního procesu cloudové služby.
Výchozí dočasný adresář pracovníka cloudové služby nebo webové role má maximální velikost 100 MB, která může být v určitém okamžiku plná. Tento článek popisuje, jak se vyhnout vyčerpání místa pro dočasný adresář.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Proč mi dojdou místa?
Standardní proměnné prostředí systému Windows TEMP a TMP jsou k dispozici pro kód, který je spuštěn ve vaší aplikaci. Temp i TMP odkazují na jeden adresář, který má maximální velikost 100 MB. Všechna data uložená v tomto adresáři nejsou v průběhu celého životního cyklu cloudové služby zachována. Pokud jsou instance rolí v cloudové službě recyklovány, adresář se vyčistí.

## <a name="suggestion-to-fix-the-problem"></a>Návrh na vyřešení problému
Implementujte jednu z následujících alternativ:

* Nakonfigurujte prostředek místního úložiště a získejte k němu přímý přístup namísto použití TEMP nebo TMP. Chcete-li získat přístup k prostředku místního úložiště z kódu, který je spuštěn v rámci vaší aplikace, volejte metodu [RoleEnvironment.GetLocalResource.](/previous-versions/azure/reference/ee772845(v=azure.100))
* Nakonfigurujte prostředek místního úložiště a namiřte adresáře TEMP a TMP tak, aby ukazovaly na cestu k prostředku místního úložiště. Tato změna by měla být provedena v rámci [Metody RoleEntryPoint.OnStart.](/previous-versions/azure/reference/ee772851(v=azure.100))

Následující příklad kódu ukazuje, jak upravit cílové adresáře pro TEMP a TMP z metody OnStart:

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
Přečtěte si blog, který [popisuje, jak zvětšit velikost role webu Azure ASP.NET dočasná složka](https://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Zobrazit další [články o řešení potíží](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/vs-azure-tools-debugging-cloud-services-overview.md) pro cloudové služby.

Chcete-li se dozvědět, jak řešit problémy s rolí cloudové služby pomocí dat diagnostiky počítače Azure PaaS, podívejte se na [řadu blogů Kevina Williamsona](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
