---
title: Zabezpečené připojení ke clusteru Azure Service Fabric | Microsoft Docs
description: Popisuje, jak k ověření přístupu klientů k cluster Service Fabric a postupy pro zabezpečení komunikace mezi klienty a cluster.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 759a539e-e5e6-4055-bff5-d38804656e10
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/10/2018
ms.author: ryanwi
ms.openlocfilehash: 055b2cdadf48c31635183b73f214f2215477c9d7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="connect-to-a-secure-cluster"></a>Připojení k zabezpečenému clusteru

Když se klient připojí k uzlu clusteru Service Fabric, klient může být ověřený a zabezpečenou komunikaci navázat pomocí certifikátů, zabezpečení nebo Azure Active Directory (AAD). Toto ověřování zajistí jenom autorizovaným uživatelům můžete přístup ke clusteru a nasazené aplikace a provádět úlohy správy.  Certifikát nebo AAD zabezpečení musí být dříve povolen v clusteru při vytvoření clusteru.  Další informace o scénáře zabezpečení clusteru najdete v tématu [clusteru zabezpečení](service-fabric-cluster-security.md). Pokud se připojujete ke clusteru zabezpečené pomocí certifikátů, [nastavit certifikát klienta](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) na počítači, který připojí ke clusteru. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Připojení ke clusteru zabezpečené pomocí Azure Service Fabric rozhraní příkazového řádku (sfctl)

Pro připojení k zabezpečení clusteru Service Fabric rozhraní příkazového řádku (sfctl) pomocí několika různými způsoby. Pokud k ověřování používáte klientský certifikát, podrobnosti o certifikátu musí odpovídat certifikátu nasazenému do uzlů clusteru. Pokud má váš certifikát certifikační autority (CA), budete muset zadat kromě důvěryhodných certifikačních autorit.

Připojením ke clusteru pomocí `sfctl cluster select` příkaz.

Klientské certifikáty lze zadat v dva různé způsoby, buď jako dvojice certifikát a klíč, nebo jako soubor pem jeden. Pro chráněné heslem `pem` soubory, zobrazí se výzva automaticky k zadání hesla.

Pokud chcete zadat klientský certifikát jako soubor pem, zadejte cestu k souboru v `--pem` argument. Příklad:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Pem, který soubory výzvou k zadání hesla před spuštěním jakékoli příkazu je chráněný heslem.

Pokud chcete zadat certifikát, klíče dvojice použití `--cert` a `--key` argumenty k určení cesty k souborům pro každý odpovídající soubor.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Někdy certifikátů používaných pro zabezpečenou testu nebo dev clustery nezdaří ověření certifikátu. Obejít ověření certifikátu, zadejte `--no-verify` možnost. Příklad:

> [!WARNING]
> Nepoužívejte `no-verify` možnost při připojování k produkci clusterů Service Fabric.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Kromě toho můžete určit cest k adresářům certifikátů důvěryhodné certifikační Autority nebo jednotlivých certifikátů. Pokud chcete zadat tyto cesty, použijte `--ca` argument. Příklad:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

Když se připojíte, byste měli moct [spouštění jiných příkazů sfctl](service-fabric-cli.md) k interakci s clusterem.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Připojení ke clusteru pomocí prostředí PowerShell
Před provedením operace na clusteru s podporou pomocí prostředí PowerShell, nejprve vytvořit připojení ke clusteru. Připojení clusteru se používá pro všechny následné příkazy v dané relace prostředí PowerShell.

### <a name="connect-to-an-unsecure-cluster"></a>Připojení ke clusteru nezabezpečená

Pro připojení k nezabezpečená clusteru, zadejte adresu koncový bod clusteru k **Connect-ServiceFabricCluster** příkaz:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Připojení ke clusteru zabezpečené pomocí Azure Active Directory

Pro připojení k zabezpečení clusteru, který používá Azure Active Directory k autorizaci přístupu správce clusteru, zadejte kryptografický otisk certifikátu clusteru a použít *azureactivedirectory selhala* příznak.  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Připojení ke clusteru zabezpečené pomocí klientského certifikátu
Spusťte následující příkaz prostředí PowerShell pro připojení k zabezpečení clusteru, který používá klientské certifikáty k autorizaci přístupu správce. 

#### <a name="connect-using-certificate-common-name"></a>Připojení pomocí běžný název certifikátu
Zadejte běžný název certifikátu clusteru a běžný název certifikátu klienta, kterému byla udělena oprávnění pro správu clusteru. Podrobnosti o certifikátu musí odpovídat certifikát na uzlech clusteru.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*Možností ServerCommonName* je běžný název certifikátu serveru nainstalované na uzlech clusteru. *FindValue* je běžný název certifikátu Správce klienta. Když jsou vyplněna parametry, vypadá příkaz v následujícím příkladu:
```powershell
$ClusterName= "sf-commonnametest-scus.southcentralus.cloudapp.azure.com:19000"
$certCN = "sfrpe2eetest.southcentralus.cloudapp.azure.com"

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName $certCN  `
    -FindType FindBySubjectName `
    -FindValue $certCN `
    -StoreLocation CurrentUser `
    -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Připojení ke clusteru zabezpečené pomocí služby Windows Active Directory
Pokud váš cluster samostatné se nasazuje pomocí zabezpečení AD, připojte se ke clusteru připojením přepínač "WindowsCredential".

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Připojení ke clusteru pomocí rozhraní API FabricClient
Sada Service Fabric SDK poskytuje [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) třídy pro správu clusteru. Pokud chcete používat rozhraní API FabricClient, získáte balíček Microsoft.ServiceFabric NuGet.

### <a name="connect-to-an-unsecure-cluster"></a>Připojení ke clusteru nezabezpečená

Připojení ke vzdálené zabezpečená clusteru, vytvořte instanci FabricClient a zadejte adresu clusteru:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

Pro kód, který je spuštěn z v rámci clusteru, například v spolehlivě, vytvořte FabricClient *bez* zadání adresy clusteru. FabricClient připojí k bráně místní správy na uzlu, na který, aktuálně běží kód zabraňující další síti směrování.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Připojení ke clusteru zabezpečené pomocí klientského certifikátu

Uzly v clusteru musí mít platné certifikáty, jejichž běžný název nebo název DNS v síti SAN se zobrazí v [RemoteCommonNames vlastnost](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials#System_Fabric_X509Credentials_RemoteCommonNames) nastavit na [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient). Provedení tohoto postupu se umožní vzájemné ověřování mezi klientem a uzly clusteru.

```csharp
using System.Fabric;
using System.Security.Cryptography.X509Certificates;

string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
var fc = new FabricClient(xc, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "My";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = clientCertThumb;
    xc.RemoteCommonNames.Add(name);
    xc.RemoteCertThumbprints.Add(serverCertThumb);
    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Připojení ke clusteru zabezpečené interaktivně pomocí služby Azure Active Directory

Následující příklad používá Azure Active Directory pro klienta certifikát identity a server pro identitu serveru.

Dialogové okno se automaticky objeví pro interaktivní přihlášení při připojování ke clusteru.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Připojení ke clusteru zabezpečené interaktivně pomocí služby Azure Active Directory

Následující příklad spoléhá na Microsoft.IdentityModel.Clients.ActiveDirectory, verze: 2.19.208020213.

Další informace o získání tokenu AAD najdete v tématu [Microsoft.IdentityModel.Clients.ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx).

```csharp
string tenantId = "C15CFCEA-02C1-40DC-8466-FBD0EE0B05D2";
string clientApplicationId = "118473C2-7619-46E3-A8E4-6DA8D5F56E12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob");

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(
    string tenantId,
    string resource,
    string clientId,
    string redirectUri)
{
    string authorityFormat = @"https://login.microsoftonline.com/{0}";
    string authority = string.Format(CultureInfo.InvariantCulture, authorityFormat, tenantId);
    var authContext = new AuthenticationContext(authority);

    var authResult = authContext.AcquireToken(
        resource,
        clientId,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Připojení ke clusteru zabezpečené bez vědomí předchozí metadat pomocí služby Azure Active Directory

Následující příklad používá neinteraktivní získání tokenu, ale ve stejný přístup lze použít k vytvoření vlastní interaktivní tokenu získávání zkušeností. Azure Active Directory metadata potřebná pro získání tokenu je pro čtení z konfigurace clusteru.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

fc.ClaimsRetrieval += (o, e) =>
{
    return GetAccessToken(e.AzureActiveDirectoryMetadata);
};

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(AzureActiveDirectoryMetadata aad)
{
    var authContext = new AuthenticationContext(aad.Authority);

    var authResult = authContext.AcquireToken(
        aad.ClusterApplication,
        aad.ClientApplication,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

<a id="connectsecureclustersfx"></a>

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Připojení k zabezpečení clusteru pomocí Service Fabric Exploreru
K dosažení [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) pro daný cluster, přejděte v prohlížeči na:

`http://<your-cluster-endpoint>:19080/Explorer`

Úplná adresa URL je také dostupná v podokně clusteru essentials na portálu Azure.

Pro připojení k zabezpečení clusteru v systému Windows nebo OS X pomocí prohlížeče, můžete importovat certifikát klienta a prohlížeč zobrazí výzvu pro certifikát, který chcete použít pro připojení ke clusteru.  Na počítačích s Linuxem bude mít certifikát pomocí nastavení pokročilé prohlížeče (každým prohlížečem, který má jiné mechanismy) lze importovat a přejděte do umístění certifikátu na disku.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Připojení ke clusteru zabezpečené pomocí Azure Active Directory

Chcete-li připojit ke clusteru, který je zabezpečen AAD, přejděte v prohlížeči na:

`https://<your-cluster-endpoint>:19080/Explorer`

Automaticky zobrazí se výzva k přihlášení pomocí AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Připojení ke clusteru zabezpečené pomocí klientského certifikátu

Chcete-li připojit ke clusteru, který je zabezpečen pomocí certifikátů, přejděte v prohlížeči na:

`https://<your-cluster-endpoint>:19080/Explorer`

Automaticky zobrazí se výzva k výběru certifikátu klienta.

<a id="connectsecureclustersetupclientcert"></a>
## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Nastavit klientský certifikát na vzdáleném počítači
Alespoň dva certifikáty slouží k zabezpečení clusteru, jeden pro certifikát cluster a server a druhý pro klientský přístup.  Doporučujeme také používat další sekundární certifikátů a certifikátů přístup klienta.  K zabezpečení komunikace mezi klientem a uzlem clusteru, používá certifikát zabezpečení, musíte nejprve získat a nainstalovat certifikát klienta. Lze nainstalovat certifikát do osobního (My) úložiště místního počítače nebo aktuálního uživatele.  Musíte taky kryptografický otisk certifikátu serveru, takže klient může ověřit clusteru.

Spuštěním následující rutiny prostředí PowerShell nastavit klientský certifikát na počítači, ze kterého jste přístup ke clusteru.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
        -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

Pokud je certifikát podepsaný svým držitelem, musíte ho importovat do úložiště "důvěryhodné osoby" váš počítač před použitím tohoto certifikátu pro připojení do clusteru s podporou zabezpečení.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
-FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
-Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

## <a name="next-steps"></a>Další postup

* [Proces upgradu Service Fabric Cluster a očekávání od vás](service-fabric-cluster-upgrade.md)
* [Správu aplikací Service Fabric v sadě Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Stav služby Fabric modelu Úvod](service-fabric-health-introduction.md)
* [Zabezpečení aplikací a RunAs](service-fabric-application-runas-security.md)
* [Začínáme se Service Fabric CLI](service-fabric-cli.md)
