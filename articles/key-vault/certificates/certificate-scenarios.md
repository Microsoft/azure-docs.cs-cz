---
title: Začínáme s certifikáty služby Key Vault
description: Následující scénáře popisují několik primárních použití služby správy certifikátů Key Vault, včetně dalších kroků potřebných k vytvoření prvního certifikátu v trezoru klíčů.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 06/13/2020
ms.author: mbaldwin
ms.openlocfilehash: d99d211ec48a507b205c4cef21618054c11aec9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86224855"
---
# <a name="get-started-with-key-vault-certificates"></a>Začínáme s certifikáty služby Key Vault
Následující scénáře popisují několik primárních použití služby správy certifikátů Key Vault, včetně dalších kroků potřebných k vytvoření prvního certifikátu v trezoru klíčů.

Jsou popsaný následující:
- Vytváří se první Key Vault certifikát.
- Vytvoření certifikátu s certifikační autoritou, která je partnerkou Key Vault
- Vytvoření certifikátu s certifikační autoritou, která není partnerkou Key Vault
- Import certifikátu

## <a name="certificates-are-complex-objects"></a>Certifikáty jsou komplexní objekty.
Certifikáty se skládají ze tří vzájemně propojených prostředků společně s certifikátem Key Vault. metadata certifikátu, klíč a tajný klíč.


![Certifikáty jsou komplexní.](../media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Vytváří se první Key Vault certifikát.  
 Předtím, než je možné vytvořit certifikát v Key Vault (KV), je nutné provést kroky 1 a 2 splnění požadavků a pro tohoto uživatele nebo organizaci musí existovat Trezor klíčů.  

**Krok 1** – poskytovatelé certifikační autority (CA)  
-   Připojování jako správce IT, správce PKI nebo kdokoli, kdo spravuje účty s CAs pro danou společnost (např. Contoso) je předpokladem pro použití Key Vaultch certifikátů.  
    Následující CA jsou aktuální partneři partnerských služeb s Key Vault:  
    -   DigiCert-Key Vault nabízí OV certifikáty TLS/SSL s DigiCert.  
    -   GlobalSign-Key Vault nabízí OV certifikáty TLS/SSL s GlobalSign.  

**Krok 2** – správce účtu pro poskytovatele certifikační autority vytvoří přihlašovací údaje, které Key Vault použít k registraci, obnovení a používání certifikátů TLS/SSL prostřednictvím Key Vault.

**Krok 3** – správce společnosti Contoso, společně se zaměstnancem společnosti Contoso (Key Vault uživatel), který vlastní certifikáty v závislosti na certifikační autoritě, může získat certifikát od správce nebo přímo od účtu s certifikační autoritou.  

- Zahajte operaci přidání přihlašovacích údajů do trezoru klíčů [nastavením prostředku vystavitele certifikátu](/rest/api/keyvault/setcertificateissuer/setcertificateissuer) . Vystavitel certifikátu je entita reprezentovaná v Azure Key Vault (KV) jako prostředek CertificateIssuer. Slouží k poskytnutí informací o zdroji certifikátu KV; název vystavitele, poskytovatel, přihlašovací údaje a další podrobnosti o správě.
  - Například MyDigiCertIssuer  
    -   Poskytovatel  
    -   Přihlašovací údaje – přihlašovací údaje účtu certifikační autority Každá certifikační autorita má vlastní konkrétní data.  

    Další informace o vytváření účtů s poskytovateli certifikačních autorit najdete v souvisejícím příspěvku na [blogu Key Vault](https://aka.ms/kvcertsblog).  

**Krok 3,1** – nastavení [kontaktů certifikátů](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) pro oznámení Toto je kontakt pro uživatele Key Vault. Key Vault nevynutila tento krok.  

Poznámka: Tento proces, prostřednictvím kroku 3,1, je operace jednorázová.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Vytvoření certifikátu s certifikační autoritou, která je partnerkou Key Vault

![Vytvoření certifikátu s Key Vault Partnerská certifikační autorita](../media/certificate-authority-2.png)

**Krok 4** : následující popisy odpovídají zeleným očíslovaným krokům v předchozím diagramu.  
  (1) – v diagramu výše vaše aplikace vytváří certifikát, který interně začíná vytvořením klíče v trezoru klíčů.  
  (2) – Key Vault odešle certifikační autoritě žádost o certifikát TLS/SSL.  
  (3) – vaše aplikace se dotazuje v rámci smyčky a procesu čekání pro Key Vault pro dokončení certifikátu. Vytvoření certifikátu je dokončeno, když Key Vault obdrží odpověď certifikační autority s certifikátem X.509.  
  (4) – certifikační autorita reaguje na žádosti o certifikát TLS/SSL v Key Vault s certifikátem x509 TLS/SSL.  
  (5) – vytvoření nového certifikátu se dokončí s fúzí certifikátu x509 pro certifikační autoritu.  

  Key Vault uživatel – vytvoří certifikát zadáním zásady.

  -   Opakovat podle potřeby  
  -   Omezení zásad  
      -   Vlastnosti x509  
      -   Základní vlastnosti  
      -   Reference k poskytovateli – > ex. MyDigiCertIssure  
      -   Informace o obnovení – > ex 90 dní před vypršením platnosti  

  - Proces vytvoření certifikátu je obvykle asynchronní proces a zahrnuje cyklické dotazování vašeho trezoru klíčů pro stav operace vytvoření certifikátu.  
[Získat operaci certifikátu](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   Stav: dokončeno, selhalo s informacemi o chybě nebo bylo zrušeno.  
      -   Kvůli prodlevě, která se má vytvořit, se dá iniciovat operace zrušení. Zrušení může nebo nemusí být účinné.  

### <a name="network-security-and-access-policies-associated-with-integrated-ca"></a>Zásady zabezpečení a přístupu k síti spojené s integrovanou certifikační autoritou
Služba Key Vault odesílá požadavky do certifikační autority (odchozí provoz). Proto je plně kompatibilní s povolenými trezory klíčů brány firewall. Key Vault nesdílí zásady přístupu k certifikační autoritě. CA musí být nakonfigurovaná tak, aby přijímala požadavky na podpis nezávisle. [Průvodce integrací důvěryhodné certifikační autority](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority)

## <a name="import-a-certificate"></a>Import certifikátu  
 Alternativně – certifikát lze importovat do Key Vault – PFX nebo PEM.  

 Import certifikátu – vyžaduje, aby byl PEM nebo PFX na disku a měl privátní klíč. 
-   Je nutné zadat název trezoru a název certifikátu (zásada je volitelná).

-   Soubory PEM/PFX obsahují atributy, které může KV analyzovat a použít k naplnění zásad certifikátu. Pokud je již zásada certifikátu zadána, KV se pokusí porovnat data ze souboru PFX/PEM.  

-   Po dokončení importu budou následné operace používat nové zásady (nové verze).  

-   Pokud neexistují žádné další operace, první věc, na kterou Key Vault, pošle oznámení o vypršení platnosti. 

-   Uživatel může také upravit zásadu, která je funkční v době importu, ale obsahuje výchozí hodnoty, při kterých nebyly při importu zadány žádné informace. Například žádné informace o vydavateli  

### <a name="formats-of-import-we-support"></a>Formáty importu, které podporujeme
Azure Key Vault podporuje soubory certifikátů. pem a. pfx pro import certifikátů do trezoru klíčů.
Podporujeme následující typ importu pro formát souboru PEM. Jeden PEM kódovaný certifikát spolu s nezašifrovaným klíčem PKCS # 8, který má následující

-----ZAČÍT CERTIFIKÁT UKONČIT---------------

-----SPUSTIT PRIVÁTNÍ KLÍČ----------KONCOVÝ PRIVÁTNÍ KLÍČ-----

Při importu certifikátu je nutné zajistit, aby byl klíč zahrnut do samotného souboru. Pokud máte privátní klíč samostatně v jiném formátu, budete muset klíč kombinovat s certifikátem. Některé certifikační autority poskytují certifikáty v různých formátech, takže před importem certifikátu se ujistěte, že jsou ve formátu. pem nebo. pfx. 

### <a name="formats-of-merge-csr-we-support"></a>Formáty sloučení CSR podporujeme
INTEGRACE podporuje 2 formáty založené na PEM. Můžete buď sloučit jeden certifikát s kódováním PKCS # 8 nebo formát P7B (řetěz certifikátů podepsaný certifikační autoritou) s kódováním base64. 

-----ZAČÍT CERTIFIKÁT UKONČIT---------------

V současné době nepodporujeme klíče ES ve formátu PEM.

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Vytvoření certifikátu s certifikační autoritou, která není partnerkou Key Vault  
 Tato metoda umožňuje pracovat s jinými certifikačními autoritami než s partnerskými poskytovateli Key Vault, což znamená, že vaše organizace může pracovat s certifikační autoritou podle svého výběru.  

![Vytvoření certifikátu s vlastní certifikační autoritou](../media/certificate-authority-1.png)  

 Následující popisy kroků odpovídají zeleným písmenům v předchozím diagramu.  

  (1) – v diagramu výše vaše aplikace vytváří certifikát, který interně začíná vytvořením klíče v trezoru klíčů.  

  (2) – Key Vault vrátí do vaší aplikace žádost o podepsání certifikátu (CSR).  

  (3) – vaše aplikace předá CSR vaší zvolené certifikační autoritě.  

  (4) – zvolená certifikační autorita odpoví certifikátem x509.  

  (5) – vaše aplikace dokončí vytváření nového certifikátu s fúzí certifikátu x509 z vaší certifikační autority.
