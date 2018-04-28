---
title: Povolit připojení ke vzdálené ploše pro roli v cloudové služby Azure
description: Postup konfigurace aplikace služby Azure cloud umožňující připojení ke vzdálené ploše
services: cloud-services
author: ghogen
manager: douge
ms.assetid: f5727ebe-9f57-4d7d-aff1-58761e8de8c1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.topic: conceptual
ms.workload: azure
ms.date: 03/06/2018
ms.author: ghogen
ms.openlocfilehash: fe8b2b59616246743b38aa3b7a7972c092529b5d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-visual-studio"></a>Povolit připojení ke vzdálené ploše pro roli ve službě Azure Cloud Services pomocí sady Visual Studio

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Vzdálená plocha umožňuje přístup k ploše role, která běží v Azure. Připojení ke vzdálené ploše můžete odstraňovat a diagnostikovat problémy s aplikací, když je spuštěná.

Průvodce publikování, který Visual Studio poskytuje pro cloudové služby zahrnuje možnost povolení služby Vzdálená plocha během procesu publikování pomocí přihlašovacích údajů, které zadáte. Použití této možnosti je vhodné, pokud používáte Visual Studio 2017 15,4 a starší verze.

U Visual Studio 2017 verze 15,5 a novější je však vhodné vyhnout povolení vzdálené plochy pomocí Průvodce publikovat, pokud pracujete pouze jako jeden vývojář. U jakékoliv jiné situaci s jinými vývojáři mohou otevřít ve kterém projektu místo toho povolit vzdálené plochy prostřednictvím portálu Azure pomocí prostředí PowerShell nebo z verze definice v pracovním postupu průběžné nasazování. Toto doporučení je z důvodu změn v jak Visual Studio komunikuje pomocí vzdálené plochy cloudové služby virtuálních počítačů, jak je popsáno v tomto článku.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Konfigurace vzdálené plochy prostřednictvím Visual Studio 2017 verze 15,4 a starší

Pokud používáte Visual Studio 2017 15,4 a starší verze, můžete použít **povolení vzdálené plochy u všech rolí** v Průvodci publikovat. Průvodce můžete pořád použít s Visual Studio 2017 verze 15,5 a novější, ale nepoužívají možnost vzdálené plochy.

1. V sadě Visual Studio, spusťte Průvodce Publikovat pravým tlačítkem myši na projekt cloudové služby v Průzkumníku řešení a zvolením **publikovat**.

2. V případě potřeby přihlásit do vašeho předplatného Azure a vybrat **Další**.

3. Na **nastavení** vyberte **povolení vzdálené plochy u všech rolí**, vyberte **nastavení...**  odkazu k otevření **konfigurace vzdálené plochy** dialogové okno.

4. V dolní části dialogových oken, vyberte **další možnosti**. Tento příkaz zobrazí rozevíracího seznamu, ve kterém vytvoříte nebo zvolit certifikát, takže můžete šifrovat informace pověření při připojování přes vzdálenou plochu.

   > [!Note]
   > Certifikáty, které potřebujete pro připojení ke vzdálené ploše se liší od certifikáty, které používáte pro jiné operace v Azure. Certifikát vzdáleného přístupu musí mít privátní klíč.

5. Vyberte certifikát ze seznamu, nebo zvolte  **&lt;vytvořit... &gt;**. Pokud vytváříte nový certifikát, zadejte popisný název pro nový certifikát po zobrazení výzvy a vyberte **OK**. V rozevíracím seznamu se zobrazí nový certifikát.

6. Zadejte uživatelské jméno a heslo. Nelze použít existující účet. Nepoužívejte "Správce" jako uživatelské jméno pro nový účet.

7. Zvolte datum, na který platnosti účtu a po které připojení ke vzdálené ploše se zablokuje.

8. Když jste zadali všechny požadované informace, vyberte **OK**. Visual Studio. přidá do projektu nastavení vzdálené plochy `.cscfg` a `.csdef` soubory, včetně heslo, které jsou šifrována pomocí zvolený certifikát.

9. Dokončit všechny zbývající kroky, pomocí **Další** tlačítko a pak vyberte **publikovat** Jakmile budete připraveni k publikování cloudové služby. Pokud si nejste připravena k publikování, vyberte **zrušit** a odpověď **Ano** po zobrazení výzvy se uložit změny. Cloudové služby můžete později publikovat s tímto nastavením.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Konfigurace vzdálené plochy, při použití Visual Studio 2017 verze 15,5 a novější

S Visual Studio 2017 verze 15,5 a novější stále můžete publikovat Průvodce projekt cloudové služby. Můžete také **povolení vzdálené plochy u všech rolí** možnost, pokud pracujete pouze jako jeden vývojář.

Pokud pracujete v rámci týmu, měli byste místo toho povolit vzdálené plochy ve službě Azure cloud pomocí [portál Azure](cloud-services-role-enable-remote-desktop-new-portal.md) nebo [prostředí PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

Toto doporučení je z důvodu změn v tom, jak Visual Studio 2017 verze 15,5 a novější komunikuje se virtuální počítač cloudové služby. Při povolení funkce Vzdálená plocha pomocí Průvodce publikování, starší verze sady Visual Studio komunikovat s virtuálním Počítačem přes co volal "RDP plugin." Visual Studio 2017 verze 15,5 a novější komunikuje, místo toho pomocí rozšíření protokolu RDP"", je flexibilnější a bezpečnější. Tato změna také zarovnaná s fakt, že portál Azure a prostředí PowerShell metody pro povolení služby Vzdálená plocha také použít rozšíření protokolu RDP.

Pokud Visual Studio komunikuje s příponou RDP, přenášet hesla v podobě prostého textu přes protokol SSL. Konfigurační soubory projektu však uložit jenom zašifrovaného hesla, která je pak možné dešifrovat do prostého textu pouze s místní certifikát, který byl původně použilo k jejich zašifrování.

Pokud nasazujete projekt cloudové služby ze stejného počítače vývoj pokaždé, když je tento místní certifikát k dispozici. V takovém případě můžete pořád použít **povolení vzdálené plochy u všech rolí** v Průvodci publikovat.

Pokud jste nebo jinými vývojáři chcete nasadit projekt cloudové služby z různých počítačů, ale pak tyto nebudou mít ostatní počítače nezbytné certifikátem k dešifrování hesla. Výsledkem je zobrazí se následující chybová zpráva:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

Pokaždé, když nasazujete cloudové služby, ale tato akce velmi praktické, pro každého, kdo potřebuje k použití služby Vzdálená plocha můžete změnit heslo.

Pokud sdílíte projektu s týmem, je nejlepší zrušte zaškrtnutí políčka v Průvodci publikovat a místo toho povolit přímo pomocí vzdálené plochy [portál Azure](cloud-services-role-enable-remote-desktop-new-portal.md) nebo pomocí [prostředí PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Nasazení ze serveru sestavení s Visual Studio 2017 verze 15,5 a novější

Můžete nasadit projekt cloudové služby ze serveru sestavení (například s Visual Studio Team Services), na které Visual Studio 2017 je nainstalována verze 15,5 nebo novějším v agentovi nástroje sestavení. K tomuto uspořádání nasazení se odehrává ze stejného počítače, na kterém je k dispozici šifrovací certifikát.

Pokud chcete používat rozšíření protokolu RDP z Visual Studio Team Services, zahrnout do vaší definice sestavení následující podrobnosti:

1. Zahrnout `/p:ForceRDPExtensionOverPlugin=true` ve vaší argumenty MSBuild a ujistit se nasazení pracuje s příponou RDP, nikoli modulu plug-in protokolu RDP. Příklad:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. Po provedení kroků vaše sestavení přidat **nasazení služby Azure Cloud** krok a nastavit jeho vlastnosti.

1. Po nasazení kroku, přidejte **prostředí Azure Powershell** krok, nastavte jeho **zobrazovaný název** vlastnost "Azure nasazení: povolení protokolu RDP rozšíření" (nebo jiný vhodný název) a vyberte vaše odpovídající Azure předplatné.

1. Nastavit **typ skriptu** k "Vložené" a vložte kód pod do **vloženého skriptu** pole. (Můžete také vytvořit `.ps1` souborů ve vašem projektu pomocí tohoto skriptu, nastavte **typ skriptu** "Cestu souboru skriptu" a sadu **cestu ke skriptu** tak, aby odkazovaly do souboru.)

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

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Připojení do Azure Role pomocí vzdálené plochy

Po publikování cloudové služby v Azure a povolení vzdálené plochy, můžete Průzkumníka serveru Visual Studia pro přihlášení na virtuální počítač cloudové služby:

1. V Průzkumníku serveru rozbalte **Azure** uzel a pak rozbalte uzel pro cloudové služby a jeden z jeho role můžete zobrazit seznam instancí.

2. Klikněte pravým tlačítkem uzel instance a vyberte **připojit pomocí vzdálené plochy**.

3. Zadejte uživatelské jméno a heslo, které jste předtím vytvořili. Nyní jste přihlášeni ke vzdálené relaci.

## <a name="additional-resources"></a>Další zdroje informací:

[Jak konfigurovat Cloud Services](cloud-services-how-to-configure-portal.md)