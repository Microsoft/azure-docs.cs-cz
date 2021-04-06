---
title: Ověřování u prostředků Azure pomocí serverů s povoleným ARC
description: Tento článek popisuje podporu Azure Instance Metadata Service pro servery s podporou ARC a způsob ověřování u prostředků Azure a místních klíčů pomocí tajného klíče.
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 49b70928ae972da8e0a0d14d711e4b6f246cca6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96939103"
---
# <a name="authenticate-against-azure-resources-with-arc-enabled-servers"></a>Ověřování u prostředků Azure pomocí serverů s povoleným ARC

Aplikace nebo procesy běžící přímo na serverech s podporou ARC Azure mohou využívat spravované identity pro přístup k dalším prostředkům Azure, které podporují ověřování založené na Azure Active Directory. Aplikace může získat [přístupový token](../../active-directory/develop/developer-glossary.md#access-token) , který reprezentuje jeho identitu, která je přiřazená systémovým serverům s podporou ARC, a použít ji jako token nosiče k ověření sebe sama na jinou službu.

Podrobný popis spravovaných identit a rozdíl mezi identitami přiřazenými systémem a uživatelem přiřazenými uživateli najdete v dokumentaci [Přehled spravované identity](../../active-directory/managed-identities-azure-resources/overview.md) .

V tomto článku vám ukážeme, jak může server k přístupu k Azure [Key Vault](../../key-vault/general/overview.md)používat spravovanou identitu přiřazenou systémem. Jako spouštěcí Key Vault umožňuje, aby klientská aplikace používala tajný klíč k přístupu k prostředkům, které nejsou zabezpečené pomocí Azure Active Directory (AD). Například certifikáty TLS/SSL používané webovými servery služby IIS mohou být uloženy v Azure Key Vault a bezpečně nasazovat certifikáty na servery se systémem Windows nebo Linux mimo Azure.

## <a name="security-overview"></a>Přehled zabezpečení

Při připojování serveru k serverům s podporou ARC Azure se provede několik akcí, které se konfigurují pomocí spravované identity, podobně jako u virtuálního počítače Azure:

- Azure Resource Manager obdrží požadavek na povolení spravované identity přiřazené systémem na serveru s povoleným ARC.

- Azure Resource Manager ve službě Azure AD vytvoří instanční objekt pro identitu serveru. Instanční objekt se vytvoří v tenantovi Azure AD důvěryhodném pro dané předplatné.

- Azure Resource Manager konfiguruje identitu na serveru tím, že aktualizuje koncový bod identity služby Azure Instance Metadata Service (IMDS) pro [Windows](../../virtual-machines/windows/instance-metadata-service.md) nebo [Linux](../../virtual-machines/linux/instance-metadata-service.md) s ID a certifikátem klienta instančního objektu. Koncový bod je koncový bod REST dostupný jenom ze serveru pomocí známé IP adresy, která není směrovatelný. Tato služba poskytuje podmnožinu informací o metadatech pro server s podporou ARC, který usnadňuje správu a konfiguraci.

Prostředí serveru s povolenou identitou se nakonfiguruje s následujícími proměnnými na serveru s podporou ARC ve Windows:

- **IMDS_ENDPOINT**: IP adresa koncového bodu IMDS `http://localhost:40342` pro servery s podporou ARC.

- **IDENTITY_ENDPOINT**: koncový bod localhost odpovídající spravované identitě služby `http://localhost:40342/metadata/identity/oauth2/token` .

Váš kód, který je spuštěn na serveru, může požádat o token z koncového bodu služby metadat instance Azure, který je přístupný pouze ze serveru aplikace.

Proměnná prostředí systému **IDENTITY_ENDPOINT** slouží ke zjištění koncového bodu identity aplikacemi. Aplikace by se měly pokoušet načíst hodnoty **IDENTITY_ENDPOINT** a **IMDS_ENDPOINT** a použít je. Aplikace s libovolnou úrovní přístupu můžou vytvářet požadavky na koncové body. Odpovědi na metadata jsou zpracovávány jako normální a jsou předány všem procesům v počítači. Nicméně když se vytvoří žádost, která by vystavila token, potřebujeme, aby klient poskytoval tajný klíč k ověření, že mají přístup k datům, která jsou dostupná jenom pro uživatele s vyššími oprávněními.

## <a name="prerequisites"></a>Požadavky

- Porozumění spravovaným identitám.
- Server se připojil a zaregistroval se servery s podporou ARC.
- Jste členem [skupiny vlastníků](../../role-based-access-control/built-in-roles.md#owner)* * ve skupině předplatného nebo skupiny prostředků, aby se prováděly požadované kroky pro vytvoření prostředku a správu rolí.
- Azure Key Vault ukládat a načítat vaše přihlašovací údaje. a přiřaďte identitám ARC Azure přístup k trezoru klíčů.

    - Pokud nemáte vytvořený Key Vault, přečtěte si téma [vytvoření Key Vault](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#create-a-key-vault-).
    - Pokud chcete nakonfigurovat přístup pomocí spravované identity, kterou používá server, přečtěte si téma [udělení přístupu pro Linux](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#grant-access) nebo [udělení přístupu pro Windows](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#grant-access). Pro krok číslo 5 se chystáte zadat název serveru s povoleným ARC. Pokud to chcete provést pomocí PowerShellu, přečtěte si téma [přiřazení zásad přístupu pomocí PowerShellu](../../key-vault/general/assign-access-policy-powershell.md).

## <a name="acquiring-an-access-token-using-rest-api"></a>Získání přístupového tokenu pomocí REST API

Metoda získání a použití spravované identity přiřazené systémem k ověřování pomocí prostředků Azure se podobá tomu, jak se provádí s virtuálním počítačem Azure.

Pro systém Windows Server s povoleným ARC pomocí PowerShellu vyvoláte webový požadavek, který získá token z místního hostitele v konkrétním portu. Zadejte požadavek s použitím IP adresy nebo proměnné prostředí **IDENTITY_ENDPOINT**.

```powershell
$apiVersion = "2020-06-01"
$resource = "https://management.azure.com/"
$endpoint = "{0}?resource={1}&api-version={2}" -f $env:IDENTITY_ENDPOINT,$resource,$apiVersion
$secretFile = ""
try
{
    Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'} -UseBasicParsing
}
catch
{
    $wwwAuthHeader = $_.Exception.Response.Headers["WWW-Authenticate"]
    if ($wwwAuthHeader -match "Basic realm=.+")
    {
        $secretFile = ($wwwAuthHeader -split "Basic realm=")[1]
    }
}
Write-Host "Secret file path: " $secretFile`n
$secret = cat -Raw $secretFile
$response = Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'; Authorization="Basic $secret"} -UseBasicParsing
if ($response)
{
    $token = (ConvertFrom-Json -InputObject $response.Content).access_token
    Write-Host "Access token: " $token
}
```

Následující odpověď je příklad, který je vrácen:

:::image type="content" source="media/managed-identity-authentication/powershell-token-output-example.png" alt-text="Úspěšné načtení přístupového tokenu pomocí PowerShellu":::

Pro linuxový Server s podporou ARC pomocí bash vyvoláte webový požadavek, který získá token z místního hostitele na konkrétním portu. Zadejte následující požadavek s použitím IP adresy nebo proměnné prostředí **IDENTITY_ENDPOINT**. K dokončení tohoto kroku budete potřebovat klienta SSH.

```bash
ChallengeTokenPath=$(curl -s -D - -H Metadata:true "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com" | grep Www-Authenticate | cut -d "=" -f 2 | tr -d "[:cntrl:]")
ChallengeToken=$(cat $ChallengeTokenPath)
if [ $? -ne 0 ]; then
    echo "Could not retrieve challenge token, double check that this command is run with root privileges."
else
    curl -s -H Metadata:true -H "Authorization: Basic $ChallengeToken" "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com"
fi
```

Následující odpověď je příklad, který je vrácen:

:::image type="content" source="media/managed-identity-authentication/bash-token-output-example.png" alt-text="Úspěšné načtení přístupového tokenu pomocí bash.":::

Odpověď zahrnuje přístupový token, který potřebujete pro přístup k jakémukoli prostředku v Azure. Chcete-li dokončit konfiguraci pro ověření Azure Key Vault, přečtěte si téma [přístup Key Vault se systémem Windows](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#access-data) nebo [přístup Key Vault se systémem Linux](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#access-data).

## <a name="next-steps"></a>Další kroky

- Další informace o Azure Key Vault najdete v tématu [Key Vault Overview](../../key-vault/general/overview.md).

- Naučte se, jak přiřadit přístup spravovaných identit k prostředku [pomocí PowerShellu](../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) nebo pomocí [Azure CLI](../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md).