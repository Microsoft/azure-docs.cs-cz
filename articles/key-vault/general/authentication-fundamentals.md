---
title: Azure Key Vault základy ověřování
description: Přečtěte si, jak funguje model ověřování trezoru klíčů.
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 09/25/2020
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: f7f9acd18da57bd83e688249600b8468cc4ebbe5
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445553"
---
# <a name="key-vault-authentication-fundamentals"></a>Základy ověřování služby Key Vault

Azure Key Vault umožňuje bezpečně ukládat a spravovat přihlašovací údaje aplikací, jako jsou tajné klíče, klíče a certifikáty, v centrálním a zabezpečeném cloudovém úložišti. Key Vault eliminují nutnost ukládat přihlašovací údaje v aplikacích. Vaše aplikace se můžou ověřit, aby se v době spuštění načetly přihlašovací údaje, aby se Key Vault.

Jako správce můžete přesně určit, kteří uživatelé a aplikace budou mít přístup k trezoru klíčů, a můžete omezit a provést audit operací, které provádějí. Tento dokument vysvětluje základní koncepty přístupového modelu trezoru klíčů. Poskytne vám tak úvodní úroveň znalostí a ukáže vám, jak můžete ověřit uživatele nebo aplikaci pro Trezor klíčů od začátku do konce.

## <a name="required-knowledge"></a>Požadované znalosti

V tomto dokumentu se předpokládá, že máte zkušenosti s následujícími koncepty. Pokud nejste obeznámeni s žádnou z těchto konceptů, před pokračováním použijte odkazy na tyto informace.

* [Odkaz](../../active-directory/fundamentals/active-directory-whatis.md) na Azure Active Directory
* [Propojení](./authentication.md#app-identity-and-security-principals) objektů zabezpečení

## <a name="key-vault-configuration-steps-summary"></a>Souhrn kroků konfigurace Key Vault

1. Zaregistrujte uživatele nebo aplikaci v Azure Active Directory jako objekt zabezpečení.
1. Nakonfigurujte přiřazení role pro objekt zabezpečení v Azure Active Directory.
1. Nakonfigurujte zásady přístupu trezoru klíčů pro objekt zabezpečení.
1. Nakonfigurujte přístup Key Vault brány firewall k trezoru klíčů (volitelné).
1. Otestujte schopnost objektu zabezpečení získat přístup k trezoru klíčů.

## <a name="register-a-user-or-application-in-azure-active-directory-as-a-security-principal"></a>Registrace uživatele nebo aplikace v Azure Active Directory jako objekt zabezpečení

Když uživatel nebo aplikace odešle požadavek do trezoru klíčů, musí být požadavek nejdřív ověřený Azure Active Directory. Aby to fungovalo, musí být uživatel nebo aplikace zaregistrované v Azure Active Directory jako objekt zabezpečení.

Postup při registraci uživatele nebo aplikace v Azure Active Directory najdete v níže uvedených odkazech k dokumentaci.
**Nezapomeňte vytvořit heslo pro registraci uživatele a tajný klíč klienta nebo přihlašovací údaje klientského certifikátu pro aplikace.**

* Registrace uživatele v Azure Active Directory [odkazu](../../active-directory/fundamentals/add-users-azure-active-directory.md)
* Registrace aplikace v Azure Active Directory [odkazu](../../active-directory/develop/quickstart-register-app.md)

## <a name="assign-your-security-principal-a-role"></a>Přiřazení objektu zabezpečení k roli

K přiřazení oprávnění k objektům zabezpečení můžete použít řízení přístupu na základě role Azure (Azure RBAC). Tato oprávnění se nazývají přiřazení rolí.

V kontextu trezoru klíčů určují Tato přiřazení rolí úroveň přístupu objektu zabezpečení k rovině správy (označované také jako rovina ovládacího prvku) trezoru klíčů. Tato přiřazení rolí neposkytují přímý přístup k tajným datům roviny dat, ale poskytují přístup ke správě vlastností trezoru klíčů. Například uživatel nebo aplikace přiřazená k **roli Čtenář** nebude moci provádět změny nastavení brány firewall trezoru klíčů, zatímco uživatel nebo aplikace, které mají přiřazenou **roli Přispěvatel** , mohou provádět změny. Žádná role nebude mít přímý přístup k provádění operací na tajných klíčích, klíčích a certifikátech, jako je vytvoření nebo načtení jejich hodnoty, dokud jim nebude přiřazen přístup k rovině dat trezoru klíčů. Tento postup je popsaný v dalším kroku.

>[!IMPORTANT]
> I když uživatelé s rolí přispěvatel nebo vlastník nemají přístup k provádění operací s tajnými kódy uloženými v trezoru klíčů ve výchozím nastavení role přispěvatel a vlastník, poskytují oprávnění k přidávání nebo odebírání zásad přístupu tajných kódů uložených v trezoru klíčů. Proto může uživatel s těmito přiřazeními rolí udělit přístup k tajným klíčům v trezoru klíčů. Z tohoto důvodu doporučujeme, aby k rolím přispěvatele nebo vlastníka měli přístup jenom správci. Pro uživatele a aplikace, které potřebují jenom načíst tajné kódy z trezoru klíčů, by měla být udělená role čtenář. **Další podrobnosti najdete v další části.**

>[!NOTE]
> Když přiřadíte přiřazení role k uživateli na úrovni Azure Active Directory tenanta, tato sada oprávnění se trickle dolů ke všem předplatným, skupinám prostředků a prostředkům v rámci rozsahu přiřazení. Chcete-li se řídit objekty s nejmenším oprávněním, můžete toto přiřazení role udělat v podrobnějším oboru. Můžete například přiřadit uživateli roli čtenáře na úrovni předplatného a roli vlastníka pro jeden Trezor klíčů. Přejděte do nastavení IAM (identity Access Management) předplatného, skupiny prostředků nebo trezoru klíčů, aby bylo přiřazení role v podrobnějším rozsahu.

* Další informace o [propojení](../../role-based-access-control/built-in-roles.md) rolí Azure
* Další informace o přiřazení nebo odebrání [odkazu](../../role-based-access-control/role-assignments-portal.md) na přiřazení rolí

## <a name="configure-key-vault-access-policies-for-your-security-principal"></a>Konfigurace zásad přístupu trezoru klíčů pro objekt zabezpečení

Předtím, než udělíte přístup pro uživatele a aplikace pro přístup k trezoru klíčů, je důležité pochopit různé typy operací, které se dají provádět na trezoru klíčů. Existují dva hlavní typy operací trezoru klíčů, rovina správy (označované také jako rovina ovládacího prvku) a operace roviny dat.

Tato tabulka obsahuje několik příkladů různých operací, které jsou ovládány rovinou správy a rovinou dat. Operace, které mění vlastnosti trezoru klíčů, jsou operace roviny správy. Operace, které mění nebo načítají hodnotu tajných kódů uložených v trezoru klíčů, jsou operace roviny dat.

|Operace roviny správy (příklady)|Operace roviny dat (příklady)|
| --- | --- |
| Vytvořit Key Vault | Vytvořit klíč, tajný klíč, certifikát
| Odstranit Key Vault | Odstranit klíč, tajný klíč, certifikát
| Přidat nebo odebrat přiřazení rolí Key Vault | Vypíše a získá hodnoty klíčů, tajných kódů a certifikátů.
| Přidat nebo odebrat zásady přístupu Key Vault | Zálohování a obnovení klíčů, tajných kódů a certifikátů
| Upravit nastavení brány Key Vault firewall | Obnovení klíčů, tajných kódů a certifikátů
| Změnit nastavení obnovení Key Vault | Vyprázdnit nebo obnovit obnovitelné odstraněné klíče, tajné klíče, certifikáty
| Upravit nastavení Key Vault diagnostické protokoly

### <a name="management-plane-access--azure-active-directory-role-assignments"></a>Přístup k rovině správy & přiřazení rolí Azure Active Directory

Přiřazení role Azure Active Directory udělují přístup k operacím roviny správy v trezoru klíčů. Tento přístup je obvykle udělen uživatelům, nikoli aplikacím. Změnou přiřazení role uživatele můžete omezit, které operace roviny správy může uživatel provádět.

Například přiřazení uživatele k roli Čtenář Key Vault uživateli umožní zobrazit vlastnosti vašeho trezoru klíčů, jako jsou zásady přístupu, ale neumožní provádět žádné změny. Když se uživatel přiřadí, role vlastníka jim umožní úplný přístup ke změně nastavení roviny správy trezoru klíčů.

Přiřazení rolí se řídí v okně trezoru klíčů Access Control (IAM). Pokud chcete, aby měl určitý uživatel přístup ke čtenáři nebo aby byl správce více prostředků trezoru klíčů, můžete vytvořit přiřazení role na úrovni trezoru, skupiny prostředků nebo předplatného a přiřazení role se přidá do všech prostředků v rámci daného přiřazení.

Přístup k rovině dat nebo přístup k provádění operací na klíčích, tajných klíčích a certifikátech uložených v trezoru klíčů je možné přidat jedním ze dvou způsobů.

### <a name="data-plane-access-option-1-classic-key-vault-access-policies"></a>Možnost přístupu k rovině dat 1: zásady přístupu pro klasický Key Vault

Zásady přístupu trezoru klíčů udělují uživatelům a aplikacím přístup k provádění operací roviny dat v trezoru klíčů.

> [!NOTE]
> Tento model přístupu není kompatibilní s Azure RBAC pro Trezor klíčů (možnost 2) popsaný níže. Musíte zvolit jednu z nich. Pokud kliknete na kartu zásady přístupu vašeho trezoru klíčů, budete mít možnost Tento výběr provést.

Klasické zásady přístupu jsou podrobné, což znamená, že můžete povolit nebo zakázat schopnost jednotlivých uživatelů nebo aplikací provádět jednotlivé operace v rámci trezoru klíčů. Tady je pár příkladů:

* Objekt zabezpečení 1 může provádět jakoukoli klíčovou operaci, ale není povoleno provádět žádné tajné operace ani operace certifikátu.
* Zabezpečení zabezpečení 2 může vypisovat a číst všechny klíče, tajné klíče a certifikáty, ale nemůže provádět operace vytvoření, odstranění nebo obnovení.
* Objekt zabezpečení 3 může zálohovat a obnovovat všechna tajná klíčová, ale nemůže číst hodnotu tajných klíčů.

Zásady klasického přístupu ale neumožňují oprávnění na úrovni jednotlivých objektů a přiřazená oprávnění se aplikují na rozsah jednotlivých trezorů klíčů. Pokud například udělíte zásadě přístupu "tajné získání" k objektu zabezpečení v konkrétním trezoru klíčů, bude mít objekt zabezpečení možnost získat všechny tajné kódy v rámci daného trezoru klíčů. Toto oprávnění "získat tajný klíč" ale nebude automaticky rozšířeno na jiné trezory klíčů a musí být přiřazeno explicitně.

> [!IMPORTANT]
> Zásady přístupu k trezoru klíčů v klasických klíčích a Azure Active Directory přiřazení rolí jsou vzájemně nezávislá. Přiřazení objektu zabezpečení role přispěvatel na úrovni předplatného neumožní objektu zabezpečení automaticky provádět operace s datovou rovinou u každého trezoru klíčů v rámci rozsahu předplatného. Aby bylo možné provádět operace roviny dat, musí být zaregistrovaný objekt zabezpečení i nadále udělen nebo udělena oprávnění zásad přístupu.

### <a name="data-plane-access-option-2--azure-rbac-for-key-vault-preview"></a>Možnost přístupu k rovině dat 2: Azure RBAC pro Key Vault (Preview)

Nový způsob, jak udělit přístup k rovině dat trezoru klíčů, je prostřednictvím řízení přístupu na základě role Azure (Azure RBAC) pro Trezor klíčů.

> [!NOTE]
> Tento model přístupu není kompatibilní se zásadami klasického přístupu trezoru klíčů, které jsou uvedené výše. Musíte zvolit jednu z nich. Pokud kliknete na kartu zásady přístupu vašeho trezoru klíčů, budete mít možnost Tento výběr provést.

Přiřazení role Key Vault jsou sada předdefinovaných přiřazení rolí Azure, která zahrnují společné sady oprávnění používaných pro přístup k klíčům, tajným klíčům a certifikátům. Tento model oprávnění také umožňuje další možnosti, které nejsou k dispozici v modelu klasických zásad přístupu trezoru klíčů.

* Oprávnění Azure RBAC je možné spravovat ve škálování tím, že uživatelům umožníte přiřadit tyto role na předplatné, skupinu prostředků nebo jednotlivé úrovně trezoru klíčů. Uživatel bude mít oprávnění roviny dat pro všechny trezory klíčů v rámci rozsahu přiřazení Azure RBAC. Tím se eliminuje nutnost přiřazení individuálních oprávnění zásad přístupu na uživatele a aplikace na Trezor klíčů.

* Oprávnění Azure RBAC jsou kompatibilní s Privileged Identity Management nebo PIM. Díky tomu můžete nakonfigurovat řízení přístupu za běhu pro privilegované role, jako je Key Vault správce. Jedná se o osvědčené postupy zabezpečení a následuje zabezpečení s nejnižšími oprávněními eliminující stálý přístup k vašim trezorům klíčů.

* Oprávnění Azure RBAC jsou kompatibilní s podrobnými oprávněními pro jednotlivé objekty, takže můžete uživatele omezit jenom na provádění operací na některých objektech trezoru klíčů. To umožňuje více aplikacím sdílet jeden Trezor klíčů a zároveň stále izolovat přístup mezi aplikacemi.

Další informace o službě Azure RBAC pro Key Vault najdete v následujících dokumentech:

* [Odkaz](./secure-your-key-vault.md#management-plane-and-azure-rbac) na službu Azure RBAC pro Key Vault
* [Odkaz](../../role-based-access-control/built-in-roles.md#key-vault-administrator-preview) Azure RBAC pro role Key Vault (Preview)

## <a name="configure-key-vault-firewall"></a>Konfigurace brány Key Vault firewall

Ve výchozím nastavení Trezor klíčů umožňuje provozu z veřejného Internetu, aby odesílal přístup k vašemu trezoru klíčů prostřednictvím veřejného koncového bodu. Pro další vrstvu zabezpečení můžete nakonfigurovat bránu Azure Key Vault firewall, aby se omezil přístup k veřejnému koncovému bodu trezoru klíčů.

Pokud chcete povolit bránu firewall trezoru klíčů, klikněte na kartu síť na portálu trezoru klíčů a vyberte přepínač, který povolí přístup z: "privátní koncový bod a vybrané sítě". Pokud se rozhodnete povolit bránu firewall trezoru klíčů, jedná se o způsoby, kterými můžete povolit provoz přes bránu firewall trezoru klíčů.

* Přidejte adresy IPv4 do seznamu povolených bran firewall trezoru klíčů. Tato možnost funguje nejlépe pro aplikace, které mají statické IP adresy.

* Přidejte virtuální síť do brány firewall trezoru klíčů. Tato možnost funguje nejlépe pro prostředky Azure s dynamickými IP adresami, jako je například Virtual Machines. Můžete přidat prostředky Azure do virtuální sítě a přidat virtuální síť do seznamu povolených bran firewall trezoru klíčů. Tato možnost používá koncový bod služby, což je privátní IP adresa v rámci virtuální sítě. To poskytuje další vrstvu ochrany, takže žádný přenos mezi trezorem klíčů a vaší virtuální sítí není směrován přes veřejný Internet. Další informace o koncovém bodu služby najdete v následující dokumentaci. [propojit](./network-security.md)

* Přidejte připojení k trezoru klíčů pomocí privátního propojení. Tato možnost připojí vaši virtuální síť přímo k určité instanci trezoru klíčů a efektivně tak zaznamená Trezor klíčů do vaší virtuální sítě. Další informace o konfiguraci připojení privátního koncového bodu k trezoru klíčů najdete na následujícím [odkazu](./private-link-service.md) .

## <a name="test-your-service-principals-ability-to-access-key-vault"></a>Otestujte schopnost objektu služby získat přístup k trezoru klíčů.

Jakmile budete dodržovat všechny výše uvedené kroky, budete moct nastavit a načíst tajné kódy z trezoru klíčů.

### <a name="authentication-process-for-users-examples"></a>Proces ověřování pro uživatele (příklady)

* Uživatelé se můžou k Azure Portal přihlásit, aby používali Trezor klíčů. [Rychlý Start portálu Key Vault](./quick-create-portal.md)

* Uživatel může použít Azure CLI k použití trezoru klíčů. [Rychlý Start k rozhraní příkazového řádku Azure Key Vault](./quick-create-cli.md)

* Uživatel může použít Azure PowerShell k použití trezoru klíčů. [Rychlý Start Key Vault Azure PowerShell](./quick-create-powershell.md)

### <a name="azure-active-directory-authentication-process-for-applications-or-services-examples"></a>Azure Active Directory procesu ověřování pro aplikace nebo služby (příklady)

* Aplikace poskytuje tajný klíč klienta a ID klienta ve funkci pro získání tokenu Azure Active Directory. 

* Aplikace poskytuje certifikát pro získání tokenu Azure Active Directory. 

* Prostředek Azure používá k získání tokenu Azure Active Directory ověřování pomocí MSI. 

* Další informace o [připojení pro](../../active-directory/managed-identities-azure-resources/overview.md) ověřování MSI

### <a name="authentication-process-for-application-python-example"></a>Proces ověřování pro aplikaci (příklad Pythonu)

Použijte následující ukázku kódu k otestování, jestli vaše aplikace může z vašeho trezoru klíčů získat tajný klíč pomocí instančního objektu, který jste nakonfigurovali.

>[!NOTE]
>Tato ukázka je určena pouze pro demonstrační a testovací účely. **Nepoužívat ověřování tajného klíče klienta v produkčním** prostředí Nejedná se o zabezpečený postup při návrhu. Jako osvědčený postup byste měli použít certifikát klienta nebo ověřování MSI.

```python
from azure.identity import ClientSecretCredential
from azure.keyvault.secrets import SecretClient

tenant_id = "{ENTER YOUR TENANT ID HERE}"                          ##ENTER AZURE TENANT ID
vault_url = "https://{ENTER YOUR VAULT NAME}.vault.azure.net/"     ##ENTER THE URL OF YOUR KEY VAULT
client_id = "{ENTER YOUR CLIENT ID HERE}"                          ##ENTER THE CLIENT ID OF YOUR SERVICE PRINCIPAL
cert_path = "{ENTER YOUR CLIEND SECRET HERE}"                      ##ENTER THE CLIENT SECRET OF YOUR SERVICE PRINCIPAL

def main():

    #AUTHENTICATION TO Azure Active Directory USING CLIENT ID AND CLIENT CERTIFICATE (GET Azure Active Directory TOKEN)
    token = ClientSecretCredential(tenant_id=tenant_id, client_id=client_id, client_secret=client_secret)

    #AUTHENTICATION TO KEY VAULT PRESENTING Azure Active Directory TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    #ENTER NAME OF A SECRET STORED IN KEY VAULT
    secret = client.get_secret('{SECRET_NAME}')

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

## <a name="next-steps"></a>Další kroky

Další informace o ověřování trezoru klíčů najdete podrobněji v následujícím dokumentu. [Ověřování ve službě Key Vault](./authentication.md)