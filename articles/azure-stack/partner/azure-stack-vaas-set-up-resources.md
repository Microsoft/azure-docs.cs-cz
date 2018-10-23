---
title: Kurz – nastavit prostředky pro ověření jako služba | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak nastavit prostředky pro ověření jako služba.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: d9406032a55c0bbd73bf16ae2f0fa272dddd7698
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49651696"
---
# <a name="tutorial-set-up-resources-for-validation-as-a-service"></a>Kurz: Nastavte prostředky pro ověření jako služba

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Je potřeba vytvořit řešení. Ověřování jako řešení služby (VaaS) představuje řešení typu Azure Stack s konkrétní hardware kusovník. Toto řešení bude používat ke kontrole, jestli váš hardware můžou podporovat spuštění služby Azure Stack. V tomto kurzu se připravit na používání služby v rámci řešení.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Používáním VaaS nastavením Azure AD (Azure AD) instance.
> * Vytvoření účtu úložiště

## <a name="configure-an-azure-ad-tenant"></a>Nakonfigurovat tenanta služby Azure AD

Je vyžadován pro ověřování a registraci VaaS tenanta služby Azure AD. Funkce řízení přístupu na základě role tenanta se použije partnerem, který Pokud chcete spravovat, kdo v organizaci partnera poskytujícího používat VaaS.

Registrace organizace služby Azure AD tenanta adresáře (spíše než adresář tenanta Azure AD použít pro službu Azure Stack) a vytvořit zásady pro správu uživatelských účtů v ní. Další informace najdete v tématu [Správa adresáře služby Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-administer).

### <a name="create-a-tenant"></a>Vytvoření tenanta

Vytvoření tenanta speciálně pro použití s VaaS s popisný název, například `ContosoVaaS@onmicrosoft.com`.

1. Vytvoření tenanta Azure AD v [webu Azure portal](https://portal.azure.com), nebo použijte existujícího tenanta. <!-- For instructions on creating new Azure AD tenants, see [Get started with Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). -->

2. Přidáte do tenanta členové vaší organizace. Tyto uživatele bude odpovědná za používání služby zobrazit nebo naplánovat testy. Po dokončení registrace bude definovat úrovně přístupu uživatelů.
 
    Autorizace uživatelů ve vašem tenantovi ke spouštění akcí v VaaS přiřazením jednu z následujících rolí:

    | Název role | Popis |
    |---------------------|------------------------------------------|
    | Vlastník | Má úplný přístup ke všem prostředkům. |
    | Čtenář | Nelze zobrazit všechny prostředky, ale vytvořit ani spravovat. |
    | Přispěvatel testu | Můžete vytvářet a spravovat testovací prostředky. |

    Přiřadit role v **služby Azure Stack ověření** aplikace:

    1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
    2. Vyberte **všechny služby** > **Azure Active Directory** pod **Identity** oddílu.
    3. Vyberte **podnikové aplikace** > **služby Azure Stack ověření** aplikace.
    4. Vyberte **Uživatelé a skupiny**. **Azure Stack ověřování služba - uživatelé a skupiny** okně zobrazí seznam uživatelů s oprávněním k používání aplikace.
    5. Vyberte **+ přidat uživatele** přidat uživatele z vašeho tenanta a přiřazení role.
   
    Pokud chcete izolovat VaaS prostředkům a akcím mezi různé skupiny v rámci organizace, můžete vytvořit více adresářů tenanta služby Azure AD.

### <a name="register-your-tenant"></a>Zaregistrujte vašeho tenanta

Tento proces povolí vašeho tenanta se **služby Azure Stack ověření** aplikaci Azure AD.

1. Poslat Microsoftu na následující informace o tenantovi [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

    | Data | Popis |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | Název organizace | Organizaci oficiální název. |
    | Název adresáře Tenanta Azure AD | Registrovaný název Tenanta služby Azure AD Directory. |
    | ID adresáře Tenanta Azure AD | Adresář Tenanta služby Azure AD, identifikátor GUID přidružený k adresáři. Informace o tom, jak najít ID adresáře vašeho Tenanta služby Azure AD najdete v tématu [získání ID tenanta](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id). |

2. Počkejte na potvrzení od týmu Azure Stack ověření a zkontrolujte, že váš tenant, můžete na portálu VaaS.

### <a name="consent-to-the-vaas-application"></a>Souhlas s aplikací VaaS

Jako správce Azure AD poskytují aplikace VaaS Azure AD potřebná oprávnění jménem svého tenanta:

1. Přihlásit pomocí přihlašovacích údajů globálního správce pro tenanta [VaaS portál](https://azurestackvalidation.com/). 

2. Vyberte **Můj účet**.

3 souhlas s podmínkami, aby bylo možné pokračovat po zobrazení výzvy k udělení oprávnění VaaS uvedené služby Azure AD.

## <a name="create-an-azure-storage-account"></a>Vytvoření účtu služby Azure Storage

Během provádění testů výstupy VaaS diagnostických protokolů pro účet služby Azure Storage. Kromě protokolů testu účtu úložiště také slouží k nahrávání výrobce OEM balíčky rozšíření pro pracovní postup ověření balíčku.

Účet služby Azure Storage hostována ve veřejném cloudu Azure, ne na prostředí Azure Stack.

1. Na webu Azure Portal, vyberte **všechny služby** > **úložiště** > **účty úložiště**. Na **účty úložiště** okně vyberte **přidat**.

2. Vyberte předplatné, ve kterém chcete vytvořit účet úložiště.

3. V části **skupiny prostředků**vyberte **vytvořit nový**. Zadejte název nové skupiny prostředků.

4. Zadejte název účtu úložiště. Název zvoleném musí být:
    - Jedinečný v rámci Azure
    - Dlouhý 3 až 24 znaků
    - Obsahovat pouze číslice a malá písmena

5. Vyberte **USA – západ** oblast pro účet úložiště.

    Aby se zajistilo, že sítě nejsou poplatky pro ukládání protokolů, účet Azure Storage lze nakonfigurovat na používání jenom **USA – západ** oblasti. Replikace dat a funkce úrovně horkého úložiště nejsou nutné pro tato data. Povolení buď funkce značně zvýší náklady.

6. Ponechejte nastavení na výchozí hodnoty s výjimkou **druh účtu**:

    - **Model nasazení** je nastaveno na **Resource Manageru** ve výchozím nastavení.
    - Pole **Výkon** má výchozí nastavení **Standardní**.
    - Vyberte **druh účtu** pole jako **úložiště objektů Blob**.
    - **Replikace pole** je nastavena na **místně redundantní úložiště (LRS)** ve výchozím nastavení.
    - Pole **Úroveň přístupu** má výchozí nastavení **Horká**.

7. Kliknutím na **Zkontrolovat a vytvořit** si prohlédněte nastavení účtu úložiště a vytvořte účet.

## <a name="next-steps"></a>Další postup

Pokud vaše prostředí neumožňuje připojení v mez, postupujte podle kurzu o nasazení místního agenta spustit test na hardwaru.

> [!div class="nextstepaction"]
> [Nasazení místního agenta](azure-stack-vaas-local-agent.md)