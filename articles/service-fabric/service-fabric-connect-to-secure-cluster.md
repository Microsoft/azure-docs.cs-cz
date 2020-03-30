---
title: Bezpečné připojení ke clusteru Azure Service Fabric
description: Popisuje, jak ověřit přístup klientů ke clusteru Service Fabric a jak zabezpečit komunikaci mezi klienty a clusterem.
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: a1f4abbabe428a09492efefca4a8da9801b9f68d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258574"
---
# <a name="connect-to-a-secure-cluster"></a>Připojení k zabezpečenému clusteru

Když se klient připojí k uzlu clusteru Service Fabric, může být klient ověřena a zabezpečena pomocí zabezpečení certifikátů nebo služby Azure Active Directory (AAD). Toto ověřování zajišťuje, že pouze oprávnění uživatelé mají přístup ke clusteru a nasazeným aplikacím a provádějí úlohy správy.  Zabezpečení certifikátu nebo aad musí být v clusteru při vytvoření clusteru dříve povoleno.  Další informace o scénářích zabezpečení clusteru naleznete v [tématu Zabezpečení clusteru](service-fabric-cluster-security.md). Pokud se připojujete ke clusteru zabezpečenému certifikáty, [nastavte klientský certifikát](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) v počítači, který se připojuje ke clusteru. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Připojení k zabezpečenému clusteru pomocí příkazového příkazu Azure Service Fabric (sfctl)

Existuje několik různých způsobů, jak se připojit k zabezpečenému clusteru pomocí cli service fabric (sfctl). Pokud k ověřování používáte klientský certifikát, podrobnosti o certifikátu musí odpovídat certifikátu nasazenému do uzlů clusteru. Pokud certifikát má certifikační autority (CERTIFIKAČNÍ autority), je třeba navíc zadat důvěryhodné certifikační autority.

Ke clusteru se můžete `sfctl cluster select` připojit pomocí příkazu.

Klientské certifikáty lze zadat dvěma různými způsoby, buď jako certifikát a pár klíčů, nebo jako jeden soubor PFX. U souborů PEM chráněných heslem budete automaticky vyzváni k zadání hesla. Pokud jste získali klientský certifikát jako soubor PFX, nejprve převeďte soubor PFX do souboru PEM pomocí následujícího příkazu. 

```shell
openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
```

Pokud váš soubor .pfx není chráněn heslem, použijte -passin pass: pro poslední parametr.

Chcete-li zadat klientský certifikát jako soubor pem, zadejte cestu k souboru v argumentu. `--pem` Například:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Heslem chráněné pem soubory vyzve k zadání hesla před spuštěním libovolného příkazu.

Chcete-li určit certifikát, dvojice `--cert` `--key` klíčů použijte argumenty a k určení cest k jednotlivým souborům.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Někdy certifikáty používané k zabezpečení testovacích nebo dev clusterů nepodaří ověření certifikátu. Chcete-li obejít `--no-verify` ověření certifikátu, zadejte možnost. Například:

> [!WARNING]
> Nepoužívejte `no-verify` tuto možnost při připojování k produkčním clusterům Service Fabric.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Kromě toho můžete určit cesty k adresářům důvěryhodných certifikátů certifikační autority nebo jednotlivých certifikátů. Chcete-li zadat tyto `--ca` cesty, použijte argument. Například:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

Po připojení byste měli být schopni [spustit další příkazy sfctl](service-fabric-cli.md) pro interakci s clusterem.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Připojení ke clusteru pomocí PowerShellu
Před provedením operací v clusteru prostřednictvím prostředí PowerShell nejprve nastavte připojení ke clusteru. Připojení clusteru se používá pro všechny následné příkazy v dané relaci prostředí PowerShell.

### <a name="connect-to-an-unsecure-cluster"></a>Připojení k nezabezpečenému clusteru

Chcete-li se připojit k nezabezpečenému clusteru, zadejte **příkazu Connect-ServiceFabricCluster** adresu koncového bodu clusteru:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Připojení k zabezpečenému clusteru pomocí služby Azure Active Directory

Chcete-li se připojit k zabezpečenému clusteru, který používá službu Azure Active Directory k autorizaci přístupu správce clusteru, zadejte kryptografický otisk certifikátu clusteru a použijte příznak *AzureActiveDirectory.*  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Připojení k zabezpečenému clusteru pomocí klientského certifikátu
Spusťte následující příkaz Prostředí PowerShell a připojte se k zabezpečenému clusteru, který k autorizaci přístupu správce používá klientské certifikáty. 

#### <a name="connect-using-certificate-common-name"></a>Připojit pomocí běžného názvu certifikátu
Zadejte běžný název certifikátu clusteru a běžný název klientského certifikátu, kterému byla udělena oprávnění pro správu clusteru. Podrobnosti o certifikátu se musí shodovat s certifikátem v uzlech clusteru.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*ServerCommonName* je běžný název certifikátu serveru nainstalovaného v uzlech clusteru. *FindValue* je běžný název klientského certifikátu správce. Po vyplnění parametrů bude příkaz vypadat jako v následujícím příkladu:
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

#### <a name="connect-using-certificate-thumbprint"></a>Připojení pomocí kryptografického otisku certifikátu
Zadejte kryptografický otisk certifikátu clusteru a kryptografický otisk klientského certifikátu, kterému byla udělena oprávnění pro správu clusteru. Podrobnosti o certifikátu se musí shodovat s certifikátem v uzlech clusteru.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* je kryptografický otisk certifikátu serveru nainstalovaného v uzlech clusteru. *FindValue* je kryptografický otisk klientského certifikátu správce.  Po vyplnění parametrů bude příkaz vypadat jako v následujícím příkladu:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Připojení k zabezpečenému clusteru pomocí služby Windows Active Directory
Pokud je samostatný cluster nasazen pomocí zabezpečení služby AD, připojte se ke clusteru připojením přepínače "WindowsCredential".

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Připojení ke clusteru pomocí infrastruktury klienta API
Sada Service Fabric SDK poskytuje třídu [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) pro správu clusteru. Chcete-li použít rozhraní API infrastruktury klienta, získejte balíček Microsoft.ServiceFabric NuGet.

### <a name="connect-to-an-unsecure-cluster"></a>Připojení k nezabezpečenému clusteru

Chcete-li se připojit ke vzdálenému nezabezpečenému clusteru, vytvořte instanci fabricclient a zadejte adresu clusteru:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

Pro kód, který je spuštěn z v rámci clusteru, například ve spolehlivé službě, vytvořte FabricClient *bez* zadání adresy clusteru. FabricClient se připojí k místní bráně pro správu v uzlu, na který je aktuálně spuštěn kód, čímž se vyhne dalšímu směrování do sítě.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Připojení k zabezpečenému clusteru pomocí klientského certifikátu

Uzly v clusteru musí mít platné certifikáty, jejichž běžný název nebo název DNS v programu SAN se zobrazí ve [vlastnosti RemoteCommonNames](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials) nastavené na [službě FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient). Po tomto procesu umožňuje vzájemné ověřování mezi klientem a uzly clusteru.

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

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Interaktivní připojení k zabezpečenému clusteru pomocí služby Azure Active Directory

Následující příklad používá službu Azure Active Directory pro identitu klienta a certifikát serveru pro identitu serveru.

Po připojení ke clusteru se automaticky objeví dialogové okno pro interaktivní přihlášení.

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

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Připojení k zabezpečenému clusteru neinteraktivně pomocí služby Azure Active Directory

Následující příklad závisí na Microsoft.IdentityModel.Clients.ActiveDirectory, Verze: 2.19.208020213.

Další informace o získávání tokenů AAD naleznete v tématu [Microsoft.IdentityModel.Clients.ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx).

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

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Připojení k zabezpečenému clusteru bez předchozích znalostí metadat pomocí služby Azure Active Directory

Následující příklad používá neinteraktivní získávání tokenů, ale stejný přístup lze použít k vytvoření vlastního interaktivního prostředí pro získávání tokenů. Metadata služby Azure Active Directory potřebná pro získání tokenů se čtou z konfigurace clusteru.

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

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Připojení k zabezpečenému clusteru pomocí Průzkumníka prostředků infrastruktury služby
Chcete-li se dostat do [Aplikace Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) pro daný cluster, přejděte prohlížeč na:

`http://<your-cluster-endpoint>:19080/Explorer`

Úplná adresa URL je taky dostupná v podokně základů clusteru na webu Azure Portal.

Pro připojení k zabezpečenému clusteru v systému Windows nebo OS X pomocí prohlížeče můžete importovat klientský certifikát a prohlížeč vás vyzve k použití certifikátu pro připojení ke clusteru.  Na počítačích s Linuxem bude nutné certifikát importovat pomocí pokročilých nastavení prohlížeče (každý prohlížeč má jiné mechanismy) a nasměrovat jej na umístění certifikátu na disku. Další informace naleznete [v článek Nastavení klientského certifikátu.](#connectsecureclustersetupclientcert)

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Připojení k zabezpečenému clusteru pomocí služby Azure Active Directory

Chcete-li se připojit ke clusteru zabezpečenému pomocí služby AAD, namiřte prohlížeč na:

`https://<your-cluster-endpoint>:19080/Explorer`

Budete automaticky vyzváni k přihlášení pomocí aplikace AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Připojení k zabezpečenému clusteru pomocí klientského certifikátu

Chcete-li se připojit ke clusteru, který je zabezpečen certifikáty, namiřte prohlížeč na:

`https://<your-cluster-endpoint>:19080/Explorer`

Budete automaticky vyzváni k výběru klientského certifikátu.

<a id="connectsecureclustersetupclientcert"></a>

## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Nastavení klientského certifikátu ve vzdáleném počítači

K zabezpečení clusteru by měly být použity alespoň dva certifikáty, jeden pro certifikát clusteru a serveru a druhý pro přístup klienta.  Doporučujeme také použít další sekundární certifikáty a certifikáty klientského přístupu.  Chcete-li zabezpečit komunikaci mezi klientem a uzlem clusteru pomocí zabezpečení certifikátu, musíte nejprve získat a nainstalovat klientský certifikát. Certifikát lze nainstalovat do osobního (moje) úložiště místního počítače nebo aktuálního uživatele.  Potřebujete také kryptografický otisk certifikátu serveru, aby klient mohl cluster ověřovat.

* Windows: Dvakrát klikněte na soubor PFX a podle zobrazených výzev nainstalujte certifikát do svého osobního úložiště `Certificates - Current User\Personal\Certificates`. Případně můžete použít příkaz PowerShell:

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

    Pokud se jedná o certifikát podepsaný svým držitelem, musíte jej před připojením k zabezpečenému clusteru importovat do úložiště důvěryhodných osob počítače.

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
    -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
    -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

* Mac: Dvakrát klikněte na soubor PFX a podle zobrazených výzev nainstalujte certifikát do své klíčenky.

## <a name="next-steps"></a>Další kroky

* [Proces upgradu clusteru Service Fabric a očekávání od vás](service-fabric-cluster-upgrade.md)
* [Správa aplikací Service Fabric v sadě Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Úvod modelu service fabric zdraví](service-fabric-health-introduction.md)
* [Zabezpečení aplikací a runas](service-fabric-application-runas-security.md)
* [Začínáme se Service Fabric CLI](service-fabric-cli.md)
