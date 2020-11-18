---
title: Vytvoření a sloučení CSR v Azure Key Vault
description: Vytvoření a sloučení CSR v Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: c8f11f17c9e110509dcbcda291194f9b8d928c50
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658957"
---
# <a name="creating-and-merging-csr-in-key-vault"></a>Vytvoření a sloučení CSR v Key Vault

Azure Key Vault podporuje ukládání digitálního certifikátu vydaného jakoukoli certifikační autoritou dle vašeho výběru v trezoru klíčů. Podporuje vytvoření žádosti o podepsání certifikátu s dvojicí klíčů privátního veřejného klíče, kterou může podepsat kterákoli vybraná certifikační autorita. Může to být interní podniková CA nebo externí Veřejná certifikační autorita. Žádost o podepsání certifikátu (také zástupce oddělení IT nebo certifikace) je zpráva odeslaná uživatelem do certifikační autority (CA), aby mohla požádat o vystavení digitálního certifikátu.

Obecnější informace o certifikátech najdete v tématu [Azure Key Vault certifikátů](./about-certificates.md).

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

## <a name="adding-certificate-in-key-vault-issued-by-partnered-ca"></a>Přidání certifikátu v Key Vault vydaném prostřednictvím partnerské certifikační autority
Key Vault partneři s následujícími dvěma certifikačními autoritami pro zjednodušení vytváření certifikátů. 

|Poskytovatel|Typ certifikátu|Nastavení konfigurace  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault nabízí certifikáty SSL OV nebo EV s DigiCert.| [Průvodce integrací](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault nabízí certifikáty SSL OV nebo EV s GlobalSign.| [Průvodce integrací](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="adding-certificate-in-key-vault-issued-by-non-partnered-ca"></a>Přidání certifikátu v Key Vault vydaném nepartnerskou certifikační autoritou

Následující kroky vám pomůžou vytvořit certifikát od certifikačních autorit, které nejsou partnerské Key Vault (například GoDaddy není certifikační autoritou důvěryhodného trezoru klíčů). 


### <a name="azure-powershell"></a>Azure PowerShell



1.  Nejdřív **Vytvořte zásady certifikátu**. Key Vault nebude registrovat ani obnovovat certifikát od vystavitele jménem uživatele, protože certifikační autorita zvolená v tomto scénáři není podporovaná, a proto je název vystavitele nastavený na neznámý.

    ```azurepowershell
    $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
    ```


2. Vytvoření **žádosti o podepsání certifikátu**

   ```azurepowershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

3. Získání žádosti CSR **podepsané certifikační autoritou** `$certificateOperation.CertificateSigningRequest` je žádost o podepsání certifikátu Base4 kódovaného certifikátu. Tento objekt blob můžete převzít a vypsat na webu žádosti o certifikát vystavitele. Tento krok se liší od certifikační autority od certifikační AUTORITy, nejlepším způsobem je vyhledat pokyny certifikační autority, jak tento krok provést. Pomocí nástrojů, jako je například certreq nebo OpenSSL, můžete také získat žádost o certifikát podepsaný a dokončit proces generování certifikátu.


4. Po podepsání **podepsané žádosti** v Key Vault po podpisu žádosti o certifikát vystavitele můžete přenést podepsaný certifikát a sloučit ho s počátečním párem privátního veřejného klíče vytvořeným v Azure Key Vault

    ```azurepowershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

    Žádost o certifikát se teď úspěšně sloučila.

### <a name="azure-portal"></a>portál Azure

1.  Pokud chcete vygenerovat CSR pro certifikační autoritu dle vašeho výběru, přejděte do trezoru klíčů, do kterého chcete certifikát přidat.
2.  Na stránkách Key Vault vlastnosti vyberte **certifikáty**.
3.  Vyberte kartu **Generovat/importovat** .
4.  Na obrazovce **vytvořit certifikát** vyberte následující hodnoty:
    - **Metoda vytvoření certifikátu:** Vytváří.
    - **Název certifikátu:** ContosoManualCSRCertificate.
    - **Typ certifikační autority (CA):** Certifikát vydaný neintegrovanou certifikační autoritou
    - **Předmět:**`"CN=www.contosoHRApp.com"`
    - Vyberte další hodnoty podle potřeby. Klikněte na **Vytvořit**.

    ![Vlastnosti certifikátu](../media/certificates/create-csr-merge-csr/create-certificate.png)
6.  Uvidíte, že certifikát se teď přidal do seznamu certifikáty. Vyberte tento nový certifikát, který jste právě vytvořili. Aktuální stav certifikátu by byl zakázán, protože ještě nebyl vydán certifikační autoritou.
7. Klikněte na kartu **operace certifikátu** a vyberte **Stáhnout CSR**.
 ![Snímek obrazovky, který zvýrazní tlačítko Stáhnout CSR.](../media/certificates/create-csr-merge-csr/download-csr.png)

8.  Pořiďte si soubor. CSR pro certifikační autoritu, aby se žádost mohla podepsat.
9.  Jakmile je žádost podepsána certifikační autoritou, vraťte soubor certifikátu pro **sloučení podepsané žádosti** na obrazovku stejné operace certifikátu.

Žádost o certifikát se teď úspěšně sloučila.

## <a name="adding-more-information-to-csr"></a>Přidání dalších informací CSR

Pokud chcete přidat další informace při vytváření CSR, např. 
    - Země:
    - Město/místní:
    - Stát/provincie:
    - Dotčen
    - Organizační jednotka: všechny tyto informace můžete přidat při vytváření CSR tím, že je definujete v tématu Subject.

Příklad
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```

>[!Note]
>Pokud požadujete certifikát DV se všemi těmito podrobnostmi v oddělení IT, certifikační autorita může žádost odmítnout, protože certifikační autorita nemusí být schopna ověřit všechny informace v žádosti. Pokud požadujete certifikát OV, je vhodnější přidat všechny tyto informace v oddělení IT.


## <a name="troubleshoot"></a>Řešení potíží

- **Typ chyby: veřejný klíč certifikátu koncové entity v zadaném obsahu certifikátu X. 509 se neshoduje s veřejnou součástí zadaného privátního klíče. Zkontrolujte prosím, jestli je certifikát platný** . k této chybě může dojít, pokud neslučujete CSR se stejným zahájeným požadavkem CSR. Při každém vytvoření CSR se vytvoří privátní klíč, který se při slučování podepsané žádosti musí shodovat.
    
- Při sloučení CSR se celý řetězec sloučí?
    Ano, sloučí celý řetěz a za předpokladu, že uživatel vrátil soubor P7B ke sloučení, se vrátí.

- Pokud je certifikát vystavený ve Azure Portal stav zakázáno, přejděte k části zobrazení **certifikátu** a zkontrolujte chybovou zprávu pro daný certifikát.

Další informace najdete v referenčních informacích o [operacích certifikátu v REST API Key Vault](/rest/api/keyvault). Informace o tom, jak vytvářet oprávnění, najdete v tématu [trezory – vytvoření nebo aktualizace](/rest/api/keyvault/vaults/createorupdate) a [trezory – zásady přístupu pro aktualizaci](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Další kroky

- [Ověřování, žádosti a odpovědi](../general/authentication-requests-and-responses.md)
- [Průvodce vývojáře pro službu Key Vault](../general/developers-guide.md)
