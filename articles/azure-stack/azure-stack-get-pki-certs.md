---
title: Vygenerujete certifikáty infrastruktury veřejných klíčů Azure zásobníku pro nasazení Azure zásobníku integrované systémy | Microsoft Docs
description: Popisuje Azure zásobníku infrastruktury veřejných KLÍČŮ certifikátu nasazení processfor zásobník Azure integrované systémy.
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
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: fc2ec96113310f54d32a67ea5fa31725600046c9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="generate-pki-certificates-for-azure-stack-deployment"></a>Generovat certifikáty PKI pro nasazení Azure zásobníku
Nyní když znáte [požadavcích na certifikáty PKI](azure-stack-pki-certs.md) pro nasazení zásobník Azure, budete muset získat tyto certifikáty z certifikační autority (CA) podle svého výběru. 

## <a name="request-certificates-using-an-inf-file"></a>Požadovat certifikáty pomocí soubor INF
Jedním ze způsobů žádosti o certifikáty z interní certifikační Autority nebo veřejné certifikační Autority je pomocí souboru INF. Nástroj Windows integrované certreq.exe, můžete použít soubor INF zadání podrobností certifikát, generovat soubor žádosti o, jak je popsáno v této části. 

### <a name="sample-inf-file"></a>Ukázkový soubor INF 
Příklad souboru INF žádost o certifikát slouží k vytvoření souboru žádosti o certifikát offline pro odeslání certifikační Autoritě (interní nebo veřejná). Soubor INF obsahuje požadované koncové body (včetně volitelné služby PaaS) v certifikát jeden zástupný znak. 

Ukázkový soubor INF předpokládá této oblasti se rovná **sea** a externí hodnota plně kvalifikovaný název domény je **sea&#46;contoso&#46;com**. Změnit tyto hodnoty tak, aby odpovídaly prostředí před vygenerováním. Soubor INF pro vaše nasazení. 

    
    [Version] 
    Signature="$Windows NT$"

    [NewRequest] 
    Subject = "C=US, O=Microsoft, L=Redmond, ST=Washington, CN=portal.sea.contoso.com"

    Exportable = TRUE                   ; Private key is not exportable 
    KeyLength = 2048                    ; Common key sizes: 512, 1024, 2048, 4096, 8192, 16384 
    KeySpec = 1                         ; AT_KEYEXCHANGE 
    KeyUsage = 0xA0                     ; Digital Signature, Key Encipherment 
    MachineKeySet = True                ; The key belongs to the local computer account 
    ProviderName = "Microsoft RSA SChannel Cryptographic Provider" 
    ProviderType = 12 
    SMIME = FALSE 
    RequestType = PKCS10
    HashAlgorithm = SHA256

    ; At least certreq.exe shipping with Windows Vista/Server 2008 is required to interpret the [Strings] and [Extensions] sections below

    [Strings] 
    szOID_SUBJECT_ALT_NAME2 = "2.5.29.17" 
    szOID_ENHANCED_KEY_USAGE = "2.5.29.37" 
    szOID_PKIX_KP_SERVER_AUTH = "1.3.6.1.5.5.7.3.1" 
    szOID_PKIX_KP_CLIENT_AUTH = "1.3.6.1.5.5.7.3.2"

    [Extensions] 
    %szOID_SUBJECT_ALT_NAME2% = "{text}dns=*.sea.contoso.com&dns=*.blob.sea.contoso.com&dns=*.queue.sea.contoso.com&dns=*.table.sea.contoso.com&dns=*.vault.sea.contoso.com&dns=*.adminvault.sea.contoso.com&dns=*.dbadapter.sea.contoso.com&dns=*.appservice.sea.contoso.com&dns=*.scm.appservice.sea.contoso.com&dns=api.appservice.sea.contoso.com&dns=ftp.appservice.sea.contoso.com&dns=sso.appservice.sea.contoso.com&dns=adminportal.sea.contoso.com&dns=management.sea.contoso.com&dns=adminmanagement.sea.contoso.com" 
    %szOID_ENHANCED_KEY_USAGE% = "{text}%szOID_PKIX_KP_SERVER_AUTH%,%szOID_PKIX_KP_CLIENT_AUTH%"

    [RequestAttributes]
    

## <a name="generate-and-submit-request-to-the-ca"></a>Generování a odeslat žádost certifikační Autority
Následující pracovní postup popisuje, jak můžete přizpůsobit a použijte vzorový soubor INF generované dříve k vyžádání certifikátu od certifikační Autority:

1. **Upravit a uložit soubor INF**. Kopírování ukázku zadat a uložit ho do nového textového souboru. Název subjektu a externí plně kvalifikovaný název domény nahraďte hodnoty, které odpovídají vaše nasazení a soubor uložte jako. Soubor INF.
2. **Vytvořit požadavek s použitím certreq**. Pomocí počítači se systémem Windows, spusťte příkazový řádek jako správce a spusťte následující příkaz k žádosti (.req) soubor: `certreq -new <yourinffile>.inf <yourreqfilename>.req`.
3. **Odeslání certifikační Autority**. Odeslání. Soubor REQ generována do vaší certifikační Autority (může být interní nebo veřejná).
4. **Import. CER**. Vrátí certifikační Autority. Soubor CER. Používáte stejný počítač systému Windows, ze kterého jste vygenerovali soubor žádosti, importujte. Soubor CER vrácen do počítače nebo osobním úložišti. 
5. **Exportovat a kopírovat. Soubor PFX do složek pro nasazení**. Export certifikátu (včetně privátního klíče) jako. Soubor PFX soubor a zkopírujte. Soubor PFX do složek nasazení popsané v [požadavky na Azure zásobníku nasazení infrastruktury veřejných KLÍČŮ](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Další postup
[Příprava certifikátů PKI zásobník Azure](azure-stack-prepare-pki-certs.md)
