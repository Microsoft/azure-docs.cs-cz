---
title: Vytvoření a sloučení CSR v Azure Key Vault
description: Naučte se, jak vytvořit a sloučit CSR v Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: a4d079855e5aa05adb84b62d686d9f386608f7bb
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752133"
---
# <a name="create-and-merge-a-csr-in-key-vault"></a>Vytvoření a sloučení CSR v Key Vault

Azure Key Vault podporuje ukládání digitálních certifikátů vydaných kteroukoli certifikační autoritou (CA). Podporuje vytvoření žádosti o podepsání certifikátu (CSR) s dvojicí privátního a veřejného klíče. CSR může podepsat kterákoli z certifikačních autorit (interní podniková CA nebo externí Veřejná certifikační autorita). CSR je zpráva, kterou odešlete certifikační autoritě, aby si vyžádala digitální certifikát.

Obecnější informace o certifikátech najdete v tématu [Azure Key Vault certifikátů](./about-certificates.md).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="add-certificates-in-key-vault-issued-by-partnered-cas"></a>Přidání certifikátů v Key Vault vydaných partnerskými certifikačními autoritami

Key Vault partneři s následujícími certifikačními autoritami pro zjednodušení vytváření certifikátů.

|Poskytovatel|Typ certifikátu|Nastavení konfigurace  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault nabízí certifikáty SSL OV nebo EV s DigiCert.| [Průvodce integrací](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault nabízí certifikáty SSL OV nebo EV s GlobalSign.| [Průvodce integrací](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="add-certificates-in-key-vault-issued-by-non-partnered-cas"></a>Přidání certifikátů v Key Vault vydaných nepartnerskými certifikačními autoritami

Pomocí těchto kroků přidejte certifikát od certifikačních autorit, které nejsou partneři Key Vault. (Například GoDaddy není důvěryhodná certifikační autorita Key Vault.)

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Přejít do trezoru klíčů, do kterého chcete certifikát přidat.
1. Na stránce Vlastnosti vyberte možnost **certifikáty**.
1. Vyberte kartu **Generovat/importovat** .
1. Na obrazovce **vytvořit certifikát** vyberte následující hodnoty:
    - **Metoda vytvoření certifikátu**: generovat.
    - **Název certifikátu**: ContosoManualCSRCertificate.
    - **Typ certifikační autority (CA)**: certifikát vystavila jiná než integrovaná certifikační autorita.
    - **Předmět**: `"CN=www.contosoHRApp.com"` .
     > [!NOTE]
     > Pokud používáte relativní rozlišující název (RDN), který má v hodnotě čárku (,), zabalte hodnotu, která obsahuje speciální znak, do dvojitých uvozovek. 
     >
     > Příklad položky pro **Předmět**: `DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com`
     >
     > V tomto příkladu `OU` obsahuje RDN hodnotu s čárkou v názvu. Výsledný výstup pro `OU` aplikace je **Docs, contoso**.
1. Vyberte další hodnoty podle potřeby a pak výběrem **vytvořit** přidejte certifikát do seznamu **certifikáty** .

    ![Snímek obrazovky s vlastnostmi certifikátu](../media/certificates/create-csr-merge-csr/create-certificate.png)  

1. V seznamu **certifikáty** vyberte nový certifikát. Aktuální stav certifikátu je **zakázaný** , protože ho zatím nevydala certifikační autorita.
1. Na kartě **operace certifikátu** vyberte **Stáhnout CSR**.

   ![Snímek obrazovky, který zvýrazní tlačítko Stáhnout CSR.](../media/certificates/create-csr-merge-csr/download-csr.png)

1. Podepisujte certifikační autoritu (CSR).
1. Po podepsání žádosti vyberte možnost **Sloučit podepsaný požadavek** na kartě **operace certifikátu** a přidejte podepsaný certifikát do Key Vault.

Žádost o certifikát byla nyní úspěšně sloučena.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Vytvořte zásady certifikátu. Vzhledem k tomu, že certifikační autorita, která je zvolená v tomto scénáři, není partnerovaná, je **název Issuer** nastavený na **Neznámý** a Key Vault neregistruje nebo neobnoví certifikát

   ```azure-powershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
     > [!NOTE]
     > Pokud používáte relativní rozlišující název (RDN), který má v hodnotě čárku (,), použijte jednoduché uvozovky pro celou hodnotu nebo sadu hodnot a zabalte hodnotu, která obsahuje speciální znak, do dvojitých uvozovek. 
     >
     >Příklad záznamu na **Subject**: `$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown` . V tomto příkladu je `OU` hodnota čtená jako **Docs, contoso**. Tento formát funguje pro všechny hodnoty, které obsahují čárku.
     > 
     > V tomto příkladu `OU` obsahuje RDN hodnotu s čárkou v názvu. Výsledný výstup pro `OU` aplikace je **Docs, contoso**.

1. Vytvořte CSR.

   ```azure-powershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

1. Podepisujte certifikační autoritu jako zástupce. `$csr.CertificateSigningRequest`Je základní KÓDOVANÝ CSR pro certifikát. Tento objekt blob můžete vypsat na webu žádosti o certifikát vystavitele. Tento krok se liší od certifikační autority až po certifikační autoritu. Projděte si pokyny pro certifikační autoritu, jak tento krok provést. Pomocí nástrojů, jako je například certreq nebo OpenSSL, můžete také získat zástupce CSR a dokončit proces generování certifikátu.

1. Sloučí podepsaný požadavek v Key Vault. Po podepsání žádosti o certifikát ji můžete sloučit s počátečním párem soukromého/veřejného klíče vytvořeným v Azure Key Vault.

    ```azure-powershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

Žádost o certifikát byla nyní úspěšně sloučena.

---

## <a name="add-more-information-to-the-csr"></a>Přidat další informace zástupci oddělení IT

Pokud chcete přidat další informace při vytváření CSR, definujte je v části **Subject**. Můžete chtít přidat informace, jako například:
- Země
- Město
- Kraj
- Organizace
- Organizační jednotka

Příklad

   ```azure-powershell
   SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
   ```

> [!NOTE]
> Pokud požadujete certifikát pro ověření domény (DV) s dalšími informacemi, certifikační autorita může žádost odmítnout, pokud nedokáže ověřit všechny informace v žádosti. Další informace můžou být vhodnější, pokud požadujete certifikát pro ověření organizace (OV).

## <a name="faqs"></a>Nejčastější dotazy

- Návody monitorování nebo Správa svého CSR?

     Viz téma [monitorování a Správa vytváření certifikátů](./create-certificate-scenarios.md).

- Co když se zobrazuje **typ chyby, veřejný klíč certifikátu koncové entity v zadaném obsahu certifikátu X. 509 se neshoduje s veřejnou součástí zadaného privátního klíče. Zkontrolujte prosím, jestli je certifikát platný**?

     K této chybě dochází, pokud neslučujete podepsaného zástupce CSR se stejným požadavkem CSR, který jste zahájili. Každý nový zástupce, kterého vytvoříte, má privátní klíč, který se musí shodovat při sloučení podepsané žádosti.

- Při sloučení CSR se celý řetězec sloučí?

     Ano, provede sloučení celého řetězce, za předpokladu, že uživatel vrátil soubor. P7B ke sloučení.

- Co když je vydaný certifikát v Azure Portal zakázaný stav?

     Zobrazte kartu **operace certifikátu** a zkontrolujte chybovou zprávu pro daný certifikát.

- Co když se zobrazuje **typ chyby, zadaný název subjektu není platným názvem X500**?

     K této chybě může dojít, pokud **Subject** obsahuje jakékoli speciální znaky. Viz poznámky v pokynech pro Azure Portal a PowerShell.

---

## <a name="next-steps"></a>Další kroky

- [Ověřování, žádosti a odpovědi](../general/authentication-requests-and-responses.md)
- [Průvodce vývojáře pro službu Key Vault](../general/developers-guide.md)
- [Odkaz na Azure Key Vault REST API](/rest/api/keyvault)
- [Trezory – vytvořit nebo aktualizovat](/rest/api/keyvault/vaults/createorupdate)
- [Trezory – aktualizace zásad přístupu](/rest/api/keyvault/vaults/updateaccesspolicy)