---
title: Vygenerujete certifikáty infrastruktury veřejných klíčů Azure zásobníku pro nasazení Azure zásobníku integrované systémy | Microsoft Docs
description: Popisuje proces nasazení certifikátu PKI zásobník Azure pro Azure zásobníku integrované systémy.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: cfac573bc9f1bdec3fd884f8090e11514f1e93b3
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604705"
---
# <a name="azure-stack-certificates-signing-request-generation"></a>Azure zásobníku certifikáty Podepisování generování požadavku

Nástroj kontrolu připravenosti zásobník Azure popsané v tomto článku je k dispozici [z Galerie Powershellu](https://aka.ms/AzsReadinessChecker). Nástroj vytváří vhodný pro nasazení služby Azure zásobníku Podepisování žádostí o certifikát (zástupci oddělení služeb zákazníkům). Certifikáty by měl požadovaný, generovány a ověřit s dostatek času k otestování před nasazením.

Nástroj pro kontrolu připravenosti zásobník Azure (AzsReadinessChecker) provádí následující žádosti o certifikát:

 - **Standardní certifikát požadavky**  
    Žádosti podle [generovat certifikáty PKI pro nasazení Azure zásobníku](azure-stack-get-pki-certs.md).
 - **Typ žádosti**  
    Určuje, zda žádosti o podepsání certifikátu bude žádost o jeden nebo více požadavků.
 - **Platforma jako služba**  
    Volitelně můžete požádat o platforma jako služba (PaaS) názvy se certifikátů, jak je uvedeno v [požadavky na certifikáty infrastruktury veřejných klíčů Azure zásobníku - volitelné certifikáty PaaS](azure-stack-pki-certs.md#optional-paas-certificates).



## <a name="prerequisites"></a>Požadavky

Váš systém by měl splňovat následující požadavky před vygenerováním CSR(s) pro certifikáty PKI pro nasazení služby Azure zásobníku:

 - Kontrola připravenosti zásobníku Microsoft Azure
 - Certifikát atributy:
    - Název oblasti
    - Externí plně kvalifikovaný název domény (FQDN)
    - Předmět
 - Windows 10 nebo Windows Server 2016
 
  > [!NOTE]
  > Jakmile se zobrazí zpět certifikáty od certifikační autority kroky v [certifikáty Příprava PKI zásobník Azure](azure-stack-prepare-pki-certs.md) bude nutné provést ve stejném systému!

## <a name="generate-certificate-signing-requests"></a>Vygeneruje certifikát pro podepisování žádostí

Pomocí těchto kroků můžete připravit a ověřit certifikáty PKI zásobník Azure: 

1.  Instalace AzsReadinessChecker z řádku prostředí PowerShell (5.1 nebo novější), spuštěním následující rutiny:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ````

2.  Deklarovat **subjektu** jako seřazený slovníku. Příklad: 

    ````PowerShell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"} 
    ````
    > [!note]  
    > Pokud se běžný název (CN) to budou přepsány první název DNS žádosti o certifikát.

3.  Deklarujte výstupní adresář, který již existuje. Příklad:

    ````PowerShell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ````
4.  Deklarovat identifikovat systému

    Azure Active Directory

    ```PowerShell
    $IdentitySystem = "AAD"
    ````

    Služba AD FS (Active Directory Federation Services)

    ```PowerShell
    $IdentitySystem = "ADFS"
    ````

5. Deklarovat **název oblasti** a **externí plně kvalifikovaný název domény** určený pro nasazení Azure zásobníku.

    ```PowerShell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ````

    > [!note]  
    > `<regionName>.<externalFQDN>` Tento balíček je základem, na kterém jsou všechny externí názvy DNS v zásobníku Azure vytvořili, v tomto příkladu, portálu budou `portal.east.azurestack.contoso.com`.  

6. K vytvoření žádosti jeden certifikát s více alternativní názvy subjektu:

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ````

    Zahrnout PaaS služby zadejte přepínač ```-IncludePaaS```

7. Pro vytvoření jednotlivých certifikátu Podepisování žádostí pro každý název DNS:

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType MultipleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ````

    Zahrnout PaaS služby zadejte přepínač ```-IncludePaaS```

8. Zkontrolujte výstup:

    ````PowerShell  
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Request Generation

    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Finished Certificate Request Generation

    AzsReadinessChecker Log location: C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\1.1803.405.3\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

9.  Odeslání **. No** souboru vygenerovaného na certifikační Autorita (interní nebo veřejná).  Do výstupního adresáře **Start-AzsReadinessChecker** obsahuje CSR(s) potřebné k odeslání certifikační autoritě.  Obsahuje taky podřízené adresáře, který obsahuje soubory INF používá při generování žádosti o certifikát, jako odkaz. Ujistěte se, že certifikační Autorita vygeneruje certifikáty pomocí generovaného žádost, které splňují [požadavky na Azure zásobníku PKI](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Další postup

[Příprava certifikátů PKI zásobník Azure](azure-stack-prepare-pki-certs.md)
