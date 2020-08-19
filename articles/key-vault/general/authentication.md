---
title: Ověřování pro Azure Key Vault
description: Zjistěte, jak ověřit a Azure Key Vault
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 06/08/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 7645600a476a1c2294ddd4a24fe01e2ffe51d5ac
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589978"
---
# <a name="authenticate-to-azure-key-vault"></a>Ověřování pro Azure Key Vault

## <a name="overview"></a>Přehled

Azure Key Vault je řešení pro správu tajných kódů, které umožňuje centralizovat úložiště tajných klíčů aplikací a řídit jejich distribuci. Azure Key Vault eliminuje nutnost ukládat přihlašovací údaje v aplikacích. Vaše aplikace se může k trezoru klíčů ověřit, aby se načetly požadované přihlašovací údaje. Tento dokument popisuje základy ověřování do trezoru klíčů.

Tento dokument vám pomůže pochopit, jak funguje ověřování trezoru klíčů. Tento dokument vysvětluje tok ověřování, ukáže vám, jak udělit přístup k trezoru klíčů a obsahuje kurz pro načtení uloženého tajného klíče v trezoru klíčů z ukázkové aplikace v Pythonu.

Tento dokument bude pokrývat:

* Klíčové koncepty
* Registrace objektu zabezpečení
* Principy Key Vaultho toku ověřování
* Udělení přístupového objektu služby Key Vault
* Kurz (Python)

## <a name="key-concepts"></a>Klíčové koncepty

### <a name="azure-active-directory-concepts"></a>Azure Active Directory koncepty

* Azure Active Directory (AAD) – Azure Active Directory (Azure AD) je cloudová služba pro správu identit a přístupu od Microsoftu, která pomáhá vašim zaměstnancům přihlašovat se a přistupovat k prostředkům.

* Definice role – definice role je kolekcí oprávnění.  AAD má standardní role (vlastníka, přispěvatele nebo čtenář), které obsahují úrovně oprávnění k provádění operací, jako jsou čtení, zápis a odstranění prostředku Azure. Role můžou být také vlastními definicemi vytvořenými uživateli s konkrétními podrobnými oprávněními.

* Registrace aplikací – když zaregistrujete aplikaci Azure AD, vytvoří se ve vašem tenantovi Azure AD, objektu aplikace a instančního objektu služby dva objekty. Zvažte použití objektu aplikace jako globální reprezentace aplikace pro použití ve všech klientech a instančního objektu jako místní reprezentace pro použití v konkrétním tenantovi.

### <a name="security-principal-concepts"></a>Základní koncepty zabezpečení

* Objekt zabezpečení – objekt zabezpečení je objekt, který představuje uživatele, skupinu, instanční objekt nebo spravovanou identitu, která žádá o přístup k prostředkům Azure.

* Uživatel – jednotlivec, který má profil ve službě Azure Active Directory.

* Skupina – skupina uživatelů vytvořená ve službě Azure Active Directory. Když přiřadíte roli skupině, budou mít danou roli všichni její uživatelé.

* Instanční objekt – identita zabezpečení, kterou používají aplikace nebo služby pro přístup ke konkrétním prostředkům Azure. Můžete si ji představit jako identitu uživatele (uživatelské jméno a heslo nebo certifikát) pro aplikaci.

* Spravovaná identita – identita v Azure Active Directory, kterou automaticky spravuje Azure.

* ID objektu (ID klienta) – jedinečný identifikátor, který vygenerovala služba Azure AD, která je vázaná na instanční objekt během počátečního zřizování.

## <a name="security-principal-registration"></a>Registrace objektu zabezpečení

1. Správce zaregistruje uživatele nebo aplikaci (instanční objekt) v Azure Active Directory.

2. Správce vytvoří Azure Key Vault a nakonfiguruje zásady přístupu (ACL).

3. Volitelné Správce nakonfiguruje bránu Azure Key Vault firewall.

![OBRÁZEK](../media/authentication-1.png)

## <a name="understand-the-key-vault-authentication-flow"></a>Princip Key Vaultho toku ověřování

1. Instanční objekt provede volání ověřování do AAD, ke kterému může dojít několika způsoby:
    * Uživatel se může přihlásit k Azure Portal pomocí uživatelského jména a hesla.
    * Aplikace používá ID klienta a prezentuje tajný klíč klienta nebo klientský certifikát pro AAD.
    * Prostředek Azure, jako je třeba virtuální počítač, má přiřazenou službu MSI a kontaktuje koncový bod IMDS REST k získání přístupového tokenu.

2. Pokud je ověření pro AAD úspěšné, instančnímu objektu se udělí token OAuth.
3. Instanční objekt provede volání Key Vault.
4. Azure Key Vault firewall určuje, zda má být volání povoleno.
    * Scénář 1: Key Vault firewall je zakázaný, veřejný koncový bod (URI) trezoru klíčů je dosažitelný z veřejného Internetu. Volání je povoleno.
    * Scénář 2: volající je Azure Key Vault Důvěryhodná služba. Pokud je vybraná možnost, můžou některé služby Azure obejít bránu firewall trezoru klíčů. [Seznam Key Vault důvěryhodných služeb](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)
    * Scénář 3: volající je uvedený v bráně Azure Key Vault firewall podle IP adresy, virtuální sítě nebo koncového bodu služby.
    * Scénář 4: volající může kontaktovat Azure Key Vault prostřednictvím nakonfigurovaného připojení s privátním propojením.
    * Scénář 5: volající není autorizovaný a vrátí se zakázaná odpověď.
5. Key Vault provede volání AAD k ověření přístupového tokenu objektu služby.
6. Key Vault kontroluje, jestli má instanční objekt dostatečná oprávnění zásad přístupu k provedení požadované operace, v tomto příkladu je operace získat tajný klíč.
7. Key Vault poskytuje tajný klíč k instančnímu objektu.

![OBRÁZEK](../media/authentication-2.png)

## <a name="grant-a-service-principal-access-to-key-vault"></a>Udělení přístupového objektu služby Key Vault

1. Pokud ještě nemáte objekt služby, vytvořte ho. [Vytvoření instančního objektu](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)
2. Přidejte přiřazení role k instančnímu objektu v nastavení Azure Key Vault IAM. Můžete přidat předem přiřazené role vlastníka, přispěvatele nebo čtenáře. Pro objekt služby můžete také vytvořit vlastní role. Měli byste dodržovat zabezpečení s nejnižšími oprávněními a poskytnout jenom minimální přístup nezbytný pro váš instanční objekt. 
3.  Nakonfigurujte bránu firewall trezoru klíčů. Bránu firewall trezoru klíčů můžete ponechat zakázanou a povolíte přístup z veřejného Internetu (méně bezpečné, jednodušší konfigurace). Můžete taky omezit přístup ke konkrétním rozsahům IP adres, koncovým bodům služby, virtuálním sítím nebo soukromým koncovým bodům (bezpečnějším).
4.  Přidejte zásady přístupu pro váš instanční objekt. Jedná se o seznam operací, které může instanční objekt provádět v trezoru klíčů. Měli byste použít objekt zabezpečení s minimálními oprávněními a omezit operace, které může instanční objekt provádět. Pokud však neposkytnete dostatečná oprávnění, instančnímu objektu bude odepřen přístup.

## <a name="tutorial"></a>Kurz

V tomto kurzu se naučíte nastavit instanční objekt pro ověření v trezoru klíčů a načíst tajný klíč. 

### <a name="part-1--create-a-service-principal-in-the-azure-portal"></a>Část 1: Vytvoření instančního objektu v Azure Portal

1. Přihlášení k webu Azure Portal
1. Hledat Azure Active Directory
1. Klikněte na kartu registrace aplikací.
1. Klikněte na + nová registrace.
1. Vytvoření názvu instančního objektu
1. Vybrat registraci

V tomto okamžiku máte registrovaný instanční objekt. Můžete ji zobrazit výběrem možnosti registrace aplikací. Vašemu instančnímu objektu se teď přiřadí identifikátor GUID ID klienta, takže si ho můžete představit jako "uživatelské jméno" pro instanční objekt. Nyní musíme vytvořit "heslo" pro instanční objekt, můžete použít tajný klíč klienta nebo klientský certifikát. Všimněte si, že použití tajného klíče klienta pro ověřování není zabezpečené a mělo by se používat pouze pro účely testování. Tento kurz vám ukáže, jak používat klientský certifikát.

### <a name="part-2-create-a-client-certificate-for-your-service-principal"></a>Část 2: Vytvoření klientského certifikátu pro objekt služby

1. Vytvoření certifikátu

    * Možnost 1: vytvoření certifikátu pomocí [OpenSSL](https://www.openssl.org/) (pouze pro účely testování nepoužívejte certifikáty podepsané svým držitelem v produkčním prostředí)

    ```console
    openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365
    ```

    * Možnost 2: vytvoření certifikátu pomocí trezoru klíčů. [Vytvoření certifikátu v Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#creating-your-first-key-vault-certificate)

1. Stažení certifikátu ve formátu PEM
1. Přihlaste se k Azure Portal a přejděte do Azure Active Directory
1. Klikněte na registrace aplikací.
1. Vyberte objekt služby, který jste vytvořili v části 1.
1. Klikněte na kartu certifikáty a tajné objekty služby.
1. Nahrajte certifikát pomocí tlačítka pro odeslání certifikátu.

### <a name="part-3-configure-an-azure-key-vault"></a>Část 3: konfigurace Azure Key Vault

1. Vytvoří [odkaz](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault) Azure Key Vault.

2. Konfigurace Key Vault oprávnění IAM
    1. Přejděte do trezoru klíčů.
    1. Vyberte kartu Access Control (IAM).
    1. Klikněte na přidat přiřazení role.
    1. Vybrat roli Přispěvatel z rozevíracího seznamu
    1. Zadejte název nebo ID klienta instančního objektu, který jste vytvořili.
    1. Kliknutím na Zobrazit přiřazení rolí potvrďte, že je váš instanční objekt uvedený.

3. Konfigurace oprávnění zásad přístupu Key Vault
    1. Přejděte do trezoru klíčů.
    1. V části nastavení vyberte kartu zásady přístupu.
    1. Vyberte odkaz + Přidat zásadu přístupu.
    1. V rozevíracím seznamu oprávnění skrytá zaškrtněte oprávnění získat a seznam.
    1. Vyberte instanční objekt podle názvu nebo ID klienta.
    1. Vyberte Přidat.
    1. Vyberte Save (Uložit).

4. V trezoru klíčů vytvořte tajný klíč.
    1. Přejděte do trezoru klíčů.
    1. V části Nastavení klikněte na kartu tajné klíče.
    1. Klikněte na + generovat/importovat.
    1. Vytvořte název tajného klíče. v tomto příkladu pojmenuje tajný klíč "test".
    1. Vytvořte hodnotu pro tajný klíč. v tomto příkladu nastavím hodnotu "password123".

Když teď spouštíte kód z místního počítače, můžete se k trezoru klíčů ověřit získáním přístupového tokenu tak, že zobrazíte ID klienta a cestu k certifikátu.

### <a name="part-4-retrieve-the-secret-from-your-azure-key-vault-in-an-application-python"></a>4. část: načtení tajného kódu z Azure Key Vault v aplikaci (Python)

Použijte následující ukázku kódu k otestování, jestli vaše aplikace může z vašeho trezoru klíčů získat tajný klíč pomocí instančního objektu, který jste nakonfigurovali. 

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import CertificateCredential


tenant_id = ""                                             ##ENTER AZURE TENANT ID
vault_url = "https://{VAULT NAME}.vault.azure.net/"        ##ENTER THE URL OF YOUR KEY VAULT
client_id = ""                                             ##ENTER CLIENT ID OF SERVICE PRINCIPAL
cert_path = r"C:\Users\{USERNAME}\{PATH}\{CERT_NAME}.pem"  ##ENTER PATH TO CERTIFICATE

def main():

    #AUTHENTICATION TO AAD USING CLIENT ID AND CLIENT CERTIFICATE
    token = CertificateCredential(tenant_id= tenant_id, client_id=client_id, certificate_path=cert_path)

    #AUTHENTICATION TO KEY VAULT PRESENTING AAD TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    secret = client.get_secret('{SECRET_NAME}')            ##ENTER NAME OF SECRET IN KEY VAULT

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

![OBRÁZEK](../media/authentication-3.png)


## <a name="next-steps"></a>Další kroky

1. Naučte se řešit chyby ověřování trezoru klíčů. [Průvodce odstraňováním potíží s Key Vault](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
