---
title: Metody vytvoření certifikátu
description: Způsoby, jak vytvořit certifikát ve službě Key Vault.
services: key-vault
documentationcenter: ''
author: bryanla
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e17b4c9b-4ff3-472f-8c9d-d130eb443968
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: bryanla
ms.openlocfilehash: a2314eaac26688881dcf60f264f039081439db6a
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2018
ms.locfileid: "42058332"
---
# <a name="certificate-creation-methods"></a>Metody vytvoření certifikátu

 Certifikát pro Key Vault (KV) můžou vytvořit nebo importovat do služby key vault. Když se vytvoří certifikát KV je privátní klíč vytvoří uvnitř trezoru klíčů a nikdy vystavený certifikát vlastníka. Následují způsoby, jak vytvořit certifikát ve službě Key Vault:  

-   **Vytvořit certifikát podepsaný svým držitelem:** to bude vytvoření páru veřejného a privátního klíče a přidružte jej k certifikátu. Certifikát podepíše svůj vlastní klíč.  

-    **Ruční vytvoření nového certifikátu:** se vytvoření páru veřejného a privátního klíče a generovat žádosti o podepsání certifikátu X.509. Žádost o podepsání může být podepsány registrační autority nebo certifikační autorita. Pair – x509 podepsaný certifikát lze sloučit s probíhající klíč k dokončení KV certifikátu ve službě Key Vault. I když tato metoda vyžaduje další kroky, se poskytují větší zabezpečení vzhledem k tomu, že privátní klíč je vytvořen a s omezením pomocí specifikátoru do služby Key Vault. To je vysvětleno v následujícím diagramu.  

![Vytvořte certifikát s svou vlastní certifikační autoritu](media/certificate-authority-1.png)  

V následujících popisech odpovídají zelené lettered kroky na předchozím obrázku.

1. Ve výše uvedeném diagramu vaše aplikace vytváří certifikát, který interně začíná tím, že vytvoříte klíče v trezoru klíčů.
2. Vrátí služby Key Vault do vaší aplikace požádat o (Podepsání certifikátu)
3. Aplikace předá žádost o podepsání certifikátu vámi zvolené certifikační Autority.
4. Vaši vybranou certifikační Autority odpoví x X509 certifikátu.
5. Vaše aplikace dokončí nové vytvoření certifikátu s spojení X509 certifikát z certifikační Autority.

-   **Vytvořte certifikát s poskytovatelem známé vystavitele:** tato metoda vyžaduje, abyste k provedení jednorázové úlohy vytvoření objektu vystavitele. Jakmile je vytvořen objekt vystavitelů ve trezor klíčů, jeho název může být odkazováno v zásadách KV certifikátu. Požadavek na vytvoření certifikátu KV vytvoření páru klíčů v trezoru, který se komunikovat se službou poskytovatele vystavitele zobrazíte x x509 pomocí informací v objektu odkazovaný vystavitele certifikátu. Vytvoření certifikátu x509 certifikát se načte ze služby vystavitele a je sloučen s pár klíčů KV dokončit.  

![Vytvořit certifikát u certifikační autority služby Key Vault partnerství](media/certificate-authority-2.png)  

V následujících popisech odpovídají zelené lettered kroky na předchozím obrázku.

1. Ve výše uvedeném diagramu vaše aplikace vytváří certifikát, který interně začíná tím, že vytvoříte klíče v trezoru klíčů.
2. Odešle služby Key Vault a žádost o certifikát SSL k certifikační Autoritě.
3. Vaše aplikace hlasování, smyčky a čekání procesu pro Key Vault pro dokončení certifikát. Vytvoření certifikátu je dokončená, pokud Key Vault obdrží odpověď Certifikační autority s x509 certifikátu.
4. Certifikační Autorita reaguje na služby Key Vault vyžádat certifikát SSL s x X509 certifikát SSL.
5. Vytvoření vašeho nového certifikátu se dokončí s spojení X509 certifikátem pro CA.

## <a name="asynchronous-process"></a>Asynchronní proces
Vytvoření certifikátu KV je asynchronní proces. Tato operace vytvoří žádost o certifikát KV a vrátit stavový kód http 202 (přijato). Stav žádosti můžete sledovat pomocí cyklického dotazování čeká na objekt vytvořený pomocí této operace. Úplný identifikátor URI objektu čekající na vyřízení je vrácená v hlavičce umístění.  

Po dokončení žádosti o vytvoření certifikátu KV stav Čeká na objektu se změní na "Dokončit" z "inprogress" a vytvoří se nová verze KV certifikátu. To se stane aktuální verzi.  

## <a name="first-creation"></a>Vytvoření první
 Když se poprvé certifikát KV, adresovatelný klíč a tajný klíč služby se vytvoří také se stejným názvem jako u certifikátu. Pokud název se už používá, se nezdaří operace s kódem stavu http 409 (konflikt).
Adresovatelný klíč a tajný kód získat jejich atributy z atributů KV certifikát. Adresovatelný klíč a tajný klíč vytvořené tímto způsobem jsou označeny jako spravované klíče a tajné kódy, jejichž životnost se spravuje přes Key Vault. Spravované klíče a tajné kódy jsou jen pro čtení. Poznámka: Pokud KV certifikátu vyprší platnost nebo je zakázaný, odpovídající klíč a tajný klíč se stane nefunkční.  

 Pokud je toto první operace vytvoření certifikátu KV se zásada vyžaduje.  Zásada může být rovněž dodán s po sobě jdoucích vytvoření operací nahradit zdroj zásad. Pokud není zadán zásady, zásady prostředků ve službě slouží k vytvoření další verzi KV certifikátu. Všimněte si, že požadavek na vytvoření další verze je v průběhu, aktuální certifikát KV a odpovídající adresovatelný klíč a tajný klíč, zůstanou beze změny.  

## <a name="self-issued-certificate"></a>Samostatně vydaný certifikát
 Chcete-li vytvořit certifikát vystavený, nastavte název vystavitele jako "Vlastní" Zásady certifikátů, jak je znázorněno v následující fragment kódu ze zásady certifikátů.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Pokud není zadán název vystavitele, název vystavitele nastavená na "Neznámý". Když Vystavitel je "Neznámá", vlastníka certifikátu muset ručně získat x509 certifikát od vydavatele jeho výběr a pak sloučení x509 veřejný certifikát s certifikátem služby key vault čekající na objekt k dokončení vytvoření certifikátu.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Poskytovatelé partnerství certifikační Autority
Vytvoření certifikátu může být dokončené ručně nebo pomocí "svým" vystavitele. Key Vault také partnerům určitých poskytovatelů vystavitele pro zjednodušené vytváření certifikátů. Následující typy certifikátů, lze provést řazení pro trezor klíčů s poskytovateli vystavitele těchto partnerů.  

|Poskytovatel|Typ certifikátu|  
|--------------|----------------------|  
|DigiCert|Key Vault nabízí OV nebo EV pomocí certifikátů SSL s DigiCert|
|GlobalCert|Key Vault nabízí OV nebo EV pomocí certifikátů SSL s GlobalSign|

 Vystavitel certifikátu se entitě reprezentované v Azure Key Vault (KV) jako prostředek CertificateIssuer. Používá se k zadání informací o zdroji KV certifikátu; název vystavitele, poskytovatele, přihlašovací údaje a další podrobnosti o správě.

Mějte na paměti, že pořadí při umístění s poskytovateli vystavitele, může přijmout nebo přepsání x509 rozšíření certifikátu a období platnosti certifikátu na základě typu certifikátu.  

 Autorizace: Vyžaduje oprávnění k vytvoření/certifikáty.

 ## <a name="see-also"></a>Viz také
 - [Informace o klíčích, tajných kódů a certifikátů](about-keys-secrets-and-certificates.md)
 - [Monitorování a správa vytvoření certifikátu](create-certificate-scenarios.md)
