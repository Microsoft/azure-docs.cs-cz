---
title: Informace o Azure Key Vault obnovení certifikátu
description: Informace o Azure Key Vault obnovení certifikátu
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 41085ee629189c32c1bc7196f23805c9c48d154a
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056274"
---
# <a name="about-azure-key-vault-certificate-renewal"></a>Informace o Azure Key Vault obnovení certifikátu

Azure Key Vault umožňuje snadno zřídit, spravovat a nasazovat digitální certifikáty pro vaši síť a povolit zabezpečenou komunikaci pro aplikace. Obecnější informace o certifikátech najdete v tématu [Azure Key Vault certifikátů](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates) .

U krátkodobého nepracovního certifikátu nebo zvýšení frekvence rotace certifikátů omezuje nežádoucí osoba rozsah škod.

## <a name="certificate-expiration-notifications"></a>Oznámení o vypršení platnosti certifikátu
Nejdřív nezapomeňte přidat do svého Key Vault kontakt s certifikátem, abyste mohli dostávat oznámení, když brzy vyprší platnost certifikátů. (např. pomocí rutiny prostředí PowerShell [Add-AzureKeyVaultCertificateContact](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact?view=azurermps-6.13.0)) nakonfigurujete, kdy se má zobrazit oznámení o vypršení platnosti certifikátu. Pokud chcete nakonfigurovat akci životního cyklu, postupujte podle [zde](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate) .

V trezoru klíčů existují tři kategorie vytvoření certifikátu. Tato příručka vám pomůže pochopit, jak lze dosáhnout obnovení certifikátů.
-   Certifikáty vytvořené s integrovanou certifikační autoritou (DigiCert nebo GlobalSign)
-   Certifikáty vytvořené bez integrované certifikační autority
-   Certifikáty podepsané svým držitelem

## <a name="renewal-of-integrated-ca-certificate"></a>Obnovení integrovaného certifikátu certifikační autority 
Máme pro vás dobré zprávy! Trezory klíčů Azure se postará o komplexní údržbu certifikátů vydaných důvěryhodnými certifikačními autoritami Microsoftu, tj. DigiCert a GlobalSign. Naučte se [integrovat důvěryhodnou certifikační autoritu s trezorem klíčů](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority).

## <a name="renewal-of-non-integrated-ca-certificate"></a>Obnovování neintegrovaného certifikátu certifikační autority 
Azure Key trezor poskytuje svým uživatelům výhody importu certifikátů z libovolné certifikační autority, aby se mohli jejich uživatelé integrovat s několika prostředky Azure a usnadnit nasazení. Pokud jste se seznámili se ztrátou vyprší platnost vašeho certifikátu nebo jste zjistili, že platnost certifikátu již vypršela, potom vám Key Vault může pokaždé v aktuálním stavu. V případě neintegrovaného certifikátu certifikační autority umožňuje Trezor klíčů uživateli nastavení e-mailových oznámení téměř po vypršení platnosti. Tato oznámení lze nastavit také pro více uživatelů.

Teď pro obnovení certifikátu je důležité pochopit, že Azure Key Vault certifikát je objekt s verzí. Pokud aktuální verze vyprší, budete muset vytvořit novou verzi. V koncepčních případech by každá nová verze měla zcela nový certifikát, který se skládá z klíče a objektu blob, který tento klíč přiřazuje identitě. Když použijete nepartnerský certifikační autoritu, Trezor klíčů vygeneruje pár klíč-hodnota a vrátí CSR.

**Postup, jak postupovat v Azure Portal: –**
1.  Otevřete certifikát, který chcete obnovit.
2.  Na obrazovce certifikátu klikněte na tlačítko **+ Nová verze** .
3.  Vybrat **operaci certifikátu**
4.  Vyberte **Stáhnout CSR**. Tím se stáhne soubor. CSR na místním disku.
5.  Přinese CSR na váš výběr certifikační autority pro podepsání žádosti.
6.  Vraťte podepsaný požadavek a vyberte **Sloučit CSR** na stejné obrazovce operace certifikátu.

> [!NOTE]
> Je důležité sloučit podepsaného CSR se stejným vytvořeným požadavkem CSR, jinak se klíč neshoduje.

Postup je podobný jako při vytváření nového certifikátu a je [tady]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal)popsaný podrobněji.

## <a name="renewal-of-self-signed-certificate"></a>Obnovení certifikátu podepsaného svým držitelem

Dobrá zpráva. Trezory klíčů Azure se také postará o automatické obnovení certifikátů podepsaných svým držitelem pro své uživatele. Další informace o tom, jak změnit zásady vystavování a aktualizovat atributy akce životního cyklu certifikátu, si můžete přečíst [tady](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate).

### <a name="troubleshoot"></a>Řešení potíží
Pokud je certifikát vystavený ve Azure Portal stav zakázáno, přejděte k části zobrazení certifikátu a zobrazte chybovou zprávu pro tento certifikát.

### <a name="frequently-asked-questions"></a>Nejčastější dotazy
Budou značky replikovány po automatickém obnovení certifikátu?
Ne, značky nebudou replikovány, pokud uživatel ručně nezkopíruje samotné značky.

### <a name="see-also"></a>Viz také
*   [Integrace služby Key Vault s certifikační autoritou DigiCert](how-to-integrate-certificate-authority.md)
*   [Kurz: Konfigurace automatického rotace certifikátů v Key Vault](tutorial-rotate-certificates.md)
