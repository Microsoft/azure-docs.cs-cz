---
title: Společné parametry pracovních postupů v Azure stacku ověření jako služba | Dokumentace Microsoftu
description: Společné parametry pracovních postupů pro Azure Stack ověření jako služba
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: ddc6942b56e3ad4d1f5b16c86dde87f408c1a2c1
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52262998"
---
# <a name="workflow-common-parameters-for-azure-stack-validation-as-a-service"></a>Společné parametry pracovních postupů pro Azure Stack ověření jako služba

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Společné parametry zahrnují hodnoty jako proměnné prostředí a uživatelské přihlašovací údaje vyžadované všechny testy v rámci ověření jako služba (VaaS). Tyto hodnoty jsou definovány na úrovni pracovního postupu při vytvoření nebo změna pracovního postupu. Při plánování testů, tyto hodnoty jsou předány jako parametry pro každý test v rámci pracovního postupu.

> [!NOTE]
> Každý test definuje vlastní sadu parametrů. V naplánovaný čas test může vyžadovat zadejte hodnotu bez ohledu na jejich společné parametry, nebo vám umožňuje přepsat hodnotu společného parametru.

## <a name="environment-parameters"></a>Parametry pro prostředí.

Parametry prostředí popisují prostředí Azure Stack v rámci testu. Tyto hodnoty musí poskytnout generování a nahrávání Azure Stack razítko souboru pro určitou instanci, kterou testujete.

> [!NOTE]
> V pracovních postupech oficiální ověření není možné měnit parametry prostředí po vytvoření pracovního postupu.

### <a name="generate-the-stamp-information-file"></a>Generovat soubor s informacemi o razítko

1. Přihlaste se DVM nebo jakýkoli počítač, který má přístup k prostředí Azure Stack.
2. V okně PowerShell se zvýšenými oprávněními spusťte následující příkazy:
    ```PowerShell  
    $CloudAdminUser = "<cloud admin username>"
    $CloudAdminPassword = ConvertTo-SecureString "<cloud admin password>" -AsPlainText -Force
    $stampInfoCreds = New-Object System.Management.Automation.PSCredential($CloudAdminUser, $CloudAdminPassword)
    $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation' -Credential $stampInfoCreds
    ConvertTo-Json $params > stampinfoproperties.json
    ```

### <a name="locate-values-in-the-ece-configuration-file"></a>Najít hodnoty v konfiguračním souboru předpisu

Hodnoty parametrů prostředí lze také ručně umístit do **OSN konfigurační soubor** umístění `C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787` na DVM.

## <a name="test-parameters"></a>Parametry testu

Společné parametry testu obsahovat citlivé informace, které nelze ukládat v konfiguračních souborech. Toto musí být prováděno manuálně.

Parametr    | Popis
-------------|-----------------
Uživatel s oprávněním správce tenanta                            | Azure Active Directory správce klienta, které bylo zřízené služby správce v adresáři AAD. Tento uživatel provede akce úrovni klienta, jako je nasazení šablony nastavit prostředky (virtuální počítače, účty úložiště atd.) a provádění úloh. Podrobnosti o zřizování účtu tenanta, naleznete v tématu [přidat nového tenanta služby Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-new-user-aad).
Uživatel s oprávněním správce služby             | Správce Azure Active Directory Directory tenanta AAD zadat během nasazování služby Azure Stack. Vyhledejte `AADTenant` v konfiguraci OSN soubor a vyberte hodnotu v `UniqueName` elementu.
Uživatel s oprávněním správce cloudu               | Účet správce domény Azure Stack (například `contoso\cloudadmin`). Vyhledejte `User Role="CloudAdmin"` v konfiguraci OSN soubor a vyberte hodnotu v `UserName` elementu.
Diagnostika připojovací řetězec          | Adresa URL SAS pro účet úložiště Azure, na které diagnostické protokoly se zkopírují během byly spuštění testu. Generuje se adresa URL SAS, v tématu [generovat připojovací řetězec diagnostiky](#generate-the-diagnostics-connection-string). |

> [!IMPORTANT]
> **Diagnostiky připojovací řetězec** musí být platná, než budete pokračovat.

### <a name="generate-the-diagnostics-connection-string"></a>Generování diagnostiky připojovací řetězec

Diagnostika připojovací řetězec je vyžadováno pro ukládání diagnostických protokolů během provádění testů. Použijte účet úložiště Azure, který se vytvoří během instalace (viz [nastavení ověření jako prostředky služby](azure-stack-vaas-set-up-resources.md)) a vytvoří adresu URL sdíleného přístupového podpisu (SAS) VaaS přístup jak odesílat protokoly do vašeho účtu úložiště.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Vyberte **Blob** z **možnosti povolené služby**. Zrušit všechny zbývající možnosti.

1. Vyberte **služby**, **kontejneru**, a **objekt** z **povolené typy prostředků**.

1. Vyberte **čtení**, **zápisu**, **seznamu**, **přidat**, **vytvořit** z **povoleno oprávnění**. Zrušit všechny zbývající možnosti.

1. Nastavte **počáteční čas** na aktuální čas a **čas ukončení** do tří měsíců od aktuálního času.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]

> [!NOTE]  
> Adresa URL SAS vyprší v čase ukončení zadat při generování adresy URL.  
Při plánování testů, ujistěte se, že adresa URL je platná po dobu nejméně 30 dnů a doby potřebné pro spuštění testu (doporučuje tří měsíců).

## <a name="next-steps"></a>Další postup

- Další informace o [ověření jako klíčové koncepty služby](azure-stack-vaas-key-concepts.md)