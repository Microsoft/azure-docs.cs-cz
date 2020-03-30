---
title: Vytvoření aplikace kontejneru pro Windows ve službě Fabric v Azure
description: V tomto rychlém startu vytvoříte svou první aplikaci typu kontejner pro Windows na platformě Azure Service Fabric.
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 477d47fabc59c5718c449418f225d6a38838b270
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75466265"
---
# <a name="quickstart-deploy-windows-containers-to-service-fabric"></a>Rychlý start: Nasazení kontejnerů Windows do Service Fabric

Azure Service Fabric je platforma distribuovaných systémů pro nasazování a správu škálovatelných a spolehlivých mikroslužeb a kontejnerů.

Spuštění existující aplikace v kontejneru Windows v clusteru Service Fabric nevyžaduje žádné změny aplikace. Tento rychlý start ukazuje, jak nasadit předem připravenou image kontejneru Dockeru v aplikaci Service Fabric. Po dokončení budete mít spuštěný kontejner Windows Server Core 2016 Server a IIS. Tento rychlý start popisuje nasazení kontejneru systému Windows. Přečtěte si [tento rychlý start](service-fabric-quickstart-containers-linux.md) pro nasazení kontejneru Linuxu.

![Výchozí webová stránka služby IIS][iis-default]

V tomto rychlém startu se naučíte:

* Zabalení kontejneru image Dockeru
* Konfigurace komunikace
* Sestavení a zabalení aplikace Service Fabric
* Nasazení aplikace typu kontejner do Azure


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure (můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
* Vývojový počítač s:
  * Visual Studio 2019 nebo Windows 2019.
  * [Service Fabric SDK a nástroje](service-fabric-get-started.md).

## <a name="package-a-docker-image-container-with-visual-studio"></a>Zabalení kontejneru image Dockeru pomocí sady Visual Studio

Sada Service Fabric SDK a nástroje poskytují šablonu služby, která vám pomůže s nasazením kontejneru do clusteru Service Fabric.

Spusťte sadu Visual Studio jako správce.  Vyberte **Soubor** > **nový** > **projekt**.

Vyberte **aplikaci Service Fabric**, pojmenujte ji "MyFirstContainer" a klepněte na tlačítko **Vytvořit**.

Z šablon **Hostované kontejnery a aplikace** vyberte **Kontejner**.

Do **pole Název bitové kopie**zadejte "mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016", základní [bitovou kopii systému Windows Server Core Server a IIS](https://hub.docker.com/_/microsoft-windows-servercore-iis).

Nakonfigurujte mapování portů kontejneru na porty hostitele tak, aby se příchozí požadavky na službu na portu 80 mapovaly na port 80 v kontejneru.  Nastavte **Port kontejneru** na 80 a **Port hostitele** také na 80.  

Pojmenujte službu MyContainerService a klikněte na **OK**.

![Dialogové okno Nová služba][new-service]

## <a name="specify-the-os-build-for-your-container-image"></a>Specifikace čísla sestavení operačního systému pro image kontejneru

Kontejnery sestavené s konkrétní verzí Windows Serveru nemusí fungovat na hostiteli s jinou verzí Windows Serveru. Kontejnery vytvořené například pomocí systému Windows Server verze 1709 se nespouštějí na hostitelích se systémem Windows Server 2016. Další informace najdete v článku o [kompatibilitě operačního systému kontejneru a operačního systému hostitele s Windows Serverem](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). 

Verze 6.1 modulu runtime služby Service Fabric a novější umožňuje specifikovat více imagí operačního systému na kontejner a označit jednotlivé image verzí sestavení daného operačního systému, na který by se měly nasazovat. Tímto se zajistí, že aplikace poběží na více hostitelích s různými verzemi operačního systému Windows. Další informace najdete v článku o [zadání imagí kontejneru pro konkrétní sestavení operačního systému](service-fabric-get-started-containers.md#specify-os-build-specific-container-images). 

Společnost Microsoft publikuje různé image pro verze IIS sestavené na různých verzích Windows Serveru. Abyste měli jistotu, že služba Service Fabric nasadí kontejner kompatibilní s verzí Windows Serveru běžící v uzlech clusteru, ve kterých nasazuje vaši aplikaci, přidejte do souboru *ApplicationManifest.xml* následující řádky. Verze sestavení pro Windows Server 2016 je 14393 a verze sestavení pro Windows Server verze 1709 je 16299.

```xml
    <ContainerHostPolicies CodePackageRef="Code"> 
      <ImageOverrides> 
        ...
          <Image Name="mcr.microsoft.com/windows/servercore/iis:windowsservercore-1803" /> 
          <Image Name= "mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016" Os="14393" /> 
          <Image Name="mcr.microsoft.com/windows/servercore/iis:windowsservercore-1709" Os="16299" /> 
      </ImageOverrides> 
    </ContainerHostPolicies> 
```

Manifest služby dále specifikuje jenom jednu image na nanoserver, `mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016`.

Také v souboru *ApplicationManifest.xml* změňte **PasswordEncrypted** na **false**. Účet a heslo jsou prázdné pro image veřejného kontejneru, který je v Docker Hubu, takže vypneme šifrování, protože šifrování prázdného hesla vygeneruje chybu sestavení.

```xml
<RepositoryCredentials AccountName="" Password="" PasswordEncrypted="false" />
```

## <a name="create-a-cluster"></a>Vytvoření clusteru

Následující ukázkový skript vytvoří cluster service fabric s pěti uzlovými sítěmi, který je zabezpečen certifikátem X.509. Příkaz vytvoří certifikát podepsaný svým držitelem a nahraje ho do nového trezoru klíčů. Certifikát se také zkopíruje do místního adresáře. Další informace o vytvoření clusteru pomocí tohoto skriptu naleznete v [seznamu Vytvoření clusteru Service Fabric](scripts/service-fabric-powershell-create-secure-cluster-cert.md).

V případě potřeby nainstalujte Azure PowerShell podle pokynů v [průvodci Azure PowerShell](/powershell/azure/overview).

Před spuštěním následujícího skriptu `Connect-AzAccount` v PowerShellu spustit vytvořit připojení s Azure.

Zkopírujte následující skript do schránky a otevřete **prostředí Windows PowerShell ISE**.  Vložte obsah do prázdného okna Untitled1.ps1. Potom zadejte hodnoty pro proměnné `subscriptionId`ve `certpwd` `certfolder`skriptu: , , `adminuser`, `adminpwd`, a tak dále.  Adresář, pro `certfolder` který zadáte, musí existovat před spuštěním skriptu.

[!code-powershell[main](../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

Jakmile zadáte hodnoty proměnných, spusťte skript stisknutím **klávesy F5.**

Po spuštění skriptu a vytvoření clusteru najděte `ClusterEndpoint` ve výstupu. Například:

```powershell
...
ClusterEndpoint : https://southcentralus.servicefabric.azure.com/runtime/clusters/b76e757d-0b97-4037-a184-9046a7c818c0
```

### <a name="install-the-certificate-for-the-cluster"></a>Instalace certifikátu pro cluster

Nyní nainstalujeme PFX v *úložišti certifikátů CurrentUser\My.* Soubor PFX bude v adresáři, `certfolder` který jste zadali pomocí proměnné prostředí ve skriptu PowerShell výše.

Změňte na tento adresář a spusťte následující příkaz prostředí PowerShell, který `certfolder` nahradí název souboru PFX, `certpwd` který je ve vašem adresáři, a heslo, které jste zadali v proměnné. V tomto příkladu je aktuální adresář nastaven `certfolder` na adresář určený proměnnou ve skriptu prostředí PowerShell. Odtud je `Import-PfxCertificate` příkaz spuštěn:

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\mysfclustergroup20190130193456.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString Password#1234 -AsPlainText -Force)
```

Příkaz vrátí kryptografický otisk:

```powershell
  ...
  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
0AC30A2FA770BEF566226CFCF75A6515D73FC686  CN=mysfcluster.SouthCentralUS.cloudapp.azure.com
```

Zapamatujte si hodnotu kryptografického otisku pro následující krok.

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Nasazení aplikace do Azure pomocí sady Visual Studio

Aplikace je teď připravená a přímo ze sady Visual Studio ji můžete nasadit do clusteru.

V Průzkumníku řešení klikněte pravým tlačítkem na **MyFirstContainer** a zvolte **Publikovat**. Zobrazí se dialogové okno Publikovat.

Zkopírujte obsah následující **CN=** v okně Prostředí `Import-PfxCertificate` PowerShell při `19000` spuštění výše uvedeného příkazu a přidejte k němu port. Například, `mysfcluster.SouthCentralUS.cloudapp.azure.com:19000`. Zkopírujte jej do pole **Koncový bod připojení.** Zapamatujte si tuto hodnotu, protože ji budete potřebovat v budoucím kroku.

Klikněte na **Rozšířené parametry připojení** a ověřte informace o parametrech připojení.  *Hodnoty FindValue* a *ServerCertThumbprint* se musí shodovat `Import-PfxCertificate` s kryptografickým otiskem certifikátu nainstalovaného při spuštění v předchozím kroku.

![Dialogové okno Publikovat](./media/service-fabric-quickstart-containers/publish-app.png)

Klikněte na **Publikovat**.

Každá aplikace v clusteru musí mít jedinečný název. Pokud dojde ke konfliktu názvů, přejmenujte projekt sady Visual Studio a nasaďte znovu.

Otevřete prohlížeč a přejděte na adresu, kterou jste vložili do pole **Koncový bod připojení** v předchozím kroku. Volitelně můžete před adresu URL přidat identifikátor schématu `http://` a připojit za ní port `:80`. Například http:\//mysfcluster.SouthCentralUS.cloudapp.azure.com:80.

 Měla by se zobrazit výchozí webová stránka služby IIS: ![Výchozí webová stránka služby IIS][iis-default]

## <a name="clean-up"></a>Vyčištění

V průběhu spuštění clusteru se budou nadále vynakládat poplatky. Zvažte [odstranění clusteru](service-fabric-cluster-delete.md).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili:

* Zabalení kontejneru image Dockeru
* Konfigurace komunikace
* Sestavení a zabalení aplikace Service Fabric
* Nasazení aplikace typu kontejner do Azure

Další informace o práci s kontejnery Windows v Service Fabric najdete v kurzu věnovaném aplikacím typu kontejner pro Windows.

> [!div class="nextstepaction"]
> [Vytvoření aplikace typu kontejner pro Windows](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
[new-service]: ./media/service-fabric-quickstart-containers/NewService.png
