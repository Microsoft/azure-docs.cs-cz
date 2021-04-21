---
title: Metody vytvoření certifikátu
description: Přečtěte si o různých možnostech, jak vytvořit nebo importovat certifikát Key Vault v Azure Key Vault. Existuje několik způsobů, jak vytvořit certifikát Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 72ff2a1a7b8bcff768248833183ce03a169f9a4d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752115"
---
# <a name="certificate-creation-methods"></a>Metody vytvoření certifikátu

 Certifikát Key Vault (KV) může být buď vytvořen, nebo importován do trezoru klíčů. Při vytvoření certifikátu KV se privátní klíč vytvoří v trezoru klíčů a nikdy se nezveřejňuje vlastníkovi certifikátu. Následující postup slouží jako způsob vytvoření certifikátu v Key Vault:  

-   **Vytvoření certifikátu podepsaného svým držitelem:** Tím se vytvoří pár veřejného klíče a přidruží se k němu certifikát. Certifikát bude podepsán vlastním klíčem.  

-    **Vytvořte nový certifikát ručně:** Tím se vytvoří pár privátních klíčů a vygeneruje se žádost o podepsání certifikátu X. 509. Žádost o podepsání může podepsat autorita pro registraci nebo certifikační autorita. Podepsaný certifikát x509 se dá sloučit s neřízeným párem klíčů, aby se mohl dokončit certifikát KV v Key Vault. I když tato metoda vyžaduje více kroků, nabízí větší zabezpečení, protože privátní klíč je vytvořený v a omezený na Key Vault. To je vysvětleno v diagramu níže.  

![Vytvoření certifikátu s vlastní certifikační autoritou](../media/certificate-authority-1.png)  

Následující popisy odpovídají zeleným písmenovým krokům v předchozím diagramu.

1. V diagramu výše vaše aplikace vytváří certifikát, což interně začíná vytvořením klíče ve vašem trezoru klíčů.
2. Key Vault vrátí do vaší aplikace žádost o podepsání certifikátu (CSR)
3. Vaše aplikace předá žádost o podepsání certifikátu vaší zvolené certifikační autoritě.
4. Zvolená certifikační autorita odpoví certifikátem x509.
5. Vaše aplikace dokončí vytváření nového certifikátu s fúzí certifikátu x509 z vaší certifikační autority.

-   **Vytvoření certifikátu se známým poskytovatelem vystavitele:** Tato metoda vyžaduje, abyste provedete jednorázovou úlohu vytvoření objektu vystavitele. Jakmile se v trezoru klíčů vytvoří objekt vystavitele, na jeho název se dá odkazovat v zásadách certifikátu KV. Požadavek na vytvoření takového certifikátu KV vytvoří pár klíčů v trezoru a nakomunikuje se službou poskytovatele vystavitelů s použitím informací v odkazovaném objektu vystavitele, aby získal certifikát x509. Certifikát x509 se načte ze služby vystavitele a sloučí se s dvojicí klíčů, aby se dokončilo vytváření certifikátu KV.  

![Vytvoření certifikátu s Key Vault Partnerská certifikační autorita](../media/certificate-authority-2.png)  

Následující popisy odpovídají zeleným písmenovým krokům v předchozím diagramu.

1. V diagramu výše vaše aplikace vytváří certifikát, což interně začíná vytvořením klíče ve vašem trezoru klíčů.
2. Key Vault odešle certifikační autoritě žádost o certifikát TLS/SSL.
3. Vaše aplikace se dotazuje Key Vaultu v procesu smyčky a čekání na dokončení certifikátu. Vytvoření certifikátu je dokončeno, když Key Vault obdrží odpověď certifikační autority s certifikátem X.509.
4. Certifikační autorita reaguje na žádosti o certifikát TLS/SSL Key Vault certifikátem TLS/SSL X. 509.
5. Vytvoření nového certifikátu se dokončí s fúzí certifikátu TLS/SSL X. 509 pro certifikační autoritu.

## <a name="asynchronous-process"></a>Asynchronní proces
KV – vytvoření certifikátu je asynchronní proces. Tato operace vytvoří žádost o certifikát KV a vrátí stavový kód HTTP 202 (přijato). Stav požadavku lze sledovat pomocí cyklického dotazování na objekt, který byl vytvořen touto operací. V hlavičce umístění se vrátí úplný identifikátor URI objektu, který čeká na vyřízení.  

Po dokončení žádosti o vytvoření certifikátu KV se stav objektu, který čeká na vyřízení, změní na dokončeno z probíhajícího a vytvoří se nová verze certifikátu KV. Tím se stane aktuální verze.  

## <a name="first-creation"></a>První vytvoření
 Při prvním vytvoření certifikátu KV se vytvoří také adresovatelný klíč a tajný kód se stejným názvem jako certifikát. Pokud se název už používá, operace se nezdaří se stavovým kódem HTTP 409 (konflikt).
Adresovatelný klíč a tajný klíč získají své atributy z atributů certifikátu KV. Adresovatelný klíč a tajný kód vytvořené tímto způsobem jsou označené jako spravované klíče a tajné klíče, jejichž životnost je spravovaná pomocí Key Vault. Spravované klíče a tajné kódy jsou jen pro čtení. Poznámka: Pokud platnost certifikátu KV vyprší nebo je zakázaná, bude se stát, že bude fungovat odpovídající klíč a tajný klíč.  

 Pokud se jedná o první operaci vytvoření certifikátu KV, vyžaduje se zásada.  Zásady je také možné dodávat s operacemi po úspěšném vytvoření, které nahradí prostředek zásad. Pokud není dodána zásada, použije se pro vytvoření další verze certifikátu KV prostředek zásady ve službě. Všimněte si, že zatímco probíhá žádost o vytvoření další verze, aktuální certifikát KV a odpovídající adresovatelný klíč a tajný kód zůstávají beze změny.  

## <a name="self-issued-certificate"></a>Certifikát vystavený svým držitelem
 Pokud chcete vytvořit certifikát vystavený svým držitelem, nastavte v zásadách certifikátu název vystavitele na "samoobslužné", jak je znázorněno v následujícím fragmentu kódu ze zásad certifikátu.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Pokud název vystavitele není zadaný, název vystavitele se nastaví na "Neznámý". Pokud je Vystavitel "Neznámý", vlastník certifikátu bude muset ručně získat certifikát x509 od vystavitele své volby a potom sloučit veřejný certifikát x509 s čekajícím objektem certifikátu trezoru klíčů, aby bylo možné dokončit vytváření certifikátu.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Poskytovatelé partnerských certifikačních autorit
Vytvoření certifikátu lze provést ručně nebo pomocí vystavitele "samotný". Key Vault taky partnery s určitými poskytovateli vystavitelů, aby zjednodušili vytváření certifikátů. Pro Trezor klíčů s těmito poskytovateli vystavitelů se dají seřadit tyto typy certifikátů.  

|Poskytovatel|Typ certifikátu|Nastavení konfigurace  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault nabízí certifikáty SSL OV nebo EV s DigiCert.| [Průvodce integrací](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault nabízí certifikáty SSL OV nebo EV s GlobalSign.| [Průvodce integrací](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

 Vystavitel certifikátu je entita reprezentovaná v Azure Key Vault (KV) jako prostředek CertificateIssuer. Slouží k poskytnutí informací o zdroji certifikátu KV; název vystavitele, poskytovatel, přihlašovací údaje a další podrobnosti o správě.

Všimněte si, že když je objednávka umístěná u poskytovatele vystavitele, může přijmout nebo přepsat rozšíření certifikátu x509 a období platnosti certifikátu na základě typu certifikátu.  

 Autorizace: vyžaduje oprávnění certifikáty/vytvořit.

## <a name="see-also"></a>Viz také

 - Průvodce vytvořením certifikátů v Key Vault pomocí [portálu](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-portal), rozhraní příkazového [řádku Azure](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-cli) [Azure PowerShell](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-powershell)
 - [Monitorování a správa vytvoření certifikátu](create-certificate-scenarios.md)
