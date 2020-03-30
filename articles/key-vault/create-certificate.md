---
title: Metody vytvoření certifikátu
description: Způsoby vytvoření certifikátu v trezoru klíčů.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: c27cde85952ca6d982accddad59eceae76e3f1e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194453"
---
# <a name="certificate-creation-methods"></a>Metody vytvoření certifikátu

 Certifikát trezoru klíčů (KV) lze vytvořit nebo importovat do trezoru klíčů. Při vytvoření certifikátu KV je soukromý klíč vytvořen uvnitř trezoru klíčů a nikdy není vystaven vlastníkovi certifikátu. Níže jsou uvedeny způsoby vytvoření certifikátu v trezoru klíčů:  

-   **Vytvoření certifikátu podepsaného svým držitelem:** Tím se vytvoří pár klíčů veřejného a soukromého sektoru a přidružení k certifikátu. Certifikát bude podepsán vlastním klíčem.  

-    **Vytvořte nový certifikát ručně:** Tím se vytvoří pár veřejných a soukromých klíčů a vygeneruje žádost o podpis certifikátu X.509. Žádost o podepsání může podepsat registrační úřad nebo certifikační úřad. Podepsaný certifikát x509 lze sloučit s čekajícím párem klíčů a dokončit certifikát KV v trezoru klíčů. Přestože tato metoda vyžaduje další kroky, poskytuje větší zabezpečení, protože soukromý klíč je vytvořen v trezoru klíčů a je omezen na něj. To je vysvětleno v diagramu níže.  

![Vytvoření certifikátu s vlastní certifikační autoritou](media/certificate-authority-1.png)  

Následující popisy odpovídají zeleným písmenům v předchozím diagramu.

1. V diagramu výše vaše aplikace vytváří certifikát, což interně začíná vytvořením klíče ve vašem trezoru klíčů.
2. Trezor klíčů vrátí do vaší aplikace žádost o podpis certifikátu (CSR)
3. Vaše aplikace předá žádost o podepsání certifikátu vaší zvolené certifikační autoritě.
4. Vybraná certifikační autorita odpoví certifikátem X509.
5. Aplikace dokončí vytvoření nového certifikátu sloučením certifikátu X509 z certifikační autority.

-   **Vytvořte certifikát se známým poskytovatelem vystavitele:** Tato metoda vyžaduje provést jednorázovou úlohu vytvoření objektu vystavittele. Jakmile je objekt vystavittele vytvořen ve vámi trezoru klíčů, jeho název může být odkazován v zásadách certifikátu KV. Požadavek na vytvoření takového certifikátu KV vytvoří v trezoru dvojici klíčů a bude komunikovat se službou poskytovatele vystavitele pomocí informací v objektu odkazovaného vystavitele k získání certifikátu x509. Certifikát x509 je načten ze služby vystavitela a je sloučen s dvojicí klíčů k dokončení vytvoření certifikátu KV.  

![Vytvoření certifikátu s přidruženou certifikační autoritou s partnerem trezoru klíčů](media/certificate-authority-2.png)  

Následující popisy odpovídají zeleným písmenům v předchozím diagramu.

1. V diagramu výše vaše aplikace vytváří certifikát, což interně začíná vytvořením klíče ve vašem trezoru klíčů.
2. Trezor klíčů odešle certifikační autoritě žádost o certifikát TLS/SSL.
3. Vaše aplikace se dotazuje Key Vaultu v procesu smyčky a čekání na dokončení certifikátu. Vytvoření certifikátu je dokončeno, když Key Vault obdrží odpověď certifikační autority s certifikátem X.509.
4. Certifikační autorita reaguje na žádost o certifikát TLS/SSL trezoru klíčů s certifikátem TLS/SSL X.509.
5. Vytvoření nového certifikátu je dokončeno sloučením certifikátu TLS/SSL X.509 pro certifikační autoritu.

## <a name="asynchronous-process"></a>Asynchronní proces
Vytvoření certifikátu KV je asynchronní proces. Tato operace vytvoří žádost o certifikát KV a vrátí stavový kód http 202 (Přijato). Stav požadavku lze sledovat dotazováním čekající objekt vytvořený touto operací. Úplný identifikátor URI čekajícího objektu je vrácen v hlavičce LOCATION.  

Po dokončení požadavku na vytvoření certifikátu KV se stav čekajícího objektu změní na "dokončeno" z "probíhá" a bude vytvořena nová verze certifikátu KV. To se stane aktuální verzí.  

## <a name="first-creation"></a>První vytvoření
 Při prvním vytvoření certifikátu KV je vytvořen adresovatelný klíč a tajný klíč se stejným názvem jako certifikát. Pokud je název již používán, operace se nezdaří s http stavový kód 409 (konflikt).
Adresovatelný klíč a tajný klíč získat jejich atributy z atributů certifikátu KV. Adresovatelný klíč a tajný klíč vytvořený tímto způsobem jsou označeny jako spravované klíče a tajné klíče, jejichž životnost je spravována trezorem klíčů. Spravované klíče a tajné klíče jsou jen pro čtení. Poznámka: Pokud vyprší platnost certifikátu KV nebo je zakázán, odpovídající klíč a tajný klíč se stanou nefunkčními.  

 Pokud se jedná o první operaci k vytvoření certifikátu KV, je vyžadována zásada.  Zásadu lze také dodat s následnými operacemi vytváření, které nahradí prostředek zásady. Pokud není zadána zásada, pak prostředek zásady ve službě se používá k vytvoření další verze certifikátu KV. Všimněte si, že zatímco požadavek na vytvoření další verze probíhá, aktuální certifikát KV a odpovídající adresovatelný klíč a tajný klíč zůstávají nezměněny.  

## <a name="self-issued-certificate"></a>Certifikát vydaný samostatně
 Chcete-li vytvořit certifikát s vlastním vydáním, nastavte název vystavittele jako "Vlastní" v zásadách certifikátu, jak je znázorněno v následujícím fragmentu z certifikačních zásad.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Pokud není zadán název vystavittele, je název vystavittele nastaven na "Neznámý". Pokud je vystavitel "Neznámý", vlastník certifikátu bude muset ručně získat certifikát x509 od vystavittele podle svého výběru a poté sloučit veřejný certifikát x509 s objektem certifikátu trezoru klíčů čekající na dokončení vytvoření certifikátu.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Partneři poskytovatelé certifikačníautority
Vytvoření certifikátu lze dokončit ručně nebo pomocí vystavitele "Self". Key Vault také spolupracuje s určitými poskytovateli emitentů, aby zjednodušil vytváření certifikátů. Následující typy certifikátů lze objednat pro trezor klíčů s těmito poskytovateli vystavitele partnera.  

|Poskytovatel|Typ certifikátu|  
|--------------|----------------------|  
|DigiCert|Key Vault nabízí Certifikáty OV nebo EV SSL s DigiCert|
|Globální znak|Trezor klíčů nabízí certifikáty OV nebo EV SSL s GlobalSign|

 Vystavitel certifikátu je entita reprezentovaná v trezoru klíčů Azure (KV) jako prostředek certifikátu issuer. Používá se k poskytnutí informací o zdroji certifikátu KV; název vystavitele, zprostředkovatele, pověření a další podrobnosti o správě.

Všimněte si, že při zadání objednávky u poskytovatele vystavitele může respektovat nebo přepsat rozšíření certifikátu x509 a dobu platnosti certifikátu na základě typu certifikátu.  

 Autorizace: Vyžaduje oprávnění k vytvoření certifikátů nebo vytvoření.

## <a name="see-also"></a>Viz také
 - [Klíče, tajné klíče a certifikáty](about-keys-secrets-and-certificates.md)
 - [Monitorování a správa vytvoření certifikátu](create-certificate-scenarios.md)
