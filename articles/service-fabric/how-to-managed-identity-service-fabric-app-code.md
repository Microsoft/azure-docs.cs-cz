---
title: Použití spravované identity s aplikací
description: Použití spravovaných identit v Azure Service Fabric kódu aplikace pro přístup ke službám Azure.
ms.topic: article
ms.date: 10/09/2019
ms.openlocfilehash: e26a29020f26583f7e4aa16434c7e8647ba9a5a3
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98871057"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services"></a>Jak využít spravovanou identitu aplikace Service Fabric pro přístup ke službám Azure

Service Fabric aplikace můžou využívat spravované identity pro přístup k jiným prostředkům Azure, které podporují ověřování založené na Azure Active Directory. Aplikace může získat [přístupový token](../active-directory/develop/developer-glossary.md#access-token) představující jeho identitu, která může být přiřazena systémem nebo uživateli, a použít ji jako token nosiče k ověření sebe sama na jinou službu, která se označuje také jako [chráněný server prostředků](../active-directory/develop/developer-glossary.md#resource-server). Token představuje identitu přiřazenou Service Fabric aplikaci a bude ji vydávat jenom pro prostředky Azure (včetně SF aplikací), které tuto identitu sdílejí. Podrobný popis spravovaných identit a rozdíl mezi identitami přiřazenými systémem a uživatelem přiřazenými uživateli najdete v dokumentaci [Přehled spravované identity](../active-directory/managed-identities-azure-resources/overview.md) . V rámci tohoto článku budeme označovat jako [klientskou aplikaci](../active-directory/develop/developer-glossary.md#client-application) Service Fabric aplikace s podporou identity.

Podívejte se na doprovodnou ukázkovou aplikaci, která demonstruje použití [spravovaných identit aplikací a Service Fabric](https://github.com/Azure-Samples/service-fabric-managed-identity) přiřazených uživatelem pomocí Reliable Services a kontejnerů.

> [!IMPORTANT]
> Spravovaná identita představuje přidružení mezi prostředkem Azure a instančním objektem v odpovídajícím tenantovi Azure AD, který je přidružený k předplatnému, které obsahuje daný prostředek. V kontextu Service Fabric se spravované identity podporují jenom pro aplikace nasazené jako prostředky Azure. 

> [!IMPORTANT]
> Před použitím spravované identity Service Fabric aplikace musí mít klientská aplikace udělený přístup k chráněnému prostředku. V seznamu [služeb Azure, které podporují ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) pro kontrolu podpory, a pak v dokumentaci příslušné služby najdete konkrétní kroky pro udělení přístupu identit k prostředkům, které vás zajímají. 
 

## <a name="leverage-a-managed-identity-using-azureidentity"></a>Využití spravované identity pomocí Azure. identity

Sada Azure identity SDK teď podporuje Service Fabric. Použití Azure. identity umožňuje psaní kódu pro použití Service Fabric spravovaných identit aplikace snadněji, protože zpracovává načítání tokenů, ukládání tokenů do mezipaměti a ověřování serveru. Při přístupu k většině prostředků Azure je koncept tokenu skrytý.

Podpora Service Fabric je k dispozici v následujících verzích pro tyto jazyky: 
- [C# ve verzi 1.3.0](https://www.nuget.org/packages/Azure.Identity). Podívejte se na [ukázku v jazyce C#](https://github.com/Azure-Samples/service-fabric-managed-identity).
- [Python ve verzi 1.5.0](https://pypi.org/project/azure-identity/) Podívejte se na [ukázku Pythonu](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/identity/azure-identity/tests/managed-identity-live/service-fabric/service_fabric.md).
- [Java ve verzi 1.2.0](/java/api/overview/azure/identity-readme).

Ukázka jazyka C# pro inicializaci přihlašovacích údajů a použití přihlašovacích údajů k načtení tajného kódu z Azure Key Vault:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;

namespace MyMIService
{
    internal sealed class MyMIService : StatelessService
    {
        protected override async Task RunAsync(CancellationToken cancellationToken)
        {
            try
            {
                // Load the service fabric application managed identity assigned to the service
                ManagedIdentityCredential creds = new ManagedIdentityCredential();

                // Create a client to keyvault using that identity
                SecretClient client = new SecretClient(new Uri("https://mykv.vault.azure.net/"), creds);

                // Fetch a secret
                KeyVaultSecret secret = (await client.GetSecretAsync("mysecret", cancellationToken: cancellationToken)).Value;
            }
            catch (CredentialUnavailableException e)
            {
                // Handle errors with loading the Managed Identity
            }
            catch (RequestFailedException)
            {
                // Handle errors with fetching the secret
            }
            catch (Exception e)
            {
                // Handle generic errors
            }
        }
    }
}

```

## <a name="acquiring-an-access-token-using-rest-api"></a>Získání přístupového tokenu pomocí REST API
V clusterech povolených pro spravovanou identitu modul runtime Service Fabric zveřejňuje koncový bod localhost, který mohou aplikace použít k získání přístupových tokenů. Koncový bod je k dispozici na každém uzlu clusteru a je přístupný všem entitám v tomto uzlu. Autorizovaní volající mohou získat přístupové tokeny voláním tohoto koncového bodu a předkládáním ověřovacího kódu; kód je generován modulem runtime Service Fabric pro každou aktivaci balíčku kódu služby a je vázán na životní cyklus procesu hostujícího daný balíček kódu služby.

Konkrétně prostředí Service Fabric služby spravované identity, která je s povolenou identitou, se dosadí s následujícími proměnnými:
- ' IDENTITY_ENDPOINT ': koncový bod localhost odpovídající spravované identitě služby
- ' IDENTITY_HEADER ': jedinečný ověřovací kód představující službu v aktuálním uzlu
- ' IDENTITY_SERVER_THUMBPRINT ': kryptografický otisk serveru spravované identity Service Fabric

> [!IMPORTANT]
> Kód aplikace by měl uvažovat o hodnotě proměnné prostředí IDENTITY_HEADER jako citlivých dat – neměl by se protokolovat ani jinak šířit. Ověřovací kód nemá žádnou hodnotu mimo místní uzel, nebo po ukončení procesu, který hostuje službu, ale představuje identitu služby Service Fabric, a měl by být zpracován se stejnými opatřeními jako přístupový token sám.

K získání tokenu klient provede následující kroky:
- Vytvoří identifikátor URI zřetězením spravovaného koncového bodu identity (IDENTITY_ENDPOINT hodnoty) s verzí rozhraní API a prostředkem (cílovou skupinou), který je pro token vyžadován.
- vytvoří požadavek GET http (s) pro zadaný identifikátor URI.
- Přidá odpovídající logiku ověřování certifikátů serveru.
- Přidá ověřovací kód (IDENTITY_HEADER hodnotu) jako hlavičku do žádosti.
- odešle žádost.

Úspěšná odpověď bude obsahovat datovou část JSON, která představuje výsledný přístupový token, a také metadata, která ho popisují. Neúspěšná odpověď bude obsahovat také vysvětlení selhání. Další podrobnosti o zpracování chyb najdete níže.

Přístupové tokeny budou uloženy v mezipaměti Service Fabric na různých úrovních (uzel, cluster, služba poskytovatele prostředků), takže úspěšná odpověď nutně neznamená, že se token vystavil přímo v reakci na žádost uživatelské aplikace. Tokeny budou uloženy v mezipaměti po dobu kratší, než je jejich životnost, a proto je zaručeno, že aplikace získá platný token. Doporučuje se, aby kód aplikace do mezipaměti získal všechny přístupové tokeny, které získá. klíč pro ukládání do mezipaměti by měl zahrnovat (odvození) cílové skupiny. 

Vzorový požadavek:
```http
GET 'https://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://vault.azure.net/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
kde:

| Element | Popis |
| ------- | ----------- |
| `GET` | Příkaz HTTP, který indikuje, že chcete načíst data z koncového bodu. V tomto případě se jedná o přístupový token OAuth. | 
| `https://localhost:2377/metadata/identity/oauth2/token` | Spravovaný koncový bod identity pro aplikace Service Fabric poskytovaný přes proměnnou prostředí IDENTITY_ENDPOINT. |
| `api-version` | Parametr řetězce dotazu, který určuje verzi rozhraní API spravované služby tokenu identity; v současné době je jediná přijatá hodnota `2019-07-01-preview` a může se změnit. |
| `resource` | Parametr řetězce dotazu, který označuje identifikátor URI ID aplikace cílového prostředku. Tato akce se projeví jako `aud` deklarace identity (cílová skupina) vydaného tokenu. Tento příklad vyžaduje token pro přístup k Azure Key Vault, jehož identifikátor URI ID aplikace je https: \/ /Vault.Azure.NET/. |
| `Secret` | Pole hlavičky požadavku HTTP, které vyžaduje služba Service Fabric Managed identity token Service pro Service Fabric Services k ověření volajícího. Tuto hodnotu poskytuje modul runtime SF prostřednictvím proměnné prostředí IDENTITY_HEADER. |


Ukázková odpověď:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://vault.azure.net/"
}
```
kde:

| Element | Popis |
| ------- | ----------- |
| `token_type` | Typ tokenu; v tomto případě se jedná o přístupový token "nosiče", což znamená, že předvádějící ("Bearer") tohoto tokenu je zamýšlený předmět tokenu. |
| `access_token` | Požadovaný přístupový token Při volání zabezpečeného REST API se token vloží do `Authorization` pole Hlavička požadavku jako "nosič", což umožňuje rozhraní API ověřit volajícího. | 
| `expires_on` | Časové razítko vypršení platnosti přístupového tokenu; reprezentované jako počet sekund od "1970-01-01T0:0: 0Z UTC" a odpovídá `exp` deklaraci identity tokenu. V tomto případě vyprší platnost tokenu v 2019-08-08T06:10:11 + 00:00 (v dokumentu RFC 3339).|
| `resource` | Prostředek, pro který se přístupový token vystavil, zadaný přes `resource` parametr řetězce dotazu žádosti; odpovídá deklaraci identity AUD tokenu. |


## <a name="acquiring-an-access-token-using-c"></a>Získání přístupového tokenu pomocí jazyka C #
Výše uvedený postup se bude v jazyce C#:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;
    using Newtonsoft.Json;

    /// <summary>
    /// Type representing the response of the SF Managed Identity endpoint for token acquisition requests.
    /// </summary>
    [JsonObject]
    public sealed class ManagedIdentityTokenResponse
    {
        [JsonProperty(Required = Required.Always, PropertyName = "token_type")]
        public string TokenType { get; set; }

        [JsonProperty(Required = Required.Always, PropertyName = "access_token")]
        public string AccessToken { get; set; }

        [JsonProperty(PropertyName = "expires_on")]
        public string ExpiresOn { get; set; }

        [JsonProperty(PropertyName = "resource")]
        public string Resource { get; set; }
    }

    /// <summary>
    /// Sample class demonstrating access token acquisition using Managed Identity.
    /// </summary>
    public sealed class AccessTokenAcquirer
    {
        /// <summary>
        /// Acquire an access token.
        /// </summary>
        /// <returns>Access token</returns>
        public static async Task<string> AcquireAccessTokenAsync()
        {
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("IDENTITY_HEADER");
            var managedIdentityServerThumbprint = Environment.GetEnvironmentVariable("IDENTITY_SERVER_THUMBPRINT");
            // Latest api version, 2019-07-01-preview is still supported.
            var managedIdentityApiVersion = Environment.GetEnvironmentVariable("IDENTITY_API_VERSION");
            var managedIdentityAuthenticationHeader = "secret";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);
            
            var handler = new HttpClientHandler();
            handler.ServerCertificateCustomValidationCallback = (httpRequestMessage, cert, certChain, policyErrors) =>
            {
                // Do any additional validation here
                if (policyErrors == SslPolicyErrors.None)
                {
                    return true;
                }
                return 0 == string.Compare(cert.GetCertHashString(), managedIdentityServerThumbprint, StringComparison.OrdinalIgnoreCase);
            };

            try
            {
                var response = await new HttpClient(handler).SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var tokenResponseString = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                var tokenResponseObject = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);

                return tokenResponseObject.AccessToken;
            }
            catch (Exception ex)
            {
                string errorText = String.Format("{0} \n\n{1}", ex.Message, ex.InnerException != null ? ex.InnerException.Message : "Acquire token failed");

                Console.WriteLine(errorText);
            }

            return String.Empty;
        }
    } // class AccessTokenAcquirer
} // namespace Azure.ServiceFabric.ManagedIdentity.Samples
```
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>Přístup k Key Vault z aplikace Service Fabric pomocí spravované identity
Tato ukázka sestaví na výše uvedeném obrázku, který předvádí přístup k tajnému kódu uloženému v Key Vault pomocí spravované identity.

```C#
        /// <summary>
        /// Probe the specified secret, displaying metadata on success.  
        /// </summary>
        /// <param name="vault">vault name</param>
        /// <param name="secret">secret name</param>
        /// <param name="version">secret version id</param>
        /// <returns></returns>
        public async Task<string> ProbeSecretAsync(string vault, string secret, string version)
        {
            // initialize a KeyVault client with a managed identity-based authentication callback
            var kvClient = new Microsoft.Azure.KeyVault.KeyVaultClient(new Microsoft.Azure.KeyVault.KeyVaultClient.AuthenticationCallback((a, r, s) => { return AuthenticationCallbackAsync(a, r, s); }));

            Log(LogLevel.Info, $"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Log(LogLevel.Info, "\n== {DateTime.UtcNow.ToString()}: Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    response = String.Format($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    response = String.Format($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
                }
            }
            catch (Microsoft.Rest.ValidationException ve)
            {
                response = String.Format($"encountered REST validation exception 0x{ve.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}' from {ve.Source}: {ve.Message}");
            }
            catch (KeyVaultErrorException kvee)
            {
                response = String.Format($"encountered KeyVault exception 0x{kvee.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {kvee.Response.ReasonPhrase} ({kvee.Response.StatusCode})");
            }
            catch (Exception ex)
            {
                // handle generic errors here
                response = String.Format($"encountered exception 0x{ex.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {ex.Message}");
            }

            Log(LogLevel.Info, response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority">The expected issuer of the access token, from the KV authorization challenge.</param>
        /// <param name="resource">The expected audience of the access token, from the KV authorization challenge.</param>
        /// <param name="scope">The expected scope of the access token; not currently used.</param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            Log(LogLevel.Verbose, $"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");
            var encodedResource = HttpUtility.UrlEncode(resource);

            // This sample does not illustrate the caching of the access token, which the user application is expected to do.
            // For a given service, the caching key should be the (encoded) resource uri. The token should be cached for a period
            // of time at most equal to its remaining validity. The 'expires_on' field of the token response object represents
            // the number of seconds from Unix time when the token will expire. You may cache the token if it will be valid for at
            // least another short interval (1-10s). If its expiration will occur shortly, don't cache but still return it to the 
            // caller. The MI endpoint will not return an expired token.
            // Sample caching code:
            //
            // ManagedIdentityTokenResponse tokenResponse;
            // if (responseCache.TryGetCachedItem(encodedResource, out tokenResponse))
            // {
            //     Log(LogLevel.Verbose, $"cache hit for key '{encodedResource}'");
            //
            //     return tokenResponse.AccessToken;
            // }
            //
            // Log(LogLevel.Verbose, $"cache miss for key '{encodedResource}'");
            //
            // where the response cache is left as an exercise for the reader. MemoryCache is a good option, albeit not yet available on .net core.

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={encodedResource}";
            Log(LogLevel.Verbose, $"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            Log(LogLevel.Verbose, $"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            Log(LogLevel.Verbose, $"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var tokenResponseString = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            var tokenResponse = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);
            Log(LogLevel.Verbose, "deserialized token response; returning access code..");

            // Sample caching code (continuation):
            // var expiration = DateTimeOffset.FromUnixTimeSeconds(Int32.Parse(tokenResponse.ExpiresOn));
            // if (expiration > DateTimeOffset.UtcNow.AddSeconds(5.0))
            //    responseCache.AddOrUpdate(encodedResource, tokenResponse, expiration);

            return tokenResponse.AccessToken;
        }

        private string PrintSecretBundleMetadata(SecretBundle bundle)
        {
            StringBuilder strBuilder = new StringBuilder();

            strBuilder.AppendFormat($"\n\tid: {bundle.Id}\n");
            strBuilder.AppendFormat($"\tcontent type: {bundle.ContentType}\n");
            strBuilder.AppendFormat($"\tmanaged: {bundle.Managed}\n");
            strBuilder.AppendFormat($"\tattributes:\n");
            strBuilder.AppendFormat($"\t\tenabled: {bundle.Attributes.Enabled}\n");
            strBuilder.AppendFormat($"\t\tnbf: {bundle.Attributes.NotBefore}\n");
            strBuilder.AppendFormat($"\t\texp: {bundle.Attributes.Expires}\n");
            strBuilder.AppendFormat($"\t\tcreated: {bundle.Attributes.Created}\n");
            strBuilder.AppendFormat($"\t\tupdated: {bundle.Attributes.Updated}\n");
            strBuilder.AppendFormat($"\t\trecoveryLevel: {bundle.Attributes.RecoveryLevel}\n");

            return strBuilder.ToString();
        }

        private enum LogLevel
        {
            Info,
            Verbose
        };

        private void Log(LogLevel level, string message)
        {
            if (level != LogLevel.Verbose
                || config.DoVerboseLogging)
            {
                Console.WriteLine(message);
            }
        }

```

## <a name="error-handling"></a>Zpracování chyb
Pole Stavový kód hlavičky HTTP Response indikuje stav úspěch žádosti; stav "200 OK" indikuje úspěch a odpověď bude obsahovat přístupový token, jak je popsáno výše. Níže jsou uvedené krátké výčty možných chybových odpovědí.

| Stavový kód | Důvod chyby | Postup zpracování |
| ----------- | ------------ | ------------- |
| 404 nebyl nalezen. | Neznámý ověřovací kód nebo aplikace nebyla přiřazena spravovaná identita. | Opravit nastavení aplikace nebo kód pro získání tokenu. |
| 429 příliš mnoho požadavků. |  Dosáhli jste limitu omezení, který je uložený pomocí AAD nebo SF. | Zkuste to znovu s exponenciálním omezení rychlosti. Viz pokyny níže. |
| Chyba 4xx v žádosti. | Minimálně jeden parametr požadavku byl nesprávný. | Neopakovat akci.  Další informace najdete v podrobnostech o chybě.  chyby 4xx jsou chyby v době návrhu.|
| 5xx Chyba služby. | Spravovaný subsystém identity nebo Azure Active Directory vrátil přechodnou chybu. | Po krátké době to může být bezpečné. Při opakovaném pokusu se můžete setkat s podmínkou omezení (429).|

Pokud dojde k chybě, odpovídající tělo odpovědi HTTP obsahuje objekt JSON s podrobnostmi o chybě:

| Element | Popis |
| ------- | ----------- |
| kód | Kód chyby |
| correlationId | ID korelace, které lze použít pro ladění. |
| zpráva | Podrobný popis chyby **Popisy chyb se můžou kdykoli změnit. Nezávisí na samotné chybové zprávě.**|

Ukázková Chyba:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

Následuje seznam typických chyb Service Fabric specifických pro spravované identity:

| Kód | Zpráva | Popis | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | Tajný kód nebyl nalezen v hlavičce požadavku. | S požadavkem nebyl poskytnut ověřovací kód. | 
| ManagedIdentityNotFound | Pro zadaného hostitele aplikace se nenašla spravovaná identita. | Aplikace nemá žádnou identitu, nebo je ověřovací kód neznámý. |
| ArgumentNullOrEmpty | Parametr Resource by neměl mít hodnotu null nebo být prázdným řetězcem. | V požadavku nebyl uveden prostředek (cílová skupina). |
| InvalidApiVersion | Verze API-Version není podporovaná. Podporovaná verze je "2019-07-01-Preview". | V identifikátoru URI požadavku je uvedena chybějící nebo Nepodporovaná verze rozhraní API. |
| InternalServerError | Došlo k chybě. | V spravovaném subsystému identity došlo k chybě, která může být mimo Service Fabric zásobník. Nejpravděpodobnější příčinou je nesprávná hodnota pro prostředek (kontrolu koncového lomítka '/'?) | 

## <a name="retry-guidance"></a>Pokyny pro opakování 

Obvykle jediný kód chyby s opakováním je 429 (příliš mnoho požadavků); interní chyby serveru/kódy chyb 5xx můžou být opakované, ale příčina může být trvalá. 

Omezení omezování se vztahují na počet volání prováděných v rámci spravovaného subsystému identity – konkrétně závislosti "nadřazených" (spravovaná identita Azure nebo služba tokenů zabezpečení). Service Fabric ukládá do mezipaměti tokeny na různých úrovních kanálu, ale s ohledem na distribuovanou povahu zúčastněných komponent, volající může zasahovat do nekonzistentních reakcí omezování (tzn. omezení na jednom uzlu nebo instanci aplikace, ale ne na jiném uzlu při požadavku na token pro stejnou identitu). Pokud je nastavená podmínka omezování, následné požadavky ze stejné aplikace můžou selhat s kódem stavu HTTP 429 (příliš mnoho požadavků), dokud se podmínka nevymaže.  

Doporučuje se, aby se požadavky nezdařily, protože došlo k opakovanému pokusu pomocí exponenciálního omezení rychlosti, jak je znázorněno níže: 

| Index volání | Akce při přijetí 429 | 
| --- | --- | 
| 1 | Počkejte 1 sekundu a zkuste to znovu. |
| 2 | Počkejte 2 sekundy a zkuste to znovu. |
| 3 | Počkejte 4 sekundy a zkuste to znovu. |
| 4 | Počkejte 8 sekund a zkuste to znovu. |
| 4 | Počkejte 8 sekund a zkuste to znovu. |
| 5 | Počkejte 16 sekund a zkuste to znovu. |

## <a name="resource-ids-for-azure-services"></a>ID prostředků pro služby Azure
V tématu [služby Azure, které podporují ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) , najdete seznam prostředků, které podporují Azure AD, a jejich odpovídajících ID prostředků.

## <a name="next-steps"></a>Další kroky
* [Nasazení aplikace Azure Service Fabric se spravovanou identitou přiřazenou systémem](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Nasazení aplikace Azure Service Fabric s uživatelem přiřazenou spravovanou identitou](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Udělení přístupu k aplikacím Azure Service Fabric k ostatním prostředkům Azure](./how-to-grant-access-other-resources.md)
* [Prozkoumejte ukázkovou aplikaci s využitím Service Fabric spravované identity](https://github.com/Azure-Samples/service-fabric-managed-identity)