---
title: Vytvoření aplikace typu kontejner pro Windows v Service Fabric v Azure
description: V tomto rychlém startu vytvoříte svou první aplikaci typu kontejner pro Windows na platformě Azure Service Fabric.
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 934b679813323fe144f935811eb613ab68b561db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87089613"
---
# <a name="quickstart-deploy-windows-containers-to-service-fabric"></a>Rychlý start: Nasazení kontejnerů Windows do Service Fabric

Azure Service Fabric je platforma distribuovaných systémů pro nasazování a správu škálovatelných a spolehlivých mikroslužeb a kontejnerů.

Spuštění existující aplikace v kontejneru Windows v clusteru Service Fabric nevyžaduje žádné změny aplikace. Tento rychlý start ukazuje, jak nasadit předem připravenou image kontejneru Dockeru v aplikaci Service Fabric. Až budete hotovi, budete mít spuštěný Server s Windows serverem Core 2016 a kontejner služby IIS. Tento rychlý Start popisuje nasazení kontejneru Windows. Přečtěte si [Tento rychlý Start](service-fabric-quickstart-containers-linux.md) a nasaďte kontejner Linux.

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
  * [Service Fabric SDK a nástroje](service-fabric-get-started.md)

## <a name="package-a-docker-image-container-with-visual-studio"></a>Zabalení kontejneru image Dockeru pomocí sady Visual Studio

Sada Service Fabric SDK a nástroje poskytují šablonu služby, která vám pomůže s nasazením kontejneru do clusteru Service Fabric.

Spusťte sadu Visual Studio jako správce.  Vyberte **soubor**  >  **Nový**  >  **projekt**.

Vyberte **Service Fabric aplikace**, pojmenujte ji "MyFirstContainer" a klikněte na **vytvořit**.

Z šablon **Hostované kontejnery a aplikace** vyberte **Kontejner**.

Do pole **název Image** zadejte "MCR.Microsoft.com/Windows/ServerCore/IIS:windowsservercore-ltsc2016", [Server jádra Windows serveru a základní bitovou kopii služby IIS](https://hub.docker.com/_/microsoft-windows-servercore-iis).

Nakonfigurujte mapování portů kontejneru na porty hostitele tak, aby se příchozí požadavky na službu na portu 80 mapovaly na port 80 v kontejneru.  Nastavte **Port kontejneru** na 80 a **Port hostitele** také na 80.  

Pojmenujte službu MyContainerService a klikněte na **OK**.

![Dialogové okno Nová služba][new-service]

## <a name="specify-the-os-build-for-your-container-image"></a>Specifikace čísla sestavení operačního systému pro image kontejneru

Kontejnery sestavené s konkrétní verzí Windows Serveru nemusí fungovat na hostiteli s jinou verzí Windows Serveru. Například kontejnery sestavené pomocí systému Windows Server verze 1709 neběží na hostitelích se systémem Windows Server 2016. Další informace najdete v článku o [kompatibilitě operačního systému kontejneru a operačního systému hostitele s Windows Serverem](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). 

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

V souboru *ApplicationManifest.xml* také změňte **PasswordEncrypted** na **hodnotu false (NEPRAVDA**). Účet a heslo pro veřejnou image kontejneru, která je v Docker Hub, jsou prázdné, takže šifrování vypneme, protože při šifrování prázdného hesla se vytvoří chyba buildu.

```xml
<RepositoryCredentials AccountName="" Password="" PasswordEncrypted="false" />
```

## <a name="create-a-cluster"></a>Vytvoření clusteru

Následující vzorový skript vytvoří cluster Service Fabric s pěti uzly zabezpečený pomocí certifikátu X. 509. Příkaz vytvoří certifikát podepsaný svým držitelem a nahraje ho do nového trezoru klíčů. Certifikát se také zkopíruje do místního adresáře. Další informace o tom, jak vytvořit cluster pomocí tohoto skriptu, najdete v [Service Fabric clusteru](scripts/service-fabric-powershell-create-secure-cluster-cert.md).

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce Azure PowerShell](/powershell/azure/).

Před spuštěním následujícího skriptu můžete v PowerShellu spustit, `Connect-AzAccount` aby se vytvořilo připojení k Azure.

Zkopírujte následující skript do schránky a otevřete **Integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell**.  Vložte obsah do prázdného okna Untitled1.ps1. Pak zadejte hodnoty pro proměnné ve skriptu: `subscriptionId` , `certpwd` , `certfolder` , `adminuser` , `adminpwd` a tak dále.  Adresář, který zadáte pro, `certfolder` musí existovat před spuštěním skriptu.

[!code-powershell[main](../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

Po zadání hodnot pro proměnné stiskněte klávesu **F5** ke spuštění skriptu.

Po spuštění skriptu a vytvoření clusteru Najděte `ClusterEndpoint` ve výstupu. Například:

```powershell
...
ClusterEndpoint : https://southcentralus.servicefabric.azure.com/runtime/clusters/b76e757d-0b97-4037-a184-9046a7c818c0
```

### <a name="install-the-certificate-for-the-cluster"></a>Instalace certifikátu pro cluster

Nyní nainstalujeme PFX do úložiště certifikátů *do currentuser\my* . Soubor PFX bude v adresáři, který jste zadali pomocí `certfolder` proměnné prostředí ve výše uvedeném skriptu PowerShellu.

Přejděte do tohoto adresáře a spusťte následující příkaz prostředí PowerShell, kde nahraďte název souboru PFX, který je ve vašem `certfolder` adresáři, a heslo, které jste zadali v `certpwd` proměnné. V tomto příkladu je aktuální adresář nastavený na adresář určený `certfolder` proměnnou ve skriptu PowerShellu. Z tohoto `Import-PfxCertificate` příkazu se spustí příkaz:

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

Při spuštění příkazu v okně PowerShellu zkopírujte obsah následující **CN =** a do `Import-PfxCertificate` něj přidejte port `19000` . Například, `mysfcluster.SouthCentralUS.cloudapp.azure.com:19000`. Zkopírujte ho do pole **koncový bod připojení** . Zapamatujte si tuto hodnotu, protože ji budete potřebovat v budoucím kroku.

Klikněte na **Rozšířené parametry připojení** a ověřte informace o parametrech připojení.  Hodnoty *findValue* a *ServerCertThumbprint* musí odpovídat kryptografickému otisku certifikátu nainstalovaného při spuštění `Import-PfxCertificate` v předchozím kroku.

![Dialogové okno Publikovat](./media/service-fabric-quickstart-containers/publish-app.png)

Klikněte na **Publikovat**.

Každá aplikace v clusteru musí mít jedinečný název. Pokud dojde ke konfliktu názvů, přejmenujte projekt sady Visual Studio a znovu ho nasaďte.

Otevřete prohlížeč a přejděte na adresu, kterou jste vložili do pole **koncový bod připojení** v předchozím kroku. Volitelně můžete před adresu URL přidat identifikátor schématu `http://` a připojit za ní port `:80`. Například http: \/ /mysfcluster.SouthCentralUS.cloudapp.Azure.com:80.

 Měla by se zobrazit výchozí webová stránka služby IIS: ![Výchozí webová stránka služby IIS][iis-default]

## <a name="clean-up"></a>Vyčištění

Po spuštění clusteru se vám budou účtovat poplatky. Zvažte [odstranění clusteru](./service-fabric-tutorial-delete-cluster.md).

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
