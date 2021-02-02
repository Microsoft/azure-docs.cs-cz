---
title: Nejčastější dotazy – Import certifikátu Azure Key Vault
description: Získejte odpovědi na nejčastější dotazy týkající se importu Azure Key Vault certifikátů.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: e75786c42b54882c249b5ed7100ebd12f3f78e78
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254355"
---
# <a name="importing-azure-key-vault-certificates-faq"></a>Nejčastější dotazy k importu Azure Key Vault certifikátů

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se importu Azure Key Vault certifikátů.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Jak mohu importovat certifikát v Azure Key Vault?

V případě operace importu certifikátu Azure Key Vault akceptuje dva formáty souboru certifikátu: PEM a PFX. I když jsou PEM soubory s pouze veřejnou částí, Key Vault vyžaduje a přijímá pouze PEM nebo soubor PFX s privátním klíčem. Další informace najdete v tématu [Import certifikátu do Key Vault](./tutorial-import-certificate.md#import-a-certificate-to-key-vault).

### <a name="after-i-import-a-password-protected-certificate-to-key-vault-and-then-download-it-why-cant-i-see-the-password-thats-associated-with-it"></a>Když naimportujem certifikát chráněný heslem, aby se Key Vault a pak si ho stáhl, proč se mi nezobrazuje heslo, které je k němu přidružené?
    
Po importu a ochraně certifikátu v Key Vault se jeho přidružené heslo neuloží. Heslo je během operace importu vyžadováno pouze jednou. Jedná se o návrh, ale kdykoli můžete získat certifikát jako tajný kód a převést ho z formátu base64 na PFX přidáním hesla prostřednictvím [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

### <a name="how-can-i-resolve-a-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-to-key-vault"></a>Jak mohu vyřešit chybu "špatný parametr"? Jaké jsou podporované formáty certifikátů pro import do Key Vault?

Při importu certifikátu je nutné zajistit, aby byl klíč součástí souboru. Pokud máte privátní klíč uložený samostatně v jiném formátu, budete muset klíč kombinovat s certifikátem. Některé certifikační autority (CA) poskytují certifikáty v jiných formátech. Proto je před importem certifikátu nutné se ujistit, že je ve formátu souboru PEM nebo PFX a že klíč používá šifrování standardu Rivest – Shamir – Adleman (RSA) nebo rozhraní s rozhraním ECC (eliptický-Curve Cryptography). 

Další informace najdete v tématu [požadavky na certifikáty](./certificate-scenarios.md#formats-of-import-we-support) a [požadavky na klíč certifikátu](../keys/about-keys.md).

###  <a name="can-i-import-a-certificate-by-using-an-arm-template"></a>Můžu importovat certifikát pomocí šablony ARM?

Ne, nemůžete provádět operace s certifikáty pomocí šablony Azure Resource Manager (ARM). Doporučeným řešením je použít metody importu certifikátu v rozhraní API Azure, Azure CLI nebo PowerShellu. Pokud máte existující certifikát, můžete ho naimportovat jako tajný kód.

### <a name="when-i-import-a-certificate-via-the-azure-portal-i-get-a-something-went-wrong-error-how-can-i-investigate-further"></a>Když naimportujem certifikát přes Azure Portal, zobrazí se chyba "něco se pokazilo". Jak se dá prozkoumat?
    
Pokud chcete zobrazit výstižnější chybu, importujte soubor certifikátu pomocí [Azure CLI](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import) nebo [PowerShellu](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0).

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Jak můžu vyřešit typ chyby: přístup byl odepřen nebo uživatel nemá oprávnění k importu certifikátu?
    
Operace importu vyžaduje, abyste uživateli udělili oprávnění k importu certifikátu v rámci zásad přístupu. Provedete to tak, že přejdete do svého trezoru klíčů, vyberete **zásady přístupu**  >  **Přidat zásady přístupu**  >  **Vybrat**  >  **hlavní objekt** oprávnění pro certifikáty, vyhledáte uživatele a pak přidáte e-mailovou adresu uživatele. 

Další informace o zásadách přístupu souvisejících s certifikátem najdete v tématu [informace o Azure Key Vaultch certifikátech](./about-certificates.md#certificate-access-control).


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Jak můžu vyřešit typ chyby: konflikt při vytváření certifikátu?
    
Každý název certifikátu musí být jedinečný. Certifikát se stejným názvem může být ve stavu odstraněno. Také, v závislosti na [složení certifikátu](./about-certificates.md#composition-of-a-certificate)se při vytvoření nového certifikátu vytvoří adresovatelný tajný klíč se stejným názvem, takže pokud je v trezoru klíčů jiný klíč nebo tajný klíč se stejným názvem, jako je ten, který se pokoušíte zadat pro váš certifikát, vytvoření certifikátu se nezdaří a bude nutné odebrat tento klíč nebo tajný klíč nebo použít jiný název certifikátu. 

Další informace najdete v tématu [získání operace odstranění certifikátu](/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate).

### <a name="why-am-i-getting-error-type-char-length-is-too-long"></a>Proč se mi zobrazuje text "typ chyby: délka znaků je moc dlouhá?"
Tato chyba může být způsobena jednou ze dvou důvodů:    
* Název subjektu certifikátu je omezený na 200 znaků.
* Heslo certifikátu je omezené na 200 znaků.


### <a name="error-the-specified-pem-x509-certificate-content-is-in-an-unexpected-format-please-check-if-certificate-is-in-valid-pem-format"></a>Chyba: zadaný obsah certifikátu PEM X. 509 je v neočekávaném formátu. Zkontrolujte prosím, jestli je certifikát v platném formátu PEM. "
Ověřte prosím, že obsah v souboru PEM používá oddělovače řádků ve stylu systému UNIX. `(\n)`

### <a name="can-i-import-an-expired-certificate-to-azure-key-vault"></a>Můžu do Azure Key Vault importovat certifikát s vypršenou platností?
    
Ne, certifikáty PFX s vypršenou platností nelze importovat do Key Vault.

### <a name="how-can-i-convert-my-certificate-to-the-proper-format"></a>Jak můžu převést svůj certifikát do správného formátu?

Můžete požádat, aby certifikační autorita poskytovala certifikát v požadovaném formátu. K dispozici jsou také nástroje třetích stran, které vám pomohou převést certifikát do správného formátu.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>Můžu importovat certifikáty z nepartnerských certifikačních autorit?
Ano, můžete importovat certifikáty z libovolné certifikační autority, ale Trezor klíčů nebude možné obnovit automaticky. Můžete nastavit připomenutí, která budou upozorňovány na vypršení platnosti certifikátu.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-autorenewal-feature-still-work"></a>Pokud naimportujem certifikát z partnerské CA, bude funkce autorenew pořád fungovat?
Ano. Po nahrání certifikátu Nezapomeňte zadat automatické otočení v zásadách vystavování certifikátu. Vaše nastavení zůstane v platnosti, dokud nebude uvolněn další cyklus nebo verze certifikátu.

### <a name="why-cant-i-see-the-app-service-certificate-that-i-imported-to-key-vault"></a>Proč se mi nezobrazuje App Service certifikát, který jste importovali do Key Vault? 
Pokud jste certifikát importovali úspěšně, měli byste ho potvrdit tak, že v podokně **tajných klíčů** kliknete na.


## <a name="next-steps"></a>Další kroky

- [Azure Key Vault certifikátů](./about-certificates.md)
