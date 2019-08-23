---
title: Azure Service Fabric – použití spravované identity s Service Fabric aplikacemi | Microsoft Docs
description: Použití spravovaných identit z Service Fabric kód aplikace
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 7/25/2019
ms.author: atsenthi
ms.openlocfilehash: 8e535fc581e186abd032206c2bbf78623d95967f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899771"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services-preview"></a>Jak využít spravovanou identitu aplikace Service Fabric pro přístup ke službám Azure (Preview)

Service Fabric aplikace můžou využívat spravované identity pro přístup k jiným prostředkům Azure, které podporují ověřování založené na Azure Active Directory. Aplikace může získat [přístupový token](../active-directory/develop/developer-glossary.md#access-token) představující jeho identitu, která může být přiřazena systémem nebo uživateli, a použít ji jako token nosiče k ověření sebe sama na jinou službu, která se označuje také jako [chráněný server prostředků](../active-directory/develop/developer-glossary.md#resource-server). Token představuje identitu přiřazenou Service Fabric aplikaci a bude ji vydávat jenom pro prostředky Azure (včetně SF aplikací), které tuto identitu sdílejí. Podrobný popis spravovaných identit a rozdíl mezi identitami přiřazenými systémem a uživatelem přiřazenými uživateli najdete v dokumentaci [Přehled spravované identity](../active-directory/managed-identities-azure-resources/overview.md) . V rámci tohoto článku budeme označovat jako [klientskou aplikaci](../active-directory/develop/developer-glossary.md#client-application) Service Fabric aplikace s podporou identity.

> [!IMPORTANT]
> Spravovaná identita představuje přidružení mezi prostředkem Azure a instančním objektem v odpovídajícím tenantovi Azure AD, který je přidružený k předplatnému, které obsahuje daný prostředek. V kontextu Service Fabric se spravované identity podporují jenom pro aplikace nasazené jako prostředky Azure. 

> [!IMPORTANT]
> Před použitím spravované identity Service Fabric aplikace musí mít klientská aplikace udělený přístup k chráněnému prostředku. V seznamu [služeb Azure, které podporují ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) pro kontrolu podpory, a pak v dokumentaci příslušné služby najdete konkrétní kroky pro udělení přístupu identit k prostředkům, které vás zajímají. 

## <a name="acquiring-an-access-token-using-rest-api"></a>Získání přístupového tokenu pomocí REST API
V clusterech povolených pro spravovanou identitu modul runtime Service Fabric zveřejňuje koncový bod localhost, který mohou aplikace použít k získání přístupových tokenů. Koncový bod je k dispozici na každém uzlu clusteru a je přístupný všem entitám v tomto uzlu. Autorizovaní volající mohou získat přístupové tokeny voláním tohoto koncového bodu a předkládáním ověřovacího kódu; kód je generován modulem runtime Service Fabric pro každou aktivaci balíčku kódu služby a je vázán na životní cyklus procesu hostujícího daný balíček kódu služby.

Konkrétně prostředí Service Fabric služby spravované identity, která je s povolenou identitou, se dosadí s následujícími proměnnými:
- ' MSI_ENDPOINT ': koncový bod localhost, úplný s cestou, verzí rozhraní API a parametry odpovídající spravované identitě této služby
- ' MSI_SECRET ': ověřovací kód, který je neprůhledný řetězec a jednoznačně představuje službu v aktuálním uzlu

> [!NOTE]
> Názvy ' MSI_ENDPOINT ' a ' MSI_SECRET ' odkazují na předchozí označení spravovaných identit ("Identita spravované služby") a které jsou nyní zastaralé. Názvy jsou také konzistentní se stejnými názvy proměnných prostředí, které používají jiné služby Azure, které podporují spravované identity.

> [!IMPORTANT]
> Kód aplikace by měl uvažovat o hodnotě proměnné prostředí MSI_SECRET jako citlivých dat – neměl by se protokolovat ani jinak šířit. Ověřovací kód nemá žádnou hodnotu mimo místní uzel, nebo po ukončení procesu, který hostuje službu, ale představuje identitu služby Service Fabric, a měl by být zpracován se stejnými opatřeními jako přístupový token sám.

K získání tokenu klient provede následující kroky:
- Vytvoří identifikátor URI zřetězením spravovaného koncového bodu identity (MSI_ENDPOINT) s verzí rozhraní API a prostředku (cílovou skupinou), který je pro token povinný.
- vytvoří požadavek GET http pro zadaný identifikátor URI.
- Přidá ověřovací kód (hodnotu MSI_SECRET) jako hlavičku do žádosti.
- odešle žádost.

Úspěšná odpověď bude obsahovat datovou část JSON, která představuje výsledný přístupový token, a také metadata, která ho popisují. Neúspěšná odpověď bude obsahovat také vysvětlení selhání. Další podrobnosti o zpracování chyb najdete níže.

Přístupové tokeny budou uloženy v mezipaměti Service Fabric na různých úrovních (uzel, cluster, služba poskytovatele prostředků), takže úspěšná odpověď nutně neznamená, že se token vystavil přímo v reakci na žádost uživatelské aplikace. Tokeny budou uloženy v mezipaměti po dobu kratší, než je jejich životnost, a proto je zaručeno, že aplikace získá platný token. Doporučuje se, aby kód aplikace do mezipaměti získal všechny přístupové tokeny, které získá. klíč pro ukládání do mezipaměti by měl zahrnovat (odvození) cílové skupiny. 


Vzorový požadavek:
```http
GET 'http://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://keyvault.azure.com/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
kde:

| Prvek | Popis |
| ------- | ----------- |
| `GET` | Příkaz HTTP, který indikuje, že chcete načíst data z koncového bodu. V tomto případě se jedná o přístupový token OAuth. | 
| `http://localhost:2377/metadata/identity/oauth2/token` | Spravovaný koncový bod identity pro aplikace Service Fabric poskytovaný přes proměnnou prostředí MSI_ENDPOINT. |
| `api-version` | Parametr řetězce dotazu, který určuje verzi rozhraní API spravované služby tokenu identity; v současné době je `2019-07-01-preview`jediná přijatá hodnota a může se změnit. |
| `resource` | Parametr řetězce dotazu, který označuje identifikátor URI ID aplikace cílového prostředku. Tato akce se projeví jako `aud` deklarace identity (cílová skupina) vydaného tokenu. Tento příklad vyžaduje token pro přístup k Azure Key Vault, jehož identifikátor URI ID aplikace je https:\//keyvault.Azure.com/. |
| `Secret` | Pole hlavičky požadavku HTTP, které vyžaduje služba Service Fabric Managed identity token Service pro Service Fabric Services k ověření volajícího. Tuto hodnotu poskytuje modul runtime SF přes proměnnou prostředí MSI_SECRET. |


Ukázková odpověď:
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

| Prvek | Popis |
| ------- | ----------- |
| `token_type` | Typ tokenu; v tomto případě se jedná o přístupový token "nosiče", což znamená, že předvádějící ("Bearer") tohoto tokenu je zamýšlený předmět tokenu. |
| `access_token` | Požadovaný přístupový token Při volání zabezpečeného REST API se token vloží do `Authorization` pole Hlavička požadavku jako "nosič", což umožňuje rozhraní API ověřit volajícího. | 
| `expires_on` | Časové razítko vypršení platnosti přístupového tokenu; reprezentované jako počet sekund od "1970-01-01T0:0: 0Z UTC" a odpovídá `exp` deklaraci identity tokenu. V tomto případě vyprší platnost tokenu v 2019-08-08T06:10:11 + 00:00 (v dokumentu RFC 3339).|
| `resource` | Prostředek, pro který se přístupový token vystavil, zadaný přes `resource` parametr řetězce dotazu žádosti; odpovídá deklaraci identity AUD tokenu. |


## <a name="acquiring-an-access-token-using-c"></a>Získání přístupového tokenu pomocíC#
Výše uvedený postup se uvolní C#v:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;

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

                var accessToken = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                return accessToken.Trim('"');
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

            Console.WriteLine($"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Console.WriteLine("\n== Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    var secretMetadata = secretResponse.Body.ToString();
                    Console.WriteLine($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    Console.WriteLine($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
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

            Console.WriteLine(response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority"></param>
        /// <param name="resource"></param>
        /// <param name="scope"></param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            if (config.DoVerboseLogging)
                Console.WriteLine($"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";
            if (config.DoVerboseLogging)
                Console.WriteLine($"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            if (config.DoVerboseLogging)
                Console.WriteLine($"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            if (config.DoVerboseLogging)
                Console.WriteLine($"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var accessToken = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            if (config.DoVerboseLogging)
                Console.WriteLine("returning access code..");

            return accessToken.Trim('"');
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

| Prvek | Popis |
| ------- | ----------- |
| code | Kód chyby. |
| correlationId | ID korelace, které lze použít pro ladění. |
| zpráva | Podrobný popis chyby **Popisy chyb se můžou kdykoli změnit. Nezávisí na samotné chybové zprávě.**|

Ukázková Chyba:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

Následuje seznam typických chyb Service Fabric specifických pro spravované identity:

| Kód | Message | Popis | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | Tajný kód nebyl nalezen v hlavičce požadavku. | S požadavkem nebyl poskytnut ověřovací kód. | 
| ManagedIdentityNotFound | Pro zadaného hostitele aplikace se nenašla spravovaná identita. | Aplikace nemá žádnou identitu, nebo je ověřovací kód neznámý. |
| ArgumentNullOrEmpty | Parametr Resource by neměl mít hodnotu null nebo být prázdným řetězcem. | V požadavku nebyl uveden prostředek (cílová skupina). |
| InvalidApiVersion | Verze API-Version není podporovaná. Podporovaná verze je "2019-07-01-Preview". | V identifikátoru URI požadavku je uvedena chybějící nebo Nepodporovaná verze rozhraní API. |
| Nenalezeno | Došlo k chybě. | V spravovaném subsystému identity došlo k chybě, která může být mimo Service Fabric zásobník. Nejpravděpodobnější příčinou je nesprávná hodnota pro prostředek (kontrolu koncového lomítka '/'?) | 

## <a name="retry-guidance"></a>Pokyny pro opakování 

Obvykle jediný kód chyby s opakováním je 429 (příliš mnoho požadavků); interní chyby serveru/kódy chyb 5xx můžou být opakované, ale příčina může být trvalá. 

Omezení omezování se vztahují na počet volání prováděných v rámci spravovaného subsystému identity – konkrétně závislosti "nadřazených" (spravovaná identita Azure nebo služba tokenů zabezpečení). Service Fabric ukládá do mezipaměti tokeny na různých úrovních kanálu, ale u distribuované povahy zúčastněných součástí se může volající vyskytnout nekonzistentní odezvy omezování (tzn. omezení na jednom uzlu nebo instanci aplikace, ale ne na jiný uzel při požadavku na token pro stejnou identitu.) Pokud je nastavená podmínka omezování, následné požadavky ze stejné aplikace můžou selhat s kódem stavu HTTP 429 (příliš mnoho požadavků), dokud se podmínka nevymaže.  

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
V tématu [služby Azure, které podporují ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-msi.md) , najdete seznam prostředků, které podporují Azure AD, a jejich odpovídajících ID prostředků.

## <a name="next-steps"></a>Další postup
* [Nasazení aplikace Azure Service Fabric se spravovanou identitou přiřazenou systémem](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Nasazení aplikace Azure Service Fabric s uživatelem přiřazenou spravovanou identitou](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Udělení přístupu k aplikacím Azure Service Fabric k ostatním prostředkům Azure](./how-to-grant-access-other-resources.md)