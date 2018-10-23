---
title: Přidání koncového bodu HTTPS využívajícího Kestrel do aplikace Service Fabric v Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak do webové front-end služby ASP.NET Core využívající Kestrel přidat koncový bod HTTPS a nasadit tuto aplikaci do clusteru.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/12/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 4333a234efe96f32541254819c9c5f21bb031757
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49115072"
---
# <a name="tutorial-add-an-https-endpoint-to-an-aspnet-core-web-api-front-end-service-using-kestrel"></a>Kurz: Přidání koncového bodu HTTPS do front-endové služby webového rozhraní API ASP.NET Core využívající Kestrel

Tento kurz je třetí částí série.  Zjistíte, jak povolit HTTPS ve službě ASP.NET Core spuštěné v Service Fabric. Až budete hotovi, budete mít hlasovací aplikaci s webovým front-endem ASP.NET Core s povoleným HTTPS, který bude naslouchat na portu 443. Pokud nechcete hlasovací aplikaci vytvářet ručně, v tématu popisujícím [vytvoření aplikace Service Fabric v .NET](service-fabric-tutorial-deploy-app-to-party-cluster.md) si můžete [stáhnout zdrojový kód](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) dokončené aplikace.

Ve třetí části této série se naučíte:

> [!div class="checklist"]
> * Definovat ve službě koncový bod HTTPS
> * Nakonfigurovat Kestrel k používání HTTPS
> * Nainstalovat certifikát SSL na uzly vzdáleného clusteru
> * Poskytnout účtu NETWORK SERVICE přístup k privátnímu klíči certifikátu
> * Otevřít port 443 v nástroji pro vyrovnávání zatížení Azure
> * Nasadit aplikaci do vzdáleného clusteru

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Sestavit aplikaci .NET pro Service Fabric](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Nasadit aplikaci do vzdáleného clusteru](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * Přidání koncového bodu HTTPS do front-end služby ASP.NET Core
> * [Nakonfigurovat CI/CD s využitím služby Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Nastavit monitorování a diagnostiku aplikace](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Nainstalujte sadu Visual Studio 2017](https://www.visualstudio.com/) verze 15.5 nebo novější se sadami funkcí **Vývoj pro Azure** a **Vývoj pro ASP.NET a web**.
* [Nainstalujte sadu Service Fabric SDK](service-fabric-get-started.md).

## <a name="obtain-a-certificate-or-create-a-self-signed-development-certificate"></a>Získání certifikátu nebo vytvoření certifikátu podepsaného svým držitelem pro vývoj

Pro produkční aplikace používejte certifikát od [certifikační autority (CA)](https://wikipedia.org/wiki/Certificate_authority). Pro účely vývoje a testování můžete vytvořit a používat certifikát podepsaný svým držitelem. Sada Service Fabric SDK poskytuje skript *CertSetup.ps1*, který vytvoří certifikát podepsaný svým držitelem a importuje ho do úložiště certifikátů `Cert:\LocalMachine\My`. Otevřete příkazový řádek jako správce a spuštěním následujícího příkazu vytvořte certifikát s předmětem CN=localhost:

```powershell
PS C:\program files\microsoft sdks\service fabric\clustersetup\secure> .\CertSetup.ps1 -Install -CertSubjectName CN=localhost
```

Pokud už máte soubor certifikátu PFX, spuštěním následujícího příkazu importujte certifikát do úložiště certifikátů `Cert:\LocalMachine\My`:

```powershell

PS C:\mycertificates> Import-PfxCertificate -FilePath .\mysslcertificate.pfx -CertStoreLocation Cert:\LocalMachine\My -Password (ConvertTo-SecureString "!Passw0rd321" -AsPlainText -Force)


   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

## <a name="define-an-https-endpoint-in-the-service-manifest"></a>Definice koncového bodu HTTPS v manifestu služby

Spusťte sadu Visual Studio jako **správce** a otevřete řešení Voting. V Průzkumníku řešení otevřete soubor *VotingWeb/PackageRoot/ServiceManifest.xml*. Manifest služby definuje koncové body služby.  Vyhledejte část **Endpoints** (Koncové body) a upravte stávající koncový bod ServiceEndpoint.  Změňte název na EndpointHttps a nastavte protokol na *https*, typ na *Vstup* a port na *443*.  Uložte provedené změny.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="configure-kestrel-to-use-https"></a>Nakonfigurovat Kestrel k používání HTTPS

V Průzkumníku řešení otevřete soubor *VotingWeb/VotingWeb.cs*.  Nakonfigurujte Kestrel k používání HTTPS a vyhledejte certifikát v úložišti `Cert:\LocalMachine\My`. Přidejte následující příkazy using:

```csharp
using System.Net;
using Microsoft.Extensions.Configuration;
using System.Security.Cryptography.X509Certificates;
```

Aktualizujte `ServiceInstanceListener` k používání nového koncového bodu *EndpointHttps* a naslouchání na portu 443. Při konfiguraci webového hostitele na používání serveru Kestrel musíte Kestrel nakonfigurovat tak, aby naslouchal IPv6 adresám na všech síťových rozhraních: `opt.Listen(IPAddress.IPv6Any, port, listenOptions => {...}`.

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Přidejte také následující metodu, aby Kestrel mohl vyhledat certifikát v úložišti `Cert:\LocalMachine\My` pomocí předmětu.  Nahraďte &lt;your_CN_value&gt; za localhost, pokud jste pomocí předchozího příkazu PowerShellu vytvořili certifikát podepsaný svým držitelem, nebo použijte název vašeho certifikátu.

```csharp
private X509Certificate2 GetCertificateFromStore()
{
    var store = new X509Store(StoreName.My, StoreLocation.LocalMachine);
    try
    {
        store.Open(OpenFlags.ReadOnly);
        var certCollection = store.Certificates;
        var currentCerts = certCollection.Find(X509FindType.FindBySubjectDistinguishedName, "CN=<your_CN_value>", false);
        return currentCerts.Count == 0 ? null : currentCerts[0];
    }
    finally
    {
        store.Close();
    }
}
```

## <a name="give-network-service-access-to-the-certificates-private-key"></a>Poskytnutí přístupu k privátnímu klíči certifikátu účtu NETWORK SERVICE

V předchozím kroku jste importovali certifikát do úložiště `Cert:\LocalMachine\My` na vývojovém počítači.  Účtu, pod kterým se služba spouští (ve výchozím nastavení je to NETWORK SERVICE), musíte také explicitně udělit přístup k privátnímu klíči certifikátu. Můžete to provést ručně (pomocí nástroje certlm.msc), ale lepší je automaticky spustit skript PowerShellu prostřednictvím [konfigurace spouštěcího skriptu](service-fabric-run-script-at-service-startup.md) v části **SetupEntryPoint** v manifestu služby.

### <a name="configure-the-service-setup-entry-point"></a>Konfigurace vstupního bodu nastavení služby

V Průzkumníku řešení otevřete soubor *VotingWeb/PackageRoot/ServiceManifest.xml*.  V části **CodePackage** přidejte uzel **SetupEntryPoint** a pak uzel **ExeHost**.  V uzlu **ExeHost** nastavte **Program** na Setup.bat a **WorkingFolder** na CodePackage.  Při spuštění služby VotingWeb se před spuštěním VotingWeb.exe provede skript Setup.bat ve složce CodePackage.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Setup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>

    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

### <a name="add-the-batch-and-powershell-setup-scripts"></a>Přidání dávkových skriptů a instalačních skriptů PowerShellu

Pokud chcete spustit PowerShell z bodu **SetupEntryPoint**, můžete spustit PowerShell.exe v dávkovém souboru odkazujícím na soubor PowerShellu. Nejprve do projektu služby přidejte dávkový soubor.  V Průzkumníku řešení klikněte pravým tlačítkem na **VotingWeb**, vyberte **Přidat**->**Nová položka** a přidejte nový soubor Setup.bat.  Upravte soubor *Setup.bat* a přidejte následující příkaz:

```bat
powershell.exe -ExecutionPolicy Bypass -Command ".\SetCertAccess.ps1"
```

Upravte vlastnosti souboru *Setup.bat* a nastavte možnost **Kopírovat do výstupního adresáře** na hodnotu Kopírovat, pokud je novější.

![Nastavení vlastností souboru][image1]

V Průzkumníku řešení klikněte pravým tlačítkem na **VotingWeb**, vyberte **Přidat**->**Nová položka** a přidejte nový soubor SetCertAccess.ps1.  Upravte soubor *SetCertAccess.ps1* a přidejte následující skript:

```powershell
$subject="localhost"
$userGroup="NETWORK SERVICE"

Write-Host "Checking permissions to certificate $subject.." -ForegroundColor DarkCyan

$cert = (gci Cert:\LocalMachine\My\ | where { $_.Subject.Contains($subject) })[-1]

if ($cert -eq $null)
{
    $message="Certificate with subject:"+$subject+" does not exist at Cert:\LocalMachine\My\"
    Write-Host $message -ForegroundColor Red
    exit 1;
}elseif($cert.HasPrivateKey -eq $false){
    $message="Certificate with subject:"+$subject+" does not have a private key"
    Write-Host $message -ForegroundColor Red
    exit 1;
}else
{
    $keyName=$cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName

    $keyPath = "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys\"
    $fullPath=$keyPath+$keyName
    $acl=(Get-Item $fullPath).GetAccessControl('Access')


    $hasPermissionsAlready = ($acl.Access | where {$_.IdentityReference.Value.Contains($userGroup.ToUpperInvariant()) -and $_.FileSystemRights -eq [System.Security.AccessControl.FileSystemRights]::FullControl}).Count -eq 1

    if ($hasPermissionsAlready){
        Write-Host "Account $userGroup already has permissions to certificate '$subject'." -ForegroundColor Green
        return $false;
    } else {
        Write-Host "Need add permissions to '$subject' certificate..." -ForegroundColor DarkYellow

        $permission=$userGroup,"Full","Allow"
        $accessRule=new-object System.Security.AccessControl.FileSystemAccessRule $permission
        $acl.AddAccessRule($accessRule)
        Set-Acl $fullPath $acl

        Write-Output "Permissions were added"

        return $true;
    }
}

```

Upravte vlastnosti souboru *SetCertAccess.ps1* a nastavte možnost **Kopírovat do výstupního adresáře** na hodnotu Kopírovat, pokud je novější.

### <a name="run-the-setup-script-as-a-local-administrator"></a>Spuštění instalačního skriptu jako místní správce

Ve výchozím nastavení se spustitelný soubor vstupního bodu nastavení služby spouští pod stejnými přihlašovacími údaji jako Service Fabric (obvykle pod účtem NetworkService). Soubor *SetCertAccess.ps1* vyžaduje oprávnění správce. V manifestu aplikace můžete změnit oprávnění zabezpečení tak, aby se spouštěcí skript spouštěl pod účtem místního správce.

V Průzkumníku řešení otevřete soubor *Voting/ApplicationPackageRoot/ApplicationManifest.xml*. Nejprve vytvořte část **Principals** (Instanční objekty) a přidejte nového uživatele (například SetupAdminUser). Přidejte uživatelský účet SetupAdminUser do systémové skupiny Správci.
Dále v části **ServiceManifestImport** souboru VotingWebPkg nakonfigurujte **RunAsPolicy** pro použití instančního objektu SetupAdminUser na vstupní bod nastavení. Tato zásada říká Service Fabric, že se soubor Setup.bat spouští jako SetupAdminUser (s oprávněními správce).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="run-the-application-locally"></a>Místní spuštění aplikace

V Průzkumníku řešení vyberte aplikaci **Voting** a nastavte vlastnost **Adresa URL aplikace** na https://localhost:443.

Uložte všechny soubory a stisknutím klávesy F5 aplikaci místně spusťte.  Po nasazení aplikace se otevře webový prohlížeč na adrese [https://localhost:443](https://localhost:443). Pokud používáte certifikát podepsaný svým držitelem, zobrazí se upozornění, že váš počítač nedůvěřuje zabezpečení tohoto webu.  Pokračujte na webovou stránku.

![Hlasovací aplikace][image2]

## <a name="install-certificate-on-cluster-nodes"></a>Instalace certifikátu na uzlech clusteru

Než aplikaci nasadíte do Azure, nainstalujte certifikát do úložiště `Cert:\LocalMachine\My` na uzly vzdáleného clusteru.  Při spuštění webové front-end služby na uzlu clusteru vyhledá spouštěcí skript certifikát a nakonfiguruje přístupová oprávnění.

Nejprve exportujte certifikát do souboru PFX. Otevřete aplikaci certlm.msc a přejděte do části **Osobní**>**Certifikáty**.  Klikněte pravým tlačítkem na certifikát *localhost* a vyberte **Všechny úlohy**>**Exportovat**.

![Export certifikátu][image4]

V průvodci exportem zvolte **Ano, exportovat privátní klíč** a zvolte formát PFX (Personal Information Exchange).  Exportujte soubor do umístění *C:\Users\sfuser\votingappcert.pfx*.

Dále nainstalujte certifikát na vzdálený cluster pomocí rutiny [Add-AzureRmServiceFabricApplicationCertificate](/powershell/module/azurerm.servicefabric/Add-AzureRmServiceFabricApplicationCertificate).

> [!Warning]
> Certifikát podepsaný svým držitelem je dostačující pro vývoj a testování aplikací. Pro produkční aplikace místo certifikátu podepsaného svým držitelem používejte certifikát od [certifikační autority (CA)](https://wikipedia.org/wiki/Certificate_authority).

```powershell
Connect-AzureRmAccount

$vaultname="sftestvault"
$certname="VotingAppPFX"
$certpw="!Password321#"
$groupname="voting_RG"
$clustername = "votinghttps"
$ExistingPfxFilePath="C:\Users\sfuser\votingappcert.pfx"

$appcertpwd = ConvertTo-SecureString -String $certpw -AsPlainText -Force

Write-Host "Reading pfx file from $ExistingPfxFilePath"
$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2 $ExistingPfxFilePath, $certpw

$bytes = [System.IO.File]::ReadAllBytes($ExistingPfxFilePath)
$base64 = [System.Convert]::ToBase64String($bytes)

$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $certpw
   } | ConvertTo-Json

$contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
$content = [System.Convert]::ToBase64String($contentbytes)

$secretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

# Upload the certificate to the key vault as a secret
Write-Host "Writing secret to $certname in vault $vaultname"
$secret = Set-AzureKeyVaultSecret -VaultName $vaultname -Name $certname -SecretValue $secretValue

# Add a certificate to all the VMs in the cluster.
Add-AzureRmServiceFabricApplicationCertificate -ResourceGroupName $groupname -Name $clustername -SecretIdentifier $secret.Id -Verbose
```

## <a name="open-port-443-in-the-azure-load-balancer"></a>Otevřít port 443 v nástroji pro vyrovnávání zatížení Azure

Pokud ještě není otevřený, otevřete port 443 v nástroji pro vyrovnávání zatížení.

```powershell
$probename = "AppPortProbe6"
$rulename="AppPortLBRule6"
$RGname="voting_RG"
$port=443

# Get the load balancer resource
$resource = Get-AzureRmResource | Where {$_.ResourceGroupName –eq $RGname -and $_.ResourceType -eq "Microsoft.Network/loadBalancers"}
$slb = Get-AzureRmLoadBalancer -Name $resource.Name -ResourceGroupName $RGname

# Add a new probe configuration to the load balancer
$slb | Add-AzureRmLoadBalancerProbeConfig -Name $probename -Protocol Tcp -Port $port -IntervalInSeconds 15 -ProbeCount 2

# Add rule configuration to the load balancer
$probe = Get-AzureRmLoadBalancerProbeConfig -Name $probename -LoadBalancer $slb
$slb | Add-AzureRmLoadBalancerRuleConfig -Name $rulename -BackendAddressPool $slb.BackendAddressPools[0] -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -Probe $probe -Protocol Tcp -FrontendPort $port -BackendPort $port

# Set the goal state for the load balancer
$slb | Set-AzureRmLoadBalancer
```

## <a name="deploy-the-application-to-azure"></a>Nasazení aplikace v Azure

Uložte všechny soubory, přepněte z režimu Ladění do Vydání a stisknutím klávesy F6 znovu spusťte sestavení.  V Průzkumníku řešení klikněte pravým tlačítkem na aplikaci **Voting** a vyberte **Publikovat**. Vyberte koncový bod připojení clusteru vytvořeného v tématu [Nasazení aplikace do clusteru](service-fabric-tutorial-deploy-app-to-party-cluster.md) nebo vyberte jiný cluster.  Kliknutím na **Publikovat** publikujte aplikaci do vzdáleného clusteru.

Po nasazení aplikace otevřete webový prohlížeč a přejděte na adresu [https://mycluster.region.cloudapp.azure.com:443](https://mycluster.region.cloudapp.azure.com:443) (v adrese URL aktualizujte koncový bod připojení pro váš cluster). Pokud používáte certifikát podepsaný svým držitelem, zobrazí se upozornění, že váš počítač nedůvěřuje zabezpečení tohoto webu.  Pokračujte na webovou stránku.

![Hlasovací aplikace][image3]

## <a name="next-steps"></a>Další kroky

V této části kurzu jste se naučili:

> [!div class="checklist"]
> * Definovat ve službě koncový bod HTTPS
> * Nakonfigurovat Kestrel k používání HTTPS
> * Nainstalovat certifikát SSL na uzly vzdáleného clusteru
> * Poskytnout účtu NETWORK SERVICE přístup k privátnímu klíči certifikátu
> * Otevřít port 443 v nástroji pro vyrovnávání zatížení Azure
> * Nasadit aplikaci do vzdáleného clusteru

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Nakonfigurovat CI/CD s využitím služby Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)

[image1]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/SetupBatProperties.png
[image2]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppLocal.png
[image3]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppAzure.png
[image4]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/ExportCert.png
