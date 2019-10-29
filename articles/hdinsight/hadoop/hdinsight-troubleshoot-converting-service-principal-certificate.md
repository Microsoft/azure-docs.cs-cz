---
title: Převádění obsahu certifikátu na Base-64 – Azure HDInsight
description: Převod obsahu certifikátu instančního objektu na formát řetězce s kódováním Base-64 ve službě Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: ad41ef464f592fd09b070ab6f715e9a07fb3cf21
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044495"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>Převod obsahu certifikátu instančního objektu na formát řetězce s kódováním Base-64 ve službě HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Zobrazí se chybová zpráva oznamující, že vstup není platný řetězec Base-64, protože obsahuje znak, který není ze základního 64, je více než dva znaky výplně nebo neprázdný znak mezi znaky výplně.

## <a name="cause"></a>Příčina

Když pomocí PowerShellu nebo nasazení šablony Azure vytvoříte clustery s Data Lake jako primární nebo další úložiště, obsah certifikátu instančního objektu, který se zadal pro přístup k účtu úložiště Data Lake, je ve formátu Base-64. Nevhodný převod obsahu certifikátu PFX na řetězec zakódovaný v základní-64 může způsobit tuto chybu.

## <a name="resolution"></a>Rozlišení

Jakmile budete mít certifikát instančního objektu ve formátu PFX ( [tady](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) najdete postup vytvoření ukázkového objektu služby), použijte následující příkaz nebo C# fragment prostředí PowerShell k převedení obsahu certifikátu na formát Base-64.

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

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) – oficiální Microsoft Azure účet pro zlepšení prostředí pro zákazníky tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
