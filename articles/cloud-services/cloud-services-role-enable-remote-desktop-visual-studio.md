---
title: Pomocí Visual Studia povolte vzdálenou plochu pro roli (Azure Cloud Services)
description: Jak nakonfigurovat aplikaci cloudové služby Azure tak, aby umožňovala připojení ke vzdálené ploše
services: cloud-services
author: ghogen
manager: jillfra
ms.assetid: f5727ebe-9f57-4d7d-aff1-58761e8de8c1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.workload: azure-vs
ms.date: 03/06/2018
ms.author: ghogen
ms.openlocfilehash: f4622e44c795182ee68c617f335c9e1651d3adcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294394"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-visual-studio"></a>Povolení připojení ke vzdálené ploše pro roli v Cloudových službách Azure pomocí Sady Visual Studio

> [!div class="op_single_selector"]
> * [Portál Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Vzdálená plocha umožňuje přístup k ploše role spuštěné v Azure. Připojení ke vzdálené ploše můžete použít k řešení a diagnostice problémů s aplikací, když je spuštěna.

Průvodce publikováním, který sada Visual Studio poskytuje pro cloudové služby, obsahuje možnost povolit vzdálenou plochu během procesu publikování pomocí pověření, která zadáte. Použití této možnosti je vhodné při použití Visual Studio 2017 verze 15.4 a starší.

S Visual Studio 2017 verze 15.5 a novější, ale doporučujeme vyhnout se povolení vzdálené plochy prostřednictvím průvodce publikováním, pokud pracujete pouze jako jeden vývojář. Pro každou situaci, ve které může být projekt otevřen jinými vývojáři, místo toho povolíte vzdálenou plochu prostřednictvím portálu Azure, prostřednictvím prostředí PowerShell nebo z kanálu vydání v pracovním postupu průběžného nasazení. Toto doporučení je způsobeno změnou ve způsobu, jakým Visual Studio komunikuje se vzdálenou plochou na virtuálním počítači cloudové služby, jak je vysvětleno v tomto článku.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Konfigurace vzdálené plochy prostřednictvím sady Visual Studio 2017 verze 15.4 a starší

Při použití Visual Studia 2017 verze 15.4 a starší, můžete použít **možnost Povolit vzdálenou plochu pro všechny role** v průvodci publikováním. Průvodce můžete dál používat v sadě Visual Studio 2017 verze 15.5 a novějších, ale nepoužívejte možnost Vzdálená plocha.

1. V sadě Visual Studio spusťte průvodce publikováním tak, že kliknete pravým tlačítkem myši na projekt cloudové služby v Průzkumníku řešení a zvolíte **Publikovat**.

2. V případě potřeby se přihlaste k předplatnému Azure a vyberte **Další**.

3. Na stránce **Nastavení** vyberte **Povolit vzdálenou plochu pro všechny role**a pak vyberte odkaz **Nastavení...** a otevřete dialogové okno **Konfigurace vzdálené plochy.**

4. V dolní části dialogového okna vyberte **Další možnosti**. Tento příkaz zobrazí rozevírací seznam, ve kterém vytvoříte nebo zvolíte certifikát, abyste mohli při připojování prostřednictvím vzdálené plochy šifrovat informace o pověřeních.

   > [!Note]
   > Certifikáty, které potřebujete pro připojení ke vzdálené ploše se liší od certifikátů, které používáte pro jiné operace Azure. Certifikát vzdáleného přístupu musí mít soukromý klíč.

5. Vyberte certifikát ze seznamu nebo zvolte ** &lt;Vytvořit... &gt;**. Pokud vytváříte nový certifikát, po zobrazení výzvy zadejte popisný název nového certifikátu a vyberte **ok**. Nový certifikát se zobrazí v rozevíracím seznamu.

6. Zadejte uživatelské jméno a heslo. Nelze použít existující účet. Nepoužívejte "Správce" jako uživatelské jméno pro nový účet.

7. Zvolte datum, kdy vyprší platnost účtu a po které budou blokována připojení ke vzdálené ploše.

8. Po zadání všech požadovaných informací vyberte **možnost OK**. Visual Studio přidá nastavení vzdálené plochy `.cscfg` do `.csdef` projektu a souborů, včetně hesla, které je zašifrováno pomocí zvoleného certifikátu.

9. Všechny zbývající kroky dokončete pomocí tlačítka **Další** a až budete připraveni publikovat cloudovou službu, vyberte **Publikovat.** Pokud nejste připraveni publikovat, vyberte **Zrušit** a po zobrazení výzvy k uložení změn odpovězte **ano.** S těmito nastaveními můžete cloudovou službu publikovat později.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Konfigurace vzdálené plochy při použití Visual Studia 2017 verze 15.5 a novějších

S Visual Studio 2017 verze 15.5 a novější, můžete stále používat průvodce publikováním s projektem cloudové služby. Možnost **Povolit vzdálenou plochu** můžete také použít pro všechny role, pokud pracujete pouze jako jeden vývojář.

Pokud pracujete jako součást týmu, měli byste místo toho povolit vzdálenou plochu ve cloudové službě Azure pomocí [portálu Azure](cloud-services-role-enable-remote-desktop-new-portal.md) nebo [PowerShellu](cloud-services-role-enable-remote-desktop-powershell.md).

Toto doporučení je způsobeno změnou způsobu, jakým Visual Studio 2017 verze 15.5 a novější komunikuje s virtuálním mandatorním virtuálním mprostřed cloudové služby. Při povolení vzdálené plochy prostřednictvím průvodce publikováním komunikují starší verze sady Visual Studio s virtuálním počítačem prostřednictvím takzvaného modulu plug-in RDP. Visual Studio 2017 verze 15.5 a novější komunikuje místo toho pomocí "rozšíření RDP", která je bezpečnější a flexibilnější. Tato změna je také zarovnána s tím, že portál Azure a metody Prostředí PowerShell k povolení vzdálené plochy také používají rozšíření RDP.

Když Visual Studio komunikuje s rozšířením RDP, přenáší heslo ve formátu prostého textu přes TLS. Konfigurační soubory projektu však ukládají pouze šifrované heslo, které lze dešifrovat do prostého textu pouze s místním certifikátem, který byl původně použit k jeho šifrování.

Pokud pokaždé nasadíte projekt cloudové služby ze stejného vývojového počítače, bude k dispozici tento místní certifikát. V takovém případě můžete v průvodci publikováním stále používat možnost **Povolit vzdálenou plochu pro všechny role.**

Pokud však vy nebo jiní vývojáři chcete nasadit projekt cloudové služby z různých počítačů, nebudou mít tyto počítače potřebný certifikát k dešifrování hesla. V důsledku toho se zobrazí následující chybová zpráva:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

Heslo můžete změnit při každém nasazení cloudové služby, ale tato akce se stane nepohodlnou pro všechny uživatele, kteří potřebují používat vzdálenou plochu.

Pokud projekt sdílíte s týmem, je nejlepší tuto možnost zrušit v průvodci publikováním a místo toho povolit vzdálenou plochu přímo prostřednictvím [portálu Azure](cloud-services-role-enable-remote-desktop-new-portal.md) nebo pomocí [powershellu](cloud-services-role-enable-remote-desktop-powershell.md).

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Nasazení ze serveru sestavení pomocí Visual Studia 2017 verze 15.5 a novějších

Projekt cloudové služby můžete nasadit ze serveru sestavení (například pomocí služby Azure DevOps Services), na kterém je visual studio 2017 verze 15.5 nebo novější nainstalováno v agentovi sestavení. S tímto uspořádáním dojde k nasazení ze stejného počítače, ve kterém je k dispozici šifrovací certifikát.

Pokud chcete použít rozšíření RDP ze služeb Azure DevOps Services, zahrňte do kanálu sestavení následující podrobnosti:

1. Zahrnout `/p:ForceRDPExtensionOverPlugin=true` do argumentů MSBuild ujistěte se, že nasazení pracuje s rozšířením RDP spíše než plugin RDP. Například:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. Po krocích sestavení přidejte krok **nasazení cloudových služeb Azure** a nastavte jeho vlastnosti.

1. Po kroku nasazení přidejte krok **Azure Powershellu,** nastavte jeho vlastnost **display name** na "Azure Deployment: Enable RDP Extension" (nebo jiný vhodný název) a vyberte příslušné předplatné Azure.

1. Nastavte **typ skriptu** na "Vložit" a vložte níže uvedený kód do pole **Vložit skript.** (Pomocí tohoto skriptu `.ps1` můžete také vytvořit soubor v projektu, nastavit typ **skriptu** na "Cesta skriptu" a nastavit cestu **skriptu** tak, aby ukazovala na soubor.)

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

Po publikování cloudové služby v Azure a povolení vzdálené plochy můžete pomocí Průzkumníka serveru Visual Studio přihlásit se k virtuálnímu počítači cloudové služby:

1. V Průzkumníkovi serveru rozbalte uzel **Azure** a potom rozbalte uzel pro cloudovou službu a jednu z jejích rolí, abyste zobrazili seznam instancí.

2. Klepněte pravým tlačítkem myši na uzel instance a vyberte **příkaz Připojit pomocí vzdálené plochy**.

3. Zadejte uživatelské jméno a heslo, které jste vytvořili dříve. Nyní jste přihlášeni ke vzdálené relaci.

## <a name="additional-resources"></a>Další zdroje

[Jak konfigurovat Cloud Services](cloud-services-how-to-configure-portal.md)
