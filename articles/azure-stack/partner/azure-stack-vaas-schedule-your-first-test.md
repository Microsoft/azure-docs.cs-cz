---
title: Použít ověření jako služba pro portálu Azure Stack k naplánování vaší první test | Dokumentace Microsoftu
description: Používejte ověření jako služba pro portálu Azure Stack k naplánování prvního testu.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 8ff78f46283d3ee4baa7310aa56bcdabde85f2cc
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159618"
---
# <a name="quickstart-use-the-validation-as-a-service-portal-to-schedule-your-first-test"></a>Rychlý start: Použijte ověřování jako portál služby k naplánování prvního testu

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Zjistěte, jak naplánovat prvního testu se ověření jako služby (VaaS) portálu ke kontrole hardwaru. Místní agent musí být nasazené ve službě Azure Stack řešení se ověřují před spuštěním testů pro ověření.

V tomto rychlém startu přidáte řešení a spuštění testů.

## <a name="prerequisites"></a>Požadavky

Než budete postupovat podle tohoto rychlého startu, je potřeba mít:
 - Ověření jako účet služby. Pokyny najdete v tématu [nastavení ověření jako účet služby](azure-stack-vaas-set-up-account.md).  
- Místní agent nainstalovaný ve vašem systému. Pokyny najdete v tématu [nasazení místních virtuálních počítačů a testovací agent](azure-stack-vaas-test-vm.md).

## <a name="add-a-new-solution"></a>Přidání nových řešení

1. Přihlaste se k [portálu ověření](https://azurestackvalidation.com).

    ![Přihlaste se do portálu pro ověření](media/vaas_portalsignin.png)  

2. Vyberte **nové řešení**.
3. Zadejte název řešení a vyberte **Uložit**.

## <a name="create-a-solution-validation-workflow"></a>Vytvořit pracovní postup ověření řešení

1. Vyberte název řešení.
2. Vyberte **spravovat** na **ověření řešení** dlaždici.

    ![Ověření řešení](media/image2.png)

## <a name="create-a-solution-workflow"></a>Vytvořit pracovní postup řešení

1. Vyberte **nového řešení ověření**.
2. Zadejte název ověření.
3. Vyberte **minimální** nebo **maximální**.  
    - **Minimum**  
    Toto řešení má nakonfigurovanou Minimální podporovaný počet uzlů.  
    - **Maximální počet**  
    Toto řešení má nakonfigurovanou maximální podporovaný počet uzlů.
4. Přidáte parametry vašeho prostředí. Další informace najdete v tématu [přidat parametry životního prostředí](#add-environmental-parameters).
5. Přidáte společné parametry testu. Další informace najdete v tématu [přidávat společné parametry testu](#add-common-test-parameters).

    V závislosti na definici testu test může vyžadovat zadejte hodnotu bez ohledu na jejich společné parametry, nebo vám umožňuje přepsat hodnotu společného parametru.
6. Klikněte na tlačítko **odeslat** naplánování testu.

## <a name="add-environmental-parameters"></a>Přidat parametry životního prostředí

Přidejte následující parametry prostředí:

| Informace o průchodu testů | Požaduje se | Popis |
| --- | --- | --- | --- |
| Azure Stack sestavení | Požaduje se | Azure Stack sestavení, číslo (například 20170501.1) hodnota musí být platný Azure Stack sestavení verze, například 1.0.170330.9 nebo číslo |
| ID tenanta | Požaduje se | ID Tenanta Active Directory Musí to být identifikátor GUID (například ECA23256-6BA0-4F27-8E4D-AFB02F088363) |
| Oblast | Požaduje se | Oblast nasazení Azure Stack |
| Koncový bod Resource Manageru tenanta | Požaduje se | Koncový bod pro operace Azure Resource Manageru Tenanta (např. https://management.loc-ext.domain.com) |
| Koncový bod Admin Resource Manageru | Nepožaduje se | Koncový bod pro operace Azure Resource Manageru Tenanta (např. https://management.loc-ext.domain.com) |
| Externí plně kvalifikovaný název domény | Nepožaduje se | Externí plně kvalifikovaný název domény použít jako příponu pro koncové body. (například local.azurestack.external nebo redmond.contoso.com) |
| Počet uzlů | Požaduje se | Počet uzlů ve vašem řešení. |

## <a name="add-common-test-parameters"></a>Přidat společné parametry testu

Přidejte následující běžné parametry:

| Informace o průchodu testů | Požaduje se | Popis |
| --- | --- | --- |
| Uživatelské jméno klienta | Požaduje se | Tenant uživatelské jméno (třeba tenant@contoso.onmicrosoft.com) |
| Heslo tenanta | Požaduje se | Heslo pro příslušného tenanta. |
| Uživatelské jméno správce služby | Nepožaduje se | Tenant uživatelské jméno (třeba tenant@contoso.onmicrosoft.com) |
| Heslo správce služeb | Nepožaduje se | Uživatelské jméno správce služby (třeba serviceadmin@contoso.onmicrosoft.com) |
| Uživatelské jméno správce cloudu | Nepožaduje se | Azure Stack účtu správce domény (například contoso\cloudadmin) |
| Heslo správce cloudu | Nepožaduje se | |
|  Diagnostika připojovací řetězec | Nepožaduje se | Identifikátor URI SAS pro účet úložiště Azure, na které diagnostické protokoly se zkopírují během byly spuštění testu. Zobrazit [vytvoření objektu blob služby Azure storage k ukládání protokolů](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs). <br><br>Hodnota **diagnostiky připojovací řetězec** společný parametr se budou ukládat ve službě a zadávat v okamžiku plán pro všechny testy v pracovním postupu, které používají tento parametr. Když do 30 dnů od vypršení platnosti je adresa URL SAS, zobrazí výzva pro novou adresu SAS URL na stránce společné parametry. |
| Značka – název | Nepožaduje se |  K označení pracovního postupu můžete zadat popisná klíčová slova. Toto je název značky. |
| Značka – Hodnota | Nepožaduje se | K označení pracovního postupu můžete zadat popisná klíčová slova. Jedná se o hodnotu značky. |

## <a name="next-steps"></a>Další postup

- [Ověření nové řešení Azure Stack](azure-stack-vaas-validate-solution-new.md)  
- Další informace o [ověřování služby Azure Stack jako služba](https://docs.microsoft.com/azure/azure-stack/partner).
