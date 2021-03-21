---
title: Zabezpečené připojení k clusteru Azure Service Fabric
description: Popisuje ověření přístupu klienta ke clusteru Service Fabric a způsob zabezpečení komunikace mezi klienty a clusterem.
ms.topic: conceptual
ms.date: 01/29/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 3a669f7400eb2daf2d71f6caed41560020699c58
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791117"
---
# <a name="connect-to-a-secure-cluster"></a>Připojení k zabezpečenému clusteru

Když se klient připojí k uzlu Service Fabric clusteru, může být klient ověřen a zabezpečená komunikace vytvořená pomocí zabezpečení certifikátů nebo Azure Active Directory (AAD). Toto ověřování zajišťuje, že přístup ke clusteru a nasazování aplikací a provádění úloh správy bude mít jenom autorizovaní uživatelé.  V době, kdy byl cluster vytvořen, musí být zabezpečení certifikátu nebo AAD v clusteru dříve povolené.  Další informace o scénářích zabezpečení clusteru najdete v tématu [zabezpečení clusteru](service-fabric-cluster-security.md). Pokud se připojujete ke clusteru zabezpečenému pomocí certifikátů, [nastavte klientský certifikát](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) na počítači, který se připojuje ke clusteru. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Připojení k zabezpečenému clusteru pomocí Azure Service Fabric CLI (sfctl)

Existuje několik různých způsobů, jak se připojit k zabezpečenému clusteru pomocí rozhraní příkazového řádku Service Fabric (sfctl). Pokud k ověřování používáte klientský certifikát, podrobnosti o certifikátu musí odpovídat certifikátu nasazenému do uzlů clusteru. Pokud váš certifikát má certifikační autority (CA), musíte taky zadat důvěryhodné certifikační autority.

Pomocí příkazu se můžete připojit ke clusteru `sfctl cluster select` .

Klientské certifikáty lze zadat dvěma různými způsoby, buď jako certifikát, jako dvojici klíčů, nebo jako jeden soubor PFX. Pro soubory PEM chráněné heslem se zobrazí výzva k automatickému zadání hesla. Pokud jste certifikát klienta získali jako soubor PFX, nejprve převeďte soubor PFX na soubor PEM pomocí následujícího příkazu. 

```shell
openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
```

Pokud Váš soubor. pfx není chráněný heslem, použijte parametr-Passin Pass: pro poslední parametr.

Pokud chcete zadat klientský certifikát jako soubor PEM, zadejte cestu k souboru v `--pem` argumentu. Například:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Soubory PEM chráněné heslem se před spuštěním libovolného příkazu zobrazí výzva k zadání hesla.

Chcete-li zadat certifikát, dvojici klíčů `--cert` použijte `--key` argumenty a k určení cest k souborům pro každý příslušný soubor.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Někdy certifikáty, které se používají k zabezpečení testovacích nebo vývojových clusterů, selžou při ověřování certifikátů. Pokud chcete obejít ověření certifikátu, zadejte `--no-verify` možnost. Například:

> [!WARNING]
> Nepoužívejte `no-verify` možnost při připojování k produkčním Service Fabric clusterům.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Kromě toho můžete zadat cesty k adresářům důvěryhodných certifikátů CA nebo jednotlivých certifikátů. K určení těchto cest použijte `--ca` argument. Například:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

Po připojení byste měli být schopni [spustit další příkazy sfctl](service-fabric-cli.md) pro interakci s clusterem.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Připojení ke clusteru pomocí PowerShellu
Než provedete operace s clusterem přes PowerShell, napřed navažte připojení ke clusteru. Připojení clusteru se používá pro všechny následné příkazy v dané relaci prostředí PowerShell.

### <a name="connect-to-an-unsecure-cluster"></a>Připojení k nezabezpečenému clusteru

Pokud se chcete připojit k nezabezpečenému clusteru, zadejte adresu koncového bodu clusteru do příkazu **Connect-ServiceFabricCluster** :

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Připojení k zabezpečenému clusteru pomocí Azure Active Directory

Pokud se chcete připojit k zabezpečenému clusteru, který používá Azure Active Directory k autorizaci přístupu správce clusteru, zadejte kryptografický otisk certifikátu clusteru a použijte příznak *azureactivedirectory selhala* .  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Připojení k zabezpečenému clusteru pomocí klientského certifikátu
Spusťte následující příkaz prostředí PowerShell pro připojení k zabezpečenému clusteru, který používá klientské certifikáty k autorizaci přístupu správce. 

#### <a name="connect-using-certificate-common-name"></a>Připojit pomocí společného názvu certifikátu
Zadejte běžný název certifikátu clusteru a běžný název klientského certifikátu, kterému bylo uděleno oprávnění ke správě clusteru. Podrobnosti o certifikátu se musí shodovat s certifikátem na uzlech clusteru.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*ServerCommonName* je běžný název certifikátu serveru nainstalovaného na uzlech clusteru. *FindValue* je běžný název certifikátu klienta správce. Po vyplnění parametrů bude příkaz vypadat jako v následujícím příkladu:
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

#### <a name="connect-using-certificate-thumbprint"></a>Připojit pomocí kryptografického otisku certifikátu
Zadejte kryptografický otisk certifikátu clusteru a kryptografický otisk klientského certifikátu, kterému bylo uděleno oprávnění ke správě clusteru. Podrobnosti o certifikátu se musí shodovat s certifikátem na uzlech clusteru.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* je kryptografický otisk certifikátu serveru nainstalovaného na uzlech clusteru. *FindValue* je kryptografický otisk certifikátu klienta správce.  Po vyplnění parametrů bude příkaz vypadat jako v následujícím příkladu:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Připojení k zabezpečenému clusteru pomocí služby Windows Active Directory
Pokud je váš samostatný cluster nasazený pomocí zabezpečení služby AD, připojte se ke clusteru připojením přepínače "WindowsCredential".

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Připojení ke clusteru pomocí rozhraní FabricClient API
Sada Service Fabric SDK poskytuje třídu [FabricClient](/dotnet/api/system.fabric.fabricclient) pro správu clusteru. Pokud chcete používat rozhraní API FabricClient, Získejte balíček NuGet Microsoft. ServiceFabric.

### <a name="connect-to-an-unsecure-cluster"></a>Připojení k nezabezpečenému clusteru

Pokud se chcete připojit ke vzdálenému nezabezpečenému clusteru, vytvořte instanci FabricClient a zadejte adresu clusteru:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

Pro kód, který běží v rámci clusteru, například ve spolehlivé službě, vytvořte FabricClient *bez* zadání adresy clusteru. FabricClient se připojí k místní bráně pro správu na uzlu, na kterém je kód aktuálně spuštěný, a vyloučí se tak další síťové směrování.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Připojení k zabezpečenému clusteru pomocí klientského certifikátu

Uzly v clusteru musí mít platné certifikáty, jejichž běžný název nebo název DNS v síti SAN se zobrazí ve [vlastnosti RemoteCommonNames](/dotnet/api/system.fabric.x509credentials) nastavené na [FabricClient](/dotnet/api/system.fabric.fabricclient). Následující postup umožňuje vzájemné ověřování mezi klientem a uzly clusteru.

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

Následující příklad používá Azure Active Directory pro identitu klienta a certifikát serveru pro identitu serveru.

Po připojení ke clusteru se automaticky zobrazí dialogové okno pro interaktivní přihlašování.

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

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Připojení k zabezpečenému clusteru bez interaktivně pomocí Azure Active Directory

Následující příklad spoléhá na Microsoft. IdentityModel. clients. Active, Version: 2.19.208020213.

Další informace o získání tokenu AAD najdete v tématu [Microsoft. IdentityModel. clients. Active](/dotnet/api/microsoft.identitymodel.clients.activedirectory).

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

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Připojení k zabezpečenému clusteru bez předchozích znalostí metadat pomocí Azure Active Directory

Následující příklad používá neinteraktivní získání tokenu, ale stejný přístup lze použít k vytvoření vlastního interaktivního prostředí pro získání interaktivního tokenu. Metadata Azure Active Directory potřebná pro získání tokenu jsou čtena z konfigurace clusteru.

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

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Připojení k zabezpečenému clusteru pomocí Service Fabric Explorer
Pokud chcete získat [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) pro daný cluster, najeďte do prohlížeče:

`http://<your-cluster-endpoint>:19080/Explorer`

Úplná adresa URL je také k dispozici v podokně základy clusteru Azure Portal.

Pro připojení k zabezpečenému clusteru v systému Windows nebo OS X pomocí prohlížeče můžete importovat certifikát klienta a v prohlížeči se zobrazí výzva k zadání certifikátu, který chcete použít pro připojení ke clusteru.  V počítačích se systémem Linux bude nutné certifikát importovat pomocí pokročilých nastavení prohlížeče (každý prohlížeč má různé mechanismy) a nasměrovat ho na umístění certifikátu na disku. Další informace najdete v tématu [nastavení klientského certifikátu](#connectsecureclustersetupclientcert) .

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Připojení k zabezpečenému clusteru pomocí Azure Active Directory

Pokud se chcete připojit ke clusteru, který je zabezpečený pomocí AAD, najeďte na prohlížeč:

`https://<your-cluster-endpoint>:19080/Explorer`

Automaticky se zobrazí výzva, abyste se přihlásili pomocí AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Připojení k zabezpečenému clusteru pomocí klientského certifikátu

Pokud se chcete připojit ke clusteru, který je zabezpečený pomocí certifikátů, najeďte na prohlížeč:

`https://<your-cluster-endpoint>:19080/Explorer`

Automaticky se zobrazí výzva k výběru klientského certifikátu.

<a id="connectsecureclustersetupclientcert"></a>

## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Nastavení klientského certifikátu na vzdáleném počítači

Pro zabezpečení clusteru, jednoho pro cluster a certifikát serveru a další pro klientský přístup by se měly použít aspoň dva certifikáty.  Doporučujeme také použít další sekundární certifikáty a certifikáty klientského přístupu.  Aby bylo možné zabezpečit komunikaci mezi klientem a uzlem clusteru pomocí zabezpečení certifikátů, musíte nejprve získat a nainstalovat certifikát klienta. Certifikát může být nainstalován do osobního (osobního) úložiště místního počítače nebo aktuálního uživatele.  Budete také potřebovat kryptografický otisk certifikátu serveru, aby mohl klient ověřit cluster.

* Windows: Dvakrát klikněte na soubor PFX a podle zobrazených výzev nainstalujte certifikát do svého osobního úložiště `Certificates - Current User\Personal\Certificates`. Případně můžete použít příkaz prostředí PowerShell:

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

    Pokud se jedná o certifikát podepsaný svým držitelem, budete ho muset před použitím tohoto certifikátu importovat do úložiště Důvěryhodné osoby v počítači, aby se mohl připojit k zabezpečenému clusteru.

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
    -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
    -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

* Mac: Dvakrát klikněte na soubor PFX a podle zobrazených výzev nainstalujte certifikát do své klíčenky.

## <a name="next-steps"></a>Další kroky

* [Service Fabric proces upgradu clusteru a očekávání od vás](service-fabric-cluster-upgrade.md)
* [Správa aplikací Service Fabric v aplikaci Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Úvod do modelu Service Fabric Health](service-fabric-health-introduction.md)
* [Zabezpečení aplikací a RunAs](service-fabric-application-runas-security.md)
* [Začínáme se Service Fabric CLI](service-fabric-cli.md)
