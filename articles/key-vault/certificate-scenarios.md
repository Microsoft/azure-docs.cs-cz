---
title: Začínáme s certifikáty služby Key Vault
description: Následující scénáře popisují některé z primární použití služby Key Vault certificate management včetně další kroky potřebné pro vytvoření vaší první certifikát do svého trezoru klíčů.
services: key-vault
documentationcenter: ''
author: bryanla
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: a788b958-3acb-4bb6-9c94-4776852aeea1
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: bryanla
ms.openlocfilehash: 1441e16e141bd33acfdfe4087f74afbc7b47bc77
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54118281"
---
# <a name="get-started-with-key-vault-certificates"></a>Začínáme s certifikáty služby Key Vault
Následující scénáře popisují některé z primární použití služby Key Vault certificate management včetně další kroky potřebné pro vytvoření vaší první certifikát do svého trezoru klíčů.

Jsou popsány následující:
- Vytvoření vaší první certifikát služby Key Vault
- Vytvoření certifikát u certifikační autority, která je ve spolupráci se službou Key Vault
- Vytvoření certifikát u certifikační autority, která není ve spolupráci se službou Key Vault
- Importovat certifikát

## <a name="certificates-are-complex-objects"></a>Certifikáty jsou komplexních objektů
Certifikáty se skládají z tři vzájemně propojené prostředky dohromady jako certifikát služby Key Vault; certifikát metadat, klíč a tajný klíč.


![Certifikáty jsou složitá](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Vytvoření vaší první certifikát služby Key Vault  
 Certifikát je možné vytvořit v Key Vault (KV), musí být úspěšně provedli požadované kroky 1 a 2 a trezor klíčů, musí existovat pro tohoto uživatele nebo organizaci.  

**Krok 1** -certifikační autority (CA) poskytovatele  
-   Zavádění jako správce IT, správce infrastruktury veřejných KLÍČŮ nebo kdokoli Správa účtů pomocí certifikačních autorit pro danou společnost angular (např. Contoso) je předpokladem pro používání služby Key Vault certifikáty.  
    Následující jsou aktuální partnered zprostředkovatele se službou Key Vault:  
    -   DigiCert - Key Vault nabízí OV SSL certifikáty s DigiCert.  
    -   GlobalSign - Key Vault nabízí OV SSL certifikáty s GlobalSign  
    -   WoSign - Key Vault nabízí OV SSL nebo certifikáty EV SSL s WoSign podle nastavení nakonfigurované podle zákazníka v jejich WoSign účtu na portálu WoSign.  

**Krok 2** -správcem účtu pro poskytovatele certifikační Autority vytvoří přihlašovací údaje služby Key Vault používané k registraci, obnovit a použít certifikáty SSL prostřednictvím služby Key Vault.

**Krok 3** -správce společnosti Contoso A společně s zaměstnanec společnosti Contoso (Key Vault uživatele), který vlastní certifikáty, v závislosti na certifikační Autority, certifikát můžete získat od správce nebo přímo z účtu s certifikační Autoritou.  

-   Zahájit operaci přidat přihlašovací údaje k trezoru klíčů pomocí [nastavení vystavitele certifikátu](/rest/api/keyvault/setcertificateissuer/setcertificateissuer) prostředků. Vystavitel certifikátu se entitě reprezentované v Azure Key Vault (KV) jako prostředek CertificateIssuer. Používá se k zadání informací o zdroji KV certifikátu; název vystavitele, poskytovatele, přihlašovací údaje a další podrobnosti o správě.
    -   Například MyDigiCertIssuer  
        -   Poskytovatel  
        -   Přihlašovací údaje – přihlašovací údaje účtu certifikační Autority. Každou certifikační Autoritu se konkrétní data.  

     Další informace o vytváření účtů s poskytovateli certifikační Autoritu, najdete v příspěvku související [blog služby Key Vault](https://aka.ms/kvcertsblog).  

**Krok 3.1** – nastavení [kontakty certifikátu](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) pro oznámení. Toto je kontakt pro uživatele služby Key Vault. Key Vault nevynucuje tento krok.  

Poznámka: Tento proces, krok 3.1, je jednorázová operace.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Vytvoření certifikátu CA ve spolupráci se službou Key Vault

![Vytvořit certifikát u certifikační autority služby Key Vault partnerství](media/certificate-authority-2.png)

**Krok 4** – v následujících popisech odpovídají zelené očíslovaných kroků v předchozím diagramu.  
  (1) – ve výše uvedeném diagramu, vaše aplikace vytváří certifikát, který interně začíná tím, že vytvoříte klíče v trezoru klíčů.  
  (2) – služby Key Vault odešle certifikát SSL žádosti k certifikační Autoritě.  
  (3) – aplikace se dotazuje, v procesu smyčky a počkejte, Key Vault pro dokončení certifikát. Vytvoření certifikátu je dokončená, pokud Key Vault obdrží odpověď Certifikační autority s x509 certifikátu.  
  (4) – certifikační Autority reaguje na služby Key Vault vyžádat certifikát SSL s x X509 certifikát SSL.  
  (5) – vaše nové vytvoření certifikátu se dokončí s spojení X509 certifikátem pro CA.  

  Uživatel služby Key Vault – vytvoří certifikát tak, že určíte zásady

  -   Opakujte podle potřeby  
  -   Omezení zásad  
      -   X509 vlastnosti  
      -   Vlastnosti klíče  
      -   Odkaz na Provider - > např. MyDigiCertIssure  
      -   Informace o obnovení - > např. 90 dní před vypršením platnosti  

  - Proces vytvoření certifikátu je obvykle asynchronního procesu a zahrnuje dotazování trezoru klíčů pro stav operace vytvoření certifikátu.  
[Získat operace certifikátu](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   Stav: dokončeno, se nezdařilo s informace o chybě, nebo zrušené  
      -   Z důvodu zpoždění vytvořit lze inicializovat operaci zrušit. Zrušení může nebo nemusí být účinné.  

## <a name="import-a-certificate"></a>Importovat certifikát  
 Můžete také – certifikát lze importovat do služby Key Vault – PFX nebo PEM.  

 Další informace o formát PEM najdete v části certifikáty [informace o klíčích, tajných kódů a certifikátů](about-keys-secrets-and-certificates.md).  

 Importujte certifikát – vyžaduje PEM a PFX na disku a mít privátní klíč. 
-   Je nutné zadat: trezor název a název certifikátu (zásad je volitelný)

-   PEM nebo soubory PFX obsahuje atributy, které KV můžete analyzovat a použít k naplnění Zásady certifikátů. Pokud je už zadaná Zásady certifikátů, KV se pokusí porovnat data z formátu PFX / PEM souboru.  

-   Po importu je konečné, následné operace použije nová zásada (nové verze).  

-   Pokud neexistují žádné další operace, jako první věc nemá služby Key Vault je odeslání oznámení o vypršení platnosti. 

-   Také uživatel může upravovat zásady, které je v době importu funkční, ale obsahuje výchozí hodnoty, pokud nebyly zadány žádné informace při importu. Například žádné informace o vystavitele  

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Vytvoření certifikátu se certifikační Autorita není ve spolupráci se službou Key Vault  
 Tato metoda umožňuje práci s jinými autoritami než služby Key Vault partnerství poskytovateli, což znamená, že vaše organizace můžete pracovat s podmíněným Přístupem podle svého výběru.  

![Vytvořte certifikát s svou vlastní certifikační autoritu](media/certificate-authority-1.png)  

 V následujících popisech krok odpovídají zelené lettered kroky na předchozím obrázku.  

  (1) – ve výše uvedeném diagramu, vaše aplikace vytváří certifikát, který interně začíná tím, že vytvoříte klíče v trezoru klíčů.  

  (2) – vrátí služby Key Vault do vaší aplikace požádat o (Podepsání certifikátu).  

  (3) – aplikace předá žádost o podepsání certifikátu vámi zvolené certifikační Autority.  

  (4) – vaši vybranou certifikační Autority jako odpověď vrátí x X509 certifikátu.  

  (5) – vaše aplikace dokončí nové vytvoření certifikátu s spojení X509 certifikát z certifikační Autority.

## <a name="see-also"></a>Viz také

- [Informace o klíčích, tajných kódech a certifikátech](about-keys-secrets-and-certificates.md)
