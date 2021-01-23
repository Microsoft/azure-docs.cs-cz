---
title: Použití sady Visual Studio, povolení vzdálené plochy pro roli (Azure Cloud Services Classic)
description: Konfigurace aplikace cloudové služby Azure tak, aby povolovala připojení ke vzdálené ploše
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ad95cefbdf839c28b0979b051e217a1dfec76eea
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743232"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic-using-visual-studio"></a>Povolení Připojení ke vzdálené ploše pro roli v Azure Cloud Services (Classic) pomocí sady Visual Studio

> [!IMPORTANT]
> [Azure Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md) je nový model nasazení založený na Azure Resource Manager pro produkt Azure Cloud Services.V důsledku této změny se Azure Cloud Services běžící na modelu nasazení založeném na Azure Service Manager přejmenovala jako Cloud Services (Classic) a všechna nová nasazení by měla používat [Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md).

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Vzdálená plocha umožňuje přístup k ploše role spuštěné v Azure. Připojení ke vzdálené ploše můžete použít k řešení potíží a diagnostikování problémů s aplikací v době, kdy je spuštěná.

Průvodce publikováním, který Visual Studio poskytuje pro cloudové služby, zahrnuje možnost povolit vzdálenou plochu během procesu publikování s použitím přihlašovacích údajů, které zadáte. Použití této možnosti je vhodné při použití sady Visual Studio 2017 verze 15,4 a starší.

Se sadou Visual Studio 2017 verze 15,5 a novější se však doporučuje, abyste zabránili povolování vzdálené plochy prostřednictvím Průvodce publikováním, pokud nepracujete pouze jako jediný vývojář. V případě situace, kdy může být projekt otevřen jinými vývojáři, je místo toho možné povolit vzdálenou plochu prostřednictvím Azure Portal, prostřednictvím PowerShellu nebo z kanálu vydání v pracovním postupu průběžného nasazování. Toto doporučení je způsobeno změnou způsobu, jakým aplikace Visual Studio komunikuje se vzdálenou plochou na VIRTUÁLNÍm počítači cloudové služby, jak je vysvětleno v tomto článku.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Konfigurace vzdálené plochy prostřednictvím sady Visual Studio 2017 verze 15,4 a starší

Při použití sady Visual Studio 2017 verze 15,4 a starší můžete použít možnost **Povolit vzdálenou plochu pro všechny role** v Průvodci publikováním. Průvodce můžete použít i v systému Visual Studio 2017 verze 15,5 a novější, ale nepoužívejte možnost Vzdálená plocha.

1. V aplikaci Visual Studio spusťte Průvodce publikováním tak, že kliknete pravým tlačítkem na projekt cloudové služby v Průzkumník řešení a kliknete na **publikovat**.

2. V případě potřeby se přihlaste k předplatnému Azure a vyberte **Další**.

3. Na stránce **Nastavení** vyberte **Povolit vzdálenou plochu pro všechny role** a pak vyberte odkaz **nastavení...** . otevře se dialogové okno **Konfigurace vzdálené plochy** .

4. V dolní části dialogového okna vyberte **Další možnosti**. Tento příkaz zobrazí rozevírací seznam, ve kterém můžete vytvořit nebo zvolit certifikát, abyste při připojování přes vzdálenou plochu mohli šifrovat informace o přihlašovacích údajích.

   > [!Note]
   > Certifikáty, které potřebujete pro připojení ke vzdálené ploše, se liší od certifikátů, které používáte pro jiné operace Azure. Certifikát vzdáleného přístupu musí mít privátní klíč.

5. Vyberte certifikát ze seznamu nebo zvolte **&lt; vytvořit... &gt;**. Při vytváření nového certifikátu zadejte po zobrazení výzvy popisný název nového certifikátu a vyberte **OK**. Nový certifikát se zobrazí v rozevíracím seznamu.

6. Zadejte uživatelské jméno a heslo. Existující účet nemůžete použít. Jako uživatelské jméno nového účtu nepoužívejte "správce".

7. Vyberte datum, kdy vyprší platnost účtu, a po kterém budou připojení ke vzdálené ploše blokovaná.

8. Až zadáte všechny požadované informace, vyberte **OK**. Visual Studio přidá nastavení vzdálené plochy do `.cscfg` souborů projektu a `.csdef` , včetně hesla zašifrovaného pomocí vybraného certifikátu.

9. Pomocí tlačítka **Další** dokončete všechny zbývající kroky a pak vyberte **publikovat** , až budete připraveni publikovat cloudovou službu. Pokud nejste připraveni k publikování, vyberte **Zrušit** a po zobrazení výzvy k uložení změn odpovězte na **Ano** . Pomocí těchto nastavení můžete cloudovou službu publikovat později.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Konfigurace vzdálené plochy při použití sady Visual Studio 2017 verze 15,5 a novější

Pomocí sady Visual Studio 2017 verze 15,5 a novější můžete i nadále používat Průvodce publikováním v projektu cloudové služby. Pokud pracujete jenom jako jediný vývojář, můžete použít taky možnost **Povolit vzdálenou plochu pro všechny role** .

Pokud pracujete jako součást týmu, měli byste místo toho povolit vzdálenou plochu v cloudové službě Azure pomocí [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md) nebo [PowerShellu](cloud-services-role-enable-remote-desktop-powershell.md).

Toto doporučení je způsobeno změnou způsobu, jakým Visual Studio 2017 verze 15,5 a novější komunikuje s virtuálním počítačem cloudové služby. Při povolování vzdálené plochy pomocí Průvodce publikováním můžou předchozí verze sady Visual Studio komunikovat s virtuálním počítačem přes to, co se nazývá modul plug-in RDP. Visual Studio 2017 verze 15,5 a novější komunikuje místo toho pomocí rozšíření RDP, které je bezpečnější a flexibilnější. Tato změna se taky zarovnává se skutečností, že Azure Portal a PowerShellové metody pro povolení vzdálené plochy používají také rozšíření RDP.

Když aplikace Visual Studio komunikuje s rozšířením RDP, přenáší heslo typu prostého textu přes protokol TLS. Konfigurační soubory projektu však ukládají pouze šifrované heslo, které lze dešifrovat pouze v případě prostého textu s místním certifikátem, který byl původně použit k zašifrování.

Pokud projekt cloudové služby nasadíte ze stejného vývojového počítače pokaždé, pak je tento místní certifikát k dispozici. V tomto případě můžete i nadále používat možnost **Povolit vzdálenou plochu pro všechny role** v Průvodci publikováním.

Pokud ale vy nebo jiní vývojáři chcete nasadit projekt cloudové služby z různých počítačů, pak tyto ostatní počítače nebudou mít potřebný certifikát k dešifrování hesla. V důsledku toho se zobrazí následující chybová zpráva:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

Heslo můžete změnit při každém nasazení cloudové služby, ale tato akce je nevhodná pro všechny uživatele, kteří potřebují používat vzdálenou plochu.

Pokud sdílíte projekt s týmem, je vhodné zrušit možnost v Průvodci publikováním a místo toho povolit vzdálenou plochu přímo prostřednictvím [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md) nebo pomocí [prostředí PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Nasazení ze serveru sestavení pomocí sady Visual Studio 2017 verze 15,5 a novější

Projekt cloudové služby lze nasadit ze serveru sestavení (například pomocí Azure DevOps Services), ve kterém je nainstalována aplikace Visual Studio 2017 verze 15,5 nebo novější v agentovi sestavení. V tomto uspořádání probíhá nasazení ze stejného počítače, ve kterém je k dispozici šifrovací certifikát.

Pokud chcete použít rozšíření RDP z Azure DevOps Services, zahrňte do svého kanálu sestavení následující podrobnosti:

1. Zahrňte `/p:ForceRDPExtensionOverPlugin=true` do argumentů MSBuild, abyste se ujistili, že nasazení funguje s rozšířením RDP a nikoli s modulem plug-in RDP. Například:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. Po dokončení postupu sestavení přidejte krok **nasazení cloudové služby Azure** a nastavte jeho vlastnosti.

1. Po kroku nasazení přidejte krok **Azure PowerShellu** , nastavte jeho vlastnost **zobrazované jméno** na "nasazení Azure: Povolit rozšíření RDP" (nebo jiný vhodný název) a vyberte příslušné předplatné Azure.

1. Nastavte **typ skriptu** na "inline" a vložte níže uvedený kód do **vloženého pole skriptu** . (V projektu můžete také vytvořit `.ps1` soubor pomocí tohoto skriptu, nastavit **typ skriptu** na "cesta k souboru skriptu" a nastavit **cestu skriptu** tak, aby odkazovala na soubor.)

    ```ps
    Param(
        [Parameter(Mandatory=$True)]
        [string]$username,

        [Parameter(Mandatory=$True)]
        [string]$password,

        [Parameter(Mandatory=$True)]
        [string]$serviceName,

        [Datetime]$expiry = ($(Get-Date).AddYears(1))
    )

    Write-Host "Service Name: $serviceName"
    Write-Host "User Name: $username"
    Write-Host "Expiry: $expiry"

    $securepassword = ConvertTo-SecureString -String $password -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential $username,$securepassword

    # Try to remote existing RDP Extensions
    try
    {
        $existingRDPExtension = Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
        if ($existingRDPExtension -ne $null)
        {
            Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
        }
    }
    catch
    {
    }

    Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry -Verbose
    ```

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Připojení k roli Azure pomocí vzdálené plochy

Po publikování cloudové služby v Azure a zapnutí vzdálené plochy můžete použít Visual Studio Průzkumník serveru pro přihlášení k virtuálnímu počítači cloudové služby:

1. V Průzkumník serveru rozbalte uzel **Azure** a potom rozbalte uzel pro cloudovou službu a jednu z jejích rolí pro zobrazení seznamu instancí.

2. Klikněte pravým tlačítkem myši na uzel instance a vyberte **připojit pomocí vzdálené plochy**.

3. Zadejte uživatelské jméno a heslo, které jste vytvořili dříve. Nyní jste přihlášeni ke vzdálené relaci.

## <a name="additional-resources"></a>Další zdroje informací

[Jak konfigurovat Cloud Services](cloud-services-how-to-configure-portal.md)
