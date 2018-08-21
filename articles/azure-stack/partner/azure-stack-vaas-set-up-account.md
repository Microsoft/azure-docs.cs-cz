---
title: Nastavení ověření jako účet služby Azure Stack | Dokumentace Microsoftu
description: Další informace o nastavení ověření jako účet služby.
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
ms.openlocfilehash: f6c78a3e79ac88194e7e34ad8be7a941ee715d39
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235279"
---
# <a name="set-up-your-validation-as-a-service-account"></a>Nastavení ověření jako účet služby

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ověření jako služba (VaaS) je služba Azure, který je k dispozici pro partnery Microsoft Azure Stack, kteří mají společné engineering smlouvy se společností Microsoft pro návrh, vývoj, ověření, prodej, nasazení a podporu řešení pro Azure Stack na trhu.

Zjistěte, jak se připravit váš systém pro ověření jako služba. Nastavit instanci Azure Active Directory a provádět další požadované úlohy pro přípravou před použitím VaaS. 

Mezi úlohy patří:

- Vytvoření objektu blob služby Azure Storage k ukládání protokolů
- Nasazení místního agenta
- Stáhněte si zkušební image virtuálních počítačů v instanci služby Azure Stack má být testována

## <a name="create-an-azure-active-directory-tenant-id"></a>Vytvoření ID tenanta Azure Active Directory

1. Vytvoření tenanta Azure Active Directory v [webu Azure portal](https://portal.azure.com) nebo použijte existujícího tenanta.

    Doporučuje se vytvořit tenanta speciálně pro použití s VaaS s popisný název, jako jsou například ContosoVaaS@onmicrosoft.com. Funkce řízení přístupu na základě Role tenanta se použije partnerem, který Pokud chcete spravovat, kdo v organizaci partnera poskytujícího používat VaaS.  
    
    Další informace najdete v tématu [Správa adresáře služby Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-administer).

    > [!Note]  
    > Další informace o vytváření nových tenantů Azure Active Directory najdete v tématu [Začínáme s Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad).

2. Přidáte členy vaší organizace, kteří odpovídají za použití služby do tenanta. Přiřadíte každého uživatele v tenantovi, jeden z následujících rolí pro řízení jejich úroveň přístupu k VaaS:

    | Název role | Popis |
    |---------------------|------------------------------------------|
    | Vlastník | Má úplný přístup ke všem prostředkům. |
    | Čtenář | Můžete zobrazit všechny prostředky, ale nikoli upravovat. |
    | Přispěvatel testu | Můžete spravovat testovací prostředky. |
    | Přispěvatel katalogu | Můžete spravovat prostředky publikování řešení. |

## <a name="set-up-your-tenant"></a>Nastavení klientů

Nastavení klientů v **služby Azure Stack ověření** aplikace. 

1. Poslat Microsoftu na následující informace o tenantovi vaashelp@microsoft.com.

    | Data | Popis |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | Název organizace | Organizaci oficiální název. |
    | Název adresáře Tenanta Azure AD | Název služby Azure AD Tenanta adresáře registrována. |
    | Id adresáře Tenanta Azure AD | Adresář Azure AD Tenanta identifikátor GUID přidružený k adresáři.<br> Informace o tom, jak najít ID adresáře vašeho Tenanta služby Azure AD najdete najdete v tématu "[získání ID tenanta](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id)." |

    

2. Tým Azure Stack nabízí potvrzení, že váš tenant, můžete na portálu VaaS.

3. Přihlásit pomocí přihlašovacích údajů globálního správce pro tenanta [VaaS portál](https://azurestackvalidation.com/
). Vyberte **Můj účet**.

    ![Přihlášení k portálu VaaS](media/vaas_portalsignin.png)

3. Web se zobrazí výzva k udělení přístupu pro VaaS. Souhlas s podmínkami, aby bylo možné pokračovat.

## <a name="assign-user-roles"></a>Přiřadit role uživatele

Přiřazení role uživatele:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** > **Azure Active Directory** v **Identity** skupiny.
3. Vyberte **podnikové aplikace** > **služby Azure Stack ověření** aplikace.
4. Vyberte **Uživatelé a skupiny**. **Azure Stack ověřování služba - uživatelé a skupiny** okně zobrazí seznam uživatelů s oprávněním k používání aplikace.
5. Vyberte **+ přidat uživatele** přidat přiřazení.

## <a name="create-an-azure-storage-blob-to-store-logs"></a>Vytvoření objektu blob služby Azure storage k ukládání protokolů

Při spuštění ověřovacích testů, vytvoří VaaS diagnostické protokoly. Potřebujete umístění služby Azure blob service adresy URL SAS pro ukládání protokolů. Účet úložiště také slouží k přizpůsobení balíčky úložiště výrobce OEM.

Tyto kroky se provedou v rámci nastavení a vygenerovat identifikátor URI úložiště as-a-service (SAS) pro účet úložiště Azure a kde zadejte účet služby storage na portálu VaaS při spouštění testů na portálu.

### <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure

1. Chcete-li vytvořit účet úložiště, postupujte podle pokynů, [vytvořit účet úložiště](https://docs.microsoft.com/azure/storage/storage-create-storage-account#create-a-storage-account).

2. Když vyberete typ účtu úložiště, vyberte **úložiště objektů Blob** typ účtu.

### <a name="generate-a-sas-url-for-the-storage-account"></a>Generování adresy URL SAS pro účet úložiště

1. Přejděte do účtu úložiště vytvořeného výše.

2. V okně v části **nastavení**vyberte **sdílený přístupový podpis**.

3. Zkontrolujte pouze **Blob** z **možnosti povolené služby** (zrušit zaškrtnutí, zbývající).

4. Zkontrolujte **služby**, ** kontejner, a **objekt** z **povolené typy prostředků**.

5. Zkontrolujte **čtení**, **zápisu**, **seznamu**, **přidat**, **vytvořit** z **povolená oprávnění**  (zrušit zaškrtnutí, zbývající).

6. Nastavte **počáteční čas** na aktuální čas a **čas ukončení** do tří měsíců od aktuálního času.

7. Vyberte **vygenerovat SAS a připojovací řetězec** a uložit **Blob SAS URL služby** řetězec.

> [!Note]  
> Adresa URL SAS vyprší v čase ukončení, nastavte při generování adresy URL. Ujistěte se, že adresa URL je dostatečně platná před sdílením s produktovým týmem pro ladění nebo adresa URL je platná po dobu více než 30 dnů při plánování testů.

## <a name="next-steps"></a>Další postup

- Použijte místní agent VaaS ke kontrole hardwaru. Pokyny naleznete v tématu [nasazení místních virtuálních počítačů a testovací agent](azure-stack-vaas-test-vm.md).
- Další informace o [ověřování služby Azure Stack jako služba](https://docs.microsoft.com/azure/azure-stack/partner).