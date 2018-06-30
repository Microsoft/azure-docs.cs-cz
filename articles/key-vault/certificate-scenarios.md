---
title: Začínáme s certifikáty Key Vault
description: Následující scénáře popisují některé z primární použití služby Key Vault certifikát správy včetně další kroky potřebné k vytvoření vašeho prvního certifikátu v trezoru klíčů.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: a788b958-3acb-4bb6-9c94-4776852aeea1
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: f1a1a2fa083dd1bf02132e08981d736a17a2c58f
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109480"
---
# <a name="get-started-with-key-vault-certificates"></a>Začínáme s certifikáty Key Vault
Následující scénáře popisují některé z primární použití služby Key Vault certifikát správy včetně další kroky potřebné k vytvoření vašeho prvního certifikátu v trezoru klíčů.

Jsou uvedeny následující:
- Vytvoření vaší první certifikát Key Vault
- Vytvoření certifikátu s certifikační autority, který je ve spolupráci s Key Vault
- Vytvoření certifikátu s certifikační autority, který není ve spolupráci s Key Vault
- Import certifikátu

## <a name="certificates-are-complex-objects"></a>Certifikáty jsou komplexní objekty
Certifikáty se skládají ze tří vzájemně souvisejících prostředků propojené společně jako certifikát Key Vault; certifikát metadata, klíč a tajný klíč.


![Certifikáty jsou komplexní](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Vytvoření vaší první certifikát Key Vault  
 Aby bylo možné vytvořit certifikát v Key Vault (KV), musí úspěšně provést požadované kroky 1 a 2 a trezoru klíčů, musí existovat pro tohoto uživatele nebo organizace.  

**Krok 1** -certifikátu certifikační autority zprostředkovatelé  
-   Jako správce IT, správce infrastruktury veřejných KLÍČŮ nebo každý, kdo Správa účtů s certifikačními autoritami, pro danou společnost (např Startovní. Contoso) je předpokladem pro používání certifikátů Key Vault.  
    Aktuální partnered zprostředkovatele s Key Vault jsou následující certifikační autority:  
    -   DigiCert - Key Vault nabízí OV SSL certifikáty s DigiCert.  
    -   GlobalSign - Key Vault nabízí OV SSL certifikáty s GlobalSign  
    -   WoSign - Key Vault nabízí OV SSL nebo certifikáty EV SSL s WoSign na základě nastavení konfigurovat tak, že zákazník v jejich WoSign účtu na portálu WoSign.  

**Krok 2** -účet správce pro poskytovatele certifikační Autority vytvoří přihlašovací údaje, které má být používána klíč trezoru k registraci, obnovit a použít certifikáty SSL prostřednictvím Key Vault.

**Krok 3** -správce společnosti Contoso A společně s zaměstnance společnosti Contoso (Key Vault uživatele), který vlastní certifikáty, v závislosti na certifikační Autoritě, můžete získat certifikát od správce nebo přímo z účtu s certifikační Autoritou.  

-   Začněte vytvořením operace přidat přihlašovací údaje k trezoru klíčů [Vystavitel certifikátu](https://docs.microsoft.com/rest/api/keyvault/certificate-issuers) prostředků. 
    -   Příklad: MyDigiCertIssuer  
        -   Poskytovatel  
        -   Přihlašovací údaje – přihlašovací údaje účtu certifikační Autority. Každá CA má svou vlastní konkrétní data.  

     Další informace o vytváření účtů zprostředkovatelům certifikační Autoritu, najdete v příspěvku související na [Key Vault blog](http://aka.ms/kvcertsblog).  

**Krok 3.1** – nastavte [certifikátu kontakty](https://docs.microsoft.com/rest/api/keyvault/certificate-contacts) pro oznámení. Toto je kontakt pro uživatele Key Vault. Key Vault nevynucuje tento krok.  

Poznámka: Tento proces, pomocí kroku 3.1, je jednorázově operace.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Vytvoření certifikátu s certifikační Autority ve spolupráci s Key Vault

![Vytvoření certifikátu pomocí Key Vault ve spolupráci certifikační autority](media/certificate-authority-2.png)

**Krok 4** – v následujících popisech odpovídají zelenou číslovaný kroky na předchozím obrázku.  
  (1) – v diagramu výše, vaše aplikace vytváří certifikát, který interně začíná vytvořením klíč v trezoru klíčů.  
  (2) - Key Vault odešle certifikát SSL požadavku k certifikační Autoritě.  
  (3) – aplikace se dotazuje, v procesu smyčky a počkejte, na Key Vault pro dokončení certifikátu. Po dokončení vytvoření certifikátu, když Key Vault obdrží odpověď Certifikační autority se x509 certifikátu.  
  (4) - certifikační Autority odpoví na Key Vault SSL žádost o certifikát s X509 certifikát SSL.  
  (5) - dokončení vytváření vašeho nového certifikátu s spojení X509 certifikát certifikační autority.  

  Uživatel Key Vault – vytvoří certifikát zadáním zásadu

  -   Opakujte podle potřeby  
  -   Omezení zásad  
      -   X509 vlastnosti  
      -   Klíčové vlastnosti  
      -   Reference pro zprostředkovatele - > např. MyDigiCertIssure  
      -   Informace o obnovení - > např. 90 dní před vypršením platnosti  

  - Proces vytvoření certifikátu je obvykle asynchronní proces a zahrnuje dotazování trezoru klíčů pro stav operace vytvoření certifikátu.  
[Získat operace certifikátu](https://docs.microsoft.com/en-us/rest/api/keyvault/getcertificateoperation)  
      -   Stav: dokončeno, se nezdařilo s informace o chybě, nebo zrušené  
      -   Z důvodu zpoždění k vytvoření lze inicializovat operace zrušení. Storno může nebo nemusí být účinné.  

## <a name="import-a-certificate"></a>Import certifikátu  
 Případně – certifikát lze importovat do Key Vault – PFX nebo PEM.  

 Další informace o formátu PEM, najdete v části certifikáty [informace o klíčích, tajných klíčů a certifikátů](about-keys-secrets-and-certificates.md).  

 Importujte certifikát – vyžaduje, aby PEM nebo PFX na disku a privátní klíč. 
-   Je nutné zadat: trezoru název a název certifikátu (zásad je volitelný)

-   PEM / soubory PFX obsahuje atributy, které KV můžete analyzovat a použít k naplnění Zásady certifikátů. Pokud zásady certifikátu byl již zadán, KV pokusí tak, aby odpovídaly data z PFX / PEM souboru.  

-   Po posledním import následných operací se pomocí nové zásady (nové verze).  

-   Pokud neexistují žádné další operace, je první věcí, kterou nemá Key Vault, odešlete oznámení vypršení platnosti. 

-   Uživatele můžete také upravit zásady, které je funkční v době importu, avšak obsahuje výchozí nastavení, kde nebyly zadány žádné informace na import. Příklad: žádné informace o vystavitele  

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Vytvoření certifikátu s certifikační Autorita není ve spolupráci s Key Vault  
 Tato metoda umožňuje práci s jiné certifikační autority než Key Vault ve spolupráci poskytovatelů, což znamená, že vaše organizace může spolupracovat s certifikační Autority podle svého výběru.  

![Vytvoření certifikátu pomocí vlastní certifikační autority](media/certificate-authority-1.png)  

 V následujících popisech krok odpovídají zelená písmeny kroky na předchozím obrázku.  

  (1) – v diagramu výše, vaše aplikace vytváří certifikát, který interně začíná vytvořením klíč v trezoru klíčů.  

  (2) - Key Vault vrátí do vaší aplikace žádost (Podepsání certifikátu).  

  (3) - aplikace předá zástupce pro vaši vybranou certifikační Autority.  

  (4) - vaši vybranou certifikační Autority odpoví X509 certifikátu.  

  (5) – aplikace dokončení nové vytvoření certifikátu s fúzi X509 certifikátu z certifikační Autority.

## <a name="see-also"></a>Viz také
- [Operace certifikátu](/rest/api/keyvault/certificate-operations)
- [Informace o klíčích, tajných kódech a certifikátech](about-keys-secrets-and-certificates.md)
