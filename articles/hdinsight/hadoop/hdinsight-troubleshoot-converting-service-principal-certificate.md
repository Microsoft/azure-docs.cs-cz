---
title: Převádění obsahu certifikátu na Base-64 – Azure HDInsight
description: Převod obsahu certifikátu instančního objektu na formát řetězce s kódováním Base-64 ve službě Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 0ee2e783a7a9443db4c96817cf611272667c675a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944407"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>Převod obsahu certifikátu instančního objektu na formát řetězce s kódováním Base-64 ve službě HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Zobrazí se chybová zpráva oznamující, že vstup není platný řetězec Base-64, protože obsahuje znak, který není ze základního 64, je více než dva znaky výplně nebo neprázdný znak mezi znaky výplně.

## <a name="cause"></a>Příčina

Když pomocí PowerShellu nebo nasazení šablony Azure vytvoříte clustery s Data Lake jako primární nebo další úložiště, obsah certifikátu instančního objektu, který se zadal pro přístup k účtu úložiště Data Lake, je ve formátu Base-64. Nevhodný převod obsahu certifikátu PFX na řetězec zakódovaný v základní-64 může způsobit tuto chybu.

## <a name="resolution"></a>Řešení

Jakmile budete mít certifikát instančního objektu ve formátu PFX ( [tady](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) najdete pokyny k vytvoření ukázkového objektu služby), použijte následující příkaz PowerShellu nebo fragment kódu jazyka C# k převedení obsahu certifikátu do formátu base-64.

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]