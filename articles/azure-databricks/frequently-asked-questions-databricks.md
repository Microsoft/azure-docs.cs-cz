---
title: 'Azure Databricks: Běžné otázky a pomoc'
description: Získejte odpovědi na běžné otázky a informace Poradce při potížích Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 3bcc511ec6ad8a246c2b1b3a33eb59043a45830e
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50138357"
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Nejčastější dotazy k Azure Databricks

Tento článek uvádí hlavní otázky, které může mít související s Azure Databricks. Také uvádí některé běžné problémy, může být při používání služby Databricks. Další informace najdete v tématu [co je Azure Databricks](what-is-azure-databricks.md). 

## <a name="can-i-use-azure-key-vault-to-store-keyssecrets-to-be-used-in-azure-databricks"></a>Můžete použít Azure Key Vault pro ukládání klíčů/tajných klíčů se používá v Azure Databricks?
Ano. Azure Key Vault můžete použít k ukládání klíčů/tajných klíčů pro použití s Azure Databricks. Další informace najdete v tématu [zajišťuje Azure Key Vault obory](https://docs.azuredatabricks.net/user-guide/secrets/secret-scopes.html#akv-ss).


## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>Můžete použít Azure Virtual Network s Databricks?
Ano. S Azure Databricks můžete použít Azure Virtual Network (VNET). Další informace najdete v tématu [nasazení Azure Databricks ve službě Azure Virtual Network](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

## <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>Jak získám přístup k Azure Data Lake Store z poznámkového bloku? 

Postupujte následovně:
1. Ve službě Azure Active Directory (Azure AD) zřízení hlavního názvu služby a svůj klíč si poznamenejte.
1. Přidělení potřebných oprávnění instančnímu objektu služby v Data Lake Store.
1. Pro přístup k souboru v Data Lake Store, použijte přihlašovací údaje instančního objektu služby v poznámkovém bloku.

Další informace najdete v tématu [použití Data Lake Store pomocí Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html).

## <a name="fix-common-problems"></a>Řešení běžných potíží

Tady je několik problémů, ke kterým může docházet v Databricks.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Problém: Předplatné není zaregistrované používání oboru názvů "Microsoft.Databricks.

#### <a name="error-message"></a>Chybová zpráva

"Toto předplatné není zaregistrované používání oboru názvů"Microsoft.Databricks". Zobrazit https://aka.ms/rps-not-found o tom, jak zaregistrovat předplatná. (Code: MissingSubscriptionRegistration) "

#### <a name="solution"></a>Řešení

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Vyberte **předplatná**, předplatné používáte, a potom **poskytovatelů prostředků**. 
1. V seznamu poskytovatelů prostředků proti **Microsoft.Databricks**vyberte **zaregistrovat**. V předplatném zaregistrovat poskytovatele prostředků musíte mít roli Přispěvatel nebo vlastník.


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Problém: Váš účet {email} nemá roli vlastníka nebo přispěvatele v prostředku pracovního prostoru Databricks na portálu Azure portal

#### <a name="error-message"></a>Chybová zpráva

"Účtu {email} nemá role přispěvatele nebo vlastníka prostředku pracovního prostoru Databricks na portálu Azure portal. K této chybě může dojít, pokud jste uživatelem typu Host v tenantovi. Požádejte správce o udělení přístupu nebo vás přidal jako uživatel přímo do pracovního prostoru Databricks." 

#### <a name="solution"></a>Řešení

Tady jsou do ní několik řešení tohoto problému:

* Inicializace klienta, musíte být přihlášeni jako běžný uživatel tenanta, nikoli jako uživatele typu Host. Také musíte mít roli přispěvatele v prostředku pracovního prostoru Databricks. Můžete udělit přístup uživatelů z **řízení přístupu (IAM)** kartu v rámci pracovního prostoru Databricks na portálu Azure portal.

* Tato chyba může také dojít, pokud název domény e-mailu je přiřazena k více adresářů ve službě Azure AD. Chcete-li tento problém obejít, vytvořte nového uživatele v adresáři, který obsahuje předplatné s pracovního prostoru Databricks.

    a. Na webu Azure Portal přejděte do služby Azure AD. Vyberte **uživatelů a skupin** > **přidání uživatele**.

    b. Přidání uživatele pomocí `@<tenant_name>.onmicrosoft.com` e-mailu místo `@<your_domain>` e-mailu. Tuto možnost v můžete najít **vlastní domény**, v rámci Azure AD na webu Azure Portal.
    
    c. Udělení tohoto nového uživatele **Přispěvatel** role v prostředku pracovního prostoru Databricks.
    
    d. Přihlaste se k webu Azure portal k novému uživateli a najít pracovního prostoru Databricks.
    
    e. Spusťte pracovní prostor Databricks jako tohoto uživatele.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Problém: {Email} váš účet není zaregistrovaný ve službě Databricks 

#### <a name="solution"></a>Řešení

Pokud jste nevytvořili pracovní prostor a jsou přidány jako uživatel, obraťte se na osobu, která vytvořila pracovní prostor. Máte tato osoba je přidat pomocí konzoly pro správu Azure Databricks. Pokyny najdete v tématu [přidání a Správa uživatelů](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html). Pokud jste vytvořili pracovní prostor a stále se zobrazí tato chyba, zkuste vybrat **inicializovat pracovní prostor** znovu z portálu Azure portal.

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>Problém: Selhání spuštění poskytovatele cloudu při nastavování clusteru (PublicIPCountLimitReached)

#### <a name="error-message"></a>Chybová zpráva

"Chyba spuštění poskytovatele cloudové: Při nastavování clusteru došlo k chybě zprostředkovatele cloudu. Další informace najdete v příručce k Databricks. Kód chyby Azure: PublicIPCountLimitReached. Chybová zpráva Azure: Nelze vytvořit více než 60 veřejné IP adresy pro toto předplatné v této oblasti. "

#### <a name="solution"></a>Řešení

Clustery Databricks používají jednu veřejnou IP adresu na jeden uzel. Pokud vaše předplatné se již používá všechny její veřejné IP adresy, měli byste [žádost o zvýšení kvóty](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request). Zvolte **kvóty** jako **typ problému**, a **síť: ARM** jako **typ kvóty**. V **podrobnosti**, požádat o zvýšení kvóty veřejnou IP adresu. Například pokud svůj limit je aktuálně 60 a chcete vytvořit cluster s uzly 100, požádat o zvýšení limitu pro 160.

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>Problém: Druhého typu selhání spuštění poskytovatele cloudu při nastavování clusteru (MissingSubscriptionRegistration)

#### <a name="error-message"></a>Chybová zpráva

"Chyba spuštění poskytovatele cloudové: Při nastavování clusteru došlo k chybě zprostředkovatele cloudu. Další informace najdete v příručce k Databricks.
Kód chyby Azure: MissingSubscriptionRegistration Azure chybová zpráva: předplatné není zaregistrované používání oboru názvů "Microsoft.Compute". Zobrazit https://aka.ms/rps-not-found o tom, jak zaregistrovat předplatná. "

#### <a name="solution"></a>Řešení

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Vyberte **předplatná**, předplatné používáte, a potom **poskytovatelů prostředků**. 
1. V seznamu poskytovatelů prostředků proti **Microsoft.Compute**vyberte **zaregistrovat**. V předplatném zaregistrovat poskytovatele prostředků musíte mít roli Přispěvatel nebo vlastník.

Podrobnější pokyny najdete v článku [poskytovatelé a typy prostředků](../azure-resource-manager/resource-manager-supported-services.md).

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>Problém: Azure Databricks potřebuje oprávnění pro přístup k prostředkům ve vaší organizaci, který může udělit jenom správce.

#### <a name="background"></a>Pozadí

Azure Databricks je integrovaná s Azure Active Directory. Oprávnění v rámci Azure Databricks (třeba na poznámkových bloků nebo clustery) můžete nastavit tak, že zadáte uživatele ze služby Azure AD. Pro Azure Databricks, aby mohli zobrazit jména uživatelů ze služby Azure AD vyžaduje oprávnění ke čtení pro tyto informace a souhlas s předávat. Pokud svůj souhlas již není k dispozici, se zobrazí chyba.

#### <a name="solution"></a>Řešení

Přihlaste se jako globální správce na webu Azure portal. Pro Azure Active Directory, přejděte **uživatelská nastavení** kartu a ujistěte se, že **uživatelé můžou udělit souhlas s aplikací, které přistupují k firemním datům jejich jménem** je nastavena na **Ano**.

## <a name="next-steps"></a>Další postup

- [Rychlý start: Začínáme s Azure Databricks](quickstart-create-databricks-workspace-portal.md)
- [Co je Azure Databricks?](what-is-azure-databricks.md)

