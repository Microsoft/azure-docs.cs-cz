---
title: Připojování ke clusteru Azure Service Fabric | Dokumentace Microsoftu
description: Popisuje, jak k ověření přístupu klientských do clusteru Service Fabric a jak pro zabezpečení komunikace mezi klienty a clusteru.
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
ms.date: 01/29/2019
ms.author: ryanwi
ms.openlocfilehash: d4760995d6bcc75bcfb974e4be6d202581828a7e
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55694088"
---
# <a name="connect-to-a-secure-cluster"></a>Připojení k zabezpečenému clusteru

Když se klient připojí k uzlu clusteru Service Fabric, klient může být ověřený a bezpečnou komunikaci naváže s využitím certifikátu zabezpečení nebo Azure Active Directory (AAD). Toto ověřování zajišťuje, že jenom Autorizovaní uživatelé můžou přistupovat ke clusteru a nasazené aplikace a provádět úlohy správy.  Certifikát nebo zabezpečení AAD musí být dříve povolen v clusteru při vytváření clusteru.  Další informace o scénáře zabezpečení clusteru najdete v tématu [clusteru zabezpečení](service-fabric-cluster-security.md). Pokud se chcete připojit k cluster zabezpečený pomocí certifikátů, [nastavení klientského certifikátu](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) na počítači, který se připojí ke clusteru. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Připojení k zabezpečenému clusteru pomocí Azure Service Fabric CLI (sfctl)

Existuje několik různých způsobů, jak připojit k zabezpečenému clusteru pomocí Service Fabric CLI (sfctl). Pokud k ověřování používáte klientský certifikát, podrobnosti o certifikátu musí odpovídat certifikátu nasazenému do uzlů clusteru. Pokud má váš certifikát certifikační autority (CA), musíte také zadat důvěryhodných certifikačních autorit.

Můžete připojit ke clusteru pomocí `sfctl cluster select` příkazu.

Klientské certifikáty lze zadat dvěma různými způsoby, buď jako dvojici certifikátu a klíče, nebo jako jeden soubor PFX. Pro soubory PEM je chráněný heslem, zobrazí se výzva automaticky k zadání hesla. Pokud jste získali klientský certifikát jako soubor PFX, nejprve převeďte soubor PFX na soubor PEM pomocí následujícího příkazu. 

```bash
openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
```

Pokud váš soubor PFX není chráněn heslem, použijte - passin průchod: pro poslední parametr.

Pokud chcete zadat klientský certifikát jako soubor pem, zadejte cestu k souboru v `--pem` argument. Příklad:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Pem, který soubory vyzve k zadání hesla před spuštěním libovolného příkazu je chráněný heslem.

Zadat certifikát, klíče dvojice použití `--cert` a `--key` argumenty k určení cesty k souborům pro každý odpovídající soubor.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Někdy certifikátů používaných pro zabezpečenou testovací nebo vývojové clustery neúspěšné ověření certifikátu. Chcete-li obejít ověření certifikátu, zadejte `--no-verify` možnost. Příklad:

> [!WARNING]
> Nepoužívejte `no-verify` možnost při připojení k produkční clustery Service Fabric.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Kromě toho můžete určit cesty k adresářům certifikátů důvěryhodné certifikační Autority nebo jednotlivých certifikátů. Pokud chcete nastavit tyto cesty použijte `--ca` argument. Příklad:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

Po připojení, byste měli být schopni [spustit další příkazy sfctl](service-fabric-cli.md) k interakci s clusterem.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Připojte se ke clusteru pomocí Powershellu
Před provedením operace v clusteru pomocí Powershellu, nejprve navázat připojení ke clusteru. Připojení clusteru se používá pro všechny následné příkazy v dané relace Powershellu.

### <a name="connect-to-an-unsecure-cluster"></a>Připojení k nezabezpečenému clusteru

Pro připojení k nezabezpečenému clusteru, zadejte adresu koncového bodu clusteru **Connect-ServiceFabricCluster** příkaz:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Připojení k zabezpečenému clusteru pomocí Azure Active Directory

Pro připojení k zabezpečenému clusteru, který používá Azure Active Directory k autorizaci přístupu správce clusteru, zadejte kryptografický otisk certifikátu clusteru a použít *AzureActiveDirectory* příznak.  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Připojení k zabezpečenému clusteru pomocí certifikátu klienta
Spusťte následující příkaz prostředí PowerShell pro připojení k zabezpečenému clusteru, který používá klientské certifikáty k autorizaci přístupu pro správce. 

#### <a name="connect-using-certificate-common-name"></a>Připojte se pomocí běžný název certifikátu
Zadejte běžný název certifikátu clusteru a běžný název certifikátu klienta, který má oprávnění pro správu clusteru. Podrobnosti o certifikátu musí odpovídat certifikátu na uzlech clusteru.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*ServerCommonName* je běžný název certifikátu serverů na uzlech clusteru nainstalované. *FindValue* je běžný název certifikát klienta pro správu. Po vyplnění parametry příkazu vypadá jako v následujícím příkladu:
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

#### <a name="connect-using-certificate-thumbprint"></a>Připojte se pomocí kryptografického otisku certifikátu
Zadejte kryptografický otisk certifikátu clusteru a kryptografický otisk klientského certifikátu, který má oprávnění pro správu clusteru. Podrobnosti o certifikátu musí odpovídat certifikátu na uzlech clusteru.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* je kryptografický otisk certifikátu serveru nainstalovaná na uzlech clusteru. *FindValue* je kryptografický otisk klientského certifikátu pro správu.  Po vyplnění parametry příkazu vypadá jako v následujícím příkladu:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Připojení k zabezpečenému clusteru pomocí služby Windows Active Directory
Pokud nasadíte samostatného clusteru pomocí zabezpečení AD, připojte se ke clusteru přidáním přepínače "WindowsCredential".

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Připojte se ke clusteru pomocí rozhraní API FabricClient
Sada Service Fabric SDK poskytuje [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) třídy za správu clusteru. Pokud chcete používat rozhraní API FabricClient, získáte balíček Microsoft.ServiceFabric NuGet.

### <a name="connect-to-an-unsecure-cluster"></a>Připojení k nezabezpečenému clusteru

Pro připojení k vzdálené nezabezpečenému clusteru, vytvořte instanci FabricClient a zadejte adresu clusteru:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

Pro kód, který je spuštěn z v rámci clusteru, například v Reliable Service, vytvořte FabricClient *bez* zadáním adresy clusteru. FabricClient se připojí k místní správy brány na uzlu, který kód aktuálně běží, jak se vyhnout další síťová směrování.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Připojení k zabezpečenému clusteru pomocí certifikátu klienta

Uzly v clusteru musí mít platné certifikáty, jejichž běžný název nebo název DNS v síti SAN se zobrazí v [RemoteCommonNames vlastnost](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials) nastavit na [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient). Provedení tohoto postupu se umožní vzájemné ověřování mezi klientem a uzly clusteru.

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

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Připojení k zabezpečenému clusteru interaktivně pomocí Azure Active Directory

Následující příklad používá Azure Active Directory pro klientský certifikát identity a server pro identitu serveru.

Dialogové okno se automaticky otevře pro interaktivní přihlášení při připojování ke clusteru.

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

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Připojení k zabezpečenému clusteru neinteraktivně pomocí Azure Active Directory

Následující příklad využívá Microsoft.IdentityModel.Clients.ActiveDirectory, verze: 2.19.208020213.

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

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Připojení k zabezpečenému clusteru bez vědomí předchozí metadata pomocí Azure Active Directory

Následující příklad používá jako neinteraktivní získání tokenu, ale stejný přístup je možné vytvářet vlastní získání tokenu interaktivní prostředí. Azure Active Directory metadata potřebná pro získání tokenu je pro čtení z konfigurace clusteru.

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

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Připojení k zabezpečenému clusteru pomocí Service Fabric Exploreru
K dosažení [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) daného clusteru, přejděte v prohlížeči na:

`http://<your-cluster-endpoint>:19080/Explorer`

Úplná adresa URL je také k dispozici v podokně essentials clusteru na portálu Azure Portal.

Pro připojení k zabezpečenému clusteru na Windows nebo OS X pomocí prohlížeče, můžete importovat certifikát klienta a prohlížeč zobrazí výzvu pro certifikát, který chcete použít pro připojení ke clusteru.  Na počítačích s Linuxem bude mít certifikát importovat nastavení pokročilé prohlížeče (každým prohlížečem má jiné mechanismy) a přejděte do umístění certifikátu na disku. Čtení [nastavení klientského certifikátu](#connectsecureclustersetupclientcert) Další informace.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Připojení k zabezpečenému clusteru pomocí Azure Active Directory

Pro připojení ke clusteru, která je zabezpečena pomocí AAD, přejděte v prohlížeči na:

`https://<your-cluster-endpoint>:19080/Explorer`

Automaticky zobrazí se výzva k přihlášení pomocí AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Připojení k zabezpečenému clusteru pomocí certifikátu klienta

Pro připojení ke clusteru, která je zabezpečena pomocí certifikátů, přejděte v prohlížeči na:

`https://<your-cluster-endpoint>:19080/Explorer`

Automaticky zobrazí se výzva k výběru certifikátu klienta.

<a id="connectsecureclustersetupclientcert"></a>

## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Nastavení certifikátu klienta na vzdáleném počítači

Minimálně dva certifikáty slouží k zabezpečení clusteru, jeden pro certifikát clusteru a serverem a druhý pro klientský přístup.  Doporučujeme také použít další sekundární certifikáty a certifikáty přístup klienta.  K zabezpečení komunikace mezi klientem a uzlem clusteru pomocí certifikátu zabezpečení, musíte nejprve získat a nainstalovat certifikát klienta. Lze nainstalovat certifikát do osobního (Moje) úložiště místního počítače nebo aktuálního uživatele.  Budete také potřebovat kryptografický otisk certifikátu serveru tak, aby se klient může ověřit cluster.

* Ve Windows: Poklikejte na soubor PFX a podle zobrazených výzev nainstalujte certifikát do svého osobního úložiště `Certificates - Current User\Personal\Certificates`. Alternativně můžete použít příkaz prostředí PowerShell:

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

    Pokud je certifikát podepsaný svým držitelem, budete muset importovat do úložiště "důvěryhodné osoby" váš počítač před použitím tohoto certifikátu pro připojení k zabezpečenému clusteru.

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
    -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
    -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

* Na počítači Mac: Poklikejte na soubor PFX a podle zobrazených výzev nainstalujte certifikát do své klíčenky.

## <a name="next-steps"></a>Další postup

* [Proces upgradu clusteru Service Fabric a očekáváních od vás](service-fabric-cluster-upgrade.md)
* [Správa aplikací Service Fabric v sadě Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Zavedení modelu stavu Service Fabric](service-fabric-health-introduction.md)
* [Zabezpečení aplikací a spustit jako](service-fabric-application-runas-security.md)
* [Začínáme se Service Fabric CLI](service-fabric-cli.md)
