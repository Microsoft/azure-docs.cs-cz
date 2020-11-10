---
title: Ověřování ve službě Azure Key Vault
description: Zjistěte, jak ověřit a Azure Key Vault
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 08/27/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 7423114d2da23207dfcc1a329675b8c902b1f320
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445536"
---
# <a name="authenticate-to-azure-key-vault"></a>Ověřování ve službě Azure Key Vault

Azure Key Vault umožňuje ukládat tajné klíče a řídit jejich distribuci v centralizovaném, zabezpečeném cloudovém úložišti, což eliminuje nutnost ukládat přihlašovací údaje v aplikacích. Pro přístup k těmto tajným klíčům se aplikace potřebují ověřit pouze pomocí Key Vault za běhu.

## <a name="app-identity-and-security-principals"></a>Identita aplikace a objekty zabezpečení

Ověřování pomocí Key Vault funguje ve spojení s [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md), která zodpovídá za ověřování identity libovolného **objektu zabezpečení**.

Objekt zabezpečení je objekt, který představuje uživatele, skupinu, službu nebo aplikaci požadující přístup k prostředkům Azure. Azure přiřadí jedinečné **ID objektu** každému objektu zabezpečení.

* Objekt zabezpečení **uživatele** identifikuje jednotlivce, který má profil v Azure Active Directory.

* Objekt zabezpečení **skupiny** identifikuje sadu uživatelů vytvořených v Azure Active Directory. Všechny role nebo oprávnění přiřazených ke skupině jsou udělena všem uživatelům v rámci dané skupiny.

* **Instanční objekt** je typ instančního objektu, který slouží k identitě aplikace nebo služby, což znamená, že místo uživatele nebo skupiny říkáte část kódu. ID objektu instančního objektu se označuje jako jeho **ID klienta** a funguje jako jeho uživatelské jméno. **Tajný kód klienta** instančního objektu funguje jako jeho heslo.

Pro aplikace existují dva způsoby, jak získat instanční objekt:

* Doporučené: pro aplikaci povolte **spravovanou identitu** přiřazenou systémem.

    Díky spravované identitě Azure interně spravuje instanční objekt aplikace a automaticky ověřuje aplikaci s ostatními službami Azure. Spravovaná identita je k dispozici pro aplikace nasazené do různých služeb.

    Další informace najdete v tématu [Přehled spravované identity](../../active-directory/managed-identities-azure-resources/overview.md). Podívejte se také na [služby Azure, které podporují spravovanou identitu](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), které odkazují na články, které popisují, jak povolit spravovanou identitu pro konkrétní služby (například App Service, Azure Functions, Virtual Machines atd.).

* Pokud nemůžete použít spravovanou identitu, můžete místo toho aplikaci **zaregistrovat** u svého TENANTA Azure AD, jak je popsáno v tématu [rychlý Start: registrace aplikace s platformou identity Azure](../../active-directory/develop/quickstart-register-app.md). Registrace vytvoří také druhý objekt aplikace, který identifikuje aplikaci napříč všemi klienty.

## <a name="authorize-a-security-principal-to-access-key-vault"></a>Autorizovat objekt zabezpečení pro přístup k Key Vault

Key Vault pracuje se dvěma různými úrovněmi autorizace:

- **Zásady přístupu** určují, jestli uživatel, skupina nebo instanční objekt mají autorizaci pro přístup k tajným klíčům, klíčům a certifikátům *v rámci* existujícího prostředku Key Vault (někdy se označuje jako "operace roviny dat"). Zásady přístupu jsou obvykle udělovány uživatelům, skupinám a aplikacím.

    Pokud chcete přiřadit zásady přístupu, přečtěte si následující články:

    - [Azure Portal](assign-access-policy-portal.md)
    - [Azure CLI](assign-access-policy-cli.md)
    - [Azure PowerShell](assign-access-policy-portal.md)

- **Oprávnění role** určují, jestli uživatel, skupina nebo instanční objekt mají autorizaci k vytváření, odstraňování a jiné správě Key Vault prostředku (někdy označovaného jako "rovina správy"). Tyto role jsou nejčastěji uděleny jenom správcům.
 
    Chcete-li přiřadit a spravovat role, přečtěte si následující články:

    - [Azure Portal](../../role-based-access-control/role-assignments-portal.md)
    - [Azure CLI](../../role-based-access-control/role-assignments-cli.md)
    - [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

    Key Vault aktuálně podporuje roli [Přispěvatel](../../role-based-access-control/built-in-roles.md#key-vault-contributor) , která umožňuje operace správy u prostředků Key Vault. Řada dalších rolí je aktuálně ve verzi Preview. Můžete také vytvořit vlastní role, jak je popsáno v tématu [vlastní role Azure](../../role-based-access-control/custom-roles.md).

    Obecné informace o rolích najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)?](../../role-based-access-control/overview.md).


> [!IMPORTANT]
> Pro nejvyšší zabezpečení vždy sledujte základní objekty s nejnižšími oprávněními a udělte pouze ty nejdůležitější zásady přístupu a role, které jsou nezbytné. 
    
## <a name="configure-the-key-vault-firewall"></a>Konfigurace brány Key Vault firewall

Ve výchozím nastavení Key Vault umožňuje přístup k prostředkům prostřednictvím veřejných IP adres. Pro zajištění vyššího zabezpečení můžete také omezit přístup ke konkrétním rozsahům IP adres, koncovým bodům služby, virtuálním sítím nebo soukromým koncovým bodům.

Další informace najdete v tématu [přístup Azure Key Vault za bránou firewall](./access-behind-firewall.md).


## <a name="the-key-vault-authentication-flow"></a>Key Vault tok ověřování

1. Instanční objekt požaduje ověření ve službě Azure AD, například:
    * Uživatel se do Azure Portal přihlásí pomocí uživatelského jména a hesla.
    * Aplikace vyvolá Azure REST API, který prezentuje ID klienta a tajný klíč nebo klientský certifikát.
    * Prostředek Azure, jako je třeba virtuální počítač se spravovanou identitou, kontaktuje koncový bod [azure instance metadata Service (IMDS)](../../virtual-machines/windows/instance-metadata-service.md) REST, který získá přístupový token.

1. Pokud je ověřování pomocí služby Azure AD úspěšné, instančnímu objektu se udělí token OAuth.

1. Instanční objekt provede volání Key Vault REST API prostřednictvím koncového bodu Key Vault (URI).

1. Brána firewall Key Vault kontroluje následující kritéria. Pokud je splněno jakékoli kritérium, volání je povoleno. V opačném případě se volání zablokuje a vrátí se zakázaná odpověď.

    * Brána firewall je zakázaná a veřejný koncový bod Key Vault dosažitelný z veřejného Internetu.
    * Volající je [Key Vault Důvěryhodná služba](./overview-vnet-service-endpoints.md#trusted-services), která umožňuje obejít bránu firewall.
    * Volající je uveden v bráně firewall podle IP adresy, virtuální sítě nebo koncového bodu služby.
    * Volající může kontaktovat Key Vault přes nakonfigurované připojení pomocí privátního propojení.    

1. Pokud brána firewall povoluje volání, Key Vault volá službu Azure AD za účelem ověření přístupového tokenu objektu služby.

1. Key Vault zkontroluje, jestli má instanční objekt pro požadovanou operaci zásadu přístupu. V takovém případě Key Vault vrátí zakázanou odpověď.

1. Key Vault provede požadovanou operaci a vrátí výsledek.

Následující obrázek znázorňuje proces pro aplikaci, která volá rozhraní API "Get tajného klíče" Key Vault:

![Azure Key Vault tok ověřování](../media/authentication/authentication-flow.png)

## <a name="code-examples"></a>Příklady kódu

Následující tabulka obsahuje odkazy na různé články, které ukazují, jak pracovat s Key Vault v kódu aplikace pomocí knihoven sady Azure SDK pro daný jazyk. Pro usnadnění jsou k dispozici další rozhraní, jako je Azure CLI a Azure Portal.

| Key Vault tajných klíčů | Key Vault klíče | Key Vault certifikátů |
|  --- | --- | --- |
| [Python](../secrets/quick-create-python.md) | [Python](../keys/quick-create-python.md) | [Python](../certificates/quick-create-python.md) | 
| [.NET (SDK v4)](../secrets/quick-create-net.md) | -- | -- |
| [.NET (sada SDK V3)](https://dotnet.microsoft.com/download/dotnet-core/3.0) | -- | -- |
| [Java](../secrets/quick-create-java.md) | -- | -- |
| [JavaScript](../secrets/quick-create-node.md) | -- | -- | 
| | | |
| [Azure Portal](../secrets/quick-create-portal.md) | [Azure Portal](../keys/quick-create-portal.md) | [Azure Portal](../certificates/quick-create-portal.md) |
| [Azure CLI](../secrets/quick-create-cli.md) | [Azure CLI](../keys/quick-create-cli.md) | [Azure CLI](../certificates/quick-create-cli.md) |
| [Azure PowerShell](../secrets/quick-create-powershell.md) | [Azure PowerShell](../keys/quick-create-powershell.md) | [Azure PowerShell](../certificates/quick-create-powershell.md) |
| [Šablona ARM](../secrets/quick-create-net.md) | -- | -- |

## <a name="next-steps"></a>Další kroky

- [Řešení potíží se zásadami přístupu Key Vault](troubleshooting-access-issues.md)
- [Kódy chyb Key Vault REST API](rest-error-codes.md)
- [Key Vault příručka pro vývojáře](developers-guide.md)
- [Co je řízení přístupu na základě role v Azure (Azure RBAC)?](../../role-based-access-control/overview.md)