---
title: Generování certifikátů infrastruktury veřejných klíčů v Azure stacku pro Azure Stack integrované systémy nasazení | Dokumentace Microsoftu
description: Popisuje postup nasazení Azure Stack infrastruktury veřejných KLÍČŮ certifikátu pro integrované systémy Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 9f88e71df7697156e0745aeaf6b989548bcc223f
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945107"
---
# <a name="azure-stack-certificates-signing-request-generation"></a>Azure Stack certifikáty Podepisování generování požadavku

Nástroj prerequisite Checker připravenosti Azure Stack popsaných v tomto článku je k dispozici [z Galerie prostředí PowerShell](https://aka.ms/AzsReadinessChecker). Nástroj vytvoří Podepisování žádostí o certifikát (nástroje CSR) vhodný pro nasazení služby Azure Stack. Certifikáty by měl požadovaná, vygeneruje a ověřit pomocí dostatek času k testování před nasazením.

Nástroj prerequisite Checker připravenosti Azure Stack (AzsReadinessChecker) provádí následující žádosti o certifikát:

 - **Standardní certifikát požadavků**  
    Požádat o podle [vygenerovat certifikáty PKI pro nasazení Azure stacku](azure-stack-get-pki-certs.md).
 - **Platforma jako služba**  
    Volitelně můžete požádat o platforma jako služba (PaaS) názvy certifikátů, jak je uvedeno v [požadavky na certifikáty infrastruktury veřejných klíčů v Azure stacku – volitelné PaaS certifikáty](azure-stack-pki-certs.md#optional-paas-certificates).



## <a name="prerequisites"></a>Požadavky

Váš systém by měl splňovat následující požadavky před generováním CSR(s) pro certifikáty PKI pro nasazení služby Azure Stack:

 - Kontrola připravenosti Microsoft Azure Stack
 - Atributy certifikátu:
    - Název oblasti
    - Externí plně kvalifikovaný název domény (FQDN)
    - Subjekt
 - Windows 10 nebo Windows Server 2016
 
  > [!NOTE]  
  > Když dostanete zpět certifikáty od certifikační autority kroky v [připravit Azure Stack infrastruktury veřejných KLÍČŮ certifikátů](azure-stack-prepare-pki-certs.md) bude nutné provést ve stejném systému!

## <a name="generate-certificate-signing-requests"></a>Generovat žádosti o podepsání certifikátu

Pomocí těchto kroků můžete připravit a ověřování certifikátů Azure Stack infrastruktury veřejných KLÍČŮ: 

1.  Nainstalujte AzsReadinessChecker z příkazového řádku Powershellu (5.1 nebo novější), spuštěním následující rutiny:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ````

2.  Deklarovat **subjektu** jako seřazený slovník. Příklad: 

    ````PowerShell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"} 
    ````
    > [!note]  
    > Pokud se běžný název (CN) se přepíšou podle názvu DNS prvního žádosti o certifikát.

3.  Deklarujte výstupní adresář, který již existuje. Příklad:

    ````PowerShell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ````
4.  Deklarovat systém identit

    Azure Active Directory

    ```PowerShell
    $IdentitySystem = "AAD"
    ````

    Služba AD FS (Active Directory Federation Services)

    ```PowerShell
    $IdentitySystem = "ADFS"
    ````

5. Deklarovat **název oblasti** a **plně kvalifikovaný název domény externího** určený pro nasazení Azure stacku.

    ```PowerShell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ````

    > [!note]  
    > `<regionName>.<externalFQDN>` Tento balíček je základem, na kterém jsou vytvořeny všechny externí názvy DNS ve službě Azure Stack, v tomto příkladu, bude portál `portal.east.azurestack.contoso.com`.  

6. Vygenerovat certifikát Podepisování žádostí pro každý název DNS:

    ```PowerShell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ````

    Aby byly služby PaaS, zadejte přepínač ```-IncludePaaS```

7. Další možností pro vývojová a testovací prostředí. K vygenerování žádosti o jeden certifikát s více alternativní názvy subjektů přidat **– typ RequestType SingleCSR** parametr a hodnotu (**není** doporučuje pro produkční prostředí):

    ```PowerShell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ````

    Aby byly služby PaaS, zadejte přepínač ```-IncludePaaS```
    
8. Zkontrolujte výstup:

    ````PowerShell  
    New-AzsCertificateSigningRequest v1.1809.1005.1 started.
    
    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com*dn2=*.adminhosting.east.azurestack.contoso.com@dns=*.hosting.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    New-AzsCertificateSigningRequest Completed
    ````

9.  Odeslání **. Počet žádostí o** souboru vygenerovaného CA (interní nebo veřejný).  Výstupní adresář **New-AzsCertificateSigningRequest** obsahuje CSR(s) nezbytné pro odeslání do certifikační autority.  Adresář obsahuje také, pro srovnání si podřízené adresář obsahující soubory INF používá při generování žádosti o certifikát. Ujistěte se, že certifikační Autorita generuje certifikáty pomocí generovaného požadavku, které splňují [požadavky na infrastrukturu veřejných KLÍČŮ Azure Stack](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Další postup

[Přidejte certifikáty Azure Stack PKI](azure-stack-prepare-pki-certs.md)
