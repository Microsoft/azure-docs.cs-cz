---
title: Konfigurace Azure Attestation pro logický Server Azure SQL
description: Nakonfigurujte Azure Attestation pro Always Encrypted s využitím zabezpečených enclaves v Azure SQL Database.
keywords: šifrování dat, šifrování SQL, šifrování databáze, citlivá data, Always Encrypted, zabezpečení enclaves, SGX, ověřování identity
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: a51aa15e1338380d4b4179e7fb8899273750c374
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106107176"
---
# <a name="configure-azure-attestation-for-your-azure-sql-logical-server"></a>Konfigurace Azure Attestation pro logický Server Azure SQL

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted se zabezpečeným enclaves pro Azure SQL Database je aktuálně ve **verzi Public Preview**.

[Ověření Microsoft Azure](../../attestation/overview.md) je řešení pro ověřování prostředí důvěryhodných spuštění (TEEs), včetně rozšíření Intel software Guard (Intel SGX) enclaves. 

Pokud chcete použít Azure Attestation k ověření identity Intel SGX enclaves, která [se používá pro Always Encrypted s zabezpečeným enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves) v Azure SQL Database, musíte:

1. Vytvořte [poskytovatele ověření identity](../../attestation/basic-concepts.md#attestation-provider) a nakonfigurujte ho pomocí doporučených zásad ověření identity.

2. Udělte vašemu poskytovateli ověřování přístup k logickému serveru Azure SQL.

> [!NOTE]
> Konfigurace ověření identity je zodpovědností Správce ověřování. [Při konfiguraci SGX enclaves a ověření identity se podívejte na role a zodpovědnosti](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation).

## <a name="requirements"></a>Požadavky

Logický Server Azure SQL a zprostředkovatel ověření identity musí patřit do stejného Azure Active Directory tenanta. Vzájemné interakce mezi klienty nejsou podporovány. 

K logickému serveru Azure SQL musí být přiřazená identita Azure AD. Jako správce ověření identity potřebujete získat identitu serveru Azure AD od správce Azure SQL Database pro tento server. Pomocí identity udělíte serveru přístup k poskytovateli ověření identity. 

Pokyny k vytvoření serveru s identitou nebo přiřazení identity existujícímu serveru pomocí PowerShellu a rozhraní příkazového řádku Azure najdete v tématu [přiřazení identity Azure AD k vašemu serveru](transparent-data-encryption-byok-configure.md#assign-an-azure-active-directory-azure-ad-identity-to-your-server).

## <a name="create-and-configure-an-attestation-provider"></a>Vytvoření a konfigurace poskytovatele ověření identity

[Poskytovatel ověření identity](../../attestation/basic-concepts.md#attestation-provider) je prostředkem v Azure Attestation, který vyhodnocuje [žádosti o ověření identity](../../attestation/basic-concepts.md#attestation-request) proti [zásadám ověřování](../../attestation/basic-concepts.md#attestation-request) a vystavuje [tokeny pro ověření identity](../../attestation/basic-concepts.md#attestation-token). 

Zásady ověření identity se zadává pomocí [gramatiky pravidla deklarace identity](../../attestation/claim-rule-grammar.md).

Microsoft doporučuje následující zásady pro ověření identity Intel SGX enclaves používané pro Always Encrypted v Azure SQL Database:

```output
version= 1.0;
authorizationrules 
{
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
};
```

Výše uvedená zásada ověřuje:

- Enklávy uvnitř Azure SQL Database nepodporuje ladění. 
  > Enclaves lze načíst s vypnutým nebo povoleným laděním. Podpora ladění je navržena tak, aby umožňovala vývojářům odstraňovat potíže s kódem spuštěným v enklávy. V produkčním systému může ladění povolit správci kontrolu obsahu enklávy, což by snížilo úroveň ochrany, kterou enklávy poskytuje. Doporučené zásady zakazují ladění, aby se zajistilo, že pokud se se zlými úmysly pokusí zapnout podporu ladění převzetím enklávy počítače, ověření identity se nezdaří. 
- ID produktu enklávy se shoduje s ID produktu přiřazeným k Always Encrypted pomocí zabezpečení enclaves.
  > Každý enklávy má jedinečné ID produktu, které odlišuje enklávy od jiných enclaves. ID produktu přiřazené k Always Encrypted enklávy je 4639. 
- Číslo verze zabezpečení (SVN) knihovny je větší než 0.
  > SVN umožňuje Microsoftu reagovat na potenciální chyby zabezpečení identifikované v kódu enklávy. V případě, že dojde k dekrytí a opravení potíží se zabezpečením, Microsoft nasadí novou verzi enklávy s novým (zvýšenou) SVN. Výše Doporučené zásady se aktualizují, aby odrážely nové SVN. Když aktualizujete zásady tak, aby odpovídala doporučeným zásadám, můžete zajistit, že pokud se správce se zlými úmysly pokusí načíst starší a nezabezpečený enklávy, ověření identity se nezdaří.
- Knihovna v enklávy byla podepsána pomocí podpisového klíče společnosti Microsoft (hodnota deklarace x-MS-SGX-mrsigner je hodnota hash podpisového klíče).
  > Jedním z hlavních cílů ověření identity je přesvědčit klienty, že binární soubor běžící v enklávy je binární soubor, který se má spustit. Zásady ověření identity poskytují pro tento účel dva mechanismy. Jedna je **mrenclave** deklarace identity, která představuje hodnotu hash binárního souboru, který má běžet v enklávy. Problém s **mrenclave** je, že se binární hodnota hash změní i v případě triviálních změn kódu, což usnadňuje revize kódu spuštěného v enklávy. Proto doporučujeme použití **mrsigner**, což je hodnota hash klíče, který se používá k podepsání binárního souboru enklávy. Když Microsoft revs enklávy, **mrsigner** zůstává stejný, dokud se podpisový klíč nemění. Tímto způsobem se dá chtít nasadit aktualizované binární soubory bez přerušujících aplikací zákazníků. 

> [!IMPORTANT]
> Poskytovatel ověření identity se vytvoří s výchozími zásadami pro Intel SGX enclaves, který neověřuje kód spuštěný uvnitř enklávy. Microsoft důrazně doporučuje nastavit výše uvedené doporučené zásady a nepoužívat výchozí zásady pro Always Encrypted s zabezpečeným enclaves.

Pokyny, jak vytvořit poskytovatele ověření identity a nakonfigurovat pomocí zásad ověření identity pomocí:

- [Rychlý Start: nastavení ověření Azure pomocí Azure Portal](../../attestation/quickstart-portal.md)
    > [!IMPORTANT]
    > Když nakonfigurujete zásady ověření identity pomocí Azure Portal, nastavte typ ověření identity na `SGX-IntelSDK` .
- [Rychlý Start: nastavení ověření Azure pomocí Azure PowerShell](../../attestation/quickstart-powershell.md)
    > [!IMPORTANT]
    > Když konfigurujete zásady ověření identity pomocí Azure PowerShell, nastavte `Tee` parametr na `SgxEnclave` .
- [Rychlý Start: nastavení ověření Azure pomocí Azure CLI](../../attestation/quickstart-azure-cli.md)
    > [!IMPORTANT]
    > Když konfigurujete zásady ověřování pomocí Azure CLI, nastavte `attestation-type` parametr na `SGX-IntelSDK` .

## <a name="determine-the-attestation-url-for-your-attestation-policy"></a>Určení adresy URL pro ověření identity pro vaše zásady ověření identity

Po nakonfigurování zásad ověření identity musíte sdílet adresu URL ověření identity, která odkazuje na zásadu, Správce aplikací, které používají Always Encrypted s zabezpečeným enclaves v Azure SQL Database. Správci aplikací nebo uživatelé aplikací budou muset nakonfigurovat své aplikace s adresou URL pro ověření identity, aby mohli spouštět příkazy, které používají zabezpečený enclaves.

### <a name="use-powershell-to-determine-the-attestation-url"></a>Použití PowerShellu k určení adresy URL ověření identity

Pomocí následujícího skriptu Určete adresu URL ověření identity:

```powershell
$attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName 
$attestationUrl = $attestationProvider.AttestUri + "/attest/SgxEnclave"
Write-Host "Your attestation URL is: " $attestationUrl 
```

### <a name="use-azure-portal-to-determine-the-attestation-url"></a>Určení adresy URL ověřování pomocí Azure Portal

1. V podokně Přehled pro poskytovatele ověření identity Zkopírujte hodnotu vlastnosti ověřit identifikátor URI do schránky. Identifikátor URI ověření by měl vypadat takto: `https://MyAttestationProvider.us.attest.azure.net` .

2. K identifikátoru URI ověření přidejte následující: `/attest/SgxEnclave` . 

Výsledná adresa URL ověření by měla vypadat takto: `https://MyAttestationProvider.us.attest.azure.net/attest/SgxEnclave`

## <a name="grant-your-azure-sql-logical-server-access-to-your-attestation-provider"></a>Udělení přístupu k vašemu poskytovateli ověření vašemu logickému serveru Azure SQL

Během pracovního postupu ověření identity logický Server Azure SQL, který obsahuje vaši databázi, zavolá poskytovatele ověření identity, aby odeslal požadavek na ověření identity. Aby mohl server Azure SQL logický server odesílat požadavky na ověření identity, musí mít server oprávnění pro `Microsoft.Attestation/attestationProviders/attestation/read` akci u poskytovatele ověření identity. Doporučeným způsobem, jak udělit oprávnění, je správce ověření identity, aby přiřadil identitu serveru Azure AD k roli čtecího modulu ověření identity pro poskytovatele ověření identity nebo jeho obsahující skupinu prostředků.

### <a name="use-azure-portal-to-assign-permission"></a>Přiřazení oprávnění pomocí Azure Portal

Pokud chcete přiřadit identitu serveru SQL Azure k roli čtenáře ověření identity pro poskytovatele ověření identity, postupujte podle obecných pokynů v tématu [přiřazení rolí Azure pomocí Azure Portal](../../role-based-access-control/role-assignments-portal.md). Když jste v podokně **Přidat přiřazení role** :

1. V rozevíracím seznamu **role** vyberte roli **čtenáře ověření identity** .
1. Do pole **Vybrat** zadejte název vašeho serveru SQL Azure, který chcete vyhledat.

Příklad najdete na následujícím snímku obrazovky.

![přiřazení role čtenáře ověření identity](./media/always-encrypted-enclaves/attestation-provider-role-assigment.png)

> [!NOTE]
> Aby se server mohl zobrazit v podokně **Přidat přiřazení role** , musí mít server přiřazenou IDENTITU Azure AD – viz [požadavky](#requirements).

### <a name="use-powershell-to-assign-permission"></a>Přiřazení oprávnění pomocí PowerShellu

1. Najděte logický Server Azure SQL.

```powershell
$serverResourceGroupName = "<server resource group name>"
$serverName = "<server name>" 
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 
```
 
2. Přiřaďte Server k roli čtenáře ověření identity pro skupinu prostředků, která obsahuje vašeho poskytovatele ověření identity.

```powershell
$attestationResourceGroupName = "<attestation provider resource group name>"
New-AzRoleAssignment -ObjectId $server.Identity.PrincipalId -RoleDefinitionName "Attestation Reader" -ResourceGroupName $attestationResourceGroupName
```

Další informace najdete v tématu [přiřazení rolí Azure pomocí Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md#assign-role-examples).

## <a name="next-steps"></a>Další kroky

- [Správa klíčů pro funkci Always Encrypted se zabezpečenými enklávami](/sql/relational-databases/security/encryption/always-encrypted-enclaves-manage-keys)

## <a name="see-also"></a>Viz také

- [Kurz: Začínáme s Always Encrypted s využitím zabezpečených enclaves v Azure SQL Database](always-encrypted-enclaves-getting-started.md)
