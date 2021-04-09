---
title: Informace o Azure Key Vault obnovení certifikátu
description: Tento článek popisuje, jak obnovit certifikáty Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: ffa130c0598d2405469d272a3ac6852f281ed965
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726358"
---
# <a name="renew-your-azure-key-vault-certificates"></a>Obnovení certifikátů Azure Key Vault

Pomocí Azure Key Vault můžete snadno zřídit, spravovat a nasazovat digitální certifikáty pro vaši síť a povolit zabezpečenou komunikaci pro vaše aplikace. Další informace o certifikátech najdete v tématu [informace o Azure Key Vaultch certifikátech](./about-certificates.md).

Pomocí krátkodobých certifikátů nebo zvýšením frekvence rotace certifikátů můžete pomoci zabránit přístupu k aplikacím neautorizovaným uživatelům.

Tento článek popisuje, jak obnovit certifikáty Azure Key Vault.

## <a name="get-notified-about-certificate-expiration"></a>Dostávat oznámení o vypršení platnosti certifikátu
Pokud chcete dostávat oznámení o událostech životního cyklu certifikátů, musíte přidat kontakt na certifikát. Kontakty certifikátu obsahují kontaktní informace pro odesílání oznámení aktivovaných událostmi životnosti certifikátu. Informace o kontaktech jsou sdíleny pomocí všech certifikátů v trezoru klíčů. Oznámení se pošle všem zadaným kontaktům pro událost pro libovolný certifikát v trezoru klíčů.

### <a name="steps-to-set-certificate-notifications"></a>Postup nastavení oznámení o certifikátu:
Nejdřív přidejte do svého trezoru klíčů kontakt s certifikátem. Můžete přidat pomocí rutiny Azure Portal nebo PowerShellu [`Add-AzureKeyVaultCertificateContact`](/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact) .

Potom nakonfigurujte, kdy chcete být upozorněni na vypršení platnosti certifikátu. Informace o konfiguraci atributů životního cyklu certifikátu najdete v tématu [Konfigurace automatické rotace certifikátů v Key Vault](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate).

Pokud je zásada certifikátu nastavená na automatické obnovení, pošle se na následující události oznámení.

- Před obnovením certifikátu
- Po obnovení certifikátu uveďte, jestli se certifikát úspěšně obnovil, nebo jestli došlo k chybě, která vyžaduje ruční obnovení certifikátu.  

  Když se zásada certifikátu, která je nastavená na ruční obnovení (jenom e-mailem), pošle, pošle se oznámení, když se certifikát obnoví.  

V Key Vault existují tři kategorie certifikátů:
-    Certifikáty, které jsou vytvořené pomocí integrované certifikační autority (CA), jako je DigiCert nebo GlobalSign
-    Certifikáty vytvořené s neintegrovanou certifikační autoritou
-    Certifikáty podepsané svým držitelem

## <a name="renew-an-integrated-ca-certificate"></a>Obnovení integrovaného certifikátu certifikační autority 
Azure Key Vault zpracovává ucelenou údržbu certifikátů vydaných důvěryhodnými certifikačními autoritami Microsoftu DigiCert a GlobalSign. Naučte se [integrovat důvěryhodnou certifikační autoritu pomocí Key Vault](./how-to-integrate-certificate-authority.md).

## <a name="renew-a-nonintegrated-ca-certificate"></a>Obnovit neintegrovanou certifikační AUTORITu 
Pomocí Azure Key Vault můžete importovat certifikáty z libovolné certifikační autority, což vám umožní integrovat s několika prostředky Azure a usnadnit nasazení. Pokud máte obavy o ztrátu sledování dat vypršení platnosti certifikátu nebo, horší, zjistili jste, že platnost certifikátu již vypršela, váš Trezor klíčů vám může pomáhat v aktuálním stavu. V případě neintegrovaných certifikátů certifikační autority vám Trezor klíčů umožňuje nastavit e-mailová oznámení s blížícím se koncem platnosti. Taková oznámení je možné nastavit i pro více uživatelů.

> [!IMPORTANT]
> Certifikát je objekt se správou verzí. Pokud aktuální verze vyprší, je nutné vytvořit novou verzi. V koncepčních případech každá nová verze je nový certifikát, který se skládá z klíče a objektu blob, který tento klíč přiřazuje k identitě. Když použijete nepartnerský certifikační autoritu, Trezor klíčů vygeneruje dvojici klíč/hodnota a vrátí žádost o podepsání certifikátu (CSR).

Pokud chcete obnovit neintegrovanou certifikační AUTORITu, udělejte toto:

1. Přihlaste se k Azure Portal a pak otevřete certifikát, který chcete obnovit.
1. V podokně certifikát vyberte **Nová verze**.
1. Vyberte **operaci certifikátu**.
1. Vyberte **Stáhnout CSR** a Stáhněte si soubor CSR na místní disk.
1. Odešlete CSR podle vašeho výběru certifikační autority pro podepsání žádosti.
1. Vraťte podepsaný požadavek a vyberte **Sloučit CSR** do stejného podokna operace certifikátu.

> [!NOTE]
> Je důležité sloučit podepsaného CSR se stejným požadavkem CSR, který jste vytvořili. V opačném případě se klíč neshoduje.

Další informace o tom, jak vytvořit nového CSR, najdete [v tématu Vytvoření a sloučení CSR v Key Vault]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal).

## <a name="renew-a-self-signed-certificate"></a>Obnovení certifikátu podepsaného svým držitelem

Azure Key Vault také zpracovává automatické obnovení certifikátů podepsaných svým držitelem. Další informace o změně zásad vystavování a aktualizaci atributů životního cyklu certifikátu najdete v tématu [Konfigurace automatické rotace certifikátů v Key Vault](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate).

## <a name="troubleshoot"></a>Řešení potíží
* Pokud je vystavený certifikát v *neaktivním* stavu na Azure Portal, podívejte se na **operaci certifikátu** a zobrazí se chybová zpráva certifikátu.
* Typ chyby: CSR použitý k získání certifikátu již byl použit. Zkuste prosím vygenerovat nový certifikát s novým CSR. "
  V části certifikát klikněte na rozšířená zásada a ověřte, jestli je vypnutá možnost **znovu použít klíč při obnovení** .


## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Jak můžu otestovat funkci autorotace certifikátu?**

Vytvořte certifikát s platností **1 měsíc** a pak nastavte akci životnosti pro otočení o **1%**. Toto nastavení otočí certifikát každých 7,2 hodin.
  
**Budou tyto značky replikovány po automatickém obnovení certifikátu?**

Ano, značky se replikují po automatickém obnovení.

## <a name="next-steps"></a>Další kroky
*    [Integrace Key Vault s certifikační autoritou DigiCert](how-to-integrate-certificate-authority.md)
*    [Kurz: konfigurace automatické rotace certifikátů v Key Vault](tutorial-rotate-certificates.md)
