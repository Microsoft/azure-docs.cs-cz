---
title: Povolit připojení ke vzdálené ploše pro roli v cloudových službách Azure
description: Jak nakonfigurovat aplikaci služby Azure cloud umožňuje připojení ke vzdálené ploše
services: cloud-services
author: ghogen
manager: douge
ms.assetid: f5727ebe-9f57-4d7d-aff1-58761e8de8c1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.workload: azure-vs
ms.date: 03/06/2018
ms.author: ghogen
ms.openlocfilehash: 703e969fe31def329be60037cceba27864063b4e
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44304046"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-visual-studio"></a>Povolení připojení ke vzdálené ploše pro roli v cloudových službách Azure pomocí sady Visual Studio

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Vzdálená plocha umožňuje přístup k ploše role, která běží v Azure. Připojení ke vzdálené ploše můžete použít k odstranění potíží a Diagnostikujte problémy s vaší aplikací je spuštěný.

Průvodce publikováním, která sadě Visual Studio poskytuje pro cloud services zahrnuje možnost povolit vzdálenou plochu během procesu publikování pomocí přihlašovacích údajů, které zadáte. Pomocí této možnosti je vhodné při používání sady Visual Studio 2017 verze 15.4 nebo starší.

S Visual Studio 2017 verze 15.5 nebo novější ale doporučujeme vyhnout povolení vzdálené plochy prostřednictvím Průvodce publikovat, pokud pracujete jenom jako jediný vývojář. Pro každé situaci, ve kterém může otevřít projekt jinými vývojáři místo toho povolit vzdálenou plochu na webu Azure portal, prostředí PowerShell nebo z kanál pro vydávání verzí v pracovním postupu průběžného nasazování. Toto doporučení je z důvodu změny jak Visual Studio komunikuje s vzdálené plochy na cloudovou službu virtuálního počítače, jak je vysvětleno v tomto článku.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Konfigurace vzdálené plochy prostřednictvím sady Visual Studio 2017 verze 15.4 nebo starší

Pokud používáte Visual Studio 2017 verze 15.4 nebo starší, můžete použít **povolit vzdálenou plochu pro všechny role** možnost v Průvodci publikováním. Můžete stále použít Průvodce sadou Visual Studio 2017 verze 15.5 nebo novější, ale nepoužívejte možnost vzdálené plochy.

1. V sadě Visual Studio, spusťte Průvodce Publikovat pravým tlačítkem myši na projekt cloudové služby v Průzkumníku řešení a zvolením **publikovat**.

2. Přihlaste se do vašeho předplatného Azure v případě potřeby a vyberte **Další**.

3. Na **nastavení** stránce **povolit vzdálenou plochu pro všechny role**a pak **nastavení...**  odkaz k otevření **konfigurace vzdálené plochy** dialogové okno.

4. V dolní části dialogového okna, vyberte **další možnosti**. Tento příkaz zobrazí rozevíracího seznamu, ve kterém vytvoříte nebo zvolte certifikát, takže můžete šifrovat přihlašovací údaje při připojování přes vzdálenou plochu.

   > [!Note]
   > Certifikáty, které budete potřebovat pro připojení ke vzdálené ploše se liší od certifikáty, které použijete pro další provoz Azure. Certifikát vzdáleného přístupu musí mít privátní klíč.

5. Vyberte certifikát ze seznamu nebo zvolte  **&lt;vytvořit... &gt;**. Při vytváření nového certifikátu, zadejte popisný název pro nový certifikát po zobrazení výzvy a vyberte **OK**. V rozevíracím seznamu se zobrazí nový certifikát.

6. Zadejte uživatelské jméno a heslo. Nelze použít existující účet. Nepoužívejte jako uživatelské jméno pro nový účet "Administrator".

7. Zvolte datum na účtu vyprší platnost a za které připojením ke vzdálené ploše se zablokuje.

8. Po zadání všech požadovaných informací, vyberte **OK**. Visual Studio přidá do vašeho projektu nastavení vzdálené plochy `.cscfg` a `.csdef` souborů, včetně heslo, které je šifrovaný s použitím zvolený certifikát.

9. Proveďte všechny zbývající kroky pomocí **Další** tlačítko a pak vyberte **publikovat** až budete připraveni publikovat vaši cloudovou službu. Pokud si nejste připraveni publikovat, vyberte **zrušit** a odpověď **Ano** po zobrazení výzvy k uložení změn. Cloudové služby můžete publikovat později pomocí těchto nastavení.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Konfigurace vzdálené plochy, když pomocí sady Visual Studio 2017 verze 15.5 nebo novější

S Visual Studio 2017 verze 15.5 nebo novější můžete stále použít Průvodce publikováním se projekt cloudové služby. Můžete také použít **povolit vzdálenou plochu pro všechny role** možnost, pokud pracujete jenom jako jediný vývojář.

Pokud pracujete jako součást týmu, měli byste místo toho povolit vzdálená plocha ve službě Azure cloud service pomocí [webu Azure portal](cloud-services-role-enable-remote-desktop-new-portal.md) nebo [Powershellu](cloud-services-role-enable-remote-desktop-powershell.md).

Toto doporučení je z důvodu změn v tom, jak Visual Studio 2017 verze 15.5 nebo novější komunikuje s cloudovou službu virtuálního počítače. Při povolení funkce Vzdálená plocha prostřednictvím Průvodce publikovat, dřívějších verzích sady Visual Studio komunikaci s virtuálním Počítačem přes takzvaný "RDP modul plug-in." Visual Studio 2017 verze 15.5 nebo novější komunikuje se místo toho používat rozšíření RDP"", které je flexibilnější a bezpečnější. Tato změna také v souladu s skutečnost, že na webu Azure portal a Powershellu metody povolit vzdálenou plochu také používat rozšíření RDP.

Když Visual Studio komunikuje se rozšíření RDP, přenášet heslo jako prostý text přes protokol SSL. Konfigurační soubory v projektu však uložit jenom šifrované heslo, které je možné dešifrovat do prostého textu jenom s místní certifikát, který byl původně použit k šifrování.

Pokud provádíte nasazení pokaždé, když projekt cloudové služby ze stejného počítače, vývoj, místní certifikátu je k dispozici. V takovém případě můžete stále použít **povolit vzdálenou plochu pro všechny role** možnost v Průvodci publikováním.

Pokud vy nebo jiní vývojáři chcete nasadit projekt cloudové služby z různých počítačů, ale pak tyto počítače nebude mít nezbytný certifikát k dešifrování hesla. V důsledku toho se zobrazí následující chybová zpráva:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

Můžete změnit heslo při každém nasazení cloudové služby, ale tato akce velmi praktické pro každého, kdo potřebuje k použití vzdálené plochy.

Pokud sdílíte s týmem projektu, pak je nejlepší, zrušte zaškrtnutí políčka v Průvodci publikováním a místo toho povolit přímo pomocí vzdálené plochy [webu Azure portal](cloud-services-role-enable-remote-desktop-new-portal.md) nebo s použitím [Powershellu](cloud-services-role-enable-remote-desktop-powershell.md).

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Nasazení ze serveru sestavení pomocí sady Visual Studio 2017 verze 15.5 nebo novější

Můžete nasadit projekt cloudové služby ze serveru sestavení (například pomocí služeb Azure DevOps), které Visual Studio 2017 verze 15.5 nebo novější nainstalovaný v agentovi sestavení. Pomocí tohoto uspořádání nasazení se odehrává ze stejného počítače, na kterém je k dispozici šifrovací certifikát.

Pokud chcete používat rozšíření RDP ze služeb Azure DevOps, uveďte následující podrobnosti ve vašem kanálu sestavení:

1. Zahrnout `/p:ForceRDPExtensionOverPlugin=true` vaše argumenty nástroje MSBuild k Ujistěte se, že nasazení funguje se rozšíření RDP spíše než plugin protokolu RDP. Příklad:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. Po kroků sestavení přidejte **nasazení cloudové služby Azure** kroku a nastavte jeho vlastnosti.

1. Po dokončení kroku nasazení přidat **prostředí Azure Powershell** krok, nastavte jeho **zobrazovaný název** vlastnost na hodnotu "Nasazení: povolení protokolu RDP rozšíření Azure" (nebo jiný vhodný název) a vyberte příslušnou Azure předplatné.

1. Nastavte **typ skriptu** na "Vloženě" a vložte následující kód do **zpracování vloženého skriptu** pole. (Můžete také vytvořit `.ps1` souboru ve vašem projektu pomocí tohoto skriptu **typ skriptu** "Cesta k souboru skriptu" a sadu **cesta ke skriptu** tak, aby odkazoval na soubor.)

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

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Připojte se k roli Azure pomocí vzdálené plochy

Po publikování vaší cloudové služby v Azure a povolení vzdálené plochy, můžete použít Průzkumníka serveru Visual Studia pro přihlášení ke cloudové službě virtuálního počítače:

1. V Průzkumníku serveru rozbalte **Azure** uzel a potom rozbalte uzel pro cloudovou službu a jeden z jeho role, chcete-li zobrazit seznam instancí.

2. Klikněte pravým tlačítkem na některý uzel instance a vyberte **připojit pomocí vzdálené plochy**.

3. Zadejte uživatelské jméno a heslo, které jste vytvořili dříve. Nyní jste se přihlásili do vzdálené relace.

## <a name="additional-resources"></a>Další zdroje informací:

[Jak konfigurovat Cloud Services](cloud-services-how-to-configure-portal.md)
