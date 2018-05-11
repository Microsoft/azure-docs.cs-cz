---
title: Metody vytváření certifikátu
description: Jak vytvořit certifikát v Key Vault.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e17b4c9b-4ff3-472f-8c9d-d130eb443968
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: 7b71c6a8daa97300ecf3b37ec6ab47207fece98e
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="certificate-creation-methods"></a>Metody vytváření certifikátu

 Certifikát klíč trezoru (KV) můžou vytvořit nebo importovat do trezoru klíčů. Při vytváření certifikátu KV privátní klíč je vytvářet v trezoru klíčů a nikdy vystavený certifikát vlastníka. Následující části jsou způsoby, jak vytvořit certifikát v Key Vault:  

-   **Vytvořit certifikát podepsaný svým držitelem:** tato akce vytvoří pár veřejného a privátního klíče a přidružte ji k certifikátu. Certifikát podepisuje vlastní klíč.  

-    **Ruční vytvoření nového certifikátu:** tato akce vytvoří pár veřejného a privátního klíče a generovat žádosti o podepsání certifikátu X.509. Podpisový žádosti může být podepsány registrační autority nebo certifikační autorita. Spárujte podepsaný x509, které by se daly sloučit certifikát čeká na klíčem k dokončení KV certifikátu v Key Vault. I když tato metoda vyžaduje další kroky, se poskytují větší zabezpečení protože privátní klíč je vytvořen a omezena na Key Vault. To je vysvětleno v následujícím diagramu.  

![Vytvoření certifikátu pomocí vlastní certifikační autority](media/certificate-authority-1.png)  

V následujících popisech odpovídají zelená písmeny kroky na předchozím obrázku.

1. V diagramu výše vaše aplikace vytváří certifikát, který interně začíná vytvořením klíč v trezoru klíčů.
2. Vrátí Key Vault do vaší aplikace žádost (Podepsání certifikátu)
3. Vaše aplikace předá zástupce pro vaši vybranou certifikační Autority.
4. Vaši vybranou certifikační Autority odpoví X509 certifikátu.
5. Vaše aplikace dokončení nové vytvoření certifikátu s fúzi X509 certifikátu z certifikační Autority.

-   **Vytvoření certifikátu pomocí zprostředkovatele známé vystavitele:** tato metoda vyžaduje, abyste provedení jednorázové úlohy vytváření objektu vystavitele. Jakmile je vytvořen objekt vystavitele v jste klíč trezoru, jeho název můžete odkazuje v zásadách KV certifikátu. Požadavek na vytvoření KV certifikát vytvoří pár klíčů v trezoru a komunikovat se službou poskytovatele vystavitele získat x509 pomocí informací v objekt odkazovaný vystavitele certifikátu. Vytvoření certifikátu x509 certifikát se načítají ze služby vystavitele a je sloučen s páru klíčů pro dokončení KV.  

![Vytvoření certifikátu pomocí Key Vault ve spolupráci certifikační autority](media/certificate-authority-2.png)  

V následujících popisech odpovídají zelená písmeny kroky na předchozím obrázku.

1. V diagramu výše vaše aplikace vytváří certifikát, který interně začíná vytvořením klíč v trezoru klíčů.
2. Odešle Key Vault a žádost o certifikát SSL k certifikační Autoritě.
3. Vaše aplikace hlasování v procesu smyčky a počkejte, pro Key Vault pro dokončení certifikátu. Po dokončení vytvoření certifikátu, když Key Vault obdrží odpověď Certifikační autority se x509 certifikátu.
4. Certifikační Autority odpoví na Key Vault SSL žádost o certifikát s X509 certifikát SSL.
5. Dokončení vytváření vašeho nového certifikátu s spojení X509 certifikát certifikační autority.

## <a name="asynchronous-process"></a>Asynchronní proces
Vytvoření certifikátu KV je asynchronní proces. Tato operace vytvoří žádost o certifikát KV a vrátit stavový kód http 202 (platných). Stav požadavku na lze sledovat pomocí cyklického dotazování objekt čekající na vyřízení vytvořené z této operace. Úplný identifikátor URI čekající objektu je vrácená v hlavičce umístění.  

Po dokončení žádosti o vytvoření certifikátu KV stav Čeká na objektu se změní na "dokončeno" z "inprogress" a vytvoří se nová verze KV certifikátu. To se stane aktuální verze.  

## <a name="first-creation"></a>První vytváření
 Při vytváření certifikátu KV poprvé adresovatelné klíč a tajný klíč je vytvořen také se stejným názvem jako u certifikátu. Pokud název je již používán, operace selže s kódem stavu http 409 (konflikt).
Adresovatelné klíč a tajný klíč získat jejich atributů z atributy KV certifikátu. Adresovatelné klíč a tajný klíč vytvořené tímto způsobem jsou označeny jako spravované klíče a tajné klíče, jejichž doba platnosti spravuje Key Vault. Spravované klíče a tajné klíče jsou jen pro čtení. Poznámka: Pokud certifikát KV vyprší platnost nebo je zakázaná, odpovídající klíč a tajný klíč bude dojít k vyřazení.  

 Pokud toto je první operaci vytvoření certifikátu KV zásad není potřeba.  Zásady můžete taky zadat s následných vytvoření operací nahradit zdroj zásad. Pokud je zásada není zadaný, prostředek zásad služby se používá k vytvoření další verzi KV certifikátu. Všimněte si, že když se požadavek na vytvoření další verze právě probíhá, aktuální KV certifikát a odpovídající adresovatelné klíč a tajný klíč, zůstanou beze změny.  

## <a name="self-issued-certificate"></a>Samoobslužné vydaných certifikátů
 Chcete-li vytvořit certifikát vystavený, nastavte název vystavitele jako "Vlastní" Zásady certifikátů, jak je znázorněno v následující fragment kódu ze zásady certifikátů.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Pokud není zadán název vystavitele, název vystavitele nastavená na "Neznámý". Při Vystavitel je "Neznámý", vlastník certifikát bude mít k ručně x509 získat certifikát od vystavitele jejich výběr a potom certifikát sloučení veřejné x509 certifikátem trezoru klíčů čekající na vyřízení objekt vytvoření certifikátu.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Zprostředkovatelé spolupráci certifikační Autority
Vytvoření certifikátu může být dokončené ručně nebo pomocí "svým" vystavitele. Key Vault také partnerům určité poskytovatelé vystavitele zjednodušení vytváření certifikátů. Následující typy certifikátů, lze provést řazení pro trezor klíčů s těchto poskytovatelů vydavatelů partnera.  

|Zprostředkovatel|Typ certifikátu|  
|--------------|----------------------|  
|DigiCert|Key Vault nabízí OV nebo rozšířené ověření pomocí certifikátů SSL s DigiCert|
|GlobalCert|Key Vault nabízí OV nebo rozšířené ověření pomocí certifikátů SSL s GlobalSign|

 Další informace, včetně zeměpisné dostupnost těchto poskytovatelů vystavitele, najdete v části [vystavitelů certifikátů](/rest/api/keyvault/certificate-issuers.md).

Poznámka: když pořadí je umístěn s poskytovatelem vystavitele, může respektovat nebo přepsání x509 rozšíření certifikátu a období platnosti certifikátu na základě typu certifikátu.  

 Autorizace: Vyžaduje oprávnění k vytvoření nebo certifikáty.

 ## <a name="see-also"></a>Viz také
 - [Informace o klíčích, tajných klíčů a certifikátů](about-keys-secrets-and-certificates.md)
 - [Sledování a správě vytváření certifikátu](create-certificate-scenarios.md)
