---
title: Nejčastější dotazy – Import certifikátu Azure Key Vault
description: Nejčastější dotazy – Import certifikátu Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 493c77a8f875018627bfe3167e66addeaf65d089
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2020
ms.locfileid: "87445788"
---
# <a name="frequently-asked-questions---azure-key-vault-certificate-import"></a>Nejčastější dotazy – Import certifikátu Azure Key Vault

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Jak mohu importovat certifikát v Azure Key Vault?

Import certifikátu – Azure Key Recovery přijímá dva formáty certifikátů PEM a PFX, a to za operaci importu. I když jsou PEM soubory s pouze veřejnou částí, vyžaduje Trezor klíčů Azure a přijímá pouze PEM nebo PFX ve složce souborů a s privátním klíčem. Podle [kurzu importujte certifikát](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-import-certificate#import-a-certificate-to-key-vault) .

### <a name="after-importing-password-protected-certificate-into-the-key-vault-and-then-downloading-it-i-am-not-able-to-see-the-password-associated-with-the-certificate"></a>Po importu certifikátu chráněného heslem do trezoru klíčů si nemůžete zobrazit heslo přidružené k certifikátu?
    
Nahraný chráněný certifikát po uložení do trezoru klíčů neuloží heslo přidružené k tomuto úložišti. Tato hesla se během operace importu vyžadují pouze jednou. I když se jedná o koncept s návrhem, můžete certifikát vždycky načíst jako tajný kód a převést ho z Base64 na PFX přidáním předchozího hesla pomocí [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

### <a name="how-can-i-resolve-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-in-key-vault"></a>Jak můžu vyřešit chybnou hodnotu parametru? Jaké jsou podporované formáty certifikátů pro import ve službě Key Vault?

Při importu certifikátu je nutné zajistit, aby byl klíč zahrnut do samotného souboru. Pokud máte privátní klíč samostatně v jiném formátu, budete muset klíč kombinovat s certifikátem. Některé certifikační autority poskytují certifikáty v různých formátech, takže před importem certifikátu se ujistěte, že jsou ve formátu. pem nebo. pfx a že použitý klíč je buď RSA, nebo ECC. Podívejte se na tyto informace a zkontrolujte [požadavky na certifikát](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#formats-of-import-we-support) a [požadavky na klíč certifikátu](https://docs.microsoft.com/azure/key-vault/keys/about-keys#cryptographic-protection).

### <a name="error-when-importing-certificate-via-portal-something-went-wrong-how-can-i-investigate-further"></a>Při importu certifikátu přes portál dojde k chybě Něco se nepovedlo. Jak se dá prozkoumat?
    
Pokud chcete zobrazit výstižnější chybu, importujte soubor certifikátu přes [Azure CLI](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import) nebo [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0).

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Jak můžu vyřešit typ chyby: přístup byl odepřen nebo uživatel nemá oprávnění k importu certifikátu?
    
Tato operace vyžaduje oprávnění k importu certifikátů. Přejděte do umístění služby Key Vault a v části Zásady přístupu musíte uživateli udělit odpovídající oprávnění. Přejděte do> zásady přístupu Key Vault > přidat zásady přístupu > vyberte oprávnění certifikátu (nebo podle potřeby oprávnění) > hlavní > vyhledejte a pak přidejte e-mail uživatele. [Další informace o zásadách přístupu souvisejících s certifikáty](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#certificate-access-control)


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Jak můžu vyřešit typ chyby: konflikt při vytváření certifikátu?
    
Název certifikátu by měl být jedinečný. Certifikát se stejným názvem může být ve stavu tichého odstranění, a to i v případě, že je na základě [složení certifikátu](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate) v trezoru klíčů Azure jiný klíč nebo tajný klíč v Key Vault se stejným názvem, který se pokoušíte zadat pro váš certifikát, selže a bude nutné buď tento klíč nebo tajný klíč odebrat, nebo použít jiný název certifikátu. [Zobrazit odstraněný certifikát](https://docs.microsoft.com/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate)

### <a name="why-am-i-getting-the-error-type-char-length-is-too-long"></a>Proč se mi zobrazuje typ chyby: délka znaků je moc dlouhá?
    
* Délka názvu subjektu certifikátu má omezení znaků 200 znaků.
* Délka hesla certifikátu má omezení znaků 200 znaků.

### <a name="can-i-import-an-expired-certificate-in-azure-key-vault"></a>Můžu importovat certifikát s vypršenou platností v trezoru klíčů Azure?
    
Ne, certifikáty PFX s vypršenou platností se neimportují do Azure Key Vault.

### <a name="how-can-i-convert-my-certificate-to-proper-format"></a>Jak můžu převést svůj certifikát do správného formátu?

Můžete požádat o certifikační autoritu, aby poskytla certifikát v požadovaném formátu. k dispozici jsou také nástroje třetích stran, které vám pomohou převést do správného formátu, ale společnost Microsoft nebude moci poradit s tím, jak získat certifikát v požadovaném formátu.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>Můžu importovat certifikáty z nepartnerských certifikačních autorit?
Ano, můžete importovat certifikáty z libovolné certifikační autority, ale Trezor klíčů nebude moct tyto certifikáty automaticky obnovit. Mohli byste nastavit e-mailová oznámení, abyste získali oznámení o vypršení platnosti certifikátu.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-auto-renew-feature-still-work"></a>Pokud naimportujem certifikát z partnerské CA, bude funkce automatického obnovení pořád fungovat?
Ano, musíte se ujistit, že po nahrání určíte automatické otočení v zásadách vystavování certifikátu. Změny se projeví také až do příštího cyklu nebo verze certifikátu.

### <a name="unable-to-see-the-app-service-certificate-imported-to-key-vault"></a>Nemůžete zobrazit App Service Certificate naimportovaná do Key Vault? 
Pokud se certifikát úspěšně naimportoval, přečtěte si ho v části tajné klíče.


## <a name="next-steps"></a>Další kroky

- [Azure Key Vault certifikátů](/azure/key-vault/certificates/about-certificates)
