---
title: Společné parametry pracovního postupu při ověřování služby Azure Stack jako služba | Dokumentace Microsoftu
description: Společné parametry pracovních postupů pro ověřování služby Azure Stack jako služba
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: c50e4b5c9eb81c9386e2cb0db96a88de70dcb9e9
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44157799"
---
# <a name="workflow-common-parameters-for-azure-stack-validation-as-a-service"></a>Společné parametry pracovních postupů pro ověřování služby Azure Stack jako služba

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Společné parametry zahrnují hodnoty, jako jsou proměnné prostředí a uživatelské přihlašovací údaje vyžadované všechny testy v rámci ověření jako služba (VaaS). Můžete definovat tyto hodnoty na úrovni pracovního postupu. Při vytváření nebo úpravě pracovního postupu, uložte příslušné hodnoty. Pracovní postup v naplánovaný čas načte hodnoty pro test. 

## <a name="environment-parameters"></a>Parametry pro prostředí.

Parametry prostředí popisují prostředí Azure Stack v rámci testu. Tyto hodnoty musí poskytnout generování a nahrávání váš konfigurační soubor na razítko `&lt;link&gt;. [How to get the stamp info link].`

| Název parametru | Požaduje se | Typ | Popis |
|----------------------------------|----------|------|---------------------------------------------------------------------------------------------------------------------------------|
| Azure Stack sestavení | Požaduje se |  | Sestavit počtu nasazení Azure Stack (například 1.0.170330.9) |
| Verze výrobce OEM | Ano |  | Číslo verze balíčku výrobce OEM používá při nasazení Azure Stack. |
| Podpis pro výrobce OEM | Ano |  | Podpis balíčku výrobce OEM používá při nasazení Azure Stack. |
| ID tenanta AAD | Požaduje se |  | Tenant Azure Active Directory GUID zadávají během nasazení Azure Stack.|
| Oblast | Požaduje se |  | Oblast nasazení Azure Stack. |
| Koncový bod Resource Manageru tenanta | Požaduje se |  | Koncový bod pro operace Azure Resource Manageru tenanta (například https://management.<ExternalFqdn>) |
| Koncový bod Admin Resource Manageru | Ano |  | Koncový bod Azure Resource Manageru Tenanta operací (například https://adminmanagement.<ExternalFqdn>) |
| Externí plně kvalifikovaný název domény | Ano |  | Externí plně kvalifikovaný název domény použít jako příponu pro koncové body. (například local.azurestack.external nebo redmond.contoso.com). |
| Počet uzlů | Ano |  | Počet uzlů na nasazení. |

## <a name="test-parameters"></a>Parametry testu

Společné parametry testu obsahovat citlivé informace, které nelze uložené v konfiguračních souborech a musí být prováděno manuálně.

| Název parametru | Požaduje se | Typ | Popis |
|--------------------------------|------------------------------------------------------------------------------|------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Uživatelské jméno klienta | Požaduje se |  | Azure Active Directory správce Tenanta, které bylo zřízené již nebo musí být zřízené služby správce v adresáři AAD. Podrobnosti o zřizování účtu tenanta, naleznete v tématu [Začínáme s Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). Tato hodnota se používá testu k provedení operace na úrovni tenanta jako je nasazení šablony zřizovat prostředky (virtuální počítače, účty úložiště atd) a spouštět úlohy. Tato hodnota se používá testu k provedení operace na úrovni tenanta jako je nasazení šablony zřizovat prostředky (virtuální počítače, účty úložiště atd) a spouštět úlohy. |
| Heslo tenanta | Požaduje se |  | Heslo pro tenanta uživatele. |
| Uživatelské jméno správce služby | Požadováno: Ověření řešení, ověření balíčku<br>Není nutné: průchodu testu |  | Azure Active Directory správce tenanta adresáře AAD zadat během nasazování služby Azure Stack. |
| Heslo správce služeb | Požadováno: Ověření řešení, ověření balíčku<br>Není nutné: průchodu testu |  | Heslo pro uživatele, Správce služeb. |
| Uživatelské jméno správce cloudu | Požaduje se |  | Azure Stack účtu správce domény (například contoso\cloudadmin). Hledat uživatelské Role = "CloudAdmin" v konfiguračním souboru a vyberte hodnotu v značky uživatelského jména v konfiguračním souboru. |
| Heslo správce cloudu | Požaduje se |  | Heslo pro uživatele správce cloudu. |
| Diagnostika připojovací řetězec | Požaduje se |  | Identifikátor URI SAS pro účet úložiště Azure, na které diagnostické protokoly se zkopírují během byly spuštění testu. Pokyny pro generování identifikátoru URI SAS se nachází [nastavení účtu úložiště blob](azure-stack-vaas-set-up-account.md). |


## <a name="next-steps"></a>Další postup

- Další informace o [ověřování služby Azure Stack jako služba](https://docs.microsoft.com/azure/azure-stack/partner).
