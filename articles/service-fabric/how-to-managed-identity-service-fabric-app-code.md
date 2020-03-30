---
title: Použití spravované identity s aplikací
description: Jak používat spravované identity v kódu aplikace Azure Service Fabric pro přístup ke službám Azure Services. Tato funkce je ve verzi Public Preview.
ms.topic: article
ms.date: 10/09/2019
ms.openlocfilehash: 59680ec7911f55c3dc49d8834b410a039aa435dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610314"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services-preview"></a>Jak využít spravovanou identitu aplikace Service Fabric pro přístup ke službám Azure (preview)

Aplikace Service Fabric můžou využít spravované identity k přístupu k dalším prostředkům Azure, které podporují ověřování na základě Azure Active Directory. Aplikace může získat [přístupový token](../active-directory/develop/developer-glossary.md#access-token) představující jeho identitu, který může být přiřazen k systému nebo uživateli, a použít jej jako token nosiče k ověření na jinou službu , označovanou také jako [chráněný server prostředků](../active-directory/develop/developer-glossary.md#resource-server). Token představuje identitu přiřazenou aplikaci Service Fabric a bude vydán pouze prostředkům Azure (včetně aplikací SF), které tuto identitu sdílejí. Podrobný popis spravovaných identit a také rozlišení mezi identitami přiřazenými systémem a identitami přiřazenými uživatelem naleznete v dokumentaci [k přehledu spravovaných](../active-directory/managed-identities-azure-resources/overview.md) identit. Budeme odkazovat na aplikaci Service Fabric s povolenou spravovanou identitou jako [klientskou aplikaci](../active-directory/develop/developer-glossary.md#client-application) v celém tomto článku.

> [!IMPORTANT]
> Spravovaná identita představuje přidružení mezi prostředkem Azure a instancí v odpovídajícím tenantovi Azure AD přidruženém k předplatnému obsahujícímu prostředek. Jako takové, v kontextu Service Fabric spravované identity jsou podporovány pouze pro aplikace nasazené jako prostředky Azure. 

> [!IMPORTANT]
> Před použitím spravované identity aplikace Service Fabric musí být klientské aplikaci udělen přístup k chráněnému prostředku. Naleznete v seznamu [služeb Azure, které podporují ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) ke kontrole podpory a pak do dokumentace příslušné služby pro konkrétní kroky k udělení přístupu k identitě zájmu. 

## <a name="acquiring-an-access-token-using-rest-api"></a>Získání přístupového tokenu pomocí rozhraní REST API
V clusterech povolených pro spravovanou identitu zpřístupňuje runtime Service Fabric koncový bod localhost, který aplikace mohou použít k získání přístupových tokenů. Koncový bod je k dispozici na každém uzlu clusteru a je přístupný všem entitám v tomto uzlu. Autorizovaní volající mohou získat přístupové tokeny voláním tohoto koncového bodu a předložením ověřovacího kódu. kód je generován runtime Service Fabric pro každou aktivaci odlišného balíčku kódu služby a je vázán na životnost procesu hostujícího tento balíček kódu služby.

Konkrétně prostředí služby Service Fabric s podporou spravované identity bude nasazeno s následujícími proměnnými:
- 'MSI_ENDPOINT': koncový bod localhost, kompletní s cestou, verzí rozhraní API a parametry odpovídajícími spravované identitě této služby
- 'MSI_SECRET': ověřovací kód, který je neprůhledný řetězec a jednoznačně představuje službu na aktuálním uzlu

> [!NOTE]
> Názvy "MSI_ENDPOINT" a "MSI_SECRET" odkazují na předchozí označení spravovaných identit ("Identita spravované služby") a které je nyní zastaralé. Názvy jsou také konzistentní s ekvivalentní mise proměnných prostředí používané jinými službami Azure, které podporují spravované identity.

> [!IMPORTANT]
> Kód žádosti by měl hodnotu proměnné prostředí "MSI_SECRET" považovat za citlivé údaje – neměla by být zaznamenávána ani jinak šířena. Ověřovací kód nemá žádnou hodnotu mimo místní uzel nebo po ukončení procesu hostujícího službu, ale představuje identitu služby Service Fabric, a proto by měl být zpracován se stejnými opatřeními jako samotný přístupový token.

Chcete-li získat token, klient provede následující kroky:
- vytvoří identifikátor URI zřetězením koncového bodu spravované identity (MSI_ENDPOINT hodnota) s verzí rozhraní API a prostředek (cílovou skupinu) požadovaný pro token
- vytvoří požadavek HTTP GET pro zadaný identifikátor URI
- přidá ověřovací kód (MSI_SECRET hodnotu) jako hlavičku do požadavku
- podá žádost

Úspěšná odpověď bude obsahovat datovou část JSON představující výsledný přístupový token a také metadata popisující ji. Neúspěšná odpověď bude také obsahovat vysvětlení selhání. Další podrobnosti o zpracování chyb naleznete níže.

Přístupové tokeny budou ukládány service fabric na různých úrovních (uzel, cluster, služba zprostředkovatele prostředků), takže úspěšná odpověď nemusí nutně znamenat, že token byl vydán přímo v reakci na požadavek uživatelské aplikace. Tokeny budou uloženy do mezipaměti po dobu kratší než jejich životnost, a proto je zaručeno, že aplikace obdrží platný token. Doporučuje se, aby kód aplikace ukládá do mezipaměti sám všechny přístupové tokeny, které získá; klíč pro ukládání do mezipaměti by měl obsahovat (odvození) publika. 


Žádost o vzorek:
```http
GET 'http://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://keyvault.azure.com/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
kde:

| Element | Popis |
| ------- | ----------- |
| `GET` | Sloveso HTTP označující, že chcete načíst data z koncového bodu. V tomto případě přístupový token OAuth. | 
| `http://localhost:2377/metadata/identity/oauth2/token` | Koncový bod spravované identity pro aplikace Service Fabric, poskytované prostřednictvím proměnné prostředí MSI_ENDPOINT. |
| `api-version` | Parametr řetězce dotazu určující verzi rozhraní API služby Token spravované identity; v současné době je `2019-07-01-preview`jedinou přijatou hodnotou a může se změnit. |
| `resource` | Parametr řetězce dotazu označující identifikátor URI ID aplikace cílového prostředku. To se projeví `aud` jako (publikum) nárok vydaného tokenu. Tento příklad požaduje token pro přístup k trezoru klíčů Azure,\/jehož identifikátor URI ID aplikace je https: /keyvault.azure.com/. |
| `Secret` | Pole hlavičky požadavku HTTP vyžadované službou Service Fabric Managed Identity Token Service Service Service Services k ověření volajícího. Tato hodnota je poskytována runtime SF prostřednictvím proměnné prostředí MSI_SECRET. |


Odpověď vzorku:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://keyvault.azure.com/"
}
```
kde:

| Element | Popis |
| ------- | ----------- |
| `token_type` | Typ tokenu; v tomto případě přístupový token "Nosič", což znamená, že prezentující ("nosič") tohoto tokenu je zamýšleným předmětem tokenu. |
| `access_token` | Požadovaný přístupový token. Při volání zabezpečené rozhraní REST API je `Authorization` token vložen do pole hlavičky požadavku jako token "nosiče", což umožňuje rozhraní API k ověření volajícího. | 
| `expires_on` | Časové razítko vypršení platnosti přístupového tokenu; reprezentovány jako počet sekund od "1970-01-01T0:0:0Z UTC" a `exp` odpovídá deklaraci tokenu. V tomto případě platnost tokenu vyprší na 2019-08-08T06:10:11+00:00 (v RFC 3339)|
| `resource` | Prostředek, pro který byl vydán přístupový `resource` token, určený pomocí parametru řetězce dotazu požadavku; odpovídá deklaraci tokenu "aud". |


## <a name="acquiring-an-access-token-using-c"></a>Získání přístupového tokenu pomocí c #
Výše uvedené se stává v C#:

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
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("MSI_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("MSI_SECRET");
            var managedIdentityAuthenticationHeader = "secret";
            var managedIdentityApiVersion = "2019-07-01-preview";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);

            try
            {
                var response = await new HttpClient().SendAsync(requestMessage)
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
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>Přístup k trezoru klíčů z aplikace Service Fabric pomocí spravované identity
Tato ukázka vychází z výše uvedeného, aby demonstrovala přístup k tajnému klíči uloženému v trezoru klíčů pomocí spravované identity.

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
Pole Stavový kód v hlavičce odpovědi HTTP označuje stav úspěchu požadavku. stav "200 OK" označuje úspěch a odpověď bude obsahovat přístupový token, jak je popsáno výše. Následuje krátký výčet možných chybových odpovědí.

| Stavový kód | Důvod chyby | Jak zacházet |
| ----------- | ------------ | ------------- |
| 404 Nalezeno. | Neznámý ověřovací kód nebo aplikaci nebyla přiřazena spravovaná identita. | Opravit nastavení aplikace nebo token pořízení kódu. |
| 429 Příliš mnoho žádostí. |  Omezení škrticí klapky, které je uloženo aad nebo SF. | Opakujte akci s exponenciálním vypnutím. Viz pokyny níže. |
| 4xx Chyba v požadavku. | Jeden nebo více parametrů požadavku bylo nesprávné. | Neopakujte akci.  Další informace naleznete v podrobnostech o chybě.  4xx chyby jsou chyby návrhu.|
| 5xx Chyba ze služby. | Subsystém spravované identity nebo služba Azure Active Directory vrátila přechodnou chybu. | Po krátké době je bezpečné to zkusit znovu. Můžete zasáhnout omezení podmínku (429) při opakování.|

Pokud dojde k chybě, odpovídající tělo odpovědi HTTP obsahuje objekt JSON s podrobnostmi o chybě:

| Element | Popis |
| ------- | ----------- |
| kód | Kód chyby |
| correlationId | ID korelace, které lze použít pro ladění. |
| zpráva | Podrobný popis chyby. **Popisy chyb se mohou kdykoli změnit. Nezávisejte na samotné chybové zprávě.**|

Ukázková chyba:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

Následuje seznam typických chyb Service Fabric specifických pro spravované identity:

| kód | Zpráva | Popis | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | Tajný klíč nebyl v hlavičkách požadavku nalezen. | Ověřovací kód nebyl k dispozici s požadavkem. | 
| Nenalezeno identity spravované identity | Spravovaná identita nebyla pro zadaný hostitel aplikace nalezena. | Aplikace nemá žádnou identitu nebo ověřovací kód není znám. |
| ArgumentnullorEmpty | Parametr 'resource' by neměl být prázdný nebo prázdný řetězec. | Prostředek (publikum) nebyl v požadavku poskytnut. |
| Verze InvalidApiVersion | Verze rozhraní api '' není podporována. Podporovaná verze je '2019-07-01-preview'. | Chybí nebo nepodporovaná verze rozhraní API zadaná v identifikátoru URI požadavku. |
| InternalServerError | Došlo k chybě. | V subsystému spravované identity došlo k chybě, pravděpodobně mimo zásobník Service Fabric. Nejpravděpodobnější příčinou je nesprávná hodnota zadaná pro prostředek (zkontrolujte koncové '/'?) | 

## <a name="retry-guidance"></a>Pokyny pro opakování 

Obvykle je pouze opakovatelný kód chyby je 429 (příliš mnoho požadavků); interní server chyby/5xx kódy chyb může být opakovatelné, i když příčina může být trvalé. 

Omezení omezení platí pro počet volání do subsystému spravované identity – konkrétně "upstream" závislosti (služba Managed Identity Azure nebo služba zabezpečeného tokenu). Service Fabric ukládá tokeny na různých úrovních v kanálu, ale vzhledem k distribuované povaze zapojených součástí může volající dojít k nekonzistentní omezení odpovědi (tj. získat omezení na jeden uzel nebo instance aplikace, ale ne na jiném uzlu při požadování tokenu pro stejnou identitu.) Pokud je nastavena podmínka omezení, následné požadavky ze stejné aplikace může selhat se stavovým kódem HTTP 429 (příliš mnoho požadavků), dokud není podmínka vymazána.  

Doporučuje se, aby požadavky se nezdařily z důvodu omezení jsou opakovány s exponenciální backoff, takto: 

| Index volání | Opatření týkající se přijetí 429 | 
| --- | --- | 
| 1 | Počkejte 1 sekundu a opakujte akci |
| 2 | Počkejte 2 sekundy a akci opakujte |
| 3 | Počkejte 4 sekundy a akci opakujte |
| 4 | Počkejte 8 sekund a akci opakujte |
| 4 | Počkejte 8 sekund a akci opakujte |
| 5 | Počkejte 16 sekund a akci opakujte |

## <a name="resource-ids-for-azure-services"></a>ID prostředků pro služby Azure
Podívejte se na [služby Azure, které podporují ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-msi.md) pro seznam prostředků, které podporují Azure AD a jejich příslušná ID prostředků.

## <a name="next-steps"></a>Další kroky
* [Nasazení aplikace Azure Service Fabric se spravovanou identitou přiřazenou systémem](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Nasazení aplikace Azure Service Fabric s uživatelem přiřazenou spravovanou identitou](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Udělení přístupu aplikací Azure Service Fabric k dalším prostředkům Azure](./how-to-grant-access-other-resources.md)