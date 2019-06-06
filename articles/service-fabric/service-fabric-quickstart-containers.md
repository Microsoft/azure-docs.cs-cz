---
title: Vytvoření aplikace typu kontejner pro Windows na platformě Service Fabric v Azure | Microsoft Docs
description: V tomto rychlém startu vytvoříte svou první aplikaci typu kontejner pro Windows na platformě Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: jpconnock
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/31/2019
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 4795952faa91d62b76f267795660db5ab4075e79
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734502"
---
# <a name="quickstart-deploy-windows-containers-to-service-fabric"></a>Rychlý start: Nasadit kontejnery Windows do Service Fabric

Azure Service Fabric je platforma distribuovaných systémů pro nasazování a správu škálovatelných a spolehlivých mikroslužeb a kontejnerů.

Spuštění existující aplikace v kontejneru Windows v clusteru Service Fabric nevyžaduje žádné změny aplikace. Tento rychlý start ukazuje, jak nasadit předem připravenou image kontejneru Dockeru v aplikaci Service Fabric. Jakmile budete hotovi, budete mít funkční kontejner Windows Server s jádrem serveru 2016 a služby IIS. Tento rychlý start popisuje nasazení kontejneru Windows. Čtení [v tomto rychlém startu](service-fabric-quickstart-containers-linux.md) nasazení kontejneru Linuxu.

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
  * Visual Studio 2015 nebo Windows 2019.
  * [Sada Service Fabric SDK a nástroje](service-fabric-get-started.md).

## <a name="package-a-docker-image-container-with-visual-studio"></a>Zabalení kontejneru image Dockeru pomocí sady Visual Studio

Sada Service Fabric SDK a nástroje poskytují šablonu služby, která vám pomůže s nasazením kontejneru do clusteru Service Fabric.

Spusťte sadu Visual Studio jako správce.  Vyberte **Soubor** > **Nový** > **Projekt**.

Vyberte **Aplikace Service Fabric**, pojmenujte ji MyFirstContainer a klikněte na **OK**.

Z šablon **Hostované kontejnery a aplikace** vyberte **Kontejner**.

V **název Image**, zadejte "mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016" [základní image Windows serveru jádra serveru a služby IIS](https://hub.docker.com/_/microsoft-windows-servercore-iis).

Nakonfigurujte mapování portů kontejneru na porty hostitele tak, aby se příchozí požadavky na službu na portu 80 mapovaly na port 80 v kontejneru.  Nastavte **Port kontejneru** na 80 a **Port hostitele** také na 80.  

Pojmenujte službu MyContainerService a klikněte na **OK**.

![Dialogové okno Nová služba][new-service]

## <a name="specify-the-os-build-for-your-container-image"></a>Specifikace čísla sestavení operačního systému pro image kontejneru

Kontejnery sestavené s konkrétní verzí Windows Serveru nemusí fungovat na hostiteli s jinou verzí Windows Serveru. Například kontejnery vytvořené pomocí systému Windows Server verze 1709 nespouštět na hostitelích s Windows serverem 2016. Další informace najdete v článku o [kompatibilitě operačního systému kontejneru a operačního systému hostitele s Windows Serverem](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). 

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

Také v *ApplicationManifest.xml* změňte **PasswordEncrypted** k **false**. Účet a heslo je prázdné, pokud veřejné image kontejneru, který je na Docker Hubu, takže jsme vypnout šifrování, protože šifrování heslo necháte prázdné, vygeneruje chybu sestavení.

```xml
<RepositoryCredentials AccountName="" Password="" PasswordEncrypted="false" />
```

## <a name="create-a-cluster"></a>Vytvoření clusteru

Následující ukázkový skript vytvoří clusteru Service Fabric pěti uzly zabezpečený pomocí certifikátu X.509. Příkaz vytvoří certifikát podepsaný svým držitelem a nahraje ho do nového trezoru klíčů. Certifikát se také zkopíruje do místního adresáře. Další informace o vytváření clusteru pomocí tohoto skriptu v [vytvořit cluster Service Fabric](scripts/service-fabric-powershell-create-secure-cluster-cert.md).

V případě potřeby nainstalujte Azure PowerShell podle pokynů v [příručce k Azure Powershellu](/powershell/azure/overview).

Před spuštěním následujícího skriptu v prostředí PowerShell spustit `Connect-AzAccount` vytvořit připojení k Azure.

Zkopírujte následující skript do schránky a otevřete **Windows PowerShell ISE**.  Umožňuje vložte obsah do prázdné okno Untitled1.ps1. Potom zadejte hodnoty pro proměnné ve skriptu: `subscriptionId`, `certpwd`, `certfolder`, `adminuser`, `adminpwd`, a tak dále.  Adresáře, zadejte pro `certfolder` musí existovat před spuštěním skriptu.

[!code-powershell[main](../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

Jakmile zadáte hodnoty pro proměnné stiskněte **F5** pro spuštění skriptu.

Po spuštění skriptu a vytvoření clusteru, vyhledejte `ClusterEndpoint` ve výstupu. Příklad:

```powershell
...
ClusterEndpoint : https://southcentralus.servicefabric.azure.com/runtime/clusters/b76e757d-0b97-4037-a184-9046a7c818c0
```

### <a name="install-the-certificate-for-the-cluster"></a>Nainstalujte certifikát pro cluster

Nyní nainstalujeme PFX do *CurrentUser\My* úložiště certifikátů. Soubor PFX bude v adresáři, které jste určili pomocí `certfolder` proměnné prostředí ve skriptu prostředí PowerShell výše.

Přechod do této složky a pak spusťte následující příkaz prostředí PowerShell, nahraďte název souboru PFX, který je ve vaší `certfolder` adresáře a heslo, které jste zadali v `certpwd` proměnné. V tomto příkladu je nastavena aktuální adresář do adresáře určeného `certfolder` proměnné ve skriptu prostředí PowerShell. Odtud `Import-PfxCertificate` spusťte příkaz:

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

Mějte na paměti hodnotu kryptografický otisk pro následující krok.

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Nasazení aplikace do Azure pomocí sady Visual Studio

Aplikace je teď připravená a přímo ze sady Visual Studio ji můžete nasadit do clusteru.

V Průzkumníku řešení klikněte pravým tlačítkem na **MyFirstContainer** a zvolte **Publikovat**. Zobrazí se dialogové okno Publikovat.

Zkopírujte do něj následující obsah **CN =** v okně Powershellu, když jste spustili `Import-PfxCertificate` příkaz výše a přidejte port `19000` k němu. Například, `mysfcluster.SouthCentralUS.cloudapp.azure.com:19000`. Zkopírujte jej do **koncový bod připojení** pole. Pamatovat si tuto hodnotu, protože ho budete potřebovat v dalším kroku.

Klikněte na **Rozšířené parametry připojení** a ověřte informace o parametrech připojení.  *FindValue* a *ServerCertThumbprint* hodnoty musí odpovídat kryptografickému otisku certifikátu nainstalovanému při spuštění `Import-PfxCertificate` v předchozím kroku.

![Dialogové okno Publikovat](./media/service-fabric-quickstart-containers/publish-app.png)

Klikněte na tlačítko **publikovat**.

Každá aplikace v clusteru musí mít jedinečný název. Pokud dojde ke konfliktu názvů, přejmenujte projekt sady Visual Studio a znovu nasaďte.

Otevřete prohlížeč a přejděte na adresu, kam si ukládáte do **koncový bod připojení** pole v předchozím kroku. Volitelně můžete před adresu URL přidat identifikátor schématu `http://` a připojit za ní port `:80`. Například http:\//mysfcluster.SouthCentralUS.cloudapp.azure.com:80.

 Zobrazí se výchozí webová stránka služby IIS: ![Výchozí webová stránka služby IIS][iis-default]

## <a name="clean-up"></a>Vyčištění

Můžete se dál budou účtovat poplatky za spuštěného clusteru. Vezměte v úvahu [odstranění clusteru](service-fabric-cluster-delete.md).

## <a name="next-steps"></a>Další postup

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
