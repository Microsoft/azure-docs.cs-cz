---
title: Začínáme s certifikáty služby Key Vault
description: Následující scénáře popisují několik primárních použití služby správy certifikátů trezoru klíčů, včetně dalších kroků potřebných k vytvoření prvního certifikátu v trezoru klíčů.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 32a453678fe3702fcb4b77f0b04a8ed5c889ef59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271002"
---
# <a name="get-started-with-key-vault-certificates"></a>Začínáme s certifikáty služby Key Vault
Následující scénáře popisují několik primárních použití služby správy certifikátů trezoru klíčů, včetně dalších kroků potřebných k vytvoření prvního certifikátu v trezoru klíčů.

Jsou uvedeny následující:
- Vytvoření prvního certifikátu trezoru klíčů
- Vytvoření certifikátu s certifikační autoritou, která je partnerem trezoru klíčů
- Vytvoření certifikátu s certifikační autoritou, která není ve společnosti Key Vault
- Import certifikátu

## <a name="certificates-are-complex-objects"></a>Certifikáty jsou složité objekty
Certifikáty se skládají ze tří vzájemně propojených prostředků propojených jako certifikát trezoru klíčů. metadat certifikátu, klíče a tajného klíče.


![Certifikáty jsou složité](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Vytvoření prvního certifikátu trezoru klíčů  
 Před vytvořením certifikátu v trezoru klíčů (KV) musí být úspěšně provedeny nezbytné kroky 1 a 2 a pro tohoto uživatele /organizace musí existovat trezor klíčů.  

**Krok 1** – Poskytovatelé certifikačníautority (CA)  
-   On-boarding jako správce IT, Správce PKI nebo kdokoli, kdo spravuje účty u ca, pro danou společnost (např. Contoso) je předpokladem pro použití certifikátů trezoru klíčů.  
    Aktuálními partnerskými poskytovateli trezoru klíčů jsou následující společnosti:  
    -   DigiCert - Key Vault nabízí Certifikáty OV TLS/SSL s DigiCert.  
    -   GlobalSign - Key Vault nabízí Certifikáty OV TLS/SSL s GlobalSign.  

**Krok 2** – Správce účtu pro poskytovatele certifikační autority vytvoří pověření, která má trezor klíčů použít k zápisu, obnovení a použití certifikátů TLS/SSL prostřednictvím trezoru klíčů.

**Krok 3** – Správce společnosti Contoso spolu se zaměstnancem contoso (uživatel trezoru klíčů), který vlastní certifikáty, může v závislosti na certifikační autoritě získat certifikát od správce nebo přímo z účtu u certifikační autority.  

- Zahajte operaci přidání pověření do trezoru klíčů nastavením prostředku [vystavitela certifikátu.](/rest/api/keyvault/setcertificateissuer/setcertificateissuer) Vystavitel certifikátu je entita reprezentovaná v trezoru klíčů Azure (KV) jako prostředek certifikátu issuer. Používá se k poskytnutí informací o zdroji certifikátu KV; název vystavitele, zprostředkovatele, pověření a další podrobnosti o správě.
  - Například MyDigiCertIssuer  
    -   Poskytovatel  
    -   Pověření – pověření účtu certifikační autority. Každá certifikační autorita má svá vlastní specifická data.  

    Další informace o vytváření účtů u poskytovatelů certifikační autority naleznete v souvisejícím příspěvku na [blogu Key Vault](https://aka.ms/kvcertsblog).  

**Krok 3.1** – Nastavení [kontaktů certifikátů](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) pro oznámení. Toto je kontakt pro uživatele trezoru klíčů. Trezor klíčů tento krok nevynucuje.  

Poznámka - Tento proces, prostřednictvím kroku 3.1, je jednorázová operace.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Vytvoření certifikátu pomocí certifikační autority s partnerem trezoru klíčů

![Vytvoření certifikátu s přidruženou certifikační autoritou s partnerem trezoru klíčů](media/certificate-authority-2.png)

**Krok 4** - Následující popisy odpovídají zeleným číslovaným krokům v předchozím diagramu.  
  (1) - Ve výše uvedeném diagramu vaše aplikace vytváří certifikát, který interně začíná vytvořením klíče v trezoru klíčů.  
  (2) - Trezor klíčů odešle certifikační autoritě žádost o certifikát TLS/SSL.  
  (3) - Vaše aplikace dotazování, ve smyčce a čekání procesu pro trezor klíčů pro dokončení certifikátu. Vytvoření certifikátu je dokončeno, když Key Vault obdrží odpověď certifikační autority s certifikátem X.509.  
  (4) - Certifikační autorita reaguje na žádost o certifikát TLS/SSL trezoru klíčů s certifikátem X509 TLS/SSL.  
  (5) - Vytvoření nového certifikátu je dokončeno sloučením certifikátu X509 pro certifikační autoritu.  

  Uživatel trezoru klíčů – vytvoří certifikát zadáním zásady

  -   Opakujte podle potřeby  
  -   Politická omezení  
      -   Vlastnosti X509  
      -   Základní vlastnosti  
      -   Odkaz na zprostředkovatele - > ex. MyDigiCertIssure  
      -   Informace o obnovení - > ex. 90 dní před vypršením platnosti  

  - Proces vytváření certifikátů je obvykle asynchronní proces a zahrnuje dotazování trezoru klíčů pro stav operace vytvoření certifikátu.  
[Získat operaci certifikátu](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   Stav: dokončen, se nezdařilo s informacemi o chybě nebo bylo zrušeno  
      -   Z důvodu zpoždění vytvořit, operace zrušení může být zahájena. Zrušení může nebo nemusí být účinné.  

## <a name="import-a-certificate"></a>Import certifikátu  
 Alternativně – certifikát lze importovat do trezoru klíčů – PFX nebo PEM.  

 Další informace o formátu PEM naleznete v části Certifikáty [o klíčích, tajných klíčích a certifikátech](about-keys-secrets-and-certificates.md).  

 Importcertifikátu – vyžaduje, aby pem nebo PFX byl na disku a měl soukromý klíč. 
-   Je nutné zadat: název úložiště a název certifikátu (zásada je nepovinná)

-   Soubory PEM / PFX obsahuje atributy, které KV můžete analyzovat a použít k naplnění zásady certifikátu. Pokud je zásada certifikátu již zadána, KV se pokusí porovnat data ze souboru PFX / PEM.  

-   Jakmile je import konečný, následné operace budou používat nové zásady (nové verze).  

-   Pokud nejsou žádné další operace, první věc, kterou trezor klíčů dělá, je odeslání oznámení o vypršení platnosti. 

-   Uživatel může také upravit zásadu, která je funkční v době importu, ale obsahuje výchozí hodnoty, kde při importu nebyly zadány žádné informace. Například žádné informace o emitentu  

### <a name="formats-of-import-we-support"></a>Formáty importu, které podporujeme
Podporujeme následující typ importu pro formát souboru PEM. Jeden certifikát kódovaný PEM spolu s nezašifrovaným klíčem PKCS#8, který má následující

-----CERTIFIKÁTBEGIN----- -----KONEC-----

-----BEGIN PRIVATE KEY----- -----END PRIVATE KEY----------

Při sloučení certifikátů podporujeme formáty založené na 2 PEM. Můžete buď sloučit jeden kódovaný certifikát PKCS#8 nebo soubor P7B kódovaný base64. -----CERTIFIKÁTBEGIN----- -----KONEC-----

V současné době nepodporujeme klíče EC ve formátu PEM.

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Vytvoření certifikátu s certifikační autoritou, která není partnerem trezoru klíčů  
 Tato metoda umožňuje práci s jinými certifikačními autoritami než partnerskými poskytovateli trezoru klíčů, což znamená, že vaše organizace může pracovat s certifikační autoritou podle svého výběru.  

![Vytvoření certifikátu s vlastní certifikační autoritou](media/certificate-authority-1.png)  

 Následující popisy kroků odpovídají zeleným písmenům v předchozím diagramu.  

  (1) - Ve výše uvedeném diagramu vaše aplikace vytváří certifikát, který interně začíná vytvořením klíče v trezoru klíčů.  

  (2) - Trezor klíčů vrátí do vaší aplikace žádost o podpis certifikátu (CSR).  

  (3) - Vaše přihláška předá zástupce společnosti vybranécertifikační autoritě.  

  (4) - Vámi zvolená certifikační autorita odpoví certifikátem X509.  

  (5) - Vaše přihláška dokončí vytvoření nového certifikátu sloučením certifikátu X509 od certifikační autority.

## <a name="see-also"></a>Viz také

- [Informace o klíčích, tajných kódech a certifikátech](about-keys-secrets-and-certificates.md)
